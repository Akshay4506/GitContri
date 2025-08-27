<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "4fa20c513e367e9cdd401bf49ae16e33",
  "translation_date": "2025-08-26T23:10:30+00:00",
  "source_file": "7-bank-project/4-state-management/README.md",
  "language_code": "sv"
}
-->
# Bygg en bankapp del 4: Koncept för hantering av tillstånd

## Förhandsquiz

[Förhandsquiz](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/47)

### Introduktion

När en webbapplikation växer blir det en utmaning att hålla reda på alla dataflöden. Vilken kod hämtar datan, vilken sida använder den, var och när behöver den uppdateras...det är lätt att hamna med rörig kod som är svår att underhålla. Detta är särskilt sant när du behöver dela data mellan olika sidor i din app, till exempel användardata. Konceptet *hantering av tillstånd* har alltid funnits i alla typer av program, men eftersom webbappar fortsätter att öka i komplexitet är det nu en viktig punkt att tänka på under utvecklingen.

I denna sista del ska vi gå igenom appen vi byggde för att ompröva hur tillstånd hanteras, vilket möjliggör stöd för att uppdatera webbläsaren när som helst och att bevara data mellan användarsessioner.

### Förutsättningar

Du behöver ha slutfört [datahämtning](../3-data/README.md)-delen av webbappen för denna lektion. Du behöver också installera [Node.js](https://nodejs.org) och [köra server-API:t](../api/README.md) lokalt så att du kan hantera kontodata.

Du kan testa att servern körs korrekt genom att köra detta kommando i en terminal:

```sh
curl http://localhost:5000/api
# -> should return "Bank API v1.0.0" as a result
```

---

## Ompröva hantering av tillstånd

I [föregående lektion](../3-data/README.md) introducerade vi ett grundläggande koncept för tillstånd i vår app med den globala variabeln `account` som innehåller bankdata för den aktuella inloggade användaren. Men vår nuvarande implementation har några brister. Försök att uppdatera sidan när du är på instrumentpanelen. Vad händer?

Det finns tre problem med den nuvarande koden:

- Tillståndet bevaras inte, eftersom en uppdatering av webbläsaren tar dig tillbaka till inloggningssidan.
- Det finns flera funktioner som ändrar tillståndet. När appen växer kan det bli svårt att spåra ändringarna och lätt att glömma att uppdatera en.
- Tillståndet rensas inte, så när du klickar på *Logga ut* finns kontodatan fortfarande kvar även om du är på inloggningssidan.

Vi skulle kunna uppdatera vår kod för att hantera dessa problem ett i taget, men det skulle skapa mer kodduplicering och göra appen mer komplex och svår att underhålla. Eller så kan vi pausa i några minuter och ompröva vår strategi.

> Vilka problem försöker vi egentligen lösa här?

[Hantering av tillstånd](https://en.wikipedia.org/wiki/State_management) handlar om att hitta en bra metod för att lösa dessa två specifika problem:

- Hur håller man dataflödena i en app begripliga?
- Hur håller man tillståndsdata alltid synkroniserade med användargränssnittet (och vice versa)?

När du har tagit hand om dessa kan eventuella andra problem du har antingen redan vara lösta eller ha blivit enklare att lösa. Det finns många möjliga metoder för att lösa dessa problem, men vi kommer att använda en vanlig lösning som består av att **centralisera datan och sätten att ändra den**. Dataflödena skulle se ut så här:

![Schema som visar dataflöden mellan HTML, användaråtgärder och tillstånd](../../../../translated_images/data-flow.fa2354e0908fecc89b488010dedf4871418a992edffa17e73441d257add18da4.sv.png)

> Vi kommer inte att täcka den del där datan automatiskt triggar uppdateringen av vyn, eftersom det är kopplat till mer avancerade koncept inom [Reaktiv programmering](https://en.wikipedia.org/wiki/Reactive_programming). Det är ett bra ämne att fördjupa sig i om du är intresserad.

✅ Det finns många bibliotek där ute med olika metoder för hantering av tillstånd, [Redux](https://redux.js.org) är ett populärt alternativ. Ta en titt på koncepten och mönstren som används eftersom det ofta är ett bra sätt att lära sig vilka potentiella problem du kan stöta på i stora webbappar och hur de kan lösas.

### Uppgift

Vi börjar med lite omstrukturering. Ersätt deklarationen av `account`:

```js
let account = null;
```

Med:

```js
let state = {
  account: null
};
```

Idén är att *centralisera* all vår appdata i ett enda tillståndsobjekt. Vi har bara `account` för tillfället i tillståndet så det förändrar inte mycket, men det skapar en väg för framtida utveckling.

Vi måste också uppdatera funktionerna som använder det. I funktionerna `register()` och `login()` ersätt `account = ...` med `state.account = ...`;

Lägg till denna rad högst upp i funktionen `updateDashboard()`:

```js
const account = state.account;
```

Denna omstrukturering i sig gav inte många förbättringar, men idén var att lägga grunden för nästa förändringar.

## Spåra dataändringar

Nu när vi har infört tillståndsobjektet för att lagra vår data är nästa steg att centralisera uppdateringarna. Målet är att göra det enklare att hålla reda på alla ändringar och när de sker.

För att undvika att ändringar görs direkt i tillståndsobjektet är det också en bra praxis att betrakta det som [*oföränderligt*](https://en.wikipedia.org/wiki/Immutable_object), vilket innebär att det inte kan ändras alls. Det innebär också att du måste skapa ett nytt tillståndsobjekt om du vill ändra något i det. Genom att göra detta bygger du ett skydd mot potentiellt oönskade [bieffekter](https://en.wikipedia.org/wiki/Side_effect_(computer_science)) och öppnar upp möjligheter för nya funktioner i din app, som att implementera ångra/göra om, samtidigt som det blir enklare att felsöka. Till exempel kan du logga varje ändring som görs i tillståndet och hålla en historik över ändringarna för att förstå källan till ett fel.

I JavaScript kan du använda [`Object.freeze()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) för att skapa en oföränderlig version av ett objekt. Om du försöker göra ändringar i ett oföränderligt objekt kommer ett undantag att kastas.

✅ Vet du skillnaden mellan ett *ytligt* och ett *djupgående* oföränderligt objekt? Du kan läsa om det [här](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze#What_is_shallow_freeze).

### Uppgift

Låt oss skapa en ny funktion `updateState()`:

```js
function updateState(property, newData) {
  state = Object.freeze({
    ...state,
    [property]: newData
  });
}
```

I denna funktion skapar vi ett nytt tillståndsobjekt och kopierar data från det tidigare tillståndet med hjälp av [*spridningsoperatorn (`...`)*](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Spread_syntax#Spread_in_object_literals). Sedan skriver vi över en specifik egenskap i tillståndsobjektet med den nya datan med hjälp av [haknotationen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Working_with_Objects#Objects_and_properties) `[property]` för tilldelning. Slutligen låser vi objektet för att förhindra ändringar med `Object.freeze()`. Vi har bara egenskapen `account` lagrad i tillståndet för tillfället, men med denna metod kan du lägga till så många egenskaper som du behöver i tillståndet.

Vi kommer också att uppdatera initialiseringen av `state` för att säkerställa att det initiala tillståndet också är låst:

```js
let state = Object.freeze({
  account: null
});
```

Efter det, uppdatera funktionen `register` genom att ersätta tilldelningen `state.account = result;` med:

```js
updateState('account', result);
```

Gör samma sak med funktionen `login`, ersätt `state.account = data;` med:

```js
updateState('account', data);
```

Vi tar nu tillfället i akt att lösa problemet med att kontodatan inte rensas när användaren klickar på *Logga ut*.

Skapa en ny funktion `logout()`:

```js
function logout() {
  updateState('account', null);
  navigate('/login');
}
```

I `updateDashboard()`, ersätt omdirigeringen `return navigate('/login');` med `return logout()`;

Försök att registrera ett nytt konto, logga ut och logga in igen för att kontrollera att allt fortfarande fungerar korrekt.

> Tips: du kan se alla tillståndsändringar genom att lägga till `console.log(state)` längst ner i `updateState()` och öppna konsolen i din webbläsares utvecklingsverktyg.

## Bevara tillståndet

De flesta webbappar behöver bevara data för att kunna fungera korrekt. All kritisk data lagras vanligtvis i en databas och nås via ett server-API, som användarkontodata i vårt fall. Men ibland kan det också vara intressant att bevara viss data i klientappen som körs i din webbläsare, för en bättre användarupplevelse eller för att förbättra laddningsprestandan.

När du vill bevara data i din webbläsare finns det några viktiga frågor du bör ställa dig:

- *Är datan känslig?* Du bör undvika att lagra känslig data på klienten, såsom användarlösenord.
- *Hur länge behöver du behålla denna data?* Planerar du att använda denna data endast för den aktuella sessionen eller vill du att den ska lagras för alltid?

Det finns flera sätt att lagra information i en webbapp, beroende på vad du vill uppnå. Till exempel kan du använda URL:er för att lagra en sökfråga och göra den delbar mellan användare. Du kan också använda [HTTP-cookies](https://developer.mozilla.org/docs/Web/HTTP/Cookies) om datan behöver delas med servern, som [autentiseringsinformation](https://en.wikipedia.org/wiki/Authentication).

Ett annat alternativ är att använda en av de många webbläsar-API:erna för att lagra data. Två av dem är särskilt intressanta:

- [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage): en [Key/Value store](https://en.wikipedia.org/wiki/Key%E2%80%93value_database) som gör det möjligt att bevara data specifik för den aktuella webbplatsen över olika sessioner. Datan som sparas i den upphör aldrig.
- [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage): denna fungerar på samma sätt som `localStorage` förutom att datan som lagras i den rensas när sessionen avslutas (när webbläsaren stängs).

Observera att båda dessa API:er endast tillåter att lagra [strängar](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String). Om du vill lagra komplexa objekt måste du serialisera dem till [JSON](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON)-formatet med [`JSON.stringify()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).

✅ Om du vill skapa en webbapp som inte fungerar med en server är det också möjligt att skapa en databas på klienten med hjälp av [`IndexedDB` API](https://developer.mozilla.org/docs/Web/API/IndexedDB_API). Detta är reserverat för avancerade användningsfall eller om du behöver lagra betydande mängder data, eftersom det är mer komplext att använda.

### Uppgift

Vi vill att våra användare ska förbli inloggade tills de uttryckligen klickar på *Logga ut*-knappen, så vi kommer att använda `localStorage` för att lagra kontodatan. Först, låt oss definiera en nyckel som vi kommer att använda för att lagra vår data.

```js
const storageKey = 'savedAccount';
```

Lägg sedan till denna rad i slutet av funktionen `updateState()`:

```js
localStorage.setItem(storageKey, JSON.stringify(state.account));
```

Med detta kommer användarkontodatan att bevaras och alltid vara uppdaterad eftersom vi tidigare centraliserade alla våra tillståndsuppdateringar. Det är här vi börjar dra nytta av alla våra tidigare omstruktureringar 🙂.

Eftersom datan sparas måste vi också ta hand om att återställa den när appen laddas. Eftersom vi börjar få mer initialiseringskod kan det vara en bra idé att skapa en ny funktion `init`, som också inkluderar vår tidigare kod längst ner i `app.js`:

```js
function init() {
  const savedAccount = localStorage.getItem(storageKey);
  if (savedAccount) {
    updateState('account', JSON.parse(savedAccount));
  }

  // Our previous initialization code
  window.onpopstate = () => updateRoute();
  updateRoute();
}

init();
```

Här hämtar vi den sparade datan, och om det finns någon uppdaterar vi tillståndet därefter. Det är viktigt att göra detta *innan* vi uppdaterar routen, eftersom det kan finnas kod som förlitar sig på tillståndet under siduppdateringen.

Vi kan också göra *Instrumentpanelen* till vår applikations standardsida, eftersom vi nu bevarar kontodatan. Om ingen data hittas tar instrumentpanelen hand om att omdirigera till *Inloggningssidan* ändå. I `updateRoute()`, ersätt fallbacken `return navigate('/login');` med `return navigate('/dashboard');`.

Logga nu in i appen och försök att uppdatera sidan. Du bör stanna kvar på instrumentpanelen. Med den uppdateringen har vi tagit hand om alla våra ursprungliga problem...

## Uppdatera datan

...Men vi kan också ha skapat ett nytt problem. Oops!

Gå till instrumentpanelen med kontot `test`, kör sedan detta kommando i en terminal för att skapa en ny transaktion:

```sh
curl --request POST \
     --header "Content-Type: application/json" \
     --data "{ \"date\": \"2020-07-24\", \"object\": \"Bought book\", \"amount\": -20 }" \
     http://localhost:5000/api/accounts/test/transactions
```

Försök att uppdatera instrumentpanelen i webbläsaren nu. Vad händer? Ser du den nya transaktionen?

Tillståndet bevaras på obestämd tid tack vare `localStorage`, men det innebär också att det aldrig uppdateras förrän du loggar ut från appen och loggar in igen!

En möjlig strategi för att lösa detta är att ladda om kontodatan varje gång instrumentpanelen laddas, för att undvika föråldrad data.

### Uppgift

Skapa en ny funktion `updateAccountData`:

```js
async function updateAccountData() {
  const account = state.account;
  if (!account) {
    return logout();
  }

  const data = await getAccount(account.user);
  if (data.error) {
    return logout();
  }

  updateState('account', data);
}
```

Denna metod kontrollerar att vi för närvarande är inloggade och laddar sedan om kontodatan från servern.

Skapa en annan funktion som heter `refresh`:

```js
async function refresh() {
  await updateAccountData();
  updateDashboard();
}
```

Denna uppdaterar kontodatan och tar sedan hand om att uppdatera HTML:en på instrumentpanelsidan. Det är vad vi behöver kalla på när routen för instrumentpanelen laddas. Uppdatera routedefinitionen med:

```js
const routes = {
  '/login': { templateId: 'login' },
  '/dashboard': { templateId: 'dashboard', init: refresh }
};
```

Försök att ladda om instrumentpanelen nu, den bör visa den uppdaterade kontodatan.

---

## 🚀 Utmaning

Nu när vi laddar om kontodatan varje gång instrumentpanelen laddas, tror du att vi fortfarande behöver bevara *all kontodata*?

Försök att arbeta tillsammans för att ändra vad som sparas och laddas från `localStorage` till att endast inkludera det som absolut krävs för att appen ska fungera.

## Efterhandsquiz
[Post-föreläsningsquiz](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/48)

## Uppgift

[Implementera dialogen "Lägg till transaktion"](assignment.md)

Här är ett exempel på resultatet efter att uppgiften är slutförd:

![Skärmdump som visar ett exempel på dialogen "Lägg till transaktion"](../../../../translated_images/dialog.93bba104afeb79f12f65ebf8f521c5d64e179c40b791c49c242cf15f7e7fab15.sv.png)

---

**Ansvarsfriskrivning**:  
Detta dokument har översatts med hjälp av AI-översättningstjänsten [Co-op Translator](https://github.com/Azure/co-op-translator). Även om vi strävar efter noggrannhet, bör det noteras att automatiserade översättningar kan innehålla fel eller brister. Det ursprungliga dokumentet på dess originalspråk bör betraktas som den auktoritativa källan. För kritisk information rekommenderas professionell mänsklig översättning. Vi ansvarar inte för eventuella missförstånd eller feltolkningar som kan uppstå vid användning av denna översättning.