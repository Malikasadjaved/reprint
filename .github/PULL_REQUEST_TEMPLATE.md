<!-- Thanks for contributing. Keep this short — a few honest lines beat a filled-in form. -->

## What this changes

<!-- And why. If it fixes an issue: Fixes #12 -->

## How you tested it

<!--
Please don't say "works for me" for anything touching page ordering or placement — that looks
right far more often than it is right, and a duplex bug isn't noticed until 200 sheets are wasted.

The convention is to label each page with its own number, run the operation, and read the labels
back out. There's a console snippet in CONTRIBUTING.md. Paste what came out:

  fronts: PAGE 1, PAGE 3, PAGE 5, PAGE 7
  backs:  BLANK, PAGE 6, PAGE 4, PAGE 2
-->

## Checklist

- [ ] Both modes still work — an overlay export and a duplex split
- [ ] Tried an odd **and** an even page count
- [ ] Tried a page size other than A4
- [ ] Works at phone width
- [ ] Works in light **and** dark mode
- [ ] No new dependencies, no build step, no network requests
- [ ] User-facing wording is plain enough for someone nervous about breaking the printer
