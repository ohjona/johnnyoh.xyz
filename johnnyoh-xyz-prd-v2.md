# johnnyoh.xyz — Product Requirements Document (v2)

**Owner:** Johnny
**Last Updated:** February 1, 2026
**Status:** Draft

---

## Purpose

A canonical personal URL. One link that gives anyone — recruiters, collaborators, readers — the full picture: who Johnny is, what he's building, and what he thinks.

Content-first. Medium-like reading experience. No gimmicks.

## Core Design Philosophy

Inspired by Medium's design language: generous whitespace, strong typography, minimal navigation chrome. The site should feel like opening a well-edited publication, not a developer portfolio template.

Every page optimized for readability. Content is the UI.

---

## Site Architecture

```
johnnyoh.xyz
├── /                     Home — short intro + recent posts feed
├── /about                Resume/introduction — single clean page
│   └── resume.pdf        Downloadable PDF (LaTeX-compiled)
├── /posts/               Blog-style writing (Medium layout)
│   ├── /post-slug-1
│   ├── /post-slug-2
│   └── ...
└── /projects/            Portfolio showcases
    ├── /ontos
    ├── /npo-lab
    └── ...
```

### Page Descriptions

**Home (`/`)**
Entry point. Brief personal tagline. Feed of recent posts and project updates, similar to a Medium profile page. No hero images, no animations. Just content.

**About / Resume (`/about`)**
Single-page introduction. Professional background, skills, current focus. Clean HTML layout styled to match the site theme. "Download Resume (PDF)" button linking to the LaTeX-compiled PDF.

**Posts (`/posts/`)**
Blog-style articles. This is the LinkedIn deep-dive strategy: LinkedIn gets the hook, johnnyoh.xyz gets the full piece. Each post is a Markdown file in the Hugo content directory. Push to GitHub, auto-deploys.

**Projects (`/projects/`)**
Portfolio entries for Ontos, NPO Lab, and future builds. Visual explanations, screenshots, architecture diagrams, usage instructions. Not working demos — clear documentation of what each project does and why.

---

## Content Model

All content authored in Markdown with YAML frontmatter.

### Post Frontmatter

```yaml
---
title: "Post Title"
date: 2026-02-01
description: "One-line summary for SEO and social sharing"
tags: ["ai", "consulting", "product"]
draft: false
image: "/images/posts/og-image.png"    # Open Graph image
---
```

### Project Frontmatter

```yaml
---
title: "Project Ontos"
date: 2026-01-15
description: "AI context management — glass box, not black box"
tags: ["ai", "context-management", "product"]
status: "active"                        # active | paused | archived
repo: "https://github.com/johnny/ontos"
image: "/images/projects/ontos-og.png"
---
```

### Resume

- **Web version:** Markdown rendered as styled HTML at `/about`
- **PDF version:** Maintained separately in LaTeX, compiled to PDF, stored in `static/resume.pdf`
- Both versions kept in sync manually. The web version is the "living" version; the PDF is the "send to recruiter" version.

---

## Technical Stack

| Component | Choice | Rationale |
|-----------|--------|-----------|
| SSG | Hugo | Blog-native, fastest builds, Markdown-first, Cloudflare Pages has native support |
| Hosting | Cloudflare Pages | Free tier, auto-deploys from GitHub, same platform as domain and analytics |
| Domain | johnnyoh.xyz (Cloudflare) | Already purchased |
| DNS / CDN | Cloudflare | Bundled with domain |
| SSL | Cloudflare | Automatic |
| Analytics | Cloudflare Web Analytics | Free, no cookies, no JS tag needed, built into the dashboard |
| Source control | GitHub | Repo triggers Cloudflare Pages builds on push to main |
| Resume PDF | LaTeX → PDF | Compiled locally, committed to `static/` |

### Deployment Pipeline

```
Write Markdown → Push to GitHub → Cloudflare Pages auto-builds Hugo → Live at johnnyoh.xyz
```

No CI/CD config needed beyond initial Cloudflare Pages setup. Hugo build command and publish directory set once in the Cloudflare dashboard.

**Build settings:**
- Build command: `hugo`
- Build output directory: `public`
- Hugo version: set via environment variable `HUGO_VERSION`

---

## Design Requirements

### Typography & Layout (Medium-Inspired)

