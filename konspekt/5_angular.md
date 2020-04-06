 # Wykład 5 (2020-03-30) - SPA, Angular
Link do wykładów: https://pwr-piisw.github.io/wyklady/angular_1.html#/

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
### NodeJS
### NPM
### Angular CLI

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

## Krótka historia Angulara

## Architektura
Angular jest realizacją architektury aplikacji "Single Page Application", którą poznaliśmy już na pierwszym wykładzie.

![Architektura SPA](../img/rich-front-architecture.svg)

Angular odpowiada za całość implementacji po stronie przeglądarki, łącznie z Routerem, przy czym Router jest modułem opcjonalnym.

![Architektura Angular](../img/angular-architecture.png)

## Podstawowe elementy Angulara
### Moduły
Moduł (`NgModule`) to podstawowy mechanizm konstruowania aplikacji Angularowej. Moduł służy do grupowania elementów należących i realizujących wspólną poddomenę. Moduły Angulara pełnią podobną rolę do modułów javaScriptów, nie należy jednak ich mylić ze sobą. Aby uniknąć pomyłek, będziemy używać nazwy NgModule.

Moduły NgModule przechowywane są w osobnych folderach wewnątrz aplikacji. Każdy NgModule opisywany jest przy pomocy pliku `*.module.ts`:

```typescript
import {NgModule} from '@angular/core';
import {CommonModule} from '@angular/common';
import {BookListComponent} from './book-list/book-list.component';
import {BookPanelComponent} from './book-list/book-panel/book-panel.component';

@NgModule({
  declarations: [
    BookListComponent,
    BookPanelComponent
  ],
  imports: [
    CommonModule
  ]
})
export class BooksModule {
}
```

Moduł pozwala zarządzać zależnościami wewnątrz aplikacji, zarówno wewnętrznymi jak i zewnętrznymi. Deklaracja zależności modułowych następuje wewnątrz dekoratora `@NgModule`, znajdują się tam kolekcje o następujących właściwości:
* `providers` - lista klas obiektów, które będą mogły być wstrzyknięte przez Angular Injector w ramach tego modułu. Wykorzystywane w przypadku np. lokalnych serwisów.
* `declarations` - lista klas komponentów, które są zadeklarowane w ramach tego modułu.
* `imports` - lista klas komponentów, które są importowane i mogą być użyte wewnątrz tego modułu. Są to zarówno komponenty pochodzące z innych, zewnętrznych bibliotek (np. Angular Material, ale także biblioteki samego Angulara, np. Angular Forms) ale także komponentów z innych modułów danej aplikacji.
* `exports` - lista komponentów zadeklarowanych lub zaimportowanych przez dany moduł, które są eksportowane, czyli są dostępne dla innych modułów (można je importować w innych modułach).
* `entryComponents` - lista komponentów, które mogą być dynamicznie ładowane przez moduł, wykorzystywane np. do deklarowania komponentów okien dialogowych.

Nowy moduł można wygenerować przy pomocy Angular CLI:
```
ng generate module <name>
```
Więcej szczegółów uzyskać można w dokumentacji Angulara: https://angular.io/cli/generate#module-command
Więcej informacji na temat samych modułów tamże: https://angular.io/guide/ngmodules

### Komponenty
Komponent jest podstawową jednostką, przy pomocy której zbudowana jest każda aplikacja Angularowa. Komponent jest elementem wizualnym, tj może on być wyrenderowany na ekranie (w modelu DOM dokumentu HTML) i służy do prezentacji danych oraz reagowania na akcje wykonywane przez użytkownika.

Komponent składa się z nastepujących elementów:
* kod komponentu (klasa Typescript),
* template komponentu (fragment HTML z możliwością stosowania dyrektyw),
* prywatny arkusz styli (dokument CSS, SCSS lub inny wspierany).

Elementy te mogą być zgrupowane w ramach jednego pliku, najczęsciej jednak stosuje się kilka niezależnych plików źródłowych:
* `<nazwa>.component.html` - dla template,
* `<nazwa>.component.scss` - dla arkusza styli,
* `<nazwa>.component.ts` - dla kodu komponentu.

Dla kodu komponentu może istnieć także zestaw testów jednostkowych: `<nazwa>.component.spec.ts`.

Komponent musi być zadeklarowany w ramach modułu (musi znaleźć się w kolekcji `declarations`). Komponenty najczęściej deklarowane są w ramach dedykowanego folderu. Możliwe - i zalecane - jest zagnieżdżanie komponentów w sobie w sytuacji, gdy deklarujemy komponenty prywatne, używane wewnętrznie przez inny komponent.

Komponenty mogą komunikować się między sobą na kilka sposobów. To zagadnienie jest szczegółowo opisane w innym punkcie tego dokumentu.

Komponenty można wygenerować z użyciem Angular CLI:
```
cd src/app
ng generate component <name>
```
Nazwa komponentu może (powinna) być poprzedzona nazwą modułu oraz ewentualnie nazwą komponentu-rodzica.

Więcej informacji na temat Angular CLI: https://angular.io/cli/generate#component-command
Więcej informacji na temat komponentów: https://angular.io/guide/displaying-data

### Template
### Data-binding
### Dyrektywy
### Serwisy
## Angular Router
## Techniki komunikacji między komponentowej
## Komunikacjaz z backendem
## Bibliografia
