# 4D Chess · intuitionmachine.com

**Learn to play across four dimensions—not just across one board.**

[intuitionmachine.com](https://intuitionmachine.com) · [GitHub repository](https://github.com/codeaudit/4d-chess)

A browser-based learning edition of spatial 4D chess. Play against a computer or
another person on the same device, inspect either side's pieces, follow eight
interactive lessons, and explore a Checkmate Lab that explains why a position
is—or is not—mate.

The entire application is contained in **`index.html`**. No account, API key,
package installation, or build step is required to play. The game runs locally
in your browser, including its rules engine and computer opponent.

> **This is a specific 4 × 4 × 2 × 2 chess variant.** W is a fourth spatial
> direction, not time. There is no time travel or branching timeline, and these
> movement rules should not be assumed to describe every game called “4D chess.”

## Start playing

Download `index.html` and open it in a modern browser with JavaScript enabled.
The standalone file includes the styles, piece graphics, rules, lessons, and
computer player; it does not need a network connection to play. The links to
GitHub, intuitionmachine.com, and the original project require internet access.

1. Choose **Learn by playing** for a guided introduction, or **Rules** for a
   reference you can open at any time.
2. Click any piece, White or Black, to see its legal destinations across all
   four boards. Hover or focus a destination to read its explanation.
3. On your turn, select your piece and then a marked empty destination to move.
   To capture, select your piece, click the marked enemy piece, and use
   **Confirm capture**. Clicking an occupied square always inspects it first.
4. Choose **Inspect only** to pause play and the computer while exploring.
   Return to **Play** when you are ready to continue.
5. Open **Checkmate examples** to study the finishing positions separately from
   your game. **Return to game** restores the position you left.

In **Computer · you play White**, the computer plays Black. **Two players ·
local** lets both players use the same browser. There is no online multiplayer.

## Understanding the four boards

Every square has four coordinates: **[X, Y, Z, W]**.

| Coordinate | Meaning | Available values |
|---|---|---|
| X | File within a board | a–d; internally 0–3 |
| Y | Rank within a board | 1–4; internally 0–3 |
| Z | Which board column / layer | 0 or 1 |
| W | Which board row / cube | 0 or 1 |

The flat view arranges the boards like this:

```text
                     Z = 0              Z = 1
                ┌────────────────┬────────────────┐
        W = 1   │ 4 × 4 squares  │ 4 × 4 squares  │
                │ board [0,1]    │ board [1,1]    │
                ├────────────────┼────────────────┤
        W = 0   │ 4 × 4 squares  │ 4 × 4 squares  │
                │ board [0,0]    │ board [1,0]    │
                └────────────────┴────────────────┘
```

Together, they form **one connected playing space with 64 squares**. Moving
between displayed boards is an ordinary move along Z, W, or both—not a special
teleportation ability.

The app writes a square as **`b2 [0,1]`**: file b, rank 2, Z = 0, W = 1. Its
internal coordinate is `[1,1,0,1]`, because internal X and Y values start at zero.

The rotatable 3D projection shows the same position in another way. Its inner
and outer cubes represent W = 0 and W = 1. Both cubes are the same size in the
actual game; the visual scaling is just a projection. Lines that appear to
cross on screen do not intersect in the game unless their full coordinates
coincide.

## Movement rules

For every piece, the destination must be within the board. You cannot land on
a friendly piece, and a legal move must leave your own king out of check.

| Piece | Rule in this variant |
|---|---|
| **Rook** | Change exactly one coordinate: X, Y, Z, or W. Slide along that axis without passing through another piece. |
| **Bishop** | Change exactly two coordinates by equal distances, with an unobstructed path. X + Z and Z + W are diagonals too. |
| **Queen** | Change any nonempty combination of coordinates, with every changed coordinate moving the same absolute distance. The path must be clear. |
| **Knight** | Jump two steps on one axis and one step on a different axis. It ignores intervening pieces. The two-step part must use X or Y, because Z and W span only 0–1. |
| **King** | Change any nonempty combination of coordinates by at most one step each. The destination must be safe after the move or capture. |
| **Pawn** | Move one empty rank forward along Y: toward rank 4 for White, or rank 1 for Black. To capture, move one rank forward plus one step along exactly one of X, Z, or W. |

Changed coordinates may have different signs. For example, a bishop can change
X by +1 and W by −1: the distances are equal even though the directions differ.
The queen's three-axis and four-axis diagonals are important: in this variant,
her movement is broader than simply combining the rook's and bishop's moves.

Pawns automatically promote to queens on the final rank. There is **no opening
pawn double move, castling, en passant, or underpromotion**.

Each side starts with a king, a queen, two rooks, one bishop, one knight, and
four pawns. White moves first. Only knights can jump over intervening pieces.

### Can a piece capture on another plane?

**Yes.** All six piece types can capture across boards when their movement
pattern allows it. These examples assume an enemy piece at the destination
and that the moving side's king remains safe:

| Piece | Example capture | Coordinates that change |
|---|---|---|
| Rook | `a2 [0,0] → a2 [0,1]` | W +1 |
| Bishop | `b2 [0,0] → c2 [1,0]` | X +1, Z +1 |
| Knight | `a2 [0,0] → c2 [0,1]` | X +2, W +1 |
| White pawn | `b2 [0,0] → b3 [0,1]` | Y +1, W +1 |

The first example looks like a jump between boards, but it is a straight rook
move: only W changes. Protection also works across boards, so a distant-looking
piece may be defending a capture square.

### Can the king capture a piece that is checking it?

**Yes—when that piece is within the king's reach and the king is safe after the
capture.** Giving check does not make a piece immune to capture.

The engine removes the captured piece, vacates the king's old square, and then
checks the new position for attacks from every board. A capture is illegal if
another enemy piece still attacks the king, including a protector on another
plane or a newly exposed sliding attack.

In the **Queen net** study, the checking queen is protected by her king and
cannot be safely captured. In **Check ≠ mate**, the queen is unprotected and
the defending king can capture her. These examples are deliberately paired to
teach the distinction.

## Inspection, highlights, and the move coach

| Marker | Meaning |
|---|---|
| Green dot | A legal empty destination that you can currently play. |
| Red ring | A legal capture that you can currently play, after confirmation. |
| Blue diamond | A legal empty destination shown for inspection only. |
| Dashed blue ring | A legal capture shown for inspection only. |
| Amber square | The selected piece. |

Selecting an opponent's piece does **not** give it a turn. Its previews describe
moves it could legally make from the current position; they do not predict
which moves will still exist after your next move.

The move coach explains the selected piece's rule, shows exactly which
coordinates change, and explains rejected destinations. Reasons include a
blocked path, a friendly piece, an incorrect movement pattern, an empty pawn
capture square, or a move that exposes the king.

The optional **controlled-squares** overlay answers a different question from
legal-move previews: which squares does a piece attack or protect? A pawn
controls its capture diagonals even when they are empty. A pinned piece may
still control a square that an opposing king cannot enter, even though moving
the pinned piece would be illegal.

Right-click a square, long-press it on touch screens, or use **Shift + F10** on
a focused square to inspect attackers.

## Guided lessons

The eight lessons cover the fourth direction with a rook, cross-board bishop
diagonals, a knight's 2 + 1 jump, a queen's multi-axis movement, king safety,
pawn captures, promotion, and inspecting the opponent.

Lessons use their own positions rather than modifying your game. Returning
restores your saved game state. Completed lesson progress is saved locally
when browser storage is available.

## Checkmate Lab

Choose **Checkmate examples** in the top bar. The lab contains six mating
positions and two contrasting positions:

| Example | What it teaches |
|---|---|
| **Queen net** | A supported queen checks across X + Y + Z + W and covers the corner king's exits. |
| **Rook through W** | A straight checking line can run between W boards. |
| **Bishop diagonal** | A checking diagonal can combine X and W. |
| **Knight jump** | A knight's checking jump cannot be blocked. |
| **Promotion mate** | A Black pawn reaches rank 1, becomes a queen, and gives mate. |
| **Double check** | A bishop move uncovers a rook's attack while giving its own check; the demonstrated captures of either checker fail because the other still checks. |
| **Check ≠ mate** | The king has a safe capture of an unprotected checking queen. |
| **Stalemate ≠ mate** | No legal moves, but no check: the result is a draw. |

Use **Before / After** to compare positions, **Watch the move** to replay the
finish, and **Try the move** to play the specified move yourself. During a
practice step, other legal moves are not accepted as the exercise's solution.

The proof panel checks attacks on the king, king escapes, and legal defenses
by the other pieces. Select a checker to trace its attack, or select a listed
king neighbor to inspect the position after a hypothetical escape or capture.
The displayed attackers are recalculated after the attempted move; a captured
piece is not mistakenly counted as still attacking. The double-check example
also demonstrates two failed defensive captures.

These are **composed teaching positions for this variant**, not historical
games. Each displayed finish follows a legal move from its shown before-position.
This is not a claim that every composition has been proven reachable from the
standard starting setup.

Entering the lab pauses the computer and preserves your game or interrupted
lesson. Returning restores it; refreshing during a study restores the saved
real game rather than the study position, when storage is available.

## Check, checkmate, and draws

**Check** means the king is attacked. A legal response might move the king,
capture a checking piece, or block a sliding attack, but it must resolve every
attack on the king.

**Checkmate** means the side to move is in check and has **no legal move by any
of its pieces**. The engine does not merely check whether the king can move.
Kings are never captured.

The application automatically declares a draw for stalemate; the third
occurrence of the same piece placement with the same side to move; 100
half-moves without a pawn move or capture (50 moves per side); or a position
with only the two kings. These are the app's implemented draw rules, not a
claim to implement every conventional chess tournament rule.

## How the computer works

The engine has two separate jobs:

**Rules:** `canReach()` checks movement geometry and blockers. `legal()` tries
candidate moves on a copied board and rejects any that leave the mover's king
in check. `explain()` describes why a move is allowed or rejected. The board
highlights and computer player use this same rules engine.

**Move selection:** `computer()` examines its legal moves and the opponent's
immediate legal replies. It prefers the move with the best worst-case score,
using material, pawn advancement, X/Y central placement, and check-related
adjustments. An immediate mate receives a large score. A small random addition
varies near-equal choices; clearly inferior candidates may be cut off early.

This is a shallow **two-ply search**: the computer's move, then your reply. It is
not a trained model, does not learn from games, and does not call ChatGPT or an
external chess API. It can miss longer tactics. The UI normally runs the
search in a browser Web Worker and has a main-thread fallback.

## Controls and accessibility

| Control | Action |
|---|---|
| Tab | Move between boards and interface controls. |
| Enter / Space | Activate a control, select a piece, or choose a focused destination. |
| Arrow keys on a board | Move focus along X and Y. |
| Alt + Arrow keys | Move focus between boards along Z and W. |
| Shift + F10 on a square | Inspect its attackers. |
| Escape | Close a dialog or clear the current inspection/selection. |
| Drag in the 3D projection | Orbit the view. |
| Arrow keys in the focused 3D projection | Rotate the view; Shift + Left/Right rolls; Home resets. |

**Move history**, **New game**, and **Keyboard help** are in the footer. Undo
and the view-reset controls are in the game interface. The UI includes visible
focus indicators, shape-based move markers, selection announcements, and
support for the browser's reduced-motion preference. This is not a claim of
formal accessibility certification.

## Saving and privacy

Game moves, game-mode settings, and completed lessons are saved with browser
`localStorage` when available. There is no account, cloud sync, or server-side
game database. Clearing site data removes saved progress, and different
browsers or devices do not share it. A downloaded file and the hosted site may
also have separate storage; the app does not migrate saves between them.

If browser policy prevents saving, the app shows a warning and remains
playable for the current session. The game itself contains no analytics or
external runtime requests. Hosting providers and websites opened through its
external links have their own logging and privacy practices.

## Deploy with GitHub Pages

Keep `index.html` at the repository root. `README.md` explains the project, and
`LICENSE` contains the existing license; neither is a runtime dependency.

```text
4d-chess/
├── index.html    # The complete playable application
├── README.md     # This guide
└── LICENSE       # Existing MIT license
```

In the repository's **Settings → Pages**, set **Source** to **Deploy from a
branch**, choose **main** and **/ (root)**, and save. After a successful
publication, the default project address is:

```text
https://codeaudit.github.io/4d-chess/
```

Use the deployment status and **Visit site** link in Pages settings to confirm
the actual address. Replacing `index.html` and committing to the configured
publishing branch republishes the game. No custom application build workflow
is required for this standalone file. See GitHub's official
[publishing-source instructions](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)
and [Pages overview](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages).

The **intuitionmachine.com** identity is branding and an outbound link; it does
not configure DNS, a custom domain, or hosting at that domain. No `CNAME` file
is needed merely to display the brand.

## Development

For the minimal repository, edit `index.html` directly. Its inline `<style>`
block controls presentation. The inline JavaScript contains
`createFourfoldEngine()` for rules and move selection,
`createFourfoldStudies()` for the Checkmate Lab, and the application code for
lessons, interaction, state, and rendering.

The separate developer-source bundle, when supplied, contains modular source
files, `build.py`, and rule/browser tests. In that bundle, edit the modular
sources and run `python build.py`; it regenerates the standalone HTML. Do not
expect edits made only to generated HTML to survive a rebuild.

Before publishing changes, check both-color inspection, explicit capture
confirmation, legal king captures and rejected unsafe captures, cross-board
movement, promotion, every lesson and study, returning to an interrupted game,
and narrow-screen layouts. Changes to the engine should preserve the same
rules for play, highlights, explanations, and checkmate analysis.

## Credits and license

Branded for **[intuitionmachine.com](https://intuitionmachine.com)**.
Source repository: **[codeaudit/4d-chess](https://github.com/codeaudit/4d-chess)**.

The original UI and variant reference are credited to **0xmiki**:
[original 4D chess gist](https://gist.github.com/0xmiki/3b6fd1e545cc04bedd9e35d2d6736dd1).
The original attribution remains visible in the application footer.

The repository includes the **[MIT License](LICENSE)**, copyright © 2026
codeaudit. Keep that license and its notice with copies or substantial
portions of the software. Branding does not change the license terms.
