# **Programowanie Obiektowe – Java (2026Z)**

## **Lab 2: Klasy, obiekty, konstruktory i referencje**

---

## 1. Cel zajęć

Celem laboratorium jest praktyczne poznanie podstawowych elementów programowania obiektowego w języku Java:

- klas,
- obiektów,
- pól,
- metod,
- konstruktorów,
- słowa kluczowego `this`,
- referencji do obiektów,
- podstaw cyklu życia obiektu i odśmiecania pamięci.

Po zakończeniu zajęć osoba studencka powinna:

- rozumieć różnicę pomiędzy klasą a obiektem,
- potrafić zdefiniować prostą klasę,
- potrafić tworzyć obiekty za pomocą `new`,
- rozumieć różnicę pomiędzy polem obiektu a zmienną lokalną,
- poprawnie stosować `this`,
- potrafić tworzyć i przeciążać konstruktory,
- rozumieć działanie konstruktora domyślnego,
- potrafić przekazywać referencje do obiektów,
- rozumieć, że dwie zmienne mogą wskazywać na ten sam obiekt,
- rozumieć znaczenie wartości `null`,
- znać podstawową ideę działania Garbage Collectora.

> **Uwaga:** hermetyzacja, modyfikatory dostępu, gettery i settery, składowe `static` oraz przeciążanie zwykłych metod będą głównym tematem kolejnego laboratorium.

---

# 2. Klasa

Klasa jest definicją nowego typu obiektowego.

Określa przede wszystkim:

- **stan** obiektu – przechowywany w polach,
- **zachowanie** obiektu – realizowane przez metody.

Przykład:

```java
public class Samochod {

    String marka;
    String model;
    int rokProdukcji;
    double przebieg;

    public void uruchom() {
        System.out.println(marka + " " + model + " został uruchomiony.");
    }

    public void wyswietlInformacje() {
        System.out.println("Marka: " + marka);
        System.out.println("Model: " + model);
        System.out.println("Rok produkcji: " + rokProdukcji);
        System.out.println("Przebieg: " + przebieg + " km");
    }
}
```

Sama definicja klasy nie oznacza jeszcze utworzenia konkretnego samochodu.

---

# 3. Obiekt

Obiekt jest konkretną instancją klasy.

Jeżeli:

```java
Samochod auto = new Samochod();
```

to:

- `Samochod` – typ zmiennej,
- `auto` – zmienna przechowująca referencję,
- `new Samochod()` – utworzenie nowego obiektu.

Możemy utworzyć wiele obiektów tej samej klasy:

```java
Samochod auto1 = new Samochod();
Samochod auto2 = new Samochod();
```

`auto1` i `auto2` mogą reprezentować dwa niezależne samochody.

---

# 4. Pola obiektu

Pola przechowują stan konkretnego obiektu.

```java
public class Punkt {

    double x;
    double y;
}
```

Tworzenie dwóch obiektów:

```java
Punkt p1 = new Punkt();
Punkt p2 = new Punkt();

p1.x = 2.0;
p1.y = 3.0;

p2.x = 10.0;
p2.y = -4.0;
```

Każdy obiekt posiada własny zestaw pól instancji.

Zmiana:

```java
p1.x = 100.0;
```

nie zmienia pola `x` obiektu `p2`.

---

# 5. Wartości domyślne pól

Jeżeli pole obiektu nie otrzyma jawnie wartości, Java ustawi dla niego wartość domyślną.

Przykładowo:

| Typ pola | Wartość domyślna |
|---|---|
| `byte`, `short`, `int`, `long` | `0` |
| `float`, `double` | `0.0` |
| `boolean` | `false` |
| `char` | `'\u0000'` |
| typ referencyjny | `null` |

Przykład:

```java
public class Produkt {

    String nazwa;
    int liczbaSztuk;
    double cena;
    boolean dostepny;
}
```

Po:

```java
Produkt produkt = new Produkt();
```

pola będą początkowo miały wartości:

```text
nazwa       -> null
liczbaSztuk -> 0
cena        -> 0.0
dostepny    -> false
```

Nie należy mylić wartości domyślnych **pól** ze zmiennymi lokalnymi. Lokalna zmienna musi zostać zainicjalizowana przed użyciem.

---

# 6. Metody instancji

Metody opisują zachowanie obiektów.

