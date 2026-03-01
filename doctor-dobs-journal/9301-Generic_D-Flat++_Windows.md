
# C PROGRAMMING 9301

## Generički D-Flat++ prozori - Al Stevens

Ovog meseca nastavlja se razvoj D-Flat++ sa osnovnom klasom DFVindov iz koje potiču svi ostali prozori. Pošto je ovo prvo izdanje nove godine, počeću pregledom šta je D-Flat++ i odakle je došao. Pre otprilike dve godine tražio sam biblioteku C funkcija koja je implementirala CUA interfejs za DOS aplikacije u tekstualnom režimu. CUA je zajednički izraz za traku menija/iskačući meni, meni/dijaloški okvir, korisnički interfejs miša/tastature koji je zajednički za toliko aplikacija i operativnih okruženja. Vindovs i Presentation Manager su kompatibilni sa CUA u svojim grafičkim korisničkim interfejsima. Većina Microsoft i Borland programa u tekstualnom režimu koristi CUA konvencije. Čak i ako nije savršena, CUA bi mogla imati blagotvoran efekat ubijanja svih tih glupih tužbi koje izgledaju i osećaju. Više o njima kasnije. Ako vaša aplikacija koristi CUA, onda niko - čak ni oni recenzenti časopisa koji se ne slažu - ne može kritikovati korisnički interfejs. Dobro ili loše, to je standard.

U svojoj potrazi, pronašao sam nekoliko CUA biblioteka u DOS tekstualnom režimu, ali svaka od njih je bila više ili manje od onoga što mi je trebalo. Dakle, sa samo malo iskustva u programiranju u Vindovs-u, odlučio sam da napravim takvu biblioteku, onu koja koristi model programiranja zasnovan na događajima, sličan onom koji koristi Vindovs. Tako je rođen D-Flat. Počeo je da bude jednostavan, mali paket koji podržava moje zahteve, i odlučio sam da ga objavim u kolumni. Odziv čitalaca je bio pozitivan, a biblioteka je u skladu s tim rasla, prošla je kroz 15 verzija i trebalo je godinu i po za objavljivanje. Mnogi čitaoci su pitali za verziju C++, a ti zahtevi su pokrenuli eksperiment da se vidi kako se model zasnovan na porukama uklapa u C++ objektno orijentisanu paradigmu. Rezultat je bio D-Flat++.

Prethodne dve kolone nisu imale DF++ radnu površinu i objekte uređaja zavisne od platforme. Ovog meseca ćemo pogledati sloj prenosivosti da bismo normalizovali kod za različite kompajlere i definiciju osnovne DFVindov klase.

### Sloj prenosivosti

