# Trainer guide

**Course:** Spend less, ship the same — minimizing GitHub Copilot tokens

**Audience:** Developers who already have GitHub Copilot and write code with Chat or Agent.

**Length:** 2 hours. Stop at 2:00.

**Shape:** A 2-hour live remote demonstration and discussion. Delegates complete one 30-minute online lab at the end.

**Group size:** Up to about 20.

**You will need:** this guide, `trainer/answer-key.md`, screen sharing, a terminal already logged into Copilot in `lab/app`, and the meeting chat or polling feature.

Delegates use `delegate/workbook.md` and `delegate/scorecard.md` after the live session. They do not need to install or run anything during the remote delivery.

## What they leave with

- A clear view of `/context`, `/usage`, model selection, conversation management, and modes
- A habit for each lever below, taken from what they saw you do
- One online lab that produces their own token reading and a rewritten prompt

## The six levers

State these in the opening, then show them. The session meters and Auto, `/new`, `/compact`, and caching follow GitHub's [Optimizing your AI usage](https://docs.github.com/en/copilot/tutorials/optimize-ai-usage). The file layout in `lab/app` follows the community guide [github-copilot-token-optimization](https://github.com/olivomarco/github-copilot-token-optimization), which is field experience, not GitHub documentation. That guide ranks output control first, because published model prices charge more per output token than per input token.

1. **Constrain output first.** "Code only, no explanation" and "Bullets over paragraphs" belong in `.github/copilot-instructions.md`. Say "explain" when the essay is the point.
2. **Keep always-on context to landmines.** `AGENTS.md` and `copilot-instructions.md` are both paid on every turn, so they must not repeat each other. Path-specific rules use `applyTo`. Occasional workflows stay in an on-demand skill. Convert HTML, PDF, and DOCX to Markdown. Leave generated files closed.
3. **Manage conversation length.** `/new` or New Chat when the task changes. `/compact` when the same task must continue.
4. **Preserve prompt caching.** Keep model, reasoning effort, tools, skills, and instructions fixed during the session. Append turns. Cached input is typically billed at about 10% of the normal input price on supported models. A model switch, a tool change, or a stale session (about 1 hour for most models, about 24 hours for OpenAI models) processes the prefix again at full input price.
5. **Reduce tool and agent overhead.** Ask mode and `/ask` are for questions. Agent mode is for multi-step work. Unused MCP servers still occupy the window.
6. **Match the model to the task, and leave Auto on.** Reasoning models for architecture and hard debugging. Mid-tier when the plan is known. Light models for mechanical edits. A one-line known fix does not need a model at all.

Show this picture once:

```text
your prompt
  → Auto (or the model you pinned)
  → context assembled: instructions + tools + MCP + history + @files
  → cached prefix reused when that block is unchanged
  → model writes the reply (output tokens)
```

Anything on the left is paid on the way in. Anything invited on the right is paid on the way out.

## Credit budget

You send all prompts during the live session, including the one verbose reply. Delegates consume no Copilot credits during the remote delivery. In the lab at the end, each delegate sends one short lookup. Sending the pricing prompt is optional.

