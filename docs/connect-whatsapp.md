# WhatsApp Bot

Get your local AI agent on WhatsApp.

## Method 1: Built-in OpenClaw Channel (Recommended)

OpenClaw now has native WhatsApp support using QR code login — no Twilio needed.

### Setup

```bash
openclaw channels login --channel whatsapp
```

Scan the QR code that appears in your terminal using WhatsApp on your phone.

That's it. Your agent is now on WhatsApp.

**Tip:** Use a dedicated phone number for the agent to avoid risking your personal WhatsApp account.

### Manage

```bash
openclaw channels list                      # View active channels
openclaw channels status --channel whatsapp  # Check connectivity
openclaw channels remove --channel whatsapp  # Remove integration
```

### Security config

In `~/.openclaw/openclaw.json`:
```json
{
  "channels": {
    "whatsapp": {
      "enabled": true,
      "dmPolicy": "allowlist",
      "allowFrom": ["+1234567890"]
    }
  }
}
```

**Always restrict `allowFrom`** to your own phone number. Without this, anyone who messages the WhatsApp number can control your agent.

---

## Method 2: Custom Bot with Twilio

If you want a traditional webhook-based setup using Twilio.

### Setup Twilio

1. Go to https://www.twilio.com/console
2. Sign up (free, needs credit card)
3. Messaging → WhatsApp sandbox settings
4. You get a number like `+1 415-XXX-XXXX`

Message that number from WhatsApp with their sandbox code.

Get Account SID and Auth Token from Account settings. Save them.

### Python Version

```bash
pip install twilio requests flask
```

Create `whatsapp_bot.py`:

```python
from flask import Flask, request
from twilio.rest import Client
import requests

OPENCLAW_URL = "http://localhost:18789"
OPENCLAW_TOKEN = "your-token-from-openclaw-config"

TWILIO_ACCOUNT_SID = "your-account-sid"
TWILIO_AUTH_TOKEN = "your-auth-token"
TWILIO_WHATSAPP_NUMBER = "whatsapp:+1415XXXXXXX"

client = Client(TWILIO_ACCOUNT_SID, TWILIO_AUTH_TOKEN)
app = Flask(__name__)

@app.route("/whatsapp", methods=['POST'])
def whatsapp():
    incoming_msg = request.values.get('Body', '').strip()
    sender = request.values.get('From')
    
    print(f"Message from {sender}: {incoming_msg}")
    
    try:
        response = requests.post(
            f"{OPENCLAW_URL}/api/chat",
            json={
                "messages": [{"role": "user", "content": incoming_msg}],
                "model": "lmstudio/qwen-3.6-27b"
            },
            headers={
                "Authorization": f"Bearer {OPENCLAW_TOKEN}",
                "Content-Type": "application/json"
            },
            timeout=30
        )
        
        if response.status_code == 200:
            result = response.json()
            ai_response = result.get("choices", [{}])[0].get("message", {}).get("content", "")
            
            message = client.messages.create(
                body=ai_response,
                from_=TWILIO_WHATSAPP_NUMBER,
                to=sender
            )
            print(f"Sent: {message.sid}")
        else:
            client.messages.create(
                body="Error",
                from_=TWILIO_WHATSAPP_NUMBER,
                to=sender
            )
    except Exception as e:
        print(f"Error: {e}")
        client.messages.create(
            body="Something broke",
            from_=TWILIO_WHATSAPP_NUMBER,
            to=sender
        )
    
    return "OK", 200

if __name__ == '__main__':
    print("Bot running on port 5000")
    app.run(debug=False, port=5000)
```

### Make It Accessible

Your bot runs on port 5000 but WhatsApp needs to reach it from the internet. Use ngrok:

```bash
ngrok http 5000
```

You get a URL like `https://abc123.ngrok.io`

### Connect Twilio

In Twilio console, WhatsApp sandbox settings:
- Set webhook to: `https://abc123.ngrok.io/whatsapp`

Now messages go to your bot.

### Test

Send a WhatsApp message to the sandbox number. Your bot should respond.
