# Proposal: Inventory Dashboard Modernization

**In response to:** RFP MC-2026-0417 (Meridian Components, Inc.)
**Submitted to:** J. Okafor, Director of Procurement
**Submitted by:** Basecamp Consulting (sample)
**Proposal deadline:** May 8, 2026, 17:00 CT

---

## 1. Executive Summary

Meridian's operations team opens the dashboard every day, but the numbers on the overview page do not change when they pick a period or a warehouse. They have been rebuilding the figures in spreadsheets. IT is cautious about approving changes because there are no automated tests. These two problems feed each other: users do not trust the numbers, and IT cannot safely approve fixes.

Our approach is built around **reliable delivery**. We will:

1. **Put a thin safety net in place first.** A small set of smoke tests on the overview page, so that the R1 fix is verified and IT can see the change is safe.
2. **Fix R1 next.** Every overview KPI will respond to the period, warehouse, category and status filters. This is the biggest complaint from R. Tanaka's team, and it is the quickest way to win back trust in the numbers.
3. **Build the full safety net.** Browser-test coverage of the existing flows (R4) and the architecture document (R5), so IT can approve future changes with confidence.
4. **Then deliver the rest.** Reports fixes (R2) and the Restocking view (R3), plus the desired i18n extension (D2) for the Tokyo team. The UI refresh (D1) and dark mode (D3) are offered as separately priced options.

**Business value for Operations.** Once the overview KPIs follow the filters, R. Tanaka's team can stop rebuilding numbers in spreadsheets and use the dashboard as the single source for the daily review. Our target is that the weekly spreadsheet reconciliation is no longer needed after R1 goes live in week 4. Restocking (R3) then turns the same trusted data into ordering decisions.

We propose a **capped time-and-materials** price of about **$210,000** for the core scope. R2 asks us to fix "all" defects with only "at least 8" recorded, so the true size is unknown. A cap gives Meridian a firm ceiling, and the phased plan gives a firm schedule and a payment point for every phase.

## 2. Technical Approach

### R1: Overview page (dashboard) fixes
- **Approach:** Add smoke tests for the overview page first. Then trace how the filters (period, warehouse, category, order status) reach each KPI, from the Vue view through `client/src/api.js` to the FastAPI summary endpoint, and make every KPI respond to the selected filters.
- **Risks:** Some KPIs may be computed on the client from unfiltered data, and some may be computed on the server without the filter. We will confirm which during the first days of the engagement.
- **Done when:** Every KPI on the overview page shows values that match the filtered underlying data for the selected period, warehouse, category and status. The smoke tests prove this.

### R2: Reports module
- **Approach:** Start with a defect inventory. Reproduce each of the eight or more recorded problems, search for further ones (filter behavior, i18n gaps, data-pattern mismatches), then fix them in priority order.
- **Risks:** The real defect count is unknown and the previous vendor's notes are thin.
- **Done when:** Every defect in the agreed inventory is fixed and covered by a test. Defects found later are added to the inventory and handled under the cap (see A1).

### R3: Restocking recommendations
- **Approach:** Agree a short specification with R. Tanaka's team during phase 0: inputs (stock levels, demand forecast, operator budget cap), the recommendation rule, and how the budget cap behaves. Then build the Restocking view in the existing Vue/FastAPI stack.
- **Risks:** The quality and granularity of the demand forecast data are unknown.
- **Done when:** An operator can set a budget cap and get a recommended order list that stays within it, matching the agreed specification.

### R4: Browser test automation
- **Approach:** Delivered in three steps. Stage 1 is minimal smoke tests on the overview page, delivered before the R1 fix. Stage 2 is full end-to-end coverage of the existing flows, agreed with IT (A3). Stage 3 adds tests for Reports (R2) and Restocking (R3) as they are fixed and built in phase 3.
- **Risks:** The set of "major user flows" is not defined in the RFP.
- **Done when:** The agreed flows run automatically and pass, and IT can run them before approving a change.

### R5: Architecture document
- **Approach:** A concise overview of the current system: components, data flow, filter system, known problems, and how to run and test it. We will verify it against the code rather than relying on the previous vendor's handoff memo, which is incomplete.
- **Done when:** An IT engineer who has not seen the code can start the app, find the main modules, and understand where to make a change.

