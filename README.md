# Autonomous fleet artifacts

Editable reference templates for product operators and engineers designing software-agent workflows. These files accompany [Operating Autonomous Fleets: A Product Operator's Runbook](https://noyb34.github.io/autonomous-fleets/).

The policies and schema describe proposed controls. Applying them requires an implementation that verifies identities, approvals, live state, spending, and recovery limits. The files alone do not enforce those controls or demonstrate that a system follows them.

## Use the artifacts

Start with the operating manual, then assign roles, define a packet, and follow the execution procedure.

| File | Purpose |
| --- | --- |
| [operating-manual-spec.md](operating-manual-spec.md) | Defines decision ownership, write approval, cost accounting, and recovery requirements. |
| [role-profiles.yaml](role-profiles.yaml) | Describes the owner, planner, executor, reviewer, and operator roles. Configure permissions separately. |
| [work-packet-schema.json](work-packet-schema.json) | Defines the structure of a bounded request, including inputs, scope, budget, authorization references, and recovery limits. |
| [request-to-execution-flow.md](request-to-execution-flow.md) | Describes the sequence from task preparation through execution, review, and recovery. |

## Edit and review changes

Use Git, Python 3, and a GitHub account with access to this private repository. The local checkout is `/Users/archon/autonomous-fleets-artifacts`.

`develop` is the default branch for integrating reviewed changes. `main` holds the accepted baseline. Create a feature branch from `develop`, open a pull request into `develop`, and use a separate pull request from `develop` into `main` when the changes are ready for that baseline.

1. Check for uncommitted work before switching branches. Commit or preserve existing changes before continuing:

   ```sh
   cd /Users/archon/autonomous-fleets-artifacts
   git status
   git switch develop
   git pull --ff-only
   git switch -c feat/update-work-packet
   ```

2. Edit the relevant files. For a schema change, check the JSON syntax:

   ```sh
   python3 -m json.tool work-packet-schema.json > /dev/null
   ```

   A successful check exits without output. This command checks JSON syntax; it does not validate a task packet against the schema or verify runtime controls.

3. Review the changes, then stage and commit only the intended files. This example commits a schema edit:

   ```sh
   git diff --check
   git diff
   git add work-packet-schema.json
   git diff --cached
   git commit -m "Clarify work packet requirements"
   git push -u origin feat/update-work-packet
   ```

4. On [GitHub](https://github.com/noyb34/autonomous-fleets-artifacts), open a pull request with `develop` as its base. Describe the changed behavior and the checks performed. Review it before merging.

5. After the merge, update the local integration branch:

   ```sh
   git switch develop
   git pull --ff-only
   ```

Choose a branch name and commit message that describe each change. Review related Markdown instructions when editing the schema so the files remain consistent.

The artifacts use the [MIT license](LICENSE).
