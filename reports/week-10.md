# Semester Week 10 Progress Report

## Completed Work

- Selected **GradPath — Autonomous Master's Program and Scholarship Research Agent** as the project topic and **A (Advanced)** as the target difficulty level.
- Read the starter repository's shared instructions and A-level requirements, including the fixed Week 10–17 calendar, required tools, state, guardrails, evaluation, and research-paper requirement.
- Prepared `reports/proposal.md` with the problem, target users, scope, expected features, proposed function signatures, six model-callable tools, model choice, agent architecture diagram, example requests, evaluation plan, and weekly milestones.
- Defined a **read-only** first version using a small curated catalogue instead of uncontrolled web browsing; included evidence links, source freshness, explicit unknown values, and limits on autonomous execution.
- Drafted optional supporting architecture, data, testing, and synthetic-fixture materials locally. These additional files were **not** added to the GitHub repository in Week 10; the required design, architecture, and evaluation plan are included in `reports/proposal.md`.
- Created a fork of the instructor repository and uploaded the completed `reports/proposal.md` and `reports/week-10.md` there. Checked the proposal and weekly report using the starter validation commands.
- Used ChatGPT to assist with interpreting the assignment, drafting the project plan, and checking the documents. I will review the materials and disclose this help in the final report.

## Important Commits

**Week 10 repository snapshot commit:** `2efee24151eafec35ffc092a1f963130cd8a9ba7` (SHA recorded in the downloaded GitHub ZIP metadata).  
**Link:** https://github.com/aiziya-sagyntay/css4007-ai-agents-final/commit/2efee24151eafec35ffc092a1f963130cd8a9ba7  
**Description:** The Week 10 repository snapshot includes the completed GradPath proposal and weekly report. Verify the hash and commit details using GitHub **History** before final submission.

## Current Functionality

**Planning phase only.** The repository contains a detailed project proposal (including architecture, data policy, planned tools, and testing scenarios) and the Week 10 progress report. The optional synthetic example records and supporting design files are prepared locally but are not in this repository snapshot. The real LLM connection, live program catalogue, tool-calling agent loop, and automated GradPath tests are **not yet implemented** and are scheduled for Weeks 11–16. The provided starter guardrail tests can be run independently and do not count as tests of the GradPath agent.

## Problems Encountered

- University and scholarship information changes and is often incomplete. A recommendation without source evidence could be misleading.
- Unrestricted web access and external actions would make the project harder to test and protect within the available time.
- The original starter ZIP did not contain the student's GitHub history; the fork was created and its reports were updated separately.

## Solutions

- Chose a limited catalogue with official source URLs, verification dates, transparent eligibility statuses, and an `unknown` state for unsupported facts.
- Planned separate deterministic functions for numeric calculations and deadline checks; the LLM will choose tools, while Python will validate arguments and enforce permissions.
- Limited initial tools to read-only operations, with execution-step, tool-call, and retry bounds; planned synthetic test data so safety cases can be reproduced.
- Created the GitHub fork and added the required reports. Captured the repository-snapshot SHA from the downloaded ZIP; GitHub History remains the authoritative way to verify it.

## What I Learned

I learned the difference between an LLM application, a tool-using agent, and an autonomous A-level agent. A-level work requires several connected steps, explicit state, adaptation when intermediate results change the plan, and safe stopping. I also learned why retrieved pages are untrusted data, why tool permissions must be checked by code rather than a prompt, and why missing admission/funding information should be reported as unknown.

## Next Week

- Confirm the Week 10 commits in GitHub History and ensure the Week 10 report reflects the actual submitted files.
- Set up Python 3.11+, dependencies and API-key handling (`.env`, GitHub Secrets where needed).
- Build a minimal command-line application and make the first **real** OpenAI Responses API request.
- Add clear user input handling and a basic documented run command; commit working Week 11 code.
