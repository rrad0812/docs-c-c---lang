
# C PROGRAMMING

## D-Flat Lists and Logs - Al Stevens

This month continues the D-Flat saga by describing the LISTBOX window class. A list box is a window that contains a list of one-line entries, and it is the base class for the D-Flat pop-down menus. You will also find list boxes as controls on dialog boxes, and it is possible to create a document window that is itself a list box. The D-Flat message log is a debugging aid that the Memopad example program uses, but it also serves as an example of a dialog box that has a list-box control with multiple-line selection. This column addresses the LISTBOX class and describes the message log.

### The LISTBOX Window Class

A list box is a window of or derived from the LISTBOX class. The user selects an item from a list box with the mouse or by using the up and down arrow keys to move the bar cursor to the desired item and pressing the Enter key. When you use pop-down menus, for example, you are using a list box.

A list box can have more items than the window will display, in which case the user can scroll through them with the keyboard or with scroll bars. The LISTBOX class is derived from the TEXTBOX class, so it inherits most of the text viewing features and messages from the text box and adds a few of its own.

Extended Selections

A list box can have the MULTILINE attribute, which means that the user can select multiple entries from the list at one time. This process is called extended selection in CUA lingo. You can select individual items and groups of items. An extended-selection list box can have many groups of selected items marked for selection before the user is ready to process the list. Here's how it works.

A program adds items to a list box by using the ADDTEXT message. Each of the text entries in an extended-selection list box will have a space in the first character. That position is reserved for a mark that identifies the item as being selected. The mark character is defined in dflat.h by the LISTSELECTOR global symbol, and it displays a small diamond character. You could predefine a list-box item by putting the LISTSELECTOR character in the first position of the item's text entry before you add it to the list box with the ADDTEXT message.

A user selects items in a group from the extended-selection list box by putting the bar cursor on the first item, holding the Shift key down, and moving the cursor to the last item in the group. You can move up and down this way, changing the range of the group in either direction. The program will mark each selection in the group with the diamond so you can see how the selection is progressing. When the group is marked the way you want it, press the Enter key. The program retrieves the selected items and processes them. If you release the Shift key and move the cursor, the selected group is deselected, the diamonds are erased, and the procedure starts over.

To select a group with the mouse, click on the first item, hold the Shift key down, and click on the last item. You can drag the mouse up and down with the Shift key held down, and you can use the scroll bar to scroll to the last item before clicking it.

### Add Mode

The extended-selection procedures just described work with one group at a time. There will be occasions when the user needs to select several groups or scattered individual selections from the list. To do this, you must enter the list box's add mode. The Shift+F8 key toggles the add mode. When a list box is in add mode, the user can make persistent individual selections by moving the cursor bar and pressing the space bar. The space bar also deselects a selected item, so you can think of it as a toggle. Moving the cursor bar does not clear existing selected groups, so the user can select multiple groups as well by moving to the first item in the new group, selecting the item with the space bar, and then holding down the Shift key to move to the last item in the group.

To select multiple items and groups with the mouse, hold down the Ctrl key while you select items and groups. Release the Ctrl key and click an item to erase the existing groups.

The user needs to know when a list box is in add mode and when it is not. When a list box goes into add mode, it tells its parent to display the "Add Mode" text string in its status bar. Most D-Flat applications will have a status bar at the bottom of the application window. The D-Flat ADDSTATUS message ripples upward from parent to parent until a window intercepts and processes it, so any window can send the ADDSTATUS message to its parent, figuring that the message will find its way to the top.

You can use the Log Messages dialog box on the Options menu of the Memopad example program to observe the behavior of an extended-selection list box.

### The LISTBOX Source Code

Listing One, page 130, is listbox.c, the source file that implements the list box within D-Flat. The ListBoxProc function is the window-processing module for list boxes. For the CREATE_WINDOW message, it sets the selection and AnchorPoint variables to-1, which is their initial and null value. The ListBoxProc function processes messages in a switch statement except when the message needs more than a few lines of code. Then, the message processing for that message has its own function named for the message. For example, the KEYBOARD message is processed by the KeyboardMsg function, which is called by the KEYBOARD case of the switch statement. The KeyboardMsg function itself is divided into calls to lower functions to process each of the keystroke values.

### List-box Keystrokes

