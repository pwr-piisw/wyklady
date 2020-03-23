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
Powszechne od lat 90-tych programowanie obiektowe oraz języki programowania je wspierające stały się standardem w projektowaniu systemów biznesowych. Obiekty, którymi operujemy są strukturami danych alokowanymi w ulotnej pamięci typu RAM. Cechą charakterystyczną systemów biznesowych zaś jest trwałość danych, a zatem także obiektów służących do modelowania tychże danych. Nic więc dziwnego, że ustawicznie trwają starania o to, aby w możliwie bezbolesny sposób umożliwić utrwalanie obiektów ulotnych. W tej częsci zapoznamy się z kilkoma najczęściej używanymi technikami.

Obecnie najczęsciej wykorzystywaną techniką w ekosystemie Javy są tzw. systemy ORM, czyli Object-Relational Mapping, techniki pozwalające na mniej lub bardziej skuteczne mapowanie obiektów i rekordów w relacyjnych bazach danych.

### Repository
Wzorzec Repository jest często używanym wzorcem wszędzie tam, gdzie istnieje komunikacja z mechanizmami utrwalania danych, w tym z bazami danych. Wzorzec ten często używany jest razem z narzędziami typu ORM jak Hibernate, ale także może być używany niezależnie, jako element tzw. czystej architektury.

![Wzorzec Repository](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/develop/konspekt/repository.puml)

_Prawidłowe_ zastosowanie wzorca Repository pozwala na odseparowanie szczegółów technicznych związanych z utrwalaniem od logiki biznesowej.

Repozytorium oferuje najczęściej następujące funkcjonalności:
- Funkcje typu CRUD, tj. Create, Read, Update, Delete.
- Funkcje typu find, czyli mniej lub bardziej złożone zapytania zwracające kolekcje obiektów na podstawie zadanych warunków wyszukiwania.
- Funkcje typu bulk-update, czyli złożone funkcjonalności pozwalające na masowe i efektywne modyfikowanie utrwalonych danych.

### Hibernate
### JPA
### Spring Data

## Transakcje

## Alternatywne metody utrwalania danych

## Testowanie
