# InFrame Risk - General Contractor

InFrame Risk paid search landing page for general contractors buying their
own insurance program. Serves campaign 3.1 Construction Broker and 3.3 Risk
Advisory. Static, no build step.

Live page: `index.html`. One self-contained file, all CSS and JS inline.

## Deploy on Render

New > Static Site > connect this repo, then:

| Setting | Value |
| --- | --- |
| Build Command | *(leave blank)* |
| Publish Directory | `.` |

## Before this takes ad traffic

1. Set `REVIEW_MODE = false` near the top of the inline script.
2. Remove the `noindex` meta tag and the `X-Robots-Tag` header in `render.yaml`.
3. Settle the open copy items below.

## Copy status

Adapted from the specialty trade contractor page. Most of it transfers
verbatim from the approved wireframe. Twelve items are new or changed and
are listed in the copy delta in the review thread. Two need InFrame input
before launch:

- The general contractor revenue band. The trade band ($5M to $50M) was
  removed from FAQ 3 rather than published on a page it does not describe.
- The second contracted case study. Module 07 ships as an empty proof slot.
