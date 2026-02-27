
# C PROGRAMMING - 9003

## A Thousand CURSES on TEXTSRCH - Al Stevens

Last month we completed the retrieval processes of the TEXTSRCH project, a "C Programming" column project that we started in December of last year. It builds and maintains a text indexing and retrieval data-base system that allows a user to find text files by composing key word query expressions. The program has two passes: an index builder and a query retrieval program. The query retrieval program searches the text file indexes for files that match the criteria of a Boolean key word search. It delivers a list of the file names that match the search. With the software, developed through last month's installment, a user can determine which files in the text data base match the criteria of the query, and from there he or she can move the files into another application, for example, a word processor.

This month we will add a new feature to TEXTSRCH to allow the user to select and view one of the files from within the TEXTSRCH retrieval program itself. Instead of merely displaying a list of file names that match the query, TEXTSRCH will display them in a menu window from which the user can select. Then it will display the contents of the selected file with the query expression's key words highlighted.

We use this new feature to explore the screen driver software called "CURSES." CURSES is a library of functions that were originally implemented in Unix V. Its purpose is to allow you to write portable, terminal device-independent C programs. The Unix system and the C language are still inexorably oriented to the simple teletype-like console device. The standard input and output devices are such that they can be anything from a clunky old ASR-33 teletype to a high-resolution, many MIPS, full-color, belch-fire, neck-snapper graphics workstation. To support them all, stdin and stdout must speak to the lowest-common denominator.

There are still many installations that use simple terminal devices, and these devices are grist for the stdin, stdout mill. Terminals are the same yet they are different. A system's local devices may be many and varied, and the remote dial-up users are likely to be calling in from any one of a number of different terminal types. These different video display terminal devices can work as one because they share the common ability to send and receive ASCII text with carriage returns and line feeds. If that is the only way a program needs to communicate with a user, then these devices share all the commonality they will ever need.

There are, however, features in the typical video display terminal that a program can use to enhance its user interface. Most such terminals have command sequences to clear the screen, position the cursor, and so forth. As you might expect, there is no one way to do all this. ANSI published a standard, and some terminal devices comply. The ANSI.SYS device driver that comes with MS-DOS allows a PC to use the ANSI protocols.

Many terminals have their own, non-ANSI ways to clear the screen, position the cursor, scroll, and achieve other video effects. A program written specifically to use the features of one of these terminals must be modified if an incompatible terminal is connected to the program. As a programmer in such an environment you have three choices: You can write to the common base, which means simple, unadorned, glass-teletype ASCII text; you can use the unique features of the terminal du jour and modify your program every time a new terminal comes into the picture; or you can write to a higher-level video protocol and have a system-level interpreter library translate your video commands into the commands of whatever terminal a user signs on with. The first choice is the appropriate one for text filter programs and console command programs. The second one is appropriate when the operating environment is well-defined and contained, and perhaps when user language performance is an issue. The third choice is the best one to make when you are striving for portability and device independence.

CURSES

To provide for an environment where users with different terminals can use the same software, and where the software can use the video terminal features that go beyond simple text display, the Unix system contains the "CURSES" library and the "termcaps" data base. The data base describes the video protocols of each of the terminals, and the library provides functions that translate a higher-level common protocol into that of the user's terminal device.

CURSES functions facilitate a primitive window-oriented display architecture. You can define windows and use them as virtual terminals. There are character and string display operations, cursor positioning operations, video attributes (such as highlighting and normal displays), keyboard character and string input, scrolling, and simple text editing operations such as inserting and deleting characters and lines.

CURSES works in memory buffers. You address your operations to a defined window, and CURSES makes the changes in memory. These changes do not appear on the screen until you tell CURSES to refresh the window. This method might seem peculiar to a PC programmer who is accustomed to instantaneous video memory updates. But it reflects its roots in the RS-232 ASCII terminal. It takes more time to update a terminal's screen than it does to write characters into a PC's video memory. For example, a 24 x 80 terminal operating at 19,200 baud will use about a second to refresh its screen. A well-behaved video library can keep a copy of the current screen image and be building another copy to contain whatever changes you are making. When you tell it to refresh, the library can, if the terminals features allow, refresh only that part of the screen that changes.

