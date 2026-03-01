# C PROGRAMMING 9309

## C++ Exception Handling - Al Stevens

### Greške i presretanja

Rukovanje izuzecima je sledeća vruća funkcija jezika C++. Koristeći ga, program može presresti i obraditi izuzetne uslove – obično greške – na uredan, organizovan i dosledan način. Rukovanje izuzecima još uvek nije implementirano u mnogim C++ kompajlerima. Jedini objavljeni MS-DOS kompajler koji podržava ovu funkciju je novi Watcom C++ kompajler. Prvo sam raspravljao o rukovanju izuzecima u udžbeniku pod nazivom "Teach Iourself C++", treće izdanje koji je izašao ovog leta. Ta i ova diskusija nisu zasnovane na bilo kakvom praktičnom iskustvu, već na onome što sam pročitao o ovoj temi, tako da fragmenti koda koje koristim za primere možda nisu sasvim tačni. Štaviše, moje reakcije na to kako je ta funkcija specificirana i kako bi se mogla primeniti ne treba shvatiti kao jevanđelje. Pragmatični programeri mogu da posmatraju rukovanje izuzetcima kao eksperimentalnu funkciju sve dok je više kompajlera ne primeni sa doslednim ponašanjem i interfejsom. To će, međutim, biti moćna i korisna jezička karakteristika, i radujem se vremenu kada će je svi kompajleri uključiti i kada ćemo svi moći da je koristimo.

Bjarne Stroustrup opisuje rukovanje izuzetcima u C++ programskom jeziku, drugo izdanje (Addison-Veslei, 1991). Anotirani C++ Referentni priručnik, od Ellisa i Stroustrupa (koji se obično naziva ARM, Addison-Veslei, 1990) takođe definiše rukovanje izuzetcima, tako da ova funkcija nije nova, jer je opisana u štampi već oko tri godine. Samo je na raspolaganju nekoliko kompajlera koji to implementiraju.

Rukovanje izuzecima omogućava jednom delu programa da otkrije i prijavi izuzetne uslove, a drugom delu da ih obradi. Ovo je odgovarajuća naredba. Klase i funkcije u bibliotekama obično znaju kako da otkriju greške, a da ne znaju šta da rade s njima. Kod aplikacije će razumeti kako da se nosi sa greškama, ali ne može uvek da ih otkrije. Usput, izuzeci nisu ograničeni na greške. Programer može da koristi rukovanje izuzetcima za upravljanje svim vrstama promenljivih arhitektura mašina konačnog stanja. U praksi, međutim, mi o izuzecima razmišljamo kao o uslovima greške, ili barem kao o neočekivanim uslovima zbog kojih program pravi neophodna bočna putovanja. Osnovna teorija je da funkcija, koja može biti duboko zakopana za mnoge pozive funkcija i koja može biti skrivena u biblioteci, detektuje izuzetak sa kojim ne može da se bavi i da mora prijaviti aplikaciji na površini, koja mora da se bavi izuzetkom.

Razmislite o funkciji matematičke biblioteke koja detektuje prelivanje, nedovoljno prelivanje, deljenje na nulu i druge uslove greške u svojim ulaznim podacima. Da li je greška uzrokovana podacima koje je uneo korisnik ili ona koju je program interno izračunao? Da li je to uslov validacije ili greška u programu? Funkcija biblioteke ne zna, tako da odgovarajuća strategija za rukovanje greškama zavisi od aplikacije. Program može prikazati poruku o grešci na konzoli ili u okviru za dijalog; može tražiti od korisnika da unese bolje podatke; moglo bi se prekinuti. Funkcije biblioteke ne bi trebalo da pretpostavljaju da znaju najbolje strategije za rukovanje izuzetcima za sve izuzetke u svim aplikacijama. S druge strane, aplikacije ne mogu znati kako da otkriju sve moguće izuzetke.

Ova podela odgovornosti za otkrivanje grešaka, izveštavanje i rukovanje nije nova. C biblioteke to podržavaju godinama. Rukovanje izuzecima u C++ dodaje strategiji uredan način da detektor izuzetaka prijavi izuzetak rukovaocu izuzetkom. Nekako, funkcija detekcije mora vratiti kontrolu funkciji rukovanja kroz uredan niz funkcija vraćanja. Funkcija otkrivanja može biti duboka za mnoge pozive funkcija. Uredno vraćanje na viši nivo funkcije rukovaoca zahteva, u najmanju ruku, koordinisano resetovanje steka.