- Never send a prompt while the bloated instructions file is loaded. That is the expensive mistake.
- Set `/limits set max-ai-credits 30` on your demo session. This CLI rejects a lower number; 30 is the minimum it accepts. The day before, run `copilot help limits` and teach the sentence your CLI prints. The cap is a soft pause.
- Do not install third-party usage extensions. `/context` and `/usage` are the live meters. The VS Code status bar is the monthly quota. [github.com/settings/billing](https://github.com/settings/billing) is the bill.

## Remote setup the day before

Rehearse the demonstrations at the same resolution and zoom level you will screen-share. You should be able to reach a readable `/context` result in under a minute.

- [ ] `node --version` is 22 or later
- [ ] `copilot --version` works in a new terminal, and `copilot login` completes
- [ ] From `lab/app`, `node --test` fails 2 tests and passes 1 (quantity 2 returns 108, expected 120)
- [ ] `node ..\tools\estimate-tokens.mjs` matches the answer key, give or take a few tokens
- [ ] `lab/app` is a Git repository (`git init` if `git status` says it is not). Copilot CLI discovers `.github/copilot-instructions.md` from the working directory up to the Git root. Restart Copilot from that folder afterwards. `/instructions` lists the files. `/context` counts them inside System/Tools.
- [ ] `/context`, `/usage`, `/new`, `/compact`, `/ask`, `/limits`, and `/mcp` exist. Note any rename.
- [ ] You know whether the org locks models to Auto. If it does, skip any model switch and say so.
- [ ] Terminal and editor fonts remain readable in the meeting preview
- [ ] Notifications are muted and unrelated editor tabs are closed
- [ ] The meeting chat and polls work
- [ ] The online lab link or course package is ready to post at the end
- [ ] A fresh learner account can launch the hosted lab, see `lab/app`, and run `node --version` and `copilot --version`
- [ ] Hosted workspaces preserve the scorecard long enough to submit it, or delegates know to download it
- [ ] The lab completion route is clear: where delegates submit a scorecard, screenshot, or completion confirmation

### If your shared demo CLI fails on the day

Say that per-prompt totals will not be visible. Run the estimator over screen share for the file comparisons. Use VS Code Ask mode for one short question and show the status-bar quota. Do not invent token numbers.

### Restore the app after class

From `lab/app`:

```powershell
Copy-Item ..\fixtures\copilot-instructions.lean.md .github\copilot-instructions.md -Force
```

The bug must still be `quantity >= 2` at the start of the next class. Restore `src/pricing.js` if you or a delegate fixed it.

## Live remote run of show

| Clock | Who | Block |
| --- | --- | --- |
| 0:00 | You, discussion | Welcome, the picture, the six levers (10 min) |
| 0:10 | You, demo | Meters and model choice (20 min) |
| 0:30 | You, demo | Output control: short reply versus essay (15 min) |
| 0:45 | Remote discussion | Chat poll and scenario questions (10 min) |
| 0:55 | Everyone | Screen break (5 min) |
| 1:00 | You, demo | Instructions, threads, Ask versus Agent, and cache (30 min) |
| 1:30 | Remote discussion | Apply the six levers to work scenarios (15 min) |
| 1:45 | You | Recap and online lab handoff. End at 2:00 (15 min) |

Delegates watch, answer through chat or polls, and ask questions. They do not open Copilot or start the lab during the live session.

### 0:00 — Welcome (10 min)

- "You will watch the meters, then read your own once. Minimizing is a set of habits."
- Draw the picture. Name the six levers in one sentence each. Do not open a feature tour.
- "Output is the expensive side of a reply. The standing default in this project is code only. You say explain when you want the essay."
- Tell delegates that the workbook and scorecard belong to the one lab at the end.

### 0:10 — Demo: meters and model (20 min)

Start in `lab/app` with Copilot already open.

```text
/limits set max-ai-credits 30
/context
```

Read the first line, then System/Tools, Messages, Free space, and Buffer. Instruction files are inside System/Tools. `/instructions` lists which files loaded. Tell them the lab at the end is where they will copy those numbers from their own machine.

```text
/model
```

Leave **Auto** selected. Auto routes the task, keeps reasoning models for harder work, and on paid plans is discounted. It also avoids switching model mid-session, which protects the cache.

Ask the group through chat or audio, and take two responses. Answers are in the answer key.

- "Rename a parameter in `formatMoney`. Which weight of model?"
- "Decide whether to split pricing into several services. Which weight?"

Then send:

```text
In src/format.js, what does formatMoney return? One sentence.
```

Point at the model name on the reply. Auto is the selector. The reply shows the selection. Run `/usage` and leave that output-token number on screen.

If the org allows it and you still have a minute, say you will not switch model now, because that drops the cache. Skip a live reasoning-model comparison unless you are ahead. One sentence covers it: "The same lookup on a reasoning model spends thought tokens on a question the file already answers."

### 0:30 — Demo: output control (15 min)

Open `.github/copilot-instructions.md` in the editor. It is four lines and starts with `Code only, no explanation.`

New session, constrained:

```text
/new
/limits set max-ai-credits 30
How does calculateOrderTotal work? Four bullet points maximum. No example code. No alternatives.
```

`/usage`. Note output tokens.

New session, verbose. This is the one long generation in the course. You send it. They do not.

```text
/new
/limits set max-ai-credits 30
Explain in detail how calculateOrderTotal works. Include background, a worked example, alternative designs, and a short blog-style summary. Be thorough.
```

`/usage`. The verbose output total should be clearly higher. If it is close, read the text and say the model ignored the instruction. A useful short answer says that `calculateOrderTotal` adds `calculateLineTotal` for each line and rounds.

Discussion, one minute: "What single line would you add to your real instructions file on Monday?"

### 0:45 — Remote discussion and poll (10 min)

Ask delegates to answer in chat or with a poll:

1. Rename a parameter: Light / Mid-tier / Reasoning / Auto.
2. Diagnose an intermittent failure across three services: Light / Mid-tier / Reasoning / Auto.
3. What would you add to your real instructions file to constrain routine output?
4. Which answer from the output demo was sufficient to act on?

Read two chat responses. Correct the reasoning, not the person. At 0:55, call a five-minute screen break.

### 1:00 — Demo: context, threads, and tools (30 min)

Talk as you go. Aim for this order. Cut from the bottom if you are slow.

**Instructions swap (6 min).** Quit Copilot. From `lab/app`:

```powershell
Copy-Item ..\fixtures\copilot-instructions.bloated.md .github\copilot-instructions.md -Force
copilot
```

`/context`. System/Tools should rise. This CLI has no Custom Instructions row. Send nothing. Then:

```text
/exit
```

```powershell
Copy-Item ..\fixtures\copilot-instructions.lean.md .github\copilot-instructions.md -Force
```

Say what is worth keeping: the discount rule already lives in the scoped pricing file, and "add no dependencies" already lives in `AGENTS.md`. The handbook's "always write a long explanation" is the output-control failure. Also say the HTML release notes are about 19× the Markdown of the same four sentences, and the typed prompt pair is only about 1.7×. Delegates will measure those in the lab at the end. Precision prevents a second turn. It does not dwarf the handbook.

**New chat (5 min).** Start Copilot. Send two one-liners in one session. After each, `/context`, and point at Messages.

```text
In src/pricing.js, what is the numeric value of TAX_RATE? Number only.
```

```text
List the exported function names in src/pricing.js. Names only.
```

Then:

```text
/new
List the exported function names in src/pricing.js. Names only.
```

Messages should be smaller. `/usage` input on the longer session is higher because each turn reprocessed the history. Mention `/compact` for a long thread that is still the same task. You do not need to run it if the thread is short.

**Ask versus Agent (5 min).**

```text
/context
/ask In src/pricing.js, what is TAX_RATE? Number only.
/context
```

Messages should be unchanged across `/ask`. The constant is `0.2`. If `/ask` says `0.08`, it used the bug: a £100 line becomes £108. Send the same sentence as a normal prompt, show Messages increase, and show that reply quote `0.2`.

In VS Code, with `lab/app` open, set Chat to **Ask**, then switch the dropdown to **Agent** and do not send. Agent is for multi-step edits. Ask and `/ask` are for a fact.

Run `/mcp`. If no server is enabled, that is a good result. If a server is listed that this demo does not need, name it and leave the disable for after class, so you do not bust the cache mid-demo.

**Cache (4 min).** If `/usage` showed a cache figure on the second turn, read it. If not, say "not shown" is normal. The habit is the lesson: pick Auto or one model before the first message, leave tools and instructions alone, append turns, attach a stable file once with `@file`. Switching model or coming back after the cache expires rebuilds the prefix at full input price.

### 1:30 — Scenario discussion (15 min)

Post each scenario in chat. Ask for a one-line response naming the model, mode, thread choice, and output constraint.

1. “Rename a parameter and update its comment.”
2. “Find the cause of an intermittent failure across three services.”
3. “The implementation plan is approved; apply it across four files and run tests.”
4. “You can see that `>= 2` must become `>= 3`.”

Use the answer key to lead the discussion:

- Scenario 1: light model or Auto, focused edit, terse output.
- Scenario 2: reasoning model, Plan or Agent, relevant tools only.
- Scenario 3: new execution session, mid-tier or Auto, Agent, stable tool set.
- Scenario 4: type it; zero tokens.

### 1:45 — Recap and online lab handoff (15 min)

Recap the six habits. Then hand off the one 30-minute online lab completed after the session. Delegates measure a real session, classify always-on and scoped context, compare lean and bloated files, diagnose the failing tests, write and estimate a constrained prompt, implement the fix, and prove all three tests pass.

Explain where the lab package lives, how to submit the scorecard, and the deadline. The lab can be completed independently; no live trainer is assumed.

Close with the billing page: session meters steer the work. The AI usage page is what the organisation pays. Dismiss at 2:00.

## If you are behind

The class still ends at 2:00.

| At this clock | Do this |
| --- | --- |
| 0:45 and a demo is unfinished | Begin the poll anyway. Summarize the missing comparison verbally. |
| 1:00 | Restart on time after the break. |
| 1:20 | Drop the cache narration and live Agent dropdown before you drop the instructions swap or `/new`. |
| 1:45 | Stop scenario discussion and hand off the lab. |

## After class

- Restore the lean instructions file and `src/pricing.js` (`quantity >= 2`) if you will teach this again.
- Delete stray `my-prompt.md` and `bad-prompt.md` files.
- Send the online lab link, deadline, submission route, and take-home card.
- If you run `/chronicle cost-tips` later and it names a repeated waste, add one line to the team's real instructions file.

## Troubleshooting

| What you see | What to do |
| --- | --- |
| `copilot` not found | New terminal. `winget install GitHub.Copilot`, or `npm install -g @github/copilot` with Node.js 22+. |
| Browser login loop | `copilot login`. Confirm the account has a Copilot license. |
| `/instructions` lists nothing | `lab/app` is not a Git repository, or Copilot was not restarted from that folder. Run `git init` there, exit, and start `copilot` again from `lab/app`. |
| System/Tools did not change after the swap | You edited the file and did not restart `copilot`. Compare System/Tools before and after. This CLI folds instruction files into that line. |
| Model picker has no reasoning model | Stay on Auto. Use the verbal comparison in the answer key. |
| `/usage` has no cache row | Allowed. Teach the invalidation rules anyway. |
| A delegate left the bloated file in place | Restore from `copilot-instructions.lean.md` before the next demo. |
| Tests all pass before the lab | Someone fixed the bug early. They can still rewrite the prompt. Restore `quantity >= 2` for the next class. |
| Cap triggers on a one-sentence answer | `/new`, stay on Auto, raise the cap by a small step. |
| Someone pastes a secret | Stop. Have them revoke it. The lab data is fictional. |

## Materials map

| Path | Who uses it |
| --- | --- |
| `delegate/workbook.md` | Delegates complete it independently online after the session. |
| `delegate/scorecard.md` | The online lab record or submission artifact. |
| `trainer/guide.md` | You, during the remote delivery. |
| `trainer/slide-content.md` | Slide text and speaker notes. |
| `trainer/demo-runbook.md` | Exact commands, expected results, and recovery steps. |
| `trainer/answer-key.md` | You. Keep it off delegate desktops. |
| `lab/app` | Your demo folder and their lab folder. |
| `lab/fixtures` | Bloated handbook, wiki-style AGENTS file, HTML and Markdown notes. |
| `lab/tools` | Estimator and bundle writer. No npm install. |
