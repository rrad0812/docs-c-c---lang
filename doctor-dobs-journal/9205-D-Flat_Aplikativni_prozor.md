
# C PROGRAMMING 9205

## D-Flat Aplikativni prozor - Al Stevens

Klasa prozora APPLICATION je osnova svake D-Flat aplikacije, koja mora da kreira prozor aplikacije pre nego što uradi bilo šta dalje. U prozoru aplikacije nalazi se traka menija, u kojoj se nalaze iskačući meniji, koji reaguju na korisničke komande kako bi program obavio svoj posao. Učili ste o menijima u ranijoj koloni „Programiranje u C“. Primer programa Memopad, koji sam takođe ranije opisao, pokazuje vam kako da kreirate prozor aplikacije i komandne funkcije koje njegov meni izvršava. Ovog meseca ću objasniti kako funkcioniše sam prozor aplikacije.

### Poruke klase prozora

[LISTING_ONE](#listing_one) je applicat.c. kod koji implementira klasu prozora APPLICATION. Njegova primarna funkcija je ApplicationProc, modul za obradu prozora za klasu. Tipična D-Flat npplication će uključivati sopstveni modul za obradu prozora aplikacije, koji presreće poruke, obrađuje one koje želi, a zatim poziva ovaj modul preko funkcije DefaultVndProc. ApplicationProc koristi prekidač za tumačenje poruka. Kao i kod drugih D-Flat modula za obradu prozora. ApplicationProc poziva funkcije za obradu većine poruka. Nazivi funkcija odražavaju naziv poruke.

Poruka CREATE_VINDOV. Poruka CREATE_VINDOV počinje izmenom dijaloga koji omogućava korisniku da promeni karakteristike ekrana. U zavisnosti od video sistema, korisnik je imao različite opcije. VGA može da prikaže 25, 43 ili 50 linija, i tako je postavljen dijalog. Ako korisnik ima VGA, nema promena. EGA sistem može da prikaže 25 i 43 reda, tako da program modifikuje okvir za dijalog tako da uključuje samo te opcije. CGA sistem može da prikaže samo 25 linija, tako da program menja okvir za dijalog tako da se ne pojavljuju opcije za izbor linija. Zatim, poruka CREATE_VINDOV proverava podešavanja varijabli konfiguracione datoteke aplikacije koje kontrolišu kako se prozor aplikacije prikazuje. Ova podešavanja kontrolišu da li prozor ima ivicu, da li je njegov prostor podataka jasan ili teksturiran, da li postoje naslovne i statusne trake, koliko linija ima na ekranu i da li će ekran biti u boji. jednobojni. ili obrnuto monohromatski. Program postavlja različite kontrole polja za potvrdu i radio-dugme u okviru za dijalog da odražavaju trenutna podešavanja. Zatim program kreira prozore menija i statusne trake kao podređene prozore prozora roditeljske aplikacije. Zatim, program poziva funkciju za učitavanje baze podataka pomoći aplikacije. Konačno. šalje poruku SHOV_MOUSE da bi se prikazao kursor miša.

Poruka HIDE_VINDOV. Poruka HIDE_VINDOV obično pokušava da promeni fokus ako prozor koji se skriva ima fokus. Ako je taj prozor prozor aplikacije, neće postojati prozor koji može da dobije fokus, jer je D-Flat prozor aplikacije predak svih njih. Stoga, prozor aplikacije presreće poruku HIDE_VINDOV za sebe i postavlja promenljivu inFocus na NULL tako da program neće pokušati da da fokus drugom prozoru.

Poruka ADDSTATIIS. D Flat aplikacije šalju poruku ADDSTATUS u prozor aplikacije da bi napisali ili obrisali oblast teksta statusne trake. Poruka ADDSTATUS šalje SETTENT ili CLEARTEKST poruke na ručku prozora statusne trake.

Poruka SETFOCUS. Klasa prozora APPLICATION ima svoju verziju SETFOCUS poruke. Radi većinu onoga što radi klasa prozora NORMAL, ali ne radi prefarbavanje prozora koji se preklapaju i koji se preklapaju koji su potrebni prozorima dokumenata.

Poruka SIZE. Poruka SIZE prozoru aplikacije mora da uradi dve stvari osim da promeni veličinu samog prozora aplikacije. Prozori menija i statusne trake su potomci prozora aplikacije, a njihove veličine zavise od njegove veličine. Stoga, kada korisnik promeni veličinu prozora aplikacije, program mora automatski da prilagodi veličinu menija i statusnih traka. To radi tako što poziva funkcije koje kreiraju ta dva prozora. Funkcije zatvaraju prozore ako već postoje, a zatim ih kreiraju sa veličinama koje odražavaju veličinu prozora aplikacije.

Poruka MINIMIZE. D-Flat vam ne dozvoljava da minimizirate prozor aplikacije. Stoga, presreće poruku MINIMIZE i jednostavno se vraća bez preduzimanja bilo čega.

Poruka KEIBOARD. Postoji nekoliko vrednosti pritiska na taster koje prozor aplikacije presreće i obrađuje. Pritisak na taster AIt+F4 je taster za ubrzavanje za Close conkti~nd u sistemskom meniju prozora aplikacije. Stoga, program šalje prozoru aplikacije poruku CLOSE_VINDOV kada prozor dobije poruku KEI BOARD sa vrednošću pritiska tastera Alt+F4. Korisnik pritiska Alt+F6 da bi prešao od prozora do prozora. Program poziva funkcije SetNektFocus i SkipSistemVindov kada prozor aplikacije dobije Alt+F6 u poruci KEIBOARD. Alt-Hiphen poziva funkciju BuildSistemMenu da iskače sistemski meni za prozor aplikacije. Prozor aplikacije prosleđuje sve ostale pritiske na tastere u prozor menija.

Događaji pritiska na taster šalju poruke TASTATURE u prozor koji ima fokus. Ako taj prozor ne obradi pritisak na taster, on šalje poruku svom roditelju. Roditeljski prozor radi istu stvar - ili obrađuje pritisak na taster ili prosleđuje poruku svom roditelju. Na kraju, neobrađena poruka TASTATURA će doći do prozora aplikacije jer se nalazi na vrhu porodičnog stabla. Ako prozor aplikacije ne obradi poruku, šalje je na traku menija, koja testira da li je pritisak na taster komandni-akceleratorski taster ili prečica menija, kombinacija tastera Alt. Ova procedura garantuje da svaki prozor dobija poruke TASTATURE koje su mu potrebne.

The SHIFT_CHANGED message. D-Flat korisnički interfejs omogućava korisniku da prelazi između prozora dokumenta i trake menija pritiskom na taster F10 ili pritiskom i otpuštanjem tastera Alt bez pritiskanja drugog tastera. Modul za obradu prozora za traku menija presreće taster F10 i upravlja prebacivanjem između prozora.

Poruka SHIFT_CHANGED se šalje kada rutine prikupljanja događaja u kodu za obradu poruka DFlat-a otkriju da je korisnik pritisnuo ili otpustio taster Shift, Alt ili Ctrl. Kao i sa neželjenim porukama KEIBOARD, svi prozori prosleđuju SHIFT_CHANGED poruku svom roditeljskom prozoru. Aplikacioni program na kraju presreće ovu poruku kako bi pratio promene u tasteru Alt. Program koristi promenljivu AllDou'ii da bi označio da je taster Alt pritisnut. Kada se pojavi druga poruka SHIFT_CHANGED sa otpuštenim tasterom Alt i kada nije bilo intervencionisanog pritiska na taster dok je taster Alt bio dole. program oseti da je taster Alt pritisnut i otpušten sam. Program zatim šalje poruku TASTATURA sa vrednošću tastera F10 na traku menija.

Poruka PAINT. Kada prozor aplikacije dobije poruku PAINT, on jednostavno briše svoj prostor podataka pozivanjem funkcije ClearVindov. Znak za čišćenje će biti ili razmak ili vrednost definisana kao APPLCHAR, u zavisnosti od toga da li je korisnik izabrao jasan ili teksturiran prozor aplikacije.

KOMANDNA poruka. Prozor aplikacije obrađuje nekoliko KOMANDNIH poruka. KOMANDNA poruka može da potiče iz aplikacijskog programa ili može biti rezultat odabira korisnika u meniju sa kojim je povezana identifikacija komande. Sve komande koje prozor aplikacije obrađuje dolaze iz izbora menija.

U stvari, sve komande menija prvo dolaze u prozor aplikacije. Iskačući meni šalje pridruženu KOMANDNU poruku svom roditelju. traku menija, kada korisnik izabere izbor menija. Traka menija šalje poruku svom roditelju, prozoru aplikacije. Ako prozor aplikacije ne obradi KOMANDNU poruku, on šalje poruku na bilo koji prozor dokumenta koji ima fokus. U suprotnom, prozor aplikacije obrađuje KOMANDNE poruke o kojima se dalje govori. KOMANDNA poruka se identifikuje kodom u svom prvom parametru.

Postoji nekoliko komandi pomoći povezanih sa odabirima u meniju Pomoć. Svaki od njih poziva funkciju DisplaiHelp da prikaže povezani ekran pomoći.

Komanda ID_DOS je povezana sa komandom DOS Shell u meniju File. bus komanda poziva Shell DOS funkciju koja poziva DOS shell. Prvo, funkcija sakriva prozor aplikacije D Flat i menja konfiguracije kursora i visine ekrana sa onima koje su bile kada je D Flat aplikacijski program pokrenut. Zatim funkcija sakriva miša, prikazuje poruku koja govori korisniku kako da se vrati u D Flat aplikaciju tako što će otkucati DOS EKSIT komandu, a zatim stvara novu kopiju DOS komandnog procesora. When the command processor retums. program prebacuje ekran i kursor nazad u njihov D-Flat kontekst i prikazuje prozor aplikacije i kursor miša.

I ID_EMT i ID_SISCLOSE comande šalju CLOSE_VINDOV poruku aplikaciji. Komanda ID EMT je povezana sa izborom Izađi u meniju Datoteka, a komanda ID_SISCLOSE je povezana sa izborom Zatvori u sistemskom meniju prozora aplikacije.

Komanda ID_DISPLAI je rezultat toga što je korisnik izabrao izbor Displai u meniju Opcije. On izvršava okvir za dijalog Displai, a zatim poziva različite funkcije koje utiču na to kako se prozor aplikacije prikazuje.

Komanda ID_SAVEOPTIONS čuva opcije koje je korisnik izabrao u konfiguracionoj datoteci.

Komanda ID_VINDOV se šalje kada korisnik izabere prozor dokumenta iz menija Prozor. Ovaj meni se gradi dinamički kada ga korisnik izabere u meniju baa. Prikazuje naslove prvih devet prozora dokumenata u prostoru podataka prozora aplikacije. Meni Prozor pruža jedan od načina na koji korisnik može da izabere novi prozor dokumenta koji će imati fokus. Poruka pronalazi izabrani prozor dokumenta među onima na listi podređenih prozora prozora aplikacije i šalje SETFOCUS poruku prozoru dokumenta. Ako je prozor dokumenta minimiziran, program mu takođe šalje naredbu RESTORE.

Komanda ID_CLOSEALL je povezana sa izborom Zatvori sve u meniju Prozor. Svakom prozoru dokumenta na listi dece prozora aplikacije šalje poruku CLOSE_VINDOV.

Komanda ID_MOREV1NDOVS je povezana sa izborom Još Vindovsa u meniju Prozor. Meni uključuje taj izbor kada postoji više od devet prozora dokumenta. Komanda prikazuje okvir za dijalog sa kontrolom okvira sa listom koja ima sve naslove prozora dokumenta. Okvir za dijalog služi kao proširenje menija Vindov u svim prozorima dokumenta umesto samo u prvih devet. Korisnik može izabrati prozor sa liste.

Poruka CLOSE-VINDOV. Ova poruka zatvara sve prozore dokumenata prozora aplikacije i postavlja STOP poruku u D-Flat sistem poruka. Nakon što se prozor aplikacije zatvori, program resetuje visinu ekrana na ono što je bila kada je program pokrenut i poziva funkciju za istovar baze podataka pomoći.

#### LISTING_ONE

```c
/* ------------- applicat.c ------------- */

#include "dflat.h"

static int ScreenHeight;
static BOOL AltDown = FALSE;

extern DBOX Display;
extern DBOX Windows;

#ifdef INCLUDE_LOGGING
extern DBOX Log;
#endif

#ifdef INCLUDE_SHELLDOS
static void ShellDOS(WINDOW);
#endif
static void CreateMenu(WINDOW);
static void CreateStatusBar(WINDOW);
static void SelectColors(WINDOW);
static void SetScreenHeight(int);
static void SelectLines(WINDOW);

#ifdef INCLUDE_WINDOWOPTIONS
static void SelectTexture(void);
static void SelectBorder(WINDOW);
static void SelectTitle(WINDOW);
static void SelectStatusBar(WINDOW);
#endif

#ifdef INCLUDE_MULTI_WINDOWS
static void CloseAll(WINDOW, int);
static void MoreWindows(WINDOW);
static void ChooseWindow(WINDOW, int);
static int WindowSel;
static WINDOW oldFocus;
static char *Menus[9] = {
    "~1.                      ",
    "~2.                      ",
    "~3.                      ",
    "~4.                      ",
    "~5.                      ",
    "~6.                      ",
    "~7.                      ",
    "~8.                      ",
    "~9.                      "
};
#endif

/* --------------- CREATE_WINDOW Message -------------- */
static int CreateWindowMsg(WINDOW wnd)
{
    int rtn;
    static BOOL DisplayModified = FALSE;
    ScreenHeight = SCREENHEIGHT;
    if (!isVGA() && !DisplayModified)    {
        /* ---- modify Display Dialog Box for EGA, CGA ---- */
        CTLWINDOW *ct, *ct1;
        int i;
        ct = FindCommand(&Display, ID_OK, BUTTON);
        if (isEGA())
            ct1 = FindCommand(&Display,ID_50LINES,RADIOBUTTON);
        else    {
            CTLWINDOW *ct2;
            ct2 = FindCommand(&Display,ID_COLOR,RADIOBUTTON)-1;
            ct2->dwnd.w++;
            for (i = 0; i < 7; i++)
                (ct2+i)->dwnd.x += 8;
            ct1 = FindCommand(&Display,ID_25LINES,RADIOBUTTON)-1;
        }
        for (i = 0; i < 4; i++)
            *ct1++ = *ct++;
        DisplayModified = TRUE;
    }
#ifdef INCLUDE_WINDOWOPTIONS
    if (cfg.Border)
        SetCheckBox(&Display, ID_BORDER);
    if (cfg.Title)
        SetCheckBox(&Display, ID_TITLE);
    if (cfg.StatusBar)
        SetCheckBox(&Display, ID_STATUSBAR);
    if (cfg.Texture)
        SetCheckBox(&Display, ID_TEXTURE);
#endif
    if (cfg.mono == 1)
        PushRadioButton(&Display, ID_MONO);
    else if (cfg.mono == 2)
        PushRadioButton(&Display, ID_REVERSE);
    else
        PushRadioButton(&Display, ID_COLOR);
    if (cfg.ScreenLines == 25)
        PushRadioButton(&Display, ID_25LINES);
    else if (cfg.ScreenLines == 43)
        PushRadioButton(&Display, ID_43LINES);
    else if (cfg.ScreenLines == 50)
        PushRadioButton(&Display, ID_50LINES);
    if (SCREENHEIGHT != cfg.ScreenLines)    {
        SetScreenHeight(cfg.ScreenLines);
        if (WindowHeight(wnd) == ScreenHeight ||
                SCREENHEIGHT-1 < GetBottom(wnd))    {
            WindowHeight(wnd) = SCREENHEIGHT-1;
            GetBottom(wnd) = GetTop(wnd)+WindowHeight(wnd)-1;
            wnd->RestoredRC = WindowRect(wnd);
        }
    }
    SelectColors(wnd);
#ifdef INCLUDE_WINDOWOPTIONS
    SelectBorder(wnd);
    SelectTitle(wnd);
    SelectStatusBar(wnd);
#endif
    rtn = BaseWndProc(APPLICATION, wnd, CREATE_WINDOW, 0, 0);
    if (wnd->extension != NULL)
        CreateMenu(wnd);
    CreateStatusBar(wnd);
    LoadHelpFile();
    SendMessage(NULL, SHOW_MOUSE, 0, 0);
    return rtn;
}

/* --------- ADDSTATUS Message ---------- */
static void AddStatusMsg(WINDOW wnd, PARAM p1)
{
    if (wnd->StatusBar != NULL)    {
        if (p1 && *(char *)p1)
            SendMessage(wnd->StatusBar, SETTEXT, p1, 0);
        else
            SendMessage(wnd->StatusBar, CLEARTEXT, 0, 0);
        SendMessage(wnd->StatusBar, PAINT, 0, 0);
    }
}

/* -------- SETFOCUS Message -------- */
static void SetFocusMsg(WINDOW wnd, BOOL p1)
{
    if (p1)
        SendMessage(inFocus, SETFOCUS, FALSE, 0);
    /* --- remove window from list --- */
    RemoveFocusWindow(wnd);
    /* --- move window to end/beginning of list --- */
    p1 ? AppendFocusWindow(wnd) : PrependFocusWindow(wnd);
    inFocus = p1 ? wnd : NULL;
    SendMessage(wnd, BORDER, 0, 0);
}

/* ------- SIZE Message -------- */
static void SizeMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    BOOL WasVisible;
    WasVisible = isVisible(wnd);
    if (WasVisible)
        SendMessage(wnd, HIDE_WINDOW, 0, 0);
    if (p1-GetLeft(wnd) < 30)
        p1 = GetLeft(wnd) + 30;
    BaseWndProc(APPLICATION, wnd, SIZE, p1, p2);
    CreateMenu(wnd);
    CreateStatusBar(wnd);
    if (WasVisible)
        SendMessage(wnd, SHOW_WINDOW, 0, 0);
}

/* ----------- KEYBOARD Message ------------ */
static int KeyboardMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    AltDown = FALSE;
    if (WindowMoving || WindowSizing || (int) p1 == F1)
        return BaseWndProc(APPLICATION, wnd, KEYBOARD, p1, p2);
    switch ((int) p1)    {
        case ALT_F4:
            PostMessage(wnd, CLOSE_WINDOW, 0, 0);
            return TRUE;
#ifdef INCLUDE_MULTI_WINDOWS
        case ALT_F6:
            SetNextFocus(inFocus);
            SkipSystemWindows(FALSE);
            return TRUE;
#endif
        case ALT_HYPHEN:
            BuildSystemMenu(wnd);
            return TRUE;
        default:
            break;
    }
    PostMessage(wnd->MenuBarWnd, KEYBOARD, p1, p2);
    return TRUE;
}

/* --------- SHIFT_CHANGED Message -------- */
static void ShiftChangedMsg(WINDOW wnd, PARAM p1)
{
    if ((int)p1 & ALTKEY)
        AltDown = TRUE;
    else if (AltDown)    {
        AltDown = FALSE;
        if (wnd->MenuBarWnd != inFocus)
            SendMessage(NULL, HIDE_CURSOR, 0, 0);
        SendMessage(wnd->MenuBarWnd, KEYBOARD, F10, 0);
    }
}

/* -------- COMMAND Message ------- */
static void CommandMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    switch ((int)p1)    {
        case ID_HELP:
            DisplayHelp(wnd, DFlatApplication);
            break;
        case ID_HELPHELP:
            DisplayHelp(wnd, "HelpHelp");
            break;
        case ID_EXTHELP:
            DisplayHelp(wnd, "ExtHelp");
            break;
        case ID_KEYSHELP:
            DisplayHelp(wnd, "KeysHelp");
            break;
        case ID_HELPINDEX:
            DisplayHelp(wnd, "HelpIndex");
            break;
#ifdef TESTING_DFLAT
        case ID_LOADHELP:
            LoadHelpFile();
            break;
#endif
#ifdef INCLUDE_LOGGING
        case ID_LOG:
            MessageLog(wnd);
            break;
#endif
#ifdef INCLUDE_SHELLDOS
        case ID_DOS:
            ShellDOS(wnd);
            break;
#endif
        case ID_EXIT:
        case ID_SYSCLOSE:
            PostMessage(wnd, CLOSE_WINDOW, 0, 0);
            break;
        case ID_DISPLAY:
            if (DialogBox(wnd, &Display, TRUE, NULL))    {
                SendMessage(wnd, HIDE_WINDOW, 0, 0);
                SelectColors(wnd);
                SelectLines(wnd);
#ifdef INCLUDE_WINDOWOPTIONS
                SelectBorder(wnd);
                SelectTitle(wnd);
                SelectStatusBar(wnd);
                SelectTexture();
#endif
                CreateMenu(wnd);
                CreateStatusBar(wnd);
                SendMessage(wnd, SHOW_WINDOW, 0, 0);
            }
            break;
        case ID_SAVEOPTIONS:
            SaveConfig();
            break;
#ifdef INCLUDE_MULTI_WINDOWS
        case ID_WINDOW:
            ChooseWindow(wnd, (int)p2-2);
            break;
        case ID_CLOSEALL:
            CloseAll(wnd, FALSE);
            break;
        case ID_MOREWINDOWS:
            MoreWindows(wnd);
            break;
#endif
#ifdef INCLUDE_RESTORE
        case ID_SYSRESTORE:
#endif
        case ID_SYSMOVE:
        case ID_SYSSIZE:
#ifdef INCLUDE_MINIMIZE
        case ID_SYSMINIMIZE:
#endif
#ifdef INCLUDE_MAXIMIZE
        case ID_SYSMAXIMIZE:
#endif
            BaseWndProc(APPLICATION, wnd, COMMAND, p1, p2);
            break;
        default:
            if (inFocus != wnd->MenuBarWnd && inFocus != wnd)
                PostMessage(inFocus, COMMAND, p1, p2);
            break;
    }
}

/* --------- CLOSE_WINDOW Message -------- */
static int CloseWindowMsg(WINDOW wnd)
{
    int rtn;
#ifdef INCLUDE_MULTI_WINDOWS
    CloseAll(wnd, TRUE);
#endif
    PostMessage(NULL, STOP, 0, 0);
    rtn = BaseWndProc(APPLICATION, wnd, CLOSE_WINDOW, 0, 0);
    if (ScreenHeight != SCREENHEIGHT)
        SetScreenHeight(ScreenHeight);
    UnLoadHelpFile();
    return rtn;
}

/* --- APPLICATION Window Class window processing module --- */
int ApplicationProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:
            return CreateWindowMsg(wnd);
        case HIDE_WINDOW:
            if (wnd == inFocus)
                inFocus = NULL;
            break;
        case ADDSTATUS:
            AddStatusMsg(wnd, p1);
            return TRUE;
        case SETFOCUS:
            if ((int)p1 == (inFocus != wnd))    {
                SetFocusMsg(wnd, (BOOL) p1);
                return TRUE;
            }
            break;
        case SIZE:
            SizeMsg(wnd, p1, p2);
            return TRUE;
#ifdef INCLUDE_MINIMIZE
        case MINIMIZE:
            return TRUE;
#endif
        case KEYBOARD:
            return KeyboardMsg(wnd, p1, p2);
        case SHIFT_CHANGED:
            ShiftChangedMsg(wnd, p1);
            return TRUE;
        case PAINT:
            if (isVisible(wnd))    {
#ifdef INCLUDE_WINDOWOPTIONS
                int cl = cfg.Texture ? APPLCHAR : ' ';
#else
                int cl = APPLCHAR;
#endif
                ClearWindow(wnd, (RECT *)p1, cl);
            }
            return TRUE;
        case COMMAND:
            CommandMsg(wnd, p1, p2);
            return TRUE;
        case CLOSE_WINDOW:
            return CloseWindowMsg(wnd);
        default:
            break;
    }
    return BaseWndProc(APPLICATION, wnd, msg, p1, p2);
}

#ifdef INCLUDE_SHELLDOS
static void SwitchCursor(void)
{
    SendMessage(NULL, SAVE_CURSOR, 0, 0);
    SwapCursorStack();
    SendMessage(NULL, RESTORE_CURSOR, 0, 0);
}

/* ------- Shell out to DOS ---------- */
static void ShellDOS(WINDOW wnd)
{
    SendMessage(wnd, HIDE_WINDOW, 0, 0);
    SwitchCursor();
    if (ScreenHeight != SCREENHEIGHT)
        SetScreenHeight(ScreenHeight);
    SendMessage(NULL, HIDE_MOUSE, 0, 0);
    printf("To return to %s, execute the DOS exit command.",
                    DFlatApplication);
    fflush(stdout);
    spawnl(P_WAIT, getenv("COMSPEC"), NULL);
    if (SCREENHEIGHT != cfg.ScreenLines)
        SetScreenHeight(cfg.ScreenLines);
    SwitchCursor();
    SendMessage(wnd, SHOW_WINDOW, 0, 0);
    SendMessage(NULL, SHOW_MOUSE, 0, 0);
}
#endif

/* -------- Create the menu bar -------- */
static void CreateMenu(WINDOW wnd)
{
    AddAttribute(wnd, HASMENUBAR);
    if (wnd->MenuBarWnd != NULL)
        SendMessage(wnd->MenuBarWnd, CLOSE_WINDOW, 0, 0);
    wnd->MenuBarWnd = CreateWindow(MENUBAR,
                        NULL,
                        GetClientLeft(wnd),
                        GetClientTop(wnd)-1,
                        1,
                        ClientWidth(wnd),
                        NULL,
                        wnd,
                        NULL,
                        0);
    SendMessage(wnd->MenuBarWnd,BUILDMENU,
        (PARAM)wnd->extension,0);
    AddAttribute(wnd->MenuBarWnd, VISIBLE);
}

/* ----------- Create the status bar ------------- */
static void CreateStatusBar(WINDOW wnd)
{
    if (wnd->StatusBar != NULL)    {
        SendMessage(wnd->StatusBar, CLOSE_WINDOW, 0, 0);
        wnd->StatusBar = NULL;
    }
    if (TestAttribute(wnd, HASSTATUSBAR))    {
        wnd->StatusBar = CreateWindow(STATUSBAR,
                            NULL,
                            GetClientLeft(wnd),
                            GetBottom(wnd),
                            1,
                            ClientWidth(wnd),
                            NULL,
                            wnd,
                            NULL,
                            0);
        AddAttribute(wnd->StatusBar, VISIBLE);
    }
}

#ifdef INCLUDE_MULTI_WINDOWS
/* -------- return the name of a document window ------- */
static char *WindowName(WINDOW wnd)
{
    if (GetTitle(wnd) == NULL)    {
        if (GetClass(wnd) == DIALOG)
            return ((DBOX *)(wnd->extension))->HelpName;
        else
            return "Untitled";
    }
    else
        return GetTitle(wnd);
}

/* ----------- Prepare the Window menu ------------ */
void PrepWindowMenu(void *w, struct Menu *mnu)
{
    WINDOW wnd = w;
    struct PopDown *p0 = mnu->Selections;
    struct PopDown *pd = mnu->Selections + 2;
    struct PopDown *ca = mnu->Selections + 13;
    int MenuNo = 0;
    WINDOW cwnd;
    mnu->Selection = 0;
    oldFocus = NULL;
    if (GetClass(wnd) != APPLICATION)    {
        int i;
        oldFocus = wnd;
        /* ----- point to the APPLICATION window ----- */
        while (GetClass(wnd) != APPLICATION)
            if ((wnd = GetParent(wnd)) == NULL)
                return;
        /* ----- get the first 9 document windows ----- */
        for (i = 0; i < wnd->ChildCt && MenuNo < 9; i++)    {
            cwnd = *(wnd->Children + i);
            if (GetClass(cwnd) != MENUBAR &&
                    GetClass(cwnd) != STATUSBAR) {
                /* --- add the document window to the menu --- */
                strncpy(Menus[MenuNo]+4, WindowName(cwnd), 20);
                pd->SelectionTitle = Menus[MenuNo];
                if (cwnd == oldFocus)    {
                    /* -- mark the current document -- */
                    pd->Attrib |= CHECKED;
                    mnu->Selection = MenuNo+2;
                }
                else
                    pd->Attrib &= ~CHECKED;
                pd++;
                MenuNo++;
            }
        }
    }
    if (MenuNo)
        p0->SelectionTitle = "~Close all";
    else
        p0->SelectionTitle = NULL;
    if (MenuNo >= 9)    {
        *pd++ = *ca;
        if (mnu->Selection == 0)
            mnu->Selection = 11;
    }
    pd->SelectionTitle = NULL;
}

/* window processing module for the More Windows dialog box */
static int WindowPrep(WINDOW wnd,MESSAGE msg,PARAM p1,PARAM p2)
{
    switch (msg)    {
        case INITIATE_DIALOG:    {
            WINDOW wnd1;
            WINDOW cwnd = ControlWindow(&Windows,ID_WINDOWLIST);
            WINDOW pwnd = GetParent(wnd);
            int sel = 0, i;
            if (cwnd == NULL)
                return FALSE;
            for (i = 0; i < pwnd->ChildCt; i++)    {
                wnd1 = *(pwnd->Children + i);
                if (wnd1 != wnd && GetClass(wnd1) != MENUBAR &&
                        GetClass(wnd1) != STATUSBAR)    {
                    if (wnd1 == oldFocus)
                        WindowSel = sel;
                    SendMessage(cwnd, ADDTEXT,
                        (PARAM) WindowName(wnd1), 0);
                    sel++;
                }
            }
            SendMessage(cwnd, LB_SETSELECTION, WindowSel, 0);
            AddAttribute(cwnd, VSCROLLBAR);
            PostMessage(cwnd, SHOW_WINDOW, 0, 0);
            break;
        }
        case COMMAND:
            switch ((int) p1)    {
                case ID_OK:
                    if ((int)p2 == 0)
                        WindowSel = SendMessage(
                                    ControlWindow(&Windows,
                                    ID_WINDOWLIST),
                                    LB_CURRENTSELECTION, 0, 0);
                    break;
                case ID_WINDOWLIST:
                    if ((int) p2 == LB_CHOOSE)
                        SendMessage(wnd, COMMAND, ID_OK, 0);
                    break;
                default:
                    break;
            }
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}

/* ---- the More Windows command on the Window menu ---- */
static void MoreWindows(WINDOW wnd)
{
    if (DialogBox(wnd, &Windows, TRUE, WindowPrep))
        ChooseWindow(wnd, WindowSel);
}

/* ----- user chose a window from the Window menu
        or the More Window dialog box ----- */
static void ChooseWindow(WINDOW wnd, int WindowNo)
{
    int i;
    WINDOW cwnd;
    for (i = 0; i < wnd->ChildCt; i++)    {
        cwnd = *(wnd->Children + i);
        if (GetClass(cwnd) != MENUBAR &&
                GetClass(cwnd) != STATUSBAR)
            if (WindowNo-- == 0)
                break;
    }
    if (wnd->ChildCt)    {
        SendMessage(cwnd, SETFOCUS, TRUE, 0);
        if (cwnd->condition == ISMINIMIZED)
            SendMessage(cwnd, RESTORE, 0, 0);
    }
}

/* ----- Close all document windows ----- */
static void CloseAll(WINDOW wnd, int closing)
{
    WINDOW wnd1;
    int i;
    SendMessage(wnd, SETFOCUS, TRUE, 0);
    for (i = wnd->ChildCt; i > 0; --i)    {
        wnd1 = *(wnd->Children + i - 1);
        if (GetClass(wnd1) != MENUBAR &&
                GetClass(wnd1) != STATUSBAR)    {
            ClearVisible(wnd1);
            SendMessage(wnd1, CLOSE_WINDOW, 0, 0);
        }
    }
    if (!closing)
        SendMessage(wnd, PAINT, 0, 0);
}

#endif    /* #ifdef INCLUDE_MULTI_WINDOWS */

static void DoWindowColors(WINDOW wnd)
{
    WINDOW cwnd;
    int i;
    InitWindowColors(wnd);
    for (i = 0; i < wnd->ChildCt; i++)    {
        cwnd = *(wnd->Children + i);
        InitWindowColors(cwnd);
        if (GetClass(cwnd) == TEXT && GetText(cwnd) != NULL)
            SendMessage(cwnd, CLEARTEXT, 0, 0);
    }
}

/* ----- set up colors for the application window ------ */
static void SelectColors(WINDOW wnd)
{
    if (RadioButtonSetting(&Display, ID_MONO))
        cfg.mono = 1;
    else if (RadioButtonSetting(&Display, ID_REVERSE))
        cfg.mono = 2;
    else
        cfg.mono = 0;
    if ((ismono() || video_mode == 2) && cfg.mono == 0)
        cfg.mono = 1;

    if (cfg.mono == 1)
        memcpy(cfg.clr, bw, sizeof bw);
    else if (cfg.mono == 2)
        memcpy(cfg.clr, reverse, sizeof reverse);
    else
        memcpy(cfg.clr, color, sizeof color);
    DoWindowColors(wnd);
}

/* ---- select screen lines ---- */
static void SelectLines(WINDOW wnd)
{
    cfg.ScreenLines = 25;
    if (isEGA() || isVGA())    {
        if (RadioButtonSetting(&Display, ID_43LINES))
            cfg.ScreenLines = 43;
        else if (RadioButtonSetting(&Display, ID_50LINES))
            cfg.ScreenLines = 50;
    }
    if (SCREENHEIGHT != cfg.ScreenLines)    {
        int FullScreen = WindowHeight(wnd) == SCREENHEIGHT;
        SetScreenHeight(cfg.ScreenLines);
        if (FullScreen || SCREENHEIGHT-1 < GetBottom(wnd))
            SendMessage(wnd, SIZE, (PARAM) GetRight(wnd),
                SCREENHEIGHT-1);
    }
}

/* ---- set the screen height in the video hardware ---- */
static void SetScreenHeight(int height)
{
    if (isEGA() || isVGA())    {
        SendMessage(NULL, SAVE_CURSOR, 0, 0);
        switch (height)    {
            case 25:
                Set25();
                break;
            case 43:
                Set43();
                break;
            case 50:
                Set50();
                break;
            default:
                break;
        }
        SendMessage(NULL, RESTORE_CURSOR, 0, 0);
        SendMessage(NULL, RESET_MOUSE, 0, 0);
        SendMessage(NULL, SHOW_MOUSE, 0, 0);
    }
}

#ifdef INCLUDE_WINDOWOPTIONS

/* ----- select the screen texture ----- */
static void SelectTexture(void)
{
    cfg.Texture = CheckBoxSetting(&Display, ID_TEXTURE);
}

/* -- select whether the application screen has a border -- */
static void SelectBorder(WINDOW wnd)
{
    cfg.Border = CheckBoxSetting(&Display, ID_BORDER);
    if (cfg.Border)
        AddAttribute(wnd, HASBORDER);
    else
        ClearAttribute(wnd, HASBORDER);
}

/* select whether the application screen has a status bar */
static void SelectStatusBar(WINDOW wnd)
{
    cfg.StatusBar = CheckBoxSetting(&Display, ID_STATUSBAR);
    if (cfg.StatusBar)
        AddAttribute(wnd, HASSTATUSBAR);
    else
        ClearAttribute(wnd, HASSTATUSBAR);
}

/* select whether the application screen has a title bar */
static void SelectTitle(WINDOW wnd)
{
    cfg.Title = CheckBoxSetting(&Display, ID_TITLE);
    if (cfg.Title)
        AddAttribute(wnd, HASTITLEBAR);
    else
        ClearAttribute(wnd, HASTITLEBAR);
}

#endif
```

Copyright © 1992, Dr. Dobb's Journal
