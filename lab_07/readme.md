# **Programowanie Obiektowe – Java (2026Z)**

## **Lab 7: Klasy zagnieżdżone, wewnętrzne, lokalne i anonimowe + powtórzenie przed Kolokwium I**

---

## 1. Cel zajęć

Celem pierwszej części laboratorium jest poznanie różnych sposobów definiowania klas **wewnątrz innych klas lub metod**.

Omówione zostaną:

- statyczne klasy zagnieżdżone (*static nested classes*),
- niestatyczne klasy wewnętrzne (*inner classes*),
- klasy lokalne,
- klasy anonimowe,
- dostęp do składowych klasy zewnętrznej,
- `OuterClass.this`,
- dostęp do lokalnych zmiennych z klas lokalnych i anonimowych,
- pojęcie zmiennej **effectively final**,
- zastosowania klas anonimowych przed wprowadzeniem wyrażeń lambda.

Druga część laboratorium stanowi **powtórzenie materiału przed Kolokwium I**.

Zakres powtórzenia obejmuje:

- klasy i obiekty,
- konstruktory i referencje,
- hermetyzację,
- `static` i `final`,
- przeciążanie metod,
- dziedziczenie,
- `super` i `@Override`,
- polimorfizm,
- upcasting i downcasting,
- `instanceof`,
- klasy abstrakcyjne,
- interfejsy,
- kompozycję.

Po zakończeniu zajęć osoba studencka powinna potrafić:

- rozróżnić cztery podstawowe rodzaje klas zagnieżdżonych,
- dobrać właściwy rodzaj klasy do prostego problemu,
- utworzyć obiekt niestatycznej klasy wewnętrznej,
- napisać klasę lokalną,
- utworzyć jednorazową implementację przez klasę anonimową,
- rozumieć ograniczenia dotyczące przechwytywanych zmiennych lokalnych,
- analizować i poprawiać kod obejmujący materiał z pierwszej części semestru.

---

# 2. Klasy zagnieżdżone

Java pozwala deklarować klasę **wewnątrz innej klasy**.

Ogólny przykład:

```java
public class Outer {

    class Inner {
    }
}
```

Typ zadeklarowany wewnątrz innej klasy nazywamy typem zagnieżdżonym.

Najważniejsze przypadki omawiane na tych zajęciach:

```text
klasa zagnieżdżona statyczna
klasa wewnętrzna
klasa lokalna
klasa anonimowa
```

---

# 3. Statyczna klasa zagnieżdżona

Przykład:

```java
public class Computer {

    private String model;

    public Computer(String model) {
        this.model = model;
    }

    public static class Specifications {

        private int ramGb;
        private int diskGb;

        public Specifications(
                int ramGb,
                int diskGb
        ) {
            this.ramGb = ramGb;
            this.diskGb = diskGb;
        }

        public void print() {
            System.out.println(
                    "RAM: " + this.ramGb
                    + " GB, dysk: "
                    + this.diskGb
                    + " GB"
            );
        }
    }
}
```

Utworzenie obiektu:

```java
Computer.Specifications specifications =
        new Computer.Specifications(
                32,
                1000
        );
```

Nie potrzebujemy obiektu:

```java
Computer
```

aby utworzyć obiekt:

```java
Specifications
```

---

# 4. Dlaczego `static`?

Statyczna klasa zagnieżdżona należy logicznie do klasy zewnętrznej, ale **nie jest powiązana z konkretną instancją klasy zewnętrznej**.

Dlatego nie posiada automatycznie:

```java
Computer.this
```

i nie może bezpośrednio odczytać niestatycznego pola:

```java
model
```

bez posiadania referencji do konkretnego `Computer`.

Niepoprawnie:

```java
public static class Specifications {

    public void showModel() {
        // System.out.println(model);
    }
}
```

---

# 5. Kiedy używać statycznej klasy zagnieżdżonej?

Może mieć sens, gdy typ:

- jest silnie związany logicznie z inną klasą,
- nie potrzebuje konkretnej instancji klasy zewnętrznej,
- nie powinien zaśmiecać przestrzeni nazw jako niezależna klasa najwyższego poziomu.

Przykładowo:

```text
Map.Entry
```

jest zagnieżdżonym interfejsem związanym logicznie z `Map`.

