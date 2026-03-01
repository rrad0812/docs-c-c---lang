
# C PROGRAMMING 9202

## D-Flat Clipboard, pretrage i slike - Al Stevens

Ovog meseca predstavljamo novu klasu prozora D-Flat, PICTUREBOKS, i obezbeđujemo kod koji podržava međuspremnik i pretraživanje teksta za klasu EDITBOKS. Za one od vas koji su došli do ovog projekta u sredini, D-Flat je biblioteka C jezika funkcije javnog domena koja implementira korisnički interfejs Common User Access (CUA) u tekstualnom režimu na DOS računarima. Prvo, ipak, putopis.

### D-Flat Clipboard

D-Flat ima međuspremnik koji radi kao onaj definisan u CUA specifikaciji. Međuspremnik je skladište podataka u koje sečete, kopirate i lepite informacije. U GUI, klipbord može da sadrži grafiku ili tekst. D-Flat je sistem tekstualnog režima. Zbog toga može da skladišti samo tekst u svom međuspremniku.

Korisnici koriste međuspremnik za premeštanje podataka u okviru aplikacije. Blok teksta isečete ili kopirate u međuspremnik sa označene lokacije u polju za uređivanje. Kopiranje teksta čini upravo to; kopira tekst u međuspremnik. Rezanjem teksta se kopira, a zatim se briše sa originalne lokacije. Razgovarali smo o obeležavanju tekstualnih blokova prošlog meseca.

Kada međuspremnik ima tekst u sebi, korisnik može da nalepi taj tekst negde drugde u dokumentu. Lepljenje umeće tekst na trenutnu lokaciju kursora na tastaturi baš kao da ga je korisnik ponovo otkucao.

