
# C++ Vodič za C programere

Al Stevens, DDJ decembar '89

C++ je objektno orijentisan nadskup C-a koji je Bjarne Stroustrup razvio u AT&T Bell Laboratories počevši od 1980. godine i nastavljajući sa uzastopnim izdanjima. Iako C++ seže skoro deset godina unazad, njegova nedavna pažnja u PC zajednici je u velikoj meri posledica sve većeg interesovanja za objektno orijentisano programiranje i dostupnosti novih okruženja za razvoj jezika C++.

U ovom članku ću raspravljati o nekim proširenjima za C++ koja olakšavaju život C programerima i kako C++ donosi paradigmu objektno orijentisanog programiranja u C. Ovaj članak nije sveobuhvatan tretman ove teme; Niti opisujem sve karakteristike C++-a niti objašnjavam svaku nijansu svake karakteristike. Takva pokrivenost bi ispunila knjigu, a knjige i članci na C++-u se pojavljuju sve češće kako jezik dobija na zamahu.

Dr Stroustrup je razvio sistem jezika C++ kao predprocesor koji prevodi C++ kod u C kod, koji zatim kompajlira tradicionalni C kompajler. Noviji sistemi jezika C++ koriste kompajlere izvornog koda koji prevode direktno sa C++ izvornog jezika na maternji jezik ciljne mašine. Ja ću sve sisteme jezika C++ nazivati "kompajlerima", ali ove reference se odnose i na prevodioce za prethodnu obradu.

## C++, poboljšani C

Zaboravite, za trenutak, objektno orijentisano programiranje. Ako nikada ne definišete klasu ili objekat – stvari o kojima ću uskoro razgovarati – i dalje možete imati koristi od poboljšanja koja C++ nudi. Mnoga od ovih poboljšanja su bila dovoljno važna da ih je ANSI Ks3J11 komitet uključio u standardnu specifikaciju jezika C, i možda ih sada poznajete kao ANSI C dodatke, a ne kao nasleđe C++. Među ANSI usvajanjima su prototip funkcije, kvalifikator tipa const i tip void. Postoje i druge C++ karakteristike koje možete da koristite u svom tradicionalnom funkcijsko orijentisanom C programiranju, one koje ANSI ne uključuje, ali su ipak određena poboljšanja. Razgovaraću o nekim od ovih prednosti pre nego što istražim objektno orijentisane aspekte C++-a. Dakle, za sada, pogledajmo C++ kao poboljšani C.

### Obavezni prototipovi

Jedan od doprinosa C++ ANSI C-u bio je novi stil definicije funkcije i blokova deklaracije. ANSI blok definicije naziva "prototipom funkcije". Oba bloka sadrže opise tipova parametara u listi parametara u zagradama.

ANSI C dozvoljava funkcionalne blokove starog i novog stila za zaštitu postojećeg koda, a možete koristiti mešavinu oba stila u ANSI C programu. Sa C++, međutim, stari stil nije podržan. C++ zahteva da kodirate sa novim stilom, a to je velika prednost, jer novi stil nameće jaču proveru tipa parametara i tipova podataka koje funkcije vraćaju.

### Komentari

C++, kao nadskup C-a, prepoznaje standardni C stil komentara koji deli komentare pomoću tokena "/*" i "*/". Ali C++ ima drugi format komentara. Gde god se pojavi // token, sve do kraja reda je komentar.

Pošto C++ prepoznaje oba formata, mnogi programeri koriste novi format za komentare i stari format da privremeno onemoguće blokove koda. Komentari se ne gnezde u C, tako da onemogućavanje koda okružujući ga "/*" i "*/" delimiterima ne funkcioniše ako sam kod koji treba da se onemogući sadrži komentare. U C++, komentari starog stila mogu uključivati novi format komentara u stilu, tako da je format "/*" ... "*/" efikasan način za "komentarisanje" koda. Ako koristite stari format isključivo za ovu svrhu, pronalaženje takvog prokomentarisanog koda je jednostavno korišćenje uslužnog programa grep ili vašeg uređivača da biste pronašli sve instance "/*" tokena.

### Podrazumevani argumenti funkcije

Možete deklarisati podrazumevanu vrednost za argumente u prototipu C++ funkcije na sledeći način:

```cpp
void func(int = 5, double = 1.23);
```

Izrazi deklarišu podrazumevane vrednosti za argumente. C++ kompajler zamenjuje podrazumevane vrednosti ako izostavite argumente kada pozovete funkciju. Funkciju možete pozvati na bilo koji od ovih načina:

```cpp
func(12, 3.45);          // overrides both defaults
func(3);                 // effectively func(3, 1.23);
func( );                 // effectively func(5, 1.23);
```

### Postavljanje deklaracije promenljive

U C-u morate deklarisati sve promenljive na početku bloka u kome imaju opseg. Ne možete mešati deklaraciju promenljive i proceduralne izraze. C++ uklanja to ograničenje, omogućavajući vam da deklarišete promenljivu bilo gde pre nego što je referencirate i čini mogućim izraze poput ovog:

```cpp
for(int ctr = 0; ctr < MAXCTR; ctr++)   // ...
```

Ova funkcija vam omogućava da kodirate deklaraciju promenljive bliže kodu koji je koristi.

### Imena samostalnih struktura

​​U C-u se odnosite na strukture sa ključnom reči `struct` kao prefiksom za ime. U C++, struktura je blisko povezana sa klasom (o kojoj će biti reči kasnije), a njeno ime je slično ključnoj reči sve dok je definicija u opsegu. Možete kodirati sledeće:

```cpp
struct linkedlist {
    /* -- whatever -- */
    linkedlist *previous_node;
    linkedlist *next_node;};
```

Obratite pažnju na to da su dva pokazivača deklarisana bez ključne reči `struct`. Kasnije deklaracije strukture mogu koristiti samo reč "linkedlist", kao što je prikazano ovde:

```cpp
linkedlist mylist;
```

Efekat ovde je kao da ste koristili ovaj `typedef` u C:

```cpp
typedef struct linkedlist linkedlist;
```

