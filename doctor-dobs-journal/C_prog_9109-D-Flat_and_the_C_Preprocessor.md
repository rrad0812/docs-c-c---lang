
# C PROGRAMMING 9109

## D-Flat and the C Preprocessor

Al Stevens

There comes a time when you want to stop pushing the envelope. My work is done on two books, the August column looks good, and the D-Flat code is complete. I took an extended weekend off and went to play the piano at the Dixieland Jazz Jubilee festival in Sacramento. Those people sure dress funny. Not the musicians, the patrons. It's a cross between a Jaycees convention and Sunday on the golf course. The average age is well above the Yupper limits. I didn't take a laptop this time. I vowed to forget about computers for the weekend.

So there I was, beer-bleary and pounding out "Little Rock Getaway" in the hot afternoon sun of Old Sacramento, when it hit me. I need to overhaul the D-Flat class definition process. Up to now it's all been #defines and structure initializations. Menus and dialog boxes are slicker than that, so I'd better get the class system into line. I finished the set and went looking for a pad and pencil. Anita O'Day, the great jazz singer from the '50s, was backstage complaining about everthing and abusing the musicians and festival volunteers. I guess there was no mailman handy for her to bite.

D-Flat uses the C preprocessor to do most of the program configuration. For example, to build an application where users do not need to move and size windows, the programmer removes the INCLUDE_SYSMENU global definition and recompiles the library. To add a pop-down menu to the application's menu bar, the programmer adds an entry to a table in a file named menus.c and recompiles. A file named dialogs.c similarly manages the definition of dialog boxes. I'll describe how the menus and dialog boxes work and then tell you what I've done to the class system.

### Menus

A Windows program defines menus and dialogs in resource files. You compile those files separately with a resource compiler and either connect them to the .EXE file or read them in at runtime. A D-Flat program starts up with initialized structures that describe the menus and dialog boxes. The preprocessor manages the initialization of those structures with some macros that translate statements into structure declarations and initializations. The macro call statements vaguely resemble the entries in a Windows resource file. The idea is to make the addition and modification of resources as easy as possible by hiding the format of the structure from the code.

Listing One, page 136, is menu.h, the header file that defines the macros with which you build a menu bar and pop-down menus. When you develop an applications program, you can generally ignore the format of the structures that describe the menus. There are functions and macros to set and test the pertinent information, such as whether a selection is active. The important part is how to invoke the macros that create a menu bar. Listing Two, page 136, is menus.c. It contains the code for the menus the example MEMOPAD application program uses. Refer to the July issue on page 120 for a figure that shows what the menu bar and the popped down File menu look like.

You define a menu bar by beginning the definition with the DEFMENU macro and ending it with the ENDMENU macro. The parameter in the DEFMENU macro names the menu. Between the two macros, you define each pop-down menus beginning with the POPDOWN macro and ending with the ENDPOP-DOWN macro. The first parameter in the POPDOWN macro specifies its title on the menu bar. The title is text. The tilde (~) character in the text is a prefix that specifies the shortcut key to the menu. The user presses Alt and this key to pop the menu down with the keyboard. The second parameter in the POPDOWN macro is a pointer to a function that will execute immediately before the pop-down menu pops down. This function can test conditions within the program and decide that toggle commands should be turned on or off and which menu selections are active and which ones are inactive. Between the POPDOWN and ENDPOPDOWN macros are the SELECTION macros that define the selections. Each SELECTION macro has a parameter that specifies the text of its selection title. The tilde characters in the title text identify the shortcut keys to the selections when the menu is popped down.

The second parameter in the SELECTION macro is the command code associated with the menu selection. When the user chooses a selection on a menu, D-Flat sends a message to the application window, the parent of all the document windows. That message is a COMMAND message with the menu selection's command code as its first parameter. If the application window cannot process the message, and if a document window has the focus, the application window sends the COMMAND message to the document window.

The third parameter in the SELECTION macro is either 0 or the key code for an accelerator key. An accelerator key is a keystroke that will execute the command from anywhere in the application, without respect to whether a menu is popped down. The Save command on the Files menu has Alt+S as its accelerator key. Its name will display on the menu next to the selection. You can see that in the figure from the July issue.

The last parameter in the SELECTION macro is the attribute value for the selection. Its values, which can be ORed together, can be INACTIVE, TOGGLE, and CHECKED. An inactive selection does not execute. For example, in the MEMOPAD program, the Save, Save As, and Print selections on the File menu are initially inactive. They have no purpose when the program has no document window in focus. If you select the pop-down menu with a document window in focus, the applications window observes that and, from its PrepFileMenu function, which is pointed to up in the POPDOWN macro, the applications window makes the Save, Save As, and Print selections active. If the in-focus window is not a document, the applications window makes the selections inactive. A selection with the TOGGLE attribute does not execute a command. Instead, when you choose it, the selection toggles its CHECKED attribute on and off. When the attribute is on, the selection displays with a check mark to the left of its title on the menu. The Insert and Word Wrap selections on the Options menu are toggle selections.

The SEPARATOR macro that several menus have between some SELECTION macros defines separator lines on the menu between the selections.

The menu bar described by menus.c is typical of most CUA applications. You would use the same menu in any application. If the application did not include text editing, you might not use the Edit menu. A more complete text editing application might include a Search menu for text searches. You would add other pop-down menus for the processes that are unique to the application. The CUA standard specifies that a View menu can be between the Edit and Options menus. The View menu would allow users to change the way information in the document windows is viewed. Lists of data might have sort sequence options, for example. Other application-dependent pop-downs would be between the View and Options menus.

The last item in menus.c is the definition of the System Menu. It is defined independently of the Main menu because it does not pop down from the menu bar. Instead it pops down from the control box in the upper-left corner of windows that use it. It has commands that let you move, size, minimize, maximize, restore, and close the window with the keyboard.

### Dialog Boxes

Figure 1 in this issue shows the MEMOPAD program with a dialog box displayed, in this case the usual File Open dialog box. You see lots of interesting things in this figure. The dialog box has some static text, a single-line text entry box, two list boxes, and three command buttons. If you've ever written a Windows application, you've seen the text input to the resource compiler. D-Flat has something similar, but instead of a special resource language compiler, D-Flat uses the C preprocessor to implement its dialog boxes.

Listing Three, page 137, is dialbox.h, the header file that defines the macros for the dialog box definitions. As with menus, you don't need to remember the structures. The macro language that defines the dialog boxes is what you will find important. Listing Four, page 138, is dialogs.c, the source file with definitions of the dialog boxes that the MEMOPAD application uses.

The definition for the File Open dialog box shows the format for describing dialog boxes. You start with the DIALOGBOX macro and end with the ENDDB macro. The parameter in the DIALOGBOX macro is the name of the structure that the program will use when it refers to fields in the dialog box.

The DB_TITLE macro must be the first macro after the DIALOGBOX macro. It defines the dialog box's title, screen position, and size. Most dialog boxes will have screen coordinates of -1, -1 to tell D-Flat to center them. The next two parameters in the DB_TITLE macro are the dialog box's height and width.

The CONTROL macros come next. Each one defines a control window on the dialog box. The first parameter is the control box class, which can be TEXT, EDITBOX, LISTBOX, BUTTON, CHECKBOX, and RADIOBUTTON. The dialog box in Figure 1 has all but the last two control box classes. A checkbox displays as [ ] or [X], depending on whether it is currently selected or not. A radio button displays as ( ) or (o).

