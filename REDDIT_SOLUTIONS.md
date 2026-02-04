# Reddit r/LocalLLaMA Solutions for OpenClaw + Ollama

## Thread Summary
**Source**: r/LocalLLaMA - "Getting OpenClaw to work with Qwen3:14b including tool calling"
**Author**: u/MarkVL
**Key Finding**: GLM-4-9B is fastest and most stable for local tool calling

---

## Quick Fix for Your "What is your name" Issue

### Step 1: Add Loop Prevention
```bash
# Edit config to add safety limits
nano ~/.openclaw/openclaw.json
```

Add these lines under `agents.defaults`:
```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/qwen2.5:14b"
      },
      "maxToolRounds": 5,        // ← ADD THIS
      "toolCallTimeout": 30000,   // ← ADD THIS (30 seconds)
      "maxConcurrent": 4,
      "subagents": {
        "maxConcurrent": 8
      }
    }
  }
}
```

Then restart:
```bash
systemctl --user restart openclaw
```

### Step 2: Test if Issue Persists
```bash
# Test via web interface at http://localhost:18789
# Ask: "What is your name?"
# Watch logs in real-time:
journalctl --user -u openclaw -f
```

---

## Better Solution: Switch to GLM-4 (Reddit's Top Pick)

### Why GLM-4-9B?
- ✅ **Fastest**: 15s responses vs 30-60s for Qwen
- ✅ **Best tool calling**: No loops reported by multiple users
- ✅ **Smaller**: 9B vs 14B (less RAM)
- ✅ **Better context handling**: Doesn't lose thread with many tools

### Installation on Windows (Ollama Server)

1. **On your Windows PC (100.76.189.18)**:
```powershell
# Install GLM-4-9B
ollama pull glm4:9b

# Verify it's installed
ollama list
```

2. **On your Debian Server**:
Edit `~/.openclaw/openclaw.json`:
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
      "maxToolRounds": 5,
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
```

4. **Test**:
```bash
# Verify model is available
openclaw models list

# Should show:
# ollama/glm4:9b    text    8k    no    yes    default
```

---

## Advanced: Increase Context Window (If Needed)

If you're using many tools and hitting context limits:

### On Windows Ollama Server:

Create a custom Modelfile:
```bash
# Create Modelfile
cat > Modelfile << 'EOF'
FROM qwen2.5:14b
PARAMETER num_ctx 8192
PARAMETER temperature 0.7
PARAMETER top_p 0.9
PARAMETER repeat_penalty 1.1
EOF

# Create custom model
ollama create qwen2.5:14b-ctx8k -f Modelfile

# Test it
ollama run qwen2.5:14b-ctx8k
```

Then update OpenClaw config to use `qwen2.5:14b-ctx8k`.

---

## Nuclear Option: Claude Haiku (Always Works)

If local models continue causing issues:

```bash
# 1. Get API key from https://openrouter.ai/
# 2. Add to OpenClaw config:
{
  "models": {
    "providers": {
      "openrouter": {
        "baseUrl": "https://openrouter.ai/api/v1",
        "apiKey": "sk-or-v1-YOUR-KEY-HERE",
        "api": "openai-completions",
        "models": [
          {
            "id": "anthropic/claude-3.5-haiku",
            "name": "Claude 3.5 Haiku",
            "reasoning": false,
            "input": ["text"],
            "contextWindow": 200000,
            "maxTokens": 8192
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "openrouter/anthropic/claude-3.5-haiku"
      }
    }
  }
}
```

**Cost**: ~$0.01 per 1000 tokens (very cheap for testing)

---

## Debugging Tool Loops

### Real-time Log Watching
```bash
# Watch for tool calls
journalctl --user -u openclaw -f | grep -E "tool|Tool"

# Watch for loops (same tool called repeatedly)
journalctl --user -u openclaw -f | grep -E "tool_start|tool_end"
```

### Check What Model Sees
```bash
# Test directly with Ollama to isolate OpenClaw issues
curl -s http://100.76.189.18:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5:14b",
    "messages": [
      {"role": "system", "content": "You are Mighty Raju, a helpful assistant."},
      {"role": "user", "content": "What is your name?"}
    ],
    "tools": [],
    "max_tokens": 100
  }' | jq .
```

### Force Disable Tools Temporarily
```bash
# Edit ~/.openclaw/openclaw.json
{
  "commands": {
    "native": "none",        // Disable all native tools
    "nativeSkills": "none"   // Disable all skills
  }
}
```

This helps test if the issue is:
- Model hallucinating tool calls
- OpenClaw providing too many tools
- Specific tool causing loops

---

## Model Comparison Matrix

| Metric | qwen2.5:14b (yours) | glm4:9b (recommended) | Claude Haiku |
|--------|---------------------|----------------------|--------------|
| **Speed** | 30-60s | 15-30s ⚡ | 2-5s 🚀 |
| **Tool Loops** | Medium risk ⚠️ | Low risk ✅ | None ✅ |
| **Context Quality** | Good 👍 | Excellent 👍👍 | Perfect 👍👍👍 |
| **Cost** | Free | Free | ~$0.01/1k tokens |
| **RAM Usage** | ~8GB | ~5GB | N/A (cloud) |
| **Your Issue** | Possible loops | Should fix it ✅ | Definitely fixes ✅ |

---

## What to Share with Me for Debugging

If issues persist, please provide:

1. **Logs during the loop**:
```bash
journalctl --user -u openclaw -n 100 --no-pager > /tmp/openclaw_logs.txt
cat /tmp/openclaw_logs.txt
```

2. **Exact prompt that causes loops**:
   - What you ask
   - What the bot responds
   - Does it loop immediately or after N turns?

3. **Model test results**:
```bash
# Test model directly
curl -s http://100.76.189.18:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5:14b",
    "messages": [{"role": "user", "content": "What is your name?"}],
    "max_tokens": 50
  }' | jq .
```

---

## Summary Action Plan

1. ✅ **Immediate**: Add `maxToolRounds: 5` to config (prevents runaway loops)
2. 🔧 **Recommended**: Install GLM-4-9B and test (Reddit's best performer)
3. 📊 **Compare**: Try both models side-by-side
4. 💰 **Backup**: Keep Claude Haiku config ready for critical tasks
5. 🐛 **Debug**: If issues persist, capture logs and share

Your setup is 90% correct - just needs loop protection and possibly a model swap!
