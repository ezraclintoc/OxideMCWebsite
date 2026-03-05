# OxideMC Website

Marketing site for [OxideMC](https://github.com/ezraclintoc/OxideMC) — a Rust-powered CLI tool for setting up Minecraft servers.

**Live:** https://oxide.clintoc.me

## Stack

- [Astro 5](https://astro.build) static site, no JS framework
- Vanilla CSS with custom properties
- Deployed via GitHub Pages

## Development

```bash
cd website
npm install
npm run dev       # http://localhost:4321
npm run build     # production build → dist/
npm run preview   # serve last build locally
```

## Project Structure

```
website/
  src/
    layouts/Base.astro       # Shared HTML shell
    pages/
      index.astro            # Landing page
      docs.astro             # Documentation page
    components/
      Hero.astro             # Hero section with terminal animation
      Features.astro         # Feature cards
      QuickStart.astro       # Install tab switcher
      ServerTypes.astro      # Server type cards
    styles/
      global.css             # CSS variables and reset
  public/
    install                  # Bash install script (served at /install)
```

## Deployment

Pushes to `main` automatically build and deploy via GitHub Actions. The `public/install` script is kept in sync with the source at [`../OxideMC/install.sh`](https://github.com/ezraclintoc/OxideMC/blob/main/install.sh).
