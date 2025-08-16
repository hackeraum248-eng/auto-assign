# Copilot Coding Agent Instructions for auto-assign

## Project Overview
This project is a GitHub Action for automatically assigning reviewers and assignees to issues and pull requests. It is designed to:
- Randomly select reviewers/assignees from configured candidate lists
- Handle GitHub teams and individual users
- Skip issues/PRs based on draft status, keywords, or labels
- Integrate with GitHub's API using an authentication token

## Architecture & Key Files
- `src/index.ts`: Main entry point. Orchestrates the workflow based on GitHub event context and delegates to utility functions.
- `src/util.ts`: Core logic for filtering, selecting, and assigning reviewers/assignees. Handles team/user distinction, validation, and API calls.
- `src/inputs.ts`: Defines and parses all configurable inputs for the action.
- `action.yml`: Declares the GitHub Action interface and input parameters.
- `README.md`: Documents usage, configuration, and important caveats for permissions and tokens.

## Developer Workflows
- **Build/Test:** No explicit build/test scripts; TypeScript is used directly. Ensure dependencies are installed via `npm install`.
- **Debugging:** Use `core.debug` and `core.info` for logging. Run the action in a GitHub workflow or locally with a compatible runner.
- **Authentication:** The default `${{ secrets.GITHUB_TOKEN }}` is limited. For team operations, use a PAT with `repo` and `admin:org` permissions, or a GitHub App token.

## Project-Specific Patterns
- **Team Handling:** Teams can be specified as `org/team_slug` or `/team_slug`. The action attempts to assign team members if a team is listed.
- **Input Parsing:** All inputs are parsed and validated via `@wow-actions/parse-inputs` in `src/inputs.ts`. Types and defaults are strictly enforced.
- **Skip Logic:** PRs/issues are skipped if they are drafts (unless overridden), contain skip keywords, or have labels matching `includeLabels`/`excludeLabels` logic.
- **Reviewer/Assignee Selection:** Uses `lodash.samplesize` for random selection. Setting `numberOfReviewers` or `numberOfAssignees` to `0` assigns all candidates.
- **Validation:** Unknown GitHub usernames are ignored after validation via API.

## Integration Points
- **GitHub API:** All assignments and queries are performed via `@actions/github` Octokit client.
- **External Dependencies:**
  - `@actions/core`, `@actions/github` for GitHub Actions runtime
  - `@wow-actions/parse-inputs` for input parsing
  - `lodash.samplesize` for random selection

## Example Usage
See `README.md` for a sample workflow configuration. Key inputs include `reviewers`, `assignees`, `skipKeywords`, and label filters.

## Conventions
- All logic is contained in `src/` and invoked from `src/index.ts`.
- Inputs and outputs are logged for traceability.
- Team assignment requires elevated permissions; see README for details.

---

For questions or unclear patterns, review `src/index.ts`, `src/util.ts`, and `README.md` for implementation details and usage examples. If conventions or workflows are missing, ask the user for clarification.
