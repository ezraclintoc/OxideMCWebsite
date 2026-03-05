# OxideMC Website Design

**Date:** 2026-03-04

## Overview

A two-page Astro static site for OxideMC — a Rust-powered Minecraft server setup CLI tool. The site serves as both a marketing landing page and a documentation hub. It also serves the `install.sh` script at `/install` to support `curl -sL https://oxidemc.dev/install | bash`.

## Tech Stack

- **Framework:** Astro (static site generation)
- **Interactivity:** Vanilla JS only — no framework islands needed
- **Styling:** Global CSS with CSS variables, no CSS framework

## Color Palette

| Token | Value | Usage |
|-------|-------|-------|
| Background | `#0d0d0d` / `#111111` | Page background |
| Surface | `#1a1a1a` | Cards, code blocks |
| Primary accent | `#e8622a` | CTAs, highlights, icons |
| Secondary accent | `#f0a07a` | Hover states, secondary highlights |
| Text primary | `#e8e8e8` | Body text |
| Text muted | `#888888` | Secondary text, captions |
| Code background | `#161616` | Code block backgrounds |

## File Structure

```
oxidemc-website/
├── public/
│   ├── install          # raw install.sh served at /install
│   ├── logo.png
│   └── favicon.ico
├── src/
│   ├── pages/
│   │   ├── index.astro  # landing page
│   │   └── docs.astro   # documentation page
│   ├── layouts/
│   │   └── Base.astro   # shared HTML shell, nav, footer
│   ├── components/
│   │   ├── Hero.astro
│   │   ├── Features.astro
│   │   ├── QuickStart.astro
│   │   ├── ServerTypes.astro
│   │   └── DocsSidebar.astro
│   └── styles/
│       └── global.css
└── astro.config.mjs
```

## Pages

### Home (`/`)

**Hero Section**
- Headline: "Set up your Minecraft server in seconds"
- Subheadline referencing Rust speed and multi-platform support
- Animated terminal block cycling through the curl install command with blinking cursor
- OS-detected CTA button: JS reads `navigator.userAgent` and links to the correct binary (Linux/macOS/Windows), with a "other platforms" dropdown
- Live GitHub stars counter fetched from GitHub API on page load

**Features Section**
- 6 cards in a responsive grid with icon, title, short description
- Features: Interactive Wizard, Multi-Platform Support, Auto Downloads, Customizable, Version Selection, Blazing Fast
- Cards animate in with fade+slide on scroll via Intersection Observer

**Quick Start Section**
- 3 tabs: "Install Script", "Download Binary", "Build from Source"
- Styled terminal code blocks per tab with copy-to-clipboard button
- Default tab: "Install Script" showing the curl command

**Server Types Section**
- 3 cards: Vanilla, Paper (with "Recommended" badge), Fabric
- Short description of trade-offs per type

**Footer**
- GitHub link, GPL v3 license note, "Made by ezraclintoc"

### Docs (`/docs`)

**Layout**
- Two-column: fixed left sidebar + scrollable right content
- Sidebar collapses to hamburger menu on mobile

**Sidebar Navigation**
- Getting Started: Installation, Quick Start
- Usage: CLI Reference, Server Types, Configuration
- Other: Roadmap, Contributing, License

**Content Sections**
- **Installation** — curl method, binary download table, build from source; all with styled code blocks
- **Quick Start** — step-by-step interactive wizard walkthrough
- **CLI Reference** — table of args: `install`/`-i`, `configure`/`-c`, interactive fallback
- **Server Types** — Vanilla, Paper, Fabric with trade-offs; Forge as "coming soon"
- **Configuration** — `configure` command: difficulty, gamemode, PVP
- **Roadmap** — TODO list rendered as styled checklist with checked/unchecked states
- **Contributing** — brief section linking to GitHub Issues
- **License** — GPL v3 with link

## Interactive Features (Vanilla JS)

| Feature | Implementation |
|---------|---------------|
| OS detection | `navigator.userAgent` check on page load, swap download button href |
| GitHub stars | `fetch('https://api.github.com/repos/ezraclintoc/OxideMC')` on load |
| Terminal animation | CSS keyframes + JS text cycling with blinking cursor |
| Scroll animations | Intersection Observer API on feature cards |
| Tab switcher | Simple JS class toggle for Quick Start tabs |
| Copy to clipboard | `navigator.clipboard.writeText()` on code blocks |
| Docs sidebar mobile | CSS + JS hamburger toggle |

## Install Script Serving

`install.sh` is copied to `public/install` (no extension). Astro's static output serves it as raw text at `/install`, making `curl -sL https://oxidemc.dev/install | bash` work correctly.
