# VoiceLab plugin for Grok Build

Connect Grok Build to [VoiceLab](https://voicelab.uz) — speech AI for agents:
text-to-speech, speech-to-text with timing, voice isolation, voice management,
and Aisha LLM completions in Uzbek, Russian, and English.

- **Website:** https://voicelab.uz
- **Docs:** https://docs.voicelab.uz
- **MCP endpoint:** https://mcp.voicelab.uz/mcp
- **Server source:** https://github.com/voicelab-uz/mcp

## What this plugin ships

- **1 MCP server** — the hosted VoiceLab endpoint at `https://mcp.voicelab.uz/mcp`
  (streamable HTTP), exposing TTS, STT, voice isolation, voices, and LLM tools.

## Installation

In Grok Build, open `/plugin`, search for **VoiceLab**, and install.

Before the first MCP call, set the gateway bearer token in your environment
(Grok expands `${VAR}` in `.mcp.json` headers):

```bash
export VOICELAB_MCP_AUTH_TOKEN='your-mcp-gateway-token'
```

Or add the server manually:

```bash
grok mcp add --transport http voicelab https://mcp.voicelab.uz/mcp \
  --header "Authorization: Bearer ${VOICELAB_MCP_AUTH_TOKEN}"
```

## Authentication

The live hosted MCP **requires** an HTTP Bearer token on every `/mcp` request:

```http
Authorization: Bearer <VOICELAB_MCP_AUTH_TOKEN>
```

| Credential | Who uses it | What to paste in Grok |
|---|---|---|
| **MCP gateway token** (`MCP_AUTH_TOKEN` / `VOICELAB_MCP_AUTH_TOKEN`) | Grok client → `mcp.voicelab.uz` | **Yes — paste this** as the Bearer value / env var |
| **VoiceLab API key** (`vlk_…`) | MCP server → `api.voicelab.uz` | **No — server-side only**; do not paste into Grok |

Obtain the gateway token from the VoiceLab MCP operator. Public discovery
endpoints (`/health`, `/v1`, `/.well-known/mcp.json`) do not require auth.

The bundled [`.mcp.json`](.mcp.json) declares:

```json
{
  "mcpServers": {
    "voicelab": {
      "type": "http",
      "url": "https://mcp.voicelab.uz/mcp",
      "headers": {
        "Authorization": "Bearer ${VOICELAB_MCP_AUTH_TOKEN}"
      }
    }
  }
}
```

Grok expands `${VOICELAB_MCP_AUTH_TOKEN}` at load time — keep the secret in your
environment, never commit it.

## Network & credentials (for review)

- **Endpoints called:** `https://mcp.voicelab.uz/mcp` (hosted MCP). Discovery
  only: `https://mcp.voicelab.uz/health`, `/v1`, `/.well-known/mcp.json`.
  Downstream VoiceLab API calls (`api.voicelab.uz`) are made by the hosted
  server using its own server-side API key — not by this plugin or the Grok client.
- **Credential:** user-supplied `VOICELAB_MCP_AUTH_TOKEN`, sent only as
  `Authorization: Bearer` to `mcp.voicelab.uz`. Nothing is read from the
  filesystem; no telemetry is collected by this plugin.

## License

MIT — see [LICENSE](LICENSE).

Author: Elzodxon Sharofaddinov \<elzodxon@gmail.com\>
