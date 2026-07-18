## Role & Mission
You are an expert Network Configuration Engineer specializing in the Shadowrocket (iOS) ecosystem. Your primary mission is to maintain, optimize, and expand the `ShadowRocketSimpleConfig` repository while ensuring 100% syntax validity and operational efficiency.

---

## Repository Architecture
- `lists/*.list` — Rule set files, one per category. Each file contains ONLY rules (no comments, no policy column).
- `baseline.conf` — Master Shadowrocket config: global settings, DNS overrides, and `RULE-SET` imports that bind each list to a policy.
- `README.md` — User-facing documentation. Update only when the public structure or usage changes.
- `AGENTS.md` — This file. Engineer-facing maintenance guide.

The policy for each list is set ONCE in `baseline.conf` via `RULE-SET, ...list,<POLICY>`. Individual rules inside `.list` files do NOT carry a policy column.

### Config ↔ Lists Consistency (STRICT)
- **Every `lists/*.list` file MUST be imported in `baseline.conf`** under `[Rule]` via a `RULE-SET` line. No orphan files allowed — if a `.list` file exists, it must have a matching import.
- **Every `.list` import MUST specify a policy in the third column.** Valid policies are listed below. A proxy group / subscription / node name may also be used as the policy.
- **No import may reference a non-existent `lists/*.list` file.** Every `RULE-SET` line must point to a file that actually exists.
- After any modification, verify 1:1 correspondence between `lists/*.list` filenames and `RULE-SET` imports in `baseline.conf` (no missing, no extra, no duplicate imports).

---

## [Rule] — Routing Rules

### Rule Types

| Type | Format | Description |
|------|--------|-------------|
| DOMAIN-SUFFIX | `DOMAIN-SUFFIX,example.com,POLICY` | Domain suffix match (`a.example.com`, `a.b.example.com`) |
| DOMAIN-KEYWORD | `DOMAIN-KEYWORD,exa,POLICY` | Keyword found anywhere in the domain |
| DOMAIN-WILDCARD | `DOMAIN-WILDCARD,a*c.example*.com,POLICY` | Wildcards `{*, ?}` |
| DOMAIN | `DOMAIN,www.example.com,POLICY` | Full domain, exact match only |
| USER-AGENT | `USER-AGENT,MicroMessenger*,POLICY` | User-Agent with `*` support |
| URL-REGEX | `URL-REGEX,^https?://.+/item.+,POLICY` | Regular expression on the URL |
| IP-CIDR | `IP-CIDR,192.168.1.0/24,POLICY` | IPv4/IPv6 range |
| IP-CIDR (no-resolve) | `IP-CIDR,172.16.0.0/12,POLICY,no-resolve` | IP rule without DNS lookup for domains |
| IP-ASN | `IP-ASN,56040,POLICY` | Autonomous System Number |
| RULE-SET | `RULE-SET,URL,POLICY` | Rule set with typed rules |
| DOMAIN-SET | `DOMAIN-SET,URL,POLICY` | Domain set without rule type |
| SCRIPT | `SCRIPT,script_name,POLICY` | Script name (rule type) |
| DST-PORT | `DST-PORT,443,POLICY` | Destination port |
| GEOIP | `GEOIP,CN,POLICY` | GeoIP database |
| FINAL | `FINAL,POLICY` | Default strategy |
| AND | `AND,((DOMAIN,www.example.com),(DST-PORT,123)),POLICY` | Logical AND |
| NOT | `NOT,((DST-PORT,123)),POLICY` | Logical NOT |
| OR | `OR,((DST-PORT,123),(DST-PORT,456)),POLICY` | Logical OR |
| PROTOCOL | `PROTOCOL,UDP` | Protocol (UDP, TCP) |

### Policies

| Policy | Description |
|--------|-------------|
| PROXY | Via the proxy server |
| DIRECT | Directly, without proxy |
| TAILSCALE | Via the Tailscale tunnel |
| REJECT | HTTP 404, no content |
| REJECT-DICT | HTTP 200 and empty JSON object `{}` |
| REJECT-ARRAY | HTTP 200 and empty JSON array `[]` |
| REJECT-200 | HTTP 200, no content |
| REJECT-IMG | HTTP 200 and a 1px GIF |
| REJECT-TINYGIF | HTTP 200 and a 1px GIF |
| REJECT-DROP | Drop the IP packet |
| REJECT-NO-DROP | ICMP "port unreachable" |

