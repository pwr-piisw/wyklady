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

Odwzorowanie obiektowo-relacyjne (czyli ORM) jest mechanizmem, który posiada następujące cechy:
* Przekształca obiekty w rekordy w bazie danych i odwrotnie.
* Mapuje typy danych specyficzne dla danego języka oraz typy danych obsługiwane przez bazę danych.
* Realizuje obiektowy język zapytań oraz mapuje go na język SQL.
* Zapewnia trwałość obiektów, tzn. każda zmiana na drzewie obiektów powinna być zapisana w bazie danych.
* Przekształca połączenia między obiektami na powiązania międzytabelowe w bazie danych (bazujące na kluczach obcych oraz tabelach łączących).

Odwzorowanie obiektowo-relacyjne jest obecnie najczęsciej wykorzystywaną techniką utrwalania danych biznesowych w ekosystemie Javy.

### Repository
Wzorzec Repository jest często używanym wzorcem wszędzie tam, gdzie istnieje komunikacja z mechanizmami utrwalania danych, w tym z bazami danych. Wzorzec ten często używany jest razem z narzędziami typu ORM jak Hibernate, ale także może być używany niezależnie, jako element tzw. czystej architektury.

![Wzorzec Repository](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/develop/konspekt/repository.puml)

_Prawidłowe_ zastosowanie wzorca Repository pozwala na odseparowanie szczegółów technicznych związanych z utrwalaniem od logiki biznesowej.

Repozytorium oferuje najczęściej następujące funkcjonalności:
- Funkcje typu CRUD, tj. Create, Read, Update, Delete.
- Funkcje typu find, czyli mniej lub bardziej złożone zapytania zwracające kolekcje obiektów na podstawie zadanych warunków wyszukiwania.
- Funkcje typu bulk-update, czyli złożone funkcjonalności pozwalające na masowe i efektywne modyfikowanie utrwalonych danych.

Wzorzec repozytorium ma zastosowanie w architekturze heksagonalnej (ports and adapters): warstwa domenowa deklaruje interfejs repozytorium, który następnie jest implementowany w warstwie aplikacyjnej.

### Hibernate
Hibernate jest biblioteką ORM dla ekosystemu Javy. Zdobyła ona największą popularność, jej twórcy uczyli się na błędach wcześniejszych rozwiązań takich jak EJB Entity Beans (a po częsci także JDO). Biblioteka Hibernate stała się inspiracją dla specyfikacji JPA, nowsze wersje Hibernate zresztą tę specyfikację implementują.

W ramach wykładu szerzej zapoznamy się ze specyfikacją JPA, warto jednak pamiętać, że w prawie każdym wypadku będziemy mieli do czynienia z realizacją opartą właśnie na silniku Hibernate.

Hibernate opiera się na obiektach POJO (Plain Java Object), co było istotną zmianą w stosunku do wcześniejszych rozwiązań, gdzie często konieczne było implementowanie specyficznych interfejsów lub rozszerzanie wygenerowanych klas. Hibernate tego nie wymaga, przez co rozwiązanie jest quasi-transparentne dla logiki biznesowej go używającego (quasi, ponieważ istnieje wiele efektów ubocznych tego mechanizmu, o czym później).

Hibernate opiera się na kilku podstawowych koncepcjach:
- Encja - to obiekt POJO, który mapowany jest przez Hibernate na rekord tabeli relacyjnej bazy danych.
- Asocjacja - to kolekcja łącząca ze sobą obiekty różnych klas; asocjacje mapowane są na powiązania międzytabelowe relacyjnej bazy danych. Asocjacje pozwalają na realizację krotności 1-1, 1-n, n-n, 0-n, itd.
- SessionManager - obiekt zarządzający sesjami.
- Session - sesja Hibernate, w ramach której zarządzane są transakcje oraz w ramach której można dokonywać przekształceń modelu relacyjnego na obiektowy i vice versa.
- HQL - język zapytań Hibernate, pozwalający na realizowanie złożonych zapytań do baz danych. Język ten jest niezależny od dialektu SQL. Hibernate dokonuje odpowiednich translacji do natywnego SQLa.

W pierwszych wersjach Hibernate, mapowanie realizowane było za pomocą plików `.hbm.xml`, obecnie najczęściej wykorzystuje się adnotacje języka Java.

### JPA
Jak wspomniano wcześniej, obecnie rzadko wykorzystuje się Hibernate bezpośrednio. Najczęsciej wykorzystywany jest mechanizm JPA, który jest standardową specyfikacją Javy.

### Spring Data

### Krytyka mechanizmów ORM

## Transakcje

## Alternatywne metody utrwalania danych

## Testowanie
