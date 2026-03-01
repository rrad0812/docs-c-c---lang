
# C PROGRAMMING 9103

## O Mševima i porukama - Al Stevens

U tipičnom modelu vođenom događajima, događaji se dešavaju kada pritisnete taster, udarite mišem ili preduzmete neku drugu spoljašnju radnju koja natera program da uradi nešto. Aplikacioni program koji prati ovu kolumnu je jednostavan program za hvatanje ekrana za PC u tekstualnom režimu pod DOS-om. Pokreće se iz DOS komandne linije, ali, sa odgovarajućim drajverom, može biti TSR, što znači da može biti rezidentan u memoriji. Program koristi tastaturu ili miša da opiše pravougaonik na ekranu, koji može da upiše u tekstualnu datoteku. Koristim varijaciju ovog programa za snimanje ekrana za softversku dokumentaciju.

Iako se većina koda bavi hardverom miša i -- sledećeg meseca -- svim neugodnim stvarima koje morate da uradite da bi TSR radio, svrha programa je da demonstrira programiranje vođeno događajima u jeziku C. Ako ne želite da se petljate sa TSR delom projekta, možete preskočiti sledeći mesec, koristiti umetnutu glavnu funkciju koja zamenjuje kao iskačuću funkciju i svaki put pokrenuti program iz komandne linije. Naravno, da bi bio koristan za nešto drugo osim kao primer programiranja zasnovanog na događajima, grabber ekrana mora biti rezidentan u memoriji.

Još jedan uslov: Napisao sam hardverske drajvere u Turbo C 2.0 i koristio pseudoregistrator i druge ekstenzije koje Turbo dijalekt jezika pruža. Ako želite da koristite drugi kompajler, morate preneti program na konvencije drugog kompajlera za upravljanje prekidima, hardverskim registrima i slično. Njih dvoje ne rade to na isti način. Aplikacioni deo koda je standardni C, ali drajveri uređaja koriste Turbo ekstenzije.

### Događaji i poruke

Kako funkcioniše program vođen događajima? Prvo razmislite kako biste mogli napisati takav program koristeći tradicionalni proceduralni pristup. Nakon što ste inicijalizovali program, anketirali biste tastaturu i miša. Kada bi neko od njih uradio nešto, pročitali biste uređaj, utvrdili da li ono što je uradio ima uticaja na ono što je vašem programu bilo potrebno, preduzeli odgovarajuće mere i vratili se da ponovo ispitate hardver. Kada je jedna od radnji korisnika ukazala da je program završen, ne biste se vraćali u petlju za prozivanje, već biste isključili stvari i izašli iz programa. Ispitivanje uređaja, njihovo čitanje i tumačenje njihovih različitih ulaza sastavni su delovi vašeg programa.

Program vođen događajima takođe radi sve to, ali na nešto drugačiji način. Umesto da proziva uređaje i reaguje, program vođen događajima koristi funkciju dispečerstva događaja da pozove funkcije vaših aplikacija kada se događaj dogodi. Dispečer šalje poruku koju vaša funkcija tumači. Tako, na primer, umesto da čita tastaturu, vaš program čeka dok vam dispečer pošalje poruku koja vam kaže da je korisnik pritisnuo taster.

Softver za događaje posmatra hardver umesto vas i stavlja u red hardverske događaje kao poruke u redu. Dispečer preuzima poruke iz reda i šalje ih vašim funkcijama. Kao što možete očekivati, vaše funkcije takođe mogu staviti poruke u red čekanja -- poruke koje će vaša aplikacija primati od dispečera.

Šta je poenta svega ovoga? Zašto je bolje od starog načina? Prvo, program vođen događajima ima tendenciju da bude uređeniji. Znam, sve ste to već čuli. Svaka nova trendovska paradigma koju neko nadima ima istu tvrdnju. Ovo ćete morati sami da vidite. Prednosti arhitekture vođene događajima nisu mi bile očigledne sve dok nisam u nju preneo konvencionalni program. Stvari je postalo mnogo lakše za održavanje. To nije odgovor na svaki programski problem, ali će u nekim slučajevima olakšati dizajn i održavanje programa jer nameće svoj oblik strukture vašem kodu. Što više strukture, to bolje.

Drugo, ako se upustite u Vindovs programiranje, naći ćete se duboko uvučeni u programiranje zasnovano na događajima. U svetu Vindovs-a, kreirate prozor sa pridruženom funkcijom za obradu prozora koju obezbedite. Vindovs dispečer šalje poruke funkcijama za obradu prozora kad god se dogodi događaj koji bi mogao biti važan za vindovs. Na primer, kad god pomerite miš preko prozora, dispečer šalje prozoru poruku o tome. Kada unesete ključ, dispečer šalje poruku bilo kom prozoru koji je aktivan.

Paradigma vođena događajima je posebno primenljiva na Vindovs programiranje. Na primer, korisnik Vindovs-a može izabrati komandu menija jednom od nekoliko ručnih radnji. Kliknite na komandu mišem. Pritisnite taster za gas komande. Otvorite meni i pritisnite taster prečice. Ili pomerite kursor menija na komandu i pritisnite taster Enter. Ali vaš aplikativni program ne mari za sve to. Menadžer poruka se brine o tome. Kada korisnik odabere komandu, vaš program dobija poruku u tom smislu, samo jednu poruku, bez obzira na to kako je korisnik napravio izbor. Poruka govori vašem programu da je korisnik izabrao komandu. Štaviše, drugi deo vašeg programa može simulirati isti izbor komande jednostavnim slanjem iste poruke.

