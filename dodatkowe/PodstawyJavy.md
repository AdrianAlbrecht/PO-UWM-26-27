# **Podstawy języka Java – materiał uzupełniający**

## **Programowanie Obiektowe – Java (2026Z)**

---

## 1. O materiale

Ten plik stanowi **materiał uzupełniający i wyrównawczy** z podstaw języka Java.

Nie jest osobnym laboratorium i nie zastępuje materiałów z kolejnych zajęć z Programowania Obiektowego. Jego celem jest zebranie w jednym miejscu konstrukcji języka, których znajomość będzie zakładana podczas laboratoriów.

Materiał obejmuje przede wszystkim:

- sposób kompilacji i uruchamiania programów w Javie,
- strukturę programu,
- zmienne i typy danych,
- operatory,
- wejście i wyjście,
- `var`, `final` i `enum`,
- instrukcje warunkowe,
- pętle,
- metody,
- metody statyczne,
- przeciążanie metod i `varargs`,
- klasę `Math`,
- generowanie liczb pseudolosowych,
- tablice,
- podstawowe użycie `ArrayList`,
- `String`, `StringBuilder` i `StringBuffer`.

> Niektóre zagadnienia, takie jak `static`, przeciążanie metod, typy generyczne czy kolekcje, pojawią się ponownie na właściwych laboratoriach i zostaną wtedy omówione dokładniej.

---

# 2. Jak działa Java?

Kod źródłowy programu Java zapisujemy zwykle w plikach z rozszerzeniem `.java`.

W klasycznym procesie:

1. programista zapisuje kod źródłowy,
2. kompilator `javac` kompiluje kod do kodu bajtowego (*bytecode*),
3. powstają pliki `.class`,
4. kod bajtowy wykonywany jest przez JVM – Java Virtual Machine.

```text
kod źródłowy .java
        ↓
      javac
        ↓
bytecode .class
        ↓
       JVM
        ↓
uruchomiony program
```

Java jest językiem **statycznie typowanym** i w dużej mierze **zorientowanym obiektowo**.

---

## 2.1. JVM i JDK

### JVM – Java Virtual Machine

JVM odpowiada za wykonywanie kodu bajtowego Javy.

### JDK – Java Development Kit

JDK zawiera narzędzia potrzebne do tworzenia programów, między innymi:

- kompilator `javac`,
- program `java`,
- standardowe biblioteki,
- narzędzia wspomagające tworzenie aplikacji.

Wersję środowiska można sprawdzić:

```bash
java --version
```

oraz:

```bash
javac --version
```

---

# 3. Minimalny program w Javie

Typowy program może wyglądać następująco:

```java
public class HelloWorld {

    public static void main(String[] args) {
        System.out.println("Witaj w świecie Javy!");
    }
}
```

Najważniejsze elementy:

- `public class HelloWorld` – definicja klasy,
- `main()` – punkt wejścia programu,
- `System.out.println()` – wypisanie danych na standardowe wyjście.

Jeżeli plik nazywa się `HelloWorld.java`, można go skompilować:

```bash
javac HelloWorld.java
```

a następnie uruchomić:

```bash
java HelloWorld
```

---

# 4. Struktura pliku `.java`

W pojedynczym pliku źródłowym może znajdować się kilka klas najwyższego poziomu, ale najwyżej jedna z nich może być `public`.

Jeżeli klasa najwyższego poziomu jest `public`, nazwa pliku musi odpowiadać nazwie tej klasy.

Przykład:

```java
public class Main {

    public static void main(String[] args) {
        System.out.println("Start programu");
    }
}

class Helper {

    static void hello() {
        System.out.println("Hello!");
    }
}
```

---

# 5. Zmienne i typy danych

Java jest językiem statycznie typowanym. Oznacza to, że typ każdej zmiennej jest znany podczas kompilacji.

Przykład:

```java
int wiek = 25;
double temperatura = 21.5;
boolean aktywny = true;
char ocena = 'A';
String imie = "Anna";
```

---

## 5.1. Typy proste

Java posiada osiem typów prostych (*primitive types*).

| Typ | Rozmiar | Przykład |
|---|---:|---|
| `byte` | 8 bitów | `byte x = 10;` |
| `short` | 16 bitów | `short x = 2000;` |
| `int` | 32 bity | `int x = 100000;` |
| `long` | 64 bity | `long x = 10_000_000_000L;` |
| `float` | 32 bity | `float x = 3.14f;` |
| `double` | 64 bity | `double x = 3.141592;` |
| `char` | 16 bitów | `char znak = 'A';` |
| `boolean` | rozmiar zależny od implementacji JVM | `boolean ok = true;` |

### Literały `long`

Duże wartości typu `long` zapisujemy zwykle z końcówką `L`:

```java
long populacja = 8_000_000_000L;
```

### Literały `float`

Domyślnym typem literału zmiennoprzecinkowego jest `double`.

Dlatego:

```java
float x = 3.14f;
```

a nie:

```java
// float x = 3.14; // błąd kompilacji
```

---

## 5.2. `char` i Unicode

Typ `char` reprezentuje pojedynczą 16-bitową jednostkę kodową UTF-16.

```java
char litera = 'A';
char polskiZnak = 'Ł';
char symbol = '\u2764';
```

Nie każdy znak Unicode mieści się jednak w pojedynczym `char`. Część znaków, w tym wiele emoji, wymaga pary jednostek UTF-16.

Dlatego tekst najczęściej przechowujemy jako `String`.

```java
String emoji = "😊";
```

