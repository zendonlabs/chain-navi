# Example Blueprint — Pocket Tally

*A fictional app, used only to show what a finished Blueprint looks like. It
was drafted by an agent from an interview and then confirmed by the owner.*

---

# Pocket Tally — Blueprint

Confirmed by the owner on 2026-01-15.

## The point
Pocket Tally helps one person see where their money went this month, without
connecting a bank account.

## Scenarios

### Recording a payment
- **B1.** When I record a $12.00 payment, both the screen and my export show
  $12.00.
- **B2.** When I record a payment without choosing a category, it appears
  under "Other", never under a category I did not pick.
- **B3.** When I record a payment while offline, it appears immediately and is
  still there after I go back online.

### Changing my mind
- **B4.** When I delete a payment, it disappears from the month total, the
  list and any export made afterwards.
- **B5.** When I delete a payment by mistake, I can undo it for 10 seconds;
  after that it is gone.

### Looking back
- **B6.** When I open a past month, I see the same total I saw at the end of
  that month, unless I edited or deleted a payment in it.
- **B7.** When I export a month, the file lists the same payments, in the same
  amounts, as the screen.

## Must never be lost
- Any payment I recorded and did not delete.

## Protected — ask before changing
- The home screen layout.
- The wording "Spent this month".

## Not in scope (for now)
- Bank connections.
- Shared budgets with other people.
- More than one currency.

## Owner-only actions
- Publishing a new version to the app store.
- Anything that deletes or moves real users' data.
