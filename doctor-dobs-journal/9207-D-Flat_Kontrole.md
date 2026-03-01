
# C PROGRAMMING 9207

## D-Flat Kontrole - Al Stevens

### D-Flat++

Pretpostavljam da će se tako zvati. Dobro sam ušao u prvi sloj D-Flat++-a i stvaram neka pozitivna mišljenja o prikladnosti C++-a kao alternative paradigmi Windows-a, D-Flat-a i drugih zasnovanoj na porukama zasnovanoj na događajima. Prva stvar koju ćete videti kada uporedite slične procese u dve D-Flat biblioteke je da je C++ kod mnogo manje složen. Kao prvo, kada ugradite funkcije članova u klase, kompajler upravlja svim dereferenciranjem pokazivača. Ne vidite to u svom kodu, a rezultati su mnogo čistiji.

Videćete još jedno očigledno poboljšanje u DF++-ovom rukovanju stringovima. Svi prave string klasu, a i ja sam. Tamo gde D-Flat ima dugačke nizove specijalizovanih strcpys, strcats, *cp++, memsetova i tako dalje, DF++ jednostavno instancira string, spaja ga sa drugim stringom, izvlači podniz iz njega itd. Kod je lakši za čitanje i ima mnogo manje redova. Poboljšanja notacije su dramatična.

C++ utiče na dizajn programa. Skloni ste da koristite više klasa jer enkapsulacija štiti stavke podataka od drugih delova programa. Osećate se sigurnije u vezi sa integritetom varijabli podataka znajući da će vas enkapsulacija sprečiti da neograničeno pristupate skrivenim članovima iz udaljenih delova programa. Pravilno dizajniran C++ program će retko čuti izdajnički šamar po čelu programera koji je upravo otkrio neku davno zaboravljenu upotrebu nevine promenljive.

Ako još niste isprobali C++, možda nećete videti mnogo smisla u tome. Ako ima, pevam u horu. To je konsenzus. C programeri ne vide prednosti C++-a sve dok ne zarone, nakon čega postaju konvertiti. Međutim, ovo nije evangelizacija OOP-a. To ću ostaviti drugima. Ovo je podrška mogućnosti jezika C++ za pravljenje apstraktnih tipova podataka, sakrivanje informacija i poboljšanje notacije programa. Nazovite to kako hoćete, ali ja sam na trećini puta, i do sada je notacija jasnija, kod je manji, a u D-Flat++ nema spoljnih promenljivih osim onih koje su statički članovi klasa, i bilo bi nepristojno praviti bilo koju.

Grejdi Buč smatra da ako koristite C++ samo kao poboljšani C, onda vam nedostaje, a možda čak i zloupotrebljavate moć jezika. Dolazim kroz iskustvo da se složim sa tim mišljenjem.

### Lepše drugi put

Dugo sam verovao da treba dva puta izgraditi softverski sistem. Prvi je da vas nauči kako sistem treba da funkcioniše. Bacite ga i drugi put uradite posao kako treba. Retko dobijamo tu priliku jer kontre pasulja to nikada ne bi dozvolile. Verzija 2 se uvek zasniva na verziji 1, koja je već plaćena, tačna ili ne. Ali D-Flat++ nije C++ omotač omotan oko D-Flat C biblioteke. To je potpuno prepisivanje u C++, jer je jedan od ciljeva korišćenje prednosti jezika C++ za pravljenje API-ja korisničkog interfejsa. Dakle, znajući da ću ionako ponovo napisati sav kod, kada se pripremam da implementiram određenu funkciju, zastajem da razmislim kako mi je ta funkcija izazvala probleme u D-Flat-u i dizajniram probleme. Rezultat je druga šansa da se posao obavi kako treba. Za D-Flat nema šaltera za pasulj, tako da ja donosim te odluke. Programeri 1, bean brojači 0.

### D-Flat kutije za poruke i drugo

Povratak u stara vremena. Prošlog meseca sam opisao kako D-Flat implementira klasu prozora za dijalog. Okvir za dijalog se sastoji od samog prozora i niza kontrolnih prozora u koje korisnik unosi podatke ili komande. Prethodnih meseci ste naučili o klasama prozora okvira za uređivanje i okvira sa listom, od kojih obe mogu biti kontrolni prozori u okviru za dijalog. Postoji još nekoliko klasa kontrolnih prozora, koje su izvedene iz drugih klasa i koje implementiraju dugmad i okvire koje možete staviti u okvir za dijalog. Dijaloški okviri u aplikaciji za primer memopada koriste sve ove kontrolne prozore. Razgovaraću o svakom od njih opisujući izvorni fajl koji ga implementira. Svi oni imaju uobičajeni format izvorne datoteke klase. Modul za obradu prozora i prateće funkcije nalaze se u samostalnoj C-izvornoj datoteci.

### Kod