```java
public class Konto {

    String wlasciciel;
    double saldo;

    public void wplac(double kwota) {
        saldo += kwota;
    }

    public void wyplac(double kwota) {
        if (kwota <= saldo) {
            saldo -= kwota;
        }
    }

    public void pokazSaldo() {
        System.out.println(
                "Saldo konta " + wlasciciel + ": " + saldo + " zł"
        );
    }
}
```

Wywołanie:

```java
Konto konto = new Konto();

konto.wlasciciel = "Anna";
konto.saldo = 1000;

konto.wplac(500);
konto.wyplac(200);
konto.pokazSaldo();
```

Metoda instancji jest wywoływana **na konkretnym obiekcie**:

```java
konto.wplac(500);
```

---

# 7. `this`

Słowo kluczowe `this` oznacza referencję do **bieżącego obiektu**.

Najczęściej spotkamy je w sytuacji, gdy parametr metody lub konstruktora ma taką samą nazwę jak pole.

```java
public class Osoba {

    String imie;

    public void ustawImie(String imie) {
        this.imie = imie;
    }
}
```

W instrukcji:

```java
this.imie = imie;
```

- `this.imie` oznacza pole bieżącego obiektu,
- `imie` oznacza parametr metody.

---

## 7.1. Kiedy `this` nie jest wymagane?

Jeżeli nazwy się nie pokrywają:

```java
public void ustawImie(String noweImie) {
    imie = noweImie;
}
```

kod jest poprawny.

Można również napisać:

```java
public void ustawImie(String noweImie) {
    this.imie = noweImie;
}
```

W tym przypadku `this` jest opcjonalne.

---

## 7.2. Błąd związany z brakiem `this`

Rozważmy:

```java
public class Osoba {

    String imie;

    public void ustawImie(String imie) {
        imie = imie;
    }
}
```

Instrukcja:

```java
imie = imie;
```

przypisuje parametr do niego samego.

Pole obiektu nie zostaje zmienione.

Poprawnie:

```java
this.imie = imie;
```

---

# 8. Konstruktor

Konstruktor służy do inicjalizacji nowo tworzonego obiektu.

```java
public class Samochod {

    String marka;
    String model;
    int rokProdukcji;

    public Samochod(
            String marka,
            String model,
            int rokProdukcji
    ) {
        this.marka = marka;
        this.model = model;
        this.rokProdukcji = rokProdukcji;
    }
}
```

Utworzenie:

```java
Samochod auto =
        new Samochod("Toyota", "Corolla", 2022);
```

Konstruktor:

- ma taką samą nazwę jak klasa,
- nie posiada typu zwracanego,
- może przyjmować parametry,
- jest wykonywany podczas tworzenia obiektu przez `new`.

---

# 9. Konstruktor domyślny

Jeżeli w klasie **nie zdefiniujemy żadnego konstruktora**, kompilator zapewnia konstruktor bezargumentowy.

```java
public class Punkt {

    double x;
    double y;
}
```

Możemy wtedy napisać:

```java
Punkt punkt = new Punkt();
```

---

## 9.1. Ważna zasada

Jeżeli napiszemy własny konstruktor:

```java
public class Punkt {

    double x;
    double y;

    public Punkt(double x, double y) {
        this.x = x;
        this.y = y;
    }
}
```

kompilator **nie dodaje już automatycznie konstruktora bezargumentowego**.

Dlatego:

```java
// Punkt p = new Punkt();
```

nie skompiluje się, jeżeli sami nie zdefiniujemy:

```java
public Punkt() {
}
```

---

# 10. Przeciążanie konstruktorów

Klasa może posiadać wiele konstruktorów różniących się listą parametrów.

```java
public class Samochod {

    String marka;
    String model;
    int rokProdukcji;
    double przebieg;

    public Samochod() {
        marka = "Nieznana";
        model = "Nieznany";
        rokProdukcji = 0;
        przebieg = 0.0;
    }

    public Samochod(String marka, String model) {
        this.marka = marka;
        this.model = model;
        rokProdukcji = 0;
        przebieg = 0.0;
    }

    public Samochod(
            String marka,
            String model,
            int rokProdukcji,
            double przebieg
    ) {
        this.marka = marka;
        this.model = model;
        this.rokProdukcji = rokProdukcji;
        this.przebieg = przebieg;
    }
}
```

Java dobiera konstruktor na podstawie przekazanych argumentów.

```java
Samochod a = new Samochod();

Samochod b =
        new Samochod("Toyota", "Corolla");

Samochod c =
        new Samochod("Audi", "A4", 2020, 42_000);
```

