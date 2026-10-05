# Slide content — Spend less, ship the same

This is the content for a 2-hour micro-course. Use one slide per numbered section. Text under **On slide** is visible. Text under **Speaker notes** is for the trainer.

## Slide 1 — Spend less, ship the same

**On slide**

Minimizing GitHub Copilot token usage

- See where tokens go
- Reduce waste without reducing quality
- Leave with six habits

**Speaker notes**

Welcome delegates to the live remote session. Explain that today is demonstration and discussion. One online lab, about 30 minutes, is completed separately at the end. They do not need to open Copilot during the call.

Time: 2 minutes.

## Slide 2 — Today’s route

**On slide**

| Time | Activity |
| --- | --- |
| 0:00 | Why tokens cost |
| 0:10 | Demo: meters and Auto |
| 0:30 | Demo: short output versus essay |
| 0:45 | Chat poll and discussion |
| 0:55 | Screen break |
| 1:00 | Demo: context, threads, and modes |
| 1:30 | Scenario discussion |
| 1:45 | Recap and online lab handoff |

**Speaker notes**

Set expectations: the live session is trainer-led and ends at two hours. One online lab takes about 30 minutes and is completed separately at the end.

Time: 2 minutes.

## Slide 3 — Token anatomy

**On slide**

```text
your prompt
  → model selected
  → instructions + tools + MCP + history + files
  → cached prefix reused when stable
  → response generated
```

Input tokens = everything Copilot reads  
Output tokens = everything Copilot writes  
Cached input = stable context that can be reused

**Speaker notes**

The typed prompt is only one part of input. Hidden context can be much larger: instructions, tool definitions, conversation history, and attached files. Published model prices generally make output more expensive per token than input. Copilot’s exact per-model billing table is not public, so use the product meters and billing page for actual consumption.

Time: 3 minutes.

## Slide 4 — Six levers

**On slide**

1. Constrain output
2. Keep always-on context lean
3. Start a new thread when the task changes
4. Preserve the prompt cache
5. Use Ask, Agent, and tools deliberately
6. Match model capability to the task

**Speaker notes**

Read the list once. Do not explain every item yet; each appears in a demonstration.

Time: 3 minutes.

## Slide 5 — GitHub Copilot CLI: install and start

**On slide**

Why use it in this course?

- Live context and session-usage meters
- Repeatable commands for the demonstration and the lab
- Works beside VS Code without hiding the measurements

Windows installation:

```powershell
winget install GitHub.Copilot
```

Alternative, with Node.js 22 or later:

```powershell
npm install -g @github/copilot
```

**Speaker notes**

The hosted online lab should already contain the CLI. These commands are the local fallback.

After installation, open a new terminal and run:

```powershell
copilot --version
copilot login
cd path\to\lab\app
copilot
```

`copilot login` opens the GitHub sign-in flow. The account needs a Copilot licence. If `copilot` is still not found, open another terminal. The CLI is not being presented as the only way to use Copilot; it is used here because it exposes the measurements needed by this course.

Time: 3 minutes, within Demo 1.

## Slide 6 — CLI commands used in this course

**On slide**

Type `copilot` in PowerShell first. Then type slash commands inside the CLI.

```text
/context       Current window
/usage         Session totals and cache
/instructions  Loaded instruction files
/model         Select Auto or another model
/new           Clear history for a new task
/compact       Summarise a long, continuing task
/ask           Side question outside history
/mcp           Enabled MCP servers
/limits set max-ai-credits 30
/exit
```

**Speaker notes**

Do not type slash commands directly into PowerShell. `/context` is the live window reading. On this CLI, instruction tokens are included in System/Tools; `/instructions` shows which files supplied them. `/usage` accumulates during the session. The 30-credit limit is a soft pause and is the lowest value accepted by this CLI, not a promise that every session costs 30 credits.

Do not explain every command now. Point out `/context`, `/usage`, and `/instructions`; the others appear when needed. Introduce the first live demonstration.

Time: 2 minutes, followed by Demo 1.

## Slide 7 — Choose the capability you need

**On slide**

| Task | Model choice |
| --- | --- |
| Architecture or difficult debugging | Reasoning |
| Execute a clear plan | Mid-tier |
| Rename, format, document | Light |
| Unsure | Auto |

As much capability as needed. As little as necessary.

**Speaker notes**

Auto is the default. It routes the task, avoids unnecessary expensive reasoning, and receives a paid-plan discount. Ask through chat or audio:

- Rename a parameter in `formatMoney`: light.
- Decide whether to split pricing into services: reasoning.
- Change a known threshold from 2 to 3: mid-tier, Auto, or no model at all.

Time: 3 minutes, within Demo 1.

## Slide 8 — Output control pays immediately

**On slide**

Project default:

```text
Code only, no explanation.
Bullets over paragraphs.
No explanations unless asked.
```

Override when needed:

```text
Explain why this design is safer.
```

**Speaker notes**

Introduce Demo 2. Short is the default for routine work. An explanation is valuable when learning or making a decision; it should be requested deliberately.

Time: 2 minutes, followed by Demo 2.

