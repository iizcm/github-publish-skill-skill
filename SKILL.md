---
name: github-publish-skill
description: "Publish individual Hermes Agent skills as standalone public GitHub repositories with bilingual README.md + SKILL.md. Covers repo creation, content formatting, badge usage, and push automation via cron."
tags: [github, skill-publishing, repo-management, automation]
version: 1.0.0
author: iizcm
license: MIT
platforms: [all]
---

# GitHub Skill Repository Publishing

## What is it?

Creates individual public GitHub repositories for each Hermes Agent skill. Each repo contains ONLY two files: `README.md` and `SKILL.md`. Used to build a public skill collection library.

## When to use
- User wants to publish skills as standalone public repos on GitHub
- Building a skill library/collection repository index
- Creating shareable skill templates for the community

## Workflow

### 1. Prepare repo content

Each repo MUST contain EXACTLY these files:
```
iizcm/<skill-name>/
├── README.md        ← Bilingual EN/ID installation & usage guide
└── SKILL.md         ← Skill definition (frontmatter + description)
```

NO extra files. No images. No demo HTML. No capture scripts. No assets folders. Keep it minimal so users can clone, install, and edit without clutter.

### 2. README.md rules

Content:
- Skill name + one-line English description
- Installation instructions (copy SKILL.md to ~/.hermes/skills/)
- Usage trigger example (chat command or CLI)
- Bilingual: English section first, then Bahasa Indonesia section
- Modern shield.io badges: License MIT, Hermes Skill tag, category tag
- NO ethical/checklist/warning sections — skills are legal and safe

Badges (use .png format, NOT deprecated .svg):
```markdown
[![License: MIT](https://img.shields.io/badge/license-MIT-blue)](LICENSE)
[![Hermes Skill](https://img.shields.io/badge/hermes-skill-orange)](#)
[![Web](https://img.shields.io/badge/category-web-purple)](#)
```

### 3. SKILL.md rules

Minimal frontmatter + brief description:
```yaml
---
name: <skill-name>
description: "<one-line description>"
tags: [<category>]
version: 1.0.0
---
<brief explanation>
```

### 4. GitHub creation

```bash
export GITHUB_TOKEN=$(grep '^GITHUB_TOKEN=' "$HOME/.hermes/.env" | head -1 | cut -d= -f2- | tr -d '\n\r')

# Create repo via API
curl -s -X POST "https://api.github.com/user/repos" \
  -H "Authorization: token $GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  -d '{"name":"<skill-name>","private":false,"auto_init":true}'

# Clone, copy files, commit, push
git init && git add -A && git commit -m "Initial: <skill-name> skill" && git branch -m main
git remote add origin https://${GITHUB_TOKEN}@github.com/iizcm/<skill-name>.git
git push -u origin main
```

### 5. Automation via cron

For batch publishing (e.g., 10 repos per run):
- Cron schedule: `0 */6 * * *` (every 6 hours)
- Each run processes next batch of 10 skills
- Process sequentially across ~15 batches for ~150 total skills

## CRITICAL: Data Privacy

NEVER include in any published repo:
- ❌ Private keys, seed phrases, mnemonics, passphrases
- ❌ Wallet addresses (primary, recovery, sybil)
- ❌ API keys, tokens, credentials
- ✅ Allowed: public contract addresses (USDC, USDbC), RPC endpoints
- ✅ Use placeholders: `<YOUR_WALLET_ADDRESS>`, `"your_seed_phrase"`, `<API_KEY>`

## Pitfalls

1. **Badge format**: Old shield.io `.svg` format may return 404 or render poorly. Use the new `.png` format.
2. **Auto-init conflict**: GitHub API `auto_init:true` creates default README that conflicts with local git branch. Workaround: `git fetch origin && git reset --hard origin/main` before pushing.
3. **No extra files**: Users want to clone → install → edit. Extras like demo HTML, screenshots, capture scripts bloat the repo and create confusion.
4. **No ethical warnings**: Don't add permission/consent checklists to README. These are legitimate open-source developer tools; unnecessary warnings deter adoption.
