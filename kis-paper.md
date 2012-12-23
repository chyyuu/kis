KIS: regression testing instant service for development on Linux kernel /(large open source software)
================================

OR

Given enough eyeballs, all bugs are shallow?
================================

Abstract
--------------------------------
  The distributed version control system, quick rolling development model, looseld coupled developers and complex huge code base give unprecedented difficulties and pressure to current linux kernel regression testing. This paper presented a preliminary study of characteristic of development, bug, patch and developers in linux kernel, and put forward an viewpoint: instead of enough eyeballs, the automated regression testing should be an instant service for kernel developers. An prototype of automated regression testing tool KIS was implementated and could check regression status of every new commits from thousands of kernel developers in **short time** (<1 hour) and test 30,000 kernels per day using 13 servers (??? 208 CPU cores and 416GB memroy ???). Our insights into Linux kernel developer behaviors and attitudes towards regression testing allow us to make KIS to provide **precise** bug reports, and to improve their usability and effectiveness **without any harassment**. In the linux kernel 3.7 development cycle, KIS provide 63 bug reports which were almost 11% of the total.




1.Introduction
--------------------------------

In 1997, Eric Steven Raymond wrote a famous paper "The Cathedral and the Bazaar", and he put forward famous "Linus's Law", less formally, "Given enough eyeballs, all bugs are shallow.". Linus was directly aiming to maximize the number of person-hours thrown at debugging and development, even at the
possible cost of instability in the code and user-base burnout if any serious bug proved intractable.The linux kernel hackers beleive that given a large enough beta-tester and co-developer base, almost every problem will be characterized quickly and the fix obvious to someone. [The Cathedral and the Bazaar paper]. 

From the statistic from the linux kernel source code repository in 8~10 years, we can see the rate of change in the kernel is high and increasing, with between 8,000 and 12,000 changesets(patchs) going into each recent kernel release. These releases each contain the work of over 1,000 developers representing nearly 200 corporations [Linux Kernel Development 2012].  There are more new or improved subsystems were added, more regressions or bugs were fixed, more developers joined the active kernel hacker group. Almost all these statistical data from  industry or academiia[????]  rveal the linux kernel will be more stable in current develop mode and the plentiful diligent top hackers??.  

An important signal of this paper is that archor kernel stability's hope on enough eyeballs of kernel developers/testers in open source communication are wrong. We urge the kernl development community to give
them their due share of attention. From our investigate, we consider there are unprecedented bug/regression-finding/bug-fixing difficulties and pressure to current and nearly future development of linux kernel, and the main factors make "Linus's Law" become invalid are the the change of major parts of bug, distributed version control system, quick rolling development model, looseld coupled developers with Novelty Seeking psychology,   and complex huge code base. 

From our investigate on bugzilla.kernel.org, we found current bug detection technologies and tools can effectively reduce the diagnosis and resolution time of memory bugs (such as NULL pointer, memory leak, etc.). However, we also found that there still exist many simple memory bugs such as NULL pointer dereferences and uninitialized memory reads, indicating memory bug detection tools have not been used at their full capacity.  Concurrency bugs account for a small portion of bug reports, probably because they are underreported. But semantic bugs(configure bug, app-special bug ) are the dominant root causes, as they are application specific and difficult to fix.

From 2005, kernel development has been based on git. And kernel development has been using the open source paradigm that allows programmers from the Internet to read, redistribute, and modify the source code. For quality control, Linus usually allow only a small set of experienced developers (containers or gatekeepers) to check code changes into the main branch of the software.This development model based on git is a double-edge sword. On the one hand, git allow developer spend more time developing in private local software repository, so more developers can work in the different or same part of linux kernel parallelly. On the other hand, one hacker's changesets in kernel carry bugs and regressions which will influence the other parts developed by other hackers. Judging by the pains that a typical kernel developer encountered in the daily hacking.  An interesting question is whether this development model takes a much shorter time to fix kernel regressions and other kernel bugs?


