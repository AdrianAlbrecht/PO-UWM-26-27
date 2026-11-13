# **Programowanie Obiektowe – Java (2026Z)**

## **Lab 6: Klasy abstrakcyjne, interfejsy i kompozycja**

---

## 1. Cel zajęć

Celem laboratorium jest poznanie sposobów projektowania wspólnych kontraktów i zachowań w programowaniu obiektowym oraz świadomego wyboru pomiędzy **dziedziczeniem** a **kompozycją**.

Podczas zajęć omówione zostaną:

- klasy abstrakcyjne,
- metody abstrakcyjne,
- konstruktory i pola w klasach abstrakcyjnych,
- interfejsy,
- implementacja wielu interfejsów,
- metody `default`, `static` i `private` w interfejsach,
- rozszerzanie interfejsów,
- konflikt metod domyślnych,
- różnice pomiędzy klasą abstrakcyjną a interfejsem,
- asocjacja,
- agregacja,
- kompozycja,
- relacja **IS-A** i relacja **HAS-A**,
- zasada **composition over inheritance**,
- projektowanie kodu zależnego od kontraktu, a nie konkretnej implementacji.

Po zakończeniu zajęć osoba studencka powinna:

- potrafić utworzyć klasę abstrakcyjną,
- rozumieć, kiedy metoda powinna być abstrakcyjna,
- potrafić zdefiniować i zaimplementować interfejs,
- rozumieć różnicę pomiędzy `extends` i `implements`,
- potrafić implementować wiele interfejsów,
- rozumieć rolę metod `default`,
- potrafić dobrać klasę abstrakcyjną lub interfejs do problemu,
- rozpoznawać relacje pomiędzy obiektami,
- potrafić wybrać kompozycję zamiast niewłaściwego dziedziczenia,
- projektować klasy zależne od interfejsów.

---

# 2. Problem z poprzedniego laboratorium

Na poprzednim laboratorium mogliśmy utworzyć:

```java
public class Shape {

    public double area() {
        return 0.0;
    }
}
```

a następnie:

```java
public class Circle extends Shape {

    private double radius;

    @Override
    public double area() {
        return Math.PI
                * this.radius
                * this.radius;
    }
}
```

Taki projekt działa, ale pojawia się pytanie:

> **Jakie pole ma zwykła figura `Shape`?**

Odpowiedź:

```text
Nie wiadomo.
```

Nie istnieje sensowny ogólny wzór na pole „dowolnej figury”.

Zwracanie:

```java
0.0
```

jest jedynie sztuczną implementacją pozwalającą skompilować kod.

Możemy powiedzieć kompilatorowi:

> Każda konkretna figura musi posiadać metodę `area()`, ale klasa bazowa nie jest w stanie dostarczyć jej ogólnej implementacji.

Do tego służy **metoda abstrakcyjna**.

---

# 3. Klasa abstrakcyjna

Klasę abstrakcyjną deklarujemy:

```java
public abstract class Shape {
}
```

Klasa abstrakcyjna reprezentuje pojęcie, które może posiadać:

- wspólny stan,
- wspólne zachowanie,
- częściową implementację,
- metody, których implementację muszą dostarczyć podklasy.

Nie można bezpośrednio utworzyć jej instancji:

```java
// Shape shape = new Shape();
```

---

# 4. Metoda abstrakcyjna

Metoda abstrakcyjna deklaruje operację, ale nie posiada implementacji.

```java
public abstract class Shape {

    public abstract double area();
}
```

Nie zapisujemy:

```java
{
    ...
}
```

po deklaracji metody.

Podklasa konkretna musi ją zaimplementować:

```java
public class Circle extends Shape {

    private double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double area() {
        return Math.PI
                * this.radius
                * this.radius;
    }
}
```

---

# 5. Klasa abstrakcyjna może posiadać zwykłe metody

Klasa abstrakcyjna nie musi składać się wyłącznie z metod abstrakcyjnych.

```java
public abstract class Shape {

    private String name;

    protected Shape(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }

    public abstract double area();

    public void printInformation() {
        System.out.printf(
                "%s: %.2f%n",
                this.name,
                this.area()
        );
    }
}
```

