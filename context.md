# User Environment & Context

## System Information
- **OS**: Debian Linux
- **Primary Server**: 100.108.37.20 (Tailscale)
- **Windows PC**: 100.76.189.18 (Ollama Server)
- **LLM Backend**: Ollama with Qwen 2.5 14B and Qwen 2.5 Coder 14B

## Setup Details
- **OpenClaw Gateway**: Running as systemd service on port 18789
- **Node.js**: v24.13.0 (via nvm)
- **Python**: 3.11.2
- **Docker**: v20.10.24

## Networking
- Using Tailscale for secure networking between devices
- OpenClaw on Debian connects to Ollama on Windows via Tailscale
- Gateway accessible at: http://localhost:18789

## Preferences
- Prefers local LLMs (no external API dependencies)
- Values security (Tailscale network isolation)
- Wants systemd service management for reliability
- Appreciates technical precision and direct communication

## Current Projects
- OpenClaw setup with remote Ollama backend
- Development environment on Debian server

## Notes
- User has coding knowledge and technical background
- Comfortable with command line and system administration
- Values organization and clean setup