---

# 11. Wywoływanie innego konstruktora – `this(...)`

Jeżeli kilka konstruktorów powtarza ten sam kod, jeden konstruktor może wywołać drugi.

```java
public class Samochod {

    String marka;
    String model;
    int rokProdukcji;
    double przebieg;

    public Samochod() {
        this("Nieznana", "Nieznany", 0, 0.0);
    }

    public Samochod(String marka, String model) {
        this(marka, model, 0, 0.0);
    }

    public Samochod(
            String marka,
            String model,
            int rokProdukcji,
            double przebieg
    ) {
        this.marka = marka;
        this.model = model;
        this.rokProdukcji = rokProdukcji;
        this.przebieg = przebieg;
    }
}
```

Wywołanie:

```java
this(...)
```

musi być **pierwszą instrukcją konstruktora**.

Dzięki temu ograniczamy duplikowanie kodu.

---

# 12. Konstruktor kopiujący

Java nie tworzy automatycznie specjalnego „konstruktora kopiującego”, ale możemy napisać własny konstruktor przyjmujący inny obiekt tej samej klasy.

```java
public class Punkt {

    double x;
    double y;

    public Punkt(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public Punkt(Punkt inny) {
        this.x = inny.x;
        this.y = inny.y;
    }
}
```

Użycie:

```java
Punkt p1 = new Punkt(2, 3);
Punkt p2 = new Punkt(p1);
```

`p1` i `p2` są dwoma różnymi obiektami posiadającymi początkowo takie same wartości pól.

---

# 13. Referencje

W przypadku typów obiektowych zmienna przechowuje **referencję do obiektu**, a nie sam obiekt.

```java
Samochod auto =
        new Samochod("Toyota", "Corolla", 2022, 10_000);
```

Możemy myśleć o tym uproszczonym schematem:

```text
auto
  │
  └──────────────► obiekt Samochod
                   marka = "Toyota"
                   model = "Corolla"
                   rok = 2022
```

---

# 14. Dwie referencje do tego samego obiektu

Rozważmy:

```java
Punkt p1 = new Punkt(1, 2);
Punkt p2 = p1;
```

Nie powstał drugi obiekt.

Powstała druga referencja do **tego samego obiektu**.

```text
p1 ─────┐
        ├────► jeden obiekt Punkt
p2 ─────┘
```

Dlatego:

```java
p2.x = 100;

System.out.println(p1.x);
```

wyświetli:

```text
100.0
```

Zmiana została wykonana na obiekcie, do którego wskazują obie zmienne.

---

# 15. Referencja a kopia obiektu

Porównaj:

```java
Punkt p1 = new Punkt(1, 2);
Punkt p2 = p1;
```

z:

```java
Punkt p1 = new Punkt(1, 2);
Punkt p2 = new Punkt(p1);
```

W pierwszym przypadku:

```text
p1 ──┐
     ├──► jeden obiekt
p2 ──┘
```

W drugim:

```text
p1 ─────► obiekt nr 1

p2 ─────► obiekt nr 2
```

To bardzo ważna różnica.

---

# 16. Przekazywanie obiektu do metody

Do metody możemy przekazać referencję do obiektu.

```java
public class Punkt {

    double x;
    double y;

    public Punkt(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public void przesunInnyPunkt(Punkt punkt) {
        punkt.x += 10;
    }
}
```

Użycie:

```java
Punkt a = new Punkt(1, 2);
Punkt b = new Punkt(5, 6);

a.przesunInnyPunkt(b);

System.out.println(b.x); // 15.0
```

Metoda otrzymała kopię referencji, ale nadal prowadzi ona do tego samego obiektu `b`.

---

# 17. Czy Java przekazuje obiekty przez referencję?

Java **zawsze przekazuje argumenty przez wartość** (*pass by value*).

W przypadku obiektów przekazywaną wartością jest **kopia referencji**.

Dlatego metoda może zmienić stan obiektu:

```java
static void zmien(Punkt punkt) {
    punkt.x = 100;
}
```

ale przypisanie parametrowi nowego obiektu:

```java
static void podmien(Punkt punkt) {
    punkt = new Punkt(500, 500);
}
```

nie podmieni referencji zapisanej w zmiennej po stronie wywołującej.

---

# 18. `null`

Zmienna referencyjna może nie wskazywać na żaden obiekt.

```java
Samochod auto = null;
```

Próba wykonania:

