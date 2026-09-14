# Request-to-execution flow

This proposed reference procedure describes one task from intake through acceptance. Before using it, assign the [five roles](role-profiles.yaml), choose a durable execution ledger, and configure the approval checks in the [operating manual](operating-manual-spec.md).

## Prepare and approve the task

1. The owner records the requested outcome, permitted destination, and acceptance criteria.
2. The planner creates a packet that follows [work-packet-schema.json](work-packet-schema.json). It includes versioned inputs, scope, a fixed budget identity, and a recovery limit.
3. The planner checks the packet's structure. The executor prepares a reviewable change using only permitted preparation actions. A preparation action that itself changes external state requires its own applicable approval.
4. For a proposed write, the executor allocates an approval-record reference, inserts it as `write_approval_ref`, and freezes the packet. It records those bytes, their digest, the proposed change, and the destination's expected state. The owner reviews that evidence and records approval or rejection in the configured approval system.
5. The executor verifies the approval immediately before a write. A missing, expired, revoked, mismatched, or unverifiable approval leaves the task blocked.

A packet can pass schema validation while `write_approval_ref` is `null`. Before seeking write approval for that packet, allocate and insert the approval-record reference, then freeze the bytes and digest. If the packet was already frozen with a null reference, make this change through a reviewed amendment and freeze a new digest. Approval must bind the final frozen bytes. An approval reference must resolve to an authentic record that covers the exact packet and change.

## Execute and review the result

1. The executor claims the task in the ledger and reserves the next action's estimated cost. The claim must prevent concurrent execution of the same task.
2. The executor checks the packet identity, approval, live destination state, and remaining budget. If a check fails, it records the reason and stops.
3. The executor performs the permitted action, records the external result, and reconciles the cost reservation. It keeps evidence of any uncertain outcome or charge.
4. The reviewer checks the result against each acceptance criterion and records the supporting evidence.
5. The owner accepts the result or records the remaining work. The task becomes complete after the outcome and costs are reconciled.

Use the same `task_id`, `packet_id`, and `budget_id` for worker assignments and recovery attempts. Record each attempt separately so its result and cost remain attributable.

## Handle a blocked task

1. The operator records the failed check, last confirmed action, checkpoint, and remaining allowance. It confirms that the earlier executor has stopped.
2. The operator checks whether an uncertain write already took effect. Until that is resolved, the task remains blocked.
3. The operator diagnoses the cause and verifies that the proposed recovery fits the original scope, active approval, and recovery policy.
4. If an attempt remains, the operator reserves it in the ledger and resumes from the verified checkpoint. If the same cause remains unresolved, the operator stops further attempts.
5. If recovery needs changed authority, scope, or limits, the operator sends the owner the recorded evidence and requested decision. Any amendment preserves the original task history and cumulative spending.

A complete or retired task stays terminal. A replacement request requires an explicit owner decision and a recorded relationship to the earlier task.
