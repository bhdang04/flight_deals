# flight-deals

A personal flight-deal detector. It watches for unusually cheap round-trip flights from the San Francisco Bay Area (SFO, SJC, OAK), flags must-see destinations at a good price, and pings my phone before the deal dies.

> **Status: Phase 1, planning. Nothing is built yet.** This README describes the plan, not working software. Nothing here claims a feature works until it has been tested.

## Why

Mistake fares and flash sales last minutes to hours. I want to hear about them first, without checking five sites, and without being pinged about deals that have already vanished.

## Planned features

- **Deal detection:** a price cap per region at first, then "percent below this route's median" once there is price history.
- **Explore anywhere:** cheapest places to go next month, as a map and a list.
- **Watchlist:** pick a route, get pinged when it drops.
- **Deal confidence:** each deal is labeled error fare suspected, flash sale, or seasonal low, with honor odds of Likely / Uncertain / Risky. No fake percentages.
- **Normal vs deal:** every card shows the usual price next to the deal price.
- **Deals that die get removed:** live deals are re-checked every 15 minutes, and anything that jumps 15%+ or becomes unbookable moves to an Expired tab. The morning brief re-checks before sending.
- **Alerts:** ntfy push (with quiet hours) or an 8:00 AM email brief. My choice.
- **Link out only:** it never books anything for me.

Later: price calendar, best-time-to-go overlay, total trip cost.

## Machine learning

Step one is plain statistics (a rolling median and percent below it). ML comes in Phase 3 as a measured experiment: a model only ships if it beats the simple baseline, and "buy now vs wait" only ships if it beats "always buy now," both on data I collected myself. I will publish accuracy numbers only after measuring them. Public evidence for reliable buy-or-wait prediction is thin.

## Stack (planned)

TypeScript, React, Supabase (free tier), a free scheduled cloud job, ntfy for push. Budget: $0.

## Data sources and honesty

No free, verified, real-time fare API for unattended monitoring has been confirmed yet (see [docs/RESEARCH.md](docs/RESEARCH.md)). Detection will start from public deal feeds and any official API that permits it. Terms of service matter: this project does not scrape sites that forbid it. The public demo will use mock or cached data, and every mock number is labeled MOCK.

## Roadmap

1. **Phase 1:** research plan (`DEALS-PLAN.md`), editable price caps and must-see list.
2. **Phase 1.5:** UI prototype on mock data (animated map, deal feed, deal cards).
3. **Phase 2:** real detection and alerts on whatever data sources the research confirms.
4. **Phase 3:** price history, ML experiments, measured against baselines.

## Not financial or travel advice

Fares change and can be cancelled. A mistake fare may not be honored: in the US, airlines may cancel a demonstrably mistaken fare if they refund the ticket and reasonable out-of-pocket costs you incurred relying on it. Don't book nonrefundable hotels before the ticket is confirmed.

## License

Not chosen yet.
