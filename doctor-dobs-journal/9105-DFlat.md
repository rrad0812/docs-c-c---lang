
# C PROGRAMMING 9105

## D-Flat - Al Stevens

Ovog meseca počinjemo novi projekat kolone za C programiranje, D-Flat C biblioteku koja implementira podskup biblioteke interfejsa IBM SAA Common User Access (CUA) u C programe. Biblioteka je za upotrebu u DOS programima u tekstualnom režimu. Ovaj projekat će trajati nekoliko meseci. Akumulirani izvorni kod sada prelazi 7500 redova, iako ga još nisam pripremio za objavljivanje. Sužavaću margine da bi se uklopile u štampanu stranicu i ubaciću mnogo komentara, tako da će kod sigurno rasti. Svakog meseca ćemo dodavati izvorni kod i na taj način ga možete prikupiti, učeći o tome iz kolone dok budete napredovali. Ako želite, možete preuzeti celu biblioteku izvornog koda sa DDJ foruma na CompuServe-u ili TelePath-u i rano početi da je koristite. Tamo će biti postavljen i mesečni kod, kao i uvek, a dokumentacija će biti tekst kolona kako projekat bude napredovao. Kompletan paket izvornog koda će biti preliminaran i generalno nepodržan, iako ću odgovoriti na sva pitanja o njemu na CompuServe-u. Paket će sadržati kratak opis funkcija i poruka u D-Flat API-ju, kao i neke primere programa koji pokazuju kako biblioteka funkcioniše. Pogledajte sledeći odeljak, „Kako dobiti D-Flat“.

Za početak, objasniću podskup CUA koji D-Flat implementira. D-Flat podržava programski model koji počinje prozorom aplikacije. Prozor aplikacije može biti bilo koje razumne veličine. Ima naslovnu traku i traku menija. Traka menija sadrži iskačuće menije. D-Flat aplikacija može otvoriti druge prozore koji su podređeni aplikaciji ili jedan drugom podređeni. Prozori mogu biti okviri za tekst, okviri sa listom i okviri za uređivanje. Vaš korisnik može da pomera i menja veličinu ovih prozora i prozora aplikacije pomoću miša ili tastature. Ove operacije prate konvencije CUA standarda, tako da će korisnički interfejs vaše aplikacije podsećati na Vindovs, Presentation Manager, TurboVision i druge. D-Flat podržava standardni skup CUA padajućih menija: File, Edit, Options, Vindov, Help i Sistem meni za pomeranje, dimenzionisanje, minimiziranje i maksimiziranje prozora sa tastature. Vaša aplikacija takođe može da dodaje svoje menije. D-Flat takođe ima klipbord i dijaloške okvire. Okvir za dijalog je prozor koji sadrži polja za unos podataka. Ova polja mogu biti okviri za uređivanje, dugmad, radio dugmad i okviri za listu. D-Flat biblioteka uključuje dijaloške okvire za CUA standardne dijaloge za otvaranje i čuvanje datoteke. D-Flat ne koristi datoteke resursa i kompajler resursa da bi opisao menije i dijaloške okvire na način na koji to radi Vindovs SDK. Umesto toga, vi kodirate svoje menije i dijaloške okvire u formatu sličnom onima koje koriste datoteke resursa SDK, a skup makroa C preprocesora kompajlira menije i dijaloške okvire u strukture koje D-Flat softver očekuje.

Za potpunu definiciju CUA, možete dobiti tom CG26-4582 iz IBM Sistems Application Architecture biblioteke. Sveska je naslovljena, Common User Access: Advanced Interface Design Guide. Dobio sam svoj primerak zajedno sa kompletom za razvoj softvera za Microsoft Vindovs 3.0. Čini se da je to definitivan opis CUA, iako je verovatno izveden iz OS/2 Presentation Manager-a. Naći ćete neka suptilna odstupanja od CUA standarda u Vindovs 3.0 i drugim Microsoft proizvodima. Interfejs Borland Turbo Debugger 2.0 donekle prati CUA, a TurboVision funkcije Turbo Paskala implementiraju neke od CUA za Pascal programere.

Razvio sam ovu biblioteku za korišćenje u projektu aplikacije. Tražio sam biblioteku usaglašenu sa CUA-om koja je bila zasnovana na tekstu i koja je nudila adekvatne performanse za rad na najnižim laptopovima, onima sa malo prostora na disku i sporim diskovima i procesorima. Pronašao sam biblioteke za brže mašine (MEVEL, na primer) i biblioteke C++ klasa (Cink, na primer), ali ništa što bi odgovaralo mojim potrebama za razvoj programa na jeziku C namenjenih nižim platformama. Kao rezultat toga, pokrenuo sam ovaj projekat i odlučio da ga objavim kao seriju za kolumnu. Do sada se kod kompajlira sa Turbo C 2.0 i Microsoft C 6.0. Zadržaću se na ova dva neko vreme i ukazati na nekoliko mesta gde je kod zavisan od kompajlera, tako da možete razmotriti portove za druge kompajlere ako želite.

Ovu kolonu „C programiranje“ pišem sa jednostavnom D-Flat aplikacijom, uređivačem teksta nalik na beležnicu sa više dokumenata. Ovaj program će biti jedan od primera koje ćemo kasnije koristiti. Koristi većinu CUA karakteristika D-Flat-a - prozore za uređivanje, dijaloške okvire, padajuće menije, međuspremnik, itd. Takođe mi pomaže da otklonim greške u D-Flat biblioteci.

