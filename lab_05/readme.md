# **Programowanie Obiektowe – Java (2026Z)**

## **Lab 5: Polimorfizm**

---

## 1. Cel zajęć

Celem laboratorium jest praktyczne poznanie **polimorfizmu dynamicznego** w języku Java.

Podczas zajęć omówione zostaną:

- typ referencji i rzeczywisty typ obiektu,
- przypisywanie obiektu klasy pochodnej do referencji typu bazowego,
- **upcasting**,
- dynamiczne wiązanie metod,
- polimorficzne tablice i kolekcje obiektów,
- projektowanie metod przyjmujących typ bazowy,
- ograniczenia wynikające z typu referencji,
- **downcasting**,
- `instanceof`,
- pattern matching dla `instanceof`,
- bezpieczne i niebezpieczne rzutowanie,
- sytuacje, w których konieczność częstego rzutowania może świadczyć o złym projekcie.

Po zakończeniu zajęć osoba studencka powinna:

- rozumieć różnicę między typem referencji a typem obiektu,
- potrafić wyjaśnić, która implementacja nadpisanej metody zostanie wykonana,
- świadomie stosować typ bazowy jako typ zmiennej, parametru i elementu tablicy,
- rozumieć działanie upcastingu,
- potrafić bezpiecznie wykonać downcasting,
- poprawnie używać `instanceof`,
- rozumieć przyczyny `ClassCastException`,
- potrafić projektować kod wykorzystujący wspólny typ zamiast wielu instrukcji warunkowych.

> **Uwaga:** klasy abstrakcyjne i interfejsy zostaną wprowadzone na następnym laboratorium.  
> W tym laboratorium wykorzystujemy zwykłe klasy bazowe, aby skupić się na samym mechanizmie polimorfizmu.

---

# 2. Co to jest polimorfizm?

Słowo **polimorfizm** oznacza „wiele form”.

W programowaniu obiektowym chodzi o możliwość traktowania obiektów różnych klas pochodnych jako obiektów wspólnego typu bazowego, przy jednoczesnym zachowaniu ich własnego zachowania.

Przykład:

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

```java
public class Cat extends Animal {

    @Override
    public void makeSound() {
        System.out.println("Miau!");
    }
}
```

Możemy napisać:

```java
Animal first = new Dog();
Animal second = new Cat();

first.makeSound();
second.makeSound();
```

Wynik:

```text
Hau!
Miau!
```

Mimo że obie zmienne mają typ:

```text
Animal
```

wykonywana jest implementacja zależna od rzeczywistego typu obiektu.

---

# 3. Typ referencji a typ obiektu

Rozważmy:

```java
Animal animal = new Dog();
```

W tym zapisie występują dwa różne typy.

### Typ referencji

```java
Animal
```

Typ referencji określa przede wszystkim, jakie składowe są dostępne z punktu widzenia kompilatora.

### Rzeczywisty typ obiektu

```java
Dog
```

Rzeczywisty typ obiektu wpływa na to, która implementacja **nadpisanej metody** zostanie wykonana w czasie działania programu.

---

# 4. Przykład

```java
public class Dog extends Animal {

    @Override
    public void makeSound() {
        System.out.println("Hau!");
    }

    public void digHole() {
        System.out.println(
                "Pies kopie dziurę."
        );
    }
}
```

Kod:

```java
Animal animal = new Dog();

animal.makeSound();
```

wykona:

```text
Hau!
```

Natomiast:

```java
// animal.digHole();
```

nie skompiluje się.

Dlaczego?

Ponieważ typ referencji:

```text
Animal
```

nie deklaruje metody:

```text
digHole()
```

---

# 5. Najważniejsza zasada

W uproszczeniu:

> **Typ referencji określa, co wolno wywołać.**

> **Typ obiektu określa, która wersja nadpisanej metody zostanie wykonana.**

Dla:

```java
Animal animal = new Dog();
```

kompilator sprawdza kod z perspektywy:

```text
Animal
```

ale przy wywołaniu nadpisanej metody JVM wybiera implementację właściwą dla:

```text
Dog
```

---

# 6. Dynamiczne wiązanie

Mechanizm wyboru odpowiedniej implementacji metody w czasie działania programu nazywamy:

