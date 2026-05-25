# 🪟 Hermes Sales Agent — Windows Setup Guide

> **Simple English · Step by Step · No Experience Needed**
> Stack: Node.js · Express · OpenAI / Claude API · WhatsApp · Telegram · Hostinger KVM 2

---

## 📋 Table of Contents

1. [Install Required Tools](#step-1--install-required-tools)
2. [Open Terminal](#step-2--open-terminal-command-prompt)
3. [Create Project Folder](#step-3--create-your-project-folder)
4. [Install Project Packages](#step-4--install-project-packages)
5. [Create .env File](#step-5--create-the-env-file-your-secret-keys)
6. [Create Code Files](#step-6--create-your-code-files)
7. [Test Locally](#step-7--test-it-locally)
8. [Test Webhook with ngrok](#step-8--test-webhook-with-ngrok-whatsapp--telegram)
9. [Upload to Hostinger](#step-9--upload-to-hostinger-server)
10. [Connect to Server via SSH](#step-10--connect-to-server-via-ssh-windows)
11. [Final Checklist](#-final-checklist)
12. [Common Errors & Fixes](#-common-errors--fixes)

---

## STEP 1 — Install Required Tools

Download and install these **one by one**. Do not skip any.

---

### 1️⃣ Node.js

- Go to: **https://nodejs.org**
- Download the **LTS version** (big green button)
- Run the installer
- Keep clicking **Next** → at the end click **Install**
- Wait for it to finish → click **Finish**

---

### 2️⃣ Git

- Go to: **https://git-scm.com**
- Click **Download for Windows**
- Run the installer
- Keep all default settings → keep clicking **Next** → click **Install**

---

### 3️⃣ VS Code (Code Editor)

- Go to: **https://code.visualstudio.com**
- Click the big **Download for Windows** button
- Run the installer → click **Next** through everything → **Install**

---

### 4️⃣ WinSCP (File Transfer to Server)

- Go to: **https://winscp.net**
- Click **Download WinSCP**
- Install it (you will use it later in Step 9)

---

### 5️⃣ ngrok (For Webhook Testing)

- Go to: **https://ngrok.com/download**
- Click **Download for Windows**
- You will get a `.zip` file
- Extract it — you will get a file called `ngrok.exe`
- Move `ngrok.exe` to your Desktop for now

---

## STEP 2 — Open Terminal (Command Prompt)

1. Press **Windows key + R** on your keyboard
2. Type `cmd`
3. Press **Enter**
4. A black window will open — this is your **terminal**

**Now check everything is installed. Type these one by one and press Enter:**

```
node --version
```
You should see something like: `v18.20.0`

```
npm --version
```
You should see something like: `9.8.1`

```
git --version
```
You should see something like: `git version 2.44.0`

> ✅ If all three show version numbers, you are ready.
> ❌ If any shows an error, reinstall that tool and restart your PC, then try again.

---

## STEP 3 — Create Your Project Folder

In the terminal, type these **one by one** and press Enter after each:

```
cd Desktop
```
```
mkdir hermes-agent
```
```
cd hermes-agent
```

> You are now inside a folder called `hermes-agent` on your Desktop.

---

## STEP 4 — Install Project Packages

Still in the terminal, run these **one by one**:

```
npm init -y
```

```
npm install express dotenv openai @anthropic-ai/sdk node-telegram-bot-api
```

> This will download all required packages. It may take 1-2 minutes. Wait for it to finish.

When done, you will see something like:
```
added 120 packages in 45s
```

---

## STEP 5 — Create the `.env` File (Your Secret Keys)

This file holds all your API keys. **Keep it secret — never share it.**

**Open VS Code:**
1. Open VS Code
2. Click **File** → **Open Folder**
3. Go to Desktop → select `hermes-agent` → click **Select Folder**

**Create the file:**
1. In VS Code, click the **New File** icon (or press `Ctrl + N`)
2. Name it exactly: `.env` (dot before env, no other extension)
3. Paste this inside:

```env
# ──────────────────────────────────────────
# AI PROVIDER — choose one only
# ──────────────────────────────────────────

# Option A: OpenAI
OPENAI_API_KEY=sk-proj-your-key-here
AI_PROVIDER=openai
AI_MODEL=gpt-4o

# Option B: Claude (comment out OpenAI above if using this)
# ANTHROPIC_API_KEY=sk-ant-your-key-here
# AI_PROVIDER=anthropic
# AI_MODEL=claude-sonnet-4-20250514

# ──────────────────────────────────────────
# WHATSAPP
# ──────────────────────────────────────────
WHATSAPP_TOKEN=your-whatsapp-token-here
WHATSAPP_PHONE_NUMBER_ID=your-phone-number-id-here
WHATSAPP_VERIFY_TOKEN=any_secret_word_you_choose

# ──────────────────────────────────────────
# TELEGRAM
# ──────────────────────────────────────────
TELEGRAM_BOT_TOKEN=your-telegram-bot-token-here

# ──────────────────────────────────────────
# SERVER
# ──────────────────────────────────────────
PORT=3000
NODE_ENV=development
WEBHOOK_BASE_URL=https://your-domain.com
```

4. Press **Ctrl + S** to save

---

### How to Get Your API Keys

**OpenAI API Key:**
```
1. Go to: https://platform.openai.com/api-keys
2. Click "Create new secret key"
3. Copy the key → paste it in .env
4. Set up billing: https://platform.openai.com/billing
```

**Claude API Key:**
```
1. Go to: https://console.anthropic.com
2. Click "API Keys" on the left
3. Click "Create Key"
4. Copy the key → paste it in .env
```

**WhatsApp Token:**
```
1. Go to: https://developers.facebook.com
2. Create App → Business type
3. Add WhatsApp product
4. Copy Access Token and Phone Number ID → paste in .env
```

**Telegram Bot Token:**
```
1. Open Telegram app
2. Search for: @BotFather
3. Send: /newbot
4. Follow instructions → at the end you get a token
5. Copy it → paste in .env
```

---

## STEP 6 — Create Your Code Files

In VS Code, create these files inside the `hermes-agent` folder.
Click the **New File** icon for each one.

---

### File 1: `sales-prompt.js`

This is where you write your sales instructions for the AI.

```javascript
const SALES_SYSTEM_PROMPT = `
You are "Hermes" — a friendly and professional AI Sales Assistant.

Company: [Write your company name here]
Products: [Write your products or services here]
Language: Reply in the same language the customer uses (Urdu or English)
Tone: Friendly, helpful, and professional

Your job:
- Understand what the customer needs
- Suggest the right product naturally
- Handle questions and objections politely
- Always end with a clear next step (call, order, visit)

Rules:
- Never lie about products or prices
- Keep replies short (2-4 sentences max for WhatsApp)
- If question is too complex, say: "Let me connect you with our sales team"

Products and Prices:
[Write your products and prices here]
`;

module.exports = SALES_SYSTEM_PROMPT;
```

---

### File 2: `whatsapp.js`

```javascript
const express = require('express');
const router = express.Router();

// Verify webhook (WhatsApp calls this once to confirm)
router.get('/webhook/whatsapp', (req, res) => {
  const mode = req.query['hub.mode'];
  const token = req.query['hub.verify_token'];
  const challenge = req.query['hub.challenge'];

  if (mode === 'subscribe' && token === process.env.WHATSAPP_VERIFY_TOKEN) {
    console.log('✅ WhatsApp Webhook verified!');
    res.status(200).send(challenge);
  } else {
    res.sendStatus(403);
  }
});

// Receive messages from WhatsApp
router.post('/webhook/whatsapp', async (req, res) => {
  const body = req.body;

  if (body.object === 'whatsapp_business_account') {
    const message = body.entry?.[0]?.changes?.[0]?.value?.messages?.[0];

    if (message?.type === 'text') {
      const from = message.from;
      const text = message.text.body;

      console.log(`📱 WhatsApp message from ${from}: "${text}"`);

      const reply = await global.generateSalesReply(text, from);
      await sendWhatsAppMessage(from, reply);
    }

    res.sendStatus(200);
  } else {
    res.sendStatus(404);
  }
});

// Send a WhatsApp message
async function sendWhatsAppMessage(to, message) {
  const response = await fetch(
    `https://graph.facebook.com/v18.0/${process.env.WHATSAPP_PHONE_NUMBER_ID}/messages`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${process.env.WHATSAPP_TOKEN}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        messaging_product: 'whatsapp',
        to: to,
        type: 'text',
        text: { body: message }
      })
    }
  );
  return response.json();
}

module.exports = router;
```

---

### File 3: `telegram.js`

```javascript
const TelegramBot = require('node-telegram-bot-api');

const bot = new TelegramBot(process.env.TELEGRAM_BOT_TOKEN, {
  polling: process.env.NODE_ENV !== 'production'
});

// When user sends /start
bot.onText(/\/start/, (msg) => {
  const chatId = msg.chat.id;
  const name = msg.from.first_name;

  bot.sendMessage(chatId,
    `Hello ${name}! 👋\n\n` +
    `I am Hermes, your AI Sales Assistant.\n` +
    `Ask me anything about our products! 🚀`
  );
});

// Handle all other messages
bot.on('message', async (msg) => {
  if (msg.text?.startsWith('/')) return;

  const chatId = msg.chat.id;
  const userId = msg.from.id.toString();
  const text = msg.text;

  console.log(`📨 Telegram from ${msg.from.first_name}: "${text}"`);

  bot.sendChatAction(chatId, 'typing');

  const reply = await global.generateSalesReply(text, userId);
  bot.sendMessage(chatId, reply);
});

async function setupTelegramWebhook(baseUrl) {
  await bot.setWebHook(`${baseUrl}/webhook/telegram`);
  console.log('✅ Telegram Webhook set!');
}

module.exports = { bot, setupTelegramWebhook };
```

---

### File 4: `agent.js` (Main File)

```javascript
require('dotenv').config();
const express = require('express');
const SALES_PROMPT = require('./sales-prompt');

const app = express();
app.use(express.json());

// Store conversation history for each user
const conversations = new Map();

// Generate AI reply
async function generateSalesReply(userMessage, userId) {
  if (!conversations.has(userId)) {
    conversations.set(userId, []);
  }

  const history = conversations.get(userId);
  history.push({ role: 'user', content: userMessage });

  let reply;

  try {
    if (process.env.AI_PROVIDER === 'openai') {
      const { OpenAI } = require('openai');
      const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

      const response = await openai.chat.completions.create({
        model: process.env.AI_MODEL || 'gpt-4o',
        messages: [
          { role: 'system', content: SALES_PROMPT },
          ...history.slice(-10)
        ],
        max_tokens: 500,
        temperature: 0.7
      });

      reply = response.choices[0].message.content;

    } else {
      const Anthropic = require('@anthropic-ai/sdk');
      const anthropic = new Anthropic({ apiKey: process.env.ANTHROPIC_API_KEY });

      const response = await anthropic.messages.create({
        model: process.env.AI_MODEL || 'claude-sonnet-4-20250514',
        max_tokens: 500,
        system: SALES_PROMPT,
        messages: history.slice(-10)
      });

      reply = response.content[0].text;
    }

  } catch (error) {
    console.error('AI Error:', error.message);
    reply = "Sorry, I am having trouble right now. Please try again in a moment.";
  }

  history.push({ role: 'assistant', content: reply });

  // Keep only last 20 messages per user
  if (history.length > 20) {
    conversations.set(userId, history.slice(-20));
  }

  return reply;
}

// Make function available globally
global.generateSalesReply = generateSalesReply;

// Load routes
app.use(require('./whatsapp'));
const { bot, setupTelegramWebhook } = require('./telegram');

// Health check endpoint
app.get('/health', (req, res) => {
  res.json({
    status: 'OK',
    agent: 'Hermes Sales Agent',
    time: new Date().toISOString()
  });
});

// Start the server
const PORT = process.env.PORT || 3000;
app.listen(PORT, async () => {
  console.log(`🚀 Hermes Sales Agent running on port ${PORT}`);
  console.log(`🔗 Health check: http://localhost:${PORT}/health`);

  if (process.env.NODE_ENV === 'production' && process.env.WEBHOOK_BASE_URL) {
    await setupTelegramWebhook(process.env.WEBHOOK_BASE_URL);
  }
});
```

---

## STEP 7 — Test It Locally

Go back to your terminal and make sure you are in the `hermes-agent` folder:

```
cd Desktop\hermes-agent
```

Start the agent:

```
node agent.js
```

You should see:
```
🚀 Hermes Sales Agent running on port 3000
🔗 Health check: http://localhost:3000/health
```

Now open your browser and go to:
```
http://localhost:3000/health
```

You should see:
```json
{ "status": "OK", "agent": "Hermes Sales Agent" }
```

> ✅ If you see this — your agent is working locally!
> Press `Ctrl + C` in terminal to stop it when done testing.

---

## STEP 8 — Test Webhook with ngrok (WhatsApp & Telegram)

WhatsApp and Telegram need a **public URL** to send messages to your bot.
ngrok gives you a temporary public URL for testing.

**Step 1:** Move `ngrok.exe` from Desktop into your `hermes-agent` folder.

**Step 2:** Start your agent in one terminal:
```
node agent.js
```

**Step 3:** Open a **second terminal** (new CMD window) and run:
```
cd Desktop\hermes-agent
ngrok http 3000
```

You will see something like:
```
Forwarding   https://abc123.ngrok-free.app -> http://localhost:3000
```

**Step 4:** Copy that `https://` URL.

**For WhatsApp:**
```
1. Go to: https://developers.facebook.com
2. Your App → WhatsApp → Configuration
3. Webhook URL: https://abc123.ngrok-free.app/webhook/whatsapp
4. Verify Token: (same word you put in .env)
5. Click Verify and Save
6. Subscribe to: messages
```

**For Telegram:**
```
Telegram sets webhook automatically when your agent starts.
Just send a message to your bot and check if it replies.
```

---

## STEP 9 — Upload to Hostinger Server

Once local testing works, upload your files to the server.

**Open WinSCP:**
1. Open WinSCP
2. File Protocol: **SCP**
3. Host name: `your_server_ip` (from Hostinger email)
4. User name: `root`
5. Password: (from Hostinger email)
6. Click **Login**

**Upload files:**
1. On the left side (your PC) — go to Desktop → `hermes-agent`
2. On the right side (server) — navigate to `/var/www/`
3. Right-click on the right side → **New Directory** → name it `hermes-agent`
4. Select all your files on the left → drag them to the right

> ⚠️ Do NOT upload the `node_modules` folder — it is too large.
> You will install packages on the server separately.

---

## STEP 10 — Connect to Server via SSH (Windows)

Windows 10 and 11 have SSH built in. Open a new terminal and type:

```
ssh root@your_server_ip
```

Type your password when asked (from Hostinger email).

> You are now inside your server. Everything you type runs on the server.

**Run these commands one by one on the server:**

```bash
# Go to your project
cd /var/www/hermes-agent
```

```bash
# Install Node.js on server
curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
apt-get install -y nodejs
```

```bash
# Install packages
npm install
```

```bash
# Install PM2 (keeps your app running 24/7)
npm install -g pm2
```

```bash
# Start your agent
pm2 start agent.js --name hermes-agent
```

```bash
# Auto-start after server reboot
pm2 startup
pm2 save
```

```bash
# Check if it is running
pm2 status
```

You should see `hermes-agent` with status `online` ✅

**Check logs if something is wrong:**
```bash
pm2 logs hermes-agent
```

---

### Nginx Setup (So your bot gets a proper URL)

```bash
# Install Nginx
apt install -y nginx

# Create config file
nano /etc/nginx/sites-available/hermes
```

Paste this inside (replace `your-domain.com` with your actual domain):

```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Press `Ctrl + X` → `Y` → `Enter` to save.

```bash
# Enable the config
ln -s /etc/nginx/sites-available/hermes /etc/nginx/sites-enabled/
nginx -t
systemctl restart nginx
```

**Free SSL (HTTPS):**
```bash
apt install -y certbot python3-certbot-nginx
certbot --nginx -d your-domain.com
```

Now update your `.env` on the server:
```bash
nano /var/www/hermes-agent/.env
```
Change:
```
NODE_ENV=production
WEBHOOK_BASE_URL=https://your-domain.com
```

Restart:
```bash
pm2 restart hermes-agent
```

---

## ✅ Final Checklist

| Step | Task | Done? |
|------|------|-------|
| 1 | Node.js installed | ☐ |
| 1 | Git installed | ☐ |
| 1 | VS Code installed | ☐ |
| 1 | WinSCP installed | ☐ |
| 1 | ngrok downloaded | ☐ |
| 2 | node, npm, git all show versions in terminal | ☐ |
| 3 | `hermes-agent` folder created on Desktop | ☐ |
| 4 | npm packages installed | ☐ |
| 5 | `.env` file created with all keys | ☐ |
| 6 | All 4 code files created | ☐ |
| 7 | `node agent.js` works locally | ☐ |
| 7 | `/health` shows OK in browser | ☐ |
| 8 | ngrok URL working | ☐ |
| 8 | WhatsApp webhook verified | ☐ |
| 8 | Telegram bot replies | ☐ |
| 9 | Files uploaded to Hostinger via WinSCP | ☐ |
| 10 | SSH connected to server | ☐ |
| 10 | `npm install` done on server | ☐ |
| 10 | PM2 running (`pm2 status` shows online) | ☐ |
| 10 | Nginx configured | ☐ |
| 10 | SSL certificate installed | ☐ |

---

## 🔧 Common Errors & Fixes

---

**Error: `node is not recognized`**
```
Fix: Restart your PC after installing Node.js, then try again.
```

**Error: `Cannot find module`**
```
Fix: Run  npm install  again in the hermes-agent folder.
```

**Error: WhatsApp webhook not verifying**
```
Fix: Make sure WHATSAPP_VERIFY_TOKEN in .env matches exactly
     what you typed in Meta Developer Console.
     Also make sure ngrok is running and URL is correct.
```

**Error: Telegram bot not replying**
```
Fix: Check your TELEGRAM_BOT_TOKEN in .env.
     Stop and restart: node agent.js
```

**Error: `EADDRINUSE: address already in use`**
```
Fix: Something else is using port 3000.
     In terminal type:  npx kill-port 3000
     Then start again:  node agent.js
```

**Error: PM2 app shows `errored` status**
```
Fix: Run  pm2 logs hermes-agent  to see what went wrong.
     Usually it is a missing .env key or typo in code.
```

**Server not responding after upload**
```
Fix: Check if PM2 is running:  pm2 status
     Check if Nginx is running:  systemctl status nginx
     Restart both:
       pm2 restart hermes-agent
       systemctl restart nginx
```

---

## 📞 Quick Reference

| What | Where |
|------|-------|
| OpenAI API Keys | https://platform.openai.com/api-keys |
| Claude API Keys | https://console.anthropic.com |
| WhatsApp Setup | https://developers.facebook.com |
| Telegram BotFather | https://t.me/BotFather |
| Hostinger Panel | https://hpanel.hostinger.com |
| ngrok Download | https://ngrok.com/download |
| WinSCP Download | https://winscp.net |

---

*Guide Version: Windows Setup — Simple English*
*Stack: Node.js · Express · PM2 · Nginx · Ubuntu 22.04 on Hostinger KVM 2*
