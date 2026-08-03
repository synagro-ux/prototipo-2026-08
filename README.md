# Synagro 8 — Standalone Export

A fully self-contained, single-file build of the Synagro 8 prototype (Inicio/Dashboard, Agricultura module, Mi Perfil, Configuración General, and shared Header/Sidebar/launcher components). Everything — markup, styles, and JavaScript — is inlined into `index.html`. No build step, no external framework, no dependency on Claude's design hosting.

## Run locally
Just open `index.html` directly in a browser, or serve the folder with any static server:

```
npx serve .
# or
python3 -m http.server 8080
```

Then visit `http://localhost:8080`.

## Deploy to GitHub Pages
1. Push this folder's contents to a GitHub repo (the contents of `github-pages-export/`, not the folder itself, should be at the root of the branch/path Pages serves).
2. In the repo: **Settings → Pages** → set Source to the branch and folder containing `index.html` (e.g. `main` / `/root`, or a dedicated `gh-pages` branch).
3. Save. GitHub will publish at `https://<user>.github.io/<repo>/` (or your custom domain).
4. No further config is required — the included `.nojekyll` file stops GitHub's Jekyll processing from touching the file (some Jekyll versions mangle files with leading underscores or Liquid-like syntax).

### Custom domain (optional)
Add a `CNAME` file next to `index.html` containing your domain, then configure DNS per GitHub's Pages docs.

## Deep links & routing
Navigation uses **hash-based routing** with one unique, readable hash per screen:

| Screen | Hash |
|---|---|
| Inicio | `#dashboard` |
| Agricultura (dashboard) | `#agriculture` |
| Agricultura → Operaciones (sin selección) | `#agriculture-operations` |
| Orden de Trabajo (listado) | `#work-orders` |
| Orden de Trabajo (nueva) | `#work-orders-create` |
| Orden de Trabajo (edición) | `#work-orders-edit` |
| Tareas por Lote | `#field-tasks` |
| Otro ítem de Operaciones | `#agriculture-operations-<id>` |
| Agricultura → Maestros | `#agriculture-masters` (o `#agriculture-masters-<id>`) |
| Agricultura → Informes (listado propio del módulo) | `#agriculture-reports` (o `#agriculture-reports-<id>`) |
| Reportes (hub) | `#reports` |
| Reporte específico | `#reports-<id>` |
| Agricultura → Ajustes | `#agriculture-settings` |
| Mi Perfil | `#profile` |
| Configuración General | `#settings` |
| Otro módulo del sidebar | `#<moduleId>` (ej. `#ganaderia`) |
| Informe standalone (link legado) | `#/informe/<moduleId>/<reportId>` |

Every hash is unique to its screen — no two screens share a hash.

Because the route lives entirely in the URL fragment (`#...`), it is **never sent to the server** — GitHub Pages (or any static host) always serves the same `index.html` regardless of what follows the `#`. This means:
- Direct navigation to a deep link (e.g. sharing `https://yoursite.github.io/repo/#agricultura/informes/margen-bruto-multicampania`) works out of the box.
- Refreshing the page on any deep link works out of the box.
- No server rewrite rules, `404.html` fallback trick, or `.htaccess` config are needed (those are only required for *path-based* routing, which this app does not use).

The app reads `location.hash` on load, on `hashchange`, and on `popstate` (browser Back/Forward), and pushes a new history entry (via `history.pushState`) whenever the active screen changes — so the address bar always reflects the current screen and Back/Forward navigate between previously visited screens.

## Assets & fonts
All icons are inline SVG. Fonts are loaded from Google Fonts CDN via a standard `<link>` — this is a public, free CDN unrelated to Claude's hosting, so the export has no dependency on any Claude infrastructure. If you need a fully offline build (no external requests at all), self-host the font files and swap the `<link>` for local `@font-face` rules.

## Notes
- This is a design prototype (mock data, no backend). See the project's `design_handoff_partial_v2/KNOWN_LIMITATIONS.md` if you have it, for details on what's mocked vs. real.
- All interactions, transitions, and responsive behavior from the live preview are preserved as-is in this export.
