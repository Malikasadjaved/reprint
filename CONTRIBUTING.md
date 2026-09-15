# Contributing to Reprint

Thanks for being here. Reprint is a small tool with a narrow job, and it wants to stay that way —
but there is plenty left to build, and contributions of every size are welcome.

**You do not have to be a programmer to help.** Skip to [Ways to help that aren't
code](#ways-to-help-that-arent-code) if that's you.

---

## What Reprint is trying to be

Four principles. A change that breaks one of them will probably be turned down, so they're worth
reading before you start on something large.

**1. Your documents never leave your computer.**
No uploads, no servers, no analytics, no telemetry, no "sign in to continue". People put invoices,
contracts, medical forms and payslips through this tool. Any feature that needs a network round trip
is the wrong feature. This is the promise the whole project rests on.

**2. It works for someone who is not technical.**
The target user is a person in an office who has been re-feeding paper for years because nobody told
them there was another way. Plain language, no jargon, and the safe path should be the obvious one.
"Find the blank area for me" beats an accurate but intimidating control panel.

**3. No build step.**
`index.html` is the whole application. You open it and it runs — from a USB stick, from a locked-down
office machine, from a folder with no internet. That property is worth more than the convenience of a
bundler, so please don't add one, and please don't add a dependency that requires one.

**4. Wrong output is worse than no output.**
A misplaced overlay wastes one sheet. A duplex ordering bug wastes two hundred and isn't noticed
until the job is finished. Correctness here is not academic.

---

## Getting set up

```bash
git clone https://github.com/Malikasadjaved/reprint.git
cd reprint
```

Then open `index.html` in a browser. That's the entire setup — no `npm install`, no dev server, no
watcher. Edit the file, reload the page.

If you prefer serving it over HTTP:

```bash
python -m http.server 8000
```

---

## How to verify your change

**Please don't verify page ordering or placement by eye.** It looks right far more often than it is
right, and the failure mode is expensive.

The convention in this project is to build a document whose pages are labelled with their own
number, run the operation, and read the labels back out of the result. Paste this into the browser
console with the tool open:

```js
// Build a labelled document, split it, and print what actually came out.
const { PDFDocument, StandardFonts } = PDFLib;
async function labelled(n) {
  const d = await PDFDocument.create();
  const f = await d.embedFont(StandardFonts.Helvetica);
  for (let i = 1; i <= n; i++) {
    d.addPage([595.28, 841.89]).drawText('PAGE ' + i, { x: 80, y: 700, size: 40, font: f });
  }
  return await d.save();
}
async function labelsOf(bytes) {
  const doc = await pdfjsLib.getDocument({ data: bytes.slice() }).promise;
  const out = [];
  for (let i = 1; i <= doc.numPages; i++) {
    const tc = await (await doc.getPage(i)).getTextContent();
    out.push(tc.items.map(x => x.str).join('').trim() || 'BLANK');
  }
  return out;
}
// capture downloads instead of writing files
window.__caught = [];
download = (bytes, name) => window.__caught.push({ name, bytes });
```

Then drive the UI, and inspect `window.__caught`. A correct 7-page duplex split in reverse order
gives you fronts `1, 3, 5, 7` and backs `BLANK, 6, 4, 2`.

For placement, the equivalent check is to replay the transform matrices out of the exported PDF with
`page.getOperatorList()` and confirm the drawn content's bounding box matches the millimetres you
asked for. This is how the four rotation cases were verified; do the same if you touch `anchor()`.

### Before you open a pull request

- [ ] Both modes still work: overlay export, and a duplex split.
- [ ] Odd **and** even page counts, in both re-feed orders.
- [ ] A page that is not A4 — Letter, or something small like A6.
- [ ] Blank-area detection on a coloured or scanned page, not only a white one.
- [ ] The page still works at phone width.
- [ ] Light **and** dark mode.
- [ ] No new network requests, and no new dependencies.

---

## Code style

Match what's there. In short:

- **Vanilla JavaScript**, no framework, no TypeScript, no build tooling.
- pdf-lib and pdf.js are the only dependencies, and the list should stay that way.
- Comments explain **why**, not what. The existing ones are a decent guide: they mark the places
  where the obvious implementation is wrong, such as why the rotation anchor is shifted, or why a
  cell needs two differing pixels to count as ink.
- Keep user-facing strings plain. Write for someone who is nervous about breaking the printer.
- Millimetres in the interface, PDF points internally. Convert at the boundary, never mix.

---

## Ways to help that aren't code

**Translate it.** This is the single most valuable non-code contribution, and probably the most
valuable contribution of any kind. Offices that still re-feed paper by hand are everywhere, and
English-only is what limits who can actually use this. See
[issue #9](https://github.com/Malikasadjaved/reprint/issues/9) — send the translated strings and
someone will wire them in. The duplex instructions matter most: a mistranslation there wastes
someone's paper.

**Tell us what your printer did.** Reprint cannot know how your specific printer re-feeds paper, and
every model differs. A report saying "on a Canon G3020, backs come out in the same order, not
reversed" is genuinely useful and takes two minutes.

**Fix the wording.** If a label or instruction confused you, that's a bug. You were the test.

**Say where it's used.** Knowing that this is running in a school office in Lahore or a print shop in
Lagos shapes what gets built next.

---

## Pull requests

- Branch from `main`.
- One change per pull request. A refactor bundled with a feature is hard to review and hard to
  revert.
- Say what you tested, and how. "Verified with a labelled 7-page document, reverse order" tells a
  reviewer more than "works for me".
- Draft pull requests are welcome if you want feedback before finishing.

Commit messages: a short summary line, then the reasoning if the change isn't obvious. Explain why
the change is right, not just what it does.

By contributing you agree your work is released under the [MIT License](LICENSE).

---

## Reporting a security issue

Reprint has no server and no accounts, so the realistic surface is small — but if you find something
that could expose a user's document, please email **malik.asad1x@gmail.com** rather than opening a
public issue.

---

## Questions

Open an issue. A question that needed asking is usually documentation that needed writing.
