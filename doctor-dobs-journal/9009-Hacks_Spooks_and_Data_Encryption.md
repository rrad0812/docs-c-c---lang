
# C PROGRAMMING 9009

## Hakovi, sablasti i šifrovanje podataka - Al Stivens

Leksikon stalno mutira. U prošlim danima "hakeri" su bile počašćene duše koje su kopale po unutrašnjosti kompjuterskih sistema pronalazeći pametne i lukave načine da urade stvari u granicama zvaničnih sankcija. Dobar algoritam se zvao „dobar hak“. Generacija kompjuterskih osvajača i mediji su takođe ukrali tu reč tako da je sada haker neko ko krade kompjutersko vreme i informacije. Ova kolumna govori o tome kako nekadašnji hakeri mogu da zaštite svoje korisnike od nove generacije pank hakera. Razgovaraćemo o osnovama šifrovanja podataka i primeniti dva algoritma za šifrovanje u C.

Upravo sam završio čitanje Kukavičje jaje od Kliforda Stola (1989, Doubledai). Ne radi se o C (iako se C pominje i Unik je glavni igrač), ali svi programeri će uživati i biti povezani sa istinitom pričom o misterioznom umetniku koji je provalio u računar i programeru koji je nemilosrdno njušio lokaciju i identitet lukavog umešača. To je ubedljiva knjiga. Isplanirajte dovoljno vremena da ga pročitate u jednom dahu jer će vam trebati. Stol je programer i piše o tome kako se naizgled svakodnevni zadatak pronalaženja neslaganja od 75 centi u kompjuterskom računovodstvenom sistemu njegovog poslodavca pretvorio u jednogodišnje putovanje kroz rupe u Unik-u i VMS-u, nemarnom zanemarivanju i neznanju koje većina sistemskih menadžera ima o bezbednosti sistema, i bezumnom, nepomičnom birou saveznog zakonodavstva.

Iz ove knjige ćete naučiti o kompjuterskim piratima i o tome kako oni rutinski stiču neovlašćeni pristup računarskim sistemima pronalazeći otključana zadnja vrata i nepočepene rupe. Takođe ćete naučiti kako su kompjuteri vlade, univerziteta i istraživačkih centara povezani u složene mreže pomoću Timneta i drugih komunikacionih usluga. I, ako nikada niste radili u svojoj vladi, dobićete od Stollovog novicijatskog uvida u zbunjujuću i zbunjujuću prirodu Potomak groznice i letargije. Razvoj likova je slab, a neki od dijaloga su očigledno napravljeni da objasne stvari, ali knjiga je puna neizvesnosti i nećete želeti da je odložite.

### Šifrovanje podataka

Čitanje Kukavičjeg jajeta dovodi nas do teme šifrovanja podataka. Šifrovanje podataka znači da uzmete neki entitet podataka koji ima značenje u svom originalnom obliku i uništite ga do neprepoznatljivosti tako da ga neovlašćene osobe koje vire ne mogu ni razumeti ni koristiti. Kasnije, vi ili neko ko ima ovlašćeni pristup informacijama možete da ih dešifrujete i koristite. Ideja je, naravno, da se to skrebuje na način poznat samo onima iz unutrašnjeg svetišta. Špijunske stvari.

Šifrovanje pomoću računara uključuje u najmanju ruku algoritam, a često i mnogo više. Složenost vaše šeme šifrovanja zavisiće od mere zaštite koja vam je potrebna ili vašeg nivoa paranoje, šta god je veće. Za dobro razvijen tretman različitih tehnika šifrovanja, pogledajte „Plašt i podaci“ Rika Grehana u Bite Magazine, jun 1990.

Druga strana enkripcije je dešifrovanje. Obično, ali ne uvek, morate nešto da dešifrujete da biste to koristili. Kada ne bi? Primer je lozinka za prijavu. Kada izaberete lozinku za pristup sistemu, računar će je često šifrovati pre nego što je snimi, zaboravljajući original. Algoritam šifrovanja je dizajniran tako da niko ne može da izvede originalnu lozinku iz njenog šifrovanog oblika. Ako neko dođe do datoteke lozinke, ne može da koristi njen sadržaj da se prijavi na tuđi nalog. Kada se prijavite, sistem šifruje lozinku koju unesete i upoređuje je sa onim što je sačuvano. Ovaj pristup sprečava sistem da napravi trajni zapis lozinki.

Stoll je uporedio takve šeme šifrovanja lozinke sa mlincima za kobasice. Ne možete mleveno meso proći unazad kroz mlin i dobiti kobasicu. I zato se pitao zašto njegov ersatz korisnik nastavlja da preuzima beskorisne datoteke lozinki. Kasnije je saznao da je algoritam za Unik šifrovanje opšte poznat, a njegov anonimni provalnik je koristio algoritam da šifruje sve reči u engleskom pravopisnom rečniku. Preuzete šifrovane lozinke bi uporedio sa onima iz svog rečnika i tako naučio lozinke onih korisnika koji su nevino birali engleske reči.

Postoje neke vrlo praktične upotrebe za šifrovanje podataka u svetu zajedničkih računara. Svaki složeni sistem mora imati superkorisnika, sistem menadžera, nalog nadzora ili bilo šta drugo, tako da programeri sistema mogu da održavaju operativni sistem. Neki drugi korisnici – korisnici aplikacija – odgovorni su za informacije koje su toliko osetljive da ih drugi ne smeju videti, čak ni nadzornik sistema. Evidencija o platnim spiskovima, predlozi troškova, neprijateljske ponude za preuzimanje, zdravstveni kartoni zaposlenih i slično, sve su to podeljene informacije koje treba da vide samo oni koji imaju potrebu da znaju.

