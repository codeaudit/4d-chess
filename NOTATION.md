# PGN4D v1 / LAN4D

**A portable game record for 4D Chess by intuitionmachine.com.**

This is the format implemented in `records.js` and embedded in `index.html`.
It is an application-specific extension for **Fourfold-4x4x2x2**, not a
recognized general standard for 4D chess, and not ordinary chess PGN.

## Design

Traditional chess notation supplies piece letters, capture/check/promotion
symbols, numbered turns, and result markers. A PGN-like header plus movetext
layout makes the file readable without special software. Every square is
extended by its Z and W coordinates. Long algebraic notation writes both
origin and destination on every move, so identical pieces on different boards
never need ambiguous file-only or rank-only disambiguation.

The ordinary-chess references behind these conventions are the
[FIDE notation appendix](https://handbook.fide.com/chapter/e012023) and the
[PGN specification](https://www.saremba.de/chessgml/standards/pgn/pgn-complete.htm).
The 4D rules and extensions below are defined by this app, not by FIDE or the
PGN specification.

Files are UTF-8 text with the recommended extension **`.pgn4d`**. Export writes
LF line endings; import also accepts CRLF and a UTF-8 byte-order mark.

## Square addresses: `a1@ZW`

| Part | Values | Meaning |
|---|---|---|
| File | `a`–`d` | X position inside a board |
| Rank | `1`–`4` | Y position inside a board |
| First digit after `@` | `0` or `1` | Z: left/right board column |
| Second digit after `@` | `0` or `1` | W: bottom/top row of boards |

The two digits are ordered **Z, W**, not W, Z. `b3@01` is b3 on Z=0,
W=1: the top-left board. The UI's longer address `b3 [0,1]` names exactly the
same square. Screen distance and rotation do not change any addresses.

All square addresses are case-sensitive. Use ASCII `@`; spaces inside an
address are not allowed.

## Move grammar

```text
[Piece]From[-|x]To[=Q][+|#]

Piece = K | Q | R | B | N       (absent for a pawn)
From  = [a-d][1-4]@[01][01]
To    = [a-d][1-4]@[01][01]
```

Square brackets in this grammar indicate optional components or permitted
characters; they are not literal move characters. Each exported move is a
single token with no spaces. Piece letters are uppercase for **both colors**;
turn order and the starting position determine the moving color.

`-` is a quiet move, `x` a capture, `=Q` automatic promotion to a queen,
`+` check, and `#` checkmate. `#` replaces `+`, rather than following it.
The capture, promotion, and check/mate markers must match the actual move;
imports do not silently repair inaccurate markers.

Examples from engine-validated positions:

```text
Ra1@00-a1@01        rook moves only along W
Bb1@10-b1@01        bishop changes Z and W
Rb4@01xb1@01       rook capture on the same board
Kc1@00xb1@01       safe king capture across boards
Nc4@11-c2@01       knight jumps along Y and Z
b2@00-b3@00        ordinary pawn advance
c3@11xd2@11        pawn capture
d2@11xc1@11=Q+    Black pawn captures, promotes, and checks
```

Pawn captures always include their complete origin, even when
X does not change, such as a capture along Y+W. No `P` letter is used.

This variant has no castling, en passant, initial pawn double move, or choice
of promotion piece, so there are no tokens for those moves. Kings may capture
non-king attackers when legal but kings themselves are never captured.

## Turns and results

Full turns are numbered with a period before White's move:

```text
1. Bb1@10-b1@01 Rb4@01xb1@01
2. Kc1@00xb1@01 d3@11-d2@11
```

If a record begins with Black to move, the first number has three periods:

```text
12... BlackMove
13. WhiteMove BlackMove
```

`WhiteMove` and `BlackMove` above are placeholders, not valid move tokens.
The initial fullmove number comes from FEN4D. After each Black move, it
increases by one. A *ply* is one player's move, not a pair of moves.

The final token is mandatory and must equal the `Result` header:

| Token | Meaning |
|---|---|
| `*` | Unfinished game, including zero recorded moves |
| `1-0` | White won by checkmate |
| `0-1` | Black won by checkmate |
| `1/2-1/2` | A draw recognized by this engine |

Results are derived from the recorded position. This version does not model
resignations, clocks, agreed draws, or adjudication. It therefore rejects
those result claims when the position itself is still ongoing.

## Headers

Every tag is written as `[Name "value"]`. Quotes and backslashes in values
are escaped as `\"` and `\\`. Each exported tag occupies its own line; a blank
line separates headers from moves.

Exports include these descriptive tags:

```text
[Event "4D chess game"]
[Site "intuitionmachine.com"]
[Date "2026.09.06"]
[Round "-"]
[White "White"]
[Black "Black"]
[Result "*"]
```

The Date above is illustrative; newly created games use the local creation
date. Unknown dates may be written `????.??.??`. Player names and the event
name are freely editable text, limited to 200 characters on one line. No tag
is treated as executable code, markup, or an instruction to fetch a URL.

The following tags identify the format and are required with these values:

```text
[Variant "Fourfold-4x4x2x2"]
[Format "PGN4D"]
[FormatVersion "1"]
[Notation "LAN4D"]
[SetUp "1"]
[FEN4D "...starting position..."]
```

`FEN4D` contains the actual position encoding described next; the ellipsis
above is not valid. A complete starting position is always included, even
for the usual opening, so records are not dependent on an implied layout.
Unknown descriptive tags may be read but are not preserved on re-export.
Duplicate tags are rejected. Version changes must be explicit; the importer
does not guess the meaning of unknown versions or variants.

## FEN4D: the starting position

FEN4D is this app's compact starting-position encoding. It adapts the
rank-compression idea used by ordinary FEN, but its board dimensions and
fields differ. It contains **four boards separated by `|`**, followed by
three space-separated fields:

```text
board00|board10|board01|board11 side halfmove fullmove
```

Board order is always:

1. Z=0, W=0: bottom-left board.
2. Z=1, W=0: bottom-right board.
3. Z=0, W=1: top-left board.
4. Z=1, W=1: top-right board.

Within each board, ranks run **4 down to 1**, separated by `/`; within a rank,
files run **a through d**. `KQRBNP` are White pieces; lowercase `kqrbnp` are
Black pieces. Digits `1`–`4` count consecutive empty squares. Each rank must
expand to exactly four squares and each board must have four ranks.

`side` is `w` or `b`. `halfmove` is the number of consecutive plies since the
last pawn move or capture (0–100). `fullmove` is the positive numbered turn
at the starting position (1–1,000,000).

The app's ordinary starting position is:

```text
4/4/PPPP/RNKQ|4/4/4/1BR1|1rb1/4/4/4|qknr/pppp/4/4 w 0 1
```

Exactly one king of each color is required. The side that just moved may
not be in check. A pawn already on its promotion rank is invalid: it should
have become a queen. Custom setups are position-validated, not proven
historically reachable from the standard opening.

A complete game export retains repetition history through its move list.
A manually supplied midgame FEN4D cannot encode occurrences **before** that
starting point; repetition counting begins at the provided position. The
halfmove counter, however, is preserved explicitly.

## Complete game example

This opening is the beginning of the included multi-move sample:

```pgn
[Event "Across four boards"]
[Site "intuitionmachine.com"]
[Date "????.??.??"]
[Round "-"]
[White "Demo White"]
[Black "Demo Black"]
[Result "*"]
[Variant "Fourfold-4x4x2x2"]
[Format "PGN4D"]
[FormatVersion "1"]
[Notation "LAN4D"]
[SetUp "1"]
[FEN4D "4/4/PPPP/RNKQ|4/4/4/1BR1|1rb1/4/4/4|qknr/pppp/4/4 w 0 1"]

1. Bb1@10-b1@01 Rb4@01xb1@01 2. Kc1@00xb1@01 d3@11-d2@11 *
```

The computer can reconstruct every position from this text without consulting
the rendered boards. The sample is for learning the mechanics, not a claim
that these are strong strategic moves.

## Import behavior and limits

Imports are validated in isolation before the UI enters replay. Each move
must belong to the correct side, match its piece's geometry, respect blockers,
use accurate capture/promotion/check markers, and leave its own king safe.
The computed result must match the record, and no move may follow a terminal
position. Illegal moves report their ply and fullmove number.

Maximum file size is **256 KiB** (262,144 UTF-8 bytes); maximum history is
**4,096 plies**. Only one main line is supported. Parenthesized variations
and multiple games in one file are rejected. Move numbers, when supplied,
must be correct; exports always supply standard numbering.

Brace comments `{text}`, semicolon comments through end of line, numeric
annotation glyphs such as `$1`, and trailing `!`/`?` annotations are accepted
and ignored. They are **not retained on re-export**. Nested or unclosed
comments are rejected. This is a game-move archive, not an annotated-study
editor or a variation tree.

The import worker uses the same rules and record code as the main app. A
local fallback is available when workers are blocked. Import does not access
the network. Canceling the import invalidates pending worker/file-read results.

## Developer API

With the standalone HTML loaded, `FourfoldRecords` exposes:

```javascript
const R = FourfoldRecords;
const start = R.standardStart();
const record = R.build(start, [{from: 0, to: 32}], {
  Event: 'My first W move', White: 'White', Black: 'Black'
});
const text = R.exportGame(record);
const restored = R.importGame(text); // throws an Error for invalid data
console.log(restored.entries[0].notation); // Ra1@00-a1@01
console.log(restored.states[1].board);     // board after the move
```

The internal square index is `x + 4*y + 16*z + 32*w`; X/Y/Z/W coordinates in
this expression are zero-based. `record.states[0]` is the starting position,
and state `n` is the position after `n` plies. Records include their initial
position, normalized moves, move-entry notation, states, metadata, and result.
Use the importer or `build()` to validate input rather than constructing a
record object from untrusted JSON.
