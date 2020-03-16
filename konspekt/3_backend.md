# Wykład 3 (2020-03-16) - backend
## Wprowadzenie
W ramach wykładu poznamy podstawowe pojęcia, narzędzia i technologie związane z wytwarzaniem komponentów uruchamianych na serwerach. Skoncentrujemy się na ekosystemie zbudowanym wokół języka Java.

Uwagi i pytania proszę kierować do: maciej.malecki@pwr.edu.pl

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


