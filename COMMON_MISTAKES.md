# Common Mistakes in Enterprise CIA

The README explains how confidentiality, integrity and availability are
supposed to fit together. This file is about how they usually fall apart
in practice — not because a control was missing, but because of how the
controls were used, measured or combined.

## 1. Treating CIA as a checklist

"Encryption: done. Hashing: done. Backup server: done." Ticking three
boxes feels like progress, but a control only helps if it is actually
doing its job. Encryption with the key stored next to the data, a hash
that is never re-checked, and a backup that has never been restored are
all ticked boxes that protect nothing.

Ask for each control: who checks that it still works, and how often? A
control nobody verifies is a hope, not a control.

## 2. Measuring uptime and ignoring the other two

Availability is easy to measure — the system was up 99.9% of the time, or
it was not. Confidentiality is measured by absence: nothing leaked, as
far as anyone knows. Integrity is measured by detection: tampering was
caught, or nobody noticed.

The result is that teams over-invest in the thing they can show on a
dashboard and under-invest in the things they only notice when they fail.
A quiet leak or a record changed months ago does not show up in any
uptime graph. If your reporting only covers availability, your blind
spots are exactly where the other two goals live.

## 3. Encrypting the data and leaving the key beside it

Encryption is the last line for confidentiality, but it only works if
the key is kept separate from the data. A database encrypted with a key
stored in the same folder, in the same config file, or in the same
backups is barely more secure than no encryption at all — anyone who
gets the data gets the key with it.

Keys belong in a dedicated key management service, with their own access
rules, and losing a key must be treated as seriously as losing the data,
because encrypted data without its key is gone for good.

## 4. Backups that have never been tested

A backup that has never been restored is not a backup — it is a hope.
This is one of the most common findings after a real incident: the
backups existed, but they were corrupt, incomplete, or so old that
restoring them would lose weeks of data. Nobody knew, because nobody
had ever tried.

Test restores regularly, on a schedule, and time them. If a restore takes
six hours, that is six hours of downtime you need to plan for — better to
learn that in a drill than during an actual outage.

## 5. Logging everything and watching nothing

Many enterprises collect enormous amounts of log data and then do almost
nothing with it. The logs exist, but nobody reads them, the alerts are
tuned so loosely that real attacks drown in noise, or different parts of
the system log in different formats so the monitoring tool only reads
some of them.

Logging without monitoring is the same mistake as hashing without
checking: the mechanism exists, but it never actually catches anything.
Decide in advance which events matter — failed logins, privilege changes,
access to the most sensitive records — and make sure someone, or
something, is actually watching for them.

## 6. Segmenting the network but sharing one service account

Segmentation is supposed to contain a breach: if the website segment is
attacked, the attacker should not automatically reach the core ledger.
That only works if nothing bridges the segments. A single service account
with access to every segment — used by a backup tool, a monitoring agent,
or a deployment script — is a bridge. Compromise that one account and the
segmentation is gone.

Every account that crosses a boundary should be reviewed like a firewall
rule: does it really need access on both sides, and what happens if it is
stolen? Shared, over-privileged service accounts are one of the easiest
ways to undo a careful network design.

## 7. Locking things down so tightly that staff work around them

Strong authentication is good for confidentiality, until logging in takes
five steps and a hardware token, and staff start writing passwords on
sticky notes, sharing accounts, or emailing files to personal addresses
so they can work from home. The control exists on paper, but in practice
confidentiality is worse than before, because the workaround is invisible
and uncontrolled.

When a control is too painful to use, people will route around it — and
the workaround is always less secure than the control was. If staff are
working around a control, that is a signal the control needs redesigning,
not that the staff need more training.

## 8. Fixing the breach but not the seam

After an incident, the usual response is to fix the specific thing that
broke: patch the vulnerability, close the open bucket, reset the leaked
password. That fixes the symptom. The failures in the README mostly come
from the seams between strategies — the DNS provider nobody counted as a
dependency, the checkbox that connected two teams' assumptions — and a
fix that only patches the hole leaves the seam in place for the next
incident.

After any incident, ask one extra question: what seam did this come
through, and who owns that seam now? If nobody owns it, it will break
again.

## A quick self-check

- Can you name the last time a backup was actually restored, and how
  long it took?
- Do you know who reads the security logs, and which events they watch
  for?
- Is there a service account with access to more than one segment?
  When was it last reviewed?
- Has anyone on the team worked around a security control this month?
  What did they do instead?