---

## 5.3. Typy proste i klasy opakowujące

Dla każdego typu prostego istnieje odpowiadająca mu klasa opakowująca.

| Typ prosty | Klasa opakowująca |
|---|---|
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

Przykład:

```java
int liczba = 42;

Integer obiektowa = liczba;    // autoboxing
int ponownieInt = obiektowa;   // unboxing
```

Klasy opakowujące udostępniają także przydatne stałe i metody:

```java
System.out.println(Integer.MAX_VALUE);
System.out.println(Integer.MIN_VALUE);

int liczba = Integer.parseInt("123");
```

---

# 6. Deklaracja i inicjalizacja

Deklaracja tworzy zmienną danego typu:

```java
int liczba;
double temperatura;
boolean aktywny;
```

Inicjalizacja przypisuje wartość:

```java
liczba = 10;
temperatura = 21.5;
aktywny = true;
```

Można wykonać obie czynności jednocześnie:

```java
int liczba = 10;
```

Lokalnej zmiennej nie można odczytać przed jej zainicjalizowaniem:

```java
int x;

// System.out.println(x); // błąd kompilacji
```

Kilka zmiennych tego samego typu można zadeklarować w jednej instrukcji:

```java
int a = 1, b = 2, c = 3;
```

Zwykle czytelniejsze jest jednak deklarowanie ich osobno.

---

# 7. Nazwy zmiennych

Nazwa zmiennej:

- może zawierać litery, cyfry, `_` i `$`,
- nie może zaczynać się cyfrą,
- nie może być słowem kluczowym Javy,
- rozróżnia wielkość liter.

Przykłady:

```java
int liczbaStudentow = 20;
double sredniaOcen = 4.25;
boolean czyAktywny = true;
```

W Javie standardowo stosuje się styl `camelCase` dla zmiennych i metod.

---

# 8. `var`

Od Javy 10 można stosować lokalne wnioskowanie typu przy użyciu `var`.

```java
var imie = "Adrian";
var wiek = 26;
var waga = 77.6;
```

Kompilator nadal ustala konkretny typ:

```java
var x = 10;      // int
var y = 10.0;    // double
var tekst = "A"; // String
```

`var` **nie oznacza dynamicznego typowania**.

```java
var liczba = 10;

// liczba = "tekst"; // błąd
```

`var` można stosować dla zmiennych lokalnych, jeżeli kompilator może wywnioskować typ z inicjalizatora.

Nie można więc napisać:

```java
// var x;        // brak możliwości określenia typu
// var x = null; // również brak możliwości określenia typu
```

---

# 9. Stałe i `final`

Słowo kluczowe `final` oznacza, że referencja lub zmienna może zostać przypisana tylko raz.

```java
final int MAX_USERS = 100;
```

Próba ponownego przypisania wartości zakończy się błędem:

```java
final int MAX_USERS = 100;

// MAX_USERS = 200; // błąd
```

Dla prostych stałych przyjęta jest konwencja zapisu `UPPER_SNAKE_CASE`.

`final` ma również inne zastosowania, które będą omawiane później:

- `final` przy metodzie – metoda nie może zostać nadpisana,
- `final` przy klasie – po klasie nie można dziedziczyć.

---

# 10. Operatory arytmetyczne

Najważniejsze operatory:

| Operator | Działanie |
|---|---|
| `+` | dodawanie |
| `-` | odejmowanie |
| `*` | mnożenie |
| `/` | dzielenie |
| `%` | reszta z dzielenia |

Przykład:

```java
int a = 10;
int b = 3;

System.out.println(a + b); // 13
System.out.println(a - b); // 7
System.out.println(a * b); // 30
System.out.println(a / b); // 3
System.out.println(a % b); // 1
```

---

## 10.1. Dzielenie całkowite

Jeżeli oba argumenty są całkowite, wynik również jest całkowity:

```java
int wynik = 5 / 2;

System.out.println(wynik); // 2
```

Aby otrzymać wynik zmiennoprzecinkowy:

```java
double wynik = 5.0 / 2;

System.out.println(wynik); // 2.5
```

lub:

```java
double wynik = (double) 5 / 2;
```

---

## 10.2. `^` nie oznacza potęgowania

W Javie:

```java
a ^ b
```

oznacza bitowy XOR.

Nie jest to operator potęgowania.

Do potęgowania można użyć:

```java
Math.pow(2, 3);
```

---

# 11. Operatory przypisania

Podstawowe przypisanie:

```java
int x = 10;
```

Złożone operatory przypisania:

```java
x += 5; // x = x + 5
x -= 2; // x = x - 2
x *= 3; // x = x * 3
x /= 2; // x = x / 2
x %= 4; // x = x % 4
```

---

# 12. Inkrementacja i dekrementacja

```java
int x = 5;

x++;
x--;

++x;
--x;
```

Różnica między postinkrementacją a preinkrementacją jest widoczna, gdy operator występuje wewnątrz większego wyrażenia:

```java
int a = 5;
int b = a++;

System.out.println(a); // 6
System.out.println(b); // 5
```

oraz:

```java
int a = 5;
int b = ++a;

System.out.println(a); // 6
System.out.println(b); // 6
```

---

# 13. Operatory porównania

| Operator | Znaczenie |
|---|---|
| `==` | równe |
| `!=` | różne |
| `<` | mniejsze |
| `>` | większe |
| `<=` | mniejsze lub równe |
| `>=` | większe lub równe |

Przykład:

