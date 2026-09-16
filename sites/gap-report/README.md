# InFrame Risk - Counterparty Gap Report

InFrame Risk paid search landing page 3 of 3. Counterparty gap report offer. Static, no build step.

Live page: `index.html`. One self-contained file. All CSS and JS are inline.
No build step, no bundler, no dependencies.

## Deploy on Render

New > Static Site > connect this repo, then:

| Setting | Value |
| --- | --- |
| Build Command | *(leave blank)* |
| Publish Directory | `.` |

`render.yaml` is included if you would rather deploy it as a Blueprint.
The dashboard settings above are the fallback and are the ones to trust
if the Blueprint disagrees.

## External requests

The page loads two things from outside this repo:

- Google Fonts (Manrope 300/400/600/700)
- The review comment endpoint, a Google Apps Script web app

Everything else, including the hero artwork and the wordmark, is in
`assets/` and is served from this repo.

## Before this takes ad traffic

1. Set `REVIEW_MODE = false` near the top of the inline script. That removes
   the per-module Comment buttons and the floating dock.
2. Remove the `noindex` meta tag and the `X-Robots-Tag` header in
   `render.yaml`, once the copy is approved.
3. Confirm the outstanding approval register items. Several claims on these
   pages are still unapproved.

## Copy status

Unapproved. Every headline, paragraph, list item, FAQ pair, form label and
button label is lifted verbatim from the wireframe review document and is
under client approval. Do not edit copy here; edit it at the source.
