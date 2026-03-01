
# C Programiranje 8808

## Da krenemo - Al Stevens

### Krokiji

Primer 1: PC verzija crypto.c

```c
#include <stdio.h>
#include <ctype.h>
#include <string.h>
char crypto [80];       /* encrypted message */
char decoded [80];      /* decrypted message */
char alphabet [];       /*"abcdefghijklmnopqrstuvwxyz"*/
char subs [];           /*"                          "*/

/* -----ANSI.SYS screen driver-----------*/
#define cursor (x,y) printf("\033[%02dH",y,x)
#define clear_screen() puts("\033[2]")

main ()
{
    int i, cp, a, b;
    char sb [3];
    clear_screen ();
    cursor (1, 6);
    puts("Enter the encrpted quote:\n");
    fget(crypto, 80, stdin);
    for (i = 0; i < strlen(crypto); i++)
        decoded[i] = ' ';       /* clear the decrypted msg */
    decoded[i] = '\0';
    while (1)  {
        cursor(1,9);
        puts(decoded);
        puts(alphabet);         /* display the alphabet */
        puts("\nEnter 2 letter substitution: (xy):");
        if(fgets(sb, "99", 2) == 0)
            break;
        a = *sb, b = *(sb+1);
        if (isalpha(a) && isalpha(b)) {
            for (cp = 0; cp < 26; cp++)
                if (subs[cp] == a)
                subs[cp] = ' ';
            subs[b - 'a'] = a;
            for (cp = 0; cp < strlen(crypto); cp++) {
                if (decoded[cp] == b)
                    decoded[cp] = ' ';
                if (tolower(crypto[cp]) == a)
                    decoded[cp] = b:
            }
        }
    }
}
```

Primer 2: Nekoliko načina za postavljanje zagrada i uvlačenje linija.

```c
/* --- then ... endif style (K&R, too) --- */
if (a == b)    {
    foo();
    bar();
}
/* --- begin ... end style --- */
if (a == b)
{
    foo();
    bar();
}
/* --- do ... doend style --- */
if (a == b)
    {
    foo();
    bar();
    }
/* --- poised roadrunner style (ugh!) --- */
if (a == b)
{   foo();
    bar();    }
```

### C kroki broj 1

Uznemiren sam programima predprocesora jezika C ili datotekama makroa koji vas podstiču da kodirate u nekoj nestandardnoj sintaksi, koja se zatim prevodi u C. Praksa sugeriše da je jeziku C potrebno poboljšanje (izvan ANSI i C + +, naravno). Na primer, neki predprocesori ili makroi žele da koristite početak i kraj umesto otvorenih i zatvorenih zagrada, navodno da bi vaš kod izgledao više kao Pascal ili na drugi način čitljiviji. po mom mišljenju, početnicima C programerima bolje je bez ovih takozvanih C pojačivača. Bolje je naučiti pravu stvar i zavoleti je mnogo ranije.

### C kroki broj 2

Ne volim da čitam C programe koji su nedosledni u svom pozicioniraju zagrada i kodu za uvlačeje. Primer 2, ova stranica, predstavja nekoliko načina na koje možete postaviti zagrade i uvući linije.

Koji god od ovih ili drugih stilova da preferirate, budite dosledni ili rizikujte da izazovete probleme sledećem čitaču vašeg koda. Problem nedoslednosti obično se pojavjuje kada neko modifikuje kod drugog i dva programera preferiraju različite stilove. Program sa takvim mešanim konvencijama je haos.

Dva krokija su dovojna da vam daju ideju i trebalo bi da budu dovojna za mesečnu kolumnu. Moji krokiji su zasnovani na mojim mišjejima i ne odražavaju ničiju zvaničnu sankciju. Nisu svi krokiji o upotrebi C. Neki bi mogli biti o kompajlerima ili bibliotekama ili piscima ili bilo čemu što se mene tiče. Ako želite da dodate ili oduzmete spisak – možda se zamarate piscima koji uvek koriste foo i bar u svojim primerima – molimo vas da svoje priloge pošajete na DDS ili na BIX (alstevens) na CompuServe (71101,1262). Ukjučiću zanimjive, smešne ili na neki drugi način relevantne krokije u ovu kolonu zajedno sa imenima saradnika za C krokije.

