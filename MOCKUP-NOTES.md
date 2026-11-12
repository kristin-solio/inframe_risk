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

## Decisions from the 5 Nov call

**CTA structure.** Every call to action is the same pair in the same
order: **Get a Quote** first, **Call an Advisor** second. That holds in
the masthead, the hero, every band, the closing band and the sticky
mobile bar. No secondary carries the phone number any more; the number
lives in the footer, and every "Call an Advisor" is still a tel: link.

**Spelling: Builder's Risk,** with the apostrophe, in all situations
from here on. In headings, page titles and meta descriptions it is
written **Builder's Risk**; in running body copy it is **builder's
risk**, as an ordinary noun. Flag if it should be capitalised
everywhere as a product name instead.

**California** stays as licence 6019212, confirmed correct.

**Response time: the same business day.** The hero form card on both
pages now reads "Takes 30 seconds. We'll follow up the same business
day." Chosen over "within 4 business hours" because it needs no
definition of business hours: a form sent at 4pm on a Friday has an
unambiguous answer either way.

The three-business-day review turnaround is a different promise and was
deliberately left alone. The two sit close together, so read them as a
pair before launch: first contact the same business day, the finished
review in three.

---

# Approval register: cleared 12 Nov

Every claim written for these pages was confirmed on 12 Nov and all
[CONFIRM] tags have been removed from the markup, along with the CSS
that styled them. Nothing on either page is now waiting on sign-off.

The five client results that once sat under the hero remain off the
pages. They were never approved, and the band carries the offer
instead.

What was confirmed, for the record:

- The renewal timeline milestones at days 120, 90, 45 and 0.
- A specialist reads your policies and your contracts side by side.
- A named risk advisor who knows your contracts.
- Contract review before you sign, certificate and compliance tracking
  for subcontractors, claims support, and a written renewal plan.
- Certificates going out fast enough that you are not the reason a job
  waits.
- Specialist programs for commercial trade contractors, in all 50
  states and DC.
- Nothing about your current program changes unless you ask.