### D2: Internationalization (desired)
- **Approach:** Extend i18n to the remaining modules, starting with the views the Tokyo team uses most. This is handled together with the i18n defects in R2. A Japanese-speaking engineer is on the team for this work.
- **Done when:** The agreed modules switch between English and Japanese.

### D1 (UI modernization) and D3 (dark mode): priced options
Both are offered as separately priced options, outside the capped price, so the core scope stays predictable. Meridian can accept either or both at contract signing or at the end of phase 2.
- **D3 (dark mode), about $12,000:** An operator-selectable theme for the low-light warehouse terminals. Applied through the existing design tokens, so it is a contained piece of work.
- **D1 (UI modernization), about $25,000:** A visual refresh. We will confirm which "current standards" Meridian has in mind (A4) before starting it.
- **Done when:** The agreed theme or visual refresh is applied to all pages in scope, and the browser tests still pass.

### Current and target architecture
We keep the current stack (Vue 3 with Vite, FastAPI, JSON data files). The change is in how the pieces are connected and protected, not in replacing them.

Figure 1: Current vs. target architecture

| Layer | Current | Target |
|---|---|---|
| Overview KPIs | Do not follow the filters | Every KPI follows period, warehouse, category and status |
| Reports | Several known defects, some filters not connected | Defects in the agreed inventory fixed and tested |
| New features | None for ordering | Restocking view and recommendations (R3) |
| Tests | None | Smoke tests, then browser tests of the main flows (R4) |
| Documentation | Thin handoff memo | Architecture document verified against the code (R5) |
| Languages | English-only screens in most modules | English and Japanese in the agreed modules (D2) |

### Requirement coverage

| # | Requirement | Phase | Approach in short |
|---|---|---|---|
| R1 | Overview page fixes | 1 | Smoke tests first, then wire every KPI to the filters |
| R2 | Reports module | 3 | Defect inventory, then prioritized fixes |
| R3 | Restocking recommendations | 3 | Short specification with Operations, then build |
| R4 | Browser test automation | 1 (smoke), 2 (existing flows), 3 (Reports, Restocking) | Three stages, flows agreed with IT |
| R5 | Architecture document | 2 | Verified against the real code |
| D2 | i18n extension | 3 | Together with the R2 i18n fixes |
| D1 | UI modernization | Option (4) | Separately priced option |
| D3 | Dark mode | Option (4) | Separately priced option |

## 3. Related Experience

The following are sample cases written for this exercise. They are fictional.

| Case (fictional) | What we did | Measured result |
|---|---|---|
| **Northwind Parts Distribution (sample)**: sales dashboard whose figures did not match the ledger | Traced filter handling across the UI, API and data layer, and fixed every KPI that bypassed the filters | KPI gap against the ledger fell from 12% to under 0.5%. Weekly spreadsheet reconciliation dropped from 6 hours to under 1 hour. |
| **Harbor Robotics (sample)**: logistics portal with no automated tests | Built smoke tests first, then browser tests for 24 main flows, run on every change | Release approval time fell from 5 days to 1 day. Defects found after release fell by 55% in two quarters. |
| **Pacific Valve Co. (sample)**: ordering by spreadsheet across three warehouses | Added a restocking view that recommends orders within a budget cap, and added English and Japanese screens | Weekly ordering time fell from 6 hours to 1.5 hours. Stock-outs on top-selling items fell by 27% in six months. |

## 4. Team and Roles

The core team averages 2.5 full-time-equivalent people (FTE) in every phase. The mix changes with the work.

| Role | Responsibility | Phase 0 | Phase 1 | Phase 2 | Phase 3 |
|---|---|---|---|---|---|
| Lead / architect | Architecture review, R3 spec, phase demos, status reports, change control | 0.8 | 0.4 | 0.6 | 0.4 |
| Engineers (including one Japanese-speaking engineer) | R1, R2, R3 and D2 implementation | 1.2 | 1.3 | 1.0 | 1.6 |
| QA engineer | Smoke tests, browser tests, defect inventory, acceptance support | 0.5 | 0.8 | 0.9 | 0.5 |
| **Total FTE** | | **2.5** | **2.5** | **2.5** | **2.5** |

For options D1 and D3 (phase 4), one engineer and half a QA engineer are assigned, within the option prices.

