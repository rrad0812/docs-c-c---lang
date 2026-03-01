
# C PROGRAMMING

## D-Flat Sistem menija - Al Stevens

Ovog meseca dodajemo menije u D-Flat. Aplikacije koriste menije za predstavljanje izbora komandi korisniku. U meniju su navedene komande od kojih korisnik može da bira. Komanda govori programu da uradi nešto, i na taj način korisnici koriste funkcije i karakteristike aplikacije.

Približavamo se kraju ove serije. Projekat D-Flat star je skoro godinu dana, a trajaće još četiri meseca. Ako ste počeli na početku, upili ste mnogo znanja. Kao nuspojava, naučili ste o programiranju zasnovanom na događajima i porukama, što je dobra lekcija jer će to biti urađeno sutrašnje programiranje - možda i umotano u objektno orijentisan omotač. O programiranju se može znati mnogo više nego što je bilo ranije. u stvari...

### D-Flat meniji

D-Flat implementira specifikaciju Common User Access (CUA), koja identifikuje standardne menije, standardne komande menija i efekat i ponašanje svakog od njih. Specifikacija dalje pruža prilagođene menije i komande koje su jedinstvene za aplikaciju. CUA sistem menija počinje sa trakom menija koja se proteže preko vrha prozora aplikacije, direktno ispod naslovne trake i iznad oblasti podataka. Traka menija je visok jedan karakter i ima oznake koje identifikuju meni aplikacije. Kada je pronalazak označio meni sa trake menija, iskačući meni će iskočiti ispod oznake. Zatim birajte komandu iz padajućeg menija.

U septembru prošle godine, ova kolona je pokazala kako programer definiše traku menija i njene iskačuće menije kodiranjem datoteke pod nazivom menu.c. Ova datoteka sadrži pozive makroa koje preprocesor C kompajlera prevodi u deklaracije strukture i inicijalizacije koristeći makroe definisane u menu.h, o čemu sam takođe govorio u septembru. Ovog meseca vam pokazujem kod iz D-Flat biblioteke koji implementira menije.

### Traka menija

