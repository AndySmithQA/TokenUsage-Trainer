# Answer key

For trainers. You lead the model discussion over screen share. Delegates write their own lab prompt before they see the sample below.

Readings from `/context` and `/usage` will differ by model, CLI version, and which MCP servers are installed. Grade the **direction** of the change, not a magic number. The estimates below are from `lab/tools/estimate-tokens.mjs` on the shipped files (about 4 characters per token).

## Discussion — model choices

Use these while you demo. Delegates do not complete this table themselves.

| # | Task | Choice | Why |
| --- | --- | --- | --- |
| 1 | Rename a parameter in `formatMoney` | Light | Mechanical, local, no design |
| 2 | Split pricing into several services? | Reasoning | Architecture. The costly part is the decision, not the typing |
| 3 | Ticket already says: threshold 2 → 3 | Mid, or Auto | The plan is known. This is execution |
| 4 | Reformat `pricing.js` | Light | Routine |
| 5 | Intermittent bug, three services, no repro | Reasoning | Needs analysis before any edit |
| 6 | Routine edit, unsure which model | Auto | Router picks a cheaper capable model, protects the cache, and is discounted on paid plans |
| 7 | Update one comment | Light | Documentation |
| 8 | One-line change the test already describes | Mid, or Auto | Execution. A reasoning model will spend thought tokens on a comparison you have already specified |

Accept Mid or Auto on rows 3 and 8. Push back on Reasoning for rows 1, 4, 7, and 8.

Your demo reply, and each scorecard, should name the model printed on the response, not only the word Auto. Auto is the selector. The reply shows the selection. There is no single correct model id.

## Lab estimates

Delegates later compare the always-on file with the handbook, `AGENTS.md` with the wiki file, and the Markdown notes with the HTML in the lab at the end. During the live session, you demonstrate the instructions swap without sending a prompt. The other rows are there if someone asks.

Character/4 estimate on the shipped files. `/context` will not match these integers. Direction is the pass mark.

| File | Chars | Words | Est. tokens | Role |
| --- | --- | --- | --- | --- |
| `.github/copilot-instructions.md` | 160 | 22 | 40 | Always-on. Output control only. |
| `copilot-instructions.bloated.md` | 9656 | 1577 | 2414 | About **60×** the always-on file |
| `AGENTS.md` | 112 | 20 | 28 | Always-on landmines. Not a copy of the Copilot file. |
| `AGENTS.wiki.md` | 872 | 139 | 218 | About **8×** `AGENTS.md`. Discoverable facts. |
| `pricing.instructions.md` | 326 | 47 | 82 | Scoped with `applyTo`. Not always-on. |
| `receipt.instructions.md` | 190 | 21 | 48 | Scoped with `applyTo`. |
| `src/pricing.js` | 1102 | 155 | 276 | Source |
| `generated/receipt-bundle.js` | 15240 | 412 | 3810 | About **14×** `pricing.js`. Leave it closed. |
| `release-notes.md` | 211 | 38 | 53 | The words |
| `release-notes.html` | 3986 | 387 | 997 | About **19×**. Same four sentences. |
| `prompt-precise.txt` | 91 | 11 | 23 | Names the function and the file |
| `prompt-vague.txt` | 149 | 28 | 38 | About **1.7×**. Small next to the handbook. |
| `SKILL.md` (plan-then-execute) | 522 | 86 | 131 | On demand. Must not be pasted into the always-on file. |

`/context` **System/Tools** should rise when the bloated file is loaded, then fall when the four-line file is restored. On the rehearsal machine it moved from about 7.8k to about 9.8k. That is about 2,000 input tokens on every later turn, not a full window. This CLI has no separate Custom Instructions row. The restored file starts with `Code only, no explanation.`

If they ask what to keep from the handbook: the discount rule (already in the scoped pricing file) and "do not add dependencies" (already in `AGENTS.md`). The greeting-and-essay policy is the output-control failure.

