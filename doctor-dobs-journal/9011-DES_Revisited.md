
# C PROGRAMMING 9011

## DES Revisited and the Shaft - Al Stevens

DATUM NOVEMBAR 2012: Posle 22 godine mešanja od suda do suda, od mesta do mesta, od nadležnosti do nadležnosti, Vrhovni sud Sjedinjenih Država konačno je rešio slučaj FoobarSoft protiv Stevensa. Čini se da je 1990. godine, u vreme značajne odluke u kojoj mišljenje kolumniste više nije bilo zaštićeno od tvrdnji o kleveti, Stivens bio žrtva obične bolesti kolumniste poznate kao „zaostajanje u kafani“. Nesvestan predstojećeg smanjenja prava kolumnista, i radeći sa uobičajenim rokom od četiri meseca kolumniste, Stivens je poslušno izvestio svoju odanu čitalačku publiku da je FoobarSoft-ov novi Foobar C++ kompajler imao malu grešku gde Foobar C++ Programer's Chaise Lounge nije uspeo da dozvoli da programeru klasu _programeru dodeli prednost da dodeli sastojke. Kao rezultat toga, programeri su bili primorani da preopterećuju, inkapsuliraju i polimorfizuju pod uticajem dvodelne votke u jednodelni vermut. Stivens je dalje rekao da nije bio u stanju da održava mentalni trag o hijerarhijama klasa i mrežama sa više od 3500 izvedenih klasa dok je bio pod uticajem instanciranih objekata klase vodka_martini. Izrazio je svoje čvrsto mišljenje da programerima treba dozvoliti da definišu suvi martini po ukusu i zaključio da FoobarSoft C++ ima nedostatak.

Nakon što je kolumna bila „u limenci“, kako mi kažemo, ali pre nego što je broj dospeo u ruke čitalaca, Vrhovni sud je doneo svoju odluku. Ako mišljenje kolumniste nekome nanosi štetu, a mišljenje nije jasno činjenično, ta osoba je oklevetana i ima pravo na obeštećenje.

Bili Goats, predsednik FoobarSoft-a, čuo je od programera iz Bangora u državi Mejn koji je rekao da će zbog Stivensovih primedbi programer odložiti promenu paradigme dok svi kompajleri ne isprave sve svoje sastojke. Ghoats je konsultovao svoje pravno osoblje koje mu je savetovalo da tuži Stivensa za 195,53 dolara, profit koji FoobarSoft ostvaruje pri svakoj prodaji paketa C++ od 200 dolara. Želeći satisfakciju kao i kompenzaciju, Goats je takođe podneo peticiju sudu da mu dozvoli da zagrebe "C++" po Stivensovim grudima kao kaznenu odštetu.

Sada, posle 22 godine, slučaj je konačno rešen. Mnogo kašnjenja je rezultat teškog kalendara suda koji je uključivao 7.532 tužbe koje su protiv Endija Runija podneli proizvođači hrane, sapuna i kozmetike odmah nakon značajne presude iz 1990. godine. Odluku u slučaju Stivens doneo je skoro isti Vrhovni sud koji je doneo prvobitnu presudu. Glavni sudija Vapner, koga je predsednik Kvejl imenovao 1998. godine, pročitao je mišljenje. Sud je jednoglasno odlučio da Stivensov izveštaj o ponašanju FoobarSoft C++ ne predstavlja klevetu jer je činjenično. Međutim, nazivanje ponašanja greškom i nedostatkom je, po mišljenju suda, nečinjenično i štetno za FoobarSoft, pa je tužilac dobio nagradu u iznosu koji je prvobitno zahtevan. Kaznene odštete su ukinute jer Stivens, koji sada ima 72 godine i penzionisan, nosi samo majice prikupljene tokom godina sa konferencija o softveru i pakete recenzenta koje je obezbedio prodavac, a Ghoats se plašio da će Stivens insistirati na nošenju retke i sada kolekcionarske „Foobar 'dok vam pokazivač ne padne!“ izdanje izdato na FoobarSoft-Voodstock Grog and Granola festivalu 1995. i naknadno zabranjeno u Berkliju.

Popustljivost kojoj sam te upravo podvrgao nije tako nategnuta kao što se čini. Presuda Vrhovnog suda se zaista dogodila. Mi kolumnisti i recenzenti proizvoda bolje pazimo na korake. Dokle može da stigne ovaj novi stav? Ako raspravljam o kompajleru, uređivaču ili debageru u ovoj kolumni i ne sviđa mi se, moje mišljenje postaje hrana za advokata. Ako programu nedostaju funkcije, da li je moje mišljenje da nedostatak predstavlja nedostatak činjenično ili je u pitanju kleveta? Ko će to prvi testirati?

Koliko daleko sežu implikacije ove odluke? Da li je ova moja tvrdoglava tirada klevetna prema samom Vrhovnom sudu? Vau! Da li biste voleli da vas tuži gomila sudija Vrhovnog suda?

### DES Revisited

U septembru sam objavio C implementaciju standarda za šifrovanje podataka (DES) algoritma. Napravio sam ga iz dokumenta Nacionalnog biroa za standarde FIPS PUB 46 koji opisuje algoritam. Požalio sam se zbog nedostatka skupa podataka za validaciju koji bi mi omogućio da utvrdim da li će algoritam biti u skladu sa specifikacijom. I bez jednog sam išao napred. Činilo se da programi rade. Odradili su sjajan posao ispravljanja datoteke do neprepoznatljivosti i vraćanja je ponovo u upotrebljiv format.