The second parameter in the CONTROL macro is a text string that the control window displays. Only TEXT and BUTTON controls have text strings. The others have NULL as that parameter. The string in a BUTTON control is the label on the button. The string in a TEXT control is the static string display. Observe that the File Open dialog box has a TEXT control window with a NULL string. This control displays the current path, and the program provides the string value when it sets up the dialog box.

The next two CONTROL macro parameters are the control window's column and row position relative to the dialog box. After that are the control window's height and width. The last parameter is the command code that the program uses to interrogate and modify the values in the control window.

When a TEXT control window has a command code, the text is associated with another control window on the dialog box that has the same command code. The text value of the TEXT control window will have a tilde character as the prefix to one of its characters. That character is the shortcut key for moving the focus to the associated other control window when the dialog box is displayed. For example, Radio buttons can be grouped by their position on the dialog box. A radio button must be one of a group, because when one is selected, the others are deselected just like the station buttons on a car radio. When some radio buttons have the same column coordinate and are on adjacent rows, they constitute a group.

### Classes

With the improvements that came to me that day on a bandstand, you can add window classes to D-Flat by making an entry in one table, providing color codes in another, and setting up a window processing function. To begin with, there is classdef.h, which I published in June. It defines the array of structures that describes classes. The new class system uses the same structure except that the colors are no longer a part of the structure, and the CLASS variable that identifies the class is not needed because it is implied by the position of the structure entry in the array.

The backbone of the class system is classes.h, Listing Five, page 138. It contains a table of ClassDef macro invocations with the data items that describe a class. The first parameter is the name of the class. This identifier will become a value in an enumerated data type for the rest of the program to use to reference the type. The second data item is the identifier for the base class, the one from which the current one is derived. A derived class inherits the properties of the base class. The third parameter is a pointer to the window processing module for the class. The last parameter is the window attribute that windows of the class will open with.

Other source files include classdef.h after defining the ClassDef macro to extract only the pertinent data items that the source file needs. For example, to define the enumerated CLASS data type, dflat.h now has the code in Example 1(a). The code in Example 1(b) builds an array of class strings for message logging and to provide default help window mnemonics. This device uses the preprocessor's # operator to build a string from the class name. For example, the entry for the TEXTBOX class looks like this: ClassDef(TEXTBOX, NORMAL, TextBoxProc, 0). The ClassDef macro, as just defined expands the macro to this: "TEXTBOX",. The code in Example 1(c) builds the array of class-defining structures.

Example 1: (a) To define the enumerated Class data type, dflat.h now has this code; (b) this code builds an array of class strings for message logging and to provide default help window mnemonics; (c) this code builds the array of class-defining structures.

(a) typedef enum window_class     {
        #define ClassDef (c,b,p,a) c,
        #include "classes.h"
        CLASSCOUNT
    } CLASS;

(b) char *ClassNames[] = {
        #undef ClassDef
        #define ClassDef (c,b,p,a) #c,
        #include "classes.h"
        NULL
    };

(c) CLASSDEFS classdefs[] = {
        #undef ClassDef
        #define ClassDef(c,b,p,a)
                             {b,p,a},
        {0,0,0}
        #include "classes.h"
    };

There is a significant performance improvement in this approach. The earlier method required a search of the table to find the matching class every time a window processing function called its base processing function or chained to the default processing function for the window class. With this array, the search is unnecessary. The entry offset is the same as the CLASS value.

Listing Six, page 139, is config.c. This replaces an earlier version because the method for defining default class colors is different. Class colors are now an array with three dimensions. There is an entry for each class. If you add a class to D-Flat, you must add an entry to the color array. Each class has four sets of foreground and background colors. There is a standard color, a color for selected items within the window, a color for the window's frame, and a color for highlighted items. The highlighted color set for the MENU BAR, POPDOWNMENU, and BUTTON classes are two foreground colors instead of a foreground and background pair. The first foreground color is for inactive items in the windows, and the second is the foreground color for shortcut keys. Those foregrounds, when used, combine with the windows' standard background colors.

There are three groups of D-Flat colors. The first is the color group for color monitors. The second group is the black and white group. The third group is a reverse black and white group, which seems to work better on some LCD screens.

### What You Can't Preprocess

It is my opinion that the ANSI X3J11 committee missed a beat when they used the ## character pair for the pre-processor's token-pasting operator and # for the "stringize" (see the "Programmer's Soapbox") operator. Because the operators are new to ANSI C, the committee could just have easily used one of the symbols that C does not use. The @ and $ characters are available, for example.

Perhaps the committee was reacting to prior art in some nonstandard compilers, and perhaps the users of those compilers had strong influence on the committee. In their rationale document, the committee says that they introduced the "stringize" # operator to replace a practice where some compilers replaced an identifier in a string literal that matched a macro argument name. They say they invented the ## token paster because some compilers supported token pasting by replacing the / **/ comment with zero characters instead of one space, as K&R specify. Whatever the reason for the operators, the choice of # to implement them means that a C macro cannot include any preprocessor statements in its replacement list. You cannot write a macro that has #ifdef or #define in it, for example.

D-Flat has a source file named keys.h that defines the values that the keyboard event sends as a keystroke message. That file was in May's issue. Another file is keys.c, Listing Seven, page 141. That file initializes a structure array with entries that D-Flat uses to display accelerator key combination names on menus. Each entry has one of the values from keys.h and a string that is the displayable name of the key. I wanted to do all of this with a macro the way I did with the ClassDef macro described earlier. That way I could add and delete keys at will without needing to make changes to tables in different source files. By including only the keys that the application uses, the program does not use unnecessary string space for unused key combination names. I envisioned something like a KeyDef macro that was invoked liked Example 2(a).

Example 2: (a) Involving KeyDef macro; (b) building the array of structures that is used in keys.c; (c) getting values defined for the key mnemonics; (d) getting the # by the pre-processor; (e) declring and initializing integers.

(a)  KeyDef (ALT_S, 159+OFFSET, "Alt+S")

(b)  struct keys keys[] = {
         #undef KeyDef
         #define KeyDef (k,v,s) {v,s},
         #include "keycaps.h"
         {-1,NULL)
     };

(c)  #undef KeyDef
     #define KeyDef (k,v,s) #define k v
     #include "keycaps.h"

(d)  #undef KeyDef
     #define KeyDef (k,v,s) extern const int k;
     #include "keycaps.h"

(e)  #undef KeyDef
     #define KeyDef (k,v,s)const int k = v;
     #include "keycaps.h"

An include file, perhaps named keycaps.h, would contain one of these calls for every key available to a D-Flat application. The programmer would comment out the ones that the application did not need. The header file would be called in different places, with the Key-Def macro defined differently depending on its purpose. For example, to build the array of structures that you see in keys.c, I would code as in Example 2(b) .

That works fine, but first I need to get values defined for the key mnemonics, such as ALT_S. Those values are #defined in keys.h from May, but I would prefer to have all my key definitions in one place, and so, in this hypothetical method, I would need to use the code in Example 2(c) somewhere.

The problem is that the C preprocessor hits the # at the beginning of the #define operator, treats it as a "stringize" operator, and preprocessing goes downhill from there because the misinterpreted //define// token is not one of the macro's parameters. If the "stringize" operator was the @, for example, this would not be a problem.

