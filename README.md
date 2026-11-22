# Learn MCP with Supabase

This repository is a learning guide for connecting AI clients to Supabase with the Model Context Protocol (MCP). The examples below use Supabase's hosted MCP server; they do not start a server from this repository.

## How it works

```text
Copilot / OpenCode / Claude  ->  Supabase MCP server  ->  Your Supabase project
```

The MCP server exposes Supabase tools to your AI client. Supabase authenticates through a browser-based OAuth flow; you do not need to put a Supabase password, access token, or API key in these configuration examples.

## Before you start

- Have a Supabase project and know its project reference ID. You can find it in the project's dashboard URL or project settings.
- Install and sign in to the AI client you want to use.
- For a safer learning setup, use a development project and start with the read-only, project-scoped URL below. Replace `YOUR_PROJECT_REF` with your project's reference ID:

```text
https://mcp.supabase.com/mcp?project_ref=YOUR_PROJECT_REF&read_only=true&features=database,docs
```

`project_ref` limits the connection to one project. `read_only=true` limits database tools to read-only operations, and `features` selects the database and documentation tool groups. You can remove these restrictions, but do so only if you understand and want to grant the additional access.

## GitHub Copilot in VS Code

1. In VS Code, open this repository (or another workspace) and create `.vscode/mcp.json`.
2. Add the following configuration, replacing `YOUR_PROJECT_REF`:

```json
{
  "servers": {
    "supabase": {
      "type": "http",
      "url": "https://mcp.supabase.com/mcp?project_ref=YOUR_PROJECT_REF&read_only=true&features=database,docs"
    }
  }
}
```

3. Save the file. If VS Code asks you to start or trust the server, review the request and approve it only if it matches this setup.
4. Follow the browser prompt to sign in to Supabase and authorize access.
5. In Copilot Chat, ask it to use the Supabase MCP tools to list the project's tables. Check the tool call and result to confirm the connection.

You can also use VS Code's **MCP: Add Server** command or **Chat: Open Customizations > MCP Servers** to add and manage the server without editing the file. See the [VS Code MCP guide](https://code.visualstudio.com/docs/agent-customization/mcp-servers).

## OpenCode

From the project directory, add the hosted server:

```sh
opencode mcp add supabase --url "https://mcp.supabase.com/mcp?project_ref=YOUR_PROJECT_REF&read_only=true&features=database,docs"
```

To make the server available in every project, add `--global`:

```sh
opencode mcp add supabase --global --url "https://mcp.supabase.com/mcp?project_ref=YOUR_PROJECT_REF&read_only=true&features=database,docs"
```

Authenticate and check the connection:

```sh
opencode mcp auth supabase
opencode mcp list
```

Complete the sign-in flow in your browser. Alternatively, configure a remote server in `opencode.json` under `mcp.servers`; see the [OpenCode MCP guide](https://opencode.ai/docs/mcp-servers/).

## Claude

### Claude Code

Run this in a terminal from your project directory, replacing `YOUR_PROJECT_REF`:

```sh
claude mcp add --scope project --transport http supabase "https://mcp.supabase.com/mcp?project_ref=YOUR_PROJECT_REF&read_only=true&features=database,docs"
```

Start Claude Code, approve the project-scoped server if prompted, then enter `/mcp` and choose **Authenticate** for `supabase`. You can check the server status from a terminal with:

```sh
claude mcp list
```

See the [Claude Code MCP guide](https://code.claude.com/docs/en/mcp).

### Claude Desktop or Claude.ai

1. Open the [Claude Connectors directory](https://claude.ai/customize/connectors).
2. Choose **Add** > **Add custom connector**.
3. Name the connector (for example, `Supabase`) and enter the same project-scoped URL used above.
4. Keep the detected OAuth authentication settings, add the connector, and complete the sign-in prompt.
5. Enable the connector for a conversation and ask Claude to list the project's tables.

On Team or Enterprise plans, an organization owner may need to add the custom connector first. See Anthropic's guide to [adding a custom connector by URL](https://claude.com/docs/connectors/custom/add-unlisted).

## Troubleshooting

- **Authentication is pending:** Use the client's MCP or connector controls to finish the browser sign-in.
- **The server is not listed:** Check that the configuration file is in the correct workspace or user scope, then reload or restart the client.
- **No tools appear:** Confirm the project reference is correct and that the connection is authorized for the expected Supabase account.
- **A tool is unavailable:** The `features` and `read_only` URL parameters intentionally limit which tools are exposed.

## Security notes

- Use a development project while learning, and scope the connection to that project.
- Keep `read_only=true` unless you specifically need write-capable tools. Read-only mode is a useful safeguard, not a substitute for reviewing tool calls.
- Review each tool call before approving it, especially before enabling write access. MCP tools can read or change data depending on the server configuration.
- Do not commit credentials, access tokens, or secrets. The examples above contain only a project reference; authentication happens through the client.
- Disconnect or remove the MCP server when you no longer need it.

For Supabase's full setup and security guidance, see the [Supabase MCP documentation](https://supabase.com/docs/guides/ai-tools/mcp).
