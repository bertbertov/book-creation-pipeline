---
name: book-creation
description: End-to-end pipeline for producing illustrated books and book series — single book or any-N-book series. Handles concept lock, voice and persona, parallel chapter drafting, cover generation, interior cartoon sketch generation, AI-humanization pass + citation audit, PDF/EPUB compile with hybrid layout (text-wrap + marquee), audiobook generation, KDP-disclosure-and-cadence rules, and KDP/Kobo/Google Play publishing. Use when the user wants to produce 1+ illustrated books (paperback + ebook + audiobook) — works for memoirs, self-help, novels, manifestos, and pop-spirituality alike.
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

### Phase 2 — Cover generation (CANONICAL Flux + PIL hybrid, locked 2026-04-29)

**The pipeline is two stacked layers, never one all-AI render:**

#### Layer 1 — Photo via local Flux Krea Dev FP8 on ComfyUI ($0)
Prompt the SCENE ONLY (setting, figures, lighting). **NO text in the prompt.** All typography is composited later in PIL — letting Flux render text wastes the budget and produces inconsistent typography across re-renders.

Flux workflow params:
- 1024×1536 portrait (2:3), euler / simple sampler, 28 steps, guidance 4.0
- ComfyUI endpoint at `http://127.0.0.1:8188`
- Reference impl: `_gen_covers_enlightened_style.py` in `Books/The_Buddha_Series/02_The_Buddha_at_2_AM/`
- Cost: $0 / Time: ~60s per take

**Composition rules (so PIL typography can land cleanly):**
- Upper 40-45% of frame = bright/empty negative space (sky, mist, sand, ocean) for the title block.
- Figures occupy the lower 55-60% of the frame.
- "The figure is small in the frame; the environment dominates."

#### Layer 2 — Typography via PIL/Pillow ($0, instant, infinitely tweakable)

**Composite all text + the canonical red bird asset on top of the Flux raw.** Iteration is by editing pixel coordinates and re-running PIL — takes <1s per round-trip.

**The canonical brand header (locked 2026-04-29 — apply to every Albert Kamalov book):**
```
            ALBERT [red hummingbird] KAMALOV       ← y=50, 56pt Arial Bold
                                                     black on bright photo, white on dark
                                                     (auto-detect via top-band brightness >145)

                  THE BIG TITLE                    ← y=130, 170pt Arial Black
                  TWO STACKED LINES                  pure black (12,12,14), 165px line leading

                  THE RED SUBTITLE.                ← +25px after title block, 76pt Arial Black
                  TWO STACKED LINES.                 red (218,49,32), 85px line leading

                  [photo dominates here]

           FROM THE AUTHOR OF THE LAST READER      ← y=h-90, 38pt Arial Bold
                                                     white with 1px black shadow
```

**Bird glyph — extracted asset, NEVER re-drawn:**
- Canonical asset: `C:/Users/A/Desktop/Books/_assets/red_bird.png` (74×76 transparent PNG)
- Extracted from `Books/The_Enlightened_Series/01_Dont_Hustle/01_Dont_Hustle_Cover_Amazon.jpg` via red-threshold + largest-blob mask
- On every cover, paste with: `im.paste(bird_resized_to_72px, (x, y), bird_resized_to_72px)` for pixel-perfect brand fidelity
- Position between names: 28-32px gap each side, vertically aligned with the cap-height of the names

**Reference impl**: `_finalize_bali_cover.py` in `Books/The_Buddha_Series/02_The_Buddha_at_2_AM/` is the canonical PIL composer. Copy and adapt.

#### Iteration recipe
When the user says "tweak X":
1. Identify which PIL parameter controls X (font size, y-position, color, gap).
2. Edit the script.
3. Re-run `python _finalize_*.py` — instant.
4. Read the updated PNG and show.
5. Loop.

**NEVER regenerate the Flux photo for a typography tweak** — that wastes 60s, costs nothing but burns time, and changes the underlying scene which the user already approved.

