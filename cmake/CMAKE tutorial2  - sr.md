
# CMake tutorijal: Izgradnja vašeg prvog C++ projekta i više

Od Hansa de Ruitera 23.05.2025., CMake, C/C++

CMake je moćan alat za pretvaranje C/C++ projekata u funkcionalne aplikacije, ali učenje njegovog korišćenja može biti teško. Veoma je složen i okružen morem loših ili zastarelih tutorijala.

Mnogo programera na kraju mrzi CMake, uključujući i mene. Izbegavao sam ga kao kugu, čak sam pisao sopstvene skripte za zamenu samo da bih izbegao njegovo korišćenje.

Onda jednog dana nešto je kliknulo, i shvatio sam kako da ga koristim na pravi način – i to je promenilo sve.

Ako ste C++ programer (ili ambiciozni programer) i mučite se da počnete sa CMake-om kao što sam se ja mučio — ili ste jednostavno umorni od kopiranja i lepljenja zbunjujućih "CMakeLists.txt" datoteka — onda je ovaj tutorijal za vas. Brzo ćemo napisati vaš prvi CMake skript za izgradnju, a zatim ćemo odatle graditi stvari.

Bez obzira da li ste početnik ili želite da unapredite svoje veštine, ovaj CMake tutorijal će vam pružiti solidnu osnovu — a ako ste spremni da idete dalje, zasnovan je na delu moje kompletne knjige/kursa: CMake tutorijal.

## Šta je CMake?

Ljudi nazivaju CMake "sistemom za izgradnju meta-korisničkih sadržaja na više platformi". Zvuči komplikovano, zar ne? Ali je zapravo prilično jednostavno.

CMake ne kompajlira vaš kod direktno. Umesto toga, generiše izvorne datoteke za izgradnju (ili skripte) za bilo koji kompajler sistema za izgradnju koji koristite, a zatim to koristi za stvarnu izgradnju.

- Na Linux-u može generisati "Makefile" datoteke za GCC.
- Na Windows-u može generisati "Visual Studio" projektne datoteke.
- Na MacOS-u može generisati "XCode" projekte.

Ili možete tačno navesti koje alate želite da koristite.

Ideja je sledeća: napišete jedan CMake skript za izgradnju, a CMake se brine o generisanju odgovarajućih podešavanja za izgradnju za platformu na kojoj se nalazite. To znači da možete da izgradite svoj C++ projekat na Linux-u, Windows-u, MacOS-u — ili čak na sva tri — koristeći isti "CMakeLists.txt" skript.

Zašto prolaziti kroz ovaj dodatni korak? Zato što vam omogućava da kreirate kros-platformske verzije bez potrebe da prepisujete logiku verzije za svaki operativni sistem ili kompajler. Naravno, vaš stvarni C++ kod i dalje mora da podržava više platformi — ali to je druga priča.

CMake-ova sposobnost da gradi kod na širokom spektru operativnih sistema i kompajlera je verovatno razlog zašto je postao de fakto standardni sistem za izgradnju. Ne postoji zvanični sistem za izgradnju u C++ ekosistemu, ali je CMake toliko široko usvojen da je gotovo sigurno da ćete ga naći u stvarnim projektima.

## Zašto je CMake zbunjujući i kako to popraviti

Ako ste ikada pokušali da naučite CMake i osećali se izgubljeno, niste sami. CMake može delovati kao zbrka kriptične sintakse, zbunjujućih poruka o greškama, zastarelih primera i debagovanja metodom pokušaja i grešaka.

Mnogo stvari koje stoje na putu, uključujući:

- Skriptni jezik koji je jedinstven za CMake,
- Dokumentacija koju je često teško pratiti,
- I gomila zastarelih saveta kruži po internetu…

Međutim, to su samo doprinosni faktori. Evo pravog problema:

> CMake se razlikuje od onoga što većina programera očekuje.

Umesto direktnog kompajliranja izvornih datoteka kao što biste to uradili sa Makefile-om ili skriptom, sa CMake-om opisujete svoju kompajlaciju na višem, apstraktnom nivou. Ne kažete "kompajliraj main.cpp". Kažete "evo liste izvornih datoteka, napravite mi izvršnu datoteku".

To je suptilna promena u razmišljanju — ali kada se jednom shvati, sve postaje mnogo lakše.
Rešenje: Počnite sa pravim mentalnim modelom.

Ključ za učenje CMake-a je razumevanje šta pokušava da uradi. CMake-ov posao je da generiše instrukcije za izgradnju za vaš kompajler i platformu — a ne da ih zameni. Kada shvatite da pišete plan, a ne stvarne korake izgradnje, stvari počinju da se sklapaju.

Odatle, samo treba da naučite:

- Osnovnu strukturu datoteke "CMakeLists.txt",
- Kako definisati "ciljeve izgradnje" (npr. izvršne datoteke koje želite da izgradite),
- Kako se povezati sa bibliotekama trećih strana.

