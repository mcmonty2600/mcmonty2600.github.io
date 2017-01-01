---
layout: post
title: "Dealing with variables 1975"
date: 2016-12-30
excerpt:
---

In my abundant free time I like to browse early issues of BYTE magazine. All issues are available at this  [archive](https://archive.org/details/byte-magazine). I can set the date filters to '75, '76, and '77, kick back and enjoy ...

There is an intersting article in the very first issue (1975) titled *Write Your Own Assembler* [1]. I have no realistic goal/desire to actually write an assembler, but the topic is interesting to me because sometimes when in a reflective mood I will wonder: what is really going on with these variables that I'm using, where are they physically placed in memory with respect to the rest of the program, how does the machine know where to find them, etc?" These are huge questions involving compiler details and processor architecture. What is great about early sources like this article is that things were simpler back then, and thus easier to understand, and can provide context for the solutions that we take for granted in modern systems.

What blew me away about this article was that it seemed to deal with these questions *in an era that pre-dates many of my assumptions about computer organization/architecture*, e.g., there is no mention of a stack/heap or of [data segments](https://en.wikipedia.org/wiki/Data_segment). (Also see this nice explanation at stackoverflow: [why-do-assembly-programs-have-separate-segments](http://stackoverflow.com/questions/7787383/why-do-assembly-programs-have-separate-segments)

We see instead a very simple assembler imagined where sequential parsing of the assembly code might simply locate a variable right after some sequence of machine instructions that have been generated up to this point. To use the example from the article: after some lines of assembly code that will generate 48 lines of machine code we could create a variable named BETA using the pseudo-op code `BETA RMB 1`. This would literally reserve a byte at the end of the machine code sequence, at memory location 48, for this variable. We can use this variable elsewhere in our assembly program, e.g. we can add the value stored at this location to the accumulator with 'ADDA BETA'. The assembler would replace BETA with the address 48 in generating the machine instruction.

The article points out that this scheme creates issues for the assembler, for example, if the assembler during its parsing encounters BETA before it knows what the location of BETA will be (since it is calcuated by this assembler based on how many machine instructions have been generated already). The article calls this the "forward reference problem" and states "There is no neat way out of it" (!).

[1] Fylstra, Dan. "Write Your Own Assembler." *Byte Magazine* September 1975: 50-58.