The Linux kernel keeps growing in size over time as more hardware is supported and new features are added. The kernel 3.2 grow in size of  15 million lines since its first release - a mere 10,000 lines of code - came out
in 1991. although the number of kernel developer steadily increased, the increasing rate of kernel code is higher than that of developers. No one can understand all current kernel codes, and every kernel developer only understand smaller and smaller proportion of kernel following the ever-increasing size of kernel. Therefore the bugs which crosss the territories of kernel developers will be hard to find and fix. 

Instead of needing more testers, we consider scalable automated kernel testing tool should help to resolve above-mentioned difficuties. By using  appropriate scale of backend computers, the instant service of automative regression testing could be provided to rapid detect potential bugs and send bug reports to kernel developer after every git commits from all linux kernel git trees. As far as we know, it looks like a huge plan without previous attempt.   

Current researchers[] proposed a lot of new method to find kernel bugs and get inspiring results. But researchers and kernel developers take care different type of kernel bugs. Researcher dig working kernel bugs. A working kernel bug is an error, flaw, mistake, failure, or fault in a computer program or system that produces an incorrect or unexpected result, or causes it to behave in unintended ways in released linux kernel. Kernel developers were busy in kernel development, and they forcused on kernel developing kernel bugs. A developing kernel bug is a kernel regression. This special type of kernel bug comes from [http://en.wikipedia.org/wiki/Software_regression] the new features or new bug fixes and  makes a feature stop functioning as intended, makes system  performs slowly or uses more memory in the developing linux kernel in kernel development cycle. This important difference makes current bug-finding research results could not directly used to help kernel developer to find and fix kernel regressoin in development time. We need new method to resolve this problem. 
  
As a first step, this paper outlines how we primary study, design and implementation the automative regression testing instant service prototype for development on Linux kernel. Two challenges make atuomative testing serverice difficult. The first is hwo to quantitative analyze the current characteristics and relations of the distributed version control system, development model, kernel developers and kernel source code. And give the statistical evidences to prove the "Linus's Law" is broken.

The second challenge is hwo to deisgn and implementate an instant service of automative regression testing. There are three requirements for this service: instant testing process, accurate bug report, wide testing coverage. Build errors are often regarded as trivial ones. However we obviously lack an
effective way to prevent many of them from leaking into Linus' tree, not to
mention the linux-next tree, where it hurts many -mm developers. Runtime oopses are more challenging. As you may discover in LKML, lots of the bug reports are simply ignored, because it's often really hard to track down
user reported problems. Hard-to-reproduce bugs are virtually not fixable; bugs
for old kernels are not cared by upstream developers; regressions not bisected
down to one particular commit could kill quite some brain cells, and there is
the question "who is to blame for this bug?". To be frank, the only way
to guarantee the prompt fix of a bug is to explicitly tell the developer: hi,
your XXX commit triggered this YYY bug.  
 
To address these challenges, we first collect data and information from Linux Kernel Mail List(LKML), Linux kernel Git Trees (LKGT) in 8~10 years. From the comprehensive achives in LKML and LKGT, only only the source size/patchs/fixed bugs/number of developers could be collected, the effort and activity estimation on the kernel developer and testers also coue be analyzed. These static data and analysis results support our viewpoints. 

Then we designed and implementated an prototype of automated regression testing service KIS. To improve the speed of testing, we use cacheing technology to buffer the compilled/checked files. We also use memory disk and multicore optimization to fasting  tesing procedure. To find more bugs, we use static and dynamic tools to check the every kernel. To automative find the bug producers, we design tool to analyze the human readable output from GCC, analysis tools and OOPS log messages. Now KIS could check regression status of every new commits from thousands of kernel developers in 1 hour and test 30,000 kernels per day using 13 servers. In the linux kernel 3.7 development cycle, KIS provide 63 bug reports which were almost 11% of the total.     

So far, this research has achieved two main contributions. First, through an initial quantitative study of linux kernel development process, we show that they are a real threat on linux kernel quality and stability. Second, we have designed and implemented a preliminary prototype of automated regression testing service KIS. To the best of our knowledge, KIS is the first automated regression testing service for linux kernel developer and has already been used in Linux kernel 3.7 development. Overall, our results not only provide software testing development with a new understanding about software regressions, but also enlighten new bug detection and recovery techniques, suggesting where effort should be put into.


2. understanding kernel regression
--------------------------------

2.1  source of regressions
--------------------------------

To discover which factors will result in possible regressons in kernel development cycle, we collect and study the data from the main sites which kernel developer provide development and communication information. The main sites include git.kernel.org (Linux Kernel Git Repositories, LKGR), bugzilla.kernel.org(Linux Kernel Bug Tracker, LKBT), The Linux kernel mailing list (LKML).

LKGR is the main kernel source code repositories, and contains 300+ important git trees and about 100~1000 patchs applied in every days kernel development. From LKGR, we can get every commits/patchs in kernel development cycle, and analyze the change rates difference between new features and regression fixes, the number and lifetime of regressions, etc. 
 
LKBT is the main kernel bug reports database.The whole kernel bug databases contain 20,000+ of bug
reports. According to root causes, bugs can be classified into three disjoint categories, Memory, Concurrency, and Semantic.To ensure correct classification, we only study fixed runtime bugs whose root causes
can be identified from reports because unfixed bugs may be invalid and root causes described in the reports can be wrong. In this way, we randomly select 548 fixed bug reports from the LKBT.  From LKBT, we can analyze the lifetime, distribution, etc. of bugs.

[http://en.wikipedia.org/wiki/Linux_kernel_mailing_list]LKML is the main electronic mailing list for Linux kernel development, where the majority of the announcements, discussions, debates, and flame wars over the kernel take place. Many other mailing lists exist to discuss the different subsystems and ports of the Linux kernel, but LKML is the principal communication channel among Linux kernel developers.[4] It is a very high volume list, usually receiving about 500 messages each day. Linux utilizes a workflow governed by LKML,[5] which is the Bazaar where kernel development takes place. In his book Linux Kernel Development, Robert Love notes:[3] If the Linux kernel community had to exist somewhere physically, it would call the Linux Kernel Mailing List home. From LKML, we can analyze the extent of activity and interesting point,etc.

2.2 analysis of regressions
------------------------------
2.2.1 the model of kernel developement
---------------------
First, we should study the details of kernel development and the organization of kernel developers. When a kernel developer want to add  new features or functions to mainline kernel, he should give the patchs and description in LKML. If the maintainer has close relation with this patch think the idea and code is acceptable, he will put the patch in his sub-system tree. At the beginning of each development cycle, the "merge window" is said to be open. Developer and maintainer will try to put the new patch into the mainline kernel tree(in LKGR). At that time, new patch which is deemed to be sufficiently stable (and which is accepted by the kernel development community) is merged into the mainline kernel. The bulk of changes for a new development cycle (and all of the major changes) will be merged during this time.

The merge window lasts for two weeks. At the end of this time, Linus Torvalds will declare that the window is closed and release the first of the "rc" kernels. For the kernel which is destined to be 3.7, for example, the release which happens at the end of the merge window will be called 3.7-rc1. The -rc1 release is the signal that the time to merge new features has passed, and that the time to stabilize the next kernel has begun.

Over the next six to ten weeks, only patches which fix regressions should be submitted to the mainline. The bug report and fixing information will be stored in LKML and LKBT. As fixes make their way into the mainline, the patch commits will be recorded in LKGR. Linus releases new -rc kernels about once a week; a normal series will get up to somewhere between -rc6 and -rc9 before the kernel is considered to be sufficiently stable and the final 3.7 release is made. At that point the whole process starts over again.

From the model of kernel development, we can analyze the organization of kernel developers. The shape of kernel developers like a pyamid. The peak point is Linus, the second level includes senior maintainers, suach as Andrew Morton, etc, the third level includes subsystem maintianers, the last level developers are new featuren providers, driver providers, kernel testers, etc. The most part of kernel developers were in low level and focused on provding new features ,and only a few were interested in kernel testing. Kernel testing requires (a) that the testers are able, diligent, and motivated enough to do boring test works from day to day; (b) that the tester can easily repeat the errors and give the detailed accurate information to the right responsible developer.[KS2012: The future of kernel regression tracking]. If someone have above ability, they will choose to be a kernel developer provding new features with great honour. The high level maintainers have to do a lot of merge work alone with testing works, but they are too busy to do sufficient testing works. 

2.2.2 lifetime and state of kernel regressions
[need a picture]
Second, we should study the lifetime of kernel regressions in kernel development. From the  kernel development process, we can see the different stage and duration of the kernel regressions.

 1. the birth of kernel regressions are derived from patches with new features and funcitons which is described in LKML; (uncertian weeks)
 1. then the kernel regressions will follow the new feature patches to exist in the git trees maintained by the close related sub-system maintainer; (uncertian weeks)
 1. after the continuous improving of the patches, the kernel regressions will follow  patches to exist in the linux-next git tree; (8~12 weeks)
 1. In beginning of the next version of kernel development , the kernel paches with the kernel regressions will be choosed by Linus to exist in mainline git tree. (2 weeks)
 1. In mainline improving stage of the next version of kernel development, the kernel paches with the kernel regressions will be updated many times; (6~10 weeks)
 1. After release of the  next version of kernel, the kernel regressions will become the working kernel bugs.
 
 From the 1~5 stage, the new kernel regressions may will be born, and some existing kernel regressions may will be fixed. In our study, we focus on the characteristics of kernel regressions that manifest at stage 1~5.

2.3 Automatic Analysis
 
To ensure correct analysis, we only study fixed kernel regressions that manifest at stage 1~5, and the  root causes of these regressions can be identified from LKGR/LKML/LKBT. Because unfixed bugs may be invalid
and root causes described in the reports can be wrong.

To verify the analysis results from the sampled datasets, we propose a novel method to automatically analyze large dataset of bugs reports in LKBT, linux kernel mails in LKML and commits info in LKGR. Specifically, we apply text classification and information retrieval techniques on the these dataset to automatically analyze below information in one kernel development cycle:
 1. the classfication of kernel regressions.
 1. the distribution of different type of kernel regressions and wroking bugs.
 1. the lifetime of difference type of kernel regressions.
 1. the effort extent of finding regression/fix regressions/adding new features.
 1. the number of new features/kernel regressions.
  
Our analysis method consists of the following steps:

 1. Preprocessing infomation in LKGR/LKBT/LKML, each bug report may contain the following information: bug ID, summary, time,
status, reporter, assignee, etc. We represent bug documents in word level, called bag-of-words approach. Each word in bug documents is parsed into an index. Each bug document is represented by a vector. 

 1. We use the manually-labeled bugs as a training set to produce classification models for different categories of bugs. We use  classifier learning methods with tuned parameters (such as support vector machine(SVM), etc.) to get the more accuracte classification.

2.4 Root Cause Analysis

We analyze the statistic results based on  datasets, and present the root causes of regressions in kernel development cycle and compare our results with those in the previous studies [???], so that we can understand some important regressions characteristic changes.

  1. There are three type of kernel regressions: memory, concurrency, Semantic. the most parts of regressions are  simple sematic regressions, and second parts are simple sematic regressions, these regressions belong to the same patchs/changeset. and these regressions have been fixed in the development cycle. The complex sematic and concurrent regressions seldom have been found and have changed to working bugs which would be found and fixed in long time.

  1. The activity and effort extent of adding new features is much higher than those of finding regression, and those of finding regression is higher than thos of fixing regressions. If a regressions bug becomes a working bugs, then the efforts and time to fix the bug will much expensive than regressions. If the find time of a working bugs is more late, then there are less probability to be fixed. So there are lot of unclear bug reports lead to the potential bugs is very hard to fix.

[LKML]通过对LKML和LKBT的对比，发现开发阶段的很多bug没有进入到LKBT中，但在LKML中存在。说明了？

Based on the linux sucessfully development process near 20 years, the famous paper "The Cathedral and the Bazaar" and a lot of research papers, people almost acknowledged: Because of "Linus's Law", less formally speaking, "Given enough eyeballs, all bugs are shallow.",  "Release Early, Release Often" should be the critical part of linux development model. And this model will ensure the persistent dependabiility of Linux.

From our root cause analysis, we consider above mention "Linus's Law" and  "Release Early, Release Often" model are unable to estabilish in current development situlation. First, there are not enought eyeballs. First, since Linux is community-developed; a customer cannot exert any pressure over the development team for a quick resolution of a bug. Kernel developers resolve bugs as they are reported. In addition, developers always balance between bug fixing and adding new features. The kernel evelopers like to use some analysis tools to help them to find some relatively simple regressions. But for some relatively complicated regressions， they have less time. So these relatively complicated and not very high severity regressions will become to working bugs. When the end users or testers find the abnormal phenomenon because of these working bugs, they are hard to descript clearly the root cause, then the developer can not get enough information to fix bug. Second, "Release Early, Release Often" model didn't reduce all bugs. This model only reduce the lifetime of the simple regression bugs, but put the the relatively complex regression changed to the working bugs which need more efforts and time to fix. Along with the accumulation of these relatively complex bugs, the dependabiility of Linux will be reduced.[EXT4, Samsung bugs].

To make "Linus's Law" and  "Release Early, Release Often" model to continue being valid in current development situlation, we should provide "enough eyeballs". The  "enough eyeballs" should be the automative instant regression tesing service for every kernel develoeprs. Below sections will descript why and how about this new regression tesing paradigm for kernel development.

完成“title”、“Abstract”、"abstract"、“analysis of kernel development”部分的初稿
================================

下面的部分需要继续修改和完成
================================

3 Desing and IMplementation of KIS
------------------------------------

3.1 Why we need KIS?
----------------------
From the analysis results in section 2, we consider the more and accurate "eyebows" should have to provide by automated testing service. The ideal situlation is the automated testing service  can guarantee to provide accurate bug finding results as soon as the kernel developers push a patch commit in kernel git repository. 最终用户看到或测试的信息太粗糙，间隔太大，对开发者意义较小。The timely bug finding feedback will help the kernel developer saving a lot of time to fix the regression bugs. Then the kernel developer can put more effort on their new addon features but not the boring regression or working bug finding. The kernel development model will be changed to "Release Early, Release Often, Test Every Commits" model.

3.2 the Challenges of KIS 

Intuitive understanding of KIS's main work is Continuous Integration.  But current technology of CI can not handle the development of linux kernel.  All in all, "Large and Complex" of the kernel is the key problem. Now, Kernel has 10,000+ configurable features, 17,000,000+ lines of srouce code, 1,000+ parallel active developer, 480+ git trees, 400+ commits per day. All these factors mixed together will kill current CI systems.
[这里不提kernel支持硬件和外设的多样性]

作为kernel developer，如果遵守“频繁提交”的原则，那么1000多人每天不间断的提交changeset，会使比较充分的编译和测试的执行过程一直处于繁忙状态；而且还可能不得不等待他人的编译测试过了以后，才能看到自己提交的结果；如果build失败了，要花很长时间才能知道是否和自己的修改相关；既使提交了fix，也会等很长时间在能知道自己的提交是否真的能够修复这个对应的bug。

作为kernel tester and container人员，在编译中产生的错误是由哪个具体的commit引起的，应该由哪个kernel developer负责；不确定在内核能够在大部分配置情况下能够正确编译和初步执行；不确定本次开发的新版本与上次的老版本之间的性能差异是有哪个commit引起的。

以上这些问题会给linux内核开发带来无限的问题和风险。In a sumarry, there are three rough challenges to make KIS practical,  How to guarantee the instantaneity of testing process in below 1 hour? How to send the bug report with only root cause to the bug producer How to provide wide testing for more coverage of bug finding? In order to solve these problems, the KIS prototype of instant automated testing service is designed, implementated, and the initial feedback from kernel developers are very well.


3.3 Architecture of KIS
------------------------
图1显示了kis的architecture。图2显示了kis的工作流程（或者合并为一个图）。kis主要包含4个部分

1. pulling commit module
2. compiling&static analysis module
3. building&dynamic analysis module
4. bug report moudling

pulling commit module主要是随时从内核开发者处取得更新的代码。这里实现了3个底层机制：（1）轮询获取更新代码。为了避免对git server的干扰，这里没有采用对git server加入commit notify hook的手段来获知kernel developer每次提交的信息。而采用了定期查询的方式来感知commit的情况。目前重点对180+个git tree进行定期查询。 These trees produce 40 new branch heads and 400 new commits on an average working day. 每次查询的间隔是5秒？？？，这意味着大约15分钟查找一个git tree的变化情况。这种方法的好处是可以尽量杜绝了对已有开发环境的干扰。如果git server能够提供commit notify hook接口，那么查询将更加及时。（2）产生kernel config。根据每个新的commit，形成与此commit相关的kernel config，减少kernel config的空间，从而减少内核编译和动静态分析的次数；（3）形成编译和静态分析的target list。这里的target list是一个包括了由git tree, branch, commit, kernel config的4层嵌套列表。4层嵌套列表中的列表项代表了一个要被便于和静态分析的linux kernel。形成的4层嵌套列表中的列表项接下来的编译和分析将据此进行工作。

compiling&static analysis module主要是在本机中保存的git tree中更新的changeset进行编译和静态分析，确保能够快速定位编译错误和静态分析出的bug。为了支持高效完成上诉工作，这里实现了6个底层机制：（（2）执行编译和静态分析。每工作日中，被监视的180个git tree会形成40个新的branchs，400个新commits，本module需要对这些commits对应的kernel进行近140 个kernel config的配置，并进行编译和静态测试，包括sparse，smatch  coccinelle。如果在此过程中不进行性能优化，将会执行得非常缓慢。（3）监视和记录执行过程和产生结果。监视和记录在编译和静态分析中，执行的编译命令和静态分析命令和命令参数，执行命令过程中读取的内核源文件信息、执行命令过程中生成的内核目标代码信息，为后续编译和静态分析性能优化提供充分的数据依据。（4）查找静态错误。如果在此过程中发现bug，将基于（1）中形成的target commits列表进行二分查找，定位产生bug的具体commit。存储编译和静态分析过程中的编译错误信息和静态分析出的bug信息，为bug report模块提供数据。（5）存储成功的编译结果。即存储编译好的linux kernel，为building&dynamic analysis module提供可执行的linux kernel。（6）安全执行。为了确保kernel编译过程的安全性，kernels are built inside chroot jails. 

building&dynamic analysis module主要是基于compiling&static analysis module编译成功的linux kernel，建立一个可执行的系统环境，通过虚拟机和模拟器进行动态运行测试与分析，来快速准确地发现其中潜在的bug。为了支持高效完成上诉工作，这里实现了四个底层机制：（1）构建动态测试系统环境。目前主流的硬件平台是X86-32、X86-64和ARM。由于我们的测试平台是X86-64环境，所以可以通过KVM虚拟机来支持运行X86-32和X86-64平台，通过QEMU模拟器来支持ARM平台，将来也可容易地扩展到其他硬件平台；（2）执行动态测试系统环境。这里主要执行boot test和bug动态分析测试benchmark，包括trinity，mem perf, cpu hotplug等。为了充分利用多核服务器的性能，该模块在一台机器上将运行多个KVM虚拟机和模拟器。（3）查找动态错误。如果在执行过程中发现bug，将基于（1）中形成的列表进行二分查找，定位产生bug的具体内核对应的commit 值。如果是不可重现错误，目前采用重复多次（100，1000？？？）执行的方法，来尽量产生更多的错误信息。存储动态分析过程中产生的bug信息，为bug report模块提供数据。（4）监视和记录执行过程中的热点内存。在Linux内核对虚拟机和模拟器的内存使用情况进行动态检查，目的是查找内容一致的内存，为进一步的调度和内存共享优化提供参考数据。

bug report moudling主要是根据compiling&static analysis module和building&dynamic analysis module产生的bug数据进行整理和分析，来准确地定位bug的来源，并提供精确的信息给产生bug的kernel developer。这里实现了3个底层机制：（1）过滤编译产生的冗余bug信息。gcc compiler如果发现编译错误，将产生很多冗余的编译错误信息，这就需要对此进行过滤和XXX技术。这里对编译输出的bug信息进行进行进一步分析，找出bug的源信息，和产生此源信息的内核配置、编译参数配置，并根据commit中的commiter信息找到kernel developer的email信息，从而把便于定位和重现错误的信息发给产生此错误的开发者；（2）过滤静态分析产生的冗余bug信息。当前的各种静态分析工具会产生大量的false positive信息，如果把这些信息发给内核开发者，开发者会很厌烦其中的无用信息，为此我们对当前用到的静态分析工具产生的信息采用XXX技术进行了分析，尽量过滤了与内核无关的false positive信息，从而把不包含false positive信息发给产生此错误的开发者；（3）过滤动态分析产生的bug信息。在动态执行过程中，在启动阶段可能不产生或产生很少的数据就死机了，也可能执行到某个阶段产生可重现或无法经常重现的kernel dump信息，甚至可能导致KVM或QEMU崩溃等，这里需要针对这三种情况进行进一步分析，通过KVM或QEMU的执行来获得内核的CPU状态和内存状态，并根据内核的编译产生的符号信息建立映射关系，从而提供给内核开发者有效的内核函数执行栈信息、关键数据结构信息等，KVM或QEMU崩溃的执行状态信息，便于内核开发者定位错误。（需要再看看 zyy的log定位文章！！！）


3.4 optimization of KIS
-------------------------------------
从kis的执行流程可以看到，如果没有相应的优化手段，在某台机器（可给出配置）上完成对一个commit的多配置的编译，静态分析，动态分析和bug r report的一次工作工程，要花费的时间是？？？（应该有一个测试统计说明，表明一天只能测试多少个commit）。这远远无法应对内核的每日正常开发工作。 There are various possible responses to the challenges above. One can, for example, scale the build infrastructure either vertically (e.g., more powerful build servers) or horizontally (e.g., build grids to eliminate build queuing). Another tactic is to manage test suites and tests themselves more carefully: individual tests shouldn’t run too long, test suites shouldn’t run too long, etc. Make sure people are using doubles (stubs, mocks, etc.) where appropriate. Etc. But such responses, while genuinely useful, are more like optimizations than root cause fixes. Vertical scaling eventually hits a wall, and horizontal scaling can become expensive if resources are treated as if they’re free, which often happens with virtualized infrastructure. Limiting test suite run times is of course necessary, but if it’s done over too broad a scope, it results in insufficient coverage.


Store Data in memory : 所有的数据存储在内存中，包括git repo, 编译生成的目标文件，内核执行文件, initrd等(都在内存中？？？）涉及到数据压缩，内存紧致/压缩和执行内存共享，清除不必要的内核中的io buffer。可比较细地讲解一下执行内存共享。

buffer/cache reusable Data  : ccache 工具通过将头文件高速缓存到源文件之中而改进了编译内核的性能，因而通过减少每一步编译时添加头文件所需要的时间而提高了build kernel的速度。我们对此进行了扩展，除了编译程序，对各种静态分析工具的执行过程也进行了分析，每个静态分析工具也会分析一些源文件，并产生分析文件，这些分析文件的产生是依赖这些源文件的，只要这些源文件不变，且针对这些源文件的分析程序和执行参数不变，则产生的分析文件就可以重复使用，而不用每次都调用分析程序进行分析。当然，由于内存有限，对于不够常用的文件数据，采用LIRS调度方法，对其进行清除。

多核优化和多机优化：对于当前单机而言，可充分利用测试机器的多核配置。根据具体硬件配置设置好并行编译或分析的并行执行的上界和下界，使得编译和分析过程可以充分并行化。对于分布的多机环境而言，通过对由git tree, branch, commit, kernel config的4层嵌套列表进行从外到里的分割，可以比较均匀地把整个target列表中的target分配到不同的机器上执行。在多台机器间配置了空闲探测器，可被动和主动地执行target的push和pull操作，从而使得整个系统可以实现负载均衡。

delta executing: 在二分查找动态执行中的bug时有用。 多次动态执行查找bug很耗时间，构造一个特殊的内核，同时包含有某个feature和没有某个feature的kernel，在差别处有特殊的插桩指令。没有执行到差别处，所有的情况一样，这样会节省一半的时间。当执行到插桩处时，形成新的一个执行环境，这时的执行情况和没有delta execution优化时一样。





4  Preliminary Results
----------------------------

4.1 implemtation of KIS
----------------------------
KIS framework  consists of a server farm that includes five build servers (three Sandy Bridge and two Itanium systems). 具体配置？？？ On these systems, kernels are built inside chroot jails. The built kernel images are then boot tested inside over 100 KVM instances on another eight test boxes. The system builds and boots each tested kernel configuration, on a commit-by-commit basis for a range of kernel configurations. (The system reuses build outputs from previous commits so as to expedite the build testing. Thus, the build time for the first commit of an allmodconfig build is typically ten minutes, but subsequent commits require two minutes to build on average.)

4.2 Experiemental Results

Tests are currently run against Linus's tree, linux-next, and more than 180 trees owned by individual kernel maintainers and developers. (Running tests against individual maintainers trees helps ensure that problems are fixed before they taint Linus's tree and linux-next.) Together, these trees produce 40 new branch heads and 400 new commits on an average working day. Each day, the system build tests 200 of the new commits. (The system allows trees to be categorized as "rebasable" or "non-rebasable". The latter are usually big subsystem trees for which the maintainers take responsibility to do bisectability tests before publishing commits. Rebaseable trees are tested on a commit-by-commit basis. For non-rebaseable trees, only the branch head is built; only if that fails does the system go though the intervening commits to locate the source of the error. This is why not all 400 of the daily commits are tested.)

The current machine power allows the build test system to test 140 kernel configurations (as well as running sparse and coccinelle) for each commit. Around half of these configurations are randconfig, which are regenerated each day in order to increase test coverage over time. (randconfig builds the kernel with randomized configuration options, so as to find test unusual kernel configurations.) Most of the built kernels are boot tested, including the randconfig ones. Boot tests for the head commits are repeated multiple times to increase the chance of catching less-reproducible regressions. In the end, 30,000 kernels are boot tested in each day. In the process, the system catches 4 new static errors or warnings per day, and 1 boot error every second day.

The responses from the kernel developers in the room were extremely positive to this new system. Andrew Morton noted he'd received a number of useful reports from the tool. "All contained good information, and all corresponded to issues I felt should be fixed." Others echoed Andrew's comments.In Linux 3.7 development cycle, kis is credited with 63 bug reports, almost 11% of the total.


5 Related Work     
----------------------------

Two most well-known projects dealing with
Linux kernel performance are Automatic test
system by Bligh [?] and Linux Kernel Performance
project by Chen et. al. [?]. The former is
a widely publicized automated system that performs
a variety of tests on number of high-end
machines with a smaller set of test tools. The
latter is less known (semi)automated system
that utilizes less hardware resources but provides
more comprehensive set of benchmarks.
Other related work includes kerncomp[?] and
Open Source Development Lab’s Linux Stabilization
and Linux Testing [?] and Linux
2.6 Compile Statistics [?]. Most of the kernel
testing statistics is available ’big iron’ machines.
Though undoubtedly useful, the test results
produced by these projects are quite unrepresentative,
since they tend to test kernel
performance on very specific hardware with
very specific configurations. As authors note
themselves, "[p]erformance tests and ratings
are measured using specific computer systems
and/or hardware and software components and
reflect the approximate performance of these
components as measured by those tests" [?].

[4] M. J. Bligh Automated Linux Testing
test.kernel.org (2006)

[5] Chen, K. and Chen, T. The Linux
Kernel Performance Project kernelperf.
sourceforge.net

[6] Wienand, I. and Williams, D. Tools for
Automated Regression Testing of the
Linux kernel kerncomp.sourceforge.net
(2006)

[7] Open Source Development Labs
Linux Stabilization and Linux Testing
osdl.org/projects/26lnxstblztn/results
(2006)

[8] Open Source Development Labs
Linux 2.6 Compile Statistics developer.
osdl.org/cherry/compile (2006)

6 Discussion and Future Directions
----------------------------
符号化执行的VMM

比较两个版本带来的差异（两个虚拟机执行网络服务，只比较网络包的差异）

只执行部分代码，其他部分代码可进一步改进

软件测试服务伴随软件开发的过程，软件开发 release，也意味着测试结束。后面是维护了。

给其他软件开发提供测试服务，给其他软件提供各种分析视图。

Acknowledgments
----------------------------
This work was supported in part by NSF grants