Ričard Fizel, čitalac, poslao mi je poruku na CompuServe-u rekavši da je uporedio rezultate mojih programa sa onima hardverske ploče koja je implementirala DES, i rezultati su bili drugačiji. Proveo je dosta vremena gledajući kod i ukazao na neke moguće probleme. Ričard nije imao DES dokument, tako da nije mogao da kaže gde je kod zalutao ili da li su ploča i moji programi samo različite implementacije. Kasnije mi je rekao da je pronašao DES kod i podatke o validaciji na CompuServe-u. DES hardver koji je koristio prošao je testove obezbeđene podacima za validaciju.

To su bile dobre vesti. Stvarno mi je bila potrebna datoteka sa podacima za validaciju. DES kod u preuzimanju je bio bonus za koji se ispostavilo da je spasio život. Setite se da DES algoritam uključuje mnogo petlji permutacija bitova. Implementatoru je potreban niz blokova podataka koji predstavljaju privremene korake koje algoritam preduzima. Primeri ulaza i izlaza su bili od pomoći, ali samo da bi se utvrdilo da li program radi ili ne radi. Ništa vas zapravo ne upućuje na određeno mesto u proceduri gde se desi prvi neuspeh. DES algoritam ulazi u petlju u kojoj se polovine 64-bitnog bloka podataka permutuju, zamenjuju i menjaju sa vrednošću izvedenom iz ključa. Prva greška, ma koliko mala, širiće se kao pečurka.

Velika pomoć je došla od programa koji je pratila datoteka sa podacima o validaciji. To je javni C program koji je prvi primenio Džejms Gilogli, a kasnije modifikovao Fil Karn. Možete ga pronaći na CompuServe-u kao datoteku DES.ARC u biblioteci podataka 3 na IBMPRO forumu. Bez sumnje je dostupan na drugim BBS-ovima i onlajn servisima. Program, koji je napisan pre nekog vremena, ne koristi ANSI C konvencije, ali su autori uključili verziju koja se kompajlira sa Turbo C-om, upozorenjima i ostalim.

Prateći napredak Gillogli/Karn programa i upoređujući njegove rezultate sa rezultatima mog programa, uspeo sam da ispravim probleme. Zbog permutacija podataka u DES-u, bio sam siguran da je moja implementacija strukture algoritma dobra. Šifrovao je i dešifrovao velike datoteke bez gubitka podataka. Problemi su se odnosili na interne reprezentacije blokova podataka koji su uzrokovali da se programi permutiraju drugačije nego što bi trebalo. I takvi su bili, većina njih. Većinu problema sam rešio korišćenjem funkcije svapbite koja menja redosled bajtova dugog celog broja iz aabbccdd u ddccbbaa. Program Gillogli/Karn koristi takvu funkciju ali samo u Turbo C verziji. Očigledno bajt/reč arhitektura originalne mašine ne poređa bajtove u prikazu unazad 8- i 16-bitnih mašina što me muči još od kada sam prvi put video PDP-11.

Bilo je još nekoliko problema. Nisam pravilno rotirao izlaz rasporeda ključeva. To je bila greška koja nije sprečila programe da šifruju i dešifruju, ali šifrovani blokovi podataka nisu bili kompatibilni sa DES specifikacijom i verovatno nisu tako bezbedni.

David Dunthorn iz Oak Ridgea, Tennessee, napisao je da moje zapažanje o 8-bitnoj vrijednosti koja je sačuvana u šifrovanoj datoteci znači da moj DES kod ne radi ispravno. Kako istinito. Ponašanje koje sam video bilo je nusprodukt drugih problema i dovelo me je do pogrešnog zaključka. G. Dunthorn je dao primere podataka koji mogu poslužiti za testiranje implementacije DES-a. Primer 1 sadrži tri primera koje je poslao. Kod u koloni ovog meseca ispravno obrađuje ove primere, kao i one u preuzetim podacima o validaciji i drugim primerima od gospodina Dunthorna.

G. Dunthorn nastavlja:

Algoritam za DES je toliko povezan da ako program uradi čak i nekoliko primera ispravno, postoji veoma velika verovatnoća da će biti tačan. Jedna mala promena bilo gde će dati potpuno drugačiji rezultat, a ne nešto što je samo malo pogrešno. . . . Izazov je da DES radi ispravno čak i kada se radi prema specifikaciji.

Ja ću reći.

DES specifikacija kaže da najmanji bitni bit svakog bajta ključa može biti za bit parnosti ili šta god želite da bude, i tako algoritam ignoriše najmanji značajan bit ključa. Kada se izlaz mog programa nije poklapao sa izlazom iz Gillogli/Karn programa, otkrio sam da oni koriste najznačajniji bit za paritet, a ja nisam. DES specifikacija se ne bavi problemima implementacije povezanih sa stvarima kao što su datoteke i paritet ključeva. Specifikacija se odnosi na to kako šifrujete i dešifrujete 8-bajtne blokove podataka pomoću 8-bajtnog ključa. Paritetni kod u programu Gillogli/Karn nalazi se u spoljnoj ljusci koja upravlja datotekama i ključem i poziva unutrašnji kod za šifrovanje i dešifrovanje. Unutrašnji algoritmi su u skladu sa DES specifikacijom. Iz ovoga sam shvatio da različiti usaglašeni DES programi koji šifruju i dešifruju datoteke podataka neće uvek biti kompatibilni. Dodao sam logiku neparnog pariteta svom programu kako bih mogao da šifrujem i dešifrujem datoteke naizmenično između mog programa i preuzetog programa da bih testirao algoritam.

