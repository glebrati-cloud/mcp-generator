---
name: mcp-generator
description: >
  Generate a complete MCP (Model Context Protocol) server to connect Claude to any API or app.
  Use this skill whenever the user wants to create an MCP server, build a Claude integration,
  connect Claude to an external service, create tools for Claude, build an API connector,
  or make Claude interact with a third-party app. Also trigger when the user says things like
  "connect Claude to Notion", "make a Slack MCP", "build an integration for my API",
  "I want Claude to access my database", "create tools for this API", "generate an MCP",
  "build a connector". This skill takes API documentation, OpenAPI/Swagger specs, or even
  just a description and generates a fully functional MCP server with proper tool definitions,
  authentication handling, error management, and TypeScript types.
---

# MCP Generator

Generate production-ready MCP (Model Context Protocol) servers that connect Claude to any API,
database, or external service. From API docs to working connector in minutes.

## What is MCP?

MCP (Model Context Protocol) is the open standard that lets Claude interact with external
tools and data sources. An MCP server exposes "tools" that Claude can call. This skill
generates those servers automatically.

## Core Workflow

### Step 1: Understand the Target API

**From OpenAPI/Swagger spec** (best case):
- Parse the spec file (JSON or YAML)
- Extract all endpoints, methods, parameters, request/response schemas
- Identify authentication method (API key, OAuth2, Bearer token)
- Map response types to TypeScript interfaces

**From API documentation URL**:
- Read the documentation
- Extract endpoints, parameters, authentication
- Identify rate limits and pagination patterns

**From user description** (minimal input):
- Ask clarifying questions about needed operations
- Identify the API base URL and auth method
- Determine the 5-10 most important operations to expose as tools

**From existing code/SDK**:
- Analyze the SDK public API surface
- Map SDK methods to MCP tools
- Preserve type definitions

### Step 2: Design the Tool Surface

Not every API endpoint should be an MCP tool. Design thoughtfully:

**Tool Selection Criteria**
- Does Claude need this to accomplish user tasks? (include)
- Is this destructive and needs human confirmation? (include with confirmation)
- Is this a low-level utility? (maybe exclude)
- Would this expose sensitive data without clear intent? (exclude or gate)

**Tool Naming Convention**
```
{service}_{action}_{resource}
```
Examples: slack_send_message, github_create_issue, notion_query_database

**Tool Description Best Practices**
Each tool description should tell Claude:
1. What the tool does (1 sentence)
2. When to use it (common scenarios)
3. What it returns (output shape)
4. Important constraints (rate limits, permissions)

**Input Schema Design**
- Descriptive parameter names (not q, use search_query)
- Descriptions for every parameter
- Required vs optional marked correctly
- Enums for known valid values
- Default values where sensible
- JSON Schema validation (min, max, pattern)

### Step 3: Generate the MCP Server

**Project Structure**
```
mcp-{service-name}/
+-- package.json
+-- tsconfig.json
+-- src/
|   +-- index.ts          # Entry point, server setup
|   +-- tools/            # One file per tool group
|   +-- client.ts         # API client with auth and error handling
|   +-- types.ts          # TypeScript interfaces
|   +-- utils.ts          # Pagination, rate limiting, retries
+-- README.md
+-- .env.example
```

**Server Entry Point Pattern**
```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new McpServer({
  name: "mcp-{service}",
  version: "1.0.0",
});

// Register tools here

const transport = new StdioServerTransport();
await server.connect(transport);
```

**Tool Registration Pattern**
```typescript
server.tool(
  "tool_name",
  "Description of what this tool does",
  {
    param1: z.string().describe("What this parameter is"),
    param2: z.number().optional().describe("Optional parameter"),
  },
  async ({ param1, param2 }) => {
    const result = await apiClient.doSomething(param1, param2);
    return {
      content: [{ type: "text", text: JSON.stringify(result, null, 2) }],
    };
  }
);
```

**API Client Pattern**
```typescript
class ApiClient {
  private baseUrl: string;
  private apiKey: string;

  constructor() {
    this.baseUrl = process.env.API_BASE_URL;
    this.apiKey = process.env.API_KEY;
    if (!this.apiKey) throw new Error("API_KEY env variable required");
  }

  async request(endpoint, options = {}) {
    const response = await fetch(this.baseUrl + endpoint, {
      ...options,
      headers: {
        "Authorization": "Bearer " + this.apiKey,
        "Content-Type": "application/json",
        ...options.headers,
      },
    });
    if (!response.ok) {
      throw new Error("API error " + response.status + ": " + await response.text());
    }
    return response.json();
  }
}
```

### Step 4: Handle Authentication

| Auth Type | Implementation |
|-----------|---------------|
| API Key | Env variable, sent as header or query param |
| Bearer Token | Env variable, Authorization: Bearer {token} |
| OAuth2 | Token refresh logic, store tokens, handle expiry |
| Basic Auth | Base64 encode, Authorization: Basic {encoded} |

Always: read from env variables, validate at startup, handle auth errors gracefully.

### Step 5: Error Handling and Resilience

**Error Classification**
- 429 or 5xx: retry with exponential backoff
- 401/403: clear message about auth failure
- 4xx: return API error for Claude self-correction

**Rate Limiting**: respect rate limit headers, implement token bucket
**Pagination**: auto-paginate lists with sensible max

### Step 6: Generate Documentation

Create README with: setup instructions, available tools table, auth guide, Claude config snippet.

### Step 7: Deliver and Test

Save all files, provide setup instructions, suggest testing first tool.

## Common Service Templates

**Slack**: send_message, list_channels, search_messages, add_reaction, create_channel
**GitHub**: create_issue, list_issues, create_pr, search_code, get_file_contents
**Notion**: query_database, create_page, update_page, search, get_page
**Postgres**: query, list_tables, describe_table, insert, update
**Stripe**: list_customers, create_payment, list_invoices, get_balance

## Quality Checklist

- [ ] All tools have clear descriptions
- [ ] Input schemas have parameter descriptions
- [ ] Auth via environment variables with validation
- [ ] Errors are readable messages (not stack traces)
- [ ] Rate limiting respected
- [ ] README has complete setup instructions
- [ ] .env.example lists all required variables
- [ ] TypeScript types for all responses
- [ ] package.json has correct dependencies
