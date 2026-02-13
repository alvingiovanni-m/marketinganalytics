# Agent Protocol

This document defines the strict protocol for any AI agent or developer working on the `marketinganalytics.github.io` project.

## 1. Project Overview
*   **Goal**: Provide a platform for customized analysis tools (web apps).
*   **Platform**: GitHub Pages (Static Website).
*   **Data Privacy**: **CRITICAL**. This project must NOT store or expose sensitive data.
    *   All data processing must happen **client-side** (in the browser).
    *   No data files (CSV, JSON, etc.) should be committed to the repository.
    *   No data should be sent to external servers without explicit user approval and strong justification.

## 2. Project Structure
Maintain this directory structure:
*   `index.html`: The main landing page. Must contain links to all available tools.
*   `tools/`: The dedicated directory for all analysis tools.
    *   All new tools must be created inside this folder: `tools/<tool-name>/`.
    *   Each tool must be a self-contained web app with its own `index.html`.
    *   Each tool must have a `README.md` explaining its usage.

## 3. Work Process (Mandatory)
You must follow this cycle for every request.

### Phase 1: Planning
1.  **Analyze**: Understand the requirements and security implications.
2.  **Checklist**: Create a detailed, step-by-step checklist for the task.
3.  **Review**: Present this checklist to the user before starting execution.

### Phase 2: Execution
**For every step in your checklist:**
1.  **Context**: Explicitly state which step you are working on.
2.  **Status**: Display the current state of your checklist (e.g., using `[ ]`, `[/]`, `[x]`).
3.  **Implement**: Perform the necessary code changes or actions.
4.  **Verify**: Confirm the step is completed before moving to the next.

### Phase 3: Completion
1.  **Security Audit**: Double-check for accidental data exposure or hardcoded secrets.
2.  **Integration**: Ensure the new tool is linked in the main `index.html`.
3.  **Documentation**: Update the tool's README and the project's main README if necessary.

## 4. Security & Tech Stack Guidelines
*   **Tech Stack**: HTML, CSS, JavaScript (Vanilla or lightweight frameworks like React/Preact via CDN or buildless if possible).
*   **Zero Backend**: The architecture must remain serverless.
*   **Git Rules**:
    *   Always check `.gitignore` before adding files.
    *   Never commit `.env` files or local data dumps.
