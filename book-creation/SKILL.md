---
name: book-creation
description: End-to-end pipeline for producing illustrated books and book series — single book or any-N-book series. Handles concept lock, voice and persona, parallel chapter drafting, cover generation, interior cartoon sketch generation, PDF/EPUB compile with hybrid layout (text-wrap + marquee), audiobook generation, and KDP/Kobo/Google Play publishing. Use when the user wants to produce 1+ illustrated books (paperback + ebook + audiobook) — works for memoirs, self-help, novels, manifestos, and pop-spirituality alike.
---

# Book Creation Pipeline

End-to-end workflow for producing illustrated books — one or many — from concept to retail listing. Companion to `illustrated-book` (single-book layout details); use this skill when the user is building anything from a single illustrated paperback to a 10-book series.

## When to use

- "Help me write a book / series of books"
- "Same cover format, same voice, same illustration style across N books"
- "Generate the entire manuscript with covers and interior sketches"
- "Translate my idea into a publishable Amazon paperback + Kindle + audiobook"

## When NOT to use

- A faithful translation of someone else's copyrighted work — refer to translation-rights conversation
- A children's picture book — different layout/illustration pipeline
- Pure non-fiction reference (textbooks, manuals) — different structure
- A literary novel where the visual signature is photographic — use `illustrated-book` skill directly with Pattern A or B

---

## The pipeline (any number of books)

Below is the proven workflow. It scales: a single book uses Phases 0-9 sequentially. An N-book series adds parallel-agent dispatch in Phase 5 and a SERIES_GUIDE.md in Phase 1. The downstream phases (compile, audiobook, publish) are identical per book.

### Phase 0 — Concept lock (USER alignment, mandatory)

Before writing anything, lock with the user:

1. **Number of books, titles, and the through-line.** What anxiety / domain / curiosity does each book address? For a single book, just the title + hook. For N books, the series arc.
2. **Voice register.** Pick one and confirm with one paragraph of sample voice:
   - Manson-spicy (F-bombs, blunt)
   - Burkeman-cerebral (no swearing, contemplative)
   - Manson × Burkeman × Gor (laid-back self-help, light Eastern wisdom, no swearing)
   - Pelevin-tradition literary (philosophical, surreal, dense)
   - Sebald-ish meditative (photographic interspersed with prose)
3. **Author persona — most important decision.**
   - Autobiographical (the user) — captivating but risky if user is niche
   - Pseudo-autobiographical generic ("a mid-30s engineer", "a teacher in their 40s") — broader appeal
   - Third-person fictional narrator
   - **Critical test:** ask "what's the cold-buyer hook?" If the persona requires reader-education before they can engage (niche subculture, foreign-emigré framing, specific religious tradition), the cold-Amazon-buyer won't bite. Generic-but-relatable wins almost every time.
   - **Run a 1-chapter sample BEFORE parallel-drafting at scale.** Persona drift discovered after 100k words is brutal to fix.
4. **Cadence (multi-book only).** Rapid release in 2-4 weeks (max KDP algorithmic boost) vs. one at a time.
5. **Languages.** EN-only, or EN + RU / other — affects audiobook engine choice (per `project_audiobook_engine.md`).
6. **Rights.** If the user references existing books to "follow," confirm whether they have translation rights. Original-work-in-similar-register is rights-clean; translation without rights is not.

### Phase 1 — Folder structure

```
Books/<Project_Name>/
├── _series/                         # only for multi-book; skip for single book
│   ├── SERIES_GUIDE.md              # voice + persona + recurring devices + art bible
│   ├── _gen_covers.py               # cover generator
│   ├── _gen_sketches.py             # interior sketch generator
│   └── test_sketches/               # approval-gate samples
├── 01_<Book_Slug>/                  # for single book, just one folder under Books/
│   ├── BOOK_PLAN.md                 # chapter outline (any N chapters; 25 typical)
│   ├── chapters/                    # NN_slug.txt files
│   ├── images/00_cover.png          # main cover
│   └── sketches/                    # interior illustrations
├── 02_<Book_Slug>/...               # additional books
└── _publishing/                     # metadata kits per book
```

