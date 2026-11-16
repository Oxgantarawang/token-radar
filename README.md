# token-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/Oxgantarawang/token-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/Oxgantarawang/token-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar: it reads a sealed DefiLlama snapshot of 758 protocols, ranks the ones that record no token by an implied value, and shows a reader how to farm each one. The watchlist (40 protocols with $50M+ TVL and no token on record) is the floor, never the ceiling — the board also carries the largest pre-token protocols below that floor, so the radar finds launches early. Protocols whose token already trades are set aside in a Live view with their market caps shown, never mixed into the ranking.

## The model, in plain words

Every pre-token protocol gets one implied value from the snapshot's own figures:

1. **Category multiple** — the median market-cap-to-TVL ratio of the token-bearing protocols in the same category (at least 3 comparables, else the global median), clamped to 0.05–1.50. A lending protocol is priced like lending protocols, a DEX like DEXes: the market's own multiples, not a number picked by hand. The multiples are rounded to four decimals once, when the board is built, so the dashboard's recomputation agrees with the board to the dollar.
2. **Growth** — seven-day TVL growth, clamped to −50%…+150% and weighted at 0.4, so one wild week cannot dominate the board.
3. **Maturity** — the longer a protocol has run with no token, the higher it scores: 0.70 plus up to 0.30 for up to three years since it was first listed. A protocol with no listing date is scored as if it were a year old.
4. **Breadth** — +4% per extra chain it runs on, up to +20%.

`implied = round(TVL × category multiple × growth × maturity × breadth)`

No protocol name is special-cased. The dashboard recomputes every row from the same figures and shows whether its number matches the board.

## Farm plans

Each row carries six concrete steps — what to do, what it costs, how long it takes — derived from the protocol's category, chains, TVL and age. The steps never invent a URL, a points program or a confirmed airdrop: the snapshot records no token, so the plan farms the protocol as it exists today and says so.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input path/to/snapshot.json
```

Node 22 or later, standard library only, no install. In grading the snapshot is mounted at `/input/snapshot.json` and the output goes to `/output`. The program writes `board.json`, `data.js`, `dashboard/` (the static page), `screenshots/` and `dossier.json`. Open `out/dashboard/index.html` (served over HTTP so it can load `data.js`) to use the dashboard; deep links work with `?protocol=<slug>`, `?filter=<category>`, `?chain=<chain>`, `?q=<text>` and `?minTvl=<dollars>`.

## Layout

- `main.ts` — the entrypoint: reads the snapshot, scores the board, writes `/output`.
- `lib/model.ts` — the model, the farm plans and the board checks.
- `vendor/makefun-board.ts` — make.fun's dossier library (copied from the kit, as its header instructs).
- `dashboard/` — the static page: `index.html`, `app.css`, `app.js`. No network, no framework.
- `screenshots/` — the three screenshots: the board, a protocol's detail view, a filtered view.

---

<sub>Entered on [make.fun](https://make.fun/?ref=0h2zmzc4&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
