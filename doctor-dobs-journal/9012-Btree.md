
# C PROGRAMMING 9012

## Ponovo B-drvo - Al Stivens

U izdanju o hipertekstu iz juna 1990. objavio sam program koji je koristio podskup algoritma B-stabla za izgradnju indeksa HiperTree u tekstualnu datoteku. Tom algoritmu su nedostajale mnoge karakteristike kompletnog B-stabla, jer je bio posvećen jednoj svrsi, konstrukciji i pretraživanju statičkog indeksa u tekstu. Kod B-stabla koji sam koristio bio je adaptacija nekih algoritama B-stabla iz knjige koju sam napisao o korišćenju jezika C za opisivanje, izgradnju i održavanje baza podataka. Sada pišem drugo izdanje knjige da bih nadogradio kod i tehnike na mogućnosti ANSI C, a ova kolona je pregled koda B-stabla kako će se pojaviti u novom radu.

Nije vam potreban sav kod u knjizi da biste koristili ove algoritme. Kao i kod većine koda koji razvijam i objavljujem, funkcije B-stabla su samostalni C alati koje možete koristiti gde god vam je potrebno rešenje B-stabla.

### Šta je B-stablo?

Algoritam B-stabla su 1970. godine razvili R. Beier i E. McCreight. To je strukturirani indeksni mehanizam koji je uravnotežen i koji se samoodržava. Prvo neke definicije. (Moj drugar, Tomi Saunders, veliki kornetista Diksilenda, priča priču o pčelinjem drveću. Ova B-stabla su drugačija, Tomi.)

„Drvo“ je višeslojna hijerarhija „čvorova“. Na vrhu postoji jedan "korenski" čvor. Koren je "roditeljski" čvor za "podređene" čvorove na sledećem nižem nivou. Čvorovi sadrže vrednosti ključeva koji isporučuju odgovarajuće vrednosti podataka. Svaki nadređeni čvor može imati više podređenih čvorova, a bilo koji podređeni čvor ne može imati više od jednog roditelja. Čvorovi na dnu hijerarhije sadrže sve vrednosti podataka za ključeve, čak i ako su vrednosti ključa u čvorovima na višim nivoima podataka, stoga su vrednosti ključeva na višim nivoima ". vektore u zapise u datoteci u kojoj postoje elementi podataka povezani sa ključnim vrednostima.

Uravnoteženo drvo je ono gde se svi putevi od korena do lišća spuštaju na isti broj nivoa. Samoodrživo drvo je ono gde se ravnoteža stabla održava algoritmima za dodavanje i brisanje; drvetu nisu potrebne dodatne procedure za balansiranje stabla.

Čvor B-stabla sadrži fiksni maksimalni broj ključeva koji se naziva m. Algoritam osigurava da će svaki čvor, osim korena, imati najmanje m/2 ključeva. Koren može imati od jednog do m ključeva. Kada je koren prazan, drvo je prazno. Kada je koren jedini nivo drveta, sam koren sadrži listove.

B-stablo ima još jedno zanimljivo svojstvo. Možete pretraživati drvo za proizvoljno odabranu vrednost ključa. Ova pretraga je nasumično pronalaženje. Možete da se krećete po istom stablu u nizu tastera u bilo kom smeru počevši od bilo kog datog tastera. Ova pretraga je sekvencijalno pronalaženje. Prema tome, možete locirati izabranu vrednost ključa i nastaviti odatle kroz ključeve u rastućem ili opadajućem nizu. Ova mešavina se često naziva metodom indeksiranog sekvencijalnog pristupa (ISAM), fraza koju sam prvi put čuo 1960-ih i koristio se za Cobol fajlove glavnog računara. Algoritmi B-stabla postdatiraju ISAM, ali mnoge implementacije ISAM-a koriste B-stabla. Slika 1 ilustruje jednostavno B-stablo. Koristi slova abecede kao ključeve i reči fonetskog alfabeta kao vrednosti podataka. U ovom primeru, m je 2, ali većina B-stabala će imati mnogo veću vrednost m.

Čvor sadrži ključne vrednosti i vektore. Vektori u korenu i podređenim čvorovima su pokazivači na čvorove na sledećem nižem nivou u stablu. Vektori na najnižem nivou su listovi, a oni obično upućuju na zapise podataka. Ključevi u svakom čvoru su u uzlaznom nizu slaganja tako da pretraga može da se nastavi s leva na desno. Neke implementacije B-stabla imaju velike m vrednosti i koriste binarnu pretragu za pretragu svakog čvora.

Čvor ima jedan vektor više nego što ima ključne vrednosti. Svaka vrednost ključa ima vektor koji pokazuje na čvorove sa vrednostima ključa manjim od ove i drugi vektor koji ukazuje na vrednost ključa veću od ove. Pogledajte sliku 1. Osnovni čvor ima ključnu vrednost "I" i dva vektora. Prvi vektor pokazuje na čvor sa tasterima „C“ i „F“, dok drugi vektor pokazuje na čvor sa „L“ i „O“.

Stablo na slici 1 ima tri nivoa. Vektori sa trećeg nivoa su listovi koji ukazuju na zapise podataka koji sadrže vrednosti podataka (reči fonetskog alfabeta) povezane sa ključevima (slova abecede).

Ovde ima mesta za zabunu. Ranije sam rekao da su listovi vrednosti podataka. Sada kažem da su oni pokazivači na vrednosti podataka. B-stablo daje svoj odgovor u obliku lista povezanog sa ključnom vrednošću. List može biti šta god želite. Ali da bi se pojednostavila arhitektura B-stabla, list treba da liči na vektor tako da format čvorova na najnižem nivou bude isti kao format čvorova na višim nivoima. Obično list pokazuje na zapis u drugoj datoteci, a datoteka može biti u nasumičnom redosledu. Pristup datoteci je funkcija preuzimanja iz indeksa.

Struktura o kojoj govorimo izgleda kao obrnuto drvo. Koren je na vrhu, a listovi na dnu. Zbog ove metafore, takvi indeksi se nazivaju „obrnuti“ indeksi. Kao što možete očekivati, možete imati više od jednog invertovanog indeksa u datoteku podataka. Svaki element podataka ili kombinacija elemenata podataka u datoteci može postati ključ indeksa. Vektor lista bi ukazivao na zapis datoteke koji odgovara ključu.

