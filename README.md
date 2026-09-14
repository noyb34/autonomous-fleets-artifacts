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

The artifacts use the [MIT license](LICENSE).
