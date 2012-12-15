KIS: regression testing instant service for development on Linux kernel /(large open source software)

Given enough eyeballs, all bugs are shallow?
================================

Abstract
--------------------------------
  The distributed version control system, quick rolling development model, looseld coupled developers and complex hug code base give unprecedented difficulties and pressure to current linux kernel regression testing. This paper presented a preliminary study of characteristic of development, bug, patch and developers in linux kernel, and put forward an viewpoint: instead of enough eyeballs, the automated regression testing should be an instant service for kernel developers. An prototype of automated regression testing tool KIS was implementated and could check regression status of every new commits from thousands of kernel developers in **short time** (<1 hour) and test 30,000 kernels per day using 13 servers (??? 208 CPU cores and 416GB memroy ???). Our insights into Linux kernel developer behaviors and attitudes towards regression testing allow us to make KIS to provide **precise** bug reports, and to improve their usability and effectiveness **without any harassment**. In the linux kernel 3.7 development cycle, KIS provide 63 bug reports which were almost 11% of the total.




1.Introduction
--------------------------------
The kernel which forms the core of the Linux system is the result of one of the largest cooperative software
projects ever attempted. Regular 2-3 month releases deliver updates to Linux users, each with significant
new features, added device support, and improved performance. The rate of change in the kernel is high and
increasing, with between 8,000 and 12,000 patches going into each recent kernel release. These releases each
contain the work of over 1,000 developers representing nearly 200 corporations [Linux Kernel Development How Fast it is Going, Who is Doing It, What They are Doing, and Who is Sponsoring It 2012].  From the statistics in the current linux kernel 3.7 development cycle, 1,271 developers contributed to nearly 12,000 non-merge changesets, nearly 395,000 lines of code were removed, 719,000 lines that were added in 90 days???[Statistics from the 3.7 development cycle [LWN.net]]. But the regression  

图表
http://www.gossamer-threads.com/lists/linux/

+2.6.1-* +patch* 
+2.6.1  +patch* 

+2.6.1-* +patch* +fix*  
+2.6.1 +patch* +fix* 

+3.1-* regression* bug* -patch* -fix*
+3.1 regression* bug* -patch* -fix*

https://groups.google.com/forum/?fromgroups#!searchin/linux.kernel/regression$20OR$20bug$202.6.0

1.2 Crrent status of testing

Because of the rapid development of Internet, there are lots of software systems that are built in a completely different manner, and have general developer/user community. These are not created from scratch in one fell swoop, but rather evolve from humble origins over a long period of time. 


1.3 example, the problems in developing linux kernel
history

The Linux data indicates that the kernel distribution grew by a
factor of 78.6 over 17(1/4) years – an average annual compounded
growth rate of 28.8% (as measured by LoC). Portrayal of how a
software system grows is a central component of the perpetual
development model. “Continued growth” is also Lehman’s 6th law
of software evolution (Lehman and Ramil, 1999). While not one
of the original three laws proposed in 1974, it nevertheless figures
prominently in the research literature. This may perhaps be
attributed to the fact that it is the easiest to measure directly. For
all these reasons, it is of interest to study the growth of Linux in
some detail.

The parallel development that is inherent in the perpetual development
model may also help to cope with radical changes to
the architecture. A project may branch off an exploratory architecture
development branch, that is developed in parallel and
independently from the main development backbone. If the new
architecture succeeds it will eventually be adopted, and other modules
will be ported to adhere to it. This is analogous to redundancy
in biological systems, which allows many mutations with no deleterious
effects on the organism, at the same time immediately
benefiting from advantageous mutations.

Development Process

Linux kernels are release in a 3 month cycle
 * First part is the 2week mergewindow
    * Linus merges new drivers, updates and so on
    * Release of a rc1 kernel marks end of the mergewindow
 * After rc1 is released only fixes are accepted
 * Every week a new release candidate
 * Somewhere after rc7 and rc9 the final kernel is released

It is easy to identify three phases in the Linux kernel’s development
(as reflected in Fig. 2), which can be viewed as variations
on the more abstract and general model. The first is the version 1
kernels, from 1994 to 1996. During this period most of the activity
was in development, and only few maintenance updates were
made to production versions. The second phase, from 1996 to
2004, is characterized by the release of three long-lived production
versions (2.0, 2.2, and 2.4) that were maintained in parallel. Significant
developments were performed between these versions. This
led to big differences between them and long intervals between
their release dates which were extended even further by the protracted
release process itself. Such long intervals contradict the
desired rapid release cycle of open source software. Indeed, in the third phase (kernel version 2.6 since 2004)
production kernels are released regularly every 2–3 months. To
reduce the maintenance effort most of these are not maintained
much beyond the release of the next production version.

analysis of release in linux kernel

we can generalize an idealized release process
that includes the following set of decision points and activities
between them(Fig. 5):

1 Decide to release. The decision at this point in time is to stop
developing new functionality, and prepare to release what has
accumulated so far. Except for version 1.2, this is reflected by a
new pre-release series of kernel updates. The following activity is
one of stabilizing and improving the code, based on internal testing
by the developers. Additional development is done mainly to
fill holes in existing functionality, not to add new functionality,
so the rate of growth is expected to be reduced.

2. Perform the release. This is the actual point of the release itself,
where the new production kernel is released and the series of its
updates is started. The initial updates reflect a period of support
and followup, in which developers continue to stabilize
and improve the code, but now this is based on feedback and
bug-reports from actual users. Again, this may include some
additional development.

3. Fork a new development version. This decision signifies that the
released version is considered stable and usable. It is reflected
in the data by the forking of a new development version, which
will be pursued in parallel to the maintenance of the production
version that has just been released.

4. Discontinue maintenance. The decision to stop supporting a
production version is usually taken only after the subsequent
production version is well established. It does not mean that this
version will immediately cease to be used – only that it will not
be further maintained, so no additional updates will be made.
In Linux the decision to cease maintenance is not necessarily
the final word, as distributors (such as Red Hat or Debian) may
continue to maintain a version that is important to them even
when it is no longer “officially” maintained.


Difference

