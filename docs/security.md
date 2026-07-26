# Security Guide

OpenClaw runs with high system privileges — shell execution, filesystem access, and credential access. Take security seriously.

## Known CVEs (2026)

| CVE | Severity | Description | Fixed In |
|---|---|---|---|
| CVE-2026-25253 | Critical | Remote code execution via malicious link | v2026.1.29 |
| CVE-2026-32922 | Critical | Privilege escalation to admin RCE | v2026.3.x |
| CVE-2026-33579 | Critical (9.4) | Missing validation in `/pair approve` | v2026.3.x |
| CVE-2026-44112 | Critical (9.6) | TOCTOU filesystem write escape | v2026.5.x |
| CVE-2026-44115 | High (8.8) | Environment variable disclosure (API keys) | v2026.5.x |
| CVE-2026-44118 | High (7.8) | MCP loopback privilege escalation | v2026.5.x |

**Always keep OpenClaw updated.** Run `openclaw update` regularly.

## Hardening Checklist

### 1. Run the built-in audit

```bash
openclaw security audit
openclaw security audit --fix
```

### 2. Bind to localhost only

In `~/.openclaw/openclaw.json`, make sure the gateway binds to `127.0.0.1`, not `0.0.0.0`.

For remote access, use Tailscale or SSH tunnels.

### 3. Use strong auth tokens

Set a strong, unique password in `gateway.auth.token`. Don't use `pick-a-password`.

### 4. Restrict commands (allowlist)

In your `TOOLS.md` or agent config, use `security: "allowlist"` and define exactly which command binaries are allowed. Don't grant arbitrary shell access.

### 5. Scope filesystem access

Restrict file access to specific directories in your agent config. Don't give the agent access to `~/.ssh`, `~/.gnupg`, or similar.

### 6. Run in a container

The safest setup is running OpenClaw inside Docker or a VM:
```bash
git clone https://github.com/openclaw/openclaw
cd openclaw
./scripts/docker/setup.sh
```

### 7. Use a dedicated user account

Run the gateway under a restricted, non-root service user.

### 8. Messaging channel access control

Always use `dmPolicy: "allowlist"` and restrict `allowFrom` to your own accounts:
```json
{
  "channels": {
    "whatsapp": {
      "enabled": true,
      "dmPolicy": "allowlist",
      "allowFrom": ["+1234567890"]
    }
  }
}
```

### 9. LM Studio server security

- Keep LM Studio on `127.0.0.1` (default)
- Enable auth tokens if exposing on LAN
- Don't enable CORS unless actively testing
- Never expose port 1234 to the internet

### 10. Watch for prompt injection

Treat all incoming messages and web content as untrusted. A malicious prompt from Discord/Telegram/WhatsApp could trick your agent into running dangerous commands if you haven't set up proper allowlists.

## Quick Diagnostic

```bash
openclaw doctor
```

Checks connectivity, config health, and known issues.
