# Theme Builder — Architecture

## System Overview

```
┌──────────────────────────────────────────────────────────────┐
│                      LIVE WORDPRESS SITE                     │
│  (Standard WP theme — pages, posts, sections, menus)         │
└──────────────┬───────────────────────────┬───────────────────┘
               │                           │
       ┌───────▼────────┐         ┌────────▼──────────┐
       │  MCP Server     │         │  AI Dashboard      │
       │  (Developer)    │         │  (Site Owner)      │
       │                 │         │                    │
       │  Claude Code    │         │  Browser chat UI   │
       │  connects here  │         │  → AI agent edits  │
       │  to edit theme  │         │  content & layout  │
       └───────┬─────────┘         └────────┬──────────┘
               │                            │
               └──────────┬─────────────────┘
                          │
                  ┌───────▼────────┐
                  │  STAGING LAYER  │
                  │                 │
                  │  Preview draft  │
                  │  Diff view      │
                  │  Approve/reject │
                  └───────┬─────────┘
                          │
                  ┌───────▼────────┐
                  │  PUBLISH        │
                  │  Goes live      │
                  └─────────────────┘
```

## 1. WordPress Theme Layer

Standard WordPress theme structure with section-based architecture.

### Template Hierarchy

```
theme-builder/
├── style.css                  ← Theme metadata + base styles
├── functions.php              ← Theme setup, hooks, enqueue scripts
├── index.php                  ← Fallback template
├── header.php                 ← Site header + nav
├── footer.php                 ← Site footer
├── page.php                   ← Page template (loads sections)
├── single.php                 ← Single post template
├── archive.php                ← Blog listing
├── 404.php                    ← Not found page
├── front-page.php             ← Homepage (section-based)
├── searchform.php             ← Search form
│
├── sections/                  ← Composable section templates
│   ├── hero.php
│   ├── features.php
│   ├── testimonials.php
│   ├── cta.php
│   ├── gallery.php
│   ├── text-image.php
│   ├── stats.php
│   ├── team.php
│   ├── pricing.php
│   ├── faq.php
│   ├── contact-form.php
│   ├── blog-feed.php
│   └── custom.php             ← Freeform HTML section
│
├── assets/
│   ├── css/
│   │   ├── theme.css          ← Main theme styles
│   │   ├── sections.css       ← Section-specific styles
│   │   └── dashboard.css      ← AI dashboard styles
│   ├── js/
│   │   ├── theme.js           ← Frontend JS (nav, scroll, etc.)
│   │   ├── dashboard.js       ← AI dashboard client-side
│   │   └── section-editor.js  ← Live preview for section editing
│   └── images/
│
├── inc/
│   ├── class-section-manager.php    ← CRUD for page sections
│   ├── class-staging-manager.php    ← Staging/preview/publish pipeline
│   ├── class-ai-dashboard.php       ← Admin chat UI + AI agent
│   ├── class-mcp-server.php         ← MCP server endpoint
│   ├── customizer.php               ← WP Customizer options
│   └── helpers.php                  ← Utility functions
│
├── mcp/                       ← MCP server package
│   ├── server.js              ← MCP server entry point (Node.js)
│   ├── tools/                 ← MCP tool definitions
│   │   ├── list-sections.js
│   │   ├── get-section.js
│   │   ├── update-section.js
│   │   ├── create-section.js
│   │   ├── delete-section.js
│   │   ├── list-pages.js
│   │   ├── get-page.js
│   │   ├── create-post.js
│   │   ├── upload-media.js
│   │   └── publish-staging.js
│   ├── package.json
│   └── README.md              ← MCP setup instructions
│
└── dashboard/                 ← AI Dashboard (site owner interface)
    ├── templates/
    │   ├── dashboard-page.php
    │   ├── chat-interface.php
    │   └── staging-review.php
    └── api/
        ├── chat-endpoint.php  ← Receives prompts, calls AI, returns changes
        └── staging-api.php    ← Preview/approve/reject endpoints
```

### Section Data Model

Each page is an ordered list of sections. Sections are stored as post meta:

```json
{
  "page_id": 42,
  "sections": [
    {
      "id": "sec_abc123",
      "type": "hero",
      "status": "published",
      "data": {
        "heading": "We make water treatment work better",
        "subheading": "Upgrade technologies for sustainable infrastructure",
        "background_image": 156,
        "cta_text": "Learn More",
        "cta_url": "#technologies"
      }
    },
    {
      "id": "sec_def456",
      "type": "features",
      "status": "staging",
      "data": {
        "heading": "Our Technologies",
        "items": [...]
      }
    }
  ]
}
```