```java
auto.uruchom();
```

spowoduje:

```text
NullPointerException
```

Przed użyciem referencji czasami trzeba sprawdzić:

```java
if (auto != null) {
    auto.uruchom();
}
```

Nie należy jednak traktować sprawdzania `null` jako rozwiązania każdego problemu projektowego. Często lepiej tak projektować kod, aby niepoprawny stan nie powstawał.

---

# 19. Porównywanie referencji operatorem `==`

Dla typów obiektowych:

```java
a == b
```

sprawdza, czy obie referencje wskazują na ten sam obiekt.

Przykład:

```java
Punkt p1 = new Punkt(1, 2);
Punkt p2 = p1;
Punkt p3 = new Punkt(1, 2);

System.out.println(p1 == p2); // true
System.out.println(p1 == p3); // false
```

Mimo że `p1` i `p3` mogą mieć takie same wartości pól, są dwoma niezależnymi obiektami.

Porównywanie zawartości obiektów za pomocą `equals()` zostanie omówione szerzej później.

---

# 20. Cykl życia obiektu

W uproszczeniu:

```text
utworzenie obiektu
        ↓
używanie obiektu
        ↓
utrata dostępnych referencji
        ↓
obiekt może zostać usunięty przez Garbage Collector
```

Obiekt powstaje zwykle przez:

```java
new
```

Przykład:

```java
Punkt p = new Punkt(1, 2);
```

Jeżeli później:

```java
p = null;
```

i nie istnieje żadna inna osiągalna referencja do tego obiektu, może on stać się kandydatem do odśmiecenia.

---

# 21. Garbage Collector

Java automatycznie zarządza pamięcią zajmowaną przez obiekty.

Mechanizm **Garbage Collector (GC)** może odzyskiwać pamięć po obiektach, które nie są już osiągalne z działającego programu.

Przykład:

```java
Punkt p1 = new Punkt(1, 2);
Punkt p2 = p1;

p1 = null;
```

Obiekt nadal jest osiągalny przez:

```java
p2
```

więc nie może zostać uznany za nieosiągalny.

Dopiero np.:

```java
p2 = null;
```

może spowodować, że nie będzie już dostępnej referencji prowadzącej do tego obiektu.

---

## 21.1. `System.gc()`

Można spotkać:

```java
System.gc();
```

Jest to jedynie **prośba** do JVM o rozważenie uruchomienia procesu odśmiecania.

Programista nie ma gwarancji, że GC zostanie uruchomiony dokładnie w tym momencie.

---

## 21.2. Java nie posiada klasycznych destruktorów

W Javie nie stosuje się destruktorów działających tak jak np. w C++.

Nie należy opierać zwalniania zasobów takich jak:

- pliki,
- połączenia sieciowe,
- połączenia z bazą danych

na Garbage Collectorze.

Mechanizmy prawidłowego zamykania zasobów zostaną omówione przy wyjątkach i `try-with-resources`.

---

# 22. Przykład – `Samochod`

```java
public class Samochod {

    String marka;
    String model;
    int rokProdukcji;
    double przebieg;

    public Samochod() {
        this("Nieznana", "Nieznany", 0, 0.0);
    }

    public Samochod(
            String marka,
            String model,
            int rokProdukcji
    ) {
        this(marka, model, rokProdukcji, 0.0);
    }

    public Samochod(
            String marka,
            String model,
            int rokProdukcji,
            double przebieg
    ) {
        this.marka = marka;
        this.model = model;
        this.rokProdukcji = rokProdukcji;
        this.przebieg = przebieg;
    }

    public void uruchom() {
        System.out.println(
                marka + " " + model + " został uruchomiony."
        );
    }

    public void przejedz(double kilometry) {
        if (kilometry > 0) {
            przebieg += kilometry;
        }
    }

    public void wyswietlInformacje() {
        System.out.println("Marka: " + marka);
        System.out.println("Model: " + model);
        System.out.println("Rok: " + rokProdukcji);
        System.out.println("Przebieg: " + przebieg + " km");
    }
}
```

Użycie:

```java
public class MainSamochod {

    public static void main(String[] args) {
        Samochod auto1 =
                new Samochod(
                        "Toyota",
                        "Corolla",
                        2018,
                        85_000
                );

        Samochod auto2 =
                new Samochod(
                        "Audi",
                        "A4",
                        2020
                );

        auto1.uruchom();
        auto1.przejedz(125.5);
        auto1.wyswietlInformacje();

        System.out.println();

        auto2.wyswietlInformacje();
    }
}
```

