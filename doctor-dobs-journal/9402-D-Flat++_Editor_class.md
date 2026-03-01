
# C PROGRAMMING

## D-Flat++ Editor klasa - Al Stevens

D-Flat++ biblioteka je blizu završetka. Radim na nekim popravkama – međuspremniku, nekim uobičajenim dijalozima i sistemu pomoći – i još uvek postoji nekoliko malih grešaka pod nogama. Imaću novu verziju spremnu za preuzimanje dok ovo pročitate.

Ovomesečna tema je klasa Editor, koja implementira višelinijski uređivač teksta. Prošlog meseca sam opisao klasu EditBox, kontrolu za uređivanje teksta u jednom redu koju koriste dijaloški okviri. Klasa Editor je izvedena iz klase EditBok, dodajući stvari koje su potrebne višelinijskom uređivaču, kao što je prelamanje reči. Osnovna klasa TextBox brine o stranicama i pomeranju. Klasa EditBok je izvedena iz klase TektBok. Klasa TektBok takođe rukuje označavanjem izabranih blokova teksta za operacije međuspremnika.

Urednici teksta su omiljena tema razgovora među programerima. Da biste videli zašto, postavite CompuServe poruku o svom omiljenom uređivaču. Pokrenućete niz mnogih poruka i mnogo različitih i nepokolebljivih mišljenja. Bliskoistočni mirovni sporazum je festival jagoda u poređenju sa navođenjem programera da se slože oko urednika. Pisci postaju slično šovinistički nastrojeni prema svojim procesorima teksta, iako u manjoj meri. Bez obzira koje funkcije ugradite u uređivač, one će biti ili nedovoljno, previše ili će biti nepravilno implementirane. Urednik će biti prevelik, prespor i previše za razliku od programa [ovde ime vašeg urednika]. Uz to, idemo dalje sa D-Flat++ editorom.

