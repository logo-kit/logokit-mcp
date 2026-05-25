# Publishing to the official MCP Registry

Publishes [`server.json`](./server.json) to https://registry.modelcontextprotocol.io
under the `com.logokit/*` namespace. Run these from this repo's root.

## 1. Install the publisher CLI

```bash
brew install mcp-publisher
# or download the binary: https://github.com/modelcontextprotocol/registry/releases/latest
mcp-publisher --help
```

## 2. Validate

```bash
mcp-publisher validate
```

## 3. Authenticate by domain (DNS TXT)

The server name is `com.logokit/brand-data`, so you must prove ownership of
`logokit.com`. (GitHub auth would force an `io.github.*` name instead.)

```bash
MY_DOMAIN="logokit.com"
openssl genpkey -algorithm Ed25519 -out key.pem
PUBLIC_KEY="$(openssl pkey -in key.pem -pubout -outform DER | tail -c 32 | base64)"
echo "${MY_DOMAIN}. IN TXT \"v=MCPv1; k=ed25519; p=${PUBLIC_KEY}\""
```

Add the printed value as a **TXT** record on `logokit.com` in Cloudflare. Once it
propagates:

```bash
PRIVATE_KEY="$(openssl pkey -in key.pem -noout -text | grep -A3 "priv:" | tail -n +2 | tr -d ' :\n')"
mcp-publisher login dns --domain "logokit.com" --private-key "${PRIVATE_KEY}"
```

> **Alternative (no DNS change):** host a file at
> `https://logokit.com/.well-known/mcp-registry-auth` and use `mcp-publisher login http`.
> See https://modelcontextprotocol.io/registry/authentication

## 4. Publish

```bash
mcp-publisher publish
curl "https://registry.modelcontextprotocol.io/v0.1/servers?search=com.logokit"
```

## Updating later

Bump `version` in `server.json` whenever the tools or config change, then re-run
`mcp-publisher publish`. Keep `key.pem` private — it is git-ignored.