---

# 6. Niestatyczna klasa wewnętrzna

Klasa zadeklarowana wewnątrz innej klasy bez `static` jest klasą wewnętrzną.

```java
public class BankAccount {

    private String owner;
    private double balance;

    public BankAccount(
            String owner,
            double balance
    ) {
        this.owner = owner;
        this.balance = balance;
    }

    public class Transaction {

        private double amount;

        public Transaction(double amount) {
            this.amount = amount;
        }

        public void print() {
            System.out.println(
                    owner
                    + ": "
                    + this.amount
                    + " zł"
            );
        }
    }
}
```

Klasa:

```text
Transaction
```

jest powiązana z konkretnym obiektem:

```text
BankAccount
```

i może korzystać z jego prywatnych składowych.

---

# 7. Tworzenie obiektu klasy wewnętrznej

Najpierw potrzebujemy obiektu klasy zewnętrznej:

```java
BankAccount account =
        new BankAccount(
                "Anna",
                1000
        );
```

Następnie:

```java
BankAccount.Transaction transaction =
        account.new Transaction(250);
```

Nie możemy po prostu napisać:

```java
// new BankAccount.Transaction(250);
```

ponieważ `Transaction` potrzebuje konkretnej instancji `BankAccount`.

---

# 8. Dostęp do klasy zewnętrznej

Klasa wewnętrzna może bezpośrednio korzystać z pól i metod klasy zewnętrznej, również prywatnych:

```java
public class Outer {

    private int value = 10;

    public class Inner {

        public void show() {
            System.out.println(value);
        }
    }
}
```

Jest to możliwe, ponieważ obiekt `Inner` jest związany z konkretnym obiektem `Outer`.

---

# 9. `this` w klasie wewnętrznej

Wewnątrz klasy wewnętrznej:

```java
this
```

oznacza **obiekt klasy wewnętrznej**.

Jeżeli chcemy jawnie odwołać się do obiektu klasy zewnętrznej, używamy:

```java
Outer.this
```

Przykład:

```java
public class Outer {

    private String name = "Outer";

    public class Inner {

        private String name = "Inner";

        public void show() {
            System.out.println(
                    this.name
            );

            System.out.println(
                    Outer.this.name
            );
        }
    }
}
```

Wynik:

```text
Inner
Outer
```

---

# 10. Prywatna klasa wewnętrzna

Klasa wewnętrzna może być:

```java
private
```

Przykład:

```java
public class TextParser {

    private class Token {

        private String value;

        public Token(String value) {
            this.value = value;
        }
    }
}
```

Typ:

```text
Token
```

jest wtedy szczegółem implementacyjnym `TextParser`.

Kod zewnętrzny nie musi nawet wiedzieć o jego istnieniu.

To może wspierać hermetyzację.

---

# 11. Klasa lokalna

Klasę można zadeklarować **wewnątrz metody**.

Przykład:

```java
public void process() {

    class LocalValidator {

        public boolean isValid(
                String value
        ) {
            return value != null
                    && !value.isBlank();
        }
    }

    LocalValidator validator =
            new LocalValidator();

    System.out.println(
            validator.isValid("Java")
    );
}
```

Klasa:

```text
LocalValidator
```

jest dostępna wyłącznie w zakresie, w którym została zadeklarowana.

---

# 12. Po co klasa lokalna?

Klasa lokalna może być przydatna, jeżeli:

- ma sens wyłącznie wewnątrz jednej metody,
- posiada więcej logiki niż prosta klasa anonimowa,
- potrzebuje własnych metod lub stanu,
- nie chcemy wystawiać jej jako elementu całej klasy.

W praktyce klasy lokalne występują rzadziej niż zwykłe klasy, klasy wewnętrzne i lambdy, ale warto znać mechanizm języka.

---

# 13. Dostęp do zmiennych lokalnych

Rozważmy:

```java
public void printWithPrefix() {

    String prefix = "[INFO] ";

    class Printer {

        public void print(
                String message
        ) {
            System.out.println(
                    prefix + message
            );
        }
    }

    new Printer().print("Start");
}
```

Klasa lokalna może korzystać z:

```text
prefix
```

ponieważ zmienna jest **final lub effectively final**.

---

