# C PROGRAMMING 9008

## The Past, the Future, and Multimania - Al Stevens

The annual C issue is a special one to me. Besides being devoted to C, the issue is also my anniversary with DDJ as the C columnist. August 1990 starts my third year, and marks the time for a retrospective view of the past two years. A lot has happened. In that time we watched C strengthen its position as the preeminent software development language. The major milestone in the advancement of C was the approval by ANSI of the standard definition early this year. It was a long time coming. Second place goes to the gradual swell and then sudden explosion of interest in and use of C++.

Other milestones. The Integrated Development Environment established itself as the preferred way to write and test C code. Source-level debuggers got bigger and better. Developers of operating environments began producing Applications Program Interface libraries so that C programmers could write code into those environments more easily.

None of these developments actually started in the last two years. They have all been around for a while. But each of them really took hold in the very recent past.

Several important new compiler versions came out in the first half of 1990. Borland announced Turbo C++ in May. Microsoft released Version 6.0 of the Microsoft C compiler. Watcom released Version 7.0 with 8.0 coming soon. Zortech released their C++ Version 2.0. It is important to consider as well what did not happen in that period. Neither Unix nor OS/2 took over the PC marketplace. Graphics user interfaces did not replace text-based applications. Every workstation does not have a CD-ROM. Programmers have not been replaced by application generators or so-called fourth-generation languages.

The Future

You do not need tarot cards to guess what C programmers are going to be doing in the next several years. A prevalent platform target for programmers will be, I believe, workstations operating in a network. For the foreseeable future, at least, the workstations will be MS-DOS machines, and the networks will be NetWare. There is nothing bold about that prediction except that it might raise the dander of those who believe or who would prefer that it is inaccurate.

The position of the DOS machine is secure. The acceptance of the NetWare environment is growing. They are a natural fit because a NetWare system can readily accommodate software that runs as well on a single-user PC. Log into the network, and your DOS application takes on the properties of a multiuser system. Usually. Many of us will be writing DOS applications that can sense the presence of a network and deal appropriately with it. To prepare we need to understand the environment and look at some of the tools that support it.

Multiuser

Multiuser systems in the past had multiple terminals connected to a single computer with a multiuser operating system. Unix is such a system. Each user had a terminal. One processor executed the programs for each user. The processor jumped from user to user by giving slices of CPU time to each terminal.

Today the terminals are computers themselves. They run their own programs. A network architecture takes advantage of that fact by off-loading to the workstations the work that is done specifically for the user and letting the host machine, the "file server," perform the common and shared tasks. The multiuser aspects of the system are managed by the file server.

A network file server maintains and stores the system's shared resources, including common data files, software, and printers. The file server can also perform services that are common to all users. One example of such common services is electronic mail. Another is printer sharing.

An advantage to the network architecture is that when users disconnect from the network or the server goes down, the users are still in business because their workstations are stand-alone computers. If you write a network-cognizant program that can run in the network or by itself, then you have made good use of that advantage. Besides supporting network users, your program can be run by other users who do not have networks.

Multitasking

Multitasking is when a user can run more than one program from the same workstation. In the old days a user started a program from the command line at the terminal. The operating system in the distant computer started it up. If the program was interactive (communicated with the user via the console), the user could call up the operating system command line with a special hotkey. If the program ran in the background, the OS returned the terminal to the command-line prompt as soon as the program was underway. In either case the user could start a second program and the two ran together in tandem.

Some personal computers have multitasking operating systems. The Amiga has AmigaDOS. The Tandy Color Computer uses OS-9. The PC's DOS is not a multitasker, which is why TSRs were invented. But a DOS user has a choice. If the PC has enough extended or expanded memory, a DOS user can add a multitasking shell to DOS and have all the benefits of the multitasking environment. Desq View is one such shell. I am writing this column with XyWrite and DesqView 386. At the same time I am compiling a large C system and exchanging electronic mail messages with kindred souls on CompuServe. Do not let anyone tell you that you cannot multitask a DOS PC.

