
# C PROGRAMMING 9107

## D-Flat obrada poruka - AL STIVENS

Ovo je treći deo u nastavljenoj sagi D-Flat. Mnogi od vas su se odazvali ovom projektu. Mnogi su preuzeli preliminarnu biblioteku izvornog koda sa CompuServe-a i TelePath-a, sastavili su je i već mi šalju izveštaje o greškama.

Jedan od problema sa preliminarnim objavljivanjem koda je taj što još ne postoji dokumentacija za poruke, makroe i funkcije D-Flat API-ja. Uskoro ću proizvoditi i postavljati API i korisničku dokumentaciju. U međuvremenu, morate iskoristiti svoje hakerske talente da iz koda izvučete šta treba da znate. Ako budete zbunjeni, pošaljite mi pitanja na CompuServe-u.

### Događaji, poruke i prozori

Ovomesečno poglavlje bavi se mehanizmima događaja i poruka koje D-Flat koristi. Posvetio sam kolumnu arhitekturi vođenoj događajima u izdanju iz marta 1991. godine. Sledi rezime kako to funkcioniše.

Program koji koristi arhitekturu vođenu događajima reaguje na događaje spolja - pritiske na tastere, radnje miša, sat. Softver sistema pretvara ove hardverske i korisničke događaje u poruke koje šalje softveru aplikacije. Aplikacioni softver miruje i čeka da stigne poruka. Kada to uradi, softver ga obrađuje i može da šalje druge poruke drugim delovima sebe i sistemskom softveru.

Arhitektura vođena događajima zasnovana na prozorima koristi video prozor kao objekat aplikacije u koji i iz kojeg prolaze poruke. Vindovs prima i obrađuje poruke i šalje poruke drugim prozorima i sistemu. Ako niste navikli na ovu paradigmu, u početku vam se neće činiti da ima smisla.

U tradicionalnom programu orijentisanom na funkcije, stavke podataka ne rade takve stvari kao što su slanje i primanje poruka. Oni jednostavno postoje, a funkcije im rade stvari. U programu vođenom događajima događaji se dešavaju i sistem šalje poruke prozorima. Prozor je stavka podataka, objekat. Prima i šalje poruke. Kako to može biti? Nije jednostavno, ali se ne razlikuje mnogo od onoga na šta ste navikli. Umesto da pišete funkciju koja radi stvari sa prozorom, vi pišete funkciju koja predstavlja sam prozor i koja prima, obrađuje i šalje poruke.

Ovo ponašanje prosleđivanja poruka između prozora i između prozora i sistema je osnova objektno orijentisanog programiranja, paradigme koja otelotvoruje i druge jedinstvene karakteristike pored prosleđivanja poruka. Nije ni čudo što je većina okruženja za razvoj programa za Vindovs i slične platforme vođene događajima zasnovana na objektno orijentisanom programiranju.

Evo jednostavnog scenarija. Aplikacioni softver otvara prozor na ekranu, a zatim čeka da se nešto dogodi. Sistemski softver je u petlji posmatrajući hardver. Korisnik pritisne taster. Taj pritisak na taster je događaj. Sistemski softver prevodi pritisak na taster u poruku i šalje poruku prozoru.

Prozor ima funkciju obrade događaja koja prima i obrađuje sve svoje poruke. Prima poruku o pritisku na taster i radi sve što prozor njegove klase radi pritiskom na taster. Možda šalje sistemu poruku koja kaže: "Gde je pozicioniran kursor?" Sistem odgovara na takve poruke. Možda prozor uspostavlja drugi prozor i šalje mu poruku koja kaže: „Uzmi ovaj tekst i prikaži ga“.

Postoje tri osnovna tipa poruka – poruke o događajima, poruke od prozora do sistema i poruke prozorima iz drugih prozora ili sistema. Poruke o događajima prijavljuju događaje miša, tastature i sata prozorima. Poruke od prozora do sistema uključuju takve radnje kao što su pozicioniranje i traženje položaja kursora miša i tastature. Poruke prozorima govore prozoru da uradi nešto, kao što je prefarbanje prostora za podatke ili promena njegove veličine. Poruke prozorima takođe mogu tražiti od prozora da kaže pošiljaocu poruke nešto o primaocu poruke.

D-Flat, kao i drugi sistemi zasnovani na prozorima, podržava skup standardnih klasa prozora. Prošlomesečna kolona je objavila kod definicije klase. Možete da napravite celu aplikaciju koristeći standardne menije, okvire za dijalog, okvire sa listama, dugmad itd. Takođe možete izvesti nove klase prozora iz postojećih i napisati funkcije za obradu prozora koje upravljaju jedinstvenim kvalitetima vaše nove klase prozora. Izvedena klasa prozora može zadržati deo ili celo ponašanje svoje osnovne klase. Ponašanje prozora je funkcija njegove reakcije na poruke. Izvedena klasa prozora može uvesti i obraditi nove poruke, i može presresti i obraditi postojeće poruke koje bi njena osnovna klasa obrađivala na druge načine. Ova sposobnost da se izvedu nove klase prozora koje zamenjuju ponašanje njihove osnovne klase je još jedna oblast u kojoj ova arhitektura liči na objektno orijentisano programiranje.

