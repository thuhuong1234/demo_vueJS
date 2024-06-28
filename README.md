## What Vue.js

- JavaScript framework for building user interfaces
- Develop user interfaces of any complexity
- Two core features of Vue:
  - Declarative Rendering
  - Reactivity: tracks JavaScript state changes, update DOM

## API Styles

- Options API

```html
<script>
  export default {
    // Properties returned from data() become reactive state
    // and will be exposed on `this`.
    data() {
      return {
        count: 0,
      };
    },

    // Methods are functions that mutate state and trigger updates.
    // They can be bound as event handlers in templates.
    methods: {
      increment() {
        this.count++;
      },
    },

    // Lifecycle hooks are called at different stages
    // of a component's lifecycle.
    // This function will be called when the component is mounted.
    mounted() {
      console.log(`The initial count is ${this.count}.`);
    },
  };
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

- Composition API (\*)
  - imported API functions

```html
<script setup>
  import { ref, onMounted } from "vue";

  // reactive state
  const count = ref(0);

  // functions that mutate state and trigger updates
  function increment() {
    count.value++;
  }

  // lifecycle hooks
  onMounted(() => {
    console.log(`The initial count is ${count.value}.`);
  });
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

- Go with Options API if you are not using build tools,

- Go with Composition API + Single-File Components if you plan to build full applications with Vue.

## Creating a Vue Application

- SPA powered by Vite

```bash
$ npm create vue@latest
```

- Use from A CDN

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp, ref } = Vue;

  createApp({
    setup() {
      const message = ref("Hello vue!");
      return {
        message,
      };
    },
  }).mount("#app");
</script>
```

## The application instance

- new application instance

```js
import { createApp } from "vue";
const app = createApp({
  /* root component options */
});
```

- The Root Component:

```js
import { createApp } from "vue";
// import the root component App from a single-file component.
import App from "./App.vue";

const app = createApp(App);
```

-​ Mounting the App:

```html
<div id="app"></div>
```

```js
app.mount("#app");
```

- App Configurations: allows us to configure a few app-level options

```js
app.config.errorHandler = (err) => {
  /* handle error */
};
/* registering a component */
app.component("TodoDeleteButton", TodoDeleteButton);
```

- Multiple application instances:

```js
const app1 = createApp({
  /* ... */
});
app1.mount("#container-1");

const app2 = createApp({
  /* ... */
});
app2.mount("#container-2");
```

## Template Syntax

- HTML-based template syntax

```html
<template>
  <h1 class="title">Hello {{ name }}</h1>
</template>

<script setup>
  const name = "Duong";
</script>
```

### Attribute Binding

```html
<template>
  <h1 :class="className">Hello {{ name }}</h1>
</template>

<script setup>
  const name = "Duong";
  const className = "title";
</script>
```

```html
<!-- same as :id="id" -->
<div :id></div>

<!-- this also works -->
<div v-bind:id></div>
```

- Boolean Attributes:

```html
<button :disabled="isButtonDisabled">Button</button>
```

- Dynamically Binding Multiple Attributes:

```html
const objectOfAttrs = { id: 'container', class: 'wrapper', style:
'background-color:green' }
```

```html
<div v-bind="objectOfAttrs"></div>
```

- Using JavaScript Expressions:

```js
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

- Calling Functions:

```html
<time :title="toTitleDate(date)" :datetime="date">
  {{ formatDate(date) }}
</time>
```

### Directives

Directives are special attributes with the `v-` prefix

```html
<p v-if="seen">Now you see me</p>
```

- Arguments:

```html
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>
```

- Dynamic Arguments:

```html
<!--
Note that there are some constraints to the argument expression,
as explained in the "Dynamic Argument Value Constraints" and "Dynamic Argument Syntax Constraints" sections below.
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- shorthand -->
<a :[attributeName]="url"> ... </a>
```

- Modifiers:

```html
<form @submit.prevent="onSubmit">...</form>
```

## Reactivity fundamentals

- `ref()` takes the argument and returns it wrapped within a ref object with a `.value` property

```js
import { ref } from "vue";

export default {
  setup() {
    const count = ref(0);

    function increment() {
      // .value is needed in JavaScript
      count.value++;
    }

    // don't forget to expose the function as well.
    return {
      count,
      increment,
    };
  },
};
```

```html
<button @click="increment">{{ count }}</button>
```

- `reactive()` API ,wraps the inner value in a special object

```js
import { reactive } from "vue";

const state = reactive({ count: 0 });
```

