# TaxisBI – Domain & Proxy Architecture (Cloudflare + GitHub Pages)

_Last updated: YYYY-MM-DD_

## Canonical repos
- **Website (marketing):** `TaxisBI/website` → `www.taxisbi.com`
- **Docs (developer):** `TaxisBI/docs` → `docs.taxisbi.dev`
- **Org health:** `TaxisBI/.github` (issue templates, CODEOWNERS)

> Personal duplicates (e.g., `chrismaclean-taxisbi/website`) were archived or transferred to the org.

---

## DNS (Cloudflare)

### taxisbi.com
| Type | Name | Value | Proxy |
|---|---|---|---|
| A | @ | 192.0.2.1 | Proxied |
| CNAME | www | taxisbi.github.io | Proxied |
| MX | @ | mail.protonmail.ch (10) | DNS only |
| MX | @ | mailsec.protonmail.ch (20) | DNS only |
| TXT | @ | v=spf1 include:_spf.protonmail.ch ~all | DNS only |
| TXT | _dmarc | v=DMARC1; p=quarantine; rua=mailto:dmarc@taxisbi.com | DNS only |
| CNAME | protonmail._domainkey | protonmail.domainkey… | DNS only |
| CNAME | pmg._domainkey | pmg.domainkey… | DNS only |
| CNAME | pm-3._domainkey | pm-3.domainkey… | DNS only |

### taxisbi.dev
| Type | Name | Value | Proxy |
|---|---|---|---|
| A | @ | 192.0.2.1 | Proxied |
| CNAME | docs | taxisbi.github.io | Proxied |
| (Optional MX/TXT) | match `.com` | DNS only |

### taxisbi.io
| Type | Name | Value | Proxy |
|---|---|---|---|
| A | @ | 192.0.2.1 | Proxied |
| CNAME | www | taxisbi.io | Proxied |

---

## Redirect rules (Cloudflare → Rules → Redirect Rules)

1. **Apex → www (.com)**
   - **Expression:** `http.host eq "taxisbi.com"`
   - **Type:** Dynamic  
   - **URL:** `concat("https://www.taxisbi.com", http.request.uri.path)`
   - **Status:** 301, **Preserve query string**

2. **.io → .com**
   - **Expression:** `http.host contains "taxisbi.io"`
   - **Type:** Dynamic  
   - **URL:** `concat("https://taxisbi.com", http.request.uri.path)`
   - **Status:** 301, **Preserve query string**

3. **Apex .dev → docs**
   - **Expression:** `http.host eq "taxisbi.dev"`
   - **Type:** Dynamic  
   - **URL:** `concat("https://docs.taxisbi.dev", http.request.uri.path)`
   - **Status:** 301, **Preserve query string**

---

## Security headers (Cloudflare → Rules → Transform Rules → Modify Response Header)

Applied to **.com** and **.dev** (optional HSTS only on .io):

