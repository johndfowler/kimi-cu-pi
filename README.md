# kimi-cu-pi

Connect [Kimi Computer Use](https://www.kimi.com) (KimiCU) to AI coding agents — [pi](https://github.com/earendil-works/pi-mono) and [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) — as an MCP server.

KimiCU exposes your macOS apps to the agent: screenshots, accessibility trees, clicks, typing, key chords, scrolling, drag — real computer use.

## Prerequisite

- [KimiCU.app](https://www.kimi.com) installed at `/Applications/KimiCU.app`
- The binary at `/Applications/KimiCU.app/Contents/MacOS/kimi-cu`

## pi (mcp.json)

Add to `~/.pi/agent/mcp.json` under `mcpServers`:

```json
{
  "mcpServers": {
    "kimi-cu": {
      "type": "stdio",
      "command": "/Applications/KimiCU.app/Contents/MacOS/kimi-cu",
      "args": ["mcp", "-s", "user"]
    }
  }
}
```

With pi's MCP adapter flags (recommended):

```json
"kimi-cu": {
  "command": "/Applications/KimiCU.app/Contents/MacOS/kimi-cu",
  "args": ["mcp", "-s", "user"],
  "lifecycle": "lazy",
  "directTools": false
}
```

Restart pi (or start a new session), then check `mcp` server status — `kimi-cu` should be listed.

- `lifecycle: "lazy"` connects on first use instead of at startup.
- `directTools: false` routes tools through the gateway instead of flattening them into the tool list.

## DeepSeek Harness (cordis.patch.yml)

Add to `$DSH_HOME/profiles/web/cordis.patch.yml` inside the top-level `insert` list:

```yaml
- id: mcp-kimi-cu
  name: '@deepseek-ai/dsh-mcp-client'
  config:
    serverName: kimi-cu
    transport: stdio
    command: /Applications/KimiCU.app/Contents/MacOS/kimi-cu
    args: ['mcp', '-s', 'user']
```

MCP entries hot-reload — the tools appear without restarting `dsh web`.

## Tools

| Tool | What it does |
| --- | --- |
| `list_apps` | Running apps (name, bundle_id, pid) |
| `get_app_state` | Screenshot and/or accessibility tree for an app |
| `click` | Click by snapshot index or screenshot-pixel coordinates |
| `type_text` | Type Unicode text into an app |
| `press_key` | Key or chord (xdotool-style DSL: `"return"`, `"cmd+a"`, …) |
| `scroll` | Scroll at a snapshot index or pixel point |
| `set_value` | Set an element's value directly via accessibility |
| `perform_secondary_action` | Secondary AX action (default `AXShowMenu`) |
| `select_text` | Select text in a text element |
| `drag` | Drag between two screenshot-pixel points |

## ⚠️ Security

This gives the agent **control of your Mac's UI** — it can click, type, and read any app you point it at. Scope sessions with `-s user` only if that's intentional, and never point an untrusted agent at it.

## License

MIT
