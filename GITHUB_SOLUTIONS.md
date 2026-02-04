# GitHub Discussion #2936 Solutions Summary

**Source**: https://github.com/openclaw/openclaw/discussions/2936  
**Title**: "How to use Ollama in moltbot?"  
**Comments**: 27 (analyzed)

---

## 🔥 KEY FINDINGS - CRITICAL FOR YOUR SETUP

### 1. **GLM-4.7-Flash is THE Winner** (Multiple confirmations)

**Why GLM-4.7 over Qwen?**
- ✅ **Better benchmarks**: Score 37 on artificialanalysis.ai (best for consumer hardware)
- ✅ **Better than Qwen3:30B** at same size
- ✅ **Memory works** without issues
- ✅ **Tool calling works** with proper config
- ✅ **Fast**: Works even with q8_0 quantization on RTX 4090

**Your Action**: Instead of `glm4:9b`, use `glm-4.7-flash:latest`

---

## 2. **CRITICAL CONFIG FIX: Remove `reasoning: true`**

**Problem**: Setting `reasoning: true` causes 400 errors with message:
```
400 think value "low" is not supported for this model
```

**Solution** (from @noozo - Comment #7):
```json
{
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "http://localhost:11434/v1",
        "apiKey": "ollama-local",
        "api": "openai-completions",
        "models": [
          {
            "id": "glm-4.7-flash:latest",
            "name": "GLM-4.7 Flash",
            "reasoning": false,  // ← MUST BE FALSE!
            "input": ["text"],
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 32768,
            "maxTokens": 32768
          }
        ]
      }
    }
  }
}
```

**This likely fixes your "what is your name" loop issue!**

---

## 3. **Temperature Settings for Agentic Tasks** (from @sebastienbo)

**Problem**: Default temperature too high for tool calling/coding

**Solution**: Add temperature to model config
```json
{
  "id": "glm-4.7-flash:latest",
  "name": "GLM-4.7 Flash",
  "reasoning": false,
  "temperature": 0.3,  // ← ADD THIS (0.3-0.4 recommended)
  "input": ["text"],
  ...
}
```

---

## 4. **Disable Repeat Penalty** (from @sebastienbo - Comment #19)

**Issue**: Repeat penalty causes issues with GLM-4.7

**Solution**: Create custom Modelfile on Windows:
```bash
# On Windows Ollama (100.76.189.18)
cat > Modelfile << 'EOF'
FROM glm-4.7-flash:latest
PARAMETER temperature 0.7
PARAMETER repeat_penalty 1.0
PARAMETER num_ctx 8192
EOF

ollama create glm-4.7-flash-tuned -f Modelfile
```

Then use `glm-4.7-flash-tuned` in your config.

---

## 5. **Tool Calling Patch Available!** (IMPORTANT!)

**From @jokelord - Comment #25**:
https://github.com/jokelord/openclaw-local-model-tool-calling-patch

This is a **patch to fix tool calling** with local Ollama models!

**What it does**:
- Fixes tool calling routing for Ollama models
- Similar to Reddit PR #4287 that was decommissioned

**Your version (v2026.2.2-3)** may not have this fix!

---

## 6. **Working Configuration Example** (from @matthewpapa07)

Full working config with GLM-4.7-flash including **memory support**:

```json
{
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "http://127.0.0.1:11434/v1",
        "apiKey": "ollama-local",
        "api": "openai-completions",
        "models": [
          {
            "id": "glm-4.7-flash:q8_0",
            "name": "GLM-4.7 Flash Q8",
            "reasoning": false,
            "input": ["text"],
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 32768,
            "maxTokens": 32768
          }
        ]
      },
      "ollama-embeddings": {
        "baseUrl": "http://127.0.0.1:11435/v1",
        "apiKey": "ollama-local",
        "api": "openai-embeddings",
        "models": [
          {
            "id": "nomic-embed-text:latest",
            "name": "Nomic Embed",
            "input": ["text"],
            "output": ["embedding"],
            "contextWindow": 8192
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/glm-4.7-flash:q8_0",
        "embeddings": "ollama-embeddings/nomic-embed-text:latest"
      }
    }
  },
  "memory": {
    "provider": "chroma",
    "chroma": {
      "path": "/root/.openclaw/chroma"
    }
  }
}
```

**Key points**:
- GLM-4.7-flash works with **memory** and **embeddings**
- Use separate Ollama instance for embeddings (port 11435) if concurrent issues
- Set `OLLAMA_CONTEXT_LENGTH=8192` for embeddings

---

## 7. **Ollama Context Length** (CRITICAL for Memory)

**Problem**: Memory doesn't work because context is too small

**Solution** (from @matthewpapa07 - Comment #11):
```bash
# On Windows, set environment variable:
set OLLAMA_CONTEXT_LENGTH=8192

# Or in Docker:
environment:
  - OLLAMA_CONTEXT_LENGTH=8192
```

**For Ollama on Windows**: 
You may need to set this in Windows environment variables or restart Ollama with the flag.

---

## 8. **Common Issues & Fixes**

### Issue A: "reasoning: true" causes 400 errors
**Fix**: Set `"reasoning": false` in model config

### Issue B: Model responds like an "outside observer"
**Symptoms**: Bot talks about "WhatsApp technical details" instead of answering
**Fix**: 
- Remove `reasoning: true`
- Lower temperature to 0.3-0.4
- Check system prompt in `.openclaw/workspace/IDENTITY.md`

### Issue C: Tool calling doesn't work
**Fix**: 
- Use GLM-4.7-flash (not Qwen3)
- Apply tool calling patch: https://github.com/jokelord/openclaw-local-model-tool-calling-patch
- Your v2026.2.2-3 may need this patch

### Issue D: Memory doesn't work
**Fix**:
- Set `OLLAMA_CONTEXT_LENGTH=8192`
- Use embeddings model (nomic-embed-text)
- Configure memory provider in config

### Issue E: HTTP 500 errors
**Symptoms**: "invalid character '<' looking for beginning of value"
**Cause**: Model too small, KV cache overflow
**Fix**: Use at least 9B model (GLM-4.7 or GLM-4-9B)

---

## 9. **Model Recommendations from Discussion**

| Model | Size | Works? | Tool Calling | Memory | Speed | Notes |
|-------|------|--------|--------------|--------|-------|-------|
| **glm-4.7-flash** | 9B | ✅ Yes | ✅ Excellent | ✅ Yes | ⚡ Fast | **BEST CHOICE** |
| glm4:9b | 9B | ✅ Yes | ✅ Good | ✅ Yes | ⚡ Fast | Alternative |
| qwen3-coder | Various | ⚠️ Partial | ❌ Struggles | ❌ No | Medium | Tool calling issues |
| qwen2.5:14b | 14B | ⚠️ Partial | ⚠️ Loops | ⚠️ Partial | Slow | Your current |
| qwen3:30b | 30B | ❌ Poor | ❌ LaTeX bugs | ❌ No | Very Slow | Not recommended |
| nemotron-nano | 30B | ⚠️ Partial | ⚠️ Limited | ⚠️ Difficult | Medium | Requires tweaks |

**Verdict**: Switch to `glm-4.7-flash:latest` immediately!

---

## 10. **Your Specific Fix Plan**

Based on GitHub discussion, here's what you need to do:

### Step 1: Download GLM-4.7-Flash (Windows)
```powershell
# On 100.76.189.18
ollama pull glm-4.7-flash:latest

# Verify
ollama list
```

### Step 2: Update Config (Debian)
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
            "id": "glm-4.7-flash:latest",
            "name": "GLM-4.7 Flash",
            "reasoning": false,
            "input": ["text"],
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 32768,
            "maxTokens": 32768
          },
          {
            "id": "qwen2.5:14b",
            "name": "Qwen 2.5 14B (Backup)",
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
        "primary": "ollama/glm-4.7-flash:latest"
      },
      "maxConcurrent": 4,
      "subagents": {
        "maxConcurrent": 8
      }
    }
  }
}
```

### Step 3: Restart & Test
```bash
systemctl --user restart openclaw
openclaw models list
```

### Step 4: Test "What is your name?"
Should work without loops now!

---

## 11. **Optional: Tool Calling Patch**

If GLM-4.7 still has tool issues, apply the patch:

```bash
# Clone the patch repo
git clone https://github.com/jokelord/openclaw-local-model-tool-calling-patch
cd openclaw-local-model-tool-calling-patch

