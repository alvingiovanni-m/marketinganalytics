
### Phase 2: Execution
**For every step in your checklist:**
1.  **Status**: Display the current state of the task you are working using the checklist created in the folder: `tasks_planning/<tool-name>/` (e.g., using `[ ]`, `[/]`, `[x]`).
2.  **Steps**: work step-by-step based on the checklist. Dont go through all the steps all at once.
3.  **Context**: Explicitly state which step you are working on.
4.  **Implement**: Perform the necessary code changes or actions.
5.  **Verify**: Confirm the step is completed before moving to the next.

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
