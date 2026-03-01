
# C PROGRAMMING 9007

## Turbo C++, lepljenje tokena i brzi tasteri za računar - Al Stivens

### Turbo C++

Vest ovog meseca je Turbo C++. Nakon njihovog uspeha sa objektno orijentisanim Pascalom 5.5, Borland je napao C++ objektno orijentisano tržište sa vatrenim oružjem. Prvobitno zamišljen da bude dodatak Turbo C 3.0, C++ lice Borlandovog kompajlera sada je u njegovom prvom planu. Naglašavajući objektno orijentisanu stranu svog najpopularnijeg kompajlerskog proizvoda, Borland se obavezao na objektno orijentisano programiranje kao paradigmatski talas sutrašnjice.

Uz Turbo C++ su uključeni C++ kompajler, ANSI-usklađeni standardni C kompajler i potpuno revidirano Integrisano razvojno okruženje. C++ kompajler je usklađen sa AT&T 2.0 jezičkom specifikacijom, svojevrsnim standardom, ali i dalje pokretna meta. ANSI je pokrenuo svoj odbor za standarde Ks3J16 C++ i čućemo više o tom poduhvatu u mesecima i godinama koje dolaze. Do tada, dobavljači proizvoda na jeziku C++ imaju za cilj AT&T referentni priručnik i AT&T implementaciju C++.

Većina C++ programskih sistema dostupnih korisnicima računara su portovi AT&T-ovog cfront programa, prevodioca koji čita C++ izvorni kod i emituje izvorni kod jezika C. Značajan izuzetak do sada je bio Zortech C++, implementacija koja je po duhu C++ 2.0, ali koja ima brojna odstupanja od AT&T implementacije. Borland je pokušao da se blisko pridržava AT&T referentnog priručnika.

Ovu kolumnu pišem u aprilu, mesec dana pre zakazane najave Turbo C++-a. Čitate ga negde sredinom juna, mesec dana nakon najave, pod pretpostavkom da nema odlaganja. Uvek postoje prepreke za blagovremeno izveštavanje kada radite u okviru ovakvog rasporeda. Moje iskustvo sa Turbo C++ stekao sam, dakle, samo u betalandu. Iz tog razloga ne mogu prijaviti nikakve probleme sa proizvodom. Naravno, bilo je problema, ali ovo je beta kopija i za to služi beta test.

Upravo sam završio pisanje knjige pod nazivom Nauči se C++. Dok ovo pročitate, Herb Šild će bez sumnje pisati jedan sa istim naslovom. Nemojte da vas zavaraju imitacije. Moj ima žuti poklopac. (Izvini, Herb.) Moja knjiga ima oko 130 programa vežbanja. Svaka vežba pokazuje specifične karakteristike jezika C++. Kao takva, knjiga je nešto poput manjeg C++ testa za mučenje, a ja sam koristio dva porta AT&T cfront programa da potvrdim vežbe. Turbo C++, a ne front port, prošao je sjajno čak iu svojoj beta konfiguraciji.

Postoje dva različita kompajlera u proizvodu koji se zove Turbo C++. Drugi kompajler je potpuni ANSI C kompajler. Oba kompajlera se izvršavaju iz integrisanog razvojnog okruženja ili iz komandne linije. Program otkriva kakav program kompajlira na osnovu ekstenzije izvorne datoteke. .C datoteka je C program. .CPP datoteka je C++ program. Kompajler treba da zna razliku da bi znao kako da se nosi sa određenim suptilnim razlikama u jezicima. Jedna od tih razlika uključuje „veze bezbedne za tipove“.

### Veze bezbedne za tip

