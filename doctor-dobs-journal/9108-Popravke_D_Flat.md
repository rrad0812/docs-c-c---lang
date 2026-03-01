
# C PROGRAMMING 9108

## Popravke D-Flata - Al Stevens

Ovo, godišnje izdanje C, označava početak moje četvrte godine kao kolumniste C programiranja Dr. Dobb's Journal-a. Oprostite mi ako naduvam grudi kada to kažem. Svakog meseca imam priliku da napišem neki C kod, pročitam neke knjige, izvučem svoju kutiju za sapunice i pišem za časopis vodećih programera. Tokom svakog meseca razgovaram sa drugim programerima na CompuServe-u. S vremena na vreme odem na neko mesto kao što je Boston, Nju Orleans ili San Francisko na kompjutersku emisiju ili programski simpozijum. I oni ovo zovu rad.

Ovog meseca ćemo popraviti neke od D-Flat fajlova objavljenih u prošlim kolumnama, pogledati Pover C, jeftin ANSI C kompajler i razgovarati o najnovijim C++ kompajlerima za PC. Diskusija o C++-u je podmukao način da uključim drugo izdanje moje knjige C++. Tradicionalno je da kompjuterski kolumnisti koriste svoje kolumne da priključuju sopstvene knjige. Praksa se zove "Pournelijev imperativ".

### Fiksiranje D-Flat

Prvo, podignite auto...