# 14. Effectively final

Zmienna jest **effectively final**, jeżeli po inicjalizacji nie jest już ponownie przypisywana.

Przykład:

```java
String prefix = "[INFO] ";
```

jest effectively final, jeżeli później nie wykonamy:

```java
prefix = "[ERROR] ";
```

Dlatego to jest poprawne:

```java
String prefix = "[INFO] ";

class Printer {

    public void print(String text) {
        System.out.println(
                prefix + text
        );
    }
}
```

---

## 14.1. Niepoprawny przykład

```java
String prefix = "[INFO] ";

prefix = "[ERROR] ";

class Printer {

    public void print(String text) {
        // użycie prefix spowoduje błąd,
        // ponieważ zmienna nie jest
        // effectively final
    }
}
```

Ta sama zasada będzie później istotna przy wyrażeniach lambda.

---

# 15. Klasa anonimowa

**Klasa anonimowa** to jednorazowa klasa bez własnej nazwy tworzona bezpośrednio w miejscu użycia.

Może:

- rozszerzać jedną klasę,
- implementować jeden interfejs w deklaracji tworzonego obiektu.

Przykład interfejsu:

```java
public interface ClickListener {

    void onClick();
}
```

Jednorazowa implementacja:

```java
ClickListener listener =
        new ClickListener() {

            @Override
            public void onClick() {
                System.out.println(
                        "Kliknięto."
                );
            }
        };
```

---

# 16. Co powstaje w tym kodzie?

W:

```java
new ClickListener() {
    ...
}
```

Java tworzy obiekt nowej, bezimiennej klasy implementującej:

```text
ClickListener
```

Kod:

```java
{
    ...
}
```

jest ciałem tej klasy.

---

# 17. Klasa anonimowa rozszerzająca klasę abstrakcyjną

Załóżmy:

```java
public abstract class Validator {

    public abstract boolean isValid(
            String value
    );
}
```

Możemy utworzyć:

```java
Validator emailValidator =
        new Validator() {

            @Override
            public boolean isValid(
                    String value
            ) {
                return value != null
                        && value.contains("@");
            }
        };
```

Nie tworzymy bezpośrednio obiektu `Validator`.

Tworzymy obiekt **anonimowej podklasy** `Validator`.

---

# 18. Klasa anonimowa może posiadać stan

```java
public abstract class Task {

    public abstract void execute();
}
```

```java
Task task =
        new Task() {

            private int counter = 0;

            @Override
            public void execute() {
                this.counter++;

                System.out.println(
                        "Wywołanie nr "
                        + this.counter
                );
            }
        };
```

Wywołania:

```java
task.execute();
task.execute();
task.execute();
```

mogą dać:

```text
Wywołanie nr 1
Wywołanie nr 2
Wywołanie nr 3
```

---

# 19. Ograniczenia klasy anonimowej

Klasa anonimowa:

- nie posiada własnej nazwy,
- nie może być później użyta jako typ po własnej nazwie,
- nie deklaruje zwykłego nazwanego konstruktora,
- jest przeznaczona głównie do jednorazowego użycia,
- może znacząco obniżyć czytelność, jeżeli jej ciało jest duże.

Jeżeli implementacja zaczyna być rozbudowana albo potrzebna w wielu miejscach, zwykle lepiej utworzyć zwykłą nazwaną klasę.

---

# 20. `this` w klasie anonimowej

W klasie anonimowej:

```java
this
```

odnosi się do **obiektu klasy anonimowej**.

Przykład:

```java
public class Window {

    private String name = "Window";

    public void register() {

        ClickListener listener =
                new ClickListener() {

                    private String name =
                            "Anonymous";

                    @Override
                    public void onClick() {
                        System.out.println(
                                this.name
                        );

                        System.out.println(
                                Window.this.name
                        );
                    }
                };
    }
}
```

To będzie ważna różnica w porównaniu z wyrażeniami lambda, które poznamy później.

---

# 21. Klasa anonimowa i zmienne lokalne

Tak samo jak klasa lokalna, klasa anonimowa może korzystać z lokalnych zmiennych, które są:

```text
final
```

lub:

```text
effectively final
```

Przykład:

```java
String prefix = "[CLICK] ";

ClickListener listener =
        new ClickListener() {

            @Override
            public void onClick() {
                System.out.println(
                        prefix + "OK"
                );
            }
        };
```

---

# 22. Statyczna zagnieżdżona czy wewnętrzna?

## Statyczna klasa zagnieżdżona

```java
static class Nested
```

- nie potrzebuje obiektu klasy zewnętrznej,
- nie posiada automatycznej referencji do konkretnego `Outer`,
- jest przede wszystkim logicznie zgrupowana z klasą zewnętrzną.

## Klasa wewnętrzna

```java
class Inner
```

- jest powiązana z konkretnym obiektem klasy zewnętrznej,
- może bezpośrednio korzystać z jego stanu,
- tworzona jest przez:

```java
outer.new Inner()
```

---

# 23. Porównanie rodzajów klas

| Rodzaj | Gdzie deklarowana? | Związana z instancją klasy zewnętrznej? | Typowe zastosowanie |
|---|---|:---:|---|
| statyczna zagnieżdżona | wewnątrz klasy | ❌ | logiczne grupowanie typów |
| wewnętrzna | wewnątrz klasy | ✅ | obiekt ściśle związany z konkretnym obiektem zewnętrznym |
| lokalna | wewnątrz bloku/metody | zależy od kontekstu | lokalna pomocnicza logika |
| anonimowa | w wyrażeniu `new` | zależy od kontekstu | jednorazowa implementacja/rozszerzenie |

---

# 24. Kiedy nie używać klas zagnieżdżonych?

Nie należy umieszczać klasy wewnątrz innej klasy tylko dlatego, że „technicznie można”.

Jeżeli typ:

- jest duży,
- posiada własną niezależną odpowiedzialność,
- jest używany w wielu miejscach,
- nie jest silnie związany z klasą zewnętrzną,

zwykła osobna klasa może być czytelniejszym rozwiązaniem.

---

# 25. Klasy anonimowe a wyrażenia lambda

Dla interfejsu:

```java
public interface ClickListener {

    void onClick();
}
```

klasa anonimowa:

```java
ClickListener listener =
        new ClickListener() {

            @Override
            public void onClick() {
                System.out.println(
                        "Kliknięto."
                );
            }
        };
```

może wyglądać dość rozwlekle.

W późniejszej części semestru zobaczymy, że jeżeli interfejs posiada dokładnie jedną metodę abstrakcyjną, często można użyć **wyrażenia lambda**.

Na tym laboratorium **nie korzystamy jeszcze z lambd**.

Celem jest zrozumienie mechanizmu klas anonimowych, ponieważ:

- klasy anonimowe mogą rozszerzać klasy,
- mogą posiadać własny stan,
- mogą implementować więcej zachowania niż pojedyncza funkcja,
- nadal występują w istniejącym kodzie Java.

---

# 26. Typowe błędy

## 26.1. Tworzenie niestatycznej klasy wewnętrznej bez obiektu zewnętrznego

Niepoprawnie:

```java
// Outer.Inner inner =
//         new Outer.Inner();
```

Poprawnie:

```java
Outer outer = new Outer();

Outer.Inner inner =
        outer.new Inner();
```

---

## 26.2. Oczekiwanie dostępu do pola instancji z klasy statycznej

```java
public class Outer {

    private int value;

    public static class Nested {

        public void show() {
            // System.out.println(value);
        }
    }
}
```

Brak konkretnego obiektu `Outer`.

---

## 26.3. Modyfikowanie przechwytywanej zmiennej lokalnej

```java
int counter = 0;

Runnable runnable =
        new Runnable() {

            @Override
            public void run() {
                // counter++;
            }
        };
```

Lokalna zmienna użyta przez klasę anonimową musi być final lub effectively final.

---

## 26.4. Za duża klasa anonimowa

Jeżeli anonimowa implementacja ma:

- wiele pól,
- wiele metod pomocniczych,
- kilkadziesiąt linii logiki,
- jest używana więcej niż raz,

warto rozważyć nazwany typ.

---

# **Zadania – klasy zagnieżdżone i anonimowe**

## Zadanie 1 – statyczna klasa zagnieżdżona

Utwórz klasę:

```text
Computer
```

z prywatnym polem:

```text
model
```

oraz statyczną klasą zagnieżdżoną:

```text
Specifications
```

przechowującą:

```text
ramGb
diskGb
processor
```

Dodaj metodę:

```java
print()
```

Utwórz obiekt `Specifications` **bez tworzenia obiektu `Computer`**.

Następnie spróbuj z poziomu `Specifications` odwołać się bezpośrednio do:

```text
model
```

i wyjaśnij wynik.

---

## Zadanie 2 – klasa wewnętrzna

Utwórz:

```text
BankAccount
```

z prywatnymi polami:

```text
owner
balance
```

Wewnątrz niej utwórz niestatyczną klasę:

```text
Transaction
```

przechowującą:

```text
description
amount
```

`Transaction.print()` ma wyświetlać również właściciela konta.

Utwórz dwie różne instancje `BankAccount` i po jednej transakcji związanej z każdym kontem.

---

## Zadanie 3 – `Outer.this`

Utwórz:

```text
Company
```

z polem:

```text
name
```

oraz klasę wewnętrzną:

```text
Department
```

również posiadającą pole:

```text
name
```

W metodzie:

```java
printNames()
```

wyświetl osobno:

```java
this.name
```

oraz:

```java
Company.this.name
```

Wyjaśnij różnicę.

---

## Zadanie 4 – prywatna klasa wewnętrzna

Utwórz klasę:

```text
PasswordValidator
```

posiadającą prywatną klasę wewnętrzną:

```text
ValidationResult
```

przechowującą:

```text
valid
message
```

Kod zewnętrzny **nie powinien tworzyć bezpośrednio** `ValidationResult`.

Zastanów się, jaki publiczny interfejs klasy `PasswordValidator` będzie bardziej użyteczny.

---

## Zadanie 5 – klasa lokalna

Napisz metodę:

```java
validateNames(String[] names)
```

Wewnątrz metody utwórz lokalną klasę:

```text
NameValidator
```

sprawdzającą, czy nazwa:

- nie jest `null`,
- nie jest pusta,
- ma co najmniej 3 znaki.

Za jej pomocą wyświetl poprawne i niepoprawne elementy tablicy.

---

## Zadanie 6 – effectively final

Przeanalizuj:

```java
int minimumLength = 5;

class Validator {

    public boolean isValid(
            String value
    ) {
        return value != null
                && value.length()
                >= minimumLength;
    }
}
```

Kod działa.

Następnie przed deklaracją klasy dopisz:

```java
minimumLength = 10;
```

Sprawdź komunikat kompilatora i wyjaśnij pojęcie:

```text
effectively final
```

---

## Zadanie 7 – anonimowy `Validator`

Utwórz klasę abstrakcyjną:

```java
public abstract class Validator {

    public abstract boolean isValid(
            String value
    );
}
```

W `main` utwórz anonimową podklasę sprawdzającą, czy tekst:

- nie jest `null`,
- zawiera `@`,
- zawiera `.` po znaku `@`.

Nie twórz osobnej klasy `EmailValidator`.

---

## Zadanie 8 – anonimowy listener

Utwórz interfejs:

```java
public interface ClickListener {

    void onClick();
}
```

oraz klasę:

```text
Button
```

posiadającą:

```java
setClickListener(...)
click()
```

Obsługę kliknięcia przekaż do `Button` jako **klasę anonimową**.

Nie korzystaj z wyrażenia lambda.

---

## Zadanie 9 – anonimowa klasa ze stanem

Utwórz klasę abstrakcyjną:

```java
public abstract class Task {

    public abstract void execute();
}
```

Utwórz anonimową podklasę posiadającą prywatne pole:

```text
counter
```

Każde:

```java
execute()
```

ma zwiększać licznik i wyświetlać aktualną wartość.

Wywołaj metodę pięć razy.

---

## Zadanie 10 – refaktoryzacja

Masz dwie identyczne klasy anonimowe używane w różnych miejscach programu:

```java
Validator first =
        new Validator() {

            @Override
            public boolean isValid(
                    String text
            ) {
                return text != null
                        && !text.isBlank();
            }
        };
```

oraz identyczny kod w innym miejscu.

Czy klasa anonimowa nadal jest najlepszym rozwiązaniem?

Przebuduj kod tak, aby nie duplikować implementacji.

