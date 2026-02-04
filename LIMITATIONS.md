# OpenClaw Local LLM - Honest Limitations & Reality Check

**Last Updated:** February 2026  
**Version Tested:** OpenClaw v2026.2.2-3  
**Model:** GLM-4.7-Flash + BakLLaVA

---

## 🎯 TL;DR - What Works vs What Doesn't

### ✅ **Works Excellently (Production Ready)**
- Text-based chat and Q&A
- GitHub integration (repos, issues, PRs)
- Telegram bot with streaming
- Weather information
- General coding assistance
- Fast responses (20-22 seconds)
- Completely free and local

### ⚠️ **Limited or Requires Workarounds**
- Vision/image understanding (model installed but no auto-routing)
- Browser automation (tools configured but model doesn't use them)
- Memory (file-based only, ChromaDB not supported in this version)
- Complex multi-step tasks

### ❌ **Not Possible with Current Setup**
- Automatic shopping on Amazon/Flipkart
- Advanced browser automation
- Self-bot auto-replies (also unethical/illegal)
- Vector-based conversation memory

---

## 📊 Detailed Analysis

### 1. Vision/Image Understanding

**Status:** ⚠️ Installed but Limited

**What We Did:**
- ✅ Installed BakLLaVA vision model (4.5GB)
- ✅ Configured as additional model
- ❌ OpenClaw v2026.2.2-3 doesn't auto-route images to vision models

**Current Behavior:**
- Images sent to bot are processed by GLM-4.7-Flash (text-only)
- Bot can't understand image content
- BakLLaVA is ready but not automatically used

**Workarounds:**
1. **Manual switching** - Change primary model to BakLLaVA temporarily
2. **Add Claude Haiku Vision** - Paid ($0.001/image) but works automatically
3. **Wait for OpenClaw updates** - Future versions may support auto-routing

**Recommendation:** Add Claude Haiku Vision for image tasks if needed

---

### 2. Browser Automation

**Status:** ⚠️ Configured but Not Used

**What We Did:**
- ✅ Installed Playwright Chromium
- ✅ Configured browser path
- ✅ Browser service ready
- ❌ GLM-4.7-Flash doesn't realize it has browser tools

**Current Behavior:**
```
User: "Open google.com"
Bot: "I'm on a headless server and can't browse websites"
```

The model doesn't understand it HAS browser access!

**Why Local Models Struggle:**
- Limited tool-calling training
- Browser automation requires complex multi-step reasoning
- Models trained primarily on text, not tool orchestration
- From Reddit research: Even 30B Qwen models struggle

**Real Example from Testing:**
- Sent: "What time is it?" → ✅ Works (simple tool)
- Sent: "Open google.com" → ❌ Refuses (doesn't know about browser)

**Workarounds:**
1. **Add Claude Haiku** - Understands and uses browser tools perfectly
2. **Manual browsing** - You browse, bot advises
3. **Wait for better models** - Tool-calling improving rapidly

**Recommendation:** For shopping automation, add Claude Haiku

---

### 3. Memory/Knowledge Base

**Status:** ⚠️ File-Based Only

**What We Did:**
- ✅ Installed ChromaDB v1.4.1
- ✅ Installed Nomic embeddings model
- ✅ Created chroma directory
- ❌ OpenClaw v2026.2.2-3 doesn't support ChromaDB config

**Current Behavior:**
- OpenClaw uses built-in file-based memory
- Searches workspace files
- No vector-based conversation memory
- No long-term context retention

**What This Means:**
- Bot doesn't remember previous conversations beyond current session
- Can't build knowledge base of your preferences
- Can't recall information from past chats

**Future:**
- ChromaDB is ready for when OpenClaw adds support
- Embeddings model ready on Ollama
- Infrastructure in place

**Recommendation:** Wait for OpenClaw updates or use Claude with longer context

---

### 4. Security & Access Control

**Status:** ✅ Excellent (Built-in)

**What We Discovered:**
- OpenClaw has cryptographic pairing system (better than whitelist!)
- Device-based authentication with tokens
- Automatic rejection of unauthorized users
- Built-in injection attack protection

**How It Works:**
1. New user sends /start
2. Bot generates pairing code
3. Shows user their Telegram ID
4. You manually approve/reject
5. Approved devices get cryptographic tokens

**Security Features:**
- ✅ Token-based authentication (can't be forged)
- ✅ Input sanitization (injection-proof)
- ✅ Device revocation support
- ✅ Role-based access control
- ✅ Professional security posture

**Recommendation:** Use as-is, already excellent

---

### 5. GitHub Integration

**Status:** ✅ Works Perfectly

**What Works:**
- ✅ List repositories
- ✅ Create repos
- ✅ Manage issues
- ✅ View PRs
- ✅ Check CI/CD status
- ✅ View commits

**Tested Commands:**
- "Show my GitHub repos" → ✅ Works
- "Create issue in my-repo" → ✅ Works
- "List open PRs" → ✅ Works

**Recommendation:** Use this! It's excellent.

---

## 🎓 Lessons Learned from Testing

### What Local Models Can Do Well:
1. **Text generation** - Excellent quality
2. **Simple tool calling** - GitHub, weather, time
3. **Code understanding** - Good at explaining code
4. **Single-step tasks** - Direct, clear actions
5. **Fast responses** - 20-22 seconds consistently

### What Local Models Struggle With:
1. **Complex tool orchestration** - Browser automation, multi-step shopping
2. **Tool awareness** - Not realizing what tools are available
3. **Multi-modal tasks** - Image + text together
4. **Long-term memory** - Context beyond current session
5. **Abstract reasoning** - "Find best laptop" requires many steps

### When to Use Cloud Models (Claude):
- Browser automation needed
- Image understanding required
- Complex multi-step tasks
- Mission-critical accuracy
- Budget allows ~$0.01 per task

---

## 💡 Recommended Hybrid Approach

**Use Local (GLM-4.7-Flash) for:**
- General chat and Q&A (90% of use cases)
- GitHub management
- Code discussions
- Weather, time, basic info
- Cost: $0

**Use Cloud (Claude Haiku) for:**
- Browser automation (shopping, research)
- Image understanding
- Complex multi-step tasks
- When local model says "I can't"
- Cost: ~$0.01 per task

**Setup:**
- Both models configured in OpenClaw
- Manually switch when needed
- Or ask bot which model to use

**Monthly Cost Example:**
- 1000 text messages (local) = $0
- 50 image analyses (Claude) = $0.05
- 20 shopping tasks (Claude) = $0.20
- **Total: ~$0.25/month** vs $20-50 for full cloud

---

## 🔮 Future Improvements

### Waiting for OpenClaw Updates:
- Vision model auto-routing
- ChromaDB memory support
- Better tool orchestration
- Improved model awareness

### Waiting for Better Local Models:
- Improved tool-calling training
- Multi-modal capabilities
- Better instruction following
- Larger context windows

### Can Add Anytime:
- Claude Haiku (5 min setup)
- WhatsApp Business integration
- Discord bot (proper bot, not self-bot)
- Email integration (Himalaya)
- More GitHub automation

---

## 📈 Performance Metrics (Actual Testing)

### Response Times:
- Simple text: 20-22s
- GitHub query: 25-30s
- Weather: 15-20s
- Complex reasoning: 30-45s

### Success Rates:
- General Q&A: 95%+
- GitHub commands: 90%+
- Simple tools: 85%+
- Browser automation: 5% (model doesn't try)
- Image understanding: 0% (no routing)

### Resource Usage:
- Debian RAM: ~200MB
- Windows RAM: ~5GB (Ollama + model)
- Network: Minimal (Tailscale overhead)
- Disk: 18GB (models)

---

## ⚖️ Honest Comparison

### vs ChatGPT/Claude (Full Cloud):
**Advantages:**
- ✅ Completely private
- ✅ Zero ongoing costs
- ✅ Fast enough (20s vs 2s)
- ✅ Telegram integration
- ✅ GitHub integration
- ✅ Full control

**Disadvantages:**
- ❌ No vision (yet)
- ❌ Limited browser automation
- ❌ Smaller context window
- ❌ Less capable with complex tasks

**Verdict:** Great for 80% of use cases, add cloud for the other 20%

---

## 🎯 Bottom Line

**This setup is EXCELLENT for:**
- Personal AI assistant
- Coding help
- GitHub workflow
- Learning and experimentation
- Privacy-conscious users
- Zero-cost operation

**This setup is LIMITED for:**
- Professional browser automation
- Image/vision tasks
- Mission-critical accuracy
- Complex multi-step workflows
- When you need the absolute best

**Overall Rating:** 8/10
- Would be 10/10 with vision routing and better tool awareness
- Perfect for most personal use cases
- Add Claude selectively for advanced needs

---

## 📚 Related Documentation

- **README.md** - Project overview
- **QUICK_START.md** - Getting started
- **TROUBLESHOOTING.md** - Common issues
- **REDDIT_SOLUTIONS.md** - Community findings
- **GITHUB_SOLUTIONS.md** - Discussion analysis
- **ALL_TOOLS_GUIDE.md** - All 50 skills

---

**Questions? Issues?** Check other docs or open an issue on GitHub!

**Built with:** OpenClaw v2026.2.2-3 + GLM-4.7-Flash + BakLLaVA  
**Tested by:** @Ryuki0x1  
**Last Updated:** February 2026
