# TablePlus parity

The goal: everything TablePlus 27.0.2 does, feature for feature. Built from the app bundle (menus, settings, every screen's labels) and the earlier internals report. `Ref:` names the TablePlus screen to look at when building an item.

`[x]` done · `[~]` partly done · `[ ]` not started

## 1. Launcher and connections

- [x] Launcher window with a searchable connection list
- [x] New connection: engine picker, then a form for that engine
- [x] Test, Save, Connect buttons
- [x] Passwords kept in the OS keychain, never in the connections file
- [x] Tag on each connection (local / development / staging / production)
- [x] Password storage choice: Store in keychain / Ask every time / No password (Ref: `PostgreSQLForm`). "Ask every time" asks at connect and keeps the password in memory until the workspace closes. Not yet for the SQLite passphrase; a Safe-level connection that asks every time has no saved password to check against
- [x] Status color for each connection, five swatches, on the launcher row and the workspace header (Ref: `ConnectionPartNameController`)
- [x] Groups: create, rename and delete groups, with an icon each; drag connections onto a group heading, or right-click › Move to (Ref: `CreateGroupWindow`). Stored in `connections.json`, which is now `{ connections, groups, tags }` (builds from before this can't read it; the old array still loads). Not yet: nested groups, manual ordering
- [x] Tag manager with custom tags and colors, launcher ⋯ › Manage Tags (Ref: `TagManagerWindow`)
- [x] Sort connections by name, driver or tag
- [x] Duplicate a connection (with its keychain secrets), Copy as URL (never the password)
- [x] Import a connection from a URL, e.g. `postgresql://user@localhost/db`, or a SQLite path; opens the form filled in (Ref: `ImportConnectionURLWindow`)
- [x] Import and export connections as a file: all, one group or one connection, password-protected (AES-256-GCM, argon2id key), with an option to include passwords, which only go into a protected file (Ref: `ExportConnectionWindow`). Name clashes on import get a free name
- [~] Open Recent list: File › Open Recent and a Recent section on the launcher (done). Still missing: open a connection in a new window (Ref: `ConnectionWindow`)
- [x] Reopen the workspaces open at quit, at startup (Settings › General, off by default)
- [~] Open by URL scheme: `postgresql://`, `postgres://`, `mysql://`, `mariadb://` links (`Info.plist`) open the new-connection form filled in. Not tried in a bundled app yet; macOS only (other platforms need the deep-link plugin)
- [~] Open `.sqlite`/`.db` files from Finder (file associations, rank Alternate): the form opens filled in. Not tried in a bundled app yet. Still missing: TablePlus connection files

## 2. Connection transport

- [x] **SSH tunnel**: server, port, user, password or private key (with key password), "use ~/.ssh/config when the key is empty" (Ref: `ConnectionPartSSHController`). Backend tested end to end with `scripts/test-ssh.sh`; the form is not yet tried in the app window. Not yet: ProxyJump/ProxyCommand, host certificates, a file picker for the key
- [x] SSL: Disable / Prefer / Require / Verify CA / Verify full for every engine, with libpq semantics (Prefer and Require encrypt without checking the certificate), CA, client cert and key files with file pickers; TLS is checked against the real host name through SSH tunnels (Ref: `PostgreSQLForm`, `MySQLForm`)
- [x] Postgres options: direct TLS negotiation, show system schemas, disable channel binding (More options in the form)
- [x] MySQL options: socket instead of host (not yet tried against a real socket), SNI name, cleartext auth plugin
- [x] MySQL `LOAD DATA LOCAL INFILE`: the server picks which local file the client sends, so a blanket switch lets any server read your files. A statement that starts with `LOAD DATA … LOCAL INFILE '<file>'` sends that file and only that file, once, whatever the server asks for; any other request is refused
- Not needed: TablePlus's MySQL "dialect" picks between its bundled 5.7 and 9.7 client libraries; the pure-Rust driver talks to old and new servers alike
- [x] SQLite: open SQLCipher-encrypted files with a passphrase (or raw `x'…'` key) kept in the keychain; uses CommonCrypto on macOS, the system libcrypto on Linux
- [x] Bootstrap commands: SQL run after connecting (and on every reopen), and a shell script run before connecting and before any SSH tunnel (Ref: `StartupQueryPopover`)
- [x] Keep connection alive: ping every 30 s (Settings › General); a connection that stops answering shows the reconnect banner
- [x] Query timeout (Settings › General, default 300 s): the query is stopped on the server (Postgres cancel, MySQL `KILL QUERY`, SQLite interrupt), and the connection stays usable
- [x] Reconnect (⌘⇧R), with a banner when the connection drops; tabs stay open and the database in use is re-selected. Returning to a workspace whose connection died reopens it quietly
- [x] Connection info popover (click the status pill): server version, driver, TLS cipher, SSH host, reconnect, disconnect (Ref: `ConnectionInfoPopover`)

## 3. Workspace window

- [x] Tabs: table tabs and SQL tabs, ⌘T new SQL tab, ⌘W closes a tab; after the last one the workspace stays connected with no tab open, and ⌘W again closes the connection
- [x] The sidebar's ‹ Connections opens the launcher as a New workspace tab (like ⌘N); the workspace stays connected in its tab
- [x] Open a database (⌘K), and connect without picking a database first
- [x] Status pill showing the connection and server version
- [x] Rename tab (double-click or right-click it), close other tabs, reopen closed tab (⌘⇧T), select tab 1–9 (⌘1…⌘9, 9 is the last), next and previous tab (⌘⇧] / ⌘⇧[, ⌃Tab / ⌃⇧Tab)
- [~] Split pane right (⌘⇧D), and next and previous pane (⌘⌥] / ⌘⌥[); each pane has its own tabs, Back/Forward and closed tabs (done). Still missing: only the focused pane is live; the others show a still copy of their last frame until clicked (the grid and editor draw into one place)
- [x] Multiple workspaces (connections) in one window: click the connection name for the switcher (Show All Workspaces), next and previous workspace (⌃⌘] / ⌃⌘[), Reconnect All Workspaces; a lost connection in the background shows on the switcher
- [x] Workspace tabs: every open workspace (a connection, or another database of one) gets a tab in a strip across the top of the window, shown as soon as one is open, so switching is one click. ⌘K › Open on another database opens it in a new tab (or goes to its tab when it's open already); Open Here (⌥↩) switches the current tab instead. Each tab has its own session, sidebar and item tabs, shows the connection's tag color and the database name, has a lost-connection mark and closes with its × (or middle-click); drag to reorder. Every tab keeps its database's SQL tabs across launches
- [x] New workspace (⌘N, or + in the tab strip): the launcher shows as a "New workspace" tab beside the open ones, like a browser's new tab; connecting fills it, × goes back. A separate window isn't needed with the tabs
- [x] Open Anything (⌘P): fuzzy search across tables, views, materialized views, functions, sequences, types, SQL tabs, other workspaces and commands, with arrow keys and Enter (Ref: `OpenAnythingController`). Saved queries join when section 12 has them
- [x] Toolbar: connection, database, safe-mode level and console log in the header, plus icon buttons for opening a SQL file, reload, row detail (pressed while on), the metrics board and user management (not on SQLite); each runs its command (Ref: `WorkspaceWindow`)
- [x] Toggle the left sidebar (⌥⌘1), the row detail sidebar (⌥⌘2), the console (⇧⌘C) and the results pane (⌥⌘3, a SQL tab's editor takes the pane), in View; the sidebar and results choices are kept
- [x] Reload current tab (⌘R), reload workspace (⌘⌥R)
- [x] Go back and forward through opened items (⌃⌘← / ⌃⌘→)
- [x] Database window (⌘K): create (encoding/character set and collation), rename and drop databases on Postgres and MySQL/MariaDB, each through the SQL preview and safe mode; open in the current tab or a new one (Ref: `DatabaseWindow`, `NewDatabaseWindow`). MySQL has no RENAME DATABASE: its tables move to a new database and the old one is dropped, refused when it has views, routines, triggers or events. Postgres can't rename or drop the database a session is on (the server says so)
- [x] Notifications for finished background jobs (exports, imports, backups, restores): every job's card stacks in one panel at the bottom right and stays until dismissed; the header's Jobs button (with a count of unseen ones) or View › Notifications lists this session's finished jobs, with Show in Finder. Not kept across launches
- [x] Resizable panes: drag the sidebar's right edge, the SQL editor's bottom edge and the console's top edge (`splitters.ts`). Clamped (sidebar 180–560px and at most half the window, editor and console leave the results room), double-click or Home resets, focus a divider and use the arrow keys (16px, Shift 64px); sizes kept in settings across launches. Not yet: the row detail sidebar's width

## 4. Left sidebar

- [x] Table list with a name filter
- [x] Expand a table to see its columns, by position or A–Z (right-click the column list, or the sidebar)
- [x] Sections for tables, views, materialized views, functions and procedures, sequences and user-defined types, collapsible, each switchable on or off (right-click the sidebar); `catalog.rs`. Recently opened items show in a Recent section (the last 8 per connection; right-click its heading to clear, or switch it off)
- [~] Schema switcher on Postgres (one schema or all, kept per connection); system schemas follow the connection's "Show system schemas" option. Still missing: lazy-loading large schemas
- [x] Item groups by name rules (starts with, ends with, contains, is), right-click the sidebar for New group, a group's heading to edit or delete it (Ref: `ItemGroupWindow`, `FilterRuleController`). Tables can also be added by hand (right-click › Add to group), and a group with no text holds only those
- [x] Pin items to the top (right-click an item)
- [x] Item context menu: open in new tab, open structure, item overview, show diagram, copy name, Copy Script As (CREATE, DROP, SELECT, INSERT, UPDATE, DELETE for tables and views; CREATE and DROP for functions, procedures, sequences and materialized views; built in `objects.rs` with quoted names and `?` placeholders), select top 100 (a new SQL tab, run at once), insert into the SQL editor, export, import, clone, truncate, delete. Other items: edit (functions, procedures, Postgres sequences; also a click), copy name, Copy Script As, insert, drop (with cascade on Postgres). Copy Script As is a second menu, since the context menu has no submenus
- [x] New: table, view, materialized view (Postgres), function, procedure (not SQLite), schema (Postgres), from the sidebar's right-click menu. Views and routines open an editor and are created through the preview
- [x] Search in database (the sidebar's Search tab): find text across every table, with contains, not contains, prefix and suffix; one table at a time with Stop, values bound as escaped LIKE patterns, counts capped at 1,000 per table; a result opens the table filtered (Ref: `LeftSidebarController`)
- [x] Item overview (right-click › Overview…): rows (estimated on Postgres and InnoDB, exact on SQLite), size, created date on MySQL (Ref: `ItemOverviewController`, `DefinitionPopover`)

## 5. Data grid

- [x] Paged table data (300 rows per page) with next and previous page
- [x] Double-click to edit a cell, with staged changes highlighted
- [x] Save (⌘S) and Discard, ⌘⌫ sets NULL
- [x] Virtualized grid so large pages scroll smoothly (only rows near the viewport are drawn; arrow keys move the selection, Shift extends it, Enter edits)
- [x] Page settings: click the row range in the footer for rows per page, the row to start at, and the count; tables the catalog estimates above a threshold (Settings › Data; Postgres `reltuples`, MySQL `table_rows`, SQLite always exact) show "~N" with a "Count" button for the exact number (Ref: `PagingPopover`, `EstimatedOffsetPopover`)
- [x] Sort by clicking a column header (ascending, descending, off); Shift-click sorts on several columns
- [x] Default sort setting (none / primary key ascending / descending), Settings › Data
- [x] Show and hide columns (Columns in the footer) and resize them (drag a header edge, double-click to fit), remembered per connection and table
- [x] Reorder columns by dragging headers (view only, remembered per connection and table; Columns › Reset order)
- [x] Add row, duplicate rows, delete rows (staged like edits; ⌘⌫ deletes the selected rows, again restores them)
- [x] Preview the pending changes as SQL before committing (generated by the backend, `rows_sql`) (Ref: toolbar "Preview your changes as SQL Statements")
- [x] Undo and redo staged edits (⌘Z / ⌘⇧Z on the grid)
- [x] Select cells (click, Shift-click, drag, row numbers, ⌘A) and copy (⌘C); Copy rows as CSV with header, Markdown table or INSERT statements; copy a column name or all its values on the page (Ref: `TableDataController`)
- [x] Paste into the selected cells (tab-separated, the inverse of Copy; one value fills the selection)
- [x] Cell editors: date picker, dropdown for enum and boolean columns, JSON editor (text, pretty and minify, validation), binary and bytea hex viewer (binary cells read-only), load a cell's value from a file (20 MB cap), and a JSON tree view (Text / Tree in Quick Look: folds, draws a branch when first opened, click a value to copy its JSONPath) (Ref: `DatePickerPopover`, `JSONEditorController`)
- [x] Quick Look editor: open a large value in a window (also opens for multi-line values, which the one-line editor would flatten) (Ref: `QuickLookWindow`)
- [x] Follow a foreign key: right-click a value in a key column (marked → in its header) › Open referenced row, which opens the referenced table filtered to that row
- [x] Rows without a primary key: warn when an edit would hit several rows, and offer "Save Again with Row Identity" (ctid / rowid). Tables without a primary key are editable too, with a warning in the footer and a question before the first save: rows are found by ctid (Postgres) or rowid (SQLite), and on MySQL by all their values with `LIMIT 1` (refused when a FLOAT, JSON, binary or spatial column can't be compared exactly)
- [x] Render PostGIS geometry as WKT (setting, on by default): extended WKT (`SRID=4326;POINT(1 2)`), which also reads back in when edited
- [x] Data-rendering settings: alternating row colors (Appearance), thin grid scroll bars shown only while the pointer is over the grid (Settings › Data)
- [x] Log the original values when updating or deleting (setting): each changed row's values go to the console log as a comment before its statement

## 6. Filters

- [x] Filter bar (⌘F, or Filters in the footer): column + operator + value, multiple rows, each with an on/off checkbox, combined with AND (default) or OR; values are bound by the backend (`browse`) (Ref: `FilterViewController`)
- [x] Operators: =, ≠, <, ≤, >, ≥, contains, not contains, starts with, ends with, IN, NOT IN, BETWEEN, NOT BETWEEN, IS NULL, IS NOT NULL, is empty, is not empty, and raw SQL
- [x] Filter on "any column"
- [x] Add, remove and duplicate filter rows; apply all, or one row alone (▸ switches the others off)
- [x] Show the SQL a filter produces (SQL in the filter bar): the backend builds it with the same code as `browse`, values written in as quoted literals for display only (`filter_sql`)
- [x] Quick filter from a cell: right-click → filter rows with this value (IS NULL for NULL cells)
- [x] Filter defaults (Settings › General): a new row's column (any, first, primary key) and operator, and whether a table opens with the filter bar showing (our reading of TablePlus's "Default Filter State"). Still missing: TablePlus's "Default Filter Column Sort"
- [x] Export the filtered result (Export… in the filter bar or the footer): every matching row or only the current page

## 7. Right sidebar

- [x] Row detail (Space): every field of the selected row as an editable form, with search, an enum dropdown and automatic JSON formatting (Ref: `RightSidebarController`)
- [ ] AI assistant tab (see section 19)

## 8. Table structure

- [x] Structure view: columns, indexes, foreign keys, checks and triggers in sections (`structure.ts`, `structure.rs`)
- [~] Edit columns: add, drop, rename, change type, nullable, default, comment and auto-increment; staged, previewed as SQL, committed with ⌘S (done). Still missing: reorder (MySQL), length as its own field, and on SQLite changing a type, nullability or default (needs a table rebuild: create, copy, drop, rename) (Ref: `TableStructureController`)
- [x] Indexes: list, add and drop with name, columns, unique flag and method (Postgres btree, hash, gist, gin, brin, spgist; MySQL BTREE, HASH) (Ref: `TableIndexController`)
- [x] Foreign keys: column, referenced table and columns, ON UPDATE and ON DELETE; on SQLite only when creating the table (Ref: `ForeignKeyPopover`)
- [x] Triggers: listed, added (name, BEFORE/AFTER, the event — several on Postgres — and what it runs: the function on Postgres, statements on MySQL and SQLite), edited (drop, then add again) and dropped. Statement-level triggers, WHEN clauses and column lists stay as written and can only be dropped (Ref: `TriggerPopover`)
- [x] Check constraints and the primary key: checks listed, added (named, any SQL expression) and dropped; the primary key set when creating a table and changed on an existing one with the Key column (MySQL in one statement; Postgres drops the old constraint by name). SQLite only at creation (a rebuild otherwise)
- [x] Table options: name and comment; MySQL's engine (from the server's) and default collation
- [x] View the DDL of a table or view: MySQL `SHOW CREATE TABLE`, SQLite `sqlite_master`, Postgres written from the structure (Ref: `ViewDDLController`)
- [x] View structure and definition editing: a view's Structure shows its columns and its SELECT in an editor, saved with ⌘S through the preview (CREATE OR REPLACE; materialized views and SQLite views are dropped and created again in one transaction). Postgres materialized views list no columns (the columns query reads `information_schema`, which leaves them out), so they can't be browsed either yet
- [x] Function and procedure editor: the CREATE statement in an editor window, one per overload on Postgres (CREATE OR REPLACE). MySQL drops and creates the routine; if the new one fails, `run_ddl` puts the original back
- [x] Sequence editor (Postgres): current value, increment, min, max, start, cache size, cycle and comment; only what changed is altered (`setval` for the value). MariaDB's sequences aren't editable yet

## 9. Table operations

Right-click a table in the sidebar (or the sidebar's empty space). Each shows its SQL and runs only when confirmed.

- [x] Create table (a new structure tab)
- [x] Clone table, with or without its data. SQLite copies the definition and rows, not indexes or triggers; a Postgres copy shares serial sequences with the original (Ref: `CloneTableWindow`)
- [x] Truncate: cascade, restart identity (Postgres); ignore foreign key checks (MySQL); reset auto-increment (SQLite) (Ref: `TruncateTableWindow`)
- [x] Drop: cascade (Postgres), ignore foreign key checks (MySQL) (Ref: `DeleteTableWindow`)
- [x] Rename table
- [x] Reset auto-increment (to one past the largest value)
- [x] Create, rename and drop schemas (Postgres)

## 10. SQL editor

- [x] CodeMirror with syntax highlighting and line numbers
- [x] Run the statement under the cursor, or the selection (⌘↩); Run all runs each statement in turn, stopping at the first error, with one safe-mode confirmation (⌘⇧↩)
- [x] Highlight the current statement
- [x] Autocomplete: tables, columns (schema loaded at connect), keywords, built-in functions per engine and the database's own (from the sidebar); keywords in upper case and accepting with Enter, Tab or both (Settings › Editor)
- [x] Format SQL (⌘I or Format in the run bar): the selection or everything, with Standard, Tabular left and Tabular right styles (Settings › Editor), using sql-formatter
- [x] Toggle line comment (⌘/) and block comment (⌥⇧A), from CodeMirror
- [x] Find and replace (⌘F in the editor), with regex, match case and whole word, from CodeMirror
- [x] Text transforms (Edit menu): upper ⌥⌘U, lower ⌥⌘L and capitalize case ⌥⌘C (the selection, or the word at the cursor), shift right ⌘] and left ⌘[, trim trailing whitespace and convert indentation to tabs or spaces (the selected lines, or all)
- [x] Show invisible characters, wrap lines (Settings › Editor), font size
- [x] Vim key binding (Settings › Editor: Sublime or Vim), with `@replit/codemirror-vim` 6.3.0 (no dependencies of its own), loaded only when chosen
- [x] Query parameters: `:name` (default), `$name`, `${name}`, `{{name}}`, `%name%` or `?` (Settings › Editor, or off), found outside strings and comments, asked for once per run with the last values remembered (Ref: `VariableWindow`, `VariableOptionWindow`)
- [x] Row limit for each run (100 to 100,000 or none; default 1,000): the backend streams rows and keeps only that many, so a huge result can't fill memory
- [x] Open (⌘O) and save (⌘S, Save as ⌘⇧S) `.sql` files in SQL tabs; files up to 20 MB, saved through a temporary file (dialogs not yet tried in the app window)
- [ ] New SQL Viewer window (Ref: `SQLViewerWindow`)
- [x] SQL tabs are saved per connection and database, from every workspace tab, and reopened on connect (Settings › Editor, on by default). Saved 400 ms after typing stops, and at once before switching database or workspace, closing a workspace, and closing the window or quitting (Quit closes the window so this runs). Tabs kept per connection by earlier builds go to the first database opened
- [x] Explain: the plan for the statement under the cursor (EXPLAIN, or EXPLAIN QUERY PLAN on SQLite); safe mode treats EXPLAIN of a query as read-only

## 11. Query results

- [x] Results table under the editor
- [x] Several statements: split results into tabs, or append them all in one tab (Ref: `QueryResultAppendingModeController`). Settings › Editor › Several results. One call returns every result set too (MySQL procedures, SQLite batches, a selection run as one), at most 100 per call, the row limit shared across them
- [x] Message tab: affected rows and errors per statement, and its time. Timed in the UI around each statement, so it includes network latency and the app's own round trip
- [x] Stop on error option (Settings › Editor › Run all)
- [x] Pin a result tab so it stays
- [x] Search and replace within a result (Ref: `QueryResultController`). Search filters the shown result's rows; Replace… (beside it, on a result that can be edited in place) replaces plain text in every cell, with match case and whole cell options, staged as one undo step to review and Save
- [x] Load more rows: a read-only statement whose result was cut runs again with room for another row limit's worth
- [x] Edit results from a single-table query in place, the same as the data grid. Only a plain `SELECT … FROM one_table [WHERE/ORDER/LIMIT]` whose columns are all the table's own, key included; not in the one-tab mode or while searching. Saving runs the statement again
- [x] Charts from results: line, bar and pie, with x and y axis pickers (several y series for line and bar), in place of the grid (the strip's Chart button); add a chart to the Metrics Board. Plain SVG, no chart library; a tooltip on hover and arrow keys
- [x] Export one result, or all result tabs (Ref: `ExportResultWindow`, `ExportMultipleResultsWindow`). Each one's statement runs again on the export's connection

## 12. Saved queries and history

- [x] History for each connection, grouped and searchable, click to reopen
- [x] Keep history 1, 2 or 3 years, or forever (Settings › General; at most 5,000 per connection); clear history; history stored in a file, not localStorage
- [x] Saved queries (favorites): folders, rename, open in a tab, add from history (right-click) or the editor (Save Query as Favorite…), per connection in `favorites.json` beside the SQL tabs, so Settings › Locations puts them in a shared folder (Ref: `LeftSidebarController` "Queries"). Shown as a Saved queries section at the top of the History pane; the pane's search box filters both. Not done: drag and drop between folders (the edit dialog moves them), nested folders, a folder of `.sql` files
- [x] A favorite can be an autocomplete snippet with keywords (Ref: `FavoriteEditorWindow`)
- [x] Console log: every statement the app sends, with syntax highlighting, and a clear button (Ref: `CommandLoggerController`). Recorded in db.rs where statements go out (the last 1,000; bound values show as placeholders), all connections in one log; the panel (⇧⌘C) reads it while open

## 13. Import and export

- [x] Export a table to CSV, JSON or SQL; delimiter, quote-if-needed or quote all, line break, field names in the first row, NULL → empty; gzip; SQL with or without structure, content and DROP IF EXISTS (Ref: `ExportTableWindow`). Rows stream from the database into a temporary file that is renamed into place. Not done: Excel, choosing fields, a decimal separator; JSON values are all strings (cells are text); SQLite blobs export as `<n bytes>`; Postgres structure is rebuilt from the columns (types, NOT NULL, defaults, primary key), without other constraints or indexes
- [x] Export several tables at once, one file each (Ref: `ExportTablesWindow`)
- [x] Export the current page, or the filtered result; an editor query's result is run again for export
- [x] File-name patterns: {connection}, {database}, {table}, {date}, {time} (Ref: `NamingPattern`)
- [x] Run exports and imports in the background on their own connection, with progress and cancel
- [x] Import CSV wizard: preview the first 100 rows, header row, delimiter and quote, empty as NULL, match columns by name (case-insensitive optional) or order, create a new table of text columns, stop / skip / update on duplicates; batched INSERTs with bound values in one transaction (Ref: `ImportCSVWizardWindow`). Not done: encodings other than UTF-8
- [x] Import JSON wizard: an array of objects, up to 100 MB (Ref: `ImportJSONWizardWindow`)
- [x] Import SQL wizard: preview, run a `.sql` file statement by statement with progress, stopping at the first error (Ref: `ImportSQLWizardWindow`). Not done: MySQL's `DELIMITER` command
- [~] CSV defaults in settings: Settings › Data › CSV export sets the delimiter, line break, header, quote-every-field and NULL-as-empty a new export starts with; the export dialog keeps its last choices in the same place (done). Still missing: the quote character and a decimal separator (the export writes numbers as the database returns them; both need backend support)

## 14. Backup and restore

- [x] Backup: pick connection and database, tool version, extra options (`--data-only`, `--schema-only`, …), gzip, file name (Ref: `BackupWindow`). Launcher right-click or the workspace's Tools menu; runs in the background with progress and cancel (bottom-right panel); a finished backup or export offers Show in Finder (Explorer, folder). SQLite uses the online backup API (SQLCipher files stay encrypted). Postgres also offers the custom archive format
- [x] Restore into an existing or new database (Ref: `RestoreWindow`). Always confirmed; safe mode is enforced in Rust too. Gzip and pg_dump archives are detected from the file (archives go to pg_restore, scripts to psql with ON_ERROR_STOP). SQLite copies the backup back over the file with the backup API
- [x] Use `pg_dump`/`pg_restore` and `mysqldump`/`mysql`, matched to the server version; pass passwords through environment variables, never in command arguments. Found on PATH, Homebrew, Postgres.app and DBngin; the oldest same-flavour version not older than the server wins, or Choose… a folder. SSH tunnels and TLS mode/files are mirrored (libpq conninfo with `hostaddr`; MySQL `--ssl-mode`, MariaDB `--ssl` flags). Not yet: a connect timeout for mysqldump (it has no flag; Cancel covers it), MySQL VERIFY_IDENTITY through a tunnel checks 127.0.0.1

## 15. Admin tools

- [x] Process list: running queries, refresh, kill (Ref: `ProcessListWindow`). Own side connection (works while the workspace runs a query), auto-refresh every 3 s, Cancel query / Kill behind a confirmation. MySQL reads `information_schema.processlist` (SHOW FULL PROCESSLIST minus its own session)
- [x] User management, Postgres: roles, login, superuser, create DB, replication, bypass RLS, global and table privileges (Ref: `PGUserManagerWindow`). Tools › Users…: create, edit, rename, drop roles; every attribute (inherit, connection limit, valid until, password); membership in other roles (predefined `pg_` roles too); database, schema and table privileges with grant option per privilege. Schemas and tables are the workspace database's. The password goes to the server as a SCRAM-SHA-256 verifier; dropping a role revokes its listed privileges first. Every change is previewed as SQL and runs under safe mode
- [x] User management, MySQL: users and hosts, global, database and table privileges, resource limits (Ref: `MSUserManagerWindow`). Create, edit, rename (user and host), drop; password and authentication plugin (MariaDB `IDENTIFIED VIA`); the four resource limits; grant option per object. MySQL 8 dynamic privileges (`BACKUP_ADMIN`…) aren't shown and are left alone. Not done: roles (MySQL 8 / MariaDB), account locking, password expiry
- [x] The console log masks password literals (`PASSWORD '…'`, `IDENTIFIED BY '…'`, `SET PASSWORD … = '…'`), also in SQL typed in the editor

## 16. Diagram

- [x] ER diagram of a schema: tables and views with columns (key, foreign-key and type), FK lines from column to column, choose tables (a schema over 80 tables starts with a picker, or from the right-clicked table and its neighbours; at most 500 drawn), add related tables, drag, auto layout, zoom and pan, fit, search (adds a table that isn't shown), double-click to open a table (Ref: `DiagramWindow`). Sidebar right-click (schema or table) → Show diagram, and Tools › Show Diagram. One query per engine (`diagram.rs`); the layout is our own layered one, no dependency. The diagram stays per session and schema until the app quits
- [x] Save and open diagram files (JSON, `version: 1`, table names and positions; temp file then rename; a malformed file is refused and leaves the diagram as it was; tables no longer in the database are left out with a note). Export PNG (2x) or SVG with the theme's colours
- [ ] Diagram: notes and colours on tables, lines routed around boxes, printing; FKs into another schema aren't drawn

## 17. Metrics board

- [x] Dashboard canvas made of widgets: data table, scoreboard, bar, line and pie charts, exporter, importer, input field, button, dropdown, connection (Ref: `MetricsBoardWindow`, `Metrics*View`). Opens over the workspace (Tools › Metrics Board); the connection widget picks which open connection the board runs on
- [x] Widgets bound to queries, with input fields and dropdowns feeding named query parameters (the editor's parameter format). A query that may change data runs only from its widget's own Run button, after asking; auto-refresh and parameter changes skip it, and skip what safe mode wants approved
- [~] Auto-refresh (per board or per widget), lock board, grid (drag to move and resize), several boards per connection, import and export a board (JSON with a version). No TablePlus-style event wiring between widgets: a parameter change refreshes the widgets using it, a button refreshes the board. Moving and resizing is pointer-only

## 18. Safety and security

- [x] Confirm before running writes (`sqlparser` read-only check)
- [x] TablePlus's five levels, set per connection plus a default for new connections: Silent; Alert 1 (warn on every query); Alert 2 (warn except SELECT/EXPLAIN/SHOW); Safe 1 (ask for the database password on every query); Safe 2 (ask for the password except reads) (Ref: `SettingSecurityController`)
- [x] Toolbar lock icon to switch the level quickly (leaving a Safe level wants the password)
- [x] Unlock safe mode with Touch ID (`robius-authentication`; macOS only)
- [x] App passcode: lock at startup and after N minutes away; unlock with Touch ID; change passcode (Ref: `PasscodeWindow`)
- [x] Passcode lock in the backend: while locked, every database request is refused, the MCP server's included (the app starts locked until it knows whether a passcode is set)
- [x] Run all at a Safe level asks once for the batch: with Touch ID, one prompt mints a short-lived grant (5 minutes, in memory, compared as a digest) that the rest of the statements send; a password was already resent without asking
- [x] Undo note: the previews before structure, object and user changes say that MySQL commits each DDL statement as it runs, so the ones before a failure stay applied (elsewhere they run in one transaction); a failure partway lists the statements that had run

## 19. AI

- [ ] Assistant tab in the right sidebar: chat that writes SQL with the schema as context, chat history, insert into the editor (Ref: `AIChatViewController`, `AIChatContextViewController`)
- [ ] Providers: OpenAI-compatible (custom base URL), Claude Code / Codex / other agents over ACP, GitHub Copilot; API keys in the keychain (Ref: `OpenAICompatibleConfigView`, `GitHubCopilotConfigView`)
- [ ] Approve each query the AI wants to run (Ref: `AIQueryApprovalPopover`)
- [ ] Consent notice that prompts go to the provider

## 20. MCP server

- [x] Built-in MCP server with stdio bridge binary (like `tableplus-mcp`): streamable HTTP on 127.0.0.1 (rmcp, stateless), `tableminus-mcp` bridge; tools list_connections, list_tables, describe_table, run_query (row limit and query timeout apply)
- [x] Ship the bridge inside the app bundle: `npm run bundle` (scripts/build.sh) builds `tableminus-mcp`, copies it in as a Tauri external binary through `src-tauri/tauri.bundle.json`, and it lands next to the app's executable
- [x] Access tokens: create, name, choose which connections each token can reach, read-only or read-write, revoke; the value is shown once (Ref: `MCPTokenSetupWindow`)
- [x] Auto-generated token for the local bridge (`mcp-bridge.json`, mode 0600; revoking it rotates it)
- [x] Approval needed for unsafe queries (on by default; a connection in safe mode always asks; read-only tokens never write)
- [x] Preferred port (another free port when it's taken)
- [ ] Optional HTTP server with TLS for remote access, export the certificate (out of scope for now: the server is loopback-only)
- [x] Manual setup instructions for Claude Code, Cursor and other agents, and for HTTP with a token
- [ ] One-click setup that edits an agent's config file (back it up first; never rewrite a config that doesn't parse)
- [x] Activity log of tool calls, viewable and clearable (Ref: `MCPIntegrationsActivityWindow`)
- [ ] Connected clients list; disconnect clients (the server is stateless, so there are no sessions to list yet)
- [x] Read-only tokens: also run their queries in a read-only transaction (SQLite: `query_only`), so a SELECT calling a function that writes can't

## 21. Plugins and custom scripts

- [ ] Run custom script: a JavaScript pane that can query the current connection (Ref: `CustomScriptWindow`)
- [ ] Plugin manager: install, update and remove plugins; plugins run in a sandbox (QuickJS) with explicit permissions, not full access (Ref: `PluginWindow`)

## 22. Settings

What already exists: theme (auto / light / dark), alternating rows, font size, line height, indent, safe-mode default. Still to add, by TablePlus tab:

- [~] General: reopen workspaces at startup, query timeout, keep alive, query history retention (done); CSV options are remembered from the last export. Still missing: language (English only for now), beta updates and turning off auto-update (wait for the updater, section 25), ⌘T opening an empty tab instead of a SQL tab. Not copying: crash reports (Sentry) (Ref: `SettingGeneralController`)
- [x] Table (Settings › Data): page size, estimate count above N rows, default sort, PostGIS → WKT, auto-hide scrollbars, log original values, show an export's files in Finder when it finishes (Ref: `SettingTableController`)
- [x] SQL Editor: uppercase keywords, complete with Enter or Tab, key binding, indent, query parameter format, close brackets and quotes; SQL tabs are kept as you type ("Keep SQL tabs"), which covers auto-save (Ref: `SettingEditorController`)
- [x] Security: safe-mode default, passcode, auto-lock time, Touch ID (Ref: `SettingSecurityController`)
- [x] Locations: folders for connections, queries and history (so it can live in iCloud or Dropbox); restore defaults (Ref: `SettingLocationController`)
- [x] Locations: settings, themes, column layouts and the other remembered choices live in `settings.json` in their own Settings folder (prefs.ts), so they can sync between Macs. localStorage only keeps a copy for a fast start; the file wins, and settings found in a newly chosen folder apply at the next start. MCP settings stay in the config folder on purpose: their access tokens belong to one machine
- [x] Shortcuts: rebind every command (click its shortcut, press the new keys), with conflict detection, clear, reset one or all; kept with the other settings (Ref: `SettingShortcutKeyController`). Commands live in `commands.ts`, which feeds the keyboard, the menu bar and Open Anything
- [ ] Fonts and themes: see section 23
- [ ] AI: see section 19
- [x] MCP: see section 20

## 23. Themes and appearance

- [x] Light, dark and auto
- [x] Theme editor: every UI color (sidebar, tabs, grid, editor syntax, status colors; chart colors once there are charts), editor and grid fonts, padding (Ref: `SettingThemeController`)
- [x] Bundled themes (our own: Paper, Harbor, Ember, Lagoon) plus import and export of theme JSON
- [ ] Install themes from a Git repository and update them (Ref: `ThemeGitController`)
- [x] Separate light and dark defaults for Auto

## 24. More engines

Done: PostgreSQL, MySQL, MariaDB, SQLite. Turn `db.rs`'s `Conn` enum into a driver trait before adding the next one.

- [ ] Postgres-compatible engines first, since they reuse the Postgres driver: CockroachDB, Redshift, Greenplum, Vertica
- [ ] SQL Server, including Microsoft Entra ID device-code sign-in (Ref: `MSSQLForm`, `MicrosoftEntraIDDeviceCodeWindow`)
- [ ] Redis: key browser, typed values (string, list, set, hash, zset), each command marked as read or write
- [ ] MongoDB: collections, JSON document grid, Mongo shell and query syntax in the editor, legacy dialect for 3.6/4.0
- [ ] ClickHouse (native and HTTP), DuckDB, libSQL / Turso, Cloudflare D1
- [ ] Oracle, Snowflake, BigQuery, Cassandra, DynamoDB, Elasticsearch

## 25. App and platform

- [x] Menu bar matching TablePlus: File, Edit, View, Navigate, Connection, Tools, Window, Help (`menu.ts`, Tauri's menu API, built from the command list and rebuilt when a shortcut changes). Not yet tried in the app window
- [ ] App updater (Tauri updater) with a changelog
- [~] Release builds: `npm run bundle` runs the checks and audit, then makes the `.app` and `.dmg` on macOS (done). Still missing: Developer ID signing and notarization (builds are ad-hoc signed, so other Macs block them), and trying the AppImage/`.deb` build on Linux
- [ ] Linux fallback when there's no Secret Service: an encrypted file protected by a master password
- [x] Move history, saved queries and settings out of localStorage into files in the data folders
- [ ] New logo: a blue mockingbird (*Melanotis caerulescens*). Reference: perched, deep blue-black with scaled, glossy blue throat and breast feathers, a red eye, a slim dark bill and a long tail. Simplify it into a mark that keeps the brand's flat ink-and-primaries style and reads at 16px. Replaces the launcher's four-disc mark (`.mark` in `styles.css`) and the app icons in `src-tauri/icons/` (regenerate them with `npm run tauri icon` from one source image)
- [ ] Custom title bar: the native macOS title bar is a strip of empty space above the workspace tabs. Replace it (Tauri `titleBarStyle: "Overlay"` with `hiddenTitle` in `tauri.conf.json`, and `data-tauri-drag-region` on the bar) and move items into it: the workspace tab strip beside the traffic lights, and candidates from the header such as the connection pill or Console / SQL. Keep empty parts draggable, double-click to zoom, and the traffic-light inset; Windows and Linux keep their native frame or get matching window buttons

## 26. Rust hygiene

Rules live in [CLAUDE.md](CLAUDE.md). Done so far: strict lints, typed errors, bound SQL parameters, secrets in `SecretString`, connect timeout, safe file writes, `npm run check`.

- [x] `cargo-audit` and `cargo-deny` with `deny.toml` (license allowlist, advisories, no unknown sources): `npm run audit`. Kept out of `npm run check` because it fetches the advisory database. One ignored advisory, RUSTSEC-2023-0071 (rsa via russh, no fix released; reason in `deny.toml`)
- [x] One lock per connection instead of one global lock (done: a slow query holds up only its own connection, and keep-alive skips busy ones). SQLite calls run in `block_in_place` (the connection is borrowed, so not `spawn_blocking`): the pool takes over the worker's other tasks while SQLite works
- [x] Safe mode: SELECTs that call built-in side-effect functions (`nextval`, `pg_terminate_backend`, `lo_unlink`, `dblink`, `load_extension`…) and `SELECT … INTO` count as writes (done). At Alert 2 and Safe 2 the editor runs what the parse calls read-only inside a read-only transaction (SQLite: `query_only`), so a user-defined function that writes is refused by the database and then asked about like any write; MCP read-only tokens do the same. Postgres still lets such a function write to temporary tables
- [x] Property tests (`proptest`) for the read-only check (never panics; a write after any read is caught; SELECTs of any quoted names pass) and for quoting (identifiers and literals read back as given); a `cargo fuzz` target for the read-only check (`src-tauri/fuzz`)
- [x] Size limits on imports (CSV and SQL records 64 MB, 4,096 fields, JSON files 100 MB) and on cell values loaded from files (20 MB)

## Not copying

Licensing, trial limits, purchase and register screens, the iOS promo, Sentry, and the "report bug" form. These belong to TablePlus as a product, not to what it does with databases.

## Suggested order

1. SSH tunnels and the remaining connection options (section 2)
2. Filters, sorting, column tools and copy-as (sections 5–6)
3. Run current statement, autocomplete, formatting (section 10)
4. Structure editing and table operations (sections 8–9)
5. Import, export, backup and restore (sections 13–14)
6. Workspace extras: Open Anything, split panes, multiple workspaces, row detail (sections 3, 7)
7. Safety levels and passcode (section 18), then the remaining settings and themes
8. Admin tools, diagram, metrics board (sections 15–17)
9. MCP server, AI, plugins (sections 19–21)
10. More engines (section 24), then packaging and the updater
