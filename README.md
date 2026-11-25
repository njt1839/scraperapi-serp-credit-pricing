# SERP API Pricing on ScraperAPI: What a Google Search Costs in Credits, How Many Searches Your Plan Really Buys, and Which Tier Fits (Full Plan Table)

A Google search through ScraperAPI costs **25 credits**. A standard web page costs 1. That single multiplier answers most of the questions people bring to a `serp api` search: how far the free allowance stretches (1,000 credits buys **40 Google searches**), how big a paid tier needs to be, and whether a SERP-heavy workload belongs here at all. Credits also reset to zero at each renewal rather than rolling over, so unused budget doesn't accumulate for a big crawl later.

What follows is the credit table from ScraperAPI's own documentation, every plan the lineup currently shows, what the structured Google endpoint returns, and how the per-search math compares with Bright Data and SerpApi.

> **Quick pick:** work out your monthly Google query count, multiply by 25, and buy the smallest tier whose credit allowance covers that number. Use the free 1,000 credits first to confirm the parsing and geo-targeting behave on your queries (5 concurrent connections), then size up. 👉 [See current ScraperAPI plans and credit allowances](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## Every ScraperAPI plan, and what happens when credits run out

ScraperAPI sizes its tiers in credits, and the per-tier dollar price and credit allowance live on the pricing page. The column that matters more for a SERP workload is the one nobody puts in a feature list: what the account does at the moment credits hit zero. On Hobby, Startup, and Business, the answer is a 429 response and a pause until renewal, or an opt-in auto-upgrade to the next tier. Pay-as-you-go only starts at Scaling.

| Plan | What happens when credits run out | Start here |
| --- | --- | --- |
| Free | 1,000 credits, 5 concurrent connections; requests are turned away once you're over | [Open a free ScraperAPI account](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Hobby | Auto-upgrade to the next tier if you opt in; no pay-as-you-go | [Check the Hobby tier](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Startup | Auto-upgrade if opted in; no pay-as-you-go | [Check the Startup tier](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Business | Auto-upgrade if opted in; no pay-as-you-go | [Check the Business tier](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Scaling | Auto-upgrade, or pay-as-you-go at a fixed per-credit rate up to a cap you set | [Check the Scaling tier](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Professional | Auto-upgrade, or pay-as-you-go with a monthly cap | [Check the Professional tier](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Advanced | Pay-as-you-go only; auto-upgrade isn't offered at this level | [Check the Advanced tier](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Enterprise | Custom terms, with pay-as-you-go available | [Ask about Enterprise pricing](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

Annual billing is exactly 10% below the monthly rate, and the multiplier doesn't change when you switch plans: a Google request costs 25 credits on Hobby and on Enterprise alike. Upgrades and downgrades can be made from the billing dashboard at any time.

## The credit table: Google and Bing cost 25x a normal page

ScraperAPI prices each request by target domain, the parameters you send, and whether anti-bot bypass had to fire. Only one row of that table matters for SERP work.

| Target | Credits per request | Google searches per 1,000 credits |
| --- | --- | --- |
| Standard page | 1 | n/a |
| Amazon | 5 | n/a |
| Google or Bing, including subdomains | 25 | 40 |
| LinkedIn | 30 | n/a |
| Cloudflare, DataDome, or PerimeterX bypass | +10 on top of the base cost | n/a |

Two things follow from that table. First, any plan's advertised credit allowance converts to Google searches by dividing by 25, so a tier that sounds generous as a general scraping plan can look thin as a rank-tracking plan. Second, 10,000 Google queries a month means 250,000 credits, not 10,000. Plan the budget off the second number.

The +10 bypass surcharge applies when ScraperAPI activates bypass logic on Cloudflare, DataDome, or PerimeterX, and it's added automatically rather than on request. Google's own blocking isn't one of those three named systems, so don't stack the surcharge onto the 25-credit rate without checking your specific URL first. The Domain Cost Estimator in the dashboard is the authoritative per-URL price, and the `max_cost` parameter lets you cap what any single scrape is allowed to spend.

One gap worth flagging: ScraperAPI advertises automatic retries on all plans, and its pricing FAQ doesn't state whether a retried request is billed again. SerpApi, by contrast, says plainly that cached, errored, and failed searches aren't counted. If your budget depends on the answer, run a small batch and check the credit counter before committing to a large tier. 👉 [Compare plans before you commit to a credit volume](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## What the Google SERP endpoint returns

ScraperAPI's structured Google endpoint is asynchronous by design: you post a job, then collect the result or receive it at a webhook you supply. That suits batch rank tracking better than a synchronous call-per-keyword loop, and it means a slow query doesn't hold a connection open.

The request accepts a single `query` or a `queries` array, so several keywords can ride in one job. Localization comes from four parameters: `tld` for the Google domain (the docs list 20+ options including `.com`, `.co.uk`, `.ca`, `.de`, `.fr`, `.co.jp`, `.in`, `.com.br`, `.com.au`, and `.se`), `country_code` for two-letter geo-targeting, `hl` for host language, and `gl` to boost results from a given country. Where you need `google.com` served from another country, both `tld` and `country_code` must be set. `start` handles pagination. Output is JSON by default, with CSV available for the structured methods.

The geo controls are country-level. If your use case needs city-level precision, that's a documented strength of Bright Data's SERP API rather than something ScraperAPI's SERP docs promise, and it's worth deciding early whether national-level localization is enough for your rankings.

## How it stacks up against Bright Data and SerpApi

The billing units are different enough that headline plan sizes can't be compared directly. ScraperAPI counts Google searches at 25 credits each. SerpApi counts one search as one credit and doesn't charge for cached or failed ones. Bright Data bills per successful request. SerpApi's 5,000-search plan buys 5,000 Google searches; the same volume on ScraperAPI needs 125,000 credits.

|  | ScraperAPI | Bright Data | SerpApi |
| --- | --- | --- | --- |
| Billing unit | Credits; Google and Bing at 25 each | Per successful request | One credit per successful search |
| Failures and empty results | Not spelled out in the pricing FAQ | "Pay only for successful delivery" | Not counted; an empty result set still costs 1 credit |
| Engines priced for search | Google and Bing | Google, Bing, DuckDuckGo, Yandex, Baidu, Yahoo, Naver | Google and other engines |
| Output formats | JSON, CSV for structured endpoints | JSON, HTML, Markdown, trimmed `parsed_light` | JSON, Markdown |
| Async and batching | Async jobs, multi-query arrays, webhooks | Sync by default, async for volume, multi-query | Each request runs immediately |
| Published speed claim | JS rendering and automatic retries; no SERP latency figure | Under 1 second with premium routing | Real time, full browser per request |
| Refund policy | Not published in the pricing FAQ | Not published in the material reviewed | 7 days, unless more than 20% of searches are used |
| Legal cover | None advertised | None advertised | U.S. Legal Shield up to $2M, Production plan and above |

ScraperAPI's advantage sits in the structured async workflow and the breadth of its general scraping platform, which handles ordinary pages, Amazon, and bot-protected sites under the same key. SerpApi's advantage is that a search is a search: no 25x mental arithmetic, a documented refund window, and a legal shield on the higher tiers if collecting search data carries real risk for you. Bright Data's is engine coverage and the sub-second claim, gated behind premium routing you have to request. All three offer throughput caps rather than unlimited concurrency, so check the hourly ceiling on whichever you pick.

## Who doesn't need a SERP API

A DIY Google scraper breaks in five predictable ways: CAPTCHAs that separate humans from bots, IP blocks for suspicious activity, rate limiting that cuts unidentified request patterns, geo-targeting that changes results by location, language and device, and honeypot traps built from hidden elements that only automated clients touch. Those are the standard anti-bot mechanisms any scraper meets; they aren't results from one vendor's testing.

If you need a few hundred queries once, to settle a keyword question before a launch, a local script plus patience is cheaper than any subscription. A SERP API earns its cost when the same queries have to run again next week, from a specific country, without you watching for blocks, or when an AI agent needs search results mid-workflow without a human in the loop.

## Before you check out

Five things worth confirming on the live page rather than assuming:

1. **The credit allowance on your target tier.** Divide by 25 before comparing it to your query volume.
2. **Whether auto-upgrade is on.** It's opt-in on Hobby through Professional, and it will move you to the next tier when credits run out. Leave it off if you'd rather hit a 429 than a bigger invoice.
3. **Your concurrency ceiling.** Going over returns a 429 and the request is turned away rather than queued, with no extra charge.
4. **Rollover.** Unused credits are lost at renewal. Buy for your steady-state volume and let pay-as-you-go absorb spikes if your tier has it.
5. **The free tier's real size.** 1,000 credits is 40 Google searches, which is enough to test parsing, TLD targeting, and your webhook, not enough to benchmark a production workload.

ScraperAPI's pricing FAQ doesn't publish a refund window. That makes the free credits the low-risk way to test, and it means a monthly first cycle is the safer commitment than an annual one until you've seen your actual credit burn. 👉 [Start with the free credits and check current pricing](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## FAQ

**Does ScraperAPI's SERP support go beyond Google?**
Google and Bing are the two engines priced in the credit table at 25 credits per request, both including subdomains. The structured search endpoint documented is Google-specific, with TLD, country, language, and pagination controls.

**Can I get Google results as JSON?**
Yes. JSON is the default for the structured methods, and CSV is available as an alternative.

**How many Google searches does the free plan include?**
40. The free tier gives 1,000 credits with a maximum of 5 concurrent connections, and Google requests consume 25 credits each.

**Can I send several keywords in one request?**
Yes, the Google SERP endpoint accepts a `queries` array alongside the single-`query` form, with a webhook callback for delivery.

**What happens when I run out of credits mid-month?**
Scraping pauses until renewal unless you've enabled auto-upgrade (Hobby through Professional) or have pay-as-you-go (Scaling and above, with a monthly cap you set). Hitting your concurrency limit returns a 429 rather than an overage charge.

**Is geo-targeting city-level?**
The documented controls are `tld`, `country_code`, `hl`, and `gl`, which give country-level localization. City-level targeting isn't part of ScraperAPI's SERP documentation.

## Which tier actually fits

Run the multiplication first, then match: under roughly 2,000 Google queries a month, take the smallest paid tier and keep auto-upgrade off so a runaway loop can't silently double your bill. A few thousand queries a month with retries and JS rendering, pick a mid tier with auto-upgrade on and watch the credit counter for the first cycle. Past roughly 50,000 Google queries a month, 25 credits per search becomes 1.25 million credits, and that's the point to compare against a provider that charges one credit per search before locking into an annual contract.

If you need Maps, Trends, Hotels, or Flights data, or city-level geo, or a legal shield covering search-data collection, ScraperAPI's documented SERP offering isn't built around those, and paying for credits you can't spend the way you need is the expensive mistake here.

For straightforward Google and Bing result collection with async jobs and JSON output, the credit math is simple enough to plan around. 👉 [Pick your ScraperAPI tier and confirm the current credit allowance](https://www.scraperapi.com/pricing/?fp_ref=coupons)
