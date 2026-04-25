# Illustrated Book — Technical Reference

Read this when implementing or debugging `compile_premium.py`.

## ReportLab architecture

### Page templates

```python
# Bleed first so document starts on a full-bleed cover page
doc.addPageTemplates([
    PageTemplate(id='Bleed', frames=[bleed_frame], onPage=lambda c,d: None),
    PageTemplate(id='Text', frames=[text_frame], onPage=on_page_paper),
])
```

### Full-bleed image flowable

ReportLab has no native "draw an image covering the entire page" primitive. Build one:

```python
from reportlab.platypus import Flowable

class BleedDrawer(Flowable):
    def __init__(self, path):
        super().__init__()
        self.path = path
        self.width = FRAME_W
        self.height = FRAME_H
    def wrap(self, aw, ah):
        return (aw, ah)
    def draw(self):
        canv = self.canv
        pimg = PIL.Image.open(self.path)
        iw, ih = pimg.size
        page_aspect = PAGE_W / PAGE_H
        img_aspect = iw / ih
        # CONTAIN fit
        if img_aspect > page_aspect:
            draw_w = PAGE_W
            draw_h = draw_w / img_aspect
            dx, dy = 0, (PAGE_H - draw_h) / 2
        else:
            draw_h = PAGE_H
            draw_w = draw_h * img_aspect
            dx, dy = (PAGE_W - draw_w) / 2, 0
        canv.saveState()
        canv.setFillColor(BG_DARK)
        canv.rect(-PAGE_W, -PAGE_H, PAGE_W*3, PAGE_H*3, fill=1, stroke=0)
        canv.drawImage(self.path, dx, dy, width=draw_w, height=draw_h, mask='auto')
        canv.restoreState()
```

### Switching templates for a bleed page

```python
# Marker flowable replaced at build time
class FullBleedImage:
    def __init__(self, path): self.img_path = path

def emit_full_bleed_pages(flowables):
    out = []
    i = 0
    while i < len(flowables):
        item = flowables[i]
        if isinstance(item, FullBleedImage):
            if not out:
                # First item: no leading PageBreak (document is already on page 1)
                out.append(NextPageTemplate('Bleed'))
                out.append(BleedDrawer(item.img_path))
                out.append(NextPageTemplate('Text'))
                out.append(PageBreak())
            else:
                out.append(NextPageTemplate('Bleed'))
                out.append(PageBreak())
                out.append(BleedDrawer(item.img_path))
                out.append(NextPageTemplate('Text'))
                out.append(PageBreak())
            # Strip following PageBreak to prevent blanks
            j = i + 1
            if j < len(flowables) and isinstance(flowables[j], PageBreak):
                i = j + 1
                continue
        else:
            out.append(item)
        i += 1
    return out
```

### Drop-in lead words (fake drop cap)

```python
def lead_in(text):
    words = text.split()
    n = min(4, max(2, len(words)//4)) if len(words) > 3 else 1
    lead = ' '.join(words[:n]).upper()
    rest = ' '.join(words[n:])
    return f'<font name="SerifB" color="#1f7a3f">{lead}</font> {rest}'
```

Render as a normal paragraph with `firstLineIndent=0` so the lead-in starts flush left.

### Section header detection

```python
import re
SECTION_HEADS = re.compile(r'^(THE CODE|THE ANCESTOR|THE PROTOCOL|THE PROOF|---)(\s|:|$)')

def is_section_head(line):
    if SECTION_HEADS.match(line):
        return True
    if line.isupper() and 3 <= len(line) <= 50 and '.' not in line and len(line.split()) <= 5:
        return True
    return False
```

### Skipping duplicate chapter titles

The source text often starts with something like `CHAPTER 1: THE CODE EXISTS` — which duplicates the Roman numeral + subtitle we already rendered.

```python
rendered_titles = {label.upper().strip()}
if sub:
    rendered_titles.add(sub.upper().strip())

# While iterating source body:
if kind == 'head':
    norm = content.upper().strip().rstrip(':.').strip()
    if norm in rendered_titles: continue
    if re.match(r'^CHAPTER\s+[IVXLCDM0-9]+', norm): continue
    if sub and sub.upper().replace('—','-') in norm.replace('—','-'): continue
    # ... else render as section header
```

## EPUB CSS mirror

Match the PDF aesthetic in EPUB:

```css
body { font-family: Georgia, serif; background: #f4efe3; color: #1a1f1a; line-height: 1.7; }
h1.chapter-num { font-family: Impact, sans-serif; font-size: 3.5em; color: #1a1f1a; }
h2.chapter-sub { font-family: Georgia, serif; font-style: italic; font-weight: normal; color: #1a1f1a; }
h3.section-head { font-weight: bold; text-transform: uppercase; letter-spacing: 0.08em; font-size: 0.95em; color: #1a1f1a; }
p.lead-in .lead { font-weight: bold; color: #1f7a3f; text-transform: uppercase; letter-spacing: 0.05em; }
div.hero { page-break-before: always; page-break-after: always; background: #0b0f0b; text-align: center; }
div.hero img { max-width: 100%; max-height: 100vh; }
```

## Amazon KDP cover

```python
from PIL import Image
cover = Image.open('images/cover.png')
cover.resize((1600, 2560), Image.LANCZOS).convert('RGB').save(
    'Book_Cover_Amazon.jpg', 'JPEG', quality=95
)
```

## Verification via PyMuPDF

Quick preview render to catch layout bugs without opening Acrobat:

```python
import fitz
doc = fitz.open('Book_PREMIUM.pdf')
for pn in [0, 1, 14, 15, 16, 22]:  # cover, title, pillar, hero, chapter, chapter 2
    pix = doc.load_page(pn).get_pixmap(dpi=80)
    pix.save(f'_preview_p{pn+1}.png')
```

Then Read the PNGs and eyeball for cropping, duplicates, blank pages, font sizing.

## Known edge cases

1. **Image aspect exactly equals page aspect**: CONTAIN and COVER give the same result (perfect fit), no bars.
2. **Single-letter Roman numeral (I)**: Impact at 36pt can look lonely. Consider padding with a small spacer or accepting it.
3. **Very long chapter subtitles**: Georgia italic 14pt may wrap awkwardly. Either trim the subtitle or drop font size to 12pt for long ones.
4. **Source text has inline emojis**: Strip them in preprocessing — most fonts don't render them and ReportLab silently drops them.
5. **EPUB readers ignore background colors**: cream paper background may not show on Kindle. Still include the CSS — Apple Books and Kobo honor it.

## Performance

- A 225-page book with 30 full-bleed PNG images at 1024×1536 produces a ~150 MB PDF and ~60 MB EPUB.
- If size is a problem, downsample hero images to 900×1350 and 80-85 JPEG quality before compilation. ReportLab accepts JPEG natively.
- EPUB size can also be reduced by converting hero PNGs to JPEG (no transparency needed for photographic posters).

## Future extensions

- True text-wrap around inline images: use `Frame` objects with custom shapes or switch to a layout engine like WeasyPrint (HTML→PDF).
- Two-column layouts for reference sections.
- Colored section dividers (thin ink rule across page).
- Font embedding control for richer typography (Cormorant, Playfair Display, etc.).