---

# 23. Typowe błędy

## 23.1. Konstruktor z typem zwracanym

```java
public class Osoba {

    // To NIE jest konstruktor.
    public void Osoba() {
    }
}
```

Konstruktor nie posiada typu zwracanego.

Poprawnie:

```java
public Osoba() {
}
```

---

## 23.2. Oczekiwanie konstruktora bezargumentowego

```java
public class Osoba {

    String imie;

    public Osoba(String imie) {
        this.imie = imie;
    }
}
```

Nie zadziała:

```java
// Osoba osoba = new Osoba();
```

ponieważ po zadeklarowaniu własnego konstruktora kompilator nie dodaje automatycznie konstruktora bezargumentowego.

---

## 23.3. `this.x = x` a `x = x`

Niepoprawnie:

```java
public Punkt(double x, double y) {
    x = x;
    y = y;
}
```

Poprawnie:

```java
public Punkt(double x, double y) {
    this.x = x;
    this.y = y;
}
```

---

## 23.4. Oczekiwanie kopii obiektu

```java
Punkt a = new Punkt(1, 2);
Punkt b = a;

b.x = 100;

System.out.println(a.x);
```

Wynik:

```text
100.0
```

`b = a` nie kopiuje obiektu.

---

## 23.5. Wywołanie metody na `null`

```java
Samochod auto = null;

// auto.uruchom();
```

Próba wywołania metody spowoduje `NullPointerException`.

---

# **Zadania**

## Zadanie 1 – `Dog`

Utwórz klasę:

```text
Dog
```

z polami:

```text
name
breed
age
```

Dodaj konstruktor przyjmujący wartości wszystkich pól.

Dodaj metodę:

```java
bark()
```

która wyświetla:

```text
Wow Wow
```

oraz metodę:

```java
showInformation()
```

wyświetlającą informacje o psie.

Utwórz co najmniej dwa różne obiekty i wywołaj ich metody.

---

## Zadanie 2 – `Point`

Utwórz klasę:

```text
Point
```

z polami:

```text
x
y
```

Dodaj konstruktor oraz metodę:

```java
distance(Point otherPoint)
```

obliczającą odległość pomiędzy bieżącym punktem a innym punktem.

Wykorzystaj:

```java
Math.sqrt(...)
```

lub:

```java
Math.hypot(...)
```

Utwórz co najmniej dwa punkty i sprawdź działanie metody.

---

## Zadanie 3 – konstruktory klasy `Book`

Utwórz klasę:

```text
Book
```

z polami:

```text
title
author
publicationYear
```

Zaimplementuj:

1. konstruktor bezargumentowy,
2. konstruktor przyjmujący `title` i `author`,
3. konstruktor przyjmujący wszystkie trzy wartości.

Wykorzystaj:

```java
this(...)
```

tak, aby nie powielać niepotrzebnie kodu inicjalizującego.

Utwórz po jednym obiekcie za pomocą każdego konstruktora.

---

## Zadanie 4 – `Car`

Utwórz klasę:

```text
Car
```

z polami:

```text
brand
model
productionYear
mileage
```

Dodaj kilka przeciążonych konstruktorów.

Dodaj metody:

```java
drive(double distance)
showInformation()
```

Metoda `drive()` powinna zwiększać przebieg tylko dla dodatniej odległości.

---

## Zadanie 5 – konstruktor kopiujący

Do klasy `Car` z poprzedniego zadania dodaj konstruktor:

```java
Car(Car other)
```

Utwórz:

```java
Car original
Car copy
```

Następnie zmień pole jednego z obiektów i sprawdź, czy drugi obiekt również został zmieniony.

Wyjaśnij wynik.

---

## Zadanie 6 – referencje

Utwórz klasę:

```text
Person
```

z polem:

```text
name
```

oraz metodą:

```java
introduceYourself()
```

Następnie wykonaj:

```java
Person first = new Person("Anna");
Person second = first;

second.name = "Maria";
```

Wyświetl dane za pomocą obu referencji.

Odpowiedz:

1. Ile obiektów `Person` powstało?
2. Ile istnieje referencji?
3. Dlaczego zmiana wykonana przez `second` jest widoczna również przez `first`?

---

## Zadanie 7 – referencja czy kopia?

Rozbuduj poprzednią klasę o konstruktor kopiujący.

