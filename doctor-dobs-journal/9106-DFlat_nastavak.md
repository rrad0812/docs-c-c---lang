
# C PROGRAMMING 9106

## D-Flat Nastavak - Al Stivens

Ovo je drugi deo D-Flat-a, novog projekta "C programiraje" kolone koji sam započeo prošlog meseca. D-Flat je biblioteka C funkcija koja implementira dizajn interfejsa SAA zajedničkog korisničkog pristupa u modelu programiraja zasnovanom na događajima za MS-DOS aplikacije u tekstualnom režimu. Prošlog meseca smo napravili hardverski zavisni kod koji se bavi mišem, tastaturom i ekranom. Takođe smo kodirali stvari specifične za kompajler, uslovni kod za vreme kompajliraja koji razlikuje Turbo C od Microsoft C. Kod od prošlog meseca obuhvata većinu hardvera i zavisnosti kompajlera. Ako želite da prenesete D-Flat na drugi kompajler ili računar, izmenili biste taj kod. Kod koji se pojavjuje od sada će uglavnom biti nezavisan od hardvera i kompajlera, iako postoji povremeno #ifdef MSC da bi se zaobišle neke razlike u kompajleru. Ovog meseca bavimo se delovima biblioteke koji upravjaju konfiguracijom D-Flat aplikacije, koji upravjaju klasama prozora i koji sadrže upravjačke programe za prozore niskog nivoa.

Moj pristup objašjavaju D-Flat-a u početku će koristiti format tutorijala. Kako budete napredovali kroz seriju u narednim mesecima, objasniću različite delove sistema čim ih koristi kod o kome se raspravja. Ovo je pristup odozdo prema gore. Želim da sklonim stvari niskog nivoa sa puta kako bismo se mogli koncentrisati na implementaciju klasa prozora i kako će ih vaši programi aplikacija koristiti. Postoji mnogo funkcija i makroa u API-ju koji će u početku proći bez mnogo objašjeja. Datoteka zaglavja dflat.h od prošlog meseca ima mnogo takvih makroa. Dok ih koristimo, objasniću ih. Datoteka vindov.c ovog meseca ima kod koji podržava kliping, ali ne moramo da ulazimo u isečak dok ne prođemo kroz osnove prikazivaja i korišćeja prozora.

Kada se serija završi, objaviću kompletan referentni vodič za programere za poruke, funkcije i makroe koji čine D-Flat API. Pored toga, objaviću generički korisnički vodič za D-Flat aplikacije. Moći ćete da koristite taj vodič za bilo koju dokumentaciju koja prati vašu prijavu. Na kraju će ovi vodiči ući u datoteku izvornog koda koju možete preuzeti sa CompuServe-a ili Tele-Path-a, kao što je objašjeno kasnije u ovoj koloni.

### Konfiguracija programa

