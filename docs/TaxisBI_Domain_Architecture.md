# **TaxisBI – Domain & Proxy Architecture (Cloudflare + GitHub Pages)**

_Last updated: 2025-10-24_

---

## 🧩 Canonical Repositories

| Purpose | Repository | Domain |
|----------|-------------|--------|
| Marketing / Main Site | `TaxisBI/website` | www.taxisbi.com |
| Developer Documentation | `TaxisBI/docs` | docs.taxisbi.dev |
| Org-level templates & config | `TaxisBI/.github` | — |

> Personal duplicates (e.g., `chrismaclean-taxisbi/website`) have been archived or transferred to the `TaxisBI` organization.

---

## 🌐 DNS Configuration (Cloudflare)

### taxisbi.com
| Type | Name | Value | Proxy |
|------|------|--------|-------|
| A | @ | 192.0.2.1 | 🟠 Proxied |
| CNAME | www | taxisbi.github.io | 🟠 Proxied |
| MX | @ | mail.protonmail.ch (10) | 🔘 DNS only |
| MX | @ | mailsec.protonmail.ch (20) | 🔘 DNS only |
| TXT | @ | v=spf1 include:_spf.protonmail.ch ~all | 🔘 DNS only |
| TXT | _dmarc | v=DMARC1; p=quarantine; rua=mailto:dmarc@taxisbi.com | 🔘 DNS only |
| CNAME | protonmail._domainkey | protonmail.domainkey.protonmail.ch | 🔘 DNS only |
| CNAME | pmg._domainkey | pmg.domainkey.protonmail.ch | 🔘 DNS only |
| CNAME | pm-3._domainkey | pm-3.domainkey.protonmail.ch | 🔘 DNS only |

---

### taxisbi.dev
| Type | Name | Value | Proxy |
|------|------|--------|-------|
| A | @ | 192.0.2.1 | 🟠 Proxied |
| CNAME | docs | taxisbi.github.io | 🟠 Proxied |
| MX | @ | mail.protonmail.ch (10) | 🔘 DNS only |
| MX | @ | mailsec.protonmail.ch (20) | 🔘 DNS only |

---

### taxisbi.io
| Type | Name | Value | Proxy |
|------|------|--------|-------|
| A | @ | 192.0.2.1 | 🟠 Proxied |
| CNAME | www | taxisbi.io | 🟠 Proxied |
| MX | @ | mail.protonmail.ch (10) | 🔘 DNS only |
| MX | @ | mailsec.protonmail.ch (20) | 🔘 DNS only |

---

## 🔁 Redirect Rules (Cloudflare → Rules → Redirect Rules)

### 1️⃣ **Apex → www (.com)**
```
Expression:  http.host eq "taxisbi.com"
Type:        Dynamic
URL:         concat("https://www.taxisbi.com", http.request.uri.path)
Status:      301
Preserve query string: ✅
```

### 2️⃣ **.io → .com**
```
Expression:  http.host contains "taxisbi.io"
Type:        Dynamic
URL:         concat("https://taxisbi.com", http.request.uri.path)
Status:      301
Preserve query string: ✅
```

### 3️⃣ **Apex .dev → docs**
```
Expression:  http.host eq "taxisbi.dev"
Type:        Dynamic
URL:         concat("https://docs.taxisbi.dev", http.request.uri.path)
Status:      301
Preserve query string: ✅
```

---

## 🔒 Security Headers (Cloudflare → Transform Rules → Modify Response Header)

Applied to **.com** and **.dev** (optional HSTS only on .io):

```
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
X-Frame-Options: SAMEORIGIN
Content-Security-Policy: default-src 'self'; img-src 'self' https: data:; script-src 'self' https:; style-src 'self' https: 'unsafe-inline'; font-src 'self' https:; connect-src 'self' https:;
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
```

**Edge Settings**
- SSL/TLS mode: **Full (Strict)**
- Always Use HTTPS: **On**
- Automatic HTTPS Rewrites: **On**

---

## ⚙️ GitHub Pages Settings

### `TaxisBI/website`
- **Source:** Deploy from branch → `main / root`
- **Custom domain:** `www.taxisbi.com`
- **Enforce HTTPS:** ✅

### `TaxisBI/docs`
- **Source:** Deploy from branch → `main / root`
- **Custom domain:** `docs.taxisbi.dev`
- **Enforce HTTPS:** ✅

---

## 📬 Email (ProtonMail)

| Domain | MX 10 | MX 20 | Notes |
|---------|--------|--------|--------|
| taxisbi.com | mail.protonmail.ch | mailsec.protonmail.ch | Primary |
| taxisbi.dev | mail.protonmail.ch | mailsec.protonmail.ch | Mirrored |
| taxisbi.io | mail.protonmail.ch | mailsec.protonmail.ch | Optional |

---

## ✅ Verification Checklist

| Test | Expected Behavior |
|------|--------------------|
| http://taxisbi.com | → https://www.taxisbi.com |
| https://taxisbi.com | → https://www.taxisbi.com |
| http://taxisbi.io | → https://taxisbi.com |
| http://www.taxisbi.io | → https://taxisbi.com |
| http://taxisbi.dev | → https://docs.taxisbi.dev |
| https://docs.taxisbi.dev | Loads via Cloudflare |
| All headers present | Check via DevTools → Network → Headers |

---

### Notes
- Cloudflare serves as the **authoritative DNS and CDN layer** for all domains.  
- GitHub Pages provides **static hosting** for the website and documentation.  
- ProtonMail manages all email routing.  
- Future additions (e.g., `studio.taxisbi.dev`, `api.taxisbi.io`) can follow the same proxy + redirect pattern.