If you write programs to run in a multitasking environment, you can take advantage of that environment by writing parallel processes that are aware of one another. For example, a database program can spawn a search process as a separate task and return to the query composition program. The user can be writing another query while the first one is being processed. You can use the facilities of the multitasker to synchronize processes.

Mix and Match

Assume that you target your next application for workstations with DesqView running on a NetWare network. You can ignore those environments and write a DOS application and it will probably run OK. It will, that is, unless you completely ignore the fact that more than one user could be running that program.

There are things to concern you when you run a program on a network. Suppose the program has one configuration file with a fixed name in a fixed place. All your users are struck with using the same configuration of options because each of them cannot have his or her own copy of the file. A network-aware program does not build in such restrictions.

There are things to consider about a program that runs in a multitasking environment. The closer the program gets to the hardware, the less likely it is to peacefully coexist with other programs. Do not confuse TSRs with multitasking. A TSR takes over the whole machine by tricking DOS into thinking the TSR is the only program running. It changes the environment to suit itself and puts things back the way it found them when it is done. A multitasked program runs in a virtual DOS machine within the context of the multitasking shell. Other programs run in that context concurrently. If your program reprograms the keyboard, reads and writes the mouse ports directly, and writes into video RAM indiscriminately, it can mangle the environment for the other programs running in the same physical machine.

Writing an application that is well-behaved in these environments and that will work as well in a vanilla DOS PC is no easy feat. You'd be nuts to tackle it without help. Quarterdeck Office Systems, the vendor of DesqView, and Novell, the vendor of NetWare, both provide C programmer's API packages to allow you to integrate the functions of those environments with those of your C programs. This month we discuss the NetWare C Inteface-DOS package.

The NetWare Environment

A NetWare network consists of a file server and a number of DOS workstations. The file server is usually a PC dedicated to its server functions, and it runs unattended. The workstations each have a network card that is cabled to the file server. There are different electronic standards for the connection and different cabling conventions.

The main purpose for the file server is to permit users to share the server's disk space and to share files. A secondary purpose allows users to share printers. You can, therefore, build a network where most of the disk space and all of the printers are on the server. The workstations have the minimum hardware needed to run the programs. All the software and most of the data files are on the server.

Once the server is running, each workstation runs a TSR program called the "network shell." Its purpose is to communicate between the server and the workstation. The shell observes how many disk drives the workstation has and maps higher-drive letters to the volumes on the file server. Then it intercepts DOS calls. If a program is opening, closing, reading, or writing a file on one of the server drives, the shell exchanges packets with the server. Otherwise it passes the calls through to DOS on the workstation. The program usually does not need to know the difference. Sounds simple enough. In fact, the environment is much more complex than that.

Ideally, a user would be unaware that the network is operating. Except for some additional commands, the user interface is the same as DOS. But there are some internal things that can muddy up the process and that a programmer needs to know about. A high-level user called the "network administrator" assigns passwords and grants privileges to users to restrict access to server file subdirectories. The shell can intercept printer output and redirect it to a network queue for spooling.

Users log on with "userids." Your user can "shell out" to DOS from inside your application and log off of the network, perhaps logging on under another userid with different access privileges. Files that were available a moment ago, now seem -- to your program -- to not exist. Programs need to be aware that such things can happen.

The NetWare C Inteface

If you are going to write a program that runs in a network, you need to know how to make the extended DOS calls that manipulate the network itself. The NetWare C Interface is a library of C functions that provide that interface. I am not going to try to discuss the entire API here. That would be a big book all by itself. Instead, I will address a few small areas to give you a taste of how the platform works and how well the API is implemented and documented.

Programs that run in a NetWare network will be one of three types. First is the program that knows nothing about NetWare. Programs in this category are DOS programs written for the single-user PC. Most of them run OK on the network. Sometimes you will find a program that opens a file in a fixed place -- usually the subdirectory where the EXE exists -- and keeps it open. A second user on the network cannot run the program until the first user's copy terminates, because NetWare will not allow two users to have the same file open at the same time. Some installations simply run such programs from the workstation's local disk and bypass the network altogether. Others use utility TSR programs such as Net-Aware to intercept DOS open calls and substitute user-defined paths and names.

