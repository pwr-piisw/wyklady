# Aplikacja full-stack: Java, Spring Boot i Angular

_Projekt i implementacja systemów webowych — materiał uzupełniający_

mgr inż. Maciej Małecki

---

## Agenda

1. Anatomia aplikacji full-stack
2. Pryncypia architektury technicznej
3. Backend: Spring Boot — REST API w praktyce
4. Warstwa danych: Spring Data JPA
5. Frontend: Angular — konsumpcja API
6. Kontrakt frontend ↔ backend (DTO, walidacja, błędy)
7. Bezpieczeństwo: JWT i CORS
8. Testowanie i QA
9. Architektura w chmurze
10. Uruchomienie lokalne i build produkcyjny

---

## 1. Anatomia aplikacji full-stack

### Dwie aplikacje, jeden produkt

Współczesna aplikacja webowa to zazwyczaj **dwa niezależne procesy**:

- **backend** — proces serwerowy (JVM + Spring Boot), wystawia API HTTP, mówi z bazą danych,
- **frontend** — aplikacja SPA (Angular), uruchamiana w przeglądarce, komunikuje się z backendem przez `fetch` / `HttpClient`.

Te dwie aplikacje są łączone wyłącznie **kontraktem HTTP** (najczęściej REST + JSON). Mogą być rozwijane, deployowane i skalowane niezależnie.

### Konsekwencje architektoniczne

- frontend **nie ma dostępu do bazy danych** — każde pobranie/zapisanie danych przechodzi przez API,
- backend **nie wie nic o DOM** — nie generuje HTML-a (poza ewentualnymi e-mailami transakcyjnymi),
- całe **uwierzytelnianie i autoryzacja** muszą zostać przeniesione na poziom HTTP (tokeny, ciasteczka),
- pojawia się problem **CORS** (cross-origin resource sharing), bo SPA serwowane jest z innego origin niż API.

> _ang. **separation of concerns** — separacja odpowiedzialności_: każda warstwa robi jedną rzecz, ale dobrze.

---

## 2. Pryncypia architektury technicznej

### Czym jest architektura?

> _„Architektura to te decyzje, które są jednocześnie istotne i trudne do zmiany."_
> — Ralph Johnson, cytowany przez Martina Fowlera

Architektura oprogramowania to proces planowania struktury systemu tak, aby spełniał wymagania funkcjonalne i jakościowe — i pozostawał możliwy do utrzymania w czasie. Dobrze dobrana architektura pozwala na **stały koszt wprowadzania zmian** w cyklu życia produktu (hipoteza Design Stamina, M. Fowler).

W całym dalszym wykładzie pokazujemy konkretne wzorce (DTO, repozytoria, interceptory, JWT) — to wszystko są **konsekwencje** kilku ogólnych zasad, które warto poznać przed wejściem w szczegóły.

### Zasady podstawowe

#### 1. Rozdział odpowiedzialności (_Separation of Concerns_)

Każdy element ma jeden powód do zmiany.

**W naszym stosie:**
- kontroler obsługuje HTTP, serwis — logikę biznesową, repozytorium — dostęp do danych,
- frontend obsługuje DOM, backend obsługuje persystencję — nigdy odwrotnie.

#### 2. Minimalizacja zależności (_Minimize Dependencies_)

- unikaj cykli w grafie zależności,
- silna spójność wewnątrz modułu (_strong cohesion_),
- luźne powiązanie między modułami (_loose coupling_).

**W naszym stosie:** osobne pakiety Mavena dla `web`, `service`, `persistence`; encja JPA nie wycieka do kontrolera.

#### 3. Odwracanie zależności (_Dependency Inversion_)

> Kod wysokiego poziomu nie powinien zależeć od kodu niskopoziomowego. Abstrakcja nie powinna zależeć od szczegółów — to szczegóły powinny zależeć od abstrakcji.

**W naszym stosie:** serwis zależy od **interfejsu** `CourseRepository`, a nie od konkretnej klasy korzystającej z Hibernate. To Spring wstrzykuje konkretną implementację (DI).

```java
// Wysoki poziom (serwis) zależy od abstrakcji.
public class CourseService {
    private final CourseRepository repo;   // interfejs, nie implementacja
    public CourseService(CourseRepository repo) { this.repo = repo; }
}
```

Korzyści: łatwa wymiana implementacji (np. mock w testach), niezależność od dostawcy ORM-a.

#### 4. Ukrywanie informacji (_Information Hiding_, _Encapsulation_)

Element ukrywa swoje wnętrze; aktorzy zewnętrzni wiedzą o nim **tylko niezbędne minimum**.

**W naszym stosie:** DTO/record na granicy API ukrywa strukturę encji JPA. Zmiana schematu bazy nie łamie kontraktu HTTP.

#### 5. Jednorodność (_Homogeneity_)

