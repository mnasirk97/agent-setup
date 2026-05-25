# 🤖 Hermes Sales Agent — Windows + Hostinger KVM 2 Setup Guide

> **NousResearch Hermes Agent · Windows (WSL2) · OpenAI / Claude API · WhatsApp · Telegram · Hostinger KVM 2**

---

## 📋 Table of Contents

1. [Requirements](#1-requirements)
2. [Windows — WSL2 Install Karo](#2-windows--wsl2-install-karo)
3. [Hermes Install Karo (WSL2 me)](#3-hermes-install-karo-wsl2-me)
4. [API Key Setup](#4-api-key-setup)
5. [Telegram Connect Karo](#5-telegram-connect-karo)
6. [WhatsApp Connect Karo](#6-whatsapp-connect-karo)
7. [Sales Personality Setup (soul.md)](#7-sales-personality-setup-soulmd)
8. [Local Test Karo](#8-local-test-karo)
9. [Hostinger KVM 2 — Server Setup](#9-hostinger-kvm-2--server-setup)
10. [Server par Deploy Karo](#10-server-par-deploy-karo)
11. [Gateway 24/7 Service](#11-gateway-247-service)
12. [Useful Commands](#12-useful-commands)
13. [Troubleshooting](#13-troubleshooting)

---

## 1. Requirements

### Aapke Windows Laptop par

- Windows 10 (Build 19041+) ya Windows 11
- Internet connection
- Virtualization enabled in BIOS (usually already on)

> **Check karo:** Start menu me `winver` type karo — build number 19041 ya upar hona chahiye.

### Accounts jo pehle se bana lo

| Account | Link | Kyun Chahiye |
|---------|------|--------------|
| OpenAI | https://platform.openai.com | Claude ya GPT API key |
| Anthropic (Claude) | https://console.anthropic.com | Claude API key |
| Meta Developer | https://developers.facebook.com | WhatsApp Business API |
| Telegram | Telegram App | Bot banana |
| Hostinger | https://hostinger.com | KVM 2 server |

### Hostinger KVM 2 Specs

```
RAM:     2 GB
CPU:     2 vCPU  
Storage: 40 GB NVMe SSD
OS:      Ubuntu 22.04 LTS  ← yahi select karna
```

---

## 2. Windows — WSL2 Install Karo

### Step 2.1 — PowerShell Admin me kholو

- Start menu me `PowerShell` search karo
- Right click → **Run as Administrator**

### Step 2.2 — WSL2 Install Command

```powershell
wsl --install
```

Yeh automatically install karega:
- WSL2 (Windows Subsystem for Linux)
- Ubuntu (latest)

### Step 2.3 — PC Restart Karo

Restart ke baad Ubuntu setup complete hoga. Ek username aur password set karo (yeh aapka Linux password hai).

### Step 2.4 — WSL2 Version Confirm Karo

PowerShell me:
```powershell
wsl --list --verbose
```
`VERSION` column me `2` hona chahiye. ✅

---

## 3. Hermes Install Karo (WSL2 me)

### Step 3.1 — Ubuntu Terminal Kholو

Start menu me **Ubuntu** search karo → open karo. Ek black/purple terminal aayega.

### Step 3.2 — Hermes Install Command

```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

Yeh automatically install karega:
- Python 3.11
- Node.js
- ripgrep, ffmpeg
- Hermes Agent khud

> ⏳ 2-5 minute lagenge. Wait karo.

### Step 3.3 — Shell Reload Karo

```bash
source ~/.bashrc
```

### Step 3.4 — Verify

```bash
hermes --version
```

Version number aana chahiye. ✅

---

## 4. API Key Setup

### Step 4.1 — Setup Wizard Chalao

```bash
hermes setup
```

Ek interactive menu khulega. **Full Setup** select karo.

### Step 4.2 — AI Provider Choose Karo

Wizard me aapko poochega — kaunsa AI use karna hai:

**Option A — OpenAI (GPT-4o)**
```
1. https://platform.openai.com/api-keys par jao
2. "Create new secret key" click karo
3. Key copy karo (sk-proj-xxxx...)
4. Hermes wizard me paste karo
```

**Option B — Anthropic Claude**
```
1. https://console.anthropic.com par jao
2. Left menu me "API Keys" click karo
3. "Create Key" click karo
4. Key copy karo (sk-ant-xxxx...)
5. Hermes wizard me paste karo
```

> 💡 **Recommendation:** Claude `claude-sonnet-4-20250514` ya OpenAI `gpt-4o` — dono sales k liye best hain.

### Step 4.3 — Model Set Karo (baad me bhi change kar sakte ho)

```bash
hermes model
```

---

## 5. Telegram Connect Karo

### Step 5.1 — Telegram Bot Banao

```
1. Telegram app kholو
2. @BotFather search karo
3. /newbot bhejo
4. Bot naam rakho: "Hermes Sales Bot"
5. Username rakho: hermes_sales_yourname_bot
6. Token milega: 1234567890:AAHxxxxxxxxxxxxxxxx
   → Yeh copy kar lo
```

### Step 5.2 — Apna Telegram User ID Lo

```
1. Telegram me @userinfobot search karo
2. /start bhejo
3. Aapka numeric ID milega (e.g. 987654321)
   → Yeh bhi copy kar lo
```

### Step 5.3 — Gateway Setup me Telegram Add Karo

```bash
hermes gateway setup
```

Wizard me **Telegram** select karo aur:
- Bot Token paste karo
- Aapka User ID paste karo (taake sirf aap hi use kar sako)

---

## 6. WhatsApp Connect Karo

### Step 6.1 — Meta Developer App Banao

```
1. https://developers.facebook.com par jao
2. "My Apps" → "Create App"
3. App Type: Business
4. App Name: Hermes Sales Bot
5. "WhatsApp" product add karo
```

### Step 6.2 — Tokens Lo

```
WhatsApp → API Setup section me:
- Access Token copy karo
- Phone Number ID copy karo
```

### Step 6.3 — Gateway Setup me WhatsApp Add Karo

```bash
hermes gateway setup
```

Wizard me **WhatsApp** select karo aur tokens paste karo.

> ⚠️ WhatsApp Business API k liye Meta verification lagti hai. Testing k liye sandbox mode use karo pehle.

---

## 7. Sales Personality Setup (soul.md)

Yeh file aapke agent ki "personality" define karti hai — yahan aap likhte ho ke agent kaise baat kare, kya bechay, kya nahi kahe.

### Step 7.1 — soul.md File Kholو

```bash
nano ~/.hermes/soul.md
```

### Step 7.2 — Sales Personality Likho

Neeche ek template hai — apne products k hisab se edit karo:

```markdown
# Hermes Sales Agent — Soul

## Identity
Your name is Hermes. You are a friendly, professional sales assistant for [COMPANY NAME].
You talk in the same language the customer uses — Urdu or English.

## Your Job
- Understand what the customer needs
- Suggest the right product naturally and honestly
- Answer questions about products, prices, and delivery
- Handle objections politely and patiently
- Always end with a clear next step (order, call, visit)

## Company
- Name: [YOUR COMPANY NAME]
- Products: [LIST YOUR PRODUCTS HERE]
- Prices: [LIST YOUR PRICES HERE]
- Contact: [YOUR PHONE / EMAIL]
- Location: [YOUR CITY / ADDRESS]

## Tone & Style
- Friendly and warm — like talking to a helpful shopkeeper
- Keep replies short for WhatsApp (2-4 sentences max)
- Never lie about products or availability
- If a question is too complex, say: "Let me connect you with our team"

## Rules
- Never give fake discounts or promises you cannot keep
- If asked about something outside your products, politely redirect
- Greet every new customer warmly
```

**Save karo:** `Ctrl + X` → `Y` → `Enter`

---

## 8. Local Test Karo

### Step 8.1 — Hermes CLI me Test

```bash
hermes
```

Ek chat interface khulega. Koi bhi message type karo — agent ko reply dena chahiye. ✅

### Step 8.2 — Health Check

```bash
hermes doctor
```

Sab green hona chahiye. Agar koi red hai — wo fix karo pehle.

### Step 8.3 — Gateway Locally Test Karo

```bash
hermes gateway
```

Ab Telegram par apne bot ko message karo — reply aana chahiye. ✅

`Ctrl + C` se band karo.

---

## 9. Hostinger KVM 2 — Server Setup

### Step 9.1 — Server Kharidna

```
1. https://hostinger.com par jao
2. VPS Hosting → KVM 2 plan
3. OS select karo: Ubuntu 22.04 LTS
4. Location: Singapore (Pakistan k liye sabse close)
5. Purchase karo
6. Email aayega → IP address aur root password hoga
```

### Step 9.2 — SSH se Server Connect Karo

WSL2 terminal me:
```bash
ssh root@YOUR_SERVER_IP
```

Password enter karo (jo email me mila). Ab aap server ke andar hain.

### Step 9.3 — Server Initial Setup

```bash
# Update karo
apt update && apt upgrade -y

# Zaroori tools
apt install -y curl wget git ufw

# Firewall setup
ufw allow OpenSSH
ufw allow 80
ufw allow 443
ufw enable
```

---

## 10. Server par Deploy Karo

### Step 10.1 — Hermes Server par Install Karo

Server par (SSH ke zariye):
```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
source ~/.bashrc
```

### Step 10.2 — Setup Karo

```bash
hermes setup
```

Same API keys enter karo jo laptop par kiye the.

### Step 10.3 — soul.md Copy Karo

**Laptop par** (WSL2 terminal me) apna soul.md server par bhejو:
```bash
scp ~/.hermes/soul.md root@YOUR_SERVER_IP:~/.hermes/soul.md
```

Ya server par dobara likho:
```bash
nano ~/.hermes/soul.md
```

### Step 10.4 — Messaging Platforms Configure Karo

```bash
hermes gateway setup
```

Same Telegram token aur WhatsApp credentials enter karo.

---

## 11. Gateway 24/7 Service

Yeh step bohot zaroori hai — iske bina bot server restart hone par band ho jaega.

### Step 11.1 — Systemd Service Install Karo

```bash
hermes gateway install
```

Yeh automatically ek systemd service banata hai jo:
- Server start hone par automatically chale
- Crash hone par restart ho
- Background me chale

### Step 11.2 — Service Start Karo

```bash
hermes gateway start
```

### Step 11.3 — Status Check Karo

```bash
hermes gateway status
```

`active (running)` hona chahiye. ✅

### Step 11.4 — Logs Dekhna

```bash
journalctl -u hermes-gateway -f
```

`Ctrl + C` se band karo.

---

## 12. Useful Commands

### Roz Kaam Aane Wale Commands

| Command | Kaam |
|---------|------|
| `hermes` | Local chat start karo |
| `hermes setup` | Settings change karo |
| `hermes model` | AI model change karo |
| `hermes gateway` | Gateway foreground me chalao |
| `hermes gateway setup` | Messaging platforms configure karo |
| `hermes gateway install` | 24/7 service install karo |
| `hermes gateway start` | Service start karo |
| `hermes gateway stop` | Service stop karo |
| `hermes gateway status` | Service status check karo |
| `hermes update` | Latest version pe update karo |
| `hermes doctor` | Sab theek hai check karo |

### Chat ke Andar (Telegram/WhatsApp) Commands

| Command | Kaam |
|---------|------|
| `/new` | Nai conversation shuru karo |
| `/reset` | History clear karo |
| `/model` | AI model dekho/badlo |
| `/status` | Session info dekho |
| `/stop` | Agent rok do |

---

## 13. Troubleshooting

---

**❌ WSL2 install nahi ho raha**
```
Fix: BIOS me Virtualization enable karo.
PC restart → BIOS me jao (Del ya F2) → 
"Intel VT-x" ya "AMD-V" enable karo → Save → Exit
```

**❌ `hermes: command not found`**
```bash
source ~/.bashrc
# Ya terminal band karke dobara kholو
```

**❌ API key error**
```bash
hermes setup
# Dobara key enter karo — make sure koi space nahi hai key me
```

**❌ Telegram bot reply nahi de raha**
```
1. hermes doctor chalao
2. Gateway running hai check karo: hermes gateway status
3. Bot token sahi hai check karo: hermes gateway setup
```

**❌ WhatsApp webhook fail**
```
Server pe public IP/domain hona chahiye.
Localhost par WhatsApp webhook kaam nahi karta.
Pehle Hostinger server par deploy karo, phir WhatsApp connect karo.
```

**❌ Server par hermes update nahi ho raha**
```bash
hermes update
source ~/.bashrc
hermes gateway restart
```

**❌ Service crash hoti rahti hai**
```bash
# Logs dekhو
journalctl -u hermes-gateway -n 50

# Dobara install karo
hermes gateway install
hermes gateway start
```

**❌ Out of memory (KVM 2 par)**
```bash
# Swap space add karo
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' >> /etc/fstab
```

---

## 📊 Final Architecture

```
Customer message karta hai
     ↓
WhatsApp / Telegram
     ↓
Hostinger KVM 2 Server
     ↓
Hermes Gateway (systemd service — 24/7)
     ↓
Hermes Agent (soul.md personality)
     ↓
OpenAI GPT-4o / Claude API
     ↓
Sales reply generate hoti hai
     ↓
Customer ko wapas bheja jata hai
```

---

## ✅ Complete Checklist

**Windows Setup:**
- [ ] Windows 10 Build 19041+ ya Windows 11
- [ ] WSL2 install (`wsl --install`)
- [ ] Ubuntu terminal kaam kar raha hai
- [ ] Hermes install (`curl | bash`)
- [ ] `hermes --version` kaam kar raha hai

**Configuration:**
- [ ] `hermes setup` complete
- [ ] OpenAI ya Claude API key add
- [ ] Telegram bot banaya (@BotFather)
- [ ] Telegram gateway configure kiya
- [ ] WhatsApp credentials ready
- [ ] `soul.md` sales personality likhi

**Testing:**
- [ ] `hermes` CLI me local test pass
- [ ] `hermes doctor` sab green
- [ ] Telegram bot locally reply de raha hai

**Hostinger Deployment:**
- [ ] KVM 2 server kharida (Ubuntu 22.04)
- [ ] SSH se connect ho gaye
- [ ] Server update kiya
- [ ] Hermes server par install kiya
- [ ] `hermes setup` server par complete
- [ ] `soul.md` server par copy kiya
- [ ] Gateway configure kiya
- [ ] `hermes gateway install` — 24/7 service
- [ ] `hermes gateway status` → active ✅
- [ ] Telegram bot server se reply de raha hai ✅

---

## 📞 Quick Reference Links

| Resource | Link |
|----------|------|
| Hermes Official Site | https://hermes-agent.nousresearch.com |
| Hermes GitHub | https://github.com/NousResearch/hermes-agent |
| Hermes Docs | https://hermes-agent.nousresearch.com/docs |
| Hermes Discord | https://discord.gg/NousResearch |
| OpenAI API Keys | https://platform.openai.com/api-keys |
| Claude API Keys | https://console.anthropic.com |
| Meta (WhatsApp) | https://developers.facebook.com |
| Telegram BotFather | https://t.me/BotFather |
| Hostinger Panel | https://hpanel.hostinger.com |

---

*Stack: NousResearch Hermes Agent · WSL2 Ubuntu · Hostinger KVM 2 · Systemd Service*
*Last Updated: May 2026*
