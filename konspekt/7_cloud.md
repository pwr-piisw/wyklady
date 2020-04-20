# Wykład 7 - Chmury obliczeniowe

## Wprowadzenie

Chmura obliczeniowa polega na dostarczaniu usług obliczeniowych — w tym serwerów, magazynu, baz danych, sieci, oprogramowania, analizy i inteligencji — za pośrednictwem Internetu, w celu zaoferowania szybszych innowacji, elastycznych zasobów i ekonomii skali. Zazwyczaj płacimy tylko za używane usługi w chmurze. Konsument płaci za użytkowanie określonej usługi, np. za możliwość korzystania z arkusza kalkulacyjnego lub serwera bazodanowego. Pomaga to obniżyć koszty operacyjne, wydajniej korzystać z infrastruktury i przeprowadzać skalowanie zgodnie ze zmianami zachodzącymi w firmie. Oznacza to także eliminację konieczności zakupu licencji czy konieczności instalowania i administracji oprogramowaniem.  

Zasada działania polega na przeniesieniu całego ciężaru świadczenia usług IT (danych, oprogramowania lub mocy obliczeniowej) na serwer i umożliwienie stałego dostępu poprzez komputery klienckie. Dzięki temu ich bezpieczeństwo nie zależy od tego, co stanie się z komputerem klienckim, a szybkość procesów wynika z mocy obliczeniowej serwera. Wystarczy zalogować się z jakiegokolwiek komputera z dostępem do Internetu by zacząć korzystać z dobrodziejstw chmury obliczeniowej.

## Korzyści z używania chmury obliczeniowej

* Szybkość 
 
  Większość usług w chmurze jest udostępniana na żądanie. Dodatkowo usługi te są obsługiwane samodzielnie przez użytkownika. Pozwala to na udostępnienie nawet ogromnej ilości zasobów w kilka minut (zwykle wystarczy kilka kliknięć myszą). 
  
* Koszty
  
  Chmura obliczeniowa eliminuje wydatki inwestycyjne związane z zakupem sprzętu i oprogramowania oraz konfigurowaniem i działaniem lokalnych centrów danych — stojaki pełne serwerów, prąd niezbędny do zasilania i chłodzenia, specjaliści IT zarządzający infrastrukturą.
  
* Skala globalna
  
  Chmura pozwala na elastyczne skalowanie, czyli zapewnienie odpowiednich ilości zasobów IT, np. więcej lub mniej mocy obliczeniowej, magazynu lub przepustowości, dokładnie wtedy, gdy są potrzebne, oraz z odpowiedniej lokalizacji geograficznej.
  
*  Produktywność
  
   Dzięki wykorzystaniu chmury obliczeniowej nie trzeba wykonywać wielu czynności, takich jak konfigurowanie sprzętu, instalowanie poprawek oprogramowania i wykonywanie innych czasochłonnych codziennych czynności niezbędnych przy zarządzaniu infrastrukturą IT. Pozwala zespołom IT na zajęcie się ważniejszymi celami biznesowymi. 
  
*  Wydajność
  
   Usługi chmury obliczeniowej są rozproszone w światowej sieci bezpiecznych centrów danych, posiadających szybki i wydajny sprzęt najnowszej generacji, co daje im przewagę w stosunku do pojedynczego korporacyjnego centrum danych, m.in. mniejsze opóźnienia sieciowe i większą ekonomię skali.
  
*  Niezawodność
  
   W chmurze mamy możliwość dublowania danych w wielu nadmiarowych lokacjach w sieci dostawcy chmury, co ułatwia tworzenie kopii zapasowych, odzyskiwanie po awarii i zapewnianie ciągłości działania oraz obniża ich koszt.
  
*  Zabezpieczenia
   
   Wielu dostawców chmury oferuje bogaty zestaw zasad, technologii i środków, które zwiększają ogólny poziom bezpieczeństwa i pomagają chronić dane, aplikacje oraz infrastrukturę przed potencjalnymi zagrożeniami.
   
## Typy chmury obliczeniowej

Aby móc zaoferować rozwiązanie odpowiednie do potrzeb użytkowników chmury, opracowano kilka różnych modeli, typów i usług.

Najpierw należy określić typ wdrożenia w chmurze lub architekturę chmury obliczeniowej, w jakiej zostaną zaimplementowane usługi w chmurze. Istnieją trzy różne sposoby wdrażania usług: w chmurze publicznej, w chmurze prywatnej i w chmurze hybrydowej.

### Chmura publiczna (public cloud)
  
Usługi w chmurze publicznej są dostarczane przez zewnętrznych dostawców, którzy je obsługują i dostarczają zasoby obliczeniowe, takie jak serwery i magazyn, za pośrednictwem Internetu. Cały sprzęt, oprogramowanie i pozostała infrastruktura pomocnicza należą do dostawcy chmury i to on nimi zarządza
  
