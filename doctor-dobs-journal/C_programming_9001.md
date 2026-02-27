# C programiranje 18

## TEXTSRCH nastavak: Interpreter izraza

Al Stevens, januar '90

Prošlog meseca smo napravili parser izraza koji obrađuje izraze koji se sastoje od ključnih reči, Bulovih operatora i zagrada. Koristićemo izraze u upitima za pretraživanje teksta za novi projekat kolone „C programiranje“, sistem za indeksiranje i pronalaženje baze tekstualnih podataka. Ovog meseca projektu dajemo ime TEKSTSRCH i nastavljamo sa tumačenjem izraza.

Tekstualna baza podataka koju će TEKSTSRCH podržati je ona koja se sastoji od velikog broja tekstualnih datoteka koje se ne menjaju mnogo. Tipične aplikacije uključuju datoteke poruka elektronske pošte i inženjersku dokumentaciju. Ima i mnogo drugih, naravno, ali njihova karakteristika je da su relativno statične.

Osnovna svrha baze podataka je da čuva naše tekstualne datoteke na centralno organizovanom mestu. Primarna svrha TEKSTSRCH-a je da nam omogući da pronađemo datoteke koje želimo da pročitamo, na osnovu nekih kriterijuma. S obzirom na to da znamo nešto o tome šta tražimo, želimo da možemo da izrazimo te kriterijume u obliku izraza upita i da pronađemo datoteke u bazi podataka koje odgovaraju izrazu. Tipičan izraz može biti:

```sh
(cobol and fortran) and not pascal
```

Pretraga baze podataka bi nam rekla koje datoteke odgovaraju upitu, odnosno koje datoteke sadrže reči „cobol“ i „fortran“, ali ne sadrže reč „pascal“.

Parser izraza od prošlog meseca je izvršio leksičko skeniranje izraza, pretvarajući reči i operatore izraza u tokene i stavljajući ih u stek u postfiks notaciji. Ovog meseca ćemo napraviti TEKSTSRCH tumač izraza. Njegova funkcija je da izdvoji ključne reči iz steka, pokrene pretragu baze podataka za podudaranje za svaku od njih i kombinuje rezultate pretrage sa Bulovim operatorima da bi se utvrdilo koje datoteke odgovaraju kriterijumima pretrage. Nakon što napravimo takvu listu datoteka, preći ćemo na skeniranje teksta da bismo završili pretragu i pronašli mesta u datotekama na kojima se pojavljuju reči i fraze. Ti procesi će doći u kasnijoj etapi. Indeks koji ćemo napraviti omogućava nam da se spustimo na nivo datoteke sa minimalnim vremenom pretraživanja.

Prvo razmotrimo kako ćemo indeksirati našu tekstualnu bazu podataka. Nećemo praviti niti koristiti indeks u ovomesečnom izdanju, ali moramo razumeti njegov rad da bismo napravili interpreter izraza.

Naša baza podataka će se sastojati od više tekstualnih datoteka i indeksa reči. Indeks će sadržati unos za svaku ključnu reč ili frazu koja se pojavljuje u bazi podataka. Unos indeksa za određenu reč će nam reći koje datoteke sadrže tu reč tako što će pokazati na listu datoteka. Lista se snima kao bit mapa gde svaki bit predstavlja datoteku. U svakom trenutku, broj datoteka u bazi podataka je konstantan, tako da je dužina mape bitova za bilo koji unos fiksirana na taj broj bitova.

Kada pretražimo indeks za reč, dobićemo nazad bit mapu fiksne dužine sa uključenim bitovima koji odgovaraju datotekama koje sadrže reč. Mehanika indeksa i pretrage nam sada nisu bitni; neophodno je da znamo samo da pretraga vraća takvu bit mapu.

Sa takvom mapom možemo reći da li se reč pojavljuje ili ne pojavljuje u datoteci. Pretpostavimo da naša baza podataka ima 16 tekstualnih datoteka (ne mnogo, ali dovoljno da ilustruje princip). Ako koristimo indeks da nam kaže koje datoteke sadrže reč „fortran“, on bi mogao da nam vrati bit mapu kao što je ova:

