# Testing opencode-fusion

## Automated checks

Run these from the repository root:

```powershell
npm test
npm run check-install
npm run check-profiles
```

`npm test` is the main validation suite. `check-install` compares the skill
bundle's prompts with installed copies under `~/.config/opencode/agent/`.
`check-profiles` verifies the model IDs in shipped profiles.

To validate the lint fixture, run `npm run lint` with `test-playground/` as the
working directory. Build and plan agents should use the tool's working-directory
parameter because `npm --prefix test-playground run lint` may not match their
command allowlist.

## Manual verification

### Skill installation

Install the published skill:

```powershell
npx skills add mihneaptu/opencode-fusion --skill fusion-setup -g -a opencode -y
```

Fully restart opencode and confirm that `fusion-setup` appears in the skill
list.

### Configuration flow

In a fresh session, ask opencode to `set up fusion`. Confirm that it asks for
the per-role models, updates `~/.config/opencode/opencode.json`, installs the
selected prompts, and shows the selected Build model after a full restart.

### Delegation flow

Seed a lint error in `test-playground/src/index.js`, then ask the Build agent to
fix it. Confirm that Build delegates the edit, reviews the result, and runs the
fixture lint itself. The fixture is gitignored, so review its changed files
directly rather than relying on `git diff`.

### Runtime audit

When surface output is not enough to establish which agent acted, inspect the
session database reported by `opencode db path` (typically
`~/.local/share/opencode/opencode.db`). Its session, message, and part records
provide the delegation tree and exact tool calls. Use this for targeted runtime
audits, not as a routine requirement for every change.
