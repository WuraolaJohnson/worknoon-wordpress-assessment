# 📦 WordPress Project Packaging Guide
### How to Package & Push the Local WordPress Site to GitHub
**Developer:** Johnson Wuraola | **Repo:** worknoon-wordpress-assessment

---

## Overview

WordPress sites cannot be pushed to GitHub as-is — the core files are large (~25MB+), contain sensitive credentials, and are available publicly from WordPress.org anyway. The correct approach is to push **only your custom work** plus a **database export**, structured so that any developer can reconstruct the site locally.

---

## ✅ What TO Commit to GitHub

```
worknoon-wordpress-assessment/
├── README.md                          ← Always commit
├── .gitignore                         ← Always commit
├── knowledge-panel-strategy.md        ← Assessment doc
├── seo-diagnosis.md                   ← Assessment doc
├── short-answers.md                   ← Assessment doc
│
├── schema/
│   ├── organization-schema.json       ← Commit
│   ├── person-schema.json             ← Commit
│   └── website-schema.json            ← Commit
│
├── docs/
│   ├── commit-history.md              ← Commit
│   ├── packaging-guide.md             ← Commit (this file)
│   └── assessment-checklist.md        ← Commit
│
├── wordpress/
│   └── wp-content/
│       ├── themes/
│       │   └── hello-elementor/       ← Commit (active theme only)
│       │       ├── style.css
│       │       ├── functions.php
│       │       └── (modified files only)
│       │
│       └── plugins/
│           └── (only custom or premium plugins)
│           ← DO NOT commit free plugins available on wordpress.org
│
└── database/
    └── worknoon_db.sql                ← Commit (exported DB dump)
```

---

## ❌ What NOT to Commit

| Path/File | Reason |
|-----------|--------|
| `wp-config.php` | Contains DB credentials — **NEVER commit** |
| `wp-core/` (`wp-admin/`, `wp-includes/`) | WordPress core — public & 25MB+ |
| `wp-content/uploads/` | Binary media files — use Git LFS or link externally |
| `wp-content/plugins/` (free plugins) | Available on WordPress.org; list in README instead |
| `wp-content/cache/` | Auto-generated — regenerated after install |
| `.htaccess` | Server-specific — may cause conflicts |
| `wp-content/upgrade/` | WordPress upgrade temp folder |
| `node_modules/` | Build dependencies — use `npm install` to restore |
| `wp-content/plugins/*/vendor/` | Composer dependencies |
| Any `.log` files | Runtime logs — not part of source |
| `credentials.txt` | Obviously — never commit passwords |

---

## 🗂️ Step-by-Step Packaging Process

### Step 1 — Export the Database

Using **phpMyAdmin** (XAMPP) or **LocalWP → Database → Open in Adminer**:

```sql
-- Export: All tables, structure + data, SQL format
-- Save as: database/worknoon_db.sql
```

Or via WP-CLI:
```bash
wp db export database/worknoon_db.sql
```

Or via **Duplicator plugin** (easiest for beginners):
1. Install Duplicator plugin
2. Packages → Create New
3. Download the `.sql` file from the archive
4. Save to `database/worknoon_db.sql`

---

### Step 2 — Create wp-config-sample.php

Copy `wp-config.php`, replace all real values with placeholders, and save as `wp-config-sample.php`:

```php
<?php
// ** Database settings ** //
define( 'DB_NAME', 'YOUR_DATABASE_NAME' );
define( 'DB_USER', 'YOUR_DATABASE_USER' );
define( 'DB_PASSWORD', 'YOUR_DATABASE_PASSWORD' );
define( 'DB_HOST', 'localhost' );
define( 'DB_CHARSET', 'utf8' );
define( 'DB_COLLATE', '' );

// ** Authentication keys and salts **
// Generate fresh salts at: https://api.wordpress.org/secret-key/1.1/salt/
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );

$table_prefix = 'wp_';
define( 'WP_DEBUG', false );
if ( ! defined( 'ABSPATH' ) ) {
    define( 'ABSPATH', __DIR__ . '/' );
}
require_once ABSPATH . 'wp-settings.php';
```

Commit **only** `wp-config-sample.php`. The real `wp-config.php` should be in `.gitignore`.

---

### Step 3 — Set Up .gitignore

Ensure your `.gitignore` in the root blocks sensitive files before committing anything:

```gitignore
# See .gitignore file in this repo for the full reference
wp-config.php
wp-content/uploads/
wp-content/cache/
wp-content/upgrade/
wp-content/advanced-cache.php
wp-content/wp-cache-config.php
*.log
.DS_Store
Thumbs.db
credentials.txt
```

---

### Step 4 — Initialize Git & Push

```bash
# Navigate to your project folder
cd "C:/path/to/worknoon-wordpress-assessment"

# Initialize git (if not already)
git init

# Stage documentation and schema files first
git add README.md .gitignore *.md schema/ docs/

# Commit first
git commit -m "init: initialize repository with README, .gitignore, and folder structure"

# Add WordPress theme (custom theme only)
git add wordpress/wp-content/themes/hello-elementor/
git commit -m "setup: add active WordPress theme files"

# Add database export
git add database/worknoon_db.sql
git commit -m "database: add full site database export"

# Add remote and push
git remote add origin https://github.com/WuraolaJohnson/worknoon-wordpress-assessment.git
git branch -M main
git push -u origin main
```

---

### Step 5 — Verify Before Pushing

Run through this final checklist before `git push`:

```bash
# Check what will be committed
git status

# Verify .gitignore is working — wp-config.php should NOT appear in untracked:
git check-ignore -v wp-config.php

# Review the diff of what you're about to commit
git diff --cached --stat
```

---

## 🔄 Alternative: Export Full Site as ZIP (For Assessors)

If the assessor needs the complete site (not just the repo), use **Duplicator** or **All-in-One WP Migration**:

1. Install **Duplicator** plugin
2. Create a new Package (full site + DB)
3. Download both `installer.php` and the `.zip` archive
4. Share via Google Drive / Dropbox (too large for GitHub)
5. Mention download link in README

---

*Packaging Guide for Worknoon WordPress Developer Assessment | Johnson Wuraola | 2026*
