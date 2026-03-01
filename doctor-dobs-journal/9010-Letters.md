
# C PROGRAMMING 9010

## Dragi Al ... - Al Stevens

### Token Pasting

U dve prethodne kolumne raspravljao sam o ANSI preprocesorskom ## operatoru lepljenja tokena, žaleći se da njegova svrha nije očigledna. Iako ANSI dokumentacija objašnjava ponašanje ##, ona ne objašnjava na adekvatan način razlog zašto postoji operator. Nekoliko čitalaca je odgovorilo svojim komentarima, a neki su poslali kod koji ilustruje njegovu upotrebu operatora ##. Pošto su ti čitaoci bili dovoljno dobri da me prosvetle, smatrao sam da je u redu da vama ostalima prenesem dobre vesti.

Robert L. White iz Danburija, Konektikat predstavlja primer iz tumača menija: Često koristim [operator ##] u makro definicijama da pojednostavim generisanje tabela. Ima tendenciju da se razlike u tabelama bolje ističu.

Evo primera iz tumača menija. Ova stvar je koristila tabele za opis različitih menija. Da bismo sačuvali zdrav razum, razvili smo konvenciju imenovanja. Liste važećih ključeva za dati meni su otišle u niz XXX_TeXt, a liste funkcija u niz XXX_Funcs. Adrese ovih različitih lista su uskladištene u strukturi koja se zove MenuDesc menija.

```c
struct MenuDesc
{
    int     *keys;
    char    **Text;
    int     (**Funcs)();
};
```

Mogli smo da imamo tabelu koja izgleda ovako:

```c
struct MenuDesc AllMenus[] =
{
    Main_Keys, Main_Text, Main_Funcs,
    Brain_Keys, Brain_Text, Brain_Funcs,
    Bone_Keys, Bone_Text, Bone_Funcs
   /*  ... etc. ... */
   };
```

Ali, koristeći sledeći makro, učinili smo tabelu jednostavnijom.

```c
#define MENU(name)\
    name##_Keys, name##_ Funcs,

struct MenuDesc AllMenus[] =
{
    MENU(Main),
    MENU(Brain),
    MENU(Bone),
    /*  ... etc. ... */
};
```

Naše tabele su zapravo bile mnogo složenije od ove, ali ovo vam daje predstavu o tome kako smo koristili operator lepljenja tokena da bismo pojednostavili izvorni kod. Lepljenje tokena takođe povećava validnost koda tako što eliminiše mogućnost pogrešnog pisanja.

Primena operatera ## gospodina Vajta je pogodna jer njegova MenuDesc struktura veoma liči na onu koju sam koristio u drajverima menija za projekat SMALLCOM prošle godine. Njegov primer ilustruje jednu od prednosti ## operatora - njegovu sposobnost da smanji ponavljajući kod i samim tim smanji potencijal za greške u kodiranju.

Fred Smit iz Stounhema, Masačusets, šalje nam nešto o istoriji: Upravo sam ponovo pročitao vašu kolumnu u izdanju iz jula 1990. i primetio da očigledno niste svesni porekla lepljenja tokena u C. Nemojte se osećati loše, jer to svakako nije široko rasprostranjena informacija. Možda ni ja nemam sve detalje, ali evo šta znam o tome: postoji bar jedan predprocesor u širokoj upotrebi na Unik sistemima (čuo sam da se zove „Rajzerov predprocesor“) koji dozvoljava upotrebu praznog komentara „/**/“ kao operatora za lepljenje tokena u izjavama preprocesora. Ovaj predprocesor se koristi na (barem) mnogim BSD sistemima, kao što su Sun radne stanice. Primer:

```c
#define pasteup(a,b) (a/**/b)
```

da vam dam "Reiser" ekvivalent vašeg primera na strani 134 julskog izdanja.

Ovo je užasna greška, jer direktno krši ono što je K&R rekao o tome šta pretprocesor radi sa komentarima, tj. komentari se zamenjuju belim razmakom. Jasno, barem u slučaju praznog komentara, ovaj predprocesor ih zapravo zamenjuje ničim. Ovo je povremeno korisno, ali svakako nije C! (da ne govorimo o tome da je veoma neprenosiv!).

Na svom poslednjem poslu imao sam (ne)sreću da budem uključen u (veoma) veliki C program koji je bio pun neprenosivih sunizama i BSD-izama, uključujući i ovaj. Zanimljiva upotreba je ipak napravljena od haka za lepljenje tokena. Korišćen je za kreiranje paketa za upravljanje redom (ili povezanom listom) opšte namene koji je u potpunosti implementiran u pretprocesoru.

Bilo je najmanje desetak makroa definisanih kao deo paketa – jedan za kreiranje u pretprocesoru deklaracije strukture podataka koju želite, jedan za inicijalizaciju, jedan za dodavanje novog elementa, jedan za uklanjanje elementa, makroi za prelazak preko liste, dodeljivanje prostora za svaku strukturu, itd.

Sledi primer makroa koji pokazuje (pojednostavljeni) ukus ovih makroa:

```c
#define Q_NEXT(a,b)\(Q_/**/a ->b.nextchild)
```

tako da ako se ovo koristi u programu kao što je ovaj:

```c
child=Q_NEXT(pending_illo_list, current_item);
```

proširenje bi izgledalo ovako:

```c
child=(Q_pending_illo_list -> current_item.nextchild);
```

Priznajem da je ova sposobnost korisna i moćna. Međutim, mislim da se ova vrsta moći lako zloupotrebljava jer stvara kod koji može biti izuzetno sažet i težak za čitanje, čak i sa proširenjima makroa u rukama (imajte na umu da su stvarni makroi u paketu koji opisujem bili mnogo složeniji od primera koji ovde dajem). Zbog faktora zamagljivanja i očigledne neprenosivosti lepljenja tokena (bilo ANSI sortiranja ili sortiranja po Reiser-u) pomislio bih, ne dvaput, već više od dvadeset puta pre nego što izvršim takav kod.

Gospodin Smit ide dalje i otkriva da tehnika pre-ANSI lepljenja tokena koju sam našao u C++ datoteci zaglavlja radi sa Microsoft C 5.1 i ne uspeva sa KuickC 2.0. Njegov primer sa povezanom listom nagoveštava načine korišćenja preprocesorskog tokena za lepljenje za izgradnju primitivnog oblika parametrizovanih tipova podataka, što je problem kojim se trenutno bavi C++ zajednica.

Steve Pritchard iz Villovdale, Ontario koristi ## token paster da sakrije elemente C sintakse i učini da operativne vrednosti budu istaknutije prilikom definisanja tabele. On nam govori o svojoj filozofiji dizajna: U programiranju pokušavam da koristim kompajler da mi pomogne da učinim kod razumljivim i održavanim. Znam da postoje različita mišljenja o tome da li to znači korišćenje egzotičnih trikova ili samo korišćenje osnovnih funkcija kompajlera. U stilu kodiranja koji koristim, sekvenciram u sledećem prioritetu:

1. Uklanjanje višestrukih, raštrkanih zavisnosti/referenci
2. Uklanjanje suvišnih informacija
3. Održavanje stvari jednostavnim

G. Pritchard je poslao mnogo koda sa svojim pismom. Pokušaću da uhvatim suštinu njegovih ideja za lepljenje tokena sa ovim fragmentom koji koristi ## operator da eliminiše nered u tabeli heksadecimalnih vrednosti.

```c
#define h(n) 0x##n
#define tbl(a,b,c,d) h(a),h(b),h(c),h(d)
int mytable[] =
{
      tbl(02,09,2a,ff),
      tbl(00,1a,1b,e5)
};
```

Ovaj kod nije tačan izvod iz primera gospodina Pritcharda, koji su u kontekstu većeg problema, irelevantnog za ovu diskusiju. Uzeo sam slobodu da preradim tehniku u opštiji primer.

Postoji prostor za debatu o tome da li eliminacija 0k notacije u inicijalizatorima tabele doprinosi čitljivosti ili konfuziji. Sakrivanjem C sintakse, mogli biste obmanuti čitaoca koda. Taj 02 inicijalizator izgleda sumnjivo kao oktalna konstanta. ff izgleda kao identifikator. Druge izgledaju kao sintaksičke greške. Kamuflaža je efikasna samo ako čitalac zna kako je koder koristio pretprocesor. Ali ovde postoji klica ideje. Ovu tehniku možete efikasno koristiti da inicijalizujete neobično velike tabele i na taj način olakšate čitanje koda, ali samo ako koristite upadljive komentare i postavite makroe u blizini tabela gde ih čitalac može lako pronaći.

Verovatno najambiciozniji među lepljenjem tokena je Gregori Kolvin iz Bouldera u Koloradu koji piše: Utvrdio sam da je lepljenje tokena neprocenjivo za automatsko kreiranje mnogih deklaracija neophodnih za imitaciju objekata u stilu C++ u C kodu.

Koristimo makroe za implementaciju biblioteke korisničkog interfejsa koja treba da bude prenosiva na sve popularne GUI sisteme. Pošto sam prošlu godinu proveo radeći u Object Pascal-u i C++-u na Mac-u, nisam želeo da odustanem od objektno orijentisanog programiranja. Ali moj tim na poslu insistira na tome da se drži C. Ovi makroi su se pokazali kao razuman kompromis. Takođe sam otkrio da imaju prednost da uklone deo misterije iz objektno orijentisanog programiranja čineći implementaciju mnogo očiglednijom.

G. Kolvin je poslao nekoliko izvornih fajlova koji rade ono što on traži. Kod uključuje definicije osnovne klase člana zajedno sa uobičajenim metodama. Zatim implementira oblik nasleđivanja klase tako što definiše CLASS makro koji povezuje nasleđene članove i metode podređene klase sa onima iz baze. Operator ## lepi zajedničke elemente različitih imena struktura u one specifične klase. Nisam koristio pristup gospodina Kolvina, ali sam često mislio da bi takva tehnika mogla biti moguća. Uključio sam tri izvorna fajla, class.h, class.c i test.c kao [LISTING_ONE](#listing_one) [LISTING_TWO](#listing_two) i [LISTING_THREE](#listing_three) za vaše uživanje i eksperimentisanje.

### Obavezujući kodovi i poruke

Pismo Roberta Vajta takođe je uključivalo ovaj dragulj: Imam, za šta mislim, inovativnu tehniku programiranja C koju želim da prenesem drugim programerima. Neki od mojih kolega programera smatraju da je to previše egzotično jer koristi makro predprocesor i nabrojane konstante za koje mnogi C programeri (da li možete da verujete?) misle da su napredni elementi C jezika. [Ne čitači DDJ, Bobe. -- AS]

Ideja je da se garantuje da simbolički imenovani indeksi u tabeli uvek upućuju na tačan element tabele čak i nakon nekoliko iteracija koda. Ponekad je to teško garantovati kada nekoliko programera radi na istom projektu.

Klasičan primer takvog uparivanja indeksa sa objektom je upotreba kodova grešaka za traženje nizova grešaka. Evo jednostavnog načina da to uradite.

```c
/* ----- ERRCODE.H ----- */
#define SUCCESS          0
#define BrainError       1
#define BoneError        2

/* ----- ERRTEXT.C ----- */
char *ErrorStrings[] =
{
    "No error",
    "Brain error",
    "Bone error"
};
```

Šta ako jedan od programera na projektu ima staru verziju liste kodova grešaka i ne zna je? On ili ona mogu provesti dobar deo dana pokušavajući da pronađu pogrešnu grešku.

Zar ne bi bilo divno da postoji način da se navede kod greške i string greške u istoj liniji izvornog koda? Ako bi ista linija izvornog koda specificirala i kod i string, oni bi uvek bili sinhronizovani.

Evo kako da to uradite. Konstruišemo novu datoteku uključivanja koja će služiti dvostrukoj dužnosti. Kada želimo da generiše nizove grešaka, postavićemo zastavicu za pretprocesor i naterati ga da ispljune listu nizova grešaka. Ako zastavica nije definisana, nateraćemo pretprocesor da ispljune skup nabrojanih konstanti za koje se garantuje da se poklapaju sa listom stringova grešaka.

Ovo radimo tako što kreiramo dve definicije za jednostavan makro. Ako je zastavica definisana, makro se proširuje samo na prvi argument. Ako zastavica nije definisana, makro se proširuje na drugi argument.

```c
#if defined Make The String Table
#define Err(string,code) string
#else   #define Err(string,code) code
#endif
```

The include file will contain the following invocations of this macro:

```c
/* ----- ERRCODE.H ----- */
Err("No Error",         SUCCESS),
Err("Brain Error",      BrainError),
Err("Bone Error",       BoneError)

Da bismo definisali tabelu poruka o grešci, sada radimo ovo:

#define MakeTheStringTable
char *ErrorStrings[] =
{
    #include "errcode.h"
);

Da bismo definisali tabelu indeksa, radimo ovo:

#undef MakeTheStringTable
enum ErrorCode
{
    #include "errcode.h"
};
```

Nema potrebe da ovu tehniku ograničavate na generisanje konstanti i tabela. To je zaista tehnika poravnanja za bilo koji skup lista. Najčešći način usklađivanja različitih lista je njihovo kombinovanje u zapise ili strukture. Na ovaj način imate samo jednu listu i sve povezane informacije su definisane u jednoj izvornoj liniji. Međutim, ponekad to nije moguće ili poželjno i liste moraju biti definisane kao zasebni nizovi. Kroz pametno definisan makro unutar uključene datoteke, elementi obe liste mogu biti definisani u istoj liniji izvornog koda.

Ovo rešenje je jednostavno i efikasno. Rešava problem koji sam imao održavanje kodova grešaka koji su implementirani tačno onako kako je gospodin Vajt predložio.

#### LISTING_ONE

```c
/************ CLASS.H COPYRIGHT 1990 GREGORY COLVIN ************
This program may be distributed free with this copyright notice.
***************************************************************/

#include <stddef.h>
#include <assert.h>

/** 
    All objects must be descendants of the Base class, so we
    define the members and methods of Base here. 
**/
#define Base_MEMBERS
#define Base_METHODS                                           \
    Base *(*create) (void *table);                             \
    Base *(*clone)  (void *self);                              \
    Base *(*copy)   (void *self, Base *other);                 \
    void  (*destroy)(void *self);

typedef struct Base_methods Base_Methods;
typedef struct Base_members Base;
struct Base_members {
    Base_Methods *methods;
};
struct Base_methods {
    char *name;
    size_t size;
    Base_Methods *selfTable;
    Base_Methods *nextTable;
    Base_METHODS
};
extern Base_Methods Base_Table, *Class_List;
Base_Methods *TableFromName(char *name);
Base *ObjectFromName(char *name);

/** 
    The CLASS macro declares a Child class of the Parent. Note
    that Child##_MEMBERS, Child##_METHODS, Parent##_MEMBERS, and
    Parent##_METHODS must be already defined. All methods except
    create() require the first parameter, self, to be a pointer
    to an object of Child type; it can be declared void to stop
    compiler warnings when Parent methods are invoked, at the
    possible cost of warnings when methods are bound. The method
    table, Child##_Table, must be defined as a global structure. 
**/
#define CLASS(Parent,Child)                                    \
typedef struct Child##_methods Child##_Methods;                \
typedef struct Child##_members Child;                          \
struct Child##_members {                                       \
    Child##_Methods *methods;                                  \
    Parent##_MEMBERS                                           \
    Child##_MEMBERS                                            \
};                                                             \
struct Child##_methods {                                       \
    char *name;                                                \
    size_t size;                                               \
    Child##_Methods *selfTable;                                \
    Base_Methods *nextTable;                                   \
    Parent##_METHODS                                           \
    Child##_METHODS                                            \
};                                                             \
extern Child##_Methods Child##_Table

/** 
    The INHERIT and BIND macros allow for binding functions to
    object methods at run-time. They must be called before
    objects can be created or used. 
**/
#define INHERIT(Parent,Child)                                  \
    Child##_Table = *(Child##_Methods*)&Parent##_Table;        \
    Child##_Table.name = #Child;                               \
    Child##_Table.size = sizeof(Child);                        \
    Child##_Table.selfTable = &Child##_Table;                  \
    Child##_Table.nextTable = Class_List;                      \
    Class_List = (Base_Methods *)&Child##_Table

#define BIND(Class,Method,Function)                            \
    Class##_Table.Method = Function

/** 
    The CREATE macro allocates and initializes an object pointer
    by invoking the create() method for the specified Class.
    The DEFINE macro declares an object structure as an
    automatic or external variable; it can take initializers
    after a WITH, and ends with ENDDEF. The NAMED macro creates
    an object pointer from a class name. 
**/
#define CREATE(Class)                                          \
    (Class *)(*Class##_Table.create)(&Class##_Table)

#define DEFINE(Class,ObjectStruct)                             \
    Class ObjectStruct = { &Class##_Table
#define WITH ,
#define ENDDEF }

#define NAMED(ClassName) ObjectFromName(ClassName)

/** The VALID macro tests the method table self reference. **/
#define VALID(ObjectPtr)                                       \
    ((ObjectPtr)->methods->selfTable == (ObjectPtr)->methods)

/** 
    The SEND and CALL macros invoke object methods, through the
    object's methods pointer with SEND, or directly from a
    method table with CALL. Method parameters besides self may
    be sent using WITH, and END terminates the invocation. 
**/
#define SEND(Message,ObjectPtr)                                \
(   
    assert(VALID(ObjectPtr)),                                  \
    (*((ObjectPtr)->methods->Message))((ObjectPtr)

#define CALL(Class,Method,ObjectPtr)                           \
(   
    assert(VALID(ObjectPtr)),                                  \
    assert(Class##_Table.selfTable == &Class##_Table),         \
    (*(Class##_Table.Method))((ObjectPtr)

#define END ))

/** The DESTROY macro invokes an objects destroy() method **/
#define DESTROY(ObjectPtr) SEND(destroy,(ObjectPtr))END
```

#### LISTING_TWO

```c
/************ CLASS.C COPYRIGHT 1990 GREGORY COLVIN ************
This program may be distributed free with this copyright notice.
***************************************************************/
#include <assert.h>
#include <string.h>
#include <stdlib.h>
#include <stdio.h>
#include "class.h"

Base *BaseCreate(Base_Methods *table)
{   Base *new = (Base *)calloc(1,table->size);
    assert(new);
    new->methods = table;
    return new;
}

Base *BaseClone(Base *self)
{   Base *new =  (Base *)malloc(self->methods->size);
    assert(new);
    memcpy(new,self,self->methods->size);
    return new;
}

Base *BaseCopy(Base *self, Base *other)
{   memcpy(self,other,self->methods->size);
    return self;
}

void BaseDestroy(Base *self)
{  if (self)
       free(self);
}

Base_Methods Base_Table = {
    "Base",
    sizeof(Base),
    &Base_Table,
    0,
    BaseCreate,
    BaseClone,
    BaseCopy,
    BaseDestroy
};
Base_Methods *Class_List = &Base_Table;

Base_Methods *TableFromName(char *name)
{   Base_Methods *table;
    char *pname, *tname=table->name;
    for (table=Class_List; table; table=table->nextTable)
        for (pname=name; *pname == *tname++; pname++ )
            if (!*pname)
                return table;
    return 0;
}

Base *ObjectFromName(char *name)
{   Base_Methods *table = TableFromName(name);
    if (table)
        return table->create(table);
    return 0;
}
```

#### LISTING_THREE

```c
/** 
    TEST.C  Add one to argv[1] the hard way. This program serves
    no real purpose except invoking most of the CLASS macros. 
**/
#include <stdio.h>
#include <stdlib.h>
#include "class.h"

/** Define My class members and methods. **/
#define My_MEMBERS int stuff;
#define My_METHODS void (*set)(void*,int); int (*next)(void*);
CLASS(Base,My);

/** Define space for My method table. **/
My_Methods My_Table;

/** Define functions to implement My methods. **/
void MySet(My *self, int new)
{ 
    self->stuff = new;
}

int MyNext(My *self)
{ 
    return ++self->stuff;
}

/** 
    A function to be called in main to initialize My method table. 
**/
void MyInit( void )
{ 
    INHERIT(Base,My);
    BIND(My,set,MySet);
    BIND(My,next,MyNext);
}

/** Make My class do something. **/
My *test( int i )
{ 
    int j;
#if 1
    My *objectPtr = CREATE(My);   /* One way to make an object */
#else
    DEFINE(My,object)ENDDEF;      /* Another way */
    My *objectPtr= (My *)SEND(clone,&object)END;
#endif
    CALL(My,set,objectPtr) WITH i END;
    if (j = SEND(next,objectPtr)END)
        return objectPtr;
    DESTROY(objectPtr);
    return 0;
}

main(int argc, char **argv)
{ 
    int arg = atoi(argv[1]);
    My *out;
    MyInit();
    if (out = test(arg)) {
        printf("%d\n",out->stuff);
        DESTROY(out);
    } else
        printf("0\n");
}
```