[LISTING_ONE](#listing_one) je "box.c", izvorna datoteka koja implementira klasu prozora okvira. Njegova svrha je da nacrta pravougaonik sa oznakom oko drugih kontrola u okviru dijaloga. Box je jednostavan. Svoj posao radi nenametljivo odbijajući da prihvati fokus i prosleđujući poruke miša svom nadređenom prozoru, dijaloškom okviru koji ga hostuje. Poruka ivice prikazuje tekstualnu oznaku okvira preko gornje ivice ivice počevši od prve pozicije iza gornjeg levog ugla.

[LISTING_TWO](#listing_two) je "button.c", izvorni fajl koji implementira degustatore. Dugme se prikazuje kao jedan red teksta sa bojom različitom od njegovog roditeljskog prozora i sa senkom napravljenom od bloka znakova polovine visine iz skupa grafičkih znakova. Kada korisnik pritisne dugme, bilo mišem ili tastaturom, program prikazuje dugme u pritisnutoj konfiguraciji, a zatim čeka da se taster ili dugme otpusti da bi oslikao dugme u njegovoj originalnoj konfiguraciji. Zatim šalje pridruženu KOMANDNU poruku za dugme roditeljskom prozoru.

[LISTING_THREE](#listing_three) je "checkbok.c", izvorni fajl koji implementira kontrolu polja za potvrdu. Polje za potvrdu beleži opcije podešavanja menija. Prikazuje se sa tekstualnim znakovima [Ks] kada je opcija uključena i sa [ ] kada je isključena. Korisnik prebacuje postavku klikom na polje za potvrdu mišem ili odabiranjem tastature i pritiskom na razmaknicu. Kursor na tastaturi prikazuje gde ide Ks kada je izabrano polje za potvrdu da bi korisniku pružio vizuelni trag. Funkcija CheckBokSetting vraća True ako je navedeno polje za potvrdu uključeno i False ako nije. Postavka za okvir za dijalog ostaje između upotrebe okvira za dijalog, tako da program može da pozove funkciju u bilo kom trenutku.

[LISTING_FOUR](#listing_four) je "combobok.c", izvorni fajl koji implementira kontrolu kombinovanog okvira. Kombinovani okvir je kombinacija okvira za uređivanje u jednom redu i okvira padajuće liste, pa tako i njegovo ime. Klasa kombinovanog okvira je izvedena iz klase edit-bok. Kada program kreira kontrolu, poruka CREATE_VINDOV kombinovanog okvira kreira pridruženi okvir sa listom, ali ga ne prikazuje. Poruka PAINT dodaje strelicu koja pokazuje nadole na kraju jednolinijskog okvira za uređivanje. Taj token je dugme za pomeranje na koje korisnik klikne da bi spustio okvir sa listom. Ako korisnik klikne na token ili pritisne taster sa strelicom nadole dok je kombinovani okvir u fokusu, program šalje SETFOCUS poruku u okvir sa liste, koja se zatim sama prikazuje. Funkcija ListProc je modul za obradu prozora za padajuću listu okvira sa kombinovanim izborom. Kada korisnik pomeri kursor izbora okvira sa listom, klasa okvira sa listom šalje sebi LB_SELECTION, koji ovaj modul presreće. On šalje tekst trenutnog izbora komponenti okvira za uređivanje kombinovanog okvira. Aplikacioni program poziva PutComboListTekt da bi dodao redove teksta u padajuću listu nakon što otvori okvir za dijalog.

[LISTING_FIVE](#listing_five) je "msgbox.c", kod koji implementira generički okvir za dijalog poruke i neke specijalizovane. Nekoliko makroa u dflat.h poziva funkciju GenericMessage da prikaže i obradi unapred pripremljene okvire za poruke, među njima MessageBox, YesNoBox, ErrorBox, CancelBox i InputBox. Svaki od njih ima svoj modul za obradu prozora, za obradu poruka na svoj način. Funkcija MomentariMessage prikazuje okvir sa porukom koji pozivalac mora da zatvori. Ovaj proces omogućava programu da objavi okvir sa porukom „molim čekajte“ dok je neki dugotrajan proces u toku. Program zatvara prozor kada se proces završi. Uskoro opisani klizač i ikona sata su još dva načina da se kaže korisniku da je u toku proces koji oduzima mnogo vremena.

[LISTING_SIX](#listing_six) je "radio.c", kod koji implementira radio dugme. Pored modula za obradu prozora koji oslikava radio dugme i obrađuje njegove radnje tastature i miša, izvorna datoteka uključuje funkciju PushRadioButton koja bira određeno radio dugme i funkciju RadioButtonSetting koja testira uključeno/isključeno stanje određenog radio dugmeta. Obe funkcije prihvataju pokazivač na okvir za dijalog i komandni kod povezan sa radio dugmetom.

[LISTING_SEVEN](#listing_seven) je "slidebox.c", kod koji implementira klizač. Klizač okvir je privremeni ekran koji program koristi da pokaže korisniku napredak dugotrajnog procesa. Program poziva funkciju SliderBok sa dužinom u znakovima okvira klizača, naslovom prozora za dijalog klizača i tekstualnom porukom koja se prikazuje iznad samog okvira klizača. Funkcija gradi i prikazuje dijalog okvira klizača i vraća svoju ručicu VINDOV pozivaocu. Pozivalac zatim šalje česte PAINT poruke sa procentom završenosti izraženim u drugom parametru. Vrednost će biti u rasponu od 0 do 100. Sve dok se ne pošalje poslednja vrednost, poruka PAINT vraća tačnu vrednost. Kada poruka PAINT stigne sa procentom koji je jednak ili veći od 100, prozor se zatvara, a poruka PAINT vraća lažnu vrednost. Ako korisnik odabere komandno dugme Otkaži pre nego što se proces završi, prozor se zatvara i poruka PAINT vraća lažnu vrednost. Primer programa memopad koristi klizač da prikaže napredak datoteke za štampanje.

[LISTING_EIGHT](#listing_eight) je "spinbutt.c", kod koji implementira dugme za okretanje. Dugme za okretanje je okvir sa listom u jednom redu sa strelicama nagore i nadole u ​​krajnjem desnom delu teksta. Korisnik može da skroluje kroz vrednosti pomoću tastera sa strelicama nagore i nadole ili klikom na znakove strelice nagore i nadole. U bilo kom trenutku, kontrola dugmeta za okretanje predstavlja trenutno prikazanu vrednost. Dijalog za podešavanje štampanja u programu za primer memopada koristi dugmad za okretanje za kontrolu margina za štampanje.

[LISTING_NINE](#listing_nine) je "text.c", izvorni kod koji implementira statičku kontrolu prikaza teksta u okviru za dijalog. Kontrola teksta se koristi za označavanje drugih kontrola i može imati istaknuti znak koji označava kombinaciju tastera Alt koja bira pridruženu kontrolu.

[LISTING_TEN](#listing_ten) je "watch.c", izvorni fajl koji implementira ikonu ručnog sata. Program može da pozove funkciju WatchIcon da promeni kursor miša u mali prozor koji podseća na brojčanik sata, kao i tekstualni režim. Funkcija VatchIcon vraća WINDOW ručicu ikone. Program može da pošalje prozoru ikone poruku CLOSE_WINDOW da se vrati na normalan kursor miša. Ikona sata je D-Flat verzija kursora Windows peščanog sata.

#### [LISTING_ONE]

```c
/* ----------- box.c ------------ */
#include "dflat.h"

int BoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int rtn;
    CTLWINDOW *ct = GetControl(wnd);
    if (ct != NULL)    {
        switch (msg)    {
            case SETFOCUS:
            case PAINT:
                return FALSE;
            case LEFT_BUTTON:
            case BUTTON_RELEASED:
                return SendMessage(GetParent(wnd), msg, p1, p2);
            case BORDER:
                rtn = BaseWndProc(BOX, wnd, msg, p1, p2);
                if (ct != NULL)
                    if (ct->itext != NULL)
                        writeline(wnd, ct->itext, 1, 0, FALSE);
                return rtn;
            default:
                break;
        }
    }
    return BaseWndProc(BOX, wnd, msg, p1, p2);
}
```

#### [LISTING_TWO]

```c
/* -------------- button.c -------------- */
#include "dflat.h"
void PaintMsg(WINDOW wnd, CTLWINDOW *ct, RECT *rc)
{
    if (isVisible(wnd))    {
        if (TestAttribute(wnd, SHADOW) && cfg.mono == 0)    {
            /* -------- draw the button's shadow ------- */
            int x;
            background = WndBackground(GetParent(wnd));
            foreground = BLACK;
            for (x = 1; x <= WindowWidth(wnd); x++)
                wputch(wnd, 223, x, 1);
            wputch(wnd, 220, WindowWidth(wnd), 0);
        }
        if (ct->itext != NULL)    {
            unsigned char *txt;
            txt = DFcalloc(1, strlen(ct->itext)+10);
            if (ct->setting == OFF)    {
                txt[0] = CHANGECOLOR;
                txt[1] = wnd->WindowColors
                            [HILITE_COLOR] [FG] | 0x80;
                txt[2] = wnd->WindowColors
                            [STD_COLOR] [BG] | 0x80;
            }
            CopyCommand(txt+strlen(txt),ct->itext,!ct->setting,
                WndBackground(wnd));
            SendMessage(wnd, CLEARTEXT, 0, 0);
            SendMessage(wnd, ADDTEXT, (PARAM) txt, 0);
            free(txt);
        }
        /* --------- write the button's text ------- */
        WriteTextLine(wnd, rc, 0, wnd == inFocus);
    }
}
void LeftButtonMsg(WINDOW wnd, MESSAGE msg, CTLWINDOW *ct)
{
    if (cfg.mono == 0)    {
        /* --------- draw a pushed button -------- */
        int x;
        background = WndBackground(GetParent(wnd));
        foreground = WndBackground(wnd);
        wputch(wnd, ' ', 0, 0);
        for (x = 0; x < WindowWidth(wnd); x++)    {
            wputch(wnd, 220, x+1, 0);
            wputch(wnd, 223, x+1, 1);
        }
    }
    if (msg == LEFT_BUTTON)
        SendMessage(NULL, WAITMOUSE, 0, 0);
    else
        SendMessage(NULL, WAITKEYBOARD, 0, 0);
    SendMessage(wnd, PAINT, 0, 0);
    if (ct->setting == ON)
        PostMessage(GetParent(wnd), COMMAND, ct->command, 0);
    else
        beep();
}
int ButtonProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    CTLWINDOW *ct = GetControl(wnd);
    if (ct != NULL)    {
        switch (msg)    {
            case SETFOCUS:
                BaseWndProc(BUTTON, wnd, msg, p1, p2);
                p1 = 0;
                /* ------- fall through ------- */
            case PAINT:
                PaintMsg(wnd, ct, (RECT*)p1);
                return TRUE;
            case KEYBOARD:
                if (p1 != '\r')
                    break;
                /* ---- fall through ---- */
            case LEFT_BUTTON:
                LeftButtonMsg(wnd, msg, ct);
                return TRUE;
            case HORIZSCROLL:
                return TRUE;
            default:
                break;
        }
    }
    return BaseWndProc(BUTTON, wnd, msg, p1, p2);
}
```

#### [LISTING_THREE]

```c
/* -------------- checkbox.c ------------ */
#include "dflat.h"
int CheckBoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int rtn;
    CTLWINDOW *ct = GetControl(wnd);
    if (ct != NULL)    {
        switch (msg)    {
            case SETFOCUS:
                if (!(int)p1)
                    SendMessage(NULL, HIDE_CURSOR, 0, 0);
            case MOVE:
                rtn = BaseWndProc(CHECKBOX, wnd, msg, p1, p2);
                SetFocusCursor(wnd);
                return rtn;
            case PAINT:    {
                char cb[] = "[ ]";
                if (ct->setting)
                    cb[1] = 'X';
                SendMessage(wnd, CLEARTEXT, 0, 0);
                SendMessage(wnd, ADDTEXT, (PARAM) cb, 0);
                SetFocusCursor(wnd);
                break;
            }
            case KEYBOARD:
                if ((int)p1 != ' ')
                    break;
            case LEFT_BUTTON:
                ct->setting ^= ON;
                SendMessage(wnd, PAINT, 0, 0);
                return TRUE;
            default:
                break;
        }
    }
    return BaseWndProc(CHECKBOX, wnd, msg, p1, p2);
}
BOOL CheckBoxSetting(DBOX *db, enum commands cmd)
{
    CTLWINDOW *ct = FindCommand(db, cmd, CHECKBOX);
    if (ct != NULL)
        return (ct->isetting == ON);
    return FALSE;
}
```

#### [LISTING_FOUR]

```c
/* -------------- combobox.c -------------- */
#include "dflat.h"
int ListProc(WINDOW, MESSAGE, PARAM, PARAM);
int ComboProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:
            wnd->extension = CreateWindow(
                        LISTBOX,
                        NULL,
                        wnd->rc.lf,wnd->rc.tp+1,
                        wnd->ht-1, wnd->wd+1,
                        NULL,
                        GetParent(wnd),
                        ListProc,
                        HASBORDER | NOCLIP | SAVESELF);
            ((WINDOW)(wnd->extension))->ct->command =
                                        wnd->ct->command;
            wnd->ht = 1;
            wnd->rc.bt = wnd->rc.tp;
            break;
        case PAINT:
            foreground = FrameForeground(wnd);
            background = FrameBackground(wnd);
            wputch(wnd, DOWNSCROLLBOX, WindowWidth(wnd), 0);
            break;
        case KEYBOARD:
            if ((int)p1 == DN)    {
                SendMessage(wnd->extension, SETFOCUS, TRUE, 0);
                return TRUE;
            }
            break;
        case LEFT_BUTTON:
            if ((int)p1 == GetRight(wnd) + 1)
                SendMessage(wnd->extension, SETFOCUS, TRUE, 0);
            break;
        case CLOSE_WINDOW:
            SendMessage(wnd->extension, CLOSE_WINDOW, 0, 0);
            break;
        default:
            break;
    }
    return BaseWndProc(COMBOBOX, wnd, msg, p1, p2);
}
int ListProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    DBOX *db = GetParent(wnd)->extension;
    WINDOW cwnd = ControlWindow(db, wnd->ct->command);
    char text[130];
    int rtn;
    WINDOW currFocus;
    switch (msg)    {
        case CREATE_WINDOW:
            wnd->ct = DFmalloc(sizeof(CTLWINDOW));
            wnd->ct->setting = OFF;
            break;
        case SETFOCUS:
            if ((int)p1 == FALSE)    {
                SendMessage(wnd, HIDE_WINDOW, 0, 0);
                wnd->ct->setting = OFF;
            }
            else
                wnd->ct->setting = ON;
            break;
        case SHOW_WINDOW:
            if (wnd->ct->setting == OFF)
                return TRUE;
            break;
        case BORDER:
            currFocus = inFocus;
            inFocus = NULL;
            rtn = DefaultWndProc(wnd, msg, p1, p2);
            inFocus = currFocus;
            return rtn;
        case LB_SELECTION:
            rtn = DefaultWndProc(wnd, msg, p1, p2);
            SendMessage(wnd, LB_GETTEXT,
                            (PARAM) text, wnd->selection);
            PutItemText(GetParent(wnd), wnd->ct->command, text);
            SendMessage(cwnd, PAINT, 0, 0);
            cwnd->TextChanged = TRUE;
            return rtn;
        case KEYBOARD:
            switch ((int) p1)    {
                case ESC:
                case FWD:
                case BS:
                    SendMessage(cwnd, SETFOCUS, TRUE, 0);
                    return TRUE;
                default:
                    break;
            }
            break;
        case LB_CHOOSE:
            SendMessage(cwnd, SETFOCUS, TRUE, 0);
            return TRUE;
        case CLOSE_WINDOW:
            if (wnd->ct != NULL)
                free(wnd->ct);
            wnd->ct = NULL;
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}
void PutComboListText(WINDOW wnd, enum commands cmd, char *text)
{
    CTLWINDOW *ct = FindCommand(wnd->extension, cmd, COMBOBOX);
    if (ct != NULL)        {
        WINDOW lwnd = ((WINDOW)(ct->wnd))->extension;
        SendMessage(lwnd, ADDTEXT, (PARAM) text, 0);
    }
}
```

#### [LISTING_FIVE]

```c
/* ------------------ msgbox.c ------------------ */
#include "dflat.h"
extern DBOX MsgBox;
extern DBOX InputBoxDB;
WINDOW CancelWnd;
static int ReturnValue;
int MessageBoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:
            GetClass(wnd) = MESSAGEBOX;
            ClearAttribute(wnd, CONTROLBOX);
            break;
        case KEYBOARD:
            if (p1 == '\r' || p1 == ESC)
                ReturnValue = (int)p1;
            break;
        default:
            break;
    }
    return BaseWndProc(MESSAGEBOX, wnd, msg, p1, p2);
}
int YesNoBoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:
            GetClass(wnd) = MESSAGEBOX;
            ClearAttribute(wnd, CONTROLBOX);
            break;
        case KEYBOARD:    {
            int c = tolower((int)p1);
            if (c == 'y')
                SendMessage(wnd, COMMAND, ID_OK, 0);
            else if (c == 'n')
                SendMessage(wnd, COMMAND, ID_CANCEL, 0);
            break;
        }
        default:
            break;
    }
    return BaseWndProc(MESSAGEBOX, wnd, msg, p1, p2);
}
int ErrorBoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:
            GetClass(wnd) = ERRORBOX;
            break;
        case KEYBOARD:
            if (p1 == '\r' || p1 == ESC)
                ReturnValue = (int)p1;
            break;
        default:
            break;
    }
    return BaseWndProc(ERRORBOX, wnd, msg, p1, p2);
}
int CancelBoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:
            CancelWnd = wnd;
            SendMessage(wnd, CAPTURE_MOUSE, 0, 0);
            SendMessage(wnd, CAPTURE_KEYBOARD, 0, 0);
            break;
        case COMMAND:
            if ((int) p1 == ID_CANCEL && (int) p2 == 0)
                SendMessage(GetParent(wnd), msg, p1, p2);
            return TRUE;
        case CLOSE_WINDOW:
            CancelWnd = NULL;
            SendMessage(wnd, RELEASE_MOUSE, 0, 0);
            SendMessage(wnd, RELEASE_KEYBOARD, 0, 0);
            p1 = TRUE;
            break;
        default:
            break;
    }
    return BaseWndProc(MESSAGEBOX, wnd, msg, p1, p2);
}
void CloseCancelBox(void)
{
    if (CancelWnd != NULL)
        SendMessage(CancelWnd, CLOSE_WINDOW, 0, 0);
}
static char *InputText;
static int TextLength;
int InputBoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int rtn;
    switch (msg)    {
        case CREATE_WINDOW:
            rtn = DefaultWndProc(wnd, msg, p1, p2);
            SendMessage(ControlWindow(&InputBoxDB,ID_INPUTTEXT),
                        SETTEXTLENGTH, TextLength, 0);
            return rtn;
        case COMMAND:
            if ((int) p1 == ID_OK && (int) p2 == 0)
                GetItemText(wnd, ID_INPUTTEXT, InputText, TextLength);
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}
BOOL InputBox(WINDOW wnd,char *ttl,char *msg,char *text,int len)
{
    InputText = text;
    TextLength = len;
    InputBoxDB.dwnd.title = ttl;
    InputBoxDB.dwnd.w = 4 +
        max(20, max(len, max(strlen(ttl), strlen(msg))));
    InputBoxDB.ctl[1].dwnd.x = (InputBoxDB.dwnd.w-2-len)/2;
    InputBoxDB.ctl[0].dwnd.w = strlen(msg);
    InputBoxDB.ctl[0].itext = msg;
    InputBoxDB.ctl[1].dwnd.w = len;
    InputBoxDB.ctl[2].dwnd.x = (InputBoxDB.dwnd.w - 20) / 2;
    InputBoxDB.ctl[3].dwnd.x = InputBoxDB.ctl[2].dwnd.x + 10;
    InputBoxDB.ctl[2].isetting = ON;
    InputBoxDB.ctl[3].isetting = ON;
    return DialogBox(wnd, &InputBoxDB, TRUE, InputBoxProc);
}

BOOL GenericMessage(WINDOW wnd,char *ttl,char *msg,int buttonct,
      int (*wndproc)(struct window *,enum messages,PARAM,PARAM),
      char *b1, char *b2, int c1, int c2, int isModal)
{
    BOOL rtn;
    MsgBox.dwnd.title = ttl;
    MsgBox.ctl[0].dwnd.h = MsgHeight(msg);
    MsgBox.ctl[0].dwnd.w = max(max(MsgWidth(msg),
            buttonct*8 + buttonct + 2), strlen(ttl)+2);
    MsgBox.dwnd.h = MsgBox.ctl[0].dwnd.h+6;
    MsgBox.dwnd.w = MsgBox.ctl[0].dwnd.w+4;
    if (buttonct == 1)
        MsgBox.ctl[1].dwnd.x = (MsgBox.dwnd.w - 10) / 2;
    else    {
        MsgBox.ctl[1].dwnd.x = (MsgBox.dwnd.w - 20) / 2;
        MsgBox.ctl[2].dwnd.x = MsgBox.ctl[1].dwnd.x + 10;
        MsgBox.ctl[2].class = BUTTON;
    }
    MsgBox.ctl[1].dwnd.y = MsgBox.dwnd.h - 4;
    MsgBox.ctl[2].dwnd.y = MsgBox.dwnd.h - 4;
    MsgBox.ctl[0].itext = msg;
    MsgBox.ctl[1].itext = b1;
    MsgBox.ctl[2].itext = b2;
    MsgBox.ctl[1].command = c1;
    MsgBox.ctl[2].command = c2;
    MsgBox.ctl[1].isetting = ON;
    MsgBox.ctl[2].isetting = ON;
    rtn = DialogBox(wnd, &MsgBox, isModal, wndproc);
    MsgBox.ctl[2].class = 0;
    return rtn;
}
WINDOW MomentaryMessage(char *msg)
{
    WINDOW wnd = CreateWindow(
                    TEXTBOX,
                    NULL,
                    -1,-1,MsgHeight(msg)+2,MsgWidth(msg)+2,
                    NULL,NULL,NULL,
                    HASBORDER | SHADOW | SAVESELF);
    SendMessage(wnd, SETTEXT, (PARAM) msg, 0);
    if (cfg.mono == 0)    {
        WindowClientColor(wnd, WHITE, GREEN);
        WindowFrameColor(wnd, WHITE, GREEN);
    }
    SendMessage(wnd, SHOW_WINDOW, 0, 0);
    return wnd;
}
int MsgHeight(char *msg)
{
    int h = 1;
    while ((msg = strchr(msg, '\n')) != NULL)    {
        h++;
        msg++;
    }
    return min(h, SCREENHEIGHT-10);
}
int MsgWidth(char *msg)
{
    int w = 0;
    char *cp = msg;
    while ((cp = strchr(msg, '\n')) != NULL)    {
        w = max(w, (int) (cp-msg));
        msg = cp+1;
    }
    return min(max(strlen(msg),w), SCREENWIDTH-10);
}
```

#### [LISTING_SIX]

```c
/* -------- radio.c -------- */
#include "dflat.h"
static CTLWINDOW *rct[MAXRADIOS];
int RadioButtonProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int rtn;
    DBOX *db = GetParent(wnd)->extension;
    CTLWINDOW *ct = GetControl(wnd);
    if (ct != NULL)    {
        switch (msg)    {
            case SETFOCUS:
                if (!(int)p1)
                    SendMessage(NULL, HIDE_CURSOR, 0, 0);
            case MOVE:
                rtn = BaseWndProc(RADIOBUTTON,wnd,msg,p1,p2);
                SetFocusCursor(wnd);
                return rtn;
            case PAINT:    {
                char rb[] = "( )";
                if (ct->setting)
                    rb[1] = 7;
                SendMessage(wnd, CLEARTEXT, 0, 0);
                SendMessage(wnd, ADDTEXT, (PARAM) rb, 0);
                SetFocusCursor(wnd);
                break;
            }
            case KEYBOARD:
                if ((int)p1 != ' ')
                    break;
            case LEFT_BUTTON:
                PushRadioButton(db, ct->command);
                break;
            default:
                break;
        }
    }
    return BaseWndProc(RADIOBUTTON, wnd, msg, p1, p2);
}
void PushRadioButton(DBOX *db, enum commands cmd)
{
    CTLWINDOW *ct = FindCommand(db, cmd, RADIOBUTTON);
    if (ct != NULL)    {
        SetRadioButton(db, ct);
        ct->isetting = ON;
    }
}
void SetRadioButton(DBOX *db, CTLWINDOW *ct)
{
    CTLWINDOW *ctt = db->ctl;
    int i;
    /* --- clear all the radio buttons in this group on the dialog box --- */
    /* -------- build a table of all radio buttons at the
            same x vector ---------- */
    for (i = 0; i < MAXRADIOS; i++)
        rct[i] = NULL;
    while (ctt->class)    {
        if (ctt->class == RADIOBUTTON)
            if (ct->dwnd.x == ctt->dwnd.x)
                rct[ctt->dwnd.y] = ctt;
        ctt++;
    }
    /* ----- find the start of the radiobutton group ---- */
    i = ct->dwnd.y;
    while (i >= 0 && rct[i] != NULL)
        --i;
    /* ---- ignore everthing before the group ------ */
    while (i >= 0)
        rct[i--] = NULL;
    /* ----- find the end of the radiobutton group ---- */
    i = ct->dwnd.y;
    while (i < MAXRADIOS && rct[i] != NULL)
        i++;
    /* ---- ignore everthing past the group ------ */
    while (i < MAXRADIOS)
        rct[i++] = NULL;
    for (i = 0; i < MAXRADIOS; i++)    {
        if (rct[i] != NULL)    {
            int wason = rct[i]->setting;
            rct[i]->setting = OFF;
            if (wason)
                SendMessage(rct[i]->wnd, PAINT, 0, 0);
        }
    }
    ct->setting = ON;
    SendMessage(ct->wnd, PAINT, 0, 0);
}
BOOL RadioButtonSetting(DBOX *db, enum commands cmd)
{
    CTLWINDOW *ct = FindCommand(db, cmd, RADIOBUTTON);
    if (ct != NULL)
        return (ct->setting == ON);
    return FALSE;
}
```

#### [LISTING_SEVEN]

```c
/* ------------- slidebox.c ------------ */
#include "dflat.h"
static int (*GenericProc)
    (WINDOW wnd,MESSAGE msg,PARAM p1,PARAM p2);
static BOOL KeepRunning;
static int SliderLen;
static int Percent;
extern DBOX SliderBoxDB;
static void InsertPercent(char *s)
{
    int offset;
    char pcc[5];

    sprintf(s, "%c%c%c",
            CHANGECOLOR,
            color[DIALOG][SELECT_COLOR][FG]+0x80,
            color[DIALOG][SELECT_COLOR][BG]+0x80);
    s += 3;
    memset(s, ' ', SliderLen);
    *(s+SliderLen) = '\0';
    sprintf(pcc, "%d%%", Percent);
    strncpy(s+SliderLen/2-1, pcc, strlen(pcc));
    offset = (SliderLen * Percent) / 100;
    memmove(s+offset+4, s+offset, strlen(s+offset)+1);
    sprintf(pcc, "%c%c%c%c",
            RESETCOLOR,
            CHANGECOLOR,
            color[DIALOG][SELECT_COLOR][BG]+0x80,
            color[DIALOG][SELECT_COLOR][FG]+0x80);
    strncpy(s+offset, pcc, 4);
    *(s + strlen(s) - 1) = RESETCOLOR;
}
static int SliderTextProc(
            WINDOW wnd,MESSAGE msg,PARAM p1,PARAM p2)
{
    switch (msg)    {
        case PAINT:
            Percent = (int)p2;
            InsertPercent(GetText(wnd) ?
                GetText(wnd) : SliderBoxDB.ctl[1].itext);
            GenericProc(wnd, PAINT, 0, 0);
            if (Percent >= 100)
                SendMessage(GetParent(wnd),COMMAND,ID_CANCEL,0);
            if (!dispatch_message())
                PostMessage(GetParent(wnd), ENDDIALOG, 0, 0);
            return KeepRunning;
        default:
            break;
    }
    return GenericProc(wnd, msg, p1, p2);
}
static int SliderBoxProc(
            WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int rtn;
    WINDOW twnd;
    switch (msg)    {
        case CREATE_WINDOW:
            AddAttribute(wnd, SAVESELF);
            rtn = DefaultWndProc(wnd, msg, p1, p2);
            twnd = SliderBoxDB.ctl[1].wnd;
            GenericProc = twnd->wndproc;
            twnd->wndproc = SliderTextProc;
            KeepRunning = TRUE;
            SendMessage(wnd, CAPTURE_MOUSE, 0, 0);
            SendMessage(wnd, CAPTURE_KEYBOARD, 0, 0);
            return rtn;
        case COMMAND:
            if ((int)p2 == 0 && (int)p1 == ID_CANCEL)    {
                if (Percent >= 100 ||
                        YesNoBox("Terminate process?"))
                    KeepRunning = FALSE;
                else
                    return TRUE;
            }
            break;
        case CLOSE_WINDOW:
            SendMessage(wnd, RELEASE_MOUSE, 0, 0);
            SendMessage(wnd, RELEASE_KEYBOARD, 0, 0);
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}
WINDOW SliderBox(int len, char *ttl, char *msg)
{
    SliderLen = len;
    SliderBoxDB.dwnd.title = ttl;
    SliderBoxDB.dwnd.w =
        max(strlen(ttl),max(len, strlen(msg)))+4;
    SliderBoxDB.ctl[0].itext = msg;
    SliderBoxDB.ctl[0].dwnd.w = strlen(msg);
    SliderBoxDB.ctl[0].dwnd.x =
        (SliderBoxDB.dwnd.w - strlen(msg)-1) / 2;
    SliderBoxDB.ctl[1].itext =
        DFrealloc(SliderBoxDB.ctl[1].itext, len+10);
    Percent = 0;
    InsertPercent(SliderBoxDB.ctl[1].itext);
    SliderBoxDB.ctl[1].dwnd.w = len;
    SliderBoxDB.ctl[1].dwnd.x = (SliderBoxDB.dwnd.w-len-1)/2;
    SliderBoxDB.ctl[2].dwnd.x = (SliderBoxDB.dwnd.w-10)/2;
    DialogBox(NULL, &SliderBoxDB, FALSE, SliderBoxProc);
    return SliderBoxDB.ctl[1].wnd;
}
```

#### [LISTING_EIGHT]

```c
/* ------------ spinbutt.c ------------- */
#include "dflat.h"
int SpinButtonProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int rtn;
    CTLWINDOW *ct = GetControl(wnd);
    if (ct != NULL)    {
        switch (msg)    {
            case CREATE_WINDOW:
                wnd->wd -= 2;
                wnd->rc.rt -= 2;
                break;
            case SETFOCUS:
                rtn = BaseWndProc(SPINBUTTON, wnd, msg, p1, p2);
                if (!(int)p1)
                    SendMessage(NULL, HIDE_CURSOR, 0, 0);
                SetFocusCursor(wnd);
                return rtn;
            case PAINT:
                foreground = FrameForeground(wnd);
                background = FrameBackground(wnd);
                wputch(wnd,UPSCROLLBOX,WindowWidth(wnd), 0);
                wputch(wnd,DOWNSCROLLBOX,WindowWidth(wnd)+1,0);
                SetFocusCursor(wnd);
                break;
            case LEFT_BUTTON:
                if (p1 == GetRight(wnd) + 1)
                    SendMessage(wnd, KEYBOARD, UP, 0);
                else if (p1 == GetRight(wnd) + 2)
                    SendMessage(wnd, KEYBOARD, DN, 0);
                if (wnd != inFocus)
                    SendMessage(wnd, SETFOCUS, TRUE, 0);
                return TRUE;
            case LB_SETSELECTION:
                rtn = BaseWndProc(SPINBUTTON, wnd, msg, p1, p2);
                wnd->wtop = (int) p1;
                SendMessage(wnd, PAINT, 0, 0);
                return rtn;
            default:
                break;
        }
    }
    return BaseWndProc(SPINBUTTON, wnd, msg, p1, p2);
}
```

#### [LISTING_NINE]

```c
/* -------------- text.c -------------- */
#include "dflat.h"
int TextProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int i, len;
    CTLWINDOW *ct = GetControl(wnd);
    char *cp, *cp2 = ct->itext;
    switch (msg)    {
        case PAINT:
            if (ct == NULL ||
                ct->itext == NULL ||
                    GetText(wnd) != NULL)
                break;
            len = min(ct->dwnd.h, MsgHeight(cp2));
            cp = cp2;
            for (i = 0; i < len; i++)    {
                int mlen;
                char *txt = cp;
                char *cp1 = cp;
                char *np = strchr(cp, '\n');
                if (np != NULL)
                    *np = '\0';
                mlen = strlen(cp);
                while ((cp1=strchr(cp1,SHORTCUTCHAR)) != NULL) {
                    mlen += 3;
                    cp1++;
                }
                if (np != NULL)
                    *np = '\n';
                txt = DFmalloc(mlen+1);
                 CopyCommand(txt, cp, FALSE, WndBackground(wnd));
                txt[mlen] = '\0';
                SendMessage(wnd, ADDTEXT, (PARAM)txt, 0);
                if ((cp = strchr(cp, '\n')) != NULL)
                    cp++;
                free(txt);
            }
            break;
        default:
            break;
    }
    return BaseWndProc(TEXT, wnd, msg, p1, p2);
}
```

#### [LISTING_TEN]

```c
/* ----------- watch.c ----------- */

#include "dflat.h"

static int WatchIconProc(
                WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int rtn;
    switch (msg)    {
        case CREATE_WINDOW:
            rtn = DefaultWndProc(wnd, msg, p1, p2);
            SendMessage(wnd, CAPTURE_MOUSE, 0, 0);
            SendMessage(wnd, HIDE_MOUSE, 0, 0);
            SendMessage(wnd, CAPTURE_KEYBOARD, 0, 0);
            return rtn;
        case PAINT:
            SetStandardColor(wnd);
            writeline(wnd, " @ ", 1, 1, FALSE);
            return TRUE;
        case BORDER:
            rtn = DefaultWndProc(wnd, msg, p1, p2);
            writeline(wnd, "M", 2, 0, FALSE);
            return rtn;
        case MOUSE_MOVED:
            SendMessage(wnd, HIDE_WINDOW, TRUE, 0);
            SendMessage(wnd, MOVE, p1, p2);
            SendMessage(wnd, SHOW_WINDOW, 0, 0);
            return TRUE;
        case CLOSE_WINDOW:
            SendMessage(wnd, RELEASE_MOUSE, 0, 0);
            SendMessage(wnd, RELEASE_KEYBOARD, 0, 0);
            SendMessage(wnd, SHOW_MOUSE, 0, 0);
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}

WINDOW WatchIcon(void)
{
    int mx, my;
    WINDOW wnd;
    SendMessage(NULL, CURRENT_MOUSE_CURSOR,
                        (PARAM) &mx, (PARAM) &my);
    wnd = CreateWindow(
                    BOX,
                    NULL,
                    mx, my, 3, 5,
                    NULL,NULL,
                    WatchIconProc,
                    VISIBLE | HASBORDER | SHADOW | SAVESELF);
    return wnd;
}
```

Copyright © 1992, Dr. Dobb's Journal