I to je upravo ono kroz šta će vas ovaj tutorijal provesti — korak po korak, bez suvišnih detalja ili zabune.

## Šta mi je potrebno da bih naučio CMake?

- Računar koji može da pokreće CMake, očigledno. Standardna mašina sa Windows-om, Linux-om ili MacOS X-om će biti dovoljna. Nešto poput Raspberry Pi-ja je takođe opcija.
- Internet konekcija (za preuzimanje CMake-a i drugog softvera koji nam je potreban)
- Spremnost za učenje

To je to. Ne morate znati kako se programira u C/C++, iako će vam svakako pomoći. C/C++ je nešto što ćete želeti da naučite, jer CMake nije baš koristan ako ne umete da pišete kod.
Korak-po-korak vodič
Instaliranje CMake-a i podešavanje razvojnog okruženja

Želite da pišete softver, zar ne? Za to će vam biti potrebni neki alati. Ne samo CMake. Potrebno vam je kompletno C/C++ razvojno okruženje:

- Uređivač koda ili integrisano razvojno okruženje (IDE)
- AC/C++ kompajler
- CMake

Preporučujem Visual Studio Code (VS Code) kao IDE, jer je prilagođen početnicima i radi na svim uobičajenim platformama (Windows, MacOS X i Linux). Ako više volite drugi IDE, onda koristite koji god želite.

Instalacija se razlikuje na svakom sistemu. Ukratko, evo šta treba da instalirate:

- Visual Studio Code – Preuzmite ovde ili pratite uputstva za instalaciju za vašu platformu
- Instalirajte VS Code-ov "C/C++ paket proširenja“ – Kliknite na dugme Proširenja ( Dugme za proširenja VS koda) u levoj koloni i unesite "C/C++ paket proširenja“ u traku za pretragu (bez navodnika). Izaberite paket proširenja, kliknite na plavo dugme "Instaliraj" i instaliraće se sve što vam je potrebno.
- CMake – Preuzmite i instalirajte ga sa ove stranice. Korisnici Linuksa mogu ga instalirati koristeći nešto poput `sudo apt install cmake`

Jedan C/C++ kompajler

- Na Windows-u preuzmite i instalirajte "Build Tools for Visual Studio"
- Na MacOS X, instalirajte "XCode". To se može uraditi iz terminala unosom sledeće komande: `xcode-select --install`
- Na Linux-u i Raspberry Pi-ju, instalirajte GCC kompajler.
  
Tačne komande koje treba uneti u terminal zavise od vaše Linux distribucije:  

- Za Debijan/Ubuntu:`sudo apt-get install build-essential gdb cmake`
- Za RHEL, Fedora i CentOS:`sudo dnf install code gcc gcc-c++ kernel-devel make gdb cmake`
- Na Raspberry Pi-ju: odjednom, sa: `sudo apt-get install code build-essential gdb cmake`  
  **NAPOMENA**  
  Gornja linija instalira sve; VS Code, CMake i kompajler. Sve je uključeno.

Ako su vam potrebna detaljnija uputstva, detaljna uputstva za Windows, MacOS X, Linux i Raspberry Pi nalaze se u CMake tutorijalu.

**SAVET**  
Ne treba vam cela knjiga/kurs; samo preuzmite besplatni uzorak. Kliknite na dugme "besplatni uzorak" na ovoj stranici i preuzmite uzorak knjige.

## Pisanje vaše prve "CMakeLists.txt" datoteke

Verujem u postizanje rezultata što pre, zato hajde da počnemo. Napravite novi direktorijum pod nazivom "Hello-CMake" (stavite ga gde god želite da čuvate svoje softverske projekte). Zatim otvorite VS Code i izaberite File ⇒ Open Folder iz menija, direktorijum "Hello-CMake".

Svaki projekat zahteva najmanje dve stavke:

- Barem jedna izvorna datoteka koja sadrži kod programa,
- Skripta za izgradnju koja sadrži uputstva o tome kako se izgradi projekat.

Počnimo sa izvornim fajlom. Izaberite File ⇒ New File, iz menija i nazovite novi fajl "Main.cpp".

Unesite sledeće u novu datoteku (tj. "Main.cpp"):

```c++
/** Hello CMake
 * 
 *  A simple hello world program.
 */
 
#include <cstdio>
#include <cstdlib>
 
int main(int argc, const char **argv) {
    printf("Hello CMake!\n");
 
    return EXIT_SUCCESS;
}
```

Trebalo bi da možete da pogodite šta ovaj program radi čak i ako nikada ranije niste videli C++ kod. Postoji `printf()` izjava koja ispisuje "Zdravo CMake!". To je sve što treba da znate za sada.

Sada želimo da kompajliramo "Main.cpp" u program koji se može izvršiti. Dakle, kreirajte novu datoteku pod nazivom "CMakeLists.txt" i unesite sledeće u datoteku:

```sh
cmake_minimum_required(VERSION 3.24)
project(hello_cmake)

add_executable(${PROJECT_NAME} Main.cpp)
```

