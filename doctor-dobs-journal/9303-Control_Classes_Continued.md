
# C PROGRAMMING 9303

## Control Classes Continued - Al Stevens

I taught a C++ class this week. The students were C programmers who are undertaking the port of a DOS system to Windows. They have to write a word processor of sorts. That's right, the ones out there aren't quite good enough. Only our government could land on logic like that. The wrinkle is that the system has to use SGML, a standard generic text mark-up language to identify fonts, headings, page breaks, and such. The Windows SGML editors available are expensive--not as expensive as writing your own, but the users need a lot of licenses. The software is for A&E firms to use in preparing engineering specifications for proposals. They have to use the system to be eligible to bid. Uncle Sam doesn't want to tell them that they have to buy a $1000 text editor just to respond to a request for proposal. I wish they'd be that considerate of me every April.

The first question out of the class was, "We have to design an object-oriented program. What are the objects?" I remember asking that same question several years ago. It took a while to figure out the answer. We kicked around some possible objects, mostly taken from exercises in the book, and then they wanted me to help them find the objects in their project's design. They were intimidated by the notion that if you don't call everything an object, you aren't doing it right. That's a flawed notion. Their compiler is rich with classes in a container-class library and a Windows-API class library. There are already plenty of objects for them to use. What they did not understand was that there are generic data structures into which parts of their problem will fit, and the container classes support those generic structures. If your class library is complete enough, you may never need to identify an object.

You never really know a subject until you've taught it. Each of my four students had a 486 with Borland C++ installed. They'd ask a question, and before I could look up the answer in the ARM, they'd have it typed in and running through the debugger to see what happened. We used my book, Teach Yourself C++. (Mine, not Herb Schildt's. Get your own class, Herb.) By the end of the week, they not only had a good foundation in C++, but they had found some errors in the book as well.

### Visual Basic - Add C and Stir

I've been working on a Windows program that provides access to a big static-text database. Most of the text compression, indexing, and search functions come from earlier work, much of which you've seen here in this column. I blanched at the thought of mastering the SDK for one program, so I decided to use Visual Basic for the user interface and build my existing C code into a DLL. It has been a smooth project primarily because the C code was already checked out in DOS programs. Borland's Turbo C++ for Windows is a good tool for editing and building the DLL directly in Windows, although their documentation is weak when it comes to DLLs. They tell you how to build one, but they leave some stuff out. In desperation I switched to Microsoft C 7.0, which gave me a much needed error message that Borland left out. I fixed the error and returned to TCW, and everything is okay now. Visual Basic is a great tool for constructing a quick and dirty Windows application. Jeff Duntemann pleads for a Visual Pascal. Listen up, Microsoft. We really need a Visual C/C++, too.

### D-Flat++ Controls

Last month's column discussed the D-Flat++ Application class and introduced the Control class. In a CUA user interface, a "control" is a generic term that applies to any of the input mechanisms that a user has to enter data values. Text entry, options, file selections, all come to the program from the user by way of one or more controls, which include edit boxes, lists, buttons, and the like. The DF++ class library implements controls with specific control classes derived from the Control class. Last month we discussed the first of the controls, the TextBox class. Most of the other controls will derive from the text box, because they require the basic text-displaying functions that it supports.

This month we look at five more control classes, all of which derive from the TextBox class. One, the ListBox class, will be the base class for pop-down menus. It will also provide listbox support for dialog boxes. The other classes are radio and command buttons, the check box, and the base class for buttons.

### The ListBox

Listing One, page 146, is listbox.h, the source file that describes the ListBox class. The class has few data members of its own. The addmode variable will indicate that the list box is in extended-selection mode, a feature that I have not implemented yet. The anchorpoint and selectcount variables are for that feature as well. The only data member in use in the current implementation is the selection variable, which subscripts the current user selection on the list box. Menus, which are implemented in the first version and which are derived from the list box, use that variable. The constructors and destructor are all inline functions. The constructors call the OpenWindow function to initialize the list box.

