
# C PROGRAMMING 9109

## D-Flat i C preprocesor - Al Stivens

D-Flat koristi C preprocesor za većinu konfiguracije programa. Na primer, da bi napravio aplikaciju u kojoj korisnici ne moraju da pomeraju i povećavaju prozore, programer uklanja globalnu definiciju INCLUDE_SISMENU i ponovo kompajlira biblioteku. Da bi dodao iskačući meni na traku menija aplikacije, programer dodaje unos u tabelu u datoteci pod nazivom menus.c i ponovo kompajlira. Datoteka pod nazivom dialogs.c na sličan način upravlja definicijom okvira za dijalog. Opisaću kako funkcionišu meniji i dijaloški okviri, a zatim ću vam reći šta sam uradio sa sistemom klasa.

### Meniji

Vindovs program definiše menije i dijaloge u datotekama resursa. Te datoteke kompajlirate odvojeno pomoću kompajlera resursa i ili ih povežete sa .EKSE datotekom ili ih pročitate u toku izvršavanja. D-Flat program se pokreće sa inicijalizovanim strukturama koje opisuju menije i dijaloške okvire. Predprocesor upravlja inicijalizacijom tih struktura pomoću nekih makroa koji prevode izjave u deklaracije strukture i inicijalizacije. Izjave makro poziva nejasno podsećaju na unose u datoteci resursa za Vindovs. Ideja je da se dodavanje i modifikacija resursa učini što lakšim skrivanjem formata strukture iz koda.

