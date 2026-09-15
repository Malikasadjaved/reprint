# Reprint

**Stop feeding the same sheet through the printer twice.**

Two everyday office problems, one small tool:

1. You print a document, then put that printed page **back** into the printer to add a second piece
   of content into its blank area. It jams, it prints crooked, and one mis-feed ruins the sheet.
2. Your printer only prints one side, so printing double-sided means working out by hand which
   pages go where — and getting it wrong wastes the whole stack.

Reprint fixes both. It merges the two contents into **one** PDF you print once, and it splits a
document into correctly ordered front and back files for manual double-siding.

- Runs entirely in your browser — **no server, no upload, no account**. Your documents never leave
  your computer.
- One HTML file. Open it from a USB stick if you like. Works offline once loaded.
- Millimetre-accurate placement, verified against the PDF's own coordinate space.
- Free and MIT licensed, forever.

---

## For everyone: the simplest possible way to use this

**You do not need to install anything or understand anything technical.**

### If you want to add something to a page you already print

1. Open `index.html` — double-click it, and it opens in your normal web browser.
2. Click the first **Choose File** button and pick the page you already print.
3. Click the second one and pick the thing you want to add.
4. You'll see your page with a green box on it. **Drag the box to the empty space** where the new
   content should go. Drag a corner to make it bigger or smaller.
5. Click **Download merged PDF**.
6. Print that file. Everything is on the page in one go. No re-feeding.

That's it. If it prints slightly off, see *"It printed a few millimetres off"* below.

### If you want to print on both sides with a one-sided printer

1. Open `index.html` and click the tab **Print both sides (manual duplex)**.
2. Choose your PDF.
3. Click **Split into front and back files**. You get two files.
4. Print the **FRONTS** file.
5. Take that stack, flip it over, put it back in the paper tray.
6. Print the **BACKS** file. Done.

**Do the feed test first.** Click *Download 2-page feed test*, print page 1, put that one sheet back
in flipped, print page 2. Whether the word BACK comes out upright or upside down tells you which
setting to pick. One sheet of paper, once in your life, and you never guess again.

**Always test a long job on 4 sheets of scrap paper first.** Printers differ.

---

## Features

### Add content to an already-printed page

| | |
|---|---|
| **Drag-and-drop placement** | Drag the box, or type exact millimetres. Corner handles resize; hold Shift to keep the shape. |
| **Exact coordinates** | X, Y, width and height in millimetres, measured from the bottom-left corner — the same origin PDF itself uses. |
| **Rotation** | 0°, 90°, 180°, 270°, each landing on precisely the footprint you drew. |
| **Position presets** | Top half, bottom half, quadrants, centre, full page, and fit-to-aspect-ratio. |
| **Alignment test sheet** | Stamps a labelled 10 mm grid on your page so you can read the right coordinates straight off the paper. |
| **Printer offset** | One global nudge in tenths of a millimetre to correct a printer that feeds slightly off. |
| **Multi-page** | Copy one placement to every page, or map content page *n* onto base page *n*. |
| **Accepts** | PDF, PNG or JPEG as the added content. |
| **Live preview** | See the real page with the real content in place before you commit paper to it. |
| **Vector output** | Content is embedded as a Form XObject — real text and graphics, not a screenshot. Stays sharp, stays searchable. |

### Manual duplex

| | |
|---|---|
| **Correct page ordering** | Fronts and backs split into two files, already in printing order. |
| **Re-feed direction** | Reverse order (most printers) or same order. |
| **Flip edge** | Long edge for upright backs, short edge to turn the backs 180°. |
| **Odd page counts** | A 7-page document becomes 4 sheets with a blank back, padded automatically so the two stacks can never drift out of alignment. |
| **Feed test sheet** | A 2-page test that tells you your printer's behaviour using one sheet of paper. |
| **Combined file** | Optionally one PDF with fronts then backs, if you prefer printing page ranges. |
| **Built-in instructions** | The exact steps, on screen, in plain language. |

