# flight-deals

Personal flight-deal detector for Brian (also a resume + personal-site project, maybe a product later). Finds unusually cheap round-trip flights from the SF Bay Area and alerts Brian fast. Link-out only. NEVER buys anything.

## Status
Phase 1 (planning): write `DEALS-PLAN.md` from `docs/RESEARCH.md`. No app code yet. UI prototype is Phase 1.5, after the plan. Do not start the next phase unless asked.

## Decisions (locked, from the grilling session)
- **Airports:** SFO = international, SJC = domestic, OAK = backup for both. Pacific time. No other airports.
- **Trips:** round-trip economy, 4-14 days; Fri-Sun weekend option. One-way later. Show basic-economy restrictions on every card.
- **Deal test:** per-region price cap first (`config/caps.json`), then percent below the route's rolling median once ~4 weeks of history exist.
- **Quality filter:** max 1 stop, layover under 6h, red-eyes OK, show a bag-included price next to the headline price, honor a refused-airlines list.
- **Alerts:** ntfy push, email morning brief at 8:00 AM PT. Instant mode has quiet hours 10 PM-7 AM that only the highest-confidence error fares break. Instant mode sends a "deal gone" follow-up. The morning brief re-checks each deal first and drops any that rose.
- **Gone rule:** re-check live deals every 15 min. Gone = price up 15%+ or unbookable. Move to an Expired tab and keep its price history.
- **Confidence labels:** error fare suspected / flash sale / seasonal low. Honor odds: Likely / Uncertain / Risky. NEVER show a fake percentage.
- **Must-see list:** `config/must-see.json` (Japan, Korea, Vietnam, NYC, Europe). Brian edits it.
- **Phase 1 features:** Explore anywhere, watchlist, deal-confidence, normal-vs-deal comparison, small advisory badge. Later: price calendar, best-time overlay, total trip cost.
- **ML:** steal-now detection first (rolling median + percent below). Buy-vs-wait is a Phase 3 experiment and ships only if it beats "always buy now" on Brian's own data. No accuracy claim anywhere until measured.
- **Stack:** TypeScript, React, Supabase free tier, free scheduled cloud job. Python only in Phase 3 if simple models fail. Budget $0.

## Rules
- Official APIs and public feeds only. NEVER scrape Google Flights, Skyscanner, Kayak, or airline sites against their terms.
- Store deal titles, prices, links, and timestamps. Do not republish feed articles.
- Anything unverified is marked `[unverified]`. Never invent endpoints, prices, limits, or accuracy numbers.
- Everything fake is labeled MOCK in code and UI. The README says what is real and what is mock.
- Never commit secrets. Keys live in env vars; list names in `.env.example` only.
- Stop and ask before: adding a dependency, an API key, a paid service, creating an account, deleting a file, or changing the database schema.
- Do not add features beyond the current phase. Prefer the smallest change that works.

## Where things are
- `docs/RESEARCH.md`: verified findings and open unknowns (Perplexity report, 2026-11-23).
- `config/caps.json`, `config/must-see.json`: editable data (Phase 1).
- `DEALS-PLAN.md`: the Phase 1 deliverable.

## Commands
None yet. Add real commands here when code exists, and only commands that were run.