Bezbedan tip znači da deklaracija funkcije i njeni pozivaoci koriste kompatibilne tipove za parametre. Već neko vreme postoji mera provere tipa u C. C program specificira tip povratka funkcije i listu parametara u prototipu funkcije. Ako deklaracija funkcije ili poziv funkcije ne odgovara specifikaciji prototipa, kompajler objavljuje grešku. U starim danima pre prototipova pretpostavljalo se da funkcija vraća ceo broj osim ako nije deklarisano da vraća nešto drugo. Možete mu proslediti bilo koji broj i tipove parametara bez obzira na to šta se očekivalo. Prevodilac nije mario. Programer je morao da se bori sa greškama koje su nastale kada je program prošao jednu stvar, a funkcija očekivala nešto drugo. C++-u je potrebno jače kucanje i tako je njegov dizajner kreirao prototip funkcije, koji su usvojili mnogi kompajleri C, a na kraju i odbor za standarde ANSI C. Prototip omogućava kompajleru da osigura da se pozivi funkcije podudaraju sa deklaracijom funkcije u odnosu na njen tip vraćanja i listu parametara.

C prototip je efikasna mera sve dok izvorna datoteka koja deklariše funkciju i ona koja je poziva koriste istu kopiju prototipa. Po dogovoru, većina programera će staviti prototip u datoteku zaglavlja i uključiti zaglavlje u izvornu datoteku koja deklarira funkciju i sve ostale koji pozivaju funkciju. Ali ova praksa je samo konvencija i ništa u jeziku C je ne sprovodi. Ako koristite različite prototipove za istu funkciju u različitim izvornim datotekama, pozivi funkcije mogu imati različite konfiguracije sa istim nepoželjnim rezultatima koje smo imali pre prototipova. Problem je, naravno, u tome što izvorne datoteke kompajlirate nezavisno, a linker nema načina da uskladi različite prototipove funkcija između nezavisno kompajliranih objektnih modula. Tradicionalni metod za kontrolisanje ove situacije je korišćenje gore pomenute konvencije zaglavlja i dobro definisane datoteke izrade koja obezbeđuje da se sve izvorne datoteke na koje utiče da se kompajliraju kada se promeni datoteka zaglavlja koje uključuju.

C++ zahteva jaču proveru tipa u povezanim modulima jer veliki deo integriteta programa zavisi od ispravne upotrebe preopterećenih funkcija. Stoga, C++ 2.0 uvodi funkciju koja se zove „povezivanje bez opasnosti od tipa“ da bi se garantovalo da su prototipovi funkcija konzistentni u nezavisno kompajliranim izvornim datotekama. Da bi se postigla ova garancija, C++ kompajler prevodi imena funkcija u „iskvarena“ imena. Prevodilac dodaje znakove imenu funkcije da bi kodirao listu parametara. Na primer, sledeći prototip funkcije bi generisao sledeće iskrivljeno ime funkcije.

```c
  long foo(int, char*, struct tm);   // mangled name: _foo_FiPc2tm
```

Iskrivljeno ime specificira, u kriptičnoj notaciji, tipove parametara. Spoljna funkcija, prema tome, nema naziv koji ste joj dali. Ako ga prototipujete drugačije u udaljenoj izvornoj datoteci, iskrivljeno ime će biti drugačije i dobićete nerešene simbole kada povežete program. Tako imate vezu bezbednu za tip na nivou liste parametara. Iz nekog razloga, ova tehnika ne kodira na sličan način povratni tip funkcije.

Ova iskrivljena imena su obično nevidljiva programeru osim ako ne pročitate mapu memorije veze, ali morate znati za njih jer će nerešene greške simbola prijaviti iskrivljena imena, a ne ona koja ste s ljubavlju dali i morate pronaći originalno ime među iskrivljenim neredom. Biće trenutaka kada ćete morati da kažete C++ da se ne kvari.

Pretpostavimo da vaš C++ program poziva funkciju koju je kompajlirao C kompajler. To nije neobična stvar. Celokupna standardna biblioteka C funkcija dostupna je C++ programima. Dakle, osim ako C++ kompajleru ne kažete drugačije, on će pokvariti ime funkcije u pozivu, a linker neće pronaći odgovarajuću funkciju. Stoga, prevodiocu C++-a je potreban način da zna da je imenovanu funkciju kompajlirao C kompajler. C++ koristi ovu konstrukciju da bi postigao tu svrhu.

