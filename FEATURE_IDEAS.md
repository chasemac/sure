# Feature ideas for the chasemac/sure fork

Prioritized 2026-10-19 after a quick codebase spot-check (models/controllers/views).
Verify in the UI before building — a few of these may exist in partial form.

## Top picks

1. **Full data export (CSV/Parquet)** — Your data lives on your hardware, but can you get it *out*? One-click full export of transactions, accounts, holdings. The most self-hosted feature there is, and I found no transaction export in the controllers.
2. **Local-first AI categorization** — The app's AI features call OpenAI, which sends your transaction data to the cloud. Wire categorization to a local model instead (Ollama is already referenced in `.env.example`) so nothing leaves the mini. Fits the whole point of this project.
3. **Unusual-spending alerts** — Push notification infra already exists (`notification_delivery.rb`, push subscriptions). Add a detector: spending spike vs your baseline, or a single charge far above normal. High signal, low noise if tuned.
4. **Receipt capture** — Snap a photo, attach it to a transaction. Family with three kids = a lot of receipts worth keeping next to the charge.
5. **Subscription audit view** — Bill tracking exists (`bills/`), but there's no "you pay $X/mo across N subscriptions" rollup. Plaid's recurring-transaction data can feed a cancel/keep review screen.
6. **Savings goals with auto-tracking** — No savings-goal model found. Example: a trip fund that fills via rules (monthly transfer, round-ups). You have a $2,893 Houston trip coming up — good first dogfood.
7. **Tax-time export** — One-click annual summary by category for your CPA. Small build, big payoff every April.

## Setup (not code)

8. **Tailscale access** — Put the mini on your tailnet so the dashboard works from your phone, not just the closet machine. No code, just config.

## Deliberately not on the list

- Budgeting overhauls — the app already does budgets well; don't rebuild its strength.
- More bank connectors — Plaid covers the US banks; breadth isn't the bottleneck.