Metoda:

```java
printInformation()
```

ma pełną implementację.

Metoda:

```java
area()
```

jest abstrakcyjna.

---

# 6. Konstruktor klasy abstrakcyjnej

Klasa abstrakcyjna może posiadać konstruktor.

Może się to początkowo wydawać dziwne, ponieważ nie można napisać:

```java
new Shape(...)
```

Konstruktor klasy abstrakcyjnej jest jednak wykonywany podczas tworzenia obiektu jej konkretnej podklasy.

```java
public abstract class Shape {

    private String name;

    protected Shape(String name) {
        System.out.println(
                "Konstruktor Shape"
        );

        this.name = name;
    }
}
```

```java
public class Circle extends Shape {

    private double radius;

    public Circle(double radius) {
        super("Koło");

        System.out.println(
                "Konstruktor Circle"
        );

        this.radius = radius;
    }
}
```

Dla:

```java
new Circle(5);
```

zostanie wykonany również konstruktor `Shape`.

---

# 7. Abstrakcyjna klasa może nie mieć metod abstrakcyjnych

Kod:

```java
public abstract class Base {

    public void show() {
        System.out.println("Base");
    }
}
```

jest poprawny.

`abstract` może oznaczać po prostu:

> Ta klasa ma służyć wyłącznie jako baza dla innych klas i nie powinna być tworzona bezpośrednio.

---

# 8. Podklasa abstrakcyjna

Jeżeli podklasa nie implementuje wszystkich odziedziczonych metod abstrakcyjnych, sama również musi być abstrakcyjna.

```java
public abstract class Animal {

    public abstract void makeSound();
}
```

```java
public abstract class Mammal
        extends Animal {

    public void breathe() {
        System.out.println(
                "Ssak oddycha."
        );
    }
}
```

Dopiero konkretna podklasa:

```java
public class Dog extends Mammal {

    @Override
    public void makeSound() {
        System.out.println("Hau!");
    }
}
```

dostarcza pełną implementację.

---

# 9. Polimorfizm z klasą abstrakcyjną

Nie możemy:

```java
// Shape shape = new Shape(...);
```

ale możemy:

```java
Shape shape = new Circle(5);
```

Możemy również:

```java
Shape[] shapes = {
        new Circle(2),
        new Rectangle(3, 4),
        new Triangle(3, 6)
};
```

i:

```java
for (Shape shape : shapes) {
    shape.printInformation();
}
```

Klasa abstrakcyjna nadal może być **typem referencji**.

---

# 10. Kiedy klasa abstrakcyjna ma sens?

Klasa abstrakcyjna jest przydatna, gdy:

- istnieje naturalna relacja **IS-A**,
- klasy są blisko ze sobą związane,
- podklasy mają wspólny stan,
- istnieje część wspólnej implementacji,
- pewne operacje powinny być obowiązkowe, ale różnią się implementacją.

Przykłady:

```text
Shape
├── Circle
├── Rectangle
└── Triangle
```

```text
Employee
├── Developer
├── Manager
└── Tester
```

---

# 11. Interfejs

Interfejs opisuje **kontrakt**.

Określa:

> Co obiekt potrafi zrobić?

Nie musi natomiast określać:

> Jak dokładnie to robi?

Przykład:

```java
public interface Printable {

    void print();
}
```

Klasa implementująca interfejs:

```java
public class Report
        implements Printable {

    @Override
    public void print() {
        System.out.println(
                "Drukowanie raportu."
        );
    }
}
```

---

# 12. `implements`

Klasa:

```java
extends
```

inną klasę.

Interfejs:

```java
implements
```

klasa.

Przykład:

```java
public class Invoice
        implements Printable {
}
```

Jeżeli klasa nie jest abstrakcyjna, musi dostarczyć implementacje wymaganych metod interfejsu.

---

# 13. Metody interfejsu

Klasyczna metoda bez implementacji:

```java
public interface Printable {

    void print();
}
```

jest w praktyce:

```java
public abstract void print();
```

Metody abstrakcyjne interfejsu są domyślnie:

```text
public abstract
```

Dlatego implementacja również musi być `public`:

```java
@Override
public void print() {
}
```

Nie można zmniejszyć poziomu dostępu.

---

# 14. Pola interfejsu

Pola zadeklarowane w interfejsie są domyślnie:

```text
public static final
```

Przykład:

```java
public interface Limits {

    int MAX_RETRIES = 3;
}
```

jest równoważne:

```java
public static final int MAX_RETRIES = 3;
```

Interfejs nie przechowuje zwykłego stanu konkretnej instancji.

Jeżeli potrzebujemy wspólnego stanu obiektów, bardziej naturalnym kandydatem może być klasa abstrakcyjna.

---

# 15. Interfejs jako typ

Podobnie jak klasa bazowa, interfejs może być typem referencji.

```java
Printable printable =
        new Report();
```

Możemy utworzyć:

```java
Printable[] elements = {
        new Report(),
        new Invoice(),
        new Ticket()
};
```

i:

```java
for (Printable element : elements) {
    element.print();
}
```

Kod nie musi znać konkretnych klas.

---

# 16. Wiele interfejsów

Java nie pozwala klasie dziedziczyć po wielu klasach:

```java
// class Example extends A, B
```

ale klasa może implementować wiele interfejsów.

```java
public interface Printable {

    void print();
}
```

```java
public interface Exportable {

    void export();
}
```

```java
public class Report
        implements Printable, Exportable {

    @Override
    public void print() {
        System.out.println(
                "Drukowanie."
        );
    }

    @Override
    public void export() {
        System.out.println(
                "Eksport."
        );
    }
}
```

---

# 17. Klasa może rozszerzać klasę i implementować interfejsy

Przykład:

```java
public class PdfReport
        extends Report
        implements Printable,
                   Exportable,
                   Savable {
}
```

Kolejność:

```text
extends ...
implements ...
```

---

# 18. Metody `default`

Interfejs może posiadać metodę z domyślną implementacją:

```java
public interface Printable {

    void print();

    default void printHeader() {
        System.out.println(
                "=== DOCUMENT ==="
        );
    }
}
```

Klasa implementująca może użyć gotowej implementacji:

```java
report.printHeader();
```

albo ją nadpisać:

```java
@Override
public void printHeader() {
    System.out.println(
            "=== REPORT ==="
    );
}
```

---

# 19. Po co istnieją metody `default`?

Jednym z powodów jest możliwość rozwijania interfejsów bez natychmiastowego psucia wszystkich istniejących implementacji.

Jeżeli interfejs otrzyma nową zwykłą metodę abstrakcyjną:

```java
void newMethod();
```

wszystkie istniejące klasy muszą ją zaimplementować.

Metoda:

```java
default void newMethod() {
    ...
}
```

dostarcza zachowanie domyślne.

---

# 20. Metody `static` w interfejsie

Interfejs może posiadać metodę statyczną:

```java
public interface Validator {

    static boolean isNotEmpty(
            String text
    ) {
        return text != null
                && !text.isBlank();
    }
}
```

Wywołanie:

```java
Validator.isNotEmpty("Java");
```

Metoda statyczna należy do samego interfejsu.

Nie jest metodą instancji implementujących go klas.

---

# 21. Metody `private` w interfejsie

Współczesna Java pozwala interfejsom posiadać prywatne metody pomocnicze.

```java
public interface Logger {

    default void info(String message) {
        print("INFO", message);
    }

    default void error(String message) {
        print("ERROR", message);
    }

    private void print(
            String level,
            String message
    ) {
        System.out.println(
                "[" + level + "] "
                + message
        );
    }
}
```

Prywatna metoda może ograniczyć duplikację wewnątrz metod `default`.

---

# 22. Interfejs rozszerzający interfejs

Interfejs może rozszerzać inny interfejs:

```java
public interface Readable {

    String read();
}
```

```java
public interface Editable
        extends Readable {

    void edit(String content);
}
```

