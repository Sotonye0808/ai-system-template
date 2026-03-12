# AI-Assisted Development Environment

## Overview
This repository contains a complete setup for a free, powerful AI-assisted development workflow in VS Code, featuring:

- **Copilot-style autocomplete** (Supermaven)
- **AI chat + code editing** (Continue)
- **Autonomous coding agent** (Cline)
- **Repository intelligence** (Sourcegraph Amp)
- **Offline fallback** (Ollama local models)
- **Structured project documentation** (.ai-system)

## Key Features

### Core Components
| Component | Purpose |
|-----------|---------|
| Supermaven | Always-on inline code completion |
| Continue | AI coding assistant for planning and reasoning |
| Cline | Autonomous agent for multi-file changes |
| Sourcegraph Amp | Intelligent code search and navigation |
| codebase-mcp | Provides structured repository context to AI tools |
| Ollama | Local model fallback for offline work |

### Model Configuration
The system uses a tiered approach to AI models:
1. **Primary (Cloud):** Qwen3 Coder and DeepSeek via Together AI
2. **Fast Chat:** Llama 3 70B via Groq
3. **Offline Fallback:** Local models via Ollama (Qwen, DeepSeek, CodeLlama)

## Getting Started

### Installation
1. Install VS Code extensions:
   - Supermaven
   - Continue
   - Cline
   - Sourcegraph Amp
2. Set up free API keys:
   - [Together AI](https://api.together.xyz)
   - [Groq](https://console.groq.com)
3. Install Ollama and download models:
   ```bash
   ollama pull qwen2.5-coder:7b
   ollama pull deepseek-coder:6.7b
   ollama pull codellama:7b
   ```

### Configuration
1. Update `~/.continue/config.yaml` with your API keys
2. Configure Cline to use OpenRouter/Qwen3 Coder
3. Set up codebase-mcp for repository context

## Workflow

### Daily Usage
1. Start services:
   ```bash
   ollama serve
   codebase-mcp start
   ```
2. Run development cycle:
   ```
   Execute command: .ai-system/commands/dev-cycle.md
   ```

### Key Commands
| Command | Purpose |
|---------|---------|
| `bootstrap-project.md` | Initialize .ai-system documentation |
| `dev-cycle.md` | Full development workflow |
| `plan-feature.md` | Plan new features |
| `refactor-codebase.md` | Improve code structure |
| `fix-build.md` | Diagnose and fix errors |

## Project Structure
The `.ai-system` directory maintains all AI-related documentation:

```
.ai-system/
├── agents/               # Core agent instructions and protocols
├── planning/             # Project plans and task queues
├── commands/             # Reusable AI commands
├── checkpoints/          # Session logs and progress tracking
├── memory/               # Project decisions and lessons learned
├── index/                # Repository maps and file summaries
└── testing/              # Test results and repair systems
```

## Troubleshooting
Common issues and solutions:

1. **API Connection Problems:**
   - Verify API keys in config.yaml
   - Check service status pages

2. **Performance Issues:**
   - Disable VS Code minimap
   - Close unused browser tabs during intensive tasks

3. **Offline Work:**
   - Ensure Ollama models are downloaded
   - Configure Continue to use local fallback models

## License
This setup uses free tiers of various services. Refer to each component's licensing:
- Supermaven: Free for basic usage
- Together AI: Free monthly credits
- Groq: Free tier with rate limits
- Ollama: MIT License

For complete documentation, see:
- [AI Workflow Setup Guide](ai-workflow-setup-guide.md)
- [Personal Reference Guide](my-ai-workflow-reference.md)