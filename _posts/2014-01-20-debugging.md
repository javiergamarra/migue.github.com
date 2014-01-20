---
layout: post
title: "Debugging"
category: 
tags: [programming, general]
---
{% include JB/setup %}

## Debugging is for losers

Many people have the strong opinion that debugging is only for losers (aka, bad programmers). I completely disagree with them.

Debugging helps create a much powerful mental model of your code, giving you a dynamic view of data and control flow of you source code. In addition, when you are working on a project with millions of lines of code and many third-party libraries the debugger can help you a lot during the process of finding bugs (especially in the latter ones).

Personally, debugging has helped me a lot in the process of understanding many of the JDK internals.


## Some debugging tips

Debugging is a good part of my daily job (this is a different story) so I would like to share with you some of my favourite tips.

### Exception breakpoints

 Surprisingly, I've met many people who have never heard about <code>Exception breakpoints</code> (I know, it is something unbelievable).

 Most of the times when you are dealing with a problem you have an <code>stacktrace</code>, so finding the root cause of the problem is just a matter of seconds. You can set a <code>Exception breakpoint</code> in your debugger (you can even define if you want to track caught or uncaught exceptions) and the execution of your program will be stopped at the point where the <code>Exception</code> is thrown.

 The image below shows how you can set an <code>Exception breakpoint</code> in IntelliJ 13

 <img src="/images/ExceptionBreakpoint.png" alt="Exception breakpoints" />

### Logging "evaluated expressions"

One of my favourite ones is the ability to log some expression into the console without stopping the execution of your program. I am not sure if all of the most popular debuggers have this ability, but the IntelliJ's debugger gives you it out of the box.

In the next example I am tracking the creation of some <code>AOP proxies</code> but I don´t want to stop the execution of my program but just log some "useful" message into the console. You just need to uncheck the <code>Suspend</code> checkbox, write your expression in the <code>Log evaluated expression</code> and you are done. Easy, right?

<img src="/images/NoSuspendLogMessage.png" alt="Logging messages without suspend the execution" />

Of course there are many more small tips than can be extremely useful during the debugging process: <code>instance filter, class filter or pass counts</code> but this could be a different story.
