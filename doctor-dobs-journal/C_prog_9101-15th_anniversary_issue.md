
# C PROGRAMMING 9101

## Down Memory Lane with C - Al Stevens

This month is DDJ's 15th anniversary issue, so I am taking time away from my usual programming pursuits and indulging in a bit of history. I thought it would be interesting to do some basic research into the impact that the C language has had on the magazine and its growth, and to reflect that impact against the influence that C has had on programming as a whole.

The C language is a few years older than DDJ. Dennis Ritchie developed the first compiler in about 1972 and, with Brian Kernighan, published The C Programming Language in 1978. C was developed on the PDP-11, and by the time K&R was published, C was running on the IBM 370, the Honeywell 6000, and the Interdata 8/32.

The emphasis for my research was on how and when C first found its way into the pages of DDJ, and what form its treatment and use took through the years. I wanted to see how the early contributors to DDJ perceived the language that was destined to pervade their industry, where their early tools came from, and what became of the tools and their creators.

Using the DDJ bound editions from 1977 until last year, I searched for articles that were about C, or that used C as the language to describe an algorithm or make a computer do something useful. The bound editions do not include advertisements, so I couldn't spot the first appearance of this or that milestone C product. But because my objective was to trace the evolution of editorial treatment of the C language, the ads were not important, except perhaps as items of nostalgic interest.

I didn't really scour those bound editions. As much fun as it might have been, there just wasn't time. Instead, I looked for references to C in the article titles, flipped through the books looking for C listings, and checked out the authors to see if anyone who eventually became a C luminary started out as a DDJ published visionary. I did not read every letter to the editor, every column, or every article. What follows, then, is the result of superficial research at best, but it offers, I think, an historical perspective on the relationship between C and the generation of programmers who grew up with DDJ.

Who's Who?

My scan for authors revealed that the early bylines in DDJ are a virtual rogues' gallery of folks who went on to become industry celebrities, luminaries of the first order in all fields of computing. Jef Raskin, George Morrow, Gary Kildall, Ward Christensen, Steve Wozniak, Ray Duncan, Lee Felsenstein, Brian Kernighan, Dennis Ritchie, Donald Knuth, Charles Petzold, Davy Crockett, Richard Wilton, and Herb Schildt all published in DDJ, most of them in the early years. (Davy Crockett? Naw, must be some other guy.)

The DDJ of today is a professional programmer's magazine without an editorial commitment to any particular platform, paradigm, or scale of computer. However, being mostly reader-written, the articles naturally incline toward the computer systems that programming writers and writing programmers have at hand -- and usually at home. But that is the legacy of the wonder years when DDJ was dedicated to articles about code that people could develop for what they called "home-brew" computers, and the code and articles were heavily oriented toward assembly language first and then Basic. C would not make an appearance in the magazine until there were compilers that programmer's could take home with them.

tiny-c

The first mention of C that I found was in a letter to the editor in February of 1979 where a reader, Ted Chapin, offered a review of a product named "tiny-c," which was a C language subset interpreter.

The product was distributed as the "tiny-c Owner's Manual" with printed assembly language source code for the 8080 and the PDP-11 and was, as near as I can tell, the first commercially available C language implementation for a microcomputer. It cost $40 and gobbled up a whopping 4K in the 8080. Well, you can still get a C compiler for under $100, anyway.

The next mention of C in DDJ was in May of 1979. In a short article -- one paragraph and some code, Ray Duncan published the assembly language interface that integrated tiny-c with CDOS, a CP/M derivative operating system that ran on Cromemco Z-80 microcomputers.

One month later, Les Hancock wrote the first DDJ article that was about the C language. In "Growing, Pruning, and Climbing Binary Trees with tiny-c," Les stressed that the article was not about binary trees but about C. The only C language system available for his microcomputer was tiny-c. He spoke to its limitations as a subset and then proceeded to use it effectively to make his point.

All three authors spoke highly of the tiny-c documentation. Hancock called it "lovely."

Tom Gibson, a C programmer who worked in an early Unix shop, was the author of the 8080 version of tiny-c. A coworker, Scott Guthery, wrote the PDP- 11 version, and together they wrote and self-published the documentation.

Scott is now the proprietor of the Austin Code Works, a company that continues the tiny-c tradition of selling software only when it comes with source code. Scott is also the author of the articulate and controversial article "Are the Emperor's New Clothes Object Oriented?" in the December 1989 DDJ.

You can still get tiny-c. Scott rewrote it in C to run on MS-DOS machines and published it in his book, Learning C with tiny-c (Tab Books, 1985). I used it to learn how to write interpreters. I liked the book, but it has too many gorilla cartoons. You need crayons.

