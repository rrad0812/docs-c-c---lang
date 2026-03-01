
# C PROGRAMMING 9304

## Of Keyboards and Menus - Al Stevens

### D-Flat++ meniji

Ovaj mesec popunjava okruženje aplikacije D-Flat++ sistemom menija. CUA arhitektura menija koristi traku menija na vrhu ekrana sa oznakama za predstavljanje svakog od nekoliko iskačućih menija. DF++ koristi klase da definiše traku menija i iskačuće menije. Aplikacioni program nema blisku interakciju sa ovim klasama. Prozor aplikacije brine o tome. Aplikacioni program mora, međutim, da definiše karakteristike sistema menija u nizu tabela.

Setićete se iz koda u februarskoj koloni „C programiranje“ da klasa Application uključuje pokazivač na objekat MenuBar. Aplikacioni program gradite tako što ćete iz generičkog prozora aplikacije izvesti prilagođeni prozor. Kada deklarišete prilagođeni objekat aplikacije, kao parametar uključujete pokazivač na niz MenuBarItem objekata. Niz definiše stavke trake menija, koje definišu iskačuće menije.

Sistem menija D-Flat++ je hijerarhija objekata - ne hijerarhija nasleđivanja klasa, već hijerarhijska organizacija povezanih klasa. Na vrhu je prozor aplikacije, koji ukazuje na objekat MenuBar. MenuBar objekat ukazuje na listu MenuBarItem objekata. Svaki MenuBarItem objekat gradi PopDovn objekat. Svaki PopDovn objekat sadrži listu MenuSelection objekata. Svaki MenuSelection objekat predstavlja akciju, koja je obično poziv funkcije, prekidač menija ili kaskadni iskačući meni.

### Primer aplikacijskog programa