```text
dynamicznym wiązaniem
dynamic binding
dynamic method dispatch
```

Przykład:

```java
Animal animal = new Dog();

animal.makeSound();
```

Kompilator wie, że:

```java
Animal
```

posiada metodę:

```java
makeSound()
```

Natomiast dopiero podczas wykonywania programu wybierana jest nadpisana implementacja z klasy:

```text
Dog
```

---

# 7. Co jest polimorficzne?

Polimorfizm dynamiczny dotyczy przede wszystkim **nadpisywanych metod instancji**.

Nie należy zakładać takiego samego zachowania dla pól.

Przykład:

```java
public class Parent {

    public String name = "Parent";

    public String getName() {
        return "Parent";
    }
}
```

```java
public class Child extends Parent {

    public String name = "Child";

    @Override
    public String getName() {
        return "Child";
    }
}
```

Kod:

```java
Parent object = new Child();

System.out.println(object.name);
System.out.println(object.getName());
```

Wynik:

```text
Parent
Child
```

Pole jest wybierane na podstawie typu referencji, natomiast nadpisana metoda podlega dynamicznemu wiązaniu.

> Z tego powodu nie należy projektować hierarchii w oparciu o ukrywanie pól.

---

# 8. Upcasting

**Upcasting** oznacza potraktowanie obiektu klasy pochodnej jako obiektu klasy bazowej.

```java
Dog dog = new Dog();

Animal animal = dog;
```

Można też od razu:

```java
Animal animal = new Dog();
```

Upcasting jest bezpieczny i zazwyczaj wykonywany automatycznie.

Dlaczego?

Ponieważ każdy:

```text
Dog
```

jest również:

```text
Animal
```

zgodnie z relacją IS-A.

---

# 9. Co tracimy przy upcastingu?

Nie tracimy samego obiektu.

Zmienia się jedynie sposób, w jaki możemy się do niego odwoływać przez daną referencję.

```java
Dog dog = new Dog();

Animal animal = dog;
```

Przez:

```java
dog
```

możemy wywołać:

```java
dog.makeSound();
dog.digHole();
```

Przez:

```java
animal
```

możemy wywołać tylko metody znane typowi `Animal`:

```java
animal.makeSound();

// animal.digHole(); // błąd kompilacji
```

Obie zmienne nadal mogą wskazywać na **ten sam obiekt**.

---

# 10. Polimorficzna tablica

Jedną z największych zalet polimorfizmu jest możliwość przechowywania obiektów różnych klas pochodnych pod wspólnym typem.

```java
Animal[] animals = {
        new Dog(),
        new Cat(),
        new Cow(),
        new Animal()
};
```

Możemy następnie napisać:

```java
for (Animal animal : animals) {
    animal.makeSound();
}
```

Każdy obiekt wykona własną wersję metody.

Przykładowy wynik:

```text
Hau!
Miau!
Muu!
Zwierzę wydaje dźwięk.
```

Kod pętli nie musi znać wszystkich konkretnych klas.

---

# 11. Dlaczego to jest ważne?

Bez polimorfizmu moglibyśmy próbować pisać:

```java
if (type.equals("dog")) {
    ...
} else if (type.equals("cat")) {
    ...
} else if (type.equals("cow")) {
    ...
}
```

Przy każdej nowej klasie trzeba byłoby modyfikować taki kod.

Z polimorfizmem:

```java
for (Animal animal : animals) {
    animal.makeSound();
}
```

dodanie nowej klasy:

```text
Sheep
```

nie wymaga zmiany pętli.

---

# 12. Metoda przyjmująca typ bazowy

Polimorfizm jest szczególnie przydatny w parametrach metod.

```java
public static void presentAnimal(
        Animal animal
) {
    animal.makeSound();
}
```

Do metody można przekazać:

```java
presentAnimal(new Dog());
presentAnimal(new Cat());
presentAnimal(new Cow());
```

Nie trzeba pisać:

```java
presentDog(...)
presentCat(...)
presentCow(...)
```

---

# 13. Metoda zwracająca typ bazowy

Metoda może również deklarować typ bazowy jako typ zwracany:

```java
public static Animal createAnimal(
        String type
) {
    if ("dog".equals(type)) {
        return new Dog();
    }

    if ("cat".equals(type)) {
        return new Cat();
    }

    return new Animal();
}
```

Kod wywołujący może pracować na wspólnym typie:

```java
Animal animal =
        createAnimal("dog");

animal.makeSound();
```

W późniejszych laboratoriach poznamy lepsze sposoby projektowania takich mechanizmów, ale przykład pokazuje, że metoda nie musi ujawniać konkretnej klasy zwracanego obiektu.

---

# 14. Projektowanie przez typ bazowy

Załóżmy hierarchię:

```text
Employee
├── Developer
├── Tester
└── Manager
```

Zamiast pisać:

```java
public static void showDeveloper(
        Developer developer
) {
}
```

oraz:

```java
public static void showTester(
        Tester tester
) {
}
```

możemy napisać:

```java
public static void showEmployee(
        Employee employee
) {
    employee.showInformation();
}
```

Jeżeli metoda potrzebuje wyłącznie zachowania dostępnego w:

```text
Employee
```

nie powinna wymagać bardziej szczegółowego typu.

---

# 15. Polimorfizm a rozszerzalność

Przykład:

```java
public static void processPayment(
        Payment payment,
        double amount
) {
    payment.pay(amount);
}
```

Jeżeli później powstaną:

```text
CardPayment
BlikPayment
TransferPayment
```

metoda:

```java
processPayment(...)
```

nie musi zostać zmieniona, o ile wszystkie klasy odpowiednio nadpisują:

```java
pay(...)
```

To jedna z głównych zalet projektowania przez wspólny typ.

> Na Lab 6 zobaczymy, że typem wspólnym nie musi być zwykła klasa – może nim być również klasa abstrakcyjna albo interfejs.

---

# 16. Downcasting

**Downcasting** oznacza rzutowanie referencji typu bazowego na bardziej szczegółowy typ.

Przykład:

```java
Animal animal = new Dog();

Dog dog = (Dog) animal;
```

Teraz:

```java
dog.digHole();
```

jest dostępne.

---

# 17. Dlaczego downcasting jest ryzykowny?

Kompilator może dopuścić:

```java
Animal animal = new Cat();

Dog dog = (Dog) animal;
```

ale w czasie działania programu otrzymamy:

```text
ClassCastException
```

Obiekt jest bowiem:

```text
Cat
```

a nie:

```text
Dog
```

---

# 18. `instanceof`

Przed downcastingiem można sprawdzić rzeczywisty typ obiektu.

```java
if (animal instanceof Dog) {
    Dog dog = (Dog) animal;

    dog.digHole();
}
```

Operator:

```java
instanceof
```

sprawdza, czy obiekt może być traktowany jako wskazany typ.

---

# 19. Pattern matching dla `instanceof`

Współczesna Java umożliwia zapis:

```java
if (animal instanceof Dog dog) {
    dog.digHole();
}
```

Nie trzeba osobno pisać:

```java
Dog dog = (Dog) animal;
```

Zmienna:

```text
dog
```

jest dostępna w miejscu, w którym kompilator wie, że warunek został spełniony.

---

# 20. Przykład

```java
public static void specialAction(
        Animal animal
) {
    if (animal instanceof Dog dog) {
        dog.digHole();
    } else if (animal instanceof Cat cat) {
        cat.climb();
    } else {
        System.out.println(
                "Brak specjalnej akcji."
        );
    }
}
```

Kod jest poprawny, ale prowadzi do ważnego pytania:

> Czy częste sprawdzanie konkretnego typu jest rzeczywiście potrzebne?

---

# 21. `instanceof` – narzędzie, nie domyślna strategia

Jeżeli kod wygląda tak:

```java
if (animal instanceof Dog dog) {
    dog.makeSound();
} else if (animal instanceof Cat cat) {
    cat.makeSound();
} else if (animal instanceof Cow cow) {
    cow.makeSound();
}
```

to `instanceof` jest całkowicie niepotrzebne.

Wystarczy:

```java
animal.makeSound();
```

To właśnie jest polimorfizm.

