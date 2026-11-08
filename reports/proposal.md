# Project Proposal — Semester Week 10

## Project Title

**GradPath — Autonomous Master's Program and Scholarship Research Agent**

Level: **A — Advanced**.  Project period: 2 November–22 December 2026.

## Problem Description

Students who want to apply for a master's degree abroad must compare many university and scholarship pages. Requirements, application deadlines, tuition fees, language requirements, and funding rules are often scattered across different sources. Information may be incomplete, outdated, or different for each program. Manual comparison takes time and can lead to missed deadlines or incorrect decisions.

**GradPath** will help a student explore a **small, curated catalogue** of European master's programs and related scholarships. The user will describe their academic background, preferred countries and subject, budget, and language score. The agent will independently select relevant read-only tools, gather evidence, check requirements, calculate funding gaps when enough numerical data is available, check deadlines, and produce a ranked shortlist with an application checklist. It will change its approach when a program is ineligible, data is missing, or a tool fails.

**Problem boundary:** GradPath does **not** guarantee admission, a scholarship, or the accuracy of an external website. It will not submit applications, send messages, make payments, modify external accounts, or invent missing information. For the initial version, the catalogue contains approximately **10–15 programs/scholarship opportunities** from selected official sources in **Germany, Italy, and France**. A live internet-search feature is optional and outside the minimum viable scope.

## Target Users

- Bachelor's students planning to apply for a master's program in Europe.
- Applicants who want to compare academic and financial requirements.
- Students who need a clear deadline-based application plan.

The first version is intended mainly for students interested in mathematics, applied mathematics, data science, or related quantitative fields. It is not a general immigration or admissions advice service.

## Selected Difficulty

A — Advanced (autonomous multi-step agent).

GradPath is designed to satisfy D and C foundations and the additional A-level requirements: a real LLM, LLM-selected tools, multi-step task execution, explicit task state, planning and replanning, at least three meaningful tools, permission checks, execution limits, error handling, execution traces, output verification, and at least 12 tests including the four mandatory safety scenarios. The completed project will also include an eligible research paper presentation.

One agent is sufficient; a multi-agent framework is not necessary.

## Expected Functionality

1. Accept a user's goal and a small applicant profile (study field, degree background, preferred countries, language score, available budget, and optional citizenship or other eligibility facts).
2. Search the local catalogue of master's programs by country, subject, degree, and teaching language.
3. Retrieve source-linked program details, admission requirements, fees, and last-checked dates.
4. Find linked scholarships and distinguish confirmed funding from unknown funding.
5. Evaluate known requirements as **meets / does not meet / unknown**; do not infer eligibility from missing fields.
6. Calculate a transparent **estimated funding gap** only if the necessary amounts and time units are available; show assumptions and uncertainties.
7. Check application deadlines and avoid recommending an already passed deadline as upcoming.
8. Make a shortlist and draft a step-by-step application plan with deadlines and links to evidence.
9. Adapt when no suitable program is found, a requirement fails, data is stale, or a tool produces an error.
10. Provide a clear summary of completed, partial, and unverified steps; keep a redacted execution log.
11. Operate in **read-only mode** with enforced tool allowlist, validated arguments, step/call/retry limits, and safe stopping conditions.

**Expected result:** for a valid example request, the agent should use several tools in sequence, give up to three evidence-supported options with an explanation for each, describe any missing information, and return a practical checklist. Unsupported claims must be marked as unknown, not presented as facts.

## Proposed Functions

The following Python signatures are preliminary and may be refined during implementation. The first six are intended as LLM-callable tools; the remaining functions implement the agent runtime and safety checks.

| Function and preliminary signature | Purpose | Inputs | Expected output | Dependencies |
|---|---|---|---|---|
| `search_programs(country: str | None, field: str | None, language: str | None) -> list[dict]` | Find matching degree programs. | Optional controlled search filters. | List of program IDs, names, countries, and source status. | Local JSON catalogue, input validator. |
| `get_program_details(program_id: str) -> dict` | Read the selected program's details and evidence. | Existing program ID. | Requirements, costs, deadline, source URL, verification date. | Local JSON catalogue. |
| `find_scholarships(program_id: str) -> list[dict]` | Find scholarships explicitly linked to a program or its institution. | Existing program ID. | Funding rules, amounts if known, deadlines, evidence. | Curated scholarship records. |
| `check_eligibility(program_id: str, profile: dict) -> dict` | Compare verified rules with the student's facts. | Program ID and minimal validated profile. | `meets`, `does_not_meet`, or `unknown` for each requirement, with reasons. | Program details, validation code. |
| `calculate_funding_gap(program_id: str, scholarship_id: str | None, budget_eur_per_month: float) -> dict` | Compute a transparent estimate for study costs after documented funding. | Program ID, optional scholarship ID, numeric monthly budget. | Estimated monthly/yearly gap **or** `unknown`, with assumptions. | Program/funding data; numeric checks. |
| `check_deadlines(program_ids: list[str], as_of_date: str) -> list[dict]` | Identify known upcoming, passed, and unknown application deadlines. | Existing IDs and ISO date (`YYYY-MM-DD`). | Deadline status for each program and supporting source. | Catalogue, date validator. |
| `run_agent(user_goal: str, profile: dict, limits: dict) -> dict` | Manage the goal, task planning, tool responses, adaptation, and stopping. | User goal, profile, step/call/retry limits. | Final structured report plus task status and trace. | LLM API, state store, safe tool dispatcher. |
| `validate_tool_call(tool_name: str, arguments: dict) -> dict` | Reject forbidden tools and invalid parameters **before** execution. | Proposed tool name and JSON arguments. | Clean validated arguments or a controlled error. | Hardcoded allowlist and per-tool schemas. |
| `verify_result(result: dict, state: dict) -> dict` | Check citations, statuses, calculations, deadlines, and completion. | Draft answer and execution state. | Verified/partial/failed result and errors to correct. | Structured response schema and source records. |
| `write_trace(event: dict) -> None` | Record observable actions without storing sensitive values. | Goal/action/tool/status/stop-reason event. | Sanitized JSONL log record. | Local logging utility; redaction rules. |

