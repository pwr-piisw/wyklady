# Wykład 4 (2020-03-23) - ORM, JPA, Spring Data
## Wprowadzenie
Wykład ten swoim zakresem obejmuje kwestię dostępu aplikacji Javowych do baz danych, z przyczyn praktycznych skupimy się głównie na relacyjnych bazach danych oraz wykorzystaniu języka SQL, jednakże wspomniane będą też alternatywy.

## JDBC - podstawowe API dostępu do relacyjnych baz danych
JDBC to inaczej Java DataBase Connectivity - jest to mechanizm pozwalający na komunikację z bazami danych wspierającymi język zapytań (najczęściej SQL) oraz operujących na danych reprezentowanych w sposób tabelaryczny. W praktyce mechanizm ten wykorzystywany jest wyłacznie z relacyjnymi silnikami baz danych.

JDBC operuje pojęciem sterownika (ang. driver), który jest implementacją specyficzną dla danego silnika SQL i jest z reguły dostarczana w formie biblioteki przez producenta tego silnika. Sterownik odpowiada za szczegóły związane z nawiązaniem połączenia sieciowego z bazą danych oraz implementuje API JDBC.

![Architektura JDBC](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/master/konspekt/jdbc.puml)

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

![Wzorzec Repository](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/master/konspekt/repository.puml)

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
Jak wspomniano wcześniej, obecnie rzadko wykorzystuje się Hibernate bezpośrednio. Najczęsciej wykorzystywany jest mechanizm JPA, który jest standardową specyfikacją Javy. Jest to podejście zalecane, ponieważ, przynajmniej w teorii, ułatwia to zmianę dostawcy JPA.

#### Modelowanie encji
Encją JPA nazywamy klasę POJO, która zmapowana jest na tabelę (lub kilka tabel) modelu relacyjego. Mapowanie najczęściej dokonane jest przy użyciu adnotacji:
* `@Entity` - określa, że dana klasa definiuje encję JPA.
* `@Id` - wyróżnia właściwość reprezentującą klucz główny.

JPA (a właściwie dostawca, np. Hibernate) dokonuje automatycznie założeń odnośnie nazwy tabeli oraz kolumn w modelu relacyjnym.

Większą kontrolę nad mapowaniem daje dodatkowe zastosowanie poniższych adnotacji:
* `@Table` - pozwala na zdefiniowanie m.in. nazwy tabeli w modelu relacyjnym.
* `@Column` - pozwala na zdefiniowanie m.in. nazwy kolumny, jej typu, rozmiaru w modelu relacyjnym.

```java
@Entity
@Table(name="LIBRARY")
public class LibraryEntity {
	@Id
	private Long id;
	@Column(name = "NAME", length = 30, nullable = false)
	private String name;
	@Column(name = "DOMAIN", length = 5, nullable = true)
	private String domain;
	public LibraryEntity () {
	}

	// getters and setters
}
```

Lista pozostałych użytecznych adnotacji JPA:
* `@Access` - pozwala na określenie miejsca umieszczania adnotacji na polach / getterach.
* `@GeneratedValue`- automatyczne generowanie wartości dla klucza głównego.
* `@Lob` - typ dla dużych danych tekstowych lub binarnych.
* `@Enumerated` - typy wyliczeniowe (enum)
* `@Transient` - pole zaanotowane w ten sposób będzie traktowane jako wyliczane i nie będzie brało udziału w utrwalaniu.
* `@MappedSuperclass` - pozwala na deklarowanie wspólnego komponentu dla wielu encji (nie mylić z trwałym dziedziczeniem, które opisane zostanie później).

Typy "embeddable" są to klasy, które deklarują złożone właściwości encji. Technika ta pozwala na wielokrotne wykorzystywanie kodu. Do deklarowania takich klas służy adnotacja `@Embeddable`:

```java
@Embeddable
public class PersonalData  {
    private String firstName;
    private String lastName;
    private Date birthDate;
}
```

```java
@Entity
public class Author {
    @Embedded
    private PersonalData personalData;
}
```

Właściwości klasy embeddable zostaną zmapowane na odpowiednie kolumny dla encji zawierającej.

JPA specyfikuje mechanizm generowania wartości dla kluczy głównych. Strategie generowania kluczy są niestety specyficzne dla konkretnego silnika baz danych i to jest jeden z aspektów JPA, który nie zapewnia bezproblemowej przenośności.

Dla baz wspierających typ AUTOINCREMENT (jak MSSQL):
```java
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;  
```

