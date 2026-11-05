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

Nothing below has been approved. The [CONFIRM] tags are mockup
scaffolding and come out before anything publishes. There is no longer a
[NEEDS CLIENT APPROVAL] tag on either page, because there is no longer
any unapproved client result to mark.

## Client results: none on the page

The two pages carried a strip of five client results under the hero. None
of them had been approved for publishing, so **all five are off the pages
and none of them are in the repo.** They are listed here only so the
register is complete.

| # | Claim | Attributed to |
| --- | --- | --- |
| 1 | 12+ carriers shopped. Fully bound 5 days after switching. | Golf course builder |
| 2 | 18.5% more property insured for 7.8% less. | Texas shopping center |
| 3 | 25 of 26 subs had a gap their certificate did not show. | California general contractor |
| 4 | 164 certificates sent in our first 3 days. | Southern California flooring contractor |
| 5 | "This is the best renewal proposal I've seen." | Manager, Texas commercial property owner |

Item 2 is a price and savings claim, a different approval category from
the other four, and needs the figures on record as well as the client's
permission.

In their place both pages carry a **value strip**. That band sits in the
slot a paid B2B page normally gives to immediate proof, so filling it
with license numbers would waste it. It carries the offer instead, as
three numbered steps: a specialist reads your program, you hear back in
3 business days, you decide what happens next. The third answers the
objection the wireframe calls the decisive one, which is the fear that
asking for a review means handing over the whole program.

The licensing line underneath keeps the trust signal in the band without
spending the band on it: brokerage name, licensed in all 50 states and
DC, National Producer Number, California license. All of it is word for
word from the approved footer copy or a public license record.

Both pages also gained a short line above the headline naming what
InFrame is and who it is for, which is the "eyebrow" Tas Bober's
framework puts above the hero. On /trade it swaps with the trade.

When a client result is approved, it goes in the slot marked in the
markup:

    <!-- APPROVED CLIENT RESULTS GO HERE. Empty on purpose. -->

near the foot of the `<section class="trust">` block on each page.

**Worth knowing:** none of this is social proof. The offer and the
licensing both answer "can I trust you", but neither answers "has this
worked for someone like me". One approved client result, or a row of
carrier logos, would do more for conversion than everything now in that
band. It is the highest-value item on this list to get signed off.

## [CONFIRM] · claims written for these pages, not supplied

### Advisor section, both pages
| Item | Needed |
| --- | --- |
| Daniel | Last name, title, years of experience, headshot |
| Paul | Last name, title, years of experience, headshot |
| Brian | Last name, title, years of experience, headshot |

Photographs on both pages are generated placeholders.

### Value strip, both pages
| Item | Note |
| --- | --- |
| A specialist reads your program: your policies and your contracts, side by side, read by a licensed advisor. | Describes the service. Needs Daniel to confirm it is what happens. |
| Nothing about your current program changes unless you ask us to move it. | The most load-bearing sentence in the band. It removes the fear of a bait-and-switch, so it has to be exactly true. |

"You hear back in 3 business days" carries no tag: it is already live copy
on the thank-you page, which says reviews come back within three business
days. Worth re-checking that it still holds.

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
- Specialist programs for commercial [trade] contractors. (On the licensing line of the value strip. This is the proof line that swaps with `?trade=`, alongside the headline, the subhead, and the eyebrow above the headline.)
- Certificates go out fast: when a GC asks for one, you are not the reason the job waits.

## One copy-rule exception

The brief rules out the word "platform". It appears once on each page, in
the second paragraph of the footer legal fine print, which the brief also
says to reuse. Legal copy was left word for word rather than edited.
Flagging it rather than changing it.
