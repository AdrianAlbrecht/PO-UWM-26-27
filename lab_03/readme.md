# **Programowanie Obiektowe – Java (2026Z)**

## **Lab 3: Hermetyzacja, składowe statyczne i przeciążanie metod**

---

## 1. Cel zajęć

Celem laboratorium jest poznanie mechanizmów pozwalających kontrolować dostęp do stanu obiektu oraz odróżnić elementy należące do konkretnego obiektu od elementów wspólnych dla całej klasy.

Podczas zajęć omówione zostaną:

- pakiety,
- modyfikatory dostępu,
- hermetyzacja,
- gettery i settery,
- walidacja stanu obiektu,
- pola i metody `static`,
- stałe z użyciem `final` i `static final`,
- statyczne oraz instancyjne bloki inicjujące,
- przeciążanie metod.

Po zakończeniu zajęć osoba studencka powinna:

- potrafić dobrać odpowiedni modyfikator dostępu,
- rozumieć ideę hermetyzacji,
- nie udostępniać bez potrzeby wewnętrznego stanu obiektu,
- potrafić tworzyć gettery i settery z walidacją,
- rozumieć, że nie każde pole powinno mieć setter,
- rozumieć różnicę pomiędzy składową instancji a składową statyczną,
- poprawnie stosować `static`,
- potrafić definiować stałe,
- znać kolejność podstawowej inicjalizacji klasy i obiektu,
- potrafić przeciążać metody,
- potrafić rozpoznać typowe błędy projektowe związane z nadmiernym udostępnianiem pól i nadużywaniem `static`.

---

# 2. Pakiety

Pakiet (*package*) służy do logicznego grupowania klas i innych typów.

Przykładowa struktura:

```text
src/
└── pl/
    └── uwm/
        └── sklep/
            ├── model/
            │   └── Product.java
            └── app/
                └── Main.java
```

W pliku:

```text
Product.java
```

możemy zadeklarować:

```java
package pl.uwm.sklep.model;
```

A w innej klasie:

```java
import pl.uwm.sklep.model.Product;
```

---

## 2.1. Po co używamy pakietów?

Pakiety pomagają:

- organizować większe projekty,
- unikać konfliktów nazw,
- kontrolować dostęp do elementów programu,
- rozdzielać odpowiedzialności pomiędzy fragmenty aplikacji.

Nazwy pakietów zapisujemy standardowo **małymi literami**.

Przykładowo:

```text
pl.uwm.po.model
pl.uwm.po.service
pl.uwm.po.app
```

> Pakiety nie tworzą mechanizmu dziedziczenia.  
> `pl.uwm.model` oraz `pl.uwm.model.internal` są odrębnymi pakietami.

---

# 3. Modyfikatory dostępu

Java pozwala kontrolować widoczność klas i ich składowych.

Najważniejsze poziomy dostępu:

| Modyfikator | Ta sama klasa | Ten sam pakiet | Podklasa w innym pakiecie | Inna klasa w innym pakiecie |
|---|:---:|:---:|:---:|:---:|
| `private` | ✅ | ❌ | ❌ | ❌ |
| brak modyfikatora (*package-private*) | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅* | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

\* Dostęp `protected` poza pakietem obowiązuje w kontekście dziedziczenia. Szczegóły zostaną omówione przy dziedziczeniu.

---

## 3.1. `private`

Element oznaczony jako `private` jest dostępny tylko wewnątrz tej samej klasy.

```java
public class User {

    private String password;

    public User(String password) {
        this.password = password;
    }

    public boolean checkPassword(String providedPassword) {
        return this.password.equals(providedPassword);
    }
}
```

Kod spoza klasy nie może wykonać:

```java
// user.password = "1234";
```

---

## 3.2. Brak modyfikatora – package-private

Jeżeli pominiemy modyfikator:

```java
class Helper {
}
```

albo:

```java
int value;
```

element jest dostępny w obrębie tego samego pakietu.

Nie istnieje słowo kluczowe:

```text
default
```

służące do ustawienia takiego poziomu dostępu.

Mówimy po prostu o dostępie:

```text
package-private
```

---

## 3.3. `protected`

`protected` będzie szczególnie istotne podczas omawiania dziedziczenia.

