# More case studies — the CIA triad in real breaches

The `README.md` already covers the 2023 MOVEit breach in detail.
These are two more real cases, each read through the CIA triad: which
leg broke, what the knock-on effects were, and what the case teaches
that MOVEit does not.

---

## 1. Change Healthcare, 2024 — when availability becomes a safety issue

**What happened:** In February 2024, the US health payments company
Change Healthcare was hit by ransomware. The attackers got in through
a remote-access portal that had no multi-factor authentication, stole
data, encrypted systems, and the company took large parts of its
network offline to contain the attack.

**Which legs broke?**

- **Confidentiality** — patient and payment data was stolen before the
  encryption started. The attackers later claimed to hold data on a
  huge number of people.
- **Availability** — this was the bigger story. Change Healthcare
  processes payments and prescriptions for a large share of US
  pharmacies and hospitals. With its systems down, pharmacies could
  not process insurance claims, patients could not get prescriptions
  filled, and hospitals could not be paid. The outage lasted weeks,
  and the disruption spread far beyond the one company that was
  attacked.

**What this case teaches:** in healthcare, availability is not just a
business problem — it is a patient-safety problem. A hospital can
survive a leaked record; it cannot treat patients if the payment and
prescription systems are down for a month. It also shows how one
company can be a single point of failure for a whole sector. The
attackers did not need to hit every hospital — hitting the shared
service in the middle was enough. That is exactly the kind of hidden
dependency the `README.md` warns about in "The single points of
failure nobody notices".

**The small detail that mattered most:** the entry point was a portal
without MFA. One missing control, on one system, led to a
sector-wide outage. Availability failed because confidentiality
controls (authentication) failed first — the legs are connected.

## 2. CrowdStrike, July 2024 — when nobody attacks you at all

**What happened:** In July 2024, a faulty update to CrowdStrike's
security software crashed millions of Windows computers around the
world. Airlines cancelled flights, hospitals delayed procedures,
banks and shops could not operate. There was no attacker, no stolen
data, and no ransom demand.

**Which legs broke?**

- **Availability** — completely. Systems that were working fine in
  the morning were unusable by lunchtime, and fixing them often meant
  touching each machine by hand, one at a time.
- **Integrity** — in a strange way, yes. The update itself was a
  trusted, signed file from a trusted vendor. The *content* of that
  file was wrong, and because it came through a trusted channel, it
  was accepted everywhere without question. Integrity is not only
  about attackers changing things — it is also about making sure a
  change is correct before it goes out.
- **Confidentiality** — not really. Almost no data was exposed. That
  is part of the lesson: a disaster can be entirely about availability
  and still be one of the biggest IT incidents ever.

**What this case teaches:** two things the MOVEit case does not.
First, your biggest risk might not be an attacker — it can be a
trusted supplier making a mistake. A vendor's bad update can do what
a ransomware gang dreams of doing. Second, recovery speed matters as
much as prevention. The companies that recovered fastest were the
ones that could fix machines without visiting each one in person, and
that had tested their recovery process before they needed it. Nobody
plans for their security tool to be the thing that takes them down —
which is exactly why it needs to be in the plan.

---

## Reading these cases like an analyst

For each case, ask three questions:

1. Which leg broke *first*? (That is usually where the fix belongs.)
2. Which other legs broke because of it? (That is how far the damage
   spreads.)
3. What would have limited the damage, even if the first failure still
   happened? (That is defence in depth — and it is the real lesson.)

If you can answer all three for a case you read in the news, you are
thinking about security the way this repo teaches it.
