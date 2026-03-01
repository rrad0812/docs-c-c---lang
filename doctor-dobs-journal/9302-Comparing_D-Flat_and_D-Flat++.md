
# C PROGRAMMING 9302

## Poredjenje D-Flat i D-Flat++ - Al Stevens

Evolucija D-Flat++ nastavlja da otkriva neka zanimljiva iznenađenja. Biblioteka je narasla do mesta gde sada ima dovoljno klasa da napravi jednostavnu aplikaciju sa menijem i nekoliko kontrola. Izvorni kod koji implementira ove funkcije je manji i lakši za čitanje od njegovih D-Flat parnjaka u C. Ovog meseca ćemo pogledati klase prozora Application, Control i TektBok i uporediti izvorni kod za ove module sa D-Flat C izvornim kodom za iste funkcije. Moja kolona iz decembra 1991. govorila je o D-Flat okvirima za tekst, a u koloni iz maja 1992. o prozoru aplikacije. U tim izdanjima naći ćete izvorni kod C za te module.

Klase DF++ Application i TektBok potiču od klase DFVindov, o kojoj smo raspravljali prošlog meseca. Svaka DF++ aplikacija ima objekat prozora aplikacije, a većina kontrola – meniji, okviri sa listom, okviri za uređivanje, dugmad i tako dalje – proizilaze iz klase TektBok, tako da su klase Application i TektBok, u stvari, osnova za DF++ aplikaciju. Kontrolna klasa je osnova za sve kontrolne prozore i obuhvata ponašanje koje kontrolni prozori dele.

### Klasa aplikacije

