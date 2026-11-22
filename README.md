# Sales reporting with Supabase MCP

This repository configures GitHub Copilot in VS Code, OpenCode, and Claude Code to connect to the Supabase project used for sales reporting. It uses Supabase's hosted MCP server; this repository does not run a server itself.

## What is configured

- [`AGENTS.md`](./AGENTS.md) contains the business reporting instructions: retrieve source data before reporting, calculate revenue as quantity × unit price using completed sales only, use SQL for calculations, and never write to the database.
- [`.vscode/mcp.json`](./.vscode/mcp.json) configures the MCP server for VS Code and Copilot.
- [`opencode.json`](./opencode.json) configures the MCP server for OpenCode.
- [`.mcp.json`](./.mcp.json) configures the MCP server for Claude Code.

All three MCP configurations contain a placeholder project reference. Before connecting, replace `YOUR_PROJECT_REF` in the relevant configuration file with your own Supabase project reference:

```text
https://mcp.supabase.com/mcp?project_ref=YOUR_PROJECT_REF&read_only=true&features=database,docs
```

Find the project reference in your Supabase project's dashboard URL or project settings. It scopes the server to one project. `read_only=true` restricts database access to read-only operations, and `features=database,docs` enables the database and documentation tools needed for reporting. This setting is an important safeguard, but still review tool calls and results.

## Requirements

- Open this repository as the workspace/project in the client. The clients discover project configuration and instructions from the opened workspace.
- Sign in to the client and to the Supabase account that has access to this project.
- Approve the project-level MCP server when prompted. Authentication is completed separately in each client; the configuration files do not contain credentials.

## Connect and authenticate

### GitHub Copilot in VS Code

1. Open this repository in VS Code.
2. Open Copilot Chat and select the agent mode/session that supports MCP tools.
3. Replace `YOUR_PROJECT_REF` in `.vscode/mcp.json` with your Supabase project reference. If prompted, start and trust the `supabase` server, then complete the Supabase browser sign-in.
4. If it does not appear, run **MCP: List Servers** or **Chat: Open Customizations > MCP Servers** to inspect and start it.

The workspace MCP configuration is in `.vscode/mcp.json`. See the [VS Code MCP guide](https://code.visualstudio.com/docs/agent-customization/mcp-servers).

### OpenCode

1. Replace `YOUR_PROJECT_REF` in `opencode.json` with your Supabase project reference, then start OpenCode with this repository as the project.
2. In a terminal, run:

   ```sh
   opencode mcp auth supabase
   opencode mcp list
   ```

3. Complete the browser sign-in. `opencode mcp list` should show `supabase` as connected.

The MCP configuration is in `opencode.json`. OpenCode loads the root `AGENTS.md` as project instructions. See the [OpenCode MCP guide](https://opencode.ai/docs/mcp-servers/) and [instructions guide](https://opencode.ai/docs/instructions/).

### Claude Code

1. Replace `YOUR_PROJECT_REF` in `.mcp.json` with your Supabase project reference, then start Claude Code from this repository.
2. Approve the project MCP server if Claude Code asks for approval.
3. In the Claude Code session, run `/mcp`, select `supabase`, and choose **Authenticate**. Complete the browser sign-in.
4. In a terminal, run `claude mcp list`; the server should show as connected.

The MCP configuration is in `.mcp.json`. Claude Code can use the root `AGENTS.md` as project instructions. See the [Claude Code MCP guide](https://code.claude.com/docs/en/mcp).

### Claude Desktop and Claude.ai

These clients do not use this repository's `.mcp.json` or automatically load its `AGENTS.md`. To connect Supabase, add a custom connector from the [Claude Connectors directory](https://claude.ai/customize/connectors), using the URL above, keep the detected OAuth settings, and complete sign-in. To apply the sales-reporting rules in these clients, add the relevant instructions to the conversation or configure instructions using that client’s supported feature.

## Verify the setup

In each client, make a low-impact request such as:

> Use the Supabase MCP tools to inspect the available sales-related tables and columns. Do not modify data. Report which tables and fields would be needed to calculate completed-sales revenue.

Confirm that the client actually invoked a Supabase MCP tool and that its response came from the intended project. A plausible answer without an MCP tool call does not confirm the connection.

For a sales report, ask the assistant to state the reporting period, show the query or supporting calculation, use completed sales only for revenue, and identify any missing fields or incomplete data. Revenue is not profit or cash flow.

## Troubleshooting

- **Authentication is required:** Complete the browser sign-in in that specific client.
- **Claude Code says approval is pending:** Start Claude Code from this repository and approve the project-scoped server when prompted.
- **The server does not appear:** Confirm this repository is the open workspace/project, then reload or restart the client and inspect its MCP server list.
- **No MCP tool call occurs:** Confirm the server is connected and the selected Copilot mode supports MCP tools. Ask explicitly to use the `supabase` server.
- **Tables or tools are unavailable:** Confirm you replaced `YOUR_PROJECT_REF` with the correct project reference and that your signed-in Supabase account can access that project. The configured `database,docs` feature selection is intentional.
- **Instructions are not applied:** Confirm the client opened this repository as the workspace and that `AGENTS.md` is present. Claude Desktop and Claude.ai do not automatically read it.

## Security

- The MCP URL contains a project reference, not a password or API key. Do not add credentials or tokens to these files.
- Keep the connection read-only for reporting. Do not remove `read_only=true` unless write access is explicitly required and understood.
- Review tool calls; model instructions guide behavior but do not enforce it. The read-only server setting is the database access restriction.
- Commit the configuration and `AGENTS.md` files if you want teammates or a fresh clone to receive the same setup. Authenticate separately on each computer.

For Supabase's full MCP setup and security guidance, see the [Supabase MCP documentation](https://supabase.com/docs/guides/ai-tools/mcp).