`instanceof` ma sens wtedy, gdy rzeczywiście potrzebujemy zachowania charakterystycznego dla konkretnej podklasy, którego nie da się sensownie umieścić we wspólnym kontrakcie.

---

# 22. Zły projekt – ciąg rzutowań

Rozważmy:

```java
for (Animal animal : animals) {
    if (animal instanceof Dog dog) {
        dog.makeSound();
    } else if (animal instanceof Cat cat) {
        cat.makeSound();
    } else if (animal instanceof Cow cow) {
        cow.makeSound();
    }
}
```

Każda nowa podklasa wymaga zmiany tej pętli.

Lepsze:

```java
for (Animal animal : animals) {
    animal.makeSound();
}
```

Kod korzysta wtedy z zachowania zadeklarowanego we wspólnym typie.

---

# 23. `getClass()` a `instanceof`

Możemy sprawdzić dokładną klasę:

```java
animal.getClass() == Dog.class
```

ale nie jest to równoważne:

```java
animal instanceof Dog
```

`instanceof Dog` zwróci `true` również dla obiektu klasy dziedziczącej po `Dog`.

Natomiast:

```java
animal.getClass() == Dog.class
```

sprawdza dokładną klasę obiektu.

Przykładowo, jeżeli:

```java
class GuideDog extends Dog {
}
```

to:

```java
Animal animal = new GuideDog();

System.out.println(
        animal instanceof Dog
); // true

System.out.println(
        animal.getClass() == Dog.class
); // false
```

---

# 24. `null` i `instanceof`

Dla:

```java
Animal animal = null;
```

wyrażenie:

```java
animal instanceof Dog
```

zwróci:

```text
false
```

Nie powoduje `NullPointerException`.

---

# 25. Polimorfizm i `Object`

Ponieważ wszystkie klasy dziedziczą po:

```java
Object
```

możliwe jest:

```java
Object object = new Dog();
```

Jeżeli `Dog` nadpisuje:

```java
toString()
```

to:

```java
System.out.println(object);
```

wywoła implementację:

```java
Dog.toString()
```

To również przykład dynamicznego wiązania.

Jednocześnie typ:

```text
Object
```

jest bardzo ogólny, więc kompilator pozwala przez taką referencję wywołać jedynie operacje dostępne w `Object`.

---

# 26. Przeciążanie a polimorfizm dynamiczny

W niektórych materiałach można spotkać podział:

```text
polimorfizm statyczny – przeciążanie
polimorfizm dynamiczny – nadpisywanie
```

Na tym laboratorium przez **polimorfizm** będziemy przede wszystkim rozumieli polimorfizm dynamiczny związany z:

- dziedziczeniem,
- nadpisywaniem,
- wspólnym typem bazowym,
- wyborem implementacji w czasie działania programu.

Przeciążanie metod zostało omówione na Lab 3 i jest rozstrzygane przez kompilator na podstawie dostępnych typów argumentów.

---

# 27. Ważna pułapka – przeciążanie i typ referencji

Rozważmy:

```java
public static void show(Animal animal) {
    System.out.println("Animal");
}

public static void show(Dog dog) {
    System.out.println("Dog");
}
```

oraz:

```java
Animal animal = new Dog();

show(animal);
```

Wynik:

```text
Animal
```

Dlaczego?

Przeciążenie jest wybierane w czasie kompilacji na podstawie **typu referencji**:

```text
Animal
```

To coś innego niż dynamiczny wybór nadpisanej metody instancji.

---

# 28. Przykład praktyczny – pracownicy

Klasa bazowa:

```java
public class Employee {

    private String name;

    public Employee(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }

    public double calculateSalary() {
        return 0.0;
    }

    public void showInformation() {
        System.out.printf(
                "%s: %.2f zł%n",
                this.name,
                this.calculateSalary()
        );
    }
}
```

Podklasa:

```java
public class Developer extends Employee {

    private double salary;

    public Developer(
            String name,
            double salary
    ) {
        super(name);
        this.salary = salary;
    }

    @Override
    public double calculateSalary() {
        return this.salary;
    }
}
```

Inna podklasa:

```java
public class Manager extends Employee {

    private double baseSalary;
    private double bonus;

    public Manager(
            String name,
            double baseSalary,
            double bonus
    ) {
        super(name);

        this.baseSalary = baseSalary;
        this.bonus = bonus;
    }

    @Override
    public double calculateSalary() {
        return this.baseSalary
                + this.bonus;
    }
}
```