Klasa implementująca:

```java
public class Document
        implements Editable {

    @Override
    public String read() {
        return "...";
    }

    @Override
    public void edit(String content) {
    }
}
```

---

# 23. Interfejs może rozszerzać wiele interfejsów

```java
public interface Readable {

    void read();
}
```

```java
public interface Writable {

    void write();
}
```

```java
public interface ReadWrite
        extends Readable, Writable {
}
```

Interfejsy pozwalają więc tworzyć wiele niezależnych kontraktów zachowania.

---

# 24. Konflikt metod `default`

Załóżmy:

```java
public interface A {

    default void hello() {
        System.out.println("A");
    }
}
```

```java
public interface B {

    default void hello() {
        System.out.println("B");
    }
}
```

Klasa:

```java
public class Example
        implements A, B {
}
```

nie skompiluje się.

Java nie wie, którą wersję:

```java
hello()
```

wybrać.

Musimy rozwiązać konflikt:

```java
public class Example
        implements A, B {

    @Override
    public void hello() {
        A.super.hello();
    }
}
```

Można również dostarczyć całkowicie własną implementację.

---

# 25. Klasa ma pierwszeństwo przed metodą `default`

Jeżeli klasa bazowa dostarcza metodę:

```java
hello()
```

a interfejs posiada:

```java
default void hello()
```

to zwykła metoda odziedziczona z klasy ma pierwszeństwo.

Jest to jedna z reguł rozwiązujących potencjalne konflikty pomiędzy dziedziczeniem klas i implementacją interfejsów.

---

# 26. Klasa abstrakcyjna a interfejs

| Cecha | Klasa abstrakcyjna | Interfejs |
|---|---|---|
| Tworzenie obiektu bezpośrednio | ❌ | ❌ |
| Pola instancji | ✅ | ❌ |
| Konstruktor | ✅ | ❌ |
| Zwykłe metody instancji | ✅ | `default` |
| Metody abstrakcyjne | ✅ | ✅ |
| Metody statyczne | ✅ | ✅ |
| Implementacja wielu naraz | ❌ | ✅ |
| Wspólny stan | ✅ | ❌ |
| Typowe zastosowanie | wspólna baza i częściowa implementacja | kontrakt/zdolność |

---

# 27. Kiedy wybrać klasę abstrakcyjną?

Rozważ klasę abstrakcyjną, gdy:

- istnieje silna relacja IS-A,
- obiekty mają wspólny stan,
- wiele metod posiada wspólną implementację,
- hierarchia opisuje blisko związane pojęcia.

Przykład:

```text
Shape
Employee
Vehicle
```

---

# 28. Kiedy wybrać interfejs?

Rozważ interfejs, gdy chcesz opisać:

- zdolność,
- rolę,
- kontrakt,
- zachowanie możliwe do realizowania przez niezwiązane klasy.

Przykłady:

```text
Printable
Comparable
Runnable
Validator
Exportable
Payable
```

Zarówno:

```text
Invoice
```

jak i:

```text
Ticket
```

mogą być `Printable`, mimo że nie muszą mieć wspólnej sensownej klasy bazowej poza `Object`.

---

# 29. Klasa abstrakcyjna i interfejs jednocześnie

Bardzo często stosujemy oba mechanizmy razem.

```java
public abstract class Document {

    private String title;

    protected Document(
            String title
    ) {
        this.title = title;
    }

    public String getTitle() {
        return this.title;
    }

    public abstract String content();
}
```

```java
public interface Printable {

    void print();
}
```

```java
public class Report
        extends Document
        implements Printable {

    public Report(String title) {
        super(title);
    }

    @Override
    public String content() {
        return "Treść raportu";
    }

    @Override
    public void print() {
        System.out.println(
                this.content()
        );
    }
}
```

Klasa abstrakcyjna przechowuje wspólny stan, a interfejs dodaje niezależną zdolność.

---

# 30. Związki pomiędzy klasami

Klasy nie istnieją w izolacji.

Najczęściej spotkamy:

- dziedziczenie,
- asocjację,
- agregację,
- kompozycję.

