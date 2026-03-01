
# C PROGRAMMING 9210

## D-Flat, Home Stretch - Al Stevens

### D-Flat omotač

Ovaj mesec završava seriju o D-Flat-u, DOS biblioteci korisničkog interfejsa CUA u tekstualnom režimu. Sledećeg meseca ću početi sa D-Flat++, prepisivanjem u C++. DF++ neće izdržati godinu i po dana kao D-Flat. Koristim DF++ da ispitam probleme koji okružuju C++ prepisivanje i da uporedim dva jezika kada se primenjuju na isto rešenje. Stoga će DF++ pokrivenost ove kolone uključivati izvorni kod koji podržava te diskusije i neće pokušati da objavi celu enchiladu. Međutim, biće dostupan za preuzimanje sa CompuServe-a i M&T Online-a i pod DDJ "carevare" programom, objašnjeno je dalje.

Kada sam pokrenuo D-Flat, postojalo je samo nekoliko opcija DOS tekstualnog režima za implementaciju CUA interfejsa. Sada ih ima više, uključujući neka nova iznenađenja u mesecima koji dolaze. Dok Vindovs proždire tržište računarskog operativnog okruženja, pitam se o budućnosti DOS aplikacija u tekstualnom režimu. Praktično svaka velika računarska aplikacija sada ima verziju operativnog sistema Vindovs, koja krade svu pažnju svog DOS pretka. OS/2 reži sa strane, preti da zgrabi svoj deo. Postoje i drugi GUI: X-Window, Motif, NeXT, GeoWorks, OpenLook, GEM, AppleDOS, AmigaDOS. Ako biste mogli da identifikujete najmanji zajednički imenitelj koji dele svi GUI, mogli biste da napišete biblioteku C++ klasa za svaki koja bi omogućila aplikaciji da se kompajlira za bilo koju GUI platformu bez promena u izvornom kodu. Aplikacioni programi ne bi bili veoma interesantni korisniku jer ne bi koristili najsjajnije, jedinstvene kvalitete bilo kog određenog operativnog okruženja, ali bi bili manje-više prenosivi.

Da bih završio D-Flat projekat, razgovaraću o dijaloškim okvirima File Open i Save As, statusnoj liniji prozora aplikacije i kompresiji teksta D-Flat baze podataka pomoći.

### Dijaloški okviri za otvaranje datoteke

Kada rade sa aplikacijom, korisnici određuju postojeće nazive datoteka za otvaranje i nazive novih datoteka za kreiranje. D-Flat uključuje dva dijaloška okvira za ove svrhe: File Open i Save As. Ovi dijaloški okviri omogućavaju korisniku da izabere datoteku sa bilo kog diska ili poddirektorijuma u ​​sistemu. Format uključuje okvir za uređivanje u jednom redu gde korisnik upisuje specifikaciju datoteke, tekstualni prikaz koji prikazuje trenutnu disk jedinicu i poddirektorijum i okvire sa listom za izbor postojećih datoteka i promenu disk jedinice i poddirektorijuma.

