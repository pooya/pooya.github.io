---
layout: post
title:  "Effective Root Cause Analysis"
date:   2016-11-02 06:00:00
categories: RCA
---

(Discuss this post on [Hacker News] [hackernews])

In the software industry, there are some situations that a developer has to analyze an issue in a complicated system and figure out its root cause. These issues can come from real deployments in customer sites, get discovered by the QA team, or the continuous integration (CI) system. This post describes the methods for effective root cause analysis (RCA) of such issues.

Ask Questions About the System
==============================
When starting on a new issue, the first action should be asking the question: “What has happened?” The next step is to gather information from the system to answer this question and then come up with better questions. The logs should be looked at only when there is something to look for.

Log spelunking as the first measure, although yielding results in some cases, is not a methodical approach. One of the shortcomings of this approach is finding red herrings in the log files and assuming they are the root cause. This usually follows by immediately assigning the ticket to another team (hot-potato style), just to understand that it is indeed expected behavior.
Looking at the familiar territory of system logs before understanding the problem is an anti-pattern called the [Streetlight Anti-Method] [gregg] by Brendan Gregg borrowing the term from [The Streetlight effect] [streetlight]:

> "A policeman sees a drunk man searching for something under a streetlight and asks what the drunk has lost. He says he lost his keys and they both look under the streetlight together. After a few minutes the policeman asks if he is sure he lost them here, and the drunk replies, no, and that he lost them in the park. The policeman asks why he is searching here, and the drunk replies, 'this is where the light is'."


Do not Hypothesize Without Enough Knowledge
===========================================
It is important to resist forming a hypothesis too early. Uninformed assumptions should not be made about the failure as this has the potential for directing the investigation into a wrong direction. When it is mistakenly assumed that a component is causing an issue, it is easy to find some supporting evidence even though it does not exist. Our eyes see things we want them to see. There are of course some cases where the first guess is indeed correct. However, this is not methodical and runs the risk of wasting a lot of time trying to prove (or even fix) an uninformed guess.

Instead, the [scientific method] [scientific_method] can be used to come up with more educated guesses for the hypothesis. Forming a hypothesis should be delayed until enough observations have been made to rule out any other alternatives to the hypothesis. In the words of Sir Arthur Conan Doyle:
> "When you have eliminated all which is impossible, then whatever remains, however improbable, must be the truth."

See [Bryan Cantrill's talk] [cantrill] for more reasons why it is dangerous to form the hypothesis before the questions.

Create a timeline of the events in the system
=============================================
One of the simplest but useful tools in doing postmortem debugging is drawing a timeline of the related events on a piece of paper. This timeline can include events that have certainly happened (based on log messages or side-effects) and events that might have happened. This timeline is useful for formulating questions about the system. For example, did a scheduled event actually happen or why did a certain event not happen.

An event timeline can also be extended to show the time interval in which an event could have happened. Such intervals can overlap for different events which means such events can happen in different orders. If the intervals are too long, adding more logging can be considered to allow narrowing down the possible orders in which the events could have happened.

An event timeline can be augmented with more and more events that are gradually less and less relevant to the observed failure. An event timeline makes it possible to visualize such events and understand if there is a correlation between the observed failures and the allegedly benign events.

This method is useful for augmenting the scientific method to come up with ideas of what could have gone wrong. It should be used as another way of forming questions like "Can the failure be a consequence of a specific event?". Of course, not all correlations mean causality.


Get the state of the system
===========================
A window into a running system is a bliss on a system that is not running as expected. There are many ways to get an idea of what a system is doing using a tracing tools such as strace and tcpdump. However, the power of "logging into" the system and inspecting the state is even more useful for complex systems. Erlang for example, provides a mechanism to attach to the running virtual machine, inspect Erlang processes and send messages to them. Python also has the [rconsole] [rconsole] library that allows effectively attaching to a running process.

An alternative for the systems that do not support such mechanisms is using signals (as in UNIX signals) and asking each component to print its state once it receives the signal. This mechanism does not provide a way of changing the state, however, the information gathered about the state of the system can be invaluable for debugging a live system as well as postmortem debugging.

Create a Mental Model of the System
===================================
One of the most effective methods for finding the root cause of an issue is taking a step back, going back to the drawing board and thinking about how the problem could have happened. Rob Pike has noted this method to be [the best programming advice he has received] [pike]. In 1919, Albert Einstein reflected on the contemporary practices of the scientists and wrote [Induction and Deduction in Physics] [einstein] on the value of deduction as opposed to induction for advancing the state of science. This paper, even a century later, is still a valuable resource for learning from the best on how to approach the unknowns and can be applied to other fields of study including computer science.

At the first glance, creating a mental model might be in contrast with the method advocating for more experimentation. However, in practice, these two methods are complementary:

* When the system is not completely understood, experimentation can be used to gain more knowledge about it. The experiments should try to answer the question: "How does the system work"?
* When you know what makes the system tick, take a step back and think about how it could have produced a wrong result.

The mental model formed for the current issue will outlive the issue and will be a crucial tool for future development and RCA.

Conclusion
==========
Root cause analysis is a science and following the scientific best practices results in being more effective and coming up with better results. Following the concrete methods outlined in this post helps remove the element of luck from RCA and improve both the RCA procedure and the system itself over time.

[hackernews]: https://news.ycombinator.com/item?id=12854191

[gregg]: https://youtu.be/abLan0aXJkw?t=1288

[streetlight]: https://en.wikipedia.org/wiki/Streetlight_effect

[scientific_method]: https://en.wikipedia.org/wiki/Scientific_method

[cantrill]: https://www.infoq.com/presentations/debugging-microservices-production

[rconsole]: http://winpdb.org/

[pike]: http://www.informit.com/articles/article.aspx?p=1941206

[einstein]: http://alberteinstein.info/vufind1/images/einstein/ear01/view/1/5-191.tr_000012852.pdf

