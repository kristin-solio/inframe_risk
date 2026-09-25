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

## Open questions on the current embed

- **One form across three pages.** The spec called for three, because the
  questions differ: trade and renewal month for a subcontractor, active
  projects and subcontractor count for a general contractor, role and
  monitored counterparties for compliance. The revenue floors differ too,
  under $5M against under $10M. A single form asks one set of questions on
  all three pages.
- **Nothing identifies the source page.** With one form and no `page`
  parameter in any landing page URL, a submission does not say which page
  produced it. Either add a `page` field in Fillout populated from the
  parent URL, or give each page its own form.
- **The conversion signal.** Confirm how Fillout reports a completed
  submission to the page, and fire the Google Ads conversion on it. There
  is no conversion tag on any page yet.

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