Listing Two, page 146, is listbox.cpp, which contains the member functions for the ListBox class. The OpenWindow function initializes the data members and calls the base TextBox class's OpenWindow function. A list box differs from a text box in that the list box has a selection cursor bar. When the user scrolls a list box up and down, the selection bar moves with the scrolling action. The ListBox class must override the TextBox's keyboard and scrolling messages to accommodate this feature. In addition, there are SetSelection and ClearSelection public member functions to allow the using program to modify the selected entry in the list box. The paint, scrolling, and paging messages all use the TextBox class's messages and then augment them by repainting the selected line if it is in view. The selected line must display in highlighted colors so that the user can distinguish it from other entries in the list.

The ListBox class intercepts mouse messages to know when the user has changed the selection or chosen one. The user "selects" a listbox entry by a single click or by moving the selection bar to the entry with the keyboard arrow keys. In this context, "selecting" means pointing. No action is taken until the user "chooses" the selection by pressing the Enter key or double-clicking the selection. These terms come from the Windows and CUA lexicon. Pop-down menus behave differently, so even though they derive from the list box, they will need to override some of the list box's behavior. I'll discuss them in a later column.

### Buttons

Users have different kinds of buttons with which to specify options and selections. There is a tendency in user interfaces to make the screen look like things more familiar to users. So, we have buttons, because our home appliances have buttons, and people know how to press them. Lyndon Johnson worried about the grave responsibility of a president who could "mash" a button and destroy the world. The command button is a familiar device on dialog boxes. Most of them have OK and Cancel command buttons, and users are accustomed to "mashing" them to accept or reject the dialog's content. Check boxes record the user's selection with respect to simple binary decisions. Either the option is enabled or not. (Toggled menu commands perform a similar function, and the designer is often unsure about which one to use.) Radio buttons resemble the radio buttons on an old car. They come in groups and when you press one, the others pop out. They represent mutually exclusive choices within a group.

### Command Buttons

Listing Three, page 146, and Listing Four, page 147, are pbutton.h and pbutton.cpp, the source files that define the PushButton class, the class that implements command buttons. A command button has an executable function associated with it. The function carries out the command. When the user presses the button, the function executes. The class, therefore, has three data members, a Boolean variable that indicates that the button is pressed, the window handle of the window that will receive the message, and the address of the function that will execute. The function is a member function of the DFWindow class. Usually it will be a member function of the application window class that you derive from the Application class, discussed last month. The SetButtonFunction member function of the Button class will initialize the window and function addresses. Command execution occurs when the user clicks the button or selects it (tabs to it) with the keyboard and presses the Enter key. The execution occurs when the user releases the mouse button or Enter key. While the mouse button or Enter key is down, the class paints the button in a depressed state. When the user releases the key or button, the class executes the function if the window and function pointers are initialized. The member functions that manage the user's selection and choice and the Paint member function are virtual functions so that you can derive new command buttons with different formats.

### Generic Buttons

Listing Five and Listing Six, page 148, are button.h and button.cpp, the source files that implement a generic Button base class from which the RadioButton and CheckBox classes are derived. The Button class is derived from the TextBox class and adds only one data member, the setting variable, which records if the button object is set on or off. The constructor and destructor are protected, so the Button class is an abstract base class, which means that you cannot instantiate an object of its type. You must instantiate one of its derived typed instead. The Button class defines keyboard and mouse processes for its derived classes and paints the button and its label.

### Radio Buttons

Listing Seven and Listing Eight, page 148, are radio.h and radio.cpp, the source files that implement radio buttons. Radio buttons come in groups, and only one button of the group may be set on at any time. Therefore, if you set one button on, the software must set the others off. The first matter to resolve is how to assign radio buttons to a group. A dialog box might have more than one group of radio buttons, so the grouping must be based on something other than the common parent of the button objects. When the user selects a radio button, the software needs to know which other buttons to turn off. I developed a technique for D-Flat that works well. Radio buttons are grouped if they share the same window X coordinate and if there are no blank lines between them. So far, that has not been a serious restriction, so I decided to keep it in DF++. The program begins by building a table of all the radio buttons that have the same parent window and the same left screen coordinate as the one being selected. Then it purges the ones that are not in a contiguous group in the Y coordinate. Next it turns off all the radio buttons. Finally, it turns on the one being selected.