### Hardverski drajveri

Da bismo napravili naš program vođen događajima, prvo nam je potreban softver za prepoznavanje događaja kako bi mogli da postanu poruke. Program za hvatanje ekrana radiće sa tastaturom i mišem, a bavi se i video ekranom. Želite da premestite detalje hardvera dalje od koda aplikacije u softver menadžera događaja. Iako većina okruženja vođenih događajima dolazi u kompletu sa hardverskim drajverima, moraćemo da napravimo sopstvene. Zapamtite, međutim, da je poenta svega ovoga u kodu događaja i poruke koji dolazi kasnije.

### Tastatura i kursor

Liste One i Tvo su keis.h i getkei.c, funkcije koje upravljaju tastaturom i kursorom na ekranu. Možda ste videli slične module u drugim programima. Datoteka keis.h definiše neke vrednosti za ključeve koje će program koristiti. Takođe pruža prototipove za funkcije tastature i kursora. Datoteka getkei.c ima dve funkcije tastature i nekoliko kursora. Funkcija keihit vraća tačnu vrednost ako ste pritisnuli taster. Funkcija getkei čita taster sa tastature, pretvarajući funkcijske tastere u vrednosti iznad 128. Definicije tastera u keis.h odražavaju ovo ponašanje. Funkcije kursora se bave kursorom tastature, dozvoljavajući programu da pozicionira kursor, dobije njegovu trenutnu poziciju, sakrije ga, otkrije i sačuva i vrati kontekst kursora i konfiguraciju bilo kog drugog programa koji TSR prekine.

### Miš

Liste tri i četiri su mouse.h i mouse.c, hardverski drajveri za računarski miš. Datoteka mouse.h definiše prototipove i neke makroe. Datoteka mouse.c sadrži funkcije koje omogućavaju programu da odredi da li je miš instaliran, pročita ili podesi ekranske koordinate svog kursora, pročita njegove dugmad, uključi i isključi kursor i sačuva i vrati kontekst miša programa koji TSR prekida. Postoji više operacija mišem nego što ove funkcije podržavaju. Uključio sam samo one koje su potrebne programu. Microsoft Press objavljuje Reference Microsoft Mouse Programmer's u kojoj se navodi kako funkcionišu sve operacije miša.

### Upravljački program za poruke

Liste pet i šest su message.h i message.c, softver drajvera za poruke. Datoteka message.h definiše kodove poruka. Za ovaj primer, postoji samo 16 poruka. Više o njima kasnije. Aplikacija može dodati prilagođene poruke ovoj listi.

Postoje tri funkcije koje program poziva da bi koristio arhitekturu upravljačkih programa poruka vođenu događajima. Funkcija dispatch_message je modul za slanje poruka. Prosledite mu adresu funkcije obrade poruka vaše aplikacije, što je nevažeća funkcija koja očekuje da primi tri celobrojna parametra. Prvi parametar će biti kod poruke, a druga dva su generički parametri koje će poruke koristiti na bilo koji način koji im je potreban.

Pozivate funkciju post_message da biste ubacili poruke u red poruka. Dispečer će poslati ove poruke redosledom kojim se pojavljuju u redu. Poruke koje funkcija post_message objavljuje ne šalju se odmah. Da biste odmah poslali poruku, pozovite funkciju send_message. Koristili biste send_message kada poruka vraća vrednost ili kada njeni efekti moraju da se dese na licu mesta.

### Poruke

Dispečer šalje START poruku vašoj funkciji kada proces prvi put započne, a vaš program objavljuje STOP poruku da kaže programu u celini da bi se petlja za otpremanje poruka trebala završiti.

U ovom jednostavnom primeru arhitekture vođene događajima, ciklus poruka počinje i završava se i ima jedno odredište. U složenijim sistemima, kao što je Vindovs, postoji mnogo više poruka, različitih kategorija poruka i one se mogu poslati na mnogo različitih funkcija obrade. Na primer, možete imati nekoliko prozora na ekranu i svaki od njih može da obrađuje svoju kopiju poruka koje mu govore da se pokrene i zaustavi.

Dispečer šalje poruku TASTATURA kada korisnik pritisne taster. Prvi od dva parametra sadrži vrednost pritiska na taster.

Vi sami šaljete poruke CURRENT_KEBOARD_CURSOR i KEIBOARD_CURSOR. Poruka CURRENT_KEIBOARD_CURSOR vraća trenutne koordinate kursora u dva parametra. Prvi parametar je adresa gde će poruka kopirati Ks koordinate, a drugi parametar je adresa I koordinate. Poruka KEIBOARD_CURSOR menja lokaciju kursora. Prvi parametar je nova Ks koordinata, a drugi parametar je nova I koordinata.

