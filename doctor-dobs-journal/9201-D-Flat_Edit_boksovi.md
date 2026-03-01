
# C PROGRAMMING

## D-Flat Edit Boxes - Al Stevens

Prošlog meseca sam opisao klasu prozora D-Flat TEKSTBOKS, osnovnu klasu za izvedene klase prozora koje prikazuju tekst. Klasa TEKSTBOKS se ne bavi manipulacijom ili značenjem teksta koji čuva. Umesto toga, on samo pruža sredstva za čuvanje teksta, njegovo prikazivanje i skrolovanje i listanje kroz njega. Druge klase prozora dodaju funkcionalnost tekstu i koriste osnovnu klasu TEKSTBOKS da podrže procese upravljanja tekstom niskog nivoa. Ovog meseca raspravljamo o jednoj takvoj klasi prozora, klasi EDITBOKS.

### D-Flat EDITBOX klasa

Klasa EDITBOX dodaje funkcije uređivača teksta klasi TEKSTBOKS. EDITBOKS podržava korisnikov unos i modifikovanje teksta obradom pritisaka na tastere i komandi za uređivanje teksta. [LISTING_ONE](#listing_one), je "editbox.c", izvorna datoteka koja implementira klasu prozora EDITBOX. Program se sastoji od modula za obradu prozora, nazvanog EditBokProc, i nekoliko drugih funkcija koje obrađuju D-Flat poruke koje EditBoxProc presreće.

Funkcija CreateVindovMsg obrađuje poruku CREATE_VINDOV i to je prva funkcija koju vidite u izvornoj datoteci. Većina funkcija ima sličan naziv i ima komentare koji identifikuju poruku koju obrađuju. Neke funkcije obrađuju podskupove KOMANDNE poruke i imaju imena i komentare koji povezuju funkciju sa KOMANDOM. Ja ću se obratiti svakoj poruci i komandi i verovati da ćete moći da pronađete ispravnu funkciju koja je obrađuje.

Poruka CREATE_WINDOW postavlja početne vrednosti u polja okvira za uređivanje prozora. On uspostavlja podrazumevanu maksimalnu dužinu teksta i postavlja početni prazan bafer. Poruka SETTEXT dozvoljava aplikacijskom programu da navede bafer teksta za okvir za uređivanje. Poruka osigurava da dužina teksta ne prelazi maksimalnu dužinu naznačenu za okvir za uređivanje. Zatim prosleđuje poruku modulu za obradu prozora za klasu TEKSTBOX preko BaseVndProc funkcije.

Poruka ADDTEKST dodaje red teksta u prozor TEKSTBOKS. Kada je prozor takođe klasa EDITBOKS, poruka prvo proverava da bi se uverila da dužina bafera sa dodatim novim tekstom neće premašiti maksimalnu dužinu teksta za prozor. Zatim program prosleđuje poruku modulu za obradu prozora klase TEKSTBOX.

Nakon toga, ako je EDITBOX okvir za uređivanje u jednom redu – što je tipično za mnoga polja za unos podataka u okvirima za dijalog – program postavlja pokazivač trenutne kolone na kraj teksta i označava blok koji obuhvata ceo tekst u okviru za uređivanje. Ovaj blok podržava CUA konvenciju gde su jednoredni okviri za uređivanje označeni kada korisnik prvi put pređe na polje. Zatim, ako korisnik počne da kuca, blok se briše. Ako korisnik prvi pomeri kursor, tekst ostaje, a oznaka bloka se uklanja.

Poruka GETTEKST kopira tekst iz tekstualnog bafera polja za uređivanje u prostor na koji ukazuje prvi parametar pošiljaoca poruke.

Poruka SETTEKSTLENGTH postavlja maksimalnu dužinu teksta za okvir za uređivanje.

Aplikacije šalju poruku KEIBOARD_CURSOR da bi pomerile kursor na novu poziciju. Kao i delovi D-Flat-a. Sama klasa EDITBOKS pomera kursor pomoću poruke KEIBOARD_CURSOR. Na kraju, poruka ulazi u kod u message.c, o čemu sam raspravljao prošlog jula, da fizički pomeri kursor. Prvo, međutim, klasa EDITBOKS treba da ažurira svoje pokazivače koji određuju gde se nalazi kursor u odnosu na tekstualni bafer i prozor. Zatim program mora da se uveri da će kursor biti u vidokrugu – da prozor ima fokus, da je vidljiv i da je njegov deo gde se kursor pomera na ekranu, unutar granica bilo kog prethodnika prozora, i da se ne preklapa sa drugim prozorom. Ako su svi ovi uslovi tačni, program šalje SHOV_CURSOR poruku sistemu. U suprotnom, šalje poruku HIDE_CURSOR.

Poruke SETFOCUS, PAINT i MOVE same prolaze u klasu osnovnog prozora i zatim šalju prozoru poruku KEIBOARD_CURSOR. Ovaj proces osigurava da je kursor ispravno postavljen kada se prozor EDITBOKS-a oslikava ili pomera i da se uključuje kada prozor dođe u fokus i isključuje se kada prozor napusti fokus.

Poruka SIZE sama prosleđuje osnovnu klasu, a zatim se uverava da operacija veličine nije izazvala da pozicija kursora pređe nove granice prozora. Ako jeste, program prilagođava poziciju kursora da ostane unutar prozora.

Poruke SCROLL, HORIZSCROLL, SCROLLPAGE i HORIZSCROLLPAGE prvo prolaze u osnovnu klasu da bi izvršile operaciju pomeranja ili stranica. Klasa TEKSTBOKS održava promenljive koje označavaju red teksta u baferu na vrhu prozora i kolonu teksta na levoj margini. Ove varijable se menjaju kako se tekst pomera i stranice horizontalno i vertikalno. Pomeranje je pomeranje teksta unutar prozora jedan po red ili poziciju znaka. Straničenje ga pomera za jednu visinu ili širinu prozora u isto vreme. Klasa EDITBOKS mora da presreće poruke za pomeranje i pejdžing da bi zadržala kursor na tastaturi unutar granica prozora. Ako prozor skroluje poziciju kursora tastature van prozora, ovi presretci će premestiti kursor tako da ostane u vidnom polju.

Poruka LEFT_BUTTON pomera kursor tastature na mesto gde je pozicioniran kursor miša. Ako korisnik pozicionira miš tamo gde nema teksta – na primer, iza kraja reda ili ispod poslednjeg reda u baferu – program postavlja kursor tastature što je bliže moguće na poziciju važećeg znaka.

Ako je program u procesu obeležavanja teksta, poruka LEFT_BUTTON nema efekta osim ako se kursor miša ne nalazi na ivici. Ako je tako, program pomera prozor u jednom od četiri pravca u zavisnosti od toga u kojoj se ivici nalazi miš. Zatim proširuje označeni blok za jednu liniju ili kolonu da bi odrazio kretanje. Ovaj proces omogućava korisniku da označi tekstualne blokove pomoću miša gde veličina bloka prelazi veličinu prozora. Prevlačenjem miša u ivicu i držanjem dugmeta, korisnik skroluje prozor i nastavlja da obeležava blok.

Klasa EDITBOKS prima poruku MOUSE_MOVED kad god korisnik pomeri miša i njen kursor je unutar prozora okvira za uređivanje. Ako je dugme miša dole, korisnik označava tekst u polju za uređivanje. Program utvrđuje poziciju miša kada korisnik pritisne dugme kao sidrenu tačku bloka. Sada, sve dok korisnik drži dugme i pomera miš, blok će biti definisan u opsegu od tačke sidrenja do sadašnje pozicije miša. Ova poruka šalje poruku MOUSE_TRAVEL da ograniči kretanje miša unutar prozora okvira za uređivanje. Prozor će držati miš ograničen na taj način sve dok korisnik ne otpusti dugme.

Poruka BUTTON_RELEASED stiže kada korisnik otpusti dugme miša. Ako je korisnik označavao tekst mišem, onda program resetuje režim označavanja teksta, otpušta ograničenje kretanja miša i podešava markere bloka tako da donji red i kolona definišu početak bloka.

Poruka KEIBOARD počinje u funkciji KeiboardMsg i razlaže se na još nekoliko poziva funkcija. Sama poruka počinje ignorisanjem svih tastera ako korisnik pomera ili menja veličinu prozora ili drži pritisnut taster Alt. Te tastere i određene kombinacije Ctrl-tastera će obraditi klase prozora više u hijerarhiji osnovnih klasa.

Funkcija DoMultiLines brine o pokretanju obeležavanja bloka. Ako okvir za uređivanje ima atribut MULTILINE, a korisnik drži pritisnut taster shift dok kuca taster koji pomera kursor, a obeležavanje tastature nije počelo, program stavlja prozor u režim označavanja teksta i postavlja trenutnu poziciju kursora na tastaturi kao sidrenu tačku za označeni blok.

Funkcija DoScrolling obrađuje sve tastere koji će se pomerati ili listati kroz tekst. Neke od ovih ključeva može obraditi osnovna klasa TEKSTBOKS. Drugi se obrađuju kao rezultat kombinacije osnovne klase i funkcija jedinstvenih za klasu EDITBOKS. Funkcije Home, End, NektVord, PrevVord, Upvard, Dovnvard, Forvard i Backvard pomeraju kursor tastature i pokazivače teksta. Ako postoji označen blok, a prozor nije u režimu označavanja teksta – što znači da je korisnik otpustio taster shift ili dugme miša – ova funkcija uklanja oznaku bloka.

Ako funkcija KeiboardMsg vidi da korisnik označava blok pomoću tastature i DoScrolling funkcija je prijavila da je zaista obrađivala taster za pomeranje ili pejdžing, funkcija KeiboardMsg poziva EktendBlock da proširi označeni blok na novu lokaciju kursora tastature.

Ako DoScrolling prijavi da ključ nije taster za pomeranje ili pejdžing, onda KeiboardMsg poziva DoKeiStroke da obradi ukucani taster. DoKeiStroke obrađuje tastere Rubout, Del, Tab, Shift+Tab i Enter. Pomera kursor ulevo za jednu poziciju za taster Rubout, a zatim se spušta u kod da obradi taster Del, koji poziva funkciju DelKei. DelKei funkcija briše ključ iz tekstualnog bafera na poziciji na koju pokazuje kursor na tastaturi. DoKeiStroke poziva funkciju TabKei za obradu tastera Tab i Shift+Tab. Za taster tab, funkcija šalje poruke TASTATURA sa ili razmakom ili FVD karakterom u zavisnosti od načina umetanja prozora. Ako je okvir za uređivanje jednolinijski, funkcija prosleđuje znak Tab ili Shift+Tab roditelju prozora. Roditelj dijaloga će koristiti ovaj znak da pomeri fokus iz kontrole okvira za uređivanje.

DoKeiStroke prosleđuje sve ostale ključeve, koji će biti prikazane vrednosti, funkciji KeiTiped. Prvo, međutim, ako korisnik unese ključ dok je blok označen, program briše blok koji će biti zamenjen ukucanim ključem. Funkcija KeiTiped ubacuje ključ u tekstualni bafer i vrši operaciju prelamanja reči kada kursor na tastaturi dostigne desnu marginu prozora.

Poruka SHIFT_CHANGED prati da korisnik otpusti taster shift dok tastaturom označava blok teksta. Kada se to dogodi, program poziva StopMarking da bi izveo prozor iz režima označavanja teksta.

COMMAND poruka obrađuje komande prozoru sa drugih mesta. Prozor obično prima KOMANDNU poruku kada korisnik izvrši izbor menija, iako ništa ne sprečava bilo koji deo programa da pošalje KOMANDNU poruku. U stvari, klasa EDITBOKS koristi sopstvenu ID_DELETETEKST komandu za brisanje blokova teksta kada korisnik uradi nešto izvan menija što bi izazvalo brisanje teksta.

Klasa EDITBOKS obrađuje pet komandi. Svih pet se nalazi u meniju Uredi u primeru aplikacije Memopad. Komande menija odgovaraju standardnim stavkama menija navedenim u SAA/CUA specifikaciji. Bez obzira da li vaša aplikacija implementira meni na isti način ili ne, većini okvira za uređivanje će biti potrebne neke ili sve ove funkcije.

Komanda ID_DELETETEKST briše označeni blok teksta. D-Flat održava bafer izbrisanog teksta tako da korisnik može da poništi poslednje brisanje. Komanda ID_DELETETEKST prvo poziva SaveDeletedTekt da bi kopirala označeni blok u bafer za poništavanje. Zatim pomera tekstualni bafer sa jednog iza kraja bloka na početak bloka i ažurira tekstualne pokazivače prozora.

Komanda ID_CLEAR je slična komandi ID_DELETETEKST osim što briše tekst, ostavljajući otvoren prostor. Program mora da obriše linije u označenom bloku, ostavljajući znakove novog reda na mestu.

Komanda ID_UNDO umeće sadržaj izbrisanog tekstualnog bafera u aktivni tekstualni bafer na trenutnoj poziciji kursora.

Komanda ID_PARAGRAPH formira novi pasus iz označenog bloka ako postoji. Ako nije, komanda formira pasus od trenutne lokacije kursora do kraja pasusa. Tekst u polju za uređivanje se sastoji od redova sa završnim znakovima novog reda. Paragraf je grupa redova do sledećeg praznog reda.

Poruka CLOSE_VINDOV sakriva kursor i oslobađa bafer za poništavanje izbrisanog teksta ako postoji.

#### LISTING_ONE

```c
/* ------------- editbox.c ------------ */
#include "dflat.h"

#define EditBufLen(wnd) (isMultiLine(wnd) ? EDITLEN : ENTRYLEN)
#define SetLinePointer(wnd, ln) (wnd->CurrLine = ln)
#define isWhite(c)     ((c)==' '||(c)=='\n')
/* ---------- local prototypes ----------- */
static void SaveDeletedText(WINDOW, char *, int);
static void Forward(WINDOW);
static void Backward(WINDOW);
static void End(WINDOW);
static void Home(WINDOW);
static void Downward(WINDOW);
static void Upward(WINDOW);
static void StickEnd(WINDOW);
static void NextWord(WINDOW);
static void PrevWord(WINDOW);
static void ResetEditBox(WINDOW);
static void ModTextPointers(WINDOW, int, int);
/* -------- local variables -------- */
static int KeyBoardMarking, ButtonDown;
static int TextMarking;
static int ButtonX, ButtonY;
static int PrevY = -1;
/* ----------- CREATE_WINDOW Message ---------- */
static int CreateWindowMsg(WINDOW wnd)
{
    int rtn = BaseWndProc(EDITBOX, wnd, CREATE_WINDOW, 0, 0);
    wnd->MaxTextLength = MAXTEXTLEN+1;
    wnd->textlen = EditBufLen(wnd);
    wnd->InsertMode = TRUE;
    ResetEditBox(wnd);
    return rtn;
}
/* ----------- SETTEXT Message ---------- */
static int SetTextMsg(WINDOW wnd, PARAM p1)
{
    int rtn = FALSE;
    if (strlen((char *)p1) <= wnd->MaxTextLength)    {
        rtn = BaseWndProc(EDITBOX, wnd, SETTEXT, p1, 0);
        wnd->CurrLine = 0;
    }
    return rtn;
}
/* ----------- ADDTEXT Message ---------- */
static int AddTextMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int rtn = FALSE;
    if (strlen((char *)p1)+wnd->textlen <= wnd->MaxTextLength) {
        rtn = BaseWndProc(EDITBOX, wnd, ADDTEXT, p1, p2);
        if (rtn != FALSE)    {
            if (!isMultiLine(wnd))    {
                wnd->CurrLine = 0;
                wnd->CurrCol = strlen((char *)p1);
                if (wnd->CurrCol >= ClientWidth(wnd))    {
                    wnd->wleft = wnd->CurrCol-ClientWidth(wnd);
                    wnd->CurrCol -= wnd->wleft;
                }
                wnd->BlkEndCol = wnd->CurrCol;
                SendMessage(wnd, KEYBOARD_CURSOR,
                                     WndCol, wnd->WndRow);
            }
        }
    }
    return rtn;
}
/* ----------- GETTEXT Message ---------- */
static int GetTextMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    char *cp1 = (char *)p1;
    char *cp2 = wnd->text;
    if (cp2 != NULL)    {
        while (p2-- && *cp2 && *cp2 != '\n')
            *cp1++ = *cp2++;
        *cp1 = '\0';
        return TRUE;
    }
    return FALSE;
}
/* ----------- SETTEXTLENGTH Message ---------- */
static int SetTextLengthMsg(WINDOW wnd, unsigned int len)
{
    if (++len < MAXTEXTLEN)    {
        wnd->MaxTextLength = len;
        if (len < wnd->textlen)    {
            if ((wnd->text=realloc(wnd->text, len+2)) != NULL) {
                wnd->textlen = len;
                *((wnd->text)+len) = '\0';
                *((wnd->text)+len+1) = '\0';
                BuildTextPointers(wnd);
            }
        }
        return TRUE;
    }
    return FALSE;
}
/* ----------- KEYBOARD_CURSOR Message ---------- */
static int KeyboardCursorMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int rtn;
    wnd->CurrCol = (int)p1 + wnd->wleft;
    wnd->WndRow = (int)p2;
    wnd->CurrLine = (int)p2 + wnd->wtop;
    rtn = BaseWndProc(EDITBOX, wnd, KEYBOARD_CURSOR, p1, p2);
    if (wnd == inFocus && CharInView(wnd, (int)p1, (int)p2))
        SendMessage(NULL, SHOW_CURSOR, wnd->InsertMode, 0);
    else SendMessage(NULL, HIDE_CURSOR, 0, 0);
    return rtn;
}
/* ----------- SIZE Message ---------- */
int SizeMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int rtn = BaseWndProc(EDITBOX, wnd, SIZE, p1, p2);
    if (WndCol > ClientWidth(wnd)-1)
        wnd->CurrCol = ClientWidth(wnd)-1 + wnd->wleft;
    if (wnd->WndRow > ClientHeight(wnd)-1)    {
        wnd->WndRow = ClientHeight(wnd)-1;
        SetLinePointer(wnd, wnd->WndRow+wnd->wtop);
    }
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    return rtn;
}
/* ----------- SCROLL Message ---------- */
static int ScrollMsg(WINDOW wnd, PARAM p1)
{
    int rtn = FALSE;
    if (isMultiLine(wnd))    {
        rtn = BaseWndProc(EDITBOX,wnd,SCROLL,p1,0);
        if (rtn != FALSE)    {
            if (p1)    {
                /* -------- scrolling up --------- */
                if (wnd->WndRow == 0)    {
                    wnd->CurrLine++;
                    StickEnd(wnd);
                }
                else
                    --wnd->WndRow;
            }
            else    {
                /* -------- scrolling down --------- */
                if (wnd->WndRow == ClientHeight(wnd)-1)    {
                    if (wnd->CurrLine > 0)
                        --wnd->CurrLine;
                    StickEnd(wnd);
                }
                else
                    wnd->WndRow++;
            }
            SendMessage(wnd,KEYBOARD_CURSOR,WndCol,wnd->WndRow);
        }
    }
    return rtn;
}
/* ----------- HORIZSCROLL Message ---------- */
static int HorizScrollMsg(WINDOW wnd, PARAM p1)
{
    int rtn = FALSE;
    char *currchar = CurrChar;
    if (!(p1 &&
            wnd->CurrCol == wnd->wleft && *currchar == '\n'))  {
        rtn = BaseWndProc(EDITBOX, wnd, HORIZSCROLL, p1, 0);
        if (rtn != FALSE)    {
            if (wnd->CurrCol < wnd->wleft)
                wnd->CurrCol++;
            else if (WndCol == ClientWidth(wnd))
                --wnd->CurrCol;
            SendMessage(wnd,KEYBOARD_CURSOR,WndCol,wnd->WndRow);
        }
    }
    return rtn;
}
/* ----------- SCROLLPAGE Message ---------- */
static int ScrollPageMsg(WINDOW wnd, PARAM p1)
{
    int rtn = FALSE;
    if (isMultiLine(wnd))    {
        rtn = BaseWndProc(EDITBOX, wnd, SCROLLPAGE, p1, 0);
        SetLinePointer(wnd, wnd->wtop+wnd->WndRow);
        StickEnd(wnd);
        SendMessage(wnd, KEYBOARD_CURSOR,WndCol, wnd->WndRow);
    }
    return rtn;
}
/* ----------- HORIZSCROLLPAGE Message ---------- */
static int HorizPageMsg(WINDOW wnd, PARAM p1)
{
    int rtn = BaseWndProc(EDITBOX, wnd, HORIZPAGE, p1, 0);
    if ((int) p1 == FALSE)    {
        if (wnd->CurrCol > wnd->wleft+ClientWidth(wnd)-1)
            wnd->CurrCol = wnd->wleft+ClientWidth(wnd)-1;
    }
    else if (wnd->CurrCol < wnd->wleft)
        wnd->CurrCol = wnd->wleft;
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    return rtn;
}
/* ----- Extend the marked block to the new x,y position ---- */
static void ExtendBlock(WINDOW wnd, int x, int y)
{
    int bbl, bel;
    int ptop = min(wnd->BlkBegLine, wnd->BlkEndLine);
    int pbot = max(wnd->BlkBegLine, wnd->BlkEndLine);
    char *lp = TextLine(wnd, wnd->wtop+y);
    int len = (int) (strchr(lp, '\n') - lp);
    x = min(x, len-wnd->wleft);
    wnd->BlkEndCol = x+wnd->wleft;
    wnd->BlkEndLine = y+wnd->wtop;
    bbl = min(wnd->BlkBegLine, wnd->BlkEndLine);
    bel = max(wnd->BlkBegLine, wnd->BlkEndLine);
    while (ptop < bbl)    {
        WriteTextLine(wnd, NULL, ptop, FALSE);
        ptop++;
    }
    for (y = bbl; y <= bel; y++)
        WriteTextLine(wnd, NULL, y, FALSE);
    while (pbot > bel)    {
        WriteTextLine(wnd, NULL, pbot, FALSE);
        --pbot;
    }
}
/* ----------- LEFT_BUTTON Message ---------- */
static int LeftButtonMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int MouseX = (int) p1 - GetClientLeft(wnd);
    int MouseY = (int) p2 - GetClientTop(wnd);
    RECT rc = ClientRect(wnd);
    char *lp;
    int len;
    if (KeyBoardMarking)
        return TRUE;
    if (WindowMoving || WindowSizing)
        return FALSE;
    if (isMultiLine(wnd))    {
        if (TextMarking)    {
            if (!InsideRect(p1, p2, rc))    {
                if ((int)p1 == GetLeft(wnd))
                    if (SendMessage(wnd, HORIZSCROLL, 0, 0))
                        ExtendBlock(wnd, MouseX-1, MouseY);
                if ((int)p1 == GetRight(wnd))
                    if (SendMessage(wnd, HORIZSCROLL, TRUE, 0))
                        ExtendBlock(wnd, MouseX+1, MouseY);
                if ((int)p2 == GetTop(wnd))
                    if (SendMessage(wnd, SCROLL, FALSE, 0))
                        ExtendBlock(wnd, MouseX, MouseY+1);
                if ((int)p2 == GetBottom(wnd))
                    if (SendMessage(wnd, SCROLL, TRUE, 0))
                        ExtendBlock(wnd, MouseX, MouseY-1);
                SendMessage(wnd, PAINT, 0, 0);
            }
            return TRUE;
        }
        if (!InsideRect(p1, p2, rc))
            return FALSE;
        if (TextBlockMarked(wnd))    {
            ClearTextBlock(wnd);
            SendMessage(wnd, PAINT, 0, 0);
        }
        if (wnd->wlines)    {
            if (MouseY > wnd->wlines-1)
                return TRUE;
            lp = TextLine(wnd, MouseY+wnd->wtop);
            len = (int) (strchr(lp, '\n') - lp);
            MouseX = min(MouseX, len);
            if (MouseX < wnd->wleft)    {
                MouseX = 0;
                SendMessage(wnd, KEYBOARD, HOME, 0);
            }
            ButtonDown = TRUE;
            ButtonX = MouseX;
            ButtonY = MouseY;
        }
        else
            MouseX = MouseY = 0;
        wnd->WndRow = MouseY;
        SetLinePointer(wnd, MouseY+wnd->wtop);
    }
    if (isMultiLine(wnd) ||
        (!TextBlockMarked(wnd)
            && MouseX+wnd->wleft < strlen(wnd->text)))
        wnd->CurrCol = MouseX+wnd->wleft;
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    return TRUE;
}
/* ----------- MOUSE_MOVED Message ---------- */
static int MouseMovedMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int MouseX = (int) p1 - GetClientLeft(wnd);
    int MouseY = (int) p2 - GetClientTop(wnd);
    RECT rc = ClientRect(wnd);
    if (!InsideRect(p1, p2, rc))
        return FALSE;
    if (MouseY > wnd->wlines-1)
        return FALSE;
    if (ButtonDown)    {
        SetAnchor(wnd, ButtonX+wnd->wleft, ButtonY+wnd->wtop);
        TextMarking = TRUE;
        SendMessage(NULL,MOUSE_TRAVEL,
                (PARAM)&WindowRect(wnd),0);
        ButtonDown = FALSE;
    }
    if (TextMarking && !(WindowMoving || WindowSizing))    {
        ExtendBlock(wnd, MouseX, MouseY);
        return TRUE;
    }
    return FALSE;
}
static void StopMarking(WINDOW wnd)
{
    TextMarking = FALSE;
    if (wnd->BlkBegLine > wnd->BlkEndLine)    {
        swap(wnd->BlkBegLine, wnd->BlkEndLine);
        swap(wnd->BlkBegCol, wnd->BlkEndCol);
    }
    if (wnd->BlkBegLine == wnd->BlkEndLine &&
            wnd->BlkBegCol > wnd->BlkEndCol)
        swap(wnd->BlkBegCol, wnd->BlkEndCol);
}
/* ----------- BUTTON_RELEASED Message ---------- */
static int ButtonReleasedMsg(WINDOW wnd)
{
    if (isMultiLine(wnd))    {
        ButtonDown = FALSE;
        if (TextMarking && !(WindowMoving || WindowSizing))  {
            /* release the mouse ouside the edit box */
            SendMessage(NULL, MOUSE_TRAVEL, 0, 0);
            StopMarking(wnd);
            return TRUE;
        }
        else
            PrevY = -1;
    }
    return FALSE;
}
/* ---- Process text block keys for multiline text box ---- */
static void DoMultiLines(WINDOW wnd, int c, PARAM p2)
{
    if (isMultiLine(wnd))    {
        if ((int)p2 & (LEFTSHIFT | RIGHTSHIFT))    {
            int kx, ky;
            SendMessage(NULL, CURRENT_KEYBOARD_CURSOR,
                (PARAM) &kx, (PARAM) &ky);
            kx -= GetClientLeft(wnd);
            ky -= GetClientTop(wnd);
            switch (c)    {
                case HOME:
                case END:
                case CTRL_HOME:
                case CTRL_END:
                case PGUP:
                case PGDN:
                case CTRL_PGUP:
                case CTRL_PGDN:
                case UP:
                case DN:
                case FWD:
                case BS:
                case CTRL_FWD:
                case CTRL_BS:
                    if (!KeyBoardMarking)    {
                        if (TextBlockMarked(wnd))    {
                            ClearTextBlock(wnd);
                            SendMessage(wnd, PAINT, 0, 0);
                        }
                        KeyBoardMarking = TextMarking = TRUE;
                        SetAnchor(wnd, kx+wnd->wleft,
                                                ky+wnd->wtop);
                    }
                    break;
                default:
                    break;
            }
        }
    }
}
/* ---------- page/scroll keys ----------- */
static int DoScrolling(WINDOW wnd, int c, PARAM p2)
{
    switch (c)    {
        case PGUP:
        case PGDN:
            if (isMultiLine(wnd))
                BaseWndProc(EDITBOX, wnd, KEYBOARD, c, p2);
            break;
        case CTRL_PGUP:
        case CTRL_PGDN:
            BaseWndProc(EDITBOX, wnd, KEYBOARD, c, p2);
            break;
        case HOME:
            Home(wnd);
            break;
        case END:
            End(wnd);
            break;
        case CTRL_FWD:
            NextWord(wnd);
            break;
        case CTRL_BS:
            PrevWord(wnd);
            break;
        case CTRL_HOME:
            if (isMultiLine(wnd))    {
                SendMessage(wnd, SCROLLDOC, TRUE, 0);
                wnd->CurrLine = 0;
                wnd->WndRow = 0;
            }
            Home(wnd);
            break;
        case CTRL_END:
            if (isMultiLine(wnd) && wnd->wlines > 0)    {
                SendMessage(wnd, SCROLLDOC, FALSE, 0);
                SetLinePointer(wnd, wnd->wlines-1);
                wnd->WndRow =
                    min(ClientHeight(wnd)-1, wnd->wlines-1);
                Home(wnd);
            }
            End(wnd);
            break;
        case UP:
            if (isMultiLine(wnd))
                Upward(wnd);
            break;
        case DN:
            if (isMultiLine(wnd))
                Downward(wnd);
            break;
        case FWD:
            Forward(wnd);
            break;
        case BS:
            Backward(wnd);
            break;
        default:
            return FALSE;
    }
    if (!KeyBoardMarking && TextBlockMarked(wnd))    {
        ClearTextBlock(wnd);
        SendMessage(wnd, PAINT, 0, 0);
    }
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    return TRUE;
}
/* -------------- Del key ---------------- */
static int DelKey(WINDOW wnd)
{
    char *currchar = CurrChar;
    int repaint = *currchar == '\n';
    if (TextBlockMarked(wnd))    {
        SendMessage(wnd, COMMAND, ID_DELETETEXT, 0);
        SendMessage(wnd, PAINT, 0, 0);
        return TRUE;
    }
    if (*(currchar+1) == '\0')
        return TRUE;
    strcpy(currchar, currchar+1);
    if (repaint)    {
        BuildTextPointers(wnd);
        SendMessage(wnd, PAINT, 0, 0);
    }
    else    {
        ModTextPointers(wnd, wnd->CurrLine+1, -1);
        WriteTextLine(wnd, NULL, wnd->WndRow+wnd->wtop, FALSE);
    }
    wnd->TextChanged = TRUE;
    return FALSE;
}
/* ------------ Tab key ------------ */
static int TabKey(WINDOW wnd, PARAM p2)
{
    if (isMultiLine(wnd))    {
        int insmd = wnd->InsertMode;
        do  {
            char *cc = CurrChar+1;
            if (!insmd && *cc == '\0')
                break;
            if (wnd->textlen == wnd->MaxTextLength)
                break;
            SendMessage(wnd,KEYBOARD,insmd ? ' ' : FWD,0);
        } while (wnd->CurrCol % cfg.Tabs);
        return TRUE;
    }
    PostMessage(GetParent(wnd), KEYBOARD, '\t', p2);
    return FALSE;
}
/* --------- All displayable typed keys ------------- */
static void KeyTyped(WINDOW wnd, int c)
{
    char *currchar = CurrChar;
    if ((c != '\n' && c < ' ') || (c & 0x1000))
        /* ---- not recognized by editor --- */
        return;
    if (!isMultiLine(wnd) && TextBlockMarked(wnd))    {
        ResetEditBox(wnd);
        currchar = CurrChar;
    }
    if (*currchar == '\0')    {
        /* ---- typing at end of text ---- */
        if (currchar == wnd->text+wnd->MaxTextLength)    {
            /* ---- typing at the end of maximum buffer ---- */
            beep();
            return;
        }
        /* --- insert a newline at end of text --- */
        *currchar = '\n';
        *(currchar+1) = '\0';
        BuildTextPointers(wnd);
    }
    /* --- displayable char or newline --- */
    if (c == '\n' || wnd->InsertMode ||    *currchar == '\n') {
        /* ------ inserting the keyed character ------ */
        if (wnd->text[wnd->textlen-1] != '\0')    {
            /* --- the current text buffer is full --- */
            if (wnd->textlen == wnd->MaxTextLength)    {
                /* --- text buffer is at maximum size --- */
                beep();
                return;
            }
            /* ---- increase the text buffer size ---- */
            wnd->textlen += GROWLENGTH;
            /* --- but not above maximum size --- */
            if (wnd->textlen > wnd->MaxTextLength)
                wnd->textlen = wnd->MaxTextLength;
            wnd->text = realloc(wnd->text, wnd->textlen+2);
            wnd->text[wnd->textlen-1] = '\0';
            currchar = CurrChar;
        }
        memmove(currchar+1, currchar, strlen(currchar)+1);
        ModTextPointers(wnd, wnd->CurrLine+1, 1);
        if (isMultiLine(wnd) && wnd->wlines > 1)
            wnd->textwidth = max(wnd->textwidth,
                (int) (TextLine(wnd, wnd->CurrLine+1)-
                TextLine(wnd, wnd->CurrLine)));
        else
            wnd->textwidth = max(wnd->textwidth,
                strlen(wnd->text));
        WriteTextLine(wnd, NULL,
            wnd->wtop+wnd->WndRow, FALSE);
    }
    /* ----- put the char in the buffer ----- */
    *currchar = c;
    wnd->TextChanged = TRUE;
    if (c == '\n')    {
        wnd->wleft = 0;
        BuildTextPointers(wnd);
        End(wnd);
        Forward(wnd);
        SendMessage(wnd, PAINT, 0, 0);
        return;
    }
    /* ---------- test end of window --------- */
    if (WndCol == ClientWidth(wnd)-1)    {
        int dif;
        char *cp = currchar;
        while (*cp != ' ' && cp != TextLine(wnd, wnd->CurrLine))
            --cp;
        if (!isMultiLine(wnd) ||
            cp == TextLine(wnd, wnd->CurrLine) ||
                !wnd->WordWrapMode)
            SendMessage(wnd, HORIZSCROLL, TRUE, 0);
        else    {
            dif = 0;
            if (c != ' ')    {
                dif = (int) (currchar - cp);
                wnd->CurrCol -= dif;
                SendMessage(wnd, KEYBOARD, DEL, 0);
                --dif;
            }
            SendMessage(wnd, KEYBOARD, '\r', 0);
            currchar = CurrChar;
            wnd->CurrCol = dif;
            if (c == ' ')
                return;
        }
    }
    /* ------ display the character ------ */
    SetStandardColor(wnd);
    PutWindowChar(wnd, c, WndCol, wnd->WndRow);
    /* ----- advance the pointers ------ */
    wnd->CurrCol++;
}
/* ------------ screen changing key strokes ------------- */
static int DoKeyStroke(WINDOW wnd, int c, PARAM p2)
{
    switch (c)    {
        case RUBOUT:
            Backward(wnd);
        case DEL:
            if (DelKey(wnd))
                return TRUE;
            break;
        case CTRL_FIVE:    /* same as Shift+Tab */
            if (!((int)p2 & (LEFTSHIFT | RIGHTSHIFT)))
                break;
        case '\t':
            if (TabKey(wnd, p2))
                return TRUE;
            break;
        case '\r':
            if (!isMultiLine(wnd))    {
                PostMessage(GetParent(wnd), KEYBOARD, c, p2);
                break;
            }
            c = '\n';
        default:
            if (TextBlockMarked(wnd))    {
                SendMessage(wnd, COMMAND, ID_DELETETEXT, 0);
                SendMessage(wnd, PAINT, 0, 0);
            }
            KeyTyped(wnd, c);
            break;
    }
    return FALSE;
}
/* ----------- KEYBOARD Message ---------- */
static int KeyboardMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    int c = (int) p1;
    if (WindowMoving || WindowSizing || ((int)p2 & ALTKEY))
        return FALSE;
    switch (c)    {
        /* --- these keys get processed by lower classes --- */
        case ESC:
        case F1:
        case F2:
        case F3:
        case F4:
        case F5:
        case F6:
        case F7:
        case F8:
        case F9:
        case F10:
        case INS:
        case SHIFT_INS:
        case SHIFT_DEL:
            return FALSE;
        /* --- these keys get processed here --- */
        case CTRL_FWD:
        case CTRL_BS:
        case CTRL_HOME:
        case CTRL_END:
        case CTRL_PGUP:
        case CTRL_PGDN:
            break;
        default:
            /* other ctrl keys get processed by lower classes */
            if ((int)p2 & CTRLKEY)
                return FALSE;
            /* --- all other keys get processed here --- */
            break;
    }
    DoMultiLines(wnd, c, p2);
    if (DoScrolling(wnd, c, p2))    {
        if (KeyBoardMarking)
            ExtendBlock(wnd, WndCol, wnd->WndRow);
    }
    else if (!TestAttribute(wnd, READONLY))    {
        DoKeyStroke(wnd, c, p2);
        SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    }
    return TRUE;
}
/* ----------- SHIFT_CHANGED Message ---------- */
static void ShiftChangedMsg(WINDOW wnd, PARAM p1)
{
    if (!((int)p1 & (LEFTSHIFT | RIGHTSHIFT)) &&
                                   KeyBoardMarking)    {
        StopMarking(wnd);
        KeyBoardMarking = FALSE;
    }
}
/* ----------- ID_DELETETEXT Command ---------- */
static void DeleteTextCmd(WINDOW wnd)
{
    if (TextBlockMarked(wnd))    {
        char *bbl=TextLine(wnd,wnd->BlkBegLine)+wnd->BlkBegCol;
        char *bel=TextLine(wnd,wnd->BlkEndLine)+wnd->BlkEndCol;
        int len = (int) (bel - bbl);
        SaveDeletedText(wnd, bbl, len);
        wnd->TextChanged = TRUE;
        strcpy(bbl, bel);
        wnd->CurrLine = TextLineNumber(wnd, bbl-wnd->BlkBegCol);
        wnd->CurrCol = wnd->BlkBegCol;
        wnd->WndRow = wnd->BlkBegLine - wnd->wtop;
        if (wnd->WndRow < 0)    {
            wnd->wtop = wnd->BlkBegLine;
            wnd->WndRow = 0;
        }
        SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
        ClearTextBlock(wnd);
        BuildTextPointers(wnd);
    }
}
/* ----------- ID_CLEAR Command ---------- */
static void ClearCmd(WINDOW wnd)
{
    if (TextBlockMarked(wnd))    {
        char *bbl=TextLine(wnd,wnd->BlkBegLine)+wnd->BlkBegCol;
        char *bel=TextLine(wnd,wnd->BlkEndLine)+wnd->BlkEndCol;
        int len = (int) (bel - bbl);
        SaveDeletedText(wnd, bbl, len);
        wnd->CurrLine = TextLineNumber(wnd, bbl);
        wnd->CurrCol = wnd->BlkBegCol;
        wnd->WndRow = wnd->BlkBegLine - wnd->wtop;
        if (wnd->WndRow < 0)    {
            wnd->WndRow = 0;
            wnd->wtop = wnd->BlkBegLine;
        }
        /* ------ change all text lines in block to \n ----- */
        while (bbl < bel)    {
            char *cp = strchr(bbl, '\n');
            if (cp > bel)
                cp = bel;
            strcpy(bbl, cp);
            bel -= (int) (cp - bbl);
            bbl++;
        }
        ClearTextBlock(wnd);
        BuildTextPointers(wnd);
        SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
        SendMessage(wnd, PAINT, 0, 0);
        wnd->TextChanged = TRUE;
    }
}
/* ----------- ID_UNDO Command ---------- */
static void UndoCmd(WINDOW wnd)
{
    if (wnd->DeletedText != NULL)    {
        PasteText(wnd, wnd->DeletedText, wnd->DeletedLength);
        free(wnd->DeletedText);
        wnd->DeletedText = NULL;
        wnd->DeletedLength = 0;
        SendMessage(wnd, PAINT, 0, 0);
    }
}
/* ----------- ID_PARAGRAPH Command ---------- */
static void ParagraphCmd(WINDOW wnd)
{
    int bc, ec, fl, el, Blocked;
    char *bl, *bbl, *bel, *bb;

    el = wnd->BlkEndLine;
    ec = wnd->BlkEndCol;
    if (!TextBlockMarked(wnd))    {
        Blocked = FALSE;
        /* ---- forming paragraph from cursor position --- */
        fl = wnd->wtop + wnd->WndRow;
        bbl = bel = bl = TextLine(wnd, wnd->CurrLine);
        if ((bc = wnd->CurrCol) >= ClientWidth(wnd))
            bc = 0;
        Home(wnd);
        /* ---- locate the end of the paragraph ---- */
        while (*bel)    {
            int blank = TRUE;
            char *bll = bel;
            /* --- blank line marks end of paragraph --- */
            while (*bel && *bel != '\n')    {
                if (*bel != ' ')
                    blank = FALSE;
                bel++;
            }
            if (blank)    {
                bel = bll;
                break;
            }
            if (*bel)
                bel++;
        }
        if (bel == bbl)    {
            SendMessage(wnd, KEYBOARD, DN, 0);
            return;
        }
        if (*bel == '\0')
            --bel;
        if (*bel == '\n')
            --bel;
    }
    else    {
        /* ---- forming paragraph from marked block --- */
        Blocked = TRUE;
        bbl = TextLine(wnd, wnd->BlkBegLine) + wnd->BlkBegCol;
        bel = TextLine(wnd, wnd->BlkEndLine) + wnd->BlkEndCol;
        fl = wnd->BlkBegLine;
        bc = wnd->CurrCol = wnd->BlkBegCol;
        wnd->CurrLine = fl;
        if (fl < wnd->wtop)
            wnd->wtop = fl;
        wnd->WndRow = fl - wnd->wtop;
        SendMessage(wnd, KEYBOARD, '\r', 0);
        el++, fl++;
        if (bc != 0)    {
            SendMessage(wnd, KEYBOARD, '\r', 0);
            el++, fl ++;
        }
        bc = 0;
        bl = TextLine(wnd, fl);
        wnd->CurrLine = fl;
        bbl = bl + bc;
        bel = TextLine(wnd, el) + ec;
    }
    /* --- change all newlines in block to spaces --- */
    while (CurrChar < bel)    {
        if (*CurrChar == '\n')    {
            *CurrChar = ' ';
            wnd->CurrLine++;
            wnd->CurrCol = 0;
        }
        else
            wnd->CurrCol++;
    }
    /* ---- insert newlines at new margin boundaries ---- */
    bb = bbl;
    while (bbl < bel)    {
        bbl++;
        if ((int)(bbl - bb) == ClientWidth(wnd)-1)    {
            while (*bbl != ' ' && bbl > bb)
                --bbl;
            if (*bbl != ' ')    {
                bbl = strchr(bbl, ' ');
                if (bbl == NULL || bbl >= bel)
                    break;
            }
            *bbl = '\n';
            bb = bbl+1;
        }
    }
    ec = (int)(bel - bb);
    BuildTextPointers(wnd);
    if (Blocked)    {
        /* ---- position cursor at end of new paragraph ---- */
        if (el < wnd->wtop ||
                wnd->wtop + ClientHeight(wnd) < el)
            wnd->wtop = el-ClientHeight(wnd);
        if (wnd->wtop < 0)
            wnd->wtop = 0;
        wnd->WndRow = el - wnd->wtop;
        wnd->CurrLine = el;
        wnd->CurrCol = ec;
        SendMessage(wnd, KEYBOARD, '\r', 0);
        SendMessage(wnd, KEYBOARD, '\r', 0);
    }
    else    {
        /* --- put cursor back at beginning --- */
        wnd->CurrLine = TextLineNumber(wnd, bl);
        wnd->CurrCol = bc;
        if (fl < wnd->wtop)
            wnd->wtop = fl;
        wnd->WndRow = fl - wnd->wtop;
    }
    SendMessage(wnd, PAINT, 0, 0);
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    wnd->TextChanged = TRUE;
    BuildTextPointers(wnd);
}
/* ----------- COMMAND Message ---------- */
static int CommandMsg(WINDOW wnd, PARAM p1)
{
    switch ((int)p1)    {
        case ID_DELETETEXT:
            DeleteTextCmd(wnd);
            return TRUE;
        case ID_CLEAR:
            ClearCmd(wnd);
            return TRUE;
        case ID_UNDO:
            UndoCmd(wnd);
            return TRUE;
        case ID_PARAGRAPH:
            ParagraphCmd(wnd);
            return TRUE;
        default:
            break;
    }
    return FALSE;
}
/* ---------- CLOSE_WINDOW Message ----------- */
static void CloseWindowMsg(WINDOW wnd)
{
    SendMessage(NULL, HIDE_CURSOR, 0, 0);
    if (wnd->DeletedText != NULL)
        free(wnd->DeletedText);
}
/* ------- Window processing module for EDITBOX class ------ */
int EditBoxProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    int rtn;
    switch (msg)    {
        case CREATE_WINDOW:
            return CreateWindowMsg(wnd);
        case ADDTEXT:
            return AddTextMsg(wnd, p1, p2);
        case SETTEXT:
            return SetTextMsg(wnd, p1);
        case CLEARTEXT:
            ResetEditBox(wnd);
            break;
        case GETTEXT:
            return GetTextMsg(wnd, p1, p2);
        case SETTEXTLENGTH:
            return SetTextLengthMsg(wnd, (unsigned) p1);
        case KEYBOARD_CURSOR:
            return KeyboardCursorMsg(wnd, p1, p2);
        case SETFOCUS:
        case PAINT:
        case MOVE:
            rtn = BaseWndProc(EDITBOX, wnd, msg, p1, p2);
            SendMessage(wnd,KEYBOARD_CURSOR,WndCol,wnd->WndRow);
            return rtn;
        case SIZE:
            return SizeMsg(wnd, p1, p2);
        case SCROLL:
            return ScrollMsg(wnd, p1);
        case HORIZSCROLL:
            return HorizScrollMsg(wnd, p1);
        case SCROLLPAGE:
            return ScrollPageMsg(wnd, p1);
        case HORIZPAGE:
            return HorizPageMsg(wnd, p1);
        case LEFT_BUTTON:
            if (LeftButtonMsg(wnd, p1, p2))
                return TRUE;
            break;
        case MOUSE_MOVED:
            if (MouseMovedMsg(wnd, p1, p2))
                return TRUE;
            break;
        case BUTTON_RELEASED:
            if (ButtonReleasedMsg(wnd))
                return TRUE;
            break;
        case KEYBOARD:
            if (KeyboardMsg(wnd, p1, p2))
                return TRUE;
            break;
        case SHIFT_CHANGED:
            ShiftChangedMsg(wnd, p1);
            break;
        case COMMAND:
            if (CommandMsg(wnd, p1))
                return TRUE;
            break;
        case CLOSE_WINDOW:
            CloseWindowMsg(wnd);
            break;
        default:
            break;
    }
    return BaseWndProc(EDITBOX, wnd, msg, p1, p2);
}
/* ------ save deleted text for the Undo command ------ */
static void SaveDeletedText(WINDOW wnd, char *bbl, int len)
{
    wnd->DeletedLength = len;
    if ((wnd->DeletedText=realloc(wnd->DeletedText,len))!=NULL)
        memmove(wnd->DeletedText, bbl, len);
}
/* ---- cursor right key: right one character position ---- */
static void Forward(WINDOW wnd)
{
    char *cc = CurrChar+1;
    if (*cc == '\0')
        return;
    if (*CurrChar == '\n')    {
        Home(wnd);
        Downward(wnd);
    }
    else    {
        wnd->CurrCol++;
        if (WndCol == ClientWidth(wnd))
            SendMessage(wnd, HORIZSCROLL, TRUE, 0);
    }
}
/* ----- stick the moving cursor to the end of the line ---- */
static void StickEnd(WINDOW wnd)
{
    char *cp = TextLine(wnd, wnd->CurrLine);
    char *cp1 = strchr(cp, '\n');
    int len = cp1 ? (int) (cp1 - cp) : 0;
    wnd->CurrCol = min(len, wnd->CurrCol);
    if (wnd->wleft > wnd->CurrCol)    {
        wnd->wleft = max(0, wnd->CurrCol - 4);
        SendMessage(wnd, PAINT, 0, 0);
    }
    else if (wnd->CurrCol-wnd->wleft >= ClientWidth(wnd))    {
        wnd->wleft = wnd->CurrCol - (ClientWidth(wnd)-1);
        SendMessage(wnd, PAINT, 0, 0);
    }
}
/* --------- cursor down key: down one line --------- */
static void Downward(WINDOW wnd)
{
    if (isMultiLine(wnd) &&
            wnd->WndRow+wnd->wtop+1 < wnd->wlines)  {
        wnd->CurrLine++;
        if (wnd->WndRow == ClientHeight(wnd)-1)
            SendMessage(wnd, SCROLL, TRUE, 0);
        wnd->WndRow++;
        StickEnd(wnd);
    }
}
/* -------- cursor up key: up one line ------------ */
static void Upward(WINDOW wnd)
{
    if (isMultiLine(wnd) && wnd->CurrLine != 0)    {
        if (wnd->CurrLine > 0)
            --wnd->CurrLine;
        if (wnd->WndRow == 0)
            SendMessage(wnd, SCROLL, FALSE, 0);
        --wnd->WndRow;
        StickEnd(wnd);
    }
}
/* ---- cursor left key: left one character position ---- */
static void Backward(WINDOW wnd)
{
    if (wnd->CurrCol)    {
        if (wnd->CurrCol-- <= wnd->wleft)
            if (wnd->wleft != 0)
                SendMessage(wnd, HORIZSCROLL, FALSE, 0);
    }
    else if (isMultiLine(wnd) && wnd->CurrLine != 0)    {
        Upward(wnd);
        End(wnd);
    }
}
/* -------- End key: to end of line ------- */
static void End(WINDOW wnd)
{
    while (*CurrChar && *CurrChar != '\n')
        ++wnd->CurrCol;
    if (WndCol >= ClientWidth(wnd))    {
        wnd->wleft = wnd->CurrCol - (ClientWidth(wnd)-1);
        SendMessage(wnd, PAINT, 0, 0);
    }
}
/* -------- Home key: to beginning of line ------- */
static void Home(WINDOW wnd)
{
    wnd->CurrCol = 0;
    if (wnd->wleft != 0)    {
        wnd->wleft = 0;
        SendMessage(wnd, PAINT, 0, 0);
    }
}
/* -- Ctrl+cursor right key: to beginning of next word -- */
static void NextWord(WINDOW wnd)
{
    int savetop = wnd->wtop;
    int saveleft = wnd->wleft;
    ClearVisible(wnd);
    while (!isWhite(*CurrChar))    {
        char *cc = CurrChar+1;
        if (*cc == '\0')
            break;
        Forward(wnd);
    }
    while (isWhite(*CurrChar))    {
        char *cc = CurrChar+1;
        if (*cc == '\0')
            break;
        Forward(wnd);
    }
    SetVisible(wnd);
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    if (wnd->wtop != savetop || wnd->wleft != saveleft)
        SendMessage(wnd, PAINT, 0, 0);
}
/* -- Ctrl+cursor left key: to beginning of previous word -- */
static void PrevWord(WINDOW wnd)
{
    int savetop = wnd->wtop;
    int saveleft = wnd->wleft;
    ClearVisible(wnd);
    Backward(wnd);
    while (isWhite(*CurrChar))    {
        if (wnd->CurrLine == 0 && wnd->CurrCol == 0)
            break;
        Backward(wnd);
    }
    while (!isWhite(*CurrChar))    {
        if (wnd->CurrLine == 0 && wnd->CurrCol == 0)
            break;
        Backward(wnd);
    }
    if (isWhite(*CurrChar))
        Forward(wnd);
    SetVisible(wnd);
    if (wnd->wleft != saveleft)
        if (wnd->CurrCol >= saveleft)
            if (wnd->CurrCol - saveleft < ClientWidth(wnd))
                wnd->wleft = saveleft;
    SendMessage(wnd, KEYBOARD_CURSOR, WndCol, wnd->WndRow);
    if (wnd->wtop != savetop || wnd->wleft != saveleft)
        SendMessage(wnd, PAINT, 0, 0);
}
/* ----- reset the text attributes of an EDITBOX ------- */
static void ResetEditBox(WINDOW wnd)
{
    unsigned blen = EditBufLen(wnd)+2;
    wnd->text = realloc(wnd->text, blen);
    memset(wnd->text, 0, blen);
    wnd->wlines = 0;
    wnd->CurrLine = 0;
    wnd->CurrCol = 0;
    wnd->WndRow = 0;
    wnd->wleft = 0;
    wnd->wtop = 0;
    wnd->textwidth = 0;
    wnd->TextChanged = FALSE;
    ClearTextPointers(wnd);
    ClearTextBlock(wnd);
}
/* ----- modify text pointers from a specified position
                by a specified plus or minus amount ----- */
static void ModTextPointers(WINDOW wnd, int lineno, int var)
{
    while (lineno < wnd->wlines)
        *((wnd->TextPointers) + lineno++) += var;
}
```

Copyright © 1992, Dr. Dobb's Journal