The second category of program is one that will run in a network or as a DOS program in a stand-alone PC. WordPerfect is an example. The program takes advantage of the facilities of the network when they are available and gets along without them when they are not. Printer selection is one such facility. If the program senses that no network is running, the program simply uses the facilities of DOS.

The third category of program is the one that runs only in a NetWare network. It uses network facilities that DOS does not support. A user-to-user message and chat system would be such a program.

You don't need any help writing the first category of program. But to write one of the others, you need to use the NetWare API. Before the NetWare C Interface was available, the API consisted of a document that explained the API calls at the assembly language level. NetWare API calls consist of an extended set of INT 0x21 functions that the shell intercepts and processes. Most of my NetWare programs in the past used C language interrupt calls to invoke the API functions.

You can still take that approach, but it is one intensive pain in the nether quarters. Every call has its own unique format for request and response packets and you will code a zillion different structures to deal with them. If you get the slightest element wrong, either in format or content, the API either returns a meaningless error code or freezes the machine. You do not want to work that way. The C Interface functions take care of the details by hiding the packet formats and providing parameter lists and return values to invoke each API function.

There are, of course, some problems. The API documentation is sprinkled with errors of commission and omission, ones that will trip you up from time to time. A CompuServe subscription and membership in the NOVA forum is a must. There are always knowledgeable folks there who can answer your questions.

Using the API requires that you understand the internal architecture of the NetWare operating environment. The API libraries are divided into 17 categories ranging from Accounting Services to Workstation Services. There is a data base called the "Bindery." There are transaction tracking functions, a queue management subsystem, and soon, a print server API. To figure out what all these categories are you need to read the "System Interface Technical Overview" document and then experiment some. It is not always clear from the descriptions of the functions what their purposes really are. Further, if you want to do something that does not have a corresponding API function, you need to piece together the logic for the series of functions that will support your requirement. There is not a lot of help in the API documentation for these kinds of analyses.

Suppose, for example, that your program wants to display the userid of the user who is logged onto the work station. (I had just such a requirement for an e-mail program.) A search through the various API services reveals no apparent function that returns that piece of information. Keep digging. What you eventually find is a function among the Connection Services called GetConnectionInformation. One of the data elements you pass it is a pointer to a null-terminated string to receive the "name of the Bindery object logged in at the connection number." Bindery object? Connection number? A careful reading of the Bindery Services documentation reveals that the Bindery is a database of objects, one of which can be a User. What's the connection number? That's the first argument to the GetConnectionInformation function. Where does it come from? Nothing says, so we go searching. Nearby we find the GetConnectionNumber function, which returns the connection number that the file server assigns to a workstation. From this bit of secondary deduction we conclude that a function to return the current userid would look like Listing One (page 168), getuser.c.

As an experiment, I wrote the same program by using the assembly language API and the intdosx function that has become a de facto standard among PC C compilers. Listing Two, page 168, is getuser1.c, and it offers an interesting contrast to the C API. Even this small example shows how much more readable the C version is. On the other hand, the version of the program compiled with the C API takes almost 4K more than the one with its own interface. Everything costs something.

Are these excursions through the oblique world of the NetWare architecture typical? In my experience, they are. For example, the documentation for the GetConnectionInformation function says that the shell uses this function to see if it is already loaded, and that other programs should, too. But the function needs a connection number, and no connection number exists without the shell already being loaded. Apparently you use any old connection number between 1 and 100, and if the function does not put something in the arguments, the caller can assume that the shell is not there. The documentation does not spell that out, however.

The Bindery