An alternative solution that gets past the preprocessor is shown in Example 2(d). That sequence of code would appear in a header file that is visible to all the files that refer to key combination mnemonics. It would define the existence of external integers with the mnemonics as identifiers. To declare and initialize the integers, I would put the code in Example 2(e) in one of the C files in the external scope.

So far, so good. But now we run into yet another obstacle. D-Flat has several places where static or external arrays are initialized with the key mnemonic values. Such initializers must be constant values, not variables. The integer declarations are const, but C (unlike C++) treats them as variables, and you must initialize a static or extern variable with a true constant. The work-around is to initialize every such array with assignments. That adds runtime code and bulk to the program. I decided not to do it.

Perhaps my grousing unfairly blames the committee. Three ANSI-compliant compilers hiccuped on the #define inside a macro, but is that the compiler's fault or the standard's? This next suggestion probably violates the rules of precedence for preprocessing, but it seems that the preprocessor could recognize #define and the other directives as unique tokens and preprocess them without dropping into the "stringizing" mess. X3J11, however, did not specify that, and so the compilers' behavior is correct, if not ideal.

### Farewell to Power C

Last month I reported that I had ported D-Flat to MIX's Power C. So I did, but version 4 is the last version that will have that support. I kept running into the walls, floors, and ceilings in Power C. The most recent one was an apparent limit on the global #define table space. D-Flat uses a lot of #define statements. The compiler didn't issue a warning or an error. It simply began to behave as if certain defined globals did not exist, including the one that told it that I was using Power C. This abandonment is not an indictment of Power C. I still think that Power C is a good deal for the budget-minded programmer, especially the C student.

### How to Get D-Flat Now

It has become obvious that I cannot continue to present the D-Flat source code in orderly monthly installments. The code already published keeps changing as readers use D-Flat and report back, and as I decide to fix things and make improvements. So here's the plan: Each column will include listings that are relevant to the discussion at hand. I will not republish entire listings of modules that change. You should not set about to collect the monthly listings with the intention of building a full system at the end of the series. Instead, you should get the complete package as we go along by downloading it or writing to me.

The D-Flat source code is on CompuServe in Library 0 of DDJ Forum and on TelePath. Its name is DFLAT n.ARC, where the n is an integer that represents a loosely-assigned version number. There is another file, named DFn-TXT.ARC, which contains the Help system database and the documentation for the programmer's API. At present, everything compiles and works with Turbo C 2.0, Turbo C++, and Microsoft C 6.0. There is a makefile for the TC and MSC compilers. There are two example program, the MEMOPAD program, a multiple document notepad, and the NOTEPAD program, a single-document text editor, built with few of the D-Flat features to demonstrate a minimum-feature compile.

If for some reason you cannot get to either online service, send me a formatted diskette -- any PC format -- and an addressed, stamped diskette mailer. Send it to me in care of DDJ. I'll send you the latest copy of the library. The software is free, but if you care to, stick a dollar bill in the mailer. I'll match the dollar and give it to the Brevard County Food Bank. They take care of homeless and hungry children.

If you want to discuss D-Flat with me, don't try to call me at that address. I am never there. Use CompuServe. My CompuServe ID is 71101,1262, and I monitor DDJ Forum daily.

### The Programmer's Soapbox: The Decline of Language

I'm heading home to get back to work. The Sacramento airport loudspeaker blares, "Hi. I'm Kevin, and I'll be your passenger boarding person today."

I am preplanning this column about the preprocessor as I wait for Kevin to finalize preboarding those passengers who have extinguished their smoking materials, need special assistance, and may now proceed down the jetway. I am thinking about the words we utilize -- err, use -- hmm, abuse.

X3J11 invented more than a "stringize" operator. They invented a word. An abomination, this "stringize," but it follows in the tradition that "verbizes" nouns with the "ize" appendage. The practice is commonplace in military and computer literature, and seems to be used (utilized) where no suitable verb is known to (memorized by) the coiner. A good example is "initialize," which I first saw in K&R, and which has now found its way into dictionaries and my XyWrite spell checker. The X3J11 committee includes several notable writers (among its standardizers). How did they allow this "stringize" word to slip into the culture as they completed (finalized) the draft? Jaeschke uses the word in his book, Mastering Standard C, as if it were a real word. Plaugher and Brodie wisely avoid it in their book, Standard C.

[LISTING ONE]

```c
/* ------------ menu.h ------------- */
#ifndef MENU_H
#define MENU_H

/* ----------- popdown menu selection structure
       one for each selection on a popdown menu --------- */
struct PopDown {
    char *SelectionTitle;  /* title of the selection      */
    int ActionId;          /* the command executed        */
    int Accelerator;       /* the accelerator key         */
    int Attrib;            /* INACTIVE | CHECKED | TOGGLE */
    char *help;            /* Help mnemonic               */
};

/* ----------- popdown menu structure
       one for each popdown menu on the menu bar -------- */
typedef struct Menu {
    char *Title;           /* title on the menu bar       */
    void (*PrepMenu)(void *, struct Menu *); /* function  */
    struct PopDown Selections[23]; /* up to 23 selections */
    int Selection;         /* most recent selection       */
} MENU;

/* --------- macros to define a menu bar with
                 popdowns and selections ------------- */
#define SEPCHAR "\xc4"
#define DEFMENU(m) MENU m[]= {
#define POPDOWN(ttl,func)      {ttl,func,{
#define SELECTION(stxt,acc,id,attr)   {stxt,acc,id,attr,#acc},
#define SEPARATOR                     {SEPCHAR},
#define ENDPOPDOWN                    {NULL},0}},
#define ENDMENU                {NULL} };

/* -------- menu selection attributes -------- */
#define INACTIVE    1
#define CHECKED     2
#define TOGGLE      4

/* --------- the standard menus ---------- */
extern MENU MainMenu[];
extern MENU SystemMenu[];
extern MENU *ActiveMenu;

int MenuHeight(struct PopDown *);
int MenuWidth(struct PopDown *);

#endif
```

[LISTING TWO]

