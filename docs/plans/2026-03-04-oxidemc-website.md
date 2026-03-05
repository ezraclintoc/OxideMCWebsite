# OxideMC Website Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a two-page Astro static site for OxideMC — a marketing landing page at `/` and a documentation hub at `/docs`, serving `install.sh` at `/install` for curl piping.

**Architecture:** Astro static site with zero framework islands. All interactivity (OS detection, GitHub stars, terminal animation, scroll effects, tabs, clipboard) handled by vanilla JS script tags in Astro components. Global CSS with CSS custom properties for theming.

**Tech Stack:** Astro 4.x, vanilla JS, CSS custom properties, no UI framework, no CSS framework.

**Reference design doc:** `docs/plans/2026-03-04-oxidemc-website-design.md`

---

### Task 1: Scaffold Astro Project

**Files:**
- Create: `website/` (project root for the Astro site)
- Create: `website/astro.config.mjs`
- Create: `website/package.json`
- Create: `website/tsconfig.json`

**Step 1: Initialize Astro project**

```bash
cd /home/ezra/Desktop/Projects/Websites/OxideMCWebsite
npm create astro@latest website -- --template minimal --no-install --typescript strict
cd website
npm install
```

**Step 2: Verify dev server starts**

```bash
npm run dev
```
Expected: Server starts at `http://localhost:4321`, no errors in terminal.

**Step 3: Replace default `astro.config.mjs`**

```js
// website/astro.config.mjs
import { defineConfig } from 'astro/config';

export default defineConfig({
  site: 'https://oxidemc.dev',
});
```

**Step 4: Create directory structure**

```bash
mkdir -p src/layouts src/components src/styles public
```

**Step 5: Commit**

```bash
git add website/
git commit -m "chore: scaffold astro project"
```

---

### Task 2: Global Styles & CSS Variables

**Files:**
- Create: `website/src/styles/global.css`

**Step 1: Write global CSS**

```css
/* website/src/styles/global.css */
:root {
  --bg: #0d0d0d;
  --bg-alt: #111111;
  --surface: #1a1a1a;
  --surface-hover: #222222;
  --accent: #e8622a;
  --accent-light: #f0a07a;
  --text: #e8e8e8;
  --text-muted: #888888;
  --code-bg: #161616;
  --border: #2a2a2a;
  --radius: 8px;
  --radius-lg: 12px;
  --font-mono: 'JetBrains Mono', 'Fira Code', 'Cascadia Code', monospace;
  --font-sans: 'Inter', system-ui, -apple-system, sans-serif;
  --max-width: 1100px;
  --transition: 150ms ease;
}

*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html {
  scroll-behavior: smooth;
}

body {
  background: var(--bg);
  color: var(--text);
  font-family: var(--font-sans);
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
}

a {
  color: var(--accent);
  text-decoration: none;
  transition: color var(--transition);
}

a:hover {
  color: var(--accent-light);
}

code, pre {
  font-family: var(--font-mono);
}

pre {
  background: var(--code-bg);
  border: 1px solid var(--border);
  border-radius: var(--radius);
  padding: 1.25rem 1.5rem;
  overflow-x: auto;
  font-size: 0.9rem;
  line-height: 1.7;
}

h1, h2, h3, h4 {
  line-height: 1.2;
  font-weight: 700;
}

.container {
  max-width: var(--max-width);
  margin: 0 auto;
  padding: 0 1.5rem;
}

section {
  padding: 5rem 0;
}

@media (max-width: 768px) {
  section {
    padding: 3rem 0;
  }
}
```

**Step 2: Verify build passes**

```bash
npm run build
```
Expected: Build completes with no errors.

**Step 3: Commit**

```bash
git add src/styles/global.css
git commit -m "feat: add global CSS variables and base styles"
```

---

### Task 3: Base Layout

**Files:**
- Create: `website/src/layouts/Base.astro`

**Step 1: Write the Base layout**

```astro
---
// website/src/layouts/Base.astro
interface Props {
  title: string;
  description?: string;
}

const { title, description = 'Set up your Minecraft server in seconds. OxideMC is a high-performance, Rust-powered Minecraft server setup tool.' } = Astro.props;
---

<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content={description} />
    <title>{title}</title>
    <link rel="icon" type="image/x-icon" href="/favicon.ico" />
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet" />
  </head>
  <body>
    <nav class="nav">
      <div class="container nav__inner">
        <a href="/" class="nav__logo">
          <img src="/logo.png" alt="OxideMC" width="28" height="28" />
          <span>OxideMC</span>
        </a>
        <div class="nav__links">
          <a href="/docs">Docs</a>
          <a href="https://github.com/ezraclintoc/OxideMC" target="_blank" rel="noopener" class="nav__github">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2C6.477 2 2 6.477 2 12c0 4.42 2.865 8.17 6.839 9.49.5.092.682-.217.682-.482 0-.237-.008-.866-.013-1.7-2.782.604-3.369-1.34-3.369-1.34-.454-1.156-1.11-1.464-1.11-1.464-.908-.62.069-.608.069-.608 1.003.07 1.531 1.03 1.531 1.03.892 1.529 2.341 1.087 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.11-4.555-4.943 0-1.091.39-1.984 1.029-2.683-.103-.253-.446-1.27.098-2.647 0 0 .84-.269 2.75 1.025A9.578 9.578 0 0 1 12 6.836c.85.004 1.705.115 2.504.337 1.909-1.294 2.747-1.025 2.747-1.025.546 1.377.202 2.394.1 2.647.64.699 1.028 1.592 1.028 2.683 0 3.842-2.339 4.687-4.566 4.935.359.309.678.919.678 1.852 0 1.336-.012 2.415-.012 2.741 0 .267.18.578.688.48C19.138 20.167 22 16.418 22 12c0-5.523-4.477-10-10-10z"/></svg>
            GitHub
          </a>
        </div>
      </div>
    </nav>

    <slot />

    <footer class="footer">
      <div class="container footer__inner">
        <p>Made with <span style="color: var(--accent);">♥</span> by <a href="https://github.com/ezraclintoc" target="_blank" rel="noopener">ezraclintoc</a></p>
        <p>Licensed under <a href="https://github.com/ezraclintoc/OxideMC/blob/main/LICENSE" target="_blank" rel="noopener">GPL v3</a></p>
      </div>
    </footer>
  </body>
</html>

<style is:global>
  @import '../styles/global.css';

  .nav {
    position: sticky;
    top: 0;
    z-index: 100;
    background: rgba(13, 13, 13, 0.85);
    backdrop-filter: blur(12px);
    border-bottom: 1px solid var(--border);
  }

  .nav__inner {
    display: flex;
    align-items: center;
    justify-content: space-between;
    height: 60px;
  }

  .nav__logo {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    color: var(--text);
    font-weight: 700;
    font-size: 1.1rem;
  }

  .nav__logo:hover {
    color: var(--accent-light);
  }

  .nav__links {
    display: flex;
    align-items: center;
    gap: 1.5rem;
  }

  .nav__links a {
    color: var(--text-muted);
    font-size: 0.95rem;
    transition: color var(--transition);
  }

  .nav__links a:hover {
    color: var(--text);
  }

  .nav__github {
    display: flex;
    align-items: center;
    gap: 0.35rem;
  }

  .footer {
    border-top: 1px solid var(--border);
    padding: 2rem 0;
  }

  .footer__inner {
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: var(--text-muted);
    font-size: 0.9rem;
    flex-wrap: wrap;
    gap: 0.5rem;
  }
</style>
```