Na razie warto zapamiętać, że element `protected`:

- jest dostępny w swoim pakiecie,
- może być dostępny w podklasach znajdujących się w innych pakietach.

---

## 3.4. `public`

Element `public` może być używany przez kod znajdujący się w innych klasach i pakietach, o ile sama klasa jest dla tego kodu dostępna.

Nie oznacza to jednak, że wszystko powinno być `public`.

> Dobrą zasadą jest udostępnianie możliwie małego, świadomie zaprojektowanego interfejsu klasy.

---

# 4. Hermetyzacja

**Hermetyzacja** polega na ukrywaniu szczegółów wewnętrznych obiektu oraz kontrolowaniu sposobu, w jaki jego stan może być odczytywany i zmieniany.

Nie chodzi jedynie o:

```text
private + getter + setter
```

dla każdego pola.

Celem jest przede wszystkim zapewnienie, że obiekt nie znajdzie się łatwo w stanie sprzecznym z założeniami programu.

---

# 5. Problem z publicznymi polami

Rozważmy:

```java
public class BankAccount {

    public String owner;
    public double balance;
}
```

Kod zewnętrzny może zrobić:

```java
BankAccount account = new BankAccount();

account.balance = -1_000_000;
```

Obiekt nie posiada żadnej kontroli nad zmianą własnego stanu.

Można również wykonać:

```java
account.owner = null;
```

nawet jeżeli w założeniach programu właściciel konta zawsze powinien istnieć.

---

# 6. Pola prywatne

Pierwszym krokiem jest ukrycie stanu:

```java
public class BankAccount {

    private String owner;
    private double balance;
}
```

Teraz kod zewnętrzny nie może wykonać:

```java
// account.balance = -1_000_000;
```

ale potrzebujemy świadomie zaprojektować operacje, przez które obiekt będzie używany.

---

# 7. Getter

Getter pozwala odczytać wartość pola.

```java
public double getBalance() {
    return this.balance;
}
```

Przykład:

```java
System.out.println(account.getBalance());
```

Dla pól logicznych często spotyka się konwencję:

```java
public boolean isActive() {
    return this.active;
}
```

---

# 8. Setter

Setter umożliwia zmianę wartości pola.

```java
public void setOwner(String owner) {
    this.owner = owner;
}
```

Setter może i często powinien sprawdzać poprawność danych.

```java
public void setOwner(String owner) {
    if (owner != null && !owner.isBlank()) {
        this.owner = owner;
    }
}
```

---

# 9. Czy każde pole powinno mieć setter?

**Nie.**

Rozważmy konto bankowe.

```java
private double balance;
```

Możemy napisać:

```java
public void setBalance(double balance) {
    this.balance = balance;
}
```

ale wtedy użytkownik klasy może wykonać:

```java
account.setBalance(1_000_000);
```

To prawdopodobnie nie odpowiada modelowi konta bankowego.

Lepszym rozwiązaniem są operacje opisujące rzeczywiste zachowanie:

```java
public void deposit(double amount) {
    if (amount > 0) {
        this.balance += amount;
    }
}
```

oraz:

```java
public boolean withdraw(double amount) {
    if (amount <= 0 || amount > this.balance) {
        return false;
    }

    this.balance -= amount;
    return true;
}
```

Wtedy stan:

```text
balance
```

może zmieniać się tylko według reguł określonych przez samą klasę.

---

# 10. Przykład poprawnej hermetyzacji

```java
public class BankAccount {

    private String owner;
    private double balance;

    public BankAccount(
            String owner,
            double initialBalance
    ) {
        if (owner == null || owner.isBlank()) {
            throw new IllegalArgumentException(
                    "Właściciel nie może być pusty."
            );
        }

        if (initialBalance < 0) {
            throw new IllegalArgumentException(
                    "Saldo początkowe nie może być ujemne."
            );
        }

        this.owner = owner;
        this.balance = initialBalance;
    }

    public String getOwner() {
        return this.owner;
    }

    public double getBalance() {
        return this.balance;
    }

    public void setOwner(String owner) {
        if (owner != null && !owner.isBlank()) {
            this.owner = owner;
        }
    }

    public void deposit(double amount) {
        if (amount > 0) {
            this.balance += amount;
        }
    }

    public boolean withdraw(double amount) {
        if (amount <= 0 || amount > this.balance) {
            return false;
        }

        this.balance -= amount;
        return true;
    }
}
```