```java
int wiek = 20;

System.out.println(wiek >= 18); // true
```

---

# 14. Operatory logiczne

| Operator | Znaczenie |
|---|---|
| `&&` | AND |
| `||` | OR |
| `!` | NOT |

Przykład:

```java
int wiek = 20;
boolean maBilet = true;

if (wiek >= 18 && maBilet) {
    System.out.println("Możesz wejść.");
}
```

### Short-circuit

Operatory `&&` i `||` wykorzystują tzw. krótkie spięcie (*short-circuit evaluation*).

Dla:

```java
warunek1 && warunek2
```

jeżeli `warunek1` jest `false`, drugi warunek nie musi zostać sprawdzony.

Dla:

```java
warunek1 || warunek2
```

jeżeli `warunek1` jest `true`, drugi warunek nie musi zostać sprawdzony.

---

# 15. Wyjście na konsolę

## 15.1. `print`

```java
System.out.print("Hello ");
System.out.print("World!");
```

Wynik:

```text
Hello World!
```

---

## 15.2. `println`

```java
System.out.println("Pierwsza linia");
System.out.println("Druga linia");
```

---

## 15.3. `printf`

`printf()` umożliwia formatowanie danych:

```java
String imie = "Adrian";
int wiek = 26;
double waga = 77.6;

System.out.printf(
        "Mam na imię %s, mam %d lat i ważę %.1f kg.%n",
        imie,
        wiek,
        waga
);
```

Podstawowe specyfikatory:

| Specyfikator | Znaczenie |
|---|---|
| `%d` | liczba całkowita |
| `%f` | liczba zmiennoprzecinkowa |
| `%s` | tekst |
| `%c` | znak |
| `%b` | wartość logiczna |
| `%n` | nowa linia |

Przykład zaokrąglenia wyłącznie podczas wyświetlania:

```java
double pi = 3.14159265;

System.out.printf("%.2f%n", pi);
```

Wynik:

```text
3.14
```

Wyrównywanie:

```java
System.out.printf("|%-10s|%10s|%n", "Imię", "Wiek");
System.out.printf("|%-10s|%10d|%n", "Adrian", 26);
```

---

# 16. Wczytywanie danych – `Scanner`

Do prostego odczytu danych z konsoli można użyć klasy `Scanner`.

```java
import java.util.Scanner;
```

Utworzenie obiektu:

```java
Scanner scanner = new Scanner(System.in);
```

Podstawowe metody:

| Metoda | Dane |
|---|---|
| `next()` | pojedyncze słowo |
| `nextLine()` | cały wiersz |
| `nextInt()` | `int` |
| `nextLong()` | `long` |
| `nextDouble()` | `double` |
| `nextBoolean()` | `boolean` |

Przykład:

```java
import java.util.Scanner;

public class Main {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Podaj imię: ");
        String imie = scanner.nextLine();

        System.out.print("Podaj wiek: ");
        int wiek = scanner.nextInt();

        System.out.printf("%s ma %d lat.%n", imie, wiek);

        scanner.close();
    }
}
```

---

## 16.1. Pułapka `nextLine()`

Po metodach takich jak:

```java
nextInt()
nextDouble()
```

znak końca linii może pozostać w wejściu.

Przykład:

```java
System.out.print("Podaj wiek: ");
int wiek = scanner.nextInt();

scanner.nextLine();

System.out.print("Podaj imię: ");
String imie = scanner.nextLine();
```

---

# 17. Konwersje typów

## 17.1. Konwersja automatyczna

Jeżeli konwersja nie powoduje utraty informacji, może zostać wykonana automatycznie:

```java
int x = 10;
double y = x;
```

---

## 17.2. Rzutowanie jawne

```java
double x = 3.9;
int y = (int) x;

System.out.println(y); // 3
```

Część ułamkowa zostaje odrzucona.

---

## 17.3. Tekst i liczby

```java
int liczba = Integer.parseInt("123");
double wynik = Double.parseDouble("3.14");

String tekst = String.valueOf(42);
```

---

# 18. `enum`

`enum` pozwala zdefiniować własny typ posiadający skończony zestaw wartości.

```java
enum DzienTygodnia {
    PONIEDZIALEK,
    WTOREK,
    SRODA,
    CZWARTEK,
    PIATEK,
    SOBOTA,
    NIEDZIELA
}
```

Użycie:

```java
DzienTygodnia dzien = DzienTygodnia.WTOREK;
```

Porównanie:

```java
if (dzien == DzienTygodnia.SOBOTA
        || dzien == DzienTygodnia.NIEDZIELA) {

    System.out.println("Weekend");
}
```

Typ wyliczeniowy pomaga ograniczyć możliwe wartości i zmniejsza ryzyko literówek obecne przy stosowaniu zwykłych napisów.

---

# 19. Instrukcje warunkowe

## 19.1. `if`

```java
if (wiek >= 18) {
    System.out.println("Osoba pełnoletnia.");
}
```

---

## 19.2. `if-else`

```java
if (wiek >= 18) {
    System.out.println("Osoba pełnoletnia.");
} else {
    System.out.println("Osoba niepełnoletnia.");
}
```

---

## 19.3. `else if`

```java
if (punkty >= 90) {
    System.out.println("Bardzo dobry wynik");
} else if (punkty >= 50) {
    System.out.println("Zaliczenie");
} else {
    System.out.println("Brak zaliczenia");
}
```

---

## 19.4. Zagnieżdżone instrukcje

