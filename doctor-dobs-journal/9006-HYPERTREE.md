
# C PROGRAMMING - 9006

## HIPERTREE: A Hipertekt Indek Technikue - Al Stevens

Programeri imaju tendenciju da razmišljaju o hipertekstu na način na koji ga mi koristimo u našim aplikacijama. U našem iskustvu se često koristi u sistemima pomoći na mreži gde je tekst pomoći organizovan u strukturirani skup prozora pomoći. Do prozora pomoći dolazite kao rezultat neke kontrole osetljive na kontekst, možda sa kursorom na ključnu reč ili program na određenom mestu u meniju ili ekranu za unos podataka. Sistem pomoći vam tada omogućava da izaberete više i niže nivoe detalja pomoći od ključnih reči strateški pozicioniranih u trenutnom prozoru. Takvi sistemi pomoći koriste lagane hipertekstne koncepte, ali oni su nam najpoznatiji.

Pravi potencijal hipertekstualne tehnologije dolazi kada razmislimo o njenoj upotrebi za strukturirana preuzimanja iz velikih statičkih tekstualnih baza podataka. Mnoge discipline se redovno bave takvim podacima. Advokati, inženjeri, programeri, doktori, pisci predloga i istraživači svih vrsta stalno rade sa velikim količinama šablonskog teksta. Hipertekst nudi način da se u ove baze podataka ugrade strukturirane i disciplinovane mogućnosti preuzimanja.

Srce većine zahtevnih hipertekstualnih aplikacija je indeks. Da biste pronašli put do povezanog tela teksta iz izabrane reči ili fraze, potreban vam je indeks koji prevodi reč u pokazivač, u tekstualnu bazu podataka. Prošlog februara smo napravili sistem indeksa za naš TEKSTSRCH projekat koji je koristio tehniku heširanja da izvede pokazivač na listu datoteka u kojima postoji izabrana ključna reč. Ta tehnika indeksa bila je korisna za svrhu TEKSTSRCH-a, što je bio izbor datoteka koje odgovaraju upitu Bulove ključne reči. Ova aplikacija je oblik hiperteksta, ali je i njena laka upotreba.

Ovo izdanje kolone „C programiranje“ razmatra drugačiju tehniku indeksiranja, labavu adaptaciju B-stabla, onu koja nudi intenzivniju vrstu podrške problemima pretraživanja teksta. Osim što omogućava nasumični izbor heširane ključne reči, B-stablo vam takođe omogućava da se krećete po indeksu u nizu njegovih ključeva. Ovo olakšava vrstu pretrage u kojoj izabrana reč daje listu reči koje su bliske, na primer.

B-stablo je struktura podataka koja liči na obrnuto drvo. Koren je na vrhu drveta, a listovi na dnu. Svaki čvor sadrži vrednosti ključa, a svaka vrednost ključa ukazuje na čvor na sledećem nižem nivou u stablu gde se nalaze vrednosti koje su veće od njega i manje od sledećeg susednog ključa. Niži čvorovi su podređeni čvorovi, viši su roditeljski čvorovi. Mešane metafore, ove porodice i drveće. Pokazivači u čvorovima na donjem nivou - listovi - nisu pokazivači čvorova, već sadrže vrednosti podataka koje odgovaraju ključevima. B-stabla tipično koriste sistemi za upravljanje bazama podataka za upravljanje indeksima za primarne i sekundarne ključeve elementa podataka. Da bi podržali takve aplikacije, tradicionalni algoritmi B-stabla moraju biti sposobni da dodaju ključeve u slučajnom nizu i da ih brišu. Objavio sam izvorni kod jezika C za takve algoritme B-stabla u dve knjige, C razvojni alati za IBM PC (Bradi Books, 1986) i C razvoj baze podataka (MIS Press, 1987).

Modifikovana B-stabla koja ćemo koristiti za indeksiranje statičke hipertekstualne baze podataka ne moraju da podržavaju brisanje ključa, a konstrukcija indeksa se može izvršiti redosledom ključeva. Ove dve karakteristike u velikoj meri pojednostavljuju kod potreban za održavanje stabla. Pošto su tehnike konstrukcije indeksa različite, stabla ove metode nisu uvek precizno izbalansirana prema modi B-stabala. Svaki čvor B-stabla ispod korena uvek sadrži najmanje polovinu broja ključeva koje može da drži. To je zato što kada je čvor popunjen do kraja, on se deli na dva čvora da bi se prilagodio sledećem ključu. Kada se podeli, vrednost ključa iz sredine ide u čvor iznad njega u stablu. Kada se ključ izbriše, čvor se kombinuje sa jednim od njegovih bratskih i sestarskih čvorova ako ta dva sada mogu da drže ključeve oba. Modifikovana struktura B-stabla za ovaj problem ne funkcioniše na taj način. Ključeve dodajemo redosledom, tako da se čvorovi popunjavaju s leva na desno. Ne dolazi do razdvajanja i svi osim krajnjeg desnog čvora na svakom nivou će biti puni. Pošto indeks podržava statičku bazu podataka, brisanje ključa se nikada ne dešava.

Naš indeks se razlikuje od B-stabla na jedan drugi način. B-stablo se sastoji od čvorova od kojih svaki može sadržati fiksni broj ključeva. Po definiciji, dužina ključa je fiksna ako je dužina čvora fiksna. U našim stablima, dužina čvora je fiksna, a dužina ključa promenljiva.