Polimorficzna tablica:

```java
Employee[] employees = {
        new Developer(
                "Anna",
                9000
        ),
        new Manager(
                "Jan",
                10000,
                2500
        )
};
```

Przetwarzanie:

```java
for (Employee employee : employees) {
    employee.showInformation();
}
```

Metoda:

```java
showInformation()
```

z klasy `Employee` wywołuje:

```java
calculateSalary()
```

a ta metoda zostanie dynamicznie wybrana zgodnie z rzeczywistą klasą obiektu.

---

# 29. Wywołanie polimorficzne wewnątrz innej metody

To szczególnie ważny przypadek.

```java
public class Employee {

    public double calculateSalary() {
        return 0;
    }

    public void showInformation() {
        System.out.println(
                this.calculateSalary()
        );
    }
}
```

Jeżeli:

```java
Employee employee =
        new Developer(...);
```

i wywołamy:

```java
employee.showInformation();
```

to wewnątrz:

```java
this.calculateSalary()
```

wykonana zostanie wersja:

```java
Developer.calculateSalary()
```

Dynamiczne wiązanie działa również wtedy, gdy nadpisana metoda jest wywoływana z metody odziedziczonej.

---

# 30. Korzyści z polimorfizmu

### Mniej duplikacji

Jedna metoda może obsłużyć wiele różnych podtypów.

### Rozszerzalność

Można dodawać nowe podklasy bez zmiany kodu korzystającego wyłącznie ze wspólnego typu.

### Czytelniejszy kod

Kod operuje na pojęciach takich jak:

```text
Animal
Employee
Payment
```

zamiast na każdej konkretnej implementacji osobno.

### Słabsze powiązanie

Kod korzystający z typu bazowego nie musi znać szczegółów wszystkich klas pochodnych.

---

# 31. Typowe błędy

## 31.1. Mylenie typu referencji z typem obiektu

```java
Animal animal = new Dog();
```

Nie oznacza to, że obiekt „zamienił się” w `Animal`.

Obiekt nadal jest typu:

```text
Dog
```

---

## 31.2. Próba wywołania metody spoza typu referencji

```java
Animal animal = new Dog();

// animal.digHole();
```

Metoda nie jest znana przez typ `Animal`.

---

## 31.3. Niebezpieczny downcasting

```java
Animal animal = new Cat();

Dog dog = (Dog) animal;
```

Kod kompiluje się, ale podczas działania zgłosi:

```text
ClassCastException
```

---

## 31.4. Nadużywanie `instanceof`

```java
if (animal instanceof Dog) {
    ...
} else if (animal instanceof Cat) {
    ...
}
```

Jeżeli wszystkie gałęzie wykonują tę samą logiczną operację, prawdopodobnie należy wykorzystać nadpisaną metodę.

---

## 31.5. Oczekiwanie polimorfizmu dla pól

```java
Parent object = new Child();

System.out.println(object.name);
```

Pole wybierane jest inaczej niż nadpisana metoda.

---

## 31.6. Mylenie przeciążania z nadpisywaniem

Przeciążanie jest rozstrzygane na etapie kompilacji.

Nadpisywanie metod instancji umożliwia dynamiczne wiązanie podczas działania programu.

---

# **Zadania**

## Zadanie 1 – typ referencji i typ obiektu

Utwórz:

```text
Animal
Dog
Cat
```

`Dog` i `Cat` mają dziedziczyć po `Animal`.

Każda klasa powinna posiadać własną implementację:

```java
makeSound()
```

Następnie utwórz:

```java
Animal first = new Dog();
Animal second = new Cat();
```

Odpowiedz przed uruchomieniem:

1. Jaki jest typ referencji `first`?
2. Jaki jest rzeczywisty typ obiektu?
3. Która wersja `makeSound()` zostanie wykonana?
4. Czy przez `first` można wywołać metodę występującą wyłącznie w `Dog`?

---

## Zadanie 2 – polimorficzna tablica

Rozbuduj poprzednie klasy o:

```text
Cow
Sheep
```

