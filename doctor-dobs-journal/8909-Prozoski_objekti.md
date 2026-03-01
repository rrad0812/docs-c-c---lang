# C programiranje 8909

## C++: Prozorski objekti - Al Stevens

### Izvedene klase prozora

"Window.h" i "window.c" sadrže tri klase izvedene iz klase Windov. U C++ izvedena klasa nasleđuje karakteristike klase iz koje je izvedena. Tri izvedene klase obezbeđuju:

- prozore sa obaveštenjima,
- prozore sa greškama i
- prozor YesNo.

Ovi uslužni prozori se pojavljuju, prikazuju poruku, čekaju pritisak na taster i iskaču. U svakom slučaju, prozor prikazuje poruku za inicijalizaciju koja je navedena kada je klasa deklarisana i čeka na pritisak pre nego što nestane. Prozor YesNo zahteva pritisak na taster Y ili N i vraća tačnu vrednost ako je ključ Y. Razlika između druga dva su boje prozora. Kako je objavljeno, prozor sa greškom je crven sa žutim slovima koji trepću, a prozor sa obaveštenjima ima crna slova na cijan pozadini. YesNo prozori su beli na zelenom.

### Funkcije konzole

Kao i sa svim takvim sistemima, moramo se baviti hardverom konzole. Definisao sam grupu funkcija koje upravljaju ekranima, kursorom i tastaturom. Većina njih ima odgovarajuće funkcije u Zortech biblioteci displeja. [LISTING_THREE](#listing_three), "console.h" sadrži makroe za Zortech funkcije i prototipove za ostale. [LISTING_FOUR](#listing_four) je "console.c", koji sadrži funkcije koje su nam potrebne, ali koje nisu predstavljene Zortech funkcijama prikaza. Korisnici drugih kompajlera moraju zameniti makroe ili funkcije za one koje podržavaju Zortech funkcije. Slede funkcije upravljanja konzolom koje su potrebne klasi Window i koje se pružaju ili kao C funkcije u "console.c" ili makroi za Zortech funkcije u "console.h":

**hidecursor**  
Ova funkcija sakriva kursor tako da njegova pozicija i dalje utiče na prikaz teksta, ali je sam kursor nevidljiv.

**unhidecursor**
Ova funkcija čini kursor ponovo vidljivim.

**savecursor**
Ova funkcija čuva trenutnu konfiguraciju kursora na steku.

**restorecursor**
Ova funkcija vraća konfiguraciju kursora na onu koja je poslednja gurnuta na stek kursora.

**getkey**
Ova funkcija dobija pritisak na taster izbegavanjem DOS poziva da bi se izbegli prekidi Ctrl-Break na MS-DOS računarima. Takođe prevodi funkcije tastera u jedinstvene 8-bitne vrednosti kao što je definisano u "console.h".

**initconsole** i **closeconsole**
Ove funkcije inicijalizuju i zatvaraju funkcije konzole. Mnogi video paketi zahtevaju takve funkcije za podešavanje sistemskih parametara i ispiranje bafera.

**savevideo** i **restaurvideo**
Ove funkcije prenose znakove video memorije u i iz bafera za čuvanje. Koriste se za čuvanje i vraćanje onoga što prozor pokriva kada dođe u opseg. Parametri funkcije određuju adresu bafera i koordinate ugla karaktera prozora.

**box**
Ova funkcija crta jednolinijski okvir na ekranu. Klasa Window ga koristi da napravi ivicu prozora.

**colors**
Ova funkcija uspostavlja boje prednjeg plana i pozadine za naredne prikaze ekrana.

**setcursor**
Ova funkcija pozicionira kursor na određene koordinate znakova.

**window_printf** i **window_putc**
Ovo su prozorski orijentisane verzije printf i putchar.

**videoscroll**
Ovo je funkcija pomeranja prozora. Njegovi parametri uključuju pravac i broj linija za pomeranje i koordinate ugla karaktera.

### Look.c

Program u [LISTING_FIVE](#listing_five) je look.c. On demonstrira klase prozora sa procedurom koja vam omogućava da pregledate tekstualnu datoteku u prozoru preko celog ekrana. Look.c počinje pozivanjem funkcije "set_new_handlerW, BS tehnike koju podržava Zortech i koja poziva vašu funkciju "get" kada novi operater ne može više da dobije memoriju iz besplatne prodavnice. Zatim, program deklariše prozor koji zauzima ceo ekran i koristi metod naslova da napiše naslov u gornjoj ivici prozora. Program postavlja `filebuf` objekat, otvara datoteku koristeći ime u komandnoj liniji i povezuje `instream` objekat sa `filebuf`-om.

Konvencija C++ toka za datoteke je ona za koju smatram da je manje nego intuitivna. Baferi su objekti, a tokovi su objekti. Deklarišete bafer i kažete mu da otvori datoteku. Zatim deklarišete tok i povezujete ga sa baferom. Da biste čitali i pisali datoteku, šaljete poruke metodama strima. Čini mi se da bi sve to moglo da se uradi sa jednim objektom što bi ga učinilo jednostavnijim i lakšim za razumevanje. Ali pošto je konvolucija dva objekta BS tehnika, proizvođači kompajlera će je, bez sumnje, zauvek održavati u ime usaglašenosti.

Program "look.c" čita sve redove teksta iz datoteke, stavlja svaki red u novi bafer i stavlja adrese bafera u niz pokazivača znakova. Zatim tekst ide u prozor preko preopterećenog `<<` operatora.

Da bi demonstrirao upotrebu izvedene klase YesNo, program koristi prozor YesNo da pita da li želite da nastavite. Ako je tako, program poziva funkciju člana stranice da bi vam omogućio skrolovanje i listanje kroz tekst.

Program koristi objekte Error da vam kaže da nije bilo imena datoteke u komandnoj liniji ili da ne može pronaći datoteku koju ste naveli.

#### LISTING_ONE

```cpp
// -------------- window.h

#ifndef WINDOWS
#define WINDOWS

// ---------- screen dimensions
#define SCREENWIDTH 80
#define SCREENHEIGHT 25

// --------- atrribute values for colors
enum color {
    BLACK, BLUE, GREEN, CYAN, RED, MAGENTA, BROWN, LIGHTGRAY,
    GRAY, LIGHTBLUE, LIGHTGREEN, LIGHTCYAN, LIGHTRED,
    LIGHTMAGENTA, YELLOW, WHITE, BLINK = 128
};

// ------------ spaces per tab stop (text displays)
#define TABS 4
// ------------ color assignments for window types
#define YESNOFG  WHITE
#define YESNOBG  GREEN
#define NOTICEFG BLACK
#define NOTICEBG CYAN
#define ERRORFG  (YELLOW | BLINK)
#define ERRORBG  RED

// ------------ a video window
class Window {
    unsigned bg, fg;        // window colors
    unsigned lf,tp,rt,bt;   // window position
    unsigned *wsave;        // video memory save buffer
    unsigned *hsave;        // hide window save buffer
    unsigned row, col;      // current cursor row and column
    int tabs;               // tab stops, this window
    char **text;            // window text content
public:
    Window(unsigned left, unsigned top,
           unsigned right, unsigned bottom,
           color wfg, color wbg);
    ~Window(void);
    void title(char *ttl);
    Window& operator<<(char **btext);
    Window& operator<<(char *ltext);
    Window& operator<<(char ch);
    void cursor(unsigned x, unsigned y);
    void cursor(unsigned *x, unsigned *y)
        { *y = row, *x = col; }
    void clear_window(void);
    void clreos(void);          // clear to end of screen
    void clreol(void);          // clear to end of line
    void hidewindow(void);      // hide an in-scope window
    void restorewindow(void);   // unhide a hidden window
    void page(void);            // page through the text
    void scroll(int d);         // scroll the window up, down
    void set_colors(int cfg, int cbg)   // change the colors
        { fg = cfg, bg = cbg; }
    void set_tabs(int t)        // change the tab stops
        { if (t > 1 && t < 8) tabs = t; }
};

// ---------- utility notice window
class Notice : Window   {
public:
    Notice(char *text);
    ~Notice(){}
};

// ---------- utility yes/no window
class YesNo : Window    {
public:
    YesNo(char *text);
    ~YesNo(){}
    int answer;
};

// ---------- utility error window
class Error : Window    {
public:
    Error(char *text);
    ~Error(){}
};

#define max(x,y) (((x) > (y)) ? (x) : (y))
#define min(x,y) (((x) > (y)) ? (y) : (x))

#endif
```

#### LISTING_TWO

```cpp
// -------------- window.c

// A C++ window library

#include <stddef.h>
#include <string.h>
#include <ctype.h>
#include "window.h"
#include "console.h"

#define HEIGHT (bt - tp + 1)
#define WIDTH  (rt - lf + 1)

// ------- constructor for a Window
Window::Window(unsigned left, unsigned top,     // 0 - 79, 0 - 24
              unsigned right, unsigned bottom,
              color wfg, color wbg)
{
    savecursor();
    initconsole();
    hidecursor();
    // ----- adjust for windows beyond the screen dimensions
    if (right > SCREENWIDTH-1)  {
        left -= right-(SCREENWIDTH-1);
        right = SCREENWIDTH-1;
    }
    if (bottom > SCREENHEIGHT-1)    {
        top -= bottom-(SCREENHEIGHT-1);
        bottom = SCREENHEIGHT-1;
    }
    // ------- initialize window dimensions
    lf = left;
    tp = top;
    rt = right;
    bt = bottom;
    // ------- initialize window colors
    fg = wfg;
    bg = wbg;
    // ------- initialize window cursor and tab stops
    row = col = 0;
    tabs = TABS;
    // ---------- save the video rectangle under the new window
    wsave = new unsigned[HEIGHT * WIDTH];
    hsave = NULL;
    savevideo(wsave, tp, lf, bt, rt);
    // --------- draw the window frame
    box(tp, lf, bt, rt, fg, bg);
    // -------- clear the window text area
    clear_window();
    unhidecursor();
}

// ------- destructor for a Window
Window::~Window(void)
{
    // ----- restore the video RAM covered by the window
    restorevideo(wsave, tp, lf, bt, rt);
    delete wsave;
    if (hsave != NULL)
        delete hsave;
    restorecursor();
}

// ------- hide a window without destroying it
void Window::hidewindow(void)
{
    if (hsave == NULL)  {
        hsave = new unsigned[HEIGHT * WIDTH];
        savevideo(hsave, tp, lf, bt, rt);
        restorevideo(wsave, tp, lf, bt, rt);
    }
}

// --------- restore a hidden window
void Window::restorewindow(void)
{
    if (hsave != NULL)  {
        savevideo(wsave, tp, lf, bt, rt);
        restorevideo(hsave, tp, lf, bt, rt);
        delete hsave;
        hsave = NULL;
        colors(fg,bg);
    }
}

// -------- add a title to a window
void Window::title(char *ttl)
{
    setcursor(lf + (WIDTH - strlen(ttl) - 1) / 2, tp);
    colors(fg, bg);
    window_printf(" %s ", ttl);
    cursor(col, row);
}

// ------- write text body to a window
Window& Window::operator<<(char **btext)
{
    cursor(0, 0);
    text = btext;
    if (*btext != NULL)
        *this << *btext++;
    while (*btext != NULL && row < HEIGHT-3)
        *this << '\n' << *btext++;
}

// -------- write a line of text to a window
Window& Window::operator<<(char *ltext)
{
    while (*ltext && col < WIDTH - 2 && row < HEIGHT - 2)
        *this << *ltext++;
    return *this;
}

// -------- write a character to a window
Window& Window::operator<<(char ch)
{
    cursor(col, row);
    switch (ch) {
        case '\n':
            clreol();
            if (row == HEIGHT-3)
                scroll(1);
            else
                row++;
        case '\r':
            col = 0;
            break;
        case '\b':
            if (col)
                --col;
            break;
        case '\t':
            do
                *this << ' ';
            while (col % tabs);
            break;
        default:
            if (col == WIDTH - 2)
                *this << '\n';
            colors(fg,bg);
            window_putc(ch);
            col++;
            return *this;
    }
    cursor(col, row);
    return *this;
}

// ----- position the window cursor
void Window::cursor(unsigned x, unsigned y)
{
    if (x < WIDTH-2 && y < HEIGHT-2)    {
        setcursor(lf+1+x, tp+1+y);
        row = y;
        col = x;
    }
}

// ------ clear a window to all blamks
void Window::clear_window(void)
{
    cursor(0,0);
    clreos();
}

// --- clear from current cursor position to end of window
void Window::clreos(void)
{
    unsigned rw = row, cl = col;
    clreol();
    col = 0;
    while (++row < HEIGHT-2)
        clreol();
    row = rw;
    col = cl;
}

// --- clear from current cursor position to end of line
void Window::clreol(void)
{
    unsigned cl = col;
    colors(fg,bg);
    while (col < WIDTH-2)
        *this << ' ';
    col = cl;
}

// ----- page and scroll through the text file
void Window::page(void)
{
    int c = 0, lines = 0;
    char **tx = text;

    hidecursor();
    // ------ count the lines of text
    while (*(tx + lines) != NULL)
        lines++;
    while (c != ESC)    {
        c = getkey();
        char **htext = text;
        switch (c)  {
            case UP:
                if (tx != text) {
                    --tx;
                    scroll(-1);
                    unsigned x, y;
                    cursor(&x, &y);
                    cursor(0, 0);
                    *this << *tx;
                    cursor(x, y);
                }
                continue;
            case DN:
                if (tx+HEIGHT-3 < text+lines-1) {
                    tx++;
                    scroll(1);
                    unsigned x, y;
                    cursor(&x, &y);
                    cursor(0, HEIGHT-3);
                    *this << *(tx + HEIGHT - 3);
                    cursor(x, y);
                }
                continue;
            case PGUP:
                tx -= HEIGHT-2;
                if (tx < text)
                    tx = text;
                break;
            case PGDN:
                tx += HEIGHT-2;
                if (tx+HEIGHT-3 < text+lines-1)
                    break;
            case END:
                tx = text+lines-(HEIGHT-2);
                if (tx > text)
                    break;
            case HOME:
                tx = text;
                break;
            default:
                continue;
        }
        *this << tx;
        text = htext;
        clreos();
    }
    unhidecursor();
}

// --------- scroll a window
void Window::scroll(int d)
{
    videoscroll(d, tp+1, lf+1, bt-1, rt-1, fg, bg);
}

// ------ utility notice window
Notice::Notice(char *text)
    : ((SCREENWIDTH-(strlen(text)+2)) / 2, 11,
        ((SCREENWIDTH-(strlen(text)+2)) / 2) + strlen(text)+2,
        14, NOTICEFG, NOTICEBG)
{
    *this << text << "\n Any key ...";
    hidecursor();
    getkey();
    unhidecursor();
    hidewindow();
}

// ------ utility error window
Error::Error(char *text)
    : ( (SCREENWIDTH-(strlen(text)+2)) / 2, 11,
        ((SCREENWIDTH-(strlen(text)+2)) / 2) + strlen(text)+2,
        14, ERRORFG, ERRORBG)
{
    *this << text << "\n Any key ...";
    hidecursor();
    getkey();
    unhidecursor();
    hidewindow();
}

// ------ utility yes/no window
YesNo::YesNo(char *text)
    : ( (SCREENWIDTH-(strlen(text)+10)) / 2, 11,
        ((SCREENWIDTH-(strlen(text)+10)) / 2) + strlen(text)+10,
        13, YESNOFG, YESNOBG)
{
    *this << text << "? (Y/N) ";
    int c = 0;
    hidecursor();
    while (tolower(c) != 'y' && tolower(c) != 'n')
        c = getkey();
    unhidecursor();
    hidewindow();
    answer = tolower(c) == 'y';
}
```

#### LISTING_THREE

```cpp
/* ----------- console.h -------- */

#ifndef CONSOLE
#define CONSOLE

#include <disp.h>

// -------- cursor and keyboard functions (via BIOS)
void hidecursor(void);
void unhidecursor(void);
void savecursor(void);
void restorecursor(void);
int getkey(void);

// -------- key values returned by getkey()
#define BELL      7
#define ESC      27
#define UP      200
#define BS      203
#define FWD     205
#define DN      208
#define HOME    199
#define END     207
#define PGUP    201
#define PGDN    209

#define attr(fg,bg) ((fg)+(((bg)&7)<<4))

// --------- video functions (defined as Zortech C++ equivalents)
#define initconsole()            disp_open()
#define closeconsole()           disp_flush()
#define savevideo(bf,t,l,b,r)    disp_peekbox(bf,t,l,b,r)
#define restorevideo(bf,t,l,b,r) disp_pokebox(bf,t,l,b,r)
#define box(t,l,b,r,fg,bg)       disp_box(1,attr(fg,bg),t,l,b,r)
#define colors(fg,bg)            disp_setattr(attr(fg,bg))
#define setcursor(x,y)           disp_move(y,x)
#define window_printf            disp_printf
#define window_putc              disp_putc
#define videoscroll(d,t,l,b,r,fg,bg) \
        disp_scroll(d,t,l,b,r,attr(fg,bg));


#endif
```

#### LISTING_FOUR

```cpp
/* ----------- console.c --------- */

/* PC-specific console functions */

#include <dos.h>
#include <conio.h>
#include "console.h"

/* ------- video BIOS (0x10) functions --------- */
#define VIDEO         0x10
#define SETCURSORTYPE 1
#define SETCURSOR     2
#define READCURSOR    3
#define HIDECURSOR    0x20

#define SAVEDEPTH     20  /* depth to which cursors are saved */

static int cursorpos[SAVEDEPTH];
static int cursorshape[SAVEDEPTH];
static int sd;

union REGS rg;

/* ---- Low-level get cursor shape and position ---- */
static void getcursor(void)
{
    rg.h.ah = READCURSOR;
    rg.h.bh = 0;
    int86(VIDEO,&rg,&rg);
}

/* ---- Save the current cursor configuration ---- */
void savecursor(void)
{
    getcursor();
    if (sd < SAVEDEPTH) {
        cursorshape[sd] = rg.x.cx;
        cursorpos[sd++] = rg.x.dx;
    }
}

/* ---- Restore the saved cursor configuration ---- */
void restorecursor(void)
{
    if (sd) {
        rg.h.ah = SETCURSOR;
        rg.h.bh = 0;
        rg.x.dx = cursorpos[--sd];
        int86(VIDEO,&rg,&rg);
        rg.h.ah = SETCURSORTYPE;
        rg.x.cx = cursorshape[sd];
        int86(VIDEO,&rg,&rg);
    }
}

/* ---- Hide the cursor ---- */
void hidecursor(void)
{
    getcursor();
    rg.h.ch |= HIDECURSOR;
    rg.h.ah = SETCURSORTYPE;
    int86(VIDEO,&rg,&rg);
}

/* ---- Unhide the cursor ---- */
void unhidecursor(void)
{
    getcursor();
    rg.h.ch &= ~HIDECURSOR;
    rg.h.ah = SETCURSORTYPE;
    int86(VIDEO,&rg,&rg);
}

/* ---- Read a keystroke ---- */
int getkey(void)
{
    rg.h.ah = 0;
    int86(0x16,&rg,&rg);
    if (rg.h.al == 0)
        return (rg.h.ah | 0x80) & 255;
    return rg.h.al & 255;
}
```

#### LISTING_FIVE

```cpp
// ---------- look.c

// A C++ program to demonstrate the use of the window library.
// This program lets you view a text file

#include <stdio.h>
#include <string.h>
#include <stream.hpp>
#include <stdlib.h>
#include "window.h"

#define MAXLINES 200            // maximum number of text lines

static char *wtext[MAXLINES+1]; // pointers to text lines

// --- taken from BS; handles all free store (heap) exhaustions
void out_of_store(void);
typedef void (*PF)();
extern PF set_new_handler(PF);

main(int argc, char *argv[])
{
    set_new_handler(&out_of_store);
    if (argc > 1)   {
        // ---- open a full-screen window
        Window wnd(0,0,79,24,CYAN,BLUE);
        char ttl[80];
        // ------ put the file name in the title
        sprintf(ttl, "Viewing %s", argv[1]);
        wnd.title(ttl);
        filebuf buf;
        if (buf.open(argv[1], input))   {
            istream infile(&buf);
            int t = 0;
            // --- read the file and load the pointer array
            char bf[120], *cp = bf;
            while (t < MAXLINES && !infile.eof())   {
                infile.get(*cp);
                if (*cp != '\r')    {
                    if (*cp == '\n')    {
                        *cp = '\0';
                        wtext[t] = new char [strlen(bf)+1];
                        strcpy(wtext[t++], bf);
                        cp = bf;
                    }
                    else
                        cp++;
                }
            }
            wtext[t] = NULL;
            // ---- write all the text to the window
            wnd << wtext;
            // ---- a YesNo window
            YesNo yn("Continue");
            if (yn.answer)
                wnd.page();
            // ------ a Notice window
            Notice nt("All done.");
        }
        else
            // ------ error windows
            Error err("No such file");
    }
    else
        Error err("No file name specified");
}

// ----- the BS free-store exhaustion handler
void out_of_store(void)
{
    cerr << "operator new failed: out of store\n";
    exit(1);
}
```