W chmurze publicznej dostajemy współdzielone zasoby (czy to serwery, platformę czy aplikacje), które jednak potrafią się skalować, które separują dane od siebie, itp.
  
Przykładem chmury publicznej są Amazon Web Services, Google Cloud Platform, Microsoft Azure. 
  
### Chmura dedykowana

To specjalna wersja chmury publicznej, która nie jest dostępna dla każdego. Dostawca ze względu na bardzo specyficzne wymagania różnych organizacji (np. rządu, banków, itp.) jest w stanie fizycznie wydzielić część zasobów na potrzeby tylko i wyłączenie określonego Klienta.

Wtedy Klient ma 100% pewność, że to, za co płaci, nie jest w żaden sposób współdzielone z innymi Klientami. Oczywiście takie rozwiązania muszą odpowiednio więcej kosztować i będą odpowiednie dla bardzo określonych organizacji, ale jednak jest nimi też spore zainteresowanie.

### Chmura prywatna (private cloud)
  
Chmura prywatna to zasoby chmury obliczeniowej używane wyłącznie przez jedno przedsiębiorstwo lub jedną organizację. Chmura prywatna może się fizycznie znajdować w lokalnym centrum danych przedsiębiorstwa. Niektóre firmy płacą zewnętrznym dostawcom usług za hostowanie ich chmury prywatnej. W przypadku chmury prywatnej usługi i infrastruktura znajdują się w sieci prywatnej.

Rozwiązanie to pozwala to na budowanie rozwiązań dla klientów, którzy nie mogą sobie pozwolić na trzymanie i przetwarzanie wrażliwych danych w chmurze publicznej. 
  
Do budowania usług IaaS w prywatnej chmurze można wykorzystać takie oprogramowanie jak Apache CloudStack czy OpenStack. Rozwiązania typu PaaS można budować z wykorzystaniem takich rozwiązań jak Kubernetes czy OpenShift.

### Chmura hybrydowa (hybrid cloud)
  
Chmury hybrydowe łączą chmury publiczne i prywatne z wykorzystaniem technologii pozwalającej na udostępnianie danych i aplikacji między nimi. Zezwalając na przechodzenie danych oraz aplikacji między chmurami prywatnymi i publicznymi, chmura hybrydowa zapewnia większą elastyczność i więcej opcji wdrażania. Pomaga również zoptymalizować istniejącą infrastrukturę, zabezpieczenia i zgodność.

Pewna część aplikacji i infrastruktury danego klienta pracuje w chmurze prywatnej, a część jest umiejscowiona w przestrzeni chmury publicznej. Użytkownik chmury może korzystać z zalet chmury publicznej, pozostawiając sobie możliwość przetwarzania wrażliwych danych na prywatnej infrastrukturze. 

To rozwiązanie pozwala także na stopniową migrację z chmury prywatnej do chmury publicznej. 
  
## Typy usług w chmurze
   
Większość usług chmury obliczeniowej należy do jednej z czterech podstawowych kategorii: infrastruktura jako usługa (IaaS), platforma jako usługa (PaaS), bezserwerowe (serverless, FaaS) i oprogramowanie jako usługa (SaaS). 
   
### Infrastruktura jako usługa (IaaS)

Jest to najbardziej podstawowa kategoria usług chmury obliczeniowej. W przypadku rozwiązań IaaS olega to na wynajmowaniu infrastruktury IT — serwery, maszyny wirtualne, magazyn, sieci i systemy operacyjne — od dostawcy chmury w modelu płatności zgodnie z rzeczywistym wykorzystaniem zasobów.

To dostawca daje nam sprzęt i dodatkowo czasem również zabezpieczenia. Naszym zadaniem jest dostarczyć system operacyjny, oprogramowanie i aplikacje. Dzięki wirtualizacji to, co dostarczamy dostawcy to najczęściej po prostu obraz maszyny wirtualnej. Często dostawcy IaaS udostępniają także obrazy z gotowym systemem operacyjnym.

W ten sposób działa m.in. chmura Amazon EC2.
   
### Platforma jako usługa (PaaS)
   
Platforma jako usługa to usługi chmury obliczeniowej dostarczające środowisko na żądanie służące do opracowywania, testowania i dostarczania aplikacji oraz zarządzania nimi. Głównym celem tego rozwiązania jest ułatwienie deweloperom szybkiego tworzenia aplikacji internetowych lub mobilnych bez przejmowania się konfigurowaniem niezbędnej podstawowej infrastruktury serwerów, magazynu, sieci i baz danych ani zarządzaniem nią.

