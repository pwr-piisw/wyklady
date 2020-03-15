# Wykład 3 (2020-03-16) - backend
## Wprowadzenie
## Architektura
Backendem nazywamy zbiór komponentów software'owych realizujących logikę aplikacji, które rozmieszczone oraz wykonywane są na serwerach. Backend z reguły jest silnie wydzielonym fragmentem kodu, który komunikuje się z innymi komponentami przy pomocy dobrze zdefiniowanych interfejsów (tzw. API).

W architekturach systemów biznesowych staramy się w ramach backendu enkapsulować tzw. logikę biznesową, czyli zbiór reguł i struktur danych specyficznych dla danej dziedziny biznesowej. Logika biznesowa jest najcenniejszym zasobem programistycznym, powinna więc podlegać szczególnej ochronie. W szczególności logika biznesowa jako relatywnie stała i niezmienna, powinna być uniezależniona od zmian technologicznych. Jedną z metod ochrony logiki jest zastosowania modelu heksagonalnego architektury, widocznego na diagramie.

![Architektura heksagonalna](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/develop/konspekt/architecture-general.puml)

Często stosuje się uproszczony model architektury - architekturę warstwową:

![Architektura warstwowa](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/develop/konspekt/architecture-layered.puml)

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

![Struktura projektu](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/develop/konspekt/maven.puml)
