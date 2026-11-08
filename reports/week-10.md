# Semester Week 10 Progress Report

## Completed Work

- Selected **GradPath — Autonomous Master's Program and Scholarship Research Agent** as the project topic and **A (Advanced)** as the target difficulty level.
- Read the starter repository's shared instructions and A-level requirements, including the fixed Week 10–17 calendar, required tools, state, guardrails, evaluation, and research-paper requirement.
- Prepared `reports/proposal.md` with the problem, target users, scope, expected features, proposed function signatures, six model-callable tools, model choice, agent architecture diagram, example requests, evaluation plan, and weekly milestones.
- Defined a **read-only** first version using a small curated catalogue instead of uncontrolled web browsing; included evidence links, source freshness, explicit unknown values, and limits on autonomous execution.
- Created supporting design materials: `docs/architecture.md`, `docs/data-plan.md`, and `docs/test-plan.md`; created a clearly fictional test-data example `data/demo_catalog.json` for later implementation and testing.
- Prepared the project locally from the official starter ZIP and checked the proposal and weekly-report structure with the starter validation commands. The files are ready to transfer to a GitHub fork.
- Used ChatGPT to assist with interpreting the assignment, drafting the project plan, and checking the documents. I will review the materials and disclose this help in the final report.

## Important Commits

**GitHub commit evidence is not yet verified in this local preparation workspace.** A ZIP copy of the upstream starter does not have the student's fork history. The real fork, commit, and push must be completed in the student's GitHub account before this section can list an authentic commit hash and URL. Do not invent a hash or claim a push that has not occurred.

Suggested first substantive commit message after transferring these files to the fork: `docs: add GradPath A-level Week 10 proposal and architecture`.

Once pushed, replace this paragraph with the **actual** commit hash, its GitHub link, and a short description of the changed files.

## Current Functionality

**Planning phase only.** The repository now contains a detailed specification, architecture, data policy, synthetic example record, and planned test scenarios. The real LLM connection, live program catalogue, tool-calling agent loop, and automated GradPath tests are **not yet implemented** and are scheduled for Weeks 11–16. The provided starter guardrail tests can be run independently and do not count as tests of the GradPath agent.

## Problems Encountered

- University and scholarship information changes and is often incomplete. A recommendation without source evidence could be misleading.
- Unrestricted web access and external actions would make the project harder to test and protect within the available time.
- The original repository was supplied as a ZIP, which does not include the student's GitHub fork or commit metadata.

## Solutions

- Chose a limited catalogue with official source URLs, verification dates, transparent eligibility statuses, and an `unknown` state for unsupported facts.
- Planned separate deterministic functions for numeric calculations and deadline checks; the LLM will choose tools, while Python will validate arguments and enforce permissions.
- Limited initial tools to read-only operations, with execution-step, tool-call, and retry bounds; planned synthetic test data so safety cases can be reproduced.
- Prepared the files for transfer to the required GitHub fork and documented the outstanding real GitHub step instead of recording fictitious activity.

## What I Learned

I learned the difference between an LLM application, a tool-using agent, and an autonomous A-level agent. A-level work requires several connected steps, explicit state, adaptation when intermediate results change the plan, and safe stopping. I also learned why retrieved pages are untrusted data, why tool permissions must be checked by code rather than a prompt, and why missing admission/funding information should be reported as unknown.

## Next Week

- Create/verify the fork in GitHub, commit and push the Week 10 files if not already done, and record the **real** commit link in this report.
- Set up Python 3.11+, dependencies and API-key handling (`.env`, GitHub Secrets where needed).
- Build a minimal command-line application and make the first **real** OpenAI Responses API request.
- Add clear user input handling and a basic documented run command; commit working Week 11 code.
