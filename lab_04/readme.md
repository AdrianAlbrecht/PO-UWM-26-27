# **Programowanie Obiektowe – Java (2026Z)**

## **Lab 4: Dziedziczenie**

---

### ⚠️ Komunikat bezpieczeństwa — Java (22.10.2026)

21 października 2026 r. firma Oracle opublikowała ostrzeżenie dotyczące **krytycznej podatności CVE-2026-91403 (CVSS 9.1/10)** w środowisku Java.

Podatność dotyczy mechanizmów bezpieczeństwa połączeń i certyfikatów cyfrowych (JSSE). Może umożliwić nieuprawniony dostęp do poufnych danych oraz ich modyfikację, w określonych warunkach również bez uwierzytelnienia.

**Zalecenia:**
1. Sprawdź zainstalowaną wersję JDK poleceniem `java -version`.
2. Zainstaluj najnowszą aktualizację bezpieczeństwa JDK z zaufanego źródła.
3. Upewnij się, że środowisko IDE (np. IntelliJ IDEA) korzysta z aktualnego JDK.
4. Nie wyłączaj weryfikacji certyfikatów SSL/TLS w celu obejścia problemów z połączeniem.

Podatność nie oznacza, że samodzielnie uruchamiane programy laboratoryjne są automatycznie zagrożone. Stanowi jednak przykład tego, dlaczego **aktualizowanie środowiska uruchomieniowego jest równie ważne jak bezpieczeństwo kodu aplikacji**.

