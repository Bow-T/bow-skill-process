---
name: impact-sweep
description: Map and cover the full blast radius of ANY code change before declaring it done — callers of a changed signature, every surface a cross-cutting value (enum/status/vocabulary/key-format/shared DB column/new slot) touches, sibling cases of a bug, and the runtime contracts static checks can't see. Use before saying a change is complete, when editing shared/widely-referenced code, changing a signature/return-shape/props/enum/schema, fixing a bug that may have sibling occurrences, or when the user says "đủ case chưa" / "quét hết". Tiered so trivial edits stay light. Prevents shipping a partial change that QC then finds.
---

# Impact Sweep — cover the blast radius, don't let QC be the gap detector

Every change has a **blast radius**: the set of sites that must change or be
re-verified together. Missing one is the bug review finds. Before declaring any
change done, **size the radius → map every site → cover in one pass → prove
completeness.** "It compiles / analyze passes" is NOT done.

> Pairs with [[verify-runtime-not-just-static-green]].

## 0. Before adding anything new — grep for the home it already has
A change is often "add a new value/helper/config", not just "edit existing code".
The first miss is reaching for a fresh file when the repo already has the
canonical home — you then ship a **parallel** abstraction that fragments the very
thing the convention says to centralize ("grep first, reuse/extract").

Before you `Write` a new helper/constant/util/config class, grep for the
established home of that concern and **extend it** instead:
- **Reading `.env` / config** → the repo usually already has a single env reader
  (one class/file that owns the base URL, API keys, dev toggles). Add a
  field/getter there — do **not** spin up a new `Env`/config class beside it.
- **Enum/status/vocabulary, formatters, localizers, model converters, icon
  resolvers** → grep an existing member/peer (`grep -rln "<peer-concept>" lib`)
  and follow it to its home file; add beside it.
- Rule of thumb: if you're about to create a file whose job is "central place for
  X", assume X already has one and prove it doesn't (grep) before creating.

The cost of skipping this isn't a compile error — analyze stays green. It's a
second source of truth the next reader (and the next QC bug) has to reconcile.

## 1. Size the radius (tier the effort — keep small changes light)
- **Local** — one function body, no contract/signature/output change → just the
  edit + its direct test. No sweep needed.
- **Contract** — a signature, return shape, widget props, public API, or stored
  format changes → every **caller / consumer** is in scope.
- **Cross-cutting** — an enum/status/vocabulary/key-format value, a new
  document/role/type slot, or a DB column referenced in many files → every
  **surface** across every app and the backend (e.g. `apps/mobile`, `apps/admin`,
  `supabase/` in a monorepo).

Pick the tier honestly. When unsure, go one tier up. Only Contract/Cross-cutting
need the full mapping below.

## 2. Map every site mechanically (never from memory)
- **Callers / consumers:** grep the symbol, route name, column name, string key,
  RPC name. Every hit is a site.
- **Peer pattern:** when adding a member to a set, grep a **peer already wired
  everywhere** (e.g. an existing document slot for a new one, an existing enum member) —
  its hits ARE your checklist.
  `grep -rln "<peer>" .` (or every app + backend dir, e.g. `apps/mobile apps/admin supabase`)
- **Generated / derived:** `.g.dart`, l10n `.arb`, generated TS types — regenerate,
  never hand-edit.
- For Contract/Cross-cutting tiers, **show the site list to the user as the scope
  before implementing.** Explicit scope they can sanity-check beats implicit scope
  only QC can. "Done" = every listed site covered, not "analyzer passed".

## 3. Traps that grep-by-new-name CANNOT find — enumerate these by hand
- **Enumerations of the OTHER members:** `switch` arms, set-aggregating getters
  (a model's `isAnyFieldRejected` / `isFullyApproved`), `Record<Union,…>`,
  RPC/edge allow-lists, DB triggers (a status-deriving trigger function). They never
  contain the new token, so a grep for the new name silently skips them.
- **Stringly/dynamic dispatch:** RPC slot strings, edge-function validators, JSON
  keys, deep-link targets, analytics events.
- **Runtime-only contracts:** DB `CHECK`/`FK`/triggers, RLS, edge allow-lists —
  invisible to `flutter analyze` / `tsc`.
- **Direction & defaults:** a new status defaults to `pending`, so it's safe to
  add to an "any rejected?" check but **unsafe** to add to an "all approved?"
  gate or status-deriving trigger without a backfill + sign-off (it would regress
  every existing row). Decide per site; flag the unsafe ones, don't flip silently.
- **Enum-widen fans out to admin surfaces too — not just mobile.** Adding one
  status (a real case: one new delivery status) that compiled clean
  still slipped through THREE surfaces that grep-by-new-name missed: the mobile
  status→label badge `switch` (raw-token default), the admin **filter-option
  array** (a `STATUS_OPTIONS` constant in the list page), and a **DB
  function's hardcoded status IN-list** (an audience-resolving function's
  "users with active bookings" segment — a user in that state dropped out of
  broadcasts). When you widen a status/enum, explicitly check: mobile badge/label
  switches, admin label maps + filter arrays, every `status IN (...)` inside a
  SQL function/trigger, edge validators, `.arb` keys. Grep a peer status across
  every app and the backend (`grep -rln "<peer_status>" .`).
