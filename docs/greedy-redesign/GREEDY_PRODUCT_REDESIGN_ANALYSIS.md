# GREEDY — Complete Product Redesign & Engineering Analysis

**Version:** November 10, 2026  
**Status:** Product concept and engineering specification; **not** a verified implementation audit  
**Audience:** Qwen, Claude Code, or any engineering agent responsible for improving the Greedy application  
**Project name:** GREEDY; internal engine name: **greedy engine**  
**Design reference:** Stellar AIO's task-oriented organization, intentionally simplified for beginners

> **IMPORTANT REPOSITORY NOTE:** This document is hosted in the public repository Tea-App-Removal/Tea-App-Green-Flags solely so an AI agent can read it via a public URL. **That repository is not identified as the Greedy application source code.** Do not edit or assume it contains Greedy. Obtain access to the actual Greedy source project separately before coding.

> **EVIDENCE NOTE:** Product architecture, features, file paths and test totals labeled "current" below are described in the owner's four-page *greedy-sheet.pdf*. They have **not** been independently verified against the real Greedy source tree or executable. Screen layouts, example products, counts, prices, timestamps and flows are proposed designs and are **illustrative**, not live measurements.

---

## Contents

1. Executive summary and proposed product direction
2. Current Greedy baseline and evidence limitations
3. Seven non-negotiable product rules
4. Stellar-inspired organization without cloning Stellar
5. Information architecture and navigation
6. Visual system, accessibility and UI conventions
7. Concept screen: Overview
8. Concept screen: Watchers
9. Concept screen: Individual Watcher
10. Single-watcher setup wizard
11. Multi-watcher and CSV import wizard
12. Watcher-level retailer account architecture
13. AI connection lifecycle and Claude integration
14. AI-powered diagnosis with deterministic safety boundaries
15. Real-time activity and watcher state machine
16. Product recognition, prices, thumbnails and page drift
17. Purchase gate, reservations, idempotency and reconciliation
18. Proposed logical data model
19. Phased implementation plan
20. Acceptance tests and release criteria
21. Risk register and mitigations
22. Coding-agent handoff prompt: Part 1
23. Coding-agent handoff prompt: Part 2
24. Sources, assumptions and boundaries

---

## 1. Executive summary and proposed product direction

**Greedy should become a premium, watcher-first, Windows-based product monitoring and purchasing application that feels as organized as a professional bot but remains easy enough for a nontechnical eighth grader to operate.**

The request is **not** for a CSS refresh. It requires reviewing and potentially correcting navigation, account ownership, watcher creation, AI integration, live observability, product metadata, spending safeguards, internal interfaces, storage, testing and packaging.

A user should be able to:

1. Click **Add Watcher**.
2. Paste one supported product URL.
3. See the *verified* product title, photo, retailer, price, currency and availability where extractable.
4. Accept or change the automatically proposed watcher name.
5. Choose **Alert Me / Manual**, **Suggest** or **Auto-buy**.
6. Set maximum per-item price, quantity, daily/weekly or total spending limits.
7. Optionally assign a retailer account, checkout profile, and AI connection **to that particular watcher**.
8. Review exactly what Greedy is authorized to do.
9. Start the watcher and immediately see true live status and activity.

For power users, the same product should support bulk import, tags/groups, batch management, clear diagnostics and safe, reproducible operations.

**The strongest differentiator:** Greedy should not merely automate. It should *explain what it sees, what it can verify, what it cannot verify, why it acted, and why it refused to act*.

The original 24-page visual report used a conceptual dark, premium desktop design. This Markdown adaptation preserves the design recommendations and replaces visual-only diagrams with readable tables, text mockups and Mermaid diagrams.

---

## 2. Current Greedy baseline and evidence limitations

The owner-provided *greedy-sheet.pdf* describes Greedy as a **portable Windows application** (one executable, greedy.exe, built with TypeScript, Electron and electron-builder), with user state stored locally. It is **not described as a cloud service**.

### What the sheet says exists

| Area | Described current behavior |
|---|---|
| Supported retailers | Pokémon Center, Bandai, Walmart and Target |
| Monitoring | Browser-driven watcher checks for appearance/availability, product price and page changes |
| Watcher management | One-at-a-time addition, URL/CSV import with preview, additions without engine restart |
| Modes | Manual (alert only), Suggest (advice only), Auto (authorized checkout) |
| Spending | Per-item, per-day and per-week caps; persistent held/spent ledger; limit review queue |
| Purchase safety | Money gate before action; emergency kill switch; one-at-a-time checkout mechanism |
| AI | Optional model assistance and help chat, with rules-only fallback |
| Surface | Today/Activity/Status views, tooltips and contextual help; local desktop/tray app |
| Tests | Reported fixture results, not independently witnessed by this analysis |

The owner's document names these source components:

| Reported file | Reported responsibility |
|---|---|
| src/watch.ts | Monitors supported retailer pages, price, presence and page changes |
| src/budget.ts | Durable spending gate and ledger |
| src/take.ts | Browser-based checkout executor |
| src/ai.ts, src/keys.ts, src/auth.ts, src/advice.ts, src/assist.ts | AI providers, authentication, advice and help interview |
| src/main.ts | Main process and coordination |
| src/preload.ts | Narrow renderer-to-engine bridge |
| ui/index.html | Existing UI |

The project sheet states that the current build passed various fixtures, including **9/9 auto-buy**, **9/9 AI path**, **13/13 AI unit**, **23/23 key-detection and CSV**, **20/20 Claude sign-in fixtures**, and **44/44 help-chat** checks. **These are claims reported by the project sheet, not tests run for this redesign.**

### Keep what already works

- The existing four-retailer boundary until more adapters are intentionally implemented and tested.
- Manual, Suggest and Auto modes.
- Browser-driven monitoring and checkout where authorized.
- Durable spending reservations and audit history.
- Global kill switch.
- No-AI fallback.
- Portable local Windows distribution.
- A secure narrow bridge between UI and privileged backend operations.

