# Quality Score landing page mockups

Two new pages on the `lp-mockups-qs` branch, built to lift the Below
average **landing page experience** rating Google reports on every
non-brand keyword. Ad relevance is already Above average or Average, so
nothing here touches the ads.

| Path | Audience | Swap parameter |
| --- | --- | --- |
| `/broker` | Broker and risk program searchers | `?kw=` |
| `/trade` | Specialty trade searchers | `?trade=` |

Neither page is deployed. Staging tracks
`claude/inframe-risk-landing-pages-z1j9sg` and production tracks `main`,
so this branch publishes nowhere. `go.inframerisk.com` is unchanged.

Both pages keep `noindex, nofollow, noarchive, nosnippet`, the same way
the three current pages do.

## What carries over unchanged

The brand tokens and type scale, the masthead, the footer with its three
paragraphs of legal fine print, the sticky mobile bar, the Fillout embed
(`fkqNiqeJmsus`, one per page, in the hero, carrying
`data-fillout-inherit-parameters`), the attribution script that writes
`page` and keeps `gclid`, `gbraid`, `wbraid` and the five `utm_*` keys in
first-party cookies for 90 days, and the Tag Manager container
`GTM-5PCQ4CBV` with its noscript iframe.

Every `tel:+18668111207` href, class and link text is unchanged, so the
click-to-call trigger configured inside the GTM container keeps matching.

## New Tag Manager events

The live pages push no dataLayer events at all. These two add three,
alongside whatever the container already does. Each tracked element
carries `data-cta` with the event name plus `data-cta-id`,
`data-cta-location` and, on phone links, `data-cta-type="phone"`. Read
them in GTM as Data Layer Variables on the event.

| Event | Fires when |
| --- | --- |
| `cta_click` | Any button or phone link with a `data-cta` attribute is clicked |
| `form_view` | The hero form card is at least 10 per cent on screen, once per page |
| `form_start` | The window loses focus to the Fillout iframe, once per page |

`form_start` is indirect on purpose. Fillout renders cross-origin, so
keystrokes inside it are not readable from the page. **Verify it once on
staging, where the embed actually loads.**

## Dynamic keyword and trade insertion

Both are allowlist lookups. The URL value is never written to the page.
It only selects one of the approved entries in the block marked
`APPROVED ALLOWLIST` in each file, so no visitor-supplied text can reach
the DOM of a licensed brokerage's page. Anything unrecognised falls
through to the approved default.

- `/broker` default: `construction insurance broker`
- `/trade` default: `specialty trade contractors`

---

# Approval register

Nothing below has been approved. Both tag styles are mockup scaffolding
and come out before anything publishes.

## [NEEDS CLIENT APPROVAL] · client results, shown in a yellow tag

| # | Claim | Attributed to | On |
| --- | --- | --- | --- |
| 1 | 12+ carriers shopped. Fully bound 5 days after switching. | Golf course builder | `/broker`, `/trade` |
| 2 | 18.5% more property insured for 7.8% less. | Texas shopping center | `/broker` |
| 3 | 25 of 26 subs had a gap their certificate did not show. | California general contractor | `/broker`, `/trade` |
| 4 | 164 certificates sent in our first 3 days. | Southern California flooring contractor | `/trade` |
| 5 | "This is the best renewal proposal I've seen." | Manager, Texas commercial property owner | `/broker`, `/trade` |

**Item 2 needs a second look.** It is a price and savings claim, which is
a different approval category from the other four. It needs the client's
written permission and a defensible record of the figures.

**Items 1 to 5** are all client-specific. Each needs the named client's
permission to publish, even anonymised.

## [CONFIRM] · claims written for these pages, not supplied

### Advisor section, both pages
| Item | Needed |
| --- | --- |
| Daniel | Last name, title, years of experience, headshot |
| Paul | Last name, title, years of experience, headshot |
| Brian | Last name, title, years of experience, headshot |

Photographs on both pages are generated placeholders.

### Renewal timeline, both pages
The 120-day start is from the brief. The milestones inside it are not.
- Day 120: we read your current policies and your contracts side by side
- Day 90: we build your submission and take your program to carriers
- Day 45: we bring back what the carriers offered and explain each one
- Day 0: you renew with time to spare, certificates go out the same week

### `/broker` only
- You get a named risk advisor who knows your contracts, not a new person every time you call.
- Contract review before you sign: send us the insurance pages, we tell you what is missing before the job starts.
- Certificate and compliance tracking: we collect certificates from your subs, check them against the contract, and chase the ones that fall short.
- Claims support from someone who already read the policy.
- A renewal plan, written down, for the whole 120 days.

### `/trade` only
- Specialist programs for commercial [trade] contractors, in all 50 states and DC. (The "all 50 states and DC" half is approved copy already on the site. The "specialist programs for [trade] contractors" half is new.)
- Certificates go out fast: when a GC asks for one, you are not the reason the job waits.

## One copy-rule exception

The brief rules out the word "platform". It appears once on each page, in
the second paragraph of the footer legal fine print, which the brief also
says to reuse. Legal copy was left word for word rather than edited.
Flagging it rather than changing it.
