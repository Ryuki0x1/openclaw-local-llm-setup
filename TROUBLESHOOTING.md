# OpenClaw Troubleshooting Guide

## Setup Overview
- **OpenClaw**: Running on Debian (100.108.37.20)
- **Ollama**: Running on Windows (100.76.189.18:11434)
- **Network**: Connected via Tailscale
- **Version**: OpenClaw 2026.2.2-3

## Common Issues & Solutions

### 1. Model Not Found Error
**Problem**: `ollama/mistral:latest` showing as "missing" in model list

**Solution**: 
```bash
# Edit ~/.openclaw/openclaw.json and change primary model
"primary": "ollama/qwen2.5:14b"  # Use a model that exists on your Ollama server
```

**Available Models**:
- `ollama/qwen2.5:14b` - General purpose (14.8B parameters)
- `ollama/qwen2.5-coder:14b` - Coding focused (14.8B parameters)
- `ollama/mistral:latest` - General purpose (7.2B parameters)
- `ollama/llama3.1:8b` - General purpose (8B parameters)

### 2. OAuth Directory Missing
**Problem**: `CRITICAL: OAuth dir missing (~/.openclaw/credentials)`

**Solution**:
```bash
mkdir -p ~/.openclaw/credentials
systemctl --user restart openclaw
```

### 3. Directory Permissions Too Open
**Problem**: Security warning about `.openclaw` permissions

**Solution**:
```bash
chmod 700 ~/.openclaw
```

### 4. Testing Ollama Connection

**Check if Ollama is accessible**:
```bash
curl http://100.76.189.18:11434/v1/models
```

**Test chat completion**:
```bash
curl -s http://100.76.189.18:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model":"qwen2.5:14b",
    "messages":[{"role":"user","content":"test"}],
    "max_tokens":10
  }'
```

### 5. Check OpenClaw Status

**View service status**:
```bash
systemctl --user status openclaw
```

**View logs**:
```bash
journalctl --user -u openclaw -f
```

**Run diagnostics**:
```bash
openclaw doctor
```

**List configured models**:
```bash
openclaw models list
```

### 6. Restart OpenClaw Service
```bash
systemctl --user restart openclaw
```

## Configuration Files

### Main Config: `~/.openclaw/openclaw.json`
Key sections:
- `models.providers.ollama.baseUrl` - Ollama server URL
- `agents.defaults.model.primary` - Default model to use
- `gateway.port` - Gateway port (default: 18789)
- `gateway.auth.token` - Authentication token

### Model Config: `~/.openclaw/agents/main/agent/models.json`
Lists available models with their configurations.

## Network Troubleshooting

**Verify Tailscale connectivity**:
```bash
ping 100.76.189.18
```

**Check Ollama is listening**:
```bash
curl -v http://100.76.189.18:11434/api/version
```

## Reddit Issues & Solutions (r/LocalLLaMA)

### Issue 1: Tool Calling Loops 🔄
**Symptoms**: Model repeatedly calls the same tool or gets stuck in infinite loops

**Root Causes** (from Reddit thread):
1. **PR #4287 Missing** - OpenClaw SDK doesn't pass tool definitions from third-party providers
   - Status: PR was decommissioned by bot, needs manual merge
   - Version: Your v2026.2.2-3 may or may not have this fix
   
2. **Qwen Model Selection Issues**:
   - `qwen3:8b` - Too small, loses context with 40+ tool schemas
   - `qwen3:14b` - Better but still struggles with tool result interpretation
   - `qwen3:30b` - Outputs LaTeX, breaks result interpretation
   - `qwen2.5:14b` - **You're using this** - More stable than Qwen3

3. **Context Window Overload** - Too many tools in system prompt overwhelms smaller models

**Solutions**:

**A. For Qwen2.5:14b (Your Current Model)**:
```json
// Add to ~/.openclaw/openclaw.json under agents.defaults
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/qwen2.5:14b"
      },
      "maxToolRounds": 5,           // Limit tool call loops (ADD THIS)
      "toolCallTimeout": 30000,      // 30s timeout per tool (ADD THIS)
      "maxConcurrent": 4,
      "subagents": {
        "maxConcurrent": 8
      }
    }
  }
}
```

**B. Increase Ollama Context Window** (on Windows):
```bash
# On your Windows machine (100.76.189.18)
# Edit Ollama service or set environment variable:
set OLLAMA_NUM_CTX=8192

# Or create/edit Modelfile:
ollama create qwen2.5:14b-extended -f Modelfile
# Where Modelfile contains:
FROM qwen2.5:14b
PARAMETER num_ctx 8192
PARAMETER temperature 0.7
PARAMETER top_p 0.9
```

