# Building the three forms in Fillout

Decision: Fillout, with its native Attio integration, replacing the
hand-built forms on the pages.

Build **three separate forms**. The fields genuinely differ per page, so
one shared form would either ask the wrong questions or need conditional
logic for no benefit.

The native forms stay live on staging until the Fillout embeds are ready.
Nothing breaks while you build.

---

## Form 1 of 3 · Specialty Trade Contractor

Page: `/contractor-insurance` · Submit button: **Get a free insurance review**

| Label | Type | Required | Options |
| --- | --- | --- | --- |
| Company name | Short text | Yes | |
| Full name | Short text | Yes | |
| Work email | Email | Yes | |
| Phone | Phone | Yes | |
| Trade | Dropdown | Yes | Electrical / HVAC & Mechanical / Plumbing / Concrete & Masonry / Drywall / Painting / Flooring |
| Annual revenue | Dropdown | Yes | Under $5M / $5M to $15M / $15M to $50M / Over $50M |
| Month your policies renew | Dropdown | No | January through December / Not sure |

## Form 2 of 3 · General Contractor

Page: `/general-contractor-insurance` · Submit button: **Get a free insurance review**

| Label | Type | Required | Options |
| --- | --- | --- | --- |
| Company name | Short text | Yes | |
| Full name | Short text | Yes | |
| Work email | Email | Yes | |
| Phone | Phone | Yes | |
| Annual revenue | Dropdown | Yes | Under $10M / $10M to $25M / $25M to $75M / $75M to $150M / Over $150M |
| Active projects | Dropdown | Yes | 1 to 5 / 6 to 15 / 16 to 40 / More than 40 |
| Subcontractors on your projects | Dropdown | No | Under 25 / 25 to 100 / 100 to 300 / More than 300 |
| Month your policies renew | Dropdown | No | January through December / Not sure |

## Form 3 of 3 · Counterparty Compliance

Page: `/counterparty-compliance` · Submit button: **Check one project for gaps**

| Label | Type | Required | Options |
| --- | --- | --- | --- |
| Company name | Short text | Yes | |
| Full name | Short text | Yes | |
| Work email | Email | Yes | |
| Phone | Phone | Yes | |
| Your role | Dropdown | Yes | Risk manager / Controller / Operations lead / Owner or developer / Property manager / Other |
| Subcontractors, tenants or borrowers monitored | Dropdown | Yes | Under 50 / 50 to 250 / 250 to 500 / Over 500 |
| Active projects | Dropdown | No | Under 10 / 10 to 25 / 25 to 50 / Over 50 |

Labels are approved copy. Keep them word for word, including the comma
series in "Subcontractors, tenants or borrowers monitored".

---

## Hidden fields, on all three forms

These carry the paid click through to the CRM. Without them you cannot
tell which campaign, ad group or keyword produced a client, and you cannot
import offline conversions back into Google Ads later. Set each to read
from the matching URL parameter.

| Field | Source |
| --- | --- |
| `gclid` | URL parameter |
| `wbraid` | URL parameter |
| `gbraid` | URL parameter |
| `utm_source` | URL parameter |
| `utm_medium` | URL parameter |
| `utm_campaign` | URL parameter |
| `utm_term` | URL parameter |
| `utm_content` | URL parameter |
| `page` | Fixed value: the page slug |

`wbraid` and `gbraid` replace `gclid` on iOS traffic. Skipping them loses
a chunk of mobile attribution silently.

---

## Brand values for the Fillout designer

Exact values from the brand book. No gradients, no drop shadows, no
outlined buttons.

| Element | Value |
| --- | --- |
| Font | Manrope (Google Fonts), weights 300/400/600/700 |
| Button fill | `#EFB921` |
| Button label | `#222F3E`, weight 700, 16px |
| Button radius | 2px |
| Field background | `#F4F6FB` |
| Field border | 1px `#C9CFE1`, radius 2px |
| Field text | `#222F3E`, weight 400, 16px |
| Field label | `#526477`, weight 600, 12px, uppercase, letter-spacing .12em |
| Form background | `#FFFFFF` |
| Focus ring | 3px `#EFB921`, offset 3px |

---

## Four things to verify in Fillout before launch

1. **Inline embed, not iframe.** An iframe brings height jumps, worse
   mobile keyboard behaviour, and makes the hidden fields harder to
   populate. If iframe is the only option, say so and we keep the native
   forms.
2. **Does the Attio integration upsert or only create?** If it only
   creates, every repeat submission makes a duplicate person in Attio.
   Test it: submit the same email twice and look at Attio.
3. **Do hidden fields actually populate?** Load a page with
   `?gclid=TEST123&utm_source=google` and check the value lands on the
   submission.
4. **Custom attributes exist in Attio first.** Trade, revenue, renewal,
   projects, subs, role and counterparties have to be created as Attio
   attributes before anything can map to them. Mapping to a field that
   does not exist fails.

---

## The one thing moving to Fillout takes away

With the native form, the moment of success happens in our own code, so a
Google Ads conversion tag can fire exactly then. With Fillout, that moment
happens inside their component.

Before spending on these pages, confirm how Fillout signals a completed
submission to the page, and wire the Ads conversion to it. Without that
there is no conversion signal, Smart Bidding has nothing to learn from,
and the campaign optimizes blind.

Also worth honoring the wireframe's note: the revenue band is the
disqualifier. Below-floor submissions should record as a separate
conversion action and be excluded from bidding, or Google optimizes toward
exactly the leads you do not want. The floor differs by page now: under
$5M on the subcontractor form, under $10M on the general contractor form.
