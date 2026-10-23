# 2048 on Tofu

2048 is Gabriele Cirulli's sliding-tile puzzle. Move tiles with the arrow keys or swipe on a touch screen; matching numbers combine, with the goal of reaching 2048. The game runs entirely in the browser and retains the upstream name, design, favicon, and author credits.

## Hosting

- Framework preset: `static`.
- Site root: the repository root (`.`), containing `index.html`.
- Install command: none. There is no package manifest or dependency lockfile.
- Build command: none. Compiled CSS, JavaScript, fonts, and icons are already checked in.
- Output directory: `.`; serve the repository root unchanged.
- Start command for a local check: `python3 -I -m http.server 61848 --bind 127.0.0.1 --directory /private/tmp/gallery-fix8/2048` (substitute your own checkout path and an available port).
- Environment variables: none.
- Database: none.
- External services and accounts: none required. No analytics, advertising, or remotely loaded scripts or fonts are included. Attribution links are ordinary links that open only when selected.
- Persistence: the current game and best score live in this site's browser local storage. They do not sync between devices or accounts and can be lost when site data is cleared. If local storage is unavailable, the upstream game falls back to memory for the current page session.

## Adaptations and provenance

- Upstream: https://github.com/gabrielecirulli/2048
- Upstream branch: `master`.
- Base upstream commit: `478b6ec346e3787f589e4af751378d06ded4cbbc`.
- Fork: https://github.com/tofu-gallery/2048
- `main` mirrors that upstream commit unchanged; `tofu` contains the adaptations.
- Verified adapted revisions are tagged `pin/2026-10-23` (the first adaptation) and `pin/2026-10-23.1` (the current one, which also removes the files Tofu's static hosting refuses).

The upstream `master` branch is the pure game, without the sharing and analytics additions on `gh-pages`. No tracking removal is needed. The only game-page change is to replace its claim that every hosted copy is the official website with an accurate self-hosted-copy notice and a link to the original. The game logic, compiled stylesheet, scripts, images, and existing author credits are unchanged. This document supplies the deployment settings and records these narrow adaptations.

### Files removed for Tofu's static hosting

Tofu's Free plan publishes a finished site only when every file in it is a type its static hosting serves; one file of another type, or one that looks like build configuration, stops the whole site. Six upstream files failed that test, and the game page loads none of them, so they are removed on `tofu`:

- `Rakefile`: a Ruby task with no file extension that rewrites an appcache timestamp on the separate `gh-pages` branch. It is not an install or build step for this tree.
- `style/main.scss` and `style/helpers.scss`: the Sass sources of `style/main.css`. The page loads only the compiled `style/main.css`, which is checked in and unchanged. (`helpers.scss` credits its exponent function to Team-Sass/Sassy-math.)
- `style/fonts/ClearSans-Light-webfont.eot`, `style/fonts/ClearSans-Regular-webfont.eot` and `style/fonts/ClearSans-Bold-webfont.eot`: Embedded OpenType copies of the Clear Sans font that only old versions of Internet Explorer use. Current browsers load the `.woff` copies, which are kept, as are the `.svg` copies.

`style/fonts/clear-sans.css` no longer lists the three `.eot` files, so nothing points at a missing file; its `.svg` and `.woff` sources, their order, and the font weights are unchanged. All six files remain on `main` and upstream. To change the styles as `CONTRIBUTING.md` describes, edit the SCSS there, compile it, and copy the resulting `style/main.css` here. No licence or notice file was removed: `LICENSE.txt` is unchanged and is published with the site.

## Verification

The following checks passed on 2026-10-23 for the tree tagged `pin/2026-10-23.1`.

Tofu's acceptance check imports a ref the way Tofu's GitHub import does and runs Tofu's own static-hosting and safety predicates (run from a Tofu checkout's `apps/mcp-server`):

```sh
pnpm exec tsx /private/tmp/ikb/lane_check.mts --one tofu-gallery/2048 <branch-or-tag> ''
```

Before this change the `tofu` branch printed `-> not on the static lane | refused: type×6` (the six files above). The same import and predicates over this tree, run on a local `git archive` of the commit before it was pushed, printed `-> static (finished site)` with nothing refused and `safety ok (0 findings)`: 29 files are imported and 24 published; `README.md`, `CONTRIBUTING.md`, `TOFU.md`, `.gitignore`, and `.jshintrc` are skipped, not refused.

```sh
rsync -a --exclude node_modules --exclude dist --exclude build --exclude target --exclude .cache --exclude .git --exclude .tofu-scratch ./ /private/tmp/gallery-fix8/2048/.tofu-scratch/2048-imported/
python3 -I -m http.server 61848 --bind 127.0.0.1 --directory /private/tmp/gallery-fix8/2048/.tofu-scratch/2048-imported
```

That directory is a copy of this tree without the folders Tofu's import drops. A browser check against it with the gstack `/browse` skill (`aside repl`): twenty arrow-key moves merged tiles to a score of 84, and New Game returned to two tiles and score 0 with the best score of 84 kept. The page and all 15 resources it requested (`style/main.css`, the ten scripts, `style/fonts/clear-sans.css`, the Regular and Bold `.woff` fonts, and `favicon.ico`) stayed on the local origin and returned HTTP 200; no `.eot` or `.svg` font was requested, and the normal and bold Clear Sans faces loaded. Console errors were `[]`. The screenshots are tinted by the browser profile's extension styling, so they are not clean visual acceptance evidence. The first adaptation (`pin/2026-10-23`) also passed a reload check (the saved game and score came back) and a 375-pixel mobile layout check; this change touches neither the scripts nor the compiled stylesheet those depend on. This is local verification; no deployment was made.

```sh
for script in js/*.js; do node --check "$script" || exit 1; done
git diff --check origin/main
git diff --quiet origin/main -- LICENSE.txt README.md CONTRIBUTING.md favicon.ico meta js style/main.css style/fonts/*.woff style/fonts/*.svg
```

All JavaScript syntax checks passed, the changes from `main` have no whitespace errors, and the license, README, contributing guide, icons, compiled stylesheet, `.woff` and `.svg` fonts, and game scripts are byte-for-byte identical to `main`. The only other differences from `main` are the self-hosted-copy notice in `index.html`, the `.eot` entries removed from `clear-sans.css`, the six removed files, and this document.

## License

MIT, copyright (c) 2014 Gabriele Cirulli. The upstream `LICENSE.txt` is preserved verbatim; include it with any distributed copy. All upstream copyright notices and attribution are retained.
