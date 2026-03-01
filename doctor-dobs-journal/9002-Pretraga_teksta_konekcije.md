
# C PROGRAMMING 9002

## TEKXTSRCH Connections - Al Stevens

Ovog meseca nastavljamo sa projektom TEXTSRCH, koji je započeo pre dva meseca. Da biste napravili ceo projekat, biće vam potreban izvorni fajl ekpress.c iz decembra, ekinterp.c i tektsrch.c fajlovi iz januara, i nekoliko izvornih datoteka predstavljenih ovog meseca.

TEXTSRCH je sistem za pronalaženje teksta koji obezbeđuje indeks sličan konkordanciji u bazi tekstualnih podataka. Vi obezbeđujete datoteke teksta, a TEXTSRCH ugrađuje indeks obrnutih reči u datoteke. Kasnije, kada krenete da tražite neku zaboravljenu referencu na nešto, koristite jezik upita TEXTSRCH da sastavite upit, a TEXTSRCH pronalazi i izveštava vam datoteke koje odgovaraju vašoj pretrazi. Pre dva meseca sam opisao sintaksu upita i prošlog meseca razvio njegov tumač. Ovog meseca gradimo indeksne datoteke i povezujemo sve zajedno.

TEXTSRCH se sastoji od nekoliko široko korišćenih tehnika. Pored svoje korisnosti kao alata za upravljanje tekstom, pruža primere heširanja, binarnih stabala, raščlanjivanja komandne linije, raščlanjivanja izraza, infiksne i postfiksne notacije izraza i tumačenja izraza.