[LISTING_ONE](#listing_one) je "applicat.h", datoteka zaglavlja koja opisuje klasu prozora aplikacije. Postoji značajna razlika između D-Flat i D-Flat++ u načinu na koji dva sistema opisuju klase prozora, a applicat.h ilustruje razliku relativnom jednostavnošću. D-Flat ima članove podataka za sve klase prozora u zajedničkoj strukturi prozora. Prednost je u tome što kod za svaku klasu ne mora da dereferencira ekstenzije bloka podataka specifične za klasu. Nedostatak je u tome što svaka klasa prozora nosi nadređenu veličinu svih klasa. DF++ koristi C++ mehanizam nasleđivanja da izvede klase prozora u hijerarhiji. Rezultat je jednostavniji kod i strukture podataka koje nisu veće nego što bi trebalo da budu. Klasa DF++ Application ima samo tri člana podataka osim onih koje nasleđuje: pokazivač na objekat MenuBar, pokazivač na objekat StatusBar i prekidač koji njena poruka Shov koristi da označi da objekat Application zauzima fokus.

[LISTING_TWO](#listing_two) je "applicat.cpp". Kada ga uporedite sa applicat.c iz prošlog maja, prvo što primetite je da je C++ verzija mnogo manja. Postoji nekoliko razloga za to. Prvo, C++ kod ne mora da presreće i tumači kodove poruka da bi primio i obradio poruke. DF++ poruke se šalju u C++ tradiciji - pozivima funkcijama članova klase. Stoga, svaku poruku poziva direktno pošiljalac i ne uključuje logiku prenošenja poruke kakvu koristi D-Flat. Drugo, applicat.cpp ne podržava meni Vindov za interfejs sa više dokumenata. Treće, applicat.cpp ne podržava promenu formata ekrana, prebacivanje na DOS ili uobičajene komande menija Help. Neke aplikacije neće koristiti ove funkcije, a one koje koriste bolje će služiti izvedenim klasama koje ih uključuju.

Funkcija OpenVindov klase Application registruje objekat sa DeskTop objektom pozivanjem funkcije SetApplication, koja omogućava radnoj površini, a samim tim i svim drugim objektima, da šalju poruke aplikaciji. Funkcija OpenVindov deklariše objekte MenuBar i StatusBar. MenuBar objekat je deklarisan samo ako konstruktor aplikacije uključuje pokazivač na niz MenuBarItem objekata, koji definišu traku menija i iskačuće menije. Provodićemo više vremena na menije u kasnijoj koloni.

Funkcije SetFocus i Shov uzrokuju da prozor aplikacije preuzme fokus drugačije od drugih prozora. Većina prozora koristi rutinske funkcije koje obezbeđuje osnovna klasa DFVindov. Prozor aplikacije se ponaša drugačije. Umesto da dozvoli da bude ponovo oslikan kad god korisnik klikne na njega, prozor aplikacije jednostavno poziva funkciju Border da bi okvir prozora odrazio stanje u fokusu. Ova strategija sprečava sistem da prefarba sve podređene prozore svaki put kada korisnik klikne na prozor aplikacije. Pretpostavka je da su već vidljivi i da ništa u prozoru aplikacije ne treba da se prefarba samo zato što se fokus pomerio.

Prozor aplikacije prima sve pritiske na tastere koje podređeni prozor u fokusu ne presreće. Klasa DFVindov brine o tome tako što šalje sve neobrađene poruke sa tastature roditelju prozora koji ih je primio. Prozor aplikacije će koristiti Ctrl+F4 i Alt+F4 da zatvori prozor. Sve ostale poruke sa tastature šalje objektu MenuBar. Ova strategija omogućava MenuBar-u da prima i obrađuje prečice na traci menija i tastere za ubrzavanje komandi menija.

Prozor aplikacije prosleđuje sve poruke ClockTick i StatusMessage objektu StatusBar na obradu.

### Klase kontola

Liste tri i četiri, strana 138, su control.h i control.cpp, respektivno, datoteke zaglavlja koje definišu klasu Control. Sve klase kontrolnih prozora potiču iz ove klase. Njegova svrha je da definiše ponašanje kontrolnog prozora bez obzira na njegovu funkciju. Kontrolni prozori su, po definiciji, uređaji za unos korisnika koji su podređeni prozori drugom prozoru. Oni mogu biti potomci prozora dokumenta specifičnog za aplikaciju, okvira za dijalog ili samog prozora aplikacije.

Trenutno, jedino ponašanje koje je inkapsulirala klasa Control je upravljanje promenljivom stanja omogućeno/onemogućeno i obrada nekih podrazumevanih pritisaka na tastere. Poruka sa tastature presreće tastere koji pomeraju fokus između srodnih kontrola roditeljskog prozora. Dok budem razvijao kontrole, logiku dijaloga i sistem pomoći, bez sumnje ću dodati funkcije ovoj klasi.

D-Flat C biblioteka gradi generičku kontrolnu logiku kao modul za obradu prozora u dijalbok.c, koji sam opisao u svojoj koloni iz juna 1992. godine. Uključuje kod osetljiv na tip klase kontrolnog prozora. DF++ inkapsulira taj kod u pojedinačne klase kontrolnog prozora.

### TektBok klasa

[LISTING_FIVE](#listing_five) je "textbox.h", datoteka zaglavlja koja definiše klasu TektBok, koja je izvedena iz klase Control. TektBok je osnovna klasa za mnoge druge kontrole koje će prikazivati, pomerati i tekst stranice. Uređivački okviri, okviri sa listama, iskačući meniji, dugmad i drugi svi potiču direktno ili indirektno iz klase TektBok, koja obuhvata ponašanje povezano sa trakama za pomeranje, prikazom i prikazom teksta i označenim blokovima teksta. TektBok je sveobuhvatna klasa. Skoro svaka vrsta prozora ima tekst kao svoju osnovu.

TektBok objekat se sastoji od prozora sa tekstom i, opciono, horizontalnih i vertikalnih traka za pomeranje. Tekst je predstavljen jednim String objektom pod nazivom tekt, koji sadrži znakove novog reda za označavanje krajeva redova i null znak za označavanje kraja teksta. Član podataka bufflen beleži dužinu bafera stringa, koja nije uvek ista kao i dužina samog stringa. Član podataka vlines je broj redova, a član podataka TektPointers ukazuje na niz celobrojnih pomeranja linija teksta. Svaki član niza je pomak prema prvom znaku tekstualne linije. Ovaj niz podržava efikasno pronalaženje određenih redova teksta. Član podataka tektlen je dužina tekstualnog niza, a tektvidth član podataka je dužina najdužeg reda u telu teksta. Članovi podataka vtop i vleft su pomaci koji predstavljaju pozicije reda i kolone teksta onako kako je trenutno prikazan u prozoru. Članovi podataka BlkBeg... i BlkEnd... specificiraju početnu i krajnju tačku označenog bloka teksta, ako postoji.

[LISTING_SIX](#listing_six) je "textbox.cpp", kod koji implementira klasu TektBok. Ovaj modul je reprezentativan za način rada kontrolnih prozora. Većina drugih kontrola će proizaći iz klase TektBok, dodajući sopstveno ponašanje. Klasa TektBok presreće svoju poruku Prikaži da bi dodala trake za pomeranje u prozor ako ih nema i ako su atributi trake za pomeranje postavljeni. TektBok klasa ima sopstvene jedinstvene poruke za inicijalizaciju teksta, dodavanje teksta, brisanje, promenu dužine bafera i izdvajanje određenog reda teksta. Postoje interne poruke, koje nisu dostupne korisnicima klase, koje prikazuju red teksta sa pažnjom koja se posvećuje ugrađenim znakovima prečice. Ove funkcije podržavaju prikaz menija i oznaka na dugmadima, koji imaju tastere za prečice koje moraju biti prikazane u boji za isticanje.

Poruka Paint TektBok-a oslikava prozor tako što prikazuje linije u vidnom polju prozora. Poruka sa tastature presreće tastere za pejdžing i tastere za pomeranje da bi poslali poruke za pejdžing i pomeranje prozoru, koji zauzvrat prelivaju i pomeraju prozor horizontalno i vertikalno. Klasa TektBok ne mora da obrađuje poruke miša za stranicu i skrolovanje. Te poruke primaju podređene trake za pomeranje, koje su sopstvene klase prozora. Oni tumače poruke miša i šalju poruke sa stranicama i pomeranjem u objekt prozora TektBok. Ovaj pristup je u poboljšanju u odnosu na način na koji D-Flat upravlja stranicama i pomeranjem. Prozor D-Flat tekstualnog okvira presreće i obrađuje same poruke miša sa trake za pomeranje, procedura koja povezuje trake za pomeranje sa okvirima za tekst i ne dozvoljava njihovo korišćenje u druge svrhe. Ovo je primer kako vas C++ namami da napravite bolji kod. Dozvoljavajući inkapsulaciju, jezik vas podstiče da organizujete funkcije objekta u posebnu definiciju i da povežete taj objekat što je lablje moguće sa drugim objektima. Uvek si to mogao da uradiš u C, i uvek smo pokušavali, ali mamac nije bio tu.

### Poruke i virtuelne funkcije

D-Flat C biblioteka podseća na Vindovs API na način na koji upravlja porukama. Svaka poruka može da ide u bilo koji prozor. Ako prozor nije zainteresovan za poruku, on može da prosledi poruku svojoj osnovnoj klasi, koja ima istu mogućnost, a to se nastavlja sve do osnovne klase. Prozor može da obradi poruku i/ili da je prosledi. Prozor može da odbije poruku tako što joj ne dozvoli da se nastavi, ili da zameni obradu od strane bilo koje osnovne klase tako što će je obraditi, a zatim ne proslediti dalje. Ovim procedurama upravljaju prilagođeni moduli za obradu prozora koji su dodeljeni instanci klase prozora i/ili podrazumevani moduli za obradu prozora koji su dodeljeni klasi u njenoj definiciji. Sve ovo veoma liči na objektno orijentisane poruke i polimorfizam C++-a, i slično je, osim što sam program donosi polimorfne odluke u toku rada umesto da ih definiše u hijerarhiji klasa. Glavna razlika je u tome što proces SendMessage C biblioteke može da pošalje bilo koju poruku u bilo koji prozor preko svog pokazivača prozora, a poruka će stići, bez obzira da li će biti obrađena ili ne. D-Flat++ nema funkciju SendMessage. Poruke su funkcije člana. Kako je ovo drugačije?

Klasa DFVindov ima niz virtuelnih funkcija članova koje predstavljaju najmanji zajednički imenitelj obrade prozora. Sve klase prozora imaju ove poruke zajedničke. Na primer, bilo koji prozor može imati naslov, biti pritisnut tasterom ili biti pomeren. Ako prozor dobro funkcioniše sa podrazumevanim ponašanjem DFVindov za te poruke, ništa mu više nije potrebno u njegovoj definiciji. Ako prozor želi da izmeni to ponašanje, on obezbeđuje sopstvene preopterećene funkcije člana i ponašanje se na odgovarajući način menja.

Poruke specifične za određenu izvedenu klasu nisu definisane u klasi DFVindov. Ilustracije radi, poruka PageUp za klasu TektBok nije definisana u DFVindov. Koje su implikacije toga? Prvo, ne možete poslati poruku PageUp prozoru koji nije izveden iz klase TektBok. Nije da biste želeli, ali čak i da jeste, ne možete to da uradite. Drugo, ne možete poslati poruku PageUp u TektBok prozor preko reference ili pokazivača na jednu od njegovih osnovnih klasa. Možda bi bilo trenutaka kada biste to želeli, ali ne možete.

Da bi se u potpunosti emulirala paradigma prenošenja poruka D-Flat-a ili Vindovs-a, biblioteka C++ klasa bi morala da uključi virtuelne funkcije člana za svaku moguću poruku u najvišoj osnovnoj klasi prozora, a to je DFVindov u D-Flat++-u. Šta bi bilo loše u tome? Dve stvari. Prvo, svaki put kada napravite novu klasu prozora za podršku vašoj aplikaciji, morali biste da dodate prazne virtuelne funkcije člana u klasu DFVindov. Drugo, svaka klasa prozora bi nosila gornje troškove svake poruke. Zašto je to tako?

Kada stavite virtuelne funkcije u klasu, dodajete dodatne troškove. Svaki objekat klase sadrži vptr pokazivač na vtbl tabelu pokazivača virtuelnih funkcija. Ako je virtuelna funkcija prazna funkcija, prostor koda se izdvaja za izraz povratka funkcije. Postoje vptrs za klasu objekata i svaku od njenih osnovnih klasa. Svi objekti iste klase ukazuju na zajednički vtbl. Vindovs ima oko 130 poruka. D-Flat ima oko 75. Ako bi biblioteka klasa inkapsulirala sve te poruke kao prazne virtuelne funkcije, postojale bi tabele za svaku klasu prozorskog objekta koju je program deklarisao. D-Flat ima preko 20 klasa prozora. Vindovs ima mnogo više. Kada bi D-Flat++ imao prazne virtuelne funkcije za svaku poruku, bilo bi preko 1500 vtbl unosa plus oni koji bi rezultirali dodavanjem prozorskih klasa za podršku vašoj aplikaciji.

Različite biblioteke C++ klasa koje obuhvataju Vindovs API rešavaju problem na različite načine. Borlandova ObjectVindovs biblioteka proširuje sintaksu jezika C++ da bi definisala funkcije poruke. Biblioteka Microsoft Foundation Class koristi makroe predprocesora za mapiranje funkcija poruke u mapu poruke. Vindovs++ biblioteka klasa o kojoj sam govorio u decembru 1992. gradi virtuelne funkcije za nekoliko najčešće korišćenih poruka i prosleđuje ostale kroz podrazumevanu funkciju koju klasa prozora mora da tumači u svoje svrhe. D-Flat++ postavlja funkcije poruka u definicije klasa na najviši nivo u hijerarhiji gde je poruka relevantna i pretpostavlja da će sistem i aplikacija slati poruke samo objektima klasa prozora koji ih očekuju.

#### LISTING_ONE

```c
// -------------- applicat.h
#ifndef APPLICAT_H
#define APPLICAT_H
#include "menubar.h"
#include "statbar.h"
class Application : public DFWindow    {
    MenuBar *menubar;            // points to menu bar
    StatusBar *statusbar;        // points to status bar
    Bool takingfocus;            // true while taking focus
    virtual void SetColors();
protected:
    // ------------- client window coordinate adjustments
    virtual void AdjustBorders();
public:
    Application(char *ttl,int lf,int tp,int ht,int wd,MenuBarItem *Menu = NULL)
                : DFWindow(ttl, lf, tp, ht, wd, NULL)
            { OpenWindow(Menu); }
    Application(char *ttl, int ht, int wd, MenuBarItem *Menu = NULL)
                : DFWindow(ttl, ht, wd, NULL)
            { OpenWindow(Menu); }
    Application(int lf, int tp, int ht, int wd, MenuBarItem *Menu = NULL)
                : DFWindow(lf, tp, ht, wd, NULL)
            { OpenWindow(Menu); }
    Application(int ht, int wd, MenuBarItem *Menu = NULL)
                : DFWindow(ht, wd, NULL)
            { OpenWindow(Menu); }
    Application(char *ttl, MenuBarItem *Menu = NULL)
                : DFWindow(ttl)
            { OpenWindow(Menu); }
    virtual ~Application()
            { if (windowstate != CLOSED) CloseWindow(); }
    // -------- API messages
    virtual void OpenWindow() { OpenWindow(NULL); }
    void OpenWindow(MenuBarItem *menu);
    virtual void CloseWindow();
    virtual Bool SetFocus();
    virtual void Show();
    virtual void Keyboard(int key);
    virtual void ClockTick();
    void StatusMessage(String& Msg);
};
void DispatchEvents(Application *ApWnd);
void InitializeEvents(void);
#endif
```

#### LISTING_TWO

```c
// ------------ applicat.cpp
#include "dflatpp.h"
void Application::OpenWindow(MenuBarItem *menu)
{
    extern DeskTop desktop;
    desktop.SetApplication(this);
    windowtype = ApplicationWindow;
    if (windowstate == CLOSED)
        DFWindow::OpenWindow();
    SetAttribute(BORDER | SAVESELF | CONTROLBOX | STATUSBAR);
    SetColors();
    if (menu != NULL)       {
        SetAttribute(MENUBAR);
        menubar = new MenuBar(menu, this);
    }
    else
        menubar = NULL;
    statusbar = new StatusBar(this);
    desktop.mouse().Show();
    DFWindow::SetFocus();
    takingfocus = False;
}
void Application::CloseWindow()
{
    if (menubar != NULL)
        delete menubar;
    if (statusbar != NULL)
        delete statusbar;
    desktop.SetApplication(NULL);
    DFWindow::CloseWindow();
}
// -------- set the fg/bg colors for the window
void Application::SetColors()
{
    colors.fg =
    colors.sfg =
    colors.ffg =
    colors.hfg = LIGHTGRAY;
    colors.bg =
    colors.sbg =
    colors.fbg =
    colors.hbg = BLUE;
}
void Application::AdjustBorders()
{
    DFWindow::AdjustBorders();
    if (attrib & MENUBAR)
        TopBorderAdj++;
    if (attrib & STATUSBAR)
        BottomBorderAdj = 1;
}
Bool Application::SetFocus()
{
    takingfocus = True;
    DFWindow::SetFocus();
    takingfocus = False;
    return True;
}
void Application::Show()
{
    if (!takingfocus || !isVisible())
        DFWindow::Show();
    else
        Border();
}
void Application::Keyboard(int key)
{
    switch (key)    {
        case CTRL_F4:
        case ALT_F4:
            CloseWindow();
            break;
        default:
            // ---- forward unprocessed keys to the menubar
            if (menubar != NULL)
                menubar->Keyboard(key);
            break;
    }
}
void Application::ClockTick()
{
    if (statusbar != NULL)
        statusbar->ClockTick();
}
void Application::StatusMessage(String& Msg)
{
    if (statusbar != NULL)
        statusbar->StatusMessage(Msg);
}
```

#### LISTING_THREE

```c
// ---------- control.h
#ifndef CONTROL_H
#define CONTROL_H
#include "dfwindow.h"
class TextBox;
class Control : public DFWindow    {
    Bool enabled;       // true if control is enabled
public:
    Control(char *ttl,int lf,int tp,int ht,int wd,DFWindow *par)
                        : DFWindow(ttl, lf, tp, ht, wd, par)
        { OpenControl(); }
    Control(char *ttl, int ht, int wd, DFWindow *par)
                        : DFWindow(ttl, ht, wd, par)
        { OpenControl(); }
    Control(int lf, int tp, int ht, int wd, DFWindow *par)
                        : DFWindow(lf, tp, ht, wd, par)
        { OpenControl(); }
    Control(int ht,int wd,DFWindow *par) : DFWindow(ht,wd,par)
        { OpenControl(); }
    Control(char *ttl)    : DFWindow(ttl)
        { OpenControl(); }
    virtual ~Control() { /* null */ }
    virtual void Keyboard(int key);
    void OpenControl() { Enable(); }
    void Enable()      { enabled = True; }
    void Disable()     { enabled = False; }
    Bool isEnabled()   { return enabled; }
};
#endif
```

#### LISTING_FOUR

```c
// ------------ control.cpp
#include "control.h"
#include "desktop.h"
void Control::Keyboard(int key)
{
    DFWindow *Wnd;
    switch (key)    {
        case UP:
            Wnd = desktop.InFocus();
            do {
                PrevSiblingFocus();
                if (Wnd == desktop.InFocus())
                    break;
            } while (desktop.InFocus()->WindowType() == MenubarWindow);
            break;
        case '\t':
        case DN:
        case ALT_F6:
            Wnd = desktop.InFocus();
            do {
                NextSiblingFocus();
                if (Wnd == desktop.InFocus())
                    break;
            } while (desktop.InFocus()->WindowType() ==
                    MenubarWindow);
            break;
        default:
            DFWindow::Keyboard(key);
            break;
    }
}
```

#### LISTING_FIVE

```c
// ------------- textbox.h
#ifndef TEXTBOX_H
#define TEXTBOX_H
#include "control.h"
const int SHORTCUTCHAR = '~';
const int InitialBufferSize = 1024;
class ScrollBar;
class TextBox : public Control    {
    ScrollBar *hscrollbar;  // horizontal scroll bar
    ScrollBar *vscrollbar;  // vertical scroll bar
    unsigned *TextPointers; // -> list of line offsets
    Bool resetscrollbox;
protected:
    // ---- text buffer
    String *text;           // window text
    unsigned int bufflen;   // length of buffer
    int wlines;             // number of lines of text
    unsigned int textlen;   // text length
    int textwidth;          // width of longest line in textbox
    // ---- text display
    int wtop;               // text line on top of display
    int wleft;              // left position in window viewport
    int BlkBegLine;         // beginning line of marked block
    int BlkBegCol;          // beginning column of marked block
    int BlkEndLine;         // ending line of marked block
    int BlkEndCol;          // ending column of marked block
    int shortcutfg;         // shortcut key color
    char *TextLine(int line)
        { return (char *)(*text) + *(TextPointers+line); }
    int DisplayShortcutField(
        String sc, int x, int y, int fg, int bg);
    void WriteShortcutLine(int lno, int fg, int bg);
    void WriteTextLine(int lno, int fg, int bg);
    void BuildTextPointers();
    void SetScrollBoxes();
    virtual void SetColors();
public:
    TextBox(char *ttl,int lf,int tp,int ht,int wd,DFWindow *par)
                        : Control(ttl, lf, tp, ht, wd, par)
            { OpenWindow(); }
    TextBox(char *ttl, int ht, int wd, DFWindow *par)
                        : Control(ttl, ht, wd, par)
            { OpenWindow(); }
    TextBox(int lf, int tp, int ht, int wd, DFWindow *par)
                        : Control(lf, tp, ht, wd, par)
            { OpenWindow(); }
    TextBox(int ht, int wd, DFWindow *par) : Control(ht,wd,par)
            { OpenWindow(); }
    TextBox(char *ttl)    : Control(ttl)
            { OpenWindow(); }
    virtual ~TextBox()
        { if (windowstate != CLOSED) CloseWindow(); }
    // -------- textbox API messages
    virtual void ScrollUp();
    virtual void ScrollDown();
    virtual void ScrollRight();
    virtual void ScrollLeft();
    virtual void PageUp();
    virtual void PageDown();
    virtual void PageRight();
    virtual void PageLeft();
    virtual void Home();
    virtual void End();
    virtual void OpenWindow();
    virtual void CloseWindow();
    virtual void AddText(char *txt);
    virtual void SetText(char *txt);
    virtual void SetTextLength(unsigned int len);
    virtual void ClearText();
    virtual void Show();
    virtual void Paint();
    virtual void Keyboard(int key);
    String ExtractTextLine(int lno);
    void ClearTextBlock()
        { BlkBegLine=BlkEndLine=BlkBegCol=BlkEndCol=0; }
    void HorizontalPagePosition(int pct);
    void VerticalPagePosition(int pct);
};
#endif
```

#### LISTING_SIX

```c
// ------------- textbox.cpp
#include "desktop.h"
#include "textbox.h"
#include "scrolbar.h"
// ----------- common constructor code
void TextBox::OpenWindow()
{
    windowtype = TextboxWindow;
    if (windowstate == CLOSED)
        Control::OpenWindow();
    text = NULL;
    bufflen = InitialBufferSize;
    hscrollbar = vscrollbar = NULL;
    TextPointers = NULL;
    ClearText();
    SetColors();
}
void TextBox::CloseWindow()
{
    ClearText();
    if (hscrollbar != NULL)
        delete hscrollbar;
    if (vscrollbar != NULL)
        delete vscrollbar;
    Control::CloseWindow();
}
// ------ show the textbox
void TextBox::Show()
{
    if ((attrib & HSCROLLBAR) && hscrollbar == NULL)    {
        hscrollbar = new ScrollBar(HORIZONTAL, this);
        hscrollbar->SetAttribute(FRAMEWND);
    }
    if ((attrib & VSCROLLBAR) && vscrollbar == NULL)    {
        vscrollbar = new ScrollBar(VERTICAL, this);
        vscrollbar->SetAttribute(FRAMEWND);
    }
    Control::Show();
}
// ------------ build the text line pointers
void TextBox::BuildTextPointers()
{
    textwidth = wlines = 0;
    // ---- count the lines of text
    char *cp1, *cp = *text;
    while (*cp)    {
        wlines++;
        while (*cp && *cp != '\n')
            cp++;
        if (*cp)
            cp++;
    }
    // ----- build the pointer array
    delete TextPointers;
    TextPointers = new unsigned int[wlines];
    unsigned int off;
    cp = *text;
    wlines = 0;
    while (*cp)    {
        off = (unsigned int) (cp - *text);
        *(TextPointers + wlines++) = off;
        cp1 = cp;
        while (*cp && *cp != '\n')
            cp++;
        textwidth = max(textwidth, (unsigned int) (cp - cp1));
        if (*cp)
            cp++;
    }
}
// --------- add a line of text to the textbox
void TextBox::AddText(char *txt)
{
    String tx(txt);
    int len = tx.Strlen();
    if (tx[len-1] != '\n')
        textlen++;
    textlen += len;
    if (text == NULL)
        text = new String(bufflen);
    if (textlen > text->StrBufLen())
        text->ChangeLength(max(bufflen, textlen));
    *text += tx;
    if (*text[len-1] != '\n')
        *text += String("\n");
    BuildTextPointers();
}
// --------- set the textbox's text buffer to new text
void TextBox::SetText(char *txt)
{
    ClearText();
    AddText(txt);
}
// ------ set the length of the text buffer
void TextBox::SetTextLength(unsigned int len)
{
    if (text != NULL)
        text->ChangeLength(len);
    bufflen = len;
}
// --------- clear the text from the textbox
void TextBox::ClearText()
{
    if (text != NULL)
        delete text;
    textlen = 0;
    wlines = 0;
    textwidth = 0;
    wtop = wleft = 0;
    ClearTextBlock();
    if (TextPointers != NULL)
        delete TextPointers;
}
// -------- set the fg/bg colors for the window
void TextBox::SetColors()
{
    colors.fg = BLACK;
    colors.bg = LIGHTGRAY;
    colors.sfg = LIGHTGRAY;
    colors.sbg = BLACK;
    colors.ffg = BLACK;
    colors.fbg = LIGHTGRAY;
    colors.hfg = BLACK;
    colors.hbg = LIGHTGRAY;
    shortcutfg = BLUE;
}
// ------- extract a text line
String TextBox::ExtractTextLine(int lno)
{
    char *lp = TextLine(lno);
    int offset = lp - (char *) *text;
    for (int len = 0; *(lp+len) && *(lp+len) != '\n'; len++)
        ;
    return text->mid(len, offset);
}
// ---- display a line with a shortcut key character
void TextBox::WriteShortcutLine(int lno, int fg, int bg)
{
    String sc = ExtractTextLine(lno);
    int x = sc.Strlen();
    int y = lno-wtop;
    x -= DisplayShortcutField(sc, 0, y, fg, bg);
    // --------- pad the line
    int wd = ClientWidth() - x;
    if (wd > 0)
        WriteClientString(String(wd, ' '), x, y, fg, bg);
}
// ---- display a shortcut field character
int TextBox::DisplayShortcutField(String sc, int x, int y, int fg, int bg)
{
    int scs = 0;
    int off = sc.FindChar(SHORTCUTCHAR);
    if (off != -1)    {
        scs++;
        if (off != 0)    {
            String ls = sc.left(off);
            WriteClientString(ls, x, y, fg, bg);
        }
        WriteClientChar(sc[off+1], x+off, y, shortcutfg, bg);
        int len = sc.Strlen()-off-2;
        if (len > 0)    {
            String rs = sc.right(len);
            scs += DisplayShortcutField(rs, x+off+1, y, fg, bg);
        }
    }
    else
        WriteClientString(sc, x, y, fg, bg);
    return scs;
}
// ------- write a text line to the textbox
void TextBox::WriteTextLine(int lno, int fg, int bg)
{
    if (lno < wtop || lno >= wtop + ClientHeight())
        return;
    int wd = ClientWidth();
    String tl = ExtractTextLine(lno);
    String ln = tl.mid(wd, wleft);
    int dif = wd-ln.Strlen();
    if (dif > 0)
        ln = ln + String(dif, ' ');    // pad the line with spaces
    // ----- display the line
    WriteClientString(ln, 0, lno-wtop, fg, bg);
}
// ---------- paint the textbox
void TextBox::Paint()
{
    if (text == NULL)
        Control::Paint();
    else    {
        int ht = ClientHeight();
        int wd = ClientWidth();
        int fg = colors.fg;
        int bg = colors.bg;
        for (int i = 0; i < min(wlines-wtop,ht); i++)
            WriteTextLine(wtop+i, fg, bg);
        // ---- pad the bottom lines in the window
        String line(wd, ' ');
        while (i < ht)
            WriteClientString(line, 0, i++, fg, bg);
        if (resetscrollbox)
            SetScrollBoxes();
        resetscrollbox = False;
    }
}
// ------ process a textbox keystroke
void TextBox::Keyboard(int key)
{
    switch (key)    {
        case UP:
            if (ClientTop() == ClientBottom())
                break;
            ScrollDown();
            return;
        case DN:
            if (ClientTop() == ClientBottom())
                break;
            ScrollUp();
            return;
        case FWD:
            ScrollLeft();
            return;
        case BS:
            ScrollRight();
            return;
        case PGUP:
            PageUp();
            return;
        case PGDN:
            PageDown();
            return;
        case CTRL_PGUP:
            PageLeft();
            return;
        case CTRL_PGDN:
            PageRight();
            return;
        case HOME:
            Home();
            return;
        case END:
            End();
            return;
        default:
            break;
    }
    Control::Keyboard(key);
}
// ------- scroll up one line
void TextBox::ScrollUp()
{
    if (wtop < wlines-1)    {
        int fg = colors.fg;
        int bg = colors.bg;
        desktop.screen().Scroll(ClientRect(), 1, fg, bg);
        wtop++;
        int ln = wtop+ClientHeight()-1;
        if (ln < wlines)
            WriteTextLine(ln, fg, bg);
        SetScrollBoxes();
    }
}
// ------- scroll down one line
void TextBox::ScrollDown()
{
    if (wtop)    {
        int fg = colors.fg;
        int bg = colors.bg;
        desktop.screen().Scroll(ClientRect(), 0, fg, bg);
        --wtop;
        WriteTextLine(wtop, fg, bg);
        SetScrollBoxes();
    }
}
// ------- scroll left one character
void TextBox::ScrollLeft()
{
    if (wleft < textwidth-1)
        wleft++;
    Paint();
}
// ------- scroll right one character
void TextBox::ScrollRight()
{
    if (wleft > 0)
        --wleft;
    Paint();
}
// ------- page up one screenfull
void TextBox::PageUp()
{
    if (wtop)    {
        wtop -= ClientHeight();
        if (wtop < 0)
            wtop = 0;
        resetscrollbox = True;
        Paint();
    }
}
// ------- page down one screenfull
void TextBox::PageDown()
{
    if (wtop < wlines-1)    {
        wtop += ClientHeight();
        if (wlines < wtop)
            wtop = wlines-1;
        resetscrollbox = True;
        Paint();
    }
}
// ------- page right one screenwidth
void TextBox::PageRight()
{
    if (wleft < textwidth-1)    {
        wleft += ClientWidth();
        if (textwidth < wleft)
            wleft = textwidth-1;
        resetscrollbox = True;
        Paint();
    }
}
// ------- page left one screenwidth
void TextBox::PageLeft()
{
    if (wleft)    {
        wleft -= ClientWidth();
        if (wleft < 0)
            wleft = 0;
        resetscrollbox = True;
        Paint();
    }
}
// ----- move to the first line of the textbox
void TextBox::Home()
{
    wtop = 0;
    Paint();
}
// ----- move to the last line of the textbox
void TextBox::End()
{
    wtop = wlines-ClientHeight();
    if (wtop < 0)
        wtop = 0;
    Paint();
}
// ----- position the scroll boxes
void TextBox::SetScrollBoxes()
{
    if (vscrollbar != NULL)
        vscrollbar->TextPosition(wlines ? (wtop*100)/wlines : 0);
    if (hscrollbar != NULL)
        hscrollbar->TextPosition(textwidth ? (wleft*100)/textwidth : 0);
}
// ---- compute the horizontal page position
void TextBox::HorizontalPagePosition(int pct)
{
    wleft = (textwidth * pct) / 100;
    Paint();
}
// ---- compute the vertical page position
void TextBox::VerticalPagePosition(int pct)
{
    wtop = (wlines * pct) / 100;
    Paint();
}
```

Copyright © 1993, Dr. Dobb's Journal