`Typedef` ne bi funkcionisao za pokazivače u strukturi jer deklaracija `typedef` nije potpuna kada se pojave deklaracije pokazivača, tako da C++ notacija nudi jasno poboljšanje.

### Umetnute funkcije

Možete reći C++ kompajleru da je funkcija `inline`. Ovo dovodi do toga da se nova kopija kompajlira svaki put kada se pozove. Ugrađena priroda pojedinačnih kopija eliminiše dodatne troškove pozivanja funkcije tradicionalne funkcije. Očigledno bi trebalo da koristite `inline` kvalifikator funkcije samo kada je sama funkcija relativno mala.

### Rezolucija globalnog opsega

U C-u, ako lokalna promenljiva i globalna promenljiva imaju isto ime, sve reference na to ime iz bloka gde je deklarisana lokalna promenljiva odnosiće se na lokalnu promenljivu. Ime lokalne promenljive zamenjuje globalno ime. C++ dodaje operator rezolucije globalnog opsega `::` pomoću kojeg možete eksplicitno referencirati globalnu promenljivu iz opsega gde lokalna promenljiva ima isto ime. Razmotrite kod u Primeru 1.

Primer 1: Globalne promenljive

```cpp
int amount = 123;
main ()
{
    int amount = 456;
    printf("%d", ::amount);
    printf("d", amount);;
}
```

U ovom primeru, izlaz bi bio 123456 jer se prvi `printf` odnosi na skrivenu promenljivu globalnog opsega "amount" na osnovu operatora rezolucije globalnog opsega `::`.

### asm ( string )

C++ nudi, kao standardni način za ugradnju asemblerskog jezika u C++ program, ključnu reč `asm`. Mnogi C kompajleri imaju načine da to urade, ali ne postoji standard. ANSI je zaobišao to pitanje jer zavisi od implementacije. C++ se suočava sa njim direktno i pruža standardni način za prosleđivanje stringa po vašem izboru asembleru. Pod pretpostavkom da svi C++ prevodioci koriste standard, samo sadržaj stringa zavisi od implementacije. Ako stringove definišete zasebno, komponente asemblerskog jezika vaših programa su lakše upravljive.

Naravno, ovo nije savršeno rešenje. Kad god koristite asemblerski jezik, morate biti spremni da se nosite sa njim kada preuzmete port. Samo zato što je asemblerski jezik bio odgovarajući u vašem originalnom programu ne znači da ćete moći da ga koristite u portiranoj verziji. Ali barem C++ pristup pomera sloj zavisnosti od neprenosive implementacije na udaljeniju platformu.

### Besplatna prodavnica

C++ uvodi poboljšanu mogućnost upravljanja heap-om pod nazivom "besplatna prodavnica". Implementiran je sa dva operatora pod nazivom `new` i `delete`. `new` operator je analogan `malloc`-u po tome što dodeljuje memoriju i dodeljuje svoju adresu pokazivaču. Njegov argument je, međutim, opisniji od celobrojne vrednosti koju prosleđujete `malloc`-u. Njegova svrha je da vam omogući da dodelite memoriju za semipermanentnu promenljivu, onu koja ostaje u opsegu izvan bloka u kome je deklarisana. Evo nekoliko primera:

```cpp
char *cp = new char[strlen(whatever)+1];
linkedlist *first_node = new linkedlist;
```

Zapazite da izraz sličan stringu u prvom primeru sadrži promenljivu dimenziju. Drugi primer pretpostavlja strukturu (ili klasu) pod nazivom "linkedlist", kao što je ona ranije korišćena.

Kada ste spremni da uništite promenljivu, koristite operator `delete` na isti način na koji ste koristili funkciju `free` u tradicionalnom C.

```cpp
delete cp;
delete first_node;
```

C++ vam omogućava da zaobiđete operatore `new` i `delete` tako što ćete kodirati sopstvene funkcije za `new` i `delete`. Takođe obezbeđuje globalni pokazivač funkcije koji sistem poziva kada bilo koja nova operacija ne može da dobije potrebnu memoriju. Pozivanjem funkcije `set_new_handler` sa adresom vaše funkcije greške iscrpljivanja heapa-a, možete izdati nove operacije bez testiranja vrednosti pokazivača za NULL povratak svaki put.

### Reference

C++ uključuje izvedeni tip podataka koji se zove `reference`. To je oblik alijasa i čudno će izgledati kao pokazivač za C programera. Evo kako izgleda jednostavna referenca:

```cpp
int sam;
int& george = sam;
```

Operator `&` je "reference-to" govori C++ kompajleru da je "george" pseudonim za sam. Gde god da kažeš "george", mogao si da kažeš "sam". Ako ga koristite samo na taj način, možete koristiti i `#define`, ali jednostavna zamena nije referentna snaga.

Ako promenljivu prosledite funkciji kao argument koja očekuje referencu, kompajler zapravo prosleđuje adresu promenljive. Pozvana funkcija deluje na kopiju promenljive pozivaoca preko njene adrese, a ne na lokalnu kopiju. Ova karakteristika pokazuje svoje obećanje kada se koristi sa strukturama. Primer:

```cpp
void setheight(struct cube& box)
{
    box.height = 5;
}
```

Ova funkcija će promeniti vrednost strukture koju poseduje funkciju koja kada se poziva dobija referencu na vrednost umesto lokalne kopije. Ova procedura eliminiše nepotrebno kopiranje vrednosti podataka napred i nazad između funkcija uz očuvanje notacije lokalne promenljive, koja je jednostavnija i čitljivija od notacije dereferenciranja pokazivača.

Funkcija može da vrati referencu, kao što je prikazano u ovom kodu:

```cpp
int& getwidth( )
{
    /*...*/
    return newwidth;
}
```

Pozivalac funkcije "getwidth" ne mora da zna da pozvana funkcija vraća referencu. Primajuću promenljivu možete kodirati kao referencu ili kao stvarnu promenljivu. U svakom slučaju radi. Neki stručnjaci za C++ veruju da nikada ne treba vraćati referencu. Drugi se ne slažu.

### Preopterećena imena funkcija

U C++-u možete imati nekoliko funkcija sa istim imenom u istom opsegu. Kompajler razlikuje funkcije na osnovu njihovih tipova argumenata. Ova mogućnost, nazvana "preopterećenje", omogućava vam da koristite isto ime za predstavljanje iste generičke operacije na različitim tipovima podataka.

