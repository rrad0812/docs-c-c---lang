
# C PROGRAMMING

## D-Flat Edit Boxes - Al Stevens

Last month I described the D-Flat TEXTBOX window class, the base class for derived window classes that display text. The TEXTBOX class does not concern itself with manipulation or meaning of the text that it stores. Rather, it just provides the means to store the text, display it, and scroll and page through it. Other window classes add functionality to the text, and they use the base TEXTBOX class to support the low-level text management processes. This month we discuss one such window class, the EDITBOX class.

### The D-Flat EDITBOX Class

The EDITBOX class adds text editor functions to the TEXTBOX class. An EDITBOX supports the user's entry and modification of text by processing keystrokes and commands for text editing. Listing One, page 132, is editbox.c, the source file that implements the EDITBOX window class. The program consists of a window-processing module, named EditBoxProc, and several other functions that process the D-Flat messages that EditBoxProc intercepts.

The CreateWindowMsg function processes the CREATE_WINDOW message, and it is the first function you see in the source file. Most of the functions are named similarly and have comments that identify the message that they process. Some functions process subsets of the COMMAND message, and they have names and comments that associate the function with the COMMAND. I'll address each message and command and trust that you will be able to find the correct function that processes it.

The CREATE_WINDOW message sets initial values into the window's edit box fields. It establishes the default maximum text length, and sets up an initial empty buffer. The SETTEXT message allows the application program to specify a buffer of text for the edit box. The message makes sure that the length of the text does not exceed the maximum length specified for the edit box. Then it passes the message to the window-processing module for the TEXTBOX class via the BaseWndProc function.

The ADDTEXT message adds a line of text to a TEXTBOX window. When the window is an EDITBOX class as well, the message first checks to make sure that the length of the buffer with the new text added will not exceed the maximum text length for the window. Then the program passes the message to the TEXTBOX class's window-processing module.

After that, if the EDITBOX is a single-line editbox--typical of many data entry fields on dialog boxes--the program sets the current column pointer to the end of the text and marks a block that spans all of the text in the edit box. This block supports the CUA convention where single line edit boxes are marked when the user first tabs to the field. Then, if the user begins typing, the block is deleted. If the user moves the cursor first, the text remains, and the block mark is removed.

The GETTEXT message copies the text from the edit box's text buffer into the space pointed to by the message sender's first parameter.

The SETTEXTLENGTH message sets the maximum text length for an edit box.

Applications send the KEYBOARD_CURSOR message to move the cursor to a new position. So do parts of D-Flat. The EDITBOX class itself moves the cursor around by using the KEYBOARD_CURSOR message. Ultimately, the message gets into the code in message.c, which I discussed last July, to physically move the cursor. First, however, the EDITBOX class needs to update its pointers that specify where the cursor is with respect to the text buffer and the window. Then the program must make sure that the cursor will be in view--that the window has the focus, is visible, and that the part of it where the cursor is being moved is on screen, within the borders of any ancestor windows, and not overlapped by another window. If all of these conditions are true, the program sends the SHOW_CURSOR message to the system. Otherwise, it sends the HIDE_CURSOR message.

The SETFOCUS, PAINT, and MOVE messages pass themselves to the base window class and then send the window a KEYBOARD_CURSOR message. This process assures that the cursor is set properly when an EDITBOX window gets painted or moved and that it gets turned on when the window comes into focus and turned off when the window leaves the focus.

The SIZE message passes itself to the base class and then makes sure that the size operation did not cause the cursor position to go beyond the new window borders. If it did, the program adjusts the cursor position to stay within the window.

The SCROLL, HORIZSCROLL, SCROLLPAGE, and HORIZSCROLLPAGE messages first pass themselves to the base class to perform the scrolling or paging operation. The TEXTBOX class maintains variables that indicate the line of text in the buffer at the top of the window and the column of text in the left margin. These variables change as the text scrolls and pages horizontally and vertically. Scrolling is moving the text within the window one line or character position at a time. Paging is moving it one window height or width at a time. The EDITBOX class has to intercept the scrolling and paging messages to keep the keyboard cursor within the boundaries of the window. If the window scrolls the keyboard cursor position off the window, these intercepts will reposition the cursor so that it stays in view.

The LEFT_BUTTON message moves the keyboard cursor to where the mouse cursor is positioned. If the user positions the mouse where there is no text--beyond the end of a line or below the last line in the buffer, for example--the program puts the keyboard cursor as close as possible on a valid character position.

If the program is in the process of marking text, the LEFT_BUTTON message has no effect unless the mouse cursor is in the border. If so, the program scrolls the window in one of four directions depending on which border the mouse is in. Then it extends the marked block by one line or column to reflect the movement. This process allows the user to mark text blocks with the mouse where the block size exceeds the size of the window. By dragging the mouse into a border and holding the button down, the user scrolls the window and continues to mark the block.

The EDITBOX class receives the MOUSE_MOVED message whenever the user moves the mouse and its cursor is inside the edit box window. If the mouse button is down, the user is marking text in the edit box. The program establishes the position of the mouse when the user pressed the button as the anchor point of the block. Now, as long as the user holds the button and moves the mouse, the block will be defined in the range from the anchor point to the present mouse position. This message sends the MOUSE_TRAVEL message to restrict the mouse's movements to inside the edit box window. The window will keep the mouse restricted that way until the user releases the button.

The BUTTON_RELEASED message arrives when the user releases the mouse button. If the user was marking text with the mouse, then the program resets the text-marking mode, releases the restriction on the mouse travel, and adjusts the block markers so that the lower line and column define the beginning of the block.

The KEYBOARD message starts in the KeyboardMsg function and breaks down into several more function calls. The message itself begins by ignoring all keys if the user is moving or sizing the window or holding down the Alt key. Those keys and certain Ctrl-key combinations will be processed by window classes higher in the base class hierarchy.

The DoMultiLines function takes care of initiating the marking of a block. If the edit box has the MULTILINE attribute, and the user is holding down a shift key while typing a key that moves the cursor, and keyboard marking has not begun, the program puts the window into text marking mode and sets the current keyboard cursor position as the anchor point for the marked block.

The DoScrolling function processes all keys that will scroll or page through the text. Some of these keys can be processed by the base TEXTBOX class. Others are processed as the result of a combination of the base class and functions unique to the EDITBOX class. The Home, End, NextWord, PrevWord, Upward, Downward, Forward, and Backward functions move the keyboard cursor and the text pointers. If there is a marked block, and the window is not in the text marking mode--which means the user released the shift key or the mouse button--this function unmarks the block.

If the KeyboardMsg function sees that the user is marking a block with the keyboard and the DoScrolling function reported that it did indeed process a scrolling or paging key, the KeyboardMsg function calls ExtendBlock to extend the marked block to the new keyboard cursor location.

If DoScrolling reports that the key is not a scrolling or paging key, then KeyboardMsg calls DoKeyStroke to process the typed key. DoKeyStroke processes the Rubout, Del, Tab, Shift+Tab, and Enter keys. It moves the cursor to the left one position for the Rubout key and then drops into the code to process the Del key, which calls the DelKey function. The DelKey function deletes the key from the text buffer at the position pointed to by the keyboard cursor. DoKeyStroke calls the TabKey function to process the Tab and Shift+Tab keys. For the tab key, the function sends KEYBOARD messages with either a space character or the FWD character depending on the insert mode of the window. If the edit box is single-line, the function passes the Tab or Shift+Tab character to the window's parent. A dialog box parent will use this character to move the focus out of the edit box control.

