---
name: code-assignment-reviewer
description: >
  Review a candidate's home assignment GitHub repository before deciding whether to invite them for an interview.
  Use this skill whenever a user provides a GitHub link to a candidate's code submission, mentions reviewing a home assignment, 
  or asks to evaluate candidate code for hiring purposes. Runs 6 parallel specialized agents — each analyzing a different quality 
  dimension — then synthesizes a final structured report with red flags, justification prompts, level-adjusted verdict, calculates a weighted score (0-100),
  and outputs data specifically formatted for Monday.com integration.
---

# Code Assignment Reviewer

This skill performs a "Senior Reviewer" audit of a candidate's codebase. It synthesizes parallel technical deep-dives into a final recruitment verdict: Success (80+), Manual Review (60-79), or Failure (0-59).

---

## Input Requirements
• GitHub Repo URL
• Position Level (Junior/Mid)
• Task Name (e.g., "Crypto Dashboard Task")

---

## Step 1 — Clone & Explore the Repo

⁠ bash
git clone <repo_url> /tmp/candidate-repo
 ⁠

Then do a top-level survey:
•⁠  ⁠List files and folders (2 levels deep)
•⁠  ⁠Read the README
•⁠  ⁠Identify the tech stack (frontend framework, backend language/framework, DB, deployment)
•⁠  ⁠Note the AI tool usage summary if present (the task requires it)

Pass this survey context to all agents so they don't duplicate discovery work.


## step 2 - The Analysis Pipeline (Parallel Agents)

Each agent uses *claude-sonnet-4-6* and receives:
 1.⁠ ⁠The full repo contents (or relevant subset)
 2.⁠ the mandatory requirement specification (from /mnt/data/assets/tasks/<task_name>.md)
 3.⁠ ⁠The *position level* (⁠ junior ⁠ or ⁠ mid ⁠)
 4.⁠ ⁠Its specific evaluation criteria (below)

Each agent returns a structured response — see *Agent Output Format* below.

### Agent Definitions

#### Agent 1 — Code Quality
Evaluate: readability, naming conventions, function length & complexity, DRY violations, commented-out code, magic numbers, consistent formatting.

*Junior bar*: Is the code understandable? Are names meaningful? No obvious spaghetti?
*Mid bar*: Also check: are abstractions well-chosen? Is complexity managed? Is there evidence of clean code habits beyond basics?

---

#### Agent 2 — Architecture & Structure
Evaluate: folder organization, separation of concerns (frontend vs backend), layering (routes → controllers → services → DB), coupling, reusability.

*Junior bar*: Is there a recognizable structure? Does the candidate separate frontend and backend?
*Mid bar*: Also check: is there a service layer? Are concerns cleanly separated? Is the architecture scalable or at least not actively harmful?

---

#### Agent 3 — Security
Evaluate: JWT handling (storage, expiry, secret exposure), password hashing, secrets in code or ⁠ .env ⁠ committed to repo, input validation/sanitization, SQL injection risk, CORS config, exposed API keys.

*Junior bar*: No plaintext passwords, no hardcoded secrets in committed files, basic JWT usage.
*Mid bar*: Also check: proper token storage (not localStorage for sensitive tokens), validation middleware, no sensitive data in logs.

---

#### Agent 4 — Error Handling
Evaluate: try/catch usage on async operations, HTTP status codes (correct 4xx/5xx usage), user-facing error messages (not stack traces exposed), unhandled promise rejections, frontend error states shown to user.

*Junior bar*: Are errors caught at all? Does the app not crash on bad input?
*Mid bar*: Also check: are errors meaningful? Are status codes semantically correct? Is there a consistent error response shape?

---

#### Agent 5 — API Design
Evaluate: REST conventions (resource naming, HTTP verb usage), request/response structure consistency, pagination or filtering where appropriate, clear separation of concerns in routes.

*Junior bar*: Does it work? Are the routes logical and not chaotic?
*Mid bar*: Also check: RESTful naming, proper HTTP methods, consistent response envelope, no business logic leaking into routes.

---

#### Agent 6 — DB Schema, Data Layer & Deployment
Evaluate:
•⁠  ⁠*Schema*: table/collection design, appropriate data types, foreign keys/relations, no data duplication
•⁠  ⁠*Queries*: ORM vs raw SQL appropriateness, N+1 risks, use of indexes where obvious
•⁠  ⁠*Deployment*: Is the app actually deployed and accessible? Are env vars handled correctly? Is there a ⁠ .env.example ⁠?

*Junior bar*: Schema makes sense, app runs somewhere, env vars not hardcoded.
*Mid bar*: Also check: schema is normalized appropriately, queries are efficient, deployment is clean with proper config separation.

---

## 3. Internal Agent Output Format

Each agent must output its findings using the following structure before the final synthesis. This granular data ensures the final report is grounded in specific code evidence.

#### 📋 DIMENSION: [Agent Name]
**Qualitative Score:** [1-100]
*Based on adherence to design patterns (SOLID, DRY) and level-specific criteria.*

**BEST PRACTICES (✅):**
- <specific observation with file/line reference if possible>

**NOT IDEAL BUT NOT BAD (⚠️):**
- <observation> [NEEDS JUSTIFICATION? yes/no]

**BIG NO-NOs (🚨):**
- <observation with severity: HIGH / MEDIUM>

**QUESTIONS TO ASK CANDIDATE:**
- <question> [requires justification]

*Note: If a section has nothing to report, write "None observed."*

---

## 4. Dynamic Asset-Based Scoring

The system must be task-agnostic. To calculate the final score, the agent must:
1. Identify the **Task Name** from the input.
2. Locate the corresponding weight file in `/mnt/data/assets/weights/<task_name>.json`.
3. If no specific file is found, default to `/mnt/data/assets/weights/default_weights.json`.

**Weight File Structure Example:**
```json
{
  "structure_architecture": 0.25,
  "security_stability": 0.20,
  "code_quality_readability": 0.20,
  "requirement_coverage": 0.15,
  "design_ux_polish": 0.10,
  "devops_setup": 0.10
}

---

## 5. Reporting & Document Generation

The agent must generate **two separate .docx files** in the sandbox. These files are the primary deliverables for the Monday.com "Files" column.

### File 1: `technical_report_dev.docx`
**Target Audience:** Engineering Managers / Lead Developers.
- **Header**: Total Score, Repo URL, Position Level.
- **Deep-Dive**: Individual Agent scores (with weights applied).
- **Findings**: Full list of Best Practices (✅) and Red Flags (🚨) with file/line citations.
- **Code Snippets**: Include relevant code blocks for any HIGH severity issues.

### File 2: `recruitment_summary_hr.docx`
**Target Audience:** Recruiters / HR.
- **Header**: Recommendation (SUCCESS/FAILURE/MANUAL), Total Score.
- **Insights**: Executive summary of main strengths and primary risks.
- **Interview Guide**: 3-5 tailored interview questions with "What to look for in the answer."

---

## 6. Output Format (Optimized for Make & Monday.com)

**IMPORTANT:** Return the following JSON block at the end of the session. Make.com will use these paths to download the files and upload them to the Monday board.

### [DATA_START]
{
  "result": "SUCCESS | FAILURE | MANUAL REVIEW",
  "final_score": 0,
  "short_summary_text": "One-sentence overview of the candidate for the Monday status update.",
  "dev_report_path": "/mnt/user-data/outputs/technical_report_dev.docx",
  "hr_report_path": "/mnt/user-data/outputs/recruitment_summary_hr.docx",
}
### [DATA_END]