Aplikacioni program D-Flat biće podložan nekim konfiguracionim stavkama koje kontroliše korisnik. Korisnik će možda moći da odredi boje, opcije uređivača i tako daje. D-Flat biblioteka ukjučuje funkcije i strukture podataka koje podržavaju održavaje konfiguracije. [LISTING_ONE](#listing_one) je "config.h", datoteka zaglavja koja opisuje strukture podataka o konfiguraciji. Prvi unos je #define za globalni simbol DFLAT_APPLICATION. Ovaj string imenuje vaš aplikacijski program i takođe će biti ime konfiguracione datoteke. Kao što je prikazano, string se inicijalizuje na literal stringa "memopad". Konfiguracioni fajl će, prema tome, biti MEMOPAD.CFG.

Struktura boja u config.h opisuje konfiguraciju boja za svaku od klasa prozora D-Flat. Vrednosti su u parovima koji se sastoje od boja predjeg plana i pozadine za svaku klasu. Ponekad ćete videti dva para za klasu, kao što su ButtonFG, ButtonBG, ButtonSelFG i ButtonSelBG. Drugi par, koji uvek ima Sel u svom identifikatoru, specificira boje za istaknute stavke podataka u klasi prozora. Ove stavke ukjučuju trake menija, birače okvira sa listom i označene blokove teksta. Neke klase prozora imaju definicije za boje okvira prozora. Ovi identifikatori ukjučuju vrednost Frame.

Neke od boja su za komponente prozora, a ne za same prozore. Vrednosti Title i InFocusTitle su boje za naslove svih prozora. Vrednosti MenuBar se odnose na traku menija aplikacije. Boje InactiveSelFG i ShortCutFG služe za vrednosti slova prečica u izborima menija.

CONFIG tipedef u config.h definiše format za konfiguracioni zapis. Ako vašoj D-Flat aplikaciji treba više konfiguracionih stavki – a većina jih hoće – dodajete svoje prilagođene objekte konfiguracionih podataka tipu podataka CONFIG. U početku postoji pet poja. Mono poje, kada je tačno, specificira da se prozori prikazuju u monohromatskim bojama bez obzira na video sistem korisnika. Poje Insert-Mode određuje da li uređivač teksta za prozore okvira za uređivaje podrazumevano počije u režimu umetaja ili prepisivaja. Poje Tabs određuje širinu kartice za uređivač teksta. Poje VordVrap određuje da li uređivač prelama reči na desnoj margini prozora okvira za uređivaje u više redova ili se pomera horizontalno dok korisnik ne pritisne taster Enter. Poje clr sadrži boje za aplikaciju.

[LISTING_TWO](#listing_two), strana 148 je config.c, koji sadrži podrazumevane inicijalizovane vrednosti za konfiguracioni zapis i funkcije za čitanje i pisanje konfiguracione datoteke. Postoje dva niza boja. Jedan je za sisteme u boji, a drugi za monohromatske sisteme. Da biste promenili podrazumevane vrednosti, promenili biste ove nizove. Nizovi se sami inicijalizuju korišćenjem vrednosti boja iz datoteke zaglavlja Turbo C conio.h. Datoteka zaglavlja dflat.h, objavljena prošlog meseca, sadrži iste vrednosti za Microsoft C korisnike.

Cfg struktura je instanca tipa podataka CONFIG koja sadrži konfiguracione vrednosti programa. Ako aplikacija ne koristi vrednosti prilagođene konfiguracije, primenjuju se podrazumevane vrednosti.

Funkcije LoadConfig i SaveConfig učitavaju i čuvaju trenutne vrednosti konfiguracije iz i u datoteku koja je imenovana prema globalnoj promenljivoj DFLAT_APPLICATION.

### Prozorske klase

D-Flat radi sa prozorima u hijerarhiji klasa prozora. Ovaj model je sličan onom koji koristi Microsoft Vindovs platforma za programiranje. Kada kreirate prozor, određujete njegovu klasu i D-Flat stoga zna kako da se nosi sa prozorom. Klasa identifikuje boje prozora i određene karakteristike obrade. Prozor okvira za uređivanje se ponaša drugačije od prozora okvira sa listom, na primer. [LISTING_THREE](#listing_three) je "classdef.h", datoteka zaglavlja koja definiše tip podataka CLASSDEFS, koji sadrži opis klase prozora. Classdefs niz CLASSDEFS tipova podataka sadrži unos za svaku klasu. Svaki unos navodi klasu prozora koju opisuje, osnovnu klasu – ako postoji – iz koje je izvedena, boje klase prozora, adresu funkcije za obradu prozora na koju se šalju sve poruke za prozor i vrednost atributa prozora.

Vrednosti klase prozora su definisane u datoteci zaglavlja dflat.h, koju sam objavio prošlog meseca. Ako želite da dodate klase prozora u hijerarhiju, dodajte unos u enum vindov_class u dflat.h i unos u classdefs niz na [LISTING_FOUR](#listing_four), "classdefs.c". Ova tehnika se razlikuje od načina na koji to rade Vindovs programeri. Oni stavljaju runtime kod u svoje programe da registruju nove klase. D-Flat programer koristi C kompajler da registruje klase tako što kodira nove klase u tabele klasa.

Svaki unos u polju classdefs definiše boje povezane sa klasom. Postoje tri seta boja - jedna za sam prozor, jedna za istaknute selekcije u prozoru i jedna za okvir prozora. Obratite pažnju na to da su unosi boja pokazivači na vrednosti boja. Ovo omogućava programu da promeni konfiguraciju boja bez promene ove tabele. Ako je unos boje NULL pokazivač, koji važi za istaknute boje, vrednost pretpostavlja boju samog prozora.

Svaka klasa ima pridruženu funkciju obrade prozora, a adresa te funkcije se pojavljuje u unosu classdefs niza za klasu. Kada nešto pošalje poruku prozoru, prvo se izvršava funkcija za obradu prozora dodeljena klasi prozora. Ta funkcija će tada ili obraditi poruku i vratiti ili pozvati funkciju klase iz koje je originalna klasa izvedena.

Originalne D-Flat klase objavljene u classdef.h su: NORMAL, APPLICATION, TEKSTBOKS, LISTBOKS, EDITBOKS, MENUBAR, POPOVNMENU, BUTTON, DIALOG, ERRORBOKS, MESSAGEBOKS, HELPBOKS i DUMMI.

Većina klasa prozora izvedena je iz klase prozora NORMAL - ili direktno ili tako što su izvedene iz klasa koje su izvedene iz klase NORMAL. Klasa NORMAL upravlja tim procesima – kao što su kreiranje, zatvaranje, pomeranje, promena veličine, fokusiranje i tako dalje – koji mogu biti zajednički za sve prozore.

Prozor APLIKACIJE je prvi prozor koji otvara D-Flat aplikacija. To je roditelj prozora MENUBAR i svih prozora dokumenata.

TEKSTBOKS prozor je onaj u koji možete da upišete tekst i pomoću kojeg korisnik može da skroluje i lista kroz tekst.

Klase prozora LISTBOKS i EDITBOKS su izvedene iz klase TEKSTBOKS. Prozor LISTBOKS se sastoji od redova na listi sa kursorom za izbor koji korisnik može da pomera gore i dole i pomoću kojeg korisnik može da bira između unosa na listi. Prozor EDITBOKS sadrži tekst koji korisnik unosi koristeći funkcije uređivača teksta.

Prozor MENIJA se sastoji od jednog reda koji se pojavljuje u prvom redu prozora APLIKACIJE i sadrži izbore menija. Kada korisnik napravi izbor menija, prozor MENI BAR otvara prozor POPDOVNMENU.

Klasa POPDOVNMENU je izvedena iz klase LISTBOKS. To je okvir sa listom na jednoj stranici koji će slati komandne poruke svom roditelju kao rezultat odabira korisnika.

Klasa BUTTON definiše mali statički prozor TEKSTBOKS koji reaguje na odabir korisnika tako što svom roditelju šalje poruku.

Klasa prozora DIALOG definiše prozor koji sadrži skup drugih tipova prozora za implementaciju okvira za dijalog.

Prozori ERRORBOKS, MESSAGEBOKS i HELPBOKS su DIJALOG prozori sa definisanim porukama i korisničkim radnjama koje su im dodeljene.

Klasa prozora DUMMI definiše okvir prozora koji se pojavljuje kada korisnik pomera ili dimenzionira prozor. Naučićete sve o ovim časovima prozora u narednim mesecima kada budemo razgovarali o njima i implementirali njihove module za obradu prozora.

### Atributi prozora

Classdefs niz definiše podrazumevane atribute prozora za klase prozora. Kada kreirate prozor, on automatski ima atribute klase kojoj pripada, kao i sve klase iz kojih je izveden. Pored toga, možete da navedete dodatne atribute kada kreirate prozor, kao i da dodate i uklonite atribute iz postojećeg prozora u bilo kom trenutku. Sledeći kodovi atributa su definisani u classdef.h: SHADOV, MOVEABLE, SIZEABLE, HASMENUBAR,VSCROLLBAR, HSCROLLBAR, VISIBLE, SAVESELF, TITLEBAR, CONTROLBOKS, MINMAKSBOKS, NOCLIP, READONLI, MULTILINE i HASBORDER.

Većina atributa ukazuje na to da prozor uključuje određeno svojstvo. Na primer, atribut SHADOV kaže da prozor ima video senku, atribut MOVEABLE kaže da korisnik može da pomera prozor po ekranu. Ostali atributi nisu tako očigledni. Atribut VISIBLE kaže da prozor treba da bude prikazan kada ga program kreira. Atribut NOCLIP govori prozoru da se može prikazati u delovima ekrana koji su izvan njegovog roditelja. Atribut READONLI je za prozore EDITBOKS, korisnik ne sme da menja tekst u prozoru READONLI EDITBOKS. Prozor EDITBOKS koji nema atribut MULTILINE zauzima samo jedan red i ne prelama reči niti reaguje na taster Enter.

Atribut SAVESELF kaže da prozor treba da sačuva i vrati video memoriju kada se kreira ili prikaže i zatvori ili sakrije. Vindovs koji nemaju ovaj atribut ne čuvaju video memoriju kada su prikazani. Štaviše, kada su skriveni ili zatvoreni, oni šalju poruke svim prozorima koji se preklapaju da prefarbaju delove sebe koji se preklapaju sa prozorom koji se zatvara. Atribut SAVESELF je taktika efikasnosti za prozore koji drže fokus sve dok su otvoreni. Klase prozora POPDOVNMENU i DIALOG imaju atribut SAVESELF.

### Upravljački program za prozor

[LISTING_FIVE], je "window.c", upravljački modul za D-Flat prozore. Počinje sa funkcijom CreateVindov. D-Flat aplikacija poziva ovu funkciju da kreira prozor. Ovoj funkciji morate proslediti klasu prozora, tekst naslova prozora i gornje leve koordinate ekrana u odnosu na nulu gde će se prozor prvo prikazati, njegovu visinu i širinu, pokazivač – obično NULL – na prostor podataka koji čuva dodatne podatke o prozoru, VINDOV ručku roditeljskog prozora, pokazivač na modul za obradu prozora i vrednost atributa.

Naslov prozora može biti NULL, u kom slučaju prozor neće imati naslov. Ako je bilo koja od gornjih levih koordinata prozora - 1, prozor će biti centriran na pridruženoj osi. Ako date adresu funkciji za obradu prozora, poruke za taj prozor će se prvo slati navedenoj funkciji. Diskusija o radu funkcije za obradu prozora pojavljuje se kasnije u ovoj seriji kada uđemo u primere programa. Vrednost atributa koju prosledite CreateVindov kao poslednji parametar sadrži dodatne atribute koje će prozor imati iznad i iznad onih dodeljenih njegovoj klasi.

Funkcija CreateVindov dodeljuje memoriju za strukturu prozora i postavlja polja u strukturi na njihove početne vrednosti koje su određene argumentima funkcije ili podrazumevanim vrednostima. Format strukture se pojavljuje u dflat.h od prošlog meseca. Nakon što je struktura inicijalizovana, funkcija šalje prozoru poruku CREATE_VINDOV. Razgovaraćemo o mehanizmu za slanje i obradu prozorskih poruka sledećeg meseca. Ako prozor ima atribut VISIBLE, funkcija šalje prozoru poruku SHOV_VINDOV. Konačno, vraća ručicu VINDOV funkciji koja je pozvala CreateVindov.

Izvorna datoteka vindov.c sadrži nekoliko funkcija upravljačkog programa prozora koje spoljne funkcije mogu pozvati. Neki od njih podržavaju D-Flat API, a drugi se koriste za same D-Flat funkcije. Funkcija AddTitle prihvata VINDOV ručicu i naslov stringa i dodaje naslov prozoru. Funkcija PutVindovChar upisuje jedan znak u prozor na određenoj k,i koordinati. Funkcije clipbottom i clipline vrše odsecanje ekrana prozora kako bi ga zadržale unutar ekrana i granica roditeljskog prozora. Funkcija vriteline upisuje string u prozor na određenoj k, i koordinati i dodaje razmake na ivicu prozora ako se to kaže. Funkcija RepaintBorder prikazuje ivicu prozora uključujući sve trake za pomeranje, senku i naslovnu traku. Funkcija ClearVindov čisti prostor podataka prozora u razmake. Funkcije GetVideoBuffer i RestoreVideoBuffer upravljaju operacijama čuvanja video memorije za prozore koji imaju atribut SAVESELF. Funkcija LineLength izračunava logičku dužinu linije podataka za prikaz, prilagođavajući se za sve ugrađene kontrole boja u tekstu.

#### LISTING_ONE

```c
/* ---------------- config.h -------------- */

#ifndef CONFIG_H
#define CONFIG_H

#define DFLAT_APPLICATION "memopad"

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
    char mono;         /* True for B/W screens on any monitor */
    int InsertMode;    /* Editor insert mode                  */
    int Tabs;          /* Editor tab stops                    */
    int WordWrap;      /* True to word wrap editor            */
    struct colors clr; /* Colors                              */
} CONFIG;

extern CONFIG cfg;
extern struct colors color, bw;

void LoadConfig(void);
void SaveConfig(void);

#endif
```

#### LISTING_TWO

```c
/* ------------- config.c ------------- */

#include <conio.h>
#include "dflat.h"

/* ----- default colors for color video system ----- */
struct colors color = {
    LIGHTGRAY, BLUE,  /* Application   */
    LIGHTGRAY, BLACK, /* Normal        */
    BLACK, CYAN,      /* Button        */
    WHITE, CYAN,      /* ButtonSel     */
    LIGHTGRAY, BLUE,  /* Dialog        */
    YELLOW, RED,      /* ErrorBox      */
    BLACK, LIGHTGRAY, /* MessageBox    */
    BLACK, LIGHTGRAY, /* HelpBox       */
    WHITE, CYAN,      /* InFocusTitle  */
    BLACK, CYAN,      /* Title         */
    GREEN, LIGHTGRAY, /* Dummy         */
    BLACK, LIGHTGRAY, /* TextBox       */
    LIGHTGRAY, BLACK, /* TextBoxSel    */
    LIGHTGRAY, BLUE,  /* TextBoxFrame  */
    BLACK, LIGHTGRAY, /* ListBox       */
    LIGHTGRAY, BLACK, /* ListBoxSel    */
    LIGHTGRAY, BLUE,  /* ListBoxFrame  */
    BLACK, LIGHTGRAY, /* EditBox       */
    LIGHTGRAY, BLACK, /* EditBoxSel    */
    LIGHTGRAY, BLUE,  /* EditBoxFrame  */
    BLACK, LIGHTGRAY, /* MenuBar       */
    BLACK, CYAN,      /* MenuBarSel    */
    BLACK, CYAN,      /* PopDown       */
    BLACK, LIGHTGRAY, /* PopDownSel    */
    DARKGRAY,         /* InactiveSelFG */
    RED               /* ShortCutFG    */
};

/* ----- default colors for mono video system ----- */
struct colors bw = {
    LIGHTGRAY, BLACK, /* Application   */
    LIGHTGRAY, BLACK, /* Normal        */
    BLACK, LIGHTGRAY, /* Button        */
    WHITE, LIGHTGRAY, /* ButtonSel     */
    LIGHTGRAY, BLACK, /* Dialog        */
    LIGHTGRAY, BLACK, /* ErrorBox      */
    LIGHTGRAY, BLACK, /* MessageBox    */
    BLACK, LIGHTGRAY, /* HelpBox       */
    BLACK, LIGHTGRAY, /* InFocusTitle  */
    BLACK, LIGHTGRAY, /* Title         */
    BLACK, LIGHTGRAY, /* Dummy         */
    LIGHTGRAY, BLACK, /* TextBox       */
    BLACK, LIGHTGRAY, /* TextBoxSel    */
    LIGHTGRAY, BLACK, /* TextBoxFrame  */
    LIGHTGRAY, BLACK, /* ListBox       */
    BLACK, LIGHTGRAY, /* ListBoxSel    */
    LIGHTGRAY, BLACK, /* ListBoxFrame  */
    LIGHTGRAY, BLACK, /* EditBox       */
    BLACK, LIGHTGRAY, /* EditBoxSel    */
    LIGHTGRAY, BLACK, /* EditBoxFrame  */
    LIGHTGRAY, BLACK, /* MenuBar       */
    BLACK, LIGHTGRAY, /* MenuBarSel    */
    BLACK, LIGHTGRAY, /* PopDown       */
    LIGHTGRAY, BLACK, /* PopDownSel    */
    DARKGRAY,         /* InactiveSelFG */
    WHITE             /* ShortCutFG    */
};

/* ------ default configuration values ------- */
CONFIG cfg = {
    FALSE,           /* mono                  */
    TRUE,            /* Editor Insert Mode    */
    4,               /* Editor tab stops      */
    TRUE             /* Editor word wrap      */
};

/* ------ load a configuration file from disk ------- */
void LoadConfig(void)
{
    FILE *fp = fopen(DFLAT_APPLICATION ".cfg", "rb");
    if (fp != NULL)    {
        fread(&cfg, sizeof(CONFIG), 1, fp);
        fclose(fp);
    }
}

/* ------ save a configuration file to disk ------- */
void SaveConfig(void)
{
    FILE *fp = fopen(DFLAT_APPLICATION ".cfg", "wb");
    if (fp != NULL)    {
        cfg.InsertMode = GetCommandToggle(ID_INSERT);
        cfg.WordWrap = GetCommandToggle(ID_WRAP);
        fwrite(&cfg, sizeof(CONFIG), 1, fp);
        fclose(fp);
    }
}
```

#### LISTING_THREE

```¢
/* ---------------- classdef.h --------------- */

#ifndef CLASSDEF_H
#define CLASSDEF_H

typedef struct classdefs {
    CLASS class;                        /* window class      */
    CLASS base;                         /* base window class */
    char *fg,*bg,*sfg,*sbg,*ffg,*fbg;   /* colors            */
    int (*wndproc)(struct window *,enum messages,PARAM,PARAM);
    int attrib;
} CLASSDEFS;

extern CLASSDEFS classdefs[];

#define SHADOW      0x0001
#define MOVEABLE    0x0002
#define SIZEABLE    0x0004
#define HASMENUBAR  0x0008
#define VSCROLLBAR  0x0010
#define HSCROLLBAR  0x0020
#define VISIBLE     0x0040
#define SAVESELF    0x0080
#define TITLEBAR    0x0100
#define CONTROLBOX  0x0200
#define MINMAXBOX   0x0400
#define NOCLIP      0x0800
#define READONLY    0x1000
#define MULTILINE   0x2000
#define HASBORDER   0x4000

int FindClass(CLASS);
#define DerivedClass(class) (classdefs[FindClass(class)].base)

#endif
```

#### LISTING_FOUR

```c
/* ---------------- classdef.c ---------------- */

#include <stdio.h>
#include "dflat.h"

/* Add class definitions to this table.
 * Add the class symbol to the CLASS list in dflat.h
 */

CLASSDEFS classdefs[] = {
    {   /* ---------- NORMAL Window Class ----------- */
        NORMAL,
        -1,
        &cfg.clr.NormalFG, &cfg.clr.NormalBG,
        NULL, NULL,
        &cfg.clr.NormalFG, &cfg.clr.NormalBG,
        NormalProc
    },
    {   /* ---------- APPLICATION Window Class ----------- */
        APPLICATION,
        NORMAL,
        &cfg.clr.ApplicationFG, &cfg.clr.ApplicationBG,
        NULL, NULL,
        &cfg.clr.ApplicationFG, &cfg.clr.ApplicationBG,
        ApplicationProc,
        VISIBLE | SAVESELF | CONTROLBOX | TITLEBAR | HASBORDER
    },
    {   /* ------------ TEXTBOX Window Class -------------- */
        TEXTBOX,
        NORMAL,
        &cfg.clr.TextBoxFG, &cfg.clr.TextBoxBG,
        &cfg.clr.TextBoxSelFG, &cfg.clr.TextBoxSelBG,
        &cfg.clr.TextBoxFrameFG, &cfg.clr.TextBoxFrameBG,
        TextBoxProc
    },
    {   /* ------------- LISTBOX Window class ------------- */
        LISTBOX,
        TEXTBOX,
        &cfg.clr.ListBoxFG, &cfg.clr.ListBoxBG,
        &cfg.clr.ListBoxSelFG, &cfg.clr.ListBoxSelBG,
        &cfg.clr.ListBoxFrameFG, &cfg.clr.ListBoxFrameBG,
        ListBoxProc
    },
    {   /* ------------- EDITBOX Window Class -------------- */
        EDITBOX,
        TEXTBOX,
        &cfg.clr.EditBoxFG, &cfg.clr.EditBoxBG,
        &cfg.clr.EditBoxSelFG, &cfg.clr.EditBoxSelBG,
        &cfg.clr.EditBoxFrameFG, &cfg.clr.EditBoxFrameBG,
        EditBoxProc
    },
    {   /* ------------- MENUBAR Window Class --------------- */
        MENUBAR,
        NORMAL,
        &cfg.clr.MenuBarFG, &cfg.clr.MenuBarBG,
        &cfg.clr.MenuBarSelFG, &cfg.clr.MenuBarSelBG,
        NULL, NULL,
        MenuBarProc,
        VISIBLE
    },
    {   /* ------------- POPDOWNMENU Window Class ----------- */
        POPDOWNMENU,
        LISTBOX,
        &cfg.clr.PopDownFG, &cfg.clr.PopDownBG,
        &cfg.clr.PopDownSelFG, &cfg.clr.PopDownSelBG,
        NULL, NULL,
        PopDownProc,
        SAVESELF | NOCLIP | HASBORDER
    },
    {   /* ----------- BUTTON Window Class --------------- */
        BUTTON,
        TEXTBOX,
        &cfg.clr.ButtonFG, &cfg.clr.ButtonBG,
        &cfg.clr.ButtonSelFG, &cfg.clr.ButtonSelBG,
        NULL, NULL,
        ButtonProc,
        SHADOW
    },
    {   /* ------------- DIALOG Window Class -------------- */
        DIALOG,
        NORMAL,
        &cfg.clr.DialogFG, &cfg.clr.DialogBG,
        NULL, NULL,
        &cfg.clr.DialogFG, &cfg.clr.DialogBG,
        DialogProc,
        SHADOW | MOVEABLE | SAVESELF | CONTROLBOX | HASBORDER
    },
    {   /* ------------ ERRORBOX Window Class ----------- */
        ERRORBOX,
        DIALOG,
        &cfg.clr.ErrorBoxFG, &cfg.clr.ErrorBoxBG,
        NULL, NULL,
        &cfg.clr.ErrorBoxFG, &cfg.clr.ErrorBoxBG,
        DialogProc,
        SHADOW | HASBORDER
    },
    {   /* --------- MESSAGEBOX Window Class ------------- */
        MESSAGEBOX,
        DIALOG,
        &cfg.clr.MessageBoxFG, &cfg.clr.MessageBoxBG,
        NULL, NULL,
        &cfg.clr.MessageBoxFG, &cfg.clr.MessageBoxBG,
        DialogProc,
        SHADOW | HASBORDER
    },
    {   /* ----------- HELPBOX Window Class --------------- */
        HELPBOX,
        DIALOG,
        &cfg.clr.HelpBoxFG, &cfg.clr.HelpBoxBG,
        NULL, NULL,
        &cfg.clr.HelpBoxFG, &cfg.clr.HelpBoxBG,
        DialogProc,
        SHADOW | HASBORDER
    },
    {   /* -------------- DUMMY Window Class ---------------- */
        DUMMY,
        -1,
        &cfg.clr.DummyFG, &cfg.clr.DummyBG,
        NULL, NULL,
        &cfg.clr.DummyFG, &cfg.clr.DummyBG,
        NULL,
        HASBORDER
    }
};

/* ------- return the offset of a class into the class
                 definition table ------ */
int FindClass(CLASS class)
{
    int i;
    for (i = 0; i < sizeof(classdefs) / sizeof(CLASSDEFS); i++)
        if (class == classdefs[i].class)
            return i;
    return 0;
}
```

#### LISTING FIVE]

```c
/* ---------- window.c ------------- */

#include <stdio.h>
#include <conio.h>
#include <stdlib.h>
#include <string.h>
#include "dflat.h"

WINDOW inFocus = NULLWND;

int foreground, background;   /* current video colors */

static void InsertTitle(WINDOW, char *);
static void DisplayTitle(WINDOW, RECT);

/* --------- create a window ------------ */
WINDOW CreateWindow(
    CLASS class,              /* class of this window       */
    char *ttl,                /* title or NULL              */
    int left, int top,        /* upper left coordinates     */
    int height, int width,    /* dimensions                 */
    void *extension,          /* pointer to additional data */
    WINDOW parent,            /* parent of this window      */
    int (*wndproc)(struct window *,enum messages,PARAM,PARAM),int attrib)
                              /* window attribute           */
{
    WINDOW wnd = malloc(sizeof(struct window));
    get_videomode();
    if (wnd != NULLWND)    {
        int base;
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
            /* -- keep upper left within borders of parent -- */
            wnd->rc.lf = max(wnd->rc.lf, GetClientLeft(parent));
            wnd->rc.tp = max(wnd->rc.tp, GetClientTop(parent) +
                                (TestAttribute(parent, HASMENUBAR) ? 1 : 0));
        }
        wnd->class = class;
        wnd->extension = extension;
        wnd->rc.rt = GetLeft(wnd)+width-1;
        wnd->rc.bt = GetTop(wnd)+height-1;
        wnd->ht = height;
        wnd->wd = width;
        wnd->title = ttl;
        if (ttl != NULL)
            InsertTitle(wnd, ttl);
        wnd->next = wnd->prev = wnd->dFocus = NULLWND;
        wnd->parent = parent;
        wnd->videosave = NULL;
        wnd->condition = ISRESTORED;
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
static void InsertTitle(WINDOW wnd, char *ttl)
{
    if ((wnd->title = malloc(strlen(ttl)+1)) != NULL)
        strcpy(wnd->title, ttl);
}

/* ------- write a character to a window at x,y ------- */
void PutWindowChar(WINDOW wnd, int x, int y, int c)
{
    int x1 = GetClientLeft(wnd)+x;
    int y1 = GetClientTop(wnd)+y;

    if (isVisible(wnd))    {
        if (!TestAttribute(wnd, NOCLIP))    {
            WINDOW wnd1 = GetParent(wnd);
            while (wnd1 != NULLWND)    {
                /* --- clip character to parent's borders --- */
                if (x1 < GetClientLeft(wnd1)   ||
                    x1 > GetClientRight(wnd1)  ||
                    y1 > GetClientBottom(wnd1) ||
                    y1 < GetClientTop(wnd1)    ||
                    (y1 < GetTop(wnd1)+2 &&
                            TestAttribute(wnd1, HASMENUBAR)))
                        return;
                wnd1 = GetParent(wnd1);
            }
        }
        if (x1 < SCREENWIDTH && y1 < SCREENHEIGHT)
            wputch(wnd, c, x, y);
    }
}

static char line[161];

/* ----- clip line if it extends below the bottom of the
             parent window ------ */
static int clipbottom(WINDOW wnd, int y)
{
    if (!TestAttribute(wnd, NOCLIP))    {
        WINDOW wnd1 = GetParent(wnd);
        while (wnd1 != NULLWND)    {
            if (GetClientTop(wnd)+y > GetBottom(wnd1))
                return TRUE;
            wnd1 = GetParent(wnd1);
        }
    }
    return GetClientTop(wnd)+y > SCREENHEIGHT;
}

/* ------ clip the portion of a line that extends past the
                     right margin of the parent window ----- */
void clipline(WINDOW wnd, int x, char *ln)
{
    WINDOW pwnd = GetParent(wnd);
    int x1 = strlen(ln);
    int i = 0;

    if (!TestAttribute(wnd, NOCLIP))    {
        while (pwnd != NULLWND)    {
            x1 = GetRight(pwnd) - GetLeft(wnd) - x;
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

/* ------ write a line to video window client area ------ */
void writeline(WINDOW wnd, char *str, int x, int y, int pad)
{
    char wline[120];

    if (TestAttribute(wnd, HASBORDER))    {
        x++;
        y++;
    }
    if (!clipbottom(wnd, y))    {
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

/* -------- display a window's title --------- */
static void DisplayTitle(WINDOW wnd, RECT rc)
{
    int tlen = min(strlen(wnd->title), WindowWidth(wnd)-2);
    int tend = WindowWidth(wnd)-4;

    if (SendMessage(wnd, TITLE, 0, 0))    {
        if (wnd == inFocus)    {
            foreground = cfg.clr.InFocusTitleFG;
            background = cfg.clr.InFocusTitleBG;
        }
        else    {
            foreground = cfg.clr.TitleFG;
            background = cfg.clr.TitleBG;
        }
        memset(line,' ',WindowWidth(wnd)-2);
        if (wnd->condition != ISMINIMIZED)
            strncpy(line + ((WindowWidth(wnd)-2 - tlen) / 2),
                wnd->title, tlen);
        line[WindowWidth(wnd)-2] = '\0';
        if (TestAttribute(wnd, CONTROLBOX))
            line[1] = CONTROLBOXCHAR;
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
        line[RectRight(rc)+1] = '\0';
        writeline(wnd, line+RectLeft(rc),
                    RectLeft(rc), -1, FALSE);
    }
}

/* --- display right border shadow character of a window --- */
static void near shadow_char(WINDOW wnd, int y)
{
    int fg = foreground;
    int bg = background;
    int x = WindowWidth(wnd);
    int c = videochar(GetLeft(wnd)+x, GetTop(wnd)+y+1);

    if (TestAttribute(wnd, SHADOW) == 0)
        return;
    foreground = SHADOWFG;
    background = BLACK;
    PutWindowChar(wnd, x-1, y, c);
    foreground = fg;
    background = bg;
}

/* --- display the bottom border shadow line for a window --- */
static void near shadowline(WINDOW wnd, RECT rc)
{
    int i;
    int y = GetBottom(wnd)+1;
    if ((TestAttribute(wnd, SHADOW)) == 0)
        return;
    if (!clipbottom(wnd, WindowHeight(wnd)))    {
        int fg = foreground;
        int bg = background;
        for (i = 0; i < WindowWidth(wnd); i++)
            line[i] = videochar(GetLeft(wnd)+i+1, y);
        line[i] = '\0';
        foreground = SHADOWFG;
        background = BLACK;
        clipline(wnd, 1, line);
        line[RectRight(rc)+3] = '\0';
        wputs(wnd, line+RectLeft(rc), 1+RectLeft(rc),
            WindowHeight(wnd));
        foreground = fg;
        background = bg;
    }
}

/* ------- display a window's border ----- */
void RepaintBorder(WINDOW wnd, RECT *rcc)
{
    int y;
    int lin, side, ne, nw, se, sw;
    RECT rc, clrc;

    if (!TestAttribute(wnd, HASBORDER))
        return;
    if (rcc == NULL)    {
        rc = SetRect(0, 0, WindowWidth(wnd)-1,
                WindowHeight(wnd)-1);
        if (TestAttribute(wnd, SHADOW))    {
            rc.rt++;
            rc.bt++;
        }
    }
    else
        rc = *rcc;
    clrc = rc;
    /* -------- adjust the client rectangle ------- */
    if (RectLeft(rc) == 0)
        --clrc.rt;
    else
        --clrc.lf;
    if (RectTop(rc) == 0)
        --clrc.bt;
    else
        --clrc.tp;
    RectRight(clrc) = min(RectRight(clrc), WindowWidth(wnd)-3);
    RectBottom(clrc) =
                     min(RectBottom(clrc), WindowHeight(wnd)-3);
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
    if (RectTop(rc) == 0)
        if (TestAttribute(wnd, TITLEBAR))
            DisplayTitle(wnd, clrc);
    foreground = FrameForeground(wnd);
    background = FrameBackground(wnd);
    /* -------- top frame corners --------- */
    if (RectTop(rc) == 0)    {
        if (RectLeft(rc) == 0)
            PutWindowChar(wnd, -1, -1, nw);
        if (RectRight(rc) >= WindowWidth(wnd)-1)
            PutWindowChar(wnd, WindowWidth(wnd)-2, -1, ne);

        if (TestAttribute(wnd, TITLEBAR) == 0)    {
            /* ----------- top line ------------- */
            memset(line,lin,WindowWidth(wnd)-1);
            line[RectRight(clrc)+1] = '\0';
            if (strlen(line+RectLeft(clrc)) > 1 ||
                            TestAttribute(wnd, SHADOW) == 0)
                writeline(wnd, line+RectLeft(clrc),
                        RectLeft(clrc), -1, FALSE);
        }
    }
    /* ----------- window body ------------ */
    for (y = 0; y < ClientHeight(wnd); y++)    {
        int ch;
        if (y >= RectTop(clrc) && y <= RectBottom(clrc))    {
            if (RectLeft(rc) == 0)
                PutWindowChar(wnd, -1, y, side);
            if (RectRight(rc) >= ClientWidth(wnd))    {
                if (TestAttribute(wnd, VSCROLLBAR))
                    ch = (    y == 0 ? UPSCROLLBOX      :
                              y == WindowHeight(wnd)-3  ?
                                   DOWNSCROLLBOX        :
                              y == wnd->VScrollBox      ?
                                   SCROLLBOXCHAR        :
                                   SCROLLBARCHAR );
                else
                    ch = side;
                PutWindowChar(wnd, WindowWidth(wnd)-2, y, ch);
            }
            if (RectRight(rc) == WindowWidth(wnd))
                shadow_char(wnd, y);
        }
    }
    if (RectBottom(rc) >= WindowHeight(wnd)-1)    {
        /* -------- bottom frame corners ---------- */
        if (RectLeft(rc) == 0)
            PutWindowChar(wnd, -1, WindowHeight(wnd)-2, sw);
        if (RectRight(rc) >= WindowWidth(wnd)-1)
            PutWindowChar(wnd, WindowWidth(wnd)-2,
                WindowHeight(wnd)-2, se);
        /* ----------- bottom line ------------- */
        memset(line,lin,WindowWidth(wnd)-1);
        if (TestAttribute(wnd, HSCROLLBAR))    {
            line[0] = LEFTSCROLLBOX;
            line[WindowWidth(wnd)-3] = RIGHTSCROLLBOX;
            memset(line+1, SCROLLBARCHAR, WindowWidth(wnd)-4);
            line[wnd->HScrollBox] = SCROLLBOXCHAR;
        }
        line[RectRight(clrc)+1] = '\0';
        if (strlen(line+RectLeft(clrc)) > 1 ||
                        TestAttribute(wnd, SHADOW) == 0)
            writeline(wnd,
                line+RectLeft(clrc),
                RectLeft(clrc),
                WindowHeight(wnd)-2,
                FALSE);
        if (RectRight(rc) == WindowWidth(wnd))
            shadow_char(wnd, WindowHeight(wnd)-2);
    }
    if (RectBottom(rc) == WindowHeight(wnd))
        /* ---------- bottom shadow ------------- */
        shadowline(wnd, clrc);
}

/* ------ clear the data space of a window -------- */
void ClearWindow(WINDOW wnd, RECT *rcc, int clrchar)
{
    if (isVisible(wnd))    {
        int y;
        RECT rc;

        if (rcc == NULL)
            rc = SetRect(0, 0, ClientWidth(wnd)-1,
                ClientHeight(wnd)-1);
        else
            rc = *rcc;
        SetStandardColor(wnd);
        memset(line, clrchar, RectWidth(rc));
        line[RectWidth(rc)] = '\0';
        for (y = RectTop(rc); y <= RectBottom(rc); y++)
            writeline(wnd, line, RectLeft(rc), y, FALSE);
    }
}

/* -- adjust a window's rectangle to clip it to its parent -- */
static RECT near AdjustRect(WINDOW wnd)
{
    RECT rc = wnd->rc;
    if (TestAttribute(wnd, SHADOW))    {
        RectBottom(rc)++;
        RectRight(rc)++;
    }
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

/* --- get the video memory that is to be used by a window -- */
void GetVideoBuffer(WINDOW wnd)
{
    RECT rc;
    int ht;
    int wd;

    rc = AdjustRect(wnd);
    ht = RectBottom(rc) - RectTop(rc) + 1;
    wd = RectRight(rc) - RectLeft(rc) + 1;
    wnd->videosave = realloc(wnd->videosave, (ht * wd * 2));
    get_videomode();
    if (wnd->videosave != NULL)
        getvideo(rc, wnd->videosave);
}

/* --- restore the video memory that was used by a window --- */
void RestoreVideoBuffer(WINDOW wnd)
{
    if (wnd->videosave != NULL)    {
        RECT rc = AdjustRect(wnd);
        storevideo(rc, wnd->videosave);
        free(wnd->videosave);
        wnd->videosave = NULL;
    }
}

/* ------- compute the logical line length of a window ------ */
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
