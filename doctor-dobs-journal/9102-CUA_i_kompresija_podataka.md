
# C PROGRAMMING 9102

## CUA i kompresija podataka - Al Stivens

### Šifrovanje i kompresija i ko šta poseduje

Nijedna tema u ovoj koloni nije izazvala veći odziv od dve kolone posvećene Standardu za šifrovanje podataka prošle godine. Čini se da je šifrovanje hobi za mnoge programere. Mnogi od vas su mi poslali svoje programe za šifrovanje ne samo za DES algoritam već i za druge metode šifrovanja. Sada imam obimnu biblioteku algoritama za šifrovanje kao rezultat vašeg doprinosa. Sad kad bih imao samo neke tajne.

Neki čitaoci su izrazili zabrinutost što objavljujem implementaciju DES algoritma, misleći da na neki način kršim interese nacionalne bezbednosti. Ako vlada može da objavi detalje algoritma u brošuri koja je dostupna svima, onda bi programeri trebali biti slobodni da pišu kod koji implementira algoritam. Nijedan momak u mantilima mi nije kucao na vrata, tako da sam valjda bezbedan.

Ovo pitanje ponovo pokreće bauk softverskih patenata. DDJ se zainteresovao za ovo pitanje. U junu smo objavili kod koji implementira LZV algoritam kompresije podataka i taj članak je zapalio fitilj. Izgleda da je neko patentirao algoritam. Zbog toga se zapitam šta je zaštićeno takvim patentima. Ako su detalji algoritma javno poznati – kao što su oni LZV i DES – da li nosilac patenta poseduje sve izraze tog algoritma, ili on ili ona poseduje prava samo na implementacije algoritma? Koja je razlika? Da li je objavljivanje izvornog koda implementacija algoritma ili samo izraz kako on funkcioniše? Očigledno, ako je samo implementacija zaštićena i ako objavljivanje izvornog koda nije implementacija, onda časopisi mogu da štampaju programe, ali čitaoci ne mogu da ih koriste. Šta bi to koristilo osim kao vežba o tome kako algoritmi funkcionišu, a kako patentni sistem ne?

Liga za slobodu programiranja napisala je članak "Softverski patenti" (DDJ, novembar 1990). Preziru tu praksu i kažu zašto. Razgovarao sam sa nekim od članova Lige u Bostonu. Igrom slučaja, novembarsko izdanje Boston Magazina ima članak koji govori o Ligi i njenom osnivaču, Ričardu Stolmanu. Većina nas poznaje Stallmana kao autora EMACS editora i GNU-a, sličnog Uniku. Morate kupiti GNU za 150 dolara od Stallmanove druge grupe, Fondacije za slobodni softver, ali vam je dozvoljeno da poklanjate kopije. Unik košta 900 dolara i ne smete davati kopije. Nikada nisam pročitao gde je GNU bar za jednu šestinu dobar od Unika, tako da ne znam da li je to dogovor ili ne. Prema članku Boston Magazina, Stalman, osnivač Lige za slobodu programiranja i Fondacije za slobodni softver, pisaće programe za vas za 260 dolara na sat sve dok program nije vlasnički. Softver bi trebao biti besplatan, ali Stallman sigurno nije.

Pretpostavljam da pod ograničenjem „nije vlasništvo“, Stalmanovi klijenti mogu da prodaju programe koje on piše, ali ne mogu sprečiti svoje kupce da im ih daju. Ima li onih koji uzimaju? Šta kažete na stvari u kući? Većina klijenata koji razvijaju prilagođeni softver isključivo za internu upotrebu smatraju ga vlasničkim, možda da bi ga sakrili od svojih konkurenata. Hmm. Sledio bih Ričardov primer, ali mislim da ne bih mogao da živim sa 520 dolara godišnje.

### Huffman Compression Trees

Ne znam da li Hafmanovo drveće uključuje vlasnički algoritam. Ali moje istraživanje za prošlomesečnu kolumnu „Memori Lane“ nije pronašlo algoritam u mrtvačnici DDJ, pa ću iskoristiti šansu i sada se pozabaviti njime. Možda ću sledećeg meseca pisati kolumnu iz saveznog kantri kluba u vazduhoplovnoj bazi Eglin. Na taj način mogu svaki dan razgovarati sa svojim advokatom, svojim bankarom i nekim kolegama pilotima.