[LISTING_ONE](#listing_one), strana 150, je menubar.c, kod koji implementira traku menija. Kada kreirate prozor aplikacije, navedete naziv menija koji ste opisali u menu.c. Kod za klasu prozora APPLICATION kreira prozor klase MENUBAR. Prozor MENUBAR koristi imenovane strukture menija da definiše svoj sadržaj. Sledećeg meseca ćete naučiti o časovima prozora APPLICATION. Traka menija postaje deo prozora aplikacije i prikazuje se kada se prikaže prozor aplikacije.

Program u Listingu jedan sastoji se od modula za obradu prozora za klasu MENUBAR i nekih funkcija koje obrađuju svaku poruku prozora. Poruka CREATE_VINDOV dodeljuje memoriju za traku menija i postavlja tu memoriju na razmake. Poruka SETFOCUS postavlja aktivni izbor trake menija na prvi izbor ako nije izabran. Oslikava prozor i govori svom roditelju da obriše statusnu traku ako traka menija odustane od fokusa.

Prozor APPLICATION šalje poruku BUILDMENU na traku menija da joj kaže da sebi doda iskačuće menije. Parametar u poruci BUILDMENU je adresa strukture menija definisane u menu.c. Poruka počinje izgradnjom prazne trake menija. Zatim pravi tabelu izbora menija sa unosom za svaki od iskačućih menija definisanih u strukturi. Tabela beleži početnu i završnu poziciju k-koordinate oznake menija na traci menija i vrednost karaktera prečice za izbor menija. Ta vrednost je definisana u tekstu strukture sa tildom (~) ispred karaktera prečice. Lik se prikazuje u istaknutoj boji na traci menija. Svaki izbor na traci menija uzrokuje da dužina stringa za prikaz na traci menija raste kako bi se prilagodili kontrolnim nizovima koji se menjaju i resetuju boje oko tastera prečice. Funkcija CopiCommand kopira ime izbora menija u string za prikaz na traci menija, ubacujući kontrole boja tamo gde pronađe tildu.

Poruka PAINT poziva funkciju vputs da prikaže traku menija. Zatim, ako traka menija ima fokus ili ako se prikazuje iskačući meni, poruka oslikava oznaku trenutnog aktivnog izbora na traci menija u obrnutim bojama trake menija. Ako traka menija ima fokus i ne prikazuje se iskačući meni, poruka prikazuje tekst pomoći za izbor trake menija u statusnoj traci prozora aplikacije slanjem poruke ADDSTATUS.

Kada korisnik pritisne taster, D-Flat sistem poruka snima događaj na tastaturi, pretvara ga u poruku TASTATURA i šalje poruku prozoru koji ima fokus. Ako prozor obradi pritisak na taster, vraća se. Ako nije, prozor prosleđuje poruku gore u hijerarhiji klasa prozora. Svaka klasa u hijerarhiji kojoj pripada prozor dobija pukotinu u poruci TASTATURA. Zapamtite da je sve ovo u imenu prozora u fokusu. Na vrhu hijerarhije je klasa prozora NORMAL. Ako klasa NORMAL ne obrađuje pritisak na taster, ovaj određeni prozor nije zainteresovan za pritisak na taster, a modul za obradu prozora NORMAL šalje poruku KEIBOARD roditeljskom prozoru prozora koji ima fokus. Prolaz kroz hijerarhiju se ponavlja, ovaj put prati hijerarhiju klasa roditeljske klase prozora. Poruka prolazi na ovaj način kroz klase svakog roditelja, a zatim kroz generacije od roditelja do roditelja sve dok bilo koji od prozora ne obradi poruku ili prozor aplikacije ne primi poruku u modulu za obradu prozora klase APPLICATION. Ako prozor aplikacije ne obradi poruku, prozor prosleđuje poruku prozoru trake menija aplikacije. Tako traka menija na kraju dobija pritiske na tastere koje nijedan drugi prozor ne želi. Poruka čini dug put na zauzetom ekranu. Pa, cela stvar bi mogla da traje mikrosekunde.

Poruke TASTATURE koje stižu sve do trake menija mogu biti prečice za oznake trake menija ili tasteri za ubrzavanje za same iskačuće menije. Ako vrednost pritiska na taster odgovara jednom od tastera prečice na traci menija i taster Alt je dole, program šalje poruku MB_SELECTION prozoru na traci menija, govoreći prozoru da iskoči izabrani iskačući meni kao da je korisnik kliknuo mišem. Ako se vrednost pritiska na taster poklapa sa jednim od tastera za ubrzavanje u iskačućem prozoru, program radi isto što i prozor POP-DOVNMENU kada korisnik izabere odgovarajući izbor iz iskačućeg menija. Program invertuje preklopnu postavku za izbor menija TOGGLE, vraća fokus na najnoviji prozor dokumenta koji je imao fokus i šalje KOMANDNU poruku prozoru aplikacije zajedno sa komandnim kodom koji odgovara izboru menija.

Poruka KEIBOARD za traku menija obrađuje neke druge vrednosti ključa. Taster F1 je taster za pomoć, a svaka oznaka menija ima pridruženi ekran pomoći. Ako nijedan iskačući prozor nije otvoren i korisnik pritisne F1, program poziva funkciju DisplaiHelp sa aktivnom oznakom trake menija kao parametrom pomoći. Ako traka menija ima fokus, ali nijedan iskačući meni nije otvoren, taster Enter znači da korisnik želi da iskače meni sa izabranom oznakom trake menija. Program šalje odgovarajuću poruku MB_SELECTION na traku menija. Korisnik može da koristi taster F10 da prebaci fokus između trake menija i prozora dokumenta, a taster Esc da pomeri fokus sa trake menija na prozor najnovijeg dokumenta. Tasteri sa strelicom desno i levo, FVD i BS, menjaju izabranu oznaku menija na traci menija. Ako je iskačući meni otvoren, ovi tasteri šalju i poruku MB_SELECTION da bi se otvorio drugi iskačući meni.

Kada korisnik klikne levo dugme na oznaci trake menija, prozor na traci menija prevodi koordinate poruke LEFT_BUTTON u izabranu oznaku i šalje poruku MB_SELECTION na traku menija.

Poruka MB_SELECTION govori traci menija da je korisnik izabrao iskačući meni. Ako padajući meni ima definisanu funkciju pripreme, program prvo poziva tu funkciju. Ako je drugi parametar MB_SELECTION tačan, izabrani meni je kaskadni meni, onaj koji se kaskadno spušta od izbora drugog menija. Na primer, izbor kartica u meniju Opcije primera programa memopad poziva kaskadni meni podešavanja kartica. Program izračunava lokaciju ekrana kaskadnog menija kao funkciju lokacije iskačućeg menija iz kojeg kaskadno izlazi. Položaj menija koji se ne slažu se izračunava iz pozicije oznake izbora na traci menija.

Ako je iskačući meni već otvoren kada stigne poruka MB_SELECTION, a novi meni se ne kaskadno postavlja, program zatvara trenutni. Zatim, kreira novi prozor iskačućeg menija. Ako iskačući meni nema tekst za izbor, onda program ne prikazuje meni. Meni Prozor neće imati tekst ako, na primer, nema otvorenih prozora dokumenata. Međutim, ako postoje izbori, program šalje BUILD_SELECTIONS i SETFOCUS poruke u iskačući meni.

Kada korisnik izabere izbor iz menija, prozor POPDOVNMENU šalje KOMANDNU poruku na traku menija sa pridruženim identifikacionim kodom komande kao parametrom. Ako komanda ukazuje na kaskadni meni, program šalje liniji menija poruku MB_COMMAND sa kodom menija za parametar i tačnom vrednošću za drugi parametar. Ako izabrana komanda ne ukazuje na kaskadni meni, program zatvara prozor iskačućeg menija, šalje SETFOCUS poruku prozoru dokumenta koji je imao fokus pre nego što je korisnik izabrao iskačući meni i postavlja KOMANDNU poruku u prozor aplikacije.

Kada se prozor POPDOVNMENU zatvori, on šalje CLOSE_POPDOVN poruku svom roditelju, u ovom slučaju liniji menija. Ako je iskačući meni kaskadni meni, traka menija šalje svom roditelju poruku CLOSE_VINDOV. U suprotnom, traka menija čisti kuću postavljanjem aktivnog izbora na nultu vrednost, vraćanjem fokusa na najnoviji prozor dokumenta u fokusu i prefarbavanjem da bi se isključilo isticanje na nalepnici za izbor. Kada se traka menija zatvori, oslobađa memoriju dodeljenu za njegov prikaz i postavlja svoje pokazivače na nulte vrednosti.

### Iskačući meni

[LISTING_TWO](#listing_two), je "popdown.c", kod koji implementira klasu prozora POPDOWN-MENU. Prozor iskačućeg menija potiče od klase LISTBOX. Klasa POPDOWNMENU dodaje neku jedinstvenu obradu poruka da bi okvir sa listom menija dao izgled i ponašanje iskačućeg menija. Poruka CREATE_VINDOV hvata miš i tastaturu u prozor. Kada se otvori iskačući prozor, nijedan drugi prozor ne može preuzeti sve dok iskačući prozor ne olabavi stvari.

Prozor okvira sa listom koristi jedan klik da bi pozicionirao kursor miša na selekciju i dvostruki klik da bi izabrao izbor za dalju radnju. Iskačući meni ignoriše dvostruke klikove i koristi jedan klik da bi napravio izbor. Program šalje poruku LB_SELECTION da pozicionira traku kursora za izbor kada prozor iskačućeg menija dobije poruku LEFT_BUTTON. Zatim, kada prozor dobije poruku BUTTON_RELEASED, program šalje poruku LB_CHOOSE koja označava izbor korisnika.

Svaki put kada prozor iskačućeg menija dobije poruku PAINT, ponovo gradi bafer za prikaz teksta. To je zato što se karakteristike prikaza izbora menjaju u zavisnosti od toga da li su selekcije aktivne ili ne. Prikazuje se neaktivan izbor sa znakovima manjeg intenziteta. Poruka BORDER privremeno onemogućava promenljivu u fokusu tako da će D-Flat prikazati ivicu u jednoj liniji, uobičajenu ivicu za prozore koji nemaju fokus. Zatim menja znakove vertikalne ivice za one redove u meniju koji imaju trake za razdvajanje između izbora.

Poruka LB_SELECTION se sama odbacuje ako korisnik izabere traku za razdvajanje u meniju. U suprotnom, dozvoljava osnovnoj klasi LISTBOKS da obradi poruku.

Poruka LB_CHOOSE znači da je korisnik izabrao izbor menija za izvršenje. Ako je komanda trenutno neaktivna, program poziva funkciju bip da bi oglasio upozorenje. U suprotnom, program invertuje prikaz oznake komande ako je komanda preklopna, a zatim šalje KOMANDNU poruku sa identifikacionim kodom komande u nadređeni prozor iskačućeg menija (u većini slučajeva, traka menija).

Izbor iz iskačućeg menija može imati prečicu i taster za ubrzavanje. Poruka KEIBOARD testira pritisak na taster da vidi da li je jedan od ovih. Ako je tako, program šalje poruke LB_SELECTION i LB_CHOOSE u prozor iskačućeg menija.

Poruka KEIBOARD može da uradi jednu od nekoliko drugih stvari, u zavisnosti od pritiska na taster. Tasteri sa strelicama nagore i nadole funkcionišu kao drugi okviri sa listom, osim što iskačući meni pretpostavlja da ceo ekran stane u prozor i da se ne pomera. Umesto toga, kursor se premotava odozdo prema gore i odozgo prema dole. Tasteri sa strelicom desno i levo znače da korisnik želi da zatvori trenutni iskačući meni i otvori onaj desno ili levo. Pošto prozor na traci menija to rešava, iskačući prozor prosleđuje te tastere na traku menija. F1 taster za pomoć poziva funkciju Displai-Help, prosleđujući identifikaciju pomoći trenutnog izbora menija. Taster Esc zatvara iskačući prozor.

Poruka CLOSE_VINDOV oslobađa miša i tastaturu i šalje CLOSE_POPDOVN poruku roditelju prozora iskačućeg menija.

### API za meni

[LISTING_THREE](#listing_three), je "menu.c", koji sadrži funkcije koje aplikativni program može da koristi za ispitivanje i promenu određenih karakteristika D-Flat menija i njihovih komandi. Funkcija FindCmd je lokalna za izvornu datoteku. Druge funkcije ga koriste da dobiju pokazivač na strukturu PopDovn koja podržava određenu identifikaciju komande.

Funkcija isActive vraća tačno ako je komanda u meniju aktivna. Funkcija GetCommandTekt vraća adresu teksta naslova komande menija. Funkcije ActivateCommand i DeactivateCommand aktiviraju i deaktiviraju komandu u meniju.

Neke komande menija su preklopnici nego izvršioci procesa. Primeri su komande Insert i Vord vrap u meniju Opcije. Funkcije GetCommandToggle, SetCommandToggle, ClearCommandToggle i InvertCommandToggle dobijaju, postavljaju, brišu i invertuju postavku prebacivanja za određenu komandu u određenom meniju.

### Sistemski meni

CUA specifikacija definiše sistemski meni za sve prozore. To je meni koji korisnik poziva biranjem kontrolne kutije u gornjem levom uglu prozora. Uključuje komande za pomeranje, veličinu, vraćanje, minimiziranje i maksimiziranje prozora, a njegova svrha je da omogući korisniku bez miša da izvrši te operacije pomoću tastature.

[LISTING_FOUR](#listing_four), je "sysmenu.c", kod koji implementira standardne sistemske menije koji se povezuju sa većinom drugih tipova prozora. D-Flat poziva BuildSistemMenu kada korisnik pritisne tastere Alt+minus ili Alt+razmak ili klikne na kontrolni okvir prozora. Funkcija izračunava poziciju sistemskog menija sa lokacije kontrolne kutije roditeljskog prozora i kreira prozor sistemskog menija. Zatim koristi funkcije ActivateCommand i DeactivateCommand da aktivira i deaktivira komande sistemskog menija na osnovu stanja roditeljskog prozora. Ne možete da vratite prozor koji nije minimiziran ili maksimiziran, na primer. Kada su komande pripremljene, program šalje poruke BUILD_SELECTIONS i SHOV_VINDOV prozoru menija da bi se napravio, prikazao i kontrolisao.

Funkcija SistemMenuProc je modul za obradu prozora za sistemski meni. Poruka CREATE_VINDOV menja aktivnu traku menija u lažnu koja je povezana sa sistemskim menijem. Poruka LEFT_BUTTON se presreće kada pritisne kontrolno dugme roditeljskog prozora. Poruka LB_CHOOSE zatvara prozor. Ako poruka DOUBLE_CLICK pogodi kontrolni okvir roditelja, to je isto kao i zatvaranje prozora, tako da program šalje poruku DOUBLE_CLICK roditelju tako da će se sam zatvoriti, a zatim zatvara prozor sistemskog menija.

### Kako odmah dobiti D-Flat

Izvorni kod D-Flat-a je na CompuServe-u u biblioteci 0 DDJ Foruma i na M&T Online-u. Ako ne možete da koristite bilo koju uslugu na mreži, pošaljite mi formatiranu disketu od 360K ili 720K i adresiranu, pečatiranu disketu u brigu o Dr. Dobb's Journal-u, 411 Borel Ave., San Mateo, CA 94402. Poslaću vam najnoviju verziju D-Flat-a. Softver je besplatan, ali ako vam je stalo, stavite novčanicu u poštansku pošiljku za banku hrane okruga Brevard. Oni pomažu beskućnicima i gladnim porodicama ovde u mom rodnom gradu. Do sada smo prikupili preko 1000 dolara od velikodušnih korisnika D-Flat "carevare-a". Ako želite da razgovarate o D-Flat-u sa mnom, koristite CompuServe. Moj CompuServe ID je 71101,1262 i svakodnevno pratim DDJ Forum.

Sledećeg meseca ćemo razgovarati o klasi prozora D-Flat APPLICATION.

#### LISTING_ONE

```c
/* ---------------- menubar.c ------------------ */

#include "dflat.h"

static void reset_menubar(WINDOW);

static struct {
    int x1, x2;     /* position in menu bar */
    char sc;        /* shortcut key value   */
} menu[10];
static int mctr;

MBAR *ActiveMenuBar;
static MENU *ActiveMenu;

static WINDOW mwnd;
static BOOL Selecting;

static WINDOW Cascaders[MAXCASCADES];
static int casc;
static WINDOW GetDocFocus(WINDOW);

/* ----------- SETFOCUS Message ----------- */
static void SetFocusMsg(WINDOW wnd, PARAM p1)
{
    if ((int)p1 && ActiveMenuBar->ActiveSelection == -1)
        ActiveMenuBar->ActiveSelection = 0;
    SendMessage(wnd, PAINT, 0, 0);
    if (!(int)p1)
        SendMessage(GetParent(wnd), ADDSTATUS, 0, 0);
}

/* --------- BUILDMENU Message --------- */
static void BuildMenuMsg(WINDOW wnd, PARAM p1)
{
    int offset = 3;
    reset_menubar(wnd);
    mctr = 0;
    ActiveMenuBar = (MBAR *) p1;
    ActiveMenu = ActiveMenuBar->PullDown;
    while (ActiveMenu->Title != NULL &&
            ActiveMenu->Title != (void*)-1)    {
        char *cp;
        if (strlen(GetText(wnd)+offset) <
                strlen(ActiveMenu->Title)+3)
            break;
        GetText(wnd) = realloc(GetText(wnd),
            strlen(GetText(wnd))+5);
        memmove(GetText(wnd) + offset+4, GetText(wnd) + offset,
                strlen(GetText(wnd))-offset+1);
        CopyCommand(GetText(wnd)+offset,ActiveMenu->Title,FALSE,
                wnd->WindowColors [STD_COLOR] [BG]);
        menu[mctr].x1 = offset;
        offset += strlen(ActiveMenu->Title) + (3+MSPACE);
        menu[mctr].x2 = offset-MSPACE;
        cp = strchr(ActiveMenu->Title, SHORTCUTCHAR);
        if (cp)
            menu[mctr].sc = tolower(*(cp+1));
        mctr++;
        ActiveMenu++;
    }
    ActiveMenu = ActiveMenuBar->PullDown;
}

/* ---------- PAINT Message ---------- */
static void PaintMsg(WINDOW wnd)
{
    if (wnd == inFocus)
        SendMessage(GetParent(wnd), ADDSTATUS, 0, 0);
    SetStandardColor(wnd);
    wputs(wnd, GetText(wnd), 0, 0);
    if (ActiveMenuBar->ActiveSelection != -1 &&
            (wnd == inFocus || mwnd != NULL))    {
        char *sel;
        char *cp;
        if ((sel = malloc(200)) != NULL)    {
            int offset=menu[ActiveMenuBar->ActiveSelection].x1;
            int offset1=menu[ActiveMenuBar->ActiveSelection].x2;
            GetText(wnd)[offset1] = '\0';
            SetReverseColor(wnd);
            memset(sel, '\0', 200);
            strcpy(sel, GetText(wnd)+offset);
            cp = strchr(sel, CHANGECOLOR);
            if (cp != NULL)
                *(cp + 2) = background | 0x80;
            wputs(wnd, sel,
                offset-ActiveMenuBar->ActiveSelection*4, 0);
            GetText(wnd)[offset1] = ' ';
            if (!Selecting && mwnd == NULL && wnd == inFocus) {
                char *st = ActiveMenu
                    [ActiveMenuBar->ActiveSelection].StatusText;
                if (st != NULL)
                    SendMessage(GetParent(wnd), ADDSTATUS,
                        (PARAM)st, 0);
            }
            free(sel);
        }
    }
}

/* ------------ KEYBOARD Message ------------- */
static void KeyboardMsg(WINDOW wnd, PARAM p1)
{
    MENU *mnu;
    if (mwnd == NULL)    {
        /* ----- search for menu bar shortcut keys ---- */
        int c = tolower((int)p1);
        int a = AltConvert((int)p1);
        int j;
        for (j = 0; j < mctr; j++)    {
            if ((inFocus == wnd && menu[j].sc == c) ||
                    (a && menu[j].sc == a))    {
                SendMessage(wnd, MB_SELECTION, j, 0);
                return;
            }
        }
    }
    /* -------- search for accelerator keys -------- */
    mnu = ActiveMenu;
    while (mnu->Title != (void *)-1)    {
        struct PopDown *pd = mnu->Selections;
        if (mnu->PrepMenu)
            (*(mnu->PrepMenu))(GetDocFocus(wnd), mnu);
        while (pd->SelectionTitle != NULL)    {
            if (pd->Accelerator == (int) p1)    {
                if (pd->Attrib & INACTIVE)
                    beep();
                else    {
                    if (pd->Attrib & TOGGLE)
                        pd->Attrib ^= CHECKED;
                    SendMessage(GetDocFocus(wnd),
                        SETFOCUS, TRUE, 0);
                    PostMessage(GetParent(wnd),
                        COMMAND, pd->ActionId, 0);
                }
                return;
            }
            pd++;
        }
        mnu++;
    }
    switch ((int)p1)    {
        case F1:
            if (ActiveMenu != NULL &&
                (mwnd == NULL ||
                (ActiveMenu+ActiveMenuBar->ActiveSelection)->
                    Selections[0].SelectionTitle == NULL)) {
                DisplayHelp(wnd,
        (ActiveMenu+ActiveMenuBar->ActiveSelection)->Title+1);
                return;
            }
            break;
        case '\r':
            if (mwnd == NULL &&
                    ActiveMenuBar->ActiveSelection != -1)
                SendMessage(wnd, MB_SELECTION,
                    ActiveMenuBar->ActiveSelection, 0);
            break;
        case F10:
            if (wnd != inFocus && mwnd == NULL)    {
                SendMessage(wnd, SETFOCUS, TRUE, 0);
                break;
            }
            /* ------- fall through ------- */
        case ESC:
            if (inFocus == wnd && mwnd == NULL)    {
                ActiveMenuBar->ActiveSelection = -1;
                SendMessage(GetDocFocus(wnd),SETFOCUS,TRUE,0);
                SendMessage(wnd, PAINT, 0, 0);
            }
            break;
        case FWD:
            ActiveMenuBar->ActiveSelection++;
            if (ActiveMenuBar->ActiveSelection == mctr)
                ActiveMenuBar->ActiveSelection = 0;
            if (mwnd != NULL)
                SendMessage(wnd, MB_SELECTION,
                    ActiveMenuBar->ActiveSelection, 0);
            else
                SendMessage(wnd, PAINT, 0, 0);
            break;
        case BS:
            if (ActiveMenuBar->ActiveSelection == 0)
                ActiveMenuBar->ActiveSelection = mctr;
            --ActiveMenuBar->ActiveSelection;
            if (mwnd != NULL)
                SendMessage(wnd, MB_SELECTION,
                    ActiveMenuBar->ActiveSelection, 0);
            else
                SendMessage(wnd, PAINT, 0, 0);
            break;
        default:
            break;
    }
}

/* --------------- LEFT_BUTTON Message ---------- */
static void LeftButtonMsg(WINDOW wnd, PARAM p1)
{
    int i;
    int mx = (int) p1 - GetLeft(wnd);
    /* --- compute the selection that the left button hit --- */
    for (i = 0; i < mctr; i++)
        if (mx >= menu[i].x1-4*i &&
                mx <= menu[i].x2-4*i-5)
            break;
    if (i < mctr)
        if (i != ActiveMenuBar->ActiveSelection || mwnd == NULL)
            SendMessage(wnd, MB_SELECTION, i, 0);
}

/* -------------- MB_SELECTION Message -------------- */
static void SelectionMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int wd, mx, my;
    MENU *mnu;

    Selecting = TRUE;
    mnu = ActiveMenu+(int)p1;
    if (mnu->PrepMenu != NULL)
        (*(mnu->PrepMenu))(GetDocFocus(wnd), mnu);
    wd = MenuWidth(mnu->Selections);
    if (p2)    {
        mx = GetLeft(inFocus) + WindowWidth(inFocus) - 1;
        my = GetTop(inFocus) + inFocus->selection;
    }
    else    {
        int offset = menu[(int)p1].x1 - 4 * (int)p1;
        if (mwnd != NULL)    {
            SendMessage(wnd, SETFOCUS, TRUE, 0);
            SendMessage(mwnd, CLOSE_WINDOW, 0, 0);
        }
        ActiveMenuBar->ActiveSelection = (int) p1;
        if (offset > WindowWidth(wnd)-wd)
            offset = WindowWidth(wnd)-wd;
        mx = GetLeft(wnd)+offset;
        my = GetTop(wnd)+1;
    }
    mwnd = CreateWindow(POPDOWNMENU, NULL,
                mx, my,
                MenuHeight(mnu->Selections),
                wd,
                NULL,
                wnd,
                NULL,
                0);
    AddAttribute(mwnd, SHADOW);
    if (mnu->Selections[0].SelectionTitle != NULL)    {
        SendMessage(mwnd, BUILD_SELECTIONS, (PARAM) mnu, 0);
        SendMessage(mwnd, SETFOCUS, TRUE, 0);
    }
    else
        SendMessage(wnd, PAINT, 0, 0);
    Selecting = FALSE;
}

/* --------- COMMAND Message ---------- */
static void CommandMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    if (isCascadedCommand(ActiveMenuBar, (int)p1))    {
        /* find the cascaded menu based on command id in p1 */
        MENU *mnu = ActiveMenu+mctr;
        while (mnu->Title != (void *)-1)    {
            if (mnu->CascadeId == (int) p1)    {
                if (casc < MAXCASCADES)    {
                    Cascaders[casc++] = mwnd;
                    SendMessage(wnd, MB_SELECTION,
                        (PARAM)(mnu-ActiveMenu), TRUE);
                }
                break;
            }
            mnu++;
        }
    }
    else     {
        if (mwnd != NULL)
            SendMessage(mwnd, CLOSE_WINDOW, 0, 0);
        SendMessage(GetDocFocus(wnd), SETFOCUS, TRUE, 0);
        PostMessage(GetParent(wnd), COMMAND, p1, p2);
    }
}

/* --------------- CLOSE_POPDOWN Message --------------- */
static void ClosePopdownMsg(WINDOW wnd)
{
    if (casc > 0)
        SendMessage(Cascaders[--casc], CLOSE_WINDOW, 0, 0);
    else     {
        mwnd = NULL;
        ActiveMenuBar->ActiveSelection = -1;
        if (!Selecting)
            SendMessage(GetDocFocus(wnd), SETFOCUS, TRUE, 0);
        SendMessage(wnd, PAINT, 0, 0);
    }
}

/* ---------------- CLOSE_WINDOW Message --------------- */
static void CloseWindowMsg(WINDOW wnd)
{
    if (GetText(wnd) != NULL)    {
        free(GetText(wnd));
        GetText(wnd) = NULL;
    }
    mctr = 0;
    ActiveMenuBar->ActiveSelection = -1;
    ActiveMenu = NULL;
    ActiveMenuBar = NULL;
}

/* --- Window processing module for MENUBAR window class --- */
int MenuBarProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int rtn;

    switch (msg)    {
        case CREATE_WINDOW:
            reset_menubar(wnd);
            break;
        case SETFOCUS:
            rtn = BaseWndProc(MENUBAR, wnd, msg, p1, p2);
            SetFocusMsg(wnd, p1);
            return rtn;
        case BUILDMENU:
            BuildMenuMsg(wnd, p1);
            break;
        case PAINT:
            if (!isVisible(wnd) || GetText(wnd) == NULL)
                break;
            PaintMsg(wnd);
            return FALSE;
        case BORDER:
            return TRUE;
        case KEYBOARD:
            KeyboardMsg(wnd, p1);
            return TRUE;
        case LEFT_BUTTON:
            LeftButtonMsg(wnd, p1);
            return TRUE;
        case MB_SELECTION:
            SelectionMsg(wnd, p1, p2);
            break;
        case COMMAND:
            CommandMsg(wnd, p1, p2);
            return TRUE;
        case INSIDE_WINDOW:
            return InsideRect(p1, p2, WindowRect(wnd));
        case CLOSE_POPDOWN:
            ClosePopdownMsg(wnd);
            return TRUE;
        case CLOSE_WINDOW:
            rtn = BaseWndProc(MENUBAR, wnd, msg, p1, p2);
            CloseWindowMsg(wnd);
            return rtn;
        default:
            break;
    }
    return BaseWndProc(MENUBAR, wnd, msg, p1, p2);
}

/* ----- return the WINDOW handle of the document window
     that had the focus when the MENUBAR was activated ----- */
static WINDOW GetDocFocus(WINDOW wnd)
{
    WINDOW DocFocus = Focus.LastWindow;
    CLASS cl;
    while ((cl = GetClass(DocFocus)) == MENUBAR ||
                cl == POPDOWNMENU ||
                    cl == STATUSBAR ||
                        cl == APPLICATION)                    {
        if ((DocFocus = PrevWindow(DocFocus)) == NULL)    {
            DocFocus = GetParent(wnd);
            break;
        }
    }
    return DocFocus;
}

/* ------------- reset the MENUBAR -------------- */
static void reset_menubar(WINDOW wnd)
{
    if ((GetText(wnd) =
            realloc(GetText(wnd), SCREENWIDTH+5)) != NULL)    {
        memset(GetText(wnd), ' ', SCREENWIDTH);
        *(GetText(wnd)+WindowWidth(wnd)) = '\0';
    }
}
```

#### LISTING_TWO

```c
/* ------------- popdown.c ----------- */

#include "dflat.h"

static int SelectionWidth(struct PopDown *);
static int py = -1;

/* ------------ CREATE_WINDOW Message ------------- */
static int CreateWindowMsg(WINDOW wnd)
{
    int rtn;
    ClearAttribute(wnd, HASTITLEBAR     |
                        VSCROLLBAR     |
                        MOVEABLE     |
                        SIZEABLE     |
                        HSCROLLBAR);
    rtn = BaseWndProc(POPDOWNMENU, wnd, CREATE_WINDOW, 0, 0);
    SendMessage(wnd, CAPTURE_MOUSE, 0, 0);
    SendMessage(wnd, CAPTURE_KEYBOARD, 0, 0);
    SendMessage(NULL, SAVE_CURSOR, 0, 0);
    SendMessage(NULL, HIDE_CURSOR, 0, 0);
    return rtn;
}

/* --------- LEFT_BUTTON Message --------- */
static void LeftButtonMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int my = (int) p2 - GetTop(wnd);
    if (InsideRect(p1, p2, ClientRect(wnd)))    {
        if (my != py)    {
            SendMessage(wnd, LB_SELECTION,
                    (PARAM) wnd->wtop+my-1, TRUE);
            py = my;
        }
    }
    else if ((int)p2 == GetTop(GetParent(wnd)))
        if (GetClass(GetParent(wnd)) == MENUBAR)
            PostMessage(GetParent(wnd), LEFT_BUTTON, p1, p2);
}

/* -------- BUTTON_RELEASED Message -------- */
static BOOL ButtonReleasedMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    py = -1;
    if (InsideRect((int)p1, (int)p2, ClientRect(wnd)))    {
        int sel = (int)p2 - GetClientTop(wnd);
        if (*TextLine(wnd, sel) != LINE)
            SendMessage(wnd, LB_CHOOSE, wnd->selection, 0);
    }
    else    {
        WINDOW pwnd = GetParent(wnd);
        if (GetClass(pwnd) == MENUBAR && (int)p2==GetTop(pwnd))
            return FALSE;
        if ((int)p1 == GetLeft(pwnd)+2)
            return FALSE;
        SendMessage(wnd, CLOSE_WINDOW, 0, 0);
        return TRUE;
    }
    return FALSE;
}

/* --------- PAINT Message -------- */
static void PaintMsg(WINDOW wnd)
{
    int wd;
    unsigned char sep[80], *cp = sep;
    unsigned char sel[80];
    struct PopDown *ActivePopDown;
    struct PopDown *pd1;

    ActivePopDown = pd1 = wnd->mnu->Selections;
    wd = MenuWidth(ActivePopDown)-2;
    while (wd--)
        *cp++ = LINE;
    *cp = '\0';
    SendMessage(wnd, CLEARTEXT, 0, 0);
    wnd->selection = wnd->mnu->Selection;
    while (pd1->SelectionTitle != NULL)    {
        if (*pd1->SelectionTitle == LINE)
            SendMessage(wnd, ADDTEXT, (PARAM) sep, 0);
        else    {
            int len;
            memset(sel, '\0', sizeof sel);
            if (pd1->Attrib & INACTIVE)
                /* ------ inactive menu selection ----- */
                sprintf(sel, "%c%c%c",
                    CHANGECOLOR,
                    wnd->WindowColors [HILITE_COLOR] [FG]|0x80,
                    wnd->WindowColors [STD_COLOR] [BG]|0x80);
            strcat(sel, " ");
            if (pd1->Attrib & CHECKED)
                /* ---- paint the toggle checkmark ---- */
                sel[strlen(sel)-1] = CHECKMARK;
            len=CopyCommand(sel+strlen(sel),pd1->SelectionTitle,
                    pd1->Attrib & INACTIVE,
                    wnd->WindowColors [STD_COLOR] [BG]);
            if (pd1->Accelerator)    {
                /* ---- paint accelerator key ---- */
                int i;
                int wd1 = 2+SelectionWidth(ActivePopDown) -
                                    strlen(pd1->SelectionTitle);
                for (i = 0; keys[i].keylabel; i++)    {
                    if (keys[i].keycode == pd1->Accelerator)   {
                        while (wd1--)
                            strcat(sel, " ");
                        sprintf(sel+strlen(sel), "[%s]",
                            keys[i].keylabel);
                        break;
                    }
                }
            }
            if (pd1->Attrib & CASCADED)    {
                /* ---- paint cascaded menu token ---- */
                if (!pd1->Accelerator)    {
                    wd = MenuWidth(ActivePopDown)-len+1;
                    while (wd--)
                        strcat(sel, " ");
                }
                sel[strlen(sel)-1] = CASCADEPOINTER;
            }
            else
                strcat(sel, " ");
            strcat(sel, " ");
            sel[strlen(sel)-1] = RESETCOLOR;
            SendMessage(wnd, ADDTEXT, (PARAM) sel, 0);
        }
        pd1++;
    }
}

/* ---------- BORDER Message ----------- */
static int BorderMsg(WINDOW wnd)
{
    int i, rtn = TRUE;
    WINDOW currFocus;
    if (wnd->mnu != NULL)    {
        currFocus = inFocus;
        inFocus = NULL;
        rtn = BaseWndProc(POPDOWNMENU, wnd, BORDER, 0, 0);
        inFocus = currFocus;
        for (i = 0; i < ClientHeight(wnd); i++)    {
            if (*TextLine(wnd, i) == LINE)    {
                wputch(wnd, LEDGE, 0, i+1);
                wputch(wnd, REDGE, WindowWidth(wnd)-1, i+1);
            }
        }
    }
    return rtn;
}

/* -------------- LB_CHOOSE Message -------------- */
static void LBChooseMsg(WINDOW wnd, PARAM p1)
{
    struct PopDown *ActivePopDown = wnd->mnu->Selections;
    if (ActivePopDown != NULL)    {
        int *attr = &(ActivePopDown+(int)p1)->Attrib;
        wnd->mnu->Selection = (int)p1;
        if (!(*attr & INACTIVE))    {
            if (*attr & TOGGLE)
                *attr ^= CHECKED;
            PostMessage(GetParent(wnd), COMMAND,
                (ActivePopDown+(int)p1)->ActionId, p1);
        }
        else
            beep();
    }
}

/* ---------- KEYBOARD Message --------- */
static BOOL KeyboardMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    struct PopDown *ActivePopDown = wnd->mnu->Selections;
    if (wnd->mnu != NULL)    {
        if (ActivePopDown != NULL)    {
            int c = (int)p1;
            int sel = 0;
            int a;
            struct PopDown *pd = ActivePopDown;

            if ((c & OFFSET) == 0)
                c = tolower(c);
            a = AltConvert(c);

            while (pd->SelectionTitle != NULL)    {
                char *cp = strchr(pd->SelectionTitle,
                                SHORTCUTCHAR);
                int sc = tolower(*(cp+1));
                if ((cp && sc == c) ||
                        (a && sc == a) ||
                            pd->Accelerator == c)    {
                    PostMessage(wnd, LB_SELECTION, sel, 0);
                    PostMessage(wnd, LB_CHOOSE, sel, TRUE);
                    return TRUE;
                }
                pd++, sel++;
            }
        }
    }
    switch ((int)p1)    {
        case F1:
            if (ActivePopDown == NULL)
                SendMessage(GetParent(wnd), KEYBOARD, p1, p2);
            else
                DisplayHelp(wnd,
                    (ActivePopDown+wnd->selection)->help);
            return TRUE;
        case ESC:
            SendMessage(wnd, CLOSE_WINDOW, 0, 0);
            return TRUE;
        case FWD:
        case BS:
            if (GetClass(GetParent(wnd)) == MENUBAR)
                PostMessage(GetParent(wnd), KEYBOARD, p1, p2);
            return TRUE;
        case UP:
            if (wnd->selection == 0)    {
                if (wnd->wlines == ClientHeight(wnd))    {
                    PostMessage(wnd, LB_SELECTION,
                                    wnd->wlines-1, FALSE);
                    return TRUE;
                }
            }
            break;
        case DN:
            if (wnd->selection == wnd->wlines-1)    {
                if (wnd->wlines == ClientHeight(wnd))    {
                    PostMessage(wnd, LB_SELECTION, 0, FALSE);
                    return TRUE;
                }
            }
            break;
        case HOME:
        case END:
        case '\r':
            break;
        default:
            return TRUE;
    }
    return FALSE;
}

/* ----------- CLOSE_WINDOW Message ---------- */
static int CloseWindowMsg(WINDOW wnd)
{
    int rtn;
    SendMessage(wnd, RELEASE_MOUSE, 0, 0);
    SendMessage(wnd, RELEASE_KEYBOARD, 0, 0);
    SendMessage(NULL, RESTORE_CURSOR, 0, 0);
    rtn = BaseWndProc(POPDOWNMENU, wnd, CLOSE_WINDOW, 0, 0);
    SendMessage(GetParent(wnd), CLOSE_POPDOWN, 0, 0);
    return rtn;
}

/* - Window processing module for POPDOWNMENU window class - */
int PopDownProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:
            return CreateWindowMsg(wnd);
        case LEFT_BUTTON:
            LeftButtonMsg(wnd, p1, p2);
            return FALSE;
        case DOUBLE_CLICK:
            return TRUE;
        case LB_SELECTION:
            if (*TextLine(wnd, (int)p1) == LINE)
                return TRUE;
            wnd->mnu->Selection = (int)p1;
            break;
        case BUTTON_RELEASED:
            if (ButtonReleasedMsg(wnd, p1, p2))
                return TRUE;
            break;
        case BUILD_SELECTIONS:
            wnd->mnu = (void *) p1;
            wnd->selection = wnd->mnu->Selection;
            break;
        case PAINT:
            if (wnd->mnu == NULL)
                return TRUE;
            PaintMsg(wnd);
            break;
        case BORDER:
            return BorderMsg(wnd);
        case LB_CHOOSE:
            LBChooseMsg(wnd, p1);
            return TRUE;
        case KEYBOARD:
            if (KeyboardMsg(wnd, p1, p2))
                return TRUE;
            break;
        case CLOSE_WINDOW:
            return CloseWindowMsg(wnd);
        default:
            break;
    }
    return BaseWndProc(POPDOWNMENU, wnd, msg, p1, p2);
}

/* --------- compute menu height -------- */
int MenuHeight(struct PopDown *pd)
{
    int ht = 0;
    while (pd[ht].SelectionTitle != NULL)
        ht++;
    return ht+2;
}

/* --------- compute menu width -------- */
int MenuWidth(struct PopDown *pd)
{
    int wd = 0, i;
    int len = 0;

    wd = SelectionWidth(pd);
    while (pd->SelectionTitle != NULL)    {
        if (pd->Accelerator)    {
            for (i = 0; keys[i].keylabel; i++)
                if (keys[i].keycode == pd->Accelerator)    {
                    len = max(len, 2+strlen(keys[i].keylabel));
                    break;
                }
        }
        if (pd->Attrib & CASCADED)
            len = max(len, 2);
        pd++;
    }
    return wd+5+len;
}

/* ---- compute the maximum selection width in a menu ---- */
static int SelectionWidth(struct PopDown *pd)
{
    int wd = 0;
    while (pd->SelectionTitle != NULL)    {
        int len = strlen(pd->SelectionTitle)-1;
        wd = max(wd, len);
        pd++;
    }
    return wd;
}

/* ----- copy a menu command to a display buffer ---- */
int CopyCommand(unsigned char *dest, unsigned char *src,
                                        int skipcolor, int bg)
{
    unsigned char *d = dest;
    while (*src && *src != '\n')    {
        if (*src == SHORTCUTCHAR)    {
            src++;
            if (!skipcolor)    {
                *dest++ = CHANGECOLOR;
                *dest++ = cfg.clr[POPDOWNMENU]
                            [HILITE_COLOR] [BG] | 0x80;
                *dest++ = bg | 0x80;
                *dest++ = *src++;
                *dest++ = RESETCOLOR;
            }
        }
        else
            *dest++ = *src++;
    }
    return (int) (dest - d);
}
```

#### LISTING_THREE

```c
/* ------------- menu.c ------------- */

#include "dflat.h"

static struct PopDown *FindCmd(MBAR *mn, int cmd)
{
    MENU *mnu = mn->PullDown;
    while (mnu->Title != (void *)-1)    {
        struct PopDown *pd = mnu->Selections;
        while (pd->SelectionTitle != NULL)    {
            if (pd->ActionId == cmd)
                return pd;
            pd++;
        }
        mnu++;
    }
    return NULL;
}

char *GetCommandText(MBAR *mn, int cmd)
{
    struct PopDown *pd = FindCmd(mn, cmd);
    if (pd != NULL)
        return pd->SelectionTitle;
    return NULL;
}

BOOL isCascadedCommand(MBAR *mn, int cmd)
{
    struct PopDown *pd = FindCmd(mn, cmd);
    if (pd != NULL)
        return pd->Attrib & CASCADED;
    return FALSE;
}

void ActivateCommand(MBAR *mn, int cmd)
{
    struct PopDown *pd = FindCmd(mn, cmd);
    if (pd != NULL)
        pd->Attrib &= ~INACTIVE;
}

void DeactivateCommand(MBAR *mn, int cmd)
{
    struct PopDown *pd = FindCmd(mn, cmd);
    if (pd != NULL)
        pd->Attrib |= INACTIVE;
}

BOOL isActive(MBAR *mn, int cmd)
{
    struct PopDown *pd = FindCmd(mn, cmd);
    if (pd != NULL)
        return !(pd->Attrib & INACTIVE);
    return FALSE;
}

BOOL GetCommandToggle(MBAR *mn, int cmd)
{
    struct PopDown *pd = FindCmd(mn, cmd);
    if (pd != NULL)
        return (pd->Attrib & CHECKED) != 0;
    return FALSE;
}

void SetCommandToggle(MBAR *mn, int cmd)
{
    struct PopDown *pd = FindCmd(mn, cmd);
    if (pd != NULL)
        pd->Attrib |= CHECKED;
}

void ClearCommandToggle(MBAR *mn, int cmd)
{
    struct PopDown *pd = FindCmd(mn, cmd);
    if (pd != NULL)
        pd->Attrib &= ~CHECKED;
}

void InvertCommandToggle(MBAR *mn, int cmd)
{
    struct PopDown *pd = FindCmd(mn, cmd);
    if (pd != NULL)
        pd->Attrib ^= CHECKED;
}
```

#### LISTING_FOUR

```c
/* ------------- sysmenu.c ------------ */

#include "dflat.h"

int SystemMenuProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int mx, my;
    WINDOW wnd1;
    switch (msg)    {
        case CREATE_WINDOW:
            wnd->holdmenu = ActiveMenuBar;
            ActiveMenuBar = &SystemMenu;
            SystemMenu.PullDown[0].Selection = 0;
            break;
        case LEFT_BUTTON:
            wnd1 = GetParent(wnd);
            mx = (int) p1 - GetLeft(wnd1);
            my = (int) p2 - GetTop(wnd1);
            if (HitControlBox(wnd1, mx, my))
                return TRUE;
            break;
        case LB_CHOOSE:
            PostMessage(wnd, CLOSE_WINDOW, 0, 0);
            break;
        case DOUBLE_CLICK:
            if (p2 == GetTop(GetParent(wnd)))    {
                PostMessage(GetParent(wnd), msg, p1, p2);
                SendMessage(wnd, CLOSE_WINDOW, TRUE, 0);
            }
            return TRUE;
        case SHIFT_CHANGED:
            return TRUE;
        case CLOSE_WINDOW:
            ActiveMenuBar = wnd->holdmenu;
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}

/* ------- Build a system menu -------- */
void BuildSystemMenu(WINDOW wnd)
{
    int lf = GetLeft(wnd)+1;
    int tp = GetTop(wnd)+1;
    int ht = MenuHeight(SystemMenu.PullDown[0].Selections);
    int wd = MenuWidth(SystemMenu.PullDown[0].Selections);
    WINDOW SystemMenuWnd;

    SystemMenu.PullDown[0].Selections[6].Accelerator =
        (GetClass(wnd) == APPLICATION) ? ALT_F4 : CTRL_F4;

    if (lf+wd > SCREENWIDTH-1)
        lf = (SCREENWIDTH-1) - wd;
    if (tp+ht > SCREENHEIGHT-2)
        tp = (SCREENHEIGHT-2) - ht;

    SystemMenuWnd = CreateWindow(POPDOWNMENU, NULL,
                lf,tp,ht,wd,NULL,wnd,SystemMenuProc, 0);

#ifdef INCLUDE_RESTORE
    if (wnd->condition == ISRESTORED)
        DeactivateCommand(&SystemMenu, ID_SYSRESTORE);
    else
        ActivateCommand(&SystemMenu, ID_SYSRESTORE);
#endif

    if (TestAttribute(wnd, MOVEABLE)
#ifdef INCLUDE_MAXIMIZE
            && wnd->condition != ISMAXIMIZED
#endif
                )
        ActivateCommand(&SystemMenu, ID_SYSMOVE);
    else
        DeactivateCommand(&SystemMenu, ID_SYSMOVE);

    if (wnd->condition != ISRESTORED ||
            TestAttribute(wnd, SIZEABLE) == FALSE)
        DeactivateCommand(&SystemMenu, ID_SYSSIZE);
    else
        ActivateCommand(&SystemMenu, ID_SYSSIZE);

#ifdef INCLUDE_MINIMIZE
    if (wnd->condition == ISMINIMIZED ||
            TestAttribute(wnd, MINMAXBOX) == FALSE)
        DeactivateCommand(&SystemMenu, ID_SYSMINIMIZE);
    else
        ActivateCommand(&SystemMenu, ID_SYSMINIMIZE);
#endif

#ifdef INCLUDE_MAXIMIZE
    if (wnd->condition != ISRESTORED ||
            TestAttribute(wnd, MINMAXBOX) == FALSE)
        DeactivateCommand(&SystemMenu, ID_SYSMAXIMIZE);
    else
        ActivateCommand(&SystemMenu, ID_SYSMAXIMIZE);
#endif

    SendMessage(SystemMenuWnd, BUILD_SELECTIONS,
                (PARAM) &SystemMenu.PullDown[0], 0);
    SendMessage(SystemMenuWnd, SHOW_WINDOW, 0, 0);
}

Copyright © 1992, Dr. Dobb's Journal