```c
extern "C" 
{ 
// C things . . . 
}
```

Možete staviti prototipove između vitičastih zagrada, a kompajler zna da se pozivi tim funkcijama moraju obaviti bez iskrivljenih imena. C++ kompajleri obično uključuju standardne C datoteke zaglavlja koje već imaju ugrađenu eksternu „C“ naredbu.

Ako celu funkciju okružite spoljnim "C" zagradama, ta funkcija će se kompajlirati sa C vezama. To biste morali da uradite u slučaju kada koristite funkciju biblioteke C koja zahteva da vaš program obezbedi imenovanu funkciju. Naravno, takve funkcije ne uživaju istu vezu bezbednu za tipove koju imaju obične C++ funkcije.

### Lepljenje ANSI C tokena

Pre nekoliko meseci sam se pitao o operaciji lepljenja tokena ## predprocesora ANSI C. Ako kodirate ## operator u listi za zamenu makroa, on zamenjuje dva parametra koja ga okružuju tako što „lepi“ dva argumenta zajedno u jedan argument i zatim nastavlja sa prethodnom obradom. na primer:

```c
#define pasteup(a,b) a ## b   
foo(pasteup(12, 34));
```

The resulting statement would be:

```c
foo(1234);
```

Teško je zamisliti upotrebu ove funkcije. Budući da je nužnost majka, možemo samo da nagađamo da je izum ## proizašao iz nedra potrebe. Ponašanje ## je čudnije kada uzmete u obzir ovo: pasteup(hello,dolli)(); Efektivna izjava je: hellodolli(); što nas navodi da sumnjamo da bi ## mogao biti od neke koristi u izradi identifikatora predprocesora.

Ponašanje ## je dobro dokumentovano u ANSI C standardu, ali nema dobrih primera njegove upotrebe ili naznaka njegove svrhe. To mi je smetalo jer nisam mogao da smislim dobru upotrebu za to. Dakle, kao eksperiment, otišao sam da tražim aplikaciju za čudno malo stvorenje. Bilo bi nesportsko nazvati nekoga iz Ks3J11 komiteta i pitati za šta je to, ali želeo sam da pronađem stvarnu upotrebu za lepljenje tokena, i na kraju sam to i uradio. Ono što sam našao, međutim, uopšte nije bio kod koji je koristio ANSI token paster. U stvari, ono što sam našao nije bilo u C programu. Umesto toga, pronašao sam izmišljotinu u datoteci zaglavlja generic.h koja dolazi sa C++ 2.0 kompajlerima. Sledeća konstrukcija radi sa programima pre-ANSI C i C++ preprocesora:

```c
#define name2(a,b) a\   
b
```

Svrha ovog makroa i sličnih mu je da zalepe imena tipova i klasa zajedno da bi se formirala ono što se naziva "parametrizovana" klasa od generičke. U Programming in C++, Prentice Hall, 1989, Devhurst i Stark opisuju ono što nazivaju „generičnost“, još jednu odvratnu nereč koju je neko, iz velikodušnosti i u pravom duhu kompjuterske literature, doprineo svetu engleskog govornog područja. Tamo primećuju da kompajleri usklađeni sa ANSI mogu postići istu stvar sa ovim:

```c  
#define name2(a,b) a##b
```

Eureka! Sada brz pogled na generic.h Turbo C++-a otkriva da oni zaista koriste ## operator upravo na taj način i moja potraga je završena.

Moji originalni komentari o ## došli su u recenziji knjige Reka Jaeschkea, Savladavanje standarda C. Neki čitaoci su zvali da kažu da ne mogu da lociraju knjigu ili izdavača. Evo adrese: Professional Press, 101 Vitmer Rd., Horsham, PA 19044, 215-957-1500.