Evo primera prototipova imena preopterećene funkcije:

```cpp
void display(int);          // display an int
void display(char);         // display a char
void display(long);         // display a long
void display(double);       // display a double
```

Ovi prototipovi predstavljaju četiri različite funkcije sa istim imenom. Kada pozovete ime, argument koji navedete govori kompajleru koju od njih želite.

Podrška za preopterećenje je jedan od razloga zašto su C++-u potrebni prototipovi za sve. Kompajler mora da vidi tipove argumenata kako bi mogao da razlikuje funkcije koje imaju isto ime.

### Strukture sa funkcijama

C++ vam omogućava da kodirate funkcije kao članove struktura. Ova praksa vezuje poziv funkcije za instancu strukture. Razmotrite kod u primeru 2.

Primer 2: Strukture sa funkcijama

```cpp
struct cube {
    int length;
    int width;
    int height;
    int volume();
};
```

Definisali ste strukturu koja ima tri cela broja i funkciju. Sada morate da dovršite definiciju strukture deklarisanjem funkcije:

```cpp
int cube::volume(void)
{
    return length * width * height;
}
```

Obratite pažnju na to da deklarišete funkciju kao člana strukture kocke tako što ćete priložiti prefiks "cube::" imenu funkcije. Primetite takođe da funkcija ne mora da imenuje instancu strukture kada se odnosi na jednog od članova strukture. Kompajler zna da će funkcija biti pozvana u ime instance strukture u kojoj je funkcija član, a prevodilac automatski pravi asocijaciju umesto vas.

Zatim deklarišete objekat "cube" i unosite neke vrednosti u dimenzije "cube":

```cpp
struct cube box = {5,4,3};
```

To izgleda kao i tradicionalno u C. Deklarisali ste strukturu tipa "cube" i nazvali je box. Inicijalizovali ste dimenzije strukture. Konačno, pozivate funkciju "volume" tipa "cube" objekta "box" da biste izračunali zapreminu "cube".

```cpp
printf("Volume: %d", box.volume( ));
```

Posmatrajte oblik poziva funkcije "volume". Ime funkcije ima prefiks sa identifikatorom deklarisane strukture u standardnom formatu adresiranja članova strukture C.

Ova mala vežba je kratak pregled klase C++, složenije verzije poboljšane strukture koja se ovde koristi. To je takođe vaš uvod u objektno orijentisano programiranje jer je upravo prikazana struktura apstraktni tip podataka koji uključuje metod, a deklaracija strukture pod nazivom "box" je objekat.

Do sada smo se koncentrisali na aditivne karakteristike C++ koje mogu poboljšati programiranje jezika C. Ali, čineći to, suprotstavili smo se nekim novim idejama objektno orijentisanog programiranja. Hajde sada da razmotrimo šta te i druge nove stvari znače kada se posmatraju sa stanovišta objektno orijentisane programske platforme.

## Objektno orijentisan C++

C++, rekli smo, dodaje objektno orijentisanu programsku paradigmu u jezik C. C++ ima "apstrakciju podataka", "enkapsulaciju", "objekte", "metode", "nasleđivanje" i "polimorfizam". Rečeno nam je da su ovi sastojci ono što otelotvoruje objektno orijentisanu programsku platformu. Svaka paradigma želi svoj jezik, i otkrićete da ovi čudni novi termini imaju neočekivane paralele u vašem C iskustvu, paralele koje, kada se jednom otkriju, olakšavaju razumevanje novih stvari. Pre nego što uđemo u objektno orijentisane detalje C++-a, pokušajmo da uporedimo neke od ovih novih objektno orijentisanih koncepata sa njihovim kolegama u tradicionalnom funkcijsko orijentisanom C-u.

### Apstrakcija podataka, inkapsulacija i objekti

"Apstrakcija podataka" je sposobnost opisivanja novih tipova podataka u smislu njihovog formata i procesa koji deluju na njih. "Enkapsulacija" je proces kojim kombinujete komponente podataka i funkcionalne delove apstraktnog tipa podataka u jednu inkapsuliranu definiciju. Apstraktni tip podataka, nazvan `class` u C++, je stoga kolekcija drugih stavki podataka i funkcija. Stavke podataka opisuju format nove klase, a funkcije opisuju kako se ona ponaša. Instanca apstraktnog tipa podataka naziva se `object`.

C ima svoje ugrađene tipove podataka. U C-u koristite `char`, `int`, `float` i `double` i kvalifikovane varijacije. Kada deklarišete jedan od ovih, deklarišete instancu tipa ili objekta. Programeri vašeg C kompajlera su inkapsulirali ove tipove podataka u kompajler tako što su opisali njihove formate i uključili metode koje deluju na njih. Kada kažeš:

```cpp
int answer = 10 + variable_integer;
```

deklarisali ste `int` objekat pod nazivom "ansver" i pozvali `int` metod kompajlera C koji sabira vrednosti iz dva celobrojna objekta tipa podataka i smešta rezultat u objekat odgovora. `Int` nije apstraktni tip podataka jer je ugrađen u kompajler, ali je ipak objekat.

U objektno orijentisanom programiranju, vi u rečnik jezika dodajete tipove podataka sa apstraktnim tipovima podataka koje ste inkapsulirali vi i, možda, programeri biblioteka tipova podataka treće strane. Dakle, pored `int` i `float`-a, možete imati "string", "blivot", "Bach_two_part_invention" i šta god još zamislite kao novi tip podataka.

**C paralele sa apstraktnim tipovima podataka**  
Najbliže stvari apstraktnim tipovima podataka u tradicionalnom C-u su `typedef` i `struct`, iako im nedostaju neki od drugih kvaliteta objektno orijentisanih tipova podataka. Ako definišete strukturu u C-u i dodelite joj `typedef name`, inkapsulirali ste apstraktni tip podataka. Enkapsulaciju završavate pisanjem C funkcija koje deluju na strukture imenovanog `typedef`-a. `stdio.h` definicija tipa FILE je `typedef`, kada se kombinuje sa funkcijama `fopen`, `fclose`, `fgets` i tako dalje, Labava enkapsulacija apstraktnog tipa podataka FILE koji upravlja ulazom/izlazom toka. Međutim, inkapsulacija nije bezbedna i tu dolazi na scenu objektno orijentisano programiranje.

