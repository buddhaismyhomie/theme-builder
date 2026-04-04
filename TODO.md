# Theme Builder — Roadmap & Tasks

## Phase 1: Foundation (WordPress Theme)

### Theme Skeleton
- [ ] Create `style.css` with theme metadata
- [ ] Create `functions.php` with theme setup (menus, thumbnails, scripts)
- [ ] Build template files: `index.php`, `header.php`, `footer.php`, `page.php`, `single.php`, `front-page.php`
- [ ] Set up asset pipeline (`assets/css/`, `assets/js/`)
- [ ] Add Google Fonts (Montserrat + Open Sans or configurable)
- [ ] Build responsive navigation (desktop + mobile hamburger)
- [ ] Build footer with widget areas

### Section System
- [ ] Design section data model (post meta JSON structure)
- [ ] Build `class-section-manager.php` — CRUD for sections
- [ ] Create section renderer (loops through page sections, includes correct template)
- [ ] Build initial section templates:
  - [ ] Hero (heading, subheading, bg image, CTA)
  - [ ] Features (grid of icon + title + text cards)
  - [ ] Text + Image (side by side, reversible)
  - [ ] CTA (call to action banner)
  - [ ] Gallery (image grid with lightbox)
  - [ ] Testimonials (quotes with author)
  - [ ] Stats (number counters)
  - [ ] Contact Form (name, email, message)
  - [ ] Blog Feed (latest posts grid)
  - [ ] FAQ (accordion)
  - [ ] Custom HTML (freeform)
- [ ] Build section CSS with design tokens (CSS custom properties)
- [ ] Add WP Customizer options: colors, fonts, logo, footer text

### Testing
- [ ] Set up local WordPress dev environment (LocalWP or Docker)
- [ ] Install theme and verify all templates render
- [ ] Test responsive on mobile/tablet/desktop
- [ ] Test with dummy content (WP Theme Unit Test Data)

---

## Phase 2: Staging Pipeline

- [ ] Design staging database table schema
- [ ] Build `class-staging-manager.php`
  - [ ] Create staged change
  - [ ] Preview staged changes (render page with changes applied)
  - [ ] Approve staged change (write to live)
  - [ ] Reject staged change (discard)
  - [ ] List pending changes
- [ ] Build staging review UI in WP admin
  - [ ] List of pending changes with before/after diff
  - [ ] Preview button (opens page with changes applied)
  - [ ] Approve / Reject buttons
- [ ] Add staging status to section data model (`published`, `staging`, `deleted`)
- [ ] Preview URL system (`/preview/?change=chg_xxx` for authenticated users only)
- [ ] Audit log — record all changes with timestamp, user, before/after

---

## Phase 3: MCP Server

### Server Setup
- [ ] Initialize Node.js project in `mcp/`
- [ ] Set up MCP SDK (`@anthropic-ai/mcp` or equivalent)
- [ ] Implement WordPress REST API client (authenticates with application passwords)
- [ ] Write MCP server entry point (`server.js`)

### MCP Tools
- [ ] `list_pages` — list all pages
- [ ] `get_page` — get page with sections
- [ ] `list_sections` — list sections on a page
- [ ] `get_section` — get section detail
- [ ] `create_section` — add section (→ staging)
- [ ] `update_section` — edit section (→ staging)
- [ ] `delete_section` — remove section (→ staging)
- [ ] `reorder_sections` — change order
- [ ] `list_section_types` — available types + schemas
- [ ] `create_post` — create blog post (→ draft or staging)
- [ ] `upload_media` — upload image/file
- [ ] `get_staging_changes` — list pending changes
- [ ] `publish_staging` — approve and publish
- [ ] `discard_staging` — reject changes
- [ ] `get_theme_settings` — read customizer
- [ ] `update_theme_settings` — change colors/fonts/logo (→ staging)

### Documentation
- [ ] Write MCP setup guide (how to add to Claude Code config)
- [ ] Document authentication setup (WP application passwords)
- [ ] Write example workflows ("add a section", "edit hero", "publish changes")

### Testing
- [ ] Test each tool individually against local WP
- [ ] Test full workflow: create section → preview → publish
- [ ] Test error handling (invalid page, auth failure, etc.)

---

## Phase 4: AI Dashboard (Site Owner)

### Backend
- [ ] Build `class-ai-dashboard.php` — registers admin page, REST endpoints
- [ ] Create chat API endpoint (`/wp-json/theme-builder/v1/chat`)
  - [ ] Receives user prompt
  - [ ] Calls Claude API with tool use (tools = section CRUD + post CRUD)
  - [ ] Returns AI response + any staged changes
- [ ] Implement AI tool functions (same CRUD as MCP, routed through staging)
- [ ] Add rate limiting and input validation
- [ ] Store Claude API key securely in wp-config.php

### Frontend
- [ ] Build chat interface in WP admin page
  - [ ] Message input + send button
  - [ ] Message history (scrollable)
  - [ ] AI responses with formatted content
  - [ ] "Changes pending" indicator when staging changes exist
- [ ] Build inline preview panel
  - [ ] Shows affected page section with changes highlighted
  - [ ] Approve / Edit / Reject buttons per change
- [ ] Build staging review page (list all pending changes from all sources)

### Safety & Permissions
- [ ] Restrict AI Dashboard to admin users (`manage_options`)
- [ ] Build action whitelist (what the AI can and cannot do)
- [ ] Add confirmation step for destructive actions
- [ ] Log all AI actions with full audit trail
- [ ] Add cost tracking / usage display for Claude API calls

---

## Phase 5: Polish & Release

- [ ] Write end-user documentation (for site owners using the dashboard)
- [ ] Write developer documentation (for devs using MCP)
- [ ] Create demo site with sample content
- [ ] Performance optimization (lazy load sections, minimize CSS/JS)
- [ ] Security audit (auth, input sanitization, XSS prevention, CSRF tokens)
- [ ] Package theme for distribution (ZIP for WP upload)
- [ ] Create GitHub release with changelog

---

## Future Ideas

- [ ] Gutenberg block versions of sections (for users who prefer the block editor)
- [ ] Multi-language support (WPML / Polylang integration)
- [ ] A/B testing for sections (show variant A to 50%, variant B to 50%)
- [ ] Version history for sections (rollback to previous version)
- [ ] Template library (pre-built page templates: agency, restaurant, SaaS, etc.)
- [ ] White-label option (remove Theme Builder branding)
- [ ] CLI tool for theme scaffolding (`npx create-theme-builder my-site`)
- [ ] Marketplace for community section templates
