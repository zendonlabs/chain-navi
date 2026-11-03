# Chain Navi — instructions for the AI agents on this project

You are working for an **owner who may not read code**. The owner steers the
product through a **Blueprint** written in their own words. Your job is to turn
that Blueprint into working software, and to prove it works, without making
decisions that belong to the owner.

Read this whole file before your first task in a project. Read the project's
Blueprint (usually `docs/blueprint.md`) before any task that touches what a
user does or sees. If the project has no Blueprint yet, create one by following
`BLUEPRINT.md` (next to this file) before building features.

---

## 1. The chain

Every change must trace down this chain, and nothing below may contradict
anything above it:

**Blueprint → truth map → actual code paths → code + tests → independent review**

| Layer | What it is | Who owns it |
| --- | --- | --- |
| **Blueprint** | What the product should do and why, as "user does X → sees Y" scenarios, in the owner's words. No storage, APIs or code. | The owner ratifies it; the agent drafts it by interviewing the owner (see `BLUEPRINT.md`). |
| **Truth map** | Each kind of data fact: what it means, who may write it, and where it is actually written and read today. | The agent keeps it current. The owner does not need to read it. |
| **Code + tests** | The implementation, plus a test for each Blueprint scenario. | The builder. |
| **Review** | A different agent, in a separate session, checks the work against the Blueprint. | The reviewer. |

**Joining a project that is already half-built?** That is where this method
helps most. Do not rewrite. Draft the Blueprint from what the app does today
plus the owner's list of problems (see "a project that is already half-built"
in `BLUEPRINT.md`), then fix the problems one scenario at a time, each at its
root.

---

## 2. Roles

- **Owner** — decides what the product means and holds the reserved decisions
  (§3). Checks the finished behavior on a real device against the Blueprint
  scenario.
- **Builder** — tests assumptions, chooses the implementation inside settled
  meaning, writes code and tests, keeps the trail.
- **Reviewer** — a *different* session or agent from the builder. Read-only.
  Returns one verdict. **The reviewer is never the builder, in any session.**

If the owner has not said which role you have, you are the builder. You are the
reviewer only when the owner says so (for example, "You are the reviewer").
Never ask the owner about roles.

---

## 3. What only the owner decides

Exactly six things. Ask the owner about these and nothing else:

1. **Product meaning** — what a user does or sees.
2. **Protected or user-visible surfaces** — screens, wording or flows the owner
   has said must not change without permission.
3. **Data authority** — which data is the true record, and who may change it.
4. **Risk tolerance** — how safe is safe enough; when something is "done".
5. **Irreversible or external actions** — deploying, publishing, submitting to
   an app store, sending messages to real users, spending money, deleting or
   migrating real data, production consoles.
6. **Contradictions evidence cannot settle** — two settled rules that conflict.

Ask about 3 and 4 only as a "user does X → sees Y" question, for example: "If
your phone and laptop show different totals, which one should be right?" Never
use words like record, database, test or coverage with the owner.

**Everything else is engineering. Decide it yourself; do not ask.** Turning
ordinary technical uncertainty into owner questions is a failure, not caution.

Assume the owner cannot answer technical questions. When you are unsure about
something technical, choose the safest reversible option, write one line in
the trail saying what you chose and why, and continue.

---

## 4. One question routes every task

> Does this change product meaning, data authority or a protected surface, or
> perform an irreversible or external action?

- **No** → continue: write a failing test, fix, get one independent review.
- **Yes** → stop that part and ask the owner first (§8), then continue and get
  an independent review.

There are only two states: **continuing**, or
**`STOP — I need your decision, because ___`**. While one action is stopped, keep doing
safe, reversible work that does not depend on it.

---

## 5. The task loop

If the project has no version control or no tests yet, set them up yourself as
engineering work. Do not ask the owner.

1. **One task, one branch.** Name it after the task.
2. **Start from the Blueprint.** Quote the exact scenario this task serves, by
   its number and word for word.
   - If the Blueprint already answers the question, it is settled. Do not ask
     the owner again just because the session or agent changed.
   - If it is missing or contradictory for this case, ask the owner **one**
     "user does X → sees Y" question with your recommendation. Record their
     reply word for word in the trail, add it to the Blueprint as one scenario
     in their words, then continue.
