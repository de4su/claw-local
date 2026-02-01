# LM Studio Setup

LM Studio is a desktop app that downloads and runs AI models on your computer. It's basically a UI for managing models locally and serving them as an API that other apps can use.

## Install

### Windows

Easiest: https://lmstudio.ai/download - download the `.exe` and run it.

Command line:
```powershell
Invoke-WebRequest -Uri "https://lmstudio.ai/download/windows" -OutFile "lm-studio.exe"
Start-Process "lm-studio.exe"
```

### Linux

```bash
curl -L -o lm-studio.AppImage "https://github.com/lmstudio-ai/lmstudio/releases/download/0.3.3/LM_Studio-0.3.3-x64.AppImage"
chmod +x lm-studio.AppImage
./lm-studio.AppImage
```

### macOS

```bash
curl -L -o lm-studio.dmg "https://lmstudio.ai/download/macos"
open lm-studio.dmg
```

Drag to Applications.

## First Time Setup

1. Open LM Studio
2. Go to "Discover" tab
3. Find `phi-4-mini-instruct` (good starter model, ~3.8GB)
4. Click download and wait
5. Go to "Server" tab
6. Pick the model from dropdown
7. Click "Start Server"
8. Wait for "Running on port 1234"

That's it. The API is up.

## Test It

```bash
curl http://127.0.0.1:1234/v1/models
```

Should return JSON with your model.

## Models to Pick

- **Phi 4 Mini** - Small, fast, basic tasks
- **Mistral** - Bigger, smarter, slower
- **Llama 2** - Solid all-rounder

## Keep It Running

Leave the window open. That's it. Close it = API stops.

## Problems

**Won't start**
- Restart LM Studio
- Did you download a model first?
- Your computer needs 8GB+ RAM

**Download is slow**
- Models are 3-13GB. Just let it run.

**"Out of memory"**
- Your hardware can't run this model
- Try a smaller one
- Adjust settings in the Server tab

**OpenClaw can't reach it**
- Is Server tab showing "Running"?
- Check port (default 1234)
- Try the curl command above

That's all. Move to OpenClaw when ready.