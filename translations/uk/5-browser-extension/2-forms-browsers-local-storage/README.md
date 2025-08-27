<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "e10f168beac4e7b05e30e0eb5c92bf11",
  "translation_date": "2025-08-27T22:17:02+00:00",
  "source_file": "5-browser-extension/2-forms-browsers-local-storage/README.md",
  "language_code": "uk"
}
-->
# Проєкт розширення для браузера, частина 2: Виклик API, використання Local Storage

## Тест перед лекцією

[Тест перед лекцією](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/25)

### Вступ

У цьому уроці ви навчитеся викликати API, надсилаючи форму вашого розширення для браузера, і відображати результати в ньому. Крім того, ви дізнаєтеся, як зберігати дані в локальному сховищі браузера для подальшого використання.

✅ Дотримуйтесь нумерованих сегментів у відповідних файлах, щоб знати, куди вставляти код.

### Налаштування елементів для роботи в розширенні:

На цьому етапі ви вже створили HTML для форми та `<div>` для результатів вашого розширення. Тепер вам потрібно працювати у файлі `/src/index.js` і поступово будувати ваше розширення. Зверніться до [попереднього уроку](../1-about-browsers/README.md) для налаштування проєкту та процесу збірки.

Працюючи у файлі `index.js`, почніть із створення кількох змінних `const`, щоб зберігати значення, пов'язані з різними полями:

```JavaScript
// form fields
const form = document.querySelector('.form-data');
const region = document.querySelector('.region-name');
const apiKey = document.querySelector('.api-key');

// results
const errors = document.querySelector('.errors');
const loading = document.querySelector('.loading');
const results = document.querySelector('.result-container');
const usage = document.querySelector('.carbon-usage');
const fossilfuel = document.querySelector('.fossil-fuel');
const myregion = document.querySelector('.my-region');
const clearBtn = document.querySelector('.clear-btn');
```

Усі ці поля посилаються на їхні CSS-класи, які ви налаштували в HTML на попередньому уроці.

### Додайте слухачі подій

Далі додайте слухачі подій для форми та кнопки очищення, яка скидає форму, щоб при надсиланні форми або натисканні кнопки очищення щось відбувалося. Також додайте виклик для ініціалізації додатка в кінці файлу:

```JavaScript
form.addEventListener('submit', (e) => handleSubmit(e));
clearBtn.addEventListener('click', (e) => reset(e));
init();
```

✅ Зверніть увагу на скорочений запис для прослуховування подій submit або click і на те, як подія передається у функції handleSubmit або reset. Чи можете ви написати еквівалент цього скорочення у довшому форматі? Який підхід вам більше подобається?

### Розробка функцій init() та reset():

Тепер ви створите функцію, яка ініціалізує розширення, і називається вона init():

```JavaScript
function init() {
	//if anything is in localStorage, pick it up
	const storedApiKey = localStorage.getItem('apiKey');
	const storedRegion = localStorage.getItem('regionName');

	//set icon to be generic green
	//todo

	if (storedApiKey === null || storedRegion === null) {
		//if we don't have the keys, show the form
		form.style.display = 'block';
		results.style.display = 'none';
		loading.style.display = 'none';
		clearBtn.style.display = 'none';
		errors.textContent = '';
	} else {
        //if we have saved keys/regions in localStorage, show results when they load
        displayCarbonUsage(storedApiKey, storedRegion);
		results.style.display = 'none';
		form.style.display = 'none';
		clearBtn.style.display = 'block';
	}
};

function reset(e) {
	e.preventDefault();
	//clear local storage for region only
	localStorage.removeItem('regionName');
	init();
}

```

У цій функції є цікава логіка. Прочитавши її, чи можете ви зрозуміти, що відбувається?

- створюються дві змінні `const`, щоб перевірити, чи зберіг користувач APIKey і код регіону в локальному сховищі.
- якщо будь-яке з цих значень дорівнює null, форма відображається, змінюючи її стиль на 'block'.
- приховуються результати, завантаження та кнопка очищення, а текст помилки встановлюється як порожній рядок.
- якщо ключ і регіон існують, запускається процедура:
  - виклик API для отримання даних про вуглецеве споживання,
  - приховування області результатів,
  - приховування форми,
  - відображення кнопки скидання.