The base Button class's constructor builds a string with open and close parentheses characters followed by the button's label, which is the default display for a radio button. A radio button will display with text like this:

```c
  ( ) Option description
```

If you put a tilde (~) in the label, the letter it follows will display in a highlighted font and the tilde will not display. This allows you to identify a shortcut key for the radio button. When the button is selected, the program modifies the text to include a bullet character (ASCII 7) between the paren characters. The character comes from the setchar data member, which the radio button's constructor provides.

### Check Boxes

Listing Nine and Listing Ten, page 149, are checkbox.h and checkbox.cpp, the source files that implement the CheckBox class. The class definition looks just like that of the RadioButton class. The difference is in the behavior as defined by the member functions. The constructor builds a check box's text display with brackets rather than parentheses characters so that the check box looks like this:

```c
  [] Option description
```

A tilde in the label has the same effect as it does for a radio button. The check box displays the X character inside its brackets when it is selected. I have a baseball cap with a selected checkbox on the front. I thought it was a Dan Quayle signature campaign cap, and I still haven't figured out why the guys down at the Moose Lodge glare at me when I wear it.

### How to Get the Source Code

D-Flat++ is still in its preliminary version. I am writing this column in December and have just uploaded the first version. It does not include a full CUA package, but there is enough of an implementation to give you an idea of how it will work and how it will differ from D-Flat. You can download DF++ from the CompuServe DDJ forum or from M&T Online. You can also get it by sending a stamped, self-addressed diskette mailer and a formatted diskette to me at Dr. Dobb's Journal, 411 Borel Ave., San Mateo, CA 94402. The software is free, but if you wish, include a dollar for my Careware charity, the Brevard County Food Bank.

### Bloated Commentary

John Dvorak, columnist, bon vivant, raconteur, and windbag at large, recently commented about a new C++ compiler for supercomputers from Cray Research, saying with typical bombast, "I'm glad to see that bloated code is now universal." As a rule, PC Magazine, where Dvorak's column appears, has good programming commentary, featuring the works of Duncan, Petzold, Prosise, Schulman, and other esteemed technical superstars. But not all PC Mag columnists are as qualified to discuss programming issues. Most of them stick judiciously to their own turf, but Dvorak is not so disciplined, and his remark evinces a serious lack of understanding about programmers' concerns. He compounds the evidence of his ignorance when he says in a subsequent column that anyone who uses the phrase, "paradigm shift" probably does not know what they are talking about. My guess is that Dvorak himself does not know what they are talking about and, therefore, dismisses as inconsequential and invalid that which he does not understand.

All that notwithstanding, the guy writes an entertaining, irreverent, and controversial column and has devoted followers who, unless otherwise informed, might believe that he always speaks with well-founded authority. You should be wary when nonprogramming writers make casual remarks about programming; they would do better to stay with operating systems, applications, and the business activities of the industry and leave programming issues to those who understand them.

### InterNetworking

Networks fascinate me. I couldn't get by without e-mail. For some time I've gotten messages that come by way of Internet. I wasn't really sure how Internet worked and how I could get into it except through a CompuServe gateway, but it seemed as if it should have more to offer than just mail. I looked for books about it and only just recently found one. It's called The Internet Companion, by Tracy LaQuey and Jeanne Ryer (Addison-Wesley, 1992). It does a nice job of explaining what Internet is, where it came from, how you get onto it, and how you use it. But the really interesting part is that the foreword is by Senator Al Gore, written in August 1992. Besides making me wonder how an author can get a vice-presidential candidate to write a foreword, this event is news. Read the foreword and you realize that a single heartbeat away from the presidency is a man who understands some of what we do. I leave it to you to decide whether that's good news or bad news.

[LISTING ONE]

