---
title: Синтаксис шаблонов
---


### Теги

---

Тег в нижнем регистре, например `<div>` — это обычный HTML элемент. Тег, написанный с большой буквы, такой как `<Widget>` или `<Namespace.Widget>`, обозначает *компонент*.

```sv
<script>
	import Widget from './Widget.svelte';
</script>

<div>
	<Widget/>
</div>
```


### Атрибуты и свойства

---


По умолчанию атрибуты работают точно так же, как их HTML-аналоги.

```sv
<div class="foo">
	<button disabled>ненажимаемая кнопка</button>
</div>
```

---

Как и в HTML, значения могут быть указаны без кавычек.

```sv
<input type=checkbox>
```

---

Значения атрибута могут содержать выражения JavaScript.

```sv
<a href="page/{p}">страница {p}</a>
```

---

Или они *целиком* могут быть выражениями JavaScript.

```sv
<button disabled={!clickable}>...</button>
```
---

Булевые атрибуты присоединяются к элементу, если их значения [истинное](https://developer.mozilla.org/ru/docs/%D0%A1%D0%BB%D0%BE%D0%B2%D0%B0%D1%80%D1%8C/Truthy) и убираются с него, когда значения являются [ложными](https://developer.mozilla.org/ru/docs/%D0%A1%D0%BB%D0%BE%D0%B2%D0%B0%D1%80%D1%8C/Falsy).

Все остальные атрибуты будут присоединены к элементу, только если их значение не является [null-образным](https://developer.mozilla.org/ru/docs/Glossary/Nullish) (`null` или `undefined`).

```sv
<input required={false} placeholder="Это поле ввода не является обязательным">
<div title={null}>У этого контейнера нет атрибута title</div>
```

---

Выражение может содержать символы, которые могут вызвать проблемы при подсветке синтаксиса в обычном HTML, поэтому допускается использование значения в кавычках. Кавычки не влияют на то, как анализируется значение:

```sv
<button disabled="{number !== 42}">...</button>
```

---

Когда имя и значение атрибута совпадают (`name = {name}`) — их можно заменить на `{name}`.

```sv
<!-- Эти строки эквивалентны -->
<button disabled={disabled}>...</button>
<button {disabled}>...</button>
```

---

Значения, передаваемые в компоненты, называются *свойствами* или *пропсами*, но не *атрибутами*, поскольку они не относятся к DOM.

Как и в DOM-элементах, `name={name}` можно заменить сокращением `{name}`.

```sv
<Widget foo={bar} answer={42} text="hello"/>
```

---

*Развёртка атрибутов* позволяет передать компоненту сразу несколько атрибутов или свойств.

Элемент или компонент может иметь сразу несколько таких развёрток вперемешку с обычными атрибутами.

```sv
<Widget {...things}/>
```
---

Объект *`$$props`* содержит в себе все свойства передаваемые компоненту, включая и те, которые не объявлены с помощью оператора `export`. В редких случаях использование этого объекта может быть полезным, но в целом не рекомендуется его использовать, поскольку это вызовет определённые трудности у Svelte при оптимизации приложения.

```sv
<Widget {...$$props}/>
```

---

Объект *`$$restProps`* содержит в себе только те переданные свойства, которые не были объявлены при помощи оператора `export`. Он может быть использован для передачи заранее неизвестных атрибутов какому-либо элементу внутри компонента.

```sv
<input {...$$restProps}>
```

> Атрибут `value` у элемента `input`, а также дочерние элементы `option` не могут быть установлены развёрткой атрибутов когда используются привязки `bind:group` или `bind:checked`. В такой ситуации Svelte необходимо видеть значения атрибутов `value` непосредственно в шаблоне, чтобы связать его с соответствующей переменной.
---

### Текстовые выражения

```sv
{выражение}
```

---

Текст также может содержать JavaScript-выражения:

```sv
<h1>Привет, {name}!</h1>
<p>{a} + {b} = {a + b}.</p>
```

### Комментарии

---

Вы можете использовать внутри компонентов обычные HTML-комментарии.

```sv
<!-- это комментарий! -->
<h1>Привет мир</h1>
```

---

Комментарии, которые начинаются со `svelte-ignore`, отключают определённые предупреждения для следующего за ним блока разметки. Обычно это используется для предупреждения о доступности — убедитесь, что вы отключаете их по уважительной причине.

```sv
<!-- svelte-ignore a11y-autofocus -->
<input bind:value={name} autofocus>
```

### {#if ...}

```sv
{#if выражение}...{/if}
```
```sv
{#if выражение}...{:else if выражение}...{/if}
```
```sv
{#if выражение}...{:else}...{/if}
```

---

Для отображения содержимого при выполнении какого-либо условия, нужно добавить его в блок `if`.

```sv
{#if answer === 42}
	<p>а какой был вопрос?</p>
{/if}
```

---

Дополнительные условия могут быть добавлены с помощью `{:else if выражение}`, а блок альтернативной разметки помещен после `{:else}`.

```sv
{#if porridge.temperature > 35}
	<p>слишком горячая!</p>
{:else if 25 > porridge.temperature}
	<p>слишком холодная!</p>
{:else}
	<p>то, что надо!</p>
{/if}
```


### {#each ...}

```sv
{#each выражение as имя}...{/each}
```
```sv
{#each выражение as имя, индекс}...{/each}
```
```sv
{#each выражение as имя (ключ)}...{/each}
```
```sv
{#each выражение as имя, индекс (ключ)}...{/each}
```
```sv
{#each выражение as имя}...{:else}...{/each}
```

---

Перебор списков значений может быть выполнен блоком `each`.

```sv
<h1>Список покупок</h1>
<ul>
	{#each items as item}
		<li>{item.name} x {item.qty}</li>
	{/each}
</ul>
```

Можно выполнять перебор по массиву или массивоподобному значению, т.е. по любому объекту, у которого есть свойство `length`.

---

Блок `each` также может отдавать *индекс* элемента, аналогично второму аргументу callback-функции в `array.map(...)`:

```sv
{#each items as item, i}
	<li>{i + 1}: {item.name} x {item.qty}</li>
{/each}
```

---

Если указать параметр *ключ*, который однозначно идентифицирует каждый элемент списка, то при изменении данных Svelte будет использовать его для изменения списка в нужном месте, а не просто удалять и добавлять элементы в конец массива. Ключом может быть любой объект, но рекомендуется использовать строки и числа, поскольку они позволяют сохранять уникальность при изменении самих объектов.

```sv
{#each items as item, i (item.id)}
	<li>{i + 1}: {item.name} x {item.qty}</li>
{/each}
```

---

При желании в блоках `each` можно использовать деструктуризацию и развёртку:

```sv
{#each items as { id, name, qty }, i (id)}
	<li>{i + 1}: {name} x {qty}</li>
{/each}

{#each objects as { id, ...rest }}
	<li><span>{id}</span><MyComponent {...rest}/></li>
{/each}

{#each items as [id, ...rest]}
	<li><span>{id}</span><MyComponent values={rest}/></li>
{/each}
```

---

Блок `each` тоже может иметь блок `{:else}`, который будет отрисовываться, если переданный список окажется пустым.

```sv
{#each todos as todo}
	<p>{todo.text}</p>
{:else}
	<p>Нет задач!</p>
{/each}
```


### {#await ...}

```sv
{#await выражение}...{:then имя}...{:catch имя}...{/await}
```
```sv
{#await выражение}...{:then имя}...{/await}
```
```sv
{#await выражение then имя}...{/await}
```
```sv
{#await выражение catch имя}...{/await}
```

---

Блоки `await` позволяют обрабатывать три возможных состояния промисов  — ожидание, выполнение или отклонение.
```sv
{#await promise}
	<!-- промис в состоянии ожидания -->
	<p>Ждём пока промис выполнится...</p>
{:then value}
	<!-- промис выполнился -->
	<p>Значение равно {value}</p>
{:catch error}
	<!-- промис отклонён -->
	<p>Что-то пошло не так: {error.message}</p>
{/await}
```

---

Блок `catch` может быть опущен, если не нужно ничего отрисовывать, при отклонении промиса (или это вообще невозможно).

```sv
{#await promise}
	<!-- промис в состоянии ожидания -->
	<p>Ждём пока промис выполнится...</p>
{:then value}
	<!-- промис выполнился -->
	<p>Значение равно {value}</p>
{/await}
```

---

Если состояние ожидания промиса не требует информирования, можно также опустить и первый блок.

```sv
{#await promise then value}
	<p>Значение равно {value}</p>
{/await}
```

---

И наоборот, если нужно показать только ошибку, можно опустить блок `then`.

```sv
{#await promise catch error}
	<p>Произошла ошибка: {error}</p>
{/await}
```


### {#key ...}

```sv
{#key выражение}...{/key}
```

Блок `key` уничтожает и пересоздаёт свое содержимое всякий раз, когда значение выражения меняется.

---

Он полезен когда, вам нужно проиграть какой-либо переход при изменении значения переменной.

```sv
{#key value}
	<div transition:fade>{value}</div>
{/key}
```

---

Когда внутри помещаются компоненты, они будут пересозданы заново.

```sv
{#key value}
	<Component />
{/key}
```

### {@html ...}

```sv
{@html выражение}
```

---

В текстовых выражениях, символы вроде `<` и `>` экранируются, а в HTML выражениях — нет.

Выражение должно быть самодостаточной и валидной HTML-разметкой — `{@html "<div>"}содержимое{@html "</div>"}` *не сработает*, поскольку `</div>` не является валидной HTML-разметкой.


> Svelte не очищает HTML код перед его обработкой! Если данные приходят из ненадёжного источника, необходимо их проверить. В противном случае, вы подвергаете пользователей возможным XSS-атакам.


```sv
<div class="blog-post">
	<h1>{post.title}</h1>
	{@html post.content}
</div>
```


### {@debug ...}

```sv
{@debug}
```
```sv
{@debug переменная1, переменная2, ..., переменнаяN}
```

---

Тег `{@debug ...}` — это альтернатива для функции `console.log (...)`. Он отображает значение указанных переменных при их изменении, и приостанавливает дальнейшее выполнение кода при открытых *инструментах разработчика* в браузере.

```sv
<script>
	let user = {
		firstname: 'Ада',
		lastname: 'Лавлейс'
	};
</script>

{@debug user}

<h1>Привет {user.firstname}!</h1>
```

---

`{@debug ...}` принимает разделенный запятыми список имён переменных (не любых выражений).

```sv
<!-- Успешно скомпилируется -->
{@debug user}
{@debug user1, user2, user3}

<!-- НЕ скомпилируется -->
{@debug user.firstname}
{@debug myArray[0]}
{@debug !isReady}
{@debug typeof user === 'object'}
```

Тег `{@debug}` без каких-либо аргументов установит оператор `debugger`, который будет срабатывать при любом изменении состояния.



### Директивы элементов

Наряду с атрибутами у элементов могут быть и *директивы*, которые предоставляют некоторые дополнительные возможности.


#### [on:*событие*](on_element_event)

```sv
on:событие={обработчик}
```
```sv
on:событие|модификаторы={обработчик}
```

---

Используйте директиву `on:` для обработки событий в DOM.

```sv
<script>
	let count = 0;

	function handleClick(event) {
		count += 1;
	}
</script>

<button on:click={handleClick}>
	счётчик: {count}
</button>
```

---

Можно задать обработчики непосредственно на месте, без какой-либо потери производительности.  Как и у атрибутов, значения директив могут быть заключены в кавычки для совместимости с подсветкой синтаксиса.

```sv
<button on:click="{() => count += 1}">
	счётчик: {count}
</button>
```

---

*Модификаторы* событий DOM добавляются после символа `|`.

```sv
<form on:submit|preventDefault={handleSubmit}>
	<!-- стандартная обработка события `submit` предотвращена,
	     поэтому страница не перезагрузится -->
</form>
```

Доступны следующие модификаторы:

* `preventDefault` — вызывает `event.preventDefault()` перед запуском обработчика. Полезно, в том числе для обработки форм на клиентской стороне.
* `stopPropagation` — вызывает `event.stopPropagation()`, предотвращает распространение события до следующих элементов.
* `passive` — улучшает производительность прокрутки при тач-событиях или при прокрутке колёсиком мышки (Svelte добавит этот модификатор автоматически там, где это безопасно).
* `nonpassive` — явно устанавливает `passive: false`
* `capture` — вызывает событие в режиме *capture* вместо *bubbling*.
* `once` — удаляет обработчик события после первого вызова.
* `self` — обработчик сработает, только если в свойстве event.target находится сам элемент, на котором слушается событие


Модификаторы можно соединять в цепочку, например `on:click|once|capture={...}`.

---

Если директива `on:` используется без значения, то компонент будет *пробрасывать* событие. Таким образом можно обрабатывать события из глубоко вложенных компонентов.

```sv
<button on:click>
	Так событие клика по кнопке сможет выйти за пределы компонента
</button>
```
---

Можно назначить несколько обработчиков для одного события:

```sv
<script>
	let counter = 0;
	function increment() {
		counter = counter + 1;
	}
	function track(event) {
		trackEvent(event)
	}
</script>

<button on:click={increment} on:click={track}>Нажми меня!</button>
```




#### [bind:*свойство*](bind_element_property)

```sv
bind:свойство={переменная}
```

---

Данные обычно передаются от родительского элемента к потомкам. Директива `bind:` позволяет передавать данные в другую сторону, от дочернего элемента в родительский. Большинство привязок соотносятся с конкретными элементами.

Простейшие привязки просто передают значение свойства элемента, например, `input.value`.

```sv
<input bind:value={name}>
<textarea bind:value={text}></textarea>

<input type="checkbox" bind:checked={yes}>
```

---

Если имя свойства и значения одинаковые, можно использовать сокращение.

```sv
<!-- Эти строки эквивалентны -->
<input bind:value={value}>
<input bind:value>
```

---

Значения из числовых `input` элементов автоматически приводятся к нужному типу. В структуре DOM значение свойства `input.value` таких элементов будет являться строкой, но Svelte будет рассматривать его как число. Если значение `input` пустое или недействительное (в случае `type="number"`), оно будет равно `undefined`.

```sv
<input type="number" bind:value={num}>
<input type="range" bind:value={num}>
```

---

На элементах`<input>`с атрибутом `type="file"` можно использовать привязку `bind:files`, чтобы получить [список выбранных файлов в виде `FileList`](https://developer.mozilla.org/ru/docs/Web/API/FileList).

```sv
<label for="avatar">Загрузить картинку:</label>
<input
	accept="image/png, image/jpeg"
	bind:files
	id="avatar"
	name="avatar"
	type="file"
/>
```


##### Привязка к значению `<select>`

---

Привязка значения `<select>` соответствует свойству `value` в выбранном `<option>`, которое может быть абсолютно любым значением, а не только строкой, как это обычно бывает в DOM.

```sv
<select bind:value={selected}>
	<option value={a}>a</option>
	<option value={b}>b</option>
	<option value={c}>c</option>
</select>
```

---

Элемент `<select multiple>` ведет себя аналогично группе чекбоксов.


```sv
<select multiple bind:value={fillings}>
	<option value="Rice">Rice</option>
	<option value="Beans">Beans</option>
	<option value="Cheese">Cheese</option>
	<option value="Guac (extra)">Guac (extra)</option>
</select>
```

---

Когда значение `<option>` соответствует его текстовому содержимому, атрибут может быть опущен.

```sv
<select multiple bind:value={fillings}>
	<option>Rice</option>
	<option>Beans</option>
	<option>Cheese</option>
	<option>Guac (extra)</option>
</select>
```
---

Элементы с атрибутом `contenteditable` поддерживают привязки к `innerHTML` и `textContent`.

```sv
<div contenteditable="true" bind:innerHTML={html}></div>
```

##### Привязка к медиа-элементам

---

Медиа-элементы (`<audio>` и `<video>`) имеют свой собственный набор привязок — шесть *только для чтения* ...

* `duration` (только для чтения) — общая продолжительность, в секундах
* `buffered` (только для чтения) — буфер, массив объектов `{start, end}`
* `played` (только для чтения) — уже проиграно, то же самое
* `seekable` (только для чтения) — доступное для перемотки, то же самое
* `seeking` (только для чтения) — выполняется ли перемотка
* `ended` (только для чтения) — окончилось ли воспроизведение

... и пять двусторонних привязок:
* `currentTime` — текущая позиция проигрывания, в секундах
* `playbackRate` — скорость проигрывания видео, где 1 обозначает 'нормально'
* `paused` — остановлено проигрывание или нет
* `volume` — громкость, значение между 0 и 1
* `muted` — логическое значение, указывающее приглушен ли звук

Для видео также доступны привязки для чтения ширины `videoWidth` и высоты `videoHeight`.

```sv
<video
		src={clip}
	bind:duration
	bind:buffered
	bind:seekable
	bind:seeking
	bind:played
	bind:ended
	bind:currentTime
	bind:paused
	bind:volume
	bind:videoWidth
	bind:videoHeight
></video>
```

##### Привязка к блочным элементам

---

У блочных элементов есть 4 привязки, доступных только для чтения. Они рассчитываются с использованием метода, аналогичного [этому](http://www.backalleycoder.com/2013/03/18/cross-browser-event-based-element-resize-detection/):

* `clientWidth`
* `clientHeight`
* `offsetWidth`
* `offsetHeight`

```sv
<div
	bind:offsetWidth={width}
	bind:offsetHeight={height}
>
	<Chart {width} {height}/>
</div>
```

#### bind:group

```sv
bind:group={переменная}
```

---

Элементы `input`, которые работают вместе, могут использовать привязку `bind:group`.

```sv
<script>
	let tortilla = 'Plain';
	let fillings = [];
</script>

<!-- сгруппированные радиокнопки являются взаимоисключающими -->
<input type="radio" bind:group={tortilla} value="Plain">
<input type="radio" bind:group={tortilla} value="Whole wheat">
<input type="radio" bind:group={tortilla} value="Spinach">

<!-- сгруппированные чекбоксы образуют массив своих значений -->
<input type="checkbox" bind:group={fillings} value="Rice">
<input type="checkbox" bind:group={fillings} value="Beans">
<input type="checkbox" bind:group={fillings} value="Cheese">
<input type="checkbox" bind:group={fillings} value="Guac (extra)">
```

#### [bind:this](bind_element)

```sv
bind:this={DOM-узел}
```
---

Для получения ссылки на сам элемент в DOM, используйте `bind:this`.

```sv
<script>
	import { onMount } from 'svelte';

	let canvasElement;

	onMount(() => {
		const ctx = canvasElement.getContext('2d');
		drawStuff(ctx);
	});
</script>

<canvas bind:this={canvasElement}></canvas>
```


#### class:*имя*

```sv
class:имя={значение}
```
```sv
class:имя
```

---

Директива `class:` обеспечивает простой способ управления классами элемента.

```sv
<!-- Эти строки эквивалентны -->
<div class="{active ? 'active' : ''}">...</div>
<div class:active={active}>...</div>

<!-- Сокращение, при одинаковых имени и значении -->
<div class:active>...</div>

<!-- можно использовать сразу несколько переключателей классов -->
<div class:active class:inactive={!active} class:isAdmin>...</div>
```


#### use:*действие*

```sv
use:действие
```
```sv
use:действие={параметры}
```

```js
action = (node: HTMLElement, parameters: any) => {
	update?: (parameters: any) => void,
	destroy?: () => void
}
```

---

Действия — это функции, которые вызываются при создании элемента. Они могут возвращать объект с методом `destroy`, который вызывается, когда этот элемент удаляется из DOM.

```sv
<script>
	function foo(node) {
		// при добавлении элемента в DOM
		// node — ссылка на элемент в DOM

		return {
			destroy() {
				// при удалении элемента из DOM
			}
		};
	}
</script>

<div use:foo></div>
```

---

Действие может иметь параметр. Если возвращаемый объект имеет метод `update`, он будет вызываться всякий раз, когда этот параметр изменяется, сразу же после изменения разметки.

> Не беспокойтесь о том, что мы объявляем функцию `foo` в каждом экземпляре компонента — Svelte выведет из контекста компонента любые функции, которые не зависят от его локального состояния.

```sv
<script>
	export let bar;

	function foo(node, bar) {
		// при добавлении элемента в DOM
		// node — ссылка на элемент в DOM

		return {
			update(bar) {
				// при изменении значения параметра `bar`
			},

			destroy() {
				// при удалении элемента из DOM
			}
		};
	}
</script>

<div use:foo={bar}></div>
```


#### transition:*fn*

```sv
transition:fn
```
```sv
transition:fn={параметры}
```
```sv
transition:fn|local
```
```sv
transition:fn|local={параметры}
```


```js
transition = (node: HTMLElement, params: any) => {
	delay?: number,
	duration?: number,
	easing?: (t: number) => number,
	css?: (t: number, u: number) => string,
	tick?: (t: number, u: number) => void
}
```

---

Переход инициируется добавлением или удалением элемента из DOM в результате изменения состояния приложения. 

Элементы внутри *исчезающего* блока, включая и те, которым не назначены собственные переходы, будут находиться в DOM до тех пор, пока не будут выполнены все запущенные внутри блока переходы.

Директива `transition:` указывает на то, что переход является *обратимым*. Т.е. он может в любой момент начать проигрываться в обратную сторону.

```sv
{#if visible}
	<div transition:fade>
		появляется и исчезает
	</div>
{/if}
```

> По умолчанию переход появления не проигрывается при первой отрисовке компонента. Вы можете изменить это поведение установив параметр `intro: true` при [создании компонента](docs#API_komponenta_na_kliente).

##### Параметры перехода

---

Как и действия, переходы могут иметь параметры.

(Двойные фигурные скобки `{{...}}` не являются особым синтаксисом; это просто литерал объекта внутри тега выражения)

```sv
{#if visible}
	<div transition:fade="{{ duration: 2000 }}">
		Влетает, исчезает. По 2 секунды на каждый переход
	</div>
{/if}
```

##### Пользовательские переходы

---

Переходы могут использовать пользовательские функции. Если возвращённый объект имеет функцию `css`, Svelte создаст CSS-анимацию, которая воспроизводится на элементе.

Аргумент `t`, передаваемый в функцию `css`, представляет собой значение между `0` и `1` после применения функции плавности `easing`. Переходы *появления* выполняются от `0` до` 1`, переходы *исчезновения* выполняются от `1` до `0` — другими словами, `1` - это естественное состояние элемента, как если бы переход не применялся. Аргумент `u` равен `1 - t`.

Функция вызывается множество раз с разными аргументами `t` и `u` *до начала* перехода.

```sv
<script>
	import { elasticOut } from 'svelte/easing';

	export let visible;

	function whoosh(node, params) {
		const existingTransform = getComputedStyle(node).transform.replace('none', '');

		return {
			delay: params.delay || 0,
			duration: params.duration || 400,
			easing: params.easing || elasticOut,
			css: (t, u) => `transform: ${existingTransform} scale(${t})`
		};
	}
</script>

{#if visible}
	<div in:whoosh>
		whooshes
	</div>
{/if}
```

---

Пользовательская функция перехода также может возвращать функцию `tick`, которая вызывается *во время* перехода с теми же аргументами` t` и `u`.

> Если возможно использовать `css` вместо` tick`, используйте — CSS-анимация может запускаться вне основного потока, предотвращая лаги на медленных устройствах.

```sv
<script>
	export let visible = false;

	function typewriter(node, { speed = 50 }) {
		const valid = (
			node.childNodes.length === 1 &&
			node.childNodes[0].nodeType === Node.TEXT_NODE
		);

		if (!valid) return {};

		const text = node.textContent;
		const duration = text.length * speed;

		return {
			duration,
			tick: (t, u) => {
				const i = ~~(text.length * t);
				node.textContent = text.slice(0, i);
			}
		};
	}
</script>

{#if visible}
	<p in:typewriter="{{ speed: 20 }}">
		Съешь ещё этих мягких французских булок, да выпей же чаю
	</p>
{/if}
```

Если переход возвращает функцию вместо объекта перехода, то она будет вызвана в следующей микрозадаче. Это позволяет координировать несколько переходов, что дает возможность запускать [перекрёстные переходы](tutorial/deferred-transitions).

##### События переходов

---

Элемент, который имеет переходы, в дополнение к стандартным событиям DOM может запускать ещё и такие события:

* `introstart`
* `introend`
* `outrostart`
* `outroend`

```sv
{#if visible}
	<p
		transition:fly="{{ y: 200, duration: 2000 }}"
		on:introstart="{() => status = 'начало появления'}"
		on:outrostart="{() => status = 'начало исчезновения'}"
		on:introend="{() => status = 'конец появления'}"
		on:outroend="{() => status = 'конец исчезновения'}"
	>
		Прилетает и улетает
	</p>
{/if}
```

---

Локальные переходы воспроизводятся только когда создается или убирается конкретный блок, к которому они прикреплены, а *не* его родительские блоки.

```sv
{#if x}
	{#if y}
		<p transition:fade>
			проигрывается когда изменяются 'x' или 'y'
		</p>

		<p transition:fade|local>
			проигрывается только когда изменяется 'y'
		</p>
	{/if}
{/if}
```


#### in:*fn*/out:*fn*

```sv
in:fn
```
```sv
in:fn={параметры}
```
```sv
in:fn|local
```
```sv
in:fn|local={параметры}
```

```sv
out:fn
```
```sv
out:fn={параметры}
```
```sv
out:fn|local
```
```sv
out:fn|local={параметры}
```

---

Аналогичны `transition:`, но применяются только когда элемент появляется (`in:`) или убирается (`out:`) из DOM.

В отличие от `transition:`, переходы `in:` и `out:` не являются *обратимыми*. При исчезновении элемента, если переход появления ещё не закончился, он будет проигрываться дальше, но уже вместе с переходом исчезновения. Если исчезновение элемента, было прервано, то переходы будут запущены заново.

```sv
{#if visible}
	<div in:fly out:fade>
		влетает, исчезает
	</div>
{/if}
```



#### animate:*fn*

```sv
animate:имя
```

```sv
animate:имя={параметры}
```

```js
animation = (node: HTMLElement, { from: DOMRect, to: DOMRect } , params: any) => {
	delay?: number,
	duration?: number,
	easing?: (t: number) => number,
	css?: (t: number, u: number) => string,
	tick?: (t: number, u: number) => void
}
```

```js
DOMRect {
	bottom: number,
	height: number,
	​​left: number,
	right: number,
	​top: number,
	width: number,
	x: number,
	y: number
}
```

---

Анимация запускается, когда изменяется порядок содержимого [блока each с ключом](docs#each). Анимации не запускаются при удалении элемента, а только лишь при переупорядочивании данных внутри блока each. Директиву animate можно устанавливать только для элемента, который является *непосредственным* дочерним элементом блока each с ключом.

Анимация может использовать [встроенные функции анимации](docs#svelte_animate) или [пользовательские функции анимации](docs#Polzovatelskie_funkczii_animaczii).

```sv
<!-- при изменении порядка элементов в списке запустится анимация-->
{#each list as item, index (item)}
	<li animate:flip>{item}</li>
{/each}
```

##### Параметры анимации

---

Как действия и переходы, анимации также могут иметь параметры.

(Двойные фигурные скобки `{{...}}` здесь не являются особым синтаксисом - это просто литерал объекта внутри тега выражения)

```sv
{#each list as item, index (item)}
	<li animate:flip="{{ delay: 500 }}">{item}</li>
{/each}
```

##### Пользовательские функции анимации

---

Анимации могут использовать пользовательские функции, которые принимают в качестве аргументов ссылку на элемент `node`, объект `animation` и объект параметров. Объект `animation` содержит свойства` from` и `to`, каждое из которых является объектом [DOMRect](https://developer.mozilla.org/ru/docs/Web/API/DOMRect#Properties), описывающим геометрию элемента в начальном и конечном положениях. Свойство `from` - это DOMRect элемента в его начальной позиции, свойство `to` - это DOMRect элемента в его конечной позиции после переупорядочивания списка и обновления DOM.

Если в возвращаемом из функции объекте есть метод `css`, Svelte создаст CSS-анимацию, которая будет применена к элементу `node`.

Аргумент `t`, передаваемый в метод `css`, представляет собой значение от `0` до `1`, которое возвращает функция плавности `easing` для нужного момента времени. Аргумент `u` равен `1 - t`.

Функция вызывается множество раз *до начала* анимации, с различными аргументами `t` и `u`.


```sv
<script>
	import { cubicOut } from 'svelte/easing';

	function whizz(node, { from, to }, params) {

		const dx = from.left - to.left;
		const dy = from.top - to.top;

		const d = Math.sqrt(dx * dx + dy * dy);

		return {
			delay: 0,
			duration: Math.sqrt(d) * 120,
			easing: cubicOut,
			css: (t, u) =>
				`transform: translate(${u * dx}px, ${u * dy}px) rotate(${t*360}deg);`
		};
	}
</script>

{#each list as item, index (item)}
	<div animate:whizz>{item}</div>
{/each}
```

---

Пользовательская функция анимации также может возвращать функцию `tick`, которая вызывается *во время* анимации с теми же аргументами `t` и `u`.

> Всегда старайтесь использовать`css` вместо` tick`, поскольку CSS-анимация запускается вне основного потока, предотвращая подёргивания на медленных устройствах.

```sv
<script>
	import { cubicOut } from 'svelte/easing';

	function whizz(node, { from, to }, params) {

		const dx = from.left - to.left;
		const dy = from.top - to.top;

		const d = Math.sqrt(dx * dx + dy * dy);

		return {
		delay: 0,
		duration: Math.sqrt(d) * 120,
		easing: cubicOut,
		tick: (t, u) =>
			Object.assign(node.style, {
				color: t > 0.5 ? 'Pink' : 'Blue'
			});
	};
	}
</script>

{#each list as item, index (item)}
	<div animate:whizz>{item}</div>
{/each}
```

### Директивы компонентов

#### [on:*событие*](on_component_event)

```sv
on:событие={обработчик}
```

---

Компоненты могут отправлять события, используя диспетчер событий [createEventDispatcher](docs#createEventDispatcher) или пробрасывая события DOM. Обработка событий компонента выглядит так же, как обработка событий DOM.

```sv
<SomeComponent on:whatever={handler}/>
```

---

Если директива `on:` используется без значения, то компонент будет *пробрасывать* событие выше, как и в аналогичном случае с событиями DOM. Событие станет доступно для прослушивания в родительском компоненте.

```sv
<SomeComponent on:whatever/>
```


#### [bind:*свойство*](bind_component_property)

```sv
bind:свойство={переменная}
```

---

К свойствам компонента можно привязаться, точно так же как к атрибутам элементов.

```sv
<Keypad bind:value={pin}/>
```

#### [bind:this](bind_component)

```sv
bind:this={экземпляр_компонента}
```

---

Компоненты также поддерживают привязку `bind:this`, позволяющую взаимодействовать с экземпляром компонента в коде.

> Обратите внимание, что использование `{cart.empty}` вызовет ошибку, поскольку при первой отрисовке кнопки `cart` ещё имеет значение `undefined`.

```sv
<ShoppingCart bind:this={cart}/>

<button on:click={() => cart.empty()}>
	Очистить корзину
</button>
```



### `<slot>`

```sv
<slot><!-- содержимое по умолчанию --></slot>
```
```sv
<slot name="x"><!-- содержимое по умолчанию --></slot>
```
```sv
<slot свойство={значениие}></slot>
```

---

Как и любой HTML-элемент, компоненты могут иметь вложенные элементы.

Вложенное содержимое размещается в компоненте при помощи элемента `<slot>`, который также может содержать содержимое по умолчанию, которое отображается, если в компонент не было предано никакого содержимого.

```sv
<!-- Widget.svelte -->
<div>
	<slot>
		это содержимое по умолчанию, которое отобразится, если не передали иного содержимого.
	</slot>
</div>

<!-- App.svelte -->
<Widget></Widget> <!-- этот компонент покажет содержимое по умолчанию -->

<Widget>
	<p>вложенный элемент, который заменит собой содержимое по умолчанию.</p>
</Widget>

```

#### [`<slot name="`*имя*`">`](slot_name)

---

Именованные слоты позволяют указать конкретные области. Они тоже могут иметь запасное содержимое по умолчанию.

```sv
<!-- Widget.svelte -->
<div>
	<slot name="header">Заголовок не задан</slot>
	<p>Любое содержимое между заголовком и футером</p>
	<slot name="footer"></slot>
</div>

<!-- App.svelte -->
<Widget>
	<h1 slot="header">Привет</h1>
	<p slot="footer">Все права (c) 2019 Svelte Industries</p>
</Widget>
```

Компоненты могут быть размещены в именованном слоте с использованием синтаксиса `<Component slot="name" />`.
Чтобы разместить контент в слоте без использования элемента-оболочки, вы можете использовать специальный элемент `<svelte:fragment>`.

 ```sv
 <!-- Widget.svelte -->
 <div>
 	<slot name="header">Заголовок не задан</slot>
 	<p>Любое содержимое между заголовком и футером</p>
 	<slot name="footer"></slot>
 </div>

 <!-- App.svelte -->
 <Widget>
 	<HeaderComponent slot="header" />
 	<svelte:fragment slot="footer">
 		<p>Все права защищены.</p>
 		<p>Копирайт (c) 2019 Svelte Industries</p>
 	</svelte:fragment>
 </Widget>
 ```

#### [`$$slots`](slots_object)

---

`$$slots` - это объект, ключи которого являются именами слотов, переданных в компонент родительским компонентом. Если родительский компонент не передавал содержимое для слота, то имя этого слота не будет присутствовать в `$$slots`. Эту особенность можно использовать для отображения как самого слота, так и части шаблона в зависимости от того, передавал ли родительский компонент содержимое для этого слота.

Note that explicitly passing in an empty named slot will add that slot's name to `$$slots`. For example, if a parent passes `<div slot="title" />` to a child component, `$$slots.title` will be truthy within the child.

Обратите внимание, что передача пустого элемента в слот все равно добавляет значение в `$$slots`. Например, если родительский компонент передает `<div slot ="title"/>` в дочерний, то `$$slots.title` будет равно `true`.

```sv
<!-- Card.svelte -->
<div>
	<slot name="title"></slot>
	{#if $$slots.description}
		<!-- Элемент <hr> и слот будут отрисованы, только 
		     если будет получен слот с именем "description". -->
		<hr>
		<slot name="description"></slot>
	{/if}
</div>

<!-- App.svelte -->
<Card>
	<h1 slot="title">Заголовок статьи</h1>
	<!-- Слота с именем "description" нет, поэтому ничего не будет отображено. -->
</Card>
```


#### [`<slot let:`*имя*`={`*значение*`}>`](slot_let)

---


Слоты могут быть отрисованы ноль или более раз и могут передавать значения *обратно* родителю через свои свойства. Родитель может получить эти значения от слота при помощи директивы `let:`.

Обычные правила сокращения работают и тут — `let:item` то же самое, что и `let:item={item}`, а `<slot {item}>` эквивалентно `<slot item={item}>`.

```sv
<!-- FancyList.svelte -->
<ul>
	{#each items as item}
		<li class="fancy">
			<slot prop={item}></slot>
		</li>
	{/each}
</ul>

<!-- App.svelte -->
<FancyList {items} let:prop={thing}>
	<div>{thing.text}</div>
</FancyList>
```

---

Именованные слоты также могут предоставлять значения. Директива `let:` указывается на элементе с атрибутом `slot`.

```sv
<!-- FancyList.svelte -->
<ul>
	{#each items as item}
		<li class="fancy">
			<slot name="item" item={item}></slot>
		</li>
	{/each}
</ul>

<slot name="footer"></slot>

<!-- App.svelte -->
<FancyList {items}>
	<div slot="item" let:item={item}>{item.text}</div>
	<p slot="footer">Все права (c) 2019 Svelte Industries</p>
</FancyList>
```


### `<svelte:self>`

---

Элемент `<svelte: self>` позволяет компоненту включать самого себя себя рекурсивно.

Он не может отображаться на верхнем уровне разметки, а должен быть помещён внутри блока `if` или `each`, а также может быть передан в слот компонента, чтобы избежать бесконечного цикла.

```sv
<script>
	export let count;
</script>

{#if count > 0}
	<p>Отсчёт... {count}</p>
	<svelte:self count="{count - 1}"/>
{:else}
	<p>Поехали!</p>
{/if}
```

### `<svelte:component>`

```sv
<svelte:component this={выражение}/>
```

---

Элемент `<svelte:component>` отрисовывает компонент динамически, используя конструктор компонента, переданный в свойстве`this`. Когда значение свойства изменяется, компонент уничтожается и создается заново.

Если выражение в свойстве `this` ложное, компонент не отрисовывается.

```sv
<svelte:component this={currentSelection.component} foo={bar}/>
```


### `<svelte:window>`

```sv
<svelte:window on:событие={обработчик}/>
```
```sv
<svelte:window bind:свойство={значение}/>
```

---

Элемент `<svelte:window>` позволяет добавлять обработчики событий к объекту `window`, не беспокоясь об их удалении при уничтожении компонента или проверять существование `window` при рендеринге на стороне сервера.

```sv
<script>
	function handleKeydown(event) {
		alert(`нажата клавиша ${event.key}`);
	}
</script>

<svelte:window on:keydown={handleKeydown}/>
```

---

Также можно сделать привязку к следующим свойствам:

* `innerWidth`
* `innerHeight`
* `outerWidth`
* `outerHeight`
* `scrollX`
* `scrollY`
* `online` — сокращение для window.navigator.onLine

Все свойства, кроме `scrollX` и `scrollY` доступны только для чтения.

```sv
<svelte:window bind:scrollY={y}/>
```


### `<svelte:body>`

```sv
<svelte:body on:событие={обработчик}/>
```

---

Как и в случае с `<svelte:window>`, этот элемент позволяет добавлять обработчики событий к `document.body`, например к `mouseenter` и` mouseleave`, которые не запускаются в `window`.

```sv
<svelte:body
	on:mouseenter={handleMouseenter}
	on:mouseleave={handleMouseleave}
/>
```


### `<svelte:head>`

```sv
<svelte:head>...</svelte:head>
```

---

Этот элемент позволяет вставлять элементы в `document.head`. При рендеринге на стороне сервера содержимое `head` предоставляется отдельно от основного содержимого `html`.

```sv
<svelte:head>
	<link rel="stylesheet" href="tutorial/dark-theme.css">
</svelte:head>
```


### `<svelte:options>`

```sv
<svelte:options параметр={значение}/>
```
---

Элемент `<svelte:options>` позволяет установить определенные параметры для компилятора для каждого отдельного компонента. Подробнее о параметрах компилятора можно узнать в разделе про функцию [compile](docs#svelte_compile). Параметры, которые можно установить:

* `immutable={true}` — установите, если вы нигде не используете изменяемые данные — тогда компилятор сможет выполнять более простые проверки равенства объектов для определения их изменения
* `immutable={false}` — по умолчанию. Svelte будет проверять изменение объектов обычным способом
* `accessors={true}` — добавляет сеттеры и геттеры для свойств компонента
* `accessors={false}` — по умолчанию, аксессоры не добавляются
* `namespace="..."` — пространство имен, где компонент будет использован (обычно требуется "svg"); используйте "foreign" чтобы имена аттрибутов стали чувствительны к регистру и не отображались ошибки специфичные для HTML
* `tag="..."` — имя, которое используется при компиляции компонента в пользовательский элемент


```sv
<svelte:options tag="my-custom-element"/>
```


### `<svelte:fragment>`

Элемент `<svelte:fragment>` позволяет размещать контент в [именованных слотах](docs#slot_name) не оборачивая его в дополнительный элемент DOM. Это сохраняет структуру макета вашего документа.

 ```sv
 <!-- Widget.svelte -->
 <div>
 	<slot name="header">Заголовок не предоставлен</slot>
 	<p>Любое содержимое между заголовком и футером</p>
 	<slot name="footer"></slot>
 </div>

 <!-- App.svelte -->
 <Widget>
 	<h1 slot="header">Привет</h1>
 	<svelte:fragment slot="footer">
 		<p>Все права защищены.</p>
 		<p>Копирайт (c) 2019 Svelte Industries</p>
 	</svelte:fragment>
 </Widget>
 ```