**Step 2: Verify build passes**

```bash
npm run build
```
Expected: Build completes with no errors.

**Step 3: Commit**

```bash
git add src/layouts/Base.astro
git commit -m "feat: add base layout with nav and footer"
```

---

### Task 4: Hero Component

**Files:**
- Create: `website/src/components/Hero.astro`

**Step 1: Write Hero component**

```astro
---
// website/src/components/Hero.astro
---

<section class="hero">
  <div class="container hero__inner">
    <div class="hero__text">
      <div class="hero__badge">
        <span class="hero__badge-dot"></span>
        Rust-powered · Blazing fast
      </div>
      <h1 class="hero__title">
        Set up your Minecraft server
        <span class="hero__title-accent"> in seconds</span>
      </h1>
      <p class="hero__subtitle">
        OxideMC is an interactive CLI tool that downloads, configures, and launches
        Vanilla, Paper, or Fabric servers — with a single command.
      </p>

      <div class="hero__cta">
        <a id="os-download-btn" href="https://github.com/ezraclintoc/OxideMC/releases/latest" class="btn btn--primary" target="_blank" rel="noopener">
          Download for <span id="os-name">your platform</span>
        </a>
        <a href="/docs" class="btn btn--secondary">Read the docs</a>
      </div>

      <div class="hero__stars">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="var(--accent)"><path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z"/></svg>
        <span id="stars-count">—</span> stars on GitHub
      </div>
    </div>

    <div class="hero__terminal">
      <div class="terminal">
        <div class="terminal__header">
          <span class="terminal__dot terminal__dot--red"></span>
          <span class="terminal__dot terminal__dot--yellow"></span>
          <span class="terminal__dot terminal__dot--green"></span>
          <span class="terminal__title">bash</span>
        </div>
        <div class="terminal__body">
          <div class="terminal__line">
            <span class="terminal__prompt">$</span>
            <span class="terminal__typed" id="terminal-text"></span><span class="terminal__cursor">|</span>
          </div>
          <div class="terminal__output" id="terminal-output" style="display:none">
            <span style="color: var(--accent);">Setting up your Minecraft Server</span><br/>
            <span style="color: var(--text-muted);">? What do you want to name your server?</span> <span style="color:var(--text)">minecraft-server</span><br/>
            <span style="color: var(--text-muted);">? Which software do you want to use?</span> <span style="color:var(--accent-light)">Paper (Recommended)</span><br/>
            <span style="color: var(--text-muted);">? Which version?</span> <span style="color:var(--text)">1.21.4</span><br/>
            <span style="color: #4ade80;">Downloading server.jar... 100%</span><br/>
            <span style="color: #4ade80;">You're all set!</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

<style>
  .hero {
    padding: 6rem 0 5rem;
    background: radial-gradient(ellipse at 20% 50%, rgba(232, 98, 42, 0.08) 0%, transparent 60%);
  }

  .hero__inner {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 4rem;
    align-items: center;
  }

  .hero__badge {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    background: rgba(232, 98, 42, 0.1);
    border: 1px solid rgba(232, 98, 42, 0.3);
    border-radius: 100px;
    padding: 0.35rem 1rem;
    font-size: 0.85rem;
    color: var(--accent-light);
    margin-bottom: 1.5rem;
  }

  .hero__badge-dot {
    width: 6px;
    height: 6px;
    background: var(--accent);
    border-radius: 50%;
    animation: pulse 2s infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.4; }
  }

  .hero__title {
    font-size: clamp(2rem, 4vw, 3rem);
    margin-bottom: 1rem;
    line-height: 1.15;
  }

  .hero__title-accent {
    color: var(--accent);
  }

  .hero__subtitle {
    color: var(--text-muted);
    font-size: 1.1rem;
    margin-bottom: 2rem;
    line-height: 1.7;
  }

  .hero__cta {
    display: flex;
    gap: 1rem;
    flex-wrap: wrap;
    margin-bottom: 1.5rem;
  }

  .btn {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.75rem 1.5rem;
    border-radius: var(--radius);
    font-weight: 600;
    font-size: 0.95rem;
    transition: all var(--transition);
    cursor: pointer;
    border: none;
  }

  .btn--primary {
    background: var(--accent);
    color: white;
  }

  .btn--primary:hover {
    background: var(--accent-light);
    color: white;
    transform: translateY(-1px);
  }

  .btn--secondary {
    background: var(--surface);
    color: var(--text);
    border: 1px solid var(--border);
  }

  .btn--secondary:hover {
    background: var(--surface-hover);
    color: var(--text);
    border-color: var(--accent);
  }

  .hero__stars {
    display: flex;
    align-items: center;
    gap: 0.4rem;
    color: var(--text-muted);
    font-size: 0.9rem;
  }

  /* Terminal */
  .terminal {
    background: var(--code-bg);
    border: 1px solid var(--border);
    border-radius: var(--radius-lg);
    overflow: hidden;
    box-shadow: 0 20px 60px rgba(0,0,0,0.5);
  }

  .terminal__header {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.75rem 1rem;
    background: var(--surface);
    border-bottom: 1px solid var(--border);
  }

  .terminal__dot {
    width: 12px;
    height: 12px;
    border-radius: 50%;
  }

  .terminal__dot--red { background: #ff5f57; }
  .terminal__dot--yellow { background: #ffbd2e; }
  .terminal__dot--green { background: #28ca41; }

  .terminal__title {
    margin-left: auto;
    margin-right: auto;
    font-size: 0.8rem;
    color: var(--text-muted);
    font-family: var(--font-mono);
  }

  .terminal__body {
    padding: 1.25rem 1.5rem;
    font-family: var(--font-mono);
    font-size: 0.875rem;
    line-height: 1.8;
    min-height: 200px;
  }

  .terminal__line {
    display: flex;
    align-items: flex-start;
    gap: 0.6rem;
  }

  .terminal__prompt {
    color: var(--accent);
    user-select: none;
  }

  .terminal__cursor {
    display: inline-block;
    animation: blink 1s infinite;
    color: var(--accent);
    font-weight: 300;
  }

  @keyframes blink {
    0%, 100% { opacity: 1; }
    50% { opacity: 0; }
  }

  .terminal__output {
    margin-top: 0.75rem;
    line-height: 1.9;
  }

  @media (max-width: 768px) {
    .hero__inner {
      grid-template-columns: 1fr;
      gap: 2.5rem;
    }
  }
</style>

<script>
  // OS detection for download button
  function detectOS() {
    const ua = navigator.userAgent;
    if (ua.includes('Win')) return { name: 'Windows', file: 'oxidemc-windows-x86_64.exe' };
    if (ua.includes('Mac')) return { name: 'macOS', file: 'oxidemc-macos-arm64' };
    return { name: 'Linux', file: 'oxidemc-linux-x86_64' };
  }

  const os = detectOS();
  const btn = document.getElementById('os-download-btn');
  const osName = document.getElementById('os-name');
  if (btn && osName) {
    osName.textContent = os.name;
    btn.href = `https://github.com/ezraclintoc/OxideMC/releases/latest/download/${os.file}`;
  }

  // GitHub stars
  fetch('https://api.github.com/repos/ezraclintoc/OxideMC')
    .then(r => r.json())
    .then(data => {
      const el = document.getElementById('stars-count');
      if (el && data.stargazers_count !== undefined) {
        el.textContent = data.stargazers_count.toLocaleString();
      }
    })
    .catch(() => {});

  // Terminal typing animation
  const commands = [
    'curl -sL https://oxidemc.dev/install | bash',
    'oxidemc install',
    'oxidemc configure',
  ];

  let cmdIndex = 0;
  let charIndex = 0;
  let isDeleting = false;
  const typedEl = document.getElementById('terminal-text');
  const outputEl = document.getElementById('terminal-output');

  function type() {
    if (!typedEl) return;
    const current = commands[cmdIndex];

    if (!isDeleting) {
      typedEl.textContent = current.slice(0, charIndex + 1);
      charIndex++;

      if (charIndex === current.length) {
        // Show output only for first command
        if (cmdIndex === 0 && outputEl) {
          setTimeout(() => { outputEl.style.display = 'block'; }, 400);
        }
        setTimeout(() => {
          if (outputEl) outputEl.style.display = 'none';
          isDeleting = true;
          setTimeout(type, 1200);
        }, 2500);
        return;
      }
    } else {
      typedEl.textContent = current.slice(0, charIndex - 1);
      charIndex--;

      if (charIndex === 0) {
        isDeleting = false;
        cmdIndex = (cmdIndex + 1) % commands.length;
        setTimeout(type, 400);
        return;
      }
    }

    setTimeout(type, isDeleting ? 40 : 70);
  }

  setTimeout(type, 800);