The precise prompt is only a little smaller than the vague one. Say so. Precision earns its keep by preventing a second turn, not by shaving a dozen tokens off the typing.

## Lab context audit

- `.github/copilot-instructions.md`: always on.
- `AGENTS.md`: always on for agents that read it.
- `.github/instructions/pricing.instructions.md`: path-scoped with `applyTo`.
- `.copilot/skills/plan-then-execute/SKILL.md`: on demand.

The bloated handbook is the largest repeated-cost instruction file. Markdown carries the same release-note content with far less markup than HTML. The pricing rule is scoped because unrelated formatting or receipt tasks do not need it.

## Your thread demo — what the meters should do

On your machine, after two prompts in one session, **Messages** is higher than after `/new` and a single prompt. `/usage` **input** on the longer session is higher, because each turn reprocessed the history. `/compact` lowers Messages on a long thread. On this short demo the drop can be small, so name the command and move on.

## Your cache narration

You do not need a delegate score for this. On your machine:

If cache is shown, turn 2's cached-input number is the one you want non-zero. A mid-session model switch in your shared demo should process the next turn without that cache.

There is no single correct cached-token integer.

## Your Ask demo

- The constant is `0.2`. A normal prompt should quote it. If `/ask` says `0.08`, it combined the quantity-2 bug with the tax: a £100 line becomes £108.
- Messages in `/context` is unchanged across `/ask`.
- The same text sent as a normal prompt increases Messages.
- `/mcp` shows no enabled server, or you name a server you would disable after class.
- In VS Code, point at Ask and at Agent. Do not send the Agent prompt.

`TAX_RATE` is `0.2` in `src/pricing.js`.

## Your output demo

There is no required integer. The verbose prompt's **output tokens** should be clearly higher than the constrained prompt's. If they are close, the model ignored "be thorough" or it padded the four bullets. Read the text. Use the pair as a discussion, not a failed lab.

A useful constrained answer covers: `calculateOrderTotal` adds up `calculateLineTotal` for each line, then rounds. It does not need a blog post.

## The lab prompt and the bug

`src/pricing.js` discounts when `quantity >= 2`. The rule and the tests say the 10% discount starts at quantity **3**, on the pre-tax amount, then 20% tax.

Observed failures from `node --test`:

| Test | Actual | Expected |
| --- | --- | --- |
| does not discount a line with quantity 2 | 108 | 120 |
| discounts a line with quantity 3 before tax | 162 | 162 (already passes) |
| sums mixed lines | 140.4 | 152.4 |

Why 108: `2 * 50 = 100`, times `0.9`, times `1.2` = 108. Without the discount, `100 * 1.2` = 120.

The edit:

```javascript
const discount = quantity >= 3 ? 0.1 : 0;
```

After that change, `node --test` should report 3 passing. No other file needs to change.

### A prompt that is strong enough to send

Delegates should write their own. This is a reference, not a script to project before they try.

```text
Make src/pricing.js match the instructions and the failing tests.
10% discount only when quantity is 3 or more, on the pre-tax amount, then TAX_RATE.
Change that file only. Do not edit the tests. Reply with the diff only.
```

That prompt is on the order of 60 estimated tokens. The bad prompt in the workbook is several times longer and, more importantly, asks for a redesign, a database, and a long explanation. Those requests dominate the cost. The estimator shows length. The open-ended instructions show wasted agent steps. Mention both.

### Zero-token path

Changing `>= 2` to `>= 3` by hand, then `node --test`, is a correct capstone. Praise it.

### If Copilot wanders

Reject edits outside `src/pricing.js`. If it "fixes" the bug by changing the test to expect 108, revert that and point it at the instructions. If the session grows past a second attempt, stop and apply the one-line patch. Further turns are the habit the course is trying to prevent.

## Chronicle

`/chronicle cost-tips` and `/chronicle tips` are reflective. There is no answer key. A useful tip names a repeated pattern from today's sessions (long thread, model switch, verbose reply). An empty result means the CLI has little history. Move on.