Porównaj:

```java
Person second = first;
```

z:

```java
Person second = new Person(first);
```

Po utworzeniu `second` zmień jego imię.

Wyjaśnij różnicę pomiędzy oboma przypadkami.

---

## Zadanie 8 – przekazywanie obiektu do metody

Utwórz klasę:

```text
Counter
```

z polem:

```text
value
```

Napisz metodę:

```java
increaseOther(Counter other)
```

która zwiększa `value` przekazanego obiektu o `1`.

Utwórz dwa obiekty i sprawdź działanie.

Następnie odpowiedz:

> Dlaczego metoda może zmienić stan obiektu utworzonego poza nią?

---

## Zadanie 9 – `null`

Utwórz zmienną:

```java
Car car = null;
```

Spróbuj przewidzieć, co wydarzy się po:

```java
car.showInformation();
```

Następnie sprawdź odpowiedź.

Dodaj warunek zabezpieczający wywołanie metody.

---

## Zadanie 10 – analiza kodu

Bez uruchamiania programu określ wynik:

```java
public class Box {

    int value;

    public Box(int value) {
        this.value = value;
    }
}
```

```java
public class Main {

    public static void main(String[] args) {
        Box a = new Box(10);
        Box b = a;
        Box c = new Box(a.value);

        b.value = 20;
        c.value = 30;

        System.out.println(a.value);
        System.out.println(b.value);
        System.out.println(c.value);

        System.out.println(a == b);
        System.out.println(a == c);
    }
}
```

Narysuj uproszczony schemat referencji i obiektów.

Dopiero później uruchom program i sprawdź odpowiedź.

---

## Zadanie 11 – napraw kod

Poniższy kod zawiera problemy:

```java
public class Product {

    String name;
    double price;

    public void Product(String name, double price) {
        name = name;
        price = price;
    }

    public void show() {
        System.out.println(name + ": " + price);
    }
}
```

Program:

```java
public class Main {

    public static void main(String[] args) {
        Product product =
                new Product("Monitor", 1200);

        product.show();
    }
}
```

1. Wyjaśnij, dlaczego program się nie kompiluje.
2. Popraw deklarację konstruktora.
3. Popraw przypisania pól.
4. Wyjaśnij rolę `this`.

---

## Zadanie 12 – model z poprzednich zajęć

Wróć do jednego z modeli przygotowanych podczas Lab 1:

- biblioteki,
- sklepu internetowego,
- kina,
- wypożyczalni samochodów,
- hotelu,
- uczelni,
- komunikatora.

Wybierz **co najmniej trzy klasy** i zaimplementuj je w Javie.

Każda klasa powinna posiadać:

- co najmniej 2–3 pola,
- konstruktor,
- co najmniej jedną metodę opisującą zachowanie obiektu.

Następnie utwórz kilka obiektów i pokaż ich współdziałanie.

Na tym etapie nie skupiaj się jeszcze na getterach, setterach ani pełnej hermetyzacji. Zostanie to omówione na kolejnych zajęciach.

---

# **Zadanie dodatkowe**

Utwórz klasę:

```text
Rectangle
```

reprezentującą prostokąt.

Klasa powinna posiadać:

```text
width
height
```

oraz konstruktory:

```text
Rectangle()
Rectangle(double side)
Rectangle(double width, double height)
Rectangle(Rectangle other)
```

Zastosuj `this(...)` tak, aby ograniczyć duplikowanie kodu.

Dodaj metody:

```java
area()
perimeter()
scale(double factor)
```

Następnie:

1. utwórz prostokąt,
2. utwórz drugą referencję do tego samego obiektu,
3. utwórz kopię za pomocą konstruktora kopiującego,
4. przeskaluj pierwszy obiekt,
5. porównaj wartości widoczne przez wszystkie referencje.

Wyjaśnij wynik.

---

# **Po zajęciach**

Po tym laboratorium należy rozumieć różnicę pomiędzy:

```text
klasą
obiektem
zmienną referencyjną
polem
zmienną lokalną
metodą
konstruktorem
```

oraz potrafić wyjaśnić różnicę pomiędzy:

```java
Person b = a;
```

a:

```java
Person b = new Person(a);
```

Na kolejnym laboratorium zostaną omówione:

- pakiety,
- modyfikatory dostępu,
- hermetyzacja,
- gettery i settery,
- składowe `static`,
- stałe,
- bloki inicjujące,
- przeciążanie metod.