DoKeyStroke passes all other keys, which will be displayable values, to the KeyTyped function. First, however, if the user types a key while a block is marked, the program deletes the block to be replaced by the typed key. The KeyTyped function inserts the key into the text buffer and performs the word wrap operation when the keyboard cursor reaches the window's right margin.

The SHIFT_CHANGED message watches for the user to release the shift key while marking a block of text with the keyboard. When that happens, the program calls StopMarking to take the window out of text marking mode.

The COMMAND message processes commands to a window from other places. A window typically receives a COMMAND message when the user executes a menu choice, although nothing prevents any part of a program from sending a COMMAND message. In fact, the EDITBOX class uses its own ID_DELETETEXT command to delete blocks of text when the user does something outside the menu that would cause text deletion.

The EDITBOX class processes five commands. All five are on the Edit menu in the example Memopad application. The menu commands correspond to standard menu items specified in the SAA/CUA specification. Whether or not your application implements the menu in the same way, most edit boxes will need some or all of these functions.

The ID_DELETETEXT command deletes a marked block of text. D-Flat maintains a buffer of deleted text so that the user can undo the most recent deletion. The ID_DELETETEXT command calls SaveDeletedText first to copy the marked block into the undo buffer. Then it moves the text buffer from one past the end of the block to the beginning of the block and updates the window's text pointers.

The ID_CLEAR command is similar to ID_DELETETEXT command except that it deletes the text, leaving an open space. The program must delete the lines in the marked block, leaving the newline characters in place.

The ID_UNDO command inserts the contents of the deleted text buffer into the active text buffer at the current cursor position.

The ID_PARAGRAPH command forms a new paragraph from the marked block if one exists. If not, the command forms the paragraph from the current cursor location to the end of the paragraph. Text in an edit box consists of lines with terminating newline characters. A paragraph is a group of lines up to the next blank line.

The CLOSE_WINDOW message hides the cursor and frees the deleted text undo buffer if one exists.

### How to Get D-Flat Now

The D-Flat source code is on CompuServe in Library O of the DDJ Forum and on M&T's OnLine. The source code is in a file named DFLATn.ARC. The n is a version number. A second file, DFnTXT.ARC, includes the following:

- A README.DOC file that describes the changes and how to build the software
- A calendar of the issues and the D-Flat subjects that my column addresses
- The D-Flat Help system database
- Documentation for the D-Flat API

D-Flat compiles with Turbo C 2.0, Borland C++ 2.0, Microsoft C 6.0, and Watcom C 8.0. There are makefiles for the TC, MSC, and Watcom compilers. There is an example program, the MEMOPAD program, a multiple document notepad.

If you cannot use either online service, send me a formatted diskette -- 360K or 720K -- and an addressed, stamped diskette mailer. Send it to me in care of DDJ, 501 Galveston Drive, Redwood City, CA 94063. I'll send you the latest copy of the library. The software is free, but if you care to, stick a dollar bill in the mailer for the Brevard County Food Bank. They take care of homeless and hungry families. We've collected about $500 so far from generous D-Flat "careware" users. I took a pile of money over there today. They are very grateful.

If you want to discuss D-Flat with me, use CompuServe. My CompuServe ID is 71101,1262, and I monitor DDJ Forum daily.

### Cheap Editor

Fast, good, cheap. Pick any two. Remember that? Well, there's a word processor that delivers all three. When I find a bargain, I want to share it. The VDE word processor/text editor is a bargain. For companies it's cheap. For individuals it's free. You can download it from Library 1 of the IBMAPP forum on CompuServe. Its file name is VDE161.ZIP. I'm using VDE to write this column. I have always wanted a word processor that devotes the entire screen to the text, is configurable, programmable, fast, small, and inexpensive. VDE is all of that. There is nothing that I want to do with text that VDE will not do. It is small and fast because it does not include all the dings and toots of those so-called full-featured behemoth word processors. VDE is not a WYSIWYG desktop publisher. It does not do graphics. There is no integrated spell checker or thesaurus. It is not a Windows app. So, just what is it? It is simply the best DOS text-based word processor I have ever seen, and I've tried most of them.

You can make VDE emulate the commands of several word processors. Its default mode uses the old WordStar command keys. Many PC users, particularly the old-timers, have those commands burned into their brains. WordStar was a staple in the Wonder years. Even though I haven't used it for a long time, my fingers leapt immediately to Ctrl-KD, Ctrl-QF, and all the other commands. You forget that you liked them, that you ever knew them. They're comfortable, like an old pair of slippers, a 1957 Tri-Pacer, or Uncle Jim's tobacco-reeking leather Morris chair.

You don't pays any money but you still gets your choice--about what goes on the screen, for example. I prefer a screen with nothing but text, but if you like a ruler line and a status bar that tells you the file name, cursor position, and other stuff, you can have them. You can have a screen border, too. When the old peepers get tired, you can switch into a VGA 20-line mode. For high-density text, you can have 28, 33, 40, 50, or 57 lines and 132 columns--your choice of colors, of course.

VDE will work with the file formats of WordStar, Word Perfect, MS Word, XWrite, as well as with ASCII text. It supports a two-window split screen, and you can have several files in memory at once. Everything it does, it does fast. It has returned validity to my old 4.77-MHz, 640K, one-diskette, T1000 laptop.

VDE's author is Eric Meyer. He wrote VDE in assembly language and maintains it as freely distributed shareware that individuals may use without charge. You can register for $30 if you want, and get support and the latest version. Companies can get site licenses that range from $2.10 down to $1 per user, depending on how many users there are. You can't beat those prices. If I was in charge at Microsoft or Word Perfect, I'd pay Meyer a million bucks just to get this thing off the market and out of the competition. In case they want to take my advice, or in case you want to send for a registered copy, here's the address: Eric Meyer, 3541 Smuggler Way, Boulder, CO 80303 USA, CompuServe: [74415,1305].

What's all that got to do with C? Well, VDE includes a C-language configuration package that does C indenting, so VDE is more than acceptable as a C programmer's editor.

### The Standard C Library

The Standard C Library (1992, Prentice Hall) by P. J. Plauger is a new book from a member of the ANSI X3J11 committee. From its title you might expect it to be a complete reference to the standard C functions as defined by ANSI, but it is not. I am not sure who the audience is for this book, so I will let you decide if it is for you.

The Standard C Library describes and publishes the source code for the header files, macros, and functions defined in standard C. In effect, it is a C-library implementation published in book form. You could use the library if you were building a new C compiler, but there are hardly enough new C compiler builders to justify the cost of publishing a book. Most readers already have a C compiler, and their compiler already has a library, so the book does not bring to the typical programmer some useful and heretofore unavailable piece of code.

So what is this book all about? For starters, it is a good study in how to implement a library. Also, the implementation has good examples of some of the more arcane features of C. I found the book useful for understanding some of the functions that the ANSI standard does not clearly describe. There are notes of historical interest throughout the book which describe the rationale behind some of the decisions made by the committee. You can often read between the lines and guess where the squeaky wheels prevailed over common sense. Even though he is an active member of the ANSI committee and contributes significantly to its work, Dr. Plauger pulls no punches when he addresses the deficiencies and failures of the standard.