Dispečer šalje RIGHT_BUTTON i LEFT_BUTTON poruke kada pritisnete desno ili levo dugme na mišu. Dokle god držite dugme pritisnuto, dispečer će nastaviti da šalje poruku. Dispečer šalje poruku MOUSE_MOVED kada pomerite miš, bez obzira da li je dugme pritisnuto ili ne. Kada otpustite dugme, dispečer šalje poruku BUTTON_RELEASED. U ove četiri poruke miša, prvi parametar sadrži koordinatu ekrana kolone (Ks), a drugi parametar sadrži koordinatu ekrana reda (I) gde je bio kursor miša kada se događaj desio.

Vaš program šalje poruke CURRENT_MOUSE_CURSOR, MOUSE_CURSOR, SHOV_MOUSE i HIDE_MOUSE. Poruka CURRENT_MOUSE_CURSOR vraća trenutne koordinate ekrana miša u dva parametra. Prvi parametar je adresa gde će poruka kopirati Ks koordinate, a drugi parametar je adresa I koordinate. Poruka MOUSE_CURSOR menja lokaciju kursora miša. Prvi parametar je nova Ks koordinata, a drugi parametar je nova I koordinata.

Poruke HIDE_MOUSE i SHOV_MOUSE sakrivaju i prikazuju kursor miša. Dva parametra su nula za obe poruke.

Postoje i druge poruke koje upravljački program miša može poslati. Možda ćete želeti da znate kada ste dvaput kliknuli na lokaciju, na primer. Upravljački program tastature može poslati status tastera Shift, Alt i Ctrl u drugom parametru. Mali skup poruka i događaja u ovom primeru služi da ilustruje princip, ali biste koristili mnogo više u stvarnoj arhitekturi vođenoj događajima.

Vaš program šalje VIDEO_CHAR poruku da preuzme znak koji se nalazi na ekranu. Parametri su koordinate ekrana Ks i I, a poruka vraća karakter.

Datoteka message.h definiše RECT strukturu, koja sadrži gornje leve i donje desne Ks/I koordinate ekrana. Poruke GET_VIDEORECT i PUT_VIDEORECT čitaju i pišu video podatke između ekrana i vašeg bafera. Prvi parametar je adresa RECT strukture koja ima koordinate pravougaonika, a drugi parametar je adresa bafera.

Obratite pažnju da je PARAM tipedef u message.h za parametre poruke ceo broj. Kada poruka očekuje adrese, morate ih prebaciti na tip PARAM. Ako koristite veliki model podataka, trebalo bi da promenite PARAM tipedef u dug ceo broj.

### Red poruka

Funkcije u message.c održavaju kružni red poruka. Funkcija post_message dodaje unos u taj red. Unosi u redu sadrže kod poruke i dva parametra. Collect_message funkcija ispituje hardver za događaje i postavlja poruke u red čekanja. Takođe prepoznaje kada se izvršava prvi put i objavljuje START poruku. Vraća tačnu vrednost ako poruke postoje u redu kada izađe. Vraća netačno ako je red prazan.

Funkcija dispatch_message je ona koju aplikacija poziva da bi poslala poruke u redu čekanja. Poziva collect_message da bi sve neprikupljene događaje stavio u red i da bi video da li ima poruka u redu za slanje aplikaciji. Ako je poruka u redu čekanja, dispatch_message uklanja poruku iz reda i poziva send_message da je pošalje funkciji obrade poruka aplikacije. Funkcija send_message takođe deluje na poruke kao što je MOUSE_CURSOR koje aplikacija šalje radi pokretanja hardvera. Zapazite da kada send_message pozove funkciju obrade poruka aplikacije, ona preduzima radnju u vezi sa porukama samo ako funkcija za obradu poruke vraća tačnu vrednost. Ovo omogućava aplikaciji da nadjača bilo koju podrazumevanu obradu poruka. U slučaju ovog primera, ta opcija se nikada ne koristi, ali je arhitektura tipična za sisteme vođene događajima. Funkcija send_message može da vrati vrednost pozivaocu. Na primer, poruka VIDEO_CHAR vraća video karakter.

Funkcija dispatch_message vraća tačnu vrednost svom pozivaocu bez obzira da li je pronašao poruku za slanje ili ne. Kada vidi da je upravo poslao STOP poruku, umesto toga vraća vrednost FALSE. Prema tome, aplikacijski program treba da nastavi da poziva dispatch_message sve dok funkcija vraća tačnu vrednost. Program bi trebalo da se zatvori kada dispatch_message vrati lažnu vrednost.

Funkcija mouse_event poziva funkcije miša za pravljenje poruka o događajima miša. Funkcija collect_messages je poziva, a mouse_event vraća sve događaje miša koji se dese. Funkcija čita poziciju miša i postavlja Ks i I koordinate u globalne promenljive. Funkcija collect_message će koristiti ove promenljive za izgradnju parametara za poruku koja će biti stavljena u red čekanja. Ako se pozicija miša promenila od poslednjeg puta kada je collect_message nazvana mouse_event, funkcija će vratiti poruku MOUSE_MOVED. Ako ste otpustili dugme miša od poslednje provere funkcije, funkcija vraća poruku BUTTON_RELEASED. Ako je desno ili levo dugme pritisnuto, funkcija vraća poruku RIGHT_BUTTON ili LEFT_BUTTON.

### Grabber ekrana