## Zbunjujuće ali neophodne C konstrukcije

Pretpostavimo da imate niz struktura i da svaka struktura ima niz pokazivača funkcija. Sada, s obzirom na pokazivač na niz struktura, ceo broj koji ide u indeks na željeni element strukture u nizu, i ceo broj koji je u indeksu na funkciju koju želite da pozovete, kako napisati izraz koji poziva funkciju?

Dok razmišljate o tome, evo neke pozadine. Ova vežba nije još jedna C zagonetka, već pravi problem na koji se susreće pisac menija opšte namene. Upravljački program koristi nizove struktura i stringova da opiše hijerarhiju menija. Ovaj koncept alatke menija za višekratnu upotrebu je centralni za većinu C sistema koje sam razvijao, a njegov format se povremeno menjao da bi podržao različite stilove prezentacije menija. Nećete biti iznenađeni da ista tehnika značajno figurira u novom projektu "C programiranje" koji će uskoro biti otkriven.

Upravo pomenuta struktura opisuje meni. Niz tih struktura predstavlja sve menije u programu. Niz pokazivača funkcija predstavlja funkcije koje treba pozvati kada korisnik odabere odgovarajuće komande menija. Primer 3, ova stranica, prikazuje relevantne strukture podataka i stavke. U problemu, pokazivač "mnn" pokazuje na niz menija, "curr_menu" ceo broj predstavlja trenutni meni, a "selection" predstavlja trenutni ceo broj za izbor. Zamislite da želite da pozovete odgovarajuću funkciju i prosledite joj broj menija i broj za izbor koji su doveli do njenog poziva.

Primer 3: Struktura koja ukazuje na niz menija i funkcija za njihovu kontrolu.

```c
struct menus {
    char *menu_selections[MAX_SELECTIONS];
    int (*func[MAX_SELECTIONS])(int, int);
} mn{MAX_MENUS];

struct menus *mnn = mn;
int curr_menu;
int selection;
```

Iskusni C programer može intuitivno smisliti tačan izraz bez razmišljanja o tome. Novi C programer će, međutim, morati da radi na tome neko vreme. Možete tvrditi da novi C programer nikada ne bi imao ovu dilemu jer konstrukcija ne bi bila očigledna – programer bi dizajnirao jednostavniju strukturu podataka – možda različite nizove za svaki meni. Ipak, ovi problemi su stvarni i bolje ćete razumeti jezik C nakon što ih rešite.

Da li se odgovor čini očiglednim? Ako jeste, čestitam. Ako ne, razjasniće se kada to shvatite. Ranije sam, kao mladi C programer, dizajnirao sebe u ovom uglu ne shvatajući koliko će to biti zbunjujuće. Tačna sintaksa pozivajuće izjave je ono što mi je izmicalo. Dakle, uzimajući komponente iskaza, isprobao sam svaku varijaciju zagrada, indeksa i operatora pokazivača dok izjava nije proradila. Rešenje koje je radilo sa jednim kompajlerom nije uspelo sa nekoliko drugih. Onaj koji je kasnije prikazan bio je onaj koji su svi prevodioci prihvatili.

Da biste ga izvukli, možete razložiti različite delove problema, što je bolji pristup od moje metode kamen spoticanja. Počinjemo tako što znamo da poziv funkcije preko pokazivača izgleda ovako:

```c
(*func)(curr_menu, selection);
```