The book is organized into chapters dedicated to the ANSI header files and the macros and functions defined and declared in each. The chapter on locale.h addresses the subject of writing C programs that you must port among international operating environments. This chapter is the best treatment of that subject that I have ever seen and is worth the price of the book by itself. So is the chapter on stdarg.h, which addresses functions that can accept a variable number of arguments. If you ever wondered how printf does its thing, this book is for you. If you ever wondered why scanf is not the answer to all your input prayers, you'll find out why here. There are places where more explanations would help. For example, the book does not explain why the implementation includes functions as well as masking macros in the header files for many of the standard C functions. Some readers will not know that older C programs often failed to include the header files, and because standard C compilers must compile old programs, they must provide the functions even when the macros are more efficient. The chapter on setjmp and longjmp is the weakest with respect to its code, and the author admits as much, telling you not to use the "grubby" code, which serves only to describe what a more platform-dependent implementation should do.

My most serious criticism of the book is that there are too many typographical errors. I can only guess that the book is a symptom of the rush-to-publish syndrome from which most managing editors and all publishers suffer. It is an author's responsibility to resist and counterbalance that pressure, and Plauger failed in that effort. Most of the errors that I found would be caught during a casual proof reading of the book, never mind the intense copy editing that most works receive.

There are some errors in the code examples that accompany the text, further indication of poor or no proof reading. I did not test the library code, so I cannot comment on its quality except to say that the brace indenting and placement style is not my favorite. You can get a diskette for $49.95 by using an order form in the back of the book. That's twice the usual cost of a companion diskette. The author claims to have validated the code with a validation suite and compiled and executed it with a number of compilers from UNIX, DOS, and other platforms. I believe that claim, but because of the surplus of publishing errors in the text, I would not trust the printed code enough to key it in. Get the diskette if you want the code. You should know, however, that if you compile a program that uses the code, the silly terms of the copyright require you to insert a string in your executable module that gives credit to Plauger and Prentice-Hall.

### Why I'm Glad My Name Isn't Pee Wee

If you've been watching the news, you've seen a recent item from my home state, Florida. Some irate parents video-taped a couple making love in view of the neighborhood and its children. The couple was arrested and have since been on Donahue, in all the papers, and on the 6 o'clock national news. The gentleman being prosecuted is a fellow Floridian named Al Stevens. I don't know what he does when he isn't performing for the neighbors, but please be advised that he DOES NOT WRITE THIS COLUMN!

### The Ascent of Language

On the whole, I like DOS 5.0--now that I've got it properly installed. I think, however, that once you've gone through the process, the experience can add to the programming languages listed on your resume. The two new ones are CONFIG.SYS and AUTOEXEC.BAT, which have now become slightly more difficult to master than APL. Here are some of the keywords that DOS 5.0 adds to our lexicon: high, loadhigh, himem, devicehigh, umb, hma, setver, fastopen, wina2O.386, temp, smartdrv, doskey, and emm386.

Judy makes kitchen samplers with poems and quotes reminiscent of her Pennsylvania Dutch origins. I think I'll ask her to cross-stitch this one:

My patience is all, The features are yet, The opener the architecture, The behinder I get.

[LISTING ONE]

