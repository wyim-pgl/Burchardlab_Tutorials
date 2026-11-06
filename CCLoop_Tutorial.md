# ccloop: Continuous Claude and Codex Workflows

ccloop runs a long project goal through Claude Code, Codex, and Kimi Code, with saved checkpoints and independent peer review. Use it for coding and analysis workflows where the work should continue across several short model turns.

## Start a goal

Run ccloop from a POSIX shell. In WSL, use the WSL path to your project.

```bash
cd /path/to/project
ccloop --driver codex --goal "Describe the result you need and how to verify it"
```

Use `--driver claude` to start from Claude Code. Managed execution is the default. ccloop saves the run ledger outside the project and continues until the original success criteria are independently verified, you stop it, or it reaches a real access or permission blocker.

To continue an existing run, use the exact ledger path printed by ccloop and preserve the original goal:

```bash
ccloop --driver codex --project /path/to/project --goal "The original goal" --root /path/to/ccloop-run
```

## Models and peer questions

You do not need to include a model name in the goal or peer question. With automatic model updates enabled, ccloop selects a model profile and records the requested and observed model separately. Claude uses the dynamic `opus` alias, which resolves to the current Opus model available to that provider. Kimi Code uses the newest K-series model configured in that computer's Kimi Code catalog. Kimi availability and model aliases can differ between accounts; sign in and refresh the model catalog on each computer first.

ccloop checks model metadata on a 30-minute cache. An explicit model flag pins that run's model profile. An inaccessible provider may trigger the configured Kimi fallback; Kimi cannot review its own work.

Ordinary technical questions go to the opposite model through ccloop. The manager records the question, response, and the worker's choice before continuing.

## Long tasks and saved progress

Each worker call handles one pending action and targets completion within 240 seconds, leaving a margin under ccloop's 300-second per-call limit. If the action needs more work, the worker records the remaining substeps in its checkpoint. ccloop reviews the checkpoint and sends the next action in a later call. It asks at most one blocking question per turn.

The run ledger includes `CONTROL.md`, `state.json`, `ccloop.md`, and bounded recovery views such as `resume.md`, `intend.md`, `todo.md`, and `quarantine.md`. These preserve the original goal, decisions, completed work, next steps, and items that must not be retried without new evidence. A timed-out or interrupted action is reconciled before it can be run again.

## HPC and permissions

On HPC installations with a ccloop direct-client profile, Claude and Kimi Code start without submitting the model client through Slurm. This only applies to the model client. Submit scientific and analysis jobs through the lab's normal Slurm workflow.

ccloop keeps the clients' normal permission controls. A peer recommendation does not grant permission, and authentication or approval requests still require the relevant user action. Do not put credentials or private run ledgers in the project or this wiki.