```sh
0010001011000010
```

Čitajući bitove s desna na levo, možemo videti da drugi, sedmi, osmi, deseti i četrnaesti fajl na listi sadrže reč. U našem menadžeru baze podataka, imaćemo tabelu imena datoteka koja odgovaraju pozicijama bitova, tako da primenom bitova jedan možemo da izvedemo imena datoteka u kojima se reč pojavljuje. Shvatite da mi zapravo nismo pročitali datoteku da bismo dobili ove informacije - ne tokom pretrage, tj. Nekada u prošlosti čitali smo sve datoteke da bismo napravili indeks. Zbog toga takve arhitekture baza podataka najbolje rade sa relativno statičnim podacima. Vremenski najintenzivniji proces je izrada indeksa. Nakon toga, pretrage mogu biti brze.

Možete videti kako bi Bulov upit funkcionisao. Pretražićemo indeks za svaku reč u upitu. Zatim ćemo primeniti Bulove operatore na bitske mape koje pretraga vraća. Poslednja bit mapa je ona koja navodi datoteke koje odgovaraju kompletnim kriterijumima pretrage.

Setićete se od prošlog meseca da upit počinje da živi u infiksnoj notaciji koju parser prevodi u postfiksnu notaciju. Sama pretraga mora biti vođena tumačem izraza koji čita postfiks stek.

Hajde da sada vidimo kako da protumačimo postfiksni izraz. Razmotrite jednostavan izraz kao što je ovaj

```sh
apples and oranges
```

Postfiks stek za ovaj izraz bi izgledao ovako:

```sh  
and   
apples   
oranges
```

Tumač izbacuje unose iz steka i obrađuje svaki prema tome šta je. Ako je unos token binarnog operatora kao što je "i" prikazano ovde, tumač izbacuje sledeća dva tokena, obrađuje ih i kombinuje dva operanda primenom operatora. Ako je unos operand reči ili fraze, tumač poziva proces pretraživanja da bi pretvorio reč ili frazu u bit mapu.

Evo kako bi se protumačio naš izraz „jabuke i pomorandže“. Prvo prevodilac izbacuje znak "i". Zatim izbacuje token narandže i pretvara ga u bit mapu. Zatim izbacuje token jabuke i pretvara ga u bit mapu. Pošto "i" token čeka, interpretator logički I povezuje dve bitne mape i vraća rezultat.

Da biste videli kako tumač radi sa složenijim izrazom, razmotrite ovo:

```sh
fortran and not (cobol or pascal)
```

Postfiks stek za ovaj izraz izgleda ovako:

```sh  
and   
not
or
pascal   
cobol   
fortran
```

Tumač izbacuje znak "i" baš kao što je to uradio sa jednostavnijim primerom. To znači da očekuje još dva operanda na I zajedno da bi dobili rezultat. Sledeći privremeni rezultat koji mora da izračuna je označen tokenom "ne" koji iskoči. Ovaj token kaže da rezultat sledećeg operanda mora biti komplementar rezultata pretrage. Sledeći token je „ili“, koji kaže da dva operanda moraju biti iskačući i logički zajedno OR. Sledeći su operandi paskal i kobol. Interpretator poziva funkciju pretraživanja za svaku od ovih reči i vraća dve bitne mape, koje zajedno ILI. Zatim tumač uzima jedinicu dopunu tog rezultata, a rezultat svega toga je prvi operand koji zahteva "i" operator. Sledeći iskočili token je fortran. Interpretator I svoju bitsku mapu povezuje sa prvom bitskom mapom i to isporučuje rezultat pretrage. Bez daha? Ono što sam upravo opisao izgleda komplikovano, ali je relativno jednostavan algoritam koji koristi rekurziju u funkciji koja vraća rezultat pretrage. Kada iskače operator, poziva se da iskače sledeći unos u stek ili dva i vraća rezultat koji dolazi od primene operatora.

