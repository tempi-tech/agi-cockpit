<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Fleet

Learn how to define dependency-aware multi-agent work in Fleet YAML, supervise its live graph, and recover safely from interruption or failure.

> Verified with AGI Cockpit 4.64.0 on 2026-08-30. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/fleet)

Fleet defines multiple AI agents, command-based verification, and human approval as a dependency graph in YAML, then executes that graph as one Run. Each agent node is a normal Cockpit task. Cockpit manages execution order, parallelism, waiting, recovery, and history.

Use `cockpit task run` for one delegated job. Choose Fleet when dependency order or parallel work should be repeatable, when tests or approvals belong between phases, or when the workflow must recover after the app restarts.

## Distinguish a Fleet from a Run

| Term | Meaning |
| --- | --- |
| Fleet | Reusable YAML describing nodes, dependencies, arguments, and default agent settings |
| Run | One execution of that Fleet, with its own `runId`, workspace, and event history |
| Node | An agent task, a message to an existing task, a command gate, a human gate, or a bounded loop |
| Fleet panel | Live view of the dependency graph, node states, tasks, reports, and events |

Changing the Fleet definition does not rewrite a Run that has already started. After changing the definition, use `rerun` to create a new Run instead of using `retry` on the old one.

## Choose where to store it

| Scope | Path | Best for |
| --- | --- | --- |
| Project | `<repo>/.cockpit/fleets/<name>.yaml` | Repeatable development and verification in one repository. Each Run gets a Git Worktree |
| Global | `~/.agi-tools/data/cockpit/fleets/<name>.yaml` | Cross-repository or repository-free routines |

A project Fleet is part of the repository workflow, much like CI configuration. Commit definitions that the team should reuse. A global Fleet has no repository of its own, so give each node a `directory` when it needs one.

## Create a minimal Fleet

This project Fleet investigates, implements, tests, and summarizes in order. `defaults` flows into agent nodes, while `needs` creates dependencies. A command gate starts no agent and passes only when its command exits with code 0.

`.cockpit/fleets/check-and-fix.yaml`

```yaml
version: 1
name: check-and-fix
description: Investigate one problem, implement the fix, and verify it
args:
  target:
    required: true
    description: What to fix
defaults:
  agent: codex
  effort: medium
max_parallel: 2
nodes:
  investigate:
    prompt: |
      Investigate {{args.target}}. Change nothing.
      Report the cause, affected files, and a concrete fix plan.
  implement:
    needs: [investigate]
    prompt: |
      Implement the fix and verify the focused behavior.
      Investigation: {{needs.investigate.report}}
  tests:
    type: gate
    gate: command
    needs: [implement]
    run: pnpm test
  summarize:
    needs: [tests]
    prompt: |
      Review the completed change and report the result, changed files,
      and verification evidence.
```

The only current schema `version` is `1`. Names and node ids accept letters, digits, `_`, and `-`. Unknown keys, missing dependencies, cycles, unresolved substitutions, and explicitly unsupported agent settings are validation errors.

## Validate before running

After saving the YAML, validate it before creating any task.

```bash
cockpit fleet validate .cockpit/fleets/check-and-fix.yaml --directory /path/to/repo
```

Start a new Fleet with `--max-parallel 1`. Watch the responsibilities, reports, and edit boundaries in sequence, then raise parallelism once the graph behaves as intended.

```bash
cockpit fleet run check-and-fix \
  --directory /path/to/repo \
  --arg target="search indexing" \
  --max-parallel 1
```

`run` returns a `runId` immediately instead of waiting for completion. Do not add `--wait` to `run`. Open the Fleet panel next, then wait only when needed.

```bash
cockpit side-panel fleet <runId>
cockpit fleet wait <runId>
```

`wait` returns when the Run completes, fails, stops, or pauses; when a human gate awaits an answer; or when a node is interrupted. If the watcher exits, resume from the last `latestSeq`.

```bash
cockpit fleet wait <runId> --since <latestSeq>
```

A timeout does not mean the Run failed. Use `wait` for continued monitoring and `status` for a one-time checkpoint. Do not build a short-interval `status` polling loop.

## Read the live graph

Use the Fleet panel to inspect:

- Which nodes are running, waiting, completed, failed, skipped, excluded, or awaiting approval.
- How far each parallel branch has progressed and which dependency blocks a downstream node.
- The real task, workspace, branch, and attempt count for each agent node.
- Node reports, structured output, command-gate results, and Run events.