### Metode

Funkcije definisane za apstraktni tip podataka nazivamo njegovim `methods`. U objektno orijentisanom programiranju, stvari se dešavaju slanjem poruka objektima. U inkapsulaciji je neko definisao metode koje deluju na poruke koje šaljete objektu.

**C paralele sa metodama**  
Postoji vrlo mala razlika između objektno orijentisanog pojma slanja poruke objektu i tradicionalnog C pojma pozivanja funkcija. Važna pojedinačna razlika je u tome što su u objektno orijentisanom programiranju poruka i metod čvrsto vezani enkapsulacijom za deklarisani objekat, instancu apstraktnog tipa podataka. Enkapsulacija definiše vezivanje, koje se dešava kada deklarišete objekat.

### Nasleđivanje

Nakon što ste definisali apstraktni tip podataka, možete definisati druge koji nasleđuju njegove atribute. Ova karakteristika se naziva `inheritance`. Jedna prednost je što se sav posao koji je ušao u definiciju osnovnog tipa spušta na izvedeni tip. Drugi je da vas podstiče da razmišljate i dizajnirate svoj sistem podataka na struktuiran način.

**C paralele sa nasleđivanjem**
U C-u, struktura koja ima drugu strukturu kao element efektivno nasleđuje svojstva strukture elementa i dodaje joj svoje jedinstvene elemente.

C niz je izvedeni tip. Opišete strukturu, a zatim opišete niz tih struktura. Struktura je osnovni tip. Niz je izvedeni tip, koji nasleđuje karakteristike strukture. Izvedene klase u C++ su, međutim, moćnije od jednostavnog dodavanja dimenzije osnovnom tipu.

Drugi izvedeni tipovi u C-u su pokazivači, koji su izvedeni direktno iz osnovnih tipova na koje ukazuju; konstante, koje su izvedene iz tipova koje im je dodelio kompajler; i strukture, koje su rani primeri izvedenih tipova sa višestrukim nasleđivanjem po tome što su izvedeni iz različitih tipova njihovih višestrukih elemenata.

### Nadjačavanje funkcije i polimorfizam

Nadjačavanje funkcije je mogućnost za tip ili metod u izvedenom tipu da zameni slično definisan tip ili metod u svom osnovnom tipu. Različiti tipovi gore i dole u ​​hijerarhiji tipova mogu definisati tipove članova i metode prema njihovim individualnim potrebama. Programer koji koristi apstraktni tip podataka ne mora da zna koji metod će obraditi poruku koja se šalje ili u kom apstraktnom tipu se pojavljuje navedeni tip člana.

Ako imate osnovni tip podataka i izvedeni tip podataka, šaljete poruke objektu izvedenog tipa podataka da biste ga naterali da uradi ono što želite. Pošto je izvedeni tip nasledio atribute baze, možete slati poruke koje su definisane kao da pripadaju osnovnom tipu podataka, a izvedeni tip će ih prihvatiti i koristiti metode baze da deluje na njih.

Pomoću nadjačavanja funkcije možete definisati tip člana ili metod u izvedenom tipu podataka koji liči na onaj u svojoj bazi. Pošaljite poruku preko tog metoda ili se pozovite na taj tip člana, a koristiće se onaj koji je definisan u izvedenom tipu. Neće svi izvedeni tipovi baze duplirati original. Ako pošaljete poruku objektu na niže hijerarhijskoj lestvici, kompajler će izabrati metod iz prvog tipa višeg u hijerarhiji gde je metod definisan.

Ako, međutim, adresirate izvedeni tip preko osnovnog tipa, možda pomoću pokazivača osnovnog tipa koji sadrži adresu izvedenog tipa, onda se osnovna funkcija koristi i ne dolazi do preglasavanja. Kada ne želite da se to dogodi, kada želite da se izvedena funkcija za nadjačavanje koristi bez obzira na to kako se obraćate tipu, onda morate pozvati "polimorfizam".

Polimorfizam je nijansa nadjačavanja funkcije. Ako deklarišete osnovnu funkciju kao `virtuel` funkciju, onda će je uvek zameniti funkcije sa istim imenom i karakteristikama u izvedenim tipovima.

**C Paralele u nadjačavanju funkcija**
Tradicionalni C koristi oblik nadjačavanja funkcije u svojim aritmetičkim operacijama. Ako posmatrate cele brojeve, brojeve sa pokretnim zarezom i pokazivače kao hijerarhiju aritmetičkih tipova, različiti načini na koje im dodajete, na primer, mogu se smatrati sličnim metodama u modelu podataka, metodama koje se pozivaju na osnovu tipova podataka, a ne operatora.

Čini se da u C nema paralele sa objektno orijentisanim konceptom polimorfizma.

Ovo su, dakle, osnove objektno orijentisanog programiranja, a mi smo razmotrili kako biste mogli da povežete svoje prakse programiranja orijentisanog na funkcije sa njima. Neke od naših analogija protežu tačku do krajnjih granica, ali njihova svrha je da vas uvere da je paradigma koja se zove "objektno orijentisano programiranje" u velikoj meri drugačiji način gledanja na ono što ste radili sve vreme.

Pogledajmo sada neke detalje o C++-u i vidimo kako se ti principi primenjuju.

## C++ klase

Osnovna jedinica enkapsulacije u C++ je klasa. Ono što smo ranije nazivali apstraktnim tipom podataka, sada ćemo nazvati "klasom" jer je to ista stvar. C++ koristi ključnu reč `class` da bi opisao verziju tipa podataka koji je definisao programer. Kada inkapsulirate apstraktni tip podataka, definišete klasu. Kada deklarišete objekat, deklarišete instancu klase. Primer 3 ilustruje kako izgleda definicija klase.

Primer 3: Definicija klase

```cpp
class date {
    int day;
    int month;
    int year;
  
  public:
    date (void)
      {day = month = year = 0;}
    date (int da, int mo, int yr);
    ~date() { /*null destructor*/ }
    void display (void);
};
```

