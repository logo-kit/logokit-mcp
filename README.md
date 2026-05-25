# LogoKit MCP Server

**Brand & company data for AI agents — by domain or stock ticker.**

The LogoKit MCP server connects Claude, Cursor, ChatGPT, and any MCP-compatible client to LogoKit's company database. Resolve any company by website domain or by security identifier and get back a structured record: industry classification, employee range, founding year, headquarters, social profiles, brand colors, and logos.

- **Endpoint:** `https://mcp.logokit.com/mcp`
- **Transport:** MCP Streamable HTTP
- **Auth:** `Authorization: Bearer sk_pa_…` (secret API key, paid plans)
- **Landing page:** https://logokit.com/mcp
- **Docs:** https://docs.logokit.com/mcp/overview

## Tools

| Tool | Argument | Returns |
|------|----------|---------|
| `get_brand_by_domain` | a website domain — e.g. `stripe.com` | full Brand Data record |
| `get_brand_by_ticker` | a ticker / ISIN / CUSIP / SEDOL / WKN — e.g. `AAPL`, `US0378331005` | the same record |

Only populated attributes are returned; empty fields are omitted from the response.

## Authentication

Pass your LogoKit **secret** key as a Bearer token:

```
Authorization: Bearer sk_pa_YOUR_TOKEN
```

Secret keys (`sk_pa_…`) are available on the Startup, Scale, and Enterprise plans. Publishable keys (`pk_*`) are rejected, because MCP traffic is run by your agent rather than an end user's browser. Get a key at https://logokit.com/account/api-tokens. MCP requests count against your Brand Data API quota.

## Install

### Claude Code

```bash
claude mcp add --transport http logokit \
  https://mcp.logokit.com/mcp \
  --header "Authorization: Bearer sk_pa_YOUR_TOKEN"
```

### Cursor, VS Code, Windsurf, and other HTTP clients

```json
{
  "mcpServers": {
    "logokit": {
      "url": "https://mcp.logokit.com/mcp",
      "headers": { "Authorization": "Bearer sk_pa_YOUR_TOKEN" }
    }
  }
}
```

### Claude Desktop

Claude Desktop's config only loads **stdio** servers, so bridge via `mcp-remote` (requires Node.js):

```json
{
  "mcpServers": {
    "logokit": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp.logokit.com/mcp",
        "--header",
        "Authorization: Bearer sk_pa_YOUR_TOKEN"
      ]
    }
  }
}
```

### ChatGPT

Settings → Connectors → Advanced → enable Developer mode → **Add custom connector**:

- **MCP server URL:** `https://mcp.logokit.com/mcp`
- **Authentication:** Custom header → name `Authorization`, value `Bearer sk_pa_YOUR_TOKEN`

Connectors require a paid ChatGPT plan (Plus, Pro, Team, Business, Enterprise, or Edu).

## Official MCP Registry

This repository holds the [`server.json`](./server.json) used to publish the server to the [official MCP Registry](https://registry.modelcontextprotocol.io) as `com.logokit/brand-data`. Publishing steps are in [PUBLISHING.md](./PUBLISHING.md).

## License

[MIT](./LICENSE)
