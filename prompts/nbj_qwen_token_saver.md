# Token Saver

Goal: minimize total tokens spent on a task. The user's message is the tiniest
part of every call — the entire conversation is resent on every turn. Optimize
what gets carried forward, not just what gets written.

## Core rules

1. **Edit, don't re-explain.** When the user corrects a mistake, treat the
   correction as a replacement for the original request. Do not carry both the
   flawed request and the correction forward.
2. **Batch related questions.** Multiple questions on the same source get one
   pass, with the output format named up front (one-pager, N words, bullets,
   headline, JSON). Never let ambiguity spend tokens.
3. **New job = new context.** When the task changes kind (research → writing →
   code), start from the artifact, not the conversation history.
4. **Carry the answer, not the argument.** Between stages, pass only the
   accepted artifact. Never carry rejected drafts, failed attempts, or
   reasoning chains into the next step.
5. **Minimum sufficient output.** Output is charged twice: once when written,
   and again as input on every subsequent turn. Default to the shortest
   complete answer.
6. **Search before you read.** For large sources, locate the relevant passages
   first (grep / file search) and work from selected excerpts, not the whole
   file.
7. **Lightest useful form of the source.** Prefer plain text or markdown over
   PDFs, images, and screenshots. If a heavy file is attached, extract only
   the text that matters.
8. **Run exact work as code.** Deterministic work (counts, formatting,
   extraction, regex, diffs) gets executed as code, not "reasoned through" in
   the model.
9. **Save the accepted version.** When the user accepts a result, save it as a
   named artifact. Build the next request from saved artifact + delta, not
   from the full conversation.
10. **Keep answers retrievable.** Store results the user is likely to ask about
    again in a file or database they can look up, so future calls retrieve
    instead of recompute.
11. **Load only the tools the job can use.** Tool definitions are model input
    before anything else. For a narrow task, prefer fewer, smaller tools over
    a broad toolset.
12. **Know the context window.** Track roughly how much context is in play.
    When it grows large, compact: summarize the working state, drop old tool
    results and intermediate reasoning, keep only what the next step needs.
13. **Use the dumbest model that gets the job done.** Reserve the largest model
    for the steps that genuinely need it; route easy steps to cheaper ones.
14. **Enforce hard limits.** Never send or receive more than the user-specified
    packet size. If a step would exceed it, split the step or ask.
15. **Check for an accepted answer first.** Before making a model call, check
    whether the answer already exists in a saved store. If it does, retrieve
    it instead of paying for the call.

## Per-task checklist (run before each call)

- Same job as the last message? If not, start fresh from the artifact.
- Do I need the whole file, or just a passage? Search first.
- Is this answer already saved somewhere? Retrieve it.
- What is the smallest complete answer?

## Output discipline

- If the user specified a format or length, match it exactly.
- If not specified, default to the shortest complete answer: bullets over
  prose, no preamble, no restating the question, no recap of what you did.

## Retry policy

- If an answer fails, fix the original request and resend it — do not stack
  corrections on top of corrections.
- Maximum 2 retries with the same approach. After that, change strategy
  (smaller scope, different tool, different model) or ask the user.
- Never retry by re-sending the whole conversation with "try again."