```java
if (wiek >= 18) {
    if (maDowod) {
        System.out.println("Możesz wejść.");
    } else {
        System.out.println("Potrzebujesz dokumentu.");
    }
}
```

---

## 19.5. Operator trójargumentowy

```java
String status = wiek >= 18
        ? "pełnoletni"
        : "niepełnoletni";
```

Operator trójargumentowy jest przydatny przede wszystkim dla krótkich, prostych wyborów wartości.

---

# 20. `switch`

Klasyczna postać:

```java
int opcja = 2;

switch (opcja) {
    case 1:
        System.out.println("Dodawanie");
        break;

    case 2:
        System.out.println("Odejmowanie");
        break;

    default:
        System.out.println("Nieznana opcja");
}
```

Kilka wartości może prowadzić do tego samego przypadku:

```java
switch (dzien) {
    case SOBOTA, NIEDZIELA:
        System.out.println("Weekend");
        break;

    default:
        System.out.println("Dzień roboczy");
}
```

W nowszej Javie można również spotkać składnię ze strzałką:

```java
switch (opcja) {
    case 1 -> System.out.println("Dodawanie");
    case 2 -> System.out.println("Odejmowanie");
    default -> System.out.println("Nieznana opcja");
}
```

---

# 21. Pętle

## 21.1. `for`

```java
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
}
```

Ogólna postać:

```java
for (inicjalizacja; warunek; instrukcjaPoIteracji) {
    // kod
}
```

Poszczególne części mogą być puste:

```java
int potega = 1;

for (; potega <= 1024; potega *= 2) {
    System.out.println(potega);
}
```

---

## 21.2. `while`

```java
int i = 1;

while (i <= 5) {
    System.out.println(i);
    i++;
}
```

`while` jest przydatny, gdy liczba iteracji nie jest z góry znana.

---

## 21.3. `do-while`

```java
int i = 1;

do {
    System.out.println(i);
    i++;
} while (i <= 5);
```

Ciało `do-while` wykonuje się co najmniej raz.

---

## 21.4. `for-each`

```java
int[] liczby = {1, 2, 3, 4, 5};

for (int liczba : liczby) {
    System.out.println(liczba);
}
```

---

## 21.5. `break`

```java
for (int i = 1; i <= 10; i++) {
    if (i == 5) {
        break;
    }

    System.out.println(i);
}
```

---

## 21.6. `continue`

```java
for (int i = 1; i <= 5; i++) {
    if (i == 3) {
        continue;
    }

    System.out.println(i);
}
```

`break` i `continue` są poprawnymi konstrukcjami języka, ale ich nadmierne użycie może utrudniać czytanie programu.

---

# 22. Metody

Metoda grupuje kod realizujący określone zadanie.

Przykład:

```java
public static int dodaj(int a, int b) {
    return a + b;
}
```

Wywołanie:

```java
int wynik = dodaj(2, 3);
```

---

## 22.1. Metoda `void`

Jeżeli metoda niczego nie zwraca:

```java
public static void przywitaj(String imie) {
    System.out.println("Cześć " + imie);
}
```

---

## 22.2. Parametry i argumenty

W definicji:

```java
public static int dodaj(int a, int b)
```

`a` i `b` są parametrami.

W wywołaniu:

```java
dodaj(5, 10);
```

`5` i `10` są argumentami.

---

## 22.3. `return`

```java
public static int kwadrat(int x) {
    return x * x;
}
```

Instrukcja `return` kończy wykonanie metody i opcjonalnie zwraca wartość.

---

# 23. Metody statyczne

Słowo `static` oznacza, że dana składowa należy do klasy, a nie do konkretnej instancji.

```java
public class Calculator {

    public static int add(int a, int b) {
        return a + b;
    }
}
```

Wywołanie:

```java
int wynik = Calculator.add(2, 3);
```

Nie trzeba tworzyć obiektu `Calculator`.

---

## 23.1. Dlaczego `main()` jest statyczna?

JVM musi mieć możliwość wywołania punktu wejścia programu bez wcześniejszego tworzenia obiektu danej klasy.

Dlatego klasyczna metoda wejściowa ma postać:

```java
public static void main(String[] args)
```

---

## 23.2. Ograniczenia kontekstu statycznego

Metoda statyczna nie działa na konkretnej instancji, dlatego bezpośrednio nie może używać:

- `this`,
- niestatycznych pól konkretnego obiektu,
- niestatycznych metod bez posiadania referencji do obiektu.

Przykład:

```java
class Demo {

    int x = 10;

    public static void pokaz() {
        // System.out.println(x); // błąd
    }
}
```

---

## 23.3. Klasy narzędziowe

Metody statyczne są często wykorzystywane w klasach narzędziowych.

Przykłady z biblioteki standardowej:

```java
Math.sqrt(25);
Integer.parseInt("10");
Arrays.sort(tablica);
```

Temat `static` będzie szerzej omawiany na właściwym laboratorium dotyczącym składowych statycznych.

---

# 24. Przeciążanie metod

Przeciążanie (*method overloading*) oznacza utworzenie kilku metod o tej samej nazwie, ale różnych listach parametrów.

Różna liczba parametrów:

```java
static int dodaj(int a, int b) {
    return a + b;
}

static int dodaj(int a, int b, int c) {
    return a + b + c;
}
```

Różne typy:

```java
static int dodaj(int a, int b) {
    return a + b;
}

static double dodaj(double a, double b) {
    return a + b;
}
```

Różna kolejność typów:

```java
static void pokaz(int x, String text) {
}

static void pokaz(String text, int x) {
}
```

