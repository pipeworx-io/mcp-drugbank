# mcp-drugbank

DrugBank MCP — wraps the DrugBank Clinical API (api.drugbank.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1663+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `drugbank_ddi` | Check drug-drug interactions among a set of drugs. Returns curated interactions with severity, description, and management guidance. Accepts drug names or DrugBank IDs (comma-separated). Example: drugbank_ddi({ drugs: "warfarin, acetaminophen", _apiKey: "your-key" }) or drugbank_ddi({ drugs: "DB00682,DB00316", _apiKey: "your-key" }) |
| `drugbank_drug_names` | Look up a drug by name or product. Resolves a query string to matching DrugBank drugs/products and their DrugBank IDs. Example: drugbank_drug_names({ query: "lipitor", _apiKey: "your-key" }) |
| `drugbank_drug` | Structured monograph (indication, MoA, dosing) for a drug. Returns indication, mechanism of action, dosages, and a summary of adverse effects for a single DrugBank drug. Example: drugbank_drug({ id: "DB00682", _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "drugbank": {
      "url": "https://gateway.pipeworx.io/drugbank/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/drugbank/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1663+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/drugbank_ddi`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "drugbank": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-drugbank"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-drugbank
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Drugbank data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