This model of a release is different from the common software
release life cycle (Wikipedia, 2010 http://en.wikipedia.org/wiki/Software_release_life_cycle). In the common model, the prealpha
phase denotes development, and the alpha phase is testing.

In Linux and other
open-source software distributed on the Internet there is no manufacturing
step, and released software is immediately available.
But more importantly, support by the development team continues
after release. This followup phase, which continues until the
decision to fork a new development version, is missing from the
common model.

The idealized release model discussed above hinges upon the
decision to release.

In the major Linux production versions these
decisions were related to the development of a major feature, such
as multi-platform support and SMP support.However, such developments
took an order of two years to complete, causing major
delays in the release of other lesser features.


With version 2.6 it
was therefore decided to switch to a periodic scheme with a new
release around every 2–3 months. This also reduced the pressure
on developers, because if one release is missed the next one is not
far off.

A faster release cycle was problematic with the idealized release
model, as an underlying notion in that model is that the same people
alternate between development and release activities.With the
growth of Linux this became increasingly untrue.Core developers
became more involved with administrating the kernel versions,
while other developers more commonly contributed complete subsystems
that had been developed externally.At the same time,
a “stable team” was formed to take care of updates to previous
production versions. This enabled a three-way parallelization of
activities:

* Development of new functionality. This is done independently
by many developers in their own environment (importantly, this
is explicitly supported by the git model of distributed version
control, where each developer has his own private copy of the
codebase). Such development may take a long time, and is not
reflected in any way in the Linux kernel releases and updates
until it is merged during a convenient merge window.  早点给这些相对独立的开发者提供testing支持，可以让新功能更快更好地merge 到linux kernel中
* Stabilization and testing. This is orchestrated by the core developers,
notably Linus Torvalds, in cooperation with the developers
who made contributions in the most recent merge window. It is
reflected as updates to the current rc version.
* Maintenance and updates of the previously released version,
reflected in updates to that version.
This parallelization allows the release cycle to be compressed
and completed within about 10 weeks.【fig8】

The life of a new version starts with a
merge window. This is a relatively short period (around two weeks)
where developers are invited to submit their new developments.（These new features are expected to have been reviewed and signed off by subsystem
maintainers, and incorporated in the Linux “next” version, **but this is not
reflected in any official releases.**）
When the merge window ends, a stabilization period (of around
two months) takes place. The updates of this not-yet-released new
version are called “release candidates” and designated by an “rc”
suffix. When the new version is considered stable, it is released. In
parallel, a new merge window is opened for the next version. The
released version is handed over to the stable team, which performs
maintenance updates (bug fixes and security patches) as needed.


In Linux, this refers to the releases of
the major production versions (1.2, 2.0, 2.2, and 2.4), and to the
“third digit” releases of the 2.6 versions starting with 2.6.11. As
we show, these two sets of releases are rather different from each
other.

Note that we do not refer to updates (or “minor releases”) of
existing versions – the third-digit releases before version 2.6.11,
and the fourth-digit releases of 2.6.11 and later. In Linux, such
updates are made when “enough” content accumulates or when
there is a new security patch that needs to be disseminated quickly.
This is a subjective decision made by whoever is responsible for the
version in question.


Importantly, Linux’s development defies common management
theory (Raymond, 2000). At the same time, it does not fit into
any common software lifecycle model. 

we note that Linux
development is a dynamic process in constant flux, and adapts to
overcome problems that occur as the system grows. Thus different
variants of the model apply to different parts of Linux’s evolution(software testing).

we emphasize the implications of continued
development and parallel branches,


All olde model we use this term exclusively to mean the
release of a new production version of the system – an event sometimes
referred to as a “major” release in daily development isn't analyzed by old papers.A notable example is the release of Windows Vista, where many
users elected not to upgrade but rather to keep using Windows XP.


This distinction between maintenance and continued
development, which is central to our model, is missing from
many discussions of maintenance,

Thus, while maintenance does
in fact apply to versions of the product that are in production use,
it is not the main activity but rather done in parallel to continued
development of the backbone. And from a terminology aspect,
maintenance is not a synonym for evolution.
所以，我们需要关注热点（即当前的develop 阶段）

An interesting question that arises from the distinction between
continued development and maintenance is who performs these
activities.
maintain的投入和development的投入是否有一个量化的分析？

Real users doing real work are effectively brought into the development
loop. (Raymond, 2000). 这个观点有错

The user requirements and the system that
solves them co-evolve together

The danger of releasing an unstable version is reduced, because
users upgrade to the new version only gradually, and they have
the previous version as a fallback. 这个观点有错，因为，bug越久存在，越没有人理会
这许这个feature就没有人选择或使用了。    



In the context of open-source projects, maintainers and
developers may be the same people, as developers may have ownership
or at least a feeling of responsibility for their code, and
will therefore maintain it in parallel to performing other development
activities.


Production versions therefore
generally need to be maintained well beyond the release of the
next version. For example, Linux kernel version 2.4, which was first
released in January 2001, was still being maintained in late 2010.


In this paper we focus on the development life cicle of Linux kernel.The choice of Linux is not accidental. Linux is perhaps the bestknown
and most successful open-source project in the world, to
the point of becoming the poster-child of the whole open source
movement. The source code of its full development history is
freely available from http://www.kernel.org and numerous mirrors
worldwide – at present totaling ??? versions(from 1991), ???? commits (from 2002) .

Importantly, Linux’s development defies common management
theory (Raymond, 2000). At the same time, it does not fit into
any common software lifecycle model. This motivates us to suggest
the perpetual development model. This model is descriptive
rather than prescriptive. Its goal is to create a framework for discussing
what is actually being done in the field and why, rather
than attempting to dictate ideals. In particular, we note that Linux
development is a dynamic process in constant flux, and adapts to
overcome problems that occur as the system grows. Thus different
variants of the model apply to different parts of Linux’s evolution.

Data from several recent Linux 2.6 releases is shown in Fig. 9.
Note that there is **no “development version”** , and there are no
updates during the merge window. All development work is done
externally by the developers in their own environment, in parallel
to the release of previous versions. In few cases the first rc
update serves to extend the merge window, but in most cases the
rc updates do not contain any significant growth.

Importantly, the 2.6 releases are time-driven rather than being
content-driven. Merge windows are relatively short, and the time
to stabilize is also limited. If stabilization is not achieved, merged
functionality may be removed and delayed to the next version. This
leads to rapid dissemination of innovations and developments, but
also means that stable versions have a very short lifetime. As a
result, some versions are singled out for “longterm” maintenance,
and updated in parallel to subsequent releases.

bug发展趋势，不过不是独立的，可能与其他代码有联系，而其他代码是频繁变化的，导致bug越老，分析bug在新的代码上是否成立需要花很大的精力，或者这个已经不是bug或被fix掉了。

[Perpetual Development: A Model of the Linux Kernel Life Cycle] 2011

Given that the main changes in the compressed release model
result from merging new functionality during a merge window, one
may expect that the rate of growth will increase and that no code
will ever be removed. This is not the case. Data collected by Greg
Kroah-Hartman from the git repositories of versions 2.6.11 through
2.6.354 shows that thousands of lines are removed in each new
version. However, additions outnumber deletions by an average
factor of 2.12.

2. Our Goal and Challengs
--------------------------------

2.1 Goal

 暂时放在这里
development model

patch model

bug/regressing model

developer/tester model



2.2 Challenges

[KS2012: Kernel build/boot testing]2012
Furthermore, even when a build problem appears in a series of kernel commits but is later fixed before a mainline -rc release, this still creates a problem: developers performing bisects to discover the causes of other kernel bugs will encounter the build failures during the bisection process.

As Fengguang noted, the problem is that it takes some time for these regressions to be detected. By that time, it may be difficult to determine what kernel change caused the problem and who it should be reported to. Many such reports on the kernel mailing list get no response, since it can be hard to diagnose user-reported problems. Furthermore, the developer responsible for the problem may have moved on to other activities and may no longer be "hot" on the details of work that they did quite some time ago. As a result, there is duplicated effort and lost time as the affected developers resolve the problems themselves.

According to Fengguang, these sorts of regressions are an inevitable part of the development process. Even the best of kernel developers may sometimes fail to test for regressions. When such regressions occur, the best way to ensure they are resolved is to quickly and accurately determine the cause of the regression and promptly notify the developer who caused the regression.

【Testing for kernel performance regressions】2012

It is not uncommon for software projects — free or otherwise — to include a set of tests intended to detect regressions before they create problems for users. The kernel lacks such a set of tests. There are some good reasons for this; most kernel problems tend to be associated with a specific device or controller and nobody has anything close to a complete set of relevant hardware. So the kernel depends heavily on early testers to find problems. The development process is also, in the form of the stable trees, designed to collect fixes for problems found after a release and to get them to users quickly.

间隔太久了

Still, there are places where more formalized regression testing could be helpful. Your editor has, over the years, heard a large number of presentations given by large "enterprise" users of Linux. Many of them expressed the same complaint: they upgrade to a new kernel (often skipping several intermediate versions) and find that the performance of their workloads drops considerably. Somewhere over the course of a year or so of kernel development, something got slower and nobody noticed. Finding performance regressions can be hard; they often only show up in workloads that do not exist except behind several layers of obsessive corporate firewalls. But the fact that there is relatively little testing for such regressions going on cannot help.

机器变化了

His results include a set of scheduler tests, consisting of the "starve," "hackbench," "pipetest," and "lmbench" benchmarks. On an Intel Core i7-based system, the results were generally quite good; he noted a regression in 3.0 that was subsequently fixed, and a regression in 3.4 that still exists, but, for the most part, the kernel has held up well (and even improved) for this particular set of benchmarks. At least, until one looks at the results for other processors. On a Pentium 4 system, various regressions came in late in the 2.6.x days, and things got a bit worse again through 3.3. On an AMD Phenom II system, numerous regressions have shown up in various 3.x kernels, with the result that performance as a whole is worse than it was back in 2.6.32.

可能的原因

Mel has a hypothesis for why things may be happening this way: core kernel developers tend to have access to the newest, fanciest processors and are using those systems for their testing. So the code naturally ends up being optimized for those processors, at the expense of the older systems. Arguably that is exactly what should be happening; kernel developers are working on code to run on tomorrow's systems, so that's where their focus should be. But users may not get flashy new hardware quite so quickly; they would undoubtedly appreciate it if their existing systems did not get slower with newer kernels.

He ran the sysbench tool on three different filesystems: ext3, ext4, and xfs. All of them showed some regressions over time, with the 3.1 and 3.2 kernels showing especially bad swapping performance. Thereafter, things started to improve, with the developers' focus on fixing writeback problems almost certainly being a part of that solution. But ext3 is still showing a lot of regressions, while ext4 and xfs have gotten a lot better. The ext3 filesystem is supposed to be in maintenance mode, so it's not surprising that it isn't advancing much. But there are a lot of deployed ext3 systems out there; until their owners feel confident in switching to ext4, it would be good if ext3 performance did not get worse over time.



，当回归测试可以自动执行的时候，系统可以较快地到达一个新的稳定状态，比如一个系统如果有良好的ut基础，那么代码的变更一旦检入代码库当中，就可以触发连续构建工具进行build and test（回归测试），失败的结果将在第一时间通知变更代码的程序员，或者通知配置管理员立即处理有问题的代码，这种just-in-time的处理方式将大大减少bug数目并提高代码质量，同时使团队的合作开发更加顺畅。测试的自动化的程度越高，回归的周期就越短，效果越明显，软件的质量也越高，测试是否自动化也是开发是否敏捷的一个主要标志。
由于敏捷提倡短迭代和频繁发布，因此，自动化的回归测试是必须的，是敏捷的重要标志。

the analysis of model of Linux kernel open source software development




提早测试其实也有问题

【http://lwn.net/Articles/269120/】

The question which was immediately raised was this: how do we deal with big API changes which require changes in multiple subsystems? These changes are already problematic, often requiring maintainers to rework their trees in the middle of the merge window. Trying to integrate such changes earlier, in a separate tree, could bring a new set of problems. There will be a lot of conflicts between patches done before and after the API change, and somebody is going to have to put the pieces back together again. Andrew does some of that now, but the problem is big enough that not even Andrew can solve it all the time. The bidirectional SCSI patches merged for 2.6.25 were held up as an example; that change required coordinated SCSI and block layer patches, and it never was possible to get the whole thing working in -mm.

补丁的困难

人们可能很想通过一系列的补丁来为内核增加一个完整的新基础设施，但直到这个系列的最后一个补丁生效，这个新设施才好用。这种想法应该尽可能的避免；如果这一系列补丁新增了regression，折半问题查找方法会把最后一个补丁视为导致问题罪魁祸首，即使真正的bug发生在别处。

此时还有可能发生的是与其他开发者所完成的工作的冲突，这取决于你的补丁的性质。最坏的情况下，严重的冲突可能导致一些工作被放在次要的位置，而剩下的补 丁可能最终成型并被合并到内核中。其他时间，冲突解决将涉及到与其他开发者一起工作并且可能涉及在源码树间移动补丁以保证所有事情都能按规则地应用。这种 工作很可能是一种痛苦，但你应该知足了：
在linux-next树出现之前，这些冲突往往只是在合并窗口打开期间出现，你必须尽快处理掉这些冲突。而现 在，在合并窗口打开之前开发者可以从容不迫地解决这些冲突。

首先，你的补丁的可见性已经再一次增加了。你可能会收到新的一轮评审意见，这些意见来自于那些之前不曾知道你的补丁的开发者们。你很可能忽略这些意见，因 为已经没有关于代码合并的问题了。但抵制住这种诱惑；对于那些提出问题或建议的开发者来说，你仍然需要保持处于积极相应的状态。

最坏的bug报告类型是regression。如果你的补丁导致了一个regression，你就会发现有许多双令人不舒服的眼睛在注视着 你；regression需要被尽早修复。如果你不情愿或没有能力修复这个regression(并且没有人为你做这件事)，那么在内核稳定期，你的补丁 几乎可以肯定会被移除。因无法修复regression而导致补丁被从内核拉出(pull)，除了否定了之前你为补丁进入主线而做的所有工作之外，还很可 能大大增加你的后续工作被合并的难度。

在处理完若干regressions之后，可能还有另外一些普通bug需要处理。内核稳定期是修正这些bug以及确保你首次出现在某内核主线版本的代码尽 可能的稳定可靠的最好时机。因此，请回答bug报告，并尽可能地修正问题。内核稳定期就是为此而设立的；一旦旧补丁中所有问题都被处理完毕，你就能够开始 创建一些绝妙的新补丁了。


linux-next的作用

[http://lwn.net/Articles/289013/]

Linux-next is a somewhat strange base on which to try to develop, though. It is built anew every day from over 100 subsystem trees, each of which can, itself, change from one day to the next. So linux-next is a moving target, just like the mainline is. But, unlike the mainline, linux-next has no consistent or coherent history. Every day's linux-next tree is a completely new creation with a unique - and transient - history.

 Rebasing completely rewrites the development history, causing the old history to disappear from the tree. So a patch series based on the previous history loses its foundation.

Another interesting aspect of linux-next development involves API changes. The longstanding rule in kernel development is that internal kernel interfaces can be changed if there is a good reason to do so, but that the person making the change is obligated to fix all in-tree code broken by that change. If an API change is introduced into linux-next, though, the developer is simply not able to fix any code which enters linux-next by way of the other subsystem trees. If the developer does get patches into those trees for the API change, they can no longer be built on top of kernels which lack that change - the mainline, for example. API changes have, in other words, become harder to do - a situation which some may see as a good thing.

What all this means is that API changes must be handled through techniques like the creation of backward-compatibility layers; those layers can then be removed a development cycle or two later once the transition is complete. Or changes can be split up and added to individual subsystem trees; that, however, can lead to interesting ordering dependencies between the trees. In some cases, we are seeing 2.6.27 changes being merged into 2.6.26 in stub form as a way of making all of the pieces fit together.

Then, there is the simple matter that developers like to have a stable base upon which to create their code. The linux-next tree, since it contains large amounts of relatively new code, will also contain its share of new bugs. That makes developers, who are often having enough trouble just tracking down their own bugs, somewhat grumpy. Development against the mainline tends to have a lower probability of forcing developers to look for bugs which are not of their own making.


[http://lwn.net/Articles/269225/]

Linux-next is a somewhat strange base on which to try to develop, though. It is built anew every day from over 100 subsystem trees, each of which can, itself, change from one day to the next. So linux-next is a moving target, just like the mainline is. But, unlike the mainline, linux-next has no consistent or coherent history. Every day's linux-next tree is a completely new creation with a unique - and transient - history.
A fully running and successful linux-next won't change outcomes a lot, I
expect.  It will help to pick up on obvious bugs and build errors and
bisection breakages prior to them hitting mainline.  But we fix those
things by -rc1 or -rc2 anyway.  So it's just a time-shift, causing no great
change in the final 2.6.x.

linux-next will do little to solve our (IMO) largest problem: unfixed
regressions and bugs.  otoh it will free up a lot of my time and
hair-tearing, so I can devote more attention to the bugs.  (where
"attention" usually= "sending irritiating emails")


linux-next 的工作过程

【http://lwn.net/Articles/287155/】

The idea behind this tree is relatively simple. Linux-next maintainer Stephen Rothwell keeps a list of trees (maintained with git or quilt) which are intended to be merged in the next development cycle. As of this writing, that list contains 95 trees, all full of patches aimed at 2.6.27. Once a day, Stephen goes through the process of applying these trees to the mainline, one at a time. With each merge, he looks for merge conflicts and build failures. The original plan for linux-next stated that trees causing conflicts or build failures would simply be dropped. In reality, so far, Stephen usually takes the time to figure out the problem; he'll then fix up or drop an individual patch to make everything fit again.

When this process is done, he releases the result as the linux-next tree for the day. Others then grab it and perform build testing on it; some people even boot and run the daily linux-next releases. All this results in a steady stream of problem reports, small fixes, patches moving from one tree to another, and so on - various bits of integration work required to make all of the pieces fit together nicely.

There is an interesting sort of implicit hierarchy in the ordering of the trees. Subsystem trees which are merged early in the process are less likely to run into conflicts than those which come later. When two trees do come into conflict, it's the owner of the later tree - the one which actually shows the conflict - who feels the most pressure to fix things up. The history so far, though, shows that there has been very little in the way of finger-pointing when conflicts arise, as they do almost every day. All of the developers understand that they are working on the same kernel, and they share a common interest in solving problems.

So, thus far, linux-next appears to be functioning as intended. It is serving as an integration point for the next kernel and helping to get many of the merging problems out of the way ahead of time. One aspect of this whole system remains untested, though: the movement of patches from linux-next into the mainline. As things stand now, there is no automatic movement between the trees; instead, maintainers will send their pull requests directly to Linus as always. If Linus refuses to merge certain trees, or if he merges them in an order different from their ordering in linux-next, integration problems could return. In the end, it seems like linux-next will have to drive the final integration process more than is anticipated now, but it will probably take a few development cycles to figure out how to make it all work.

Meanwhile, anybody who is interested in 2.6.27 can, to a great extent, run it now by grabbing linux-next. This tree has clarified one aspect of the development process: the 2-3 month "development cycle" run by Linus is, in fact, just the tip of the kernel development iceberg. It is the final integration and stabilization stage. Linux-next nearly doubles the length of the visible development cycle by assembling the next kernel long before Linus starts working on it. And even linux-next only comes into play toward the end of a patch's life.

Meanwhile, however, linux-next appears to have settled in as a long-term feature of the kernel development landscape. It is serving its purpose as a place to find and resolve integration problems; it has also had the effect of taking much of that integration work off of Andrew Morton's shoulders. And that, in turn, should free him to spend more time trying to get developers to fix all those bugs.

3. Priliminary Design and Implementation
--------------------------------
3.1 Arch and Components

3.2 Workflows

3.3 Optimizations

fast compile for large softs

[Reducing Build Time Through Precompilations for Evolving Large Software]
the longer the build process
(e.g., compilation and linking), the longer developers have
to wait in order to integrate their changes. Such problems
are exacerbated in sync-and-stabilize developments [10],
where program changes are integrated into the code-base
nightly as the product is built.
Concerning the efficiency in a build process, the declaration
redundancies slow down the compilation of individual
compilation unit where they occur; the false dependencies
require extra recompilation of the compilation units that do
not need to include the changed part.
This paper presents a precompilation (i.e. source-tosource
transformation before compilation) approach to the
removal of redundancies inside compilation units and false
dependencies among the files


 1) 目录的 snapshot 技术和去重存储技术，用最小的存储容量来保存每一个历史状态 , 2) 和 p2p 传输类似的分块且增量传输技术, 3）无需人工干预的自动化同步算法。

开发者在kerneld的不同开发阶段有不同的特点，某阶段开发人员多，但要求的测试少，这样可以快速提供简单测试分析给更多的人。某阶段maintainer为主（人少），测试多，可以深入测试一下。但其实这些阶段在时间上有重叠。

3.4 Priliminary Implementation

shell lang,  machine , hwo to run

Identify who to notify

Once you know the subsystem that is causing the issue, you should send a bug report. Some maintainers prefer bugs to be reported via the kernel.org Bugzilla, while others prefer that bugs be reported via the subsystem mailing list.

One of the most common questions you'll be asked when you report a bug is, "Can you reproduce your bug on the latest kernel?"

4. Preliminary Results
--------------------------------

4.1 performance of building kernel

4.2 performance of running kernel

4.3 the number of error every day

4.4 the accurate degree of error source 


5. Discussion and Future Directions
--------------------------------

比较其他FLOSS communities.

6. Reference
--------------------------------
[http://en.wikipedia.org/wiki/Linux_kernel]

Development model


The current development model of the Linux kernel is such that Linus Torvalds makes the releases of new versions, also called the "vanilla" or "mainline" kernels, meaning that they contain the main, generic branch of development. This branch is officially released as a new version approximately every three months, after Torvalds does an initial round of integrating major changes made by all other programmers, and several rounds of bug-fix pre-releases.

In the current scheme, the main branch of development is not a traditional "stable" branch, instead it incorporates all kinds of changes, both the latest features as well as security and bug fixes. For users who do not want to risk updating to new versions containing code that may not be well tested, a separate set of "stable" branches exist, one for each released version, which are meant for people who just want the security and bug fixes, but not a whole new version. These branches are maintained by the stable team (Greg Kroah-Hartman, Chris Wright, maybe others).

Most Linux users use a kernel supplied by their Linux distribution. Some distributions ship the "vanilla" and/or "stable" kernels. However, several Linux distribution vendors (such as Red Hat and Debian) maintain another set of Linux kernel branches which are integrated into their products. These are by and large updated at a slower pace compared to the "vanilla" branch, and they usually include all fixes from the relevant "stable" branch, but at the same time they can also add support for drivers or features which had not been released in the "vanilla" version the distribution vendor started basing their branch from.

The development model for Linux 2.6 was a significant change from the development model for Linux 2.5. Previously there was a stable branch (2.4) where only relatively minor and safe changes were merged, and an unstable branch (2.5), where bigger changes and cleanups were allowed. Both of these branches had been maintained by the same set of people, led by Torvalds. This meant that users would always have a well-tested 2.4 version with the latest security and bug fixes to use, though they would have to wait for the features which went into the 2.5 branch. The downside of this was that the "stable" kernel ended up so far behind that it no longer supported recent hardware and lacked needed features. In the late 2.5.x series kernel some maintainers elected to try and back port their changes to the stable series kernel which resulted in bugs being introduced into the 2.4.x series kernel. The 2.5 branch was then eventually declared stable and renamed to 2.6. But instead of opening an unstable 2.7 branch, the kernel developers decided to continue putting major changes into the 2.6 branch, which would then be released at a pace faster than 2.4.x but slower than 2.5.x. This had the desirable effect of making new features more quickly available and getting more testing of the new code, which was added in smaller batches and easier to test.

As a response to the lack of a stable kernel tree where people could coordinate the collection of bug fixes as such, in December 2005 Adrian Bunk announced that he would keep releasing 2.6.16.y kernels when the stable team moved on to 2.6.17.[91] He also included some driver updates, making the maintenance of the 2.6.16 series very similar to the old rules for maintenance of a stable series such as 2.4.[92] Since then, the "stable team" had been formed, and it would keep updating kernel versions with bug fixes. In October 2008 Adrian Bunk announced that he will maintain 2.6.27 for a few years as a replacement of 2.6.16.[93] The stable team picked up on the idea[94] and as of 2010 they continue to maintain that version and release bug fixes for it, in addition to others.

After the change of the development model with 2.6.x, developers continued to want what one might call an unstable kernel tree, one that changes as rapidly as new patches come in. Andrew Morton decided to repurpose his -mm tree from memory management to serve as the destination for all new and experimental code. In September 2007 Morton decided to stop maintaining this tree.[95] In February 2008, Stephen Rothwell created the linux-next tree to serve as a place where patches aimed to be merged during the next development cycle are gathered.[96][97] Several subsystem maintainers also adopted the suffix -next for trees containing code which is meant to be submitted for inclusion in the next release cycle.

[edit]Maintenance
While Linus Torvalds supervises code changes and releases to the latest kernel versions, he has delegated the maintenance of older versions to other programmers.[98] Major releases as old as 2.0 (officially made obsolete with the kernel 2.2.0 release in January 1999) are maintained as needed, although at a very slow pace.

内核开发过程是如何进行的

内核开发者使用一种松散的基于时间的发布(release)过程，每2到3个月发布一个新内核版本。

每个2.6.x发布版本都是一个主要的内核发布版，其中包含了新特性、内部API变化以及其他更多内容。一个典型的2.6发布可能包括超过10000个变 更以及几十万行代码的改变。因此2.6是Linux内核开发的前沿；内核采用一种滚动开发的模型，持续不断地将重大变化整合进来。

关于每个发行版的补丁合并，大家遵循一个相对简单直接的规则。在每轮内核开发周期的开始，我们说"合并窗口"是打开的。那时，那些被认为已经足够稳定(并 且被开发社区接受)的代码会被合入主线内核。在这期间，内核将以接近每天1000个变化("补丁"或"变更")的速度将一个新开发周期的大量改变(以及所 有重大的变化)合入主线内核。

(顺便说一句，这是值得注意的是合并窗口阶段整合的变化不是凭空而来的；它们早已被收集、测试和提交到阶段树中的。这个过程是如何进行的在后续会有详细介绍)。

合并窗口持续打开约两周时间。在这段时间的末尾，Linus Torvalds会宣布合并窗口关闭并发布这一轮内核版本开发的第一个"rc"版本(译注：Release Candidate，发布候选版)。例如，对于版本号确定为2.6.26的内核来说，合并窗口关闭后发布的版本将称为2.6.26-rc1。-rc1的发 布是一个信号，预示着合并新特性的阶段已经过去了，开发工作进入了稳定内核版本的阶段。

在接下来的6到10个星期里，只有那些修正问题的补丁才应该提交到主线中。偶尔也会有某个特别重要的变化被允许提交到主线，但这种情况极其少见；那些尝试 在合并窗口之外提交新特性的开发者往往会收到一个不友好的对待。作为一般规则，如果你的特性错过了合并窗口，那你最好是等待下一个开发周期。(一个不常见 的例外是那些用于之前不支持的硬件的驱动程序；如果它们没有触及到树内代码，那么它们就不可能导致回退(regression)，在任何时候添加都应该是 安全的。)

随着越来越多的修复进入主线，补丁率将随着时间的推移而下降。Linus大约每周发布一个新的-rc内核版本；在内核被认为足够稳定且最终的2.6.x版本发布之前，一个正常系列的版本号会演进到-rc6和-rc9之间。一旦版本稳定且最终内核版本发布，整个过程又将重新开始。

    

对 regression的理解

开发人员是如何判断何时结束这一轮的开发周期并发布稳定版呢？他们采用的最重要的度量方法是上一个版本的regression列表。Bug虽然是不受欢迎 的，但那些存在于以前版本中的可导致系统崩溃的问题则被认为是更为严重的。因此，那些导致内核回退的补丁将遭受冷遇，并且非常可能在内核稳定化阶段被恢复 到原先状态。

开发者的目标是在稳定版内核发布之前修正所有已知的regressions。但在现实世界中，要想达成这种完美的目标十分困难；这种规模的项目存在太多的 变数。有某一点导致最终发布推迟就会使问题变得更加糟糕；等待下一个合并窗口的改变将会越来越多，并且会在下一个周期导致更多的regession出现。 因此，大多数2.6.x内核发布时只带有少了的已知regressions，然而，但愿这些regressions都不那么严重。

一旦稳定版内核发布，其后续的维护工作将交由"稳定版小组(stable team)"进行，目前这个小组成员包括Greg Kroah-Hartman和 Chris Wright。稳定版小组会不时地发布稳定版内核的更新版本，版本号采用2.6.x.y这种数字样式。如果想要被纳入更新版本，补丁必须(1)修正一个重 要的bug并且(2)已经被合入下一个内核开发版本的主线中了。

一个补丁的生命周期

补丁不会从开发者的键盘直接进入内核主线。相反，开发社区设计了一个稍显复杂的过程(虽然有些非正式)来保证每个补丁都能被评审以确保质量，且每个补丁都 实现了一个对内核主线有吸引力的改变。对于一些较小的修正来说，这个过程执行地很快，但对于较大的且有争议的改变来说，这个过程可能会持续数年。许多开发 人员所遭遇的挫折都是来自于缺乏对这一过程的理解或尝试绕开这一过程。

补丁的特征

补丁必须是为某一个特定的内核版本而准备的。通常，补丁应该基于linus的git树中的当前主线版本。

只有最简单的改变才应该以一个单独的补丁形式提供；其他所有补丁都应该由一系列合理的改变组成。

每个逻辑上独立的改变都应该以一个单独的补丁提供。开发者只对离散的、自完备的改变感兴趣，而不是你提供的这些改变的路径信息。这些改变可小("为这个结构体增加一个字段")可大(例如，增加一个重要的新驱动程序)，但它们都应该是小概念的并且可用一行文字描述的。每个补丁所带来的改变都应该可以被独立评审和独立验证的。

每个补丁都应该生产出一个可以正确编译和运行的内核；即使你的补丁序列在中间被打断，结果仍然应该是一个可工作的内核。部分应用一个补丁序列是一种常见的情况，尤其是当"git bisect"工具被用于查找regression时；如果补丁序列被打断的结果是一个损坏的内核，则会导致那些参与追查内核问题的开发者和用户的工作更加困难。


人们可能很想通过一系列的补丁来为内核增加一个完整的新基础设施，但直到这个系列的最后一个补丁生效，这个新设施才好用。这种想法应该尽可能的避免；如果这一系列补丁新增了regression，折半问题查找方法会把最后一个补丁视为导致问题罪魁祸首，即使真正的bug发生在别处。所以无论何时，添加了新代码的补丁都应该使得那些代码立即可用。


some tools


In FLOSSMetrics, the information is retrieved automatically, thanks to the Blackbird 4 system
developed for it, which integrates some mining tools such as:
 CVSAnalY: stores in a database (MySQL or SQLite) the parsing of a log file from several
SCMs such as CVS, Subversion and Git.
 Mailing List Stats: parses information from mailing lists in mbox format and stores it in a
MySQL database.
 Bicho: retrieves information from Bugzilla BTS (so far, it works with KDE, GNOME and
Apache communities) and also the SourceForge tracker and stores it in a MySQL database.
 CMetrics, CCCC, PyMetrics, PerlMetrics: offer complexity metrics for several programming
languages.
 SLOCCOunt: developed by David Wheeler, provides basic information related to number
of SLOC and a basic COCOMO model.




二、软件测试分类

  黑盒测试与白盒测试——按是否查看源代码划分
          
黑盒测试：把被测的软件看做是一个黑盒子，不关心盒子里面的结构是什么样子的，只关心软件的输入数据和输出结果。
          
白盒测试：把盒子盖打开，去研究里面的源代码和程序结构，查看程序的源代码具体是怎么实现的。

静态测试与动态测试：——按是否运行程序划分
          静态测试：static testing 不实际运行被测软件，而只是静态的检查程序代码（包括检查注释），界面或者文档中可能存在的错误的过程。
          动态测试：dynamic testing 实际运行被测程序，输入相应的测试数据，检查实际输出结果和预期结果是否相符。
         判断一个测试属于动态测试还是静态测试，唯一的标准是看是否运行程序。

单元测试、集成测试、系统测试、验收测试：——按阶段划分
          时间比例1:2:4:2
          
单元测试：unit testing 对软件中的最小可测单元进行检查和验证（单元是指认为规定的最小被测功能模块）
                          依据：源程序，《详细设计文档》
                          一般步骤：1.编译运行程序
                                           2.静态测试（检查代码是否符合规范）
                                           3.动态测试
                          桩模块和驱动模块
                                   桩模块：stub 模拟被测模块所调用的模块
                                    驱动模块：driver 用来接收测试数据，启动被测模块并输出结果
          
集成测试：integration testing 单元测试的下一阶段，将通过测试的单元模块组装成系统或子系统，再进行测试，重点测试不同模块的接口，检查各个单元模块结合到一起能否协同配合，正常运行。
                         依据：单元测试的模块以及《概要设计文档》
          
系统测试：system testing 将整个软件系统看做一个整体进行测试，包括对功能，性能，以及软件所运行的软硬件环境进行测试。
                          需要花大量时间和精力去完成的，也是软件交给用户进行验收测试的最后一道关卡。
                         依据：《系统需求规格说明书》
           
验收测试：acceptance testing 系统测试的后期，以用户测试为主，或有测试人员等质量保障人员共同参与的测试。
                              分为 a（a fa）测试 【由用户，测试人员，开发人员等共同参与的内部测试】和 b (be ta)测试【内测后的公测，完全交给最终用户的测试】

功能测试和性能测试：——属于黑盒测试
         
 功能测试：function testing  检查实际软件的功能是否符合用户的需求
                         功能测试又可以细分为很多种：逻辑功能测试logic function testing，界面测试UI testing，易用性测试usability，安装测试installation，兼容性测   试compatibility等。
        
  性能测试：performance testing 一般要使用自动化测试工具。软件性能主要包括时间性能和空间性能。
                           性能测试分为：一般性能测试，稳定性测试，负载测试，压力测试
                                   一般性能测试：让被测系统在正常的软硬件环境下运行，不向其施加任何压力的性能测试。
                                   稳定性测试：reliability 也叫做可靠性测试。连续运行被测系统，检查系统运行时的稳定程度。
                                   负载测试：load 让被测系统在其能忍受的压力的极限范围之内连续运行，来测试系统的稳定性。
                                   压力测试：stress  持续不断的给被测系统增加压力，只要将被测系统压垮为止，用来测试系统所能承受的最大压力。

回归测试，冒烟测试，随机测试：
          
回归测试：regression 对软件的新的版本测试时，重复执行上一个版本测试时的用例。

          
冒烟测试：smoke 是指对一个新版本进行系统大规模的测试之前，先验证一下软件的基本功能是否实现，是否具备可测性。
          
随机测试：random 测试中所有的输入数据都是随机生成的，其目的是模拟用户的真是操作，并发现一些边缘性的错误

三、软件测试基本原则
        1.zero bug 和good enough原则
               zero bug ： 软件没有任何的错误
               good enough：只要软件达到一定的质量要求，就可以停止测试了。（过分的测试浪费资源）
          2.不要试图穷举测试
          3.开发人员不能既是运动员又是裁判
          4.软件测试要尽早执行
          5.软件测试应该追溯需求
          6.缺陷的二八定理
               一般情况下，软件80%的缺陷集中在20%的模块
          7.缺陷具有免疫性



【Tracking of Linux Kernel Regressions】

In recent years the tracking of kernel regressions has become an important part of the kernel development
process for two basic reasons. First, it tells developers which bugs should be taken care of rst so that they
know what to focus on. Second, it gives Linus an idea about how many things should ideally be xed before
the next major release of the kernel so that the users for whom the previous kernels worked correctly aren't
disappointed.

It is done in such a way that the entire history record associated with it is preserved in the kernel Bugzilla
and can be extracted from there at any time. Among other things, this allows one to produce statistics similar
to the ones presented in the previous sections and to get some insight into the testers' and developers' activities
related to the listed regressions.


[1] Linux: Releasing With Known Regressions (http://kerneltrap.org/node/8110).
[2] Linux: Tracking Regressions (http://kerneltrap.org/node/8241).
[3] Radioactive decay (http://en.wikipedia.org/wiki/Radioactive_decay).


【http://kerneltrap.org/node/8110】 in 2007 
需要仔细阅读
There is a conflict between Linus trying to release kernels every
2 months and releasing with few regressions.

(I've said this before, but I'll say it again: one thing that would 
already make bugzilla better is to just always drop any bug reports that 
are more than a week old and haven't been touched. It wouldn't need *much* 
touching, but if a reporter cannot be bothered to say "still true with 
current snapshot" once a week, then it shouldn't be seen as being somehow 
up to those scare resources we call "developers" to have to go through 
it).

And almost all hard bugs are about hardware interactions. Drivers. Big 
iron. Things like that - ie unlike something like a compiler, you can 
seldom say "this test-case crashes". Yes, that does happen for the kernel 
too, but those are the *easy* bugs. Those generally get fixed in a day or 
two.


[KS2011: Development process issues]

Dave Airlie complained about the number of patches that break the kernel and make bisection of problems during the merge window impossible. Linus acknowledges the problem, saying that he wishes every tree that feeds into the kernel would be bisectable, but that is never going to happen.

That is especially important for problem reporters who are not kernel developers and who cannot be expected to cope with kernels that explode in the middle of a bisection series. 

[KS2012: Improving development processes: linux-next [LWN.net]]

In response to the question of how much work was required to maintain linux-next, the maintainer, Stephen Rothwell, said it required between four and ten hours per day, depending on the stage in the kernel-release cycle. In the end



[KS2012: Improving development processes: linux-next [LWN.net]]
need 5MB more memory
such as kernel panics will generate a call trace that includes source file names and line numbers: 

    Call to panic() with the patch set
    ----------------------------------
    Call Trace:
     [<ffffffff815f3003>] panic+0xbd/0x14 panic.c:111
     [<ffffffff815f31f4>] ? printk+0x68/0xd printk.c:765
     [<ffffffffa0000175>] panic_write+0x25/0x30 [test_panic] test_panic.c:189
     [<ffffffff8118aa96>] proc_file_write+0x76/0x21 generic.c:226
     [<ffffffff8118aa20>] ? __proc_create+0x130/0x21 generic.c:211
     [<ffffffff81185678>] proc_reg_write+0x88/0x21 inode.c:218
     [<ffffffff81125718>] vfs_write+0xc8/0x20 read_write.c:435
     [<ffffffff811258d1>] sys_write+0x51/0x19 read_write.c:457
     [<ffffffff815f84d9>] ia32_do_call+0x13/0xc ia32entry.S:427

helpfull for git bisection

The improved call-tracing information that is provided by these patches would undoubtedly make life somewhat easier for diagnosing the causes of some kernel crashes. However, there is a cost: the memory footprint of the resulting kernel is much larger. During the session, a figure of 20 MB was mentioned,although in a mail that he later sent to the kernel summit discussion list, Jason clarified that the figure was more like 10 MB. 

Linus then weighed in to argue against the proposal altogether. In his view, kernel panics are a small enough part of user bug reports that the cost of this approach is unwarranted; an overhead of something like 1 MB for the increase in memory footprint would be more reasonable, he thought. Linus further opined that one can, with some effort, obtain similar traceback information by loading the kernel into GDB. 

Although Jason's proposed patches provide some helpful debugging functionality, the approach met enough negative response that it seems unlikely to be merged in anything like its current form. However, Jason may not be ready to completely give up on the idea yet. In his mail sent soon after the session, he hypothesized about some modifications to his approach that might bring the memory footprint of his feature down to something on the order of 5MB, as well as other approaches that could be employed so that the end user had greater control over when and if this feature was deployed for a running kernel. Thus, it may be that we'll see this idea reappear in a different form at a later date.

[KS2012: Stable kernel management [LWN.net]]

A few people wanted to understand more clearly the criteria that determine whether a patch should be sent for the stable series, and others noted that there seemed to be some latitude as to what Greg considered to be an acceptable patch


 the summary rationale for stable: if the patch would be of interest for distributions aiming to produce a stable kernel for a distribution release, then that patch should be submitted to stable. 

They are regressions under some specific workload that a user has, or with specific hardware (or combinations of hardware) that a user has.
As a result, it's impossible for any single test project to have comprehensive coverage.

The kernel regression tracker (and assistants) are not people running the tests, they are people working with the users who run into problems, helping those users identify the relevant factors of their environment/workload, identifying where the problem started, helping get the report in front of the appropriate maintainer, and then tracking it to keep it from getting lost (and hopefully the user doesn't disappear in the middle of all this), with an additional task of trying to combine duplicate reports (which gains reliability in terms of the user reporting)

If it's a workload related problem, once a problem workload can be simulated, the maintainer/developer may be able to go off and work on it without needing the user to test it all the time, but the user is still needed to test the resulting fix because the simulated workload may not match the real workload as closely as everyone thinks.

If it's a hardware related problem, it requires someone with the appropriate hardware to test the result (and if it's a combination of hardware it's even worse), the maintainers and developers cannot have every variant of hardware, so it's impossible for them to test, and so when they think they have the fix, they will again need to go back to the user to validate the fix.

[KS2008: Kernel quality and release process]
Rafael also noted that some regressions attract no debugging effort at all; it seems that nobody is interested in working on them. It can be disheartening for users to hear nothing about a reported regression at all

He also noted that regressions which have been bisected (to identify the change which first caused the problem to happen) tend to get fixed much more quickly.

The data from the bisection is undoubtedly useful, but the real benefit probably comes from fingering the guilty party, who then feels the need to get a fix in place.


Another thing Rafael pointed out is that we have a small core of dedicated testers; most of our regressions are reported by a small, recurring group of people. Perhaps we could recruit some of those people to help with the management of bugs. They could track reports, get more information from users, and harass maintainers to get fixes in place. These people have already shown a certain amount of dedication; giving them this kind of role would let them expand the help they are able to give to the kernel community.


[KS2009: Regressions]
Looking at things that way, Rafael says, regressions have a half life of about 17 days, and a mean lifetime of 24 days.

There is an important qualification to bear in mind here, though: these plots only show regressions which have been fixed. Over the year or so surveyed, 858 regressions were reported, but only 738 of those were reported to be fixed. So, says Rafael, the full picture has to include a second type of regression which is harder to find and to fix.

What would really help, it was agreed, is more testers - hardly a new or novel conclusion. 


Perhaps if more people would run linux-next, more bugs would be found (and fixed) early. Linux-next is hard to test, though, and hard to debug. It changes radically from one day to the next, and it is not bisectable.

Andrew Morton thought that developers should be testing their code in linux-next as a way of finding bugs caused by interactions with other changes. That is a hard sell, though; working with linux-next can force developers to contend with bugs from completely unrelated changes. Alan Cox noted that linux-next is often more reliable than the -rc1 releases.    

There was some talk about how long it can take for regression fixes to get into the mainline. Often these fixes will set in maintainer trees for some time. Might there be a way to get them in more quickly? As it turns out, a number of subsystem maintainers feel that these fixes should age for a while in a testing tree, lest they introduce other problems. So that situation is not likely to change much.


We *have* that. The problem is that users tend to have stranger hardware 
and stranger configurations than anything that we can actually *test*: 
this is as opposed to Ingo's randconfig does-it-boot testing


[KS2010: Regressions]

How much better is not clear, though. According to Rafael, the mean lifetime of a reported regression is "approximately 24.5 days." That number has not really changed over the last two years.

Where are the regressions being found? The direct rendering subsystem is by far the biggest offender, with the Intel-specific DRI code, on its own, being at the top of the list. After that are the x86 architecture, the filesystem code, and the network subsystem. This information led to the fairly obvious conclusion that regressions tend to be found in code which (1) is actually used, and (2) is under relatively heavy development. Rafael also noted that code which deals closely with hardware tends to be more prone to regressions than core kernel code.

Rafael took some time to complain about the state of the kernel bugzilla system which, it seems, is essentially unmaintained at the moment. Sometimes it just doesn't work, it doesn't track information he would like to have, an account is required for anybody who would like to be on the Cc list, etc. So it seems that there is an opening available for somebody who would like to volunteer to improve the kernel bugzilla; volunteers were notably absent from the room, though.

【KS2011: Regressions】
Rafael Wysocki's talk on regression tracking has become a kernel summit tradition. The overall picture remains quite stable; most kernels have 20-30 outstanding regressions one full cycle after their release. The number of short-term regressions seems to have fallen a bit in recent times; Rafael chalked that up to the fact that the graphics drivers have finally stabilized somewhat.The number of short-term regressions seems to have fallen a bit in recent times


The numbers showed that, as a whole, regressions are not being found as quickly as they were a year or so ago. It was asked: is that good or bad? Perhaps it is bad; we're just not as good at finding bugs as we once were. Linus, instead, said that it's a sign that the bugs we are seeing are becoming more subtle; they don't affect as many users, and, thus, take more time to find. Rafael thought it may mean that we were getting more testing of post-release kernels than before, but he wasn't sure. He was convinced that the trend meant something, but couldn't say what.

[A more detailed look at kernel regressions]

定义

A regression is a user-visible change in the behavior of the kernel between two releases.

A program that was working on one kernel version and then suddenly stops working on a newer version has detected a kernel regression. 


Regressions are added to bugzilla one week after they are reported by email, if they haven't been fixed the interim. That's a change from earlier practices to save Rutecki's time as well as to reduce unhelpful noise. Bugzilla entries are linked to fixes as they become available. The bug state is changed to "resolved" once a patch is available and "closed" once Torvalds merges the fix into the mainline.


峰光关注了mail中提到的bug reports吗