Zwróć uwagę:

- `balance` jest `private`,
- istnieje getter `getBalance()`,
- **nie istnieje `setBalance()`**,
- saldo zmienia się przez `deposit()` oraz `withdraw()`.

> Na obecnym etapie wykorzystujemy `IllegalArgumentException` jedynie jako gotowy mechanizm sygnalizowania błędnego argumentu. Wyjątki będą dokładnie omawiane na późniejszym laboratorium.

---

# 11. Hermetyzacja a mutowalne obiekty

Samo pole `private` nie zawsze wystarcza.

Rozważmy:

```java
public class Measurements {

    private int[] values;

    public Measurements(int[] values) {
        this.values = values;
    }

    public int[] getValues() {
        return this.values;
    }
}
```

Kod:

```java
int[] data = {10, 20, 30};

Measurements measurements =
        new Measurements(data);

data[0] = -100;
```

zmienia również dane przechowywane przez obiekt.

Tak samo:

```java
measurements.getValues()[1] = -200;
```

pozwala zmienić tablicę znajdującą się wewnątrz obiektu.

---

## 11.1. Kopia defensywna

Możemy wykonać kopię:

```java
import java.util.Arrays;

public class Measurements {

    private int[] values;

    public Measurements(int[] values) {
        this.values =
                Arrays.copyOf(
                        values,
                        values.length
                );
    }

    public int[] getValues() {
        return Arrays.copyOf(
                this.values,
                this.values.length
        );
    }
}
```

Dzięki temu kod zewnętrzny nie otrzymuje bezpośredniej referencji do wewnętrznej tablicy.

To przykład pokazujący, że hermetyzacja dotyczy nie tylko modyfikatorów dostępu, ale również **kontroli dostępu do stanu obiektu**.

---

# 12. Składowe instancji a składowe klasy

Do tej pory większość pól należała do konkretnego obiektu.

```java
public class Car {

    private String brand;
}
```

Jeżeli utworzymy:

```java
Car first = new Car(...);
Car second = new Car(...);
```

każdy obiekt posiada własne pole:

```text
brand
```

---

# 13. `static`

Element oznaczony słowem:

```java
static
```

należy do **klasy jako całości**, a nie do konkretnego obiektu.

Przykład:

```java
public class Product {

    private String name;

    private static int numberOfProducts = 0;

    public Product(String name) {
        this.name = name;
        numberOfProducts++;
    }
}
```

Pole:

```java
name
```

istnieje osobno dla każdego produktu.

Pole:

```java
numberOfProducts
```

jest wspólne dla całej klasy.

---

# 14. Dostęp do pola statycznego

Do składowej statycznej najlepiej odwoływać się przez nazwę klasy:

```java
Product.getNumberOfProducts();
```

a nie przez konkretny obiekt.

Przykład:

```java
public class Product {

    private String name;
    private static int numberOfProducts;

    public Product(String name) {
        this.name = name;
        numberOfProducts++;
    }

    public static int getNumberOfProducts() {
        return numberOfProducts;
    }
}
```

Użycie:

```java
new Product("Monitor");
new Product("Klawiatura");
new Product("Mysz");

System.out.println(
        Product.getNumberOfProducts()
);
```

Wynik:

```text
3
```

---

# 15. Metody statyczne

Metoda statyczna również należy do klasy.

```java
public class Calculator {

    public static int add(int a, int b) {
        return a + b;
    }
}
```

Wywołanie:

```java
int result = Calculator.add(2, 3);
```

Nie musimy tworzyć:

```java
new Calculator();
```

---

# 16. Ograniczenia metod statycznych

Metoda statyczna nie jest wykonywana na konkretnym obiekcie.

Dlatego bezpośrednio nie może korzystać z:

- `this`,
- niestatycznych pól bez wskazania obiektu,
- niestatycznych metod bez wskazania obiektu.

Niepoprawnie:

```java
public class User {

    private String name;

    public static void showName() {
        // System.out.println(this.name);
    }
}
```

