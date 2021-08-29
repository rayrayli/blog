---
title: FB 面试记
date: 2021-03-21 14:15:01
categories: Miscellaneous
tags: interview,
---

## 背景
去年10月份的时候，我在找工作，在linkedin上收到了facebook recruiter的私信，标题是 `Scaling Facebook's Products & Infrastructure`，说是纽约在招聘工程师，提供title是`Production Engineer`。

Recruiter 给我发了几个链接介绍了这个岗位，我发现这个岗位是偏底层，和运维有关，跟我以前在华为干的事情差不多，比如大规模网站OS层面的性能优化，负责系统的可靠性等等，这个岗位在业界叫`Site Reliability Engineer`，是谷歌最早提出来的。于是第二天和Recruiter约了电话。

## Phone Screen

Recruiter电话中主要问了我的目前的情况，以及背景，并且确认我有英文的沟通能力。其中有意思的是，他问我做过的项目时，我就开始blabla的说，因为有很多技术细节他估计也没完全听明白，最后问我对这个工作的意向如何，我当场回复说，Yea, this is what I did before.  Recruiter应该觉得我很自信，也没有问别的技术问题了。 据我后面了解，其他人会问10-20道比较简单的技术问题。
结束后Recruiter给我发了封邮件，让我选三个我比较方便的时间，然后迅速给我安排了电话面试。

## 电话面试
Facebook的电话面试分两轮，每轮45分钟，第一轮是coding，第二轮是System Interview。值得一提的是，Recruiter的邮件里面带了一个5页的Phone Interview Guide， 里面详细的描述了两轮面试问题的问题范围，介绍coding 环境，可以用的语言，以及应试的各种tips。不禁让人感叹面试细节的到位，相比之下，我准备面试TwoSigma的时候，Recruiter基本是只管预约时间，从来不主动介绍这一轮是什么面试，而且面试的过程中随意调换之前说好的次序，让人很无语。

### 第一轮coding

考了两个比较简单的题目，一个是操作字符串类型的，另外一个考察操作文件和数据结构，比如heap的使用和时间复杂度等，面试官在我做对题目的基础上， 重点问了一些异常场景的处理。

### 第二轮system 面

面试官直接贴上来一段vmstat的输出内容，让我一列一列的回答，每个指标是什么意思，以及延伸出去关于操作系统的一些细节知识，比如进程状态，内存管理机制，中断，syscall的背景，swap，Context switch，user space和kernel space区别，以及为什么要这么区分， free/buff/cache memory 区别等等。
后来面试官又问了两个问题让我进行troubleshooting，大概就是如果有个ml server上进程占用内存过高，影响了其他进程怎么办？你应该如何去定位这是什么进程，并且如何预防这样的情况再次发生。 另外一个问题就是shell上面运行一个命令，如果卡住了，是会有什么原因，你会怎么去定位和处理，用什么工具进行debug等等。

