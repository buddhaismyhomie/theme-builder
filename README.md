# Theme Builder

A WordPress theme with built-in AI editing capabilities. Site owners and developers can modify pages, add content, and build sections using natural language — either through Claude Code (MCP) or through a browser-based dashboard.

## What This Is

Theme Builder is a WordPress theme that works like any standard theme (pages, posts, menus, responsive layout, customizer options) but adds two powerful features:

1. **MCP Server for Claude Code** — Developers connect Claude Code to a running WordPress site and can vibe-code individual sections, edit layouts, add components, and push changes through a staging pipeline before they go live.

2. **AI Dashboard for Site Owners** — Non-technical site owners log into a custom admin panel and use a chat interface to prompt an AI agent: "add a blog post about X", "change the hero image", "add a testimonials section", etc.

Both workflows go through a **staging → review → publish** pipeline so nobody accidentally breaks the live site.

## Who It's For

- **Agencies** building sites for clients who want to self-manage content
- **Developers** who want to use Claude Code to rapidly build and iterate on WordPress sites
- **Site owners** who want to make changes without learning WordPress or hiring a developer

## Core Principles

- **Standard WordPress underneath** — uses standard theme APIs, hooks, template hierarchy, and block editor. Not a proprietary page builder.
- **Sections are the unit of work** — the theme is built around composable sections (hero, features, testimonials, CTA, gallery, etc.) that AI can create, modify, and rearrange.
- **Nothing goes live without approval** — all AI-generated changes land in a staging/preview state first.
- **Progressive enhancement** — works as a normal WordPress theme even without AI features enabled.
