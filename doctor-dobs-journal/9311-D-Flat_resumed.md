
# C PROGRAMMING 9311

## D-Flat++ Resumed - Al Stevens

### Nazad na D-Flat++

Ovog meseca se vraćamo na D-Flat++. Odložio sam projekat na pola godine dok sam radio na drugim stvarima. Pregledaću šta je D-Flat++ i šta smo do sada pokrili, da bih vas obavestio o novostima.

D-Flat++ je potomak D-Flat-a, koji je biblioteka funkcija C koja implementira korisnički interfejs Common User Access (CUA) u DOS okruženju u tekstualnom režimu. Tu biblioteku sam objavio u ovoj rubrici. Trebalo je oko godinu i po dana da se pokrije cela stvar. Kada sam počeo da prepisujem D-Flat kao biblioteku klasa C++ (formalno nazvana "D-Flat++" i skraćeno nazvana "DF++"), odlučio sam da izostavim neke od karakteristika koje D-Flat ima, uglavnom zato što ih nikada nisam koristio, i dalje sam odlučio da objavim samo onaj deo koda koji je dovoljno interesantan da opravda diskusiju kako projekat ne bi trajao toliko dugo. Možete dobiti sav izvorni kod tako što ćete ga preuzeti ili poslati za verziju za brigu. Objasniću kako to funkcioniše kasnije u ovoj kolumni. Možete dobiti nazad izdanja DDJ-a da nadoknadite diskusije. Brojevi od novembra 1992. do aprila 1993. pokrivaju sve do sada. DDJ CD-ROM uključuje sve D-Flat kao i D-Flat++.

Do ove tačke u DF++ projektu smo razgovarali o radnoj površini, aplikaciji i nekim kontrolnim prozorima. DF++ aplikacija se pokreće sa virtuelne radne površine koja sadrži ekran, tastaturu, miš, sat i zvučnik. Radna površina i uređaji su predstavljeni klasama u biblioteci. Aplikacija je osnovna klasa iz koje izvodite prilagođenu klasu aplikacije. Meni aplikacije je definisan kao deo izvedene klase. Vaša izvedena klasa aplikacije uključuje funkcije člana koje se izvršavaju kada korisnik izabere izbore menija.

Kontrolni prozori su okviri za tekst, okviri za uređivanje, dugmad, potvrdni okviri, okviri sa listom itd. Svaki od njih je implementiran u klase koje su izvedene iz Control klase. Svi prozori u DF++ su izvedeni iz osnovne klase DFWindow.

### Prenosivost

U ranijoj koloni, raspravljao sam o sloju prenosivosti koji omogućava DF++ kompajliranje i sa Borland C++ i Microsoft C++. Nadao sam se da će funkcionisati sa tim kompajlerima, a možda i sa nekim drugim. Odustao sam od Zortech-a kada nisam mogao da dobijem pouzdanu tehničku podršku od njih na CompuServe-u. Sada sam privremeno napustio i Microsoft jer MSC/C++ kompajler još uvek ne podržava šablone. Kada počnete da koristite šablone, ne možete bez njih. Kada vreme dozvoli, preneću DF++ na Comeau C++ (CFRONT port) i novi Watcom C++ kompajler, od kojih oba podržavaju šablone. Do tada vam je potreban Borland C++ 3.1 ili noviji za kompajliranje DF++.

Pokušavam da izbegnem klase kontejnera i šablone koji dolaze sa nekim kompajlerima. Nije da nisu dobri alati, samo još uvek ne postoje standardi, a različite implementacije su različite jedna od druge i verovatno različite od onoga što ANSI komitet smisli. Zato ćete naći šablon Tree i String klasu i druge slične stvari u paketu u DF++. Pokušaću da ispoštujem kada ANSI objavi nacrt, odnosno ako ne budem na stolici za ljuljanje u penzionerskom selu Starih Kolumnista.

### Stari pas, novi trik