```c
// -------- listbox.h

#ifndef LISTBOX_H
#define LISTBOX_H

#include "textbox.h"

const int LISTSELECTOR = 4; // selected list box entry
class ListBox : public TextBox    {
    Bool addmode;       // adding extended selections mode
    int anchorpoint;    // anchor point for extended selections
    int selectcount;    // count of selected items
    virtual void SetColors();
protected:
    int selection;        // current selection
    virtual void ClearSelection();
public:
    ListBox(char *ttl, int lf, int tp, int ht, int wd,
        DFWindow *par) : TextBox(ttl, lf, tp, ht, wd, par)
            { OpenWindow(); }
    ListBox(char *ttl, int ht, int wd, DFWindow *par)
                        : TextBox(ttl, ht, wd, par)
            { OpenWindow(); }
    ListBox(int lf, int tp, int ht, int wd, DFWindow *par)
                        : TextBox(lf, tp, ht, wd, par)
            { OpenWindow(); }
    ListBox(int ht, int wd, DFWindow *par) : TextBox(ht,wd,par)
            { OpenWindow(); }
    ListBox(char *ttl)    : TextBox(ttl)
            { OpenWindow(); }
    virtual ~ListBox()
        { if (windowstate != CLOSED) CloseWindow(); }
    // -------- listbox API messages
    virtual void OpenWindow();
    virtual void CloseWindow();
    virtual void Paint();
    virtual void Keyboard(int key);
    virtual void SetSelection(int sel);
    virtual void ButtonReleased(int mx, int my);
    virtual void LeftButton(int mx, int my);
    virtual void DoubleClick(int mx, int my);
    virtual void Choose();
    virtual void ScrollUp();
    virtual void ScrollDown();
    virtual void ScrollRight();
    virtual void ScrollLeft();
    virtual void PageUp();
    virtual void PageDown();
    virtual void PageRight();
    virtual void PageLeft();
};
#endif
```

[LISTING TWO]

```c
// ------------ listbox.cpp
#include "listbox.h"
#include "keyboard.h"

// ----------- common constructor code
void ListBox::OpenWindow()
{
    windowtype = ListboxWindow;
    if (windowstate == CLOSED)
        TextBox::OpenWindow();
    selection = -1;
    addmode = False;
    anchorpoint = -1;
    selectcount = 0;
    SetColors();
}
// -------- set the fg/bg colors for the window
void ListBox::SetColors()
{
    colors.fg = YELLOW;
    colors.bg = BLUE;
    colors.sfg = LIGHTGRAY;
    colors.sbg = BLACK;
    colors.ffg = LIGHTGRAY;
    colors.fbg = BLUE;
    colors.hfg = BLACK;
    colors.hbg = LIGHTGRAY;
}
void ListBox::CloseWindow()
{
    TextBox::CloseWindow();
}
void ListBox::ClearSelection()
{
    if (selection != -1)
        WriteTextLine(selection, colors.fg, colors.bg);
}
void ListBox::SetSelection(int sel)
{
    ClearSelection();
    if (sel >= 0 && sel < wlines)    {
        selection = sel;
        WriteTextLine(sel, colors.sfg, colors.sbg);
    }
}
void ListBox::Keyboard(int key)
{
    int sel = selection; // (ClearSelection changes selection)
    switch (key)    {
        case UP:
            if (sel > 0)    {
                ClearSelection();
                if (sel == wtop)
                    ScrollDown();
                SetSelection(sel-1);
            }
            return;
        case DN:
            if (sel < wlines-1)    {
                ClearSelection();
                if (sel == wtop+ClientHeight()-1)
                    ScrollUp();
                SetSelection(sel+1);
            }
            return;
        case '\r':
            Choose();
            return;
        default:
            break;
    }
    TextBox::Keyboard(key);
}
// ---------- paint the listbox
void ListBox::Paint()
{
    TextBox::Paint();
    if (text != NULL)
        WriteTextLine(selection, colors.sfg, colors.sbg);
}
void ListBox::ScrollUp()
{
    TextBox::ScrollUp();
    WriteTextLine(selection, colors.sfg, colors.sbg);
}
void ListBox::ScrollDown()
{
    TextBox::ScrollDown();
    WriteTextLine(selection, colors.sfg, colors.sbg);
}
void ListBox::ScrollRight()
{
    TextBox::ScrollRight();
    WriteTextLine(selection, colors.sfg, colors.sbg);
}
void ListBox::ScrollLeft()
{
    TextBox::ScrollLeft();
    WriteTextLine(selection, colors.sfg, colors.sbg);
}
void ListBox::PageUp()
{
    TextBox::PageUp();
    WriteTextLine(selection, colors.sfg, colors.sbg);
}
void ListBox::PageDown()
{
    TextBox::PageDown();
    WriteTextLine(selection, colors.sfg, colors.sbg);
}
void ListBox::PageRight()
{
    TextBox::PageRight();
    WriteTextLine(selection, colors.sfg, colors.sbg);
}
void ListBox::PageLeft()
{
    TextBox::PageLeft();
    WriteTextLine(selection, colors.sfg, colors.sbg);
}
// ---------- Left mouse button was clicked
void ListBox::LeftButton(int mx, int my)
{
       if (my != prevmouseline)    {
        if (ClientRect().Inside(mx, my))    {
            int y = my - ClientTop();
            if (wlines && y < wlines-wtop)
                SetSelection(wtop+y);
        }
    }
    DFWindow::LeftButton(mx, my);
}
void ListBox::DoubleClick(int mx, int my)
{
    if (ClientRect().Inside(mx, my))    {
        my -= ClientTop();
        if (wlines && my < wlines-wtop)
            Choose();
    }
    DFWindow::DoubleClick(mx, my);
}
void ListBox::ButtonReleased(int, int)
{
    prevmouseline = -1;
}
void ListBox::Choose()
{
    // --- does nothing yet
}
```