### Rukovanje izuzecima u C

C programi obično testiraju povratne vrednosti bibliotečke funkcije za greške i koriste setjmp i longjmp za upravljanje izuzecima. Prvi pristup, koji koristi stvari kao što su errno i povratne vrednosti funkcije NULL ili ERROR, je pouzdan, ali dosadan. Programeri ponekad izbegavaju ili previđaju neke od mogućnosti. Setjmp/longjmp ​​pristup je bliži onome što C++ rukovanje izuzetcima radi: obezbeđuje uredan i automatski način za resetovanje steka u njegovo stanje na određenom mestu višem u hijerarhiji poziva funkcije.

Na primer, provera sintakse kompajlera može biti mnogo nivoa duboko u rekurzivnom silaznom parseru kada otkrije sintaksičku grešku. Kompajler ne mora da prekine. Umesto toga, želi da prijavi grešku i vrati se tamo gde može da pročita sledeći red koda i nastavi. Program koristi setjmp da identifikuje to mesto i longjmp ​​da dođe do njega. Longjmp ​​poziv resetuje stek u njegovo stanje kao što je zabeleženo u jmp_buf pozivom setjmp. Početni setjmp poziv vraća 0. Longjmp ​​poziv skače na povratak iz setjmp, zbog čega se čini da setjmp vraća navedeni kod greške, koji bi trebalo da bude različit od nule.

Međutim, u ovoj šemi postoje anomalije. Kao što ćete uskoro naučiti, C++ rukovanje izuzetcima takođe ne rešava njih. Razmotrite funkciju kao što je Primer 1. Zaboravite za trenutak da bi pravi program testirao izuzetke za fopen i malloc. Dva poziva predstavljaju resurse koje program dobija pre i oslobađa nakon longjmp. Pozivi mogu biti u funkcijama koje se (1) pozivaju nakon operacije setjmp, a (2) koje se same nazivaju funkcijom raščlanjivanja. Poenta je da se longjmp ​​javlja pre nego što se ti resursi oslobode. Stoga, svaki izuzetak koji ovaj program detektuje predstavlja dva izgubljena sistemska resursa – segment gomile i rukohvat datoteke. U slučaju FILE* resursa, naknadni pokušaji otvaranja iste datoteke neće uspeti. Ako bi svaki prolazak kroz sistem otvorio drugu datoteku – na primer, privremenu datoteku sa imenom datoteke koje je generisao sistem – program bi otkazao kada bi operativni sistem ostao bez rukova za datoteke. Mi programeri tradicionalno rešavamo ovaj problem tako što strukturišemo naše programe kako bismo ga izbegli. Ili upravljamo i čistimo resurse pre pozivanja longjmp, ili ne koristimo longjmp ​​kada su privremeni resursi u opasnosti. U primeru 1, problem je rešen pomeranjem longjmp ​​ispod slobodnih i fclose poziva. Međutim, nije uvek tako jednostavno.

Resetovanje steka u C programu uključuje resetovanje pokazivača steka na mesto gde je pokazivao kada je setjmp pozvan. jmp_buf čuva sve što program treba da zna da bi to uradio. Ova procedura funkcioniše jer stek sadrži automatske promenljive i povratne adrese funkcije. Resetovanje pokazivača steka odbacuje automatske promenljive i zaboravlja povratne adrese funkcije, što je sve ispravno ponašanje, jer automatske promenljive više nisu potrebne i privremene funkcije neće biti nastavljene.

### Rukovanje izuzecima u C++

Međutim, korišćenje longjmp za odmotavanje steka u C++ programu ne funkcioniše, jer automatske promenljive na steku uključuju objekte klasa, a ti objekti moraju da izvrše svoje funkcije destruktora. Razmotrite primer 2. Bez sumnje, konstruktor za klasu String dodeljuje memoriju za vrednost stringa, a njegov destruktor briše tu memoriju. Ako dođe do izuzetka, destruktor stringa se ne izvršava jer longjmp ​​resetuje stek i skače na setjmp pre nego što objekat String izađe van opsega. Memorija koju koristi String objekat se oslobađa, ali se memorija gomile na koju ukazuje pokazivač u String objektu ne briše jer se destruktor ne izvršava.