#### When to fall back to Banana Pro
- User explicitly wants typography photographically embedded INTO the scene (e.g. "as if printed on the wall")
- User wants the AI to generate weird-stylized glyphs that PIL fonts can't do
- These are rare. Default to Flux + PIL.

#### Prompt rules for the Flux photo layer (Albert-tested):
- DO compose for big bright sky at top (40-45% of frame).
- DO place figures small in the lower 55-60% of the frame.
- DON'T request "empty white sky-space" — produces flat blown-out band. Ask for "soft pastel sky" / "bright misty sky" / "golden-hour sky."
- DON'T put text in the Flux prompt. PIL handles all of it.

#### Series cohesion
Same canonical brand header (ALBERT / bird / KAMALOV) across all covers regardless of book series. Series-specific motifs go into the photo composition (panama-hat man for Enlightened, weathered Buddha for Buddha series, etc.) — never in the typographic header.

**See also**: `~/.claude/projects/c--Users-A--claude/memory/project_book_cover_pipeline.md` (full memory entry with parameter values + iteration history) and `feedback_book_cover_brand_standard.md` (the brand decision).

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

### Phase 6.5 — Humanizer pass (MANDATORY for KDP-bound books)

After drafting and before sketches/compile, every chapter goes through a humanizer pass against the operational guide at `_shared/HUMANIZER_STYLE_GUIDE.md` (4,920-word reference, last updated 2026-04-27).

**Why this phase is non-negotiable:** Amazon KDP terminated Albert's account on 2026-04-27 after one AI-drafted book (*The Enlightened Don't Doomscroll*) was caught — first appeal succeeded but the account is on a one-strike warning. Detection is now structural, not stylistic; the chapters need to read as human-written before upload.

**The 5 load-bearing rules** (cribbed from `HUMANIZER_STYLE_GUIDE.md`):

1. **Strip AI-vocabulary first.** Top banned words: *delve, tapestry, navigate (metaphorical), intricate, multifaceted, nuanced, pivotal, underscore, testament, paradigm, embark, holistic, synergy, encompass, foster, robust, comprehensive, keen, arguably, notably, realm, "shed light", "serves as", "at its core", "it is worth noting", "let's dive in".* Each one is a token-frequency anomaly that classifiers weight heavily. Max Planck logged "delve" usage spiking >50% in academic prose post-ChatGPT — Amazon's vendors are trained on that delta.

2. **Cap em-dashes at 2 per chapter.** GPT-4o uses ~10× more em-dashes than GPT-3.5 (Goedecke 2024 traces it to 19th-century books entering training data ~2023). Em-dash density alone is near-deterministic in modern classifiers. Replace with commas, periods, parentheses, or restructure the sentence.

3. **Kill rule-of-three triples + "not X — it's Y" parallelisms.** AI loves balanced clauses. Humans don't. Break them: ditch one item, restructure one to be asymmetric, or rewrite as prose.