Depending on what key the user presses, the KeyboardMsg function calls other functions to process the keys. The Shift+F8 key toggles the add mode of an extended-selection list box. When the list-box is in this mode, the user can preserve existing selections while moving the cursor. The Up, Down, PgUp, PgDn, Home, and End keys move the list-box cursor. Each of these keys calls the TestExtended function. If the list box is not in add mode and there are existing selections, this function clears the existing selections before the cursor is moved.

Moving the cursor is done by the SCROLL, HORIZSCROLL, SCROLLPAGE, HORIZPAGE, and SCROLLDOC messages. Most of the processing of these messages is handled by the window-processing module of the base TEXTBOX class. The Up and Down keys work differently from their counterparts in a text box, however. The text box uses the keys to scroll the text within the window. A list box uses the keys to move the bar cursor up and down, scrolling the text only when the cursor is on the top or bottom line of the window.

Keys that move the bar cursor also post the LB_SELECTION message to the window. That message tells the window that the selection cursor has changed.

The space-bar key toggles selections in add mode. If the extended-selection anchor point is not set, the space-bar key sets it at the current line. This is the point from which extended-selection groups begin. Then, if the space bar toggled the selection on, the Extend-Selections function extends the selected group from the anchor-point line to the current line.

The Enter key sends the LB_SELECTION and LB_CHOOSE messages to the window. While the LB_SELECTION tells the window that its selection cursor is now on another line, the LB_CHOOSE message tells the window that the user has chosen the selected line from the list box to be processed by the application.

Other keystrokes are tested to see if they are the first character of one of the list-box entries that occur past the current selection. If so, the user is selecting the next entry with the matching character. That way, if you have the four names Jones, Smith, Brown, and Green in a list box, the user can quickly go to the Brown entry by pressing the B key.

### Messages from the Mouse

When the user presses the left mouse button in a list box, the LEFT_BUTTON message calls the LeftButtonMsg function. LEFT_BUTTON messages continue to come as long as the user holds the mouse button down, so the function ignores all but the first one as long as the y coordinate does not change. When a new y-coordinate value arrives, the function checks to see if the user has a shift key down. If so, an extended selection is underway. If not, the program clears all existing selections unless the Ctrl key is down, which means the user is selecting individual and multiple items with single button presses. The function finishes by sending the LB_SELECTION to the window to tell it the user selected an item.

The DOUBLE_CLICK message occurs when the user double-clicks the left mouse button on a list box. This means the user is choosing that item to be processed, and so the program sends the LB_CHOOSE message to the window.

The BUTTON_RELEASED message arrives when the user releases the left mouse button. It resets the previous mouse y-coordinate variable to its -1 null value.

### Adding, Reading, and Displaying List-box Text

The ADDTEXT message sets the current selection to the first item in the list box if no item is currently selected. This lets the list box start out with an initial selected item. If the first character of the text is the LISTSELECTOR character, the program increments the SelectCount variable, which counts the number of selected items in the list box.

The LB_GETTEXT message retrieves the line of text relative to the line number specified in the second parameter and copies the line to the address specified in the first parameter. It copies up to and not including the newline character, and null terminates the receiving field.

The CLEARTEXT message resets the anchor point, current selection, and extended-selection counter.

The PAINT, paging, and scrolling messages call their counterparts in the base window class's window-processing module and then call the WriteSelection function to display the currently selected list entry with the selector bar cursor colors turned on.

### Selecting and Choosing

In CUA parlance, "selecting" is moving the list-box cursor to an item or marking one or more items in an extended selection, whereas "choosing" is telling the application to process the current selection. This is an important distinction. For example, a pop-down menu is a list-box derivative. When you move a pop-down menu's cursor, you are changing its selection. When you press the Enter key to execute the menu's command, you are choosing the list-box item. The program sometimes needs to know about both events, so there are LB_CHOOSE and LB_SELECTION messages. Both messages send themselves to the list box's parent window as well. This allows an application window, dialog box, or menu bar to do something meaningful when the user takes an action on the list box. The list-box window itself uses the LB_SELECTION message to change the current selection to the one specified in the first parameter. The LB_CURRENTSELECTION message returns the current selection line number to the sender of the message. The LB_SETSELECTION lets the sender position the selection to a specified line number.

### The Message Log