Nie można przeciążyć metod wyłącznie przez zmianę typu zwracanego:

```java
// int test(int x)
// double test(int x)
//
// Błąd: identyczna lista parametrów.
```

Przeciążanie zostanie dokładniej omówione w późniejszym laboratorium.

---

# 25. `varargs`

Mechanizm `varargs` umożliwia przekazanie zmiennej liczby argumentów tego samego typu.

```java
static int suma(int... liczby) {
    int wynik = 0;

    for (int liczba : liczby) {
        wynik += liczba;
    }

    return wynik;
}
```

Wywołania:

```java
System.out.println(suma());
System.out.println(suma(1));
System.out.println(suma(1, 2, 3));
```

Wewnątrz metody parametr `int... liczby` zachowuje się jak tablica `int[]`.

---

## 25.1. Ograniczenia `varargs`

Metoda może mieć tylko jeden parametr `varargs` i musi być on ostatnim parametrem:

```java
static void test(String nazwa, int... liczby) {
}
```

Niepoprawne:

```java
// static void test(int... liczby, String nazwa) {
// }
```

---

# 26. Klasa `Math`

Klasa `Math` należy do `java.lang`, więc nie wymaga importu.

Zawiera metody statyczne i stałe matematyczne.

---

## 26.1. Stałe

```java
System.out.println(Math.PI);
System.out.println(Math.E);
```

---

## 26.2. Wartość bezwzględna

```java
System.out.println(Math.abs(-10)); // 10
```

---

## 26.3. Minimum i maksimum

```java
System.out.println(Math.min(10, 20));
System.out.println(Math.max(10, 20));
```

---

## 26.4. Potęgowanie

```java
double wynik = Math.pow(2, 3);

System.out.println(wynik); // 8.0
```

---

## 26.5. Pierwiastek

```java
double wynik = Math.sqrt(25);

System.out.println(wynik); // 5.0
```

---

## 26.6. Zaokrąglanie

```java
System.out.println(Math.round(3.6));
System.out.println(Math.floor(3.6));
System.out.println(Math.ceil(3.1));
```

---

## 26.7. Trygonometria

Funkcje trygonometryczne wykorzystują radiany:

```java
double kat = Math.toRadians(30);

System.out.println(Math.sin(kat));
System.out.println(Math.cos(kat));
System.out.println(Math.tan(kat));
```

Konwersja:

```java
double radiany = Math.toRadians(180);
double stopnie = Math.toDegrees(Math.PI);
```

---

## 26.8. Logarytmy i funkcje wykładnicze

```java
System.out.println(Math.log(Math.E));  // logarytm naturalny
System.out.println(Math.log10(1000));  // logarytm dziesiętny
System.out.println(Math.exp(1));       // e^1
```

---

# 27. Liczby pseudolosowe

Komputerowe generatory liczb używane w typowych programach generują liczby **pseudolosowe**.

---

## 27.1. `Math.random()`

```java
double x = Math.random();
```

wynik należy do przedziału:

```text
0.0 <= x < 1.0
```

Liczba całkowita od `1` do `100`:

```java
int liczba = (int) (Math.random() * 100) + 1;
```

---

## 27.2. `Random`

```java
import java.util.Random;

public class Main {

    public static void main(String[] args) {
        Random random = new Random();

        int liczba = random.nextInt(100);

        System.out.println(liczba);
    }
}
```

`nextInt(100)` zwraca wartość od `0` do `99`.

Zakres `[min, max]`:

```java
int min = 5;
int max = 10;

int liczba = random.nextInt(max - min + 1) + min;
```

---

## 27.3. Ziarno generatora

Można ustawić ziarno:

```java
Random random = new Random(12345);
```

To samo ziarno pozwala uzyskać powtarzalną sekwencję pseudolosową, co jest przydatne podczas testowania.

---

# 28. Tablice jednowymiarowe

Tablica przechowuje określoną liczbę elementów tego samego typu.

Deklaracja:

```java
int[] liczby;
```

Utworzenie:

```java
liczby = new int[5];
```

Jednocześnie:

```java
int[] liczby = new int[5];
```

---

## 28.1. Wartości domyślne

Dla pól tablicy Java automatycznie ustawia wartości domyślne.

Przykładowo:

- `int` → `0`,
- `double` → `0.0`,
- `boolean` → `false`,
- typ referencyjny → `null`.

---

## 28.2. Dostęp do elementów

```java
int[] liczby = new int[3];

liczby[0] = 10;
liczby[1] = 20;
liczby[2] = 30;

System.out.println(liczby[1]);
```

Indeksy zaczynają się od `0`.

---

## 28.3. Inicjalizacja przy deklaracji

```java
int[] liczby = {10, 20, 30, 40};
```

---

## 28.4. Długość tablicy

```java
System.out.println(liczby.length);
```

`length` dla tablicy jest polem, a nie metodą.

---

## 28.5. Iteracja indeksowa

```java
for (int i = 0; i < liczby.length; i++) {
    System.out.println(liczby[i]);
}
```

---

## 28.6. `for-each`

```java
for (int liczba : liczby) {
    System.out.println(liczba);
}
```

`for-each` jest wygodny, gdy nie potrzebujemy indeksu.

---

## 28.7. Najczęstszy błąd – indeks poza zakresem

```java
int[] liczby = {10, 20, 30};

// System.out.println(liczby[3]);
```

Poprawne indeksy:

```text
0
1
2
```

Próba dostępu poza zakresem powoduje `ArrayIndexOutOfBoundsException`.

