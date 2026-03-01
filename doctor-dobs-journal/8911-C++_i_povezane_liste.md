
# C programiranje 8911

## C++ i povezane liste - Al Stevens

Ovog meseca se upuštam dalje u C++ sa novom klasom, LinkedList. Programeri već godinama koriste strukturu podataka povezane liste. Omogućava vam da sačuvate nepoznat broj stavki podataka u tabeli gde unosi ne moraju da budu susedni u memoriji. Takođe rešava probleme koji se javljaju kod standardnih nizova kada unosi imaju različite veličine. Evo kako funkcioniše povezana lista.

Struktura podataka povezane liste povezuje listu stavki podataka zajedno sa skupom pokazivača. Sama lista ima pokazivač na prvi i poslednji unos u listi. Ovi pokazivači se nazivaju "glava liste". Povezana lista se sastoji od glave liste i unosa podataka. Svaki unos podataka na listi ima pokazivač na onaj koji je neposredno pre njega i pokazivač na onaj koji je neposredno iza njega. Prvi i poslednji pokazivač u glavi liste su u početku NULL. Oni se inicijalizuju sa vrednostima koje nisu NULL kada se prva stavka doda na listu. Prva stavka na listi ima NULL pokazivač prethodne stavke, a poslednja stavka na listi ima NULL pokazivač sledeće stavke.

Da biste prešli preko povezane liste u nizu unapred, počinjete sa vrednošću u pokazivaču prve stavke iz glave liste. Ako je taj pokazivač NULL, lista je prazna. U suprotnom, ukazuje na prvu stavku podataka. Ta stavka ima pokazivač na sledeću stavku podataka. Krećete se napred koristeći pokazivač sledeće stavke u svakoj sledećoj stavci sve dok ne pronađete stavku sa NULL pokazivačem sledeće stavke, što znači da ste na kraju liste. Ruta obrnutog niza kroz listu funkcioniše na isti način, ali počinje sa pokazivačem poslednje stavke u glavi liste i koristi pokazivač prethodne stavke u stavkama.

Da biste dodali stavku na kraj liste, postavite pokazivač prethodne stavke na mesto gde pokazuje pokazivač poslednje stavke, a pokazivač poslednje stavke postavite na novu stavku. Takođe morate da postavite pokazivač sledeće stavke u ono što je bila poslednja stavka, sa svojim pokazivačem sledeće stavke, na novu stavku.

Brisanje unosa sa povezane liste je pitanje prekida i popravljanja poslednjeg lanca. Prethodna stavka obrisane stavke sada mora da ukazuje na sledeću stavku obrisane stavke i obrnuto. Naravno, morate da testirate da li je izbrisana stavka prva ili poslednja (ili oboje) i da popravite prvi i poslednji pokazivač u glavi liste u skladu sa tim.

To je ono što je povezana lista. Svi rade skoro isto. Ono što se razlikuju je u formatu stavki podataka kojima upravljaju. Kao takva, povezana lista je dobar kandidat da bude generička C++ klasa, i siguran sam da to nije nova ideja.

