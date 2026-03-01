
# C PROGRAMMING

## D-Flat liste i evidencije - Al Stivens

Ovaj mesec nastavlja D-Flat sagu opisujući klasu prozora LISTBOKS. Okvir sa listom je prozor koji sadrži listu unosa u jednom redu i to je osnovna klasa za D-Flat iskačuće menije. Takođe ćete pronaći okvire sa listom kao kontrole u ​​okvirima za dijalog, a moguće je kreirati prozor dokumenta koji je i sam okvir sa listom. Dnevnik poruka D-Flat je pomoć za otklanjanje grešaka koju primer programa Memopad koristi, ali takođe služi i kao primer okvira za dijalog koji ima kontrolu okvira liste sa izborom više linija. Ova kolona se odnosi na klasu LISTBOKS i opisuje dnevnik poruka.

### Klasa prozora LISTBOKS

Okvir sa listom je prozor ili izveden iz klase LISTBOKS. Korisnik bira stavku iz okvira sa listom pomoću miša ili pomoću tastera sa strelicama nagore i nadole da pomeri kursor trake do željene stavke i pritisne taster Enter. Kada koristite iskačuće menije, na primer, koristite okvir sa listom.

Okvir sa listom može imati više stavki nego što će prozor prikazati, u kom slučaju korisnik može da se kreće kroz njih pomoću tastature ili traka za pomeranje. Klasa LISTBOKS je izvedena iz klase TEKSTBOKS, tako da nasleđuje većinu funkcija pregleda teksta i poruka iz okvira za tekst i dodaje nekoliko svojih.

Prošireni izbori

Okvir sa listom može imati atribut MULTILINE, što znači da korisnik može da izabere više unosa sa liste istovremeno. Ovaj proces se u CUA žargonu naziva proširena selekcija. Možete odabrati pojedinačne stavke i grupe stavki. Okvir sa listom proširenog izbora može imati mnogo grupa izabranih stavki označenih za izbor pre nego što korisnik bude spreman da obradi listu. Evo kako to funkcioniše.

Program dodaje stavke u okvir sa listom pomoću poruke ADDTEKST. Svaki od tekstualnih unosa u okviru liste proširenog izbora imaće razmak u prvom znaku. Ta pozicija je rezervisana za oznaku koja identifikuje stavku kao izabranu. Oznaka je definisana u dflat.h globalnim simbolom LISTSELECTOR i prikazuje mali dijamantski znak. Možete unapred da definišete stavku u okviru liste tako što ćete staviti znak LISTSELECTOR na prvu poziciju tekstualnog unosa stavke pre nego što je dodate u okvir sa listom sa porukom ADDTEKST.

Korisnik bira stavke u grupi iz okvira liste proširenog izbora tako što stavlja kursor trake na prvu stavku, drži pritisnut taster Shift i pomera kursor na poslednju stavku u grupi. Na ovaj način možete da se krećete gore-dole, menjajući opseg grupe u bilo kom smeru. Program će svaku selekciju u grupi označiti dijamantom kako biste mogli da vidite kako selekcija napreduje. Kada je grupa označena onako kako želite, pritisnite taster Enter. Program preuzima izabrane stavke i obrađuje ih. Ako otpustite taster Shift i pomerite kursor, izabrana grupa se poništava, dijamanti se brišu i postupak počinje iznova.

Da biste izabrali grupu mišem, kliknite na prvu stavku, držite pritisnut taster Shift i kliknite na poslednju stavku. Možete da povlačite miša nagore i nadole držeći pritisnut taster Shift, a možete koristiti traku za pomeranje da biste se pomerili do poslednje stavke pre nego što kliknete na nju.

### Dodaj režim