### Vrući tasteri

Svi smo koristili programe koji koriste prečice. Na računaru, interventni tasteri su kombinacije tastera koje program koristi da izazove stvari. Najtipičnija upotreba je da se TSR pojavi. Po definiciji, interventni taster bi trebalo da bude kombinacija tastera koju aplikacije ne koriste u svom normalnom skupu komandi. Po konvenciji, interventni tasteri su kombinacije koje ne proizvode ništa na ekranu u normalnoj upotrebi DOS-a i koje nisu jedan od generičkih funkcijskih tastera. Sidekick koristi kombinaciju Ctrl-Alt. Readi koristi 5 na numeričkoj tastaturi (sa isključenim NumLock-om).

Programer mora da reši tri problema u vezi sa prečicama. Prvo, kako da pročitate jedan sa tastature da biste omogućili korisniku da promeni postavku prečice? Drugo, kako to prevesti u smislen prikaz da biste korisniku rekli koja je kombinacija tastera? Treće, kako znate kada je pritisnut preči taster? Kod koji prati ovomesečnu kolonu rešava te probleme.

[LISTING_ONE](#listing_one) je "hotkey.h", datoteka zaglavlja koju će aplikacijski program uključiti da koristi funkcije prečice. Sadrži prototipove.

[LISTING_TWO](#listing_two) je "testhk.c", program koji demonstrira upotrebu funkcija prečice. Prečice su opisane u smislu njihovog koda za skeniranje tastature i maske BIOS tastera za prebacivanje. Svaki taster na tastaturi šalje jedinstveni kod za skeniranje računaru kada pritisnete taster. Ovi kodovi nisu ASCII i naći ćete ih dokumentovane u mnogim knjigama o programiranju računara. BIOS Shift maska tastera je bajt u maloj RAM memoriji koji predstavlja trenutni status tastera Shift, Ctrl i Alt. Softver za prečice se brine o prevođenju kodova u vrednosti ključeva.

Program "testhk.c" počinje prikazivanjem ovih poruka da bi opisao šta očekuje da uradite:

Press a hot key
Press Esc to keep the one you have
Press the Space Bar to clear the one you have
Press Enter to use the new one

Zatim poziva funkciju interventnog tastera, prosleđujući adrese celog broja za kod za skeniranje i jedne za masku tastera za smenu. Funkcija interventnih tastera omogućava korisniku da pritisne različite kombinacije interventnih tastera, prikazujući svaku od njih dok korisnik ne pritisne Enter, Esc ili razmaknicu.

Kada korisnik odabere interventni taster, program testhk prikazuje njegovu vrednost pozivanjem funkcije shovkei, koja prikazuje nešto poput Ctrl-Ks na ekranu. Zatim testhk poziva initkei da bi inicijalizovao testiranje za hot taster. Funkcija iskeihit vraća nulu ako je pritisnut preči taster i nulu ako nije. Program testhk se ponavlja čekajući na pritisak prečice. Zatim poziva endkey.

Važno je da program pozove initkei pre testiranja da li se pritisne preči taster i zatim da se završi. Ne izostavljajte ih.

[LISTING_THREE](#listing_three) je "hotkey.c", funkcije koje upravljaju programiranjem prečice za vašu aplikaciju. Funkcija initkei povezuje vektor prekida tastature na rutinu usluge prekidanja nevkb. Ta funkcija jednostavno čita ulazni port sa tastature u promenljivu pod nazivom kbval i povezuje stari vektor prekida. Na nekim računarima vam nije potrebna rutina usluge prekida. Gde god program ispituje vrednost u kbval, možete jednostavno pročitati ulazni port sa tastature 0k60. Drugi računari ne rade na taj način. Ulazni port isporučuje značajnu vrednost tek nakon prekida 9. Stoga se program povezuje sa vektorom prekida 9 da bi pročitao vrednost porta tastature.

Funkcija iskeihit prihvata kod za skeniranje i masku tastera shift i testira ih u odnosu na trenutne vrednosti kbval i BIOS masku tastera za prebacivanje. Funkcija vraća true ako se vrednosti podudaraju i false u suprotnom.

Funkcija interventnog tastera programira interventni taster. Kada korisnik pritisne važeću kombinaciju prečice, funkcija prikazuje njen ASCII prikaz na ekranu i čeka na drugi pritisak na taster. Kada korisnik pritisne Enter, vrednosti skeniranja i maske za pomeranje povezane sa poslednjim tasterom se vraćaju pozivaocu. Ako korisnik pritisne taster Esc, vraćaju se originalne vrednosti pozivaoca. Ako korisnik pritisne razmaknicu, vraćaju se nule.

Čitanje vrućeg tastera nije tortura. Ni DOS ni BIOS ne vraćaju ništa kada pritisnete jednu od kombinacija tastera za koje smo rekli da su validne prečice. Ono što morate da uradite je da ispitate unos sa porta za tastaturu i reagujete u skladu sa kodovima za skeniranje koji dolaze. Prva stvar koju treba da uradite je da sačekate dok korisnik ne siđe sa tastature. Najznačajniji bit (0k80) koda za skeniranje biće nula ako se drži pritisnut bilo koji taster. Inače će biti jedan. Zatim morate obrisati BIOS bafer za čitanje unapred i držati ga čistim. Svaki put kada korisnik pritisne važeći BIOS taster, BIOS dodaje unos u kružni bafer za čitanje unapred. Brisanjem tog bafera sprečavate da BIOS oglašava bip kada je bafer pun. Zatim ulazite u petlju koja čita port za tastaturu i čeka da se pritisne taster. Ako je taj taster kod za skeniranje za tastere Esc, Enter ili razmaknicu, gotovi ste. U suprotnom, to mora biti taster Ctrl, Alt ili Shift. Nakon toga sačekajte da se pritisne drugi taster. Zatim sačekate treći pritisak na taster ili otpuštanje tastera i tada imate dva ili tri koda za skeniranje koji čine interventni taster. Od njih je jednostavno potvrditi kombinaciju.

Korisnik može pokušati da programira nevažeći preči taster. Ne biste želeli da koristite slovo A ili taster Home kao prečice, na primer. Interventni taster mora da bude kombinacija koja uključuje jedan ili više tastera Shift, Ctrl i Alt i još jedan taster. Ili važeći interventni taster može biti bilo koja dva ili više tastera Shift, Ctrl i Alt. Ako korisnik odabere bilo šta drugo, program interventnih tastera zuji i odbija izbor.

Funkcija shovkei vraća pokazivač na string za prikaz koji konstruiše iz svog koda za skeniranje i parametara maske tastera za pomeranje. Možete da koristite ovaj niz za prikaz imena prečice.

Ove funkcije kompajliraju pomoću Turbo C-a. Da biste ih koristili sa drugim kompajlerom, morate koristiti verziju setvect, getvect, sound, delai i nosound tog kompajlera. Većina kompajlera ima varijacije na prva dva. Microsoft C koristi _dos_getvect i _dos_setvect, na primer. Ostala tri su specifična za Turbo C. Možete koristiti bilo koji metod koji vaš kompajler ima da pravi buku kroz zvučnik ili možete isključiti funkciju zujanja.

#### LISTING_ONE

```c
/* ----------- hotkey.h ---------- */

char *showkey(unsigned keyscan, unsigned keymsk);
int iskeyhit(char keyscan, char keymsk);
void hotkey(unsigned *keyscan, unsigned *keymsk);
void initkey(void);
void endkey(void);
```

#### LISTING_TWO

```c
/* -------------- testhk.c --------------- */

/*
 * A program to demonstrate the use of the hotkey programmer
 */

#include <stdio.h>
#include "hotkey.h"

void main()
{
    unsigned scan = 0;
    unsigned mask = 0;

    printf("\nPress a hot key");
    printf("\nPress Esc to keep the one you have");
    printf("\nPress the Space Bar to clear the one you have");
    printf("\nPress Enter to use the new one\n");

    /* ------- program the hot key ---------- */
    hotkey(&scan, &mask);

    if (scan || mask)    {
        /* ------- display the programmed hot key --------- */
        printf("\nThe new hot key is %s (%02x, %02x)\n",
            showkey(scan, mask), scan, mask);

        initkey();
        while (!iskeyhit(scan, mask))
            printf("\rWaiting for you to press the hot key..");
        endkey();

        printf("\n%s was pressed", showkey(scan, mask));
    }
    else
        printf("\nNo hot key programmed");
}
```

#### LISTING_THREE

```c
/* ---------- hotkey.c ------------ */

#include <stdio.h>
#include <conio.h>
#include <bios.h>
#include <dos.h>
#include <string.h>
#include "hotkey.h"

#define KYBRD 9

/*
 * Program a hot key
 */

static char hotky[50];

static int multibits(int k);
static void clearkb(void);
static void buzz(void);

char *scodes[] = {
    "Esc",
    "1", "2", "3", "4", "5", "6", "7", "8", "9", "0",
    "[-]",
    "[=]",
    "Bksp",
    "Tab",
    "Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P", "[", "]",
    "Enter",
    "Ctrl",
    "A", "S", "D", "F", "G", "H", "J", "K", "L",
    "[;]",
    "[\"]",
    "[`]",
    "LShift",
    "\\",
    "Z", "X", "C", "V", "B", "N", "M",
    "[,]",
    "[.]",
    "[/]",
    "RShift",
    "PrtSc",
    "Alt",
    "",
    "",
    "F1", "F2", "F3", "F4", "F5", "F6", "F7", "F8", "F9", "F10",
    "",
    "",
    "Home",
    "Up",
    "PgUp",
    "[-]",
    "[<-]",
    "[5]",
    "[->]",
    "[+]",
    "End",
    "Dn",
    "PgDn",
    "Ins",
    "Del"
};

static void (interrupt *oldkb)(void);
static void interrupt newkb(void);
static int kbval = 0;

/* ----- keyboard ISR ------ */
static void interrupt newkb(void)
{
    kbval = inportb(0x60);
    (*oldkb)();
}

/* ------------ initialize for hot key test ----------- */
void initkey(void)
{
    /* -------- attach to keyboard interrupt -------- */
    oldkb = getvect(KYBRD);
    setvect(KYBRD, newkb);
}

void endkey(void)
{
    setvect(KYBRD, oldkb);
}

/* --------- test for a specified keystroke ----------- */
int iskeyhit(char keyscan, char keymsk)
{
    char far *bp = MK_FP(0, 0x417);
    int rtn;

    /* -------- if no key is depressed, bail out ---------- */
    if ((kbval & 0x80) != 0)
        rtn = 0;
    else if (keyscan && kbval != keyscan)
        rtn = 0;
    else
        rtn = (keymsk == ((*bp) & 0xf));

    return rtn;
}

/* ---------- program the user hot key ----------- */
void hotkey(unsigned *keyscan, unsigned *keymsk)
{
    int key1, key2 = 0, key3, k = 0;
    char svbios;
    char far *bp = MK_FP(0, 0x417);

    /* ---------- attach to keyboard interrupt ------------- */
    initkey();

    /* ------- first, we need to be off the keyboard ------- */
    kbval = 0x80;
    while ((kbval & 0x80) == 0)
        ;

    /* --------- clear the BIOS readahead buffer ---------- */
    clearkb();
    svbios = *bp;
    while (1)    {
        key1 = 0x80;
        key3 = 0;
        /* ------- wait for a key press --------- */
        while (key1 & 0x80)
            key1 = kbval;
        /* ------ if Spacebar, Enter or Esc, drop out ------ */
        if (key1 == 57 || key1 == 1 || key1 == 28)
            break;
        /* ------ must be Alt, Ctrl, or Shift -------- */
        if (key1 != 56 && key1 != 29 &&
               key1 != 42 && key1 != 54)    {
            buzz();
            continue;
        }
        /* ------- wait for a second key press -------- */
        while ((key2 = kbval) == key1)
            if (key2 & 0x80)
                break;
        key2 &= 0x7f;
        k = 0;
        if (key1 == key2)
            /* ----- released the first key ----*/
            continue;
        /* ---- wait for key release or key change ----- */
        while (((key3 = kbval) & 0x80) == 0)
            if (key3 != key2 && key3 != key1)
                break;
        key3 &= 0x7f;
        if (key3 == key2 || key3 == key1)
            key3 = 0;
        /* -------- clear the bios readahead buffer -------- */
        clearkb();
        *bp = svbios;
        /* ------ look at the keystrokes -------- */
        switch (key1)    {
            case 56:    k |= 8; break;
            case 29:    k |= 4; break;
            case 42:    k |= 2; break;
            case 54:    k |= 1; break;
            default:    break;
        }
        switch (key2)    {
            case 56:    k |= 8; key2 = 0; break;
            case 29:    k |= 4; key2 = 0; break;
            case 42:    k |= 2; key2 = 0; break;
            case 54:    k |= 1; key2 = 0; break;
            default:    break;
        }
        switch (key3)    {
            case 56:    k |= 8; key3 = 0; break;
            case 29:    k |= 4; key3 = 0; break;
            case 42:    k |= 2; key3 = 0; break;
            case 54:    k |= 1; key3 = 0; break;
            default:    break;
        }
        if (key2 != 1 && key2 != 28)    {
            if (key2 == 70 || key3 == 70)    {
                /* ---- can't use the Break key ---- */
                buzz();
            }
            else    {
                if (key2 == 0)
                    key2 = key3;
                /* ---- shift Function keys allowed ---- */
                if ((k<4 && key2) && (key2 < 59 || key2 > 68))
                    buzz();
                else if (!(k && (key2 || multibits(k))))
                    buzz();
                else
                    printf("\r%s                              ",
                        showkey(key2, k));
            }
        }
        /* -------- wait for the key release -------- */
        while ((kbval & 0x80) == 0)
            ;
    }
    if (key1 == 57 && k == 0)
        *keymsk = *keyscan = 0;
    if (key1 == 28 && k != 0)    {
         *keymsk = k;
        *keyscan = key2;
    }
    clearkb();
    *bp = svbios;

    endkey();
    kbval = 0;
}

/* ----- test a nybl for multiple bits ---- */
static int multibits(int k)
{
    int ct = 0, i;

    for (i = 1; i < 16; i *= 2)
        if (k & i)
            ct++;
    return (ct > 1);
}

/* ----- show the hot key ------ */
char *showkey(unsigned keyscan, unsigned keymsk)
{
    *hotky = '\0';
    if (keymsk & 8)
        strcat(hotky, "Alt-");
    if (keymsk & 4)
        strcat(hotky, "Ctrl-");
    if (keymsk & 2)
        strcat(hotky, "LShift-");
    if (keymsk & 1)
        strcat(hotky, "RShift-");
    if (keyscan)
        strcat(hotky, scodes[keyscan-1]);
    else
        *(hotky + strlen(hotky) - 1) = '\0';
    return hotky;
}

/* ----- clear the BIOS keyboard read-ahead buffer ----- */
static void clearkb(void)
{
    int far *nextoff = MK_FP(0x40,0x1a);
    int far *nexton  = MK_FP(0x40,0x1c);
    *nextoff = *nexton;
}

static void buzz(void)
{
    sound(100);
    delay(250);
    nosound();
}
```
