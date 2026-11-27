# Glossary — CIA in Enterprise Systems

The README already has a short glossary of the terms used most in this
write-up. This file adds the terms you will meet when you read further
about how large organisations plan, run and recover their systems.
Plain English, one or two lines per term, in alphabetical order.

## The terms

- **BCP — Business Continuity Plan** — the plan for keeping the
  business running during a disruption: who decides what, which
  systems matter most, and what the manual workaround is while systems
  are down. It is the business side of the technical recovery plan.
- **Blast radius** — how far the damage spreads when something fails or
  is breached. Segmentation is about shrinking the blast radius: a
  breach of the website should not reach the core ledger.
- **Chaos engineering** — deliberately breaking parts of a system, in
  production, to see whether it survives. Netflix's Chaos Monkey
  randomly switched off servers during working hours. If the system kept
  working, the redundancy was real; if not, the team found the weakness
  before a real failure did.
- **Defence in depth** — layering several controls so that if one fails,
  another still holds. A firewall, plus authentication, plus encryption,
  plus monitoring: an attacker has to beat all of them, not just one.
- **Disaster recovery** — the technical plan for getting systems back
  after a major failure: restoring from backups, failing over to
  another region, rebuilding what was lost. It is measured by RTO and
  RPO.
- **DLP — Data Loss Prevention** — tools and rules that stop sensitive
  data leaving the organisation: blocking an email with patient records
  attached, or flagging a large download from the customer database.
  It is a confidentiality control aimed at the exit, not the entrance.
- **Failover** — the automatic switch to a backup system when the main
  one fails. Redundancy is having the spare; failover is the spare
  actually taking over. It has to be tested, or it is only a hope.
- **IAM — Identity and Access Management** — the systems that decide who
  people are and what they may do: accounts, roles, permissions, and
  the processes for granting and removing access. Most breaches involve
  an IAM failure somewhere: an account with too much access, or an
  account that should have been removed months ago.
- **Incident response playbook** — a short, practised set of steps for a
  specific incident: who decides to isolate a server, who tells
  managers, when to call in outside help. Written before the incident,
  not during it, when nobody is thinking clearly.
- **Key management** — the work of creating, storing, rotating and
  retiring encryption keys safely. Encryption is only as strong as its
  key handling: a key sitting next to the data it protects is barely
  better than no encryption at all.
- **Least privilege** — giving a person or system only the smallest
  access needed to do their job. It limits the damage when an account
  is compromised, and it is the single cheapest control in this whole
  glossary.
- **Penetration test** — a controlled, authorised attempt to break into
  a system, done to find weaknesses before an attacker does. It tests
  the whole system together, which is where the failures usually hide.
- **Redundancy** — having more than one of something important, so one
  failure does not take the system down. Two servers, two network
  links, two regions. It costs money and makes confidentiality harder,
  because data now lives in more places.
- **RPO — Recovery Point Objective** — how much data you are willing to
  lose. An RPO of one hour means backups at least every hour.
- **RTO — Recovery Time Objective** — how long you are willing to be
  down. An RTO of four hours means you must be able to restore the
  system within four hours.
- **SIEM — Security Information and Event Management** — the system that
  collects logs and alerts from across the organisation into one place.
  Powerful, but it can produce thousands of alerts a day — which is how
  alert fatigue happens.
- **SLA — Service Level Agreement** — a promised level of service, such
  as 99.9% uptime, usually written into a contract with a supplier.
  It is the number that turns "available" into something measurable
  and enforceable.
- **Third-party risk** — the risk that comes from suppliers and partners:
  a file transfer tool with a vulnerability, a cloud provider going
  down, a contractor with too much access. The 2023 MOVEit breach is
  the example in the README: one tool's flaw hit hundreds of
  organisations at once.
- **Uptime** — the share of time a system is working, usually quoted as
  a percentage. 99.9% means about 43 minutes of downtime a month;
  99.99% means about 4 minutes. Each extra nine costs roughly ten
  times as much.

## Terms that are easy to mix up

- **RTO vs RPO** — RTO is how long you can be down, RPO is how much
  data you can lose. One is about time, the other is about data.
- **BCP vs disaster recovery** — the business continuity plan is about
  keeping the business running during a disruption; disaster recovery
  is the technical work of getting the systems back. You need both.
- **Redundancy vs failover** — redundancy is having a spare, failover
  is the spare taking over. A spare that has never been tested taking
  over is an assumption, not a fact.
