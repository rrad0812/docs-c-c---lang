
# C PROGRAMMING 9312

## Alati - Al Stivens

### D-Flat++: Toolbars i TED

Svideo mi se način na koji sam implementirao dijaloške okvire u D-Flat++. Klasu izvodite iz klase DialogBox i u nju ugrađujete kontrolne objekte. To je sve što je potrebno. Razlog zbog kojeg su Windows programeri bili potrebni kompajleri resursa je taj što kod za implementaciju dijaloškog okvira ili menija ne liči na izlaz, strukturalno ili na bilo koji drugi način. To je uključivalo gomilu poziva funkcija. Tabelarni format jezika kompajlera resursa je bio poboljšanje jer je dao mnemoničku reprezentaciju dizajnu. D-Flat++ zamenjuje dizajn klase za jezik resursa i ne gubi ništa od pogodnosti notacije u procesu. Toliko je dobro funkcionisalo da sam koristio isti pristup za klase ToolBar i ToolButton. Svoju klasu trake sa alatkama izvodite iz osnovne klase ToolBar i u nju ugrađujete ToolButton objekte. Zatim ugrađujete objekat izvedene klase trake sa alatkama u svoju izvedenu klasu aplikacije. To je funkcionisalo tako dobro, da sam se vratio na način na koji je implementirana traka menija i promenio je da koristim isti pristup.

Nije važno koliko dobro nešto funkcioniše, uvek možemo pronaći razlog da to popravimo.

Ovog meseca ću vam pokazati novi primer aplikacije koja demonstrira biblioteku klasa D-Flat++. To je jednostavan uređivač teksta koji se zove TED. Ima traku menija, statusnu traku i traku sa alatkama i omogućava vam da radite na jednoj tekstualnoj datoteci u isto vreme. Nakon diskusije o TED-u, pogledaću kako funkcionišu traka sa alatkama i dugmad.

### TED verzija 1

