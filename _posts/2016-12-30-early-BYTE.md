---
layout: post
title: "Early BYTE"
date: 2016-12-30
excerpt:
---

I just discovered all issues of BYTE magazine can be viewed or downloaded at this [archive](https://archive.org/details/byte-magazine). In my plentiful free time I like to head over there, set the date filters to '75, '76, and '77, and browse away...

There are lots of really awesome pictures of minicomputers from this period, e.g. the KIM-1. But I also find that I learn a lot from the technical articles from these early issues. These address topics that are of interest today, but reference much simpler hardware, pre-date many of the concepts we take for granted, and make a different set of assumptions about the knowledge of the reader. Overall, they give a very different perspective on some familiar topics and this makes them fun to read, and can make one look at things in a fresh way.

There is an intersting article in the very first issue (1975) titled *Write Your Own Assembler* [1]. I have no realistic goal/desire to actually write an assembler, but I have read this article a couple of times now and have learned a few things on each reading. Its approach is to start out by imagining the simplest of assemblers and then point out the issues that come up. So we know that one of the most useful things an assembler gives you over machine language is the ability to use variable names. But how exactly is this implemented? In the example given, an assembly instruction (a "pseudo-op") will tell the assembler to reserve a certain memory location to store the variable value, and when generating machine code, the assembler will replace the variable name with this physical memory location. Fine, but where in memory will this variable reside? In the simplest case, an assembler parsing sequentially would simply locate the variable immediately after the sequence of machine instructions that have been generated up to the point of its declaration.

A straight-forward approach, but it creates issues for the assembler, for example, if the assembler should encounter the variable name before it knows what its memory location will be (since the memory location is calcuated based on how many machine instructions that preceded its declaration in the assembly). This "forward reference problem" is overcome by using a two-pass approach, where the first pass only scans the assembly for variable declarations, and builds a symbol table as it goes of all the variable names and their memory addresses. This is table will then be used on the second parsing of the assembly code, and this time machine instructions will be generated, and variable names will be replaced with the memory addresses from the table.

I've read a little about these topics before, but I've never seen it presented in such a simple and clear way. The article is imagining what happens when a reader with little prior knowledge on the subject sets out to create an assembler from the ground up, shows some issues they might encounter and on the way and how they might be solved. The result is an article that made for fun casual reading but also helped me to better appreciate some of the operation of an assembler/compiler that I've known about but always just taken for granted.

[1] Fylstra, Dan. "Write Your Own Assembler." *Byte Magazine* September 1975: 50-58.
