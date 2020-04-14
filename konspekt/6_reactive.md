# Wykład 5 - programowanie reaktywne, Angular dla zaawansowanych
Link do prezentacji: https://pwr-piisw.github.io/wyklady/reactive.html#/

Koniecznie proszę zapoznać się z prezentacją, jest w niej sporo przykładów na Plunkerze, które można własnoręcznie uruchomić i zapoznać się z zasadami działania operatorów RxJS.

## Asynchroniczność
Asynchroniczność należy rozumieć jako sytuację, w której odpowiedź na zdarzenie może pojawić się w dowolnym momencie w przyszłości, dlatego nie jest praktyczne wstrzymywanie wykonywania kodu na ten czas (blocking, busy waiting). Zamiast tego, podczas wywoływania funkcji asynchronicznej rejestrujemy funkcję zwrotną (callback function), zawiera kod obsługujący odpowiedź w momencie, w którym ta nadejdzie.

Asynchroniczność nie jest pojęciem nowym, ten model programowania wykorzystywany jest m.in.:
* w obsłudze sygnałów systemu operacyjnego UNIX,
* w obsłudze zdarzeń systemu operacyjnego Windows,
* w procedurach obsługi przerwań pracy procesora (CPU).

## Asynchroniczność w systemach jednowątkowych
JavaScript, jak już wiemy, jest językiem jednowątkowym. Sytuacja, w której kod musi poczekać na odpowiedź z zewnętrznego systemu (system plików, sieć komputerowa) powoduje, że JavaScript nie jest w stanie nic wykonać. Nie działałoby wtedy chociażby renderowanie oraz reakcja na jakiekolwiek zdarzenia. Pomocna zatem staje się w JavaScripcie asynchroniczność rozumiana podobnie jak to opisano powyżej: wywołanie zdalnego API wymaga zarejestrowanie funkcji zwrotnej, która zostanie wykonana w dogodnym momencie.

Co ciekawe, w podobny sposób działały pierwsze okienkowe systemy operacyjne, takie jak Microsoft Windows (wersja 3.x i starsze), MacOS firmy Apple.

Wracając do JavaScriptu, na następnym diagramie widzimy jego model wykonawczy.

![JavaScript Runtime Engine](../img/JavaScript-runtime-engine.png)

Widzimy tam trzy elementy składające się na model wykonawczy:
* JavaScript runtime - system wykonawczy w ramach którego alokowane są dane oraz wykonywany jest kod.
* WebAPI - API systemów zewnętrznych, do których dostęp możliwy jest poprzez asynchroniczne wywołania.
* Callback Queue - kolejka wywołań zwrotnych - zarejestrowanych funkcji, z których każda zostanie wykonana w momencie, gdy system wykonawczy nie będzie miał nic innego do wykonania (stos wywołań będzie pusty).

Problem pojawia się w momencie, w którym wywołanie asynchroniczne skutkuje kolejnym wykonaniem asynchronicznym i tak dalej. Prowadzi to do fenomenu znanego jako Callback Hell: kod przestaje być przejrzysty.

![Callback hell](../img/Callback-Hell.png)

Istnieje kilka mechanizmów - technik usprawnienia programowania asynchroniczego w JavaScripcie / TypeScripcie - opiszemy je w następnych punktach.

### Promise
Promise dostępne są w JavaScripcie począwszy od wersji ECMAScript 6.

Obiekt `Promise` posiada wewnątrz callbacki obsługujące sytuację pozytywną i negatywną (błędną). Obiekt `Promise` najczęściej jest zwracany jako wartość funkcji asynchronicznej.

```JavaScript
const p = new Promise((resolve, reject) => {
   if (/* condition */) {
      resolve(/* value */);  // fulfilled successfully
   }
   else {
      reject(/* reason */);  // error, rejected
   }
});
```

Obiekt `Promise` pozwala na rejestrowanie callbacków w prosty sposób:

```JavaScript
p.then((val) => console.log('fulfilled:' + val),
       (err) => console.log('rejected: ' + err));
```

Obiekt `Promise` pozwala także łączyć wywołania asynchroniczne w łańcuchy:

```JavaScript
p.then((val) => someOtherAsyncFunction(val))
 .then((val) => someOtherAsyncFunction2(val))
 .then((val) => console.log('fulfilled:' + val)) ;
```

### Async / await

Mechanizm async / await jest cukrem syntaktycznym pozwalającym na ukrycie obiektów `Promise`.

Funkcje typy `async` zawsze zwracają wartość opakowaną w `Promise`: jeśli zwracana wartość nie jest obiektem `Promise`, JavaScript automatycznie opakuje taką wartość w `Promise`.

```JavaScript
async function someFunction() {
  return 'foo';
}

someFunction().then((val) => console.log(val)); // -> 'foo'
```

Słowo `async` nie jest specjalnie magiczne. Ciekawsze jest słowo `await`. Słowo kluczowe `await` można wywołać jedynie wewnątrz funkcji typu `async`. Poprzedzenie słowem `await` wywołania zwracajacego `Promise` lub wartości `Promise` powoduje, że dalsze instrukcje (poniżej `await`) zostaną wykonane dopiero w momencie pozytywnego rozwiązana promise - funkcja staje się nieblokującą. Kod zaczyna przypominać kod synchroniczny, staje się przez to bardziej czytelny.

```JavaScript
async function showAvatar() {

  // read our JSON
  let response = await fetch('/article/promise-chaining/user.json');
  let user = await response.json();

  // read github user
  let githubResponse = await fetch(`https://api.github.com/users/${user.name}`);
  let githubUser = await githubResponse.json();

  // show the avatar
  let img = document.createElement('img');
  img.src = githubUser.avatar_url;
  img.className = "promise-avatar-example";
  document.body.append(img);

  // wait 3 seconds
  await new Promise((resolve, reject) => setTimeout(resolve, 3000));

  img.remove();

  return githubUser;
}
```

### Observables / RxJS