B-stablo može da podrži duplirane vrednosti ključa. Ova funkcija omogućava da jedna vrednost ključa ima nekoliko listova i stoga ukazuje na više od jednog zapisa datoteke. Zapis baze podataka obično ima vrednost "primarnog ključa" koja jedinstveno identifikuje zapis. Datoteka zaposlenih ima jedan zapis po broju zaposlenih. Indeks za primarni ključ ne sme da dozvoli duple vrednosti ključa jer samo jedan zaposleni može da ima dati broj zaposlenog. Ali evidencija o zaposlenima može imati i broj odeljenja i možda ćete želeti da preuzmete sve zaposlene za dato odeljenje. U tom slučaju, indeks broja odeljenja u datoteci zaposlenih mora da podržava duplirane vrednosti jer mnogi zaposleni mogu da rade u istom odeljenju. Element podataka sa indeksom koji podržava više vrednosti ključa je „sekundarni ključ“.

### Pretraživanje B-stabla

Pretraga B-stabla počinje sa ključnim argumentom i isporučuje ili list ili činjenicu da argument ne postoji u B-stablu. Pogledajte ponovo sliku 1. Svaka pretraga počinje u osnovnom čvoru jer ga algoritmi uvek mogu pronaći. Da biste tražili vrednost K, pročitajte osnovni čvor i pretražite ga. Ako pronađete vrednost, skoro ste gotovi. Ako ne, morate preći na sledeći nivo. Da biste nastavili nadole, koristite vektor koji se nalazi iza sledećeg nižeg ključa i pre sledećeg višeg ključa u trenutnom čvoru. U ovom slučaju, koren ima samo vrednost I, tako da, korišćenjem vektora neposredno iza njega, preuzimate L0 čvor u njemu i počinjete ponovo pretragu. Ova pretraga se nastavlja sve dok ne pronađete vrednost ili ne budete na najnižem nivou. Pronašli biste J na donjem nivou, tako da je vektor desno od J njegov pridruženi list i, u stvari, ukazuje na zapis podataka koji sadrži vrednost JULIJA.

Pretraga je malo složenija kada se argument podudara sa ključem na višem nivou. Ako ste tražili C, pretraga bi se zaustavila na CF čvoru na drugom nivou. Vektor neposredno iza C nije list. Ukazuje na donji čvor. Kada se to dogodi, koristite vektor za čitanje donjeg čvora. Krajnji levi vektor u tom čvoru je onaj koji želite. Nastavljate da čitate čvorove koristeći krajnji levi vektor dok ne dođete do najnižeg nivoa. Krajnji levi vektor na najnižem nivou je list za argument. Posmatrajte to da biste pronašli I iou read LO čvor, koristite njegov krajnji levi vektor da pročitate JK čvor, a zatim koristite njegov krajnji levi vektor kao list koji pokazuje na INDIJA.

### Dodavanje ključa B-stablu

Kada želite da dodate ključ u B-stablo, morate da obezbedite vrednost ključa i vrednost lista algoritmu za umetanje. Za aplikaciju baze podataka, možda dodajete zapis, tako da list može biti broj zapisa baze podataka.

Da biste dodali ključ, prvo ga morate potražiti. Ako postoji, morate videti da li B-stablo podržava višestruke vrednosti ključa. Neki neće, posebno oni koji su određeni za primarni ključ datoteke baze podataka. Ako vrednost postoji i indeks je za jedinstvene vrednosti, procedura dodavanja ključa mora da vrati indikaciju greške. Ako vrednost ne postoji ili drvo dozvoljava duplikate, umetćete ključ u stablo.

Umetanje ključa uvek počinje na najnižem nivou čvora. Ako ste pronašli ključ na višem nivou, morate se kretati do najnižeg nivoa gde se čuva list odgovarajućeg ključa. Ako niste pronašli ključ, pretraga je završila na najnižem nivou na mestu gde ključ ide i tu ubacujete ključ.

Ključ i list ubacujete u čvor u njegovom nizu slaganja. Ako nakon umetanja čvor i dalje ima m ili manje ključeva, umetanje je obavljeno. Međutim, ako čvor sada ima m+1 ključeva, čvor mora da se podeli.

Podela čvora uključuje izgradnju novog čvora i kopiranje druge polovine ključeva u njega, ostavljajući prvu polovinu u originalnom čvoru. Ključ u centru podele mora biti umetnut u roditelj originalnog čvora sa vektorom do novog čvora. To umetanje koristi isti kod kao i prvo, tako da se cepanje i rast B-stabla nastavlja naviše do korena. Kada se koren popuni i podeli, algoritam gradi novi koren sa samo jednim ključem.

### Brisanje ključa sa B-stabla

Brisanje ključa se uvek dešava na najnižem nivou. Ako se ključ za brisanje nalazi u višem čvoru, idete nadole do najnižeg čvora, pomerate njegovu krajnju levu vrednost ključa preko vrha vrednosti ključa koji želite da obrišete, a zatim nastavljate da brišete krajnji levi ključ iz čvora najnižeg nivoa.

Da bi održao ravnotežu stabla, algoritam za brisanje gleda na dva čvora sa obe strane trenutnog na istom nivou. Ovi čvorovi su braća i sestre trenutnim. Ako je moguće, algoritam redistribuira ključeve između trenutnog čvora i jednog od njegovih braće i sestara. Dalje, ako bi se ključevi dva čvora, kao rezultat brisanja, uklapali u jedan čvor sa prostorom za još jedan, algoritam kombinuje ključeve oba čvora sa ključem njihovog zajedničkog roditelja i briše jedan od čvorova. Ključ mora biti obrisan iz nadređenog, što je procedura koja ponavlja algoritam brisanja, omogućavajući stablu da se smanji u visini kako ključevi odlaze.

### Kod B-stabla

