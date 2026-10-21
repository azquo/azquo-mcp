<p align="center"><img src="azquo-logo.png" width="96" alt="Azquo"></p>

# Azquo MCP server

**Exact, shared memory for AI agents.** Azquo is a hosted hierarchical database: each fact is stored once, in
hierarchies that always total correctly, every write is attributed to the agent and the person behind it, and any
figure traces back to the file, person and date it came from. Several agents and people can work in the same
database at once, and people see the same data live in Microsoft Excel.

This repository describes the **hosted** Azquo MCP server - it holds no server code. The server runs at:

```
https://data.azquo.ai/mcp
```

Transport: Streamable HTTP. Listed in the official MCP Registry as `ai.azquo/azquo`.

## Connect

You need an Azquo account - evaluation accounts on request at <https://www.azquo.com/connect/>.

**Claude (claude.ai, desktop)** - Settings > Connectors > Add custom connector, enter `https://data.azquo.ai/mcp`,
and sign in with your Azquo login.

**Claude Code**

```bash
claude mcp add --transport http azquo https://data.azquo.ai/mcp
```

**Cursor, VS Code and other MCP clients** - add the server from [`mcp.json`](mcp.json):

```json
{
  "mcpServers": {
    "azquo": {
      "type": "http",
      "url": "https://data.azquo.ai/mcp"
    }
  }
}
```

Setup notes written for AI agents (Cline and others) are in [`llms-install.md`](llms-install.md).

Clients that support OAuth sign in with an Azquo login (OAuth 2.1 with dynamic client registration; discovery at
`https://data.azquo.ai/.well-known/oauth-protected-resource`). Others can send an API key from the Azquo admin site:

```
Authorization: Bearer <your API key>
```

## What an agent can do

- Create databases and import spreadsheets, CSV files and documents
- Build and reorganise hierarchies without designing a schema first
- Query with AQL and run Excel reports
- Trace any value back to its source - file, person and date
- Rehearse risky changes on a copy, see history, and restore deleted names
- Keep notes on what the data means inside the database, for the next agent

A shared library of worked examples (budgeting, management accounts, time and expenses, insurance premiums) can be
opened and copied as a starting point.

## Tools

40 tools. Each carries MCP annotations, so clients can run read-only tools freely and ask before
destructive ones. Full descriptions and input schemas are in [`server-card.json`](server-card.json), also served
live at `https://data.azquo.ai/.well-known/mcp/server-card.json`.

