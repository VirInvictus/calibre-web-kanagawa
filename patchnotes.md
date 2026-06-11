# Patchnotes

## 0.4.0 (2026-06-11)

Phase 3: the library's reading_status, read-only, plus the hard read-only
guarantee.

- The fork now accepts enumeration custom columns as the linked read column
  and the instance links cc2 (`reading_status`). Enum branches added to both
  query builders (`generate_linked_query` AND `get_book_read_archived`; the
  second one was missed by the research map and found when the badge showed
  To Read on a Read book), joining through the normalized link table.
- Detail page shows a read-only 4-state badge (Read / Reading / To Read /
  DNF in Dragon colors) instead of the toggle checkbox; grid read-ticks
  accept the enum value; Read/Unread sections and advanced search project
  Read == 'Read'.
- Write-guard: the toggle endpoint refuses with "Read status is managed in
  Calibre and is read-only here" (HTTP 400).
- metadata.db is now attached `mode=ro` at both attach sites; the web app
  cannot write the library by construction.
- Verified on a scratch instance: badges exact for all three live statuses,
  read section paginates to exactly 149 books (matches SQL), checksum
  identical across a full browse session, validator at 0 errors.

## 0.3.0 (2026-06-11)

Phase 2: the feature surface trimmed to browse / search / read / download.

- New `cps/smallscope.py` in the fork: `trim()` installs before_request 404
  guards on unused blueprints ahead of registration, so `url_for` keeps
  resolving and the diff stays rebase-friendly. Disabled: tasks, shelf,
  editbook, remotelogin.
- Template removals: Tasks navbar item; the entire shelves sidebar section
  and detail-page shelf toolbar (the slot is reserved for Wings);
  send-to-eReader buttons and the per-user eReader email field; the Edit
  Metadata button; mass mark-read radios in the list view.
- Config baseline (spec §6.1) applied; only `config_embed_metadata` needed
  flipping, the rest were already off. Kobo per-user UI stays config-gated.
- Verified: trimmed routes 404, login renders themed, auth redirects
  intact, Jinja syntax pass on all edited templates.

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