Utwórz:

```java
Animal[] animals
```

zawierającą obiekty wszystkich klas.

Za pomocą **jednej pętli** wywołaj:

```java
makeSound()
```

dla każdego obiektu.

Nie używaj:

```java
instanceof
```

ani:

```java
getClass()
```

---

## Zadanie 3 – metoda przyjmująca typ bazowy

Utwórz metodę:

```java
public static void present(
        Animal animal
)
```

która:

1. wyświetla obiekt,
2. wywołuje `makeSound()`.

Przekaż do niej kolejno:

```java
new Dog()
new Cat()
new Cow()
```

Odpowiedz:

> Dlaczego nie potrzebujemy trzech różnych metod `present()`?

---

## Zadanie 4 – pracownicy

Utwórz:

```text
Employee
Developer
Tester
Manager
```

Każda podklasa ma inaczej obliczać wynagrodzenie przez:

```java
calculateSalary()
```

Utwórz tablicę:

```java
Employee[] employees
```

i wyświetl pensję każdego pracownika w jednej pętli.

Dodanie nowej klasy:

```text
Administrator
```

nie może wymagać zmiany tej pętli.

---

## Zadanie 5 – upcasting

Utwórz:

```java
Dog dog = new Dog();
Animal animal = dog;
```

1. Sprawdź, jakie metody można wywołać przez `dog`.
2. Sprawdź, jakie metody można wywołać przez `animal`.
3. Zmień stan obiektu przez jedną referencję i odczytaj go przez drugą.

Odpowiedz:

> Czy upcasting tworzy nowy obiekt?

---

## Zadanie 6 – downcasting

Do klasy `Dog` dodaj:

```java
digHole()
```

Do `Cat`:

```java
climb()
```

Następnie:

```java
Animal first = new Dog();
Animal second = new Cat();
```

Wykonaj bezpieczny downcasting i wywołaj odpowiednie metody.

Następnie celowo spróbuj:

```java
Dog dog = (Dog) second;
```

i sprawdź, jaki wyjątek zostanie zgłoszony.

---

## Zadanie 7 – `instanceof`

Napisz:

```java
public static void specialAction(
        Animal animal
)
```

Jeżeli obiekt jest:

```text
Dog
```

wywołaj:

```java
digHole()
```

Jeżeli:

```text
Cat
```

wywołaj:

```java
climb()
```

Wykorzystaj współczesną składnię:

```java
if (animal instanceof Dog dog) {
    ...
}
```

---

## Zadanie 8 – usuń niepotrzebne `instanceof`

Otrzymujesz:

```java
public static void makeSound(
        Animal animal
) {
    if (animal instanceof Dog dog) {
        dog.makeSound();
    } else if (animal instanceof Cat cat) {
        cat.makeSound();
    } else if (animal instanceof Cow cow) {
        cow.makeSound();
    }
}
```

Zrefaktoryzuj metodę tak, aby:

- nie używała `instanceof`,
- nie wykonywała żadnego rzutowania,
- poprawnie działała także dla przyszłych podklas `Animal`.

Wyjaśnij, dlaczego nowa wersja jest bardziej rozszerzalna.

---

## Zadanie 9 – pola a metody

Utwórz:

```java
class Parent {
    String value = "Parent";

    String getValue() {
        return "Parent";
    }
}
```

oraz:

```java
class Child extends Parent {
    String value = "Child";

    @Override
    String getValue() {
        return "Child";
    }
}
```

Następnie:

```java
Parent object = new Child();
```

Przed uruchomieniem przewidź wynik:

```java
System.out.println(object.value);
System.out.println(object.getValue());
```

Wyjaśnij różnicę.

---

## Zadanie 10 – przeciążanie kontra nadpisywanie

Utwórz:

```java
static void identify(Animal animal) {
    System.out.println("Animal");
}
```

oraz:

```java
static void identify(Dog dog) {
    System.out.println("Dog");
}
```

Następnie:

```java
Animal animal = new Dog();

identify(animal);
animal.makeSound();
```

Przed uruchomieniem określ:

1. którą wersję `identify()` wybierze kompilator,
2. którą wersję `makeSound()` wykona JVM,
3. dlaczego te dwa wywołania zachowują się inaczej.