Mnogi uspešni neovlašćeni prodori u sistem uključuju sposobnost počinioca da stekne status superkorisnika kroz saznanje o grešci u sistemu. Sa tim statusom, sve datoteke i programi su širom otvoreni za sablast. Šifrovanje dodaje još jedan nivo zaštite od upitnih umova.

Elektronska pošta je popularna nova stavka među lokalnim i širokim mrežama. Uz sav taj tekst koji luta po svim tim sistemima sa toliko različitih stepena zaštite i privilegija, uvek postoji potencijal da pogrešne oči vide taj poverljivi memorandum, ljubavno pismo ili fudbalski bazen. Nevolje obično slede.

Na primer, Novell mreža pruža meru zaštite dozvoljavajući nadzorniku sistema da dodeli privilegije čitanja i pisanja korisnicima na nivou mrežnog poddirektorija. Ako je sistem pravilno konfigurisan, mogu da pišem u vaš poddirektorijum pošte, ali ne mogu da ga pročitam. Mogu da pročitam poddirektorijum u kome je softver uskladišten, ali ne mogu da ga zapišem. Mogu čitati i pisati u svoj poddirektorijum pošte. Kada pošta napusti moju lokalnu mrežu i ode nekome negde drugde, moram da zavisim od integriteta te udaljene lokacije da bih na sličan način zaštitio svoje dragocene podatke od znatiželjnih očiju. Hajde da napravimo apsurdnu pretpostavku da su svi sistemi sigurni i da su svi sistemski supervizori od poverenja. Da li postoje stražnja vrata u ovoj šemi zaštite, koja bi omogućila prodor uljeza? Naravno da postoji.

Novell mrežne aplikacije razmenjuju poruke između mreža koristeći program agenta za isporuku koji se zove Message Handling Service (MHS). MHS je postao standard, i zbog njegovog prihvatanja, druge platforme kao što su MCI Mail i CompuServe imaju ili razvijaju procese prolaza koji razmenjuju poruke između sebe i MHS instalacija. Velike stvari. Pretpostavimo sada da smo ti i ja obični, neprivilegovani korisnici na mreži koja koristi MHS i ja želim da presretnem vašu dolaznu poštu. Naša mreža ima ime čvorišta i lozinku koje MHS administrator dodeljuje. Čvorišta se međusobno zovu i koriste te elemente podataka da identifikuju i verifikuju jedno drugo. Sve što treba da uradim da dobijem vašu poštu je da postavim drugi sistem, dodelim mu ime i lozinku našeg čvorišta i povežem ga sa momcima koji vam šalju stvari. Kako da saznam lozinku čvorišta? Simple. MHS to beleži u javnom direktorijumu koji svako može da pročita. Dumb. Ali tipično. I dovoljan razlog da razmislite o dodavanju šifrovanja našim sistemima za razmenu poruka.

### Šifrovanje sa jednim i dva ključa

Obično šifrujete datoteku tako što ćete navesti vrednost ključa algoritmu za šifrovanje. Predviđeni primalac datoteke mora znati vrednost ključa da bi dešifrovao i koristio podatke. Ovo često predstavlja dilemu. Ako želite da zapamtite samo jedan ključ – um, ne smete da ga zapisujete ili čuvate u datoteci – ali imate mnogo ljudi kojima možete da pošaljete poštu, onda svako od njih mora da zna vaš ključ. Što ih je više, to je veći potencijal za kompromis i manja je zaštita. Ako razmenjujete poštu sa mnogo ljudi koji ne znaju vaš ključ, onda morate da zapamtite sve njihove ključeve. Iako vas ispravnost sprečava da napravite bilo kakvu vrstu trajnog zapisa, niko ne može da zapamti toliko ključeva, a stvari se negde zapisuju.

Bolja šema za šifrovanje u sistemima elektronske pošte uključuje dva ključa, javni i privatni. Vaš javni ključ je svima poznat i samo vi znate privatni. S druge strane, znate javne ključeve drugih korisnika. Javni ključ je funkcija privatnog ključa, i poput lozinke o kojoj smo gore govorili, obično se ne može obrnuti inženjering privatnog ključa od javnog. Svi vam šalju stvari koje su šifrovane javnim ključem. Ali samo privatni ključ može da dešifruje poruke. Morate da zapamtite samo jedan ključ za sebe, a javne ključeve svojih dopisnika možete da snimite gde god želite, čak i u svoj adresar elektronske pošte. Grehanov članak u Bite-u objašnjava nekoliko algoritama za razvoj sistema za šifrovanje sa dva ključa.

U razmeni jedan na jedan, čini se da sistem sa jednim ključem bolje funkcioniše. Ti i tvoj drugar znate ključ, a niko drugi ne zna. Slobodno razmenjujete šifrovane poruke. Kada je potrebno, vas dvoje se slažete da promenite ključ.

### CRIPTO

