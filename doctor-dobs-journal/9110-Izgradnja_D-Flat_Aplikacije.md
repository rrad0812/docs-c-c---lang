
# C PROGRAMMING 9110

## Izgradnja D-Flat aplikacije - Al Stevens

Ovog meseca nastavljamo sa D-Flat projekat objavljivanjem poslednjeg od pratećih modula izvornog koda. Dve datoteke pružaju funkcije koje D-Flat koristi za upravljanje listama i pravougaonicima ekrana, a dve druge datoteke opisuju poruke i komande koje D-Flat i njegove aplikacije koriste. Po prvi put ćemo videti primer D-Flat aplikacijskog programa. Sledećeg meseca počinjemo da razmatramo module koji opisuju i upravljaju prozorskim klasama.

### Povezane liste u D-Flat-u

[LISTING_ONE](#listing__one), je "lists.c", izvorni fajl koji održava dve povezane liste prozora. Prva povezana lista, Izgrađena, beleži prozore D-Flat aplikacije u redosledu kojim su napravljeni. Druga povezana lista, Fokus, beleži prozore redosledom kojim zauzimaju fokus na unosu korisnika.

Povezanim listama upravlja struktura koja sadrži dva pokazivača na strukture prozora: FirstVindov pokazivač i LastVindov pokazivač. Svaka struktura prozora ima pokazivače nektfocus, prevfocus, nektbuilt i prevbuilt. Struktura LinkedList je glava liste, a struktura prozora sadrži pokazivače liste unapred i unazad. Ove strukture su definisane u dflat.h.

Postoje dve povezane liste jer neki procesi moraju da prelaze kroz prozore redosledom u kome su bili u fokusu, a drugi treba da koriste sekvencu u kojoj su prozori kreirani.

Funkcije pod nazivom SetNektFocus i SetPrevFocus postavljaju fokus na sledeći ili prethodni prozor na povezanoj listi Fokus. Parametar vnd je prozor iz kojeg funkcije počinju da traže. Ove funkcije se pozivaju kada se prozori zatvore ili kada korisnik prelazi kroz prozore pomoću tastera Alt+F6 ili tastera Tab u okviru za dijalog. Funkcija SetNektFocus pokušava da pronađe prozor koji ima isti roditelj kao onaj sa kojeg se uzima fokus. Za to koristi funkciju SearchFocus-Nekt.

Četiri funkcije dodaju i uklanjaju prozore sa dve povezane liste. To su AppendBuiltVindov, RemoveBuiltVindov, AppendFocusVindov i RemoveFocus Vindov.

Postoje četiri funkcije za kretanje kroz liste u nizu od prvog do poslednjeg. Program poziva GetFirstChild ili GetFirstFocusChild da bi dobio prvi podređeni prozor za roditeljski prozor sa jedne od dve liste. Zatim uzastopno poziva GetNektChild ili GetNektFocusChild sve dok jedna od ovih funkcija ne vrati NULL. Primer 1 pokazuje sekvencu koda za prelazak preko liste. U ovom primeru, pvnd je roditeljski prozor, a cvnd je redom svaki od podređenih prozora.

Primer 1: Kodni niz za prelazak preko liste

```c
  WINDOW cwnd = GetFirstChild (pwnd);
  while (cwnd != NULLWND) {
     /*--process the child window--*/
     cwnd = GetNextChild (pwnd, cwnd);
  }
```

Postoje dve funkcije za prelazak preko Izgrađene povezane liste obrnutim redosledom. To su GetLastChild i GetPrevChild. Oni rade isto kao i njihovi prethodnici. Ne postoje funkcije za prelazak preko Fokus liste jer za njima nema potrebe.

Funkcija SkipSistemVindovs se poziva da zaobiđe dodeljivanje fokusa prozorima APLIKACIJA, MENI TRAKA I STATUS-BAR kada korisnik koristi Alt+F6 ili neki drugi metod za kretanje između prozora dokumenta.

### Pravougaonici

Da bi pravilno upravljao ekranima, D-Flat-u su potrebne neke funkcije upravljanja pravougaonikom. [LISTING_TWO](#listing__two), je "rect.c", izvorna datoteka koja sadrži ove funkcije pravougaonika. Prva funkcija je podvektor, koju koriste samo ostale funkcije u rect.c. Funkcija prihvata dva vektora, opisana njihovim celobrojnim krajnjim tačkama kao tl, t2 i o1, o2. On izračunava preklapanje dva vektora i skladišti rezultat kao dve krajnje tačke u celim brojevima na koje ukazuju parametri celobrojnog pokazivača v1 i v2. Funkcija koristi unutar makro definisan u rect.h, koji sam objavio pre nekoliko meseci. Ta datoteka zaglavlja takođe definiše RECT tipedef, što je struktura koja sadrži leve, gornje, desne i donje koordinate pravougaonika.

Funkcija subRectangle prihvata dva RECT i izračunava pravougaonik koji je rezultat preklapanja ova dva, vraćajući izračunati pravougaonik u RECT strukturi.

Funkcija ClientRect izračunava klijentski pravougaonik određenog prozora koristeći četiri makroa, GetClientLeft, GetClientRight, GetClientTop i GetClientBottom. Ovi makroi su definisani u dflat.h. Oni izračunavaju klijentski pravougaonik iz pravougaonika prozora prilagođavajući ivicu prozora, naslovnu traku i traku menija, ako postoje.

Funkcija Relative VindovRect proizvodi pravougaonik koji je relativan u odnosu na 0 unutar pravougaonika navedenog prozora.

Funkcija ClipRectangle iseče pravougaonik koji se nalazi unutar prozora tako da se pravougaonik ne proteže izvan granica ekrana ili granica bilo kog prethodnika prozora.

### D-Flat poruke

Pre nekoliko meseci objavio sam datoteku messages.h koja je definisala D-Flat poruke kao članove enum definicije. Ta tehnika je promenjena. [LISTING_THREE](#listing__three), je "dflatmsg.h", nova datoteka zaglavlja koja sadrži poruke izražene kao parametri za makro DFlatMsg. Delovi programa kojima su potrebne liste poruka u različitim formatima definišu makro prema sopstvenim potrebama i uključuju datoteku dflatmsg.h. Na primer, nabrojana lista poruka u dflat.h sada funkcioniše kao Primer 2. Makro DFlatMsg se redefiniše da koristi vrednosti poruke kao članove nabrojane liste. Unos MESSAGE-COUNT predstavlja poslednji unos na listi i stoga je broj poruka u sistemu.

Primer 2: Nabrojana lista poruka u dflat.h funkcioniše ovako.

```c
  typedef enum messages {
    #undef DFlatMsg
    #define DFlatMsg(m) m,
    #include "dflatmsg.h"
    MESSAGECOUNT
  } MESSAGE;
```

Procesu evidentiranja poruka potrebni su nizovi imena poruke tako da može da prikaže poruke u prozoru dnevnika. Koristi dflatmsg.h, kao u Primeru 3. Makro DFlatMsg se redefiniše da koristi vrednosti poruke kao stringove sa jednim vodećim razmakom. Ova upotreba omogućava da se poruke pojavljuju u listi sa više izbora.

Primer 3: Proces evidentiranja poruka koristi nizove imena poruke da prikaže poruke u prozoru dnevnika.

```c
  static char *message[] = {
      #undef DFlatMsg
      #define DFlatMsg(m) " " #m,
      #include "dflatmsg.h"
      NULL
  };
```

### D-Flat Commands

Jedna od standardnih D-Flat poruka je COMMAND poruka. Program šalje tu poruku u prozor sa ovim formatom:

```c
SendMessage(wnd, COMMAND, cmdcode, 0);
```

Komandni kod je jedinstvena vrednost koju prozor mora prepoznati. Sistem menija koristi komandne kodove da kaže prozoru koja komanda menija je izvršena. Sistem dijalog bok-a identifikuje kontrolne prozore sa komandnim kodovima. [LISTING_FOUR](#listing__four), je "commands.h", datoteka koja specificira komande u nabrojanoj listi. Kako je objavljeno, lista sadrži sve komande koje koriste D-Flat i aplikacija memopad. Na ovu listu ćete dodati unose da biste dodali prilagođene komande za vašu aplikaciju.

### Primer programa: MEMOPAD

[LISTING_FIVE](#listing__five), je "memopad.c", primer aplikacije koja koristi D-Flat. To je program za beležnicu sa više dokumenata koji koristi sve standardne D-Flat menije i dijaloške okvire i omogućava vam da pregledate i menjate mnogo različitih tekstualnih datoteka odjednom. Njegova arhitektura je tipična za D-Flat aplikacijski program.

Glavna funkcija u memopad.c počinje pozivanjem funkcije init_messages za inicijalizaciju obrade poruke. Zatim čuva svoj argv parametar u eksternoj promenljivoj tako da drugi delovi programa – konkretno modul koji učitava bazu podataka pomoći – mogu da odrede poddirektorijum iz kojeg se program pokreće.

Program memopad sada kreira svoj prozor aplikacije pozivanjem funkcije CreateVindov. Argument APPLICATION govori D-Flat klasi prozora. Sledeći argument je naslov prozora. Nakon toga su gornje leve koordinate prozora i visina i širina. Koristeći 0,0 kao koordinate i -1, -1 kao dimenzije, program govori D-Flat-u da koristi ceo ekran za prozor. Argument MainMenu je adresa strukture MENU za traku menija prozora aplikacije. Sledeći argument je NULL. Taj argument obično specificira VINDOV ručicu roditelja prozora, ali prozor APLIKACIJE nema roditelja. Argument MemoPadProc je adresa modula za obradu prozora koji će primati poruke poslate prozoru. Prozor APLIKACIJE ima podrazumevani modul za obradu prozora, ali svaka aplikacija mora da obezbedi dodatni za rukovanje komandama koje su jedinstvene za aplikaciju. Poslednji argument funkcije CreateVindov je atributska reč prozora, koja se sastoji od vrednosti atributa ILI zajedno. Prozor aplikacije memopad mora da bude pokretljiv i veličine, i da ima ivicu i statusnu traku.

Nakon što je prozor kreiran, program mu šalje poruku u kojoj mu govori da preuzme fokus na unos korisnika. Zatim program skenira komandnu liniju da vidi da li je korisnik imenovao neke datoteke. Ako je tako, svaka specifikacija datoteke se naizmenično šalje funkciji PadVindov.

Sledeći korak je da program uđe u petlju za slanje poruka. To radi uzastopnim pozivanjem funkcije dispatch_message sve dok funkcija ne vrati lažnu vrednost, kada je program završen i može da se završi.

Funkcija PadVindov prihvata specifikaciju datoteke. Funkcija proširuje specifikaciju u imena datoteka jedan po jedan i šalje nazive datoteka funkciji OpenPadVindov.

Funkcija MemoPadProc je modul za obradu prozora za prozor aplikacije memopad. Kada sistem ili prozor pošalju poruku prozoru aplikacije, D-Flat će izvršiti ovu funkciju, prosleđujući mu poruku. To čini zato što je adresa funkcije uključena u poziv Kreiraj prozor koji je kreirao prozor.

Modul za obradu prozora može obraditi bilo koju poruku. Obično će, međutim, modul za obradu prozora za aplikaciju obraditi samo KOMANDNE poruke koje sistem menija šalje kada korisnik odabere komandu menija i kada tekući prozor dokumenta – ako postoji – ne presretne poruku. MemoPadProc obrađuje one KOMANDNE poruke jedinstvene za aplikaciju memopad i ne obrađuju ih prozori dokumenata. Komanda ID_NEV je kreiranje nove datoteke u prozoru novog dokumenta. Komanda ID_OPEN je da izaberete postojeću datoteku za učitavanje u novi prozor dokumenta. Komande ID_SAVE i ID_SAVEAS služe za čuvanje prozora dokumenta koji ima fokus nazad na disk. ID_DELETEFILE je za brisanje datoteke predstavljene prozorom koji ima fokus. ID_PRINT štampa trenutni dokument, a ID_ABOUT prikazuje poruku o aplikaciji.

Kada MemoPadProc obradi poruku, može jednostavno da vrati TRUE vrednost. Ova akcija sprečava bilo kakvu dalju obradu poruke od strane podrazumevanog modula za obradu prozora za klasu APPLICATION. Ako MemoPadProc ne želi da presretne i obradi poruku, poziva funkciju DefaultVndProc, koja prosleđuje poruku podrazumevanom modulu za obradu prozora za klasu prozora, u ovom slučaju klasu APPLICATION.

Funkcije NevFile i SelectFile pozivaju OpenPadVindov da otvore novi prozor dokumenta. NevFile prosleđuje „Untitled“ kao naslov prozora. SelectFile pokreće dijalog Otvaranje datoteke pozivanjem funkcije OpenFileDialogBok. Ako ta funkcija vrati tačnu vrednost, korisnik je izabrao specifikaciju datoteke koju je funkcija uskladištila u nizu znakova FileName. Funkcija SelectFile pretražuje prozore koji su potomci prozora aplikacije memopad da vidi da li je izabrana datoteka već učitana u prozor dokumenta. Ako je tako, SelectFile daje fokus tom prozoru i vraća se. U suprotnom, SelectFile poziva OpenPadVindov, prosleđujući mu specifikaciju datoteke izabrane datoteke.

Funkcija OpenPadVindov otvara prozor dokumenta i, ako je navedena datoteka „Untitled“, inicijalizuje prozor sa praznim dokumentom. U suprotnom, učitava tekstualnu datoteku u prozor. Prvo, funkcija OpenPadVindov koristi funkciju stat da vidi da li datoteka postoji i, ako postoji, da odredi njenu veličinu. Ako datoteka ne postoji, OpenPad Vindov poziva funkciju ErrorMessage da prikaže poruku o grešci u tom smislu. Ako datoteka postoji, ili ako se pravi novi dokument bez naslova, OpenPadVindov poziva CreateVindov da kreira prozor dokumenta. Naslov prozora potiče od komponente naziva datoteke specifikacije datoteke. Položaj prozora se izračunava tako da su uzastopni prozori kaskadno raspoređeni. Prozor aplikacije je roditelj prozora dokumenta, a EditorProc je modul za obradu prozora prozora dokumenta. Nakon kreiranja prozora dokumenta, OpenPadVindov poziva LoadFile da učita navedenu datoteku u prozor, a zatim postavlja fokus korisničkog unosa na prozor dokumenta.

Pažljivo obratite pažnju na ono što se upravo dogodilo. Program kreira prozor aplikacije i zatim ulazi u petlju dispatch_message čekajući da petlja vrati false, nakon čega se program prekida. Ta jednostavna logika je osnova programiranja zasnovanog na događajima i porukama. Sve ostalo što se dešava u programu D-Flat rezultat je poruka koje se šalju u prozor aplikacije. Osim za prikaz vremena u danu u statusnoj traci, program neće uraditi ništa dok korisnik ne preduzme akciju koja generiše poruku. Kada funkcija MemoPadProc primi jednu od KOMANDNIH poruka koje prepozna, stvari počinju da se dešavaju. Dok se jedna od tih komandi ne pojavi, korisnik može da pomera i menja veličinu prozora aplikacije, menja opcije i čita ekrane pomoći, ali se ništa značajno neće dogoditi u samoj aplikaciji dok MemoPadProc ne dobije poruku.

Funkcija LoadFile učitava određenu tekstualnu datoteku u prozor dokumenta. Prvo funkcija dodeljuje bafer koji može da sadrži tekst datoteke. Zatim funkcija čita datoteku u bafer i šalje SETTEKST poruku prozoru da mu kaže da koristi bafer za prikaz teksta.

Funkcija PrintPad štampa sadržaj prozora trenutnog dokumenta tako što ga šalje na standardni uređaj za štampanje. Ako želite da koristite ovaj memopad program za ozbiljno uređivanje teksta, želeli biste da stavite testove za štampač spreman u ovu funkciju, a zatim presretnete kritične greške u slučaju da štampaču ostane bez papira ili na neki drugi način ne uspe dok štampate. Bez ovih mera, DOS će prskati svoje grube poruke o grešci po vašim urednim D-Flat ekranima.

Funkcija SaveFile čuva tekst u prozoru trenutnog dokumenta u datoteku na disku. Ako prozor dokumenta nije naslovljen ili ako je korisnik izabrao komandu Sačuvaj kao u meniju Datoteka, funkcija SaveFile izvršava dijalog Sačuvaj kao pozivanjem funkcije SaveAsDialogBok. Ako ta funkcija vrati false, korisnik je zanemario da navede ime datoteke za dokument, a funkcija SaveFile se vraća bez preduzimanja bilo čega. U suprotnom, funkcija SaveFile dodeljuje ime datoteke prozoru dokumenta i koristi komponentu naziva datoteke novog imena datoteke kao novi naslov prozora dokumenta. Zatim, funkcija SaveFile upisuje tekst u datoteku diska. Pre otvaranja datoteke, funkcija SaveFile poziva funkciju MomentariMessage da prikaže malu poruku u prozoru na ekranu. Ovo govori korisniku da se nešto dešava i da je spreman. Nakon zatvaranja datoteke, funkcija SaveFile šalje poruku CLOSE_VINDOV u prozor trenutne poruke.

Funkcija DeleteFile briše datoteku koja se nalazi u prozoru trenutnog dokumenta i zatvara taj prozor. Prvo, poziva funkciju IesNoBok da bi omogućio korisniku da potvrdi brisanje.

Funkcija ShovPosition je primer kako aplikacija može da prikaže jednostruke reči u statusnoj traci prozora aplikacije. Pravi tekstualni prikaz koji sadrži brojeve redova i kolona gde se nalazi kursor tastature u prozoru trenutnog dokumenta. Zatim šalje ADDSTATUS poruku prozoru aplikacije sa adresom tekstualnog prikaza kao prvim parametrom u poruci.

Funkcija EditorProc je modul za obradu prozora za prozore dokumenata. Svaka poruka poslata jednom od ovih prozora prvo dolazi do ove funkcije. Posmatrajte tretman poruka SETFOCUS i KEIBOARD_CURSOR. Kada prozor treba da preuzme fokus, on prima SETFOCUS poruku sa prvim parametrom postavljenim na tačno. Kada prozor treba da odustane od fokusa, on prima SETFOCUS poruku sa prvim parametrom postavljenim na false. Funkcija EditorProc koristi ovu poruku i poruku KEIBOARD_CURSOR za upravljanje kontinuiranim prikazom linije/kolone u statusnoj traci. Kad god kursor tastature treba da se promeni, trenutni prozor prima poruku KEIBOARD_CURSOR sa koordinatom kolone kao prvim parametrom i koordinatom reda kao drugim parametrom. Ove poruke uzrokuju da funkcija EditorProc pozove funkciju ShovPosition da ažurira prikaz statusne trake na novi red i kolonu. Kada prozor dokumenta odustane od fokusa, funkcija EditorProc šalje ADDSTATUS poruku prozoru aplikacije sa nultim parametrima da kaže prozoru aplikacije da obriše prikaz teksta u statusnoj traci. Poruke kursora SETFOCUS i KEIBOARD pozivaju funkciju DefaultVndProc pre nego što izvrše svoju obradu. Ovo je procedura koju modul za obradu prozora koristi da izazove da se podrazumevana obrada poruka dogodi prva. Modul tada mora da vrati vrednost vraćenu iz funkcije DefaultVndProc kada modul završi sa sopstvenom obradom.

Funkcija EditorProc presreće funkciju CLOSE_VINDOV da vidi da li je korisnik izvršio bilo kakve promene u tekstu bez da ga sačuva. Ako je tako, funkcija poziva funkciju IesNoBok da bi omogućila korisniku da sačuva tekst. Ako korisnik odabere da sačuva tekst, funkcija EditorProc šalje COMMAND poruku sa komandom ID_SAVE u prozor aplikacije. Ovo je primer kako prozor dokumenta simulira radnju korisnika tako što šalje KOMANDU aplikaciji kao da je korisnik izabrao komandu iz menija.

Sve poruke koje EditorProc ne presretne šalju se podrazumevanom modulu za obradu prozora za klasu prozora EDITBOKS kada EditorProc pozove DefaultVndProc pre nego što se EditorProc vrati.

Odvojite još trenutak da ponovo razmislite šta se dogodilo. Pre nego što je korisnik izabrao novi prozor dokumenta bez naslova ili učitao datoteku u prozor dokumenta, program se vrtio u petlji i čekao da nešto uradi. Izbor korisnikovog menija poslao je poruku u prozor aplikacije. Prozor aplikacije je napravio prozor dokumenta i dao mu fokus. Sada skoro svaka stvar koju korisnik uradi rezultira porukom u prozoru dokumenta u fokusu. Prozor dokumenta je EDITBOKS, tako da sve poruke koje podržavaju EDITBOKS idu u prozor dokumenta. Podrazumevani modul za obradu prozora EDITBOKS-a upravlja detaljima uređivanja teksta, a modul za obradu okvira za uređivanje aplikacije obrađuje samo one poruke koje su jedinstvene za aplikaciju.

Osnovni koncepti obrade zasnovane na događajima i porukama su ono što koristi memopad aplikacijski program. Ovi isti koncepti prožimaju samu D-Flat biblioteku. Pomoć, opcije, meniji, dijaloški okviri, statusna traka i uređivanje teksta koriste iste mehanizme koje će koristiti aplikacija. Oni obuhvataju uobičajene procese koje većina aplikacija koristi u biblioteku korisničkog interfejsa. Aplikacioni softver može biti trivijalan u poređenju. Program memopad je implementiran u relativno malom modulu izvornog koda – manje od 400 linija koda – ipak je to program za uređivanje teksta sa više dokumenata, nešto što bi predstavljalo značajan napor u razvoju softvera ako biste morali da ugradite korisnički interfejs i funkcije za uređivanje teksta u samu aplikaciju.

#### [LISTING__ONE

```c
/* --------------- lists.c -------------- */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <conio.h>
#include <dos.h>
#include "dflat.h"

struct LinkedList Focus = {NULLWND, NULLWND};
struct LinkedList Built = {NULLWND, NULLWND};

/* --- set focus to the window beneath the one specified --- */
void SetPrevFocus(WINDOW wnd)
{
    if (wnd != NULLWND && wnd == inFocus)    {
        WINDOW wnd1 = wnd;
        while (TRUE)    {
            if ((wnd1 = PrevWindow(wnd1)) == NULLWND)
                wnd1 = Focus.LastWindow;
            if (wnd1 == wnd)
                return;
            if (wnd1 != NULLWND)
                break;
        }
        if (wnd1 != NULLWND)
            SendMessage(wnd1, SETFOCUS, TRUE, 0);
    }
}

/* this function assumes that wnd is in the Focus linked list */
static WINDOW SearchFocusNext(WINDOW wnd, WINDOW pwnd)
{
    WINDOW wnd1 = wnd;

    if (wnd != NULLWND)    {
        while (TRUE)    {
            if ((wnd1 = NextWindow(wnd1)) == NULLWND)
                wnd1 = Focus.FirstWindow;
            if (wnd1 == wnd)
                return NULLWND;
            if (wnd1 != NULLWND)
                if (pwnd == NULLWND || pwnd == GetParent(wnd1))
                    break;
        }
    }
    return wnd1;
}

/* ----- set focus to the next sibling ----- */
void SetNextFocus(WINDOW wnd)
{
    WINDOW wnd1;

    if (wnd != inFocus)
        return;
    if ((wnd1 = SearchFocusNext(wnd, GetParent(wnd)))==NULLWND)
        wnd1 = SearchFocusNext(wnd, NULLWND);
    if (wnd1 != NULLWND)
        SendMessage(wnd1, SETFOCUS, TRUE, 0);
}

/* ---- remove a window from the Built linked list ---- */
void RemoveBuiltWindow(WINDOW wnd)
{
    if (wnd != NULLWND)    {
        if (PrevWindowBuilt(wnd) != NULLWND)
            NextWindowBuilt(PrevWindowBuilt(wnd)) =
                NextWindowBuilt(wnd);
        if (NextWindowBuilt(wnd) != NULLWND)
            PrevWindowBuilt(NextWindowBuilt(wnd)) =
                PrevWindowBuilt(wnd);
        if (wnd == Built.FirstWindow)
            Built.FirstWindow = NextWindowBuilt(wnd);
        if (wnd == Built.LastWindow)
            Built.LastWindow = PrevWindowBuilt(wnd);
    }
}

/* ---- remove a window from the Focus linked list ---- */
void RemoveFocusWindow(WINDOW wnd)
{
    if (wnd != NULLWND)    {
        if (PrevWindow(wnd) != NULLWND)
            NextWindow(PrevWindow(wnd)) = NextWindow(wnd);
        if (NextWindow(wnd) != NULLWND)
            PrevWindow(NextWindow(wnd)) = PrevWindow(wnd);
        if (wnd == Focus.FirstWindow)
            Focus.FirstWindow = NextWindow(wnd);
        if (wnd == Focus.LastWindow)
            Focus.LastWindow = PrevWindow(wnd);
    }
}

/* ---- append a window to the Built linked list ---- */
void AppendBuiltWindow(WINDOW wnd)
{
    if (wnd != NULLWND)    {
        if (Built.FirstWindow == NULLWND)
            Built.FirstWindow = wnd;
        if (Built.LastWindow != NULLWND)
            NextWindowBuilt(Built.LastWindow) = wnd;
        PrevWindowBuilt(wnd) = Built.LastWindow;
        NextWindowBuilt(wnd) = NULLWND;
        Built.LastWindow = wnd;
    }
}

/* ---- append a window to the Focus linked list ---- */
void AppendFocusWindow(WINDOW wnd)
{
    if (wnd != NULLWND)    {
        if (Focus.FirstWindow == NULLWND)
            Focus.FirstWindow = wnd;
        if (Focus.LastWindow != NULLWND)
            NextWindow(Focus.LastWindow) = wnd;
        PrevWindow(wnd) = Focus.LastWindow;
        NextWindow(wnd) = NULLWND;
        Focus.LastWindow = wnd;
    }
}

/* -------- get the first child of a parent window ------- */
WINDOW GetFirstChild(WINDOW wnd)
{
    WINDOW ThisWindow = Built.FirstWindow;
    while (ThisWindow != NULLWND)    {
        if (GetParent(ThisWindow) == wnd)
            break;
        ThisWindow = NextWindowBuilt(ThisWindow);
    }
    return ThisWindow;
}

/* -------- get the next child of a parent window ------- */
WINDOW GetNextChild(WINDOW wnd, WINDOW ThisWindow)
{
    if (ThisWindow != NULLWND)    {
        do    {
            if ((ThisWindow = NextWindowBuilt(ThisWindow)) !=
                    NULLWND)
                if (GetParent(ThisWindow) == wnd)
                    break;
        }    while (ThisWindow != NULLWND);
    }
    return ThisWindow;
}

/* -- get first child of parent window from the Focus list -- */
WINDOW GetFirstFocusChild(WINDOW wnd)
{
    WINDOW ThisWindow = Focus.FirstWindow;
    while (ThisWindow != NULLWND)    {
        if (GetParent(ThisWindow) == wnd)
            break;
        ThisWindow = NextWindow(ThisWindow);
    }
    return ThisWindow;
}

/* -- get next child of parent window from the Focus list -- */
WINDOW GetNextFocusChild(WINDOW wnd, WINDOW ThisWindow)
{
    while (ThisWindow != NULLWND)    {
        ThisWindow = NextWindow(ThisWindow);
        if (ThisWindow != NULLWND)
            if (GetParent(ThisWindow) == wnd)
                break;
    }
    return ThisWindow;
}

/* -------- get the last child of a parent window ------- */
WINDOW GetLastChild(WINDOW wnd)
{
    WINDOW ThisWindow = Built.LastWindow;
    while (ThisWindow != NULLWND)    {
        if (GetParent(ThisWindow) == wnd)
            break;
        ThisWindow = PrevWindowBuilt(ThisWindow);
    }
    return ThisWindow;
}

/* ------- get the previous child of a parent window ------- */
WINDOW GetPrevChild(WINDOW wnd, WINDOW ThisWindow)
{
    if (ThisWindow != NULLWND)    {
        do    {
            if ((ThisWindow = PrevWindowBuilt(ThisWindow)) !=
                    NULLWND)
                if (GetParent(ThisWindow) == wnd)
                    break;
        }    while (ThisWindow != NULLWND);
    }
    return ThisWindow;
}

/* --- bypass system windows when stepping through focus --- */
void SkipSystemWindows(int Prev)
{
    int cl, ct = 0;
    while ((cl = GetClass(inFocus)) == MENUBAR ||
            cl == APPLICATION
#ifdef INCLUDE_STATUSBAR
                 || cl == STATUSBAR
#endif
                                )    {
        if (Prev)
            SetPrevFocus(inFocus);
        else
            SetNextFocus(inFocus);
        if (++ct == 3)
            break;
    }
}
```

#### [LISTING__TWO

```c
/* ------------- rect.c --------------- */

#include <dos.h>
#include "dflat.h"

 /* -- Produce vector end points produced by overlap of two other vectors -- */
static void subVector(int *v1, int *v2, int t1, int t2, int o1, int o2)
{
    *v1 = *v2 = -1;
    if (within(o1, t1, t2))    {
        *v1 = o1;
        if (within(o2, t1, t2))
            *v2 = o2;
        else
            *v2 = t2;
    }
    else if (within(o2, t1, t2))    {
        *v2 = o2;
        if (within(o1, t1, t2))
            *v1 = o1;
        else
            *v1 = t1;
    }
    else if (within(t1, o1, o2))    {
        *v1 = t1;
        if (within(t2, o1, o2))
            *v2 = t2;
        else
            *v2 = o2;
    }
    else if (within(t2, o1, o2))    {
        *v2 = t2;
        if (within(t1, o1, o2))
            *v1 = t1;
        else
            *v1 = o1;
    }
}

 /* -- Return rectangle produced by the overlap of two other rectangles --- */
RECT subRectangle(RECT r1, RECT r2)
{
    RECT r = {0,0,0,0};
    subVector((int *) &RectLeft(r), (int *) &RectRight(r),
        RectLeft(r1), RectRight(r1),
        RectLeft(r2), RectRight(r2));
    subVector((int *) &RectTop(r), (int *) &RectBottom(r),
        RectTop(r1), RectBottom(r1),
        RectTop(r2), RectBottom(r2));
    if (RectRight(r) == -1 || RectTop(r) == -1)
        RectRight(r) =
        RectLeft(r) =
        RectTop(r) =
        RectBottom(r) = 0;
    return r;
}

/* ------- return the client rectangle of a window ------ */
RECT ClientRect(void *wnd)
{
    RECT rc;

    RectLeft(rc) = GetClientLeft((WINDOW)wnd);
    RectTop(rc) = GetClientTop((WINDOW)wnd);
    RectRight(rc) = GetClientRight((WINDOW)wnd);
    RectBottom(rc) = GetClientBottom((WINDOW)wnd);
    return rc;
}

/* ---- return the rectangle relative to its window's screen position ---- */
RECT RelativeWindowRect(void *wnd, RECT rc)
{
    RectLeft(rc) -= GetLeft((WINDOW)wnd);
    RectRight(rc) -= GetLeft((WINDOW)wnd);
    RectTop(rc) -= GetTop((WINDOW)wnd);
    RectBottom(rc) -= GetTop((WINDOW)wnd);
    return rc;
}

/* ----- clip a rectangle to the parents of the window ----- */
RECT ClipRectangle(void *wnd, RECT rc)
{
    RECT sr;
    RectLeft(sr) = RectTop(sr) = 0;
    RectRight(sr) = SCREENWIDTH-1;
    RectBottom(sr) = SCREENHEIGHT-1;
    if (!TestAttribute((WINDOW)wnd, NOCLIP))
        while ((wnd = GetParent((WINDOW)wnd)) != NULLWND)
            rc = subRectangle(rc, ClientRect(wnd));
    return subRectangle(rc, sr);
}
```

#### [LISTING__THREE

```c
/* ----------- dflatmsg.h ------------ */

/* message foundation file
 * make message changes here
 * other source files will adapt
 */

/* -------------- process communication messages ----------- */
DFlatMsg(START)              /* start message processing     */
DFlatMsg(STOP)               /* stop message processing      */
DFlatMsg(COMMAND)            /* send a command to a window   */
/* -------------- window management messages --------------- */
DFlatMsg(CREATE_WINDOW)      /* create a window              */
DFlatMsg(SHOW_WINDOW)        /* show a window                */
DFlatMsg(HIDE_WINDOW)        /* hide a window                */
DFlatMsg(CLOSE_WINDOW)       /* delete a window              */
DFlatMsg(SETFOCUS)           /* set and clear the focus      */
DFlatMsg(PAINT)              /* paint the window's data space*/
DFlatMsg(BORDER)             /* paint the window's border    */
DFlatMsg(TITLE)              /* display the window's title   */
DFlatMsg(MOVE)               /* move the window              */
DFlatMsg(SIZE)               /* change the window's size     */
DFlatMsg(MAXIMIZE)           /* maximize the window          */
DFlatMsg(MINIMIZE)           /* minimize the window          */
DFlatMsg(RESTORE)            /* restore the window           */
DFlatMsg(INSIDE_WINDOW)      /* test x/y inside a window     */
/* ---------------- clock messages ------------------------- */
DFlatMsg(CLOCKTICK)          /* the clock ticked             */
DFlatMsg(CAPTURE_CLOCK)      /* capture clock into a window  */
DFlatMsg(RELEASE_CLOCK)      /* release clock to the system  */
/* -------------- keyboard and screen messages ------------- */
DFlatMsg(KEYBOARD)           /* key was pressed              */
DFlatMsg(CAPTURE_KEYBOARD) /* capture keyboard into a window */
DFlatMsg(RELEASE_KEYBOARD)   /* release keyboard to system   */
DFlatMsg(KEYBOARD_CURSOR)    /* position the keyboard cursor */
DFlatMsg(CURRENT_KEYBOARD_CURSOR) /*read the cursor position */
DFlatMsg(HIDE_CURSOR)        /* hide the keyboard cursor     */
DFlatMsg(SHOW_CURSOR)        /* display the keyboard cursor  */
DFlatMsg(SAVE_CURSOR)      /* save the cursor's configuration*/
DFlatMsg(RESTORE_CURSOR)     /* restore the saved cursor     */
DFlatMsg(SHIFT_CHANGED)      /* the shift status changed     */
DFlatMsg(WAITKEYBOARD)     /* waits for a key to be released */
/* ---------------- mouse messages ------------------------- */
DFlatMsg(MOUSE_INSTALLED)    /* test for mouse installed     */
DFlatMsg(RIGHT_BUTTON)       /* right button pressed         */
DFlatMsg(LEFT_BUTTON)        /* left button pressed          */
DFlatMsg(DOUBLE_CLICK)       /* left button double-clicked   */
DFlatMsg(MOUSE_MOVED)        /* mouse changed position       */
DFlatMsg(BUTTON_RELEASED)    /* mouse button released        */
DFlatMsg(CURRENT_MOUSE_CURSOR)/* get mouse position          */
DFlatMsg(MOUSE_CURSOR)       /* set mouse position           */
DFlatMsg(SHOW_MOUSE)         /* make mouse cursor visible    */
DFlatMsg(HIDE_MOUSE)         /* hide mouse cursor            */
DFlatMsg(WAITMOUSE)          /* wait until button released   */
DFlatMsg(TESTMOUSE)          /* test any mouse button pressed*/
DFlatMsg(CAPTURE_MOUSE)      /* capture mouse into a window  */
DFlatMsg(RELEASE_MOUSE)      /* release the mouse to system  */
/* ---------------- text box messages ---------------------- */
DFlatMsg(ADDTEXT)            /* add text to the text box     */
DFlatMsg(CLEARTEXT)          /* clear the edit box           */
DFlatMsg(SETTEXT)            /* set address of text buffer   */
DFlatMsg(SCROLL)             /* vertical scroll of text box  */
DFlatMsg(HORIZSCROLL)        /* horizontal scroll of text box*/
/* ---------------- edit box messages ---------------------- */
DFlatMsg(EB_GETTEXT)         /* get text from an edit box    */
DFlatMsg(EB_PUTTEXT)         /* put text into an edit box    */
/* ---------------- menubar messages ----------------------- */
DFlatMsg(BUILDMENU)          /* build the menu display       */
DFlatMsg(SELECTION)          /* menubar selection            */
/* ---------------- popdown messages ----------------------- */
DFlatMsg(BUILD_SELECTIONS)   /* build the menu display       */
DFlatMsg(CLOSE_POPDOWN)    /* tell parent popdown is closing */
/* ---------------- list box messages ---------------------- */
DFlatMsg(LB_SELECTION)       /* sent to parent on selection  */
DFlatMsg(LB_CHOOSE)          /* sent when user chooses       */
DFlatMsg(LB_CURRENTSELECTION)/* return the current selection */
DFlatMsg(LB_GETTEXT)         /* return the text of selection */
DFlatMsg(LB_SETSELECTION)    /* sets the listbox selection   */
/* ---------------- dialog box messages -------------------- */
DFlatMsg(INITIATE_DIALOG)    /* begin a dialog               */
DFlatMsg(ENTERFOCUS)         /* tell DB control got focus    */
DFlatMsg(LEAVEFOCUS)         /* tell DB control lost focus   */
DFlatMsg(ENDDIALOG)          /* end a dialog                 */
/* ---------------- help box messages ---------------------- */
DFlatMsg(DISPLAY_HELP)
/* --------------- application window messages ------------- */
DFlatMsg(ADDSTATUS)
```

#### [LISTING__FOUR

```c
/* ---------------- commands.h ----------------- */

/* Command values sent as the first parameter
 * in the COMMAND message
 * Add application-specific commands to this enum
 */

#ifndef COMMANDS_H
#define COMMANDS_H

enum commands {
    /* --------------- File menu ---------------- */
    ID_OPEN,
    ID_NEW,
    ID_SAVE,
    ID_SAVEAS,
    ID_DELETEFILE,
    ID_PRINT,
    ID_DOS,
    ID_EXIT,
    /* --------------- Edit menu ---------------- */
    ID_UNDO,
    ID_CUT,
    ID_COPY,
    ID_PASTE,
    ID_PARAGRAPH,
    ID_CLEAR,
    ID_DELETETEXT,
    /* --------------- Search Menu -------------- */
    ID_SEARCH,
    ID_REPLACE,
    ID_SEARCHNEXT,
    /* -------------- Options menu -------------- */
    ID_INSERT,
    ID_WRAP,
    ID_LOG,
    ID_TABS,
    ID_DISPLAY,
    ID_SAVEOPTIONS,
    /* --------------- Window menu -------------- */
    ID_WINDOW,
    ID_CLOSEALL,
    /* --------------- Help menu ---------------- */
    ID_HELPHELP,
    ID_EXTHELP,
    ID_KEYSHELP,
    ID_HELPINDEX,
    ID_ABOUT,
    ID_LOADHELP,
    /* --------------- System menu -------------- */
    ID_SYSRESTORE,
    ID_SYSMOVE,
    ID_SYSSIZE,
    ID_SYSMINIMIZE,
    ID_SYSMAXIMIZE,
    ID_SYSCLOSE,
    /* ---- FileOpen and SaveAs dialog boxes ---- */
    ID_FILENAME,
    ID_FILES,
    ID_DRIVE,
    ID_PATH,
    /* ----- Search and Replace dialog boxes ---- */
    ID_SEARCHFOR,
    ID_REPLACEWITH,
    ID_MATCHCASE,
    ID_REPLACEALL,
    /* ----------- Windows dialog box ----------- */
    ID_WINDOWLIST,
    /* --------- generic command buttons -------- */
    ID_OK,
    ID_CANCEL,
    ID_HELP,
    /* -------------- TabStops menu ------------- */
    ID_TAB2,
    ID_TAB4,
    ID_TAB6,
    ID_TAB8,
    /* ------------ Display dialog box ---------- */
    ID_BORDER,
    ID_TITLE,
    ID_STATUSBAR,
    ID_TEXTURE,
    ID_COLOR,
    ID_MONO,
    ID_REVERSE,
    ID_25LINES,
    ID_43LINES,
    ID_50LINES,
    /* ------------- Log dialog box ------------- */
    ID_LOGLIST,
    ID_LOGGING,
    /* ------------ HelpBox dialog box ---------- */
    ID_HELPTEXT,
    ID_BACK,
    ID_PREV,
    ID_NEXT
};

#endif
```

#### [LISTING__FIVE

```c
/* --------------- memopad.c ----------- */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys\types.h>
#include <sys\stat.h>
#include <dos.h>
#ifndef MSC
#include <dir.h>
#endif
#include "dflat.h"

static char Untitled[] = "Untitled";
static int wndpos = 0;

static int MemoPadProc(WINDOW, MESSAGE, PARAM, PARAM);
static void NewFile(WINDOW);
static void SelectFile(WINDOW);
static void PadWindow(WINDOW, char *);
static void OpenPadWindow(WINDOW, char *);
static void LoadFile(WINDOW, int);
static void PrintPad(WINDOW);
static void SaveFile(WINDOW, int);
static void DeleteFile(WINDOW);
static int EditorProc(WINDOW, MESSAGE, PARAM, PARAM);
static char *NameComponent(char *);
char **Argv;

void main(int argc, char *argv[])
{
    WINDOW wnd;
    init_messages();
    Argv = argv;
    wnd = CreateWindow(APPLICATION,
                        "D-Flat MemoPad " VERSION,
                        0, 0, -1, -1,
                        &MainMenu,
                        NULL,
                        MemoPadProc,
                        MOVEABLE  |
                        SIZEABLE  |
                        HASBORDER |
                        HASSTATUSBAR
                        );

    SendMessage(wnd, SETFOCUS, TRUE, 0);
    while (argc > 1)    {
        PadWindow(wnd, argv[1]);
        --argc;
        argv++;
    }
    while (dispatch_message())
        ;
}
/* ------ open text files and put them into editboxes ----- */
static void PadWindow(WINDOW wnd, char *FileName)
{
    int ax, criterr = 1;
    struct ffblk ff;
    char path[64];
    char *cp;

    CreatePath(path, FileName, FALSE, FALSE);
    cp = path+strlen(path);
    CreatePath(path, FileName, TRUE, FALSE);
    while (criterr == 1)    {
        ax = findfirst(path, &ff, 0);
        criterr = TestCriticalError();
    }
    while (ax == 0 && !criterr)    {
        strcpy(cp, ff.ff_name);
        OpenPadWindow(wnd, path);
        ax = findnext(&ff);
    }
}
/* ----- window processing module for the memopad application window ----- */
static int MemoPadProc(WINDOW wnd,MESSAGE msg,PARAM p1,PARAM p2)
{
    switch (msg)    {
        case COMMAND:
            switch ((int)p1)    {
                case ID_NEW:
                    NewFile(wnd);
                    return TRUE;
                case ID_OPEN:
                    SelectFile(wnd);
                    return TRUE;
                case ID_SAVE:
                    SaveFile(inFocus, FALSE);
                    return TRUE;
                case ID_SAVEAS:
                    SaveFile(inFocus, TRUE);
                    return TRUE;
                case ID_DELETEFILE:
                    DeleteFile(inFocus);
                    return TRUE;
                case ID_PRINT:
                    PrintPad(inFocus);
                    return TRUE;
                case ID_ABOUT:
                    MessageBox(
                         "About D-Flat and the MemoPad",
                        "   ZDDDDDDDDDDDDDDDDDDDDDDD?\n"
                        "   3    \\\   \\\     \    3\n"
                        "   3    [  [  [  [    [    3\n"
                        "   3    [  [  [  [    [    3\n"
                        "   3    [  [  [  [ [  [    3\n"
                        "   3    ___   ___   __     3\n"
                        "   @DDDDDDDDDDDDDDDDDDDDDDDY\n"
                        "D-Flat implements the SAA/CUA\n"
                        "interface in a public domain\n"
                        "C language library originally\n"
                        "published in Dr. Dobb's Journal\n"
                        "    ------------------------ \n"
                        "MemoPad is a multiple document\n"
                        "editor that demonstrates D-Flat");
                    MessageBox(
                        "D-Flat Testers and Friends",
            "Jeff Ratcliff, David Peoples, Kevin Slater,\n"
            "Naor Toledo Pinto, Jeff Hahn, Jim Drash,\n"
            "Art Stricek, John Ebert, George Dinwiddie,\n"
            "Damaian Thorne, Wes Peterson, Thomas Ewald,\n"
            "Mitch Miller, Ray Waters, Jim Drash, Eric\n"
            "Silver, Russ Nelson, Elliott Jackson, Warren\n"
            "Master, H.J. Davey, Jim Kyle, Jim Morris,\n"
            "Andrew Terry, Michel Berube, Bruce Edmondson,\n"
            "Peter Baenziger, Phil Roberge, Willie Hutton,\n"
            "Randy Bradley, Tim Gentry, Lee Humphrey,\n"
            "Larry Troxler, Robert DiFalco, Carl Huff,\n"
            "Vince Rice, Michael Kaufman, Donald Cumming,\n"
            "Ross Wheeler, Lou DiNardo, Keith London, Frank\n"
            "Burleigh, Jason Ward, Skip Key, Sam Gentile,\n"
            "Dana P'Simer, Ken North");
                    return TRUE;
                default:
                    break;
            }
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}
/* --- The New command. Open an empty editor window --- */
static void NewFile(WINDOW wnd)
{
    OpenPadWindow(wnd, Untitled);
}
/* --- The Open... command. Select a file  --- */
static void SelectFile(WINDOW wnd)
{
    char FileName[64];
    if (OpenFileDialogBox("*.PAD", FileName))    {
        /* --- see if the document is already in a window --- */
        WINDOW wnd1 = GetFirstChild(wnd);
        while (wnd1 != NULLWND)    {
            if (stricmp(FileName, wnd1->extension) == 0)    {
                SendMessage(wnd1, SETFOCUS, TRUE, 0);
                SendMessage(wnd1, RESTORE, 0, 0);
                return;
            }
            wnd1 = GetNextChild(wnd, wnd1);
        }
        OpenPadWindow(wnd, FileName);
    }
}
/* --- open a document window and load a file --- */
static void OpenPadWindow(WINDOW wnd, char *FileName)
{
    static WINDOW wnd1 = NULLWND;
    struct stat sb;
    char *Fname = FileName;
    char *ermsg;
    if (strcmp(FileName, Untitled))    {
        if (stat(FileName, &sb))    {
            if ((ermsg = malloc(strlen(FileName)+20)) != NULL) {
                strcpy(ermsg, "No such file as\n");
                strcat(ermsg, FileName);
                ErrorMessage(ermsg);
                free(ermsg);
            }
            return;
        }
        Fname = NameComponent(FileName);
    }
    wndpos += 2;
    if (wndpos == 20)
        wndpos = 2;
    wnd1 = CreateWindow(EDITBOX,
                Fname,
                (wndpos-1)*2, wndpos, 10, 40,
                NULL, wnd, EditorProc,
                SHADOW     |
                MINMAXBOX  |
                CONTROLBOX |
                VSCROLLBAR |
                HSCROLLBAR |
                MOVEABLE   |
                HASBORDER  |
                SIZEABLE   |
                MULTILINE
    );
    if (strcmp(FileName, Untitled))    {
        if ((wnd1->extension =
                malloc(strlen(FileName)+1)) != NULL)    {
            strcpy(wnd1->extension, FileName);
            LoadFile(wnd1, (int) sb.st_size);
        }
    }
    SendMessage(wnd1, SETFOCUS, TRUE, 0);
}
/* --- Load the notepad file into the editor text buffer --- */
static void LoadFile(WINDOW wnd, int tLen)
{
    char *Buf;
    FILE *fp;

    if ((Buf = malloc(tLen+1)) != NULL)    {
        if ((fp = fopen(wnd->extension, "rt")) != NULL)    {
            memset (Buf, 0, tLen+1);
            fread(Buf, tLen, 1, fp);
            SendMessage(wnd, SETTEXT, (PARAM) Buf, 0);
            fclose(fp);
        }
        free(Buf);
    }
}
/* --- print the current notepad --- */
static void PrintPad(WINDOW wnd)
{
    unsigned char *text;

    /* ---------- print the file name ---------- */
    fputs("\r\n", stdprn);
    fputs(GetTitle(wnd), stdprn);
    fputs(":\r\n\n", stdprn);

    /* ---- get the address of the editor text ----- */
    text = GetText(wnd);

    /* ------- print the notepad text --------- */
    while (*text)    {
        if (*text == '\n')
            fputc('\r', stdprn);
        fputc(*text++, stdprn);
    }

    /* ------- follow with a form feed? --------- */
    if (YesNoBox("Form Feed?"))
        fputc('\f', stdprn);
}
/* ---------- save a file to disk ------------ */
static void SaveFile(WINDOW wnd, int Saveas)
{
    FILE *fp;
    if (wnd->extension == NULL || Saveas)    {
        char FileName[64];
        if (SaveAsDialogBox(FileName))    {
            if (wnd->extension != NULL)
                free(wnd->extension);
            if ((wnd->extension =
                    malloc(strlen(FileName)+1)) != NULL)    {
                strcpy(wnd->extension, FileName);
                AddTitle(wnd, NameComponent(FileName));
                SendMessage(wnd, BORDER, 0, 0);
            }
        }
        else
            return;
    }
    if (wnd->extension != NULL)    {
        WINDOW mwnd = MomentaryMessage("Saving the file");
        if ((fp = fopen(wnd->extension, "wt")) != NULL)    {
            fwrite(GetText(wnd), strlen(GetText(wnd)), 1, fp);
            fclose(fp);
            wnd->TextChanged = FALSE;
        }
        SendMessage(mwnd, CLOSE_WINDOW, 0, 0);
    }
}
/* -------- delete a file ------------ */
static void DeleteFile(WINDOW wnd)
{
    if (wnd->extension != NULL)    {
        if (strcmp(wnd->extension, Untitled))    {
            char *fn = NameComponent(wnd->extension);
            if (fn != NULL)    {
                char msg[30];
                sprintf(msg, "Delete %s?", fn);
                if (YesNoBox(msg))    {
                    unlink(wnd->extension);
                    SendMessage(wnd, CLOSE_WINDOW, 0, 0);
                }
            }
        }
    }
}
/* ------ display the row and column in the statusbar ------ */
static void ShowPosition(WINDOW wnd)
{
    char status[30];
    sprintf(status, "Line:%4d  Column: %2d",
        wnd->CurrLine, wnd->CurrCol);
    SendMessage(GetParent(wnd), ADDSTATUS, (PARAM) status, 0);
}
/* ----- window processing module for the editboxes ----- */
static int EditorProc(WINDOW wnd,MESSAGE msg,PARAM p1,PARAM p2)
{
    int rtn;
    switch (msg)    {
        case SETFOCUS:
            rtn = DefaultWndProc(wnd, msg, p1, p2);
            if ((int)p1 == FALSE)
                SendMessage(GetParent(wnd), ADDSTATUS, 0, 0);
            else
                ShowPosition(wnd);
            return rtn;
        case KEYBOARD_CURSOR:
            rtn = DefaultWndProc(wnd, msg, p1, p2);
            ShowPosition(wnd);
            return rtn;
        case COMMAND:
            if ((int) p1 == ID_HELP)    {
                DisplayHelp(wnd, "MEMOPADDOC");
                return TRUE;
            }
            break;
        case CLOSE_WINDOW:
            if (wnd->TextChanged)    {
                char *cp = malloc(25+strlen(GetTitle(wnd)));
                SendMessage(wnd, SETFOCUS, TRUE, 0);
                if (cp != NULL)    {
                    strcpy(cp, GetTitle(wnd));
                    strcat(cp, "\nText changed. Save it?");
                    if (YesNoBox(cp))
                        SendMessage(GetParent(wnd),
                            COMMAND, ID_SAVE, 0);
                    free(cp);
                }
            }
            wndpos = 0;
            if (wnd->extension != NULL)    {
                free(wnd->extension);
                wnd->extension = NULL;
            }
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}
/* -- point to the name component of a file specification -- */
static char *NameComponent(char *FileName)
{
    char *Fname;
    if ((Fname = strrchr(FileName, '\\')) == NULL)
        if ((Fname = strrchr(FileName, ':')) == NULL)
            Fname = FileName-1;
    return Fname + 1;
}

Example 1:

```c
 WINDOW cwnd = GetFirstChild(pwnd);
 while (cwnd != NULLWND)   {
     /* -- process the child window -- */
     cwnd = GetNextChild(pwnd, cwnd);

 }
```

Example 2:

```c
 typedef enum messages {
     #undef DFlatMsg
     #define DFlatMsg(m) m,
     #include "dflatmsg.h"
     MESSAGECOUNT
 } MESSAGE;
```

Example 3

```c
 static char *message[] = {
     #undef DFlatMsg
     #define DFlatMsg(m) " " #m,
     #include "dflatmsg.h"
     NULL

 };
```

Copyright © 1991, Dr. Dobb's Journal