`this` oznacza bieżący obiekt, a w metodzie statycznej takiego bieżącego obiektu nie ma.

---

# 17. Kiedy używać `static`?

Typowe zastosowania:

### Wspólne liczniki

```java
private static int numberOfObjects;
```

### Stałe

```java
public static final int MAX_USERS = 100;
```

### Metody narzędziowe

```java
Math.sqrt(25);
```

### Fabryki lub metody pomocnicze powiązane z klasą

```java
Integer.parseInt("123");
```

---

## 17.1. Kiedy nie używać `static`?

Jeżeli dana informacja opisuje **stan konkretnego obiektu**, zazwyczaj nie powinna być statyczna.

Źle:

```java
public class Student {

    private static String name;
}
```

W takim przypadku wszyscy studenci dzieliliby jedno wspólne `name`.

Poprawniej:

```java
public class Student {

    private String name;
}
```

---

# 18. `final`

`final` oznacza, że po dokonaniu przypisania nie można przypisać zmiennej lub polu innej wartości.

```java
final int max = 100;
```

Nie można później wykonać:

```java
// max = 200;
```

---

# 19. Stałe klasowe – `static final`

Stała wspólna dla wszystkich obiektów jest najczęściej deklarowana:

```java
public static final int MAX_PRODUCTS = 100;
```

Użycie:

```java
System.out.println(
        Product.MAX_PRODUCTS
);
```

Konwencja nazewnicza stałych:

```text
UPPER_SNAKE_CASE
```

Przykłady:

```java
public static final double PI = 3.1415926535;
public static final int MAX_RETRIES = 3;
public static final String DEFAULT_LANGUAGE = "pl";
```

---

## 19.1. `final` a mutowalny obiekt

`final` przy referencji oznacza, że referencji nie można skierować do innego obiektu.

Nie oznacza automatycznie, że sam obiekt staje się niemodyfikowalny.

Przykład:

```java
final int[] values = {1, 2, 3};

values[0] = 100; // poprawne

// values = new int[]{4, 5, 6}; // błąd
```

---

# 20. Bloki inicjujące

Java posiada między innymi:

- statyczne bloki inicjujące,
- instancyjne bloki inicjujące.

---

# 21. Statyczny blok inicjujący

```java
public class Configuration {

    private static String mode;

    static {
        System.out.println(
                "Inicjalizacja klasy Configuration"
        );

        mode = "PRODUCTION";
    }
}
```

Blok:

```java
static {
    ...
}
```

jest wykonywany podczas inicjalizacji klasy przez JVM, zasadniczo raz dla danego załadowania klasy.

Może służyć do bardziej złożonej inicjalizacji pól statycznych.

---

# 22. Instancyjny blok inicjujący

```java
public class User {

    private String name;

    {
        System.out.println(
                "Tworzenie nowego obiektu User"
        );
    }

    public User(String name) {
        this.name = name;
    }
}
```

Blok:

```java
{
    ...
}
```

jest wykonywany podczas tworzenia każdej instancji przed wykonaniem właściwego ciała konstruktora.

W praktyce większość typowej inicjalizacji można czytelnie wykonać w konstruktorach, dlatego bloków instancyjnych nie należy stosować bez potrzeby.

---

# 23. Kolejność – prosty przykład

```java
public class Example {

    static {
        System.out.println("1. Blok statyczny");
    }

    {
        System.out.println("2. Blok instancyjny");
    }

    public Example() {
        System.out.println("3. Konstruktor");
    }
}
```

Program:

```java
public class Main {

    public static void main(String[] args) {
        System.out.println("A");

        new Example();

        System.out.println("B");

        new Example();
    }
}
```

Przy pierwszym użyciu klasy można zaobserwować wykonanie bloku statycznego, a dla każdej nowej instancji – bloku instancyjnego i konstruktora.

---

# 24. Przeciążanie metod

**Przeciążanie metod** (*method overloading*) oznacza utworzenie kilku metod o tej samej nazwie, ale różnych listach parametrów.

Przykład:

```java
public class Calculator {

    public static int add(
            int a,
            int b
    ) {
        return a + b;
    }

    public static int add(
            int a,
            int b,
            int c
    ) {
        return a + b + c;
    }

    public static double add(
            double a,
            double b
    ) {
        return a + b;
    }
}
```