NetWare programmers should be familiar with the Bindery, a general-purpose database that NetWare uses and for which it provides support for applications use. The Bindery is the home for definitions of users, user groups, queues, servers, gateways, and so on. A Bindery entry is an "object." Each object has an object "type" and can contain one or more "properties." A property can be an "item," which can contain one "value" or it can be a "set," which contains a list of values. Apparently Novell is rewriting the database lexicon.

NetWare defines certain object types for the things it keeps in the Bindery. Application programs can add their own object types. As an experiment, let's look at the OT_USER Bindery object type. You can scan the Bindery for objects of a certain type, all objects, objects that match a specified name, and objects with a given object identification number. Listing Three, page 168, is showusrs.c, a program that scans the Bindery for all registered users and displays their userids on the console. To scan the Bindery, you pass an initial objectid of -1 and a name with a wild card (*) to the ScanBinderyObject function. The function returns the object's name and objectid. Subsequent calls to the same function accept the objectid filled in by the previous call. This scan continues as long as the function returns the SUCCESSFUL return code. The documentation is vague about what stops the scan, but I figured it out by experimenting.

Incidentally, the SUCCESSFUL return code is zero, the same value that the function returns if the shell is not loaded. Try the program without NetWare, and you go into a loop displaying garbage. So, you see, you do need a way to tell if the shell is loaded and the network is operating. I fooled around for a while with the GetConnectionInformation function attempting to find a reliable way for it to tell a program that the shell was or was not loaded. No luck. Some time ago I found a different technique, which you can see in Listing Four, page 170, nwloaded.c. It calls the shell at the lower level by using the System Calls API to set the NetWare lock level. The effect and meaning of the calls are unclear, particularly when viewed in the light of what those API functions are supposed to do, but the function works with no apparent ill effects.

Queues

NetWare includes a queue management system (QMS). Queues are lists that the queue manager maintains and they exist as Bindery objects. Users can be defined as being queue users, queue operators, and queue servers. A queue user can add an entry to a queue and observe the status of queues. A queue operator can modify queue entries. A queue server can retrieve entries from queues and service them.

A queue entry is a job. The job is whatever you want it to be. The queue server will perform the job based on the presence of the entry in the queue.

NetWare uses QMS to manage spooling to network printers. The API provides a number of functions that allow an application to control how that printing will occur. A future API will permit you to write print server applications so that you can print from NetWare print queues at workstations. By studying the API interface between print queues and QMS, you can see how you might use QMS in your own applications.

VAPs

A Value-Added Process is a program that runs in the file server. The VAP is for use on NetWare 286. This is a weighty subject, far beyond the scope of this column, and it makes the brave quiver because VAPs are difficult to write and downright thorny to test. NetWare 386 has an improvement on the VAP called the "NLM." The C API provides functions that you use in VAP development, but does not mention the NLM.

A VAP executes when the file server starts up. You have to bring the server down to reload a new VAP. VAPs run in 286 protected mode, which means that you cannot test them with the usual debugger techniques.

Other API Services

The C API includes functions that account for the use of different services in the network. You can write an accounting package that will distribute costs across users. There are API functions to support exchange of data files between DOS and Apple workstations. There are communications and message services for inter-user and inter-network data packets. There are connection, workstation, file and directory services, and on and on.

Summary

I have barely touched the surface of the NetWare programming environment. Of course, it is not possible to cover all bases in the space of a column. The purpose of this coverage is to expose you to the environment, show you the tools, and suggest that this might be the coming thing.

On the whole, I prefer using the C API to the alternative method of coding the low-level API calls into my C programs. The problems I addressed in this column are not problems with the API software itself, but with the level of information imparted by the documentation. Like any other complex software development platform, the NetWare environment takes a while to learn. Programmers who have experience with these functions would intuitively know the answers to the questions I pose. But it takes a while to reach that level of knowledge.

Probably my severest criticism is of the packaging. Novell uses double slip covers with two ring binders in a cover. Even my slender pianist's fingers are not deft enough to easily pull a manual out of its box. Usually when I finally get it out, the rings have popped open, and the pages fall on the floor. Yet the binders are that lay-flat kind with the rings offset on one side, and they cannot be stored outside the slip covers. Doesn't anyone ever try these things out for a while before they commit a big budget to using them?

