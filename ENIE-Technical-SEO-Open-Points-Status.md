# en-IE — Technical SEO Open Points (status)

Status snapshot as reviewed with Niels. Legend: 🟢 Done · 🟡 In progress / scoped · 🔴 To do · 🔵 V2 (not in V1) · ⚪ Needs verification/decision.

| # | Ref | Open point | Status | Notes |
|---|---|---|---|---|
| 1 | SSR-P0-1 | Prices not in SSR HTML (old category template + homepage) | 🟡 In progress | Scoped |
| 2 | SSR-P0-1 | `ItemList` schema missing on old-template category pages | 🔴 To do | |
| 3 | SSR-P0-2 | Listing pages SSR only first ~15 products | ⚪ Decision | Intent: full **first page** must render. Open Q: is 15 a hard cap or the pagination page size? → verify + set page size |
| 4 | SSR-P1-1 | Trustpilot company rating server-rendered + schema | 🔴 To do | See #17 — aggregate removed; render company Trustpilot |
| 5 | SSR-V2-1 | Full template convergence (old → new listing template) | ⚪ Clarify | See explanation below |
| 6 | SSR-V2-2 | Crawlable / "earned" filter pages | 🔵 V2 | Not part of V1 |
| 7 | OTH-P0-1 | Title & `og:title` truncation → render Contentful verbatim | 🟡 In progress | Scoped |
| 8 | OTH-P0-2 | Placeholder `{{ }}` token leak (body + JSON-LD) | 🟡 In progress | Scoped |
| 9 | OTH-P0-3 | Robots / sitemap / noindex + canonical correctness | 🟡 Verify | Staging scrape done — robots.txt good (AI bots allowed; only funnel/account disallowed). **2 issues:** robots `Sitemap:` points to `www.helloprint.com/sitemap.xml` (wrong domain); **no `<lastmod>`** in any sitemap. Sitemap structure clean (per-locale/per-type, no param URLs). See findings below |
| 10 | OTH-P0-4 | hreflang reciprocity / return-tags | 🟡 In progress | Being worked on, on staging |
| 11 | OTH-P1-1 | Meta description localisation (Ireland) | 🟢 Done | |
| 12 | OTH-V2-1 | Responsive images `srcset`/`sizes` (mobile LCP) | 🟡 In progress | Scoped |
| 13 | OTH-V2-2 | Image `alt` hygiene | 🔵 V2 | Not part of V1 |
| 14 | — | Canonical deeper check (normalisation, params) | 🟢 Looks good | **Combined into #9.** Sitemap URLs all lowercase, no-www, no params; pages self-canonical + params→base. Confirm on production domain |
| 15 | — | Parameter/hyphen URLs → 301 vs canonical | 🟡 Flag | Working but **very slow → flag to Frederico** |
| 16 | — | Product/Offer/Breadcrumb/ItemList full field validation | ⚪ Clarify | Scope explained below |
| 17 | — | Star ratings on product/category | 🟢 Done | AggregateRating removed; company Trustpilot rendering is the accepted approach |
| 18 | — | Breadcrumb on category pages | 🟡 In progress | **Prioritise now** |
| 19 | — | `CollectionPage` removal | 🟢 Done | Confirmed |
| 20 | — | Redirects & 404s | 🔴 Post-launch | Check after launch. Helpcenter scoped; Blog by Frederico |
| 21 | — | Sitemap completeness (all URLs, lastmod, regen) | ⚪ Recheck | Mostly confirmed, BUT staging scrape found **no `<lastmod>`** (plan requires it) and `products_1` had only 11 URLs — sanity-check coverage |
| 22 | — | Core Web Vitals (LCP/CLS/INP) | 🟡 Measure | Could not measure in remote session (automation tab records no paint/LCP; PSI API didn't return). Use Lighthouse/PSI directly — was reportedly done this morning |

## Clarifications

**#5 — Full template convergence (what it means):** two category templates exist in production. The *new* listing template (`/printed-water-bottles`, `/ballpoint-pens`) already server-renders prices + `ItemList`; the *old* one (`/brochure-and-booklet-printing`, `/promotional-printing`) does not. For V1 we only **backport** SSR prices to the old template (ticket #1). "Convergence" (V2) means fully moving the old pages onto the new template so there's a single template — cleaner long-term, but riskier (it can disturb the richer SEO copy), hence deferred.

**#16 — Schema field validation (what's needed):** run each rendered JSON-LD type through Google's Rich Results Test / Schema Markup Validator and confirm zero errors and that required + recommended fields are populated:
- `Product`: name, image, description, brand, sku/mpn.
- `Offer`/`AggregateOffer`: price, priceCurrency (EUR), availability, priceValidUntil, url.
- `BreadcrumbList`: correct position ordering, item URLs.
- `ItemList`: item, position, url per entry (and no `{{ }}` tokens — links to #8).
Deliverable = a per-template validation pass with zero errors.

**#3 — page size:** on staging the SSR'd set matched the `ItemList` (15 items) and the "Show more" batch, which points to a pagination **page size of ~15**, not an arbitrary cap. If the intent is "full first page renders", the decision is simply what that page size should be. I can confirm by triggering "Show more" and checking whether the next batch is the same size.

## Live verification findings (remote session, staging)

**robots.txt (`/robots.txt`, 200):**
- Explicitly allows AI/LLM crawlers: GPTBot, ChatGPT-User, OAI-SearchBot, ClaudeBot, anthropic-ai, Claude-Web, PerplexityBot, Google-Extended, CCBot, Applebot-Extended, Bytespider. ✅ (strong GEO signal)
- `User-agent: *` → `Allow: /`; disallows only `/*/cart`, `/*/checkout/`, `/*/account/`, `/*/login`, `/*/auth/`. ✅
- Comment states parameter/query URLs stay crawlable (folded into canonical) — consistent with the canonical→base behaviour.
- ⚠️ **`Sitemap: https://www.helloprint.com/sitemap.xml`** — points at a different domain. The new-site sitemaps are at `/sitemap.xml` (index) and `/en-ie/sitemap.xml`. Verify/fix the declared sitemap for the launch domain.

**Sitemaps:**
- `/sitemap.xml` → index of 5 locale sitemaps (en-gb, en-ie, nl-nl, de-de, …).
- `/en-ie/sitemap.xml` → index of 8 child sitemaps (products, collections, pages, blog_articles, …). ✅ good structure.
- `/en-ie/sitemap_products_1.xml` → 11 URLs · `/en-ie/sitemap_collections_1.xml` → 205 URLs. All lowercase, no `www`, **no parameter URLs**. ✅
- 🔴 **No `<lastmod>`** in the index or child sitemaps — the go-live plan requires it.
- ⚪ `products_1` had only 11 product URLs — low; sanity-check product coverage.
- ⚪ Non-existent sitemap paths (e.g. `/sitemap_index.xml`) soft-return 200 HTML instead of 404.

**Canonical (#14):** sitemap URLs are canonical-clean (lowercase, no-www, no-params); pages self-canonical and param URLs canonicalise to base. Re-confirm on the production domain (staging URLs currently use `v4.staging.helloprint.dev`).

**Core Web Vitals (#22):** not measurable from the remote session — the automation tab recorded no paint/LCP/layout-shift entries, and the PageSpeed Insights API did not return via fetch. Use Lighthouse or PSI directly (throttled, cold cache) or CrUX field data.