```c
/* -------------- menus.c ------------- */
#include <stdio.h>
#include "dflat.h"

/* --------------------- the main menu --------------------- */
DEFMENU(MainMenu)
    /* --------------- the File popdown menu ----------------*/
    POPDOWN(       "~File",  PrepFileMenu    )
        SELECTION( "~New",        ID_NEW,          0, 0 )
#ifdef INCLUDE_DIALOG_BOXES
        SELECTION( "~Open...",    ID_OPEN,         0, 0 )
        SEPARATOR
#endif
        SELECTION( "~Save",       ID_SAVE,     ALT_S, INACTIVE)
#ifdef INCLUDE_DIALOG_BOXES
        SELECTION( "Save ~as...", ID_SAVEAS,       0, INACTIVE)
#endif
        SEPARATOR
        SELECTION( "~Print",      ID_PRINT,        0, INACTIVE)
        SEPARATOR
        SELECTION( "~DOS",        ID_DOS,          0, 0 )
        SELECTION( "E~xit",       ID_EXIT,     ALT_X, 0 )
    ENDPOPDOWN
    /* --------------- the Edit popdown menu ----------------*/
    POPDOWN(       "~Edit", PrepEditMenu    )
        SELECTION( "~Undo",      ID_UNDO,  ALT_BS,    INACTIVE)
#ifdef INCLUDE_CLIPBOARD
        SEPARATOR
        SELECTION( "Cu~t",       ID_CUT,   SHIFT_DEL, INACTIVE)
        SELECTION( "~Copy",      ID_COPY,  CTRL_INS,  INACTIVE)
        SELECTION( "~Paste",     ID_PASTE, SHIFT_INS, INACTIVE)
        SEPARATOR
        SELECTION( "Cl~ear",     ID_CLEAR, 0,         INACTIVE)
#endif
        SELECTION( "~Delete",    ID_DELETETEXT, DEL,  INACTIVE)
        SEPARATOR
        SELECTION( "Pa~ragraph", ID_PARAGRAPH,  ALT_P,INACTIVE)
    ENDPOPDOWN
    /* ------------- the Options popdown menu ---------------*/
    POPDOWN(       "~Options", NULL     )
        SELECTION( "~Insert",       ID_INSERT,     INS, TOGGLE)
        SELECTION( "~Word wrap",    ID_WRAP,        0,  TOGGLE)
#ifdef INCLUDE_DIALOG_BOXES
        SELECTION( "~Tabs...",      ID_TABS,        0,      0 )
        SEPARATOR
        SELECTION( "~Display...",   ID_DISPLAY,     0,      0 )
#ifdef INCLUDE_LOGGING
        SEPARATOR
        SELECTION( "~Log Messages       ",ID_LOG,   0,      0 )
#endif
#endif
        SEPARATOR
        SELECTION( "~Save Options", ID_SAVEOPTIONS, 0,      0 )
    ENDPOPDOWN

#ifdef INCLUDE_MULTIDOCS
    /* --------------- the Window popdown menu --------------*/
    POPDOWN( "~Window", PrepWindowMenu        )
        SELECTION(  NULL,  ID_CLOSEALL, 0, 0)
        SEPARATOR
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
        SELECTION(  "~More Windows...", ID_WINDOW, 0, 0)
        SELECTION(  NULL,  ID_WINDOW, 0, 0 )
    ENDPOPDOWN
#endif
#ifdef INCLUDE_HELP
    /* --------------- the Help popdown menu ----------------*/
    POPDOWN( "~Help", NULL  )
        SELECTION(  "~Help for help...",  ID_HELPHELP,  0, 0 )
        SELECTION(  "~Extended help...",  ID_EXTHELP,   0, 0 )
        SELECTION(  "~Keys help...",      ID_KEYSHELP,  0, 0 )
        SELECTION(  "Help ~index...",     ID_HELPINDEX, 0, 0 )
        SEPARATOR
        SELECTION(  "~About...",          ID_ABOUT,     0, 0 )
#ifdef INCLUDE_RELOADHELP
        SEPARATOR
        SELECTION(  "~Reload Help Database",ID_LOADHELP,0, 0 )
#endif
    ENDPOPDOWN
#endif

ENDMENU

#ifdef INCLUDE_SYSTEM_MENUS
/* ------------- the System Menu --------------------- */
DEFMENU(SystemMenu)
    POPDOWN("System Menu", NULL)
        SELECTION("~Restore",  ID_SYSRESTORE,  0,         0 )
        SELECTION("~Move",     ID_SYSMOVE,     0,         0 )
        SELECTION("~Size",     ID_SYSSIZE,     0,         0 )
        SELECTION("Mi~nimize", ID_SYSMINIMIZE, 0,         0 )
        SELECTION("Ma~ximize", ID_SYSMAXIMIZE, 0,         0 )
        SEPARATOR
        SELECTION("~Close",    ID_SYSCLOSE,    CTRL_F4,   0 )
    ENDPOPDOWN
ENDMENU

#endif
```

[LISTING THREE]

```c
/* ----------------- dialbox.h ---------------- */
#ifndef DIALOG_H
#define DIALOG_H

#include <stdio.h>

#define MAXCONTROLS 25

#define OFF FALSE
#define ON  TRUE
/* -------- dialog box and control window structure ------- */
typedef struct  {
    char *title;    /* window title         */
    int x, y;       /* relative coordinates */
    int h, w;       /* size                 */
} DIALOGWINDOW;
/* ------ one of these for each control window ------- */
typedef struct {
    DIALOGWINDOW dwnd;
    int class;      /* LISTBOX, BUTTON, etc */
    char *itext;    /* initialized text     */
    char *vtext;    /* variable text        */
    int command;    /* command code         */
    char *help;     /* help mnemonic        */
    int isetting;   /* initially ON or OFF  */
    int setting;    /* ON or OFF            */
    void *wnd;      /* window handle        */
} CTLWINDOW;
/* --------- one of these for each dialog box ------- */
typedef struct {
    char *HelpName;
    DIALOGWINDOW dwnd;
    CTLWINDOW ctl[MAXCONTROLS+1];
} DBOX;
/* -------- macros for dialog box resource compile -------- */
#define DIALOGBOX(db) DBOX db={ #db,
#define DB_TITLE(ttl,x,y,h,w) {ttl,x,y,h,w},{
#define CONTROL(ty,tx,x,y,h,w,c) \
 {{NULL,x,y,h,w},ty,tx,NULL,c,#c,(ty==BUTTON?ON:OFF),OFF,NULL},
#define ENDDB }};

#define Cancel  " Cancel "
#define Ok      "   OK   "
#define Yes     "  Yes   "
#define No      "   No   "

#endif
```

[LISTING FOUR]