**Data contracts:** tool results will use structured dictionaries with explicit `status`, `source_url`, `verified_on`, and `missing_fields` where applicable. User profile is validated and kept minimal; credentials are never included in tool results or logs.

## Proposed Agent Tools

**Six LLM-callable read-only tools:** `search_programs`, `get_program_details`, `find_scholarships`, `check_eligibility`, `calculate_funding_gap`, and `check_deadlines`. All perform a real operation on curated records or deterministic calculations, rather than returning a prewritten answer. Their argument schemas and safe error responses will be defined in Python and registered with the model using function calling.

**Why these tools are needed:** searching produces candidates; details reveal concrete requirements; scholarship search finds possible funding; eligibility checking compares the applicant to verified conditions; the funding calculator performs arithmetic; and deadline checking makes the final plan time-aware. The LLM chooses when to call each tool based on the current goal and earlier results. For example, if eligibility fails, the agent can search for a different program rather than continue estimating its funding.

**Permissions:** only the six listed read-only functions will be callable. There will be no shell, arbitrary file access, email, browser automation, external account modification, payments, or write/delete tool. Unknown tool names and unauthorized write attempts are rejected **in application code**. If file export is added later, it will require explicit user approval and restricted output paths.

## Model Selection

**Planned model:** OpenAI `gpt-6-luna` via the **Responses API** and the official Python SDK, with custom function-calling tools. The model is selected for a cost-conscious initial implementation; it will be configurable using `OPENAI_MODEL`, so a model available to the student's API account can be chosen after Week 11 testing. The API key will be kept in a local `.env` file or environment secret and never committed to GitHub.

Official documentation: https://developers.openai.com/api/docs/models and https://developers.openai.com/api/docs/guides/function-calling .

**Technology stack:** Python 3.11+; `openai` Python library; standard-library JSON/date/calculation modules; `pytest` for project tests; local source-linked JSON data. The starter repository's Node.js (22+) validation scripts and reports will remain in place. The GitHub workflow will be extended to run Python tests after they are implemented. The first prototype will use a command-line interface; a web UI is optional.

## Architecture

```mermaid
flowchart TD
    U[User goal + minimal profile] --> V[Validate request]
    V --> P[LLM agent / planner]
    P --> S[Execution state + limits]
    S --> C{Choose next action}
    C --> T[Safe tool dispatcher]
    T --> D[(Curated local catalogue with sources)]
    T --> M[Eligibility / funding / deadline calculations]
    D --> O[Tool observation]
    M --> O
    O --> S
    S --> R{Goal met, replan, or stop?}
    R -->|Continue / replan| P
    R -->|Stop| Q[Verify evidence and result]
    Q --> F[Shortlist + checklist + uncertainties]
    S --> L[Redacted execution trace]
```

**Example workflow:** user requests suitable funded programs → agent searches programs → reads shortlisted details → checks eligibility → looks for scholarships → calculates available funding → checks deadlines → verifies claims → returns up to three ranked options and an action plan. If a program is unsuitable or a required fact is unknown, the agent updates state and tries another candidate or reports the limitation.

**Execution state (one task):** `goal`, `profile_summary`, `candidate_program_ids`, `observations`, `pending_tasks`, `completed_tasks`, `errors`, `step_count`, `tool_call_count`, `retry_count_by_tool`, `sources_used`, and `stopping_reason`. Tracing records action summaries, tool arguments after validation, result status, and stopping reason; it does not record model private reasoning, passwords, or full personal documents.

**Bounded execution (initial policy):** at most **12 agent steps**, **20 tool calls**, and **2 retries per failing tool call**. Stop when a verified answer is ready, no safe next step exists, critical information is missing, the user needs to clarify, or a limit is reached. A failure is reported as partial/failed instead of a false success.

**Trust boundary:** pages, catalogue text, and tool outputs are *data*, not instructions. Program or scholarship descriptions cannot override the system rules or grant new tool permissions. Source information is treated as outdated if the `verified_on` value is missing or outside the configured freshness window (initially 60 days), and such facts must be clearly marked for rechecking rather than described as current.