Lattice C 6.O

Lattice C is an old PC workhorse that has been around since Gates was in short pants and Kahn was a dynasty. It was one of the original full K&R C compilers for the PC. The first Microsoft C was in fact Lattice in a Microsoft binder giving Microsoft an entrance into the C compiler marketplace while they took their time building one of their own. Because Microsoft's own C compiler targeted upward compatibility for programs written with their earlier Lattice version and because the rest of the C compiler business strives for compatibility with Microsoft C, it can be said that Lattice had a strong influence on what C compilers for the PC would become.

There are Lattice versions now for other platforms, including the amazing and wonderful Commodore Amiga. The most recent version for the PC, Version 6.0, supports DOS and OS/2, conforms with the ANSI proposed draft, and comes with a source-level debugger, an editor, an assembler, a librarian, a linker, lots of utility programs, a communications function library, a database library that supports dBase III formats, a graphics library, a library of DOS-OS/2 Family Mode functions, and a CURSES library. If you do not require an Integrated Development Environment after the fashion of Turbo C, QuickC, and others (and many of us do not), this is as complete a C language development environment as you'd want.

The Lattice CURSES Library

The Lattice CURSES library is available in source code for $125 so you can port it to the compiler of your choice. This CURSES library provides a means for developing screen programs that can be ported between DOS, OS/2, and Unix with minimum changes. I used the Lattice compiler and this library to build the document viewing feature that we are adding to TEXTSRCH this month.

Porting Crotchety TEXTSRCH to Lattice C

I wrote the first three installments of TEXTSRCH in Turbo C 2.0. My intention was to make the code as close to ANSI C and as far from the PC architecture as possible to avoid restricting the program to a particular platform. To use the Lattice CURSES library, I decided to port the code to Lattice C rather than to port the CURSES code to Turbo C. Somehow I figured I'd have an easier time of it by porting my own stuff. Maybe, maybe not.

The port was reasonably easy with just a few hitches. Here is what I ran up against, and what follows is a new crotchet that I hereby induct into the "C Programming" column Crotchet Hall of Fame.

It is said that a compiler that compiles programs that comply with the ANSI standard is considered to be an ANSI-conforming compiler. But what about those compilers that extend the standard? For example, Watcom C supports the C++ convention for double-slash comments. The Turbo C fopen function allows the use of a non-standard mode parameter. To be sure, both compilers will compile programs that do not use these extensions. But, because you can write programs that use them, you can unintentionally write code that is not ANSI-conforming. Turbo C has, of course, many other extensions, such as pseudo-register variables and interrupt functions. Many compilers now include the interrupt function type, which I first saw in Wizard C, the ancestor of Turbo C. Usually you can tell the compilers to disallow such extensions, that you are interested in writing portable code, and the compilers will comply. But when an extension takes the form of the values accepted as a function's parameters, the compiler does not preempt the extension. So, in all my innocence and with good intentions aforethought, I used the Turbo C "rt" and "wt" formats for the fopen mode parameter. The Lattice fopen function, in true ANSI compliance, simply refused to open those files because it did not recognize the modes. Turbo C also supports mode formats such as "r+b" where ANSI and the Lattice documentation specify "rb+." Naturally, I used the non-standard formats in my fopen calls. You should go through all the code in <index.c> from last month and change every "r+b" to "rb+" and "w+b" to "wb+." Change all fopen modes that include the "t" to remove the "t." I believe that the definition of compliance should exclude such extensions.

The next portability issue came with header files. Turbo C puts some function prototypes into more than one header file. In this case, I included the non-standard <process.h> to get the prototype for the exit function. According to ANSI, this prototype is in <stdlib.h>, and that is where Lattice keeps it.

The moral of the story has to be: Get a good ANSI function library reference book and ignore the library documentation that comes with your compiler.