Ovo je problem koji rešava C++ rukovanje izuzetcima. Operacija izbacivanja rukovanja izuzetkom - analogna longjmp ​​- odmotava stek na način koji poziva destruktore svih automatskih objekata. Ako se bacanje desi unutar konstruktora automatskog objekta, destruktor objekta se ne poziva, iako se pozivaju destruktori objekata ugrađenih u objekat bacanja.

C++ funkcije koje mogu da otkriju greške i da se oporave od njih se izvršavaju unutar bloka tri koji izgleda kao Primer 3(a). Kod koji se izvršava van bilo kog bloka pokušaja nije u stanju da otkrije ili obradi izuzetke. Pokušajte blokovi mogu biti ugnežđeni. Blok tri obično poziva druge funkcije koje su u stanju da otkriju izuzetke.

Nakon bloka tri sledi obrađivač izuzetaka catch sa listom parametara kao što je prikazano u Primeru 3(b). Može postojati više rukovalaca catch sa različitim listama parametara, jedan rukovalac za drugim u izvornoj datoteci. Svaki rukovalac hvatanjem se razlikuje po svojoj listi parametara. Sam parametar ne mora biti imenovan. Imenovani parametar deklariše objekat, a kod za otkrivanje izuzetka može da prosledi vrednost u parametru. Ako je parametar neimenovan, kod za otkrivanje izuzetaka može skočiti na obrađivač izuzetaka catch samo imenovanjem tipa.

Da bi otkrila izuzetak i prešla na obrađivač catch, C++ funkcija izdaje naredbu throw sa tipom podataka koji se poklapa sa listom parametara odgovarajućeg obrađivača catch. Izjava throw odmotava stek, čisteći sve objekte deklarisane u bloku tri pozivanjem njihovih destruktora. Zatim, throv poziva odgovarajući obrađivač catch, prosleđujući objekat parametra.

Primer 3(c) počinje sve da spaja. U ovom primeru, program ulazi u blok tri, što znači da funkcije koje se pozivaju direktno ili indirektno iz bloka tri mogu izbaciti izuzetke. Drugim rečima, funkcija foo može da izbaci izuzetke, kao i bilo koja funkcija koju poziva foo, i tako dalje.

Funkcija rukovanja izuzetkom catch odmah nakon bloka tri je jedini rukovalac u ovom primeru. Hvata izuzetke koji su izbačeni sa int parametrom.

Obrađivači hvatanja i njihovi odgovarajući izrazi bacanja mogu imati parametar bilo kog tipa. Parametar može biti automatska promenljiva unutar bloka koji koristi throv, čak i ako lista parametara catch specificira referencu. U ovom slučaju, naredba throv gradi privremeni objekat za prosleđivanje rukovaocu catch. Automatskom objektu u funkciji bacanja je dozvoljeno da izađe van dometa. Privremeni objekat se ne uništava sve dok rukovalac catch ne završi obradu.

Kada blok tri ima više od jednog rukovaoca catch, bacanje izvršava onaj koji odgovara listi parametara. Taj rukovalac je jedini koji se izvršava osim ako ne izbaci izuzetak za izvršavanje drugog rukovaoca hvatanja. Kada izvršni rukovalac catch izađe, program nastavlja sa kodom koji sledi poslednji rukovalac catch.

Možete navesti izuzetke koje funkcija može da izbaci kada deklarišete funkciju kao što je prikazano u Primeru 3(d). Ako funkcija uključuje takvu specifikaciju izuzetka, a funkcija izbaci izuzetak koji nije dat u specifikaciji, izuzetak se prosleđuje sistemskoj funkciji pod nazivom unekpected(). Neočekivana funkcija poziva najnoviju funkciju imenovanu kao argument u pozivu funkcije set_unekpected, koja vraća njenu trenutnu postavku. Funkcija bez specifikacije izuzetka može da izbaci bilo koji izuzetak.

Rukovalac hvatanjem sa elipsama za listu parametara hvata sve izuzetke. U grupi obrađivača hvatanja koji su povezani sa blokom pokušaja, rukovalac za hvatanje svega mora da se pojavi poslednji.

Možete kodirati bacanje bez operanda u rukovaocu catch ili u funkciji koju poziva jedan. Izbacivanje bez operanda ponovo baca originalni izuzetak.

