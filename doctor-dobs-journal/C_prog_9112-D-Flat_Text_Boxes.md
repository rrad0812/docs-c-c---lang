
# C PROGRAMMING

## D-Flat Text Boxes - Al Stevens

We are into the eighth month of the D-Flat project now. For those of you who tuned in late, I'll briefly explain the project. D-Flat is a DOS text-mode library that implements the IBM SAA/CUA user interface with an event-driven, message-based programming model. CUA is the interface used in Windows, Presentation Manager, QuickBasic, Turbo products, and many other programs. CUA is fast becoming the de facto standard for interactive software user languages. I started the product last year to provide CUA capabilities to DOS C programmers. My inspiration was Borland's Turbo Vision, which was available with Turbo Pascal. Borland now offers Turbo Vision with Turbo C++, but there is still no version for the Borland C compiler. This is because Turbo Vision is based on the object-oriented classes of Turbo Pascal and C++. There are other libraries that offer CUA for DOS text-mode programmers. D-Flat is an alternative that is free with source code, supports several compilers, and has no restrictions on its use or distribution.

### The D-flat TEXTBOX Class

Last month I described the NORMAL window class, the base class for all others. It manages the messages common to all windows, dealing with window moving, sizing, overlapping, and so on. This month we move to the TEXTBOX class, the next class in the hierarchy, which is a base to several others. A text box is a window that can display text. As you will see in later installments, list boxes, menus, edit boxes, and other classes derive from the text box. A window class that has text in all or part of its client area is likely to be derived from a text box.

Just as the NORMAL class manages operations common to all windows, the TEXTBOX class manages to operations on text in text box windows. The class displays, scrolls, and pages through the text by processing the messages that support those operations.

Text-box Data Fields

There are a number of members in the window structure for a text box that the class uses to manage the text. The structure is defined in dflat.h. The text pointer points to the heap memory where the text is stored. Text in text box consists of displayable lines of text, each terminated by a newline character. The complete body of text is null-terminated. The memory occupied by the text is taken from the heap. The textlen integer is the length of the text buffer, not necessarily the length of the text in the buffer, but greater than or equal to that length. The wlines integer is the number of lines of text in the buffer. The textwidth integer is the length of the longest line in the buffer and is, therefore, the effective width of the text file. The wtop integer is the line number, relative to 0, that is displayed at the top of the window. This value changes when the user scrolls and pages the text. The wleft integer is the column number, relative to 0, that is displayed in the left margin of the window. This value changes when the user scrolls the text horizontally.

The window structure also contains the TextPointers members, which points to an array of integers. The integers are offsets to the lines of text in the buffer. This array is to improve performance in scrolling, paging, and deleting text. It eliminates the need for the program to constantly scan the text, looking for the addresses of lines.

There are four integer values in the window structure that specify the line and column boundaries of a marked block of text, if one exists. These integers are named BlkBegLine, BlkEndLine, BlkBegCol, and BlkEndCol.

Listing One, page 144, is textbox.c, the source file that implements the TEXTBOX class. It begins with a number of functions that process the messages for the text box. Earlier versions of this code and the code for other window classes had the message-processing statements mostly in the cases for the one big switch statement that the class's window-processing module used. Several readers reported to me that the TopSpeed C compiler was bombing out on these big functions. I did not support TopSpeed C at the time, and JPI, the vendor, suggested breaking the program into smaller functions. At the same time, I observed that the Watcom C compiler was reporting that it did not have enough memory to fully optimize the functions with the big switch statements. For these reasons, I decided to put the message-processing code into individual functions. Watcom no longer reports the optimization problems, and the executable from Watcom has shrunk by about 5K. The code is easier to read, too, because there are fewer levels of indenture. If you download the D-Flat library, compare the normal.c source file with the version of it that I published last month. You'll see the difference.

Each function has a comment that says which message the function processes. The window-processing module is TextBoxProc, and you can see that the cases of its switch statement usually just call the functions for the messages. I will discuss each of the messages and you can follow along by finding the function in the listing.

The ADDTEXT message appends text to a text box. Its first parameter is a pointer to the text string to be appended. The window makes its own copy of the text, so a program that sends the ADDTEXT message need not to worry about the string going out of scope while the window is still open.

The SETTEXT message assigns a new block of a text to the text-box window. The first parameter points to the new text, which replaces any existing text in the text box. Just as with the ADDTEXT message, the window makes its own copy of the text string.

The CLEARTEXT message clears the text in a text box window. It frees the text's memory space and sets all the text control fields to 0.

The KEYBOARD message processes keystrokes for the text box. Text boxes react to keys that page or scroll the text. The function that processes these keys simply sends messages to the window that tell it to scroll or page, up or down, horizontally or vertically, a line or a page or the entire document.

The LEFT_BUTTON message also scrolls and pages the text box if the mouse is positioned in the horizontal or vertical scroll bar. If the user clicks a scroll button, this message sends the appropriate SCROOL or HORIZSCROLL message to scroll the text one line or column vertically or horizontally. If the user clicks in the scroll bar but not on the scroll slider box, this message sends the appropriate up or down or left or right page scrolling message. If the user clicks on the scroll slider box, this message sets either the VSliding or H-Sliding indicator so the window knows it is sliding vertically or horizontally. Then the message sends the MOUSE-TRAVEL message to the system to make the mouse stay inside the scroll bar. This technique keeps the mouse cursor from going all over the screen and confusing the user and the rest of the system while the user slides the slider box. This condition will persist until the user releases the mouse button.