</script>
```

**Step 2: Verify build passes**

```bash
npm run build
```
Expected: Build completes with no errors.

**Step 3: Commit**

```bash
git add src/components/Hero.astro
git commit -m "feat: add hero component with terminal animation and OS detection"
```

---

### Task 5: Features Component

**Files:**
- Create: `website/src/components/Features.astro`

**Step 1: Write Features component**

```astro
---
// website/src/components/Features.astro

const features = [
  {
    icon: '🪄',
    title: 'Interactive Setup Wizard',
    desc: 'Easy and advanced modes guide you through every step — from naming your server to selecting a version.',
  },
  {
    icon: '🎮',
    title: 'Multi-Platform Support',
    desc: 'Download Vanilla, Paper, or Fabric servers. More platforms coming soon.',
  },
  {
    icon: '📥',
    title: 'Automatic Downloads',
    desc: 'Fetches the latest server JAR files directly with a real-time progress bar.',
  },
  {
    icon: '⚙️',
    title: 'Fully Customizable',
    desc: 'Set server name, directory, port, gamemode, difficulty, and PVP — all from the CLI.',
  },
  {
    icon: '📋',
    title: 'Version Selection',
    desc: 'Browse and choose from all available Minecraft versions without leaving the terminal.',
  },
  {
    icon: '🦀',
    title: 'Blazing Fast',
    desc: 'Built with Rust for maximum performance, minimal memory usage, and instant startup.',
  },
];
---

<section class="features">
  <div class="container">
    <h2 class="features__heading">Everything you need to run a server</h2>
    <p class="features__subheading">No config files, no Java flags, no searching StackOverflow.</p>
    <div class="features__grid">
      {features.map(f => (
        <div class="feature-card" data-animate>
          <div class="feature-card__icon">{f.icon}</div>
          <h3 class="feature-card__title">{f.title}</h3>
          <p class="feature-card__desc">{f.desc}</p>
        </div>
      ))}
    </div>
  </div>
</section>

<style>
  .features {
    background: var(--bg-alt);
  }

  .features__heading {
    font-size: clamp(1.5rem, 3vw, 2rem);
    text-align: center;
    margin-bottom: 0.75rem;
  }

  .features__subheading {
    text-align: center;
    color: var(--text-muted);
    margin-bottom: 3rem;
  }

  .features__grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 1.25rem;
  }

  .feature-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius-lg);
    padding: 1.75rem;
    transition: border-color var(--transition), transform var(--transition);
    opacity: 0;
    transform: translateY(20px);
  }

  .feature-card.visible {
    opacity: 1;
    transform: translateY(0);
    transition: opacity 0.5s ease, transform 0.5s ease, border-color var(--transition);
  }

  .feature-card:hover {
    border-color: var(--accent);
    transform: translateY(-2px);
  }

  .feature-card__icon {
    font-size: 2rem;
    margin-bottom: 1rem;
  }

  .feature-card__title {
    font-size: 1rem;
    margin-bottom: 0.5rem;
  }

  .feature-card__desc {
    color: var(--text-muted);
    font-size: 0.9rem;
    line-height: 1.6;
  }
</style>

<script>
  const cards = document.querySelectorAll('[data-animate]');
  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry, i) => {
        if (entry.isIntersecting) {
          setTimeout(() => entry.target.classList.add('visible'), i * 80);
          observer.unobserve(entry.target);
        }
      });
    },
    { threshold: 0.1 }
  );
  cards.forEach(card => observer.observe(card));
