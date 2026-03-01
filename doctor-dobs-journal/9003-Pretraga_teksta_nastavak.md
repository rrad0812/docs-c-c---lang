
# C PROGRAMMING - 9003

## TEXTSRCH nastavak - Al Stivens

### TEXTSRCH

Da biste instalirali nove funkcije pregleda datoteka TEXTSRCH-a, morate da zamenite izvornu datoteku pod nazivom "search.c" iz prošlog meseca sa onom u [LISTING_TWO](listing_two). Takođe morate da kompajlirate i povežete "display.c", [LISTING_THREE](#listing_three) i "error.c", [LISTING_FOUR](listing_four).

BLDINDEX program radi na isti način kao i ranije. Nova funkcija je u programu TEXTSRCH. Kada unesete izraz upita, rezultati se sada prikazuju u prozoru ekrana sa ASCII -> kursorom levo od imena svake datoteke. Pomoću tastera sa strelicama nagore i nadole pomerate taj kursor i pomerate ekran. Kada kursor pokaže na datoteku koju biste možda želeli da pogledate, pritisnite taster Enter. Prva stranica izabranog teksta dokumenta prikazuje se u novom prozoru preko celog ekrana. Tasteri sa strelicama nagore i nadole će pomerati ekran. Tasteri nagore i nadole će prikazati stranicu. Taster Home ide na prvu stranicu, a taster End na poslednju. Tokom prikaza sva pojavljivanja ključnih reči iz izraza upita prikazuju se u istaknutom režimu. Možete da pređete na sledeću stranicu na kojoj se pojavljuje ključna reč pritiskom na taster sa strelicom udesno. Taster sa strelicom nalevo pomera vas na prethodnu stranicu gde se pojavljuje ključna reč.

Evo kako da koristite CURSES da biste postigli ove rezultate. Funkcija process_result u <search.c> je promenjena. Umesto da prikazuje imena datoteka koje se podudaraju, ona pravi niz tih imena. Zatim poziva funkciju CURSES initscr da bi inicijalizovao menadžer ekrana, poziva select_tekt tako da korisnik može da izabere datoteku koju će pogledati, i poziva funkciju CURSES endvin da bi isključio menadžer ekrana.

Funkcija select_tekt je mesto gde korisnik bira datoteku za pregled. Koristimo funkciju CURSES nevvin da napravimo prozor menija. Funkcija tastature omogućava rutinama tastature CURSES da prepoznaju karaktere tastature, a funkcija vsetcrreg definiše granice pomeranja prozora. Korišćenje ove funkcije sprečava da se ivice prozora pomeraju zajedno sa ostatkom.

Funkcija displai_page prikazuje određenu stranicu menija datoteke u prozoru. U početku ga zovemo da prikaže prvu stranicu. Zatim nacrtamo okvir oko prozora, napišemo ASCII -> selektor kursor i pročitamo tastaturu. Različiti slučajevi ispod prekidača za pritiskanje tastera vode računa o pomeranju kursora selektora gore i dole, kao i o stranicama i pomeranju menija birača datoteka. Kada korisnik pritisne taster Enter, taj slučaj poziva funkciju displai_tekt, prenoseći ime izabrane datoteke kao što je prikazano u prozoru menija.

U ovom trenutku moramo razmotriti vrednosti dodeljene različitim ključevima koje tumačimo. Preuzete su iz datoteke zaglavlja Lattice <curses.h> i odgovaraju onome što Lattice verzija funkcije CURSES vgetch vraća za tastere sa kursorom kada je uključen režim tastature CURSES. Ove vrednosti se možda ne primenjuju na različita okruženja. Takođe pogledajte upotrebu globalnih promenljivih VERT_DOUBLE i HORIZ_DOUBLE u pozivu funkcije okvira CURSES. I oni se pojavljuju u <curses.h> i odgovaraju grafičkim znakovima računara za ivice znakova. Možda ćete morati da promenite ove vrednosti u nešto što odgovara vašem sistemu. CURSES ne obezbeđuje granične uglove znakova, ali implementacija Lattice prepoznaje IBM skup i koristi odgovarajuće uglove grafičke karaktere.

Pogledajte sada LISTING_tri da vidite kod koji prikazuje tekstualnu datoteku. Funkcija pod nazivom displai_tekt otvara datoteku i poziva njenu funkciju do_displai ako se datoteka otvori OK. Ako nije, poziva funkciju error_handler koju ćete naći u Listingu četiri. Ova funkcija opšte namene prikazuje poruku o grešci u prozoru, čeka na pritisak tastera i briše poruku.

Funkcija do_displai čita sve redove teksta iz izabrane datoteke i skladišti ih u povezanu listu u hrpi. Lista povezuje svaki red sa sledećim redom i beleži pozicije bilo koje ključne reči u svakom redu.

Funkcija findkeis brine o pronalaženju i čuvanju ključnih reči. Skenira red teksta upoređujući svaku reč sa onim u izrazu upita. Ako se reč poklapa sa jednim od ključeva, njen pomak karaktera u odnosu na početak reda ide u blok zaglavlja povezanog unosa u listi linije. Blok zaglavlja može da sadrži do pet ključnih reči za svaki red, što bi trebalo da bude dovoljno da vam skrene pažnju na red.

Nakon što su svi redovi teksta skriveni u povezanoj listi, program pravi prozor preko celog ekrana za prikaz teksta. Funkcija displai_tektpage prikazuje stranicu teksta koja počinje određenim redom. Prikazuje linije po jedan znak. Ako je trenutna pozicija karaktera označena u bloku zaglavlja linije kao pozicija ključne reči, program poziva funkciju CURSES vpstandout da izazove da reč bude istaknuta. Kada program pronađe sledeći znak razmaka, poziva funkciju CURSES vpstandend da vrati ekran u normalan, neistaknuti režim.

Kada se stranica prikaže, program čita tastaturu. Kao i kod menija za biranje datoteka, vrednosti pritiska na tastere kontrolišu prikaz na ekranu. Možete listati i pomerati se gore i dole, a možete i da pomerate sledeću ili prethodnu stranicu na kojoj se pojavljuje označena ključna reč. Funkcija označena stranica pravi ovaj test, pronalazeći prvi red navedene stranice i gledajući svaki unos na listi da vidi da li bilo koji red ima označenu ključnu reč.

Kada pritisnete taster ESC, funkcija poziva vclear da obriše prozor za prikaz teksta i refresh da osveži to brisanje na ekranu. Zatim briše prozor i oslobađa hrpu povezanih unosa liste.

Nazad u funkciji select_tekt prozor selektora datoteka se ponovo prikazuje i korisnik može izabrati drugu datoteku koju će pogledati.

### TEXTSRCH Performanse

Koliko je efikasan pristup CURSES razvoju prenosivog koda? Dokaz bi bio u uspešnom prenosu programa kao što je ovaj na drugu platformu. Siguran sam da bi se ovaj program prebacio na Unik sistem bez više gužve nego što sam ga ja premestio na Lattice C. Međutim, postoji jedna velika oblast zabrinutosti u takvom potezu. Ne znamo koliko bi program efikasno funkcionisao. CURSES je tehnika za prenosivost koda drajvera ekrana na mnoštvo uređaja za prikaz. Njegova implementacija u biblioteci Lattice čini efikasan i efikasan program jer su koristili sve PC trikove za brzo ažuriranje ekrana. Štaviše, razvio sam ovaj program na i za 20-MHz 386 računar. Jedini način da saznate koliko dobro ili loše bi ova konkretna upotreba CURSES-a funkcionisala na sporijoj mašini ili na drugom terminalu je da pomerite program. Dakle, imajući to na umu, premestio sam TEXTSRCH na najsporiji kompatibilan računar u mojoj kući, 8-MHz COMPAK II. Drago mi je da mogu da prijavim da radi dobro. Ovo ga, međutim, ne kvalifikuje za okruženje u kojem je terminalni uređaj serijski VDT. Sumnjao bih da neki od načina na koje sam koristio CURSES nisu najbolji izbor za takvo podešavanje. Iskusni CURSES programer verovatno intuitivno zna šta treba da radi, a šta da izbegava da bi podržao najefikasniji korisnički interfejs.

Kolektivne sposobnosti i nedostaci CURSES-a u širokom izboru terminala bi, bez sumnje, uticali na način na koji ćete dizajnirati korisnički interfejs. S obzirom na to da se te granice mogu naučiti i imajući sve ovo na umu, mogu zaključiti da je CURSES efikasna tehnika za široku platformsku nezavisnost upravljanja ekranom zasnovanom na tekstu. To, naravno, nije novost za Unik programere, koji već nekoliko godina imaju KLETVE. To je novost za one druge od nas koji možda traže uredne načine za razvoj programa na računaru koji se mogu premestiti u druga operativna okruženja.

#### LISTING_ONE

```c
/* ------------ dir.h ----------- */

/* Substitute Lattice directory functions for
 * Turbo C directory functions
 */

#include <dos.h>

#define ffblk FILEINFO
#define ff_name name

#define findfirst(path,ff,attr) dfind(ff,path,attr)
#define findnext(ff) dnext(ff)
```

#### LISTING_TWO

```c
/* ---------- search.c ----------- */

/*
 * the TEXTSRCH retrieval process
 */

#include <stdio.h>
#include <string.h>
#include <curses.h>
#include "textsrch.h"

static char fnames[MAXFILES] [65];
static int fctr;

static void select_text(void);
static void display_page(WINDOW *file_selector, int pg);
void display_text(char *fname);

/* ---- process the result of a query expression search ---- */
void process_result(struct bitmap map1)
{
    int i;
    extern int file_count;
    for (i = 0; i < file_count; i++)
        if (getbit(&map1, i))
            strncpy(fnames[fctr++], text_filename(i), 64);
    initscr();                 /* initialize curses */
    select_text();             /* select a file to view */
    endwin();                  /* turn off curses */
    fctr = 0;
}

/* ------- search the data base for a word match -------- */
struct bitmap search(char *word)
{
    struct bitmap map1;

    memset(&map1, 0xff, sizeof (struct bitmap));
    if (srchtree(word) != 0)
        map1 = search_index(word);
    return map1;
}

#define HEIGHT 8
#define WIDTH 70
#define HOMEY 3
#define HOMEX 3

#define ESC 27

/* --- select text file from those satisfying the query ---- */
static void select_text(void)
{
    WINDOW *file_selector;
    int selector = 0; /*selector cursor relative to the table */
    int cursor = 0;   /*selector cursor relative to the screen*/
    int keystroke = 0;

    /* --- use a window with a border to display the files -- */
    file_selector = newwin(HEIGHT+2, WIDTH+2, HOMEY, HOMEX);

    keypad(file_selector, 1);       /* turn on keypad mode    */
    noecho();                       /* turn off echo mode     */
    wsetscrreg(file_selector, 1, HEIGHT);/* set scroll limits */

    /* -------- display the first page of the table --------- */
    display_page(file_selector, 0);

    while (keystroke != ESC)    {
        /* ----- draw the window frame ------ */
        box(file_selector, VERT_DOUBLE, HORIZ_DOUBLE);

        /* ------------ fill the selector window ------------ */
        mvwaddstr(file_selector, cursor+1, 1, "->");
        wrefresh(file_selector);

        /* -------------- make a selection ------------------ */
        keystroke = wgetch(file_selector);/* read a keystroke */
        mvwaddstr(file_selector, cursor+1, 1, "  ");
        switch (keystroke)  {

            case KEY_HOME:
                /* -------- Home key (to top of list) ------- */
                selector = cursor = 0;
                display_page(file_selector, 0);
                break;

            case KEY_END:
                /* ------- End key (to bottom of list) ------ */
                selector = fctr - HEIGHT;
                if (selector < 0)   {
                    selector = 0;
                    cursor = fctr-1;
                }
                else
                    cursor = HEIGHT-1;
                display_page(file_selector, selector);
                break;

            case KEY_DOWN:
                /* - down arrow (move the selector cursor) -- */
                /* --------- test at bottom of list --------- */
                if (selector < fctr-1)  {
                    selector++;
                    /* ------ test at bottom of window ------ */
                    if (cursor < HEIGHT-1)
                        cursor++;
                    else    {
                        /* ---- scroll the window up one ---- */
                        scroll(file_selector);
                        /* --- paint the new bottom line ---- */
                        mvwprintw(file_selector, cursor+1, 3,
                            fnames[selector]);
                    }
                }
                break;

            case KEY_UP:
                /* --- up arrow (move the selector cursor) -- */
                /* ----------- test at top of list ---------- */
                if (selector)   {
                    --selector;
                    /* -------- test at top of window ------- */
                    if (cursor)
                        --cursor;
                    else    {
                        /* --- scroll the window down one --- */
                        winsertln(file_selector);
                        /* ----- paint the new top line ----- */
                        mvwprintw(file_selector, 1, 3,
                            fnames[selector]);
                    }
                }
                break;

            case '\n':
                /* -- user selected a file, go display it --- */
                display_text(fnames[selector]);
                break;

            case ESC:
                /* --------- exit from the display ---------- */
                break;

            default:
                /* ----------- invalid keystroke ------------ */
                beep();
                break;
        }
    }
    delwin(file_selector);  /* delete the selector window */
    clear();                /* clear the standard window  */
    refresh();
}

/* ------ display a page of the file selector window ------- */
static void display_page(WINDOW *file_selector, int line)
{
    int y = 0;
    werase(file_selector);
    while (line < fctr && y < HEIGHT)
        mvwprintw(file_selector, ++y, 3, fnames[line++]);
}
```

#### LISTING_THREE

```c
/* ---------------- display.c ----------------- */

/* Display a text file on the screen.
 * User may scroll and page the file.
 * Highlight key words from the search.
 * User may jump to the next and previous key word.
 */

#include <stdio.h>
#include <stdlib.h>
#include <curses.h>
#include <ctype.h>
#include <string.h>
#include "textsrch.h"

#define ESC 27

/* ----------- header block for a line of text ----------- */
struct textline {
    char keys[5];               /* offsets to key words     */
    struct textline *nextline;  /* pointer to next line     */
    char text;                  /* first character of text  */
};

/* --------- listhead for text line linked list -------- */
struct textline *firstline;
struct textline *lastline;

int pagemarked(int topline);
static void do_display(FILE *fp);
static void findkeys(struct textline *thisline);
static void display_textpage(WINDOW *text_window, int line);

/* ---------- display the text in a selected file --------- */
void display_text(char *filepath)
{
    FILE *fp;

    fp = fopen(filepath, "r");
    if (fp != NULL) {
        do_display(fp);
        fclose(fp);
    }
    else    {
        /* ----- the selected file does not exist ----- */
        char ermsg[80];
        sprintf(ermsg, "%s: No such file", filepath);
        error_handler(ermsg);
    }
}

static void do_display(FILE *fp)
{
    char line[120];
    WINDOW *text_window;
    int keystroke = 0;
    int topline = 0;
    int linect = 0;
    struct textline *thisline;

    firstline = lastline = NULL;

    /* --------- read the text file into the heap ------- */
    while (fgets(line, sizeof line, fp) != NULL)    {
        line[78] = '\0';
        thisline =
            malloc(sizeof(struct textline)+strlen(line)+1);
        if (thisline == NULL)
            break;      /* no more room */

        /* ----- clear the text line record space -------- */
        memset(thisline, '\0', sizeof(struct textline) +
                        strlen(line)+1);

        /* ---- build the text line linked list entry ---- */
        if (lastline != NULL)
            lastline->nextline = thisline;
        lastline = thisline;
        if (firstline == NULL)
            firstline = thisline;
        thisline->nextline = NULL;
        strcpy(&thisline->text, line);

        /* ------------ mark the key words ------------ */
        findkeys(thisline);
        linect++;
    }

    /* ------- build a window to display the text ------- */
    text_window = newwin(LINES, COLS, 0, 0);
    keypad(text_window, 1);     /* turn on keypad mode    */

    while (keystroke != ESC)    {
        /* --- display the text and draw the window frame --- */
        display_textpage(text_window, topline);
        box(text_window, VERT_SINGLE, HORIZ_SINGLE);
        wrefresh(text_window);

        /* ------------ read a keystroke ------------- */
        keystroke = wgetch(text_window);
        switch (keystroke)  {
            case KEY_HOME:
                /* ------- Home key (to top of file) ------ */
                topline = 0;
                break;
            case KEY_DOWN:
                /* --- down arrow (scroll up) ---- */
                if (topline < linect-(LINES-2))
                    topline++;
                break;
            case KEY_UP:
                /* ----- up arrow (scroll down) ---- */
                if (topline)
                    --topline;
                break;
            case KEY_PGUP:
                /* -------- PgUp key (previous page) -------- */
                topline -= LINES-2;
                if (topline < 0)
                    topline = 0;
                break;
            case KEY_PGDN:
                /* -------- PgDn key (next page) ------------ */
                topline += LINES-2;
                if (topline <= linect-(LINES-2))
                    break;
            case KEY_END:
                /* ------- End key (to bottom of file) ------ */
                topline = linect-(LINES-2);
                if (topline < 0)
                    topline = 0;
                break;
            case KEY_RIGHT:
                /* - Right arrow. Go to next marked key word  */
                do  {
                    /* -- repeat PGDN until we find a mark -- */
                    topline += LINES-2;
                    if (topline > linect-(LINES-2)) {
                        topline = linect-(LINES-2);
                        if (topline < 0)
                            topline = 0;
                    }
                    if (pagemarked(topline))
                        break;
                }   while (topline &&
                        topline < linect-(LINES-2));
                break;
            case KEY_LEFT:
                /* Left arrow. Go to previous marked key word */
                do  {
                    /* -- repeat PGUP until we find a mark -- */
                    topline -= LINES-2;
                    if (topline < 0)
                        topline = 0;
                    if (pagemarked(topline))
                        break;
                }   while (topline > 0);
                break;
            case ESC:
                break;
            default:
                beep();
                break;
        }
    }
    /* -------- clean up and exit --------- */
    wclear(text_window);
    wrefresh(text_window);
    delwin(text_window);
    thisline = firstline;
    while (thisline != NULL)    {
        free(thisline);
        thisline = thisline-> nextline;
    }
}

/* ---- test a page to see if a marked keyword is on it ---- */
int pagemarked(int topline)
{
    struct textline *tl = firstline;
    int line;
    while (topline-- && tl != NULL)
        tl = tl->nextline;
    for (line = 0; tl != NULL && line < LINES-2; line++)    {
        if (*tl->keys)
            break;
        tl = tl->nextline;
    }
    return *tl->keys;
}

#define iswhite(c) ((c)==' '||(c)=='\t'||(c)=='\n')

/* ---- Find the key words in a line of text. Mark their
   character positions in the text structure ------- */
static void findkeys(struct textline *thisline)
{
    char *cp = &thisline->text;
    int ofptr = 0;

    while (*cp && ofptr < 5)    {
        struct postfix *pf = pftokens;/* the query expression */
        while (iswhite(*cp))  /* skip the white space */
            cp++;
        if (*cp)    {
            /* ---- test this word against each argument in the
                query expression ------- */
            while (pf->pfix != TERM)    {
                if (pf->pfix == OPERAND &&
                        strnicmp(cp, pf->pfixop,
                            strlen(pf->pfixop)) == 0)
                    break;
                pf++;
            }
            if (pf->pfix != TERM)
                /* ----- the word matches a query argument.
                   Put its offset into the line's header --- */
                thisline->keys[ofptr++] =
                    (cp - &thisline->text) & 255;

            /* --- skip to the next word in the line --- */
            while (*cp && !iswhite(*cp))
                cp++;
        }
    }
}

/* --- display page of text starting with specified line --- */
static void display_textpage(WINDOW *text_window, int line)
{
    struct textline *thisline = firstline;
    int y = 1;

    wclear(text_window);
    wmove(text_window, 0, 0);

    /* ---- point to the first line of the page ----- */
    while (line-- && thisline != NULL)
        thisline = thisline->nextline;

    /* ------- display all the lines on the page ------ */
    while (thisline != NULL && y < LINES-1) {
        char *cp = &thisline->text;
        char *kp = thisline->keys;
        char off = 0;
        wmove(text_window, y++, 1);

        /* ------ a character at a time -------- */
        while (*cp) {
            /* --- is this character position a key word? --- */
            if (*kp && off == *kp)  {
                wstandout(text_window); /* highlight key words*/
                kp++;
            }

            /* ---- is this character white space? ---- */
            if (iswhite(*cp))
                wstandend(text_window); /* turn off hightlight*/

            /* ---- write the character to the window ------ */
            waddch(text_window, *cp);
            off++;
            cp++;
        }
        /* -------- a line at a time ---------- */
        thisline = thisline->nextline;
    }
}
```

#### LISTING_FOUR

```c
/* ------------- error.c ------------- */

/* General-purpose error handler */

#include <curses.h>
#include <string.h>

void error_handler(char *ermsg)
{
    int x, y;
    WINDOW *error_window;

    x = (COLS - (strlen(ermsg)+2)) / 2;
    y = LINES/2-1;
    error_window = newwin(3, 2+strlen(ermsg), y, x);
    box(error_window, VERT_SINGLE, HORIZ_SINGLE);
    mvwprintw(error_window, 1, 1, ermsg);
    wrefresh(error_window);
    beep();
    getch();
    wclear(error_window);
    wrefresh(error_window);
    delwin(error_window);
}
```