Перед тим як рухатися далі, корисно дізнатися про дуже важливу концепцію, доступну в браузерах: [LocalStorage](https://developer.mozilla.org/docs/Web/API/Window/localStorage). LocalStorage — це зручний спосіб зберігати рядки в браузері у вигляді пари `ключ-значення`. Цей тип веб-сховища можна маніпулювати за допомогою JavaScript для управління даними в браузері. LocalStorage не має терміну дії, тоді як SessionStorage, інший вид веб-сховища, очищується при закритті браузера. Різні типи сховищ мають свої переваги та недоліки.

> Примітка: ваше розширення для браузера має власне локальне сховище; головне вікно браузера — це окремий екземпляр і працює незалежно.

Ви встановлюєте значення APIKey як рядок, наприклад, і можете побачити, що воно встановлене в Edge, "інспектуючи" веб-сторінку (ви можете клацнути правою кнопкою миші в браузері, щоб інспектувати) і перейти на вкладку Applications, щоб побачити сховище.

![Панель локального сховища](../../../../translated_images/localstorage.472f8147b6a3f8d141d9551c95a2da610ac9a3c6a73d4a1c224081c98bae09d9.uk.png)

✅ Подумайте про ситуації, коли НЕ варто зберігати деякі дані в LocalStorage. Загалом, розміщення API Keys у LocalStorage — це погана ідея! Чи розумієте ви чому? У нашому випадку, оскільки наш додаток створений виключно для навчання і не буде розміщений у магазині додатків, ми використовуємо цей метод.

Зверніть увагу, що ви використовуєте Web API для маніпуляції LocalStorage, використовуючи `getItem()`, `setItem()` або `removeItem()`. Це широко підтримується у браузерах.

Перш ніж створювати функцію `displayCarbonUsage()`, яка викликається в `init()`, давайте створимо функціонал для обробки початкового надсилання форми.

### Обробка надсилання форми

Створіть функцію `handleSubmit`, яка приймає аргумент події `(e)`. Зупиніть поширення події (у цьому випадку ми хочемо зупинити оновлення браузера) і викличте нову функцію `setUpUser`, передаючи аргументи `apiKey.value` і `region.value`. Таким чином, ви використовуєте два значення, які вводяться через початкову форму, коли відповідні поля заповнені.

```JavaScript
function handleSubmit(e) {
	e.preventDefault();
	setUpUser(apiKey.value, region.value);
}
```

✅ Освіжіть пам'ять — HTML, який ви створили на попередньому уроці, має два поля введення, значення яких захоплюються через `const`, які ви створили на початку файлу, і обидва вони є `required`, тому браузер не дозволяє користувачам вводити порожні значення.

### Налаштування користувача

Переходячи до функції `setUpUser`, тут ви встановлюєте значення локального сховища для apiKey і regionName. Додайте нову функцію:

```JavaScript
function setUpUser(apiKey, regionName) {
	localStorage.setItem('apiKey', apiKey);
	localStorage.setItem('regionName', regionName);
	loading.style.display = 'block';
	errors.textContent = '';
	clearBtn.style.display = 'block';
	//make initial call
	displayCarbonUsage(apiKey, regionName);
}
```

Ця функція встановлює повідомлення про завантаження, яке відображається під час виклику API. На цьому етапі ви дійшли до створення найважливішої функції цього розширення для браузера!

### Відображення вуглецевого споживання

Нарешті, час зробити запит до API!

Перш ніж рухатися далі, варто обговорити API. API, або [Інтерфейси програмування додатків](https://www.webopedia.com/TERM/A/API.html), є критичним елементом інструментарію веб-розробника. Вони забезпечують стандартні способи взаємодії та інтерфейсу між програмами. Наприклад, якщо ви створюєте веб-сайт, який потребує запиту до бази даних, хтось міг створити API для вас. Хоча існує багато типів API, одним із найпопулярніших є [REST API](https://www.smashingmagazine.com/2018/01/understanding-using-rest-api/).

✅ Термін 'REST' означає 'Representational State Transfer' і передбачає використання різноманітно налаштованих URL-адрес для отримання даних. Проведіть невелике дослідження про різні типи API, доступні розробникам. Який формат вам здається найбільш привабливим?

Є важливі моменти, на які слід звернути увагу в цій функції. По-перше, зверніть увагу на ключове слово [`async`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function). Написання ваших функцій таким чином, щоб вони працювали асинхронно, означає, що вони чекатимуть завершення дії, наприклад, повернення даних, перш ніж продовжити.

Ось коротке відео про `async`:

[![Async і Await для управління обіцянками](https://img.youtube.com/vi/YwmlRkrxvkk/0.jpg)](https://youtube.com/watch?v=YwmlRkrxvkk "Async і Await для управління обіцянками")

> 🎥 Натисніть на зображення вище, щоб переглянути відео про async/await.

Створіть нову функцію для запиту до API C02Signal:

```JavaScript
import axios from '../node_modules/axios';

async function displayCarbonUsage(apiKey, region) {
	try {
		await axios
			.get('https://api.co2signal.com/v1/latest', {
				params: {
					countryCode: region,
				},
				headers: {
					'auth-token': apiKey,
				},
			})
			.then((response) => {
				let CO2 = Math.floor(response.data.data.carbonIntensity);

				//calculateColor(CO2);

				loading.style.display = 'none';
				form.style.display = 'none';
				myregion.textContent = region;
				usage.textContent =
					Math.round(response.data.data.carbonIntensity) + ' grams (grams C02 emitted per kilowatt hour)';
				fossilfuel.textContent =
					response.data.data.fossilFuelPercentage.toFixed(2) +
					'% (percentage of fossil fuels used to generate electricity)';
				results.style.display = 'block';
			});
	} catch (error) {
		console.log(error);
		loading.style.display = 'none';
		results.style.display = 'none';
		errors.textContent = 'Sorry, we have no data for the region you have requested.';
	}
}
```

Це велика функція. Що тут відбувається?

- дотримуючись найкращих практик, ви використовуєте ключове слово `async`, щоб ця функція працювала асинхронно. Функція містить блок `try/catch`, оскільки вона повертає обіцянку, коли API повертає дані. Оскільки ви не контролюєте швидкість відповіді API (він може взагалі не відповісти!), вам потрібно обробити цю невизначеність, викликаючи його асинхронно.
- ви робите запит до API co2signal, щоб отримати дані вашого регіону, використовуючи ваш API Key. Щоб використовувати цей ключ, вам потрібно використовувати тип автентифікації в параметрах заголовка.
- коли API відповідає, ви призначаєте різні елементи його відповіді частинам вашого екрану, які ви налаштували для відображення цих даних.
- якщо виникає помилка або результату немає, ви відображаєте повідомлення про помилку.

✅ Використання асинхронних шаблонів програмування — це ще один дуже корисний інструмент у вашому арсеналі. Прочитайте [про різні способи](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function), якими можна налаштувати цей тип коду.

Вітаємо! Якщо ви зберете своє розширення (`npm run build`) і оновите його на панелі розширень, у вас буде працююче розширення! Єдине, що поки не працює, — це іконка, і ви виправите це в наступному уроці.

---

## 🚀 Виклик

Ми обговорили кілька типів API у цих уроках. Оберіть веб-API та дослідіть, що воно пропонує. Наприклад, ознайомтеся з API, доступними в браузерах, такими як [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API). Що, на вашу думку, робить API чудовим?

## Тест після лекції

[Тест після лекції](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/26)

## Огляд і самостійне навчання

У цьому уроці ви дізналися про LocalStorage та API, обидва дуже корисні для професійного веб-розробника. Чи можете ви подумати, як ці дві речі працюють разом? Подумайте, як би ви спроєктували веб-сайт, який зберігає елементи для використання API.

## Завдання

[Освойте API](assignment.md)

---

**Відмова від відповідальності**:  
Цей документ було перекладено за допомогою сервісу автоматичного перекладу [Co-op Translator](https://github.com/Azure/co-op-translator). Хоча ми прагнемо до точності, зверніть увагу, що автоматичні переклади можуть містити помилки або неточності. Оригінальний документ мовою оригіналу слід вважати авторитетним джерелом. Для критично важливої інформації рекомендується професійний людський переклад. Ми не несемо відповідальності за будь-які непорозуміння або неправильні тлумачення, що виникли внаслідок використання цього перекладу.