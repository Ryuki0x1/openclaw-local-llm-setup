# OpenClaw All Tools & Skills Guide

## 📊 Current Status: 5 of 50 Skills Ready

---

## ✅ ALREADY WORKING (No Setup Needed)

### 📦 **bluebubbles**
- **What**: iMessage integration for OpenClaw
- **Use**: Not needed (you have Telegram)

### 📦 **healthcheck**
- **What**: Security audits, system hardening
- **Use**: `openclaw skills info healthcheck`
- **Good for**: Server security checks

### 📦 **skill-creator**
- **What**: Create custom OpenClaw skills
- **Use**: For advanced users who want to extend OpenClaw

### 🧵 **tmux**
- **What**: Terminal multiplexer integration
- **Use**: Terminal session management
- **Status**: Ready to use

### 🌤️ **weather**
- **What**: Weather information
- **Use**: Ask bot "What's the weather?"
- **Status**: ✅ Ready!

---

## 🎯 RECOMMENDED TO SET UP (Easy & Useful)

### 🐙 **GitHub Integration**
**What it does**: Manage GitHub issues, PRs, CI/CD  
**Requirements**: `gh` CLI  
**Setup**:
```bash
# Install GitHub CLI
sudo apt install gh
gh auth login
```
**Use cases**:
- "Create an issue in my repo"
- "Show my open PRs"
- "Check CI status"

### 📧 **Himalaya (Email)**
**What it does**: Read/send emails via IMAP/SMTP  
**Requirements**: `himalaya` CLI  
**Setup**:
```bash
# Install
cargo install himalaya
# Or download from: https://github.com/soywod/himalaya
```
**Use cases**:
- "Check my email"
- "Send email to..."
- "Any unread messages?"

### 🌤️ **Weather (Already Ready!)**
**What it does**: Get weather information  
**Use cases**:
- "What's the weather?"
- "Will it rain today?"

### 📜 **Session Logs**
**What it does**: Search your chat history  
**Requirements**: `jq` and `rg` (ripgrep)  
**Setup**:
```bash
sudo apt install jq ripgrep
```
**Use cases**:
- "What did we talk about yesterday?"
- "Search my conversations for 'shopping'"

### 🎞️ **Video Frames**
**What it does**: Extract frames from videos  
**Requirements**: `ffmpeg`  
**Setup**:
```bash
sudo apt install ffmpeg
```
**Use cases**:
- "Extract frames from video.mp4"
- "Get thumbnail from clip"

---

## 💰 REQUIRES API KEYS (Optional)

### 📍 **Google Places**
**What it does**: Search for places, restaurants, etc.  
**Requirements**: Google Places API key  
**Cost**: Free tier available  
**Use cases**:
- "Find best restaurants near me"
- "Coffee shops in downtown"

### 📝 **Notion**
**What it does**: Create/manage Notion pages  
**Requirements**: Notion API key  
**Cost**: Free  
**Use cases**:
- "Add note to Notion"
- "Create meeting notes"

### 🖼️ **OpenAI Image Generation**
**What it does**: Generate images with DALL-E  
**Requirements**: OpenAI API key  
**Cost**: ~$0.02-0.08 per image  
**Use cases**:
- "Generate image of sunset"
- "Create logo for my app"

### 🎙️ **OpenAI Whisper API**
**What it does**: Speech to text  
**Requirements**: OpenAI API key  
**Cost**: $0.006 per minute  
**Use cases**:
- "Transcribe this audio"
- "Convert speech to text"

### 💬 **Slack**
**What it does**: Send Slack messages  
**Requirements**: Slack workspace + API token  
**Cost**: Free  
**Use cases**:
- "Send message to #general"
- "Post update to team"

### 📋 **Trello**
**What it does**: Manage Trello boards  
**Requirements**: Trello API key  
**Cost**: Free  
**Use cases**:
- "Add card to my board"
- "Show my Trello tasks"

---

## 🍎 MACOS ONLY (Not for Debian)

