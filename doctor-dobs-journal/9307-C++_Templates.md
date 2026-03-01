
# C PROGRAMMING 9307

## C++ Templates and Filling in some Historical Gaps - Al Stevens

This month's column looks at C++ templates. The template implements what Stroustrup calls "parameterized types" in a paper in the Winter 1989 Journal of the USENIX Association. His proposal for the addition of class templates and function templates was ultimately implemented and released in the AT&T 3.0 C++ system, adopted by the ANSI C++ technical committee, and is now available in several commercial compilers.

 Let's consider what templates do and where they are useful. A class template is a generic class that takes on meaning when it is compiled to support objects of some other concrete class.

 Consider data types that are maintained in collections of objects. An application has many different classes and potentially many different ways to organize them. Depending on how the application manages the objects, it might use a number of different container organizations, such as trees, queues, lists, and stacks. Each container type exists only to manage objects. You could have a stack of pointers, a queue of integers, a balanced tree of employee objects, a linked list of window handles, and so on. What is more, you could have a stack of date objects, a queue of date objects, and a tree of date objects, each container supporting different storage and retrieval requirements, but all of them containing objects of the same type. Each data type has its behavior, and each container type has its behavior, and the behaviors of the two are unrelated. If I want to organize my objects of type SolidCube into a stack in one part of the program and into a list in another, that decision has nothing to do with the behavior of the SolidCube class. Making that distinction is essential to understanding templates.

 The template is a mechanism with which you define a class that you instantiate only in conjunction with another type. Given a class template for type `LinkedList<T>`, for example, you may declare an object of that type only by associating it with another type, the type that the linked list manages. The `LinkedList<T>` class template is the "parameterized" type. The other type, represented by the `<T>`, is the parameter. (There can be more than one.) Therefore, if you instantiate the LinkedList class with, for example, the Date class, you have declared an object that is a linked list of Date objects, or, more precisely, of type `LinkedList<Date>`.

 Before looking at templates, let's consider ways to implement such things with traditional C++. I'll discuss three alternatives for managing objects in a linked list without using templates and give examples of two of them. Then I'll build a class template to perform the same operation.

### The Linked List

First, a brief explanation of the linked-list data structure. A list is a collection of like objects, not necessarily in an array. They could be on the heap, on the stack, declared as global or static variables, or any combination of these. A linked list associates the objects with one another in an incidental sequence unrelated to any particular collating sequence. The first object points to the second object, which points to the third object, and so on. If the list is bidirectional, the third object also points to the second, which points to the first. So, each object in the list has one or two pointers: a pointer to the next object and a pointer to the previous object. To navigate the list, the program uses a listhead, which contains a pointer to the first object in the list, and, if the list is bidirectional, a pointer to the last object. If the list is bidirectional, you can insert objects into, and delete objects from, the middle of the list.

 There are two ways to look at such a container. The container class can either be a repository that makes a copy of the object and puts it in the container, or it can "containerize" the user's copy of the object.

### First Alternative - Do It Yourself

A C++ programmer has at least three options without templates. The simplest solution adds the next and previous pointers to the classes that need linked-list management and builds a listhead that points specifically to objects of that class. There are disadvantages to this approach. First, you have to modify the class to add the behavior of a linked list, which is unrelated to its original purpose. You would probably do that with inheritance. Now, besides having your concrete SolidCube class, you have something like a LinkedListSolidCube class, which is one more class than you need, and not a particularly good application of inheritance. The other disadvantage is that you have to write linked-list code for every such derived class instead of having it be generic behavior. Duplicating that code in multiple classes creates maintenance problems.

### Second Alternative - An Embedded LinkedList Class