</script>
```

**Step 2: Verify build passes**

```bash
npm run build
```
Expected: Build completes with no errors.

**Step 3: Commit**

```bash
git add src/components/Features.astro
git commit -m "feat: add features grid with scroll animations"
```

---

### Task 6: Quick Start Component

**Files:**
- Create: `website/src/components/QuickStart.astro`

**Step 1: Write QuickStart component**

```astro
---
// website/src/components/QuickStart.astro

const tabs = [
  {
    id: 'script',
    label: 'Install Script',
    code: 'curl -sL https://oxidemc.dev/install | bash',
    note: 'Installs the latest binary to ~/.local/bin',
  },
  {
    id: 'binary',
    label: 'Download Binary',
    code: `# Linux x86_64
wget https://github.com/ezraclintoc/OxideMC/releases/latest/download/oxidemc-linux-x86_64
chmod +x oxidemc-linux-x86_64 && mv oxidemc-linux-x86_64 ~/.local/bin/oxidemc

# macOS ARM64
wget https://github.com/ezraclintoc/OxideMC/releases/latest/download/oxidemc-macos-arm64
chmod +x oxidemc-macos-arm64 && mv oxidemc-macos-arm64 ~/.local/bin/oxidemc`,
    note: 'Download the pre-built binary for your platform',
  },
  {
    id: 'source',
    label: 'Build from Source',
    code: `git clone https://github.com/ezraclintoc/OxideMC.git
cd OxideMC
cargo build --release
./target/release/oxidemc`,
    note: 'Requires Rust and Cargo installed',
  },
];
---

<section class="quickstart">
  <div class="container">
    <h2 class="quickstart__heading">Get started in 30 seconds</h2>
    <p class="quickstart__subheading">Three ways to install OxideMC — pick your preference.</p>

    <div class="tabs">
      <div class="tabs__list" role="tablist">
        {tabs.map((tab, i) => (
          <button
            class={`tabs__btn ${i === 0 ? 'tabs__btn--active' : ''}`}
            role="tab"
            data-tab={tab.id}
            aria-selected={i === 0}
          >
            {tab.label}
          </button>
        ))}
      </div>

      {tabs.map((tab, i) => (
        <div
          class={`tabs__panel ${i === 0 ? 'tabs__panel--active' : ''}`}
          data-panel={tab.id}
          role="tabpanel"
        >
          <div class="code-block">
            <div class="code-block__header">
              <span class="code-block__lang">bash</span>
              <button class="code-block__copy" data-code={tab.code}>
                <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="9" y="9" width="13" height="13" rx="2" ry="2"/><path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"/></svg>
                Copy
              </button>
            </div>
            <pre><code>{tab.code}</code></pre>
          </div>
          <p class="tabs__note">{tab.note}</p>
        </div>
      ))}
    </div>
  </div>
</section>

<style>
  .quickstart__heading {
    font-size: clamp(1.5rem, 3vw, 2rem);
    text-align: center;
    margin-bottom: 0.75rem;
  }

  .quickstart__subheading {
    text-align: center;
    color: var(--text-muted);
    margin-bottom: 2.5rem;
  }

  .tabs {
    max-width: 700px;
    margin: 0 auto;
  }

  .tabs__list {
    display: flex;
    gap: 0.25rem;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 0.25rem;
    margin-bottom: 1rem;
  }

  .tabs__btn {
    flex: 1;
    padding: 0.5rem 1rem;
    background: none;
    border: none;
    color: var(--text-muted);
    border-radius: calc(var(--radius) - 2px);
    cursor: pointer;
    font-size: 0.9rem;
    font-weight: 500;
    transition: all var(--transition);
  }

  .tabs__btn--active, .tabs__btn:hover {
    background: var(--accent);
    color: white;
  }

  .tabs__panel {
    display: none;
  }

  .tabs__panel--active {
    display: block;
  }

  .tabs__note {
    color: var(--text-muted);
    font-size: 0.85rem;
    margin-top: 0.75rem;
    text-align: center;
  }

  .code-block {
    background: var(--code-bg);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    overflow: hidden;
  }

  .code-block__header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0.5rem 1rem;
    background: var(--surface);
    border-bottom: 1px solid var(--border);
  }

  .code-block__lang {
    font-family: var(--font-mono);
    font-size: 0.8rem;
    color: var(--text-muted);
  }

  .code-block__copy {
    display: flex;
    align-items: center;
    gap: 0.4rem;
    background: none;
    border: 1px solid var(--border);
    color: var(--text-muted);
    padding: 0.25rem 0.6rem;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.8rem;
    transition: all var(--transition);
  }

  .code-block__copy:hover {
    border-color: var(--accent);
    color: var(--accent);
  }

  .code-block pre {
    border: none;
    border-radius: 0;
    margin: 0;
  }
</style>

<script>
  // Tab switching
  document.querySelectorAll('.tabs__btn').forEach(btn => {
    btn.addEventListener('click', () => {
      const tab = (btn as HTMLElement).dataset.tab;
      document.querySelectorAll('.tabs__btn').forEach(b => {
        b.classList.remove('tabs__btn--active');
        b.setAttribute('aria-selected', 'false');
      });
      document.querySelectorAll('.tabs__panel').forEach(p => p.classList.remove('tabs__panel--active'));
      btn.classList.add('tabs__btn--active');
      btn.setAttribute('aria-selected', 'true');
      document.querySelector(`[data-panel="${tab}"]`)?.classList.add('tabs__panel--active');
    });
  });

  // Copy to clipboard
  document.querySelectorAll('.code-block__copy').forEach(btn => {
    btn.addEventListener('click', async () => {
      const code = (btn as HTMLElement).dataset.code || '';
      try {
        await navigator.clipboard.writeText(code);
        const orig = btn.innerHTML;
        btn.innerHTML = '✓ Copied!';
        setTimeout(() => { btn.innerHTML = orig; }, 2000);
      } catch {}
    });
  });
</script>
```

**Step 2: Verify build passes**

```bash
npm run build
```
Expected: Build completes with no errors.

**Step 3: Commit**

```bash
git add src/components/QuickStart.astro
git commit -m "feat: add quick start section with tabs and copy-to-clipboard"
```

---

### Task 7: Server Types Component

**Files:**
- Create: `website/src/components/ServerTypes.astro`

**Step 1: Write ServerTypes component**

```astro
---
// website/src/components/ServerTypes.astro