The progress bar in the Run header groups every node by completed, running, failed, interrupted, stopped, skipped, or pending state and shows completed nodes over total nodes. It is a current status breakdown, not elapsed time or an estimated completion time.

Select a gate node to see the actual exit code for a command gate, or the approved or rejected result and answer text for a human gate. Exit code 0 passes; any other code fails. Inspect the gate details and output instead of inferring the result only from the overall Run status.

Use these commands for deeper inspection.

| Need | Command |
| --- | --- |
| Current state | `cockpit fleet status <runId>` |
| All nodes, resolved runtimes, branches, and task ids | `cockpit fleet show <runId>` |
| Why the Run made a decision | `cockpit fleet logs <runId>` |
| One node's report and failure reason | `cockpit fleet logs <runId> --node <nodeId>` |
| The node's real task | `cockpit task get <taskId>` |

Sending Steer directly to a live node task, canceling its turn, completing it, or removing it interrupts the node and pauses the Run. Unless you intend to intervene, supervise through the Fleet panel and `fleet` commands rather than operating node tasks by hand.

When a Fleet definition cannot be loaded, the panel distinguishes a missing file, invalid YAML, schema validation, and another load failure. It shows the target path and validation issues. When the path is available, use **Open file** to correct it and **Reload** to read the same definition again. If the file is missing, confirm the displayed path, create the file, then reload.

## Assign roles to nodes

Agent nodes can select `agent`, `model`, `effort`, `account`, `browser_identity`, `service_tier`, `workspace`, and `approval`. Put the majority configuration in `defaults`, then override only the nodes that differ. For a temporary Run-level experiment, use `--set '<node>.<field>=<value>'` or `--set '*.<field>=<value>'`.

One Run can assign design to Claude, implementation to Codex, and research to Grok. A setting written directly on a node fails validation when that agent does not support it. An unsupported setting inherited broadly from `defaults` or `*` is dropped only for that node.

`max_parallel` is the number of nodes that may be active at once, not the width of the graph. It defaults to 4 and accepts 1 through 32. Set it according to the machine and the number of usable agent accounts.

## Pass results downstream

Use `{{needs.<id>.report}}` for prose reports, `{{needs.<id>.files}}` for changed files, and `{{needs.<id>.branch}}` for the branch of an isolated node. Declare arguments under `args` before using `{{args.<key>}}`. A node cannot reference a sibling or downstream node outside its transitive dependencies.

When a machine decision needs a value, use `output_contract` instead of parsing report prose. The JSON Schema declares required fields and types. Before reporting completion, the node submits a value through `cockpit fleet output`, and Fleet validates it immediately.

```yaml
  inspect:
    prompt: Count unresolved problems and report the evidence.
    output_contract:
      type: object
      required: [issues]
      properties:
        issues: { type: integer, minimum: 0 }
  clean:
    type: gate
    gate: command
    needs: [inspect]
    run: test "{{needs.inspect.output.issues}}" -eq 0
```

Use reports for explanation and structured output for conditions and values. Do not interpolate agent-authored strings directly into a command gate. Use a validated number or boolean, or parse the validated JSON supplied as `FLEET_NEEDS_<ID>_OUTPUT`.

## Design parallel edits and Worktrees

A project Fleet creates a shared Worktree on `fleet/<name>-<runId>` for each Run. `workspace: shared` is the default, so sequential nodes see changes left on disk by earlier nodes.

Give concurrently editing nodes `workspace: isolated`. Each one works in a separate Worktree on `fleet/<name>-<runId>-<nodeId>`, so they cannot see one another's changes. Tell isolated nodes to commit, then use one downstream integrate node to merge the branches named by `{{needs.<id>.branch}}`.

The Fleet runtime never merges branches. Choosing changes and resolving conflicts belongs to the integrate node. Use shared parallel work only for read-only work or clearly disjoint edit scopes, because shared nodes can change the same file at the same time.

In a global Fleet, `workspace: isolated` requires a repository-root `directory` on that node. If the path is not a Git repository, the node fails instead of silently falling back to an ordinary folder.

## Branch, fan out, and repeat

