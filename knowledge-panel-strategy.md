# 🧠 Knowledge Panel Strategy 
**File:** `knowledge-panel-strategy.md`
---

## 1. 🚀 How Worknoon Can Trigger or Strengthen a Google Knowledge Panel

Google establishes entity identity through consistency + authority + explicitness. Worknoon must help Google confirm it is a real, distinct, and notable entity.

## 2. 🏗️ Entity Building Steps

1. Claim and complete profiles on LinkedIn, Crunchbase, Twitter, and Google Business Profile
2. Use exact same name "Worknoon" and description "Flexible hybrid spaces" everywhere
3. Add sameAs links from website to all official profiles
4. Remove any duplicate or fake profiles
5. Verify profiles where possible (LinkedIn, Twitter, Google)

## 3. 📋 Schema Requirements

Add this JSON-LD to the homepage and About page:

```json
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Worknoon",
  "url": "https://worknoon.com",
  "logo": "https://worknoon.com/logo.png",
  "description": "Platform for flexible hybrid spaces in 100+ cities.",
  "sameAs": [
    "https://www.linkedin.com/company/worknoon",
    "https://twitter.com/worknoon"
  ]
}
</script>

## 4. 🎨 Brand Identity Consistency Signals
a. Use the same logo file on website, LinkedIn, Twitter, and Crunchbase
b. Always write name as "Worknoon" (never "Work Noon" or "WorkNoon")
c. Use the exact tagline "Flexible Hybrid Spaces" on every profile
d. Keep brand colors the same across website and social banners
e. Display the same favicon on browser tab and search results
f. Use hello@worknoon.com email domain (not Gmail or Hotmail)

## 5. 📰 Press & Authority Signals

a. Get listed on Crunchbase and LinkedIn (high priority)
b. Pursue launch announcement on Betalist or TechCrunch
c. List Worknoon on directory sites: Coworker.com, Upflex, Spacebase
d. Encourage user reviews on Trustpilot or G2

## 6. 📄 About Page Hierarchy
URL: /about

Structure (top to bottom):
a. H1: About Worknoon
b. Intro paragraph describing what Worknoon does
c. Mission statement (optional)
d. Team / Founders with names and LinkedIn links
e. Timeline: founded date, key milestones
f. Company stats: cities served, number of spaces
g. Contact info and social links
h. JSON-LD schema (copy from above)

