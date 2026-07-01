---
title:  "Mobile AI and Cloud Context: Connecting Joey iOS to FastMCP on Render"
date:   2026-06-30
badges:
 - type: info
   tag: mobile
 - type: info
   tag: ai
tags:
  - ai
  - mobile
  - intervals.cu
  - render
  - openrouter
---

# End-to-End Blueprint: Connecting Intervals.icu MCP to Joey iOS via Render and OpenRouter

The Model Context Protocol (MCP) allows LLMs to interact with remote tools and data sources. While desktop clients typically rely on persistent local connections, mobile setups require an architecture optimized for short-lived, atomic network requests. 

This guide outlines how to configure and link four distinct systems—**Intervals.icu**, **Render**, **OpenRouter**, and **Joey iOS**—into a stateless, single-endpoint engine to query fitness metrics and training load insights natively from your phone.

<!--more-->

---

## 1. Intervals.icu: Extracting API Credentials

The Python MCP container acts as a direct proxy to your training logs. It requires developer authentication parameters from the Intervals.icu platform to execute queries successfully.

1. Log into your desktop dashboard at `https://intervals.icu`.
2. Navigate to **Settings** and scroll down to the **API Keys** section.
3. Locate and copy your **Athlete ID** (a unique identifier alphanumeric or numerical string, distinct from your public username).
4. Click **Generate API Key**. Copy the random string immediately and store it securely.

---

## 2. Render: Containerization & Transport Routing

To accept payloads from a mobile device without connection degradation, your containerized server must run a unified, stateless HTTP transport layer instead of streaming Server-Sent Events (SSE).

### Step A: The Start Command
In your Render Web Service settings under **Build & Deploy**, swap out your default launch arguments. Force the application executable to run its native stateless HTTP configuration. 

*(Note: The command below uses `\` to break the line so it remains fully visible on narrow screens, with no trailing backslash on the final argument)*:

```bash
/app/.venv/bin/intervals-icu-mcp \
  --transport streamable-http \
  --port 10000 \
  --host 0.0.0.0
```

### Step B: Production Environment Variables
Navigate to **Settings** -> **Environment Variables** on Render. Inject the following parameters to eliminate internal state/redirect tracking loops and provide platform access:

| Key | Value / Format | Architectural Purpose |
| :--- | :--- | :--- |
| `FASTMCP_STATELESS_HTTP` | `true` | Forces the FastMCP framework to process requests via individual, stateless HTTP transactions. |
| `FASTMCP_PATH` | `/` | Aligns the underlying server mounting layout straight to the base directory. |
| `INTERVALS_ICU_ATHLETE_ID` | `your_athlete_id` | Identifies your profile to the underlying application engine during API loops. |
| `INTERVALS_ICU_API_KEY` | `your_generated_key` | Authenticates backend calls passing through your Render proxy. |

Click **Save Changes**. Render will automatically trigger a clean rebuild and redeploy your container.

---

## 3. OpenRouter: API Key Provisioning

Because Joey acts as a consumer interface, it passes prompt queries to a hosted language model engine. OpenRouter serves as the LLM aggregator, handling context execution and tool-calling logic.

1. Go to `https://openrouter.ai` and sign into your developer console.
2. Navigate to **Keys** -> **Create Key**.
3. Name the token contextually (e.g., `Joey-MCP-Link`) and generate it. Copy the string immediately. Ensure your account balance contains credits to handle tool-calling inference loops.

---

## 4. Joey iOS: Endpoint Consolidation

With the cloud infrastructure running a unified HTTP transport layer, input the final routing configuration parameters directly into your iOS mobile client.

### Step A: LLM Setup
In the Joey settings panel, paste your newly generated OpenRouter API token into the provider configurations and select an intelligent, tool-capable model (such as Anthropic's Claude 3.5 Sonnet or OpenAI's GPT-4o).

### Step B: Server Integration
Tap **MCP Servers** -> **Add Server** and configure the endpoint fields exactly as specified below:

* **URL:** `https://intervals-icu-mcp-2j3k.onrender.com/mcp` *(Note: Do not include an additional trailing slash at the absolute end of the URL string).*
* **Custom Headers:**
  * **Key:** `Authorization`
  * **Value:** `Bearer your_chosen_mcp_access_token`

---

## Connection Verification

When Joey saves the configuration, it initiates a single `POST` handshake request to the server. The proxy validates the `Authorization` header, maps the payload to the stateless engine, and returns a successful connection status in your Render logs:

```text
INFO:     31.154.8.117:0 - "POST /mcp HTTP/1.1" 200 OK
```

The status indicator inside Joey will illuminate green, confirming that the LLM now has full visibility of your custom Intervals.icu tools.




