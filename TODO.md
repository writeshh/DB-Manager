# TablePlus parity

The goal: everything TablePlus 27.0.2 does, feature for feature. Built from the app bundle (menus, settings, every screen's labels) and the earlier internals report. `Ref:` names the TablePlus screen to look at when building an item.

`[x]` done · `[~]` partly done · `[ ]` not started

## 1. Launcher and connections

- [x] Launcher window with a searchable connection list
- [x] New connection: engine picker, then a form for that engine
- [x] Test, Save, Connect buttons
- [x] Passwords kept in the OS keychain, never in the connections file
- [x] Tag on each connection (local / development / staging / production)
- [ ] Password storage choice: Store in keychain / Ask every time / No password (Ref: `PostgreSQLForm`)
- [ ] Status color for each connection, five swatches (Ref: `ConnectionPartNameController`)
- [ ] Groups: create, rename and delete groups, with an icon each; drag connections into them (Ref: `CreateGroupWindow`)
- [ ] Tag manager with custom tags and colors (Ref: `TagManagerWindow`)
- [ ] Sort connections by name, driver or tag
- [ ] Duplicate a connection, Copy as URL
- [ ] Import a connection from a URL, e.g. `postgresql://user@localhost/db` (Ref: `ImportConnectionURLWindow`)
- [ ] Import and export connections as a file: all, one group or one connection, password-protected, with an option to include passwords (Ref: `ExportConnectionWindow`)
- [ ] Open Recent list, and open a connection in a new tab or a new window (Ref: `ConnectionWindow`)
- [ ] Reopen closed workspaces at startup
- [ ] Open by URL scheme, e.g. `postgresql://`, `mysql://`, `redis://` links open the app
- [ ] Open `.sqlite` files and TablePlus connection files from Finder

## 2. Connection transport

- [x] **SSH tunnel**: server, port, user, password or private key (with key password), "use ~/.ssh/config when the key is empty" (Ref: `ConnectionPartSSHController`). Backend tested end to end with `scripts/test-ssh.sh`; the form is not yet tried in the app window. Not yet: ProxyJump/ProxyCommand, host certificates, a file picker for the key
- [x] SSL: Disable / Prefer / Require / Verify CA / Verify full for every engine, with libpq semantics (Prefer and Require encrypt without checking the certificate), CA, client cert and key files with file pickers; TLS is checked against the real host name through SSH tunnels (Ref: `PostgreSQLForm`, `MySQLForm`)
- [x] Postgres options: direct TLS negotiation, show system schemas, disable channel binding (More options in the form)
- [x] MySQL options: socket instead of host (not yet tried against a real socket), SNI name, cleartext auth plugin
- [ ] MySQL `LOAD DATA LOCAL INFILE`: the server picks which local file the client sends, so a blanket switch lets any server read your files. Only allow files named in the statement being run
- Not needed: TablePlus's MySQL "dialect" picks between its bundled 5.7 and 9.7 client libraries; the pure-Rust driver talks to old and new servers alike
- [x] SQLite: open SQLCipher-encrypted files with a passphrase (or raw `x'…'` key) kept in the keychain; uses CommonCrypto on macOS, the system libcrypto on Linux
- [x] Bootstrap commands: SQL run after connecting (and on every reopen), and a shell script run before connecting and before any SSH tunnel (Ref: `StartupQueryPopover`)
- [x] Keep connection alive: ping every 30 s (Settings › General); a connection that stops answering shows the reconnect banner
- [x] Query timeout (Settings › General, default 300 s): the query is stopped on the server (Postgres cancel, MySQL `KILL QUERY`, SQLite interrupt), and the connection stays usable
- [x] Reconnect (⌘⇧R), with a banner when the connection drops; tabs stay open and the database in use is re-selected. Returning to a workspace whose connection died reopens it quietly
- [x] Connection info popover (click the status pill): server version, driver, TLS cipher, SSH host, reconnect, disconnect (Ref: `ConnectionInfoPopover`)

## 3. Workspace window

- [x] Tabs: table tabs and SQL tabs, ⌘T new SQL tab, ⌘W closes a tab (and closes the workspace on the last one)
- [x] Open a database (⌘K), and connect without picking a database first
- [x] Status pill showing the connection and server version
- [ ] Rename tab, close other tabs, reopen closed tab, select tab 1–9 (⌘1…⌘9), next and previous tab
- [ ] Split pane right, and next and previous pane
- [ ] Multiple workspaces (connections) in one window, with a workspace switcher, next and previous workspace
- [ ] New window, new workspace
- [ ] Open Anything (⌘P): fuzzy search across tables, views, functions, saved queries and commands (Ref: `OpenAnythingController`)
- [ ] Toolbar: connection, database, SQL file, reload, safe-mode level, console log, row detail, metrics, user management (Ref: `WorkspaceWindow`)
- [ ] Toggle the left sidebar, right sidebar, bottom console and results pane
- [ ] Reload current tab (⌘R), reload workspace
- [ ] Go back and forward through opened items
- [ ] Database window: create, rename and drop databases; open in a new or the current tab (Ref: `DatabaseWindow`, `NewDatabaseWindow` with encoding and collation)
- [ ] Notifications panel for finished background jobs (exports, imports, backups)

## 4. Left sidebar

- [x] Table list with a name filter
- [~] Expand a table to see its columns (done). Still missing: sort columns A–Z or by position
- [ ] Sections for schemas, tables, views, materialized views, functions and procedures, sequences, user-defined types, and recently used items, each one switchable on or off
- [ ] Schema switcher; load system schemas; lazy-load large schemas
- [ ] Item groups: add items by hand, or by filter rules such as "name has prefix" (Ref: `ItemGroupWindow`, `FilterRuleController`)
- [ ] Pin items to the top
- [ ] Item context menu: open in new tab, open structure, item overview, show diagram, copy name, Copy Script As (CREATE, DROP, …), select top 100, insert into the SQL editor, export, import, clone, truncate, delete
- [ ] New: table, view, materialized view, function or procedure, schema
- [ ] Search in database: find text across every table, with contains, not contains, prefix and suffix (Ref: `LeftSidebarController`)
- [ ] Item overview: rows, size, created date (Ref: `ItemOverviewController`, `DefinitionPopover`)

## 5. Data grid

- [x] Paged table data (300 rows per page) with next and previous page
- [x] Double-click to edit a cell, with staged changes highlighted
- [x] Save (⌘S) and Discard, ⌘⌫ sets NULL
- [ ] Virtualized grid so large pages scroll smoothly
- [ ] Page settings: page size, jump to an offset, estimated count for tables above a threshold with a "Count" button for the exact number (Ref: `PagingPopover`, `EstimatedOffsetPopover`)
- [x] Sort by clicking a column header (ascending, descending, off); Shift-click sorts on several columns
- [ ] Default sort setting (none / primary key ascending / descending)
- [x] Show and hide columns (Columns in the footer) and resize them (drag a header edge, double-click to fit), remembered per connection and table
- [ ] Reorder columns by dragging headers
- [ ] Add row, duplicate rows, delete rows (staged like edits)
- [ ] Preview the pending changes as SQL before committing (Ref: toolbar "Preview your changes as SQL Statements")
- [ ] Undo and redo staged edits
- [x] Select cells (click, Shift-click, drag, row numbers, ⌘A) and copy (⌘C); Copy rows as CSV with header, Markdown table or INSERT statements; copy a column name or all its values on the page (Ref: `TableDataController`)
- [ ] Paste into the selected cells
- [ ] Cell editors: date picker, dropdown for enum and boolean columns, JSON editor (tree and text, pretty and minify), binary and bytea viewer, load a cell's value from a file (Ref: `DatePickerPopover`, `JSONEditorController`)
- [ ] Quick Look editor: open a large value in a window (Ref: `QuickLookWindow`)
- [ ] Follow a foreign key: jump from a cell to the row it references
- [ ] Rows without a primary key: warn when an edit would hit several rows, and offer "Save Again with Row Identity" (ctid / rowid)
- [ ] Render PostGIS geometry as WKT (setting)
- [ ] Data-rendering settings: alternating row colors (done as zebra), auto-hide scrollbars
- [ ] Log the original values when updating or deleting (setting)

## 6. Filters

- [x] Filter bar (⌘F, or Filters in the footer): column + operator + value, multiple rows, each with an on/off checkbox, combined with AND (default) or OR; values are bound by the backend (`browse`) (Ref: `FilterViewController`)
- [x] Operators: =, ≠, <, ≤, >, ≥, contains, not contains, starts with, ends with, IN, NOT IN, BETWEEN, NOT BETWEEN, IS NULL, IS NOT NULL, is empty, is not empty, and raw SQL
- [x] Filter on "any column"
- [~] Add and remove filter rows, apply all (done). Still missing: clone a row, apply a single row
- [ ] Show the SQL a filter produces
- [x] Quick filter from a cell: right-click → filter rows with this value (IS NULL for NULL cells)
- [ ] Filter defaults: default column, operator and state (Ref: TableDataController "Advanced Filter settings")
- [ ] Export the filtered result (Ref: `ExportFilterResultWindow`)

## 7. Right sidebar

- [ ] Row detail (Space): every field of the selected row as an editable form, with search, an enum dropdown and automatic JSON formatting (Ref: `RightSidebarController`)
- [ ] AI assistant tab (see section 19)

## 8. Table structure

- [~] Structure view showing columns (read-only today)
- [ ] Edit columns: add, drop, rename, change type, length, nullable, default, comment, auto-increment, and reorder (MySQL); staged like data edits, previewed as SQL, committed with ⌘S (Ref: `TableStructureController`)
- [ ] Indexes: list, add and drop indexes, with name, columns, unique flag and method (Ref: `TableIndexController`)
- [ ] Foreign keys: column, referenced table and columns, ON UPDATE and ON DELETE (Ref: `ForeignKeyPopover`)
- [ ] Triggers: list, add, edit and drop (Ref: `TriggerPopover`)
- [ ] Check constraints and the primary key
- [ ] Table options: name, comment, engine and collation (MySQL)
- [ ] View the DDL of a table or view (Ref: `ViewDDLController`)
- [ ] View structure and definition editing (Ref: `ViewStructureController`)
- [ ] Function and procedure editor (Ref: `FunctionViewController`)
- [ ] Sequence editor: current value, increment, cache size, comment (Ref: `SequenceController`)

## 9. Table operations

- [ ] Create table (a new structure tab)
- [ ] Clone table, with or without its data (Ref: `CloneTableWindow`)
- [ ] Truncate: cascade, restart identity (Ref: `TruncateTableWindow`)
- [ ] Drop: cascade, ignore foreign key checks (Ref: `DeleteTableWindow`)
- [ ] Rename table
- [ ] Reset auto-increment
- [ ] Create, rename and drop schemas

## 10. SQL editor

- [x] CodeMirror with syntax highlighting and line numbers
- [x] Run the selection, or everything when nothing is selected
- [ ] Run Current Query (⌘↩: the statement under the cursor) versus Run All (⌘⇧↩)
- [ ] Highlight the current statement
- [ ] Autocomplete: tables, columns, keywords and functions, with optional automatic schema prefixes, automatic uppercase keywords, and accept with Enter or Tab (setting)
- [ ] Beautify / format SQL, with Standard, Tabular Left and Tabular Right styles
- [ ] Toggle line comment (⌘/) and block comment
- [ ] Find and replace, including regex, match case and whole word
- [ ] Text transforms: upper, lower and capitalize case, shift left and right, trim trailing whitespace, convert indentation
- [ ] Show invisible characters, wrap lines, font size
- [ ] Vim key binding (setting: Sublime or Vim)
- [ ] Query parameters: `:name`, `$name`, `${name}`, `{{name}}`, `%name%`, `?`, with a prompt for values before running (Ref: `VariableWindow`, `VariableOptionWindow`)
- [ ] Row limit for each run: none, 100, 500, 1k, 5k, 10k, 50k, 100k, 500k
- [ ] Open and save `.sql` files; New SQL Viewer window (Ref: `SQLViewerWindow`)
- [ ] Auto-save SQL tabs (setting: save none, the first, or all)
- [ ] Explain / query plan

## 11. Query results

- [x] Results table under the editor
- [ ] Several statements: split results into tabs, or append them all in one tab (Ref: `QueryResultAppendingModeController`)
- [ ] Message tab: affected rows and errors; execution time including network latency
- [ ] Stop on error option
- [ ] Pin a result tab so it stays
- [ ] Search and replace within a result (Ref: `QueryResultController`)
- [ ] Load more rows
- [ ] Edit results from a single-table query in place, the same as the data grid
- [ ] Charts from results: line, bar and pie, with x and y axis pickers; add a chart to the Metrics Board
- [ ] Export one result, or all result tabs (Ref: `ExportResultWindow`, `ExportMultipleResultsWindow`)

## 12. Saved queries and history

- [x] History for each connection, grouped and searchable, click to reopen
- [ ] Keep history 1, 2 or 3 years, or forever; clear history; history stored in a file, not localStorage
- [ ] Saved queries (favorites): folders, rename, open in a tab, add from history, shared folder on disk (Ref: `LeftSidebarController` "Queries")
- [ ] A favorite can be an autocomplete snippet with keywords (Ref: `FavoriteEditorWindow`)
- [ ] Console log: every statement the app sends, with syntax highlighting, and a clear button (Ref: `CommandLoggerController`)

## 13. Import and export

- [ ] Export a table to CSV, JSON, SQL or Excel; choose fields, delimiter, quoting, line break and decimal; field names in the first row; NULL → empty; gzip; SQL with or without structure, content and DROP IF EXISTS (Ref: `ExportTableWindow`)
- [ ] Export several tables at once (Ref: `ExportTablesWindow`)
- [ ] Export the current page, or the filtered result
- [ ] File-name patterns such as connection name, table name and date (Ref: `NamingPattern`)
- [ ] Run exports and imports in the background, with progress and cancel
- [ ] Import CSV wizard: preview the first 100 rows, header row, encoding, delimiter and quote, match columns by name and/or order (case-insensitive optional), create a new table, insert / update on duplicate / stop on error (Ref: `ImportCSVWizardWindow`)
- [ ] Import JSON wizard (Ref: `ImportJSONWizardWindow`)
- [ ] Import SQL wizard: preview, run a `.sql` file with progress (Ref: `ImportSQLWizardWindow`)
- [ ] CSV defaults in settings: delimiter, quote, line break, decimal

## 14. Backup and restore

- [ ] Backup: pick connection and database, tool version, extra options (`--data-only`, `--schema-only`, …), gzip, file name (Ref: `BackupWindow`)
- [ ] Restore into an existing or new database (Ref: `RestoreWindow`)
- [ ] Use `pg_dump`/`pg_restore` and `mysqldump`/`mysql`, matched to the server version; pass passwords through environment variables, never in command arguments

## 15. Admin tools

- [ ] Process list: running queries, refresh, kill (Ref: `ProcessListWindow`)
- [ ] User management, Postgres: roles, login, superuser, create DB, replication, bypass RLS, global and table privileges (Ref: `PGUserManagerWindow`)
- [ ] User management, MySQL: users and hosts, global, database and table privileges, resource limits (Ref: `MSUserManagerWindow`)

## 16. Diagram

- [ ] ER diagram of a schema: tables with columns and FK lines, add tables, rearrange and auto-layout, zoom, search, save and open diagram files (Ref: `DiagramWindow`)

## 17. Metrics board

- [ ] Dashboard canvas made of widgets: data table, scoreboard, bar, line and pie charts, exporter, importer, input field, button, dropdown, connection (Ref: `MetricsBoardWindow`, `Metrics*View`)
- [ ] Widgets bound to queries, with inputs feeding into query parameters
- [ ] Auto-refresh, lock board, grid, several boards, import and export a board

## 18. Safety and security

- [x] Confirm before running writes (`sqlparser` read-only check)
- [ ] TablePlus's five levels, set per connection plus a default for new connections: Silent; Alert 1 (warn on every query); Alert 2 (warn except SELECT/EXPLAIN/SHOW); Safe 1 (ask for the database password on every query); Safe 2 (ask for the password except reads) (Ref: `SettingSecurityController`)
- [ ] Toolbar lock icon to switch the level quickly
- [ ] Unlock safe mode with Touch ID
- [ ] App passcode: lock at startup and after N minutes away; unlock with Touch ID; change passcode (Ref: `PasscodeWindow`)
- [ ] Undo note: say that DDL statements can't be rolled back when a commit fails partway

## 19. AI

- [ ] Assistant tab in the right sidebar: chat that writes SQL with the schema as context, chat history, insert into the editor (Ref: `AIChatViewController`, `AIChatContextViewController`)
- [ ] Providers: OpenAI-compatible (custom base URL), Claude Code / Codex / other agents over ACP, GitHub Copilot; API keys in the keychain (Ref: `OpenAICompatibleConfigView`, `GitHubCopilotConfigView`)
- [ ] Approve each query the AI wants to run (Ref: `AIQueryApprovalPopover`)
- [ ] Consent notice that prompts go to the provider

## 20. MCP server

- [ ] Built-in MCP server with stdio bridge binary (like `tableplus-mcp`)
- [ ] Access tokens: create, name, choose which connections each token can reach, read-only or read-write, revoke; the value is shown once (Ref: `MCPTokenSetupWindow`)
- [ ] Auto-generated token for the local bridge
- [ ] Approval needed for unsafe queries (on by default)
- [ ] Optional HTTP server with TLS for remote access, preferred port, export the certificate
- [ ] One-click setup for Claude Code, Cursor and other agents, plus manual setup instructions
- [ ] Activity log of tool calls; connected clients list; disconnect clients (Ref: `MCPIntegrationsActivityWindow`)

## 21. Plugins and custom scripts

- [ ] Run custom script: a JavaScript pane that can query the current connection (Ref: `CustomScriptWindow`)
- [ ] Plugin manager: install, update and remove plugins; plugins run in a sandbox (QuickJS) with explicit permissions, not full access (Ref: `PluginWindow`)

## 22. Settings

What already exists: theme (auto / light / dark), alternating rows, font size, line height, indent, safe-mode default. Still to add, by TablePlus tab:

- [ ] General: language, beta updates, turn off auto-update, reopen workspaces at startup, ⌘T opens a SQL tab or an empty tab, query timeout, keep alive, CSV defaults, report crashes (opt-in) (Ref: `SettingGeneralController`)
- [ ] Table: estimate count above N rows (default 500,000), PostGIS → WKT, auto-hide scrollbars, open the folder after export (Ref: `SettingTableController`)
- [ ] SQL Editor: auto-save queries, uppercase keywords, close braces and quotes, complete with Enter or Tab, indent style and width, key binding, query parameter format (Ref: `SettingEditorController`)
- [ ] Security: safe-mode default, passcode, auto-lock time, Touch ID (Ref: `SettingSecurityController`)
- [ ] Locations: folders for connections, queries and history, and other data (so it can live in iCloud or Dropbox); restore defaults (Ref: `SettingLocationController`)
- [ ] Shortcuts: rebind every menu command, with conflict detection (Ref: `SettingShortcutKeyController`)
- [ ] Fonts and themes: see section 23
- [ ] AI: see section 19
- [ ] MCP: see section 20

## 23. Themes and appearance

- [x] Light, dark and auto
- [ ] Theme editor: every UI color (sidebar, tabs, grid, editor syntax, chart colors, status colors), editor and grid fonts, padding (Ref: `SettingThemeController`)
- [ ] Bundled themes (TablePlus ships Jade, Polaris, Silver and Sunset) plus import and export of theme JSON
- [ ] Install themes from a Git repository and update them (Ref: `ThemeGitController`)
- [ ] Separate light and dark defaults for Auto

## 24. More engines

Done: PostgreSQL, MySQL, MariaDB, SQLite. Turn `db.rs`'s `Conn` enum into a driver trait before adding the next one.

- [ ] Postgres-compatible engines first, since they reuse the Postgres driver: CockroachDB, Redshift, Greenplum, Vertica
- [ ] SQL Server, including Microsoft Entra ID device-code sign-in (Ref: `MSSQLForm`, `MicrosoftEntraIDDeviceCodeWindow`)
- [ ] Redis: key browser, typed values (string, list, set, hash, zset), each command marked as read or write
- [ ] MongoDB: collections, JSON document grid, Mongo shell and query syntax in the editor, legacy dialect for 3.6/4.0
- [ ] ClickHouse (native and HTTP), DuckDB, libSQL / Turso, Cloudflare D1
- [ ] Oracle, Snowflake, BigQuery, Cassandra, DynamoDB, Elasticsearch

## 25. App and platform

- [ ] Menu bar matching TablePlus: File, Edit, View, Navigate, Connection, Tools, Window, Help
- [ ] App updater (Tauri updater) with a changelog
- [ ] Release builds: `.dmg` for macOS, AppImage and `.deb` for Linux
- [ ] Linux fallback when there's no Secret Service: an encrypted file protected by a master password
- [ ] Move history, saved queries and settings out of localStorage into files in the data folders (needed for section 22 Locations)

## 26. Rust hygiene

Rules live in [CLAUDE.md](CLAUDE.md). Done so far: strict lints, typed errors, bound SQL parameters, secrets in `SecretString`, connect timeout, safe file writes, `npm run check`.

- [ ] Install `cargo-audit` and `cargo-deny`; add `deny.toml` (license allowlist, no yanked crates, no unknown sources) and add both to `npm run check`
- [ ] One lock per connection instead of one global lock, and SQLite calls in `spawn_blocking` (a slow query on one connection currently blocks the rest)
- [ ] Safe mode: SELECTs that call side-effect functions (`pg_terminate_backend`, `nextval`, `lo_unlink`) or `SELECT … INTO OUTFILE` still count as read-only; mark them as writes
- [ ] Property tests (`proptest`) for the read-only check and identifier quoting; fuzz the read-only check with `cargo fuzz`
- [ ] Size limits on imports (CSV, JSON, SQL files) and on cell values loaded from files once those features exist

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
