# Live demonstration runbook

Use this beside the slide deck. It contains every trainer action, expected result, and recovery step.

## Before the remote meeting opens

1. Open VS Code at `lab/app`, not the course root.
2. Open these files in editor tabs:
   - `.github/copilot-instructions.md`
   - `AGENTS.md`
   - `.github/instructions/pricing.instructions.md`
   - `src/pricing.js`
3. Close `generated/receipt-bundle.js`. It is the example of context you should not open.
4. Open a terminal in `lab/app`.
5. Confirm the intended failure:

   ```powershell
   node --test
   ```

   Expected: one passing test and two failures. Quantity 2 returns 108; expected 120.

6. If `git status` says this is not a Git repository, run `git init`. Copilot discovers instruction files from the working directory up to the Git root. After `git init`, exit Copilot and start it again from `lab/app`. Confirm with `/instructions`. On this CLI, `/context` counts those files inside **System/Tools**. It does not print a Custom Instructions row.
7. Restore lean instructions:

   ```powershell
   Copy-Item ..\fixtures\copilot-instructions.lean.md .github\copilot-instructions.md -Force
   ```

8. Start Copilot, log in if needed, and verify the commands:

   ```powershell
   copilot
   ```

   ```text
   /limits set max-ai-credits 30
   /context
   /usage
   ```

9. Exit and restart Copilot just before the course so the first demo begins cleanly.
10. Mute desktop notifications and close unrelated tabs.
11. Preview the screen share. Increase terminal and editor zoom until `/context` is readable in the meeting window.
12. Prepare four scenario prompts in the meeting chat, but do not post them yet.
13. Replace the placeholders on Slide 16 with the online lab URL, deadline, submission route, and support channel.

Do not rely on exact token numbers. Model, CLI version, and enabled tools change them. Teach the direction of each comparison.

---

## Demo 1 — Meters and Auto model choice

Time: 20 minutes, beginning at 0:10.

### Goal

Show what occupies the context window, where session totals appear, and why Auto is the default.

### Steps

1. Show that the terminal path ends in `lab\app`.
2. Start Copilot if it is not already running:

   ```powershell
   copilot
   ```

3. Set the soft credit cap:

   ```text
   /limits set max-ai-credits 30
   ```

4. Run:

   ```text
   /context
   ```

5. Point to these lines in this order:
   - Active model and tokens in use, on the first line
   - System/Tools
   - Messages
   - Free space
   - Buffer

6. Say:

   > Your typed prompt is not the whole input. Instructions, tools, history, and files are also in the window. On this CLI, instruction files are inside System/Tools. `/instructions` is the list of which files loaded.

7. Open the model picker:

   ```text
   /model
   ```

8. Point at **Auto**. Leave it selected. Do not switch models.
9. Ask through chat or audio:
   - Rename a parameter in `formatMoney`: light model.
   - Split pricing into several services: reasoning model.
   - Change a known comparison from 2 to 3: mid-tier, Auto, or no AI.
10. Send:

    ```text
    In src/format.js, what does formatMoney return? One sentence.
    ```

11. Point at the model name shown with the response.
12. Say:

    > Auto is the selector. This is the model it selected.

13. Run:

    ```text
    /usage
    ```

14. Point at input and output tokens. If cached input is present, name it but save the explanation for Demo 3.

### Expected result

- `/context` shows a non-zero System/Tools total before a user question.
- `/instructions` lists the AndyCorp instruction files.
- The answer is one sentence.
- `/usage` shows a small output total.

### If something differs

- `/instructions` is empty: exit and restart Copilot from `lab/app`. Confirm `git status` works in that folder.
- No cache row: normal for this first turn or this model.
- Model name not obvious: hover the VS Code response or point to the CLI response footer.

---

## Demo 2 — Constrained output versus an essay

Time: 15 minutes, beginning at 0:30.

### Goal

Show that one standing output rule reduces every routine reply, and that delegates can override it when they need an explanation.

### Steps

1. In VS Code, open `.github/copilot-instructions.md`.
2. Read only the first two lines:

   ```text
   Code only, no explanation.
   Bullets over paragraphs. No explanations unless asked.
   ```

3. Return to Copilot CLI and start a clean comparison:

   ```text
   /new
   /limits set max-ai-credits 30
   ```

4. Send the constrained prompt:

   ```text
   How does calculateOrderTotal work? Four bullet points maximum. No example code. No alternatives.
   ```

