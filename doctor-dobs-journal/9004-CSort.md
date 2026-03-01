
# C PROGRAMMING - 9004

## CSORT - Al Stivens

Oni od vas sa iskustvom u glavnom računaru znaju da je uobičajen i nezamenljiv uslužni program u tom okruženju program za sortiranje datoteka. Mnoge paketne aplikacije uključuju generisanje izveštaja preuzetih iz datoteka sa podacima koje održavate u nizovima drugačijim od redosleda koji je potreban za izveštaj koji je pri ruci. Sećam se dvosmernih i trosmernih vrsta traka na IBM-u 1401 iz daleke 1961. Bilo bi nezamislivo pokušati da dizajniramo sistem bez jedne.

### Razvrstavanje trake glavnog računara

Evo kako takva vrsta radi u tipičnoj konfiguraciji trake iz prošlosti. Potrebna su vam najmanje četiri traka za pokretanje dvosmernog sortiranja. Nerazvrstana datoteka počinje na prvom drajvu trake, a ostala tri imaju kolutove trake za grebanje. Datoteka parametara (obično na kontrolnoj kartici) govori programu za sortiranje veličinu zapisa datoteke i lokaciju, dužinu i redosled (uzlazno ili opadajuće) svakog od polja koje treba sortirati.

Pass One, ulazni prolaz -- Program za sortiranje čita onoliko zapisa u memoriju koliko CPU može da zadrži i sortira ih u niz. Zatim upisuje sortirani blok zapisa u treći pogon trake. Zatim čita i sortira drugi blok zapisa i upisuje taj blok na četvrti disk, zatim sledeći blok u treći, i tako dalje dok se svi ulazni zapisi ne pročitaju. Diskovi 3 i 4 sada sadrže više blokova pojedinačno sortiranih zapisa, i ovo je kraj prvog prolaza.

Tokom prvog prolaza, veličina bloka je ograničena na količinu memorije koja je dostupna. Veličine blokova se udvostručuju tokom narednih prolaza jer je svaki blok u nizu, a program ih čita zapis po zapis da bi izvršio spajanje.

Pass 2, prolaz spajanja nadole -- Operater računara zamenjuje ulaznu traku na drajvu 1 četvrtom trakom za grebanje, a program za sortiranje spaja prvi blok sa diska 3 sa prvim blokom sa diska 4, upisujući spojeni blok na drajv 1. Zatim bi spojio sledeće blokove sa drajva 3 i 4 na drajv 2. i nastavlja se od flip-flo diska do 4. spojeni u diskove 1 i 2, koji sada zajedno sadrže upola manje blokova od 3 i 4. (Oldtajmeri među mojim čitaocima počinju da se dosađuju.)

Treći prolaz, izlazni prolaz – spajanja se nastavljaju napred-nazad sa svakim prolazom smanjujući broj naručenih blokova za polovinu i udvostručujući veličinu svakog bloka sve dok na konačnom prolazu ne bude samo jedan blok na bilo drajvu 1 ili 3, a ova traka sadrži sortiranu datoteku, koju aplikacija može pročitati.

### Pokvareni podaci o Microsu

Kada sam sredinom sedamdesetih počeo da radim sa mikroračunarima, tražio sam standardni uslužni program za sortiranje za koji sam mislio da će doći sa operativnim sistemima, i nisam ga našao. Prvoj značajnoj aplikaciji koju sam napisao za IMSAI 8080 bilo je potrebno sortiranje datoteka, a pretraga je pokazala CP/M program pod nazivom „KSORT“, ali sam bio iznenađen kada sam saznao da je bilo teško pronaći ono što je oduvek bio nezamenljiv uslužni program.

Sa PC-om i MS-DOS-om došao je i SORT filterski program, ali on ima tri ozbiljna ograničenja: može sortirati samo onoliko zapisa koliko stane u 64K, može sortirati samo jedno polje zapisa datoteke i, budući da je filter, radi samo sa tekstualnim datotekama.

### In-line sortiranje