W praktycznym projektowaniu szczególnie ważne jest rozróżnienie:

```text
IS-A
```

oraz:

```text
HAS-A
```

---

# 31. Dziedziczenie – IS-A

```text
Dog IS-A Animal
```

```text
Manager IS-A Employee
```

Kod:

```java
public class Dog
        extends Animal {
}
```

Dziedziczenie opisuje **rodzaj** obiektu.

---

# 32. Asocjacja – zna/używa

Asocjacja oznacza ogólną relację pomiędzy obiektami.

Przykład:

```java
public class Teacher {

    public void evaluate(
            Student student
    ) {
        ...
    }
}
```

`Teacher` zna lub używa `Student`, ale żaden z obiektów nie musi być właścicielem drugiego.

---

# 33. Agregacja – HAS-A, ale obiekt może istnieć niezależnie

Przykład:

```java
public class Team {

    private List<Player> players;

    public Team(
            List<Player> players
    ) {
        this.players = players;
    }
}
```

Zawodnik może istnieć niezależnie od konkretnego zespołu i może potencjalnie zmienić zespół.

Agregację można traktować jako słabszą relację „całość–część”.

---

# 34. Kompozycja – silne HAS-A

Kompozycja oznacza, że obiekt zawiera inne obiekty i przejmuje odpowiedzialność za ich utworzenie oraz używanie jako swoich części.

Przykład:

```java
public class Engine {

    public void start() {
        System.out.println(
                "Silnik uruchomiony."
        );
    }
}
```

```java
public class Car {

    private final Engine engine;

    public Car() {
        this.engine = new Engine();
    }

    public void start() {
        this.engine.start();

        System.out.println(
                "Samochód gotowy."
        );
    }
}
```

Relacja:

```text
Car HAS-A Engine
```

jest naturalniejsza niż:

```text
Car IS-A Engine
```

---

## 34.1. Uwaga o „cyklu życia”

W modelowaniu kompozycja zwykle oznacza, że część jest własnością całości i nie jest niezależnie współdzielona.

W Javie fizyczne usuwanie obiektów nadal wykonuje Garbage Collector. Nie należy rozumieć kompozycji jako ręcznego „niszczenia” obiektu części przez obiekt całości.

Chodzi przede wszystkim o **własność i odpowiedzialność w modelu**.

---

# 35. Kompozycja zamiast dziedziczenia

Rozważmy zły pomysł:

```java
public class Car
        extends Engine {
}
```

Czy samochód jest silnikiem?

```text
Nie.
```

Samochód **ma silnik**.

Lepszy projekt:

```java
public class Car {

    private Engine engine;
}
```

---

# 36. „Composition over inheritance”

Popularna zasada projektowa mówi:

> **Preferuj kompozycję nad dziedziczeniem, jeżeli nie występuje rzeczywista i stabilna relacja IS-A.**

Nie oznacza to:

> Nigdy nie używaj dziedziczenia.

Dziedziczenie jest bardzo przydatne, gdy opisuje rzeczywistą hierarchię typów.

Problem pojawia się wtedy, gdy jest używane wyłącznie po to, aby „odziedziczyć trochę kodu”.

---

# 37. Problem ze sztywnym dziedziczeniem

Załóżmy:

```java
public class Bird {

    public void fly() {
    }
}
```

i:

```java
public class Penguin
        extends Bird {
}
```

Jeżeli wszystkie `Bird` posiadają:

```java
fly()
```

to `Penguin` dziedziczy zachowanie, którego nie powinien posiadać.

Można próbować:

```java
@Override
public void fly() {
    throw new UnsupportedOperationException();
}
```

ale jest to sygnał, że kontrakt klasy bazowej został źle zaprojektowany.

---

# 38. Interfejs jako niezależna zdolność

Lepszy projekt:

```java
public abstract class Bird {

    public abstract void eat();
}
```

```java
public interface Flyable {

    void fly();
}
```

```java
public class Eagle
        extends Bird
        implements Flyable {

    @Override
    public void eat() {
    }

    @Override
    public void fly() {
    }
}
```