### Softver za poruke

[LISING_ONE](#listing_one), je "message.h". Počinje sa nekim globalnim definicijama koje se odnose na poruke. MAKSMESSAGES je maksimalan broj poruka koje se mogu staviti u red čekanja. Sa 50, trebalo bi da bude dovoljno. FIRSTDELAI i DELAITICKS kontrolišu ponašanje miša slično tipu kada držite dugme pritisnuto za radnje prevlačenja ili pomeranja. Vrednosti u ovim globalima su otkucaji sata od 1/18 sekunde. Sistem čeka otkucaje FIRSTDELAI nakon što prvi put pritisnete dugme pre nego što počne da ponavlja događaj dugmeta. Zatim čeka DELAITICKS otkucaja između svakog ponavljanja događaja. Vrednost DOUBLETICKS je maksimalni broj tiketa koji može da prođe između dva klika na istoj poziciji ekrana pre nego što sistem proglasi događaj dvostrukog klika. Neki programi vođeni mišem omogućavaju korisniku da izmeni ove vrednosti. D-Flat to ne radi, ali ako odlučite da to želite, trebalo bi da promenite ove globalne simbole u ​​promenljive koje vaš korisnik može da promeni.

### Poruke

Message.h je datoteka zaglavlja koja definiše poruke u D-Flat aplikaciji. Ako izvodite nove klase prozora i trebate da dodate poruke, dodali biste ih ovoj izvornoj datoteci. Iz naziva poruka i njihovih komentara nije vidljivo da li su poruke zasnovane na događajima, poruke od prozora ka sistemu ili poruke između prozora. Ova razlika bi trebalo da postane očigledna dok koristite poruke u programima koji slede.

Prva poruka je START poruka. Još mu nisam našao upotrebu, ali je tu u slučaju da ga nađem. Ako jednog dana nestane iz šifre, odustao sam od toga. Poruke su podeljene na procesne komunikacione poruke; poruke za upravljanje prozorima; poruke sata, tastature i miša; poruke za različite standardne klase prozora; i poruke dijaloga.

Vindovs može da šalje poruke sistemu i šalje ili postavlja poruke drugim prozorima, uključujući i njih same. Prošlomesečna kolona je opisala funkciju Kreiraj prozor, koja kreira prozor i vraća njegovu ručicu, promenljivu tipa VINDOV. Taj rukohvat je način na koji se prozor identifikuje i način na koji pošiljalac poruke adresira poruku prozoru. Ako je poruka za sistem, VINDOV adresa je konstantna vrednost NULLVND.

Svaka poruka uključuje identifikator poruke preuzet sa liste u message.h. Pored identifikatora poruke, svaka poruka ima dva duga celobrojna parametra koja prosleđuju argumente zajedno sa porukom. Sadržaj i značenje parametara zavisi od same poruke. Ponekad su prazni. Neke poruke koriste samo prvi parametar, druge koriste oba. Ponekad su parametri pokazivači; drugi put su celobrojne vrednosti; u drugim slučajevima oni su jednostavni indikatori za uključivanje/isključivanje.

[LISTING_TWO](#listing_two), je "message.c", izvorni kod za obradu događaja i poruka. Održava red događaja i red poruka. Red događaja prikuplja događaje miša, tastature i sata. Red poruka prikuplja poruke. Datoteka message.c ima dve funkcije prekida. Prvi, novi tajmer, je za prekid tajmera. Odbrojava tri softverska tajmera koja podržavaju događaje. Jedan tajmer, brojač dvostrukog tajmera broji otkucaje između klikova mišem da vidi da li se dogodio događaj dvostrukog klika. Brojač kašnjenja kontroliše kašnjenja između ponavljanja događaja dugmeta kada korisnik ne otpusti dugme. Brojač časovnika je za događaj sata od jedne sekunde.

Druga funkcija prekida je nev-crit. On presreće prekide kritične greške, objavljuje grešku i objavljuje slovo disk jedinice kritične greške u poruku o grešci. Funkcija TestCriticalError prikazuje tu poruku o grešci u okviru za dijalog i preuzima opciju ignorisanja ili pokušaja korisnika. Ova tehnika sprečava DOS da svoju grubu poruku „Prekini, pokušaj ponovo ili ignoriši“ ispljusne na vaš uredan D-Flat ekran.

Funkcija init_messages inicijalizuje obradu poruke. Aplikacioni program mora pozvati ovu funkciju pre nego što počne da čeka poruke. Funkcija inicijalizuje sve promenljive, vezuje vektore prekida za tajmer i rukovalac kritičnim greškama i postavlja START poruku u sistem.

### Čekam poruku

Nakon što program kreira prozor i pozove init_messages, može ući u petlju koja čeka poruke. Petlja izgleda ovako:

```c
while (dispatch_message()) ;
```

Funkcija dispatch_message je na dnu messages.c. Sve dok ništa ne šalje STOP poruku sistemu, funkcija dispatch_message će vratiti tačnu vrednost, a vaš program će ostati u petlji. Kada se petlja prekine, obrada poruke je završena. Morali biste da pozovete init_messages da biste izvršili bilo kakvu dalju obradu poruke sa dispatch_message.

### Prikupljanje događaja

Funkcija dispatch_message poziva funkciju collect_events da prikupi sve događaje na čekanju i stavi ih u red. Funkcije collect_events nadgledaju hardver, tumače događaje i stavljaju ih u red. Događaji u redu čekanja se sastoje od MESSAGE koda koji identifikuje događaj i dva celobrojna parametra.

Ako se brojač clocktimera smanjio, funkcija collect_events čita sat vremena u danu i postavlja događaj CLOCKTICK u red događaja. Dva parametra su segment i pomak promenljive niza koja sadrži kopiju vremena koja se može prikazati. Prikaz vremena se menja između stavljanja dvotačke i razmaka između sata i minuta, tako da će izgledati da sat otkucava kada prikažete string.

Ako se status tastera shift na tastaturi promenio, funkcija collect_events stavlja u red događaj SHIFT_CHANGED sa novom vrednošću statusa shift kao prvim parametrom. Ako je korisnik uneo ključ, funkcija collect_events prevodi pritisak na taster u događaj i stavlja ga u red.

Događaji miša su nezgodni. Program pamti najnoviju lokaciju miša. Ako pomerite miša, funkcija collect_events stavlja u red događaj MOUSE_MOVED sa novim koordinatama miša u parametrima. Ako je desno dugme dole, funkcija objavljuje događaj RIGHT_BUTTON koji takođe sadrži koordinate ekrana gde je bio miš kada ste pritisnuli dugme. Ako je levo dugme dole i miš se pomerio, ovo je novi pritisak na dugme što se tiče softvera i on stavlja u red događaj LEFT_BUTTON sa koordinatama ekrana. Takođe isključuje dvostruki tajmer i postavlja tajmer odlaganja.

Ako ste otpustili dugme miša, funkcija ukida tajmer odlaganja i pokreće dvostruki tajmer. Ako ponovo pritisnete levo dugme na istom mestu pre nego što tajmer istekne, to je dvostruki klik.

Ako je levo dugme dole, a miš se nije pomerio, možda se desila jedna od dve stvari. Možda samo držite dugme pritisnuto. Ako to uradite i tajmer odlaganja se smanji, funkcija objavljuje drugi događaj LEFT_BUTTON i ponovo pokreće tajmer kašnjenja. Međutim, ako je dvostruki tajmer pokrenut, bilo je dva klika u vremenskom periodu trajanja tajmera, a funkcija onemogućava tajmer i stavlja u red događaj DOUBLE_CLICK.

### Pretvaranje događaja u poruke

Nakon poziva funkcije collect_events, funkcija dispatch_message traži da li ima bilo kakvih događaja u redu događaja. Sve dok ih ima, on ih uklanja iz reda, prevodi ih u poruke i šalje poruke prozorima koji bi ih trebali dobiti. Poruke miša obično idu do prozora u kojem se dogodio događaj miša kako je određeno ekranskim koordinatama događaja. Izuzetak je kada je drugi prozor uhvatio sve događaje miša bez obzira na to gde su pogodili.

Poruke sa tastature idu do prozora koji trenutno ima „fokus“, a to je prozor u koji korisnik upisuje podatke. Prozor u fokusu je onaj koji privlači pažnju korisnika. Prikazuje se iznad svih drugih prozora i, ako je to prozor koji prihvata kucanje, kursor tastature je u njemu. Ako nijedan takav prozor nije u fokusu, događaji na tastaturi idu u bilo koji prozor koji ima fokus, a odgovornost tog prozora je da vidi da li se tastatura obrađuje ili ignoriše, šta god je prikladno.

Ako događaj miša LEFT_BUTTON ide na prozor koji nema fokus, funkcija dispatch_message šalje prozoru SETFOCUS poruku da mu kaže da preuzme fokus. Ova procedura vam omogućava da dovedete prozor na vrh i da mu date fokus za unos tako što ćete kliknuti bilo gde u njegovom prostoru.

Funkcija dispatch_message šalje CLOCKTICK poruke svakom prozoru koji je uhvatio sat tako što sam sebi šalje poruku CAPTURE_CLOCK.

### Objavljivanje i slanje poruka

Red poruka je mesto gde sistem i Vindovs postavljaju poruke da bi otišli u vindovs. Oni to rade tako što pozivaju funkciju PostMessage, koja prihvata VINDOV handle, identifikator MESSAGE i dva parametra poruke. Funkcija dispatch_message uklanja te poruke iz reda i šalje ih preko funkcije SendMessage, koja uzima iste parametre. Ako je poruka STOP ili ENDDIALOG poruka, dispatch_message vraća lažnu vrednost da kaže petlji obrade poruka aplikacije ili menadžeru dijaloga da se zaustavi.

Sistem i prozori šalju poruke jedni drugima pomoću funkcije SendMessage. Razlikuje se od funkcije PostMessage po tome što se poruke šalju odmah. Ako pozovete SendMessage, sistem se neće vratiti na funkciju poziva sve dok poruka ne bude poslata i obrađena. Ako pozovete PostMessage, poruka će otići u red poruka i neće biti poslata do sledećeg puta kroz petlju za slanje poruka. Ako je funkcija poruke da vrati vrednost, morate koristiti SendMessage. Objavljena poruka ne vraća ništa u prozor koji je postavlja.

Funkcija SendMessage obrađuje poruke koje su za sistem, a ne za druge prozore. Ovo uključuje STOP, CAPTURE_CLOCK, RELEASE_CLOCK, KEIBOARD_CURSOR, CAPTURE_KEIBOARD, RELEASE_KEIBOARD, CURRENT_KEIBOARD_CURSOR, SAVE_CURSOR, RESTORE_CURSOR, HIDE_CURSOR, SHOVE_CURSOR, SHOVE_CURSOR, MODEUS, MODEUS HIDE_MOUSE, MOUSE_CURSOR, CURRENT_MOUSE_CURSOR, VAITMOUSE, TESTMOUSE, CAPTURE_MOUSE i RELEASE_MOUSE poruke. Ove poruke ili govore sistemu da promeni nešto u hardveru, da traži neke vrednosti vezane za hardver ili da uhvati i oslobodi ulazne uređaje.

### Promene u D-Flat kodu

To je neizbežno. Projekat ove veličine raste i menja se, a ponekad morate da se povučete. Napravio sam jedan mali dodatak strukturi prozora u dflat.h, objavljenom u izdanju iz maja 1991. godine. Ubacite ovu promenljivu odmah iza uslova celog broja.

```c
int restored_attrib; /* attributes when restored */
```

Dodajte i dodeljivanje nule promenljivoj u funkciji CreateWindow u wvindow.c. Ova promenljiva čuva reč atributa prozora kada je prozor minimiziran ili maksimiziran. Procedure minimiziranja i maksimiziranja tada mogu isključiti neke atribute koji su neprikladni u minimiziranom ili maksimalnom prozoru.

### Procedura vraćanja atributnu reč kada vraća prozor

Nekoliko čitalaca je pitalo zašto je visina ekrana zaglavljena na 25 redova kada računar podržava nekoliko drugih konfiguracija. Promenite ovu globalnu definiciju u sistem.h od pre dva meseca.

```c
#define SCREENHEIGHT (peekb(0x40, 0x84)+1)
```

Prethodna verzija globalnog bila je postavljena na 25. Ova verzija dobija trenutnu visinu ekrana iz oblasti podataka BIOS RAM-a. Jedan čitalac izveštava da ova promena donosi visinu ekrana od 8 na njegovom video sistemu Hercules. Ako imate ovaj problem, vratite iskaz na raniju vrednost.

Naravno, sve promene koje pominjem u koloni nalaze se u D-Flat paketu izvornog koda koji možete preuzeti.

### Kako će to izgledati?

Iako smo u trećem mesecu kodiranja, ne možete ništa da uradite sa programima koji su do sada objavljeni. Postoje zavisnosti u izvornim modulima za nekoliko meseci. Možete preuzeti ceo paket, koristiti primer programa memopad, a možda i sami. Da bi vam pomogla da to uradite, Slika 1 prikazuje ekran memopad sa dva otvorena prozora uređivača i iskačućim menijem.

#### LISTING_ONE

```c
/* ----------- message.h ------------ */

#ifndef MESSAGES_H
#define MESSGAES_H

#define MAXMESSAGES 50
#define DELAYTICKS 1
#define FIRSTDELAY 7
#define DOUBLETICKS 5

typedef enum messages {
    /* ------------- process communication messages --------- */
    START,                  /* start message processing       */
    STOP,                   /* stop message processing        */
    COMMAND,                /* send a command to a window     */
    /* ------------- window management messages ------------- */
    CREATE_WINDOW,          /* create a window                */
    SHOW_WINDOW,            /* show a window                  */
    HIDE_WINDOW,            /* hide a window                  */
    CLOSE_WINDOW,           /* delete a window                */
    SETFOCUS,               /* set and clear the focus        */
    PAINT,                  /* paint the window's data space  */
    BORDER,                 /* paint the window's border      */
    TITLE,                  /* display the window's title     */
    MOVE,                   /* move the window                */
    SIZE,                   /* change the window's size       */
    MAXIMIZE,               /* maximize the window            */
    MINIMIZE,               /* minimize the window            */
    RESTORE,                /* restore the window             */
    INSIDE_WINDOW,          /* test x/y inside a window       */
    /* ------------- clock messages ------------------------- */
    CLOCKTICK,              /* the clock ticked               */
    CAPTURE_CLOCK,          /* capture clock into a window    */
    RELEASE_CLOCK,          /* release clock to the system    */
    /* ------------- keyboard and screen messages ----------- */
    KEYBOARD,               /* key was pressed                */
    CAPTURE_KEYBOARD,       /* capture keyboard into a window */
    RELEASE_KEYBOARD,       /* release keyboard to system     */
    KEYBOARD_CURSOR,        /* position the keyboard cursor   */
    CURRENT_KEYBOARD_CURSOR,/* read the cursor position       */
    HIDE_CURSOR,            /* hide the keyboard cursor       */
    SHOW_CURSOR,            /* display the keyboard cursor    */
    SAVE_CURSOR,            /* save the cursor's configuration*/
    RESTORE_CURSOR,         /* restore the saved cursor       */
    SHIFT_CHANGED,          /* the shift status changed       */
    /* ------------- mouse messages ------------------------- */
    MOUSE_INSTALLED,        /* test for mouse installed       */
    RIGHT_BUTTON,           /* right button pressed           */
    LEFT_BUTTON,            /* left button pressed            */
    DOUBLE_CLICK,           /* right button double-clicked    */
    MOUSE_MOVED,            /* mouse changed position         */
    BUTTON_RELEASED,        /* mouse button released          */
    CURRENT_MOUSE_CURSOR,   /* get mouse position             */
    MOUSE_CURSOR,           /* set mouse position             */
    SHOW_MOUSE,             /* make mouse cursor visible      */
    HIDE_MOUSE,             /* hide mouse cursor              */
    WAITMOUSE,              /* wait until button released     */
    TESTMOUSE,              /* test any mouse button pressed  */
    CAPTURE_MOUSE,          /* capture mouse into a window    */
    RELEASE_MOUSE,          /* release the mouse to system    */
    /* ------------- text box messages ---------------------- */
    ADDTEXT,                /* add text to the text box       */
    CLEARTEXT,              /* clear the edit box             */
    SETTEXT,                /* set address of text buffer     */
    SCROLL,                 /* vertical scroll of text box    */
    HORIZSCROLL,            /* horizontal scroll of text box  */
    /* ------------- edit box messages ---------------------- */
    EB_GETTEXT,             /* get text from an edit box      */
    EB_PUTTEXT,             /* put text into an edit box      */
    /* ------------- menubar messages ----------------------- */
    BUILDMENU,              /* build the menu display         */
    SELECTION,              /* menubar selection              */
    /* ------------- popdown messages ----------------------- */
    BUILD_SELECTIONS,       /* build the menu display         */
    CLOSE_POPDOWN,          /* tell parent popdown is closing */
    /* ------------- list box messages ---------------------- */
    LB_SELECTION,           /* sent to parent on selection    */
    LB_CHOOSE,              /* sent when user chooses         */
    LB_CURRENTSELECTION,    /* return the current selection   */
    LB_GETTEXT,             /* return the text of selection   */
    LB_SETSELECTION,        /* sets the listbox selection     */
    /* ------------- dialog box messages -------------------- */
    INITIATE_DIALOG,        /* begin a dialog                 */
    ENTERFOCUS,             /* tell DB control got focus      */
    LEAVEFOCUS,             /* tell DB control lost focus     */
    ENDDIALOG               /* end a dialog                   */
} MESSAGE;

/* --------- message prototypes ----------- */
void init_messages(void);
void PostMessage(WINDOW, MESSAGE, PARAM, PARAM);
int SendMessage(WINDOW, MESSAGE, PARAM, PARAM);
int dispatch_message(void);
int TestCriticalError(void);

#endif
```

#### LISTING_TWO

```c
/* --------- message.c ---------- */

#include <stdio.h>
#include <dos.h>
#include <conio.h>
#include <string.h>
#include <time.h>
#include "dflat.h"

static int px = -1, py = -1;
static int pmx = -1, pmy = -1;
static int mx, my;

static int CriticalError;

/* ---------- event queue ---------- */
static struct events    {
    MESSAGE event;
    int mx;
    int my;
} EventQueue[MAXMESSAGES];

/* ---------- message queue --------- */
static struct msgs {
    WINDOW wnd;
    MESSAGE msg;
    PARAM p1;
    PARAM p2;
} MsgQueue[MAXMESSAGES];

static int EventQueueOnCtr;
static int EventQueueOffCtr;
static int EventQueueCtr;

static int MsgQueueOnCtr;
static int MsgQueueOffCtr;
static int MsgQueueCtr;

static int lagdelay = FIRSTDELAY;

static void (interrupt far *oldtimer)(void) = NULL;
WINDOW CaptureMouse = NULLWND;
WINDOW CaptureKeyboard = NULLWND;
static int NoChildCaptureMouse = FALSE;
static int NoChildCaptureKeyboard = FALSE;

static int doubletimer = -1;
static int delaytimer  = -1;
static int clocktimer  = -1;

WINDOW Cwnd = NULLWND;

/* ------- timer interrupt service routine ------- */
static void interrupt far newtimer(void)
{
    if (timer_running(doubletimer))
        countdown(doubletimer);
    if (timer_running(delaytimer))
        countdown(delaytimer);
    if (timer_running(clocktimer))
        countdown(clocktimer);
    oldtimer();
}

static char ermsg[] = "Error accessing drive x";

/* -------- test for critical errors --------- */
int TestCriticalError(void)
{
    int rtn = 0;
    if (CriticalError)    {
        rtn = 1;
        CriticalError = FALSE;
        if (TestErrorMessage(ermsg) == FALSE)
            rtn = 2;
    }
    return rtn;
}

/* ------ critical error interrupt service routine ------ */
static void interrupt far newcrit(IREGS ir)
{
    if (!(ir.ax & 0x8000))     {
        ermsg[sizeof(ermsg) - 2] = (ir.ax & 0xff) + 'A';
        CriticalError = TRUE;
    }
    ir.ax = 0;
}

/* ------------ initialize the message system --------- */
void init_messages(void)
{
    resetmouse();
    show_mousecursor();
    px = py = -1;
    pmx = pmy = -1;
    mx = my = 0;
    CaptureMouse = CaptureKeyboard = NULLWND;
    NoChildCaptureMouse = FALSE;
    NoChildCaptureKeyboard = FALSE;
    MsgQueueOnCtr = MsgQueueOffCtr = MsgQueueCtr = 0;
    EventQueueOnCtr = EventQueueOffCtr = EventQueueCtr = 0;
    if (oldtimer == NULL)    {
        oldtimer = getvect(TIMER);
        setvect(TIMER, newtimer);
    }
    setvect(CRIT, newcrit);
    PostMessage(NULLWND,START,0,0);
    lagdelay = FIRSTDELAY;
}

/* ----- post an event and parameters to event queue ---- */
static void PostEvent(MESSAGE event, int p1, int p2)
{
    if (EventQueueCtr != MAXMESSAGES)    {
        EventQueue[EventQueueOnCtr].event = event;
        EventQueue[EventQueueOnCtr].mx = p1;
        EventQueue[EventQueueOnCtr].my = p2;
        if (++EventQueueOnCtr == MAXMESSAGES)
            EventQueueOnCtr = 0;
        EventQueueCtr++;
    }
}

/* ------ collect mouse, clock, and keyboard events ----- */
static void near collect_events(void)
{
    struct tm *now;
    static int flipflop = FALSE;
    static char timestr[8];
    int hr, sk;
    static int ShiftKeys = 0;

    /* -------- test for a clock event (one/second) ------- */
    if (timed_out(clocktimer))    {
        /* ----- get the current time ----- */
        time_t t = time(NULL);
        now = localtime(&t);
        hr = now->tm_hour > 12 ?
             now->tm_hour - 12 :
             now->tm_hour;
        if (hr == 0)
            hr = 12;
        sprintf(timestr, "%2.2d:%02d", hr, now->tm_min);
        strcpy(timestr+5, now->tm_hour > 11 ? "pm" : "am");
        /* ------- blink the : at one-second intervals ----- */
        if (flipflop)
            *(timestr+2) = ' ';
        flipflop ^= TRUE;
        /* -------- reset the timer -------- */
        set_timer(clocktimer, 1);
        /* -------- post the clock event -------- */
        PostEvent(CLOCKTICK, FP_SEG(timestr), FP_OFF(timestr));
    }

    /* --------- keyboard events ---------- */
    if ((sk = getshift()) != ShiftKeys)    {
        ShiftKeys = sk;
        /* ---- the shift status changed ---- */
        PostEvent(SHIFT_CHANGED, sk, 0);
    }

    /* ---- build keyboard events for key combinations that
        BIOS doesn't report --------- */
    if (sk & ALTKEY)
        if (inp(0x60) == 14)    {
            while (!(inp(0x60) & 0x80))
                ;
            PostEvent(KEYBOARD, ALT_BS, sk);
        }
    if (sk & CTRLKEY)
        if (inp(0x60) == 82)    {
            while (!(inp(0x60) & 0x80))
                ;
            PostEvent(KEYBOARD, CTRL_INS, sk);
        }

    /* ----------- test for keystroke ------- */
    if (keyhit())    {
        static int cvt[] = {SHIFT_INS,END,DN,PGDN,BS,'5',
                        FWD,HOME,UP,PGUP};
        int c = getkey();

        /* -------- convert numeric pad keys ------- */
        if (sk & (LEFTSHIFT | RIGHTSHIFT))    {
            if (c >= '0' && c <= '9')
                c = cvt[c-'0'];
            else if (c == '.' || c == DEL)
                c = SHIFT_DEL;
            else if (c == INS)
                c = SHIFT_INS;
        }
        /* -------- clear the BIOS readahead buffer -------- */
        *(int far *)(MK_FP(0x40,0x1a)) =
            *(int far *)(MK_FP(0x40,0x1c));
        /* ---- if help key call generic help function ---- */
        if (c == F1)
            HelpFunction();
        else
            /* ------ post the keyboard event ------ */
            PostEvent(KEYBOARD, c, sk);
    }

    /* ------------ test for mouse events --------- */
    get_mouseposition(&mx, &my);
    if (mx != px || my != py)  {
        px = mx;
        py = my;
        PostEvent(MOUSE_MOVED, mx, my);
    }
    if (rightbutton())
        PostEvent(RIGHT_BUTTON, mx, my);
    if (leftbutton())    {
        if (mx == pmx && my == pmy)    {
            /* ---- same position as last left button ---- */
            if (timer_running(doubletimer))    {
                /* -- second click before double timeout -- */
                disable_timer(doubletimer);
                PostEvent(DOUBLE_CLICK, mx, my);
            }
            else if (!timer_running(delaytimer))    {
                /* ---- button held down a while ---- */
                delaytimer = lagdelay;
                lagdelay = DELAYTICKS;
                /* ---- post a typematic-like button ---- */
                PostEvent(LEFT_BUTTON, mx, my);
            }
        }
        else    {
            /* --------- new button press ------- */
            disable_timer(doubletimer);
            delaytimer = FIRSTDELAY;
            lagdelay = DELAYTICKS;
            PostEvent(LEFT_BUTTON, mx, my);
            pmx = mx;
            pmy = my;
        }
    }
    else
        lagdelay = FIRSTDELAY;
    if (button_releases())    {
        /* ------- the button was released -------- */
        doubletimer = DOUBLETICKS;
        PostEvent(BUTTON_RELEASED, mx, my);
        disable_timer(delaytimer);
    }
}

/* ----- post a message and parameters to msg queue ---- */
void PostMessage(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    if (MsgQueueCtr != MAXMESSAGES)    {
        MsgQueue[MsgQueueOnCtr].wnd = wnd;
        MsgQueue[MsgQueueOnCtr].msg = msg;
        MsgQueue[MsgQueueOnCtr].p1 = p1;
        MsgQueue[MsgQueueOnCtr].p2 = p2;
        if (++MsgQueueOnCtr == MAXMESSAGES)
            MsgQueueOnCtr = 0;
        MsgQueueCtr++;
    }
}

/* --------- send a message to a window ----------- */
int SendMessage(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int rtn = TRUE, x, y;
    if (wnd != NULLWND)
        switch (msg)    {
            case PAINT:
            case BORDER:
            case RIGHT_BUTTON:
            case LEFT_BUTTON:
            case DOUBLE_CLICK:
            case BUTTON_RELEASED:
            case KEYBOARD:
            case SHIFT_CHANGED:
                /* ------- don't send these messages unless the
                    window is visible -------- */
                if (!isVisible(wnd))
                    break;
            default:
                rtn = wnd->wndproc(wnd, msg, p1, p2);
                break;
        }
    /* ----- window processor returned or the message was sent
        to no window at all (NULLWND) ----- */
    if (rtn != FALSE)    {
        /* --------- process messages that a window sends to the
            system itself ---------- */
        switch (msg)    {
            case STOP:
                hide_mousecursor();
                if (oldtimer != NULL)    {
                    setvect(TIMER, oldtimer);
                    oldtimer = NULL;
                }
                break;
            /* ------- clock messages --------- */
            case CAPTURE_CLOCK:
                Cwnd = wnd;
                set_timer(clocktimer, 0);
                break;
            case RELEASE_CLOCK:
                Cwnd = NULLWND;
                disable_timer(clocktimer);
                break;
            /* -------- keyboard messages ------- */
            case KEYBOARD_CURSOR:
                if (wnd == NULLWND)
                    cursor((int)p1, (int)p2);
                else
                 cursor(GetClientLeft(wnd)+(int)p1, GetClientTop(wnd)+(int)p2);
                break;
            case CAPTURE_KEYBOARD:
                if (p2)
                    ((WINDOW)p2)->PrevKeyboard=CaptureKeyboard;
                else
                    wnd->PrevKeyboard = CaptureKeyboard;
                CaptureKeyboard = wnd;
                NoChildCaptureKeyboard = (int)p1;
                break;
            case RELEASE_KEYBOARD:
                CaptureKeyboard = wnd->PrevKeyboard;
                NoChildCaptureKeyboard = FALSE;
                break;
            case CURRENT_KEYBOARD_CURSOR:
                curr_cursor(&x, &y);
                *(int*)p1 = x;
                *(int*)p2 = y;
                break;
            case SAVE_CURSOR:
                savecursor();
                break;
            case RESTORE_CURSOR:
                restorecursor();
                break;
            case HIDE_CURSOR:
                normalcursor();
                hidecursor();
                break;
            case SHOW_CURSOR:
                if (p1)
                    set_cursor_type(0x0106);
                else
                    set_cursor_type(0x0607);
                unhidecursor();
                break;
            /* -------- mouse messages -------- */
            case MOUSE_INSTALLED:
                rtn = mouse_installed();
                break;
            case SHOW_MOUSE:
                show_mousecursor();
                break;
            case HIDE_MOUSE:
                hide_mousecursor();
                break;
            case MOUSE_CURSOR:
                set_mouseposition((int)p1, (int)p2);
                break;
            case CURRENT_MOUSE_CURSOR:
                get_mouseposition((int*)p1,(int*)p2);
                break;
            case WAITMOUSE:
                waitformouse();
                break;
            case TESTMOUSE:
                rtn = mousebuttons();
                break;
            case CAPTURE_MOUSE:
                if (p2)
                    ((WINDOW)p2)->PrevMouse = CaptureMouse;
                else
                    wnd->PrevMouse = CaptureMouse;
                CaptureMouse = wnd;
                NoChildCaptureMouse = (int)p1;
                break;
            case RELEASE_MOUSE:
                CaptureMouse = wnd->PrevMouse;
                NoChildCaptureMouse = FALSE;
                break;
            default:
                break;
        }
    }
    return rtn;
}

/* ---- dispatch messages to the message proc function ---- */
int dispatch_message(void)
{
    WINDOW Mwnd, Kwnd;
    /* -------- collect mouse and keyboard events ------- */
    collect_events();
    /* --------- dequeue and process events -------- */
    while (EventQueueCtr > 0)  {
        struct events ev = EventQueue[EventQueueOffCtr];

        if (++EventQueueOffCtr == MAXMESSAGES)
            EventQueueOffCtr = 0;
        --EventQueueCtr;

        /* ------ get the window in which a
                        mouse event occurred ------ */
        Mwnd = inWindow(ev.mx, ev.my);

        /* ---- process mouse captures ----- */
        if (CaptureMouse != NULLWND)
            if (Mwnd == NULLWND ||
                    NoChildCaptureMouse ||
                        GetParent(Mwnd) != CaptureMouse)
                Mwnd = CaptureMouse;

        /* ------ get the window in which a
                        keyboard event occurred ------ */
        Kwnd = inFocus;

        /* ---- process keyboard captures ----- */
        if (CaptureKeyboard != NULLWND)
            if (Kwnd == NULLWND ||
                    NoChildCaptureKeyboard ||
                        GetParent(Kwnd) != CaptureKeyboard)
                Kwnd = CaptureKeyboard;

        /* -------- send mouse and keyboard messages to the
            window that should get them -------- */
        switch (ev.event)    {
            case SHIFT_CHANGED:
            case KEYBOARD:
                SendMessage(Kwnd, ev.event, ev.mx, ev.my);
                break;
            case LEFT_BUTTON:
                if (!CaptureMouse ||
                        (!NoChildCaptureMouse &&
                            GetParent(Mwnd) == CaptureMouse))
                    if (Mwnd != inFocus)
                        SendMessage(Mwnd, SETFOCUS, TRUE, 0);
            case BUTTON_RELEASED:
            case DOUBLE_CLICK:
            case RIGHT_BUTTON:
            case MOUSE_MOVED:
                SendMessage(Mwnd, ev.event, ev.mx, ev.my);
                break;
            case CLOCKTICK:
                SendMessage(Cwnd, ev.event,
                    (PARAM) MK_FP(ev.mx, ev.my), 0);
            default:
                break;
        }
    }
    /* ------ dequeue and process messages ----- */
    while (MsgQueueCtr > 0)  {
        struct msgs mq = MsgQueue[MsgQueueOffCtr];
        if (++MsgQueueOffCtr == MAXMESSAGES)
            MsgQueueOffCtr = 0;
        --MsgQueueCtr;
        SendMessage(mq.wnd, mq.msg, mq.p1, mq.p2);
        if (mq.msg == STOP || mq.msg == ENDDIALOG)
               return FALSE;
    }
    return TRUE;
}
```

#### LISTING_THREE

```c
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
    RectBottom(clrc) = min(RectBottom(clrc), WindowHeight(wnd)-3);
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
        if (RectLeft(rc) < WindowWidth(wnd))
            if (TestAttribute(wnd, TITLEBAR))
                DisplayTitle(wnd, clrc);
    foreground = FrameForeground(wnd);
    background = FrameBackground(wnd);
    /* -------- top frame corners --------- */
    if (RectTop(rc) == 0)    {
        if (RectLeft(rc) == 0)
            PutWindowChar(wnd, -1, -1, nw);
        if (RectLeft(rc) < RectRight(rc))    {
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
    }
    /* ----------- window body ------------ */
    for (y = 0; y < ClientHeight(wnd); y++)    {
        int ch;
        if (y >= RectTop(clrc) && y <= RectBottom(clrc))    {
            if (RectLeft(rc) == 0)
                PutWindowChar(wnd, -1, y, side);
            if (RectLeft(rc) < RectRight(rc))    {
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
                    PutWindowChar(wnd,WindowWidth(wnd)-2,y,ch);
                }
            }
            if (RectRight(rc) == WindowWidth(wnd))
                shadow_char(wnd, y);
        }
    }
    if (RectTop(rc) < RectBottom(rc) &&
            RectBottom(rc) >= WindowHeight(wnd)-1)    {
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
        if (strlen(line+RectLeft(clrc)) > 1 || TestAttribute(wnd, SHADOW) == 0)
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
```c
---

Example 1:

```c
#include <stdio.h>
main()
{
    int x=1,y=1;
    x = (y++,y+3);
    printf("x=%d,y=%d\n",x,y);
    printf("++y=%d,x=%d\n",(--x,++y),x);
}
```

Copyright © 1991, Dr. Dobb's Journal
