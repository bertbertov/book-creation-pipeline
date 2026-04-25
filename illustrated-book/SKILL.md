---
name: illustrated-book
description: Build a premium illustrated manifesto or novel from text chapters and images. Uses ReportLab for high-quality manifesto PDF with full-bleed hero images, drop-in lead-in words, styled section headers, and pillar dividers, OR fpdf2 for novel-style PDF with wrap-around chapter-opening images, plus EbookLib for matching EPUB. Use when the user wants to produce a book-format PDF+EPUB from chapter text files and a folder of images.
---

# Illustrated Book Compiler

Two distinct layout patterns are supported. Pick one:

## Pattern A — MANIFESTO (ReportLab, full-bleed)

Used for non-fiction / illustrated manifestos / coffee-table-adjacent literary work where each chapter deserves a dramatic full-bleed hero. Reference: *Vegan Badass*. See "Reference implementation (Pattern A)" below.

## Pattern B — NOVEL with hybrid wrap + marquee (fpdf2 + ebooklib)

Used for **literary novels** with one image per chapter (or fewer). Reference: `C:/Users/A/Desktop/Books/_scripts/_compile_novel.py` — drives The Last Reader, Mother Simulator, Blueprint Machine, Ward Vostok, Apollo Protocol.

### The hybrid layout rule (CANONICAL — locked 2026-04-25)

For every novel with chapter illustrations:
- **First img_map entry → full-page marquee** (dedicated image page before chapter text — Sebald/Folio plate convention; gives the opener weight)
- **Last img_map entry → full-page marquee** (the last image the reader sees before closing the book)
- **All middle img_map entries → text wrap** (Pattern B v2: image anchored top-left or top-right of chapter text, paragraphs wrap around it line-by-line, alternating L/R across the book)

Why hybrid: full-page-only feels heavy and dated; wrap-only loses the dramatic pause that openers and finales deserve. Hybrid matches the best illustrated literary editions (Sebald sparingly full-page; Folio Society with deliberate plates + occasional integrated images).

### PDF wrap algorithm (the validated B v2)
1. Compute `lines_beside = floor(image_height_mm / line_height_mm)` (e.g. 55mm / 5mm = 11 lines).
2. For each paragraph in the chapter body, hand-wrap it to the narrow column width (`TEXT_W - img_w - gap`).
3. Render exactly `lines_beside` narrow-column lines alongside the image.
4. If a paragraph spans the boundary, split it: render the lines that fit alongside, keep the remainder as the first paragraph of the full-width region below.
5. Render leftover paragraphs at full width starting `image_height + 2mm` below the image.

This avoids the bug where multi_cell renders an entire paragraph in narrow column even after passing the image bottom (which leaves dead space).

### EPUB wrap CSS
```css
img.ch-img-left  { float: left;  width: 45%; max-width: 45%; margin: 0.2em 1.2em 0.6em 0; height: auto; }
img.ch-img-right { float: right; width: 45%; max-width: 45%; margin: 0.2em 0 0.6em 1.2em; height: auto; }
.clearfix::after { content: ""; display: block; clear: both; }
div.chapter-image-full { text-align: center; margin: 2em 0; page-break-before: always; }
```
Float is well-supported on Apple Books, Kobo, Google Play, browsers; older Kindles may drop float and revert to standalone. That degradation is acceptable.

See "Reference implementation (Pattern B)" below.

### When to pick which

- **Manifesto / non-fiction / Pattern A** — full-bleed hero per chapter, drop-in lead-in words, styled section headers. Pelevin-tradition serious fiction would feel like a marketing pamphlet here. Reserve for actual manifestos.
- **Novel / literary fiction / Pattern B** — clean A5 page, image at chapter open with text wrapping around it, body text in serif, no lead-in colors, no full-bleed. This is the right choice for any serious literary fiction with images.

---

# PATTERN A reference (manifesto)

Output matches design patterns from modern illustrated non-fiction: full-bleed hero image per chapter, Roman numeral chapter labels, italic subtitles, drop-in lead-in words in an accent color, styled section headers, pillar dividers, cream paper with ink text.

## When to invoke

- The user has chapters as `.txt` files and images as PNGs, and wants a book-ready PDF/EPUB.
- The user asks to "compile", "build", "assemble" a book from source files.
- The user wants visual rhythm (hero image → chapter text → inline images between sections).
- The user wants the aesthetic of an "illustrated manifesto" — not a plain novel.

## Required inputs (ask the user if missing)

1. **Chapters directory** — folder containing `.txt` files named `01_chapter.txt`, `02_chapter.txt`, etc.
2. **Images directory** — folder of PNG/JPG images.
3. **Book metadata** — title, subtitle, author name, copyright year.
4. **Section map** — list of (chapter_file, chapter_label, subtitle, hero_image, pillar_header_if_any). Can be inferred from filenames if consistent.
5. **Output directory** — where to save the final PDF + EPUB.

## Dependencies

```bash
python -m pip install reportlab ebooklib pillow pymupdf pypdf
```

Windows fonts used: Georgia (body), Impact (display), Calibri (author name). Substitute equivalents on Mac/Linux.

## Core design patterns

### Page sizes
- A5 portrait (148mm × 210mm) — standard novel/manifesto
- US Trade (5×8 in) or KDP 5.5×8.5 in for paperback
- Let user choose

