# ELP Team 2 — Multi-Agent Research Team

A Streamlit app that answers a business research question with a team of agents:

    Question → Supervisor → Specialists (parallel) → Shared Evidence (SQLite) → Synthesis → Reviewer → Report

## Run it

From this folder:

    python -m pip install -r requirements_team2.txt
    python -m streamlit run multiagent.py

The app needs a `.env` file in this folder containing `TAVILY_API_KEY` and `DEEPSEEK_API_KEY`.

## What is in this folder

| File | What it is |
| --- | --- |
| `multiagent.py` | **The app.** One file holding Weeks 3 through 8, in that order. |
| `requirements_team2.txt` | Python packages the app needs. |
| `.env` | Your API keys. Never commit this. |
| `team2_evidence.db` | SQLite database the app creates: `evidence` table (Week 4) and `reports` table (Week 5). Safe to delete; it is rebuilt on the next run. |

## Where each week lives in `multiagent.py`

| Week | Section | Status |
| --- | --- | --- |
| 3 | Supervisor, specialist agents, parallel execution, first button | Done |
| 4 | Evidence extraction, validation, SQLite store, second button | Done |
| 5 | Report synthesis, `reports` table, third button | Done |
| 6 | Reviewer/critic agent, one controlled revision, fourth button | Done |
| 7 | Gap/conflict check, one bounded extra research pass, fifth button | Done |
| 8 | Final research workspace: quality gate, timing, approved download | Code done; manual checks pending |

Each week starts with a large docstring banner (`ELP Week 4 — ...`, `ELP WEEK 5 — ...` through `ELP WEEK 8 — ...`), so searching the file for `ELP W` jumps between them.

### Week 6 progress

| TODO | What | Status |
| --- | --- | --- |
| 0 | `review_report` — scores the draft on six criteria, treats malformed reviews as a failure, enforces the "all scores ≥ 7" PASS rule in code | Done |
| 1 | `revise_report_once` — one revision from existing evidence only; a section the model drops keeps its draft text | Done |
| 2 | `Week6ResearchState` — adds `review`, `initial_review`, `final_report_markdown`, `revision_performed` | Done |
| 3 | `reviewer_node` — review → revise at most once → review again | Done |
| 4 | `build_week6_graph` — Week 5 graph plus the reviewer node | Done |
| 5 | Week 6 UI — "Run Reviewed Synthesis Pipeline" button | Done |

Use the Week 6 button for the full flow. Only a PASS report is shown as final; a failed report is labelled unapproved and kept in an expander. The Week 5 draft is still saved to SQLite before review, so a saved report is not automatically approved.

### Week 7 progress

| TODO | What | Status |
| --- | --- | --- |
| 0 | `week7_t2_find_gaps` — asks the supervisor for at most one material gap or conflict; bad model output never triggers research | Done |
| 1 | `week7_t2_route` — goes to `additional` only for a valid gap that has not been researched yet, otherwise `synthesis` | Done |
| 2 | `week7_t2_additional_research` — reruns the existing supervisor, specialists and evidence store once on the targeted question, then merges the new evidence with the old evidence | Done |
| 3 | `Week7ResearchState` — adds `gap_check`, `additional_research_performed`, `new_evidence_count` | Done |
| 4 | `build_week7_graph` — evidence store → gap check → (one extra pass) → synthesis → reviewer | Done |
| 5 | `week7_graph` compiled and assigned | Done |
| 6 | Week 7 UI — "Run Week 7 Research Loop" button, gap notes, PASS-only final report | Done |

The extra pass always goes straight to synthesis and never back to the gap check, so research runs at most twice.

### Week 8 progress

Week 8 adds no new agents. It reuses `week7_graph` in a final workspace with a "Run final Team 2 research" button.

| TODO | What | Status |
| --- | --- | --- |
| 1 | `_w8_t2_ready` — quality gate: PASS review, non-empty report, at least 1 evidence record, at least 3 specialist outputs. Fails closed. | Done |
| 2 | Check that the three specialist outputs are distinct and that the gap pass runs at most once | Manual check — not yet recorded |
| 3 | Open 3 cited source links by hand and note any problems | Manual check — not yet recorded |

Remaining checklist from the Week 8 banner: confirm all 10 report headings are present, run three repeated trials with the same prompt (note errors and timing), and remove obsolete prior-week UI once the Week 8 workspace is confirmed working.

The download button appears only when `_w8_t2_ready` passes. A failed report is shown in an "Unapproved draft" expander only.