系统面要求对操作系统有熟悉的了解。如果你一直在linux上工作，应该会很熟悉debug 的各种命令。如果不了解，建议要把` Advanced Programming on Unix Enviroment ` 这本书过一遍。另外我又读了[linux-insides](https://0xax.gitbooks.io/linux-insides/content/) 这本书，这本书是gitbook上面的书，从开机，初始化开始讲，又重点讲了中断和系统调用，抢占式内核，内存管理等，主要是对着kernel代码来讲，还是值得一读的。


## onsite 面试
因为疫情的原因，我的onsite被安排成了virtual onsite。 为了保证一次成功，我将onsite预约到了电话面试的一个月以后。 我这个期间我主攻网络知识，和系统设计。 由于我对自己对操作系统的基本知识已经比较自信，在这上面花的时间比较少，基本上就是查缺补漏。我的复习时间分布主要如下：

![timeline](./images/timeline.png)

### 网络轮面试
网络轮面试涉及到了网络中的基础知识和协议栈：TCP/IP stack, ARP, DHCP, DNS, HTTP, SSL等等，还包括一些网络问题的定位手段。这轮从一个简单的问题开始，比如 “你curl 一个网址，这个背后发生了什么？” “"Tell me about your favorite protocol" 等等，然后每个协议都展开，问的非常细。我面试前详细的过了`TCP/IP Illustrated Vol. 1 by Richard W. Stevens` 这本书，把大学里面计算机网络的知识复习了一遍，这个过程还是受益良多，比如看到TCP congestion control，联系起来自己以前工作中做过的流量控制，对它背后的设计理念还有各种优缺点理解也更深入。 也好在面试没有涉及到各种routing protocals 比如(BGP, IS-IS, OSPF, RIP)等，个人觉得如果有时间，还可以看看 TCP/IP卷二，卷三，但是卷一用来面试是足够了。

### coding轮面试
coding是在Facebook自己的coderpad上进行编程，上面支持十几种语言的高亮，用起来还算舒服。 面试内容同样有两题，属于medium难度，我本来还蛮担心的，因为复习中花在coding的时间比较少，但好在之前刷leetcode比较充分（刷了大约350道题），两道题都很快的解决了。基本过程是我先给了一个解法，然后面试官围绕我的解法问一些边界异常情况，以及其他的更优解法，或者是同时给出迭代实现和递归实现等等。对于面试的准备来说，需要熟练运用String, Array, Graph and Recursion algorithms, 还有一些基础的数据结构 (Queue, Stack, Linked Lists, Graphs & Trees等) 还有一些高级的数据结构比如 LFU/LRU Caches, Heaps, Tries. 另外一些Dynamic Programming/Memorization可能考的比较少，但是要准备到。 leetcode有个monthly challenge，我每天都会做，能让自己一直保持做题的感觉。

### 系统轮面试
算是最难的一轮，但是整体下来我感觉是我表现最好的一轮。先是问了一个问题是在linux shell中执行ls -l foo* ,背后发生了什么。 我之前自己用C同时实现过一个shell 和 ls命令，所以对shell还算是比较了解。 从命令参数对解析入手，到进程的fork/exec/wait，进程的状态切换流程等，中间涉及到`system call，signal，interrupt，inode，file descriptors, memory management， ulimit，cgroup`的知识，问的非常细致。
后来面试官又问了一个troubleshooting的问题，“如果系统有个隐藏进程在跑，占用了系统资源，但你不知道是什么进程，怎么去找到他？”  ,这里涉及到
- 对各种tool的考察，`lsof, vmstat, pidstat, mpstat, iostat,stace,perf,sar,gdb` 
- /proc 文件系统的考察 
- User-mode 和 kernel-mode 的切换过程
- Linux package 管理等

因为我使用linux工作很长时间了，之前读过APUE这本书，在面试准备期间，又使用`Linux Programming Interfaces by Michael Kerrisk`  和`System Performance: Enterprise and the Cloud by Brendan Gregg`进行查缺补漏。这几本书对于理解linux基础原理方面很有帮助。

### 系统设计轮面试
这轮会考察你对系统的理解，面试官会问比如让你设计一个网络爬虫，你对系统总体的理解，你如何识别系统中的故障点并且如何加以防范。

我试图在设计过程中遵循着 “需求分析-->容量评估-->总体设计-->数据库设计-->详细设计-->容错设计” 这样的步骤，然而面试官似乎更加关心容错部分的设计，因为之前没有沟通好，所以浪费了一部分时间，好在最终我画出来了详细的模块设计，并且讲了讲可靠性设计的部分，比如一致性hash，data sharding和备份等。
值得一提的是，在准备方面，Facebook的recruiter给我发了`Grokking system design` 课程的免费优惠券，如果没有这十几门课的系统性学习，我可能还只能受限在github上`System Design Primer` 这个课程的水平。
当然，这个课程也不是万能的，设计方面没有统一答案，比如设计一个系统将大文件分发到服务器上， 可以设计成类似bittorrent的原理，可以使用UFTP，也可以使用split stream tree，同时要思考到每个方式的优缺点。


### Behavior Question

行为问题主要是用来考察你对工作一些特定场景的反应，这些问题包括介绍自己，你是如何解决工作中的问题（工程延期等），如何处理一些和同事和上级的冲突， 如何克服自身的缺点，如何面对成功和失败等等，一般结合自身的经验回答并没有固定答案。众所周知，亚马逊是一家面向Behavior Question的公司，其14条军规（公司文化准则）在业界相当有名。 我准备的时候就借鉴了大家准备的应对亚马逊的这方面的经验和题目，每个问题准备几个小例子，每个例子使用STAR （situation，task，action，result）方法进行展开叙述即可。当然英文一定要练习熟练。

## 结论

Onsite面完以后一周，我接到了面试官的口头offer，不得不说FB的招聘效率实在高。 看到自己每天辛苦的准备面试最终得到了回报，当时激动了半天。

![verbaloffer](./images/verbaloffer.png)

和国内不同的是，在美国是只要你有可以用来竞价的另外一个offer，你就可以和招聘人员谈判薪水，这点让我感受到美国对人才的尊重。 他们很鼓励你进行谈判薪水，如果你想要一个更高的价格的offer，那说明你对自己的价值的自信和肯定，如果有其他公司给你offer，从另外一方面也证明了你的价值，那么他们也更加愿意给你加价。再加上从第一轮面试到最终出结果，招聘成本对于公司也是很高的，你也不用担心他们会撤销你的offer。这点跟国内的大厂很不一样，除非你非常厉害技术过人，否则你没什么话语权。
后来我也顺利拿到了Tiktok的offer，并且使用Tiktok的offer 和Facebook的进行竞价，最终为自己多拿了差不多5到10万美金的包裹。中间来来回回和recruiter谈判了一个多月，到最后敲定合同的时候，已经是2021年1月了。 

回头看看这几个月，Facebook的给我带来的整个招聘体验是非常流畅丝滑，公司专门开发了一个招聘主页，你可以登陆上去后清楚的看到你现在是在哪一轮，你接下来要干什么，你即将要准备跟谁进行面试和谈话。招聘过程中招聘人员是有求必应，而且出结果的速度非常快。他们同时还会给你很多资源，尽量让你做好充足的准备，你如果准备不充分，也可以跟他们要求延期，这些都是非常灵活的。这些细节，是我从其他公司没有看到的。

我之所以不厌其烦的把这些写出来，主要就是想让大家了解Facebook是如何招聘系统工程师的。而当你经历了这些轮面试，最终也会是收获满满。





















