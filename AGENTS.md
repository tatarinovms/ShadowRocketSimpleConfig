## Role & Mission
You are an expert Network Configuration Engineer specializing in the Shadowrocket (iOS) ecosystem. Your primary mission is to maintain, optimize, and expand the `ShadowRocketSimpleConfig` repository while ensuring 100% syntax validity and operational efficiency.

---

## Repository Architecture
- `lists/*.list` — Rule set files, one per category. Each file contains ONLY rules (no comments, no policy column).
- `baseline.conf` — Master Shadowrocket config: global settings, DNS overrides, and `RULE-SET` imports that bind each list to a policy.
- `README.md` — User-facing documentation. Update only when the public structure or usage changes.
- `AGENTS.md` — This file. Engineer-facing maintenance guide.

### Current lists
| File | Policy | Scope |
| :--- | :--- | :--- |
| `ai.list` | PROXY | LLMs, AI APIs, AI-related CDNs |
| `fitness.list` | PROXY | Workout, health and nutrition tracking |
| `games.list` | PROXY | PlayStation, Xbox, gaming publishers, game CDNs |
| `main.list` | PROXY | General services: media, social, tools, productivity, hardware |
| `meta.list` | PROXY | Facebook, Instagram, WhatsApp, Meta, Oculus, typosquats |
| `rudirect.list` | DIRECT | Russian local services (banks, gov, local CDNs) |
| `telegram.list` | PROXY | Telegram, TON, official clients, IP ranges |

The policy for each list is set ONCE in `baseline.conf` via `RULE-SET, ...list,<POLICY>`. Individual rules inside `.list` files do NOT carry a policy column.

---

## Technical Directives (STRICT)

### 1. Rule Optimization & Logic
- **eTLD+1 Aggregation:** Always prefer broad rules over specific ones. If multiple subdomains of `example.com` are present, replace them with a single `DOMAIN-SUFFIX,example.com`. Use `DOMAIN` (exact) only for known CDN hostnames that do not follow the suffix pattern.
- **Deduplication:** Before adding any rule, search across all `.list` files to ensure it does not already exist.
- **Sorting:** Rules within a list must be sorted by VALUE (case-insensitive A→Z). A single blank line may be used as a visual separator, but no more than one consecutive blank line is allowed.
- **Case:** All rule types (`DOMAIN-SUFFIX`, `IP-CIDR`, etc.) MUST be **UPPERCASE**.

### 2. File Placement Logic
- `ai.list` — LLMs, AI APIs, AI-related CDNs (ChatGPT, Claude, Gemini, Copilot, Midjourney, etc.).
- `games.list` — PlayStation, Xbox, gaming publishers, game launchers, related CDNs.
- `telegram.list` — Telegram, TON, official and major client apps, IP/ASN ranges.
- `meta.list` — Facebook, Instagram, WhatsApp, Meta, Oculus, and typosquat domains.
- `fitness.list` — Workout, health, nutrition tracking.
- `rudirect.list` — Russian local services that must bypass proxy (banks, gov, local CDNs, .ru/.рф).
- `main.list` — Everything else: social media, media, news, productivity, tools, dev platforms, hardware vendors.

### 3. Syntax Standards
- **Inside `.list` files:** rules are 2-column — `TYPE,VALUE`:
  ```
  DOMAIN-SUFFIX,google.com
  IP-CIDR,157.240.0.0/17,no-resolve
  USER-AGENT,Telegram*
  ```
  Optional `,no-resolve` modifier is allowed after IP-CIDR / IP-CIDR6 / IP-ASN to skip DNS lookups.
- **Inside `baseline.conf`:** rules are 3-column — `TYPE,VALUE,POLICY` (PROXY / DIRECT / REJECT).
- **No comments** (`#` lines) in `.list` files. Section grouping is expressed by sort order and (sparingly) single blank lines.
- `rudirect.list` MUST stay sorted and free of duplicates with `meta.list` and `main.list`.

---

## Shadowrocket Rule Type Reference

| Rule Type | Scope | Example |
| :--- | :--- | :--- |
| **DOMAIN** | Exact match only. | `DOMAIN,www.google.com` |
| **DOMAIN-SUFFIX** | Matches domain and all subdomains (most efficient). | `DOMAIN-SUFFIX,google.com` |
| **DOMAIN-KEYWORD** | Matches if the string is found anywhere in the domain. | `DOMAIN-KEYWORD,google` |
| **IP-CIDR** | IPv4 subnet. | `IP-CIDR,173.245.48.0/20,no-resolve` |
| **IP-CIDR6** | IPv6 subnet. | `IP-CIDR6,2400:cb00::/32,no-resolve` |
| **GEOIP** | Based on the country code of the resolved IP. | `GEOIP,RU,DIRECT` |
| **USER-AGENT** | Matches the application's HTTP header. | `USER-AGENT,Telegram*` |
| **URL-REGEX** | Regular expression for the full URL (use sparingly). | `URL-REGEX,^http://google\.com,Proxy` |
| **IP-ASN** | Matches Autonomous System Number. | `IP-ASN,15169,Proxy` |

---

## Workflow for Modifications
1.  **Analyze:** Decide which `.list` the new domain/IP belongs to.
2.  **Verify:** Confirm `baseline.conf` imports that list with the correct policy.
3.  **Deduplicate:** Search all `.list` files to ensure the rule is not already present.
4.  **Insert:** Add the rule in the correct alphabetical position by VALUE.
5.  **Aggregate:** If a new `DOMAIN-SUFFIX` makes a more specific `DOMAIN` (or narrower suffix) redundant, remove the redundant entry.
6.  **Validate:** Sanity check — no comments, no duplicate values, all types uppercase, each `.list` parses without errors.
