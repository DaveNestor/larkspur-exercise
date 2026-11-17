# Client review: Larkspur disruption-care agent

**To:** Room 12  
**From:** Larkspur client review agent, on behalf of Priya Raghavan  
**Re:** the disruption-care agent you walked us through in our last session  
**Generated:** 2026-11-17 10:28

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

**1. agent.py adds one local tool, fare_rules, reading disruption_policy.json and fare_rules_excerpt.md directly off disk.**

The diff wires LOCAL_TOOLS to {"fare_rules": fare_rules} and the function opens data/americas/disruption_policy.json and data/americas/fare_rules_excerpt.md on every call, slicing the handbook text between "### 5." and "### 7.". EXTRA_TOOLS now holds exactly 1 schema, matching the static scan's EXTRA_TOOLS declared: 1. The last committed trace never calls it: tools called, in order: lookup_booking, get_flight_status, check_policy.

Run python3 run.py K7PQ2M --trace with a question that references a policy_row_id and confirm fare_rules appears in the tool call list.

**2. search_alternatives description grew from the placeholder string "search" to 521 characters in this pod's diff.**

The template shipped with "description": "search" at 6 characters; the diff replaces it with a 521-character description covering when to call it, what it returns, and that option_id should be passed on to hold_seat and check_policy as chosen_option_id. No other tool description in build_tools() was touched by this diff. This is a prompt-engineering change, not a model change, and nothing in the repository shows whether it altered tool-selection accuracy.

Run python3 bench.py --compare <before> <after> against a bench-before.json captured on the unedited template to see if the rewritten description changed call accuracy.

**3. TONE_ADDENDUM is 0 characters and PITCH.md is still the unfilled template.**

The static scan confirms TONE_ADDENDUM: still empty (0) characters, and the diff never touches it. PITCH.md is reported byte-identical to the shipped template, with nothing filled in.

Check git log for commits touching TONE_ADDENDUM or PITCH.md before claiming an intelligence or pitch goal is set.

**4. run_agent's fix in the diff changes which message gets returned, not what the model does.**

The template captured answer = text_of(response) one turn behind and returned that stale answer; the diff removes the answer variable entirely and returns text_of(response) only after the loop exits, with messages.append now passing response.content instead of the flattened text_of(response). This is a bookkeeping correction inside the existing while loop, which still tests stop_reason == tool_use and stops at turns < MAX_TOOL_CALLS, unmodified at 8. No larger or smaller model changes what this loop returns; it only fixes which turn's text comes back.

Run python3 verify.py 1.2 to confirm the fixed return value passes the gate the fix targets.

**5. The one banked wire run shows 0 prompt-cache reads against 10700 tokens written, on a single turn with 3 tool calls.**

readout-trace.json records tokens: 2773 in, 666 out, cache read 0, cache written 10700, hit ratio 0.0, across API turns: 4 and wall clock: 9.5s. The static scan shows cache_control anywhere: no, so every one of those 4 turns re-sends the full system prompt and tool list from scratch. That is a cost and latency number nothing about model choice touches; it is a wiring gap in this file.

Run python3 run.py --tool-tax to see the per-turn token cost this lack of caching is adding, independent of which model answers.

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

- `agent.py (274 lines)`
- `PITCH.md (unchanged template)`
- `TEAM.md`
- `readout-trace.json`
- `readout.html (evidence block)`

Reviewer: `claude-sonnet-latest`. Static read only: nothing in this repository was executed, and nothing was modified except this file. Larkspur Airlines is a fictional training scenario. Confidential, do not distribute.
