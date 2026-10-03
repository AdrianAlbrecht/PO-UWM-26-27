1. Stwórz metodę `static List<Zamowienie> generujZamowienia(int n)`, która wygeneruje listę zamówień o długości `n`. W ciele metody losuj:
   * klienta z listy 10 klientów (imię + nazwisko),
   * datę z zakresu ostatnich 3 lat,
   * kwotę z zakresu 10–5000 zł,
   * status z listy: *NOWE*, *W_REALIZACJI*, *ZAKOŃCZONE*.
   
    Uwzględnij, że:
   * daty nie mogą być w przyszłości,
   * kwota powinna mieć 2 miejsca po przecinku.

2. Napisz metodę `static List<Student> generujStudentow(int n)`, która wygeneruje listę studentów o długości n. W ciele metody losuj:
   * imię + nazwisko (po 5 wariantów),
   * numer indeksu typu 5 losowych cyfr,
   * rok studiów: 1–5,
   * lista ocen → 3–8 ocen z zakresu 2.0–5.0.
   
   Dodatkowo:
   * oceny zaokrąglaj do 0.5,
   * minimum jedna ocena musi być ≥ 4.0.

3. Napisz klasę Wydarzenie z polami: String `tytul`, LocalDate `dataStartu`, int `czasTrwania` oraz `typ` typu wyliczeniowego.
Następnie napisz metodę `static List<Wydarzenie> generujWydarzenia(int n)`, która generuj listę klasy Wydarzenie o długości n. W ciele metody losuj:
   * tytuł wydarzenia z listy 10 nazw,
   * data startu (z najbliższych 30 dni),
   * czas trwania 1–8 godzin,
   * typ: *Spotkanie*, *Zadanie*, *Przypomnienie*.

    Warunek:
   * jeśli typ = Spotkanie → minimalny czas trwania to 1h,
   * jeśli Zadanie → minimalnie 30 minut (przelicz na LocalTime),
   * jeśli Przypomnienie → czas trwania zawsze 0.

4. Stwórz klasę Pojazd z polami: String marka, String model, int rokProdukcji, String numerRejestracyjny.
Następnie stwórz metodę `
static List<Pojazd> generujPojazdy(int n)
`, która wygeneruje listę klasy Pojazd o długości `n`.
W ciele metody losuj:
   * markę z 8 marek,
   * model z 12 modeli,
   * rok produkcji 1990–2022,
   * numer rejestracyjny → 2 litery + 4 cyfry (np. "KR1234").