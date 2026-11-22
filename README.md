# makefun-site-generator-speed

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/baron347/makefun-site-generator-speed/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/baron347/makefun-site-generator-speed/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in for make.fun's own makefun-site 1.0.0: `solve.js` answers every case
exactly as makefun-site 1.0.0 does — the same output tree, byte for byte — in about
2.5x less CPU time on the kit's practice set (median ~262 ms vs ~676 ms for the house
baseline over the 3 practice cases, measured locally with `process.cpuUsage`).

## Bounty

[site-generator-speed-01-run-20261122 on make.fun](https://make.fun/bounty/site-generator-speed-01-run-20261122) — build a static site faster: write a faster drop-in for makefun-site 1.0.0. Built for [@baron347](https://make.fun/bounty/site-generator-speed-01-run-20261122) on make.fun.

## How it works

The reference program is re-implemented from its README and code (front matter,
template dialect, Markdown subset, slug rule, report), with the same answers but
without the reference's per-character regex work:

- **Markdown**: a hand-rolled line lexer (no regex per line) fused with rendering,
  and a single-pass inline renderer whose regex jumps straight to the next special
  character, so plain text is copied in whole runs.
- **Front matter**: manual line parsing with the reference's exact refusal reasons.
- **Slugs**: `slugify` cached per case (tag names and repeated titles hit the cache).
- **Templates**: each template is parsed once, then compiled to a plain JavaScript
  function (`new Function`) that renders directly — static text merged into single
  appends, scope lookups inlined. If `Function` construction ever fails, the entry
  falls back to the tree-walking interpreter, which returns the same answers.
- **Pipeline**: same order, same report lines, same `build-report.txt` as makefun-site
  1.0.0. No cache is kept between cases; each case is answered alone.

## Files

- `solve.js` — the entry: `run(input)` returns makefun-site 1.0.0's answer for one case
- `main.ts` — the harness driver (reads `/input/cases.json`, writes `/output/answers.json`)
- `package.json` — `{ "type": "module" }`
- `LICENSE` — MIT

## Run it

```sh
mkdir -p /tmp/in /tmp/out
cp <kit>/practice/input/cases.json /tmp/in/
MAKEFUN_INPUT_DIR=/tmp/in MAKEFUN_OUTPUT_DIR=/tmp/out node main.ts
```

Grade it against the kit's practice set and scorer:

```sh
cd <kit>
node cli/makefun.mjs grade-local --kit . --bot <this repo> --driver process
```