```html
<button @click="state.count++">{{ state.count }}</button>
```

## Single file component

```html
<template></template>

<script></script>

<style></style>
```

## Computed Property

```html
<script setup>
  import { reactive, computed } from "vue";

  const author = reactive({
    name: "John Doe",
    books: [
      "Vue 2 - Advanced Guide",
      "Vue 3 - Basic Guide",
      "Vue 4 - The Mystery",
    ],
  });

  // a computed ref
  const publishedBooksMessage = computed(() => {
    return author.books.length > 0 ? "Yes" : "No";
  });
</script>

<template>
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

- Computed Caching vs. Methods

```html
<p>{{ calculateBooksMessage() }}</p>
```

```html
// in component function calculateBooksMessage() { return author.books.length >
0 ? 'Yes' : 'No' }
```

- Writable Computed:

```js
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // Note: we are using destructuring assignment syntax here.
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

## Class and Style Bindings

- use `v-bind` to asign `class` and `style`

### Binding to Objects:

```js
const isActive = ref(true);
const error = ref(null);

const classObject = computed(() => ({
  active: isActive.value && !error.value,
  "text-danger": error.value && error.value.type === "fatal",
}));
```

```html
<div :class="classObject"></div>
```

- Binding to Arrays: bind `:class` to an array to apply a list of classes

```js
const activeClass = ref("active");
const errorClass = ref("text-danger");
```

```html
<div :class="[activeClass, errorClass]"></div>
```

### Binding Inline Styles:

```js
const activeColor = ref("red");
const fontSize = ref(30);
```

```html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

- Binding to Arrays:

```html
<div :style="[baseStyles, overridingStyles]"></div>
```

## Conditional Rendering

- `v-if`:

```html
<h1 v-if="awesome">Vue is awesome!</h1>
```

- `v-else`:

```html
<button @click="awesome = !awesome">Toggle</button>

<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

- `v-else-if`:

```html
<div v-if="type === 'A'">A</div>
<div v-else-if="type === 'B'">B</div>
<div v-else-if="type === 'C'">C</div>
<div v-else>Not A/B/C</div>
```

- `v-if` on <template>:

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

- `v-show`:

```html
<h1 v-show="ok">Hello!</h1>
```

## List Rendering

- `v-for`:render a list of items based on an array

```js
const items = ref([{ message: "Foo" }, { message: "Bar" }]);
```

```html
<li v-for="item in items">{{ item.message }}</li>
```

- nested functions:

```html
<li v-for="item in items">
  <span v-for="childItem in item.children">
    {{ item.message }} {{ childItem }}
  </span>
</li>
```

- `v-for` with an Object:

```js
const myObject = reactive({
  title: "How to do lists in Vue",
  author: "Jane Doe",
  publishedAt: "2016-04-10",
});
```

```html
<ul>
  <li v-for="value in myObject">{{ value }}</li>
</ul>
```

- `v-for` with a Range:

```html
<span v-for="n in 10">{{ n }}</span>
```

- `v-for` with `v-if`:

```html
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">{{ todo.name }}</li>
</template>
```

- Maintaining State with `key`:

```html
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```

- Array Change Detection:
  - `push()`
  - `pop()`
  - `shift()`
  - `splice()`
  - `sort()`
  - `reverse()`
  - `filter()`

## Event Handling

- Listening to Events:can use the `v-on` directive or `@`symbol

```js
const count = ref(0);
```

```html
<button @click="count++">Add 1</button>
<p>Count is: {{ count }}</p>
```

- Method Handlers:

```js
const name = ref("Vue.js");

function greet(event) {
  alert(`Hello ${name.value}!`);
  // `event` is the native DOM event
  if (event) {
    alert(event.target.tagName);
  }
}
```

```html
<!-- `greet` is the name of the method defined above -->
<button @click="greet">Greet</button>
```

- Accessing Event Argument in Inline Handlers:

```html
<!-- using $event special variable -->
<button @click="warn('Form cannot be submitted yet.', $event)">Submit</button>

<!-- using inline arrow function -->
<button @click="(event) => warn('Form cannot be submitted yet.', event)">
  Submit
</button>
```

```js
function warn(message, event) {
  // now we have access to the native event
  if (event) {
    event.preventDefault();
  }
  alert(message);
}
```

- Event Modifiers:`event.preventDefault()` or `event.stopPropagation()` inside event handlers