> Używaj podobnych rozwiązań dla podobnych problemów.

**W naszym stosie:** każdy zasób REST ma tę samą strukturę (kontroler + serwis + repozytorium + DTO); każdy moduł frontu ma tę samą strukturę (komponent + serwis API + model). Nowa osoba w zespole uczy się raz, używa wszędzie.

> ⚠️ Uważaj — technologie ewoluują. Jednorodność nie oznacza zamrożenia.

#### 6. Kontrolowana nadmiarowość (_DRY, Controlled Redundancy_)

> _„The first time you do something, just do it. The second time, you do it similarly. The third time, you refactor."_ — Martin Fowler

Wspólny kod wyodrębniaj **dopiero gdy widzisz powtórzenie po raz trzeci**. Wcześniejsze abstrahowanie zwykle daje złą abstrakcję.

### Zasady pochodne

#### 7. Warstwowość (_Layering_)

> Każda warstwa może korzystać tylko z własnych komponentów oraz z warstwy bezpośrednio poniżej.

**W naszym stosie:** controller → service → repository → database. Nie wolno wołać repozytorium z kontrolera ani DAO z DTO.

Wynika z: rozdziału odpowiedzialności + minimalizacji zależności + ukrywania informacji.

#### 8. Projektowanie kontraktowe (_Design by Contract_)

Kontrakt opisuje, **jak aktor korzysta z elementu**, ukrywając szczegóły implementacji.

**W naszym stosie:** OpenAPI to kontrakt między backendem a frontem; interfejs `CourseRepository` to kontrakt między serwisem a warstwą danych.

Korzyści: niezależność implementacji, niezależność testowania, stabilność abstrakcji.

#### 9. Suwerenność danych (_Data Sovereignty_)

- dane mają jednego właściciela, który zarządza modyfikacją,
- dostęp do odczytu może być zdecentralizowany (cache, repliki, projekcje).

**W naszym stosie (mikroserwisy):** tabela `courses` należy do jednego serwisu. Inne serwisy nie czytają jej bezpośrednio z bazy — proszą o dane przez API. To otwiera drogę do wzorców jak **CQRS** czy **data mesh**.

#### 10. Ponowne użycie (_Re-use_)

Don't Repeat Yourself — ale **nie na siłę**. Często mechanizmy są podobne tylko pozornie; przedwczesna konsolidacja tworzy gorszy kod niż niewielka duplikacja.

### Dlaczego o tym mówimy teraz

Wzorce, które za chwilę zobaczysz — DTO, repozytorium, interceptor, OpenAPI — to nie są „triki Springa". To **realizacje konkretnych pryncypiów** w danej technologii. Ten sam zestaw zasad zobaczysz w stosie Pythonowym, .NET, Node.js — różnią się tylko nazwy klas i adnotacji.

---

## 3. Backend: Spring Boot

### Dlaczego Spring Boot?

- konwencja ponad konfiguracją (_ang. convention over configuration_),
- **autokonfiguracja** — jeśli na classpath jest `spring-boot-starter-web`, Spring Boot wystawia serwer Tomcat na porcie 8080,
- ekosystem starterów: `data-jpa`, `security`, `validation`, `actuator`, `test`,
- aplikacja uruchamiana jako **pojedynczy `jar`** (`java -jar app.jar`) — żadnego zewnętrznego serwera aplikacyjnego.

### Minimalny projekt

`pom.xml` (Maven):

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

Klasa startowa:

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### Warstwy backendu

```
Controller  ──>  Service  ──>  Repository  ──>  Database
   (HTTP)         (logika)       (JPA)
```

- **Controller** — mapuje URL-e na metody Javy, parsuje JSON, zwraca odpowiedzi HTTP. Nie zawiera logiki biznesowej.
- **Service** — realizuje przypadki użycia, transakcje, walidacje biznesowe.
- **Repository** — interfejs dostępu do danych; Spring Data sam dostarcza implementację.

---

## 4. REST API w Spring

### Kontroler i mapowania HTTP

```java
@RestController
@RequestMapping("/api/courses")
public class CourseController {

    private final CourseService service;

    public CourseController(CourseService service) {
        this.service = service;
    }

    @GetMapping
    public List<CourseDto> list() {
        return service.findAll();
    }

    @GetMapping("/{id}")
    public CourseDto getOne(@PathVariable Long id) {
        return service.findById(id);
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public CourseDto create(@Valid @RequestBody CreateCourseRequest req) {
        return service.create(req);
    }

    @PutMapping("/{id}")
    public CourseDto update(@PathVariable Long id,
                            @Valid @RequestBody UpdateCourseRequest req) {
        return service.update(id, req);
    }

    @DeleteMapping("/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void delete(@PathVariable Long id) {
        service.delete(id);
    }
}
```

Co warto zauważyć:

- `@RestController` = `@Controller` + `@ResponseBody` — każda metoda zwraca dane (JSON), nie nazwę widoku,
- `@RequestBody` triggeruje deserializację JSON → Java (Jackson),
- `@Valid` aktywuje walidację Bean Validation (`jakarta.validation`).

### DTO vs encja

**Nigdy nie zwracaj encji JPA z kontrolera.** Powody:

1. Encja zawiera relacje, które przy serializacji mogą wciągnąć pół bazy,
2. Zmiana modelu danych natychmiast łamie API,
3. Encja może mieć pola, których klient nie powinien widzieć (np. `passwordHash`).

```java
public record CourseDto(Long id, String title, String lecturer, int ects) {}

public record CreateCourseRequest(
        @NotBlank @Size(max = 200) String title,
        @NotBlank String lecturer,
        @Min(1) @Max(30) int ects
) {}
```

---

## 5. Warstwa danych: Spring Data JPA

### Encja

```java
@Entity
@Table(name = "courses")
public class Course {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 200)
    private String title;

    @Column(nullable = false)
    private String lecturer;

    @Column(nullable = false)
    private int ects;

    protected Course() {} // wymagane przez JPA

    public Course(String title, String lecturer, int ects) {
        this.title = title;
        this.lecturer = lecturer;
        this.ects = ects;
    }

    // gettery, settery
}
```

### Repozytorium

```java
public interface CourseRepository extends JpaRepository<Course, Long> {
    List<Course> findByLecturer(String lecturer);

    @Query("select c from Course c where c.ects >= :min")
    List<Course> findHighEcts(@Param("min") int min);
}
```

Spring Data **generuje implementację** na podstawie nazwy metody. `findByLecturer` automatycznie staje się `WHERE lecturer = ?`.

### Serwis łączy kropki

```java
@Service
@Transactional
public class CourseService {

    private final CourseRepository repo;

    public CourseService(CourseRepository repo) {
        this.repo = repo;
    }

    @Transactional(readOnly = true)
    public List<CourseDto> findAll() {
        return repo.findAll().stream().map(this::toDto).toList();
    }

    public CourseDto create(CreateCourseRequest req) {
        Course saved = repo.save(new Course(req.title(), req.lecturer(), req.ects()));
        return toDto(saved);
    }

    private CourseDto toDto(Course c) {
        return new CourseDto(c.getId(), c.getTitle(), c.getLecturer(), c.getEcts());
    }
}
```

### `application.yml`

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/courses
    username: app
    password: app
  jpa:
    hibernate:
      ddl-auto: validate   # produkcja: NIGDY 'update'
    properties:
      hibernate.format_sql: true
  flyway:
    enabled: true          # migracje schematu
```

---

## 6. Frontend: Angular

### Stos technologiczny

- **TypeScript** — typowany nadzbiór JavaScriptu,
- **Angular CLI** — `ng new`, `ng serve`, `ng build`,
- **RxJS** — `Observable`, asynchroniczne strumienie,
- **standalone components** (Angular ≥ 14) — bez `NgModule`.

### Generowanie projektu

```bash
ng new piisw-frontend --standalone --routing --style=scss
cd piisw-frontend
ng serve   # http://localhost:4200
```

### Model danych po stronie klienta

Typy TypeScript powinny **odpowiadać DTO** z backendu:

```typescript
export interface Course {
  id: number;
  title: string;
  lecturer: string;
  ects: number;
}

export interface CreateCourseRequest {
  title: string;
  lecturer: string;
  ects: number;
}
```

### Serwis HTTP

```typescript
@Injectable({ providedIn: 'root' })
export class CourseApi {

  private readonly http = inject(HttpClient);
  private readonly base = '/api/courses';

  list(): Observable<Course[]> {
    return this.http.get<Course[]>(this.base);
  }

  create(req: CreateCourseRequest): Observable<Course> {
    return this.http.post<Course>(this.base, req);
  }

  delete(id: number): Observable<void> {
    return this.http.delete<void>(`${this.base}/${id}`);
  }
}
```

`HttpClient` zwraca `Observable` — strumień RxJS. Pojedyncze żądanie HTTP emituje **dokładnie jedną wartość** i się zamyka.

### Komponent

```typescript
@Component({
  selector: 'app-course-list',
  standalone: true,
  imports: [CommonModule, AsyncPipe],
  template: `
    <ul>
      @for (course of courses$ | async; track course.id) {
        <li>
          <strong>{{ course.title }}</strong>
          — {{ course.lecturer }} ({{ course.ects }} ECTS)
          <button (click)="remove(course.id)">Usuń</button>
        </li>
      }
    </ul>
  `,
})
export class CourseListComponent {

  private readonly api = inject(CourseApi);
  private readonly reload$ = new BehaviorSubject<void>(undefined);

  readonly courses$ = this.reload$.pipe(
    switchMap(() => this.api.list())
  );

