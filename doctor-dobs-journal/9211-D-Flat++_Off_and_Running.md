
# C PROGRAMMING 9211

## D-Flat++ Off and Running - Al Stevens

### D-Flat++ gciljevi

D-Flat++ biblioteka klasa je uveliko u toku. Na njegov dizajn u velikoj meri utiču moja iskustva sa D-Flat-om. DF++ će podržati moje originalne zahteve bolje nego D-Flat, jer će biti manje, ne više, i biće u C++. Ciljevi koji su pokrenuli D-Flat bili su za DOS-baziranu CUA biblioteku u tekstualnom režimu u C-u da podrži neke aplikacije koje sam razvijao. Biblioteka je rasla sa funkcijama kako sam saznao više o CUA i kako su čitaoci odgovarali. Ali kada pogledam svoje D-Flat aplikacije, shvatam da nikada nisam koristio mnoge njegove karakteristike. Primer aplikacije Memopad koristi više D-Flat funkcija nego bilo koja od mojih stvarnih aplikacija. Pitao sam se zašto, i zaključio sam: Ekran u tekstualnom režimu veličine 25k80 ne podržava potpuno CUA tako dobro. Na primer, zašto imati interfejs sa više dokumenata sa minimiziranim prozorima na ekranu u tekstualnom režimu? Kakva korist od simuliranih ikona na tako bednom ekranu?

CUA je pokretna meta. IBM je bar jednom izmenio svoj standardni referentni dokument. Dobijam e-poruke u kojima se žale da D-Flat ne funkcioniše baš kao ovaj ili onaj "usaglašen sa CUA". Svaki program ima svoju interpretaciju. IBM-ov standardni referentni dokument je nedvosmislen, ali izgleda da ga niko ne čita. Microsoft redefiniše standard uz manje izmene – zapravo poboljšanja – interfejsa Vindovs 3.1. Njihova verzija će, bez sumnje, prevladati.

Koji su naši zahtevi? Ako nam je potreban kompletan C++ CUA paket, imamo nekoliko načina. Microsoft-ovi i Borland-ovi paketi okvira aplikacija su C++ klase koje se povezuju sa Vindovs API-jem. Borlandov Turbo Vision je biblioteka klasa C++ sa punim funkcijama koja implementira CUA u DOS tekstualnom režimu. Šta nam treba od D-Flat++ što ne možemo da nabavimo na drugom mestu? Potrebna nam je jednostavna biblioteka klasa koja implementira minimalne funkcije neophodne za pokretanje aplikacije za jednog korisnika i jednog dokumenta. CUA deo će podržavati menije, dijaloške okvire i uobičajene kontrole.

### Radna površina

Ovog meseca ćemo pogledati dizajn D-Flat++ desktopa i njegovih ulazno/izlaznih uređaja i razgovarati o nekim DF++ konceptima uopšteno.

DF++ je objektno orijentisan dizajn. Aplikacija će raditi iz globalnog objekta koji se zove „desktop“. Klasa DeskTop sadrži jedan objekat prozora aplikacije i objekte uređaja – miš, tastatura, ekran, kursor, zvučnik i objekti sat. Aplikacioni program ne deklariše većinu ovih objekata. Već postoji DeskTop objekat pod nazivom desktop kada se program pokrene i već ima sve uređaje osim miša ako nije instaliran drajver za miš. Aplikacioni program deklarira prozor aplikacije, koji se automatski povezuje sa globalnim objektom radne površine.

DF++ program će definisati svoju aplikaciju definisanjem menija i dijaloških okvira i izvođenjem prilagođene klase prozora aplikacije iz osnovne klase aplikacije. DF++ usvaja model D-Flat zasnovan na porukama zasnovan na događajima, ali koristi karakteristike C++ za upravljanje porukama. Aplikacioni program postavlja prozor aplikacije i dozvoljava desktop objektu da prikuplja događaje i šalje poruke. Razlika je u tome što su poruke funkcije članice klase koje pripadaju različitim klasama prozora. Kosa obrada SendMessage D-Flat-a je nepotrebna u DF++.

