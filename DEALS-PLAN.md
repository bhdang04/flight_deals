# DEALS-PLAN (Phase 1)

Planning only. No app code exists. Every fact comes from `docs/RESEARCH.md` (a Perplexity report, 2026-11-23, not independently verified by Claude) or is marked `[unverified]`. No official-page lookups were done in this pass, so every item on the research "Could not verify" list is still open. Prices in `config/` are seed guesses.

## 1. Detection signals

Ranked by speed, then reliability. "Legal" means official API or public feed only.

| # | Signal | Speed | Reliability | Legal path / note |
|---|---|---|---|---|
| 1 | Error fares (mistake pricing) | Minutes to hours | Low honor odds, high payoff | Seen via deal feeds and price anomalies. See section 8. |
| 2 | Airline flash sales / promo pages | Hours to days | High that the price is real | Airline email and promo pages. Reading a page by hand is fine. Automated fetching: check each airline's terms first `[unverified]`. |
| 3 | Deal-feed monitoring (Fly4Free, The Flight Deal; r/flightdeals unreliable) | Feed latency `[unverified]` | Medium; human-curated, can be stale | Public RSS only. Store title, price, link, timestamp. Endpoints and operation `[unverified]` (RESEARCH s3). |
| 4 | Price-history anomalies | Depends on data age | Medium | Travelpayouts cached prices (about 2-7 days old) if accepted `[unverified]`. Cached data cannot catch a minutes-old error fare. |
| 5 | Fare-class changes | Hours | Low | No public source found. `[unverified]`. Skip in Phase 2. |
| 6 | New-route launches | Days | Medium (intro fares tend to be low `[unverified]`) | Airline press releases, manual. Low priority. |
| 7 | Flexible-date sweeps | Per API call | Medium | Travelpayouts calendar and cheap methods (300/min per RESEARCH). Duffel is metered at $0.005 per extra search, so not free monitoring. |
| 8 | Nearby-airport and positioning tricks | n/a | Medium | Locked airports: SFO, SJC, OAK only. Positioning from other airports is out of scope. |
| 9 | Hidden-city / throwaway ticketing | n/a | **RISK, not a recommendation** | Often violates airline contracts of carriage `[unverified]`; can lead to cancelled return legs or loyalty penalties `[unverified]`. Never alert on these. |

## 2. Detection model

### Phase 2 core (plain statistics)
1. **Price cap first.** Deal if the round-trip economy price is at or under the region cap in `config/caps.json` (or the must-see `cap_usd_override`).
2. **Percent below rolling median.** After about 4 weeks of history for a route, deal if the price is X% below the route's rolling median. X is an open tuning choice (not set here; no number is claimed).
3. **Seasonality.** Keep the median per route and per month. Compare against same-season history once enough exists. Until then, seasonality is a note on the card, not a filter.
4. **Quality filter (locked):** max 1 stop, layover under 6h, honor the refused-airlines list.

**Required data per observation:** route (origin, destination), depart and return dates, trip length, price, bag-included price if available, airline, stops, layover, source, observed-at timestamp, booking link.

**Accuracy measure: alert precision** = share of alerts whose price did not rise 15% or more within 24h. Same threshold as the "gone" rule. Log every alert and re-check at 24h. Recall is not measurable without ground truth; do not claim it.

### Phase 3 ML experiment
- Buy-vs-wait ships only if it beats "always buy now" on held-out data Brian collected himself.
- **No accuracy number may be claimed before it is measured.** Vendor ">90% accurate" claims are unverified.
- Public evidence is weak (RESEARCH s6): a 2019 study (3 tools, 77 days, 10 one-way routes) found "no best website" and reported no accuracy numbers in the abstract. An old FiveThirtyEight piece (undated) found KAYAK advice saved money on 5 of 15 routes. No independent proof of reliable buy-or-wait prediction was found.
- Python only if simple models fail.

## 3. Data sources

All statuses are from RESEARCH s2-s3 as given.

