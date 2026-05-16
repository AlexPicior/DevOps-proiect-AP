# CI/CD Pipeline Documentation

## Pipeline Stages

### Stage 1 — Test
Runs on every push to main or any pull request. Checks out the code, sets up Node.js 20, installs dependencies, and runs the Jest test suite. If this stage fails, the pipeline stops — build and deploy never run.

### Stage 2 — Build
Runs only after the test stage passes. In this project it is a placeholder, but in a real project this is where you would compile TypeScript, bundle assets, or build a Docker image.

### Stage 3 — Deploy
Runs only after build passes and only on the main branch. This prevents deploy from triggering on pull requests.

---

## Tools Chosen

| Tool | Purpose |
|---|---|
| GitHub Actions | CI/CD runner — free, built into GitHub, no extra setup |
| Node.js 20 | Runtime for the sample app |
| Jest | Testing framework — simple, zero config for basic JS |
| Ubuntu | Runner OS — standard and well supported |

---

## Lessons Learned

- **The .github folder must be at the root of the repository**, not inside a subfolder. GitHub only detects workflows from that specific location. Moving it one level down breaks discovery silently — the Actions tab simply shows nothing.
- **Working directory matters in layouts where the app lives inside a subfolder.** When package.json is not at the repo root, npm install fails because it cannot find the file. The fix is specifying a working directory at the job level so every step runs from the correct folder.
- **File permissions differ between Windows and Linux.** Jest installed on Windows and committed to git loses its executable bit when the Linux runner tries to use it. The fix is either running chmod on the binary before the test step, or using npx to invoke jest instead.
- **Git tokens need the workflow scope** to push changes to the .github/workflows folder. The default token or an old personal access token will silently reject the push with a permissions error.
- **The needs keyword is what controls stage ordering.** Without it, all jobs run in parallel. With it, you get a strict sequence where a failure in an earlier stage blocks everything that comes after it.
