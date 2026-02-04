# OpenClaw Quick Start Guide

## ✅ Your Setup Status

**Current Configuration:**
- ✅ OpenClaw v2026.2.2-3 running on Debian
- ✅ Ollama on Windows (100.76.189.18:11434)
- ✅ Connected via Tailscale
- ✅ Primary Model: `qwen2.5:14b`
- ✅ Gateway: Port 18789
- ✅ Permissions: Fixed (chmod 700)
- ✅ OAuth directory: Created

**Your Issue:**
- "What is your name" queries might be getting stuck
- Possible tool calling loops

---

## 🚀 Immediate Next Steps

### Option 1: Test Current Setup (Recommended First)
Your logs show OpenClaw is working correctly. Test it:

1. **Open Web Interface**: http://localhost:18789
2. **Ask simple questions**:
   - "What is your name?"
   - "What time is it?"
   - "Calculate 2+2"
3. **Watch for loops**:
   ```bash
   journalctl --user -u openclaw -f
   ```

If it works → You're good! No changes needed.
If it loops → Try Option 2 or 3 below.

---

### Option 2: Switch to GLM-4 (Reddit's Best Performer) ⚡

**Why?** Reddit users report GLM-4-9B is:
- 2-3x faster than Qwen2.5:14b
- Better at tool calling (no loops)
- Smaller (9B vs 14B)

**How to Install:**

1. **On Windows (100.76.189.18)**:
   ```powershell
   # Download GLM-4
   ollama pull glm4:9b
   
   # Verify
   ollama list
   ```

2. **On Debian (your server)**:
   ```bash
   # Backup current config
   cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.backup
   
   # Edit config
   nano ~/.openclaw/openclaw.json
   ```
   
   Add GLM-4 to the models array and change primary:
   ```json
   {
     "models": {
       "providers": {
         "ollama": {
           "baseUrl": "http://100.76.189.18:11434/v1",
           "apiKey": "ollama-local",
           "api": "openai-completions",
           "models": [
             {
               "id": "glm4:9b",
               "name": "GLM-4 9B",
               "reasoning": false,
               "input": ["text"],
               "cost": {
                 "input": 0,
                 "output": 0,
                 "cacheRead": 0,
                 "cacheWrite": 0
               },
               "contextWindow": 8192,
               "maxTokens": 4096
             },
             {
               "id": "qwen2.5:14b",
               "name": "Qwen 2.5 14B",
               "reasoning": false,
               "input": ["text"],
               "cost": {
                 "input": 0,
                 "output": 0,
                 "cacheRead": 0,
                 "cacheWrite": 0
               },
               "contextWindow": 32768,
               "maxTokens": 8192
             }
           ]
         }
       }
     },
     "agents": {
       "defaults": {
         "model": {
           "primary": "ollama/glm4:9b"
         },
         "maxConcurrent": 4,
         "subagents": {
           "maxConcurrent": 8
         }
       }
     }
   }
   ```

3. **Restart OpenClaw**:
   ```bash
   systemctl --user restart openclaw
   
   # Verify model is loaded
   openclaw models list
   ```

4. **Test**: Ask "What is your name?" again

---

### Option 3: Use Claude Haiku (Nuclear Option) 💰

If local models keep causing issues, Claude Haiku is the gold standard:
- Works perfectly every time
- 10x faster than local models
- Costs ~$0.01 per 1000 tokens (very cheap)

**Setup:**
1. Get API key from https://openrouter.ai/
2. Edit `~/.openclaw/openclaw.json` and add OpenRouter provider
3. See `.openclaw/REDDIT_SOLUTIONS.md` for full instructions

---

## 🐛 Debugging Guide

### If You're Seeing Loops:

**Watch logs in real-time:**
```bash
journalctl --user -u openclaw -f | grep -E "tool|Tool"
```

**Test Ollama directly (bypass OpenClaw):**
```bash
curl -s http://100.76.189.18:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5:14b",
    "messages": [
      {"role": "system", "content": "You are Mighty Raju, a technical AI assistant."},
      {"role": "user", "content": "What is your name? Answer in one sentence."}
    ],
    "max_tokens": 50
  }' | jq .
```

**Check if specific question causes it:**
- Try different phrasings
- Try simpler questions
- Try questions that don't need tools

---

## 📊 Model Comparison (From Reddit)

| Model | Your Setup | Speed | Tool Loops | Quality |
|-------|-----------|-------|------------|---------|
| **qwen2.5:14b** | ✅ Current | 30-60s | Medium risk | Good |
| **glm4:9b** | 🔧 Recommended | 15-30s | Low risk | Excellent |
| **Claude Haiku** | 💰 Backup | 2-5s | None | Perfect |

---

## 📝 Files Created for You

1. **`.openclaw/TROUBLESHOOTING.md`** - Comprehensive troubleshooting
2. **`.openclaw/REDDIT_SOLUTIONS.md`** - Detailed Reddit solutions
3. **`.openclaw/QUICK_START.md`** - This file (quick reference)

---

## 🔍 What to Do Right Now

1. **Test your current setup first** - It might already work!
   ```bash
   # Access web interface
   http://localhost:18789
   ```

2. **If you see loops**:
   - Option A: Try GLM-4 (free, faster)
   - Option B: Try Claude Haiku (paid, perfect)

3. **Share results with me**:
   - Does it loop on "What is your name?"
   - Or does it work fine?
   - What exact behavior are you seeing?

---

## 🆘 Quick Commands Reference

```bash
# Restart OpenClaw
systemctl --user restart openclaw

# Check status
systemctl --user status openclaw

# Watch logs
journalctl --user -u openclaw -f

# List models
openclaw models list

# Run diagnostics
openclaw doctor

# Test Ollama connection
curl http://100.76.189.18:11434/v1/models
```

---

## 💡 Pro Tips from Reddit

1. **GLM-4 is fastest** for local tool calling
2. **Qwen2.5 > Qwen3** for stability
3. **Context window matters** - more tools = need bigger context
4. **Your v2026.2.2-3 is recent** - should have most bug fixes
5. **Loop protection** - Not available in current config schema, but model should handle it

---

**Ready to test? Tell me what happens! 🚀**