[LISTING_ONE](#listing_one) je "textsrch.h", verzija 3. Prvu verziju smo napravili pre dva meseca i dodali je prošlog meseca. Većina dodataka ovog meseca su prototipovi funkcija za kod koji sledi, ali postoji još jedan značajan dodatak. Promenljiva MAKSFILES će uticati na veličinu indeksa vaše baze podataka. On određuje maksimalan broj tekstualnih datoteka koje TEXTSRCH indeks može da podrži. Ako promenite ovaj broj, promenićete veličinu mape bitova koja je potrebna interpretatoru izraza. Zapamtite, bit mapa ima jedan bit po datoteci u bazi podataka. Kada se veličina bit mape promeni, menja se i veličina jedne od indeksnih datoteka. Datoteka pod nazivom "TEKST.NDKS" uključuje, između ostalog, kopiju bit mape koja odgovara svakoj jedinstvenoj reči u bazi podataka.

Pre nego što pređemo na indeksni kod, pogledajmo dve funkcije opšte namene koje TEXTSRCH koristi i koje bi vam mogle biti korisne u drugim projektima.

### Raščlanjivanje komandne linije

[LISTING_TWO](#listing_two), je "cmdline.c", izvorna datoteka koja sadrži funkciju parse_cmdline. Funkcija obrađuje parametre komandne linije programa i jedini je deo TEXTSRCH-a koji je orijentisan na bilo koju specifičnu arhitekturu jer radi sa MS-DOS-om i Turbo C-om. Možete je lako ponoviti za drugi kompajler ili drugu arhitekturu. Njegova svrha je da raščlani specifikacije imena datoteke iz komandne linije u diskretna imena datoteka i da pozove određenu funkciju za obradu svake datoteke. Pored toga, parse_cmdline će osetiti prisustvo prekidača opcija komandne linije i podesiti unose u pridruženom nizu u logička tačna i lažna stanja u skladu sa tim. TEXTSRCH ne koristi ovu poslednju funkciju, ali je parse_cmdline ipak podržava.

Kada vaš program prvi put počne, on poziva parse_cmdline i prosleđuje konvencionalne argc i argv promenljive koje su dostupne kao parametri glavnoj funkciji. Vaš program takođe prenosi adresu 128-bajtnog niza prekidača i adresu funkcije. Funkcija, obezbeđena u vašem programu, će obraditi datoteke, jednu po jednu.

Niz opcija sadrži početnu podrazumevanu postavku za prekidače komandne linije. Ova izjava testira niz:

```c
if (array('a'))
...
```

Kada bi komandna linija imala +a, ovaj test bi bio tačan. Ako je parametar bio -a ili ako je podrazumevana vrednost imala nultu vrednost na 61. (ASCII pretpostavljenoj) poziciji, test bi bio lažan.

Funkcija bira svaku specifikaciju datoteke i poziva funkciju kojoj je adresiran poslednji parametar u pozivu parse_cmdline. Ako eksplicitno imenujete datoteke u komandnoj liniji, svaka od njih se obrađuje naizmjenično. Možete koristiti džokere ili prefiks imena datoteke sa znakom @ da biste naveli da se imena datoteka koje treba obraditi snimaju u tekstualnoj datoteci.

Ako koristite džoker kartice, datoteke koje odgovaraju dvosmislenoj specifikaciji se obrađuju onim redosledom kojim ih pronalazi skeniranje direktorijuma. Koristio sam Turbo C funkcije findfirst i findnekt za skeniranje direktorijuma. Microsoft C ima slične, ali različite, pod nazivom _dos_findfirst i _dos_findnekt. Drugi prevodioci imaju svoje verzije ovih funkcija ili, barem, načine da direktno pozovu MS-DOS da bi koristili MS-DOS funkcije koje obavljaju ove operacije. Ako koristite drugi operativni sistem, onda morate da obezbedite druge načine da dobijete isti rezultat. Ako koristite operativni sistem koji ne podržava skeniranje direktorijuma ili dvosmislene specifikacije imena datoteke, onda morate da koristite datoteku sa prefiksom @ sa imenima datoteka koje podržava parse_cmdline.

### Binarno drvo

[LISTING_THREE](#listing_three) je "bintree.c". Sadrži funkcije za pravljenje i pretraživanje binarnog stabla. Dotično stablo se koristi za ono što nazivamo „šumnim“ rečima i uskoro ću objasniti tu funkciju, ali prvo morate da razmotrite funkcije addtree i srchtree i binarna stabla uopšte.

Binarno stablo je struktura podataka za pretragu koja olakšava brzu pretragu ključnih vrednosti. Svaki unos u stablu sadrži vrednost koju treba pretražiti i dva pokazivača -- pokazivač na unose sa vrednostima koje su veće od trenutne i pokazivač na unose koji su niži. Čak i sa velikim brojem unosa, broj poređenja potrebnih za pronalaženje vrednosti je relativno mali.

Funkcija addtree dodaje unose stablu, koje je u početku prazno. On dodeljuje memoriju za unos iz gomile, a zatim poziva srchtree da vidi da li je unos već tamo ili, ako nije, gde treba da bude umetnut.

Nakon što je drvo izgrađeno, koristimo funkciju srchtree da nam kaže da li je vrednost u stablu. U ovoj implementaciji binarnog stabla nema drugih vrednosti podataka u unosu. Koristimo drvo da odredimo prisustvo ili odsustvo vrednosti, ništa više.

Binarna stabla su najefikasnija kada se unosi dodaju stablu nasumičnim redosledom. Ako biste ga napravili od uređene liste unosa, drvo bi imalo jednu dugu granu, a pretrage bi bile neefikasne. Postoje algoritmi za balansiranje stabala za binarna stabla, ali nam nije potreban za našu upotrebu ovde jer drvo gradimo jednom, a naš originalni redosled je nasumičan.

### Bučne reči

Koristimo binarno stablo za listu reči buke. Reči buke su one reči koje su toliko uobičajene da možemo pretpostaviti da se pojavljuju u svakoj datoteci. Reč "the" je tipična bučna reč. Pošto možemo da napravimo tu pretpostavku, možemo da zaobiđemo takve reči kada pravimo naš indeks, čime se štedi prostor indeksa. Isto tako, možemo zaobići pretragu indeksa za takve reči tokom preuzimanja, čime ćemo uštedeti vreme.

Funkcija build_noisevords u bintree.c gradi binarno stablo iz tekstualne datoteke koja sadrži sve reči buke. [LISTING_FOUR](#listing_four), strana 142, je buka.1st, mala datoteka bučnih reči koju sam proizvoljno izabrao. Možda biste želeli da pregledate listu i izmenite je. Što više reči možete da unesete u listu bučnih reči, to će vaš indeks biti efikasniji.

### Izgradnja indeksa

Prošlog meseca smo zaustavili proces pretraživanja teksta koristeći uslužni program GREP za obradu naših upita. Ovog meseca odbacujemo tu metodu i pravimo obrnuti indeks u našu bazu podataka. Da bismo napravili indeks, izdvajamo svaku reč iz svake datoteke u bazi podataka. Od reči koristimo algoritam heširanja za izračunavanje slučajne adrese. Nasumična adresa ukazuje na SLOTS.NDKS, datoteku slotova sa jednim slotom koji je dostupan za svaku moguću slučajnu adresu. Za ovaj projekat ćemo pretpostaviti maksimalno 64K reči, tako da će postojati 64K slotova.

Svaki slot sadrži dug ceo broj sa vrednošću -1 ako se slot ne koristi. Ako je slot u upotrebi, on sadrži pomak karaktera u tekstualnoj indeksnoj datoteci, pod nazivom TEKST.NDKS. Pomak ukazuje na prvi znak zapisa promenljive dužine u TEKST.NDKS. Zapis sadrži odgovarajuću tekstualnu reč, bit mapu datoteke i pokazivač lanca zapisa.

Bit mapa u TEKST.NDKS zapisu sadrži jedan bit za svaku tekstualnu datoteku u bazi podataka. Ako je bit u mapi bitova jedan, reč se pojavljuje u datoteci koja je povezana sa bitom.

Datoteka pod nazivom "FILELIST.NDKS" sadrži nazive tekstualnih datoteka u bazi podataka. Pomak bita u mapi bitova i ofset imena u FILELIST.NDKS su isti.

Pokazivač lanca u TEKST.NDKS zapisu upravlja onim rečima koje heširaju uobičajene slotove. Kada se reč hešira u slot koji je prethodna reč koristila, imamo koliziju. Dodaćemo novi zapis na kraj TEKST.NDKS i upisati njegov pomak karaktera u pokazivač u zapisu na koji ukazuje unos slota. Ovo stvara lanac. Slot pokazuje na prvu reč koja je heširana u slot. Zapis prve reči ukazuje na drugu i tako dalje. Naredni sudari produžavaju lanac.

Kod koji pravi i pretražuje ove datoteke nalazi se u [LISTING_FIVE](#listing_five) "index.c".

Izgradnjom indeksa upravlja program koji počinje [LISTING_SIX](#listing_six), "bldindek.c". Poziva funkciju parse_cmdline da izdvoji imena datoteka iz specifikacija na komandnoj liniji i da izvrši funkciju indek_file za svaku datoteku koju treba indeksirati. Funkcija indek_file otvara navedenu datoteku i poziva funkciju ektract_vord (objašnjeno sledeće) sve dok u datoteci više nema reči koje treba izdvojiti. Funkcija srchtree nam govori da li je reč bučna reč snimljena u binarnom stablu. Ako nije, dodajemo reč u indeks pozivanjem funkcije addindek, koja se nalazi u indek.c.

### Izdvajanje reči

Da bismo napravili indeks (i da bismo napravili binarno stablo reči sa bukom) potrebna nam je funkcija koja izdvaja svaku reč iz tekstualne datoteke i normalizuje je u formu pogodnu za indeksiranje. Funkcija ektract_vord u tekstu.c, [LISTING_SEVEN](#listing_seven), stranica 144, radi upravo to. Prosledite mu FILE pokazivač otvorene datoteke i pokazivač karaktera gde će kopirati sledeću reč, a on radi ostalo. Proces normalizacije se sastoji od preskakanja znakova koji se ne smatraju tekstom i zatim odabira svih tekstualnih znakova do sledećeg netekstualnog karaktera. Za svoje potrebe, biram grupe znakova koje uključuju abecedne znakove, cifre, donju crtu (_), znak plus (+) i znak funte (#). Ovo mi omogućava da indeksiram tekstualne reči, reference na C++, imena funkcija i direktive pretprocesora. Funkcija ektract_vord pretvara abecedne znakove u mala slova. Druge aplikacije će imati drugačije zahteve, tako da is-TEKST makro u tekt.c omogućava da definišete sopstvene kriterijume za izbor.

### Algoritam heširanja

Heširanje je kada izračunate slučajni broj iz ključnog argumenta. Zatim možete koristiti broj kao slučajnu adresu u datoteci u kojoj postoje informacije vezane za argument. Sa heširanjem počinjete sa ključem, izračunavate adresu i koristite adresu da pronađete zapis, sve u treptaju oka. Ili bi bar tako izgledalo. U praksi ima malo više toga.

Heširaćemo nasumični broj iz svake reči u našim tekstualnim datotekama i kasnije iz reči koje koristimo kao argumente u našim upitima. Algoritam koji sam odabrao je labava adaptacija onog koji se pojavio u članku Stiva Helera „Proširivo heširanje“ u izdanju DDJ-a iz novembra 1989. godine. Dodaje sedmobitnu ASCII vrednost svakog znaka u reči na početno nultu vrednost heša i pomera ukupnih sedam bitova ulevo. Ova petlja dodavanja pomeranja se nastavlja za svaki znak u reči.

Postoji onoliko različitih algoritama za heširanje koliko i formata podataka za heširanje. Ideja je da se izračuna slučajni broj koji je ravnomerno raspoređen u definisanom opsegu vrednosti. Ako možete da imate 100.000 zapisa o zalihama u bazi podataka, algoritam mora da izračuna iz ključa za kontrolu inventara slučajni broj između 1 i 100.000. Uobičajena tehnika je da se ključ svede na numeričku vrednost i podeli tu vrednost prostim brojem koji je najbliži opsegu. Ostatak tog deljenja se koristi kao slučajni broj.

U našem pristupu odlučili smo da opseg ide na 64K. Prema tome, 16-bitni ceo broj koji izračunavamo iz tekstualnog niza treba da bude dovoljan kao slučajni broj. Dalje smanjenje ne bi trebalo biti potrebno. Naše ograničenje od 64K je približno broju različitih reči koje možemo očekivati da ćemo pronaći u bazi tekstualnih podataka. Većina proznih dela će imati mnogo manje od tog broja jedinstvenih reči. Čak i ako napravimo indeks za neku knjigu koja premašuje taj broj, onda naša strategija kolizije obezbeđuje da svaka reč ima mesto u indeksu.

Compute_hash funkcija u indek.c je naš algoritam heširanja.

### TEXTSRCH preuzimanja

Uspostavili smo naš jezik upita i tumača u ranijim serijama. Sada kada možemo da napravimo prave indekse, moramo da zamenimo stubove od prošlog meseca pravim procesom pretraživanja. [LISTING_EIGHT](#listing_eight) je "search.c" i zamenjuje prošlomesečni program search.c. Prava stvar je jednostavnija od stubića jer se većina posla sada obavlja u indek.c. Funkcija pretrage poziva srchtree da bi videla da li se reč nalazi na listi bučnih reči. Ako je tako, funkcija pretrage vraća bit mapu sa svim bitovima postavljenim na jedan jer se pretpostavlja da reči buke postoje u svim tekstualnim datotekama u bazi podataka. Ako reč nije šumna reč, funkcija pretrage poziva search_indek u indek.c da bi izvela bit mapu koja kaže koje datoteke u bazi podataka sadrže tu reč.

Search_indek funkcija poziva compute_hash da razvije slučajnu adresu unosa reči u SLOT.NDKS. Taj unos sadrži pomak karaktera u odnosu na prvu reč koja koristi taj slot. Čitamo pomak i tražimo tu lokaciju zapisa u TEKST.NDKS. Ako je reč u tom zapisu ista kao ona koju tražimo, imamo pogodak. Ako ne, i nema više zapisa vezanih za utor, imamo promašaj. Krećemo se kroz lanac ako postoji i upoređujemo svaku reč u njemu sa onom koju tražimo. Ako dobijemo pogodak, bit mapa povezana sa podudarnim zapisom je ona koja je vraćena. U suprotnom se vraća mapa bitova sa svim nulama.

Upravo opisani proces pretraživanja je samo za jednu reč. Interpretator izraza od prošlog meseca kombinuje pretrage za sve reči u izrazu sa Bulovim operatorima i razvija jednu bit mapu koja predstavlja rezultat pune pretrage.

Datoteka search.c takođe sadrži funkciju process_result, koja nije ništa pametnija od one koju smo ubili prošlog meseca. Sve što radi je da prikaže na konzoli imena datoteka koje odgovaraju kriterijumima pretrage. Šta ćete uraditi sa tom listom zavisi od vas. Pogledajte diskusiju o Mogućim poboljšanjima za neka razmišljanja o ovom pitanju.

### Pokretanje TEXTSRCH

Da biste napravili indeks, skupite sve svoje tekstualne datoteke i shvatite kako ćete ih navesti u komandnoj liniji. Ako su razbacani po vašim poddirektorijumima, možda biste želeli da napravite njihovu listu u datoteci. Koristite uređivač teksta da napravite datoteku i stavite svaki naziv datoteke u poseban red. Ovo su različite komande za pravljenje indeksa:

```sh
bldindex *.txt
bldindex textfile.001 textfile.002 textfile.003 
bldindex @files.lst
```

Ove formate možete mešati ovako:

```sh
bldindex *.txt textfile.001 @files.lst
```

Specifikacije datoteke mogu imati putanje.

Ako datoteke pod nazivom SLOTS.NDKS, TEKST.NDKS i FILELIST.NDKS već postoje, dodaćete ih u postojeći indeks. Pokušajte da ne dodajete datoteke koje ste već indeksirali jer ova praksa dovodi do nepotrebne suvišnosti u vašim indeksnim datotekama. Ako datoteke ne postoje, pravite novi indeks.

Pravljenje indeksa traje dugo, pa biste možda želeli da to radite u malim koracima, nekoliko datoteka odjednom. Nestanak struje može da vas natera da počnete iznova. Tekstualne datoteke moraju biti dostupne tokom faze pravljenja, ali mogu biti i na drugim mestima tokom preuzimanja. Proces preuzimanja samo identifikuje specifikacije datoteke kakve su postojale kada je indeks napravljen. Preuzimanja ne uključuju same tekstualne datoteke.

Da biste izvršili preuzimanje, pokrenite program tektsrch odakle se mogu pročitati tri .NDKS datoteke. Unesite izraze upita kao što ste to učinili prošlog meseca i pregledajte spisak datoteka koje su rezultat. Null izraz upita završava program.

### Moguća poboljšanja

TEXTSRCH ima veliku moć i veliki potencijal. Kao što je ovde objavljeno, to je podskup moćnijeg sistema za obradu teksta koji sam razvio za aplikacije inženjerske dokumentacije. Koristim verziju koju vidite ovde bez poboljšanja za ličnu upotrebu, ali postoji nekoliko proširenja koja biste mogli razmotriti. Evo nekih od njih.

Pretraživanje fraza -- TEXTSRCH preuzimanja su zasnovana na pretraživanju reči jer je indeks napravljen od diskretnih reči. Pretragu fraze možete približiti kombinovanjem svih reči u frazi u izraz upita sa logičkim operatorom AND. Ovo preuzimanje bi isporučilo imena datoteka koje sadrže sve reči u frazi, ali vam ne bi reklo da li se sama fraza nalazi u bilo kojoj, svim ili nijednoj od datoteka. Možete pretraživati svaku datoteku za samu određenu frazu koristeći Boier-Moore ili sličan algoritam za podudaranje teksta. Ovaj pristup bi zahtevao modifikaciju funkcije unosa izraza da bi se prepoznala razlika između fraza i pojedinačnih reči. Funkcija process_result mora biti proširena da bi se izvršila naknadna pretraga samih tekstualnih datoteka.

Pretraživanje divljih kartica – TEXTSRCH pretražuje jednostavan obrazac reči bez obzira na velika i mala slova. Možete dodati "?" vild card promenom funkcije pretrage u search.c da izvrši uzastopne pretrage koristeći svaki mogući znak gde se pojavio džoker znak.

### Integracija sa programom za obradu teksta

Industrijski sistem iz kojeg potiče TEXTSRCH je integrisan sa procesorom teksta. Kada se lista datoteka prikaže korisniku, on ili ona bira jednu, a program pokreće program za obradu teksta govoreći mu da učita izabranu datoteku i da se pozicionira do prvog pojavljivanja odgovarajuće reči ili fraze. Ova implementacija povezuje dve aplikacije zajedno i izlazi iz okvira ove kolone.

### Šta je dobro?

Kako možete koristiti TEXTSRCH? Provodim dosta vremena čitajući i pišući elektronsku poštu i razgovore na forumima na mreži. Ponekad moram da se vratim i pronađem poruku u kojoj se spominje nešto čega želim da se setim. Znaš kako to ide. Neko komentariše nešto što jedva privlači vašu pažnju. Nekoliko meseci kasnije radite na novom projektu i taj komentar bi vam bio od pomoći kada biste se samo setili ko ga je napravio i šta je rekao. Sa TEXTSRCH indeksima u mojoj staroj pošti obično mogu pronaći poruke koje se odnose na predmetnu temu.

Sve svoje kolumne, članke i rukopise knjiga čuvam u TEXTSRCH bazama podataka. Tako je, ne mogu uvek da se setim šta sam napisao, kada sam to napisao ili gde, a TEXTSRCH pomaže mom neuspešnom pamćenju (uvek je druga stvar).

Radim još jedan trik za indeksiranje sa TEXTSRCH. Pravim lažne rukopise koji simuliraju desetine članaka, priručnika i knjiga koje čitam svakog meseca. Lažni rukopisi ne sadrže ništa više od ključnih reči iz članaka i knjiga. Sledeći TEXTSRCH indeksi pomažu mi da pronađem štampane kopije gde se materijal pojavljuje. Jednog dana kada knjige i članci budu dostupni na mašinski čitljivim medijima ili kada budem imao pouzdan OCR skener, moći ću direktno da napravim indekse i zaobiđem lažni proces rukopisa.

Postoji još jedna upotreba koja će biti od interesa za C programere. Ako stavite samo ključne reči C (int, long, tipedef i tako dalje) u listu reči buke i napravite indeks iz izvornog koda za ogroman projekat, u TEXTSRCH-u imate početke zgodne unakrsne reference funkcija i promenljivih. Nije vredno truda ako razvijate novi sistem, ali ako ste nasledili jednu od onih ogromnih nedokumentovanih aplikacija za održavanje, TEXTSRCH bi vam mogao uštedeti mnogo GREP vremena.

#### LISTING_ONE

```c
/* ----------- textsrch.h ---------- */

#define OK    0
#define ERROR !OK

#define MXTOKS 25           /* maximum number of tokens */
#define MAXFILES 64         /* maximum number of files  */
#define MAXWORDLEN 25       /* maximum word length      */
#define MAPSIZE MAXFILES/16 /* number of ints/map       */

#define SLOTINDEX  "slots.ndx"
#define TEXTINDEX  "text.ndx"
#define FILELIST   "filelist.ndx"

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

/* --------------- textsrch prototypes --------------- */
struct postfix *lexical_scan(char *expr);
struct bitmap exinterp(void);
struct bitmap search(char *word);
void process_result(struct bitmap);

/* -------------- binary tree prototypes ------------- */
void addtree(char *s);
int srchtree(char *s);
void delete_tree(void);
void build_noisewords(void);

/* ------------- command line prototypes ------------- */
void parse_cmdline(int,char **,char *,void (*)(char *));

/* ---------------- text prototypes ------------------ */
void extract_word(FILE *fp, char *s);

/* --------------- index prototypes ------------------ */
void open_database(void);
void init_database(void);
void close_database(void);
void addindex(char *word, int fileno);
void indexing(char *filename);
int getbit(struct bitmap *map1, int bit);
struct bitmap search_index(char *word);
char *text_filename(int fileno);
```

#### LISTING_TWO

```c
/* ----------- cmdline.c ----------- */

#include <stdio.h>
#include <dir.h>
#include <string.h>
#include "textsrch.h"

/*
 * Parse a command line:
 *  filename1 filename2 ... filenamen
 *  @filelist
 *  wild cards
 *  -+x option list (-a +b -c -xyz +pdq)
 *  any mix of the above
 */

/* ---- parse a command line for options and file names ---- */
void parse_cmdline(int argc, char *argv[], char *options,
                void (*func)(char *fn))
{
    char path[65];
    FILE *fp;
    while (argc-- > 1)  {
        switch (**++argv)   {
            case '/':
            case '+':
                /* -------- add an option --------- */
                while (options && *++*argv)
                    options[**argv] = 1;
                break;
            case '-':
                /* -------- remove an option --------- */
                while (options && *++*argv)
                    options[**argv] = 0;
                break;
            case '@':
                /* ----- a file of file path/names ----- */
                if ((fp = fopen(*argv+1, "rt")) != NULL)
                    while ((fgets(path, 65, fp)) != NULL)   {
                        path[strlen(path)-1] = '\0';
                        (*func)(path);
                    }
                break;
            default:
                /* ---- a file spec on the command line ---- */
                if (strchr(*argv, '*') || strchr(*argv, '?')) {
                    /* ------ an ambiguous file spec ------- */
                    struct ffblk ff;
                    char *cp;
                    int rtn;

                    /* ---- copy the ambiguous file spec ---- */
                    strcpy(path, *argv);

                    /* ---- find the filename part ---- */
                    if ((cp = strrchr(path, '\\')) == NULL)
                        if ((cp = strrchr(path, ':')) == NULL)
                            cp = path-1;
                    cp++;

                    /* ---- search for matches ---- */
                    rtn = findfirst(*argv, &ff, 0);
                    while (rtn == 0)    {
                        strcpy(cp, ff.ff_name);
                        (*func)(path);
                        rtn = findnext(&ff);
                    }
                }
                else
                    /* ----- an unambiguous file spec ----- */
                    (*func)(*argv);
                break;
        }
    }
}
```

#### LISTING_THREE

```c
/* ------------ bintree.c ---------- */

#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include "textsrch.h"

struct bintree {
    struct bintree *lower;
    struct bintree *higher;
    char wd[1];
};

static struct bintree *first, *next;

/* ---------- add a string to a binary tree ----------- */
void addtree(char *s)
{
    struct bintree *tp;
    int intree;

    tp = malloc(sizeof (struct bintree) + strlen(s));
    if (tp == NULL)
        return;
    strcpy(tp->wd, s);
    tp->lower = tp->higher = NULL;
    if ((intree = srchtree(s)) != 0)    {
        if (first == NULL)
            first = tp;
        if (next != NULL)   {
            if (intree < 0)
                next->lower = tp;
            else
                next->higher = tp;
        }
    }
}

/* ------ Search a binary tree for a string.
    Return 0 if the string is in the tree.
    Return < 0 or > 0 if not.
    If not, next -> the node where insertion may occur. --- */

int srchtree(char *s)
{
    struct bintree *this;
    int an = -1;
    this = next = first;
    while (this != NULL)    {
        if ((an = strcmp(s, this->wd)) == 0)
            break;
        next = this;
        this = ((an < 0) ? this->lower : this->higher);
    }
    return an;
}

/* ------- delete the nodes of a branch of the tree ------ */
static void delete_nodes(struct bintree *nd)
{
    if (nd->lower)
        delete_nodes(nd->lower);
    if (nd->higher)
        delete_nodes(nd->higher);
    free(nd);
}

/* ------- free all memory allocated for the tree -------- */
void delete_tree(void)
{
    delete_nodes(first);
}

/* ------- build a binary tree of noise words -------- */
void build_noisewords(void)
{
    FILE *fp;
    char word[MAXWORDLEN+1];
    /* ---- open the noise word file ---- */
    if ((fp = fopen("NOISE.LST", "rt")) != NULL)    {
    /* ---- extract words and add them to the list ---- */
        while (!feof(fp))   {
            extract_word(fp, word);
            /* -------- search the noise word list -------- */
            addtree(word);
        }
        fclose(fp);
    }
}
```

#### LISTING_FOUR

```sh
so very what he she her both there if above only it again done our then from just my along left who may we all these them us after once need through onto others can want where would nor none with do here been was see own since off this not will which itself that each to take down on you under at their however the an as even have whose a said before or nearly below are those possible alike begin out than having two know when way often together by many thus whatever i his past another though other entire in against am most taken instead him because for might too rather few soon either near about every until neither beyond your better must usually several does such something using put more whether sent any later like and now they also upon close be use of is should could were into made further used let how enough its himself known never become sure over next up among had causes has no old some come but while me 
```

#### LISTING_FIVE

```c
/* ----------- index.c ------------- */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "textsrch.h"

static void setbit(struct bitmap *map1, int bit);
static unsigned compute_hash(char *word);

static FILE *slots, *text, *flist;
static char path[65];
int file_count = 0;

/* ------ open or create the index files for building ------ */
void open_database(void)
{
    unsigned ctr = 0xffff;
    long empty = -1L;
    build_noisewords();
    if ((slots = fopen(SLOTINDEX, "r+b")) == NULL)
        slots = fopen(SLOTINDEX, "w+b");
    if (slots != NULL)  {
        if ((text = fopen(TEXTINDEX, "r+b")) == NULL)
            text = fopen(TEXTINDEX, "w+b");
        if (text != NULL)   {
            if ((flist = fopen(FILELIST, "rt")) != NULL)    {
                while (fread(path, sizeof path, 1, flist))
                    file_count++;
                fclose(flist);
                flist = fopen(FILELIST, "at");
                return;
            }
            /* ---- if the file list does not exist,
                we must be building a new data base ----- */
            if ((flist = fopen(FILELIST, "wt")) != NULL) {
                /* --- preset the slots to -1 values --- */
                printf("\nBuilding index. Please wait...\n");
                while (ctr--)   {
                    if ((ctr % 1000) == 0)
                        putchar('.');
                    fwrite(&empty, sizeof(long), 1, slots);
                }
                file_count = 0;
                return;
            }
            fclose(text);
        }
        fclose(slots);
    }
    printf("\nCannot establish index files");
    exit(1);
}

/* -------- open an index data base for retrieval ------- */
void init_database(void)
{
    build_noisewords();
    /* ---------- open all three index files ---------- */
    if ((slots = fopen(SLOTINDEX, "rb")) != NULL)   {
        if ((text = fopen(TEXTINDEX, "rb")) != NULL)    {
            if ((flist = fopen(FILELIST, "rt")) != NULL) {
                /* ------- count the text files ---------- */
                while (fread(path, sizeof path, 1, flist))
                    file_count++;
                printf("\n%d files in the data base.",
                            file_count);
                return;
            }
            fclose(text);
        }
        fclose(slots);
    }
    printf("\nCannot open Index Data Base");
    exit(1);
}

/* --------- close the index files ---------- */
void close_database(void)
{
    delete_tree();
    fclose(slots);
    fclose(text);
    fclose(flist);
}

/* --------- add a word to the index
        or add the file number to the existing word -------- */
void addindex(char *word, int fileno)
{
    long slotno;
    long hash;
    struct bitmap map1;
    long ptr = -1L;
    char wd[MAXWORDLEN+1];

    /* ------- compute a randon address from the word ------ */
    hash = compute_hash(word);
    hash *= sizeof(long);
    /* ------ read the random slot value -------- */
    fseek(slots, hash, SEEK_SET);
    fread(&slotno, sizeof(long), 1, slots);
    if (slotno == -1L)  {
        /* --- empty slot, add the word to the data base --- */
        fseek(text, 0L, SEEK_END);
        slotno = ftell(text);
        /* ----- set the bit map to this file only ----- */
        memset(&map1, 0, sizeof(struct bitmap));
        setbit(&map1, fileno);
        fwrite(&map1, sizeof(struct bitmap), 1, text);
        /* -- set the chain pointer to the terminal value -- */
        fwrite(&ptr, sizeof(long), 1, text);
        fwrite(word, strlen(word)+1, 1, text);
        /* --- insert the text address into the slot file --- */
        fseek(slots, hash, SEEK_SET);
        fwrite(&slotno, sizeof(long), 1, slots);
        return;
    }
    /* -------- the hashed slot is in use -------- */
    for (;;)    {
        /* --- point to the text index record ---- */
        fseek(text, slotno, SEEK_SET);
        fread(&map1, sizeof(struct bitmap), 1, text);
        fread(&ptr, sizeof(long), 1, text);
        fgets(wd, MAXWORDLEN+1, text);
        /* --- see if the entry matches the word --- */
        if (strcmp(wd, word) == 0)  {
            /* ---- the word matches this entry,
                    set this file's bit ---- */
            setbit(&map1, fileno);
            fseek(text, slotno, SEEK_SET);
            fwrite(&map1, sizeof(struct bitmap), 1, text);
            break;
        }
        /* ----- see if there is a chain ----- */
        if (ptr == -1)  {
            /* ------ end of the chain; add this word ---- */
            long newslotno;
            /* ---- to the end of the text index file ---- */
            fseek(text, 0L, SEEK_END);
            newslotno = ftell(text);
            /* ----- set the bit map to this file only ----- */
            memset(&map1, 0, sizeof(struct bitmap));
            setbit(&map1, fileno);
            fwrite(&map1, sizeof(struct bitmap), 1, text);
            fwrite(&ptr, sizeof(long), 1, text);
            fwrite(word, strlen(word)+1, 1, text);
            /* ----- chain the last entry to this one ----- */
            fseek(text, slotno+sizeof(struct bitmap), SEEK_SET);
            fwrite(&newslotno, sizeof(long), 1, text);
            break;
        }
        /* ------ the text record is chained
            (multiple words hash to the same slot) ---- */
        slotno = ptr;
    }
}

/* ------ search the data base for a match on a word ------ */
struct bitmap search_index(char *word)
{
    long slotno = -1;
    long hash;
    struct bitmap map1;
    char wd[MAXWORDLEN+1];

    /* ----- preset the bit map to all zeros ----- */
    memset(&map1, 0, sizeof(struct bitmap));
    /* ---- compute random slot address for the word ---- */
    hash = compute_hash(word);
    hash *= sizeof(long);
    fseek(slots, hash, SEEK_SET);
    fread(&slotno, sizeof(long), 1, slots);
    /* ------- navigate the chain -------- */
    while (slotno != -1)    {
        fseek(text, slotno, SEEK_SET);
        fread(&map1, sizeof(struct bitmap), 1, text);
        fread(&slotno, sizeof(long), 1, text);
        fgets(wd, MAXWORDLEN+1, text);
        if (strcmp(wd, word) == 0)
            /* ---- the word matches this entry ---- */
            break;
        memset(&map1, 0, sizeof(struct bitmap));
    }
    return map1;
}

/* ---- compute a random address from an ASCII string ---- */
static unsigned compute_hash(char *word)
{
    unsigned hash = 0;
    while (*word)
        hash = (hash << 7) + (hash >> 9) + *word++;
    return hash;
}

/* ------ sets a designated bit in the bit map ------ */
static void setbit(struct bitmap *map1, int bit)
{
    int off = bit / 16;
    int mask = 1 << (bit % 16);
    map1->map[off] |= mask;
}

/* ------ tests a designated bit in the bit map ------ */
int getbit(struct bitmap *map1, int bit)
{
    int off = bit / 16;
    int mask = 1 << (bit % 16);
    return map1->map[off] & mask;
}

/* --------- add a file name to the list ----------- */
void indexing(char *filename)
{
    /* ----- add the file path to the index file list ------ */
    memset(path, '\0', sizeof path);
    strcpy(path, filename);
    fwrite(path, sizeof path, 1, flist);
    file_count++;
}

/* ---- return the file name associated with an offset ---- */
char *text_filename(int fileno)
{
    fseek(flist, (long) (fileno * sizeof path), SEEK_SET);
    fread(path, sizeof path, 1, flist);
    return path;
}
```

#### LISTING_SIX

```c
/* -------------- bldindex.c --------------- */

#include <stdio.h>
#include <string.h>
#include "textsrch.h"

static void index_file(char *filename);

void main(int argc, char *argv[])
{
    open_database();
    printf("\nTextSrch: Building a TextSrch Index File");
    parse_cmdline(argc, argv, NULL, index_file);
    close_database();
}

static void index_file(char *filename)
{
    FILE *fp;
    char word[MAXWORDLEN+1];
    extern int file_count;
    int wdctr = 0;

    printf("\nIndexing %s", filename);
    indexing(filename);
    /* ---- open the object file ---- */
    if ((fp = fopen(filename, "rt")) == NULL)   {
        printf("\nError: No such file");
        return;
    }
    putchar('\n');
    /* ---- extract words and add them to the index ---- */
    while (!feof(fp))   {
        extract_word(fp, word);
        /* -------- search the noise word list -------- */
        if (srchtree(word) != 0)    {
            if ((++wdctr % 100) == 0)
                printf("\r%5d words", wdctr);
            /* ------- add the word to the index ------- */
            addindex(word, file_count-1);
        }
    }
    printf("\r%5d words", wdctr);
    fclose(fp);
}
```

#### LISTING_SEVEN

```c
/* ----------- text.c ------------ */

#include <stdio.h>
#include <ctype.h>
#include "textsrch.h"

#define isTEXT(c) (isalpha(c) || isdigit(c) || \
                   c=='+' || c=='#' || c=='_')

/* --------- extract a word from an input stream ----------- */
void extract_word(FILE *fp, char *s)
{
    int i, c;

    c = i = 0;
    while (!isTEXT(c))
        if ((c = fgetc(fp)) == EOF)
            break;
    while (isTEXT(c))   {
        if  (i++ < MAXWORDLEN)
            *s++ = tolower(c);
        if ((c = fgetc(fp)) == EOF)
            break;
    }
    *s = '\0';
}
```

#### LISTING_EIGHT

```c
/* ---------- search.c ----------- */

/*
 * the TEXTSRCH retrieval process
 */

#include <stdio.h>
#include <string.h>
#include "textsrch.h"

/* ---- process the result of a query expression search ---- */
void process_result(struct bitmap map1)
{
    int i;
    extern int file_count;
    for (i = 0; i < file_count; i++)
        if (getbit(&map1, i))
            printf("\n%s", text_filename(i));
}

/* ------- search the data base for a word match -------- */
struct bitmap search(char *word)
{
    struct bitmap map1;

    memset(&map1, 0xff, sizeof (struct bitmap));
    if (srchtree(word) != 0)
        map1 = search_index(word);
    return map1;
}
```
