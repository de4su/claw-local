# Telegram Bot

Hook your local AI agent to Telegram so you can give it tasks from your phone.

## Get a Bot Token

1. Open Telegram, find `@BotFather`
2. Send `/newbot`
3. Give it a name and username
4. BotFather gives you a token like `123456789:ABCdefGHIjklmnoPQRstuvWXYZabcdefg`

Save it.

## Python Version

```bash
pip install python-telegram-bot requests
```

Create `telegram_bot.py`:

```python
import logging
from telegram import Update
from telegram.ext import Application, CommandHandler, MessageHandler, filters, ContextTypes
import requests

OPENCLAW_URL = "http://localhost:18789"
OPENCLAW_TOKEN = "your-token-from-openclaw-config"
TELEGRAM_TOKEN = "your-telegram-token"

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE) -> None:
    await update.message.reply_text("Local AI agent ready. Give me tasks.")

async def handle_message(update: Update, context: ContextTypes.DEFAULT_TYPE) -> None:
    user_message = update.message.text
    await update.message.chat.send_action("typing")
    
    try:
        response = requests.post(
            f"{OPENCLAW_URL}/api/chat",
            json={
                "messages": [{"role": "user", "content": user_message}],
                "model": "lmstudio/phi-4-mini-instruct"
            },
            headers={
                "Authorization": f"Bearer {OPENCLAW_TOKEN}",
                "Content-Type": "application/json"
            }
        )
        
        if response.status_code == 200:
            result = response.json()
            ai_response = result.get("choices", [{}])[0].get("message", {}).get("content", "")
            await update.message.reply_text(ai_response)
        else:
            await update.message.reply_text("Error")
    except Exception as e:
        logger.error(f"Error: {e}")
        await update.message.reply_text("Something broke")

def main() -> None:
    application = Application.builder().token(TELEGRAM_TOKEN).build()
    application.add_handler(CommandHandler("start", start))
    application.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_message))
    print("Bot running")
    application.run_polling()

if __name__ == '__main__':
    main()
```

Run it:
```bash
python telegram_bot.py
```

## Node.js Version

```bash
npm install telegraf axios
```

Create `telegram_bot.js`:

```javascript
const { Telegraf } = require('telegraf');
const axios = require('axios');

const OPENCLAW_URL = "http://localhost:18789";
const OPENCLAW_TOKEN = "your-token-from-openclaw-config";
const TELEGRAM_TOKEN = "your-telegram-token";

const bot = new Telegraf(TELEGRAM_TOKEN);

bot.start((ctx) => {
    ctx.reply("Local AI agent ready.");
});

bot.on('text', async (ctx) => {
    const userMessage = ctx.message.text;
    
    try {
        await ctx.sendChatAction('typing');
        
        const response = await axios.post(
            `${OPENCLAW_URL}/api/chat`,
            {
                messages: [{ role: 'user', content: userMessage }],
                model: 'lmstudio/phi-4-mini-instruct'
            },
            {
                headers: {
                    'Authorization': `Bearer ${OPENCLAW_TOKEN}`,
                    'Content-Type': 'application/json'
                }
            }
        );
        
        const aiResponse = response.data.choices[0].message.content;
        ctx.reply(aiResponse);
    } catch (error) {
        console.error(error);
        ctx.reply("Something broke");
    }
});

bot.launch();
console.log("Bot running");
```

Run it:
```bash
node telegram_bot.js
```

**Connection refused**
- Start OpenClaw: `npx openclaw gateway --port 18789`

**Slow to respond**
- AI is processing. Normal. Could be 10-30 seconds depending on hardware.
