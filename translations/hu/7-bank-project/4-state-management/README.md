<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "4fa20c513e367e9cdd401bf49ae16e33",
  "translation_date": "2025-08-28T03:33:48+00:00",
  "source_file": "7-bank-project/4-state-management/README.md",
  "language_code": "hu"
}
-->
# Banki alkalmazás építése 4. rész: Az állapotkezelés alapjai

## Előadás előtti kvíz

[Előadás előtti kvíz](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/47)

### Bevezetés

Ahogy egy webalkalmazás növekszik, egyre nehezebb nyomon követni az adatáramlásokat. Melyik kód kapja meg az adatokat, melyik oldal használja fel, hol és mikor kell frissíteni... könnyen előfordulhat, hogy a kód rendezetlenné válik, és nehéz lesz karbantartani. Ez különösen igaz, ha az adatokat több oldal között kell megosztani az alkalmazásban, például a felhasználói adatokat. Az *állapotkezelés* fogalma mindig is létezett mindenféle programban, de ahogy a webalkalmazások egyre bonyolultabbá válnak, ez ma már kulcsfontosságú szempont a fejlesztés során.

Ebben az utolsó részben áttekintjük az eddig épített alkalmazást, hogy újragondoljuk az állapotkezelést, lehetővé téve a böngésző frissítésének támogatását bármely ponton, és az adatok megőrzését a felhasználói munkamenetek között.

### Előfeltétel