```c
/* ----------- dialogs.c --------------- */
#include "dflat.h"

#ifdef INCLUDE_DIALOG_BOXES

/* -------------- the File Open dialog box --------------- */
DIALOGBOX( FileOpen )
    DB_TITLE(        "Open File",    -1,-1,19,48)
    CONTROL(TEXT,    "~Filename",     2, 1, 1, 8, ID_FILENAME)
    CONTROL(EDITBOX, NULL,           13, 1, 1,29, ID_FILENAME)
    CONTROL(TEXT,    "Directory:",    2, 3, 1,10, 0 )
    CONTROL(TEXT,    NULL,           13, 3, 1,28, ID_PATH )
    CONTROL(TEXT,    "F~iles",        2, 5, 1, 5, ID_FILES )
    CONTROL(LISTBOX, NULL,            2, 6,11,16, ID_FILES )
    CONTROL(TEXT,    "~Directories", 19, 5, 1,11, ID_DRIVE )
    CONTROL(LISTBOX, NULL,           19, 6,11,16, ID_DRIVE )
    CONTROL(BUTTON,  "   ~OK   ",    36, 7, 1, 8, ID_OK)
    CONTROL(BUTTON,  " ~Cancel ",    36,10, 1, 8, ID_CANCEL)
    CONTROL(BUTTON,  "  ~Help  ",    36,13, 1, 8, ID_HELP)
ENDDB
/* -------------- the Save As dialog box --------------- */
DIALOGBOX( SaveAs )
    DB_TITLE(        "Save As",    -1,-1,19,48)
    CONTROL(TEXT,    "~Filename",   2, 1, 1, 8, ID_FILENAME)
    CONTROL(EDITBOX, NULL,         13, 1, 1,29, ID_FILENAME)
    CONTROL(TEXT,    "Directory:",  2, 3, 1,10, 0 )
    CONTROL(TEXT,    NULL,         13, 3, 1,28, ID_PATH )
    CONTROL(TEXT,    "~Directories",2, 5, 1,11, ID_DRIVE )
    CONTROL(LISTBOX, NULL,          2, 6,11,16, ID_DRIVE )
    CONTROL(BUTTON,  "   ~OK   ",  36, 7, 1, 8, ID_OK)
    CONTROL(BUTTON,  " ~Cancel ",  36,10, 1, 8, ID_CANCEL)
    CONTROL(BUTTON,  "  ~Help  ",  36,13, 1, 8, ID_HELP)
ENDDB
/* -------------- generic message dialog box --------------- */
DIALOGBOX( MsgBox )
    DB_TITLE(       NULL,  -1,-1, 0, 0)
    CONTROL(TEXT,   NULL,   1, 1, 0, 0, 0)
    CONTROL(BUTTON, NULL,   0, 0, 1, 8, ID_OK)
    CONTROL(0,      NULL,   0, 0, 1, 8, ID_CANCEL)
ENDDB

#ifdef INCLUDE_MULTIDOCS
#define offset 4
#else
#define offset 0
#endif
/* ------------ VGA Display dialog box -------------- */
DIALOGBOX( DisplayVGA )
    DB_TITLE(     "Display", -1, -1, 13+offset, 34)
#ifdef INCLUDE_MULTIDOCS
    CONTROL(CHECKBOX,    OFF,     9, 1, 1, 3, ID_TITLE)
    CONTROL(TEXT,     "~Title",  15, 1, 1, 5, ID_TITLE)
    CONTROL(CHECKBOX,    OFF,     9, 2, 1, 3, ID_BORDER)
    CONTROL(TEXT,     "~Border", 15, 2, 1, 6, ID_BORDER)
    CONTROL(CHECKBOX,    OFF,     9, 3, 1, 3, ID_TEXTURE)
    CONTROL(TEXT,     "Te~xture",15, 3, 1, 7, ID_TEXTURE)
#endif
    CONTROL(RADIOBUTTON, OFF,      9,1+offset,1,3,ID_COLOR)
    CONTROL(TEXT,     "Co~lor",   15,1+offset,1,5,ID_COLOR)
    CONTROL(RADIOBUTTON, OFF,      9,2+offset,1,3,ID_MONO)
    CONTROL(TEXT,     "~Mono",    15,2+offset,1,4,ID_MONO)
    CONTROL(RADIOBUTTON, OFF,      9,3+offset,1,3,ID_REVERSE)
    CONTROL(TEXT,     "~Reverse", 15,3+offset,1,7,ID_REVERSE)
    CONTROL(RADIOBUTTON, OFF,      9,5+offset,1,3,ID_25LINES)
    CONTROL(TEXT,     "~25 Lines",15,5+offset,1,8,ID_25LINES)
    CONTROL(RADIOBUTTON, OFF,      9,6+offset,1,3,ID_43LINES)
    CONTROL(TEXT,     "~43 Lines",15,6+offset,1,8,ID_43LINES)
    CONTROL(RADIOBUTTON, OFF,      9,7+offset,1,3,ID_50LINES)
    CONTROL(TEXT,     "~50 Lines",15,7+offset,1,8,ID_50LINES)
    CONTROL(BUTTON, "   ~OK   ",   2,9+offset,1,8,ID_OK)
    CONTROL(BUTTON, " ~Cancel ",  12,9+offset,1,8,ID_CANCEL)
    CONTROL(BUTTON, "  ~Help  ",  22,9+offset,1,8,ID_HELP)
ENDDB
/* ------------ EGA Display dialog box -------------- */
DIALOGBOX( DisplayEGA )
    DB_TITLE(     "Display", -1, -1, 12+offset, 34)
#ifdef INCLUDE_MULTIDOCS
    CONTROL(CHECKBOX,    OFF,     9, 1, 1, 3, ID_TITLE)
    CONTROL(TEXT,     "~Title",  15, 1, 1, 5, ID_TITLE)
    CONTROL(CHECKBOX,    OFF,     9, 2, 1, 3, ID_BORDER)
    CONTROL(TEXT,     "~Border", 15, 2, 1, 6, ID_BORDER)
    CONTROL(CHECKBOX,    OFF,     9, 3, 1, 3, ID_TEXTURE)
    CONTROL(TEXT,     "Te~xture",15, 3, 1, 7, ID_TEXTURE)
#endif
    CONTROL(RADIOBUTTON, OFF,      9, 1+offset,1,3,ID_COLOR)
    CONTROL(TEXT,     "Co~lor",   15, 1+offset,1,5,ID_COLOR)
    CONTROL(RADIOBUTTON, OFF,      9, 2+offset,1,3,ID_MONO)
    CONTROL(TEXT,     "~Mono",    15, 2+offset,1,4,ID_MONO)
    CONTROL(RADIOBUTTON, OFF,      9, 3+offset,1,3,ID_REVERSE)
    CONTROL(TEXT,     "~Reverse", 15, 3+offset,1,7,ID_REVERSE)
    CONTROL(RADIOBUTTON, OFF,      9, 5+offset,1,3,ID_25LINES)
    CONTROL(TEXT,     "~25 Lines",15, 5+offset,1,8,ID_25LINES)
    CONTROL(RADIOBUTTON, OFF,      9, 6+offset,1,3,ID_43LINES)
    CONTROL(TEXT,     "~43 Lines",15, 6+offset,1,8,ID_43LINES)
    CONTROL(BUTTON, "   ~OK   ",   2, 8+offset,1,8,ID_OK)
    CONTROL(BUTTON, " ~Cancel ",  12, 8+offset,1,8,ID_CANCEL)
    CONTROL(BUTTON, "  ~Help  ",  22, 8+offset,1,8,ID_HELP)
ENDDB
/* ------------ CGA/MDA Display dialog box -------------- */
DIALOGBOX( DisplayCGA )
    DB_TITLE(     "Display", -1, -1, 9+offset, 34)
#ifdef INCLUDE_MULTIDOCS
    CONTROL(CHECKBOX,    OFF,     9, 1, 1, 3, ID_TITLE)
    CONTROL(TEXT,     "~Title",  15, 1, 1, 5, ID_TITLE)
    CONTROL(CHECKBOX,    OFF,     9, 2, 1, 3, ID_BORDER)
    CONTROL(TEXT,     "~Border", 15, 2, 1, 6, ID_BORDER)
    CONTROL(CHECKBOX,    OFF,     9, 3, 1, 3, ID_TEXTURE)
    CONTROL(TEXT,     "Te~xture",15, 3, 1, 7, ID_TEXTURE)
#endif
    CONTROL(RADIOBUTTON, OFF,      9, 1+offset,1,3,ID_COLOR)
    CONTROL(TEXT,     "Co~lor",   15, 1+offset,1,5,ID_COLOR)
    CONTROL(RADIOBUTTON, OFF,      9, 2+offset,1,3,ID_MONO)
    CONTROL(TEXT,     "~Mono",    15, 2+offset,1,4,ID_MONO)
    CONTROL(RADIOBUTTON, OFF,      9, 3+offset,1,3,ID_REVERSE)
    CONTROL(TEXT,     "~Reverse", 15, 3+offset,1,7,ID_REVERSE)
    CONTROL(BUTTON, "   ~OK   ",   2, 5+offset,1,8,ID_OK)
    CONTROL(BUTTON, " ~Cancel ",  12, 5+offset,1,8,ID_CANCEL)
    CONTROL(BUTTON, "  ~Help  ",  22, 5+offset,1,8,ID_HELP)
ENDDB

#define TS2 "~2  "
#define TS4 "~4  "
#define TS6 "~6  "
#define TS8 "~8  "
/* ------------ Tab Stops dialog box -------------- */
DIALOGBOX( TabStops )
    DB_TITLE(      "Editor Tab Stops", -1,-1, 10, 35)
    CONTROL(RADIOBUTTON,  OFF,     2, 1, 1,  3, ID_TAB2)
    CONTROL(TEXT,         TS2,     7, 1, 1, 23, ID_TAB2)
    CONTROL(RADIOBUTTON,  OFF,     2, 2, 1, 11, ID_TAB4)
    CONTROL(TEXT,         TS4,     7, 2, 1, 23, ID_TAB4)
    CONTROL(RADIOBUTTON,  OFF,     2, 3, 1, 11, ID_TAB6)
    CONTROL(TEXT,         TS6,     7, 3, 1, 23, ID_TAB6)
    CONTROL(RADIOBUTTON,  OFF,     2, 4, 1, 11, ID_TAB8)
    CONTROL(TEXT,         TS8,     7, 4, 1, 23, ID_TAB8)
    CONTROL(BUTTON,  "   ~OK   ",  1, 6, 1,  8, ID_OK)
    CONTROL(BUTTON,  " ~Cancel ", 12, 6, 1,  8, ID_CANCEL)
    CONTROL(BUTTON,  "  ~Help  ", 23, 6, 1,  8, ID_HELP)
ENDDB
/* ------------ Windows dialog box -------------- */
#ifdef INCLUDE_MULTIDOCS
DIALOGBOX( Windows )
    DB_TITLE(     "Windows", -1, -1, 19, 24)
    CONTROL(LISTBOX, NULL,         1,  1,11, 20, ID_WINDOWLIST)
    CONTROL(BUTTON,  "   ~OK   ",  2, 13, 1, 8, ID_OK)
    CONTROL(BUTTON,  " ~Cancel ", 12, 13, 1, 8, ID_CANCEL)
    CONTROL(BUTTON,  "  ~Help  ",  7, 15, 1, 8, ID_HELP)
ENDDB
#endif

#ifdef INCLUDE_LOGGING
/* ------------ Message Log dialog box -------------- */
DIALOGBOX( Log )
    DB_TITLE(    "D-Flat Message Log", -1, -1, 18, 41)
    CONTROL(TEXT,  "~Messages",   10,   1,  1,  8, ID_LOGLIST)
    CONTROL(LISTBOX,    NULL,      1,   2, 14, 26, ID_LOGLIST)
    CONTROL(TEXT,    "~Logging:", 29,   4,  1, 10, ID_LOGGING)
    CONTROL(CHECKBOX,    OFF,     31,   5,  1,  3, ID_LOGGING)
    CONTROL(BUTTON,  "   ~OK   ", 29,   7,  1,  8, ID_OK)
    CONTROL(BUTTON,  " ~Cancel ", 29,  10,  1,  8, ID_CANCEL)
    CONTROL(BUTTON,  "  ~Help  ", 29,  13, 1,   8, ID_HELP)
ENDDB
#endif

#ifdef INCLUDE_HELP
/* ------------ the Help window dialog box -------------- */
DIALOGBOX( HelpBox )
    DB_TITLE(         NULL,       -1, -1, 0, 45)
    CONTROL(TEXTBOX, NULL,         1,  1, 0, 40, ID_HELPTEXT)
    CONTROL(BUTTON,  "  ~Close ",  0,  0, 1,  8, ID_CANCEL)
    CONTROL(BUTTON,  "  ~Back  ", 10,  0, 1,  8, ID_BACK)
    CONTROL(BUTTON,  "<< ~Prev ", 20,  0, 1,  8, ID_PREV)
    CONTROL(BUTTON,  " ~Next >>", 30,  0, 1,  8, ID_NEXT)
ENDDB
#endif
#endif
```