const servers = [
  {
    name: 'Vanilla',
    badge: null,
    desc: 'The official Mojang server. Pure, unmodified Minecraft experience.',
    pros: ['No extra software required', 'Always up to date', 'Great for small private servers'],
    icon: '🟩',
  },
  {
    name: 'Paper',
    badge: 'Recommended',
    desc: 'High-performance fork with plugin support and major optimizations.',
    pros: ['Plugin ecosystem (Bukkit/Spigot)', 'Significantly better performance', 'Active community'],
    icon: '📄',
  },
  {
    name: 'Fabric',
    badge: null,
    desc: 'Lightweight modding toolchain for client and server mods.',
    pros: ['Rich mod ecosystem', 'Fast update cycle', 'Works great with OptiFine alternatives'],
    icon: '🧵',
  },
];
---

<section class="server-types">
  <div class="container">
    <h2 class="server-types__heading">Pick your platform</h2>
    <p class="server-types__subheading">OxideMC supports the most popular server types out of the box.</p>
    <div class="server-types__grid">
      {servers.map(server => (
        <div class={`server-card ${server.badge ? 'server-card--featured' : ''}`}>
          {server.badge && <div class="server-card__badge">{server.badge}</div>}
          <div class="server-card__icon">{server.icon}</div>
          <h3 class="server-card__name">{server.name}</h3>
          <p class="server-card__desc">{server.desc}</p>
          <ul class="server-card__pros">
            {server.pros.map(pro => (
              <li>
                <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="var(--accent)" stroke-width="2.5"><polyline points="20 6 9 17 4 12"/></svg>
                {pro}
              </li>
            ))}
          </ul>
        </div>
      ))}
    </div>
    <p class="server-types__coming-soon">Forge support coming soon.</p>
  </div>
</section>