Ovu strukturu podataka nazivamo „HiperTree“. Sastoji se od zapisa zaglavlja i određenog broja čvorova. Zapis u zaglavlju sadrži broj čvora osnovnog čvora. Svaki čvor sadrži blok zaglavlja i niz ključeva promenljive dužine. Blok zaglavlja čvora sadrži zastavicu koja pokazuje da li je čvor list ili ne, pokazivač na čvor koji je roditelj trenutnom i pokazivač na čvor na sledećem nižem nivou koji sadrži ključeve koji su manji od ključeva u ovom čvoru. Svaki ključ u nizu sadrži vrednost niza ključa i pokazivač na čvor na sledećem nižem nivou u stablu koji sadrži ključeve veće od trenutnog ključa i manje od sledećeg susednog ključa u ovom nizu.

Kada je čvor list, pokazivači ključa i pokazivač zaglavlja čvora na niže ključeve služe drugoj svrsi. Oni sadrže neku vrednost koja je relevantna za ključ. U nekim aplikacijama će pokazivati na zapis podataka. U drugim će oni biti zapis podataka. Možda ukazuju na listu zapisa podataka. Funkcija vrednosti ključa je nezavisna od rada HiperTree-a. Kada dodate ključ, dodajete njegovu pridruženu vrednost ključa. Kada preuzmete ključ, preuzimate njegovu vrednost. HiperTree, dakle, služi za dva cilja preuzimanja: govori vam da li ključni argument postoji u indeksu i isporučuje vrednost ključa povezanu sa ključnim argumentima.

