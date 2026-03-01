
# C PROGRAMMING 9111

## D-Flat prozorske klase - Al Stevens

Proteklih meseci sam raspravljao i objavio većinu D-Flat koda koji podupire dizajn klasa prozora i njihovo upravljanje i prikaz. Ostali su moduli koda koji implementiraju svaku od standardnih D-Flat klasa u hijerarhiji. Proučavajući ovo, videćete kako funkcionišu časovi prozora. Ovo znanje će biti dragoceno kada počnete da izvodite sopstvene klase iz onih koje D-Flat pruža. Ova kolona započinje diskusiju o specifičnim klasama prozora pregledom klasa koje pruža D-Flat, diskusijom o tome kako funkcioniše hijerarhija klasa i opisom klase NORMAL, koja je osnovna klasa iz koje potiču sve ostale.

Tabela 1 navodi standardne klase D-Flat prozora. Na slici 1 prikazane su klase vindovs u njihovoj hijerarhiji. O većini ovih časova govorio sam u kolumni iz juna 1991. godine.

Tabela 1: Standardne klase D-Flat prozora

  Class | Description
  ----- | ---------------------------------------------------------------
  NORMAL | Base window for all window classes
  APPLICATION | Application window -- has the menu
  TEXTBOX | Contains text.  Base window for listbox, editbox, and so on
  LISTBOX | Contains a list of text -- base window for menubar
  EDITBOX | Text that a user can edit
  MENUBAR | The application's menu bar
  POPDOWNMENU | Pop-down menu
  BUTTON | Command button in a dialog box
  DIALOG | Dialog box -- parent to editbox, button, listbox, and so on
  ERRORBOX | For displaying an error message
  MESSAGEBOX | For displaying a message
  HELPBOX | Help window
  TEXT | Static text on a dialog box
  RADIOBUTTON | Radio button on a dialog box
  CHECKBOX | Check box on a dialog box
  STATUSBAR | Status bar at the bottom of application window

Slika 1 pokazuje kako sve klase prozora proizilaze iz osnovne klase NORMAL. U septembru ste naučili kako da dodate nove klase prozora u hijerarhiju dodavanjem unosa u classes.h i config.c.

### Modul za obradu prozora

Svaka klasa prozora treba modul za obradu prozora kome će D-Flat slati poruke. Ako klasa nema svoj modul za obradu prozora, D-Flat će poslati poruke modulu za obradu osnovne klase prozora. Modul za obradu klase prozora ima oblik prikazan u Primeru 1.

Primer 1: Modul za obradu klase prozora

```c
  int ClassProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
  {
      switch (msg)     {
          /* ---- process the window's messages ---- */
          default:
              break;
      }
      return BaseWndProc (CLASS, wnd, msg, p1, p2);
  }
```

ClassProc identifikator je jedinstven za svaku klasu, a konstanta CLASS u poslednjoj naredbi je ime klase prozora, na primer TEKSTBOKS ili MENIBAR.

D-Flat poziva modul za obradu prozora da prozoru pošalje poruku. Poruka može imati jedan ili dva parametra koji proširuju njeno značenje. Na primer, poruka LEFT_BUTTON se javlja kada korisnik pritisne levo dugme. Parametri poruke sadrže koordinate k i i gde je kursor miša bio pozicioniran kada je korisnik pritisnuo dugme.

D-Flat odlučuje koji prozor će dobiti poruku na osnovu trenutnog radnog okruženja. Poruke sa tastature idu do prozora koji ima fokus. Poruke miša idu do prozora gde se nalazi kursor miša u trenutku poruke. Poruke iz drugih prozora idu u prozore na koje su adresirane. Na primer, prozor može poslati poruku svom roditeljskom prozoru ili jednom od svojih podređenih prozora.

Modul za obradu prozora može ignorisati poruku. U stvari, većina modula za obradu prozora obrađuje samo mali podskup poruka. Pozivanjem modula BaseVndProc, modul za obradu prozora garantuje da će njegova osnovna klasa prozora obraditi poruke koje su mu potrebne. Na primer, nijedna klasa prozora ne mora da se bavi detaljima kretanja po ekranu. Modul za obradu prozora za klasu NORMAL brine o pomeranju prozora. Pošto je klasa NORMAL osnova za sve druge klase, sve ostale klase dele kod pomeranja prozora koji obezbeđuje klasa NORMAL. S druge strane, većina klasa prozora ima jedinstvene operacije za PAINT poruku. Neki od njih će u potpunosti obraditi poruku, neki će postaviti svoje PAINT operacije iznad onih osnovnih klasa, a neki će ignorisati poruku i dozvoliti osnovnoj klasi da upravlja celim procesom. U nekoliko slučajeva oni će presresti i odbiti poruku.

Modul za obradu prozora može sprečiti odlazak poruke u modul za obradu osnovnog prozora jednostavnim vraćanjem bez pozivanja funkcije BaseVndProc. Ponekad modul za obradu prozora želi da se procesi osnovne klase prvi dogode, tako da poziva BaseVndProc pre nego što uradi svoje stvari, a ne posle.

Setićete se iz diskusije o funkciji CreateVindov da možete dodati modul za obradu prozora instanci prozora. Ovo nije isto što i modul za obradu izvedenog prozora, iako ima neke od istih karakteristika. Modul za preklapanje prozora za postojeću klasu prvo otkriva poruke, koristi iste strategije za presretanje, ignorisanje i povećanje obrade klase, a zatim poziva funkciju DefaultVndProc umesto funkcije BaseVndProc. Primer aplikacije memopad.c od prošlog meseca kreirao je prozor aplikacije koji je koristio sopstveni modul za obradu prozora pored podrazumevanog koji D-Flat obezbeđuje.

### Normalna klasa