Toliko o uslužnom programu za sortiranje datoteka. Hajde sada da razmotrimo funkciju sortiranja u liniji. Svako ko je programirao na mnogim kompjuterskim jezicima, akumulirao je neke omiljene karakteristike o svakom od njih. Idealan lični jezik bi imao sve te karakteristike upakovane u jednu. Naravno, kada pokušate da izgradite jezik sa svima omiljenim funkcijama, dobijate jezik koji je dizajnirao odbor - možda ćete dobiti ADA. Savršen lični jezik bi bio toliko proširiv da bi svako od nas mogao da dizajnira sopstvenu sintaksu i tipove podataka, dodajući karakteristike koje cenimo i izostavljajući one koje nemamo. Mana u tom pristupu je u tome što niko nije mogao da čita tuđi kodeks, i tako, umesto da svako od nas izgradi lični idealni jezik, mi se približavamo pristupu komiteta, pojavljuju se standardi i težimo da ih uskladimo.

Kao programer i pisac C sa punim radnim vremenom, često razmišljam o jezičkim karakteristikama u smislu karakteristika koje su mi se dopale u prošlim jezicima koje C nema. Na primer, funkcije stringova Basic-a nedostaju u C. Cobol-ov MOVE CORRESPONDING bi bio zgodan u C-u gde bi dodeljivanje strukture C dodelilo samo one članove koji imaju ista imena. Možete dodati obe ove funkcije u C tako što ćete izgraditi odgovarajuće C++ klase ako koristite C++ ekstenzije za C, i tako se sumnjiva oblast prilagođenog jezika ponovo nazire u bliskoj budućnosti.

Postoji jedna karakteristika koja mi se dopala kod Cobol-a koju možete ugraditi u tradicionalni C bez proširenja jezika osim sa nekoliko funkcija. Ta karakteristika je glagol SORT i relevantna je za ovomesečno lutanje.

Cobol program može da prosleđuje zapise SORT glagolu jedan po jedan, a zatim kasnije preuzima te zapise u sortiranom nizu. Postoji nekoliko prednosti ove tehnike. Jedan je taj što program ne mora da pripremi nesređenu datoteku, izađe u pomoćni program za sortiranje, a zatim da izvrši treći program za obradu sortirane datoteke. Možete formirati svaki nesortirani zapis iz jednog ili više izvora neposredno pre nego što ga pošaljete na sortiranje i možete transformisati sortirane zapise u drugi format pre nego što bilo šta uradite sa njima. Nije uključena nikakva posredna datoteka jedinstveno formatiranih zapisa.

Funkcija C ksort je slična ovom pristupu osim što sortira niz u memoriji, tako da svi vaši zapisi podataka moraju stati u memoriju. Pravo in-line sortiranje će prihvatiti onoliko zapisa, jedan po jedan, koliko želite da mu date, i vraćaće vam te zapise, jedan po jedan, u sortiranom redosledu.

Dakle, sada smo definisali dva zahteva koja nedostaju u jeziku C i okruženju operativnog sistema mikroprocesora. Zašto, nakon deset solidnih godina mikro upotrebe, ove karakteristike nisu standardne? Očigledno, oni nisu široko potrebni iz nekog razloga, a taj razlog je, pretpostavljam, taj što je lična, jeftina priroda računara redefinisala način na koji dizajniramo sisteme. Većina današnjih PC programa je interaktivna. Ako uključuju više sekvenci datoteka sa podacima, koriste indeksirane menadžere baza podataka ili DBMS jezike višeg nivoa koji se bave sortiranjem na svoje načine. OK, tako da nam većinu vremena nije potreban program za sortiranje ili in-line sortiranje. Ali šta je sa nekoliko puta kada to uradimo? Ništa drugo neće učiniti.