The MOUSE_MOVED message is important only if the VSliding or HSliding indicator is set, in which case the program moves the corresponding slider box by writing a SCROLLBARCHAR where the box was previously and the SCROLLBOXCHAR where it is now.

When the BUTTON_RELEASED message occurs with either the VSliding or HSliding indicator set, the program uses the new slider box position to compute new page-display parameters for the text box. The program also sends the MOUSE-TRAVEL message to permit the mouse to roam the entire screen again.

The SCROLL message adjusts the text box's wtop offset. Then, if the window is visible, the message scrolls the window up or down and displays the bottom or top line, depending on which way the scroll is going. Finally, the message computes a new position for the slider box and displays it.

The HORIZCROLL message adjusts the text box's wleft offset and sends the PAINT message to the window.

The SCROLLPAGE and HORIZSCROLLPAGE messages work like the SCROLL and HORIZSCROLL messages, except that they scroll the number of lines or columns visible in the window--one page--and then send the PAINT message.

The SCROLLDOC message scrolls the text box to the beginning or end of the document.

The PAINT message causes the text box to repaint its client area. The first parameter can be a pointer to a RECT structure relative to the screen rectangle of the window. The parameter specifies that the PAINT message is only to paint the portion of the window represented by the RECT structure. If there is no RECT pointer in the parameter, the program computes the rectangle to be the entire client area of the window. For each line in the rectangle, the program calls the WriteTextLine function to display the line. If there are fewer lines of text than will fill the window, the program calls the writeline function to pad the client area with blank lines. Finally, the message computes new positions for the horizontal and vertical scroll bar slider boxes and displays them.

The CLOSE_WINDOW message sends the CLEARTEXT message to remove the text from the window and then frees the memory allocated for the text line pointers.

There are several functions in textbox.c that support the message processing. The ComputeHScrollBox and ComputeVScrollBox functions compute positions on the scroll bars for the slider boxes based on the length and width of the document, the height and width of the text box window, and the current visible portion of the document in the window, as indicated by the wtop and wleft variables. The ComputeWindowTop and Compute WindowLeft functions are the inverse of ComputeHScrollBox and ComputeVScrollBox functions. They compute values for the wtop and wleft variables from the current slider box positions.

The GetTextline function locates a specified line of text in the document buffer, allocates enough memory from the heap to hold the line, copies the line from the document buffer to the allocated space, and returns the address of the allocated line buffer. This function is used only from within the code in textbox.c. The calling function must free the allocated memory. The function uses the TextLine macro to locate the address of the line in the document buffer. That macro is defined in dflat.h. It uses the specified line number, the address of the document buffer, and the array of offsets pointed to by the TextPointers member of the window structure.

The WriteTextLine function is a complicated piece of code. Its job is to prepare a displayable buffer for a specified line of text in the text box's document buffer. The displayable buffer will be clipped on either or both ends if the RECT parameter does not encompass the whole line. The text line or a portion of it might fall within a currently selected block, and the function must insert the appropriate color controls into the text line for the video functions to use to display the text. D-Flat assumes that a window's text will display with its default client area colors unless an escape sequence appears in the text.

This sequence, which must displace no screen character positions, consists of a CHANGECOLOR character value followed by a foreground color byte and a background color byte. This sequence will tell the display functions to switch to the specified color until it finds a RESETCOLOR control byte or reaches the end of the text line. These control bytes must be taken into consideration when the function is mapping the clipping rectangle onto the text line. This is one of those pieces of code that defies description. It took me a long time to get it working, and I am not sure I could describe its operation now. I am not sure I even understand it any more. I pray regularly that it never needs work and will fight to the bitter end any proposed modification to D-Flat that would affect this code. I counsel you to never paint yourself into such a code corner, but I know you will from time to time.

The SetAnchor function accepts column and row coordinates for the window and sets the anchor point for the marking of a block of text.

The ClearTextPointers function establishes the TextPointers array by reallocating it to the size of a single offset integer, which is given a 0 value. The BuildTextPointers function scans the document text buffer looking for the addresses of the beginnings of text lines. It puts offset values into the TextPointers array for each line found. The program calls this function whenever some editing operation--deleting a block, for example--changes the buffer in a way that would alter the text line offsets.

### How to Get D-Flat Now

The D-Flat source code is on CompuServe in Library 0 of the DDJ Forum and on M&T Online. Its name is DFLAtn.-ARC, where the n is an integer that represents a loosely assigned version number. There is another file, named DFnTXT.ARC. It includes a README. DOC file that describes the changes and how to build the software. There is a calendar in the README.DOC file that shows the past and scheduled monthly columns and the D-Flat subjects that they address. The DFnTXT.ARC file also contains the Help system database and the documentation for the programmer's API. D-Flat compiles with Turbo C 2.0, Borland C++ 2.0, Microsoft C 6.0, and Watcom C 8.0. There are makefiles for the TC, MSC, and Watcom compilers. There is an example program, the MEMOPAD program, which is a multiple document notepad.