---

# 29. `Arrays`

Klasa:

```java
java.util.Arrays
```

udostępnia pomocnicze operacje na tablicach.

Import:

```java
import java.util.Arrays;
```

---

## 29.1. `Arrays.toString()`

```java
int[] liczby = {3, 1, 2};

System.out.println(Arrays.toString(liczby));
```

---

## 29.2. `Arrays.sort()`

```java
int[] liczby = {42, 7, 19, 3, 25};

Arrays.sort(liczby);

System.out.println(Arrays.toString(liczby));
```

---

## 29.3. `Arrays.binarySearch()`

Wyszukiwanie binarne powinno być wykonywane na tablicy posortowanej zgodnie z porządkiem oczekiwanym przez metodę.

```java
int[] liczby = {3, 7, 19, 25, 42};

int index = Arrays.binarySearch(liczby, 25);

System.out.println(index);
```

Jeżeli element nie istnieje, metoda zwraca wartość ujemną kodującą miejsce potencjalnego wstawienia elementu.

---

## 29.4. `Arrays.equals()`

Dla tablic operator `==` porównuje referencje.

Do porównania zawartości:

```java
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};

System.out.println(Arrays.equals(a, b)); // true
```

---

## 29.5. `Arrays.fill()`

```java
int[] liczby = new int[5];

Arrays.fill(liczby, 10);

System.out.println(Arrays.toString(liczby));
```

---

## 29.6. `Arrays.copyOf()`

```java
int[] original = {1, 2, 3, 4, 5};

int[] kopia = Arrays.copyOf(
        original,
        original.length
);
```

---

## 29.7. `Arrays.copyOfRange()`

```java
int[] original = {1, 2, 3, 4, 5};

int[] fragment = Arrays.copyOfRange(
        original,
        1,
        4
);

System.out.println(Arrays.toString(fragment));
```

Wynik:

```text
[2, 3, 4]
```

Indeks końcowy jest wyłączny.

---

# 30. Tablice wielowymiarowe

Przykład:

```java
int[][] macierz = {
        {1, 2},
        {3, 4}
};
```

Dostęp:

```java
System.out.println(macierz[1][0]); // 3
```

Iteracja:

```java
for (int[] wiersz : macierz) {
    for (int element : wiersz) {
        System.out.print(element + " ");
    }

    System.out.println();
}
```

Do głębokiego porównywania zagnieżdżonych tablic obiektowych można wykorzystać:

```java
Arrays.deepEquals(a, b);
```

---

# 31. `Arrays.asList()`

Dla tablic typów obiektowych:

```java
String[] owoce = {
        "Jabłko",
        "Banan",
        "Gruszka"
};

var lista = Arrays.asList(owoce);
```

Lista zwrócona przez `Arrays.asList()` ma rozmiar powiązany z tablicą – nie można zmieniać jej długości przez `add()` lub `remove()`.

Uwaga na tablice typów prostych:

```java
int[] liczby = {1, 2, 3};
```

`Arrays.asList(liczby)` nie utworzy `List<Integer>` zawierającej trzy liczby. Tablice typów prostych wymagają innego podejścia.

---

# 32. `ArrayList`

`ArrayList` jest implementacją listy opartą o tablicę o automatycznie zarządzanej pojemności.

Import:

```java
import java.util.ArrayList;
```

Utworzenie:

```java
ArrayList<String> imiona = new ArrayList<>();
```

`ArrayList` przechowuje obiekty. Dla liczb całkowitych używamy więc:

```java
ArrayList<Integer> liczby = new ArrayList<>();
```

a nie:

```java
// ArrayList<int> liczby;
```

---

## 32.1. Podstawowe metody

| Metoda | Działanie |
|---|---|
| `add(element)` | dodaje element |
| `add(index, element)` | dodaje element w danym miejscu |
| `get(index)` | pobiera element |
| `set(index, element)` | zastępuje element |
| `remove(index)` | usuwa element o indeksie |
| `remove(object)` | usuwa pasujący obiekt |
| `size()` | liczba elementów |
| `contains(object)` | sprawdza obecność |
| `isEmpty()` | sprawdza, czy lista jest pusta |
| `clear()` | usuwa wszystkie elementy |

Przykład:

```java
ArrayList<String> studenci = new ArrayList<>();

studenci.add("Anna");
studenci.add("Jan");
studenci.add("Karol");

System.out.println(studenci.get(1));

studenci.remove("Jan");

System.out.println(studenci);
```

Kolekcje zostaną omówione dokładniej na osobnym laboratorium.

---

# 33. `String`

`String` reprezentuje ciąg znaków.

```java
String text = "Java";
```

Obiekty `String` są **niemodyfikowalne** (*immutable*).

Operacja:

```java
text.toUpperCase();
```

nie zmienia oryginalnego napisu. Zwraca nowy:

```java
text = text.toUpperCase();
```

---

## 33.1. Porównywanie napisów

Do porównywania zawartości napisów używamy:

```java
String a = "Java";
String b = "Java";

System.out.println(a.equals(b));
```

Nie należy traktować:

```java
a == b
```

jako ogólnego sposobu porównywania treści napisów, ponieważ `==` dla typów referencyjnych porównuje referencje.

---

## 33.2. Przydatne metody `String`

