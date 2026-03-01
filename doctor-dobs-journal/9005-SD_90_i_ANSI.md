
# C PROGRAMMING - 9005

## SD '90 i ANSI konačno - Al Stivens

### Panel uporednog jezika

Varren Keuffel iz Miller-Freeman-a pozvao me je da predstavljam C i C++ na ovogodišnjem panelu uporednih jezika na SD '90. Svaki od članova panela je napisao program prema specifikaciji koju je propisao Voren, a zatim su koristili svoje programe da bi naglasili prednosti svojih jezika. Evo specifikacije.

„Literate za unosan ugovor sa MegaBucks Corporation, razvijajući sav njihov softver. Kao deo procesa nadmetanja, MegaBucks je od vas tražio da napišete mali program koji demonstrira prednosti vaših alata. Program, provera kilometraže gasa za predsednika MegaBucks-a, prihvata kao (minimalni) unos datuma, broj isporučenih galona, a što znači isplaćenu cenu gasa. prikazati ili odštampati izveštaj koji će impresionirati rukovodioce MegaBucks-a."

Jedan od govornika na SD '90 bio je Ken Orr koji je napisao i objavio knjigu 1981. godine pod nazivom Definicija strukturiranih zahteva. Knjiga govori o teoriji dizajna orijentisanog na rezultate, koja kaže, „odredite izlaze i radite unazad“. Vorenova specifikacija za panel prenosi tu ideju na novi nivo. Ovo je moj prvi dizajn koji počinje bilo kakvim značajnim rezultatom koji će impresionirati klijenta.

Vorenova svrha je, naravno, bila da nam da dovoljno slobode da koristimo naše jezike u najbolju korist. Odlučio sam da se odreknem fensi grafičkih izlaza jer takve biblioteke, iako dostupne C i C++ programerima, nisu suštinski delovi samih jezika. Umesto toga, koristio sam jednostavne standardne ulazne i izlazne funkcije iz oba jezika i napisao programe za filtriranje koji čitaju ulaznu datoteku i pišu izveštaj.

Svaki od nas je imao 10 minuta za naše prezentacije. Voren je seo ispred i zurio između nas i svog sata. Osećao sam se kao čovek koji brzo priča u TV reklamama. Iako sam učio dva jezika, Voren mi nije dao 20 minuta. Mora da je zato što radi za taj drugi časopis.

Slika 1 prikazuje izveštaj. Izveštaj o putovanju prikazuje putovanja automobilima koji prikazuju datum, pređene milje, kupljeni benzin, cenu, milje po galonu i cenu po milji. Slika 2 je ulaz. Pretpostavio sam da sistem ima proces unosa podataka, validacije i sortiranja i da će unos biti tačan i u nizu, pa sam napravio test ulaznu datoteku kao što je prikazano na slici 2. Prva tri reda su opis prvog automobila sa brojem licence, godinom modela i markom. Zatim dolaze zapisi o putovanju u pet redova sa datumom, ulaznim kilometražom, izlazom, kupljenim galonima i potrošenim novcem. Evidencija putovanja za vozilo se završava zapisom „kraj“. Datoteka je takođe prekinuta.

Slika 1: Izveštaj o kilometraži gasa

```sh
  1987 Corvette License # JXA283

    Date    miles  gas   cost    mpg   cost/mile
------------------------------------------------

   2-01-90    550    40   45.00    13      0.08
   2-09-90    450    30   32.50    15      0.07
  totals     1000    70   77.50    14      0.08

  1989 Dodge License # HMS001

    Date    miles  gas   cost    mpg   cost/mile
------------------------------------------------

   2-02-90    250    10   11.25    25      0.05
   2-04-90     10     0    0.00     0      0.00
   2-04-90    122     7    7.00    17      0.06
  totals      382    17   18.25    22      0.05
```

Figure 2: Report input

```sh
  JXA283
  1987
  Corvette

  02/01/90
  10550
  10000
  40
  45.00

  02/09/90
  11000
  10550
  30
  32.50
  end

  HMS001
  1989
  Dodge

  02/02/90
  5450
  5200
  10
  11.25

  02/04/90
  5460
  5450
  0
  0

  02/04/90
  5582
  5460
  7
  7
  end
  end
```