5. Run:

   ```text
   /usage
   ```

6. Write the output-token total on the board under **Constrained**.
7. Start another clean session:

   ```text
   /new
   /limits set max-ai-credits 30
   ```

8. Send the verbose prompt:

   ```text
   Explain in detail how calculateOrderTotal works. Include background, a worked example, alternative designs, and a short blog-style summary. Be thorough.
   ```

9. Run:

   ```text
   /usage
   ```

10. Write the output-token total under **Verbose**.
11. Ask:

    > Which answer could you act on for this routine lookup?

12. Make the trade-off explicit:
    - Short by default for routine work.
    - Ask for explanation when learning, debugging, or deciding.

### Expected result

The verbose output total is materially higher. The constrained answer says that `calculateOrderTotal` maps or reduces over lines using `calculateLineTotal`, sums, and rounds.

### If the totals are close

Read the replies. Either the model ignored “be thorough” or it padded the four bullets. The lesson is still to constrain the format and inspect whether the model obeyed.

---

## Remote interaction — poll and chat

Time: 10 minutes, beginning at 0:45.

1. Launch a poll or paste these choices in chat:
   - Rename a parameter: Light / Mid-tier / Reasoning / Auto.
   - Diagnose an intermittent failure across three services: Light / Mid-tier / Reasoning / Auto.
2. Ask in chat:
   - “What output instruction would you add to your own project?”
   - “Which answer in the output demo was sufficient to act on?”
3. Read two answers and explain the trade-off.
4. At 0:55, call a five-minute screen break.

Do not start the lab. It is one independent online activity at the end of the course.

---

## Demo 3 — Context, threads, Ask, Agent, and cache

Time: 30 minutes, beginning at 1:00.

### Part A — Swap the instructions without sending a prompt

Goal: show the cost of always-on context.

1. Ensure Copilot is closed:

   ```text
   /exit
   ```

2. From PowerShell in `lab/app`:

   ```powershell
   Copy-Item ..\fixtures\copilot-instructions.bloated.md .github\copilot-instructions.md -Force
   copilot
   ```

3. Run:

   ```text
   /context
   ```

4. Point at **System/Tools**. Compare it with the number from the four-line file. On the rehearsal machine the line moved from about 7.8k to about 9.8k, a gap of about 2,000 tokens. The display rounds to one decimal place, so it will not match the 2,414-token file estimate exactly. The first-line token total should rise by the same gap. This CLI does not print a Custom Instructions row. System/Tools also includes the built-in prompt and tool definitions, so the comparison is the change, not the absolute number. Then run `/instructions`. That screen lists the loaded files. An empty list means they were not discovered: `lab/app` must be a Git repository, and Copilot must be restarted from that folder.
5. Do **not** send a user prompt.
6. Say:

   > That gap is about 2,000 tokens, and it is sent again on every turn. It will not fill the window. It is still a standing charge before anyone types. The same file also tells the model to write a long essay, and that output can cost more than these 2,000 input tokens.

7. Exit:

   ```text
   /exit
   ```

8. Restore:

   ```powershell
   Copy-Item ..\fixtures\copilot-instructions.lean.md .github\copilot-instructions.md -Force
   copilot
   ```

9. Optional, if time permits:

   ```text
   /context
   ```

   Show that System/Tools, and the first-line total, have fallen again.

10. Open the following files in VS Code and point, without reading every line:
    - `AGENTS.md`: landmines only.
    - `.github/instructions/pricing.instructions.md`: `applyTo` makes the pricing rule conditional.
    - `.copilot/skills/plan-then-execute/SKILL.md`: occasional workflow, loaded on demand.

### Part B — Conversation history and `/new`

Goal: show that history grows and follows each new turn.

1. Start clean:

   ```text
   /new
   ```

2. Send:

   ```text
   In src/pricing.js, what is the numeric value of TAX_RATE? Number only.
   ```

3. Run:

   ```text
   /context
   ```

4. Point at Messages.
5. Send:

   ```text
   List the exported function names in src/pricing.js. Names only.
   ```

6. Run `/context` and point at the larger Messages figure.
7. Start a new conversation with only the second task:

   ```text
   /new
   List the exported function names in src/pricing.js. Names only.
   ```

8. Run `/context`. Messages should be smaller.
9. Say:

   > New problem, new chat. Same problem but a long useful thread, compact it.

10. Name `/compact focus on pricing rules`; do not wait for a visible reduction on this short thread.