Dokumentacija koja je isporučena sa programom Gillogli/Karn identifikovala je režim DES Cipher Block Chaining (CBC), nešto što nije adresirano u NBS 1977 FIPS PUB DES specifikaciji, iako program koristi CBC režim kao podrazumevani. To me je nateralo neko vreme. U CBC režimu, prethodni šifrovani blok od 8 bajtova je ekskluzivno ili poređan sa trenutnim nešifrovanim blokom pre nego što program šifruje trenutni blok. Drugi režim, koji zaobilazi dodatno šifrovanje, je režim elektronske šifre. To je režim koji podržavaju moji programi. Gillogli/Karn program implementira CBC režim izvan DES koda, tako da je to još jedna oblast u kojoj će se implementacije razlikovati.

Spomenuo sam u septembru da DES algoritam ima nedostatak. Pošto šifruje blokove od 8 bajtova preklapanjem i rasipanjem bitova po celom bloku, šifrovana datoteka mora nužno imati dužinu koja je višestruka od 8 bajtova. DES algoritam nema načina da zna stvarnu dužinu tog poslednjeg bloka. U nekim aplikacijama precizna lokacija kraja datoteke je kritična za korišćenje datoteke. Program Gillogli/Karn beleži dužinu poslednjeg bloka u poslednjem bajtu datoteke. Ako je datoteka paran umnožak od 8 bajtova, algoritam dodaje blok sa tom informacijom u njoj. Ovaj pristup rešava problem, ali nije deo DES specifikacije. Kao i kod drugih ekstenzija, ovo rešenje proizvodi format datoteke koji ne bi bio kompatibilan sa drugim implementacijama.

Primer 1: Primeri podataka za DES testiranje

  Key:   0123456789ABCDEF  0123456789ABCDEF  0123456789ABCDEF
  Text:  4E6F772069732074  68652074696D6520  666F7220616C6C20
  Encr:  3FA40E8A984D4815  6A271787AB8883F9  893D51EC4B563B53

Verovatno najznačajnija razlika između ove dve implementacije je u njihovoj brzini izvršavanja. Program Gillogli/Karn je brži. Oni ne sprovode sve permutacije koristeći tabele kao što sam ja uradio. Umesto toga, da bi poboljšali performanse, oni koriste određene karakteristike vrednosti tabele da kodiraju neke od permutacija sa pomacima i maskama. Cena za ta poboljšanja performansi je što je kod na nekim mestima teško razumeti. Često nisam mogao da uporedim njihove privremene rezultate sa svojim jer su naši pristupi bili dovoljno različiti da nije bilo odgovarajućih obrazaca podataka za poređenje.

Kolona „Programiranje na C“ se odnosi na C kod koliko i na algoritme, tako da nisam pokušao da ugradim nijedno od poboljšanja Gillogli/Karn performansi. Da koristim DES u okruženju malih datoteka, kao što je aplikacija za elektronsku poštu, verovatno bih koristio svoje programe jer bi mi ih bilo lakše razumeti i održavati. Za aplikacije koje uključuju velike datoteke, prilagodio bih Gillogli/Karn algoritam da dobijem njegove prednosti u pogledu performansi. Ili bih potražio neke od implementacija asemblerskog jezika koje su još brže.