In May of 1980, Gibson and Guthery wrote an article in DDJ called "Structured Programming, C and tiny-c," where they described their view of what structured programming is by using examples in both languages.

BDS C

In January of 1980, Les Hancock returned to the pages of DDJ with "Implementing a Tiny Interpreter With a CP/M-flavored C." Les's good news was that CP/M users finally had a C compiler. The product was BDS C, authored by Leor Zolman and published by BD Software, Zolman's company. The article used BDS C to implement Les's own tiny language interpreter. The language was tinier and further away from C than tiny-c, but the importance of this article is that it was the first one that used compiled C to implement something in the pages of DDJ.

BDS C was the first compiler I saw and used. My brother was using it to develop firmware for black box applications -- what they call "embedded systems" now -- and he showed it to me and gave me my first copy of K&R. That was around 1981.

Not long afterward I wrote an assembler in BDS C for a member of the TMS family of microprocessors. BDS C was a really fast, integer-only compiler with a Fortran-like common area, and I loved it but abandoned it as soon as a full K&R compiler for CP/M was available that I could afford. I met Leor at Software Development '90. He's with the C User's Group and still sells an occasional copy of BDS C.

K&R & J&L

In 1980, DDJ published an AT&T paper by Brian Kernighan, Dennis Ritchie, S.C. Johnson, and M.E. Lesk, titled, "The C Programming Language." In their closing remarks they ponder the future of C: "Should the pressure for improvements become too strong for the language to accommodate, C would probably have to be left as is, and a totally new language developed. We leave it to the reader to speculate on whether it should be called D or P."

Three years later the ANSI X3J11 committee was formed.

Small C

In May of 1980, Ron Cain published "A Small C Compiler for the 8080s," an article that contained a subset C compiler named "Small C," which was written in Small C. He started by developing the compiler in tiny-c and then used it to compile itself iteratively as he refined it. Cain, too, took the time in the article to praise the tiny-c documentation. This thing must have been something. Small C compiled to 8080 assembly language.

Cain had no way for readers to compile the compiler except by their own ingenuity. The code he published would compile on a Unix machine, but he openly left it to others to put executable versions of the compiler onto media that would be readable by all the many diskette and cassette formats of the day. Obviously, Cain was no entrepreneur. He confessed that he could not and did not want to keep up with the demands from programmers who wanted the Small C compiler.

Small C was a major breakthrough for budding C programmers who had 8080-based computers at home. It became a subject for many articles to follow, it was the language that other writers used to write about implementations of other things, and was eventually ported to CP/M and then to the 8088 and MS-DOS.

Cain wrote a follow-up article in September of the same year. In "Runtime Library for the Small C Compiler," he published the arithmetic and logical runtime code and the I/O library which resembled the Unix functions. All of his source code for the runtime library was in 8080 assembly language.

Cain mentioned the availability of C compilers for CP/M machines at the time of his article. Cain didn't like CP/M and didn't use it, and he spoke of the high cost of the existing compilers. I remember hearing about C compilers that ran on CP/M back then, but I do not know when they first came out or which ones Cain referred to.

In 1981, there were four Small C articles. These were about the compiler itself. One author rewrote the compiler in Fortran to compile the first bootstrap of the compiler, and he wrote two articles about his experiences. Another rewrote the compiler, changing its structure considerably, and offering it to DDJ readers as the InfoSoft C compiler for $50.

The fourth article about Small C was the first appearance in DDJ by J.E. Hendrix. His short article published some patches to the compiler. Hendrix was to become the heir apparent to the Small C mantle. In 1982 he published "Small C Compiler, v.2," adding many features to the compiler. He later published that version in a book, and has recently published in book and diskette form, A Small C Compiler, 2nd Edition (M&T Books, 1990), which ports the compiler and compiled language to the 8088 and MS-DOS.

I have a complaint about the book. Small C has never supported structures. I wish it did, because it is a small, fast compiler that would work nicely on my slow, single-disk laptop. But I need structures. In his discussion of what the Small C subset leaves out, Hendrix brushes off structures and programmers who use them when he says, "The use of structures and unions is never essential, although it may seem so to programmers who depend on them."

Now, in the first place, I don't need to be talked down to from that far up. And, in the second place, to address the importance of structures I'll quote Dennis Ritchie from my interview with him in the DDJ C Sourcebook for the 1990s. When asked how long it took to rewrite Unix in C, Dennis said:

"There were two tries at it... Ken Thompson tried to do it and gave up. The single thing that made the difference was the addition of structures to the language... Without that it was too much of a mess."

So there. Back to the history lesson.

