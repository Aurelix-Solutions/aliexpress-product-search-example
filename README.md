# AliExpress Scraper: Python example

**The problem:** You want AliExpress prices, discounts and units sold for a search in a spreadsheet you can sort, without copying product pages by hand.

**The tool:** [AliExpress Scraper](https://apify.com/aurelix/aliexpress-product-search), an Apify Actor by Aurelix. This repository is a small, runnable Python example for it.

**The price:** $1.80 per 1,000 products on Apify's free plan and $1.26 on paid plans, plus $0.001 per run; you pay only for results. Apify's free plan includes $5 of credit a month.

**Try it** (needs an Apify API token from Console > Settings > API & Integrations):

```bash
pip install -r requirements.txt
export APIFY_TOKEN=your_token_here
python main.py
```

[![AliExpress Scraper: 75-second demo video](https://api.apify.com/v2/key-value-stores/twkCCtTWzsi8fVbx5/records/aliexpress-product-search-demo-thumb-v1.png)](https://api.apify.com/v2/key-value-stores/twkCCtTWzsi8fVbx5/records/aliexpress-product-search-demo-v1.mp4)

**[▶ Watch the 75-second demo](https://api.apify.com/v2/key-value-stores/twkCCtTWzsi8fVbx5/records/aliexpress-product-search-demo-v1.mp4)**

## What it does

AliExpress Scraper turns AliExpress search results into clean rows: product ID, title, link, images, price and original price, discount, deal badge, rating, units sold as a number, listing date, ship-from country and a sponsored flag. Type any keywords and sort by best match, most orders or price, up to 60 pages of 60 products per search. Schedule it with 'Only new products' to get just the listings you have not seen before.

## Why this over alternatives

Compared with the leading alternatives on the Apify Store (full table on the Store page):

- $1.80 per 1,000 products vs $2.49 and $9.00, with a $0.001 start fee instead of $0.05.
- Sponsored results flagged and an only-new-products mode. Not offered: variants, reviews and seller details.

## When to use it

- Dropshippers researching which products in a niche sell the most
- E-commerce sellers checking AliExpress prices and discounts before sourcing
- Product researchers watching a niche weekly for new listings
- Analysts comparing prices, ratings and units sold across search terms

## Input example

This is the `aliexpress-led-strip-lights` input used in `main.py`:

```json
{
  "queries": [
    "led strip lights"
  ],
  "sort": "orders",
  "maxPagesPerQuery": 2
}
```

Every input field is documented on the [Store page](https://apify.com/aurelix/aliexpress-product-search).

## How to run

1. Get an Apify API token from Apify Console (Settings, API & Integrations).
2. Install and run:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
export APIFY_TOKEN=your_token_here
python main.py
```

The script starts a run of `aurelix/aliexpress-product-search`, prints a few fields from the first results and writes every result to `results.csv`. Lists and nested objects are stored in the CSV as JSON text. Each run is billed to your Apify account (see Pricing below).

## Sample output

One record as returned by a real run (long text is shortened):

```json
{
  "query": "phone case",
  "page": 1,
  "position": 1,
  "product_id": "1005006995199586",
  "title": "New Armor Bumper Shockproof Phone Case For iPhone 15 11 12 13 Mini 14 Pro XR X Xs Max 7 8 Plus SE 2020 Silicone Hard Cover Capa",
  "product_url": "https://www.aliexpress.com/item/1005006995199586.html",
  "image_url": "https://ae-pic-a1.aliexpress-media.com/kf/Sd23fa488f7864890bdff9e610c8bbe50v.jpg",
  "images": [
    "https://ae-pic-a1.aliexpress-media.com/kf/Sd23fa488f7864890bdff9e610c8bbe50v.jpg",
    "https://ae-pic-a1.aliexpress-media.com/kf/S02a5c42b984e41e1beb5140c3445a4deU.png",
    "https://ae-pic-a1.aliexpress-media.com/kf/Seedb43be9ea547de8d8a16eda7bcf784H.png",
    "https://ae-pic-a1.aliexpress-media.com/kf/Seb4494b7f35c4d53bce61ef7caddc03ae.png",
    "https://ae-pic-a1.aliexpress-media.com/kf/S419fe5af6b5643d4a35bd2a45064d4bdU.png",
    "... [1 more]"
  ],
  "price": 0.33,
  "original_price": 2.71,
  "discount_pct": 88,
  "currency": "USD",
  "deal": "Dollar Express",
  "rating": 4.9,
  "sold_text": "10,000+ sold",
  "sold_count": 10000,
  "listed_date": "2024-05-16",
  "ship_from": "CN",
  "sponsored": false,
  "source_url": "https://www.aliexpress.com/w/wholesale-phone-case.html?SearchText=phone%20case",
  "scraped_at": "2026-11-04T11:24:32Z",
  "new_since_last_run": true
}
```

## Export to CSV

`main.py` writes `results.csv` with the `csv` module. You can also download any run's results directly:

```
https://api.apify.com/v2/datasets/<datasetId>/items?format=csv&clean=true
```

Send your token in an `Authorization: Bearer <token>` header (or `?token=`) if the dataset is not readable without one. Other formats: `format=json`, `format=xlsx`.

## Google Sheets

- Simplest: use Apify's Google Sheets integration on the Actor or a saved task, so each run's results land in a sheet with no token in the sheet itself.
- Quick pull of one run: `=IMPORTDATA("https://api.apify.com/v2/datasets/<datasetId>/items?format=csv&clean=true")`. This only works without a token if your account lets anyone with the dataset ID read it. Do not paste an API token into a sheet formula: anyone who can view or copy the sheet can read it and use your account.

## n8n

- Apify node: choose "Run an Actor", enter `aurelix/aliexpress-product-search`, paste the input above, then read the run's dataset items.
- HTTP Request node: `POST https://api.apify.com/v2/acts/aurelix~aliexpress-product-search/run-sync-get-dataset-items` with header `Authorization: Bearer <token>` (stored as an n8n credential) and the input as the JSON body. The response is the list of results. This endpoint waits for the run to finish, so for large runs start the run asynchronously and fetch the dataset when it ends.

## Limits

- Search results only: no product-page details such as full descriptions, variants or shipping cost.
- Up to 60 pages of 60 products per search (AliExpress' own limit).
- Prices are as shown to a logged-out new visitor and can include new-customer deals.
- Results vary between visits, so two runs of the same search are not identical.
- Only products whose title contains at least half the words of your search (or of AliExpress' English translation of a non-English search) are returned and charged. A search AliExpress has no match for returns 0 products and costs nothing beyond the start fee; broad category searches (e.g. 'kitchen gadgets') can miss products whose title names neither word.
- If AliExpress answers with a slider check, the Actor retries with a fresh connection; if it keeps refusing, the products already read are kept and the run status says 'Partial'.

## Pricing

At the time of writing:

Pay per event: you pay only for what the run returns, plus a small start fee. Bronze, Silver, Gold and higher Apify plans pay less per result.

| Event | Free plan | Paid plans (Bronze, Silver, Gold) |
|---|---|---|
| `product-result` | $1.80 per 1,000 products | $1.26 per 1,000 products |
| `apify-actor-start` | $0.001 per run | $0.001 per run |

Example: 1,000 products cost $1.80 on the free plan and $1.26 on a paid plan, plus the start fee. Apify's free plan includes $5 of credit a month, enough for about 2,700 products.

Products whose titles do not match your search are dropped and not charged; a search AliExpress has no match for costs nothing beyond the start fee.

Check the [Store page](https://apify.com/aurelix/aliexpress-product-search) for current prices.

## Links

- Actor: https://apify.com/aurelix/aliexpress-product-search
- Questions, bugs and feature requests: the Issues tab on the Store page.

## Licence

The example code in this repository is released under the MIT licence (see `LICENSE`). The Actor itself runs on Apify and is billed through your Apify account.
