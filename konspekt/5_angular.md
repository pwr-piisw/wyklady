 # Wykład 5 (2020-03-30) - SPA, Angular
## Wprowadzenie
Framework Angular jest jednym z dwóch najważniejszych obecnie frameworków wykorzystywanych do implementacji aplikacji SPA w środowisku JavaScript. Jest to kompletne narzędzie w skład którego wchodzą także:
* Angular Router do obsługi nawigacji międzykomponentowej oraz do interakcji z paskiem adresu.
* RxJS - środowisko programowania reaktywnego.

Dodatkowo, dla Angulara tworzone jest wiele bibliotek, m.in.:
* Angular Material - zestaw widgetów.
* NgBootstrap - implementacja biblioteki Bootstrap dla Angulara.
* NgxBootstrap - inna implementacja biblioteki Bootstrap dla Angulara.
* NgRX - implementacja wzorca Redux dla Angulara.

## Środowisko developerskie

## Opis aplikacji Bookstore
Aplikacja Bookstore dostępna jest w repozytorium [Bookstore](https://github.com/pwr-piisw/bookstore). Jest to referencyjny projekt Angularowy z backendem napisanym w Javie. Jest on podstawą dla listy nr 5, jest też przykładem, w jaki sposób można konstruować proste aplikacje z backendem i frontendem. W tym punkcie przedstawimy podstawowe cechy tego projektu.

### Modułowość
Klient Angularowy oraz Backend zrealizowane są jako dwa moduły Mavena. Znajdują się one odpowiednio w folderach `frontend` oraz `backend`. Proszę zwrócić uwagę, że nie ma formalnej zależności między tymi modułami - moduł `backend` po prostu kopiuje pliki pochodzące z folderu `dist` modułu `frontend` do swoich zasobów webowych tylko po to, aby serwować te pliki z własnego serwera HTTP.

### Backend
Backend zrealizowano jako bardzo prostą aplikację w Javie napisanej z użyciem biblioteki WebFlux (jest to reaktywny serwer webowy).

Wewnętrzna struktura pakietowa wyróżnia:
* podmoduł `domain` - zawierający logikę biznesową,
* podmoduł `app` - zawierają kod powiązany z infrastrukturą - w tym wypadku z serwerem webowym (`app/rest`)  oraz z implementacją prostego mechanizmu zarządzania stanem (`app/store`).

Mimo swojej prostoty, moduł `backend` można łatwo rozszerzać, dodawać nowe endpointy oraz bardziej zaawansowane mechanizmy utrwalania danych (np. JPA, MongoDB itp).

### Frontend
Moduł zawarty w folderze `frontend` został wygenerowany przy użyciu Angular CLI a następnie zmodyfikowany do naszych potrzeb. 

Moduł posiada własny plik `pom.xml` - co jest akurat specyficzne dla środowiska Javowego. Istnieje jednak bardzo dobra integracja JavaScriptowego środowiska w Mavenie, dzieki pluginowi `frontend-maven-plugin`. Plugin ten pozwala na zarządzanie lokalną wersją NodeJS oraz NPM, dzięki czemu możliwe jest wykonywania skryptów NPM i komend Angular CLI nawet na zdalnym środowisku CircleCI - w tym wypadku nie ma konieczności stosowania innych obrazów Dockerowych - wystarcza obraz Java.

Wewnątrz folderu `frontend` można wykorzystywać komendy `npm` oraz `ng`, przy założeniu, że są one globalnie dostępne. Pomimo istnienia `frontend-maven-plugin` zaleca się korzystać z własnej instalacji NodeJS przy pracy z projektem na lokalnej maszynie.

Aplikacja Angularowa znajduje się w folderze `src/app`. Istnieje tam w tej chwili jeden moduł `books`. Dalsza struktura folderów ma następujące znaczenie:
* `app/books/model` - folder dla definicji obiektów modelu (głównie data transfer objects).
* `app/books/shared` - folder dla elementów współdzielonych przez wszystkie komponenty w module `books`.
* `app/books/shared/services` - folder dla serwisów używanych w ramach modułu.
* `app/books/book-list` - komponent "top level" realizujący widok listy książek. Inne komponenty `top-level` należy umieszczać także na poziomie `books`.
* `app/books/book-list/book-panel` - komponent "dummy", prywatny dla "book-list". Wszelkie prywatne komponenty umieszczamy wewnątrz hierarchii komponentu "top-level".

W głównym katalogu aplikacji (`app`) znajdują się także następujące istotne pliki źródłowe:
* `app.component.*` - główny komponent aplikacji - wewnątrz niego osadzone są wszystkie inne komponenty "top level".
* `app-routing.module.ts` - kod routera dla aplikacji Bookstore.

## Architektura
Angular jest realizacją architektury aplikacji "Single Page Application", którą poznaliśmy już na pierwszym wykładzie.

![Architektura SPA](../img/rich-front-architecture.svg)

Angular odpowiada za całość implementacji po stronie przeglądarki, łącznie z Routerem, przy czym Router jest modułem opcjonalnym.

![Architektura Angular](../img/angular-architecture.png)

## Podstawowe elementy Angulara
### Moduły
### Komponenty
### Template
### Data-binding
### Dyrektywy
### Serwisy
## Angular Router
## Techniki komunikacji międzykomponentowej
## Komunikacjaz z backendem
## Bibliografia