Wywołania:

```java
Calculator.add(2, 3);
Calculator.add(2, 3, 4);
Calculator.add(2.5, 3.5);
```

Kompilator dobiera odpowiednią metodę na podstawie argumentów.

---

# 25. Co może różnić przeciążone metody?

### Liczba parametrów

```java
print(String text)

print(String text, int count)
```

### Typ parametrów

```java
print(int value)

print(double value)
```

### Kolejność różnych typów

```java
show(String text, int number)

show(int number, String text)
```

---

# 26. Czego nie wystarczy zmienić?

Nie można przeciążyć metod wyłącznie przez zmianę typu zwracanego.

Niepoprawnie:

```java
public int getValue(int x) {
    return x;
}

// public double getValue(int x) {
//     return x;
// }
```

Dla wywołania:

```java
getValue(10);
```

kompilator nie wiedziałby, którą wersję wybrać na podstawie samych argumentów.

---

# 27. Przeciążanie konstruktorów a metod

Na poprzednim laboratorium używaliśmy:

```java
public Person() {
}

public Person(String name) {
}

public Person(String name, int age) {
}
```

To również jest **przeciążanie** – w tym przypadku konstruktorów.

W tym laboratorium stosujemy tę samą ideę do zwykłych metod.

---

# 28. Przeciążanie a nadpisywanie

Nie należy mylić:

```text
overloading
```

z:

```text
overriding
```

### Przeciążanie

Ta sama nazwa metody, różne parametry:

```java
add(int, int)
add(double, double)
```

### Nadpisywanie

Dotyczy dziedziczenia i zmiany implementacji metody odziedziczonej z klasy bazowej.

Nadpisywanie zostanie omówione podczas kolejnych laboratoriów.

---

# 29. Przykład łączący zagadnienia

```java
public class Product {

    public static final int MAX_PRODUCTS = 100;

    private static int numberOfProducts = 0;

    private String name;
    private double price;

    static {
        System.out.println(
                "Załadowano klasę Product."
        );
    }

    public Product(
            String name,
            double price
    ) {
        this.setName(name);
        this.setPrice(price);

        numberOfProducts++;
    }

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        if (name != null && !name.isBlank()) {
            this.name = name;
        }
    }

    public double getPrice() {
        return this.price;
    }

    public void setPrice(double price) {
        if (price >= 0) {
            this.price = price;
        }
    }

    public static int getNumberOfProducts() {
        return numberOfProducts;
    }

    public double calculatePrice() {
        return this.price;
    }

    public double calculatePrice(
            double discountPercent
    ) {
        return this.price
                * (1 - discountPercent / 100.0);
    }
}
```

W jednej klasie występują:

- pola `private`,
- gettery i settery,
- walidacja,
- pole statyczne,
- stała,
- blok statyczny,
- metoda statyczna,
- przeciążone metody.

---

# 30. Typowe błędy

## 30.1. Publiczne pola bez potrzeby

```java
public class Person {

    public String name;
    public int age;
}
```

Taka klasa praktycznie nie kontroluje własnego stanu.

---

## 30.2. Setter do wszystkiego

```java
public void setBalance(double balance) {
    this.balance = balance;
}
```

Sam fakt, że pole jest `private`, nie oznacza jeszcze dobrej hermetyzacji.

---

## 30.3. Statyczne pole zamiast pola obiektu

```java
public class Student {

    private static String name;
}
```

Każdy obiekt korzystałby z tego samego pola.

---

## 30.4. Próba użycia `this` w metodzie statycznej

```java
public static void show() {
    // System.out.println(this.name);
}
```

Metoda statyczna nie posiada bieżącego obiektu.

---

## 30.5. Próba zmiany stałej

```java
public static final int MAX = 10;

// MAX = 20;
```

---

## 30.6. Przeciążanie tylko przez typ zwracany

```java
// int calculate(int x)
// double calculate(int x)
```

To nie jest poprawne przeciążenie.

---

# **Zadania**

## Zadanie 1 – pakiety

Utwórz pakiety:

```text
animals
app
```

W pakiecie:

```text
animals
```

utwórz klasy:

```text
Dog
Cat
```

Każda klasa powinna posiadać:

```text
name
age
```

oraz metodę:

```java
makeSound()
```

Dla psa:

```text
Woof, woof!
```

dla kota:

```text
Meow!
```

W pakiecie:

```text
app
```

utwórz klasę `Main`, zaimportuj obie klasy, utwórz obiekty i wywołaj ich metody.

---

## Zadanie 2 – hermetyzacja klasy `Person`

Utwórz klasę:

```text
Person
```

z prywatnymi polami:

```text
name
age
email
```

Dodaj:

- konstruktor,
- gettery,
- odpowiednie settery.

Reguły:

- `name` nie może być `null` ani pustym napisem,
- `age` musi należeć do przedziału od `0` do `150`,
- `email` powinien zawierać znak `@`.

Zastanów się:

> Czy wszystkie trzy pola rzeczywiście muszą posiadać setter?

---

## Zadanie 3 – konto bankowe

Utwórz klasę:

```text
BankAccount
```

posiadającą:

```text
owner
balance
```

Pola mają być prywatne.

Udostępnij:

```java
getOwner()
getBalance()
deposit(double amount)
withdraw(double amount)
```

Nie twórz:

```java
setBalance(...)
```

Metoda `deposit()` ma akceptować tylko dodatnie wartości.

Metoda `withdraw()` może wykonać operację tylko wtedy, gdy:

- kwota jest dodatnia,
- na koncie znajdują się wystarczające środki.

---

## Zadanie 4 – kopia defensywna

Utwórz klasę:

```text
ExamResults
```

z prywatnym polem:

```java
int[] points;
```

Konstruktor ma przyjmować tablicę punktów.

Dodaj:

```java
getPoints()
```

Najpierw zaimplementuj klasę bez kopiowania tablicy.

Sprawdź:

1. czy zmiana oryginalnej tablicy po utworzeniu obiektu wpływa na obiekt,
2. czy zmiana tablicy otrzymanej z `getPoints()` wpływa na obiekt.

Następnie popraw klasę za pomocą kopii defensywnych.

---

## Zadanie 5 – licznik obiektów

Utwórz klasę:

```text
Product
```

posiadającą:

```text
name
price
```

oraz statyczne pole:

```text
numberOfProducts
```

Licznik powinien zwiększać się przy utworzeniu każdego obiektu.

Dodaj statyczną metodę:

```java
getNumberOfProducts()
```

Utwórz kilka produktów i sprawdź działanie licznika.

---

## Zadanie 6 – stała

Rozbuduj klasę `Product` o:

```java
public static final int MAX_PRODUCTS
```

Ustal dowolny sensowny limit.

Konstruktor powinien uniemożliwiać utworzenie większej liczby produktów niż dopuszczalna.

Na tym etapie możesz zasygnalizować problem w prosty sposób, np. komunikatem lub `IllegalStateException`.

Odpowiedz:

1. dlaczego `MAX_PRODUCTS` powinno być `static`,
2. dlaczego powinno być `final`,
3. dlaczego `numberOfProducts` nie powinno być `final`.

---

## Zadanie 7 – kalkulator statyczny

Utwórz klasę:

```text
StaticCalculator
```

posiadającą statyczne metody:

```java
add(...)
subtract(...)
multiply(...)
```

oraz statyczne pole:

```text
numberOfOperations
```

Każde wywołanie operacji ma zwiększać licznik.

Dodaj:

```java
getNumberOfOperations()
```

Nie twórz obiektu klasy kalkulatora w `main`.

---

## Zadanie 8 – przeciążanie

W klasie `StaticCalculator` przeciąż metodę:

```java
add(...)
```

tak, aby obsługiwała:

```java
add(int a, int b)
add(int a, int b, int c)
add(double a, double b)
```

Sprawdź działanie każdej wersji.

Spróbuj utworzyć dwie metody różniące się wyłącznie typem zwracanym i sprawdź reakcję kompilatora.

---

## Zadanie 9 – bloki inicjujące

Utwórz klasę:

```text
InitializationDemo
```

zawierającą:

- statyczne pole,
- pole instancji,
- blok statyczny,
- blok instancyjny,
- konstruktor.

Każdy element powinien wypisywać komunikat pozwalający rozpoznać kolejność wykonania.

