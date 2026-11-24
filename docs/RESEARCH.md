# Research findings (condensed)

Source: Perplexity research report pasted by Brian on 2026-11-23 (cutoff 2026-11-23), plus a short earlier agent report. NOT independently verified by Claude. `[unverified]` = could not be established, not proven false. Re-verify anything load-bearing from the official page before building on it.

## 1. Price benchmarks
- Going's published general "good deal" ranges (round trip): East/SE Asia $700s-$900s; Europe $400s-$500s; Hawaii $200s-$500s. Source: going.com/guides/how-to-find-cheap-flights (updated 2026-11-19). Not West-Coast-specific.
- Going also reports sub-$500 West Coast to Asia fares (e.g. LAX-Tokyo $425) and calls ~$550 LAX-Japan "underpaying". Source: going.com/guides/flights-to-asia-expensive-right-now (2026-01-15). An exact Going "Asia $500 or less" page was NOT found. A different publisher (What Do You Sea, 2018) says "Asia $500 or below".
- KAYAK monthly **median** round-trip, SFO (12-month rolling, top 0.1% removed; cabin filter unverified; NOT deal prices). Lowest month:

| Route | Lowest month median |
|---|---|
| NYC | Jan $317 |
| Honolulu | Jan $382 |
| Mexico City | Jan $456 |
| Cancun | Jan $533 |
| London | Feb $656 |
| Paris | Feb $670 |
| Rome | Feb $635 |
| Tokyo | Jan $852 |
| Seoul | Feb $698 |
| Ho Chi Minh City | Mar $758 |
| Bangkok | Feb $783 |
| Bogota | Feb $455 |
| Lima | Feb $637 |
| Sydney | Mar $1,091 |

  SJC/OAK: only Honolulu verified (SJC low Apr $384, OAK low Jan $388). Other SJC/OAK routes `[unverified]`. Do NOT scrape KAYAK; these are one-time manual references.
- Full cheap/normal/expensive by season for every route: `[unverified]`.

## 2. Flight data APIs (all for a new individual developer, late 2026)
- **Amadeus Self-Service:** reported shut down 2026-07-17 (phocuswire, 2026-02-09; first-party notice not retrieved). Treat as unavailable.
- **Skyscanner:** commercial partners only (100k+ monthly users). Live Pricing explicitly bans unattended requests. Not usable.
- **Kiwi Tequila:** invitation-only partnerships. Not usable.
- **Duffel:** sandbox prices are not real. Live access for an individual `[unverified]`. Live search limit 10 requests/60s; extra searches $0.005 each (not free monitoring). Calls must go through own backend. Retention and unattended monitoring rights `[unverified]`.
- **Travelpayouts / Aviasales Data API:** registration with token. Prices are **cached** user-search data (about 2-7 days old), not guaranteed live. Limits per method (e.g. 300/min calendar and cheap, 60/min monthly). Meant for informational pages; docs recommend caching locally 24h. Best candidate so far for cheap-price history, but current acceptance `[unverified]`.
- **Aviasales live Search API:** separate; eligibility and limits conflicting (100 vs 200/hour); automatic collection of booking links prohibited. Not a fit.
- **FlightAPI:** advertised free trial (20 calls vs 30 credits conflicting), not recurring. Terms `[unverified]`.
- **Conclusion:** no verified free, real-time, unattended-monitoring fare API. The Phase 2 fallback applies: feed-based detection, Travelpayouts cached data if accepted, and a documented path to a paid API later.

## 3. Deal feeds
- **r/flightdeals:** Reddit reportedly shut RSS on 2026-11-13 and stopped new public API applications 2026-10-31 (secondary source: Mashable, 2026-09-30). Treat as unreliable.
- **Fly4Free:** RSS listed at fly4free.com/feed/ (via directories); direct retrieval failed; current operation `[unverified]`. About 15 posts/day.
- **The Flight Deal:** RSS historically listed (FeedBurner); current endpoint `[unverified]`. Site says "All Rights Reserved".
- **Secret Flying, Going:** no verified public feed.
- Feed latency and republication licenses: `[unverified]` for all. Store titles, prices, links, timestamps only.

## 4. Free-tier limits
- **ntfy.sh:** 250 messages/day hosted. Burst 60 requests, then refill 1 per 5s (publish docs) vs 1 per 10s (FAQ): conflict, `[unverified]` actual. Self-hosting possible.
- **Supabase Free:** 500 MB database, 2 active projects, projects pause after 1 week of inactivity. Scheduling via `pg_cron` (+ `pg_net` to call Edge Functions); sub-minute schedules documented. Whether cron activity prevents pausing: `[unverified]`.

## 5. Error fares (US DOT)
- Once purchased, a fare generally cannot be repriced upward.
- Mistaken-fare policy (DOT, 2015): non-enforcement allowed if the seller shows a mistake and refunds the ticket plus reasonable, actual, verifiable reliance expenses (e.g. nonrefundable hotel).
- 24-hour rule: for tickets bought 7+ days before departure, the airline must offer a 24-hour hold OR free cancellation (not both). Applies to airlines, not directly to third-party agents.
- No time limit after which an error fare must be honored: `[unverified]` / not in the cited policy.
- Cases: United/Denmark site 2015, not honored, DOT declined to enforce. Cathay Pacific 2019 first/business, honored (premium cabin).

## 6. Prediction evidence (earlier agent report)
- 2019 study (3 tools, 77 days, 10 one-way routes): "no best website", no accuracy numbers in abstract.
- FiveThirtyEight (old, undated): KAYAK advice saved money on 5 of 15 routes.
- Vendor ">90% accurate" claims: unverified. No independent proof of reliable buy-or-wait prediction found. Fixed caps plus percent-below-median are the safer core.

## Could not verify (open)
1. Exact Going "Asia $500 or less" source.
2. Economy-only cheap/normal/expensive bands per season for all routes; SJC/OAK route benchmarks beyond Honolulu.
3. Amadeus first-party shutdown notice.
4. Duffel individual live access and unattended monitoring rights.
5. Travelpayouts current acceptance and storage terms.
6. Working public feeds for Secret Flying, Going; Fly4Free RSS operation; The Flight Deal endpoint; r/flightdeals status.
7. Deal-feed latency and reuse licenses.
8. ntfy real refill interval; Supabase cron vs inactivity pausing.
9. Any DOT deadline for honoring an error fare.