[LISTING FIVE]

```c
/* ----------- classes.h ------------ */
/*         Class definition source file
 *         Make class changes to this source file
 *         Other source files will adapt
 *         You must add entries to the color tables in
 *         CONFIG.C for new classes.
 *        Class Name  Base Class   Processor       Attribute
 *       ------------  --------- ---------------  -----------
 */
ClassDef(  NORMAL,      -1,      NormalProc,      0 )
ClassDef(  APPLICATION, NORMAL,  ApplicationProc, VISIBLE   |
                                                  SAVESELF  |
                                                  CONTROLBOX )
ClassDef(  TEXTBOX,     NORMAL,  TextBoxProc,     0          )
ClassDef(  LISTBOX,     TEXTBOX, ListBoxProc,     0          )
ClassDef(  EDITBOX,     TEXTBOX, EditBoxProc,     0          )
ClassDef(  MENUBAR,     NORMAL,  MenuBarProc,     NOCLIP     )
ClassDef(  POPDOWNMENU, LISTBOX, PopDownProc,     SAVESELF  |
                                                  NOCLIP    |
                                                  HASBORDER  )
#ifdef INCLUDE_DIALOG_BOXES
ClassDef(  BUTTON,      TEXTBOX, ButtonProc,      SHADOW    |
                                                  NOCLIP     )
ClassDef(  DIALOG,      NORMAL,  DialogProc,      SHADOW    |
                                                  MOVEABLE  |
                                                  CONTROLBOX|
                                                  HASBORDER |
                                                  NOCLIP     )
ClassDef(  ERRORBOX,    DIALOG,  DialogProc,      SHADOW    |
                                                  HASBORDER  )
ClassDef(  MESSAGEBOX,  DIALOG,  DialogProc,      SHADOW    |
                                                  HASBORDER  )
#else
ClassDef(  ERRORBOX,    TEXTBOX, NULL,            SHADOW    |
                                                  HASBORDER  )
ClassDef(  MESSAGEBOX,  TEXTBOX, NULL,            SHADOW    |
                                                  HASBORDER  )
#endif

#ifdef INCLUDE_HELP
ClassDef(  HELPBOX,     DIALOG,  HelpBoxProc,     SHADOW    |
                                                  MOVEABLE  |
                                                  SAVESELF  |
                                                  HASBORDER |
                                                  NOCLIP    |
                                                  CONTROLBOX )
#endif

/* ========> Add new classes here <========  */

/* ---------- pseudo classes to create enums, etc. ---------- */
ClassDef(  TITLEBAR,    -1,      NULL,            0          )
ClassDef(  DUMMY,       -1,      NULL,            HASBORDER  )
ClassDef(  TEXT,        -1,      NULL,            0          )
ClassDef(  RADIOBUTTON, -1,      NULL,            0          )
ClassDef(  CHECKBOX,    -1,      NULL,            0          )
```

[LISTING SIX]

