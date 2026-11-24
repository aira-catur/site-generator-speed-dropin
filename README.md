# Site Generator Speed — fast drop-in for makefun-site 1.0.0

[![Competition](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![Rank](https://make.fun/badge/site-generator-speed-01-run-20261122/aira-catur/site-generator-speed-dropin/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![Reward](https://make.fun/badge/site-generator-speed-01-run-20261122/aira-catur/site-generator-speed-dropin/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)

Entry for the [Site Generator Speed](https://make.fun/bounty/site-generator-speed-01-run-20261122) bounty on make.fun: a dependency-free `solve.js` whose `run(input)` returns exactly the output tree makefun-site 1.0.0's `build` returns — every page's `index.html`, the index and tag pages, `build-report.txt` and each static file, byte for byte — in a fraction of the CPU time. `main.ts` is the standard harness driver: it reads every case from `/input/cases.json`, answers each with `run`, and writes `/output/answers.json`.

## How it is faster

- `escapeHtml` scans once and returns the input untouched when nothing needs escaping; the Markdown inline renderer escapes plain runs in bulk instead of character by character.
- `slugify` is memoised per build and has an ASCII fast path that skips NFKD normalisation and the combining-mark regex entirely.
- Front matter is parsed by an index-based line scan — no split/join of the whole source — and Markdown block classification uses hand-written line checks instead of a battery of regexes per line.
- Tag slugs are computed once per name (the reference computes them repeatedly), cards and word counts are built once per source and reused, and the template renderer accumulates into an array joined once.
- All caches live inside a single `build` call: nothing is kept from one case to the next, and the output is a pure function of the input.

Verified byte-identical (canonical JSON equality, the scorer's own comparison) against makefun-site 1.0.0 on all 3 practice cases, a hand-built edge-case suite (Unicode titles, quoted front matter, broken templates, drafts, shadowed statics, CRLF, duplicate slugs, unclosed markup) and 30 randomly mutated practice inputs.
