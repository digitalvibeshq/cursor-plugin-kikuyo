# Kikuyo Cursor Plugin

Official [Cursor](https://cursor.com) plugin for [Kikuyo](https://kikuyo.app) - manage product feedback, feature requests, roadmap statuses, and user discussions directly from your AI coding assistant.

## What's Included

- **Hosted MCP Server (`mcp.json`)**: Connects Cursor to `https://mcp.kikuyo.app/mcp` over Streamable HTTP with browser-based OAuth and dynamic client registration - no API keys or secrets stored on disk.
- **Kikuyo Agent Skill (`skills/kikuyo/SKILL.md`)**: Guides the agent through org/project resolution, duplicate checks, status transitions, and threaded replies.

## Installation

### From the Cursor Marketplace

1. Open **Cursor Settings -> Plugins / Customize**.
2. Search for **Kikuyo** and click **Install**.
3. On first connection, Cursor opens your browser to authorize access to your Kikuyo account via OAuth.

### Local Testing / Manual Install

Clone and symlink into Cursor's local plugins directory:

```bash
git clone https://github.com/digitalvibeshq/cursor-plugin-kikuyo.git
mkdir -p ~/.cursor/plugins/local
ln -s "$(pwd)/cursor-plugin-kikuyo" ~/.cursor/plugins/local/kikuyo
```

Then run **Developer: Reload Window** in Cursor.

## Authentication

The hosted MCP server at `https://mcp.kikuyo.app/mcp` uses OAuth 2.1 with PKCE and dynamic client registration. When Cursor first connects, a browser window opens to sign in and authorize your Kikuyo account. No environment variables or committed API keys are required.

## Available MCP Tools

| Tool | Description |
| :--- | :--- |
| `list_orgs` | List all organizations you belong to |
| `get_org` | Get details for a specific organization including its projects |
| `list_projects` | List all projects within an organization |
| `get_project` | Get details for a specific project |
| `list_feedback` | List feedback items for a project, optionally filtered by status (`pending`, `planned`, `in_progress`, `completed`, `rejected`) |
| `get_feedback` | Get full details and body of a feedback item by UUID |
| `create_feedback` | Create a new feedback item on a project |
| `edit_feedback` | Update the title and/or body of a feedback item |
| `update_feedback_status` | Change a feedback item's status |
| `list_comments` | List threaded comments and replies on a feedback item |
| `create_comment` | Post a comment or reply on a feedback item |

## Links

- [Kikuyo](https://kikuyo.app)
- [Kikuyo MCP Documentation](https://kikuyo.app/docs/mcp)
- [Kikuyo REST API Documentation](https://kikuyo.app/docs/api)
- [Kikuyo CLI](https://github.com/digitalvibeshq/kikuyo-cli)

## License

[MIT](./LICENSE)