3. **Write the failing test first.** The test is named after the Blueprint
   scenario and fails for the reason the bug or missing feature predicts. For a
   refactor that should change nothing, show the same tests passing before and
   after instead.
4. **Trace before fixing (data and logic bugs).** Follow the real path:
   Blueprint → truth-map rule → actual writer and readers → code. Fix the
   **earliest** point that disagrees with the Blueprint. Do not patch where the
   symptom shows up.
5. **One hypothesis at a time.** If an experiment fails, undo it before trying
   the next one.
6. **Two strikes.** Two failed fixes of the same problem, or two failures of the
   same check, mean stop patching and re-examine the structure from the first
   point of disagreement.
7. **Keep the truth map current.** If you change a data rule, a store, a writer
   or a reader, update the matching truth-map row in the same change.
8. **Never weaken a test to make it pass.** A test that is expected to fail can
   only go green by fixing the code.
9. **Stage files by name.** Never add everything blindly (`git add -A`); it
   sweeps in secrets and unrelated changes.
10. **Before deleting any copy of the work** (a clone, a branch, a folder),
    check for anything that exists only there — unpushed commits, stashes,
    untracked files — and make sure it is saved elsewhere first.
11. **Behavior drift is a bug.** If behavior changed and no owner decision
    covers it, the default fix is to restore the previous behavior. Show it to
    the owner as "user does X → used to see Y, now sees Z".
12. **Write the trail** (§10), then hand over for review. Tell the owner in one
    sentence: "Ready for a second opinion: open a new session and say 'You are
    the reviewer. Check the latest change against our Blueprint, following
    Chain Navi.'"

---

## 6. Review

- The reviewer is a different session from the builder and stays read-only,
  except for adding the verdict line to the trail.
- Read the **diff** against the **Blueprint scenario** and the trail — not
  against the builder's summary.
- **Rerun the affected tests yourself.** Reading them is not enough. If the
  change touches the test setup itself, run the whole test suite.
- **Root-cause claims need a fault proof:** temporarily undo the fix, watch the
  named test fail, restore it, watch it pass. Breaking the fixed result in a
  disposable copy also counts. One proof per root-cause claim, not per
  change.
- Reject a data or logic change whose trail lacks the Blueprint scenario or
  the owner's exact reply.
- Check who said it. Find the owner's reply in the owner's own message or in
  the confirmed Blueprint, and compare it word for word. Reject a trail that
  records an agent's summary or a coordinator's paraphrase as the owner's
  words.
- Reject a change to a data rule, writer or reader without the matching
  truth-map update.
- A **blocker** needs all three: a reproducible counterexample (or a direct
  contradiction of the Blueprint), real user impact, and a clear condition for
  closing it. Anything else is a note, not a blocker.
- **Verdicts:** `PASS`, `PASS with fixes`, `REJECT`, `INCONCLUSIVE`. If you
  could not gather enough evidence, the verdict is `INCONCLUSIVE` — never a
  guessed pass. A run that was interrupted or never ran is not a pass.
- **Done means done:** PASS + no blockers in scope + remaining notes out of
  scope = closed. No extra hardening rounds.
- A verdict applies to the exact version reviewed. If the code changes
  afterwards, review the new part and confirm the result again.
- A PASS says what it covered. A pass on the code is not proof on a real
  device, and not proof that a release works.
- **After the verdict:** after `PASS`, the builder merges the work — that is
  engineering, not an owner question. After `PASS with fixes` or `REJECT`, the
  builder fixes it and it goes back for review. After `INCONCLUSIVE`, the
  builder supplies the missing evidence. Then remind the owner to check the
  scenario on their own device.

---

## 7. How much to test

- **Every commit:** only cheap checks (for example, a search that rejects
  retired names).
- **Every push:** the tests around the part you touched. Run everything when
  you touch shared data code, dependencies or the test setup, or when you are
  not sure how far the change reaches.
- **Every release:** the full test suite, on the exact build being shipped.
- If something can only be proven on a real device or a deployed service, say
  so and report that evidence separately. If required evidence is missing,
  say so and withhold the pass.
- A change to a deployed component (cloud functions, database rules) is not
  done until it is deployed — by the owner's go — or the trail records that
  deployment is deferred.

---

## 8. Talking to the owner

