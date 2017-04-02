# Condorcet Ranked Pairs Vote Resolver

This is an implementation of the Condorcet Ranked-Pairs election system.
In a Condorcet election, you have a bunch of candidates. Each voter ranks some or all of the candidates, from best to worst. Ties are okay (although you may wish to disallow ties for the sake of simplicity). A voter does not have to rank every candidate; leaving a candidate off neither helps nor hurts it.

The system then tries to figure out a ranking that matches everybody's preferences as closely as possible.

Using The Program

python rpvote.py VOTEFILE

Alternatively, you can use the --complete option: python rpvote.py --complete VOTEFILE

With --complete, any incomplete ballot is assumed to have all the missing candidates tied for last place. In other words, "AAA" is interpreted as "AAA BBB/CCC/DDD". Use this option if you know that every voter has seen every candidate.

When you run the script, you will see two charts and a final tally.

The first chart looks like this:

Margins:

    AAA BBB CCC DDD
AAA   `   1  -2  -3
BBB  -1   `  -3  -3
CCC   2   3   `  -1
DDD   3   3   1   `

For any two candidates, this lists the margin between the people who preferred one and the people who preferred the other. In our example, two voters preferred AAA over BBB, and one preferred the reverse; the difference is 1. So AAA's margin over BBB is 1. (And BBB's margin over AAA is -1.) The margin of DDD over AAA is 3, because three voters preferred DDD over AAA (and none the reverse).

Ties might appear as 0, or (if nobody expressed a preference at all) a blank entry.

The second chart:

Outrankings:

    AAA BBB CCC DDD
AAA   `   +   -   -
BBB   -   `   -   -
CCC   +   +   `   -
DDD   +   +   +   `

This expresses which candidates beat which others, once everything is worked out. In our example, AAA beats BBB, but loses to CCC and to DDD.

Place: Name (wins, losses, unresolved)
  1: DDD (3, 0, 0)
  2: CCC (2, 1, 0)
  3: AAA (1, 2, 0)
  4: BBB (0, 3, 0)
  
This is the final tally. Each entry simply reads off a row of the previous chart; CCC scored two wins (+) and one loss (-), so its standing is 2, 1, and 0. The tally is sorted in order of the final standing. Ties will show up as a " mark in the first column. This code includes a tiebreaker rule -- see below -- but there can still be genuine ties. For example, if nobody votes at all, you'd see this tally:

Place: Name (wins, losses, unresolved)
  1: AAA (0, 0, 3)
  ": BBB (0, 0, 3)
  ": CCC (0, 0, 3)
  ": DDD (0, 0, 3)

This indicates that all four candidates were tied for the first (and only) place.

The Caveats

My modification to Condorcet is to accept incomplete ballots. (All the sample ballots above which list fewer than four candidates are incomplete.) An ideal Condorcet system only accepts complete ballots. If you want to run the election this way, simply reject all incomplete ballots.
Alternatively, you can add missing entries as a tie for last place. (The --complete option does this automatically.) So if a voter offers you "AAA BBB", you would record it as "AAA BBB CCC/DDD". If you do this, be sure to explain that an incomplete ballot does hurt the missing candidates!

My hack is a tiebreaker rule. An election with few voters will tend to produce ties. That is, a pair of candidates will be indeterminate -- neither beats the other according to the Condorcet rules. I resolve these in favor of whichever candidate beat the most other candidates overall. If that doesn't help, I pick whichever candidate lost to the fewest others overall.

The bug is in a particular corner case: when a set of pairs have the same margin, are not contradicted by higher-margin pairs, but contradict each other. My code tries to resolve this, but not in a very smart way.

How It Can Go Wrong

Any election system will produce unintuitive results if you get a screwy pattern of ballots. (This turns out to be mathematically provable.)
Ranked Pairs works well, but you should be aware of what sort of thing confuses it.

Missing candidates:

If a particular candidate is ignored by every single voter, the algorithm doesn't know what to do with it. My tiebreaker rule tends to put it near the bottom of the tally, but that doesn't necessarily make sense. It's better to eliminate it from the contest entirely. (Delete it from the * line in the vote file.)
Partitioned ballots:

Say you're running a story contest. Some of the entries are mysteries, and others are science fiction. But your voting audience is very parochial. The SF fans only vote for the SF stories and the mystery fans vote for the mystery stories. Nobody votes for both.
In this situation, the algorithm has no way to compare one side of the list to the other. It will work out the two tallies, but present them to you interleaved, in a not very meaningful way.

The only way to avoid this is to encourage voters to rank a good sample of the candidates. You don't have to require everybody to rank every candidate. As long as many people try most of the entries, it'll work out.

The minority opinion:

Say candidate XYZ is ignored by almost everybody -- but the few voters who do rank it, put it right at the top. The result: XYZ will come in first place.
This is actually not a mistake. The algorithm counts strong (popular) opinions over weak opinions, but it counts weak opinions over no opinion at all. In this situation, a few people said XYZ was better than every other entry; and nobody went on record as saying that any entry beat XYZ. The algorithm looks at this and thinks, okay, XYZ must be the winner -- nobody will object to that!

If you want not just a winner, but a strong winner, consider using this rule: If a candidate appears on fewer than 5% of the ballots, it is eliminated from the contest.

Allow ties?

This is up to you. It's easier to explain the system if you simply say "rank the candidates from best to worst." But most people will come up with ties, particularly among a large field of candidates.
There's also the case where someone thinks, "I like A best, and B second best; but C, D, and E looked so tedious that I didn't even want to start them." If the voter has a genuinely negative opinion of the latter three, this is best expressed as "A B C/D/E".