Neuhvaćeni izuzetak je onaj za koji nije naveden rukovalac hvatanjem ili onaj koji je izbacio destruktor koji se izvršava kao rezultat drugog bacanja. Takav izuzetak uzrokuje pozivanje funkcije terminate. Možete odrediti funkciju za terminate da pozovete pozivanjem funkcije set_terminate, koja vraća njenu trenutnu vrednost. Ako nije izvršen poziv funkcije set_terminate, prekidni pozivi se prekidaju.

U svom dizajnu morate da odlučite kako da razlikujete izuzetke i kodirate rukovaoce hvatanjem sa njihovim listama parametara za razlikovanje. Možete kodirati samo jedan obrađivač catch sa int parametrom i dozvoliti da vrednost parametra odredi tip greške. Ovaj pristup pretpostavlja da imate kontrolu nad svim bacanjima u svim funkcijama u svim bibliotekama koje koristite.

Nema sumnje da će se konvencije pojaviti. Jedan od načina je korišćenje definicija klasa za razlikovanje izuzetaka i kategorija izuzetaka. Bacanje sa javno izvedenom klasom kao parametrom je uhvaćeno od strane catch rukovaoca sa osnovnom klasom kao parametrom. Razmotrite primer 4. FileError je javna virtuelna bazna klasa. Njegove izvedene klase su NotFound i Locked. Jedini obrađivač hvatanja za ovu kategoriju izuzetaka je onaj sa referentnim parametrom FileError. Ne zna koji od izuzetaka je izbačen, ali poziva HandleEkception čistu virtuelnu funkciju, koja automatski poziva odgovarajuću funkciju u izvedenoj klasi. Imajte na umu da ne preporučujem ovaj određeni skup izuzetaka za fstream biblioteku; Ja samo koristim primer da ilustrujem tehniku. Možda postoje dobri razlozi da se izbegne bacanje izuzetaka iz standardnih biblioteka strimova. Ja ću se pozabaviti tom zabrinutošću kasnije u ovoj diskusiji.

Postoje i drugi načini za nabrajanje izuzetaka. Umesto klasa, možete koristiti nabrojane tipove i imati naredbe svitch u rukovaocima catch. Izdavači biblioteka će dokumentovati konvencije koje koriste za izbacivanje izuzetaka, a vaši rukovaoci catch-om će koristiti te konvencije, možda koristeći nekoliko različitih konvencija u jednoj aplikaciji. Na kraju se moraju pojaviti standardi. Ako to ne urade, zavladaće haos, a mi ćemo se podsetiti na kolizije imena funkcija i promenljivih koje stvaraju mnoge nekompatibilne biblioteke C funkcija.

Podsetite se ranije diskusije o anomaliji setjmp/longjmp ​​sa neobjavljenim resursima. C++ rukovanje izuzetcima ne rešava taj problem. Razmotrimo uslov iz Primera 5 gde je objekat String dodeljen iz gomile pomoću operatora nev. Ako se izbaci izuzetak, operacija brisanja se ne izvodi. U ovom slučaju postoje dve komplikacije. Memorija dodeljena na hrpi za String objekat se ne oslobađa, a njegov destruktor se ne poziva, što znači da se gubi i memorija koju je konstruktor dodelio za vrednost stringa.

Isti problem postoji sa visećim, otvorenim ručkama datoteka, nezatvorenim prozorima ekrana i drugim sličnim sistemskim resursima. Ako vam se čini da je upravo prikazani program lako popraviti, zapamtite da se dobacivanje može dogoditi iz bibliotečke funkcije daleko u gomilu ugnežđenih poziva funkcija.

Predloženi su programski idiomi koji rešavaju ovaj problem. U drugom izdanju svoje knjige, dr Stroustrup sugeriše da se svim takvim resursima može upravljati iz automatskih instanci klasa upravljanja resursima. Njihovi destruktori bi pustili sve dok bacanje odmotava stek. Drugi pristup je da se svi dinamički pokazivači gomile i ručke datoteka i prozora postanu globalni kako bi rukovalac hvatanjem mogao sve da očisti. Međutim, ove metode zvuče glomazno i rade samo ako sve funkcije u svim bibliotekama sarađuju.

### Izuzeci u standardnoj C++ biblioteci

Odbor za standarde ANSI X3J16 C++ usvaja i prilagođava Stroustrup specifikaciju za izuzetke. Izuzeci imaju značajan uticaj na bibliotečki komitet X3J11, jer se kreću ka standardu koji savija rukovanje izuzetcima u one bibliotečke funkcije koje mogu da otkriju izuzetke. Ispravno se tvrdi da je jezik koji uključuje strategije rukovanja izuzetcima kao integralnu komponentu jezika u lošem društvu ako njegova biblioteka ne koristi dobro ovu funkciju.

