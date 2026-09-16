# Deploying go.inframerisk.com

Two Render static sites, both from this repo, both publishing `public`.
The only difference between them is the branch they track.

| | Service | Branch | Serves |
| --- | --- | --- | --- |
| Staging | `go.inframerisk.com-staging` | `claude/inframe-risk-landing-pages-z1j9sg` | work in review |
| Production | `go.inframerisk.com` | `main` | approved copy only |

## Settings, identical on both

| Setting | Value |
| --- | --- |
| Build Command | *(leave blank)* |
| Publish Directory | `public` |
| Root Directory | *(leave blank)* |

Nothing is built. Every page is one self-contained HTML file with its CSS
inline. The only external request is Google Fonts.

## What each URL serves

| Path | Page |
| --- | --- |
| `/` | Index of the set |
| `/contractor-insurance` | Subcontractor |
| `/general-contractor-insurance` | General contractor |
| `/counterparty-compliance` | Counterparty compliance |
| `/wireframe.html` | The source review document |

These match the paths in the Google Ads plan's Landing Page Map, so the
final ad destinations do not change when the domain is attached.

## Promoting staging to production

Merge the working branch into `main`. Production redeploys on its own.
Nothing else differs between the two environments, so anything that looks
right on staging looks the same in production.

## Before production takes ad traffic

1. Remove the `noindex, nofollow, noarchive, nosnippet` meta tag from each
   page. It is there because the copy is unapproved, not as a strategy, and
   the plan expects these pages to earn organic long-tail traffic.
2. Clear the outstanding approval register items. Several claims are still
   unapproved.

## Editing

`public/` is the only copy. Edit the page in `public/<slug>/index.html`.
Shared artwork lives once in `public/assets/` and every page reaches it as
`../assets/`.
