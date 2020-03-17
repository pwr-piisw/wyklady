# Wykład 3 (2020-03-16) - backend
## Wprowadzenie
W ramach wykładu poznamy podstawowe pojęcia, narzędzia i technologie związane z wytwarzaniem komponentów uruchamianych na serwerach. Skoncentrujemy się na ekosystemie zbudowanym wokół języka Java.

Uwagi i pytania proszę kierować do: maciej.malecki@pwr.edu.pl

Link do slajdów: [https://pwr-piisw.github.io/wyklady/backend_spring.html#/](https://pwr-piisw.github.io/wyklady/backend_spring.html#/)

## Architektura
Backendem nazywamy zbiór komponentów software'owych realizujących logikę aplikacji, które rozmieszczone oraz wykonywane są na serwerach. Backend z reguły jest silnie wydzielonym fragmentem kodu, który komunikuje się z innymi komponentami przy pomocy dobrze zdefiniowanych interfejsów (tzw. API).

W architekturach systemów biznesowych staramy się w ramach backendu enkapsulować tzw. logikę biznesową, czyli zbiór reguł i struktur danych specyficznych dla danej dziedziny biznesowej. Logika biznesowa jest najcenniejszym zasobem programistycznym, powinna więc podlegać szczególnej ochronie. W szczególności logika biznesowa jako relatywnie stała i niezmienna, powinna być uniezależniona od zmian technologicznych. Jedną z metod ochrony logiki jest zastosowania modelu heksagonalnego architektury, widocznego na diagramie.

![Architektura heksagonalna](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/master/konspekt/architecture-general.puml)

Często stosuje się uproszczony model architektury - architekturę warstwową:

![Architektura warstwowa](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/master/konspekt/architecture-layered.puml)

Architektura warstwowa dopuszcza zależność logiki biznesowej od warstwy repozytorium, przez co możliwe jest bezpośrednie wykorzystywanie encji JPA do modelowania domeny. Pozwala to na prostszy kod i przynosi oszczędności w przypadku niewielkich projektów. Niestety, często architektura warstwowa uzależnia logikę biznesową od warstwy utrwalania, przez co niemożliwe staje się proste wymienienie tej warstwy np poprzez zastosowanie innego systemu bazy danych.

## Automatyzacja budowania oprogramowania
Narzędzia automatyzujące budowanie oprogramowania zapewniają powtarzalność procesu budowania. W przypadku backendów implementowanych w języku Java najczęsciej wykorzystywane to:
* Apache ANT
* Apache Maven
* Gradle

### Apache MAVEN
W ramach wykładu skupimy się na systemie Maven. Maven służy do automatyzacji i standaryzacji procesu budowania oprogramowania. Danymi wejściowymi do systemu Maven są:
* źródło oprogramowania,
* opis projektu stworzony w formie pliku `pom.xml` (Project Object Model).

Drugą ważną cechą systemu Maven jest zarządzanie zależnościami, zarówno wewnętrznymi jak i zewnętrznymi.

Każda zależność określona jest przy pomocy:
* identyfikatora grupy (Group ID, np. `org.hibernate`),
* identyfikatora artefaktu (Artifact ID, np. `hibernate-tools`),
* specyfikatora wersji (np. `5.4.12.Final`).

Zależności rejestrowane są globalnie w tzw. repozytoriach, np. https://search.maven.org. Maven potrafi także używać lokalnych repozytoriów, wykorzystywanych w celach developerskich.

Maven wykorzystuje ustandaryzowaną strukturę projektu zgodnie z ideą "convention over configuration".

![Struktura projektu](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/master/konspekt/maven.puml)

Dla każdego projektu określamy koordynaty (grupa, id artefaktu, wersja) - podobnie jak w przypadku zależności. Ta unifikacja pozwala na to, aby nasz projekt także był zależnością w innym projekcie. W przypadku projektów i zależności Mavenowych obowiązuje tzw. reguła przechodniości - dzięki koordynatom i zależnościom jesteśmy w stanie określać zależności od zależności, itp. W ten sposób powstaje tzw. drzewo zależności projektu.

Przykładowy `pom.xml`:

```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    
    <!-- coordinates of the project -->
    <groupId>com.mycompany</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0.0</version>
    
    <dependencies>
        <dependency>
            <!-- coordinate of the dependency -->
            <groupId>junit</groupId>
            <artifacId>junit</artifacId>
            <version>3.8.1</version>
            <!-- scope in which given dependency is used (here - test only) -->
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

Budowanie przy pomocy Mavena zdefiniowane jest przy użyciu tzw. cyklu życia (ang. Lifecycle). Maven posiada 3 wbudowane cykle życia:
1. clean  - służący do czyszczenia projektu
2. default - służacy dobudowania i deploymentu projektu - artefaktu
3. site - służący do generowania i publikowania dokumentacji

Każdy cykl życia składa się z tworzących kaskadę faz. Uruchomienie dowolnej fazy powoduje automatyczne uruchomienie wszystkich poprzedzających faz danego cyklu życia.

Główne fazy cyklu `default` uszeregowane są następująco:
1. validate
1. compile
1. test-compile
1. test
1. package
1. verify
1. install
1. deploy

Znaczenie istotniejszych faz jest następujące:
- compile - kompilacja plików źródłowych
- test - uruchomienie testów jednostkowych (unit tests)
- package - pakowanie skompilowanego kodu i zasobów w paczki (EAR, WAR, JAR, itd.)
- verify - uruchomienie testów integracyjnych i e2e
- install - instalacja paczek w lokalnym repozytorium Mavena (~/.m2/repo)
- deploy - instalacja paczek w zdalnym repozytorium Mavena

W trakcie budowania zależności projektu są instalowane w lokalnym repozytorium, przez co kolejne uruchomienia Mavena mogą wykonywać się szybciej (brak konieczności ponownego ściągania zależności).

## Spring / SpringBoot
Spring framework powstał w 2003 roku jako szkielet aplikacji biznesowych tworzonych w języku Java. Spring był powszechnie akceptowaną alternatywą dla technologii EJB. W praktyce jednak Spring był implementacją modelu komponentowego przeznaczoną do wewnętrznego modelowania aplikacji (EJB jest rozproszonym modelem komponentowym).

Sercem biblioteki są Spring Core oraz Spring Beans, które implementują mechanizm IoC (inversion of control) oraz wstrzykiwanie zależności (dependency injection). Spring troszczy się także o instancjonowanie komponentów (beans). Spring Context umożliwia dostęp do komponentów zarządzanych przez framework, na zarządzanie konfiguracją, wydzielanie jej do plików `*.properties`.

Konfiguracja aplikacji Springowej może być dokonywana przy pomocy deskryptorów XML (znaczenie głównie historyczne) oraz poprzez mechanizmy refleksji i annotacje (stosowane powszechnie obecnie).

Z biegiem czasu Spring został wzbogacony o wiele bibliotek i modułów:
- Spring security - realizacja autentykacji zgodnej z JAAS oraz autoryzacji
- Spring data - integracja z mechanizmami utrwalania danych jak JDBC, JPA
- Spring batch - mechanizmy pozwalające na wykonywanie długotrwających operacji wykonywanych w tle
- Spring MVC - moduł webowy, implementacja zarówno webowych interfejsów graficznych jak i interfejsów RESTful

### Wstrzykiwanie zależności
Jak już wspomniano, Spring troszczy się o tworzenie instancji obiektów. Tam, gdzie jest to konieczne, Spring może wstrzyknąć zależności, czyli ustawić referencje do innych obiektów, którymi Spring zarządza.

Istnieje wstrzykiwanie przez:
- pola
- settery
- konstruktor

Obecnie najbardziej zalecaną metodą wstrzykiwania zależności jest metoda konstruktorowa. Nie wymaga ona stosowania żadnych dodatkowych annotacji.

```java
@Component
public class CustomerService {
    private final CustomerEventPublisher customerEventPublisher;
    private final CustomerRepository customerRepository;
    
    public CustomerService(CustomerEventPublisher cep, CustomerRepository cr) {
        customerEventPublisher = cep;
        customerRepository = cr;
    }
}
```

### Postawowe annotacje
- `@Component` - oznacza klasę, której instancje (komponenty) są zarządzane przez Springa
- `@Service` - komponenty zawierające logikę biznesową
- `@Repository` - komponenty DAO (Data Access Object), bezpośrednia komunikacja z bazami danych i innymi systemami zewnętrznymi

Wszystkie trzy annotacje czynią z klasy komponent zarządzany przez Springa. Dodatkowo dla `@Repository` Spring konwertuje wyjątki dostawców baz danych, a także w wypadku repozytoriów JPA potrafi wygenerować proste zapytania SQL na podstawie sygnatur metod.

Inne użytecznie annotacje to:
- `@Controller` - oznacza komponent potrafiący przetwarzać żądania HTTP
- `@RequestMapping` - używana w komponentach kontrolera - umożliwia mapowanie adresu URL na metodę
- `@Scope` - umożliwia zmianę domyślnego czasu życia komponentu (dopuszcza wartości: singleton, prototype, request, session, application, websocket; ostatnie cztery wartości są specyficzne dla zastosowań webowych)
- `@PostConstruct` - umożliwia wywołanie dodatkowego kodu po utworzeniu obiektu

Przykład - end point RESTowy

```java
@RequestMapping("/services")
@RestController
public class BooksRestService {

    private final BookService bookService;

    @Autowired
    public BooksRestService(BookService bookService) {
        this.bookService = bookService;
    }

    @RequestMapping(path = "/books", method = RequestMethod.GET)
    public List<BookTo> findBooks(BookSearchCriteria bookSearchCriteria) {
        return bookService.findBooks(bookSearchCriteria);
    }
}
```
### Rozpoczęcie pracy
Jak zacząć: jedyna słuszna metoda w 2020 roku to: https://start.spring.io

## RESTful web services
REST to skrót od Representation State Transfer. REST to styl architektoniczny, który nadaje pewną okresloną semantykę elementom protokołu HTTP tworzącemu serwisy webowe (web services).

### Ograniczenia architektoniczne stylu REST
1. *Architektura klient-serwer*
2. *Bezstanowość* - każde żądanie zawiera pełną informację niezbędną do jego wykonania, tj. kontekst klienta nie jest przechowywany na serwerze.
3. *Buforowalność* (cacheability)
4. *Warstwowość* - dopuszczalne jest wstawianie dodatkowych warstw między klientem i serwerem; warstwy te są przeźroczyste dla kilenta. Warstwy te mogą np realizować funkcje proxy lub load balancera.
5. *Jednolitość interfejsu*

### Jednolitość interfejsu REST
1. Zasoby udostępniane przez interfejs REST są identyfikowalne przez ich lokalizator (URI) oraz typ reprezentacji (MIME type).
2. Typ reprezentacji zasobu może być rózna od formy składowania tego zasobu na serwerze.
3. Zasobami można manipulować (usuwać, zmieniać, dodawać) posiadając jego lokalizację.
4. Samoopisywalność - odpowiedź serwera zawiera komplet informacji opisujących format danych.
5. Hipermedialność - odpowiedź serwera zawiera komplet informacji dotyczących operacji, jakie można wykonać na zasobie, łącznie z linkami.

### Elementy słownika REST
1. Lokalizator (URI) - część adresu - łącza, identyfikująca zasób, np. `\orders\123\lines` określa zbiór linii zamówienia o identyfikatorze `123`.
2. Akcja - czasownik - metoda protokołu HTTP określającą czynność, jaka będzie wykonana na zasobie: `HEAD`, `GET`, `PUT`, `POST`, `DELETE`, `CONNECT`, `OPTIONS`, `TRACE`.
3. Wyróżnik formatu danych wejściowych i wyjściowych: `content-type` akceptujący tzw. MIME types, jak: `application/json`, `text/html`, `application/xml`, itp.

### Podstawowa semantyka lokalizatorów i metod
<table>
  <thead>
    <tr>
      <th>Metoda</th>
      <th>Kolekcja (/orders)</th>
      <th>Element (/orders/123)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>GET</td>
      <td>Pobranie listy elementów kolekcji</td>
      <td>Pobranie elementu</td>
    </tr>
    <tr>
      <td>POST</td>
      <td>Dodanie nowego elementu do kolekcji</td>
      <td>Utworzenie nowego elementu</td>
    </tr>
    <tr>
      <td>PUT</td>
      <td>Zastąpienie kolekcji nową kolekcją</td>
      <td>Modyfikacja istniejącego elementu</td>
    </tr>
    <tr>
      <td>DELETE</td>
      <td>Usunięcie kolekcji</td>
      <td>Usunięcie elementu</td>
    </tr>
  </tbody>
</table>

### Implementacja stylu REST z użyciem Spring Web
```java
@RequestMapping(path = "/cars", method = RequestMethod.GET)
public List<CarTo> findAllCars() { ... }

@RequestMapping(path = "/car", method = RequestMethod.POST)
public CarTo addCar(@RequestBody CarTo car) { ... }

@RequestMapping(path = "/car", method = RequestMethod.PUT)
public CarTo updateCar(@RequestBody CarTo car) { ... }

@RequestMapping(path = "/car/{id}", method = RequestMethod.DELETE)
public boolean deleteCar(@PathVariable("id") Long id) { ... }
```

## Springboot
Springboot uzupełnia Spring framework o konfigurację, mechanizmy autokonfiguracji oraz pozwala w prosty sposób stworzyć działającą aplikację. Aplikacja Springboot posiada wbudowany serwer HTTP przez co nie ma potrzeby uruchamiania jej za pośrednictwem serwera aplikacji. Jest to ostateczne zerwanie z koncepcją serwerów aplikacji propagowanych przez architekturę JEE.

### Zalety
1. Aplikacja uruchamiana jest z poziomu metody statycznej `main`, nie jest potrzebna żadna dodatkowa konfiguracja.
2. Dodatkowa konfiguracja możliwa dzięki beanom `@Configuration` oraz plikom konfiguracyjnym (`yml`)

### Startery
Starterem nazywamy zależność zawierającą dodatkową konfigurację Springa, który wzbogaca aplikację Springboot o dodatkowe funkcjonalności.

Przykład: aby użyć kodu bazodanowego z pośrednictwem JPA należy użyć startera `spring-boot-starter-data-jpa` dodając następującą zależność:
```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <scope>provided</scope>
    </dependency>
```

Kompletna lista oficjalnych starterów: [link](https://github.com/spring-projects/spring-baoot/tree/master/spring-boot-project/spring-boot-starters)

## Dodatkowe materiały
1. [Clean architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
1. [Hexagonal architecture](https://herbertograca.com/2017/11/16/explicit-architecture-01-ddd-hexagonal-onion-clean-cqrs-how-i-put-it-all-together/)
1. [Maven](https://maven.apache.org/)
1. [Gradle](https://gradle.org/)
1. [Spring](https://spring.io/)
1. [Start.spring.io](https://start.spring.io)
1. [RESTful](https://en.wikipedia.org/wiki/Representational_state_transfer)
1. [Springboot](https://spring.io/projects/spring-boot)
