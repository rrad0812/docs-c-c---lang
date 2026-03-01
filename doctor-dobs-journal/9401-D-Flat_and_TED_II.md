
# C PROGRAMMING 9401

## TED i D-Flat++ Editor

Napisao sam deo ove kolumne pomoću TED uređivača teksta koji sam napravio da proverim model D-Flat++ aplikacija. S vremena na vreme, TED zaboravlja da je u režimu prelomanja reči i počinje da skroluje tekst nalevo kako red postaje sve duži i duži. Onda se posle nekog vremena seća i pravilno umota stvari. Mislim da to ima veze sa tim da li kucam razmak kada program dođe do desne margine. Ponekad ću izbrisati znak da ispravim jednu od mnogih štamparskih grešaka. TED briše znak, ali zatim prikazuje pogrešan red teksta. Radim mnogo čuvanja dokumenata.

Kada pronađem ove probleme, rešim ih. Logika proširenja kartica i dalje ima problema, ali znam gde su i šta da radim u vezi sa njima. Nažalost, rešavanje problema rizikuje smanjenje performansi koje nisam shvatio kako da izbegnem. Pa, ako veliki momci mogu da izdaju nekompletan softver sa greškom, mogu i ja.

D-Flat++ ima dve klase uređivača teksta. Prva je klasa EditBox, jednoredni uređivač koji obično vidite u dijaloškim okvirima. Dijalog Open File koristi jedan za kontrolu unosa teksta imena datoteke. Druga klasa je klasa Editor, koja je izvedena iz EditBox-a. Klasa Editor je višelinijski uređivač teksta. Obmotava reči i formira pasuse.