Pre otprilike pet godina, rešio sam ovaj problem tako što sam napisao funkciju sortiranja na liniji u jeziku C za neke programe za prodavnicu video traka. Kasnije sam ga koristio kao mehanizam za sortiranje za samostalni program za sortiranje koji sortira datoteke na disku. Koristio sam pre-ANSI Aztec C za IBM-PC i objavio kod u knjizi (C razvojni alati za IBM PC, Bradi Books) 1986. Od tada sam ponovo koristio taj program mnogo puta u okolnostima u kojima bih možda izabrao pristupačnije, ali manje odgovarajuće rešenje da već nisam imao ovaj mali program za sortiranje. Sada kada je ANSI standardna definicija C-a na mestu, vreme je da ažuriramo taj program, pa ga ovog meseca promovišemo u standard i objavljujemo ovde.

### CSORT

Ono što sledi je CSORT, sredstvo za sortiranje koje možete da koristite u okviru svojih programa ili iz komandne linije. Iako sam ga razvio za upotrebu u CP/M sistemima i kasnije u MS-DOS-u, on nije povezan ni sa jednim od tih okruženja i trebalo bi lako da se prebaci na drugu platformu gde je dostupan standardni C kompajler.

CSORT projekat ima dva dela, in-line sortiranje i program za sortiranje datoteka. Da biste koristili in-line sortiranje u C programu, opisujete karakteristike zapisa koje treba sortirati i šaljete ih, jedan po jedan, u proces sortiranja. Nakon što pošaljete poslednji zapis, šaljete NULL i idete svojim poslom. Kada ste spremni za zapise u sortiranom nizu, pozivate program za sortiranje i on vraća zapise, po jedan za svaki poziv. Nakon što vrati poslednji od sortiranih zapisa, vraća NULL. Vaš program su u suštini polarni krajevi trofazne cevi.

Vi i CSORT prenosite zapise napred-nazad sa pokazivačima. CSORT pravi kopiju zapisa koji mu prosledite, tako da možete bezbedno da ponovo koristite prostor koji je zapis zauzimao. CSORT očekuje da uradite isto jer bi mogao ponovo da koristi prostor zapisa na koji je ukazao da je prethodni pokazivač koji je vratio.

CSORT zahteva da sortirate zapise fiksne dužine, ali sami zapisi ne moraju da budu u tekstualnom formatu. Poređenje sortiranja koristi funkciju strnicmp za upoređivanje polja, tako da ne moraju da budu završena nulom.

### CSORT API