Hafmanov algoritam je oblik kompresije podataka. Drugi su LZV, gore pomenuti, i kodiranje dužine trajanja (RLE), gde se nizovi istog karaktera zamenjuju brojem znakova i karakterom. Koristio sam RLE u jednom od programa za šifrovanje u novembru. Bilo je i drugih RLE članaka u prošlim izdanjima DDJ-a. Savremeni uslužni programi za kompresiju datoteka koriste kombinacije nekoliko algoritama kompresije, ispitujući svaku datoteku da bi videli koji algoritam daje najbolji odnos kompresije. Malo je verovatno da ćete napisati još jedan uslužni program za kompresiju datoteka opšte namene, ali ćete možda morati da koristite kompresiju u nekoj od svojih aplikacija. Programeri koji distribuiraju softver na disketama često ga komprimuju i zahtevaju da ga instalacioni program dekomprimuje za korisnika. Ako koristite neki od komercijalnih ili sharevare uslužnih programa za kompresiju, možda ćete morati da platite licencu za distribuciju programa za dekompresiju. U najmanju ruku, vaša procedura instalacije će možda morati da prikaže obaveštenje o autorskim pravima kompanije ili osobe koja je vlasnik programa. Sa programima u ovoj koloni i LZV programom od prošlog juna, možete napisati sopstvene programe za kompresiju/dekompresiju.

Hafmanov algoritam kompresije pretpostavlja da se datoteke sa podacima sastoje od obrazaca znakova gde se neki bajtovi javljaju češće od drugih. Ovo važi za tekstualne datoteke na engleskom jeziku. Neka slova koristimo češće od drugih. Analizom datoteke, algoritam gradi niz koji identifikuje učestalost svakog karaktera. Zatim gradi strukturu Hafmanovog drveta iz frekvencijskog niza. Svrha stabla je da poveže svaki znak sa bitnim nizom. Češći karakteri dobijaju kraće bitne nizove; ređe dobijaju duži niz bitova, pa se podaci u datoteci mogu komprimovati.

Da bi komprimovao datoteku, Hafmanov algoritam čita datoteku drugi put, prevodi svaki znak u niz bitova koji mu je dodelilo Hafmanovo stablo i upisuje nizove bitova u komprimovanu datoteku.

Algoritam dekompresije obrće proces. Koristi niz frekvencija koji je kompresioni prolaz kreirao za izgradnju Hafmanovog stabla. Neke aplikacije koriste globalni niz konstantnih frekvencija zasnovan na empirijskom razumevanju baze podataka. Sa ovom tehnikom, program ne mora da upisuje niz u komprimovani fajl. Druge aplikacije dozvoljavaju da svaka datoteka ima jedinstveni niz frekvencija. Prolaz kompresije upisuje jedinstveni niz u komprimovani fajl, a prolaz za dekompresiju ga čita i ponovo gradi stablo pre dekompresije nizova bitova. Ovaj pristup može da generiše veću komprimovanu datoteku od originalne iz kratke datoteke ili iz one sa relativno ravnomernom distribucijom znakova.

### Kompresija

Da biste razumeli kako Hafman komprimuje podatke, morate da posmatrate kako gradi stablo. Razmotrite ovu rečenicu u primeru datoteke: sada je vreme za kompresiju. Da biste napravili stablo, prvo morate da napravite niz frekvencija. Ovaj niz će vam reći učestalost svakog znaka u datoteci. Niz frekvencija za datoteku primera izgleda kao u tabeli 1.

Table 1: The frequency array for our example file.

 Character | Frequency
 --------- | ----------
  ' | 5
  e | 3
  o | 3
  s | 3
  t | 3
  i | 2
  m | 2
  c | 1
  h | 1
  n | 1
  p | 1
  r | 1
  w | 1

Sledeći korak je izgradnja Hafmanovog drveta. Struktura stabla sadrži čvorove, od kojih svaki sadrži karakter, njegovu frekvenciju, pokazivač na roditeljski čvor i pokazivače na levi i desni podređeni čvor. Stablo sadrži unose za svih 256 mogućih karaktera i 255 roditeljskih čvorova. U početku nema roditeljskih čvorova. Drvo raste tako što uzastopno prolazi kroz postojeće čvorove. Svaki prolaz traži dva čvora koji nisu razvili roditeljski čvor i koji imaju dva najniža frekvencija. Kada program pronađe ta dva čvora, on dodeljuje novi čvor, dodeljuje ga kao roditelj za dva čvora i daje novom čvoru broj frekvencija koji je zbir dva podređena čvora. Sledeća iteracija pretrage ignoriše ta dva podređena čvora, ali uključuje novi roditeljski čvor. Prolazi se nastavljaju sve dok ne ostane samo jedan čvor bez roditelja. Taj čvor će biti korenski čvor drveta.