[LISTING_ONE](#listing_one) je "ted.h", izvorni fajl koji definiše klasu Ted aplikacije. Verzija 1 je jednostavna aplikacija. Ima traku sa menijima i traku sa alatkama. Klasa aplikacije ugrađuje kontrolni objekat klase EditBox za rukovanje uređivanjem teksta. Klasa presreće poruku o veličini da bi promenila veličinu okvira za uređivanje kad god korisnik promeni veličinu prozora aplikacije. Postoji funkcija člana za pravljenje naslova prozora aplikacije tako da odražava ime datoteke koju korisnik uređuje. Druga funkcija člana traži od korisnika da sačuva datoteku kada su promene napravljene i program će izaći ili će koristiti prozor za drugu datoteku. Klasa aplikacije presreće poruku CloseWindow da bi izvršila taj test. Postoje konstruktor i destruktor, a postoji i funkcija člana za svaku od komandi u meniju.

[LISTING_TWO](#listing_two)dva, "ted.cpp", koji sadrži funkcije članova za klasu Ted. Ovde je malo na putu uređivača teksta. Za sve to brine klasa EditBox.

Meniji za TED su izgrađeni u izvornoj datoteci "tedmenu.cpp", [LISTING_THREE](#listing_three). Prvo su deklaracije svih komandi menija, koje su instance klase MenuSelection. O toj klasi i ostalim korišćenim u ovoj izvornoj datoteci sam raspravljao u mojoj koloni iz aprila 1993. godine. Uključujem ga ovde da vam pokažem kako izgledaju TED meniji. Postoje meniji File, Edit i Options. Meni File ima komande New, Open, Save, Save as i Exit. Meni Edit ima Cut, Copy i Paste. Meni Opcije ima Insert i Word Wrap. Svaka od ovih komandi, osim za Word Wrap, ima pridruženu funkciju člana u izvedenoj klasi aplikacije Ted. Word Wrap je prekidač.

TED traka sa alatkama je izgrađena u dve datoteke, "tedtools.h", [LISTING_FOUR](#listing_four) i "tedtools.cpp", [LISTING_FIVE](#listing_five) "tedtools.h" definiše traku sa alatkama, koja je klasa izvedena iz klase ToolBar. Ima tri dugmeta sa alatkama koje odgovaraju komandama menija Novo, Otvori i Sačuvaj. CUA programi obično koriste trake sa alatkama kao prečice mišem do komandi dostupnih u menijima. Aplikacija definiše traku sa alatkama tako što izvodi klasu iz osnovne klase ToolBar, ugrađuje neke ToolButton objekte u klasu i obezbeđuje konstruktor za inicijalizaciju dugmadi sa oznakama i funkcijama za pozivanje kada se pritisnu. [LISTING_FIVE](#listing_five) sadrži konstruktor koji inicijalizuje tri ToolButton objekta i poziva njihov metod SetButtonFunction da bi im dodelio funkcije.

### Klase dizajnera

Dizajn kompleksne biblioteke klasa iz temelja teži da sruši konvencionalnu mudrost. Taman kada pomislite da imate zrelu, radničku klasu, onu čije detalje možete sakriti i zaboraviti u pravom inženjerskom stilu crne kutije, to se vraća da vas proganja. Zašto? Zato što pokušavate da izvedete novu klasu iz toga, eto zašto. Ništa ne ponižava dizajnera klase više od spoznaje da neka stara klasa od poverenja ne može da ispuni svoje obećanje jer ne ugošćuje određenu novoizvedenu klasu sa ljupkošću i gostoprimstvom. Pogrešna je ideja da C++ i objektno orijentisani dizajn promovišu softver za višekratnu upotrebu nego tradicionalni proceduralni dizajn. C++ podstiče takav dizajn, naravno, jer struktura klase drži ključ za enkapsulaciju. Ali dizajni su napravljeni da se modifikuju, a ideja da to možete da uradite jednom, odozgo prema dole, odozdo prema gore, naopako, ili na bilo koji drugi način, i da uklesate detalje u kamenu da se nikada više ne pomeraju, je pogrešna.

Primer: Imam ovu zgodnu klasu PushButton u D-Flat++. Proističe iz osnovne Button klase koja rukuje svim detaljima zajedničkim za sva kontrolna dugmad - bez obzira da li su omogućena, trenutno podešavanje, kada se pritisne itd. Klasa PushButton implementira komandno dugme. Kada ga korisnik pritisne – zapravo ga otpusti – klasa poziva funkciju u klasi aplikacije programa. Klasa PushButton ima sve stvari koje su joj potrebne za gledanje tastature i miša dok korisnik drži taster ili dugme pritisnutim i pomera ga okolo - sve što mora da uradi pritisnuto dugme koje se dobro ponaša.

Dugmad na traci sa alatkama funkcionišu isto kao tasteri, osim što imaju drugačiji izgled i grupisani su na traci sa alatkama umesto da koegzistiraju sa drugim nepovezanim tasterima u okviru za dijalog. Savršeno za nasledstvo. Većina detalja je ista, a samo dve oblasti treba da budu prilagođene. Pa sam to uradio; Izveo sam klasu ToolButton iz klase PushButton. I sada radi. Problem je, međutim, bio u tome što je bilo puno detalja u funkcijama članova PushButton-a koje bi trebalo naslediti i nekih drugih koje bi trebalo prilagoditi. Šta je to, kažeš? Zašto je to problem? Nije li to upravo ono što je polimorfizam? Da. Osim što su ti razumni i zamenljivi detalji implementacije obično bili uvučeni zajedno u iste funkcije člana PushButton-a. Ne možete polimorfizovati deo funkcije i naslediti ostatak. Sve je na ovaj ili onaj način. Čisti objektno orijentisani dizajner bi prezao od starih helikoptera i nadjačao sve te virtuelne funkcije. Ne ja. Znam sve o tim detaljima u toj osnovnoj klasi. Ja sam ga dizajnirao i napravio. Da su kodirani drugačije – bolje – bilo bi lakše naslediti njihove dobre stvari i ignorisati delove koji mi nisu potrebni u novoj klasi. Pa sam samo ušao i promenio ga.

To je teško izbeći jer ne možete uvek unapred znati koje klase će postati osnovne klase i koji će njihovi delovi biti nasleđeni i zamenjeni. Još nisam smislio način da pogledam nastavu tokom dizajna i uočim sve funkcionalno nezavisne detalje implementacije. Mislim da je potrebno neko vreme da se to oko razvije, a još nisam video nijedan dizajn klase koji pokazuje takvu vrstu predviđanja dizajnera.

### Implementacija trake sa alatkama

Iz kutije za sapun. [LISTING_SIX](#listing_six) je "toolbar.h", izvorna datoteka koja definiše klase ToolBar i ToolButton. Traka sa alatkama je jednostavna stvar. To nije ništa više od prazne tačke na ekranu koja se proteže dužinom prozora aplikacije i koja je roditelj dugmadi alata.

Kada imate posla sa ekranom u tekstualnom režimu 25k80, nema puno prostora za gubljenje. Ova dugmad sa alatkama su visoka tri karaktera da bi omogućila okvire i oznaku. Traka sa alatkama će biti iste visine. Neophodno je obezbediti vizuelno razdvajanje između trake menija iznad i prozora dokumenta ispod. Nema dovoljno redova znakova da bi svi ovi delovi prozora imali svoje okvire, tako da implementacija koristi boju da ih razdvoji.

[LISTING_SEVEN](#listing_seven) je "toolbar.cpp", kod koji implementira klasu ToolBar. Sve što radi je da se pozicionira na pravo mesto u prozoru aplikacije i vidi da se njegova veličina na odgovarajući način menja kada se promeni veličina prozora aplikacije.

### Tedo-vi alati

[LISTING_EIGHT](#listing_eight) je "toolbutt.cpp". (Nemojte se smejati, oni vam daju samo osam znakova za ime datoteke.) Funkcije člana klase ToolButton su u ovoj datoteci. Da bih vizuelno razlikovao ove dugmad od drugih kontrola, implementirao sam ih kao male prozore sa okvirima. Koristim jednolinijske znakove okvira za gornju i levu stranu okvira i znakove u dva reda za desnu i donju stranu. Ta konfiguracija daje dugmetu 3-D izgled. Kada korisnik klikne na dugme, program menja jednostruku i dvostruku liniju, što čini da izgleda kao da se dugme uvlači kada se pritisne.

Postoje tri konfiguracije boja za dugme sa alatkama, jedna normalna, boja postavljena za vreme dok je dugme pritisnuto i jedna za kada je dugme onemogućeno. Tri objekta Color na vrhu toolbutt.cpp definišu ove boje. Dve BoxLines strukture definišu karaktere okvira za dve konfiguracije. Postoje dva konstruktora, jedan koji omogućava da se dugme automatski pozicionira na traci sa alatkama, a drugi da dodeljuje lokaciju ekrana za dugme. Klasa presreće metod Border da nacrta jedan ili drugi okvir, u zavisnosti od toga da li je dugme pritisnuto. Presretanje boje dodeljuje ispravnu konfiguraciju boje u zavisnosti od trenutnog stanja dugmeta. Presretanja SetFocus i ButtonCommand osiguravaju da koji god prozor imao fokus pre nego što je dugme pritisnuto, vrati ga nakon što se vrati komandna funkcija dugmeta. Osim toga, kod omogućava osnovnim klasama Button i PushButton da obavljaju većinu posla.

#### LISTING_ONE

```c
// --------- ted.h
#ifndef TED_H
#define TED_H

#include "dflatpp.h"
#include "fileopen.h"
#include "tedtools.h"

#define Df void (DFWindow::*)()
#define Ap void (Application::*)()

extern MenuBarItem TedMenu[];
extern MenuSelection InsertCmd, WordWrapCmd;

// ------- Ted application definition
class Ted : public Application {
    MenuBar menubar;
    TedTools toolbar;
    String  fname;
    EditBox editor;
protected:
    virtual void Size(int x, int y);
    void BuildTitle();
    void TestChanged();
public:
    Ted();
    virtual ~Ted() {}
    // ----- menu command functions
    void CmNew();
    void CmOpen();
    void CmSave();
    void CmSaveAs();
    void CmInsert();
    void CmCut() {}
    void CmCopy() {}
    void CmPaste() {}
    void CmExit()   { CloseWindow(); }
    virtual void CloseWindow();
};
#endif
```

#### LISTING_TWO

```c
// ------------- ted.cpp
#include <fstream.h>
#include "ted.h"

static char untitled[] = "(untitled)";
main()
{
    Ted ma;
    ma.Execute();
    return 0;
}

// ---- construct application
Ted::Ted() :  menubar(TedMenu, this),
              toolbar(this),
              editor(ClientLeft(),
                     ClientTop(),
                     ClientHeight(),
                     ClientWidth(),
                     this),
              fname(untitled)
{
    SetAttribute(SIZEABLE | MOVEABLE);
    SetClearChar(' ');
    BuildTitle();
    Show();
    editor.SetFocus();
}
// ---- builds the title with the current document name
void Ted::BuildTitle()
{
    SetTitle(String("TED: ") + fname);
    Title();
}
// ---- File/New Menu Command
void Ted::CmNew()
{
    TestChanged();
    editor.ClearText();
    editor.Paint();
}
// ---- File/Open Menu Command
void Ted::CmOpen()
{
    TestChanged();
    FileOpen fo;
    fo.Execute();
    if (fo.OKExit())    {
        editor.ClearText();
        fname = String(fo.FileName());
        BuildTitle();
        editor.ClearChanged();
        ifstream tfile(fname);
        String ip(200);
        while (!tfile.eof())    {
            tfile.getline((char *) ip, 200);
            editor.AddText(ip);
        }
        editor.Paint();
    }
}
// ---- File/Save Menu Command
void Ted::CmSave()
{
    if (fname == String(untitled))
        CmSaveAs();
    if (fname != String(untitled))    {
        editor.ClearChanged();
        ofstream tfile(fname);
        tfile.write((char *)*editor.Text(),editor.TextLength());
    }
}
// ---- File/Save As Menu Command
void Ted::CmSaveAs()
{
    SaveAs sa;
    sa.Execute();
    if (sa.OKExit())    {
        fname = String(sa.FileName());
        BuildTitle();
        CmSave();
    }
}
// ---- Options/Insert Menu Command
void Ted::CmInsert()
{
    if (InsertCmd.isToggled())
        desktop.keyboard().SetInsertMode();
    else
        desktop.keyboard().ClearInsertMode();
}
// ----- resize the editor when the application resizes
void Ted::Size(int x, int y)
{
    editor.Hide();
    editor.Size(editor.Right()+(x-Right()),
                editor.Bottom()+(y-Bottom()));
    Application::Size(x, y);
    editor.Show();
}
// ---- test for changes to the document before discarding
void Ted::TestChanged()
{
    if (editor.Changed())    {
        String msg(fname + "has changed. Save?");
        if (YesNo(msg))
            CmSave();
    }
}
// ---- test for changes before closing
void Ted::CloseWindow()
{
    TestChanged();
    Application::CloseWindow();
}
```

#### LISTING_THREE

```c
// ---------- tedmenu.cpp
#include "dflatpp.h"
#include "ted.h"

// --------- MenuSelection objects
MenuSelection
    NewCmd      ("~New",           (Ap) &Ted::CmNew ),
    OpenCmd     ("~Open...",       (Ap) &Ted::CmOpen ),
    SaveCmd     ("~Save",          (Ap) &Ted::CmSave ),
    SaveAsCmd   ("Save ~As...",    (Ap) &Ted::CmSaveAs ),
    ExitCmd     ("E~xit   Alt+F4", (Ap) &Ted::CmExit,  ALT_F4 ),
    CutCmd      ("~Cut    Ctrl+X", (Ap) &Ted::CmCut,   CTRL_X ),
    CopyCmd     ("C~opy   Ctrl+C", (Ap) &Ted::CmCopy,  CTRL_C ),
    PasteCmd    ("~Paste  Ctrl+V", (Ap) &Ted::CmPaste, CTRL_V ),
    InsertCmd   ("~Insert Ins",  (Ap) &Ted::CmInsert, On, INS ),
    WordWrapCmd ("~Word wrap",     On );

// --------- File menu definition
MenuSelection *File[] = {
    &NewCmd,
    &OpenCmd,
    &SaveCmd,
    &SaveAsCmd,
    &SelectionSeparator,
    &ExitCmd,
    0
};
MenuSelection *Edit[] = {
    &CutCmd,
    &CopyCmd,
    &PasteCmd,
    0
};
MenuSelection *Options[] = {
    &InsertCmd,
    &WordWrapCmd,
    0
};
// --------- menu bar definition
MenuBarItem TedMenu[] = {
    MenuBarItem( "~File",    File    ),
    MenuBarItem( "~Edit",    Edit    ),
    MenuBarItem( "~Options", Options ),
    MenuBarItem( 0 )
};
```

#### LISTING_FOUR

```c
// ---------- tedtools.h
#ifndef TEDTOOLS_H
#define TEDTOOLS_H

#include "toolbar.h"

// -------- the TED toolbar
class TedTools : public ToolBar    {
    ToolButton newtool;
    ToolButton opentool;
    ToolButton savetool;
public:
    TedTools(DFWindow *par);
};
#endif
```

#### LISTING_FIVE

```c
// ---------- tedtools.cpp
#include "ted.h"

// -------- the TED toolbar
TedTools::TedTools(DFWindow *par) : ToolBar(par),
            newtool("New",   (DFWindow *) this),
            opentool("Open", (DFWindow *) this),
            savetool("Save", (DFWindow *) this)
{
    newtool.SetButtonFunction(this->Parent(),
        (Df) (Ap) &Ted::CmNew);
    opentool.SetButtonFunction(this->Parent(),
        (Df) (Ap) &Ted::CmOpen);
    savetool.SetButtonFunction(this->Parent(),
        (Df) (Ap) &Ted::CmSave);
}
```

#### LISTING_SIX

```c
// -------- toolbar.h
#ifndef TOOLBAR_H
#define TOOLBAR_H

#include "pbutton.h"

const COLORS ToolBarBG = BLUE;

// ------ Toolbar class
class ToolBar : public DFWindow {
    void ParentSized(int xdif, int ydif);
public:
    ToolBar(DFWindow *par);
};
// ------- Toolbar button class
class ToolButton : public PushButton    {
    void SetColors();
    void InitWindow(char *lbl);
    DFWindow *oldFocus;
    virtual void ButtonCommand();
    virtual Bool SetFocus();
public:
    ToolButton(char *lbl, int lf, int tp, DFWindow *par=0);
    ToolButton(char *lbl, DFWindow *par=0);
    virtual void Paint();
    virtual void Border();
};
#endif
```

#### LISTING_SEVEN

```c
// ------------ toolbar.cpp
#include "toolbar.h"

// ----- construct a Toolbar
ToolBar::ToolBar(DFWindow *par) : DFWindow(par)
{
    windowtype = ToolbarWindow;
    if (par != 0)    {
        // --- put it into the application window
        Move(par->ClientLeft(), par->ClientTop());
        Size(par->ClientRight(), par->ClientTop()+2);
        par->SetAttribute(TOOLBAR);
    }
    colors.fg = colors.bg = ToolBarBG;
}
// ---- resize the menubar when the application window resizes
void ToolBar::ParentSized(int xdif, int)
{
    Size(Right()+xdif, Bottom());
}
```

#### LISTING_EIGHT

```c
// ---------- toolbutt.cpp
#include "toolbar.h"
#include "desktop.h"

// ------- various color patterns
static Color EnabledColor = {
    LIGHTGRAY,            // fg
    ToolBarBG,            // bg
    WHITE,                // selected fg
    ToolBarBG,            // selected bg
    CYAN,                 // frame fg
    ToolBarBG,            // frame bg
    WHITE,                // highlighted fg
    ToolBarBG             // highlighted bg
};
static Color PressedColor = {
    BLACK,                // fg
    ToolBarBG,            // bg
    ToolBarBG,            // selected fg
    ToolBarBG,            // selected bg
    CYAN,                 // frame fg
    ToolBarBG,            // frame bg
    ToolBarBG,            // highlighted fg
    ToolBarBG             // highlighted bg
};
static Color DisabledColor = {
    BLACK,                // fg
    ToolBarBG,            // bg
    BLACK,                // selected fg
    ToolBarBG,            // selected bg
    BLACK,                // frame fg
    ToolBarBG,            // frame bg
    BLACK,                // highlighted fg
    ToolBarBG             // highlighted bg
};
// ---- pressed and unpressed frame corner characters
const int PRESSED_NE   = 184;
const int PRESSED_SW   = 211;
const int UNPRESSED_NE = 183;
const int UNPRESSED_SW = 212;
// ----- button frame when pressed
static BoxLines PressedBorder = {
    FOCUS_NW,
    FOCUS_LINE,
    PRESSED_NE,
    SIDE,
    SE,
    LINE,
    PRESSED_SW,
    FOCUS_SIDE,
};
// ----- button frame when not pressed
static BoxLines UnPressedBorder = {
    NW,
    LINE,
    UNPRESSED_NE,
    FOCUS_SIDE,
    FOCUS_SE,
    FOCUS_LINE,
    UNPRESSED_SW,
    SIDE
};
// --- common constructor code
void ToolButton::InitWindow(char *lbl)
{
    oldFocus = 0;
    windowtype = ToolButtonWindow;
    Size(Left()+5, Top()+2);
    ClearAttribute(SHADOW);
    SetAttribute(BORDER);
    SetText(lbl);
    SetColors();
}
// --------- construct a Toolbar button specifying position
ToolButton::ToolButton(char *lbl, int lf, int tp, DFWindow *par)
                : PushButton(lbl, lf, tp, par)
{
    InitWindow(lbl);
}
// ------ construct a Toolbar button, self-positioning
ToolButton::ToolButton(char *lbl, DFWindow *par)
                : PushButton(lbl, 0, 0, par)
{
    InitWindow(lbl);
    if (par != 0 && par->WindowType() == ToolbarWindow)    {
        int btcount = 0;
        DFWindow *Wnd = par->First();
        while (Wnd != 0 && Wnd != this)    {
            if (Wnd->WindowType() == ToolButtonWindow)
                btcount++;
            Wnd = Wnd->Next();
        }
        Move(par->Left()+btcount*6, par->Top());
    }
}
// ----- draw the button's frame
void ToolButton::Border()
{
    if (visible)    {
        if (pressed)    {
            colors = PressedColor;
            DrawBorder(PressedBorder);
        }
        else    {
            SetColors();
            DrawBorder(UnPressedBorder);
        }
    }
}
// -------- set the button's colors
void ToolButton::SetColors()
{
    if (isEnabled())
        colors = EnabledColor;
    else
        colors = DisabledColor;
}
// ------- paint the button
void ToolButton::Paint()
{
    SetColors();
    TextBox::Paint();
}
// -------- the button was pressed
void ToolButton::ButtonCommand()
{
    PushButton::ButtonCommand();
    if (oldFocus != 0)
        oldFocus->SetFocus();
    oldFocus = 0;
}
// ---- remember who had the focus before the button got it
Bool ToolButton::SetFocus()
{
    if (oldFocus == 0)
        oldFocus = desktop.InFocus();
    return PushButton::SetFocus();
}
```
