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

---

# How to build it, step by step

Written for someone who has not built a Fillout form before. Fillout
renames menu items from time to time, so if a label below does not
match what you see, look for the nearest equivalent rather than
assuming something is missing.

## Before you start

You need the Fillout login that owns form `fkqNiqeJmsus`, which is the
form running on the other four landing pages. Everything below starts
from that form rather than from a blank one, which saves the slowest
part of the job.

## Step 1 · Duplicate the live form, do not start from scratch

In Fillout, find the form `fkqNiqeJmsus` and **duplicate** it. Name the
copy something like `Builder's Risk`.

Duplicating carries over three things that are tedious to rebuild and
easy to get subtly wrong:

- the **Attio integration**, already mapped and working
- the **nine hidden fields**, already named correctly and already set
  to read from the URL
- the **branding**, so the new form looks like the others

Starting from a blank form means rebuilding all three by hand. Do not.

## Step 2 · Delete the questions that do not apply

The live form asks about trade, annual revenue and renewal month. None
of those belong on a Builder's Risk quote. Delete them.

Leave the contact questions alone: company name, full name, work email
and phone all stay.

**Do not touch anything in the hidden fields section.** That is the
part you duplicated the form for.

## Step 3 · Add the Builder's Risk questions

Add the questions from the Fields table above, in that order. Put the
project questions on **step one** and the contact questions on **step
two**.

That order matters for two reasons. Someone is more willing to answer
"what are you building" than "what is your phone number", so the easier
questions go first. And Fillout saves a partial submission once step
one is done, so a visitor who abandons at the contact step still leaves
you the project details.

## Step 4 · Make the three fields required, with the friendly messages

Open each of **Completed project value**, **Project size** and **Is the
project financed?** and turn on Required.

Then replace the default required message with the matching line from
the Friendly error messages table above. Fillout supports a custom
error message per question, confirmed 12 Nov, so use that rather than
the help text.

## Step 5 · The homeowner filter and where people land

Add the commercial-or-homeowner question as the **first question on
step one**, then set up two different endings.

Exact wording:

> **Is this a commercial or multifamily project?**
> - Yes
> - No, it's a home I own and live in

The second answer is the one that counts as a homeowner. Keep it that
explicit: "No" on its own gets picked by mistake by people doing
mixed-use or by an owner who does not think of themselves as
commercial.

Copy for the homeowner ending:

> Thanks for checking. InFrame places insurance for commercial and
> multifamily construction, so we are not the right broker for a home
> you own and live in.
>
> Your own homeowners insurer or a personal lines agent is the best
> first call. They can tell you whether your existing policy covers the
> work, or whether you need a separate course of construction policy.

**Commercial or multifamily:** on submit, redirect to
`https://go.inframerisk.com/thank-you`.

**My own home:** end the form on a short message pointing them to their
own homeowners insurer or a personal lines agent. **Do not redirect
them anywhere.**

This is the part worth getting right. The Google Ads conversion does
not fire when the form is submitted. It fires when someone loads
`/thank-you`. So a homeowner who never reaches that page never counts
as a conversion, which keeps Smart Bidding from learning to find more
homeowners. If you redirect everyone to the same place, the bidding is
trained on leads InFrame cannot write.

**Decided 12 Nov: one thank-you page.** Every completed commercial
submission goes to `/thank-you`, whatever the project value. Builder's
Risk does not use the `/thank-you-received` split the other pages use
for below-floor leads, because no value floor was ever agreed for it.

If a floor is agreed later, the split is two changes: a condition on
the value question routing that band to `/thank-you-received`, and a
secondary conversion action in Google Ads excluded from bidding.

This does not change the homeowner ending, which still redirects
nowhere at all.

## Step 6 · Check the hidden fields survived

Open the hidden fields and confirm all nine are still there, still
named exactly as in the Hidden fields table, and still set to read from
a URL parameter:

    gclid   wbraid   gbraid
    utm_source   utm_medium   utm_campaign   utm_term   utm_content
    page

Names are case sensitive and must match exactly. A field called
`GCLID` or `utm-source` arrives empty, and nothing on the page will
warn you.

## Step 7 · Connect it to the page

Fillout gives each form an id, the short code in its share URL and in
its embed snippet. Copy that id.

Open `public/builders-risk/index.html`, scroll to the bottom, and find:

    var FILLOUT_FORM_ID = "PASTE_BUILDERS_RISK_FORM_ID_HERE";

Replace the placeholder with the id, keeping the quotes. That is the
only edit the page needs. The setup notice removes itself and the embed
appears in its place.

## Step 8 · Test before any ad traffic reaches it

Four tests, in this order:

1. **A normal commercial submission.** Confirm it reaches Attio and
   that it lands on `/thank-you`.
2. **An attribution test.** Load the page with a fake click id on the
   end of the URL:

       /builders-risk/?gclid=TEST123&utm_source=google&utm_medium=cpc

   Submit, then check the record in Attio carries `gclid` as `TEST123`,
   the two UTM values, and `page` as `builders-risk`.
3. **A homeowner submission.** Confirm it ends on the message and never
   reaches `/thank-you`.
4. **A required-field test.** Leave each of the three blank in turn and
   confirm you see your sentence, not Fillout's default.

Test 2 is the one people skip and the one that quietly costs money. If
`gclid` does not arrive, you cannot import offline conversions back
into Google Ads later, and you will not find out for months.

## One thing to add downstream

`page` arrives as `builders-risk`, which is a value Attio has never
seen. Anything filtering on the existing slugs, which are
`contractor-insurance`, `general-contractor-insurance`,
`counterparty-compliance` and `home`, needs `builders-risk` adding, and
`broker` and `trade` as well if that has not been done since those two
pages went live.
