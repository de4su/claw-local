# Discord Bot

Add your local AI agent to Discord. Give it tasks from your server.

## Method 1: Built-in OpenClaw Channel (Recommended)

OpenClaw now has native Discord support — no custom bot code needed.

### Setup

1. Go to https://discord.com/developers/applications
2. New Application → Bot tab → Add Bot
3. Copy the bot token
4. Enable Message Content Intent under Privileged Gateway Intents
5. OAuth2 → URL Generator → check `bot` scope → check `Send Messages` and `Read Messages/View Channels`
6. Copy the invite URL, open in browser, pick your server

Then in your terminal:
```bash
openclaw channels add --channel discord
```

Paste your bot token when prompted. That's it.

### Manage

```bash
openclaw channels list                    # View active channels
openclaw channels status --channel discord # Check connectivity
openclaw channels remove --channel discord # Remove integration
```

### Config (manual)

In `~/.openclaw/openclaw.json`:
```json
{
  "channels": {
    "discord": {
      "enabled": true,
      "token": "YOUR_DISCORD_BOT_TOKEN",
      "dmPolicy": "allowlist",
      "allowFrom": ["your-discord-user-id"]
    }
  }
}
```

**Always set `dmPolicy` to `allowlist`** and restrict `allowFrom` to your own user ID to prevent strangers from using your agent.

---

## Method 2: Custom Bot Script

If you want more control, you can write your own bot that talks to OpenClaw's API.

### Python Version

```bash
pip install discord.py requests
```

Create `discord_bot.py`:

```python
import discord
from discord.ext import commands
import requests

OPENCLAW_URL = "http://localhost:18789"
OPENCLAW_TOKEN = "your-token-from-openclaw-config"
DISCORD_TOKEN = "your-discord-token"

intents = discord.Intents.default()
intents.message_content = True
bot = commands.Bot(command_prefix="!", intents=intents)

@bot.event
async def on_ready():
    print(f"✓ Bot logged in as {bot.user}")

@bot.event
async def on_message(message):
    if message.author == bot.user or message.author.bot:
        return
    
    if not (bot.user.mentioned_in(message) or isinstance(message.channel, discord.DMChannel)):
        return
    
    user_message = message.content.replace(f"<@{bot.user.id}>", "").strip()
    
    if not user_message:
        return
    
    try:
        async with message.channel.typing():
            response = requests.post(
                f"{OPENCLAW_URL}/api/chat",
                json={
                    "messages": [{"role": "user", "content": user_message}],
                    "model": "lmstudio/qwen-3.6-27b"
                },
                headers={
                    "Authorization": f"Bearer {OPENCLAW_TOKEN}",
                    "Content-Type": "application/json"
                }
            )
            
            if response.status_code == 200:
                result = response.json()
                ai_response = result.get("choices", [{}])[0].get("message", {}).get("content", "")
                
                if len(ai_response) > 2000:
                    chunks = [ai_response[i:i+2000] for i in range(0, len(ai_response), 2000)]
                    for chunk in chunks:
                        await message.reply(chunk)
                else:
                    await message.reply(ai_response)
            else:
                await message.reply(f"Error: {response.status_code}")
    except Exception as e:
        print(f"Error: {e}")
        await message.reply("Something broke")
    
    await bot.process_commands(message)

bot.run(DISCORD_TOKEN)
```

Run it:
```bash
python discord_bot.py
```

### Node.js Version

```bash
npm install discord.js axios
```

Create `discord_bot.js`:

```javascript
const { Client, GatewayIntentBits } = require('discord.js');
const axios = require('axios');

const OPENCLAW_URL = "http://localhost:18789";
const OPENCLAW_TOKEN = "your-token-from-openclaw-config";
const DISCORD_TOKEN = "your-discord-token";

const client = new Client({ 
    intents: [
        GatewayIntentBits.Guilds, 
        GatewayIntentBits.GuildMessages, 
        GatewayIntentBits.MessageContent, 
        GatewayIntentBits.DirectMessages
    ] 
});

client.on('ready', () => {
    console.log(`✓ Bot logged in as ${client.user.tag}`);
});

client.on('messageCreate', async (message) => {
    if (message.author.bot) return;
    if (!message.mentions.has(client.user.id) && !message.channel.isDMBased()) return;
    
    const userMessage = message.content.replace(`<@${client.user.id}>`, '').trim();
    if (!userMessage) return;
    
    try {
        await message.channel.sendTyping();
        
        const response = await axios.post(
            `${OPENCLAW_URL}/api/chat`,
            {
                messages: [{ role: 'user', content: userMessage }],
                model: 'lmstudio/qwen-3.6-27b'
            },
            {
                headers: {
                    'Authorization': `Bearer ${OPENCLAW_TOKEN}`,
                    'Content-Type': 'application/json'
                }
            }
        );
        
        const aiResponse = response.data.choices[0].message.content;
        
        if (aiResponse.length > 2000) {
            const chunks = aiResponse.match(/[\s\S]{1,2000}/g);
            for (const chunk of chunks) {
                await message.reply(chunk);
            }
        } else {
            await message.reply(aiResponse);
        }
    } catch (error) {
        console.error(error);
        message.reply("Something broke");
    }
});

client.login(DISCORD_TOKEN);
```

Run it:
```bash
node discord_bot.js
```