**Źródło:** [Oracle Security Alert — CVE-2026-91403](https://www.oracle.com/security-alerts/alert-cve-2026-91403.html)

## 1. Cel zajęć

Celem laboratorium jest praktyczne poznanie mechanizmu **dziedziczenia klas** w języku Java.

Podczas zajęć omówione zostaną:

- relacja **IS-A**,
- słowo kluczowe `extends`,
- klasa bazowa i klasa pochodna,
- dziedziczenie pól i metod,
- dostęp do składowych klasy bazowej,
- `protected` w kontekście dziedziczenia,
- konstruktory w hierarchii klas,
- słowo kluczowe `super`,
- nadpisywanie metod i `@Override`,
- wielopoziomowe dziedziczenie,
- klasa `Object`,
- `final` w kontekście klas i metod.

Po zakończeniu zajęć osoba studencka powinna:

- potrafić rozpoznać, kiedy relacja dziedziczenia ma sens,
- utworzyć klasę pochodną za pomocą `extends`,
- poprawnie wywołać konstruktor klasy bazowej,
- rozumieć kolejność wykonywania konstruktorów,
- poprawnie nadpisywać metody,
- rozumieć znaczenie `super`,
- znać podstawową rolę klasy `Object`,
- rozumieć działanie `final` dla klas i metod,
- potrafić zaprojektować prostą hierarchię klas.

> **Uwaga:** klasy abstrakcyjne i interfejsy zostaną omówione na późniejszym laboratorium.  
> Polimorfizm, wiązanie dynamiczne i rzutowanie typów będą głównym tematem Lab 5.

---

# 2. Dziedziczenie

**Dziedziczenie** (*inheritance*) pozwala utworzyć nową klasę na bazie klasy już istniejącej.

Klasa istniejąca nazywana jest między innymi:

```text
klasą bazową
nadklasą
superklasą
```

Nowa klasa:

```text
klasą pochodną
podklasą
subklasą
```

Przykład:

```java
public class Animal {

    public void breathe() {
        System.out.println("Zwierzę oddycha.");
    }
}
```

oraz:

```java
public class Dog extends Animal {

    public void bark() {
        System.out.println("Hau!");
    }
}
```

Klasa:

```text
Dog
```

rozszerza:

```text
Animal
```

---

# 3. Relacja IS-A

Dziedziczenie powinno reprezentować sensowną relację:

```text
X jest rodzajem Y
```

Przykłady:

```text
Dog IS-A Animal
Student IS-A Person
ElectricCar IS-A Car
Manager IS-A Employee
```

Czyli:

```text
Pies jest rodzajem zwierzęcia.
Student jest rodzajem osoby.
Samochód elektryczny jest rodzajem samochodu.
```

Jeżeli relacja:

```text
X jest Y
```

nie ma sensu, dziedziczenie prawdopodobnie nie jest dobrym rozwiązaniem.

Przykład:

```text
Engine IS-A Car
```

jest błędną relacją.

Silnik **nie jest rodzajem samochodu**.

Takie przypadki będą później omawiane przy porównaniu dziedziczenia z kompozycją.

---

# 4. `extends`

Dziedziczenie klas deklarujemy za pomocą:

```java
extends
```

Przykład:

```java
public class Person {

    protected String firstName;

    public Person(String firstName) {
        this.firstName = firstName;
    }

    public void introduceYourself() {
        System.out.println(
                "Mam na imię " + this.firstName
        );
    }
}
```

Podklasa:

```java
public class Student extends Person {

    private int albumNumber;

    public Student(
            String firstName,
            int albumNumber
    ) {
        super(firstName);
        this.albumNumber = albumNumber;
    }

    public void showAlbumNumber() {
        System.out.println(
                "Numer albumu: " + this.albumNumber
        );
    }
}
```

Obiekt `Student` może korzystać zarówno z metod zdefiniowanych w `Student`, jak i z dostępnych metod odziedziczonych z `Person`.

---

# 5. Co dziedziczy podklasa?

Podklasa dziedziczy zachowanie i stan wynikający z klasy bazowej, ale **dostęp do poszczególnych składowych zależy od ich modyfikatorów dostępu**.

Ważne:

> Pole `private` klasy bazowej nadal stanowi część stanu obiektu klasy pochodnej, ale kod podklasy nie może odwoływać się do niego bezpośrednio.

Przykład:

```java
public class Person {

    private String firstName;

    public Person(String firstName) {
        this.firstName = firstName;
    }

    public String getFirstName() {
        return this.firstName;
    }
}
```

Podklasa:

```java
public class Student extends Person {

    public Student(String firstName) {
        super(firstName);
    }

    public void showName() {
        // System.out.println(this.firstName); // niedostępne

        System.out.println(
                this.getFirstName()
        );
    }
}
```

Pole istnieje w obiekcie, ale dostęp do niego odbywa się przez udostępniony interfejs klasy bazowej.

---

# 6. Modyfikatory dostępu a dziedziczenie

Przypomnienie:

| Modyfikator | Ta sama klasa | Ten sam pakiet | Podklasa w innym pakiecie | Inna klasa w innym pakiecie |
|---|:---:|:---:|:---:|:---:|
| `private` | ✅ | ❌ | ❌ | ❌ |
| package-private | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅* | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

\* `protected` poza pakietem działa poprzez relację dziedziczenia.

---

## 6.1. `protected`

Przykład:

```java
package example.base;

public class Person {

    protected String lastName;
}
```

Podklasa w innym pakiecie:

```java
package example.student;

import example.base.Person;

public class Student extends Person {

    public void showLastName() {
        System.out.println(this.lastName);
    }
}
```

To jest poprawne.

Nie oznacza to jednak, że `protected` staje się odpowiednikiem `public`.

Kod niezwiązany z dziedziczeniem nie uzyskuje automatycznie dostępu do pola.

---

## 6.2. Czy warto używać `protected` dla pól?

Można, ale należy robić to świadomie.

Często bezpieczniejszym rozwiązaniem pozostaje:

```java
private
```

w klasie bazowej oraz odpowiednio zaprojektowane:

```java
protected
```

lub:

```java
public
```

metody dostępu.

Przykład:

```java
public class Person {

    private String lastName;

    protected String getLastName() {
        return this.lastName;
    }
}
```

Podklasa korzysta wtedy z kontrolowanego sposobu dostępu.

---

# 7. Konstruktor klasy bazowej

Konstruktor **nie jest dziedziczony**.

Podczas tworzenia obiektu klasy pochodnej musi jednak zostać wykonany konstruktor klasy bazowej.

Przykład:

```java
public class Person {

    private String name;

    public Person(String name) {
        System.out.println(
                "Konstruktor Person"
        );

        this.name = name;
    }
}
```

Podklasa:

```java
public class Student extends Person {

    private int albumNumber;

    public Student(
            String name,
            int albumNumber
    ) {
        super(name);

        System.out.println(
                "Konstruktor Student"
        );

        this.albumNumber = albumNumber;
    }
}
```

Dla:

```java
new Student("Anna", 123456);
```

wynik będzie:

```text
Konstruktor Person
Konstruktor Student
```

Najpierw inicjalizowana jest część obiektu pochodząca z klasy bazowej.

---

# 8. `super(...)`

Do jawnego wywołania konstruktora klasy bazowej używamy:

```java
super(...)
```

Przykład:

```java
public Student(
        String name,
        int albumNumber
) {
    super(name);
    this.albumNumber = albumNumber;
}
```

Wywołanie `super(...)` musi być pierwszą instrukcją konstruktora.

---

# 9. Niejawne `super()`

Jeżeli konstruktor podklasy nie zawiera jawnego wywołania:

```java
super(...)
```

kompilator próbuje wstawić:

```java
super();
```

Przykład:

```java
public class Animal {

    public Animal() {
        System.out.println("Animal()");
    }
}
```

```java
public class Dog extends Animal {

    public Dog() {
        // super();  <- wstawione niejawnie
        System.out.println("Dog()");
    }
}
```

---

## 9.1. Typowy problem

Jeżeli klasa bazowa posiada tylko:

```java
public Person(String name) {
}
```

i nie posiada:

```java
public Person() {
}
```

to podklasa musi jawnie wywołać odpowiedni konstruktor:

```java
public Student(String name) {
    super(name);
}
```

W przeciwnym przypadku program się nie skompiluje.

---

# 10. `super` – dostęp do implementacji klasy bazowej

`super` pozwala także odwołać się do metody klasy bazowej.

```java
public class Animal {

    public void makeSound() {
        System.out.println(
                "Zwierzę wydaje dźwięk."
        );
    }
}
```

```java
public class Dog extends Animal {

    @Override
    public void makeSound() {
        super.makeSound();

        System.out.println("Hau!");
    }
}
```

Wynik:

```text
Zwierzę wydaje dźwięk.
Hau!
```

---

# 11. Ukrywanie pola a `super`

Możliwe jest zadeklarowanie pola o tej samej nazwie w klasie bazowej i pochodnej.

```java
public class Parent {

    protected String name = "Parent";
}
```

```java
public class Child extends Parent {

    protected String name = "Child";

    public void showNames() {
        System.out.println(this.name);
        System.out.println(super.name);
    }
}
```

Wynik:

```text
Child
Parent
```

Taka konstrukcja jest możliwa, ale zwykle może utrudniać czytelność kodu.

> Pola nie są polimorficzne w taki sam sposób jak nadpisywane metody.

---

# 12. Nadpisywanie metod

Podklasa może dostarczyć własną implementację odziedziczonej metody.

```java
public class Animal {

    public void makeSound() {
        System.out.println(
                "Zwierzę wydaje dźwięk."
        );
    }
}
```

```java
public class Dog extends Animal {

    @Override
    public void makeSound() {
        System.out.println("Hau!");
    }
}
```

Adnotacja:

```java
@Override
```

informuje kompilator, że intencją programisty jest nadpisanie metody z klasy bazowej.

---

# 13. Dlaczego warto używać `@Override`?

Rozważmy literówkę:

```java
public class Dog extends Animal {

    public void makeSounds() {
        System.out.println("Hau!");
    }
}
```

To jest nowa metoda, a nie nadpisanie:

```java
makeSound()
```

Jeżeli użyjemy:

```java
@Override
public void makeSounds() {
}
```

kompilator zgłosi błąd.

Dzięki temu `@Override` pomaga wykrywać pomyłki.

---

# 14. Zasady nadpisywania

Przy nadpisywaniu:

- nazwa metody musi odpowiadać metodzie z klasy bazowej,
- lista parametrów musi odpowiadać metodzie z klasy bazowej,
- metoda nie może mieć bardziej restrykcyjnego poziomu dostępu,
- typ zwracany musi być zgodny z metodą bazową,
- metoda `final` nie może zostać nadpisana.

Przykład:

```java
class Parent {

    protected void show() {
    }
}
```

Podklasa może zwiększyć dostępność:

```java
class Child extends Parent {

    @Override
    public void show() {
    }
}
```

ale nie może jej zmniejszyć:

```java
// @Override
// private void show() {
// }
```

---

# 15. Typ zwracany kowariantny

Metoda nadpisująca może zwracać bardziej szczegółowy typ referencyjny.

Przykład:

```java
class Animal {

    public Animal create() {
        return new Animal();
    }
}
```

```java
class Dog extends Animal {

    @Override
    public Dog create() {
        return new Dog();
    }
}
```

`Dog` jest podtypem `Animal`, dlatego taka zmiana typu zwracanego jest dopuszczalna.

---

# 16. Przeciążanie a nadpisywanie

Przypomnienie:

## Przeciążanie – overloading

Ta sama nazwa, różne parametry:

```java
calculate(int x)

calculate(int x, int y)
```

## Nadpisywanie – overriding

Ta sama sygnatura w klasie pochodnej:

```java
class Animal {

    public void makeSound() {
    }
}
```

```java
class Dog extends Animal {

    @Override
    public void makeSound() {
    }
}
```

To dwa różne mechanizmy.

---

# 17. Dziedziczenie wielopoziomowe

Klasa może dziedziczyć po klasie, która sama dziedziczy po innej klasie.

```java
public class Vehicle {

    public void start() {
        System.out.println("Pojazd rusza.");
    }
}
```

```java
public class Car extends Vehicle {

    public void stop() {
        System.out.println(
                "Samochód się zatrzymuje."
        );
    }
}
```

```java
public class ElectricCar extends Car {

    public void charge() {
        System.out.println(
                "Ładowanie baterii."
        );
    }
}
```

Obiekt:

```java
ElectricCar car = new ElectricCar();
```

może korzystać z:

```java
car.start();
car.stop();
car.charge();
```

---

# 18. Java nie pozwala na wielokrotne dziedziczenie klas

Nie można napisać:

```java
// class C extends A, B {
// }
```

Klasa może bezpośrednio dziedziczyć tylko po **jednej klasie**.

Java pozwala natomiast implementować wiele interfejsów.

Interfejsy będą omawiane później.

---

# 19. Klasa `Object`

Każda klasa w Javie ostatecznie dziedziczy po:

```java
java.lang.Object
```

Jeżeli napiszemy:

```java
public class Person {
}
```

to można myśleć o tym jak o:

```java
public class Person extends Object {
}
```

`Object` znajduje się na szczycie hierarchii klas Javy.

---

# 20. Wybrane metody `Object`

Najczęściej spotykane metody odziedziczone z `Object` to między innymi:

```java
toString()
equals(Object obj)
hashCode()
getClass()
```

Na tym laboratorium najważniejsza będzie:

```java
toString()
```

---

# 21. Nadpisywanie `toString()`

Domyślna reprezentacja obiektu nie jest zwykle przyjazna dla użytkownika.

```java
Person person =
        new Person("Anna");

System.out.println(person);
```

`println()` pośrednio wykorzystuje:

```java
toString()
```

Możemy ją nadpisać:

```java
public class Person {

    private String name;

    public Person(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Person{name='%s'}"
                .formatted(this.name);
    }
}
```

Teraz:

```java
System.out.println(person);
```

wyświetli czytelny opis obiektu.

---

# 22. `equals()` i `hashCode()`

Klasa `Object` udostępnia między innymi metody:

```java
equals()
hashCode()
```

Służą one do określania, **kiedy dwa obiekty mają być traktowane jako równe**.

### `equals()`

Metoda:

```java
equals()
```

odpowiada na pytanie:

> Czy ten obiekt jest logicznie równy innemu obiektowi?

Domyślna implementacja odziedziczona z `Object` traktuje dwa różne obiekty jako różne, nawet jeżeli zawierają takie same dane.

Jeżeli chcemy porównywać obiekty według ich pól, nadpisujemy `equals()`.

### `hashCode()`

Metoda:

```java
hashCode()
```

zwraca liczbę typu:

```java
int
```

reprezentującą kod skrótu obiektu.

Jest wykorzystywana między innymi przez:

```java
HashSet
HashMap
```

do szybkiego wyszukiwania i porównywania obiektów.

Jeżeli dwa obiekty są równe według:

```java
equals()
```

to muszą mieć również taki sam:

```java
hashCode()
```

---

## `==` a `equals()`

Operator:

```java
==
```

dla obiektów sprawdza, czy dwie referencje wskazują na **ten sam obiekt**.

Przykład:

```java
Student first =
        new Student(
                123456,
                "Anna"
        );

Student second =
        new Student(
                123456,
                "Anna"
        );

System.out.println(
        first == second
); // false
```

Są to dwa osobne obiekty.

Domyślnie również:

```java
System.out.println(
        first.equals(second)
); // false
```

---

## Własne `equals()`

Jeżeli student ma być identyfikowany przez numer albumu:

```java
@Override
public boolean equals(Object object) {

    if (this == object) {
        return true;
    }

    if (
        object == null
        || this.getClass()
                != object.getClass()
    ) {
        return false;
    }

    Student other =
            (Student) object;

    return this.albumNumber
            == other.albumNumber;
}
```

Wtedy:

```java
Student first =
        new Student(
                123456,
                "Anna"
        );

Student second =
        new Student(
                123456,
                "Anna Kowalska"
        );

System.out.println(
        first.equals(second)
); // true
```

Obiekty są różnymi instancjami, ale reprezentują logicznie tego samego studenta.

---

## Własne `hashCode()`

Jeżeli nadpisujemy:

```java
equals()
```

powinniśmy również nadpisać:

```java
hashCode()
```

i używać tych samych pól.

```java
@Override
public int hashCode() {
    return Objects.hash(
            this.albumNumber
    );
}
```

Wymaga:

```java
import java.util.Objects;
```

Najważniejsza zasada:

```text
jeżeli a.equals(b) == true,
to a.hashCode() == b.hashCode()
```

---

## Porównywanie pól referencyjnych

Dla pól takich jak:

```java
String
```

nie używamy zwykle:

```java
==
```

lecz:

```java
Objects.equals(...)
```

Przykład:

```java
@Override
public boolean equals(Object object) {

    if (this == object) {
        return true;
    }

    if (
        object == null
        || this.getClass()
                != object.getClass()
    ) {
        return false;
    }

    Person other =
            (Person) object;

    return Objects.equals(
            this.firstName,
            other.firstName
    )
            && Objects.equals(
                    this.lastName,
                    other.lastName
            );
}

@Override
public int hashCode() {
    return Objects.hash(
            this.firstName,
            this.lastName
    );
}
```

### Do zapamiętania

```text
==          → czy referencje wskazują na ten sam obiekt

equals()    → czy dwa obiekty mają być traktowane
              jako logicznie równe

hashCode()  → liczbowy kod skrótu obiektu,
              używany m.in. przez HashSet i HashMap
```

Na późniejszych zajęciach wykorzystamy te metody praktycznie przy:

```java
HashSet
HashMap
```
---

# 23. `final` i dziedziczenie

Słowo:

```java
final
```

ma kilka zastosowań.

W kontekście dziedziczenia najważniejsze są:

- finalna klasa,
- finalna metoda.

---

# 24. Finalna klasa

```java
public final class SecurityToken {
}
```

Nie można po niej dziedziczyć:

```java
// public class CustomToken
//         extends SecurityToken {
// }
```

---

# 25. Finalna metoda

```java
public class Game {

    public final void initialize() {
        System.out.println(
                "Inicjalizacja gry."
        );
    }
}
```

Podklasa może dziedziczyć metodę:

```java
public class RPG extends Game {
}
```

ale nie może jej nadpisać:

```java
// @Override
// public void initialize() {
// }
```

---

# 26. Dlaczego metoda może być `final`?

Metoda może zostać oznaczona jako `final`, jeżeli autor klasy chce zagwarantować, że kluczowy fragment zachowania pozostanie taki sam we wszystkich klasach pochodnych.

Należy jednak stosować `final` świadomie – zbyt restrykcyjny projekt może utrudnić późniejsze rozszerzanie kodu.

---

# 27. Przykład – pracownicy

Klasa bazowa:

```java
public class Employee {

    private String name;
    private double baseSalary;

    public Employee(
            String name,
            double baseSalary
    ) {
        this.name = name;
        this.baseSalary = baseSalary;
    }

    public String getName() {
        return this.name;
    }

    protected double getBaseSalary() {
        return this.baseSalary;
    }

    public double calculateSalary() {
        return this.baseSalary;
    }

    public void showInformation() {
        System.out.println(
                this.name
                + ": "
                + this.calculateSalary()
                + " zł"
        );
    }
}
```

Podklasa:

```java
public class Manager extends Employee {

    private double bonus;

    public Manager(
            String name,
            double baseSalary,
            double bonus
    ) {
        super(name, baseSalary);
        this.bonus = bonus;
    }

    @Override
    public double calculateSalary() {
        return this.getBaseSalary()
                + this.bonus;
    }

    @Override
    public String toString() {
        return "Manager{name='%s', salary=%.2f}"
                .formatted(
                        this.getName(),
                        this.calculateSalary()
                );
    }
}
```

W przykładzie występują:

- `extends`,
- `super(...)`,
- `private`,
- `protected`,
- nadpisywanie metod,
- `@Override`,
- metoda odziedziczona z `Object`.

---

# 28. Typowe błędy

## 28.1. Dziedziczenie bez relacji IS-A

```text
Engine extends Car
```

Silnik nie jest rodzajem samochodu.

---

## 28.2. Bezpośredni dostęp do pola `private`

```java
public class Person {

    private String name;
}
```

```java
public class Student extends Person {

    public void show() {
        // System.out.println(this.name);
    }
}
```

---

## 28.3. Brak poprawnego `super(...)`

Klasa bazowa:

```java
public class Person {

    public Person(String name) {
    }
}
```

Podklasa:

```java
public class Student extends Person {

    // public Student() {
    // }
}
```

Kompilator próbowałby wywołać:

```java
super();
```

którego nie ma.

---

## 28.4. Literówka bez `@Override`

```java
public void makeSounds() {
}
```

zamiast:

```java
public void makeSound() {
}
```

Bez `@Override` błąd intencji może być trudniejszy do zauważenia.

---

## 28.5. Próba nadpisania metody `final`

```java
public final void initialize() {
}
```

nie może zostać nadpisana w podklasie.

---

## 28.6. Niepotrzebne ukrywanie pól

```java
class Parent {
    protected String name;
}

class Child extends Parent {
    protected String name;
}
```

Kod jest możliwy, ale dwie różne składowe o tej samej nazwie mogą znacząco obniżyć czytelność.

---

# **Zadania**

## Zadanie 1 – `Person` i `Employee`

Utwórz klasę:

```text
Person
```

z polami:

```text
firstName
lastName
```

Zastosuj:

- `private` dla `firstName`,
- `protected` dla `lastName`.

Dodaj konstruktor oraz getter dla `firstName`.

Utwórz:

```text
Employee extends Person
```

z dodatkowym polem:

```text
position
```

W metodzie:

```java
displayData()
```

spróbuj uzyskać dostęp do obu pól.

Odpowiedz:

1. dlaczego `firstName` nie jest bezpośrednio dostępne,
2. dlaczego `lastName` jest dostępne,
3. czy lepszym projektem zawsze będzie `protected` zamiast `private`.

---

## Zadanie 2 – różne pakiety

Umieść:

```text
Person
```

oraz:

```text
Employee
```

w dwóch różnych pakietach.

Sprawdź zachowanie:

```text
private
package-private
protected
public
```

Utwórz dodatkową klasę, która:

- znajduje się w pakiecie `Employee`,
- ale **nie dziedziczy** po `Person`.

Sprawdź, czy ma dostęp do `protected` składowej `Person`.

---

## Zadanie 3 – `Vehicle` i `Car`

Utwórz klasę:

```text
Vehicle
```

z prywatnymi polami:

```text
brand
model
```

oraz konstruktorem.

Następnie:

```text
Car extends Vehicle
```

z dodatkowym polem:

```text
numberOfDoors
```

Konstruktor `Car` powinien wykorzystać:

```java
super(...)
```

Dodaj:

```java
showInformation()
```

i wyświetl wszystkie dane pojazdu.

Nie zmieniaj pól klasy bazowej na `public` tylko po to, aby dostać się do nich z podklasy.

---

## Zadanie 4 – kolejność konstruktorów

Utwórz hierarchię:

```text
A
↑
B
↑
C
```

Każda klasa powinna posiadać konstruktor wypisujący nazwę klasy.

Utwórz:

```java
new C();
```

Przed uruchomieniem programu zapisz przewidywaną kolejność komunikatów.

Następnie zweryfikuj odpowiedź.

---

## Zadanie 5 – nadpisywanie

Utwórz klasę:

```text
Animal
```

z metodą:

```java
makeSound()
```

Następnie utwórz:

```text
Dog
Cat
Cow
```

dziedziczące po `Animal`.

Każda klasa powinna nadpisać:

```java
makeSound()
```

za pomocą:

```java
@Override
```

Na tym etapie utwórz osobne zmienne dla poszczególnych typów.

Polimorficzne przechowywanie różnych zwierząt w jednej kolekcji będzie tematem kolejnych zajęć.

---

## Zadanie 6 – `super.method()`

W klasie:

```text
Employee
```

utwórz:

```java
showInformation()
```

wyświetlającą podstawowe dane.

W:

```text
Manager extends Employee
```

nadpisz metodę tak, aby:

1. najpierw wykonać implementację klasy bazowej przez:

```java
super.showInformation();
```

2. następnie wyświetlić dział zarządzany przez kierownika.

---

## Zadanie 7 – dziedziczenie wielopoziomowe

Zaprojektuj:

```text
Device
↑
Computer
↑
Laptop
```

### `Device`

powinno zawierać:

```text
manufacturer
```

### `Computer`

dodatkowo:

```text
ramGb
```

### `Laptop`

dodatkowo:

```text
batteryCapacityWh
```

Każda klasa powinna posiadać odpowiedni konstruktor.

Dodaj metody charakterystyczne dla kolejnych poziomów i pokaż, że obiekt `Laptop` może korzystać z odziedziczonego zachowania.

---

## Zadanie 8 – klasa `Object`

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

Nadpisz:

```java
toString()
```

tak, aby:

```java
System.out.println(book);
```

wyświetlało czytelne informacje o książce.

Następnie wywołaj:

```java
book.getClass()
```

i sprawdź wynik.

---

## Zadanie 9 – `final`

Utwórz klasę:

```text
Game
```

z finalną metodą:

```java
initialize()
```

oraz zwykłą metodą:

```java
start()
```

Utwórz:

```text
RPG extends Game
```

Nadpisz `start()`.

Spróbuj nadpisać `initialize()` i sprawdź komunikat kompilatora.

Następnie utwórz prostą klasę oznaczoną:

```java
final
```

i spróbuj po niej dziedziczyć.

---

## Zadanie 10 – kowariantny typ zwracany

Utwórz:

```text
Document
```

z metodą:

```java
public Document copy()
```

Następnie:

```text
Report extends Document
```

i nadpisz:

```java
copy()
```

tak, aby zwracała:

```java
Report
```

Sprawdź, czy kod się kompiluje.

---

## Zadanie 11 – napraw hierarchię

Otrzymujesz kod:

```java
public class Person {

    private String name;

    public Person(String name) {
        this.name = name;
    }

    public void showInformation() {
        System.out.println(
                "Person: " + this.name
        );
    }
}
```

```java
public class Student extends Person {

    private int albumNumber;

    public Student(int albumNumber) {
        this.albumNumber = albumNumber;
    }

    public void showInformations() {
        System.out.println(
                name + ", " + albumNumber
        );
    }
}
```

Znajdź i popraw problemy:

1. z konstruktorem,
2. z dostępem do pola,
3. z nadpisywaniem metody,
4. z brakiem `@Override`.

Po poprawieniu kodu `Student` powinien rzeczywiście rozszerzać zachowanie `Person`.

---

## Zadanie 12 – projekt hierarchii

Zaprojektuj hierarchię dla jednego z systemów:

### A. Pracownicy

```text
Employee
Developer
Manager
Tester
```

### B. Pojazdy

```text
Vehicle
Car
Motorcycle
Truck
```

### C. Konta użytkowników

```text
Account
StandardAccount
PremiumAccount
AdministratorAccount
```

Wymagania:

- minimum jedna klasa bazowa,
- minimum trzy podklasy,
- wykorzystanie `super(...)`,
- co najmniej jedna metoda nadpisana,
- co najmniej jedna metoda z klasy bazowej wywołana przez `super`,
- sensowne zastosowanie `private` oraz ewentualnie `protected`,
- `toString()` w co najmniej jednej klasie.

Na końcu odpowiedz:

> Dlaczego wybrane relacje są relacjami IS-A?

---

# **Zadanie dodatkowe – znajdź zły model**

Dla każdego przypadku zdecyduj, czy dziedziczenie jest dobrym rozwiązaniem:

```text
Car extends Engine
```

```text
Dog extends Animal
```

```text
Library extends Book
```

```text
Manager extends Employee
```

```text
House extends Address
```

```text
ElectricCar extends Car
```

Dla przypadków błędnych zaproponuj, jaka relacja pomiędzy obiektami byłaby bardziej naturalna.

Nie musisz jeszcze implementować kompozycji – chodzi o poprawne modelowanie.

---

# **Po zajęciach**

Po laboratorium należy potrafić wyjaśnić:

```text
klasa bazowa
klasa pochodna
IS-A
extends
super
@Override
final
Object
```

Należy rozumieć różnicę pomiędzy:

```text
dziedziczeniem a zwykłym użyciem innego obiektu
przeciążaniem a nadpisywaniem
private a protected
this a super
```

oraz umieć przewidzieć kolejność wykonywania konstruktorów w hierarchii.

Kolejne laboratorium będzie poświęcone **polimorfizmowi, wiązaniu dynamicznemu, rzutowaniu typów i operatorowi `instanceof`**.