[LISTING_ONE](#listing_one) je "editbox.h", datoteka zaglavlja koja opisuje klasu EditBox, koja je izvedena iz klase TextBox. Klasa EditBox dodaje tri člana podataka: broj tekstualne kolone, zastavicu koja označava režim umetanja/prepisivanja i zastavicu koja označava da je tekst promenio korisnik. Postoje nove funkcije članova za pomeranje kursora od znaka do znaka i reči do reči, kao i za ubacivanje i brisanje karaktera u baferu. Klasa zamenjuje neke funkcije člana TextBox-a, jer će imati drugačije ponašanje, i dodaje neke svoje funkcije.

[LISTING_TWO](#listing_two) je "editbox.cpp", izvorni kod koji implementira klasu EditBox. Iznenađujuće je koliko je malo koda potrebno za implementaciju uređivača teksta kada je veći deo ponašanja već definisan u osnovnoj klasi. Klasa EditBox presreće metode SetFocus, ResetFocus, Paint, Move i ClearText da bi uključila i isključila kursor na tastaturi. Metod Keyboard obrađuje ključeve jedinstvene za EditBox i one različite od TextBox-a.

Sledećeg meseca ćemo razgovarati o klasi Editor, koja je izvedena iz klase EditBok. Funkcije koje još nisam napravio uključuju mogućnost označavanja blokova i obavljanja operacija međuspremnika. Kada se oni završe, D-Flat++ će biti završen.

### Specifikatori pristupa

Pregledajući izvorni kod D-Flat++, vidim da je evoluirao na načine koji liče na mnoge srednje i velike dizajnerske projekte. Potrebna su mu brojna poboljšanja dizajna, posebno među specifikacijama pristupa za članove klase. Možete pogledati većinu deklaracija klasa i videti javne članove koji bi trebalo da budu privatni ili zaštićeni. Neki zaštićeni članovi treba da budu privatni sa zaštićenim funkcijama članova kako bi im se podržao pristup.

Nije da dizajn ne funkcioniše. To radi. Ali delovi dizajna koji bi trebalo da budu sakriveni su izloženi. U dizajnu klase bilo koje veličine i posledice, takvi propusti su u principu neizbežni. Kada nešto počne da funkcioniše, obično se ne vraćate na to. Ali to ne znači da ne možete učiniti nešto povodom toga. Kasnije, kada vreme i raspored budu dozvolili, nameravam da prepravim specifikaciju pristupa za sve klase. (Još uvek mogu da čujem majčin oprez o trotoaru i dobrim namerama.)

Primer 1: Problem kompajliranja u (a) se izbegava korišćenjem pokazivača člana sa `<T>` kvalifikacijom u (b).

```c
(a)
    template <class T>
    class Foo   {
        Foo *nextfoo;
        // ...
    };

(b)
    Foo<T> *nextfoo;
```

Primer 2: Šablonska funkcija

```c
template <class T>
void Foo(T *tp)
{
    *tp = 321;
}

template <class T>
void Bar(T& tr)
{
    Foo(&tr);
}

main()
{

    int x = 123;
    Bar(x);
}
```

Primer 3: Prevodilac objavljuje fatalnu grešku pri kompajliranju ako funkcija koja nije void ne uspe da vrati nešto u (a) iako je to u suprotnosti sa pravilom ARM u (b).

```c
(a)
     #include <iostream.h>
     main()
     {
         cout << "Hello, Timna";
     }


(b)
     extern f();
     main() { }
```

#### LISTING_ONE

```c
// -------- editbox.h
#ifndef EDITBOX_H
#define EDITBOX_H

#include "textbox.h"

class EditBox : public TextBox    {
    void OpenWindow();
protected:
    int column;       // Current column
    Bool changed;     // True if text has changed
    Bool insertmode;  // True if in insert mode
    virtual void Home();
    virtual void End();
    virtual void NextWord();
    virtual void PrevWord();
    virtual void Forward();
    virtual void Backward();
    virtual void DeleteCharacter();
    virtual void InsertCharacter(int key);
public:
    EditBox(const char *ttl, int lf, int tp, int ht, int wd, DFWindow *par=0)
                        : TextBox(ttl, lf, tp, ht, wd, par)
            { OpenWindow(); }
    EditBox(const char *ttl, int ht, int wd, DFWindow *par=0)
                        : TextBox(ttl, ht, wd, par)
            { OpenWindow(); }
    EditBox(int lf, int tp, int ht, int wd, DFWindow *par=0)
                        : TextBox(lf, tp, ht, wd, par)
            { OpenWindow(); }
    EditBox(int ht, int wd, DFWindow *par=0) : TextBox(ht, wd, par)
            { OpenWindow(); }
    EditBox(const char *ttl) : TextBox(ttl)
            { OpenWindow(); }
    // -------- API messages
    virtual Bool SetFocus();
    virtual void ResetFocus();
    virtual void SetCursor(int x, int y);
    virtual void ResetCursor();
    virtual void SetCursorSize();
    virtual unsigned char CurrentChar()
            { return (*text)[column]; }
    virtual unsigned CurrentCharPosition()
            { return column; }
    virtual Bool AtBufferStart()
            { return (Bool) (column == 0); }
    virtual void Keyboard(int key);
    virtual void Move(int x, int y);
    virtual void Paint();
    virtual void PaintCurrentLine()
            { Paint(); }
    virtual void ClearText();
    virtual void LeftButton(int mx, int my);
    Bool Changed()
            { return changed; }
    void ClearChanged()
            { changed = False; }
    Bool InsertMode()
            { return insertmode; }
    void SetInsertMode(Bool imode)
            { insertmode = imode; ResetCursor(); }
};
inline Bool isWhite(int ch)
{
    return (Bool)
        (ch == ' ' || ch == '\n' || ch == '\t' || ch == '\r');
}
#endif
```

#### LISTING_TWO

```c
// ------------- editbox.cpp
#include <ctype.h>
#include "desktop.h"
#include "editbox.h"

// ----------- common constructor code
void EditBox::OpenWindow()
{
    windowtype = EditboxWindow;
    column = 0;
    changed = False;
    text = new String(1);
    BuildTextPointers();
}
Bool EditBox::SetFocus()
{
    Bool rtn = TextBox::SetFocus();
    if (rtn)    {
        ResetCursor();
        desktop.cursor().Show();
    }
    return rtn;
}
void EditBox::ResetFocus()
{
    desktop.cursor().Hide();
    TextBox::ResetFocus();
}
// -------- process keystrokes
void EditBox::Keyboard(int key)
{
    int shift = desktop.keyboard().GetShift();
    if ((shift & ALTKEY) == 0)    {
        switch (key)    {
            case HOME:
                Home();
                return;
            case END:
                End();
                return;
            case CTRL_FWD:
                NextWord();
                return;
            case CTRL_BS:
                PrevWord();
                return;
            case FWD:
                Forward();
                return;
            case BS:
                Backward();
                return;
            case RUBOUT:
                if (CurrentCharPosition() == 0)
                    break;
                Backward();
                // --- fall through
            case DEL:
                DeleteCharacter();
                BuildTextPointers();
                PaintCurrentLine();
                return;
            default:
                if (!isprint(key))
                    break;
                // --- printable keys processed by editbox
                InsertCharacter(key);
                BuildTextPointers();
                PaintCurrentLine();
                return;
        }
    }
    TextBox::Keyboard(key);
}
// -------- paint the editbox
void EditBox::Paint()
{
    TextBox::Paint();
    ResetCursor();
}
// -------- move the editbox
void EditBox::Move(int x, int y)
{
    TextBox::Move(x, y);
    ResetCursor();
}
// --------- clear the text from the editbox
void EditBox::ClearText()
{
    TextBox::ClearText();
    OpenWindow();
    ResetCursor();
}
// ----- move cursor to left margin
void EditBox::Home()
{
    column = 0;
    if (wleft)    {
        wleft = 0;
        Paint();
    }
    ResetCursor();
}
// ----- move the cursor to end of line
void EditBox::End()
{
    int ch;
    while ((ch = CurrentChar()) != '\0' && ch != '\n')
        column++;
    if (column - wleft >= ClientWidth())    {
        wleft = column - ClientWidth() + 1;
        Paint();
    }
    ResetCursor();
}
// ---- move the cursor to the next word
void EditBox::NextWord()
{
    while (!isWhite(CurrentChar()) && CurrentChar())
        Forward();
    while (isWhite(CurrentChar()))
        Forward();
}
// ---- move the cursor to the previous word
void EditBox::PrevWord()
{
    Backward();
    while (isWhite(CurrentChar()) && !AtBufferStart())
        Backward();
    while (!isWhite(CurrentChar()) && !AtBufferStart())
        Backward();
    if (isWhite(CurrentChar()))
        Forward();
}
// ---- move the cursor one character to the right
void EditBox::Forward()
{
    if (CurrentChar())    {
        column++;
        if (column-wleft == ClientWidth())
            ScrollLeft();
        ResetCursor();
    }
}
// ---- move the cursor one character to the left
void EditBox::Backward()
{
    if (column)    {
        if (column == wleft)
            ScrollRight();
        --column;
        ResetCursor();
    }
}
// ---- insert a character into the edit buffer
void EditBox::InsertCharacter(int key)
{
    unsigned col = CurrentCharPosition();
    if (insertmode || CurrentChar() == '\0')    {
        // ---- shift the text to make room for new character
        String ls, rs;
        if (col)
            ls = text->left(col);
        int rt = text->Strlen()-col;
        if (rt > 0)
            rs = text->right(rt);
        *text = ls + " " + rs;
    }
    (*text)[col] = (char) key;
    if (key == '\n')
        BuildTextPointers();
    Forward();
    changed = True;
}
// ---- delete a character from the edit buffer
void EditBox::DeleteCharacter()
{
    if (CurrentChar())    {
        String ls, rs;
        unsigned col = CurrentCharPosition();
        if (col)
            ls = text->left(col);
        int rt = text->Strlen()-col-1;
        if (rt > 0)
            rs = text->right(rt);
        *text = ls + rs;
        changed = True;
    }
}
// ---- position the cursor
void EditBox::SetCursor(int x, int y)
{
    desktop.cursor().SetPosition(
        x+ClientLeft()-wleft, y+ClientTop()-wtop);
}
// ---- left mouse button was pressed
void EditBox::LeftButton(int mx, int my)
{
    if (ClientRect().Inside(mx, my))    {
        column = max(0, min(text->Strlen()-1,
                          mx-ClientLeft()+wleft));
        ResetCursor();
    }
    else
        TextBox::LeftButton(mx, my);
}
// ---- set the size of the cursor
void EditBox::SetCursorSize()
{
    if (insertmode)
        desktop.cursor().BoxCursor();
    else
        desktop.cursor().NormalCursor();
}
// ---- reset the cursor
void EditBox::ResetCursor()
{
    SetCursorSize();
    if (visible)
        SetCursor(column, 0);
}
```

Copyright © 1994, Dr. Dobb's Journal