Ova definicija opisuje klasu pod nazivom "date". Podseća na strukturu po tome što ima članove koji se sastoje od promenljivih podataka i funkcija. Članovi pre ključne reči `public` su privatni delovi. Ostalo je javno. Razlika između klase i strukture prikazane ranije je u tome što su svi članovi strukture vidljivi svim delovima vašeg programa, dok funkcije vašeg programa mogu videti samo `public` članove klase. Privatne delove klase mogu čitati i menjati samo funkcije koje su i same članovi klase. (Za izuzetak, pogledajte diskusiju o prijateljima.)

Obično su privatni delovi promenljive, a javni delovi funkcije. Ništa ne kaže da to mora biti tako, ali izgleda da je to dobra konvencija koju treba pratiti i radi za većinu definicija klasa.

Pored `public` ključne reči, možete da izjavite da su neki članovi `private`, a drugi `protected`.

- Članovi u klasi su podrazumevano privatni, kao i naši članovi za dan, mesec i godinu.
- Članovi u strukturi su podrazumevano javni osim ako ih ne proglasite privatnim ili zaštićenim.

Kontrola pristupa člana pokazuje koje vrste funkcija mogu pristupiti članovima.

- Privatnom članu mogu pristupiti samo funkcije članova i prijatelji.
- Javnim članovima može pristupiti bilo koja funkcija koja deklariše objekat klase ili ima strukturu u opsegu.
- Zaštićeni članovi klasa su privatni osim što funkcije članova i prijatelji izvedenih klasa mogu da im pristupe.

Uskoro ćemo razgovarati o funkcijama članova, prijateljima i izvedenim klasama.

Enkapsulacija naše klase date nije završena jer sve metode još nisu tu. Zapamtite, metode su funkcije.

Klase obično imaju najmanje dve funkcije člana, a često i više. Uobičajene dve su funkcija člana `constructor` i funkcija člana `destructor`, iako nije potrebno da ih definišete. Druge su metode.

### Konstruktori i destruktori

Kada deklarišete objekat kao instancu klase, to radite na isti način na koji deklarišete bilo koji drugi tip podataka. Uporedite deklaraciju ovog celog broja i datuma:

```cpp
int days_on_board; 
date date_hired;
```

Izgledaju isto, a programeru koji ih koristi jesu. Ali kada dizajnirate klasu, obično obezbeđujete jednu funkciju konstruktora i jednu funkciju destruktora. Funkcija konstruktora se izvršava kada deklarišete objekat, a funkcija destruktora se izvršava kada objekat izađe van opsega. Ako izostavite ove funkcije, onda se neće desiti posebna obrada kada objekat uđe i izađe iz opsega.

Funkcija konstruktora ima isto ime kao i sama klasa i nema povratnu vrednost. U gornjem primeru datuma, definisali smo dve funkcije konstruktora, obe nazvane "date". Oni se razlikuju po različitim tipovima argumenata; prvi konstruktor nema argumente, a drugi konstruktor ima tri celobrojna argumenta. Ovo su preopterećene funkcije konstruktora jer imaju isto ime i različite parametre.

Obratite pažnju na to da prva definicija konstruktora nije završena tačkom i zarezom, već ima blok koda okružen zagradama koji sledi iza nje. Ovaj format je verzija `inline` funkcije funkcije člana. Uključivanjem koda sa definicijom na ovaj način, gradite `inline` funkciju, u ovom slučaju onu koja samo inicijalizuje tri člana promenljive datuma na nulu. Ova funkcija konstruktora se izvršava kada deklarišete objekat datuma bez vrednosti inicijalizacije kao što je prikazano ovde:

```cpp
date date_hired;
```

Druga funkcija konstruktora nije `inline` funkcija (iako bi mogla biti), tako da morate negde navesti njen kod. Funkcija bi mogla izgledati ovako u primeru 4.

Primer 4: Konstruktor funkcija

```cpp
date:: date (int da, int mo, int yr)
{
    day = da;
    month = mo;
    year = yr;
}
```

Obratite pažnju na to da deklaracija funkcije ima prefiks `date::` da biste je povezali sa klasom "date". Obratite pažnju i na to da funkcija ima slobodan pristup privatnim članovima klase. Ovaj konstruktor se izvršava kada deklarišete objekat datuma sa tri celobrojne vrednosti kao što je prikazano ovde:

```cpp
date date_retired(25, 5, 88);
```

Funkcija destruktora ima isto ime kao i klasa, ali sa prefiksom tilde ( `~` ). U klasi primera "date" koja se ovde koristi, funkcija destruktora ne radi ništa, tako da je kodirana kao `null` `inline` funkcija. Složenije klase će zahtevati da se stvari urade kada objekat izađe van opsega. Možda je potrebno izbrisati neku slobodnu memoriju, na primer, a funkcija destruktora bi se pobrinula za to.

### Funkcije članovi

Pored konstruktora i destruktora, klasa može imati i druge funkcije članove. Ovo su metode klase. U našem primeru "date" klase prikazujemo funkciju člana pod nazivom "display“. Da bismo koristili ovaj metod, moramo ga kodirati ovako:

```cpp
void date::display(void) { printf("%d/%d/%d", month, day, year); }
```

(Iskusni C++ programeri bi se mogli zapitati zašto koristim staromodni `printf` kada je C++ `stream` mogućnost dostupna dostupna. Pošto još nisam opisao `stream` klase, njihova upotreba bi zbunila one koji nisu upoznati sa njima. O `stream`-ovima ću govoriti kasnije.)

Program koji deklariše objekat "date" onda može da koristi funkciju člana koja se odnosi na klasu date ovako:

```cpp
date_hired.display( );
```

U govoru objektno orijentisanog programiranja, poslali smo poruku "date_hired" objektu da mu kažemo da koristi svoj metod "display". Mnogo liči na tradicionalni poziv funkcije, zar ne?

### Prijatelji

