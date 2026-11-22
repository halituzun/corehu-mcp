# CoreHu GPU Cloud — MCP server

Find, price and rent cloud GPUs from your AI assistant. [CoreHu](https://corehu.com) runs a remote
[Model Context Protocol](https://modelcontextprotocol.io) server, so Claude, ChatGPT, Claude Code, Cursor, VS Code and
any other MCP client can search live GPU offers, compare hourly prices, estimate job costs and, once you sign in and allow
it, rent and manage machines.

This repository holds the public configuration: the MCP Registry entry, a Claude Code plugin and install instructions.
The server itself is hosted by CoreHu; there is nothing to install or run locally.

| Server | URL | Sign-in |
| --- | --- | --- |
| Account (recommended) | `https://corehu.com/api/mcp/account` | OAuth 2.1 (PKCE, dynamic client registration) |
| Catalog | `https://corehu.com/api/mcp` | none, read-only prices and offers |

Both use Streamable HTTP. Registry name: `com.corehu/gpu-cloud`.
Full guide: **https://corehu.com/developers/mcp**

## Install

**Claude (claude.ai and desktop)**: Settings → Connectors → Add custom connector → paste the account URL → Connect.

**ChatGPT**: Settings → Apps → Advanced settings → developer mode → create an app with the account URL and OAuth.

**Claude Code**

```bash
claude mcp add --transport http corehu https://corehu.com/api/mcp/account
```

or as a plugin:

```
/plugin marketplace add halituzun/corehu-mcp
/plugin install corehu@corehu
```

**VS Code**: [Install in VS Code](vscode:mcp/install?%7B%22name%22%3A%22corehu%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A//corehu.com/api/mcp/account%22%7D) (opens the CoreHu sign-in the first time a tool runs), or `.vscode/mcp.json`:

```json
{ "servers": { "corehu": { "type": "http", "url": "https://corehu.com/api/mcp/account" } } }
```

**Cursor**: [Add to Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=corehu&config=eyJ1cmwiOiJodHRwczovL2NvcmVodS5jb20vYXBpL21jcCJ9) adds the catalog server (no sign-in). For account tools, create a connection under
CoreHu Console → Keys → AI connections and use `~/.cursor/mcp.json`:

```json
{ "mcpServers": { "corehu": { "url": "https://corehu.com/api/mcp/account", "headers": { "Authorization": "Bearer <SECRET>" } } } }
```

## Tools

Catalog, read-only, no sign-in:

- `search_offers`: free GPU machines by model, count, VRAM, price and country, cheapest first
- `get_gpu_prices`: starting hourly USD price per GPU model
- `estimate_cost`: USD cost of a job for N hours on the cheapest free machine
- `get_gpu_availability`: free machines per GPU model and country
- `recommend_gpu`: GPU memory estimate, a ready recipe and fitting offers for a job (LLM serving, fine-tuning, image generation)
- `list_recipes`: ready setups (vLLM API, Ollama, ComfyUI, LoRA fine-tuning, PyTorch notebook)

Account, only what you share on the consent screen:

- `list_machines`, `get_machine`, `get_metrics`, `get_usage`, `get_connection_guide`, `get_balance`

Rental, only if you allow it:

- `list_templates`, `prepare_rental` → `confirm_rental`, `get_rental_status`, `stop_rental`, `start_rental`,
  `cancel_rental`, `prepare_terminate` → `confirm_terminate`

## Safety

- Renting and deleting are two-step: the assistant shows the exact price or what will be deleted and acts only after
  you say yes in the chat.
- You choose per machine what the assistant may do (read, stop/start, delete, commands). Delete and commands are off by default.
- Optional limits per connection: max USD per hour, machines at once, rentals per day.
- The assistant cannot add funds, make payments or read private SSH keys.
- Connections expire after 30 days; revoke any time under Console → Keys → AI connections.

Prices are in USD per hour and billed per second from a prepaid balance.

## Example prompts

- "What's the cheapest RTX 4090 on CoreHu right now?"
- "How much would 10 hours on an H100 cost?"
- "Recommend a GPU to serve Llama 3 70B in 4-bit, then rent it."
- "Is my CoreHu machine still running? Show its GPU utilization."

## Support

info@corehu.com · [Privacy Policy](https://corehu.com/privacy) · [Terms](https://corehu.com/terms)