---

## Troubleshooting

**It printed a few millimetres off.**
That's the printer's paper feed, not the file. Don't move every box — put the correction into
**Printer offset** once and it shifts every placement at export.

**I can't tell exactly where the blank area is.**
Use **Download alignment test sheet**, print it, and read the numbers off the grid. They are
millimetres from the bottom-left corner.

**My page previews sideways.**
That PDF carries a page-rotation flag, so it displays turned relative to how its content is stored.
Reprint previews it in the unrotated space your content is actually placed into, and says so on
screen. Trust the numbers, not the orientation.

**My PDF won't open.**
Encrypted or password-protected PDFs must be unlocked first.

---

## Fully offline / self-hosting

`index.html` pulls [pdf-lib](https://pdf-lib.js.org/) and [pdf.js](https://mozilla.github.io/pdf.js/)
from a CDN. To make it work with no network at all, download these three files next to it and point
the three `src` attributes at the local copies:

- `pdf-lib.min.js`
- `pdf.min.js`
- `pdf.worker.min.js`

For GitHub Pages: enable Pages on this repo and it's live. No build step, no dependencies to install,
nothing to configure.

---

## Ideas for what to add next

Ordered roughly by how much they'd help a typical user versus how much work they are. Pull requests
very welcome — pick any one.

**High value, small effort**

- **Save and reload placements.** Most people reprint the *same* form every month. Export the box
  position as a small `.json` preset and load it next time instead of re-dragging.
- **Drag-and-drop files** onto the window, instead of the Choose File buttons.
- **N-up printing** — 2 or 4 pages per sheet, to halve the paper.
- **Merge and reorder** — a general page organiser, since people already have the file open.
- **Snap to the page edges and to the other content** while dragging.
- **Undo/redo** for placement changes.

**High value, more effort**

- **Automatic blank-area detection.** Scan the base page for the largest empty rectangle and put the
  box there on its own. This is the single biggest usability win available.
- **Translations.** The audience for this tool is global and largely non-English-speaking.
- **A print-shop batch mode.** Apply one placement across a folder of PDFs at once.
- **Booklet imposition** — proper saddle-stitch page ordering, folded and stapled in the middle.
- **Fill-in fields.** Type text directly onto the page instead of supplying a second PDF, which
  covers the very common "add a signature, a date, a stamp or a reference number" case.
- **A progressive web app** so it installs like a normal program and opens offline by default.

**Nice to have**

- Overlay more than one piece of content per page.
- Opacity and blend control for watermarks.
- A command-line version for servers and batch jobs.

---

## How it works, technically

The base PDF is loaded and re-saved by pdf-lib; the original page content is never rasterised or
altered. Added content is embedded as a Form XObject and drawn with an explicit transform.

Placement is computed in PDF user-space points (1 pt = 1/72 in) and converted to millimetres only
for display. Rotation is applied about the box anchor with the offset compensation PDF requires, so
a rotated placement occupies exactly the footprint drawn on screen — verified for all four angles by
replaying the transform matrices out of the exported file.

For manual duplex, sheet *i* carries page *2i* on the front and *2i+1* on the back. The back list is
always padded to one entry per sheet, which is what stops odd-length documents drifting out of
alignment when the stack order is reversed.

## Limits, stated honestly

- In overlay mode the second content goes on the **same side** of the sheet. Use the duplex tab for
  front-and-back.
- Encrypted PDFs must be unlocked first.
- Very large scanned PDFs preview slowly; the export is unaffected.
- Reprint cannot know your printer's feed behaviour — that's what the feed test is for.

## Contributing

Issues and pull requests are welcome, including from people who are not programmers: a clear bug
report saying which printer did what is genuinely useful.

## Author

**Malik Asad Javed** — <malik.asad1x@gmail.com>

## License

MIT — see [LICENSE](LICENSE). Use it, change it, sell it, ship it inside your own product. No
permission needed and no attribution demanded.
