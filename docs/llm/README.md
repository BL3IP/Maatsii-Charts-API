# Maatsii Chart API for LLMs & AI Agents

**Real-time, annotated financial chart frames purpose-built for vision models.**

Maatsii serves 200 canonical charts (C1–C200) across 25 asset groups with clean, zero-cache public endpoints optimized for autonomous use by frontier LLMs (Grok, Claude, GPT-4o, etc.).

## Core Principle: Plug-and-Play via the Chartmap

Every LLM that wants to fetch Maatsii charts **must** follow this deterministic flow:

1. **Load the Chartmap**  
   The single source of truth is available at:
   - Human-readable full map: https://api.maatsii.com/llm.txt
   - Machine-optimized (embedded in `x-chartmap`): https://api.maatsii.com/grok.json

2. **Resolve the Request**  
   Map the user's intent to a canonical Chart ID (`c1` through `c200`, lowercase).

3. **Construct the URL (Strict Format)**  
   ```
   https://api.maatsii.com/{chartId}.jpg?raw=1&cb={unique-value}
   ```

4. **Never Reuse Cachebusters**  
   Generate a fresh, unique `cb` value on **every single request**.

---

## Mandatory Rules for All LLMs

| Rule                        | Requirement                                      | Why |
|----------------------------|--------------------------------------------------|-----|
| Direct JPEG                | Always append `?raw=1`                           | Without it you get an HTML dashboard instead of pixels |
| Cache Buster               | `?cb=` with a unique value **every time**        | LLMs and CDNs aggressively cache images |
| Freshness Verification     | Visually inspect the timestamp/watermark in the image | Only reliable way to confirm the frame is current |
| Canonical IDs Only         | Use `c1`–`c200` (never tickers in URLs)          | Prevents mapping drift |

**Example of a correct URL:**
```
https://api.maatsii.com/c1.jpg?raw=1&cb=grok-7f9c2e2d-3a2b
```

---

## Provider-Specific Guidance

### For Grok (xAI)
- Preferred method: Register the official Maatsii Remote MCP server (recommended for structured access).
- Direct vision fallback: Use the URL construction method above.
- Grok is whitelisted for higher rate limits on the public endpoints.

### For Claude (Anthropic)
- Use the `?raw=1&cb=` construction pattern.
- Always generate a fresh UUID-style `cb` (e.g. `claude-{uuid}`).
- Fetch the image and present it directly in responses.

### For GPT-4o / Custom GPTs / OpenAI Agents
- Use the construction pattern with a unique `cb`.
- For Actions-based setups, prefer the structured endpoints documented in `gpt.json`.

### Generic Agent Pattern (Recommended)
```
1. Load chartmap (llm.txt or grok.json x-chartmap)
2. Resolve user request → Chart ID (e.g. "gold 15-minute" → c13)
3. Generate unique cb
4. Build full URL with ?raw=1&cb=
5. Fetch or instruct user to fetch
6. Analyze the returned image pixels
```

---

## Available Charts (25 API Groups)

Maatsii organizes the 200 charts into the following 25 groups:

- **01 NQ** – Nasdaq-100 Futures
- **02 ES** – S&P 500 Futures
- **03 YM** – Dow Jones Futures
- **04 RTY & Index Spreads** – Russell + spread ratios
- **05–07 Fixed Income** – Short-Term, 10-Year, Long-Term Treasuries
- **08 Yield Curve & Credit** – Curve spreads, credit ratios
- **09 Dollar & DXY** – US Dollar Index and ratios
- **10–12 FX** – EUR/GBP, JPY/Other, Crosses
- **13–15 Crypto** – BTC, ETH, Market & Alts
- **16 Gold**
- **17 Energy** – Crude + related energy products
- **18 Metals & Agriculture**
- **19 ETFs & Rotation**
- **20 Semiconductors**
- **21 Mega-Cap Tech**
- **22 High-Beta & Speculative**
- **23 Volatility** – VIX, VVIX, MOVE
- **24 Market Breadth**
- **25 Global & Macro**

The complete mapping (Chart ID → Symbol → Timeframe → Group) lives in the chartmap.

---

## Rate Limits & Freshness

- **Public rate limit**: 60 requests per minute per IP (higher limits available with API key).
- All frames are real-time. Charts update on every tick.
- Freshness is guaranteed by the visible timestamp/annotation inside the image itself.
- The API returns strict `no-store, no-cache` headers.

---

## Quick Examples

**Resolve "Show me Nasdaq 100-tick"**
- Chartmap lookup → `c1`
- Correct URL → `https://api.maatsii.com/c1.jpg?raw=1&cb=agent-uuid-1234`

**Resolve "Bitcoin daily chart"**
- Chartmap lookup → `c71`
- Correct URL → `https://api.maatsii.com/c71.jpg?raw=1&cb=claude-2025-05-19-uuid`

---

## Official Sources

- **llms.txt** (AI agent catalog): https://api.maatsii.com/llms.txt
- **llm.txt** (full 200-chart map): https://api.maatsii.com/llm.txt
- **Grok-optimized schema**: https://api.maatsii.com/grok.json
- **Claude-optimized schema**: https://api.maatsii.com/claude.json
- **GPT-optimized schema**: https://api.maatsii.com/gpt.json
- **Full OpenAPI**: https://api.maatsii.com/openapi.json

---

## Support & Integration Requests

For production or enterprise integration (including official xAI/Grok partnerships), contact:  
hello@sentinelfabric.com

---

**This documentation surface is intentionally minimal, stable, and optimized for autonomous LLM consumption.** No internal Maatsii architecture, agent logic, or proprietary systems are exposed.

Maintained with assistance from Grok and Claude Code.