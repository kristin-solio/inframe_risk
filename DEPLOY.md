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
| `/` | General contractor, so the root domain shows a real landing page |
| `/contractor-insurance` | Subcontractor |
| `/general-contractor-insurance` | General contractor |
| `/counterparty-compliance` | Counterparty compliance |
| `/pages` | Index of the set, with the campaign notes |
| `/wireframe.html` | The source review document |

`/` and `/general-contractor-insurance` are two copies of the same page.
They differ only in the depth of the `assets/` path. **Edit the one in
`public/general-contractor-insurance/` and copy it to `public/index.html`,
changing `../assets/` to `assets/`.** Editing one and not the other leaves
the root domain, which is the address the client visits, showing stale
copy.

These match the paths in the Google Ads plan's Landing Page Map, so the
final ad destinations do not change when the domain is attached.

## Promoting staging to production

Merge the working branch into `main`. Production redeploys on its own.
Nothing else differs between the two environments, so anything that looks
right on staging looks the same in production.

## Pointing go.inframerisk.com at the production site

DNS for `inframerisk.com` is hosted on **AWS Route 53** (nameservers
`ns-456.awsdns-57.com` and three siblings). The main site sits behind
CloudFront. `go.inframerisk.com` does not resolve yet, so nothing is at
risk of breaking: this adds a new subdomain and leaves the main site alone.

1. In Render, open the **production** service, then Settings, then Custom
   Domains, then Add Custom Domain. Enter `go.inframerisk.com`.
2. Render shows a DNS record to create. For a subdomain it is a CNAME
   pointing at the service's own `<name>.onrender.com` hostname. Copy that
   hostname exactly.
3. In the AWS console, open Route 53, then Hosted zones, then
   `inframerisk.com`, then Create record:
   - Record name: `go`
   - Record type: `CNAME`
   - Value: the `onrender.com` hostname from step 2
   - TTL: `300`
   - Routing policy: Simple
   Leave Alias off. Alias records only point at AWS resources.
4. Save, wait a few minutes, then click Verify in Render. Render issues the
   TLS certificate on its own once the record resolves.

Do not change the existing records for `inframerisk.com` or
`www.inframerisk.com`. Those serve the main site through CloudFront.

Staging can stay on its `onrender.com` URL. If it ever gets a subdomain,
the steps are the same with a different record name, and it must keep the
`noindex` tag so the two sites never compete in search.

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