Naučićete o D-Flat prozorima odozgo prema dole, koristeći grafikon na slici 1 kao vodič. Ovog meseca počećemo sa klasom NORMAL prozora, koja sadrži kod zajednički za sve prozore. Modul za obradu prozora NormalProc upravlja sledećim operacijama: Prikazivanje, skrivanje i održavanje prozora po redosledu fokusiranih prozora; pomeranje i promena veličine prozora; farbanje praznih prozora za časove koji sami ne farbaju; prikazivanje ivice prozora i naslovne trake; minimiziranje, maksimiziranje i vraćanje prozora; pozivanje i upravljanje komandama iz sistemskog menija; i zatvaranje prozora. Funkcija NormalProc je uključena u [LISTING_ONE](#listing_one), "normal.c". Ta izvorna datoteka takođe uključuje kod za odlučivanje koje delove prozora treba ponovo obojiti kada se prozor koji se preklapa zatvori ili pomeri sa puta, i koji delovi prozora treba da se prefarbaju kada je u pitanju prednji deo fokusa.

Prvo ćemo pogledati NormalProc. Koristi format dat u Primeru 1 za module za obradu prozora. Modul za obradu prozora je u suštini jedna velika naredba prekidača sa slučajevima za svaku od poruka. Razgovaraćemo o porukama jednu po jednu. Zapamtite da je do trenutka kada funkcija NormalProc primi poruku, prošla kroz svaki modul za obradu prozora u hijerarhiji klasa, počevši od dna. Dakle, ako se poruka pošalje u prozor POPDOVNMENU, na primer, moduli za obradu prozora za klase POPDOVNMENU, LISTBOKS i TEKSTBOKS dobijaju je prvo pre nego što je vidi funkcija NormalProc. Štaviše, ako instanca prozora ima modul za obradu prozora, taj ga prvi dobija.

### Poruke

Funkcija CreateVindov šalje poruku CREATE_VINDOV nakon što je funkcija izgradila osnovnu strukturu prozora. NormalProc na kraju dobija poruku i dodaje prozor na dve povezane liste koje sam opisao prošlog meseca. Zatim, ako miš nije instaliran, NormalProc uklanja atribute trake za pomeranje prozora. Nema smisla imati trake za pomeranje ako nemate miša. Neki prozori imaju atribut SAVESELF, što znači da se nikada neće preklapati sa drugim prozorom. To uključuje iskačuće menije, okvire za poruke i modalne dijaloške okvire. Ovi prozori ne zahtevaju dodatne troškove za ponovno farbanje sadržaja prozora koji se preklapaju jer nikada neće izgubiti fokus sve dok postoje. Umesto toga, oni jednostavno čuvaju video memoriju koju će prepisati. NormalProc poziva funkciju GetVideoBuffer za takve prozore ako imaju atribut VISIBLE kada su kreirani.

Poruka SHOV_VINDOV je da se oslika ceo prozor. Ako prozor ima roditelja, a roditelj nije vidljiv, ova poruka ne radi ništa. U suprotnom, ako prozor ima atribut SAVESELF i njegov bafer za čuvanje video zapisa nije izgrađen, poruka poziva funkciju GetVideoBuffer. Poruka zatim postavlja atribut VISIBLE za prozor i šalje poruke PAINT i BORDER prozoru.

Odvojite trenutak i razmotrite upravo opisanu operaciju. Modul za obradu prozora za klasu prozora je upravo poslao poruku prozoru koji je ili je izveden iz klase. Zašto modul jednostavno ne bi mogao da izbegne prekomerno prosleđivanje poruke i uradi sve što radi kada primi poruku? Videćete mnogo ove vrste aktivnosti u D-Flat-u i drugim sistemima zasnovanim na porukama. To je jedan od razloga zašto su generalno manje efikasni od drugih modela programiranja. Funkcija NormalProc ne može jednostavno preneti kontrolu svom sopstvenom kodu za obradu poruka PAINT i BORDER. Klasa prozora je sasvim sigurno izvedena - direktno ili indirektno - iz klase NORMAL. Izvedena klasa bi mogla -- verovatno će -- imati sopstvenu obradu za PAINT poruku, ako ne i za poruku BORDER.

Nakon što sam sebi pošalje poruke PAINT i BORDER, ovaj prozor šalje poruku SHOV_VINDOV svakom od svojih podređenih prozora tako da će i oni biti prikazani.

Poruka HIDE_VINDOV se obrađuje samo ako prozor ima atribut VISIBLE. Prvo, poruka briše atribut VISIBLE iz prozora i svih podređenih prozora. Zatim mora ponovo prikazati ostale prozore koje prozor pokriva. Ako prozor ima bafer za čuvanje video zapisa, poruka poziva funkciju RestoreVideoBuffer. U suprotnom, poziva funkciju PaintOverLappers – o kojoj ćemo uskoro razgovarati – koja uklanja prozor slanjem poruka PAINT i BORDER na bilo koji prozor pokriven. Ne baš tako jednostavno, ali konceptualno ispravno.

Poruku DISPLAI_HELP postavlja prozor HELPBOKS kada menja prikaze prozora pomoći. NormalProc poziva funkciju DisplaiHelp kada dobije ovu poruku.

Poruka INSIDE_VINDOV se šalje da bi se utvrdilo da li su koordinate ekrana u parametrima unutar prozora.

Poruka KEIBOARD obrađuje pritiske na tastere koje izvedene klase ne presreću. Poruka prevodi taster F1 u poruku COMMAND ID_HELP. Ako se prozor pomera ili menja veličina, poruka KEIBOARD obrađuje tastere sa strelicama, taster Enter i taster Esc za ove operacije tako što ih prevodi u poruke MOUSE_CURSOR i MOUSE_MOVED. Ako je pritisak taster za pomoć F1, Alt+F6 iskoračni taster prozora, sistemski taster menija Alt+razmaknica ili taster za zatvaranje prozora Ctrl+F4, program obrađuje te tastere ovde jer su njihove funkcije zajedničke za sve prozore. Ako određena klasa prozora ne koristi jednu ili više ovih operacija, ili ih koristi drugačije, modul za obradu prozora klase će presresti i obraditi ili ignorisati ključ.

Svi ostali tasteri koji dođu do NormalProc-a se objavljuju kao poruke roditelju prozora. Ova procedura obezbeđuje da se tasteri za čitav sistem, kao što su tasteri za ubrzavanje menija, obrađuju u prozoru aplikacije, koji je roditelj svih ostalih prozora. NormalProc na sličan način prosleđuje poruke ADDSTATUS i SHIFT_CHANGED u roditeljski prozor bilo kog prozora koji ih prvobitno primi. Ako to ne bi trebalo da se desi za određenu izvedenu klasu prozora, modul za obradu prozora jednog od prozora u hijerarhiji će presresti i obraditi poruku.

Odvojite još trenutak da razmotrite razliku između hijerarhije klasa prozora i odnosa roditelj-dijete instanci prozora. To su dva različita logička lanca i lako ih je zbuniti. Instanca prozora je kreirana, a definicija i ponašanje tog prozora su funkcije njegove klase i osnovnih klasa iz kojih je izvedena njegova klasa. Isti prozor može biti dete drugog prozora iste ili različite klase, i može imati jedan ili više sopstvenih podređenih prozora, od kojih svaki može biti od klasa koje su iste ili različite jedna od druge i od svog roditelja.

Poruka PAINT za klasu prozora NORMAL jednostavno poziva ClearVindov da oslika prazan prozor. NORMALNI prozor nema prikaz podataka u svom klijentskom prostoru podataka.

Poruka BORDER poziva funkcije za prikaz ivice prozora i naslovne trake, i, ako prozor ima statusnu traku, šalje prozoru statusne trake poruku PAINT.

Poruka COMMAND uključuje parametar koji identifikuje komandu koja se obrađuje. Kao opšte pravilo, komandne poruke nastaju kada korisnik izabere komandu iz menija ili izvrši neku aktivnost u okviru za dijalog. D-Flat šalje KOMANDNU poruku prozoru dokumenta koji je trenutno u fokusu, dijaloškom okviru ili prozoru koji je roditelj menija. Prvi parametar poruke COMMAND sadrži kod komande. NormalProc upravlja komandom ID_HELP i komandama iz Primera 2, koje šalje sistemski meni.

Primer 2: Ove komande, kojima upravlja NormalProc, šalje sistemski meni.

  ID_SYSRESTORE to restore a minimized or maximized window
  ID_SYSMOVE to move a window
  ID_SYSSIZE to resize a window
  ID_SYSMINIMIZE to minimize a window
  ID_SYSMAXIMIZE to maximize a window
  ID_SYSCLOSE to close a window

Prozor prima SETFOCUS poruku kada treba da primi ili napusti fokus na unosu korisnika. Da bi prozor primio fokus ulaza, poruka ima tačnu vrednost u prvom parametru. Da biste napustili fokus, prvi parametar je netačan.

Brisanje fokusa iz prozora se sastoji od postavljanja globalnog tipa inFocus VINDOV na NULL i slanja poruke BORDER prozoru. Trenutni prozor u fokusu identifikuje se za korisnika pomoću dvolinije i istaknute naslovne trake, tako da ivica mora biti ponovo oslikana kada prozor izgubi fokus.

Postavljanje fokusa na prozor je komplikovanije. Oznaka za ponovno iscrtavanje će pokazati da li prozor treba selektivno prefarbati za one njegove delove koji se preklapaju sa drugim prozorima. Ako je prozor trenutno vidljiv i nema atribut SAVESELF, možda će morati da se ponovo nacrta. Međutim, ako je prozor podređeni prozoru APLIKACIJE i sam nema dece, neće ga trebati selektivno ponovo farbati. Kada se ta odluka donese, program šalje SETFOCUS poruku sa lažnim parametrom u prozor koji je trenutno u fokusu kako bi mu rekao da se odrekne fokusa. Ako je indikator Redrav podešen, program poziva funkciju PaintUnderLappers da bi oslikao one delove prozora koji se preklapaju sa drugim prozorima. Zatim postavlja prozor na vrh liste povezanih sa infokusom i stavlja svoju VINDOV ručicu u globalnu promenljivu inFocus. Ako je indikator Redrav podešen, program šalje prozoru poruku GRANICA da mu kaže da promeni svoju ivicu na upravo opisanu konfiguraciju u fokusu. U suprotnom, poruka SHOV_VINDOV će reći prozoru da se potpuno prefarba. Ako prozor nema roditelja ili je njegov roditelj prozor aplikacije ili okvir za dijalog, poruka SHOV_VINDOV ide u sam prozor. U suprotnom, ide u roditeljski prozor prozora. Možete postaviti fokus na podređeni prozor kada se ceo ili deo njegovog nadređenog prozora preklapa sa drugim prozorima.

Ako poruka DOUBLE_CLICK pogodi kontrolnu kutiju prozora, program šalje prozoru poruku CLOSE_VINDOV. Ako poruka LEFT_BUTTON pogodi kontrolnu kutiju prozora, program šalje poruku LEFT_BUTTON roditelju prozora. LEFT_BUTTON takođe može pogoditi okvire za minimiziranje, maksimiziranje ili vraćanje, u kom slučaju program šalje odgovarajuću poruku prozoru. Ako poruka udari u naslovnu traku, program postavlja operaciju pomeranja prozora hvatanjem miša i pozivanjem funkcije dragborder. Ako poruka pogodi donji desni ugao prozora, program podešava operaciju promene veličine prozora na isti način.

Poruka MOUSE_MOVED upravlja pomeranjem i dimenzionisanjem prozora tako što poziva funkcije dragborder i sizeborder svaki put kada dođe do pomeranja miša. BUTTON_RELEASED prekida svako pomeranje ili promenu veličine prozora koji su na snazi.

Slede poruke MAKSIMIRAJ, MINIMIZUJ, VRATI, PREMESTI i VELIČINA. Ove poruke obavljaju te operacije na prozoru. Poruka MOVE šalje odgovarajuće MOVE poruke svim podređenim prozorima prozora. Ako promenite veličinu prozora koji ima maksimizirani podređeni prozor, program detetu šalje odgovarajuću poruku SIZE.

Poruka CLOSE_VINDOV šalje HIDE_VINDOV poruku prozoru, a zatim šalje CLOSE_VINDOV poruke svim podređenim prozorima prozora. Ako prozor ima fokus, program poziva funkciju SetPrevFocus da bi postavio fokus na onaj koji ga je imao neposredno pre nego što ga je ovaj prozor dobio. Zatim program uklanja strukturu prozora sa liste povezanih prozora i oslobađa memoriju koju koristi struktura prozora i njen bafer za čuvanje video zapisa.

### Ostale funkcije

Izvorna datoteka normal.c uključuje funkcije za izračunavanje prostora za D-Flat verziju minimiziranih ikona prozora. Kada korisnik minimizira prozor, D-Flat jednostavno menja njegovu veličinu na mali prozor i pronalazi mesto za njega negde u prostoru podataka klijenta prozora aplikacije. Funkcija LoverRight izračunava pravougaonik koji zauzima prvi minimizirani prozor, koji će se prikazati u donjem desnom uglu prozora aplikacije. Funkcija PositionIcon izračunava sledeće pozicije susednih ikona levo i iznad prve.

Funkcije TerminateMoveSize, dragborder i sizeborder podržavaju pomeranje i određivanje veličine prozora. Kada pomerate ili dimenzionirate prozor pomoću tastature ili miša, D-Flat crta ivicu okvira lažnog prozora oko originalne pozicije prozora. Interaktivno pomerate ili menjate veličinu tog lažnog okvira pomoću funkcija prevlačenja i promene veličine ivica. Kada završite, funkcija TerminateMoveSize se oslobađa lažnog okvira.

Sledećih nekoliko funkcija upravlja ponovnim prikazom prozora dokumenata kada ih ima nekoliko na ekranu, a jedan od njih bude skriven ili doveden na vrh. Kada prozor postane sakriven, bilo zato što ga pomerite na drugu poziciju ili zatvorite, prozori koji se preklapaju moraju biti ponovo ofarbani. D-Flat počinje na dnu liste u fokusu i svakom prozoru šalje poruku PAINT. Taj proces bi funkcionisao sam od sebe, ali bi bio dugotrajan i vizuelno ometajući ako bi bilo uključeno mnogo prozora.

Umesto da potpuno prefarba svaki prozor koji se preklapa, NormalProc poziva funkciju PaintOverLappers da bi izračunao i prefarbao prostor pravougaonika koji se preklapa unutar svakog preklapanog prozora. PORUKE PAINT za svaku klasu prozora i poruka BORDER za sve klase prozora priznaju prvi parametar kao RECT strukturu koja je relativni pravougaonik unutar njih koji se ponovo farba. PaintOverLappers čuva VINDOV ručku prozora koji se preklapa u promenljivoj HiddenVindov i poziva PaintOverParents. Ova funkcija poziva sebe rekurzivno za svog roditelja, a zatim poziva PaintOver da ponovo oslika prozor subjekta i PaintOverChildren da ponovo oslika podređene prozore prozora. Rekurzivni pozivi imaju za cilj da osiguraju da se najdalje obradi najudaljeniji predak prozora i njegova deca. Funkcija PaintOverChildren obrađuje sve podređene prozore za prozor. Poziva PaintOver da slika svako dete, a zatim poziva sebe rekurzivno u slučaju da podređeni prozor ima podređene prozore. Funkcija PaintOver izračunava pravougaonik koji je presek pravougaonika za slikanje i onog koji je sakriven – onog čiji je ručnik u promenljivoj HiddenVindov. Zatim poziva funkciju PaintOverLap, koja iz pravougaonika odlučuje koji od sastavnih delova prozora treba da se ofarbaju.

Funkcija PaintUnderLappers je druga strana medalje. Program poziva funkciju kada prozor dobije fokus i dođe na prednji deo ekrana. Umesto da nepotrebno ponovo farba ceo prozor, PaintUnderLappers izračunava pravougaonik koji uključuje sve delove prozora koji se preklapaju. Prvo, locira najstarijeg pretka prozora i radi sa njim pozivanjem funkcije PaintUnder. Ova funkcija gleda u svaki drugi prozor u sistemu. Ako je prozor potomak onog koji se obrađuje, funkcija ga preskače. Potomci će biti prefarbani kao rezultat prefarbanja njihovog pretka. Ako je prozor predak, i on se preskače. Ovaj pristup osigurava da će se posmatrati samo braća i sestre i nepovezani prozori. Kada funkcija pronađe takav prozor, traži da vidi da li je pronađeni prozor ispred prozora subjekta u lancu fokusa. Ako je tako, funkcija čuva ručicu pronađenog prozora u promenljivoj HiddenVindov i poziva funkciju PaintOver za prozor predmeta. Nakon što je PaintUnder obradio sve ostale prozore u odnosu na prozor subjekta na ovaj način, on se rekurzivno poziva za svaki od podređenih prozora prozora subjekta.

Uzimajući u obzir prethodna dva paragrafa, da li se pitate zašto su sistemi prozora zasnovani na porukama vođeni događajima generalno manje brzi od nekih svojih prethodnika?

#### LISTING_ONE

```c
/* ------------- normal.c ------------ */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <conio.h>
#include <dos.h>
#include "dflat.h"

#ifdef INCLUDE_MULTIDOCS
static void near PaintOverLappers(WINDOW wnd);
static void near PaintUnderLappers(WINDOW wnd);
#endif
static int InsideWindow(WINDOW, int, int);
#ifdef INCLUDE_SYSTEM_MENUS
static void TerminateMoveSize(void);
static void SaveBorder(RECT);
static void RestoreBorder(RECT);
static RECT PositionIcon(WINDOW);
static void near dragborder(WINDOW, int, int);
static void near sizeborder(WINDOW, int, int);
static int px = -1, py = -1;
static int diff;
static int conditioning;
static struct window dwnd = {DUMMY, NULL, NULL, NormalProc, {-1,-1,-1,-1}};
static int *Bsave;
static int Bht, Bwd;
int WindowMoving;
int WindowSizing;
#endif
/* -------- array of class definitions -------- */
CLASSDEFS classdefs[] = {
    #undef ClassDef
    #define ClassDef(c,b,p,a) {b,p,a},
    #include "classes.h"
};
WINDOW HiddenWindow;

int NormalProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int mx = (int) p1 - GetLeft(wnd);
    int my = (int) p2 - GetTop(wnd);
    int DoneClosing = FALSE;
    switch (msg)    {
        case CREATE_WINDOW:
            AppendBuiltWindow(wnd);  /* add to the lists */
            AppendFocusWindow(wnd);
#ifdef INCLUDE_SCROLLBARS
            if (!SendMessage(NULL, MOUSE_INSTALLED, 0, 0))
                ClearAttribute(wnd, VSCROLLBAR | HSCROLLBAR);
#endif
            if (TestAttribute(wnd, SAVESELF) && isVisible(wnd))
                GetVideoBuffer(wnd);
            break;
        case SHOW_WINDOW:
            if ((GetParent(wnd) == NULL || isVisible(GetParent(wnd)))
#ifdef INCLUDE_SYSTEM_MENUS
                            && !conditioning
#endif
                                                    )    {
                WINDOW cwnd = Focus.FirstWindow;
                if (TestAttribute(wnd, SAVESELF) &&
                                wnd->videosave == NULL)
                    GetVideoBuffer(wnd);
                SetVisible(wnd);
                SendMessage(wnd, PAINT, 0, 0);
                SendMessage(wnd, BORDER, 0, 0);
                /* --- show the children of this window --- */
                while (cwnd != NULL)    {
                    if (GetParent(cwnd) == wnd &&
                            cwnd->condition != ISCLOSING)
                        SendMessage(cwnd, msg, p1, p2);
                    cwnd = NextWindow(cwnd);
                }
            }
            break;
        case HIDE_WINDOW:
            if (isVisible(wnd)
#ifdef INCLUDE_SYSTEM_MENUS
                && !conditioning
#endif
                                )    {
                WINDOW cwnd = Focus.LastWindow;
                /* --- hide the children of this window --- */
                while (cwnd != NULL)    {
                    if (GetParent(cwnd) == wnd)
                        ClearVisible(cwnd);
                    cwnd = PrevWindow(cwnd);
                }
                ClearVisible(wnd);
                /* --- paint what this window covered --- */
                if (wnd->videosave != NULL)
                    RestoreVideoBuffer(wnd);
#ifdef INCLUDE_MULTIDOCS
                else
                    PaintOverLappers(wnd);
#endif
            }
            break;
#ifdef INCLUDE_HELP
        case DISPLAY_HELP:
            DisplayHelp(wnd, (char *)p1);
            break;
#endif
        case INSIDE_WINDOW:
            return InsideWindow(wnd, (int) p1, (int) p2);
        case KEYBOARD:
#ifdef INCLUDE_SYSTEM_MENUS
            if (WindowMoving || WindowSizing)    {
                /* -- move or size a window with keyboard -- */
                int x, y;
                x=WindowMoving?GetLeft(&dwnd):GetRight(&dwnd);
                y=WindowMoving?GetTop(&dwnd):GetBottom(&dwnd);
                switch ((int)p1)    {
                    case ESC:
                        TerminateMoveSize();
                        return TRUE;
                    case UP:
                        if (y)
                            --y;
                        break;
                    case DN:
                        if (y < SCREENHEIGHT-1)
                            y++;
                        break;
                    case FWD:
                        if (x < SCREENWIDTH-1)
                            x++;
                        break;
                    case BS:
                        if (x)
                            --x;
                        break;
                    case '\r':
                        SendMessage(wnd,BUTTON_RELEASED,x,y);
                    default:
                        return TRUE;
                }
                /* -- use the mouse functions to move/size - */
                SendMessage(wnd, MOUSE_CURSOR, x, y);
                SendMessage(wnd, MOUSE_MOVED, x, y);
                break;
            }
#endif
            switch ((int)p1)    {
#ifdef INCLUDE_HELP
                case F1:
                    SendMessage(wnd, COMMAND, ID_HELP, 0);
                    return TRUE;
#endif
                case ALT_F6:
                    SetNextFocus(inFocus);
                    SkipSystemWindows(FALSE);
                    return TRUE;
#ifdef INCLUDE_SYSTEM_MENUS
                case ' ':
                    if ((int)p2 & ALTKEY)
                        if (TestAttribute(wnd, HASTITLEBAR))
                            if (TestAttribute(wnd, CONTROLBOX))
                                BuildSystemMenu(wnd);
                    return TRUE;
#endif
                case CTRL_F4:
                    SendMessage(wnd, CLOSE_WINDOW, 0, 0);
                    SkipSystemWindows(FALSE);
                    return TRUE;
                default:
                    break;
            }
            /* ------- fall through ------- */
        case ADDSTATUS:
        case SHIFT_CHANGED:
            if (GetParent(wnd) != NULL)
                PostMessage(GetParent(wnd), msg, p1, p2);
            break;
        case PAINT:
            if (isVisible(wnd))
                ClearWindow(wnd, (RECT *)p1, ' ');
            break;
        case BORDER:
            if (isVisible(wnd))    {
                if (TestAttribute(wnd, HASBORDER))
                    RepaintBorder(wnd, (RECT *)p1);
                else if (TestAttribute(wnd, HASTITLEBAR))
                    DisplayTitle(wnd, (RECT *)p1);
                if (wnd->StatusBar != NULL)
                    SendMessage(wnd->StatusBar, PAINT, p1, 0);
            }
            break;
#ifdef INCLUDE_SYSTEM_MENUS
        case COMMAND:
            switch ((int)p1)    {
#ifdef INCLUDE_HELP
                case ID_HELP:
                    DisplayHelp(wnd,ClassNames[GetClass(wnd)]);
                    break;
#endif
                case ID_SYSRESTORE:
                    SendMessage(wnd, RESTORE, 0, 0);
                    break;
                case ID_SYSMOVE:
                    SendMessage(wnd, CAPTURE_MOUSE, TRUE, (PARAM) &dwnd);
                    SendMessage(wnd, CAPTURE_KEYBOARD, TRUE, (PARAM) &dwnd);
                    SendMessage(wnd, MOUSE_CURSOR, GetLeft(wnd), GetTop(wnd));
                    WindowMoving = TRUE;
                    dragborder(wnd, GetLeft(wnd), GetTop(wnd));
                    break;
                case ID_SYSSIZE:
                 SendMessage(wnd, CAPTURE_MOUSE, TRUE, (PARAM) &dwnd);
                 SendMessage(wnd, CAPTURE_KEYBOARD, TRUE, (PARAM) &dwnd);
                 SendMessage(wnd, MOUSE_CURSOR, GetRight(wnd), GetBottom(wnd));
                 WindowSizing = TRUE;
                 dragborder(wnd, GetLeft(wnd), GetTop(wnd));
                 break;
                case ID_SYSMINIMIZE:
                    SendMessage(wnd, MINIMIZE, 0, 0);
                    break;
                case ID_SYSMAXIMIZE:
                    SendMessage(wnd, MAXIMIZE, 0, 0);
                    break;
                case ID_SYSCLOSE:
                    SendMessage(wnd, CLOSE_WINDOW, 0, 0);
                    break;
                default:
                    break;
            }
            break;
#endif
        case SETFOCUS:
            if (p1 && inFocus != wnd)    {
                WINDOW pwnd = GetParent(wnd);
                int Redraw = isVisible(wnd) &&
                       !TestAttribute(wnd, SAVESELF);
                if (GetClass(pwnd) == APPLICATION)    {
                    WINDOW cwnd = Focus.FirstWindow;
                    /* -- if no children, do not need selective
                        redraw --- */
                    while (cwnd != NULL)    {
                        if (GetParent(cwnd) == wnd)
                            break;
                        cwnd = NextWindow(cwnd);
                    }
                    if (cwnd == NULL)
                        Redraw = FALSE;
                }
                /* ---- setting focus ------ */
                SendMessage(inFocus, SETFOCUS, FALSE, 0);

                if (Redraw)
                    PaintUnderLappers(wnd);

                /* remove window from list */
                RemoveFocusWindow(wnd);
                /* move window to end of list */
                AppendFocusWindow(wnd);
                inFocus = wnd;

                if (Redraw)
                    SendMessage(wnd, BORDER, 0, 0);
                else    {
                    if (pwnd == NULL ||
                        GetClass(pwnd) == DIALOG ||
                            DerivedClass(GetClass(pwnd)) ==
                                DIALOG || GetClass(pwnd) ==
                                        APPLICATION)
                        SendMessage(wnd, SHOW_WINDOW, 0, 0);
                    else
                        SendMessage(pwnd, SHOW_WINDOW, 0, 0);
                }
            }
            else if (!p1 && inFocus == wnd)    {
                /* -------- clearing focus --------- */
                inFocus = NULL;
                SendMessage(wnd, BORDER, 0, 0);
            }
            break;
        case DOUBLE_CLICK:
#ifdef INCLUDE_SYSTEM_MENUS
            if (!WindowSizing && !WindowMoving)
#endif
                if (HitControlBox(wnd, mx, my))
                    PostMessage(wnd, CLOSE_WINDOW, 0, 0);
            break;
        case LEFT_BUTTON:
#ifdef INCLUDE_SYSTEM_MENUS
            if (WindowSizing || WindowMoving)
                break;
#endif
            if (HitControlBox(wnd, mx, my))    {
                BuildSystemMenu(wnd);
                break;
            }
#ifdef INCLUDE_SYSTEM_MENUS
            if (my == 0 && mx > -1 && mx < WindowWidth(wnd))  {
                /* ---------- hit the top border -------- */
                if (TestAttribute(wnd, MINMAXBOX) &&
                        TestAttribute(wnd, HASTITLEBAR))  {
                    if (mx == WindowWidth(wnd)-2)    {
                        if (wnd->condition == ISRESTORED)
                            /* --- hit the maximize box --- */
                            SendMessage(wnd, MAXIMIZE, 0, 0);
                        else
                            /* --- hit the restore box --- */
                            SendMessage(wnd, RESTORE, 0, 0);
                        break;
                    }
                    if (mx == WindowWidth(wnd)-3)    {
                        /* --- hit the minimize box --- */
                        if (wnd->condition != ISMINIMIZED)
                            SendMessage(wnd, MINIMIZE, 0, 0);
                        break;
                    }
                }
                if (wnd->condition != ISMAXIMIZED &&
                            TestAttribute(wnd, MOVEABLE))    {
                    WindowMoving = TRUE;
                    px = mx;
                    py = my;
                    diff = (int) mx;
                    SendMessage(wnd, CAPTURE_MOUSE, TRUE,
                        (PARAM) &dwnd);
                    dragborder(wnd, GetLeft(wnd), GetTop(wnd));
                }
                break;
            }
            if (mx == WindowWidth(wnd)-1 &&
                    my == WindowHeight(wnd)-1)    {
                /* ------- hit the resize corner ------- */
                if (wnd->condition == ISMINIMIZED ||
                        !TestAttribute(wnd, SIZEABLE))
                    break;
                if (wnd->condition == ISMAXIMIZED)    {
                    if (TestAttribute(GetParent(wnd),HASBORDER))
                        break;
                    /* ----- resizing a maximized window over a
                            borderless parent ----- */
                    wnd = GetParent(wnd);
                }
                WindowSizing = TRUE;
                SendMessage(wnd, CAPTURE_MOUSE,
                    TRUE, (PARAM) &dwnd);
                dragborder(wnd, GetLeft(wnd), GetTop(wnd));
            }
#endif
            break;
#ifdef INCLUDE_SYSTEM_MENUS
        case MOUSE_MOVED:
            if (WindowMoving)    {
                int leftmost = 0, topmost = 0,
                    bottommost = SCREENHEIGHT-2,
                    rightmost = SCREENWIDTH-2;
                int x = (int) p1 - diff;
                int y = (int) p2;
                if (GetParent(wnd) != NULL &&
                        !TestAttribute(wnd, NOCLIP))    {
                    WINDOW wnd1 = GetParent(wnd);
                    topmost    = GetClientTop(wnd1);
                    leftmost   = GetClientLeft(wnd1);
                    bottommost = GetClientBottom(wnd1);
                    rightmost  = GetClientRight(wnd1);
                }
                if (x < leftmost || x > rightmost ||
                        y < topmost || y > bottommost)    {
                    x = max(x, leftmost);
                    x = min(x, rightmost);
                    y = max(y, topmost);
                    y = min(y, bottommost);
                    SendMessage(NULL,MOUSE_CURSOR,x+diff,y);
                }
                if (x != px || y != py)    {
                    px = x;
                    py = y;
                    dragborder(wnd, x, y);
                }
                return TRUE;
            }
            if (WindowSizing)    {
                sizeborder(wnd, (int) p1, (int) p2);
                return TRUE;
            }
            break;
        case BUTTON_RELEASED:
            if (WindowMoving || WindowSizing)    {
                if (WindowMoving)
                    PostMessage(wnd,MOVE,dwnd.rc.lf,dwnd.rc.tp);
                else
                    PostMessage(wnd,SIZE,dwnd.rc.rt,dwnd.rc.bt);
                TerminateMoveSize();
            }
            break;
        case MAXIMIZE:
            if (wnd->condition != ISMAXIMIZED)    {
                RECT rc = {0, 0, 0, 0};
                RECT holdrc;
                holdrc = wnd->RestoredRC;
                rc.rt = SCREENWIDTH-1;
                rc.bt = SCREENHEIGHT-1;
                if (GetParent(wnd))
                    rc = ClientRect(GetParent(wnd));
                wnd->condition = ISMAXIMIZED;
                SendMessage(wnd, HIDE_WINDOW, 0, 0);
                conditioning = TRUE;
                SendMessage(wnd, MOVE, RectLeft(rc), RectTop(rc));
                SendMessage(wnd, SIZE, RectRight(rc), RectBottom(rc));
                conditioning = FALSE;
                if (wnd->restored_attrib == 0)
                    wnd->restored_attrib = wnd->attrib;
#ifdef INCLUDE_SHADOWS
                ClearAttribute(wnd, SHADOW);
#endif
                SendMessage(wnd, SHOW_WINDOW, 0, 0);
                wnd->RestoredRC = holdrc;
            }
            break;
        case MINIMIZE:
            if (wnd->condition != ISMINIMIZED)    {
                RECT rc;
                RECT holdrc;

                holdrc = wnd->RestoredRC;
                rc = PositionIcon(wnd);
                wnd->condition = ISMINIMIZED;
                SendMessage(wnd, HIDE_WINDOW, 0, 0);
                conditioning = TRUE;
                SendMessage(wnd, MOVE, RectLeft(rc), RectTop(rc));
                SendMessage(wnd, SIZE, RectRight(rc), RectBottom(rc));
                SetPrevFocus(wnd);
                conditioning = FALSE;
                if (wnd->restored_attrib == 0)
                    wnd->restored_attrib = wnd->attrib;
                ClearAttribute(wnd,
                    SHADOW | SIZEABLE | HASMENUBAR |
                    VSCROLLBAR | HSCROLLBAR);
                SendMessage(wnd, SHOW_WINDOW, 0, 0);
                wnd->RestoredRC = holdrc;
            }
            break;
        case RESTORE:
            if (wnd->condition != ISRESTORED)    {
                RECT holdrc;
                holdrc = wnd->RestoredRC;
                wnd->condition = ISRESTORED;
                SendMessage(wnd, HIDE_WINDOW, 0, 0);
                wnd->attrib = wnd->restored_attrib;
                wnd->restored_attrib = 0;
                conditioning = TRUE;
                SendMessage(wnd, MOVE, wnd->RestoredRC.lf, wnd->RestoredRC.tp);
                wnd->RestoredRC = holdrc;
                SendMessage(wnd, SIZE, wnd->RestoredRC.rt, wnd->RestoredRC.bt);
                SendMessage(wnd, SETFOCUS, TRUE, 0);
                conditioning = FALSE;
                SendMessage(wnd, SHOW_WINDOW, 0, 0);
            }
            break;
        case MOVE:    {
            WINDOW wnd1 = Focus.FirstWindow;
            int wasVisible = isVisible(wnd);
            int xdif = (int) p1 - wnd->rc.lf;
            int ydif = (int) p2 - wnd->rc.tp;

            if (xdif == 0 && ydif == 0)
                return FALSE;
            if (wasVisible)
                SendMessage(wnd, HIDE_WINDOW, 0, 0);
            wnd->rc.lf = (int) p1;
            wnd->rc.tp = (int) p2;
            wnd->rc.rt = GetLeft(wnd)+WindowWidth(wnd)-1;
            wnd->rc.bt = GetTop(wnd)+WindowHeight(wnd)-1;
            if (wnd->condition == ISRESTORED)
                wnd->RestoredRC = wnd->rc;
            while (wnd1 != NULL)    {
                if (GetParent(wnd1) == wnd)
                  SendMessage(wnd1, MOVE, wnd1->rc.lf+xdif, wnd1->rc.tp+ydif);
                wnd1 = NextWindow(wnd1);
            }
            if (wasVisible)
                SendMessage(wnd, SHOW_WINDOW, 0, 0);
            break;
        }
        case SIZE:    {
            int wasVisible = isVisible(wnd);
            WINDOW wnd1 = Focus.FirstWindow;
            RECT rc;
            int xdif = (int) p1 - wnd->rc.rt;
            int ydif = (int) p2 - wnd->rc.bt;

            if (xdif == 0 && ydif == 0)
                return FALSE;
            if (wasVisible)
                SendMessage(wnd, HIDE_WINDOW, 0, 0);
            wnd->rc.rt = (int) p1;
            wnd->rc.bt = (int) p2;
            wnd->ht = GetBottom(wnd)-GetTop(wnd)+1;
            wnd->wd = GetRight(wnd)-GetLeft(wnd)+1;

            if (wnd->condition == ISRESTORED)
                wnd->RestoredRC = WindowRect(wnd);

            rc = ClientRect(wnd);
            while (wnd1 != NULL)    {
                if (GetParent(wnd1) == wnd &&
                        wnd1->condition == ISMAXIMIZED)
                    SendMessage(wnd1, SIZE, RectRight(rc), RectBottom(rc));
                wnd1 = NextWindow(wnd1);
            }

            if (wasVisible)
                SendMessage(wnd, SHOW_WINDOW, 0, 0);
            break;
        }
#endif
        case CLOSE_WINDOW:
            wnd->condition = ISCLOSING;
            if (wnd->PrevMouse != NULL)
                SendMessage(wnd, RELEASE_MOUSE, 0, 0);
            if (wnd->PrevKeyboard != NULL)
                SendMessage(wnd, RELEASE_KEYBOARD, 0, 0);
            /* ----------- hide this window ------------ */
            SendMessage(wnd, HIDE_WINDOW, 0, 0);
            /* --- close the children of this window --- */
            while (!DoneClosing)    {
                WINDOW wnd1 = Focus.LastWindow;
                DoneClosing = TRUE;
                while (wnd1 != NULL)    {
                    WINDOW prwnd = PrevWindow(wnd1);
                    if (GetParent(wnd1) == wnd)    {
                        if (inFocus == wnd1)    {
                            RemoveFocusWindow(wnd);
                            AppendFocusWindow(wnd);
                            inFocus = wnd;
                        }
                        SendMessage(wnd1,CLOSE_WINDOW,0,0);
                        DoneClosing = FALSE;
                        break;
                    }
                    wnd1 = prwnd;
                }
            }
            /* --- change focus if this window had it -- */
            SetPrevFocus(wnd);
            /* ------- remove this window from the
                    list of open windows ------------- */
            RemoveBuiltWindow(wnd);
            /* ------- remove this window from the
                    list of in-focus windows ---------- */
            RemoveFocusWindow(wnd);
            /* -- free memory allocated to this window - */
            if (wnd->title != NULL)
                free(wnd->title);
            if (wnd->videosave != NULL)
                free(wnd->videosave);
            free(wnd);
            break;
        default:
            break;
    }
    return TRUE;
}
/* ---- compute lower left icon space in a rectangle ---- */
#ifdef INCLUDE_SYSTEM_MENUS
static RECT LowerRight(RECT prc)
{
    RECT rc;
    RectLeft(rc) = RectRight(prc) - ICONWIDTH;
    RectTop(rc) = RectBottom(prc) - ICONHEIGHT;
    RectRight(rc) = RectLeft(rc)+ICONWIDTH-1;
    RectBottom(rc) = RectTop(rc)+ICONHEIGHT-1;
    return rc;
}
/* ----- compute a position for a minimized window icon ---- */
static RECT PositionIcon(WINDOW wnd)
{
    RECT rc;
    RectLeft(rc) = SCREENWIDTH-ICONWIDTH;
    RectTop(rc) = SCREENHEIGHT-ICONHEIGHT;
    RectRight(rc) = SCREENWIDTH-1;
    RectBottom(rc) = SCREENHEIGHT-1;
    if (GetParent(wnd))    {
        WINDOW wnd1 = (WINDOW) -1;
        RECT prc;
        prc = WindowRect(GetParent(wnd));
        rc = LowerRight(prc);
        /* - search for icon available location - */
        while (wnd1 != NULL)    {
            wnd1 = GetFirstChild(GetParent(wnd));
            while (wnd1 != NULL)    {
                if (wnd1->condition == ISMINIMIZED)    {
                    RECT rc1;
                    rc1 = WindowRect(wnd1);
                    if (RectLeft(rc1) == RectLeft(rc) &&
                            RectTop(rc1) == RectTop(rc))    {
                        RectLeft(rc) -= ICONWIDTH;
                        RectRight(rc) -= ICONWIDTH;
                        if (RectLeft(rc) < RectLeft(prc)+1)   {
                            RectLeft(rc) =
                                RectRight(prc)-ICONWIDTH;
                            RectRight(rc) =
                                RectLeft(rc)+ICONWIDTH-1;
                            RectTop(rc) -= ICONHEIGHT;
                            RectBottom(rc) -= ICONHEIGHT;
                            if (RectTop(rc) < RectTop(prc)+1)
                                return LowerRight(prc);
                        }
                        break;
                    }
                }
                wnd1 = GetNextChild(GetParent(wnd), wnd1);
            }
        }
    }
    return rc;
}
/* ----- terminate the move or size operation ----- */
static void TerminateMoveSize(void)
{
    px = py = -1;
    diff = 0;
    SendMessage(&dwnd, RELEASE_MOUSE, TRUE, 0);
    SendMessage(&dwnd, RELEASE_KEYBOARD, TRUE, 0);
    RestoreBorder(dwnd.rc);
    WindowMoving = WindowSizing = FALSE;
}
/* ---- build a dummy window border for moving or sizing --- */
static void near dragborder(WINDOW wnd, int x, int y)
{
    RestoreBorder(dwnd.rc);
    /* ------- build the dummy window -------- */
    dwnd.rc.lf = x;
    dwnd.rc.tp = y;
    dwnd.rc.rt = dwnd.rc.lf+WindowWidth(wnd)-1;
    dwnd.rc.bt = dwnd.rc.tp+WindowHeight(wnd)-1;
    dwnd.ht = WindowHeight(wnd);
    dwnd.wd = WindowWidth(wnd);
    dwnd.parent = GetParent(wnd);
    dwnd.attrib = VISIBLE | HASBORDER | NOCLIP;
    InitWindowColors(&dwnd);
    SaveBorder(dwnd.rc);
    RepaintBorder(&dwnd, NULL);
}
/* ---- write the dummy window border for sizing ---- */
static void near sizeborder(WINDOW wnd, int rt, int bt)
{
    int leftmost = GetLeft(wnd)+10;
    int topmost = GetTop(wnd)+3;
    int bottommost = SCREENHEIGHT-1;
    int rightmost  = SCREENWIDTH-1;
    if (GetParent(wnd))    {
        bottommost = min(bottommost, GetClientBottom(GetParent(wnd)));
        rightmost  = min(rightmost, GetClientRight(GetParent(wnd)));
    }
    rt = min(rt, rightmost);
    bt = min(bt, bottommost);
    rt = max(rt, leftmost);
    bt = max(bt, topmost);
    SendMessage(NULL, MOUSE_CURSOR, rt, bt);

    if (rt != px || bt != py)
        RestoreBorder(dwnd.rc);

    /* ------- change the dummy window -------- */
    dwnd.ht = bt-dwnd.rc.tp+1;
    dwnd.wd = rt-dwnd.rc.lf+1;
    dwnd.rc.rt = rt;
    dwnd.rc.bt = bt;
    if (rt != px || bt != py)    {
        px = rt;
        py = bt;
        SaveBorder(dwnd.rc);
        RepaintBorder(&dwnd, NULL);
    }
}
#endif
/* ----- adjust a rectangle to include the shadow ----- */
#ifdef INCLUDE_SHADOWS
static RECT adjShadow(WINDOW wnd)
{
    RECT rc;
    rc = wnd->rc;
    if (TestAttribute(wnd, SHADOW))    {
        if (RectRight(rc) < SCREENWIDTH-1)
            RectRight(rc)++;
        if (RectBottom(rc) < SCREENHEIGHT-1)
            RectBottom(rc)++;
    }
    return rc;
}
#endif
/* --- repaint a rectangular subsection of a window --- */
#ifdef INCLUDE_MULTIDOCS
static void near PaintOverLap(WINDOW wnd, RECT rc)
{
    int isBorder, isTitle, isData;
    isBorder = isTitle = FALSE;
    isData = TRUE;
    if (TestAttribute(wnd, HASBORDER))    {
        isBorder =  RectLeft(rc) == 0 &&
                    RectTop(rc) < WindowHeight(wnd);
        isBorder |= RectLeft(rc) < WindowWidth(wnd) &&
                    RectRight(rc) >= WindowWidth(wnd)-1 &&
                    RectTop(rc) < WindowHeight(wnd);
        isBorder |= RectTop(rc) == 0 &&
                    RectLeft(rc) < WindowWidth(wnd);
        isBorder |= RectTop(rc) < WindowHeight(wnd) &&
                    RectBottom(rc) >= WindowHeight(wnd)-1 &&
                    RectLeft(rc) < WindowWidth(wnd);
    }
    else if (TestAttribute(wnd, HASTITLEBAR))
        isTitle = RectTop(rc) == 0 &&
                  RectLeft(rc) > 0 &&
                  RectLeft(rc)<WindowWidth(wnd)-BorderAdj(wnd);

    if (RectLeft(rc) >= WindowWidth(wnd)-BorderAdj(wnd))
        isData = FALSE;
    if (RectTop(rc) >= WindowHeight(wnd)-BottomBorderAdj(wnd))
        isData = FALSE;
    if (TestAttribute(wnd, HASBORDER))    {
        if (RectRight(rc) == 0)
            isData = FALSE;
        if (RectBottom(rc) == 0)
            isData = FALSE;
    }
#ifdef INCLUDE_SHADOWS
    if (TestAttribute(wnd, SHADOW))
        isBorder |= RectRight(rc) == WindowWidth(wnd) ||
                    RectBottom(rc) == WindowHeight(wnd);
#endif
    if (isData)
        SendMessage(wnd, PAINT, (PARAM) &rc, 0);
    if (isBorder)
        SendMessage(wnd, BORDER, (PARAM) &rc, 0);
    else if (isTitle)
        DisplayTitle(wnd, &rc);
}
/* ------ paint the part of a window that is overlapped
            by another window that is being hidden ------- */
static void PaintOver(WINDOW wnd)
{
        RECT wrc, rc;
#ifdef INCLUDE_SHADOWS
        wrc = adjShadow(HiddenWindow);
        rc = adjShadow(wnd);
#else
        wrc = HiddenWindow->rc;
        rc = wnd->rc;
#endif
        rc = subRectangle(rc, wrc);
        if (ValidRect(rc))
            PaintOverLap(wnd, RelativeWindowRect(wnd, rc));
}
/* --- paint the overlapped parts of all children --- */
static void PaintOverChildren(WINDOW pwnd)
{
    WINDOW cwnd = GetFirstFocusChild(pwnd);
    while (cwnd != NULL)    {
        if (cwnd != HiddenWindow)    {
            PaintOver(cwnd);
            PaintOverChildren(cwnd);
        }
        cwnd = GetNextFocusChild(pwnd, cwnd);
    }
}
/* -- recursive overlapping paint of parents -- */
static void PaintOverParents(WINDOW wnd)
{
    WINDOW pwnd = GetParent(wnd);
    if (pwnd != NULL)    {
        PaintOverParents(pwnd);
        PaintOver(pwnd);
        PaintOverChildren(pwnd);
    }
}
/* - paint the parts of all windows that a window is over - */
static void near PaintOverLappers(WINDOW wnd)
{
    HiddenWindow = wnd;
    PaintOverParents(wnd);
}
/* --- paint those parts of a window that are overlapped --- */
static void near PaintUnder(WINDOW wnd)
{
    WINDOW hwnd = Focus.FirstWindow;
    while (hwnd != NULL)    {
        /* ---- don't bother testing self ----- */
        if (hwnd != wnd)    {
            /* --- see if other window is descendent --- */
            WINDOW pwnd = GetParent(hwnd);
            while (pwnd != NULL)    {
                if (pwnd == wnd)
                    break;
                pwnd = GetParent(pwnd);
            }
            /* ----- don't test descendent overlaps ----- */
            if (pwnd == NULL)    {
                /* -- see if other window is ancestor --- */
                pwnd = GetParent(wnd);
                while (pwnd != NULL)    {
                    if (pwnd == hwnd)
                        break;
                    pwnd = GetParent(pwnd);
                }
                /* --- don't test ancestor overlaps --- */
                if (pwnd == NULL)    {
                    /* ---- other window must be ahead in
                        focus chain ----- */
                    WINDOW fwnd = NextWindow(wnd);
                    while (fwnd != NULL)    {
                        if (fwnd == hwnd)
                            break;
                        fwnd = NextWindow(fwnd);
                    }
                    if (fwnd != NULL)    {
                        HiddenWindow = hwnd;
                        PaintOver(wnd);
                    }
                }
            }
        }
        hwnd = NextWindow(hwnd);
    }
    /* --------- repaint all children of this window
        the same way ----------- */
    hwnd = Focus.FirstWindow;
    while (hwnd != NULL)    {
        if (GetParent(hwnd) == wnd)
            PaintUnder(hwnd);
        hwnd = NextWindow(hwnd);
    }
}
/* paint the parts of a window that are under other windows */
static void near PaintUnderLappers(WINDOW wnd)
{
    WINDOW pwnd = wnd;
    /* find oldest ancestor younger than application window */
    while (pwnd != NULL && GetClass(pwnd) != APPLICATION) {
        if (TestAttribute(wnd, SAVESELF))
            break;
        wnd = pwnd;
        pwnd = GetParent(pwnd);
    }
    PaintUnder(wnd);
}
#endif
#ifdef INCLUDE_SYSTEM_MENUS
/* --- save video area to be used by dummy window border --- */
static void SaveBorder(RECT rc)
{
    Bht = RectBottom(rc) - RectTop(rc) + 1;
    Bwd = RectRight(rc) - RectLeft(rc) + 1;
    if ((Bsave = realloc(Bsave, (Bht + Bwd) * 4)) != NULL)    {
        RECT lrc;
        int i;
        int *cp;

        lrc = rc;
        RectBottom(lrc) = RectTop(lrc);
        getvideo(lrc, Bsave);
        RectTop(lrc) = RectBottom(lrc) = RectBottom(rc);
        getvideo(lrc, Bsave + Bwd);
        cp = Bsave + Bwd * 2;
        for (i = 1; i < Bht-1; i++)    {
            *cp++ = GetVideoChar(RectLeft(rc),RectTop(rc)+i);
            *cp++ = GetVideoChar(RectRight(rc),RectTop(rc)+i);
        }
    }
}
/* ---- restore video area used by dummy window border ---- */
static void RestoreBorder(RECT rc)
{
    if (Bsave != NULL)    {
        RECT lrc;
        int i;
        int *cp;
        lrc = rc;
        RectBottom(lrc) = RectTop(lrc);
        storevideo(lrc, Bsave);
        RectTop(lrc) = RectBottom(lrc) = RectBottom(rc);
        storevideo(lrc, Bsave + Bwd);
        cp = Bsave + Bwd * 2;
        for (i = 1; i < Bht-1; i++)    {
            PutVideoChar(RectLeft(rc),RectTop(rc)+i, *cp++);
            PutVideoChar(RectRight(rc),RectTop(rc)+i, *cp++);
        }
        free(Bsave);
        Bsave = NULL;
    }
}
#endif
/* ----- test if screen coordinates are in a window ---- */
static int InsideWindow(WINDOW wnd, int x, int y)
{
    RECT rc;
    rc = WindowRect(wnd);
    if (!TestAttribute(wnd, NOCLIP))    {
        WINDOW pwnd = GetParent(wnd);
        while (pwnd != NULL)    {
            rc = subRectangle(rc, ClientRect(pwnd));
            pwnd = GetParent(pwnd);
        }
    }
    return InsideRect(x, y, rc);
}
/* ----- find window that screen coordinates are in --- */
WINDOW inWindow(int x, int y)
{
    WINDOW wnd = Focus.LastWindow;
    while (wnd != NULL)    {
        if (SendMessage(wnd, INSIDE_WINDOW, x, y))    {
            WINDOW wnd1 = GetLastChild(wnd);
            while (wnd1 != NULL)    {
                if (SendMessage(wnd1, INSIDE_WINDOW, x, y)) {
                    if (isVisible(wnd))  {
                        wnd = wnd1;
                        break;
                    }
                }
                wnd1 = GetPrevChild(wnd, wnd1);
            }
            break;
        }
        wnd = PrevWindow(wnd);
    }
    return wnd;
}
```

Copyright © 1991, Dr. Dobb's Journal