[LISTING_ONE](#listing_one) i [LISTING_TWO](#listing_two) su "linklist.h" i "linklist.cpp", izvorne datoteke koje implementiraju klasu LinkedList.

Da biste koristili klasu LinkedList, u svoj program uključite "linklist.h". Okruženje strukture podataka svojstvima povezane liste je jednostavno pitanje deklarisanja LinkedList objekta i dodavanja tih struktura kao unosa na listu. Sama struktura podataka može biti jednostavna kao karakter i složena kao nova klasa. Kada prosledite njenu adresu i veličinu funkciji (metodu) člana povezane liste koja dodaje unose, vi kontrolišete podatke u povezanoj listi. Evo kako deklarišete listu:

```cpp
LinkList mylist;
```

To je sve. Sve dok klasa ne izađe iz opsega, lista, isprva prazna, postoji. Evo kako dodajete unose podataka na listu:

```cpp
mylist.addentry(&data, sizeof(data));
```

Ako proglasite listu, a zatim počnete da dodajete unose, oni će se redom dodavati na kraju liste. Svaki novi unos prati onaj koji je neposredno pre njega, i nalazi se na kraju dok ne dođe drugi.

Ako pređete preko liste, a zatim dodate unos dok je pozicioniran u sredini liste, unos će biti dodat odmah iza onog koji ste poslednji preuzeli. Ako se nalazite na početku liste, unos će biti dodat kao prvi.

Da biste preuzeli unos sa povezane liste, vijugate kroz listu unapred ili unazad i pogledate vrednosti podataka unutar svakog unosa. Svaka funkcija člana koja prolazi kroz listu vraća pokazivač na unos liste koji je pronašao. Evo metoda za prelazak liste:

```cpp
void *getfirst(void);
void *getnext(void);
void *getprev(void);
void *getlast(void);
void *getcurr(void);
```

Jednog od njih biste nazvali na ovaj način:

```cpp
char *cp = mylist.getfirst();
```

Svaka od ovih funkcija članica vraća pokazivač na vrednost podataka pronađenog unosa. Ove vraćene vrednosti možete dodeliti pokazivaču na sve što imate na listi. Ako je lista prazna ili skeniranje liste dođe do kraja ili početka, funkcija vraća NULL pokazivač.

Da biste izbrisali unos, idite do njega i pozivate funkciju člana koja briše trenutni unos. Evo poziva za tu operaciju:

```cpp
mylist.delete_entry();
```

[LISTING_THREE](#listing_three) je "demolist.cpp", C++ program koji koristi klasu LinkedList za prikupljanje i upravljanje listom imena. Program koristi string klasu od prošlog meseca i prva stvar koju sam shvatio je da je string klasi potrebna funkcija člana koja vraća veličinu stringa. Sada je ovo vreme kada bi strogo kontrolisan objektno orijentisan projekat verovatno izgradio izvedenu klasu samo da bi dodao tu funkciju. Ali, pošto nismo tako rigorozni i uopšte nismo kontrolisani, samo ćemo dodati funkciju našoj originalnoj string klasi. Pogledajte "strings.h" od prošlog meseca i umetnite ove redove sa funkcijama člana:

```cpp
// -------- return the length of a string
int length(void) { return strlen(sptr)+1; }
```

Program demolist traži od vas da unesete neka imena u listu. Dodaje svako ime koje unesete na LinkedList. Kada završite, otkucate „kraj“ i program će prikazati meni. Možete prikazati imena, umetnuti novo ime na trenutnoj lokaciji, kretati se napred i nazad kroz listu i izbrisati trenutno ime.

### LISTING_ONE

```cpp
// -------- linklist.h

#ifndef LINKLIST
#define LINKLIST

#include <stdio.h>

class LinkedList {
   typedef struct list_entry   {
      struct list_entry *NextEntry;
      struct list_entry *PrevEntry;
      void *entrydata;
   } ListEntry;
   ListEntry *FirstEntry;
   ListEntry *LastEntry;
   ListEntry *CurrEntry;
public:
   // ---- constructor
   LinkedList(void)
      { FirstEntry = LastEntry = CurrEntry = NULL; }
   // ---- destructor
   ~LinkedList(void);
   // ---- add an entry
   void addentry(void *newentry, int size);
   // ---- delete the current entry
   void delete_entry(void);
   // ---- get the first entry in the list
   void *getfirst(void);
   // ---- get the next entry in the list
   void *getnext(void);
   // ---- get the previous entry in the list
   void *getprev(void);
   // ---- get the last entry in the list
   void *getlast(void);
   // ---- get the current entry in the list
   void *getcurr(void)
      {return CurrEntry==NULL ? NULL : CurrEntry->entrydata;}
};

#endif
```

### LISTING_TWO

```cpp
// -------------- linklist.cpp

#include <string.h>
#include "linklist.h"

// ------- linked list destructor
LinkedList::~LinkedList(void)
{
   ListEntry *thisentry = FirstEntry;

   while (thisentry != NULL)   {
      delete thisentry->entrydata;
      ListEntry *hold = thisentry;
      thisentry = thisentry->NextEntry;
      delete hold;
   }
}

// --------- add an entry to the list
void LinkedList::addentry(void *newentry, int size)
{
   /* ------- build the new entry ------- */
   ListEntry *thisentry = new ListEntry;
   thisentry->entrydata = new char[size];
   memcpy(thisentry->entrydata, newentry, size);

   if (CurrEntry == NULL)   {
      thisentry->PrevEntry = NULL;
      // ---- adding to the beginning of the list
      if (FirstEntry != NULL)   {
         /* ---- already entries in this list ---- */
         thisentry->NextEntry = FirstEntry;
         FirstEntry->PrevEntry = thisentry;
      }
      else   {
         // ----- adding to an empty list
         thisentry->NextEntry = NULL;
         LastEntry = thisentry;
      }
      FirstEntry = thisentry;
   }
   else   {
      // ------- inserting into the list
      thisentry->NextEntry = CurrEntry->NextEntry;
      thisentry->PrevEntry = CurrEntry;
      if (CurrEntry == LastEntry)
         // ---- adding to the end of the list
         LastEntry = thisentry;
      else
         // ---- inserting between existing entries
         CurrEntry->NextEntry->PrevEntry = thisentry;
      CurrEntry->NextEntry = thisentry;
   }
   CurrEntry = thisentry;
}

// ---------- delete the current entry from the list
void LinkedList::delete_entry(void)
{
   if (CurrEntry != NULL)   {
      if (CurrEntry->NextEntry != NULL)
         CurrEntry->NextEntry->PrevEntry = CurrEntry->PrevEntry;
      else
         LastEntry = CurrEntry->PrevEntry;
      if (CurrEntry->PrevEntry != NULL)
         CurrEntry->PrevEntry->NextEntry = CurrEntry->NextEntry;
      else
         FirstEntry = CurrEntry->NextEntry;
      delete CurrEntry->entrydata;
      ListEntry *hold = CurrEntry->NextEntry;
      delete CurrEntry;
      CurrEntry = hold;
   }
}

// ---- get the first entry in the list
void *LinkedList::getfirst(void)
{
   CurrEntry = FirstEntry;
   return CurrEntry == NULL ? NULL : CurrEntry->entrydata;
}

// ---- get the next entry in the list
void *LinkedList::getnext(void)
{
   if (CurrEntry == NULL)
      CurrEntry = FirstEntry;
   else
      CurrEntry = CurrEntry->NextEntry;
   return CurrEntry == NULL ? NULL : CurrEntry->entrydata;
}

// ---- get the previous entry in the list
void *LinkedList::getprev(void)
{
   if (CurrEntry == NULL)
      CurrEntry = LastEntry;
   else
      CurrEntry = CurrEntry->PrevEntry;
   return CurrEntry == NULL ? NULL : CurrEntry->entrydata;
}

// ---- get the last entry in the list
void *LinkedList::getlast(void)
{
   CurrEntry = LastEntry;
   return CurrEntry == NULL ? NULL : CurrEntry->entrydata;
}
```

### LISTING_THREE

```cpp
// -------- demolist.cpp

#include <stream.hpp>
#include "strings.h"
#include "linklist.h"

void collectnames(LinkedList& namelist);
int menu(void);
void displaynames(LinkedList& namelist);
void stepforward(LinkedList& namelist);
void stepbackward(LinkedList& namelist);
string insertname(LinkedList& namelist);

void main(void)
{
   cout << "Enter some names followed by \"end\"\n";
   // ------ a linked list of names
   LinkedList namelist;
   collectnames(namelist);
   int key = 0;
   while (key != 6)   {
      switch (key = menu())   {
         case 1:
            displaynames(namelist);
            break;
         case 2:
            stepforward(namelist);
            break;
         case 3:
            stepbackward(namelist);
            break;
         case 4:
            insertname(namelist);
            break;
         case 5:
            namelist.delete_entry();
            break;
         case 6:
            cout << "Quitting...";
            break;
         default:
            break;
      }
   }
}

void collectnames(LinkedList& namelist)
{
   // ------- until the user types "end"
   while (insertname(namelist) != "end")
      ;
}

int menu(void)
{
   cout << "\n1 = display the names";
   cout << "\n2 = step forward through the names";
   cout << "\n3 = step backward through the names";
   cout << "\n4 = insert a name";
   cout << "\n5 = delete the current name";
   cout << "\n6 = quit";
   cout << "\nEnter selection: ";
   int key;
   cin >> key;
   return key;
}

// ------ read the names in a list and display them
void displaynames(LinkedList& namelist)
{
   cout << "------ NAME LIST ------\n";
   char *name = namelist.getfirst();
   while (name != NULL)   {
      cout << name << "\n";
      name = namelist.getnext();
   }
   cout << "-----------------------\n";
}

// ------- step forward through the list of names
void stepforward(LinkedList& namelist)
{
   char *name = namelist.getnext();
   cout << (name ? name : "-- End of list --") << "\n";
}

// ------- step backwardward through the list of names
void stepbackward(LinkedList& namelist)
{
   char *name = namelist.getprev();
   cout << (name ? name : "-- Beginning of list --") << "\n";
}

// ------- insert a name into the list
string insertname(LinkedList& namelist)
{
   cout << "Enter a name: ";
   // ----- a string to hold one name
   string name(80);
   // ------- read a name
   cin >> name.stradr();
   // ------- add it to the list
   if (name != "end")
      namelist.addentry(name.stradr(), name.length());
   return name;
}