In 1983 there were articles publishing "A Small C Operating System," and "A Small C Help Facility." 1984 saw "A New Library for Small C," "cc - A Driver for a Small C Programming System," and "p - A Small C Preprocessor." Hendrix wrote "Small C Update" in 1985.

Ed Ream's Editor

1982 was the year that Edward K. Ream wrote, "A Portable Screen-Oriented Editor," the program that you will find in every public domain C library in the Western world. Ed wrote his editor in Small C. Alan Howard wrote a responding article that enhanced the editor, and Ream published his own enhancement called "RED," written in BDS C.

C Gets Its Own Column

In October, 1983, Anthony Skjellum wrote the first installment of his "C/Unix Programmer's Notebook" column, a bimonthly column that would run more than a year. C finally rated its own place in the magazine. In 1984 Allen Holub published an article and the code to GREP.C. He would launch the "C Chest" in March of the following year when Skjellum decided to give up his column. The "C Chest" was the first of the monthly columns devoted entirely to the C language, and it ran for over five years.

Reviews

By 1985, there were enough C compilers for MS-DOS machines to warrant a benchmark article. They were Aztec C, Control C, C Systems C, Computer Innovations C86, Datalight C, DeSmet C, Digital Research C, EcoSoft C, Lattice C, Mark Williams C, Microsoft C, Software Toolworks C, and Wizard C. Fourteen in all.

In 1986 the number was up to 17, and another benchmark article followed. Digital Research C and Control C were gone from the list, but Datalight added something called the "Datalight Kit," and the new ones were Hot C, IBM C, High C, Mix C, and Whitesmiths C.

Where are they all now? The first, Microsoft C, was really a repackaged Lattice compiler that Microsoft sold until their own in-house compiler was ready. Datalight changed to Zortech and moved up to C++. Wizard moved west and joined Borland to become Turbo C. The Turbo C that was being developed in-house left Borland and became TopSpeed C. Mix C became Power C. Digital Research C became extinct. Mark Williams C became Let's C. This is as hard to keep up with as the "Twin Peaks" plot.

The 1988 article, "Speed Trials: Five Cs Compared," tested Microsoft C, Watcom C, Turbo C, Datalight Optimum-C, and Computer Innovations C86+, and mentioned High C.

Nay-Sayers

1986 was the year that someone discovered C-bashing as a national sport. It gained popularity mostly among programmers who were just learning C and couldn't wait to write something clever about it, mainly about how much trouble it was giving them. Couldn't be them. Must be C. It's fashionable not to like something that's so popular. I never liked Johnny Cash all that much. In January of 1986, we had "Inefficient C," (get it?) telling us how slow and big C programs are. And in June, we got "What's Wrong With C," telling us more of the same, and suggesting that C programmers do it for the glory of being in on something esoteric. Yeah, and your mama, too.

ANSI C

The first DDJ article I found about the ANSI standardization of C was in "Preparing for ANSI C" in August of 1987. I guess they had the shape of the conference table settled by then. We were to keep preparing for almost another three years. In 1988, K&R second edition came out, based on ANSI C. They couldn't wait. In August of 1989, DDJ had "Going from K&R to ANSI C," although it still wasn't ready for us to go there yet. We got the standard in early 1990. It's pretty good.

The C Programming Column

In August of 1988, Alan Holub hung up his spurs, closed the C chest, and I took over the C desk at DDJ. No big deal in the annals of computer science, but the little old lady in the next condo is impressed.

Classy C

The emphasis in 1989 was on C++. We had articles on C++ vs. Modula-2, C++ multitasking, directory searches with C++, and TAWK in C++. In December I found myself dead center between the past and the future when I interviewed Dennis Ritchie and Bjarne Stroustrup in the DDJ C Sourcebook for the 1990s about the history of C and the directions of C++.

Back to the Present

If we've worn too many ruts in Memory Lane this month, don't fret. We won't do it again for another five years, I bet. As I wandered through the DDJ titles of 15 years worth of reader-written journalism, I was struck by what I found and by what I didn't find, too. You'd think that the articles would not wear well, that the technology would have overtaken them and made them obsolete. To a certain extent that happened. You won't find much demand for 8080 assembly language any more. But just this past week someone on the CompuServe DDJ Forum was looking for the source code to Tiny Basic.

The Small C articles all the way back to the first are still valid to anyone who wants to see how a compiler can be written in C and watch how it grows and improves with time. There are many articles about fundamental algorithms that will remain valid and will survive all the paradigm shifts yet to come because the underlying principals never wear out.

And what didn't I see? There are a lot of data structures and fundamental algorithms that I expected to find somewhere in the lore of the last 15 years just because they have been out there waiting to be written about. Many have been neglected. That's reassuring. I was worried about running out of ideas.

Copyright © 1991, Dr. Dobb's Journal
