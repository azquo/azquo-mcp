# Installing the Azquo MCP server (for AI agents such as Cline)

Azquo is a **hosted** MCP server. There is nothing to clone, build or run - you only add its address to the
client's MCP settings and give it the user's API key.

## 1. Get an API key (the user does this)

The user needs an Azquo account (evaluation accounts on request at https://www.azquo.com/connect/). They create an
API key for themselves on the Azquo admin site (https://admin.azquo.ai) and give it to you. Never invent a key; if
the user has none, stop and tell them how to get one.

## 2. Add the server

Cline - add this to `cline_mcp_settings.json`, replacing `<AZQUO_API_KEY>` with the user's key:

```json
{
  "mcpServers": {
    "azquo": {
      "type": "streamableHttp",
      "url": "https://data.azquo.ai/mcp",
      "headers": {
        "Authorization": "Bearer <AZQUO_API_KEY>"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

`"type": "streamableHttp"` must be given explicitly. Clients that support OAuth sign-in (Claude, Cursor, VS Code)
can leave out `headers` and sign in with the Azquo login instead.

## 3. Check it works

Call `list_databases`. It should return the databases in the user's Azquo business (plus the shared read-only
`azquo_skills` notes and library examples). A 401 means the key is missing or wrong.

## 4. Before doing real work

Read `azquo_skills` > Start here > READ ME FIRST (`describe_set` on "Start here" with attributes ["AI TEXT"]). Azquo
is a hierarchical database and behaves unlike SQL in ways that can quietly give wrong figures; that note covers them.
