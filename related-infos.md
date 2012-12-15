
1.1 Lifecycle models for long-lived software projects 

The field of software engineering grew from the perception that
the practice of software development is in crisis. Too many projects
are late, over budget, or do not provide the expected functionality.
This is especially problematic with large-scale systems, where hundreds
of millions of dollars may be wasted on failed efforts. Many
such failures may be rooted in a failure to recognize and use perpetual
development as described above (Gilb, 1988; Woodward,
1999; Denning et al., 2008). Basically, projects that attempt to do
too much at once will most probably fail in one way or another
(Hoare, 1981). An ironic outcome of this is that the burst of the hitech
bubble in 2001 led to an improvement in project success rates,
because the reduced budgets led to smaller, more focused projects
(Hayes, 2004). This underscores the importance of the incremental
approach to project development: using increments reduces the
scope being considered at each step, which makes it realizable.

Classical software lifecycle  typically partition the software’s
life into two periods: development and maintenance. The
models focus on the development, further partitioning it into
phases and articulating its iterative nature. The implication of such models is that the full system size is expected to be achieved at the end of development.

Lifecycle models for long-lived software projects

Classical software lifecycle models typically cover the development
from a concept to a delivered software product. A recurring
feature in early models such as the waterfall model and the V
model was the quest for stability. First, one needs to get all the
requirements right. This is then used to formalize comprehensive
specifications. Given the specifications, we can create a design that
satisfies them, and so on. But this scenario is often simply irrelevant
in real life, because the clients can’t articulate all their requirements
in advance, so specifications are never complete, leading to designs
that will have to change when more requirements are discovered

As a result of focusing on the initial development, up to product
delivery, common lifecycle models do not apply to the full life-span
of long-lived software products. In particular, they do not describe
the relationship between successive releases of the product. This
has prompted the development of specialized lifecycle models to
fill this gap.

Doing automated testing in a cloud instead of on individual developers’
machines increases the available compute power by orders
of magnitude. In the past, faster CPUs enabled increased levels
of interactivity in development, such as quick compile-retry cycles.
Cloud-based computation, offering vast numbers of fast CPUs with
plenty of memory, could engender a similar transformation, with
TaaS becoming a seamless extension of a developer’s environment.
If automated testing techniques can be adapted to scale up on cloud
infrastructures, they can yield the order-of-magnitude lower bug
density and higher programmer productivity we seek.


原则：

The whole 'more eyes makes bugs shallow' is nothing but a bad joke.

1 写代码的人对修改自己的代码最容易

2 bug时间越短，约容易修复bug

3 bug越密集，修复bug的难度越高

4 bug存在时间约长，约会引入新的commit,使得bug查找更加困难