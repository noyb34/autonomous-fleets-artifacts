# Operating manual specification

This proposed reference policy defines an editable starting point for operating a group of software agents. Choose the owners, limits, and approval system before applying it to a real service. The templates describe intended controls and provide no evidence that a particular system enforces them.

## Assign decision ownership

Assign a named person or service identity to each role in [role-profiles.yaml](role-profiles.yaml). A role name describes responsibility. Access to a tool or credential requires separate configuration.

| Decision | Accountable role | Required record |
| --- | --- | --- |
| Define the outcome and accept the result | `owner` | Request and acceptance criteria |
| Assemble the work packet | `planner` | Inputs, permitted actions, and proposed budget |
| Approve a write to an external system | `owner` | Approval bound to the exact packet and proposed change |
| Execute an approved action | `executor` | Action result and cost entry |
| Check the result | `reviewer` | Evidence against each acceptance criterion |
| Resume a blocked task | `operator` | Diagnosis, checkpoint, and recovery attempt |

For a consequential write, assign the approving owner and executing agent different identities. Have a reviewer other than the executor check the result. Document any different separation policy before execution.

## Freeze the work packet

Use [work-packet-schema.json](work-packet-schema.json) to describe one bounded request. Record the `task_id`, `packet_id`, and `budget_id` in the execution ledger, which is the durable record of attempts, approvals, costs, and results. Preserve those identities across delegation and recovery. For a proposed write, allocate an approval-record reference and insert it as `write_approval_ref` before freezing the packet. Store those packet bytes and their SHA-256 digest, then request approval bound to that digest.

Include versioned inputs, permitted resources and actions, acceptance criteria, spending limits, and recovery limits. Keep credentials and private source material outside a public packet. References can identify access-controlled material without copying its contents.

When the objective, scope, budget, or approved change needs revision, stop execution and obtain a separately reviewed amendment. Record its relationship to the original packet and approval. Do not overwrite an approved packet or erase the task's earlier spending. If a frozen draft has `write_approval_ref: null`, use a reviewed amendment to insert the allocated reference and freeze the amended bytes with a new digest before requesting write approval.

## Check the write boundary

A write boundary is an action that changes external state, such as publishing a page, sending a message, changing a record, or deploying software. Prepare a reviewable proposed change before requesting approval.

The executor must verify the following conditions immediately before the write:

1. The approval record comes from the configured authority and identifies the permitted approver.
2. The approval covers the exact packet digest, proposed change, destination, and permitted action.
3. The approval is active, unexpired, and has not been revoked.
4. The destination still has the reviewed version or other recorded precondition.
5. The task has no other active executor and has enough uncommitted budget for the action.

If a condition fails or cannot be checked, record a blocked result. Schema validation checks document structure. It cannot authenticate an approver, establish consent, verify live state, or authorize execution.

## Account for context and execution costs

The budget applies to the whole task, including planning, execution, review, tool calls with charges, and recovery. Keep cumulative usage in the ledger. A new worker or recovery attempt retains the same `budget_id` and remaining allowance.

Record each call's role, input tokens, output tokens, reported cached tokens when available, provider charge or estimate, and currency. Record whether the cost is estimated or settled. The packet uses integer minor currency units for its cost limit, such as cents for USD.

Before admitting work, reserve its estimated cost and token use atomically against the remaining allowance. Include a conservative allowance for uncertain charges. Reconcile the reservation with reported usage afterward. Unknown usage remains reserved until reconciled. Stop further paid work when the remaining allowance cannot cover it.

The input manifest records each source's purpose, version, and estimated token size. Check the assembled request, including instructions and prior messages, against `max_context_tokens_per_call`. Count context sent again on a later call in the cumulative task total. A smaller packet is acceptable only when it still contains the evidence needed for the decision.

## Recover a blocked task

Follow [request-to-execution-flow.md](request-to-execution-flow.md) for the complete sequence. Use these conditions for recovery:

1. Record the last confirmed action, checkpoint, error, unresolved charges, and external state.
2. Verify that the original executor has stopped and that the task is neither complete nor retired.
3. Inspect the cause before another attempt. If a write's outcome is unknown, reconcile it with the destination before deciding whether it can be repeated.
4. Confirm that the original scope, approval, budget identity, and remaining allowance still apply.
5. Reserve one recovery attempt in the ledger before resuming from a verified checkpoint.
6. Stop when the attempt limit is reached, the same cause remains unresolved, or recovery needs a change in authority or scope. Give the owner the evidence and the decision needed.

Set `max_attempts` to zero when automatic recovery is inappropriate. An attempt limit bounds recovery activity. It does not grant permission to repeat a write.

## Verify completion

The reviewer records evidence for each acceptance criterion. The owner accepts or rejects the result. Mark the task complete only after the external outcome and outstanding cost records are reconciled. Preserve the packet, approval reference, ledger, and review so another operator can determine what happened.
