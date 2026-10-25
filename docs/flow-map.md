# FormReady PDF Editor — build flow map

How the tool was built, how it works inside, and how it shipped. Everything runs in the browser; files are never uploaded.

## The build journey, step by step

1. Brief and rules
2. Engine choice
3. First editor (v0)
4. Font matching
5. Download fix
6. Gap and shift
7. Add text, Draw, Sign
8. Find and Replace
9. Pages and Flatten
10. Image, Highlight, Box
11. Form fill and Move
12. Mobile fixes
13. Fixes from real PDFs
14. Deploy

Browser-only, no upload, 25 MB / 100 pages, permissive licences, honest failures instead of silent bad edits.

pdf.js to read, pdf-lib to write. PDFium WASM cannot load on the hosted page, so editing is Cover only.

Open PDF, tap a word, type, Done. Save and self-check, then download.

DejaVu Sans and Bold embedded. Bold, size and colour copied from the tapped word.

Always-visible Download button. First tap saves and checks, second tap downloads.

Close the gap or Shift following text when a word gets shorter or longer. One Undo reverses it.

Added text snaps to the nearest line. Style picker lists the PDF's fonts and sizes.

Searches all pages, highlights matches, Replace next or Replace all, case option.

Thumbnails: rotate, delete, reorder. Flatten turns pages into images so old text is truly gone.

Drag a box to place. Tap an item to move, resize or remove it. Done button leaves a tool.

Fill PDF form fields with read-back check. Move any word or a whole line.

Hidden-button bug fixed, scrollable toolbar, Tools toggle, always-visible Done and Download.

Real glyph widths, cover trimmed to ink edges, Shift also moves already-edited words (build q).

Standalone index.html, site theme applied separately. Staging first, then production (build r).

## How the tool works inside

From Open PDF to a checked, downloadable file.

### 1. Open PDF

Limits: 25 MB and 100 pages. The file stays in the browser. Encrypted or scanned PDFs are refused with a clear message.

### 2. Read the page (pdf.js)

Text items are split into words using real glyph widths. Each word keeps font, bold, size, colour and baseline.

### 3. One shared page model

Built once and used by every tool, by Save and by the self-check. No second copy in another code path.

Tools: Tap word edit, Add text, Move, Draw / Sign, Find and Replace, Image / Highlight / Box, Form fill / Pages.

### 4. Edit list (Undo and Redo)

Every gesture adds the same kind of edit. Groups undo together. Nothing touches the file until Save.

### 5. Save and check (pdf-lib applies the edits)

a) Cover patch over old text, then new text in DejaVu Sans or Bold.
b) Highlights, boxes, drawings, images.
c) Form field values.
d) Page tools: copy pages in new order and rotation.
e) Optional Flatten: every page becomes an image.

### 6. Self-check on the SAVED bytes

File differs from the original. Page count is right. No text lost. New text found in the saved file. Form values read back. Flattened files skip the text checks and say NOT RUN.

All checks pass? NO: plain message, original file unchanged. YES: Download PDF, Undo, size shown, warning if the file grew a lot.

### Honest limit

Edited text is only COVERED. The original text stays inside the file unless Flatten on save is ticked. Real removal needs PDFium (not built).

## From build to production

Testing loop: build and syntax check, owner tests (phone, laptop, Adobe Reader), bug found? Fix and rebuild. Repeat until the owner says the tests pass. New build stamp each time.

### Release steps

1. Hand over: index.html + deploy brief.
2. Theme: restyle the style block only.
3. Staging: Netlify preview URL. Owner tests.
4. Approve: owner says publish to production.
5. Production: /pdf-editor/ goes live with Beta badge.
6. Check live: stamp, no red banner, toolbar, Download.
7. Watch a week: collect tester PDFs and screenshots.
8. Rollback: republish the previous Netlify deploy.

Production before build r was deploy 6addc14fd9eb52994c5040f0 (rollback target).

### Build stamps that mattered

- p: Move tool, Add text, Pages, Form fill. First themed live build.
- q: Real glyph widths, ink-trimmed covers, shifted edited words (undo safe).
- r: q code merged into the themed live file. Approved on staging.

### Known limits (also on the tool page)

Cover only: old text stays in the file unless Flatten is on. True removal with PDFium WASM, self-hosted on Netlify.

Text is written in DejaVu Sans or Bold (no Hindi, excluded by owner). Right-aligned numbers keep their right edge. Scans, Type3 fonts, signed and password PDFs are not edited. Rotated pages cannot be edited (page tools can rotate them). Filling a form plus page tools or Flatten makes fields plain content. Limits: 25 MB, 100 pages. Reorder or delete may drop metadata.

### Not built / next ideas

Original-font reuse instead of DejaVu only. Golden test corpus and automatic regression checks. Real Android low-end load-time measurement. Shapes (circle, line) and signature as image. Only after tester feedback.