[LISTING_ONE](#listing_one), je "clipbord.c", kod koji implementira kopiranje i lepljenje. Izvorni fajl editbox.c od prošlog meseca ima kod koji poziva funkcije u clipbord.c. Kada korisnik izvrši operaciju sečenja, kod editboka koristi rutinu kopiranja međuspremnika, a zatim koristi rutinu brisanja bloka u editbok.c da izbriše blok teksta. Pokazivač karaktera Clipboard u clipbord.c pokazuje na tekst međuspremnika. Kada je taj pokazivač NULL, klipbord je prazan. Kada korisnik izvrši prvo isečenje ili kopiranje, klipbord ima sadržaj sve dok program radi. Svaki novi isečak ili kopija zamenjuje trenutni sadržaj klipborda. Promenljiva ClipboardLength sadrži broj znakova u međuspremniku.

Funkcija CopiToClipboard kopira označeni tekst u međuspremnik. Prvo izračunava pokazivače na početak i kraj tekstualnog bloka i dužinu bloka. Zatim ponovo dodeljuje memorijski bafer klipborda i kopira označeni blok u novi memorijski bafer.

Funkcija PasteTekt umeće tekst klipborda u okvir za uređivanje na trenutnoj lokaciji kursora. Ova funkcija prihvata adresu bafera međumemorije i dužinu teksta kao parametre, a ne iz globalnih promenljivih Clipboard i ClipboardLength jer funkcija poništavanja editboka koristi funkciju PasteTekt da ponovo ubaci tekst u editbok iz bafera za poništavanje. Funkcija prvo izračunava novu dužinu bafera okvira za uređivanje sumiranjem trenutne dužine bafera i dužine teksta koji treba nalepiti. Ako je nova dužina manja od trenutne veličine bafera, funkcija ponovo dodeljuje bafer na novu dužinu. Zatim mora otvoriti rupu u tekstu da bi prihvatio nalepljeni tekst. Funkcija pravi pokazivač na trenutnu lokaciju kursora i jedan na tu lokaciju plus dužinu teksta koji treba nalepiti. Zatim pomera tekst okvira za uređivanje sa prve lokacije na drugu koristeći dužinu teksta od prve lokacije do kraja bafera. Konačno, funkcija PasteTekt pomera tekst koji treba zalepiti u rupu koju je upravo kreirala. On označava tekst kao promenjen i poziva funkciju BuildTektPointers da bi ponovo izgradio niz tekstualnih pokazivača bafera teksta.

### Traženje teksta

[LISTING_TWO](#listing_two), je "search.c", kod koji omogućava korisniku da traži i zameni vrednosti stringova u tekstu polja za uređivanje. Postoje tri funkcije koje aplikativni program može pozvati. Funkcija ReplaceTekt koristi dijalog ReplaceTektDB za korisnika da unese tekstualni niz za pretraživanje i tekstualni niz za zamenu podudaranja. Funkcija SearchTekt koristi dijalog SearchTektDB da bi korisnik uneo tekstualni niz za pretraživanje bez zamene. Funkcija SearchNekt pretpostavlja da je postojao prethodni poziv funkcije SearchTekt i nastavlja pretragu nakon tačke u kojoj je prethodna pretraga pronašla podudaranje. Sve tri funkcije se pozivaju iz aplikativnog programa. U primeru aplikacije Memopad koju koristim, modul za obradu prozora za prozore dokumenta okvira za uređivanje poziva ove funkcije kada korisnik napravi odgovarajući izbor iz menija za pretragu prozora aplikacije.

Sve tri funkcije koriste zajedničku funkciju SearchTektBok, koja vrši pretrage i zamene. Prvi parametar je rukohvat prozora polja za uređivanje koji se traži. Drugi parametar je indikator tačno/netačno koji govori funkciji da li je zamena uključena ili ne. Treći parametar je tačan da kaže funkciji da počne od jedne posle trenutne pozicije kursora i netačan da joj kaže da počne od trenutne pozicije kursora. Ovaj parametar sprečava funkciju da se podudara sa istim stringom pri uzastopnim pozivima iz SearchNekt-a. Algoritam pretrage je jednostavno skeniranje teksta koje upoređuje tekst na svakoj lokaciji sa stringom za pretragu. Poređenje je osetljivo na velika i mala slova samo kada je promenljiva Check-Case netačna. Pre pozivanja funkcije SearchTektBok, program postavlja ovu promenljivu na osnovu podešavanja polja za potvrdu u okviru za dijalog. Kada program pronađe podudaranje u nizu za pretragu, on označava odgovarajući blok teksta u okviru za uređivanje i pozicionira kursor tastature na prvi znak bloka. Ako operacija uključuje zamenu stringa, program poziva funkciju ReplaceTekt da zameni odgovarajući tekst navedenim nizom. Ta funkcija će proširiti ili skupiti tekstualni bafer prozora u zavisnosti od toga da li je novi tekst veći ili manji od teksta koji zamenjuje.

### Klasa prozora PICTUREBOKS

Klasa prozora D-Flat PICTUREBOKS vam omogućava da koristite grafički skup znakova računara za crtanje horizontalnih i vertikalnih linija i traka u tekstualni prozor. Ova funkcija podržava prozore koji imaju prilagođene ivice i okvire unutar prostora podataka. Takođe vam omogućava da crtate trakaste grafikone. Klasa prozora PICTUREBOKS beleži prisustvo vektora i šipki u VectorList nizu, koji je niz VECT struktura, od kojih svaka definiše vektor ili traku. VECT struktura sadrži RECT strukturu koja definiše dimenzije vektora ili trake i VectTipes promenljivu koja pokazuje da li je stavka vektor ili traka i, ako je traka, znak koji se koristi za prikaz trake. Vektor je jedna linija napravljena od znakova koji crtaju pojedinačne linije. Koristili ste te iste likove za pravljenje prozorskih okvira. Traka je linija napravljena od blok znakova iz grafičkog skupa znakova. Postoje četiri različita bloka sa različitim teksturama.

[LISTING_THREE], je "pictbox.c", kod koji implementira klasu PICTUREBOKS. Interfejs aplikacijskog programa za okvir sa slikama je prilično jednostavan. Pravite prozor klase i pozivate funkcije ili šaljete poruke za crtanje vektora i traka.

Na primer, poruka DRAVVECTOR crta horizontalnu ili vertikalnu liniju na kutiji sa slikom. Čini se da je korišćenje skupa grafičkih znakova za crtanje linije jednostavna operacija. Ovo bi bilo tačno da se linije koje ste nacrtali nikada nisu ukrštale sa drugim linijama koje ste nacrtali. Ali linije se ukrštaju, a algoritam za crtanje linija mora da se bavi tim presecima na odgovarajući način. To znači da dok program povlači crtu, mora da pogleda karakter koji se prepisuje. Ako je taj znak drugi vektorski karakter, program mora da nacrta taj element novog vektora sa znakom koji ispravno predstavlja presek. Zamenjeni karakter će biti drugačiji, u zavisnosti od toga da li je novi vektor horizontalan ili vertikalni; da li je vektor koji se preseca horizontalan ili vertikalan, da li je znak koji se seku prvi, srednji ili poslednji znak novog vektora; da li je karakter koji se seče prvi, srednji ili poslednji znak presečenog vektora; a šta je presečeni vektorski karakter. To može biti jednostavan karakter linije ili može biti ugao ili drugi znak koji je rezultat ranijeg preseka. Skup grafičkih znakova računara uključuje horizontalne i vertikalne ravne linije, znak gde se horizontalne vertikalne linije seku formirajući krst, četiri ugla kutije i četiri različite T formacije gde linija počinje ili se završava na preseku druge linije.

Funkcija PaintVector u pictbok.c počinje znakom prave linije, horizontalno ili vertikalno. Zatim nastavlja da upisuje taj znak u prozor za dužinu vektora - osim kada je karakter koji zamenjuje sam vektorski karakter. CharInVnd niz sadrži sve znakove koji, kada se preseku, ukazuju na to da se drugi vektor preseca. Kada se to desi, program mora da izvrši zamenu. VectCvt tabela je niz sa četiri dimenzije. Njegova svrha je da vrati karakter zamene da nacrta trenutni element novog vektora. Njegova prva dimenzija je od 0 do 2, u zavisnosti od toga da li je presečeni vektorski znak prvi, srednji ili poslednji znak tog vektora. Funkcija FindVector izračunava taj indeks tako što pretražuje niz VectList prozora za poslednji vektor u nizu koji prolazi kroz presečenu poziciju karaktera. Zatim, na osnovu relativnog položaja karaktera prema vektoru, funkcija FindVector vraća indeks od 0 do 2. Druga dimenzija VectCvt niza je od 0 do 12, u zavisnosti od toga koji je od 13 znakova za vektorsko crtanje presečen. Ova vrednost se izračunava iz relativne pozicije karaktera u CharInVnd nizu. Treća dimenzija VectCvt niza je 0 ako je novi vektor horizontalan i 1 ako je vertikalan. Poslednja dimenzija niza je 0, 1 ili 2, u zavisnosti od toga da li je znak koji se seku prvi, srednji ili poslednji karakter novog vektora. Ta četiri indeksa će vratiti ispravan znak za prikaz iz VectCvt niza.

Funkcija DravVector šalje poruku DRAVVECTOR, funkcija DravBok šalje poruku DRAVBOKS, a funkcija DravBar šalje poruku DRAVBAR.

Funkciji DravVector prenosite ručku prozora, koordinate k i i prve pozicije vektora, dužinu vektora u pozicijama znakova i indikator tačno/netačno koji je tačan za horizontalni vektor i netačan za vertikalni vektor. Funkcija DravVector šalje poruku DRAVVECTOR prozoru. Poruka prosleđuje pokazivač na RECT strukturu koja definiše vektor. RECT ima visinu ili širinu od jednog znaka, u zavisnosti od toga da li je vektor horizontalan ili vertikalan. Možete i sami da pošaljete poruku, ali je sa funkcijom lakše raditi. Često je funkcija jednostavnija i lakša za razumevanje od slanja poruke. Jedan od načina na koji je Vindovs SDK evoluirao bio je da je uvedeno sve više funkcija koje su preuzele mukotrpnost nekih manje intuitivnih poruka.

Poruka DRAVBOKS se proširuje u četiri DRAVVECTOR poruke da nacrta četiri vektora kutije. Poruka DRAVBAR je slična poruci DRAVVECTOR osim što ne brine o raskrsnicama.

### Kalendar

[LISTING_FOUR], je "calendar.c", program koji koristi vektore u klasi PICTUREBOKS za prikaz kalendara. Klasa PICTUREBOKS potiče od klase TEKSTBOKS, tako da program može da prikaže tekst kao i vektore i trake. Funkcija Kalendar dobija trenutno vreme i datum i kreira prozor PICTUREBOKS za prikaz kalendara. Kada modul za obradu prozora, CalendarProc, dobije poruku CREATE_VINDOV, on crta okvir u kome će se prikazati dani u mesecu i pravi mesečnu mrežu 5 k 7 crtanjem vektora koji se seku. Poruka PAINT prikazuje datume u mesecu u ispravnim mrežama izračunavanjem dana u nedelji od kojeg počinje prvi u mesecu i popunjavanjem mreže odatle. Program prikazuje trenutni datum crvenom, a ostali crnom bojom. Poruka KEIBOARD presreće PgUp i PgDn tastere da bi omogućila korisniku da prelistava kalendar po mesec dana.

### Bar grafikon

[LISTING_FIVE](#listing_five), je "barchart.c", koji prikazuje primer trakastog grafikona. Počinje kreiranjem prozora PICTUREBOKS i pisanjem teksta u prozor. Da bi simulirao kako bi program dinamički izgradio trakasti grafikon od niza informacija, program trakastog grafikona koristi jednostavnu strukturu koja sadrži opisni tekst i početne i završne pozicije povezanih traka na grafikonu. Ovaj jednostavan primer imitira raspored projekta. Polja početka i zaustavljanja su meseci početka i zaustavljanja svakog projekta u nizu. Program crta horizontalnu traku za svaki projekat, menjajući karakter prikaza za traku tako da grafikon pruža vizuelno razdvajanje šipki. Slika 1 prikazuje kalendar i trakasti grafikon kako ih prikazuje primer programa memopad.

#### LISTING_ONE

```c
/* ----------- clipbord.c ------------ */
#include "dflat.h"

char *Clipboard;
int ClipboardLength;

void CopyToClipboard(WINDOW wnd)
{
    if (TextBlockMarked(wnd))    {
        char *bbl=TextLine(wnd,wnd->BlkBegLine)+wnd->BlkBegCol;
        char *bel=TextLine(wnd,wnd->BlkEndLine)+wnd->BlkEndCol;
        ClipboardLength = (int) (bel - bbl);
        Clipboard = realloc(Clipboard, ClipboardLength);
        if (Clipboard != NULL)
            memmove(Clipboard, bbl, ClipboardLength);
    }
}

void PasteText(WINDOW wnd, char *SaveTo, int len)
{
    if (SaveTo != NULL && len > 0)    {
        int plen = strlen(wnd->text) + len;
        char *bl, *el;

        if (plen > wnd->textlen)    {
            wnd->text = realloc(wnd->text, plen+2);
            wnd->textlen = plen;
        }
        if (wnd->text != NULL)    {
            bl = CurrChar;
            el = bl+len;
            memmove(el, bl, strlen(bl)+1);
            memmove(bl, SaveTo, len);
            BuildTextPointers(wnd);
            wnd->TextChanged = TRUE;
        }
    }
}
```

#### LISTING_TWO

```c
/* ---------------- search.c ------------- */
#include "dflat.h"

extern DBOX SearchTextDB;
extern DBOX ReplaceTextDB;
static int CheckCase = TRUE;

/* - case-insensitive, white-space-normalized char compare - */
static int SearchCmp(int a, int b)
{
    if (b == '\n')
        b = ' ';
    if (CheckCase)
        return a != b;
    return tolower(a) != tolower(b);
}

/* ----- replace a matching block of text ----- */
static void replacetext(WINDOW wnd, char *cp1, DBOX *db)
{
    char *cr = GetEditBoxText(db, ID_REPLACEWITH);
    char *cp = GetEditBoxText(db, ID_SEARCHFOR);
    int oldlen = strlen(cp); /* length of text being replaced */
    int newlen = strlen(cr); /* length of replacing text      */
    int dif;
    if (oldlen < newlen)    {
        /* ---- new text expands text size ---- */
        dif = newlen-oldlen;
        if (wnd->textlen < strlen(wnd->text)+dif)    {
            /* ---- need to reallocate the text buffer ---- */
            int offset = (int)(cp1-wnd->text);
            wnd->textlen += dif;
            wnd->text = realloc(wnd->text, wnd->textlen+2);
            if (wnd->text == NULL)
                return;
            cp1 = wnd->text + offset;
        }
        memmove(cp1+dif, cp1, strlen(cp1)+1);
    }
    else if (oldlen > newlen)    {
        /* ---- new text collapses text size ---- */
        dif = oldlen-newlen;
        memmove(cp1, cp1+dif, strlen(cp1)+1);
    }
    strncpy(cp1, cr, newlen);
}

/* ------- search for the occurrance of a string ------- */
static void SearchTextBox(WINDOW wnd, int Replacing, int incr)
{
    char *s1, *s2, *cp1;
    DBOX *db = Replacing ? &ReplaceTextDB : &SearchTextDB;
    char *cp = GetEditBoxText(db, ID_SEARCHFOR);
    int rpl = TRUE, FoundOne = FALSE;

    while (rpl == TRUE && cp != NULL)    {
        rpl = Replacing ?
                CheckBoxSetting(&ReplaceTextDB, ID_REPLACEALL) :
                FALSE;
        if (TextBlockMarked(wnd))    {
            ClearTextBlock(wnd);
            SendMessage(wnd, PAINT, 0, 0);
        }
        /* search for a match starting at cursor position */
        cp1 = CurrChar;
        if (incr)
            cp1++;    /* start past the last hit */
        /* --- compare at each character position --- */
        while (*cp1)    {
            s1 = cp;
            s2 = cp1;
            while (*s1 && *s1 != '\n')    {
                if (SearchCmp(*s1, *s2))
                    break;
                s1++, s2++;
            }
            if (*s1 == '\0' || *s1 == '\n')
                break;
            cp1++;
        }
        if (*s1 == 0 || *s1 == '\n')    {
            /* ----- match at *cp1 ------- */
            FoundOne = TRUE;

            /* mark a block at beginning of matching text */
            wnd->BlkEndLine = TextLineNumber(wnd, s2);
            wnd->BlkBegLine = TextLineNumber(wnd, cp1);
            if (wnd->BlkEndLine < wnd->BlkBegLine)
                wnd->BlkEndLine = wnd->BlkBegLine;
            wnd->BlkEndCol =
                (int)(s2 - TextLine(wnd, wnd->BlkEndLine));
            wnd->BlkBegCol =
                (int)(cp1 - TextLine(wnd, wnd->BlkBegLine));

            /* position the cursor at the matching text */
            wnd->CurrCol = wnd->BlkBegCol;
            wnd->CurrLine = wnd->BlkBegLine;
            wnd->WndRow = wnd->CurrLine - wnd->wtop;

            /* align the window scroll to matching text */
            if (WndCol > ClientWidth(wnd)-1)
                wnd->wleft = wnd->CurrCol;
            if (wnd->WndRow > ClientHeight(wnd)-1)    {
                wnd->wtop = wnd->CurrLine;
                wnd->WndRow = 0;
            }

            SendMessage(wnd, PAINT, 0, 0);
            SendMessage(wnd, KEYBOARD_CURSOR,
                WndCol, wnd->WndRow);

            if (Replacing)    {
                if (rpl || YesNoBox("Replace the text?"))  {
                    replacetext(wnd, cp1, db);
                    wnd->TextChanged = TRUE;
                    BuildTextPointers(wnd);
                }
                if (rpl)    {
                    incr = TRUE;
                    continue;
                }
                ClearTextBlock(wnd);
                SendMessage(wnd, PAINT, 0, 0);
            }
            return;
        }
        break;
    }
    if (!FoundOne)
        MessageBox("Search/Replace Text", "No match found");
}

/* ------- search for the occurrance of a string,
        replace it with a specified string ------- */
void ReplaceText(WINDOW wnd)
{
    if (CheckCase)
        SetCheckBox(&ReplaceTextDB, ID_MATCHCASE);
    if (DialogBox(wnd, &ReplaceTextDB, TRUE, NULL))    {
        CheckCase=CheckBoxSetting(&ReplaceTextDB,ID_MATCHCASE);
        SearchTextBox(wnd, TRUE, FALSE);
    }
}

/* ------- search for the first occurrance of a string ------ */
void SearchText(WINDOW wnd)
{
    if (CheckCase)
        SetCheckBox(&SearchTextDB, ID_MATCHCASE);
    if (DialogBox(wnd, &SearchTextDB, TRUE, NULL))    {
        CheckCase=CheckBoxSetting(&SearchTextDB,ID_MATCHCASE);
        SearchTextBox(wnd, FALSE, FALSE);
    }
}

/* ------- search for the next occurrance of a string ------- */
void SearchNext(WINDOW wnd)
{
    SearchTextBox(wnd, FALSE, TRUE);
}
```

#### LISTING_THREE

```c
/* -------------- pictbox.c -------------- */

#include "dflat.h"

typedef struct    {
    enum VectTypes vt;
    RECT rc;
} VECT;

unsigned char CharInWnd[] = "D3Z?Y@EC4AB";

unsigned char VectCvt[3][11][2][4] = {
    {   /* --- first character in collision vector --- */
        /* ( drawing D ) ( drawing 3 ) */
             {{"DDD"},     {"ZC@"}},
             {{"ZB?"},     {"333"}},
             {{"ZBB"},     {"ZCC"}},
             {{"???"},     {"???"}},
             {{"YYY"},     {"YYY"}},
             {{"@AA"},     {"CC@"}},
             {{"EEE"},     {"EEE"}},
             {{"CEE"},     {"CCC"}},
             {{"444"},     {"444"}},
             {{"AAA"},     {"AAA"}},
             {{"BBB"},     {"BEE"}}    },
    {   /* --- middle character in collision vector --- */
        /* ( drawing D ) ( drawing 3 ) */
             {{"DDD"},     {"BEA"}},
             {{"CE4"},     {"333"}},
             {{"ZZZ"},     {"ZZZ"}},
             {{"???"},     {"???"}},
             {{"YYY"},     {"YYY"}},
             {{"@@@"},     {"@@@"}},
             {{"EEE"},     {"EEE"}},
             {{"CCC"},     {"CCC"}},
             {{"EE4"},     {"444"}},
             {{"AAA"},     {"EEA"}},
             {{"BBB"},     {"BBB"}}    },
    {   /* --- last character in collision vector --- */
        /* ( drawing D ) ( drawing 3 ) */
             {{"DDD"},     {"?4Y"}},
             {{"@AY"},     {"333"}},
             {{"ZZZ"},     {"ZZZ"}},
             {{"BB?"},     {"?44"}},
             {{"AAY"},     {"44Y"}},
             {{"@@@"},     {"@@@"}},
             {{"EEE"},     {"EEE"}},
             {{"CCC"},     {"CCC"}},
             {{"EE4"},     {"444"}},
             {{"AAA"},     {"EEA"}},
             {{"BBB"},     {"BBB"}}    }
};

/* -- compute whether character is first, middle, or last -- */
static int FindVector(WINDOW wnd, RECT rc, int x, int y)
{
    RECT rcc;
    VECT *vc = wnd->VectorList;
    int i, coll = -1;
    for (i = 0; i < wnd->VectorCount; i++)    {
        if ((vc+i)->vt == VECTOR)    {
            rcc = (vc+i)->rc;
            /* --- skip the colliding vector --- */
            if (rcc.lf == rc.lf && rcc.rt == rc.rt &&
                    rcc.tp == rc.tp && rc.bt == rcc.bt)
                continue;
            if (rcc.tp == rcc.bt)    {
                /* ---- horizontal vector,
                    see if character is in it --- */
                if (rc.lf+x >= rcc.lf && rc.lf+x <= rcc.rt &&
                        rc.tp+y == rcc.tp)    {
                    /* --- it is --- */
                    if (rc.lf+x == rcc.lf)
                        coll = 0;
                    else if (rc.lf+x == rcc.rt)
                        coll = 2;
                    else
                        coll = 1;
                }
            }
            else     {
                /* ---- vertical vector,
                    see if character is in it --- */
                if (rc.tp+y >= rcc.tp && rc.tp+y <= rcc.bt &&
                        rc.lf+x == rcc.lf)    {
                    /* --- it is --- */
                    if (rc.tp+y == rcc.tp)
                        coll = 0;
                    else if (rc.tp+y == rcc.bt)
                        coll = 2;
                    else
                        coll = 1;
                }
            }
        }
    }
    return coll;
}

static void PaintVector(WINDOW wnd, RECT rc)
{
    int i, cw, fml, vertvect, coll, len;
    unsigned int ch, nc;

    if (rc.rt == rc.lf)    {
        /* ------ vertical vector ------- */
        nc = '3';
        vertvect = 1;
        len = rc.bt-rc.tp+1;
    }
    else     {
        /* ------ horizontal vector ------- */
        nc = 'D';
        vertvect = 0;
        len = rc.rt-rc.lf+1;
    }

    for (i = 0; i < len; i++)    {
        unsigned int newch = nc;
        int xi = 0, yi = 0;
        if (vertvect)
            yi = i;
        else
            xi = i;
        ch = videochar(GetClientLeft(wnd)+rc.lf+xi,
                    GetClientTop(wnd)+rc.tp+yi);
        for (cw = 0; cw < sizeof(CharInWnd); cw++)    {
            if (ch == CharInWnd[cw])    {
                /* ---- hit another vector character ---- */
                if ((coll=FindVector(wnd, rc, xi, yi)) != -1) {
                    /* compute first/middle/last subscript */
                    if (i == len-1)
                        fml = 2;
                    else if (i == 0)
                        fml = 0;
                    else
                        fml = 1;
                    newch = VectCvt[coll][cw][vertvect][fml];
                }
            }
        }
        PutWindowChar(wnd, newch, rc.lf+xi, rc.tp+yi);
    }
}

static void PaintBar(WINDOW wnd, RECT rc, enum VectTypes vt)
{
    int i, vertbar, len;
    unsigned int tys[] = {'[', '2', '1', '0'};
    unsigned int nc = tys[vt-1];

    if (rc.rt == rc.lf)    {
        /* ------ vertical bar ------- */
        vertbar = 1;
        len = rc.bt-rc.tp+1;
    }
    else     {
        /* ------ horizontal bar ------- */
        vertbar = 0;
        len = rc.rt-rc.lf+1;
    }

    for (i = 0; i < len; i++)    {
        int xi = 0, yi = 0;
        if (vertbar)
            yi = i;
        else
            xi = i;
        PutWindowChar(wnd, nc, rc.lf+xi, rc.tp+yi);
    }
}

static void PaintMsg(WINDOW wnd)
{
    int i;
    VECT *vc = wnd->VectorList;
    for (i = 0; i < wnd->VectorCount; i++)    {
        if (vc->vt == VECTOR)
            PaintVector(wnd, vc->rc);
        else
            PaintBar(wnd, vc->rc, vc->vt);
        vc++;
    }
}

static void DrawVectorMsg(WINDOW wnd,PARAM p1,enum VectTypes vt)
{
    if (p1)    {
        wnd->VectorList = realloc(wnd->VectorList,
                sizeof(VECT) * (wnd->VectorCount + 1));
        if (wnd->VectorList != NULL)    {
            VECT vc;
            vc.vt = vt;
            vc.rc = *(RECT *)p1;
            *(((VECT *)(wnd->VectorList))+wnd->VectorCount)=vc;
            wnd->VectorCount++;
        }
    }
}

static void DrawBoxMsg(WINDOW wnd, PARAM p1)
{
    if (p1)    {
        RECT rc = *(RECT *)p1;
        rc.bt = rc.tp;
        SendMessage(wnd, DRAWVECTOR, (PARAM) &rc, TRUE);
        rc = *(RECT *)p1;
        rc.lf = rc.rt;
        SendMessage(wnd, DRAWVECTOR, (PARAM) &rc, FALSE);
        rc = *(RECT *)p1;
        rc.tp = rc.bt;
        SendMessage(wnd, DRAWVECTOR, (PARAM) &rc, TRUE);
        rc = *(RECT *)p1;
        rc.rt = rc.lf;
        SendMessage(wnd, DRAWVECTOR, (PARAM) &rc, FALSE);
    }
}

int PictureProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    switch (msg)    {
        case PAINT:
            BaseWndProc(PICTUREBOX, wnd, msg, p1, p2);
            PaintMsg(wnd);
            return TRUE;
        case DRAWVECTOR:
            DrawVectorMsg(wnd, p1, VECTOR);
            return TRUE;
        case DRAWBOX:
            DrawBoxMsg(wnd, p1);
            return TRUE;
        case DRAWBAR:
            DrawVectorMsg(wnd, p1, p2);
            return TRUE;
        case CLOSE_WINDOW:
            if (wnd->VectorList != NULL)
                free(wnd->VectorList);
            break;
        default:
            break;
    }
    return BaseWndProc(PICTUREBOX, wnd, msg, p1, p2);
}

static RECT PictureRect(int x, int y, int len, int hv)
{
    RECT rc;
    rc.lf = rc.rt = x;
    rc.tp = rc.bt = y;
    if (hv)
        /* ---- horizontal vector ---- */
        rc.rt += len-1;
    else
        /* ---- vertical vector ---- */
        rc.bt += len-1;
    return rc;
}

void DrawVector(WINDOW wnd, int x, int y, int len, int hv)
{
    RECT rc = PictureRect(x,y,len,hv);
    SendMessage(wnd, DRAWVECTOR, (PARAM) &rc, 0);
}

void DrawBox(WINDOW wnd, int x, int y, int ht, int wd)
{
    RECT rc;
    rc.lf = x;
    rc.tp = y;
    rc.rt = x+wd-1;
    rc.bt = y+ht-1;
    SendMessage(wnd, DRAWBOX, (PARAM) &rc, 0);
}

void DrawBar(WINDOW wnd,enum VectTypes vt,int x,int y,int len,int hv)
{
    RECT rc = PictureRect(x,y,len,hv);
    SendMessage(wnd, DRAWBAR, (PARAM) &rc, (PARAM) vt);
}
```

#### LISTING_FOUR

```c
/* ------------- calendar.c ------------- */
#include "dflat.h"

#ifndef TURBOC

#define CALHEIGHT 17
#define CALWIDTH 33

static int DyMo[] = {31,28,31,30,31,30,31,31,30,31,30,31};
static struct tm ttm;
static int dys[42];
static WINDOW Cwnd;

static void FixDate(void)
{
    /* ---- adjust Feb for leap year ---- */
    DyMo[1] = (ttm.tm_year % 4) ? 28 : 29;
#ifndef BCPP
    /* bug in the Borland C++ mktime function prohibits this */
    ttm.tm_mday = min(ttm.tm_mday, DyMo[ttm.tm_mon]);
#endif
}

/* ---- build calendar dates array ---- */
static void BuildDateArray(void)
{
    int offset, dy = 0;
    memset(dys, 0, sizeof dys);
    FixDate();
    /* ----- compute the weekday for the 1st ----- */
    offset = ((ttm.tm_mday-1) - ttm.tm_wday) % 7;
    if (offset < 0)
        offset += 7;
    if (offset)
        offset = (offset - 7) * -1;
    /* ----- build the dates into the array ---- */
    for (dy = 1; dy <= DyMo[ttm.tm_mon]; dy++)
        dys[offset++] = dy;
}
static void CreateWindowMsg(WINDOW wnd)
{
    int x, y;
    DrawBox(wnd, 1, 2, CALHEIGHT-4, CALWIDTH-4);
    for (x = 5; x < CALWIDTH-4; x += 4)
        DrawVector(wnd, x, 2, CALHEIGHT-4, FALSE);
    for (y = 4; y < CALHEIGHT-3; y+=2)
        DrawVector(wnd, 1, y, CALWIDTH-4, TRUE);
}
static void DisplayDates(WINDOW wnd)
{
    int week, day;
    char dyln[10];
    int offset;
    char banner[CALWIDTH-1];
    char banner1[30];

    SetStandardColor(wnd);
    PutWindowLine(wnd, "Sun Mon Tue Wed Thu Fri Sat", 2, 1);
    memset(banner, ' ', CALWIDTH-2);
    strftime(banner1, 16, "%B, %Y", &ttm);
    offset = (CALWIDTH-2 - strlen(banner1)) / 2;
    strcpy(banner+offset, banner1);
    strcat(banner, "    ");
    PutWindowLine(wnd, banner, 0, 0);
    BuildDateArray();
    for (week = 0; week < 6; week++)    {
        for (day = 0; day < 7; day++)    {
            int dy = dys[week*7+day];
            if (dy == 0)
                strcpy(dyln, "   ");
            else    {
                if (dy == ttm.tm_mday)
                    sprintf(dyln, "%c%c%c%2d %c",
                        CHANGECOLOR,
                        SelectForeground(wnd)+0x80,
                        SelectBackground(wnd)+0x80,
                        dy, RESETCOLOR);
                else
                    sprintf(dyln, "%2d ", dy);
            }
            SetStandardColor(wnd);
            PutWindowLine(wnd, dyln, 2 + day * 4, 3 + week*2);
        }
    }
}
static int KeyboardMsg(WINDOW wnd, PARAM p1)
{
    switch ((int)p1)    {
        case PGUP:
            if (ttm.tm_mon == 0)    {
                ttm.tm_mon = 12;
                ttm.tm_year--;
            }
            ttm.tm_mon--;
            FixDate();
            mktime(&ttm);
            DisplayDates(wnd);
            return TRUE;
        case PGDN:
            ttm.tm_mon++;
            if (ttm.tm_mon == 12)    {
                ttm.tm_mon = 0;
                ttm.tm_year++;
            }
            FixDate();
            mktime(&ttm);
            DisplayDates(wnd);
            return TRUE;
        default:
            break;
    }
    return FALSE;
}
static int CalendarProc(WINDOW wnd,MESSAGE msg,PARAM p1,PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:
            DefaultWndProc(wnd, msg, p1, p2);
            CreateWindowMsg(wnd);
            return TRUE;
        case KEYBOARD:
            if (KeyboardMsg(wnd, p1))
                return TRUE;
            break;
        case PAINT:
            DefaultWndProc(wnd, msg, p1, p2);
            DisplayDates(wnd);
            return TRUE;
        case COMMAND:
            if ((int)p1 == ID_HELP)    {
                DisplayHelp(wnd, "Calendar");
                return TRUE;
            }
            break;
        case CLOSE_WINDOW:
            Cwnd = NULL;
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}
void Calendar(WINDOW pwnd)
{
    if (Cwnd == NULL)    {
        time_t tim = time(NULL);
        ttm = *localtime(&tim);
        Cwnd = CreateWindow(PICTUREBOX,
                    "Calendar",
                    -1, -1, CALHEIGHT, CALWIDTH,
                    NULL, pwnd, CalendarProc,
                    SHADOW     |
                    MINMAXBOX  |
                    CONTROLBOX |
                    MOVEABLE   |
                    HASBORDER
        );
    }
    SendMessage(Cwnd, SETFOCUS, TRUE, 0);
}

#endif
```

#### LISTING_FIVE

```c
/* ------------ barchart.c ----------- */
#include "dflat.h"

#define BCHEIGHT 12
#define BCWIDTH 44
#define COLWIDTH 4

static WINDOW Bwnd;

/* ------- project schedule array ------- */
static struct ProjChart {
    char *prj;
    int start, stop;
} projs[] = {
    {"Center St", 0,3},
    {"City Hall", 0,5},
    {"Rt 395   ", 1,4},
    {"Sky Condo", 2,3},
    {"Out Hs   ", 0,4},
    {"Bk Palace", 1,5}
};

static char *Title =  "              PROJECT SCHEDULE";
static char *Months = "           Jan Feb Mar Apr May Jun";

static int BarChartProc(WINDOW wnd, MESSAGE msg,PARAM p1, PARAM p2)
{
    switch (msg)    {
        case COMMAND:
            if ((int)p1 == ID_HELP)    {
                DisplayHelp(wnd, "BarChart");
                return TRUE;
            }
            break;
        case CLOSE_WINDOW:
            Bwnd = NULL;
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}
void BarChart(WINDOW pwnd)
{
    int pct = sizeof projs / sizeof(struct ProjChart);
    int i;

    if (Bwnd == NULL)    {
        Bwnd = CreateWindow(PICTUREBOX,
                    "BarChart",
                    -1, -1, BCHEIGHT, BCWIDTH,
                    NULL, pwnd, BarChartProc,
                    SHADOW     |
                    CONTROLBOX |
                    MOVEABLE   |
                    HASBORDER
        );
        SendMessage(Bwnd, ADDTEXT, (PARAM) Title, 0);
        SendMessage(Bwnd, ADDTEXT, (PARAM) "", 0);
        for (i = 0; i < pct; i++)    {
            SendMessage(Bwnd,ADDTEXT,(PARAM)projs[i].prj,0);
            DrawBar(Bwnd, SOLIDBAR+(i%4),
                11+projs[i].start*COLWIDTH, 2+i,
                (1 + projs[i].stop-projs[i].start) * COLWIDTH,
                TRUE);
        }
        SendMessage(Bwnd, ADDTEXT, (PARAM) "", 0);
        SendMessage(Bwnd, ADDTEXT, (PARAM) Months, 0);
        DrawBox(Bwnd, 10, 1, pct+2, 25);
    }
    SendMessage(Bwnd, SETFOCUS, TRUE, 0);
}
```

Copyright © 1992, Dr. Dobb's Journal