- **Reading width:** Max ~680px content column, centered. This is the single most important design decision.
- **Font:** System font stack or a clean serif for body text (like Medium's Charter). Sans-serif for headings and navigation.
- **Line height:** 1.6–1.8 for body text
- **Font size:** 18–21px body text on desktop, scales down on mobile
- **Spacing:** Generous paragraph spacing. Content breathes.
- **Colors:** Near-black on off-white. Minimal accent color. No dark mode in MVP.

### Navigation

Minimal. Site title/logo (left) + 3-4 links (right): Home, About, Posts, Projects. Sticky or static — not important in MVP.

### Shareability (MVP Requirement)

Every page must have proper Open Graph and Twitter Card meta tags so links render well when shared on LinkedIn, Slack, iMessage, Twitter.

Required meta per page:
- `og:title`
- `og:description`
- `og:image` (1200x630px recommended)
- `og:url`
- `og:type` (article for posts, website for others)
- `twitter:card` = `summary_large_image`

Hugo templates should auto-generate these from frontmatter. If no custom `image` is set in frontmatter, fall back to a default site-wide OG image.

---

## Feature Prioritization

### MVP (Ship First)

- Home page with recent content feed
- About/resume page with PDF download
- Blog post template (Medium-style reading experience)
- Project showcase template
- Open Graph meta tags on all pages
- Cloudflare Pages deployment from GitHub
- Cloudflare Web Analytics
- Mobile responsive
- RSS feed (Hugo generates this by default)
- Basic SEO: sitemap.xml, robots.txt, semantic HTML, clean URLs

### Phase 2 (Nice to Have)

- Estimated read time on posts
- Topic tags with tag index pages
- Related posts / "read next" suggestions
- KaTeX support for math notation in posts
- Code syntax highlighting (Hugo supports via Chroma)
- Search functionality (client-side, e.g., Fuse.js)

### Not Now

- Comments / reactions
- Newsletter signup
- Dark mode
- Custom CMS / admin panel
- Working project demos
- GA4 or any heavier analytics

---

## Hugo Theme Approach

**Option A: Start from an existing Medium-inspired Hugo theme and customize.**
Candidates: hugo-theme-nostyleplease, hugo-paper, hugo-blog-awesome, or similar minimal themes. Faster to ship, risk of looking generic.

**Option B: Build a custom theme from scratch.**
Full control over the Medium-like design. More upfront work, but exactly what you want. Hugo themes are just HTML templates + CSS.

**Recommendation:** Option A — pick the closest theme, strip it to essentials, then customize typography and spacing to hit the Medium feel. You can always rebuild later. Ship first.

---

## Repo Structure

```
johnnyoh-xyz/
├── archetypes/           # Default frontmatter templates
│   ├── posts.md
│   └── projects.md
├── content/
│   ├── _index.md         # Home page
│   ├── about.md          # Resume / intro page
│   ├── posts/
│   │   └── my-first-post.md
│   └── projects/
│       ├── ontos.md
│       └── npo-lab.md
├── layouts/              # Custom templates (or theme overrides)
├── static/
│   ├── resume.pdf        # LaTeX-compiled resume
│   └── images/
│       └── og-default.png
├── themes/               # Hugo theme (git submodule or vendored)
├── hugo.toml             # Site config
└── README.md
```

---

## Open Questions

- [ ] Hugo theme selection (find best Medium-like base theme)
- [ ] Visual identity: any color preferences, or pure black/white/gray?
- [ ] Which projects to include in MVP launch?
- [ ] Do you have existing writing to backfill, or starting fresh?
- [ ] Default OG image design — simple text-based or illustrated?
- [ ] LaTeX resume: do you already have a `.tex` file, or need to create one?

---

## Next Steps

1. ~~Purchase johnnyoh.xyz domain on Cloudflare~~ ✓
2. ~~Choose SSG~~ ✓ Hugo
3. ~~Choose hosting~~ ✓ Cloudflare Pages
4. Select and customize Hugo theme
5. Set up GitHub repo with Hugo scaffold
6. Connect repo to Cloudflare Pages
7. Configure johnnyoh.xyz DNS → Cloudflare Pages
8. Create base templates (home, about, post, project)
9. Populate initial content
10. Configure OG meta tags
11. Deploy and verify
