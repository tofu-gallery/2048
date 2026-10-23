# 2048 on Tofu

2048 is Gabriele Cirulli's sliding-tile puzzle. Move tiles with the arrow keys or swipe on a touch screen; matching numbers combine, with the goal of reaching 2048. The game runs entirely in the browser and retains the upstream name, design, favicon, and author credits.

## Hosting

- Framework preset: `static`.
- Site root: the repository root (`.`), containing `index.html`.
- Install command: none. There is no package manifest or dependency lockfile.
- Build command: none. Compiled CSS, JavaScript, fonts, and icons are already checked in.
- Output directory: `.`; serve the repository root unchanged.
- Start command for a local check: `python3 -I -m http.server 60836 --bind 127.0.0.1 --directory /private/tmp/gallery-batch2/2048` (substitute your own checkout path and an available port).
- Environment variables: none.
- Database: none.
- External services and accounts: none required. No analytics, advertising, or remotely loaded scripts or fonts are included. Attribution links are ordinary links that open only when selected.
- Persistence: the current game and best score live in this site's browser local storage. They do not sync between devices or accounts and can be lost when site data is cleared. If local storage is unavailable, the upstream game falls back to memory for the current page session.

The upstream `Rakefile` only updates an appcache timestamp on the separate `gh-pages` branch. It is not an installation or build step for this static source tree.

## Adaptations and provenance

- Upstream: https://github.com/gabrielecirulli/2048
- Upstream branch: `master`.
- Base upstream commit: `478b6ec346e3787f589e4af751378d06ded4cbbc`.
- Fork: https://github.com/tofu-gallery/2048
- `main` mirrors that upstream commit unchanged; `tofu` contains the adaptations.
- The verified adapted revision is tagged `pin/2026-10-23`.

The upstream `master` branch is the pure game, without the sharing and analytics additions on `gh-pages`. No tracking removal is needed. The only game-page change is to replace its claim that every hosted copy is the official website with an accurate self-hosted-copy notice and a link to the original. The game logic, styles, assets, and existing author credits are unchanged. This document supplies the deployment settings and records that narrow adaptation.

## Verification

The following local checks passed on 2026-10-23:

```sh
python3 -I -m http.server 60836 --bind 127.0.0.1 --directory /private/tmp/gallery-batch2/2048
curl --fail --silent --show-error --max-time 10 --output /dev/null --write-out 'GET / HTTP %{http_code}\n' http://127.0.0.1:60836/
```

The server served the root entry page and curl reported `GET / HTTP 200`. No installation or build is required for this prebuilt static tree.

```sh
for script in /private/tmp/gallery-batch2/2048/js/*.js; do node --check "$script" || exit 1; done
git -C /private/tmp/gallery-batch2/2048 diff --check
git -C /private/tmp/gallery-batch2/2048 diff --quiet upstream/master -- LICENSE.txt README.md favicon.ico meta style js
```

All JavaScript syntax checks passed, the diff has no whitespace errors, and the upstream license, README, icons, styles, fonts, and game scripts are byte-for-byte unchanged.

An additional local browser check used the gstack `/browse` skill with `aside repl`: arrow-key play produced merges and a score of 84; reloading restored the same tile values, positions, and score; New Game returned to two tiles and score 0. All 14 observed game resource requests stayed on the local origin and returned HTTP 200. Console errors were `[]`. At a 375-pixel mobile viewport, document width stayed 375 pixels and the board was 280 pixels wide. Desktop and mobile screenshots were inspected. The final check explicitly brought the test tab to the front because the background tab delayed animation-frame rendering. This is local verification; no deployment was made.

## License

MIT, copyright (c) 2014 Gabriele Cirulli. The upstream `LICENSE.txt` is preserved verbatim; include it with any distributed copy. All upstream copyright notices and attribution are retained.
