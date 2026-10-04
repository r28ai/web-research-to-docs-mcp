# Web Research to Docs

Competitor watch, cited research reports, paper alerts and market maps, filed where you read.

An MCP server with **10 workflows** across Firecrawl, Tavily, Notion, Slack, Google Docs, Google Sheets, GitHub and Linear. Each workflow is a prompt your agent runs as a slash command, over the 19 tools it needs and no others.

```bash
uv tool install https://github.com/r28ai/web-research-to-docs-mcp/releases/download/v0.1.0/web_research_to_docs_mcp-0.1.0-py3-none-any.whl
claude mcp add research -- web-research-to-docs-mcp
```

It installs with [uv](https://docs.astral.sh/uv/) from this repository's release, with no git and nothing to build; nothing but Charter and the libraries it uses comes from PyPI. To update, run the install line from the [latest release](https://github.com/r28ai/web-research-to-docs-mcp/releases/latest). If a desktop app cannot find `web-research-to-docs-mcp`, give it the full path from `which web-research-to-docs-mcp` (`where web-research-to-docs-mcp` on Windows).

Then ask your agent to **connect your apps**, or run `/mcp__research__setup`.

## Connect your apps

Ask the agent to connect one ("connect Linear"). It tells you where to get that app's key and the command that stores it, and the next call works, with no restart. The agent never asks for a key in the chat.

Or connect everything this server uses from a terminal:

```bash
web-research-to-docs-mcp login            # each app in turn
web-research-to-docs-mcp login firecrawl  # just one
web-research-to-docs-mcp status           # what is connected
```

Tokens and keys go to your operating system's keychain (macOS Keychain, Windows Credential Manager, the Secret Service on Linux), and are checked with one read-only call to the app's own API before they are kept. Every key, token and OAuth client is yours: we register no app with any of these services, and nothing passes through a server of ours, because there isn't one.

| App | How it connects | Or set |
|---|---|---|
| Firecrawl | Your own key ([get one](https://www.firecrawl.dev/app/api-keys)), entered once. | `FIRECRAWL_API_KEY` |
| Tavily | Your own key ([get one](https://app.tavily.com/home)), entered once. | `TAVILY_API_KEY` |
| Notion | Your own key ([get one](https://www.notion.so/profile/integrations)), entered once. Then share the pages it should see with the integration. | `NOTION_API_KEY` |
| Slack | Your own key ([get one](https://docs.r28.ai/charter/auth/setup/slack)), entered once. A bot token from your own Slack app, which the guide sets up in about three minutes. | `SLACK_BOT_TOKEN` |
| Google | Browser sign-in, over your own OAuth client ([make one](https://docs.r28.ai/charter/auth/setup/google)). | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` |
| GitHub | Your own key ([get one](https://github.com/settings/tokens/new?description=Charter&scopes=repo,read:user)), entered once. | `GITHUB_TOKEN` |
| Linear | Your own key ([get one](https://linear.app/settings/account/security)), entered once. | `LINEAR_API_KEY` |

A variable set in your client's config always wins over the keychain.

## Workflows

| Workflow | What you get | Apps |
|---|---|---|
| **Competitive intel digest** <br>`competitive_intel_digest` | Site changes and news per competitor, weekly, in one page. | Firecrawl, Tavily, Notion, Slack |
| **Deep research → shared doc** <br>`deep_research_to_shared_doc` | A cited report in the team's Drive, not in someone's chat history. | Tavily, Google Docs, Slack |
| **Paper watch** <br>`paper_watch` | New papers on your topics land in a reading list with abstracts. | Firecrawl, Notion, Slack |
| **Market map** <br>`market_map` | Players, pricing, funding and positioning, one row each. | Tavily, Firecrawl, Google Sheets |
| **Pricing benchmark memo** <br>`pricing_benchmark_memo` | Ten competitors' pricing pages normalised into one table and a recommendation. | Firecrawl, Google Sheets, Google Docs |
| **Regulatory watch** <br>`regulatory_watch` | A regulator's guidance page changes and legal hears the same day. | Firecrawl, Notion, Slack |
| **Company due diligence** <br>`company_due_diligence` | Public footprint, open-source activity and product surface in one memo. | Tavily, GitHub, Firecrawl, Google Docs |
| **Public complaints → roadmap evidence** <br>`public_complaints_to_roadmap_evidence` | What people complain about in your category, attached to the issues it supports. | Tavily, Firecrawl, Notion, Linear |
| **Open-source landscape** <br>`open_source_landscape` | Who is building what in your space, with momentum, before you build it. | GitHub, Tavily, Notion |
| **Web page → Linear issue** <br>`web_page_to_linear_issue` | A public bug report, forum post or status page becomes a tracked issue. | Firecrawl, Linear |

Every prompt takes one optional argument, `details`: the repo, team, channel, customer or date range you mean, so the agent does not have to ask. In Claude Code, put it in quotes, or only its first word arrives:

```
/mcp__research__competitive_intel_digest "competitors acme.com and globex.com"
```

Reads run without asking. Before anything that creates, sends, changes or deletes, the prompt tells the agent to show you the call and wait.

6 of the 10 workflows need no Google or Granola credential.

## Other clients

**Claude Desktop**: install [uv](https://docs.astral.sh/uv/getting-started/installation/) if you have not, since Claude Desktop starts the server with it, then open the `.mcpb` from the [latest release](https://github.com/r28ai/web-research-to-docs-mcp/releases/latest). Claude asks for any keys in its own settings and keeps them in your keychain. The first start takes a few seconds longer, while uv installs it.

**VS Code** (`.vscode/mcp.json`): VS Code asks for each key the first time the server starts and stores it securely. Leave out any you stored with `login`.

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "firecrawl-api-key",
      "description": "Firecrawl: API key",
      "password": true
    },
    {
      "type": "promptString",
      "id": "tavily-api-key",
      "description": "Tavily: API key",
      "password": true
    },
    {
      "type": "promptString",
      "id": "notion-api-key",
      "description": "Notion: Integration secret (ntn_\u2026)",
      "password": true
    },
    {
      "type": "promptString",
      "id": "slack-bot-token",
      "description": "Slack: Bot token (xoxb-\u2026)",
      "password": true
    },
    {
      "type": "promptString",
      "id": "google-client-secret",
      "description": "Google: OAuth client secret",
      "password": true
    },
    {
      "type": "promptString",
      "id": "github-token",
      "description": "GitHub: Personal access token",
      "password": true
    },
    {
      "type": "promptString",
      "id": "linear-api-key",
      "description": "Linear: Personal API key",
      "password": true
    }
  ],
  "servers": {
    "research": {
      "type": "stdio",
      "command": "web-research-to-docs-mcp",
      "env": {
        "FIRECRAWL_API_KEY": "${input:firecrawl-api-key}",
        "TAVILY_API_KEY": "${input:tavily-api-key}",
        "NOTION_API_KEY": "${input:notion-api-key}",
        "SLACK_BOT_TOKEN": "${input:slack-bot-token}",
        "GOOGLE_CLIENT_SECRET": "${input:google-client-secret}",
        "GITHUB_TOKEN": "${input:github-token}",
        "LINEAR_API_KEY": "${input:linear-api-key}",
        "GOOGLE_CLIENT_ID": ""
      }
    }
  }
}
```

**Cursor** (`.cursor/mcp.json`) starts it the same way:

```json
{
  "mcpServers": {
    "research": {
      "command": "web-research-to-docs-mcp"
    }
  }
}
```

**Codex** (`~/.codex/config.toml`) starts a turn without waiting for a server unless it is `required`, and then the agent has none of its tools. `required = true` makes the session wait for it, and `startup_readiness = "catalog"` waits for its tool list rather than just its connection:

```toml
[mcp_servers.research]
command = "web-research-to-docs-mcp"
required = true
startup_readiness = "catalog"
startup_timeout_sec = 30
```

Name the server `research`. A host builds each tool's name from that key, and a longer one can push a tool past the 64 characters a function name allows.

## Built with Charter

Every tool here is a [Charter](https://github.com/r28ai/charter) declaration: a Pydantic schema saying where each field goes on the wire. Charter's runtime builds the request, attaches and refreshes the credential, and trims the response before the model reads it. It runs in your process, with no proxy and no telemetry.

The 19 tool schemas come to 38,591 tokens.

The same tools work in your own agent, without MCP:

```python
from charter.adapters.openai import to_openai_tools
from charter_packs_mcp import FAMILIES

tools = FAMILIES["research"].tools()
definitions = to_openai_tools(tools)   # or charter.adapters.langchain
```

Need an API that isn't here? [Write a pack](https://docs.r28.ai/charter/start/coding-agents): your coding agent writes the declarations, and Charter's conformance suite checks them.

<details>
<summary>All 19 tools</summary>

- **Firecrawl**: `firecrawl_monitor_checks_list`, `firecrawl_research_papers_search`, `firecrawl_research_paper_get`, `firecrawl_extract`, `firecrawl_monitor_create`, `firecrawl_crawl`, `firecrawl_scrape`
- **Tavily**: `tavily_search`, `tavily_research_create`, `tavily_research_get`
- **Notion**: `notion_pages_create`
- **Slack**: `slack_chat_post_message`
- **Google Docs**: `gdocs_documents_create`
- **Google Sheets**: `gsheets_spreadsheets_values_update`
- **GitHub**: `github_search_repositories`, `github_repos_list_languages`
- **Linear**: `linear_customer_need_create`, `linear_search_issues`, `linear_issue_create`

</details>

## License

Apache 2.0.
