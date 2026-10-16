# Setup

This repo holds only the portfolio pages. It contains no product code.

## 1. Site address

| Setting | Value |
|---|---|
| GitHub user (`github_user`) | `ramybassem` |
| Repository (`repo_name`) | `ramy.github.io` |
| `url` | `https://ramybassem.github.io` |
| `baseurl` | `/ramy.github.io` |
| Project-site URL | `https://ramybassem.github.io/ramy.github.io/` |

The repository is named `ramy.github.io`, not `ramybassem.github.io`, so GitHub Pages treats it as a project site under the user domain. `url` is the domain only. `baseurl` is the project path.

Product pages read `site.github_user` for the private source-code links. `README.md` is the Jekyll home page (`layout: home`, `permalink: /`). GitHub hides that front matter when it renders the repo README. Image paths in the README are relative (`assets/img/...`) so they resolve both on GitHub and on the project-site home page. Portfolio and product links in the README are absolute, because GitHub does not run `relative_url`. `SETUP.md` stays out of the Jekyll build.

Planned private product repos, linked from each product page under "Source code" (they show a 404 to visitors by design):
`scrollscripture`, `wellness-compass`, `tntcalc`, `memsynk`, `aigloservices`.

### Renaming this repo to a user site

If the repository is renamed from `ramy.github.io` to `ramybassem.github.io`, GitHub Pages serves it at the root of the user site. Update `_config.yml`:

```yaml
repo_name: ramybassem.github.io
url: "https://ramybassem.github.io"
baseurl: ""
```

Leave `github_user` as `ramybassem`.

Every internal link and image on the Jekyll pages uses the `relative_url` filter, for example `{{ '/products/tntcalc/' | relative_url }}` and `{{ '/assets/img/tntcalc/app-icon.png' | relative_url }}`. With a project `baseurl` of `/ramy.github.io`, those become `/ramy.github.io/...`. With `baseurl: ""`, the same tags become root paths such as `/products/tntcalc/`. No page file needs a link edit after the rename.

`SETUP.md` is not run through Jekyll. `README.md` is. After a rename, update the absolute `https://ramybassem.github.io/ramy.github.io/` links in `README.md` to `https://ramybassem.github.io/`. Relative image paths on the home page keep working.

Minima's header, stylesheet, and feed links already use `relative_url`, so the navigation keeps working with either `baseurl`.

## 2. Publish with GitHub Pages

1. Push these files to the `main` branch of the public repo `ramy.github.io`.
2. In the repo, open Settings > Pages.
3. Under "Build and deployment", choose "Deploy from a branch", branch `main`, folder `/ (root)`, and save.
4. After the Pages build finishes, the site is at `https://ramybassem.github.io/ramy.github.io/`.

## 3. Before you publish

- Brand art lives in `assets/img/<product>/` and is the product's real public logo or app icon. Each product page has a Brand section with that mark and palette swatches. Accent colors are front matter (`accent`, `accent_ink`, `accent_soft`) and are applied in `assets/main.scss`.
- Keep it separate from work: no employer logos, data, tools, or internal details anywhere in this repo.
- Optional local preview: install Ruby, run `bundle install`, then `bundle exec jekyll serve`. Open `http://127.0.0.1:4000/ramy.github.io/`.

## Brand sources

Colors were taken from public sites where one was reachable. Nothing here came from a private source.

- **ScrollScripture.** From `https://scrollscripture.life` (scrollscripture.com did not connect). Header logo: `https://scrollscripture.life/brand/scrollscripture-symbol.png` (1024×1024), saved as `symbol.png` and used on the home card, hero, and Brand section. App icon also available at `https://scrollscripture.life/icons/icon-512.png`. Theme `#091311`. CSS `--gold` `#e9c46a`, `--ink` `#f8f3e8`, plus `#c49845` and `#9fc3a9`. Fonts: Inter for body, Georgia for headings.
- **TNTCalc.** App icon `https://tntcalc.com/assets/apple-touch-icon.png` (180×180). Theme `#0f766e`. CSS also uses `#1e3a8a`, `#f59e0b`, `#eef2f5`, `#17202a`. Font: Inter. No og:image.
- **MemSynk.** Official square icon from the MemSynk brand folder, saved as `assets/img/memsynk/icon.png` (256×256). A larger brand image is saved as `MemSynk.png`. The public favicon is not used. Theme `#08101d`, teal `#55dfdb`, violet `#b394f6`, blue `#53a6f8`, ink `#101827`. Font: Inter.
- **Aiglo Services.** Eagle logo `https://aigloservices.ca/wp-content/uploads/2026/06/logo.png` (1024×1024). The header uses a smaller eagle head, `https://aigloservices.ca/wp-content/uploads/2026/06/logo-2.png` (130×130). No favicon, apple-touch-icon, manifest icon, or og:image. Kit colors `#004A99`, `#007BFF`, `#1A1A1A`, `#333333`. Font: Roboto.
- **Wellness Compass.** No public site. The iOS app icon was supplied locally and saved as `app-icon.png` (512×512, downsized from 1024). Palette: `#173f35`, `#12473B`, `#245b4c`, `#EBF5F0`, `#f2f4ef`, `#17251f`.