```c
/* ------------- config.c ------------- */
#include <conio.h>
#include <string.h>
#include "dflat.h"

/* ----- default colors for color video system ----- */
unsigned char color[CLASSCOUNT] [4] [2] = {
    /* ------------ NORMAL ------------ */
   {{LIGHTGRAY, BLACK}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {LIGHTGRAY, BLACK}, /* FRAME_COLOR  */
    {LIGHTGRAY, BLACK}},/* HILITE_COLOR */
    /* ---------- APPLICATION --------- */
   {{LIGHTGRAY, BLUE},  /* STD_COLOR    */
    {LIGHTGRAY, BLUE},  /* SELECT_COLOR */
    {LIGHTGRAY, BLUE},  /* FRAME_COLOR  */
    {LIGHTGRAY, BLUE}}, /* HILITE_COLOR */
    /* ------------ TEXTBOX ----------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
    /* ------------ LISTBOX ----------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {LIGHTGRAY, BLUE},  /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
    /* ----------- EDITBOX ------------ */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {LIGHTGRAY, BLUE},  /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
    /* ---------- MENUBAR ------------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {BLACK, CYAN},      /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {DARKGRAY, RED}},   /* HILITE_COLOR Inactive, Shortcut (both FG) */
    /* ---------- POPDOWNMENU --------- */
   {{BLACK, CYAN},      /* STD_COLOR    */
    {BLACK, LIGHTGRAY}, /* SELECT_COLOR */
    {BLACK, CYAN},      /* FRAME_COLOR  */
    {DARKGRAY, RED}},   /* HILITE_COLOR Inactive ,Shortcut (both FG) */
#ifdef INCLUDE_DIALOG_BOXES
    /* ------------ BUTTON ------------ */
   {{BLACK, CYAN},      /* STD_COLOR    */
    {WHITE, CYAN},      /* SELECT_COLOR */
    {BLACK, CYAN},      /* FRAME_COLOR  */
    {DARKGRAY, RED}},   /* HILITE_COLOR Inactive ,Shortcut (both FG) */
    /* ------------- DIALOG ----------- */
   {{LIGHTGRAY, BLUE},  /* STD_COLOR    */
    {LIGHTGRAY, BLUE},  /* SELECT_COLOR */
    {LIGHTGRAY, BLUE},  /* FRAME_COLOR  */
    {LIGHTGRAY, BLUE}}, /* HILITE_COLOR */
#endif
    /* ----------- ERRORBOX ----------- */
   {{YELLOW, RED},      /* STD_COLOR    */
    {YELLOW, RED},      /* SELECT_COLOR */
    {YELLOW, RED},      /* FRAME_COLOR  */
    {YELLOW, RED}},     /* HILITE_COLOR */
    /* ----------- MESSAGEBOX --------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {BLACK, LIGHTGRAY}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
#ifdef INCLUDE_HELP
    /* ----------- HELPBOX ------------ */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLUE},  /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {WHITE, LIGHTGRAY}},/* HILITE_COLOR */
#endif
    /* ---------- TITLEBAR ------------ */
   {{BLACK, CYAN},      /* STD_COLOR    */
    {BLACK, CYAN},      /* SELECT_COLOR */
    {BLACK, CYAN},      /* FRAME_COLOR  */
    {WHITE, CYAN}},     /* HILITE_COLOR */
    /* ------------ DUMMY ------------- */
   {{GREEN, LIGHTGRAY}, /* STD_COLOR    */
    {GREEN, LIGHTGRAY}, /* SELECT_COLOR */
    {GREEN, LIGHTGRAY}, /* FRAME_COLOR  */
    {GREEN, LIGHTGRAY}} /* HILITE_COLOR */
};
/* ----- default colors for mono video system ----- */
unsigned char bw[CLASSCOUNT] [4] [2] = {
    /* ------------ NORMAL ------------ */
   {{LIGHTGRAY, BLACK}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {LIGHTGRAY, BLACK}, /* FRAME_COLOR  */
    {LIGHTGRAY, BLACK}},/* HILITE_COLOR */
    /* ---------- APPLICATION --------- */
   {{LIGHTGRAY, BLACK}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {LIGHTGRAY, BLACK}, /* FRAME_COLOR  */
    {LIGHTGRAY, BLACK}},/* HILITE_COLOR */
    /* ------------ TEXTBOX ----------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
    /* ------------ LISTBOX ----------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {LIGHTGRAY, BLACK}, /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
    /* ----------- EDITBOX ------------ */
   {{LIGHTGRAY, BLACK}, /* STD_COLOR    */
    {BLACK, LIGHTGRAY}, /* SELECT_COLOR */
    {LIGHTGRAY, BLACK}, /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
    /* ---------- MENUBAR ------------- */
   {{LIGHTGRAY, BLACK}, /* STD_COLOR    */
    {BLACK, LIGHTGRAY}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {DARKGRAY, WHITE}}, /* HILITE_COLOR Inactive, Shortcut (both FG) */
    /* ---------- POPDOWNMENU --------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {DARKGRAY, WHITE}}, /* HILITE_COLOR Inactive ,Shortcut (both FG) */
#ifdef INCLUDE_DIALOG_BOXES
    /* ------------ BUTTON ------------ */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {WHITE, LIGHTGRAY}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {DARKGRAY, WHITE}}, /* HILITE_COLOR Inactive ,Shortcut (both FG) */
    /* ------------- DIALOG ----------- */
   {{LIGHTGRAY, BLACK},  /* STD_COLOR    */
    {LIGHTGRAY, BLACK},  /* SELECT_COLOR */
    {LIGHTGRAY, BLACK},  /* FRAME_COLOR  */
    {LIGHTGRAY, BLACK}}, /* HILITE_COLOR */
#endif
    /* ----------- ERRORBOX ----------- */
   {{LIGHTGRAY, BLACK}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {LIGHTGRAY, BLACK}, /* FRAME_COLOR  */
    {LIGHTGRAY, BLACK}},/* HILITE_COLOR */
    /* ----------- MESSAGEBOX --------- */
   {{LIGHTGRAY, BLACK}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {LIGHTGRAY, BLACK}, /* FRAME_COLOR  */
    {LIGHTGRAY, BLACK}},/* HILITE_COLOR */
#ifdef INCLUDE_HELP
    /* ----------- HELPBOX ------------ */
   {{LIGHTGRAY, BLACK}, /* STD_COLOR    */
    {WHITE, BLACK},     /* SELECT_COLOR */
    {LIGHTGRAY, BLACK}, /* FRAME_COLOR  */
    {WHITE, LIGHTGRAY}},/* HILITE_COLOR */
#endif
    /* ---------- TITLEBAR ------------ */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {BLACK, LIGHTGRAY}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {WHITE, LIGHTGRAY}},/* HILITE_COLOR */
    /* ------------ DUMMY ------------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {BLACK, LIGHTGRAY}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}} /* HILITE_COLOR */
};
/* ----- default colors for reverse mono video ----- */
unsigned char reverse[CLASSCOUNT] [4] [2] = {
    /* ------------ NORMAL ------------ */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {BLACK, LIGHTGRAY}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
    /* ---------- APPLICATION --------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {BLACK, LIGHTGRAY}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
    /* ------------ TEXTBOX ----------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
    /* ------------ LISTBOX ----------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY},  /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
    /* ----------- EDITBOX ------------ */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY},  /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
    /* ---------- MENUBAR ------------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {DARKGRAY, WHITE}}, /* HILITE_COLOR Inactive, Shortcut (both FG) */
    /* ---------- POPDOWNMENU --------- */
   {{LIGHTGRAY, BLACK}, /* STD_COLOR    */
    {BLACK, LIGHTGRAY}, /* SELECT_COLOR */
    {LIGHTGRAY, BLACK}, /* FRAME_COLOR  */
    {DARKGRAY, WHITE}}, /* HILITE_COLOR Inactive ,Shortcut (both FG) */
#ifdef INCLUDE_DIALOG_BOXES
    /* ------------ BUTTON ------------ */
   {{LIGHTGRAY, BLACK}, /* STD_COLOR    */
    {WHITE, BLACK},     /* SELECT_COLOR */
    {LIGHTGRAY, BLACK}, /* FRAME_COLOR  */
    {DARKGRAY, WHITE}}, /* HILITE_COLOR Inactive ,Shortcut (both FG) */
    /* ------------- DIALOG ----------- */
   {{BLACK, LIGHTGRAY},  /* STD_COLOR    */
    {BLACK, LIGHTGRAY},  /* SELECT_COLOR */
    {BLACK, LIGHTGRAY},  /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}}, /* HILITE_COLOR */
#endif
    /* ----------- ERRORBOX ----------- */
   {{BLACK, LIGHTGRAY},      /* STD_COLOR    */
    {BLACK, LIGHTGRAY},      /* SELECT_COLOR */
    {BLACK, LIGHTGRAY},      /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},     /* HILITE_COLOR */
    /* ----------- MESSAGEBOX --------- */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {BLACK, LIGHTGRAY}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {BLACK, LIGHTGRAY}},/* HILITE_COLOR */
#ifdef INCLUDE_HELP
    /* ----------- HELPBOX ------------ */
   {{BLACK, LIGHTGRAY}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {BLACK, LIGHTGRAY}, /* FRAME_COLOR  */
    {WHITE, LIGHTGRAY}},/* HILITE_COLOR */
#endif
    /* ---------- TITLEBAR ------------ */
   {{LIGHTGRAY, BLACK},      /* STD_COLOR    */
    {LIGHTGRAY, BLACK},      /* SELECT_COLOR */
    {LIGHTGRAY, BLACK},      /* FRAME_COLOR  */
    {LIGHTGRAY, BLACK}},     /* HILITE_COLOR */
    /* ------------ DUMMY ------------- */
   {{LIGHTGRAY, BLACK}, /* STD_COLOR    */
    {LIGHTGRAY, BLACK}, /* SELECT_COLOR */
    {LIGHTGRAY, BLACK}, /* FRAME_COLOR  */
    {LIGHTGRAY, BLACK}} /* HILITE_COLOR */
};
#define SIGNATURE DFLAT_APPLICATION " " VERSION

/* ------ default configuration values ------- */
CONFIG cfg = {
    SIGNATURE,
    0,               /* Color                       */
    TRUE,            /* Editor Insert Mode          */
    4,               /* Editor tab stops            */
    TRUE,            /* Editor word wrap            */
    TRUE,            /* Application Border          */
    TRUE,            /* Application Title           */
    TRUE,            /* Textured application window */
    25               /* Number of screen lines      */
};
/* ------ load a configuration file from disk ------- */
int LoadConfig(void)
{
    FILE *fp = fopen(DFLAT_APPLICATION ".cfg", "rb");
    if (fp != NULL)    {
        fread(cfg.version, sizeof cfg.version+1, 1, fp);
        if (strcmp(cfg.version, SIGNATURE) == 0)    {
            fseek(fp, 0L, SEEK_SET);
            fread(&cfg, sizeof(CONFIG), 1, fp);
        }
        else
            strcpy(cfg.version, SIGNATURE);
        fclose(fp);
    }
    return fp != NULL;
}
/* ------ save a configuration file to disk ------- */
void SaveConfig(void)
{
    FILE *fp = fopen(DFLAT_APPLICATION ".cfg", "wb");
    if (fp != NULL)    {
        cfg.InsertMode = GetCommandToggle(MainMenu, ID_INSERT);
        cfg.WordWrap = GetCommandToggle(MainMenu, ID_WRAP);
        fwrite(&cfg, sizeof(CONFIG), 1, fp);
        fclose(fp);
    }
}
```

