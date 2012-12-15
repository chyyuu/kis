
??? the measured values of shord time/precise/without harassment???

??? how many ommit per hours/days/weeks???

in "Effort Estimation of FLOSS Projexts A Study of the Linux"  30000 commits/day  (6/7 days)
The density distribution of
changes (see Figures 4) confirms that some 60-70% of the changes (added, deleted
or modified lines) always fall in the size cluster of [0-10] lines. more than 95% of such changes are within a [0-100] lines boundary,
very few changes are over and
above 1,000 lines per change, and those are usually coupled to a change of opposite
sign (e.g., a very large commit of added lines coupled to a very large commit of
deleted lines)

??? the source line changed(+/-/m) per commits  (0~10, 10~100, 100~1000)???

??? 1 hour response time for one commit need how many resource of cpus and memorys ???  

???Are Developers Fixing Their Own Bugs??? 

[Are Developers Fixing Their Own Bugs?Tracing Bug-fixing and Bug-seeding Committers]
We have detected that less than 5% of the bugfixing
commits were handled by who first introduced the changes or “seeded” the bug.

The results of this study show, at first, that in 80% of the cases, the bug-fixing activity
involves source code modified by at most two developers.
It also emerges that the developers
fixing the bug are only responsible for 3.5% of the previous modifications to the lines affected.this implies that the other developers making changes to those lines could have made that fix.We conclude by stating that, in most of the cases the bug fixing process in comm-central is not carried out by the same developers than those who seeded the buggy code.

???how and when the buggy source code has been introduced into the repository, how developers deal with this, and which effort needs to be applied and by who???

??? how to Identify bug-fixing and bug-seeding committers???

This algorithm is named as the “SZZ algorithm” and fully detailed in [´Sliwerski et al., 2005]:

there is a 80% of probability that a selected bug-fixing commit was introduced
by at most two developers

??? the percentage of commits belong to bug fixed ???

it has been found that some 50% of all the commits are detected
as fixing bugs