# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Astro-based link-in-bio website that displays a personal profile with clickable links. It's a single-page application that renders static HTML from structured JSON data.

## Development Commands

```bash
# Install dependencies
npm install

# Start development server (localhost:4321)
npm run dev

# Build for production (output: ./dist/)
npm run build

# Preview production build locally
npm run preview

# Run Astro CLI commands
npm run astro ...
npm run astro -- --help
```

## Deployment

The site is configured for GitHub Pages deployment at `https://taipinc.github.io/my-links/`

### Automatic Deployment
- Pushing to the `master` branch automatically triggers deployment via GitHub Actions
- The workflow is defined in `.github/workflows/deploy.yml`
- Build artifacts are deployed to GitHub Pages

### Manual Steps (First Time Setup)
1. Go to repository Settings > Pages
2. Under "Build and deployment", set Source to "GitHub Actions"
3. Push changes to master branch
4. GitHub Actions will automatically build and deploy

## Architecture

### Data-Driven Design

The entire site content is driven by a single JSON file at `src/data/links.json` containing:
- Profile information (name, bio, subtitle, avatar)
- pinnedLinks array: Featured items that always appear first
- links array: Temporal/secondary items that appear after pinned items

The Astro component renders pinnedLinks first, then a horizontal separator, then links - maintaining clear visual separation between permanent and temporal content.

### Single-Page Structure

The site consists of one Astro page (`src/pages/index.astro`) that:
1. Imports data from `links.json`
2. Renders profile header with avatar, name, and bio
3. Maps over pinnedLinks array, then separator, then links array
4. Conditionally renders content based on `style` property (text, image-caption, image-fill, or default)
5. Includes all styles inline using `<style>` tags

### Asset Handling

- User-facing images go in `public/` directory (avatar, thumbnails, favicon)
- Images are referenced with absolute paths (e.g., `/avatar.png`)
- Icons use external CDNs:
  - Brand logos: Simple Icons (`https://cdn.simpleicons.org/[brand]`)
  - Generic icons: Lucide Icons (`https://cdn.jsdelivr.net/npm/lucide-static@latest/icons/[icon-name].svg`)
  - Material Symbols: Google Material Icons (use `materialIcon` property with icon name)
- Browse icons at:
  - Lucide: https://lucide.dev/icons/
  - Simple Icons: https://simpleicons.org/
  - Material Symbols: https://fonts.google.com/icons

## Configuration

- `astro.config.mjs`: Minimal Astro configuration (currently empty export)
- `tsconfig.json`: Extends Astro's strict TypeScript config
- No additional integrations or plugins configured

## Typography

The site uses Adobe Typekit font "area-normal-light" loaded via `@import`:
- Font weights available: 300 (light), 400 (normal), 700 (bold)
- Font styles: normal, italic
- Body default: font-weight 300
- Headings and emphasis: font-weight 400 or 700

## Editing Content

To update the website content, edit `src/data/links.json`:
- Profile section: Update name, bio (first line), subtitle (second line, optional), or avatar path
- pinnedLinks array: Items that always appear first (e.g., Portfolio, Contact, CV, social links)
- links array: Temporal or secondary content that appears after pinned items

A horizontal separator is rendered between pinnedLinks and links sections.

Link objects support the following properties:
- `title`: Link title text (supports HTML formatting)
- `url`: Destination URL
- `layout`: Layout size (row, tiny-box, small-box, wide-box, tall-box, big-box)
- `style`: Content presentation style (see below)
- `thumbnail`, `circleColor`, `materialIcon`, or `icon`: Visual element (optional, depends on style)
  - `thumbnail`: Image URL (from CDN or local)
  - `circleColor`: Hex color code for filled circle (e.g., "#E42A1D")
  - `materialIcon`: Google Material Symbol name (e.g., "alternate_email", "home")
  - `icon`: Emoji or text character (legacy)
- `caption`: Caption text for image-caption style (supports HTML formatting)
- `description`: Body text for text style (supports HTML formatting)

### Text Formatting

All text fields (title, bio, subtitle, description, caption) support basic HTML formatting:
- **Italic**: `<em>text</em>` or `<i>text</i>`
- **Bold**: `<strong>text</strong>` or `<b>text</b>`
- **Underline**: `<u>text</u>`
- Can be combined: `<strong><em>bold italic</em></strong>`

### Bento Box Layout System

The site uses a CSS Grid bento-box layout with a 6-column system where the icon-box serves as the base unit. 7 layout options:

- **`icon-box`**: Base unit - compact icon-only square (1 column, 1:1 aspect ratio) - for social media links, hides text
- **`tiny-box`**: Compact horizontal card (3 columns, ~88px height) - half-width
- **`small-box`**: Square box (3 columns, 1:1 aspect ratio) - half-width
- **`row`**: Full-width horizontal card (6 columns, ~88px height) - traditional link-in-bio style
- **`wide-box`**: Wide horizontal box (6 columns, 2:1 aspect ratio)
- **`tall-box`**: Tall vertical box (3 columns spanning 2 rows, aligns with 2 small-boxes + gap)
- **`big-box`**: Large square box (6 columns × 2 rows, 1:1 aspect ratio)

The 6-column grid with `grid-auto-flow: dense` enables optimal packing. Icon-boxes are 1/6 width, allowing up to 6 social icons per row. Small-boxes and tiny-boxes are 3/6 (half-width), so 2 fit per row. Each link item's size and proportions are controlled by the `layout` property and CSS `aspect-ratio`.

### Content Style System

The `style` property controls how content is arranged within each box:

- **`default`** (or omitted): Icon/thumbnail + title centered in box - traditional link card style
- **`text`**: Large text display with title and description, top-left aligned. Ideal for quotes or text-heavy content.
- **`image-caption`**: Image fills top portion with title and caption below. Good for showcasing visual work with context.
- **`image-fill`**: Full-bleed image covering entire box with title overlaid at bottom. Maximum visual impact.
