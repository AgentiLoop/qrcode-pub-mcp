# qrcode-pub-mcp

MCP server for [QRCode.Pub](https://qrcode.pub/?ref=github-mcp) tools, runnable with one command and no API key:

| Tool | What it does | Price |
|---|---|---|
| `qr_code` | QR code image (PNG or SVG) for any text or link — returned inline plus a stable image URL | free |
| `page_to_markdown` | Fetch a public web page → title, description, clean Markdown, links (deterministic, no model call) | $0.01 USDC via x402 |
| `page_metadata` | Page metadata: title, description, canonical, language, OpenGraph/Twitter cards, icons, JSON-LD | $0.005 USDC via x402 |
| `host_file` | Upload a file (≤ 5 MB: png, jpeg, webp, gif, pdf, json, txt, md, csv) → public URL `https://qrcode.pub/f/<id>` for 30 days | $0.01 USDC via x402 |

This package is a dependency-free **stdio bridge** (Node 18+, ~60 lines) to the hosted Streamable-HTTP server at `https://qrcode.pub/mcp`. Clients that speak Streamable HTTP natively can skip the bridge and use that URL directly.

## Install

```bash
npx -y github:AgentiLoop/qrcode-pub-mcp        # runs the stdio bridge
```

### Claude Code

```bash
claude mcp add qrcode-pub -- npx -y github:AgentiLoop/qrcode-pub-mcp
# or, no bridge:
claude mcp add --transport http qrcode-pub https://qrcode.pub/mcp
```

### Claude Desktop / Cursor / Windsurf / Cline (stdio)

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

### Cursor / VS Code / any Streamable-HTTP client (no bridge)

```json
{
  "mcpServers": {
    "qrcode-pub": { "type": "http", "url": "https://qrcode.pub/mcp" }
  }
}
```

### Verify

```bash
echo '{"jsonrpc":"2.0","id":1,"method":"tools/list"}' | npx -y github:AgentiLoop/qrcode-pub-mcp
```

## Paid tools and x402

The three paid tools cost cents, paid in USDC with the open [x402](https://x402.org) protocol (v2, scheme `exact`) on **Base** (`eip155:8453`) or **Solana** mainnet. No account, no key, no subscription:

1. Call the tool without `payment` → the result is `isError: true` with `structuredContent.paymentRequired` (the full x402 `accepts` list: network, asset, amount, `payTo`).
2. Sign one entry with any x402 client (e.g. `@x402/fetch`, `x402-axios`, or the agent's own wallet) and call the tool again with the base64 payment payload as `payment`.
3. The server verifies and settles through the facilitator and returns the result; a 4xx answer is never charged.

The same endpoints are plain HTTP for agents that prefer `fetch` + x402 middleware: `GET /x402/extract?url=…`, `GET /x402/meta?url=…`, `POST /x402/store`. Catalog: `https://qrcode.pub/x402`, OpenAPI: `https://qrcode.pub/openapi.json`.

## Environment

| Variable | Default | Purpose |
|---|---|---|
| `QRCODE_PUB_MCP_URL` | `https://qrcode.pub/mcp` | remote endpoint |
| `QRCODE_PUB_MCP_TIMEOUT_MS` | `60000` | per-request timeout |

## Links

- Docs: https://qrcode.pub/qr-code-api?ref=github-mcp#mcp
- Official MCP registry entry: `pub.qrcode/tools`
- Free QR image API (no MCP needed): `https://qrcode.pub/api/qr?data=https://example.com&size=300`
- Agent skills for the same tools: https://github.com/AgentiLoop/skills

## License

MIT — the bridge is open source; the hosted service terms are at https://qrcode.pub/.