Slika 1 ilustruje kako bi se moglo pojaviti Hafmanovo stablo za datoteku primera. Obratite pažnju na to da samo originalni listovi čvorovi imaju značajne vrednosti znakova. To polje u višim čvorovima je besmisleno. Algoritmi za pretragu drveta razlikuju listove od viših čvorova po izgledu pokazivača na podređene čvorove. Ako čvor ima decu, to nije list. Čvor koji nema roditelja je koren. Algoritam kompresije koristi stablo da prevede znakove u datoteci u nizove bitova. Na slici 1 možete videti da ako počnete od osnovnog čvora i pratite svoj put do najčešćeg karaktera, ima manje čvorova na putanji nego do najmanje učestalog znaka. Stoga je u pitanju dodeljivanje vrednosti bita, 0 ili 1, svakoj desnoj ili levoj grani od roditeljskog čvora do njegovih potomaka. Slika 2 prikazuje primer Hafmanovog stabla sa vrednošću 1 dodeljenom svakoj levoj grani, i 0 dodeljenom svakoj desnoj grani. Ako pratite kroz stablo, možete videti da je niz bitova koji predstavlja znak za razmak, koji je najčešći, 011, dok je string za 'n' 01001. U datoteci sa instancama sa mnogo više znakova, duži nizovi će biti mnogo duži od najdužih u ovom primeru. Samo jedan kod karaktera u Hafmanovom stablu počinje sa 011, na primer, i tako ne postoji mogućnost da nizovi bitova od dva znaka zbune algoritam dekompresije.

Kompresija, dakle, uključuje obilaženje stabla koje počinje od čvora lista da bi se karakter komprimovao i navigacija do korena. Ova navigacija iterativno bira roditelja trenutnog čvora i vidi da li je trenutni čvor desno ili levo dete roditelja, čime se određuje da li je sledeći bit jedinica ili nula. Pošto idete od lista do korena, skupljate bitove obrnutim redosledom u kom ćete ih upisivati u komprimovani fajl.

Dodeljivanje bita 1 levoj grani i bita 0 desnoj grani je proizvoljno. Takođe, stvarno drvo možda neće uvek izgledati baš kao ono na slikama. To zavisi od redosleda kojim se nastavlja pretraga stabla dok gradi roditelje i gde u stablu ubacuje te roditelje. Desna i leva grana iz korenskog čvora mogu se obrnuti bez uticaja na odnos kompresije, na primer. Slike 1 i Slika 2 su reprezentativne. Stablo koje bi bilo izgrađeno kodom u koloni ovog meseca bi se malo razlikovalo u odnosu na dodeljivanje desnog i levog podređenog čvora, ali je teže nacrtati za primere. Stablo na slikama bi komprimovalo "sada je" u ovaj bit tok:

```c
    n    o    w   ''   i   s 01001 101 1000 011 0001 110
```

### Dekompresija

Dekompresija uključuje izgradnju Hafmanovog stabla, čitanje kompresovane datoteke i pretvaranje njenih tokova bitova u znakove. Čitate datoteku pomalo. Počevši od korenskog čvora u Hafmanovom stablu i u zavisnosti od vrednosti bita, uzimate desnu ili levu granu stabla, a zatim se vraćate da čitate drugi bit. Kada je čvor koji izaberete list – to jest, nema desni i levi podređeni čvor – upisujete njegovu vrednost karaktera u dekomprimovanu datoteku i vraćate se na osnovni čvor za sledeći bit.

Većina definicija Hafmanovog drveća tretira vrednosti frekvencije kao decimalne, a ne kao cele brojeve. Znak visoke frekvencije može imati vrednost čvora od .55, dok vrednost niske frekvencije ima vrednost od .00002. Vrednost u korenskom čvoru bi, prema tome, bila 1,0, zbir svih decimalnih delova. Moja implementacija koristi stvarne brojeve za vrednosti, tako da je vrednost u osnovnom čvoru ukupan broj bajtova u datoteci. Ovaj pristup izbegava upotrebu aritmetike sa pomičnim zarezom.

### Programi Hafman

