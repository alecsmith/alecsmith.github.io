# alecsmith.github.io

Personal academic site for Alec Smith. Built with Jekyll and the jekyll-scholar plugin.

---

## Site architecture

```
_bibliography/        BibTeX files
  publications.bib    published papers (rendered on research page)
  workingpapers.bib   working papers (section currently commented out in research/index.html)
_layouts/
  bibtemplate.html    controls how each bibliography entry is rendered
  default.html        base page layout
  mainpage.html       homepage layout
_site/                Jekyll build output — do not edit directly
publications/         PDF files served at /publications/<citekey>.pdf
research/
  index.html          the Research page
index.html            homepage
_config.yml           Jekyll + jekyll-scholar configuration
rakefile              build + publish script
```

The site uses two git branches:
- `source` — the working branch; all edits go here
- `master` — the built output pushed to GitHub Pages by `rake publish`

Never commit directly to `master`.

---

## How to publish changes

```bash
git add <files>
git commit -m "your message"
git push
rake publish
```

`rake publish` runs `jekyll build` internally (via the `:generate` task) before pushing `_site/` to the `master` branch. You do not need to run Jekyll separately.

For local preview, VS Code Live Server needs to serve from `_site/`, not the project root. This is configured in `.vscode/settings.json` (`liveServer.settings.root: "/_site"`). Raw source files (e.g. `research/index.html`) contain Liquid tags that only render after a Jekyll build.

---

## Adding a publication

1. Add the BibTeX entry to `_bibliography/publications.bib`. The sort order is by year then month (descending), so include a `month` field if ordering within a year matters.
2. Drop the PDF into `publications/<citekey>.pdf`. jekyll-scholar automatically generates a PDF button for any entry whose citekey matches a file in that folder (configured via `repository: /publications` in `_config.yml`).
3. If the paper was previously in `workingpapers.bib`, remove it from there.
4. Publish: `git add . && git commit -m "..." && git push && rake publish`.

---

## Custom citation formatting

The bibliography template is `_layouts/bibtemplate.html`. It currently supports two optional custom BibTeX fields:

### `note`
Appended after the APA-formatted reference. Useful for forthcoming status, special issue info, etc. Supports HTML (e.g. `<i>italic text</i>`).

Example:
```bibtex
note = {Forthcoming in the special issue of <i>Journal of Economic Behavior \& Organization</i> in honor of Gary Charness.}
```

### `suppress_journal`
When set to `true`, the auto-generated italic journal name is stripped from the APA reference before the `note` is appended. Use this when you want full control over how the journal appears (e.g. only inside the note, formatted differently).

Example (forthcoming paper where journal should only appear in the note):
```bibtex
@article{dufwenberg2026promises,
  author           = {Dufwenberg, Martin and Li, Flora and Smith, Alec},
  title            = {Promises and Punishment},
  journal          = {Journal of Economic Behavior \& Organization},
  year             = {2026},
  month            = {6},
  suppress_journal = {true},
  note             = {Forthcoming in the special issue of <i>Journal of Economic Behavior \& Organization</i> in honor of Gary Charness.}
}
```

Renders as: Dufwenberg, M., Li, F., & Smith, A. (2026). Promises and Punishment. Forthcoming in the special issue of *Journal of Economic Behavior & Organization* in honor of Gary Charness.

---

## jekyll-scholar

This site relies heavily on jekyll-scholar. Key references:
- Documentation: https://github.com/inukshuk/jekyll-scholar
- Configuration options are in `_config.yml` under the `scholar:` key
- The template system uses Liquid; available variables in `bibtemplate.html` include `reference` (formatted APA string), `link` (auto-generated PDF URL from the repository folder), and `entry.<fieldname>` for any BibTeX field
- Citation style is APA (set via `style: apa` in `_config.yml`)
- URLs and DOIs are stripped from the rendered reference via a `split: "https://"` filter in the template