  remove(id: number): void {
    this.api.delete(id).subscribe(() => this.reload$.next());
  }
}
```

Pipe `async` **subskrybuje strumień** w szablonie i automatycznie odsubskrybowuje przy zniszczeniu komponentu — to idiomatyczny sposób unikania wycieków pamięci.

---

## 7. Kontrakt frontend ↔ backend

### Format błędów

Backend powinien zwracać **spójny format błędów** — najlepiej zgodny z RFC 7807 (`application/problem+json`):

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ProblemDetail> handleValidation(MethodArgumentNotValidException ex) {
        ProblemDetail problem = ProblemDetail.forStatus(HttpStatus.BAD_REQUEST);
        problem.setTitle("Validation failed");
        problem.setProperty("errors", ex.getBindingResult().getFieldErrors().stream()
                .collect(Collectors.toMap(FieldError::getField, FieldError::getDefaultMessage)));
        return ResponseEntity.badRequest().body(problem);
    }

    @ExceptionHandler(EntityNotFoundException.class)
    public ResponseEntity<ProblemDetail> handleNotFound(EntityNotFoundException ex) {
        return ResponseEntity.status(404).body(
                ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage()));
    }
}
```

### Generowanie kontraktu

W praktyce produkcyjnej warto generować klienta TypeScript automatycznie:

- backend wystawia opis API w **OpenAPI** (Spring Boot + `springdoc-openapi`),
- frontend uruchamia `openapi-generator` → typy + serwisy są generowane.

Dzięki temu **niedopasowanie kontraktu jest błędem kompilacji**, nie błędem runtime'u.

---

## 8. Bezpieczeństwo: JWT i CORS

### CORS

Domyślnie przeglądarka **blokuje** żądania `XHR`/`fetch` do innego origin. SPA na `localhost:4200` chce wołać API na `localhost:8080` — to różne originy.

```java
@Configuration
public class CorsConfig {

    @Bean
    public CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration cfg = new CorsConfiguration();
        cfg.setAllowedOrigins(List.of("http://localhost:4200"));
        cfg.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE"));
        cfg.setAllowedHeaders(List.of("Authorization", "Content-Type"));
        UrlBasedCorsConfigurationSource src = new UrlBasedCorsConfigurationSource();
        src.registerCorsConfiguration("/api/**", cfg);
        return src;
    }
}
```

### JWT — uwierzytelnianie bezstanowe

1. Klient wysyła `POST /api/auth/login` z hasłem,
2. Backend weryfikuje, generuje **JWT** podpisany kluczem serwera,
3. Klient zapisuje token (np. w pamięci) i dodaje go w nagłówku `Authorization: Bearer <token>` do każdego żądania,
4. Backend waliduje podpis i wyciąga z tokenu tożsamość użytkownika — **bez sięgania do bazy**.

Po stronie Angulara typowo używa się **interceptora HTTP**:

```typescript
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = inject(AuthStore).token();
  if (!token) return next(req);
  return next(req.clone({
    setHeaders: { Authorization: `Bearer ${token}` }
  }));
};
```

> **Uwaga:** _localStorage_ jest podatny na XSS. Dla aplikacji z wyższymi wymaganiami bezpieczeństwa rozważ **HttpOnly cookies** + ochronę CSRF.

---

## 9. Testowanie i QA

### Po co automatyzujemy testy?

- **powtarzalność** — ten sam scenariusz daje ten sam wynik za każdym uruchomieniem,
- **szybka informacja zwrotna** — błąd wykryty w 30 sekund w pipeline jest tańszy o rząd wielkości niż ten sam błąd zauważony przez użytkownika produkcyjnego,
- **koszt** — testy ręczne skalują się liniowo z liczbą wydań; testy automatyczne mają koszt jednorazowy plus utrzymanie.

> _„Tools don't test. Only people test. Tools only perform actions that 'help' people test."_ — James Bach

Automatyzacja nie zastępuje myślenia testerskiego. Pisanie testów to projektowanie scenariuszy — narzędzia tylko je uruchamiają.

### Rodzaje testów

| Poziom | Co testuje | Koszt | Stabilność |
|--------|------------|-------|------------|
| **Unit** (jednostkowe) | pojedynczą klasę / funkcję, w izolacji | niski | wysoka |
| **Component / module** | kilka klas razem (np. serwis + repo z bazą in-memory) | średni | wysoka |
| **Integration** | warstwy / komponenty zintegrowane (np. kontroler + serwis + DB) | wyższy | średnia |
| **E2E** | całą aplikację „od kliknięcia do bazy" | wysoki | niska |

### Piramida testów

> Martin Fowler — _The Practical Test Pyramid_

```
            /\
           /E2E\           <- niewiele, drogie, wolne
          /------\
         /  inte- \        <- średnia liczba
        /  gracja  \
       /------------\
      /     unit     \    <- bardzo dużo, tanie, szybkie
     /----------------\
```

