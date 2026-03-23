# MCP Generator - Claude Skill

> **Connect Claude to anything.** Generate a complete MCP server for any API in minutes.

[![Claude Skill](https://img.shields.io/badge/Claude-Skill-blueviolet?style=for-the-badge)](https://github.com/glebrati-cloud/mcp-generator)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)
[![Stars](https://img.shields.io/github/stars/glebrati-cloud/mcp-generator?style=for-the-badge)](https://github.com/glebrati-cloud/mcp-generator/stargazers)

---

## What is this?

A Claude skill that **generates complete MCP (Model Context Protocol) servers** to connect Claude to any API, database, or service. Give it API docs, an OpenAPI spec, or even just a description -- and it produces a fully functional MCP server.

```
You:    "Connect Claude to the Notion API"
Claude: *reads API docs, designs tool surface, generates full TypeScript
         MCP server with 8 tools, auth, pagination, error handling,
         README, and setup instructions*
```

**This is the skill that creates integrations.**

---

## How it works

```
INPUT (any of these):                    OUTPUT:

 OpenAPI/Swagger spec          +         mcp-{service}/
 API documentation URL         +------>  +-- src/index.ts    (server + tools)
 "Connect Claude to X"        |         +-- src/client.ts   (API client)
 Existing SDK/library          +         +-- src/types.ts    (TypeScript types)
                                         +-- package.json
                                         +-- .env.example
                                         +-- README.md
```

---

## What you get

A complete, production-ready MCP server with:

- **Smart tool design** -- Not every endpoint becomes a tool. The skill selects operations Claude actually needs.
- **Type-safe** -- Full TypeScript with interfaces for all API responses
- **Auth handling** -- API keys, Bearer tokens, OAuth2 via environment variables
- **Error handling** -- Retries, rate limiting, clear error messages
- **Pagination** -- Auto-pagination for list endpoints
- **Documentation** -- README with setup instructions and Claude config snippet

---

## Examples

### "Connect Claude to Slack"

| Tool | Description |
|------|-------------|
| `slack_send_message` | Send a message to a channel |
| `slack_list_channels` | List available channels |
| `slack_search_messages` | Search message history |
| `slack_add_reaction` | React to a message |
| `slack_create_channel` | Create a new channel |

### "Build an MCP for my REST API"

Give it your OpenAPI spec or API docs, and it generates custom tools matching your exact endpoints.

### "Connect Claude to Postgres"

| Tool | Description |
|------|-------------|
| `postgres_query` | Execute a read-only SQL query |
| `postgres_list_tables` | List all tables |
| `postgres_describe_table` | Get table schema |
| `postgres_insert` | Insert rows (with confirmation) |
| `postgres_update` | Update rows (with confirmation) |

---

## Installation

```bash
claude skill install glebrati-cloud/mcp-generator
```

Or copy [`SKILL.md`](./SKILL.md) into your Claude skills directory.

---

## Usage

```
"Generate an MCP server for the GitHub API"
"Connect Claude to my Supabase database"
"Build an MCP connector for this OpenAPI spec" [attach spec]
"I want Claude to send emails via SendGrid"
"Create tools for this REST API: https://api.example.com/docs"
```

---

## Supported Input Formats

- **OpenAPI 3.x / Swagger 2.0** (JSON or YAML)
- **API documentation** (URL or pasted text)
- **Natural language** description
- **Existing SDK** code to wrap
- **GraphQL schema**
- **Database connection** string

---

## Why this skill?

| Feature | Manual MCP dev | This skill |
|---------|---------------|------------|
| Time to working server | Hours-days | Minutes |
| Tool naming | Inconsistent | Standardized |
| Error handling | Often forgotten | Built in |
| Auth setup | Manual | Auto-detected |
| Documentation | Usually missing | Generated |
| Type safety | Optional | Default |

---

## Contributing

PRs welcome! Ideas: service templates, OAuth2 flows, test generation, transport options.

## License

MIT

---

<p align="center">
  <b>Built by <a href="https://github.com/glebrati-cloud">Gabriel Lebrati</a></b><br>
  <sub>The skill that builds integrations</sub>
</p>