<style>
  .server-types {
    background: var(--bg-alt);
  }

  .server-types__heading {
    font-size: clamp(1.5rem, 3vw, 2rem);
    text-align: center;
    margin-bottom: 0.75rem;
  }

  .server-types__subheading {
    text-align: center;
    color: var(--text-muted);
    margin-bottom: 3rem;
  }

  .server-types__coming-soon {
    text-align: center;
    color: var(--text-muted);
    font-size: 0.9rem;
    margin-top: 2rem;
  }

  .server-types__grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 1.25rem;
  }

  .server-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius-lg);
    padding: 2rem;
    position: relative;
    transition: border-color var(--transition);
  }

  .server-card--featured {
    border-color: var(--accent);
  }

  .server-card:hover {
    border-color: var(--accent-light);
  }

  .server-card__badge {
    position: absolute;
    top: -12px;
    left: 50%;
    transform: translateX(-50%);
    background: var(--accent);
    color: white;
    font-size: 0.75rem;
    font-weight: 700;
    padding: 0.2rem 0.8rem;
    border-radius: 100px;
    white-space: nowrap;
  }

  .server-card__icon {
    font-size: 2rem;
    margin-bottom: 1rem;
  }

  .server-card__name {
    font-size: 1.2rem;
    margin-bottom: 0.5rem;
  }

  .server-card__desc {
    color: var(--text-muted);
    font-size: 0.9rem;
    margin-bottom: 1.25rem;
    line-height: 1.6;
  }

  .server-card__pros {
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .server-card__pros li {
    display: flex;
    align-items: center;
    gap: 0.6rem;
    font-size: 0.875rem;
    color: var(--text-muted);
  }
</style>
```

**Step 2: Verify build passes**

```bash
npm run build
```
Expected: Build completes with no errors.

**Step 3: Commit**

```bash
git add src/components/ServerTypes.astro
git commit -m "feat: add server types comparison section"
```

---

### Task 8: Home Page

**Files:**
- Modify: `website/src/pages/index.astro`

**Step 1: Write home page**

```astro
---
// website/src/pages/index.astro
import Base from '../layouts/Base.astro';
import Hero from '../components/Hero.astro';
import Features from '../components/Features.astro';
import QuickStart from '../components/QuickStart.astro';
import ServerTypes from '../components/ServerTypes.astro';
---

<Base title="OxideMC — Minecraft Server Setup Tool">
  <main>
    <Hero />
    <Features />
    <QuickStart />
    <ServerTypes />
  </main>
</Base>
```

**Step 2: Verify the page builds and renders correctly**

```bash
npm run dev
```
Open `http://localhost:4321` and verify:
- Hero renders with terminal animation
- Features grid visible
- Quick Start tabs functional
- Server Types cards visible

**Step 3: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: assemble home page"
```

---

### Task 9: Docs Page

**Files:**
- Create: `website/src/pages/docs.astro`

**Step 1: Write the docs page**

```astro
---
// website/src/pages/docs.astro
import Base from '../layouts/Base.astro';

const roadmap = {
  'Server Types': [
    { done: true, text: 'Paper server support' },
    { done: true, text: 'Vanilla server support' },
    { done: true, text: 'Fabric server support' },
    { done: false, text: 'Forge server support' },
  ],
  'Core Features': [
    { done: true, text: 'Interactive setup wizard (easy/advanced modes)' },
    { done: true, text: 'Version selection from available MC versions' },
    { done: true, text: 'Automatic JAR downloads with progress' },
    { done: true, text: 'Path expansion (~, ./, ../)' },
    { done: true, text: 'Server start after download with EULA' },
    { done: false, text: 'Add more server configuration options (RAM, JVM flags)' },
    { done: true, text: 'Server port configuration' },
    { done: true, text: 'Server name configuration' },
  ],
  'Mod Management': [
    { done: false, text: 'Create start script (cross-platform)' },
    { done: false, text: 'Modrinth support' },
    { done: false, text: 'CurseForge support' },
    { done: false, text: 'Add mods by URL' },
  ],
  'Distribution': [
    { done: true, text: 'GitHub Actions for automated builds' },
    { done: false, text: 'Host install script on domain' },
    { done: false, text: 'Add more platforms for binary releases' },
  ],
  'Polish': [
    { done: true, text: 'Configuration file support' },
    { done: false, text: 'Update checker' },
    { done: false, text: 'Server properties editor' },
  ],
};
---

<Base title="Docs — OxideMC" description="OxideMC documentation: installation, CLI reference, server types, configuration, and roadmap.">
  <div class="docs-layout">
    <aside class="sidebar" id="sidebar">
      <button class="sidebar__close" id="sidebar-close" aria-label="Close menu">✕</button>
      <nav class="sidebar__nav">
        <div class="sidebar__section">
          <p class="sidebar__section-label">Getting Started</p>
          <a href="#installation" class="sidebar__link">Installation</a>
          <a href="#quick-start" class="sidebar__link">Quick Start</a>
        </div>
        <div class="sidebar__section">
          <p class="sidebar__section-label">Usage</p>
          <a href="#cli-reference" class="sidebar__link">CLI Reference</a>
          <a href="#server-types" class="sidebar__link">Server Types</a>
          <a href="#configuration" class="sidebar__link">Configuration</a>
        </div>
        <div class="sidebar__section">
          <p class="sidebar__section-label">Other</p>
          <a href="#roadmap" class="sidebar__link">Roadmap</a>
          <a href="#contributing" class="sidebar__link">Contributing</a>
          <a href="#license" class="sidebar__link">License</a>
        </div>
      </nav>
    </aside>

    <div class="docs-overlay" id="docs-overlay"></div>

    <main class="docs-content">
      <button class="docs-menu-btn" id="docs-menu-btn" aria-label="Open menu">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="3" y1="12" x2="21" y2="12"/><line x1="3" y1="6" x2="21" y2="6"/><line x1="3" y1="18" x2="21" y2="18"/></svg>
        Menu
      </button>

      <article class="prose">

        <h1>OxideMC Documentation</h1>
        <p>OxideMC is a Rust-powered CLI tool that sets up Minecraft servers interactively — no manual config required.</p>

        <hr />

        <h2 id="installation">Installation</h2>

        <h3>Option 1: Install Script (Recommended)</h3>
        <div class="code-block">
          <div class="code-block__header">
            <span>bash</span>
            <button class="copy-btn" data-code="curl -sL https://oxidemc.dev/install | bash">Copy</button>
          </div>
          <pre><code>curl -sL https://oxidemc.dev/install | bash</code></pre>
        </div>
        <p>Installs the latest binary to <code>~/.local/bin/oxidemc</code>. Make sure <code>~/.local/bin</code> is in your <code>$PATH</code>.</p>

        <h3>Option 2: Download Binary</h3>
        <table>
          <thead><tr><th>Platform</th><th>File</th></tr></thead>
          <tbody>
            <tr><td>Linux x86_64</td><td><a href="https://github.com/ezraclintoc/OxideMC/releases/latest" target="_blank" rel="noopener">oxidemc-linux-x86_64</a></td></tr>
            <tr><td>macOS ARM64</td><td><a href="https://github.com/ezraclintoc/OxideMC/releases/latest" target="_blank" rel="noopener">oxidemc-macos-arm64</a></td></tr>
            <tr><td>Windows x86_64</td><td><a href="https://github.com/ezraclintoc/OxideMC/releases/latest" target="_blank" rel="noopener">oxidemc-windows-x86_64.exe</a></td></tr>
          </tbody>
        </table>

        <h3>Option 3: Build from Source</h3>
        <div class="code-block">
          <div class="code-block__header">
            <span>bash</span>
            <button class="copy-btn" data-code={`git clone https://github.com/ezraclintoc/OxideMC.git\ncd OxideMC\ncargo build --release\n./target/release/oxidemc`}>Copy</button>
          </div>
          <pre><code>{`git clone https://github.com/ezraclintoc/OxideMC.git\ncd OxideMC\ncargo build --release\n./target/release/oxidemc`}</code></pre>
        </div>
        <p>Requires <a href="https://rustup.rs" target="_blank" rel="noopener">Rust</a> and Cargo installed.</p>

        <hr />

        <h2 id="quick-start">Quick Start</h2>
        <p>Run OxideMC with no arguments to launch the interactive wizard:</p>
        <div class="code-block">
          <div class="code-block__header"><span>bash</span><button class="copy-btn" data-code="oxidemc">Copy</button></div>
          <pre><code>oxidemc</code></pre>
        </div>
        <p>The wizard will ask you for:</p>
        <ol>
          <li><strong>Server name</strong> — used as the directory name</li>
          <li><strong>Directory</strong> — where to save the server files (supports <code>~</code>)</li>
          <li><strong>Platform</strong> — Vanilla, Paper, or Fabric</li>
          <li><strong>Version</strong> — choose from all available Minecraft versions</li>
          <li><strong>EULA acceptance</strong> — required to start the server</li>
        </ol>
        <p>After setup, OxideMC downloads the server JAR, runs it once to generate config files, and you're done.</p>

        <hr />

        <h2 id="cli-reference">CLI Reference</h2>
        <table>
          <thead>
            <tr><th>Command</th><th>Aliases</th><th>Description</th></tr>
          </thead>
          <tbody>
            <tr><td><code>oxidemc</code></td><td>—</td><td>Launch interactive mode (install or configure)</td></tr>
            <tr><td><code>oxidemc install</code></td><td><code>-i</code>, <code>--install</code>, <code>i</code></td><td>Run the interactive setup wizard</td></tr>
            <tr><td><code>oxidemc configure</code></td><td><code>-c</code>, <code>--configure</code>, <code>c</code></td><td>Configure an existing server</td></tr>
          </tbody>
        </table>

        <hr />

        <h2 id="server-types">Server Types</h2>

        <h3>Vanilla</h3>
        <p>The official Mojang server — unmodified, always up to date. Best for small private servers where you don't need plugins or mods.</p>

        <h3>Paper</h3>
        <p>A high-performance fork with plugin support (Bukkit/Spigot API). Significantly faster than Vanilla for large player counts. <strong>Recommended for most use cases.</strong></p>

        <h3>Fabric</h3>
        <p>A lightweight modding toolchain. Supports server-side mods from Modrinth and CurseForge. Best for technical modpacks.</p>

        <h3>Forge</h3>
        <p><em>Coming soon.</em></p>

        <hr />

        <h2 id="configuration">Configuration</h2>
        <p>Run <code>oxidemc configure</code> (or <code>oxidemc -c</code>) and point it at your existing server directory. OxideMC will auto-detect the platform and present a menu.</p>

        <h3>Available settings</h3>
        <table>
          <thead><tr><th>Setting</th><th>Options</th></tr></thead>
          <tbody>
            <tr><td>Difficulty</td><td>peaceful, easy, normal, hard</td></tr>
            <tr><td>Gamemode</td><td>survival, creative, adventure, spectator</td></tr>
            <tr><td>PVP</td><td>true, false</td></tr>
          </tbody>
        </table>
        <p>Changes are written to <code>server.properties</code> in your server directory.</p>

        <hr />

        <h2 id="roadmap">Roadmap</h2>
        {Object.entries(roadmap).map(([category, items]) => (
          <div>
            <h3>{category}</h3>
            <ul class="roadmap-list">
              {items.map(item => (
                <li class={item.done ? 'roadmap-list__item--done' : ''}>
                  <span class="roadmap-list__check">{item.done ? '✅' : '⬜'}</span>
                  {item.text}
                </li>
              ))}
            </ul>
          </div>
        ))}

        <hr />

        <h2 id="contributing">Contributing</h2>
        <p>Contributions are welcome! Please open an issue on <a href="https://github.com/ezraclintoc/OxideMC/issues" target="_blank" rel="noopener">GitHub Issues</a> before submitting a pull request to discuss the change.</p>

        <hr />

        <h2 id="license">License</h2>
        <p>OxideMC is licensed under the <a href="https://github.com/ezraclintoc/OxideMC/blob/main/LICENSE" target="_blank" rel="noopener">GNU General Public License v3.0</a>.</p>

      </article>
    </main>
  </div>
</Base>

<style>
  .docs-layout {
    display: flex;
    min-height: calc(100vh - 60px);
  }

  /* Sidebar */
  .sidebar {
    width: 240px;
    flex-shrink: 0;
    position: sticky;
    top: 60px;
    height: calc(100vh - 60px);
    overflow-y: auto;
    border-right: 1px solid var(--border);
    padding: 2rem 1rem;
    background: var(--bg);
  }

  .sidebar__close {
    display: none;
  }

  .sidebar__section {
    margin-bottom: 1.5rem;
  }

  .sidebar__section-label {
    font-size: 0.75rem;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 0.08em;
    color: var(--text-muted);
    margin-bottom: 0.5rem;
    padding: 0 0.5rem;
  }

  .sidebar__link {
    display: block;
    padding: 0.35rem 0.5rem;
    border-radius: 4px;
    color: var(--text-muted);
    font-size: 0.9rem;
    transition: all var(--transition);
  }

  .sidebar__link:hover, .sidebar__link.active {
    color: var(--text);
    background: var(--surface);
  }

  /* Docs content */
  .docs-content {
    flex: 1;
    padding: 3rem 4rem;
    max-width: 800px;
    min-width: 0;
  }

  .docs-menu-btn {
    display: none;
    align-items: center;
    gap: 0.5rem;
    background: var(--surface);
    border: 1px solid var(--border);
    color: var(--text);
    padding: 0.5rem 1rem;
    border-radius: var(--radius);
    cursor: pointer;
    font-size: 0.9rem;
    margin-bottom: 2rem;
  }

  /* Overlay */
  .docs-overlay {
    display: none;
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.6);
    z-index: 49;
  }

  /* Prose */
  .prose h1 {
    font-size: 2rem;
    margin-bottom: 0.75rem;
  }

  .prose h2 {
    font-size: 1.4rem;
    margin-top: 2.5rem;
    margin-bottom: 1rem;
    padding-top: 2rem;
    border-top: 1px solid var(--border);
  }

  .prose h2:first-of-type {
    border-top: none;
    margin-top: 1.5rem;
  }

  .prose h3 {
    font-size: 1.1rem;
    margin-top: 1.5rem;
    margin-bottom: 0.75rem;
    color: var(--accent-light);
  }

  .prose p {
    color: var(--text-muted);
    line-height: 1.75;
    margin-bottom: 1rem;
  }

  .prose ol, .prose ul {
    color: var(--text-muted);
    padding-left: 1.5rem;
    line-height: 1.8;
    margin-bottom: 1rem;
  }

  .prose code {
    background: var(--surface);
    border: 1px solid var(--border);
    padding: 0.15rem 0.4rem;
    border-radius: 4px;
    font-size: 0.875em;
    color: var(--accent-light);
  }

  .prose hr {
    border: none;
    border-top: 1px solid var(--border);
    margin: 2rem 0;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.9rem;
    margin-bottom: 1.5rem;
  }

  th {
    text-align: left;
    padding: 0.6rem 1rem;
    background: var(--surface);
    border: 1px solid var(--border);
    color: var(--text-muted);
    font-size: 0.8rem;
    text-transform: uppercase;
    letter-spacing: 0.05em;
  }

  td {
    padding: 0.6rem 1rem;
    border: 1px solid var(--border);
    color: var(--text-muted);
  }

  /* Code blocks in docs */
  .code-block {
    background: var(--code-bg);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    overflow: hidden;
    margin-bottom: 1rem;
  }

  .code-block__header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0.4rem 1rem;
    background: var(--surface);
    border-bottom: 1px solid var(--border);
    font-family: var(--font-mono);
    font-size: 0.8rem;
    color: var(--text-muted);
  }

  .copy-btn {
    background: none;
    border: 1px solid var(--border);
    color: var(--text-muted);
    padding: 0.2rem 0.5rem;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.75rem;
    transition: all var(--transition);
  }

  .copy-btn:hover {
    border-color: var(--accent);
    color: var(--accent);
  }

  .code-block pre {
    border: none;
    border-radius: 0;
    margin: 0;
  }

  /* Roadmap */
  .roadmap-list {
    list-style: none;
    padding: 0;
    margin-bottom: 1.5rem;
  }

  .roadmap-list li {
    display: flex;
    align-items: center;
    gap: 0.6rem;
    padding: 0.35rem 0;
    color: var(--text-muted);
    font-size: 0.9rem;
  }

  .roadmap-list__item--done {
    color: var(--text) !important;
  }

  /* Mobile sidebar */
  @media (max-width: 768px) {
    .sidebar {
      position: fixed;
      top: 0;
      left: 0;
      height: 100vh;
      z-index: 50;
      transform: translateX(-100%);
      transition: transform 0.25s ease;
      padding-top: 3.5rem;
    }

    .sidebar.open {
      transform: translateX(0);
    }

    .sidebar__close {
      display: block;
      position: absolute;
      top: 1rem;
      right: 1rem;
      background: none;
      border: none;
      color: var(--text-muted);
      font-size: 1.2rem;
      cursor: pointer;
    }

    .docs-overlay.open {
      display: block;
    }

    .docs-menu-btn {
      display: flex;
    }

    .docs-content {
      padding: 2rem 1.5rem;
    }
  }
