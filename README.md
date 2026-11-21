# @pipeworx/uk-cma-cases

Has the UK Competition and Markets Authority opened a case about this company or
market: a merger inquiry, a market study, a Competition Act investigation, a
consumer-protection enforcement case?

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2024+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `uk_cma_cases_list(query?, case_type?, case_state?, market_sector?, opened_from?, opened_to?, start?, count?)`
  — the CMA's case listing, newest first when no text is given. Each row carries
  the title, case type, open or closed state, market sectors, opened and closed
  dates, outcome code, the CMA's own summary and the case page link. The first five
  rows also carry `latest_update` (date and text of the newest dated entry on the
  case page, e.g. a Phase 1 decision). Returns
  `result` (`rows`, `beyond_last_page`, `no_case_matched`), `total_matches`,
  `individual_cases_excluded` and `data_as_of`.
- `uk_cma_case_read(slug)` — one case page as plain text: statutory timetable,
  decisions, final-report notices, undertakings, `latest_update` and a dated
  `timeline` (newest first), the list of published case
  documents with titles and links, and the page's change history, newest first.

## Auth

Keyless.

## Data sources

- Competition and Markets Authority, case register. Read from the
  publisher on request. `data_as_of` is the day of the read; each row carries the
  page's own `last_updated`.

## Behaviour worth knowing

- Firms and markets only. The CMA also lists criminal-cartel cases and
  competition-disqualification cases, which concern individuals. Those case types
  are excluded in the query and the number left out for the same filters is
  returned as `individual_cases_excluded`; asking for one of them is refused.
- E-mail addresses and phone numbers in case text are removed and counted in
  `redactions`.
- The outcome code is often empty while a decision is already published in the case
  text. An empty code is rendered "not recorded in case metadata", never as
  "undecided"; `latest_update` and `timeline` hold the decision.
- `no_case_matched` means the CMA listing was read and holds no case for these
  filters; it is not a clearance of a company. A failed read throws instead.
- Case type values: mergers, markets, ca98-and-civil-cartels, consumer-enforcement,
  digital-markets-unit, regulatory-references-and-appeals,
  review-of-orders-and-undertakings, sau-referral, information-and-advice-to-government.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "uk-cma-cases": {
      "url": "https://gateway.pipeworx.io/uk-cma-cases/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/uk-cma-cases/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 2024+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/uk_cma_cases_list \
  -H 'Content-Type: application/json' \
  -d '{"query":"Vodafone"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/uk_cma_cases_list`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-uk-cma-cases` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/uk-cma-cases/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "uk-cma-cases": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-uk-cma-cases"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Uk Cma Cases data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
