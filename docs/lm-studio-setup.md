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
3. Find `phi-4-mini-instruct` (or anything you want)
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

That's all. Move to OpenClaw when ready.