Ušli smo u četvrtu verziju D-Flat-a i već imam neke ispravke koda koji je prethodno objavljen. Tri lista ovog meseca su nove verzije "dflat.h" [LISTING_ONE](#listing_one), "config.h" [LISTING_TWO](#listing_two), i "window.c" [LISTING_THREE](#listing_three). Ovo su neke od datoteka koje su se promenile. Ostale ću objaviti zajedno sa nekim novim kodom sledećeg meseca. Ovaj put ću objasniti šta se promenilo u sistemu što utiče na postojeći kod.

Jedna od mojih primedbi na mnoge prozore i biblioteke korisničkog interfejsa je da mali programi često moraju da nose težinu većine biblioteke u .EKSE datoteci, iako programu nije potrebna većina funkcija. Otkrio sam da se ista stvar dešava sa D-Flat-om. Kako sam dodavao funkcije korisničkom interfejsu, veličina aplikacijskog programa je rasla, iako se kod aplikacije nije promenio. To je bilo u suprotnosti sa mojim prvobitnim namerama za D-Flat, tako da sam morao da uradim nešto po tom pitanju. Odlučio sam da prepustim C preprocesoru da bude menadžer konfiguracije programa. Ako aplikaciji nije potrebna funkcija D-Flat, ne morate da kompajlirate kod koji podržava tu funkciju. S obzirom na to da se kod za podršku diskretnih funkcija može naći bilo gde u mnogim D-Flat izvornim datotekama, najbolji način da se potisnu funkcije su uslovi kompajliranja.

"config.h", sadrži neke nove globalne definicije. Nazivaju se INCLUDE_SISTEM_MENUS, INCLUDE_DIALOG_BOKSES, i tako dalje. Ako ne želite sistemske menije u svom programu, na primer, uklonite globalnu definiciju INCLUDE_SISTEM_MENUS iz config.h i ponovo kompajlirate D-Flat biblioteku. Efekat će biti da nećete moći da pomerate, menjate veličinu, minimizirate i maksimizirate prozore, ali će program biti mnogo manji. Jednostavan CUA program koji ne koristi nijednu od konfigurabilnih funkcija biće čak 60K manji od onog koji koristi sve.

Selektivnim uključivanjem i isključivanjem globalnih definicija, možete da generišete ili potisnete sistemske menije, prikaz vremena u danu, interfejs za više dokumenata, trake za pomeranje, senke, dijaloške okvire, međuspremnik, višelinijske liste i okvire za uređivanje, i evidentiranje poruka. Ovaj pristup znači da D-Flat nije nužno statična biblioteka koju koristite nepromenjenu za sve svoje projekte. Umesto toga, modifikujete funkcije biblioteke tako da odgovaraju potrebama specifične aplikacije. Ova praksa je u skladu sa mojim originalnim ciljevima dizajna, koji uključuju korišćenje kompajlera umesto dodatnog koda za izvršavanje za dodavanje klasa prozora, menija i dijaloških okvira u aplikaciju.

Postoji niz drugih promena u vindov.c, koje odražavaju probleme koje sam imao sa isecanjem i preslikavanjem kada aplikacija ima mnogo prozora za dokumente. Nove datoteke dflat.h i config.h koriste nove INCLUDE_global simbole. dflat.h dodaje neke članove strukturi prozora kako bi podržao funkcije koje sam dodao u D-Flat u oblastima o kojima još nismo razgovarali, ali im je potrebno više podataka u strukturi. dflat.h ima većinu prototipova i makroa za metode klase vindovs, tako da su se i oni na sličan način promenili i povećali. Umesto specificiranja imena CLASS enuma, dflat.h sada uključuje datoteku pod nazivom class.h, koju ćete videti u kasnijoj koloni. Ova datoteka lokalizuje definiciju klasa. Slična datoteka pod nazivom dflatmsg.h definiše kodove poruka. Morao sam da podelim ove definicije u druge datoteke zaglavlja jer su za novu funkciju potrebna imena klasa i poruka u formatu stringa koji se može prikazati. Nisam želeo da održavam suvišne liste klasa i poruka.

Nova funkcija koja koristi nizove naziva klasa i poruka je pomoć za otklanjanje grešaka koja evidentira odabrane D-Flat poruke u tekstualnu datoteku. Kasniji deo opisuje ovu funkciju.

Neke od drugih izvornih datoteka su se promenile i neću ih ponovo objavljivati jer su promene male i treba da imaju mali uticaj na rad programa. Svako ko želi da koristi D-Flat treba da ga preuzme sa CompuServe-a ili TelePath-a. Najnovija verzija uključuje retku tekstualnu datoteku koja dokumentuje D-Flat funkcije, klase i poruke.

#### LISTING_ONE

```c
/* ------------- dflat.h ----------- */
#ifndef WINDOW_H
#define WINDOW_H

#define VERSION "Version 3 Beta"
#define TRUE 1
#define FALSE 0

#include "system.h"
#include "config.h"
#include "rect.h"
#include "menu.h"
#include "keys.h"
#include "commands.h"
#include "config.h"
#include "dialbox.h"

/* ------ integer type for message parameters ----- */
typedef long PARAM;
#define TE(m) m
typedef enum window_class    {
#include "classes.h"
} CLASS;
typedef struct window {
    CLASS class;           /* window class                  */
    char *title;           /* window title                  */
    struct window *parent; /* parent window                 */
    int (*wndproc)
        (struct window *, enum messages, PARAM, PARAM);
    /* ---------------- window dimensions ----------------- */
    RECT rc;               /* window coordinates (0/0 to 79/24)  */
    int ht, wd;            /* window height and width       */
    RECT RestoredRC;       /* restored condition rect       */
    /* -------------- linked list pointers ---------------- */
    struct window *next;        /* next window on screen    */
    struct window *prev;        /* previous window on screen*/
    struct window *nextbuilt;   /* next window built        */
    struct window *prevbuilt;   /* previous window built    */
    int attrib;                 /* Window attributes        */
    char *videosave;            /* video save buffer        */
    int condition;              /* Restored, Maximized, Minimized */
    int restored_attrib;        /* attributes when restored */
    void *extension;            /* -> menus, dialog box, etc*/
    struct window *PrevMouse;
    struct window *PrevKeyboard;
    /* ----------------- text box fields ------------------ */
    int wlines;     /* number of lines of text              */
    int wtop;       /* text line that is on the top display */
    char *text;     /* window text                          */
    int textlen;    /* text length                          */
    int wleft;      /* left position in window viewport     */
    int textwidth;  /* width of longest line in textbox     */
    int BlkBegLine; /* beginning line of marked block       */
    int BlkBegCol;  /* beginning column of marked block     */
    int BlkEndLine; /* ending line of marked block          */
    int BlkEndCol;  /* ending column of marked block        */
    int HScrollBox; /* position of horizontal scroll box    */
    int VScrollBox; /* position of vertical scroll box      */
    /* ----------------- list box fields ------------------ */
    int selection;  /* current selection                    */
    int AddMode;    /* adding extended selections mode      */
    int AnchorPoint;/* anchor point for extended selections */
    int SelectCount;/* count of selected items              */
    /* ----------------- edit box fields ------------------ */
    int CurrCol;    /* Current column                       */
    int CurrLine;   /* Current line                         */
    int WndRow;     /* Current window row                   */
    int TextChanged; /* TRUE if text has changed            */
    char *DeletedText; /* for undo                          */
    int DeletedLength; /*  "   "                            */
    /* ---------------- dialog box fields ----------------- */
    struct window *dFocus; /* control that has the focus    */
    int ReturnCode;        /* return code from a dialog box */
} * WINDOW;
#include "message.h"
#include "classdef.h"
#include "video.h"
enum Condition     {
    ISRESTORED, ISMINIMIZED, ISMAXIMIZED
};
void LogMessages (WINDOW, MESSAGE, PARAM, PARAM);
void MessageLog(WINDOW);
/* ------- window methods ----------- */
#define WindowHeight(w)      ((w)->ht)
#define WindowWidth(w)       ((w)->wd)
#define BorderAdj(w)         (TestAttribute(w,HASBORDER)?1:0)
#define TopBorderAdj(w)      ((TestAttribute(w,TITLEBAR) &&   \
                              TestAttribute(w,HASMENUBAR)) ?  \
                              2 : (TestAttribute(w,TITLEBAR | \
                              HASMENUBAR | HASBORDER) ? 1 : 0))
#define ClientWidth(w)       (WindowWidth(w)-BorderAdj(w)*2)
#define ClientHeight(w)      (WindowHeight(w)-TopBorderAdj(w)-\
                              BorderAdj(w))
#define WindowRect(w)        ((w)->rc)
#define GetTop(w)            (RectTop(WindowRect(w)))
#define GetBottom(w)         (RectBottom(WindowRect(w)))
#define GetLeft(w)           (RectLeft(WindowRect(w)))
#define GetRight(w)          (RectRight(WindowRect(w)))
#define GetClientTop(w)      (GetTop(w)+TopBorderAdj(w))
#define GetClientBottom(w)   (GetBottom(w)-BorderAdj(w))
#define GetClientLeft(w)     (GetLeft(w)+BorderAdj(w))
#define GetClientRight(w)    (GetRight(w)-BorderAdj(w))
#define GetParent(w)         ((w)->parent)
#define GetTitle(w)          ((w)->title)
#define NextWindow(w)        ((w)->next)
#define PrevWindow(w)        ((w)->prev)
#define NextWindowBuilt(w)   ((w)->nextbuilt)
#define PrevWindowBuilt(w)   ((w)->prevbuilt)
#define GetClass(w)          ((w)->class)
#define GetAttribute(w)      ((w)->attrib)
#define AddAttribute(w,a)    (GetAttribute(w) |= a)
#define ClearAttribute(w,a)  (GetAttribute(w) &= ~(a))
#define TestAttribute(w,a)   (GetAttribute(w) & (a))
#define isVisible(w)         (GetAttribute(w) & VISIBLE)
#define SetVisible(w)        (GetAttribute(w) |= VISIBLE)
#define ClearVisible(w)      (GetAttribute(w) &= ~VISIBLE)
#define gotoxy(w,x,y) cursor(w->rc.lf+(x)+1,w->rc.tp+(y)+1)
WINDOW CreateWindow(CLASS,char *,int,int,int,int,void*,WINDOW,
       int (*)(struct window *,enum messages,PARAM,PARAM),int);
void AddTitle(WINDOW, char *);
void InsertTitle(WINDOW, char *);
void DisplayTitle(WINDOW, RECT *);
void RepaintBorder(WINDOW, RECT *);
void ClearWindow(WINDOW, RECT *, int);
#ifdef INCLUDE_SYSTEM_MENUS
void clipline(WINDOW, int, char *);
#else
#define clipline(w,x,c) /**/
#endif
void writeline(WINDOW, char *, int, int, int);
void writefull(WINDOW, char *, int);
void SetNextFocus(WINDOW,int);
void SetPrevFocus(WINDOW,int);
void PutWindowChar(WINDOW, int, int, int);
void GetVideoBuffer(WINDOW);
void RestoreVideoBuffer(WINDOW);
void CreatePath(char *, char *, int, int);
int LineLength(char *);
RECT AdjustRectangle(WINDOW, RECT);
#define DisplayBorder(wnd) RepaintBorder(wnd, NULL)
#define DefaultWndProc(wnd,msg,p1,p2)    \
    (*classdefs[FindClass(wnd->class)].wndproc)(wnd,msg,p1,p2)
#define BaseWndProc(class,wnd,msg,p1,p2)    \
    (*classdefs[DerivedClass(class)].wndproc)(wnd,msg,p1,p2)
#define NULLWND ((WINDOW) 0)
struct LinkedList    {
    WINDOW FirstWindow;
    WINDOW LastWindow;
};
extern struct LinkedList Focus;
extern struct LinkedList Built;
extern WINDOW inFocus;
extern WINDOW CaptureMouse;
extern WINDOW CaptureKeyboard;
extern int foreground, background;
extern int WindowMoving;
extern int WindowSizing;
extern int TextMarking;
extern char *Clipboard;
extern WINDOW SystemMenuWnd;
/* --------------- border characters ------------- */
#define FOCUS_NW       '\xc9'
#define FOCUS_NE       '\xbb'
#define FOCUS_SE       '\xbc'
#define FOCUS_SW       '\xc8'
#define FOCUS_SIDE     '\xba'
#define FOCUS_LINE     '\xcd'
#define NW             '\xda'
#define NE             '\xbf'
#define SE             '\xd9'
#define SW             '\xc0'
#define SIDE           '\xb3'
#define LINE           '\xc4'
#define LEDGE          '\xc3'
#define REDGE          '\xb4'
/* ------------- scroll bar characters ------------ */
#define UPSCROLLBOX    '\x1e'
#define DOWNSCROLLBOX  '\x1f'
#define LEFTSCROLLBOX  '\x11'
#define RIGHTSCROLLBOX '\x10'
#define SCROLLBARCHAR  176
#define SCROLLBOXCHAR  178
#define CHECKMARK      251      /* menu item toggle         */
/* ----------------- title bar characters ----------------- */
#define CONTROLBOXCHAR '\xf0'
#define MAXPOINTER     24      /* maximize token            */
#define MINPOINTER     25      /* minimize token            */
#define RESTOREPOINTER 18      /* restore token             */
/* --------------- text control characters ---------------- */
#define APPLCHAR     176    /* fills application window     */
#define SHORTCUTCHAR '~'    /* prefix: shortcut key display */
#define CHANGECOLOR  174    /* prefix to change colors      */
#define RESETCOLOR   175    /* reset colors to default      */
#define LISTSELECTOR   4    /* selected list box entry      */
/* ---- standard window message processing prototypes ----- */
int ApplicationProc(WINDOW, MESSAGE, PARAM, PARAM);
int NormalProc(WINDOW, MESSAGE, PARAM, PARAM);
int TextBoxProc(WINDOW, MESSAGE, PARAM, PARAM);
int ListBoxProc(WINDOW, MESSAGE, PARAM, PARAM);
int EditBoxProc(WINDOW, MESSAGE, PARAM, PARAM);
int MenuBarProc(WINDOW, MESSAGE, PARAM, PARAM);
int PopDownProc(WINDOW, MESSAGE, PARAM, PARAM);
int ButtonProc(WINDOW, MESSAGE, PARAM, PARAM);
int DialogProc(WINDOW, MESSAGE, PARAM, PARAM);
int SystemMenuProc(WINDOW, MESSAGE, PARAM, PARAM);
int HelpBoxProc(WINDOW, MESSAGE, PARAM, PARAM);
int MessageBoxProc(WINDOW, MESSAGE, PARAM, PARAM);
/* ------------- normal box prototypes ------------- */
int isWindow(WINDOW);
WINDOW inWindow(int, int);
int WndForeground(WINDOW);
int WndBackground(WINDOW);
int FrameForeground(WINDOW);
int FrameBackground(WINDOW);
int SelectForeground(WINDOW);
int SelectBackground(WINDOW);
void SetStandardColor(WINDOW);
void SetReverseColor(WINDOW);
void SetClassColors(CLASS);
WINDOW GetFirstChild(WINDOW);
WINDOW GetNextChild(WINDOW);
WINDOW GetLastChild(WINDOW);
WINDOW GetPrevChild(WINDOW);
#define HitControlBox(wnd, p1, p2)     \
    (TestAttribute(wnd, TITLEBAR)   && \
     TestAttribute(wnd, CONTROLBOX) && \
     p1 == 2 && p2 == 0)
/* -------- text box prototypes ---------- */
#define TextLine(wnd, sel) \
      (wnd->text + *((int *)(wnd->extension) + sel))
void WriteTextLine(WINDOW, RECT *, int, int);
void SetAnchor(WINDOW, int, int);
#define BlockMarked(wnd) (  wnd->BlkBegLine ||    \
                            wnd->BlkEndLine ||    \
                            wnd->BlkBegCol  ||    \
                            wnd->BlkEndCol)
#define ClearBlock(wnd) wnd->BlkBegLine = wnd->BlkEndLine =  \
                        wnd->BlkBegCol  = wnd->BlkEndCol = 0;
#define GetText(w)        ((w)->text)
void ClearTextPointers(WINDOW);
void BuildTextPointers(WINDOW);
/* --------- menu prototypes ---------- */
int CopyCommand(char *, char *, int, int);
void PrepOptionsMenu(void *, struct Menu *);
void PrepEditMenu(void *, struct Menu *);
void PrepWindowMenu(void *, struct Menu *);
void BuildSystemMenu(WINDOW);
int isActive(MENU *, int);
void ActivateCommand(MENU *,int);
void DeactivateCommand(MENU *,int);
int GetCommandToggle(MENU *,int);
void SetCommandToggle(MENU *,int);
void ClearCommandToggle(MENU *,int);
void InvertCommandToggle(MENU *,int);
/* ------------- list box prototypes -------------- */
int ItemSelected(WINDOW, int);
/* ------------- edit box prototypes ----------- */
#ifdef INCLUDE_MULTILINE
#define isMultiLine(wnd)     TestAttribute(wnd, MULTILINE)
#else
#define isMultiLine(wnd)     FALSE
#endif
/* --------- message box prototypes -------- */
void MessageBox(char *, char *);
void ErrorMessage(char *);
int TestErrorMessage(char *);
int YesNoBox(char *);
int MsgHeight(char *);
int MsgWidth(char *);

#ifdef INCLUDE_DIALOG_BOXES
/* ------------- dialog box prototypes -------------- */
int DialogBox(WINDOW,DBOX *,int(*)(struct window *,enum messages,PARAM,PARAM));
int DlgOpenFile(char *, char *);
int DlgSaveAs(char *);
void GetDlgListText(WINDOW, char *, enum commands);
int DlgDirList(WINDOW, char *, enum commands, enum commands, unsigned);
int RadioButtonSetting(DBOX *, enum commands);
void PushRadioButton(DBOX *, enum commands);
void PutItemText(WINDOW, enum commands, char *);
void GetItemText(WINDOW, enum commands, char *, int);
void SetCheckBox(DBOX *, enum commands);
void ClearCheckBox(DBOX *, enum commands);
int CheckBoxSetting(DBOX *, enum commands);
WINDOW ControlWindow(DBOX *, enum commands);
CTLWINDOW *ControlBox(DBOX *, WINDOW);
#endif
/* ------------- help box prototypes ------------- */
void HelpFunction(void);
void LoadHelpFile(void);
#define swap(a,b){int x=a;a=b;b=x;}

#endif
```

#### LISTING_TWO

```c
/* ---------------- config.h -------------- */

#ifndef CONFIG_H
#define CONFIG_H

#define DFLAT_APPLICATION "MEMOPAD"

#ifdef BUILD_FULL_DFLAT
#define INCLUDE_SYSTEM_MENUS
#define INCLUDE_CLOCK
#define INCLUDE_MULTIDOCS
#define INCLUDE_SCROLLBARS
#define INCLUDE_SHADOWS
#define INCLUDE_DIALOG_BOXES
#define INCLUDE_CLIPBOARD
#define INCLUDE_MULTILINE
#define INCLUDE_LOGGING
#endif

struct colors {
    /* ------------ colors ------------ */
    char ApplicationFG,  ApplicationBG;
    char NormalFG,       NormalBG;
    char ButtonFG,       ButtonBG;
    char ButtonSelFG,    ButtonSelBG;
    char DialogFG,       DialogBG;
    char ErrorBoxFG,     ErrorBoxBG;
    char MessageBoxFG,   MessageBoxBG;
    char HelpBoxFG,      HelpBoxBG;
    char InFocusTitleFG, InFocusTitleBG;
    char TitleFG,        TitleBG;
    char DummyFG,        DummyBG;
    char TextBoxFG,      TextBoxBG;
    char TextBoxSelFG,   TextBoxSelBG;
    char TextBoxFrameFG, TextBoxFrameBG;
    char ListBoxFG,      ListBoxBG;
    char ListBoxSelFG,   ListBoxSelBG;
    char ListBoxFrameFG, ListBoxFrameBG;
    char EditBoxFG,      EditBoxBG;
    char EditBoxSelFG,   EditBoxSelBG;
    char EditBoxFrameFG, EditBoxFrameBG;
    char MenuBarFG,      MenuBarBG;
    char MenuBarSelFG,   MenuBarSelBG;
    char PopDownFG,      PopDownBG;
    char PopDownSelFG,   PopDownSelBG;
    char InactiveSelFG;
    char ShortCutFG;
};
/* ----------- configuration parameters ----------- */
typedef struct config {
    char version[sizeof DFLAT_APPLICATION + sizeof VERSION];
    char mono;         /* 0=color, 1=mono, 2=reverse mono    */
    int InsertMode;    /* Editor insert mode                 */
    int Tabs;          /* Editor tab stops                   */
    int WordWrap;      /* True to word wrap editor           */
    int Border;        /* True for application window border */
    int Title;         /* True for application window title  */
    int Texture;       /* True for textured appl window      */
    int ScreenLines;   /* Number of screen lines (25/43/50)  */
    struct colors clr; /* Colors                             */
} CONFIG;
extern CONFIG cfg;
extern struct colors color, bw, reverse;
int LoadConfig(void);
void SaveConfig(void);

#endif
```

#### LISTING_THREE

```c
/* ---------- window.c ------------- */
#include <stdio.h>
#include <conio.h>
#include <stdlib.h>
#include <string.h>
#include <dos.h>
#include "dflat.h"

WINDOW inFocus = NULLWND;

int foreground, background;   /* current video colors */
static void TopLine(WINDOW, int, RECT);

/* --------- create a window ------------ */
WINDOW CreateWindow(
    CLASS class,              /* class of this window       */
    char *ttl,                /* title or NULL              */
    int left, int top,        /* upper left coordinates     */
    int height, int width,    /* dimensions                 */
    void *extension,          /* pointer to additional data */
    WINDOW parent,            /* parent of this window      */
    int (*wndproc)(struct window *,enum messages,PARAM,PARAM),
    int attrib)               /* window attribute           */
{
    WINDOW wnd = malloc(sizeof(struct window));
    get_videomode();
    if (wnd != NULLWND)    {
        int base;
        /* ----- height, width = -1: fill the screen ------- */
        if (height == -1)
            height = SCREENHEIGHT;
        if (width == -1)
            width = SCREENWIDTH;
        /* ----- coordinates -1, -1 = center the window ---- */
        if (left == -1)
            wnd->rc.lf = (SCREENWIDTH-width)/2;
        else
            wnd->rc.lf = left;
        if (top == -1)
            wnd->rc.tp = (SCREENHEIGHT-height)/2;
        else
            wnd->rc.tp = top;
        wnd->attrib = attrib;
        if (ttl != NULL)
            AddAttribute(wnd, TITLEBAR);
        if (wndproc == NULL)
            wnd->wndproc = classdefs[FindClass(class)].wndproc;
        else
            wnd->wndproc = wndproc;
        /* ---- derive attributes of base classes ---- */
        base = class;
        while (base != -1)    {
            int tclass = FindClass(base);
            AddAttribute(wnd, classdefs[tclass].attrib);
            base = classdefs[tclass].base;
        }
        if (parent && !TestAttribute(wnd, NOCLIP))    {
            /* -- keep upper left within borders of parent - */
            wnd->rc.lf = max(wnd->rc.lf,GetClientLeft(parent));
            wnd->rc.tp = max(wnd->rc.tp,GetClientTop(parent));
        }
        wnd->class = class;
        wnd->extension = extension;
        wnd->rc.rt = GetLeft(wnd)+width-1;
        wnd->rc.bt = GetTop(wnd)+height-1;
        wnd->ht = height;
        wnd->wd = width;
        wnd->title = NULL;
        if (ttl != NULL)
            InsertTitle(wnd, ttl);
        wnd->next = wnd->prev = wnd->dFocus = NULLWND;
        wnd->parent = parent;
        wnd->videosave = NULL;
        wnd->condition = ISRESTORED;
        wnd->restored_attrib = 0;
        wnd->RestoredRC = wnd->rc;
        wnd->PrevKeyboard = wnd->PrevMouse = NULL;
        wnd->DeletedText = NULL;
        SendMessage(wnd, CREATE_WINDOW, 0, 0);
        if (isVisible(wnd))
            SendMessage(wnd, SHOW_WINDOW, 0, 0);
    }
    return wnd;
}
/* -------- add a title to a window --------- */
void AddTitle(WINDOW wnd, char *ttl)
{
    InsertTitle(wnd, ttl);
    SendMessage(wnd, BORDER, 0, 0);
}
/* ----- insert a title into a window ---------- */
void InsertTitle(WINDOW wnd, char *ttl)
{
    if ((wnd->title=realloc(wnd->title,strlen(ttl)+1)) != NULL)
        strcpy(wnd->title, ttl);
}
/* ------- write a character to a window area at x,y ------- */
void PutWindowChar(WINDOW wnd, int x, int y, int c)
{
    int x1 = GetLeft(wnd)+x;
    int y1 = GetTop(wnd)+y;
    if (isVisible(wnd))    {
        if (!TestAttribute(wnd, NOCLIP))    {
            WINDOW wnd1 = GetParent(wnd);
            while (wnd1 != NULLWND)    {
                /* --- clip character to parent's borders -- */
                if (x1 < GetClientLeft(wnd1)   ||
                    x1 > GetClientRight(wnd1)  ||
                    y1 > GetClientBottom(wnd1) ||
                    y1 < GetClientTop(wnd1))
                        return;
                wnd1 = GetParent(wnd1);
            }
        }
        if (x1 < SCREENWIDTH && y1 < SCREENHEIGHT)
            wputch(wnd, c, x, y);
    }
}
static char line[161];
#ifdef INCLUDE_SYSTEM_MENUS
/* ----- clip line if it extends below the bottom of parent window ------ */
static int clipbottom(WINDOW wnd, int y)
{
    if (!TestAttribute(wnd, NOCLIP))    {
        WINDOW wnd1 = GetParent(wnd);
        while (wnd1 != NULLWND)    {
            if (GetClientTop(wnd)+y > GetClientBottom(wnd1)+1)
                return TRUE;
            wnd1 = GetParent(wnd1);
        }
    }
    return GetTop(wnd)+y > SCREENHEIGHT;
}
/* -- clip portion of line that extends past right margin of parent window-- */
void clipline(WINDOW wnd, int x, char *ln)
{
    WINDOW pwnd = GetParent(wnd);
    int x1 = strlen(ln);
    int i = 0;
    if (!TestAttribute(wnd, NOCLIP))    {
        while (pwnd != NULLWND)    {
            x1 = GetClientRight(pwnd) - GetLeft(wnd) - x + 1;
            pwnd = GetParent(pwnd);
        }
    }
    else if (GetLeft(wnd) + x > SCREENWIDTH)
        x1 = SCREENWIDTH-GetLeft(wnd) - x;
    /* --- adjust the clipping offset for color controls --- */
    if (x1 < 0)
        x1 = 0;
    while (i < x1)    {
        if ((unsigned char) ln[i] == CHANGECOLOR)
            i += 3, x1 += 3;
        else if ((unsigned char) ln[i] == RESETCOLOR)
            i++, x1++;
        else
            i++;
    }
    ln[x1] = '\0';
}
#else
#define clipbottom(w,y) FALSE
#endif
/* ------ write a line to video window client area ------ */
void writeline(WINDOW wnd, char *str, int x, int y, int pad)
{
    static char wline[120];
    if (!clipbottom(wnd, y))
    {
        char *cp;
        int len;
        int dif;
        memset(wline, 0, sizeof wline);
        len = LineLength(str);
        dif = strlen(str) - len;
        strncpy(wline, str, ClientWidth(wnd) + dif);
        if (pad)    {
            cp = wline+strlen(wline);
            while (len++ < ClientWidth(wnd)-x)
                *cp++ = ' ';
        }
        clipline(wnd, x, wline);
        wputs(wnd, wline, x, y);
    }
}
/* -- write a line to video window (including the border) -- */
void writefull(WINDOW wnd, char *str, int y)
{
    if (!clipbottom(wnd, y))    {
        strcpy(line, str);
        clipline(wnd, 0, line);
        wputs(wnd, line, 0, y);
    }
}
RECT AdjustRectangle(WINDOW wnd, RECT rc)
{
    /* -------- adjust the rectangle ------- */
    if (TestAttribute(wnd, HASBORDER))    {
        if (RectLeft(rc) == 0)
            --rc.rt;
        else if (RectLeft(rc) < RectRight(rc) &&
                RectLeft(rc) < WindowWidth(wnd)+1)
            --rc.lf;
    }
    if (TestAttribute(wnd, HASBORDER | TITLEBAR))    {
        if (RectTop(rc) == 0)
            --rc.bt;
        else if (RectTop(rc) < RectBottom(rc) &&
                RectTop(rc) < WindowHeight(wnd)+1)
            --rc.tp;
    }
    RectRight(rc) = max(RectLeft(rc),min(RectRight(rc),WindowWidth(wnd)));
    RectBottom(rc) = max(RectTop(rc),min(RectBottom(rc),WindowHeight(wnd)));
    return rc;
}
/* -------- display a window's title --------- */
void DisplayTitle(WINDOW wnd, RECT *rcc)
{
    int tlen = min(strlen(wnd->title), WindowWidth(wnd)-2);
    int tend = WindowWidth(wnd)-3-BorderAdj(wnd);
    RECT rc;
    if (rcc == NULL)
        rc = RelativeWindowRect(wnd, WindowRect(wnd));
    else
        rc = *rcc;
    rc = AdjustRectangle(wnd, rc);
    if (SendMessage(wnd, TITLE, LPARAM(rcc), 0))    {
        if (wnd == inFocus)    {
            foreground = cfg.clr.InFocusTitleFG;
            background = cfg.clr.InFocusTitleBG;
        }
        else    {
            foreground = cfg.clr.TitleFG;
            background = cfg.clr.TitleBG;
        }
        memset(line,' ',WindowWidth(wnd));
        if (wnd->condition != ISMINIMIZED)
            strncpy(line + ((WindowWidth(wnd)-2 - tlen) / 2),
                wnd->title, tlen);
        if (TestAttribute(wnd, CONTROLBOX))
            line[2-BorderAdj(wnd)] = CONTROLBOXCHAR;
#ifdef INCLUDE_SYSTEM_MENUS
        if (TestAttribute(wnd, MINMAXBOX))    {
            switch (wnd->condition)    {
                case ISRESTORED:
                    line[tend+1] = MAXPOINTER;
                    line[tend]   = MINPOINTER;
                    break;
                case ISMINIMIZED:
                    line[tend+1] = MAXPOINTER;
                    break;
                case ISMAXIMIZED:
                    line[tend]   = MINPOINTER;
                    line[tend+1] = RESTOREPOINTER;
                    break;
                default:
                    break;
            }
        }
#endif
        line[RectRight(rc)+1] = line[tend+3] = '\0';
        writeline(wnd, line+RectLeft(rc),
                       RectLeft(rc)+BorderAdj(wnd),
                       0,
                       FALSE);
    }
}
#ifdef INCLUDE_SHADOWS
/* --- display right border shadow character of a window --- */
static void near shadow_char(WINDOW wnd, int y)
{
    int fg = foreground;
    int bg = background;
    int x = WindowWidth(wnd);
    int c = videochar(GetLeft(wnd)+x, GetTop(wnd)+y);

    if (TestAttribute(wnd, SHADOW) == 0)
        return;
    foreground = DARKGRAY;
    background = BLACK;
    PutWindowChar(wnd, x, y, c);
    foreground = fg;
    background = bg;
}
/* --- display the bottom border shadow line for a window -- */
static void near shadowline(WINDOW wnd, RECT rc)
{
    int i;
    int y = GetBottom(wnd)+1;

    if ((TestAttribute(wnd, SHADOW)) == 0)
        return;
    if (!clipbottom(wnd, WindowHeight(wnd)))    {
        int fg = foreground;
        int bg = background;
        for (i = 0; i < WindowWidth(wnd)+1; i++)
            line[i] = videochar(GetLeft(wnd)+i, y);
        line[i] = '\0';
        foreground = DARKGRAY;
        background = BLACK;
        clipline(wnd, 1, line);
        line[RectRight(rc)+1] = '\0';
        if (RectLeft(rc) == 0)
            rc.lf++;
        wputs(wnd, line+RectLeft(rc), RectLeft(rc),WindowHeight(wnd));
        foreground = fg;
        background = bg;
    }
}
#endif
/* ------- display a window's border ----- */
void RepaintBorder(WINDOW wnd, RECT *rcc)
{
    int y;
    int lin, side, ne, nw, se, sw;
    RECT rc, clrc;
    if (!TestAttribute(wnd, HASBORDER))
        return;
    if (rcc == NULL)    {
        rc = RelativeWindowRect(wnd, WindowRect(wnd));
#ifdef INCLUDE_SHADOWS
        if (TestAttribute(wnd, SHADOW))    {
            rc.rt++;
            rc.bt++;
        }
#endif
    }
    else
        rc = *rcc;
    clrc = AdjustRectangle(wnd, rc);
    if (wnd == inFocus)    {
        lin  = FOCUS_LINE;
        side = FOCUS_SIDE;
        ne   = FOCUS_NE;
        nw   = FOCUS_NW;
        se   = FOCUS_SE;
        sw   = FOCUS_SW;
    }
    else    {
        lin  = LINE;
        side = SIDE;
        ne   = NE;
        nw   = NW;
        se   = SE;
        sw   = SW;
    }
    line[WindowWidth(wnd)] = '\0';
    /* ---------- window title ------------ */
    if (TestAttribute(wnd, TITLEBAR))
        if (RectTop(rc) == 0)
            if (RectLeft(rc) < WindowWidth(wnd)-BorderAdj(wnd))
                DisplayTitle(wnd, &rc);
    foreground = FrameForeground(wnd);
    background = FrameBackground(wnd);
    /* -------- top frame corners --------- */
    if (RectTop(rc) == 0)    {
        if (RectLeft(rc) == 0)
            PutWindowChar(wnd, 0, 0, nw);
        if (RectLeft(rc) < WindowWidth(wnd))    {
            if (RectRight(rc) >= WindowWidth(wnd)-1)
                PutWindowChar(wnd, WindowWidth(wnd)-1, 0, ne);
            TopLine(wnd, lin, rc);
        }
    }
    /* ----------- window body ------------ */
    for (y = RectTop(rc); y <= RectBottom(rc); y++)    {
        int ch;
        if (y == 0 || y >= WindowHeight(wnd)-1)
            continue;
        if (RectLeft(rc) == 0)
            PutWindowChar(wnd, 0, y, side);
        if (RectLeft(rc) < WindowWidth(wnd) &&
                RectRight(rc) >= WindowWidth(wnd)-1)    {
#ifdef INCLUDE_SCROLLBARS
            if (TestAttribute(wnd, VSCROLLBAR))
                ch = (    y == 1 ? UPSCROLLBOX       :
                            y == WindowHeight(wnd)-2  ?
                                DOWNSCROLLBOX       :
                            y-1 == wnd->VScrollBox    ?
                                SCROLLBOXCHAR       :
                                SCROLLBARCHAR );
            else
#endif
                ch = side;
            PutWindowChar(wnd, WindowWidth(wnd)-1, y, ch);
        }
#ifdef INCLUDE_SHADOWS
        if (RectRight(rc) == WindowWidth(wnd))
            shadow_char(wnd, y);
#endif
    }
    if (RectTop(rc) <= WindowHeight(wnd)-1 &&
            RectBottom(rc) >= WindowHeight(wnd)-1)    {
        /* -------- bottom frame corners ---------- */
        if (RectLeft(rc) == 0)
            PutWindowChar(wnd, 0, WindowHeight(wnd)-1, sw);
        if (RectLeft(rc) < WindowWidth(wnd) &&
                RectRight(rc) >= WindowWidth(wnd)-1)
            PutWindowChar(wnd, WindowWidth(wnd)-1,
                WindowHeight(wnd)-1, se);
        /* ----------- bottom line ------------- */
        memset(line,lin,WindowWidth(wnd)-1);
#ifdef INCLUDE_SCROLLBARS
        if (TestAttribute(wnd, HSCROLLBAR))    {
            line[0] = LEFTSCROLLBOX;
            line[WindowWidth(wnd)-3] = RIGHTSCROLLBOX;
            memset(line+1, SCROLLBARCHAR, WindowWidth(wnd)-4);
            line[wnd->HScrollBox] = SCROLLBOXCHAR;
        }
#endif
        line[WindowWidth(wnd)-2] = line[RectRight(rc)] = '\0';
        if (RectLeft(rc) != RectRight(rc) ||
           (RectLeft(rc) && RectLeft(rc) < WindowWidth(wnd)-1))
            writeline(wnd,
                line+(RectLeft(clrc)),
                RectLeft(clrc)+1,
                WindowHeight(wnd)-1,
                FALSE);
#ifdef INCLUDE_SHADOWS
        if (RectRight(rc) == WindowWidth(wnd))
            shadow_char(wnd, WindowHeight(wnd)-1);
#endif
    }
#ifdef INCLUDE_SHADOWS
    if (RectBottom(rc) == WindowHeight(wnd))
        /* ---------- bottom shadow ------------- */
        shadowline(wnd, rc);
#endif
}
static void TopLine(WINDOW wnd, int lin, RECT rc)
{
    if (TestAttribute(wnd, TITLEBAR | HASMENUBAR))
        return;
    if (RectLeft(rc) < RectRight(rc))    {
        /* ----------- top line ------------- */
        memset(line,lin,WindowWidth(wnd)-1);
        line[RectRight(rc)] = '\0';
        writeline(wnd, line+RectLeft(rc),
            RectLeft(rc)+1, 0, FALSE);
    }
}
/* ------ clear the data space of a window -------- */
void ClearWindow(WINDOW wnd, RECT *rcc, int clrchar)
{
    if (isVisible(wnd))    {
        int y;
        RECT rc;
        if (rcc == NULL)
            rc = RelativeWindowRect(wnd, WindowRect(wnd));
        else
            rc = *rcc;
        if (RectLeft(rc) == 0)
            RectLeft(rc) = BorderAdj(wnd);
        if (RectRight(rc) > WindowWidth(wnd)-1)
            RectRight(rc) = WindowWidth(wnd)-1;
        SetStandardColor(wnd);
        memset(line, clrchar, sizeof line);
        line[RectRight(rc)+1] = '\0';
        for (y = RectTop(rc); y <= RectBottom(rc); y++)    {
            if (y < TopBorderAdj(wnd) ||
                    y >= WindowHeight(wnd)-1)
                continue;
            writeline(wnd,
                line+(RectLeft(rc)),
                RectLeft(rc),
                y,
                FALSE);
        }
    }
}
/* -- adjust a window's rectangle to clip it to its parent -- */
static RECT near ClipRect(WINDOW wnd)
{
    RECT rc;
    rc = wnd->rc;
#ifdef INCLUDE_SHADOWS
    if (TestAttribute(wnd, SHADOW))    {
        RectBottom(rc)++;
        RectRight(rc)++;
    }
#endif
    if (!TestAttribute(wnd, NOCLIP))    {
        WINDOW pwnd = GetParent(wnd);
        if (pwnd != NULLWND)    {
            RectTop(rc) = max(RectTop(rc),
                        GetClientTop(pwnd));
            RectLeft(rc) = max(RectLeft(rc),
                        GetClientLeft(pwnd));
            RectRight(rc) = min(RectRight(rc),
                        GetClientRight(pwnd));
            RectBottom(rc) = min(RectBottom(rc),
                        GetClientBottom(pwnd));
        }
    }
    RectRight(rc) = min(RectRight(rc), SCREENWIDTH-1);
    RectBottom(rc) = min(RectBottom(rc), SCREENHEIGHT-1);
    RectLeft(rc) = min(RectLeft(rc), SCREENWIDTH-1);
    RectTop(rc) = min(RectTop(rc), SCREENHEIGHT-1);
    return rc;
}
/* -- get the video memory that is to be used by a window -- */
void GetVideoBuffer(WINDOW wnd)
{
    RECT rc;
    int ht;
    int wd;
    rc = ClipRect(wnd);
    ht = RectBottom(rc) - RectTop(rc) + 1;
    wd = RectRight(rc) - RectLeft(rc) + 1;
    wnd->videosave = realloc(wnd->videosave, (ht * wd * 2));
    get_videomode();
    if (wnd->videosave != NULL)
        getvideo(rc, wnd->videosave);
}
/* --- restore the video memory that was used by a window -- */
void RestoreVideoBuffer(WINDOW wnd)
{
    if (wnd->videosave != NULL)    {
        RECT rc;
        rc = ClipRect(wnd);
        storevideo(rc, wnd->videosave);
        free(wnd->videosave);
        wnd->videosave = NULL;
    }
}
/* ------ compute the logical line length of a window ------ */
int LineLength(char *ln)
{
    int len = strlen(ln);
    char *cp = ln;
    while ((cp = strchr(cp, CHANGECOLOR)) != NULL)    {
        cp++;
        len -= 3;
    }
    cp = ln;
    while ((cp = strchr(cp, RESETCOLOR)) != NULL)    {
        cp++;
        --len;
    }
    return len;
}
```

Copyright © 1991, Dr. Dobb's Journal
