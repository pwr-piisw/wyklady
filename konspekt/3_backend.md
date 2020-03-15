# Wykład 3 (2020-03-16) - backend
## Wprowadzenie
## Architektura
Backendem nazywamy zbiór komponentów software'owych realizujących logikę aplikacji, które rozmieszczone oraz wykonywane są na serwerach. Backend z reguły jest silnie wydzielonym fragmentem kodu, który komunikuje się z innymi komponentami przy pomocy dobrze zdefiniowanych interfejsów (tzw. API).

W architekturach systemów biznesowych staramy się w ramach backendu enkapsulować tzw. logikę biznesową, czyli zbiór reguł i struktur danych specyficznych dla danej dziedziny biznesowej. Logika biznesowa jest najcenniejszym zasobem programistycznym, powinna więc podlegać szczególnej ochronie. W szczególności logika biznesowa jako relatywnie stała i niezmienna, powinna być uniezależniona od zmian technologicznych. Jedną z metod ochrony logiki jest zastosowania modelu heksagonalnego architektury, widocznego na diagramie.

![Architektura heksagonalna](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/pwr-piisw/wyklady/develop/konspekt/architecture-general.puml)