[LISTING THREE]

```c
// ----------- pbutton.h
#ifndef PBUTTON_H
#define PBUTTON_H

#include "textbox.h"
class PushButton : public TextBox    {
    virtual void SetColors();
    Bool pressed;
    DFWindow *owner;  // window that gets the command
    void (DFWindow::*cmdfunction)();    // selection function
public:
    PushButton(char *lbl, int lf, int tp, DFWindow *par);
    virtual ~PushButton()
        { if (windowstate != CLOSED) CloseWindow(); }
    // -------- API messages
    virtual void OpenWindow();
    virtual void CloseWindow();
    virtual Bool SetFocus();
    virtual void ResetFocus();
    virtual void Paint();
    virtual void Shadow();
    virtual void Keyboard(int key);
    virtual void LeftButton(int mx, int my);
    virtual void ButtonReleased(int mx, int my);
    virtual void MouseMoved(int mx, int my);
    virtual void KeyReleased();
    virtual void PressButton();
    virtual void ReleaseButton();
    virtual void ButtonCommand();
    void SetButtonFunction(DFWindow *wnd,
                    void (DFWindow::*cmdfunc)())
        { owner = wnd; cmdfunction = cmdfunc; }
};
#endif
```

[LISTING FOUR]

```c
// ------------- pbutton.cpp
#include "pbutton.h"
#include "desktop.h"

PushButton::PushButton(char *lbl, int lf, int tp, DFWindow *par)
                : TextBox(lf, tp, 1, strlen(lbl)+2, par)
{
    OpenWindow();
    String lb(" ");
    lb += lbl;
    lb += " ";
    SetText(lb);
}
// ----------- common constructor code
void PushButton::OpenWindow()
{
    windowtype = PushButtonWindow;
    if (windowstate == CLOSED)
        TextBox::OpenWindow();
    SetColors();
    pressed = False;
    cmdfunction = NULL;
}
void PushButton::CloseWindow()
{
    TextBox::CloseWindow();
}
// -------- set the fg/bg colors for the window
void PushButton::SetColors()
{
    colors.fg = BLACK;
    colors.bg = CYAN;
    colors.sfg = WHITE;
    colors.sbg = CYAN;
    colors.ffg = BLACK;
    colors.fbg = CYAN;
    colors.hfg = DARKGRAY;    // Inactive FG
    colors.hbg = CYAN;        // Inactive BG
    shortcutfg = RED;
}
void PushButton::Paint()
{
    if (visible)    {
        COLORS fg;
        COLORS bg;
        if (!pressed)    {
            if (isEnabled())    {
                if (this == desktop.InFocus())
                    fg = colors.sfg;
                else
                    fg = colors.fg;
                WriteShortcutLine(0, fg, colors.bg);
            }
            else
                WriteTextLine(0, colors.hfg, colors.hbg);
        }
        else     {
            // ---- display a pressed button
            fg = ClientBG();
            bg = Parent()->ClientBG();
            int wd = Width();
            WriteWindowChar(' ', 0, 0, fg, bg);
            for (int x = 0; x < wd; x++)    {
                WriteWindowChar(220, x+1, 0, fg, bg);
                WriteWindowChar(223, x+1, 1, fg, bg);
            }
        }
    }
}
void PushButton::Shadow()
{
    if (visible && (attrib & SHADOW))    {
        COLORS bg = Parent()->ClientBG();
        int wd = Width();
        WriteWindowChar(220, wd, 0, BLACK, bg);
        for (int x = 1; x <= wd; x++)
            WriteWindowChar(223, x, 1, BLACK, bg);
    }
}
Bool PushButton::SetFocus()
{
    TextBox::SetFocus();
    Paint();
    return True;
}
void PushButton::ResetFocus()
{
    TextBox::ResetFocus();
    Paint();
}
void PushButton::Keyboard(int key)
{
    if (key == '\r')
        PressButton();
    else
        TextBox::Keyboard(key);
}
void PushButton::LeftButton(int mx, int my)
{
    if (ClientRect().Inside(mx,my))    {
        PressButton();
        CaptureFocus();
    }
}
void PushButton::MouseMoved(int mx, int my)
{
    if (desktop.FocusCapture() == this)
        if (ClientRect().Inside(mx,my))
            PressButton();
        else
            ReleaseButton();
}
void PushButton::ButtonReleased(int, int)
{
    ReleaseFocus();
    ButtonCommand();
}
void PushButton::KeyReleased()
{
    ButtonCommand();
}
void PushButton::ButtonCommand()
{
    if (pressed)    {
        ReleaseButton();
        if (cmdfunction != NULL && owner != NULL)
            (owner->*cmdfunction)();
    }
}
void PushButton::PressButton()
{
    if (!pressed)    {
        pressed = True;
        Paint();
    }
}
void PushButton::ReleaseButton()
{
    if (pressed)    {
        pressed = False;
        Paint();
        Shadow();
    }
}
```