Other storms in my port were the result of issues unrelated to ANSI C. The TEXTSRCH <cmdline.c> source file uses the Turbo C findfirst and findnext functions to search a file directory. ANSI C has no equivalent functions because, I suppose, there are some C platforms that have no analogue to the DOS directory search. When I wrote about those functions last month, I said you would need to make substitutions if you are using a different compiler. Now I find myself in that same boat. Because Lattice has equivalent functions in its dfind and dnext functions and because it does not have the <dir.h> file that cmdline.c includes, I coded a <dir.h> that substitutes with macros the Lattice functions for the Turbo C functions. You will find <dir.h> as Listing One on page 144.

I had the global variable OK defined as 0, and the Lattice <curses.h> defines it as 1. If you use the Lattice definition, all the TEXTSRCH code works fine.

The next set of problems occurs because of errors in the Lattice header files. It's difficult to imagine how these errors have gone undetected until now. The <curses.h> file includes definitions of keystroke values for the keypad keys. One of these is KEY_PGDN, which defines the value returned when you press the PgDn key. The definition, 0x0181, is wrong. It should be 0x0151. The macros for the CURSES wstandout and wstandend functions are incorrect. They do not include the win parameter in the macro expansion. Not only do you get compiler warnings, but the functions do not work. Finally, the Lattice <stdlib.h> header file specifies in the free function prototype that free returns void, which is wrong. It returns int. I had to repair the Lattice header files to proceed.

My final problem was with the CURSES screen driver software. For some reason it reprograms the video mode of my Vega Video 7 in a way that makes the display go off into the weeds at unexpected times, usually after I exit my program. To solve this problem I would need to look at the source code for CURSES, and time and deadlines do not permit. A workaround solution is to run the TEXTSRCH program from a batch file that executes the DOS command MODE C080 after the TEXTSRCH program exits to DOS.

TEXTSRCH

To install the new file-viewing functions of TEXTSRCH, you must replace the source file named <search.c> from last month with the one in Listing Two, page 144. You must also compile and link <display.c>, Listing Three, page 144, and <error.c>, Listing Four, page 149, into the <textsrch.exe> program.

The BLDINDEX program works the same way that it did before. The new feature is in the TEXTSRCH program. When you enter a query expression the results are now displayed in a screen window with an ASCII -> cursor to the left of each file name. With the up and down arrow keys, you move that cursor and scroll the display. When the cursor points to a file you might want to view, press the Enter key. The first page of the selected document text displays in a new full-screen window. The up and down arrow keys will scroll the display. The up and down page keys will page the display. The Home key goes to the first page and the End key to the last. During the display all occurrences of the key words from the query expression display in a highlighted mode. You can move to the next page where a key word appears by pressing the right arrow key. The left arrow key moves you to the previous page where a key word appears.

Here is how to use CURSES to achieve these results. The process_result function in <search.c> is changed. Instead of displaying the matching file names it builds an array of those names. Then it calls the CURSES initscr function to initialize the screen manager, calls select_text so the user can select a file to look at, and calls the CURSES endwin function to shut down the screen manager.

The select_text function is where the user picks a file to view. We use the CURSES newwin function to build a menu window. The keypad function allows the CURSES keyboard routines to recognize the keypad characters, and the wsetscrreg function defines the scrolling boundaries of the window. Use of this function prevents the window borders from scrolling along with the rest of it.

The display_page function displays a specified page of the file menu in the window. Initially we call it to display the first page. Then we draw a box around the window, write the ASCII -> selector cursor, and read the keyboard. The various cases under the keystroke switch, take care of moving the selector cursor up and down, and paging and scrolling the file selector menu. When the user presses the Enter key, that case calls the display_text function, passing the name of the selected file as shown in the menu window.

At this point we must consider the values assigned to the different keys we are interpreting. They are taken from the Lattice <curses.h> header file and they correspond to what the Lattice version of the CURSES wgetch function returns for the cursor keys when the CURSES keypad mode is on. These values might not apply to different environments. Also see the use of the VERT_DOUBLE and HORIZ_DOUBLE global variables in the call to the CURSES box function. These too appear in <curses.h> and they correspond to the PC's graphics characters for border characters. You might need to change these values to something that matches your system. CURSES does not provide for border corner characters, but the Lattice implementation recognizes the IBM set and uses the matching corner graphics characters.