| Source | Gives | Cost | Limits | ToS / constraint | Viability |
|---|---|---|---|---|---|
| Amadeus Self-Service | Fares | n/a | Reported shut down 2026-07-17 (phocuswire, 2026-02-09; first-party notice not retrieved) | n/a | **Unavailable** |
| Skyscanner API | Prices | n/a | Commercial partners only (100k+ monthly users) | Live Pricing bans unattended requests | **Not usable** |
| Kiwi Tequila | Search | n/a | Invitation-only | n/a | **Not usable** |
| Duffel | Live offers | Extra searches $0.005 each | 10 requests/60s | Sandbox prices not real. Individual live access, retention, and unattended monitoring `[unverified]`. Calls must go via own backend | Weak. Metered, so not free monitoring. Upgrade candidate |
| Travelpayouts / Aviasales Data API | Cached cheap prices and calendars | Free `[unverified]` | e.g. 300/min calendar and cheap, 60/min monthly | Token registration. Informational use. Docs suggest caching 24h. Current acceptance `[unverified]` | **Best primary candidate**, if accepted. Not real-time |
| Aviasales live Search API | Live search | n/a | 100 vs 200/hour (conflicting) | Automatic collection of booking links prohibited | **Not a fit** |
| FlightAPI | Fares | Free trial only (20 calls vs 30 credits, conflicting) | Not recurring | Terms `[unverified]` | Not a fit for monitoring |
| Fly4Free RSS (fly4free.com/feed/) | Deal posts, about 15/day | Free | Retrieval failed; operation `[unverified]` | Store title, price, link, timestamp only | Fallback candidate, verify first |
| The Flight Deal RSS | Deal posts | Free | Endpoint `[unverified]` | Site says "All Rights Reserved" | Fallback candidate, verify first |
| r/flightdeals | Deal posts | n/a | RSS reportedly shut 2026-11-13; new API applications stopped 2026-10-31 (Mashable, 2026-09-30) | n/a | Unreliable |
| Secret Flying, Going | Deals | n/a | No verified public feed | Scraping would be needed, so **excluded** | Not usable |
| KAYAK, Google Flights, airline sites | Prices | n/a | n/a | Scraping forbidden. Manual one-time reference only | **Excluded** as automated sources |

**Recommended primary path:** Travelpayouts Data API for cached price history and calendar sweeps, plus public RSS deal feeds for fast signals. This needs Brian to create an account and token. I have not done that and will not without his go-ahead.
**Fallback path:** RSS feeds only (Fly4Free, The Flight Deal, after checking they work), with manual entry of airline flash sales. Each deal is checked by hand via link-out. No fare API at all.
**Upgrade path to paid API:** Duffel (documented $0.005 per extra search beyond the 10/60s limit). Confirm individual live access and monitoring rights first `[unverified]`. Budget is $0 until Brian approves otherwise.

**Gap:** with neither source confirmed, the 15-minute re-check of live deals (section 5) has no verified data source. Cached Travelpayouts data would be 2-7 days old, so "unbookable" and 15% rise checks would be unreliable. This is risk #1.

## 4. Cross-source comparison

- Compare the same itinerary key only: origin, destination, dates, cabin (economy), stops. Different dates or cabins are never compared.
- Each source's price is shown as reported, labeled with its source and last-checked time. No scraping to fill gaps. If only one source has a fare, the card shows one source.
- Compare against the route's own stored median (normal-vs-deal), not another vendor's live page.

**Deal card fields:**
1. Price (headline)
2. Bag-included price next to the headline
3. Source name
4. Last-checked time
5. Booking link (link out only)
6. Basic-economy restrictions (shown on every card)
7. Normal vs deal comparison (route median vs deal price)
8. Confidence label (error fare suspected / flash sale / seasonal low)
9. Honor odds where relevant (Likely / Uncertain / Risky). Never a percentage.
10. Small advisory badge

## 5. Alerts

- **ntfy push** (instant mode) and **email brief at 8:00 AM PT**. Brian chooses.
- **Quiet hours** 10 PM-7 AM PT. Only the highest-confidence error fares break them.
- **Re-check** every live deal every 15 minutes.
- **Gone rule:** price up 15%+ or unbookable. Move to the Expired tab and keep price history.
- **Instant mode** sends a "deal gone" follow-up.
- **Morning brief** re-checks each deal first and drops any that rose.

**Avoiding stale and false alerts:**
- Show last-checked time on every alert. Never present cached data as live.
- Alert only on the quality-filtered set.
- De-duplicate by itinerary key; do not re-alert the same deal.
- Cap instant alerts per day so ntfy's limit is never hit.
- Log each alert for the precision measure in section 2.

**Free-tier limits:**

| Service | Verified (from research) | Gaps `[unverified]` |
|---|---|---|
| ntfy.sh | 250 messages/day hosted. Burst 60 requests, then refill 1 per 5s (publish docs) vs 1 per 10s (FAQ). Self-hosting possible | Which refill interval is real |
| Supabase Free | 500 MB database, 2 active projects, pause after 1 week of inactivity. `pg_cron` (+ `pg_net` to Edge Functions); sub-minute schedules documented | Whether cron activity prevents pausing. If not, a weekly manual visit or a keep-alive is needed |

Email delivery service: not chosen `[unverified]`. Picking one may require an account or key, so ask first.

## 6. Architecture

