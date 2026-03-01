
# C PROGRAMMING 9206

## D-Flat dijaloški okviri - Al Stevens

### D-Flat dijaloški okviri

Okvir za dijalog je prozor koji ima jedan ili više kontrolnih prozora pomoću kojih korisnik unosi komande i podatke. U koloni iz septembra 1991. opisao sam kako definišete svoje dijaloške okvire pomoću makroa. Prošlog meseca smo razgovarali o klasi prozora APPLICATION i videli ste kako program koristi dijaloške okvire. Ovog meseca ćemo pogledati kod koji implementira sam okvir za dijalog. Sledećeg meseca ćemo pogledati kod koji implementira kontrolne prozore.

[LISTING_ONE](#listing_one), je "dialbox.c". Uključuje funkciju DialogBox koju aplikacija poziva da otvori okvir za dijalog i kod za pravljenje okvira za dijalog i upravljanje njim. Ako je okvir za dijalog „modalan“, on zadržava fokus u sebi dok je otvoren. Ne možete doći do stavke menija ili prozora dokumenta bez zatvaranja okvira za dijalog. Ako je okvir za dijalog „bez moda“, on radi kao i svaki drugi prozor dokumenta. Otvarate ih oba pozivanjem iste funkcije. Funkcija DialogBok za dijaloški okvir bez načina vraća se čim kreira i prikaže okvir za dijalog. Funkcija za modalni okvir za dijalog se ne vraća sve dok korisnik ne zatvori okvir za dijalog.

Prva funkcija u dijalbok.c je funkcija ClearDialogBokes. Prozori za kontrolu teksta u okviru za dijalog zadržavaju tekstualne vrednosti koje korisnik unese čak i nakon što se prozor zatvori. Ovo omogućava da unosi teksta traju za naredne upotrebe okvira za dijalog. Ove tekstualne vrednosti su na dalekoj hrpi. Obično DOS oslobađa sve alokacije gomile kada se program završi, ali sam dodao ovu funkciju da bih mogao da koristim D-Flat u TSR aplikacijama. D-Flat poziva funkciju ClearDialogBokes kada se aplikacija završi, a funkcija oslobađa sve alokacije gomile koje su još otvorene.

Funkcija DialogBok je ona koju aplikacija poziva da otvori okvir za dijalog. Funkcija kreira prozor okvira za dijalog, prikazuje ga, postavlja fokus na svoj prvi kontrolni prozor i šalje prozoru dijaloga poruku INITIATE_DIALOG. Ako je okvir za dijalog bez mode, funkcija se vraća. Ako je okvir za dijalog modalni, funkcija hvata miša i tastaturu u prozor okvira za dijalog i ulazi u petlju za slanje D-Flat poruka. Petlja se neće vratiti sve dok se okvir za dijalog ne zatvori, što će se desiti kao rezultat radnji preduzetih u podrazumevanom modulu za obradu prozora dijaloga.

### Poruke u okviru dijaloga

Okvir za dijalog može imati prilagođeni modul za obradu prozora, koji aplikacija obezbeđuje. Tamo će njegove komande biti presretane i obrađene. Podrazumevani modul za obradu prozora u dijalbok.c je DialogProc. Presreće poruke i obrađuje ih.

Poruka CREATE_VINDOV pravi tabelu dijaloških okvira koje će funkcija ClearDialogBok koristiti. Zatim se povezuje sa modulom za obradu prozora osnovne klase prozora da bi kreirao prozor. Okvir za dijalog ima nekoliko kontrolnih prozora. Ova poruka zatim kreira svaki od kontrolnih prozora i inicijalizuje kontrole zasnovane na tekstu tekstom koji definicija okvira za dijalog navodi.

Poruka SETFOCUS zaobilazi uobičajenu D-Flat logiku kada se fokus postavlja na modalni okvir za dijalog. Šalje poruku da izbriše fokus sa bilo kog prozora koji trenutno ima fokus i postavlja promenljivu inFocus.

Modalni dijaloški okviri ignorišu SHIFT_CHANGED poruku kako bi sprečili D-Flat da pokuša da stavi fokus na traku menija kada korisnik pritisne i otpusti taster Alt.

Okvir za dijalog obrađuje presretnutu poruku LEFT_BUTTON miša za kontrole kombinovanog okvira i dugmeta za okretanje. Ako miš pritisne dugmad za pomeranje za te kontrole, program šalje poruku samim kontrolnim prozorima.

Ako korisnik pritisne F1, poruka KEI-BOARD prikazuje prozor pomoći povezan sa kontrolom koja je u fokusu. Tasteri Shift+Tab, Backspace i Up daju fokus kontroli koja prethodi onoj sa trenutnim fokusom, dok tasteri Alt+F6, Tab, strelica nadesno i strelica nadole daju fokus kontroli koja prati trenutni fokus. Tasteri Ctrl+F4 i Esc šalju naredbu ID_CANCEL u okvir za dijalog. Svi ostali pretisci na tastere se podudaraju sa prečicama za dijalog kako bi se videlo da li korisnik pokušava da postavi fokus na jedan od njih.

Ako korisnik izabere komande OK ili Cancel, COMMAND poruka postavlja globalnu promenljivu koja označava koju, i postavlja END_DIALOG poruku u modalni okvir za dijalog ili šalje poruku CLOSE_VINDOV u okvir za dijalog bez načina.

Poruka CLOSE_VINDOV šalje naredbu ID_CANCEL u okvir za dijalog pre zatvaranja prozora.

### Druge funkcije dijaloškog okvira

Nekoliko drugih funkcija dijaloga podržava kontrolne prozore ili aplikaciju. Funkcija FindCommand vraća kontrolnu strukturu za kontrolu koja sadrži navedeni kod komande. Funkcija ControlVindov vraća ručku prozora određene kontrole. Neke kontrole, kao što su polja za potvrdu, su ili uključene ili isključene. Funkcija ControlSetting postavlja stanje tih kontrola. Postoji nekoliko funkcija koje dobijaju i postavljaju tekstualne vrednosti za kontrole dijaloga.

### Kontrolne poruke prozora

Funkcija ControlProc je generički modul za obradu prozora za sve kontrolne prozore. Svaka klasa kontrolnog prozora će imati svoj modul za obradu prozora, ali funkcija ControlProc prva dobija poruke za sve kontrole.

Poruka CREATE_VINDOV premešta adresu kontrolne strukture iz polja proširenja u sopstveno polje u strukturi prozora.

Ako korisnik pritisne F1, poruka KEIBOARD prikazuje prozor pomoći povezan sa kontrolom. Alt+F6, Ctrl +F4 i Alt+F4 se postavljaju u prozor za dijalog. Tasteri sa strelicama se konvertuju u tastere koji menjaju fokus na susedne kontrole, osim ako kontrola nije okvir za uređivanje ili okvir sa listom. Taster Enter se konvertuje u komandu ID_OK osim za višelinijske okvire za uređivanje i dugmad. Poruka PAINT dodaje trake za pomeranje iz tekstualnih prozora ako širina ili dužina teksta premašuje dimenzije prozora. Poruka BORDER sprečava okvire za uređivanje da prikažu ivicu dvostruke linije kada dobiju fokus. Poruka SET-FOCUS šalje komande ENTERFOCUS ili LEAVEFOCUS u okvir za dijalog. Komanda CLOSE_VINDOV ažurira tekstualne vrednosti za inicijalizaciju tekstualnih kontrolnih prozora ako je korisnik izabrao komandu OK.

### Neki problemi sa D-Flat++ dizajnom

Razmišljam o arhitekturi onoga što ću nazvati D-Flat++, ili DF++, dok ne smislim bolje ime. Postoje brojna razmatranja dizajna o kojima treba razmisliti. Prvi među njima su moji ciljevi za DF++, a to su:

- C++ biblioteka klasa koja implementira D-Flat korisnički interfejs
- Kod koji se kompajlira sa kompajlerima Borland, Microsoft, "TopSpeed i Zortech C++"

Objektno orijentisana biblioteka klasa za takav sistem imaće mnogo toga zajedničkog sa arhitekturom zasnovanom na porukama zasnovanom na događajima, a imaće i mnogo stvari koje su takođe različite. Imam razumnu ranu ideju o tome kako će API izgledati. Program će deklarisati promenljivu prozora i prozor će se pojaviti. Program će izmeniti izgled prozora slanjem poruka i njegovo ponašanje dodavanjem novih poruka. Kako će program to učiniti?

### Obrada poruka

U D-Flat API-ju, program obezbeđuje funkciju obrade poruka kada kreira prozor. Funkcija za obradu poruka presreće poruke koje želi da obradi i prosleđuje ostale sledećoj funkciji za obradu poruka gore u stablu klasa. Ovaj postupak liči na polimorfizam, ali ga jezik ne primenjuje; programer je.

U hijerarhiji C++ klasa, osnovna klasa može deklarisati virtuelnu funkciju člana. Kada izvedena klasa deklariše funkciju člana sa istim imenom i listom parametara, funkcija izvedene klase se izvršava kada program pozove funkciju u ime objekta izvedene klase. Hajde da zadržimo ovu terminologiju ispravno. Pozivanje funkcija članova je način na koji C++ program šalje poruku objektu. To znači isto.

Dakle, sledi da je način implementacije klase prozora koja ima sopstvene poruke da se izvede klasa i da joj se dodaju neke funkcije poruke. Zapamtite da klasa na dnu hijerarhije dobija prvi udarac na svaku poruku, tako da svaka funkcija poruke mora biti virtuelna da bi podržala sledeće izvedene klase.

Postoji, međutim, bora. U arhitekturi D-Flat zasnovanoj na porukama, određena klasa prozora ne mora da zna za sve poruke – samo za one koje presreće. Prolazi sve ostale u hijerarhiji. To može učiniti jer je poruka generički konstrukt podataka, a njeno značenje tumače funkcije klase koje je obrađuju. Poruka u C++ je funkcija. Pretpostavimo hijerarhiju klasa A->B->C, sa A kao najvišom osnovnom klasom. Ako i A i C obrađuju određenu poruku, o kojoj B ne zna, onda C mora znati da prosledi poruku A kada C završi sa njom. To znači da kada dizajnirate izvedenu klasu, morate znati mnogo o klasama gore u organizaciji, koja leti brzinom od 1 maha pravo u lice tradicionalnog koncepta apstraktne crne kutije. Svetla strana je što objektno orijentisani pristup eliminiše ozbiljan nivo troškova. Svaka D-Flat poruka prolazi kroz funkciju obrade poruka svake klase do stabla sve dok je jedna od njih ne presretne i obradi. Pristup DF++ porukama će poslati poruku najnižoj funkciji u stablu koja je zainteresovana za to. Funkcije niže klase to nikada neće videti. Ako ta funkcija treba da prosledi poruku gore, poruka će ići direktno sledećoj zainteresovanoj klasi. Ostaje da se vidi da li mehanizam virtuelne tabele u C++-u nameće veće troškove od onog koji ćemo eliminisati. Jedno je ipak sigurno. Provešćete manje vremena u svom programu za otklanjanje grešaka koračajući kroz slojeve funkcija za obradu poruka koje ispuštaju dno velikog iskaza prekidača, samo da bi poruku preneli sledećoj klasi.

### Prepoznavanje poruka

U D-Flat-u, poruka ima nabrojanu vrednost. U C++, poruka ima ime funkcije. Jedna od D-Flat poruka je COMMAND, koju meniji i dijaloški okviri šalju da kažu da je korisnik izabrao stavku menija, pritisnuo komandno dugme i tako dalje. Svaka diskretna komanda takođe ima nabrojanu vrednost. Kada dizajnirate meni ili okvir za dijalog, vrednost komande povezujete sa radnjom korisnika. Prozor aplikacije ili dijaloga ima jedinstvenu funkciju za obradu poruka koja presreće poruku KOMANDA, otkriva koja se komandna radnja dogodila i izvršava neki kod da odgovori na komandu.

U idealnom slučaju, klasa prozora bi imala funkciju člana za svaku komandu, a dizajn menija ili okvira za dijalog bi povezivao tu funkciju člana sa radnjom korisnika. D-Flat API je na mnogo načina sličan Vindovs API-ju, pa bi bilo korisno videti kako to rade neke od biblioteka C++ klasa za Vindovs.

The Foundation. Microsoft Foundation Class Librari koristi ono što nazivaju „mapa poruka“. Oni zadržavaju Vindovs poruke i komandne kodove i povezuju ih na mapi poruka sa makro izjavama kao što je ova:

```c
ON_COMMAND(IDM_NEW, OnNew)
```

Parametar OnNev u tom makrou imenuje funkciju člana koja predstavlja COMMAND poruku za komandu IDM_NEV.

OVL. Biblioteka klasa Borland ObjectVindovs ima drugačiji pristup. Borland je proširio sintaksu jezika C++ da bi specificirao deklaraciju „funkcije člana-odgovora“. Deklaracija je javna funkcija člana klase koja izgleda ovako:

```c
  virtual void CMFileNew(RtMessage Msg) = [CM_FIRST+CM_FILENEW];
```

C++/Vievs. C++/Vievs biblioteka klasa iz CNS-a koristi još jedan pristup. Aplikacioni program kreira iskačući meni i šalje mu poruku sa pokazivačem na niz struktura – ​​po jednu za svaku stavku u meniju. Struktura uključuje referencu na prozor aplikacije i pokazivač funkcije koji se poziva kada korisnik odabere stavku menija.

Imajte na umu, međutim, da su sve ove biblioteke klasa C++ omoti oko Vindovs API-ja zasnovanog na porukama zasnovanog na događajima, i stoga nužno zadržavaju neke od karakteristika te platforme. Cilj DF++ je da prepiše osnovni softver korisničkog interfejsa kao i njegov API u C++, tako da nismo vezani ni za jednu postojeću metodu i arhitekturu. Videćemo kako će biti.

### Druge klase

Ne postoje standardi za C++ klase. Stroustrup je ignorisao ovo pitanje. Jedini de facto standard je IOSTREAM paket koji AT&T distribuira i koji uključuje većina kompajlera. Ipak, postoje neke očigledne potrebe. Mnogi prevodioci uključuju klase za stringove, kontejnere i slično, ali ne postoji konsenzus o formatu i implementaciji. Neki se neće složiti, ali, po mom mišljenju, jedino najvrednije poboljšanje koje C++ donosi u C je mogućnost programera da proširi jezik dodavanjem tipova podataka. Postoje i druga poboljšanja, naravno, ali ako nikada niste koristili nijedno od njih i ako nikada niste napisali objektno orijentisani program, ova karakteristika bi učinila da C++ vredi koristiti. Ali gde su časovi? Projekat DF++ počinje bez standardne biblioteke osim standardne biblioteke C, i, prema tome, bez standardnih klasa, i ja moram da odlučim šta da radim u vezi sa tim.

#### LISTING_ONE

```c
/* ----------------- dialbox.c -------------- */

#include "dflat.h"

static int inFocusCommand(DBOX *);
static void dbShortcutKeys(DBOX *, int);
static int ControlProc(WINDOW, MESSAGE, PARAM, PARAM);
static void ChangeFocus(WINDOW, int);
static CTLWINDOW *AssociatedControl(DBOX *, enum commands);

static BOOL SysMenuOpen;

static DBOX **dbs = NULL;
static int dbct = 0;

/* --- clear all heap allocations to control text fields --- */
void ClearDialogBoxes(void)
{
    int i;
    for (i = 0; i < dbct; i++)    {
        CTLWINDOW *ct = (*(dbs+i))->ctl;
        while (ct->class)    {
            if ((ct->class == EDITBOX ||
                    ct->class == COMBOBOX) &&
                    ct->itext != NULL)
                free(ct->itext);
            ct++;
        }
    }
    if (dbs != NULL)    {
        free(dbs);
        dbs = NULL;
    }
    dbct = 0;
}

/* -------- CREATE_WINDOW Message --------- */
static int CreateWindowMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    DBOX *db = wnd->extension;
    CTLWINDOW *ct = db->ctl;
    WINDOW cwnd;
    int rtn, i;
    /* ---- build a table of processed dialog boxes ---- */
    for (i = 0; i < dbct; i++)
        if (db == dbs[i])
            break;
    if (i == dbct)    {
        dbs = realloc(dbs, sizeof(DBOX *) * (dbct+1));
        if (dbs != NULL)
            *(dbs + dbct++) = db;
    }
    rtn = BaseWndProc(DIALOG, wnd, CREATE_WINDOW, p1, p2);
    ct = db->ctl;
    while (ct->class)    {
        int attrib = 0;
        if (TestAttribute(wnd, NOCLIP))
            attrib |= NOCLIP;
        if (wnd->Modal)
            attrib |= SAVESELF;
        ct->setting = ct->isetting;
        if (ct->class == EDITBOX && ct->dwnd.h > 1)
            attrib |= (MULTILINE | HASBORDER);
        else if (ct->class == LISTBOX || ct->class == TEXTBOX)
            attrib |= HASBORDER;
        cwnd = CreateWindow(ct->class,
                        ct->dwnd.title,
                        ct->dwnd.x+GetClientLeft(wnd),
                        ct->dwnd.y+GetClientTop(wnd),
                        ct->dwnd.h,
                        ct->dwnd.w,
                        ct,
                        wnd,
                        ControlProc,
                        attrib);
        if ((ct->class == EDITBOX ||
                ct->class == COMBOBOX) &&
                    ct->itext != NULL)
            SendMessage(cwnd, SETTEXT, (PARAM) ct->itext, 0);
        if (ct->class != BOX &&
            ct->class != TEXT &&
                wnd->dFocus == NULL)
            wnd->dFocus = ct;
        ct++;
    }
    return rtn;
}

/* -------- LEFT_BUTTON Message --------- */
static BOOL LeftButtonMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    DBOX *db = wnd->extension;
    CTLWINDOW *ct = db->ctl;
    if (WindowSizing || WindowMoving)
        return TRUE;
    if (HitControlBox(wnd, p1-GetLeft(wnd), p2-GetTop(wnd))) {
        PostMessage(wnd, KEYBOARD, ' ', ALTKEY);
        return TRUE;
    }
    while (ct->class)    {
        WINDOW cwnd = ct->wnd;
        if (ct->class == COMBOBOX)    {
            if (p2 == GetTop(cwnd))    {
                if (p1 == GetRight(cwnd)+1)    {
                    SendMessage(cwnd, LEFT_BUTTON, p1, p2);
                    return TRUE;
                }
            }
            if (GetClass(inFocus) == LISTBOX)
                SendMessage(wnd, SETFOCUS, TRUE, 0);
        }
        else if (ct->class == SPINBUTTON)    {
            if (p2 == GetTop(cwnd))    {
                if (p1 == GetRight(cwnd)+1 ||
                        p1 == GetRight(cwnd)+2)    {
                    SendMessage(cwnd, LEFT_BUTTON, p1, p2);
                    return TRUE;
                }
            }
        }
        ct++;
    }
    return FALSE;
}

/* -------- KEYBOARD Message --------- */
static BOOL KeyboardMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    DBOX *db = wnd->extension;
    CTLWINDOW *ct = db->ctl;

    if (WindowMoving || WindowSizing)
        return FALSE;
    switch ((int)p1)    {
        case F1:
            ct = wnd->dFocus;
            if (ct != NULL)
                if (DisplayHelp(wnd, ct->help))
                    return TRUE;
            break;
        case SHIFT_HT:
        case BS:
        case UP:
            ChangeFocus(wnd, FALSE);
            break;
        case ALT_F6:
        case '\t':
        case FWD:
        case DN:
            ChangeFocus(wnd, TRUE);
            break;
        case ' ':
            if (((int)p2 & ALTKEY) &&
                    TestAttribute(wnd, CONTROLBOX))    {
                SysMenuOpen = TRUE;
                BuildSystemMenu(wnd);
            }
            break;
        case CTRL_F4:
        case ESC:
            SendMessage(wnd, COMMAND, ID_CANCEL, 0);
            break;
        default:
            /* ------ search all the shortcut keys ----- */
            dbShortcutKeys(db, (int) p1);
            break;
    }
    return wnd->Modal;
}

/* -------- COMMAND Message --------- */
static BOOL CommandMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    DBOX *db = wnd->extension;
    switch ((int) p1)    {
        case ID_OK:
        case ID_CANCEL:
            if ((int)p2 != 0)
                return TRUE;
            wnd->ReturnCode = (int) p1;
            if (wnd->Modal)
                PostMessage(wnd, ENDDIALOG, 0, 0);
            else
                SendMessage(wnd, CLOSE_WINDOW, TRUE, 0);
            return TRUE;
        case ID_HELP:
            if ((int)p2 != 0)
                return TRUE;
            return DisplayHelp(wnd, db->HelpName);
        default:
            break;
    }
    return FALSE;
}

/* ----- window-processing module, DIALOG window class ----- */
int DialogProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    DBOX *db = wnd->extension;

    switch (msg)    {
        case CREATE_WINDOW:
            return CreateWindowMsg(wnd, p1, p2);
        case SETFOCUS:
            if (wnd->Modal)    {
                if (p1)
                    SendMessage(inFocus, SETFOCUS, FALSE, 0);
                inFocus = p1 ? wnd : NULL;
                return TRUE;
            }
            break;
        case SHIFT_CHANGED:
            if (wnd->Modal)
                return TRUE;
            break;
        case LEFT_BUTTON:
            if (LeftButtonMsg(wnd, p1, p2))
                return TRUE;
            break;
        case KEYBOARD:
            if (KeyboardMsg(wnd, p1, p2))
                return TRUE;
            break;
        case CLOSE_POPDOWN:
            SysMenuOpen = FALSE;
            break;
        case LB_SELECTION:
        case LB_CHOOSE:
            if (SysMenuOpen)
                return TRUE;
            SendMessage(wnd, COMMAND, inFocusCommand(db), msg);
            break;
        case COMMAND:
            if (CommandMsg(wnd, p1, p2))
                return TRUE;
            break;
        case PAINT:
            p2 = TRUE;
            break;
        case CLOSE_WINDOW:
            if (!p1)    {
                SendMessage(wnd, COMMAND, ID_CANCEL, 0);
                return TRUE;
            }
            break;
        default:
            break;
    }
    return BaseWndProc(DIALOG, wnd, msg, p1, p2);
}

/* ------- create and execute a dialog box ---------- */
BOOL DialogBox(WINDOW wnd, DBOX *db, BOOL Modal,
  int (*wndproc)(struct window *, enum messages, PARAM, PARAM))
{
    BOOL rtn;
    int x = db->dwnd.x, y = db->dwnd.y;
    CTLWINDOW *ct;
    WINDOW oldFocus = inFocus;
    WINDOW DialogWnd;

    if (!Modal && wnd != NULL)    {
        x += GetLeft(wnd);
        y += GetTop(wnd);
    }
    DialogWnd = CreateWindow(DIALOG,
                        db->dwnd.title,
                        x, y,
                        db->dwnd.h,
                        db->dwnd.w,
                        db,
                        wnd,
                        wndproc,
                        Modal ? SAVESELF : 0);
    DialogWnd->Modal = Modal;
    SendMessage(inFocus, SETFOCUS, FALSE, 0);
    SendMessage(DialogWnd, SHOW_WINDOW, 0, 0);
    SendMessage(((CTLWINDOW *)(DialogWnd->dFocus))->wnd,
        SETFOCUS, TRUE, 0);
    SendMessage(DialogWnd, INITIATE_DIALOG, 0, 0);
    if (Modal)    {
        SendMessage(DialogWnd, CAPTURE_MOUSE, 0, 0);
        SendMessage(DialogWnd, CAPTURE_KEYBOARD, 0, 0);
        while (dispatch_message())
            ;
        rtn = DialogWnd->ReturnCode == ID_OK;
        SendMessage(DialogWnd, RELEASE_MOUSE, 0, 0);
        SendMessage(DialogWnd, RELEASE_KEYBOARD, 0, 0);
        SendMessage(inFocus, SETFOCUS, FALSE, 0);
        SendMessage(DialogWnd, CLOSE_WINDOW, TRUE, 0);
        SendMessage(oldFocus, SETFOCUS, TRUE, 0);
        if (rtn)    {
            ct = db->ctl;
            while (ct->class)    {
                ct->wnd = NULL;
                if (ct->class == RADIOBUTTON ||
                        ct->class == CHECKBOX)
                    ct->isetting = ct->setting;
                ct++;
            }
        }
        return rtn;
    }
    return FALSE;
}

/* ----- return command code of in-focus control window ---- */
static int inFocusCommand(DBOX *db)
{
    CTLWINDOW *ct = db->ctl;
    while (ct->class)    {
        if (ct->wnd == inFocus)
            return ct->command;
        ct++;
    }
    return -1;
}

/* -------- find a specified control structure ------- */
CTLWINDOW *FindCommand(DBOX *db, enum commands cmd, int class)
{
    CTLWINDOW *ct = db->ctl;
    while (ct->class)    {
        if (ct->class == class)
            if (cmd == ct->command)
                return ct;
        ct++;
    }
    return NULL;
}

/* ---- return the window handle of a specified command ---- */
WINDOW ControlWindow(DBOX *db, enum commands cmd)
{
    CTLWINDOW *ct = db->ctl;
    while (ct->class)    {
        if (ct->class != TEXT && cmd == ct->command)
            return ct->wnd;
        ct++;
    }
    return NULL;
}

/* ---- set a control ON or OFF ----- */
void ControlSetting(DBOX *db, enum commands cmd,
                                int class, int setting)
{
    CTLWINDOW *ct = FindCommand(db, cmd, class);
    if (ct != NULL)
        ct->isetting = setting;
}

/* ---- return pointer to the text of a control window ---- */
char *GetDlgTextString(DBOX *db,enum commands cmd,CLASS class)
{
    CTLWINDOW *ct = FindCommand(db, cmd, class);
    if (ct != NULL)
        return ct->itext;
    else
        return NULL;
}

/* ------- set the text of a control specification ------ */
void SetDlgTextString(DBOX *db, enum commands cmd,
                                    char *text, CLASS class)
{
    CTLWINDOW *ct = FindCommand(db, cmd, class);
    if (ct != NULL)    {
        ct->itext = realloc(ct->itext, strlen(text)+1);
        if (ct->itext != NULL)
            strcpy(ct->itext, text);
    }
}

/* ------- set the text of a control window ------ */
void PutItemText(WINDOW wnd, enum commands cmd, char *text)
{
    CTLWINDOW *ct = FindCommand(wnd->extension, cmd, EDITBOX);

    if (ct == NULL)
        ct = FindCommand(wnd->extension, cmd, TEXTBOX);
    if (ct == NULL)
        ct = FindCommand(wnd->extension, cmd, COMBOBOX);
    if (ct == NULL)
        ct = FindCommand(wnd->extension, cmd, LISTBOX);
    if (ct == NULL)
        ct = FindCommand(wnd->extension, cmd, SPINBUTTON);
    if (ct == NULL)
        ct = FindCommand(wnd->extension, cmd, TEXT);
    if (ct != NULL)        {
        WINDOW cwnd = (WINDOW) (ct->wnd);
        switch (ct->class)    {
            case COMBOBOX:
            case EDITBOX:
                SendMessage(cwnd, CLEARTEXT, 0, 0);
                SendMessage(cwnd, ADDTEXT, (PARAM) text, 0);
                if (!isMultiLine(cwnd))
                    SendMessage(cwnd, PAINT, 0, 0);
                break;
            case LISTBOX:
            case TEXTBOX:
            case SPINBUTTON:
                SendMessage(cwnd, ADDTEXT, (PARAM) text, 0);
                break;
            case TEXT:    {
                SendMessage(cwnd, CLEARTEXT, 0, 0);
                SendMessage(cwnd, ADDTEXT, (PARAM) text, 0);
                SendMessage(cwnd, PAINT, 0, 0);
                break;
            }
            default:
                break;
        }
    }
}

/* ------- get the text of a control window ------ */
void GetItemText(WINDOW wnd, enum commands cmd,
                                char *text, int len)
{
    CTLWINDOW *ct = FindCommand(wnd->extension, cmd, EDITBOX);
    unsigned char *cp;

    if (ct == NULL)
        ct = FindCommand(wnd->extension, cmd, COMBOBOX);
    if (ct == NULL)
        ct = FindCommand(wnd->extension, cmd, TEXTBOX);
    if (ct == NULL)
        ct = FindCommand(wnd->extension, cmd, TEXT);
    if (ct != NULL)    {
        WINDOW cwnd = (WINDOW) (ct->wnd);
        if (cwnd != NULL)    {
            switch (ct->class)    {
                case TEXT:
                    if (GetText(cwnd) != NULL)    {
                        cp = strchr(GetText(cwnd), '\n');
                        if (cp != NULL)
                            len = (int) (cp - GetText(cwnd));
                        strncpy(text, GetText(cwnd), len);
                        *(text+len) = '\0';
                    }
                    break;
                case TEXTBOX:
                    if (GetText(cwnd) != NULL)
                        strncpy(text, GetText(cwnd), len);
                    break;
                case COMBOBOX:
                case EDITBOX:
                    SendMessage(cwnd,GETTEXT,(PARAM)text,len);
                    break;
                default:
                    break;
            }
        }
    }
}

/* ------- set the text of a listbox control window ------ */
void GetDlgListText(WINDOW wnd, char *text, enum commands cmd)
{
    CTLWINDOW *ct = FindCommand(wnd->extension, cmd, LISTBOX);
    int sel = SendMessage(ct->wnd, LB_CURRENTSELECTION, 0, 0);
    SendMessage(ct->wnd, LB_GETTEXT, (PARAM) text, sel);
}

/* -- find control structure associated with text control -- */
static CTLWINDOW *AssociatedControl(DBOX *db,enum commands Tcmd)
{
    CTLWINDOW *ct = db->ctl;
    while (ct->class)    {
        if (ct->class != TEXT)
            if (ct->command == Tcmd)
                break;
        ct++;
    }
    return ct;
}

/* --- process dialog box shortcut keys --- */
static void dbShortcutKeys(DBOX *db, int ky)
{
    CTLWINDOW *ct;
    int ch = AltConvert(ky);

    if (ch != 0)    {
        ct = db->ctl;
        while (ct->class)    {
            char *cp = ct->itext;
            while (cp && *cp)    {
                if (*cp == SHORTCUTCHAR &&
                            tolower(*(cp+1)) == ch)    {
                    if (ct->class == TEXT)
                        ct = AssociatedControl(db, ct->command);
                    if (ct->class == RADIOBUTTON)
                        SetRadioButton(db, ct);
                    else if (ct->class == CHECKBOX)    {
                        ct->setting ^= ON;
                        SendMessage(ct->wnd, PAINT, 0, 0);
                    }
                    else if (ct->class)    {
                        SendMessage(ct->wnd, SETFOCUS, TRUE, 0);
                        if (ct->class == BUTTON)
                           SendMessage(ct->wnd,KEYBOARD,'\r',0);
                    }
                    return;
                }
                cp++;
            }
            ct++;
        }
    }
}

/* --- dynamically add or remove scroll bars
                            from a control window ---- */
void SetScrollBars(WINDOW wnd)
{
    int oldattr = GetAttribute(wnd);
    if (wnd->wlines > ClientHeight(wnd))
        AddAttribute(wnd, VSCROLLBAR);
    else
        ClearAttribute(wnd, VSCROLLBAR);
    if (wnd->textwidth > ClientWidth(wnd))
        AddAttribute(wnd, HSCROLLBAR);
    else
        ClearAttribute(wnd, HSCROLLBAR);
    if (GetAttribute(wnd) != oldattr)
        SendMessage(wnd, BORDER, 0, 0);
}

/* ------- CREATE_WINDOW Message (Control) ----- */
static void CtlCreateWindowMsg(WINDOW wnd)
{
    CTLWINDOW *ct;
    ct = wnd->ct = wnd->extension;
    wnd->extension = NULL;
    if (ct != NULL)
        ct->wnd = wnd;
}

/* ------- KEYBOARD Message (Control) ----- */
static BOOL CtlKeyboardMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    CTLWINDOW *ct = GetControl(wnd);
    switch ((int) p1)    {
        case F1:
            if (WindowMoving || WindowSizing)
                break;
            if (!DisplayHelp(wnd, ct->help))
                SendMessage(GetParent(wnd),COMMAND,ID_HELP,0);
            return TRUE;
        case ' ':
            if (!((int)p2 & ALTKEY))
                break;
        case ALT_F6:
        case CTRL_F4:
        case ALT_F4:
            PostMessage(GetParent(wnd), KEYBOARD, p1, p2);
            return TRUE;
        default:
            break;
    }
    if (GetClass(wnd) == EDITBOX)
        if (isMultiLine(wnd))
            return FALSE;
    switch ((int) p1)    {
        case UP:
            if (!isDerivedFrom(wnd, LISTBOX))    {
                p1 = CTRL_FIVE;
                p2 = LEFTSHIFT;
            }
            break;
        case BS:
            if (!isDerivedFrom(wnd, EDITBOX))    {
                p1 = CTRL_FIVE;
                p2 = LEFTSHIFT;
            }
            break;
        case DN:
            if (!isDerivedFrom(wnd, LISTBOX) &&
                    !isDerivedFrom(wnd, COMBOBOX))
                p1 = '\t';
            break;
        case FWD:
            if (!isDerivedFrom(wnd, EDITBOX))
                p1 = '\t';
            break;
        case '\r':
            if (isDerivedFrom(wnd, EDITBOX))
                if (isMultiLine(wnd))
                    break;
            if (isDerivedFrom(wnd, BUTTON))
                break;
            SendMessage(GetParent(wnd), COMMAND, ID_OK, 0);
            return TRUE;
        default:
            break;
    }
    return FALSE;
}

/* ------- CLOSE_WINDOW Message (Control) ----- */
static void CtlCloseWindowMsg(WINDOW wnd)
{
    CTLWINDOW *ct = GetControl(wnd);
    if (ct != NULL)    {
        if (GetParent(wnd)->ReturnCode == ID_OK &&
                (ct->class == EDITBOX ||
                    ct->class == COMBOBOX))    {
            if (wnd->TextChanged)    {
                ct->itext=realloc(ct->itext,strlen(wnd->text)+1);
                strcpy(ct->itext, wnd->text);
                if (!isMultiLine(wnd))    {
                    char *cp = ct->itext+strlen(ct->itext)-1;
                    if (*cp == '\n')
                        *cp = '\0';
                }
            }
        }
    }
}

/* -- generic window processor used by dialog box controls -- */
static int ControlProc(WINDOW wnd,MESSAGE msg,PARAM p1,PARAM p2)
{
    DBOX *db;
    CTLWINDOW *ct;

    if (wnd == NULL)
        return FALSE;
    db = GetParent(wnd) ? GetParent(wnd)->extension : NULL;
    ct = GetControl(wnd);

    switch (msg)    {
        case CREATE_WINDOW:
            CtlCreateWindowMsg(wnd);
            break;
        case KEYBOARD:
            if (CtlKeyboardMsg(wnd, p1, p2))
                return TRUE;
            break;
        case PAINT:
            if (GetClass(wnd) == EDITBOX ||
                    GetClass(wnd) == LISTBOX ||
                        GetClass(wnd) == TEXTBOX)
                SetScrollBars(wnd);
            break;
        case BORDER:
            if (GetClass(wnd) == EDITBOX)    {
                WINDOW oldFocus = inFocus;
                inFocus = NULL;
                DefaultWndProc(wnd, msg, p1, p2);
                inFocus = oldFocus;
                return TRUE;
            }
            break;
        case SETFOCUS:
            if (p1)    {
                DefaultWndProc(wnd, msg, p1, p2);
                GetParent(wnd)->dFocus = ct;
                SendMessage(GetParent(wnd), COMMAND,
                    inFocusCommand(db), ENTERFOCUS);
                return TRUE;
            }
            else
                SendMessage(GetParent(wnd), COMMAND,
                    inFocusCommand(db), LEAVEFOCUS);
            break;
        case CLOSE_WINDOW:
            CtlCloseWindowMsg(wnd);
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}

/* ---- change the focus to the next or previous control --- */
static void ChangeFocus(WINDOW wnd, int direc)
{
    DBOX *db = wnd->extension;
     CTLWINDOW *ct = db->ctl;
     CTLWINDOW *ctt;

    /* --- find the control that has the focus --- */
    while (ct->class)    {
        if (ct == wnd->dFocus)
            break;
        ct++;
    }
    if (ct->class)    {
        ctt = ct;
        do    {
            /* ----- point to next or previous control ----- */
            if (direc)    {
                ct++;
                if (ct->class == 0)
                    ct = db->ctl;
            }
            else    {
                if (ct == db->ctl)
                    while (ct->class)
                        ct++;
                --ct;
            }

            if (ct->class != BOX && ct->class != TEXT)    {
                SendMessage(ct->wnd, SETFOCUS, TRUE, 0);
                SendMessage(ctt->wnd, PAINT, 0, 0);
                SendMessage(ct->wnd, PAINT, 0, 0);
                break;
            }
        } while (ct != ctt);
    }
}

void SetFocusCursor(WINDOW wnd)
{
    if (wnd == inFocus)    {
        SendMessage(NULL, SHOW_CURSOR, 0, 0);
        SendMessage(wnd, KEYBOARD_CURSOR, 1, 0);
    }
}
```

Copyright © 1992, Dr. Dobb's Journal