The Memopad example program uses a D-Flat debugging technique to log D-Flat messages as they occur. This feature lets a programmer view the messages that passed through the system during a test. There are always a lot of messages flying around in an event-driven system, and you might not want to look at all of them, so the message log uses an extended-selection list box from which you can select the messages you want to log.

Listing Two, page 133, is log.c, the source file that implements the message log. An application calls the MessageLog function to let you turn logging on and off and select the messages you want to log. The Memopad application has a Log Messages command on its Option menu that calls the MessageLog function. The function executes the modal Log dialog box, which is defined in dialogs.c, a source file that we discussed in the September 1991 column. The dialog box has an extended-selection list box to display the messages, a check box to turn logging on and off, and the usual OK, Cancel, and Help pushbuttons. The message array is the display of messages. The first character position of each entry is blank at first. When that position is changed to contain the LISTSELECTOR character, the corresponding message is selected to be logged. When the ID_LOGGING check box is on, logging is underway. The message-dispatching code in message.c calls the LogMessages function every time a message gets sent. When logging is turned on and the most recent message is selected in the list, the program writes the message's particulars to the log file, DFLAT.LOG, which you can view with any text editor, including one of the document windows of the Memopad program.

### How to Get D-Flat Now

The D-Flat source code is on CompuServe in Library 0 of the DDJ Forum and on M&T Online. If you cannot use either online service, send a formatted 360K or 720K diskette and an addressed, stamped diskette mailer to me in care of Dr. Dobb's Journal, 411 Borel Ave., San Mateo, CA 94402. I'll send you the latest version of D-Flat. The software is free, but if you care to, stuff a dollar bill in the mailer for the Brevard County Food Bank. They help homeless and hungry families here in my home town. We've collected over $1000 so far from generous D-Flat "careware" users. If you want to discuss D-Flat with me, use CompuServe. My CompuServe ID is 71101,1262, and I monitor the DDJ Forum daily.

Next month we'll discuss the D-Flat menu system, including the menu bar, pop-down menus, and the system menu.

### OOPS Unraveled

A lot of programmers have trouble getting their first handle on object-oriented programming. It's not that OOP is hard to understand; it's that OOP is hard to explain. There seems to be no better way to learn it than to get hold of a C++ compiler and design and program some systems. That's fine if you have time to burn getting up the curve, but most programmers have deadlines and bosses who get testy if the programmers spend too much time learning and not enough time coding.

Your urgency to learn notwithstanding, OOP seems to be one of those subjects such as flying an airplane and catching a fish that needs hands-on experience before you can say you know how to do it. You just can't get it all from a book. You should be able to get a reasonable introduction, though, and I've found a book that does a good job of explaining OOP to programmers. It's called Object-Oriented Technology: A Manager's Guide, by David Taylor (Addison-Wesley, 1990), and it will get you started. You can tell from the title that the book is not aimed at programmers, and that is why it is so good at explaining OOP to programmers -- it uses plain English and believable examples. This approach attempts to explain a complex technical methodology to nontechnical managers -- a futile effort at best -- and in doing so, puts it in a language that technicians can embrace and understand. Too bad the book will not serve the audience it targets and does not target the audience it serves. It's a small book, about the size of K&R, easy to read, with a lot of illustrations, and without the usual nonsense about families, genera, and species of fruit and animals that some texts and teachers use when they try to explain object-oriented class hierarchies. The examples always use classes of things that you could imagine yourself writing programs about. Good stuff. Recommended.

[LISTING ONE]

