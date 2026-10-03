1. Stwórz klasę Pracownik, która posiada pola: LocalDate dataZatrudnienia, LocalDate dataZwolnienia.
   Dodaj metodę prywatną wyliczStaz(), która wylicza całkowity staż pracy w pełnych miesiącach, uwzględniając przypadki:
   * data zwolnienia = null (pracuje do dziś)
   * data zwolnienia < data zatrudnienia → rzuć wyjątek
   * przejścia przez lata (np. od października 2020 do lutego 2023)
   
   Użyj metody w konstruktorze.
2. Stwórz metodę statyczną generujRaty(LocalDate start, int liczbaRat), która zwraca listę dat przypadających co miesiąc, ale:
   * jeśli data przypadnie na weekend – przenieś na najbliższy poniedziałek,
   * jeśli miesiąc ma mniej dni, a rata przypada 31., 30. itd., automatycznie przesuwaj na ostatni dzień miesiąca.
3. Napisz metodę `boolean czyKonflikt(LocalDate start1, LocalDate koniec1, LocalDate start2, LocalDate koniec2)`, która sprawdza, czy przedziały nakładają się choćby jednym dniem.
   Zwróć uwagę na sytuacje:
   * przedziały stykają się jednym dniem,
   * jeden przedział jest w całości wewnątrz drugiego,
   * przedziały nie zachodzą na siebie.
4. Stwórz metodę `LocalDate dopasujDoDniaRoboczego(LocalDate data)`, która:
   * jeśli data przypada w sobotę – przesuwa na poniedziałek,
   * jeśli niedziela – również na poniedziałek,
   * jeśli w święto (podaj 3 przykładowe własne święta) – przesuwa o 1 dzień dalej, aż trafi na dzień roboczy.
5. Stwórz metodę `String roznicaDat(LocalDate d1, LocalDate d2)`, która  zwróci różnicę w czytelnej formie, np.:
„2 lata, 3 miesiące i 12 dni”.
6. Stwórz metodę `LocalDate nastepneUrodziny(LocalDate dataUrodzenia)`, która:
   * liczy najbliższą przyszłą datę urodzin,
   * obsługuje daty 29 lutego (lata nieprzestępne → 28 lutego lub 1 marca — wybierz jedną i opisz zasadę),
   * zwraca dodatkowo liczbę dni do urodzin.