---

# **Powtórzenie przed Kolokwium I**

Poniższe zadania obejmują materiał z laboratoriów 2–6.

---

## Zadanie 11 – przewidź wynik

Bez uruchamiania programu określ wynik:

```java
class A {

    public A() {
        System.out.println("A()");
    }

    public void show() {
        System.out.println("A.show");
    }
}
```

```java
class B extends A {

    public B() {
        System.out.println("B()");
    }

    @Override
    public void show() {
        System.out.println("B.show");
    }
}
```

```java
public class Main {

    public static void main(String[] args) {
        A object = new B();

        object.show();
    }
}
```

Wyjaśnij osobno:

1. kolejność konstruktorów,
2. wybór implementacji `show()`.

---

## Zadanie 12 – uzupełnij kod

Uzupełnij miejsca `???`:

```java
public abstract class Employee {

    private String name;

    protected Employee(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }

    public ??? double calculateSalary();
}
```

```java
public class Manager ??? Employee {

    private double baseSalary;
    private double bonus;

    public Manager(
            String name,
            double baseSalary,
            double bonus
    ) {
        ???(name);

        this.baseSalary = baseSalary;
        this.bonus = bonus;
    }

    ???
    public double calculateSalary() {
        return this.baseSalary
                + this.bonus;
    }
}
```

---

## Zadanie 13 – znajdź błędy

Kod zawiera kilka problemów:

```java
public class Account {

    public double balance;
    private static String owner;

    public Account(
            String owner,
            double balance
    ) {
        owner = owner;
        balance = balance;
    }

    public static void deposit(
            double amount
    ) {
        balance += amount;
    }
}
```

Wskaż:

1. problemy z hermetyzacją,
2. błędne zastosowanie `static`,
3. problem z `this`,
4. problem kompilacji,
5. poprawną wersję klasy.

---

## Zadanie 14 – napraw dziedziczenie

```java
public class Person {

    private String name;

    public Person(String name) {
        this.name = name;
    }

    protected String getName() {
        return this.name;
    }

    public final void identify() {
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

    @Override
    public void identify() {
        System.out.println(
                getName()
                + " / "
                + albumNumber
        );
    }
}
```

Kod się nie kompiluje.

Znajdź **dwa niezależne powody** i zaproponuj poprawę.

---

## Zadanie 15 – usuń `instanceof`

Otrzymujesz:

```java
public static double calculate(
        Employee employee
) {
    if (employee instanceof Developer developer) {
        return developer.getSalary();
    }

    if (employee instanceof Manager manager) {
        return manager.getBaseSalary()
                + manager.getBonus();
    }

    return 0;
}
```

Przebuduj hierarchię tak, aby metoda wyglądała:

```java
public static double calculate(
        Employee employee
) {
    return employee.calculateSalary();
}
```

Zdecyduj:

- co powinno znaleźć się w klasie bazowej,
- czy `Employee` powinno być zwykłą czy abstrakcyjną klasą.

---

## Zadanie 16 – interfejs czy klasa abstrakcyjna?

Masz zaprojektować:

```text
EmailNotification
SmsNotification
PushNotification
```

Wszystkie posiadają:

```text
recipient
createdAt
```

i muszą realizować:

```java
send()
```

Jednocześnie różne, niezwiązane klasy systemu mogą być:

```text
Loggable
```

Zaproponuj:

1. co powinno być klasą abstrakcyjną,
2. co powinno być interfejsem,
3. dlaczego.

---

## Zadanie 17 – dziedziczenie czy kompozycja?

Oceń:

```java
class Car extends Engine
```

```java
class Manager extends Employee
```

```java
class Order extends PaymentMethod
```

```java
class Dog extends Animal
```

```java
class Computer extends Processor
```

Dla błędnych relacji zaproponuj kompozycję lub inną zależność.

---

## Zadanie 18 – referencje

Bez uruchamiania programu:

```java
class Box {

    int value;

    Box(int value) {
        this.value = value;
    }
}
```

```java
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
```

Narysuj schemat obiektów i referencji oraz zapisz wynik.

---

## Zadanie 19 – przeciążanie czy nadpisywanie?

Dany jest kod:

```java
class Animal {

    public void sound() {
        System.out.println("Animal");
    }
}
```

