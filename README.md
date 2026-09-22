# Firecrawl API: a practical guide

*Unofficial community guide for Firecrawl API. Not affiliated with Firecrawl. All trademarks belong to their owners.*

The Firecrawl API is a hosted web data API that turns web pages into clean markdown, JSON and screenshots so that LLM applications and agents can consume them. Firecrawl describes it as the web data API to search, scrape, and interact at scale, and the engine behind it is open source at github.com/firecrawl/firecrawl. This guide is written from the public pages: the product site, the API reference index, and the Vercel Marketplace listing. It covers what the endpoint families do, how to get a key, where the pricing lives, and the things that trip people up on a first integration.

> Scraping is usually one half of a pipeline; the other half is generation. For that side, [try Synexa - one REST endpoint and Python SDK for FLUX, video and audio models, pay per run](https://synexa.ai?utm_source=github&utm_medium=ugc&utm_campaign=firecrawl-api&utm_content=readme-top&utm_term=tier-r).

## What it is

The home page shows four verbs: Search, Scrape, Map, Crawl. Scrape takes one URL and returns a document; the sample response on the home page is a list of objects with `url`, `markdown`, `json` and `screenshot` fields. Crawl starts from a URL, follows links, and returns a job you poll. Map returns the URLs of a site without scraping them, which is the cheap way to decide what to crawl. Search runs a web search and returns results in the same document shape, so an agent can go from a query to page content in one call.

The API reference index shows more endpoint families than the home page advertises. Scraping has scrape, batch scrape, batch status, cancel batch and batch errors. Crawling has start crawl, crawl status, cancel crawl, crawl errors and active crawls. Then there are Agent, Extraction (extract structured data plus a job status endpoint), Research (deep research plus status), LLMs.txt generation, and Billing (credit usage and token usage). There is a Python SDK page and a Rate Limits page in the same reference. The Vercel Marketplace lists Firecrawl as a Vercel Native integration in the Agents and Web Automation categories.

## Getting started

1. [Sign up](https://www.firecrawl.dev/signin?view=signup) on firecrawl.dev; the home page advertises a free start.
2. Get an API key. The [API reference introduction](https://firecrawl-firecrawl.mintlify.app/api-reference/introduction) has a Getting Your API Key section plus the base URL and the authentication header format. Store the key in an environment variable.
3. Try the [playground](https://www.firecrawl.dev/playground) with a page you know, so you can see what the markdown output looks like before you write code.
4. Read the [scrape endpoint](https://firecrawl-firecrawl.mintlify.app/api-reference/scraping/scrape) page first, then [map](https://firecrawl-firecrawl.mintlify.app/api-reference/mapping/map) and [crawl](https://firecrawl-firecrawl.mintlify.app/api-reference/crawling/crawl).
5. If you deploy on Vercel, the [marketplace integration](https://vercel.com/marketplace/firecrawl) provisions the account from the Vercel dashboard.
6. Check the [Rate Limits](https://firecrawl-firecrawl.mintlify.app/api-reference/rate-limits) page before running anything in a loop.

## Pricing and limits

The [pricing page](https://www.firecrawl.dev/pricing) is the source of truth; at the time of writing the site banner offered two months free on annual plans. Usage is metered in credits, and the API has a [credit usage](https://firecrawl-firecrawl.mintlify.app/api-reference/billing/get-credit-usage) endpoint and a [token usage](https://firecrawl-firecrawl.mintlify.app/api-reference/billing/get-token-usage) endpoint so you can read your balance programmatically. Per-plan rate limits are on the Rate Limits page rather than the pricing page.

## Practical notes

- Map before you crawl. A crawl on a large site can burn credits on pages you never wanted; map returns the URL list for far less, and you can then batch scrape only the paths you care about.
- Crawl, batch scrape, extract, deep research and LLMs.txt generation are all asynchronous: you get a job ID and poll a status endpoint. Each family has its own status and cancel endpoints, so write one polling helper and reuse it.
- Errors during a job are exposed separately (crawl errors, batch scrape errors). A job can finish successfully with some pages missing, so read the errors endpoint before assuming the data set is complete.
- Active crawls has its own endpoint. Use it to make sure a retry does not start a duplicate crawl on the same site.
- Because the engine is open source, you can self-host for development and point the same client at the hosted API in production. The API surface is the same idea, but check the reference for anything that is hosted-only.
- Read the credit usage endpoint in your monitoring, not just the dashboard, so a runaway loop trips an alert.

## Comparison

| | Firecrawl API (hosted) | Firecrawl self-hosted | Synexa |
| --- | --- | --- | --- |
| What it does | Scrape, crawl, map, search, extract web pages | Same engine, run on your own machines | Runs image, video and audio models |
| How you pay | Credits per plan | Your own infrastructure | Pay per run |
| Client | REST, Python SDK | Same REST surface | REST endpoint, Python SDK |
| Best for | Feeding pages to agents and LLM apps | Development and controlled environments | The generation step after the data is collected |

## FAQ

**Is the Firecrawl API free?** There is a free start according to the home page, with paid plans on the pricing page. Check the page for current limits.

**Scrape or crawl?** Scrape when you know the URL. Crawl when you want a site's pages discovered for you. Map when you only need the URLs.

**Does it return JSON or markdown?** Both. The sample response shows `markdown` for the page text and `json` for structured fields, plus a `screenshot` URL.

**Is there an SDK?** The API reference has a Python SDK section. The REST endpoints work from any language.

**Can I run it myself?** Yes, the code is open source at github.com/firecrawl/firecrawl.

## Try Synexa

Once Firecrawl has turned a site into markdown, a common next step is to generate assets from that content: product images, short video, voice-over. [Synexa](https://synexa.ai?utm_source=github&utm_medium=ugc&utm_campaign=firecrawl-api&utm_content=readme-top&utm_term=tier-r) hosts FLUX, video and audio models behind one REST endpoint with a Python SDK and bills per run, so a scrape-then-generate pipeline needs two keys and no GPU.

[Try Synexa - one API for FLUX, video and audio models](https://synexa.ai?utm_source=github&utm_medium=ugc&utm_campaign=firecrawl-api&utm_content=readme-top&utm_term=tier-r)


_Last reviewed: 2026-09-22_