## Slide 9 — Same question, different bill

**On slide**

Constrained:

```text
How does calculateOrderTotal work?
Four bullets maximum. No alternatives.
```

Verbose:

```text
Explain in detail. Include background,
an example, alternatives, and a summary.
```

Question for the group:

Which answer could you act on?

**Speaker notes**

Leave the two `/usage` output-token totals visible. The useful answer is not necessarily the longest answer.

Time: 3 minutes discussion after Demo 2.

## Slide 10 — Always-on context: landmines, not a wiki

**On slide**

Keep:

- Rules the code cannot reveal
- Dangerous exceptions
- Required verification commands
- Terse output defaults

Remove:

- Company history
- Discoverable project structure
- Generic engineering advice
- Duplicate instructions

**Speaker notes**

Introduce Demo 3. In AndyCorp:

- `.github/copilot-instructions.md` controls output.
- `AGENTS.md` contains only landmines.
- `.github/instructions/pricing.instructions.md` uses `applyTo`.
- The plan skill is on demand.

Time: 3 minutes.

## Slide 11 — Context comparison

**On slide**

Approximate file sizes:

- Always-on Copilot instructions: **40 tokens**
- Bloated handbook: **2,414 tokens**
- `AGENTS.md`: **28 tokens**
- Wiki-style AGENTS file: **218 tokens**
- Markdown release notes: **53 tokens**
- HTML export: **997 tokens**

Large context is paid repeatedly.

**Speaker notes**

These are character-based teaching estimates, not billing figures. The live `/context` reading is the real window measurement. Demonstrate the instructions swap without sending a prompt. Delegates reproduce the comparisons in the lab at the end of the course.

Time: 3 minutes, within Demo 3.

## Slide 12 — Threads and caching

**On slide**

New task → `/new` or New Chat  
Same long task → `/compact`  
Side question → `/ask`

Protect the cache:

- Pick model and reasoning before work starts
- Enable only required tools
- Keep instructions stable
- Append turns
- Attach a stable file once

**Speaker notes**

Demonstrate Messages growing, then falling after `/new`. Explain that switching models, tools, or instructions changes the prefix and can invalidate the cache.

Time: 3 minutes, within Demo 3.

## Slide 13 — Ask, Agent, or no AI?

**On slide**

Ask:

- Facts
- Explanations
- Small comparisons

Agent:

- Find several files
- Edit
- Run tests
- Iterate

No AI:

- You already know the one-line edit

**Speaker notes**

Show `/ask` not joining the history. In VS Code, point at Ask and Agent but do not send an Agent prompt. Name any unused MCP server shown by `/mcp`; disable it after the demo, not midway through a cache demonstration.

Time: 3 minutes, within Demo 3.

## Slide 14 — Choose the lane

**On slide**

Reply in chat:

```text
model | mode | thread | output
```

1. Rename a parameter and its comment
2. Diagnose an intermittent failure across three services
3. Apply an approved plan across four files and test
4. You can already see the one-line fix

**Speaker notes**

Run this at 1:30. Post one scenario at a time. Expected direction:

1. Light or Auto; focused edit; new task; diff only.
2. Reasoning; Plan or Agent; new investigation; concise findings.
3. Mid-tier or Auto; Agent; fresh execution session; code and test result.
4. No AI; edit and test.

Time: 12 minutes.

## Slide 15 — Check and control

**On slide**

During work:

```text
/context
/usage
/limits set max-ai-credits 30
```

For the month:

```text
github.com/settings/billing
```

Measure → change one habit → measure again

**Speaker notes**

Session meters help individuals steer. The billing page is what the organisation pays. Encourage delegates to check it once this week.

Time: 3 minutes.

## Slide 16 — The delegate lab

**On slide**

Complete separately online, after this session — about 30 minutes

1. Measure `/instructions`, `/context`, and `/usage`
2. Classify always-on, scoped, and on-demand files
3. Estimate lean versus bloated context
4. Diagnose the two failing tests
5. Write and estimate a constrained prompt
6. Fix the bug and prove all three tests pass
7. Submit one scorecard

Do not load the bloated file into Copilot. Zero tokens is a valid result.

- Lab: [trainer inserts URL]
- Submit: [trainer inserts route]
- Deadline: [trainer inserts date]
- Help: [trainer inserts channel]

**Speaker notes**

Show this at 1:45. Replace the placeholders before delivery. Do not start the lab on the call, and do not reveal the code change or the sample prompt. The workbook gives five timed sections: measure (6 minutes), audit (7), diagnose (8), implement (7), and submit (2). Delegates complete the whole lab independently afterwards. The scorecard is the evidence of completion.

Time: 4 minutes.

## Slide 17 — References

**On slide**

- GitHub Docs: Optimizing your AI usage
- GitHub Docs: Auto model selection
- GitHub Docs: Managing context in Copilot CLI
- GitHub Docs: AI credit session limits
- Community: github-copilot-token-optimization

The community guide is not official GitHub or Microsoft guidance.

**Speaker notes**

Dismiss at 2:00. Links are in the delegate workbook.

Time: final 2 minutes.
