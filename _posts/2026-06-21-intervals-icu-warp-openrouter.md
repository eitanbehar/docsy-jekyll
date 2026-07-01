---
title:  "Using Local AI Agents to Analyze Personal Fitness Data"
date:   2026-06-21
badges:
 - type: info
   tag: terminal
 - type: info
   tag: ai
tags:
  - ai
  - intervals.cu
  - warp terminal
  - openrouter
---

# 💻 Using Local AI Agents to Analyze Personal Fitness Data

Model Context Protocol (MCP) and AI agents are frequently discussed in the context of enterprise software development. However, these tools are also highly effective for personal hobbies and data analysis.

I wanted a way to query my cycling and recovery metrics securely without relying on premium AI subscriptions. By combining open-source tools and custom inference endpoints, I built a local training assistant for free.

<!--more-->

## The Setup

1. **The Data Source: Intervals.icu** Tracks fitness metrics like training load, fatigue, and form (CTL/ATL/TSB) and provides an open API.
   
2. **The Terminal: Warp** Warp’s AI Agent natively supports MCP. I configured a local MCP server (`intervals-icu-mcp`) to let the terminal communicate directly with the API.
   
3. **The LLM: OpenRouter** To bypass standard platform credit limits, I used Warp’s "Bring Your Own Inference" capability to connect to OpenRouter, routing queries through free models like Qwen or Llama.

---

## The Workflow

I can open the AI panel in my terminal and ask:  
> *"Analyze my training load from this week. Based on my current injury recovery, should I take a rest day tomorrow?"*

The agent fetches the real-time data via the local MCP server, processes it through OpenRouter, and returns a data-driven recovery recommendation in seconds.

Using local tools and open APIs makes it straightforward to build custom, private data pipelines for any hobby.

***

#AI #ModelContextProtocol #DataScience #WarpTerminal #OpenRouter #FitnessTech