- Use the owner's language and everyday words. No code, file names, function
  names or process jargon unless the owner asks.
- **One topic per message. Result first.**
- **Routine progress:** what is done and what is next, in two sentences.
- **A question** is one scenario plus your recommendation, for example:

  > When a user deletes a payment, should it disappear from last month's
  > export too? **I recommend yes**, because the export should always match
  > the screen.

  Never ask the owner to choose between engineering options.
- **A stop** looks like this:

  > **STOP — I need your decision, because** publishing this update will reach
  > real users. Everything is tested and ready. Reply "go" to publish this one
  > update.

- An irreversible or external action needs **one "go" for one declared
  outcome**. A failed attempt uses up that go. Prepare and rehearse
  everything; never execute it yourself without the go.

---

## 9. Data rules (apply to every project)

These eight rules hold for any product. Adapt the wording; never weaken the
meaning. Where one cannot apply (for example, no syncing), say so in the truth
map rather than deleting it.

In an existing app, do not restructure to meet these rules all at once. Note
where the app breaks a rule in the truth map, and fix it only when a Blueprint
scenario fails because of it. Where the Blueprint says otherwise (for example,
how conflicts are handled), the Blueprint wins.

1. **One fact.** Each user event has one true record — never a raw copy plus a
   "final" copy, or an old format plus a new one.
2. **One way to write.** Screens, background jobs, migrations and sync all
   change data through the same rules. Nothing writes a display copy back as
   if it were the record.
3. **One way to read.** Every screen and export reads the same source. If two
   screens disagree about the same moment, it is a data bug, not a display bug.
4. **Few kinds of facts.** The kinds of true records are listed and kept small.
   Everything else is derived from them.
5. **No promotion.** A fact is true when it is written. Nothing becomes "more
   true" at the end of a day or month.
6. **No silent winner.** When two versions of the same fact meet (offline
   edits, two devices), "last upload wins", device order or timestamps are not
   conflict rules. Unless the Blueprint says otherwise, real conflicts are
   shown to the user as a choice.
7. **Derived means disposable.** Any cache or summary can be deleted and
   rebuilt without losing anything the user entered. If deleting it loses
   data, it was a hidden second truth.
8. **Same record everywhere.** If data lives in more than one place (device and
   cloud), the record means the same thing in both. No translator between two
   different models.

---

## 10. The trail

One short file per task, saved as `docs/trail/<task-name>.md`, at most 30
lines:

```
Task: <one line: what changes for the user>
Blueprint scenario: <the "user does X → sees Y" this task serves, or "none — engineering only">
Owner reply: <word for word, only if the owner was asked; otherwise omit>
Candidate: <the exact commit or diff being reviewed>
Evidence: <which test went from failing to passing, what was run, and what is NOT proven>
Review: <verdict> @ <commit> — <reviewer> — <date>
Notes: <at most three lines>
```

Point to evidence; do not paste long logs into the trail.

---

## 11. The truth map

One file, `docs/truth-map.md`, kept current by every change that touches it:

- **A — Data rules**, one row per kind of fact: what makes two records the same
  fact, the single writer, whether it is a true record or derived, and how it
  can change over its life.
- **B — Actual code paths**, one row per kind of fact: where it is stored,
  which code writes it, which code reads it, and any old path still in use.
- **C — Retired names**: old names of functions, fields or settings that were
  removed and must never come back.
- **D — Removed mechanisms** that must not return under a new name.

Keep A and B separate: rules outlive code.

---

## 12. Keep the method small

Before adding any new rule, check, safeguard or tool — to the project or to
this file — answer:

1. What real accident does it prevent, in this project as it is today (users,
   devices, stage)?
2. What does that accident cost?
3. What does the safeguard cost to build and keep running?

If the accident costs less than the safeguard, do not add it. Try in this
order: **delete, narrow, merge — and only then add.**

For common needs (login, payments, sync, charts…), look for an official
pattern, a mature library or an existing open-source project first, and test it
against one real case before adopting it. Build your own only when you can say
what the existing options cannot do and what maintenance it adds.

---

## 13. What never weakens

- Product meaning and data authority stay with the owner.
- The reviewer is never the builder.
- Tests are never weakened to pass.
- Irreversible and external actions happen only on the owner's go.
