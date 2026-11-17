# Tiny Chess — a 4KB chess engine

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/iwandarmawan/tiny-chess/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/iwandarmawan/tiny-chess/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A compact chess engine for [make.fun tiny-chess-01](https://make.fun/bounty/tiny-chess-01). It plays the house
gauntlet through the env at `MAKEFUN_ENV_URL` (`POST /game`, `/move`,
`/resign`) and searches to a fixed node budget — never a clock — so a
replayed trial produces the same moves.

## Engine

- 0x88 board with an occupied-square list; full move generation
  (castling, en passant, promotions).
- Alpha-beta with iterative deepening to a fixed 50,000-node budget per
  move, quiescence over captures/promotions and replies to check.
- Evaluation: material (100/320/330/500/900) + piece-square tables.
- Move ordering: MVV-LVA captures first, then promotions.
- Deterministic: fixed node count, seeded Zobrist-free design, no
  wall-clock use, no randomness.

## Size

`main.py` is 3,216 bytes as committed. The engine source is stored as a
zlib-compressed, base85-encoded payload that `main.py` decompresses and
execs at startup; payload + unpacker together are within the 4,096-byte
cap (compression is allowed and counted by the rules). Python 3.13
standard library only. It reads no other file in the repository.

## Local result

Practice trial-0 (6 games vs the house gauntlet): 2.0/6, Elo estimate
~1,099 — wins vs depth-2 and depth-3, losses vs depth-4, nodes-100k,
nodes-250k and nodes-500k. Trial wall ~264s, well inside the 1,800s
limit; slowest move ~2-3s, inside the 10s per-move limit.

## Grade it locally

You need Docker and the `makefun` CLI. Download the competition's dev
kit, unpack it, and from inside it run:

    makefun grade-local --kit . --bot <your repository> --driver docker

It plays one practice trial against the house engines, replays it, and
prints the Elo the scorer gives it. Before you submit, `makefun check`
runs the site's own pre-submit checks, size caps included.