Rather than duplicate the code for every class, you could build a generic LinkedList class and embed an object of it in the target class. This approach shares one disadvantage with the first alternative in that it requires modifications to the class. It does, however, eliminate the duplication of the linked-list code.

 [LISTING_ONE](#listing_one) is emblist.h, the header file that defines the LinkedList class to embed in a target class. Two classes are defined: the LinkedList class, which is the listhead; and the ListEntry class, which encapsulates the linked list behavior. That behavior includes the nextentry and preventry pointers to other ListEntry objects.

 ListEntry also includes the listhead pointer to an object of type LinkedList to associate the object with a particular list.

 Since a ListEntry object is embedded in the target object, and since an embedded object cannot determine its owner's address, the ListEntry class includes a void pointer named thisentry, which points to the outer object. It must be void because the generic class does not know about the type that it supports. That's where the "parameterized" part of templates is going to help.

 The ListHead class contains pointers to the first and last objects in the list. These point to embedded ListEntry objects. The address of the listed object is dereferenced through the thisentry pointer mentioned earlier.

 [LISTING_TWO](#listing_two) is emblist.cpp, which contains member functions for the LinkedList and ListEntry classes. It includes the constructors and destructors and ListEntry member functions to append and remove objects from the list. Observe that the ListEntry constructor's parameter is a void pointer to the object of the outer class. This pointer initializes the ListEntry's thisentry pointer. This technique is aesthetically displeasing. It offends me to know that an object stores its own address, even in a data member of an embedded object. Furthermore, it compromises the type safety of the linked list. The compiler can't prevent me from passing any address as an argument to that parameter.

 The ListEntry::AppendEntry function appends the outer object to the linked list by modifying the pointers in the LinkedList object and by setting its own nextentry and preventry pointers. The ListEntry::RemoveEntry function does the opposite, removing the object from the list, patching any hole opened by its departure, and repairing the LinkedList object's firstentry and lastentry pointers if the departing object is the first or last object in the list.

 The destructor for the ListEntry class calls its own ListEntry::RemoveEntry function, and the destructor for the LinkedList class calls the ListEntry::

RemoveEntry function for every object in the list. That is because this implementation of a linked list does not make copies of the objects. It adds linked list behavior to the user's copies. When an object goes out of scope, its destructor is called. The linked list has to expel the object to preserve the list's integrity. If the LinkedList object goes out of scope while objects are still in the list, the objects will seem to be in a list that no longer exists.

 [LISTING_THREE](#listing_three) is testemb.cpp, the program that uses the LinkedList and ListEntry classes. It defines a Date class in a linked list. The class has the usual day, month, and year data members, and it overloads the << insertion operator to display itself. There are two additions to the class to support the linked list. First is the ListEntry le data member. Second is the call to the ListEntry constructor from the constructor for the Date object. This call initializes the thisentry pointer with the Date object's this pointer, which is where type safety breaks down. You could pass any address, including a NULL address, and the compiler wouldn't complain.

 The program declares a LinkedList object and then gets dates from the user and appends them to the linked list by calling the ListEntry le object's AppendEntry function.

 After the last date entry, the program iterates through the list and displays the dates on the console. The calls to ListEntry::FirstEntry and ListEntry::NextEntry return void pointers, so they are cast to Date pointers.

 I didn't bother deleting all of the dates from the heap in these examples, and a more complete program would do that. Also, a more complete ListEntry class would include member functions to retrieve the last and previous entries as well as to insert entries into specified places in the list.

### Third Alternative - Inherit the Linked-list Behavior

We can eliminate some of our objections to the LinkedList and ListEntry classes. Instead of embedding the ListEntry object, the Date object is derived from the base ListEntry class, inheriting the linked-list behavior. This approach improves the code's notation, and it improves type safety by eliminating void pointers, but it introduces a new objection. Inheritance is typically used to model the is a relationship between classes. By deriving Date from ListEntry, we are saying that a date is a list entry. Well, yes it is, but only in the programmer's view. It is only incidental to the way the program manages objects that a date is a list entry, and the model does not reflect common-sense object-oriented design. Nonetheless, this approach is our final alternative to templates.

 [LISTING_FOUR](#listing_four) is inhlist.h, and [LISTING_FIVE](#listing_five) is inhlist.cpp, modify the LinkedList class to be a base class. The thisentry void pointer is gone, and the other void pointers and void pointer functions are now pointers to type ListEntry. Since the target object is derived from, rather than host to, the ListEntry class, the this pointer serves as the address of the object.

 [LISTING_SIX](#listing_six) is testinh.cpp, the test program modified to use the base class. The only concession that the Date class makes to being in a linked list is that it is derived from the ListEntry class. AppendEntry is not called through an embedded object but directly through the Date object itself. The calls to ListEntry::FirstEntry and ListEntry::NextEntry still need casts, however. They return pointers to the base ListEntry class which must be cast to pointers to the Date class.

### A LinkedList Class Template

We can eliminate all of our objections to the approaches just discussed by using templates. Realize first, however, that when you declare an object of a template class, the compiler builds source code that associates the template with its parameter class. If you use the same template for a different type, you get another copy of the source code, customized for the other type. If the algorithm is big and your program needs many versions of it, the template solution, while easier to code, might produce a bigger executable program than you want.

 [LISTING_SEVEN](#listing_seven) is linklist.h, the LinkedList class template. The header file contains the class definition and the member-function templates. The compiler uses these member-function templates to build source code when your program declares an object of a class-template type. The template is a form of macro, and all of its code must be visible wherever you declare a parameterized type. This version of the linked list contains everything. It includes functions to append, insert, remove, and find objects on the list. This implementation makes copies of the objects that go into the list, which means that your program can let the objects go out of scope after you put them in the list, but also means that your classes must have valid copy constructors.

 Only the `LinkedList<T>` class can declare objects of the `ListEntry<T>` class because `ListEntry<T>` has no public members and the `LinkedList<T>` class is a friend. The list-navigation member functions are in the `LinkedList<T>` class instead of the `ListEntry<T>` class. The user declares an object of the `LinkedList<T>` class template with a parameter and adds to, deletes from, and navigates that list through the `LinkedList<T>` object.

 [LISTING_EIGHT](#listing_eight) is testtmpl.cpp, a program that tests the `LinkedList<T>` class template. Its Date class is unaware that its objects are in a linked list. This is the strength of the class template. You don't have to monkey with the target class to get it into a container, and you don't have to give up type checking.

 The program declares an object of type `LinkedList<Date>`, then gets dates from the user and puts them into the list. The program does not retain copies of the objects. This is another strength of the class template: It knows its own size and can instantiate objects of itself. A base class cannot do that and include the derived class's members in the instantiated object. An embedded class cannot do that and include the outer class. Templates solve that problem. In this example, the `ListEntry<T>` class includes a copy of the parameterized type. The `LinkedList<T>::AppendEntry` and `LinkedList<T>::InsertEntry` functions build an object of type `ListEntry<T>` on the heap, initializing it with the object being added to the list.

#### LISTING_ONE

```c
// ------------ emblist.h
// a linked list class embedded in the listed class

#ifndef EMBLIST_H
#define EMBLIST_H

class LinkedList;

// --- the linked list entry
class ListEntry    {
    void *thisentry;
    ListEntry *nextentry;
    ListEntry *preventry;
    LinkedList *listhead;
    friend class LinkedList;
public:
    ListEntry(void *entry, LinkedList *lh = 0);
    ~ListEntry() { RemoveEntry(); }
    void AppendEntry(LinkedList *lh = 0);
    void RemoveEntry();
    void *NextEntry()
        { return nextentry ? nextentry->thisentry : 0; }
    void *PrevEntry()
        { return preventry ? preventry->thisentry : 0; }
};
// ---- the linked list
class LinkedList    {
    // --- the listhead
    ListEntry *firstentry;
    ListEntry *lastentry;
    friend class ListEntry;
public:
    LinkedList();
    ~LinkedList();
    void *FirstEntry() { return firstentry->thisentry; }
    void *LastEntry()  { return lastentry->thisentry; }
};
#endif
```

#### LISTING_TWO

```c
// ------------ emblist.cpp
// linked list class

#include "emblist.h"

// ---- construct a linked list
LinkedList::LinkedList()
{
    firstentry = 0;
    lastentry = 0;
}
// ---- destroy a linked list
LinkedList::~LinkedList()
{
    while (firstentry)
        firstentry->RemoveEntry();
}
// ---- construct a linked list entry
ListEntry::ListEntry(void *entry, LinkedList *lh)
{
    thisentry = entry;
    listhead = lh;
    nextentry = 0;
    preventry = 0;
}
// ---- append an entry to the linked list
void ListEntry::AppendEntry(LinkedList *lh)
{
    if (lh)
        listhead = lh;
    if (listhead != 0)    {
        preventry = listhead->lastentry;
        if (listhead->lastentry)
            listhead->lastentry->nextentry = this;
        if (listhead->firstentry == 0)
            listhead->firstentry = this;
        listhead->lastentry = this;
    }
}
// ---- remove an entry from the linked list
void ListEntry::RemoveEntry()
{
       // ---- repair any break made by this removal
       if (nextentry)
           nextentry->preventry = preventry;
       if (preventry)
           preventry->nextentry = nextentry;
    if (listhead)    {
        // --- maintain listhead if this is last and/or first
        if (this == listhead->lastentry)
            listhead->lastentry = preventry;
        if (this == listhead->firstentry)
            listhead->firstentry = nextentry;
    }
    preventry = 0;
    nextentry = 0;
}
```

#### LISTING_THREE

```c
// -------- testemp.cpp

#include <iostream.h>
#include "emblist.h"

class Date {
    int mo, da, yr;
public:
    ListEntry le;
    Date(int m=0, int d=0, int y=0) : le(this)
        { mo = m; da = d; yr = y; }
    friend ostream& operator << (ostream& os, Date& dt)
    { os << dt.da << æ/' << dt.mo << æ/' << dt.yr; return os; }
};
void main()
{
    LinkedList dtlist;
    int d = 0, m, y;
    Date *dt;
    while (d != 99)    {
        cout << "Enter dd mm yy (99 .. .. when done): ";
        cout << flush;
        cin >> d >> m >> y;
        if (d != 99)    {
            dt = new Date(m,d,y);
            dt->le.AppendEntry(&dtlist);
        }
    }
    dt = (Date *) dtlist.FirstEntry();
    while (dt != 0)    {
        cout << æ\n' << *dt;
        dt = (Date *) dt->le.NextEntry();
    }
}
```

#### LISTING_FOUR

```c
// ------------ inhlist.h
// a linked list base class
#ifndef INHLIST_H
#define INHLIST_H

class LinkedList;

// --- the linked list entry
class ListEntry    {
    ListEntry *nextentry;
    ListEntry *preventry;
    LinkedList *listhead;
    friend class LinkedList;
protected:
    ListEntry(LinkedList *lh = 0);
    virtual ~ListEntry() { RemoveEntry(); }
public:
    void AppendEntry(LinkedList *lh = 0);
    void RemoveEntry();
    ListEntry *NextEntry() { return nextentry; }
    ListEntry *PrevEntry() { return preventry; }
};
// ---- the linked list
class LinkedList    {
    // --- the listhead
    ListEntry *firstentry;
    ListEntry *lastentry;
    friend class ListEntry;
public:
    LinkedList();
    ~LinkedList();
    ListEntry *FirstEntry() { return firstentry; }
    ListEntry *LastEntry()  { return lastentry; }
};

#endif
```

#### LISTING_FIVE

```c
// ------------ inhlist.cpp
// linked list base class
#include "inhlist.h"

// ---- construct a linked list
LinkedList::LinkedList()
{
    firstentry = 0;
    lastentry = 0;
}
// ---- destroy a linked list
LinkedList::~LinkedList()
{
    while (firstentry)
        firstentry->RemoveEntry();
}
// ---- construct a linked list entry
ListEntry::ListEntry(LinkedList *lh)
{
    listhead = lh;
    nextentry = 0;
    preventry = 0;
}
// ---- append an entry to the linked list
void ListEntry::AppendEntry(LinkedList *lh)
{
    if (lh)
        listhead = lh;
    if (listhead != 0)    {
        preventry = listhead->lastentry;
        if (listhead->lastentry)
            listhead->lastentry->nextentry = this;
        if (listhead->firstentry == 0)
            listhead->firstentry = this;
        listhead->lastentry = this;
    }
}
// ---- remove an entry from the linked list
void ListEntry::RemoveEntry()
{
       // ---- repair any break made by this removal
       if (nextentry)
           nextentry->preventry = preventry;
       if (preventry)
           preventry->nextentry = nextentry;
    if (listhead)    {
        // --- maintain listhead if this is last and/or first
        if (this == listhead->lastentry)
            listhead->lastentry = preventry;
        if (this == listhead->firstentry)
            listhead->firstentry = nextentry;
    }
    preventry = 0;
    nextentry = 0;
}
```

#### LISTING_SIX

```c
// -------- testinh.cpp
#include <iostream.h>
#include "inhlist.h"

class Date : public ListEntry {
    int mo, da, yr;
public:
    Date(int m=0, int d=0, int y=0)
        { mo = m; da = d; yr = y; }
    friend ostream& operator << (ostream& os, Date& dt)
    { os << dt.da << æ/' << dt.mo << æ/' << dt.yr; return os; }
};
void main()
{
    LinkedList dtlist;
    int d = 0, m, y;
    Date *dt;
    while (d != 99)    {
        cout << "Enter dd mm yy (99 .. .. when done): ";
        cout << flush;
        cin >> d >> m >> y;
        if (d != 99)    {
            dt = new Date(m,d,y);
            dt->AppendEntry(&dtlist);
        }
    }
    dt = (Date *) dtlist.FirstEntry();
    while (dt != 0)    {
        cout << æ\n' << *dt;
        dt = (Date *) dt->NextEntry();
    }
}
```

#### LISTING_SEVEN

```c
// ------------ linklist.h
// a template for a linked list

#ifndef LINKLIST_H
#define LINKLIST_H
template <class T>
// --- the linked list entry
class ListEntry    {
    T thisentry;
    ListEntry<T> *nextentry;
    ListEntry<T> *preventry;
    ListEntry(T& entry);
    friend class LinkedList<T>;
};
template <class T>
// ---- construct a linked list entry
ListEntry<T>::ListEntry(T &entry)
{
    thisentry = entry;
    nextentry = 0;
    preventry = 0;
}
template <class T>
// ---- the linked list
class LinkedList    {
    // --- the listhead
    ListEntry<T> *firstentry;
    ListEntry<T> *lastentry;
    ListEntry<T> *iterator;
    void RemoveEntry(ListEntry<T> *lentry);
    void InsertEntry(T& entry, ListEntry<T> *lentry);
public:
    LinkedList();
    ~LinkedList();
    void AppendEntry(T& entry);
    void RemoveEntry(int pos = -1);
    void InsertEntry(T&entry, int pos = -1);
    T *FindEntry(int pos);
    T *CurrentEntry();
    T *FirstEntry();
    T *LastEntry();
    T *NextEntry();
    T *PrevEntry();
};
template <class T>
// ---- construct a linked list
LinkedList<T>::LinkedList()
{
    iterator = 0;
    firstentry = 0;
    lastentry = 0;
}
template <class T>
// ---- destroy a linked list
LinkedList<T>::~LinkedList()
{
    while (firstentry)
        RemoveEntry(firstentry);
}
template <class T>
// ---- append an entry to the linked list
void LinkedList<T>::AppendEntry(T& entry)
{
    ListEntry<T> *newentry = new ListEntry<T>(entry);
    newentry->preventry = lastentry;
    if (lastentry)
        lastentry->nextentry = newentry;
    if (firstentry == 0)
        firstentry = newentry;
    lastentry = newentry;
}
template <class T>
// ---- remove an entry from the linked list
void LinkedList<T>::RemoveEntry(ListEntry<T> *lentry)
{
    if (lentry == 0)
        return;
    if (lentry == iterator)
        iterator = lentry->preventry;
    // ---- repair any break made by this removal
    if (lentry->nextentry)
        lentry->nextentry->preventry = lentry->preventry;
    if (lentry->preventry)
        lentry->preventry->nextentry = lentry->nextentry;
    // --- maintain listhead if this is last and/or first
    if (lentry == lastentry)
        lastentry = lentry->preventry;
    if (lentry == firstentry)
        firstentry = lentry->nextentry;
    delete lentry;
}
template <class T>
// ---- insert an entry into the linked list
void LinkedList<T>::InsertEntry(T& entry, ListEntry<T> *lentry)
{
    ListEntry<T> *newentry = new ListEntry<T>(entry);
    newentry->nextentry = lentry;
    if (lentry)    {
        newentry->preventry = lentry->preventry;
        lentry->preventry = newentry;
    }
    if (newentry->preventry)
        newentry->preventry->nextentry = newentry;
    if (lentry == firstentry)
        firstentry = newentry;
}
template <class T>
// ---- remove an entry from the linked list
void LinkedList<T>::RemoveEntry(int pos)
{
    FindEntry(pos);
    RemoveEntry(iterator);
}
template <class T>
// ---- insert an entry into the linked list
void LinkedList<T>::InsertEntry(T& entry, int pos)
{
    FindEntry(pos);
    InsertEntry(entry, iterator);
}
template <class T>
// ---- return the current linked list entry
T *LinkedList<T>::CurrentEntry()
{
    return iterator ? &(iterator->thisentry) : 0;
}
template <class T>
// ---- return a specific linked list entry
T *LinkedList<T>::FindEntry(int pos)
{
    if (pos != -1)    {
        iterator = firstentry;
        if (iterator)    {
            while (pos--)
                iterator = iterator->nextentry;
        }
    }
    return CurrentEntry();
}
template <class T>
// ---- return the first entry in the linked list
T *LinkedList<T>::FirstEntry()
{
    iterator = firstentry;
    return CurrentEntry();
}
template <class T>
// ---- return the last entry in the linked list
T *LinkedList<T>::LastEntry()
{
    iterator = lastentry;
    return CurrentEntry();
}
template <class T>
// ---- return the next entry in the linked list
T *LinkedList<T>::NextEntry()
{
    if (iterator == 0)
        iterator = firstentry;
    else
        iterator = iterator->nextentry;
    return CurrentEntry();
}
template <class T>
// ---- return the previous entry in the linked list
T *LinkedList<T>::PrevEntry()
{
    if (iterator == 0)
        iterator = lastentry;
    else
        iterator = iterator->preventry;
    return CurrentEntry();
}
#endif
```

#### LISTING_EIGHT

```c
// -------- testtmpl.cpp
#include <iostream.h>
#include "linklist.h"

class Date {
    int mo, da, yr;
public:
    Date(int m=0, int d=0, int y=0)
        { mo = m; da = d; yr = y; }
    friend ostream& operator << (ostream& os, Date& dt)
    { os << dt.da << æ/' << dt.mo << æ/' << dt.yr; return os; }
};
void main()
{
    LinkedList<Date> dtlist;
    int d = 0, m, y;
    while (d != 99)    {
        cout << "Enter dd mm yy (99 .. .. when done): ";
        cout << flush;
        cin >> d >> m >> y;
        if (d != 99)
            dtlist.AppendEntry(Date(m,d,y));
    }
    Date *dt = dtlist.FirstEntry();
    while (dt != 0)    {
        cout << æ\n' << *dt;
        dt = dtlist.NextEntry();
    }
}
```

Copyright © 1993, Dr. Dobb's Journal