U ovom trenutku sam doneo, možda, slučajnu sumnjivu istorijsku odluku. Odlučio sam da prvo napišem C++ program. Nakon što sam završio C++ program, preneo sam ga na C. Ovo je možda jedini put da je neko to uradio. Uradio sam to jer sam bio previše lenj da ponovo počnem od nule. Svi delovi su bili na mestu u C++ programu, i sve što je trebalo da uradim je da ih pomerim.

Ova obrnuta vežba inercije mi je dala priliku da posmatram u kojoj meri C++ tretman, sa svojim skoro objektno orijentisanim ukusom, utiče na izgled C programa. Da li bi pristup C izgledao drugačije nego da sam počeo od nule? Zaključci koje sam izveo su dvoslojni. Program ne izgleda bitno drugačije od drugih C programa sličnog obima. Razlika je u tome kako ja razmišljam o programu. Dizajnirao sam C++ program počevši od objekata podataka i građenja klasa. Dok sam razmišljao o algoritmima koji su potrebni jednoj klasi, smatrao sam ih metodama klase, funkcijama koje su vezane za klasu i koje se ne izvršavaju ni u jednom drugom kontekstu. C port ima iste algoritme, ali oni nisu vezani za strukture podataka, barem ne u kodu. Oni su, međutim, povezani zajedno u mom umu. Taj program je objektno orijentisan u duhu, ako ne u suštini, ali to ne biste znali ako ga pogledate. Čudno.