**SERIES_GUIDE.md** (or `BOOK_GUIDE.md` for single-book) must include:
- Voice register (with positive examples + FORBIDDEN registers)
- Narrator persona (specific bio, texture, things to draw from, things NOT to disclose)
- Recurring devices (e.g. "I tried that" pattern, parable retold modern, a meditative anchor — pick one universal practice if the book has wisdom moments)
- Sketch art-bible (LOCKED prompt prefix; tested via approval samples)
- Cover bible (composition formula — same X across all covers in series)
- Marker conventions (Phase 4)
- Production checklists

### Phase 2 — Cover generation

Best-in-class for legible-text-on-cover: **Google Gemini's Nano Banana Pro** model (`nano-banana-pro-preview`). Best for general scenes with cleaner typography: **Nano Banana Flash** (`gemini-2.5-flash-image`). Local alternative: Flux Krea Dev FP8 via ComfyUI.

**API keys must be supplied via environment variable** — never hardcoded in the script:
```bash
export GEMINI_API_KEYS="key1,key2,key3,key4"   # comma-separated, will be rotated
```

Or per-call:
```python
import os
keys = os.environ["GEMINI_API_KEYS"].split(",")
```

**Cover composition formula** (proven for laid-back self-help series; adapt per genre):
- Top: AUTHOR NAME in spaced caps with optional logo separator
- Title: bold hand-drawn-style caps filling upper third (the clickbait headline)
- Subtitle in accent color (the descriptive promise)
- Hero subject small in frame, environment dominates
- Footer: small cap pull-quote (e.g. "FROM THE AUTHOR OF ___")
- Series cohesion: same recurring visual motif across all covers (a hat, a chair, a window — anything consistent)

**Prompt rules (learned 2026-04-25):**
- DO NOT ask for "empty white sky-space at the top" → produces flat blown-out band. Let scene fill frame; overlay title on natural shadow.
- DO put exact text in quotes ("text reads exactly: '…'") — Pro reproduces verbatim.
- DO say "the entire scene fills the frame to the very top, NO blown-out negative space."

### Phase 3 — Sketch art approval gate (mandatory)

**DO NOT generate the full sketch batch before getting user approval.** Always:

1. Generate **~10 test sketches** at high quality (Flux Krea Dev FP8 at 1024×1024, 25 steps)
2. Show user → wait for approval
3. After approval, switch to faster production settings (768×768, 18 steps) for ~3-4× speedup
4. Smoke-test sped-up settings with 1 sketch — confirm quality matches approved style
5. Then run full batch

**Locked production prompt prefix for cartoon line-art** (Gor-aesthetic style, proven 2026-04-25):
```
Black ink loose hand-drawn pen sketch on pure white background,
minimalist cartoon line art, single subject, no shading, no fills, no grayscale tones,
no color, no gradient, no background detail, just black lines on white,
simple expressive contour lines, slight wobble, lots of empty white space,
flat 2D illustration, the entire image is pure solid white with only black ink lines drawn on it.
NOT a photograph, NOT a notebook page, NO paper texture, NO frame, NO binding, NO border.
```

**Locked negative prompt:**
```
photorealistic, photograph of a sketch, photograph of a notebook, paper texture,
notebook binding, page edges, sketchbook, journal, frame around the drawing,
3D render, color, gradient, shading, watercolor, oil paint, anime, manga,
intricate background, busy composition, detailed environment, gray fills,
cross-hatching, dense linework, decorative border, dark background, black background
```

**Common pitfall:** if sketches come back as photos of pen-sketches inside a notebook, your prompt mentioned "paperback book" / "notebook" / "between paragraphs" → strip those phrases.

**For other styles** (watercolor, photo-collage, vector flat, etc.), substitute the prompt prefix and run the same approval gate.

**Post-process:** any pixel with R+G+B > 720 → snap to (255,255,255). Cleans near-white tints.

### Phase 4 — Per-book BOOK_PLAN.md

