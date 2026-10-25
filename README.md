# FormReady PDF Editor — browser-only PDF editor

A cover-based PDF editor that runs 100% in the browser. Files are never uploaded. Limits: 25 MB, 100 pages.

## How it works

1. **Open PDF.** The file stays in the browser. Encrypted or scanned PDFs are refused with a clear message.
2. **Read the page (pdf.js).** Text items are split into words using real glyph widths. Each word keeps font, bold, size, colour and baseline.
3. **One shared page model.** Built once and used by every tool, by Save and by the self-check. No second copy in another code path.
4. **Edit list (Undo/Redo).** Every gesture adds the same kind of edit. Groups undo together. Nothing touches the file until Save.
5. **Save and check (pdf-lib applies the edits).** Cover patch over old text, then new text in DejaVu Sans or Bold; highlights, boxes, drawings, images; form field values; page tools (copy pages in new order and rotation); optional Flatten (every page becomes an image).
6. **Self-check on the SAVED bytes.** File differs from the original, page count is right, no text lost, new text found in the saved file, form values read back. Flattened files skip the text checks and say NOT RUN. Only if all checks pass does Download unlock. If not, a plain message, the original file unchanged.

Tools: tap-a-word edit, Add text, Draw, Sign, Find and Replace (all pages, highlights matches, Replace next or Replace all, case option), Pages (thumbnails: rotate, delete, reorder), Flatten, Image, Highlight, Box, Form fill (with read-back check), Move (any word or a whole line).

## How it was built

See `docs/flow-map.md` — the full build journey (14 steps), the inside story, release steps, and build stamps p/q/r.

## Deploy it

Staging first, then production (this is how the live tool shipped):

1. Host `index.html` + `lib/` on a static host. No server, no build step — it is a standalone page.
2. Test on staging: phone, laptop, Adobe Reader, with real PDFs and screenshots.
3. Publish to production only after the owner approves the tests.
4. Rollback = republish the previous deploy.

The live tool runs at https://formready-app.netlify.app/pdf-editor/ (Beta).

## Honest limits

- Edited text is only COVERED. The original text stays inside the file unless Flatten on save is ticked. Real removal needs PDFium (not built).
- Text is written in DejaVu Sans or Bold (no Hindi). Rotated pages cannot be edited (page tools can rotate them). Scans, Type3 fonts, signed and password PDFs are not edited.
- Filling a form plus page tools or Flatten makes fields plain content. Reorder or delete may drop metadata.

## Libraries (local, in `lib/`)

- pdf.js (`pdf.min.js` + `pdf.worker.min.js`) — reading
- pdf-lib (`pdf-lib.min.js`) — writing
- fontkit (`fontkit.umd.min.js`) — font metrics

## Licence

MIT. See `LICENSE`.