```c
/* ------------- editbox.c ------------ */
#include "dflat.h"

#define EditBufLen(wnd) (isMultiLine(wnd) ? EDITLEN : ENTRYLEN)
#define SetLinePointer(wnd, ln) (wnd->CurrLine = ln)
#define isWhite(c)     ((c)==' '||(c)=='\n')
/* ---------- local prototypes ----------- */
static void SaveDeletedText(WINDOW, char *, int);
static void Forward(WINDOW);
static void Backward(WINDOW);
static void End(WINDOW);
static void Home(WINDOW);
static void Downward(WINDOW);
static void Upward(WINDOW);
static void StickEnd(WINDOW);
static void NextWord(WINDOW);
static void PrevWord(WINDOW);
static void ResetEditBox(WINDOW);
static void ModTextPointers(WINDOW, int, int);
/* -------- local variables -------- */
static int KeyBoardMarking, ButtonDown;
static int TextMarking;
static int ButtonX, ButtonY;
static int PrevY = -1;
/* ----------- CREATE_WINDOW Message ---------- */
static int CreateWindowMsg(WINDOW wnd)
{
    int rtn = BaseWndProc(EDITBOX, wnd, CREATE_WINDOW, 0, 0);
    wnd->MaxTextLength = MAXTEXTLEN+1;
    wnd->textlen = EditBufLen(wnd);
    wnd->InsertMode = TRUE;
    ResetEditBox(wnd);
    return rtn;
}
/* ----------- SETTEXT Message ---------- */
static int SetTextMsg(WINDOW wnd, PARAM p1)
{
    int rtn = FALSE;
    if (strlen((char *)p1) <= wnd->MaxTextLength)    {
        rtn = BaseWndProc(EDITBOX, wnd, SETTEXT, p1, 0);
        wnd->CurrLine = 0;
    }
    return rtn;
}
/* ----------- ADDTEXT Message ---------- */
static int AddTextMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int rtn = FALSE;
    if (strlen((char *)p1)+wnd->textlen <= wnd->MaxTextLength) {
        rtn = BaseWndProc(EDITBOX, wnd, ADDTEXT, p1, p2);
        if (rtn != FALSE)    {
            if (!isMultiLine(wnd))    {
                wnd->CurrLine = 0;
                wnd->CurrCol = strlen((char *)p1);
                if (wnd->CurrCol >= ClientWidth(wnd))    {
                    wnd->wleft = wnd->CurrCol-ClientWidth(wnd);
                    wnd->CurrCol -= wnd->wleft;
                }
                wnd->BlkEndCol = wnd->CurrCol;
                SendMessage(wnd, KEYBOARD_CURSOR,
                                     WndCol, wnd->WndRow);
            }
        }
    }
    return rtn;
}
/* ----------- GETTEXT Message ---------- */
static int GetTextMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    char *cp1 = (char *)p1;
    char *cp2 = wnd->text;
    if (cp2 != NULL)    {
        while (p2-- && *cp2 && *cp2 != '\n')
            *cp1++ = *cp2++;
        *cp1 = '\0';
        return TRUE;
    }
    return FALSE;
}
/* ----------- SETTEXTLENGTH Message ---------- */
static int SetTextLengthMsg(WINDOW wnd, unsigned int len)
{
    if (++len < MAXTEXTLEN)    {
        wnd->MaxTextLength = len;
        if (len < wnd->textlen)    {
            if ((wnd->text=realloc(wnd->text, len+2)) != NULL) {
                wnd->textlen = len;
                *((wnd->text)+len) = '\0';
                *((wnd->text)+len+1) = '\0';
                BuildTextPointers(wnd);
            }
        }
        return TRUE;
    }
    return FALSE;
}
/* ----------- KEYBOARD_CURSOR Message ---------- */
static int KeyboardCursorMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int rtn;
    wnd->CurrCol = (int)p1 + wnd->wleft;
    wnd->WndRow = (int)p2;
    wnd->CurrLine = (int)p2 + wnd->wtop;
    rtn = BaseWndProc(EDITBOX, wnd, KEYBOARD_CURSOR, p1, p2);
    if (wnd == inFocus && CharInView(wnd, (int)p1, (int)p2))
        SendMessage(NULL, SHOW_CURSOR, wnd->InsertMode, 0);
    else SendMessage(NULL, HIDE_CURSOR, 0, 0);
    return rtn;
}
/* ----------- SIZE Message ---------- */
int SizeMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int rtn = BaseWndProc(EDITBOX, wnd, SIZE, p1, p2);
    if (WndCol > ClientWidth(wnd)-1)
        wnd->CurrCol = ClientWidth(wnd)-1 + wnd->wleft;
    if (wnd->WndRow > ClientHeight(wnd)-1)    {
        wnd->WndRow = ClientHeight(wnd)-1;
        SetLinePointer(wnd, wnd->WndRow+wnd->wtop);
    }
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    return rtn;
}
/* ----------- SCROLL Message ---------- */
static int ScrollMsg(WINDOW wnd, PARAM p1)
{
    int rtn = FALSE;
    if (isMultiLine(wnd))    {
        rtn = BaseWndProc(EDITBOX,wnd,SCROLL,p1,0);
        if (rtn != FALSE)    {
            if (p1)    {
                /* -------- scrolling up --------- */
                if (wnd->WndRow == 0)    {
                    wnd->CurrLine++;
                    StickEnd(wnd);
                }
                else
                    --wnd->WndRow;
            }
            else    {
                /* -------- scrolling down --------- */
                if (wnd->WndRow == ClientHeight(wnd)-1)    {
                    if (wnd->CurrLine > 0)
                        --wnd->CurrLine;
                    StickEnd(wnd);
                }
                else
                    wnd->WndRow++;
            }
            SendMessage(wnd,KEYBOARD_CURSOR,WndCol,wnd->WndRow);
        }
    }
    return rtn;
}
/* ----------- HORIZSCROLL Message ---------- */
static int HorizScrollMsg(WINDOW wnd, PARAM p1)
{
    int rtn = FALSE;
    char *currchar = CurrChar;
    if (!(p1 &&
            wnd->CurrCol == wnd->wleft && *currchar == '\n'))  {
        rtn = BaseWndProc(EDITBOX, wnd, HORIZSCROLL, p1, 0);
        if (rtn != FALSE)    {
            if (wnd->CurrCol < wnd->wleft)
                wnd->CurrCol++;
            else if (WndCol == ClientWidth(wnd))
                --wnd->CurrCol;
            SendMessage(wnd,KEYBOARD_CURSOR,WndCol,wnd->WndRow);
        }
    }
    return rtn;
}
/* ----------- SCROLLPAGE Message ---------- */
static int ScrollPageMsg(WINDOW wnd, PARAM p1)
{
    int rtn = FALSE;
    if (isMultiLine(wnd))    {
        rtn = BaseWndProc(EDITBOX, wnd, SCROLLPAGE, p1, 0);
        SetLinePointer(wnd, wnd->wtop+wnd->WndRow);
        StickEnd(wnd);
        SendMessage(wnd, KEYBOARD_CURSOR,WndCol, wnd->WndRow);
    }
    return rtn;
}
/* ----------- HORIZSCROLLPAGE Message ---------- */
static int HorizPageMsg(WINDOW wnd, PARAM p1)
{
    int rtn = BaseWndProc(EDITBOX, wnd, HORIZPAGE, p1, 0);
    if ((int) p1 == FALSE)    {
        if (wnd->CurrCol > wnd->wleft+ClientWidth(wnd)-1)
            wnd->CurrCol = wnd->wleft+ClientWidth(wnd)-1;
    }
    else if (wnd->CurrCol < wnd->wleft)
        wnd->CurrCol = wnd->wleft;
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    return rtn;
}
/* ----- Extend the marked block to the new x,y position ---- */
static void ExtendBlock(WINDOW wnd, int x, int y)
{
    int bbl, bel;
    int ptop = min(wnd->BlkBegLine, wnd->BlkEndLine);
    int pbot = max(wnd->BlkBegLine, wnd->BlkEndLine);
    char *lp = TextLine(wnd, wnd->wtop+y);
    int len = (int) (strchr(lp, '\n') - lp);
    x = min(x, len-wnd->wleft);
    wnd->BlkEndCol = x+wnd->wleft;
    wnd->BlkEndLine = y+wnd->wtop;
    bbl = min(wnd->BlkBegLine, wnd->BlkEndLine);
    bel = max(wnd->BlkBegLine, wnd->BlkEndLine);
    while (ptop < bbl)    {
        WriteTextLine(wnd, NULL, ptop, FALSE);
        ptop++;
    }
    for (y = bbl; y <= bel; y++)
        WriteTextLine(wnd, NULL, y, FALSE);
    while (pbot > bel)    {
        WriteTextLine(wnd, NULL, pbot, FALSE);
        --pbot;
    }
}
/* ----------- LEFT_BUTTON Message ---------- */
static int LeftButtonMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int MouseX = (int) p1 - GetClientLeft(wnd);
    int MouseY = (int) p2 - GetClientTop(wnd);
    RECT rc = ClientRect(wnd);
    char *lp;
    int len;
    if (KeyBoardMarking)
        return TRUE;
    if (WindowMoving || WindowSizing)
        return FALSE;
    if (isMultiLine(wnd))    {
        if (TextMarking)    {
            if (!InsideRect(p1, p2, rc))    {
                if ((int)p1 == GetLeft(wnd))
                    if (SendMessage(wnd, HORIZSCROLL, 0, 0))
                        ExtendBlock(wnd, MouseX-1, MouseY);
                if ((int)p1 == GetRight(wnd))
                    if (SendMessage(wnd, HORIZSCROLL, TRUE, 0))
                        ExtendBlock(wnd, MouseX+1, MouseY);
                if ((int)p2 == GetTop(wnd))
                    if (SendMessage(wnd, SCROLL, FALSE, 0))
                        ExtendBlock(wnd, MouseX, MouseY+1);
                if ((int)p2 == GetBottom(wnd))
                    if (SendMessage(wnd, SCROLL, TRUE, 0))
                        ExtendBlock(wnd, MouseX, MouseY-1);
                SendMessage(wnd, PAINT, 0, 0);
            }
            return TRUE;
        }
        if (!InsideRect(p1, p2, rc))
            return FALSE;
        if (TextBlockMarked(wnd))    {
            ClearTextBlock(wnd);
            SendMessage(wnd, PAINT, 0, 0);
        }
        if (wnd->wlines)    {
            if (MouseY > wnd->wlines-1)
                return TRUE;
            lp = TextLine(wnd, MouseY+wnd->wtop);
            len = (int) (strchr(lp, '\n') - lp);
            MouseX = min(MouseX, len);
            if (MouseX < wnd->wleft)    {
                MouseX = 0;
                SendMessage(wnd, KEYBOARD, HOME, 0);
            }
            ButtonDown = TRUE;
            ButtonX = MouseX;
            ButtonY = MouseY;
        }
        else
            MouseX = MouseY = 0;
        wnd->WndRow = MouseY;
        SetLinePointer(wnd, MouseY+wnd->wtop);
    }
    if (isMultiLine(wnd) ||
        (!TextBlockMarked(wnd)
            && MouseX+wnd->wleft < strlen(wnd->text)))
        wnd->CurrCol = MouseX+wnd->wleft;
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    return TRUE;
}
/* ----------- MOUSE_MOVED Message ---------- */
static int MouseMovedMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int MouseX = (int) p1 - GetClientLeft(wnd);
    int MouseY = (int) p2 - GetClientTop(wnd);
    RECT rc = ClientRect(wnd);
    if (!InsideRect(p1, p2, rc))
        return FALSE;
    if (MouseY > wnd->wlines-1)
        return FALSE;
    if (ButtonDown)    {
        SetAnchor(wnd, ButtonX+wnd->wleft, ButtonY+wnd->wtop);
        TextMarking = TRUE;
        SendMessage(NULL,MOUSE_TRAVEL,
                (PARAM)&WindowRect(wnd),0);
        ButtonDown = FALSE;
    }
    if (TextMarking && !(WindowMoving || WindowSizing))    {
        ExtendBlock(wnd, MouseX, MouseY);
        return TRUE;
    }
    return FALSE;
}
static void StopMarking(WINDOW wnd)
{
    TextMarking = FALSE;
    if (wnd->BlkBegLine > wnd->BlkEndLine)    {
        swap(wnd->BlkBegLine, wnd->BlkEndLine);
        swap(wnd->BlkBegCol, wnd->BlkEndCol);
    }
    if (wnd->BlkBegLine == wnd->BlkEndLine &&
            wnd->BlkBegCol > wnd->BlkEndCol)
        swap(wnd->BlkBegCol, wnd->BlkEndCol);
}
/* ----------- BUTTON_RELEASED Message ---------- */
static int ButtonReleasedMsg(WINDOW wnd)
{
    if (isMultiLine(wnd))    {
        ButtonDown = FALSE;
        if (TextMarking && !(WindowMoving || WindowSizing))  {
            /* release the mouse ouside the edit box */
            SendMessage(NULL, MOUSE_TRAVEL, 0, 0);
            StopMarking(wnd);
            return TRUE;
        }
        else
            PrevY = -1;
    }
    return FALSE;
}
/* ---- Process text block keys for multiline text box ---- */
static void DoMultiLines(WINDOW wnd, int c, PARAM p2)
{
    if (isMultiLine(wnd))    {
        if ((int)p2 & (LEFTSHIFT | RIGHTSHIFT))    {
            int kx, ky;
            SendMessage(NULL, CURRENT_KEYBOARD_CURSOR,
                (PARAM) &kx, (PARAM) &ky);
            kx -= GetClientLeft(wnd);
            ky -= GetClientTop(wnd);
            switch (c)    {
                case HOME:
                case END:
                case CTRL_HOME:
                case CTRL_END:
                case PGUP:
                case PGDN:
                case CTRL_PGUP:
                case CTRL_PGDN:
                case UP:
                case DN:
                case FWD:
                case BS:
                case CTRL_FWD:
                case CTRL_BS:
                    if (!KeyBoardMarking)    {
                        if (TextBlockMarked(wnd))    {
                            ClearTextBlock(wnd);
                            SendMessage(wnd, PAINT, 0, 0);
                        }
                        KeyBoardMarking = TextMarking = TRUE;
                        SetAnchor(wnd, kx+wnd->wleft,
                                                ky+wnd->wtop);
                    }
                    break;
                default:
                    break;
            }
        }
    }
}
/* ---------- page/scroll keys ----------- */
static int DoScrolling(WINDOW wnd, int c, PARAM p2)
{
    switch (c)    {
        case PGUP:
        case PGDN:
            if (isMultiLine(wnd))
                BaseWndProc(EDITBOX, wnd, KEYBOARD, c, p2);
            break;
        case CTRL_PGUP:
        case CTRL_PGDN:
            BaseWndProc(EDITBOX, wnd, KEYBOARD, c, p2);
            break;
        case HOME:
            Home(wnd);
            break;
        case END:
            End(wnd);
            break;
        case CTRL_FWD:
            NextWord(wnd);
            break;
        case CTRL_BS:
            PrevWord(wnd);
            break;
        case CTRL_HOME:
            if (isMultiLine(wnd))    {
                SendMessage(wnd, SCROLLDOC, TRUE, 0);
                wnd->CurrLine = 0;
                wnd->WndRow = 0;
            }
            Home(wnd);
            break;
        case CTRL_END:
            if (isMultiLine(wnd) && wnd->wlines > 0)    {
                SendMessage(wnd, SCROLLDOC, FALSE, 0);
                SetLinePointer(wnd, wnd->wlines-1);
                wnd->WndRow =
                    min(ClientHeight(wnd)-1, wnd->wlines-1);
                Home(wnd);
            }
            End(wnd);
            break;
        case UP:
            if (isMultiLine(wnd))
                Upward(wnd);
            break;
        case DN:
            if (isMultiLine(wnd))
                Downward(wnd);
            break;
        case FWD:
            Forward(wnd);
            break;
        case BS:
            Backward(wnd);
            break;
        default:
            return FALSE;
    }
    if (!KeyBoardMarking && TextBlockMarked(wnd))    {
        ClearTextBlock(wnd);
        SendMessage(wnd, PAINT, 0, 0);
    }
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    return TRUE;
}
/* -------------- Del key ---------------- */
static int DelKey(WINDOW wnd)
{
    char *currchar = CurrChar;
    int repaint = *currchar == '\n';
    if (TextBlockMarked(wnd))    {
        SendMessage(wnd, COMMAND, ID_DELETETEXT, 0);
        SendMessage(wnd, PAINT, 0, 0);
        return TRUE;
    }
    if (*(currchar+1) == '\0')
        return TRUE;
    strcpy(currchar, currchar+1);
    if (repaint)    {
        BuildTextPointers(wnd);
        SendMessage(wnd, PAINT, 0, 0);
    }
    else    {
        ModTextPointers(wnd, wnd->CurrLine+1, -1);
        WriteTextLine(wnd, NULL, wnd->WndRow+wnd->wtop, FALSE);
    }
    wnd->TextChanged = TRUE;
    return FALSE;
}
/* ------------ Tab key ------------ */
static int TabKey(WINDOW wnd, PARAM p2)
{
    if (isMultiLine(wnd))    {
        int insmd = wnd->InsertMode;
        do  {
            char *cc = CurrChar+1;
            if (!insmd && *cc == '\0')
                break;
            if (wnd->textlen == wnd->MaxTextLength)
                break;
            SendMessage(wnd,KEYBOARD,insmd ? ' ' : FWD,0);
        } while (wnd->CurrCol % cfg.Tabs);
        return TRUE;
    }
    PostMessage(GetParent(wnd), KEYBOARD, '\t', p2);
    return FALSE;
}
/* --------- All displayable typed keys ------------- */
static void KeyTyped(WINDOW wnd, int c)
{
    char *currchar = CurrChar;
    if ((c != '\n' && c < ' ') || (c & 0x1000))
        /* ---- not recognized by editor --- */
        return;
    if (!isMultiLine(wnd) && TextBlockMarked(wnd))    {
        ResetEditBox(wnd);
        currchar = CurrChar;
    }
    if (*currchar == '\0')    {
        /* ---- typing at end of text ---- */
        if (currchar == wnd->text+wnd->MaxTextLength)    {
            /* ---- typing at the end of maximum buffer ---- */
            beep();
            return;
        }
        /* --- insert a newline at end of text --- */
        *currchar = '\n';
        *(currchar+1) = '\0';
        BuildTextPointers(wnd);
    }
    /* --- displayable char or newline --- */
    if (c == '\n' || wnd->InsertMode ||    *currchar == '\n') {
        /* ------ inserting the keyed character ------ */
        if (wnd->text[wnd->textlen-1] != '\0')    {
            /* --- the current text buffer is full --- */
            if (wnd->textlen == wnd->MaxTextLength)    {
                /* --- text buffer is at maximum size --- */
                beep();
                return;
            }
            /* ---- increase the text buffer size ---- */
            wnd->textlen += GROWLENGTH;
            /* --- but not above maximum size --- */
            if (wnd->textlen > wnd->MaxTextLength)
                wnd->textlen = wnd->MaxTextLength;
            wnd->text = realloc(wnd->text, wnd->textlen+2);
            wnd->text[wnd->textlen-1] = '\0';
            currchar = CurrChar;
        }
        memmove(currchar+1, currchar, strlen(currchar)+1);
        ModTextPointers(wnd, wnd->CurrLine+1, 1);
        if (isMultiLine(wnd) && wnd->wlines > 1)
            wnd->textwidth = max(wnd->textwidth,
                (int) (TextLine(wnd, wnd->CurrLine+1)-
                TextLine(wnd, wnd->CurrLine)));
        else
            wnd->textwidth = max(wnd->textwidth,
                strlen(wnd->text));
        WriteTextLine(wnd, NULL,
            wnd->wtop+wnd->WndRow, FALSE);
    }
    /* ----- put the char in the buffer ----- */
    *currchar = c;
    wnd->TextChanged = TRUE;
    if (c == '\n')    {
        wnd->wleft = 0;
        BuildTextPointers(wnd);
        End(wnd);
        Forward(wnd);
        SendMessage(wnd, PAINT, 0, 0);
        return;
    }
    /* ---------- test end of window --------- */
    if (WndCol == ClientWidth(wnd)-1)    {
        int dif;
        char *cp = currchar;
        while (*cp != ' ' && cp != TextLine(wnd, wnd->CurrLine))
            --cp;
        if (!isMultiLine(wnd) ||
            cp == TextLine(wnd, wnd->CurrLine) ||
                !wnd->WordWrapMode)
            SendMessage(wnd, HORIZSCROLL, TRUE, 0);
        else    {
            dif = 0;
            if (c != ' ')    {
                dif = (int) (currchar - cp);
                wnd->CurrCol -= dif;
                SendMessage(wnd, KEYBOARD, DEL, 0);
                --dif;
            }
            SendMessage(wnd, KEYBOARD, '\r', 0);
            currchar = CurrChar;
            wnd->CurrCol = dif;
            if (c == ' ')
                return;
        }
    }
    /* ------ display the character ------ */
    SetStandardColor(wnd);
    PutWindowChar(wnd, c, WndCol, wnd->WndRow);
    /* ----- advance the pointers ------ */
    wnd->CurrCol++;
}
/* ------------ screen changing key strokes ------------- */
static int DoKeyStroke(WINDOW wnd, int c, PARAM p2)
{
    switch (c)    {
        case RUBOUT:
            Backward(wnd);
        case DEL:
            if (DelKey(wnd))
                return TRUE;
            break;
        case CTRL_FIVE:    /* same as Shift+Tab */
            if (!((int)p2 & (LEFTSHIFT | RIGHTSHIFT)))
                break;
        case '\t':
            if (TabKey(wnd, p2))
                return TRUE;
            break;
        case '\r':
            if (!isMultiLine(wnd))    {
                PostMessage(GetParent(wnd), KEYBOARD, c, p2);
                break;
            }
            c = '\n';
        default:
            if (TextBlockMarked(wnd))    {
                SendMessage(wnd, COMMAND, ID_DELETETEXT, 0);
                SendMessage(wnd, PAINT, 0, 0);
            }
            KeyTyped(wnd, c);
            break;
    }
    return FALSE;
}
/* ----------- KEYBOARD Message ---------- */
static int KeyboardMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int c = (int) p1;
    if (WindowMoving || WindowSizing || ((int)p2 & ALTKEY))
        return FALSE;
    switch (c)    {
        /* --- these keys get processed by lower classes --- */
        case ESC:
        case F1:
        case F2:
        case F3:
        case F4:
        case F5:
        case F6:
        case F7:
        case F8:
        case F9:
        case F10:
        case INS:
        case SHIFT_INS:
        case SHIFT_DEL:
            return FALSE;
        /* --- these keys get processed here --- */
        case CTRL_FWD:
        case CTRL_BS:
        case CTRL_HOME:
        case CTRL_END:
        case CTRL_PGUP:
        case CTRL_PGDN:
            break;
        default:
            /* other ctrl keys get processed by lower classes */
            if ((int)p2 & CTRLKEY)
                return FALSE;
            /* --- all other keys get processed here --- */
            break;
    }
    DoMultiLines(wnd, c, p2);
    if (DoScrolling(wnd, c, p2))    {
        if (KeyBoardMarking)
            ExtendBlock(wnd, WndCol, wnd->WndRow);
    }
    else if (!TestAttribute(wnd, READONLY))    {
        DoKeyStroke(wnd, c, p2);
        SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    }
    return TRUE;
}
/* ----------- SHIFT_CHANGED Message ---------- */
static void ShiftChangedMsg(WINDOW wnd, PARAM p1)
{
    if (!((int)p1 & (LEFTSHIFT | RIGHTSHIFT)) &&
                                   KeyBoardMarking)    {
        StopMarking(wnd);
        KeyBoardMarking = FALSE;
    }
}
/* ----------- ID_DELETETEXT Command ---------- */
static void DeleteTextCmd(WINDOW wnd)
{
    if (TextBlockMarked(wnd))    {
        char *bbl=TextLine(wnd,wnd->BlkBegLine)+wnd->BlkBegCol;
        char *bel=TextLine(wnd,wnd->BlkEndLine)+wnd->BlkEndCol;
        int len = (int) (bel - bbl);
        SaveDeletedText(wnd, bbl, len);
        wnd->TextChanged = TRUE;
        strcpy(bbl, bel);
        wnd->CurrLine = TextLineNumber(wnd, bbl-wnd->BlkBegCol);
        wnd->CurrCol = wnd->BlkBegCol;
        wnd->WndRow = wnd->BlkBegLine - wnd->wtop;
        if (wnd->WndRow < 0)    {
            wnd->wtop = wnd->BlkBegLine;
            wnd->WndRow = 0;
        }
        SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
        ClearTextBlock(wnd);
        BuildTextPointers(wnd);
    }
}
/* ----------- ID_CLEAR Command ---------- */
static void ClearCmd(WINDOW wnd)
{
    if (TextBlockMarked(wnd))    {
        char *bbl=TextLine(wnd,wnd->BlkBegLine)+wnd->BlkBegCol;
        char *bel=TextLine(wnd,wnd->BlkEndLine)+wnd->BlkEndCol;
        int len = (int) (bel - bbl);
        SaveDeletedText(wnd, bbl, len);
        wnd->CurrLine = TextLineNumber(wnd, bbl);
        wnd->CurrCol = wnd->BlkBegCol;
        wnd->WndRow = wnd->BlkBegLine - wnd->wtop;
        if (wnd->WndRow < 0)    {
            wnd->WndRow = 0;
            wnd->wtop = wnd->BlkBegLine;
        }
        /* ------ change all text lines in block to \n ----- */
        while (bbl < bel)    {
            char *cp = strchr(bbl, '\n');
            if (cp > bel)
                cp = bel;
            strcpy(bbl, cp);
            bel -= (int) (cp - bbl);
            bbl++;
        }
        ClearTextBlock(wnd);
        BuildTextPointers(wnd);
        SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
        SendMessage(wnd, PAINT, 0, 0);
        wnd->TextChanged = TRUE;
    }
}
/* ----------- ID_UNDO Command ---------- */
static void UndoCmd(WINDOW wnd)
{
    if (wnd->DeletedText != NULL)    {
        PasteText(wnd, wnd->DeletedText, wnd->DeletedLength);
        free(wnd->DeletedText);
        wnd->DeletedText = NULL;
        wnd->DeletedLength = 0;
        SendMessage(wnd, PAINT, 0, 0);
    }
}
/* ----------- ID_PARAGRAPH Command ---------- */
static void ParagraphCmd(WINDOW wnd)
{
    int bc, ec, fl, el, Blocked;
    char *bl, *bbl, *bel, *bb;

    el = wnd->BlkEndLine;
    ec = wnd->BlkEndCol;
    if (!TextBlockMarked(wnd))    {
        Blocked = FALSE;
        /* ---- forming paragraph from cursor position --- */
        fl = wnd->wtop + wnd->WndRow;
        bbl = bel = bl = TextLine(wnd, wnd->CurrLine);
        if ((bc = wnd->CurrCol) >= ClientWidth(wnd))
            bc = 0;
        Home(wnd);
        /* ---- locate the end of the paragraph ---- */
        while (*bel)    {
            int blank = TRUE;
            char *bll = bel;
            /* --- blank line marks end of paragraph --- */
            while (*bel && *bel != '\n')    {
                if (*bel != ' ')
                    blank = FALSE;
                bel++;
            }
            if (blank)    {
                bel = bll;
                break;
            }
            if (*bel)
                bel++;
        }
        if (bel == bbl)    {
            SendMessage(wnd, KEYBOARD, DN, 0);
            return;
        }
        if (*bel == '\0')
            --bel;
        if (*bel == '\n')
            --bel;
    }
    else    {
        /* ---- forming paragraph from marked block --- */
        Blocked = TRUE;
        bbl = TextLine(wnd, wnd->BlkBegLine) + wnd->BlkBegCol;
        bel = TextLine(wnd, wnd->BlkEndLine) + wnd->BlkEndCol;
        fl = wnd->BlkBegLine;
        bc = wnd->CurrCol = wnd->BlkBegCol;
        wnd->CurrLine = fl;
        if (fl < wnd->wtop)
            wnd->wtop = fl;
        wnd->WndRow = fl - wnd->wtop;
        SendMessage(wnd, KEYBOARD, '\r', 0);
        el++, fl++;
        if (bc != 0)    {
            SendMessage(wnd, KEYBOARD, '\r', 0);
            el++, fl ++;
        }
        bc = 0;
        bl = TextLine(wnd, fl);
        wnd->CurrLine = fl;
        bbl = bl + bc;
        bel = TextLine(wnd, el) + ec;
    }
    /* --- change all newlines in block to spaces --- */
    while (CurrChar < bel)    {
        if (*CurrChar == '\n')    {
            *CurrChar = ' ';
            wnd->CurrLine++;
            wnd->CurrCol = 0;
        }
        else
            wnd->CurrCol++;
    }
    /* ---- insert newlines at new margin boundaries ---- */
    bb = bbl;
    while (bbl < bel)    {
        bbl++;
        if ((int)(bbl - bb) == ClientWidth(wnd)-1)    {
            while (*bbl != ' ' && bbl > bb)
                --bbl;
            if (*bbl != ' ')    {
                bbl = strchr(bbl, ' ');
                if (bbl == NULL || bbl >= bel)
                    break;
            }
            *bbl = '\n';
            bb = bbl+1;
        }
    }
    ec = (int)(bel - bb);
    BuildTextPointers(wnd);
    if (Blocked)    {
        /* ---- position cursor at end of new paragraph ---- */
        if (el < wnd->wtop ||
                wnd->wtop + ClientHeight(wnd) < el)
            wnd->wtop = el-ClientHeight(wnd);
        if (wnd->wtop < 0)
            wnd->wtop = 0;
        wnd->WndRow = el - wnd->wtop;
        wnd->CurrLine = el;
        wnd->CurrCol = ec;
        SendMessage(wnd, KEYBOARD, '\r', 0);
        SendMessage(wnd, KEYBOARD, '\r', 0);
    }
    else    {
        /* --- put cursor back at beginning --- */
        wnd->CurrLine = TextLineNumber(wnd, bl);
        wnd->CurrCol = bc;
        if (fl < wnd->wtop)
            wnd->wtop = fl;
        wnd->WndRow = fl - wnd->wtop;
    }
    SendMessage(wnd, PAINT, 0, 0);
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    wnd->TextChanged = TRUE;
    BuildTextPointers(wnd);
}
/* ----------- COMMAND Message ---------- */
static int CommandMsg(WINDOW wnd, PARAM p1)
{
    switch ((int)p1)    {
        case ID_DELETETEXT:
            DeleteTextCmd(wnd);
            return TRUE;
        case ID_CLEAR:
            ClearCmd(wnd);
            return TRUE;
        case ID_UNDO:
            UndoCmd(wnd);
            return TRUE;
        case ID_PARAGRAPH:
            ParagraphCmd(wnd);
            return TRUE;
        default:
            break;
    }
    return FALSE;
}
/* ---------- CLOSE_WINDOW Message ----------- */
static void CloseWindowMsg(WINDOW wnd)
{
    SendMessage(NULL, HIDE_CURSOR, 0, 0);
    if (wnd->DeletedText != NULL)
        free(wnd->DeletedText);
}
/* ------- Window processing module for EDITBOX class ------ */
int EditBoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int rtn;
    switch (msg)    {
        case CREATE_WINDOW:
            return CreateWindowMsg(wnd);
        case ADDTEXT:
            return AddTextMsg(wnd, p1, p2);
        case SETTEXT:
            return SetTextMsg(wnd, p1);
        case CLEARTEXT:
            ResetEditBox(wnd);
            break;
        case GETTEXT:
            return GetTextMsg(wnd, p1, p2);
        case SETTEXTLENGTH:
            return SetTextLengthMsg(wnd, (unsigned) p1);
        case KEYBOARD_CURSOR:
            return KeyboardCursorMsg(wnd, p1, p2);
        case SETFOCUS:
        case PAINT:
        case MOVE:
            rtn = BaseWndProc(EDITBOX, wnd, msg, p1, p2);
            SendMessage(wnd,KEYBOARD_CURSOR,WndCol,wnd->WndRow);
            return rtn;
        case SIZE:
            return SizeMsg(wnd, p1, p2);
        case SCROLL:
            return ScrollMsg(wnd, p1);
        case HORIZSCROLL:
            return HorizScrollMsg(wnd, p1);
        case SCROLLPAGE:
            return ScrollPageMsg(wnd, p1);
        case HORIZPAGE:
            return HorizPageMsg(wnd, p1);
        case LEFT_BUTTON:
            if (LeftButtonMsg(wnd, p1, p2))
                return TRUE;
            break;
        case MOUSE_MOVED:
            if (MouseMovedMsg(wnd, p1, p2))
                return TRUE;
            break;
        case BUTTON_RELEASED:
            if (ButtonReleasedMsg(wnd))
                return TRUE;
            break;
        case KEYBOARD:
            if (KeyboardMsg(wnd, p1, p2))
                return TRUE;
            break;
        case SHIFT_CHANGED:
            ShiftChangedMsg(wnd, p1);
            break;
        case COMMAND:
            if (CommandMsg(wnd, p1))
                return TRUE;
            break;
        case CLOSE_WINDOW:
            CloseWindowMsg(wnd);
            break;
        default:
            break;
    }
    return BaseWndProc(EDITBOX, wnd, msg, p1, p2);
}
/* ------ save deleted text for the Undo command ------ */
static void SaveDeletedText(WINDOW wnd, char *bbl, int len)
{
    wnd->DeletedLength = len;
    if ((wnd->DeletedText=realloc(wnd->DeletedText,len))!=NULL)
        memmove(wnd->DeletedText, bbl, len);
}
/* ---- cursor right key: right one character position ---- */
static void Forward(WINDOW wnd)
{
    char *cc = CurrChar+1;
    if (*cc == '\0')
        return;
    if (*CurrChar == '\n')    {
        Home(wnd);
        Downward(wnd);
    }
    else    {
        wnd->CurrCol++;
        if (WndCol == ClientWidth(wnd))
            SendMessage(wnd, HORIZSCROLL, TRUE, 0);
    }
}
/* ----- stick the moving cursor to the end of the line ---- */
static void StickEnd(WINDOW wnd)
{
    char *cp = TextLine(wnd, wnd->CurrLine);
    char *cp1 = strchr(cp, '\n');
    int len = cp1 ? (int) (cp1 - cp) : 0;
    wnd->CurrCol = min(len, wnd->CurrCol);
    if (wnd->wleft > wnd->CurrCol)    {
        wnd->wleft = max(0, wnd->CurrCol - 4);
        SendMessage(wnd, PAINT, 0, 0);
    }
    else if (wnd->CurrCol-wnd->wleft >= ClientWidth(wnd))    {
        wnd->wleft = wnd->CurrCol - (ClientWidth(wnd)-1);
        SendMessage(wnd, PAINT, 0, 0);
    }
}
/* --------- cursor down key: down one line --------- */
static void Downward(WINDOW wnd)
{
    if (isMultiLine(wnd) &&
            wnd->WndRow+wnd->wtop+1 < wnd->wlines)  {
        wnd->CurrLine++;
        if (wnd->WndRow == ClientHeight(wnd)-1)
            SendMessage(wnd, SCROLL, TRUE, 0);
        wnd->WndRow++;
        StickEnd(wnd);
    }
}
/* -------- cursor up key: up one line ------------ */
static void Upward(WINDOW wnd)
{
    if (isMultiLine(wnd) && wnd->CurrLine != 0)    {
        if (wnd->CurrLine > 0)
            --wnd->CurrLine;
        if (wnd->WndRow == 0)
            SendMessage(wnd, SCROLL, FALSE, 0);
        --wnd->WndRow;
        StickEnd(wnd);
    }
}
/* ---- cursor left key: left one character position ---- */
static void Backward(WINDOW wnd)
{
    if (wnd->CurrCol)    {
        if (wnd->CurrCol-- <= wnd->wleft)
            if (wnd->wleft != 0)
                SendMessage(wnd, HORIZSCROLL, FALSE, 0);
    }
    else if (isMultiLine(wnd) && wnd->CurrLine != 0)    {
        Upward(wnd);
        End(wnd);
    }
}
/* -------- End key: to end of line ------- */
static void End(WINDOW wnd)
{
    while (*CurrChar && *CurrChar != '\n')
        ++wnd->CurrCol;
    if (WndCol >= ClientWidth(wnd))    {
        wnd->wleft = wnd->CurrCol - (ClientWidth(wnd)-1);
        SendMessage(wnd, PAINT, 0, 0);
    }
}
/* -------- Home key: to beginning of line ------- */
static void Home(WINDOW wnd)
{
    wnd->CurrCol = 0;
    if (wnd->wleft != 0)    {
        wnd->wleft = 0;
        SendMessage(wnd, PAINT, 0, 0);
    }
}
/* -- Ctrl+cursor right key: to beginning of next word -- */
static void NextWord(WINDOW wnd)
{
    int savetop = wnd->wtop;
    int saveleft = wnd->wleft;
    ClearVisible(wnd);
    while (!isWhite(*CurrChar))    {
        char *cc = CurrChar+1;
        if (*cc == '\0')
            break;
        Forward(wnd);
    }
    while (isWhite(*CurrChar))    {
        char *cc = CurrChar+1;
        if (*cc == '\0')
            break;
        Forward(wnd);
    }
    SetVisible(wnd);
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    if (wnd->wtop != savetop || wnd->wleft != saveleft)
        SendMessage(wnd, PAINT, 0, 0);
}
/* -- Ctrl+cursor left key: to beginning of previous word -- */
static void PrevWord(WINDOW wnd)
{
    int savetop = wnd->wtop;
    int saveleft = wnd->wleft;
    ClearVisible(wnd);
    Backward(wnd);
    while (isWhite(*CurrChar))    {
        if (wnd->CurrLine == 0 && wnd->CurrCol == 0)
            break;
        Backward(wnd);
    }
    while (!isWhite(*CurrChar))    {
        if (wnd->CurrLine == 0 && wnd->CurrCol == 0)
            break;
        Backward(wnd);
    }
    if (isWhite(*CurrChar))
        Forward(wnd);
    SetVisible(wnd);
    if (wnd->wleft != saveleft)
        if (wnd->CurrCol >= saveleft)
            if (wnd->CurrCol - saveleft < ClientWidth(wnd))
                wnd->wleft = saveleft;
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    if (wnd->wtop != savetop || wnd->wleft != saveleft)
        SendMessage(wnd, PAINT, 0, 0);
}
/* ----- reset the text attributes of an EDITBOX ------- */
static void ResetEditBox(WINDOW wnd)
{
    unsigned blen = EditBufLen(wnd)+2;
    wnd->text = realloc(wnd->text, blen);
    memset(wnd->text, 0, blen);
    wnd->wlines = 0;
    wnd->CurrLine = 0;
    wnd->CurrCol = 0;
    wnd->WndRow = 0;
    wnd->wleft = 0;
    wnd->wtop = 0;
    wnd->textwidth = 0;
    wnd->TextChanged = FALSE;
    ClearTextPointers(wnd);
    ClearTextBlock(wnd);
}
/* ----- modify text pointers from a specified position
                by a specified plus or minus amount ----- */
static void ModTextPointers(WINDOW wnd, int lineno, int var)
{
    while (lineno < wnd->wlines)
        *((wnd->TextPointers) + lineno++) += var;
}
```

Copyright © 1992, Dr. Dobb's Journal
