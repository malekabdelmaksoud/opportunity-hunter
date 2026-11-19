# Opportunity Hunter

A remote Model Context Protocol (MCP) server on Cloudflare Workers for discovering public internships, PFE projects, apprenticeships, and junior opportunities.

## Features

- Search public web results by skills, location, opportunity type, and freshness.
- Return listing titles, URLs, snippets, and dates when available.
- Expose a health endpoint at `/health`.
- Protect the remote MCP endpoint at `/mcp` with a Bearer API key (or `x-api-key`).

## Boundaries

This project searches public web pages only. It does not log in to LinkedIn, access private profiles, messages, or notifications, bypass website restrictions, send messages, or submit applications. Search snippets may be incomplete or outdated; always verify an offer on its original website.

## Cloudflare deployment

This repository is configured as a Cloudflare Worker using Wrangler and the Cloudflare Agents MCP handler (Streamable HTTP).

In the Cloudflare Workers & Pages project settings, use:

- **Build command:** `npm run build`
- **Deploy command:** `npx wrangler deploy`
- **Root directory:** `/` (repository root)

The Worker entry point is `src/index.ts`, configured in `wrangler.jsonc`.

### Required Worker secrets

In **Worker → Settings → Variables and Secrets**, add these as secrets:

- `TAVILY_API_KEY`: your Tavily Search API key.
- `MCP_API_KEY`: a long random secret used by Claude to authenticate.

Never commit either value to Git or paste them into public messages.

The public endpoints are:

- Health check: `https://YOUR-WORKER.workers.dev/health`
- MCP endpoint: `https://YOUR-WORKER.workers.dev/mcp`

The health endpoint reports whether Tavily is configured. The MCP endpoint requires the configured API key.

## MCP tools

### `server_info`

Reports server status and whether the search provider is configured.

### `search_opportunities`

Parameters:

- `keywords`: skills or role, e.g. Java Spring Boot, web development, cybersecurity, AI security.
- `location`: country, city, or region (default: Tunisia).
- `opportunityType`: internship, PFE, junior job, apprenticeship, or any.
- `freshness`: day, week, month, year, or any.
- `count`: 1–10 results.

## Connect Claude Web

After deployment succeeds and the health endpoint responds:

1. Add a custom connector in Claude Web using the HTTPS MCP URL ending in `/mcp`.
2. Configure the connector's Bearer token using the same `MCP_API_KEY` secret.
3. Test `server_info`, then `search_opportunities`.

Claude's connector UI and authentication options can vary by account. Do not connect until the endpoint is deployed and tested.

## Development checks

```bash
npm install
npm run typecheck
npm run build
npx wrangler dev
```

## Security

- Keep API keys in Cloudflare Worker secrets, not in Git.
- Use a unique, high-entropy MCP API key.
- Do not share the deployed connector URL and key publicly.
- Treat external listing text as untrusted input.
- Review eligibility, deadline, location, and application requirements before applying.