Utwórz dwa obiekty.

Zapisz kolejność komunikatów **przed uruchomieniem programu**, a następnie zweryfikuj odpowiedź.

---

## Zadanie 10 – co powinno być `static`?

Dla każdego elementu zdecyduj, czy powinien być:

```text
statyczny
```

czy:

```text
instancyjny
```

i uzasadnij odpowiedź:

1. `studentName` w klasie `Student`,
2. `numberOfStudents` w klasie `Student`,
3. `PI` w klasie matematycznej,
4. `balance` w klasie `BankAccount`,
5. `calculateVat(double price)` w bezstanowej klasie pomocniczej,
6. `carMileage` w klasie `Car`,
7. `MAX_LOGIN_ATTEMPTS` w klasie `LoginPolicy`.

---

## Zadanie 11 – refaktoryzacja

Otrzymujesz kod:

```java
public class User {

    public String login;
    public String password;
    public static int age;

    public User(
            String login,
            String password,
            int age
    ) {
        this.login = login;
        this.password = password;
        User.age = age;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public static void showUser() {
        System.out.println(
                "Login: " + login
                + ", hasło: " + password
                + ", wiek: " + age
        );
    }
}
```

Przeanalizuj kod.

1. Wskaż błędy kompilacji.
2. Wskaż problemy projektowe.
3. Zdecyduj, które pola powinny być instancyjne, a które statyczne.
4. Zastosuj hermetyzację.
5. Zdecyduj, czy hasło powinno mieć zwykły getter.
6. Dodaj walidację wieku.
7. Przepisz `showUser()` tak, aby odpowiadała poprawionej strukturze klasy.

---

## Zadanie 12 – projekt mini-systemu

Utwórz klasy:

```text
Course
Student
```

### `Course`

Powinna posiadać co najmniej:

```text
name
maxStudents
numberOfCreatedCourses
```

### `Student`

Powinna posiadać co najmniej:

```text
name
albumNumber
```

Zastosuj:

- pola `private`,
- świadomie dobrane gettery i settery,
- walidację,
- co najmniej jedną stałą `static final`,
- co najmniej jedno sensowne pole `static`,
- co najmniej jedną przeciążoną metodę.

Nie dodawaj getterów i setterów automatycznie do każdego pola.

Po wykonaniu kodu uzasadnij:

> Dlaczego dla każdego pola wybrano właśnie taki sposób dostępu?

---

# **Zadanie dodatkowe – znajdź naruszenie hermetyzacji**

Przeanalizuj:

```java
public class Team {

    private String name;
    private String[] members;

    public Team(
            String name,
            String[] members
    ) {
        this.name = name;
        this.members = members;
    }

    public String[] getMembers() {
        return this.members;
    }
}
```

Kod testowy:

```java
String[] people = {
        "Anna",
        "Jan",
        "Maria"
};

Team team =
        new Team(
                "Java Team",
                people
        );

people[0] = "HACKED";

String[] received =
        team.getMembers();

received[1] = "CHANGED";
```

Odpowiedz:

1. Czy `members` rzeczywiście jest chronione tylko dlatego, że jest `private`?
2. Jak można zmienić wewnętrzny stan obiektu bez wywołania żadnego settera?
3. Popraw klasę.
4. Czy pole `name` również wymaga takiej ochrony? Dlaczego?

---

# **Po zajęciach**

Po laboratorium należy potrafić wyjaśnić różnicę pomiędzy:

```text
private
package-private
protected
public
```

oraz:

```text
pole obiektu
pole statyczne
stała static final
```

Należy również rozumieć, dlaczego:

```text
private + getter + setter
```

nie jest automatycznie równoznaczne z poprawną hermetyzacją.

Przed kolejnymi zajęciami osoba studencka powinna umieć:

- ukryć stan obiektu,
- zaprojektować kontrolowany sposób jego modyfikowania,
- rozpoznać, kiedy setter jest zbędny lub szkodliwy,
- poprawnie zastosować `static`,
- zdefiniować stałą,
- przeciążyć metodę,
- przeanalizować kolejność podstawowej inicjalizacji klasy i obiektu.

Kolejne laboratorium będzie poświęcone **dziedziczeniu**.