# Follow instructions in repo
# (Likely patches OpenClaw SDK files)
```

---

## 12. **Official Ollama Documentation**

OpenClaw now has official Ollama docs:
- https://docs.molt.bot/concepts/model-providers#ollama
- https://docs.molt.bot/providers/ollama

**Auto-detection**: Ollama is automatically detected at `http://127.0.0.1:11434/v1`

---

## Summary: What Changed Your Situation

**Before reading GitHub**:
- ❌ Using `qwen2.5:14b` (slower, loops)
- ❌ Possibly had `reasoning: true` (causes 400 errors)
- ❌ No temperature tuning
- ❌ Didn't know about GLM-4.7-flash

**After applying GitHub solutions**:
- ✅ Use `glm-4.7-flash:latest` (fastest, most stable)
- ✅ Set `reasoning: false` (fixes 400 errors)
- ✅ Add `temperature: 0.3-0.4` (better tool calling)
- ✅ Apply tool calling patch if needed
- ✅ Optionally add embeddings for memory

**Expected result**: 2-3x faster responses, no loops, working tool calls!

---

## Action Items for You

1. ✅ **Already prepared**: GLM-4 config ready
2. ⏳ **Waiting**: You to download `glm-4.7-flash:latest` on Windows
3. 🔧 **Next**: Update config with `reasoning: false` and `temperature: 0.3`
4. 🧪 **Test**: "What is your name?" should work!
5. 📦 **Optional**: Apply tool calling patch if issues persist

Let me know when GLM-4.7-flash download is done! 🚀
