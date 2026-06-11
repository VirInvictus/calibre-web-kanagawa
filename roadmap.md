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

- [x] caliBlur color inventory + role mapping (spec §4.3); implemented as a
      regenerable mechanical pass: `scripts/recolor_caliblur.py` parses
      caliBlur.css + caliBlur_override.css and rewrites the marked block in
      the theme sheet (~176 rules; media-query context and !important
      preserved, @keyframes replaced whole). 16 unit tests
- [x] `theme/kanagawa-dragon.css`: `:root` palette + caliBlur variable
      overrides + generated recolor block + hand polish layer
- [x] Cover-forward polish: warm headings (oldWhite), cover radius/shadow/
      hover lift, quiet authors/series, subdued read badge, dragonYellow
      stars, warm links/buttons/focus ring, status-badge classes for Phase 3
- [x] `layout.html`: stylesheet link added after `caliBlur_override.css`
- [x] `just sync-theme` vendoring works; vendored copy in the fork
- [x] logo.svg first draft; `favicon.ico` / `icon.svg` / `icon.png`
      regenerated from it; `config_theme` flipped to caliBlur base
- [ ] Brandon's visual pass (desktop + mobile widths), logo verdict, and
      per-page touch-ups that fall out of it (the generated recolor is
      mechanical; expect a polish iteration)

## Phase 2: Trim the feature surface

- [x] Config baseline applied (spec §6.1): everything was already off except
      `config_embed_metadata`, now 0
- [x] Patch: tasks navbar item removed; tasks blueprint answers 404
- [x] Patch: shelves UI removed (sidebar section, create-shelf, detail-page
      add/remove toolbar); shelf blueprint answers 404. Sidebar slot reserved
      for Wings (Phase 4)
- [x] Patch: send-to-eReader buttons removed from detail page; per-user
      eReader email field removed from user_edit (SMTP admin pane left in
      place: admin-only, inert without recipients; revisit if it grates)
- [x] Kobo per-user fields stay config-gated (invisible with sync off);
      no patch needed
- [x] Patch: Edit Metadata button removed from detail page; editbook
      blueprint (edit/upload/convert ajax) answers 404; uploads also off in
      config
- [x] Patch: mass mark-read buttons removed from book_table
- [x] Registration / magic-link / Goodreads: config-off, and the remotelogin
      blueprint answers 404 (admin config panes left as-is: admin-only)
- [x] Route trimming implemented as `cps/smallscope.py` `trim()`:
      before_request 404 guards installed pre-registration, so `url_for`
      keeps resolving everywhere (rebase-friendly)
- [ ] Rin's account created (Brandon: admin UI, needs a password chosen)
- [x] Verify: /tasks, /shelf/*, /admin/book/* return 404; /login renders
      200 themed; auth redirects intact; Jinja syntax pass on all four
      edited templates
- [ ] Brandon's browse pass over the trimmed UI

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
