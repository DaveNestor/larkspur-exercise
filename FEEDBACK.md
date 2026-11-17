# Client review: Larkspur disruption-care agent

**To:** Room 12  
**From:** Larkspur client review agent, on behalf of Priya Raghavan  
**Re:** the disruption-care agent you walked us through in our last session  
**Generated:** 2026-11-17 09:10

## Priya's note

> Our vendor says we should just be using your best model.
>
> Why aren't we?
>
> Priya Raghavan, Larkspur Airlines

She sent that before this session opened. She means it. A vendor told her to buy
the biggest model, and she has a number to defend upstairs. Her four questions from
day one are still open. Naming a model answers none of them.

## Still open from day one

| Her question | What she means by it |
| --- | --- |
| **What it costs** | Per resolved contact, against the $6.90 a human contact costs us. |
| **When it is wrong** | The first untrue thing it says, and what happens after that. |
| **Who runs it** | In June, after you have left. |
| **What you left out** | The scope you cut, and why. |

## What the review agent found

Larkspur pointed a review agent at your repository. It read the
code. It did not run your agent, and the only file it changed is this one. Each
item below names the file and the line it is about.

**1. agent.py search_alternatives: your diff replaced the 6-character description "search" with 521 characters and a pnr-only schema.**

The new description tells the model to call it after lookup_booking and get_flight_status show a cancelled or delayed flight, and to pass the chosen option_id to hold_seat and check_policy. The committed readout-trace.json shows 3 tool calls: lookup_booking, get_flight_status, check_policy. search_alternatives is not among them.

Run python3 run.py K7PQ2M --trace and paste the tool-call sequence to show whether search_alternatives is now called.

**2. agent.py run_agent loop: your diff appends response.content to messages and returns text_of(response) after the loop.**

The shipped version appended text_of(response) as the assistant turn and returned the text from the previous iteration. Your version keeps the tool_use blocks in the transcript, which the API requires before tool_result blocks. The committed run shows 4 API turns for 3 tool calls, which matches a loop that continues.

Run python3 run.py --all --trace and paste the totals footer so the turn count is on record for every booking shape.

**3. agent.py MAX_TOOL_CALLS is 8 and the loop test is turns < MAX_TOOL_CALLS, so a stalled run returns the last text_of(response) or an empty string.**

When the cap hits while stop_reason is still tool_use, the function returns text_of(response), which is "" if the final turn held only tool_use blocks. Nothing in your diff routes that case to escalate_to_human. No run in the material reached 8 turns, so what a capped customer sees is unobserved.

Run python3 run.py --all --trace and paste the turn count for the longest booking, plus any run that ended on the cap.

**4. readout-trace.json: prompt caching shows 0 read, 10700 written, hit ratio 0.0, and agent.py contains no cache_control.**

Your committed run wrote 10700 tokens to cache and read none of them. The same run billed 2773 input and 666 output tokens over 4 turns. A bigger model changes the price of those 10700 written tokens but not the 0.0 ratio, and nobody has measured what a cache hit would save.

Run python3 bench.py --label before --stage 1 --runs 3, change one thing, run it again as after, and paste the python3 bench.py --compare before after output.

**5. The repository has no evals/cases.json and PITCH.md is blank, so the only evidence is one trace and gates 1.2, 1.3, 1.4 banked by Lynda Hughes.**

The static scan shows TONE_ADDENDUM at 0 characters, EXTRA_TOOLS at 0, and no LOCAL_TOOLS executors. hold_seat (73 characters) and send_confirmation (71 characters) still have one-line descriptions. A single 9.5s run over one booking cannot show which model Priya's question should name, and nothing here says what to optimise.

Run python3 eval_harness.py and paste the pass count, then name in PITCH.md the one lever you are pulling and the number you will move.

## Your four answers

The four lines under `## Priya asked` in your PITCH.md are still empty. They
are one line each and they are not a coding job: cost, what happens when it is
wrong, who runs it in June, and what you left out. Whoever on your side is not
editing agent.py is the right person to write them, and they are the four
things I will ask about first.

## Before our next meeting

> Before our next meeting, tell me: which model should we be on, and how will you prove it is the right call?
>
> Priya Raghavan, Larkspur Airlines

Bring two things. A recommendation, and the measurement behind it. If the model is
not the problem, say so, and bring the number that shows it.

## What this review read

- `agent.py (232 lines)`
- `PITCH.md`
- `TEAM.md`
- `readout-trace.json`
- `readout.html (evidence block)`
- given files that differ from the shipped pack: `verify.py`, `eval_harness.py`, `bench.py`, `readout.py`, `setup.py`, `pod_sync.py`, `run.py`, `review.py`, `support/__init__.py`, `support/client.py`, `support/data.py`, `support/models.py`, `support/tools.py`, `support/trace.py`, `support/offline.py`, `support/mock_backend.py`, `support/mcp_client.py`, `support/mcp_server.py`, `support/mcp_selftest.py`, `support/access.py`, `support/keyscan.py`, `support/review.py`

Reviewer: `claude-sonnet-5-5`. Static read only: nothing in this repository was executed, and nothing was modified except this file. Larkspur Airlines is a fictional training scenario. Confidential, do not distribute.
