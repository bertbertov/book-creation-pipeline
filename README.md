# Book Creation Pipeline From Scratch

A collection of [Claude Code skills](https://docs.claude.com/en/docs/claude-code/skills) that take a book from a one-line concept to a published Kindle/Kobo/Google-Play listing — drafting, illustrating, compiling, audiobooking, publishing.

Works for one book, or a series. Self-help, novels, manifestos, memoirs.

Drop a folder into `~/.claude/skills/` (or your project's `.claude/skills/`) and the skill becomes discoverable by Claude Code.

## Skills in this repo

### `book-creation/`
End-to-end pipeline for producing illustrated books — single book or any-N-book series. Handles concept lock, voice and persona, parallel chapter drafting (multi-agent), cover generation (Nano Banana Pro), interior cartoon sketch generation (Flux Krea local), PDF/EPUB compile with hybrid layout (text-wrap + marquee), audiobook generation (Kokoro/F5-TTS), and KDP/Kobo/Google Play publishing. Works for memoirs, self-help, novels, manifestos, and pop-spirituality alike.

### `illustrated-book/`
Single-book layout details — the layer that the `book-creation` skill calls into for the per-book compile step. Two patterns:
- **Pattern A — Manifesto** (ReportLab) — full-bleed hero images, Roman numeral chapter labels, drop-in lead-in words, pillar dividers
- **Pattern B — Novel hybrid wrap+marquee** (fpdf2 + ebooklib) — first + last chapter image are full-page; middle chapter images wrap text around them with line-by-line algorithm; alternating left/right anchoring

## Install

```bash
# clone or download this repo
git clone https://github.com/bertbertov/claude-skills.git

# copy any skill into your global skills folder
cp -r claude-skills/book-creation ~/.claude/skills/
cp -r claude-skills/illustrated-book ~/.claude/skills/
```

Claude Code auto-discovers skills in `~/.claude/skills/`. The skill becomes available under its `name` field (defined in the YAML frontmatter of each `SKILL.md`).

## Required environment

For `book-creation`'s downstream phases:

| Dep | Purpose |
|---|---|
| Python 3.10+ with `fpdf2`, `ebooklib`, `Pillow`, `numpy` | Compile PDFs/EPUBs |
| ComfyUI + Flux Krea Dev FP8 | Local interior sketch generation |
| Google Gemini API key(s) — `GEMINI_API_KEYS` env var, comma-separated | Cover generation |
| Audiobook-Engine bundle (separate) | EN+RU audiobook generation |
| `gh` CLI authed | Publishing skills |
| `ffmpeg` on PATH | Audiobook stitching |

API keys must be supplied via environment variables — never committed.

## Status

Both skills proven on real production runs:
- The Last Reader (literary novel, EN+RU, Pattern B hybrid layout, Nano Banana cover, Kokoro audiobook)
- The Compassion Algorithm Cycle (Books 1-5: Compassion Algorithm, Mother Simulator, Blueprint Machine, Ward Vostok, Apollo Protocol)
- The Enlightened Don't… series (4-book pop-spirituality, in production)

## License

MIT.