```c
/* ------------- listbox.c ------------ */

#include "dflat.h"

#ifdef INCLUDE_EXTENDEDSELECTIONS
static int ExtendSelections(WINDOW, int, int);
static void TestExtended(WINDOW, PARAM);
static void ClearAllSelections(WINDOW);
static void SetSelection(WINDOW, int);
static void FlipSelection(WINDOW, int);
static void ClearSelection(WINDOW, int);
#else
#define TestExtended(w,p) /**/
#endif
static void near ChangeSelection(WINDOW, int, int);
static void near WriteSelection(WINDOW, int, int, RECT *);
static int near SelectionInWindow(WINDOW, int);

static int py = -1;    /* the previous y mouse coordinate */

#ifdef INCLUDE_EXTENDEDSELECTIONS
/* --------- SHIFT_F8 Key ------------ */
static void AddModeKey(WINDOW wnd)
{
    if (isMultiLine(wnd))    {
        wnd->AddMode ^= TRUE;
        SendMessage(GetParent(wnd), ADDSTATUS,
            wnd->AddMode ? ((PARAM) "Add Mode") : 0, 0);
    }
}
#endif

/* --------- UP (Up Arrow) Key ------------ */
static void UpKey(WINDOW wnd, PARAM p2)
{
    if (wnd->selection > 0)    {
        if (wnd->selection == wnd->wtop)    {
            BaseWndProc(LISTBOX, wnd, KEYBOARD, UP, p2);
            PostMessage(wnd, LB_SELECTION, wnd->selection-1,
                isMultiLine(wnd) ? p2 : FALSE);
        }
        else    {
            int newsel = wnd->selection-1;
            if (wnd->wlines == ClientHeight(wnd))
                while (*TextLine(wnd, newsel) == LINE)
                    --newsel;
            PostMessage(wnd, LB_SELECTION, newsel,
#ifdef INCLUDE_EXTENDEDSELECTIONS
                isMultiLine(wnd) ? p2 :
#endif
                FALSE);
        }
    }
}

/* --------- DN (Down Arrow) Key ------------ */
static void DnKey(WINDOW wnd, PARAM p2)
{
    if (wnd->selection < wnd->wlines-1)    {
        if (wnd->selection == wnd->wtop+ClientHeight(wnd)-1)  {
            BaseWndProc(LISTBOX, wnd, KEYBOARD, DN, p2);
            PostMessage(wnd, LB_SELECTION, wnd->selection+1,
                isMultiLine(wnd) ? p2 : FALSE);
        }
        else    {
            int newsel = wnd->selection+1;
            if (wnd->wlines == ClientHeight(wnd))
                while (*TextLine(wnd, newsel) == LINE)
                    newsel++;
            PostMessage(wnd, LB_SELECTION, newsel,
#ifdef INCLUDE_EXTENDEDSELECTIONS
                isMultiLine(wnd) ? p2 :
#endif
                FALSE);
        }
    }
}

/* --------- HOME and PGUP Keys ------------ */
static void HomePgUpKey(WINDOW wnd, PARAM p1, PARAM p2)
{
    BaseWndProc(LISTBOX, wnd, KEYBOARD, p1, p2);
    PostMessage(wnd, LB_SELECTION, wnd->wtop,
#ifdef INCLUDE_EXTENDEDSELECTIONS
        isMultiLine(wnd) ? p2 :
#endif
        FALSE);
}

/* --------- END and PGDN Keys ------------ */
static void EndPgDnKey(WINDOW wnd, PARAM p1, PARAM p2)
{
    int bot;
    BaseWndProc(LISTBOX, wnd, KEYBOARD, p1, p2);
    bot = wnd->wtop+ClientHeight(wnd)-1;
    if (bot > wnd->wlines-1)
        bot = wnd->wlines-1;
    PostMessage(wnd, LB_SELECTION, bot,
#ifdef INCLUDE_EXTENDEDSELECTIONS
        isMultiLine(wnd) ? p2 :
#endif
        FALSE);
}

#ifdef INCLUDE_EXTENDEDSELECTIONS
/* --------- Space Bar Key ------------ */
static void SpacebarKey(WINDOW wnd, PARAM p2)
{
    if (isMultiLine(wnd))    {
        int sel = SendMessage(wnd, LB_CURRENTSELECTION, 0, 0);
        if (sel != -1)    {
            if (wnd->AddMode)
                FlipSelection(wnd, sel);
            if (ItemSelected(wnd, sel))    {
                if (!((int) p2 & (LEFTSHIFT | RIGHTSHIFT)))
                    wnd->AnchorPoint = sel;
                ExtendSelections(wnd, sel, (int) p2);
            }
            else
                wnd->AnchorPoint = -1;
            SendMessage(wnd, PAINT, 0, 0);
        }
    }
}
#endif

/* --------- Enter ('\r') Key ------------ */
static void EnterKey(WINDOW wnd)
{
    if (wnd->selection != -1)    {
        SendMessage(wnd, LB_SELECTION, wnd->selection, TRUE);
        SendMessage(wnd, LB_CHOOSE, wnd->selection, 0);
    }
}

/* --------- All Other Key Presses ------------ */
static void KeyPress(WINDOW wnd, PARAM p1, PARAM p2)
{
    int sel = wnd->selection+1;
    while (sel < wnd->wlines)    {
        char *cp = TextLine(wnd, sel);
        if (cp == NULL)
            break;
#ifdef INCLUDE_EXTENDEDSELECTIONS
        if (isMultiLine(wnd))
            cp++;
#endif
        /* --- special for directory list box --- */
        if (*cp == '[')
            cp++;
        if (tolower(*cp) == (int)p1)    {
            SendMessage(wnd, LB_SELECTION, sel,
                isMultiLine(wnd) ? p2 : FALSE);
            if (!SelectionInWindow(wnd, sel))    {
                wnd->wtop = sel-ClientHeight(wnd)+1;
                SendMessage(wnd, PAINT, 0, 0);
            }
            break;
        }
        sel++;
    }
}

/* --------- KEYBOARD Message ------------ */
static int KeyboardMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    switch ((int) p1)    {
#ifdef INCLUDE_EXTENDEDSELECTIONS
        case SHIFT_F8:
            AddModeKey(wnd);
            return TRUE;
#endif
        case UP:
            TestExtended(wnd, p2);
            UpKey(wnd, p2);
            return TRUE;
        case DN:
            TestExtended(wnd, p2);
            DnKey(wnd, p2);
            return TRUE;
        case PGUP:
        case HOME:
            TestExtended(wnd, p2);
            HomePgUpKey(wnd, p1, p2);
            return TRUE;
        case PGDN:
        case END:
            TestExtended(wnd, p2);
            EndPgDnKey(wnd, p1, p2);
            return TRUE;
#ifdef INCLUDE_EXTENDEDSELECTIONS
        case ' ':
            SpacebarKey(wnd, p2);
            break;
#endif
        case '\r':
            EnterKey(wnd);
            return TRUE;
        default:
            KeyPress(wnd, p1, p2);
            break;
    }
    return FALSE;
}

/* ------- LEFT_BUTTON Message -------- */
static int LeftButtonMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int my = (int) p2 - GetTop(wnd);
    if (my >= wnd->wlines-wnd->wtop)
        my = wnd->wlines - wnd->wtop;

    if (WindowMoving || WindowSizing)
        return FALSE;
    if (!InsideRect(p1, p2, ClientRect(wnd)))
        return FALSE;
    if (wnd->wlines && my != py)    {
        int sel = wnd->wtop+my-1;
#ifdef INCLUDE_EXTENDEDSELECTIONS
        int sh = getshift();
        if (!(sh & (LEFTSHIFT | RIGHTSHIFT)))    {
            if (!(sh & CTRLKEY))
                ClearAllSelections(wnd);
            wnd->AnchorPoint = sel;
            SendMessage(wnd, PAINT, 0, 0);
        }
#endif
        SendMessage(wnd, LB_SELECTION, sel, TRUE);
        py = my;
    }
    return TRUE;
}

/* ------------- DOUBLE_CLICK Message ------------ */
static int DoubleClickMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    if (WindowMoving || WindowSizing)
        return FALSE;
    if (wnd->wlines)    {
        RECT rc = ClientRect(wnd);
        BaseWndProc(LISTBOX, wnd, DOUBLE_CLICK, p1, p2);
        if (InsideRect(p1, p2, rc))
            SendMessage(wnd, LB_CHOOSE, wnd->selection, 0);
    }
    return TRUE;
}

/* ------------ ADDTEXT Message -------------- */
static int AddTextMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int rtn = BaseWndProc(LISTBOX, wnd, ADDTEXT, p1, p2);
    if (wnd->selection == -1)
        SendMessage(wnd, LB_SETSELECTION, 0, 0);
#ifdef INCLUDE_EXTENDEDSELECTIONS
    if (*(char *)p1 == LISTSELECTOR)
        wnd->SelectCount++;
#endif
    return rtn;
}

/* --------- GETTEXT Message ------------ */
static void GetTextMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    if ((int)p2 != -1)    {
        char *cp1 = (char *)p1;
        char *cp2 = TextLine(wnd, (int)p2);
        while (cp2 && *cp2 && *cp2 != '\n')
            *cp1++ = *cp2++;
        *cp1 = '\0';
    }
}

/* --------- LISTBOX Window Processing Module ------------ */
int ListBoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:
            BaseWndProc(LISTBOX, wnd, msg, p1, p2);
            wnd->selection = -1;
#ifdef INCLUDE_EXTENDEDSELECTIONS
            wnd->AnchorPoint = -1;
#endif
            return TRUE;
        case KEYBOARD:
            if (WindowMoving || WindowSizing)
                break;
            if (KeyboardMsg(wnd, p1, p2))
                return TRUE;
            break;
        case LEFT_BUTTON:
            if (LeftButtonMsg(wnd, p1, p2) == TRUE)
                return TRUE;
            break;
        case DOUBLE_CLICK:
            if (DoubleClickMsg(wnd, p1, p2))
                return TRUE;
            break;
        case BUTTON_RELEASED:
            py = -1;
            return TRUE;
        case ADDTEXT:
            return AddTextMsg(wnd, p1, p2);
        case LB_GETTEXT:
            GetTextMsg(wnd, p1, p2);
            return TRUE;
        case CLEARTEXT:
            wnd->selection = -1;
#ifdef INCLUDE_EXTENDEDSELECTIONS
            wnd->AnchorPoint = -1;
#endif
            wnd->SelectCount = 0;
            break;
        case PAINT:
            BaseWndProc(LISTBOX, wnd, msg, p1, p2);
            WriteSelection(wnd, wnd->selection, TRUE, (RECT *)p1);
            return TRUE;
        case SCROLL:
        case HORIZSCROLL:
        case SCROLLPAGE:
        case HORIZPAGE:
        case SCROLLDOC:
            BaseWndProc(LISTBOX, wnd, msg, p1, p2);
            WriteSelection(wnd,wnd->selection,TRUE,NULL);
            return TRUE;
        case LB_CHOOSE:
            SendMessage(GetParent(wnd), LB_CHOOSE, p1, p2);
            return TRUE;
        case LB_SELECTION:
            ChangeSelection(wnd, (int) p1, (int) p2);
            SendMessage(GetParent(wnd), LB_SELECTION,
                wnd->selection, 0);
            return TRUE;
        case LB_CURRENTSELECTION:
            return wnd->selection;
        case LB_SETSELECTION:
            ChangeSelection(wnd, (int) p1, 0);
            return TRUE;
#ifdef INCLUDE_EXTENDEDSELECTIONS
        case CLOSE_WINDOW:
            if (isMultiLine(wnd) && wnd->AddMode)    {
                wnd->AddMode = FALSE;
                SendMessage(GetParent(wnd), ADDSTATUS, 0, 0);
            }
            break;
#endif
        default:
            break;
    }
    return BaseWndProc(LISTBOX, wnd, msg, p1, p2);
}

static int near SelectionInWindow(WINDOW wnd, int sel)
{
    return (wnd->wlines && sel >= wnd->wtop &&
            sel < wnd->wtop+ClientHeight(wnd));
}

static void near WriteSelection(WINDOW wnd, int sel,
                                    int reverse, RECT *rc)
{
    if (isVisible(wnd))
        if (SelectionInWindow(wnd, sel))
            WriteTextLine(wnd, rc, sel, reverse);
}

#ifdef INCLUDE_EXTENDEDSELECTIONS
/* ----- Test for extended selections in the listbox ----- */
static void TestExtended(WINDOW wnd, PARAM p2)
{
    if (isMultiLine(wnd) && !wnd->AddMode &&
            !((int) p2 & (LEFTSHIFT | RIGHTSHIFT)))    {
        if (wnd->SelectCount > 1)    {
            ClearAllSelections(wnd);
            SendMessage(wnd, PAINT, 0, 0);
        }
    }
}

/* ----- Clear selections in the listbox ----- */
static void ClearAllSelections(WINDOW wnd)
{
    if (isMultiLine(wnd) && wnd->SelectCount > 0)    {
        int sel;
        for (sel = 0; sel < wnd->wlines; sel++)
            ClearSelection(wnd, sel);
    }
}

/* ----- Invert a selection in the listbox ----- */
static void FlipSelection(WINDOW wnd, int sel)
{
    if (isMultiLine(wnd))    {
        if (ItemSelected(wnd, sel))
            ClearSelection(wnd, sel);
        else
            SetSelection(wnd, sel);
    }
}

static int ExtendSelections(WINDOW wnd, int sel, int shift)
{
    if (shift & (LEFTSHIFT | RIGHTSHIFT) &&
                        wnd->AnchorPoint != -1)    {
        int i = sel;
        int j = wnd->AnchorPoint;
        int rtn;
        if (j > i)
            swap(i,j);
        rtn = i - j;
        while (j <= i)
            SetSelection(wnd, j++);
        return rtn;
    }
    return 0;
}

static void SetSelection(WINDOW wnd, int sel)
{
    if (isMultiLine(wnd) && !ItemSelected(wnd, sel))    {
        char *lp = TextLine(wnd, sel);
        *lp = LISTSELECTOR;
        wnd->SelectCount++;
    }
}

static void ClearSelection(WINDOW wnd, int sel)
{
    if (isMultiLine(wnd) && ItemSelected(wnd, sel))    {
        char *lp = TextLine(wnd, sel);
        *lp = ' ';
        --wnd->SelectCount;
    }
}

int ItemSelected(WINDOW wnd, int sel)
{
    if (isMultiLine(wnd) && sel < wnd->wlines)    {
        char *cp = TextLine(wnd, sel);
        return (int)((*cp) & 255) == LISTSELECTOR;
    }
    return FALSE;
}
#endif

static void near ChangeSelection(WINDOW wnd,int sel,int shift)
{
    if (sel != wnd->selection)    {
#ifdef INCLUDE_EXTENDEDSELECTIONS
        if (isMultiLine(wnd))        {
            int sels;
            if (!wnd->AddMode)
                ClearAllSelections(wnd);
            sels = ExtendSelections(wnd, sel, shift);
            if (sels > 1)
                SendMessage(wnd, PAINT, 0, 0);
            if (sels == 0 && !wnd->AddMode)    {
                ClearSelection(wnd, wnd->selection);
                SetSelection(wnd, sel);
                wnd->AnchorPoint = sel;
            }
        }
#endif
        WriteSelection(wnd, wnd->selection, FALSE, NULL);
        wnd->selection = sel;
        WriteSelection(wnd, sel, TRUE, NULL);
     }
}
```

