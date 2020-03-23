# Wykład 4 (2020-03-23) - ORM, JPA, Spring Data
## Wprowadzenie
Wykład ten swoim zakresem obejmuje kwestię dostępu aplikacji Javowych do baz danych, z przyczyn praktycznych skupimy się głównie na relacyjnych bazach danych oraz wykorzystaniu języka SQL, jednakże wspomniane będą też alternatywy.

## JDBC - podstawowe API dostępu do relacyjnych baz danych
JDBC to inaczej Java DataBase Connectivity - jest to mechanizm pozwalający na komunikację z bazami danych wspierającymi język zapytań (najczęściej SQL) oraz operujących na danych reprezentowanych w sposób tabelaryczny. W praktyce mechanizm ten wykorzystywany jest wyłacznie z relacyjnymi silnikami baz danych.

JDBC operuje pojęciem sterownika (ang. driver), który jest implementacją specyficzną dla danego silnika SQL i jest z reguły dostarczana w formie biblioteki przez producenta tego silnika. Sterownik odpowiada za szczegóły związane z nawiązaniem połączenia sieciowego z bazą danych oraz implementuje API JDBC.

![Architektura JDBC](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/develop/konspekt/jdbc.puml)

JDBC pozwala na wykorzystanie pełni funkcjonalności języka zapytań:
* Zadawanie zapytań (`Statement`, `PreparedStatement`) i odbieranie wyników (`ResultSet`).
* Wykonywanie operacji modyfikujących i odbieranie ilości zmienionych wierszy.
* Zarządzanie transakcjami (`setTransactionIsolation`, `setAutocommit`, `commit`, `rollback`).
* Wywoływanie procedur składowanych.
* Wykorzystywanie funkcjonalności kursorów, w tym kursorów modyfikowalnych.
* Wykonywanie prekompilowanych zapytań (`PreparedStatement`).

Obiekty typu `PreparedStatement` są wyjątkowo użyteczne, ponieważ po pierwsze pozwalają na szybsze wykonywanie wielu podobnych zapytań, a po drugie pozwalają w bezpieczny sposób parametryzować zapytania.

UWAGA: zaleca sie wykorzystywać `PreparedStatement` wszędzie tam, gdzie musimy dynamicznie zbudować zapytanie, np poprzez przekazanie wartości do warunku `WHERE`. Sklejanie zapytania SQL przy użyciu wartości typu `String` jest potencjalnie niebezpieczne, ponieważ otwiera to możliwości naruszenia bezpieczeństwa, tzw. SQL injection.


## Mapowanie obiektowo-relacyjne
### Hibernate
### JPA
### Spring Data

## Transakcje

## Alternatywne metody utrwalania danych

## Testowanie
