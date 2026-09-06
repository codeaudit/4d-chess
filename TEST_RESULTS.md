# Recording & replay edition — verification

Build date: 2026-09-06. Format: **PGN4D v1 / LAN4D** for Fourfold-4x4x2x2.

## Automated results

| Suite | Assertions | Result |
|---|---:|---|
| `engine.test.js` | 64,868 | PASS |
| `checkmates.test.js` | 3,123 | PASS |
| `records.test.js` | 913 | PASS |
| **Rules, studies, and records total** | **68,904** | **PASS** |
| `browser.test.py` | 189 | PASS |
| `checkmates.browser.test.py` | 1,086 | PASS |
| `branding.browser.test.py` | 289 | PASS |
| `records.browser.test.py` | 217 | PASS |
| **Chromium interface total** | **1,781** | **PASS** |

These are counted assertions, not distinct test cases or a guarantee that all
possible positions and browser configurations have been covered.

## Recording and notation coverage

All 64 square addresses round-trip. Tests serialize/deserialize every legal
opening move, every study's finishing position, all legal move tokens in the
study starting positions, and 24 deterministically randomized game sequences.
Every prefix of the 18-ply sample is reconstructed and can be exported/imported.

Additional checks cover custom FEN4D starts, Black-first records, non-default
fullmove numbering, safe king captures, cross-plane movement, promotion,
capture-promotion with check, all six checkmates, stalemate, threefold repetition,
and the 100-halfmove draw. Record headers and canonical movetext survive exact
round-trips, including quotes, backslashes, and Unicode metadata.

Rejected input includes ordinary 2D PGN, unsupported variants/versions, illegal
moves, wrong turn, incorrect piece letters, wrong capture/check markers, false
results, moves after a terminal position, missing/inconsistent result markers,
invalid starting positions, oversized files/move lists, duplicate headers,
multiple games, and unsupported variations. Comments and annotation handling
are checked against the documented lossy behavior.

## Interface coverage

The integration suite uses the actual standalone HTML, file inputs, UTF-8
file decoding, Blob downloads, import workers and the worker-disabled fallback,
scoresheet buttons, replay transport, slider, metadata fields, and dialogs.
The download bytes match the displayed record text. Additional smoke checks
observed completed import-worker messages and successful Copy feedback on
desktop and mobile-sized viewports.

Tests verify that imports and replay do not replace or write over the live
record; invalid and canceled imports are non-mutating; export at replay cursor
zero still includes the complete game; all 19 sample positions reconstruct
correctly; captures/promotions change the actual board; autoplay stops at the
end; pause/seek cancel timers and animation; pending AI is canceled during
replay and resumes appropriately on return; and either side remains inspectable
without permitting a board move.

Continuation requires confirmation. The retained prefix becomes a paused local
game; later replay moves are not copied. New moves append correctly, undo works,
custom starts restore, and existing version-2 saves migrate with their history.
Long metadata cannot force horizontal overflow, and choosing an invalid new
file cannot leave an older record silently loaded in the import text field.

Responsive checks cover widths 320, 390, 768, 1024, and 1440 for new controls;
the original regression suites cover additional widths. Existing lessons,
all eight Checkmate Lab studies, 120 king-escape probes, capture confirmation,
keyboard inspection, branding links, and projection interactions are retained.
No uncaught browser exceptions were reported by the passing suites.

## Limits of verification

Environment: Node.js 22.16.0, Python 3.13, Playwright, and Chromium
144.0.7559.96 on Linux. Safari, Firefox, real phones, and OS-level file/clipboard
integrations on other platforms were not tested.

This environment blocks navigation to HTTP and file URLs. The browser harness
therefore uses `set_content` and an in-memory Storage shim. It exercises
save/restore and migration logic through app reinitialization, **not native
localStorage persistence across real browser navigation or reopening a file**.
Blob downloads and file input decoding were exercised directly. On a normal
browser origin, the app uses native localStorage and reports a warning if it
is unavailable.

No GitHub commit, Pages deployment, DNS change, or live-site verification was
performed. No formal security or accessibility certification is claimed.

## Build integrity

`engine.js`, `checkmates.js`, and `LICENSE` are byte-for-byte unchanged from the
previous intuitionmachine-branded bundle. Recording logic is isolated in
`records.js`; UI integration, styles, and documentation are updated. Both
standalone HTML aliases are generated from the same modular sources. The
standalone page has no duplicate HTML IDs and no external script dependencies.

SHA-256 of `index.html`:

```text
aa333333f245b149f3dcce5759837af646acfdba39b9959440f9c909572d517e
```

To reproduce the suites, use the commands in the README from the developer
source bundle. `test-logs/` in that bundle contains the captured passing logs.