[LISTING_ONE](#listing_one) je test.cpp, veoma mala D-Flat++ aplikacija koja pokazuje kako aplikacijski program definiše meni i povezuje ga sa prozorom aplikacije. Program pravi prozor aplikacije sa jednim iskačućim menijem File. Prve dve komande, Novo i Otvori, ne rade ništa. Komanda Ekit prekida program.

Ovaj program od 45 redova je samostalan. U potpunosti opisuje svoje menije i okruženje aplikacija. Klasa miAppl definiše aplikaciju koja ima meni, naslov i tri funkcije koje se izvršavaju komandama menija.

Posmatrajte način na koji program definiše menije. Deklariše tri MenuSelection objekta. Ovo su izbori koje se daju korisnicima. Oni će biti raspoređeni među iskačućim menijima. Njihova blizina jedna drugoj ovde je slučajna. Možete promeniti njihovu distribuciju na menijima bez dodirivanja ovih deklaracija.

Sledeće dolazi niz pokazivača na objekte MenuSelection. Svaki takav niz definiše iskačući meni, u ovom slučaju jedini u programu. Adresa objekta SelectionSeparator navodi da DF++ treba da prikaže horizontalnu liniju razdvajanja između selekcija. Niz je završen NULL.

Niz MenuBarItem objekata definiše traku menija. Svaki unos u nizu navodi oznaku trake menija i pokazivač na niz MenuSelection koji definiše pridruženi iskačući meni. Niz se završava unosom konstruisanim sa NULL argumentom pokazivača.

Možete videti da definicija menija koristi dve različite tehnike za definisanje trake menija i iskačućih menija. Niz MenuBarItems je niz objekata. Nizovi stavki MenuSelection su nizovi pokazivača na eksterno deklarisane objekte MenuSelection. Deklarisanjem tih objekata izvan stvarnih definicija menija, svakoj komandi dajete sopstveni identitet i možete joj slati poruke, kao što je da joj kažete da se isključi, da prijavi njeno trenutno stanje prebacivanja i tako dalje. Ako su objekti sami raspoređeni u menije, njihovi identiteti bi bili preko indeksa u određenim menijima i ne bi bilo tako lako pomerati se kada modifikujete dizajn.

### Klasa MenuBar

Konstruktor za klasu prozora aplikacije koristi pokazivač na niz MenuBarItem da bi napravio instancu klase MenuBar definisanu u menubar.h [LISTING_TWO](#listing_two). Ovaj listing takođe definiše klasu MenuBarItem. Niz MenuBarItem objekata definiše sadržaj MenuBar objekta. [LISTING_THREE](#listing_three) je menubar.cpp, koji sadrži funkcije člana za MenuBar i MenuBarItem klase.

Kada se prikaže prozor aplikacije, prikazuje se i traka menija. Kada se promeni veličina prozora aplikacije, prozor MenuBar prima poruku roditeljske veličine i prilagođava sopstvenu veličinu tako da odgovara novoj veličini prozora aplikacije.

### Tasteri na traci menija

Kada bilo koji prozor dokumenta u aplikaciji ne presretne i ne obradi pritisak na taster, osnovna klasa DFVindov prosleđuje pritisak na roditelj prozora koji je odbio ključ. Glavni predak svih prozora je vladajući prozor aplikacije, tako da na kraju dobija sve nepresretnute pritiske na tastere. Kada prozor aplikacije primi pritisak na taster koji ne želi da obradi, on prosleđuje pritisak na MenuBar objekat. Objekat MenuBar, dakle, dobija sve pritiske na tastere koje nijedan drugi prozor ne želi.

Funkcija tastature klase MenuBar prvo gleda da vidi da li je pritisak na neki od tastera za ubrzavanje dodeljenih komandama menija u okviru objekata PopDovn. Taster za ubrzavanje je pritisak na taster koji korisnik može da upotrebi da izvrši komandu menija iako meni nije iskočio. Kasnije ćete videti kako se grade PopDovn objekti. Zatim, MenuBar objekat testira da li je pritisak na taster jedna od prečica na traci menija. Taster za prečicu je istaknuto slovo ili broj u oznaci izbora menija. Korisnik može da pritisne Alt plus označeni znak da bi izabrao prečicu na traci menija. (Alt nije potreban da bi se izabrala prečica u iskačućem meniju.) Izborom prečice u meniju na ovaj način birate i iskačete povezani meni.

Poruka LeftButton klase MenuBar takođe može izabrati iskačući meni. Ako se koordinate miša nalaze u okviru prikaza oznake menija, objekat MenuBar će iskočiti u meniju.

### Klasa MenuBarItem

Svaki MenuBarItem objekat predstavlja jedan iskačući meni i uključuje adresu niza MenuSelection objekata koji definišu karakteristike svakog izbora u meniju. Većina selekcija će se sastojati od oznake i adrese funkcije koja će se izvršiti kada korisnik odabere izbor. Te funkcije su funkcije članice prilagođene klase aplikacije koju izvodite iz klase aplikacije D-Flat++.

### Klasa MenuSelection

[LISTING_FOUR](#listing_four) je menusel.h, datoteka zaglavlja koja definiše klasu MenuSelection, a [LISTING_FIVE](#listing_five) je menusel.cpp. Definicije menija za aplikaciju se sastoje od nizova pokazivača na objekte MenuSelection, kao što je prikazano na [LISTING_ONE](#listing_one). Možete konstruisati MenuSelection objekat sa oznakom i funkcijom, ili sa različitim kombinacijama oznake, funkcije, tastera za ubrzavanje i uslova aktivnog/neaktivnog stanja. Objekti MenuSelection takođe mogu biti tokeni za razdvajanje za prikaz linije između normalnih izbora u meniju. Jedan konstruktor vam omogućava da navedete preklopnu vrednost Uključeno ili Isključeno, što identifikuje izbor kao onaj koji se može prebaciti. Izbor iz menija koji se prebacuje održava uključeno/isključeno stanje u zavisnosti od poslednjeg izbora korisnika. Prikazuje kvačicu pored svoje oznake kada je u stanju Uključeno. Aplikacioni program može da upita trenutno stanje uključenog/isključenog izbora menija čak i kada padajući meni nije aktivan.

Jedan konstruktor za klasu MenuSelection vam omogućava da navedete adresu drugog niza MenuSelection objekata. Ovaj format identifikuje kaskadni izbor menija. Kada korisnik odabere stavku, DF++ iskače kaskadni meni kao što je predstavljeno drugim nizom. Kaskadni meniji mogu sami da sadrže kaskadne menije.

### Pravljenje trake menija

MenuBar konstruktor iterira kroz listu MenuBarItem objekata i pravi traku menija tako što pravi niz oznaka menija. Ovaj niz, koji je prikaz na traci menija, ima tekstualnu oznaku za svaki iskačući meni. Konstruktor takođe instancira svaki od iskačućih menija tako što pravi PopDovn objekte, prosleđujući adresu niza MenuSelection objekata povezanih sa svakim MenuBarItem objektom. Iskačući prozori se trenutno ne prikazuju – jednostavno su napravljeni. Objekat MenuBar će prikazati svaki od iskačućih prozora samo kada ih korisnik izabere.

### PopDovn klasa

[LISTING_SIX](#listing_six) i [LISTING_SEVEN](#listing_seven) su popdovn.h i popdovn.cpp, respektivno. Oni definišu klasu PopDovn, koja je izvedena iz klase ListBok koju sam opisao prošlog meseca. Međutim, PopDovn objekat ima neko jedinstveno ponašanje. Svaki iskačući prozor prilagođava svoju veličinu i poziciju tako da odgovara njegovom sadržaju i poziciji svog roditelja. Nekaskadni iskačući meni se nalazi odmah ispod oznake trake menija koja ga bira. Ako ta pozicija stavi meni van ekrana, meni će se sam podesiti da ostane na ekranu. Kaskadni meni se postavlja pored izbora menija koji je izabrao kaskadu, takođe se prilagođava da ostane na ekranu.

Granice iskačućeg menija su konstantne. Oni se ne menjaju da bi odražavali stanje u fokusu kao što to čine drugi okviri sa listom. Poruka Paint prepoznaje preklopne izbore i prikazuje ili briše kvačicu u zavisnosti od stanja prekidača.

Poruka sa tastature takođe pokazuje jedinstveno ponašanje. Tekst iskačućeg menija nikada ne prelazi visinu prozora, tako da se pomeranje nikada ne dešava. Kursor za izbor se premotava odozdo prema gore i obrnuto. Tasteri sa strelicom napred i nazad zatvaraju trenutni meni i dovode do otvaranja sledećeg desnog ili levog padajućeg menija.

Izbor stavke menija se dešava kada korisnik izabere stavku i pritisne taster Enter, odgovarajući taster za ubrzavanje ili odgovarajuću prečicu dok je meni otvoren. Selekcija se takođe dešava ako korisnik pritisne i pusti levi taster miša na izboru menija. Otpuštanje dugmeta vrši izbor.

Izbor menija – implementiran metodom Choose – čini jednu od sledećih stvari: Ako meni nije omogućen, izbor oglašava zvučni alarm; prebacuje se na uključeno/isključeno stanje izbora ako je izbor prekidač; otvara kaskadni iskačući meni ako je izbor kaskadni izbor; i poziva funkciju člana prozora aplikacije ako je ona dodeljena izboru. Ako je funkcija dodeljena, program zatvara iskačući meni i sve više kaskadne menije. Funkcije aplikacije za izbor menija se izvršavaju kada su meniji svi zatvoreni. Izbor za prebacivanje može uključivati i komandnu funkciju.

### Kontrolni meni

Poslednji deo sistema menija je kontrolni meni, implementiran u [LISTING_EIGHT](#listing_eight) je ctlmenu.cpp. CUA kontrolni meni, koji se ponekad naziva i „sistemski meni“, sadrži jednu ili više fiksnih generičkih komandi prozora. Ove komande su: Vrati, Premesti, Veličina, Umanji, Maksimiziraj i Zatvori. Korisnik može koristiti ovaj meni za obavljanje ovih operacija. Korisnik otvara prozor klikom na kontrolni okvir u gornjem levom uglu prozora ili pritiskom na Alt+razmaknica za prozor aplikacije ili okvir za dijalog i Alt+crticu za prozor dokumenta. Metod Keiboard klase DFVindov presreće ove pritiske na tastere i poziva funkciju OpenCtlMenu, koja prilagođava kontrolni meni prema atributima prozora. Na primer, ako se prozor može minimizirati ili maksimizirati, kontrolni meni će imati komandu Restore. Ako je prozor trenutno u nekom od ovih uslova, komanda Restore će biti omogućena. Ostale komande su slično prilagođene.

#### LISTING_ONE

```c
// ------------- test.cpp
#include "dflatpp.h"

extern MenuBarItem aMenu[];

// ------- application definition
class myAppl : public Application {
public:
    myAppl() : Application("Hello",aMenu) {}
    // ----- menu command functions
    void DoNew()  {}
    void DoOpen() {}
    void DoExit() { CloseWindow(); }
};
// --------- MenuSelection objects
MenuSelection
    NewCmd  ("~New",  (void (DFWindow::*)()) &myAppl::DoNew ),
    OpenCmd ("~Open", (void (DFWindow::*)()) &myAppl::DoOpen ),
    ExitCmd ("E~xit   Alt+F4", (void (DFWindow::*)()) &myAppl::DoExit, ALT_F4);

// --------- File menu definition
MenuSelection *File[] = {
    &NewCmd,
    &OpenCmd,
    &SelectionSeparator,
    &ExitCmd,
    NULL
};
// --------- menu bar definition
MenuBarItem aMenu[] = {
    MenuBarItem( "~File",    File    ),
    MenuBarItem( NULL )
};
void main()
{
    myAppl *aWnd = new myAppl;
    while (desktop.DispatchEvents())
        ;
    delete aWnd;
}
```

#### LISTING_TWO

```c
// -------- menubar.h
#ifndef MENUBAR_H
#define MENUBAR_H

#include "textbox.h"
#include "popdown.h"

class MenuBarItem    {
public:
    String *title;       // menu bar selection label
    int x1;              // 1st label position on bar
    int x2;              // last  "      "     "   "
    MenuSelection **ms;  // popdown selection list
    PopDown *popdown;    // popdown window
    void (*menuprep)();  // menu prep function
    MenuBarItem(char *Title, MenuSelection **Ms = NULL,
                             void (*MenuPrep)() = NULL);
    ~MenuBarItem() { if (title) delete title; }
};
class MenuBar : public TextBox    {
    MenuBarItem *menuitems; // list of popdowns
    int menucount;          // count of popdowns
    int selection;          // current selection on the bar
    Bool ispoppeddown;      // True = a menu is down
    DFWindow *oldfocus;     // previous focus
    void SetColors();
    void Select();
    Bool AcceleratorKey(int key);
    Bool ShortCutKey(int key);
public:
    MenuBar(MenuBarItem *MenuItems, DFWindow *par);
    ~MenuBar();
    // -------- menubar API messages
    void Keyboard(int key);
    void LeftButton(int mx, int my);
    Bool SetFocus();
    void ResetFocus();
    void Paint();
    void Select(int sel);
    void SetSelection(int sel);
    void ParentSized(int xdif, int ydif);
};
#endif
```

#### LISTING_THREE

```c
// ------------- menubar.cpp
#include <ctype.h>
#include "desktop.h"
#include "menubar.h"
#include "menusel.h"

// -------- construct a menubar item
MenuBarItem::MenuBarItem(char *Title, MenuSelection **Ms,void (*MenuPrep)())
{
    if (Title != NULL)
        title = new String(Title);
    else
        title = NULL;
    ms = Ms;
    menuprep = MenuPrep;
    popdown = NULL;
}
// -------- construct a menubar
MenuBar::MenuBar( MenuBarItem *MenuItems, DFWindow *par) :
            TextBox( par->ClientLeft(), par->ClientTop()-1,
                        1, par->ClientWidth(), par)
{
    windowtype = MenubarWindow;
    menuitems = MenuItems;
    SetAttribute(NOCLIP);
    SetColors();
    selection = -1;
    ispoppeddown = False;
    MenuBarItem *menu = menuitems;
    menucount = 0;
    oldfocus = NULL;
    int off = 2;
    SetTextLength(desktop.screen().Width()*2);
    while (menu->title != NULL)    {
        int len = menu->title->Strlen()-1;
        menu->x1 = off;
        menu->x2 = off+len-1;
        off += len+2;
        String ttl("  ");
        ttl += *(menu->title);
        AddText(ttl);
        int n = text->Strlen()-1;
        (*text)[n] = '\0';
        menu->popdown = new PopDown(this, menu->ms);
        menu++;
        menucount++;
    }
}
// -------- menubar destructor
MenuBar::~MenuBar()
{
    MenuBarItem *menu = menuitems;
    while (menu->title != NULL)    {
        delete menu->popdown;
        menu++;
    }
    TextBox::CloseWindow();
}
// -------- set the fg/bg colors for the window
void MenuBar::SetColors()
{
    colors.fg = BLACK;
    colors.bg = LIGHTGRAY;
    colors.sfg = BLACK;
    colors.sbg = CYAN;
    colors.ffg = BLACK;
    colors.fbg = LIGHTGRAY;
    colors.hfg = BLACK;
    colors.hbg = LIGHTGRAY;
    shortcutfg = RED;
}
// ---- menubar gets the focus
Bool MenuBar::SetFocus()
{
    if (oldfocus == NULL)
        if (desktop.InFocus() != NULL)
            if (desktop.InFocus()->State() != ISCLOSING)
                oldfocus = desktop.InFocus();
    return TextBox::SetFocus();
}
// ---- menubar loses the focus
void MenuBar::ResetFocus()
{
    if (!ispoppeddown)    {
        SetSelection(-1);
        oldfocus = NULL;
    }
    TextBox::ResetFocus();
}
// -------- paint the menubar
void MenuBar::Paint()
{
    WriteShortcutLine(0, colors.fg, colors.bg);
    if (selection != -1)    {
        int x = menuitems[selection].x1;
        int len = menuitems[selection].x2-x+2;
        String sel = text->mid(len, x+selection);
        DisplayShortcutField(sel,x,0,colors.sfg,colors.sbg);
    }
}
// ------- left mouse button is pressed
void MenuBar::LeftButton(int mx, int)
{
    mx -= Left();
    MenuBarItem *menu = menuitems;
    int sel = 0;
    while (menu->title != NULL)    {
        if (mx >= menu->x1 && mx <= menu->x2)    {
            if (selection != sel || !ispoppeddown)    {
                if (ispoppeddown)    {
                    PopDown *pd = menuitems[selection].popdown;
                    if (pd->isOpen())
                        pd->CloseMenu(False);
                }
                Select(sel);
            }
            return;
        }
        sel++;
        menu++;
    }
    if (selection == -1)
        SetSelection(0);
}
void MenuBar::SetSelection(int sel)
{
    selection = sel;
    Paint();
}
// ----- programmed selection
void MenuBar::Select(int sel)    {
    selection = sel;
    Select();
}
// ------- user selection
void MenuBar::Select()
{
    Paint();
    ispoppeddown = True;
    MenuBarItem &mb = *(menuitems+selection);
    int lf = Left() + mb.x1;
    int tp = Top()+1;
    if (mb.menuprep != NULL)
        (*mb.menuprep)();
    mb.popdown->OpenMenu(lf, tp);
}
// ------ test for popdown accelerator key
Bool MenuBar::AcceleratorKey(int key)
{
    MenuBarItem *menu = menuitems;
    while (menu->title != NULL)    {
        PopDown *pd = menu->popdown;
        if (pd->AcceleratorKey(key))
            return True;
        menu++;
    }
    return False;
}
// ------ test for menubar shortcut key
Bool MenuBar::ShortCutKey(int key)
{
    int altkey = desktop.keyboard().AltConvert(key);
    MenuBarItem *menu = menuitems;
    int sel = 0;
    while (menu->title != NULL)    {
        int off = menu->title->FindChar(SHORTCUTCHAR);
        if (off != -1)    {
            String &cp = *(menu->title);
            int c = cp[off+1];
            if (tolower(c) == altkey)    {
                SetFocus();
                Select(sel);
                return True;
            }
        }
        sel++;
        menu++;
    }
    return False;
}
// -------- keystroke while menubar has the focus
void MenuBar::Keyboard(int key)
{
    if (AcceleratorKey(key))
        return;
    if (!ispoppeddown && ShortCutKey(key))
        return;
    switch (key)    {
        case F10:
            if (ispoppeddown)
                break;
            if (this != desktop.InFocus())    {
                if (selection == -1)
                    selection = 0;
                SetFocus();
                break;
            }
            // ------ fall through
        case ESC:
            ispoppeddown = False;
            SetSelection(-1);
            if (oldfocus != NULL)
                oldfocus->SetFocus();
            else
                parent->SetFocus();
            break;
        case FWD:
            selection++;
            if (selection == menucount)
                selection = 0;
            if (ispoppeddown)
                Select();
            else
                Paint();
            break;
        case BS:
            if (selection == 0)
                selection = menucount;
            --selection;
            if (ispoppeddown)
                Select();
            else
                Paint();
            break;
        case '\r':
            if (selection != -1)
                Select();
            break;
        case ALT_F6:
            TextBox::Keyboard(key);
            break;
        default:
            break;
    }
}
// ---- resize the menubar when the application window resizes
void MenuBar::ParentSized(int xdif, int)
{
    Size(Right()+xdif, Bottom());
}
```

#### LISTING_FOUR

```c
// ------- menusel.h
#ifndef MENUSEL_H
#define MENUSEL_H

#include <stdio.h>
#include "strings.h"
#include "dfwindow.h"

enum MenuType {
    NORMAL,
    TOGGLE,
    CASCADER,
    SEPARATOR
};
enum Toggle { Off, On };

class DFWindow;
class PopDown;

#define NULLFUNC (void (DFWindow::*)())NULL
class MenuSelection    {
    String *label;          // selection label
    void (DFWindow::*cmdfunction)(); // selection function
    MenuType type;          // NORMAL, TOGGLE,
                            // CASCADER, SEPARATOR
    Bool isenabled;         // True = enabled, False = disabled
    int accelerator;        // accelerator key
    PopDown *cascade;       // cascaded menu window
    MenuSelection **cascaders; // cascaded menu selection list
    Toggle toggle;          // On or Off
    void NullSelection();   // Build a null selection
    friend PopDown;
    void CommonConstructor(    char *Label,
                            int Accelerator,
                            void (DFWindow::*CmdFunction)(),
                            Bool Active,
                            MenuType Type,
                            Toggle Tgl,
                            MenuSelection **Cascaders = NULL);
public:
    MenuSelection(    char *Label,
                    void (DFWindow::*CmdFunction)() = 0,
                    int Accelerator=0,
                    Bool Active=True );
    MenuSelection(    char *Label,
                    void (DFWindow::*CmdFunction)(),
                    Toggle Tgl,
                    int Accelerator=0,
                    Bool Active=True );
    MenuSelection(    char *Label,
                    MenuSelection **Cascaders,
                    int Accelerator=0,
                    Bool Active=True );
    MenuSelection(MenuType Type);

    Bool isEnabled()    { return isenabled; }
    void Enable()       { isenabled = True; }
    void Disable()      { isenabled = False; }
    void SetToggle()    { toggle = On; }
    void ClearToggle()  { toggle = Off; }
    void InvertToggle() { toggle = toggle == On ? Off : On; }
    Bool isToggled()    { return (Bool) (toggle == On); };
    Type()              { return type; }
};
extern MenuSelection SelectionSeparator;
extern MenuSelection SelectionTerminator;

#endif
```

#### LISTING_FIVE

```c
// ------- menusel.cpp
#include <string.h>
#include "menusel.h"

MenuSelection SelectionSeparator(SEPARATOR);
void MenuSelection::NullSelection()
{
    label = NULL;
    cmdfunction = NULL;
    type = NORMAL;
    cascade = NULL;
    isenabled = True;
    accelerator = 0;
    cascade = NULL;
    toggle = Off;
}
void MenuSelection::CommonConstructor(
                               char *Label,
                               int Accelerator,
                               void (DFWindow::*CmdFunction)(),
                               Bool Active,
                               MenuType Type,
                               Toggle Tgl,
                               MenuSelection **Cascaders)
{
    NullSelection();
    if (Label != NULL)
        label = new String(Label);
    accelerator = Accelerator;
    cmdfunction = CmdFunction;
    isenabled = Active;
    type = Type;
    toggle = Tgl;
    cascaders = Cascaders;
}
MenuSelection::MenuSelection(  char *Label,
                               void (DFWindow::*CmdFunction)(),
                               int Accelerator, Bool Active )
{
    CommonConstructor(Label, Accelerator, CmdFunction, Active, NORMAL, Off);
}
MenuSelection::MenuSelection(  char *Label,
                               void (DFWindow::*CmdFunction)(),
                               Toggle Tgl, int Accelerator, Bool Active)
{
    CommonConstructor(Label, Accelerator, CmdFunction, Active, TOGGLE, Tgl);
}

MenuSelection::MenuSelection(char *Label,
                            MenuSelection **Cascaders,
                            int Accelerator, Bool Active )
{
    CommonConstructor(Label, Accelerator, NULL,
                             Active, CASCADER, Off, Cascaders);
}
MenuSelection::MenuSelection(MenuType Type)
{
    NullSelection();
    type = Type;
}
```

#### LISTING_SIX

```c
// -------- popdown.h
#ifndef POPDOWN_H
#define POPDOWN_H

#include "desktop.h"
#include "listbox.h"

const unsigned char LEDGE          = '\xc3';
const unsigned char REDGE          = '\xb4';
const unsigned char CASCADEPOINTER = '\x10';

inline unsigned char CheckMark()
{
    return desktop.screen().Height() == 25 ? 251 : 4;
}
class MenuSelection;
class MenuBar;

class PopDown : public ListBox    {
    MenuSelection **selections; // array of selections
    Bool isopen;                // True = menu is open
    Bool iscascaded;            // True = menu is cascaded
    int menuwidth;              // width of menu
    int menuheight;             // height of menu

    void BuildMenuLine(int sel);
    void MenuDimensions();
    void SetColors();
    void DisplayMenuLine(int lno);
    Bool ShortCutKey(int key);
protected:
    void ClearSelection();
public:
    PopDown(DFWindow *par, MenuSelection **Selections = NULL)
                        : ListBox(5, 5, par)
            { selections = Selections; OpenWindow(); }
    virtual ~PopDown()
        { if (windowstate != CLOSED) CloseWindow(); }
    // -------- listbox API messages
    void OpenWindow();
    void CloseWindow();
    void OpenMenu(int left, int top);
    void CloseMenu(Bool SendESC = False);
    void Show();
    void Paint();
    void Border();
    void Keyboard(int key);
    void ShiftChanged(int sk);
    void ButtonReleased(int mx, int my);
    void LeftButton(int mx, int my);
    void DoubleClick(int mx, int my);
    void Choose();
    void SetSelection(int sel);
    Bool isOpen() { return isopen; }
    Bool &isCascaded() { return iscascaded; }
    Bool AcceleratorKey(int key);
    Bool ParentisMenu(DFWindow &wnd);
    Bool ParentisMenu() { return ParentisMenu(*this); }
};
#endif
```

#### LISTING_SEVEN

```c
// ------------- popdown.cpp
#include <ctype.h>
#include "desktop.h"
#include "popdown.h"
#include "menusel.h"

// --------- create a popdown menu
void PopDown::OpenWindow()
{
    windowtype = PopdownWindow;
    if (windowstate == CLOSED)
        ListBox::OpenWindow();
    SetAttribute(BORDER | SHADOW | SAVESELF | NOCLIP);
    selection = 0;
    DblBorder = False;
    isopen = False;
    SetColors();
    iscascaded = False;
    if (selections != NULL)    {
        MenuDimensions();
        SetTextLength(menuwidth * menuheight);
        for (int i = 0; i < menuheight; i++)    {
            MenuSelection &ms = **(selections+i);
            BuildMenuLine(i);
            if (ms.type == CASCADER)    {
                ms.cascade = new PopDown(this, ms.cascaders);
                ms.cascade->isCascaded() = True;
            }
        }
        rect.Right() = rect.Left() + menuwidth;
        rect.Bottom() = rect.Top() + menuheight + 1;
    }
}
// ---- shut down a popdown menu
void PopDown::CloseWindow()
{
    if (selections != NULL)    {
        // --- delete all cascader popdowns
        for (int i = 0; selections[i]; i++)    {
            MenuSelection &ms = *selections[i];
            if (ms.type == CASCADER && ms.cascade != NULL)
                delete ms.cascade;
        }
    }
    ListBox::CloseWindow();
}

// ------- pop down the menu
void PopDown::OpenMenu(int left, int top)
{
    Rect rc(0, 0, desktop.screen().Width()-1,
        desktop.screen().Height()-1);
    DFWindow *Wnd = parent;
    while (Wnd != NULL && Wnd->WindowType() == PopdownWindow)
        Wnd = Wnd->Parent();
    if (Wnd != NULL && (Wnd = Wnd->Parent()) != NULL)    {
        Rect rc = Wnd->ClientRect();
        left = min(max(left, rc.Left()), rc.Right() - ClientWidth());
        top = min(max(top, rc.Top()), rc.Bottom() - ClientHeight());
    }
    left = min(max(left, rc.Left()), rc.Right()-ClientWidth()-1);
    top = min(max(top, rc.Top()), rc.Bottom()-ClientHeight()-1);
    isopen = True;
    Move(left, top);
    CaptureFocus();
    Paint();        // in case a command attribute changed
}
// ---------- deactivate the popdown menu
void PopDown::CloseMenu(Bool SendESC)
{
    if (isopen)    {
        // ------- close any open cascaded menus
        PopDown *Wnd = (PopDown *)first;
        while (Wnd != NULL)    {
            Wnd->CloseMenu();
            Wnd = (PopDown *) (Wnd->next);
        }
        Hide();
        isopen = False;
        ReleaseFocus();
        if (parent && !iscascaded && SendESC)
            parent->Keyboard(ESC);
    }
}
void PopDown::Show()
{
    if (isopen)
        ListBox::Show();
}
// -------- build a menu line
void PopDown::BuildMenuLine(int sel)
{
    int wd = menuwidth;
    String ln;
    if (selections[sel]->type == SEPARATOR)
        ln = String(--wd, LINE);
    else    {
        ln = String(" ");
        ln += *(selections[sel]->label);
        int r = wd-ln.Strlen();
        ln += String(r, ' ');
        if (selections[sel]->type == CASCADER)
            ln[wd-1] = CASCADEPOINTER;
    }
    AddText(ln);
}
// -------- compute menu width
void PopDown::MenuDimensions()
{
    int txlen = 0;
    for (int i = 0; selections[i] != NULL; i++)  {
        if (selections[i]->type != SEPARATOR)    {
            int lblen = (selections[i]->label)->Strlen()-1;
            txlen = max(txlen, lblen);
        }
    }
    menuwidth = txlen+4;
    menuheight = i;
}
// -------- set the fg/bg colors for the window
void PopDown::SetColors()
{
    colors.fg = BLACK;
    colors.bg = CYAN;
    colors.sfg = BLACK;
    colors.sbg = LIGHTGRAY;
    colors.ffg = BLACK;
    colors.fbg = CYAN;
    colors.hfg = DARKGRAY;    // Inactive FG
    colors.hbg = CYAN;        // Inactive FG
    shortcutfg = RED;
}
// ------ display a menu line
void PopDown::DisplayMenuLine(int lno)
{
    if (isopen)    {
        int fg, bg;
        int isActive = selections[lno]->isEnabled();
        int sfg = shortcutfg;
        if (lno == selection)    {
            fg = colors.sfg;
            bg = colors.sbg;
        }
        else if (isActive)    {
            fg = colors.fg;
            bg = colors.bg;
        }
        else     {
            fg = colors.hfg;
            bg = colors.hbg;
        }
        if (!isActive)
            shortcutfg = fg;
        WriteShortcutLine(lno, fg, bg);
        shortcutfg = sfg;
    }
}
// ------ set no selection current
void PopDown::ClearSelection()
{
    if (selection != -1)    {
        int sel = selection;
        selection = -1;
        DisplayMenuLine(sel);
    }
}
// ------ set a current menu selection
void PopDown::SetSelection(int sel)
{
    ClearSelection();
    if (sel >= 0 && sel < wlines)    {
        selection = sel;
        DisplayMenuLine(sel);
    }
}
// ---------- paint the menu
void PopDown::Paint()
{
    if (text == NULL)
        ListBox::Paint();
    else    {
        for (int i = 0; i < wlines; i++)    {
            if (selections[i]->type == TOGGLE)    {
                char *cp = TextLine(i);
                if (selections[i]->toggle == On)
                    *cp = CheckMark();
                else
                    *cp = ' ';
            }
            DisplayMenuLine(i);
        }
    }
}
// --------- paint the menu's border
void PopDown::Border()
{
    if (isopen && isVisible())    {
        int fg = colors.ffg;
        int bg = colors.fbg;
        int rt = Width()-1;
        ListBox::Border();
        for (int i = 0; i < wlines; i++)    {
            if (selections[i]->type == SEPARATOR)    {
                WriteWindowChar(LEDGE, 0, i+1, fg, bg);
                WriteWindowChar(REDGE, rt, i+1, fg, bg);
            }
        }
    }
}
// ------- test for a menu selection accelerator key
Bool PopDown::AcceleratorKey(int key)
{
    for (int i = 0; i < wlines; i++)    {
        MenuSelection &ms = **(selections+i);
        if (key == ms.accelerator)    {
            SetSelection(i);
            Choose();
            return True;
        }
    }
    return False;
}
// ------- test for a menu selection shortcut key
Bool PopDown::ShortCutKey(int key)
{
    key = tolower(key);
    for (int i = 0; i < wlines; i++)    {
        MenuSelection &ms = **(selections+i);
        int off = ms.label->FindChar(SHORTCUTCHAR);
        if (off != -1)    {
            String &cp = *ms.label;
            int c = cp[off+1];
            if (key == tolower(c))    {
                SetSelection(i);
                Choose();
                return True;
            }
        }
    }
    return False;
}
// ----- keystroke while menu is popped down
void PopDown::Keyboard(int key)
{
    if (AcceleratorKey(key))
        return;
    if (ShortCutKey(key))
        return;
    switch (key)    {
        case UP:
            if (selection == 0)    {
                SetSelection(wlines-1);
                return;
            }
            if (selections[selection-1]->type == SEPARATOR)  {
                SetSelection(selection-2);
                return;
            }
            break;
        case DN:
            if (selection == wlines-1)    {
                SetSelection(0);
                return;
            }
            if (selections[selection+1]->type == SEPARATOR)  {
                SetSelection(selection+2);
                return;
            }
            break;
        case ESC:
            CloseMenu(ParentisMenu());
            return;
        case FWD:
        case BS:
            CloseMenu();
            if (parent != NULL)    {
                parent->Keyboard(key);
                return;
            }
            break;
        default:
            break;
    }
    ListBox::Keyboard(key);
}
// ----- shift key status changed
void PopDown::ShiftChanged(int sk)
{
    if (sk & ALTKEY)
        CloseMenu(ParentisMenu());
}
// ---------- Left mouse button was clicked
void PopDown::LeftButton(int mx, int my)
{
    if (ClientRect().Inside(mx, my))    {
        if (my != prevmouseline)    {
            int y = my - ClientTop();
            if (selections[y]->type != SEPARATOR)
                SetSelection(y);
        }
    }
    else if (!rect.Inside(mx, my))    {
        if (parent && my == parent->Bottom())
            parent->LeftButton(mx, my);
    }
    prevmouseline = my;
    prevmousecol = mx;
}
// ---------- Left mouse button was double-clicked
void PopDown::DoubleClick(int mx, int my)
{
    if (!rect.Inside(mx, my))    {
        CloseMenu();
        if (parent)
            parent->DoubleClick(mx, my);
    }
}
// ---------- Left mouse button was released
void PopDown::ButtonReleased(int mx, int my)
{
    if (ClientRect().Inside(mx, my))    {
        if (prevmouseline == my && prevmousecol == mx)
            if (selections[my-ClientTop()]->type != SEPARATOR)
                Choose();
    }
    else if (!rect.Inside(mx, my))    {
        DFWindow *Wnd = desktop.inWindow(mx, my);
        if (!(Wnd == parent && my == Top()-1 &&
                mx >= Left() && mx <= Right()))    {
            CloseMenu(ParentisMenu());
            if (Wnd != NULL && Wnd != desktop.InFocus())
                Wnd->SetFocus();
        }
    }
}
// --------- user chose a menu selection
void PopDown::Choose()
{
    MenuSelection &ms = *selections[selection];
    if (ms.isEnabled())    {
        if (ms.type == CASCADER && ms.cascade != NULL)
            // -------- cascaded menu
            ms.cascade->OpenMenu(Right(), Top()+selection);
        else    {
            if (ms.type == TOGGLE)    {
                // ---- toggle selection
                ms.InvertToggle();
                char *cp = TextLine(selection);
                if (*cp == CheckMark())
                    *cp = ' ';
                else
                    *cp = CheckMark();
                DisplayMenuLine(selection);
            }
            if (ms.cmdfunction != NULL)    {
                // ---- there is a function associated
                DFWindow *wnd = (DFWindow *)this;
                // --- close all menus
                while (wnd &&
                        wnd->WindowType() == PopdownWindow) {
                    ((PopDown *)wnd)->CloseMenu();
                    wnd = wnd->Parent();
                }
                if (wnd && wnd->WindowType() == MenubarWindow){
                    wnd->Keyboard(ESC);
                    wnd = wnd->Parent();
                }
                if (wnd)
                    // ---- execute the function
                    (wnd->*ms.cmdfunction)();
            }
        }
    }
    else
        desktop.speaker().Beep();    // disabled selection
}
inline Bool isMenu(DFWindow *wnd)
{
    if (wnd != NULL)    {
        WndType wt = wnd->WindowType();
        return (Bool) (wt==MenubarWindow || wt==PopdownWindow);
    }
    return False;
}
// ----- test for the parent as menu or menubar
Bool PopDown::ParentisMenu(DFWindow &wnd)
{
    return isMenu(wnd.Parent());
}
```

#### LISTING_EIGHT

```c
// --------------- ctlmenu.cpp
#include "dflatpp.h"
#include "frame.h"

MenuSelection RestoreCmd  ("~Restore",  &DFWindow::Restore);
MenuSelection MoveCmd     ("~Move",     &DFWindow::CtlMenuMove);
MenuSelection SizeCmd     ("~Size",     &DFWindow::CtlMenuSize);
MenuSelection MinimizeCmd ("Mi~nimize", &DFWindow::Minimize);
MenuSelection MaximizeCmd ("Ma~ximize", &DFWindow::Maximize);
MenuSelection CloseDocCmd ("~Close [Ctrl+F4]",&DFWindow::CloseWindow, CTRL_F4);
MenuSelection CloseApCmd  ("~Close [Alt+F4]",&DFWindow::CloseWindow, ALT_F4);

MenuSelection *ControlMenu[8];

MenuBarItem CtlMenu[] = {
    MenuBarItem( "", ControlMenu ),
    MenuBarItem( NULL, NULL )
};
void DFWindow::OpenCtlMenu()
{
    if (ctlmenu != NULL)
        delete ctlmenu;
    int mn = 0;
    if (attrib & (MINBOX | MAXBOX))
        ControlMenu[mn++] = &RestoreCmd;
    if (attrib & MOVEABLE)
        ControlMenu[mn++] = &MoveCmd;
    if (attrib & SIZEABLE)
        ControlMenu[mn++] = &SizeCmd;
    if (attrib & MINBOX)
        ControlMenu[mn++] = &MinimizeCmd;
    if (attrib & MAXBOX)
        ControlMenu[mn++] = &MaximizeCmd;
    if (mn != 0)
        ControlMenu[mn++] = &SelectionSeparator;
    if (Parent())
        ControlMenu[mn++] = &CloseDocCmd;
    else
        ControlMenu[mn++] = &CloseApCmd;
    ControlMenu[mn] = NULL;

    MinimizeCmd.Disable();
    MaximizeCmd.Disable();
    RestoreCmd.Disable();
    SizeCmd.Disable();
    MoveCmd.Disable();

    switch (windowstate)    {
        case ISRESTORED:
            if (attrib & MINBOX)
                MinimizeCmd.Enable();
            if (attrib & MAXBOX)
                MaximizeCmd.Enable();
            MoveCmd.Enable();
            SizeCmd.Enable();
            break;
        case ISMINIMIZED:
            RestoreCmd.Enable();
            MoveCmd.Enable();
            break;
        case ISMAXIMIZED:
            RestoreCmd.Enable();
            if (attrib & MINBOX)
                MinimizeCmd.Enable();
            break;
    }
    ctlmenu = new PopDown(this, ControlMenu);
    ctlmenu->OpenMenu(Left()+1, Top()+1);
}
void DFWindow::DeleteCtlMenu()
{
    if (ctlmenu != NULL)
        delete ctlmenu;
    ctlmenu = NULL;
}
void DFWindow::CtlMenuMove()
{
    desktop.mouse().SetPosition(Left(), Top());
    new Frame(this, Left());
}
void DFWindow::CtlMenuSize()
{
    desktop.mouse().SetPosition(Right(), Bottom());
    new Frame(this);
}
```

Copyright © 1993, Dr. Dobb's Journal