Look now at Listing Three to see the code that displays a text file. The function named display_text opens the file and calls its do_display function if the file opens OK. If not, it calls the error_handler function that you will find in Listing Four. This general-purpose function displays an error message in a window, waits for a key press, and clears the message.

The do_display function reads all the lines of text from the chosen file and stores them in a linked list in the heap. The list connects each line to its following line and records the positions of any key words in each line.

The findkeys function takes care of finding and storing key word occurrences. It scans the line of text comparing each word to the ones in the query expression. If a word matches one of the keys, its character offset relative to the start of the line goes into the header block of the line's linked list entry. The header block can contain up to five key words for each line, which should be enough to call your attention to the line.

After all the lines of text are tucked away in the linked list, the program builds a full-screen window to display the text. The display_textpage function displays a page of text beginning with a specified line. It displays the lines a character at a time. If the current character position is marked in the line's header block as the position of a key word, the program calls the CURSES wpstandout function to cause the word to be highlighted. When the program finds the next white space character, it calls the CURSES wpstandend function to return the display to the normal, non-highlighted mode.

Once a page is displayed, the program reads the keyboard. As with the file selector menu, the keystroke values control the screen display. You can page and scroll up and down, and you can move the next or previous page where a marked key word appears. The pagemarked function makes this test, finding the first line of the specified page and looking at each entry in the list to see if any line has a marked key word.

When you press the ESC key, the function calls wclear to clear the text display window and wrefresh to refresh that clearing to the screen. Then it deletes the window and frees the heap of the linked list entries.

Back in the select_text function the file selector window gets redisplayed and the user can pick out another file to look at.

TEXTSRCH Performance

How effective is the CURSES approach to the development of portable code? The proof would be in the successful porting of a program such as this one to another platform. I am sure that this program would port to a Unix system with no more fuss than I had moving it to Lattice C. There is, however, one big area of concern in such a move. We do not know how efficiently the program would operate. CURSES is a technique for the portability of screen driver code to a multitude of display devices. Its implementation in the Lattice library makes for an effective and efficient program because they used all the PC tricks for fast screen updates. What's more, I developed this program on and for a 20-MHz 386 computer. The only way to know how well or poorly this particular use of CURSES would work on a slower machine or with a different terminal is to move the program. So, with that in mind, I moved TEXTSRCH to the slowest compatible computer at my house, an 8-MHz COMPAQ II. I am happy to report that it works fine. This does not, however, qualify it for an environment where the terminal device is a serial VDT. I would suspect that some of the ways I used CURSES are not the best choices for such a setup. A seasoned CURSES programmer probably knows intuitively what to do and what to avoid to support the most effective user interface.

The collective abilities and shortcomings of CURSES across a wide selection of terminals would, no doubt, influence the way you would design a user interface. Given that one could learn these boundaries and with all this in mind, I can conclude that CURSES is an effective technique for wide platform independence of text-based screen management. That, of course, is no news to Unix programmers, who have had CURSES for several years. It is news to those others of us who might be looking for tidy ways to develop programs on the PC that can be moved to other operating environments.

C PROGRAMMING COLUMN by Al Stevens [LISTING ONE]



/* ------------ dir.h ----------- */

/* Substitute Lattice directory functions for
 * Turbo C directory functions
 */

#include <dos.h>

#define ffblk FILEINFO
#define ff_name name

#define findfirst(path,ff,attr) dfind(ff,path,attr)
#define findnext(ff) dnext(ff)




[LISTING TWO]



/* ---------- search.c ----------- */

/*
 * the TEXTSRCH retrieval process
 */

#include <stdio.h>
#include <string.h>
#include <curses.h>
#include "textsrch.h"

static char fnames[MAXFILES] [65];
static int fctr;

static void select_text(void);
static void display_page(WINDOW *file_selector, int pg);
void display_text(char *fname);