These won't work on your Debian server:
- 📝 Apple Notes
- ⏰ Apple Reminders
- 🐻 Bear Notes
- 📨 iMessage
- 👀 Peekaboo (macOS screenshots)
- ✅ Things
- 📊 Model Usage (CodexBar)

---

## 🔧 ADVANCED/SPECIALIZED TOOLS

### 🧩 **Coding Agent**
**What it does**: Run AI coding assistants  
**Requirements**: Codex/Claude Code/OpenCode  
**Use**: Advanced code generation

### 📦 **ClawHub**
**What it does**: Install community skills  
**Setup**: `npm install -g clawhub`  
**Use**: Expand capabilities with community tools

### 🎮 **Google Workspace (gog)**
**What it does**: Gmail, Calendar, Drive, Docs  
**Requirements**: `gog` CLI  
**Use cases**: Full Google Workspace integration

### 💡 **OpenHue**
**What it does**: Control Philips Hue lights  
**Requirements**: Philips Hue bridge  
**Use cases**: "Turn on living room lights"

### 🔊 **Sonos Control**
**What it does**: Control Sonos speakers  
**Requirements**: Sonos system  
**Use cases**: "Play music in kitchen"

### 🎵 **Spotify Player**
**What it does**: Control Spotify  
**Requirements**: `spogo` or `spotify_player`  
**Use cases**: "Play my playlist"

---

## 📝 MY RECOMMENDATIONS FOR YOU

Based on your use case (daily tasks, email, shopping), I recommend:

### **TIER 1 - Set Up Now (Easy & Useful)**
1. ✅ **Weather** - Already working!
2. 📧 **Himalaya** - Email integration
3. 📜 **Session Logs** - Search conversations
4. 🎞️ **Video Frames** - FFmpeg tools

### **TIER 2 - Consider Later**
5. 🐙 **GitHub** - If you code
6. 📍 **Google Places** - For local search
7. 📝 **Notion** - For note-taking
8. 💬 **Slack** - If you use Slack

### **TIER 3 - Optional**
9. 🖼️ **OpenAI Images** - AI image generation
10. 📋 **Trello** - Task management

---

## 🚀 QUICK SETUP COMMANDS

### Install Commonly Useful Tools:
```bash
# Email client
cargo install himalaya

# Session logs (search your chats)
sudo apt install jq ripgrep

# Video processing
sudo apt install ffmpeg

# GitHub CLI
sudo apt install gh
gh auth login
```

---

## 📊 NATIVE OPENCLAW COMMANDS (Built-in)

These work without any skills:

### **File Operations**
- Read/write files
- List directories
- Execute terminal commands

### **System Information**
- Time/date
- System stats
- Process management

### **Browser Control** (Configured)
- Open URLs
- Take screenshots
- Navigate web pages
- *Note: Limited by local model capabilities*

---

## 🎯 WHAT TO SET UP FIRST?

**For your needs (daily tasks, email, shopping):**

1. **Email (Himalaya)** - Check mail
2. **Weather** - Already works!
3. **Session Logs** - Search past conversations
4. **Google Places API** - Local search (optional, needs API key)

**Commands to run:**
```bash
# Email
cargo install himalaya

# Session logs
sudo apt install jq ripgrep

# That's it! Test with your bot.
```

---

## 📝 HOW TO USE SKILLS

Once installed, just ask your bot:
- "What's the weather in New York?"
- "Check my email"
- "Search for 'shopping' in our conversations"
- "Find restaurants near me" (if Google Places set up)

---

## 🔗 USEFUL LINKS

- **ClawHub**: https://clawhub.com (community skills)
- **Himalaya Email**: https://github.com/soywod/himalaya
- **GitHub CLI**: https://cli.github.com
- **OpenClaw Docs**: https://docs.openclaw.ai

---

## ❓ NEED HELP?

Check skill details:
```bash
openclaw skills info <skill-name>
```

Example:
```bash
openclaw skills info weather
openclaw skills info himalaya
```

---

**Want me to help you set up any specific tools? Just let me know which ones interest you!**