### Do not assume the sheet is reality

Before making changes, inspect real source, execute existing tests, try the packaged application, observe actual logs, and identify any mismatch between the sheet and code. Record *confirmed*, *broken*, *missing*, and *not yet tested* separately.

---

## 3. Seven non-negotiable product rules

| Rule | Requirement |
|---|---|
| **1. Watcher-first** | Create, start, pause and troubleshoot a watcher from the watcher workspace, without opening general Settings. |
| **2. One obvious action at a time** | Beginner pathways show a clear next step; advanced fields live behind expandable sections. |
| **3. Prove every connection** | "Connected" means a real, recent successful connection check, not "key saved" or "toggle on". |
| **4. Never fake observations** | Product title, photo, price and availability must be marked verified, stale, unavailable or unknown. |
| **5. Budget gate always wins** | AI and UI cannot change or bypass deterministic mode, site, quantity or spending restrictions. |
| **6. Explain what is happening** | Every watcher exposes its state, last check, latest event, next action and readable failure reason. |
| **7. Migrate safely** | Preserve old settings, profiles, watcher records and financial history with versioned migrations and testing. |

Successful redesign means **truthful, easy-to-operate functionality**, not prettier placeholders.

---

## 4. Stellar-inspired organization without cloning Stellar

Use [Stellar AIO](https://stellaraio.com/) and its official navigation guides as a reference for task management, grouping, accounts, profiles, dashboard and bulk actions—not for copying branding or forcing users through professional-only workflows.

| Stellar pattern | Greedy evolution | Reason |
|---|---|---|
| Tasks | Watchers | "Watch a product" is more intuitive than a generic task |
| Task groups | Optional smart groups/tags | Organization should not block first-time setup |
| Multiple tasks/modes | Watcher with Manual/Suggest/Auto behavior | The mode belongs to the watcher |
| Retailer accounts | Centrally stored, watcher-assigned account | Reuse without implicit global sign-in |
| Checkout profiles | Reusable, explicitly watcher-assigned profile | Required only when the action needs checkout |
| Dashboard | Live Overview | Quickly assess active monitoring, errors and budget |
| Bulk controls | Beginner-friendly bulk wizard plus advanced batch tools | Supports five URLs and large imports without surprise side effects |

**Keep Greedy simpler than Stellar.** The core novice mental model is: "**I have a product link. Help me watch it.**"

---

## 5. Information architecture and navigation

### Recommended sidebar

| Navigation | User question it answers | Core features |
|---|---|---|
| **Overview** | Is Greedy running? What needs me? | System health, active count, alerts, spend, newest events |
| **Watchers** | What am I monitoring? | Single/bulk add, cards/table, groups, start/pause/edit |
| **Activity** | What happened? | Live feed, filters, watcher timeline, searchable historical events |
| **Spending** | What can it spend? | Limits, reserves, spent amounts, pending outcomes, audit ledger |
| **Accounts & Profiles** | Which retailer identity or checkout settings should a watcher use? | Reusable retailer accounts, sessions, checkout resources |
| **AI Connections** | Is my AI provider actually working? | Supported provider setup, testing, model/status, per-watcher selection |
| **Settings** | How should Greedy operate globally? | Appearance, notifications, engine interval preferences, diagnostics |

Put **Groups/Tags** inside Watchers initially as a filter and organizational control. Promote to separate navigation only if real workflows justify it.

### Navigation diagram

~~~mermaid
flowchart TD
    G[Greedy desktop app]
    G --> O[Overview]
    G --> W[Watchers]
    G --> A[Activity]
    G --> S[Spending]
    G --> R[Accounts and Profiles]
    G --> I[AI Connections]
    G --> T[Settings]
    W --> WD[Watcher Details]
    W --> NW[Add Watcher Wizard]
    W --> BW[Bulk Add Wizard]
    WD --> WA[Assigned account and checkout profile]
    WD --> WI[Assigned AI connection and policy]
    WD --> WL[Watcher live events]
~~~

A central resource list is acceptable. **Its usage is not global:** the user chooses an account, profile and AI policy **inside each watcher**.

---

## 6. Visual system, accessibility and UI conventions

### Design direction

Premium dark operations software: calm, consistent, precise and readable. Avoid flashy trading/casino imagery, excessive gradients and meaningless charts. Every visual signal should carry information.

**Proposed color system (illustrative):**

| Token | Hex | Meaning |
|---|---|---|
| background | #080E19 | Dark canvas |
| surface | #18283D | Panels/cards |
| success | #5EE8BD | Actively verified / healthy |
| information | #8EB9FF | Informational / paused |
| attention | #FFCF88 | Requires review, uncertainty |
| error | #F49597 | Genuine failure / blocked |

Use these colors consistently with text labels and icons; don't convey state by color alone.

### UI conventions

- Product photo/thumbnail and retailer are visible in watcher lists and details.
- Use one type scale, consistent spacing, button hierarchy and component library.
- Avoid overwhelming beginners with technical account, proxy, network or browser controls.
- Labels must say **Start**, **Pause**, **Edit**, **Test Connection**, **Review**, **Delete**, not cryptic abbreviations.
- Show explanatory tooltips for maximum price, quantity, budgets, monitoring mode, AI help and session assignment.
- Show progress, empty states, retry instructions and failure details.
- Clearly distinguish *engine online* from *a particular watcher monitoring successfully*.
- Keep keyboard navigation, focus indicators, readable contrast and accessible names.
- Advanced diagnostics belong in a collapsible section or details drawer.

**Example tooltips:**

- **Maximum item price:** "The most you're willing to pay for one item. Greedy will not buy above this price."
- **Quantity limit:** "The maximum number of this product the watcher may purchase."
- **AI assistance:** "AI can help understand page changes. It cannot change your spending rules."
- **Retailer account:** "The account this watcher uses at this store. Other watchers keep their own assignments."

---

## 7. Concept screen 01 — Overview

The Overview must answer four questions at a glance:

1. Is the engine online and checking products?
2. How many watchers are actively monitoring?
3. What needs user attention?
4. How much money is spent, held or available under relevant limits?

### Text wireframe (fictional example data)

~~~text
+-----------------------------------------------------------------------+
| GREEDY   Overview                                       Engine: ONLINE |
+----------------+----------------+----------------+-------------------+
| 12 WATCHERS    | 8 MONITORING   | 2 NEED REVIEW  | $84 / $250 TODAY |
+----------------+----------------+----------------+-------------------+
| LIVE WATCHERS                     | NEEDS YOUR ATTENTION           |
| [img] Collector Box / Pokemon     | Bandai page layout changed    |
|       MONITORING                  | Stock cannot be confirmed      |
| [img] Red Action Figure / Bandai  | [OPEN WATCHER]                 |
|       NEEDS REVIEW               |                                |
| [img] Card Bundle / Target        | LATEST ACTIVITY                |
|       PAUSED                     | 14:06 Target checked           |
|                                  | 14:05 Pokemon price verified   |
|                                  | 14:04 Bandai needs review      |
+----------------------------------+--------------------------------+
| Spending protection ON | Unresolved attempts: 0 | AI: connected    |
+-----------------------------------------------------------------------+
~~~

Values above are sample content, not actual Greedy telemetry.

**Behavior requirements:** Each metric is backed by the engine/event state. Every card opens a corresponding watcher, alert, ledger entry or integration. If events are stale or the connection dropped, show **STALE**, **OFFLINE**, or **RECONNECTING** instead of deceptively "ONLINE".

---

## 8. Concept screen 02 — Watchers

This is the primary workspace. The product should be the first thing a user recognizes, status the second, controls the third.

### Text wireframe (fictional values)

~~~text
GREEDY > WATCHERS                                      [+ ADD WATCHER]
Search products, shops or groups...                    [BULK ADD]

[All: 12] [Pokemon Center] [Target] [Walmart] [Bandai]
[Monitoring] [Paused] [Needs review] [Group: TCG drops]

PRODUCT / WATCHER               STATUS          PRICE       LAST EVENT
[img] Collector Box / Pokemon   MONITORING      $119.99     Checked 8s ago   [Edit]
[img] Action Figure / Bandai    NEEDS REVIEW   Unknown     Layout changed  [Review]
[img] Retail Bundle / Target    PAUSED          $39.99      Paused by user  [Start]
[img] Console Kit / Walmart     MONITORING      $299.00     Out of stock    [Edit]

[ ] Select all  [Pause selected] [Edit selected] [Duplicate] [Delete]
~~~

### Required watcher row or card fields

- Thumbnail, generated/editable name, retailer and product URL.
- Current mode (Manual, Suggest, Auto).
- Engine state and accurate latest activity.
- Last verified price and age of observation; currency.
- Availability and source confidence.
- Assigned retailer account and AI status, where relevant.
- Start, Pause, Edit, Duplicate and View Activity actions.

Support search, filters, tags/groups and bulk operations. Confirm the affected watchers before potentially destructive or purchasing-related batch changes.

---

## 9. Concept screen 03 — Individual Watcher

The watcher detail view is a self-contained control center. The owner should not need to search global Settings to understand one bot.

### Text wireframe (fictional values)

~~~text
Collector Box / Pokemon Center       Watcher W-0023      [PAUSE] [EDIT]

[img] Collector Box - UPC            Latest verified price: $119.99
      Product URL verified           Checked 8 seconds ago
      STATUS: MONITORING              Stock: not available

WATCHER RULES                        LIVE TIMELINE
Mode: Suggest / never buy            14:06:03 Product page checked
Max unit price: $130.00                       Price unchanged
Quantity cap: 2 units total          14:05:56 Inventory checked
Daily spend cap: $250.00                      Not available
Retailer account: None               14:05:42 AI: no action needed
Checkout profile: None                         Budget rules unchanged
AI: On - diagnose only

[VIEW FULL ACTIVITY] [VIEW PRODUCT] [ADVANCED OPTIONS]
~~~

**Required behavior:**

- Price, image and stock must be tied to timestamped observations.
- If the parser fails, show what failed and whether buying is paused.
- Edit configuration without recreating a watcher.
- Explicitly assign account/profile and optional AI policy per watcher.
- Provide a detailed log alongside a short plain-language summary.
- Explain whether the watcher is currently checking, sleeping until next check, paused, blocked or needs human attention.

---

## 10. Concept screen 04 — Single-watcher creation wizard

**Design target:** A successful supported-URL setup should be achievable in under one minute by a novice, when metadata is available. This is a UX goal, not an already measured result.

### User journey

~~~mermaid
flowchart TD
    A[Click Add Watcher] --> B[Paste supported retailer URL]
    B --> C{URL is supported and safe?}
    C -->|No| D[Explain unsupported retailer or invalid URL]
    C -->|Yes| E[Load verified product metadata]
    E --> F{Price and identity verified?}
    F -->|Yes| G[Show title, image, retailer, price and age]
    F -->|Partial or no| H[Show UNKNOWN fields and explicit reason]
    G --> I[Edit generated watcher name]
    H --> I
    I --> J[Choose Alert Me, Suggest or Auto-buy]
    J --> K[Configure limits and optional resources]
    K --> L[Review plain-language summary]
    L --> M{Safety requirements satisfied?}
    M -->|No| N[Keep paused or ask for missing setup]
    M -->|Yes| O[Start watcher]
~~~

### Wizard steps

**Step 1 — Paste link:** One large input labeled "Paste a product link." Validate the retailer against the existing four supported retailers. Never claim every website works.

**Step 2 — Confirm product:** Automatically propose a watcher name and show the image, product name, retailer, current price, currency, availability and observed timestamp where verified. Missing metadata must display **Unknown** or **Unavailable**, never fabricated data.

**Step 3 — Choose behavior:**
- **Alert Me / Manual:** Notify only; no checkout.
- **Suggest:** Monitor and recommend actions; no checkout.
- **Auto-buy:** May attempt an authorized purchase after verifying all rules, account/profile and hard spending gate.

**Step 4 — Limits and resources:** Simple fields for maximum unit price, quantity limit, day/week/overall spending caps (as supported), account, checkout profile and optional AI. Hide advanced options until expanded.

**Step 5 — Review and start:** Display a human-readable authorization summary. For Auto, make buying consent and caps explicit. Never silently change mode or allow unknown prices to proceed to purchase.

---

## 11. Concept screen 05 — Multi-watcher and CSV import wizard

Beginner workflow: paste five links. Power-user workflow: import a large CSV. Both should lead to the same reliable preview.

~~~mermaid
flowchart TD
    A[Paste URL list or import CSV] --> B[Parse input]
    B --> C[Check supported domain and URL]
    C --> D[Resolve product when possible]
    D --> E[Detect duplicates and errors]
    E --> F[Preview every row and reason]
    F --> G[Choose default monitoring rules]
    G --> H[Review before confirming]
    H --> I[Create watchers in PAUSED state]
    I --> J[Explicitly start selected watchers]
~~~

### Preview example (illustrative)

| Input | Validation | Result |
|---|---|---|
| Target product link | Supported and parseable | Ready to add, Alert Me |
| Walmart product link | Supported and parseable | Ready to add, Alert Me |
| Bandai product link | Supported and parseable | Ready to add, Alert Me |
| unsupported.shop/item | Not a supported retailer | Rejected with explanation |

Show counts for **valid, rejected and duplicates**. A rejected item must not secretly create a watcher. Duplicate detection should identify whether a watcher already exists and what user action is available.

**Default:** Bulk-added watchers start **PAUSED** until reviewed. Import must never silently authorize purchases.

### Operational requirements

- Support paste, CSV upload, preview, validation, cancel and confirm.
- Process large imports incrementally without freezing the interface.
- Provide row-specific errors and optional safe export of rejection reasons.
- Support selecting multiple watchers and applying shared, previewed changes.
- Preserve successful entries without forcing a full re-import when some rows fail.

---

## 12. Retailer accounts and profiles — assign per watcher

**Core correction:** Accounts may be stored centrally for reuse, but there must be **no invisible global retailer login that every watcher inherits**.

### Architecture diagram

~~~mermaid
flowchart LR
    subgraph Vault[Protected local resource vault]
      RA[Retailer accounts]
      BS[Browser sessions]
      CP[Checkout profiles]
    end
    subgraph Config[Explicit watcher assignments]
      W1[Watcher A config]
      W2[Watcher B config]
      W3[Watcher C config]
    end
    RA --> W1
    RA --> W2
    RA --> W3
    CP --> W1
    CP --> W2
    BS --> W1
    BS --> W2
    BS --> W3
    W1 --> E1[Watcher A isolated execution]
    W2 --> E2[Watcher B isolated execution]
    W3 --> E3[Watcher C isolated execution]
~~~

Connections shown above are *possible explicit assignments*, not a global default. A shared account/session must be intentional.

### Resource boundaries

| Resource | Meaning |
|---|---|
| Retailer account | Named credentials for one retailer |
| Browser session | Signed-in browser data for a retailer; may expire |
| Checkout profile | Shipping/billing references and protected payment reference |
| Watcher assignment | Explicit account/profile IDs plus selected session policy |
| Watcher execution | Uses only permitted resources and the selected session context |

Example:

| Watcher | Retailer account | Checkout profile |
|---|---|---|
| Pokémon watcher A | Pokémon account 1 | Profile A |
| Pokémon watcher B | Pokémon account 2 | Profile B |
| Target watcher C | Target account 1 | Profile A |

### Security and lifecycle

- Use supported OS-backed credential storage or encrypted resources.
- Never save plaintext passwords or full payment-card details in ordinary config JSON.
- Redact tokens, credentials and sensitive checkout data from UI events and exported logs.
- Display account/session states such as **Untested**, **Valid**, **Expired**, **Needs Sign-In**, **Error**.
- Enforce retailer compatibility (e.g., no Walmart account attached to a Target watcher).
- Clearly define whether the same session may be shared, and isolate contexts accordingly.
- Prevent editing one watcher's account assignment from silently changing another's assignment.

**Crucial distinction:** "Account settings belong to the watcher" refers to assignment and usage; storage can still be reusable.

---

## 13. AI connections — verified lifecycle, not a saved toggle

The owner reports that connecting Claude currently gives no credible proof of success.

The existing project sheet describes reading Claude Code local credentials (including files under ~/.claude) to reuse a Claude subscription token. That approach must **not** be presumed a supported third-party integration. For third-party applications, use currently documented, authorized provider/API authentication; avoid repurposing Claude Code subscription sessions.

A Claude subscription does **not** automatically equal Anthropic API access.

### Suggested connection lifecycle

~~~mermaid
flowchart TD
    A[AI Connections] --> B[Choose supported provider]
    B --> C[Enter credential by supported method]
    C --> D[Store encrypted credential reference]
    D --> E[Test with a genuine small provider request]
    E --> F{Valid response?}
    F -->|Yes| G[CONNECTED: show provider model time latency]
    F -->|No| H[REJECTED or UNAVAILABLE: actionable error]
    G --> I[Allow assignment to individual watchers]
    H --> J[Retry or use no-AI rules]
    I --> K[Health may change over time]
    K --> E
~~~

### State definitions

| State | User-facing meaning |
|---|---|
| Not configured | No credential or supported provider configured |
| Testing | Real request in progress |
| Connected | A genuine provider request succeeded recently |
| Unavailable | Timeout, quota issue, model not available or network failure |
| Rejected | Authentication failed or unsupported credential |

The connection screen should expose provider name, chosen model, last successful test, latency, timestamp, error message, safe retry action and per-watcher assignment.

**Never** display Connected merely because a key was saved.

### No-AI fallback

Manual and rule-based monitoring must work with **zero configured AI**. A failed AI call should produce a truthful diagnostic event and fall back safely to deterministic behavior.

---

## 14. AI-powered watcher diagnosis — propose, validate, gate, act

AI should be useful beyond chat, without becoming an untrusted automation authority.

~~~mermaid
flowchart LR
    A[1 Observe product page] --> B[2 Detect price stock or layout change]
    B --> C[3 Optional AI diagnosis on redacted observations]
    C --> D[4 Validate structured schema and action allowlist]
    D --> E[5 Deterministic policies and hard spending gate]
    E --> F[6 Approved action or user-visible alert]
    C -.-> G[Fallback to non-AI rules]
    G --> D
~~~

### Appropriate AI tasks

- Explain an unusual page layout, missing element or changed copy.
- Categorize a page change and recommend a safe **already implemented** parsing strategy.
- Summarize what was observed and confidence level.
- Explain why a watcher needs user review.
- Guide a novice through configuration using understandable questions.
- Suggest pausing a watcher until a parser is verified.

### Forbidden AI authority

AI must never independently:

- Change or remove hard price, quantity, day/week or total spend limits.
- Change a watcher into Auto-buy without explicit authorization.
- Override the money gate.
- Invent a product price, stock state, order status or site capability.
- Bypass retailer access/security challenges.
- Execute arbitrary browser actions or new parsing code without validation.
- Declare a purchase successful based solely on a button click.
- Conceal uncertainty, failed connections or parser changes.

### Decision contract

Pass AI a **minimal, redacted, structured observation**, not raw credentials. Require a strict response schema such as classification, explanation, confidence and **allowlisted proposal**. Reject malformed, unsafe or unsupported proposals. Log whether a suggestion was **applied**, **rejected**, or **shown for human review**.

The real watcher executes only tested actions authorized by deterministic code.

---

## 15. Real-time Activity — one shared source of truth

The Activity screen must reflect actual runtime behavior of individual watchers and Greedy as a whole. It should not be a collection of UI-only mock statuses.

### Event architecture

~~~mermaid
flowchart LR
    WW[Watcher worker] --> EV[Normalized event]
    EV --> ES[Local append-only event store]
    ES --> UI[Live UI subscriptions]
    UI --> OVER[Overview counters]
    UI --> WATCH[Watcher list and detail timeline]
    UI --> FEED[Global Activity feed]
    ES --> AUDIT[Audit history and recovery]
~~~

### Suggested event schema

- event_id (monotonic or otherwise orderable)
- watcher_id (nullable for global engine events)
- type (started, checked, stock, price, page_changed, ai_diagnosis, buy_blocked, checkout_attempt, purchase_confirmed, unknown_checkout, account_expired, error)
- severity
- timestamp and local display conversion
- correlation_id and optional attempt_id
- redacted/safe payload
- readable message
- sequence and source information

### Watcher state model

~~~mermaid
stateDiagram-v2
    [*] --> Draft
    Draft --> Paused: saved
    Paused --> Starting: start
    Starting --> Monitoring: checks ready
    Starting --> NeedsReview: missing configuration
    Starting --> Error: startup failure
    Monitoring --> Waiting: check completed
    Waiting --> Monitoring: next check
    Monitoring --> NeedsReview: layout or auth problem
    Waiting --> NeedsReview: stale / inaccessible
    Monitoring --> Paused: pause
    Waiting --> Paused: pause
    NeedsReview --> Starting: reviewed and restart
    Error --> Starting: retry
    NeedsReview --> Paused: pause
    Error --> Paused: pause
~~~

**Always display:** current state, latest successful check, latest event, next planned check, exact failure reason, and whether the engine is currently online.

If the UI loses connection to the engine, show **Disconnected/Reconnecting** and observation ages. Do not continue displaying a falsely live green state.

### Activity UX

- Live streaming feed, grouped and searchable by watcher/retailer.
- Filters for informational, changes, warnings, errors, AI decisions and purchasing.
- Plain-language messages with expandable technical details.
- Links from every event to its watcher and related purchase/ledger record.
- Secret-safe export only.

---

## 16. Product recognition, thumbnails and page-layout drift

A pasted URL is an input, **not** proof that the page has readable price, image or stock data.

### Extraction pipeline

~~~mermaid
flowchart TD
    A[URL supplied] --> B[1 Validate supported domain and safe path]
    B --> C[2 Resolve product ID and variant]
    C --> D[3 Extract title thumbnail price stock currency timestamp]
    D --> E[4 Compare against prior observations]
    E --> F{Changed or unparseable?}
    F -->|No| G[Publish verified observation]
    F -->|Yes| H[5 Diagnose content or structural drift]
    H --> I[Optional constrained AI explanation]
    I --> J[6 Mark verified unavailable or unknown]
    G --> K[Update watcher and Activity]
    J --> K
~~~

### Observability states

- **Verified:** Current observation was successfully extracted under tested rules.
- **Unavailable:** The requested field is not present or not offered on the page.
- **Unknown:** The engine could not reliably infer the field.
- **Stale:** A former verified observation exists but is too old to support an immediate action.

A previously known price should not quietly be shown as current during a parser failure.

When a page's structure changes, log the difference, attempt only validated fallback parsers, and flag **Needs Review** if confidence is inadequate. Unverified prices/availability must **block automatic checkout**.

### Per-retailer contract

Have a dedicated parser and fixture suite for each of the four supported retailers. Every new page type and fallback should ship with a realistic test fixture and an explicit known limitation. Do not claim universal website support.

---

## 17. Purchasing gate — preserve the hard safety boundary

The existing Greedy sheet describes the money gate as the only way to authorize purchases. The redesigned architecture must preserve that invariant.

### Purchase execution diagram

~~~mermaid
flowchart TD
    A[Verified available product event] --> B{Watcher in Auto mode?}
    B -->|No| Z[Alert or Suggest only]
    B -->|Yes| C{Price and product verified?}
    C -->|No| X[Block and report unknown data]
    C -->|Yes| D{Account profile quantity and caps valid?}
    D -->|No| Y[Block and explain]
    D -->|Yes| E{New unique purchase attempt?}
    E -->|No| DUP[Reject duplicate]
    E -->|Yes| F[Durably reserve budget allowance]
    F --> G[Execute authorized checkout once]
    G --> H{Outcome reliably known?}
    H -->|Confirmed order| I[Commit actual amount once]
    H -->|Definitive failure| J[Release or adjust hold]
    H -->|Uncertain or timed out| K[Keep unresolved and reconcile]
~~~

### Purchase hard stops and correct behavior

| Condition | Required action |
|---|---|
| Unknown/stale price | Block purchase; log reason |
| Price or quantity cap exceeded | Refuse and log |
| Day/week cap exceeded | Refuse and log |
| Duplicate attempt | Reject by durable idempotency key |
| Checkout outcome uncertain | Keep hold unresolved and reconcile; **do not blindly retry** |
| Global kill switch active | Block all new purchase attempts |
| AI disagrees with limits | Reject AI suggestion; rules win |

### Invariants to test

1. **Before checkout:** A durable budget reservation exists.
2. **On confirmed success:** The actual charge is recorded exactly once.
3. **On definitive failure:** The hold is adjusted/released only with reliable evidence.
4. **On timeout:** The result becomes unknown, not automatically failed/free to retry.
5. **Across restarts:** Ledger, holds, attempt identifiers and unknown outcomes survive.
6. **On kill switch:** No new checkout operation begins.

Never confuse "clicked purchase" with **a confirmed order**.

Use only supported and permitted retailer actions; do not bypass security challenges or site policies.

---

## 18. Proposed logical data model (not a claimed current schema)

| Entity | Proposed important fields |
|---|---|
| **Watcher** | id, title, product_url, retailer, product_id, mode, group_id/tags, account_id, checkout_profile_id, ai_policy_id, limits, status |
| **Product** | id, retailer, retailer_product_id, variant, title, image_url, price, currency, stock, observed_at, verification_source |
| **RetailerAccount** | id, retailer, display_label, secure_credential_ref, session_id, verified_at, status, expiry |
| **CheckoutProfile** | id, label, shipping_ref, billing_ref, protected_payment_ref, verification_state |
| **AIConnection** | id, provider, model, secure_key_ref, enabled, last_test_result, last_test_at |
| **WatcherEvent** | id, watcher_id, type, severity, timestamp, safe_payload, correlation_id, sequence |
| **BudgetLedger** | id, hold_id, watcher_id, time_period, amount, currency, state (held/done/unknown), audit_sequence |
| **PurchaseAttempt** | idempotency_key, watcher_id, state, budget_reservation_id, order_reference, created_at, reconciled_at |

### Key relationships

~~~mermaid
erDiagram
    WATCHER }o--|| PRODUCT : monitors
    WATCHER }o--o| RETAILER_ACCOUNT : assigns
    WATCHER }o--o| CHECKOUT_PROFILE : assigns
    WATCHER }o--o| AI_CONNECTION : uses
    WATCHER ||--o{ WATCHER_EVENT : emits
    WATCHER ||--o{ BUDGET_LEDGER : reserves
    WATCHER ||--o{ PURCHASE_ATTEMPT : attempts
    PURCHASE_ATTEMPT }o--o| BUDGET_LEDGER : references
~~~

The exact storage representation (JSON, local database, append-only logs, browser profile directories) should be decided *after* the source audit.

### Safe migration plan

- Inventory old configuration, watcher fields, browser profiles, ledger and events.
- Version data schema.
- Back up current state.
- Migrate existing global account references into explicit, visible watcher assignments only when the mapping is known and safe.
- Treat ambiguous assignments as **unassigned and needs review**, never silently reassign sign-ins.
- Preserve audit/ledger semantics and rollback or repair capabilities.
- Test upgrade from a realistic existing user-state fixture.

---

## 19. Execution roadmap — preserve, rebuild, prove

**Implementation phases are an ordering recommendation, not a claim that the work has been done.** Changes should be staged behind flags or equivalent safe rollout mechanisms where necessary.

### Phase 0 — Audit and baseline

- Inspect actual source, IPC bridge, storage, browser adapters, account state, AI integration, money gate and packaged executable.
- Run existing suites; record successes, failures and reproducible fixture behavior.
- Identify architectural mismatches and obsolete descriptions.

**Exit criterion:** A reproducible baseline and source-backed inventory.

### Phase 1 — Data and account boundaries

- Design explicit watcher assignment fields, resource vault, session isolation and checkout profiles.
- Implement secure secret storage and versioned migration.
- Preserve old records and prevent global sign-in leakage.

**Exit criterion:** Two watchers can retain different account/session assignments correctly.

### Phase 2 — Watcher-first interface

- Shared design system.
- Overview, Watcher list/details, Activity, Spending, Accounts/Profiles, AI Connections and Settings.
- Single-URL wizard and validated bulk wizard.
- Tooltips, state badges, accessible navigation and empty/error states.

**Exit criterion:** A novice can add and understand a supported watcher without technical setup knowledge.

### Phase 3 — Genuine live observability

- Normalized event schema and durable event store.
- UI subscriptions and shared state.
- Explicit watcher state machine and offline/stale handling.

**Exit criterion:** No fake live statuses; the same event appears consistently in all views.

### Phase 4 — AI and page diagnostics

- Supported AI authentication, real connection test, status and model visibility.
- Constrained AI proposals, structured validation and no-AI fallback.
- Per-retailer drift detection, safe fallback parser handling.

**Exit criterion:** Verified provider status and safe, logged AI analysis without spending-rule bypass.

### Phase 5 — Purchase safety, packaging and release

- Budget invariants, idempotency, uncertain-outcome reconciliation.
- Crash and restart recovery.
- Real packaged portable Windows executable tests.
- Compare before/after behavior with baseline.

**Exit criterion:** All critical release gates pass with recorded evidence, not claims.

---

## 20. Acceptance tests — define "done" before coding

Each item requires reproducible evidence from the actual code and executable.

| Area | Required acceptance proof |
|---|---|
| **Simple onboarding** | One supported URL becomes a watcher with verified metadata or explicit unknown state |
| **Bulk import** | Mixed valid/invalid/duplicate inputs produce an accurate preview; no surprise actions |
| **Scoped accounts** | Two watchers may use distinct retailer accounts; changing one does not silently affect the other |
| **AI connection** | Valid credential passes real minimal provider request; invalid fails; no-AI fallback works |
| **Real-time UI** | Engine event updates watcher row, detail timeline and Activity feed consistently |
| **Site redesign** | Parser drift produces human-readable Needs Review state and blocks unverified Auto-buy |
| **Money safety** | Caps enforced before checkout; duplicate/unknown outcomes never cause unintended retries |
| **Crash recovery** | Watchers, ledger, unresolved attempts, events and selected sessions persist appropriately |
| **Accessible UX** | Simple labels, keyboard navigation, tooltips, progress indicators and useful errors |
| **Packaging** | Portable Windows executable launches and works without external development tools |

### Minimum test scenarios to include

1. Supported URL succeeds; generated name and thumbnail appear.
2. Unsupported URL rejected with explicit retailer limitation.
3. Product without extractable price displays Unknown and cannot auto-buy.
4. Watcher edit persists across restart.
5. Bulk import of duplicates, invalid URLs and successful URLs has expected counts.
6. Two different assigned retailer accounts remain isolated.
7. Expired session is accurately shown and blocked where necessary.
8. AI key succeeds and displays timestamp/model.
9. Incorrect AI key fails visibly.
10. Offline AI does not stop rule-only monitoring.
11. Page layout changes trigger Needs Review without speculative purchase.
12. Price/quantity/day/week caps block purchase reliably.
13. Purchase attempt is idempotent across UI retries and engine restart.
14. Unknown checkout outcome remains unresolved; no blind retry.
15. Kill switch prevents new purchase execution.
16. Live UI disconnect shows stale/offline status.
17. Redacted activity export contains no credentials.
18. Packaged executable passes fixture-based end-to-end tests.

### Evidence to demand

Exact commands/tests run, counts and failures, fixture conditions, before/after screenshots, a file-change summary, stored-data migration checks, known limitations, and proof from the packaged executable.

Do not accept screenshot-only "proof" for invisible backend behavior.

---

## 21. Risk register and mitigations

| Severity | Risk | Mitigation |
|---|---|---|
| **P0 Critical** | Claude subscription token reused in a third-party app | Remove direct ~/.claude credential reuse; use officially supported authentication |
| **P0 Critical** | Unknown checkout result treated as failure and retried | Persist unresolved hold and reconcile; never blindly retry |
| **P0 Critical** | Cross-watcher retailer session leakage | Explicit watcher assignments and isolated session contexts |
| **P0 Critical** | Duplicate purchases or stale price | Durable idempotency, verified price and money reservation |
| **P1 High** | UI claims live health when stale | Event-backed status, ages, disconnect/reconnect handling |
| **P1 High** | Retailer price/stock parsing breaks | Per-retailer contracts, fixtures and visible drift alerts |
| **P1 High** | Secrets or payment data appear in logs | Protected storage, redaction, restricted diagnostics/exports |
| **P2 Medium** | Group workflow overwhelms novices | Optional groups and one-link creation path |

A polished interface must not hide uncertainty or make unsafe behavior easier.

---

## 22. Copy-ready coding-agent brief — Part 1

> **ROLE:** You are the staff product designer, senior Electron/TypeScript engineer, system architect and QA lead for **GREEDY**, whose runtime is called the **greedy engine**. Your mission is a complete functional and product redesign — **not** merely a CSS facelift.
>
> **CONTEXT:** Read the owner's four-page Greedy project sheet and this complete redesign analysis. Inspect the real Greedy source repository and executable before modifying code. The GitHub repository hosting this Markdown file is a **publishing location only**, not verified to be the Greedy source.
>
> **FIRST TASK:** Independently critique this report. Explain which recommendations you support, which you would change, and why, using findings from the actual implementation. Distinguish verified facts from assumptions, proposed features and missing dependencies.
>
> **MISSION:** Create a premium, professional, watcher-first monitoring application inspired by Stellar AIO's task organization but significantly easier for beginners. Preserve the four supported retailers: Pokémon Center, Bandai, Walmart and Target. Preserve the local portable Windows architecture, rules-only fallback, money gate and current working functionality. Do not invent support for other sites.
>
> **DESIGN:** Implement coherent Overview, Watchers, Watcher Details, Activity, Spending, Accounts & Profiles, AI Connections and Settings interfaces. Use consistent components, accessible text, accurate statuses and meaningful tooltips. Do not hide advanced complexity in the initial wizard.
>
> **SINGLE WATCHER:** From a supported product URL, obtain verified product title, photo, price and current availability where available. Generate an editable name. Let the owner choose Alert Me/Manual, Suggest or Auto; set hard purchase limits; explicitly assign a relevant retailer account, checkout profile and optional AI policy; review and start. Show Unknown where verification fails.
>
> **MULTIPLE WATCHERS:** Support pasted lists and CSV imports with validation, duplicate detection, preview and precise rejection reasons. Never auto-start unreviewed imported watchers. Provide bulk pause, edit, duplicate, group/tag and delete with affected-item confirmation.
>
> **SCOPED ACCOUNTS:** Keep reusable retailer accounts and checkout profiles in secure storage but assign them **inside each watcher**. Eliminate silent global retailer login behavior. Isolate browser sessions or document safe explicit sharing. Surface session validity and expiration.
>
> **TRUTH:** Never label a connection "Connected", a watcher "Live", a product "In Stock", a price "Verified" or an order "Purchased" unless the underlying operation produced real evidence. Display observation timestamp/source and clearly mark stale or unknown values.

---

## 23. Copy-ready coding-agent brief — Part 2

> **AI CONNECTIONS:** Inspect current src/auth.ts and determine whether it uses Claude Code subscription credentials. Do not copy or reuse local ~/.claude tokens for unauthorized third-party calls. Implement supported Anthropic API-key or other documented provider authentication. Run a genuine minimal test request; show provider, model, result, timestamp and error. Encrypt secrets, redact logs, and preserve no-AI operation.
>
> **AI BEHAVIOR:** AI may diagnose page changes, classify observations, explain errors and propose an approved known response. All suggestions must pass schema validation and an action allowlist. AI cannot alter purchase caps, enable Auto without consent, bypass security challenges, authorize payment or override the deterministic money gate. Explain which suggestions were applied or rejected.
>
> **LIVE ACTIVITY:** Create normalized events with one source of truth. Surface current state, last check, last event, next action and actionable error on Watchers, individual watcher detail and global Activity. Filter by watcher, shop, severity and type. Keep historical events and export them with secrets redacted. Truthfully show stale, offline and reconnecting states.
>
> **PURCHASING:** Preserve reserve-before-attempt semantics, kill switch, durable ledger, quantity and per-item/day/week limits, and UNKNOWN outcomes. Add or verify idempotency and reconciliation. Never treat clicking a checkout button as proof of purchase. An uncertain result must remain unresolved and must not be blindly retried.
>
> **PRODUCT EXTRACTION:** Keep retailer-specific parsing for the current four sites. Validate URL, product ID/variant, image, price, stock and observation timestamp. Detect page/layout drift and suspend unverified automatic checkout. Write tests with fixtures for changed page structure.
>
> **ENGINEERING WORKFLOW:** Audit existing modules and IPC boundaries; define schema migrations; implement in logical phases; run unit/integration/fixture and real packaged-EXE tests. Preserve existing settings, sessions, ledger and watcher state. Report exact passes and failures, before/after screenshots, files changed, limitations and a novice-facing walkthrough.
>
> **RESPONSE CONTRACT:** First (1) critique this report; then (2) propose better ideas with reasons; (3) identify what actually exists, is broken or missing; (4) present architecture, migration and test plan; (5) implement the highest-value safe changes; (6) show evidence, not aspirational claims. Do not ask unnecessary clarification for ordinary design decisions, but never silently change purchasing permissions or security-sensitive behavior.
>
> **NO FAKE PROGRESS:** Do not substitute decorative mockups, stubbed connections, hard-coded prices, placeholder statuses or untested UI flows for working functionality. If source access, a supported integration or a retailer capability is absent, say so plainly.
>
> **SUCCESS CONDITION:** A nontechnical eighth grader can safely paste a supported URL, understand and configure a watcher, verify that it is working, and diagnose any problem; power users can efficiently operate many watchers without sacrificing transparency or budget safety.

---

## 24. Sources, assumptions and boundaries

### Source materials

- **[G1] Owner-supplied**: *greedy-sheet.pdf* (four pages, dated November 2026). Source for the described current product, supported retailers, file names, existing safety design and reported test counts. This source is not public in this repository.
- **[S1] Stellar AIO Tasks Tab:** https://guides.stellaraio.com/stellar/navigating-stellaraio/what-is-the-tasks-tab
- **[S2] Stellar AIO Accounts Tab:** https://guides.stellaraio.com/stellar/navigating-stellaraio/what-is-the-accounts-tab
- **[S3] Stellar AIO Profiles Tab:** https://guides.stellaraio.com/stellar/navigating-stellaraio/what-is-the-profiles-tab
- **[S4] Stellar AIO Dashboard:** https://guides.stellaraio.com/stellar/navigating-stellaraio/what-is-the-dashboard-tab
- **[A1] Anthropic / Claude Code legal and compliance:** https://code.claude.com/docs/en/legal-and-compliance
- **[A2] Anthropic API authentication:** https://platform.claude.com/docs/en/manage-claude/authentication
- **[A3] Anthropic API overview:** https://platform.claude.com/docs/en/api/overview

### Interpretation notes

1. This is a **design and engineering recommendation**, not a security audit, source audit or test run.
2. The 24-page original PDF contained illustrated layouts; the layouts above are **text representations**, and Mermaid diagrams represent the intended architecture, not confirmed live wiring.
3. Product names, thumbnails, counts, timestamps, budgets and prices used in the wireframes are fictional examples.
4. The owner's current claims may be stale or incomplete and must be checked against source.
5. Retailer automation, checkout and session handling must comply with applicable law, terms, and approved authentication methods. Do not implement CAPTCHA, fraud, rate-limit or access-control bypasses.
6. Do not publish API keys, retailer passwords, browser-session files, card data or the owner's private project sheet when implementing this report.

---

## How to give this to the next AI agent

Provide the **raw Markdown URL** for this file and instruct the agent:

> "Read the entire Greedy redesign analysis at this raw URL. Then independently critique it against the real Greedy codebase, propose improvements, and implement the safest highest-priority changes with actual test evidence. The repository hosting the report is not the Greedy codebase."

**End of Greedy product redesign and engineering analysis.**