**OPREZ**  
Imena datoteka razlikuju velika i mala slova na operativnim sistemima kao što je Linux, zato se uverite da je Main.cpp tačno napisano. U suprotnom, doći će do greške koja može biti zbunjujuća (npr. "Izvorni direktorijum 'somedir' ne sadrži CMakeLists.txt“). Ako dobijete takvu grešku, proverite da li ste napisali ispravna velika slova u "CMakeLists.txt".

Ovo je verovatno najkraći CMake skript koji ćete ikada napisati. Hajde da ga pregledamo red po red:

```sh
cmake_minimum_required(VERSION 3.24)
```

Prvi red iznad postavlja minimalnu verziju CMake-a koja je potrebna za izgradnju našeg projekta. Ovo osigurava da su sve funkcije koje su potrebne skripti za izgradnju dostupne. Zapravo nam nije potrebna novija verzija za ovaj jednostavan projekat, ali ćemo koristiti moderni CMake za sve što radimo od sada pa nadalje. Dakle, hajde da se držimo novije verzije.

**NAPOMENA**  
Često pitanje početnika je "koju minimalnu verziju da izaberem?". Molimo vas da se ne mučite oko toga koja je verzija apsolutni minimum potreban. Vaše vreme je vrednije od toga. Dobra početna tačka je bilo koja verzija CMake-a koju trenutno koristite (ukucajte `cmake --version`). Na taj način minimalna verzija podržava sve funkcije koje su vam trenutno dostupne.

Sledeći red deklariše ime našeg projekta:

```sh
project(hello_cmake)
```

CMake će podesiti `PROJECT_NAME` promenljivu na ime koje ovde dajemo.

```sh
add_executable(${PROJECT_NAME} Main.cpp)
```

Poslednja linija dodaje izlaznu izvršnu datoteku "target" projektu "hello_cmake" koji se gradi iz jedne izvorne datoteke: "Main.cpp".  

**NAPOMENA**  
"Izvršni program" označava računarski program koji se može izvršiti (tj. pokrenuti).

## Kreiranje i pokretanje vašeg prvog programa u VS Code-u

Iz nekog razloga, VS Code mora biti resetovan pre nego što primeti CMake dodatak i koristi novu "CMakeLists.txt" datoteku. Dakle, izaberite iz menija File ⇒ Close Folder, File ⇒ Open Recent ⇒ "Hello-CMake".

**NAPOMENA**  
Podmeni "Otvori nedavno korišćene funkcije" će prikazati punu putanju do vašeg foldera "Hello-CMake" umesto samo "Hello-CMake".

CMake kontrole se nalaze u plavoj traci na dnu prozora:

**CMake Toolbar**  
Kontrole su:

- Dugme za izgradnju ( Dugme za izgradnju u VS Code CMake Toolbar-u)
- Dugme za otklanjanje grešaka ( Dugme za otklanjanje grešaka na traci sa alatkama VS Code CMake )
- Dugme za pokretanje ( Dugme za pokretanje u traci sa alatkama CMake u VS Code-u)

Kliknite na dugme "Pokreni" ( Dugme za pokretanje u traci sa alatkama CMake u VS Code-u ). Trebalo bi da vidite tekst koji se pomera u podprozoru "Izlaz" direktno iznad plave trake. To je CMake koji gradi "hello_cmake".

**NAPOMENA**  
Nema potrebe da kliknete na dugme za izgradnju, jer će VS Code automatski izgraditi program ako je potrebno (npr., ako ste promenili jednu od skripti za izgradnju ili izvornih datoteka).

Ako sve bude u redu, prebaciće se na karticu "Terminal" kada se kompilacija završi i videćete "Zdravo CMake!". Čestitamo na kompilaciji vašeg prvog programa pomoću CMake-a!

## Izgradnja iz komandne linije

Iako ćemo sve raditi iz VS Code-a, važno je da znate šta se dešava "ispod haube". Zato hajde da ponovo izgradimo i pokrenemo "hello_cmake", ali iz komandne linije.

Ako pogledate uputstva za izgradnju tipičnog CMake projekta, obično će vam reći da unesete sledeće iz komandne linije:

```sh
cmake -B build
cd build
cmake --build . --parallel
```

Prvi red kreira direktorijum za izgradnju i konfiguriše projekat. Drugi red vrši samu izgradnju. Kasnije ćemo objasniti zašto se CMake pokreće dva puta.

Prvo, pogledajte u "Explorer" i primetite kako vaš projekat sada ima fasciklu "build" (pogledajte dole). Tu se nalaze sve datoteke za izgradnju. Kliknite na nju da biste videli šta je unutra. Videćete gomilu datoteka i direktorijuma. To je pomalo nered. Nered koji želite da držite podalje od svog korenskog direktorijuma. Zato smo napravili poseban direktorijum za izgradnju za to.

## Direktorijum za izgradnju CMake-a

Hajde da odmah ručno izgradimo "hello_cmake". Zatvorite VS Code, obrišite direktorijum za izgradnju, a zatim otvorite prozor terminala. Promenite direktorijum u vaš "hello_cmake" projekat, npr:

```sh
cd /path/to/hello_cmake
```

**VAŽNO**  
Potrebno je da promenite /path/to/hello_cmake direktorijum u koji ste stavili hello_cmake.

Zatim, unesite "standardne" komande za izgradnju u prozor terminala:

```sh
cmake -B build
cd build
cmake --build . --parallel
```

**SAVET**  
Možete preskočiti `cd build` komandu i `cmake --build build --parallel` umesto nje koristiti. Prikazujem je u tri komande kako bi bilo jasno da je kreiran direktorijum za izgradnju. Prelazak na direktorijum za izgradnju može vam biti pogodan i za potrebe testiranja.

CMake bi trebalo da ispiše neke informacije o tome šta radi. Kada to završi, trebalo bi da budete u mogućnosti da pokrenete "hello_cmake" unosom "./hello_cmake" i pritiskom na Enter. Ako to ne funkcioniše, pokušajte "Debug/hello_cmake" (ili "Debug\hello_cmake" na Windows-u). Na Linux-u ćete možda morati da koristite "./hello_cmake" umesto toga. "./" označava da bi trebalo da traži "hello_cmake" u trenutnom direktorijumu.

**NAPOMENA**: Neki kompajleri generišu odvojene Debug i Release verzije (npr. Visual Studio i XCode), dok drugi (kao što je GNU Make) to ne čine. Otuda i gornja razlika. Odvojene debug i release verzije završavaju u svojim poddirektorijumima.

Zašto smo morali dva puta da pokrenemo CMake? Prvi put generišemo direktorijum za izgradnju i skriptu za izgradnju:

```sh
cmake -B build
```

Sledeći red vrši stvarnu izgradnju:

```sh
cmake --build . --parallel
```

Jedna tačka ( `.` ) je skraćenica za trenutni direktorijum (tj. direktorijum za izgradnju). Ova `--parallel` opcija omogućava paralelnu izgradnju. Ovo ubrzava izgradnju na višejezgarnim sistemima, jer možete dobiti sva jezgra koja kompajliraju kod umesto samo jednog.

**SAVET**  
NE morate svaki put da izvršavate sve linije. Kada se generišu direktorijum za izgradnju i skripte, `cmake --build . --parallel` je potrebno samo izvršiti. Promene u "CMakeLists.txt" će biti automatski detektovane, što će pokrenuti ponovno pokretanje procesa konfiguracije.

## Kompajliranje više izvornih datoteka u jedan program

Kako programi postaju veći, jedna izvorna datoteka postaje glomazna. Toliko skrolovanja... I teško je održavati stvari organizovanim. Kod postaje velika međusobno povezana zbrka, odnosno „špageti kod“. Dakle, veliki programi se dele na module sačuvane u odvojenim datotekama; svaki ima svoju posebnu ulogu.

Kako da pretvorimo više izvornih datoteka u jedan program? Jednostavna modifikacija naše "CMakeLists.txt" datoteke će završiti posao (one "CMakeLists.txt" koju smo kreirali u prethodnom poglavlju, to jest).

Prvo, međutim, moramo da napišemo program sa više izvornih datoteka. Da bismo pojednostavili stvari, podelićemo naš "Hello CMake" program na dva modula: "Main" i "Hello". Da, dosadno je, ali ovde učimo CMake, a ne C++. Zato, hajde da pojednostavimo stvari, zar ne?

Evo izvornih datoteka:

Hello.cpp:

```c++
/** Hello.cpp
 *
 * See header file for details.
 */
 
#include <cstdio>
 
#include "Hello.h"
 
void printHello(const char *name) {
    printf("Hello %s.\n", name);
}
```

Hello.h:

```c++
/** @file Hello.h
 *
 * Our module for printing hello.
 * Yes, it's pointless. It's also the most basic example of splitting software into 
 * multiple files. If this program did something useful, then this would be a module
 * dedicated to a specific task (e.g., drawing graphics on-screen).
 */
 
#pragma once
 
/** Prints hello.
 *
 * @param name the name of whoever we're saying hello to
 */
void printHello(const char *name);
```

Main.cpp:

```c++
/** Main.cpp
 *
 * @author Hans de Ruiter
 */
 
#include "Hello.h"
 
#include <cstdlib>
 
int main(int argc, const char **argv) {
    printHello("cmake");
     
    return EXIT_SUCCESS;
}
```

Napravite tri izvorne datoteke pod nazivom "Hello.cpp", "Hello.h" i "Main.cpp" i dodajte sadržaj iz gornje liste programa. Slobodno napravite kopiju vašeg originalnog projekta "Hello CMake" i izmenite je.

Sada treba da kažemo CMake-u da "hello_cmake" ima dve izvorne datoteke umesto jedne. Mogli biste jednostavno dodati "Hello.cpp" posle "Main.cpp", ali ćete kasnije zažaliti (opet je to problem sa „špageti kodom“). Dakle, umesto toga postavljamo promenljivu pod nazivom `SOURCE_FILES`:

```sh
set(SOURCE_FILES
    Main.cpp
    Hello.cpp)
```

Zatim koristimo `SOURCE_FILES` promenljivu u `add_executable()` pozivu ispod:

```sh
add_executable(${PROJECT_NAME} ${SOURCE_FILES})
```

Evo cele datoteke CMakeLists.txt:

```sh
cmake_minimum_required(VERSION 3.24)
project(hello_cmake)
 
# Our Project
set(SOURCE_FILES
    Main.cpp
    Hello.cpp)
 
add_executable(${PROJECT_NAME} ${SOURCE_FILES})
```

To je to! Kliknite na dugme "Pokreni" ( Dugme za pokretanje u traci sa alatkama CMake u VS Code-u) u plavoj traci sa alatkama. Pod pretpostavkom da ste sve uradili kako treba, program će se izgraditi, pokrenuti i ponovo ćete dobiti isti izlaz "Zdravo CMake!". Ako je nešto pošlo po zlu, pogledajte poruke o grešci u podprozoru "Izlaz", uporedite svoj kod sa mojim iznad i pokušajte ponovo.

**NAPOMENA**
Možda ste primetili dodatni red koji počinje sa `#`. To je komentar umetnut za čitanje ljudima. CMake će ga ignorisati. Koristite takve komentare štedljivo, da biste dali dodatni kontekst kodu.

## Povezivanje sa bibliotekama (tj. zavisnostima)

Niko ne piše 100% koda za aplikaciju sam. To je previše posla. Umesto toga, povezujemo naš kod sa bibliotekama koda koje su napisali drugi (ili mi sami). Želite da nacrtate nešto na ekranu? Onda morate da se povežete sa jednom (ili više) grafičkih biblioteka operativnog sistema (OS). Verovatnije je da ćete se povezati sa bibliotekom „posredničkog softvera“ koja pruža opsežne funkcije crtanja. Posrednički softver se zatim povezuje sa grafičkim bibliotekama OS-a umesto vas.

Korišćenje biblioteka trećih strana može brzo postati komplikovano, jer je to mesto gde vaš kod interaguje sa tuđim. Odnosi su obično komplikovani…

U ovom tutorijalu ću vam pokazati kako da se povežete sa dobro napisanim bibliotekama trećih strana koje dolaze u paketu sa sopstvenim "CMakeLists.tx" tskriptama.

### Povezivanje sa (otvorenim kodom) bibliotekama trećih strana

Stari primeri i tutorijali na internetu će vam reći da koristite `find_package()` za povezivanje sa zavisnostima (kao što su biblioteke trećih strana). Ja kažem, nemojte to da radite! Postoji bolji način koji se zove `FetchContent`.

Iako `find_package()` radi, neće uspeti ako se „paket“ ne pronađe. Tada morate ručno da instalirate paket od kojeg zavisi kod pre nego što možete da nastavite.

Zašto CMake uopšte mora da "pronađe paket"? Zato što ne postoji standardno mesto za instaliranje zavisnosti trećih strana. To je neizbežno dovelo do toga da se datoteke postavljaju svuda. `Find_package()` tražiće zavisnosti na najčešćim lokacijama ili lokacijama koje navedete u posebnim skriptnim datotekama. To je neuredno.

Nasuprot tome, ako paket nedostaje, `FetchContent` preuzeće ga i izgraditi za vas. Nema više traženja i ručnog instaliranja zavisnosti. Možete razumeti zašto preporučujem korišćenje `FetchContent` umesto toga...

Zašto je nazvano `FetchContent` umesto `FetchPackage`? Nemam pojma, žao mi je. CMake-ovo imenovanje funkcija i modula može biti nedosledno. Pokušajte da se ne zamarate previše imenima i umesto toga potražite pravi alat za postizanje svakog cilja. U ovom slučaju:

- `find_package()` traži zavisnost treće strane na vašem računaru i vratiće grešku ako paket nije pronađen
- `FetchContent` prvo će potražiti zavisnost. Ako je ne pronađe, automatski će je preuzeti i izgraditi, tako da vi ne morate.

**NAPOMENA**  
Ovo nije moj preferirani način povezivanja sa bibliotekama trećih strana, ali je veoma zgodan. Više volim da uključim biblioteke trećih strana direktno u moj repozitorijum izvornog koda kako ne bi bilo potrebno ništa dodatno preuzimati sa interneta. Ovo je obrađeno u CMake tutorijalu.

Linkovaću biblioteku `Raylib`. Kreator biblioteke Raylib je Ramon Santamarija („Ray“ u Ray lib), je jednostavna biblioteka za igre/multimediju. Odlična je za početnike jer možete mnogo toga da uradite sa nekoliko linija koda. Na primer, kod ispod će otvoriti prozor koji sadrži poruku "Zdravo svete!":

```c++
/** Main.cpp
 *
 * A simple Raylib example.
 */
 
#include "raylib.h"
#include <cstdlib>
 
int main(int argc, const char **argv) {
    // Initialization
    const int screenWidth = 1280;
    const int screenHeight = 768;
    const char *windowTitle = "Hello world!";
    const char *message = "Hello world! It's great to be here.";
    const int fontSize = 40;
    const float msgSpacing = 1.0f;
 
    InitWindow(screenWidth, screenHeight, windowTitle);
 
    // NOTE: The following only works after calling InitWindow() (i.e,. Raylib is initialized)
    const Font font = GetFontDefault();
    const Vector2 msgSize = MeasureTextEx(font, message, fontSize, msgSpacing);
    const Vector2 msgPos = Vector2{(screenWidth - msgSize.x) / 2, (screenHeight - msgSize.y) / 2};
 
    SetTargetFPS(60);
 
    // Main loop
    while(!WindowShouldClose()) {
 
        // Update the display
        BeginDrawing();
            ClearBackground(RAYWHITE);
            DrawTextEx(font, message, msgPos, fontSize, msgSpacing, RED);
        EndDrawing();
    }
 
    // Cleanup
    CloseWindow();
     
    return EXIT_SUCCESS;
}
```

Mogao bih da skratim ovaj kod tako što bih direktno uneo vrednosti u pozive funkcija umesto da postavljam konstante u odeljku za inicijalizaciju (videti vrh "main()"). Međutim, želim da pokažem dobar stil kodiranja. Uneti vrednosti "magičnim brojevima" je loša ideja, jer je pokušaj sinhronizacije istog broja na više lokacija odličan način za stvaranje haosa sa greškama.

Gornji kod je deo našeg sledećeg projekta. Dakle, kreirajte novi direktorijum projekta i unesite gornji kod u novu datoteku pod nazivom "Main.cpp".

Sada kreirajte "CMakeLists.txt" datoteku i unesite sledeći kod:

```sh
cmake_minimum_required(VERSION 3.24)
project(hello_raylib)
 
# Workaround for CLang and Raylib's compound literals
# See: https://github.com/raysan5/raylib/issues/1343
set(CMAKE_CXX_STANDARD 11)
 
# Dependencies
include(FetchContent)
 
set(RAYLIB_VERSION 4.5.0)
FetchContent_Declare(
    raylib
    URL https://github.com/raysan5/raylib/archive/refs/tags/${RAYLIB_VERSION}.tar.gz
    FIND_PACKAGE_ARGS ${RAYLIB_VERSION} EXACT
)
set(BUILD_EXAMPLES OFF CACHE INTERNAL "")
FetchContent_MakeAvailable(raylib)
 
# Our Project
set(SOURCE_FILES
    Main.cpp)
 
add_executable(${PROJECT_NAME} ${SOURCE_FILES})
target_link_libraries(${PROJECT_NAME} raylib)
 
# Checks if OSX and links appropriate frameworks (Only required on MacOS)
if (APPLE)
    target_link_libraries(${PROJECT_NAME} "-framework IOKit")
    target_link_libraries(${PROJECT_NAME} "-framework Cocoa")
    target_link_libraries(${PROJECT_NAME} "-framework OpenGL")
endif()
```

Trebalo bi da budete u stanju da prepoznate osnovni skelet CMake skripte iz prethodnih poglavlja. Zaglavlje skripte `SOURCE_FILES`, i `add_executable()` delovi se nisu promenili. Uporedite gornju skriptu sa onim koji ste napisali u prethodnom poglavlju i uverite se da možete da identifikujete zajedničke delove.

Postoje četiri oblasti od interesa. Prvo, potrebno je da uključimo `FetchContent` modul:

```sh
# Dependencies
include(FetchContent)
```

To je dovoljno jednostavno. Evo gde stvari postaju komplikovane. Sledeći deo koda dohvata Raylib verziju 4.5.0:

```sh
set(RAYLIB_VERSION 4.5.0)
FetchContent_Declare(
    raylib
    URL https://github.com/raysan5/raylib/archive/refs/tags/${RAYLIB_VERSION}.tar.gz
    FIND_PACKAGE_ARGS ${RAYLIB_VERSION} EXACT
)
set(BUILD_EXAMPLES OFF CACHE INTERNAL "")
FetchContent_MakeAvailable(raylib)
```

Hajde da prođemo kroz ovo red po red. Prvo, postavljamo promenljivu `RAYLIB_VERSION` na verziju koju želimo da koristimo:

```sh
set(RAYLIB_VERSION 4.5.0)
```

**VAŽNO**  
Eksplicitno podešavanje verzije biblioteke osigurava da će svi koji kompajliraju ovaj kod koristiti istu verziju. U suprotnom, moguće je da koristite verziju 4.5.0, a vaš kolega ima verziju 4.2.1 ili 5.1.0, u kom slučaju program može, ali i ne mora da radi ispravno u zavisnosti od toga ko ga je od vas kompajlirao. Nije dobro. To je recept za zbunjujuće probleme i izveštaje o greškama. Toplo preporučujem eksplicitno podešavanje verzije biblioteke.

Zatim, `FetchContent_Declare()` koristi se za deklarisanje paketa koji želimo:

```sh
FetchContent_Declare(
    raylib
    URL https://github.com/raysan5/raylib/archive/refs/tags/${RAYLIB_VERSION}.tar.gz
    FIND_PACKAGE_ARGS ${RAYLIB_VERSION} EXACT
)
```

Gornji kod kaže da želimo paket koji nazivamo `raylib`. Ako nije dostupan na ovoj mašini, onda ga preuzmite sa navedene URL adrese. `FIND_PACKAGE_ARGS` se prosleđuje `find_package()` kada traži `raylib`. Želimo da pronađe `EXACT` verziju koju želimo (tj. `${RAYLIB_VERSION}`).

Obratite pažnju na URL adresu, jer je ona ključ za preuzimanje paketa sa GitHub-a (i drugih repozitorijuma izvornog koda). URL adresa je u formatu:
https://{repository_base_url}/archive/refs/tags/{version_number_or_tag}.tar.gz

Većina projekata će označiti svoja izdanja brojem verzije, tako da je gornja URL adresa direktna veza za preuzimanje te verzije. Ovo možete potvrditi posetom <https://github.com/raysan5/raylib/tree/4.5.0> i pogledom veze za preuzimanje ZIP arhive (npr., pogledajte dole). Uporedite tu vezu za preuzimanje sa gornjom URL adresom i videćete da se potpuno podudara sa obrascem, sa izuzetkom završetka .zip umesto .tar.gz. Izabrao sam .tar.gz arhivu jer je obično manja od .zip. Obe će raditi.

**NAPOMENA**  
Neki projekti stavljaju prefiks broja verzije nečim poput „release-“. U tom slučaju, `{version_number_or_tag}` to bi bilo `release-{version_number}`.

Pređimo na sledeći red, koji je pomalo čudan:

```sh
set(BUILD_EXAMPLES OFF CACHE INTERNAL "")
```

Raylib ima `BUILD_EXAMPLES` opciju. Ne trebaju nam primeri, a njihovo pravljenje bi bilo gubljenje vremena. Zato ga onemogućavamo koristeći gornju liniju.

**VAŽNO**  
Programeri biblioteka : molimo vas da dodate prefiks vašim konfiguracionim promenljivim (npr. `RAYLIB_BUILD_EXAMPLES`) tako da budu jedinstvene i da se ne sukobljavaju sa drugim bibliotekama. Ako `BUILD_EXAMPLES` koristi više biblioteka, onda bi njegovo onemogućavanje za jednu onemogućilo za sve, što bi zahtevalo ručnu intervenciju. Jedinstvena imena promenljivih olakšavaju život svima.

Nakon što je sve gore navedeno urađeno, konačno možemo učiniti Raylib dostupnim:

```sh
FetchContent_MakeAvailable(raylib)
```

Uf! To je zahtevalo malo objašnjenja. Još nismo završili jer, iako je `Raylib` sada dostupan, nismo rekli CMake-u koje ciljeve da poveže sa njim. To je urađeno sa linijom odmah ispod `add_executable()`:

```sh
target_link_libraries(${PROJECT_NAME} raylib)
```

Ova linija kaže da cilj `${PROJECT_NAME}` (tj. "hello_raylib" kao što je podešeno u liniji 2) treba da bude povezan sa raylib-om.

Ostatak koda su zaobilazna rešenja za probleme sa Clang-om i MacOS X-om. Sećate se, rekao sam da se stvari komplikuju tamo gde se vaš kod susreće sa tuđim...

U redu, dosta teorije; hajde da kompiliramo kod. Kliknite na dugme „Pokreni“. Pod pretpostavkom da sve prođe kako treba, trebalo bi da vas dočeka prozor koji izgleda ovako:

Zdravo, Rejlib, snimak ekrana.

Ako ne dobijete prozor kao na snimku ekrana iznad, proverite poruke o greškama kompajlera i pokušajte da ih ispravite. Uporedite svoj kod sa mojim iznad.

**Da li se prozor ne otvara?**
Ako koristite nešto poput Raspberry Pi-ja, onda "hello_raylib" može da prikaže sledeću poruku o grešci umesto otvaranja prozora:

```sh
WARNING: GLFW: Error: 65543 Description: GLX: Failed to create context: GLXBadFBConfig
WARNING: GLFW: Failed to initialize Window
FATAL: Failed to initialize Graphic Device
```

Desilo se to da Raylib nije mogao da detektuje ispravnu verziju OpenGL-a koju treba koristiti. Ako dobijete ovu grešku, pokušajte da dodate sledeće odmah iznad `FetchContent_MakeAvailable()`:

```sh
set(GRAPHICS GRAPHICS_API_OPENGL_21)
```

Ovo primorava `Raylib` skriptu za izgradnju da koristi OpenGL 2.1, koji će verovatnije biti podržan na starijim sistemima.

### Uobičajene zamke i kako ih izbeći

**Izgleda da izvorni direktorijum ne sadrži "CMakeLists.txt"**  
Ako dobijete ovu grešku, ali imate "CMakeLists.txt" datoteku, proverite da li su imena datoteke napisana velikim slovima. Sistemi kao što su Linux i MacOS razlikuju velika i mala slova, tako da "Cmakelists.txt" NIJE ista datoteka kao "CMakeLists.txt". Uverite se da su C , M i L velika slova, a sve ostalo mala slova.

**Zaboravljanje podešavanja minimalne potrebne verzije CMake-a**  
CMake se ponaša različito u zavisnosti od verzije. Ako ne uključite `cmake_minimum_required (VERSION x.y)`, vaš projekat bi se mogao neočekivano ponašati na tuđem sistemu — ili čak na vašem nakon ažuriranja.

Koju verziju treba da izaberete? Trenutna verzija koju koristite je dobar početak. Ne razmišljajte previše o ovome i pokušajte da pronađete apsolutni minimum verzije koja ima funkcije koje su vam potrebne. Lako je promeniti je kasnije ako je potrebno (npr. ako treba da gradite na platformi sa starijom verzijom).

**Nesporazum `target_include_directories`**  
Mnogo ljudi pokušava da doda globalne putanje uključivanja pomoću `include_directories(...)`, ali to otvara mogućnost za sukobe (tj. različite biblioteke koriste ista imena za različite svrhe). Moderni preporučeni način je korišćenje `target_include_directories() for each target`.

**Nepravilno korišćenje ciljeva**
Početnici često pokušavaju da podese zastavice kompajlera, linkuju biblioteke ili uključuju putanje globalno. Ovo može brzo postati neuredno. Moderni pristup je da se sve poveže sa ciljevima pomoću **target_*komandi** ( `target_compile_options`, `target_link_libraries`, itd.).

**Pokušaj korišćenja promenljivih za izvorne datoteke u različitim direktorijumima**
Primamljivo je prikupljati .cpp datoteke koristeći nešto poput `file(GLOB ...)`, ali to može stvoriti probleme pri izgradnji kada se datoteke promene ili je potrebno da isključite datoteke u nekim izgradnjama. Obično je bolje eksplicitno navesti izvorne datoteke ili koristiti `target_sources() from` poddirektorijume.

**Zaboravljanje dodavanja poddirektorijuma**
Možete definisati biblioteku u poddirektorijumu "CMakeLists.txt", ali ako zaboravite da pozovete `add_subdirectory()`, ta ciljna lokacija neće postojati kada pokušate da je koristite.

**Pokušavam ručno podešavanje `CMAKE_CXX_FLAGS` umesto korišćenja `target_compile_options()`**
Modifikovanje globalnih zastavica je staromodan CMake i može da ugrozi prenosivost. Koristite target_compile_options()umesto toga. Bezbednije je i lakše za održavanje.

**Mešanje starog i modernog CMake-a**
Mnogi tutorijali i odgovori na StackOverflow-u koriste zastarele prakse (kao što su globalne promenljive, `include_directories()` ili `link_directories()`), koje su u sukobu sa modernim modelom zasnovanim na ciljevima. Mešanje ova dva može izazvati suptilne i zbunjujuće probleme pri izgradnji.

## Kada ste spremni da idete dublje

**Mogu li naučiti CMake za jedan dan?**
Osnove možete naučiti za jedan dan. Savladavanje traje mesecima do godinama, pod pretpostavkom da redovno koristite CMake. To je zato što je potrebno vreme za sticanje iskustva.

**Da li je CMake bolji od Make-a?**
Za pisanje multiplatformskog softvera, da, CMake je generalno bolji. To je zato što je dizajniran za izradu multiplatformskog (tj. krosplatformskog) softvera.

Uz to rečeno, neki preferiraju Make, jer im daje bolju kontrolu nad procesom izgradnje. CMake skripte za izgradnju rade na višem nivou apstrakcije od Makefiles-a.

**Koji je najbolji način za učenje CMake-a?**
Hehe, preko CMake tutorijala, naravno!
Zapravo, najbolji način je preko CMake tutorijala i pravljenja projekata koristeći CMake. Tutorijali, knjige i video snimci sami po sebi nisu dovoljni. Najbolje učimo kroz rad.

Takođe možete učiti pretraživanjem interneta za tutorijale i primere koda. Samo imajte na umu da postoji mnogo loših i zastarelih primera.

**Kako da strukturiram veliki projekat pomoću CMake-a?**
Ukratko: deljenjem vaše "CMakeLists.txt" skripte na više manjih datoteka. Funkcije `include()` i `add_subdirectory()` su predviđene za ovu svrhu. `Include()` omogućava podelu skripti na više datoteka, dok `add_subdirectory()` vam omogućava podelu projekata na više podprojekata.

Strukturiranje većih projekata je detaljno obrađeno u CMake tutorijalu.