The policy may also be a proxy group name, subscription name, group name, or node name.

### Rule Priority
1. Module rules take precedence over configuration rules.
2. Rules are evaluated top to bottom.
3. Domain rules (DOMAIN, DOMAIN-SUFFIX, DOMAIN-KEYWORD, DOMAIN-WILDCARD) take precedence over IP rules (IP-CIDR, IP-ASN, GEOIP).
4. When DOMAIN, DOMAIN-SUFFIX, DOMAIN-WILDCARD, and DOMAIN-KEYWORD all match, only one type is applied.

---

## Technical Directives (STRICT)

### 1. Rule Optimization & Logic
- **eTLD+1 Aggregation:** Always prefer broad rules over specific ones. If multiple subdomains of `example.com` are present, replace them with a single `DOMAIN-SUFFIX,example.com`. Use `DOMAIN` (exact) only for known CDN hostnames that do not follow the suffix pattern.
- **Deduplication:** Before adding any rule, search across all `.list` files to ensure it does not already exist. A domain MUST NOT appear in more than one `.list` file.
- **Sorting:** Rules within a list must be sorted by VALUE (case-insensitive A→Z). A single blank line may be used as a visual separator, but no more than one consecutive blank line is allowed.
- **Case:** All rule types (`DOMAIN-SUFFIX`, `IP-CIDR`, etc.) MUST be **UPPERCASE**.

### 2. File Placement Logic
- `ai.list` — LLMs, AI APIs, AI-related CDNs (ChatGPT, Claude, Gemini, Copilot, Midjourney, etc.).
- `games.list` — PlayStation, Xbox, gaming publishers, game launchers, mobile game studios (Supercell, Gameloft), related CDNs.
- `fitness.list` — Workout, health, nutrition tracking.
- `music.list` — Music streaming services, artists, labels, and related CDNs.
- `video.list` — Video streaming platforms, movie/TV services, and related CDNs.
- `nevamessenger.list` — Neva Messenger and related services.
- `redtube.list` — RedTube and related adult content domains.
- `ruads.list` — Russian ad/tracker domains to be REJECTed.
- `rubanking.list` — Russian banks, fintech, payment systems, acquiring, gift cards, leasing.
- `ruipchecker.list` — IP address check services used by Russian mobile apps.
- `rudirect.list` — Russian local services that must bypass proxy (gov, retail, telecom, media, local CDNs, .ru/.рф).
- `zetaservices.list` — Zeta Services and related domains.
- `main.list` — Everything else: social media, media, news, productivity, tools, dev platforms, hardware vendors.

### 3. Syntax Standards
- **Inside `.list` files:** rules are 2-column — `TYPE,VALUE`:
  ```
  DOMAIN-SUFFIX,google.com
  IP-CIDR,157.240.0.0/17,no-resolve
  USER-AGENT,Telegram*
  ```
  `,no-resolve` modifier is **required** after all IP-CIDR / IP-CIDR6 / IP-ASN rules to skip DNS lookups.
- **Inside `baseline.conf`:** rules are 3-column — `TYPE,VALUE,POLICY` (PROXY / DIRECT / REJECT).
- **No comments** (`#` lines) in `.list` files. Section grouping is expressed by sort order and (sparingly) single blank lines.
- `rudirect.list`, `rubanking.list`, and `ruipchecker.list` MUST stay sorted and free of duplicates with each other and with `meta.list` and `main.list`.

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
5.  **Aggregate:** If a new `DOMAIN-SUFFIX` makes a more specific `DOMAIN` (or narrower suffix) redundant, remove the redundant entry. If a `DOMAIN` rule exists for the same value as an existing `DOMAIN-SUFFIX`, the `DOMAIN` is fully redundant — remove it.
6.  **Validate:** Sanity check — no comments, no duplicate values, all types uppercase, all IP rules have `,no-resolve`, each `.list` parses without errors.
