## Mission
Maintain ShadowRocket (iOS) routing rules. Goal: 100% valid syntax, no duplicates, correct policy binding.

## Layout
- `baseline.conf` — master config. Holds `[General]` settings, DNS, and `[Rule]` section.
- `lists/*.list` — rule sets, one file per category. Contain ONLY `TYPE,VALUE` lines (no policy column, no comments).
- `README.md` — user-facing. Edit only when public structure/usage changes.

## Config ↔ Lists binding (STRICT, easy to break)
- Policy for each list is set **once** in `baseline.conf` via a `RULE-SET,<raw-url>,<POLICY>` line. The `.list` file itself carries no policy.
- Imports use full raw URLs (`raw.githubusercontent.com/.../lists/X.list`), NOT local paths.
- 1:1 rule: every `lists/*.list` must be imported; every `RULE-SET` import must point to an existing file. After any edit, confirm no missing / extra / duplicate imports. Current lists (13): ai, main, zetaservices, games, nevamessenger, fitness, music, video, redtube, ruads, ruipchecker, rubanking, rudirect.
- Rule order in `[Rule]` matters: `ruads` (REJECT) first, then PROXY lists, then `GEOIP,RU,DIRECT`, then `FINAL,DIRECT`. Keep ruads before mass PROXY lists.

## Per-list policy (source of truth = baseline.conf)
- PROXY: ai, main, zetaservices, games, nevamessenger, fitness, music, video, redtube
- DIRECT (bypass proxy): rudirect, rubanking, ruipchecker
- REJECT: ruads

## `.list` syntax rules
- Format: `TYPE,VALUE` (2 columns). IP rules need trailing `,no-resolve` (`IP-CIDR`, `IP-CIDR6`, `IP-ASN`).
- Types UPPERCASE (`DOMAIN-SUFFIX`, `DOMAIN`, `DOMAIN-KEYWORD`, `DOMAIN-WILDCARD`, `IP-CIDR`, `IP-CIDR6`, `IP-ASN`, `USER-AGENT`, `URL-REGEX`).
- No `#` comments. Max one blank line as separator.
- Sort by VALUE, case-insensitive A→Z.
- A domain MUST appear in exactly ONE list file. No duplicates across files.

## Placement guide (which list for new rule)
- `ai` LLMs/AI APIs/CDNs · `games` PlayStation/Xbox/launchers · `fitness` health · `music` streaming · `video` streaming/TV · `nevamessenger` Neva messenger · `redtube` RedTube/RedMusic · `zetaservices` Zeta/VK-like · `main` everything else (social, news, dev, tools).
- `rudirect` Russian local services (gov, retail, telecom, .ru/.рф) · `rubanking` banks/fintech/payments · `ruipchecker` IP-check services · `ruads` Russian ad/tracker → REJECT.

## Workflow for adding rules
1. Pick the list by category above.
2. Confirm it is imported in `baseline.conf` with the right policy.
3. Dedupe across ALL lists first.
4. Insert at correct alphabetical position.
5. Aggregate: a new `DOMAIN-SUFFIX,example.com` makes any `DOMAIN,*.example.com` / narrower suffix redundant — delete the redundant entries.
6. Sanity check: no comments, no dup values, uppercase types, IP rules have `,no-resolve`.