Reguły:

- **piramida, nie odwrócona piramida** — gdy E2E jest więcej niż unitów, każda zmiana w aplikacji łamie wiele testów na raz, a feedback loop wydłuża się z sekund do minut,
- **testy niskiego rzędu wykonują się szybko** — sekundy, nie minuty,
- **mockowanie ma swoje pułapki** — testy z mockami przechodzą, gdy realna integracja jest zepsuta. Im wyżej w piramidzie, tym mniej mocków.

### Testowanie w aplikacji webowej — co testujemy gdzie

| Warstwa | Typ testu | Narzędzia (Java/Angular) |
|---------|-----------|--------------------------|
| Backend — pojedyncza klasa serwisu | unit | JUnit 5 + Mockito |
| Backend — kontroler bez DB | slice / module | Spring `@WebMvcTest` + MockMvc |
| Backend — pełen stos z DB | integration | Spring `@SpringBootTest` + **Testcontainers** |
| Frontend — pipe / serwis | unit | Vitest (lub Karma+Jasmine) |
| Frontend — komponent | component | Angular TestBed + Vitest, opcjonalnie Cypress Component Testing |
| Cała aplikacja | E2E | **Cypress** lub **Playwright** |

### Backend — testy jednostkowe (JUnit + Mockito)

```java
@ExtendWith(MockitoExtension.class)
class CourseServiceTest {

    @Mock CourseRepository repo;
    @InjectMocks CourseService service;

    @Test
    void create_savesAndReturnsDto() {
        Course saved = new Course("PIiSW", "Małecki", 4);
        when(repo.save(any())).thenReturn(saved);

        CourseDto result = service.create(new CreateCourseRequest("PIiSW", "Małecki", 4));

        assertThat(result.title()).isEqualTo("PIiSW");
        verify(repo).save(any(Course.class));
    }
}
```

### Backend — testy slice'owe kontrolera (`@WebMvcTest`)

Uruchamia tylko warstwę web (kontrolery + walidacja + serializacja JSON), bez podnoszenia całej aplikacji:

```java
@WebMvcTest(CourseController.class)
class CourseControllerTest {

    @Autowired MockMvc mvc;
    @MockBean CourseService service;

    @Test
    void list_returnsCoursesAsJson() throws Exception {
        when(service.findAll()).thenReturn(List.of(new CourseDto(1L, "PIiSW", "Małecki", 4)));

        mvc.perform(get("/api/courses"))
           .andExpect(status().isOk())
           .andExpect(jsonPath("$[0].title").value("PIiSW"));
    }
}
```

### Backend — testy integracyjne z Testcontainers

Pułapka mockowania: test z `H2` zachowuje się inaczej niż produkcja na PostgreSQL (typy, indeksy, dialect). Rozwiązaniem jest **Testcontainers** — uruchomienie prawdziwego PostgreSQL w Dockerze na czas testu:

```java
@SpringBootTest
@Testcontainers
class CourseIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16");

    @DynamicPropertySource
    static void props(DynamicPropertyRegistry r) {
        r.add("spring.datasource.url", postgres::getJdbcUrl);
        r.add("spring.datasource.username", postgres::getUsername);
        r.add("spring.datasource.password", postgres::getPassword);
    }

    @Autowired CourseRepository repo;

    @Test
    void persistsAndQueries() {
        repo.save(new Course("PIiSW", "Małecki", 4));
        assertThat(repo.findByLecturer("Małecki")).hasSize(1);
    }
}
```

### Frontend — testy jednostkowe i komponentowe

W roku 2026 **Vitest jest domyślnym test runnerem dla nowych projektów Angular**. Karma + Jasmine są oficjalnie zdeprecjonowane (utrzymanie tylko dla bug-fixów), ale wciąż działają i dominują w istniejących projektach. Jasmine jako _matcher library_ pozostaje wspierany.

```typescript
// course.service.spec.ts
import { TestBed } from '@angular/core/testing';
import { provideHttpClientTesting, HttpTestingController } from '@angular/common/http/testing';
import { CourseApi } from './course-api';

describe('CourseApi', () => {
  let api: CourseApi;
  let http: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [CourseApi, provideHttpClientTesting()],
    });
    api = TestBed.inject(CourseApi);
    http = TestBed.inject(HttpTestingController);
  });

  it('lists courses', () => {
    api.list().subscribe(courses => {
      expect(courses.length).toBe(1);
    });

    const req = http.expectOne('/api/courses');
    req.flush([{ id: 1, title: 'PIiSW', lecturer: 'Małecki', ects: 4 }]);
  });
});
```

### Testy E2E

> ⚠️ **Protractor nie żyje.** End-of-life sierpień 2023, builder usunięty z Angulara w v15. Nigdy nie zaczynaj nowego projektu z Protractorem — nie dostaniesz nawet oficjalnych dokumentów do migracji.