Naučio sam lekciju o dizajnu klasa koju još nisam video ni u jednoj od naprednih knjiga u stilu C++. Znate na koje mislim; oni vam govore kako i kada da koristite ovu ili onu jezičku funkciju, a kada ne. Uopšteno je poznato da članove podataka treba da učinite privatnim i omogućite im pristup preko interfejsa funkcije člana. Sve knjige vam to govore. Razlozi su zasnovani na zdravim principima objektno orijentisanog dizajna. Kada zaštitite programera koji koristi klasu od detalja implementacije, štitite podatke. Olakšavate sebi i kasnije, kada želite da izmenite implementaciju. Obezbeđivanjem interfejsa funkcije i skrivanjem implementacije, možete promeniti implementaciju bez uticaja na korisnički kod.

Evo još jedne konvencije koju treba razmotriti. Svi znamo da samo funkcije člana klase mogu pristupiti privatnim članovima podataka. Ako ti članovi podataka imaju javni interfejs, članovi klase bi takođe trebalo da ga koriste. Zašto? Jer kada se spremate da promenite tu implementaciju, promenićete mnogo manje koda implementacije ako članovi koriste javni interfejs.

Kako sam došao do tog zaključka? Ništa vredno znati ne dolazi lako. DF++ ima osnovnu klasu DFWindow koja rukuje svim operacijama koje su zajedničke za sve prozore. Sve ostale klase prozora su izvedene iz DFWindow. Jedna od njegovih funkcija je da održava odnos roditelj/dete/sestra između prozora. Pre šablona, ugradio sam pokazivače glave, repa i povezane liste u samu klasu DFWindow. Nedavno sam odlučio da koristim klasu porodice koju sam dizajnirao na osnovu klase LinkedList o kojoj sam raspravljao pre nekoliko meseci. Na moje zaprepašćenje, otkrio sam da mnoge funkcije članice DFWindow koriste te ugrađene pokazivače. Postojao je veoma lep interfejs za korišćenje funkcija koje nisu članice, ali funkcije članice ga nisu koristile. Moj rad bi bio mnogo lakši da sam na početku koristio razumne tehnike dizajna.

### Resursi i šta je C++ izostavio

D-Flat++ koristi resurse slične GUI-ima koje emulira. Resursi u ovom kontekstu su meniji i dijaloški okviri. Razgovarao sam o menijama u aprilu. Traka menija je definisana kao instanca MenuBar klase sa listom MenuBarItem objekata, od kojih svaki ima PopDown objekat povezan sa njim. Svaki PopDown objekat je definisan listom MenuSelection objekata. Ovo je slično nizovima struktura koje koristi D-Flat, osim što u ovom slučaju koristimo objekte klase, au ovom slučaju mi – nažalost – nemamo jezik resursa.

Windoes programeri su upoznati sa programom kompajlera resursa i jezikom resursa koji definiše menije i dijaloške okvire. Implementirao sam D-Flat jezik resursa sa makroima C preprocesora i obavio razuman posao emulacije sintakse Windows jezika. Bez obzira na to kako se trudim, međutim, još nisam smislio elegantan način da uradim istu stvar sa DF++. Čini se da su nešto izostavili kada su dizajnirali C++. Ne možete priključiti inicijalizatore na deklaraciju nizova objekata klase ako su klase složenije od jednostavne C strukture, što znači da ne možete koristiti takve inicijalizatore za deklarisanje podrazumevane dimenzije za niz. To ograničenje me sprečava da koristim pretprocesor da prevedem iskaze resursa u deklaracije niza na isti način kao što sam uradio sa C. Nešto dobijate, nešto gubite.

### Dijaloški okviri