---

## Zadanie 11 – `getClass()` i `instanceof`

Utwórz:

```text
Animal
Dog
GuideDog extends Dog
```

Następnie:

```java
Animal animal = new GuideDog();
```

Sprawdź:

```java
animal instanceof Animal
animal instanceof Dog
animal instanceof GuideDog
animal.getClass() == Animal.class
animal.getClass() == Dog.class
animal.getClass() == GuideDog.class
```

Przed uruchomieniem zapisz przewidywane wyniki.

---

## Zadanie 12 – refaktoryzacja systemu płatności

Otrzymujesz kod:

```java
public class Payment {

    private String type;

    public Payment(String type) {
        this.type = type;
    }

    public void pay(double amount) {
        if ("card".equals(this.type)) {
            System.out.println(
                    "Płatność kartą: " + amount
            );
        } else if ("blik".equals(this.type)) {
            System.out.println(
                    "Płatność BLIK: " + amount
            );
        } else if ("transfer".equals(this.type)) {
            System.out.println(
                    "Przelew: " + amount
            );
        }
    }
}
```

Przebuduj rozwiązanie za pomocą dziedziczenia i polimorfizmu.

Utwórz:

```text
Payment
CardPayment
BlikPayment
TransferPayment
```

Wspólny kod powinien móc wykonać:

```java
for (Payment payment : payments) {
    payment.pay(99.99);
}
```

bez sprawdzania konkretnego typu płatności.

---

## Zadanie 13 – polimorficzne przetwarzanie figur

Bez używania klas abstrakcyjnych utwórz klasę bazową:

```text
Shape
```

z metodami:

```java
double area()
String getName()
```

Domyślna implementacja `area()` może zwracać `0.0`.

Utwórz:

```text
Circle
Rectangle
Triangle
```

nadpisujące obie metody.

Napisz:

```java
public static void printShape(
        Shape shape
)
```

która wyświetla nazwę i pole figury.

Następnie przetwórz tablicę:

```java
Shape[] shapes
```

w jednej pętli.

> Na kolejnym laboratorium poprawimy ten projekt za pomocą klasy abstrakcyjnej.

---

# **Zadanie dodatkowe – usuń rzutowania**

Otrzymujesz system:

```java
for (Employee employee : employees) {
    if (employee instanceof Developer developer) {
        System.out.println(
                developer.calculateDeveloperSalary()
        );
    } else if (employee instanceof Manager manager) {
        System.out.println(
                manager.calculateManagerSalary()
        );
    } else if (employee instanceof Tester tester) {
        System.out.println(
                tester.calculateTesterSalary()
        );
    }
}
```

Przebuduj hierarchię tak, aby pętla wyglądała:

```java
for (Employee employee : employees) {
    System.out.println(
            employee.calculateSalary()
    );
}
```

Nie korzystaj w rozwiązaniu z:

```java
instanceof
```

ani jawnego rzutowania.

Odpowiedz:

1. jakie zachowanie musiało zostać przeniesione do wspólnego kontraktu klasy bazowej,
2. dlaczego dodanie nowej podklasy jest teraz prostsze,
3. kiedy mimo wszystko `instanceof` może być uzasadnione.

---

# **Po zajęciach**

Po laboratorium należy potrafić wyjaśnić:

```text
typ referencji
typ obiektu
upcasting
downcasting
dynamiczne wiązanie
instanceof
ClassCastException
```

Należy rozumieć:

```java
Animal animal = new Dog();
```

oraz potrafić odpowiedzieć na dwa osobne pytania:

> **Jakie metody można wywołać przez `animal`?**

i:

> **Która implementacja nadpisanej metody zostanie wykonana?**

Należy również rozumieć, dlaczego kod:

```java
animal.makeSound();
```

jest zwykle lepszy od:

```java
if (animal instanceof Dog) {
    ...
} else if (animal instanceof Cat) {
    ...
}
```

jeżeli wszystkie klasy realizują to samo wspólne zachowanie.

Kolejne laboratorium będzie poświęcone **klasom abstrakcyjnym, metodom abstrakcyjnym, interfejsom oraz porównaniu dziedziczenia z kompozycją**.
