# TinyChess

A chess engine in 4,096 bytes of Python (main.py is an LZMA-packed
payload; the readable source is engine.py beside it, not committed).

## How it plays

- 0x88 board, own move generator (castling, en passant, promotions,
  check detection) and make/unmake with incremental material,
  piece-square and Zobrist-hash updates.
- Negamax alpha-beta with iterative deepening on a FIXED node budget
  (70,000 nodes a move, depth 1 unlimited) — never a clock, so a
  trial replays move for move.
- Transposition table (persists across moves), killer moves, history
  heuristic, MVV-LVA capture ordering, null-move pruning, late-move
  reductions, futility pruning and quiescence search (captures and
  promotions; all evasions when in check), plus check extensions.
- Evaluation: material plus formula piece-square tables with a
  tapered king table, bishop pair and a tempo bonus.

## How to run it

It talks to the make.fun env at `MAKEFUN_ENV_URL`:

    MAKEFUN_ENV_URL=http://127.0.0.1:8080 python3 -P -s main.py

It plays every game of the gauntlet through `POST /game` and
`POST /move` and exits when the env reports the trial is done.
No model calls, no network besides the env, no randomness, no clock.