### Color palette (modifiable)
- `INK` (#1a1f1a) — near-black text/titles
- `PAPER` (#f4efe3) — cream background
- `ACCENT` (#1f7a3f) — forest green (lead-in words ONLY, not headers)
- `BG_DARK` (#0b0f0b) — full-bleed image pages

**Rule:** Never color section headers or titles. Accent color goes on the drop-in lead-words only. Keep titles in ink for readability and prevent the book looking like a marketing pamphlet.

### Typography
- Title: Impact display font, 36-40pt
- Chapter label (Roman numeral): Impact, 36pt, ink
- Chapter subtitle: Georgia Italic, 14pt, ink
- Section header: Georgia Bold 11pt, ALL-CAPS, ink, tight letter-spacing
- Body: Georgia 10.5pt, 15pt leading, justified, first-line indent 12pt
- Page number: Georgia 8pt, muted gray, centered at bottom

### Layout rules
1. **Cover is page 1** — full-bleed image (swap page templates so Bleed is first).
2. **Hero image before each chapter** — full-bleed page using the Bleed page template.
3. **Chapter starts immediately** after hero on a Text-template page — Roman label at top, subtitle below, body begins.
4. **First paragraph of chapter** — first 3-4 words in ACCENT_BOLD_UPPERCASE (NOT a drop cap — those overflow line height in ReportLab).
5. **Section headers inline in chapter** — detect all-caps lines or lines matching THE CODE/THE ANCESTOR/THE PROTOCOL/THE PROOF.
6. **Strip duplicate titles** — if the source text starts with "CHAPTER 1: THE CODE EXISTS", skip that line since we render it as "I" + "The Code Exists" already.
7. **Pillar dividers** — standalone page with centered pillar header (e.g., "PILLAR I — THE CODE") for grouping chapters.
8. **Images CONTAIN-fit, not COVER-fit** — whole image visible, small bars acceptable. Never crop hero images.

### Critical traps to avoid
- **COVER-fit cropping**: compute aspect and fit the smaller dimension (CONTAIN), not the larger (COVER).
- **Double page-breaks**: if your hero-image emitter adds a PageBreak and your chapter-start also adds one, you get blank pages. Solution: strip a leading PageBreak from the next flowable when emitting a hero page.
- **Drop caps overflow**: true drop caps need multi-column layout. Fake it with bold accent-color first-words in the first paragraph.
- **Color overload**: do not color section headers in accent — looks like a spam ad. Only lead-in words get color.
- **Page 1 default template**: ReportLab uses the FIRST page template in addPageTemplates by default. To start on Bleed, register Bleed first.

## Reference implementation

The proven working build script is at:
`C:\Users\A\Desktop\Books\Vegan Badass\compile_premium.py` (PDF)
`C:\Users\A\Desktop\Books\Vegan Badass\compile_premium_epub.py` (EPUB)

Read these for the canonical structure. They handle:
- PageTemplate switching (Bleed vs Text)
- FullBleedImage flowable pattern
- Section header detection with regex
- Lead-in word rendering
- Pillar dividers
- TOC generation
- Back matter (protocols, glossary, acknowledgments)
- EPUB-side CSS that mirrors the PDF aesthetic

## Typical workflow

1. **Confirm inputs** — chapters dir, images dir, metadata, section map.
2. **Copy `compile_premium.py`** to the book's folder and adapt:
   - Update `SECTIONS` list with the user's chapter files + image assignments
   - Update book title, author, copyright in the title/copyright pages
   - Adjust colors if the book's theme needs a different palette
3. **Run** `python compile_premium.py` → produces `<Book>_PREMIUM.pdf`
4. **Verify** by rendering 4-6 preview pages via PyMuPDF at 80 dpi; check hero pages, chapter openers, mid-chapter spreads.
5. **Iterate** on any issues (cropping, duplicates, overlaps) using the debugging patterns in `REFERENCE.md` below.
6. **Copy `compile_premium_epub.py`** and build the matching EPUB.
7. **Upscale cover** to 1600×2560 JPG for Amazon KDP listing.
8. **Clean up** intermediate preview files before handing back to user.

## Common asks the user makes after first build

- "Titles look like spam" → remove accent color from titles, keep only on lead-in words.
- "Images are cropped" → flip COVER to CONTAIN fit.
- "Too many blank pages" → fix double PageBreak in emitter.
- "Chapter title appears twice" → add source-text header detection and skip duplicates.
- "Looks like a boring novel, not a manifesto" → add more hero image rotation, add pillar dividers, amplify section header styling.

## Verification checklist before delivery

- [ ] Page 1 is the cover, full-bleed, uncropped
- [ ] Every chapter has a hero image page + a clean chapter opener page
- [ ] No duplicate titles in chapter body
- [ ] No blank pages between hero and chapter content
- [ ] Section headers in ink (NOT accent color)
- [ ] Drop-in lead-words in accent color in paragraph one only
- [ ] Page numbers present after front matter
- [ ] Back matter (protocols, glossary, acknowledgments) included
- [ ] EPUB validates (use `epubcheck` if available)
- [ ] Amazon cover JPG generated at 1600×2560

## File layout the skill expects in the book project

```
<book_folder>/
  chapters/            # source text
    00_front_matter.txt
    01_chapter.txt
    02_chapter.txt
    ...
  images/              # PNG/JPG images
    cover.png
    hero_ch01.png
    hero_ch02.png
    support_*.png
  compile_premium.py   # adapted from reference
  compile_premium_epub.py
  BOOK_PLAN.md         # structure doc
  marketing/
    amazon_listing.txt
    booktok_quotes.txt
  <Book>_PREMIUM.pdf   # final
  <Book>_PREMIUM.epub  # final
  <Book>_Cover_Amazon.jpg
```