[LISTING_ONE](#listing_one) i [LISTING_TWO](#listing_two) su "editor.h" i "editor.cpp", izvorne datoteke koje deklarišu i definišu klasu Editor. Klasa dodaje tri člana podataka, vrednost za proširenje tabulatora, oznaku koja označava da li je uređivač u režimu prelamanja reči i ceo broj sa brojem reda tekstualnog dokumenta. Osnovna klasa EditBok se već brine o koloni.

Klasa Editor ima funkcije člana za rukovanje pomeranjem kursora nagore, nadole, napred i nazad i za upravljanje stranicama. Ove operacije pomeraju pokazivač umetanja uređivača, koji podržava unos sa tastature kao i druge funkcije. Oni takođe menjaju prikaz stranicama i skrolovanjem kada je to potrebno. Klasa EditBok ima operacije kursora unapred i unazad, a klasa Editor ih koristi, ali dodaje logiku da pomeri pokazivač umetanja napred preko kraja reda do sledećeg i unazad preko početka reda do prethodnog. Klasa TektBok upravlja stranicama, ali klasa Editor presreće operacije da ažurira svoje pokazivače umetanja.

### Kartice uređivača

Klasa Editor ima privatne funkcije člana za upravljanje karticama u tekstu. Čitaoci su se uvek pitali zašto uređivač D-Flat C biblioteke ne obrađuje proširenje kartica, pa sam odlučio da ovu funkciju ugradim u D-Flat++. Razumevanje šta je to zahtevalo otkriva zašto nisam požurio da stavim funkciju u C biblioteku.

Nije tako lako pratiti tabove u tekstu dok je tekst u memoriji. DF++ tekstualne klase čuvaju tekst kao bafer redova koji se završava nulom, a svaki je završen znakom novog reda. Paragrafi se završavaju praznim redovima. Svaki tabulator u tekstu je praćen pseudotab znakovima koji predstavljaju broj razmaka do sledeće tabulatorske tačke. Program izračunava broj takvih umetanja, što zavisi od toga koliko je daleko do sledeće kartice. Taj broj je funkcija mesta gde se u tekstualnoj liniji pojavljuje tabulator i širine samih kartica. Funkcija Paint prepoznaje ove znakove i prikazuje ih kao razmake. Funkcije pomeranja kursora obezbeđuju da kursor ne padne na jednu od pozicija proširenja pseudokartica na ekranu. Funkcije znaka za brisanje brišu znakove za proširenje kad god korisnik izbriše znak tabulatora.

Logika proširenja tabulatora postaje dlakava kada korisnik ubacuje ili briše znakove u liniji ili reformiše pasus. U prvom slučaju, proširenja za sledeće kartice u istoj liniji moraju da se podese. U slučaju reformi pasusa, koje se dešavaju tokom preloma reči ili naredbom korisnika, program bi morao da prilagodi tabove za ceo pasus da bi održao integritet kartice. Za sada, jednostavno "de-tab" pasus, što znači da su tabulatori i znakovi za umetanje tabulatora zamenjeni razmacima.

Postoje kompromisi između načina na koji klasa Editor čuva tekst i načina na koji to rade drugi uređivači. Jedna tehnika skladišti pasuse kao linije koje se završavaju novim redom, prelamanja reči u hodu i vrši proširenje kartice i sažimanje samo kada slikate ekran. Ova tehnika uključuje složenije upravljanje linijama teksta i karticama i kretanje kursora od one koju sam koristio. Ako bih napravio ozbiljan program za obradu teksta sa D-Flat++ (malo verovatno, mogao bih da dodam) verovatno bih napravio takvu klasu samo da bih dobio poboljšanja performansi koja bi ponudila. Međutim, klasa DF++ Editor je više nego adekvatna za jednostavne aplikacije za uređivanje teksta i koristi jednostavniji kod od drugih uređivača.

### Velika debata: preobraditi ili ne

Mnogo literature o C++ govori nam da C++ čini C preprocesor zastarelim. Po pravilu, treba izbegavati pretprocesor, osim direktiva #include i vremena kompajliranja koje menjaju prevedeni izlaz (da bi se isključio kod za otklanjanje grešaka, na primer). Mnogi autori tvrde da inline funkcije čine makroe nepotrebnim i nepoželjnim jer eliminišu neželjene efekte i proveravaju parametre. Tako i rade. Autori dalje navode da const objekti mogu zameniti #define za povezivanje vrednosti sa globalnim simbolima. Kao i kod inline liste parametara funkcija, oni tvrde, const objekti uključuju tipove i, prema tome, takođe uživaju u bezbednosti tipova. Tako i rade. (Uvek sam se pitao zašto ih zovu „konstantne promenljive“. Kako konstantni objekat može biti promenljiv?)

Imajući u vidu svu ovu toplu, udobnu proveru tipa, većina programera se razumljivo opire povratku u stare dane bezobzirnog C programiranja. Oni zaključuju da nešto tako loše kao što je #define bez tipa nikada više nije potrebno.

Ali uvek postoje izuzeci. Ponekad naletite na njih baš kada pokušavate da uradite pravu stvar. Razmotrite ovaj poznati makro:

```c
#define min(a,b) ((a)<(b)?(a):(b))
```

Mogu postojati neželjeni efekti, kao što se vidi kada kodirate ovu upotrebu makroa:

```c
a = min(b++, c++);
```

U zavisnosti od toga koja je vrednost veća, jedna od promenljivih se uvećava dva puta. C programeri su rano naučili da ne pišu kod koji bi mogao da generiše takve neželjene efekte. Ponekad, međutim, ne možete biti sigurni. Na primer, neki prevodioci implementiraju porodicu funkcija ctipe kao makroe; drugi ih grade kao funkcije.

C++ programmers solve the side-effect issue by using inline functions such as this one:

```c
inline int min(int a, int b)
{
  return (a < b ? a : b);
}
```

Nema neželjenih efekata sa ugrađenom verzijom makroa/funkcije. Štaviše, postoji provera tipa, što je i dobro i loše. Provera tipa je sama po sebi dobra. Međutim, ne možete koristiti inline min funkciju osim ako se oba tipa argumenata ne mogu implicitno konvertovati u celobrojne vrednosti. Drugi tipovi koji nemaju konstruktore konverzije da ih konvertuju u numeričke tipove ne rade sa ovom inline min funkcijom. Trebala bi vam preopterećena min funkcija za svaki takav tip.

C++ rešava problem sa više funkcija sa klasom šablona, kao što je prikazano ovde.

```c
template<class T>
T min(T a, T b)
{
  return (a < b ? a : b);
}
```

Sada, jedan mali makro odgovara svim tipovima sve dok imaju manje od relacionog operatora. Možete kodirati sledeće izjave koristeći isti šablon makroa.

```c
int a, b, c;
a = min(b, c);

String s1, s2;
s1 = min(s2, s3);
```

Ne štedite sobu korišćenjem šablona. Svaka upotreba različitog tipa generiše sopstvenu kopiju koda u toku izvođenja, ali barem ne morate da održavate nekoliko različitih verzija izvornog koda istog makroa.

Rešenje šablona nije savršeno. Objekti koji se porede moraju biti istog tipa, bez obzira na implicitna ili programirana pravila konverzije. I tu dolazi do pitanja const vs. #define.

Koristim šablonsku verziju minimalnog makroa u D-Flat++. Podigao se i ugrizao me kada sam pokušao da ga upotrebim sa argumentom const. Program za uređivanje primera koji koristim da testiram i demonstriram D-Flat++ svoju specifičnu klasu aplikacije izvodi iz DF++ generičke klase Application. Korisnici mogu da menjaju veličinu prozora aplikacija, a ja sam morao da postavim minimalnu veličinu tako da prozor ne bude suviše mali da bi mogao da drži traku menija i druge stvari. To je dovoljno lako učiniti: presretnite funkciju poruke o veličini i prilagodite njene parametre visine i širine pre nego što je pustite da prođe. Kodirao sam to otprilike ovako:

```c
const int MinimumWidth = 40;
Ted::Size(int x, int y)
{
  // _
  x = min(x, MinimumWidth);
}
```

Prevodilac je odbio upotrebu. Ne postoji, kako je rečeno, min funkcija koja očekuje int i const int kao parametre. Ni prebacivanje argumenta k na const int nije uspelo. Kompajler Borlanda je ignorisao glumce. Nisam siguran zašto, ali je tako.

Uzgred, ova diskusija se zasniva na tome kako funkcioniše Borland C++ 3.1. Drugi kompajleri pokazuju drugačije ponašanje i o njima ću razgovarati sledećeg meseca.

Postoje i druga rešenja, jedno vrlo jednostavno i neka druga koja su donekle izmišljena. (Čak i da je cast funkcionisao, to bi takođe bila izmišljotina.) Pretpostavite za ovu diskusiju da promenljiva k mora da bude nekonstantna. Možete deklarisati objekat MinimumVidth kao non-const, ali onda biste morali da ga stavite u izvršnu datoteku izvornog koda. Trebaće vam eksterna deklaracija u zaglavlju da biste je učinili globalno vidljivom. Možete dodeliti const objekat ne-const promenljivoj i koristiti promenljivu non-const u pozivu min. Ili obrnuto. Možete da vratite vrednost const int iz ne-const funkcije člana klase i koristite tu vrednost. Možete pronaći mnogo takvih rešenja, od kojih svako uključuje dodatni kod. Možete čak i prepisati funkciju šablona ovako:

```c
template<class T, class U>
T min(T a, U b)
{
  return (a < b ? a : b);
}
```

Na površini, ovo bi se činilo najelegantnijim od smišljanja, i radi u nekim slučajevima, ali može izazvati neke sopstvene neželjene efekte. Koji god tip da kodirate kao prvi argument u pozivu ovog makroa, postaje tip rezultata. Kao rezultat toga može doći do neželjenog skraćenja konverzije. Na primer, sledeći izraz, kada se koristi taj makro, bi dodelio vrednost 1 promenljivoj foo.

```c
float foo = min(4, 1.23);
```

Obrnite argumente i izraz vraća 1.23. To je vrsta nedoslednog ponašanja koja stvara bube u dubokom svemiru, crne rupe.

To su izmišljena rešenja i možete koristiti jedno od njih. S druge strane, možete izabrati jednostavno rešenje – nesigurno, staromodno, zastarelo koje vam više ne treba ili želite – i ono koje funkcioniše. Deklaraciju MinimumVidth možete da promenite u ovu izjavu:

```c
#define MinimumWidth 40
```

Ponekad malo domišljatosti jednostavno nadmaši mnoge konvencionalne mudrosti.

#### LISTING_ONE

```c
// -------- editor.h

#ifndef EDITOR_H
#define EDITOR_H

#include "editbox.h"

const unsigned char Ptab = '\t'+0x80; // pseudo tab expansion
#define MaxTab 12                     // maximum tab width

class Editor : public EditBox    {
    int tabs;           // tab expansion value
    Bool wordwrapmode;  // True = wrap words
    int row;            // Current row
    void OpenWindow();
    void ScrollCursor();
    void AdjustCursorTabs(int key = 0);
    void ExtendBlock(int x, int y);
    void InsertTab();
    void AdjustTabInsert();
    void AdjustTabDelete();
    void WordWrap();
    Bool AtBufferStart()
        { return (Bool) (column == 0 && row == 0); }
protected:
    virtual void Upward();
    virtual void Downward();
    virtual void Forward();
    virtual void Backward();
    virtual void DeleteCharacter();
    virtual void BeginDocument();
    virtual void EndDocument();
    virtual Bool PageUp();
    virtual Bool PageDown();
    virtual void InsertCharacter(int key);
    virtual void PaintCurrentLine()
        { WriteTextLine(row); }
    virtual Bool ResetCursor();
    virtual void WriteString(String &ln,
                        int x, int y, int fg, int bg);
    virtual void LeftButton(int mx, int my);
public:
    Editor(const char *ttl, int lf, int tp, int ht, int wd,
                                            DFWindow *par=0)
                        : EditBox(ttl, lf, tp, ht, wd, par)
            { OpenWindow(); }
    Editor(const char *ttl, int ht, int wd, DFWindow *par=0)
                        : EditBox(ttl, ht, wd, par)
            { OpenWindow(); }
    Editor(int lf, int tp, int ht, int wd, DFWindow *par=0)
                        : EditBox(lf, tp, ht, wd, par)
            { OpenWindow(); }
    Editor(int ht, int wd, DFWindow *par=0)
                        : EditBox(ht, wd, par)
            { OpenWindow(); }
    Editor(const char *ttl) : EditBox(ttl)
            { OpenWindow(); }
    virtual void Keyboard(int key);
    virtual unsigned char CurrentChar()
        { return *(TextLine(row) + column); }
    virtual unsigned CurrentCharPosition()
        { return (unsigned)
            ((const char *)
            (TextLine(row)+column) - (const char *) *text);
        }
    virtual void FormParagraph();
    virtual void AddText(const String& txt);
    virtual const String GetText();
    virtual void ClearText();
    virtual int GetRow() const { return row; }
    int Tabs()
        { return tabs; }
    void SetTabs(int t);
    Bool WordWrapMode()
        { return wordwrapmode; }
    void SetWordWrapMode(Bool wmode)
        { wordwrapmode = wmode; }
    virtual void DeleteSelectedText();
    virtual void InsertText(const String& txt);
};

#endif
```

#### LISTING_TWO

```c
// ----- editor.cpp

#include "editor.h"
#include "desktop.h"

// ----------- common constructor code
void Editor::OpenWindow()
{
    windowtype = EditorWindow;
    row = 0;
    tabs = 4;
    insertmode = desktop.keyboard().InsertMode();
    wordwrapmode = True;
    DblBorder = False;
}
// ---- keep the cursor out of tabbed space
void Editor::AdjustCursorTabs(int key)
{
    while (CurrentChar() == Ptab)
        key == FWD ? column++ : --column;
    ResetCursor();
}
// -------- process keystrokes
void Editor::Keyboard(int key)
{
    int svwtop = wtop;
    int svwleft = wleft;
    switch (key)    {
        case '\t':
            InsertTab();
            BuildTextPointers();
            PaintCurrentLine();
            ResetCursor();
            break;
        case ALT_P:
            FormParagraph();
            break;
        case UP:
            Upward();
            TestMarking();
            break;
        case DN:
            Downward();
            TestMarking();
            break;
        case CTRL_HOME:
            BeginDocument();
            TestMarking();
            break;
        case CTRL_END:
            EndDocument();
            TestMarking();
            break;
        case '\r':
            InsertCharacter('\n');
            BuildTextPointers();
            ResetCursor();
            Paint();
            break;
        case DEL:
        case RUBOUT:
            visible = False;
            EditBox::Keyboard(key);
            visible = True;
            BuildTextPointers();
            PaintCurrentLine();
            ResetCursor();
            break;
        default:
            EditBox::Keyboard(key);
            break;
    }
    if (svwtop != wtop || svwleft != wleft)
        Paint();
}
// --- move the cursor forward one character
void Editor::Forward()
{
    if (CurrentChar())    {
        if (CurrentChar() == '\n')    {
            Home();
            Downward();
        }
        else
            EditBox::Forward();
        AdjustCursorTabs(FWD);
    }
}
// --- move the cursor back one character
void Editor::Backward()
{
    if (column)
        EditBox::Backward();
    else if (row)    {
        Upward();
        End();
    }
    AdjustCursorTabs();
}
// ---- if cursor moves out of the window, scroll
void Editor::ScrollCursor()
{
    if (column < wleft || column >= wleft + ClientWidth())    {
        wleft = column;
        Paint();
    }
}
// --- move the cursor up one line
void Editor::Upward()
{
    if (row)    {
        if (row == wtop)
            ScrollDown();
        --row;
        AdjustCursorTabs();
        ScrollCursor();
    }
}
// --- move the cursor down one line
void Editor::Downward()
{
    if (row < wlines)    {
        if (row == wtop + ClientHeight() - 1)
            ScrollUp();
        row++;
        AdjustCursorTabs();
        ScrollCursor();
    }
}
// --- move the cursor to the beginning of the document
void Editor::BeginDocument()
{
    row = 0;
    wtop = 0;
    EditBox::Home();
    AdjustCursorTabs();
}
// --- move the cursor to the end of the document
void Editor::EndDocument()
{
    TextBox::End();
    row = wlines-1;
    End();
    AdjustCursorTabs();
}
// --- keep cursor in the window, in text and out of empty space
Bool Editor::ResetCursor()
{
    KeepInText(column, row);
    if (EditBox::ResetCursor())    {
        if (!(row >= wtop && row < wtop+ClientHeight()))    {
            desktop.cursor().Hide();
            return False;
        }
    }
    return True;
}
// ------- page up one screenfull
Bool Editor::PageUp()
{
    if (wlines)    {
        row -= ClientHeight();
        if (row < 0)
            row = 0;
        EditBox::PageUp();
        AdjustCursorTabs();
        return True;
    }
    return False;
}
// ------- page down one screenfull
Bool Editor::PageDown()
{
    if (wlines)    {
        row += ClientHeight();
        if (row >= wlines)
            row = wlines-1;
        EditBox::PageDown();
        AdjustCursorTabs();
        return True;
    }
    return False;
}
// --- insert a tab into the edit buffer
void Editor::InsertTab()
{
    visible = False;
    if (insertmode)    {
        EditBox::InsertCharacter('\t');
        while ((column % tabs) != 0)
            EditBox::InsertCharacter(Ptab);
    }
    else
        do
            Forward();
        while ((column % tabs) != 0);
    visible = True;
}
// --- When inserting char, adjust next following tab, same line
void Editor::AdjustTabInsert()
{
    visible = False;
    // ---- test if there is a tab beyond this character
    int savecol = column;
    while (CurrentChar() && CurrentChar() != '\n')    {
        if (CurrentChar() == '\t')    {
            column++;
            if (CurrentChar() == Ptab)
                EditBox::DeleteCharacter();
            else
                for (int i = 0; i < tabs-1; i++)
                    EditBox::InsertCharacter(Ptab);
            break;
        }
        column++;
    }
    column = savecol;
    visible = True;
}
// --- test for wrappable word and wrap it
void Editor::WordWrap()
{
    // --- test for word wrap
    int len = LineLength(row);
    int wd = ClientWidth()-1;
    if (len >= wd)    {
        const char *cp = TextLine(row);
        char ch = *(cp + wd);
        // --- test words beyond right margin
        if (len > wd || (ch && ch != ' ' && ch != '\n'))    {
            // --- test typing in last word in window's line
            const char *cw = cp + wd;
            cp += column;
            while (cw > cp)    {
                if (*cw == ' ')
                    break;
                --cw;
            }
            int newcol = 0;
            if (cw <= cp)    {
                // --- user was typing last word on line
                // --- find beginning of the word
                const char *cp1 = TextLine(row);
                const char *cw1 = cw;
                while (*cw1 != ' ' && cw1 > cp1)
                    --cw1, newcol++;
                wleft = 0;
            }
            FormParagraph();
            if (cw <= cp)    {
                // --- user was typing last word on line
                column = newcol;
                if (cw == cp)
                    --column;
                row++;
                if (row - wtop >= ClientHeight())
                    ScrollUp();
                ResetCursor();
            }
        }
    }
}
// --- insert a character at the current cursor position
void Editor::InsertCharacter(int key)
{
    if (insertmode)    {
        if (key != '\n')
            AdjustTabInsert();
    }
    else if (CurrentChar() == '\t')    {
        // --- overtyping a tab
        visible = False;
        column++;
        while (CurrentChar() == Ptab)
            EditBox::DeleteCharacter();
        --column;
    }
    visible = False;
    EditBox::InsertCharacter(key);
    visible = True;
    ResetCursor();
    if (wordwrapmode)
        WordWrap();
}
// --- When deleting char, adjust next following tab, same line
void Editor::AdjustTabDelete()
{
    visible = False;
    // ---- test if there is a tab beyond this character
    int savecol = column;
    while (CurrentChar() && CurrentChar() != '\n')    {
        if (CurrentChar() == '\t')    {
            column++;
            // --- count pseudo tabs
            int pct = 0;
            while (CurrentChar() == Ptab)
                pct++, column++;
            if (pct == tabs-1)    {
                column -= tabs-1;
                for (int i = 0; i < tabs-1; i++)
                    EditBox::DeleteCharacter();
            }
            else
                EditBox::InsertCharacter(Ptab);
            break;
        }
        column++;
    }
    column = savecol;
    visible = True;
}
// --- delete the character at the current cursor position
void Editor::DeleteCharacter()
{
    if (CurrentChar() == '\0')
        return;
    if (insertmode)
        AdjustTabDelete();
    if (CurrentChar() == '\t')    {
        // --- deleting a tab
        EditBox::DeleteCharacter();
        while (CurrentChar() == Ptab)
            EditBox::DeleteCharacter();
        return;
    }
    const char *cp = TextLine(row);
    const char *cw = cp + column;
    const Bool delnewline = (Bool) (*cw == '\n');
    const Bool reform = (Bool) (delnewline && *(cw+1) != '\n');
    const Bool lastnewline =
                        (Bool) (delnewline && *(cw+1) == '\0');
    int newcol = 0;
    if (reform && !lastnewline)    {
        // --- user is deleting /n, find beginning of last word
        while (*--cw != ' ' && cw > cp)
            newcol++;
    }
    EditBox::DeleteCharacter();
    if (lastnewline)
        return;
    if (delnewline && !reform)    {
        // --- user deleted a blank line
        visible = True;
        BuildTextPointers();
        Paint();
        return;
    }
    if (wordwrapmode && reform)    {
        // --- user deleted /n
        wleft = 0;
        FormParagraph();
        if (CurrentChar() == '\n')    {
            // ---- joined the last word with next line's
            //      first word and then wrapped the result
            column = newcol;
            row++;
        }
    }
}
// --- form a paragraph from the current cursor position
//    through one line before the next blank line or end of text
void Editor::FormParagraph()
{
    int BegCol, FirstLine;
    const char *blkBegLine, *blkEndLine, *blkBeg;

    // ---- forming paragraph from cursor position
    FirstLine = wtop + row;
    blkBegLine = blkEndLine = TextLine(row);
    if ((BegCol = column) >= ClientWidth())
        BegCol = 0;
    // ---- locate the end of the paragraph
    while (*blkEndLine)    {
        Bool blank = True;
        const char *BlankLine = blkEndLine;
        // --- blank line marks end of paragraph
        while (*blkEndLine && *blkEndLine != '\n')    {
            if (*blkEndLine != ' ')
                blank = False;
            blkEndLine++;
        }
        if (blank)    {
            blkEndLine = BlankLine;
            break;
        }
        if (*blkEndLine)
            blkEndLine++;
    }
    if (blkEndLine == blkBegLine)    {
        visible = True;
        Downward();
        return;
    }
    if (*blkEndLine == '\0')
        --blkEndLine;
    if (*blkEndLine == '\n')
        --blkEndLine;
    // --- change newlines, tabs, and tab expansions to spaces
    blkBeg = blkBegLine;
    while (blkBeg < blkEndLine)    {
        if (*blkBeg == '\n' || ((*blkBeg) & 0x7f) == '\t')    {
            int off = blkBeg - (const char *)*text;
            (*text)[off] = ' ';
        }
        blkBeg++;
    }
    // ---- insert newlines at new margin boundaries
    blkBeg = blkBegLine;
    while (blkBegLine < blkEndLine)    {
        blkBegLine++;
        if ((int)(blkBegLine - blkBeg) == ClientWidth()-1)    {
            while (*blkBegLine != ' ' && blkBegLine > blkBeg)
                --blkBegLine;
            if (*blkBegLine != ' ')    {
                blkBegLine = strchr(blkBegLine, ' ');
                if (blkBegLine == NULL ||
                        blkBegLine >= blkEndLine)
                    break;
            }
            int off = blkBegLine - (const char *)*text;
            (*text)[off] = '\n';
            blkBeg = blkBegLine+1;
        }
    }
    BuildTextPointers();
    changed = True;
    // --- put cursor back at beginning
    column = BegCol;
    if (FirstLine < wtop)
        wtop = FirstLine;
    row = FirstLine - wtop;
    visible = True;
    Paint();
    ResetCursor();
}
// --------- add a line of text to the editor textbox
void Editor::AddText(const String& txt)
{
    // --- compute the buffer size based on tabs in the text
    const char *tp = txt;
    int x = 0;
    int sz = 0;
    while (*tp)    {
        if (*tp == '\t')    {
            // --- tab, adjust the buffer length
            int sps = Tabs() - (x % Tabs());
            sz += sps;
            x += sps;
        }
        else    {
            // --- not a tab, count the character
            sz++;
            x++;
        }
        if (*tp == '\n')
            x = 0;    // newline, reset x
        tp++;
    }
    // --- allocate a buffer
    char *ep = new char[sz];
    // --- detab the input file
    tp = txt;
    char *ttp = ep;
    x = 0;
    while (*tp)    {
        // --- put the character (\t, too) into the buffer
        *ttp++ = *tp;
        x++;
        // --- expand tab into \t and expansions (\t + 0x80)
        if (*tp == '\t')
            while ((x % Tabs()) != 0)
                *ttp++ = Ptab, x++;
        else if (*tp == '\n')
            x = 0;
        tp++;
    }
    *ttp = '\0';
    // ---- add the text to the editor window
    EditBox::AddText(String(ep));
}
// ------- retrieve editor text collapsing tabs
const String Editor::GetText()
{
    char *tx = new char[text->Strlen()+1];
    const char *tp = (const char *) *text;
    char *nt = tx;
    while (*tp)    {
        if (*(const unsigned char *)tp != Ptab)
            *tx++ = *tp;
        tp++;
    }
    *tx = '\0';
    String temp(nt);
    return nt;
}
// --- write a string to the editor window
void Editor::WriteString(String &ln,int x,int y,int fg,int bg)
{
    String nln(ln.Strlen());
    int ch;
    for (int i = 0; i < ln.Strlen(); i++)    {
        ch = ln[i];
        nln[i] = (ch & 0x7f) == '\t' ? ' ' : ch;
    }
    EditBox::WriteString(nln, x, y, fg, bg);
}

// ---- left mouse button pressed
void Editor::LeftButton(int mx, int my)
{
    if (ClientRect().Inside(mx, my))    {
        column = mx-ClientLeft()+wleft;
        row = my-ClientTop()+wtop;
        ResetCursor();
    }
    TextBox::LeftButton(mx, my);
}
// --------- clear the text from the editor window
void Editor::ClearText()
{
    row = 0;
    EditBox::ClearText();
}
// ------ extend the marked block
void Editor::ExtendBlock(int x, int y)
{
    row = y;
    EditBox::ExtendBlock(x, y);
}
// ---- delete a marked block
void Editor::DeleteSelectedText()
{
    if (TextBlockMarked())    {
        row = BlkBegLine;
        column = BlkBegCol;
        if (row < wtop || row >= wtop + ClientHeight())
            wtop = row;
        if (column < wleft || column >= wleft + ClientWidth())
            wleft = column;
        EditBox::DeleteSelectedText();
        FormParagraph();
    }
}
// ---- insert a string into the text
void Editor::InsertText(const String& txt)
{
    EditBox::InsertText(txt);
    FormParagraph();
}
// ---- set tab width
void Editor::SetTabs(int t)
{
    if (t && tabs != t && t <= MaxTab)    {
        tabs = t;
        if (text != 0)    {
            String ln;
            // ------- retab the text
            for (int lno = 0; lno < wlines; lno++)    {
                // --- retrieve a line at a time
                ln = ExtractTextLine(lno);
                int len = ln.Strlen();
                String newln(len * t);
                // --- copy string, collapsing old tabs
                //     and expanding new tabs
                unsigned int ch;
                for (int x2 = 0, x1 = 0; x2 < len; x2++)    {
                    ch = ln[x2] & 0xff;
                    if (ch == Ptab)
                        // --- collapse old tab expansion
                        continue;
                    // --- copy text
                    newln[x1++] = ch;
                    if (ch == '\t')
                        // --- expand new tabs
                        while ((x1 % t) != 0)
                            newln[x1++] = Ptab;
                }
                newln[x1] = '\0';
                newln += "\n";

                // --- compute left segment length
                unsigned seg1 = (unsigned)
                    (TextLine(lno) - (const char *) *text);
                // --- compute right segment length
                unsigned seg2 = 0;
                if (lno < wlines+1)
                    seg2 = (unsigned) text->Strlen() -
                    (TextLine(lno+1) - (const char *) *text);
                // --- rebuild the text from the three parts
                String lft = text->left(seg1);
                String rht = text->right(seg2);
                *text = lft+newln+rht;
                BuildTextPointers();
            }
            Paint();
        }
    }
}
```

Copyright © 1994, Dr. Dobb's Journal
