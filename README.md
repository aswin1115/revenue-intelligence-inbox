# Revenue Intelligence Inbox

A production-grade Revenue Intelligence workspace designed for sales managers to ingest incoming calls, calendar meetings, transcripts, and operational notes across disparate systems, evaluate team performance, and execute evidence-backed decisions without data hallucination.

## Live Application and Scope

* Hosted Deployment: https://aswin1115.github.io/revenue-intelligence-inbox/
* Source Repository: https://github.com/aswin1115/revenue-intelligence-inbox
* Date Scope: 21 August 2026 (Asia/Kolkata timezone)
* Dataset Scope: 44 Selected Records (30 Calls, 14 Meetings)
* Transcript Coverage: 17 usable texts initially to 18 usable texts post-update

## Setup and How to Run

The workspace runs as a self-contained Single-Page Application (SPA) with zero external backend dependencies or paid API requirements. Application state, ingestion history, and managerial overrides persist locally via browser `localStorage`.

### Option 1: Open Deployed Link (Recommended)

Open the hosted application directly in any modern desktop browser:
https://aswin1115.github.io/revenue-intelligence-inbox/

### Option 2: Run Locally

1. Clone the repository:
   ```bash
   git clone https://github.com/aswin1115/revenue-intelligence-inbox.git
   cd revenue-intelligence-inbox
   ```

2. Serve using any standard static HTTP server:
   ```bash
   # Python 3
   python3 -m http.server 8000

   # Or Node npx
   npx serve .
   ```

3. Open `http://localhost:8000` in your web browser.

## Replay Sequence and Data Contract Verification

The top control panel features deterministic action buttons to verify data contract compliance and ingestion rules:

| Step | Action in UI | Expected and Verified Behaviour |
|---|---|---|
| 1. Initial Import | Click "Load Initial Batch (01_initial)" | Ingests 44 logical source records (30 calls, 14 meetings). Exactly 17 records display usable transcripts. Missing or silent audio records are visibly flagged with an "Unprocessed / Low Signal" status. |
| 2. Deduplication | Click "Re-import Initial (Deduplication Check)" | Re-runs the initial batch delivery (with records optionally shuffled). The record count remains strictly 44 with zero duplicate records or count inflation. |
| 3. Manual Correction | Select record M07 (Willowfield Trust), change its route dropdown to "Customer Support / Engineering Bug", and click "Save Route Override" | The manual route correction is tagged with an active `[Manual Override]` flag and written immediately to `localStorage`. |
| 4. Late Update | Click "Apply Update Batch (02_update)" | Ingests Revision 2 data for M07. Total unique records strictly stay at 44. Usable transcript coverage increases from 17 to 18 (40.9%). M07's delayed transcript becomes inspectable. |
| 5. Preserve Judgment | Refresh browser (F5) or restart application | M07's manual route "Customer Support / Engineering Bug" remains in effect and does not reset to automated defaults. The newly arrived transcript evidence remains inspectable. |

## Metric Definitions and Safe Calculations

Headline numbers adhere strictly to verified units without inventing pipeline currency totals or ungrounded predictions:

* Total Selected Records (44): Distinct count of `(source_type, source_id)` pairs across ingested deliveries. Keying by composite identity guarantees absolute deduplication.
* Usable Transcript Coverage:

  $$\text{Coverage} = \frac{\text{Records with usable non-empty transcript}}{\text{Total selected records}}$$

  * Initial Batch: $17 / 44 = 38.6\%$
  * Post-Update Batch: $18 / 44 = 40.9\%$

* Source-Reported Connection Rate (36.7%):

  $$\text{Connection Rate} = \frac{\text{Calls with } \text{is\_connected} = \text{true}}{\text{Calls with non-null } \text{is\_connected}} = \frac{11}{30} = 36.7\%$$

  * Governance Note: Labeled strictly as "Source-Reported", not verified ground truth (for example, calls B11 and B14 report `is_connected=true` despite containing zero text).

* Needs Review / Triage Count: Tracks records pending managerial classification review, pending overrides, or containing unresolved customer blockers.

## Key Findings and Deliberate Decisions

### 3 Most Important Analytical Findings

1. Critical Defect Escalation (M07 — Willowfield Trust, Turns 3–25):
   * Upstream calendar metadata listed this as a standard operational catchup, but the conversation established 4 severe software defects:
     * Line item description edit failing to update upon save (Turns 3–7)
     * File attachment crash on "Recreate payment" flow (Turns 8–15)
     * Employee reimbursement payouts stalled in "Processing" for over 48 hours (Turns 16–19)
     * Stale transaction value retained during "Mark as paid" sequence (Turns 21–25)
   * Product Impact: Categorized as "Customer Support / Engineering Bug", bypassing sales pipeline tracking.

2. Structural Product Gap: Prepaid vs. Corporate Credit (M03 and M04 — Asterloom and Cedarwave):
   * Asterloom (Turn 6: "I had thought about a corporate credit card") and Cedarwave (Turn 11: "Moving from prepaid to credit is the main issue for us") both stalled due to corporate credit line expectations. SpendNest's prepaid card model caused deal resistance.
   * Product Impact: Surfaced as a high-priority "Product Positioning Gap" in the manager daily brief.

3. Strategic Supplier vs Prospect Distinction (M02 — LedgerBridge, Turns 1–31):
   * Logged upstream as an outbound external interaction, but transcript analysis proves LedgerBridge is a compliant e-invoicing middleware vendor pitching services to SpendNest.
   * Product Impact: Routed to "Internal and Vendor Strategy" to prevent artificial pipeline inflation.

### 1 Deliberately Rejected Decision

* Rejected Date Range Filtering:
  * Alternative Considered: Implementing a standard calendar date picker on the executive dashboard.
  * Reason for Rejection: All 44 provided records fall strictly on a single local date (21 August 2026, Asia/Kolkata). Adding a date range filter introduces empty states and user friction without providing analytical utility.
  * Trade-off Accepted: Prioritized multi-factor person (Rep / Organizer) and source-type (Call vs Meeting) filters instead.

## Export Structure and Machine-Readable Manifest

Executing "Export Manifest & Records" downloads an audit-ready package containing:

* `manifest.json`: Machine-readable audit file recording:
  * `source_type`: `call` or `meeting`
  * `source_id`: UUID
  * `applied_revision`: `1` or `2`
  * `route`: Assigned category
  * `output_path`: Relative paths inside `/outputs/deals/`, `/outputs/support/`, `/outputs/coaching/`, `/outputs/vendor/`
  * `review_state`: Triage/review status
  * `manual_override`: Boolean (`true` or `false`)
  * `analysis_freshness`: `current` or `stale`

## Architecture, AI Implementation, and Effort

* Analysis Architecture: All 18 transcript syntheses and the August 21 Manager Daily Brief are structured and pre-generated to eliminate runtime LLM latency, paid API dependencies, and risk of non-deterministic output during reviewer testing.
* Deterministic Engine: Deduplication, revision precedence (higher revision overwrites), and manual override persistence are enforced strictly by client-side deterministic JavaScript logic adhering to `DATA_CONTRACT.md`.
* Approximate Time Spent: Approximately 7 hours across requirement analysis, data contract engine implementation, UI development, testing, slide deck compilation, and documentation.