```java
class Dog extends Animal {

    @Override
    public void sound() {
        System.out.println("Dog");
    }
}
```

```java
static void show(Animal animal) {
    System.out.println("show Animal");
}

static void show(Dog dog) {
    System.out.println("show Dog");
}
```

```java
Animal animal = new Dog();

show(animal);
animal.sound();
```

Określ wynik i wyjaśnij:

- które wywołanie rozstrzyga kompilator,
- które podlega dynamicznemu wiązaniu.

---

## Zadanie 20 – większe zadanie przekrojowe

Zaprojektuj prosty system biblioteczny.

### `LibraryItem`

Utwórz abstrakcyjną klasę:

```text
LibraryItem
```

posiadającą prywatne pola:

```text
id
title
```

Konstruktor powinien sprawdzać, czy:

- `id` jest większe od `0`,
- `title` nie jest `null`,
- `title` nie jest pustym napisem.

Dodaj odpowiednie gettery.

Klasa ma posiadać abstrakcyjną metodę:

```java
public abstract int getLoanDays();
```

oraz zwykłą metodę:

```java
public String getInformation();
```

zwracającą tytuł oraz liczbę dni wypożyczenia.

---

### Licznik obiektów

Dodaj prywatne statyczne pole:

```java
private static int numberOfItems;
```

Licznik powinien zwiększać się przy tworzeniu każdego obiektu klasy pochodnej.

Dodaj:

```java
public static int getNumberOfItems();
```

---

### `toString()`

Nadpisz:

```java
toString()
```

tak, aby:

```java
System.out.println(item);
```

nie wyświetlało adresu obiektu, lecz np.:

```text
Book: Wiedźmin – 30 dni
```

Reprezentacja ma zawierać:

- nazwę konkretnej klasy,
- informacje zwracane przez `getInformation()`.

---

### Podklasy

Utwórz:

```text
Book
Magazine
BoardGame
```

Każda klasa:

- dziedziczy po `LibraryItem`,
- posiada co najmniej jedno własne pole,
- poprawnie wywołuje konstruktor klasy bazowej przez `super(...)`,
- nadpisuje `getLoanDays()`.

Każdy typ powinien zwracać inną liczbę dni wypożyczenia.

---

### `Reservable`

Utwórz interfejs:

```java
public interface Reservable {

    void reserve();

    boolean isReserved();
}
```

Tylko wybrane typy elementów bibliotecznych mają implementować ten interfejs.

---

### `Library`

Utwórz klasę:

```text
Library
```

Biblioteka ma **posiadać elementy biblioteczne**, a nie po nich dziedziczyć.

Dodaj metodę:

```java
public void printLoanInformation(
        LibraryItem item
)
```

która wykorzystuje polimorfizm i nie sprawdza konkretnego typu obiektu przez:

```java
instanceof
```

---

### `Main`

Utwórz tablicę:

```java
LibraryItem[] items
```

zawierającą co najmniej:

- jedną książkę,
- jedno czasopismo,
- jedną grę planszową.

Za pomocą **jednej pętli**:

- wyświetl każdy obiekt,
- wyświetl informacje o czasie wypożyczenia.

Następnie wyświetl:

```java
LibraryItem.getNumberOfItems()
```

---

### Klasa anonimowa

W metodzie `main` utwórz anonimową implementację:

```java
Reservable
```

która przechowuje informację o tym, czy obiekt został zarezerwowany.

Wywołaj:

```java
reserve()
```

oraz:

```java
isReserved()
```

Nie używaj wyrażeń lambda.

---

### Wymagania

W rozwiązaniu zastosuj:

- hermetyzację pól,
- walidację danych w konstruktorze,
- poprawne użycie `this`,
- `static`,
- `static final`,
- `super(...)`,
- `@Override`,
- `toString()`,
- dziedziczenie,
- polimorfizm,
- klasę abstrakcyjną,
- interfejs,
- klasę anonimową,
- kompozycję/agregację zamiast błędnego dziedziczenia,
- brak zbędnego `instanceof`.

---

# **Checklist przed Kolokwium I**

Przed kolokwium należy umieć samodzielnie wyjaśnić i zastosować:

### Klasy i obiekty

```text
class
new
pole
metoda
this
konstruktor
referencja
null
```