W wypadku PaaS bierzemy to wszystko, co było w IaaS, ale dostawca dorzuca nam do tego całą platformę aplikacyjną. Przestajemy się martwić o system operacyjny (w tym jego utrzymanie, zarządzanie, patchowanie), a zajmujemy się tylko pisaniem aplikacji i ich utrzymaniem. Aplikacje możemy użytkować sami lub po prostu je sprzedawać jako usługi.
   
### Operacje obliczeniowe bez użycia serwera (serverless), funkcja jako usługa (FaaS)
   
Przetwarzanie bezserwerowe koncentruje się na tworzeniu funkcjonalności aplikacji bez marnowania czasu na ciągłe zarządzanie wymaganymi do tego serwerami i infrastrukturą. Dostawca chmury wyręcza Cię w konfigurowaniu, planowaniu wydajności i zarządzaniu serwerami. Architektury bezserwerowe są wysoce skalowalne i sterowane zdarzeniami, a z zasobów korzystają tylko wtedy, gdy wystąpi konkretna funkcja lub wyzwalacz.

Do rozwiązań bezserwerowych zaliczamy m.in. bazy danych dostępne w chmurze. Użytkownik dostarcza schemat i dane, które mają być składowane, natomiast o całą resztę (dostępność, skalowalność, niezawodność, kopie zapasowe itd.) troszczy się dostawca usługi.

Innym przykładem, ostatnio coraz bardziej popularnym, są usługi typu FaaS. Użytkownik dostarcza kod aplikacji i definiuje wyzwalacze, przy których ten kod ma być uruchomiony. Za uruchamianie i skalowanie aplikacji odpowiedzialna jest infrastruktura chmury.
   
### Oprogramowanie jako usługa (SaaS)
   
Oprogramowanie jako usługa to metoda dostarczania aplikacji za pośrednictwem Internetu. Aplikacja jest dostarczana na żądanie i zazwyczaj na podstawie subskrypcji. W przypadku rozwiązania SaaS dostawcy chmury hostują aplikację wraz z podstawową infrastrukturą i zarządzają nimi, a także zajmują się konserwacją, np. uaktualnieniami oprogramowania i stosowaniem poprawek zabezpieczeń. Użytkownicy łączą się z aplikacją za pośrednictwem Internetu, zwykle przy użyciu przeglądarki internetowej na swoim telefonie, tablecie lub komputerze.

Użytkownik korzysta tylko z określonej aplikacji i jej funkcjonalności. Czyli kupuje trochę gotowe pudełko z aplikacją, ale nie instaluje jej u siebie na komputerze, tylko korzystamy z niej w chmurze (przez Internet).

W tym wypadku rozlicza się najczęściej w modelu – opłata za jednego użytkownika za miesiąc korzystania z aplikacji. Minusem jest to, że nie zmienimy sobie nic w tej aplikacji ani nie mamy wpływu na jej rozwój.

Chmurę tego typu udostępniają między innymi Google (Google Apps), Salesforce (system CRM), Microsoft (Office 365, OneDrive).

## Zastosowania chmury obliczeniowej
   
Z chmury obliczeniowej korzystamy prawie na każdym kroku, nie zdając sobie z tego sprawy. Usługi online do wysyłania wiadomości e-mail, edytowania dokumentów, oglądania filmów lub telewizji, słuchania muzyki, grania w gry albo przechowywania zdjęć i innych plików — w tle każdej z tych usług prawdopodobnie działa chmura obliczeniowa, która to wszystko umożliwia. 

Oto kilka przykładów przedstawiających, jakie rozwiązania dotyczące usług w chmurze mogą być obecnie oferowane przez dostawców chmury:
    
* Tworzenie aplikacji natywnych dla chmury (cloud native)
    
  Chmura pozwala nam na szybkie tworzenie, wdrażanie i skalowanie aplikacji — internetowych, mobilnych i interfejsów API. W tym celu możemy skorzystać z zalet technologii i podejść natywnych dla chmury, takich jak kontenery, platforma Kubernetes, architektura mikroserwisów, komunikacja sterowana interfejsami API i infrastruktura DevOps.
  
* Testowanie i tworzenie aplikacji
    
  Korzystając z infrastruktur, które można łatwo skalować w górę i w dół, możemy zmniejszać koszt i skracać czas opracowania aplikacji, .
    
* Przechowywanie danych oraz wykonywanie i odzyskiwanie kopii zapasowych tych danych
  
  Przenosząc swoje dane za pośrednictwem Internetu do chmury poza siedzibą firmy, która jest dostępna z dowolnej lokalizacji i za pomocą dowolnego urządzenia, chronimy swoje dane bardziej ekonomicznie.
    
* Analizowanie danych
  
  Korzystając z chmury możemy zunifikować swoje dane wymieniane między zespołami, działami i lokalizacjami w chmurze oraz skorzystać z usług w chmurze, takich jak uczenie maszynowe i sztuczna inteligencja.
    