| Feature | Purpose | Important behavior |
| --- | --- | --- |
| `when` | Runtime branch based on an upstream pass or approval | A false node is `skipped`, and the skip propagates downstream |
| `enabled` | Remove an optional, expensive node through a Run argument | Evaluated once at Run creation. False becomes `excluded`, while contracted dependencies continue |
| `foreach` | Process each item discovered by an earlier node | Each item consumes a `max_parallel` slot and the group combines results downstream |
| Bounded loop | Repeat repair and verification up to a limit | `max_iterations` prevents an infinite loop; exit code 0 from `until.run` ends it |
| Message node | Prompt an existing Cockpit task outside the Fleet and wait for its report | The Fleet does not own that task, so stopping or removing the Run never completes or stops it |

When using `enabled`, run `validate` with the same arguments first and inspect which nodes become `excluded`. Give a writing `foreach` node `workspace: isolated`. Give every loop a realistic bound, then inspect the saved evidence before granting more iterations.

## Put facts and approval in gates

| Gate | Passes when | Use it for |
| --- | --- | --- |
| command | A non-interactive command exits with code 0 | Tests, lint, type checking, and machine verification of publication state |
| human | The user approves a Cockpit Ask | Immediately before pushing, publishing, deleting, sending, spending, or another externally visible or irreversible action |

A command gate times out after 30 minutes and serializes against command gates from other Runs using Worktrees of the same Git repository. `retries` accepts 0 through 3, but automatic retries are limited to load-dependent flakes where one or two failed tests are named in Vitest format. Type errors, build failures, timeouts, and the same test failing twice fail immediately.

When a Vitest command gate times out, its gate output and failed-test list record the tests that failed before the timeout, test files that started streaming output but did not finish, and up to five of the slowest completed tests that took at least 30 seconds. Inspect the Fleet panel's Report or `cockpit fleet logs <runId> --node <nodeId>` for evidence of where the suite stopped.

Write a human gate's Ask so it stands alone: state what finished, what evidence was checked, and what external action approval will cause. Rejection skips downstream work. Dismissing the Ask interrupts the gate and pauses the Run instead of rejecting it.

## Recover from pause and failure

| Situation | Action | What remains |
| --- | --- | --- |
| Stop dispatching new nodes while active nodes finish | `cockpit fleet pause <runId>` | Currently running nodes continue |
| Continue after pause, app restart, or interruption | `cockpit fleet resume <runId>` | Same Run, workspace, and completed nodes |
| Fix one failure and redo only its descendants | `cockpit fleet retry <runId> --node <nodeId>` | Unrelated completed and passed nodes |
| Start over after changing the definition | `cockpit fleet rerun <runId>` | Same Fleet and arguments, but a new `runId` and workspace |
| Terminate active tasks and gates | `cockpit fleet stop <runId>` | The saved Run remains resumable |
| Permanently delete unneeded history | `cockpit fleet remove <runId>` | Nothing. Only a terminal Run can be removed |

Downstream nodes are skipped after a failure, while independent branches continue. Inspect `logs --node` first, then retry the earliest node that is actually wrong. Retrying a downstream node cannot correct bad upstream output.

After an app restart, Cockpit reattaches tasks that are still alive and records other active nodes as `interrupted`. On `resume`, an ordinary agent node continues in a new task using the same workspace. Write prompts that re-check the current files instead of assuming a clean checkout or retained conversation memory.

When an account usage limit interrupts work, select another signed-in profile while recovering.

```bash
cockpit fleet resume <runId> --set '*.account=<profile>'
cockpit fleet retry <runId> --node <nodeId> --set '<nodeId>.account=<profile>'
```

Cockpit retains the 200 most recent terminal Runs. `remove` permanently deletes the event history. Save needed reports, diffs, and publication URLs first, and stop a running or paused Run before removing it.

## Determine completion

Do not rely only on a `completed` label in the Fleet panel. Confirm that:

- Every required node completed or passed, with no unintended `skipped`, `excluded`, or `interrupted` state.
- The integrate node merged isolated branches and reported conflict resolution and commits.
- Command gates passed in the intended final workspace.
- External actions after a human gate stayed within the approved scope and produced a verifiable URL or id.
- The final report summarizes changes, verification, and remaining constraints.

See the [`cockpit fleet` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/fleet) for every field and option, [Task management (CLI)](https://agi-labo.com/en/tools/cockpit/docs/task-management) for single delegation and parent-child tasks, and [Master Agent](https://agi-labo.com/en/tools/cockpit/docs/master-agent) when the decomposition itself requires judgment.
