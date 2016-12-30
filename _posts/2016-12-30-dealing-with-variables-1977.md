---
layout: post
title: "Dealing with variables 1977"
date: 2016-12-30
excerpt:
---

In my ample free time I like to browse old issues of Byte magazine. I own no paper copies at this time, but I can go to this [archive](https://archive.org/details/byte-magazine), set the date filters to '75, '76, and '77, and *kick back*...

There is an intersting article in the **Best of Byte Volume 1** from June 1977 called **Write Your Own Assembler** (page 247). I have no realistic goal/desire to actually write an assembler, but the topic is interesting to me because sometimes when in a reflective mood I will wonder: what is really going on with these variables that I'm using, where are they physically placed in memory with respect to the rest of the program, how does the machine know where to find them, etc?" These are huge questions involving compiler details and processor architecture. What is great about early sources like this article is that things were simpler back then, and thus easier to understand, and can provide context for the solutions that we take for granted in modern systems.

What blew me away about this article was that it seemed to deal with these questions *in an era that pre-dates many of my assumptions about computer organization/architecture*, e.g., there is no mention of a stack/heap or of [data segments](https://en.wikipedia.org/wiki/Data_segment). (Also see this nice explanation at stackoverflow: [why-do-assembly-programs-have-separate-segments](http://stackoverflow.com/questions/7787383/why-do-assembly-programs-have-separate-segments)

We see instead a situation where variables can be simply placed in memory at the end of a sequence of machine instructions. To use the example from the article: after some lines of assembly code that will generate 48 lines of machine code we could create a variable named BETA using the pseudo-op code `BETA RMB 1`. This would literally reserve a byte at the end of the machine code sequence, at location 48, for this variable. We can use this variable elsewhere in our assembly program, e.g. 'ADDA BETA', and the assembler would replace BETA with the address 48.

The article points out that this scheme creates issues for the assemble, for example, if during parsing the assembler encounters BETA before it knows what the location of BETA will be. The article calls this the "forward reference problem" and states "There is no neat way out of it" (!).
