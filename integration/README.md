# Lead capture

**Live: a Fillout embed, form id `fkqNiqeJmsus`**, on all three landing
pages and the copy of the general contractor page at the root. It writes
to Attio through Fillout's own integration. Nothing in this repo talks to
Attio.

The embed carries `data-fillout-inherit-parameters`, so Fillout reads the
query string of the page it sits on. That is what puts `gclid` and the
`utm_*` values on the submission. It only works for parameters that are
actually in the URL, which for paid traffic they will be.

`fillout-build-spec.md` holds the field lists, the brand values and the
checks to run before launch. `n8n-attio-lead-capture.json` is the unused
alternative, kept in case Fillout falls short.

## Conversion tracking, as fixed on Oct 1

- **Thank-you pages break out of the embed.** Fillout loads its redirect
  inside the embed, where the browser hides this site's cookies, so the
  Ads tag fired but could not find the saved click. Both confirmation
  pages now reload themselves as the full page and only start Tag Manager
  once they are top level. If a browser blocks that reload, the tags run
  in place as before.
- **Thank-you pages load Tag Manager once.** A second copy of the
  container sat above `</body>`; it is gone.
- **Click ids survive a return visit.** The landing page script saves
  `gclid`, `gbraid`, `wbraid` and the `utm_*` values in first-party
  cookies for 90 days and writes them back into the query string when a
  visitor returns without them, so `inherit-parameters` still carries
  them. A new click or new UTMs replace the saved set.
- **Fillout must have the hidden fields.** Each of those keys needs a
  hidden field of exactly that name in Fillout, mapped to Attio, or it
  arrives empty.

## Call tracking, as it stands

Every page shows one hardcoded number, the sales mainline
`(866) 811-1207` — eleven to thirteen times on a landing page, twice on a
confirmation page. There is no dynamic number insertion and no call
tracking vendor, so a call arriving on it cannot be told apart from an
organic, direct or offline call. This is not a reporting gap waiting to be
found in Google Ads: no data exists anywhere that would tie such a call to
a click.

What the account shows over the first eight days of spend, Sep 29 to
Oct 6, $919 and 59 clicks:

| Conversion action | State |
| --- | --- |
| `Calls from ads` | 0. The account-level call asset carries the raw mainline. ~350 impressions, zero clicks, and the call detail report is empty. |
| `Click to call` | 0. Enabled and counted as primary, but nothing on the pages was firing it. |
| `Qualified Lead` | 1, on Sep 29, from `3 - Broker & Risk Advisory`. The thank-you redirect does work. |
| `Below Floor Lead` | 0, and correctly excluded from bidding. |

So a caller who reaches the mainline did not come through the ad's call
button; nobody has tapped it. They either dialled from a page, or read the
number off a desktop ad and dialled by hand. Both are invisible until a
tracked number is in place.

### What the pages now send

Every live page pushes one Tag Manager event, `ifr_call_click`, when any
`tel:` link is tapped. It carries the page slug, which part of the page was
tapped, the link's label, and the `gclid`, `gbraid` or `wbraid` when the
visitor arrived on a Google click — from the query string, or from the
cookie the attribution script saved, so a return visitor still reports.

**Fire the `Click to call` conversion from exactly one trigger.** Tag
Manager's built-in link trigger fires `gtm.linkClick` and its all-elements
trigger fires `gtm.click`, so a tag bound to `ifr_call_click` can never
also be fired by either of them. That is the point of the custom name: a
second tag is the only way to double count. Before adding one, open
container `GTM-5PCQ4CBV` and check whether a tag already fires that
conversion on a link click. If one does, either repoint it at
`ifr_call_click` or leave it as it is — but do not run both.

A tap is intent, not a conversation. Keep it out of Smart Bidding until it
has been reconciled against calls the team actually took, or the campaign
learns to favour people who tap and hang up.

### Still missing

Real call attribution needs a number that differs per visitor:

- **Google forwarding numbers.** Free, built into Google Ads, and cover
  calls that follow an ad click. An organic caller stays invisible, so this
  does not answer "was that caller from a campaign" for all traffic.
- **A call tracking platform** (CallRail, WhatConverts). Serves a number
  pool with dynamic insertion, covers every source paid and organic,
  records the call, and can pass the click id into Attio for offline
  conversion import.

Until one is chosen, the only way to answer that question is to ask on the
call and log the answer against the date and the number dialled.

## Open questions on the current embed