## 2. MCP Server (Developer Workflow)

The MCP server runs alongside WordPress (Node.js process) and exposes tools that Claude Code can call.

### How It Works

1. Developer adds the MCP server to their Claude Code config
2. Claude Code discovers available tools (list sections, edit section, create post, etc.)
3. Developer says "add a testimonials section to the homepage after the hero"
4. Claude Code calls `create-section` tool → section lands in **staging**
5. Developer previews, then says "looks good, publish it" → calls `publish-staging`

### MCP Tools

| Tool | Description |
|------|-------------|
| `list_pages` | List all pages with their section counts |
| `get_page` | Get a page with all its sections and content |
| `list_sections` | List sections on a specific page |
| `get_section` | Get a section's type, data, and status |
| `create_section` | Add a new section to a page (lands in staging) |
| `update_section` | Modify a section's content or settings (lands in staging) |
| `delete_section` | Remove a section (marks for deletion, confirmed on publish) |
| `reorder_sections` | Change section order on a page |
| `list_section_types` | List available section types with their data schemas |
| `create_post` | Create a new blog post (draft or staging) |
| `upload_media` | Upload an image/file to the media library |
| `get_staging_changes` | List all pending staging changes |
| `publish_staging` | Publish one or all staging changes to live |
| `discard_staging` | Discard staging changes |
| `get_theme_settings` | Get current theme customizer settings |
| `update_theme_settings` | Update colors, fonts, logo, etc. (staging) |

### Authentication

MCP server authenticates via WordPress application passwords (built into WP since 5.6). The developer generates an app password in WP admin → Users → Profile, and configures it in their MCP settings.

## 3. AI Dashboard (Site Owner Workflow)

A custom WordPress admin page where non-technical users chat with an AI agent.

### How It Works

1. Site owner logs into WordPress admin
2. Goes to "AI Assistant" in the sidebar
3. Types: "Add a blog post about our new water treatment project in Malaysia"
4. AI agent processes the request, generates content, creates a draft post
5. Owner sees a preview with diff highlighting
6. Owner clicks "Approve" or "Edit" or "Reject"

### Capabilities

The AI agent can:
- Create and edit blog posts
- Add/modify/reorder page sections
- Change hero images, headings, CTAs
- Update menus and navigation
- Suggest and apply design changes (colors, fonts via customizer)
- Upload and place images

### What It Cannot Do (Safety Rails)

- Cannot delete pages or posts (only archive/draft)
- Cannot modify theme PHP files
- Cannot install/remove plugins
- Cannot change user permissions
- Cannot access or modify wp-config.php
- All changes must be approved before going live

### Tech Stack

- Frontend: Simple chat UI in WP admin (vanilla JS or lightweight React)
- Backend: WP REST API endpoint that receives prompts
- AI: Calls Claude API (Anthropic SDK) with tool use — tools map to the same section CRUD as MCP
- Auth: Standard WordPress admin authentication + capability checks

## 4. Staging Layer

The staging system is the safety net for both workflows.

### How Staging Works

1. Any change from MCP or AI Dashboard creates a "staged change" record
2. Staged changes are stored in a custom database table
3. The preview system renders the page with staged changes applied (but only for authenticated users)
4. Approved changes get written to the actual post meta / post content
5. Rejected changes are discarded

### Staged Change Record

```json
{
  "id": "chg_789",
  "type": "section_update",
  "page_id": 42,
  "section_id": "sec_abc123",
  "author": "mcp:developer" | "dashboard:owner@email.com",
  "created_at": "2026-04-04T12:00:00Z",
  "status": "pending" | "approved" | "rejected",
  "before": { "heading": "Old heading" },
  "after": { "heading": "New heading" },
  "preview_url": "/preview/?change=chg_789"
}
```

## 5. Security Considerations

- MCP server requires WordPress application password (never stores WP admin password)
- AI Dashboard only available to users with `manage_options` capability (admins)
- All AI actions are logged with timestamp, user, and before/after state
- Staging prevents accidental live changes
- Rate limiting on AI Dashboard API endpoint
- Claude API key stored in wp-config.php, never exposed to frontend
- MCP server only accessible from localhost by default (configurable)