Postoje tri izvorne datoteke za funkcije B-stabla. [LISTING_ONE](#listing_one), je "database.h", datoteka koja uključuje one elemente iz većeg sistema baze podataka koje koriste funkcije B-stabla. Ako idemo dalje u ovu tehnologiju u budućim kolonama, moraćemo da zamenimo ovu datoteku. [LISTING_TWO](#listing_two) je "btree.h", datoteka zaglavlja koja opisuje format zapisa B-stabla kao i prototipove za funkcije koje bi eksterni programi pozvali. [LISTING_THREE](#listing_three), je "btree.c", kod koji implementira B-stabla.

Struktura treehdr u btree.h opisuje zapis zaglavlja na prednjoj strani svakog B-stabla. Stavka korenskog čvora je vektor za korenski čvor. Stavka dužine ključa je dužina svakog ključa. Ključevi u ovoj implementaciji B-stabla su fiksne dužine. Promenljiva m je m vrednost B-stabla.

Sledeća dva elementa, rlsed_node i endnode, podržavaju održavanje datoteka B-stabla. Čvorovi dolaze i odlaze dok ubacujete i brišete ključeve. Promenljiva rlsed_node je vektor prvog u povezanoj listi izbrisanih čvorova. Kada algoritam sabiranja dodaje novi čvor, uzima unos sa ove liste ako postoji. Ako nije, dodaje novi čvor u datoteku B-stabla i povećava promenljivu endnode. Ova tehnika ponovo koristi izbrisane čvorove da eliminiše nepotreban rast stabala.

Zaključana varijabla označava da je B-stablo u upotrebi. Funkcija koja pokreće obradu B-stabla postavlja ovu zastavicu i upisuje zapis zaglavlja nazad u datoteku. Funkcija koja prekida obradu briše zastavicu. Pošto je integritet B-stabla stvar sa više zapisa i zato što se čvorovi i zaglavlje motaju u memoriji dok ne treba da budu zapisani, sistemski kvar bi mogao da ugrozi integritet indeksa. Program vam neće dozvoliti da koristite B-stablo koje je zaključano.

Krajnji levi i krajnji desni vektori ukazuju na krajnji levi i krajnji desni čvor u B-stablu kako se posmatra u njegovom nizu za sređivanje. Ovi vektori omogućavaju trenutni pristup oba kraja stabla.

Struktura čvorova drveta opisuje format zapisa čvora. Svaki čvor ima neke sopstvene informacije zaglavlja praćene listom ključeva i vektora. Promenljiva koja nije lista je nula ako je čvor na najnižem nivou stabla, a inače nije nula. Vektor prntnode ukazuje na roditeljski čvor za ovaj čvor. Ako je ovaj vektor nula, čvor je osnovni čvor, jedini bez roditelja. Vektori lfsib i rtsib ukazuju na levi i desni srodni čvor za trenutni čvor. Varijabla keict je broj ključeva trenutno snimljenih u čvoru. Vektor kei0 je vektor koji će biti levo od krajnjeg levog ključa u čvoru i koji ukazuje na čvor koji sadrži ključeve manje od svih ključeva u ovom čvoru. Niz razmaka ključeva je prostor za ključeve i vektore. Ovaj prostor je dinamički raspoređen prema dužini ključa sa prostorom za m tastera i m vektora. Spil prostor je u strukturi da omogući umetanje ključa da ide do m+1 ključeva pre podele, ali nije deo čvora u datoteci B-stabla.

Funkcije u btree.c implementiraju algoritme B-stabla na način koji vam omogućava da istovremeno pokrećete više od jednog B-stabla. Ako koristite operativni sistem kao što je MS-DOS koji ograničava broj otvorenih datoteka za pokrenuti program, možda ćete želeti da izmenite kod da biste zatvorili i ponovo otvorili datoteke B-stabla u strateškim tačkama.

Tabela 1 navodi funkcije koje pozivate iz svog aplikacijskog programa da biste koristili B-stabla. Da biste koristili funkcije B-stabla, morate napisati program koji se povezuje sa i poziva ove funkcije. Takođe morate obezbediti jednostavnu funkciju za obradu grešaka pod nazivom dberror koja se ne vraća pozivaocu. Postoje dva uslova greške koja će pozvati dberror. Jedan je kada program ne može da dodeli dovoljno gomile. Drugi je bilo kakva greška u čitanju ili pisanju datoteke B-stabla. Ovi uslovi greške stavljaju D_OM i D_IOERR, koji su definisani u bazi podataka.h, u globalnu promenljivu errno.

### B-tree funkcije

- **void build_b(char** ***name, int len)**  
Ova funkcija gradi datoteku B-stabla sa imenom datoteke navedenim u parametru imena. Parametar len određuje dužinu ključa. Funkcija kreira datoteku, izračunava i upisuje sve početne vrednosti za zapis zaglavlja i zatvara datoteku.

- **int btree_init(char** ***ndx_name)**  
Nakon što ste kreirali B-stablo sa build_b, možete ga koristiti u drugim programima pozivanjem btree_init. Parametar imena odgovara imenu sa kojim ste kreirali B-stablo. Funkcija vraća -1 ako B-stablo ne postoji, ako je zaključano, ili ako imate maksimalan broj B-stabala već otvoren kao što je definisano u btree.h globalnom promenljivom MXTREES. U suprotnom, funkcija otvara datoteku, zaključava je i vraća celobrojni broj stabla koji ćete koristiti u svim narednim pozivima funkcije koji se odnose na ovo B-stablo.

- **int btree_close(int tree)**  
Ova funkcija zatvara i otključava B-stablo.

- **RPTR locate(int tree, char *k)**
Ovo je ključna funkcija pretraživanja. Dajete pokazivač na vrednost ključa, a funkcija vraća vektor lista ako podudaranje postoji i nulti vektor ako ne postoji. Datoteka baze podataka.h definiše tip promenljive RPTR. To je integralni tip, a njegova širina definiše opseg vrednosti lista.

- **int deletekey(int tree, char** ***x, RPTR ad)**  
Ovo je funkcija za brisanje ključa sa B-stabla. Prosledite pokazivač karaktera za ključ i RPTR vektor koji odgovara onom koji želite da izbrišete. Ovaj parametar omogućava funkciji da razlikuje više tastera.

- **int insertkey(int tree, char** ***x, RPTR ad, int unique)**  
Ova funkcija dodaje stablu specificiranu vrednost ključa i njenu RPTR vrednost lista. Jedinstveni parametar je TRUE ako vrednost ključa mora biti jedinstvena, a u suprotnom FALSE. Ako pokušate da dodate ključ koji postoji kada je jedinstveni indikator TRUE, funkcija vraća - 1.

- **RPTR firstkey(int tree)**
- **RPTR lastkey(int tree)**
- **RPTR nextkey(int tree)**
- **RPTR prevkey(int tree)**  
Ove četiri funkcije se kreću po B-stablu. Prve dve funkcije pozicioniraju navigaciju na početak ili kraj stabla u njegovom nizu za sređivanje i vraćaju RPTR vektore koji odgovaraju tim pozicijama. Ako je B-stablo prazno, ove funkcije vraćaju nulti RPTR. Druge dve funkcije se kreću napred ili nazad do sledeće vrednosti ključa u sekvenci vraćajući pridruženi RPTR. Možete koristiti ove funkcije nakon lociranja, prvog ključa, poslednjeg ključa ili jedne druge. Oni vraćaju RPTR-ove povezane sa novim pozicijama. Ako pozovete nektkei kada ste već pozicionirani na poslednjem tasteru ili prevkei kada ste već pozicionirani na prvom tasteru, funkcije će vratiti nulti RPTR.

- **RPTR currkey(int tree)**  
Ova funkcija vraća RPTR za trenutnu poziciju ključa.

- **void keyval(int tree, char** ***ky)**  
Ova funkcija kopira vrednost ključa trenutne pozicije ključa u string na koji ukazuje parametar ki.

Postoje mnoge druge funkcije u btree.c, one koje vaš program ne poziva, ali koje algoritmi koriste interno. Funkcija btreescan skenira stablo u potrazi za podudaranjem na navedenom ključu. On modifikuje pokazivače u funkciji za pozivanje da naznači gde se skeniranje zaustavlja i vraća TRUE ili FALSE u zavisnosti od toga da li je pronašao podudaranje ili ne. Poziva funkciju skeniranja čvorova, koja pretražuje pojedinačne čvorove. Ta funkcija poziva compare_keis da testira ključ u čvoru na podudaranje sa ključem argumenta. Funkcija fileaddr vraća vrednost lista za trenutnu poziciju ključa. Poziva funkciju nivoa lista za navigaciju do najnižeg nivoa u B-stablu. Funkcija implode kombinuje ključeve dva srodna čvora, a funkcija redist redistribuira ključeve dva srodna čvora tako da su ključevi ravnomerno raspoređeni između njih. Funkciju usvajanja koriste implode, redist i insert_kei za dodeljivanje novog roditelja podređenom čvoru. Funkcija nektnode dobija novi vektor čvora ili sa objavljene liste povezanih čvorova ili sa kraja datoteke koji će biti dodeljen novom čvoru. Funkcije scannekt i scanprev vrše navigaciju po stablu za nektkei i prevkei. Childptr funkcija čita roditeljski čvor trenutnog i postavlja pokazivač na ključ u roditeljskom čvoru koji ukazuje na ovo dete. Funkcije read_node i vrite_node obavljaju sav ulaz/izlaz čvora za funkcije. Obojica koriste funkciju bseek da traže poziciju čvora.

#### LISTING_ONE

```C
/* --------------- database.h ---------------------- */
#define MXKEYLEN 80  /* maximum key length for indexes     */

#define ERROR -1
#define OK 0

#ifndef TRUE
#define TRUE 1
#define FALSE 0
#endif

typedef long RPTR;  /* B-tree node and file address     */
void dberror(void);

/* ----------- dbms error codes for errno return ------ */
enum dberrors {
   D_OM,      /* out of memory                        */
   D_IOERR    /* i/o error                            */
};
```

#### LISTING_TWO

```c
/* --------------- btree.h ---------------------- */
#define MXTREES 20
#define NODE 512       /* length of a B-tree node          */
#define ADR sizeof(RPTR)

/* ---------- btree prototypes -------------- */
int btree_init(char *ndx_name);
int btree_close(int tree);
void build_b(char *name, int len);
RPTR locate(int tree, char *k);
int deletekey(int tree, char *x, RPTR ad);
int insertkey(int tree, char *x, RPTR ad, int unique);
RPTR nextkey(int tree);
RPTR prevkey(int tree);
RPTR firstkey(int tree);
RPTR lastkey(int tree);
void keyval(int tree, char *ky);
RPTR currkey(int tree);

/* --------- the btree node structure -------------- */
typedef struct treenode    {
   int nonleaf;      /* 0 if leaf, 1 if non-leaf           */
   RPTR prntnode;    /* parent node                        */
   RPTR lfsib;       /* left sibling node                  */
   RPTR rtsib;       /* right sibling node                 */
   int keyct;        /* number of keys                     */
   RPTR key0;        /* node # of keys < 1st key this node */
   char keyspace [NODE - ((sizeof(int) * 2) + (ADR * 4))];
   char spil [MXKEYLEN];  /* for insertion excess */
} BTREE;

/* ---- the structure of the btree header node --------- */
typedef struct treehdr    {
   RPTR rootnode;            /* root node number     */
   int keylength;            /* length of a key      */
   int m;                    /* max keys/node        */
   RPTR rlsed_node;          /* next released node   */
   RPTR endnode;             /* next unassigned node */
   int locked;               /* if btree is locked   */
   RPTR leftmost;            /* left-most node       */
   RPTR rightmost;           /* right-most node      */
} HEADER;
```

#### LISTING_THREE

```C
/* --------------------- btree.c ----------------------- */
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <stdlib.h>
#include "database.h"
#include "btree.h"

#define KLEN bheader[trx].keylength
#define ENTLN (KLEN+ADR)

HEADER bheader[MXTREES];
BTREE trnode;

static FILE *fp[MXTREES];      /* file pointers to indexes   */
static RPTR currnode[MXTREES]; /* node number of current key */
static int currkno[MXTREES];   /* key number of current key  */
static int trx;                /* current tree               */

/* --------- local prototypes ---------- */
static int btreescan(RPTR *t, char *k, char **a);
static int nodescan(char *keyvalue, char **nodeadr);
static int compare_keys(char *a, char *b);
static RPTR fileaddr(RPTR t, char *a);
static RPTR leaflevel(RPTR *t, char **a, int *p);
static void implode(BTREE *left, BTREE *right);
static void redist(BTREE *left, BTREE *right);
static void adopt(void *ad, int kct, RPTR newp);
static RPTR nextnode(void);
static RPTR scannext(RPTR *p, char **a);
static RPTR scanprev(RPTR *p, char **a);
static char *childptr(RPTR left, RPTR parent, BTREE *btp);
static void read_node(RPTR nd, void *bf);
static void write_node(RPTR nd, void *bf);
static void bseek(RPTR nd);
static void memerr(void);

/* -------- initiate b-tree processing ---------- */
int btree_init(char *ndx_name)
{
   for (trx = 0; trx < MXTREES; trx++)
       if (fp[trx] == NULL)
           break;
   if (trx == MXTREES)
       return ERROR;
   if ((fp[trx] = fopen(ndx_name, "rb+")) == NULL)
       return ERROR;
   fread(&bheader[trx], sizeof(HEADER), 1, fp[trx]);
   /* --- if this btree is locked, something is amiss --- */
   if (bheader[trx].locked)    {
       fclose(fp[trx]);
       fp[trx] = NULL;
       return ERROR;
   }
   /* ------- lock the btree --------- */
   bheader[trx].locked = TRUE;
   fseek(fp[trx], 0L, SEEK_SET);
   fwrite(&bheader[trx], sizeof(HEADER), 1, fp[trx]);
   currnode[trx] = 0;
   currkno[trx] = 0;
   return trx;
}

/* ----------- terminate b tree processing ------------- */
int btree_close(int tree)
{
   if (tree >= MXTREES || fp[tree] == 0)
       return ERROR;
   bheader[tree].locked = FALSE;
   fseek(fp[tree], 0L, SEEK_SET);
   fwrite(&bheader[tree], sizeof(HEADER), 1, fp[tree]);
   fclose(fp[tree]);
   fp[tree] = NULL;
   return OK;
}

/* --------Build a new b-tree ------------------ */
void build_b(char *name, int len)
{
   HEADER *bhdp;
   FILE *fp;

   if ((bhdp = malloc(NODE)) == NULL)
       memerr();
   memset(bhdp, '\0', NODE);
   bhdp->keylength = len;
   bhdp->m = ((NODE-((sizeof(int)*2)+(ADR*4)))/(len+ADR));
   bhdp->endnode = 1;
   remove(name);
   fp = fopen(name, "wb");
   fwrite(bhdp, NODE, 1, fp);
   fclose(fp);
   free(bhdp);
}

/* --------------- Locate key in the b-tree -------------- */
RPTR locate(int tree, char *k)
{
   int i, fnd = FALSE;
   RPTR t, ad;
   char *a;

   trx = tree;
   t = bheader[trx].rootnode;
   if (t)    {
       read_node(t, &trnode);
       fnd = btreescan(&t, k, &a);
       ad = leaflevel(&t, &a, &i);
       if (i == trnode.keyct + 1)    {
           i = 0;
           t = trnode.rtsib;
       }
       currnode[trx] = t;
       currkno[trx] = i;
   }
   return fnd ? ad : 0;
}

/* ----------- Search tree ------------- */
static int btreescan(RPTR *t, char *k, char **a)
{
   int nl;
   do    {
       if (nodescan(k, a))    {
           while (compare_keys(*a, k) == FALSE)
               if (scanprev(t, a) == 0)
                   break;
           if (compare_keys(*a, k))
               scannext(t, a);
           return TRUE;
       }
       nl = trnode.nonleaf;
       if (nl)    {
             *t = *((RPTR *) (*a - ADR));
           read_node(*t, &trnode);
       }
   }    while (nl);
   return FALSE;
}

/* ------------------ Search node ------------ */
static int nodescan(char *keyvalue, char **nodeadr)
{
   int i;
   int result;

   *nodeadr = trnode.keyspace;
   for (i = 0; i < trnode.keyct; i++)    {
       result = compare_keys(keyvalue, *nodeadr);
       if (result == FALSE)
           return TRUE;
       if (result < 0)
           return FALSE;
       *nodeadr += ENTLN;
   }
   return FALSE;
}

/* ------------- Compare keys ----------- */
static int compare_keys(char *a, char *b)
{
   int len = KLEN, cm;

   while (len--)
       if ((cm = (int) *a++ - (int) *b++) != 0)
           break;
   return cm;
}

/* ------------ Compute current file address  ------------ */
static RPTR fileaddr(RPTR t, char *a)
{
   RPTR cn, ti;
   int i;

   ti = t;
   cn = leaflevel(&ti, &a, &i);
   read_node(t, &trnode);
   return cn;
}

/* ---------------- Navigate down to leaf level ----------- */
static RPTR leaflevel(RPTR *t, char **a, int *p)
{
   if (trnode.nonleaf == FALSE)    { /* already at a leaf? */
       *p = (*a - trnode.keyspace) / ENTLN + 1;
       return *((RPTR *) (*a + KLEN));
   }
   *p = 0;
   *t = *((RPTR *) (*a + KLEN));
   read_node(*t, &trnode);
   *a = trnode.keyspace;
   while (trnode.nonleaf)    {
       *t = trnode.key0;
       read_node(*t, &trnode);
   }
   return trnode.key0;
}

/* -------------- Delete a key  ------------- */
int deletekey(int tree, char *x, RPTR ad)
{
   BTREE *qp, *yp;
   int rt_len, comb;
   RPTR p, adr, q, *b, y, z;
   char *a;

   trx = tree;
   if (trx >= MXTREES || fp[trx] == 0)
       return ERROR;
   p = bheader[trx].rootnode;
   if (p == 0)
       return OK;
   read_node(p, &trnode);
   if (btreescan(&p, x, &a) == FALSE)
       return OK;
   adr = fileaddr(p, a);
   while (adr != ad)    {
       adr = scannext(&p, &a);
       if (compare_keys(a, x))
           return OK;
   }
   if (trnode.nonleaf)    {
       b = (RPTR *) (a + KLEN);
       q = *b;
       if ((qp = malloc(NODE)) == NULL)
           memerr();
       read_node(q, qp);
       while (qp->nonleaf)    {
           q = qp->key0;
           read_node(q, qp);
       }
   /* Move the left-most key from the leaf
                       to where the deleted key is */
       memmove(a, qp->keyspace, KLEN);
       write_node(p, &trnode);
       p = q;
       trnode = *qp;
       a = trnode.keyspace;
       b = (RPTR *) (a + KLEN);
       trnode.key0 = *b;
       free(qp);
   }
   currnode[trx] = p;
   currkno[trx] = (a - trnode.keyspace) / ENTLN;
   rt_len = (trnode.keyspace + (bheader[trx].m * ENTLN)) - a;
   memmove(a, a+ENTLN, rt_len);
   memset(a+rt_len, '\0', ENTLN);
   trnode.keyct--;
   if (currkno[trx] > trnode.keyct)    {
       if (trnode.rtsib)    {
           currnode[trx] = trnode.rtsib;
           currkno[trx] = 0;
       }
       else
           currkno[trx]--;
   }
   while (trnode.keyct <= bheader[trx].m / 2 &&
                          p != bheader[trx].rootnode)    {
       comb = FALSE;
       z = trnode.prntnode;
       if ((yp = malloc(NODE)) == NULL)
           memerr();
       if (trnode.rtsib)    {
           y = trnode.rtsib;
           read_node(y, yp);
           if (yp->keyct + trnode.keyct <
                   bheader[trx].m && yp->prntnode == z)    {
               comb = TRUE;
               implode(&trnode, yp);
           }
       }
       if (comb == FALSE && trnode.lfsib)    {
           y = trnode.lfsib;
           read_node(y, yp);
           if (yp->prntnode == z)    {
               if (yp->keyct + trnode.keyct <
                                   bheader[trx].m) {
                   comb = TRUE;
                   implode(yp, &trnode);
               }
               else    {
                   redist(yp, &trnode);
                   write_node(p, &trnode);
                   write_node(y, yp);
                   free(yp);
                   return OK;
               }
           }
       }
       if (comb == FALSE)    {
           y = trnode.rtsib;
           read_node(y, yp);
           redist(&trnode, yp);
           write_node(y, yp);
           write_node(p, &trnode);
           free(yp);
           return OK;
       }
       free(yp);
       p = z;
       read_node(p, &trnode);
   }
   if (trnode.keyct == 0)    {
       bheader[trx].rootnode = trnode.key0;
       trnode.nonleaf = FALSE;
       trnode.key0 = 0;
       trnode.prntnode = bheader[trx].rlsed_node;
       bheader[trx].rlsed_node = p;
   }
   if (bheader[trx].rootnode == 0)
       bheader[trx].rightmost = bheader[trx].leftmost = 0;
   write_node(p, &trnode);
   return OK;
}

/* ------------ Combine two sibling nodes. ------------- */
static void implode(BTREE *left, BTREE *right)
{
   RPTR lf, rt, p;
   int rt_len, lf_len;
   char *a;
   RPTR *b;
   BTREE *par;
   RPTR c;
   char *j;

   lf = right->lfsib;
   rt = left->rtsib;
   p = left->prntnode;
   if ((par = malloc(NODE)) == NULL)
       memerr();
   j = childptr(lf, p, par);
   /* --- move key from parent to end of left sibling --- */
   lf_len = left->keyct * ENTLN;
   a = left->keyspace + lf_len;
   memmove(a, j, KLEN);
   memset(j, '\0', ENTLN);
   /* --- move keys from right sibling to left --- */
   b = (RPTR *) (a + KLEN);
   *b = right->key0;
   rt_len = right->keyct * ENTLN;
   a = (char *) (b + 1);
   memmove(a, right->keyspace, rt_len);
   /* --- point lower nodes to their new parent --- */
   if (left->nonleaf)
       adopt(b, right->keyct + 1, lf);
   /* --- if global key pointers -> to the right sibling,
                                   change to -> left --- */
   if (currnode[trx] == left->rtsib)    {
       currnode[trx] = right->lfsib;
       currkno[trx] += left->keyct + 1;
   }
   /* --- update control values in left sibling node --- */
   left->keyct += right->keyct + 1;
   c = bheader[trx].rlsed_node;
   bheader[trx].rlsed_node = left->rtsib;
   if (bheader[trx].rightmost == left->rtsib)
       bheader[trx].rightmost = right->lfsib;
   left->rtsib = right->rtsib;
   /* --- point the deleted node's right brother
                               to this left brother --- */
   if (left->rtsib)    {
       read_node(left->rtsib, right);
       right->lfsib = lf;
       write_node(left->rtsib, right);
   }
   memset(right, '\0', NODE);
   right->prntnode = c;
   /* --- remove key from parent node --- */
   par->keyct--;
   if (par->keyct == 0)
       left->prntnode = 0;
   else    {
       rt_len = par->keyspace + (par->keyct * ENTLN) - j;
       memmove(j, j+ENTLN, rt_len);
   }
   write_node(lf, left);
   write_node(rt, right);
   write_node(p, par);
   free(par);
}

/* ------------------ Insert key  ------------------- */
int insertkey(int tree, char *x, RPTR ad, int unique)
{
   char k[MXKEYLEN + 1], *a;
   BTREE *yp;
   BTREE *bp;
   int nl_flag, rt_len, j;
   RPTR t, p, sv;
   RPTR *b;
   int lshft, rshft;

   trx = tree;
   if (trx >= MXTREES || fp[trx] == 0)
       return ERROR;
   p = 0;
   sv = 0;
   nl_flag = 0;
   memmove(k, x, KLEN);
   t = bheader[trx].rootnode;
   /* --------------- Find insertion point ------- */
   if (t)    {
       read_node(t, &trnode);
       if (btreescan(&t, k, &a))    {
           if (unique)
               return ERROR;
           else    {
               leaflevel(&t, &a, &j);
               currkno[trx] = j;
           }
       }
       else
           currkno[trx] = ((a - trnode.keyspace) / ENTLN)+1;
       currnode[trx] = t;
   }
   /* --------- Insert key into leaf node -------------- */
   while (t)    {
       nl_flag = 1;
       rt_len = (trnode.keyspace+(bheader[trx].m*ENTLN))-a;
       memmove(a+ENTLN, a, rt_len);
       memmove(a, k, KLEN);
       b = (RPTR *) (a + KLEN);
       *b = ad;
       if (trnode.nonleaf == FALSE)    {
           currnode[trx] = t;
           currkno[trx] = ((a - trnode.keyspace) / ENTLN)+1;
       }
       trnode.keyct++;
       if (trnode.keyct <= bheader[trx].m)    {
           write_node(t, &trnode);
           return OK;
       }
       /* --- Redistribute keys between sibling nodes ---*/
       lshft = FALSE;
       rshft = FALSE;
       if ((yp = malloc(NODE)) == NULL)
           memerr();
       if (trnode.lfsib)    {
           read_node(trnode.lfsib, yp);
           if (yp->keyct < bheader[trx].m &&
                       yp->prntnode == trnode.prntnode)    {
               lshft = TRUE;
               redist(yp, &trnode);
               write_node(trnode.lfsib, yp);
           }
       }
       if (lshft == FALSE && trnode.rtsib)    {
           read_node(trnode.rtsib, yp);
           if (yp->keyct < bheader[trx].m &&
                       yp->prntnode == trnode.prntnode)    {
               rshft = TRUE;
               redist(&trnode, yp);
               write_node(trnode.rtsib, yp);
           }
       }
       free(yp);
       if (lshft || rshft)    {
           write_node(t, &trnode);
           return OK;
       }
       p = nextnode();
       /* ----------- Split node -------------------- */
       if ((bp = malloc(NODE)) == NULL)
           memerr();
       memset(bp, '\0', NODE);
       trnode.keyct = (bheader[trx].m + 1) / 2;
       b = (RPTR *)
             (trnode.keyspace+((trnode.keyct+1)*ENTLN)-ADR);
       bp->key0 = *b;
       bp->keyct = bheader[trx].m - trnode.keyct;
       rt_len = bp->keyct * ENTLN;
       a = (char *) (b + 1);
       memmove(bp->keyspace, a, rt_len);
       bp->rtsib = trnode.rtsib;
       trnode.rtsib = p;
       bp->lfsib = t;
       bp->nonleaf = trnode.nonleaf;
       a -= ENTLN;
       memmove(k, a, KLEN);
       memset(a, '\0', rt_len+ENTLN);
       if (bheader[trx].rightmost == t)
           bheader[trx].rightmost = p;
       if (t == currnode[trx] &&
                       currkno[trx]>trnode.keyct)    {
           currnode[trx] = p;
           currkno[trx] -= trnode.keyct + 1;
       }
       ad = p;
       sv = t;
       t = trnode.prntnode;
       if (t)
           bp->prntnode = t;
       else    {
           p = nextnode();
           trnode.prntnode = p;
           bp->prntnode = p;
       }
       write_node(ad, bp);
       if (bp->rtsib)    {
           if ((yp = malloc(NODE)) == NULL)
               memerr();
           read_node(bp->rtsib, yp);
           yp->lfsib = ad;
           write_node(bp->rtsib, yp);
           free(yp);
       }
       if (bp->nonleaf)
           adopt(&bp->key0, bp->keyct + 1, ad);
       write_node(sv, &trnode);
       if (t)    {
           read_node(t, &trnode);
           a = trnode.keyspace;
           b = &trnode.key0;
           while (*b != bp->lfsib)    {
               a += ENTLN;
               b = (RPTR *) (a - ADR);
           }
       }
       free(bp);
   }
   /* --------------------- new root -------------------- */
   if (p == 0)
       p = nextnode();
   if ((bp = malloc(NODE)) == NULL)
       memerr();
   memset(bp, '\0', NODE);
   bp->nonleaf = nl_flag;
   bp->prntnode = 0;
   bp->rtsib = 0;
   bp->lfsib = 0;
   bp->keyct = 1;
   bp->key0 = sv;
   *((RPTR *) (bp->keyspace + KLEN)) = ad;
   memmove(bp->keyspace, k, KLEN);
   write_node(p, bp);
   free(bp);
   bheader[trx].rootnode = p;
   if (nl_flag == FALSE)    {
       bheader[trx].rightmost = p;
       bheader[trx].leftmost = p;
       currnode[trx] = p;
       currkno[trx] = 1;
   }
   return OK;
}

/* ----- redistribute keys in sibling nodes ------ */
static void redist(BTREE *left, BTREE *right)
{
   int n1, n2, len;
   RPTR z;
   char *c, *d, *e;
   BTREE *zp;

   n1 = (left->keyct + right->keyct) / 2;
   if (n1 == left->keyct)
       return;
   n2 = (left->keyct + right->keyct) - n1;
   z = left->prntnode;
   if ((zp = malloc(NODE)) == NULL)
       memerr();
   c = childptr(right->lfsib, z, zp);
   if (left->keyct < right->keyct)    {
       d = left->keyspace + (left->keyct * ENTLN);
       memmove(d, c, KLEN);
       d += KLEN;
       e = right->keyspace - ADR;
       len = ((right->keyct - n2 - 1) * ENTLN) + ADR;
       memmove(d, e, len);
       if (left->nonleaf)
           adopt(d, right->keyct - n2, right->lfsib);
       e += len;
       memmove(c, e, KLEN);
       e += KLEN;
       d = right->keyspace - ADR;
       len = (n2 * ENTLN) + ADR;
       memmove(d, e, len);
       memset(d+len, '\0', e-d);
       if (right->nonleaf == 0 &&
                           left->rtsib == currnode[trx])
           if (currkno[trx] < right->keyct - n2)    {
               currnode[trx] = right->lfsib;
               currkno[trx] += n1 + 1;
           }
           else
               currkno[trx] -= right->keyct - n2;
       }
   else    {
       e = right->keyspace+((n2-right->keyct)*ENTLN)-ADR;
       memmove(e, right->keyspace-ADR,
           (right->keyct * ENTLN) + ADR);
       e -= KLEN;
       memmove(e, c, KLEN);
       d = left->keyspace + (n1 * ENTLN);
       memmove(c, d, KLEN);
       memset(d, '\0', KLEN);
       d += KLEN;
       len = ((left->keyct - n1 - 1) * ENTLN) + ADR;
       memmove(right->keyspace-ADR, d, len);
       memset(d, '\0', len);
       if (right->nonleaf)
           adopt(right->keyspace - ADR,
                           left->keyct - n1, left->rtsib);
       if (left->nonleaf == FALSE)
           if (right->lfsib == currnode[trx] &&
                                   currkno[trx] > n1)    {
               currnode[trx] = left->rtsib;
               currkno[trx] -= n1 + 1;
           }
           else if (left->rtsib == currnode[trx])
               currkno[trx] += left->keyct - n1;
   }
   right->keyct = n2;
   left ->keyct = n1;
   write_node(z, zp);
   free(zp);
}

/* ----------- assign new parents to child nodes ---------- */
static void adopt(void *ad, int kct, RPTR newp)
{
   char *cp;
   BTREE *tmp;

   if ((tmp = malloc(NODE)) == NULL)
       memerr();
   while (kct--)    {
       read_node(*(RPTR *)ad, tmp);
       tmp->prntnode = newp;
       write_node(*(RPTR *)ad, tmp);
       cp = ad;
       cp += ENTLN;
       ad = cp;
   }
   free(tmp);
}

/* ----- compute node address for a new node -----*/
static RPTR nextnode(void)
{
   RPTR p;
   BTREE *nb;

   if (bheader[trx].rlsed_node)    {
       if ((nb = malloc(NODE)) == NULL)
           memerr();
       p = bheader[trx].rlsed_node;
       read_node(p, nb);
       bheader[trx].rlsed_node = nb->prntnode;
       free(nb);
   }
   else
       p = bheader[trx].endnode++;
   return p;
}

/* ----- next sequential key ------- */
RPTR nextkey(int tree)
{
   trx = tree;
   if (currnode[trx] == 0)
       return firstkey(trx);
   read_node(currnode[trx], &trnode);
   if (currkno[trx] == trnode.keyct)    {
       if (trnode.rtsib == 0)    {
           return 0;
       }
       currnode[trx] = trnode.rtsib;
       currkno[trx] = 0;
       read_node(trnode.rtsib, &trnode);
   }
   else
       currkno[trx]++;
   return *((RPTR *)
           (trnode.keyspace+(currkno[trx]*ENTLN)-ADR));
}

/* ----------- previous sequential key ----------- */
RPTR prevkey(int tree)
{
   trx = tree;
   if (currnode[trx] == 0)
       return lastkey(trx);
   read_node(currnode[trx], &trnode);
   if (currkno[trx] == 0)    {
       if (trnode.lfsib == 0)
           return 0;
       currnode[trx] = trnode.lfsib;
       read_node(trnode.lfsib, &trnode);
       currkno[trx] = trnode.keyct;
   }
   else
       currkno[trx]--;
   return *((RPTR *)
       (trnode.keyspace + (currkno[trx] * ENTLN) - ADR));
}

/* ------------- first key ------------- */
RPTR firstkey(int tree)
{
   trx = tree;
   if (bheader[trx].leftmost == 0)
       return 0;
   read_node(bheader[trx].leftmost, &trnode);
   currnode[trx] = bheader[trx].leftmost;
   currkno[trx] = 1;
   return *((RPTR *) (trnode.keyspace + KLEN));
}

/* ------------- last key ----------------- */
RPTR lastkey(int tree)
{
   trx = tree;
   if (bheader[trx].rightmost == 0)
       return 0;
   read_node(bheader[trx].rightmost, &trnode);
   currnode[trx] = bheader[trx].rightmost;
   currkno[trx] = trnode.keyct;
   return *((RPTR *)
       (trnode.keyspace + (trnode.keyct * ENTLN) - ADR));
}

/* -------- scan to the next sequential key ------ */
static RPTR scannext(RPTR *p, char **a)
{
   RPTR cn;

   if (trnode.nonleaf)    {
       *p = *((RPTR *) (*a + KLEN));
       read_node(*p, &trnode);
       while (trnode.nonleaf)    {
           *p = trnode.key0;
           read_node(*p, &trnode);
       }
       *a = trnode.keyspace;
       return *((RPTR *) (*a + KLEN));
   }
   *a += ENTLN;
   while (-1)    {
       if ((trnode.keyspace + (trnode.keyct)
               * ENTLN) != *a)
           return fileaddr(*p, *a);
       if (trnode.prntnode == 0 || trnode.rtsib == 0)
           break;
       cn = *p;
       *p = trnode.prntnode;
       read_node(*p, &trnode);
       *a = trnode.keyspace;
       while (*((RPTR *) (*a - ADR)) != cn)
           *a += ENTLN;
   }
   return 0;
}

/* ---- scan to the previous sequential key ---- */
static RPTR scanprev(RPTR *p, char **a)
{
   RPTR cn;

   if (trnode.nonleaf)    {
       *p = *((RPTR *) (*a - ADR));
       read_node(*p, &trnode);
       while (trnode.nonleaf)    {
           *p = *((RPTR *)
               (trnode.keyspace+(trnode.keyct)*ENTLN-ADR));
           read_node(*p, &trnode);
       }
       *a = trnode.keyspace + (trnode.keyct - 1) * ENTLN;
       return *((RPTR *) (*a + KLEN));
   }
   while (-1)    {
       if (trnode.keyspace != *a)    {
           *a -= ENTLN;
           return fileaddr(*p, *a);
       }
       if (trnode.prntnode == 0 || trnode.lfsib == 0)
           break;
       cn = *p;
       *p = trnode.prntnode;
       read_node(*p, &trnode);
       *a = trnode.keyspace;
       while (*((RPTR *) (*a - ADR)) != cn)
           *a += ENTLN;
   }
   return 0;
}

/* ------ locate pointer to child ---- */
static char *childptr(RPTR left, RPTR parent, BTREE *btp)
{
   char *c;

   read_node(parent, btp);
   c = btp->keyspace;
   while (*((RPTR *) (c - ADR)) != left)
       c += ENTLN;
   return c;
}

/* -------------- current key value ---------- */
void keyval(int tree, char *ky)
{
   RPTR b, p;
   char *k;
   int i;

   trx = tree;
   b = currnode[trx];
   if (b)    {
       read_node(b, &trnode);
       i = currkno[trx];
       k = trnode.keyspace + ((i - 1) * ENTLN);
       while (i == 0)    {
           p = b;
           b = trnode.prntnode;
           read_node(b, &trnode);
           for (; i <= trnode.keyct; i++)    {
               k = trnode.keyspace + ((i - 1) * ENTLN);
               if (*((RPTR *) (k + KLEN)) == p)
                   break;
           }
       }
       memmove(ky, k, KLEN);
   }
}

/* --------------- current key ---------- */
RPTR currkey(int tree)
{
   RPTR f = 0;

   trx = tree;
   if (currnode[trx])    {
       read_node(currnode[trx], &trnode);
       f = *( (RPTR *)
           (trnode.keyspace+(currkno[trx]*ENTLN)-ADR));
   }
   return f;
}

/* ---------- read a btree node ----------- */
static void read_node(RPTR nd, void *bf)
{
   bseek(nd);
   fread(bf, NODE, 1, fp[trx]);
   if (ferror(fp[trx]))    {
       errno = D_IOERR;
       dberror();
   }
}

/* ---------- write a btree node ----------- */
static void write_node(RPTR nd, void *bf)
{
   bseek(nd);
   fwrite(bf, NODE, 1, fp[trx]);
   if (ferror(fp[trx]))    {
       errno = D_IOERR;
       dberror();
   }
}

/* ----------- seek to the b-tree node ---------- */
static void bseek(RPTR nd)
{
   if (fseek(fp[trx],
         (long) (NODE+((nd-1)*NODE)), SEEK_SET) == ERROR) {
       errno = D_IOERR;
       dberror();
   }
}

/* ----------- out of memory error -------------- */
static void memerr(void)
{
    errno = D_OM;
    dberror();
}
```