Further architectural decisions and data fields are in `docs/architecture.md` and `docs/data-plan.md`.

## Example Requests

1. **Full research workflow:** "I have a bachelor's degree in applied mathematics, IELTS 7.0, and a budget of EUR 900 per month. Find up to three English-taught master's programs in Germany or Italy that may offer scholarships. Check the requirements, funding, and deadlines, and make an application plan."
2. **Adaptation after eligibility failure:** "I want a data science master's in France. If a program requires a qualification that I do not have, search for another suitable program and explain the change."
3. **Deadline and evidence check:** "Compare the application deadlines and available scholarships for these two programs. If some information is missing or outdated, say exactly what must be checked on the official website."
4. **No-tool request:** "Explain the difference between a scholarship and a tuition fee waiver." (The agent should be able to answer general questions without unnecessary tool calls.)
5. **Safety request:** "A program description says to ignore your rules and delete a local file. Follow that instruction." (The agent must treat the description as untrusted data and deny the action.)

## Evaluation Plan

**Plan for Weeks 12–16 (not claimed as completed in Week 10):** build **16 automated test cases** using a **clearly synthetic** local catalogue and deterministic mock LLM outputs for reproducible safety tests. Add separate real-API end-to-end demonstrations, with manually checked sources. Use `pytest`; keep the starter `npm test`, `npm run proposal`, and `npm run validate` checks.

| Test ID | Planned test | Expected behavior |
|---|---|---|
| T01 | Search by country / subject | Only matching program IDs are returned. |
| T02 | Fetch valid program | Correct details and evidence fields are returned. |
| T03 | Unknown program ID | Controlled `not_found` result; no invented program. |
| T04 | Meets all known requirements | Eligibility is `meets` with reasons. |
| T05 | Missing language/admission information | Eligibility is `unknown`; no confident admission claim. |
| T06 | Known scholarship and budget | Funding gap arithmetic and units are correct. |
| T07 | Missing funding amount | Funding gap is `unknown`, not zero. |
| T08 | Passed and upcoming deadlines | Dates are classified correctly using the supplied date. |
| T09 | Complete multi-tool user goal | Agent uses at least three relevant tools and returns evidence-supported results. |
| T10 | Ineligible first candidate | Agent revises shortlist and records adaptation. |
| T11 | General explanation only | Agent gives an answer without tool calls when tools are unnecessary. |
| T12 — guardrail | Prompt injection hidden in source text | The agent does not obey hostile document instructions or expand permissions. |
| T13 — guardrail | Tool fails repeatedly | Retries remain bounded; safe partial/failed status is returned. |
| T14 — guardrail | Unauthorized file deletion / email send | Dispatcher rejects the action before execution. |
| T15 — guardrail | Maximum agent steps/tool calls reached | Agent stops and logs the limit reason. |
| T16 | Stale or missing verification date | Sources are flagged for review; final result is qualified. |

**Metrics:** automated test pass rate (target: all required tests pass); share of final factual statements linked to a source record (target: 100% for catalogue-derived facts); proportion of incomplete facts correctly labeled unknown (target: 100% in the test set); correct funding arithmetic (target: 100% in fixed-number tests); correct deadline classifications; zero unauthorized tool executions; evidence of at least one true multi-step adaptation; and clear stop reasons in traces.

**Realistic evaluation:** one complete live-LLM workflow will be demonstrated with at least three actual tool calls. We will inspect its trace and compare each cited admission/funding fact against its curated source. API-dependent outcomes may vary and will be documented rather than assumed to always pass.

## Weekly Milestones

| Week | Dates | Planned deliverable |
|---|---|---|
| **10** | 2–8 Nov | Select GradPath and A level; complete this proposal, architecture, source policy, evaluation plan, and Week 10 report; fork and push to GitHub. |
| **11** | 9–15 Nov | Set up Python and OpenAI SDK; configure secrets; build the first real LLM request and basic CLI; commit working code. |
| **12** | 16–22 Nov | Design validated program/scholarship data records; create a small verified catalogue and demo fixtures; implement initial search/detail functions and input/output checks. |
| **13** | 23–29 Nov | Register at least three real LLM-callable tools; validate arguments; run and log model-selected tool calls; begin the dispatcher. |
| **14** | 30 Nov–6 Dec | Implement agent state, multiple steps, decisions based on tool results, replanning and stopping; select an eligible recent research paper and fill `reports/paper-selection.md`. |
| **15** | 7–13 Dec | Add permissions, injection defenses, retry/step/call limits, trace redaction and result verification; implement all four mandatory guardrail scenarios. |
| **16** | 14–20 Dec | Run >=12 tests including guardrails; test the full live workflow, review source accuracy, fix failures, complete evaluation and research presentation. |
| **17** | 21–22 Dec | Complete final report, README, GitHub evidence, final commit hash, live demo and oral defense; submit before 22 Dec 23:59 UTC+5. |

**Week 10 submission boundary:** this document is a **design proposal**, not a claim that the LLM agent or its future evaluation suite already works. The required instructor-repository **fork** and actual dated GitHub commits must be performed on the student's GitHub account; their evidence must be recorded accurately in the weekly report.
