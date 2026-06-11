# Patchnotes

## 0.2.0 (2026-06-11)

Phase 1: the Kanagawa Dragon theme, live on the instance.

- `scripts/recolor_caliblur.py`: stdlib generator that parses caliBlur.css
  (+ caliBlur_override.css) and rewrites the marked recolor block in the
  theme sheet, mapping every hardcoded caliBlur color to its Dragon
  equivalent by role (spec §4.3). ~176 rules; media-query nesting and
  !important flags preserved; @keyframes recolored whole. 16 unit tests
  (`just test-theme`); regenerate with `just regen` after upstream caliBlur
  changes.
- Hand polish layer in `theme/kanagawa-dragon.css`: warm oldWhite headings,
  cover-forward grid treatment (radius, shadow, hover lift), quiet
  author/series text, subdued read badge, dragonYellow rating stars, warm
  links/buttons/focus ring, and `.kngw-status-*` badge classes ready for
  the Phase 3 reading-status display.
- Fork wiring: `kanagawa-dragon.css` linked after `caliBlur_override.css`
  in `layout.html` (vendored via `just sync-theme`); favicon/icon assets
  regenerated from `logo.svg`; instance flipped to the caliBlur base theme
  (`config_theme = 1`).
- justfile: new `regen` and `test-theme` tasks.

## 0.1.0 (2026-06-11)

Project scaffold.

- Repos established: `calibre-web-kanagawa` (project home) and
  `calibre-web-smallscope` (fork of janeczku/calibre-web; branch
  `smallscope` cut from tag 0.6.26, upstream remote wired).
- Documentation framework: README, spec.md (full contract: palette and
  caliBlur mapping, feature-removal lists, read-only reading_status
  semantics, metadata.db mode=ro guarantee, Wings design, testing
  contract), roadmap.md (Phases 0-6), CLAUDE.md, .gitignore, GPL-3.0
  LICENSE, logo.svg, VERSION, justfile.
- Theme stub: `theme/kanagawa-dragon.css` with the pinned Dragon palette
  as CSS custom properties (palette sourced from kanagawa-dragon-nvim-emacs).
- No fork code changes yet; baseline is stock 0.6.26.
