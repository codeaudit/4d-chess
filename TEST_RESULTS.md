# Verification — intuitionmachine.com branding and README

## Changes

- Added linked intuitionmachine.com identity to the application header and footer.
- Added the codeaudit/4d-chess repository link to the header and footer.
- Updated the browser title and description metadata.
- Kept the original 0xmiki footer attribution.
- Added responsive styling for the new links and branding.
- Wrote a standalone explanatory README covering the variant, rules, cross-plane
  captures, legal king captures, inspection, lessons, checkmate examples,
  computer search, controls, saving, deployment, development, and licensing.
- Updated the modular HTML/CSS/build sources so rebuilding preserves the branding.

## Base revision and unchanged logic

The published index.html retrieved from codeaudit/4d-chess had Git blob SHA
`a4d8f32f2ce01958336d414b477db70cf2efd24e`. It matches the previously supplied
Checkmate Lab file with one leading and one trailing newline. The update uses
that Checkmate Lab version, not an older learning-only version.

The complete inline JavaScript in the updated standalone HTML was compared with
the prior standalone HTML and is **identical**. Every pre-existing HTML ID was
retained, with no duplicate IDs. No external script, stylesheet, image, or font
dependency was added.

The MIT LICENSE is preserved byte-for-byte from the repository, with Git blob
SHA `fe13841b4af0d0a7d774730cbd7cb3851c0cca6b`.

## Tests run on this update

| Suite | Result |
|---|---:|
| `node engine.test.js` | 64,868 rule assertions passed |
| `node checkmates.test.js` | 3,123 checkmate-study assertions passed |
| `python browser.test.py` | 189 browser assertions passed |
| `python checkmates.browser.test.py` | 1,086 checkmate browser assertions passed |
| `python branding.browser.test.py` | 289 branding/browser assertions passed |

Totals: **67,991 rule/study assertions** and **1,564 browser assertions**.
No uncaught browser exceptions occurred in the passing suites.

The existing suites cover both-color inspection, legal-move previews, explicit
capture confirmation, lessons, saved-state handling, all eight checkmate
studies, all 120 neighboring-king probes, both demonstrated double-check
defenses, and responsive UI behavior.

The new branding suite checks exact brand/repository URLs, retained original
attribution, new-tab attributes, noopener/noreferrer, accessible new-tab labels,
keyboard focus, and non-mutating anchor clicks. It also checks visible,
unclipped, non-overlapping header groups in game, lesson, and checkmate views at
320, 390, 768, 1024, 1100, and 1440 CSS pixels. No application network requests
were observed during the branding test.

Desktop and phone screenshots were produced from the updated standalone HTML.
The new navigation and footer were visually inspected. Screenshots use a
separate test state, not a modification to the app's default starting position.

## Scope and limitations

Tests ran with Node.js and Chromium through Python Playwright. Safari and
Firefox were not tested, and this is not formal accessibility certification.

The test environment's browser policy blocks normal navigation, including the
attempted locally intercepted test origin. Passing tests therefore render the
actual standalone HTML with Playwright's set_content and an in-memory Storage
shim. External anchor clicks are recorded and prevented by the test harness;
these checks validate destinations, target/rel attributes, and preserved game
state, **not live external-page availability or a completed popup navigation**.
No attempt was made to bypass the browser policy.

The existing composed checkmate examples retain their prior scope: their shown
finishing move and result are verified, but reachability of each composition
from the opening position is not claimed.

No repository commits, GitHub Pages settings, DNS, custom domains, or hosted
files were changed. The deliverables are local files ready for the user to upload.

## Application checksum

SHA-256 of the delivered `index.html`:

```text
fc9f74d4cc38bd3d065a2a13fda834eee129bce1b60ced8ea2abd4cc809d4ddd
```