### Part C — `/ask` versus a normal turn

Goal: show a side question that does not join history.

1. Record Messages:

   ```text
   /context
   ```

2. Ask:

   ```text
   /ask In src/pricing.js, what is TAX_RATE? Number only.
   ```

3. Run `/context` again. Messages should be unchanged.
4. Send the same text normally:

   ```text
   In src/pricing.js, what is TAX_RATE? Number only.
   ```

5. Run `/context`. Messages should increase.
6. The constant in `src/pricing.js` is `0.2`. If `/ask` says `0.08`, it did not read that constant. The current bug discounts quantity 2, so a £100 line becomes £108, and `(108 - 100) / 100` is `0.08`. Say that, then show the normal prompt quoting `0.2`.

### Part D — Ask versus Agent in VS Code

1. Open Copilot Chat with `lab/app` as the workspace.
2. Select **Ask** mode.
3. Point at the composer and say: “A fact or explanation.”
4. Select **Agent** mode.
5. Point at the composer and say: “Find files, edit, test, and iterate.”
6. Do not send an Agent prompt.
7. Switch back to Ask.

### Part E — Tools and cache

1. In CLI, run `/mcp`.
2. If no server is enabled, say: “Good. No unused MCP tax.”
3. If a server is listed, name one this task does not need. Do not disable it mid-demo, because changing tools changes the cacheable prefix.
4. Run `/usage`.
5. If cached input is shown, point to it. If not, say “not shown on this model/session.”
6. Close with:

   > Pick model, reasoning, tools, and instructions before the first message. Append turns. Changing that prefix forces the model to process it again.

### Time-saving cut order

If this demo is running long:

1. Drop the live Agent dropdown; explain it from Slide 13.
2. Drop the final `/usage` cache check; narrate the rule.
3. Keep the instruction swap and `/new`. They are the clearest demonstrations.

---

## Remote scenario discussion

Time: 15 minutes, beginning at 1:30.

Paste one scenario at a time. Ask delegates to answer in chat with:

```text
model | Ask/Agent/no AI | new or existing thread | output constraint
```

Scenarios:

1. Rename a parameter and update its comment.
2. Diagnose an intermittent failure across three services.
3. Apply an approved implementation plan across four files and run tests.
4. You can already see the one-line comparison that is wrong.

Expected direction:

- Light or Auto | focused edit | new task thread | diff only.
- Reasoning | Plan or Agent | new investigation | concise findings.
- Mid-tier or Auto | Agent | fresh execution thread | code and test result.
- No AI | edit and test.

## Online lab handoff

Time: 15 minutes, beginning at 1:45.

1. Show Slide 16. Do not start the lab.
2. Replace its placeholders before delivery:
   - Lab URL or package location
   - Submission route
   - Deadline
   - Support channel
3. Explain the one lab, about 30 minutes, completed after the session:
   - Measure `/instructions`, `/context`, and `/usage` after one constrained lookup.
   - Classify always-on, path-scoped, and on-demand context.
   - Compare lean and bloated fixtures without loading the handbook into Copilot.
   - Diagnose the failing tests and write a constrained prompt.
   - Type the fix or use the prompt, then prove all three tests pass.
   - Submit one scorecard.
4. State that the lab is independent and no live trainer is assumed. Setup and troubleshooting are in the workbook.
5. Use chat for the live close:
   - What will you remove from an instruction file?
   - When will you start a new chat?
   - Which task will you do without Copilot?
6. Close with:

   ```text
   /context
   /usage
   /limits set max-ai-credits 30
   ```

   and [github.com/settings/billing](https://github.com/settings/billing).

Dismiss at 2:00.

## Online lab support and marking

These steps are for the trainer or course administrator after the live session.

### Scorecard checks

- The scorecard includes `/context` values and the model selected by Auto.
- The four context files are classified as always on, always on, path-scoped, and on demand.
- The handbook estimate is much larger than the lean Copilot instructions.
- The HTML estimate is much larger than the Markdown notes.
- Exact numbers are not graded; versions and local tools vary.

### Prompt checks

A strong prompt:

- Names `src/pricing.js`
- Points to the failing test or quantity-3 rule
- Restricts changes to that file
- Forbids test edits and extra files
- Constrains the reply

The final scorecard should report 3 passing and 0 failing tests, with only `src/pricing.js` changed. Typing the known one-line fix is a valid and preferred decision. If Copilot edits tests, adds files, or introduces a framework, the delegate should revert and stop.