Ali razmislite o implikacijama biblioteke klasa ili funkcija koje izbacuju izuzetke. Programi koji koriste tu biblioteku ne mogu zanemariti izuzetke osim ako je prihvatljivo da se prekinu kada se izuzeci pojave. To su i dobre i loše vesti. Programi, po pravilu, ne bi trebalo da ignorišu izuzetke, a takva biblioteka to pravilo može da primeni. Programi koji dodeljuju memoriju iz gomile bez testiranja, na primer, treba da očekuju da će biti izbačeni ako je gomila iscrpljena. U starim danima, ti programi su se odvijali sve dok dereferenciranje nultog pokazivača nije uzelo gadan danak. Međutim, sa novim operaterom koji zna za izuzetke, ovi programi će se prekinuti ako se ne trude da uhvate izuzetak. Programeri koji se protive da novi operator izbaci izuzetak uvek mogu da koriste set_nev_handler da zaobiđu ponašanje.

Mislim da su to bile dobre vesti. Pa šta je loše?

Primer iscrpljivanja gomile je onaj koji se obično koristi za opravdavanje biblioteka koje bacaju izuzetke. Postoje, međutim, legitimni programski idiomi koji ignorišu – ili barem odlažu – upravljanje nekim izuzecima. Na primer, možda nećete uvek želeti da odmah nakon otvaranja datoteke testirate da datoteka ne postoji. Ranije opisan primer FileError uradio je upravo to, ali to možda nije ono što želite da uradite. Štaviše, ako je biblioteka za izbacivanje izuzetaka nova verzija starije koja nije izbacivala izuzetke, postojeći kod koji koristi biblioteku može da se pokvari.

Brinemo o tome da X3J16 komitet izmišlja jezik jer njihov prethodni odbor X3J11 C to nije trebalo da uradi. Prethodno stanje tehnike je najbolji način da se testira održivost nove i veoma složene jezičke karakteristike. Ako radi u rovovima, opraće se u standardu. Obrnuto možda nije tačno. Shvatajući to, bibliotečki odbor Ks3J16 pažljivo primenjuje ovaj koncept. Glavna pitanja su: Koje bibliotečke funkcije bi trebalo da dovedu izuzetke; i koje izuzetke treba da dovedu. Jedna krajnost bi potpuno ignorisala rukovanje izuzetcima, unapređujući C tradiciju gde su programeri odgovorni za sve, a programi koji ignorišu izuzetke padaju. Druga krajnost bi izazvala izuzetke za sve promenljive uslove koje detektuju sve funkcije biblioteke, u kom slučaju bismo mogli i da promenimo ime glavne funkcije da bismo pokušali. Negde između je nirvana, a komisija za njom traga.

### Posledice rukovanja izuzecima

Rukovanje izuzecima kao karakteristika jezika C++ ima posledice po C++ programere jer menja jezik. Međutim, to nije planirana, dobro integrisana promena. To je dodatak, dodatna oprema, naknadna misao. Zbog svog nasleđa, C++ već ima konstrukcije koje imaju tendenciju da ometaju ono što rukovanje izuzetcima pokušava da uradi, što dovodi do izmišljenih rešenja kao što su klase za upravljanje resursima koje je predložio Stroustrup, rešenja kojima je potrebna stalna negovanje i pažnja da bi bili zdravi.

Pored dodatnog tereta razumevanja koji se nameće programeru, postoji i kazna performansi koju neadekvatna implementacija kompajlera može da nanese. Čovek se zapita kako će ova stvar funkcionisati. Da bi pravilno odmotao stek, kod izvođenja treba da održava trag od vraćanja nazad na gore kroz sve povratke funkcije i blokira izlaze do pokušaja tako da može pozvati automatske destruktore objekata u uređenom nizu. Da li svaki poziv svakoj funkciji i svaki izlaz iz svakog bloka treba da se testira da bi se videlo da li treba da se nastavi normalno ili mu bacanje govori da ignoriše sve što neposredno sledi i nastavi ka sopstvenom izlazu? Spoljna aplikacija ne može imati koristi čak ni od pretpostavljenog znanja kompajlera da se kod izvršava van bloka tri jer je svaka funkcija osim glavne podložna pozivu iz jedne, a vezivanje se dešava u vreme izvršavanja, a ne u vreme kompajliranja.

