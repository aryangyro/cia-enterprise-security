# Keeping Confidentiality, Integrity and Availability in Enterprise Systems

Enterprise systems are the big computer systems that companies, banks,
hospitals and governments depend on every day. They hold customer data,
money, medical records, and the services millions of people use without
thinking about it. Keeping those systems safe comes down to three ideas
that security people call the CIA triad: confidentiality, integrity and
availability.

- **Confidentiality** means only the right people can see the data.
  A patient's medical record should be visible to their doctor, not
  to everyone in the hospital, and definitely not to a stranger on
  the internet.
- **Integrity** means the data has not been changed by anyone who
  should not change it, and that you can prove it. If a bank balance
  or a medical dose was altered, someone needs to be able to spot
  that.
- **Availability** means the system works when people need it. A
  hospital system that is down during an emergency, or a bank that
  cannot process payments on payday, has failed just as badly as a
  system that leaked data.

This write-up is about how real organisations try to hold all three
together at once, why that is harder than it sounds, and where the
failures usually happen. It is written for beginners: no background in
security is assumed, and every idea is explained in plain English
before it is used.

## The short version

If you only read one section, read this one. The three goals pull
against each other. Make a system very hard to access and people
cannot use it when they need it. Spread data everywhere so the system
never goes down and it becomes much harder to keep that data private.
Lock everything down so nothing can be changed and the system becomes
too rigid to run a real business.

So enterprises do not try to maximise all three. They decide where
each one matters most, and build around that. A bank protects its core
ledger far more strictly than its marketing website. A hospital
protects the integrity of patient records even more than it protects
their secrecy in an emergency, because a wrong dose can kill someone.
A streaming service protects availability above almost everything
else, because if the video stops, people cancel.

The rest of this write-up walks through the strategies that show up
again and again in real systems: zero trust and segmentation for
confidentiality, hashing and signed logs for integrity, and
multi-region cloud design for availability. Then it looks at the part
most people miss — the seams between those strategies, where the real
failures happen.

## Why this is a trade-off, not a checklist

It is tempting to treat confidentiality, integrity and availability
as three boxes to tick. Add encryption, tick confidentiality. Add
hashing, tick integrity. Add a backup server, tick availability. Done.

Real systems do not work like that, for two reasons.

First, every control has a cost, and the cost is usually paid by one
of the other two goals. Strong authentication is good for
confidentiality, but if logging in takes five steps and a hardware
token, staff will find workarounds — writing passwords on sticky
notes, sharing accounts, or emailing files to personal addresses so
they can work from home. The control exists on paper, but in practice
confidentiality is worse than before, because the workaround is
invisible and uncontrolled.

Second, the three goals are measured on different scales. Availability
is easy to measure: the system was up 99.9% of the time, or it was
not. Confidentiality is measured by absence: nothing leaked, as far
as anyone knows. Integrity is measured by detection: tampering was
caught, or it was not noticed at all. That makes it easy to
over-invest in the thing you can measure (uptime) and under-invest
in the things you only notice when they fail (a quiet leak, a record
changed months ago).

A useful way to think about it is as a budget. You have a limited
amount of money, staff time, and patience from users. Every control
spends some of that budget. The job of the security architect is to
spend it where it matters most, and to be honest about what is being
given up.

## A quick glossary

| Term | Meaning |
| --- | --- |
| CIA triad | Confidentiality, integrity, availability — the three goals of information security. |
| Zero trust | A design idea: never trust a request just because it comes from inside the network. Verify every request. |
| Segmentation | Splitting a network into separate parts, so a breach in one part cannot easily reach the others. |
| Encryption | Scrambling data so only someone with the right key can read it. |
| Hashing | Producing a short fingerprint of data. Change one byte and the fingerprint changes completely. |
| Signed log | A log where each entry is sealed with a cryptographic signature, so tampering with the log is detectable. |
| Multi-region | Running a service in several separate data centre regions, so one region failing does not take the service down. |
| Single point of failure | One component whose failure breaks the whole system. |
| Misconfiguration | A setting left wrong — for example, a storage bucket left open to the internet. |
| Threat model | A structured description of what could go wrong, who might attack, and what matters most. |

