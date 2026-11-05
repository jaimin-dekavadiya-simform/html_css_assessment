# Distribution

This repository is a build-less static site: a Flipkart-style storefront
built for the HTML & CSS assessment. "Distribution" here means copying the
tracked files to a static host and checking that the result works. There is
no bundler, no package manager and no server-side code.

## What gets distributed

| Path | Purpose | Needed to serve the page? |
| --- | --- | --- |
| `index.html` (27 KB) | the whole page markup | Yes |
| `style.css` (15 KB) | styles, breakpoints, themes | Yes |
| `images/` (33 files, 2.9 MB) | every picture on the page | Yes |
| `screenshots/` (3 PNGs, 1.2 MB) | embedded in the README only | No |
| `README.md` | project documentation | No |
| `distribution.md` | this document | No |

`index.html` holds the header, the nine-item category strip, the "Deals of
the Day" and "Gaming & More" sections and an empty footer. `style.css`
holds the whole design: the 450, 700, 800, 900 and 1300 px breakpoints and
the light and dark palettes.

Every asset URL in the page is relative (`style.css`, `images/...`), so keep
`index.html` together with `style.css` and `images/`; the folder can be
mounted at the site root or under any subdirectory.

## Requirements

- A modern browser. The WebP images need a 2020-or-later browser; every
  other file is plain HTML, CSS and JavaScript.
- Optional: a static file server such as `python3 -m http.server`, or the
  Live Server extension for VS Code.
- Nothing else: no Node.js or npm install, no database, no environment
  variables and no secrets.

## Distribution channels

### GitHub Pages (live demo)

The site is published at
<https://jaimin-dekavadiya-simform.github.io/html_css_assessment/>, the same
link as the "Live Demo" entry in the README.

The published copy is the root of the `develop` branch, so merging a pull
request into `develop` is the deploy; no workflow file is involved. If the
deployment ever stops matching `develop`, confirm the source under
Settings, then Pages.

Pages serves the branch with `cache-control: max-age=600`, so give it about
ten minutes, or hard-reload, after a push. To compare the published page
with the branch, run this in the repository root:

```bash
LIVE=https://jaimin-dekavadiya-simform.github.io/html_css_assessment/
curl -sS "$LIVE" -o /tmp/live.html
diff /tmp/live.html index.html
```

No output from `diff` means the published copy and `develop` agree.

### Local run or offline copy

```bash
git clone \
  https://github.com/jaimin-dekavadiya-simform/html_css_assessment
cd html_css_assessment
python3 -m http.server 8000
```

Then open <http://localhost:8000>. Opening `index.html` directly from the
file system also works, but some browsers block `localStorage` for `file://`
URLs; in that case the page follows the operating system theme and does not
remember a manual choice.

### Handoff or submission package

```bash
git archive --format=zip --prefix=html_css_assessment/ \
  -o html_css_assessment.zip HEAD \
  index.html style.css images README.md distribution.md
```

The archive holds the files needed to serve the page plus the two Markdown
docs: `screenshots/` is left out and `.git` is never included. Unpack the
zip into any static host as it is; the folder prefix keeps the relative
paths valid.

## External dependency

The page loads one file at runtime: the Font Awesome 7.0.1 CSS from cdnjs,
pinned with a SHA-512 subresource-integrity hash and sent with
`referrerpolicy="no-referrer"`. If cdnjs is unreachable, the icons
(including the theme-toggle glyph) do not render, but the layout, the text
and the toggle's `aria-label` still work. Nothing else is fetched.

## Branch and release flow

- Work on a feature branch and open a pull request into `develop`. Merging
  that pull request publishes the site.
- A separate pull request from `develop` into `main` snapshots a release on
  `main`, for example the commit "Release v1.2".
- `main` is not the published branch, so a release pull request does not
  change the live site. `main` currently lags `develop` and does not have
  the dark mode yet.

## Post-deploy checklist

- The live URL returns 200 and the served HTML matches `develop`, as the
  `diff` in the GitHub Pages section shows.
- The header, the category strip, "Deals of the Day" and "Gaming & More"
  render, and every image loads.
- The theme toggle switches between light and dark, and the choice survives
  a reload.
- There is no horizontal overflow at 375, 800 and 1280 px wide.
- A first visit with no saved choice follows the operating system theme.
- The browser console shows no errors.

## Not distributed

- `.git/`, which holds history and remotes.
- `screenshots/`, which only the README uses.
- Local editor and tooling files such as `.vscode/` or `.DS_Store`.
- No secrets or configuration files are involved at all.