Each book gets a chapter outline. Three acts of ~⅓ each. Required elements:
- Filename slug for each chapter (snake_case)
- One-paragraph chapter summary
- Sketch hints list (recurring visual elements)
- Pull-quote candidate bank (the agent uses some, invents others)
- Recurring meditative-anchor placement (~3 chapters per book if applicable)
- Cross-reference rules (only relevant for multi-book series)

Chapter count: 15-30 is typical. Some manifestos work at 10 chapters; some literary novels at 40+.

### Phase 5 — Drafting

**Single book:** dispatch ONE `general-purpose` agent with the BOOK_GUIDE.md + BOOK_PLAN.md.

**Multi-book series (parallel):** dispatch ONE `general-purpose` agent PER BOOK. Each agent receives:
- Path to SERIES_GUIDE.md (read first)
- Path to its BOOK_PLAN.md
- Persona spec recap in the prompt itself (defense in depth)
- Output folder spec
- Marker conventions (Phase 6)
- Word target per chapter and total
- Save-as-you-go directive (don't batch in memory)
- Report-back format

**Anthropic API limit:** 4 parallel `general-purpose` agents is the safe ceiling. 5+ may throttle.

**Persona drift watch.** The number-one failure mode is the agent picking up wrong persona texture. Mid-flight, periodically `Grep` for forbidden tokens (whatever you defined as off-limits in the GUIDE). If detected, stop, archive, rewrite the GUIDE more emphatically, redispatch.

### Phase 6 — Marker conventions

Source `.txt` files use marker tokens that the compile script substitutes.

| Marker | Where | Renders as |
|---|---|---|
| `[SKETCH: scene description]` | Inline, on its own line | A pre-generated PNG sketch inserted at that position |
| `[QUOTE: single-sentence pull-quote]` | Inline | EPUB: styled HTML pull-quote callout. PDF: styled callout OR regenerated as graphic |
| `[SEEDS]…[/SEEDS]` | At chapter end, with `- ` bullets | Styled callout box with italic serif aphorisms (the Gor "Seeds of Wisdom" device) |
| First and last `img_map` entries | Per chapter image map | Full-page marquee illustration BEFORE chapter text. Middle entries → text-wrap (Pattern B v2 from `illustrated-book` skill) |

Adopt or omit each marker per genre. Self-help and pop-spirituality use all four. Literary novels often use only `[SKETCH:]` and the chapter-image map.

### Phase 7 — Sketch generation (production batch)

After all chapters drafted:
1. `Grep` all `[SKETCH:]` markers across `chapters/` files
2. Run sketch generator script in batch — generates one PNG per marker into `<book>/sketches/`
3. File naming convention: `<chapter_slug>_s01.png`, `_s02.png`, etc.
4. Whitewash post-processing
5. Production settings (locked for line-art): 768×768, 18 steps, Flux Krea Dev FP8, euler/simple, cfg 1.0, guidance 3.0

Throughput: ~15s/sketch on RTX 5080. 400 sketches in ~1.5 hours.

### Phase 8 — Compile (per `illustrated-book` skill)

Use the proven `_compile_novel.py` script (handles fpdf2 PDF + ebooklib EPUB). The script supports the full marker set from Phase 6 and the hybrid layout (Pattern B v2: first + last `img_map` entries become full-page marquees; middle entries text-wrap with alternating L/R).

Per book:
```bash
python _compile_novel.py <Project_Name>/<Book_Slug>
```

Outputs: `<Book>_EN.pdf`, `<Book>_EN.epub`, `<Book>_Cover_Amazon.jpg` (1600×2560), and `<Book>_RU.{pdf,epub}` if RU chapters exist.

### Phase 9 — Audiobooks (Audiobook-Engine)

```bash
cd <Audiobook-Engine path>
tts.bat audiobook "<full path to book folder>" en
```

EN: Kokoro v1.0 af_heart (~25-49× RTF). RU: F5-TTS Misha24-10 with cloned voice (~1× RTF). Output: `<Book>_AUDIOBOOK_<LANG>.mp3` at 192 kbps.

### Phase 10 — Publish

Use the publishing metadata kit pattern: per-book metadata pack with KDP categories, BISAC codes, keywords, suggested prices, blurb (HTML), comp titles, author bio. Hybrid drive: prepare metadata, user opens KDP/Kobo/Google Play, pastes/uploads.

For multi-book series: rapid release across 2-4 weeks, KDP Select on Book 1 only.

---

## Critical lessons (proven on multiple book runs through 2026-04-25)

### 1. Persona iteration is the highest-stakes phase
Drafting at scale with the wrong persona is brutal to undo. Always run a 1-chapter sample of the proposed persona BEFORE parallel-drafting. Ask explicitly: "is this hooky for an Amazon cold-buyer?"

### 2. The user's autobiographical voice is rarely the right hook
Authors imagine readers will be fascinated by their actual life. Cold readers want to project. **Default to pseudo-autobiographical generic** unless the user has demonstrable name recognition.

### 3. Sketch test gate is non-negotiable
~10 test sketches → user approval → production settings → smoke test → full batch. Skipping the gate produces hundreds of wrong sketches.

### 4. Cover prompt rules
- Avoid "empty white sky-space" → flat blown-out band
- Quote exact text in the prompt — Pro reproduces verbatim
- Series cohesion = explicitly state "same X as previous covers" in the prompt

### 5. Sketch prompt rules
- Avoid "paperback book / notebook page" → Flux frames the sketch as a notebook photo
- Use "flat 2D drawing on pure white" instead

### 6. Cover-API quota model
Free-tier Nano Banana Pro is per-key-per-time-window, not per-day-total. Rotate keys; when one is rate-limited, the next picks up after ~45s cooldown.

### 7. Kokoro audiobook: clamp style index
Kokoro-onnx has an off-by-one bug at MAX_PHONEME_LENGTH=510. Patch the helper:
```python
style = voice_style[min(len(tokens), voice_style.shape[0] - 1)]
```

### 8. Backup before reset
When a persona pivot is needed mid-draft, archive the old chapters (move to `_v1_old_persona_archive/`). Don't delete. They contain reusable structures, beats, and pull-quotes that can be mined into v2.

### 9. Compile + audiobook + publish phases are 90% reusable
Once the GUIDE and PLAN are right, downstream pipeline runs unmodified. Investment: ~2 hours of pipeline setup once. Per-book downstream: ~30 min.

### 10. The same Russian-style cover formula reproduces cleanly via Nano Banana Pro for English-language books
Hat + tropical setting + bold black caps title + accent-color subtitle + author name in spaced caps + "FROM THE AUTHOR OF ___" footer = consistently strong EN-market cover. (Adapt per genre — literary fiction has a different formula.)

### 11. Persona breadth check — "would a non-tech reader feel excluded?"

On the Enlightened series we drafted with the persona "burnt-out AI engineer." The voice was strong but too narrow: 100 chapters thick with niche tool names, jargon, and Slack-channel-namedrop. A CMO, a teacher, a lawyer in their 40s reads page 5 and feels the door shut.

**For mass-market self-help / pop-spirituality**, the persona should be a knowledge worker / mid-career professional that any white-collar reader can project into.

**The refined keep-vs-soften rule** (locked 2026-04-25 by Albert after voice-broaden review):

| KEEP (universally known, every reader gets these) | SOFTEN (niche tools / jargon — replace with category nouns) |
|---|---|
| ChatGPT | Cursor → "the AI code tool" |
| Instagram, TikTok, Twitter/X, YouTube, LinkedIn, Reddit | Hacker News → "the news feed at 1 AM" |
| Slack, Zoom, email, calendar | n8n → "an automation I wrote" |
| Generic "AI tools," "the new tools," "the hype" | Notion / Linear / Reclaim / Motion / Sunsama → "the seven productivity apps I paid for" |
| Layoffs (universal anxiety) | "vibe-coded" → "winged it" / "improvised" |
| Conferences, offsites | hackathon → "innovation week" / "internal demo day" |
| Startups, founders, CEOs, PMs | Slack channel called "#ai-experiments" → "the project folder of abandoned initiatives" |
| iPhone, Android, smartphone | "agent loop" → "the workflow" / "the script that ran on its own" |
| Audible, Kindle, Substack | "shipped a feature" → "launched something" |

**Mitigation if drafted too narrow:** run a **voice-broaden** surgical-edit pass. 4 parallel agents (one per book) make targeted replacements per the table above. Keep all sketches/quotes/seeds intact; preserve emotional beats; just swap niche jargon for category words. Cheaper than re-drafting from scratch.

**Persona description to use in book guides:** "a senior-something at a tech-adjacent company / a knowledge worker / a mid-career professional who got swept up in the hype wave" — NOT "a software engineer who built AI agents." That single phrasing change in the SERIES_GUIDE.md cascades through every chapter the agents write.

### 12. fpdf2: `pdf.image()` does NOT auto page-break
`pdf.image(..., y=pdf.get_y(), w=W)` will draw the image at current Y even if it overflows the page bottom — the bottom gets clipped. Always pre-calculate image height and force `pdf.add_page()` before drawing if it won't fit:
```python
img_w = 70  # mm
with Image.open(sp) as im:
    img_h = img_w * (im.height / im.width)
bottom_margin = 20
needed = 2 + img_h + 4
if pdf.get_y() + needed > PAGE_H - bottom_margin:
    pdf.add_page()
pdf.image(str(sp), x=(PAGE_W - img_w) / 2, y=pdf.get_y(), w=img_w)
pdf.set_y(pdf.get_y() + img_h + 4)
```
Same logic for `seeds` callout boxes — pre-estimate height, page-break if it won't fit.

### 13. fpdf2: long italic strings need hand-wrap — `cell(w=0)` AND `multi_cell` both fail differently
**The bug bites in TWO places**, not one. Check both before claiming fixed:

**(a) `cell(w=0, align="C")` with a long string** = ONE page-wide line, edge-to-edge. Looks like a giant italic banner. Most common culprit: **chapter subtitle/epigraph rendered as a single italic line under the chapter title.** `cell()` does NOT wrap.

**(b) `set_x(...)` then `multi_cell(narrow_w, align="C")`** = first line indented, subsequent wrapped lines reset to LEFT-margin and render full-width. Most common culprits: pull-quotes, callout boxes (e.g. SEEDS items).

**Both fixes are the same pattern: hand-wrap with `get_string_width`, render line-by-line with `cell(w=narrow, align="C")` after `set_x(centered_x)`:**
```python
pdf.set_font("T", "I", 11)
col_w = TEXT_W - 30                     # narrow column
col_x = (PAGE_W - col_w) / 2            # centered
words = text.split()
lines, cur = [], words[0] if words else ""
for w in words[1:]:
    trial = cur + " " + w
    if pdf.get_string_width(trial) <= col_w: cur = trial
    else: lines.append(cur); cur = w
if cur: lines.append(cur)
for line in lines:
    pdf.set_x(col_x)
    pdf.cell(w=col_w, h=7, text=line, align="C", new_x="LMARGIN", new_y="NEXT")
```
For SEEDS-style hanging-bullet lists, same hand-wrap with bullet on first line and indent on continuation lines (use `cell(new_x="RIGHT", new_y="TOP")` to keep the bullet inline).

**Verify visually, not just by x-positions.** Render the suspect pages to PNG with PyMuPDF (`fitz.open(...).get_pixmap(dpi=120).save(...)`) and READ them back. `pypdf` x-position checks alone missed this for me — they reported correct x for `cell()` blocks (subtitle started at x=44 because that's the page margin, technically "correct"), but the LINE EXTENDED to right margin. Visual render is the only reliable check.
If you want centered pull-quotes in a narrow indented column, `multi_cell(align="C")` is a trap:
- Calling `pdf.set_x(LEFT + 12)` before it indents ONLY the first line; subsequent wrapped lines reset to left-margin.
- Temporarily widening `set_left_margin` / `set_right_margin` ALSO doesn't reliably fix it — fpdf2 still renders wrapped lines full-page-wide. (Don't trust the first fix that "looks right" — verify positions in the output PDF.)

Real fix: bypass `multi_cell` entirely and hand-wrap + render line-by-line with `cell()`:
```python
pdf.set_font("T", "I", 12)
quote_w = TEXT_W - 30                      # narrow column (e.g. 88mm on A5)
quote_x = (PAGE_W - quote_w) / 2           # centered → ~30mm indent each side
quote_text = f'"{text}"'
words = quote_text.split()
lines, cur = [], words[0] if words else ""
for w in words[1:]:
    trial = cur + " " + w
    if pdf.get_string_width(trial) <= quote_w: cur = trial
    else: lines.append(cur); cur = w
if cur: lines.append(cur)
needed = len(lines) * 6 + 6
if pdf.get_y() + needed > PAGE_H - 20: pdf.add_page()
for line in lines:
    pdf.set_x(quote_x)
    pdf.cell(w=quote_w, h=6, text=line, align="C", new_x="LMARGIN", new_y="NEXT")
```
Verification (don't trust visual scan in a viewer — extract positions): use `pypdf` with a `visitor_text` callback to assert quote-line `x` values are inside the narrow column (e.g. ~85–100pt) and NOT at body-text x (~45pt). If quotes still appear at body-x, the fix didn't take.

---

## Required environment / dependencies

| Dependency | Purpose | Install |
|---|---|---|
| Python 3.10+ with `fpdf2`, `ebooklib`, `Pillow`, `numpy` | Compile PDFs/EPUBs | `pip install fpdf2 ebooklib Pillow numpy` |
| ComfyUI + Flux Krea Dev FP8 | Local sketch generation | per ComfyUI docs; place model in `models/diffusion_models/` |
| Google Gemini API key(s) with billing enabled or free-tier acceptance | Cover generation | `aistudio.google.com` |
| Audiobook-Engine bundle | EN+RU audiobook generation | `Desktop/Audiobook-Engine/` (separate skill) |
| `gh` CLI with auth | GitHub publishing of skills/code | `gh auth login` |
| `ffmpeg` on PATH | Audiobook stitching | `winget install Gyan.FFmpeg` (Win) |

**API key handling — DO NOT hardcode.** Read from environment variables, e.g.:
```python
import os
GEMINI_KEYS = os.environ.get("GEMINI_API_KEYS", "").split(",")
if not GEMINI_KEYS or not GEMINI_KEYS[0]:
    raise RuntimeError("Set GEMINI_API_KEYS env var (comma-separated keys)")
```

---

## Verification checklist before delivering

- [ ] GUIDE has voice, persona, devices, art bible, cover bible, marker conventions filled
- [ ] BOOK_PLAN per book has chapter outlines + sketch hints + pull-quote bank
- [ ] All covers generated and visually cohesive (if multi-book)
- [ ] ~10 test sketches user-approved BEFORE production sketch run
- [ ] Each chapter has appropriate `[SKETCH:]` `[QUOTE:]` and `[SEEDS]` markers
- [ ] No persona drift (grep forbidden tokens before compile)
- [ ] PDFs + EPUBs render correctly with hybrid layout
- [ ] Audiobook generated end-to-end on at least one book before scaling
- [ ] Publishing metadata pack drafted per book
- [ ] Rights status confirmed (original-work, not unauthorized translation)

## Companion skills

- **illustrated-book** — single-book layout (Pattern A: manifesto / Pattern B: novel hybrid wrap+marquee)
- **audiobook-engine** (if a separate skill) — Kokoro EN + F5-TTS RU pipeline

---

## Quick-start scaffold

For a new project:

1. `mkdir Books/<Project_Name>/01_<Book_Slug>/{chapters,images,sketches}`
2. Copy `SERIES_GUIDE.md` template (or `BOOK_GUIDE.md` for single-book) and fill voice/persona/devices
3. Copy `BOOK_PLAN.md` template, fill chapter outline
4. Set `GEMINI_API_KEYS` env var
5. Run cover generator → user approval
6. Run 10-sketch test → user approval
7. Dispatch drafting agent(s)
8. After drafts land, run sketch batch
9. Run `_compile_novel.py`
10. Run audiobook (Audiobook-Engine)
11. Prepare publishing metadata pack
12. User uploads to KDP/Kobo/Google Play

That's the entire pipeline. Each phase has a hard quality gate so failures surface before they cost a redo.