| Tool | Title | Kind | Summary |
|---|---|---|---|
| `audit_cell` | Trace where a value came from | read-only | Explain where one value in a query_aql result came from: the provenance (which import, file, user or write, and when) of every stored value behind the cell,... |
| `current_business` | Show current business | read-only | Return the currently effective business and user for this MCP session, and whether an acting-as override is in effect |
| `describe_database` | Describe database | read-only | Get an overview of an Azquo database: top-level sets with child counts, available attributes, and the 'meta information' subtree (semantic descriptions... |
| `describe_query` | Look up names and notes | read-only | Resolve a name-query expression and return the matched names with full details: parents, attributes (including AI Text bodies and any other attribute... |
| `describe_set` | Describe set | read-only | Drill into a named set: returns its child names with child counts, plus the parent path |
| `list_businesses` | List businesses | read-only | List the businesses this MCP session can switch between |
| `list_database_sheets` | List database sheets | read-only | List every Excel workbook associated with one database, grouped into the three kinds Azquo holds: `reports` (online reports users run), `setupSheets` (the... |
| `list_databases` | List databases | read-only | List all Azquo databases available to the current user |
| `list_deleted_names` | List deleted names | read-only | Names that have been deleted, most recent first, with who deleted them and when, how many children they had and which sets they were in |
| `list_documents` | List documents | read-only | List the documents stored with a database (uploaded via create_upload_link), newest first: id, filename, size, the name they are attached to, and... |
| `list_external_connections` | List external connections | read-only | List the external database connections configured for this business |
| `list_import_templates` | List import templates | read-only | List every import template in the current business |
| `list_library` | List library examples | read-only | Browse the shared Azquo library of example/demo databases |
| `list_names` | Find names | read-only | Find names in a database by text match on their display name, when you know roughly what something is called but not where it sits |
| `list_reports` | List reports | read-only | List the online reports (Excel reports users run in Azquo) the current user can see, across every database in the business |
| `list_users` | List users | read-only | List the user accounts of the current business (see current_business; switch_business to change it) |
| `name_history` | Show name history | read-only | History of one name: every earlier version of its attributes (who set them, who replaced them, when), each change to its membership as a set (names... |
| `query_aql` | Query with AQL | read-only | Query an Azquo database using AQL (Azquo Query Language) |
| `read_document` | Read document | read-only | Read a stored document (see list_documents) as Markdown - headings, lists, tables, slides and pages kept |
| `read_workbook` | Read workbook | read-only | Read inside one workbook held against a database — a `report`, `importTemplate` or `setupSheet` (use list_database_sheets to see what exists) |
| `apply_structure` | Apply structure | write | Add parent → child relations between names |
| `copy_from_library` | Copy from library | write | Copy one database from the shared Azquo library into the current business, with its reports, import templates and uploaded file types |
| `create_database` | Create database | write | Create a new, empty Azquo database in the current business (see current_business) |
| `create_upload_link` | Create upload link | write | Get a link where the USER uploads documents (Word, PDF, Excel, PowerPoint, anything) to keep with a database |
| `get_report_data` | Run report | write | Fetch the full data for a report, including all regions and current choice values |
| `persist_database` | Save database to disk | write | Flush pending writes for this database to disk |
| `restore_name` | Restore deleted name | write | Bring back a deleted name (ids from list_deleted_names or name_history) |
| `switch_business` | Switch business | write | Switch the active business for subsequent MCP tool calls in this session |
| `try_on_copy` | Try changes on a copy | write | Try changes on a throwaway copy of a database before making them for real |
| `aql_write` | Write values with AQL | destructive | Write a 2D region of values into an Azquo database using the same AQL grammar as query_aql |
| `copy_azquo_skills` | Copy Azquo skills | destructive | Copy azquo_skills (the Azquo usage-skills database) from data.azquo.ai into the current business, so a local or offline Azquo has its own copy |
| `delete_database` | Delete database | destructive | Permanently delete an Azquo database by name, with all its names, values and history |
| `delete_import_template` | Delete import template | destructive | Permanently delete one import template (the workbook of rules that interprets incoming data files) and its file on disk |
| `delete_name` | Delete name | destructive | Delete a name from the database |
| `delete_report` | Delete report | destructive | Permanently delete one online report (an Excel report users run in Azquo): its workbook file, its menu entries, its external data requests and its schedules |
| `edit_name_attributes` | Edit name attributes | destructive | Surgical attribute edit on an existing name |
| `history_settings` | Change history settings | destructive | See or change whether a database records history - value history (audit_cell), name attribute history and set membership history (name_history), all... |
| `import_file` | Import file | destructive | Import SMALL INLINE DATA into an Azquo database - a base64-encoded blob under `fileContent` |
| `import_from_query` | Import from external database | destructive | Run SQL against a configured external database connection and import the result into an Azquo database |
| `import_from_url` | Import from URL | destructive | Bulk-load a delimited-text or spreadsheet file into a database by URL |

Any tool except `switch_business`, `list_businesses` and `current_business` also takes an optional `business`
argument, for logins that belong to more than one business.

## REST API

For pipelines and scripts without an agent - bulk file loads (`POST /api/importdata`), AQL over HTTP and more - see
<https://www.azquo.com/connect/>.

## Links

- Website and documentation: <https://www.azquo.com/connect/>
- AQL reference: <https://www.azquo.com/connect/#aql>
- Privacy policy: <https://www.azquo.com/privacy-policy/>
- Contact: <https://www.azquo.com/contact-us/> or info@azquo.com

Azquo is provided by Azquo Holdings Ltd, United Kingdom. The contents of this repository (documentation and
configuration) are released under the MIT licence; the Azquo service itself is proprietary.
