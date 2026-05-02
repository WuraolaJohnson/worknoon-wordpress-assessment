# 🔍 SEO Diagnosis: Worknoon Website Not Indexing After Sitemap Submission
**File:** `seo-diagnosis.md`  

**Scenario:** A new Worknoon website has been launched and the sitemap submitted to Google Search Console, but the site is not appearing in Google search results.
---

Step 1: Check Crawlability
Use GSC > URL Inspection. If it says "not available," Google can't reach your site.

Step 2: Audit robots.txt
Open yourwebsite.com/robots.txt. If you see Disallow: /, you've blocked Google entirely.

Step 3: Check for no-index tags
View page source → search for noindex. This tag tells Google "ignore this page" — your sitemap won't override it.

Step 4: Verify canonical URLs
Check for <link rel="canonical">. Wrong canonicals point Google to a different page instead of yours.

Step 5: Test sitemap
Open sitemap.xml. If it's broken or empty, Google has nothing to crawl.

Step 6: Check page speed
Run PageSpeed Insights. Very slow pages (<40) get delayed or skipped from indexing.

Step 7: Search Console debugging
Request Indexing — forces Google to crawl now

Coverage report — shows exact error (crawled but not indexed = thin content; discovered not indexed = crawl budget issue)