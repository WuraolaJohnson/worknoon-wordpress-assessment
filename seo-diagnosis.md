# 🔍 SEO Diagnosis: Worknoon Website Not Indexing After Sitemap Submission
**File:** `seo-diagnosis.md`  
**Author:** Johnson Wuraola | **Assessment:** Worknoon WordPress Developer Assessment  
**Scenario:** A new Worknoon website has been launched and the sitemap submitted to Google Search Console, but the site is not appearing in Google search results.

---

## Overview

Sitemap submission alone does not guarantee indexing. Google must first **discover**, **crawl**, **render**, and then **index** a page before it appears in search results. When this pipeline breaks at any stage, the site remains invisible in search — even after sitemap submission.

This document provides a structured, step-by-step diagnosis for exactly this scenario.

```
Discovery → Crawl → Render → Index → Rank
             ↑
         Broken here? This guide finds it.
```

---

## Step 1: Crawlability Tests

The first question is: **can Google even reach the site?**

### 1.1 Use the URL Inspection Tool in Google Search Console
1. Open [Google Search Console](https://search.google.com/search-console)
2. Paste the homepage URL into the top search bar
3. Click **"Request Indexing"** — note the result message
4. Check the **Coverage** section for the response:

| Message | Meaning |
|---------|---------|
| `URL is on Google` | ✅ Already indexed |
| `URL is not on Google` | ❌ Not yet crawled or blocked |
| `Crawled - currently not indexed` | ⚠️ Crawled but Google chose not to index |
| `Discovered - currently not indexed` | ⚠️ In the queue but not yet crawled |

### 1.2 Test Googlebot's Access Manually
Use the **"Test Live URL"** button in the URL Inspection Tool. This simulates how Googlebot sees the page, including rendered JavaScript.

- ✅ If it shows the page content — crawling is fine; the issue is downstream
- ❌ If it shows a blank page or error — the page has a render/access problem

### 1.3 Fetch and Render with Browser Developer Tools
Simulate a crawler with no JavaScript:
```bash
# Use curl to simulate Googlebot user-agent
curl -A "Googlebot" https://devportfolio.com
```
If the HTML response is empty or returns an error code (403, 500), the server is blocking Googlebot.

### 1.4 Check Server Response Codes
Every page must return HTTP `200 OK`. Use a tool like [httpstatus.io](https://httpstatus.io):

| Status Code | Meaning | Action |
|------------|---------|--------|
| `200 OK` | ✅ Page accessible | Move to next check |
| `301/302` | Redirect — check destination | Ensure destination is indexable |
| `403 Forbidden` | Server blocking bots | Check `.htaccess` and hosting firewall |
| `404 Not Found` | Page does not exist | Fix broken URL |
| `500 Server Error` | Site is crashing | Fix server/plugin error immediately |

---

## Step 2: Canonical Tag Checks

A misconfigured canonical tag is one of the most common causes of pages not indexing — it tells Google to index a *different* URL instead of the one being checked.

### 2.1 Inspect the Canonical Tag in Page Source
Right-click the page → **View Page Source** → search for `canonical`:

```html
<!-- ✅ Correct — points to itself -->
<link rel="canonical" href="https://devportfolio.com/" />

<!-- ❌ Wrong — points to a staging/local URL -->
<link rel="canonical" href="http://localhost/worknoon/" />

<!-- ❌ Wrong — points to HTTP instead of HTTPS -->
<link rel="canonical" href="http://devportfolio.com/" />
```

If the canonical URL is wrong, Google will crawl the Worknoon site but pass all indexing credit to the incorrect URL — which likely doesn't exist publicly.

### 2.2 Common WordPress Canonical Mistakes

| Cause | How to Fix |
|-------|-----------|
| Site URL still set to localhost | **WP Admin → Settings → General** → update both URLs to `https://devportfolio.com` |
| Yoast SEO pointing to wrong URL | **Yoast → Search Appearance** → verify site URL |
| Duplicate canonical from theme | Check `<head>` for multiple `<link rel="canonical">` tags — remove duplicates |
| HTTP/HTTPS mismatch | Force HTTPS via `.htaccess` redirect or hosting SSL settings |

### 2.3 Check for Self-Referencing Canonicals on All Key Pages
Every important page (homepage, about, services, contact) must have a canonical pointing to its own exact URL. Use [Screaming Frog SEO Spider](https://www.screamingfrog.co.uk/seo-spider/) (free up to 500 URLs) to audit all canonicals at once.

---

## Step 3: Robots.txt & Noindex Audit

### 3.1 Check the robots.txt File
Visit: `https://devportfolio.com/robots.txt`

A correct WordPress robots.txt should look like:
```
# Correct — allows all Googlebot access
User-agent: *
Disallow: /wp-admin/
Allow: /wp-admin/admin-ajax.php

Sitemap: https://devportfolio.com/sitemap_index.xml
```

**Dangerous patterns to look for and remove:**

```
# ❌ This blocks Google from the entire site
User-agent: *
Disallow: /

# ❌ This blocks Google from crawling key pages
Disallow: /services/
Disallow: /contact/
```

### 3.2 The Most Common WordPress robots.txt Trap
During development, WordPress has a **"Discourage search engines"** setting. If this was checked during development and not unchecked before launch, the site will be actively blocking Google.

**Fix:**
```
WP Admin → Settings → Reading → 
Uncheck: "Discourage search engines from indexing this site"
→ Save Changes
```

> ⚠️ This is the single most common reason a new WordPress site is not indexing. Always check this first.

### 3.3 Check for Noindex Meta Tags
In the page source, search for:
```html
<!-- ❌ This tells Google explicitly NOT to index this page -->
<meta name="robots" content="noindex, nofollow" />

<!-- ✅ This is correct -->
<meta name="robots" content="index, follow" />
```

In **Yoast SEO**, check each page:
- **Yoast SEO meta box → Advanced tab**
- Ensure "Allow search engines to show this page in search results" is set to **Yes**

### 3.4 Check Yoast SEO Global Noindex Settings
Some Yoast configurations noindex entire content types:
```
Yoast SEO → Search Appearance → Content Types
→ For Pages: "Show pages in search results" → YES ✅
→ For Posts: Check if needed
```

---

## Step 4: Sitemap Structure Issues

### 4.1 Verify the Sitemap is Accessible
Visit the sitemap URL directly in the browser:
```
https://devportfolio.com/sitemap_index.xml
```

**Expected output:** An XML file listing all sub-sitemaps.  
**If you get a 404:** The sitemap hasn't been generated. Go to **Yoast SEO → General → Features → XML Sitemaps → On**.

### 4.2 Verify the Sitemap is Submitted in Search Console
```
Google Search Console → Sitemaps → 
Enter: sitemap_index.xml → Submit
```

Check the **Status column**:

| Status | Meaning | Action |
|--------|---------|--------|
| `Success` | ✅ Sitemap read correctly | Check URLs discovered count |
| `Couldn't fetch` | ❌ Google can't access the file | Check robots.txt and hosting firewall |
| `Has errors` | ❌ XML is malformed | Validate at xml-sitemaps.com |
| `Pending` | ⏳ Not yet processed | Wait 24–48 hours |

### 4.3 Validate Sitemap XML Structure
A valid sitemap entry must follow this format:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <!-- ✅ Must be the canonical HTTPS URL -->
    <loc>https://devportfolio.com/</loc>
    <lastmod>2026-05-02</lastmod>
    <changefreq>monthly</changefreq>
    <priority>1.0</priority>
  </url>
</urlset>
```

**Common sitemap errors to fix:**

| Error | Cause | Fix |
|-------|-------|-----|
| URLs show `http://` not `https://` | SSL not forced | Add HTTPS redirect in `.htaccess` |
| URLs show `localhost` | Site migrated without updating URLs | Run Search & Replace in WP database |
| `noindex` pages in sitemap | Sitemap includes blocked pages | Exclude noindexed pages from sitemap in Yoast |
| Sitemap over 50,000 URLs | Too large | Split into multiple sub-sitemaps |
| Malformed XML | Plugin conflict | Deactivate plugins one by one and re-test |

### 4.4 Fix localhost URLs in the Database (Post-Migration)
If the site was built locally and migrated to a live server, URLs may still contain `localhost`:

```sql
-- Run in phpMyAdmin or via WP-CLI
UPDATE wp_options SET option_value = replace(option_value, 'http://localhost/worknoon', 'https://devportfolio.com') WHERE option_name = 'home' OR option_name = 'siteurl';
UPDATE wp_posts SET post_content = replace(post_content, 'http://localhost/worknoon', 'https://devportfolio.com');
UPDATE wp_postmeta SET meta_value = replace(meta_value, 'http://localhost/worknoon', 'https://devportfolio.com');
```

Or use the **Better Search Replace** plugin for a safer GUI approach.

---

## Step 5: Page Speed as an Indexing Blocker

Page speed does not directly prevent indexing, but **extremely slow or unresponsive pages can cause Googlebot to abandon the crawl** before fully processing the page — effectively treating it as inaccessible.

### 5.1 Core Web Vitals Thresholds
Use [Google PageSpeed Insights](https://pagespeed.web.dev/):

| Metric | Good | Needs Work | Poor (Crawl Risk) |
|--------|------|------------|-------------------|
| LCP (Largest Contentful Paint) | < 2.5s | 2.5–4s | > 4s ❌ |
| FID / INP | < 100ms | 100–300ms | > 300ms ❌ |
| CLS (Cumulative Layout Shift) | < 0.1 | 0.1–0.25 | > 0.25 ❌ |
| TTFB (Time to First Byte) | < 600ms | 600ms–1.8s | > 1.8s ❌ |

### 5.2 Speed Blockers That Affect Googlebot

| Blocker | Symptom | Fix |
|---------|---------|-----|
| No caching | High TTFB on every request | Enable WP Super Cache |
| Uncompressed images | Large LCP value | Install Smush, convert to WebP |
| Render-blocking scripts | Googlebot sees blank page | Defer non-critical JS |
| No server-side compression | Slow HTML delivery | Enable Gzip in `.htaccess` |
| Shared hosting overload | Random timeouts | Upgrade hosting or add CDN |
| Too many plugins | Page size > 3MB | Audit and remove unused plugins |

### 5.3 Enable Gzip Compression (`.htaccess`)
Add to WordPress `.htaccess` if not already present:
```apache
<IfModule mod_deflate.c>
  AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css application/javascript application/json
</IfModule>
```

### 5.4 Check if JavaScript is Required for Content
If the Worknoon site content is only visible after JavaScript executes (e.g., loaded via React or AJAX), Googlebot may not see it:
- Use **URL Inspection → "Test Live URL"** in Search Console
- Click the **"Screenshot"** tab to see what Googlebot actually rendered
- If key content is missing, it needs server-side rendering or static fallback

---

## Step 6: Google Search Console Debugging Steps

Follow this exact sequence in Search Console when the site is not indexing:

### Step 1 — Verify Ownership
```
Search Console → Settings → Ownership verification → Confirm "Verified"
```
If not verified, Google will not process any data for the property.

### Step 2 — Check Coverage Report
```
Search Console → Pages (or Coverage) → Filter by status
```

| Tab | What to Look For |
|-----|-----------------|
| **Error** | Pages returning 404, 500, redirect errors |
| **Valid with warning** | Pages with canonical issues or noindex conflicts |
| **Excluded** | Pages blocked by robots.txt, noindex, or canonical redirect |
| **Valid** | ✅ Properly indexed pages |

> If the homepage appears under **"Excluded → Crawled - currently not indexed"** — Google crawled it but decided not to index it. This usually means thin content, duplicate content, or a quality signal issue.

### Step 3 — Use URL Inspection on Specific Pages
For each key page (homepage, services, contact):
1. Paste URL → Inspect
2. Check: **Indexing allowed? → Yes**
3. Check: **Canonical → User-declared vs. Google-selected** (they must match)
4. Check: **Crawl → Last crawl date** (if never crawled, request indexing)
5. Click **"Test Live URL"** → Confirm content is rendered

### Step 4 — Check for Manual Actions
```
Search Console → Security & Manual Actions → Manual Actions
```
If Google has issued a manual penalty (e.g., for spam or policy violation), the site will not index until the action is resolved and a reconsideration request is submitted.

### Step 5 — Check Security Issues
```
Search Console → Security & Manual Actions → Security Issues
```
Hacked sites or sites flagged for malware are de-indexed. Clean any infections and request a review.

### Step 6 — Monitor Crawl Stats
```
Search Console → Settings → Crawl Stats
```
Review the crawl history:
- **Crawl errors:** Any spikes indicate server issues
- **Robots.txt fetches:** Confirms Google is reading the robots.txt
- **Average response time:** Should be under 600ms consistently

### Step 7 — Re-submit the Sitemap
If all checks pass but the site still isn't indexed:
```
Search Console → Sitemaps → Delete existing sitemap → Re-submit
```
Sometimes re-submission resets the queue and speeds up crawling.

---

## Summary Diagnosis Flowchart

```
Site not indexing after sitemap submission?
│
├─ Is "Discourage search engines" checked in WP Settings?
│   YES → Uncheck it immediately → Re-submit sitemap
│
├─ Does robots.txt have Disallow: / ?
│   YES → Fix robots.txt → Wait 48 hours
│
├─ Does any page have <meta name="robots" content="noindex">?
│   YES → Remove noindex tag via Yoast → Re-inspect URL
│
├─ Are canonical tags pointing to localhost or HTTP?
│   YES → Update site URL in WP Settings → Run DB search-replace
│
├─ Does sitemap return 404?
│   YES → Enable XML sitemap in Yoast → Re-submit
│
├─ Does URL Inspection show a blank rendered screenshot?
│   YES → JS rendering issue → Add static HTML fallback content
│
├─ Is page speed TTFB > 1.8s?
│   YES → Enable caching → Compress images → Upgrade hosting
│
└─ All above pass but still not indexed?
    → Check Manual Actions in Search Console
    → Wait 2–4 weeks (new sites take time)
    → Build 2–3 quality backlinks to accelerate discovery
```

---

## Quick Reference Checklist

- [ ] "Discourage search engines" setting is **OFF** in WordPress
- [ ] `robots.txt` does not block `/` or key pages
- [ ] No `noindex` meta tags on indexable pages
- [ ] Yoast SEO is not set to noindex any page type
- [ ] Canonical tags point to correct `https://` URLs
- [ ] Site URL in WP Settings matches live domain (not localhost)
- [ ] Sitemap accessible at `/sitemap_index.xml` and returns valid XML
- [ ] Sitemap submitted and showing `Success` in Search Console
- [ ] URL Inspection shows page content in rendered screenshot
- [ ] No Manual Actions or Security Issues in Search Console
- [ ] TTFB < 600ms and mobile PageSpeed score > 70
- [ ] No 403/500 errors on server

---

*SEO Diagnosis — Indexing Troubleshooting Guide | Worknoon WordPress Assessment | Johnson Wuraola | 2026*