Privatni članovi klase su vidljivi samo funkcijama članovima klase. Funkcije `constructor`, `destructora` i `display` u klasi `date` mogu čitati i pisati cele brojeve meseca, dana i godine, ali nijedna spoljna funkcija nema taj pristup. S vremena na vreme ćete naći potrebu da obezbedite spoljni pristup unutrašnjosti nekog od vaših časova. Imenovana funkcija ili klasa može biti `friend` klase koja se definiše. Dodeljivanje statusa prijatelja možete izvršiti samo iz definicije klase koja daje pristup kao što je prikazano u Primeru 5.

Primer 5: Dodeljivanje statusa prijatelja

```cpp
class time;
class date {
  // ...
  friend void shownow (date&, time&);
};

class time {
  // ...
  friend void shownow (date&, time&);
};
```

Ovo su dve klase sa imenom "date" i "time" (sa izostavljenim detaljima). Potrebna nam je dodatna deklaracija prikaza vremena jer izjava prijatelja u klasi datuma ima referencu na nju. Dve klase dele funkciju prijatelja pod nazivom "shownow", koja može prikazati trenutni datum i vreme ovako:

```cpp
void shownow (date& d, time& t)
{
  printf("\n%d/%d/%d", d.day, d.month, d.year);
  printf("\n%d:%d:%d", t.hr, t.min, t.sec);
}
```

Pošto je funkcija "shownow" prijatelj obe klase, ona može da čita privatne članove obe.

Klasa može imati drugu klasu kao prijatelja sa ovom naredbom:

```cpp
class date {// ... friend class time;};
```

### Preopterećenje operatora

Preopterećenje operatera je jedan od urednijih trikova koje možete da uradite sa C++. To je ono što jezik čini tako proširivim. Već ste videli kako možete da dodate tipove podataka definisanjem klasa. Sledeće, možda ćete želeti da izvršite aritmetičke, relacione i druge operacije na svojim klasama na isti način kao što to radite sa `int` i `float` promenljivim i slično. Napravili smo jednostavnu klasu "date". Razmotrite primer 6.

Primer 6: Jednostavna klasa date

```cpp
date retirement_date, today;
    // ...
if (retirement_date < today)
    // Keep working ...

Šta kažeš na ovo?

date date_married, today;
    // ...
if (date_married + 365 == today)
    // Happy Anniversary ...
```

Oba ova oblika su moguća sa C++. Možete da napravite funkcije člana klase koje kompajler povezuje sa C operatorima. Ova funkcija se naziva "preopterećenje operatera". Kada koristite operativni izraz kao što su upravo prikazani, pozivaju se funkcije člana koje preopterećuje vaš operator. Evo primera:

```cpp
class date {
    // ...
    int operator<(date&);
}
```

Definicija klase "date" kaže da funkcija člana `operator<` preopterećuje operator manje od. Kada kodirate ovaj izraz:

```cpp
(retirement_date < today)
```

funkcija koja povezuje operator manje klase date sa drugom klasom datuma kao argument se poziva. Vraća tačan ili netačan ceo broj. Funkcija člana `operator<` može izgledati ovako u primeru 7.

Primer 7: Funkcija člana operator<

```cpp
int date::operator<(date& dt)
{
    if (year < dt.year)
        return TRUE;
    if (year == dt.year)  {
        if (month < dt.month)
            return TRUE;
        if (month == dt.month)
            if (day < dt.day)
                return TRUE;
    }
    return FALSE;
}
```

Funkcija se odnosi na klasu na levoj strani izraza tako što imenuje njene privatne delove bez kvalifikacija (dan, mesec, godina). Odnosi se na privatne delove argumenta putem referentne varijable (dt.day, dt.month, dt.year).

