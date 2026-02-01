# WhatsApp Bot

Get your local AI agent on WhatsApp. Use Twilio for messaging.

## Setup Twilio

1. Go to https://www.twilio.com/console
2. Sign up (free, needs credit card)
3. Messaging → WhatsApp sandbox settings
4. You get a number like `+1 415-XXX-XXXX`

Message that number from WhatsApp with their sandbox code.

Get Account SID and Auth Token from Account settings. Save them.

## Python Version

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
                "model": "lmstudio/phi-4-mini-instruct"
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

## Node.js Version

```bash
npm install twilio express axios
```

Create `whatsapp_bot.js`:

```javascript
const express = require('express');
const twilio = require('twilio');
const axios = require('axios');

const OPENCLAW_URL = "http://localhost:18789";
const OPENCLAW_TOKEN = "your-token-from-openclaw-config";

const TWILIO_ACCOUNT_SID = "your-account-sid";
const TWILIO_AUTH_TOKEN = "your-auth-token";
const TWILIO_WHATSAPP_NUMBER = "whatsapp:+1415XXXXXXX";

const client = twilio(TWILIO_ACCOUNT_SID, TWILIO_AUTH_TOKEN);
const app = express();

app.use(express.urlencoded({ extended: false }));

app.post('/whatsapp', async (req, res) => {
    const incomingMsg = req.body.Body || '';
    const sender = req.body.From;
    
    console.log(`Message from ${sender}: ${incomingMsg}`);
    
    try {
        const response = await axios.post(
            `${OPENCLAW_URL}/api/chat`,
            {
                messages: [{ role: 'user', content: incomingMsg }],
                model: 'lmstudio/phi-4-mini-instruct'
            },
            {
                headers: {
                    'Authorization': `Bearer ${OPENCLAW_TOKEN}`,
                    'Content-Type': 'application/json'
                },
                timeout: 30000
            }
        );
        
        const aiResponse = response.data.choices[0].message.content;
        
        const message = await client.messages.create({
            body: aiResponse,
            from: TWILIO_WHATSAPP_NUMBER,
            to: sender
        });
        
        console.log(`Sent: ${message.sid}`);
    } catch (error) {
        console.error(error);
        client.messages.create({
            body: 'Something broke',
            from: TWILIO_WHATSAPP_NUMBER,
            to: sender
        });
    }
    
    res.status(200).send('OK');
});

app.listen(5000, () => {
    console.log("Bot running on port 5000");
});
```

## Make It Accessible

Your bot runs on port 5000 but WhatsApp needs to reach it from the internet. Use ngrok:

```bash
# Get ngrok from https://ngrok.com/
ngrok http 5000
```

You get a URL like `https://abc123.ngrok.io`

## Connect Twilio

In Twilio console, WhatsApp sandbox settings:
- Set webhook to: `https://abc123.ngrok.io/whatsapp`

Now messages go to your bot.

## Test

Send a WhatsApp message to the sandbox number. Your bot should respond.

## Issues

**Webhook not working**
- ngrok running?
- URL correct in Twilio?

**Bot doesn't respond**
- OpenClaw running?
- Check console output

**Takes a while**
- WhatsApp + local AI = slow. Normal.

**Go live?**
- Twilio needs to approve you
- Check their docs
- Same code, different setup