Procedure proširene selekcije upravo su opisale rad sa jednom po jednom grupom. Biće prilika kada korisnik treba da izabere nekoliko grupa ili raštrkanih pojedinačnih izbora sa liste. Da biste to uradili, morate ući u režim dodavanja okvira sa listom. Taster Shift+F8 uključuje režim dodavanja. Kada je okvir sa listom u režimu dodavanja, korisnik može da vrši stalne pojedinačne selekcije pomeranjem trake kursora i pritiskom na razmaknicu. Taster za razmak takođe poništava izbor izabrane stavke, tako da je možete zamisliti kao prekidač. Pomeranjem trake kursora ne brišete postojeće izabrane grupe, tako da korisnik može da izabere i više grupa tako što će preći na prvu stavku u novoj grupi, izabrati stavku pomoću razmaknice, a zatim držati pritisnut taster Shift da pređe na poslednju stavku u grupi.

Da biste izabrali više stavki i grupa pomoću miša, držite pritisnut taster Ctrl dok birate stavke i grupe. Otpustite taster Ctrl i kliknite na stavku da biste izbrisali postojeće grupe.

Korisnik treba da zna kada je okvir sa listom u režimu dodavanja, a kada nije. Kada okvir sa listom pređe u režim dodavanja, on kaže svom roditelju da prikaže tekstualni niz „Add Mode“ u statusnoj traci. Većina D-Flat aplikacija će imati statusnu traku na dnu prozora aplikacije. D-Flat ADDSTATUS poruka se kreće nagore od roditelja do roditelja sve dok je prozor ne presretne i obradi, tako da svaki prozor može poslati ADDSTATUS poruku svom roditelju, računajući da će poruka pronaći svoj put do vrha.

Možete da koristite dijalog Poruke dnevnika u meniju Opcije primera programa Memopad da biste posmatrali ponašanje okvira sa listom proširenog izbora.

### Izvorni kod LISTBOKS-a