</style>

<script>
  // Sidebar toggle
  const sidebar = document.getElementById('sidebar');
  const overlay = document.getElementById('docs-overlay');
  const menuBtn = document.getElementById('docs-menu-btn');
  const closeBtn = document.getElementById('sidebar-close');

  function openSidebar() {
    sidebar?.classList.add('open');
    overlay?.classList.add('open');
  }

  function closeSidebar() {
    sidebar?.classList.remove('open');
    overlay?.classList.remove('open');
  }

  menuBtn?.addEventListener('click', openSidebar);
  closeBtn?.addEventListener('click', closeSidebar);
  overlay?.addEventListener('click', closeSidebar);

  // Active link on scroll
  const headings = document.querySelectorAll('article h2[id]');
  const links = document.querySelectorAll('.sidebar__link');

  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          links.forEach(l => l.classList.remove('active'));
          const active = document.querySelector(`.sidebar__link[href="#${entry.target.id}"]`);
          active?.classList.add('active');
        }
      });
    },
    { rootMargin: '-20% 0% -70% 0%' }
  );

  headings.forEach(h => observer.observe(h));

  // Copy buttons
  document.querySelectorAll('.copy-btn').forEach(btn => {
    btn.addEventListener('click', async () => {
      const code = (btn as HTMLElement).dataset.code || '';
      try {
        await navigator.clipboard.writeText(code);
        const orig = btn.textContent;
        btn.textContent = 'Copied!';
        setTimeout(() => { btn.textContent = orig; }, 2000);
      } catch {}
    });
  });