[LISTING FIVE]

```c
// ----------- button.h
#ifndef BUTTON_H
#define BUTTON_H

#include "textbox.h"
class Button : public TextBox    {
    virtual void SetColors();
    Bool setting;
protected:
    char setchar;
    Button(char *lbl, int lf, int tp, DFWindow *par);
    virtual ~Button()
        { if (windowstate != CLOSED) CloseWindow(); }
public:
    // -------- API messages
    virtual void OpenWindow();
    virtual void CloseWindow();
    virtual Bool SetFocus();
    virtual void ResetFocus();
    virtual void Paint();
    virtual void Keyboard(int key);
    virtual void LeftButton(int mx, int my);
    virtual void InvertButton();
    virtual void PushButton();
    virtual void ReleaseButton();
    Bool Setting() { return setting; }
};
#endif
```

[LISTING SIX]

```c
// ------------- button.cpp
#include "button.h"
#include "desktop.h"
Button::Button(char *lbl, int lf, int tp, DFWindow *par)
                : TextBox(lf, tp, 1, strlen(lbl)+5, par)
{
    OpenWindow();
    String lb("( ) ");
    lb += lbl;
    lb += " ";
    SetText(lb);
}
// ----------- common constructor code
void Button::OpenWindow()
{
    if (windowstate == CLOSED)
        TextBox::OpenWindow();
    SetColors();
    setting = False;
    setchar = ' ';
}
void Button::CloseWindow()
{
    TextBox::CloseWindow();
}
// -------- set the fg/bg colors for the window
void Button::SetColors()
{
    colors.fg =
    colors.sfg =
    colors.ffg =
    colors.hfg = Parent()->ClientFG();
    colors.bg =
    colors.sbg =
    colors.fbg =
    colors.hbg = Parent()->ClientBG();
    shortcutfg = RED;
}
void Button::Paint()
{
    if (visible)    {
        (*text)[1] = setting ? setchar : ' ';
        if (isEnabled())
            WriteShortcutLine(0, colors.fg, colors.bg);
        else
            WriteTextLine(0, colors.hfg, colors.hbg);
    }
}
Bool Button::SetFocus()
{
    TextBox::SetFocus();
    desktop.cursor().normalcursor();
    desktop.cursor().SetPosition(Left()+1, Top());
    desktop.cursor().Show();
    return True;
}
void Button::ResetFocus()
{
    TextBox::ResetFocus();
    desktop.cursor().Hide();
}
void Button::Keyboard(int key)
{
    if (key == ' ')
        InvertButton();
    else
        TextBox::Keyboard(key);
}
void Button::LeftButton(int mx, int my)
{
    if (ClientRect().Inside(mx,my))
        InvertButton();
}
void Button::InvertButton()
{
    if (setting)
        ReleaseButton();
    else
        PushButton();
}
void Button::PushButton()
{
    setting = True;
    Paint();
}
void Button::ReleaseButton()
{
    setting = False;
    Paint();
}
```

