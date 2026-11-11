# Exercises: confidentiality, integrity and availability in enterprise systems

Short exercises to go with the write-up in the README. Each one is
about the trade-offs — there is rarely one right answer, and the
point is to notice what a control costs, not just what it protects.

## 1. Rank the three goals for three systems

For each system below, rank confidentiality, integrity and
availability from most to least important, and write one sentence
explaining the top choice.

- A hospital's emergency department record system.
- A bank's core ledger — the system that records who owns what money.
- A video streaming service.

Then compare your ranking with the examples in the README's short
version. If your hospital ranking puts confidentiality first, think
again about what happens when a doctor cannot open a record during
an emergency.

## 2. Spot the workaround

A company makes its staff log in with a password, a phone app code
and a hardware token, and changes the password every 30 days. Six
months later, an audit finds passwords written on sticky notes,
shared accounts, and work files emailed to personal addresses.

- Which goal did the company try to protect, and which goal actually
  got worse?
- What would you change: the controls, or how the controls are
  explained and enforced? Give one concrete change.

## 3. Price a control

Pick one control from the list and work out what it costs each of
the three goals — not in money, but in what gets harder.

- Encrypting every database field, including the ones staff search
  on every day.
- Requiring two managers to approve any change to a production
  system.
- Keeping three live copies of a system in three different regions.

For each one, name one thing it improves and one thing it makes
worse. If a control makes nothing worse, you have not looked hard
enough yet.

## 4. Find the seam

Most real failures happen in the seams between strategies, not in
the strategies themselves. Read this scenario and find the seam:

A bank encrypts customer data in its database and hashes every
transaction. The audit log is signed, so nobody can change it
without being caught. But the backup tapes are stored unencrypted
in a warehouse, and the support team can export a customer's full
record to a spreadsheet to "help faster".

- List every place the confidentiality of that customer data
  depends on something other than the encryption.
- Which single fix would you make first, and why that one?

## 5. Argue the other side

Pick a position from the write-up that you agree with — for
example, "a hospital should protect integrity over secrecy in an
emergency" — and argue against it for five minutes, out loud or
on paper. Then write down the strongest point from the other side.

If you cannot find a strong point against your own position, that
usually means the scenario is missing something, not that the
position is obviously right.