[LISTING_ONE](#listing_one), je "listbox.c", izvorni fajl koji implementira okvir sa listom unutar D-Flat-a. Funkcija ListBokProc je modul za obradu prozora za okvire sa listom. Za poruku CREATE_VINDOV, postavlja promenljive za izbor i AnchorPoint na -1, što je njihova početna i nulta vrednost. Funkcija ListBokProc obrađuje poruke u naredbi svitch osim kada je poruci potrebno više od nekoliko redova koda. Zatim, obrada poruke za tu poruku ima svoju funkciju imenovanu za poruku. Na primer, poruku KEIBOARD obrađuje funkcija KeiboardMsg, koju poziva KEIBOARD slučaj naredbe svitch. Sama funkcija KeiboardMsg je podeljena na pozive nižim funkcijama za obradu svake vrednosti pritiska na taster.

### Pritisci na tastere u okviru liste

U zavisnosti od toga koji taster korisnik pritisne, funkcija KeiboardMsg poziva druge funkcije za obradu tastera. Taster Shift+F8 uključuje režim dodavanja okvira sa listom proširenog izbora. Kada je okvir sa listom u ovom režimu, korisnik može da sačuva postojeće selekcije dok pomera kursor. Tasteri Gore, Dole, PgUp, PgDn, Početna i Kraj pomeraju kursor okvira sa listom. Svaki od ovih tastera poziva funkciju TestEktended. Ako okvir sa listom nije u režimu dodavanja i postoje postojeći izbori, ova funkcija briše postojeće izbore pre nego što se kursor pomeri.

Pomeranje kursora se vrši pomoću poruka SCROLL, HORIZSCROLL, SCROLLPAGE, HORIZPAGE i SCROLLDOC. Većinom obrade ovih poruka upravlja modul za obradu prozora osnovne klase TEKSTBOKS. Međutim, tasteri gore i dole funkcionišu drugačije od svojih kolega u polju za tekst. Okvir za tekst koristi tastere za pomeranje teksta unutar prozora. Okvir sa listom koristi tastere za pomeranje kursora na traci gore i dole, pomerajući tekst samo kada je kursor na gornjem ili donjem redu prozora.

Tasteri koji pomeraju kursor trake takođe postavljaju poruku LB_SELECTION u prozor. Ta poruka govori prozoru da se kursor izbora promenio.

Taster za razmak prebacuje izbore u režimu dodavanja. Ako sidrišna tačka proširenog izbora nije postavljena, taster za razmak postavlja je na trenutnu liniju. Ovo je tačka od koje počinju grupe proširenog izbora. Zatim, ako je razmaknica uključila izbor, funkcija Ektend-Selections proširuje izabranu grupu sa linije sidrišta na trenutnu liniju.

Taster Enter šalje poruke LB_SELECTION i LB_CHOOSE u prozor. Dok LB_SELECTION govori prozoru da je njegov kursor za izbor sada na drugoj liniji, poruka LB_CHOOSE govori prozoru da je korisnik izabrao izabranu liniju iz okvira sa listom koju će aplikacija obraditi.

Ostala pritiskanja tastera se testiraju da bi se videlo da li su prvi znak jednog od unosa u okviru liste koji se javljaju nakon trenutnog izbora. Ako jeste, korisnik bira sledeći unos sa odgovarajućim karakterom. Na taj način, ako imate četiri imena Džons, Smit, Braun i Grin u polju sa listom, korisnik može brzo da pređe na Braun unos pritiskom na taster B.

### Poruke od miša

Kada korisnik pritisne levi taster miša u okviru sa listom, poruka LEFT_BUTTON poziva funkciju LeftButtonMsg. Poruke LEFT_BUTTON nastavljaju da dolaze sve dok korisnik drži dugme miša pritisnuto, tako da funkcija ignoriše sve osim prve sve dok se i koordinata ne promeni. Kada stigne nova vrednost i-koordinate, funkcija proverava da li korisnik ima taster shift nadole. Ako je tako, u toku je prošireni izbor. Ako nije, program briše sve postojeće izbore osim ako je taster Ctrl dole, što znači da korisnik bira pojedinačne i više stavki pritiskom na jedno dugme. Funkcija se završava slanjem LB_SELECTION prozoru da mu kaže da je korisnik izabrao stavku.

Poruka DOUBLE_CLICK se javlja kada korisnik dvaput klikne levi taster miša na okvir sa listom. To znači da korisnik bira tu stavku za obradu, pa program šalje poruku LB_CHOOSE prozoru.

Poruka BUTTON_RELEASED stiže kada korisnik otpusti levi taster miša. Resetuje prethodnu promenljivu i-koordinate miša na njenu -1 nultu vrednost.

### Dodavanje, čitanje i prikazivanje teksta okvira sa listom

Poruka ADDTEKST postavlja trenutni izbor na prvu stavku u okviru liste ako nijedna stavka trenutno nije izabrana. Ovo omogućava da okvir sa listom počne sa početno izabranom stavkom. Ako je prvi znak teksta znak LISTSELECTOR, program povećava promenljivu SelectCount, koja broji broj izabranih stavki u polju sa listom.

Poruka LB_GETTEKST preuzima red teksta u odnosu na broj reda naveden u drugom parametru i kopira red na adresu navedenu u prvom parametru. Kopira do i ne uključuje karakter novog reda, a null završava prijemno polje.

Poruka CLEARTEKST resetuje tačku sidrenja, trenutni izbor i brojač proširenog izbora.

Poruke PAINT, stranica i pomeranje pozivaju svoje parnjake u modulu za obradu prozora osnovne klase prozora, a zatim pozivaju funkciju VriteSelection da prikaže trenutno izabrani unos liste sa uključenim bojama kursora na traci birača.

### Biranje i biranje

U jeziku CUA, „odabir“ je pomeranje kursora u okviru liste na stavku ili označavanje jedne ili više stavki u proširenom izboru, dok „izbor“ govori aplikaciji da obradi trenutni izbor. Ovo je važna razlika. Na primer, iskačući meni je derivat okvira sa listom. Kada pomerite kursor iskačućeg menija, menjate njegov izbor. Kada pritisnete taster Enter da izvršite komandu menija, birate stavku u okviru liste. Program ponekad mora da zna za oba događaja, tako da postoje poruke LB_CHOOSE i LB_SELECTION. Obe poruke se šalju i u roditeljski prozor okvira sa listom. Ovo omogućava prozoru aplikacije, dijaloškom okviru ili traci menija da urade nešto značajno kada korisnik preduzme neku radnju u okviru sa listom. Sam prozor okvira sa listom koristi poruku LB_SELECTION da promeni trenutni izbor u onaj naveden u prvom parametru. Poruka LB_CURRENTSELECTION vraća trenutni broj linije za izbor pošiljaocu poruke. LB_SETSELECTION omogućava pošiljaocu da pozicionira izbor na određeni broj linije.

### Dnevnik poruka

Program za primer Memopad koristi tehniku otklanjanja grešaka u D-Flat-u da evidentira D-Flat poruke kada se pojave. Ova funkcija omogućava programeru da vidi poruke koje su prošle kroz sistem tokom testa. U sistemu vođenom događajima uvek leti mnogo poruka i možda nećete želeti da ih gledate sve, tako da dnevnik poruka koristi okvir sa listom proširenog izbora iz kojeg možete da izaberete poruke koje želite da evidentirate.

[LISTING_TWO](#listing_two), strana 133, je log.c, izvorni fajl koji implementira dnevnik poruka. Aplikacija poziva funkciju MessageLog da bi vam omogućila da uključite i isključite prijavljivanje i izaberete poruke koje želite da evidentirate. Aplikacija Memopad ima komandu Log Messages u svom meniju sa opcijama koja poziva funkciju MessageLog. Funkcija izvršava modalni dijalog dnevnik, koji je definisan u dialogs.c, izvornoj datoteci o kojoj smo raspravljali u koloni iz septembra 1991. godine. Okvir za dijalog ima prošireni okvir sa listom za izbor za prikaz poruka, polje za potvrdu za uključivanje i isključivanje prijavljivanja i uobičajena dugmad OK, Cancel i Help. Niz poruka je prikaz poruka. Pozicija prvog znaka svakog unosa je isprva prazna. Kada se ta pozicija promeni tako da sadrži karakter LISTSELECTOR, odgovarajuća poruka se bira da se evidentira. Kada je polje za potvrdu ID_LOGGING uključeno, evidentiranje je u toku. Kod za slanje poruka u message.c poziva funkciju LogMessages svaki put kada se poruka pošalje. Kada je evidentiranje uključeno i najnovija poruka je izabrana na listi, program upisuje pojedinosti o poruci u datoteku evidencije, DFLAT.LOG, koju možete pogledati u bilo kom uređivaču teksta, uključujući jedan od prozora dokumenata programa Memopad.

#### LISTING_ONE

```c
/* ------------- listbox.c ------------ */

#include "dflat.h"

#ifdef INCLUDE_EXTENDEDSELECTIONS
static int ExtendSelections(WINDOW, int, int);
static void TestExtended(WINDOW, PARAM);
static void ClearAllSelections(WINDOW);
static void SetSelection(WINDOW, int);
static void FlipSelection(WINDOW, int);
static void ClearSelection(WINDOW, int);
#else
#define TestExtended(w,p) /**/
#endif
static void near ChangeSelection(WINDOW, int, int);
static void near WriteSelection(WINDOW, int, int, RECT *);
static int near SelectionInWindow(WINDOW, int);

static int py = -1;    /* the previous y mouse coordinate */

#ifdef INCLUDE_EXTENDEDSELECTIONS
/* --------- SHIFT_F8 Key ------------ */
static void AddModeKey(WINDOW wnd)
{
    if (isMultiLine(wnd))    {
        wnd->AddMode ^= TRUE;
        SendMessage(GetParent(wnd), ADDSTATUS,
            wnd->AddMode ? ((PARAM) "Add Mode") : 0, 0);
    }
}
#endif

/* --------- UP (Up Arrow) Key ------------ */
static void UpKey(WINDOW wnd, PARAM p2)
{
    if (wnd->selection > 0)    {
        if (wnd->selection == wnd->wtop)    {
            BaseWndProc(LISTBOX, wnd, KEYBOARD, UP, p2);
            PostMessage(wnd, LB_SELECTION, wnd->selection-1,
                isMultiLine(wnd) ? p2 : FALSE);
        }
        else    {
            int newsel = wnd->selection-1;
            if (wnd->wlines == ClientHeight(wnd))
                while (*TextLine(wnd, newsel) == LINE)
                    --newsel;
            PostMessage(wnd, LB_SELECTION, newsel,
#ifdef INCLUDE_EXTENDEDSELECTIONS
                isMultiLine(wnd) ? p2 :
#endif
                FALSE);
        }
    }
}

/* --------- DN (Down Arrow) Key ------------ */
static void DnKey(WINDOW wnd, PARAM p2)
{
    if (wnd->selection < wnd->wlines-1)    {
        if (wnd->selection == wnd->wtop+ClientHeight(wnd)-1)  {
            BaseWndProc(LISTBOX, wnd, KEYBOARD, DN, p2);
            PostMessage(wnd, LB_SELECTION, wnd->selection+1,
                isMultiLine(wnd) ? p2 : FALSE);
        }
        else    {
            int newsel = wnd->selection+1;
            if (wnd->wlines == ClientHeight(wnd))
                while (*TextLine(wnd, newsel) == LINE)
                    newsel++;
            PostMessage(wnd, LB_SELECTION, newsel,
#ifdef INCLUDE_EXTENDEDSELECTIONS
                isMultiLine(wnd) ? p2 :
#endif
                FALSE);
        }
    }
}

/* --------- HOME and PGUP Keys ------------ */
static void HomePgUpKey(WINDOW wnd, PARAM p1, PARAM p2)
{
    BaseWndProc(LISTBOX, wnd, KEYBOARD, p1, p2);
    PostMessage(wnd, LB_SELECTION, wnd->wtop,
#ifdef INCLUDE_EXTENDEDSELECTIONS
        isMultiLine(wnd) ? p2 :
#endif
        FALSE);
}

/* --------- END and PGDN Keys ------------ */
static void EndPgDnKey(WINDOW wnd, PARAM p1, PARAM p2)
{
    int bot;
    BaseWndProc(LISTBOX, wnd, KEYBOARD, p1, p2);
    bot = wnd->wtop+ClientHeight(wnd)-1;
    if (bot > wnd->wlines-1)
        bot = wnd->wlines-1;
    PostMessage(wnd, LB_SELECTION, bot,
#ifdef INCLUDE_EXTENDEDSELECTIONS
        isMultiLine(wnd) ? p2 :
#endif
        FALSE);
}

#ifdef INCLUDE_EXTENDEDSELECTIONS
/* --------- Space Bar Key ------------ */
static void SpacebarKey(WINDOW wnd, PARAM p2)
{
    if (isMultiLine(wnd))    {
        int sel = SendMessage(wnd, LB_CURRENTSELECTION, 0, 0);
        if (sel != -1)    {
            if (wnd->AddMode)
                FlipSelection(wnd, sel);
            if (ItemSelected(wnd, sel))    {
                if (!((int) p2 & (LEFTSHIFT | RIGHTSHIFT)))
                    wnd->AnchorPoint = sel;
                ExtendSelections(wnd, sel, (int) p2);
            }
            else
                wnd->AnchorPoint = -1;
            SendMessage(wnd, PAINT, 0, 0);
        }
    }
}
#endif

/* --------- Enter ('\r') Key ------------ */
static void EnterKey(WINDOW wnd)
{
    if (wnd->selection != -1)    {
        SendMessage(wnd, LB_SELECTION, wnd->selection, TRUE);
        SendMessage(wnd, LB_CHOOSE, wnd->selection, 0);
    }
}

/* --------- All Other Key Presses ------------ */
static void KeyPress(WINDOW wnd, PARAM p1, PARAM p2)
{
    int sel = wnd->selection+1;
    while (sel < wnd->wlines)    {
        char *cp = TextLine(wnd, sel);
        if (cp == NULL)
            break;
#ifdef INCLUDE_EXTENDEDSELECTIONS
        if (isMultiLine(wnd))
            cp++;
#endif
        /* --- special for directory list box --- */
        if (*cp == '[')
            cp++;
        if (tolower(*cp) == (int)p1)    {
            SendMessage(wnd, LB_SELECTION, sel,
                isMultiLine(wnd) ? p2 : FALSE);
            if (!SelectionInWindow(wnd, sel))    {
                wnd->wtop = sel-ClientHeight(wnd)+1;
                SendMessage(wnd, PAINT, 0, 0);
            }
            break;
        }
        sel++;
    }
}

/* --------- KEYBOARD Message ------------ */
static int KeyboardMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    switch ((int) p1)    {
#ifdef INCLUDE_EXTENDEDSELECTIONS
        case SHIFT_F8:
            AddModeKey(wnd);
            return TRUE;
#endif
        case UP:
            TestExtended(wnd, p2);
            UpKey(wnd, p2);
            return TRUE;
        case DN:
            TestExtended(wnd, p2);
            DnKey(wnd, p2);
            return TRUE;
        case PGUP:
        case HOME:
            TestExtended(wnd, p2);
            HomePgUpKey(wnd, p1, p2);
            return TRUE;
        case PGDN:
        case END:
            TestExtended(wnd, p2);
            EndPgDnKey(wnd, p1, p2);
            return TRUE;
#ifdef INCLUDE_EXTENDEDSELECTIONS
        case ' ':
            SpacebarKey(wnd, p2);
            break;
#endif
        case '\r':
            EnterKey(wnd);
            return TRUE;
        default:
            KeyPress(wnd, p1, p2);
            break;
    }
    return FALSE;
}

/* ------- LEFT_BUTTON Message -------- */
static int LeftButtonMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int my = (int) p2 - GetTop(wnd);
    if (my >= wnd->wlines-wnd->wtop)
        my = wnd->wlines - wnd->wtop;

    if (WindowMoving || WindowSizing)
        return FALSE;
    if (!InsideRect(p1, p2, ClientRect(wnd)))
        return FALSE;
    if (wnd->wlines && my != py)    {
        int sel = wnd->wtop+my-1;
#ifdef INCLUDE_EXTENDEDSELECTIONS
        int sh = getshift();
        if (!(sh & (LEFTSHIFT | RIGHTSHIFT)))    {
            if (!(sh & CTRLKEY))
                ClearAllSelections(wnd);
            wnd->AnchorPoint = sel;
            SendMessage(wnd, PAINT, 0, 0);
        }
#endif
        SendMessage(wnd, LB_SELECTION, sel, TRUE);
        py = my;
    }
    return TRUE;
}

/* ------------- DOUBLE_CLICK Message ------------ */
static int DoubleClickMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    if (WindowMoving || WindowSizing)
        return FALSE;
    if (wnd->wlines)    {
        RECT rc = ClientRect(wnd);
        BaseWndProc(LISTBOX, wnd, DOUBLE_CLICK, p1, p2);
        if (InsideRect(p1, p2, rc))
            SendMessage(wnd, LB_CHOOSE, wnd->selection, 0);
    }
    return TRUE;
}

/* ------------ ADDTEXT Message -------------- */
static int AddTextMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int rtn = BaseWndProc(LISTBOX, wnd, ADDTEXT, p1, p2);
    if (wnd->selection == -1)
        SendMessage(wnd, LB_SETSELECTION, 0, 0);
#ifdef INCLUDE_EXTENDEDSELECTIONS
    if (*(char *)p1 == LISTSELECTOR)
        wnd->SelectCount++;
#endif
    return rtn;
}

/* --------- GETTEXT Message ------------ */
static void GetTextMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    if ((int)p2 != -1)    {
        char *cp1 = (char *)p1;
        char *cp2 = TextLine(wnd, (int)p2);
        while (cp2 && *cp2 && *cp2 != '\n')
            *cp1++ = *cp2++;
        *cp1 = '\0';
    }
}

/* --------- LISTBOX Window Processing Module ------------ */
int ListBoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:
            BaseWndProc(LISTBOX, wnd, msg, p1, p2);
            wnd->selection = -1;
#ifdef INCLUDE_EXTENDEDSELECTIONS
            wnd->AnchorPoint = -1;
#endif
            return TRUE;
        case KEYBOARD:
            if (WindowMoving || WindowSizing)
                break;
            if (KeyboardMsg(wnd, p1, p2))
                return TRUE;
            break;
        case LEFT_BUTTON:
            if (LeftButtonMsg(wnd, p1, p2) == TRUE)
                return TRUE;
            break;
        case DOUBLE_CLICK:
            if (DoubleClickMsg(wnd, p1, p2))
                return TRUE;
            break;
        case BUTTON_RELEASED:
            py = -1;
            return TRUE;
        case ADDTEXT:
            return AddTextMsg(wnd, p1, p2);
        case LB_GETTEXT:
            GetTextMsg(wnd, p1, p2);
            return TRUE;
        case CLEARTEXT:
            wnd->selection = -1;
#ifdef INCLUDE_EXTENDEDSELECTIONS
            wnd->AnchorPoint = -1;
#endif
            wnd->SelectCount = 0;
            break;
        case PAINT:
            BaseWndProc(LISTBOX, wnd, msg, p1, p2);
            WriteSelection(wnd, wnd->selection, TRUE, (RECT *)p1);
            return TRUE;
        case SCROLL:
        case HORIZSCROLL:
        case SCROLLPAGE:
        case HORIZPAGE:
        case SCROLLDOC:
            BaseWndProc(LISTBOX, wnd, msg, p1, p2);
            WriteSelection(wnd,wnd->selection,TRUE,NULL);
            return TRUE;
        case LB_CHOOSE:
            SendMessage(GetParent(wnd), LB_CHOOSE, p1, p2);
            return TRUE;
        case LB_SELECTION:
            ChangeSelection(wnd, (int) p1, (int) p2);
            SendMessage(GetParent(wnd), LB_SELECTION,
                wnd->selection, 0);
            return TRUE;
        case LB_CURRENTSELECTION:
            return wnd->selection;
        case LB_SETSELECTION:
            ChangeSelection(wnd, (int) p1, 0);
            return TRUE;
#ifdef INCLUDE_EXTENDEDSELECTIONS
        case CLOSE_WINDOW:
            if (isMultiLine(wnd) && wnd->AddMode)    {
                wnd->AddMode = FALSE;
                SendMessage(GetParent(wnd), ADDSTATUS, 0, 0);
            }
            break;
#endif
        default:
            break;
    }
    return BaseWndProc(LISTBOX, wnd, msg, p1, p2);
}

static int near SelectionInWindow(WINDOW wnd, int sel)
{
    return (wnd->wlines && sel >= wnd->wtop &&
            sel < wnd->wtop+ClientHeight(wnd));
}

static void near WriteSelection(WINDOW wnd, int sel,
                                    int reverse, RECT *rc)
{
    if (isVisible(wnd))
        if (SelectionInWindow(wnd, sel))
            WriteTextLine(wnd, rc, sel, reverse);
}

#ifdef INCLUDE_EXTENDEDSELECTIONS
/* ----- Test for extended selections in the listbox ----- */
static void TestExtended(WINDOW wnd, PARAM p2)
{
    if (isMultiLine(wnd) && !wnd->AddMode &&
            !((int) p2 & (LEFTSHIFT | RIGHTSHIFT)))    {
        if (wnd->SelectCount > 1)    {
            ClearAllSelections(wnd);
            SendMessage(wnd, PAINT, 0, 0);
        }
    }
}

/* ----- Clear selections in the listbox ----- */
static void ClearAllSelections(WINDOW wnd)
{
    if (isMultiLine(wnd) && wnd->SelectCount > 0)    {
        int sel;
        for (sel = 0; sel < wnd->wlines; sel++)
            ClearSelection(wnd, sel);
    }
}

/* ----- Invert a selection in the listbox ----- */
static void FlipSelection(WINDOW wnd, int sel)
{
    if (isMultiLine(wnd))    {
        if (ItemSelected(wnd, sel))
            ClearSelection(wnd, sel);
        else
            SetSelection(wnd, sel);
    }
}

static int ExtendSelections(WINDOW wnd, int sel, int shift)
{
    if (shift & (LEFTSHIFT | RIGHTSHIFT) &&
                        wnd->AnchorPoint != -1)    {
        int i = sel;
        int j = wnd->AnchorPoint;
        int rtn;
        if (j > i)
            swap(i,j);
        rtn = i - j;
        while (j <= i)
            SetSelection(wnd, j++);
        return rtn;
    }
    return 0;
}

static void SetSelection(WINDOW wnd, int sel)
{
    if (isMultiLine(wnd) && !ItemSelected(wnd, sel))    {
        char *lp = TextLine(wnd, sel);
        *lp = LISTSELECTOR;
        wnd->SelectCount++;
    }
}

static void ClearSelection(WINDOW wnd, int sel)
{
    if (isMultiLine(wnd) && ItemSelected(wnd, sel))    {
        char *lp = TextLine(wnd, sel);
        *lp = ' ';
        --wnd->SelectCount;
    }
}

int ItemSelected(WINDOW wnd, int sel)
{
    if (isMultiLine(wnd) && sel < wnd->wlines)    {
        char *cp = TextLine(wnd, sel);
        return (int)((*cp) & 255) == LISTSELECTOR;
    }
    return FALSE;
}
#endif

static void near ChangeSelection(WINDOW wnd,int sel,int shift)
{
    if (sel != wnd->selection)    {
#ifdef INCLUDE_EXTENDEDSELECTIONS
        if (isMultiLine(wnd))        {
            int sels;
            if (!wnd->AddMode)
                ClearAllSelections(wnd);
            sels = ExtendSelections(wnd, sel, shift);
            if (sels > 1)
                SendMessage(wnd, PAINT, 0, 0);
            if (sels == 0 && !wnd->AddMode)    {
                ClearSelection(wnd, wnd->selection);
                SetSelection(wnd, sel);
                wnd->AnchorPoint = sel;
            }
        }
#endif
        WriteSelection(wnd, wnd->selection, FALSE, NULL);
        wnd->selection = sel;
        WriteSelection(wnd, sel, TRUE, NULL);
     }
}
```

#### LISTING_TWO

```c
/* ------------ log .c ------------ */

#include "dflat.h"

#ifdef INCLUDE_LOGGING

static char *message[] = {
    #undef DFlatMsg
    #define DFlatMsg(m) " " #m,
    #include "dflatmsg.h"
    NULL
};

static FILE *log = NULL;
extern DBOX Log;

void LogMessages (WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    if (log != NULL && message[msg][0] != ' ')
        fprintf(log,
            "%-20.20s %-12.12s %-20.20s, %5.5ld, %5.5ld\n",
            wnd ? (GetTitle(wnd) ? GetTitle(wnd) : "") : "",
            wnd ? ClassNames[GetClass(wnd)] : "",
            message[msg]+1, p1, p2);
}

static int LogProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    WINDOW cwnd = ControlWindow(&Log, ID_LOGLIST);
    char **mn = message;
    switch (msg)    {
        case INITIATE_DIALOG:
            AddAttribute(cwnd, MULTILINE | VSCROLLBAR);
            while (*mn)    {
                SendMessage(cwnd, ADDTEXT, (PARAM) (*mn), 0);
                mn++;
            }
            SendMessage(cwnd, SHOW_WINDOW, 0, 0);
            break;
        case COMMAND:
            if ((int) p1 == ID_OK)    {
                int item;
                int tl = GetTextLines(cwnd);
                for (item = 0; item < tl; item++)
                    if (ItemSelected(cwnd, item))
                        mn[item][0] = LISTSELECTOR;
            }
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}

void MessageLog(WINDOW wnd)
{
    if (DialogBox(wnd, &Log, TRUE, LogProc))    {
        if (CheckBoxSetting(&Log, ID_LOGGING))    {
            log = fopen("DFLAT.LOG", "wt");
            SetCommandToggle(&MainMenu, ID_LOG);
        }
        else if (log != NULL)    {
            fclose(log);
            log = NULL;
            ClearCommandToggle(&MainMenu, ID_LOG);
        }
    }
}

#endif
```

Copyright © 1992, Dr. Dobb's Journal