/* ---- process the result of a query expression search ---- */
void process_result(struct bitmap map1)
{
    int i;
    extern int file_count;
    for (i = 0; i < file_count; i++)
        if (getbit(&map1, i))
            strncpy(fnames[fctr++], text_filename(i), 64);
    initscr();                 /* initialize curses */
    select_text();             /* select a file to view */
    endwin();                  /* turn off curses */
    fctr = 0;
}

/* ------- search the data base for a word match -------- */
struct bitmap search(char *word)
{
    struct bitmap map1;

    memset(&map1, 0xff, sizeof (struct bitmap));
    if (srchtree(word) != 0)
        map1 = search_index(word);
    return map1;
}

#define HEIGHT 8
#define WIDTH 70
#define HOMEY 3
#define HOMEX 3

#define ESC 27

/* --- select text file from those satisfying the query ---- */
static void select_text(void)
{
    WINDOW *file_selector;
    int selector = 0; /*selector cursor relative to the table */
    int cursor = 0;   /*selector cursor relative to the screen*/
    int keystroke = 0;

    /* --- use a window with a border to display the files -- */
    file_selector = newwin(HEIGHT+2, WIDTH+2, HOMEY, HOMEX);

    keypad(file_selector, 1);       /* turn on keypad mode    */
    noecho();                       /* turn off echo mode     */
    wsetscrreg(file_selector, 1, HEIGHT);/* set scroll limits */

    /* -------- display the first page of the table --------- */
    display_page(file_selector, 0);

    while (keystroke != ESC)    {
        /* ----- draw the window frame ------ */
        box(file_selector, VERT_DOUBLE, HORIZ_DOUBLE);

        /* ------------ fill the selector window ------------ */
        mvwaddstr(file_selector, cursor+1, 1, "->");
        wrefresh(file_selector);

        /* -------------- make a selection ------------------ */
        keystroke = wgetch(file_selector);/* read a keystroke */
        mvwaddstr(file_selector, cursor+1, 1, "  ");
        switch (keystroke)  {

            case KEY_HOME:
                /* -------- Home key (to top of list) ------- */
                selector = cursor = 0;
                display_page(file_selector, 0);
                break;

            case KEY_END:
                /* ------- End key (to bottom of list) ------ */
                selector = fctr - HEIGHT;
                if (selector < 0)   {
                    selector = 0;
                    cursor = fctr-1;
                }
                else
                    cursor = HEIGHT-1;
                display_page(file_selector, selector);
                break;

            case KEY_DOWN:
                /* - down arrow (move the selector cursor) -- */
                /* --------- test at bottom of list --------- */
                if (selector < fctr-1)  {
                    selector++;
                    /* ------ test at bottom of window ------ */
                    if (cursor < HEIGHT-1)
                        cursor++;
                    else    {
                        /* ---- scroll the window up one ---- */
                        scroll(file_selector);
                        /* --- paint the new bottom line ---- */
                        mvwprintw(file_selector, cursor+1, 3,
                            fnames[selector]);
                    }
                }
                break;

            case KEY_UP:
                /* --- up arrow (move the selector cursor) -- */
                /* ----------- test at top of list ---------- */
                if (selector)   {
                    --selector;
                    /* -------- test at top of window ------- */
                    if (cursor)
                        --cursor;
                    else    {
                        /* --- scroll the window down one --- */
                        winsertln(file_selector);
                        /* ----- paint the new top line ----- */
                        mvwprintw(file_selector, 1, 3,
                            fnames[selector]);
                    }
                }
                break;

            case '\n':
                /* -- user selected a file, go display it --- */
                display_text(fnames[selector]);
                break;

            case ESC:
                /* --------- exit from the display ---------- */
                break;

            default:
                /* ----------- invalid keystroke ------------ */
                beep();
                break;
        }
    }
    delwin(file_selector);  /* delete the selector window */
    clear();                /* clear the standard window  */
    refresh();
}

/* ------ display a page of the file selector window ------- */
static void display_page(WINDOW *file_selector, int line)
{
    int y = 0;
    werase(file_selector);
    while (line < fctr && y < HEIGHT)
        mvwprintw(file_selector, ++y, 3, fnames[line++]);
}