```java
public class Penguin
        extends Bird {

    @Override
    public void eat() {
    }
}
```

Latanie staje się **zdolnością**, a nie wymuszonym zachowaniem każdego ptaka.

---

# 39. Kompozycja zachowania

Interfejs może również umożliwić łatwą wymianę zachowania.

```java
public interface PaymentMethod {

    void pay(double amount);
}
```

```java
public class CardPayment
        implements PaymentMethod {

    @Override
    public void pay(double amount) {
        System.out.println(
                "Płatność kartą: "
                + amount
        );
    }
}
```

```java
public class OrderService {

    private PaymentMethod paymentMethod;

    public OrderService(
            PaymentMethod paymentMethod
    ) {
        this.paymentMethod =
                paymentMethod;
    }

    public void checkout(double amount) {
        this.paymentMethod.pay(amount);
    }
}
```

Użycie:

```java
OrderService service =
        new OrderService(
                new CardPayment()
        );
```

`OrderService` nie dziedziczy po sposobie płatności.

**Posiada** obiekt realizujący kontrakt `PaymentMethod`.

---

# 40. Zależność od abstrakcji

Zwróć uwagę na konstruktor:

```java
public OrderService(
        PaymentMethod paymentMethod
)
```

Nie przyjmuje:

```java
CardPayment
```

tylko:

```java
PaymentMethod
```

Dzięki temu możemy przekazać:

```java
new CardPayment()
new BlikPayment()
new TransferPayment()
```

bez zmiany klasy `OrderService`.

To przykład projektowania zależnego od **kontraktu**, a nie konkretnej implementacji.

---

# 41. Typowe błędy

## 41.1. Tworzenie obiektu klasy abstrakcyjnej

```java
// Shape shape = new Shape();
```

Nie jest dozwolone.

---

## 41.2. Brak implementacji metody abstrakcyjnej

Jeżeli:

```java
class Circle extends Shape
```

nie implementuje wymaganej metody abstrakcyjnej, klasa `Circle` również musi zostać oznaczona jako `abstract`.

---

## 41.3. Zbyt wąski poziom dostępu przy implementacji interfejsu

Interfejs:

```java
void print();
```

Implementacja musi być `public`:

```java
@Override
public void print() {
}
```

Niepoprawne:

```java
// void print() {
// }
```

---

## 41.4. Traktowanie interfejsu jak miejsca na stan obiektu

Interfejs nie służy do przechowywania zwykłych pól instancji.

Jeżeli wiele obiektów ma wspólny stan, rozważ klasę abstrakcyjną albo kompozycję.

---

## 41.5. Nadużywanie dziedziczenia

```java
class House extends Address
```

Dom nie jest adresem.

Dom **ma adres**.

---

## 41.6. Interfejs „dla jednego gettera”

Nie należy tworzyć interfejsów mechanicznie.

Interfejs powinien reprezentować sensowny kontrakt lub rolę, a nie być dodawany tylko dlatego, że „interfejsy trzeba gdzieś użyć”.

---

# **Zadania**

## Zadanie 1 – popraw `Shape` z Lab 5

Przebuduj poprzednią klasę:

```text
Shape
```

na klasę abstrakcyjną.

Powinna posiadać:

```java
public abstract double area();
```

oraz zwykłą metodę:

```java
public void printInformation()
```

Utwórz:

```text
Circle
Rectangle
Triangle
```

i zaimplementuj `area()`.

Następnie:

```java
Shape[] shapes
```

przetwórz w jednej pętli.

---

## Zadanie 2 – wspólny stan klasy abstrakcyjnej

Rozbuduj `Shape` o:

```text
name
```

Pole ma być prywatne.

Dodaj:

- konstruktor klasy abstrakcyjnej,
- getter,
- wykorzystanie `name` w `printInformation()`.

Sprawdź kolejność wywołań konstruktorów.

---

## Zadanie 3 – `Employee`

Utwórz abstrakcyjną klasę:

```text
Employee
```

z polami:

```text
name
baseSalary
```

