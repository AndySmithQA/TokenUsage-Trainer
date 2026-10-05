# Spend less, ship the same

A 2-hour live remote demonstration and discussion. The trainer shows how many tokens GitHub Copilot is using, and how to use fewer of them. Delegates complete one 30-minute online lab at the end.

The live meters are in **GitHub Copilot CLI**:

- `/context` — what is in the window right now (instructions, tools, MCP, history)
- `/usage` — tokens this session actually consumed, including cache figures when the model reports them

A local estimator compares files **before** anyone spends a credit, so the expensive examples (a bloated instructions file, HTML release notes) are measured and not sent.

## Who it is for

Developers who already use GitHub Copilot in VS Code. Trainers who need a lab, a delegate script, and a run of show.

**Length:** 2 hours live remote delivery, then one independent online lab of about 30 minutes.

## Run the class

| Role | Start here |
| --- | --- |
| Trainer | [trainer/guide.md](trainer/guide.md), [slide content](trainer/slide-content.md), [demo runbook](trainer/demo-runbook.md), and [answer key](trainer/answer-key.md) |
| Delegate | [delegate/workbook.md](delegate/workbook.md), filling in [delegate/scorecard.md](delegate/scorecard.md) |

Delegates do not need a configured environment during the live remote session. The preferred online lab is a hosted browser workspace containing the course files, Node.js, and Copilot CLI. The workbook also documents a local fallback.

During the live call, the trainer drives the demos from **`lab/app`** over screen share. Delegates later open that same folder for the lab at the end, not the course root. The always-on Copilot file is four lines of output control. Discount rules sit in a path-scoped instructions file. Landmines sit in `AGENTS.md`. A plan-then-execute skill stays on disk until someone loads it.

The file layout follows the community guide [github-copilot-token-optimization](https://github.com/olivomarco/github-copilot-token-optimization). That repository is practitioner experience, not official GitHub documentation. The live meters in this course are still Copilot CLI `/context` and `/usage`.

## How the 2 hours are spent

| Clock | Who | What |
| --- | --- | --- |
| 0:00 | Trainer | Why tokens cost, and the six levers |
| 0:10 | Trainer | `/context`, `/usage`, and Auto model choice |
| 0:30 | Trainer | A short reply beside a long one |
| 0:45 | Everyone | Remote poll, discussion, and screen break |
| 1:00 | Trainer | Bloated instructions, a new chat, Ask versus Agent |
| 1:30 | Everyone | Scenario discussion |
| 1:45 | Trainer | Recap and online lab handoff |

Afterwards, delegates complete one lab of about 30 minutes: measure their own window, audit context files, compare file sizes, diagnose the pricing bug, write a constrained prompt, implement the fix, and verify the tests.

## Lab folder

```text
lab/app          AndyCorp Lite. Open this folder. Output control, landmines, scoped instructions.
lab/fixtures     Bloated handbook, wiki-style AGENTS file, HTML notes, Markdown notes.
lab/tools        estimate-tokens.mjs and write-bundle.mjs. No npm install.
```

Check the app:

```powershell
cd lab\app
node --test
node ..\tools\estimate-tokens.mjs .github\copilot-instructions.md ..\fixtures\copilot-instructions.bloated.md
```

`node --test` should fail two tests at the start of class. The estimator should show the bloated instructions far above the four-line Copilot file (about 2,400 estimated tokens versus about 40).

## Sources

Session meters, Auto, and conversation controls follow GitHub's docs:

- [Optimizing your AI usage to maximize efficiency and reduce cost](https://docs.github.com/en/copilot/tutorials/optimize-ai-usage)
- [About Copilot auto model selection](https://docs.github.com/en/copilot/concepts/models/auto-model-selection)
- [Managing context in GitHub Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/context-management)
- [About GitHub Copilot CLI session data](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/chronicle)
- [Setting an AI credit session limit](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/set-session-limit)

The sample app's file layout follows the community guide [github-copilot-token-optimization](https://github.com/olivomarco/github-copilot-token-optimization): output control in the always-on file, landmines only, `applyTo` for path rules, on-demand skills, and Markdown before rich files. That repository states it is not official GitHub or Microsoft guidance.

CLI slash-command names move between versions. The trainer guide says to trust `copilot help` and `/help` on the version used for delivery.