Dwa praktyczne wybory dziś:

- **Cypress** — najbardziej popularny w społeczności Angulara, świetny developer experience (interaktywny runner, time-travel debugging),
- **Playwright** — wieloprzeglądarkowy (Chromium / Firefox / WebKit), automatyczne `auto-wait`, mocniejszy w scenariuszach z wieloma originami i zakładkami.

```typescript
// e2e/courses.cy.ts (Cypress)
describe('Courses page', () => {
  it('shows the course list', () => {
    cy.intercept('GET', '/api/courses', { fixture: 'courses.json' });
    cy.visit('/courses');
    cy.contains('PIiSW').should('be.visible');
  });
});
```

### Pułapki, które bolą

- **Mockowanie bazy danych** — H2 / mocki ORM-a maskują błędy, które ujawniają się tylko na produkcyjnym dialekcie. Dla testów dotykających persystencji **Testcontainers**.
- **E2E zamiast unitów** — „testujemy klikaniem" sprawia, że suite zaczyna trwać 40 minut, a krytyczne ścieżki i tak zostają nieprzetestowane.
- **Flaky tests** — losowo padający test E2E gorszy niż brak testu: zespół uczy się go ignorować, a prawdziwa regresja przejdzie niezauważona.
- **Testy zależne od zegara / kolejności** — zawsze podawaj zegar przez interfejs (`Clock` w Springu), nie używaj `LocalDateTime.now()` wprost.

### CI/CD

- testy unitów + slice'owe — w każdym PR-ze (czas: < 2 min),
- integracyjne z Testcontainers — w każdym PR-ze (czas: 2–5 min),
- pełne E2E na środowisku staging — przed merge'em do `main` lub jako nightly,
- **żaden test nie powinien wymagać manualnej konfiguracji** — kontener PostgreSQL podnosi się sam, fixtures budują się same.

---

## 10. Architektura w chmurze

### Dlaczego chmura?

Dwa fundamenty, na których stoi cloud computing:

- **Obliczenia równoległe** (_parallel computing_) — dzielenie problemu na _n_ podproblemów rozwiązywanych jednocześnie; systemy wielordzeniowe i wieloprocesorowe.
- **Obliczenia rozproszone** (_distributed computing_) — przetwarzanie rozłożone na wiele węzłów w sieci; podejścia typu Map-Reduce, skalowalność horyzontalna, rozproszony storage.

Prawo Moore'a ma fizyczne ograniczenia — dalszy wzrost mocy obliczeniowej osiągamy nie przez szybsze procesory, lecz przez **więcej maszyn działających równolegle**. Chmura jest natywnym środowiskiem takiej architektury.

### Modele usług: SaaS / PaaS / IaaS

| Model | Co dostarcza dostawca | Przykład |
|-------|----------------------|----------|
| **SaaS** (_Software as a Service_) | gotową aplikację dostępną przez sieć | GitHub, Gmail, Salesforce |
| **PaaS** (_Platform as a Service_) | zarządzaną platformę uruchomieniową + narzędzia | Heroku, AWS Elastic Beanstalk, Azure App Service |
| **IaaS** (_Infrastructure as a Service_) | maszyny, sieć, storage „na żądanie", płacone za użycie | AWS EC2, Azure VMs, GCP Compute Engine |

Im wyżej w stosie, tym mniej kontroli — ale tym mniej rzeczy musisz utrzymywać samodzielnie. Aplikacja Spring Boot uruchomiona jako kontener na **AWS ECS Fargate** to PaaS; ta sama aplikacja na **EC2** z ręcznie konfigurowanym Tomcatem to IaaS.

### Charakterystyki rozwiązań chmurowych

#### Skalowalność (_scalability_)

Dopasowanie rozmiaru infrastruktury do popytu — adekwatna zarówno dla małych, jak i bardzo dużych aplikacji.

> _Przykład:_ PoC lub startup używa minimalnej infrastruktury, by trzymać koszty nisko; produkcyjna wersja obsługuje masowe użycie.

#### Elastyczność (_elasticity_)

Alokacja zasobów może być zmieniana **dynamicznie** w odpowiedzi na bieżący popyt — z redukcją infrastruktury po okresie zwiększonego ruchu.

> _Przykład:_ dodatkowe zasoby dla operatora telekomunikacyjnego o północy w Sylwestra; **scale up** w Black Friday, **scale down** w nocy.

| Scenariusz | Akcja |
|------------|-------|
| Black Friday | **SCALE UP** |
| Zwykły dzień roboczy | _steady state_ |
| Środek nocy | **SCALE DOWN** |

#### Współdzielenie zasobów (_resource sharing_)

- efektywność kosztowa,
- jedna infrastruktura obsługuje wielu użytkowników (multi-tenancy).