[LISTING TWO]

```c
/* ------------ log .c ------------ */

#include "dflat.h"

#ifdef INCLUDE_LOGGING

static char *message[] = {
    #undef DFlatMsg
    #define DFlatMsg(m) " " #m,
    #include "dflatmsg.h"
    NULL
};

static FILE *log = NULL;
extern DBOX Log;

void LogMessages (WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    if (log != NULL && message[msg][0] != ' ')
        fprintf(log,
            "%-20.20s %-12.12s %-20.20s, %5.5ld, %5.5ld\n",
            wnd ? (GetTitle(wnd) ? GetTitle(wnd) : "") : "",
            wnd ? ClassNames[GetClass(wnd)] : "",
            message[msg]+1, p1, p2);
}

static int LogProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    WINDOW cwnd = ControlWindow(&Log, ID_LOGLIST);
    char **mn = message;
    switch (msg)    {
        case INITIATE_DIALOG:
            AddAttribute(cwnd, MULTILINE | VSCROLLBAR);
            while (*mn)    {
                SendMessage(cwnd, ADDTEXT, (PARAM) (*mn), 0);
                mn++;
            }
            SendMessage(cwnd, SHOW_WINDOW, 0, 0);
            break;
        case COMMAND:
            if ((int) p1 == ID_OK)    {
                int item;
                int tl = GetTextLines(cwnd);
                for (item = 0; item < tl; item++)
                    if (ItemSelected(cwnd, item))
                        mn[item][0] = LISTSELECTOR;
            }
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}

void MessageLog(WINDOW wnd)
{
    if (DialogBox(wnd, &Log, TRUE, LogProc))    {
        if (CheckBoxSetting(&Log, ID_LOGGING))    {
            log = fopen("DFLAT.LOG", "wt");
            SetCommandToggle(&MainMenu, ID_LOG);
        }
        else if (log != NULL)    {
            fclose(log);
            log = NULL;
            ClearCommandToggle(&MainMenu, ID_LOG);
        }
    }
}

#endif
```

Copyright © 1992, Dr. Dobb's Journal