```html
<!-- the click event's propagation will be stopped -->
<a @click.stop="doThis"></a>

<!-- the submit event will no longer reload the page -->
<form @submit.prevent="onSubmit"></form>

<!-- modifiers can be chained -->
<a @click.stop.prevent="doThat"></a>

<!-- just the modifier -->
<form @submit.prevent></form>

<!-- only trigger handler if event.target is the element itself -->
<!-- i.e. not from a child element -->
<div @click.self="doThat">...</div>
```

- Key Modifiers:

```html
<!-- only call `submit` when the `key` is `Enter` -->
<input @keyup.enter="submit" />
```

```html
<input @keyup.page-down="onPageDown" />
```

## Lifecycle hooks

- update the DOM when data changes, runs functions
- lifecycle hooks : `beforeCreate`, `created`, `beforeMount`, `mounted`, `beforeUpdate`, `updated`, `beforeDestroy`, `destroyed`,...
- Registering Lifecycle Hooks:

```js
<script setup>
  import {onMounted} from 'vue' onMounted(() =>{" "}
  {console.log(`the component is now mounted.`)})
</script>
```

## Watchers

- perform "side effects" in reaction to state changes

```js
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Questions usually contain a question mark. ;-)')
const loading = ref(false)

// watch works directly on a ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.includes('?')) {
    loading.value = true
    answer.value = 'Thinking...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Error! Could not reach the API. ' + error
    } finally {
      loading.value = false
    }
  }
})
</script>

<template>
  <p>
    Ask a yes/no question:
    <input v-model="question" :disabled="loading" />
  </p>
  <p>{{ answer }}</p>
</template>
```

- Watch Source Types:

```js
const x = ref(0);
const y = ref(0);

// single ref
watch(x, (newX) => {
  console.log(`x is ${newX}`);
});

// getter
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`sum of x + y is: ${sum}`);
  }
);

// array of multiple sources
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x is ${newX} and y is ${newY}`);
});
```

- Deep Watchers:triggered on all nested mutations

```js
const obj = reactive({ count: 0 });

watch(obj, (newValue, oldValue) => {
  // fires on nested property mutations
  // Note: `newValue` will be equal to `oldValue` here
  // because they both point to the same object!
});

obj.count++;
```

- Eager Watchers:the callback won't be called until the watched source has changed.

```js
watch(
  source,
  (newValue, oldValue) => {
    // executed immediately, then again when `source` changes
  },
  { immediate: true }
);
```

- Once Watchers:trigger only once

```js
watch(
  source,
  (newValue, oldValue) => {
    // when `source` changes, triggers only once
  },
  { once: true }
);
```

- `watchEffect()`:track the callback's reactive dependencies automatically

```js
const todoId = ref(1);
const data = ref(null);

watch(
  todoId,
  async () => {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
    );
    data.value = await response.json();
  },
  { immediate: true }
);
```

- `watch()` and `watchEffect()`:
  - `watch` only tracks the explicitly watched source. It won't track anything accessed inside the callback.
  - `watchEffect`: on the other hand,
- Stopping a Watcher:stopped when the owner component is unmounted

```js
<script setup>
  import {watchEffect} from 'vue' // this one will be automatically stopped
  watchEffect(() => {}) // ...this one will not! setTimeout(() =>{" "}
  {watchEffect(() => {})}, 100)
</script>
```

## Template Refs:

- access to the underlying DOM elements

```html
<script setup>
  import { ref, onMounted } from "vue";

  // declare a ref to hold the element reference
  // the name must match template ref value
  const input = ref(null);

  onMounted(() => {
    input.value.focus();
  });
</script>

<template>
  <input ref="input" />
</template>
```

- Refs inside `v-for`

```html
<script setup>
  import { ref, onMounted } from "vue";

  const list = ref([
    /* ... */
  ]);

  const itemRefs = ref([]);

  onMounted(() => console.log(itemRefs.value));
</script>

<template>
  <ul>
    <li v-for="item in list" ref="itemRefs">{{ item }}</li>
  </ul>
</template>
```

- Function Refs:

```html
<input :ref="(el) => { /* assign el to a property or ref */ }" />
```

- Ref on Component:`ref` can also be used on a child component

```html
<script setup>
  import { ref, onMounted } from "vue";
  import Child from "./Child.vue";

  const child = ref(null);

  onMounted(() => {
    // child.value will hold an instance of <Child />
  });
</script>

<template>
  <Child ref="child" />
</template>
```

## Components Basics:

- Components allow us to split the UI into independent and reusable pieces,

```html
<script setup>
  import ButtonCounter from "./ButtonCounter.vue";
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```
