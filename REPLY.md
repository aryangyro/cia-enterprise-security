# Reply to a classmate — Discussion 3

This is the reply I posted on November 11, 2026, to Chioma Chineyenwa Iroka's post "CIA Triad Maintenance in Enterprise DevSecOps" (posted November 11, 2026). Her post walked through each part of CIA with a control and a real platform as an example: Azure for confidentiality, GitHub version control for integrity, and AWS multi-availability-zone design for availability.

## The reply (posted verbatim)

Hi Chioma, I liked how you tied each part of CIA to a real platform, the Azure, GitHub and AWS examples made it easy to follow. One thing I kept thinking about though is that those controls don't always sit comfortably together in a real enterprise. Like, the AWS multi-zone setup you mentioned is great for availability, but every extra copy of the data is also one more place it has to be protected, so availability and confidentiality can pull in opposite directions. Same with integrity, signed commits and code reviews are solid, but in a team shipping every day they can slow things down enough that people start looking for shortcuts around them.

So I wonder if the hard part isn't picking the right control for each pillar, but deciding what to give up when two of them conflict. In your example, if a team had to choose between faster recovery and tighter access on those backups, which way do you think most enterprises actually go, and is that the right call?

## Why this angle

My original post was about CIA as trade-offs in enterprise systems, so the reply stays on that same idea but pushes it further: instead of listing one control per pillar, it asks what happens when the controls fight each other. The AWS example is a good one for that, because redundancy helps availability and hurts confidentiality at the same time.