## Part 1: Confidentiality — zero trust and segmentation

### The old idea: a wall around everything

For a long time, enterprise security worked like a castle. There was a
strong wall — the firewall — around the company network, and everything
inside the wall was trusted. Once you were in, you could reach most
things. That worked when everyone worked in the office, on company
computers, on the company network.

It does not work any more. People work from home, from cafes, from
phones. Companies use cloud services that live outside the wall.
Partners and suppliers need access to some systems. An attacker who
gets past the wall once — through a phishing email, for example — finds
themselves inside, trusted, with the run of the place.

### Zero trust: verify every request

Zero trust is the response to that problem. The name is a little
dramatic, but the idea is simple: do not trust a request because of
where it comes from. Trust it because it was checked, right now, for
this specific request.

In practice that means several checks happen every time someone asks
for something:

- **Who are you?** Authentication — a password, plus usually a
  second factor, like a code from a phone app.
- **Are you allowed this?** Authorisation — does this person, in
  this role, have permission for this exact thing?
- **Is your device healthy?** Is it a known company laptop with
  up-to-date software, or an unknown device?
- **Does this look normal?** Is the request coming from the usual
  country, at the usual time, for the usual kind of data?

If any of those checks fail, access is refused or limited. And the
checks are repeated, not done once at login and then forgotten. A
session that was fine an hour ago can be re-checked when it asks for
something sensitive.

Zero trust does not mean nobody is trusted. It means trust is earned
per request, and it can be taken away quickly.

### Segmentation: so one breach does not become everything

Zero trust checks each request. Segmentation limits what a successful
attack can reach. The network is split into separate zones, with
strict rules about what can move between them.

Think of a bank. The payment processing system — the core ledger that
records who paid whom — sits in its own segment, with very strict
access. Only a small number of systems and people can reach it, and
every access is logged. The customer-facing website and mobile app
live in a different segment. They can ask the core system to do
things, through a narrow, checked channel, but they cannot reach
directly into it.

If the website is attacked, the attacker gets the website segment.
They do not automatically get the ledger. That is the whole point:
turning one big breach into a small, contained one.

Segmentation shows up in other industries too. In a hospital, the
systems that control medical devices sit apart from the general office
network, because a compromised office computer should never be able
to reach an infusion pump. In a factory, the control systems for the
production line are separated from the business systems, for the same
reason.

The cost is complexity. Every segment boundary is a rule to write, a
firewall to maintain, and a possible cause of something breaking when
a legitimate request is blocked. That is why segmentation is usually
applied most strictly around the most valuable things, and more
loosely elsewhere — another trade-off.

### Encryption: the last line for confidentiality

Even with zero trust and segmentation, data should be encrypted. That
means scrambling it so that it is unreadable without the right key.
Two places matter:

- **In transit:** data moving between systems — over the internet,
  or between services inside a company — is encrypted, so anyone
  listening in the middle sees only scrambled bytes.
- **At rest:** data sitting in a database or on a disk is encrypted,
  so stealing a hard drive or a database backup does not hand over
  readable data.

Encryption is powerful, but it creates a new problem: the keys. If
the key sits next to the data, an attacker who gets the data gets the
key too. So serious systems store keys separately, in a dedicated
key management service, with strict access of their own. Losing a key
can be as bad as losing the data — encrypted data without its key is
gone for good.

## Part 2: Integrity — hashing and signed logs

### Why access control is not enough for integrity

Access control stops people doing things they should not do *through*
the system. It does not stop data being changed outside the system,
and it does not help when the person changing the data is allowed to
be there. A doctor is allowed to edit a patient record. A bank clerk
is allowed to update an account. The question is not only whether
they were allowed to change it, but whether the change is what it
claims to be, and whether anyone can tell afterwards.