El kell végezned a webalkalmazás [adatlekérdezés](../3-data/README.md) részét ehhez a leckéhez. Telepítened kell a [Node.js](https://nodejs.org) programot, és [futtatnod kell a szerver API-t](../api/README.md) helyileg, hogy kezelhesd a fiókadatokat.

Ellenőrizheted, hogy a szerver megfelelően fut-e, ha végrehajtod ezt a parancsot egy terminálban:

```sh
curl http://localhost:5000/api
# -> should return "Bank API v1.0.0" as a result
```

---

## Az állapotkezelés újragondolása

Az [előző leckében](../3-data/README.md) bevezettünk egy alapvető állapotfogalmat az alkalmazásunkban a globális `account` változóval, amely a jelenleg bejelentkezett felhasználó banki adatait tartalmazza. Azonban a jelenlegi megvalósításunknak vannak hibái. Próbáld meg frissíteni az oldalt, amikor a műszerfalon vagy. Mi történik?

A jelenlegi kóddal három probléma van:

- Az állapot nem marad meg, mivel a böngésző frissítése visszavisz a bejelentkezési oldalra.
- Több funkció is módosítja az állapotot. Ahogy az alkalmazás növekszik, nehéz lehet nyomon követni a változásokat, és könnyen elfelejthetjük frissíteni az egyiket.
- Az állapot nincs törölve, így amikor a *Kijelentkezés* gombra kattintasz, a fiókadatok még mindig ott vannak, annak ellenére, hogy a bejelentkezési oldalon vagy.

Frissíthetnénk a kódunkat, hogy egyenként kezeljük ezeket a problémákat, de ez több kódismétlést eredményezne, és az alkalmazás bonyolultabbá és nehezebben karbantarthatóvá válna. Vagy megállhatnánk néhány percre, és újragondolhatnánk a stratégiánkat.

> Milyen problémákat próbálunk valójában megoldani?

Az [állapotkezelés](https://en.wikipedia.org/wiki/State_management) lényege, hogy megtaláljuk a megfelelő megközelítést e két konkrét probléma megoldására:

- Hogyan lehet az adatáramlásokat egy alkalmazásban érthetővé tenni?
- Hogyan lehet az állapotadatokat mindig szinkronban tartani a felhasználói felülettel (és fordítva)?

Ha ezekkel foglalkoztál, bármilyen más probléma, amivel szembesülsz, vagy már megoldódott, vagy könnyebben megoldhatóvá vált. Számos lehetséges megközelítés létezik ezeknek a problémáknak a megoldására, de mi egy általános megoldást választunk, amely abból áll, hogy **központosítjuk az adatokat és a változtatás módjait**. Az adatáramlások így néznének ki:

![Séma, amely az adatáramlásokat mutatja a HTML, a felhasználói műveletek és az állapot között](../../../../translated_images/data-flow.fa2354e0908fecc89b488010dedf4871418a992edffa17e73441d257add18da4.hu.png)

> Itt nem térünk ki arra a részre, ahol az adatok automatikusan frissítik a nézetet, mivel ez a [Reaktív programozás](https://en.wikipedia.org/wiki/Reactive_programming) fejlettebb fogalmaihoz kapcsolódik. Ez egy jó következő téma, ha mélyebben bele szeretnél merülni.

✅ Számos könyvtár létezik különböző megközelítésekkel az állapotkezeléshez, például a [Redux](https://redux.js.org), amely egy népszerű opció. Nézd meg a használt fogalmakat és mintákat, mivel ez gyakran jó módja annak, hogy megértsd, milyen potenciális problémákkal szembesülhetsz nagy webalkalmazásokban, és hogyan lehet ezeket megoldani.

### Feladat

Kezdjünk egy kis refaktorálással. Cseréld ki az `account` deklarációt:

```js
let account = null;
```

Erre:

```js
let state = {
  account: null
};
```

Az ötlet az, hogy *központosítsuk* az összes alkalmazásadatot egyetlen állapotobjektumban. Jelenleg csak az `account` van az állapotban, így ez nem sokat változtat, de megnyitja az utat a további fejlesztések előtt.

Frissítenünk kell az ezt használó funkciókat is. A `register()` és `login()` funkciókban cseréld ki az `account = ...` sort `state.account = ...`-ra.

Az `updateDashboard()` függvény tetején add hozzá ezt a sort:

```js
const account = state.account;
```

Ez a refaktorálás önmagában nem hozott sok javulást, de az ötlet az volt, hogy lefektessük az alapokat a következő változtatásokhoz.

## Az adatok változásainak nyomon követése

Most, hogy létrehoztuk az `state` objektumot az adatok tárolására, a következő lépés az, hogy központosítsuk a frissítéseket. A cél az, hogy könnyebb legyen nyomon követni az esetleges változásokat és azok időpontját.

Az `state` objektum módosításának elkerülése érdekében jó gyakorlatnak számít, ha azt [*változatlannak*](https://en.wikipedia.org/wiki/Immutable_object) tekintjük, ami azt jelenti, hogy egyáltalán nem lehet módosítani. Ez azt is jelenti, hogy új állapotobjektumot kell létrehoznod, ha bármit meg akarsz változtatni benne. Ezzel védelmet építesz ki a potenciálisan nem kívánt [mellékhatások](https://en.wikipedia.org/wiki/Side_effect_(computer_science)) ellen, és lehetőséget nyitsz új funkciók bevezetésére az alkalmazásodban, például visszavonás/újra végrehajtás megvalósítására, miközben megkönnyíted a hibakeresést. Például naplózhatod az állapoton végrehajtott minden változást, és nyilvántarthatod a változások történetét, hogy megértsd egy hiba forrását.

JavaScriptben az [`Object.freeze()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) használatával hozhatsz létre változatlan objektumot. Ha megpróbálsz változtatásokat végrehajtani egy változatlan objektumon, kivétel keletkezik.

✅ Tudod, mi a különbség a *sekély* és a *mély* változatlan objektum között? Erről [itt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze#What_is_shallow_freeze) olvashatsz.

### Feladat

Hozzunk létre egy új `updateState()` függvényt:

```js
function updateState(property, newData) {
  state = Object.freeze({
    ...state,
    [property]: newData
  });
}
```

Ebben a függvényben létrehozunk egy új állapotobjektumot, és az előző állapotból másoljuk az adatokat a [*spread (`...`) operátor*](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Spread_syntax#Spread_in_object_literals) segítségével. Ezután felülírunk egy adott tulajdonságot az állapotobjektumban az új adatokkal a [zárójel notáció](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Working_with_Objects#Objects_and_properties) `[property]` használatával. Végül zároljuk az objektumot, hogy megakadályozzuk a módosításokat az `Object.freeze()` segítségével. Jelenleg csak az `account` tulajdonságot tároljuk az állapotban, de ezzel a megközelítéssel annyi tulajdonságot adhatsz hozzá az állapothoz, amennyire szükséged van.

Frissítsük az `state` inicializálását is, hogy az inicializálási állapot is zárolva legyen:

```js
let state = Object.freeze({
  account: null
});
```

Ezután frissítsd a `register` függvényt az `state.account = result;` helyett:

```js
updateState('account', result);
```

Ugyanezt tedd a `login` függvénnyel, cseréld ki az `state.account = data;` sort:

```js
updateState('account', data);
```

Most kihasználhatjuk az alkalmat, hogy kijavítsuk azt a problémát, hogy a fiókadatok nem törlődnek, amikor a felhasználó a *Kijelentkezés* gombra kattint.

Hozz létre egy új `logout()` függvényt:

```js
function logout() {
  updateState('account', null);
  navigate('/login');
}
```

Az `updateDashboard()` függvényben cseréld ki az `return navigate('/login');` átirányítást `return logout();`-ra.

Próbálj meg regisztrálni egy új fiókot, kijelentkezni, majd újra bejelentkezni, hogy ellenőrizd, minden továbbra is megfelelően működik-e.

> Tipp: Nyomon követheted az összes állapotváltozást, ha hozzáadod a `console.log(state)` sort az `updateState()` függvény aljára, és megnyitod a böngésződ fejlesztői eszközeinek konzolját.

## Az állapot megőrzése

A legtöbb webalkalmazásnak szüksége van az adatok megőrzésére a megfelelő működés érdekében. Az összes kritikus adatot általában egy adatbázisban tárolják, és egy szerver API-n keresztül érik el, például a felhasználói fiókadatokat az esetünkben. De néha az is érdekes lehet, ha néhány adatot a böngésződben futó kliensalkalmazásban tárolsz, a jobb felhasználói élmény vagy a betöltési teljesítmény javítása érdekében.

Amikor adatokat szeretnél tárolni a böngésződben, néhány fontos kérdést fel kell tenned magadnak:

- *Érzékeny-e az adat?* Kerüld az érzékeny adatok, például a felhasználói jelszavak tárolását a kliensen.
- *Meddig van szükséged az adatok megőrzésére?* Csak az aktuális munkamenethez szeretnéd elérni ezeket az adatokat, vagy örökre tárolni szeretnéd őket?

Számos módja van az információk tárolásának egy webalkalmazásban, attól függően, hogy mit szeretnél elérni. Például használhatod az URL-eket egy keresési lekérdezés tárolására, és megoszthatóvá teheted más felhasználók között. Használhatsz [HTTP sütiket](https://developer.mozilla.org/docs/Web/HTTP/Cookies), ha az adatokat meg kell osztani a szerverrel, például az [azonosítási](https://en.wikipedia.org/wiki/Authentication) információkat.

Egy másik lehetőség, hogy a böngésző API-k egyikét használod az adatok tárolására. Két különösen érdekes lehetőség van:

- [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage): egy [kulcs/érték tároló](https://en.wikipedia.org/wiki/Key%E2%80%93value_database), amely lehetővé teszi az adott webhelyhez tartozó adatok megőrzését különböző munkamenetek között. Az itt tárolt adatok soha nem járnak le.
- [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage): ez ugyanúgy működik, mint a `localStorage`, kivéve, hogy az itt tárolt adatok törlődnek, amikor a munkamenet véget ér (amikor a böngészőt bezárod).

Fontos megjegyezni, hogy mindkét API csak [sztringeket](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) képes tárolni. Ha összetett objektumokat szeretnél tárolni, akkor azokat a [JSON](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON) formátumba kell sorosítanod a [`JSON.stringify()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) használatával.

✅ Ha olyan webalkalmazást szeretnél létrehozni, amely nem működik szerverrel, az [`IndexedDB` API](https://developer.mozilla.org/docs/Web/API/IndexedDB_API) használatával kliensoldali adatbázist is létrehozhatsz. Ez az opció fejlettebb esetekre vagy jelentős mennyiségű adat tárolására van fenntartva, mivel bonyolultabb a használata.

### Feladat

Azt szeretnénk, hogy a felhasználók bejelentkezve maradjanak, amíg kifejezetten nem kattintanak a *Kijelentkezés* gombra, ezért a `localStorage`-t fogjuk használni a fiókadatok tárolására. Először definiáljunk egy kulcsot, amelyet az adatok tárolására használunk.

```js
const storageKey = 'savedAccount';
```

Ezután add hozzá ezt a sort az `updateState()` függvény végéhez:

```js
localStorage.setItem(storageKey, JSON.stringify(state.account));
```

Ezzel a felhasználói fiókadatok megőrzésre kerülnek, és mindig naprakészek lesznek, mivel korábban központosítottuk az összes állapotfrissítést. Itt kezdjük el élvezni az összes korábbi refaktorálás előnyeit 🙂.

Mivel az adatok mentésre kerülnek, gondoskodnunk kell azok visszaállításáról is, amikor az alkalmazás betöltődik. Mivel egyre több inicializáló kódunk lesz, érdemes lehet létrehozni egy új `init` függvényt, amely tartalmazza az `app.js` alján található korábbi kódunkat is:

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

Itt visszakeressük a mentett adatokat, és ha van ilyen, frissítjük az állapotot ennek megfelelően. Fontos, hogy ezt *azelőtt* tegyük meg, hogy frissítenénk az útvonalat, mivel az oldalfrissítés során lehet, hogy van olyan kód, amely az állapotra támaszkodik.

Az *Irányítópult* oldalt is megtehetjük az alkalmazásunk alapértelmezett oldalának, mivel most már megőrizzük a fiókadatokat. Ha nem található adat, az irányítópult gondoskodik a *Bejelentkezés* oldalra történő átirányításról. Az `updateRoute()` függvényben cseréld ki az alapértelmezett `return navigate('/login');` sort `return navigate('/dashboard');`-ra.

Most jelentkezz be az alkalmazásba, és próbáld meg frissíteni az oldalt. Az irányítópulton kell maradnod. Ezzel a frissítéssel gondoskodtunk az összes kezdeti problémánkról...

## Az adatok frissítése

...De lehet, hogy egy új problémát is létrehoztunk. Hoppá!

Lépj az irányítópultra a `test` fiókkal, majd futtasd le ezt a parancsot egy terminálban egy új tranzakció létrehozásához:

```sh
curl --request POST \
     --header "Content-Type: application/json" \
     --data "{ \"date\": \"2020-07-24\", \"object\": \"Bought book\", \"amount\": -20 }" \
     http://localhost:5000/api/accounts/test/transactions
```

Most próbáld meg frissíteni az irányítópult oldalt a böngészőben. Mi történik? Látod az új tranzakciót?

Az állapot a `localStorage`-nak köszönhetően határozatlan ideig megmarad, de ez azt is jelenti, hogy soha nem frissül, amíg ki nem jelentkezel az alkalmazás
[Előadás utáni kvíz](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/48)

## Feladat

[Valósítsd meg az „Új tranzakció hozzáadása” párbeszédablakot](assignment.md)

Íme egy példakép az elkészült feladat eredményéről:

![Példa az „Új tranzakció hozzáadása” párbeszédablakra](../../../../translated_images/dialog.93bba104afeb79f12f65ebf8f521c5d64e179c40b791c49c242cf15f7e7fab15.hu.png)

---

**Felelősség kizárása**:  
Ez a dokumentum az AI fordítási szolgáltatás, a [Co-op Translator](https://github.com/Azure/co-op-translator) segítségével lett lefordítva. Bár törekszünk a pontosságra, kérjük, vegye figyelembe, hogy az automatikus fordítások hibákat vagy pontatlanságokat tartalmazhatnak. Az eredeti dokumentum az eredeti nyelvén tekintendő hiteles forrásnak. Kritikus információk esetén javasolt professzionális emberi fordítást igénybe venni. Nem vállalunk felelősséget semmilyen félreértésért vagy téves értelmezésért, amely a fordítás használatából eredhet.