[LISTING_ONE](#listing_one) je "cripto.c", implementacija malog algoritma za šifrovanje. To je sve šifrovanje koje bi većini od nas ikada trebalo. Osim ako niste u stvarno neprijateljskom okruženju u kojem se čuvaju veoma osetljivi zapisi, šifrovanje obično služi samo da bi radoznali bili iskreni. U Stolovoj knjizi, iako je uljez godinu dana pokušavao da dođe do nečega poverljivog, nikada nije uspeo. Slabo obezbeđenje koje je stalno pronalazilo omogućavalo mu je da krade samo iz neklasifikovanih sistema.

CRIPTO čita ključnu reč i dva imena datoteka iz komandne linije. Ako pokrenete program protiv šifrovane datoteke, CRIPTO je dešifruje. Algoritam je jednostavan. Program čita ulaznu datoteku u 64-bitnim blokovima, vrši ekskluzivno OR bloka i ključne reči i upisuje blok u izlaznu datoteku. Da biste dešifrovali šifrovanu datoteku, pokrećete je na istom CRIPTO programu koristeći istu lozinku.

Neupućenima, izgleda da šifrovana datoteka sadrži nasumične vrednosti bajtova. Čak i ako njuškar ima program CRIPTO, bez ključne reči datoteka je beskorisna. Ovaj algoritam je mnogo bolji od jednostavne šeme šifrovanja zamene karaktera jer je modifikacija svakog karaktera funkcija njegove pozicije u 8-bajtnom bloku i odgovarajućeg karaktera u ključnoj reči. Ova tehnika nije imuna na tehnike razbijanja koda, ali će vaš razbijač koda biti zauzet neko vreme. Šifrovanje ponekad služi svojoj svrsi ako samo odlaže kada informacije postanu javne. Ako vaša poruka kaže: „Napadamo u podne“, koga briga ako razbijači šifre to shvate do 13:00?

### Standard šifrovanja podataka

Vlada je odobrila standardni algoritam za šifrovanje sa jednim ključem pod nazivom Standard za šifrovanje podataka (DES). Možete dobiti kopiju standarda iz većine državnih biblioteka tako što ćete zatražiti publikaciju federalnih standarda za obradu informacija (FIPS PUB) 46. Algoritam je složen, a FIPS PUB 46 nije najjasnija dokumentacija koju ćete ikada pročitati. Pokušaću da to bolje objasnim i istovremeno demonstriram sa C programima koji implementiraju algoritam.

### Šifrovanje sa DES-om

DES šifruje podatke 64 bita istovremeno. On uništava 64 bita pomoću algoritma u nekoliko koraka koji uključuje 64-bitnu vrednost ključa koju daje korisnik. Standard specificira ključ od osam 8-bitnih bajtova i ne koristi najznačajniji bit svakog bajta u ključu, rezervišući ga kao paritetni bit ako je potrebno.

DES radi sa nizovima bitova i odnosi se na bit 1, bit 2 itd. Morate pažljivo da pročitate standard do kraja da biste utvrdili da je bit 1 najznačajniji bit 8-bitnog bajta ili 64-bitnog bloka i da se brojevi bita čitaju s leva na desno. Oni su nespecifični u vezi sa celobrojnom i dugom celobrojnom konfiguracijom arhitekture, tako da morate biti pažljivi u pogledu prikaza podataka od poslednjeg bajta u većini 16- i 32-bitnih mašina.

Algoritam šifrovanja radi na datoteci podataka u 64-bitnim blokovima. Počinje preuređivanjem bitova u bloku prema početnoj tabeli permutacije. Zatim razdvaja blok na dve 32-bitne polovine, levu i desnu polovinu. Postoji 16 iteracija procesa manglinga dizajniranih da mutiraju podatke do neprepoznatljivosti pri čemu ključ igra glavnu ulogu. Rezultat ovih iteracija je konačni par 32-bitnih polovina. Algoritam permutira ovaj 64-bitni blok korišćenjem tabele permutacije koja je inverzna od početne tabele permutacije. Izlaz iz ove permutacije je šifrovani 64-bitni blok. Zanimljivo svojstvo ove inverzne permutacije je da ako vaša ulazna datoteka ne koristi najznačajniji osmi bit, neće ni šifrovana datoteka. To znači da ako prenosite ASCII datoteke tekstualnih podataka preko serijskih linija sa 7-bitnim komunikacionim protokolima bajtova podataka, vaše šifrovane datoteke će se prenositi ispravno.

To izgleda dovoljno jednostavno. Sada stvari počinju da se komplikuju. Proces obrade podataka od 16 iteracija ide ovako:

Za svaku iteraciju, leva polovina bloka je isključivo OR sa 32-bitnim izlazom funkcije koja se zove f. Zatim, osim šesnaeste iteracije, polovice se razmenjuju. To nije tako loše. Hajde sada da razmotrimo funkciju f, koja je prikladno nazvana.

Funkcija f uzima 32-bitnu desnu polovinu bloka i 48-bitni izlaz funkcije KS (ključni raspored) kao svoje argumente. Desna polovina se zove "R". Funkcija permutira 32 bita R u 48 bita. Ova permutacija se zove "E" i isključivo je OR sa 48-bitnim izlazom iz KS funkcije. 48-bitni rezultat se zatim segmentira na osam 6-bitnih vrednosti. Funkcija S kompresuje svaku od njih 4-bitnu vrednost. Osam 4-bitnih vrednosti se kombinuju u jednu 32-bitnu vrednost, koja se zatim permutira kroz tabelu permutacije koja se zove „P“. Ta permutovana vrednost je izlaz funkcije f. Vhev.

S funkcija se sastoji od osam različitih podfunkcija (S1, S2, S3 ...) u zavisnosti od toga da li kompresuje prvi, drugi, treći itd. 6-bitni blok. Postoji osam tabela, po jedna za svaku podfunkciju. Tabele su nizovi od dve dimenzije sa 4 kolone i 16 redova. Svaki unos u nizu sadrži vrednost između 0 i 15 koja predstavlja 4-bitni komprimovani prikaz 6-bitnog ulaza. 6-bitna vrednost koja treba da se komprimuje obezbeđuje indekse niza na ovaj bizaran način: Bitovi 1 i 6 se kombinuju da budu indeksi reda 0 - 3. Bitovi 2 - 5 su indeksni indeks kolone 0 - 15. Funkcija S vraća 4-bitnu vrednost uzetu iz odgovarajućeg niza kao rezultat ovih indeksa. Još nismo gotovi.

Funkcija KS vraća 48-bitnu vrednost na osnovu 64-bitnog ključa. Njegovi argumenti su broj iteracije od 16 iteracija o kojima smo ranije govorili i vrednost ključa za šifrovanje. Za prvu iteraciju, funkcija permutira vrednost ključa koristeći tabelu Permuted Choice 1. Ključ se deli na dve polovine i svaka od njih se pomera za jedan ili dva bita ulevo u zavisnosti od toga koja se iteracija izvodi. Tabela kontroliše vrednosti pomeranja. Svaka iteracija posle prve koristi pomerenu vrednost prethodne iteracije kao svoj ulaz, vrši sopstvenu smenu, a zatim još jednom permutira vrednost koristeći tabelu Permuted Choice 2.

### Dešifrovanje sa DES-om

Dešifrovanje obrće proceduru šifrovanja. Pošto su početna i inverzno-inicijalna permutacija recipročne, koraci dešifrovanja su isti osim što dešifrovanje preokreće polurazmenu tokom iteracija i koristi permutirane vrednosti ključa koje vraća funkcija KS u redosledu obrnutom od onog koji koristi šifrovanje.

### ŠIFIRAJ i DEKRIPT

[LISTING_TWO](#listing_two) je "des.h", datoteka zaglavlja koja definiše globalne formate, prototipove funkcija i imena eksternih podataka. Datoteka takođe definiše makroe predprocesora koje program koristi za pravljenje tabela permutacije. Kasnije pogledajte diskusiju o permutacijama za objašnjenje ovih makroa.

[LISTING_THREE](#listing_three) je "encript.c", program koji šifrira datoteku sa podacima. Pokrećete ga tako što ćete uneti ključnu reč koja treba da ima osam znakova, naziv ulazne datoteke i naziv šifrovane izlazne datoteke. Program čita datoteku 64 bita istovremeno i šifruje ta 64 bita pomoću DES algoritma. On permutira ulaz sa početnom tabelom permutacije i vrši 16 iteracija razmene desne i leve polovine i modifikacija bloka zavisnih od ključa. Zatim koristi inverznu početnu tabelu permutacije da permutira izlaz.

Imajte na umu da program izračunava rasporede sa 16 tastera u niz na početku programa. Ove vrednosti su konstantne za ceo proces i ne treba ih ponovo izračunavati za svaku iteraciju koja ih koristi.

Izlazna datoteka će uvek biti paran višekratnik od 8 bajtova. Ovo je neophodno zato što se proces šifrovanja skromuje u 64-bitne delove.

[LISTING_FOUR](#listing_four) je "decript.c", koji je sličan encript.c osim što obrće redosled razmene desno-levo i preuzimanja iz niza rasporeda ključeva. Dešifrovana datoteka je dužina šifrovane datoteke, paran umnožak od 8 bajtova sa viškom bajtova ispunjenim nulama. Ovo popunjavanje može biti problem, posebno sa datotekama fiksnog formata gde aplikacija dodaje zapise. Ako je to slučaj, možete modifikovati program za šifrovanje da zapiše kontrolnu vrednost kao deo šifrovane datoteke. Vrednost bi specificirala dužinu originalne datoteke sa podacima. Možete da izmenite program za dešifrovanje da koristi ovu vrednost za skraćenje viška bajtova u dešifrovanoj datoteci. Možete odlučiti da zaobiđete šifrovanje poslednjeg bloka ako je manji od 8 bajtova. Ipak, čuvajte se. Ako se korisnikova poruka sekretarici njegovog šefa završi sa "Ljubav, Ed", možda će biti potrebno objašnjenje.

[LISTING_FIVE](#listing_five) je "des.c", koji sadrži DES funkcije. Većinu sam objasnio u diskusiji o algoritmima. Kratka imena funkcija f, S i KS potiču iz DES specifikacije. Diskusija o permutacijama koja sledi objašnjava funkcije permute i inverse_permute. Funkcija šest bitova možda treba neko objašnjenje. Njegova svrha je da izdvoji i vrati 6-bitnu vrednost iz 48-bitnog bloka. Ništa od ovoga nije lako podržano tipičnim 8- i 16-bitnim arhitekturama koje većina nas koristi, tako da funkcija šest bitova koristi metodu grube sile. Tabela pod nazivom "ek6" sadrži unos za svaku od osam 6-bitnih vrednosti. Svaki unos identifikuje 2 bajta koji sadrže širenje od 6 bitova. Jedan element određuje broj bitova za pomeranje bajta ulevo, a drugi određuje broj za pomeranje udesno. Ta dva elementa se međusobno isključuju. Drugi element specificira masku za AND uklanjanje viška bitova.

[LISTING_SIX](#listing_six), strana 149, je tabele.c. Stavio sam tabele u poseban izvorni fajl jer se kompajliraju tako sporo. Sledeća diskusija objašnjava zašto.

### DES permutacije

Spomenuo sam razne permutacije koje DES izvodi. Permutacija bloka podataka se sastoji od preuređivanja bitova u specificirani redosled. DES specifikacija pruža tabele koje identifikuju koji će biti permutirani redosled bitova za svaku permutaciju. Postoji nekoliko načina na koje možete da primenite takve permutacije u C. Napravio sam niz bitnih maski za svaki od 64 bita u permutaciji. Svaki unos niza sadrži 64-bitnu vrednost sa jednim uključenim bitom. Njegova pozicija u tabeli povezuje ga sa bitom koji će se promeniti. Dakle, ako treći unos tabele ima postavljen peti bit, onda će permutovani izlaz imati postavljen peti bit samo ako ulaz ima postavljen treći bit. Za testiranje 64 ulaznih bitova, napravio sam tabelu opšte namene sa bitovima postavljenim u rastućem redosledu - prvi bit je postavljen u prvom unosu, drugi u drugom, itd. Nešto od ovoga bi se moglo efikasnije uraditi u asemblerskom jeziku sa smenama i testovima za nošenje. Međutim, jezik C koji je implementiran na računaru nema pogodan 64-bitni operator pomeranja. Shodno tome, izabrao sam pristup maski za testiranje bitova i pristup maski skupa bitova opisan ovde.

Da bih olakšao pravljenje bitnih maski i smanjio potencijal za greške u transkripciji, razvio sam makroe za pretprocesor u DES.H. Kodiranjem p(n) makroa sa brojem između 1 i 64 kao argumentom, vi kažete pretprocesoru da definiše skup od osam integralnih vrednosti razdvojenih zarezima sa sedam od njih vrednosti 0, a jedna od njih ima vrednost sa jednim bitom (1, 2, 4, 8, 0k10, 0k20, ili 0k4). Vrednost 1 postavlja krajnji levi bit krajnjeg levog bajta. Sledeće rastuće vrednosti postavljaju sledeći sekvencijalni bit udesno. P(n) izrazi se zatim mogu kodirati u inicijalizatore za niz znakova.

Koristeći ovu tehniku, uspeo sam da podignem vrednosti tabele permutacije direktno iz DES standardnog dokumenta i kodiram ih u p(n) izraze. Posmatrajte makroe. Kada kodirate p(n) izraz, on se proširuje na osam poziva za b(n,r) makro, po jedan za svaki bajt u 64-bitnoj maski. Argument r identifikuje koji od 8 bajtova b(n,r) makro proširuje. Makro koristi tu vrednost da testira opseg argumenta n da vidi da li njegov odgovarajući bit spada u bajt. Ako nije, izraz se proširuje na nultu vrednost za bajt. Ako je tako, makro poziva ps(n) makro prosleđujući argument u opsegu 1 - 8 koji je izračunat iz n i r argumenata u b(n,r) makro. Makro ps(n) proširuje bajt na vrednost sa jednim bitom pomerenim u ispravnu poziciju.

Ova vežba ilustruje snagu C preprocesora. Kodiranjem ovih makroa ja sam u stanju da izrazim tabele permutacije čitljivijim jezikom i manje margine za greške. Ali postoji trošak. Postoji nekoliko takvih tabela u programu, a sva ta aktivnost predprocesora usporava 20-MHz 386 do indeksiranja kada se datoteka tables.c kompajlira. Prvi put kada se to dogodilo, pomislio sam da je Turbo C spustio slušalicu.

### DES Performans

DES algoritam je složen i spor. Ovde objavljenom programu ENCRIPT potrebno je jedan minut, deset sekundi da šifruje tekst ove kolone, oko 25K bajtova, a program DECRIPT koristi isto vreme da ga dešifruje. Ove mere sam napravio na 20-MHz 386 sa Turbo C 2.0 za kompajler. Poređenja radi, jednostavnijem CRIPTO programu potrebno je oko 3,5 sekunde da izvrši isti zadatak. Loš prikaz DES programa mogao bi da odražava lošu implementaciju (ko, ja?) ili neoptimalno kompajliranje. Nisam ga isprobao na računaru na 4,77 MHz. Spakujte ručak i ponesite četkicu za zube.

Algoritmi u DES.C pretpostavljaju 32-bitni dug ceo broj i 8-bitni bajt. Koristio sam dugi ceo broj računara da primenim polovine 64-bitnog ključa i bloka podataka jer je to bilo zgodno i nudilo je najbolju šansu za efikasnost. Prenosiviji program bi se prilagodio ispravnom integralnom tipu podataka za kompajler i možda neće raditi tako dobro. Možda bi se funkcije permutacije mogle optimizovati. Profiler će pokazati da te funkcije zauzimaju većinu vremena algoritma. Optimizacija bi, međutim, zavisila od kompajlera. Možete pogledati 64 iteracije funkcije permute i odlučiti da koristite indekse umesto pokazivača, ili da koristite 64 diskretna in-line testa bitova i OR-a da biste izbegli prekomernu petlju. Kodirajte ga na ovaj ili onaj način, a performanse programa bi se mogle poboljšati ili pogoršati u zavisnosti od C kompajlera koji koristite.

Pošto je DES definisan od strane Nacionalnog biroa za standarde, istinski odgovarajući uređaj ili program moraju biti validirani, a ovi programi nisu testirani i blagosloveni. Moj primerak standarda ne identifikuje procedure validacije. Možda bi dobar test bio da se vidi da li implementacija kandidata može uspešno da razmenjuje šifrovane datoteke sa odobrenim uređajem. Specifikacija implicira, međutim, da implementator ima izvesnu slobodu u dizajniranju različitih permutacionih tabela. Ako je to tako, onda šifrovane datoteke ne bi bile kompatibilne između različitih implementacija.

#### LISTING_ONE

```c
/* ---------------------- crypto.c ----------------------------- */

/*
 * Simple, single key file encryption/decryption filter
 * Usage: crypto keyvalue infile outfile
 */

#include <stdio.h>
#include <string.h>

void main(int argc, char *argv[])
{
    FILE *fi, *fo;
    int ct;
    char ip[8];

    char *cp1, *cp2;

    if (argc > 3)    {
        if ((fi = fopen(argv[2], "rb")) != NULL)    {
            if ((fo = fopen(argv[3], "wb")) != NULL)    {
                while ((ct = fread(ip, 1, 8, fi)) != 0)    {
                    cp1 = argv[1];
                    cp2 = ip;
                    while (*cp1 && cp1 < argv[1]+8)
                        *cp2++ ^= *cp1++;
                    fwrite(ip, 1, ct, fo);
                }
                fclose(fo);
            }
            fclose(fi);
        }
    }
}
```

#### LISTING_TWO

```c
/* -------------- des.h ---------------- */

/*
 * Header file for Data Encryption Standard algorithms
 */

/* ------- two halves of a 64-bit data block ------- */
struct LR    {
    long L;
    long R;
};

/* -------- 48-bit key permutation ------- */
struct ks    {
    char ki[6];
};

/* --------- macros to define a permutation table ---------- */
#define ps(n)       ((unsigned char)(0x80 >> (n-1)))
#define b(n,r)      ((n>r||n<r-7)?0:ps(n-(r-8)))
#define p(n)        b(n, 8),b(n,16),b(n,24),b(n,32),\
                    b(n,40),b(n,48),b(n,56),b(n,64)

/* -------------- prototypes ------------------- */
void inverse_permute(long *op, long *ip, long *tbl, int n);
void permute(long *op, long *ip, long *tbl, int n);
long f(long blk, struct ks ky);
struct ks KS(int n, char *key);

/* ----------- tables ------------ */
extern unsigned char Pmask[];
extern unsigned char IPtbl[];
extern unsigned char Etbl[];
extern unsigned char Ptbl[];
extern unsigned char stbl[8][4][16];
extern unsigned char PC1tbl[];
extern unsigned char PC2tbl[];
extern unsigned char ex6[8][2][4];
```

#### LISTING_THREE

```c
/* ------------- encrypt.c ------------- */

/*
 * Data Encryption Standard encryption filter
 * Usage: encrypt keyvalue infile outfile
 */

#include <stdio.h>
#include "des.h"

void main(int argc, char *argv[])
{
    int i;
    struct LR op, ip;
    FILE *fi, *fo;
    struct ks keys[16];

    for (i = 0; i < 16; i++)
        keys[i] = KS(i, argv[1]);
    if (argc > 3)    {
        if ((fi = fopen(argv[2], "rb")) != NULL)    {
            if ((fo = fopen(argv[3], "wb")) != NULL)    {
                while (fread(&ip, 1,
                        sizeof(struct LR), fi) != 0)    {
                    int n;
                    /* -------- initial permuation -------- */
                    permute(&op.L, &ip.L, (long *)IPtbl, 64);
                    /* ------ swap and key iterations ----- */
                    for (n = 0; n < 16; n++)    {
                        ip.L = op.R;
                        ip.R = op.L ^ f(op.R, keys[n]);

                        op.R = ip.R;
                        op.L = ip.L;
                    }
                    /* ----- inverse initial permuation ---- */
                    inverse_permute(&op.L, &ip.L,
                        (long *)IPtbl, 64);
                    fwrite(&op, 1, sizeof(struct LR), fo);
                    /* ------- to pad the last block ------- */
                    ip.L = ip.R = 0;
                }
                fclose(fo);
            }
            fclose(fi);
        }
    }
}
```

#### LISTING_FOUR

```c
/* ------------- decrypt.c ------------- */

/*
 * Data Encryption Standard encryption filter
 * Usage: decrypt keyvalue infile outfile
 */

#include <stdio.h>
#include "des.h"

void main(int argc, char *argv[])
{
    int i;
    struct LR op, ip;
    FILE *fi, *fo;
    struct ks keys[16];

    for (i = 0; i < 16; i++)
        keys[i] = KS(i, argv[1]);
    if (argc > 3)    {
        if ((fi = fopen(argv[2], "rb")) != NULL)    {
            if ((fo = fopen(argv[3], "wb")) != NULL)    {
                while (fread(&ip, 1,
                        sizeof(struct LR), fi) != 0)    {
                    int n;
                    /* -------- initial permuation -------- */
                    permute(&op.L, &ip.L, (long *)IPtbl, 64);
                    /* ------ swap and key iterations ----- */
                    for (n = 15; n >= 0; --n)    {
                        ip.R = op.L;
                        ip.L = op.R ^ f(op.L, keys[n]);

                        op.R = ip.R;
                        op.L = ip.L;
                    }
                    /* ----- inverse initial permuation ---- */
                    inverse_permute(&op.L, &ip.L,
                        (long *)IPtbl, 64);
                    fwrite(&op, 1, sizeof(struct LR), fo);
                    /* ------- to pad the last block ------- */
                    ip.L = ip.R = 0;
                }
                fclose(fo);
            }
            fclose(fi);
        }
    }
}
```

#### LISTING_FIVE

```c
/* ---------------------- des.c --------------------------- */

/*
 * Functions and tables for DES encryption and decryption
 */

#include <stdio.h>
#include "des.h"

static void rotate(unsigned char *c, int n);
static long S(struct ks ip);
static int fourbits(struct ks, int s);
static int sixbits(struct ks, int s);

/* ------- inverse permute a 64-bit string ------- */
void inverse_permute(long *op, long *ip, long *tbl, int n)
{
    int i;
    long *pt = (long *)Pmask;

   *op = *(op+1) = 0;
    for (i = 0; i < n; i++)    {
        if ((*ip & *pt) || (*(ip+1) & *(pt+1)))  {
            *op |= *tbl;
            *(op+1) |= *(tbl+1);
       }
       tbl += 2;
        pt += 2;
   }
}

/* ------- permute a 64-bit string ------- */
void permute(long *op, long *ip, long *tbl, int n)
{
    int i;
      long *pt = (long *)Pmask;

    *op = *(op+1) = 0;
    for (i = 0; i < n; i++)    {
        if ((*ip & *tbl) || (*(ip+1) & *(tbl+1))) {
            *op |= *pt;
            *(op+1) |= *(pt+1);
        }
        tbl += 2;
         pt += 2;
    }
}

/* ----- Key dependent computation function f(R,K) ----- */
long f(long blk, struct ks key)
{
    struct LR ir = {0,0};
    struct LR or;

    union    {
        struct LR f;
        struct ks kn;
    } tr = {0,0}, kr = {0,0};
    ir.L = blk;
    kr.kn = key;
    permute(&tr.f.L, &ir.L, (long *)Etbl, 48);

    tr.f.L ^= kr.f.L;
    tr.f.R ^= kr.f.R;

    ir.L = S(tr.kn);

    permute(&or.L, &ir.L, (long *)Ptbl, 32);
    return or.L;
}

/* ---- convert 48-bit block/key permutation to 32 bits ---- */
static long S(struct ks ip)
{
    int i;
    long op = 0;
    for (i = 8; i > 0; --i)    {
        long four = fourbits(ip, i);
        op |= four << ((i-1) * 4);
    }
    return op;
}

/* ------- extract a 4-bit stream from the block/key ------- */
static int fourbits(struct ks k, int s)
{
    int i = sixbits(k, s);
    int row, col;
    row = ((i >> 4) & 2) | (i & 1);
    col = (i >> 1) & 0xf;
    return stbl[8-s][row][col];
}

/* ---- extract 6-bit stream fr pos s of the  block/key ---- */
static int sixbits(struct ks k, int s)
{
    int op = 0;
    int n = (8-s);
    int i;
    for (i = 0; i < 2; i++)    {
        int off = ex6[n][i][0];
        unsigned char c = k.ki[off];
        c >>= ex6[n][i][1];
        c <<= ex6[n][i][2];
        c &=  ex6[n][i][3];
        op |= c;
    }
    return op;
}

/* ---------- DES Key Schedule (KS) function ----------- */
struct ks KS(int n, char *key)
{
    static unsigned char cd[8];
    static int its[] = {1,1,2,2,2,2,2,2,1,2,2,2,2,2,2,1};
    union    {
        struct ks kn;
        struct LR filler;
    } result;

    if (n == 0)
        permute((long *)cd, (long *) key, (long *)PC1tbl, 64);

    rotate(cd, its[n]);
    rotate(cd+4, its[n]);

    permute(&result.filler.L, (long *)cd, (long *)PC2tbl, 48);
    return result.kn;
}

/* ----- rotate a 4-byte string n positions to the left ---- */
static void rotate(unsigned char *c, int n)
{
    int i;
    unsigned j, k;
    k = *c >> (8 - n);
    for (i = 3; i >= 0; --i)    {
        j = (*(c+i) << n) + k;
        k = j >> 8;
        *(c+i) = j;
    }
}
```

#### LISTING_SIX

```c
/* --------------- tables.c --------------- */

/*
 * tables for the DES algorithm
 */

#include "des.h"

/* --------- permutation masks ----------- */
unsigned char Pmask[] = {
    p( 1),p( 2),p( 3),p( 4),p( 5),p( 6),p( 7),p( 8),
    p( 9),p(10),p(11),p(12),p(13),p(14),p(15),p(16),
    p(17),p(18),p(19),p(20),p(21),p(22),p(23),p(24),
    p(25),p(26),p(27),p(28),p(29),p(30),p(31),p(32),
    p(33),p(34),p(35),p(36),p(37),p(38),p(39),p(40),
    p(41),p(42),p(43),p(44),p(45),p(46),p(47),p(48),
    p(49),p(50),p(51),p(52),p(53),p(54),p(55),p(56),
    p(57),p(58),p(59),p(60),p(61),p(62),p(63),p(64)
};

/* ----- initial and inverse-initial permutation table ----- */
unsigned char IPtbl[] = {
    p(58),p(50),p(42),p(34),p(26),p(18),p(10),p( 2),
    p(60),p(52),p(44),p(36),p(28),p(20),p(12),p( 4),
    p(62),p(54),p(46),p(38),p(30),p(22),p(14),p( 6),
    p(64),p(56),p(48),p(40),p(32),p(24),p(16),p( 8),
    p(57),p(49),p(41),p(33),p(25),p(17),p( 9),p( 1),
    p(59),p(51),p(43),p(35),p(27),p(19),p(11),p( 3),
    p(61),p(53),p(45),p(37),p(29),p(21),p(13),p( 5),
    p(63),p(55),p(47),p(39),p(31),p(23),p(15),p( 7)
};

/* ---------- permutation table E for f function --------- */
unsigned char Etbl[] = {
    p(32),p( 1),p( 2),p( 3),p( 4),p( 5),
    p( 4),p( 5),p( 6),p( 7),p( 8),p( 9),
    p( 8),p( 9),p(10),p(11),p(12),p(13),
    p(12),p(13),p(14),p(15),p(16),p(17),
    p(16),p(17),p(18),p(19),p(20),p(21),
    p(20),p(21),p(22),p(23),p(24),p(25),
    p(24),p(25),p(26),p(27),p(28),p(29),
    p(28),p(29),p(30),p(31),p(32),p( 1)
};

/* ---------- permutation table P for f function --------- */
unsigned char Ptbl[] = {
    p(16),p( 7),p(20),p(21),p(29),p(12),p(28),p(17),
    p( 1),p(15),p(23),p(26),p( 5),p(18),p(31),p(10),
    p( 2),p( 8),p(24),p(14),p(32),p(27),p( 3),p( 9),
    p(19),p(13),p(30),p( 6),p(22),p(11),p( 4),p(25)
};

/* --- table for converting six-bit to four-bit stream --- */
unsigned char stbl[8][4][16] = {
    /* ------------- s1 --------------- */
    14,4,13,1,2,15,11,8,3,10,6,12,5,9,0,7,
    0,15,7,4,14,2,13,1,10,6,12,11,9,5,3,8,
    4,1,14,8,13,6,2,11,15,12,9,7,3,10,5,0,
    15,12,8,2,4,9,1,7,5,11,3,14,10,0,6,13,
    /* ------------- s2 --------------- */
    15,1,8,14,6,11,3,4,9,7,2,13,12,0,5,10,
    3,13,4,7,15,2,8,14,12,0,1,10,6,9,11,5,
    0,14,7,11,10,4,13,1,5,8,12,6,9,3,2,15,
    13,8,10,1,3,15,4,2,11,6,7,12,0,5,14,9,
    /* ------------- s3 --------------- */
    10,0,9,14,6,3,15,5,1,13,12,7,11,4,2,8,
    13,7,0,9,3,4,6,10,2,8,5,14,12,11,15,1,
    13,6,4,9,8,15,3,0,11,1,2,12,5,10,14,7,
    1,10,13,0,6,9,8,7,4,15,14,3,11,5,2,12,
    /* ------------- s4 --------------- */
    7,13,14,3,0,6,9,10,1,2,8,5,11,12,4,15,
    13,8,11,5,6,15,0,3,4,7,2,12,1,10,14,9,
    10,6,9,0,12,11,7,13,15,1,3,14,5,2,8,4,
    3,15,0,6,10,1,13,8,9,4,5,11,12,7,2,14,
    /* ------------- s5 --------------- */
    2,12,4,1,7,10,11,6,8,5,3,15,13,0,14,9,
    14,11,2,12,4,7,13,1,5,0,15,10,3,9,8,6,
    4,2,1,11,10,13,7,8,15,9,12,5,6,3,0,14,
    11,8,12,7,1,14,2,13,6,15,0,9,10,4,5,3,
    /* ------------- s6 --------------- */
    12,1,10,15,9,2,6,8,0,13,3,4,14,7,5,11,
    10,15,4,2,7,12,9,5,6,1,13,14,0,11,3,8,
    9,14,15,5,2,8,12,3,7,0,4,10,1,13,11,6,
    4,3,2,12,9,5,15,10,11,14,1,7,6,0,8,13,
    /* ------------- s7 --------------- */
    4,11,2,14,15,0,8,13,3,12,9,7,5,10,6,1,
    13,0,11,7,4,9,1,10,14,3,5,12,2,15,8,6,
    1,4,11,13,12,3,7,14,10,15,6,8,0,5,9,2,
    6,11,13,8,1,4,10,7,9,5,0,15,14,2,3,12,
    /* ------------- s8 --------------- */
    13,2,8,4,6,15,11,1,10,9,3,14,5,0,12,7,
    1,15,13,8,10,3,7,4,12,5,6,11,0,14,9,2,
    7,11,4,1,9,12,14,2,0,6,10,13,15,3,5,8,
    2,1,14,7,4,10,8,13,15,12,9,0,3,5,6,11
};

/* ---- Permuted Choice 1 for Key Schedule calculation ---- */
unsigned char PC1tbl[] = {
    p(57),p(49),p(41),p(33),p(25),p(17),p( 9),p( 0),
    p( 1),p(58),p(50),p(42),p(34),p(26),p(18),p( 0),
    p(10),p( 2),p(59),p(51),p(43),p(35),p(27),p( 0),
    p(19),p(11),p( 3),p(60),p(52),p(44),p(36),p( 0),
    p(63),p(55),p(47),p(39),p(31),p(23),p(15),p( 0),
    p( 7),p(62),p(54),p(46),p(38),p(30),p(22),p( 0),
    p(14),p( 6),p(61),p(53),p(45),p(37),p(29),p( 0),
    p(21),p(13),p( 5),p(28),p(20),p(12),p( 4),p( 0)
};

/* ---- Permuted Choice 2 for Key Schedule calculation ---- */
unsigned char PC2tbl[] = {
    p(14),p(17),p(11),p(24),p( 1),p( 5),p( 3),p(28),
    p(15),p( 6),p(21),p(10),p(23),p(19),p(12),p( 4),
    p(26),p( 8),p(16),p( 7),p(27),p(20),p(13),p( 2),
    p(41),p(52),p(31),p(37),p(47),p(55),p(30),p(40),
    p(51),p(45),p(33),p(48),p(44),p(49),p(39),p(56),
    p(34),p(53),p(46),p(42),p(50),p(36),p(29),p(32)
};

/* ---- For extracting 6-bit strings from 64-bit string ---- */
unsigned char ex6[8][2][4]    = {
    /* byte, >>, <<, & */
    /* ---- s = 8  ---- */
    0,2,0,0x3f,
    0,2,0,0x3f,
    /* ---- s = 7  ---- */
    0,0,4,0x30,
    1,4,0,0x0f,
    /* ---- s = 6  ---- */
    1,0,2,0x3c,
    2,6,0,0x03,
    /* ---- s = 5  ---- */
    2,0,0,0x3f,
    2,0,0,0x3f,
    /* ---- s = 4 ---- */
    3,2,0,0x3f,
    3,2,0,0x3f,
    /* ---- s = 3 ---- */
    3,0,4,0x30,
    4,4,0,0x0f,
    /* ---- s = 2 ---- */
    4,0,2,0x3c,
    5,6,0,0x03,
    /* ---- s = 1 ---- */
    5,0,0,0x3f,
    5,0,0,0x3f
};
```
