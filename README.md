# Condorcet Ranked Pairs Vote Resolver

This is an implementation of the Condorcet Ranked-Pairs election system.
In a Condorcet election, you have a bunch of candidates. Each voter ranks some or all of the candidates, from best to worst. Ties are okay (although you may wish to disallow ties for the sake of simplicity). A voter does not have to rank every candidate; leaving a candidate off neither helps nor hurts it.

The system then tries to figure out a ranking that matches everybody's preferences as closely as possible.