Promenio sam strukturu programa da bih izolovao DES algoritme od koda koji upravlja ključem i datotekama. Ispravljene i modifikovane DES programe ćete pronaći ovde u [LISTING_ONE](#listing_one), "des.h", [LISTING_TWO](#listing_two), "main.c", [LISTING_THREE](#listing_three), "des.c" i [LISTING_FOUR](#listing_four), tables.c (stranica 165). Kompajlirajte i povežite sve u izvršni program pod nazivom „des.eke“. Umesto odvojenih programa za šifrovanje i dešifrovanje, kakve smo imali u septembru, des program upravlja obema funkcijama. Navodite -e ili -d kao prvi parametar komandne linije da kažete programu koju operaciju da izvede. Drugi parametar je 8-bajtni ključ, a treći i četvrti parametar su nazivi ulaznih i izlaznih datoteka.

Možete da ugradite DES funkcije u svoje programe tako što ćete uključiti des.h i povezati se sa des.c i tables.c. Pozovite funkciju initkei sa adresom vašeg 8-bajtnog ključa kao argumenta. Zatim pozovite funkciju za šifrovanje ili dešifrovanje jednom za svaki 8-bajtni blok u podacima koje želite da šifrujete ili dešifrujete. Obe funkcije prihvataju pokazivač na 8-bajtni blok gde će ići ulaz i izlaz iz algoritma.

### CRIPTO program

Čitalac po imenu Dejv je pozvao da kaže da cripto.c, moj prvi primer jednostavnog algoritma za šifrovanje/dešifrovanje, nije baš dobar. Čini se da je šifrovao datoteku koja je imala dugačke nizove od nula bajtova. Kripto program šifruje i dešifruje jednostavnim isključivanjem ili korišćenjem 8-bajtnog ključa sa svakim uzastopnim blokom od 8 bajta datoteke sa podacima. Kada datoteka ima nizove od nula bajtova, šifrovana vrednost je sam ključ.

Dizajnirao sam cripto.c za šifrovanje ASCII teksta elektronske pošte, koji nikada nema nizove bajtova nulte vrednosti. Međutim, trebalo je da te upozorim na takvo ponašanje.

Drugi čitalac, Roberto Kuijalvo iz Plantationa, Florida, napisao je da je istakao da ako vaša ASCII tekstualna datoteka ima nizove razmaka i ključ je takođe ASCII tekst, isključiva-ili umeće ključ u tekst sa promenom velikih i malih slova. Taj me nikada nije ugrizao jer moje ASCII datoteke prolaze kroz koder dužine pokretanja, koji skraćuje zaostali beli prostor iz redova i komprimuje druge nizove znakova u izlazne sekvence pre šifrovanja. Taj proces, međutim, nije bio za šifrovanje, već za minimiziranje vremena prenosa.

G. Kuijalvo je dao neki kod +da interno pokvari ključ. On kaže

Mala izmena u kodu bi otežala otkrivanje ključa:

```c
  /* original code segment */
  while (*cp1 && cp1 < argv [1]+8)
      *cp2++ ^= *cp1++;

  /* altered code segment */
  while (*cp1 && cp1 < argv [1]+8)
       *cp2++ ^=
               (*cp1<0x41 ? *cp1++:
        *cp1++ - 0x40);
```

Proveravanjem ključa za znak veći od 0k40 ('@') i promenom bilo kog alfa znaka u ne-alfa znak pre nego što ga upotrebite, ključ se može sačuvati od tako sramotno očigledne izloženosti.

Frensis Roks iz Bervina u Pensilvaniji prijavio je isti problem i ponudio drugačije rešenje. Francis kaže,

Rešenje bi bilo da eliminišete hek 20 (prazno) kodiranje sa sledećim kodom koji zamenjuje vašu drugu konstrukciju „vhile“:

```c
  while (*cp1 && cp1 < argv[1]+8) {
         if (*cp2 != 0x20) {
            *cp2++ ^=*cp1++;
         }
         else {
              *cp2++;
              *cp1++;
         }
  }
```

Sva tri čitaoca su mislila da je cripto.c manje bezbedan nego što bi trebalo da bude, što jeste ako ga koristite van okruženja kontrolisanih podataka kao što je ono za koje sam napravio algoritam.

Ove modifikacije su, naravno, samo potezi za poboljšanje jednostavnog algoritma čija je prvobitna namera bila samo da trenutno skrene samo radoznale, što je razlog za šifrovanje za većinu nas. U neprijateljskijem okruženju, ako napadač zna da je tekst šifrovan, on ili ona moraju otkriti algoritam kao i ključ. Pošto je algoritam isključivanja-ili uobičajen, pojava bilo kog ponovljenog obrasca, čak i iskrivljenog ključa, ima tendenciju da upozori špijuna na mogućnost isključivanja-ili ispravljanja cele poruke različitim permutacijama tog obrasca da vidi šta će se dogoditi. Roksovo rešenje radi samo za nizove prostora. Ali ako datoteka ima ponovljene nizove drugih vrednosti, fiksne permutacije ključa će biti unutra, a razbijač koda će iskoristiti obrasce.

Kripto program je bio kao brava za bicikl. To bi odvratilo slučajnog uljeza, ali ozbiljan lopov ima alate i veštine da ga prođe. Ako želite da budete toliko sigurni, potrebno vam je nešto drugo osim kripto.

[LISTING_FIVE](#listing_five) i [LISTING_SIX](#listing_six), su "encripto.c" i "decripto.c", dva programa koji eliminišu izložene ključne probleme koje "cripto.c" ima sa tekstualnim datotekama. Kombinovao sam algoritam za kodiranje dužine trajanja mog kompresora teksta i šifrovanje programa cripto.c u ova dva programa. Pored šifrovanja i dešifrovanja, oni komprimuju niz dupliranih znakova u jednostavnu escape sekvencu. Programi rade samo sa ASCII tekstualnim datotekama.

Program encripto.c prevodi svaki niz od dva ili više dupliranih karaktera u 2 bajta. Prvi bajt je broj pokretanja sa uključenim bitnim bitom. Drugi bajt je duplirani karakter. Pošto algoritam koristi najznačajniji bit za identifikaciju brojača dužine pokretanja, on radi samo sa 7-bitnim ASCII datotekama i prekinuće se ako pronađe najvažniji bit postavljen u ulaznoj datoteci. Nakon što encripto.c komprimuje tekst, on ga šifruje u blokove dužine jednake dužini ključa. Kompresija i šifrovanje se obavljaju u jednom prolazu datoteke.

Program decripto.c preokreće proces kompresije/šifrovanja. On dešifruje znakove, a zatim ih dekompresuje.

Program cripto.c u septembru je koristio ključ fiksne dužine od 8 bajtova. encripto.c i decripto.c uzimaju dužinu ključa koja je uneta i šifruju/dešifruju blokove te dužine.

Razmišljao sam da napišem identifikacioni potpis na prednjoj strani šifrovane datoteke kako bi program decripto.c mogao da potvrdi da se od njega traži da dešifruje ispravno šifrovanu datoteku. Odbio sam ovu ideju jer bi osoba sa strane koja je presrela datoteku mogla tada znati koji program ju je šifrovao. Zatim sam razmišljao o šifrovanju potpisa. Odbio sam i tu ideju. Razbijač šifre koji je već poznavao algoritam bi tada imao poznatu vrednost podataka iz koje bi mogao da izvrši reverzni inženjering ključa.

Iako nisu imuni na napade upornog razbijača šifre, algoritmi u encripto.c i decripto.c su bezbedniji od onih jednostavnijeg programa cripto.c iz septembra i daleko manje složeni od onih u DES-u.

#### LISTING_ONE

```c
/* -------------- des.h ---------------- */
/* Header file for Data Encryption Standard algorithms  */

/* -------------- prototypes ------------------- */
void initkey(char *key);
void encrypt(char *blk);
void decrypt(char *blk);

/* ----------- tables ------------ */
extern unsigned char Pmask[];
extern unsigned char IPtbl[];
extern unsigned char Etbl[];
extern unsigned char Ptbl[];
extern unsigned char stbl[8][4][16];
extern unsigned char PC1tbl[];
extern unsigned char PC2tbl[];
extern unsigned char ex6[8][2][4];
```

#### LISTING_TWO

```c
/* Data Encryption Standard front end
 * Usage: des [-e -d] keyvalue infile outfile
 */

#include <stdio.h>
#include <string.h>
#include "des.h"

static void setparity(char *key);

void main(int argc, char *argv[])
{
   FILE *fi, *fo;
   char key[9];
   char blk[8];

   if (argc > 4)    {
        strncpy(key, argv[2], 8);
        key[8] = '\0';
        setparity(key);

        initkey(key);
       if ((fi = fopen(argv[3], "rb")) != NULL)    {
           if ((fo = fopen(argv[4], "wb")) != NULL)    {
               while (!feof(fi))    {
                   memset(blk, 0, 8);
                   if (fread(blk, 1, 8, fi) != 0)    {
                       if (stricmp(argv[1], "-e") == 0)
                           encrypt(blk);
                       else
                           decrypt(blk);
                          fwrite(blk, 1, 8, fo);
                   }
               }
               fclose(fo);
           }
           fclose(fi);
       }
   }
   else
       printf("\nUsage: des [-e -d] keyvalue infile outfile");
}

/* -------- make a character odd parity ---------- */
static unsigned char oddparity(unsigned char s)
{
   unsigned char c = s | 0x80;
   while (s)    {
       if (s & 1)
           c ^= 0x80;
       s = (s >> 1) & 0x7f;
   }
   return c;
}

/* ------ make a key odd parity ------- */
void setparity(char *key)
{
   int i;
   for (i = 0; i < 8; i++)
       *(key+i) = oddparity(*(key+i));
}
```

#### LISTING_THREE

```c
/* ---------------------- des.c --------------------------- */
/* Functions and tables for DES encryption and decryption
 */

#include <stdio.h>
#include <string.h>
#include "des.h"

/* -------- 48-bit key permutation ------- */
struct ks    {
    char ki[6];
};

/* ------- two halves of a 64-bit data block ------- */
struct LR    {
    long L;
    long R;
};

static struct ks keys[16];

static void rotate(unsigned char *c, int n);
static int fourbits(struct ks, int s);
static int sixbits(struct ks, int s);
static void inverse_permute(long *op,long *ip,long *tbl,int n);
static void permute(long *op, long *ip, long *tbl, int n);
static long f(long blk, struct ks ky);
static struct ks KS(int n, char *key);
static void swapbyte(long *l);

/* ----------- initialize the key -------------- */
void initkey(char *key)
{
    int i;
    for (i = 0; i < 16; i++)
        keys[i] = KS(i, key);
}

/* ----------- encrypt an 8-byte block ------------ */
void encrypt(char *blk)
{
   struct LR ip, op;
   long temp;
   int n;

   memcpy(&ip, blk, sizeof(struct LR));
   /* -------- initial permuation -------- */
   permute(&op.L, &ip.L, (long *)IPtbl, 64);
   swapbyte(&op.L);
   swapbyte(&op.R);
   /* ------ swap and key iterations ----- */
   for (n = 0; n < 16; n++)    {
       temp = op.R;
       op.R = op.L ^ f(op.R, keys[n]);
       op.L = temp;
   }
   ip.R = op.L;
   ip.L = op.R;
   swapbyte(&ip.L);
   swapbyte(&ip.R);
   /* ----- inverse initial permutation ---- */
   inverse_permute(&op.L, &ip.L,
       (long *)IPtbl, 64);
   memcpy(blk, &op, sizeof(struct LR));
}

/* ----------- decrypt an 8-byte block ------------ */
void decrypt(char *blk)
{
   struct LR ip, op;
   long temp;
   int n;

   memcpy(&ip, blk, sizeof(struct LR));
   /* -------- initial permuation -------- */
   permute(&op.L, &ip.L, (long *)IPtbl, 64);
   swapbyte(&op.L);
   swapbyte(&op.R);
   ip.R = op.L;
   ip.L = op.R;
   /* ------ swap and key iterations ----- */
   for (n = 15; n >= 0; --n)    {
       temp = ip.L;
       ip.L = ip.R ^ f(ip.L, keys[n]);
       ip.R = temp;
   }
   swapbyte(&ip.L);
   swapbyte(&ip.R);
   /* ----- inverse initial permuation ---- */
   inverse_permute(&op.L, &ip.L,
       (long *)IPtbl, 64);
   memcpy(blk, &op, sizeof(struct LR));
}

/* ------- inverse permute a 64-bit string ------- */
static void inverse_permute(long *op,long *ip,long *tbl,int n)
{
    int i;
    long *pt = (long *)Pmask;

    *op = *(op+1) = 0;
    for (i = 0; i < n; i++)    {
       if ((*ip & *pt) || (*(ip+1) & *(pt+1)))  {
           *op |= *tbl;
           *(op+1) |= *(tbl+1);
        }
        tbl += 2;
        pt += 2;
   }
}

/* ------- permute a 64-bit string ------- */
static void permute(long *op, long *ip, long *tbl, int n)
{
    int i;
    long *pt = (long *)Pmask;

    *op = *(op+1) = 0;
    for (i = 0; i < n; i++)    {
        if ((*ip & *tbl) || (*(ip+1) & *(tbl+1))) {
            *op |= *pt;
            *(op+1) |= *(pt+1);
        }
        tbl += 2;
        pt += 2;
    }
}

/* ----- Key dependent computation function f(R,K) ----- */
static long f(long blk, struct ks key)
{
    struct LR ir;
    struct LR or;
    int i;

    union    {
        struct LR f;
        struct ks kn;
    } tr = {0,0}, kr = {0,0};

    ir.L = blk;
    ir.R = 0;

    kr.kn = key;

    swapbyte(&ir.L);
    swapbyte(&ir.R);

    permute(&tr.f.L, &ir.L, (long *)Etbl, 48);

    tr.f.L ^= kr.f.L;
    tr.f.R ^= kr.f.R;

   /*   the DES S function: ir.L = S(tr.kn);  */
    ir.L = 0;
    for (i = 0; i < 8; i++)    {
        long four = fourbits(tr.kn, i);
        ir.L |= four << ((7-i) * 4);
    }
    swapbyte(&ir.L);

    ir.R = or.R = 0;
    permute(&or.L, &ir.L, (long *)Ptbl, 32);

    swapbyte(&or.L);
    swapbyte(&or.R);

    return or.L;
}

/* ------- extract a 4-bit stream from the block/key ------- */
static int fourbits(struct ks k, int s)
{
    int i = sixbits(k, s);
    int row, col;
    row = ((i >> 4) & 2) | (i & 1);
    col = (i >> 1) & 0xf;
    return stbl[s][row][col];
}

/* ---- extract 6-bit stream fr pos s of the  block/key ---- */
static int sixbits(struct ks k, int s)
{
    int op = 0;
    int n = (s);
    int i;
    for (i = 0; i < 2; i++)    {
        int off = ex6[n][i][0];
        unsigned char c = k.ki[off];
        c >>= ex6[n][i][1];
        c <<= ex6[n][i][2];
        c &=  ex6[n][i][3];
        op |= c;
    }
    return op;
}

/* ---------- DES Key Schedule (KS) function ----------- */
static struct ks KS(int n, char *key)
{
    static unsigned char cd[8];
    static int its[] = {1,1,2,2,2,2,2,2,1,2,2,2,2,2,2,1};
    union    {
        struct ks kn;
        struct LR filler;
    } result;

    if (n == 0)
        permute((long *)cd, (long *) key, (long *)PC1tbl, 64);

    rotate(cd, its[n]);
    rotate(cd+4, its[n]);

    permute(&result.filler.L, (long *)cd, (long *)PC2tbl, 48);
    return result.kn;
}

/* rotate a 4-byte string n (1 or 2) positions to the left */
static void rotate(unsigned char *c, int n)
{
    int i;
    unsigned j, k;
    k = ((*c) & 255) >> (8 - n);
    for (i = 3; i >= 0; --i)    {
        j = ((*(c+i) << n) + k);
        k = (j >> 8) & 255;
        *(c+i) = j & 255;
    }
    if (n == 2)
       *(c+3) = (*(c+3) & 0xc0) | ((*(c+3) << 4) & 0x30);
    else
       *(c+3) = (*(c+3) & 0xe0) | ((*(c+3) << 4) & 0x10);
}

/* -------- swap bytes in a long integer ---------- */
static void swapbyte(long *l)
{
   char *cp = (char *) l;
   char t = *(cp+3);

   *(cp+3) = *cp;
   *cp = t;
   t = *(cp+2);
   *(cp+2) = *(cp+1);
   *(cp+1) = t;
}
```

#### LISTING_FOUR

```c
/* --------------- tables.c --------------- */
/* tables for the DES algorithm
 */

/* --------- macros to define a permutation table ---------- */
#define ps(n)       ((unsigned char)(0x80 >> (n-1)))
#define b(n,r)      ((n>r||n<r-7)?0:ps(n-(r-8)))
#define p(n)        b(n, 8),b(n,16),b(n,24),b(n,32),\
                    b(n,40),b(n,48),b(n,56),b(n,64)
#define q(n)        p((n)+4)

/* --------- permutation masks ----------- */
unsigned char Pmask[] = {
    p( 1),p( 2),p( 3),p( 4),p( 5),p( 6),p( 7),p( 8),
    p( 9),p(10),p(11),p(12),p(13),p(14),p(15),p(16),
    p(17),p(18),p(19),p(20),p(21),p(22),p(23),p(24),
    p(25),p(26),p(27),p(28),p(29),p(30),p(31),p(32),
    p(33),p(34),p(35),p(36),p(37),p(38),p(39),p(40),
    p(41),p(42),p(43),p(44),p(45),p(46),p(47),p(48),
    p(49),p(50),p(51),p(52),p(53),p(54),p(55),p(56),
    p(57),p(58),p(59),p(60),p(61),p(62),p(63),p(64)
};

/* ----- initial and inverse-initial permutation table ----- */
unsigned char IPtbl[] = {
    p(58),p(50),p(42),p(34),p(26),p(18),p(10),p( 2),
    p(60),p(52),p(44),p(36),p(28),p(20),p(12),p( 4),
    p(62),p(54),p(46),p(38),p(30),p(22),p(14),p( 6),
    p(64),p(56),p(48),p(40),p(32),p(24),p(16),p( 8),
    p(57),p(49),p(41),p(33),p(25),p(17),p( 9),p( 1),
    p(59),p(51),p(43),p(35),p(27),p(19),p(11),p( 3),
    p(61),p(53),p(45),p(37),p(29),p(21),p(13),p( 5),
    p(63),p(55),p(47),p(39),p(31),p(23),p(15),p( 7)
};

/* ---------- permutation table E for f function --------- */
unsigned char Etbl[] = {
    p(32),p( 1),p( 2),p( 3),p( 4),p( 5),
    p( 4),p( 5),p( 6),p( 7),p( 8),p( 9),
    p( 8),p( 9),p(10),p(11),p(12),p(13),
    p(12),p(13),p(14),p(15),p(16),p(17),
    p(16),p(17),p(18),p(19),p(20),p(21),
    p(20),p(21),p(22),p(23),p(24),p(25),
    p(24),p(25),p(26),p(27),p(28),p(29),
    p(28),p(29),p(30),p(31),p(32),p( 1)
};

/* ---------- permutation table P for f function --------- */
unsigned char Ptbl[] = {
    p(16),p( 7),p(20),p(21),p(29),p(12),p(28),p(17),
    p( 1),p(15),p(23),p(26),p( 5),p(18),p(31),p(10),
    p( 2),p( 8),p(24),p(14),p(32),p(27),p( 3),p( 9),
    p(19),p(13),p(30),p( 6),p(22),p(11),p( 4),p(25)
};

/* --- table for converting six-bit to four-bit stream --- */
unsigned char stbl[8][4][16] = {
    /* ------------- s1 --------------- */
    14,4,13,1,2,15,11,8,3,10,6,12,5,9,0,7,
    0,15,7,4,14,2,13,1,10,6,12,11,9,5,3,8,
    4,1,14,8,13,6,2,11,15,12,9,7,3,10,5,0,
    15,12,8,2,4,9,1,7,5,11,3,14,10,0,6,13,
    /* ------------- s2 --------------- */
    15,1,8,14,6,11,3,4,9,7,2,13,12,0,5,10,
    3,13,4,7,15,2,8,14,12,0,1,10,6,9,11,5,
    0,14,7,11,10,4,13,1,5,8,12,6,9,3,2,15,
    13,8,10,1,3,15,4,2,11,6,7,12,0,5,14,9,
    /* ------------- s3 --------------- */
    10,0,9,14,6,3,15,5,1,13,12,7,11,4,2,8,
    13,7,0,9,3,4,6,10,2,8,5,14,12,11,15,1,
    13,6,4,9,8,15,3,0,11,1,2,12,5,10,14,7,
    1,10,13,0,6,9,8,7,4,15,14,3,11,5,2,12,
    /* ------------- s4 --------------- */
    7,13,14,3,0,6,9,10,1,2,8,5,11,12,4,15,
    13,8,11,5,6,15,0,3,4,7,2,12,1,10,14,9,
    10,6,9,0,12,11,7,13,15,1,3,14,5,2,8,4,
    3,15,0,6,10,1,13,8,9,4,5,11,12,7,2,14,
    /* ------------- s5 --------------- */
    2,12,4,1,7,10,11,6,8,5,3,15,13,0,14,9,
    14,11,2,12,4,7,13,1,5,0,15,10,3,9,8,6,
    4,2,1,11,10,13,7,8,15,9,12,5,6,3,0,14,
    11,8,12,7,1,14,2,13,6,15,0,9,10,4,5,3,
    /* ------------- s6 --------------- */
    12,1,10,15,9,2,6,8,0,13,3,4,14,7,5,11,
    10,15,4,2,7,12,9,5,6,1,13,14,0,11,3,8,
    9,14,15,5,2,8,12,3,7,0,4,10,1,13,11,6,
    4,3,2,12,9,5,15,10,11,14,1,7,6,0,8,13,
    /* ------------- s7 --------------- */
    4,11,2,14,15,0,8,13,3,12,9,7,5,10,6,1,
    13,0,11,7,4,9,1,10,14,3,5,12,2,15,8,6,
    1,4,11,13,12,3,7,14,10,15,6,8,0,5,9,2,
    6,11,13,8,1,4,10,7,9,5,0,15,14,2,3,12,
    /* ------------- s8 --------------- */
    13,2,8,4,6,15,11,1,10,9,3,14,5,0,12,7,
    1,15,13,8,10,3,7,4,12,5,6,11,0,14,9,2,
    7,11,4,1,9,12,14,2,0,6,10,13,15,3,5,8,
    2,1,14,7,4,10,8,13,15,12,9,0,3,5,6,11
};

/* ---- Permuted Choice 1 for Key Schedule calculation ---- */
unsigned char PC1tbl[] = {
    p(57),p(49),p(41),p(33),p(25),p(17),p( 9),
    p( 1),p(58),p(50),p(42),p(34),p(26),p(18),
    p(10),p( 2),p(59),p(51),p(43),p(35),p(27),
    p(19),p(11),p( 3),p(60),p(52),p(44),p(36),
    p(0),p(0),p(0),p(0),

    p(63),p(55),p(47),p(39),p(31),p(23),p(15),
    p( 7),p(62),p(54),p(46),p(38),p(30),p(22),
    p(14),p( 6),p(61),p(53),p(45),p(37),p(29),
    p(21),p(13),p( 5),p(28),p(20),p(12),p( 4),
    p(0),p(0),p(0),p(0)
};

/* ---- Permuted Choice 2 for Key Schedule calculation ---- */
unsigned char PC2tbl[] = {
    p(14),p(17),p(11),p(24),p( 1),p( 5),p( 3),p(28),
    p(15),p( 6),p(21),p(10),p(23),p(19),p(12),p( 4),
    p(26),p( 8),p(16),p( 7),p(27),p(20),p(13),p( 2),

    q(41),q(52),q(31),q(37),q(47),q(55),q(30),q(40),
    q(51),q(45),q(33),q(48),q(44),q(49),q(39),q(56),
    q(34),q(53),q(46),q(42),q(50),q(36),q(29),q(32)
};

/* ---- For extracting 6-bit strings from 64-bit string ---- */
unsigned char ex6[8][2][4] = {
    /* byte, >>, <<, & */
    /* ---- s = 8  ---- */
    0,2,0,0x3f,
    0,2,0,0x3f,
    /* ---- s = 7  ---- */
    0,0,4,0x30,
    1,4,0,0x0f,
    /* ---- s = 6  ---- */
    1,0,2,0x3c,
    2,6,0,0x03,
    /* ---- s = 5  ---- */
    2,0,0,0x3f,
    2,0,0,0x3f,
    /* ---- s = 4 ---- */
    3,2,0,0x3f,
    3,2,0,0x3f,
    /* ---- s = 3 ---- */
    3,0,4,0x30,
    4,4,0,0x0f,
    /* ---- s = 2 ---- */
    4,0,2,0x3c,
    5,6,0,0x03,
    /* ---- s = 1 ---- */
    5,0,0,0x3f,
    5,0,0,0x3f
};
```

#### LISTING_FIVE

```c
/* ---------------------- encrypto.c ----------------------- */
/* Single key text file encryption
 * Usage: encrypto keyvalue infile outfile
 */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define FALSE 0
#define TRUE !FALSE

static void charout(FILE *fo, char prev, int runct, int last);
static void encrypt(FILE *fo, char ch, int last);

static char *key = NULL;
static int keylen;
static char *cipher = NULL;
static int clen = 0;

void main(int argc, char *argv[])
{
   FILE *fi, *fo;
   char ch, prev = 0;
   int runct = 0;

   if (argc > 3)    {
       /* --- alloc memory for the key and cipher blocks --- */
       keylen = strlen(argv[1]);
       cipher = malloc(keylen+1);
       key = malloc(keylen+1);
       strcpy(key, argv[1]);

       if (cipher != NULL && key != NULL &&
               (fi = fopen(argv[2], "rb")) != NULL)        {
           if ((fo = fopen(argv[3], "wb")) != NULL)    {
               while ((ch = fgetc(fi)) != EOF)    {
                    /* ---- validate ASCII input ---- */
                   if (ch & 128)    {
                       fprintf(stderr, "%s is not ASCII",
                                   argv[2]);
                       fclose(fi);
                       fclose(fo);
                       remove(argv[3]);
                       free(cipher);
                       free(key);
                       exit(1);
                   }

                   /* --- test for duplicate bytes --- */
                   if (ch == prev && runct < 127)
                       runct++;
                   else    {
                       charout(fo, prev, runct, FALSE);
                       prev = ch;
                       runct = 0;
                   }
               }
               charout(fo, prev, runct, TRUE);
               fclose(fo);
           }
           fclose(fi);
       }
       if (cipher)
           free(cipher);
       if (key)
           free(key);
   }
}

/* ------- send an encrypted byte to the output file ------ */
static void charout(FILE *fo, char prev, int runct, int last)
{
   if (runct)
       encrypt(fo, (runct+1) | 0x80, last);
   if (prev)
       encrypt(fo, prev, last);
}

/* ---------- encrypt a byte and write it ---------- */
static void encrypt(FILE *fo, char ch, int last)
{
   *(cipher+clen) = ch ^ *(key+clen);
   clen++;
   if (last || clen == keylen)    {
       /* ----- cipher buffer full or last buffer ----- */
       int i;
       for (i = 0; i < clen; i++)
           fputc(*(cipher+i), fo);
       clen = 0;
   }
}
```

#### LISTING_SIX

```c
/* ---------------------- decrypto.c ----------------------- */
/* Single key text file decryption
 * Usage: decrypto keyvalue infile outfile
 */

#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <process.h>

static char decrypt(FILE *);

static char *key = NULL;
static int keylen;
static char *cipher = NULL;
static int clen = 0, coff = 0;

void main(int argc, char *argv[])
{
   FILE *fi, *fo;
   char ch;
   int runct = 0;

   if (argc > 3)    {
       /* --- alloc memory for the key and cipher blocks --- */
       keylen = strlen(argv[1]);
       cipher = malloc(keylen+1);
       key = malloc(keylen+1);
       strcpy(key, argv[1]);

       if (cipher != NULL && key != NULL &&
               (fi = fopen(argv[2], "rb")) != NULL)    {

           if ((fo = fopen(argv[3], "wb")) != NULL)    {
               while ((ch = decrypt(fi)) != EOF)    {
                   /* --- test for run length counter --- */
                   if (ch & 0x80)
                       runct = ch & 0x7f;
                   else    {
                       if (runct)
                           /* --- run count: dup the byte -- */
                           while (--runct)
                               fputc(ch, fo);
                       fputc(ch, fo);
                   }
               }
               fclose(fo);
           }
           fclose(fi);
       }
       if (cipher)
           free(cipher);
       if (key)
           free(key);
   }
}

/* ------ decryption function: returns decrypted byte ----- */
static char decrypt(FILE *fi)
{
   char ch = EOF;
   if (clen == 0)    {
       /* ---- read a block of encrypted bytes ----- */
       clen = fread(cipher, 1, keylen, fi);
       coff = 0;
   }
   if (clen > 0)    {
       /* --- decrypt the next byte in the input block --- */
       ch = *(cipher+coff) ^ *(key+coff);
       coff++;
       --clen;
   }
   return ch;
}
```