| Metoda | Znaczenie |
|---|---|
| `length()` | długość |
| `charAt(index)` | znak pod indeksem |
| `substring(...)` | fragment napisu |
| `contains(...)` | sprawdzenie fragmentu |
| `equals(...)` | porównanie |
| `equalsIgnoreCase(...)` | porównanie bez uwzględniania wielkości liter |
| `indexOf(...)` | pierwsze wystąpienie |
| `lastIndexOf(...)` | ostatnie wystąpienie |
| `replace(...)` | zamiana |
| `trim()` | usunięcie wiodących i końcowych znaków o kodzie <= U+0020 |
| `strip()` | usunięcie białych znaków Unicode z początku i końca |
| `toUpperCase()` | wielkie litery |
| `toLowerCase()` | małe litery |
| `startsWith(...)` | sprawdzenie początku |
| `endsWith(...)` | sprawdzenie końca |
| `split(...)` | podział według wyrażenia regularnego |
| `toCharArray()` | tablica `char` |
| `matches(...)` | dopasowanie do regex |
| `repeat(n)` | powtórzenie tekstu |
| `compareTo(...)` | porównanie leksykograficzne |

Przykład:

```java
String s = "Java";

System.out.println(s.length());
System.out.println(s.charAt(1));
System.out.println(s.contains("av"));
System.out.println(s.replace("J", "j"));
System.out.println(s.toUpperCase());
```

---

# 34. `StringBuilder`

Przy częstym budowaniu i modyfikowaniu tekstu wygodniejszy od wielokrotnego łączenia obiektów `String` może być `StringBuilder`.

```java
StringBuilder builder = new StringBuilder("Java");

builder.append(" jest");
builder.append(" fajna");

System.out.println(builder);
```

Przydatne metody:

```java
append(...)
insert(...)
replace(...)
delete(...)
reverse()
```

Przykład:

```java
StringBuilder text = new StringBuilder("Hello");

text.append(" World");
text.insert(5, ",");
text.replace(0, 5, "Hi");

System.out.println(text);
```

---

# 35. `StringBuffer`

`StringBuffer` udostępnia bardzo podobne operacje do `StringBuilder`, ale jego podstawowe operacje są synchronizowane.

```java
StringBuffer buffer = new StringBuffer("Hello");

buffer.append(" World");
buffer.reverse();

System.out.println(buffer);
```

W typowym kodzie jednowątkowym, gdy potrzebny jest modyfikowalny bufor tekstowy, częściej używa się `StringBuilder`.

---

## 35.1. Porównanie

| Cecha | `String` | `StringBuilder` | `StringBuffer` |
|---|---|---|---|
| Modyfikowalny | nie | tak | tak |
| Typowe częste modyfikacje tekstu | mniej wygodne | tak | tak |
| Synchronizacja metod | nie | nie | tak |

---

# 36. Najczęstsze błędy

## Brak średnika

```java
// int x = 10
```

---

## Przypisanie zamiast porównania

Niepoprawne dla `boolean`-owego wyniku porównania liczbowego:

```java
// if (x % 2 = 0) {
// }
```

Poprawnie:

```java
if (x % 2 == 0) {
}
```

---

## Pętla zmienia zmienną w złą stronę

```java
for (int i = 0; i <= 5; i--) {
    System.out.println(i);
}
```

Warunek może nigdy nie przestać być prawdziwy.

---

## Brak zmiany w `while`

```java
int i = 0;

while (i < 10) {
    System.out.println(i);
}
```

Pętla jest nieskończona, ponieważ `i` się nie zmienia.

---

## Indeks poza tablicą

```java
int[] t = {1, 2, 3};

// System.out.println(t[3]);
```

---

## `==` zamiast `equals()` dla napisów

```java
String a = new String("Java");
String b = new String("Java");

System.out.println(a == b);      // porównanie referencji
System.out.println(a.equals(b)); // porównanie zawartości
```

---

## Dzielenie całkowite

```java
double wynik = 5 / 2;

System.out.println(wynik); // 2.0
```

Poprawnie:

```java
double wynik = 5.0 / 2;
```

---

## `^` jako rzekome potęgowanie

```java
int x = 4 ^ 2;
```

To nie jest `4²`.

`^` oznacza XOR.

---

# 37. Zadania powtórkowe

Poniższe zadania są materiałem dodatkowym. Nie wszystkie muszą być realizowane podczas zajęć.

## Zadanie 1 – kalkulator

Napisz aplikację tekstowego kalkulatora przyjmującą dwie liczby oraz wybraną operację:

- dodawanie,
- odejmowanie,
- mnożenie,
- dzielenie.

Obsłuż próbę dzielenia przez zero.

---

## Zadanie 2 – złożone operatory przypisania

Zapisz poniższe operacje z wykorzystaniem operatorów złożonego przypisania tam, gdzie ma to sens:

```text
a = a + 4
b = b - a
c = c * (2 - 4 * a)
d = d / (4 - a * a)
```

Zwróć uwagę, że `^` w Javie nie oznacza potęgowania.

---

## Zadanie 3 – rok przestępny

Napisz program sprawdzający, czy podany rok jest przestępny.

Rok jest przestępny, jeżeli:

- jest podzielny przez `4`,
- ale nie jest podzielny przez `100`,
- chyba że jest jednocześnie podzielny przez `400`.

---

## Zadanie 4 – progi podatkowe

Napisz program obliczający podatek na podstawie reguł podanych w treści zadania przez prowadzącego.

Celem zadania jest przećwiczenie:

- `Scanner`,
- typów liczbowych,
- instrukcji warunkowych,
- obliczeń zmiennoprzecinkowych.

> Wartości progów w zadaniu dydaktycznym nie należy traktować jako aktualnych przepisów podatkowych.

