# Primena neuronskih mreža za predikciju kretanja cena akcija na osnovu sentimenta finansijskih vesti

## 1. Opis problema

Finansijsko tržište predstavlja složen i dinamičan sistem u kome na kretanje cena akcija utiču brojni faktori, uključujući ekonomske pokazatelje, tržišne trendove i informacije objavljene u finansijskim vestima. Sentiment sadržan u vestima može uticati na ponašanje investitora, a samim tim i na promene cena akcija.

Cilj ovog projekta je ispitivanje mogućnosti primene neuronskih mreža za predikciju kratkoročnog kretanja cena akcija na osnovu sentimenta finansijskih vesti i istorijskih tržišnih podataka. Na osnovu dostupnih informacija model predviđa da li će cena akcije u narednom periodu ostvariti rast (UP) ili pad (DOWN). Za implementaciju i treniranje modela korišćena je PyTorch biblioteka, dok su eksperimenti sprovedeni nad podacima kompanija iz grupe Magnificent 7.

## 2. Podaci

### Izvor podataka

U projektu su korišćeni podaci o finansijskim vestima i istorijskim cenama akcija kompanija iz grupe Magnificent 7:

- Apple (AAPL)
- Amazon (AMZN)
- Alphabet (GOOGL)
- Microsoft (MSFT)
- Meta (META)
- Nvidia (NVDA)
- Tesla (TSLA)

Finansijske vesti preuzete su iz Kaggle skupa podataka *Apple Stock (AAPL): Historical Financial News Data (2016–2024)*, dok su istorijski podaci o cenama akcija preuzeti korišćenjem biblioteke yfinance.

Zbog ograničenja veličine GitHub repozitorijuma, obrađeni skupovi podataka nalaze se na Google Drive-u:

https://drive.google.com/drive/folders/1qTZZoD7rzb419hRqnB5aUqOzpeW41jSh?usp=drive_link

Folder sadrži CSV datoteke za sve kompanije iz grupe Magnificent 7 korišćene u ovom projektu.

### Struktura podataka

Skup podataka sadrži:

- datum objavljivanja vesti
- naslov vesti
- sadržaj vesti
- sentiment oznaku
- sentiment score
- podatke o cenama akcija
- prinose akcija

### Analiza i preprocesiranje

Za svaku kompaniju izdvojene su relevantne finansijske vesti. Naslov i sadržaj vesti spojeni su u jedinstveno tekstualno polje koje je korišćeno za određivanje sentimenta.

Nakon sentiment analize, vesti su povezane sa istorijskim podacima o cenama akcija korišćenjem vremenskog usklađivanja sa narednim trgovačkim danom. Kreirani su dodatni atributi poput prinosa, pokretnih proseka sentimenta i prethodnih vrednosti sentimenta.

### Ulazni atributi modela

Za treniranje neuronske mreže korišćeni su sentiment atributi izvedeni iz finansijskih vesti i tržišni atributi izvedeni iz istorijskih podataka o akcijama.

#### Sentiment atributi

- sentiment_score – numerička vrednost sentimenta vesti
- sentiment_num – numerički prikaz sentiment kategorije
- sent_lag1 – sentiment prethodnog dana
- sent_ma3 – pokretni prosek sentimenta za poslednja tri dana
- news_count – broj objavljenih vesti tokom dana

#### Tržišni atributi

- return_lag1 – prinos akcije prethodnog dana
- return_lag2 – prinos akcije dva dana unazad
- return_lag3 – prinos akcije tri dana unazad

#### Atribut kompanije

- company – oznaka kompanije iz grupe Magnificent 7, predstavljena korišćenjem One-Hot Encoding metode

Kombinovanjem sentiment karakteristika i istorijskih tržišnih podataka formiran je konačni skup ulaznih atributa za treniranje modela.

## 3. Arhitektura modela

Model je implementiran kao višeslojna neuronska mreža (Multilayer Perceptron – MLP) korišćenjem PyTorch biblioteke.

Arhitektura mreže sastoji se od:

- ulaznog sloja dimenzije jednake broju ulaznih atributa
- prvog linearnog sloja sa 32 neurona i ReLU aktivacionom funkcijom
- drugog linearnog sloja sa 16 neurona i ReLU aktivacionom funkcijom
- izlaznog sloja sa jednim neuronom
- Sigmoid aktivacione funkcije na izlazu

Sigmoid funkcija vraća verovatnoću pripadnosti klasi, na osnovu koje model predviđa da li će cena akcije ostvariti rast (UP) ili pad (DOWN).

Arhitektura modela:

Input → Linear(32) → ReLU → Linear(16) → ReLU → Linear(1) → Sigmoid

## 4. Trening

Podaci su podeljeni na trening i test skup u odnosu 80% : 20%.

Pre treniranja izvršena je standardizacija ulaznih atributa korišćenjem StandardScaler metode.

