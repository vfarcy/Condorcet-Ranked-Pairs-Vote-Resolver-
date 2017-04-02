# Condorcet Ranked Pairs Vote Resolver

This is an implementation of the Condorcet Ranked-Pairs election system.
In a Condorcet election, you have a bunch of candidates. Each voter ranks some or all of the candidates, from best to worst. Ties are okay (although you may wish to disallow ties for the sake of simplicity). A voter does not have to rank every candidate; leaving a candidate off neither helps nor hurts it.

The system then tries to figure out a ranking that matches everybody's preferences as closely as possible.

Create a vote file following this format :

* AAA BBB CCC DDD
# The first line should begin with a *. This defines the list of
# candidates in the contest. (All on one line, separated by whitespace.)

# The remaining lines define ballots -- one line per voter.
  
DDD CCC BBB AAA
# This is a complete ballot. The voter ranked all four candidates,
# from DDD (best) to AAA (worst).

DDD AAA BBB
# This is an incomplete ballot. The voter only ranked three candidates;
# he didn't have any opinion about CCC at all. (This neither helps nor
# hurts CCC.)

DDD AAA/CCC BBB
# This ballot contains a tie. The voter liked DDD best, BBB least,
# and AAA and CCC tied for middle place. This is not the same as the
# previous ballot, because the voter *did* express opinions about CCC;
# he says CCC is better than BBB and worse than DDD.

CCC AAA/DDD/BBB
# This voter likes CCC best, but sees the other candidates as all
# equally bad. This ballot *does* hurt AAA, BBB, and DDD.

AAA
# This voter says AAA is... well, he isn't saying anything about AAA.
# This is legal, but pointless. It doesn't express any preferences
# at all, so it's the same as not voting.

AAA/DDD
# This voter ranked AAA and DDD as equal and ignores the others. This
# is also pointless; it doesn't favor any candidate over another.