4. **NEVER run drafts through commercial humanizers** (QuillBot, Undetectable.ai, StealthGPT, WordAi). Pangram Labs (likely Amazon's vendor class, ~1-in-10,000 false-positive rate) explicitly trains discriminators against these tools. Their output has its own fingerprint that's *worse* than raw GPT.

5. **Inject specifics + asymmetry + friction.** Smoothness is the AI signature. Add: dates ("2017", "around 18 months"), names (real people, real companies), embodied detail ("the metallic taste at 3 AM"), self-correction mid-paragraph ("Actually, that's not quite right."), sentence fragments ("Like that."), one-line paragraphs as punctuation, mid-paragraph topic shift without a transition word.

**Per-paragraph humanizer checklist** (10 steps, applied to every paragraph):
1. Replace all banned vocabulary words with plain alternatives.
2. Count em-dashes — max 2 across the entire chapter, not per paragraph.
3. Find rule-of-three triples; break to two items or four.
4. Find "not X — it's Y" / "from X to Y" parallelisms; restructure.
5. Replace abstract nouns with concrete: not "concerning" → "the kind of thing that keeps you up at 2 AM."
6. Vary sentence rhythm — short punchy + one long unspooling per beat.
7. Add at least one specific date / name / number per chapter that isn't rounded.
8. Insert a sentence fragment for emphasis.
9. End at least one paragraph mid-thought, then start the next on a tangent.
10. Read aloud — if it sounds like a TED talk, rewrite.

**Voice anchor: Mary Karr's *Cherry*** (~50% second-person, "burnt-out narrator without a self to be inside" — her *Paris Review* interview describes the technique). Stronger fit than Lamott for the *Vegan-Badass* register. Hand to writing agents alongside `VEGAN_BADASS_VOICE.md`.

**Round-trip translation as final scrambling pass** (optional but recommended for highest-risk books):
1. Translate EN → RU via DeepL or LLM A.
2. **Manually edit the RU stage** — rephrase, change emphasis, swap a name. Critical: Krishna et al. NeurIPS 2023 §5 retrieval defense matches *semantics*, not just tokens. Surface paraphrase is dead.
3. Translate RU → EN via LLM B (different model, different vendor).
4. **Manually edit the EN stage** using the per-paragraph checklist above.
5. Spot-check 3 paragraphs — if any read as AI, apply checklist again.

**Self-screen before upload**: paste 3 random chapters into Originality.ai (target <20% AI), GPTZero (target perplexity >85, burstiness >0.7). If either fails, redo the humanizer pass on the failed chapters.

### Phase 6.7 — Citation audit (MANDATORY for non-fiction)

For any non-fiction book that names studies, quotes, real people, dates, or statistics, every cited fact must be verified against a primary source before compile. Hallucinated citations cross from style violation (recoverable) into TOS violation (account-ender — "Misleading content" is a forbidden category in KDP's content guidelines).

**Process:**
1. `Grep` chapters for: years (`19[0-9]{2}|20[0-2][0-9]`), proper nouns referenced as study authors, "according to", "X found", "research shows", "%", named book titles, named people.
2. For each hit, run a Google Scholar / Google Books / primary-source search.
3. If verifiable: keep with attribution, add source line in chapter footer or PUBLISHING_KIT.md.
4. If unverifiable / hallucinated: rewrite the paragraph without the false claim, OR replace with a verified analogous source.
5. Common AI hallucination patterns to scrub: fake study names, made-up Stanford/MIT/Harvard quotes, invented book titles by real authors, statistics with no source ("studies show 73%"), composite "experts" with no real-world referent.

**For Albert's catalog:** the *Decoded* series cites real Robbins quotes from the corpus (verified by `video_id`+`timecode`); the trilogy and Enlightened books reference public-domain or generic claims. Audit each book individually. Citations that survive an audit go into a `SOURCES.md` per book.

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

**MANDATORY at upload time on every store that asks (KDP, Kobo Writing Life, Google Play, Findaway, Apple Books):**

1. **Tick the AI-disclosure boxes honestly.** KDP, Kobo, and Google Play now have explicit fields for AI-generated text, AI-generated images, AI translations, AI-generated narration. **Disclosure does not affect ranking or sales** (Inkfluence AI 2026 policy guide; Kindlepreneur). **Undisclosed AI is what gets accounts terminated.** This is the single highest-leverage action in the entire pipeline.
2. **Hard-cap upload velocity at 1 book per 14 days on KDP.** Submission velocity is named explicitly as a detection signal in Inkfluence's 2026 policy summary. Faster than that triggers the spam classifier independent of content quality. For a 13-book queue → 6-month publishing window. Don't flinch on this.
3. **Lead with non-Amazon channels.** Kobo Writing Life, Google Play Books, Apple Books (via Draft2Digital), Findaway Voices, IngramSpark — each accepts AI disclosures cleanly and combined reach is ~50% of US ebook market and ~70% of audio. KDP slots in at the 14-day cadence; KDP-skeptical books go non-Amazon-only.
4. **Audit submission cadence vs. account history.** A new KDP author account uploading 3 titles in its first month is automatic-review territory. New accounts season for 60–90 days with their first title before adding more. Established accounts can run faster.
5. **One-strike accounts:** if KDP has terminated and reinstated the account, treat every subsequent upload as terminal. Cap at 1/30 days, audit citations triple-hard, never use the subject's name in the title without a clean fair-use disclaimer page in the front matter.

**Subtitle convention for critique books:** prefer "An Independent Critical Analysis" over "An Unauthorized Critique." Same legal posture, less inflammatory to Amazon's automated review.

For multi-book series: prefer staggered release at 14-day cadence, KDP Select on Book 1 only.

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

### 14. fpdf2: explicitly call `set_left_margin` and `set_right_margin` to match your LEFT/RIGHT constants
fpdf2's default left margin is **10 mm**, not your design's `LEFT` value. If you write code like:
```python
LEFT, RIGHT = 22, 22
# ... later ...
pdf.cell(0, 7, "SECTION HEADER", new_x="LMARGIN", new_y="NEXT")  # snaps to x=10mm
pdf.set_xy(LEFT, pdf.get_y()); pdf.cell(...)                     # body text at x=22mm
```
Headers will sit ~12 mm further left than body text and visually look misaligned, even though both came from `cell()` calls. The fix is one line in `__init__`:
```python
self.set_left_margin(LEFT)
self.set_right_margin(RIGHT)
```
Do this BEFORE any rendering. After this, `cell(0, ...)` (full-width) and `new_x="LMARGIN"` both honor your design margins. Body and headers align at the same x.

### 15. Drop caps on chapter openings — easier to skip than to render correctly
Tempting design touch, but the engineering is fiddly: a 3-line-tall capital placed at the body baseline overlaps subsequent wrapped lines unless you carefully shrink each affected line's `w`. fpdf2 doesn't have a built-in for this; rolling your own breaks on long words at the wrap boundary. **For most books, just don't.** The first paragraph reads fine without one. If a client demands them, render the cap as a separate image and compose the paragraph text around it manually — don't try to share fpdf2's cell flow.

### 16. Cover pages need `set_auto_page_break(False)` while drawing them
If your cover places elements near the bottom of the page (edition line, studio name footer band), fpdf2's auto page-break can fire mid-cover and spill the last few lines onto a blank page 2. Wrap your `cover_page()` in:
```python
pdf.set_auto_page_break(False)
pdf.add_page()
# ... draw cover ...
pdf.set_auto_page_break(True, margin=BOTTOM)
```
Otherwise you get a phantom page 2 with one tan rule and the word "FIRST EDITION · 2025" floating at the top.

### 17. Anatomy / labeled diagrams: prompt the labels EXPLICITLY or get them wrong
Default Gemini image-gen ("a labeled muscle diagram") returns plausible-but-wrong labels — pectoralis on a thigh, sartorius on a forearm. Two paths that work:
1. **No labels**: prompt "no labels, no text, no annotations" — get a clean unlabeled illustration. Safest.
2. **Explicit labels with positions**: enumerate each label with a side ("LEFT side of figure: 'Sternocleidomastoid' at neck, 'Pectoralis major' at chest...") and the model places them correctly. Verify visually before shipping. Use `gemini-2.5-flash-image` (cheap) for this — Nano Banana Pro is no more accurate at anatomy than 2.5 Flash, just more expensive.

### 18. Body alignment of section headers — set the same x for headers + paragraphs + bullets
After fix 14, also make sure your `render_h2` calls `pdf.set_x(LEFT)` explicitly before drawing the header cell. Don't rely on the cursor position from a prior `ln()` — that snaps to LMARGIN, which is now correct, but explicit is safer for future-you when you change margin constants.

### 19. Avoid heavy color blocks on covers if the brand is a "minimalist luxury" aesthetic
Default cover instinct is to put the title in white text on a dark band at top + bottom. For a Phaidon / Penguin Modern / pravilo-global.com style brand, this looks gimmicky. Better: pure white background, type at top with a thin tan rule beneath, hero image framed in a thin rule, type below. The white space IS the brand.

### 20. Interior image style MUST match the cover aesthetic of THIS book — don't carry over from a previous series
Sketchy black-ink-on-cream interior illustrations were the *Enlightened* series signature. They are NOT a default. For every new book, choose the chapter-image style that matches THIS book's cover:
- **Typographic cover** (e.g. *948 Business Books* black/cream/red slash) → iPhone-realistic editorial PHOTOS of single objects in soft daylight, magazine-spread aesthetic.
- **Caricature/illustration cover** (e.g. *Self-Help Decade* 12-figure grid) → atmospheric documentary photos that evoke each chapter's world; slice-of-life, no people, real textures.
- **Sketchy ink cover** (e.g. *Enlightened* series) → matching black-line ink illustrations on cream.

The user's locked preference for non-illustrated books is **iPhone-realistic** (per `feedback_image_realism.md`). Never batch-generate 30+ images without first running ONE test image and visually confirming the style is right for THIS book.

### 21. Do NOT force chapter openers onto recto (right-hand) pages with blank versos
The traditional-book convention "if current page is even, add a blank page so the chapter opener lands on the right" produces empty pages mid-book. Users WILL flag these as bugs. Just `add_page()` per chapter — let chapters start where they fall. If a true print-shop layout is required later (for offset printing), add the recto-forcing as a CLI flag, off by default.

### 22. fpdf2 `set_right_margin(distance)` takes DISTANCE FROM RIGHT EDGE, not the absolute X position of the right edge
A nasty bug:
```python
LEFT = 18              # distance from left edge — correct
RIGHT = PAGE_W - 18    # absolute x of right margin — DO NOT pass to set_right_margin
pdf.set_left_margin(LEFT)        # OK
pdf.set_right_margin(RIGHT)      # WRONG — sets right margin to ~134mm from the right edge
                                 # = negative usable area
```
`pdf.write()` will then error with `FPDFException: Not enough horizontal space to render a single character`. The fix: pass the same constant for both:
```python
pdf.set_left_margin(MARGIN_MM)
pdf.set_right_margin(MARGIN_MM)
```
`multi_cell(TEXT_W, ...)` will still work because it takes the width explicitly — but `write()` and other right-margin-aware calls silently break.

### 23. Action blocks (the "One. Two. Three." closer) — use `pdf.write()` for inline bold + full-width wrap
The naïve approach (render bold label via `cell(label_w)`, then body via `cell(rem_w)` per line) produces narrower-than-body, left-aligned-looking action paragraphs because all subsequent lines inherit the shortened wrap width. The user will flag this as "not full width."

The clean pattern (requires fix #22 already in place):
```python
pdf.set_x(LEFT)
pdf.set_font("Body", "B", 11)
pdf.write(5.6, label + " ")    # bold "One. "
pdf.set_font("Body", "", 11)
pdf.write(5.6, body)            # body wraps to full text width
pdf.ln(8)
```
`write()` flows from the cursor across the full text-width and wraps at the right margin. Loses justification on the action block (ragged-right) but keeps the bold lead-in and uses the full page width — the right tradeoff.

### 24. Pure white pages everywhere — don't paint cream backgrounds on SOME pages
Trap: writing an `add_cream_page()` helper that paints a cream rect on every explicit `add_page()` call, but doing nothing for the auto-flowed pages fpdf2 creates when `multi_cell` overflows. Result: inconsistent backgrounds — explicit pages cream, overflow pages white. Users will flag immediately.

Either paint EVERY page (override `header()` to fill the page background) or paint NONE (let fpdf2's default white carry it). For premium book aesthetic, **none** wins — pure white throughout.

### 25.5. Print/ebook covers MUST be 2:3 portrait — never square
**Hard rule (Albert, locked 2026-04-27).** Every print/ebook book cover is generated at the same aspect as the PDF title page = **2:3 portrait** (typically 1600×2400 or 1024×1536). NEVER 1:1 square.

The compile script renders the cover as page 1 with `pdf.image(cover, x=0, y=0, w=PAGE_W_MM, h=PAGE_H_MM)` — it stretches whatever you hand it to fit the 6×9 page. Square covers come out distorted (faces squished tall), 9:16 covers come out cramped horizontally.

When generating via Nano Banana Pro, the API's nearest preset is `aspectRatio: "3:4"` (0.75) — generate there and **center-crop** to 2:3 (1600×2400). Verify dimensions before locking:
```python
from PIL import Image
im = Image.open(cover_path)
w, h = im.size
assert abs(w/h - 2/3) < 0.005, f"cover must be 2:3, got {w}x{h}"
```

The audiobook square cover (Findaway/ACX requires 2400×2400) is a SEPARATE file — pad the 2:3 print cover with matching color bands left+right to square it. Don't regenerate.

This bit *The Self-Help Decade* — the original 1024×1024 caricature got stretched on the print PDF. Audiobook square was fine. Different files for different platforms.

### 27. Disclosure beats evasion (the actual KDP lesson, locked 2026-04-28)

Albert's KDP account was terminated 2026-04-27 over *The Enlightened Don't Doomscroll*. The cause was almost certainly the AI-disclosure checkbox not being ticked at upload — not the prose itself failing detection. Reinstatement came after a clean appeal email and now the account is one-strike.

The non-obvious finding from research (Stanford 2023, Inkfluence AI 2026 policy guide, Kindlepreneur, KDP Help G200672390): **disclosure does not affect ranking, sales, or visibility**. Undisclosed AI is what gets terminated. Disclosed AI publishes fine.

Compounding factor: ESL/bilingual writers (Albert is Russian/English) get false-positived on AI detectors at 2-3× the rate of native English writers. Stanford found 61% of TOEFL essays were flagged as AI when none were AI-written. Fighting a false positive after termination is harder than disclosing honestly upfront.

**The rule:** every AI-assisted upload — text, images, narration, translation — gets the corresponding disclosure box ticked. Always. No exceptions. This is the single highest-leverage action in the pipeline.

### 28. Em-dash density is a near-deterministic AI tell

GPT-4o uses ~10× more em dashes than GPT-3.5 (Sean Goedecke, 2024 — traces to 19th-century books entering the training corpus around 2023; peak em-dash density in published English was 1860). Modern classifiers weight em-dash density heavily because of this delta.

**Hard rule for KDP-bound prose: max 2 em dashes per chapter, total.** Not per paragraph — per chapter. Replace em-dashes with: commas (most cases), periods + new sentence (preserves cadence, often improves rhythm), parentheses (for true asides), or restructure to remove the dash entirely.

The Tony Robbins, Decoded chapters that just shipped have ~8-12 em dashes per chapter. If pushing those to KDP, run a humanizer pass first to bring the count under the cap.

### 29. Commercial AI humanizers are FORBIDDEN

QuillBot, Undetectable.ai, StealthGPT, WordAi, Phrasly. **All of them.** Pangram Labs (industry-leading detector, ~1-in-10,000 false-positive rate, almost certainly Amazon's vendor class) explicitly trains discriminators against these tools' outputs in their published benchmarks. Their output has a fingerprint that's *worse* than raw GPT.

Round-trip translation through Russian (Albert's native language) plus manual rewrite using the per-paragraph checklist in §6.5 is the durable substitute. It works because:
- DeepL/LLM-A translation breaks the source token sequence (kills surface fingerprint)
- Manual RU edit changes ideas (defeats Krishna §5 retrieval-based detection)
- Reverse translation by LLM-B reconstructs from semantic meaning, not from cached patterns

### 30. Citation hallucinations cross from style to TOS violation

A book with too many em dashes is a *style* problem (recoverable; redo the humanizer pass). A book that cites a fake Stanford study, an invented Robbins quote, or a non-existent paper by a real author is a *TOS* problem (account-ender — KDP categorizes this as "Misleading content," a hard forbidden category).

Phase 6.7 (citation audit) is mandatory for any non-fiction with named studies, quotes, statistics, or real-people claims. Run a Google Scholar / Google Books / primary-source check on every fact. Hallucinated citations get rewritten or removed. Verified citations go in `SOURCES.md` per book.

### 31. Round-trip translation needs MANUAL edits at both stages

Krishna et al. NeurIPS 2023 §5 proposes retrieval-based defense — find the original AI text in a corpus, match by semantics. That defense is in production. Pure DeepL → DeepL round-trip scrambles tokens but preserves semantics, so the retrieval defender still matches.

The fix: between the two translation passes, manually rewrite. Change emphasis, swap names, add specifics, restructure paragraph order. The *ideas* have to drift from the original AI output, not just the words.

Workflow that survives current detectors:
1. EN draft (AI) → DeepL → RU
2. **Human edit** of RU (rephrase, rearrange, add concrete details) — 30 min per chapter
3. RU → different LLM (Claude or GPT-5) → EN
4. **Human edit** of EN using §6.5 checklist — 20 min per chapter
5. Spot-check 3 paragraphs through Originality.ai (target <20% AI) and GPTZero (perplexity >85, burstiness >0.7)

Total ~50 min/chapter of human time on top of compute. Slow, but durable.

### 32. Mary Karr's *Cherry* is the closest published voice anchor for "Vegan-Badass"

For burnt-out narrator addressing the reader as "you" with detachment: *Cherry* (Karr, 2000) is ~50% second-person. From her *Paris Review* interview: she wrote it that way because she "didn't have a self to be inside" at the time. That's the exact emotional register Albert is reaching for.

Stronger anchor than Lamott (*Bird by Bird* — too encouraging), Geoff Dyer (*Out of Sheer Rage* — too tangential), or Heather Havrilesky (*Ask Polly* — too advice-column). Karr's first 50 pages of *Cherry* are the canonical reference for any rewrite agent working in Albert's register.

Hand to writing agents alongside `VEGAN_BADASS_VOICE.md` and the sample chapter `948_Business_Books/SAMPLE_CH1_HARD_WORK_LIE.md`.

### 26. Always ship a PUBLISHING_KIT.md per book — don't wait to be asked
The book is not "done" until the publishing kit is in the folder alongside the PDF. Required sections:
- Title block (title, subtitle, author, imprint, edition, language, page count, ISBN plan)
- Short description (back cover, ~80 words)
- Long description (product page, ~330 words)
- BISAC categories (3 picks + backups)
- Amazon keywords (7 slots, ≤50 chars each)
- Category browse paths (Amazon, Kobo, Google Play separately)
- Audience tiers (primary / secondary / tertiary)
- Comparable titles (5–6, real titles for the algo)
- Author bio (~120 words)
- Pricing table per platform (US/UK/DE × ebook/paperback)
- Marketing angles (15-word hook, one tweet, launch sequence, "don't market it as")
- Production checklist with checkboxes (cover, manuscript, photos, PDF, RU translation, EPUB, audiobook, Findaway kit, ISBN, Amazon Author Central, Goodreads)
- File map (where everything lives in the folder)

A publishing kit is typically 250–400 lines of Markdown per book.

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
- [ ] **Humanizer pass complete** — `HUMANIZER_STYLE_GUIDE.md` checklist applied to every chapter; em-dash count ≤2/chapter; banned vocabulary scrubbed; rule-of-three triples broken
- [ ] **Citation audit complete** (non-fiction only) — every named study/quote/person verified against primary source; `SOURCES.md` populated
- [ ] **Optional but recommended:** round-trip translation EN→RU(manual edit)→EN(manual edit) applied to highest-risk books
- [ ] **Self-screen passed:** 3 random chapters tested through Originality.ai (<20% AI) and GPTZero (perplexity >85, burstiness >0.7)
- [ ] PDFs + EPUBs render correctly with hybrid layout
- [ ] Audiobook generated end-to-end on at least one book before scaling
- [ ] Publishing metadata pack drafted per book
- [ ] **AI disclosure plan documented** — for each book, which boxes get ticked at upload (text/images/translation/narration)
- [ ] **Upload cadence plan documented** — 14-day spacing for KDP, no faster
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
