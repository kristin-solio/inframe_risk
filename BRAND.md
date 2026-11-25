# The new InFrame Risk brand on these pages

Every page under `public/` was moved off the old navy-and-amber system
and onto the brand in the **InFrame Style Guidelines (Bright Scout, Nov
2026)**, as captured in the *InFrame Risk Design System 2.0* artifact.

Nothing about the copy, the forms, the tracking or the URLs changed.
This was a visual rebrand only.

## What the brand is

| | Old (retired) | New |
| --- | --- | --- |
| Primary | Amber `#EFB921` | **Iron `#8C3014`** |
| Dark ground | Navy `#222F3E` | **Deep Black `#1F1F1F`** |
| Secondary | — | **Teal `#698893`** |
| Light ground | Cool `#F4F6FB` | **Beige `#F7F6F4`** |
| Type | Manrope 400/600/700 | **Switzer Regular 400 only** |

**Navy `#222F3E` and amber `#EFB921` are retired.** They appear nowhere
in `public/` any more, and neither do the class names that referenced
them (`rule-amber`, `band--amber`, `band--steel`, `band--dots` are now
`rule-accent`, `band--tint`, `band--sunken`, `band--subtle`).

Other rules the guidelines set, now followed here:

- **One weight.** Switzer Regular 400 for headlines, body, buttons and
  labels alike. Every step of hierarchy is size and colour, never bold.
  `b` and `strong` are 400 and carry emphasis in colour.
- **Square corners.** 2px on controls, 4px on cards and panels. The only
  round thing left was a numbered step marker; it is now a square chip.
- **No gradients.** The hero's three-stop navy gradient and the dotted
  steel band are gone. Photography takes a flat Deep Black overlay at
  66%, the top of the 45–66% range the guidelines allow.
- **Eyebrows** are 12px uppercase at +0.12em with the 6px square brand
  bullet, in Iron on light grounds and Beige on dark.
- **Focus** is a 2px Teal ring at 2px offset, everywhere.
- **Stepped skyline edge.** The section below the hero steps up into the
  photograph on the brand's right-angled stair-step. It is drawn as a CSS
  mask, so it always takes that section's own colour, and it is wrapped
  in `@supports` so older browsers simply don't get it.

## Where the assets are

Copied from the design system, unmodified except where noted:

```
public/assets/brand/
  fonts/Switzer-Variable.woff2, Switzer-VariableItalic.woff2
  logo/logotype_{white,black,iron}.svg, logomark_{beige,white,iron}.svg
  photography/site-team-aerial.jpg, towers-looking-up.jpg, welding.jpg
```

The logo SVGs had a ~12KB C2PA metadata block stripped; the artwork
itself is byte-identical and was never redrawn or recoloured. Switzer is
now self-hosted, so the pages no longer call Google Fonts at all.

The old `public/assets/inframe-logo-*.png` and `inframe-hero-city.webp`
are unused and can be deleted once you're happy with staging.

## Which photograph each page uses

| Page | Hero |
| --- | --- |
| `/` and `/general-contractor-insurance` | `site-team-aerial.jpg` |
| `/contractor-insurance` | `welding.jpg` |
| `/counterparty-compliance` | `towers-looking-up.jpg` |
| `/builders-risk`, `/broker`, `/trade` | none — solid Deep Black |
| `/thank-you`, `/thank-you-received` | none — solid Deep Black |

`/broker` and `/trade` were built with an imageless hero on purpose, to
keep the largest contentful paint off an image while the Quality Score
work is running. That decision was left alone: their heroes are now a
solid Deep Black brand field rather than the old navy gradient.

## Two judgement calls worth a look

**1. The secondary button on dark grounds is Teal 700, not Teal.** The
guidelines list "White on Teal" as an approved pair, but white on
`#698893` measures 3.8:1, under the 4.5:1 a 16px button label needs. The
darker `--teal-700 #4D6872` carries the same colour at 6.1:1. Say the
word if you'd rather have the lighter Teal and accept the measurement.

**2. The highlighted phrase in the hero headline is Teal.** It used to be
amber, which was bright on navy. Iron is too dark to use as text on
Deep Black, so the options were Beige (nearly indistinguishable from the
white around it) or Teal. Teal reads as a real accent and passes at
display size. It is most noticeable on `/broker` and `/trade`, where the
inserted keyword covers most of the headline — worth a look there first.

## Checks run

- Contrast audited on every rendered page against each element's actual
  background: **no text under its WCAG AA threshold** on any of them.
  Three failures that predate the rebrand were fixed on the way through
  (the large step numeral at 1.3:1, the footer separator at 3.8:1, and
  the numbered step chip, where `.vstrip__item span` out-specified
  `.vstrip__n` and kept body grey on an Iron chip).
- Rendered at 1440px and on an iPhone 13: no horizontal overflow, sticky
  mobile bar intact.
- `/` still mirrors `/general-contractor-insurance` exactly, bar the
  `assets/` path depth, as DEPLOY.md requires.

## One thing to check on staging

The licensed-states map is still `https://inframerisk.com/assets/us-states-outline.svg`,
an asset on the main site that this sandbox can't reach, so it has not
been seen against the new palette. It was drawn for the navy page. If it
reads cold or clashes, it needs recolouring to Teal or Beige — the brand
library has no map, which is why the design system's own rebrand kit
dropped it for the logomark instead.