### Hermetyzacja

```text
private
protected
public
package-private
getter
setter
kopie defensywne
```

### Składowe klasy

```text
static
final
static final
blok statyczny
blok instancyjny
przeciążanie
```

### Dziedziczenie

```text
extends
IS-A
super(...)
super.method()
@Override
Object
final class
final method
```

### Polimorfizm

```text
typ referencji
typ obiektu
upcasting
downcasting
instanceof
dynamic binding
ClassCastException
```

### Abstrakcja i interfejsy

```text
abstract class
abstract method
interface
implements
default
wiele interfejsów
```

### Relacje

```text
IS-A
HAS-A
USES-A
dziedziczenie
kompozycja
agregacja
asocjacja
```

---

# **Po zajęciach**

Po laboratorium należy rozróżniać:

```text
static nested class
inner class
local class
anonymous class
```

oraz rozumieć:

```text
Outer.this
effectively final
```

Należy również być przygotowanym do zadań polegających nie tylko na napisaniu klasy od zera, lecz także na:

- przewidywaniu wyniku programu,
- uzupełnianiu brakującego kodu,
- wskazywaniu błędów kompilacji,
- poprawianiu błędów logicznych,
- refaktoryzacji,
- uzasadnianiu decyzji projektowych.

Kolejne zajęcia będą przeznaczone na **Kolokwium I**.

# **Czego możecie się spodziewać na Kolokwium I**

Kolokwium trwa:

```text
90 minut
```

Do zdobycia jest maksymalnie:

```text
40 punktów
```

Kolokwium składa się z **4 zadań** o różnym charakterze.

---

## Rodzaje zadań

### Zadanie 1 – uzupełnianie kodu

Otrzymacie fragment istniejącego kodu z brakującymi elementami.

Waszym zadaniem będzie poprawne uzupełnienie fragmentów kodu, aby kod działał i zgadzał się z ideą Paradygmatu Programowania Obiektowego.

Nie jest to zadanie polegające na napisaniu całego programu od początku.

---

### Zadanie 2 – znajdowanie i naprawianie błędów

Otrzymacie istniejący kod zawierający:

- błędy kompilacji,
- błędne użycie mechanizmów programowania obiektowego,
- problemy projektowe.

Należy znaleźć problemy i poprawić kod tak, aby spełniał podane wymagania.

Możecie spodziewać się między innymi problemów związanych z:

```text
hermetyzacją
static
this
konstruktorami
dziedziczeniem
super
final
@Override
polimorfizmem
typem referencji a rzeczywistym obiektu
podstawowymi błędami programistycznymi
```

---

### Zadanie 3 – samodzielna implementacja

Jest to największa część kolokwium.

Na podstawie opisu należy SAMODZIELNIE zaprojektować i zaimplementować niewielki system obiektowy.

W zadaniu mogą pojawić się między innymi:

```text
klasy
konstruktory
walidacja danych
hermetyzacja
static
dziedziczenie
klasy abstrakcyjne
interfejsy
polimorfizm
@Override
toString()
klasy anonimowe
```

Należy również przygotować klasę `Main` pokazującą działanie stworzonego rozwiązania.

---

### Zadanie 4 – refaktoryzacja i projektowanie

Otrzymacie działający lub częściowo poprawny projekt, którego struktura obiektowa wymaga poprawy.

Należy rozpoznać między innymi:

```text
IS-A
HAS-A
dziedziczenie
kompozycję
polimorfizm
```

oraz przebudować kod tak, aby wykorzystywał poprawne relacje pomiędzy klasami.

Może być również wymagane krótkie uzasadnienie decyzji projektowej w komentarzu.

---

# Ogólne wymagania

Dla każdego zadania należy utworzyć osobny podpakiet, np.:

```text
pl.edu.uwm.wmii.programowanie.obiektowe.s123456.z1
pl.edu.uwm.wmii.programowanie.obiektowe.s123456.z2
pl.edu.uwm.wmii.programowanie.obiektowe.s123456.z3
pl.edu.uwm.wmii.programowanie.obiektowe.s123456.z4
```

Każda publiczna klasa oraz każdy publiczny interfejs powinny znajdować się w osobnym pliku.

### Powodzenia! :)