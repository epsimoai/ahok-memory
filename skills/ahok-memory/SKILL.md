---
name: ahok-memory
description: "Long-term memory for AI agents, hosted by ahok.io. Use when: remembering user preferences, storing persistent context, recalling past conversations, personalizing responses, or creating new agent memories that persist across sessions."
license: Apache-2.0
metadata:
  author: Epsimo AI
  version: "1.3.5"
  homepage: https://ahok.io
  repository: https://github.com/epsimoai/ahok-memory
---

# ahok.io Memory

Universal long-term memory for AI agents. Store and retrieve memories across conversations.

Memories you store with this skill are sent to and kept on the ahok.io hosted service (`https://memtool.ahok.io`). Do not store passwords, API keys or other secrets as memories.

## Credentials

- **Account**: You need an ahok.io account.
- **API key**: Create an API key in your ahok.io account and set it as `AHOK_API_KEY`.

| Variable | Status | Used by |
| --- | --- | --- |
| `AHOK_API_KEY` | Required | REST API examples. Its value is sent as the `x-api-key` header, also for the hosted MCP endpoint `https://memtool.ahok.io/mcp` |

Keep keys out of chats, prompts and commits. Store them in environment variables or your agent tool's secret settings.

## Quick Start

1.  **Get API Key**: see [Credentials](#credentials), then set `AHOK_API_KEY`.
2.  **Add Memory**: `POST /memory/add` with `content`.
3.  **Recall Memory**: `POST /query` with `query`.

See [references/api_reference.md](references/api_reference.md) for full API documentation.
See [references/examples.md](references/examples.md) for code integration examples.

## Best Practices

1.  **DO NOT explicitly pass user_id** if invoking using a standard Workspace API Key! The middleware natively derives the proper internal dashboard scope UUID directly from the key. Supplying one manually overrides this bridge and renders the memories orphaned from the UI dashboard.
2.  **Use tags** for easier filtering and organization.
3.  **Query at conversation start** to personalize responses based on past interactions.
4.  **Store important facts** as they are shared by users (e.g., preferences, project details).
5.  **Use workspaces** to effectively isolate memories by different project domains entirely on the backend auto-magically.

## MCP Integration

Add this block to your MCP client configuration (for Claude Desktop, `claude_desktop_config.json`). It connects to the hosted MCP endpoint and sends your ahok.io API key (the `AHOK_API_KEY` value) in the `x-api-key` header:

```json
{
  "mcpServers": {
    "ahok-memory": {
      "url": "https://memtool.ahok.io/mcp",
      "headers": {
        "x-api-key": "<YOUR_AHOK_API_KEY>"
      }
    }
  }
}
```

## Available MCP Tools

Once connected, the agent will have access to:

- **openmemory_query** - Search memories semantically
- **openmemory_store** - Save new memories
- **openmemory_list** - List recent memories
- **openmemory_get** - Fetch a specific memory
- **openmemory_reinforce** - Boost memory importance

## API Endpoints

### Authentication
All requests require an API key in the `x-api-key` header.
Read it from the `AHOK_API_KEY` environment variable. Base URL: `https://memtool.ahok.io`. See [Credentials](#credentials) for where to get the key.

### POST /memory/add
Store a new memory.

**Request Body:**
```json
{
  "content": "The information to remember",
  "user_id": "optional-user-identifier",
  "tags": ["optional", "tags"],
  "metadata": {"any": "json object"},
  "memory_key_id": "optional-workspace-uuid"
}
```

**Response:**
```json
{
  "id": "uuid",
  "primary_sector": "semantic|procedural|episodic",
  "sectors": ["semantic"],
  "chunks": 1
}
```

### POST /query
Search memories semantically.

**Request Body:**
```json
{
  "query": "natural language search",
  "k": 5,
  "user_id": "optional-filter"
}
```

**Response:**
```json
{
  "query": "natural language search",
  "result": "Formatted memory results as text",
  "matches": [
    {"id": "uuid", "content": "...", "score": 0.95}
  ]
}
```

### POST /openmemory/reinforce
Reinforce a memory's importance (prevents decay).

**Request Body:**
```json
{
  "memory_id": "uuid",
  "boost_factor": 1.5
}
```

**Response:**
```json
{
  "id": "uuid",
  "reinforced": true,
  "new_importance": 0.95
}
```

### GET /memory/all
List all memories with pagination.

**Query Parameters:**
- `user_id`: Filter by user
- `l`: Limit (default 50)
- `u`: Offset (default 0)
- `key_id`: Filter by workspace

### DELETE /memory/{id}
Delete a specific memory.

## Memory Sectors

Memories are automatically classified into sectors:
- **semantic**: Facts, knowledge, preferences
- **procedural**: How-to, processes, workflows
- **episodic**: Events, conversations, experiences

## Security Best Practices

1. **API Key Management**
   - Never commit API keys to version control
   - Use environment variables for API keys (`AHOK_API_KEY`)
   - Rotate keys regularly
   - Use different keys for development/production

2. **Data Privacy**
   - Always use `user_id` for multi-user applications
   - Use workspaces (`memory_key_id`) to isolate sensitive data
   - Implement proper access controls in your application layer
   - Regularly audit stored memories for sensitive information

3. **Rate Limiting**
   - Implement application-level rate limiting
   - Cache frequently accessed memories
   - Use batch operations when available

4. **Network Security**
   - Always use HTTPS endpoints
   - Validate SSL certificates
   - Consider using API gateways for additional security layers

## Integration Examples

### Python
```python
import requests
import os

API_KEY = os.getenv("AHOK_API_KEY")
BASE_URL = "https://memtool.ahok.io"

def remember(content, user_id=None):
    return requests.post(f"{BASE_URL}/memory/add",
        headers={"x-api-key": API_KEY},
        json={"content": content, "user_id": user_id}
    ).json()

def recall(query, k=5):
    return requests.post(f"{BASE_URL}/query",
        headers={"x-api-key": API_KEY},
        json={"query": query, "k": k}
    ).json()
```

### TypeScript
```typescript
const API_KEY = process.env.AHOK_API_KEY;
const BASE_URL = "https://memtool.ahok.io";

async function remember(content: string, userId?: string) {
  const res = await fetch(`${BASE_URL}/memory/add`, {
    method: "POST",
    headers: { "Content-Type": "application/json", "x-api-key": API_KEY! },
    body: JSON.stringify({ content, user_id: userId })
  });
  return res.json();
}

async function recall(query: string, k = 5) {
  const res = await fetch(`${BASE_URL}/query`, {
    method: "POST",
    headers: { "Content-Type": "application/json", "x-api-key": API_KEY! },
    body: JSON.stringify({ query, k })
  });
  return res.json();
}
```

See [references/api_reference.md](references/api_reference.md) for full API documentation.
See [references/examples.md](references/examples.md) for more integration examples.
