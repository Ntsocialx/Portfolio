# Tiyani Nkuna, Portfolio

Personal portfolio for Tiyani Nkuna, final-year Informatics student at the
Tshwane University of Technology, aimed at **data science** and **junior
developer** roles.

The site is a single static page in a newspaper "broadsheet" style: newsprint
paper, ultramarine accent, Anton headlines. No build step, no framework.

## Project structure

```
Portfolio/
├── index.html              the site: markup, styles and scripts in one file
├── index.html.old          previous Tailwind version, kept for reference
├── README.md
├── .prettierrc.json        formatting rules
├── .editorconfig           editor defaults (2 spaces, LF, UTF-8)
├── assets/
│   └── images/             portrait and project images
├── certificates/           original certificate PDFs
│   └── previews/           rendered images used by the site
└── games/
    ├── snake/index.html
    ├── tiyani_run_v1.html
    ├── tiyani_run_v2.html
    └── assets/             game backgrounds and soundtrack
```

All file and folder names are snake_case.

## Previewing locally

From the repo root:

```bash
python3 -m http.server 8000
```

Then open <http://localhost:8000/>. Stop the server with `Ctrl+C`.

A local server is used instead of opening the file directly because browsers
restrict `file://` pages, which stops the games and live app previews from
loading in the popup.

## Page sections

| #   | Section     | What it shows                                                |
| --- | ----------- | ------------------------------------------------------------ |
|     | Masthead    | Dateline, fitted TIYANI NKUNA headline, portrait, sticker    |
| 01  | Story       | About, leadership and study                                  |
| 02  | Index       | Numbered project list with cursor-following image previews   |
| 03  | Case files  | Sideways-scrolling case studies (DFD, SQL, Arena, web apps)  |
| 04  | Skills      | Stock-table style skills with sparklines, plus the watchlist |
| 05  | Credentials | Certificates as taped newspaper cuttings                     |
|     | Contact     | Classifieds-style contact block                              |

## Editing content

Everything lives in `index.html`. Search for the `BLOCK:` or `SECTION:`
banner to find a part quickly.

- **Text, headlines, contact details:** edit the HTML under the matching
  section banner.
- **Projects:** edit the `PROJECTS` array (`BLOCK: project_data`). An entry
  with an `app` field opens in the popup as a live preview:

  | Field   | Meaning                                                            |
  | ------- | ------------------------------------------------------------------ |
  | `url`   | Where the app lives; also used by the Open app button              |
  | `embed` | `true` loads it in the popup; `false` shows the screenshot instead |
  | `kind`  | Label in the popup's top bar (`"Live app"` or `"Game"`)            |
  | `label` | Text on the open button                                            |

  Set `embed: false` for any site that refuses to be framed (it sends an
  `X-Frame-Options` or `frame-ancestors` header).

- **Certificates:** edit `CERTIFICATES` (`BLOCK: certificate_data`). Each
  entry's `slug` matches its files in `certificates/`.
- **Colours and fonts:** change the custom properties at the top of the
  `<style>` block.
- **Watchlist:** the "Learning now" ad in the Skills section. Solid tags are
  in progress now (Power BI, data modelling, data visualisation); a dashed
  `tag_next` tag marks something planned but not started. Move a tag from
  dashed to solid once study begins, and never list a tool as current before
  then.

## Popup viewer

One shared viewer handles everything that opens in a popup. It picks a mode
from the item it is given:

| Mode    | Used for                                   | Controls                                     |
| ------- | ------------------------------------------ | -------------------------------------------- |
| `image` | Credentials, case file images, screenshots | Click to zoom, drag to pan, prev / next      |
| `code`  | The Bookstore SQL case file                | Scrollable, selectable text, Copy            |
| `frame` | Live apps and games                        | Usable in place, Open app / Play full screen |

Closing the popup unloads any live frame, so a game's music stops with it.

## Certificates

Certificates are shown as images, never embedded PDFs: an embedded PDF brings
the browser's own toolbar into the design and looks different in every
browser. The original PDF stays one click away through the "Original PDF" link.

Each PDF in `certificates/` has two rendered images in `certificates/previews/`:

| File               | Used for                        |
| ------------------ | ------------------------------- |
| `<slug>.jpg`       | Full view in the popup (1800px) |
| `<slug>_thumb.jpg` | Cuttings on the page (640px)    |

To add a certificate, save the PDF as `certificates/<slug>.pdf`, then run from
`certificates/`:

```bash
pdftoppm -r 220 -singlefile -png <slug>.pdf /tmp/<slug>
magick /tmp/<slug>.png -resize "1800x1800>" -strip -quality 84 previews/<slug>.jpg
magick /tmp/<slug>.png -resize "640x640>" -strip -quality 78 previews/<slug>_thumb.jpg
```

- `pdftoppm` turns a PDF page into an image. `-r 220` is the resolution and
  `-singlefile` stops it adding a page number to the file name.
- `-resize "1800x1800>"` shrinks the longest side to at most 1800px. The `>`
  means it only ever shrinks, never enlarges.
- `-strip` drops metadata and `-quality` sets the JPEG compression.

Then add an entry to `CERTIFICATES` with the same `slug`.

## Code style

| Rule               | What it means here                                                                |
| ------------------ | --------------------------------------------------------------------------------- |
| snake_case         | JS variables and functions, CSS classes, ids and custom properties, file names    |
| Functional blocks  | No classes, no `this`. Small pure functions, `const` by default                   |
| One init per block | Each behaviour exposes one `init_*` function; the bottom of the script calls them |
| Local state only   | At most one small `state` object per block, never shared between blocks           |
| Documentation      | JSDoc (`@param`, `@returns`) on every function, banner comments for every section |
| Prettier           | 2-space indent, double quotes, semicolons, trailing commas, 80 column print width |

Names that belong to the browser (`addEventListener`, `IntersectionObserver`)
keep their own casing; only names we invent are snake_case.

Format or check with Prettier:

```bash
npx prettier --write index.html   # rewrite in place
npx prettier --check index.html   # report only, change nothing
```

## Accessibility and motion

- Every animation stops under `prefers-reduced-motion: reduce`.
- Popups close on `Esc`, keep keyboard focus inside while open, and return
  focus to whatever opened them.
- Layouts are mobile first and checked for no horizontal scroll at 375px.