[LISTING_ONE](#listing_one) je "desktop.h", koji definiše klasu DeskTop. Klasa uključuje pokazivače na prozor aplikacije, prozor koji ima fokus i prozor, ako postoji, koji je uhvatio fokus.

Imati fokus je karakteristika koju deli većina biblioteka prozora koje koriste metaforu desktopa. Korisnik ima ekran sa više prozora na njemu. Neki od njih prikazuju informacije koje korisnik treba da pročita; drugi pružaju načine na koje korisnik može da unese informacije. Postoje meniji, okviri sa listama, okviri za uređivanje, dugmad i tako dalje, a pažnja korisnika je usmerena samo na jedan po jedan od njih. Za taj kontrolni prozor se kaže da „ima fokus“. Kada korisnik pritisne taster ili pomeri ili klikne mišem, taj događaj bi trebalo da ide u prozor koji ima fokus. Taster može da skroluje tekst prozora, da dodaje otkucani znak u tekst prozora, da pritisne dugme ili da izvrši bilo koju od brojnih stvari koje korisnik može da uradi. To može čak dovesti do toga da drugi prozor dobije fokus.

Ponekad će prozorski objekat uhvatiti fokus, što znači da sve dok ga prozor ne otpusti, nijedna od radnji korisnika neće biti poslata drugom prozoru. Radna površina održava jednostavnu listu prozora koji su uhvatili fokus. Kada prozor uhvati fokus, pridružuje se listi. Kada prozor oslobodi fokus, radna površina predaje fokus prozoru koji ga je poslednji imao.

Radna površina deklariše sve objekte uređaja. Liste od dva do sedam, počevši od strane 182, su screen.h, mouse.h, cursor.h, keiboard.h, speaker.h i clock.h, koji definišu klase za objekte uređaja. Aplikacioni program će ispitivati ili modifikovati ove uređaje tako što će im slati poruke preko radne površine.

### Događaji i poruke

Aplikacija poziva funkciju DispatchEvents na radnoj površini da započne obradu događaja i poruka. Ta funkcija zatim poziva funkcije DispatchEvents objekata sismouse, siskeiboard i sisclock. Te funkcije ispituju svoje odgovarajuće uređaje i pozivaju odgovarajuću funkciju člana prozora koji treba da dobije poruku. Objekat uređaja određuje koji prozor treba da bude. Poruke sa tastature idu u prozor koji je uhvatio fokus, ako ga ima. U suprotnom idu ili u prozor koji ima fokus ili u prozor aplikacije. U svim slučajevima, poruke se šalju kao pozivi funkciji člana prozora preko jednog od pokazivača u radnom objektu. Poruke miša idu do prozora na koji miš pokazuje osim ako prozor nije uhvatio miša, u kom slučaju prozor za snimanje dobija poruku, bez obzira na to gde kursor miša pokazuje.

Kada objekat prozora dobije poruku, može da uradi jednu od četiri stvari. Prvo, objekat prozora može obraditi poruku i vratiti se. Drugo, može odlučiti da ga poruka ne zanima, ali da neka osnovna klasa negde gore u hijerarhiji klasa kojoj pripada klasa prozora treba da obradi poruku. Treće, objekat prozora može napraviti kombinaciju prva dva. Konačno, može da presretne i odbije poruku, odlučujući da ni ona ni njegove osnovne klase ne treba da obrađuju poruku.

Pretpostavimo da imate klasu EditBok izvedenu iz klase TektBok (što ćete, na kraju krajeva), i da objekat te klase ima fokus. Korisnik pritisne taster, a klasa EditBok dobija poruku sa tastature. Primer 1 pokazuje kako će klasa vežbati četiri opcije o kojima smo upravo govorili za obradu poruka.

Primer 1: Obrada poruke.

```c
Void EditBox::Keyboard(int key)
{
  switch (key)   {
    case F1:
         // --- process the F1 key
         // ...
         break;
    case F2:
         // --- pass the F2 key up the hierarchy
         TextBox::Keyboard(key);
         break;
    case F3:
         // --- process the F3 key
         // ...
         // --- pass the F3 key up the hierarchy
         TextBox::Keyboard(key);
         break;
    case F4:
         // --- intercept and reject the F4 key
         break;
    default:
         break;
    }
}
```

Objekti prozora mogu slati poruke drugim objektima prozora ili sebi. Iskaču meniji zbog poruka između prozora, a procesi aplikacije se pozivaju porukama o izboru menija. Kasnije kolumne će detaljno opisati ove procese. Koncept slanja poruka nije nov ni za C++ programere ni za programere u okruženjima vođenim događajima sličnim Vindovs-u. Ovaj projekat koristi C++ poruke za implementaciju poruka vođenih događajima. Do sada u mom razvoju, DF++ programi su jednostavniji i izražajniji od D-Flat programa, bez sumnje zbog prirodnog uklapanja C++ poruka i poruka korisničkog interfejsa vođenih događajima.

#### LISTING_ONE

```c
// ---------- desktop.h
#ifndef DESKTOP_H
#define DESKTOP_H

#include "screen.h"
#include "cursor.h"
#include "keyboard.h"
#include "mouse.h"
#include "speaker.h"
#include "clock.h"

class DFWindow;

class DeskTop {
    DFWindow *apwnd;        // application window
    DFWindow *infocus;      // current window with the focus
    DFWindow *firstcapture; // first window to capture the focus
    DFWindow *focuscapture; // current window with captured focus
    // ------- the desktop devices
    Screen   sysscreen;     // the system screen
    Mouse    sysmouse;      // the system mouse
    Keyboard syskeyboard;   // the system keyboard
    Cursor   syscursor;     // the system cursor
    Clock    sysclock;      // the system clock
    Speaker  sysspeaker;    // the system speaker
public:
    DeskTop();
    ~DeskTop();
    DFWindow *ApplWnd() { return apwnd; }
    void SetApplication(DFWindow *ApWnd) { apwnd = ApWnd; }
    Bool DispatchEvents();
    DFWindow *InFocus()      { return infocus; }
    DFWindow *FocusCapture() { return focuscapture; }
    DFWindow *FirstCapture() { return firstcapture; }
    void SetFocus(DFWindow *wnd) { infocus = wnd; }
    void SetFocusCapture(DFWindow *wnd) { focuscapture = wnd; }
    void SetFirstCapture(DFWindow *wnd) { firstcapture = wnd; }
    // ------- the desktop devices
    Mouse&    mouse()       { return sysmouse;    }
    Screen&   screen()      { return sysscreen;   }
    Keyboard& keyboard()    { return syskeyboard; }
    Cursor&   cursor()      { return syscursor;   }
    Clock&    clock()       { return sysclock;    }
    Speaker&  speaker()     { return sysspeaker;  }
};
extern DeskTop desktop;

#endif
```

#### LISTING_TWO

```c
// ----------- screen.h
#ifndef SCREEN_H
#define SCREEN_H

#include <dos.h>
#include "dflatdef.h"
#include "rectangl.h"

class Screen    {
    unsigned address;
    unsigned mode;
    unsigned page;
    unsigned height;
    unsigned width;
    union REGS regs;
    // ---- compute video offset address
    unsigned vad(int x, int y) { return y * (width*2) + x*2; }
public:
    Screen();
    unsigned Height() { return height; }
    unsigned Width()  { return width; }
    unsigned Page()   { return page; }
    Bool isEGA();
    Bool isVGA();
    Bool isMono() { return (Bool) (mode == 7); }
    Bool isText() { return (Bool) (mode < 4);  }
    void Scroll(Rect &rc, int d, int fg, int bg);
    unsigned int GetVideoChar(int x, int y);
    void PutVideoChar(int x, int y, unsigned int c);
    void WriteVideoString(char *s,int x,int y,int fg,int bg);
    void SwapBuffer(Rect &rc, char *bf,
        Bool Hiding, Bool HasShadow, Bool isFrame);
    void GetBuffer(Rect &rc, char *bf);
    void PutBuffer(Rect &rc, char *bf);
};
const int VIDEO = 0x10;
inline int clr(int fg, int bg)
{
    return fg | (bg << 4);
}
#endif
```

#### LISTING_THREE

```c
// ---------- mouse.h
#ifndef MOUSE_H
#define MOUSE_H

#include <dos.h>
#include "dfwindow.h"
#include "timer.h"

class Mouse    {
    Bool installed;      // True = mouse is installed
    char *statebuffer;   // mouse state buffer
    Timer doubletimer;   // mouse double-click timer
    Timer delaytimer;    // mouse typematic click timer
    int prevx;           // previous mouse x coordinate
    int prevy;           // previous mouse y coordinate
    int clickx;          // click x position
    int clicky;          // click y position
    int releasex;        // release x position
    int releasey;        // release y position
    union REGS regs;
    DFWindow *MouseWindow(int mx, int my);
    void CallMouse(int m1,int m2=0,int m3=0,int m4=0,unsigned es=0);
    void DispatchRelease();
    void DispatchMove();
    void DispatchLeftButton();
public:
    Mouse();
    ~Mouse();
    Bool Installed() { return installed; }
    void GetPosition(int &x, int &y); // get mouse position
    void SetPosition(int x, int y);   // set mouse position
    Bool Moved();           // True if mouse has moved
    void Show();            // show the mouse cursor
    void Hide();            // hide the mouse cursor
    Bool LeftButton();     // True if left button is pressed
    Bool ButtonReleased(); // True if button was released
    void SetTravel(int minx, int maxx, int miny, int maxy);
    void DispatchEvent();
};
const int MOUSE          = 0x33;  // mouse interrupt vector
// -------- mouse commands
const int RESETMOUSE     =  0;
const int SHOWMOUSE      =  1;
const int HIDEMOUSE      =  2;
const int READMOUSE      =  3;
const int SETPOSITION    =  4;
const int BUTTONRELEASED =  6;
const int XLIMIT         =  7;
const int YLIMIT         =  8;
const int BUFFSIZE       = 21;
const int SAVESTATE      = 22;
const int RESTORESTATE   = 23;
// -------- timer delays for mouse repeat, double clicks
const int DELAYTICKS     =  1;
const int FIRSTDELAY     =  7;
const int DOUBLETICKS    =  5;

#endif
```

#### LISTING_FOUR

```c
// ------------- cursor.h
#ifndef CURSOR_H
#define CURSOR_H

// ------- video BIOS (0x10) functions
const int SETCURSORTYPE = 1;
const int SETCURSOR     = 2;
const int READCURSOR    = 3;
const int HIDECURSOR    = 0x20;

const int MAXSAVES = 50;  // depth of cursor save/restore stack
class Cursor {
    // --- cursor save/restore stack
    int cursorpos[MAXSAVES];
    int cursorshape[MAXSAVES];
    int cs;             // count of cursor saves in effect
    union REGS regs;
    void Cursor::GetCursor();
public:
    Cursor();
    ~Cursor();
    void SetPosition(int x, int y);
    void GetPosition(int &x, int &y);
    void SetType(unsigned t);
    void normalcursor() { SetType(0x0607); }
    void Hide();
    void Show();
    void Save();
    void Restore();
    void SwapStack();
};
inline void swap(int a, int b)
{
    int x = a;
    a = b;
    b = x;
}
#endif
```

#### LISTING_FIVE

```c
// ------------ keyboard.h
#ifndef KEYBOARD_H
#define KEYBOARD_H

#include <dos.h>
#include "dflatdef.h"

class Keyboard    {
    union REGS regs;
    int shift;
public:
    Keyboard() { shift = GetShift(); }
    Bool ShiftChanged();
    int ShiftState() { return shift = GetShift(); }
    int AltConvert(int);
    int GetKey();
    int GetShift();
    Bool KeyHit();
    void clearBIOSbuffer();
    void DispatchEvent();
};
const int KEYBOARDVECT = 9;
const int KEYBOARDPORT = 0x60;

inline void Keyboard::clearBIOSbuffer()
{
    *(int far *)(MK_FP(0x40,0x1a)) =
    *(int far *)(MK_FP(0x40,0x1c));
}
// ----- keyboard BIOS (0x16) functions
const int KEYBRD = 0x16;
const int READKB = 0;
const int KBSTAT = 1;

const int ZEROFLAG = 0x40;
const int OFFSET = 0x1000;

const int RUBOUT = 8;
const int BELL   = 7;
const int ESC    = 27;
const unsigned F1          = (187+OFFSET);
const unsigned F8          = (194+OFFSET);
const unsigned SHIFT_F8    = (219+OFFSET);
const unsigned F10         = (196+OFFSET);
const unsigned HOME        = (199+OFFSET);
const unsigned UP          = (200+OFFSET);
const unsigned PGUP        = (201+OFFSET);
const unsigned BS          = (203+OFFSET);
const unsigned FWD         = (205+OFFSET);
const unsigned END         = (207+OFFSET);
const unsigned DN          = (208+OFFSET);
const unsigned PGDN        = (209+OFFSET);
const unsigned INS         = (210+OFFSET);
const unsigned DEL         = (211+OFFSET);
const unsigned CTRL_HOME   = (247+OFFSET);
const unsigned CTRL_PGUP   = (132+OFFSET);
const unsigned CTRL_BS     = (243+OFFSET);
const unsigned CTRL_FIVE   = (143+OFFSET);
const unsigned CTRL_FWD    = (244+OFFSET);
const unsigned CTRL_END    = (245+OFFSET);
const unsigned CTRL_PGDN   = (246+OFFSET);
const unsigned SHIFT_HT    = (143+OFFSET);
const unsigned ALT_A       = (158+OFFSET);
const unsigned ALT_B       = (176+OFFSET);
const unsigned ALT_C       = (174+OFFSET);
const unsigned ALT_D       = (160+OFFSET);
const unsigned ALT_E       = (146+OFFSET);
const unsigned ALT_F       = (161+OFFSET);
const unsigned ALT_G       = (162+OFFSET);
const unsigned ALT_H       = (163+OFFSET);
const unsigned ALT_I       = (151+OFFSET);
const unsigned ALT_J       = (164+OFFSET);
const unsigned ALT_K       = (165+OFFSET);
const unsigned ALT_L       = (166+OFFSET);
const unsigned ALT_M       = (178+OFFSET);
const unsigned ALT_N       = (177+OFFSET);
const unsigned ALT_O       = (152+OFFSET);
const unsigned ALT_P       = (153+OFFSET);
const unsigned ALT_Q       = (144+OFFSET);
const unsigned ALT_R       = (147+OFFSET);
const unsigned ALT_S       = (159+OFFSET);
const unsigned ALT_T       = (148+OFFSET);
const unsigned ALT_U       = (150+OFFSET);
const unsigned ALT_V       = (175+OFFSET);
const unsigned ALT_W       = (145+OFFSET);
const unsigned ALT_X       = (173+OFFSET);
const unsigned ALT_Y       = (149+OFFSET);
const unsigned ALT_Z       = (172+OFFSET);
const unsigned ALT_1      = (0xf8+OFFSET);
const unsigned ALT_2      = (0xf9+OFFSET);
const unsigned ALT_3      = (0xfa+OFFSET);
const unsigned ALT_4      = (0xfb+OFFSET);
const unsigned ALT_5      = (0xfc+OFFSET);
const unsigned ALT_6      = (0xfd+OFFSET);
const unsigned ALT_7      = (0xfe+OFFSET);
const unsigned ALT_8      = (0xff+OFFSET);
const unsigned ALT_9      = (0x80+OFFSET);
const unsigned ALT_0      = (0x81+OFFSET);
const unsigned ALT_HYPHEN = (130+OFFSET);
const unsigned CTRL_F4    = (225+OFFSET);
const unsigned ALT_F4     = (235+OFFSET);
const unsigned ALT_F6     = (237+OFFSET);

enum {CTRL_A=1,CTRL_B,CTRL_C,CTRL_D,CTRL_E,CTRL_F,CTRL_G,CTRL_H,
        CTRL_I,CTRL_J,CTRL_K,CTRL_L,CTRL_M,CTRL_N,CTRL_O,CTRL_P,
        CTRL_Q,CTRL_R,CTRL_S,CTRL_T,CTRL_U,CTRL_V,CTRL_W,CTRL_X,
        CTRL_Y,CTRL_Z };
// ------- BIOS shift key mask values
const int RIGHTSHIFT = 0x01;
const int LEFTSHIFT  = 0x02;
const int CTRLKEY    = 0x04;
const int ALTKEY     = 0x08;
const int SCROLLLOCK = 0x10;
const int NUMLOCK    = 0x20;
const int CAPSLOCK   = 0x40;
const int INSERTKEY  = 0x80;

#endif
```

#### LISTING_SIX

```c
// --------- speaker.h
#ifndef SPEAKER_H
#define SPEAKER_H

class Speaker    {
    void Wait(int n);
public:
    void Beep();
};
const int FREQUENCY = 100;
const long COUNT = 1193280L / FREQUENCY;

#endif
```

#### LISTING_SEVEN

```c
// --------- clock.h
#ifndef CLOCK_H
#define CLOCK_H

#include "timer.h"

class Clock    {
    Timer clocktimer;
public:
    Clock();
    void DispatchEvent();
};
#endif
```

Copyright © 1992, Dr. Dobb's Journal