[LISTING THREE]



/* ---------------- display.c ----------------- */

/* Display a text file on the screen.
 * User may scroll and page the file.
 * Highlight key words from the search.
 * User may jump to the next and previous key word.
 */

#include <stdio.h>
#include <stdlib.h>
#include <curses.h>
#include <ctype.h>
#include <string.h>
#include "textsrch.h"

#define ESC 27

/* ----------- header block for a line of text ----------- */
struct textline {
    char keys[5];               /* offsets to key words     */
    struct textline *nextline;  /* pointer to next line     */
    char text;                  /* first character of text  */
};

/* --------- listhead for text line linked list -------- */
struct textline *firstline;
struct textline *lastline;

int pagemarked(int topline);
static void do_display(FILE *fp);
static void findkeys(struct textline *thisline);
static void display_textpage(WINDOW *text_window, int line);

/* ---------- display the text in a selected file --------- */
void display_text(char *filepath)
{
    FILE *fp;

    fp = fopen(filepath, "r");
    if (fp != NULL) {
        do_display(fp);
        fclose(fp);
    }
    else    {
        /* ----- the selected file does not exist ----- */
        char ermsg[80];
        sprintf(ermsg, "%s: No such file", filepath);
        error_handler(ermsg);
    }
}

static void do_display(FILE *fp)
{
    char line[120];
    WINDOW *text_window;
    int keystroke = 0;
    int topline = 0;
    int linect = 0;
    struct textline *thisline;

    firstline = lastline = NULL;

    /* --------- read the text file into the heap ------- */
    while (fgets(line, sizeof line, fp) != NULL)    {
        line[78] = '\0';
        thisline =
            malloc(sizeof(struct textline)+strlen(line)+1);
        if (thisline == NULL)
            break;      /* no more room */

        /* ----- clear the text line record space -------- */
        memset(thisline, '\0', sizeof(struct textline) +
                        strlen(line)+1);

        /* ---- build the text line linked list entry ---- */
        if (lastline != NULL)
            lastline->nextline = thisline;
        lastline = thisline;
        if (firstline == NULL)
            firstline = thisline;
        thisline->nextline = NULL;
        strcpy(&thisline->text, line);

        /* ------------ mark the key words ------------ */
        findkeys(thisline);
        linect++;
    }

    /* ------- build a window to display the text ------- */
    text_window = newwin(LINES, COLS, 0, 0);
    keypad(text_window, 1);     /* turn on keypad mode    */

    while (keystroke != ESC)    {
        /* --- display the text and draw the window frame --- */
        display_textpage(text_window, topline);
        box(text_window, VERT_SINGLE, HORIZ_SINGLE);
        wrefresh(text_window);

        /* ------------ read a keystroke ------------- */
        keystroke = wgetch(text_window);
        switch (keystroke)  {
            case KEY_HOME:
                /* ------- Home key (to top of file) ------ */
                topline = 0;
                break;
            case KEY_DOWN:
                /* --- down arrow (scroll up) ---- */
                if (topline < linect-(LINES-2))
                    topline++;
                break;
            case KEY_UP:
                /* ----- up arrow (scroll down) ---- */
                if (topline)
                    --topline;
                break;
            case KEY_PGUP:
                /* -------- PgUp key (previous page) -------- */
                topline -= LINES-2;
                if (topline < 0)
                    topline = 0;
                break;
            case KEY_PGDN:
                /* -------- PgDn key (next page) ------------ */
                topline += LINES-2;
                if (topline <= linect-(LINES-2))
                    break;
            case KEY_END:
                /* ------- End key (to bottom of file) ------ */
                topline = linect-(LINES-2);
                if (topline < 0)
                    topline = 0;
                break;
            case KEY_RIGHT:
                /* - Right arrow. Go to next marked key word  */
                do  {
                    /* -- repeat PGDN until we find a mark -- */
                    topline += LINES-2;
                    if (topline > linect-(LINES-2)) {
                        topline = linect-(LINES-2);
                        if (topline < 0)
                            topline = 0;
                    }
                    if (pagemarked(topline))
                        break;
                }   while (topline &&
                        topline < linect-(LINES-2));
                break;
            case KEY_LEFT:
                /* Left arrow. Go to previous marked key word */
                do  {
                    /* -- repeat PGUP until we find a mark -- */
                    topline -= LINES-2;
                    if (topline < 0)
                        topline = 0;
                    if (pagemarked(topline))
                        break;
                }   while (topline > 0);
                break;
            case ESC:
                break;
            default:
                beep();
                break;
        }
    }
    /* -------- clean up and exit --------- */
    wclear(text_window);
    wrefresh(text_window);
    delwin(text_window);
    thisline = firstline;
    while (thisline != NULL)    {
        free(thisline);
        thisline = thisline-> nextline;
    }
}