That is what integrity controls are for. They do not just prevent
tampering. They make tampering *detectable*.

### Hashing: a fingerprint for data

A hash is a fingerprint. Feed data into a hash function — SHA-256 is
the standard one — and it produces a short, fixed-length string, 64
characters long. Change one character in the data and the fingerprint
changes completely. It is practically impossible to find two different
pieces of data with the same fingerprint, and you cannot work
backwards from a fingerprint to the original data.

Systems use this by storing the hash alongside the data, or in a
separate store. Every time the data is read, the hash is recomputed
and compared. If they match, the data is as it was. If they do not,
something changed it — and the system refuses to serve data it cannot
trust.

Healthcare is a good example. Patient records are hashed, and every
access is logged. Even if someone with legitimate access changes a
record, the change shows up: the hash no longer matches, or the log
shows a change nobody can explain. It is not just about stopping
tampering, it is about being able to prove later what happened.

The same idea protects software itself. When you download an update,
the publisher provides a hash. Your computer checks the downloaded
file against it. If they match, the file is genuine. If not, it was
altered somewhere along the way.

### Signed logs: a record that cannot be quietly rewritten

A log is a list of what happened: who did what, when, to which
record. Logs are essential for integrity, because they are how you
find out about tampering after the fact. But a plain log has an
obvious weakness: an attacker who changes the data can also change
the log to hide it.

Signed logs fix that. Each entry is sealed with a cryptographic
signature, often chained so that each entry depends on the one before
it. Change one entry and every entry after it fails its check. Some
systems go further and send log copies to a separate, protected store
in real time, so even an attacker with full access to the main system
cannot rewrite history.

This is also why integrity matters for compliance. Regulations in
healthcare and finance do not just ask whether data was kept safe.
They ask whether the organisation can *show* what happened to it.
A signed, complete audit trail is the answer to that question.

### Versioning and backups as integrity tools

Two quieter integrity tools deserve a mention. Versioning keeps old
copies of data, so a bad change can be undone and compared with what
was there before. Backups do the same on a larger scale. Neither
prevents tampering, but both limit the damage, and both make it
possible to recover the true state of the data. A backup that is never
tested, though, is not a backup — it is a hope. Enterprises that take
integrity seriously test their restores regularly.

## Part 3: Availability — multi-region cloud design

### What availability really means

Availability is usually quoted as a percentage: 99.9% uptime means
the system is allowed to be down for about 43 minutes a month. 99.99%
means about 4 minutes a month. Each extra nine costs roughly ten
times as much to achieve, because the failures you have to defend
against get rarer and stranger.

But a percentage hides the important question: down for whom, and
when? A system that is down for an hour at 3 a.m. on a Sunday is a
different problem from a system that is down for an hour on payday,
or during an emergency. Enterprises think about availability in terms
of what the business loses when the system is not there.

### Redundancy: having a spare of everything important

The basic availability strategy is redundancy — having more than one
of everything that matters. Two servers instead of one, two network
connections, two power supplies. If one fails, the other takes over.

That works for individual components. It does not work for a whole
data centre. If a flood, a fire, or a major power failure takes out
the building, every redundant component inside it goes down together.
So serious systems spread across regions.

### Multi-region: Netflix as an example

Netflix is the example most people reach for, because it is public
about how it works and because its availability demands are extreme:
millions of people watching at the same time, in the evening, when
any failure is immediately visible.

Netflix runs its service across multiple cloud regions — separate
geographic areas, each with its own data centres. If one region goes
down, traffic moves to another, and the service keeps running. The
system is designed so that the failure of a whole region is a routine
event it handles automatically, not a disaster.