Šta je sa bibliotečkim funkcijama? Hoće li svi imati opuštanje iznad glave? Čini mi se da bi morali. Šta je sa eksternim "C" funkcijama? Kako će kompajleri biti dovoljno pametni da znaju da im se ne vraćaju? Oni sigurno neće imati testove odmotavanja za rukovanje izuzetcima i nastavili bi da veselo obrađuju kao da se izuzetak nije dogodio. Da li će C programeri platiti cenu zato što strcmp treba da učestvuje u rukovanju C++ izuzecima? Nadam se da nije. Ne možete jednostavno resetovati stek svake C funkcije na način na koji to radi longjmp. C funkcija može pozvati C++ funkciju preko pokazivača povratnog poziva, a toj C++ funkciji je potrebno odmotavanje čak i ako C funkcija to ne čini.

Ako stek ne odmotate kontrolisanim nizom vraćanja nagore kroz stablo funkcija, onda svaka instancija automatskog objekta mora biti snimljena negde u skoro globalnom prostoru tako da bacanje može da pozove destruktore. To samo po sebi predstavlja značajne troškove jer dodaje skrivene procese konstruktorima. Može li kompajler iz odsustva deklarisanog destruktora zaključiti da klasi nije potrebno uništavanje tokom odmotavanja? Ovo su, naravno, vijuge neupućenog autsajdera. Ne znam kako da implementiram funkciju jer nisam pokušao da rešim problem. Imam problema da pronađem pouzdan način da to učinim da radi loše, mnogo manje efikasno. Realizatori će se bez sumnje boriti sa svim ovim pitanjima. Bjarne Stroustrup kaže da je implementirao rukovanje izuzetcima u laboratoriji pre nego što je predložio njegovu specifikaciju u ARM-u. Vatcom ima kompajler koji ga sada implementira. Koliko dobro ova poboljšanja funkcionišu, ostaje da se vidi.

Nadam se da dobro funkcionišu jer je koncept rukovanja izuzetcima dobra stvar. Moramo da ga razumemo da bismo mogli da ga iskoristimo u našim dizajnima. Moramo da proširimo naše razumevanje biblioteka koje koristimo da bismo znali koje izuzetke donose i šta naši programi treba da urade u vezi sa njima. Moramo da analiziramo strategiju izuzetaka svake biblioteke kako bismo osigurali da nepažljivi dizajneri biblioteka ne stvore potencijalne kolizije parametara koji identifikuju izuzetak. Moramo da budemo svesni da izuzeci koji su bačeni negde u biblioteci mogu da zaokupe dinamičke resurse osim ako pažljivo ne planiramo svaku takvu okolnost. Konačno, moramo budno paziti na nove verzije isprobanih i istinitih biblioteka koje sada bacaju izuzetke tamo gde nikada ranije nisu radili.

Example 1:

```c
void foo()
{
    FILE *fp = fopen(fname, "rt");
    char *cp = malloc(1000);
    /* parse the input */
    /* ... */
    if (error)
        longjmp(jb, ErrorCode);
    free(cp);
    fclose(fp);
}
```

Example 2:

```c
void parse()
{
    String str("Parsing now");
    // parse the input
    // ...
    if (error)
        longjmp(jb, ErrorCode);
}
```

Example 3:

```c
(a)
try  {
    // C++ statements
}

try  {
    // C++ statements
}
catch(int err)   {
    // error-handling code
}

(c)
void main()
{
    // --- the try block
    try  {
        foo(); // call a lower function
    }
    // --- catch exception handler
    catch(int errcode)   {
        // error-handling code
    }
}

// --- program function
void foo()
{
    // C++ statements to do stuff
    if (error)
        throw -1;
}

(d)
void f() throw(char *, int)
```

Example 4:

```c
class FileError {
public:
    virtual void HandleException() = 0;
};

class Locked : public FileError {
public:
    void HandleException();
};

class NotFound : public FileError {
public:
    void HandleException();
};

void bar()
{
    try  {
        foo();
    }
    catch(FileError& fe)   {
        fe.HandleException();
    }
}

void foo()
{
    // ...
    if (file_locked)
        throw Locked();
}

Example 5:

void foo()
{
    String *str = new String("Hello, Dolly");
    // ...
    if (file_locked)
        throw Locked();
    delete str;
}
```
