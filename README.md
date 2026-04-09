# DDEV AI harness proposal deck

Marp (Markdown) slides for discussing an **observable, measurable AI harness** with ddev maintainers. Source: [`deck.md`](deck.md).

## Tracking (upstream)

- **Discussion:** [ddev/ddev#8307 — Proposal: AI harness…](https://github.com/ddev/ddev/issues/8307)
- **Prebuilt PDF:** [Latest release `deck.pdf`](https://github.com/jonesrussell/ddev-ai-deck/releases/latest/download/deck.pdf) (also on the [Releases](https://github.com/jonesrussell/ddev-ai-deck/releases) page).

## Prerequisites

- Node.js 18+ (for `npx`), or install Marp CLI globally.

## Preview & export

From this directory:

```bash
# Open live preview in browser (watches file changes)
npx @marp-team/marp-cli --no-stdin deck.md --preview

# PDF (recommended for GitHub uploads / email)
npx @marp-team/marp-cli --no-stdin deck.md -o deck.pdf

# HTML (single file)
npx @marp-team/marp-cli --no-stdin deck.md -o deck.html
```

[`marp.config.js`](marp.config.js) sets `allowLocalFiles: true` so the embedded SVG in [`assets/harness-loop.svg`](assets/harness-loop.svg) resolves when exporting.

Use **`--no-stdin`** if the CLI waits on stdin in your environment.

Optional: install globally to shorten commands:

```bash
npm install -g @marp-team/marp-cli
marp --no-stdin deck.md -o deck.pdf
```

## Configuration

[`marp.config.js`](marp.config.js) enables local file resolution and HTML when needed. Slide size and theme are set in the YAML front matter of `deck.md` (`size: 16:9`).

## Presenter notes

- The closing **Summary** slide includes a short on-slide presenter hint; expand verbally with maintainer-specific examples (CI flake rate, review time, pilot area).
- For heavier “speaker notes” UX, use the [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode) extension’s presenter view.

## License

Deck content is proposal material for community discussion; apply the same license you use when contributing to [ddev/ddev](https://github.com/ddev/ddev) if you commit excerpts there.
