# Roadmap

Phases for calibre-web-kanagawa and the `smallscope` fork branch. Tick boxes
when shipping; details and rationale live in `spec.md`.

## Phase 0: Scaffold (in flight)

- [x] Clone `calibre-web-kanagawa` and `calibre-web-smallscope` into `~/.gitrepos/`
- [x] Fork: add `upstream` remote (janeczku/calibre-web), fetch tags, branch
      `smallscope` from tag `0.6.26` (matches the installed release)
- [x] Project framework: README, spec.md, roadmap.md, patchnotes.md, CLAUDE.md,
      .gitignore, LICENSE (GPL-3.0), logo.svg, VERSION, justfile, theme/ stub
- [x] Fork CLAUDE.md (brief; points here for the contract)
- [x] Add both repos to `~/.gitrepos/CLAUDE.md` inventory
- [x] Venv swap: `pip uninstall calibreweb`; fork runs from source
      (`just serve`; the 0.6.26 tree has no src/ layout, editable install is
      not possible). Settings carry over via `CALIBRE_DBPATH=~/.calibre-web`
- [x] Baseline: unmodified fork serves on :8083 with existing settings
      (login page 200, Tornado start clean)
- [ ] Manual baseline pass by Brandon (browse, detail, search, EPUB read)
- [ ] Initial commits in both repos (messages reviewed before committing)

## Phase 1: Kanagawa Dragon theme

- [ ] Extract caliBlur color inventory (hex frequency pass over
      `caliBlur.css`, `style.css`, `main.css`) and finalize the role mapping
      table in spec.md §4.3
- [ ] `theme/kanagawa-dragon.css`: `:root` palette + caliBlur variable
      overrides
- [ ] Surface pass: navbar, sidebar, book grid (cover-forward treatment:
      spacing, radius, shadow, hover lift), pagination, footer
- [ ] Detail page pass: cover presentation, metadata block, custom-column
      rows, buttons
- [ ] Forms/admin/login/search pass (low-traffic pages still themed)
- [ ] `layout.html`: add the single stylesheet link after
      `caliBlur_override.css`
- [ ] `just sync-theme` vendoring works; vendored copy committed in the fork
- [ ] logo.svg finalized; regenerate `favicon.ico` / `icon.svg` / `icon.png`
- [ ] Verify across pages listed in spec §10.1 plus mobile widths
      (caliBlur's `--color-background-mobile` path)

## Phase 2: Trim the feature surface

- [ ] Apply and document the config baseline (spec §6.1) in the admin UI
- [ ] Patch: tasks page + navbar link removed, routes disabled
- [ ] Patch: shelves UI removed (sidebar section, create/edit), routes disabled
- [ ] Patch: send-to-eReader button, SMTP/email config UI removed
- [ ] Patch: Kindle/Kobo per-user fields removed from user_edit
- [ ] Patch: upload + web metadata-editing entry points removed, editbooks
      routes disabled
- [ ] Patch: mass mark-read buttons removed from book_table
- [ ] Patch: registration / magic-link / Goodreads remnants removed
- [ ] Two accounts confirmed (Brandon, Rin), both unrestricted
- [ ] Verify: stripped elements gone, disabled routes 404, core browse /
      read / download flows intact

## Phase 3: Read-only reading_status + read-only hardening

- [ ] `admin.py`: accept enumeration columns for `config_read_column`
      (dropdown filter + `check_valid_read_column`)
- [ ] `db.py generate_linked_query`: enum branch via the normalized link
      table (idiom: restricted-column filter at db.py:786-809)
- [ ] `web.py:1644`: enum projection (`== 'Read'`) + raw label for the badge
- [ ] `web.py:747-749` and `search.py:145-147`: Read/Unread filters for enum
- [ ] `helper.py edit_book_read_status`: write-guard; toggle endpoint refuses
      on enum columns
- [ ] `detail.html`: 4-state badge (Read/Reading/To Read/DNF, spec §5.2
      colors) replaces the checkbox
- [ ] Harden: attach `metadata.db` read-only (`file:...?mode=ro`) in `db.py`
- [ ] Link cc2 in config; verify badge + section counts against cquarry;
      checksum metadata.db before/after a browse session

## Phase 4: Wings

- [ ] cquarry installed editable into the venv; importability of the search
      engine confirmed (else: agree an API addition with CalibreQuarry first)
- [ ] `cps/wings.py`: read `preferences.virtual_libraries`, evaluate via
      cquarry, mtime-keyed cache
- [ ] Sidebar "Wings" section with counts; `/wings/<name>` route renders the
      standard grid filtered by id set
- [ ] Unsorted wing handled (vl: references resolve; empty wing displays sanely)
- [ ] Verify: web counts match `cquarry --wings` for all 32 entries

## Phase 5: EPUB reader theme (stretch)

- [ ] "Kanagawa" entry in `epub_themes.css` + `read.html` theme selector
- [ ] Reader verified with a known EPUB in light/dark rooms

## Phase 6: Tests and close-out

- [ ] `tests/` in the fork: fixture mini metadata.db (bool + enum custom
      columns, preferences row with wing expressions)
- [ ] Unit: enum projection, Read/Unread filters, write-guard, wing
      evaluation + cache invalidation
- [ ] Smoke (Flask test client): detail badge renders, `/wings/<name>`
      renders, disabled routes 404
- [ ] Full verification pass (spec §10); `validate_library.py` 0 errors
- [ ] roadmap boxes ticked, patchnotes entry, spec.md synced
- [ ] VERSION 1.0.0 when the instance is daily-driver ready

## Later / opportunistic

- [ ] Offer enum read-column support upstream (it is generally useful)
- [ ] Homelab deployment notes (September 2026 build): serve from Rin's
      machine against the mirrored library
- [ ] Library-graduation check-in on cquarry's search engine if the fork's
      consumption deepens
