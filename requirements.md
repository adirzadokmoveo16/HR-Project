# Recruitment System Characterization

## General Overview
The purpose of this system is to streamline the code task evaluation process within the company's recruitment pipeline. By transitioning from manual software engineer reviews to **AI-based automated reviews**, the system aims to provide high-accuracy scoring based on predetermined criteria. The system supports the candidate submission process, automates quality evaluation, and assists recruiters in making data-driven decisions regarding candidate progression.

---

## System Screens

### A. Submission Screen (Candidate Facing)
Used by the candidate to submit their technical task.
* **Fields:**
    * **Personal Details:** First Name, Last Name, Phone, Email.
    * **Task Submission:** Project Name, ZIP file upload (source code), Live Site URL, GitHub Link, and README file.
* **Key Features:**
    * **Auth:** Only whitelisted/expected candidates can submit.
    * **File Handling:** Secure ZIP upload with future parsing capabilities.
    * **Validation:** Client-side and server-side validation for required fields and link formats.
    * **Storage:** File persistence via AWS S3.

### B. Evaluation Screen (Recruiter Facing)
A secure dashboard for authorized personnel to review results.
* **Candidate Profile:** Displays personal details and submission links.
* **Detailed Results:**
    * Final weighted score and specific Frontend vs. Backend breakdowns.
    * Category-specific scores and verbal feedback (per Appendix A).
    * Summary verbal evaluation of the overall task.
* **Scoring Range Breakdown:**
    * **80-100 (Success):** Clear "Pass" indicator for next stage progression.
    * **60-79 (Manual Review):** Indicator flagging the task for human software engineer evaluation.
    * **0-59 (Failure):** Failure indicator; candidate will not proceed.
* **Key Features:**
    * Role-based access control (RBAC).
    * Data retrieval from cloud storage for knowledge retention.
    * Search and filter functionality for candidate management.

---

## AI Evaluation Engine (Core Pipeline)

### 1. Ingestion Layer
* **Atomic Extraction:** Unpacks ZIP files into a sandboxed temporary directory to mitigate path conflicts and security risks.
* **Structural Validation:** Verifies the presence of mandatory root folders (e.g., `Backend`, `Frontend`) before utilizing processing resources.

### 2. Context Extraction Layer
* **Noise Reduction:** Excludes non-source files (node_modules, binaries, build artifacts) to focus on core logic.
* **Dependency Mapping:** Generates a visual tree and import map to provide the AI with context on service/module interactions.
* **Significance Filtering:** Prioritizes "logic-heavy" files (controllers, services, components) over boilerplate.

### 3. Analysis Layer
* **Contextual Mapping:** The Agent analyzes entry points and file trees to evaluate architectural flow rather than just isolated syntax.
* **Persona-Based Review:** Utilizes a specialized "Senior Reviewer" persona to evaluate code against Appendix A criteria.
* **Qualitative Scoring:** Assigns 1-100 scores based on design patterns (SOLID, DRY) and custom rules.
* **Justification Logging:** Generates technical "Reasoning Notes" mapping specific code blocks to the assigned score.

### 4. Synthesis & Reporting Layer
* **Weighted Consolidation:** Applies Appendix A multipliers to raw scores to calculate a final weighted average.
* **Threshold Classification:** Automatically assigns "Success," "Manual Review," or "Failure" status based on the final result.
* **Result Serialization:** Packages all data into a structured JSON object to drive the UI.
* **Actionable Feedback:** Synthesizes notes into a summary of "Main Reasons" for the score and provides a roadmap for areas of improvement.

---

## Appendix A: Categories & Evaluation Emphases

### Structure & Modularity
* **Organization:** Clear folder hierarchy (e.g., `/services`, `/hooks`, `/components`).
* **Separation of Concerns:** Proper isolation of UI, business logic, and API calls.
* **Standards:** Consistent naming conventions and avoiding "God files" (large utils with mixed responsibilities).
* **Modularity:** Small, reusable components/functions and proper use of routing.

### DevOps & Innovation
* **Setup:** Comprehensive `README.md` with step-by-step instructions.
* **Configuration:** Proper use of environment variables (Secrets/API keys must not be hardcoded).
* **Tech Stack:** Use of relevant, modern libraries and tools.

### Performance & Readability
* **Frontend Efficiency:** Minimizing re-renders and efficient state updates.
* **Async Logic:** Proper handling of Promises/async-await without blocking the main thread.
* **Clean Code:** Code simplicity proportional to task complexity (avoiding over-engineering).

### Creativity & Design
* **UX/UI Polish:** Intuitive interfaces, including loading states and error handling.
* **Edge Cases:** Handling of empty states, slow connections, or invalid inputs.
* **Design Consistency:** Responsiveness and consistent spacing/typography.

### Execution & Stability
* **Requirements:** Coverage of all mandatory functional specifications.
* **End-to-End Flow:** Application runs correctly without manual intervention.
* **API Integration:** Correct use of HTTP methods, request payloads, and response handling.
* **Reliability:** Freedom from runtime crashes in main application flows.