## 5. Schedule

| Phase | Weeks | Contents | Milestone |
|---|---|---|---|
| 0. Onboarding | 1–2 | Environment setup, code and handoff review, agree test flows, defect inventory and R3 specification | Kickoff report, agreed inventory and assumptions |
| 1. Trust the numbers | 3–4 | Smoke tests on the overview page (R4 stage 1), R1 fix | Overview KPIs match the filtered data |
| 2. Make change safe | 5–7 | Browser tests for the existing flows (R4 stage 2), architecture document (R5) | IT can approve changes using the tests |
| 3. Complete the scope | 8–13 | R2 fixes, R3 Restocking view, D2 i18n, tests for Reports and Restocking (R4 stage 3), user acceptance | Final acceptance |
| 4. Options (if accepted) | 14–16 | D3 dark mode, D1 UI refresh | Option acceptance |

Each phase ends with a short demonstration and a written status report. We will agree the start date at contract signing (A2).

## 6. Price and Assumptions

### Price
**Capped time-and-materials.** Work is billed at a blended rate of about $150 per hour and cannot exceed the cap. The figures below are illustrative.

| Item | Basis | Amount |
|---|---|---|
| Phase 0: Onboarding | 200 h (2 weeks × 2.5 FTE) | $30,000 |
| Phase 1: Trust the numbers | 200 h | $30,000 |
| Phase 2: Make change safe | 300 h | $45,000 |
| Phase 3: Complete the scope | 600 h | $90,000 |
| **Core scope, phases 0–3** | 1,300 h | **$195,000** |
| R2 contingency (inside the cap, billed only if used) | About 100 h | $15,000 |
| **Cap for phases 0–3** | | **about $210,000** |
| Option D3: dark mode | About 80 h | about $12,000 |
| Option D1: UI modernization | About 165 h | about $25,000 |

**Payment by milestone.** Each phase is invoiced when its milestone is accepted:

| Milestone | Invoice |
|---|---|
| Phase 0: kickoff report and agreed defect inventory accepted | $30,000 |
| Phase 1: R1 live, KPIs match the filtered data | $30,000 |
| Phase 2: IT sign-off on tests and architecture document | $45,000 |
| Phase 3: final acceptance | $90,000, plus any R2 contingency actually used |
| Options (if accepted): option acceptance | Option price |

Payment terms are net 30 days from invoice.

**Change control:** If the R2 contingency is used up, we stop and report before doing more. Any work beyond the cap is done only after Meridian approves it in writing.

### Assumptions
The RFP leaves several points open. We have used the following assumptions to prepare this proposal. We keep the current stack (Vue 3, FastAPI, JSON data files).

| # | Gap in the RFP | Our assumption |
|---|---|---|
| A1 | R2 defect count is "at least 8" | The defect inventory is fixed at the end of phase 0 and agreed in writing. The cap covers that inventory plus the stated contingency; defects found later are handled under the change control above. |
| A2 | No budget range and no target delivery date | We plan for about 13 weeks for the core scope (phases 0–3) from contract signing, and 3 more weeks if the options are accepted. |
| A3 | "Main user flows" for R4 are not defined | Full coverage means the existing flows agreed with IT in phase 0, roughly 4–6 flows (for example filtering, orders, inventory, spending). Reports and Restocking tests are added in phase 3. |
| A4 | D1 refers to "current standards" with no definition | The D1 price assumes a refresh of the existing pages using a common modern design system, not a full redesign. We will confirm the standard before starting D1. D3 uses the existing design tokens. |
| A5 | No confidentiality terms | The source code and Meridian data are covered by a mutual NDA, which we are ready to sign. |
| A6 | No warranty or maintenance terms | We offer a 30-day warranty on delivered fixes, starting at the acceptance of each phase. |
| A7 | Hosting and environment access are not stated | Meridian provides a staging environment and the access needed to run the application and the tests. |

Meridian is asked to confirm all of A1–A7 at contract signing.

### Why this order
The RFP lists the tests (R4) and the architecture document (R5) after the fixes, but its own text says IT is cautious about approving changes without tests. We therefore deliver a minimal set of tests before the R1 fix, and the full set before R2 and R3, so that every change after the first can be verified.