[LISTING SEVEN]

```c
// ----------- radio.h
#ifndef RADIO_H
#define RADIO_H

#include "button.h"
class RadioButton : public Button    {
public:
    RadioButton(char *lbl, int lf, int tp, DFWindow *par);
    virtual ~RadioButton()
        { if (windowstate != CLOSED) CloseWindow(); }
    // -------- API messages
    virtual void InvertButton();
    virtual void PushButton();
    virtual void OpenWindow();
};
#endif
```

[LISTING EIGHT]

```c
// ------------- radio.cpp
#include "radio.h"
#include "desktop.h"

RadioButton::RadioButton(char *lbl, int lf, int tp,
        DFWindow *par) : Button(lbl, lf, tp, par)
{
    OpenWindow();
    setchar = 7;
}
void RadioButton::OpenWindow()
{
    if (windowstate == CLOSED)
        Button::OpenWindow();
    windowtype = RadioButtonWindow;
}
void RadioButton::InvertButton()
{
    PushButton();
}
void RadioButton::PushButton()
{
    int ht = desktop.screen().Height();
    DFWindow **rd = new DFWindow *[ht];
    for (int i = 0; i < ht; i++)
        rd[i] = NULL;
    // - build a table of radio buttons at the same x coordiate
    DFWindow *sib = Parent()->First();
    while (sib != NULL)    {
        if (sib->WindowType() == RadioButtonWindow)    {
            if (sib->Left() == Left())    {
                int tp = sib->Top();
                if (tp < ht)
                    rd[tp] = sib;
            }
        }
        sib = sib->Next();
    }
    // ----- find the start of the radiobutton group
    i = Top();
    while (i >= 0 && rd[i] != NULL)
        --i;
    // ---- ignore everthing before the group
    while (i >= 0)
        rd[i--] = NULL;
    // ----- find the end of the radiobutton group
    i = Top();
    while (i < ht && rd[i] != NULL)
        i++;
    // ---- ignore everthing past the group
    while (i < ht)
        rd[i++] = NULL;
    // ------ release all the radio buttons in the group
    for (i = 0; i < ht; i++)
        if (rd[i] != NULL)
            ((RadioButton *)rd[i])->ReleaseButton();
    delete [] rd;
    // ----- set the chosen radio button
    Button::PushButton();
}
```

[LISTING NINE]

```c
// ----------- checkbox.h
#ifndef CHECKBOX_H
#define CHECKBOX_H

#include "button.h"
class CheckBox : public Button    {
public:
    CheckBox(char *lbl, int lf, int tp, DFWindow *par);
    virtual ~CheckBox()
        { if (windowstate != CLOSED) CloseWindow(); }
    // -------- API messages
    virtual void OpenWindow();
};
#endif
```

[LISTING TEN]

```c
// ------------- checkbox.cpp
#include "checkbox.h"
#include "desktop.h"
CheckBox::CheckBox(char *lbl, int lf, int tp, DFWindow *par) :
                    Button(lbl, lf, tp, par)
{
    OpenWindow();
    setchar = 'X';
    (*text)[0] = '[';
    (*text)[2] = ']';
}
// ----------- common constructor code
void CheckBox::OpenWindow()
{
    if (windowstate == CLOSED)
        Button::OpenWindow();
    windowtype = CheckBoxWindow;
}
```

Copyright © 1993, Dr. Dobb's Journal
