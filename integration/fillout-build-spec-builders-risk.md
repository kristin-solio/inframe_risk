# Building the Builder's Risk form in Fillout

The Builder's Risk page at `public/builders-risk/index.html` asks
different questions from the other landing pages, so it needs **its own
Fillout form**, not the shared one (`fkqNiqeJmsus`). The shared form has
no asset value, project size or financing question, and those three are
required here.

Until this form exists, the page shows a setup notice in place of the
embed. Build the form, then paste its id into `FILLOUT_FORM_ID` near the
foot of the page file. Nothing else on the page needs editing.

## Fields

| Label | Type | Required | Options |
| --- | --- | --- | --- |
| Project type | Dropdown | Yes | Ground-up commercial / Multifamily / Commercial renovation or retrofit / Something else |
| Completed project value | Dropdown | **Yes** | Under $1M / $1M to $5M / $5M to $20M / $20M to $75M / Over $75M |
| Project size | Dropdown | **Yes** | Under 10,000 sq ft / 10,000 to 50,000 sq ft / 50,000 to 200,000 sq ft / Over 200,000 sq ft / Not sure yet |
| Is the project financed? | Dropdown | **Yes** | Yes, construction loan in place / Yes, loan in progress / No financing / Not sure |
| Project state | Dropdown | Yes | All 50 states and DC |
| Construction start date | Date | No | |
| Company name | Short text | Yes | |
| Full name | Short text | Yes | |
| Work email | Email | Yes | |
| Phone | Phone | Yes | |

The three in bold are the ones the 5 Nov call asked to be required.
They are the three answers that decide which carriers can look at the
project at all, which is why the page cannot usefully route a lead
without them.

## Friendly error messages

Set these as the required-field messages rather than leaving Fillout's
default "This field is required". Each says why the answer matters, so
the message reads as help instead of a scold.

| Field | Message |
| --- | --- |
| Completed project value | We need the completed value to know which carriers can look at this. A rough number is fine. |
| Project size | Roughly how big is the project? An estimate is fine, you can refine it later. |
| Is the project financed? | Let us know whether there is a construction loan. It changes what the policy has to include. |

## The homeowner filter

The page says twice that InFrame places insurance for commercial and
multifamily projects only, and not for individual homeowners. Add a
first question that lets a residential visitor out politely:

> **Is this a commercial or multifamily project?**
> Yes / No, it is my own home

On **No**, end the form with a short message pointing them to their own
homeowners insurer or a personal lines agent, and **do not redirect to
the thank-you page.**

That last part matters for conversion tracking. The Google Ads
conversion fires on the `/thank-you` page view, so a homeowner who never
reaches it never counts as a conversion, and the bidding is not trained
on leads InFrame cannot write.

## Hidden fields

The same set as the other forms. Each needs a hidden field of exactly
this name, mapped to Attio, or it arrives empty.

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
| `page` | URL parameter, arrives as `builders-risk` |

The page's attribution script writes `page` and restores saved click ids
into the query string before the embed loads, and the embed carries
`data-fillout-inherit-parameters`, so all of the above are read from the
URL. `page` arrives as **`builders-risk`**, which is a new value: add it
anywhere downstream that filters on the existing page slugs.

## Before it goes live

1. Build the form and paste its id into `FILLOUT_FORM_ID`. The setup
   notice disappears on its own once a real id is set.
2. Submit one test lead and confirm `gclid` and the `utm_*` values land
   in Attio, and that `page` reads `builders-risk`.
3. Submit one test as a homeowner and confirm it ends without reaching
   `/thank-you`.
