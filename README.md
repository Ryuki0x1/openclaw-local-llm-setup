# OpenClaw Local LLM Setup

Complete setup guide for running OpenClaw with local LLM (GLM-4.7-Flash) on Debian + Ollama on Windows via Tailscale.

## 🎯 Overview

This repository documents a fully functional OpenClaw setup with:
- **Local LLM**: GLM-4.7-Flash (via Ollama on Windows)
- **Telegram Bot**: Real-time messaging integration
- **GitHub Integration**: Manage repos, issues, PRs via chat
- **Browser Automation**: Playwright Chromium configured
- **Zero API Costs**: Completely local (except optional Claude Haiku)

## 📊 System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  Debian Server (100.108.37.20)                              │
│  ├── OpenClaw Gateway (port 18789)                          │
│  ├── Telegram Bot (@mightyrajbot)                           │
│  ├── Playwright Chromium                                    │
│  └── ChromaDB (v1.4.1)                                      │
└─────────────────────────────────────────────────────────────┘
                          │
                     Tailscale VPN
                          │
┌─────────────────────────────────────────────────────────────┐
│  Windows PC (100.76.189.18)                                 │
│  ├── Ollama Server (port 11434)                             │
│  ├── GLM-4.7-Flash:q4_K_M (17.7GB)                          │
│  └── Nomic-Embed-Text (embeddings)                          │
└─────────────────────────────────────────────────────────────┘
```

## ✅ Features

### Working Now:
- ✅ **Chat & Q&A** - Fast responses (20-22 seconds)
- ✅ **Telegram Integration** - @mightyrajbot with streaming
- ✅ **GitHub Management** - Repos, issues, PRs, CI/CD
- ✅ **Web Interface** - http://localhost:18789
- ✅ **Weather Info** - Built-in skill
- ✅ **No Tool Loops** - Stable performance

### Configured (Model-Limited):
- ⚠️ **Browser Automation** - Configured but local models have limited tool use
- ⚠️ **Memory** - File-based (ChromaDB ready for future versions)

## 🚀 Quick Start

### Prerequisites
- Debian 12 server (or similar Linux)
- Windows PC with Ollama
- Tailscale for networking
- Node.js v24+ (via nvm)

### Installation

```bash
# 1. Install OpenClaw
npm install -g openclaw

# 2. Install Ollama on Windows
# Download from: https://ollama.com

# 3. Pull GLM-4.7-Flash on Windows
ollama pull glm-4.7-flash

# 4. Configure OpenClaw (see docs/)
openclaw setup
```

## 📚 Documentation

Comprehensive guides included:

- **[QUICK_START.md](QUICK_START.md)** - Get started quickly
- **[TROUBLESHOOTING.md](TROUBLESHOOTING.md)** - Common issues & fixes
- **[REDDIT_SOLUTIONS.md](REDDIT_SOLUTIONS.md)** - Community solutions (r/LocalLLaMA)
- **[GITHUB_SOLUTIONS.md](GITHUB_SOLUTIONS.md)** - GitHub Discussion #2936 analysis
- **[ALL_TOOLS_GUIDE.md](ALL_TOOLS_GUIDE.md)** - All 50 OpenClaw skills explained

## 🔧 Key Configuration

### Model Selection
After extensive testing (Reddit + GitHub research):
- ❌ Qwen2.5:14b - Tool calling loops
- ❌ Qwen3:30b - LaTeX formatting issues
- ✅ **GLM-4.7-Flash** - Best performance (2x faster, stable)

### Critical Settings
```json
{
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "http://100.76.189.18:11434/v1",
        "models": [{
          "id": "glm-4.7-flash:q4_K_M",
          "reasoning": false  // CRITICAL: Must be false!
        }]
      }
    }
  }
}
```

## 🎯 Performance Metrics

| Metric | Value |
|--------|-------|
| Model | GLM-4.7-Flash (9B params, q4_K_M) |
| Response Time | 20-22 seconds |
| Context Window | 32,768 tokens |
| Tool Loops | None (fixed with GLM-4.7) |
| Memory Usage | ~188MB (Debian) + ~5GB (Windows) |
| Cost | $0 (fully local) |

## 🐛 Known Limitations

1. **Browser Automation**: Local models don't use complex tools well yet
   - **Workaround**: Add Claude Haiku for advanced features (~$0.01/1k tokens)
   
2. **Memory**: File-based only (ChromaDB needs newer OpenClaw)
   - **Status**: ChromaDB installed, ready for future versions

3. **Tool Calling**: Works for simple tools (GitHub, weather)
   - **Complex tools**: May need cloud models

## 🔗 Integrations

### Active:
- ✅ Telegram Bot
- ✅ GitHub CLI
- ✅ Playwright Chromium
- ✅ Weather API

### Available (50 skills):
See [ALL_TOOLS_GUIDE.md](ALL_TOOLS_GUIDE.md) for full list

## 📦 What's Included

```
openclaw-local-llm-setup/
├── README.md                 # This file
├── QUICK_START.md           # Getting started
├── TROUBLESHOOTING.md       # Issues & fixes
├── REDDIT_SOLUTIONS.md      # Community wisdom
├── GITHUB_SOLUTIONS.md      # GitHub Discussion analysis
├── ALL_TOOLS_GUIDE.md       # All 50 skills explained
└── .gitignore               # Excludes sensitive data
```

## 🔒 Security

**Excluded from this repo:**
- API keys & tokens
- Bot credentials
- Session data
- Auth profiles
- Personal configurations

**Included safely:**
- Documentation
- Setup guides
- Configuration templates
- Troubleshooting tips

## 🎓 Research Summary

This setup is based on:
- Reddit r/LocalLLaMA thread (27 comments analyzed)
- GitHub Discussion #2936 (community solutions)
- Extensive testing of Qwen vs GLM models
- Real-world usage patterns

**Key Finding**: GLM-4.7-Flash outperforms larger Qwen models for tool calling!

## 🤝 Contributing

Found this helpful? Issues and PRs welcome!

## 📝 License

MIT License - Use freely, attribution appreciated

## 🙏 Credits

- OpenClaw team
- Ollama project
- Reddit r/LocalLLaMA community
- GitHub Discussion contributors
- GLM-4 by Zhipu AI

## 📞 Support

Check the documentation files for:
- Setup help
- Troubleshooting
- Community solutions
- Advanced configurations

---

**Built by**: Ryuki (@Ryuki0x1)  
**Date**: February 2026  
**Status**: Production-ready ✅  
**Stars**: ⭐ If this helped you!