/* ---- test a page to see if a marked keyword is on it ---- */
int pagemarked(int topline)
{
    struct textline *tl = firstline;
    int line;
    while (topline-- && tl != NULL)
        tl = tl->nextline;
    for (line = 0; tl != NULL && line < LINES-2; line++)    {
        if (*tl->keys)
            break;
        tl = tl->nextline;
    }
    return *tl->keys;
}

#define iswhite(c) ((c)==' '||(c)=='\t'||(c)=='\n')

/* ---- Find the key words in a line of text. Mark their
   character positions in the text structure ------- */
static void findkeys(struct textline *thisline)
{
    char *cp = &thisline->text;
    int ofptr = 0;

    while (*cp && ofptr < 5)    {
        struct postfix *pf = pftokens;/* the query expression */
        while (iswhite(*cp))  /* skip the white space */
            cp++;
        if (*cp)    {
            /* ---- test this word against each argument in the
                query expression ------- */
            while (pf->pfix != TERM)    {
                if (pf->pfix == OPERAND &&
                        strnicmp(cp, pf->pfixop,
                            strlen(pf->pfixop)) == 0)
                    break;
                pf++;
            }
            if (pf->pfix != TERM)
                /* ----- the word matches a query argument.
                   Put its offset into the line's header --- */
                thisline->keys[ofptr++] =
                    (cp - &thisline->text) & 255;

            /* --- skip to the next word in the line --- */
            while (*cp && !iswhite(*cp))
                cp++;
        }
    }
}

/* --- display page of text starting with specified line --- */
static void display_textpage(WINDOW *text_window, int line)
{
    struct textline *thisline = firstline;
    int y = 1;

    wclear(text_window);
    wmove(text_window, 0, 0);

    /* ---- point to the first line of the page ----- */
    while (line-- && thisline != NULL)
        thisline = thisline->nextline;

    /* ------- display all the lines on the page ------ */
    while (thisline != NULL && y < LINES-1) {
        char *cp = &thisline->text;
        char *kp = thisline->keys;
        char off = 0;
        wmove(text_window, y++, 1);

        /* ------ a character at a time -------- */
        while (*cp) {
            /* --- is this character position a key word? --- */
            if (*kp && off == *kp)  {
                wstandout(text_window); /* highlight key words*/
                kp++;
            }

            /* ---- is this character white space? ---- */
            if (iswhite(*cp))
                wstandend(text_window); /* turn off hightlight*/

            /* ---- write the character to the window ------ */
            waddch(text_window, *cp);
            off++;
            cp++;
        }
        /* -------- a line at a time ---------- */
        thisline = thisline->nextline;
    }
}





[LISTING FOUR]



/* ------------- error.c ------------- */

/* General-purpose error handler */

#include <curses.h>
#include <string.h>

void error_handler(char *ermsg)
{
    int x, y;
    WINDOW *error_window;

    x = (COLS - (strlen(ermsg)+2)) / 2;
    y = LINES/2-1;
    error_window = newwin(3, 2+strlen(ermsg), y, x);
    box(error_window, VERT_SINGLE, HORIZ_SINGLE);
    mvwprintw(error_window, 1, 1, ermsg);
    wrefresh(error_window);
    beep();
    getch();
    wclear(error_window);
    wrefresh(error_window);
    delwin(error_window);
}