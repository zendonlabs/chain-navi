# Chain Navi — Blueprint guide

The Blueprint is the one document the owner controls. It says what the product
should do, in the owner's own words. Everything else in the project must agree
with it.

**The owner does not have to write it.** The agent drafts it by asking the
owner questions, then the owner reads it and says "yes, that's right" or
corrects it. Only after the owner confirms it does it become the rule.

---

## For the agent: a project that is already half-built

This is the most common case: the app exists, bugs keep coming back, and fixes
pile up as patches on patches. Do not start from a blank page, and do not
propose a rewrite.

1. **Read the app first.** Go through its screens, flows and data, and write
   down what it does today as "when the user does X, they see Y".
2. **Collect the open problems.** Ask the owner to list what is wrong in their
   own words ("the total is different after I sync"). Turn each one into
   "user does X → sees Y, but should see Z?".
3. **Draft the Blueprint from both.** Behavior that is clearly intended goes in
   as is. Where today's behavior and the owner's problems disagree, or where
   you cannot tell what was intended, ask the owner one plain question each,
   with your recommendation. Start with the areas where the bugs are.
4. **Confirm it with the owner**, then build the truth map from the existing
   code (`CHAIN.md` §11) for the areas the bugs touch first.
5. **Fix the backlog one scenario at a time**, oldest and most painful first,
   each at its root, following `CHAIN.md` §5. Areas with no bugs can wait.

The aim is not a perfect document on day one. It is one confirmed answer for
each place the project keeps breaking.

---

## For the agent: how to interview the owner

Use the owner's language and everyday words. Ask **one question at a time**,
and offer a concrete example or a recommendation the owner can just accept.
Before asking, look at what already exists (the app, its screens, its data) so
you ask about meaning, not about things you can find out yourself.

Cover these, in this order:

1. **The point.** Who is this for, and what problem does it solve for them?
   (One or two sentences.)
2. **The main moments.** Walk through what a user actually does, start to
   finish. For each step: "When the user does X, what should they see?"
3. **What must never be lost.** Which information would upset a user if it
   disappeared or changed by itself?
4. **What must never change without asking.** Screens, wording or flows the
   owner wants protected.
5. **Edge cases that matter.** Undo, delete, offline, two devices, wrong
   input, money, time zones — only the ones that apply. Ask as
   "If the user does X, should they see Y or Z? I recommend Y because …".
6. **Not in scope.** What the product deliberately does not do, for now.
7. **Owner-only actions.** Which actions must always wait for the owner's go
   (publishing, charging money, contacting users, deleting data).

Then write the Blueprint using the template below, read it back to the owner in
plain words, and record their confirmation (word for word) in the trail.

After the owner confirms, add one line to the project's `CLAUDE.md` or
`AGENTS.md` (create the file if needed): "This project follows Chain Navi
(Blueprint: docs/blueprint.md)." Every later session will then know.

**Do not put in the Blueprint:** databases, APIs, file names, libraries,
release steps or any other engineering detail. Those belong to the truth map
and the code.

### Changing the Blueprint later

- If a task needs an answer the Blueprint does not give, ask **one** question
  with a recommendation, then add the answer as one new scenario in the
  owner's words.
- Never quietly reinterpret a scenario. If code and Blueprint disagree, the
  Blueprint wins unless the owner changes it.
- Keep old decisions short: replace the scenario, keep its number, and note
  the date and the owner's words in the trail.

---

## Template

Copy this into `docs/blueprint.md` (or wherever the project keeps it).

```markdown
# <Product name> — Blueprint

Confirmed by the owner on <date>.

## The point
<Who it is for and what it does for them, in one or two sentences.>

## Scenarios
Each scenario: "When the user does X (under these conditions), they see Y."

### <Area, e.g. Recording a payment>
- **B1.** When I …, I see …
- **B2.** When I …, I see …

### <Next area>
- **B3.** …

## Must never be lost
- …

## Protected — ask before changing
- …

## Not in scope (for now)
- …

## Owner-only actions
- …
```

Number every scenario (B1, B2 …). Tests, trails and reviews refer to these
numbers, so the same scenario is written once and checked everywhere. Never
reuse or renumber a scenario: tests and trails depend on its number.

See a filled-in example at
https://github.com/zendonlabs/chain-navi/blob/main/examples/blueprint-example.md
