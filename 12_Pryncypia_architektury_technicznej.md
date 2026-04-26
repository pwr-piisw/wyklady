# Pryncypia Architektury Technicznej

**Autorzy:** Maciej Małecki, Marek Matczak
**Miejsce i data:** Wrocław, 20.11.2024

---

## Czym jest architektura?

Architektura oprogramowania to proces planowania i nadawania struktury systemowi, aby spełniał on określone wymagania jakościowe i funkcjonalne. Jest to tylko jedna z wielu definicji.

Inna, dość popularna, pochodzi od Martina Fowlera (cytującego Ralpha Johnsona):

> *„Architektura to te decyzje, które są jednocześnie istotne i trudne do zmiany."*
> — Ralph Johnson, quoted by Martin Fowler

Niezależnie od przyjętej definicji — czy mówimy o planowaniu, czy o decyzjach — w trakcie tworzenia architektury stosujemy określone zasady, które pomagają w tworzeniu oprogramowania zgodnego z wymaganiami oraz łatwego w utrzymaniu przez dłuższy czas.

Do tych zasad należą m.in.:

- podział odpowiedzialności,
- minimalizacja zależności,
- zasada odwrócenia zależności,
- ukrywanie informacji,
- jednorodność,
- kontrola redundancji,
- różnicowanie kategorii oprogramowania,
- warstwowość,
- projektowanie kontraktowe.

Zastosowanie tych zasad wspomaga zarządzanie złożonością oprogramowania, sprawia, że kod jest zrozumiały, a także zwiększa elastyczność całego rozwiązania. Krótko mówiąc, znajomość tych zasad jest niezbędna w pracy każdego architekta, aby tworzyć niezawodne rozwiązania.

*Źródło: https://pwr-piisw.github.io/wyklady/03-backend.html#/2*

---

## Czy rzeczywiście potrzebujemy architektury?

*Źródło: Design Stamina Hypothesis, M. Fowler*

---

# Zasady podstawowe

1. Zasada rozdziału odpowiedzialności
2. Zasada minimalizacji zależności
3. Zasada odwracania zależności
4. Zasada ukrywania informacji
5. Zasada jednorodności
6. Zasada kontrolowanej nadmiarowości

---

## 1. Zasada rozdziału odpowiedzialności (Separation of Concerns)

**Przykłady zastosowania:**

- Adaptery w architekturze **Ports and Adapters** rozdzielają odpowiedzialności związane z komunikacją z bazą danych i komunikacją przez protokół HTTP.
- Odpowiedzialności biznesowe (np. przygotowanie dokumentu i jego weryfikacja) są rozdzielone za pomocą modułów Maven/Gradle lub pakietów w Javie.

---

## 2. Zasada minimalizacji zależności (Minimize Dependencies)

- Unikaj cykli w grafie zależności.
- Komponenty powinny mieć silną spójność (*strong cohesion*).
- Komponenty powinny być ze sobą luźno powiązane (*loose coupling*).

---

## 3. Zasada odwracania zależności (Dependency Inversion)

- Kod wysokiego poziomu nie powinien zależeć od kodu niskopoziomowego.
- Abstrakcja nie powinna zależeć od szczegółów, lecz to szczegóły powinny zależeć od abstrakcji.

**Korzyści z tej zasady:**

- Separacja niskiego i wysokiego poziomu.
- Łatwość użycia.
- Łatwość wymiany.

### Przykłady

- Windows Graphics Architecture
- Architektura portów i adapterów

---

## 4. Zasada ukrywania informacji (Information Hiding)

- Element konstrukcyjny powinien ukrywać swoje wnętrze (*Encapsulation*).
- Zewnętrzni aktorzy powinni wiedzieć o elemencie jedynie niezbędne minimum.

**Korzyści z tej zasady:**

- Minimalizacja zależności.
- Łatwość zmiany wnętrza.

### Przykłady

- Programowanie obiektowe
- Szyna zdarzeń

---

## 5. Zasada jednorodności (Homogeneity)

> Używaj podobnych rozwiązań dla podobnych problemów.

**Korzyści z tej zasady:**

- Przyspieszenie prac w przyszłości (rozwiązanie jest gotowe).
- Łatwość uczenia dla nowych członków zespołu.

> ⚠️ **UWAGA:** technologie oraz rozwiązania mogą ewoluować!

### Przykłady

- Ujednolicony stos technologiczny
- Ujednolicony styl programowania
- API first
- Reactive
- AOP (Aspect-Oriented Programming)

---

## 6. Zasada kontrolowanej nadmiarowości (DRY, Controlled Redundancy)

> *„The first time you do something, just do it.
> The second time, you do it similarly.
> The third time, you refactor."*
> — Martin Fowler

Identyczna funkcjonalność należąca do tego samego właściciela biznesowego powinna być wyodrębniona do współdzielonego bloku, np. serwisu lub komponentu.

---

# Zasady pochodne

Wszystkie te zasady wywodzą się z zasad podstawowych.

1. Zasada kategoryzacji oprogramowania
2. Zasada warstwowości
3. Zasada kontraktu
4. Zasada suwerenności danych
5. Zasada ponownego użycia

---

## 1. Zasada kategoryzacji oprogramowania

**Wynika z:**

- Zasady rozdziału odpowiedzialności

---

## 2. Zasada warstwowości (Layering)

> Każda warstwa może korzystać tylko z własnych komponentów oraz komponentów warstwy poniżej.

**Wynika z:**

- Zasady rozdziału odpowiedzialności
- Zasady minimalizacji zależności
- Zasady ukrywania informacji
- Zasady jednorodności

---

## 3. Zasada kontraktu (Design by Contract)

- Kontrakt opisuje sposób wykorzystania elementu przez aktora.
- Kontrakt jest abstrakcją ukrywającą szczegóły przed aktorem.

**Wynika z:**

- Zasady ukrywania informacji
- Zasady minimalizacji zależności
- Zasady odwracania zależności

**Korzyści z tej zasady:**

- Niezależność implementacji.
- Niezależność testowania.
- Wysoka stabilność abstrakcji.

---

## 4. Zasada suwerenności danych (Data Sovereignty)

- Dane mają jedynego właściciela, który zarządza dostępem do ich modyfikacji.
- Dostęp „do odczytu" może być jednak zdecentralizowany.

**Wynika z:**

- Zasady ukrywania informacji
- Zasady rozdziału kwestii
- Zasady minimalizacji zależności

**Korzyści z tej zasady:**

- Łatwe zachowanie spójności.
- Rozszerzalność.
- Skalowalność.

### Przykłady

- **CQRS** – Command and Query Responsibility Segregation
  *Źródło: https://martinfowler.com*
- **Data mesh**
  *Źródło: https://www.datamesh-architecture.com/*

---

## 5. Zasada ponownego użycia (Re-use)

- **Don't Repeat Yourself.**
- Nie stosuj tej zasady „na siłę" — często mechanizmy lub dane są podobne tylko pozornie.

**Wynika z:**

- Zasady kontrolowanej nadmiarowości

**Korzyści z tej zasady:**

- Łatwość utrzymania.
- Szybkość pracy.

---

# Pytania?


