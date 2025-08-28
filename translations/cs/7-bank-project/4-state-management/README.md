<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "4fa20c513e367e9cdd401bf49ae16e33",
  "translation_date": "2025-08-28T03:34:35+00:00",
  "source_file": "7-bank-project/4-state-management/README.md",
  "language_code": "cs"
}
-->
# Vytvoření bankovní aplikace, část 4: Koncepty správy stavu

## Kvíz před lekcí

[Kvíz před lekcí](https://ff-quizzes.netlify.app/web/quiz/47)

### Úvod

Jak webová aplikace roste, stává se stále obtížnější sledovat všechny datové toky. Který kód získává data, která stránka je využívá, kde a kdy je třeba je aktualizovat... snadno se dostanete k nepořádnému kódu, který je těžké udržovat. To platí zejména tehdy, když potřebujete sdílet data mezi různými stránkami aplikace, například uživatelská data. Koncept *správy stavu* existoval vždy ve všech typech programů, ale jak webové aplikace rostou na složitosti, stává se klíčovým bodem, o kterém je třeba během vývoje přemýšlet.

V této závěrečné části se podíváme na aplikaci, kterou jsme vytvořili, a přehodnotíme, jak je spravován stav, abychom umožnili podporu obnovení prohlížeče kdykoli a zachování dat mezi uživatelskými relacemi.

### Předpoklady

Musíte mít dokončenou část [získávání dat](../3-data/README.md) webové aplikace pro tuto lekci. Také je třeba nainstalovat [Node.js](https://nodejs.org) a [spustit serverové API](../api/README.md) lokálně, abyste mohli spravovat data účtu.

Můžete otestovat, zda server běží správně, spuštěním tohoto příkazu v terminálu:

```sh
curl http://localhost:5000/api
# -> should return "Bank API v1.0.0" as a result
```

---

## Přehodnocení správy stavu

V [předchozí lekci](../3-data/README.md) jsme představili základní koncept stavu v naší aplikaci pomocí globální proměnné `account`, která obsahuje bankovní data aktuálně přihlášeného uživatele. Naše současná implementace má však několik nedostatků. Zkuste obnovit stránku, když jste na přehledu. Co se stane?

V aktuálním kódu jsou tři problémy:

- Stav není zachován, protože obnovení prohlížeče vás vrátí na přihlašovací stránku.
- Existuje více funkcí, které stav upravují. Jak aplikace roste, může být obtížné sledovat změny a snadno zapomenete na aktualizaci jedné z nich.
- Stav není vyčištěn, takže když kliknete na *Odhlásit se*, data účtu tam stále jsou, i když jste na přihlašovací stránce.

Mohli bychom aktualizovat náš kód, abychom tyto problémy řešili jeden po druhém, ale to by vedlo k většímu duplicitnímu kódu a aplikace by byla složitější a obtížněji udržovatelná. Nebo bychom si mohli na pár minut udělat pauzu a přehodnotit naši strategii.

> Jaké problémy se zde vlastně snažíme vyřešit?

[Správa stavu](https://en.wikipedia.org/wiki/State_management) je o nalezení dobrého přístupu k řešení těchto dvou konkrétních problémů:

- Jak udržet datové toky v aplikaci srozumitelné?
- Jak udržet data stavu vždy synchronizovaná s uživatelským rozhraním (a naopak)?

Jakmile se o tyto problémy postaráte, jakékoli další problémy, které byste mohli mít, mohou být buď již vyřešeny, nebo se staly snazšími k řešení. Existuje mnoho možných přístupů k řešení těchto problémů, ale my se zaměříme na běžné řešení, které spočívá v **centralizaci dat a způsobů jejich změny**. Datové toky by vypadaly takto:

![Schéma zobrazující datové toky mezi HTML, uživatelskými akcemi a stavem](../../../../translated_images/data-flow.fa2354e0908fecc89b488010dedf4871418a992edffa17e73441d257add18da4.cs.png)

> Zde se nebudeme zabývat částí, kde data automaticky spouštějí aktualizaci zobrazení, protože je to spojeno s pokročilejšími koncepty [reaktivního programování](https://en.wikipedia.org/wiki/Reactive_programming). Je to dobré téma k dalšímu studiu, pokud se chcete ponořit hlouběji.

✅ Existuje mnoho knihoven s různými přístupy ke správě stavu, přičemž [Redux](https://redux.js.org) je populární volbou. Podívejte se na koncepty a vzory, které používají, protože často poskytují dobrý přehled o potenciálních problémech, kterým můžete čelit ve velkých webových aplikacích, a o tom, jak je lze řešit.

### Úkol

Začneme malou refaktorizací. Nahraďte deklaraci `account`:

```js
let account = null;
```

Tímto:

```js
let state = {
  account: null
};
```

Myšlenkou je *centralizovat* všechna data naší aplikace do jediného objektu stavu. Zatím máme ve stavu pouze `account`, takže se toho moc nezmění, ale vytváříme tím cestu pro budoucí rozšíření.

Musíme také aktualizovat funkce, které jej používají. Ve funkcích `register()` a `login()` nahraďte `account = ...` za `state.account = ...`;

Na začátek funkce `updateDashboard()` přidejte tento řádek:

```js
const account = state.account;
```

Tato refaktorizace sama o sobě nepřinesla mnoho vylepšení, ale myšlenkou bylo položit základy pro další změny.

## Sledování změn dat

Nyní, když jsme zavedli objekt `state` pro ukládání našich dat, dalším krokem je centralizace aktualizací. Cílem je usnadnit sledování jakýchkoli změn a kdy k nim dochází.

Abychom zabránili provádění změn v objektu `state`, je také dobré považovat jej za [*neměnný*](https://en.wikipedia.org/wiki/Immutable_object), což znamená, že jej nelze vůbec upravovat. To také znamená, že musíte vytvořit nový objekt stavu, pokud chcete v něm něco změnit. Tímto způsobem si vytvoříte ochranu proti potenciálně nežádoucím [vedlejším efektům](https://en.wikipedia.org/wiki/Side_effect_(computer_science)) a otevřete možnosti pro nové funkce ve vaší aplikaci, jako je implementace funkce zpět/znovu, a zároveň usnadníte ladění. Například byste mohli zaznamenávat každou změnu provedenou ve stavu a uchovávat historii změn, abyste pochopili zdroj chyby.

V JavaScriptu můžete použít [`Object.freeze()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) k vytvoření neměnné verze objektu. Pokud se pokusíte provést změny v neměnném objektu, bude vyvolána výjimka.

✅ Znáte rozdíl mezi *mělkým* a *hlubokým* neměnným objektem? Můžete si o tom přečíst [zde](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze#What_is_shallow_freeze).

### Úkol

Vytvořme novou funkci `updateState()`:

```js
function updateState(property, newData) {
  state = Object.freeze({
    ...state,
    [property]: newData
  });
}
```

V této funkci vytváříme nový objekt stavu a kopírujeme data z předchozího stavu pomocí [*operátoru rozbalení (`...`)*](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Spread_syntax#Spread_in_object_literals). Poté přepíšeme konkrétní vlastnost objektu stavu novými daty pomocí [notace hranatých závorek](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Working_with_Objects#Objects_and_properties) `[property]` pro přiřazení. Nakonec objekt uzamkneme, aby nebylo možné provádět úpravy, pomocí `Object.freeze()`. Zatím máme ve stavu pouze vlastnost `account`, ale s tímto přístupem můžete do stavu přidat tolik vlastností, kolik potřebujete.

Také aktualizujeme inicializaci `state`, abychom zajistili, že počáteční stav bude také zmrazen:

```js
let state = Object.freeze({
  account: null
});
```

Poté aktualizujte funkci `register` nahrazením přiřazení `state.account = result;` za:

```js
updateState('account', result);
```

Uděláme totéž s funkcí `login`, kde nahradíme `state.account = data;` za:

```js
updateState('account', data);
```

Nyní využijeme příležitosti k opravě problému, kdy data účtu nejsou vymazána, když uživatel klikne na *Odhlásit se*.

Vytvořte novou funkci `logout()`:

```js
function logout() {
  updateState('account', null);
  navigate('/login');
}
```

V `updateDashboard()` nahraďte přesměrování `return navigate('/login');` za `return logout();`;

Zkuste zaregistrovat nový účet, odhlásit se a znovu se přihlásit, abyste ověřili, že vše stále funguje správně.

> Tip: můžete se podívat na všechny změny stavu přidáním `console.log(state)` na konec `updateState()` a otevřením konzole v nástrojích pro vývojáře vašeho prohlížeče.

## Zachování stavu

Většina webových aplikací potřebuje uchovávat data, aby mohla správně fungovat. Všechna kritická data jsou obvykle uložena v databázi a přistupuje se k nim prostřednictvím serverového API, jako jsou například uživatelská data účtu v našem případě. Někdy je však také zajímavé uchovávat některá data na klientské aplikaci běžící ve vašem prohlížeči, pro lepší uživatelský zážitek nebo pro zlepšení výkonu načítání.

Když chcete uchovávat data ve vašem prohlížeči, měli byste si položit několik důležitých otázek:

- *Jsou data citlivá?* Měli byste se vyhnout ukládání jakýchkoli citlivých dat na klienta, jako jsou uživatelská hesla.
- *Jak dlouho potřebujete tato data uchovávat?* Plánujete přistupovat k těmto datům pouze během aktuální relace, nebo je chcete uchovávat navždy?

Existuje několik způsobů, jak ukládat informace uvnitř webové aplikace, v závislosti na tom, čeho chcete dosáhnout. Například můžete použít URL k uložení vyhledávacího dotazu a umožnit jeho sdílení mezi uživateli. Můžete také použít [HTTP cookies](https://developer.mozilla.org/docs/Web/HTTP/Cookies), pokud je potřeba data sdílet se serverem, například informace o [autentizaci](https://en.wikipedia.org/wiki/Authentication).

Další možností je použití jedné z mnoha API pro ukládání dat v prohlížeči. Dvě z nich jsou obzvláště zajímavé:

- [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage): [Key/Value store](https://en.wikipedia.org/wiki/Key%E2%80%93value_database), který umožňuje uchovávat data specifická pro aktuální webovou stránku mezi různými relacemi. Data uložená v něm nikdy nevyprší.
- [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage): funguje stejně jako `localStorage`, kromě toho, že data uložená v něm jsou vymazána, když relace skončí (když je prohlížeč zavřen).

Všimněte si, že obě tato API umožňují ukládat pouze [řetězce](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String). Pokud chcete ukládat složité objekty, budete je muset serializovat do formátu [JSON](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON) pomocí [`JSON.stringify()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).

✅ Pokud chcete vytvořit webovou aplikaci, která nepracuje se serverem, je také možné vytvořit databázi na klientovi pomocí [`IndexedDB` API](https://developer.mozilla.org/docs/Web/API/IndexedDB_API). Toto je vyhrazeno pro pokročilé případy použití nebo pokud potřebujete ukládat významné množství dat, protože je složitější na použití.

### Úkol

Chceme, aby naši uživatelé zůstali přihlášeni, dokud explicitně nekliknou na tlačítko *Odhlásit se*, takže použijeme `localStorage` k ukládání dat účtu. Nejprve definujme klíč, který použijeme k ukládání našich dat.

```js
const storageKey = 'savedAccount';
```

Poté přidejte tento řádek na konec funkce `updateState()`:

```js
localStorage.setItem(storageKey, JSON.stringify(state.account));
```

Tímto způsobem budou data uživatelského účtu zachována a vždy aktuální, protože jsme dříve centralizovali všechny aktualizace stavu. Zde začínáme těžit ze všech našich předchozích refaktorů 🙂.

Protože jsou data uložena, musíme se také postarat o jejich obnovení při načtení aplikace. Vzhledem k tomu, že začneme mít více inicializačního kódu, může být dobrý nápad vytvořit novou funkci `init`, která také zahrnuje náš předchozí kód na konci `app.js`:

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

Zde načítáme uložená data a pokud nějaká existují, aktualizujeme stav odpovídajícím způsobem. Je důležité to udělat *před* aktualizací trasy, protože během aktualizace stránky může být kód závislý na stavu.

Můžeme také nastavit stránku *Dashboard* jako výchozí stránku naší aplikace, protože nyní zachováváme data účtu. Pokud žádná data nenajdeme, přehled se stejně postará o přesměrování na stránku *Login*. V `updateRoute()` nahraďte výchozí `return navigate('/login');` za `return navigate('/dashboard');`.

Nyní se přihlaste do aplikace a zkuste obnovit stránku. Měli byste zůstat na přehledu. Touto aktualizací jsme se postarali o všechny naše počáteční problémy...

## Aktualizace dat

...Ale možná jsme také vytvořili nový problém. Ups!

Přejděte na přehled pomocí účtu `test`, poté spusťte tento příkaz v terminálu pro vytvoření nové transakce:

```sh
curl --request POST \
     --header "Content-Type: application/json" \
     --data "{ \"date\": \"2020-07-24\", \"object\": \"Bought book\", \"amount\": -20 }" \
     http://localhost:5000/api/accounts/test/transactions
```

Zkuste nyní obnovit stránku přehledu v prohlížeči. Co se stane? Vidíte novou transakci?

Stav je zachován na neurčito díky `localStorage`, ale to také znamená, že se nikdy neaktualizuje, dokud se z aplikace neodhlásíte a znovu nepřihlásíte!

Jednou z možných strategií, jak to opravit, je znovu načíst data účtu pokaždé, když je přehled načten, aby se předešlo zastaralým datům.

### Úkol

Vytvořte novou funkci `updateAccountData`:

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

Tato metoda ověřuje, že jsme aktuálně přihlášeni, a poté znovu načte data účtu ze serveru.

Vytvořte další funkci s názvem `refresh`:

```js
async function refresh() {
  await updateAccountData();
  updateDashboard();
}
```

Tato funkce aktualizuje data účtu a poté se postará o aktualizaci HTML stránky přehledu. To je to, co potřebujeme zavolat, když je načtena trasa přehledu. Aktualizujte definici trasy na:

```js
const routes = {
  '/login': { templateId: 'login' },
  '/dashboard': { templateId: 'dashboard', init: refresh }
};
```

Zkuste nyní obnovit přehled, měl by zobrazit aktualizovaná data účtu.

---

## 🚀 Výzva

Nyní, když znovu načítáme data účtu pokaždé, když je přehled načten, myslíte si, že stále potřebujeme zachovávat *všechna data účtu*?

Zkuste společně změnit, co je uloženo a načítáno z `localStorage`, aby zahrnovalo pouze to, co je pro fungování aplikace absolutně nezbytné.

## Kvíz po lekci
[Post-přednáškový kvíz](https://ff-quizzes.netlify.app/web/quiz/48)

## Úkol

[Implementujte dialog "Přidat transakci"](assignment.md)

Zde je příklad výsledku po dokončení úkolu:

![Snímek obrazovky zobrazující příklad dialogu "Přidat transakci"](../../../../translated_images/dialog.93bba104afeb79f12f65ebf8f521c5d64e179c40b791c49c242cf15f7e7fab15.cs.png)

---

**Prohlášení**:  
Tento dokument byl přeložen pomocí služby AI pro překlady [Co-op Translator](https://github.com/Azure/co-op-translator). Ačkoli se snažíme o přesnost, mějte prosím na paměti, že automatizované překlady mohou obsahovat chyby nebo nepřesnosti. Původní dokument v jeho původním jazyce by měl být považován za autoritativní zdroj. Pro důležité informace se doporučuje profesionální lidský překlad. Neodpovídáme za žádné nedorozumění nebo nesprávné interpretace vyplývající z použití tohoto překladu.