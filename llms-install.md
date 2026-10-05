# Installing qrcode-pub-mcp (instructions for AI agents)

This MCP server needs **no API key, no account and no build step**. It is a stdio bridge to the hosted server `https://qrcode.pub/mcp`. Requirements: Node.js 18 or newer and network access to qrcode.pub.

## Preferred: stdio via npx

Add this server to the client's MCP configuration:

```json
{
  "mcpServers": {
    "qrcode-pub": {
      "command": "npx",
      "args": ["-y", "github:AgentiLoop/qrcode-pub-mcp"]
    }
  }
}
```

Config file locations:
- Cline: the `cline_mcp_settings.json` opened from the MCP Servers panel → Configure.
- Claude Desktop: `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows).
- Cursor: `~/.cursor/mcp.json` or `.cursor/mcp.json` in the project.
- Claude Code: run `claude mcp add qrcode-pub -- npx -y github:AgentiLoop/qrcode-pub-mcp`.

## Alternative: direct Streamable HTTP (no local process)

If the client supports remote MCP servers, use `{"type": "http", "url": "https://qrcode.pub/mcp"}` instead. No authentication header is needed.

## Verify

```bash
npx -y @modelcontextprotocol/inspector --cli npx -y github:AgentiLoop/qrcode-pub-mcp --method tools/list
```

Expected: four tools — `qr_code`, `page_to_markdown`, `page_metadata`, `host_file`.

Quick functional test: call `qr_code` with `{"data": "https://example.com"}` — the response contains an `image/png` content block and a text block with a stable image URL.

## Notes

- `qr_code` is free. `page_to_markdown`, `page_metadata` and `host_file` are paid per call with USDC through the x402 protocol; without a `payment` argument they return the price and payment requirements (`isError: true`, `structuredContent.paymentRequired`) instead of the result. Nothing is charged unless the caller signs a payment.
- Environment variables (optional): `QRCODE_PUB_MCP_URL` (default `https://qrcode.pub/mcp`), `QRCODE_PUB_MCP_TIMEOUT_MS` (default `60000`).
- No files are written locally; the bridge keeps no state.