[LISTING SEVEN]

```c
/* ------------- keys.c ----------- */

#include <stdio.h>
#include "keys.h"

struct keys keys[] = {
    {F1,        "F1"},
    {F2,        "F2"},
    {F3,        "F3"},
    {F4,        "F4"},
    {F5,        "F5"},
    {F6,        "F6"},
    {F7,        "F7"},
    {F8,        "F8"},
    {F9,        "F9"},
    {F10,       "F10"},
    {CTRL_F1,   "Ctrl+F1"},
    {CTRL_F2,   "Ctrl+F2"},
    {CTRL_F3,   "Ctrl+F3"},
    {CTRL_F4,   "Ctrl+F4"},
    {CTRL_F5,   "Ctrl+F5"},
    {CTRL_F6,   "Ctrl+F6"},
    {CTRL_F7,   "Ctrl+F7"},
    {CTRL_F8,   "Ctrl+F8"},
    {CTRL_F9,   "Ctrl+F9"},
    {CTRL_F10,  "Ctrl+F10"},
    {ALT_F1,    "Alt+F1"},
    {ALT_F2,    "Alt+F2"},
    {ALT_F3,    "Alt+F3"},
    {ALT_F4,    "Alt+F4"},
    {ALT_F5,    "Alt+F5"},
    {ALT_F6,    "Alt+F6"},
    {ALT_F7,    "Alt+F7"},
    {ALT_F8,    "Alt+F8"},
    {ALT_F9,    "Alt+F9"},
    {ALT_F10,   "Alt+F10"},
    {HOME,      "Home"},
    {UP,        "Up"},
    {PGUP,      "PgUp"},
    {BS,        "BS"},
    {END,       "End"},
    {DN,        "Dn"},
    {PGDN,      "PgDn"},
    {INS,       "Ins"},
    {DEL,       "Del"},
    {CTRL_HOME, "Ctrl+Home"},
    {CTRL_PGUP, "Ctrl+PgUp"},
    {CTRL_BS,   "Ctrl+BS"},
    {CTRL_END,  "Ctrl+End"},
    {CTRL_PGDN, "Ctrl+PgDn"},
    {SHIFT_HT,  "Shift+Tab"},
    {ALT_BS,    "Alt+BS"},
    {SHIFT_DEL, "Shift+Del"},
    {SHIFT_INS, "Shift+Ins"},
    {CTRL_INS,  "Ctrl+Ins"},
    {ALT_A,     "Alt+A"},
    {ALT_B,     "Alt+B"},
    {ALT_C,     "Alt+C"},
    {ALT_D,     "Alt+D"},
    {ALT_E,     "Alt+E"},
    {ALT_F,     "Alt+F"},
    {ALT_G,     "Alt+G"},
    {ALT_H,     "Alt+H"},
    {ALT_I,     "Alt+I"},
    {ALT_J,     "Alt+J"},
    {ALT_K,     "Alt+K"},
    {ALT_L,     "Alt+L"},
    {ALT_M,     "Alt+M"},
    {ALT_N,     "Alt+N"},
    {ALT_O,     "Alt+O"},
    {ALT_P,     "Alt+P"},
    {ALT_Q,     "Alt+Q"},
    {ALT_R,     "Alt+R"},
    {ALT_S,     "Alt+S"},
    {ALT_T,     "Alt+T"},
    {ALT_U,     "Alt+U"},
    {ALT_V,     "Alt+V"},
    {ALT_W,     "Alt+W"},
    {ALT_X,     "Alt+X"},
    {ALT_Y,     "Alt+Y"},
    {ALT_Z,     "Alt+Z"},
    {-1,        NULL}
};
```

Copyright © 1991, Dr. Dobb's Journal