[Listing 1](#listing-1) je nova verzija „textsrch.h“ koja zamenjuje onu od prošlog meseca. Značajan dodatak ovoj datoteci je globalna promenljiva MAKSFILES. Ova promenljiva određuje maksimalan broj tekstualnih datoteka u vašoj bazi podataka. Struktura bitmapa definiše višecelobrojnu bitsku mapu koja predstavlja rezultat pretrage. Ovde koristimo strukturu tako da možemo lako da prenosimo mapu između funkcija.

[Listing 2](#listing-2) je "exinterp.c", tumač izraza. Očekuje se da je postfiks izraz napravio parser od prošlog meseca. Vraća bit mapu koja predstavlja rezultat pretrage. Kasnije ćemo više razgovarati o potrazi. Ekinterp funkcija poziva rekurzivnu pop funkciju da bi dobila mapu bitova. Pop funkcija je mesto gde se pravi posao. Izbacuje token iz postfiks steka i obrađuje ga. Ako je taj token operand (reč), pop funkcija poziva funkciju pretrage, koja vraća bit mapu. Bit mapa ima set bitova za svaku datoteku koja uključuje reč. Ako je token operator, pop funkcija poziva samu sebe rekurzivno da bi dobila sledeći token ili dva na kojima će operater raditi. Tri funkcije imenovane i, ili, i ne modifikuju bit mapu kako bi odražavale rezultat Bulovih operacija koje predstavljaju. Ovde koristimo funkcije jer je bit mapa niz. (Zamislite koliko bi ovo bilo lakše sa C++.)

[Listing 3](#listing-3) je "search.c". Ovaj je za bacanje. Iako funkcioniše, manje je nego efikasan. Njegova svrha je da nam omogući da testiramo i demonstriramo algoritme za evaluaciju izraza. Uključuje tri generičke funkcije koje ćemo kasnije zameniti. Prvi, nazvan init_database, inicijalizuje sistem baze podataka. Za sada, sve što radi je da pravi listu imena datoteka koje imaju ekstenziju .TKST. Ova stub funkcija koristi MS-DOS strukture datoteka i Turbo C funkcije findfirst i findnekt. Ako koristite drugo okruženje, zamene bi trebalo da budu jednostavne. Ono što želite je spisak svih imena datoteka u bazi podataka ugrađenih u niz pod nazivom imena.

Druga generička stub funkcija se zove pretraga. Njegova svrha je da pretraži bazu podataka da bi se utvrdilo koje datoteke imaju navedenu reč u sebi. Ovaj vara. Poziva program grep i preusmerava njegov izlaz u datoteku pod nazivom „hits“. Grep komanda koju izvršava izgleda ovako:

```sh
grep -l -i<word>*.txt>hits
```

Ovaj format radi sa programom Turbo C grep i trebalo bi da radi sa većinom drugih. Parametar -1 govori grep-u da navede samo imena datoteka koja odgovaraju pretrazi, a parametar -i mu govori da učini pretragu neosetljivim na velika i mala slova. Kada se program grep završi, funkcija pretrage čita hits datoteku i upoređuje njene unose sa listom naziva datoteke koju je init_database napravila. Za one fajlove koji se podudaraju, funkcija pretrage postavlja deo u bit mapi koju gradi i vraća.

Treća generička stub funkcija u search.c se zove process_result. On prihvata bit mapu kao rezultat pretrage izraza. Zapamtite da pretraga pravi bit mapu za jednu reč, a ekinterp kombinuje sve bitske mape sa operatorima da bi napravio konačnu bit mapu. Taj poslednji je onaj koji proces_result dobija. U ovoj verziji sa završetkom, process_result jednostavno navodi datoteke za koje je pronađeno da odgovaraju kriterijumima pretrage.

[Listing 4](#listing-4) je "textsrch.c", glavna funkcija programa TEKSTSRCH i ona koja povezuje proces pretraživanja. Poziva init_database i zatim čita izraz za pretragu sa konzole. Poziva lekical_scan od prošlog meseca da pretvori izraz u postfiksnu notaciju. Ako je izraz u grešci, program prikazuje umetak ispod oznake gde je pronašao problem. U suprotnom, poziva ekinterp za tumačenje izraza i process_result za obradu rezultata.

Morate da kompajlirate ekpress.c od prošlog meseca i tektsrch.c, search.c i ekinterp.c od ovog meseca da biste napravili program. Uverite se da koristite novi tektsrch.h od ovog meseca.

Da biste pokrenuli program, prvo napravite svoju bazu podataka. Kopirajte sve svoje tekstualne datoteke u jedan poddirektorijum i dajte im ekstenziju datoteke .TKST. Ako datoteke imaju ugrađene stvari za obradu teksta, pokrenite ih kroz program za obradu teksta da biste napravili prave ASCII tekstualne datoteke. U suprotnom, grep možda neće moći da pronađe reči koje traži.

Ova uputstva pretpostavljaju MS-DOS okruženje. Uverite se da su programi grep i tektsrch na putanji i da ste prijavljeni u poddirektorijum gde ste napravili bazu podataka. Pokrenite program tektsrch iz komandne linije. To će od vas zatražiti izraz. Unesite jedan i gledajte kako se vrti. Ako je vaša baza podataka velika, biće potrebno neko vreme. Kasnije, nakon što napravimo indeks, pretraga će biti mnogo brža. Rezultat programa će biti prikazana lista datoteka koje odgovaraju upitu. Rezultate možete proveriti pomoću grep-a.

Sledećeg meseca ćemo raditi na ugradnji indeksa u bazu podataka. Stvari postaju interesantne jer imamo nekoliko različitih načina da pristupimo problemu.

## OOPSLA

Prisustvovao sam ACM-ovoj četvrtoj godišnjoj Konvenciji o objektno orijentisanim programskim sistemima, jezicima i aplikacijama (OOPSLA), održanoj u Nju Orleansu u oktobru 1989. Ovo je bilo moje prvo putovanje u Crescent Citi, retka ispovest starog džez muzičara, i pored halabuke OOPSLA-a i dobrih prijatelja, čuo sam i na podijumu dobre džez konferencije orkestri širom grada.

C++ je prožimao emisiju. Ovo je očigledno platforma vrlo bliske budućnosti. Bilo je demonstracija okruženja jezika C++ od ParcPlace-a i Apple-a, kao i brojnih proizvoda koji poboljšavaju C++ platformu. Odsutni su bili Zortech, Intek i Guidelines, glavni dobavljači kompletnih C++ sistema za PC. Uskoro možemo očekivati najave, nadam se, od nekih drugih prodavaca.

Jedna tema se često pojavljivala u razgovorima sa dobavljačima jezika C++. Ne postoji standardna biblioteka klasa, a čini se da niko ne zna odakle će doći prva. Čini se da je stav da će korisnici jezika svaki izgraditi svoj. Ovo me podseća na rane dane C-a kada je nekoliko funkcija opisanih u K&R-u predstavljalo jedini standard koji smo imali, a proizvođači kompajlera su ga proširili svojim sopstvenim jedinstvenim verzijama stvari izostavljenih. Postojala je Unik biblioteka, ali su mnogi kompajleri za PC krenuli svojim putem. Pritisak tržišta je nametnuo usklađenost sa de facto standardom mnogo pre nego što je ANSI Ks3J11 komitet bio blizu gotovog nacrta. Taj pritisak je stvoren ogromnim uspehom kompajlera Microsoft C. Svaki drugi proizvođač kompajlera pokušao je tada da bude kompatibilan sa dobrim starim MSC-om, i razvio se svojevrstan PC standard. Neki od tog standarda - delovi koji nisu bili specifični za PC - našli su se u ANSI nacrtu.

Trenutno postoji nekoliko knjiga o C++. Većina njih opisuje neke specifične klase koje koriste da objasne kako gradite klase, a ne da unaprede standard za biblioteku klasa. I sam sam uradio istu stvar nekoliko kolona unazad sa domaćim klasama za prozore, menije, stringove i povezane liste. Nijedna od ovih različitih biblioteka verovatno neće pokretati industrijski prihvaćen standard. Pretpostavljam da će prvi proizvođač kompajlera koji bi ukomponovao biblioteku klasa sa veoma uspešnim C++ kompajlerom držati vodeću ulogu.

Bjarne Stroustrup ističe da ne smemo razmišljati o univerzalnoj biblioteci klasa na način na koji bi to zahtevao čisti objektno orijentisani model podataka. Klase prkose univerzalnoj definiciji jer je univerzum prevelik. Zapamtite da je svrha standardne biblioteke C da definiše metode za manipulaciju generičkim objektima: datoteke, stringovi, znakovi, brojevi, memorijski blokovi, izvršavanje programa, datumi, vreme i tako dalje. Standardna biblioteka klasa takođe mora da se ograniči na klase koje imaju opštu primenu, a hijerarhija za dati program nije nužno podskup univerzalno definisane hijerarhije, već ona koja je napravljena da odgovara problemu koji je pri ruci. Na kraju, kreatori vertikalnih aplikacija mogu doći da podele svoj rad u nizu biblioteka klasa specifičnih za aplikaciju. Ali sumnjam. Postoji nekoliko takvih deljenja biblioteka C funkcija širom industrije.

## Učenje C

Još jedno često izražavano mišljenje u OOPSLA-i bilo je da je C++ previše težak za učenje, a jezička industrija strahuje da će se programeri time uplašiti. Oni upoređuju C++ sa jednim od novih Paskala sa objektno orijentisanim ekstenzijama i zaključuju da je Pascal ruta lakša za putovanje. Donekle su u pravu. C++ je teže naučiti od objektno orijentisanog Paskala. Ali opet, tradicionalni C je teže naučiti od tradicionalnog Paskala, a ipak je C postao i ostao dominantan jezik. Zašto? Jer, kada se jednom nauči, C postaje preferirani jezik programera, bez obzira na verske jezičke ratove. Mislim da je C teže naučiti samo zato što je teže predavati. Prvo treba da obrazujemo nastavnike. Oni moraju da znaju kako da predstave C (i C++) učeniku u logičkim koracima.

Od nastavnika C trebalo bi se tražiti da prvo podučavaju Pascal. Možda će im njegova sintaksa pomoći da razumeju svojstva programskog jezika čija je prvobitna namena bila podučavanje programiranja. Instruktori C-a širom zemlje zbunjuju učenike sa nejasnim primerima koda koje će dozvoliti samo kratki jezik kao što je C. Nije da morate tako da pišete C programe, samo možete, a nastavnici nisu naučili da taj deo ostave tek kasnije.

Jedna od prvih lekcija u K&R-u govori o tome kako možete reći ovo:

```sh
while ((c = getchar( )) != EOF)     
  ...
```

Mnogi nastavnici zaključuju da je ova mogućnost C-a za podršku ugrađenim izrazima toliko moćna da je treba naučiti prvo iz palube. Nema ubedljivog razloga da izbegavate takve izraze kada ih razumete, ali takođe nema razloga zašto novajlija u C ne bi bio pošteđen ovih detalja dok on ili ona nisu spremni za to.

Knjiga K&R prethodi ovom primeru sa manje sažetim koji razlaže svaki od izraza i operacija u pojedinačne izjave poput ove:

```sh
c = getchar( );
while (c!= EOF) {
    ...
    c = getchar( );
}
```

Svrha ovog ranijeg primera je da upozna iskusnog programera sa kratkim stilom koda i da ilustruje kako je svaka izjava izraz i da možete koristiti tu funkciju C-a da napišete manje redova koda.

Kada vidi takav program, C veteran je odmah pokrenut da implodira sve dok ne izgleda kao prvi primer. Ali početnik smatra da je opširniji program mnogo lakši za razumevanje. Učenik vidi radni program pre nego što nauči mnogo finijih tačaka C. U kojoj meri bi nastavnik trebalo da odloži neke od finijih detalja zavisiće od programskog iskustva novog učenika C, posebno zato što je sve više nastavnih planova i programa namenjeno učenicima koji progresivno prolaze kroz slojeve preduslova bez evidentiranja vremena programiranja u stvarnom svetu. Pokušajte, na primer, da objasnite nekome ko nije programer zašto je rekurzija važna.

Drugi problem je što nastavnici predaju previše drugih složenih tema u predmetima koji bi trebalo da budu C. Oni nastoje da C obaviju misterijom. Nedavno sam podučavao studenta koji je bio upisan na napredni C kurs na lokalnom koledžu. Programski zadaci su od nje zahtevali da koristi I/O niskog nivoa i funkcije prekida Turbo C-a da bi manipulisala sektorima direktorijuma DOS disketa. Razred je proveo više vremena učeći o unutrašnjosti DOS-ovog sistema datoteka nego o C. Instruktorovo obrazloženje za takve zadatke je bilo da se C koristi prvenstveno za sistemsko programiranje, a DOS je sistem koji škola koristi. Učenik koji ide u okruženje koje nije DOS C prevaren je ovim pristupom. Za početak, pretpostavka instruktora više ne važi. Koristimo C za razvoj skoro svake vrste softverskog sistema. Trošeći toliko vremena i energije časa na učenje DOS-a i BIOS sektora I/O, instruktor je izgubio dragocene prilike da predaje C. Ovaj čas bi se prikladnije označio kao Kurs programiranja sistema MS-DOS sa C kao programskim jezikom. Manje učenika bi se, naravno, prijavilo, ali oni koji jesu dobili bi ono što su platili, a ostali su mogli biti negde drugde u pravom C razredu.

## ANSI ugao

Ponekad poboljšanje može stati na put. Nacrt ANSI specifikacije za standardni C dodaje susedne stringove literalne konkatenacije jeziku, i to je zgodna stvar. Pomoću ove funkcije možete kodirati susedne literale, a kompajler će ih spojiti. Na primer, razmotrite ovaj kod:

```sh
printf("Hello," "World");
```

Kada prevodilac vidi susedne stringove literale poput ovih, kombinuje ih. Snaga ovog uređaja postaje očigledna kada se koristi zajedno sa predprocesorom na ovaj način:

```c
#define PROGRAM "Jiffy SpreadSheet"
#define VERSION "v1.23" 
#define DATE "1990"

printf(PROGRAM VERSION "        Copyright " DATE);
```

This feature, handy as it is, cost me a bit of time. I had the following array in a program:

```c
char *names[] = {
    "add",
    "change",
    "delete",
    "cut"
    "paste",
    NULL
 };
```

Vidite onaj zarez koji nedostaje posle doslovnog slova „iseci“? Nisam, ne dugo vremena, i nisam mogao da shvatim zašto je ova izjava delovala samo povremeno:

```c
if (strcmp(names[i], name) = = 0)
```

Mogao sam da dodajem, menjam i brišem, ali nisam mogao da isečem ili nalepim. Nakon što sam pronašao problem, zurio sam u neverici, pitajući se zašto me kompajler nije upozorio na zarez koji nedostaje. Onda me je pogodilo. Dobri stari ANSI C je mislio da zaista nameravam da napravim "cutpaste" string. Ali, kada se posmatra u kontekstu kako sam kodirao niz, zarez koji nedostaje bilo je teško uočiti i, kada se jednom otkrije staroj očnoj jabučici, izgledalo je kao da je kompajler trebalo da ga označi kao grešku. Čak i nakon što sam video grešku, nije mi odmah bilo očigledno da sam kodirao savršeno prihvatljiv ANSI C jezik. Prevodilac, međutim, nepristrasno gleda u naš kod i ne može da pogodi naše namere. Ono što sam kodirao bilo je legitimno C, ali nije funkcionisalo vredno pažnje.

Evo još jedne upotrebe operatora pretprocesora # o kojoj smo razgovarali prošlog meseca. Pretpostavimo da imate neki maksimalan broj stvari koje će vaš program obrađivati i da taj maksimum definišete na sledeći način:

```c
#define MAXTHINGS 500
```

Kada korisnik pokuša da doda 501. stvar, želite da prikažete poruku koja navodi ograničenje. Nekada smo to radili na ovaj način.

```c
printf("%d entries only", MAX);
```

Ovo je izmišljotina konstanti. MAKSTHINGS je konstanta i treba je uključiti u literal stringa. Ali pre-ANSI C nije imao pogodan način da to uradi i da i dalje sačuva globalnu definiciju MAKSTHINGS-a. Sa novim operatorom # i konkatenacijom susednih stringova, možemo da uradimo ovo:

```c
#define str(x) #x   
printfSTR(MAX "entries only");
```

## Књига месеца

Nabavite sebi primerak "C Traps and Pitfalls" Endrua Keniga (Addison-Veslei, 1989). To je skup grešaka koje pravi C programer. Osim onih očiglednih (onog što vise, na primer), knjiga sadrži mnogo pronicljivih diskusija o manje očiglednim, ali uobičajenim greškama, uključujući kvarove na strašnom C pokazivaču izazvane programerima.

Mnoge greške koje Koenig opisuje biće označene kao upozorenja od strane savremenih kompajlera usklađenih sa ANSI. Ali mnogo pre-ANSI koda je još uvek na održavanju i moramo ili isključiti upozorenja ili ih zanemariti, pa smo ponekad podložni ovim greškama kao i u starim danima.

## LISTING 1

```c
/* ----------- textsrch.h ---------- */

#define OK    0
#define ERROR !OK

#define MXTOKS 25           /* maximum number of tokens */
#define MAXFILES 512        /* maximum number of files  */
#define MAPSIZE MAXFILES/16 /* number of ints/map       */

/* ---- the search decision bitmap (one bit per file) ---- */
struct bitmap {
    int map[MAPSIZE];
};

/* ------- the postfix expression structure -------- */
struct postfix  {
    char pfix;      /* tokens in postfix notation */
    char *pfixop;   /* operand strings            */
};

/* --------- the postfix stack ---------- */
extern struct postfix pftokens[];
extern int xp_offset;

/* --------- expression token values ---------- */
#define TERM     0
#define OPERAND 'O'
#define AND     '&'
#define OR      '|'
#define OPEN    '('
#define CLOSE   ')'
#define NOT     '!'
#define QUOTE   '"'

/* ---------- textsrch prototypes ---------- */
struct postfix *lexical_scan(char *expr);
struct bitmap exinterp(void);
void init_database(void);
struct bitmap search(char *word);
void process_result(struct bitmap);
```

## LISTING 2

```c
/* ------------ exinterp.c --------------- */

/*
 * An expression interpreter that processes the
 * tokens on a postfix stack.
 */

#include "textsrch.h"

static struct postfix *pf = pftokens;

static struct bitmap pop(void);
static struct bitmap not(struct bitmap map1);
static struct bitmap and(struct bitmap map1, struct bitmap map2);
static struct bitmap or(struct bitmap map1, struct bitmap map2);

/* ----- entry to the interpreter
         returns a bitmap which indicates the files
         that match a search expression ------------- */
struct bitmap exinterp(void)
{
    /* ------- find the top of the postfix stack ------- */
    while (pf->pfix != TERM)
        pf++;
    /* ------- get the result of the expression ------- */
    return pop();
}

/* ------ pops an operand and converts it to a bit map ------ */
static struct bitmap pop(void)
{
    struct bitmap map1;
    switch ((--pf)->pfix)   {
        case OPERAND:
            map1 = search(pf->pfixop);
            break;
        case NOT:
            map1 = not(pop());
            break;
        case AND:
            map1 = and(pop(), pop());
            break;
        case OR:
            map1 = or(pop(), pop());
            break;
        default:
            break;
    }
    return map1;
}

/* ------- unary <not> operator ----------- */
static struct bitmap not(struct bitmap map1)
{
    int i;
    for (i = 0; i < MAPSIZE; i++)
        map1.map[i] = ~map1.map[i];
    return map1;
}

/* ------- binary <and> operator -------------- */
static struct bitmap and(struct bitmap map1, struct bitmap map2)
{
    int i;
    for (i = 0; i < MAPSIZE; i++)
        map1.map[i] &= map2.map[i];
    return map1;
}

/* ------- binary <or> operator -------------- */
static struct bitmap or(struct bitmap map1, struct bitmap map2)
{
    int i;
    for (i = 0; i < MAPSIZE; i++)
        map1.map[i] |= map2.map[i];
    return map1;
}
```

## LISTING 3

```c
/* ---------- search.c ----------- */

/*
 * stub functions to simulate the TEXTSRCH retrieval process
 */

#include <stdio.h>
#include <dir.h>
#include <string.h>
#include <process.h>
#include "textsrch.h"

static char names[MAXFILES][13];
static int namect;
static void setbit(struct bitmap *map1, int bit);
static int getbit(struct bitmap *map1, int bit);

/* --------- initialize the data base environment --------- */
void init_database(void)
{
    struct ffblk ff;
    int rtn;
    rtn = findfirst("*.txt", &ff, 0);
    while (rtn != -1 && namect < MAXFILES)  {
        strcpy(names[namect++], ff.ff_name);
        rtn = findnext(&ff);
    }
}

/* ------ search the data base for a match on a word ------ */
struct bitmap search(char *word)
{
    int i;
    FILE *fp;
    char str[80];
    struct bitmap map1;

    for (i = 0; i < MAPSIZE; i++)
        map1.map[i] = 0;
    sprintf(str, "grep -l -i %s *.txt >hits", word);
    system(str);
    if ((fp = fopen("hits", "rt")) != NULL) {
        while (fgets(str, 80, fp) != NULL)  {
            *strchr(str, ':') = '\0';
            for (i = 0; i < MAXFILES; i ++) {
                if (stricmp(str+5, names[i]) == 0)  {
                    setbit(&map1, i);
                    break;
                }
            }
        }
        fclose(fp);
    }
    return map1;
}

/* ---- process the result of a query expression search ---- */
void process_result(struct bitmap map1)
{
    int i;
    for (i = 0; i < MAXFILES; i++)
        if (getbit(&map1, i))
            printf("\n%s", names[i]);
}

/* ------ sets a designated bit in the bit map ------ */
static void setbit(struct bitmap *map1, int bit)
{
    int off = bit / 16;
    int mask = 1 << (bit % 16);
    map1->map[off] |= mask;
}

/* ------ tests a designated bit in the bit map ------ */
static int getbit(struct bitmap *map1, int bit)
{
    int off = bit / 16;
    int mask = 1 << (bit % 16);
    return map1->map[off] & mask;
}
```

## LISTING 4

```c
/* ----------- textsrch.c ------------- */

/*
 * The TEXTSRCH program.
 */

#include <stdio.h>
#include <process.h>
#include <string.h>
#include "textsrch.h"

void main(void)
{
    char expr[80];
    /* -------- initialize the text data base --------- */
    init_database();
    do  {
        /* ----- read the expression from the console ------ */
        printf("\nEnter the search expression:\n");
        gets(expr);
        if (*expr)  {
            /* --- scan for errors and convert to postfix --- */
            if (lexical_scan(expr) == NULL) {
                /* ---- the expression is invalid ---- */
                while(xp_offset--)
                    putchar(' ');
                putchar('^');
                printf("\nSyntax Error");
                exit(1);
            }
            /* --- interpret the expression, search the data base,
                and process the hits ------- */
            process_result(exinterp());
        }
    } while (*expr);
}
```