[LISTING_ONE](#listing_one), je "direct.c", kod koji podržava dijalog za otvaranje i čuvanje za obradu disk jedinice i direktorijuma. Funkcija CreatePath konvertuje dvosmislenu specifikaciju datoteke u onu gde su delovi putanje nedvosmisleni, a ime datoteke i ekstenzija su predstavljeni barem džoker karticama. Ako specifikacija nema disk jedinicu ili poddirektorijum, funkcija dodaje trenutno prijavljenu disk jedinicu i poddirektorijum na putanju. Ako disk postoji, ali nema putanje, funkcija dodaje putanju za taj disk. Ako specifikacija nema naziv datoteke ili ekstenziju, funkcija dodaje džoker karticu za njih.

Funkcija DlgDirList pravi okvir liste sa unosima za sve disk jedinice i poddirektorijume ispod trenutnog na trenutnoj disk jedinici. Takođe gradi ekran za kontrolu teksta koji prikazuje trenutnu disk jedinicu i direktorijum. Dva dijaloga koriste ovu funkciju da prikažu gde se korisnik nalazi u sistemu datoteka i daju izbore za promenu disk jedinice i direktorijuma.

[LISTING_TWO](#listing_two), je "fileopen.c", program koji implementira dijaloge Open i Save As. Aplikacija poziva funkciju OpenFileDialogBok ili SaveAsDialogBok, u zavisnosti od toga koji dijaloški okvir želi. Prethodna funkcija prihvata džoker parametar koji određuje početnu putanju i ime datoteke za pretragu. ako ste prosledili vrednost "C:\DOS\*.EKSE", okvir za dijalog bi počeo sa poddirektorijumom \DOS na C: disku i prikazao bi sve *.EKSE datoteke u polju sa spiskom naziva datoteke. Obe funkcije prihvataju parametar pokazivača karaktera sa adresom za pisanje specifikacije datoteke koju je izabrao korisnik.

Program počinje tako što prikazuje specifikaciju naziva datoteke pozivaoca u polju za uređivanje u jednom redu, trenutnu disk jedinicu i direktorijum u tekstualnoj kontroli, listu imena datoteka koja se poklapaju sa specifikacijom datoteke u okviru sa listom i dostupnih disk jedinica i poddirektorijuma u ​​drugom okviru sa listom. Korisnici mogu da unesu novu specifikaciju datoteke ili da je izaberu sa liste. Korisnici takođe mogu da izaberu drugu disk jedinicu ili poddirektorijum, a drugi prikazi u okviru za dijalog se menjaju tako da odražavaju novu putanju. Kada korisnici izaberu komandu OK, trenutna putanja i ime datoteke se kopiraju u string pozivaoca i funkcija se vraća.

### Statusna traka

Prozor aplikacije D-Flat može imati statusnu traku na donjoj ivici prozora. [LISTING_THREE](#listing_three), je "statbar.c", kod koji implementira modul za obradu prozora za klasu prozora STATUS-BAR. Klasa snima sat za prozor i prikazuje vreme za svaki događaj CLOCKTICK. Kada aplikacija želi da koristi statusnu traku da prikaže tekstualnu poruku u jednom redu, ona šalje ADDSTATUS poruku prozoru aplikacije, koja koristi poruku SETTEKST da stavi tekst u prozor. Poruka PAINT na statusnoj traci prikazuje bilo koju tekstualnu vrednost koju prozor sadrži.

### Kompresija pomoći

Pre otprilike dve godine objavio sam u ovoj kolumni kod koji implementira Hafmanove algoritme kompresije i dekompresije. Odlučio sam da prilagodim te algoritme da komprimujem D-Flat bazu podataka teksta pomoći iz dva razloga. Prvo, komprimovana datoteka pomoći čini manju distribuciju vaše aplikacije. Drugo, komprimovani tekstualni fajl je zaštićen od potencijalnih promena koje unese radoznali korisnik.

Liste četiri, pet i šest, strana 162, su htree.h, htree.c i huffc.c, kod koji komprimira tekst pomoći. htree.h definiše strukturu Hafmanovog stabla kako je izgrađeno i njegovu reprezentaciju u kompresovanoj datoteci. Hafmanova kompresija se sastoji od redukcije znakova u tekstu na nizove bitova. Češći znakovi su predstavljeni kraćim nizovima bitova.

Da biste napravili Hafmanovo stablo, prvo pročitajte tekstualnu datoteku i prebrojite znakove. Na dnu stabla je širina od 256 čvorova, od kojih svaki predstavlja osmobitnu vrednost. U početku, ovi čvorovi su sve što drvo ima, i svaki od njih beleži koliko puta se karakter koji predstavlja pojavljuje u tekstu. Prvi čvor predstavlja vrednost 0, drugi čvor predstavlja 1 i tako dalje. Nijedan od ovih čvorova nema dece ili roditelja. Zatim prođite kroz čvorove i pronađite dva koja predstavljaju znakove koji imaju najmanji broj pojavljivanja u tekstu. Za ta dva čvora, napravite novi čvor koji je njihov roditelj i koji kao svoju frekvenciju ima ukupan broj ta dva čvora. Taj čvor će ukazivati na svoja dva podređena čvora. Ponovite ovo skeniranje, uvek zaobilazeći čvorove koji imaju odrasle roditelje i uključite sve nove roditeljske čvorove koji su dodati sve dok ne ostane samo jedan čvor koji nema roditelja. Taj čvor će biti korenski čvor drveta.

Nakon što je stablo napravljeno, program ga može zapisati u komprimovanu datoteku. Nije potrebno svo drvo, ali algoritmu za dekompresiju je potrebno dovoljno da dekompresuje tekst. Potreban vam je broj bajtova u tekstu, broj roditeljskih čvorova u stablu i pomak u odnosu na osnovni čvor. Tada su vam potrebni samo pokazivači dečjeg čvora za svaki od čvorova koji su roditelji - čvorovi iznad originalnih 256.

Da bi komprimovao tekst, program ponovo čita datoteku bajt po bajt. Za svaki bajt, program prati putanju od korenskog čvora niz desno ili levo potomstvo dok ne dođe do čvora koji predstavlja karakter. Za svaki pravi put, program upisuje 0 bit. Za svaku levu putanju program upisuje 1 bit. Trik uključuje program da pronađe svoj put na pravom putu. Da bi to uradila, funkcija počinje u osnovnom čvoru koji predstavlja karakter i rekurzivno poziva samu sebe dok ne dođe do korena.

[LISTING_SEVEN](#listing_seven), je "decomp.c", koji sadrži dekompresijski kod za bazu podataka pomoći. Dekompresija se dešava dva puta. Kada program učita bazu podataka pomoći, on čita tekst i pravi tabelu tekstova pomoći, navodeći identifikator teksta i njegov pomak bajta/bita u komprimovanu datoteku. Kasnije, kada korisnik zatraži pomoć, program traži komprimovani tekst i dekompresuje ga za prikaz.

Algoritam dekompresije je inverzan algoritmu kompresije. Dok čita bitove iz kompresovanog toka, program se kreće od osnovnog čvora do baze, uzimajući levu podređenu putanju za 1 bit i desnu podređenu putanju za 0 bit. Kada putanja isporučuje broj čvora manji od 256, taj broj je dekomprimovani tekstualni karakter.

#### LISTING_ONE

```c
/* ---------- direct.c --------- */
#include "dflat.h"

static char path[MAXPATH];
static char drive[MAXDRIVE] = " :";
static char dir[MAXDIR];
static char name[MAXFILE];
static char ext[MAXEXT];

/* --- Create unambiguous path from file spec, filling in the drive and
directory if incomplete. Optionally change to new drive and subdirectory --- */
void CreatePath(char *path,char *fspec,int InclName,int Change)
{
    int cm = 0;
    unsigned currdrive;
    char currdir[64];
    char *cp;

    if (!Change)    {
        /* ---- save the current drive and subdirectory ---- */
        currdrive = getdisk();
        getcwd(currdir, sizeof currdir);
        memmove(currdir, currdir+2, strlen(currdir+1));
        cp = currdir+strlen(currdir)-1;
        if (*cp == '\\')
            *cp = '\0';
    }
    *drive = *dir = *name = *ext = '\0';
    fnsplit(fspec, drive, dir, name, ext);
    if (!InclName)
        *name = *ext = '\0';
    *drive = toupper(*drive);
    if (*ext)
        cm |= EXTENSION;
    if (InclName && *name)
        cm |= FILENAME;
    if (*dir)
        cm |= DIRECTORY;
    if (*drive)
        cm |= DRIVE;
    if (cm & DRIVE)
        setdisk(*drive - 'A');
    else     {
        *drive = getdisk();
        *drive += 'A';
    }
    if (cm & DIRECTORY)    {
        cp = dir+strlen(dir)-1;
        if (*cp == '\\')
            *cp = '\0';
        chdir(dir);
    }
    getcwd(dir, sizeof dir);
    memmove(dir, dir+2, strlen(dir+1));
    if (InclName)    {
        if (!(cm & FILENAME))
            strcpy(name, "*");
        if (!(cm & EXTENSION) && strchr(fspec, '.') != NULL)
            strcpy(ext, ".*");
    }
    else
        *name = *ext = '\0';
    if (dir[strlen(dir)-1] != '\\')
        strcat(dir, "\\");
    memset(path, 0, sizeof path);
    fnmerge(path, drive, dir, name, ext);
    if (!Change)    {
        setdisk(currdrive);
        chdir(currdir);
    }
}
static int dircmp(const void *c1, const void *c2)
{
    return stricmp(*(char **)c1, *(char **)c2);
}
BOOL DlgDirList(WINDOW wnd, char *fspec,
                enum commands nameid, enum commands pathid, unsigned attrib)
{
    int ax, i = 0, criterr = 1;
    struct ffblk ff;
    CTLWINDOW *ct = FindCommand(wnd->extension,nameid,LISTBOX);
    WINDOW lwnd;
    char **dirlist = NULL;

    CreatePath(path, fspec, TRUE, TRUE);
    if (ct != NULL)    {
        lwnd = ct->wnd;
        SendMessage(ct->wnd, CLEARTEXT, 0, 0);

        if (attrib & 0x8000)    {
            union REGS regs;
            char drname[15];
            unsigned int cd, dr;

            cd = getdisk();
            for (dr = 0; dr < 26; dr++)    {
                unsigned ndr;
                setdisk(dr);
                ndr = getdisk();
                if (ndr == dr)    {
                    /* ----- test for remapped B drive ----- */
                    if (dr == 1)    {
                        regs.x.ax = 0x440e; /* IOCTL func 14 */
                        regs.h.bl = dr+1;
                        int86(DOS, &regs, &regs);
                        if (regs.h.al != 0)
                            continue;
                    }
                    sprintf(drname, "[%c:]", dr+'A');

                    /* ---- test for network or RAM disk ---- */
                    regs.x.ax = 0x4409;     /* IOCTL func 9 */
                    regs.h.bl = dr+1;
                    int86(DOS, &regs, &regs);
                    if (!regs.x.cflag)    {
                        if (regs.x.dx & 0x1000)
                            strcat(drname, " (Network)");
                        else if (regs.x.dx == 0x0800)
                            strcat(drname, " (RAMdisk)");
                    }
                    SendMessage(lwnd,ADDTEXT,(PARAM)drname,0);
                }
            }
            setdisk(cd);
        }
        while (criterr == 1)    {
            ax = findfirst(path, &ff, attrib & 0x3f);
            criterr = TestCriticalError();
        }
        if (criterr)
            return FALSE;
        while (ax == 0)    {
            if (!((attrib & 0x4000) &&
                    (ff.ff_attrib & (attrib & 0x3f)) == 0) &&
                        strcmp(ff.ff_name, "."))    {
                char fname[15];
                sprintf(fname, (ff.ff_attrib & 0x10) ?
                                "[%s]" : "%s" , ff.ff_name);
                dirlist = DFrealloc(dirlist,
                                    sizeof(char *)*(i+1));
                dirlist[i] = DFmalloc(strlen(fname)+1);
                if (dirlist[i] != NULL)
                    strcpy(dirlist[i], fname);
                i++;
            }
            ax = findnext(&ff);
        }
        if (dirlist != NULL)    {
            int j;
            /* -- sort file/drive/directory list box data -- */
            qsort(dirlist, i, sizeof(void *), dircmp);
            /* ---- send sorted list to list box ---- */
            for (j = 0; j < i; j++)    {
                SendMessage(lwnd,ADDTEXT,(PARAM)dirlist[j],0);
                free(dirlist[j]);
            }
            free(dirlist);
        }
        SendMessage(lwnd, SHOW_WINDOW, 0, 0);
    }
    if (pathid)    {
        fnmerge(path, drive, dir, NULL, NULL);
        PutItemText(wnd, pathid, path);
    }
    return TRUE;
}
```

#### LISTING_TWO

```c
/* ----------- fileopen.c ------------- */
#include "dflat.h"

static BOOL DlgFileOpen(char *, char *, DBOX *);
static int DlgFnOpen(WINDOW, MESSAGE, PARAM, PARAM);
static void InitDlgBox(WINDOW);
static void StripPath(char *);
static BOOL IncompleteFilename(char *);

static char *OrigSpec;
static char *FileSpec;
static char *FileName;
static char *NewFileName;

static BOOL Saving;
extern DBOX FileOpen;
extern DBOX SaveAs;

/* ----  Dialog Box to select a file to open ---- */
BOOL OpenFileDialogBox(char *Fpath, char *Fname)
{
    return DlgFileOpen(Fpath, Fname, &FileOpen);
}
/* ----  Dialog Box to select a file to save as ---- */
BOOL SaveAsDialogBox(char *Fname)
{
    return DlgFileOpen(NULL, Fname, &SaveAs);
}
/* --------- generic file open ---------- */
static BOOL DlgFileOpen(char *Fpath, char *Fname, DBOX *db)
{
    BOOL rtn;
    char savedir[80];
    char OSpec[80];
    char FSpec[80];
    char FName[80];
    char NewFName[80];

    OrigSpec = OSpec;
    FileSpec = FSpec;
    FileName = FName;
    NewFileName = NewFName;

    getcwd(savedir, sizeof savedir);
    if (Fpath != NULL)    {
        strncpy(FileSpec, Fpath, 80);
        Saving = FALSE;
    }
    else    {
        *FileSpec = '\0';
        Saving = TRUE;
    }
    strcpy(FileName, FileSpec);
    strcpy(OrigSpec, FileSpec);

    if ((rtn = DialogBox(NULL, db, TRUE, DlgFnOpen)) != FALSE)
        strcpy(Fname, NewFileName);
    else
        *Fname = '\0';

    setdisk(toupper(*savedir) - 'A');
    chdir(savedir);

    return rtn;
}
static int CommandMsg(WINDOW wnd, PARAM p1, PARAM p2)
{
    switch ((int) p1)    {
        case ID_FILENAME:
            if (p2 != ENTERFOCUS)    {
                /* allow user to modify the file spec */
                GetItemText(wnd, ID_FILENAME, FileName, 65);
                if (IncompleteFilename(FileName) || Saving)    {
                    strcpy(OrigSpec, FileName);
                    StripPath(OrigSpec);
                }
                if (p2 != LEAVEFOCUS)
                    SendMessage(wnd, COMMAND, ID_OK, 0);
            }
            return TRUE;
        case ID_OK:
            if (p2 != 0)
                break;
            GetItemText(wnd, ID_FILENAME,
                    FileName, 65);
            strcpy(FileSpec, FileName);
            if (IncompleteFilename(FileName))    {
                /* no file name yet */
                InitDlgBox(wnd);
                strcpy(OrigSpec, FileSpec);
                return TRUE;
            }
            else    {
                GetItemText(wnd, ID_PATH, FileName, 65);
                strcat(FileName, FileSpec);
                strcpy(NewFileName, FileName);
            }
            break;
        case ID_FILES:
            switch ((int) p2)    {
                case ENTERFOCUS:
                case LB_SELECTION:
                    /* selected a different filename */
                    GetDlgListText(wnd, FileName, ID_FILES);
                    PutItemText(wnd, ID_FILENAME, FileName);
                    break;
                case LB_CHOOSE:
                    /* chose a file name */
                    GetDlgListText(wnd, FileName, ID_FILES);
                    SendMessage(wnd, COMMAND, ID_OK, 0);
                    break;
                default:
                    break;
            }
            return TRUE;
        case ID_DRIVE:
            switch ((int) p2)    {
                case ENTERFOCUS:
                    if (Saving)
                        *FileSpec = '\0';
                    break;
                case LEAVEFOCUS:
                    if (Saving)
                        strcpy(FileSpec, FileName);
                    break;
                case LB_SELECTION:    {
                    char dd[25];
                    /* selected different drive/dir */
                    GetDlgListText(wnd, dd, ID_DRIVE);
                    if (*(dd+2) == ':')
                        *(dd+3) = '\0';
                    else
                        *(dd+strlen(dd)-1) = '\0';
                    strcpy(FileName, dd+1);
                    if (*(dd+2) != ':' && *OrigSpec != '\\')
                        strcat(FileName, "\\");
                    strcat(FileName, OrigSpec);
                    if (*(FileName+1)!=':'&&*FileName!='.')    {
                        GetItemText(wnd,ID_PATH,FileSpec,65);
                        strcat(FileSpec, FileName);
                    }
                    else
                        strcpy(FileSpec, FileName);
                    break;
                }
                case LB_CHOOSE:
                    /* chose drive/dir */
                    if (Saving)
                        PutItemText(wnd, ID_FILENAME, "");
                    InitDlgBox(wnd);
                    return TRUE;
                default:
                    break;
            }
            PutItemText(wnd, ID_FILENAME, FileSpec);
            return TRUE;
        default:
            break;
    }
    return FALSE;
}
/* ----   Process File Open dialog box messages ---- */
static int DlgFnOpen(WINDOW wnd,MESSAGE msg,PARAM p1,PARAM p2)
{
    switch (msg)    {
        case CREATE_WINDOW:    {
            int rtn = DefaultWndProc(wnd, msg, p1, p2);
            DBOX *db = wnd->extension;
            WINDOW cwnd = ControlWindow(db, ID_FILENAME);
            SendMessage(cwnd, SETTEXTLENGTH, 64, 0);
            return rtn;
        }
        case INITIATE_DIALOG:
            InitDlgBox(wnd);
            break;
        case COMMAND:
            if (CommandMsg(wnd, p1, p2))
                return TRUE;
            break;
        default:
            break;
    }
    return DefaultWndProc(wnd, msg, p1, p2);
}
/* ----   Initialize the dialog box ---- */
static void InitDlgBox(WINDOW wnd)
{
    if (*FileSpec && !Saving)
        PutItemText(wnd, ID_FILENAME, FileSpec);
    if (DlgDirList(wnd, FileSpec, ID_FILES, ID_PATH, 0))    {
        StripPath(FileSpec);
        DlgDirList(wnd, "*.*", ID_DRIVE, 0, 0xc010);
    }
}
/* ----  Strip drive and path information from file spec ---- */
static void StripPath(char *filespec)
{
    char *cp, *cp1;
    cp = strchr(filespec, ':');
    if (cp != NULL)
        cp++;
    else
        cp = filespec;
    while (TRUE)    {
        cp1 = strchr(cp, '\\');
        if (cp1 == NULL)
            break;
        cp = cp1+1;
    }
    strcpy(filespec, cp);
}
/* ---- test for an incomplete file name ---- */
static BOOL IncompleteFilename(char *s)
{
    int lc = strlen(s)-1;
    if (strchr(s, '?') || strchr(s, '*') || !*s)
        return TRUE;
    if (*(s+lc) == ':' || *(s+lc) == '\\')
        return TRUE;
    return FALSE;
}
```

#### LISTING_THREE

```c
/* ---------------- statbar.c -------------- */
#include "dflat.h"

int StatusBarProc(WINDOW wnd, MESSAGE msg, PARAM p1, PARAM p2)
{
    char *statusbar;
    switch (msg)    {
        case CREATE_WINDOW:
        case MOVE:
            SendMessage(wnd, CAPTURE_CLOCK, 0, 0);
            break;
        case KEYBOARD:
            if ((int)p1 == CTRL_F4)
                return TRUE;
            break;
        case PAINT:
            if (!isVisible(wnd))
                break;
            statusbar = DFcalloc(1, WindowWidth(wnd)+1);
            memset(statusbar, ' ', WindowWidth(wnd));
            *(statusbar+WindowWidth(wnd)) = '\0';
            strncpy(statusbar+1, "F1=Help", 7);
            if (wnd->text)    {
                int len = min(strlen(wnd->text),
                    WindowWidth(wnd)-17);
                if (len > 0)    {
                    int off=(WindowWidth(wnd)-len)/2;
                    strncpy(statusbar+off, wnd->text, len);
                }
            }
            if (wnd->TimePosted)
                *(statusbar+WindowWidth(wnd)-8) = '\0';
            SetStandardColor(wnd);
            PutWindowLine(wnd, statusbar, 0, 0);
            free(statusbar);
            return TRUE;
        case BORDER:
            return TRUE;
        case CLOCKTICK:
            SetStandardColor(wnd);
            PutWindowLine(wnd,(char *)p1,WindowWidth(wnd)-8,0);
            wnd->TimePosted = TRUE;
            return TRUE;
        case CLOSE_WINDOW:
            SendMessage(NULL, RELEASE_CLOCK, 0, 0);
            break;
        default:
            break;
    }
    return BaseWndProc(STATUSBAR, wnd, msg, p1, p2);
}
```

#### LISTING_FOUR

```c
/* ------------------- htree.h -------------------- */
#ifndef HTREE_H
#define HTREE_H

typedef unsigned int BYTECOUNTER;

/* ---- Huffman tree structure for building ---- */
struct htree    {
    BYTECOUNTER cnt;        /* character frequency         */
    int parent;             /* offset to parent node       */
    int right;              /* offset to right child node  */
    int left;               /* offset to left child node   */
};
/* ---- Huffman tree structure in compressed file ---- */
struct htr    {
    int right;              /* offset to right child node  */
    int left;               /* offset to left child node   */
};
extern struct htr *HelpTree;
void buildtree(void);
FILE *OpenHelpFile(void);
void HelpFilePosition(long *, int *);
void *GetHelpLine(char *);
void SeekHelpLine(long, int);

#endif
```

#### LISTING_FIVE

```c
/* ------------------- htree.c -------------------- */
#include "dflat.h"
#include "htree.h"

struct htree *ht;
int root;
int treect;

/* ------ build a Huffman tree from a frequency array ------ */
void buildtree(void)
{
    int i;
    treect = 256;
    /* ---- preset node pointers to -1 ---- */
    for (i = 0; i < treect; i++)    {
        ht[i].parent = -1;
        ht[i].right  = -1;
        ht[i].left   = -1;
    }
    /* ---- build the huffman tree ----- */
    while (1)   {
        int h1 = -1, h2 = -1;
        /* ---- find the two lowest frequencies ---- */
        for (i = 0; i < treect; i++)   {
            if (i != h1) {
                struct htree *htt = ht+i;
                /* --- find a node without a parent --- */
                if (htt->cnt > 0 && htt->parent == -1)   {
                    /* ---- h1 & h2 -> lowest nodes ---- */
                    if (h1 == -1 || htt->cnt < ht[h1].cnt) {
                        if (h2 == -1 || ht[h1].cnt < ht[h2].cnt)
                            h2 = h1;
                        h1 = i;
                    }
                    else if (h2 == -1 || htt->cnt < ht[h2].cnt)
                        h2 = i;
                }
            }
        }
        /* --- if only h1 -> a node, that's the root --- */
        if (h2 == -1) {
            root = h1;
            break;
        }
        /* --- combine two nodes and add one --- */
        ht[h1].parent = treect;
        ht[h2].parent = treect;
        ht = realloc(ht, (treect+1) * sizeof(struct htree));
        if (ht == NULL)
            break;
        /* --- the new node's frequency is the sum of the two
            nodes with the lowest frequencies --- */
        ht[treect].cnt = ht[h1].cnt + ht[h2].cnt;
        /* - the new node points to the two that it combines */
        ht[treect].right = h1;
        ht[treect].left = h2;
        /* --- the new node has no parent (yet) --- */
        ht[treect].parent = -1;
        treect++;
    }
}
```

#### LISTING_SIX

```c
/* ------------------- huffc.c -------------------- */
#include "dflat.h"
#include "htree.h"

extern struct htree *ht;
extern int root;
extern int treect;
static int lastchar = '\n';

static void compress(FILE *, int, int);
static void outbit(FILE *fo, int bit);

static int fgetcx(FILE *fi)
{
    int c;
    /* ------- bypass comments ------- */
    if ((c = fgetc(fi)) == ';' && lastchar == '\n')
        do    {
            while (c != '\n' && c != EOF)
                c = fgetc(fi);
        } while (c == ';');
    lastchar = c;
    return c;
}
void main(int argc, char *argv[])
{
    FILE *fi, *fo;
    int c;
    BYTECOUNTER bytectr = 0;

    if (argc < 3)   {
        printf("\nusage: huffc infile outfile");
        exit(1);
    }
    if ((fi = fopen(argv[1], "rb")) == NULL)    {
        printf("\nCannot open %s", argv[1]);
        exit(1);
    }
    if ((fo = fopen(argv[2], "wb")) == NULL)    {
        printf("\nCannot open %s", argv[2]);
        fclose(fi);
        exit(1);
    }
    ht = calloc(256, sizeof(struct htree));

    /* - read the input file and count character frequency - */
    while ((c = fgetcx(fi)) != EOF)   {
        c &= 255;
        ht[c].cnt++;
        bytectr++;
    }
    /* ---- build the huffman tree ---- */
    buildtree();
    /* --- write the byte count to the output file --- */
    fwrite(&bytectr, sizeof bytectr, 1, fo);
    /* --- write the tree count to the output file --- */
    fwrite(&treect, sizeof treect, 1, fo);
    /* --- write the root offset to the output file --- */
    fwrite(&root, sizeof root, 1, fo);
    /* -- write the tree to the output file -- */
    for (c = 256; c < treect; c++)   {
        int lf = ht[c].left;
        int rt = ht[c].right;
        fwrite(&lf, sizeof lf, 1, fo);
        fwrite(&rt, sizeof rt, 1, fo);
    }
    /* ------ compress the file ------ */
    fseek(fi, 0L, 0);
    while ((c = fgetcx(fi)) != EOF)
        compress(fo, (c & 255), 0);
    outbit(fo, -1);
    fclose(fi);
    fclose(fo);
    free(ht);
    exit(0);
}
/* ---- compress a character value into a bit stream ---- */
static void compress(FILE *fo, int h, int child)
{
    if (ht[h].parent != -1)
        compress(fo, ht[h].parent, h);
    if (child)  {
        if (child == ht[h].right)
            outbit(fo, 0);
        else if (child == ht[h].left)
            outbit(fo, 1);
    }
}
static char out8;
static int ct8;
/* -- collect and write bits to the compressed output file -- */
static void outbit(FILE *fo, int bit)
{
    if (ct8 == 8 || bit == -1)  {
        while (ct8 < 8)    {
            out8 <<= 1;
            ct8++;
        }
        fputc(out8, fo);
        ct8 = 0;
    }
    out8 = (out8 << 1) | bit;
    ct8++;
}
```

#### LISTING_SEVEN

```c
/* ------------------- decomp.c -------------------- */
/* Decompress the application.HLP file or load the application.TXT file if
 * the .HLP file does not exist */

#include "dflat.h"
#include "htree.h"

static int in8;
static int ct8 = 8;
static FILE *fi;
static BYTECOUNTER bytectr;
static int LoadingASCII;
struct htr *HelpTree;
static int root;

/* ------- open the help database file -------- */
FILE *OpenHelpFile(void)
{
    char *cp;
    int treect, i;
    char helpname[65];
    /* -------- get the name of the help file ---------- */
    BuildFileName(helpname, ".hlp");
    if ((fi = fopen(helpname, "rb")) == NULL)    {
        /* ---- no .hlp file, look for .txt file ---- */
        if ((cp = strrchr(helpname, '.')) != NULL)    {
            strcpy(cp, ".TXT");
            fi = fopen(helpname, "rt");
        }
        if (fi == NULL)
            return NULL;
        LoadingASCII = TRUE;
    }
    if (!LoadingASCII && HelpTree == NULL)    {
        /* ----- read the byte count ------ */
        fread(&bytectr, sizeof bytectr, 1, fi);
        /* ----- read the frequency count ------ */
        fread(&treect, sizeof treect, 1, fi);
        /* ----- read the root offset ------ */
        fread(&root, sizeof root, 1, fi);
        HelpTree = DFcalloc(treect-256, sizeof(struct htr));
        /* ---- read in the tree --- */
        for (i = 0; i < treect-256; i++)    {
            fread(&HelpTree[i].left,  sizeof(int), 1, fi);
            fread(&HelpTree[i].right, sizeof(int), 1, fi);
        }
    }
    return fi;
}
/* ----- read a line of text from the help database ----- */
void *GetHelpLine(char *line)
{
    int h;
    if (LoadingASCII)    {
        void *hp;
        do
            hp = fgets(line, 160, fi);
        while (*line == ';');
        return hp;
    }
    *line = '\0';
    while (TRUE)    {
        /* ----- decompress a line from the file ------ */
        h = root;
        /* ----- walk the Huffman tree ----- */
        while (h > 255)    {
            /* --- h is a node pointer --- */
            if (ct8 == 8)   {
                /* --- read 8 bits of compressed data --- */
                if ((in8 = fgetc(fi)) == EOF)    {
                    *line = '\0';
                    return NULL;
                }
                ct8 = 0;
            }
            /* -- point to left or right node based on msb -- */
            if (in8 & 0x80)
                h = HelpTree[h-256].left;
            else
                h = HelpTree[h-256].right;
            /* --- shift the next bit in --- */
            in8 <<= 1;
            ct8++;
        }
        /* --- h < 255 = decompressed character --- */
        if (h == '\r')
            continue;    /* skip the '\r' character */
        /* --- put the character in the buffer --- */
        *line++ = h;
        /* --- if '\n', end of line --- */
        if (h == '\n')
            break;
    }
    *line = '\0';    /* null-terminate the line */
    return line;
}
/* --- compute the database file byte and bit position --- */
void HelpFilePosition(long *offset, int *bit)
{
    *offset = ftell(fi);
    if (LoadingASCII)
        *bit = 0;
    else    {
        if (ct8 < 8)
            --*offset;
        *bit = ct8;
    }
}
/* -- position the database to the specified byte and bit -- */
void SeekHelpLine(long offset, int bit)
{
    int i;
    fseek(fi, offset, 0);
    if (!LoadingASCII)    {
        ct8 = bit;
        if (ct8 < 8)    {
            in8 = fgetc(fi);
            for (i = 0; i < bit; i++)
                in8 <<= 1;
        }
    }
}
```

Copyright © 1992, Dr. Dobb's Journal