---

## Zadanie 5 – poprawna data

Pobierz:

```text
dzień
miesiąc
rok
```

i sprawdź, czy tworzą poprawną datę.

Spróbuj wykorzystać:

```java
enum Miesiac
```

oraz:

```java
switch
```

Uwzględnij rok przestępny.

---

## Zadanie 6 – liczby parzyste i nieparzyste

Za pomocą `do-while` wyświetl pierwszych 20 dodatnich liczb:

- parzystych,
- nieparzystych.

---

## Zadanie 7 – odwracanie liczby

Pobierz dodatnią liczbę całkowitą i wyświetl jej cyfry w odwrotnej kolejności.

Przykład:

```text
12345 → 54321
```

Nie konwertuj liczby do `String`.

---

## Zadanie 8 – NWW

Dla dwóch dodatnich liczb całkowitych oblicz ich najmniejszą wspólną wielokrotność.

---

## Zadanie 9 – suma przedziału

Wczytaj dwie liczby:

```text
n < m
```

i oblicz:

```text
n + (n + 1) + ... + m
```

---

## Zadanie 10 – filtrowanie liczb

Pobierz dodatnie liczby całkowite:

```text
a, b, c
```

Wyświetl wszystkie dodatnie liczby całkowite:

```text
x > b
x <= a
x % c == 0
```

---

## Zadanie 11 – dane do wartości ujemnej

Wczytuj liczby całkowite aż do podania liczby ujemnej.

Następnie wyświetl:

- minimum,
- maksimum

spośród wcześniejszych liczb.

Nie używaj kolekcji.

---

## Zadanie 12 – liczba pierwsza

Napisz statyczną metodę:

```java
largestPrimeBelow(int n)
```

która zwraca największą liczbę pierwszą mniejszą od `n`.

Załóż:

```text
n > 2
```

---

## Zadanie 13 – potęga bez gotowej funkcji

Napisz statyczną metodę, która dla dodatniego `n` zwraca:

```text
7^(-n)
```

Nie korzystaj wewnątrz metody z `Math.pow()`.

---

## Zadanie 14 – losowanie z zakresu

Napisz metodę:

```java
generateRandomIntInRange(int min, int max)
```

zwracającą losową liczbę całkowitą z domkniętego przedziału:

```text
[min, max]
```

---

## Zadanie 15 – tablica losowych liczb

Utwórz tablicę 20 losowych liczb całkowitych z zakresu od `1` do `100`.

Oblicz ich średnią.

---

## Zadanie 16 – kwadraty liczb całkowitych

Utwórz tablicę 30 liczb całkowitych.

Policz, ile elementów jest kwadratem pewnej liczby całkowitej.

---

## Zadanie 17 – minimum w `ArrayList`

Napisz metodę:

```java
minimumValue(ArrayList<Integer> values)
```

zwracającą najmniejszy element listy.

Nie używaj gotowej metody wyznaczającej minimum.

---

## Zadanie 18 – odwrócenie `ArrayList`

Napisz metodę przyjmującą:

```java
ArrayList<Integer>
```

i zwracającą nową listę z elementami w odwrotnej kolejności.

Przykład:

```text
[1, 2, 3, 4, 5]
↓
[5, 4, 3, 2, 1]
```

---

## Zadanie 19 – zamiana pierwszego i ostatniego znaku

Napisz metodę przyjmującą `String` i zwracającą napis z zamienionym pierwszym i ostatnim znakiem.

Uwzględnij przypadki:

- pustego napisu,
- napisu jednoznakowego.

---

## Zadanie 20 – piramida

Pobierz znak i dodatnią liczbę `n`.

Za pomocą `StringBuilder` zbuduj piramidę:

```text
*
***
*****
```

dla:

```text
n = 3
```

---

## Zadanie 21 – `StringBuffer`

Napisz metodę:

```java
capitalizeEverySecond(StringBuffer text)
```

która zmienia co drugi znak będący literą na wielką literę.

---

## Zadanie 22 – analiza i naprawa kodu

Znajdź i popraw błędy:

```java
import java.util.Scanner;

public class Main {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in)

        System.out.print("Podaj liczbę: ");
        int liczba = scanner.nextInt();

        if (liczba % 2 = 0) {
            System.out.println("Liczba jest parzysta");
        } else {
            System.out.println("Liczba jest nieparzysta")
        }

        for (int i = 0; i <= 5; i--) {
            System.out.println(i);
        }
    }
}
```

Dla każdego problemu określ:

1. czy jest błędem kompilacji,
2. czy może prowadzić do błędu w czasie wykonania,
3. czy jest błędem logicznym.

---

# 38. Co należy umieć przed właściwymi laboratoriami?

Przed rozpoczęciem dalszej części przedmiotu należy swobodnie rozumieć kod wykorzystujący:

```text
zmienne
typy proste
String
Scanner
operatory
if / else
switch
for
while
do-while
tablice
proste metody
return
```

W szczególności student powinien potrafić:

- przeczytać prosty program i przewidzieć jego wynik,
- znaleźć podstawowe błędy składniowe i logiczne,
- napisać prostą metodę,
- przejść pętlą po tablicy,
- pobrać dane od użytkownika,
- poprawnie dobrać instrukcję warunkową,
- samodzielnie rozwiązać proste zadanie algorytmiczne.

Kolejne laboratoria koncentrują się już przede wszystkim na **programowaniu obiektowym**, a nie na nauce podstawowej składni Javy.
