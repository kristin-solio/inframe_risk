# Quality Score landing page mockups

Two new pages on the `lp-mockups-qs` branch, built to lift the Below
average **landing page experience** rating Google reports on every
non-brand keyword. Ad relevance is already Above average or Average, so
nothing here touches the ads.

| Path | Audience | Swap parameter |
| --- | --- | --- |
| `/broker` | Broker and risk program searchers | `?kw=` |
| `/trade` | Specialty trade searchers | `?trade=` |

**Both pages are on staging as of 19 Nov.** Staging tracks
`claude/inframe-risk-landing-pages-z1j9sg`, which now carries them, and
answers at `https://inframe-risk-contractor-insurance.onrender.com`.
Production tracks `main` and has neither page, so
`go.inframerisk.com` is unchanged.

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

Every `tel:+18668111207` href is unchanged, so the click-to-call trigger
configured inside the GTM container keeps matching. Two link texts did
change on 18 Nov; see the amendment below.

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

## The comparison section on /trade

Added 19 Nov on branch `claude/blissful-gauss-b4otvx`, from an approved
design supplied as desktop and mobile exports. It sits directly after
the hero, above "You carry more risk than the people who hired you." It
is on `/trade` only, so all seven trades carry it and no other page
does.

One component, not two. Above 768px it is a three-column table: the row
label, a typical broker on grey, InFrame on amber tint under a navy
header carrying the white logo. Below 768px it becomes a check table,
with the benefit and a subline in the first column and two narrow icon
columns. Each breakpoint words the rows differently, so both strings sit
in the markup and the media query chooses between them.

It is a real `<table>`. The rows use `display:grid` to hold the design's
column widths, which would otherwise drop the table semantics, so every
element carries its ARIA role explicitly. Checked at both breakpoints:
the rowheaders, columnheaders and cells survive, and each icon reads as
"Included" or "Typically not included". The table has a visually hidden
caption.

Both buttons point at `#request`, the hero form, and carry
`data-cta="cta_click"` with `data-cta-id="get_quote"`. **The locations
`comparison` and `comparison_band` are new and nothing in GTM reads them
yet.**

Two deliberate departures from the brand book. The section heading takes
`--t-headline` rather than `--t-subline`, because that is what produces
the design's 56px, so this h2 is larger than every other h2 on the page.
The lede sits between Body and Subline at 17 to 21px, a sixth size the
five-role scale does not have.

The headline wraps to two lines on desktop. The design frame allows
1216px of content and the page shell allows 1160px, so "More than a
traditional broker." will not fit on one line at 56px. Left as is.

## Decisions from the 5 Nov call

**CTA structure.** Every call to action is the same pair in the same
order: **Get a Quote** first, **Call an Advisor** second. That holds in
the masthead, the hero, every band, the closing band and the sticky
mobile bar. No secondary carries the phone number any more; the number
lives in the footer, and every "Call an Advisor" is still a tel: link.

> **Amended 18 Nov, branch `phone-cta-hero-mobile`.** Calls were 70 per
> cent of October conversions and fell to almost nothing once the number
> left the hero, so the hero and the sticky mobile bar now carry it
> again. The hero keeps **Get a Quote** first and adds **Call (866)
> 811-1207** as the Slate 03 secondary, with the line "Prefer to talk?
> Call a licensed advisor." underneath. The sticky bar puts the phone
> first, as **Call now** with a phone icon, and **Get a Quote** second.
> The masthead, the bands and the closing band are unchanged and still
> read "Call an Advisor". Every phone link is still a plain
> `<a href="tel:+18668111207">`.

> **Amended 19 Nov, branch `claude/blissful-gauss-b4otvx`.** The six
> buttons on `/broker` now read **Get a free insurance review** in place
> of **Get a Quote**: the hero, the three band buttons, the closing band
> and the sticky mobile bar. The hero form card heading still reads "Get
> a Quote", the masthead still reads "Call an Advisor", and every
> `data-cta-id` is still `get_quote`, so the GTM triggers keep matching.
> `/trade` and the four original pages are untouched and still lead with
> **Get a Quote**, so the single CTA standard above no longer describes
> the whole set. The longer label wraps to two lines in the sticky mobile
> bar, taking it to 87px tall, or 105px at 320px wide. Check that against
> the call volume the 18 Nov amendment was protecting before this goes to
> `main`.

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

> **Amended 19 Nov, branch `claude/blissful-gauss-b4otvx`.** The
> comparison section on `/trade` carries copy that has not been through
> this register, so the paragraph above no longer holds for that page.
>
> It was written to be more defensible than the design it came from, not
> to be exempt from review. The left column no longer states as fact what
> other brokers do and reads as a tendency instead: "often not reviewed",
> "can sit in a queue", "often renews as-is", "can arrive as a surprise".
> The InFrame column describes the work rather than promising an outcome,
> so "issued fast, the moment a GC asks" became "treated as urgent when a
> GC asks", and "one secure home, found in minutes" became "kept together
> in one place". The band headline claimed the broker "owns your
> outcome", which a broker cannot do, since carriers decide coverage and
> claims, and now reads "stays with your program".
>
> The row that read "Your subs" was addressed to a general contractor,
> the wrong party on a page written for specialty trade contractors
> working under someone else's contract. It is now "Your general
> contractor", about the requirements a GC writes into the contract.
>
> **"150+ insurance markets" was removed.** It appears nowhere else on
> the site and is not in this register. The band carries the approved
> 120-day renewal milestone in its place. Restore the figure if it can be
> substantiated.
>
> A qualifying line under the table notes that the descriptions are
> general, that brokers and programs vary, and that coverage is subject
> to the policy issued, echoing the disclaimer already in the footer.
>
> Still to clear: every row of the table, the band headline, the eyebrow
> "For specialty contractors", and "More than a traditional broker." as a
> comparative claim.

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