</script>
```

**Step 2: Verify the docs page builds**

```bash
npm run build
```
Expected: Build completes with no errors.

**Step 3: Check the page visually**

```bash
npm run dev
```
Open `http://localhost:4321/docs` and verify:
- Sidebar visible on desktop
- Hamburger menu works on mobile
- All sections scroll into view
- Roadmap checklist renders correctly

**Step 4: Commit**

```bash
git add src/pages/docs.astro
git commit -m "feat: add full docs page with sidebar navigation"
```

---

### Task 10: Serve install.sh at /install

**Files:**
- Create: `website/public/install` (copy of `install.sh`, no extension)

**Step 1: Copy the install script**

```bash
cp /home/ezra/Desktop/Projects/Websites/OxideMCWebsite/ezraclintoc-oxidemc-8a5edab282632443.txt /tmp/ref.txt
# The actual install.sh is in the OxideMC repo. Extract it into public/install:
cat > website/public/install << 'INSTALLEOF'
#!/bin/bash

set -e

REPO="ezraclintoc/OxideMC"
INSTALL_DIR="${HOME}/.local/bin"
BINARY_NAME="oxidemc"

detect_os() {
    case "$(uname -s)" in
        Linux*)     echo "linux";;
        Darwin*)    echo "macos";;
        *)          echo "unknown";;
    esac
}

detect_arch() {
    case "$(uname -m)" in
        x86_64)     echo "x86_64";;
        aarch64|arm64) echo "arm64";;
        *)          echo "unknown";;
    esac
}

echo "⬇️  Downloading OxideMC..."

LATEST_TAG=$(curl -sL "https://api.github.com/repos/${REPO}/releases/latest" | grep '"tag_name"' | cut -d '"' -f4)

if [ -z "$LATEST_TAG" ]; then
    echo "❌ Error: Could not find latest release"
    exit 1
fi

OS=$(detect_os)
ARCH=$(detect_arch)

if [ "$OS" = "unknown" ]; then
    echo "❌ Error: Unsupported OS"
    exit 1
fi

if [ "$OS" = "macos" ]; then
    if [ "$ARCH" = "arm64" ]; then
        FILENAME="oxidemc-macos-arm64"
    else
        FILENAME="oxidemc-x86_64"
    fi
else
    FILENAME="oxidemc-linux-${ARCH}"
fi

DOWNLOAD_URL="https://github.com/${REPO}/releases/download/${LATEST_TAG}/${FILENAME}"

mkdir -p "$INSTALL_DIR"

echo "📦 Downloading from: $DOWNLOAD_URL"
curl -sL "$DOWNLOAD_URL" -o "${INSTALL_DIR}/${BINARY_NAME}"
chmod +x "${INSTALL_DIR}/${BINARY_NAME}"

echo "✅ Installed to ${INSTALL_DIR}/${BINARY_NAME}"
echo ""
echo "Run 'oxidemc' to start!"
INSTALLEOF
```

**Step 2: Verify it builds and is accessible**

```bash
npm run build
# The file should appear in dist/install
ls dist/install
```
Expected: `dist/install` exists.

**Step 3: Verify curl works locally**

```bash
npm run preview &
sleep 2
curl -sL http://localhost:4321/install | head -5
```
Expected: First lines of the shell script, starting with `#!/bin/bash`.

**Step 4: Commit**

```bash
git add public/install
git commit -m "feat: serve install.sh at /install for curl pipe"
```

---

### Task 11: Add logo and favicon

**Files:**
- Create: `website/public/favicon.svg` (generated SVG favicon as fallback)

**Step 1: Copy logo from OxideMC repo if available, else create SVG placeholder**

If `logo.png` exists in the OxideMC repo, copy it to `website/public/logo.png`.

Otherwise, create a simple SVG placeholder at `website/public/logo.svg` and update `Base.astro` to reference it:

```svg
<!-- website/public/logo.svg -->
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 64 64" width="64" height="64">
  <rect width="64" height="64" rx="12" fill="#e8622a"/>
  <text x="32" y="44" text-anchor="middle" font-family="monospace" font-weight="bold" font-size="32" fill="white">O</text>
</svg>
```

**Step 2: Create favicon**

```bash
# Use the SVG as favicon directly
cp website/public/logo.svg website/public/favicon.svg
```

Update `Base.astro` favicon line if using SVG:
```html
<link rel="icon" type="image/svg+xml" href="/favicon.svg" />
```

**Step 3: Verify build**

```bash
npm run build
```
Expected: Build passes with no errors.

**Step 4: Commit**

```bash
git add public/
git commit -m "feat: add logo and favicon"
```

---

### Task 12: Final verification

**Step 1: Production build**

```bash
npm run build
```
Expected: Build completes, `dist/` directory created, no errors or warnings.

**Step 2: Preview production build**

```bash
npm run preview
```
Open `http://localhost:4321` and verify:
- [ ] Hero terminal animation runs
- [ ] OS detection shows correct platform in download button
- [ ] GitHub stars loads (may show — if rate limited)
- [ ] Features cards animate in on scroll
- [ ] Quick Start tabs switch correctly
- [ ] Copy buttons work
- [ ] Server Types cards render
- [ ] Nav links work
- [ ] `/docs` page loads
- [ ] Docs sidebar scrollspy highlights correct section
- [ ] Mobile hamburger menu works (resize browser)
- [ ] `curl -sL http://localhost:4321/install | head -1` returns `#!/bin/bash`

**Step 3: Final commit**

```bash
git add .
git commit -m "feat: complete OxideMC website v1"
```