- **One form across three pages.** The spec called for three, because the
  questions differ: trade and renewal month for a subcontractor, active
  projects and subcontractor count for a general contractor, role and
  monitored counterparties for compliance. The revenue floors differ too,
  under $5M against under $10M. A single form asks one set of questions on
  all three pages.
- **The source page arrives as `page`.** Each landing page writes a
  `page` parameter into its own query string before the embed script
  loads, so `inherit-parameters` carries it onto the submission. The value
  is the URL slug: `contractor-insurance`,
  `general-contractor-insurance`, `counterparty-compliance`, and `home`
  for the copy at the root. A `page` already in the URL is left alone, so
  an ad can override it. **Fillout needs a hidden field named exactly
  `page` to receive it.** A different name there means an empty value
  here, and the fix is to rename the field in Fillout.
- **The conversion signal.** Settled, and this note is kept only because
  it predates the fix. Fillout redirects to a confirmation page, and that
  page fires the conversion; the Oct 1 section above describes it. The
  account has recorded one `Qualified Lead` through this path, on Sep 29,
  so it works. One in eight days on 59 clicks is a volume problem, not a
  tracking one.

---

# Piping the landing page forms into Attio

The pages are static files on Render. They have no server, so they cannot
talk to Attio directly: an Attio API key in page JavaScript is readable by
anyone who views source. A middle layer is required. n8n is the one used
here.

    landing page form  ->  n8n webhook  ->  Attio API

## What the page already does

Each form posts JSON to whatever URL is set in `FORM_ENDPOINT`, near the
bottom of the page file. It ships empty, so the form tells the visitor to
call instead. Paste the n8n production webhook URL between the quotes and
the form starts delivering.

    var FORM_ENDPOINT = "";

The payload carries every visible field plus:

| Key | Why |
| --- | --- |
| `page` | which landing page the lead came from |
| `gclid`, `wbraid`, `gbraid` | the Google click id, for offline conversion import |
| `utm_*` | campaign, ad group and keyword attribution |
| `referrer` | where the visitor arrived from |
| `submitted_at` | UTC timestamp |

A hidden `website` field is a honeypot. Bots fill it, people never see it.
Submissions with it filled are dropped in the browser and never reach n8n.

## Field names by page

| Page | Fields |
| --- | --- |
| `contractor-insurance` | company, name, email, phone, trade, revenue, renewal |
| `general-contractor-insurance` | company, name, email, phone, revenue, projects, subs, renewal |
| `counterparty-compliance` | company, name, email, phone, role, projects, counterparties |

All three post to the same webhook. The `page` key tells them apart.

## Setting up n8n

1. Import `n8n-attio-lead-capture.json`.
2. Create a Header Auth credential: name `Authorization`, value
   `Bearer <your Attio API key>`. Attio issues keys under workspace
   settings, in the developers section. Attach it to both HTTP Request
   nodes.
3. On the Webhook node, set Allowed Origins to the staging and production
   domains. Without this the browser blocks the request as a CORS error
   and nothing reaches n8n. This is the most common failure.
4. Activate the workflow, copy the production webhook URL, and paste it
   into `FORM_ENDPOINT` on all three pages.

## Before it will work

The workflow writes the person and the company. It does not yet write
`trade`, `revenue`, `renewal`, `projects`, `subs`, `role` or
`counterparties`, because those attributes have to exist in Attio first.
Create them in Attio, note the exact slug each one is given, then add them
to the values object on the person or company node.

## Verify against current Attio documentation

These pages were built without network access to Attio, so the request
shapes below are from prior knowledge and were never run against the live
API. Check each one before going live:

- the assert endpoint is `PUT /v2/objects/{object}/records` with
  `?matching_attribute={slug}`
- the people matching attribute is `email_addresses`, and companies match
  on `domains`
- value shapes: `name` takes `full_name`, `email_addresses` takes
  `email_address`, `phone_numbers` takes `original_phone_number` with a
  `country_code`
- a record reference is `{ target_object, target_record_id }`

A single test submission through n8n will confirm or correct all of it.

## Closing the loop back to Google Ads

The `gclid` is captured for a reason. Once leads land in Attio, export the
gclid with the outcome and import it into Google Ads as an offline
conversion, so Smart Bidding optimizes toward leads that became clients
rather than toward form fills.

The wireframe's own note is worth honoring here: the revenue band is the
disqualifier, and below-floor submissions should record as a separate
conversion action and be excluded from bidding. The `qualified` flag the
workflow computes is there for that split. Keep its band list in step with
the form, which now differs between the subcontractor and general
contractor pages.
