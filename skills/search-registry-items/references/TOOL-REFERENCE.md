# Tool Reference

Read this file when you need parameter details, response fields, or pagination behavior.

## MCP server

| Field | Value |
| ----- | ----- |
| Server | `user-shoogle` |
| Tool | `search_registry_items` *(only enabled tool)* |
| Endpoint | `https://mcp.shoogle.dev/mcp` |

Call via `CallMcpTool` on server `user-shoogle`. Read the MCP tool descriptor before calling if parameters may have changed.

## Search behavior

`query` runs **full-text matching** against indexed `name` and `description` fields. It does not search titles or use semantic/vector ranking.

- Prefer short keywords: `button`, `hero`, `data-table`
- Description matches are valid — e.g. `animated` may surface items whose description mentions animation
- Avoid registry prefixes in the query (`@acme/button` → query `button`)


| Parameter | Type | Required | Default | Notes |
| --------- | ---- | -------- | ------- | ----- |
| `query` | string | yes | — | Full-text search on item `name` and `description` |
| `offset` | number | no | `0` | Pagination start index |
| `limit` | number | no | `100` | Page size; max `100` |

## Response

```json
{
  "query": "button",
  "pagination": {
    "total": 42,
    "offset": 0,
    "limit": 100,
    "hasMore": false
  },
  "items": [
    {
      "name": "button",
      "type": "registry:ui",
      "description": "…",
      "registry": "shadcn",
      "addCommandArgument": "shadcn/button"
    }
  ],
  "totalResults": 42,
  "timestamp": "2026-05-22T…"
}
```

## Item fields

| Field | Use |
| ----- | --- |
| `name` | Item name in the registry JSON |
| `type` | e.g. `registry:ui`, `registry:block` |
| `description` | Optional short description |
| `registry` | Namespace used as `@`-prefix in CLI |
| `addCommandArgument` | Pass to `npx shadcn@latest add` — e.g. `acme/button` |

## Pagination

Increment `offset` by `limit` while `pagination.hasMore` is `true`. Stop when you have enough candidates or `hasMore` is `false`.

## Call examples

```json
{ "query": "button" }
{ "query": "card", "offset": 100, "limit": 100 }
{ "query": "sidebar", "limit": 20 }
```