[LISTING_ONE](#listing_one) je "hyprtree.h", datoteka zaglavlja koja opisuje strukture HiperTree. Definiše nekoliko globalnih vrednosti koje ćete promeniti u skladu sa svojim potrebama. Prvi koji treba razmotriti je NODELEN. Ovo je dužina bajta HiperTree čvora. Ako je čvor prekratak, sadržaće mali broj ključeva, a drvo će imati mnogo nivoa. Ova okolnost bi uticala na performanse preuzimanja jer svaka pretraga mora da se kreće po svim nivoima stabla. Ako je dužina čvora predugačka, to će uticati na pojedinačne pretrage čvorova, koje su serijske.

Sledeća vrednost u "hyprtree.h" je MAXLEVELS, maksimalni broj nivoa u stablu. Ova vrednost je funkcija prosečnog broja ključeva u čvoru i ukupnog broja ključeva u indeksu. Možete ga konzervativno postaviti na bezbednu visoku vrednost kao što je 10. Deset nivoa HiperTree-a bi zaista bio veliki indeks. Sama vrednost se koristi kao dimenzija za niz pokazivača. Potrebna nam je neka vrednost da bismo koristili, jer ne znamo gornju granicu dok je ne dostignemo u vremenu izvršavanja.

MAXKEYLENGTH se koristi za ograničavanje dužine stringa ključa. Ova vrednost će zavisiti od vaše aplikacije. Sprečava da ključ potpuno zauzme čvor. Trebalo bi da ga podesite tako da svaki čvor može da sadrži najmanje tri ključa maksimalne dužine. Ovo će osigurati ispravno ponašanje HiperTree algoritama.

Moraćete da uzmete u obzir KEYVALUE typedef u "hyprtree.h". Kao što je prikazano na listi, KEIVALUE je dug ceo broj. Ta upotreba će podržati primere koji slede, koji koriste KEYVALUE kao pokazivač na datoteku. KEIVALUE može biti bilo koji važeći tip C uključujući strukturu. Zapamtite da će svaki listni čvor sadržati KEIVREDNOSTI za ključeve, tako da ih nemojte činiti predugačkim. Ako je svaki ključ u vašem HiperTree-u vektor neke složene strukture podataka, stavite tu strukturu u sopstvenu datoteku i koristite KEIVALUE kao pokazivač na tu datoteku.

### Izgradnja hiperstabla

[LISTING_TWO](#listing_two) je "addkey.c", kod potreban za pravljenje HiperTree-a. Da biste se pripremili za ovaj proces, potrebno je da izaberete svoje ključne reči i fraze iz tekstualne baze podataka i da ih sortirate u redosled razvrstavanja stabla. Algoritam pretrage koji je kasnije opisan pretpostavlja redosled upoređivanja bez obzira na velika i mala slova. Morate da navedete vrednost podataka za povezivanje sa svakom ključnom reči. Taj tip podataka je opisan u "hyprtree.h" kao tipedef KEYVALUE. Vrednost će biti vrednost koju vraćaju algoritmi pretraživanja. To može biti pokazivač na poziciju teksta u tekstualnoj datoteci, ili može biti pokazivač na strukturu podataka koja opisuje datoteke, poglavlja i pasuse. U zavisnosti od arhitekture vaše hipertekstualne baze podataka, vrednost KEYVALUE može imati identifikaciju dokumenta baze podataka, poglavlja i pasusa kodirane u svom nizu bitova. Ovde je važna stvar da ste se pripremili da napravite HiperTree tako što ćete izdvojiti ključne reči, kombinovati svaku od njih sa vrednošću podataka koje treba vratiti i sortirati ih. Imaćemo primer tog procesa kasnije u ovoj koloni.

U nekim aplikacijama, možda bi bilo prikladno dalje obraditi ključeve nakon sortiranja. Duplicirane vrednosti bi bile izbrisane, a vrednost podataka povezana sa svakom vrednošću ključa postala bi pokazivač na složeniji zapis podataka ili strukturu koja opisuje lokaciju i značaj vrednosti ključa.

Izgradnja HiperTree sastoji se od čitanja ključeva u nizu i popunjavanja čvorova. Kada je čvor pun, sledeća vrednost se dodaje, u nizu, sledećem višem čvoru sa brojem čvora trenutnog čvora kao pridruženom vrednošću podataka. Naredni ključevi se dodaju novom čvoru na istom nivou kao i onaj koji je popunjen. Ovaj uzlazni rast je rekurzivna operacija.

Pozivate addkei funkciju da biste dodali čvorove u HiperTree. Njegovi parametri su pokazivač na string ključa i KEIVALUE povezanu sa ključem. Kada prvi put pozovete ovu funkciju, ona kreira indeksnu datoteku. Kada završite sa dodavanjem ključeva, pozivate ga sa NULL pokazivačem niza ključa da biste mu rekli da završi. Funkcija addkei poziva rekurzivnu addkeientri funkciju. Ova funkcija je menadžer na nivou stabla za dodavanje ključeva. Pozivi za njega iz addkei-a navode da se ključevi dodaju na nulti nivo. Funkcija addkeientri poziva samu sebe tako što navodi progresivno više nivoe kako se čvorovi pune i stablo raste.

Svaki čvor sadrži broj čvora svog roditelja. Ovaj element podataka podržava pretragu HiperTree-a i zato mora biti izgrađen kada se drvo izgradi. Pošto roditelji rastu kada su deca potpuno odrasla - čini se da su ove metafore unazad - program ne zna broj roditeljskog čvora dok se roditelj ne popuni. O tome brine funkcija usvajanja. Kada funkcija vritenode upiše čvor, ona skenira čvor i izdvaja brojeve čvorova svakog od njegovih potomaka. Zatim poziva funkciju usvajanja da upiše broj roditeljskog čvora u svaki zapis o čvoru dece.

### Pretraživanje hiperdrveta

[LISTING_THREE](#listing_three) je "srchtree.c", koji sadrži funkcije koje pretražuju HiperTree. Nekoliko funkcija menja položaj pokazivača za pretragu na taster. Drugi vraćaju string ključa ili pridruženu vrednost.

Prva funkcija pretrage je taster za pronalaženje. Prosledite mu pokazivač na niz koji sadrži vrednost ključa koju želite da pronađete. Ako pronađe podudaranje, vraća pravu vrednost. U suprotnom vraća lažnu vrednost. U oba slučaja, on pozicionira pokazivače pretrage na ključ koji je prekinuo pretragu. Sada možete pozvati current_value da biste preuzeli KEYVALUE ključa koji je prekinuo pretragu ili current_keistring da biste preuzeli vrednost stringa ključa. Ova poslednja funkcija je korisna kada findkei vrati lažnu vrednost na 4 i kaže da nije pronašao odgovarajući unos u HiperTree-u. Završni ključ je sledeći najviši u nizu za sređivanje hiperstabla.

Postoje i druge funkcije pretrage koje se koriste za navigaciju po HiperTree-u u njegovom razvrstanom nizu. To su firstkei, lastkei, nektkei i prevkei. Oni modifikuju poziciju pokazivača pretrage, a naredni pozivi „current_“ keyvalue i current_keystring će odražavati novu poziciju. Svaka funkcija vraća tačnu vrednost ako može da zadovolji zahtev. Ako firstkey ili lastkey vraćaju lažnu vrednost, indeks je prazan. Ako nektkei vrati lažnu vrednost, pokazivači pretrage su već pozicionirani na kraju sekvence za sređivanje HiperTree-a. Ako prevkei vrati lažnu vrednost, pokazivači pretrage su već pozicionirani na početku sekvence za sređivanje HiperTree-a.

Pretraga HiperTree ključem za pronalaženje počinje u osnovnom čvoru. Funkcije pretrage pronalaze broj korenskog čvora čitanjem zapisa zaglavlja HiperTree. Da bi pretražila stablo, funkcija pretražuje korenski čvor za podudaranje argumenta. Ključevi se snimaju u nizu u čvoru, tako da se pretraga nastavlja s leva na desno. Ako ključ nije tu, funkcija podiže pokazivač čvora levo od ključa koji je prekinuo pretragu, čita taj čvor i počinje ispočetka. Ovo se nastavlja sve dok pretraga ili ne pronađe podudaranje ili se završi u lisnom čvoru.

Kada zatražite KEIVALUE pozivanjem funkcije current_keivalue, algoritam pretrage mora da se kreće od pozicije ključa u sistemu nivoa HiperTree do nivoa lista. KEIVREDNOSTI postoje samo na nivou lista. Viši nivoi sadrže pokazivače na niže nivoe. Ako dođete do ključa u čvoru bez lista, KEIVALUE ćete pronaći tako što ćete se kretati dole do lista na ovaj način. Počnite čitanjem čvora na koji pokazuje pokazivač desno od odgovarajućeg ključa. Zatim pročitajte čvor na koji ukazuje pokazivač čvora nižeg ključa u bloku zaglavlja trenutnog čvora. Nastavite da radite dok ne dođete do lista. Polje pokazivača donjeg ključa sadrži (u uniji) KEIVALUE odgovarajućeg ključa. Nema smisla kada to objašnjavam, ali to zaista funkcioniše na taj način.

### Primer HiperTree

Da bih sve ovo iskoristio, osmislio sam jednostavnu aplikaciju nalik hipertekstu koja pravi HiperTree indeks iz tekstualne datoteke i omogućava vam da ga pretražujete. Počinjemo sa nekom tekstualnom datotekom za testiranje. Trebalo bi da bude napravljen od neukrašenog ASCII-a sa CR/LF parovima koji završavaju svaki red. Koristite svoj uređivač da označite neke vrednosti indeksa ključa u tekstu okružujući tastere ugaonim zagradama (manje od, veće od parova). Napravite mnogo ključeva. Sada možemo da napravimo HiperTree.

[LISTING_FOUR](#listing_four) i [LISTING_FIVE](#listing_five) su "keyextr.c" i "keybuild.c", dva jednostavna programa za filtriranje za pravljenje HiperTree-a. Morate povezati datoteku objekta sa "keybuild.c" s tim od "addkey.c". Compile "keyextr.c" sama po sebi. Pokrenite dva programa zajedno sa SORT filterom (pod pretpostavkom MS-DOS) na ovaj način:

```c
  keyextr<textfile.dat | sort | keybuild
```

gde "textfile.dat" je naziv datoteke ASCII teksta. `<keyextr>` program izdvaja vrednosti ključa koje ste označili ugaonim zagradama u niz navodnika, nakon čega sledi zarez i numerička vrednost koja predstavlja poziciju ključa u tekstualnoj datoteci. Izvučena datoteka bi izgledala ovako:

```c
  "fortran", 125 "the merry widow", 257 "godfrey daniel", 3244
```

SORT filter sortira ekstrahovane vrednosti i šalje ih ka `<keybuild>` program, koji gradi HiperTree. To je sve o tome. Indeks možete pogledati pomoću uslužnog programa za istovar. Njegovo ime je "hyprtree.ndx".

Da biste koristili novi HiperTree indeks, napravićete primer programa na [LISTING_SIX](#listing_six), stranica 159, "hyprsrch.c". Njegova komandna linija navodi ime tekstualne datoteke i niz ključeva za pretragu, kao što je ovaj:

```c
  hyprsrch textfile.dat "windows on the world"
```

Program pretražuje HiperTree pomoću funkcije findkei. Zatim prikazuje ekran teksta koji počinje na poziciji teksta koju predstavlja KEYYVRED ključa koji je prekinuo pretragu. Vrh ekrana prikazuje stvarnu vrednost ključa koja je pronađena zajedno sa pozicijom. Donji deo ekrana je meni koji vam omogućava da pritisnete F, L, N, P ili K da biste prešli na prvi, poslednji, sledeći ili prethodni taster ili da biste napustili i prekinuli program.

### HiperTree Ektended

Mali primer koji smo ovde koristili je samo ukus kako možete da koristite strukturu podataka HiperTree. Tehnika bi se mogla koristiti kao mehanizam za pronalaženje za program za proveru pravopisa ili tezaurus. Može se koristiti kao obrnuti indeks za druge dokumente unutar dokumenta. Može se čak koristiti i u sistemu za pronalaženje nalik na konkordanciju koji smo ugradili u TEKSTSRCH.

Postoje neke oblasti u kojima se HiperTree može poboljšati. Evo nekoliko koji vam padaju na pamet.

U primeru aplikacije, ne vezujemo indeks za datoteku koju indeksira, već zahtevamo da datoteci date naziv u komandnoj liniji. Vaša aplikacija bi bez sumnje upravljala ovom asocijacijom za korisnika. Jedan od načina bi bio da uključite ime tekstualne datoteke kao deo TREEHDR strukture.

Pošto HiperTree čvor sadrži ključeve u razvrstanoj sekvenci, možete dodati tehniku kompresije podataka koja se zove „kodiranje dužine rada“ u strukturu čvora. Mnogi susedni tasteri će početi sa identičnim nizovima znakova kao na ovoj listi:

  program
  programmer
  programming
  progress
  prototype

Jednostavna izlazna sekvenca može da odredi koliko karaktera ključ nasleđuje od onog koji mu prethodi. Lista bi tada mogla izgledati ovako:

  program
  \ 7mer
  \ 8ing
  \ 5ess
  \ 3totype

Svojstva HiperTree-a nasumično pa sekvencijalna čine ga prirodnim za pronalaženje džokera. Ako koristite ? znak na prvoj poziciji tastera („?obbs“, na primer), pretraživali biste sve kombinacije tastera sa slovima i brojevima na prvoj poziciji. Ako je znak pitanja bio dalje u ključu (na primer, „Doc?or Dobbs“), koristili biste funkciju tastera za pronalaženje da biste se pozicionirali blizu verovatnog podudaranja i funkciju sledećeg tastera da biste preuzeli sve mogućnosti, odbacujući one koje se ne uklapaju.

### Uređivanje hiperteksta

Postoje dva alata za uređivanje programera – BTAGS i 4C – koji nude funkcije slične hipertekstu za PC programere koji rade sa sistemom koji uključuje mnogo datoteka izvornog koda. Pogledaćemo svaki od njih redom.

BTAGS je program koji koristite zajedno sa Brief programerovim editorom. Brief je jedan od najpopularnijih programskih uređivača za PC i uključuje sveobuhvatan makro jezik nalik C. BTAGS je pretprocesor izvorne datoteke i skup kratkih makroa koji implementiraju funkciju sličnu onoj koja se koristi u uređivaču Unik vi. Evo kako to funkcioniše.

BTAGS program pravi datoteku "tagova" čitajući sve vaše izvorne datoteke i pronalazeći gde su sve funkcije definisane. Datoteka sa oznakama je u skladu sa Unik formatom ctags, koji identifikuje svaki identifikator funkcije, datoteku u kojoj je deklarisana i string preuzet iz deklaracije. String je napravljen tako da ako uređivač pretraži datoteku za string, pronaći će deklaraciju funkcije.

BTAGS kratki makroi dodaju komandne tastere u Brief editor. Sa kursorom na pozivu funkcije, pritisnete Ctrl-T, a makro otvara novi kratki prozor, čita datoteku u kojoj je funkcija deklarisana i pozicionira kursor na deklaraciju funkcije. Ako niste blizu poziva funkcije koju želite, Ctrl-E vam omogućava da unesete ime funkcije. Ctrl-B vas vraća tamo gde ste bili pre poslednjeg Ctrl-T ili Ctrl-E.

BTAGS radi samo sa deklaracijama funkcija i tipovima. Bilo bi bolje da beleži i eksterne varijable i članove strukture. Još jedna manja smetnja je što Ctrl-T makro radi samo ako je kursor na prvom znaku imena funkcije. Dobijate Kratak izvorni kod makro jezika za makroe, tako da možete hakovati ispravku za taj ako želite.

4C je programerski uređivač koji je sličan BTAGS-u po tome što njegov program za analizu izvornog koda proizvodi datoteku sa oznakama kompatibilnom sa ctagsom. 4C proizvodi ctags zapise za sve konstrukcije jezika C, a ne samo za deklaracije funkcija. 4C uključuje sopstveni uređivač, koji koristi datoteku oznaka da bi vam omogućio da skačete kroz izvorne datoteke tako što ćete staviti kursor bilo gde na ime promenljive i pritisnuti funkcijski taster. Ako više volite da koristite sopstveni uređivač, možete reći 4C da ga pozove umesto svog.

4C uređivač i njegovo pozivanje uređivača određenog od strane korisnika koriste strategiju prikaza koju smatram neprirodnom. Ako pozovete glavnu funkciju, to je sve što dobijate. Ne možete stranica dalje ili ispred njega. Ako pozovete drugu funkciju ili promenljivu, to je sve što vidite. Smatram da je frustrirajuće što moram da zovem funkciju po imenu kada već znam da se pojavljuje na dometu skrolovanja od mesta gde sam u tom trenutku.

Na SD90 prošlog februara, 4C ljudi su pokazali novu verziju 4C koju planiraju da izdaju kasnije ove godine. Poboljšana verzija pruža interfejse za razne uređivače, uključujući Brief, Multi-edit, Vedit, ME i Slick, dok analizator izvornog koda podržava C++, objektno orijentisane Pascals, Modula-2 i ASM. Upit baze podataka omogućava traženje prema imenu datoteke i direktorijumu i, za objektno orijentisane jezike, traženje po klasama i hijerarhiji klasa.

Rezultat je da mi se sviđa BTAGS integracija sa Briefom jer je Brief uređivač koji koristim. Sviđa mi se što je 4C uključio sve C konstrukcije u njihovu datoteku oznaka. Oba proizvoda koriste izvorne analizatore koji proizvode datoteke sa oznakama kompatibilnim sa ctagovima, pa sam pokušao očigledno. Koristio sam 4C da napravim datoteku sa oznakama i BTAGS kratke makroe da ih obradim. Naravno da nije uspelo, ali na površini izgleda da bi neko hakovanje to dovelo u red. Možda kasnije.

#### LISTING_ONE

```c
/* ---------- hyprtree.h --------- */

#define NODELEN 256         /* length of a node   */
#define MAXLEVELS 10        /* tree levels        */
#define MAXKEYLENGTH 25     /* maximum key length */

#define TRUE 1
#define FALSE !TRUE

#define KSPACE (NODELEN-sizeof(NODEHDR))
#define HYPERTREE "hyprtree.ndx"

/* ----- computes length of a key in a node -------- */
#define keylength(nd,kp) (strlen(kp) + 1 + \
    ((nd).nodehdr.isleaf?sizeof(KEYVALUE):sizeof(NODEPOINTER)))

/* ---------- node pointers and key values ---------- */
typedef long KEYVALUE;
typedef int NODEPOINTER;

typedef union {
    KEYVALUE keyvalue;
    NODEPOINTER nodepointer;
} KEYPOINTER;

/* ---------- header record for a hypertree ---------- */
typedef struct {
    NODEPOINTER rootnode;
} TREEHDR;

/* ---- header structure for a hypertree node -------- */
typedef struct  {
    int isleaf;         /* true if the node is a leaf */
    NODEPOINTER parent; /* node number of parent */
    KEYPOINTER lower;   /* keys < this node (or value if leaf)*/
} NODEHDR;

/* --------- node record for a hypertree ---------- */
typedef struct {
    NODEHDR nodehdr;
    char keys[KSPACE];
} TREENODE;

/* ------------- prototypes --------------- */
void addkey(char *key, KEYVALUE keyvalue);
int findkey(char *key);
int firstkey(void);
int lastkey(void);
int nextkey(void);
int prevkey(void);
KEYVALUE current_keyvalue(void);
char *current_keystring(void);
```

#### LISTING_TWO

```c
/* ---------------- addkey.c -------------- */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "hyprtree.h"

static void addkeyentry(int level, char *key,
                KEYPOINTER keypointer, NODEPOINTER lowernode);
static void nullnode(int level);
static int freespace(TREENODE node);
static void writenode(int level);
static void adopt(NODEPOINTER child, NODEPOINTER parent);

static TREENODE *nodes[MAXLEVELS];
static NODEPOINTER nodenbr[MAXLEVELS];
static NODEPOINTER nextnode;

static FILE *htree;
static TREEHDR th;

/*
 *  Add a key string value and its associated KEYVALUE to
 * the hypertree.
 */
void addkey(char *key, KEYVALUE keyvalue)
{
    KEYPOINTER keypointer;
    if (key == NULL)    {
        /* ------ terminal key, complete the tree ------ */
        if (htree != NULL)  {
            int level = 0;
            /* -------- write the unwritten nodes -------- */
            while (nodes[level] != NULL)    {
                writenode(level);
                free(nodes[level]);
                level++;
            }
            /* ------ update the header block ---------- */
            th.rootnode = nodenbr[level-1];
            fseek(htree, 0L, SEEK_SET);
            fwrite(&th, sizeof(TREEHDR), 1, htree);

            fclose(htree);
        }
    }
    else    {
        keypointer.keyvalue = keyvalue;
        if (strlen(key) <= MAXKEYLENGTH)
            addkeyentry(0, key, keypointer, 0);
    }
}

static void addkeyentry(int level, char *key,
                KEYPOINTER keypointer, NODEPOINTER lowernode)
{
    char *kp;

    if (nodes[level] == NULL)   {
        /* -------- build a new node ---------- */
        if (level == 0) {
            /* ------ building a new tree ------ */
            htree = fopen(HYPERTREE, "wb+");
            /* ----- write a NULL header for now ----- */
            fwrite(&th, sizeof(TREEHDR), 1, htree);
        }
        if ((nodes[level] = malloc(sizeof(TREENODE))) == NULL)  {
            fputs("\nOut of memory!", stderr);
            exit(1);
        }
        nodes[level+1] = NULL;
        nullnode(level);
        /* ---- point to the node of the lower keys --- */
        nodes[level]->nodehdr.lower.nodepointer = lowernode;
        /* --- assign the next node number to this node --- */
        nodenbr[level] = ++nextnode;
    }
    if (keylength(*nodes[level],key) >
            freespace(*nodes[level]))   {
        /* -------- this node is full -------- */
        KEYPOINTER keyp;
        NODEPOINTER lowernode;
        /* ---- write the node to the index file ---- */
        writenode(level);
        /* ---- remember the node nbr at this level ---- */
        lowernode = nodenbr[level];
        /* --- assign a new node number to this level --- */
        nodenbr[level] = ++nextnode;
        /* --- grow or add to parent with the current key --- */
        memset(&keyp, 0, sizeof(KEYPOINTER));
        keyp.nodepointer = nodenbr[level];
        addkeyentry(level+1, key, keyp, lowernode);
        /* - now set the node at this level to NULL values - */
        nullnode(level);
        nodes[level]->nodehdr.lower = keypointer;
    }
    else    {
        /* ------ insert the key into the node ------ */
        kp = nodes[level]->keys;
        /* ----- scan to the end of the node ----- */
        while (*kp)
            kp += keylength(*nodes[level], kp);
        strcpy(kp, key);
        /* ----- attach the key pointer to the key ----- */
        kp += strlen(kp)+1;
        if (level == 0)
            *((KEYVALUE *)kp) = keypointer.keyvalue;
        else
            *((NODEPOINTER *)kp) = keypointer.nodepointer;
    }
}

/* --------- build a null node ------------ */
static void nullnode(int level)
{
    memset(nodes[level], 0, NODELEN);
    nodes[level]->nodehdr.isleaf = level == 0;
}

/* ------ compute space remaining in a node ------- */
static int freespace(TREENODE node)
{
    int sp = KSPACE;
    char *kp = node.keys;

    while (*kp) {
        sp -= keylength(node,kp);
        kp += keylength(node,kp);
    }
    return sp;
}

/* ------ write a completed node ------- */
static void writenode(int level)
{
    long where = (nodenbr[level]-1)*NODELEN+sizeof(TREEHDR);

    fseek(htree, where, SEEK_SET);
    fwrite(nodes[level], NODELEN, 1, htree);
    if (level)  {
        /* - this is a parent node, update its children - */
        char *kp = nodes[level]->keys;
        adopt(nodes[level]->nodehdr.lower.nodepointer,
                    nodenbr[level]);
        while (*kp) {
            adopt(*((NODEPOINTER *)(kp+strlen(kp)+1)),
                    nodenbr[level]);
            kp += keylength(*nodes[level], kp);
        }
    }
}

/* ---- write a parent node number into a child node ---- */
static void adopt(NODEPOINTER child, NODEPOINTER parent)
{
    NODEHDR nodehdr;
    long here = ftell(htree);
    long where = (child-1)*NODELEN+sizeof(TREEHDR);

    fseek(htree, where, SEEK_SET);
    fread(&nodehdr, sizeof(NODEHDR), 1, htree);
    nodehdr.parent = parent;
    fseek(htree, where, SEEK_SET);
    fwrite(&nodehdr, sizeof(NODEHDR), 1, htree);
    fseek(htree, here, SEEK_SET);
}
```

#### LISTING_THREE

```c
/* ----------- srchtree.c ------------ */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "hyprtree.h"

/* ---------- search packet ----------- */
typedef struct  {
    NODEPOINTER node;
    char *ky;
} SEARCH;

FILE *htree;
static TREEHDR th;
static TREENODE node;
static SEARCH current;

static void readnode(NODEPOINTER nodepointer);
static void opentree(void);

/*
 * Find a specified key in the tree.
 * Return TRUE if found.
 * Return FALSE if not found.
 */
int findkey(char *key)
{
    opentree();
    if (th.rootnode)    {
        SEARCH save = current;
        readnode(th.rootnode);
        while (TRUE)    {
            int cmp;
            current.ky = node.keys;
            while (*current.ky) {
                cmp = stricmp(key, current.ky);
                if (cmp < 0)    {
                    save = current;
                    break;
                }
                if (cmp == 0)
                    return TRUE;
                current.ky += keylength(node, current.ky);
            }
            if (!node.nodehdr.isleaf)   {
                if (current.ky == node.keys)
                    readnode(node.nodehdr.lower.nodepointer);
                else
                    readnode(*((NODEPOINTER *)
                        (current.ky-sizeof(NODEPOINTER))));
            }
            else    {
                current = save;
                readnode(current.node);
                break;
            }
        }
    }
    return FALSE;
}

/*
 * Find the first sequential key in the tree.
 * Return TRUE if found.
 * Return FALSE if not found (the tree is empty).
 */
int firstkey(void)
{
    opentree();
    if (th.rootnode)    {
        readnode(th.rootnode);
        while (!node.nodehdr.isleaf)
            readnode(node.nodehdr.lower.nodepointer);
        current.ky = node.keys;
        return TRUE;
    }
    return FALSE;
}

/*
 * Find the last sequential key in the tree.
 * Return TRUE if found.
 * Return FALSE if not found (the tree is empty).
 */
int lastkey(void)
{
    NODEPOINTER np;
    opentree();
    if (th.rootnode)    {
        int len = 0;
        np = th.rootnode;
        current.node = th.rootnode;
        current.ky = node.keys;
        do  {
            SEARCH save = current;
            readnode(np);
            current.ky = node.keys;
            while (*current.ky) {
                len = keylength(node, current.ky);
                current.ky += len;
            }
            if (current.ky == node.keys)    {
                readnode(save.node);
                current.ky = save.ky;
                break;
            }
            else
                np = *((NODEPOINTER *)
                    (current.ky-sizeof(NODEPOINTER)));
        } while (!node.nodehdr.isleaf);
        current.ky -= len;
        return TRUE;
    }
    return FALSE;
}

/*
 * Find the next sequential key in the tree.
 * Return TRUE if found.
 * Return FALSE if not found (the tree is empty or at the end).
 */
int nextkey(void)
{
    opentree();
    if (th.rootnode)    {
        if (current.ky == NULL)
            return firstkey();
        current.ky += keylength(node, current.ky);
        if (!node.nodehdr.isleaf)   {
            readnode(*((NODEPOINTER *)
                    (current.ky-sizeof(NODEPOINTER))));
            current.ky = node.keys;
            while (!node.nodehdr.isleaf)
                readnode(node.nodehdr.lower.nodepointer);
        }
        /* ----- while at the end of a node ------ */
        while (*current.ky == '\0') {
            NODEPOINTER child = current.node;
            if (node.nodehdr.parent == 0)
                break;
            readnode(node.nodehdr.parent);
            current.ky = node.keys;
            if (child == node.nodehdr.lower.nodepointer)
                break;
            while (*current.ky) {
                NODEPOINTER this = *((NODEPOINTER *)
                    (current.ky+strlen(current.ky)+1));
                current.ky += keylength(node, current.ky);
                if (this == child)
                    break;
            }
        }
        return *current.ky != '\0';
    }
    return FALSE;
}

/*
 * Find the previous sequential key in the tree.
 * Return TRUE if found.
 * Return FALSE if not found
 * (the tree is empty or at the beginning).
 */
int prevkey(void)
{
    char *kp;
    opentree();
    if (th.rootnode)    {
        if (current.ky == NULL)
            return lastkey();
        if (!node.nodehdr.isleaf)   {
            /* ----- navigate to end of the lower leaf ----- */
            NODEPOINTER np;
            if (current.ky == node.keys)
                np = node.nodehdr.lower.nodepointer;
            else
                np = *((NODEPOINTER *)
                    (current.ky-sizeof(NODEPOINTER)));
            while (!node.nodehdr.isleaf)    {
                readnode(np);
                current.ky = node.keys;
                while (*current.ky)
                    current.ky += keylength(node, current.ky);
                np = *((NODEPOINTER *)
                    (current.ky-sizeof(NODEPOINTER)));
            }
        }
        /* ----- while at the beginning of a node ------ */
        while (current.ky == node.keys) {
            NODEPOINTER child = current.node;
            if (node.nodehdr.parent == 0)
                break;
            readnode(node.nodehdr.parent);
            current.ky = node.keys;
            if (child == node.nodehdr.lower.nodepointer)
                continue;

            while (*current.ky) {
                NODEPOINTER this = *((NODEPOINTER *)
                    (current.ky+strlen(current.ky)+1));
                current.ky += keylength(node, current.ky);
                if (this == child)
                    break;
            }
        }
        /* -------- go to previous key in node -------- */
        if (current.ky != node.keys)    {
            kp = node.keys;
            while (kp+keylength(node, kp) < current.ky)
                kp += keylength(node, kp);
            current.ky = kp;
            return TRUE;
        }
    }
    return FALSE;
}

/*
 * Return the key value associated with the most recently
 * retrieved key.
 */
KEYVALUE current_keyvalue(void)
{
    KEYVALUE rtn;
    if (!node.nodehdr.isleaf)   {
        SEARCH save = current;
        current.ky += keylength(node, current.ky);
        while (!node.nodehdr.isleaf)    {
            NODEPOINTER np;
            if (current.ky == node.keys)
                np = node.nodehdr.lower.nodepointer;
            else
                np = *((NODEPOINTER *)
                    (current.ky-sizeof(NODEPOINTER)));
            readnode(np);
            current.ky = node.keys;
        }
        rtn = node.nodehdr.lower.keyvalue;
        current = save;
        readnode(save.node);
        return rtn;
    }
    return *((KEYVALUE *)(current.ky+strlen(current.ky)+1));
}

/*
 * Return the key string value of the most recently
 * retrieved key.
 */
char *current_keystring(void)
{
    return current.ky;
}

/* -------- open the tree ----------- */
static void opentree(void)
{
    if (htree == NULL)  {
        if ((htree = fopen(HYPERTREE, "rb")) == NULL)   {
            fputs("\n" HYPERTREE " not found", stderr);
            exit(1);
        }
        fread(&th, sizeof(TREEHDR), 1, htree);
    }
}

/* ---------- read a specified node ------------- */
static void readnode(NODEPOINTER nodepointer)
{
    long where = (nodepointer-1)*NODELEN+sizeof(TREEHDR);

    fseek(htree, where, SEEK_SET);
    fread(&node, NODELEN, 1, htree);
    current.node = nodepointer;
}
```

#### LISTING_FOUR

```c
/* -------- keyextr.c ---------- */

/*
 * Build a test HyperTree from standard input.
 * <> delimiters define the key values.
 * This is pass one, which builds the sortable table.
 */

#include <stdio.h>
#include <string.h>
#include "hyprtree.h"

#define KEYTOKEN '<'
#define TERMINAL '>'
#define QUOTE    '"'
#define ESCAPE   '\\'

void main(void)
{
    int c;
    while ((c = getchar()) != EOF)  {
        if (c == KEYTOKEN)  {
            long fileposition = ftell(stdin)-1;
            int counter = 0;
            putchar(QUOTE);
            while ((c = getchar()) != TERMINAL) {
                if (c == EOF || counter++ == MAXKEYLENGTH)
                    break;
                if (c == QUOTE)
                    putchar(ESCAPE);
                putchar(c);
            }
            putchar(QUOTE);
            putchar(',');
            printf("%ld\n", fileposition);
        }
    }
}
```

#### LISTING_FIVE

```c
/* ----------- keybuild.c ----------- */

/*
 * Build a test HyperTree from standard input.
 * This is pass three, which builds the HyperTree
 * from the sorted table.
 */

#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include "hyprtree.h"

#define QUOTE    '"'
#define ESCAPE   '\\'

void main(void)
{
    int c;
    char key[MAXKEYLENGTH+1];
    char kv[20];
    KEYVALUE keyvalue;

    while ((c = getchar()) != EOF)  {
        if (c == QUOTE) {
            int counter = 0;
            char *cp = key;
            while ((c = getchar()) != QUOTE)    {
                if (c == EOF || counter++ == MAXKEYLENGTH)
                    break;
                if (c == ESCAPE)
                    c = getchar();
                *cp++ = c;
            }
            *cp = '\0';
            if (getchar() == ',')   {
                char *kp = kv;
                while ((c = getchar()) != '\n' && c != EOF)
                    *kp++ = c;
                *kp = '\0';
                keyvalue = atol(kv);
                addkey(key, keyvalue);
            }
        }
    }
    addkey(NULL, keyvalue);
}
```

#### LISTING_SIX

```c
/* ------------- hyprsrch.c -------------- */

/*
 * Search the test HyperTree for a match on the
 * command-line parameter.
 * Display a screen of text starting at the
 * position indicated for the matching (or closest)
 * entry in the HyperTree.
 */

#include <stdio.h>
#include <string.h>
#include <ctype.h>
#include <conio.h>
#include "hyprtree.h"

#define SCRLINES 25

void main(int argc, char *argv[])
{
    if (argc < 3)
        fprintf(stderr, "Usage: hyprsrch textfile keyvalue");
    else    {
        int kb = 0;
        FILE *fp = fopen(argv[1], "r");
        if (fp == NULL) {
            fprintf(stderr, "No such file as %s", argv[1]);
            return;
        }
        findkey(argv[2]);
        while (toupper(kb) != 'Q')  {
            int lc = 4, c;
            KEYVALUE kv = current_keyvalue();
            printf("\n%s (%ld)", current_keystring(), kv);
            printf("\n------------------------------------\n");
            if (kv) {
                fseek(fp, kv, SEEK_SET);
                while ((c = getc(fp)) != EOF && lc < SCRLINES){
                    if (c == '\n')
                        lc++;
                    putchar(c);
                }
            }
            printf("\nF = first, "
                     "L = last, "
                     "N = next, "
                     "P = previous, "
                     "Q = quit...");
            kb = getch();
            switch (toupper(kb))    {
                case 'F':
                    firstkey();
                    break;
                case 'L':
                    lastkey();
                    break;
                case 'N':
                    nextkey();
                    break;
                case 'P':
                    prevkey();
                    break;
                default:
                    break;
            }
        }
    }
}
```