[LISTING_ONE](#listing_one) je "htree.h". BYTECOUNTER typedef definiše tip podataka za brojanje frekvencija. Koristio sam unsigned int, što znači da program može komprimovati datoteke do 64K bajtova. Možete ga promeniti u dug ceo broj da biste komprimovali veće datoteke. Struktura htree definiše Hafmanovo stablo, a prototip buildtree je za funkciju koju programi za kompresiju i dekompresiju koriste za pravljenje Hafmanovog stabla iz niza frekvencija znakova.

[LISTING_TWO](#listing_two) je "htree.c", koji sadrži funkciju buildtree. Pretpostavlja se da je prvih 256 unosa u globalnom stablu inicijalizovano sa vrednostima lista stabla. On skenira stablo i inicijalizuje lokalne pokazivače na dva čvora za koja pronađe da imaju najmanji broj frekvencija. Zaobilazi svaki čvor koji već ima roditelj ili koji ima nultu vrednost u svom broju frekvencija. Nakon svakog skeniranja gde se pronađu dva čvora, funkcija dodaje čvor stablu, postavljajući adresu novog čvora u roditeljski član oba čvora koja su pronađena skeniranjem. Stavlja zbir broja dva podređena frekvencija u broj frekvencija novog čvora, a adrese dva podređena čvora stavlja u desni i levi pokazivač podređenih čvorova novog čvora. Kada skeniranje ne uspe da pronađe dva čvora koji nemaju roditelje i koji imaju broj frekvencija različit od nule, stablo je kompletno, a preostali čvor bez roditelja je osnovni čvor.

[LISTING_THREE](#listing_three) je "huffc.c", program za kompresiju datoteka. Nakon otvaranja ulaznih i izlaznih datoteka, program čita ulaznu datoteku, gradi frekvencijski niz i broji bajtove. Takođe broji broj različitih vrednosti znakova pronađenih u datoteci. Kada program pročita celu datoteku, u izlaznu datoteku upisuje broj bajtova i broj različitih vrednosti znakova. Zatim upisuje niz frekvencija, koji se sastoji od svake vrednosti znakova koja se pojavila u ulaznoj datoteci i koliko puta se to dogodilo.

Postoje i drugi načini na koje je program mogao da snimi frekvencijski niz. Mogao je da napiše 256 vrednosti, pri čemu je pozicija svake vrednosti u nizu karakter koji je brojao. Niz bi zabeležio sve nule kao i značajne brojeve. Ovo bi moglo da koristi manje prostora u kompresovanoj datoteci od izabranog metoda, koji upisuje broj značajnih znakova nakon čega sledi vrednost svake znaka i njen broj. Unosi za vrednosti znakova koji se ne pojavljuju u ulaznoj datoteci nisu u kompresovanoj datoteci. Datoteka koja ima pojavljivanja većine od 256 znakova verovatno bi snimila manji niz korišćenjem prethodne metode. Datoteka sa manje različitih vrednosti znakova – kao što je 7-bitna ASCII tekstualna datoteka – bi imala manji niz korišćenjem izabranog metoda. Zaista pametan program za kompresiju bi odlučio koji je oblik manji i napisao kraći oblik sa kontrolnom vrednošću koja ga identifikuje.

Nakon pisanja niza frekvencija, program za kompresiju poziva funkciju buildtree da bi napravio Hafmanovo stablo. Zatim premotava ulaznu datoteku, čita svaki znak i poziva funkciju kompresije. Rekurzivna funkcija kompresije šalje komprimovani tok bitova za karakter u izlaznu datoteku. Počinje od pozicije lisnog čvora karaktera u stablu i poziva sebe adresom roditelja čvora dok ne dođe do korenskog čvora. Kada se vrati iz poziva u sebe, upisuje nula ili jedan bit u komprimovanu datoteku, u zavisnosti od toga da li je pozvana sa desnog ili levog podređenog čvora.

Program poziva funkciju izlaza za pisanje svakog bita kompresovanih podataka. Outbit funkcija rotira bitove u bajt sve dok se ne doda 8 bitova, nakon čega upisuje bajt u datoteku. Program poslednji put poziva outbit sa parametrom -1 da bi mu rekao da zapiše poslednju vrednost bajta u datoteku.

[LISTING_FOUR](#listing_four) je "huffd.c", program za dekompresiju datoteka. Otvara ulazne i izlazne datoteke, a zatim čita broj bajtova i frekvencija. Svrha broja bajtova je da program za dekompresiju zna kada je dekompresovanje završeno. Poslednji bajt u kompresovanoj datoteci obično sadrži manje bitova nego što je potrebno da se završi dekompresovana datoteka, tako da broj bajtova kontroliše broj upisanih bajtova.

Vrednost brojanja frekvencija govori programu koliko unosa treba da pročita u niz frekvencija. Svaki unos se sastoji od vrednosti karaktera i broja frekvencija. Ovaj niz, kada se jednom učita, postaje Hafmanovo stablo kada program pozove funkciju buildtree. Zatim se program dekompresuje pozivanjem funkcije za dekompresiju da bi svaki bajt mogao da upiše u izlaznu datoteku.

Funkcija dekompress poziva funkciju inbita da pročita svaku sledeću vrednost bita u kompresovanoj datoteci. Inbit funkcija čita bajt iz kompresovane datoteke i pomera bitove iz nje sve dok ne vrati svih 8 bitova, nakon čega čita sledeći bajt. Funkcija dekompresije koristi bitove da hoda niz Hafmanovo stablo počevši od korenskog čvora. Sve dok trenutni čvor ima podređene čvorove – unose u desni i levi pokazivači – funkcija dobija još jedan bit i pomera se na levi podređeni čvor ako je bit 1 i desni podređeni čvor ako je bit 0. Kada trenutni čvor nema podređeni čvor, to je list, a funkcija za dekompresiju vraća vrednost svog karaktera.

### Kompresija kao šifrovanje

Možete koristiti Huffman kompresiju za šifrovanje datoteka sa podacima. Da biste dešifrovali datoteku, morate znati algoritam koji ju je šifrovao i vrednost ključa za šifrovanje. Datoteka podataka koju komprimujete pomoću Hafmanovog drveta je prilično dobro šifrovana ako držite niz frekvencija privatnim. Niz postaje ključ za dešifrovanje datoteke. Datoteka je dodatno zaštićena ako je sam algoritam deo tajne. Razbijač šifre bi prvo morao da shvati da ste koristili Hafmanovo stablo da kompresujete datoteku, a zatim, nakon što je doneo tu odluku, špijun bi morao da dešifruje niz frekvencija da bi ga dekompresovao/dešifrovao.

#### LISTING_ONE

```c
/* ------------------- htree.h -------------------- */
typedef unsigned int BYTECOUNTER;

/* ---- Huffman tree structure ---- */
struct htree    {
    unsigned char ch;       /* character value             */
    BYTECOUNTER cnt;        /* character frequency         */
    struct htree *parent;   /* pointer to parent node      */
    struct htree *right;    /* pointer to right child node */
    struct htree *left;     /* pointer to left child node  */
};
extern struct htree ht[];
extern struct htree *root;

void buildtree(void);
```

#### LISTING_TWO

```c
/* ------------------- htree.c -------------------- */
#include <stdio.h>
#include <stdlib.h>
#include "htree.h"

struct htree ht[512];
struct htree *root;

/* ------ build a Huffman tree from a frequency array ------ */
void buildtree(void)
{
    int treect = 256;
    int i;

    /* ---- build the huffman tree ----- */
    while (1)   {
        struct htree *h1 = NULL, *h2 = NULL;
        /* ---- find the two smallest frequencies ---- */
        for (i = 0; i < treect; i++)   {
            if (ht+i != h1) {
                if (ht[i].cnt > 0 && ht[i].parent == NULL)   {
                    if (h1 == NULL || ht[i].cnt < h1->cnt) {
                        if (h2 == NULL || h1->cnt < h2->cnt)
                            h2 = h1;
                        h1 = ht+i;
                    }
                    else if (h2 == NULL || ht[i].cnt < h2->cnt)
                        h2 = ht+i;
                }
            }
        }
        if (h2 == NULL) {
            root = h1;
            break;
        }
        /* --- combine two nodes and add one --- */
        h1->parent = ht+treect;
        h2->parent = ht+treect;
        ht[treect].cnt = h1->cnt + h2->cnt;
        ht[treect].right = h1;
        ht[treect].left = h2;
        treect++;
    }
}
```

#### LISTING_THREE

```c
/* ------------------- huffc.c -------------------- */
#include <stdio.h>
#include <stdlib.h>
#include "htree.h"

static void compress(FILE *fo, struct htree *h,struct htree *child);
static void outbit(FILE *fo, int bit);

void main(int argc, char *argv[])
{
    FILE *fi, *fo;
    int c;
    BYTECOUNTER bytectr = 0;
    int freqctr = 0;
    if (argc < 3)   {
        printf("\nusage: huffc infile outfile");
        exit(1);
    }

    if ((fi = fopen(argv[1], "rb")) == NULL)    {
        printf("\nCannot open %s", argv[1]);
        exit(1);
    }
    if ((fo = fopen(argv[2], "wb")) == NULL)    {
        printf("\nCannot open %s", argv[2]);
        fclose(fi);
        exit(1);
    }

    /* - read the input file and count character frequency - */
    while ((c = fgetc(fi)) != EOF)   {
        c &= 255;
        if (ht[c].cnt == 0)   {
            freqctr++;
            ht[c].ch = c;
        }
        ht[c].cnt++;
        bytectr++;
    }

    /* --- write the byte count to the output file --- */
    fwrite(&bytectr, sizeof bytectr, 1, fo);

    /* --- write the frequency count to the output file --- */
    fwrite(&freqctr, sizeof freqctr, 1, fo);

    /* -- write the frequency array to the output file -- */
    for (c = 0; c < 256; c++)   {
        if (ht[c].cnt > 0)    {
            fwrite(&ht[c].ch, sizeof(char), 1, fo);
            fwrite(&ht[c].cnt, sizeof(BYTECOUNTER), 1, fo);
        }
    }

    /* ---- build the huffman tree ---- */
    buildtree();

    /* ------ compress the file ------ */
    fseek(fi, 0L, 0);
    while ((c = fgetc(fi)) != EOF)
        compress(fo, ht + (c & 255), NULL);
    outbit(fo, -1);
    fclose(fi);
    fclose(fo);
}

/* ---- compress a character value into a bit stream ---- */
static void compress(FILE *fo, struct htree *h,
                                struct htree *child)
{
    if (h->parent != NULL)
        compress(fo, h->parent, h);
    if (child)  {
        if (child == h->right)
            outbit(fo, 0);
        else if (child == h->left)
            outbit(fo, 1);
    }
}
static char out8;
static int ct8;

/* -- collect and write bits to the compressed output file -- */
static void outbit(FILE *fo, int bit)
{
    if (ct8 == 8 || bit == -1)  {
        fputc(out8, fo);
        ct8 = 0;
    }
    out8 = (out8 << 1) | bit;
    ct8++;
}
```

#### LISTING_FOUR

```c
/* ------------------- huffd.c -------------------- */
#include <stdio.h>
#include <stdlib.h>
#include "htree.h"

static int decompress(FILE *fi, struct htree *root);

void main(int argc, char *argv[])
{
    FILE *fi, *fo;
    unsigned char c;
    BYTECOUNTER bytectr;
    int freqctr;
    if (argc < 3)   {
        printf("\nusage: huffd infile outfile");
        exit(1);
    }
    if ((fi = fopen(argv[1], "rb")) == NULL)    {
        printf("\nCannot open %s", argv[1]);
        exit(1);
    }
    if ((fo = fopen(argv[2], "wb")) == NULL)    {
        printf("\nCannot open %s", argv[2]);
        fclose(fi);
        exit(1);
    }

    /* ----- read the byte count ------ */
    fread(&bytectr, sizeof bytectr, 1, fi);

    /* ----- read the frequency count ------ */
    fread(&freqctr, sizeof freqctr, 1, fi);

    while (freqctr--)   {
        fread(&c, sizeof(char), 1, fi);
        ht[c].ch = c;
        fread(&ht[c].cnt, sizeof(BYTECOUNTER), 1, fi);
    }

    /* ---- build the huffman tree ----- */
    buildtree();

    /* ----- decompress the file ------ */
    while (bytectr--)
        fputc(decompress(fi, root), fo);
    fclose(fo);
    fclose(fi);
}
static int in8;
static int ct8 = 8;

/* ---- read a bit at a time from a file ---- */
static int inbit(FILE *fi)
{
    int obit;
    if (ct8 == 8)   {
        in8 = fgetc(fi);
        ct8 = 0;
    }
    obit = in8 & 0x80;
    in8 <<= 1;
    ct8++;
    return obit;
}

/* ----- decompress file bits into characters ------ */
static int decompress(FILE *fi, struct htree *h)
{
    while (h->right != NULL)
        if (inbit(fi))
            h = h->left;
        else
            h = h->right;
    return h->ch;
}
```

Copyright © 1991, Dr. Dobb's Journal