Dla baz wspierających sekwencje:
```java
@Id
@SequenceGenerator(name = "bookGen", sequenceName = "BOOK_SEQ")
@GeneratedValue(strategy = GenerationType.SEQUENCE,
		generator = "bookGen")
private Long id;
```

Dla baz, które nie wspierają ani AUTOINCREMENT, ani sekwencji:
```java
@Id
@TableGenerator(
     name="bookGen",
     table="ID_GEN", // opcjonalnie
     pkColumnName="GEN_KEY", // opcjonalnie
     valueColumnName="GEN_VALUE", // opcjonalnie
     pkColumnValue="BOOK_ID_GEN") // opcjonalnie
@GeneratedValue(strategy = GenerationType.TABLE, generator = "bookGen")
private Long id;
```

#### Cykl życia obiektu JPA
Encja JPA czyli instancja obiektu zaadnotowanego jako `@Entity` ma określony cykl życia i może znajdować się w następujących stanach:
* nowy (new/transient) - brak przypisanego identyfikatora oraz brak powiązania z kontekstem trwałości (persistence context). Obiekt taki zachowuje się jak zwykły obiekt Javowy.
* zarządzany (managed) - encja posiada ID i jest powiązana z kontekstem trwałości. JPA zarządza tą encją, śledzi zmiany, jakim podlega oraz zapewnia synchronizację tej encji z bazą danych. JPA odpowiada za materializację leniwych (lazy) cech tej encji a także leniwych asocjacji.
* odłączony (detached) - encja posiada ID, ale nie jest powiązana z kontekstem trwałości. JPA nie zarządza tą encją, wszelkie zmiany nie będą synchronizowane z bazą danych, próba odwołania się do leniwych cech i asocjacji może zakończyć się wyjątkiem wykonania (runtime exception).
* usunięty (removed) - encja została oznaczona jako "do usunięcia". Jest ona wciąż zarządzana przez JPA ale odpowiadający jej rekord w bazie danych zostanie usunięty podczas zatwierdzania transakcji.

Poniżej przedstawiono kompletny diagram przejść stanów encji JPA.

![Diagram przejść stanów encji](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/master/konspekt/entity-lifecycle.puml)

JPA pozwala na wykonywanie dodatkowych akcji podczas przejść między stanami encji. Akcje te można wykonać w ramach metod klasy encji oznaczonych odpowiednimi adnotacjami:
* `@PrePersist`
* `@PostPersist`
* `@PreUpdate`
* `@PostUpdate`
* `@PostLoad`
* `@PreRemove`
* `@PostRemove`

Metody powyższe powinny zawsze być bez parametrowe i nie powinny zwracać żadnej wartości (`void`).

#### Entity Manager
Entity Manager jest serwisem zarządzającym sesją JPA (jest to odpowiednik obiektu Session w Hibernate). Zawiera metody służące do obsługi cyklu życia encji, do wykonywania zapytań, do obsługi transakcji oraz blokad.

```java
    EntityManager em = ...

    // wczytanie encji o ID = 123    
    Product product = em.find(Product.class, 123);

    // usunięcie encji
    em.remove(product);

    // utrwalenie nowej encji
    Product newProduct = new Product(123, "foo");
    em.persist(newProduct);

    // zapytanie JPQL
    
```

Instancję `EntityManager` uzyskujemy:
* ręcznie, z użyciem `EntityManagerFactory`,
* z kontenera, poprzez mechanizm dependency injection (używamy adnotacji `@PersistenceContext`),
* w aplikacji Springowej zależność ta jest wstrzykiwana przez Springa.

#### Obiektowy język zapytań JPQL
JPA wprowadza nowy język zapytań, niezależny od dialektu SQL. Jest to odpowiednik (bardzo zbliżony) języka HQL z Hibernate.

Składnia języka JPQL przypomina nieco SQL jednak zamiast tabelami operujemy w nim encjami, ich właściwościami oraz asocjacjami między encjami.

Podstawowym elementem JPQL jest projekcja, do której wykorzystujemy klauzulę `SELECT`:
```jpaql
SELECT a FROM Author a
```
Tak określone zapytanie zwraca wszystkie encje typu `Author`. W praktyce, wykonanie tego zapytania w Javie wyglądać będzie następująco:
```java
List<Author> authors = em.createQuery("SELECT a FROM Author a").getResultList();
```
przy czym `Author` musi być encją JPA.