Na ovaj način možete preopteretiti bilo koji C operator. Ne možete da kreirate sopstvene operatore kao što je ")(" ili bilo šta u čemu bi se kompajler ugušio, i ne smete da koristite preopterećene operatore na načine koje kompajler ne može da raščlani, kao što je A [ B. Možete, međutim, da koristite ovu funkciju da napravite neki zaista zbunjujući kod. Možete, na primer, da koristite operator + da logički oduzmete dve klase. Pokušajte da se klonite takvih radnji.

Preopterećenje operatera je moćna karakteristika. Možete preopteretiti operator niza [] da biste kreirali sopstvenu obradu niza. Možete preopteretiti ( ) operator poziva funkcije da bi klasa izgledala kao poziv funkcije. I, naravno, možete imati nekoliko različitih funkcija koje preopterećuju isti operator različitim tipovima argumenata. Možda ćete želeti zasebne funkcije za dodavanje plutajućih i dugih u vašu klasu, na primer.

### Pokazivač this

Svaka funkcija člana ima ugrađeni pokazivač pod nazivom `this`. Ukazuje na objekat koji obrađuje funkcija člana. Kada funkcija-član želi da napravi kopiju objekta ili da ga vrati nakon što ga možda modifikuje, funkcija-član može referencirati objekat kao `*this`.

Da bismo ilustrovali upotrebu pokazivača `this`, pogledajmo još jednom preopterećeni operator sabiranja. Naš manje nego preopterećeni operator je vratio ceo broj, ali aritmetički izraz treba da vrati kopiju klase. Razmotrite primer koji smo videli ranije:

```cpp
if (date_married + 365 == today) //
    Happy Anniversary ...
```

Postoje dve preopterećene operacije koje implicira ovaj izraz. Jedan je operator jednakosti ==, koji bi mnogo ličio na operator manje od koji smo već napravili. Drugi je operator sabiranja. Evo ih u definiciji klase.

```cpp
class date {
    // ...
    date& operator+(int);
    int operator==(date &);
}
```

Preopterećeni operator + mora da vrati klasu koja kao vrednost ima rezultat sabiranja. Ne dodaje ceo broj klasi pozvanoj u izrazu. On vrši sabiranje i vraća rezultat, koji je i sam klasa "date". Bez složene aritmetike datuma koja proverava prekoračenje meseca, prestupne godine i sve to, evo kako izgleda funkcija preopterećena za "+" operator.

```cpp
date& date::operator+(int n)
{
    date dt;
    dt = *this;
    //add n to dt.month,dt.day,dt.year
    // ...
    return dt;
}
```

Videćete da kopiramo objekat u koji se dodaje u privremeni datum pod nazivom dt. Ovaj pokazivač nam pruža način da to uradimo.

Ako želite da preopterećujete operator +=, funkcija bi izgledala ovako:

```cpp
date& date::operator+=(int n)
{
    //add n to month,day,year
    // ...
    return *this;
}
```

### Dodela klase

Zapazite u preopterećenom + operatoru iznad da je objekat na koji ukazuje ovo dodeljen objektu po imenu dt. C++ uključuje ugrađeni operator dodeljivanja za svaku klasu koju definišete. Jednostavno kopira sve članove iz jednog objekta u drugi na način na koji dodeljivanje strukture funkcioniše u C. Možete preopteretiti operator dodeljivanja ako je potrebno. To biste uradili ako biste želeli da dodelite nešto drugo osim drugog objekta iste klase. Razmotrite ovo, na primer:

```cpp
#include <time.h>
// ...
date_hired = time(NULL);
```

### Streaming

U dosadašnjim primerima koristili smo `printf` za prikaz podataka. C++ uključuje ulazno/izlazne klase toka ( stream ) koje su već definisane u "stream.h". Imaju nekoliko prednosti u odnosu na tradicionalni `printf/scanf` par. Da biste nešto napisali na konzoli, kažete:

```cpp
cout << "Hello, Dolly";
```

Datoteka "stream.h" ne uključuje samo klase `ostream` i `istream`, već uključuje i eksterne deklaracije standardnih objekata, `cout` i `cin`, koji su dodeljeni konzoli. Upravo dati primer pokazuje kako je klasa `ostream` preopteretila operator `<<`. Ovo nije operacija pomeranja bitova u ovoj upotrebi. Oznaka treba da predstavlja pravac toka podataka. Operator `<<` je preopterećen nekoliko puta kako bi vam omogućio da šaljete različite tipove podataka na konzolu bez brige o njihovom formatu. Evo primera:

```cpp
cout << "Blossom";
cout <<' \ n';
cout << 123;
```

Preopterećene operatorske funkcije vraćaju reference na objekat koji se obrađuje, tako da možete napisati gornji primer na ovaj način:

```cpp
cout << "Blossom" <<' \ n'<< 123;
```

Klasa `istream` radi na drugi način sa `>>` kao operatorom koji označava podatke koji teku od objekta do promenljive argumenta kao što je prikazano ovde:

```cpp
void main( )
{
    char mystring[80];
    // ...
    cin >> mystring;
}
```

Možete deklarisati tokove i povezati ih sa baferima datoteka. Postoji nekoliko standardnih metoda koje podržavaju "ulaz/izlaz" toka datoteka uključenih u "stream.h".

### Funkcije konverzije

Funkcije konverzije vam omogućavaju da obezbedite konverziju klase u drugi tip, bilo standardni C tip podataka ili drugu klasu kao što je prikazano ovde:

```cpp
class date {
    // ..
    operator long( );
};
```

Napisali biste preopterećenu funkciju kao što je ona prikazana u Primeru 8 i možete pozvati funkciju na jedan od nekoliko načina kao što je prikazano u Primeru 9.

Primer 8: Preopterećenje funkcije konverzije

```cpp
long date::operator long () (void)
{
    long days_since_creation;
    // compute the number of days...
    // ...
    return days_since_creation;
}
```

Primer 9: Načini pozivanja funkcije navedene u Primeru 8

```cpp
void main ()
{
    date today;
    long eversince;
    // ...
    eversince = (long) today;
    eversince = today;
    eversince = long (today);
}
```

Notacija koju odaberete zavisiće od toga kako koristite konverziju. Prvi format izgleda kao `cast`, a drugi podrazumeva da se odvija normalna konverzija tipa. Možete koristiti poslednji format ako želite da vas kod podseti da pozivate funkciju konverzije klase.

Ovaj primer pokazuje kako konvertujete klasu u standardni C ili C++ tip podataka. Možete koristiti istu tehniku za pravljenje funkcija konverzije klase u klasu, ali neki programeri radije koriste specifične funkcije konstruktora da bi izvršili konverzije klasa u druge klase. Kada ciljna klasa dođe u opseg, funkcija konverzije konstruktora dobija vrednosti podataka iz objekta koji je njen parametar. na primer:

```cpp
date today(25, 12, 89);
// ...
Julian julian_today(today); // a class named Julian
```

### Nasledjivanje

Nasleđivanje je vitalni sastojak objektno orijentisanog programiranja. Mnogi C programeri će koristiti napredne funkcije C++ klasa da dodaju tipove podataka i nikada se neće baviti hijerarhijama klasa. Drugi će istraživati mračne dubine nasleđa i izgradiće ogromne, egzotične klasne sisteme, čineći svaku novu klasu novom bora na starom. Negde između je verovatni idealno.

Klasa u C++ se može definisati kao derivat druge klase. S obzirom na generičku klasu date koju smo do sada koristili, možda će nam trebati određeniji datum, onaj koji ima sva svojstva drugih datuma, ali koji ima nekoliko sopstvenih jedinstvenih. Evo kako biste kodirali izvedenu klasu:

```cpp
class workday: date
{
    int shift;
    public:
    workday(int da, int mo, int yr, int sh);
    // ...
};
```

Definisali smo klasu pod nazivom "workday" koja je izvedena iz klase pod nazivom "date". Klasa "workday" se naziva "izvedena" klasa, a klasa "date" se naziva "osnovna" klasa. Izvedena klasa "workday" ima svog privatnog člana, promenljivu "shift". Ono što ne vidite je da klasa takođe ima promenljive po imenu "day", "month" i "year" jer je izvedena iz klase "date" koja ima te varijable, i tako funkcioniše nasleđivanje. Funkcija konstruktora klase "workday" ima promenljivu "shift", jednu promenljivu više od odgovarajućeg konstruktora date. Evo funkcije konstruktora:

```cpp
workday::workday(int da,int mo,int yr,int sh)
    :date(da, mo, yr)
{
    shift = sh;
}
```

Izraz koji sledi dvotačku posle liste argumenata pokazuje šta će funkcija konstruktora "workday" preneti funkciji konstruktora date. Vrednosti ne moraju biti uzete sa liste argumenata kao što smo uradili ovde, to mogu biti bilo koji važeći izrazi koji odgovaraju tipovima argumenata funkcije konstruktora osnovne klase.

Funkcije članova u izvedenoj klasi mogu čitati i pisati javne članove osnovne klase ako izvedena klasa nije ponovo koristila ime člana kao što je prikazano u primeru 10.

Primer 10: Funkcija članica

```cpp
class workday : date {

  public:
    // ...
    int isXmas (void);
};

int workday;;isXmas (void)
{
    return month == 12 && day == 25;
}
```

Izvedena klasa može ponovo da koristi ime člana koje se koristi u bazi. Ovo je nadjačavanje funkcije. Ako je izvedena klasa ponovo koristila ime osnovnog člana, sve nekvalifikovane reference na to ime će ukazivati na člana u izvedenoj klasi. Ali ako član želi da pristupi članu osnovne klase, izvedena funkcija člana kvalifikuje ime kao što je ovo:

```cpp
x = date:: month;
```

Funkcije koje nisu članice koje deklarišu objekte izvedene klase ne mogu pristupiti javnim članovima osnovne klase osim ako `public` ključna reč ne kvalifikuje nasleđe kao što je prikazano u Primeru 11.

Primer 11: Funkcije koje nisu članice

```cpp
class workday : public date {
    // ...
};
void main ()
{
    workday proj_dt;
    // ...
    int yr_comp = proj_dt.year;
}
```

Izvedena klasa ne može pristupiti privatnim delovima svoje baze osim ako je baza ne proglasi kao prijatelja. Ako dodajete novu klasu u hijerarhiju klasa i otkrijete da izvedena klasa treba da dođe do privatnog člana osnovne klase negde gore, moraćete da preturate po definicijama klasa da biste pronašli i izmenili osnovnu klasu. Morate ili deklarisati izvedenu klasu (ili jednu od njenih funkcija) kao prijatelja baze ili premestiti željeni element u javni deo osnovne klase.

### Višestruko nasleđe

Objektno orijentisani svet tradicionalno opisuje svoj tip u onome što nazivaju "hijerarhijom klasa". Ovo je često pogrešan naziv. U objektno orijentisanom dizajnu, osnovni tip može imati mnogo izvedenih tipova. Ako biste tu stali, imali biste hijerarhiju; u pravoj hijerarhiji svaki niži element je podređen samo jednom nadređenom. Verzije C++ pre verzije 2.0, koja je najnovija, pridržavale su se hijerarhijskog modela u smislu da izvedena C++ klasa može imati samo jednu osnovnu klasu. U verziji 2.0, kao i u mnogim drugim objektno orijentisanim jezicima, izvedena klasa može imati više osnovnih klasa. Ovaj model se naziva "višestruko nasleđivanje" i to nije hijerarhija – to je mreža. Programeri vole da mešaju, zbunjuju i zloupotrebljavaju metafore. Nema sumnje da je metafora "hijerarhije tipova" predodređena da ostane sa nama iako zloupotrebljava osnovu iz koje potiče.

C++ klasa može da nasledi atribute više osnovnih klasa kao što je prikazano u primeru 12.

Primer 12: Nasleđivanje atributa više osnovnih klasa

```cpp
class date {
    // ...
};
class time {
    // ...
};
class datetime : date, time {
    // ...
  public:
    detetime (int da,int mo,int yr, int hr,int min, int sec);
};
```

Definisali smo izvedenu klasu pod nazivom "datetime" koja nasleđuje atribute dve osnovne klase pod nazivom "date" i "time". Konstruktor za klasu "datetime" bi izgledao ovako:

```cpp
datetime:: datetime(int da, int mo, int yr, int hr, int min, int sec)
   : date (da, mo, yr), time (hr, min, sec)
{
    // ...
}
```

Postoji mnogo stvari koje treba uzeti u obzir kada gradite mrežu klasa sa višestrukim nasleđivanjem. Morate biti svesni redosleda pozivanja konstruktora baze i morate se čuvati dvosmislenosti kada upućujete na članove izvedenih i baznih klasa. Što je porodično stablo više, to je teže pratiti šta je povezano sa dalekim, nevidljivim rođacima, precima i prijateljima.

### Virtuelne funkcije

C++ koristi kvalifikator `virtual` funkcije da deklariše funkciju članicu osnovne klase kao onu koja je uvek nadjačana od strane izvedene klase bez obzira na to kako se izvedena klasa adresira. Ako deklarišete funkciju u izvedenoj klasi sa istim imenom i tipom argumenata, onda će svaka referenca na tu funkciju – čak i kroz referencu na bazu – pozvati kopiju virtuelne funkcije izvedene klase. Primer 13 ilustruje neke od ovih koncepata.

Primer 13: Primeri C++ virtuelnih funkcija

```cpp
class date {
    // ...
  public:
    virtual int isXmas (void);
};

class workday : date {

  public:
    int isXmas (void);
};

void main ()
{
    date dt;
    workday wd;
    date = dat = &dt;
    // ...
    wd.isXmas ();           // workday::isXmas
    dt.isXmas();            // date::isXmas
    dat->isXmas();          // workday::isXmas (!)
    wd.date::isXmas ();     // date::isXmas
}
```

Ova sposobnost redefinisanja metoda gore-dole u ​​mreži klasa je ono što definicijama C++ klase daje njihove polimorfne karakteristike.

### Buduća uputstva

Neke od funkcija o kojima sam govorio u ovom članku su nove za C++ 2.0. Ali jezik nije završio rast. Na primer, dr Stroustrup radi na tome da doda parametrizovane tipove podataka i rukovanje izuzetcima kao suštinske delove jezika. A ANSI će uskoro postaviti komisiju čiji će zadatak biti da napiše standardni opis C++.

Sa Microsoft i Borlandom koji se kreću u pravcu objektno orijentisanog jezika i sa glasinama da će i jedni i drugi uvesti C++ kompajlere 90-ih, budućnost C++-a izgleda sigurno. S obzirom na velika poboljšanja koja C++ donosi u jeziku C, možemo sa sigurnošću predvideti da će C++ na kraju zameniti C kao jezik izbora.