If you cannot use the online service, send me a formatted diskette--360K or 720K--and an addressed, stamped diskette mailer. Send it to me in care of DDJ. I'll send you the latest copy of the library. The software is free, but if you care to, stick a dollar bill in the mailer for the Brevard County Food Bank. They take care of homeless and hungry families. We've collected about $375 so far from generous D-Flat "careware" users. I took a pile of money over there today. They are very grateful.

If you want to discuss D-Flat with me, use CompuServe. My CompuServe ID is 71101,1262, and I monitor the DDJ Forum daily.

[LISTING ONE]

```c
/* ------------- textbox.c ------------ */

#include "dflat.h"

#ifdef INCLUDE_SCROLLBARS
static void ComputeWindowTop(WINDOW);
static void ComputeWindowLeft(WINDOW);
static int ComputeVScrollBox(WINDOW);
static int ComputeHScrollBox(WINDOW);
static void MoveScrollBox(WINDOW, int);
#endif
static char *GetTextLine(WINDOW, int);

#ifdef INCLUDE_SCROLLBARS
int VSliding;
int HSliding;
#endif

/* ------------ ADDTEXT Message -------------- */
static void AddTextMsg(WINDOW wnd, PARAM p1)
{
    /* --- append text to the textbox's buffer --- */
    unsigned adln = strlen((char *)p1);
    if (adln > (unsigned)0xfff0)
        return;
    if (wnd->text != NULL)    {
        /* ---- appending to existing text ---- */
        unsigned txln = strlen(wnd->text);
        if ((long)txln+adln > (unsigned) 0xfff0)
            return;
        if (txln+adln > wnd->textlen)    {
            wnd->text = realloc(wnd->text, txln+adln+3);
            wnd->textlen = txln+adln+1;
        }
    }
    else    {
        /* ------ 1st text appended ------ */
        wnd->text = calloc(1, adln+3);
        wnd->textlen = adln+1;
    }
    if (wnd->text != NULL)    {
        /* ---- append the text ---- */
        strcat(wnd->text, (char*) p1);
        strcat(wnd->text, "\n");
        BuildTextPointers(wnd);
    }
}

/* ------------ SETTEXT Message -------------- */
static void SetTextMsg(WINDOW wnd, PARAM p1)
{
    /* -- assign new text value to textbox buffer -- */
    char *cp;
    unsigned int len;
    cp = (void *) p1;
    len = strlen(cp)+1;
    if (wnd->text == NULL || wnd->textlen < len)    {
        wnd->textlen = len;
        if ((wnd->text=realloc(wnd->text, len+1)) == NULL)
            return;
        wnd->text[len] = '\0';
    }
    strcpy(wnd->text, cp);
    BuildTextPointers(wnd);
}

/* ------------ CLEARTEXT Message -------------- */
static void ClearTextMsg(WINDOW wnd)
{
    /* ----- clear text from textbox ----- */
    if (wnd->text != NULL)
        free(wnd->text);
    wnd->text = NULL;
    wnd->textlen = 0;
    wnd->wlines = 0;
    wnd->textwidth = 0;
    wnd->wtop = wnd->wleft = 0;
    ClearBlock(wnd);
    ClearTextPointers(wnd);
}

/* ------------ KEYBOARD Message -------------- */
static int KeyboardMsg(WINDOW wnd, PARAM p1)
{
    switch ((int) p1)    {
        case UP:
            return SendMessage(wnd,SCROLL,FALSE,0);
        case DN:
            return SendMessage(wnd,SCROLL,TRUE,0);
        case FWD:
            return SendMessage(wnd,HORIZSCROLL,TRUE,0);
        case BS:
            return SendMessage(wnd,HORIZSCROLL,FALSE,0);
        case PGUP:
            return SendMessage(wnd,SCROLLPAGE,FALSE,0);
        case PGDN:
            return SendMessage(wnd,SCROLLPAGE,TRUE,0);
        case CTRL_PGUP:
            return SendMessage(wnd,HORIZPAGE,FALSE,0);
        case CTRL_PGDN:
            return SendMessage(wnd,HORIZPAGE,TRUE,0);
        case HOME:
            return SendMessage(wnd,SCROLLDOC,TRUE,0);
        case END:
            return SendMessage(wnd,SCROLLDOC,FALSE,0);
        default:
            break;
    }
    return FALSE;
}

#ifdef INCLUDE_SCROLLBARS
/* ------------ LEFT_BUTTON Message -------------- */
static int LeftButtonMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int mx = (int) p1 - GetLeft(wnd);
    int my = (int) p2 - GetTop(wnd);
    if (TestAttribute(wnd, VSCROLLBAR) &&
                        mx == WindowWidth(wnd)-1)    {
        /* -------- in the right border ------- */
        if (my == 0 || my == ClientHeight(wnd)+1)
            /* --- above or below the scroll bar --- */
            return FALSE;
        if (my == 1)
            /* -------- top scroll button --------- */
            return SendMessage(wnd, SCROLL, FALSE, 0);
        if (my == ClientHeight(wnd))
            /* -------- bottom scroll button --------- */
            return SendMessage(wnd, SCROLL, TRUE, 0);
        /* ---------- in the scroll bar ----------- */
        if (!VSliding && my-1 == wnd->VScrollBox)    {
            RECT rc;
            VSliding = TRUE;
            rc.lf = rc.rt = GetRight(wnd);
            rc.tp = GetTop(wnd)+2;
            rc.bt = GetBottom(wnd)-2;
            return SendMessage(NULL, MOUSE_TRAVEL,
                (PARAM) &rc, 0);
        }
        if (my-1 < wnd->VScrollBox)
            return SendMessage(wnd,SCROLLPAGE,FALSE,0);
        if (my-1 > wnd->VScrollBox)
            return SendMessage(wnd,SCROLLPAGE,TRUE,0);
    }
    if (TestAttribute(wnd, HSCROLLBAR) &&
                        my == WindowHeight(wnd)-1) {
        /* -------- in the bottom border ------- */
        if (mx == 0 || my == ClientWidth(wnd)+1)
            /* ------  outside the scroll bar ---- */
            return FALSE;
        if (mx == 1)
            return SendMessage(wnd, HORIZSCROLL,FALSE,0);
        if (mx == WindowWidth(wnd)-2)
            return SendMessage(wnd, HORIZSCROLL,TRUE,0);
        if (!HSliding && mx-1 == wnd->HScrollBox)    {
            /* --- hit the scroll box --- */
            RECT rc;
            rc.lf = GetLeft(wnd)+2;
            rc.rt = GetRight(wnd)-2;
            rc.tp = rc.bt = GetBottom(wnd);
            /* - keep the mouse in the scroll bar - */
            SendMessage(NULL,MOUSE_TRAVEL,(PARAM)&rc,0);
            HSliding = TRUE;
            return TRUE;
        }
        if (mx-1 < wnd->HScrollBox)
            return SendMessage(wnd,HORIZPAGE,FALSE,0);
        if (mx-1 > wnd->HScrollBox)
            return SendMessage(wnd,HORIZPAGE,TRUE,0);
    }
    return FALSE;
}

/* ------------ MOUSE_MOVED Message -------------- */
static int MouseMovedMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int mx = (int) p1 - GetLeft(wnd);
    int my = (int) p2 - GetTop(wnd);
    if (VSliding)    {
        /* ---- dragging the vertical scroll box --- */
        if (my-1 != wnd->VScrollBox)    {
            foreground = FrameForeground(wnd);
            background = FrameBackground(wnd);
            PutWindowChar(wnd, WindowWidth(wnd)-1,
                    wnd->VScrollBox+1,SCROLLBARCHAR);
            wnd->VScrollBox = my-1;
            PutWindowChar(wnd, WindowWidth(wnd)-1,
                    my, SCROLLBOXCHAR);
        }
        return TRUE;
    }
    if (HSliding)    {
        /* --- dragging the horizontal scroll box --- */
        if (mx-1 != wnd->HScrollBox)    {
            foreground = FrameForeground(wnd);
            background = FrameBackground(wnd);
            PutWindowChar(wnd, wnd->HScrollBox+1,
                    WindowHeight(wnd)-1, SCROLLBARCHAR);
            wnd->HScrollBox = mx-1;
            PutWindowChar(wnd, mx, WindowHeight(wnd)-1,
                    SCROLLBOXCHAR);
        }
        return TRUE;
    }
    return FALSE;
}

/* ------------ BUTTON_RELEASED Message -------------- */
static void ButtonReleasedMsg(WINDOW wnd)
{
    if (HSliding || VSliding)    {
        RECT rc;
        rc.lf = rc.tp = 0;
        rc.rt = SCREENWIDTH-1;
        rc.bt = SCREENHEIGHT-1;
        /* release the mouse ouside the scroll bar */
        SendMessage(NULL, MOUSE_TRAVEL, (PARAM) &rc, 0);
            VSliding ? ComputeWindowTop(wnd) :
                            ComputeWindowLeft(wnd);
        SendMessage(wnd, PAINT, 0, 0);
        SendMessage(wnd, KEYBOARD_CURSOR, 0, 0);
        VSliding = HSliding = FALSE;
    }
}
#endif

/* ------------ SCROLL Message -------------- */
static int ScrollMsg(WINDOW wnd, PARAM p1)
{
    /* ---- vertical scroll one line ---- */
    if (p1)    {
        /* ----- scroll one line up ----- */
        if (wnd->wtop+ClientHeight(wnd) >= wnd->wlines)
            return FALSE;
        wnd->wtop++;
    }
    else    {
        /* ----- scroll one line down ----- */
        if (wnd->wtop == 0)
            return FALSE;
        --wnd->wtop;
    }
    if (isVisible(wnd))    {
        RECT rc;
        rc = ClipRectangle(wnd, ClientRect(wnd));
        if (ValidRect(rc))    {
            /* ---- scroll the window ----- */
            scroll_window(wnd, rc, (int)p1);
            if (!(int)p1)
                /* -- write top line (down) -- */
                WriteTextLine(wnd,NULL,wnd->wtop,FALSE);
            else    {
                /* -- write bottom line (up) -- */
                int y=RectBottom(rc)-GetClientTop(wnd);
                WriteTextLine(wnd, NULL,
                    wnd->wtop+y, FALSE);
            }
        }
#ifdef INCLUDE_SCROLLBARS
        /* ---- reset the scroll box ---- */
        if (TestAttribute(wnd, VSCROLLBAR))    {
            int vscrollbox = ComputeVScrollBox(wnd);
            if (vscrollbox != wnd->VScrollBox)
                MoveScrollBox(wnd, vscrollbox);
        }
#endif
        return TRUE;
    }
    return FALSE;
}

/* ------------ HORIZSCROLL Message -------------- */
static void HorizScrollMsg(WINDOW wnd, PARAM p1)
{
    /* --- horizontal scroll one column --- */
    if (p1)    {
        /* --- scroll left --- */
        if (wnd->wleft + ClientWidth(wnd)-1 <
                    wnd->textwidth)
            wnd->wleft++;
    }
    else
        /* --- scroll right --- */
        if (wnd->wleft > 0)
            --wnd->wleft;
    SendMessage(wnd, PAINT, 0, 0);
}

/* ------------  SCROLLPAGE Message -------------- */
static void ScrollPageMsg(WINDOW wnd, PARAM p1)
{
    /* --- vertical scroll one page --- */
    if ((int) p1 == FALSE)    {
        /* ---- page up ---- */
        if (wnd->wtop)    {
            wnd->wtop -= ClientHeight(wnd);
            if (wnd->wtop < 0)
                wnd->wtop = 0;
        }
    }
    else     {
        /* ---- page down ---- */
        if (wnd->wtop+ClientHeight(wnd) < wnd->wlines) {
            wnd->wtop += ClientHeight(wnd);
            if (wnd->wtop>wnd->wlines-ClientHeight(wnd))
                wnd->wtop=wnd->wlines-ClientHeight(wnd);
        }
    }
    SendMessage(wnd, PAINT, 0, 0);
}

/* ------------ HORIZSCROLLPAGE Message -------------- */
static void HorizScrollPageMsg(WINDOW wnd, PARAM p1)
{
    /* --- horizontal scroll one page --- */
    if ((int) p1 == FALSE)    {
        /* ---- page left ----- */
        wnd->wleft -= ClientWidth(wnd);
        if (wnd->wleft < 0)
            wnd->wleft = 0;
    }
    else    {
        /* ---- page right ----- */
        wnd->wleft += ClientWidth(wnd);
        if (wnd->wleft>wnd->textwidth-ClientWidth(wnd))
            wnd->wleft=wnd->textwidth-ClientWidth(wnd);
    }
    SendMessage(wnd, PAINT, 0, 0);
}

/* ------------ SCROLLDOC Message -------------- */
static void ScrollDocMsg(WINDOW wnd, PARAM p1)
{
    /* --- scroll to beginning or end of document --- */
    if ((int) p1)
        wnd->wtop = wnd->wleft = 0;
    else if (wnd->wtop+ClientHeight(wnd) < wnd->wlines){
        wnd->wtop = wnd->wlines-ClientHeight(wnd);
        wnd->wleft = 0;
    }
    SendMessage(wnd, PAINT, 0, 0);
}

/* ------------ PAINT Message -------------- */
static int PaintMsg(WINDOW wnd, PARAM p1)
{
    /* ------ paint the client area ----- */
    RECT rc, rcc;
    int y;
    char blankline[201];

    /* ----- build the rectangle to paint ----- */
    if ((RECT *)p1 == NULL)
        rc=RelativeWindowRect(wnd, WindowRect(wnd));
    else
        rc= *(RECT *)p1;
    if (TestAttribute(wnd, HASBORDER) &&
            RectRight(rc) >= WindowWidth(wnd)-1) {
        if (RectLeft(rc) >= WindowWidth(wnd)-1)
            return FALSE;
        RectRight(rc) = WindowWidth(wnd)-2;
    }
    rcc = AdjustRectangle(wnd, rc);

    /* ----- blank line for padding ----- */
    memset(blankline, ' ', SCREENWIDTH);
    blankline[RectRight(rcc)+1] = '\0';

    /* ------- each line within rectangle ------ */
    for (y = RectTop(rc); y <= RectBottom(rc); y++){
        int yy;
        /* ---- test outside of Client area ---- */
        if (TestAttribute(wnd,
                    HASBORDER | HASTITLEBAR))    {
            if (y < TopBorderAdj(wnd))
                continue;
            if (y > WindowHeight(wnd)-2)
                continue;
        }
        yy = y-TopBorderAdj(wnd);
        if (yy < wnd->wlines-wnd->wtop)
            /* ---- paint a text line ---- */
            WriteTextLine(wnd, &rc,
                        yy+wnd->wtop, FALSE);
        else    {
            /* ---- paint a blank line ---- */
            SetStandardColor(wnd);
            writeline(wnd, blankline+RectLeft(rcc),
                    RectLeft(rcc)+1, y, FALSE);
        }
    }
#ifdef INCLUDE_SCROLLBARS
    /* ------- position the scroll box ------- */
    if (TestAttribute(wnd, VSCROLLBAR|HSCROLLBAR)) {
        int hscrollbox = ComputeHScrollBox(wnd);
        int vscrollbox = ComputeVScrollBox(wnd);
        if (hscrollbox != wnd->HScrollBox ||
                vscrollbox != wnd->VScrollBox)    {
            wnd->HScrollBox = hscrollbox;
            wnd->VScrollBox = vscrollbox;
            SendMessage(wnd, BORDER, p1, 0);
        }
    }
#endif
    return TRUE;
}

/* ------------ CLOSE_WINDOW Message -------------- */
static void CloseWindowMsg(WINDOW wnd)
{
    SendMessage(wnd, CLEARTEXT, 0, 0);
    if (wnd->TextPointers != NULL)    {
        free(wnd->TextPointers);
        wnd->TextPointers = NULL;
    }
}

/* ----------- TEXTBOX Message-processing Module ----------- */
int TextBoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:
            wnd->HScrollBox = wnd->VScrollBox = 1;
            ClearTextPointers(wnd);
            break;
        case ADDTEXT:
            AddTextMsg(wnd, p1);
            break;
        case SETTEXT:
            SetTextMsg(wnd, p1);
            break;
        case CLEARTEXT:
            ClearTextMsg(wnd);
            break;
        case KEYBOARD:
#ifdef INCLUDE_SYSTEM_MENUS
            if (WindowMoving || WindowSizing)
                return FALSE;
#endif
            if (KeyboardMsg(wnd, p1))
                return TRUE;
            break;
        case LEFT_BUTTON:
#ifdef INCLUDE_SYSTEM_MENUS
            if (WindowSizing || WindowMoving)
                return FALSE;
#endif
#ifdef INCLUDE_SCROLLBARS
            if (LeftButtonMsg(wnd, p1, p2))
                return TRUE;
            break;
        case MOUSE_MOVED:
            if (MouseMovedMsg(wnd, p1, p2))
                return TRUE;
            break;
        case BUTTON_RELEASED:
            ButtonReleasedMsg(wnd);
#endif
            break;
        case SCROLL:
            ScrollMsg(wnd, p1);
            return TRUE;
        case HORIZSCROLL:
            HorizScrollMsg(wnd, p1);
            return TRUE;
        case SCROLLPAGE:
            ScrollPageMsg(wnd, p1);
            return TRUE;
        case HORIZPAGE:
            HorizScrollPageMsg(wnd, p1);
            return TRUE;
        case SCROLLDOC:
            ScrollDocMsg(wnd, p1);
            return TRUE;
        case PAINT:
            if (isVisible(wnd) && wnd->wlines)    {
                PaintMsg(wnd, p1);
                return FALSE;
            }
            break;
        case CLOSE_WINDOW:
            CloseWindowMsg(wnd);
            break;
        default:
            break;
    }
    return BaseWndProc(TEXTBOX, wnd, msg, p1, p2);
}

#ifdef INCLUDE_SCROLLBARS
/* ------ compute the vertical scroll box position from
                   the text pointers --------- */
static int ComputeVScrollBox(WINDOW wnd)
{
    int pagelen = wnd->wlines - ClientHeight(wnd);
    int barlen = ClientHeight(wnd)-2;
    int lines_tick;
    int vscrollbox;

    if (pagelen < 1 || barlen < 1)
        vscrollbox = 1;
    else    {
        if (pagelen > barlen)
            lines_tick = pagelen / barlen;
        else
            lines_tick = barlen / pagelen;
        vscrollbox = 1 + (wnd->wtop / lines_tick);
        if (vscrollbox > ClientHeight(wnd)-2 ||
                wnd->wtop + ClientHeight(wnd) >= wnd->wlines)
            vscrollbox = ClientHeight(wnd)-2;
    }
    return vscrollbox;
}

/* ---- compute top text line from scroll box position ---- */
static void ComputeWindowTop(WINDOW wnd)
{
    int pagelen = wnd->wlines - ClientHeight(wnd);
    if (wnd->VScrollBox == 0)
        wnd->wtop = 0;
    else if (wnd->VScrollBox == ClientHeight(wnd)-2)
        wnd->wtop = pagelen;
    else    {
        int barlen = ClientHeight(wnd)-2;
        int lines_tick;

        if (pagelen > barlen)
            lines_tick = pagelen / barlen;
        else
            lines_tick = barlen / pagelen;
        wnd->wtop = (wnd->VScrollBox-1) * lines_tick;
        if (wnd->wtop + ClientHeight(wnd) > wnd->wlines)
            wnd->wtop = pagelen;
    }
    if (wnd->wtop < 0)
        wnd->wtop = 0;
}

/* ------ compute the horizontal scroll box position from
                   the text pointers --------- */
static int ComputeHScrollBox(WINDOW wnd)
{
    int pagewidth = wnd->textwidth - ClientWidth(wnd);
    int barlen = ClientWidth(wnd)-2;
    int chars_tick;
    int hscrollbox;

    if (pagewidth < 1 || barlen < 1)
        hscrollbox = 1;
    else     {
        if (pagewidth > barlen)
            chars_tick = pagewidth / barlen;
        else
            chars_tick = barlen / pagewidth;
        hscrollbox = 1 + (wnd->wleft / chars_tick);
        if (hscrollbox > ClientWidth(wnd)-2 ||
                wnd->wleft + ClientWidth(wnd) >= wnd->textwidth)
            hscrollbox = ClientWidth(wnd)-2;
    }
    return hscrollbox;
}

/* ---- compute left column from scroll box position ---- */
static void ComputeWindowLeft(WINDOW wnd)
{
    int pagewidth = wnd->textwidth - ClientWidth(wnd);

    if (wnd->HScrollBox == 0)
        wnd->wleft = 0;
    else if (wnd->HScrollBox == ClientWidth(wnd)-2)
        wnd->wleft = pagewidth;
    else    {
        int barlen = ClientWidth(wnd)-2;
        int chars_tick;

        if (pagewidth > barlen)
            chars_tick = pagewidth / barlen;
        else
            chars_tick = barlen / pagewidth;
        wnd->wleft = (wnd->HScrollBox-1) * chars_tick;
        if (wnd->wleft + ClientWidth(wnd) > wnd->textwidth)
            wnd->wleft = pagewidth;
    }
    if (wnd->wleft < 0)
        wnd->wleft = 0;
}
#endif

/* ----- get the text to a specified line ----- */
static char *GetTextLine(WINDOW wnd, int selection)
{
    char *line;
    int len = 0;
    char *cp, *cp1;
    cp = cp1 = TextLine(wnd, selection);
    while (*cp && *cp != '\n')    {
        len++;
        cp++;
    }
    line = malloc(len+6);
    if (line != NULL)    {
        memmove(line, cp1, len);
        line[len] = '\0';
    }
    return line;
}

/* ------- write a line of text to a textbox window ------- */
void WriteTextLine(WINDOW wnd, RECT *rcc, int y, int reverse)
{
    int len = 0;
    int dif = 0;
    unsigned char *line;
    RECT rc;
    unsigned char *lp, *svlp;
    int lnlen;
    int i;
    int trunc = FALSE;

    /* ------ make sure y is inside the window ----- */
    if (y < wnd->wtop || y >= wnd->wtop+ClientHeight(wnd))
        return;

    /* ---- build the retangle within which can write ---- */
    if (rcc == NULL)    {
        rc = RelativeWindowRect(wnd, WindowRect(wnd));
        if (TestAttribute(wnd, HASBORDER) &&
                RectRight(rc) >= WindowWidth(wnd)-1)
            RectRight(rc) = WindowWidth(wnd)-2;
    }
    else
        rc = *rcc;

    /* ----- make sure rectangle is within window ------ */
    if (RectLeft(rc) >= WindowWidth(wnd)-1)
        return;
    if (RectRight(rc) == 0)
        return;
    rc = AdjustRectangle(wnd, rc);
    if (y-wnd->wtop<RectTop(rc) || y-wnd->wtop>RectBottom(rc))
        return;

    /* --- get the text and length of the text line --- */
    lp = svlp = GetTextLine(wnd, y);
    if (svlp == NULL)
        return;
    lnlen = LineLength(lp);

    /* -------- insert block color change controls ------- */
    if (BlockMarked(wnd))    {
        int bbl = wnd->BlkBegLine;
        int bel = wnd->BlkEndLine;
        int bbc = wnd->BlkBegCol;
        int bec = wnd->BlkEndCol;
        int by = y;

        /* ----- put lowest marker first ----- */
        if (bbl > bel)    {
            swap(bbl, bel);
            swap(bbc, bec);
        }
        if (bbl == bel && bbc > bec)
            swap(bbc, bec);

        if (by >= bbl && by <= bel)    {
            /* ------ the block includes this line ----- */
            int blkbeg = 0;
            int blkend = lnlen;
            if (!(by > bbl && by < bel))    {
                /* --- the entire line is not in the block -- */
                if (by == bbl)
                    /* ---- the block begins on this line --- */
                    blkbeg = bbc;
                if (by == bel)
                    /* ---- the block ends on this line ---- */
                    blkend = bec;
            }
            /* ----- insert the reset color token ----- */
            memmove(lp+blkend+1,lp+blkend,strlen(lp+blkend)+1);
            lp[blkend] = RESETCOLOR;
            /* ----- insert the change color token ----- */
            memmove(lp+blkbeg+3,lp+blkbeg,strlen(lp+blkbeg)+1);
            lp[blkbeg] = CHANGECOLOR;
            /* ----- insert the color tokens ----- */
            SetReverseColor(wnd);
            lp[blkbeg+1] = foreground | 0x80;
            lp[blkbeg+2] = background | 0x80;
            lnlen += 4;
        }
    }
    /* - make sure left margin doesn't overlap color change - */
    for (i = 0; i < wnd->wleft+3; i++)    {
        if (*(lp+i) == '\0')
            break;
        if (*(unsigned char *)(lp + i) == RESETCOLOR)
            break;
    }
    if (*(lp+i) && i < wnd->wleft+3)    {
        if (wnd->wleft+4 > lnlen)
            trunc = TRUE;
        else
            lp += 4;
    }
    else     {
        /* --- it does, shift the color change over --- */
        for (i = 0; i < wnd->wleft; i++)    {
            if (*(lp+i) == '\0')
                break;
            if (*(unsigned char *)(lp + i) == CHANGECOLOR)    {
                *(lp+wnd->wleft+2) = *(lp+i+2);
                *(lp+wnd->wleft+1) = *(lp+i+1);
                *(lp+wnd->wleft) = *(lp+i);
                break;
            }
        }
    }
    /* ------ build the line to display -------- */
    if ((line = malloc(200)) != NULL)    {
        if (!trunc)    {
            if (lnlen < wnd->wleft)
                lnlen = 0;
            else
                lp += wnd->wleft;
            if (lnlen > RectLeft(rc))    {
                /* ---- the line exceeds the rectangle ---- */
                int ct = RectLeft(rc);
                char *initlp = lp;
                /* --- point to end of clipped line --- */
                while (ct)    {
                    if (*(unsigned char *)lp == CHANGECOLOR)
                        lp += 3;
                    else if (*(unsigned char *)lp == RESETCOLOR)
                        lp++;
                    else
                        lp++, --ct;
                }
                if (RectLeft(rc))    {
                    char *lpp = lp;
                    while (*lpp)    {
                        if (*(unsigned char*)lpp==CHANGECOLOR)
                            break;
                        if (*(unsigned char*)lpp==RESETCOLOR) {
                            lpp = lp;
                            while (lpp >= initlp)    {
                                if (*(unsigned char *)lpp ==
                                                CHANGECOLOR) {
                                    lp -= 3;
                                    memmove(lp,lpp,3);
                                    break;
                                }
                                --lpp;
                            }
                            break;
                        }
                        lpp++;
                    }
                }
                lnlen = LineLength(lp);
                len = min(lnlen, RectWidth(rc));
                dif = strlen(lp) - lnlen;
                len += dif;
                if (len > 0)
                    strncpy(line, lp, len);
            }
        }
        /* -------- pad the line --------- */
        while (len < RectWidth(rc)+dif)
            line[len++] = ' ';
        line[len] = '\0';
        dif = 0;
        /* ------ establish the line's main color ----- */
        if (reverse)    {
            char *cp = line;
            SetReverseColor(wnd);
            while ((cp = strchr(cp, CHANGECOLOR)) != NULL)    {
                cp += 2;
                *cp++ = background | 0x80;
            }
            if (*(unsigned char *)line == CHANGECOLOR)
                dif = 3;
        }
        else
            SetStandardColor(wnd);
        /* ------- display the line -------- */
        writeline(wnd, line+dif,
                    RectLeft(rc)+BorderAdj(wnd),
                        y-wnd->wtop+TopBorderAdj(wnd), FALSE);
        free(line);
    }
    free(svlp);
}

/* ----- set anchor point for marking text block ----- */
void SetAnchor(WINDOW wnd, int mx, int my)
{
    if (BlockMarked(wnd))    {
        ClearBlock(wnd);
        SendMessage(wnd, PAINT, 0, 0);
    }
    /* ------ set the anchor ------ */
    wnd->BlkBegLine = wnd->BlkEndLine = my;
    wnd->BlkBegCol = wnd->BlkEndCol = mx;
}

/* ----- clear and initialize text line pointer array ----- */
void ClearTextPointers(WINDOW wnd)
{
    wnd->TextPointers = realloc(wnd->TextPointers, sizeof(int));
    if (wnd->TextPointers != NULL)
        *(wnd->TextPointers) = 0;
}

#define INITLINES 100

/* ---- build array of pointers to text lines ---- */
void BuildTextPointers(WINDOW wnd)
{
    char *cp = wnd->text, *cp1;
    int incrs = INITLINES;
    unsigned int off;
    wnd->textwidth = wnd->wlines = 0;
    while (*cp)    {
        if (incrs == INITLINES)    {
            incrs = 0;
            wnd->TextPointers = realloc(wnd->TextPointers,
                    (wnd->wlines + INITLINES) * sizeof(int));
            if (wnd->TextPointers == NULL)
                break;
        }
        off = (unsigned int) (cp - wnd->text);
        *((wnd->TextPointers) + wnd->wlines) = off;
        wnd->wlines++;
        incrs++;
        cp1 = cp;
        while (*cp && *cp != '\n')
            cp++;
        wnd->textwidth = max(wnd->textwidth,
                        (unsigned int) (cp - cp1));
        if (*cp)
            cp++;
    }
}

#ifdef INCLUDE_SCROLLBARS
static void MoveScrollBox(WINDOW wnd, int vscrollbox)
{
    foreground = FrameForeground(wnd);
    background = FrameBackground(wnd);
    PutWindowChar(wnd, WindowWidth(wnd)-1,
            wnd->VScrollBox+1, SCROLLBARCHAR);
    PutWindowChar(wnd, WindowWidth(wnd)-1,
            vscrollbox+1, SCROLLBOXCHAR);
    wnd->VScrollBox = vscrollbox;
}
#endif
```

Copyright © 1991, Dr. Dobb's Journal
