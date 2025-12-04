# Project: Go-Reviewer-CLI

## 1. Objective
To build a local CLI tool that acts as an intelligent peer reviewer, analyzing code changes before they are committed to ensure quality, catch bugs, and suggest optimizations using the Gemini LLM.

## 2. Architecture & Workflow
This is the core logic of the application.

### A. The Input Pipeline (Git & Context)
* **Trigger:** The user runs `go-reviewer review` in the terminal.
* **Diff Extraction:** The tool runs `git diff` (or `git diff --staged`) to capture specific changes.
* **Context Gathering:** (Decision Point needed here)
    * *Current Strategy:* Read the raw diff.
    * *Proposed Enhancement:* Read the full file content for any modified files to understand where the changes fit.

### B. The Processing Engine (LLM)
* **Prompt Construction:** Assembles a system prompt that defines the "Senior Go Engineer" persona + the diff + relevant code context.
* **API Interaction:** Sends the payload to the Gemini API (Google AI Studio).

### C. The Output Layer (User Experience)
* **Terminal Display:** Renders markdown responses directly in the terminal (using a library like `glamour` or `bubbletea`).
* **Interaction:**
    * **Level 1:** Static Report (List of issues/suggestions).
    * **Level 2:** Interactive Chat (User can ask "Why is this wrong?" or "Refactor this for me").

## 3. Key Features
* **Smart Diff Parsing:** Ignore vendor files (`go.sum`, `vendor/`) and auto-generated code to save tokens.
* **Security Check:** Basic scan for hardcoded secrets before sending data to LLM.
* **Performance Focus:** Only review changes, not the whole repo.

## 4. Tech Stack
* **Language:** Golang 1.23+
* **AI Model:** Gemini 1.5 Flash (optimized for speed/cost) or Pro (for complex logic).
* **Libraries:**
    * Git interaction: `os/exec` or `go-git`
    * TUI (Terminal UI): `bubbletea` (for interactivity) or `cobra` (for CLI commands).

***

### Let's tune the engine 🔧

Looking at **Section 2A (Context Gathering)**, this is our biggest trade-off.

If we *only* send the diff (the `+` and `-` lines), the LLM might hallucinate because it doesn't see the variable definitions. If we send *every* file, it's too slow.

Which approach feels right for our MVP (Minimum Viable Product)?
1.  **Strict Diff:** Only send the added/removed lines. (Fastest, but potentially less accurate).
2.  **File-Scope:** Send the diff **plus** the entire content of the files that were touched. (Good balance of context vs. speed).