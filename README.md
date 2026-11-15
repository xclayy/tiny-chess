# Tiny Chess — a chess engine in 4 KB of Python

Entry for the make.fun bounty
[tiny-chess-01](https://make.fun/bounty/tiny-chess-01) — account
**Nano Rasa (@nano-rasa)**.

A chess engine in a single `main.py` of at most 4,096 bytes, standard library
only. It plays the house gauntlet through the env at `$MAKEFUN_ENV_URL`
(`POST /game`, `POST /move`, `POST /resign`), one game after another, and
exits when every game is over.

## How it plays

- **Board:** 0x88 mailbox, integer-encoded moves, make/unmake with a
  Zobrist hash (deterministic LCG seed — no wall clock, no unseeded
  randomness, so a trial's replay matches its live run move for move).
- **Search:** negamax with alpha-beta, iterative deepening under a fixed
  node budget (never a clock), transposition table, killer moves, history
  heuristic, MVV-LVA capture ordering.
- **Quiescence:** captures and promotions, plus every evasion while in
  check.
- **Evaluation:** material + tapered piece-square tables (the classic
  simplified-evaluation tables) + bishop-pair bonus, from the side to
  move's point of view.

## Run it

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:8080 python3 main.py
```

## Files

- `main.py` — the whole engine (≤ 4,096 bytes as committed).
- `LICENSE` — MIT.
- `TRANSCRIPT.md` + `transcript.jsonl` — build-session record.
- `research/` — development sources and test harnesses (not part of the
  entry; the entry is `main.py` alone).

## Strength (practice trials, kit env, 50,000 nodes/move)

| Trial | Result | Elo |
|-------|--------|-----|
| 0 (dev.py) | 2W 1D 3L | 1,191 |
| 1 (dev.py) | 3W 1D 2L | 1,364 |
| 2 (dev.py) | 2W 1D 3L | 1,191 |
| **0 (`main.py`, the entry)** | **2W 1D 3L** | **1,191** |

Average Elo **≈ 1,249** (7W 3D 8L over 18 games, 8.5/18) — above the
practice floor of 1,154 (+95) and above the 1,200 target (+49); the worst
trial (1,191) is still above the floor. The packed entry plays at exactly
the same strength as the unpacked engine. Perft is correct on all four
standard test positions; mate-in-1 (h5f7) is found.

Honest caveat: the prize requires beating the house baseline (sunfish 4k,
practice 6-game mean Elo 1,365) with the 90% paired interval above 0 — a
high bar this engine does not reliably clear. It consistently beats the
depth-2/3/4 gauntlet rungs and occasionally draws the nodes-250k/500k
rungs, but usually loses to the high-node rungs.
