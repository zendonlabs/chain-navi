# One payment, two views

**In plain words:** a user records a $12 payment. The screen shows $12, but the export shows $0. Instead of patching the export, the builder checks the Blueprint, follows where the number is actually written and read, and finds that the export reads an old copy. The fix makes the export read the same source as the screen. A different reviewer then proves the fix is what made the difference. The rest of this page shows the same story in a few lines of code, for readers who want to try it.

This is a fictional exercise. Nothing here charges a card. Read it on paper, or save the Python block below as `one_payment.py` and run `python3 one_payment.py` in a scratch directory.

**Blueprint B1:** “When I record a $12.00 payment, both the screen and my export show $12.00.” The worked scenario has one account, one currency and no refunds. A later refund would need a rule of its own.

| Truth map | What the builder found |
| --- | --- |
| A — data rule | Each recorded payment has one ID and an integer amount in cents. The total is the sum of recorded amounts. Screen and export are readers. |
| B — actual path | `record_payment` writes a payment; `screen` reads `total_cents`; `export` reads an old cached total. |

The writer stores 1200 cents and the screen is right. The first disagreement is the export's choice of reader. Updating a second total on every write would add a second place to keep correct.

```python
def record_payment(book, payment_id, cents):
    book["payments"][payment_id] = cents

def total_cents(book):
    return sum(book["payments"].values())

def screen(book):
    return total_cents(book)

def export(book):
    return book["cached_total"]  # Old reader; observe the failure first.

def test_b1_one_payment_two_views():
    book = {"payments": {}, "cached_total": 0}
    record_payment(book, "p1", 1200)
    observed = (screen(book), export(book))
    assert observed == (1200, 1200), observed

test_b1_one_payment_two_views()
print("B1 passed")
```

**Walk the repair.** The first run should fail with `AssertionError: (1200, 0)`. Replace the export body with `return total_cents(book)` and remove the unused `cached_total` field from the fixture. The second run should print `B1 passed`. Update B of the map: both readers now use `total_cents`; A has not changed.

**What the reviewer does.** Open the actual candidate, read B1, and run the check yourself. To challenge the cause, temporarily change only the export result to `0` in a disposable copy: the same assertion must fail again. Restore and rerun. (Undoing the fix itself works just as well; either counts as the fault proof.) In a real app, repeat through the actual export entry point; this example proves nothing about that app's file format or ingestion.

A sufficient trail for a real repair would link B1, the candidate and the check result, then state who verified which path. It need not repeat the payment history or the test output.
