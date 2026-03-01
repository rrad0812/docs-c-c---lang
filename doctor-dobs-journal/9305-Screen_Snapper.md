
# C PROGRAMMING 9305

## Screen Snapper - Al Stevens

### Grabber ekrana

[LISTING_FOUR](#listing_four) je copysrcn.c, deo programa koji snima ekrane. Kada je učitate, glavna funkcija prikazuje nijanse sive povezane sa svakom bojom pozadine i omogućava vam da ih promenite. Otkrio sam da svi obrasci popunjavanja ne funkcionišu dobro, a neki ekrani se prikazuju bolje ako promenim podrazumevani. Nakon što završite sa tim, program se proglašava rezidentnim.

### The Capture

Program snima sliku ekrana u datoteku pod nazivom SNAP.000. Naredna snimanja tokom istog učitavanja programa nazivaju se SNAP.001, SNAP.002 itd. Da biste pokrenuli program na određenoj numerisanoj ekstenziji, unesite broj kao parametar komandne linije kada ga učitate.

Datoteka će imati tekst na ekranu i naredbe LaserJet-a za štampanje slike na ekranu. Možete kopirati datoteku direktno na štampač da biste videli kako izgleda. Možete i da uvezete datoteku u tekstualnu datoteku dokumenta. Broj redova teksta je isti kao i broj redova na 8,5 lpi. Smatram da komande štampača gobbledigook odvlače pažnju u tekstu. Zbog toga ubacujem komande za spajanje u program za obradu teksta tako da će preuzeti datoteke u vreme štampanja. Promenom margina i linija po inču u procesoru teksta, mogu da centriram figure i održavam integritet stranice. Koristim KsiVrite za DOS obradu teksta i dobro podržava ove operacije.

### Korišćenje različitih laserskih štampača

Neće svi koristiti laserski štampač kompatibilan sa LaserJet, iako većina njih emulira komandni jezik. Ako želite da modifikujete program da radi sa drugim štampačem, on mora da se ponaša barem kao LaserJet u smislu da mora imati komande za pomeranje kursora za štampanje i za štampanje pravougaonika u nijansama sive. Takođe mora imati kompatibilan font.

Makroi LaserJet III u copiscrn.c implementiraju interfejs. Makroi push i pop pišu komande govoreći štampaču da zapamti trenutnu lokaciju kursora i vrati je kasnije. Ovo omogućava programu da privremeno promeni položaj kursora bez potrebe da zna kako da ga vrati. Makroi CH i CV definišu visinu i širinu karaktera u koracima kursora-adrese. Makroi za pomeranje horizontalnog i vertikalnog pokreta prihvataju celobrojni parametar koji određuje relativnu poziciju kursora. Makroi pomeraju kursor za štampanje u skladu sa tim. Makro za senčenje prihvata celobrojnu vrednost koja programira procenat senčenja za obrazac popunjavanja pravougaonog bloka. Makro pravougaonika definiše pravougaonik koji treba da se popuni, a makro za popunjavanje pravougaonika crta definisani pravougaonik sa najnovijim programiranim obrascem popunjavanja. Makro selectfont bira font za dump ekrana, a makro resetfont ga resetuje na podrazumevane vrednosti štampača.

### Pravljenje i pokretanje ekrana Snapper

Koristio sam ekstenzije Turbo C da napravim screen snapper, tako da će vam trebati Borland kompajler da biste ga napravili. Sastavite tri modula sa modelom male memorije i povežite ih zajedno koristeći komandnu liniju bcc -esnap.eke copiscrn.c console.c tsr.c. Ovo će izgraditi snap.eke, TSR program. Kada učitate snap, program će prikazati meni na slici 2(a). Pritisnite cifru povezanu sa bojom i videćete sliku 2(b).

Slika 2: Glavni meni za snimanje ekrana.

```sh
  (a)

  Screen Snapper Version 2 - snap.000
    Gray scale defaults:
     0. Black    45%
     1. Blue     45%
     2. Green    30%
     3. Cyan     15%
     4. Red      45%
     5. Magenta  15%
     6. Brown    30%
     7. White     0%

  Enter number of a color to change,
  Esc to return to defaults,
  Or Enter to accept settings
  ...

  (b)

  Black:

  Sp = change, Esc = quit, Enter = accept
  45%
```

Pritisnite razmaknicu da biste prelazili kroz obrasce popunjavanja, Esc da biste se vratili na raniju postavku i Enter da biste prihvatili bilo koju promenu koju ste napravili. Program se vraća na prvi meni. Nastavite da menjate boje dok ne dobijete željeno podešavanje.

Kada otvorite program, on čuva trenutne konfiguracije miša i kursora, postavlja ih u gornji levi ugao ekrana i uključuje kursor miša. Definišite pravougaonik ekrana pomoću tastature ili miša. Pomoću tastature pomerate kursor u ugao pravougaonika i pritiskate taster F2 da biste rekli programu da sada definišete pravougaonik. Pomerite kursor u suprotni ugao i pritisnite Enter da biste sačuvali snimak ekrana ili Esc da biste ga odbili i vratili na prekinutu aplikaciju. Ponovo pritisnite F2 ako želite da promenite usidreni ugao. Dok pomerate kursor, ekran invertuje boju pozadine tako da možete da vidite definisani pravougaonik. Pomoću miša pomerite kursor miša u ugao i pritisnite i držite levo dugme. Prevucite kursor miša u suprotni ugao. Otpustite dugme. Pravougaonik je definisan. Možete ga ignorisati tako što ćete pomeriti kursor i ponovo pritisnuti dugme. Kada pravougaonik opisuje onaj koji želite da odštampate, pritisnite desni taster miša. Program čuva ekran u datoteku diska, vraća prekinutu konfiguraciju miša i kursora i vraća se na prekinuti program.

#### LISTING_ONE

```c
/* ----------- console.h ------------ */
#ifndef CONSOLE_H
#define CONSOLE_H

#define TRUE  1
#define FALSE 0
#define ESC      27
#define F2      188
#define UP      200
#define FWD     205
#define DN      208
#define BS      203
#define KEYBOARD   0x16
#define ZEROFLAG   0x40
#define SETCURSORTYPE 1
#define SETCURSOR     2
#define READCURSOR    3
#define HIDECURSOR 0x20

int getkey(void);
int keyhit(void);
void curr_cursor(int *, int *);
void cursor(int, int);
void hidecursor(void);
void unhidecursor(void);
void savecursor(void);
void restorecursor(void);
void set_cursor_type(unsigned);

#define MOUSE 0x33
void resetmouse(void);
unsigned mouse_buffer(void);
int mouse_installed(void);
int mousebuttons(void);
void get_mouseposition(int *x, int *y);
void set_mouseposition(int x, int y);
void show_mousecursor(void);
void hide_mousecursor(void);
int button_releases(void);
void intercept_mouse(void *);
void restore_mouse(void *);
#define leftbutton() (mousebuttons()&1)
#define rightbutton() (mousebuttons()&2)

/* ------- defines a screen rectangle ------ */
typedef struct {
    int x, y, x1, y1;
} RECT;

#endif
```

#### LISTING_TWO

```c
/* ----------- console.c ---------- */

#include <bios.h>
#include <dos.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "console.h"

static unsigned video_mode;
static unsigned video_page;
static int cursorpos;
static int cursorshape;

/* ---- Test for keystroke ---- */
int keyhit(void)
{
    _AH = 1;
    geninterrupt(KEYBOARD);
    return (_FLAGS & ZEROFLAG) == 0;
}
/* ---- Read a keystroke ---- */
int getkey(void)
{
    int c;
    while (keyhit() == 0)
        ;
    if (((c = bioskey(0)) & 0xff) == 0)
        c = (c >> 8) | 0x80;
    else
        c &= 0xff;
    return c;
}
static void videoint(void)
{
    static unsigned oldbp;
    _DI = _DI;
    oldbp = _BP;
    geninterrupt(0x10);
    _BP = oldbp;
}
void videomode(void)
{
    _AH = 15;
    videoint();
    video_mode = _AL;
    video_page = _BX;
    video_page &= 0xff00;
    video_mode &= 0x7f;
}
/* ---- Position the cursor ---- */
void cursor(int x, int y)
{
    videomode();
    _DX = ((y << 8) & 0xff00) + x;
    _AX = 0x0200;
    _BX = video_page;
    videoint();
}
/* ---- get cursor shape and position ---- */
static void near getcursor(void)
{
    videomode();
    _AH = READCURSOR;
    _BX = video_page;
    videoint();
}
/* ---- Get current cursor position ---- */
void curr_cursor(int *x, int *y)
{
    getcursor();
    *x = _DL;
    *y = _DH;
}
/* ---- Hide the cursor ---- */
void hidecursor(void)
{
    getcursor();
    _CH |= HIDECURSOR;
    _AH = SETCURSORTYPE;
    videoint();
}
/* ---- Unhide the cursor ---- */
void unhidecursor(void)
{
    getcursor();
    _CH &= ~HIDECURSOR;
    _AH = SETCURSORTYPE;
    videoint();
}
/* ---- Save the current cursor configuration ---- */
void savecursor(void)
{
    getcursor();
    cursorshape = _CX;
    cursorpos = _DX;
}
/* ---- Restore the saved cursor configuration ---- */
void restorecursor(void)
{
    videomode();
    _DX = cursorpos;
    _AH = SETCURSOR;
     _BX = video_page;
    videoint();
    set_cursor_type(cursorshape);
}
/* ----------- set the cursor type -------------- */
void set_cursor_type(unsigned t)
{
    videomode();
    _AH = SETCURSORTYPE;
     _BX = video_page;
    _CX = t;
    videoint();
}
/* --------- generic mouse utility ---------- */
static void mouse(int m1,int m2,int m3,int m4)
{
    _DX = m4;
    _CX = m3;
    _BX = m2;
    _AX = m1;
    geninterrupt(MOUSE);
}
/* ----- test to see if the mouse driver is installed ----- */
int mouse_installed(void)
{
    unsigned char far *ms;
    ms = MK_FP(peek(0, MOUSE*4+2), peek(0, MOUSE*4));
    return (ms != NULL && *ms != 0xcf);
}
/* ---------- reset the mouse ---------- */
void resetmouse(void)
{
    if (mouse_installed())
        mouse(0,0,0,0);
}
/* ------ return true if mouse buttons are pressed ------- */
int mousebuttons(void)
{
    int bx = 0;
    if (mouse_installed())    {
        mouse(3,0,0,0);
        bx = _BX;
    }
    return bx & 3;
}
/* ---------- return mouse coordinates ---------- */
void get_mouseposition(int *x, int *y)
{
    if (mouse_installed())    {
        int mx, my;
        mouse(3,0,0,0);
        mx = _CX;
        my = _DX;
        *x = mx/8;
        *y = my/8;
    }
}
/* -------- position the mouse cursor -------- */
void set_mouseposition(int x, int y)
{
    if(mouse_installed())
        mouse(4,0,x*8,y*8);
}
/* --------- display the mouse cursor -------- */
void show_mousecursor(void)
{
    if(mouse_installed())
        mouse(1,0,0,0);
}
/* --------- hide the mouse cursor ------- */
void hide_mousecursor(void)
{
    if(mouse_installed())
        mouse(2,0,0,0);
}
/* --- return true if a mouse button has been released --- */
int button_releases(void)
{
    int ct = 0;
    if(mouse_installed())    {
        mouse(6,0,0,0);
        ct = _BX;
    }
    return ct;
}
/* --------- get mouse state buffer size --------- */
unsigned mouse_buffer(void)
{
    if (mouse_installed())    {
        mouse(21,0,0,0);
        return _BX;
    }
    return 0;
}
/* ----- intercept mouse in case an interrupted program is using it ------ */
void intercept_mouse(void *bf)
{
    if (mouse_installed())  {
        _ES = _DS;
        mouse(22, 0, 0, (unsigned) bf);
    }
}
/* ----- restore the mouse to the interrupted program ----- */
void restore_mouse(void *bf)
{
    if (mouse_installed())  {
        _ES = _DS;
        mouse(23, 0, 0, (unsigned) bf);
    }
}
```

#### LISTING_THREE

```c
/* --------- tsr.c --------- */
#include <dos.h>
#include <stdlib.h>
#include <stdio.h>
#include "console.h"

void tsr_program(void);
/* ------- the interrupt function registers -------- */
typedef struct {
    int bp,di,si,ds,es,dx,cx,bx,ax,ip,cs,fl;
} IREGS;
#define DISK    0x13
#define CTRLBRK 0x1b
#define INT28   0x28
#define CRIT    0x24
#define CTRLC   0x23
#define TIMER   8
#define KYBRD   9
#define DOS     0x21
#define KEYMASK  8
#define SCANCODE 52
unsigned highmemory;
/* ------ interrupt vector chains ------ */
static void (interrupt *oldtimer)(void);
static void (interrupt *old28)(void);
static void (interrupt *oldkb)(void);
static void (interrupt *olddisk)(void);
/* ------ ISRs for the TSR ------- */
static void interrupt newtimer(void);
static void interrupt new28(void);
static void interrupt newdisk(IREGS);
static void interrupt newkb(void);
static void interrupt newcrit(IREGS);
static void interrupt newbreak(void);
static unsigned sizeprogram; /* TSR's program size     */
unsigned dossegmnt;          /* DOS segment address    */
unsigned dosbusy;            /* offset to InDOS flag   */
static int diskflag;         /* Disk BIOS busy flag    */
unsigned mcbseg;             /* address of 1st DOS mcb */
static char far *mydta;      /* TSR's DTA              */
int hotkeyhit = 0;
int tsrss;          /* TSR's stack segment */
int tsrsp;          /* TSR's stack pointer */
/* -------------- context for the popup ---------------- */
unsigned intpsp;            /* Interrupted PSP address   */
int running;                /* TSR running indicator     */
char far *intdta;           /* interrupted DTA           */
unsigned intsp;             /*     "       stack pointer */
unsigned intss;             /*     "       stack segment */
unsigned ctrl_break;        /* Ctrl-Break setting        */
void (interrupt *oldcrit)(void);
void (interrupt *oldbreak)(void);
void (interrupt *oldctrlc)(void);
/* ------- local prototypes -------- */
static void resident_psp(void);
static void interrupted_psp(void);
static void popup(void);
void initialize(void);

void main(void)
{
    unsigned es, bx;
    initialize();
    /* ---------- compute memory parameters ------------ */
    highmemory = _SS + ((_SP + 256) / 16);
    /* ------ get address of DOS busy flag ---- */
    _AH = 0x34;
    geninterrupt(DOS);
    dossegmnt = _ES;
    dosbusy = _BX;
    /* ---- get the seg addr of 1st DOS MCB ---- */
    _AH = 0x52;
    geninterrupt(DOS);
    es = _ES;
    bx = _BX;
    mcbseg = peek(es, bx-2);
    /* ----- get address of resident program's dta ----- */
    mydta = getdta();
    /* ------------ prepare for residence ------------ */
    tsrss = _SS;
    tsrsp = _SP;
    oldtimer = getvect(TIMER);
    old28 = getvect(INT28);
    oldkb = getvect(KYBRD);
    olddisk = getvect(DISK);
    /* ----- attach vectors to resident program ----- */
    setvect(KYBRD, newkb);
    setvect(INT28, new28);
    setvect(DISK, newdisk);
    setvect(TIMER, newtimer);
    /* ------ compute program size ------- */
    sizeprogram = highmemory - _psp + 1;
    /* ----- terminate and stay resident ------- */
    _DX = sizeprogram;
    _AX = 0x3100;
    geninterrupt(DOS);
}
/* ---------- break handler ------------ */
static void interrupt newbreak(void)
{
    return;
}
/* -------- critical error ISR ---------- */
static void interrupt newcrit(IREGS ir)
{
    ir.ax = 0;            /* ignore critical errors */
}
/* ------ BIOS disk functions ISR ------- */
static void interrupt newdisk(IREGS ir)
{
    diskflag++;
    (*olddisk)();
    ir.ax = _AX;        /* for the register returns */
    ir.cx = _CX;
    ir.dx = _DX;
    ir.es = _ES;
    ir.di = _DI;
    ir.fl = _FLAGS;
    --diskflag;
}
/* ----- keyboard ISR ------ */
static void interrupt newkb(void)
{
    static unsigned char kbval;

    kbval = inportb(0x60);
    if (!hotkeyhit && !running)
        if ((peekb(0, 0x417) & 0xf) == KEYMASK)
            if (SCANCODE == kbval)    {
                hotkeyhit = 1;
                /* --- reset the keyboard ---- */
                kbval = inportb(0x61);
                outportb(0x61, kbval | 0x80);
                outportb(0x61, kbval);
                outportb(0x20, 0x20);
                return;
            }
    (*oldkb)();
}
/* ----- timer ISR ------- */
static void interrupt newtimer(void)
{
    (*oldtimer)();
    if (hotkeyhit && (peekb(dossegmnt, dosbusy) == 0) &&
            !diskflag)
        popup();
}
/* ----- 0x28 ISR -------- */
static void interrupt new28(void)
{
    (*old28)();
    if (hotkeyhit)
        popup();
}
/* ------ switch psp context from interrupted to TSR ----- */
static void resident_psp(void)
{
    intpsp = getpsp();
    _AH = 0x50;
    _BX = _psp;
    geninterrupt(DOS);
}
/* ---- switch psp context from TSR to interrupted ---- */
static void interrupted_psp(void)
{
     _BX = intpsp;
     _AH = 0x50;
    geninterrupt(DOS);
}
/* ------ execute the resident program ------- */
static void popup(void)
{
    running = 1;
    hotkeyhit = 0;
    intsp = _SP;
    intss = _SS;
    _SP = tsrsp;
    _SS = tsrss;
    oldcrit = getvect(CRIT);    /* redirect critical err  */
    oldbreak = getvect(CTRLBRK);
    oldctrlc = getvect(CTRLC);
    setvect(CRIT, newcrit);
    setvect(CTRLBRK, newbreak);
    setvect(CTRLC, newbreak);
    ctrl_break = getcbrk();     /* get ctrl break setting */
    setcbrk(0);                 /* turn off ctrl break    */
    intdta = getdta();          /* get interrupted dta    */
    setdta(mydta);              /* set resident dta       */
    resident_psp();             /* swap psps              */
    enable();
    tsr_program();              /* call the TSR C program */
    disable();
    interrupted_psp();          /* reset interrupted psp  */
    setdta(intdta);             /* reset interrupted dta  */
    setvect(CRIT, oldcrit);     /* reset critical error   */
    setvect(CTRLBRK, oldbreak);
    setvect(CTRLC, oldctrlc);
    setcbrk(ctrl_break);        /* reset ctrl break       */
    disable();
    _SP = intsp;                /* reset interrupted stack*/
    _SS = intss;
    running = 0;                /* reset semaphore        */
}
```

#### LISTING_FOUR

```c
/* ---------------- copyscrn.c -------------- */
#include <stdio.h>
#include <conio.h>
#include <stdlib.h>
#include <dos.h>
#include "console.h"

static void near highlight(RECT);
static void writescreen(RECT);
static void near setstart(int *, int, int);
static void near forward(int);
static void near backward(int);
static void near upward(int);
static void near downward(int);
static void init_variables(void);
extern unsigned _stklen = 1024;
extern unsigned _heaplen = 8192;
static int cursorx, cursory;
static RECT blk;
static int done = 0;
static int kx = 0, ky = 0;
static int mx, my;
static int px = -1, py = -1;
static int mouse_marking = FALSE;
static int keyboard_marking = FALSE;
static int marked_block = FALSE;
static int vpcts[] = {0,2,10,15,30,45,70,90,100};
static int extension = 0;
static int pcts[]   = {45, 45, 30, 15, 45, 15, 30, 0};
static int dfpcts[] = {45, 45, 30, 15, 45, 15, 30, 0};
char *clrs[] = {"Black", "Blue", "Green", "Cyan",
                "Red", "Magenta", "Brown", "White"};
/* ----- LaserJet III Macros ----- */
#define push(fp) fputs("&f0S",fp)
#define pop(fp)  fputs("&f1S",fp)
#define CW 18
#define CH 40
#define movehorizontal(fp,n) fprintf(fp,"&a%dC",n)
#define movevertical(fp,n)   fprintf(fp,"*p%dY",n)
#define shading(fp, c)       fprintf(fp,"*c%dg",c)
#define rectangle(fp,h,w)    fprintf(fp,"%db%dA",h*CH,w*CW)
#define rectanglefill(fp)    fputs("*c2P", fp)
#define selectfont(fp)       \
            fputs("&l6C&l8D(10U(s0p16.67h8.5v0s0b0T",fp);
#define resetfont(fp)        \
            fputs("&l0O(8U(s1p10v0s0b4101T",fp);
/* ----------- make a RECT from coordinates --------- */
RECT rect(int x, int y, int x1, int y1)
{
    RECT rc;
    rc.x = x;
    rc.y = y;
    rc.x1 = x1;
    rc.y1 = y1;
    return rc;
}
static char *FileName(void)
{
    static char fn[15];
    sprintf(fn, "snap.%03d", extension);
    return fn;
}
void initialize(void)
{
    int i, c, done = 0;

    if (_argc > 1)
        extension = atoi(_argv[1]);
    printf("\nScreen Snapper Version 2 - %s", FileName());
    while (!done)   {
        printf("\n  Gray scale defaults:");
        for (i = 0; i < 8; i++)
            printf("\n     %d. %-7.7s %d%%",
                i, clrs[i], pcts[i]);
        printf("\n\nEnter number of a color to change,\n"
                "Esc to return to defaults,\n"
                "Or Enter to accept settings\n...");
        c = getch();
        putch(c);
        switch (c)  {
            case 27:
                for (i = 0; i < 8; i++)
                    pcts[i] = dfpcts[i];
                break;
            case '\r':
                done = 1;
                break;
            default:
                if (c > '0'-1 && c < '8')   {
                    int ch = 0;
                    int cl = c - '0';
                    int p;
                    for (p = 0; p < 8; p++)
                        if (pcts[cl] == vpcts[p])
                            break;
                    printf("\n  %s:", clrs[cl]);
                    printf(
            "\n  Sp = change, Esc = quit, Enter = accept\n");
                    while (ch != 27 && ch != '\r')  {
                        printf("\r%3d%%", vpcts[p]);
                        ch = getch();
                        if (ch == ' ')  {
                            if (++p == 9)
                                p = 0;
                        }
                        else if (ch == '\r')
                            pcts[cl] = vpcts[p];
                        else
                            putch('/a');
                    }
                }
                else
                    putch('/a');
                break;
        }
    }
    printf("\n\nHot key is Alt+Period\nSnapper is resident");
}
/* ----- process keystrokes ------ */
static void keystroke(void)
{
    int key = getkey();
    switch (key)    {
        case FWD:
            if (kx < 79)    {
                if (keyboard_marking)
                    forward(1);
                kx++;
            }
            break;
        case BS:
            if (kx)    {
                if (keyboard_marking)
                    backward(1);
                --kx;
            }
            break;
        case UP:
            if (ky)    {
                if (keyboard_marking)
                    upward(1);
                --ky;
            }
            break;
        case DN:
            if (ky < 24)    {
                if (keyboard_marking)
                    downward(1);
                ky++;
            }
            break;
        case F2:
            mouse_marking = FALSE;
            setstart(&keyboard_marking, kx, ky);
            break;
        case '\r':
            done = 1;
            break;
        case ESC:
            done = -1;
            break;
    }
    cursor(kx, ky);
}
/* ---------- enter here to run screen grabber --------- */
void tsr_program(void)
{
    static char *bf = NULL;
    int bfsize = mouse_buffer();
    /* ------ save the video cursor configuration ------- */
    savecursor();
    set_cursor_type(0x0607);
    unhidecursor();
    if (bfsize)
        if ((bf = malloc(bfsize)) != NULL)
            intercept_mouse(bf);
    resetmouse();
    init_variables();
    curr_cursor(&cursorx, &cursory);
    unhidecursor();
    cursor(0, 0);
    set_mouseposition(0, 0);
    show_mousecursor();
    /* ----- event message dispatching loop ---- */
    done = 0;
    while (done == 0)   {
        if (keyhit())
            keystroke();
        if (leftbutton())   {
            if (!mouse_marking)    {
                px = mx;
                py = my;
                keyboard_marking = FALSE;
                setstart(&mouse_marking, mx, my);
            }
        }
        get_mouseposition(&mx, &my);
        if (mx != px || my != py)  {
            if (mouse_marking)    {
                if (px < mx)
                    forward(mx-px);
                if (mx < px)
                    backward(px-mx);
                if (py < my)
                    downward(my-py);
                if (my < py)
                    upward(py-my);
            }
            px = mx;
            py = my;
        }
        if (button_releases())
            mouse_marking = FALSE;
        if (rightbutton())
            done = 1;
    }

    /* ----- done ------ */
    if (marked_block)    {
        highlight(blk);
        if (done == 1)
            writescreen(rect(min(blk.x, blk.x1),
                                min(blk.y, blk.y1),
                                max(blk.x, blk.x1),
                                max(blk.y, blk.y1)));
    }
    resetmouse();
    if (bf != NULL) {
        restore_mouse(bf);
        free(bf);
        bf = NULL;
    }
    cursor(cursorx, cursory);
    restorecursor();
    init_variables();
}
/* ------- set the start of block marking ------- */
static void near setstart(int *marking, int x, int y)
{
    if (marked_block)
        highlight(blk);      /* turn off old block */

    marked_block = FALSE;
    *marking ^= TRUE;
    blk.x1 = blk.x = x;   /* set the corners of the new block */
    blk.y1 = blk.y = y;
    if (*marking)
        highlight(blk);   /* turn on the new block */
}
/* ----- move the block rectangle forward one position ----- */
static void near forward(int n)
{
    marked_block = TRUE;
    while (n-- > 0)    {
        if (blk.x < blk.x1)
            highlight(rect(blk.x,blk.y,blk.x,blk.y1));
        else
            highlight(rect(blk.x+1,blk.y,blk.x+1,blk.y1));
        blk.x++;
    }
}
/* ---- move the block rectangle backward one position ----- */
static void near backward(int n)
{
    marked_block = TRUE;
    while (n-- > 0)    {
        if (blk.x > blk.x1)
            highlight(rect(blk.x,blk.y,blk.x,blk.y1));
        else
            highlight(rect(blk.x-1,blk.y,blk.x-1,blk.y1));
        --blk.x;
    }
}
/* ----- move the block rectangle up one position ----- */
static void near upward(int n)
{
    marked_block = TRUE;
    while (n-- > 0)    {
        if (blk.y > blk.y1)
            highlight(rect(blk.x,blk.y,blk.x1,blk.y));
        else
            highlight(rect(blk.x,blk.y-1,blk.x1,blk.y-1));
        --blk.y;
    }
}
/* ----- move the block rectangle down one position ----- */
static void near downward(int n)
{
    marked_block = TRUE;
    while (n-- > 0)    {
        if (blk.y < blk.y1)
            highlight(rect(blk.x,blk.y,blk.x1,blk.y));
        else
            highlight(rect(blk.x,blk.y+1,blk.x1,blk.y+1));
        blk.y++;
    }
}
static void fill(FILE *fp, int color, int len)
{
    if (color != LIGHTGRAY && pcts[color] != 0) {
        push(fp);
        // ---- go back len character positions
        movehorizontal(fp, -len);
        movevertical(fp, -30);
        // ----- set the shading
        shading(fp, pcts[color]);
        // ----- define the rectangle
        rectangle(fp, 1, len);
        // ----- fill the rectangle
        rectanglefill(fp);
        pop(fp);
    }
}
/* ------ write the rectangle to the file ------- */
static void writescreen(RECT rc)
{
    FILE *fp = fopen(FileName(), "wt");
    hide_mousecursor();
    extension++;
    if (fp != NULL)    {
        int vx, vy, x;
        int fg, bg, color, colorstart = 0, prevcolor = -1;
        int wd = rc.x1-rc.x+1;
        int margin = 6 + (70-wd)/2;
        int i;

        selectfont(fp);
        fputc('\n', fp);
        // ---------- write the text
        for (vy = rc.y; vy < rc.y1+1; vy++) {
            for (i = 0; i < margin; i++)
                fputc(' ', fp);
            for (vx = rc.x; vx < rc.x1+1; vx++) {
                int vid;
                gettext(vx+1, vy+1, vx+1, vy+1, &vid);
                /* ---- get the video attribute ---- */
                color = (vid >> 8) & 255;
                bg = (color >> 4) & 7;
                fg = color & 15;
                if (bg != prevcolor)    {
                    if (prevcolor != -1)
                         fill(fp, prevcolor, x-colorstart);
                    prevcolor = bg;
                    colorstart = x;
                }
                fputc((bg == BLACK && fg == DARKGRAY) ?
                    0xdb : (vid & 255), fp);
                x++;
            }
            fill(fp, bg, x-colorstart);
            fputc('\n', fp);
            x = 0;
            prevcolor = -1;
            colorstart = 0;
        }
        resetfont(fp);
        fclose(fp);
    }
}
#define swap(a,b) {int s=a;a=b;b=s;}
/* -------- invert the video of a defined rectangle ------- */
static void near highlight(RECT rc)
{
    int *bf, *bf1, bflen;
    if (rc.x > rc.x1)
        swap(rc.x,rc.x1);
    if (rc.y > rc.y1)
        swap(rc.y,rc.y1);
    bflen = (rc.y1-rc.y+1) * (rc.x1-rc.x+1) * 2;
    if ((bf = malloc(bflen)) != NULL)    {
        hide_mousecursor();
        gettext(rc.x+1, rc.y+1, rc.x1+1, rc.y1+1, bf);
        bf1 = bf;
        bflen /= 2;
        while (bflen--)
            *bf1++ ^= 0x7700;
        puttext(rc.x+1, rc.y+1, rc.x1+1, rc.y1+1, bf);
        show_mousecursor();
        free(bf);
    }
}
/* ---- initialize global variables for later popup ---- */
static void init_variables(void)
{
    kx = ky = blk.x = blk.y = blk.x1 = blk.y1 = 0;
    px = py = -1;
    mouse_marking = FALSE;
    keyboard_marking = FALSE;
    marked_block = FALSE;
}
```

Copyright © 1993, Dr. Dobb's Journal
