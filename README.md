# kimi-cu-pi

Kimi Computer Use MCP server config for [pi](https://github.com/earendil-works/pi-mono) (the coding-agent harness).

## Prerequisite

KimiCU.app installed at `/Applications/KimiCU.app`.

## Setup

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

With pi's MCP adapter flags:

```json
"kimi-cu": {
  "command": "/Applications/KimiCU.app/Contents/MacOS/kimi-cu",
  "args": ["mcp", "-s", "user"],
  "lifecycle": "lazy",
  "directTools": false
}
```

Restart pi (or start a new session), then check `mcp` server status — `kimi-cu` should be listed.

## Notes

- `lifecycle: "lazy"` connects on first use instead of at startup.
- `directTools: false` routes tools through the gateway instead of flattening them into the tool list.