* Przesyłanie strumieniowe audio i wideo
  
  Dzięki dystrybucji globalnej możemy łączyć się ze swoimi odbiorcami w dowolnym miejscu i czasie za pośrednictwem dowolnego urządzenia obsługującego dźwięk i wideo w wysokiej rozdzielczości.
    
* Dostarczanie oprogramowania na żądanie
 
  Oprogramowanie na żądanie, znane też jako oprogramowanie jako usługa (SaaS), umożliwia oferowanie klientom najnowszych wersji i aktualizacji oprogramowania — zawsze, kiedy go potrzebują, i wszędzie, gdzie się znajdują.
  
## Aplikacje natywne dla chmury (cloud native)
 
Aplikacje cloud native są specjalnie przystosowane do architektury cloud computing. Mogą być szybko budowane i wdrażane przez małe dedykowane zespoły deweloperów na platformy, które działają na dowolnym sprzęcie i które można łatwo skalować. Aplikacje chmurowe zapewniają zwinność (tzw. agility) i elastyczność. Można je też łatwo przenosić między chmurami. Oczywiście aplikacje cloud native nie powinny być zależne od środowiska, nie ważne, czy to jest chmura publiczna, chmura prywatna czy maszyna developerska

Aplikacje cloud native to oprogramowanie projektowane specjalnie na potrzeby architektury chmurowej, którą tworzą rozproszone, luźno połączone usługi zwane mikroserwisami. Mikroserwis to taka aplikacja/proces, która nie jest sztywno powiązana z innymi komponentami systemu i robi jedną rzecz (z biznesowego punktu widzenia). Dodatkowym atutem systemów mikroserwis jest to, że są wysoce skalowalne. Powinniśmy być w stanie uruchamiać nasze systemy na niewielkiej liczbie maszyn, a także na takiej ilości która uniemożliwia już manualne ingerencje i zarządzanie nimi powierza się innym maszynom.

Przy podejściu cloud native zakłada się, że infrastruktura może ulegać awariom, natomiast niezawodne mają pozostać aplikacje. Zadaniem programistów jest rozdzielenie zadań na oddzielne usługi, które mogą działać na kilku serwerach w różnych miejscach. Ponieważ infrastruktura obsługująca natywną aplikację chmurową nie działa lokalnie, przy projektowaniu aplikacji NCA należy uwzględniać redundancję, dzięki czemu aplikacja może pozostać odporna na awarię sprzętu i być w stanie automatycznie ponownie odwzorować adresy IP w razie awarii sprzętu. Dzięki temu, że aplikacja nie jest przywiązana do serwera, środowisko można łatwo skalować.

Jeżeli nasz system jest rozproszony, pojawia się wyzwanie – jak je monitorować, zbierać logi i debugować? Aplikacje cloud native powinny być obserwowalne. Dzięki temu będziemy w stanie powiedzieć, która z 300 instancji serwisu obsłużyła jakiś proces biznesowy, ile w tym czasie zużywała czasu procesora i ile pamięci potrzebowała.

Dotychczas, programista podczas pracy nad jakimś systemem musiał myśleć o tym, czy to będzie aplikacja uruchamiana jako WebApp w Azure, AWS Lambda, czy może Linux service w datacenter naszego klienta. Razem z cloud native takie rozważania odchodzą do lamusa, bo dla programisty, chmurą może być Azure, AWS, GCP czy też serwerownia dwa piętra niżej. Elastyczność takich aplikacji może być osiągnięta poprzez ich konteneryzację – dzięki temu łatwo odciąć się od fizycznych zasobów.

Cechą aplikacji cloud native jest także to, że potrafimy w sposób zautomatyzowany wdrożyć nowe wersje, co jest szczególnie ważne, kiedy musimy szybko wdrożyć poprawkę z powodu błędu wykrytego na produkcji lub dostarczyć nowe funkcjonalności klientowi. W idealnym świecie chcielibyśmy, żeby zmiany zostały wdrożone na systemie produkcyjnym natychmiast po dokonaniu poprawki przez developera – oczywiście po wcześniejszym, zautoomatyzowanym przetestowaniu aplikacji. To wszystko jest możliwe dzięki automatyzacji i wdrożeniu procesów Devops. Wyeliminowanie interfejsu białkowego (człowieka) z tego procesu pozwala na zmniejszenie ilości błędnych decyzji – oznacza to, że im większa automatyzacja, tym większa niezawodność, dostępność i jednolity format tego, co jest uruchamiane. Jedną z najbardziej popularnych obecnie platform jest Kubernetes, który zapewnia API – jednolite niezależnie od infrastruktury i dodatkowo zarządza kontenerami uruchomionymi na nim.