Func pokazivač sadrži adresu neke funkcije. U ANSI standardu, možete izostaviti zvezdicu i zagrade za vezivanje tako da poziv funkcije izgleda kao običan poziv funkcije bez pokazivača. Nisam baš spreman za ovu novu konvenciju. Starija konvencija – koja je još uvek podržana, hvala Bogu – govori vam dok čitate izjavu da se poziv vrši preko pokazivača. Ova konvencija vam daje naznaku gde da tražite funkciju koja se poziva. Takvi tragovi su vredni kada pretražujete neki stari, možda nedovoljno komentarisani kod. Više volim kada jezik pomaže da se objasni sam.

Evo načina na koji se adresa funkcije dodeljuje pokazivaču funkcije:

```c
func = funcname;
```

Jednostavno, zar ne? To ste već znali. Pretpostavimo sada da je "func" pokazivač na niz funkcija. Ako "selection" korisničkog menija odgovara stavki u nizu, dodeljivanje izgleda ovako:

```c
func[selection] = funcname;
```

I ovaj zadatak je očigledan. Ali da biste pozvali ovaj niz pokazivača, potreban vam je pretplatni ceo broj da biste odredili koji od pokazivača u nizu sadrži adresu funkcije koju treba pozvati. Pretplaćeni poziv izgleda ovako:

```c
(*func[selection]) (curr_menu,selection);
```

Ovaj poziv nije tako očigledan. Trebalo mi je malo eksperimentisanja da dođem do toga. Sada na idemo strukturu: ako je pokazivač funkcije u strukturi bio u nizu, a "mn" je ukazivao na konkretnu strukturu, dodeljivanje bi bilo ovako:

```c
mn->func = funcname;
```

Tada bi poziv izgledao ovako:

```c
(*mn->func)(curr_menu, selection);
```

Napredak do ove tačke takođe nije tako očigledan, ali je razuman. Trebalo mi je vremena da shvatim blizinu operatora pokazivača - zvezdice i zagrada. Sada dodajte indeks selekcije u izjavu i to izgleda ovako:

```c
(*mn->func[selection]) (curr_menu, selection);
```

Ali u problemu, pokazivač "mn" pokazuje na niz struktura – prvi element strukture u nizu – umesto na željeni element strukture, a ceo broj curr_menu pokazuje koji element u nizu predstavlja trenutni meni. Dakle, konačni odgovor je ovaj:

```c
(*(mm + curr_menu)->func[selection])(curr_menu, selection);
```

The new ANSI rule would simplify it only a little as shown here.

```c
(mn + curr_menu)->func[selection](curr_menu, selection); 

// ili 
mn[cur_menu]->func[selection](curr_menu, selection);
```

Zbog bliskog odnosa između pokazivača i nizova, postoje varijacije ovog izraza koje će takođe funkcionisati. Svi su podjednako zagonetni.

Ovakvi problemi će obeshrabriti sve osim najjačih srca. Kada se C programeri okupe oko hladnjaka i razgovaraju o takvim stvarima, oni imaju tendenciju da takva rešenja odbace kao trivijalna i očigledna. Izluđujuća je karakteristika C-a da su odgovori na zagonetke kodiranja neuhvatljivi kada ih ne znate i očigledni kada ih znate. Ova karakteristika vas tera da se zapitate da li ste jedini koji ne razumete. Ohrabri se. Osim ako niste kompjuterski naučnik i stručnjak za jezik sa prirodnom sklonošću refleksivnom raščlanjivanju s leva na desno i evaluaciji prioriteta, moraćete s vremena na vreme da rešite problem poput ovog. Koristeći postepeni pristup otkrivanju složenog C idioma, kao što je ovaj ilustrovan ovde, možete izbeći metod pokušaja i greške koji sam nevino koristio. Evo saveta. Prijavite se na BIX ili CompuServe i pitajte nekoga. Tamo ima nekih pametnih ljudi i skoro sigurno će neko od njih rešiti sličan problem. Ako nije, neko je obično voljan da ode van mreže, smisli rešenje i ponovo se prijavi da objavi odgovor. Nikada me nisu razočarali ovi velikodušni ljudi.