When the demand for better NetWare tools reaches a level that justifies an investment in their development, software houses who cater to programmers will respond. Then you will see third-party function libraries that provide a higher level of access to the NetWare innards, providing an easier interface for programmers. Perhaps someday there will be C++ class libraries with classes for objects in the Bindery, queues, the communications packets, and the rest.

In the meantime, if you will be writing NetWare-savvy programs, you will want to get the NetWare C Interface-DOS libraries from Novell. If you want to study or use the underlying assembly language calls, get the NetWare System Calls-DOS package.

[LISTING ONE]

/* ---------- Display NetWare USERID ---------- */
#include <stdio.h>
#include <nit.h>

char *GetUserid(void);

void main()
{
    printf("\nUserid: %s", GetUserid());
}

/*
 * Get the current logged on userid
 */

char *GetUserid(void)
{
    static char userid[48];
    WORD connection_number = GetConnectionNumber();
    WORD objecttype;
    long objectid;
    BYTE logintime[7];

    GetConnectionInformation(connection_number, userid,
                    &objecttype, &objectid, logintime);
    return userid;
}

[LISTING TWO]

/* -------- getuser1.c ----------- */

#include <stdio.h>
#include <dos.h>
#include <string.h>

/* ----- request packet for get connection information ----- */
static struct {
    int  rlen;            /* packet length-2  */
    char func;            /* NW function      */
    char station;         /* station number   */
} rqpacket = { 2, 22 };

/* ------ reply buffer for get connection information ----- */
static struct {
    int rlen;            /* packet length-2  */
    long id;             /* object id        */
    int type;            /* type of object   */
    char userid[48];     /* name of object   */
    char time[8];        /* log on time      */
} rsbuffer = { 62 };

char *GetUserid(void);

void main()
{
    printf("\nUserid: %s", GetUserid());
}

char *GetUserid(void)
{
    union REGS regs;
    struct SREGS segs;

    segread(&segs);
    segs.es = segs.ds;
    /* ------- get connection (station) number ------ */
    regs.h.ah = 0xdc;
    intdosx(&regs, &regs, &segs);
    rqpacket.station = regs.h.al;
    /* ------- get connection information --------- */
    regs.x.si = (unsigned) &rqpacket;
    regs.x.di = (unsigned) &rsbuffer;
    regs.h.ah = 0xe3;
    intdosx(&regs, &regs, &segs);
    return rsbuffer.userid;
}

[LISTING THREE]

/* -------------- showusrs.c ---------------- */

#include <stdio.h>
#include <nit.h>
#include <niterror.h>

static long objid;

char *getusers(void);

void main()
{
    char *userid;
    objid = -1;
    while ((userid = getusers()) != NULL)
        printf("\n%s", userid);
}

/* ---------- scan bindery for users ------------- */
char *getusers(void)
{
    static char name[48];
    char hasproperties, flag, security;
    WORD type;

    if (ScanBinderyObject("*", OT_USER, &objid, name, &type,
               &hasproperties, &flag, &security) == SUCCESSFUL)
        return name;
    return NULL;
}

[LISTING FOUR]

/* --------------- nwloaded.c ------------ */

#include <stdio.h>
#include <dos.h>

int NetworkLoaded(void);

void main()
{
    printf("\nThe network %s operating",
            NetworkLoaded() ? "is" : "is not");
}

#define encr(lm) (0-(~(0-lm)))

/* ------- test network operating ----------- */
int NetworkLoaded(void)
{
    int lockmode, encrypted;
    union REGS regs;

    regs.x.ax = 0xc600;
    intdos(&regs, &regs);
    lockmode = regs.h.al;

    encrypted = encr(lockmode);

    regs.h.ah = 0xc6;
    regs.h.al = encrypted;
    intdos(&regs, &regs);
    lockmode = regs.h.al;

    return encrypted != encr(lockmode);
}