> _Przykład:_ Amazon Bedrock — współdzielone GPU dla modeli językowych.

> Chmura **może** być oszczędnością — pod warunkiem, że jest dobrze zarządzana. Bez kontroli kosztów potrafi wygenerować rachunki większe niż własne datacenter.

### On-demand provisioning

Zasoby są dostarczane dynamicznie, gdy są potrzebne — bez ręcznej interwencji. Aplikacja zgłasza zapotrzebowanie (przez API dostawcy lub deklaratywnie przez IaC), platforma dostarcza zasób w sekundach lub minutach.

### Usługi serverless

Model, w którym **nie zarządzasz serwerami** — dostawca skaluje, łata, monitoruje. Płacisz za rzeczywiste użycie (czas wykonania, liczbę żądań, ilość danych).

Przykłady z AWS, mapowane na role w typowej aplikacji webowej:

| Usługa | Rola |
|--------|------|
| **AWS Lambda** | obliczenia (krótkie funkcje wywoływane zdarzeniem) |
| **Amazon API Gateway** | wystawienie REST API |
| **Amazon Aurora Serverless** | relacyjna baza danych |
| **Amazon DynamoDB** | baza NoSQL |
| **Amazon S3** | object storage (pliki, statyki) |
| **Amazon Bedrock** | modele generatywnego AI |

> Spring Boot ma **wsparcie dla AWS Lambda** (`spring-cloud-function`), ale dla aplikacji o stałym ruchu klasyczny kontener z ECS/Kubernetes bywa bardziej opłacalny niż billing per-invocation.

### Infrastructure as Code (IaC)

Automatyzacja i zarządzanie infrastrukturą **przez kod** zamiast przez ręczne klikanie w konsoli.

- **Terraform** (HashiCorp) — agnostyczny względem dostawcy,
- **AWS CloudFormation**, **Azure Bicep** — natywne dla swojej chmury,
- **Pulumi** — IaC w językach ogólnego przeznaczenia (TypeScript, Python).

Korzyści: powtarzalność środowisk (`dev` / `staging` / `prod` z tego samego kodu), historia zmian w Git, code review na zmiany w infrastrukturze.

### Ryzyka chmury

- **Vendor lock-in** — uzależnienie od konkretnego dostawcy (np. od specyficznych usług DynamoDB czy Bedrock),
- **Bezpieczeństwo i compliance** — dane wychodzą poza Twoje datacenter; obowiązki RODO, sektorowe regulacje,
- **Koszty** — łatwo wygenerować rachunek wielokrotnie wyższy niż planowany,
- **Złożoność operacyjna** — bogaty katalog usług bywa trudny do opanowania.

### Mitygacja ryzyk

- **Abstrakcja portami i adapterami** — kod biznesowy nie wie, czy zapisuje do PostgreSQL na EC2, czy do Aurora,
- **Multi-cloud / hybrid cloud** — krytyczne komponenty rozproszone między dostawców,
- **Monitoring kosztów** — alarmy budżetowe, automatyczne wyłączanie środowisk dev w nocy,
- **Polityki bezpieczeństwa as code** — np. AWS SCP, Azure Policy.

### Architektura aplikacji chmurowych

Wszyscy główni dostawcy publikują **frameworki dobrze zaprojektowanej architektury**, oparte na podobnych filarach:

- **AWS Well-Architected Framework**
- **Azure Well-Architected Framework**
- **Google Cloud Architecture Framework**

Wspólne filary (z drobnymi różnicami w nazewnictwie):

1. **Operational excellence** — automatyzacja, monitoring, lessons learned,
2. **Security** — _least privilege_, szyfrowanie, audyt,
3. **Reliability** — odporność na awarie, multi-AZ, kopie zapasowe,
4. **Performance efficiency** — dobór odpowiednich zasobów, autoskalowanie,
5. **Cost optimization** — _right-sizing_, instancje rezerwowane/spotowe,
6. **Sustainability** (AWS) — efektywność energetyczna obciążeń.

Wybór konkretnych usług zależy od typu aplikacji. Dla typowej aplikacji Spring Boot + Angular w AWS możliwa kombinacja:

- **Compute:** ECS Fargate (kontener z `app.jar`) albo Elastic Beanstalk,
- **DB:** RDS PostgreSQL (z replikami w wielu AZ),
- **Storage:** S3 (statyki Angular, uploady),
- **CDN:** CloudFront (dystrybucja statyków),
- **Auth:** Cognito (zewnętrzny dostawca tożsamości) zamiast własnego JWT,
- **Monitoring:** CloudWatch + X-Ray,
- **IaC:** Terraform.

### Pryncypia ↔ chmura

Cloud nie unieważnia zasad architektury — wręcz przeciwnie:

- **Suwerenność danych** → mikroserwisy z własnymi bazami (DynamoDB / Aurora per serwis),
- **Ukrywanie informacji** → API Gateway jako jedyny punkt wejścia,
- **Warstwowość** → CDN → Load Balancer → Compute → DB,
- **Kontrakt** → OpenAPI + walidacja na poziomie API Gateway.

---

## 11. Uruchomienie lokalne i build

### Tryb deweloperski

Dwa terminale:

```bash
# terminal 1: backend
./mvnw spring-boot:run        # http://localhost:8080

# terminal 2: frontend
ng serve --proxy-config proxy.conf.json   # http://localhost:4200
```

`proxy.conf.json` przekierowuje `/api` na backend, eliminując CORS w trakcie developmentu:

```json
{
  "/api": { "target": "http://localhost:8080", "secure": false }
}
```

### Build produkcyjny

Dwa popularne podejścia:

**A. Osobne deploymenty** (zalecane dla większych zespołów):
- backend → kontener z `app.jar` na porcie 8080,
- frontend → `ng build` → statyki serwowane przez Nginx / CDN,
- reverse proxy (Nginx, Traefik) routuje `/api/*` na backend, resztę na frontend.

**B. Wbudowany frontend w jar backendu** (proste deploymenty):
- `ng build --output-path=../backend/src/main/resources/static`,
- Spring Boot serwuje statyki automatycznie z `static/`,
- jeden artefakt, jeden deployment.

---

## Podsumowanie

- Aplikacja full-stack to **dwa niezależne procesy** połączone kontraktem HTTP.
- Wszystkie konkretne wzorce (DTO, repozytorium, interceptor, OpenAPI) wynikają z **kilku ogólnych pryncypiów**: rozdziału odpowiedzialności, minimalizacji zależności, odwracania zależności, ukrywania informacji, warstwowości i kontraktu.
- Spring Boot dostarcza warstwy: kontroler → serwis → repozytorium, z autokonfiguracją i ekosystem starterów.
- **Nigdy nie eksponuj encji JPA** — używaj DTO/recordów na granicy API.
- Angular konsumuje API przez `HttpClient` i `Observable`; pipe `async` zarządza subskrypcjami.
- Spójny format błędów (RFC 7807) i generowanie klienta z OpenAPI eliminują całe klasy bugów integracyjnych.
- CORS i JWT to **standardowe** problemy w architekturze SPA + REST — warto znać szablony rozwiązań.
- **Piramida testów** — dużo szybkich unitów, mniej integracyjnych, najmniej E2E. Nie odwracaj jej.
- Backend testujemy na trzech poziomach: jednostkowo (JUnit + Mockito), warstwowo (`@WebMvcTest` + MockMvc), integracyjnie z **Testcontainers** (prawdziwy PostgreSQL w Dockerze).
- Frontend: **Vitest** to nowy domyślny runner Angulara; Karma + Jasmine zdeprecjonowane, ale działają. **Protractor jest martwy** — używaj Cypress lub Playwright dla E2E.
- Chmura zmienia model dostarczania (SaaS / PaaS / IaaS, serverless, IaC) i daje **skalowalność i elastyczność**, ale wymaga świadomego zarządzania kosztami i ryzykiem vendor lock-in.
- Frameworki Well-Architected (AWS / Azure / GCP) opisują wspólne filary: niezawodność, bezpieczeństwo, wydajność, koszty, doskonałość operacyjną.

---

## Materiały uzupełniające

- Spring Boot Reference: <https://docs.spring.io/spring-boot/docs/current/reference/html/>
- Spring Data JPA: <https://docs.spring.io/spring-data/jpa/docs/current/reference/html/>
- Angular: <https://angular.dev/>
- RxJS: <https://rxjs.dev/>
- RFC 7807 (Problem Details): <https://www.rfc-editor.org/rfc/rfc7807>
- OpenAPI Generator: <https://openapi-generator.tech/>
- AWS Well-Architected Framework: <https://aws.amazon.com/architecture/well-architected/>
- Azure Well-Architected Framework: <https://learn.microsoft.com/azure/well-architected/>
- Google Cloud Architecture Framework: <https://cloud.google.com/architecture/framework>
- Martin Fowler — Design Stamina Hypothesis: <https://martinfowler.com/bliki/DesignStaminaHypothesis.html>
- Martin Fowler — The Practical Test Pyramid: <https://martinfowler.com/articles/practical-test-pyramid.html>
- Spring — testowanie aplikacji: <https://docs.spring.io/spring-boot/reference/testing/index.html>
- Testcontainers: <https://java.testcontainers.org/>
- Angular Testing — migracja Karma → Vitest: <https://angular.dev/guide/testing/migrating-to-vitest>
- Cypress: <https://www.cypress.io/>
- Playwright: <https://playwright.dev/>
- Protractor end-of-life (issue): <https://github.com/angular/protractor/issues/5502>
