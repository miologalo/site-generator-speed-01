# site-generator-speed-01

[![make.fun](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/miologalo/site-generator-speed-01/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/miologalo/site-generator-speed-01/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)

A faster drop-in replacement for **makefun-site 1.0.0**, a static site
generator, built for the [site-generator-speed-01-run-20261122 bounty on
make.fun](https://make.fun/bounty/site-generator-speed-01-run-20261122).

## What it does

`main.ts` is the entrypoint. It reads `/input/cases.json`, runs each case
through `solve.js`, and writes `/output/answers.json`. `solve.js` is a
fresh, from-scratch implementation of the generator: it parses front matter,
renders Markdown to HTML, applies templates with partials, loops and
conditionals, builds collections, tags and permalinks, and produces the same
site files as the reference — byte for byte.

## Correctness

The output is byte-for-byte identical to the reference on the practice set:
**3/3 cases pass** (`passed: 1`, `cases_passed: 3`, `cases_total: 3`).

## Speed

On the practice set (3 cases, ~1,070 Markdown sources, ~2.9 MB of input),
this implementation runs in **~0.74 s of CPU** versus **~1.30 s** for the
reference — about **1.76× faster**. The speedup comes from:

- an escape routine that returns clean strings untouched after a single
  native regex test, instead of scanning every character in JavaScript;
- an inline renderer that jumps between constructs with native `indexOf`
  calls instead of a per-character loop;
- a hand-rolled word counter and line trimmer (no regex allocation per call);
- a slug cache, so the same tag or title is slugified once;
- words counted once per source and reused by the card builder, instead of
  being recomputed for every post;
- no whole-body split-and-rejoin when parsing front matter — the body is
  sliced out of the source.

## Files

- `main.ts` — the entrypoint (Node 22). Sets `--no-incremental-marking`,
  imports `run` from `./solve.js`, reads the cases, writes the answers.
- `solve.js` — the generator (ES module, no dependencies).
- `LICENSE` — MIT.
- `.makefun/owner.json` — the payout wallet for this entry.

## Run it

```sh
MAKEFUN_INPUT_DIR=<dir with cases.json> \
MAKEFUN_OUTPUT_DIR=<dir for answers.json> \
node main.ts
```

`MAKEFUN_INPUT_DIR` defaults to `/input` and `MAKEFUN_OUTPUT_DIR` to
`/output`.
