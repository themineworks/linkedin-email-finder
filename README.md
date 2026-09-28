# LinkedIn Email Finder: Name + Company to Work Email

Turn a person's name and company into their most likely work email address. Generates the 8 standard email patterns for that domain and SMTP-confirms which one actually accepts mail before returning it. No API key. Pay only for emails delivered.

**Run it on Apify:** [apify.com/themineworks/linkedin-email-finder](https://apify.com/themineworks/linkedin-email-finder)
**Docs, FAQ and pricing:** [themineworks.com/actors/linkedin-email-finder](https://themineworks.com/actors/linkedin-email-finder/)

**Price:** From $1.50 per 1,000 emails on Apify's higher plans ($2.00 on the free plan), plus a $0.01 start fee per run. Failed and empty results are never charged.

## What it returns

* Generates all 8 standard email patterns for a domain
* SMTP-confirms the real pattern before returning it
* Takes just a name and company, or a name and domain
* No API key or LinkedIn login required
* Emails no pattern confirms are never charged

## Quick start

You need a free [Apify account](https://console.apify.com/sign-up) and its API token (Settings, API & Integrations).

### Python

```bash
pip install apify-client
```

```python
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_TOKEN")
run = client.actor("themineworks/linkedin-email-finder").call(run_input={
    "people": [
        {
            "fullName": "Patrick Collison",
            "domain": "stripe.com"
        },
        {
            "fullName": "Tobi Lutke",
            "companyName": "Shopify"
        }
    ]
})

for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item)
```

### Node.js

```bash
npm install apify-client
```

```javascript
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: 'YOUR_APIFY_TOKEN' });
const run = await client.actor('themineworks/linkedin-email-finder').call({
    "people": [
        {
            "fullName": "Patrick Collison",
            "domain": "stripe.com"
        },
        {
            "fullName": "Tobi Lutke",
            "companyName": "Shopify"
        }
    ]
});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items);
```

### cURL

One request that runs the actor and returns the results in the response (for runs under 5 minutes):

```bash
curl -X POST "https://api.apify.com/v2/acts/themineworks~linkedin-email-finder/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"people": [{"fullName": "Patrick Collison", "domain": "stripe.com"}, {"fullName": "Tobi Lutke", "companyName": "Shopify"}]}'
```

### Command line

This repo includes ready-made clients that save results to JSON and CSV:

```bash
python3 linkedin_email_finder.py --token YOUR_APIFY_TOKEN --people ","
node linkedin_email_finder.mjs --token YOUR_APIFY_TOKEN --people ","
```

## Input

| Field | Type | Default | Description |
|---|---|---|---|
| `people` (required) | array |  | List of people to find work emails for |
| `checkSmtp` | boolean | `true` | Open a connection to the company's mail server and test which generated pattern is actually accepted |

## Output

One row per result, as JSON, CSV, Excel or through the API.

| Field | Type | Description |
|---|---|---|
| `fullName` | string | The person's name, as given in the input |
| `domain` | string | The company domain used to generate candidate emails |
| `domainGuessed` | boolean | True if no explicit domain was given and this was derived from companyName as a best-effort guess |
| `email` | string | The most likely work email for this person |
| `pattern` | string | Which pattern the email matched: first, first.last, firstlast, firstl, flast, first_last, last.first, or last |
| `status` | string | confirmed (SMTP verified this exact mailbox), guessed (MX exists but the specific mailbox could not be… |
| `candidatesChecked` | integer | How many candidate patterns were SMTP-probed for this person |
| `scrapedAt` | string | ISO timestamp when this person was processed |

## Use it from an AI agent

The actor works as a tool in Claude, Cursor or any MCP client through Apify's MCP server:

```
https://mcp.apify.com/?tools=themineworks/linkedin-email-finder
```

## FAQ

### How does it find the email without scraping LinkedIn directly?

It works from a name and a company or domain you already have, generates the 8 email patterns most companies use (first.last@, flast@, first@, and so on), then SMTP-confirms which one is a live mailbox before returning it.

### What does SMTP-confirmed mean?

The actor connects to the domain's mail server and checks whether the generated address is accepted, without sending an email. Only a pattern the mail server accepts is returned.

### Is this a Hunter.io or Apollo alternative?

For pattern-based email finding, yes. It solves the same problem, guessing the pattern and confirming it is real, without a monthly seat.

### What happens if no pattern confirms?

You are not charged. If every generated pattern comes back rejected or unverifiable, the actor returns no result for that lookup and no billing event fires.

### What does it cost?

Pay per result: from $1.50 per 1,000 emails on Apify's higher plans, $2.00 on the free plan, plus a $0.01 start fee per run. Unconfirmed lookups are never charged.

### Can I export the results to CSV or Excel?

Yes. Every run saves to an Apify dataset you can download as JSON, CSV, Excel or XML, or read through the API. The Python and Node clients in this repo also write the results to local files.

### Can I run it on a schedule?

Yes. Save your input as a task on Apify and attach a schedule, or call the API from your own cron job. Scheduled runs are billed the same way as manual ones.

## Related scrapers

* [B2B Leads Finder](https://themineworks.com/actors/b2b-leads-finder/): Business emails and LinkedIn profiles for target companies
* [LinkedIn Company Scraper](https://themineworks.com/actors/linkedin-company-details/): Company size, industry, website, and followers without login
* [Zillow Rental Listings Scraper](https://themineworks.com/actors/zillow-rental-listings/): Scrape Zillow for-rent listings by city or zip. $1 per 1,000 results

Part of [The Mine Works](https://themineworks.com/): 151 pay-per-result scrapers with no login and no browser setup on your side.

## License

MIT © The Mine Works