That costs a lot. Running in several regions means paying for
capacity that mostly sits idle, and keeping data in sync across
regions is a hard engineering problem. It also makes confidentiality
harder, because data now lives in more places, which is why Netflix
encrypts everything in transit and at rest and keeps keys separate
from the data.

Not every enterprise needs that level of availability. A small
company might be fine with one region and a good backup. The point is
that the level of availability is a choice, with a price, and the
choice should be made deliberately, not by accident.

### Chaos engineering: breaking things on purpose

Netflix also popularised an idea called chaos engineering: deliberately
breaking parts of the system, in production, to see what happens. The
famous tool was called Chaos Monkey — it randomly switched off
servers during working hours. If the system kept working, the
redundancy was real. If it did not, the team learned about a weakness
before a real failure exposed it.

Most enterprises do a gentler version: planned failover tests,
disaster recovery exercises, and drills where a team pretends a
region is down and works through the response. The principle is the
same. Availability that has never been tested is an assumption, not
a fact.

### The single points of failure nobody notices

The most dangerous availability failures are the ones nobody planned
for, because they sit outside the system people were thinking about.
A company can have redundant servers, redundant regions, and
redundant networks, and still go down because of a single DNS
provider, a single certificate authority, or a single
third-party service that everything depends on.

That is why availability planning starts with a map: what does this
system actually depend on, all the way down? The answer is usually
longer and more surprising than anyone expected.

## Part 4: The seams — where failures actually happen

Here is the part that matters most, and the part most textbooks skip.
Individual controls usually work. The failures come from the seams
between them — the places where one strategy meets another, and
nobody owns the gap.

A system can have strong encryption and still go down because a
single DNS provider fails. Encryption protects confidentiality. DNS
is an availability dependency. Nobody who designed the encryption
was thinking about DNS, and nobody who chose the DNS provider was
thinking about encryption. The seam between them is where the
failure lives.

A system can be highly available and still leak data through a
misconfigured storage bucket. The availability team made data easy
to reach from everywhere, so that any region could serve it. The
confidentiality team assumed the bucket was private. The setting
that connected those two assumptions was a single checkbox, set
wrong, in a console nobody reviewed.

A system can have perfect audit logs and still miss an attack,
because the logs from one part of the system use a different format
from the logs in another part, and the monitoring tool only reads
one of them. The integrity strategy and the monitoring strategy
were both sound, separately. Together they had a gap.

These are not hypothetical. Misconfigured storage, single-provider
dependencies, and unmonitored gaps between systems are behind a
large share of real breaches and outages. The lesson is that
maintaining CIA in an enterprise is really about testing the whole
thing together, not just each part on its own. Penetration tests,
disaster recovery drills, and reviews that look across team
boundaries are how organisations find the seams before an attacker
or an accident does.

## Part 5: The trade-off question

If there is one question worth arguing about, it is this: which
trade-off is the hardest to get right — availability against
confidentiality, or something else?

Availability against confidentiality is a strong candidate. Making a
system available usually means making data reachable — from more
places, by more people, through more channels. Every new way in is a
new way data can leak. Making data confidential usually means
restricting access, which is exactly what hurts availability when
the people who need the data cannot reach it.

But other trade-offs are just as real. Confidentiality against
integrity: the more people who can verify data, the more people who
can see it. Integrity against availability: the checks that prove
data has not been tampered with take time, and time is availability.
Usability against all three: the most secure system in the world is
useless if nobody can, or will, use it.

There is no single right answer. A hospital will answer differently
from a bank, and a bank will answer differently from a streaming
service. The right answer depends on what the system is for, what
happens when it fails, and who is harmed. What matters is that the
choice is made deliberately, written down, and revisited when the
system changes — because the system always changes, and the trade-off
made two years ago may no longer be the right one.

## How the pieces fit together