oraz:

```java
public abstract double calculateSalary();
```

Dodaj zwykłą metodę:

```java
public void printPayslip()
```

wykorzystującą:

```java
calculateSalary()
```

Utwórz:

```text
Developer
Manager
Salesperson
```

z różnymi sposobami obliczania wynagrodzenia.

---

## Zadanie 4 – interfejs `Printable`

Utwórz:

```java
public interface Printable {

    void print();
}
```

oraz klasy:

```text
Invoice
Report
Ticket
```

implementujące interfejs.

Utwórz:

```java
Printable[] elements
```

i wywołaj `print()` w jednej pętli.

---

## Zadanie 5 – kilka interfejsów

Utwórz:

```text
Printable
Exportable
Savable
```

oraz klasę:

```text
Report
```

implementującą wszystkie trzy.

Każdy interfejs powinien wymagać co najmniej jednej sensownej metody.

Przypisz ten sam obiekt kolejno do zmiennych:

```java
Printable printable
Exportable exportable
Savable savable
```

Sprawdź, jakie metody są dostępne przez każdy z typów referencji.

---

## Zadanie 6 – metoda `default`

Dodaj do:

```text
Printable
```

domyślną metodę:

```java
default void printHeader()
```

Klasa `Report` ma korzystać z implementacji domyślnej.

Klasa `Invoice` ma ją nadpisać.

Sprawdź zachowanie obu obiektów.

---

## Zadanie 7 – konflikt `default`

Utwórz:

```text
A
B
```

zawierające identyczną metodę:

```java
default void hello()
```

Następnie utwórz klasę:

```text
C
```

implementującą oba interfejsy.

1. Sprawdź komunikat kompilatora.
2. Rozwiąż konflikt.
3. Wywołaj jawnie jedną z metod za pomocą:

```java
A.super.hello();
```

---

## Zadanie 8 – interfejs rozszerzający interfejs

Utwórz:

```text
Readable
Writable
ReadWrite
```

gdzie:

```java
ReadWrite
```

rozszerza:

```java
Readable
Writable
```

Utwórz klasę:

```text
TextDocument
```

implementującą `ReadWrite`.

---

## Zadanie 9 – ptaki

Otrzymujesz model:

```java
class Bird {

    public void fly() {
    }
}
```

oraz:

```java
class Penguin extends Bird {
}
```

Przebuduj go tak, aby:

- każdy ptak miał wspólne zachowanie związane np. z jedzeniem,
- tylko ptaki potrafiące latać implementowały `Flyable`.

Utwórz:

```text
Eagle
Sparrow
Penguin
```

Wyjaśnij, dlaczego nowy projekt jest lepszy.

---

## Zadanie 10 – dziedziczenie czy kompozycja?

Dla każdej relacji zdecyduj:

```text
dziedziczenie
kompozycja/agregacja
inna asocjacja
```

### Relacje:

```text
Car – Engine
```

```text
Dog – Animal
```

```text
House – Address
```

```text
Manager – Employee
```

```text
Computer – Processor
```

```text
Student – University
```

```text
Book – Author
```

```text
Order – PaymentMethod
```

Uzasadnij każdą odpowiedź za pomocą:

```text
IS-A
```

lub:

```text
HAS-A / USES-A
```

---

## Zadanie 11 – samochód przez kompozycję

Utwórz:

```text
Engine
Gearbox
Car
```

`Car` powinien **posiadać**:

```java
Engine engine;
Gearbox gearbox;
```

Nie używaj:

```java
extends Engine
```

ani:

```java
extends Gearbox
```

Dodaj:

```java
start()
```

w `Car`, która korzysta z obiektu `Engine`.

Następnie odpowiedz:

> Dlaczego dziedziczenie byłoby tutaj błędem modelowania?

---

## Zadanie 12 – strategia płatności przez kompozycję

Utwórz interfejs:

```java
PaymentMethod
```

z metodą:

```java
void pay(double amount);
```

Implementacje:

```text
CardPayment
BlikPayment
TransferPayment
```

Utwórz klasę:

```text
OrderService
```