Sledi interfejs aplikacijskog programa za CSORT. [LISTING_ONE](#listing_one) je "CSORT.H", koji sadrži nekoliko kontrolnih vrednosti za sortiranje. Globalna vrednost NOFLDS specificira maksimalan broj polja koja mogu biti uključena u sortiranje zapisa. Vrednosti MOSTMEM i LEASTMEM kontrolišu prisvajanje bafera iz gomile za sortiranje. MOSTMEM je iznos za koji pokušavate, a LEASTMEM je najmanji iznos koji ćete prihvatiti. Postavio sam ih da rade u okviru programa malog modela na računaru.

Evo prototipova i opisa CSORT API funkcija.

- **int init_sort(struct s_prm *prms);**
Da biste sortirali zapise, morate ih opisati. Struktura s_prm sadrži definiciju zapisa koje treba sortirati. Definisan je u CSORT.H. Strukturu ćete deklarisati i inicijalizovati na sledeći način. Dodelite dužinu zapisa članu rc_len. Niz podstruktura s_fld će sadržati unose za svako polje koje treba sortirati. Član s_fld.f_pos sadrži poziciju zapisa za polje. Ova vrednost je relativna za jedan. Ako ima manje od NOFLDS polja, unos terminala u ovom nizu će sadržati nultu vrednost za ovog člana. Član s_fld.f_len je dužina polja. Član s_fld.ad je znak "a" ako polje treba da se poredi u rastućem nizu i "d" ako treba da se poredi u opadajućem nizu. Prvo polje u nizu je glavno polje sortiranja; poslednji je maloletnik; svi ostali su srednji. Dakle, ako su vam potrebni zapisi u, na primer, broju odeljenja, broju odeljenja i redosledu brojeva zaposlenih, u nizu ćete definisati polja tim redosledom. Kada je niz pravilno inicijalizovan, pozovite funkciju init_sort. Ako sortiranje može da se nastavi – ako ima dovoljno memorije – funkcija vraća nulu. U suprotnom vraća -1.

- **void sort(char *rcd);**
Sa programom za sortiranje pravilno inicijalizovanim putem init_sort, možete slati zapise da se sortiraju pozivanjem funkcije sortiranja jednom za svaki zapis. Prosledite pokazivač na zapis i funkcija sortiranja će napraviti kopiju zapisa. Nakon što ste prošli poslednji zapis, prosledite NULL pokazivač. Ovo govori funkciji sortiranja da završi sortiranje i pripremi se za vraćanje sortiranih zapisa.

- **char *sort_op(void);**
Da biste preuzeli sortirane zapise, pozovite funkciju sort_op. Svaki poziv na njega će vratiti pokazivač na sledeći zapis u sortiranom nizu. Nakon što se poslednji zapis vrati, sort_op vraća NULL pokazivač.

- **void sort_stats(void);**
Ova funkcija prikazuje neke od vrednosti koje se koriste u funkciji sortiranja.

### Program FILESORT

[LISTING_TWO](#listing_two) je "FILESORT.C", program koji koristi CSORT API za implementaciju samostalnog programa za sortiranje datoteka. Pokrećete ga tako što ćete u komandnu liniju uneti ime datoteke, dužinu zapisa i parametre polja. Koristi API na upravo opisan način.

### CSORT Internals

[LISTING_THREE](#listing_three) je "CSORT.C", koji sadrži funkcije sortiranja u liniji. Prva funkcija o kojoj se raspravlja je init_sort, koju pozivate da biste započeli sortiranje. Poziva appr_mem da dodeli blok memorije u kojoj će se sortirati, inicijalizuje parametre sortiranja i vraća.

Funkcija sortiranja prihvata zapise za sortiranje iz aplikacije koja poziva. U početku ih jednostavno kopira u bafer, jednu za drugom. Kada je bafer pun, funkcija sortiranja poziva standardnu funkciju ksort da sortira zapise u baferu. Ovo postaje blok sortiranih zapisa, koji se ovde naziva i „sekvenca“. Dumpbuff funkcija ih upisuje u radnu datoteku sortiranja. CSORT koristi samo jednu radnu datoteku za sortiranje, za razliku od sortiranja traka o kojima smo ranije govorili. Pošto ovaj program koristi datoteke diska i pošto je direktno adresiranje zapisa moguće, ne moramo da održavamo više, serijskih uređaja za blokove sortiranih zapisa na način na koji se koristi strimer traka.

Kada aplikacija koja poziva šalje NULL pokazivač funkciji sortiranja, ona sortira konačnu sekvencu, upisuje je u radnu datoteku i poziva prep_merge da bi se pripremila za to kada pozivalac želi sortirane zapise. Objedinjavanje deli radni bafer za sortiranje na mnogo minijaturnih bafera, po jedan za svaku sekvencu koju je generisala funkcija sortiranja. Ovi baferi sadrže dva ili, obično, više zapisa. Ako je i dok je broj sekvenci dovoljno visok da spreči da se bafer segmentira na ovaj način, prep_merge koristi funkciju spajanja da spoji grupe od dve sekvence u jednu, prepolovivši broj sekvenci i udvostručivši njihovu veličinu.

Kada je broj sekvenci dovoljno mali da svaka može da doprinese najmanje dva zapisa u baferu za sortiranje, funkcije stapanja i prep_merge su gotove.

Aplikacija koja poziva poziva sort_op da bi dobila sortirane zapise. Funkcija sort_op gleda prvi zapis u svakom segmentu bafera da bi pronašla najniži zapis. Pomera pokazivač zapisa tog segmenta i vraća pokazivač na zapis koji je pronašao. Kada iscrpi segment bafera, čita više zapisa iz pridruženog niza na radnoj datoteci sortiranja. Kada su svi segmenti prazni, aplikacija je pročitala poslednji sortirani zapis, a program za sortiranje signalizira da je to urađeno vraćanjem NULL pokazivača.

#### LISTING_ONE

```c
/* ---------------------- csort.h --------------------------- */

#define NOFLDS 5        /* maximum number of fields to sort   */
#define MOSTMEM 50000U  /* most memory for sort buffer        */
#define LEASTMEM 10240  /* least memory for sort buffer       */

struct s_prm {              /* sort parameters                */
    int rc_len;             /* record length                  */
    struct  {
        int f_pos;          /* 1st position of field (rel 1)  */
        int f_len;          /* length of field                */
        char ad;            /* a = ascending; d = descending  */
    } s_fld [NOFLDS];       /* one per field                  */
};

struct bp   {        /* one for each sequence in merge buffer */
    char *rc;        /* -> record in merge buffer             */
    int rbuf;        /* records left in buffer this sequence  */
    int rdsk;        /* records left on disk this sequence    */
};

int init_sort(struct s_prm *prms);  /* Initialize the sort    */
void sort(char *rcd);               /* Pass records to Sort   */
char *sort_op(void);                /* Retrieve sorted records*/
void sort_stats(void);              /* Display sort statistics*/
```

#### LISTING_TWO

```c
/* --------------------- filesort.c ------------------------ */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "csort.h"

/* sort a file:
 *     command line: filesort filename length {f1, l1, az1, ...}
 *     filename is the name of the input file
 *         length is the length of a record
 *         for each field:
 *             f1 is the field position relative to 1
 *             l1 is the field lengths
 *             az1 = A = ascending, D = descending
 */

static struct s_prm sp;
static void usage(void);

void main(int argc, char *argv[])
{
    int i, fct = 0;
    FILE *fpin, *fpout;
    char *buf;
    char filename[64];

    /* ------- get the file name from the command line ------ */
    if (argc-- > 1)
        strcpy(filename, argv++[1]);
    else
        usage();
    /* ----- get the record length from the command line ---- */
    if (argc-- > 1)
        sp.rc_len = atoi(argv++[1]);
    else
        usage();
    /* ----- get field definitions from the command line ---- */
    do  {
        if (argc < 4)
            usage();
        sp.s_fld[fct].f_pos = atoi(argv++[1]);
        sp.s_fld[fct].f_len = atoi(argv++[1]);
        sp.s_fld[fct].ad = *argv++[1];
        argc -= 3;
        fct++;
    } while (argc > 1);

    printf("\nFile: %s, length", filename, sp.rc_len);
    for (i = 0; i < fct; i++)
        printf("\nField %d: position %d, length %d, %s",
            i+1,
            sp.s_fld[i].f_pos,
            sp.s_fld[i].f_len,
            sp.s_fld[i].ad == 'd' ?
                            "descending" : "ascending");

    if ((fpin = fopen(filename, "rb")) == NULL) {
        printf("\nInput file not found");
        exit(1);
    }
    if ((buf = malloc(sp.rc_len)) == NULL ||
                init_sort(&sp) == -1)   {
        printf("\nInsufficient memory to sort");
        exit(1);
    }
    /* ------ sort the input records ------- */
    while (fread(buf, sp.rc_len, 1, fpin) == 1)
        sort(buf);
    sort(NULL);
    fclose(fpin);
    /* ----- retrieve the sorted output records ------ */
    fpout = fopen("SORTED.DAT", "wb");
    while ((buf = sort_op()) != NULL)
        fwrite(buf, sp.rc_len, 1, fpout);
    fclose(fpout);
    sort_stats();
    free(buf);
}

static void usage(void)
{
    printf("\nusage: filesort fname len {pos length ad...}");
    exit(1);
}
```

#### LISTING_THREE

```c
/* ----------------------- csort.c ------------------------- */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "csort.h"

static struct s_prm *sp;  /* structure of sort parameters    */
static unsigned totrcd;   /* total records sorted            */
static int no_seq;        /* counts sequences                */
static int no_seq1;
static unsigned bspace;   /* available buffer space          */
static int nrcds;         /* # of records in sort buffer     */
static int nrcds1;
static char *bf, *bf1;    /* points to sort buffer           */
static int inbf;          /* variable records in sort buffer */
static char **sptr;       /* -> array of buffer pointers     */
static char *init_sptr;   /* pointer to appropriated buffer  */
static int rcds_seq;      /* rcds / sequence in merge buffer */
static FILE *fp1, *fp2;   /* sort work file fds              */
static char fdname[15];   /* sort work name                  */
static char f2name[15];   /* sort work name                  */

static int comp(char **a, char **b);
static char *appr_mem(unsigned *h);
static FILE *wopen(char *name, int n);
static void dumpbuff(void);
static void merge(void);
static void prep_merge(void);

/* -------- initialize sort global variables----------  */
int init_sort(struct s_prm *prms)
{
    sp = prms;
    if ((bf = appr_mem(&bspace)) != NULL)   {
        nrcds1 = nrcds = bspace / (sp->rc_len + sizeof(char *));
        init_sptr = bf;
        sptr = (char **) bf;
        bf += nrcds * sizeof(char *);
        fp1 = fp2 = NULL;
        totrcd = no_seq = inbf = 0;
        return 0;
    }
    else
        return -1;
}

/* --------- Function to accept records to sort ------------ */
void sort(char *s_rcd)
{
    if (inbf == nrcds)  {   /* if the sort buffer is full */
        qsort(init_sptr, inbf,
            sizeof (char *), comp);
        if (s_rcd)  {   /* if there are more records to sort  */
            dumpbuff(); /* dump the buffer to a sort work file*/
            no_seq++;   /* count the sorted sequences         */
        }
    }
    if (s_rcd != NULL)  {
        /* --- this is a record to sort --- */
        totrcd++;
        /* --- put the rcd addr in the pointer array --- */
        *sptr = bf + inbf * sp->rc_len;
        inbf++;
        /* --- move the rcd to the buffer --- */
        memmove(*sptr, s_rcd, sp->rc_len);
        sptr++;                 /* point to next array entry */
    }
    else    {               /* null pointer means no more rcds */
        if (inbf)   {       /* any records in the buffer?      */
            qsort(init_sptr, inbf,
                sizeof (char *), comp);
            if (no_seq)      /* if this isn't the only sequence*/
                dumpbuff();  /* dump the buffer to a work file */
            no_seq++;        /* count the sequence             */
        }
        no_seq1 = no_seq;
        if (no_seq > 1)    /* if there is more than 1 sequence */
            prep_merge();  /* prepare for the merge            */
    }
}

/* -------------- Prepare for the merge ----------------- */
static void prep_merge()
{
    int i;
    struct bp *rr;
    unsigned n_bfsz;

    memset(init_sptr, '\0', bspace);
    /* -------- merge buffer size ------ */
    n_bfsz = bspace - no_seq * sizeof(struct bp);
    /* ------ # rcds/seq in merge buffer ------- */
    rcds_seq = n_bfsz / no_seq / sp->rc_len;
    if (rcds_seq < 2)   {
        /* ---- more sequence blocks than will fit in buffer,
                merge down ---- */
        fp2 = wopen(f2name, 2);     /* open a sort work file */
        while (rcds_seq < 2)    {
            FILE *hd;
            merge();                /* binary merge */
            hd = fp1;               /* swap fds */
            fp1 = fp2;
            fp2 = hd;
            nrcds *= 2;
            /* ------ adjust number of sequences ------ */
            no_seq = (no_seq + 1) / 2;
            n_bfsz = bspace - no_seq * sizeof(struct bp);
            rcds_seq = n_bfsz / no_seq / sp->rc_len;
        }
    }
    bf1 = init_sptr;
    rr = (struct bp *) init_sptr;
    bf1 += no_seq * sizeof(struct bp);
    bf = bf1;

    /* fill the merge buffer with records from all sequences */

    for (i = 0; i < no_seq; i++)    {
        fseek(fp1, (long) i * ((long) nrcds * sp->rc_len),
                        SEEK_SET);
        /* ------ read them all at once ------ */
        fread(bf1, rcds_seq * sp->rc_len, 1, fp1);
        rr->rc = bf1;
        /* --- the last seq has fewer rcds than the rest --- */
        if (i == no_seq-1)  {
            if (totrcd % nrcds > rcds_seq)      {
                rr->rbuf = rcds_seq;
                rr->rdsk = (totrcd % nrcds) - rcds_seq;
            }
            else        {
                rr->rbuf = totrcd % nrcds;
                rr->rdsk = 0;
            }
        }
        else        {
            rr->rbuf = rcds_seq;
            rr->rdsk = nrcds - rcds_seq;
        }
        rr++;
        bf1 += rcds_seq * sp->rc_len;
    }
}

/* ------- Merge the work file down
    This is a binary merge of records from sequences
    in fp1 into fp2. ------ */
static void merge()
{
    int i;
    int needy, needx;   /* true = need a rcd from (x/y)   */
    int xcnt, ycnt;     /* # rcds left each sequence      */
    int x, y;           /* sequence counters              */
    long adx, ady;      /* sequence record disk addresses */

    /* --- the two sets of sequences are x and y ----- */
    fseek(fp2, 0L, SEEK_SET);
    for (i = 0; i < no_seq; i += 2)     {
        x = y = i;
        y++;
        ycnt =
            y == no_seq ? 0 : y == no_seq - 1 ?
            totrcd % nrcds : nrcds;
        xcnt = y == no_seq ? totrcd % nrcds : nrcds;
        adx = (long) x * (long) nrcds * sp->rc_len;
        ady = adx + (long) nrcds * sp ->rc_len;
        needy = needx = 1;
        while (xcnt || ycnt)    {
            if (needx && xcnt)  {   /* need a rcd from x? */
                fseek(fp1, adx, SEEK_SET);
                adx += (long) sp->rc_len;
                fread(init_sptr, sp->rc_len, 1, fp1);
                needx = 0;
            }
            if (needy && ycnt)  {   /* need a rcd from y? */
                fseek(fp1, ady, SEEK_SET);
                ady += sp->rc_len;
                fread(init_sptr+sp->rc_len, sp->rc_len, 1, fp1);
                needy = 0;
            }
            if (xcnt || ycnt)   {   /* if anything is left */
                /* ---- compare the two sequences --- */
                if (!ycnt || (xcnt &&
                    (comp(&init_sptr, &init_sptr + sp->rc_len))
                        < 0))   {
                    /* ----- record from x is lower ---- */
                    fwrite(init_sptr, sp->rc_len, 1, fp2);
                    --xcnt;
                    needx = 1;
                }
                else if (ycnt)  {  /* record from y is lower */
                    fwrite(init_sptr+sp->rc_len,
                                sp->rc_len, 1, fp2);
                    --ycnt;
                    needy = 1;
                }
            }
        }
    }
}

/* -------- Dump the sort buffer to the work file ---------- */
static void dumpbuff()
{
    int i;

    if (fp1 == NULL)
        fp1 = wopen(fdname, 1);
    sptr = (char **) init_sptr;
    for (i = 0; i < inbf; i++)  {
        fwrite(*(sptr + i), sp->rc_len, 1, fp1);
        *(sptr + i) = 0;
    }
    inbf = 0;
}

/* --------------- Open a sort work file ------------------- */
static FILE *wopen(char *name, int n)
{
    FILE *fp;
    strcpy(name, "sortwork.000");
    name[strlen(name) - 1] += n;
    if ((fp =  fopen(name, "wb+")) == NULL) {
        printf("\nFile error");
        exit(1);
    }
    return fp;
}

/* --------- Function to get sorted records ----------------
This is called to get sorted records after the sort is done.
It returns pointers to each sorted record.
Each call to it returns one record.
When there are no more records, it returns NULL. ------ */

char *sort_op()
{
    int j = 0;
    int nrd, i, k, l;
    struct bp *rr;
    static int r1 = 0;
    char *rtn;
    long ad, tr;

    sptr = (char **) init_sptr;
    if (no_seq < 2) {
        /* -- with only 1 sequence, no merge has been done -- */
        if (r1 == totrcd)   {
            free(init_sptr);
            fp1 = fp2 = NULL;
            r1 = 0;
            return NULL;
        }
        return *(sptr + r1++);
    }
    rr = (struct bp *) init_sptr;
    for (i = 0; i < no_seq; i++)
        j |= (rr + i)->rbuf | (rr + i)->rdsk;

    /* -- j will be true if any sequence still has records - */
    if (!j)     {
        fclose(fp1);            /* none left */
        remove(fdname);
        if (fp2)    {
            fclose(fp2);
            remove(f2name);
        }
        free(init_sptr);
        fp1 = fp2 = NULL;
        r1 = 0;
        return NULL;
    }
    k = 0;

    /* --- find the sequence in the merge buffer
                        with the lowest record --- */
    for (i = 0; i < no_seq; i++)
        k = ((comp( &(rr + k)->rc, &(rr + i)->rc) < 0) ? k : i);

    /* --- k is an integer sequence number that offsets to the
        sequence with the lowest record ---- */

    (rr + k)->rbuf--;           /* decrement the rcd counter */
    rtn = (rr + k)->rc;         /* set the return pointer */
    (rr + k)->rc += sp->rc_len;
    if ((rr + k)->rbuf == 0)    {
        /* ---- the sequence got empty ---- */
        /* --- so get some more if there are any --- */
        rtn = bf + k * rcds_seq * sp->rc_len;
        memmove(rtn, (rr + k)->rc - sp->rc_len, sp->rc_len);
        (rr + k)->rc = rtn + sp->rc_len;
        if ((rr + k)->rdsk != 0)    {
            l = ((rcds_seq-1) < (rr+k)->rdsk) ?
                rcds_seq-1 : (rr+k)->rdsk;
            nrd = k == no_seq - 1 ? totrcd % nrcds : nrcds;
            tr = (long) ((k * nrcds + (nrd - (rr + k)->rdsk)));
            ad = tr * sp->rc_len;
            fseek(fp1, ad, SEEK_SET);
            fread(rtn + sp->rc_len, l * sp->rc_len, 1, fp1);
            (rr + k)->rbuf = l;
            (rr + k)->rdsk -= l;
        }
        else
            memset((rr + k)->rc, 127, sp->rc_len);
    }
    return rtn;
}

/* ------- Function to display sort stats -------- */
void sort_stats()
{
    printf("\n\n\nRecord Length = %d",sp->rc_len);
    printf("\n%d records sorted",totrcd);
    printf("\n%d sequence",no_seq1);
    if (no_seq1 != 1)
        putchar('s');
    printf("\n%u characters of sort buffer", bspace);
    printf("\n%d records per buffer\n\n",nrcds1);
}

/* ----- appropriate available memory ----- */
static char *appr_mem(unsigned *h)
{
    char *buff = NULL;

    *h = (unsigned) MOSTMEM + 1024;
    while (buff == NULL && *h > LEASTMEM)   {
        *h -= 1024;
        buff = malloc(*h);
    }
    return buff;
}

/* ------- compare function for sorting, merging -------- */
static int comp(char **a, char **b)
{
    int i, k;

    if (**a == 127 || **b == 127)
        return (int) **a - (int) **b;
    for (i = 0; i < NOFLDS; i++)    {
        if (sp->s_fld[i].f_pos == 0)
            break;
        if ((k = strnicmp((*a)+sp->s_fld[i].f_pos - 1,
                          (*b)+sp->s_fld[i].f_pos - 1,
                          sp->rc_len)) != 0)
            return (sp->s_fld[i].ad == 'd')?-k:k;
    }
    return 0;
}
```