| Goal | Main strategies | Real-world example | Main cost |
| --- | --- | --- | --- |
| Confidentiality | Zero trust, segmentation, encryption | Bank core ledger isolated from web systems | Complexity, slower access |
| Integrity | Hashing, signed logs, versioning | Healthcare records hashed and logged | Storage, checking time |
| Availability | Redundancy, multi-region, failover testing | Netflix across regions | Money, data spread wider |
| All three | Testing the whole system together | Disaster drills, penetration tests | Staff time, coordination |

No single strategy covers all three goals. Zero trust helps
confidentiality and, through better logging, integrity — but it does
nothing for availability on its own. Multi-region design helps
availability but makes confidentiality harder. Hashing helps
integrity but adds work to every read and write.

The architect's job is to combine them so the weaknesses of one are
covered by the strengths of another, and to keep testing the
combination, because that is where the failures hide.

## The original post

The text below is the post this write-up is based on, copied
verbatim. It was written for a discussion about maintaining
confidentiality, integrity and availability in enterprise systems.

> In enterprise systems CIA is less about ticking three boxes and more about trade-offs. Push too hard on confidentiality and availability suffers, people start working around the controls. Push too hard on availability and you end up with data spread so wide that confidentiality is hard to control. So the strategy is usually deciding where each one matters most, and then building around that.
>
> One pattern that shows up a lot is zero trust plus segmentation. Instead of trusting anything inside the network, every request is verified, and the network is split so a breach in one part doesn't spread. Banks do this with their core systems, the payment processing sits in its own segment with very strict access, while customer-facing apps live elsewhere with looser but still checked access. If the web layer gets hit, the core ledger is still isolated.
>
> For integrity, the real-world answer is usually hashing and signed logs rather than just access control. Healthcare systems are a good example, patient records get hashed and every access is logged, so even if someone with access changes a record, the change shows up. It's not just about stopping tampering, it's about being able to prove later what happened.
>
> Availability is where cloud architecture changed things. Netflix runs across multiple regions, so if one region goes down the service keeps running from another. That costs a lot, and it makes confidentiality harder because data now lives in more places, which is why they encrypt everything in transit and at rest and keep keys separate.
>
> What I find interesting is that the failures usually come from the seams between these strategies, not from one missing control. A system can have strong encryption and still go down because a single DNS provider fails, or it can be highly available and still leak data through a misconfigured storage bucket. So maintaining CIA in an enterprise is really about testing the whole thing together, not just each part on its own. Does anyone else think the trade-off between availability and confidentiality is the hardest one to get right?

## A note on the discussion

No classmates had posted yet when this post was written — the
discussion board was hidden until the first post was made. So no
reply is included in this write-up. A reply will be added later,
once classmates have posted and there is something to respond to.

## Limitations and honest caveats

- The examples in this write-up — banking segmentation, healthcare
  hashing, Netflix multi-region design — are simplified. Real
  systems in each of those industries are far more detailed, and
  the details matter.
- Zero trust, segmentation and multi-region design are expensive.
  Small organisations often cannot afford the full version, and
  have to choose a simpler set of controls that covers their
  biggest risks first.
- No control is perfect. Encryption can be defeated by a stolen
  key. Hashing can be defeated if the stored hash is also changed.
  Redundancy can be defeated by a failure nobody planned for.
  The goal is not perfection. It is making failure unlikely, and
  making it detectable and recoverable when it happens.
- The trade-offs described here are opinions informed by how real
  systems are built, not a standard. Different organisations,
  with different risks, will quite reasonably make different
  choices.

## Further reading

- NIST SP 800-207, *Zero Trust Architecture* — the standard
  reference for zero trust design.
- NIST SP 800-53, *Security and Privacy Controls for Federal
  Information Systems and Organizations* — a catalogue of
  controls, including access control, audit logging and integrity
  checks.
- MITRE ATT&CK — a catalogue of real attacker techniques, useful
  for thinking about what segmentation and logging are actually
  defending against.
- The Netflix Technology Blog — public write-ups on multi-region
  design and chaos engineering.