[LISTING_ONE](#listing_one) je "menu.h", datoteka zaglavlja koja definiše makroe sa kojima pravite traku menija i iskačuće menije. Kada razvijate aplikacioni program, generalno možete zanemariti format struktura koje opisuju menije. Postoje funkcije i makroi za postavljanje i testiranje relevantnih informacija, kao što je da li je selekcija aktivna. Važan deo je kako da pozovete makroe koji kreiraju traku menija. [LISTING_TWO](#listing_two) je "menus.c". Sadrži kod za menije koje koristi primer aplikacije MEMOPAD. Pogledajte julsko izdanje na stranici 120 za sliku koja pokazuje kako izgleda traka menija i iskačući meni File.

Traku menija definišete tako što započnete definiciju makroom DEFMENU i završite je makroom ENDMENU. Parametar u makrou DEFMENU imenuje meni. Između dva makroa, definišete svaki iskačući meni koji počinje makroom POPDOVN i završava se makroom ENDPOP-DOVN. Prvi parametar u POPDOVN makrou navodi njegov naslov na traci menija. Naslov je tekst. Znak tilde (~) u tekstu je prefiks koji određuje prečicu do menija. Korisnik pritisne Alt i ovaj taster da iskoči meni pomoću tastature. Drugi parametar u makrou POPDOVN je pokazivač na funkciju koja će se izvršiti neposredno pre nego što iskačući meni iskoči. Ova funkcija može testirati uslove unutar programa i odlučiti da li komande za prebacivanje treba da budu uključene ili isključene i koji izbori menija su aktivni, a koji neaktivni. Između makroa POPDOVN i ENDPOPDOVN nalaze se makroi SELECTION koji definišu izbore. Svaki makro SELECTION ima parametar koji specificira tekst njegovog naslova izbora. Znakovi tilde u tekstu naslova identifikuju tastere za prečice do izbora kada je meni iskačući.

Drugi parametar u makrou SELECTION je kod komande povezan sa izborom menija. Kada korisnik odabere izbor u meniju, D-Flat šalje poruku prozoru aplikacije, roditelju svih prozora dokumenata. Ta poruka je KOMANDNA poruka sa komandnim kodom za izbor menija kao prvim parametrom. Ako prozor aplikacije ne može da obradi poruku i ako prozor dokumenta ima fokus, prozor aplikacije šalje KOMANDNU poruku prozoru dokumenta.

Treći parametar u makrou SELECTION je ili 0 ili šifra tastera za ubrzavanje. Taster za ubrzavanje je pritisak na taster koji će izvršiti komandu sa bilo kog mesta u aplikaciji, bez obzira na to da li je meni iskočio. Komanda Sačuvaj u meniju Datoteke ima Alt+S kao taster za ubrzavanje. Njegovo ime će se prikazati u meniju pored izbora. To možete videti na slici iz julskog broja.

Poslednji parametar u makrou SELECTION je vrednost atributa za izbor. Njegove vrednosti, koje se mogu ILI zajedno, mogu biti NEAKTIVNE, PREKLjUČENE i PROVERENE. Neaktivna selekcija se ne izvršava. Na primer, u programu MEMOPAD, izbori Sačuvaj, Sačuvaj kao i Štampaj u meniju Datoteka su inicijalno neaktivni. Oni nemaju svrhu kada program nema prozor dokumenta u fokusu. Ako izaberete iskačući meni sa prozorom dokumenta u fokusu, prozor aplikacije to primećuje i, iz njegove funkcije PrepFileMenu, koja je usmerena nagore u makrou POPDOVN, prozor aplikacija čini aktivne izbore Sačuvaj, Sačuvaj kao i Štampaj. Ako prozor u fokusu nije dokument, prozor aplikacije čini izbor neaktivnim. Izbor sa atributom TOGGLE ne izvršava komandu. Umesto toga, kada ga izaberete, izbor uključuje i isključuje njegov atribut CHECKED. Kada je atribut uključen, izbor se prikazuje sa kvačicom levo od naslova u meniju. Izbori Insert i Vord Vrap u meniju Opcije su izbori za prebacivanje.

Makro SEPARATOR koji nekoliko menija ima između nekih SELECTION makroa definiše linije za razdvajanje u meniju između izbora.

Traka menija koju opisuje menus.c tipična je za većinu CUA aplikacija. Isti meni biste koristili u bilo kojoj aplikaciji. Ako aplikacija nije uključivala uređivanje teksta, možda nećete koristiti meni Uredi. Kompletnija aplikacija za uređivanje teksta može uključivati meni za pretragu za tekstualne pretrage. Dodali biste druge iskačuće menije za procese koji su jedinstveni za aplikaciju. CUA standard navodi da meni Pogled može biti između menija Uređivanje i Opcije. Meni Prikaz bi omogućio korisnicima da promene način na koji se gledaju informacije u prozorima dokumenta. Na primer, liste podataka mogu imati opcije redosleda sortiranja. Drugi iskačući prozori zavisni od aplikacije bili bi između menija Prikaz i Opcije.

Poslednja stavka u menus.c je definicija sistemskog menija. Definisan je nezavisno od glavnog menija jer se ne pojavljuje sa trake menija. Umesto toga, iskoči iz kontrolne kutije u gornjem levom uglu prozora koji ga koriste. Ima komande koje vam omogućavaju da pomerate, veličinu, minimizirate, maksimizirate, vratite i zatvorite prozor pomoću tastature.

### Dijaloški okviri

Slika 1 u ovom izdanju prikazuje program MEMOPAD sa prikazanim okvirom za dijalog, u ovom slučaju uobičajenim dijalogom File Open. Na ovoj slici vidite mnogo zanimljivih stvari. Okvir za dijalog ima neki statički tekst, okvir za unos teksta u jednom redu, dva okvira sa listom i tri komandna dugmeta. Ako ste ikada pisali Vindovs aplikaciju, videli ste unos teksta u kompajler resursa. D-Flat ima nešto slično, ali umesto specijalnog kompajlera jezika resursa, D-Flat koristi C preprocesor da implementira svoje dijaloške okvire.

[LISTING_THREE](#listing_three) je "dialbox.h", datoteka zaglavlja koja definiše makroe za definicije dijaloga. Kao i kod menija, ne morate da pamtite strukture. Makro jezik koji definiše dijaloške okvire je ono što ćete smatrati važnim. [LISTING_FOUR](#listing_four) je "dialogs.c", izvorna datoteka sa definicijama okvira za dijalog koje koristi aplikacija MEMOPAD.

Definicija dijaloga Otvaranje datoteke pokazuje format za opisivanje okvira za dijalog. Počinjete sa DIALOGBOKS makroom i završavate sa makroom ENDDB. Parametar u DIALOGBOKS makrou je ime strukture koju će program koristiti kada se poziva na polja u okviru za dijalog.

Makro DB_TITLE mora biti prvi makro posle DIALOGBOKS makroa. Definiše naslov okvira za dijalog, poziciju na ekranu i veličinu. Većina dijaloških okvira će imati koordinate ekrana od -1, -1 da bi D-Flat-u rekao da ih centrira. Sledeća dva parametra u makrou DB_TITLE su visina i širina okvira za dijalog.

Sledeći su CONTROL makroi. Svaki od njih definiše kontrolni prozor u okviru za dijalog. Prvi parametar je klasa kontrolne kutije, koja može biti TEKST, EDITBOKS, LISTBOKS, BUTTON, CHECKBOKS i RADIOBUTTON. Okvir za dijalog na slici 1 ima sve osim poslednje dve klase kontrolne kutije. Polje za potvrdu se prikazuje kao [ ] ili [X], u zavisnosti od toga da li je trenutno izabrano ili ne. Radio dugme se prikazuje kao ( ) ili (o).

Drugi parametar u CONTROL makrou je tekstualni niz koji prikazuje kontrolni prozor. Samo kontrole TEKST i BUTTON imaju tekstualne nizove. Ostali imaju NULL kao taj parametar. Niz u kontroli BUTTON je oznaka na dugmetu. String u TEKST kontroli je prikaz statičkog stringa. Obratite pažnju na to da dijalog Otvaranje datoteke ima kontrolni prozor TEKST sa NULL stringom. Ova kontrola prikazuje trenutnu putanju, a program daje vrednost stringa kada postavlja dijaloški okvir.

Sledeća dva CONTROL makro parametra su položaj kolone i reda kontrolnog prozora u odnosu na okvir za dijalog. Nakon toga su visina i širina kontrolnog prozora. Poslednji parametar je komandni kod koji program koristi za ispitivanje i izmenu vrednosti u kontrolnom prozoru.

Kada kontrolni prozor TEKST ima komandni kod, tekst je povezan sa drugim kontrolnim prozorom u okviru za dijalog koji ima isti komandni kod. Tekstualna vrednost kontrolnog prozora TEKST će imati znak tilde kao prefiks za jedan od svojih znakova. Taj znak je prečica za pomeranje fokusa na povezani drugi kontrolni prozor kada je dijaloški okvir prikazan. Na primer, radio dugmad se mogu grupisati prema njihovoj poziciji u okviru za dijalog. Radio dugme mora biti jedno iz grupe, jer kada je jedno izabrano, drugi se poništavaju baš kao i dugmad stanica na radiju u automobilu. Kada neki radio dugmad imaju istu koordinatu kolone i nalaze se u susednim redovima, oni čine grupu.

### Classes

Sa poboljšanjima koja su mi došla tog dana na stalku, možete dodati klase prozora u D-Flat tako što ćete uneti unos u jednu tabelu, obezbediti kodove boja u drugoj i podesiti funkciju obrade prozora. Za početak, postoji classdef.h, koji sam objavio u junu. Definiše niz struktura koje opisuju klase. Novi sistem klasa koristi istu strukturu osim što boje više nisu deo strukture, a promenljiva CLASS koja identifikuje klasu nije potrebna jer je implicirana pozicijom unosa strukture u nizu.

Okosnica sistema klasa je classes.h, [LISTING_FIVE](#listing_five). Sadrži tabelu poziva ClassDef makroa sa stavkama podataka koje opisuju klasu. Prvi parametar je naziv klase. Ovaj identifikator će postati vrednost u nabrojanom tipu podataka za ostatak programa koji će koristiti za referenciranje tipa. Druga stavka podataka je identifikator za osnovnu klasu, onu iz koje je izvedena trenutna. Izvedena klasa nasleđuje svojstva osnovne klase. Treći parametar je pokazivač na modul za obradu prozora za klasu. Poslednji parametar je atribut prozora sa kojim će se otvarati prozori klase.

Druge izvorne datoteke uključuju classdef.h nakon definisanja makroa ClassDef za izdvajanje samo relevantnih stavki podataka koje su potrebne izvornoj datoteci. Na primer, da bi se definisao nabrojani tip podataka CLASS, dflat.h sada ima kod iz Primera 1(a). Kod u Primeru 1(b) gradi niz stringova klasa za evidentiranje poruka i za obezbeđivanje podrazumevane mnemonike prozora pomoći. Ovaj uređaj koristi operator # pretprocesora da napravi string od imena klase. Na primer, unos za klasu TEKSTBOKS izgleda ovako: ClassDef(TEKSTBOKS, NORMAL, TektBokProc, 0). Makro ClassDef, kako je upravo definisano, proširuje makro na ovo: "TEKSTBOKS". Kod u Primeru 1(c) gradi niz struktura koje definišu klasu.

Primer 1: (a) Za definisanje nabrojanog tipa podataka Class, dflat.h sada ima ovaj kod; (b) ovaj kod gradi niz stringova klasa za evidentiranje poruka i da obezbedi podrazumevane mnemonike prozora pomoći; (c) ovaj kod gradi niz struktura koje definišu klasu.

```c
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
```

Postoji značajno poboljšanje performansi u ovom pristupu. Raniji metod je zahtevao pretragu tabele da bi se pronašla odgovarajuća klasa svaki put kada funkcija za obradu prozora pozove svoju osnovnu funkciju za obradu ili se poveže sa podrazumevanom funkcijom obrade za klasu prozora. Sa ovim nizom, pretraga je nepotrebna. Pomak unosa je isti kao vrednost CLASS.

[LISTING_šest, strana 139, je config.c. Ovo zamenjuje raniju verziju jer je metod za definisanje podrazumevanih boja klase drugačiji. Boje klasa su sada niz sa tri dimenzije. Za svaki razred postoji unos. Ako dodate klasu u D-Flat, morate dodati unos u niz boja. Svaka klasa ima četiri seta boja prednjeg plana i pozadine. Postoji standardna boja, boja za izabrane stavke unutar prozora, boja za okvir prozora i boja za istaknute stavke. Istaknuti skup boja za klase MENU BAR, POPDOVNMENU i BUTTON su dve boje prednjeg plana umesto para prednjeg plana i pozadine. Prva boja prednjeg plana je za neaktivne stavke u prozorima, a druga je boja prednjeg plana za prečice. Ti prednji planovi, kada se koriste, kombinuju se sa standardnim bojama pozadine prozora.

Postoje tri grupe D-Flat boja. Prva je grupa boja za monitore u boji. Druga grupa je crno-bela grupa. Treća grupa je obrnuto crno-bela grupa, koja izgleda bolje radi na nekim LCD ekranima.

### Ono što ne možete prethodno obraditi

Moje mišljenje je da je ANSI Ks3J11 komitet promašio kada je koristio par znakova ## za operator lepljenja tokena u pretprocesoru i # za operator "stringize" (pogledajte "Programer's Soapbok"). Pošto su operatori novi u ANSI C-u, komitet je jednostavno mogao da koristi jedan od simbola koje C ne koristi. Dostupni su, na primer, znakovi @ i $.

Možda je komitet reagovao na prethodno stanje u nekim nestandardnim kompajlerima, i možda su korisnici tih kompajlera imali snažan uticaj na komitet. U svom dokumentu sa obrazloženjem, komitet kaže da su uveli operator "stringize" # da bi zamenili praksu u kojoj su neki kompajleri zamenili identifikator u literalu stringa koji odgovara imenu makro argumenta. Kažu da su izmislili ## token paster jer su neki kompajleri podržavali lepljenje tokena zamenjujući / **/ komentar sa nula znakova umesto jednim razmakom, kako navodi K&R. Bez obzira na razlog za operatore, izbor # za njihovu implementaciju znači da C makro ne može uključiti nikakve naredbe pretprocesora u svoju listu zamene. Ne možete napisati makro koji ima #ifdef ili #define u sebi, na primer.

D-Flat ima izvornu datoteku pod nazivom keis.h koja definiše vrednosti koje događaj sa tastature šalje kao poruku pritiska na taster. Taj fajl je bio u izdanju za maj. Druga datoteka je keis.c, [LISTING_Seven, strana 141. Ta datoteka inicijalizuje niz struktura sa unosima koje D-Flat koristi za prikaz imena kombinacija tastera za ubrzavanje u menijima. Svaki unos ima jednu od vrednosti iz keis.h i string koji je prikazano ime ključa. Hteo sam sve ovo da uradim sa makroom na način na koji sam to uradio sa ClassDef makroom opisanim ranije. Na taj način sam mogao da dodajem i brišem ključeve po želji bez potrebe da pravim promene u tabelama u različitim izvornim datotekama. Uključujući samo ključeve koje aplikacija koristi, program ne koristi nepotreban prostor u nizu za neiskorišćena imena kombinacija tastera. Zamislio sam nešto poput KeiDef makroa koji je pozvan kao primer 2(a).

Primer 2: (a) Uključuje KeiDef makro; (b) izgradnju niza struktura koje se koriste u ključevima.c; (c) dobijanje vrednosti definisanih za ključne mnemonike; (d) dobijanje # od strane pretprocesora; (e) declring i inicijalizacija celih brojeva.

```c
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
```

Uključena datoteka, možda nazvana keicaps.h, bi sadržala jedan od ovih poziva za svaki ključ dostupan D-Flat aplikaciji. Programer bi komentarisao one koje aplikaciji nisu potrebne. Datoteka zaglavlja bi se pozivala na različitim mestima, sa makroom Kei-Def definisanim različito u zavisnosti od njegove namene. Na primer, da bih napravio niz struktura koje vidite u keis.c, kodirao bih kao u Primeru 2(b).

To dobro funkcioniše, ali prvo moram da dobijem vrednosti za ključne mnemonike, kao što je ALT_S. Te vrednosti su #defined u keis.h od maja, ali bih više voleo da imam sve svoje definicije ključeva na jednom mestu, pa bih, u ovoj hipotetičkoj metodi, morao negde da koristim kod iz Primera 2(c).

Problem je u tome što C preprocesor pogađa # na početku #define operatora, tretira ga kao operator "stringize", a prethodna obrada odatle ide nizbrdo jer pogrešno interpretirani //define// token nije jedan od parametara makroa. Ako je operator "stringize" bio @, na primer, to ne bi bio problem.

Alternativno rešenje koje prevazilazi pretprocesor je prikazano u Primeru 2(d). Taj niz koda bi se pojavio u datoteci zaglavlja koja je vidljiva svim datotekama koje se odnose na mnemoniku kombinacije tastera. Definisalo bi postojanje spoljašnjih celih brojeva sa mnemotehnikom kao identifikatorima. Da bih deklarisao i inicijalizovao cele brojeve, stavio bih kod iz Primera 2(e) u jednu od C datoteka u eksternom opsegu.

Za sada je dobro. Ali sada nailazimo na još jednu prepreku. D-Flat ima nekoliko mesta gde se statički ili spoljni nizovi inicijalizuju sa ključnim mnemoničkim vrednostima. Takvi inicijalizatori moraju biti konstantne vrednosti, a ne promenljive. Deklaracije celog broja su const, ali C (za razliku od C++) ih tretira kao promenljive i morate da inicijalizujete statičku ili eksternu promenljivu sa istinitom konstantom. Zaobilazno rešenje je inicijalizacija svakog takvog niza sa zadacima. To dodaje runtime kod i masu programu. Odlučio sam da to ne radim.

Možda moje gunđanje nepravedno okrivljuje komitet. Tri kompajlera kompatibilna sa ANSI-jem su se zaglavila na #define unutar makroa, ali da li je to greška kompajlera ili standarda? Ova sledeća sugestija verovatno krši pravila prioriteta za pretprocesiranje, ali izgleda da bi pretprocesor mogao da prepozna #define i druge direktive kao jedinstvene tokene i da ih prethodno obradi bez upadanja u "stringing" nered. Ks3J11, međutim, to nije precizirao, pa je ponašanje kompajlera ispravno, ako ne i idealno.

#### LISTING_ONE

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

#### LISTING_TWO

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

#### LISTING_THREE

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

#### LISTING_FOUR

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

#### LISTING_FIVE

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

#### LISTING_SIX

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

#### LISTING_SEVEN

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