TypeScript, React, Supabase free tier, a free scheduled job. Phase 2 only; nothing is built.

| Step | Component | What happens |
|---|---|---|
| 1 | Scheduled job (Supabase `pg_cron` + Edge Function, or a free scheduler `[unverified]`) | Pull RSS feeds and Travelpayouts data |
| 2 | Supabase Postgres | Store observations, deals, alerts, config |
| 3 | Detection function | Apply caps, then median test, then quality filter |
| 4 | Re-check job (15 min) | Mark deals gone or live |
| 5 | Notifier | ntfy publish, or add to the 8:00 AM brief |
| 6 | React app | Read-only view: Explore, watchlist, feed, Expired tab |

Data volume against the 500 MB limit has not been estimated. Estimate before Phase 2. Any schema change needs Brian's approval.

## 7. Features by phase

**Phase 1 plan covers:**
- **Explore anywhere:** cheapest destinations from SFO/SJC/OAK for a chosen window, as map and list.
- **Watchlist:** Brian picks a route; an alert fires when it meets the deal test.
- **Deal-confidence labels:** error fare suspected / flash sale / seasonal low, with honor odds Likely / Uncertain / Risky. Labels are heuristics based on source and price gap, with no percentages.
- **Normal-vs-deal comparison:** route median beside the deal price. Before ~4 weeks of history it shows the regional cap instead and says so.
- **Advisory badge:** small note such as "restrictions apply" or "may not be honored".

**Deferred:**
- **Price calendar:** a grid of the cheapest price per departure date for a route. Needs history or the calendar endpoint of the primary source.
- **Best-time-to-go overlay:** marks months when a route is usually cheapest. Needs about a year of per-route history. Until then it would be a guess, so do not ship it.
- **Total trip cost:** fare plus bags plus rough ground costs. Non-fare costs have no verified source, so they must be labeled as estimates.

## 8. Error-fare handling

**What the DOT sources support (RESEARCH s5):**
- Once purchased, a fare generally cannot be repriced upward.
- Mistaken-fare policy (DOT, 2015): non-enforcement is allowed if the seller shows a mistake and refunds the ticket plus reasonable, actual, verifiable reliance expenses (e.g. a nonrefundable hotel).
- 24-hour rule: for tickets bought 7+ days before departure, the airline must offer a 24-hour hold OR free cancellation (not both). It applies to airlines, not directly to third-party agents.
- Cases: United/Denmark site (2015) was not honored and DOT declined to enforce. Cathay Pacific (2019, first/business) was honored; that was a premium cabin, so it is weak evidence for economy.

**What they do not support:** any deadline after which an error fare must be honored. `[unverified]` / not in the cited policy.

> **Warning: do not book nonrefundable hotels, tours, or other extras until the airline has confirmed the ticket.** A mistaken fare may be cancelled, and reliance costs are refunded only if they are reasonable and verifiable.

Honor odds therefore stay Likely / Uncertain / Risky with no percentage. Facts above are from a secondary report, so verify on the DOT page before relying on them. This is not legal advice.

## 9. UI direction (Phase 1.5, not started)

Screens:
- Map or globe with glowing deal pins
- Live feed with a "just dropped" animation and a freshness indicator
- Deal card with sparkline and source comparison
- MUST-SEE badge

Targets: WCAG AA, full keyboard use, `prefers-reduced-motion` respected, works at 375px and 1440px.
All fake data is labeled MOCK in code and UI.
**At Phase 1.5, invoke the `ui-ux-pro-max` skill.** It was not invoked in this phase.

## 10. Top 5 risks and open decisions (ranked)

1. **No verified live data source.** Travelpayouts acceptance and its terms are unknown, and its data is days old. Decide: apply for a Travelpayouts token (needs an account), or accept feed-only detection. Without live data, the 15-minute re-check and "unbookable" test cannot work.
2. **Deal feeds may be dead or slow.** Fly4Free and The Flight Deal endpoints are unverified; r/flightdeals is unreliable. Brian should confirm one working feed before Phase 2.
3. **Free-tier fit.** Supabase may pause on inactivity, and 15-minute checks plus ntfy's 250/day cap need a rate budget. Decide: add a keep-alive, or accept pauses.
4. **Starting caps are guesses.** `config/caps.json` has no verified basis. Several caps look below the KAYAK monthly medians in RESEARCH s1 (those are medians, not deal prices, but they flag that some caps may never fire). Decide: review the caps after about 4 weeks of real data.
5. **Error fares may not be honored.** Alerts can cause real losses if Brian books extras early. Decide how loud the advisory badge and warning should be, and whether error-fare alerts should be limited to the highest-confidence cases only (as locked for quiet hours).
