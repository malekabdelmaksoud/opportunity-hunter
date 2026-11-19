# Opportunity Hunter

A remote Model Context Protocol (MCP) server for discovering public internships, PFE projects, apprenticeships, and junior opportunities.

## Features

- Search public web results by skills, location, opportunity type, and freshness.
- Return listing titles, URLs, snippets, and dates when available.
- Expose a health endpoint for hosting checks.
- Protect the remote MCP endpoint with a Bearer API key in production.
- Use Streamable HTTP for remote MCP clients such as Claude Web.

## Boundaries

This project searches public web pages only. It does not log in to LinkedIn, access private profiles, messages, or notifications, bypass website restrictions, send messages, or submit applications. Search snippets may be incomplete or outdated; always verify an offer on its original website.

## Requirements

- Node.js 20+
- A Brave Search API key
- A public HTTPS host for Claude Web

**Cost note:** Brave currently provides $5 in monthly Search API credits (about 1,000 searches at its listed rate), but activating the API requires a payment card. Set a strict usage limit and keep auto-reload disabled if you want to stay within the included credits. Hosting may also have separate limits or charges.

## Local setup

1. Install packages:

   ```bash
   npm install
   ```

2. Copy `.env.example` to `.env` and set `BRAVE_SEARCH_API_KEY`.

3. Start the development server:

   ```bash
   npm run dev
   ```

4. Health check: `http://localhost:3000/health`

5. MCP endpoint: `http://localhost:3000/mcp`

Claude Web cannot connect to localhost. It requires a public HTTPS deployment.

## Tools

### server_info

Reports server status and whether search is configured.

### search_opportunities

Parameters:

- `keywords`: skills or role, e.g. Java Spring Boot, web development, cybersecurity, AI security.
- `location`: country, city, or region (default: Tunisia).
- `opportunityType`: internship, PFE, junior job, apprenticeship, or any.
- `freshness`: day, week, month, year, or any.
- `count`: 1–10 results.

## Deploying for Claude Web

Deploy this Node.js service to a host that provides a public HTTPS URL and supports Node.js web services. Configure these environment variables in the host dashboard:

- `BRAVE_SEARCH_API_KEY`: your search provider key.
- `MCP_API_KEY`: a long random secret. Never commit it to Git.
- `NODE_ENV=production`
- `PORT`: the port supplied by the hosting provider.

Your MCP URL will look like:

```text
https://YOUR-HOST/mcp
```

In Claude, go to **Customize → Connectors → Add custom connector**, enter the HTTPS URL, and configure the request header as a Bearer token using the same `MCP_API_KEY` value. The labels may differ slightly by account.

Do not connect Claude until deployment is live and the health endpoint responds successfully.

## Development checks

```bash
npm run typecheck
npm run build
npm start
```

## Security

- Keep `.env` and API keys out of Git.
- Use a unique, high-entropy MCP API key for a public deployment.
- Do not share the deployed connector URL and key publicly.
- Treat external listing text as untrusted input.
- Review eligibility, deadline, location, and application requirements before applying.

## Status

Initial implementation is committed. Dependency installation, compilation, deployment, API-key configuration, and an end-to-end Claude connection test are still required.