Razgovarao sam o D-Flat++ kontrolnim klasama u februaru i martu. DF++ dijaloški okviri su klase koje izvodite iz Dialog klase i u njih ugrađujete kontrolne klase. Da bi ilustrovao tehniku i da bi imao neke često korišćene dijaloške okvire, DF++ uključuje prozore za poruke i greške, prozor za izbor da/ne i dijaloge za otvaranje i čuvanje datoteke. Postoji pet kontrolnih klasa izvedenih iz klase PushButton: dugme OK i Cancel, dugme Yes i No i dugme Help. Pogledaćemo ovaj dizajn odozgo nadole ispitivanjem dijaloga FileOpen i SaveAs. [LISTING_ONE](#listing_one) je "fileopen.h", izvorna datoteka koja definiše klase dijaloga FileOpen i SaveAs. FileOpen je izveden iz klase Dialog, a SaveAs je izveden iz FileOpen. Razgovaraćemo o prvom.

Klasa FileOpen sadrži nekoliko objekata klase, od kojih je svaki kontrola. Objekti Label su statički tekst koji dijaloški okvir prikazuje i koji korisnik ne može da promeni ili da ga tabulatorom. Program ih može promeniti, ali korisnik ne može. Dijalog FileOpen koristi nekoliko oznaka za identifikaciju drugih kontrola. D-Flat++ povezuje objekat Label sa kontrolom ako objekat Label neposredno prethodi kontroli. Ako oznaka ima definisanu prečicu, korisnik može direktno da pređe na kontrolu pritiskom na Alt i prečicu - baš kao i Windows. Obratite pažnju na to da definicije kontrole nemaju inicijalizatore koji bi im dali bilo kakve detalje. Ne možete izraziti inicijalizacijske vrednosti unutar definicije klase. Konstruktor dijaloškog okvira to radi.

### Diskovi, poddirektoriji i putanje

Tri kontrolne klase koje se koriste u klasi dijaloga FileOpen su izvedene iz kontrolne klase ListBox. To su kontrola FileNameListBox, kontrola DirectoriListBox i kontrola DriveListBox. Četvrta klasa, kontrola PathNameLabel, izvedena je iz klase TextBox. Ove kontrolne klase su definisane u [LISTING_TWO](#listing_two) "directori.h". Njihova svrha je da prikažu trenutnu putanju i liste datoteka, direktorijuma i disk jedinica. [LISTING_THREE](#listing_three) je "directory.cpp", koji sadrži funkcije članova za kontrolne klase diska i direktorijuma. Liste inicijalizuju njihovi konstruktori. Sve osim klase DriveListBox imaju funkcije članova koje dozvoljavaju dijaloškom okviru za promenu njihovih lista. Na primer, kada korisnik promeni disk jedinice ili poddirektorijume, okvir za dijalog treba da ažurira liste datoteka i poddirektorijuma.

Postoji neka mračna logika u konstruktoru DriveListBox da bi se utvrdilo da li je disk RAM disk ili mrežni disk ili da bi se videlo da li je disk jedinica B: ponovo mapirana na A: kao što se dešava kada računar ima samo jednu disketnu jedinicu. Logika uključuje pozive DOS IOCTL funkciji sa nekim nedokumentovanim protokolima.

[LISTING_FOUR](#listing_four) je "fileopen.cpp", koji sadrži funkcije člana za klase FileOpen i SaveAs. Deklarator za konstruktor klase je mesto gde program definiše svojstva okvira za dijalog i njegove kontrole obezbeđujući parametre za njihove konstruktore. U ovom slučaju, klasa Dialog je konstruisana sa naslovom "Otvori datoteku", visinom od 19 i širinom od 57. Objekti Label imaju tekstualne vrednosti za prikaz i x/y poziciju ekrana u odnosu na roditeljski prozor dijaloškog okvira. Znak tilde (~) u tekstu identifikuje tastersku prečicu oznake. Druge kontrole imaju pozicije i druge neophodne inicijalizatore.

Nekoliko funkcija člana FileOpen zamenjuje virtuelne funkcije u klasama DFVindov i Dialog. Funkcije ControlSelected i ControlChosen se pozivaju kada se izabere ili izabere kontrola. Na primer, kada korisnik pomeri traku selektora na polju sa listom, ova radnja se zove "odabir" stavke. Kada korisnik dvaput klikne ili pritisne Enter na stavku, to se zove "odabir" stavke. Kontrolni prozor prijavljuje ove radnje svom roditelju pozivanjem virtuelnih funkcija. Ovo obaveštenje omogućava roditelju, dijaloškom okviru u ovom slučaju, da preduzme radnju na osnovu radnji korisnika. U klasi FileOpen, na primer, korisnikov izbor imena datoteke sa liste dovodi do toga da okvir za dijalog kopira to ime u okvir za uređivanje imena datoteke.

Postoje virtuelne funkcije OKFunction, CancelFunction i HelpFunction koje se pozivaju kada korisnik odabere odgovarajuće dugme. Prva dva se takođe pozivaju kada korisnik pritisne Enter i Esc, a treća se poziva kada korisnik pritisne F1. Ove funkcije omogućavaju dijaloškom okviru da preduzme bilo koju odgovarajuću radnju. Ako ih izvedena klasa dijalog bok-a ne zameni, klasa Dialog preduzima podrazumevane radnje. Za prva dva, zatvara prozor dijaloga i vraća se na program koji ga je napravio. Funkcija pomoći će, nakon što je završim, prikazati pomoć o dijaloškim okvirima uopšte ako izvedeni okvir za dijalog ne nadjača funkciju.

Izvedena klasa dijaloga može zameniti još dve funkcije. To su funkcija EnterFocus, koja se poziva kada kontrola treba da dobije fokus, i LeaveFocus funkcija, koja se poziva kada kontrola izgubi fokus. Ove funkcije omogućavaju dijaloškom okviru da preduzme odgovarajuću akciju na unose korisnika kada se pojave.

### Dijalog klasa

[LISTING_FIVE](#listing_five) je "dialog.h", koji definiše osnovnu klasu Dialog. Definicija klase je prilično jednostavna. Uključuje nekoliko članova podataka i primitivne funkcije zajedničke svim dijaloškim okvirima. Svi konstruktori su zaštićeni, tako da ne možete instancirati objekat klase. Morate izvesti klasu kao što je klasa FileOpen o kojoj smo gore govorili i umesto nje instancirati je. [LISTING_SIX](#listing_six) je "dialog.cpp", koji sadrži funkcije člana. Funkcija TestShortCut povezuje prečice na nalepnicama sa objektima klase kontrole koje neposredno slede. Funkcija Execute pokreće dijaloški okvir nakon što je napravljen. Nisam mogao jednostavno da pozovem tu funkciju iz konstruktora jer bi to sprečilo bilo kakve izvedene klase dijaloga da je zamene ili bilo koje druge funkcije člana. Funkcija Execute hvata fokus za okvir za dijalog i postavlja fokus na prvu omogućenu kontrolu u okviru za dijalog. Zatim ostaje u petlji, pozivajući DispatchEvents sve dok je okvir za dijalog aktivan.

### Korišćenje okvira za dijalog

Program prikazuje dijalog FileOpen na ekranu i izvršava ga tako što deklariše instancu klase i poziva njenu funkciju člana Ekecute kao što je prikazano ovde:

```c
FileOpen fo;
fo.Execute();
```

Funkcija Execute daje korisnikov fokus na okvir za dijalog i ne vraća se u program koji poziva sve dok korisnik ne odabere dugme OK ili Cancel. Program može testirati rezultat pozivanjem funkcije člana OKEkit kao što je prikazano ovde:

```c
if (fo.OKExit())     {
    String fname = String(fo.FileName());
// ....
}
```

Funkcija člana FileName vraća izabrano ime datoteke. Iako je dijaloški okvir zatvoren i izbrisan sa ekrana, objekat klase postoji sve dok ne izađe van opsega, tako da program preuzima vrednosti podataka iz kontrola kako ih korisnik menja.

Ove procedure su iste za sve modalne dijaloške okvire. Još ih nisam razradio za dijaloške okvire bez modela, ali ta funkcija bi trebalo da bude na mestu do trenutka kada ovo pročitate.

#### LISTING_ONE

```c
// --------- fileopen.h
#ifndef FILEOPEN_H
#define FILEOPEN_H

#include "dflatpp.h"
#include "directry.h"

// ------------ File Open dialog box
class FileOpen : public Dialog    {
    // -----File Open Dialog Box Controls:
protected:
    // ----- file name editbox
    Label filelabel;
    EditBox filename;
    // ----- drive:path display
    PathNameLabel dirlabel;
    // ----- files list box
    Label fileslabel;
    FileNameListBox files;
    // ----- directories list box
    Label dirslabel;
    DirectoryListBox dirs;
    // ----- drives list box
    Label disklabel;
    DriveListBox disks;
    // ----- command buttons
    OKButton ok;
    CancelButton cancel;
    HelpButton help;
    // ------ file open data members
    String filespec;
    // ------ private member functions
    void SelectFileName();
    void ShowLists();
    // --- functions inherited from DFWindow
    virtual void ControlSelected(DFWindow *Wnd);
    virtual void ControlChosen(DFWindow *Wnd);
    virtual void EnterFocus(DFWindow *Wnd);
    virtual void OKFunction();
public:
    FileOpen(char *spec = "*.*", char *ttl = "File Open");
    const String& FileName() { return filespec; }
};
class SaveAs : public FileOpen    {
    virtual void OKFunction();
public:
    SaveAs(char *spec = "", char *ttl = "Save As")
        : FileOpen(spec, ttl) { }
};
#endif
```

#### LISTING_TWO

```c
// ---------- directry.h
#ifndef DRIVE_H
#define DRIVE_H

#include <dir.h>
#include <dos.h>
#include "listbox.h"
#include "label.h"

class PathNameLabel : public Label {
    char *CurrentPath();
public:
    PathNameLabel(int x, int y, int wd) :
        Label(CurrentPath(), x, y, wd) {}
    void FillLabel();
};
class DriveListBox : public ListBox    {
    unsigned currdrive;
public:
    DriveListBox(int lf, int tp);
};
class DirectoryListBox : public ListBox    {
public:
    DirectoryListBox(int lf, int tp);
    void FillList();
};
class FileNameListBox : public ListBox    {
public:
    FileNameListBox(char *filespec, int lf, int tp);
    void FillList(char *filespec);
};
#endif
```

#### LISTING_THREE

```c
// ---------- directry.cpp

#include <direct.h>
#include "directry.h"

char *PathNameLabel::CurrentPath()
{
    static char path[129];
    _getdcwd(0, path, 129);
    return path;
}
void PathNameLabel::FillLabel()
{
    SetText(CurrentPath());
}
DriveListBox::DriveListBox(int lf, int tp) : ListBox(lf, tp, 10, 10, 0)
{
    SetAttribute(BORDER);
    currdrive = getdisk();
    union REGS regs;
    for (unsigned int dr = 0; dr < 26; dr++)    {
        setdisk(dr);
        if (getdisk() == dr)    {
            // ----- test for remapped B drive
            if (dr == 1)    {
                regs.x.ax = 0x440e; // IOCTL func 14
                regs.h.bl = dr+1;
                int86(0x21, &regs, &regs);
                if (regs.h.al != 0)
                    continue;
            }
            String drname(" :");
            drname[0] = dr+'A';
            // ---- test for network or RAM disk
            regs.x.ax = 0x4409;     // IOCTL func 9
            regs.h.bl = dr+1;
            int86(0x21, &regs, &regs);
            if (!regs.x.cflag)    {
                if (regs.x.dx & 0x1000)
                    drname += " (Net)";
                else if (regs.x.dx == 0x0800)
                    drname += " (RAM)";
            }
            AddText(drname);
        }
    }
    setdisk(currdrive);
    SetScrollBars();
}
DirectoryListBox::DirectoryListBox(int lf, int tp) : ListBox(lf, tp, 10, 13, 0)
{
    SetAttribute(BORDER);
    FillList();
}
void DirectoryListBox::FillList()
{
    ClearText();
    int ax;
    struct ffblk ff;
    ax = findfirst("*.*", &ff, FA_DIREC);
    while (ax == 0)    {
        if ((ff.ff_attrib & FA_DIREC) != 0)    {
            if (strcmp(ff.ff_name, "."))    {
                String fname("[");
                fname += ff.ff_name;
                fname += "]";
                AddText(fname);
            }
        }
        ax = findnext(&ff);
    }
    SetScrollBars();
}
FileNameListBox::FileNameListBox(char *filespec,int lf,int tp)
                        : ListBox(lf, tp, 10, 14, 0)
{
    SetAttribute(BORDER);
    FillList(filespec);
}
void FileNameListBox::FillList(char *filespec)
{
    ClearText();
    int ax;
    struct ffblk ff;
    ax = findfirst(*filespec ? filespec : "*.*", &ff, 0);
    while (ax == 0)    {
        AddText(ff.ff_name);
        ax = findnext(&ff);
    }
    SetScrollBars();
}
```

#### LISTING_FOUR

```c
// ---------- fileopen.cpp

#include <io.h>
#include <dir.h>
#include "fileopen.h"
#include "notice.h"

// ----------------- File Open Dialog Box
FileOpen::FileOpen(char *spec, char *ttl) :
        Dialog(ttl, 19, 57),
        // ----- file name editbox
        filelabel ("~Filename:", 3, 2),
        filename  (13, 2, 1, 40),
        // ----- drive:path display
        dirlabel  (3, 4, 50),
        // ----- files list box
        fileslabel("F~iles:", 3, 6),
        files     (spec, 3, 7),
        // ----- directories list box
        dirslabel ("~Directories:", 19, 6),
        dirs      (19, 7),
        // ----- drives list box
        disklabel ("Dri~ves:", 34, 6),
        disks     (34, 7),
        // ----- command buttons
        ok        (46, 8),
        cancel    (46,11),
        help      (46,14),
        // ------ file open data members
        filespec(spec)
{
    filename.AddText(filespec);
}
// --- Get selected filename: files listbox->filename editbox
void FileOpen::SelectFileName()
{
    int sel = files.Selection();
    if (sel != -1)    {
        String fname = files.ExtractTextLine(sel);
        filename.SetText(fname);
        filename.Paint();
    }
}
// ---- called when user "selects" a control
//      e.g. changes the selection on a listbox
void FileOpen::ControlSelected(DFWindow *Wnd)
{
    if (Wnd == (DFWindow *) &files)
        // --- user selected a filename from list
        SelectFileName();
    else if (Wnd == (DFWindow *) &dirs ||
            Wnd == (DFWindow *) &disks)    {
        // --- user is selecting a different drive or directory
        filename.SetText(filespec);
        filename.Paint();
    }
}
// ---- called when user "chooses" a control
//      e.g. chooses the current selection on a listbox
void FileOpen::ControlChosen(DFWindow *Wnd)
{
    if (Wnd == (DFWindow *) &files)
        // --- user chose a filename from filename list
        OKFunction();
    else if (Wnd == (DFWindow *) &dirs)    {
        // --- user chose a directory from directory list
        int dr = dirs.Selection();
        String dir = dirs.ExtractTextLine(dr);
        int len = dir.Strlen();
        String direc = dir.mid(len-2,1);
        chdir(direc);
        ShowLists();
    }
    else if (Wnd == (DFWindow *) &disks)    {
        // --- user chose a drive from drive list
        int dr = disks.Selection();
        String drive = disks.ExtractTextLine(dr);
        setdisk(drive[0] - 'A');
        ShowLists();
    }
}
// ---- called when user chooses OK command
void FileOpen::OKFunction()
{
    String fname = filename.ExtractTextLine(0);
    if (access(fname, 0) == 0)    {
        filespec = fname;
        Dialog::OKFunction();
    }
    else if (fname.FindChar('*') != -1 ||
            fname.FindChar('?') != -1)    {
        filespec = fname;
        ShowLists();
    }
    else
        // ---- No file as specified
        ErrorMessage("File does not exist");
}
// ------ refresh the current directory display and the directories and files
//        list after user changes filespec, drive, or directory
void FileOpen::ShowLists()
{
    dirlabel.FillLabel();
    dirlabel.Show();
    dirs.FillList();
    dirs.Show();
    files.FillList(filespec);
    files.Show();
}
// ------- called just before a control gets the focus
void FileOpen::EnterFocus(DFWindow *Wnd)
{
    if (Wnd == (DFWindow *) &files)
        // --- The file name list box is getting the focus
        SelectFileName();
}
// ---- called when user chooses OK command
void SaveAs::OKFunction()
{
    String fname = filename.ExtractTextLine(0);
    if (access(fname, 0) != 0)    {
        // ---- chosen file does not exist
        if (fname.FindChar('*') != -1 ||
            fname.FindChar('?') != -1)    {
            // --- wild cards
            filespec = fname;
            ShowLists();
        }
        else    {
            filespec = fname;
            Dialog::OKFunction();
        }
    }
    else    {
        // ---- file exists
        String msg = fname + " already exists. Replace?";
        if (YesNo(msg))    {
            filespec = fname;
            Dialog::OKFunction();
        }
    }
}
```

#### LISTING_FIVE

```c
// -------- dialog.h

#ifndef DIALOG_H
#define DIALOG_H

#include "dfwindow.h"
#include "desktop.h"
#include "control.h"

class Control;
class Dialog : public DFWindow    {
    virtual void SetColors();
    Bool isRunning;
    Bool okexit;
    void OpenWindow();
    friend Control;
    void TestShortcut(int key);
protected:
    Dialog(char *ttl, int lf, int tp, int ht, int wd,
            DFWindow *par = (DFWindow *)desktop.ApplWnd())
                : DFWindow(ttl, lf, tp, ht, wd, par)
            { OpenWindow(); }
    Dialog(char *ttl, int ht, int wd,
            DFWindow *par = (DFWindow *)desktop.ApplWnd())
                : DFWindow(ttl, ht, wd, par)
            { OpenWindow(); }
    virtual ~Dialog() {}
    virtual void CloseWindow();
    virtual void Keyboard(int key);
    virtual void OKFunction();
    virtual void CancelFunction();
    virtual void HelpFunction();
public:
    virtual void Execute();
    Bool OKExit() { return okexit; }
};
#endif
```

#### LISTING_SIX

```c
// ------------- dialog.cpp

#include <ctype.h>
#include "dialog.h"
#include "desktop.h"

Dialog *ThisDialog;

// ----------- common constructor code
void Dialog::OpenWindow()
{
    ThisDialog = this;
    windowtype = DialogWindow;
    DblBorder = False;
    SetAttribute(BORDER | SAVESELF | CONTROLBOX |
        MOVEABLE | SHADOW);
    isRunning = False;
    okexit = False;
}
void Dialog::CloseWindow()
{
    ReleaseFocus();
    DFWindow::CloseWindow();
    isRunning = False;
}
// -------- set the fg/bg colors for the window
void Dialog::SetColors()
{
    colors.fg =
    colors.sfg =
    colors.ffg =
    colors.hfg = LIGHTGRAY;
    colors.bg =
    colors.sbg =
    colors.fbg =
    colors.hbg = BLUE;
}
void Dialog::Keyboard(int key)
{
    switch (key)    {
        case ESC:
            CancelFunction();
            break;
        case '\r':
            OKFunction();
            break;
        case ALT_F4:
            CloseWindow();
            break;
        case ' ':
            if ((desktop.keyboard().GetShift() & ALTKEY) == 0)
                break;
            // ---- fall through
        case ALT_F6:
            DFWindow::Keyboard(key);
            break;
        default:
            if ((desktop.keyboard().GetShift() & ALTKEY) != 0)
                TestShortcut(key);
            break;
    }
}
void Dialog::TestShortcut(int key)
{
    key = desktop.keyboard().AltConvert(key);
    key = tolower(key);
    Control *Ctl = (Control *)First();
    while (Ctl != 0)    {
        if (key == Ctl->Shortcut())    {
            Ctl->ShortcutSelect();
            break;
        }
        Ctl = (Control *) Ctl->Next();
    }
}
void Dialog::Execute()
{
    ThisDialog = 0;
    CaptureFocus();
    Control *Ctl = (Control *) First();
    while (Ctl != 0)    {
        if (Ctl->isEnabled())    {
            Ctl->SetFocus();
            break;
        }
        Ctl = (Control *) Ctl->Next();
    }
    // ---- modal dialog box
    isRunning = True;
    while (isRunning)
        desktop.DispatchEvents();
}
void Dialog::OKFunction()
{
    okexit = True;
    CloseWindow();
}
void Dialog::CancelFunction()
{
    CloseWindow();
}
void Dialog::HelpFunction()
{
}
```

Copyright © 1993, Dr. Dobb's Journal