**C. Try GLM-4-9B-Chat** (Reddit user MarkVL's final recommendation):
```bash
# On Windows Ollama:
ollama pull glm4:9b

# Then update ~/.openclaw/openclaw.json:
{
  "models": {
    "providers": {
      "ollama": {
        "models": [
          {
            "id": "glm4:9b",
            "name": "GLM-4 9B",
            "reasoning": false,
            "input": ["text"],
            "contextWindow": 8192,
            "maxTokens": 4096
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/glm4:9b"
      }
    }
  }
}
```
**Why GLM-4?** Reddit reports it's **fastest** and most stable for tool calling with local models.

**D. Fallback to Claude Haiku** (if local models fail):
Multiple Reddit users confirm Claude Haiku works perfectly but costs money:
```bash
# Get OpenRouter API key from https://openrouter.ai/
openclaw models add openrouter/anthropic/claude-3.5-haiku

# Update config:
"primary": "openrouter/anthropic/claude-3.5-haiku"
```

### Issue 2: Empty/Blank Responses
**Symptom**: Model runs for 60+ seconds, produces nothing

**Root Cause**: OpenClaw sends `stream: true` but Ollama returns non-streaming JSON

**Solution**: Already handled in newer OpenClaw versions (your v2026.2.2-3 should be OK)

### Issue 3: Missing ollama-mcp-bridge
**Symptom**: MCP tools not showing up in OpenClaw

**What is it?**: Bridge that converts MCP servers to OpenAI-compatible tool schemas

**Do you need it?**: Only if you want to add external MCP tools beyond OpenClaw's native ones

**Setup** (optional, for advanced users):
```bash
# Install ollama-mcp-bridge
npm install -g ollama-mcp-bridge

# Configure MCP servers in bridge config
# Point OpenClaw to bridge instead of Ollama directly:
"baseUrl": "http://100.76.189.18:11435/v1"  # Bridge port, not Ollama
```

### Issue 4: "What is your name" Loops
**Your Specific Issue**: Getting stuck when asking "what is your name"

**Likely Cause**: 
- Model trying to call tools unnecessarily
- Or model getting confused about identity/context

**Debug Steps**:
```bash
# 1. Check actual logs during the loop
journalctl --user -u openclaw -f

# 2. Test directly with Ollama (bypass OpenClaw):
curl -s http://100.76.189.18:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5:14b",
    "messages": [
      {"role": "system", "content": "You are Mighty Raju, a technical AI assistant."},
      {"role": "user", "content": "What is your name?"}
    ],
    "max_tokens": 50
  }' | jq .

# 3. Check if it's a tool-calling issue or response issue
```

**Immediate Fix**:
```bash
# Add maxToolRounds limit to prevent infinite loops
openclaw config set agents.defaults.maxToolRounds 5

# Or manually edit ~/.openclaw/openclaw.json
```

### Performance Benchmarks (from Reddit)
| Model | Speed (no tools) | Speed (with tools) | Context Quality | Tool Loop Issues |
|-------|------------------|-------------------|-----------------|------------------|
| qwen2.5:7b | ~12s | ~45s | Poor with 40+ tools | High |
| qwen2.5:14b | ~30-60s | ~60-150s | Good | Medium |
| qwen3:14b | ~30s | ~60s | Poor (dumps raw results) | Medium |
| qwen3:30b | ~60s | ~120s | Poor (LaTeX formatting) | High |
| glm4:9b | ~15s | ~30s | **Excellent** | **Low** ✅ |
| Claude Haiku | ~2-5s | ~5-10s | Perfect | None |

### Recommended Action Plan
1. ✅ **Your current setup is working** - Logs show no loops
2. ⚠️ **Add safety limits** - Set `maxToolRounds: 5` to prevent future loops
3. 🔧 **Test GLM-4** - If you encounter issues, GLM-4 is Reddit's top pick
4. 💰 **Claude Haiku backup** - Keep as fallback for critical tasks

## Quick Health Check
```bash
# 1. Check Ollama is responding
curl http://100.76.189.18:11434/v1/models

# 2. Check OpenClaw service
systemctl --user status openclaw

# 3. Run diagnostics
openclaw doctor

# 4. Test model configuration
openclaw models list
```

## Last Updated
2026-02-04 - Initial setup and common fixes documented