### D-Flat kod niskog nivoa

Kao i njegov muzički imenjak, D-Flat nije najlakši ključ za sviranje. On koristi zastrašujuću arhitekturu zasnovanu na porukama, vođenu događajima, sličnu onoj koja tera programere da žure da izbegnu Vindovs. Ali uz vežbu možete da igrate „Bodi and Soul” u „petu”, a sa marljivošću možete savladati i iskoristiti prednosti ovog alternativnog načina pisanja programa. Kao što ćete videti u mesecima koji dolaze, programiranje zasnovano na događajima nije toliko zastrašujuće.

Moramo prvo da sklonimo neki kod zavisan od platforme. Ovaj mesec je posvećen C datotekama koje povezuju ostatak biblioteke sa arhitekturom računara. Ako želite da premestite D-Flat na drugi sistem, promenili biste ovaj kod, koji obezbeđuje interfejse za tastaturu, miš i ekran.

[LISTING_ONE](#listing_one), je "dflat.h", zaglavni fajl koji uključuju sve D-Flat aplikacije i većina izvornih datoteka D-Flat biblioteke. Počinje uključivanjem nekoliko drugih datoteka zaglavlja, od kojih se četiri -- sistem.h, keis.h, rect.h i video.h -- pojavljuju u koloni ovog meseca, a ostale ćete videti sledećeg meseca. Ako želite da obavite kompajliranje i testiranje drajvera niskog nivoa sa ovomesečnim kodom, možete da isključite uključene datoteke koje još uvek nemate.

Pregledanje dflat.h će vam dati pregled onoga što D-Flat podržava. Prva stavka od interesa je definicija standardnih klasa prozora u nabrojanom tipu podataka CLASS. Iz njihovih naslova -- TEKSTBOKS, RADIOBUTTON, i tako dalje, dobijate unapred upozorenje o tome šta dolazi. Struktura VINDOV u dflat.h je kontrolna struktura koja prati prozore koje vaša aplikacija otvara. Brojni makroi i prototipovi definišu D-Flat API. Postoje neke spoljne definicije podataka, a zatim i makroi koji definišu granične znakove prozora. Ovi znakovi koriste tekstualni grafički skup znakova tekstualnog video sistema računara. Znakovi sa FOCUS_ prefiksom definišu granice za prozor koji je „u fokusu“, na primer onaj u koji korisnik trenutno unosi podatke. Ostali definišu granične znakove za druge prozore.

D-Flat podržava trake za pomeranje. Znakovi trake za pomeranje u dflat.h definišu znakove koje D-Flat koristi za slikanje trake za pomeranje i okvira za pomeranje i dugmadi. Oznaka za potvrdu je za stavke menija koje se prebacuju. Prikazuje se pored preklopnog menija kao potvrdni znak kada je stavka uključena.

D-Flat naslovna traka može da sadrži kontrolnu kutiju, min/maks kutiju i karakter za vraćanje prozora. Znakovi naslovne trake u dflat.h definišu ove vrednosti.

Kontrolni karakteri teksta u dflat.h definišu kontrolne bajtove koje D-Flat koristi za upravljanje bojama kada prikazuje sadržaj tekstualnog prozora. O njima ćete saznati u kasnijim kolumnama.

Makroi i prototipovi na kraju dflat.h definišu spoljne funkcije koje koristi vaša aplikacija ili sam D-Flat API. Objasniću svaki od njih kada se bavim temom na koju se stavka odnosi.

[LISTING_TWO](#listing_two), strana 142, je keis.h, datoteka zaglavlja koja definiše vrednosti ključeva za D-Flat. Vaša aplikacija bi koristila ove vrednosti da identifikuje da je određeni taster pritisnut. D-Flat je arhitektura zasnovana na porukama zasnovana na događajima. Kasnije ćete naučiti kako da napravite funkcije za obradu prozora koje reaguju na događaje i poruke. Pritisak na taster bi bio događaj koji šalje poruku prozoru koji je u fokusu. Vaša funkcija za obradu prozora bi primila poruku zajedno sa pritisnutim tasterom kao parametar. Vrednosti u keis.h će biti od interesa kada dođemo do tog dela diskusije o sistemu.

[LISTING_THREE](#listing_three), je "sistem.h", datoteka zaglavlja koja definiše brojne globalne vrednosti i prototipove koji se povezuju sa hardverom i njegovim drajverima. Vaši aplikativni programi se obično neće baviti ovim vrednostima, ali hardverski zavisne funkcije D-Flat-a hoće. Postoje vrednosti za vektore prekida, dimenzije ekrana, funkcije BIOS-a i prototipove za funkcije miša, kursora i tajmera. Postoje makroi koji čine kod kompatibilnim sa Microsoft C. Turbo C i Microsoft C kompajleri drugačije implementiraju funkcije DOS interfejsa. Pošto sam koristio Turbo C za razvoj D-Flat-a, morao sam da razvijem makroe za kompatibilnost koji prevode Turbo C implementacije u Microsoft C konvencije. Ovi makroi i nekoliko drugih mesta u kodu gde ćete pronaći #ifdef MSC naredbu su mesta gde postoji kod zavisan od kompajlera. Ako prebacite D-Flat na drugi kompajler, ovo su mesta koja zahtevaju vašu pažnju.

[LISTING_FOUR](#listing_four) i [LISTING_FIVE](#listing_five) su "rect.h" i "video.h". Prvi definiše neke makroe i prototipove koje D-Flat koristi za obradu pravougaonika ekrana. Drugi definiše interfejs za video drajvere.

[LISTING_6](#listing_six), je "video.c", koji sadrži D-Flat video drajvere. Funkcije getvideo i storevideo čitaju i pišu pravougaonike video memorije iz i u RAM bafere. Ove funkcije podržavaju čuvanje i vraćanje video prostora koji će zauzeti privremeni prozor. Ne koriste svi prozori ovu tehniku, ali neki koriste. Funkcije GetVideoChar i PutVideoChar čitaju i pišu video karakter i bajt njegovog atributa iz i u video memoriju. Funkcija vputch upisuje jedan znak na poziciju unutar određenog prozora i koristi trenutno uspostavljene vrednosti boja. Funkcija vputs na sličan način upisuje string u prozor. Funkcija get_videomode određuje trenutni video režim i adresu video memorije. Imajte na umu da ove funkcije ne brinu o karakteristikama snega videa starog IBM Color Graphics Adaptor-a. Ako je to problem, morate preći na asemblerski jezik da biste ga rešili. Rešenje je često objavljivano u prošlosti. U kasnijoj kolumni će se raspravljati o tehnici ako mi dovoljno vas kaže da je to opravdano.

[LISTING_SEVEN](#listing_seven) je "console.c", koji sadrži kod koji upravlja tastaturom, kursorom i generisanjem zujanja upozorenja. Funkcija bita tastera je od interesa. Određuje da li je taster pritisnut korišćenjem BIOS poziva. Mogao sam da koristim funkcije Turbo C bioskei i MSC_dos_bioskei, ali obe imaju grešku koja postoji od kada su prvi put predstavljene. Ako uđete u petlju čekajući da vam jedna od ovih funkcija kaže da je taster pritisnut, a korisnik pritisne Ctrl-Break, nikada nećete izaći iz petlje. Prijavio sam ovu grešku oba dobavljača kompajlera pre mnogo godina, ali nijedan nije smatrao potrebnim da izvrši popravku. Oba kompajlera podržavaju kbhit funkciju, ali ta funkcija koristi DOS poziv, nešto što pokušavam da izbegnem. Funkcije koje koriste DOS za I/O konzole će se srušiti ako se izvrše iz TSR-a. Kao alternativu, koristim kod koji vidite u console.c kada kompajliram sa Turbo C-om. MSC ne pruža način da testiram nultu zastavicu nakon poziva prekida osim korišćenjem asemblerskog jezika, a ja to ne želim da radim. Stoga, MSC D-Flat implementacija zamenjuje kbhit za keihit i nije pogodna za upotrebu u TSR-u.

Funkcija getkei u console.c čita taster sa tastature. Ako je ključ funkcijski taster ili vrednost tastera Alt, funkcija getkei za njega gradi jedinstvenu 8-bitnu vrednost tako da funkcije koje pozivaju ne moraju da prevedu 16-bitnu vrednost koda za skeniranje koju BIOS vraća.

Funkcija zvučnog signala čuje kratko vreme. Posmatrajte implementaciju makroa čekanja. Izgleda nešto kao C++ inline funkcija.

Funkcije kursora u console.c koriste BIOS da pozicioniraju kursor tastature, pročitaju njegovu trenutnu poziciju, promene njegov oblik, sačuvaju i vrate njegovu konfiguraciju i sakriju i otkriju je.

[LISTING_EIGHT](#listing_eight) je "mouse.c", koji sadrži kod koji upravlja mišem. Ove funkcije su jednostavni pozivi vektoru prekida miša sa odgovarajućim vrednostima u registrima. Generička funkcija miša vrši poziv. Druge funkcije ga pozivaju da koristi API drajvera miša. Pre nego što vaš program može da koristi miš, morate imati instaliran drajver za miš. API za upravljačke programe za miš prati standard koji je uspostavio Microsoft miš, tako da ovaj kod radi za većinu miševa. Funkcija mouse_installed testira da li je drajver miša instaliran kao drajver DOS uređaja ili kao TSR gledajući sadržaj vektora prekida miša. Ako sadrži nulu ili ako ukazuje na IRET instrukciju, drajver nije tamo. Komentari koji prethode svakoj od ostalih funkcija miša objašnjavaju šta one rade.

Kod ovog meseca je osnova za D-Flat. Možete ga koristiti za pisanje drugih programa ako želite, ali njegova svrha je da podrži D-Flat operativno okruženje. Sledećeg meseca ćemo objaviti ostale datoteke zaglavlja i ući u funkcije koje kreiraju prozore i upravljaju porukama.

### Nema kraja Hafmanu

Nekoliko vas je pisalo da ukaže na grešku u Hafmanovom kodu kompresije koji sam objavio u februarskom izdanju 1991. godine. Čini se da je dekomprimovani fajl imao loš poslednji bajt. Neki od vas su čak poslali ispravke koda. Funkcija koja je pomerila bitove u komprimovanu datoteku nije pomerila naniže poslednji komprimovani bajt pre nego što ga je ispisala. Očigledno su dve datoteke koje sam koristio da testiram program bile, slučajno, prave veličine da izbegnem grešku. Kvote osam prema jedan je teško pobediti, posebno dva puta. Zamenite izlaznu funkciju u huffc.c kodom iz Primera 1.

```c
Example 1: Replacement code for the huffc.c outbit function in my February 1991 column.

  /* -- collect and write bits to the
          compressed output file -- */
  static void outbit (FILE *fo, int bit)
  {
      if (ct8 == 8 || bit == -1)  {
          while (ct8 < 8)    {
              out8 <<= 1;
              ct8++;
          }
          fputc(out8, fo);
          ct8 = 0;
      }
      out8 = (out8 << 1) | bit;
      ct8++;
  }
```

#### LISTING_ONE

```c
/* ------------- dflat.h ----------- */

#ifndef WINDOW_H
#define WINDOW_H

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
typedef enum window_class    {
    NORMAL,
    APPLICATION,
    TEXTBOX,
    LISTBOX,
    EDITBOX,
    MENUBAR,
    POPDOWNMENU,
    BUTTON,
    DIALOG,
    ERRORBOX,
    MESSAGEBOX,
    HELPBOX,
    TEXT,
    RADIOBUTTON,
    DUMMY
} CLASS;
typedef struct window {
    CLASS class;           /* window class                  */
    char *title;           /* window title                  */
    struct window *parent; /* parent window                 */
    int (*wndproc)
        (struct window *, enum messages, PARAM, PARAM);
    /* ---------------- window dimensions ----------------- */
    RECT rc;               /* window coordinates
                                            (0/0 to 79/24)  */
    int ht, wd;            /* window height and width       */
    RECT RestoredRC;       /* restored condition rect       */
    /* -------------- linked list pointers ---------------- */
    struct window *next;        /* next window on screen    */
    struct window *prev;        /* previous window on screen*/
    struct window *nextbuilt;   /* next window built        */
    struct window *prevbuilt;   /* previous window built    */

    int attrib;                 /* Window attributes        */
    char *videosave;            /* video save buffer        */
    int condition;              /* Restored, Maximized,
                                           Minimized        */
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
    /* ------------------ list box field ------------------ */
    int selection;  /* current selection                    */
    /* ----------------- edit box fields ------------------ */
    int CurrCol;    /* Current column                       */
    char *CurrLine; /* Current line                         */
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
/* ------- window methods ----------- */
#define WindowHeight(w)      ((w)->ht)
#define WindowWidth(w)       ((w)->wd)
#define BorderAdj(w,n)       (TestAttribute(w,HASBORDER)?n:0)
#define ClientWidth(w)       (WindowWidth(w)-BorderAdj(w,2))
#define ClientHeight(w)      (WindowHeight(w)-BorderAdj(w,2))
#define WindowRect(w)        ((w)->rc)
#define GetTop(w)            (RectTop(WindowRect(w)))
#define GetBottom(w)         (RectBottom(WindowRect(w)))
#define GetLeft(w)           (RectLeft(WindowRect(w)))
#define GetRight(w)          (RectRight(WindowRect(w)))
#define GetClientTop(w)      (GetTop(w)+BorderAdj(w,1))
#define GetClientBottom(w)   (GetBottom(w)-BorderAdj(w,1))
#define GetClientLeft(w)     (GetLeft(w)+BorderAdj(w,1))
#define GetClientRight(w)    (GetRight(w)-BorderAdj(w,1))
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
void RepaintBorder(WINDOW, RECT *);
void ClearWindow(WINDOW, RECT *, int);
void clipline(WINDOW, int, char *);
void writeline(WINDOW, char *, int, int, int);
void writefull(WINDOW, char *, int);
void SetNextFocus(WINDOW,int);
void PutWindowChar(WINDOW, int, int, int);
void GetVideoBuffer(WINDOW);
void RestoreVideoBuffer(WINDOW);
int LineLength(char *);
#define DisplayBorder(wnd) RepaintBorder(wnd, NULL)
#define DefaultWndProc(wnd,msg,p1,p2)    \
    classdefs[FindClass(wnd->class)].wndproc(wnd,msg,p1,p2)
#define BaseWndProc(class,wnd,msg,p1,p2)    \
    classdefs[DerivedClass(class)].wndproc(wnd,msg,p1,p2)
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
#define SHADOWFG       DARKGRAY
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
char *TextLine(WINDOW, int);
void WriteTextLine(WINDOW, RECT *, int, int);
void SetTextBlock(WINDOW, int, int, int, int);
#define BlockMarked(wnd) (  wnd->BlkBegLine ||    \
                            wnd->BlkEndLine ||    \
                            wnd->BlkBegCol  ||    \
                            wnd->BlkEndCol)
#define ClearBlock(wnd) wnd->BlkBegLine = wnd->BlkEndLine =  \
                        wnd->BlkBegCol  = wnd->BlkEndCol = 0;
#define GetText(w)        ((w)->text)
/* --------- menu prototypes ---------- */
int CopyCommand(char *, char *, int, int);
void PrepOptionsMenu(void *, struct Menu *);
void PrepEditMenu(void *, struct Menu *);
void PrepWindowMenu(void *, struct Menu *);
void BuildSystemMenu(WINDOW);
/* ------------- edit box prototypes ----------- */
#define isMultiLine(wnd)     TestAttribute(wnd, MULTILINE)
/* --------- message box prototypes -------- */
void MessageBox(char *, char *);
void ErrorMessage(char *);
int TestErrorMessage(char *);
int YesNoBox(char *);
int MsgHeight(char *);
int MsgWidth(char *);
/* ------------- dialog box prototypes -------------- */
int DialogBox(DBOX *, int (*)(struct window *,
                        enum messages, PARAM, PARAM));
int DlgOpenFile(char *, char *);
int DlgSaveAs(char *);
void GetDlgListText(WINDOW, char *, enum commands);
int DlgDirList(WINDOW, char *, enum commands,
                            enum commands, unsigned);
int RadioButtonSetting(DBOX *, enum commands);
void PushRadioButton(DBOX *, enum commands);
void PutItemText(WINDOW, enum commands, char *);
void GetItemText(WINDOW, enum commands, char *, int);
/* ------------- help box prototypes ------------- */
void HelpFunction(void);
void LoadHelpFile(void);
#define swap(a,b){int x=a;a=b;b=x;}

#endif
```

#### LISTING_TWO

```c
/* ----------- keys.h ------------ */
#ifndef KEYS_H
#define KEYS_H
#define RUBOUT        8
#define BELL          7
#define ESC          27
#define ALT_BS      197
#define SHIFT_DEL   198
#define CTRL_INS    186
#define SHIFT_INS   185
#define F1          187
#define F2          188
#define F3          189
#define F4          190
#define F5          191
#define F6          192
#define F7          193
#define F8          194
#define F9          195
#define F10         196
#define CTRL_F1     222
#define CTRL_F2     223
#define CTRL_F3     224
#define CTRL_F4     225
#define CTRL_F5     226
#define CTRL_F6     227
#define CTRL_F7     228
#define CTRL_F8     229
#define CTRL_F9     230
#define CTRL_F10    231
#define ALT_F1      232
#define ALT_F2      233
#define ALT_F3      234
#define ALT_F4      235
#define ALT_F5      236
#define ALT_F6      237
#define ALT_F7      238
#define ALT_F8      239
#define ALT_F9      240
#define ALT_F10     241
#define HOME        199
#define UP          200
#define PGUP        201
#define BS          203
#define FWD         205
#define END         207
#define DN          208
#define PGDN        209
#define INS         210
#define DEL         211
#define CTRL_HOME   247
#define CTRL_PGUP   132
#define CTRL_BS     243
#define CTRL_FIVE   143
#define CTRL_FWD    244
#define CTRL_END    245
#define CTRL_PGDN   246
#define SHIFT_HT    143
#define ALT_A       158
#define ALT_B       176
#define ALT_C       174
#define ALT_D       160
#define ALT_E       146
#define ALT_F       161
#define ALT_G       162
#define ALT_H       163
#define ALT_I       151
#define ALT_J       164
#define ALT_K       165
#define ALT_L       166
#define ALT_M       178
#define ALT_N       177
#define ALT_O       152
#define ALT_P       153
#define ALT_Q       144
#define ALT_R       147
#define ALT_S       159
#define ALT_T       148
#define ALT_U       150
#define ALT_V       175
#define ALT_W       145
#define ALT_X       173
#define ALT_Y       149
#define ALT_Z       172
#define ALT_1      0xf8
#define ALT_2      0xf9
#define ALT_3      0xfa
#define ALT_4      0xfb
#define ALT_5      0xfc
#define ALT_6      0xfd
#define ALT_7      0xfe
#define ALT_8      0xff
#define ALT_9      0x80
#define ALT_0      0x81
#define ALT_HYPHEN  130

#define RIGHTSHIFT 0x01
#define LEFTSHIFT  0x02
#define CTRLKEY    0x04
#define ALTKEY     0x08
#define SCROLLLOCK 0x10
#define NUMLOCK    0x20
#define CAPSLOCK   0x40
#define INSERTKEY  0x80

struct keys {
    int keycode;
    char *keylabel;
};
int getkey(void);
int getshift(void);
int keyhit(void);
void beep(void);
extern struct keys keys[];
extern char altconvert[];

#endif
```

#### LISTING_THREE

```c
/* --------------- system.h -------------- */
#ifndef SYSTEM_H
#define SYSTEM_H
/* ----- interrupt vectors ----- */
#define TIMER  8
#define VIDEO  0x10
#define KEYBRD 0x16
#define DOS    0x21
#define CRIT   0x24
#define MOUSE  0x33
/* ------- platform-dependent values ------ */
#define FREQUENCY 100
#define COUNT (1193280L / FREQUENCY)
#define ZEROFLAG 0x40
#define MAXSAVES 50
#define SCREENWIDTH  80
#define SCREENHEIGHT 25
/* ----- keyboard BIOS (0x16) functions -------- */
#define READKB 0
#define KBSTAT 1
/* ------- video BIOS (0x10) functions --------- */
#define SETCURSORTYPE 1
#define SETCURSOR     2
#define READCURSOR    3
#define READATTRCHAR  8
#define WRITEATTRCHAR 9
#define HIDECURSOR 0x20
/* ------- the interrupt function registers -------- */
typedef struct {
    int bp,di,si,ds,es,dx,cx,bx,ax,ip,cs,fl;
} IREGS;
/* ---------- cursor prototypes -------- */
void curr_cursor(int *x, int *y);
void cursor(int x, int y);
void hidecursor(void);
void unhidecursor(void);
void savecursor(void);
void restorecursor(void);
void normalcursor(void);
void set_cursor_type(unsigned t);
void videomode(void);
/* ---------- mouse prototypes ---------- */
int mouse_installed(void);
int mousebuttons(void);
void get_mouseposition(int *x, int *y);
void set_mouseposition(int x, int y);
void show_mousecursor(void);
void hide_mousecursor(void);
int button_releases(void);
void resetmouse(void);
#define leftbutton()     (mousebuttons()&1)
#define rightbutton()     (mousebuttons()&2)
#define waitformouse()     while(mousebuttons());
/* ------------ timer macros -------------- */
#define timed_out(timer)         (timer==0)
#define set_timer(timer, secs)     timer=(secs)*182/10+1
#define disable_timer(timer)     timer = -1
#define timer_running(timer)     (timer > 0)
#define countdown(timer)         --timer
#define timer_disabled(timer)     (timer == -1)

#ifdef MSC
/* ============= MSC Compatibility Macros ============ */
#define BLACK         0
#define BLUE          1
#define GREEN         2
#define CYAN          3
#define RED           4
#define MAGENTA       5
#define BROWN         6
#define LIGHTGRAY     7
#define DARKGRAY      8
#define LIGHTBLUE     9
#define LIGHTGREEN   10
#define LIGHTCYAN    11
#define LIGHTRED     12
#define LIGHTMAGENTA 13
#define YELLOW       14
#define WHITE        15

#define getvect(v)   _dos_getvect(v)
#define setvect(v,f) _dos_setvect(v,f)
#define MK_FP(s,o)   ((void far *) \
               (((unsigned long)(s) << 16) | (unsigned)(o)))
#undef FP_OFF
#undef FP_SEG
#define FP_OFF(p)    ((unsigned)(p))
#define FP_SEG(p)    ((unsigned)((unsigned long)(p) >> 16))
#define poke(a,b,c)  (*((int  far*)MK_FP((a),(b))) = (int)(c))
#define pokeb(a,b,c) (*((char far*)MK_FP((a),(b))) = (char)(c))
#define peek(a,b)    (*((int  far*)MK_FP((a),(b))))
#define peekb(a,b)   (*((char far*)MK_FP((a),(b))))
#define findfirst(p,f,a) _dos_findfirst(p,a,f)
#define findnext(f)      _dos_findnext(f)
#define ffblk            find_t
#define ff_name          name
#define ff_fsize         size
#define ff_attrib        attrib
#define fnsplit          _splitpath
#define fnmerge          _makepath
#define EXTENSION         2
#define FILENAME          4
#define DIRECTORY         8
#define DRIVE            16
#define MAXPATH          80
#define MAXDRIVE          3
#define MAXDIR           66
#define MAXFILE           9
#define MAXEXT            5
#define setdisk(d)       _dos_setdrive((d)+1, NULL)
#define bioskey          _bios_keybrd
#define keyhit           kbhit
#endif
#endif
```

#### LISTING_FOUR

```c
/* ----------- rect.h ------------ */
#ifndef RECT_H
#define RECT_H

typedef struct    {
    int lf,tp,rt,bt;
} RECT;
#define within(p,v1,v2)   ((p)>=(v1)&&(p)<=(v2))
#define RectTop(r)        (r.tp)
#define RectBottom(r)     (r.bt)
#define RectLeft(r)       (r.lf)
#define RectRight(r)      (r.rt)
#define InsideRect(x,y,r) (within(x,RectLeft(r),RectRight(r)) \
                               &&                             \
                          within(y,RectTop(r),RectBottom(r)))
#define ValidRect(r)      (RectRight(r) || RectLeft(r))
#define RectWidth(r)      (RectRight(r)-RectLeft(r)+1)
#define RectHeight(r)     (RectBottom(r)-RectTop(r)+1)
RECT subRectangle(RECT, RECT);
RECT RelativeRectangle(RECT, RECT);
RECT ClientRect(void *);
RECT SetRect(int,int,int,int);
#endif
```

#### LISTING_FIVE

```c
/* ---------------- video.h ----------------- */

#ifndef VIDEO_H
#define VIDEO_H

#include "rect.h"

void getvideo(RECT, void far *);
void storevideo(RECT, void far *);
extern unsigned video_mode;
extern unsigned video_page;
void wputch(WINDOW, int, int, int);
int GetVideoChar(int, int);
void PutVideoChar(int, int, int);
void get_videomode(void);
void wputs(WINDOW, void *, int, int);

#define clr(fg,bg) ((fg)|((bg)<<4))
#define vad(x,y) ((y)*160+(x)*2)
#define ismono() (video_mode == 7)
#define istext() (video_mode < 4)
#define videochar(x,y) (GetVideoChar(x,y) & 255)

#endif
```

#### LISTING_SIX

```c
/* --------------------- video.c -------------------- */
#include <stdio.h>
#include <dos.h>
#include <string.h>
#include <conio.h>
#include "dflat.h"

static unsigned video_address;
/* -- read a rectangle of video memory into a save buffer -- */
void getvideo(RECT rc, void far *bf)
{
    int ht = RectBottom(rc)-RectTop(rc)+1;
    int bytes_row = (RectRight(rc)-RectLeft(rc)+1) * 2;
    unsigned vadr = vad(RectLeft(rc), RectTop(rc));
    hide_mousecursor();
    while (ht--)    {
        movedata(video_address, vadr, FP_SEG(bf),
                FP_OFF(bf), bytes_row);
        vadr += 160;
        (char far *)bf += bytes_row;
    }
    show_mousecursor();
}

/* -- write a rectangle of video memory from a save buffer -- */
void storevideo(RECT rc, void far *bf)
{
    int ht = RectBottom(rc)-RectTop(rc)+1;
    int bytes_row = (RectRight(rc)-RectLeft(rc)+1) * 2;
    unsigned vadr = vad(RectLeft(rc), RectTop(rc));
    hide_mousecursor();
    while (ht--)    {
        movedata(FP_SEG(bf), FP_OFF(bf), video_address,
                vadr, bytes_row);
        vadr += 160;
        (char far *)bf += bytes_row;
    }
    show_mousecursor();
}

/* -------- read a character of video memory ------- */
int GetVideoChar(int x, int y)
{
    int c;
    hide_mousecursor();
    c = peek(video_address, vad(x,y));
    show_mousecursor();
    return c;
}

/* -------- write a character of video memory ------- */
void PutVideoChar(int x, int y, int c)
{
    if (x < SCREENWIDTH && y < SCREENHEIGHT)    {
        hide_mousecursor();
        poke(video_address, vad(x,y), c);
        show_mousecursor();
    }
}

/* -------- write a character to a window ------- */
void wputch(WINDOW wnd, int c, int x, int y)
{
    int x1 = GetClientLeft(wnd)+x;
    int y1 = GetClientTop(wnd)+y;
    if (x1 < SCREENWIDTH && y1 < SCREENHEIGHT)    {
        hide_mousecursor();
        poke(video_address,
            vad(x1,y1),(c & 255) |
                (clr(foreground, background) << 8));
        show_mousecursor();
    }
}

/* ------- write a string to a window ---------- */
void wputs(WINDOW wnd, void *s, int x, int y)
{
    int x1 = GetLeft(wnd)+x;
    int y1 = GetTop(wnd)+y;
    if (x1 < SCREENWIDTH && y1 < SCREENHEIGHT)    {
        int fg = foreground;
        int bg = background;
        unsigned char *str = s;
        char ss[200];
        int ln[SCREENWIDTH];
        int *cp1 = ln;
        int len;
        strncpy(ss, s, 199);
        ss[199] = '\0';
        clipline(wnd, x, ss);
        str = (unsigned char *) ss;
        hide_mousecursor();
        while (*str)    {
            if (*str == CHANGECOLOR)    {
                str++;
                foreground = (*str++) & 0x7f;
                background = (*str++) & 0x7f;
                continue;
            }
            if (*str == RESETCOLOR)    {
                foreground = fg;
                background = bg;
                str++;
                continue;
            }
            *cp1++ = (*str & 255) |
                (clr(foreground, background) << 8);
            str++;
        }
        foreground = fg;
        background = bg;
        len = (int)(cp1-ln);
        if (x1+len > SCREENWIDTH)
            len = SCREENWIDTH-x1;
        movedata(FP_SEG(ln), FP_OFF(ln), video_address,
            vad(x1,y1), len*2);
        show_mousecursor();
    }
}

/* --------- get the current video mode -------- */
void get_videomode(void)
{
    videomode();
    /* ---- Monochrome Display Adaptor or text mode ---- */
    if (ismono())
        video_address = 0xb000;
    else
        /* ------ Text mode -------- */
        video_address = 0xb800 + video_page;
}
```

#### LISTING_SEVEN

```c
/* ----------- console.c ---------- */

#include <conio.h>
#include <bios.h>
#include <dos.h>
#include "system.h"
#include "keys.h"

/* ----- table of alt keys for finding shortcut keys ----- */
char altconvert[] = {
    ALT_A,ALT_B,ALT_C,ALT_D,ALT_E,ALT_F,ALT_G,ALT_H,
    ALT_I,ALT_J,ALT_K,ALT_L,ALT_M,ALT_N,ALT_O,ALT_P,
    ALT_Q,ALT_R,ALT_S,ALT_T,ALT_U,ALT_V,ALT_W,ALT_X,
    ALT_Y,ALT_Z,ALT_0,ALT_1,ALT_2,ALT_3,ALT_4,ALT_5,
    ALT_6,ALT_7,ALT_8,ALT_9,0
};

unsigned video_mode;
unsigned video_page;

static int near cursorpos[MAXSAVES];
static int near cursorshape[MAXSAVES];
static int cs = 0;

static union REGS regs;

#ifndef MSC
#define ZEROFLAG 0x40
/* ---- Test for keystroke ---- */
int keyhit(void)
{
    _AH = 1;
    geninterrupt(KEYBRD);
    return (_FLAGS & ZEROFLAG) == 0;
}
#endif

/* ---- Read a keystroke ---- */
int getkey(void)
{
    int c;
    while (keyhit() == 0)
        ;
    if (((c = bioskey(0)) & 0xff) == 0)
        c = (c >> 8) | 0x80;
    return c & 0xff;
}

/* ---------- read the keyboard shift status --------- */
int getshift(void)
{
    regs.h.ah = 2;
    int86(KEYBRD, &regs, &regs);
    return regs.h.al;
}

/* ------- macro to wait one clock tick -------- */
#define wait()                     \
{                                  \
    int now = peek(0x40,0x6c);     \
    while (now == peek(0x40,0x6c)) \
        ;                          \
}

/* -------- sound a buzz tone ---------- */
void beep(void)
{
    wait();
    outp(0x43, 0xb6);               /* program the frequency */
    outp(0x42, (int) (COUNT % 256));
    outp(0x42, (int) (COUNT / 256));
    outp(0x61, inp(0x61) | 3);      /* start the sound */
    wait();
    outp(0x61, inp(0x61) & ~3);     /* stop the sound  */
}

/* -------- get the video mode and page from BIOS -------- */
void videomode(void)
{
    regs.h.ah = 15;
    int86(VIDEO, &regs, &regs);
    video_mode = regs.h.al;
    video_page = regs.x.bx;
    video_page &= 0xff00;
    video_mode &= 0x7f;
}

/* ------ position the cursor ------ */
void cursor(int x, int y)
{
    videomode();
    regs.x.dx = ((y << 8) & 0xff00) + x;
    regs.x.ax = 0x0200;
    regs.x.bx = video_page;
    int86(VIDEO, &regs, &regs);
}

/* ------ get cursor shape and position ------ */
static void near getcursor(void)
{
    videomode();
    regs.h.ah = READCURSOR;
    regs.x.bx = video_page;
    int86(VIDEO, &regs, &regs);
}

/* ------- get the current cursor position ------- */
void curr_cursor(int *x, int *y)
{
    getcursor();
    *x = regs.h.dl;
    *y = regs.h.dh;
}

/* ------ save the current cursor configuration ------ */
void savecursor(void)
{
    if (cs < MAXSAVES)    {
        getcursor();
        cursorshape[cs] = regs.x.cx;
        cursorpos[cs] = regs.x.dx;
        cs++;
    }
}

/* ---- restore the saved cursor configuration ---- */
void restorecursor(void)
{
    if (cs)    {
        --cs;
        videomode();
        regs.x.dx = cursorpos[cs];
        regs.h.ah = SETCURSOR;
         regs.x.bx = video_page;
        int86(VIDEO, &regs, &regs);
        set_cursor_type(cursorshape[cs]);
    }
}

/* ------ make a normal cursor ------ */
void normalcursor(void)
{
    set_cursor_type(0x0607);
}

/* ------ hide the cursor ------ */
void hidecursor(void)
{
    getcursor();
    regs.h.ch |= HIDECURSOR;
    regs.h.ah = SETCURSORTYPE;
    int86(VIDEO, &regs, &regs);
}

/* ------ unhide the cursor ------ */
void unhidecursor(void)
{
    getcursor();
    regs.h.ch &= ~HIDECURSOR;
    regs.h.ah = SETCURSORTYPE;
    int86(VIDEO, &regs, &regs);
}

/* ---- use BIOS to set the cursor type ---- */
void set_cursor_type(unsigned t)
{
    videomode();
    regs.h.ah = SETCURSORTYPE;
     regs.x.bx = video_page;
    regs.x.cx = t;
    int86(VIDEO, &regs, &regs);
}
```

#### LISTING_EIGHT

```d
/* ------------- mouse.c ------------- */

#include <stdio.h>
#include <dos.h>
#include <stdlib.h>
#include <string.h>
#include "system.h"

static union REGS regs;

static void near mouse(int m1,int m2,int m3,int m4)
{
    regs.x.dx = m4;
    regs.x.cx = m3;
    regs.x.bx = m2;
    regs.x.ax = m1;
    int86(MOUSE, &regs, &regs);
}

/* ---------- reset the mouse ---------- */
void resetmouse(void)
{
    mouse(0,0,0,0);
}

/* ----- test to see if the mouse driver is installed ----- */
int mouse_installed(void)
{
    unsigned char far *ms;
    ms = MK_FP(peek(0, MOUSE*4+2), peek(0, MOUSE*4));
    return (ms != NULL && *ms != 0xcf);
}

/* ------ return true if mouse buttons are pressed ------- */
int mousebuttons(void)
{
    if (mouse_installed())
        mouse(3,0,0,0);
    return regs.x.bx & 3;
}

/* ---------- return mouse coordinates ---------- */
void get_mouseposition(int *x, int *y)
{
    if (mouse_installed())    {
        mouse(3,0,0,0);
        *x = regs.x.cx/8;
        *y = regs.x.dx/8;
    }
}

/* -------- position the mouse cursor -------- */
void set_mouseposition(int x, int y)
{
    if(mouse_installed())
        mouse(4,0,x*8,y*8);
}

/* --------- display the mouse cursor -------- */
void show_mousecursor(void)
{
    if(mouse_installed())
        mouse(1,0,0,0);
}

/* --------- hide the mouse cursor ------- */
void hide_mousecursor(void)
{
    if(mouse_installed())
        mouse(2,0,0,0);
}

/* --- return true if a mouse button has been released --- */
int button_releases(void)
{
    if(mouse_installed())
        mouse(6,0,0,0);
    return regs.x.bx;
}
```

Copyright © 1991, Dr. Dobb's Journal