Hajde da prvo pogledamo C program. [LISTING_ONE](#listing_one) je "auto.h", datoteka zaglavlja koja opisuje strukture podataka. Uticaj C++-a možete videti odmah. Sve stavke podataka imaju nazive objekata čak i ako samo putem typedef-a. [LISTING_TWO](#listing_two), je "auto.c", ostatak programa. Ovo je neupadljiv program, onaj koji čita unos i piše izveštaj. Vidite da li možete naći mnogo na putu uticaja C++ ovde.

Sada razmotrite C++ sistem. [LISTING_THREE](#listing_three) je "auto.hpp", datoteka zaglavlja koja opisuje klase za program. Prva primetna razlika između ove datoteke i auto.h je u tome što struktura datuma ima ugrađenu funkciju prikaza. Ta ista funkcija postoji u C programu, ali nije tako čvrsto vezana za strukturu koju podržava. U C-u možete to nazvati, proslediti mu bilo šta što liči na datum, i prikazaće nešto. U C++ jedini način da se to pozove je preko instance strukture datuma.

Klase Automobile i Trip_Record imaju iste članove podataka koje imaju njihove ekvivalentne C strukture, ali takođe imaju konstruktor, destruktor i druge funkcije članova. Postoje preopterećene funkcije i operatori i druge C++ ekstenzije. [LISTING_FOUR](#listing_four) je "auto.cpp", kod za funkcije člana. Zatim [LISTING_FIVE](#listing_five) je "autorpt.cpp", kod za aplikaciju.

Distribucija koda je drugačija u C++ programu. Veliki deo onoga što nađete u "auto.c" delu C programa ide u funkcije članova klase i može biti skriveno kao deo biblioteke klasa za višekratnu upotrebu za programere koji rade za dobavljače izlaznog sistema za praćenje upotrebe automobila (AUTOS. Vidite kako su akronimi laki?)

Postoje dve upitne prečice u C++ programu. Memorija za instance Automobiles i Trip_Records dolazi iz besplatne prodavnice i nikada se ne vraća nazad. Njihova alokacija se dešava u funkcijama koje vraćaju same objekte, a ne pokazivače, tako da kada su spremni da izađu van opsega, vrednosti pokazivača nisu u blizini za brisanje. Ovo očigledno ludilo došlo je kao rezultat nekog još nerešenog sukoba između mene i Zortech kompajlera. Problem je verovatno bio moj, a rok za panel se približio, pa sam odlučio lakšim putem.

Da nisam imao taj problem, možda bih morao da kodiram preopterećenu funkciju dodele za klasu Automobile. Njegov destruktor briše stringove koji sadrže broj licence i proizvođača. Ako koristite objekte ove klase onako kako su dizajnirani u dodeljivanju, pokazivači objekta koji se šalje će se dvaput izbrisati kada dva objekta izađu van opsega. Dobro dizajnirana klasa će uključivati funkciju dodeljivanja koja pravilno upravlja pokazivačima slobodnih prodavnica u objektima za slanje i primanje. Ali pošto se klasa Automobila nikada ne dodeljuje na takav način, problem se ne pojavljuje, a ja nisam potrošio vreme da napravim funkciju dodeljivanja.

Poenta prezentacije bila je da se naglasi prednosti jezika. Snage C-a su dobro poznate. Međutim, njegova najveća snaga je najnoviji, a to je što je standard odobren. C je standardni jezik. Isporučuje čvrst, sažet kod, poznat je mnogim programerima i ima poverenje mnogih menadžera. Te prednosti doprinose velikom broju poslova za programere i skoro puno programera koji će popuniti poslove. C je posebno pogodan za sistemsko programiranje zbog svog čvrstog koda, jer se možete približiti mašini i zato što je labavo kucan.

Ne smej se. Jedna od prednosti C++-a je to što je snažno otkucan. Ali C++ služi različitim svrhama sa svojim objektno orijentisanim pristupom dizajnu i razvoju softvera i njegovom mogućnošću koja omogućava programeru da proširi jezik novim tipovima podataka. Moja omiljena prednost C++-a je u tome što kada se čini da OOPS način ne odgovara, mogu upasti u stari dobri pouzdani C.

Oba jezika dele prednost u tome što kada ih izaberete, ne potpisujete automatski p na određenom korisničkom interfejsu ili modelu baze podataka. Oni su implementirani na mnogim sistemima sa odvojenim bibliotekama funkcija i klasa koje podržavaju ove stvari.

Ostali jezici predstavljeni na panelu bili su SCHEME, Rekk, Smalltalk i MicroStep. Kada je panel završen, Voren je zamolio svakog od nas da izabere jezik koji ćemo koristiti ako treba da pređemo na neki od drugih. Nikada nisam koristio nijedan drugi jezik i zato sam izabrao onaj koji sam mogao da čitam a da ga već nisam znao. Taj je bio Rekk, interpretirani jezik za izradu prototipa.

#### LISTING_ONE

```c
/* --------- auto.h ------------ */

/*
 * simple data element classes
 */
typedef long odometer;
typedef double Dollars;

/*
 * a simple date class
 */
typedef struct {
    int mo, da, yr;
} date;

/*
 * Automobile class
 */
typedef struct {
    int modelyear;
    char manufacturer[20];
    char license[9];
} Automobile;

/*
 * Trip_Record class
 */
typedef struct {
    date trip_date;
    odometer odometer_in;
    odometer odometer_out;
    int gallons;
    Dollars cost;
} Trip_Record;
```

#### LISTING_TWO

```c
/* --------- auto.c ------------ */

#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include "auto.h"

static Automobile get_car(void);
static void reportauto(Automobile);
char *display_date(date dt);
static Trip_Record get_trip(void);
static void reporttrip(Trip_Record);

void main()
{
    while (1)   {
        /* ---- a report for each car in the input ----- */
        Automobile car = get_car();
        Trip_Record trip_totals = {{0,0,0},0,0,0,0};

        if (car.modelyear == 0)
            break;
        reportauto(car);
        /* -------- prepare the detail report ---------- */
        printf("\n  Date   miles  gas    cost    mpg  cost/mile");
        printf("\n-------- ----- ----- -------- ----- ---------");
        while (1)   {
            /* ----- get the next Trip_Record from the input ---- */
            Trip_Record trip = get_trip();
            if (trip.trip_date.mo == 0)
                break;
            /* -------- report the trip ----------- */
            reporttrip(trip);
            /* -------- collect totals for this car -------- */
            trip_totals.odometer_in =
                max(trip_totals.odometer_in, trip.odometer_in);
            trip_totals.odometer_out = trip_totals.odometer_out ?
                min(trip_totals.odometer_out, trip.odometer_out) :
                trip.odometer_out;
            trip_totals.gallons += trip.gallons;
            trip_totals.cost += trip.cost;
        }
        reporttrip(trip_totals);
    }
}

/* ---------- get a car's description from the input -------- */
static Automobile get_car(void)
{
    static Automobile car;

    car.modelyear = 0;
    scanf("%s", car.license);
    if (strcmp(car.license, "end") != 0)    {
        scanf("%d", &car.modelyear);
        scanf("%s", car.manufacturer);
    }
    return car;
}

static void reportauto(Automobile car)
{
    printf("\n\n%d %s License # %s\n",
        car.modelyear, car.manufacturer, car.license);
}

/* ------ get a trip record from the input stream ----- */
static Trip_Record get_trip(void)
{
    date dt;
    char strdate[9];
    odometer oin = 0, oout = 0;
    int gas = 0;
    Dollars cost = 0;
    static Trip_Record trip;

    dt.mo = 0;
    scanf("%s", strdate);
    if (strcmp(strdate, "end")) {
        dt.mo = atoi(strdate);
        dt.da = atoi(strdate + 3);
        dt.yr = atoi(strdate + 6);
        scanf("%ld", &oin);
        scanf("%ld", &oout);
        scanf("%d", &gas);
        scanf("%lf", &cost);
    }
    trip.trip_date = dt;
    trip.odometer_in = oin;
    trip.odometer_out = oout;
    trip.gallons = gas;
    trip.cost = cost;
    return trip;
}

static void reporttrip(Trip_Record trip)
{
    int miles = (int) (trip.odometer_in - trip.odometer_out);
    printf("\n%s %5d %5d %#8.2f %5d %#8.2f",
                trip.trip_date.mo ?
                    display_date(trip.trip_date) :
                    "totals  ",
                miles,
                trip.gallons,
                trip.cost,
                trip.gallons ? miles / trip.gallons : 0,
                miles ? trip.cost / miles : 0
                );
}

char *display_date(date dt)
{
    static char d[9];
    sprintf(d, "%2d-%02d-%02d", dt.mo, dt.da, dt.yr);
    return d;
}
```

#### LISTING_THREE

```c
// auto.hpp

// -----------------------------
// simple data element classes
// -----------------------------
typedef long odometer;
typedef double Dollars;

//  -----------------------------
// a simple date class
// -----------------------------
struct date {
    int mo, da, yr;
    char *display(void);
};

// -----------------------------
// Automobile class
// -----------------------------
class Automobile    {
private:
    int modelyear;
    char *manufacturer;
    char *license;
public:
    Automobile(char *license, int year, char *make);
    Automobile();
    ~Automobile();
    char *display(void);
    int nullcar(void) {return modelyear == 0;}
};

// -----------------------------
// Trip_Record class
// -----------------------------
class Trip_Record   {
private:
    date trip_date;
    odometer odometer_in;
    odometer odometer_out;
    int gallons;
    Dollars cost;
public:
    Trip_Record(void);
    Trip_Record(date, odometer, odometer, int, Dollars);
    int mileage(void) {return odometer_in - odometer_out;}
    char *display(void);
    int nulltrip(void) {return trip_date.mo == 0;}
    void operator += (Trip_Record&);
};
```

#### LISTING_FOUR

```c
// auto.cpp

#include <stream.hpp>
#include <string.h>
#include <stdlib.h>
#include "auto.hpp"

char *date::display()
{
    return form("%2d-%02d-%02d", mo, da, yr);
}

// --------------- constructors ---------------------------
Automobile::Automobile()
{
    modelyear = 0;
    manufacturer = license = NULL;
}

Automobile::Automobile(char *lic, int year, char *make)
{
    license = new char[strlen(lic)+1];
    strcpy(license, lic);
    modelyear = year;
    manufacturer = new char[strlen(make)+1];
    strcpy(manufacturer, make);
}

// -------------- destructor -------------------
Automobile::~Automobile()
{
    if (license != NULL)
        delete license;
    if (manufacturer != NULL)
        delete manufacturer;
}

char *Automobile::display()
{
    return form("%d %s License # %s",
                    modelyear, manufacturer, license);
}

#define max(a,b) ((a)>(b)?(a):(b))
#define min(a,b) ((a)<(b)?(a):(b))

void Trip_Record::operator += (Trip_Record& trip)
{
    this->odometer_in = max(this->odometer_in, trip.odometer_in);
    this->odometer_out = this->odometer_out ?
                         min(this->odometer_out, trip.odometer_out) :
                         trip.odometer_out;
    this->gallons += trip.gallons;
    this->cost += trip.cost;
}

// --------------- constructors ---------------------------
Trip_Record::Trip_Record()
{
    trip_date.mo = trip_date.da = trip_date.yr = 0;
    odometer_in = odometer_out = 0;
    gallons = 0;
    cost = 0;
}

Trip_Record::Trip_Record(date trdt, odometer oin, odometer oout,
                          int gas, Dollars cst)
{
    trip_date = trdt;
    odometer_in = oin;
    odometer_out = oout;
    gallons = gas;
    cost = cst;
}

char *Trip_Record::display()
{
    int miles = mileage();
    return form("%s %5d %5d %#8.2f %5d %#8.2f",
                trip_date.mo ? trip_date.display() : "totals  ",
                miles,
                gallons,
                cost,
                gallons ? miles / gallons : 0,
                miles ? cost / miles : 0
                );
}
```

#### LISTING_FIVE

```c
// autorpt.cpp

#include <stream.hpp>
#include <string.h>
#include <stdlib.h>
#include "auto.hpp"

// =========================================================
// automobile operating costs system
// =========================================================

// ------- prototypes
static Automobile& get_car(void);
static Trip_Record& get_trip(void);
static void report(Automobile&);
static void report(Trip_Record&);

main()
{
    while (1)   {
        // -------- a report for each car in the input
        Automobile car = get_car();
        if (car.nullcar())
            break;
        report(car);
        // -------- prepare the detail report
        cout << "\n  Date   miles  gas    cost    mpg  cost/mile";
        cout << "\n-------- ----- ----- -------- ----- ---------";
        // ---------- a Trip_Record to hold the totals
        Trip_Record trip_totals;

        while (1)   {
            // -------- get the next Trip_Record from the input
            Trip_Record trip = get_trip();
            if (trip.nulltrip())
                break;
            // -------- report the trip
            report(trip);
            // -------- collect totals for this car
            trip_totals += trip;
        }
        report(trip_totals);
    }
}

// ---------- get a car's description from the input
static Automobile& get_car(void)
{
    char license[7];
    int modelyear = 0;
    char make[25] = "";

    cin >> license;
    if (strcmp(license, "end") != 0)    {
        cin >> modelyear;
        cin >> make;
    }
    Automobile *car = new Automobile(license, modelyear, make);
    return *car;
}

static void report(Automobile& car)
{
    cout << "\n\n" << car.display() << '\n';
}

// ---------- get a trip record from the input stream
static Trip_Record& get_trip()
{
    date dt;
    dt.mo = 0;
    char strdate[9];
    odometer oin = 0, oout = 0;
    int gas = 0;
    Dollars cost = 0;

    cin >> strdate;
    if (strcmp(strdate, "end")) {
        dt.mo = atoi(strdate);
        dt.da = atoi(strdate + 3);
        dt.yr = atoi(strdate + 6);
        cin >> oin;
        cin >> oout;
        cin >> gas;
        cin >> cost;
    }
    Trip_Record *trip = new Trip_Record(dt,oin, oout, gas, cost);
    return *trip;
}

static void report(Trip_Record& trip)
{
    cout << '\n' << trip.display();
}
```