Za treniranje modela korišćeni su:

- Adam optimizer
- Binary Cross Entropy Loss funkcija
- PyTorch DataLoader
- 50 epoha treniranja

Tokom treniranja praćena je vrednost funkcije greške radi procene uspešnosti procesa učenja.

## 5. Analiza osetljivosti i hiperparametarska optimizacija

U okviru projekta analiziran je uticaj različitih ulaznih atributa na performanse modela. Posebna pažnja posvećena je poređenju modela koji koristi kombinaciju sentiment karakteristika i istorijskih prinosa akcija sa modelom koji koristi samo sentiment karakteristike finansijskih vesti, broj vesti i informaciju o kompaniji.

Rezultati su pokazali da model bez istorijskih tržišnih podataka ostvaruje tačnost od **50,32%**, dok model koji uključuje i istorijske prinose postiže znatno bolje rezultate. Ovakvo poređenje ukazuje da istorijski tržišni podaci imaju značajan doprinos uspešnosti predikcije.

Pored analize ulaznih atributa, izvršena je i osnovna hiperparametarska analiza ispitivanjem uticaja broja epoha na performanse modela za predikciju kretanja akcija jedan dan unapred.

| Broj epoha | Accuracy |
|------------|----------|
| 20 | 82,97% |
| 50 | 84,09% |

Dobijeni rezultati pokazuju da povećanje broja epoha doprinosi boljem učenju modela i blagom unapređenju performansi. Na osnovu toga kao konačna konfiguracija odabran je model treniran tokom 50 epoha.

## 6. Rezultati evaluacije

Performanse modela evaluirane su na test skupu korišćenjem metrika Accuracy, Precision, Recall i F1-score.

Najbolji rezultat ostvaren je kod modela za predikciju jedan dan unapred, dok se sa povećanjem vremenskog horizonta predikcije tačnost postepeno smanjuje.

- Predikcija kretanja akcija jedan dan unapred (1D): **84,09%**
- Predikcija kretanja akcija dva dana unapred (2D): **65,73%**
- Predikcija kretanja akcija tri dana unapred (3D): **59,92%**
- Model bez istorijskih tržišnih podataka: **50,32%**

## 7. Diskusija

Rezultati istraživanja pokazuju da kombinacija sentiment karakteristika finansijskih vesti i istorijskih prinosa akcija može biti korisna za predikciju kratkoročnog kretanja cena akcija.

Najbolje performanse ostvarene su kod modela za predikciju jedan dan unapred, sa tačnošću od **84,09%**, dok se tačnost smanjuje na **65,73%** za dva dana i **59,92%** za tri dana unapred.

Pad performansi sa povećanjem vremenskog horizonta predikcije je očekivan, jer na buduće kretanje akcija utiče veliki broj faktora koji nisu obuhvaćeni modelom. Dobijeni rezultati ukazuju da su korišćeni atributi najkorisniji za predikciju neposrednih tržišnih reakcija.

Posebno značajan rezultat predstavlja poređenje modela koji koristi sentiment karakteristike i istorijske prinose sa modelom bez istorijskih tržišnih podataka. Model bez istorijskih tržišnih podataka ostvario je tačnost od **50,32%**, dok je uključivanjem istorijskih prinosa tačnost povećana na **84,09%** za predikciju jedan dan unapred.

Na osnovu dobijenih rezultata može se zaključiti da istorijski tržišni podaci imaju značajnu ulogu u procesu predikcije i da njihova kombinacija sa sentiment informacijama daje znatno bolje rezultate od korišćenja samo jednog izvora informacija.

## 8. Zaključak

U ovom projektu razvijen je model zasnovan na neuronskim mrežama za predikciju kratkoročnog kretanja akcija korišćenjem sentiment karakteristika finansijskih vesti i istorijskih tržišnih podataka kompanija iz grupe Magnificent 7.

Rezultati su pokazali da model ostvaruje najbolje performanse pri predikciji jedan dan unapred, sa tačnošću od **84,09%**, dok se tačnost smanjuje sa povećanjem vremenskog horizonta predikcije. Takođe, poređenje sa modelom bez istorijskih tržišnih podataka pokazalo je da istorijski prinosi i tržišni pokazatelji imaju značajnu ulogu u unapređenju performansi modela.

Na osnovu dobijenih rezultata može se zaključiti da kombinovanje informacija iz finansijskih vesti i istorijskih tržišnih podataka predstavlja koristan pristup za predikciju kratkoročnog kretanja akcija.

Ograničenje istraživanja predstavlja činjenica da model koristi ograničen skup tržišnih i sentiment karakteristika, dok na kretanje akcija utiču i brojni drugi faktori koji nisu obuhvaćeni analizom.

Buduća istraživanja mogu uključiti dodatne finansijske indikatore, veći broj tržišnih faktora i naprednije arhitekture neuronskih mreža radi daljeg unapređenja performansi modela.
