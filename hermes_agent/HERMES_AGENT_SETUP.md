# 🤖 Hermes Sales Agent — Complete Setup Guide

> **Stack:** Hermes Agent · OpenAI / Claude API · WhatsApp · Telegram · Hostinger KVM 2
> **Maqsad:** Ek fully automated AI sales agent jo WhatsApp aur Telegram par customers se deal kare.

---

## 📋 Table of Contents

1. [Prerequisites — Pehle kya chahiye](#1-prerequisites)
2. [Local Laptop Setup](#2-local-laptop-setup)
3. [Hermes Agent Installation](#3-hermes-agent-installation)
4. [API Keys Configure Karna](#4-api-keys-configure-karna)
5. [WhatsApp Integration](#5-whatsapp-integration)
6. [Telegram Bot Integration](#6-telegram-bot-integration)
7. [Hostinger KVM 2 Server Setup](#7-hostinger-kvm-2-server-setup)
8. [Server par Deploy Karna](#8-server-par-deploy-karna)
9. [Sales Agent ko Customize Karna](#9-sales-agent-ko-customize-karna)
10. [Testing & Verification](#10-testing--verification)
11. [Monitoring & Maintenance](#11-monitoring--maintenance)
12. [Troubleshooting](#12-troubleshooting)

---

## 1. Prerequisites

### 💻 Aapke Laptop par (Windows/macOS/Linux)

| Tool | Version | Download |
|------|---------|----------|
| Node.js | v18+ | https://nodejs.org |
| Git | Latest | https://git-scm.com |
| Python | 3.10+ | https://python.org |
| VS Code | Latest | https://code.visualstudio.com |

### 🔑 Accounts jo chahiye honge

- [ ] **OpenAI Account** → https://platform.openai.com (API key k liye)
- [ ] **Ya Anthropic Account** → https://console.anthropic.com (Claude API k liye)
- [ ] **Hostinger Account** → KVM 2 plan subscribe kiya hua
- [ ] **Meta Developer Account** → https://developers.facebook.com (WhatsApp k liye)
- [ ] **Telegram Account** → Bot banana k liye

### 🖥️ Hostinger KVM 2 Specs (Jo aapko milega)

```
RAM:    2 GB
CPU:    2 vCPU
Storage: 40 GB NVMe SSD
OS:     Ubuntu 22.04 LTS (recommended)
```

---

## 2. Local Laptop Setup

### Step 2.1 — Node.js Install Karo

```bash
# Windows (PowerShell as Admin)
winget install OpenJS.NodeJS

# macOS
brew install node

# Ubuntu/Debian Linux
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### Step 2.2 — Verify Installation

```bash
node --version    # v18.x.x hona chahiye
npm --version     # 9.x.x hona chahiye
git --version     # git version 2.x.x
```

### Step 2.3 — Project Folder Banao

```bash
mkdir hermes-sales-agent
cd hermes-sales-agent
git init
```

---

## 3. Hermes Agent Installation

### Step 3.1 — Hermes Agent Clone / Install

```bash
# Option A: NPM se install
npm install hermes-agent

# Option B: GitHub se clone (agar custom version ho)
git clone https://github.com/your-hermes-repo/hermes-agent.git .
npm install
```

### Step 3.2 — Project Structure

```
hermes-sales-agent/
├── 📁 src/
│   ├── agent.js          ← Main agent logic
│   ├── whatsapp.js       ← WhatsApp handler
│   ├── telegram.js       ← Telegram handler
│   └── sales-prompt.js   ← Sales persona & instructions
├── 📁 config/
│   └── settings.json     ← All configurations
├── 📄 .env               ← API keys (SECRET — git me add mat karna!)
├── 📄 .gitignore
├── 📄 package.json
└── 📄 ecosystem.config.js ← PM2 config (server k liye)
```

### Step 3.3 — .gitignore Banao

```bash
cat > .gitignore << 'EOF'
.env
node_modules/
*.log
.DS_Store
EOF
```

---

## 4. API Keys Configure Karna

### Step 4.1 — .env File Banao

```bash
touch .env
```

### Step 4.2 — .env File Fill Karo

```env
# ============================================
# AI PROVIDER (ek choose karo)
# ============================================

# Option A: OpenAI
OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
AI_PROVIDER=openai
AI_MODEL=gpt-4o

# Option B: Anthropic Claude
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
AI_PROVIDER=anthropic
AI_MODEL=claude-sonnet-4-20250514

# ============================================
# WHATSAPP (Meta Cloud API)
# ============================================
WHATSAPP_TOKEN=EAAxxxxxxxxxxxxxxxxxxxxxxxxxx
WHATSAPP_PHONE_NUMBER_ID=1234567890123
WHATSAPP_VERIFY_TOKEN=mera_secret_verify_token_123

# ============================================
# TELEGRAM
# ============================================
TELEGRAM_BOT_TOKEN=1234567890:AAHxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

# ============================================
# SERVER
# ============================================
PORT=3000
NODE_ENV=production
WEBHOOK_BASE_URL=https://aapka-domain.com
```

### Step 4.3 — OpenAI API Key Kaise Milegi

```
1. https://platform.openai.com/api-keys par jao
2. "Create new secret key" click karo
3. Key copy karo aur .env me paste karo
4. Billing setup karo: https://platform.openai.com/billing
```

### Step 4.4 — Claude API Key Kaise Milegi

```
1. https://console.anthropic.com par jao
2. "API Keys" section me jao
3. "Create Key" click karo
4. Key copy karo aur .env me paste karo
```

---

## 5. WhatsApp Integration

### Step 5.1 — Meta Developer App Banao

```
1. https://developers.facebook.com par login karo
2. "My Apps" → "Create App" click karo
3. App Type: "Business" select karo
4. App Name: "Hermes Sales Bot" rakho
5. "WhatsApp" product add karo
```

### Step 5.2 — WhatsApp Business API Setup

```
1. WhatsApp → Getting Started
2. "Add phone number" karo (ya test number use karo)
3. Phone Number ID copy karo → .env me rakho
4. Access Token generate karo → .env me rakho
```

### Step 5.3 — Webhook Configure Karo

```
Webhook URL: https://aapka-domain.com/webhook/whatsapp
Verify Token: (jo aapne .env me rakha)

Subscribe karo in fields:
✅ messages
✅ message_deliveries
✅ message_reads
```

### Step 5.4 — WhatsApp Handler Code (src/whatsapp.js)

```javascript
const express = require('express');
const router = express.Router();

// Webhook Verification
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

// Message Receive karna
router.post('/webhook/whatsapp', async (req, res) => {
  const body = req.body;

  if (body.object === 'whatsapp_business_account') {
    const entry = body.entry?.[0];
    const change = entry?.changes?.[0];
    const message = change?.value?.messages?.[0];

    if (message?.type === 'text') {
      const from = message.from;       // Customer ka number
      const text = message.text.body;  // Customer ka message

      console.log(`📱 WhatsApp se message: ${from} → "${text}"`);

      // AI se reply generate karo
      const reply = await generateSalesReply(text, from);

      // Reply bhejo
      await sendWhatsAppMessage(from, reply);
    }
    res.sendStatus(200);
  } else {
    res.sendStatus(404);
  }
});

// Message bhejna
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

## 6. Telegram Bot Integration

### Step 6.1 — Telegram Bot Banao

```
1. Telegram me @BotFather ko search karo
2. /newbot command bhejo
3. Bot ka naam rakho: "Hermes Sales Bot"
4. Username rakho: hermes_sales_bot
5. Token copy karo → .env me rakho (TELEGRAM_BOT_TOKEN)
```

### Step 6.2 — Bot Settings Configure Karo

```
BotFather me ye commands bhejo:

/setdescription
→ "AI-powered Sales Assistant — Hum aapki madad k liye yahan hain!"

/setcommands
→ start - Bot shuru karo
→ help - Madad lो
→ products - Hamare products dekhو
→ contact - Human se baat karo
```

### Step 6.3 — Telegram Handler Code (src/telegram.js)

```javascript
const TelegramBot = require('node-telegram-bot-api');

const bot = new TelegramBot(process.env.TELEGRAM_BOT_TOKEN, {
  // Development me polling, production me webhook
  polling: process.env.NODE_ENV !== 'production'
});

// /start command
bot.onText(/\/start/, (msg) => {
  const chatId = msg.chat.id;
  const name = msg.from.first_name;

  bot.sendMessage(chatId,
    `Assalam o Alaikum ${name}! 👋\n\n` +
    `Main Hermes hun, aapka AI Sales Assistant.\n` +
    `Koi bhi sawal poochein, main haazir hun! 🚀`
  );
});

// Har message handle karo
bot.on('message', async (msg) => {
  if (msg.text?.startsWith('/')) return; // Commands skip karo

  const chatId = msg.chat.id;
  const userId = msg.from.id.toString();
  const text = msg.text;

  console.log(`📨 Telegram se: ${msg.from.first_name} → "${text}"`);

  // Typing indicator
  bot.sendChatAction(chatId, 'typing');

  // AI se reply generate karo
  const reply = await generateSalesReply(text, userId);

  bot.sendMessage(chatId, reply, { parse_mode: 'Markdown' });
});

// Webhook setup (production k liye)
async function setupTelegramWebhook(baseUrl) {
  await bot.setWebHook(`${baseUrl}/webhook/telegram`);
  console.log('✅ Telegram Webhook set!');
}

module.exports = { bot, setupTelegramWebhook };
```

### Step 6.4 — Telegram npm Package Install

```bash
npm install node-telegram-bot-api
```

---

## 7. Hostinger KVM 2 Server Setup

### Step 7.1 — Hostinger se Server Kharidna

```
1. https://hostinger.com par jao
2. VPS Hosting → KVM 2 plan
3. OS: Ubuntu 22.04 LTS select karo
4. Location: Singapore ya Europe (Pakistan k liye)
5. Purchase complete karo
6. Email se IP address aur root password milega
```

### Step 7.2 — Server se Connect Karo (SSH)

```bash
# Windows me (PowerShell ya CMD)
ssh root@YOUR_SERVER_IP

# macOS / Linux
ssh root@YOUR_SERVER_IP

# Password enter karo (jo email me mila)
```

### Step 7.3 — Server Initial Setup

```bash
# Server update karo
apt update && apt upgrade -y

# Zaroori tools install karo
apt install -y curl wget git ufw nano htop

# Node.js 18 install karo
curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
apt-get install -y nodejs

# PM2 install karo (process manager)
npm install -g pm2

# Nginx install karo (reverse proxy)
apt install -y nginx

# Verify
node --version
npm --version
pm2 --version
nginx -v
```

### Step 7.4 — Firewall Setup

```bash
# UFW configure karo
ufw allow OpenSSH
ufw allow 80      # HTTP
ufw allow 443     # HTTPS
ufw allow 3000    # App port (temporary)
ufw enable

# Status check
ufw status
```

### Step 7.5 — Domain Setup (Optional lekin Recommended)

```
Hostinger DNS Settings me:
Type: A
Name: @
Value: YOUR_SERVER_IP

Subdomain k liye:
Type: A
Name: bot
Value: YOUR_SERVER_IP
```

---

## 8. Server par Deploy Karna

### Step 8.1 — Code Server par Upload Karo

```bash
# Option A: Git se (recommended)
# Pehle GitHub par repo banao, phir:
git clone https://github.com/aapka-username/hermes-sales-agent.git
cd hermes-sales-agent
npm install --production

# Option B: SCP se (local se directly)
scp -r ./hermes-sales-agent root@YOUR_SERVER_IP:/var/www/
```

### Step 8.2 — .env File Server par Banao

```bash
cd /var/www/hermes-sales-agent
nano .env

# Puri .env file paste karo (local wali)
# Ctrl+X → Y → Enter (save karo)
```

### Step 8.3 — PM2 Config File (ecosystem.config.js)

```javascript
module.exports = {
  apps: [{
    name: 'hermes-sales-agent',
    script: 'src/agent.js',
    instances: 1,
    autorestart: true,
    watch: false,
    max_memory_restart: '500M',
    env: {
      NODE_ENV: 'production',
      PORT: 3000
    },
    log_file: './logs/combined.log',
    out_file: './logs/out.log',
    error_file: './logs/error.log',
    time: true
  }]
};
```

### Step 8.4 — PM2 se Start Karo

```bash
# Logs folder banao
mkdir -p logs

# PM2 se start karo
pm2 start ecosystem.config.js

# Server restart hone par auto-start
pm2 startup
pm2 save

# Status check
pm2 status
pm2 logs hermes-sales-agent
```

### Step 8.5 — Nginx Reverse Proxy Configure Karo

```bash
nano /etc/nginx/sites-available/hermes-agent
```

```nginx
server {
    listen 80;
    server_name aapka-domain.com www.aapka-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```bash
# Enable karo
ln -s /etc/nginx/sites-available/hermes-agent /etc/nginx/sites-enabled/
nginx -t
systemctl restart nginx
```

### Step 8.6 — SSL Certificate (HTTPS) — Free!

```bash
# Certbot install karo
apt install -y certbot python3-certbot-nginx

# SSL certificate lo
certbot --nginx -d aapka-domain.com -d www.aapka-domain.com

# Auto-renewal check
certbot renew --dry-run
```

---

## 9. Sales Agent ko Customize Karna

### Step 9.1 — Sales Prompt File (src/sales-prompt.js)

```javascript
const SALES_SYSTEM_PROMPT = `
Tum "Hermes" ho — ek professional AI Sales Assistant.

## Tumhara Role:
- Company: [Aapki Company ka Naam]
- Products/Services: [Aapke Products describe karo]
- Tone: Friendly, professional, aur helpful
- Language: Urdu/English (customer jo use kare)

## Sales Strategy:
1. Pehle customer ki zaroorat samjho
2. Apna product naturally suggest karo
3. Objections handle karo professionally
4. Call-to-action dena mat bhulo

## Important Rules:
- Kabhi jhoot mat bolo
- Price sirf confirm hone par batao
- Complex queries k liye human ko forward karo: "Mein aapko hamare sales expert se connect karta hun"
- Replies short rakho (max 3-4 sentences WhatsApp par)

## Products:
[Yahan apne products/services ki list likho]

## Pricing:
[Yahan pricing structure likho]
`;

module.exports = SALES_SYSTEM_PROMPT;
```

### Step 9.2 — Main Agent File (src/agent.js)

```javascript
require('dotenv').config();
const express = require('express');
const SALES_PROMPT = require('./sales-prompt');

const app = express();
app.use(express.json());

// Conversation history store karna
const conversations = new Map();

// AI Reply Generate karna
async function generateSalesReply(userMessage, userId) {
  // History get karo
  if (!conversations.has(userId)) {
    conversations.set(userId, []);
  }
  const history = conversations.get(userId);

  // User message add karo
  history.push({ role: 'user', content: userMessage });

  let reply;

  if (process.env.AI_PROVIDER === 'openai') {
    // OpenAI
    const { OpenAI } = require('openai');
    const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

    const response = await openai.chat.completions.create({
      model: process.env.AI_MODEL || 'gpt-4o',
      messages: [
        { role: 'system', content: SALES_PROMPT },
        ...history.slice(-10) // Last 10 messages
      ],
      max_tokens: 500,
      temperature: 0.7
    });
    reply = response.choices[0].message.content;

  } else {
    // Anthropic Claude
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

  // Assistant reply history me add karo
  history.push({ role: 'assistant', content: reply });

  // History 20 messages tak limit karo
  if (history.length > 20) {
    conversations.set(userId, history.slice(-20));
  }

  return reply;
}

// Routes
app.use(require('./whatsapp'));
const { bot, setupTelegramWebhook } = require('./telegram');

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'OK', agent: 'Hermes Sales Agent', time: new Date() });
});

// Start server
const PORT = process.env.PORT || 3000;
app.listen(PORT, async () => {
  console.log(`🚀 Hermes Sales Agent running on port ${PORT}`);

  // Production me Telegram webhook set karo
  if (process.env.NODE_ENV === 'production' && process.env.WEBHOOK_BASE_URL) {
    await setupTelegramWebhook(process.env.WEBHOOK_BASE_URL);
  }
});

// Global function available karo
global.generateSalesReply = generateSalesReply;
```

### Step 9.3 — npm Packages Install

```bash
# Local aur server dono par
npm install openai @anthropic-ai/sdk express dotenv node-telegram-bot-api
```

---

## 10. Testing & Verification

### Step 10.1 — Local Testing

```bash
# Local par start karo
node src/agent.js

# Dusre terminal me test karo
curl http://localhost:3000/health
```

### Step 10.2 — ngrok se Local Webhook Test (Development)

```bash
# ngrok install karo
npm install -g ngrok

# Tunnel banao
ngrok http 3000

# Jo URL mile (e.g. https://abc123.ngrok.io)
# Us URL ko WhatsApp aur Telegram webhook me lagao
```

### Step 10.3 — WhatsApp Test

```
1. Meta Developer Console → WhatsApp → API Setup
2. Test number se apne number par message bhejo
3. Bot ka reply aana chahiye
```

### Step 10.4 — Telegram Test

```
1. Telegram me @aapka_bot_username search karo
2. /start bhejo
3. Koi sales question poochho
4. AI reply aana chahiye
```

### Step 10.5 — Server Status Check

```bash
# PM2 status
pm2 status

# Live logs dekhna
pm2 logs hermes-sales-agent --lines 50

# Server resources
htop
```

---

## 11. Monitoring & Maintenance

### Daily Check Commands

```bash
# App status
pm2 status

# Errors dekhna
pm2 logs hermes-sales-agent --err --lines 20

# Server resources
free -h          # RAM
df -h            # Disk
top              # CPU
```

### Auto-Restart Setup

```bash
# Agar app crash ho to auto restart (PM2 handles this)
pm2 restart hermes-sales-agent

# Nginx restart
systemctl restart nginx

# Full reboot ke baad sab automatically start hoga
# (pm2 startup aur pm2 save already run kiya tha)
```

### Backup Strategy

```bash
# Weekly .env aur config backup
cp /var/www/hermes-sales-agent/.env ~/backups/.env.backup
cp -r /var/www/hermes-sales-agent/config ~/backups/config-backup
```

---

## 12. Troubleshooting

### ❌ Bot Reply Nahi De Raha

```bash
# Logs check karo
pm2 logs hermes-sales-agent

# API key check karo
nano .env

# App restart karo
pm2 restart hermes-sales-agent
```

### ❌ Webhook Error

```bash
# Nginx status
systemctl status nginx

# SSL check
certbot certificates

# Port check
netstat -tlnp | grep 3000
```

### ❌ WhatsApp Webhook Verify Fail

```
Problem: Verify token match nahi ho raha
Solution:
1. .env me WHATSAPP_VERIFY_TOKEN check karo
2. Meta Console me same token likha ho
3. Server accessible ho (http/https se)
```

### ❌ Out of Memory Error

```bash
# Swap space add karo (KVM 2 k liye helpful)
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' >> /etc/fstab
```

### ❌ PM2 App Crash hota raha

```bash
# Error log dekhna
pm2 logs hermes-sales-agent --err

# Memory limit badhana (ecosystem.config.js me)
max_memory_restart: '800M'

# Phir reload
pm2 reload ecosystem.config.js
```

---

## 📊 Architecture Overview

```
Customer (WhatsApp / Telegram)
        ↓
  Hostinger KVM 2 Server
        ↓
   Nginx (Port 80/443)
        ↓
   Hermes Agent (Port 3000)
        ↓
  AI API (OpenAI / Claude)
        ↓
   Generated Sales Reply
        ↓
Customer ko wapas bhejo
```

---

## ✅ Final Checklist

- [ ] Node.js 18+ installed (local + server)
- [ ] .env file configured (both local and server)
- [ ] OpenAI ya Claude API key working
- [ ] WhatsApp Business API setup
- [ ] Telegram bot created
- [ ] Hostinger KVM 2 server ready
- [ ] Nginx configured aur running
- [ ] SSL certificate installed
- [ ] PM2 se app running
- [ ] PM2 startup configured
- [ ] WhatsApp webhook verified
- [ ] Telegram webhook set
- [ ] Local test passed
- [ ] Production test passed

---

## 📞 Quick Reference — Important URLs

| Service | URL |
|---------|-----|
| OpenAI Console | https://platform.openai.com |
| Anthropic Console | https://console.anthropic.com |
| Meta Developers | https://developers.facebook.com |
| Hostinger Panel | https://hpanel.hostinger.com |
| BotFather (Telegram) | https://t.me/BotFather |

---

*Prepared for: Hermes AI Sales Agent Deployment*
*Last Updated: May 2025*
*Stack: Node.js · Express · PM2 · Nginx · Ubuntu 22.04*