Istnieje bardzo użyteczna forma projekcji z wykorzystaniem słowa kluczowego `new`, która pozwala zmapować wynik zapytania na dowolny obiekt Javy, przy założeniu, że istnieje odpowiedni konstruktor:
```java
class AuthorCount {
    public AuthorCount(String lastName, int count) {
        ...
    }   
}

List<AuthorCount> authors = em.createQuery(
    "SELECT new io.github.pwrpiiws.AuthorCount(a.lastName, COUNT(a))" + 
    " FROM Author a GROUP BY a.department")
```

Zapytania można zawężać stosując klauzulę `WHERE`:
```jpaql
SELECT a FROM Author a WHERE a.name LIKE 'Sta%' AND a.age > 40
```

Zapytania można sortować stosująć klauzulę `ORDER BY`. Zapytania można agregować stosująć klauzulę `GROUP BY`, można stosować także warunki zawężające dla agregatów stosując klauzulę `HAVING`.

`EntityManager` umożliwia parametryzowanie zapytań przy pomocy nazwanych parametrów, np:

```java
List<Author> authors = em.createQuery(
    "SELECT a FROM Author a WHERE a.age > :age and a.name = :name")
        .setParameter("age", 40)
        .setParameter("name", "Stanislaw")
        .getResultList();
```

#### Modelowanie asocjacji
Asocjacja jest relacją między encjami. W przypadku języka Java, w zależności od krotności asocjacje realizujemy jako zbiory (Set), listy (List) lub referencje (dla krotności `0..1` lub `1`). W przypadku baz relacyjnych zależności takie realizujemy przy pomocy kluczy obcych (relacje 1-1, 1-n) lub tabel łaczących (n-n).

Mapowanie obiektowo relacyjne realizowane jest w przypadku asocjacji przy pomocy adnotacji:
* `@OneToOne` - relacja jeden do jednego, realizowana przy użyciu referencji do obiektu
* `@OneToMany` - releacja jeden do wielu (opcjonalna lub nie), realizowana przy pomocy referencji
* `@ManyToOne` - relacja jeden do wielu realizowana przy pomocy kolekcji
* `@ManyToMany` - relacja wiele do wielu realizowana przy pomocy obustronnej kolekcji

Asocjacje można modelować jako jednokierunkowe jak i dwukierunkowe. Kierunek oznacza możliwość nawigacji po grafie obiektów z encji A do encji B. W przypadku asocjacji dwukierunkowych jeden z kierunków musi być kierunkiem pasywnym (tj. JPA nie śledzi zmian w kolekcji realizującej danych kierunek).

Kolekcje mogą być leniwe albo natychmiastowe (odpowiednio lazy i eager). Zadeklarowanie asocjacji jako eager oznacza konieczność wczytania wszystkich elementów danej kolekcji przez JPA. Zadeklarowanie wszystkich asocjacji jako eager oznacza konieczność każdorazowego podnoszenia grafu obiektów - jest do zdecydowanie niezalecane podejscie.

Kolekcje typu lazy oznaczają, że dane zostaną wczytane dopiero wtedy, gdy będzie taka potrzeba, tj. podczas iterowania kolekcji lub przy dostępie do właściwości obiektów. W praktyce oznacza to, że JPA generuje obiekty proxy, które udają obiekty docelowe i które realizują funkcję wczytywania leniwego.

W przypadku odłączenia encji zawierającej leniwe asocjacje, przy próbie dostępu do niezainicjalizowanych kolekcji nastąpi błąd wykonania.

Asocjacje JPA oferują funkcję kaskadowej propagacji. Oznacza to propagację operacji wykonywanej na encji bazowej na encje powiązane. Następujące operacje mogą być realizowane kaskadowo:
* PERSIST
* MERGE
* REMOVE

Możliwe jest także uzyskanie pełnej kaskadowości (opcja ALL).

<!--
#### Modelowanie dziedziczenia
* Single table
* Table per class
* Joined

### Spring Data
* Czym jest Spring Data: repozytorium
* Query by convention
* Crud repository

### Query DSL

### Krytyka mechanizmów ORM
* Zbędna warstwa abstakcji
* Złudna abstrakcja
* Kłopoty wydajnościowe
* Kłopotliwy cykl życia
* Bardzo wysoka złożoność biblioteki

## Transakcje
* Cechy transakcji: ACID
* Poziomy izolacji
* Realizacja izolacji transakcji
* EntityManager
* Spring i Transactional
* JPA i lock
* Blokady optymistyczne i pesymistyczne

## Alternatywne metody utrwalania danych
* Reaktywny driver R2DBC
* Narzędzia NOSQL:
** Bazy Grafowe: NeoDB
** MongoDB
** Elastic Search

## Testowanie
-->