[LISTING_ONE](#listing_one) je "dflatdef.h", datoteka zaglavlja koja definiše globalne vrednosti i konvertuje Borland C++ idiome u Microsoft C++. Nabrojani tip VndTipe identifikuje tipove prozora za različite klase prozora u hijerarhiji klasa DF++. Iz liste možete videti koji tipovi prozora su do sada projektovani. Verovatno ću ga dodati kako se projekat nastavi. Ovde su definisani Bool enum i minimalni i maksimalni makroi. Izjave koje slede omogućavaju mi da kompajliram DF++ koji sam razvio pod Borland C++ sa Microsoft C++. Razlike su u pozivima funkcija specifičnim za PC. Dva kompajlera su kompatibilna u pogledu čistog C++ koda. Ovaj sloj prenosivosti osigurava da makroi FP_SEG, FP_OFF i MK_FP rade isto kao funkcije koje čitaju i pišu I/O portove, pristupaju funkcijama BIOS tastature i dobijaju i postavljaju vektore prekida. Dva kompajlera različito implementiraju funkcije prekida, a makro INTERRUPTARGS podržava tu razliku, kao i neki uslovni uslovi za vreme prevođenja gde god program deklariše pokazivače na funkcije prekida. Makroi zavirivanje i probijanje u dflatdef.h pružaju te funkcije za korisnike Microsoft C-a.

Kasnije, kada prenesem DF++ na druge C++ kompajlere, ova datoteka je mesto gde će se desiti većina promena. Takođe, kako se projekat razvija, ovde ću staviti sve nove zavisnosti od prenosivosti.

### Klasa DFVindov

[LISTING_TWO](#listing_two) je "dfwindow.h", datoteka zaglavlja koja definiše osnovnu klasu DFVindov, klasu iz koje će proizaći svi DF++ prozori. Klasa obuhvata sve što je zajedničko svim prozorima, bez obzira na njihov tip. Datoteka zaglavlja definiše vrednosti za zastavice atributa prozora. Svaka zastavica je sopstvena pozicija bita i oni određuju da li prozor može da se pomeri ili promeni veličinu; da li ima ivicu, naslov, kontrolni okvir, minimalni okvir, maksimalan okvir, senku, traku menija, statusnu traku ili trake za pomeranje; da li štedi video memoriju koju zauzima ili se jednostavno prefarba na zahtev; da li je njegov prikaz isečen na klijentsku oblast svog roditelja; i da li je ili nije komponenta okvira svog roditelja, kao što je traka za pomeranje ili statusna traka.

Datoteka dfwindow.h definiše karakteristike prikaza prozora kao što su struktura za određivanje boja prozora, boje za senku prozora i tekstualni karakteri koji čine okvir prozora. Definicija klase DFVindov je osnovna klasa za sve prozore. Uključuje članove podataka koji određuju tip prozora, njegovu veličinu i poziciju ekrana, naslov, atribute i reči stanja, adresu bafera za čuvanje video zapisa, boje i adresu nadređenog prozora prozora. Postoji glava liste za povezanu listu podređenih prozora i sledeći i prethodni pokazivači na susedne srodne prozore.

Zatim tu su funkcije članova. DFVindov objekat ima nekoliko konstruktora koji omogućavaju korisnicima da instanciraju prozor pomoću kombinacija parametara koji uključuju naslov, veličinu, poziciju i roditelj. Postoje funkcije koje omogućavaju izvedenim prozorima da upisuju znakove i nizove u video prostor prozora. Postoje funkcije koje vraćaju poziciju i veličinu prozora, boje, roditelj, atribute, stanje i tip, i koje menjaju te stvari. Na kraju, tu su funkcije API-ja, koje su ekvivalentne porukama u modelu programiranja zasnovanom na D-Flat porukama.

[LISTING_THREE](#listing_three) je "dfwindow.cpp“, izvorni kod za funkcije člana DF-Vindov koje nisu ugrađene. Ove funkcije konstruišu i uništavaju prozor, otvaraju i zatvaraju ga, prikazuju ga i sakrivaju i obrađuju njegove poruke, uključujući pomeranje, promenu veličine, minimiziranje, maksimiziranje, vraćanje prozora i obradu poruka tastature i miša koje prosleđuju izvedene klase prozora.

Morate razumeti odnose između konstruisanja prozora i njegovog otvaranja i uništavanja i zatvaranja. Program konstruiše prozor tako što ga instancira kao objekat - bilo kao globalni objekat, objekat u okviru bloka okruženog zagradama, ili sa C++ operatorom nev. Konstruktor prozora takođe otvara prozor. Program uništava prozor dozvoljavajući mu da izađe van opsega ili, u slučaju prozora napravljenog sa novim operatorom, korišćenjem C++ operatora brisanja. Destruktor prozora takođe zatvara prozor. Program može zatvoriti napravljeni prozor bez da ga uništi. Program tada može ponovo otvoriti isti prozor bez pozivanja konstruktora prozora. Konstruisani prozor je onaj koji je instanciran. Otvoreni prozor je onaj koji je dostupan programu. Ova razlika je neophodna zbog CUA konvencije koja omogućava korisniku da zatvori prozor radnjom tastature ili miša. Ovo bi se moglo desiti na način asinhroni sa programom instanciranja prozora i prozora koji izlazi van opsega. Nije moguće definisati C++ klasu sa objektima deklarisanim samo sa novim operatorom. Stoga, pošto DF++ ne može da primeni konvenciju koja kaže da će korisnici samo instancirati prozore sa novim operatorom, DF++ ne može pretpostaviti da uvek može da koristi operator delete kada korisnik zatvori prozor. Štaviše, ne postoji način da sistem zna gde program koji koristi može da sačuva adresu instanciranog prozora, tako da ne postoji način da se postavi na NULL. Bilo bi glomazno zahtevati prozor da obavesti svog "vlasnika" softvera da je uništen iznutra kao rezultat radnje korisnika. Iz tog razloga, DF++ implementira model gde je prozor konstruisan i prozor se može zatvoriti pre uništenja. C++ objekat i dalje postoji, ali logički prozor je zatvoren. Prozor zna da se zatvara i neće primati dalje poruke o događajima, iako je objekat i dalje u opsegu.

Da bi se stvari dodatno zakomplikovale, napravljeni otvoreni prozor može biti vidljiv ili skriven. Skriveni prozor nije u vidu korisnika i stoga neće primati poruke o događajima u vezi sa uređajem.

Naučićete u kasnijim kolonama kako sistem iskačućih menija koristi prednosti ovog ponašanja. Sistem konstruiše sve iskačuće menije kada konstruiše traku menija. Zatim otvara i zatvara prozore iskačućeg menija kada ih korisnik odabere.

#### LISTING_ONE

```c
// -------- dflatdef.h

#ifndef DFLATDEF_H
#define DFLATDEF_H

// -------- window class types
enum WndType {
    DFlatWindow,
    ApplicationWindow,
    TextboxWindow,
    FrameWindow,
    ScrollbarWindow,
    MenubarWindow,
    ListboxWindow,
    PopdownWindow,
    EditboxWindow,
    DialogboxWindow,
    PushButtonWindow,
    RadioButtonWindow,
    StatusbarWindow
};
enum Bool {False, True};
inline int max(int a, int b) { return a > b ? a : b; }
inline int min(int a, int b) { return a < b ? a : b; }

// ----- portablility layer for MSC C++
#ifdef MSC

#define keyhit       kbhit
#undef FP_OFF
#undef FP_SEG
#undef MK_FP
#define FP_OFF(p)    ((unsigned)(p))
#define FP_SEG(p)    ((unsigned)((unsigned long)(p) >> 16))
#define MK_FP(s,o)   ((void far *) \
               (((unsigned long)(s) << 16) | (unsigned)(o)))
#define outp         _outp
#define inp          _inp
#define bioskey      _bios_keybrd
#define getvect(v)   _dos_getvect(v)
#define setvect(v,f) _dos_setvect(v,f)

#define INTERRUPTARGS void
#else
#define INTERRUPTARGS ...
#endif

#define poke(a,b,c) (*((int far*)MK_FP((a),(b))) = (int)(c))
#define peek(a,b)   (*((int far*)MK_FP((a),(b))))

#endif
```

#### LISTING_TWO

```c
// ------------ dfwindow.h

#ifndef DFWINDOW_H
#define DFWINDOW_H

#include <stdio.h>
#include <string.h>
#include <dos.h>
#include <stdlib.h>

#include "dflatdef.h"
#include "rectangl.h"
#include "strings.h"

// -------- window attribute flags
const int MOVEABLE   = 0x0001;
const int SIZEABLE   = 0x0002;
const int BORDER     = 0x0004;
const int TITLEBAR   = 0x0008;
const int CONTROLBOX = 0x0010;
const int MINBOX     = 0x0020;
const int MAXBOX     = 0x0040;
const int SHADOW     = 0x0080;
const int SAVESELF   = 0x0100;
const int NOCLIP     = 0x0200;
const int MENUBAR    = 0x0400;
const int STATUSBAR  = 0x0800;
const int VSCROLLBAR = 0x1000;
const int HSCROLLBAR = 0x2000;
const int FRAMEWND   = 0x4000;

// --------------- Color Macros
enum Colors {
    BLACK,
    BLUE,
    GREEN,
    CYAN,
    RED,
    MAGENTA,
    BROWN,
    LIGHTGRAY,
    DARKGRAY,
    LIGHTBLUE,
    LIGHTGREEN,
    LIGHTCYAN,
    LIGHTRED,
    LIGHTMAGENTA,
    YELLOW,
    WHITE
};

// ------ window shadow attributes
const unsigned char ShadowFG = DARKGRAY;
const unsigned char ShadowBG = BLACK;

// ----- minimized icon dimensions
const int IconWidth = 10;
const int IconHeight = 3;

// --------------- border characters
const unsigned char FOCUS_NW   = '\xc9';
const unsigned char FOCUS_NE   = '\xbb';
const unsigned char FOCUS_SE   = '\xbc';
const unsigned char FOCUS_SW   = '\xc8';
const unsigned char FOCUS_SIDE = '\xba';
const unsigned char FOCUS_LINE = '\xcd';
const unsigned char NW         = '\xda';
const unsigned char NE         = '\xbf';
const unsigned char SE         = '\xd9';
const unsigned char SW         = '\xc0';
const unsigned char SIDE       = '\xb3';
const unsigned char LINE       = '\xc4';

// ----------------- title bar characters
const unsigned char CONTROLBOXCHAR = '\xf0';
const unsigned char MAXPOINTER     = 24;
const unsigned char MINPOINTER     = 25;
const unsigned char RESTOREPOINTER = 18;

// ----------- window states
enum WndState     {
    OPENING,
    ISRESTORED,
    ISMINIMIZED,
    ISMAXIMIZED,
    ISCLOSING,
    CLOSED
};
// ---------- window colors
struct Color {
    Colors fg, bg;      // standard colors
    Colors sfg, sbg;    // selected text colors
    Colors ffg, fbg;    // window frame colors
    Colors hfg, hbg;    // highlighted text colors
};
class Application;
class StatusBar;
class PopDown;

class DFWindow    {
protected:
    WndType windowtype;   // window type
    int prevmouseline;    // holders for
    int prevmousecol;     // mouse coordinates
private:
    String *title;              // window title
    // -------------- window attributes
    int restored_attrib;        // attributes when restored
    Bool clipoverride;          // True to override clipping
    Rect restored_rc;           // restored state rect
    // ------- for painting overlapping windows
    void PaintOverLappers();
    // ----- control menu
    PopDown *ctlmenu;
    void OpenCtlMenu();
    void DeleteCtlMenu();
    // --------- common window contructor code
    void InitWindow(char *ttl,
        int lf, int tp, int ht, int wd, DFWindow *par);
    void InitWindow(int lf, int tp, int ht, int wd,
        DFWindow *par);
    virtual void SetColors();
    void Enqueue();
    void Dequeue();
    Bool ClipParent(int &x, int y, String *ln);
    void WriteChar(int ch, int x, int y,
        Rect &rc, int fg, int bg);
    void WriteString(String &ln, int x, int y,
        Rect &rc, int fg, int bg);
    Rect PositionIcon();
    friend class StatusBar;
protected:
    // --------------- video memory save data
    char *videosave;      // video save buffer
    Bool visible;         // True = window has been shown
    int attrib;           // Window attribute flags
    Bool DblBorder;       // True = dbl border on focus
    Color colors;         // window colors
    WndState windowstate; // Restored, Maximized, Minimized, Closing
    Rect rect;            // window coordinates (0/0 to 79/24)
    char clearch;         // for clearing the window
    // ------ previous capture focus handle
    DFWindow *prevcapture;
    // -------------- window geneology
    DFWindow *parent;           // parent window
    // -------- children
    DFWindow *first;            // first child window
    DFWindow *last;             // last child window
    // -------- siblings
    DFWindow *next;             // next sibling window
    DFWindow *prev;             // previous sibling window
    // -------- system-wide
    void NextSiblingFocus();
    void WriteClientString(String &ln,int x,int y,int fg,int bg);
    void WriteWindowString(String &ln,int x,int y,int fg,int bg);
    void WriteWindowChar(int ch,int x,int y,int fg,int bg);
    void WriteClientChar(int ch,int x,int y,int fg,int bg);
    // ------------- client window coordinate adjustments
    virtual void AdjustBorders();
    int BorderAdj;              // adjust for border
    int TopBorderAdj;           // adjust for top border
    int BottomBorderAdj;        // adjust for bottom border
    // -----
    Bool HitControlBox(int x, int y)
        { return (Bool)(x-Left() == 2 && y-Top() == 0 &&
            (attrib & CONTROLBOX)); }
    friend void DispatchEvents(Application *ApWnd);
    friend DFWindow *MouseWindow();
    friend DFWindow *inWindow(int x, int y, int &fg, int &bg);
public:
    // -------- constructors
    DFWindow(char *ttl, int lf, int tp, int ht, int wd,
                DFWindow *par)
        { InitWindow(ttl, lf, tp, ht, wd, par); }
    DFWindow(char *ttl, int ht, int wd, DFWindow *par)
        { InitWindow(ttl, -1, -1, ht, wd, par); }
    DFWindow(int lf, int tp, int ht, int wd, DFWindow *par)
        { InitWindow(lf, tp, ht, wd, par); }
    DFWindow(int ht, int wd, DFWindow *par)
        { InitWindow(-1, -1, ht, wd, par); }
    DFWindow(char *ttl, DFWindow *par = NULL)
        { InitWindow(ttl, 0, 0, -1, -1, par); }
    // -------- destructor
    virtual ~DFWindow()
        { if (windowstate != CLOSED) CloseWindow(); }
    // ------- window dimensions and position
    Rect WindowRect()    { return rect; }
    Rect ShadowedRect();
    int Right()          { return rect.Right(); }
    int Left()           { return rect.Left(); }
    int Top()            { return rect.Top(); }
    int Bottom()         { return rect.Bottom(); }
    int Height()         { return Bottom() - Top() + 1; }
    int Width()          { return Right() - Left() + 1; }
    // ------ client space dimensions and position
    Rect ClientRect();
    int ClientRight()    { return Right()-BorderAdj; }
    int ClientLeft()     { return Left()+BorderAdj; }
    int ClientTop()      { return Top()+TopBorderAdj; }
    int ClientBottom()   { return Bottom()-BottomBorderAdj; }
    int ClientHeight()   { return Height()-TopBorderAdj-
                              BottomBorderAdj; }
    int ClientWidth()    { return Width()-BorderAdj*2; }

    DFWindow *Parent()   { return parent; }
    Bool isVisible()     { return visible; }
    int Attribute()      { return attrib; }
    void SetAttribute(int atr)     { attrib |= atr; AdjustBorders(); }
    void ClearAttribute(int atr)   { attrib &= ~atr; AdjustBorders(); }
    WndType WindowType() { return windowtype; }
    // ----- Control Menu messages
    void CtlMenuMove();
    void CtlMenuSize();
    // -------- API messages
    virtual void OpenWindow();
    virtual void CloseWindow();
    virtual void Show();
    virtual void Hide();
    virtual Bool SetFocus();
    virtual void ResetFocus();
    virtual void EnterFocus(DFWindow *child) {}
    virtual void LeaveFocus(DFWindow *child) {}
    void CaptureFocus();
    void ReleaseFocus();
    virtual void Paint();
    virtual void Paint(Rect rc);
    virtual void Border();
    virtual void Shadow();
    virtual void Title();
    virtual void ClearWindow();
    virtual void ShiftChanged(int sk);
    virtual void Keyboard(int key);
    virtual void DoubleClick(int mx, int my);
    virtual void LeftButton(int mx, int my);
    virtual void ButtonReleased(int, int);
    virtual void MouseMoved(int, int) {}
    virtual void Move(int x, int y);
    virtual void Size(int x, int y);
    virtual void ParentSized(int, int) {}
    virtual void ClockTick() {}
    void Minimize();
    void Maximize();
    void Restore();
    WndState State() { return windowstate; }
    Rect &VisibleRect();
    Colors CLientFG()    { return colors.fg; }
    Colors ClientBG()    { return colors.bg; }
    Colors SelectedFG()  { return colors.sfg; }
    Colors SelectedBG()  { return colors.sbg; }
    Colors FrameFG()     { return colors.ffg; }
    Colors FrameBG()     { return colors.fbg; }
    Colors HighlightFG() { return colors.ffg; }
    Colors HighlightBG() { return colors.fbg; }
};
inline DFWindow *inWindow(int x, int y)
{
    int fg, bg;
    return inWindow(x, y, fg, bg);
}
inline void WriteClientString(String &ln,int x,int y,int fg,int bg)
{
    WriteString(ln,x+ClientLeft(),y+ClientTop(),ClientRect(),fg,bg);
}
inline void WriteWindowString(String &ln,int x,int y,int fg,int bg)
{
    WriteString(ln,x+Left(),y+Top(),ShadowedRect(),fg,bg);
}
inline void WriteWindowChar(int ch,int x,int y,int fg,int bg)
{
    WriteChar(ch, x+Left(), y+Top(),ShadowedRect(), fg, bg);
}
inline void WriteClientChar(int ch,int x,int y,int fg,int bg)
{
    WriteChar(ch, x+ClientLeft(),y+ClientTop(),ClientRect(),fg,bg);
}
#endif
```

#### LISTING_THREE

```c
// ------------ dfwindow.cpp

#include "dflatpp.h"
#include "frame.h"
#include "desktop.h"

// -------- common constructor initialization code
void DFWindow::InitWindow(int lf, int tp,int ht, int wd, DFWindow *par)
{
    windowtype = DFlatWindow;
    if (lf == -1)
        lf = (desktop.screen().Width()-wd)/2;
    if (tp == -1)
        tp = (desktop.screen().Height()-ht)/2;
    if (ht == -1)
        ht = desktop.screen().Height();
    if (wd == -1)
        wd = desktop.screen().Width();
    attrib = restored_attrib = 0;
    title = NULL;
    ctlmenu = NULL;
    videosave = NULL;
    visible = False;
    clipoverride = False;
    first = NULL;
    last = NULL;
    next = NULL;
    prev = NULL;
    prevcapture = NULL;
    parent = par;
    BorderAdj = TopBorderAdj = BottomBorderAdj = 0;
    Rect rcc(lf, tp, lf+wd-1, tp+ht-1);
    restored_rc = rect = rcc;
    SetColors();
    clearch = ' ';
    DblBorder = True;
    Enqueue();
    if (parent == NULL)
        SetAttribute(SAVESELF);
    windowstate = ISRESTORED;
}
void DFWindow::InitWindow(char *ttl, int lf, int tp,
          int ht, int wd, DFWindow *par)
{
    InitWindow(lf, tp, ht, wd, par);
    attrib |= TITLEBAR;
    title = new String(ttl);
}
void DFWindow::OpenWindow()
{
    if (windowstate == CLOSED)
        InitWindow(*title, Left(), Top(),Height(), Width(), parent);
}
void DFWindow::CloseWindow()
{
    windowstate = ISCLOSING;
    Hide();
    // ------- close window's children
    DFWindow *Wnd = first;
    while (Wnd != NULL)    {
        Wnd->CloseWindow();
        Wnd = Wnd->next;
    }
    // ------ delete this window's memory
    if (title != NULL)
        delete title;
    if (videosave != NULL)
        delete videosave;
    DeleteCtlMenu();
    if (this == desktop.InFocus())    {
        if (desktop.FocusCapture() == this)
            ReleaseFocus();
        else if (parent == NULL ||
                parent->windowstate == ISCLOSING)
            desktop.SetFocus(parent ? parent : NULL);
        else    {
            NextSiblingFocus();
            if (this == desktop.InFocus())
                if (!parent->SetFocus())
                    desktop.SetFocus(NULL);
        }
    }
    Dequeue();
    windowstate = CLOSED;
}
// -------- set the fg/bg colors for the window
void DFWindow::SetColors()
{
    colors.fg =
    colors.sfg =
    colors.ffg =
    colors.hfg = WHITE;
    colors.bg =
    colors.sbg =
    colors.fbg =
    colors.hbg = BLACK;
}
// ---------- display the window
void DFWindow::Show()
{
    if (attrib & SAVESELF)    {
        Rect rc = ShadowedRect();
        if (videosave == NULL)    {
            int sz = rc.Height() * rc.Width() * 2;
            videosave = new char[sz];
        }
        if (!visible)
            desktop.screen().GetBuffer(rc, videosave);
    }
    visible = True;
    clipoverride = True;
    Paint();
    Border();
    Shadow();
    clipoverride = False;
    if (windowstate != ISMINIMIZED)    {
        // --- show the children of this window
        DFWindow *Wnd = first;
        while (Wnd != NULL)    {
            if (Wnd->windowstate != ISCLOSING)
                Wnd->Show();
            Wnd = Wnd->next;
        }
    }
}
Rect DFWindow::ShadowedRect()
{
    Rect rc = rect;
    if (attrib & SHADOW)    {
        rc.Right()++;
        rc.Bottom()++;
    }
    return rc;
}
void DFWindow::Hide()
{
    if (visible)    {
        Rect rc = rect;
        Bool HasShadow = (Bool)((attrib & SHADOW) != 0);
        if (HasShadow)    {
            rc.Bottom()++;
            rc.Right()++;
        }
        visible = False;
        // ----- hide the children
        DFWindow *Wnd = first;
        while (Wnd != NULL)    {
            Wnd->Hide();
            Wnd = Wnd->next;
        }
        if (videosave != NULL)    {
            desktop.screen().PutBuffer(rc, videosave);
            delete videosave;
            videosave = NULL;
        }
        else if (parent != NULL)    {
            if (parent->isVisible())    {
                parent->Paint(ShadowedRect());
                PaintOverLappers();
            }
        }
    }
}
void DFWindow::Keyboard(int key)
{
    switch (key)    {
        case CTRL_F4:
            CloseWindow();
            break;
        case ALT_HYPHEN:
            OpenCtlMenu();
            break;
        default:
            // --- send all unprocessed keystrokes
            //     to the parent window
            if (parent != NULL)
                parent->Keyboard(key);
            break;
    }
}
void DFWindow::ShiftChanged(int sk)
{
    if (parent != NULL)
        parent->ShiftChanged(sk);
}
void DFWindow::DoubleClick(int mx, int my)
{
    if (HitControlBox(mx, my))
        CloseWindow();
}
void DFWindow::LeftButton(int mx, int my)
{
    if (my == Top())    {
        // ----- hit the top border
        int x = mx-Left();
        int wd = Width();
        if (x == wd-2)    {
            // ---- hit the restore or maximize box
            if (windowstate != ISRESTORED)
                Restore();
            else if (attrib & MAXBOX)
                Maximize();
        }
        else if (x == wd-3)    {
            // ----- hit the minimize box
            if (windowstate != ISMINIMIZED &&
                    (attrib & MINBOX))
                Minimize();
        }
        else if (HitControlBox(mx, my) &&
                (attrib & CONTROLBOX))
            // ------- hit the control box
            OpenCtlMenu();
        else if ((attrib & MOVEABLE) &&
                windowstate != ISMAXIMIZED)
            // ---- none of the above, move the window
            new Frame(this, mx);
    }
    else if ((attrib & SIZEABLE) && windowstate == ISRESTORED)
        if (mx == Right() && my == Bottom())
            // --- hit the lower right corner, size the window
            new Frame(this);
    prevmouseline = my;
    prevmousecol = mx;
}
void DFWindow::ButtonReleased(int, int)
{
    prevmouseline = -1;
    prevmousecol = -1;
}
Rect DFWindow::ClientRect()
{
    Rect rc(ClientLeft(), ClientTop(),
        ClientRight(), ClientBottom());
    return rc;
}
// ------------ move a window
void DFWindow::Move(int x, int y)
{
    int xdif = x - Left();
    int ydif = y - Top();
    if (xdif == 0 && ydif == 0)
        return;
    Bool wasVisible = visible;
    if (wasVisible)
        Hide();
    int ht = Height();
    int wd = Width();
    rect.Left() = x;
    rect.Top() = y;
    rect.Right() = Left()+wd-1;
    rect.Bottom() = Top()+ht-1;
    if (windowstate == ISRESTORED)
        restored_rc = rect;
    DFWindow *Wnd = first;
    while (Wnd != NULL)    {
        Wnd->Move(Wnd->Left()+xdif, Wnd->Top()+ydif);
        Wnd = Wnd->next;
    }
    if (wasVisible)
        Show();
}
// ------------ size a window
void DFWindow::Size(int x, int y)
{
    int xdif = x - Right();
    int ydif = y - Bottom();
    if (xdif == 0 && ydif == 0)
        return;
    Bool wasVisible = visible;
    if (wasVisible)
        Hide();
    rect.Right() = x;
    rect.Bottom() = y;
    if (windowstate == ISRESTORED)
        restored_rc = rect;
    DFWindow *Wnd = first;
    while (Wnd != NULL)    {
        Wnd->ParentSized(xdif, ydif);
        Wnd = Wnd->next;
    }
    if (wasVisible)
        Show();
}
void DFWindow::Minimize()
{
    if (windowstate == ISRESTORED)
        restored_rc = rect;
    Rect rc = PositionIcon();
    Hide();
    windowstate = ISMINIMIZED;
    Move(rc.Left(), rc.Top());
    Size(rc.Right(), rc.Bottom());
    Show();
}
void DFWindow::Maximize()
{
    restored_rc = rect;
    Rect rc(0, 0, desktop.screen().Width()-1,desktop.screen().Height()-1);
    if (parent != NULL)
        rc = parent->ClientRect();
    Hide();
    windowstate = ISMAXIMIZED;
    Move(rc.Left(), rc.Top());
    Size(rc.Right(), rc.Bottom());
    Show();
}
void DFWindow::Restore()
{
    Hide();
    Move(restored_rc.Left(), restored_rc.Top());
    Size(restored_rc.Right(), restored_rc.Bottom());
    windowstate = ISRESTORED;
    if (this == desktop.InFocus())
        Show();
    else
        SetFocus();
}
// ---- compute lower right icon space in a rectangle
static Rect LowerRight(Rect &prc)
{
    Rect rc(prc.Right()-IconWidth,prc.Bottom()-IconHeight,0,0);
    rc.Right() = rc.Left() + IconWidth-1;
    rc.Bottom() =  rc.Top() + IconHeight-1;
    return rc;
}
// ----- compute a position for a minimized window icon
Rect DFWindow::PositionIcon()
{
    Rect rc(desktop.screen().Width()-IconWidth,
            desktop.screen().Height()-IconHeight,
            desktop.screen().Width()-1,
            desktop.screen().Height()-1);
    if (parent != NULL)    {
        Rect prc = parent->rect;
        rc = LowerRight(prc);
        // --- search for icon available location
        DFWindow *Wnd = parent->first;
        while (Wnd != NULL)    {
            if (Wnd->windowstate == ISMINIMIZED)    {
                Rect rc1= Wnd->rect;
                if (rc1.Left() == rc.Left() &&
                        rc1.Top() == rc.Top())    {
                    rc.Left() -= IconWidth;
                    rc.Right() -= IconWidth;
                    if (rc.Left() < prc.Left()+1)   {
                        rc.Left() =
                            prc.Right()-IconWidth;
                        rc.Right() =
                            rc.Left()+IconWidth-1;
                        rc.Top() -= IconHeight;
                        rc.Bottom() -= IconHeight;
                        if (rc.Top() < prc.Top()+1)
                            return LowerRight(prc);
                    }
                    break;
                }
            }
            Wnd = Wnd->next;
        }
    }
    return rc;
}
```

Copyright © 1993, Dr. Dobb's Journal