posiadającą pole:

```java
PaymentMethod paymentMethod;
```

oraz metodę:

```java
checkout(double amount)
```

W konstruktorze `OrderService` przekazuj konkretną implementację.

Sprawdź działanie z każdą z trzech implementacji bez zmieniania kodu `OrderService`.

---

## Zadanie 13 – refaktoryzacja złego dziedziczenia

Otrzymujesz:

```java
public class Database {

    public void save(String data) {
        System.out.println(
                "Zapis do bazy: " + data
        );
    }
}
```

```java
public class ReportService
        extends Database {

    public void generateReport() {
        String report = "Raport";

        this.save(report);
    }
}
```

Odpowiedz:

1. Czy `ReportService IS-A Database`?
2. Dlaczego dziedziczenie zostało użyte wyłącznie jako sposób ponownego wykorzystania kodu?
3. Przebuduj rozwiązanie przez kompozycję.

Docelowo:

```java
ReportService
```

powinien **posiadać** zależność odpowiadającą za zapis.

---

## Zadanie 14 – zależność od interfejsu

Rozbuduj poprzednie zadanie.

Zamiast:

```java
Database
```

utwórz:

```java
Storage
```

jako interfejs:

```java
void save(String data);
```

Implementacje:

```text
DatabaseStorage
FileStorage
ConsoleStorage
```

`ReportService` ma zależeć od:

```java
Storage
```

a nie od konkretnej klasy.

Sprawdź możliwość zmiany sposobu zapisu bez modyfikowania `ReportService`.

---

## Zadanie 15 – klasa abstrakcyjna czy interfejs?

Dla każdego przypadku wybierz bardziej naturalne rozwiązanie:

```text
Shape
```

```text
Printable
```

```text
Employee
```

```text
Comparable
```

```text
Vehicle
```

```text
Encryptable
```

```text
NotificationSender
```

```text
Account
```

Dla każdej odpowiedzi uzasadnij:

- czy potrzebny jest wspólny stan,
- czy istnieje naturalne IS-A,
- czy kontrakt może dotyczyć wielu niezwiązanych klas,
- czy potrzebna jest częściowa implementacja.

---

# **Zadanie dodatkowe – przebudowa mini-systemu**

Zaprojektuj prosty system wysyłania powiadomień.

Interfejs:

```java
NotificationSender
```

powinien posiadać:

```java
void send(String message);
```

Implementacje:

```text
EmailSender
SmsSender
ConsoleSender
```

Klasa:

```text
NotificationService
```

ma posiadać:

```java
NotificationSender sender;
```

i delegować wysyłanie do przekazanego obiektu.

Następnie utwórz abstrakcyjną klasę:

```text
Notification
```

przechowującą:

```text
recipient
message
```

oraz abstrakcyjną metodę:

```java
String format();
```

Utwórz co najmniej dwie konkretne podklasy.

W jednym rozwiązaniu wykorzystaj więc jednocześnie:

- klasę abstrakcyjną,
- interfejs,
- polimorfizm,
- kompozycję.

Na końcu narysuj uproszczony diagram zależności klas.

---

# **Po zajęciach**

Po laboratorium należy potrafić wyjaśnić różnicę pomiędzy:

```text
extends
implements
```

oraz:

```text
klasą zwykłą
klasą abstrakcyjną
interfejsem
```

Należy rozumieć relacje:

```text
IS-A
HAS-A
USES-A
```

oraz potrafić odpowiedzieć:

> Czy dana zależność powinna być dziedziczeniem, czy obiekt powinien raczej posiadać inny obiekt?

Należy również rozumieć, że:

```text
interfejs = kontrakt / rola / zdolność
```

natomiast:

```text
klasa abstrakcyjna = wspólna baza + stan + częściowa implementacja
```

Nie jest to reguła absolutna, ale dobry punkt wyjścia do projektowania.

Kolejne laboratorium będzie poświęcone **klasom zagnieżdżonym, wewnętrznym, lokalnym i anonimowym oraz zadaniom przekrojowym przed Kolokwium I**.