- **FK / table re-point → grep every READER, not just the writer.** When a
  migration re-points an FK or changes what a column means (a real case: a
  ticket table's order-reference column re-pointed from one table to another), a
  consumer still doing `from('<old_table>').eq('id', <column>)` silently
  returns null forever — the id now lives in a different table. A re-point is
  Cross-cutting: `grep -rn "<column>" .` and fix every read site,
  not only the write path that triggered the migration.
- **Derived accessors re-expose a value under a DIFFERENT name — grep the
  projections, not just the base field.** When you change *how* a stored value
  must be READ (not the value itself), a getter/computed-prop that projects it is
  named for its USE, not the column, so a base-name grep skips it. A real case:
  locking a storage bucket private meant every read of a stored image-URL column
  had to be re-signed; grepping the field name covered the query layer + VMs
  but **missed a model getter** that
  concatenates the record-level + per-item URLs into one list
  the inline strip rendered RAW → broken images on the now-private bucket, shipped
  green and merged. When you change a field's read-contract (signing, format,
  nullability, source table), follow the base field to **every accessor that
  returns/derives it** (`grep -rn "<field>\|<UseName>Url\|<UseName>Urls\|<UseName>Images"`)
  and fix at the accessor or its source — a grep for the column name alone is not
  the site list.

## 4. Make it self-enforcing so the NEXT change can't miss
- Prefer `switch` with **no `default`** / full `Record` → a new member becomes a
  compile error at every exhaustive site.
- **Single source of truth:** one descriptor list (key, label, field, getter)
  that consumers iterate, instead of each screen re-listing members. Adding once
  propagates everywhere. Propose this refactor when you see duplicated member
  lists (it's the durable fix; needs user OK — touches shared code).
- Add a **parametrized test over `<Enum>.values`** (or the canonical list)
  asserting every member is handled → a missing case is a red test, not a ticket.

## 5. Prove completeness + state it honestly
Done = site list covered + compiler/tests green + runtime path verified
([[verify-runtime-not-just-static-green]]). Report "swept N sites; runtime path
verified" — never "no errors" from static checks alone.

### 5b. Parallel-merge clobber — re-verify after a merged develop lands
When several branches touch the **same object** — common when two branches share
a ticket number (one sprint had three such pairs, one pair both editing the same
SQL function) —
a full-body `CREATE OR REPLACE <fn>` / `DROP+CREATE POLICY` from the branch with
the **later migration timestamp silently WINS** and can drop the other branch's
change. `analyze`/tests stay green; nothing conflicts textually because each
migration is a separate file. After you pull a merged `develop` (or before you
declare a cross-cutting change safe post-merge):
- **Confirm YOU are still the latest definer of every object you changed:**
  `grep -rln 'FUNCTION public.<fn>\|POLICY "<name>"\|<index_name>' supabase/migrations | sort | tail -1`
  → must be your file. If a parallel branch's later-timestamped migration is the
  tail, it clobbered you (or you clobbered its addition — check the reverse too:
  did your restate, based on an OLDER body, drop an arm another branch added
  between the version you copied and yours?).
- **Check for duplicate migration timestamps:**
  `ls supabase/migrations | grep -oE '^[0-9]{14}' | sort | uniq -d` — two files
  with the same prefix are an apply-order hazard.
- **Then re-run the ground truth on the merged tree**: `analyze` + full suite +
  the pgTAP the object is pinned by. A merge that auto-resolved cleanly can still
  be logically wrong; the suite (and "am I the latest definer") is what proves it.

**After shipping a cross-cutting change you could NOT runtime-test, adversarially
self-review your own diff before trusting it.** Your sweep is blind to the site
you didn't think to grep — an independent skeptic isn't. Spawn per-change review
agents (opt-in, §6) told to *break* the diff: hunt read-path misses (the derived
accessor you skipped), shadowed branches (a new early-return that makes a later
one unreachable — a real case: a terminal-status guard placed above the
already-used-PIN check made the dedicated "already used" branch dead for
single-stop), and guard/validation regressions (a real case: a new required-field
check that blocked editing a locked record). A batch of DB/storage/RPC changes
that all passed `analyze`+tests can still carry these; the self-review catches
them before QC — or before a merged regression does.

## 6. Large radius → fan out (opt-in)
If the site set is large, offer a multi-agent workflow: parallel finders per
subsystem + a completeness critic that asks "what site/modality is unaccounted
for?". Only with explicit user opt-in (it spends real tokens).

---

### Worked example (adding a new document slot to a KYC flow)
Peer = an existing document slot. Surfaces grep surfaced: signup step 1/2/3, vehicle-info,
Documents view+replace, admin detail/approve-reject/audit-labels/user-card, DB
columns+CHECK, the submit-document + admin-decide RPC allow-lists,
the edge-function slot maps, `intl_*.arb`.
Hand-checked traps: `isAnyFieldRejected` (added — safe), `isFullyApproved` +
the status-deriving trigger (deferred — needs backfill). The misses that reached
QC (step 2/3, vehicle-info, audit labels, the reject→resubmit gate) are exactly
what step 1's peer-grep + step 3's trap list would have caught up front.