Možete videti da su funkcije poruke i događaja male. Nije potrebno mnogo koda za implementaciju jednostavnog okruženja vođenog događajima. Snaga pristupa je u tome kako ga koristite. [LISTING_SEVEN](#listing_-seven) je "copyscrn.c", aplikacija vođena događajima koja ilustruje upotrebu softvera za upravljanje događajima i porukama. Obratite pažnju na #ifdef TSR izjavu na početku copiscrn.c. Koristićemo taj uslovni izraz za vreme kompajliranja sledećeg meseca da instaliramo ovaj program kao rezidentni uslužni program.

Program počinje kreiranjem tekstualne datoteke diska pod nazivom „grab.dat“. Zatim vrši ponovljene pozive dispatch_message, prosleđujući adresu funkcije message_proc. Ovi pozivi se nastavljaju sve dok funkcija dispatch_message ne vrati lažnu vrednost, nakon čega funkcija copiscrn zatvara tekstualnu datoteku i vraća se.

Od ovog trenutka, zamislite ovaj program kao sistem vođen događajima. Jedini način na koji će se nešto desiti je kada korisnik uradi nešto pomoću tastature ili miša. Ovi događaji će postati poruke koje programska funkcija message_proc prima i obrađuje.

### Poruka START

Ako pogledate nazad u message.c, videćete da će prvi put kada copiscrn pozove dispatch_message, funkcija collect_message objaviti START poruku koja će biti poslata funkciji message_proc aplikacije. Koristi START poruku da mu kaže da započne obradu. U ovom slučaju, on šalje poruke koje čuvaju pozicije kursora tastature i miša i postavljaju oba kursora u gornji levi ugao ekrana.

### Poruke sa tastature

Sada program radi sa tastaturom i kursorima na ekranu iu gornjem levom uglu ekrana i sa otvorenom tekstualnom datotekom. Ništa se ne dešava ovde u zemlji aplikacije jer korisnik ništa ne radi. Ali dispečer užurbano posmatra tastaturu i miša za neku akciju. Pretpostavimo sada da korisnik pritisne taster na tastaturi. Pogledajte nazad u poruku.c. Funkcija collect_messages poziva funkciju tastera drajvera tastature da vidi da li je taster pritisnut. Kada keihit vrati pravu vrednost, collect_messages postavlja poruku KEIBOARD u red sa vrednošću vraćenom iz getkei kao prvim parametrom. Pošto je pokrenuta petlja za slanje poruka, send_message će na kraju poslati tu poruku funkciji message_proc u izvornoj datoteci copiscrn.c. Možete videti da funkcija message_proc obrađuje poruku KEIBOARD, radeći različite stvari u zavisnosti od vrednosti pritiska na taster. On prati tastere sa strelicama nagore, nadole, nadesno i nalevo; taster Esc; taster Enter; i taster F2. Ignorisaće sve druge ključeve. Možete da koristite tastere sa strelicama da pomerate kursor po celom ekranu. Kada pritisnete F2, kažete programu da želite da počnete da opisujete pravougaonik ekrana koji će upisati u datoteku. Lokacija kursora kada pritisnete F2 postaće jedan od uglova pravougaonika. Kada naknadno pomerite kursor, program definiše pravougaonik tako što menja video boje znakova unutar pravougaonika. Ako ponovo pritisnete F2, govorite programu da vam se ne sviđa taj pravougaonik. Obrnuti video pravougaonik nestaje i možete ponovo da pređete na novu početnu tačku i pritisnete F2. Kada je pravougaonik definisan, pritisnite taster Enter. Da biste zaboravili celu stvar, pritisnite taster Esc.

Funkcije napred, gore, nazad i dole upravljaju pozicijom kursora. Funkcija isticanja crta i ponovo iscrtava pravougaonik dok pomerate kursor. Funkcija setstart uključuje i isključuje označavanje na ekranu i pozicionira markere bloka.

### STOP poruka

Posmatrajte tretman tastera Enter ('\r') i tastera Esc u funkciji message_proc. Zapamtite da ovi ključevi prekidaju program pisanjem pravougaonika na disk ili ignorisanjem. Kada njihove poruke stignu, funkcija message_proc poziva post_message da objavi STOP poruku. Prvi parametar ima tačnu vrednost za taster Enter i lažnu vrednost za taster Esc. Ta poruka će, kao i sve poruke, na kraju biti poslata i na message_proc. Sada posmatrajte kako funkcija postupa sa STOP porukom. Ako je blok označen, funkcija poziva highlight da bi ga isključila. Ako je prvi parametar tačan, funkcija poziva funkciju vritescreen da upiše blok u datoteku diska. U oba slučaja, funkcija šalje poruke za vraćanje kursora miša i tastature na pozicije koje su imali kada je program počeo da radi. Zapamtite da funkcija dispatch_message koristi STOP poruku da joj kaže da vrati lažnu vrednost svom pozivaocu da zaustavi program. Ali to radi nakon što vaša funkcija za obradu poruka dobije pukotinu u STOP poruci.

### Poruke miša

Miš radi na sličan način kao i tastatura. Možete da pomerate miša dok se njegov kursor ne nađe na uglu - bilo kom uglu - pravougaonika koji želite da definišete. Pritisnite levi taster miša i držite ga dok pomerate kursor. Program će pratiti vaše kretanje i definisati pravougaonik. Otpustite dugme da biste zaustavili definisanje. Ako vam se ne sviđa da je pravougaonik definisan, pređite u novi ugao i ponovo pritisnite levo dugme. Da biste upisali pravougaonik na disk, pritisnite taster Enter ili desni taster miša. Taster Esc odbacuje pravougaonik i prekida program.

Kada pritisnete levo dugme, funkcija message_proc prima poruku LEFT_BUTTON. Sve dok držite to dugme pritisnuto, ta poruka će nastaviti da dolazi, tako da želite da uradite nešto sa njom samo prvi put. Ako program trenutno ne označava blok mišem, ova poruka ga pokreće tako što će sačuvati koordinate i pozvati setstart.

Funkcija message_proc dobija poruku MOUSE_MOVED kad god pomerite miš. Ako trenutno ne označavate pravougaonik, funkcija ignoriše poruku. Ako označavate, funkcija poziva jednu od istih funkcija unapred, nagore, unazad i nadole koje poruka TASTATURA koristi za definisanje pravougaonika.

Kada stigne poruka BUTTON_RELEASED, funkcija primećuje da više ne označava blok mišem. Kada se RIGHT_BUTTON pojavi u funkciji, radi istu stvar koju radi vrednost ključa Enter u poruci KEIBOARD -- objavljuje STOP poruku sa pravim prvim parametrom da kaže STOP poruci da napiše pravougaonik na ekranu.

Program vam omogućava da definišete pravougaonik pomoću miša, a zatim pritisnite F2 da definišete drugi pomoću tastature. Omogućava vam da kliknete mišem tokom definicije tastature da biste zamenili pravougaonik tastature i pokrenuli novi pomoću miša.

### Poruka je Medijum

Arhitektura ovog malog programa vođena događajima tipična je za većinu sistema vođenih događajima. Većina komponenti je ovde, iako u manjem obimu. Ne trebaju vam hardverski događaji da biste koristili arhitekturu vođenu događajima. Možete koristiti tradicionalne ulazno/izlazne funkcije i implementirati meke događaje za upravljanje tokom obrade programa. Ovo je još jedna tehnika, ona koja nudi drugačiji način gledanja na programiranje i koja izgleda da obećava da će uvesti red u složene probleme programiranja.

#### LISTING_ ONE

```c
/* ----------- keys.h ------------ */

#define TRUE  1
#define FALSE 0

#define ESC      27
#define F2      188
#define UP      200
#define FWD     205
#define DN      208
#define BS      203

int getkey(void);
int keyhit(void);
void curr_cursor(int *, int *);
void cursor(int, int);
void hidecursor(void);
void unhidecursor(void);
void savecursor(void);
void restorecursor(void);
void set_cursor_type(unsigned);
#define normalcursor() set_cursor_type(0x0607)
```

#### LISTING_ TWO

```c
/* ----------- getkey.c ---------- */

#include <bios.h>
#include <dos.h>
#include "keys.h"

#define KEYBOARD 0x16
#define ZEROFLAG 0x40
#define SETCURSORTYPE 1
#define SETCURSOR     2
#define READCURSOR    3
#define HIDECURSOR 0x20

static unsigned video_mode;
static unsigned video_page;
static int cursorpos;
static int cursorshape;

/* ---- Test for keystroke ---- */
int keyhit(void)
{
    _AH = 1;
    geninterrupt(KEYBOARD);
    return (_FLAGS & ZEROFLAG) == 0;
}

/* ---- Read a keystroke ---- */
int getkey(void)
{
    int c;
    while (keyhit() == 0)
        ;
    if (((c = bioskey(0)) & 0xff) == 0)
        c = (c >> 8) | 0x80;
    else
        c &= 0xff;
    return c;
}

static void videoint(void)
{
    static unsigned oldbp;
    _DI = _DI;
    oldbp = _BP;
    geninterrupt(0x10);
    _BP = oldbp;
}

void videomode(void)
{
    _AH = 15;
    videoint();
    video_mode = _AL;
    video_page = _BX;
    video_page &= 0xff00;
    video_mode &= 0x7f;
}

/* ---- Position the cursor ---- */
void cursor(int x, int y)
{
    videomode();
    _DX = ((y << 8) & 0xff00) + x;
    _AX = 0x0200;
    _BX = video_page;
    videoint();
}

/* ---- get cursor shape and position ---- */
static void near getcursor(void)
{
    videomode();
    _AH = READCURSOR;
    _BX = video_page;
    videoint();
}

/* ---- Get current cursor position ---- */
void curr_cursor(int *x, int *y)
{
    getcursor();
    *x = _DL;
    *y = _DH;
}

/* ---- Hide the cursor ---- */
void hidecursor(void)
{
    getcursor();
    _CH |= HIDECURSOR;
    _AH = SETCURSORTYPE;
    videoint();
}

/* ---- Unhide the cursor ---- */
void unhidecursor(void)
{
    getcursor();
    _CH &= ~HIDECURSOR;
    _AH = SETCURSORTYPE;
    videoint();
}

/* ---- Save the current cursor configuration ---- */
void savecursor(void)
{
    getcursor();
    cursorshape = _CX;
    cursorpos = _DX;
}

/* ---- Restore the saved cursor configuration ---- */
void restorecursor(void)
{
    videomode();
    _DX = cursorpos;
    _AH = SETCURSOR;
     _BX = video_page;
    videoint();
    set_cursor_type(cursorshape);
}

/* ----------- set the cursor type -------------- */
void set_cursor_type(unsigned t)
{
    videomode();
    _AH = SETCURSORTYPE;
     _BX = video_page;
    _CX = t;
    videoint();
}
```

#### LISTING_ THREE

```c
/* ------------- mouse.h ------------- */

#ifndef MOUSE_H
#define MOUSE_H

#define MOUSE 0x33

int mouse_installed(void);
int mousebuttons(void);
void get_mouseposition(int *x, int *y);
void set_mouseposition(int x, int y);
void show_mousecursor(void);
void hide_mousecursor(void);
int button_releases(void);
void intercept_mouse(void);
void restore_mouse(void);
void resetmouse(void);

#define leftbutton() (mousebuttons()&1)
#define rightbutton() (mousebuttons()&2)
#define waitformouse() while(mousebuttons());

#endif
```

#### LISTING_ FOUR

```c
/* ------------- mouse.c ------------- */

#include <stdio.h>
#include <dos.h>
#include <stdlib.h>
#include <string.h>
#include "mouse.h"

static void mouse(int m1,int m2,int m3,int m4)
{
    _DX = m4;
    _CX = m3;
    _BX = m2;
    _AX = m1;
    geninterrupt(MOUSE);
}

/* ---------- reset the mouse ---------- */
void reset_mouse(void)
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
    int bx = 0;
    if (mouse_installed())    {
        mouse(3,0,0,0);
        bx = _BX;
    }
    return bx & 3;
}

/* ---------- return mouse coordinates ---------- */
void get_mouseposition(int *x, int *y)
{
    if (mouse_installed())    {
        int mx, my;
        mouse(3,0,0,0);
        mx = _CX;
        my = _DX;
        *x = mx/8;
        *y = my/8;
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
    int ct = 0;
    if(mouse_installed())    {
        mouse(6,0,0,0);
        ct = _BX;
    }
    return ct;
}

static int mx, my;

/* ----- intercept the mouse in case an interrupted program is using it ---- */
void intercept_mouse(void)
{
    if (mouse_installed())    {
        _AX = 3;
        geninterrupt(MOUSE);
        mx = _CX;
        my = _DX;
        _AX = 31;
        geninterrupt(MOUSE);
    }
}

/* ----- restore the mouse to the interrupted program ----- */
void restore_mouse(void)
{
    if (mouse_installed())    {
        _AX = 32;
        geninterrupt(MOUSE);
        _CX = mx;
        _DX = my;
        _AX = 4;
        geninterrupt(MOUSE);
    }
}
```

#### LISTING_ FIVE

```c
/* ----------- message.h ------------ */

#ifndef MESSAGES_H
#define MESSGAES_H

#define MAXMESSAGES 50

/* --------- event message codes ----------- */
enum messages {
    START,
    STOP,
    KEYBOARD,
    RIGHT_BUTTON,
    LEFT_BUTTON,
    MOUSE_MOVED,
    BUTTON_RELEASED,
    CURRENT_MOUSE_CURSOR,
    MOUSE_CURSOR,
    SHOW_MOUSE,
    HIDE_MOUSE,
    KEYBOARD_CURSOR,
    CURRENT_KEYBOARD_CURSOR,
    VIDEO_CHAR,
    PUT_VIDEORECT,
    GET_VIDEORECT
};

/* ------- defines a screen rectangle ------ */
typedef struct {
    int x, y, x1, y1;
} RECT;

/* ------ integer type for message parameters ----- */
typedef int PARAM;

void post_message(enum messages, PARAM, PARAM);
int send_message(enum messages, PARAM, PARAM);
int dispatch_message(int (*)(enum messages, PARAM, PARAM));
RECT rect(int, int, int, int);

#endif
```

#### LISTING_ SIX

```c
/* --------- message.c ---------- */

#include <stdio.h>
#include <dos.h>
#include <conio.h>
#include "mouse.h"
#include "keys.h"
#include "message.h"

static int mouse_event(void);

static int px = -1, py = -1;
static int mx, my;
static int first_dispatch = TRUE;

static struct msgs {
    enum messages msg;
    PARAM p1;
    PARAM p2;
} msg_queue[MAXMESSAGES];

static int qonctr;
static int qoffctr;
static int (*mproc)(enum messages,int,int);

/* ----- post a message and parameters to msg queue ---- */
void post_message(enum messages msg, PARAM p1, PARAM p2)
{
    msg_queue[qonctr].msg = msg;
    msg_queue[qonctr].p1 = p1;
    msg_queue[qonctr].p2 = p2;
    if (++qonctr == MAXMESSAGES)
        qonctr = 0;
}

/* --------- clear the message queue --------- */
static void clear_queue(void)
{
    px = py = -1;
    mx = my = 0;
    first_dispatch = TRUE;
    qonctr = qoffctr = 0;
}

/* ------ collect events into the message queue ------ */
static int collect_messages(void)
{
    /* ---- collect any unqueued messages ---- */
    enum messages event;
    if (first_dispatch)    {
        first_dispatch = FALSE;
        reset_mouse();
        show_mousecursor();
        send_message(START,0,0);
    }
    if ((event = mouse_event()) != 0)
        post_message(event, mx, my);
    if (keyhit())
        post_message(KEYBOARD, getkey(), 0);
    return qoffctr != qonctr;
}

int send_message(enum messages msg, PARAM p1, PARAM p2)
{
    int rtn = 0;
    RECT rc;
    if (mproc == NULL)
        return -1;
    if (mproc(msg, p1, p2))    {
        switch (msg)    {
            case STOP:
                hide_mousecursor();
                clear_queue();
                mproc = NULL;
                break;
            /* -------- keyboard messages ------- */
            case KEYBOARD_CURSOR:
                unhidecursor();
                cursor(p1, p2);
                break;
            case CURRENT_KEYBOARD_CURSOR:
                curr_cursor((int*)p1,(int*)p2);
                break;
            /* -------- mouse messages -------- */
            case SHOW_MOUSE:
                show_mousecursor();
                break;
            case HIDE_MOUSE:
                hide_mousecursor();
                break;
            case MOUSE_CURSOR:
                set_mouseposition(p1, p2);
                break;
            case CURRENT_MOUSE_CURSOR:
                get_mouseposition((int*)p1,(int*)p2);
                break;
            /* ----------- video messages ----------- */
            case VIDEO_CHAR:
                gettext(p1+1, p2+1, p1+1, p2+1, &rtn);
                rtn &= 255;
                break;
            case PUT_VIDEORECT:
                rc = *(RECT *) p1;
                puttext(rc.x+1, rc.y+1, rc.x1+1, rc.y1+1,(char *) p2);
                break;
            case GET_VIDEORECT:
                rc = *(RECT *) p1;
                gettext(rc.x+1, rc.y+1, rc.x1+1, rc.y1+1,(char *) p2);
                break;
            default:
                break;
        }
    }
    return rtn;
}

/* ---- dispatch messages to the message proc function ---- */
int dispatch_message(
    int (*msgproc)(enum messages msg,PARAM p1,PARAM p2))
{
    mproc = msgproc;
    /* ------ dequeue the next message ----- */
    if (collect_messages())  {
        struct msgs mq = msg_queue[qoffctr];
        send_message(mq.msg, mq.p1, mq.p2);
        if (++qoffctr == MAXMESSAGES)
            qoffctr = 0;
        if (mq.msg == STOP)
               return FALSE;
    }
    return TRUE;
}

/* ---------- gather and interpret mouse events -------- */
static int mouse_event(void)
{
    get_mouseposition(&mx, &my);
    if (mx != px || my != py)  {
        px = mx;
        py = my;
        return MOUSE_MOVED;
    }
    if (button_releases())
        return BUTTON_RELEASED;
    if (rightbutton())
        return RIGHT_BUTTON;
    if (leftbutton())
        return LEFT_BUTTON;
    return 0;
}

/* ----------- make a RECT from coordinates --------- */
RECT rect(int x, int y, int x1, int y1)
{
    RECT rc;
    rc.x = x;
    rc.y = y;
    rc.x1 = x1;
    rc.y1 = y1;
    return rc;
}
```

#### LISTING_ SEVEN

```c
/* ---------------- copyscrn.c -------------- */
#include <stdio.h>
#include <stdlib.h>
#include "keys.h"
#include "message.h"

static int message_proc(enum messages, int, int);
static void near highlight(RECT);
static void writescreen(RECT);
static void near setstart(int *, int, int);
static void near forward(int);
static void near backward(int);
static void near upward(int);
static void near downward(int);
static void init_variables(void);

static int cursorx, cursory;    /* Cursor position       */
static int mousex, mousey;      /* Mouse cursor position */

static RECT blk;
static int kx = 0, ky = 0;
static int px = -1, py = -1;
static int mouse_marking = FALSE;
static int keyboard_marking = FALSE;
static int marked_block = FALSE;
static FILE *fp;

#ifdef TSR
#define main tsr_program
#endif

/* ---------- enter here to run screen grabber --------- */
void main(void)
{
    fp = fopen("grab.dat", "wt");
    if (fp != NULL)    {
        /* ----- event message dispatching loop ---- */
        while(dispatch_message(message_proc))
            ;
        fclose(fp);
    }
}

/* --------- event-driven message processing function ------- */
static int message_proc(
    enum message message,   /* message */
    int param1,             /* 1st parameter */
    int param2)             /* 2nd parameter */
{
    int mx = param1;
    int my = param2;
    int key = param1;

    switch (message)    {
      case START:
      init_variables();
      post_message(CURRENT_KEYBOARD_CURSOR,(PARAM) &cursorx, (PARAM) &cursory);
      post_message(KEYBOARD_CURSOR, 0, 0);
      post_message(CURRENT_MOUSE_CURSOR,(PARAM) &mousex, (PARAM) &mousey);
      post_message(MOUSE_CURSOR, 0, 0);
            break;
      case KEYBOARD:
      switch (key)    {
                case FWD:
                    if (kx < 79)    {
                        if (keyboard_marking)
                            forward(1);
                        kx++;
                    }
                    break;
                case BS:
                    if (kx)    {
                        if (keyboard_marking)
                            backward(1);
                        --kx;
                    }
                    break;
                case UP:
                    if (ky)    {
                        if (keyboard_marking)
                            upward(1);
                        --ky;
                    }
                    break;
                case DN:
                    if (ky < 24)    {
                        if (keyboard_marking)
                            downward(1);
                        ky++;
                    }
                    break;
                case F2:
                    mouse_marking = FALSE;
                    setstart(&keyboard_marking, kx, ky);
                    break;
                case '\r':
                    post_message(STOP, TRUE, 0);
                    break;
                case ESC:
                    post_message(STOP, FALSE, 0);
                    break;
      }
      send_message(KEYBOARD_CURSOR, kx, ky);
      break;
      case LEFT_BUTTON:
            if (!mouse_marking)    {
                px = mx;
                py = my;
                keyboard_marking = FALSE;
                setstart(&mouse_marking, mx, my);
      }
      break;
      case MOUSE_MOVED:
            if (mouse_marking)    {
                if (px < mx)
                    forward(mx-px);
                if (mx < px)
                    backward(px-mx);
                if (py < my)
                    downward(my-py);
                if (my < py)
                    upward(py-my);
                px = mx;
                py = my;
      }
      break;
      case BUTTON_RELEASED:
            mouse_marking = FALSE;
            break;
      case RIGHT_BUTTON:
            post_message(STOP, TRUE, 0);
            break;
      case STOP:
      if (marked_block)    {
                highlight(blk);
                if (param1)
                    writescreen(rect(min(blk.x, blk.x1),min(blk.y, blk.y1),
                                     max(blk.x, blk.x1),max(blk.y, blk.y1)));
            }
            send_message(MOUSE_CURSOR, mousex, mousey);
            send_message(KEYBOARD_CURSOR, cursorx, cursory);
            init_variables();
            break;
        default:
            break;
    }
    return TRUE;
}

/* ------- set the start of block marking ------- */
static void near setstart(int *marking, int x, int y)
{
    if (marked_block)
        highlight(blk);      /* turn off old block */

    marked_block = FALSE;
    *marking ^= TRUE;
    blk.x1 = blk.x = x;   /* set the corners of the new block */
    blk.y1 = blk.y = y;
    if (*marking)
        highlight(blk);   /* turn on the new block */
}

/* ----- move the block rectangle forward one position ----- */
static void near forward(int n)
{
    marked_block = TRUE;
    while (n-- > 0)    {
        if (blk.x < blk.x1)
            highlight(rect(blk.x,blk.y,blk.x,blk.y1));
        else
            highlight(rect(blk.x+1,blk.y,blk.x+1,blk.y1));
        blk.x++;
    }
}

/* ---- move the block rectangle backward one position ----- */
static void near backward(int n)
{
    marked_block = TRUE;
    while (n-- > 0)    {
        if (blk.x > blk.x1)
            highlight(rect(blk.x,blk.y,blk.x,blk.y1));
        else
            highlight(rect(blk.x-1,blk.y,blk.x-1,blk.y1));
        --blk.x;
    }
}

/* ----- move the block rectangle up one position ----- */
static void near upward(int n)
{
    marked_block = TRUE;
    while (n-- > 0)    {
        if (blk.y > blk.y1)
            highlight(rect(blk.x,blk.y,blk.x1,blk.y));
        else
            highlight(rect(blk.x,blk.y-1,blk.x1,blk.y-1));
        --blk.y;
    }
}

/* ----- move the block rectangle down one position ----- */
static void near downward(int n)
{
    marked_block = TRUE;
    while (n-- > 0)    {
        if (blk.y < blk.y1)
            highlight(rect(blk.x,blk.y,blk.x1,blk.y));
        else
            highlight(rect(blk.x,blk.y+1,blk.x1,blk.y+1));
        blk.y++;
    }
}

/* ------ write the rectangle to the file ------- */
static void writescreen(RECT rc)
{
    int vx = rc.x;
    int vy = rc.y;
    while (vy != rc.y1+1)    {
        if (vx == rc.x1+1)    {
            fputc('\n', fp);
            vx = rc.x;
            vy++;
        }
        else    {
            fputc(send_message(VIDEO_CHAR, vx, vy), fp);
            vx++;
        }
    }
}

/* ------- simple swap macro ------ */
#define swap(a,b) {int s=a;a=b;b=s;}

/* -------- invert the video of a defined rectangle ------- */
static void near highlight(RECT rc)
{
    int *bf, *bf1, bflen;
    if (rc.x > rc.x1)
        swap(rc.x,rc.x1);
    if (rc.y > rc.y1)
        swap(rc.y,rc.y1);
    bflen = (rc.y1-rc.y+1) * (rc.x1-rc.x+1) * 2;
    if ((bf = malloc(bflen)) != NULL)    {
        send_message(HIDE_MOUSE, 0, 0);
        send_message(GET_VIDEORECT, (PARAM) &rc, (PARAM) bf);
        bf1 = bf;
        bflen /= 2;
        while (bflen--)
            *bf1++ ^= 0x7700;
        send_message(PUT_VIDEORECT, (PARAM) &rc, (PARAM) bf);
        send_message(SHOW_MOUSE, 0, 0);
        free(bf);
    }
}

/* ---- initialize global variables for later popup ---- */
static void init_variables(void)
{
    mouse_marking = keyboard_marking = FALSE;
    kx = ky = blk.x = blk.y = blk.x1 = blk.y1 = 0;
    px = py = -1;
    mouse_marking = FALSE;
    keyboard_marking = FALSE;
    marked_block = FALSE;
}
```

Copyright © 1991, Dr. Dobb's Journal
