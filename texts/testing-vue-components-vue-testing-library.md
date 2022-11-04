# Testing Vue Components with Vue Testing Library

see [Vue Testing Library](https://testing-library.com/docs/vue-testing-library/intro/)

repo [here](https://github.com/testing-library/vue-testing-library)

Vue Testing Library builds on top of DOM Testing Library by adding APIs for working with Vue components. It is built on top of @vue/test-utils, the official testing library for Vue.

If using Vue2

`npm install --save-dev @testing-library/vue@5`

or 

`yarn add --dev @testing-library/vue@5`

If using Vue3

`npm install --save-dev @testing-library/vue`

or 

`yarn add --dev @testing-library/vue`

**All examples taken from [vue testing library repo](https://github.com/testing-library/vue-testing-library)**

## Basic Example

Button.vue: 

```vue
<template>
  <p>Times clicked: {{ count }}</p>
  <button @click="increment">increment</button>
</template>

<script>
  export default {
    name: 'Button',
    data: () => ({
      count: 0,
    }),
    methods: {
      increment() {
        this.count++
      },
    },
  }
</script>
```

test

```js
import {render, screen, fireEvent} from '@testing-library/vue'
import Button from './Button'

test('increments value on click', async () => {
  // The `render` method renders the component into the document.
  // It also binds to `screen` all the available queries to interact with
  // the component.
  render(Button)

  // queryByText returns the first matching node for the provided text
  // or returns null.
  expect(screen.queryByText('Times clicked: 0')).toBeTruthy()

  // getByText returns the first matching node for the provided text
  // or throws an error.
  const button = screen.getByText('increment')

  // Click a couple of times.
  await fireEvent.click(button)
  await fireEvent.click(button)

  expect(screen.queryByText('Times clicked: 2')).toBeTruthy()
})
```

## Testing Vuex

Asuming we've this  vuex store: in store.js

```js
export const store = {
  state() {
    return {
      count: 0,
    }
  },
  actions: {
    increment: ({commit, state}) => commit('SET_COUNT', state.count + 1),
    decrement: ({commit, state}) => commit('SET_COUNT', state.count - 1),
  },
  mutations: {
    SET_COUNT: (state, count) => {
      state.count = count
    },
  },
}
```

and a VuexTest component in VuexTest.vue in the same folder :

```vue
<template>
  <h2>Counter</h2>
  <button data-testid="decrementer" @click="decrement">-</button>
  <span data-testid="count-value">{{ count }}</span>
  <button data-testid="incrementer" @click="increment">+</button>
</template>

<script>
import {mapActions, mapState} from 'vuex'
export default {
  computed: {
    ...mapState(['count']),
  },
  methods: {
    ...mapActions(['decrement', 'increment']),
  },
}
</script>
```

our test could look like: 

```js
import '@testing-library/jest-dom'
import {createStore} from 'vuex'
import {render, fireEvent} from '@testing-library/vue'
import VuexTest from './VuexTest'
import {store} from './store'

test('basic test with Vuex store', async () => {
  const storeInstance = createStore(store)

  const {getByTestId, getByText} = render(VuexTest, {
    global: {
      plugins: [storeInstance],
    },
  })

  expect(getByTestId('count-value')).toHaveTextContent('0')

  await fireEvent.click(getByText('+'))
  expect(getByTestId('count-value')).toHaveTextContent('1')

  await fireEvent.click(getByText('+'))
  expect(getByTestId('count-value')).toHaveTextContent('2')

  await fireEvent.click(getByText('-'))
  expect(getByTestId('count-value')).toHaveTextContent('1')
})
```

A common testing pattern is to create a custom renderer for a specific test file. This way, common operations such as registering a Vuex store can be abstracted out while avoiding sharing mutable state.

```js
function renderVuexTestComponent(customStore) {
  // Create a custom store with the original one and the one coming as a
  // parameter. This way we can alter some of its values.
  const mergedStoreInstance = createStore({...store, ...customStore})

  return render(VuexTest, {
    global: {
      plugins: [mergedStoreInstance],
    },
  })
}

test('can render with vuex with defaults', async () => {
  const {getByTestId, getByText} = renderVuexTestComponent()

  await fireEvent.click(getByText('+'))

  expect(getByTestId('count-value')).toHaveTextContent('1')
})

test('can render with vuex with custom initial state', async () => {
  const {getByTestId, getByText} = renderVuexTestComponent({
    state: () => ({count: 3}),
  })

  await fireEvent.click(getByText('-'))

  expect(getByTestId('count-value')).toHaveTextContent('2')
})

test('can render with vuex with custom store', async () => {
  // This is a silly store that can never be changed.
  // eslint-disable-next-line no-shadow
  const store = createStore({
    state: () => ({count: 1000}),
    actions: {
      increment: () => jest.fn(),
      decrement: () => jest.fn(),
    },
  })

  // Notice how here we are not using the helper rendering method, because
  // there's no need to do that here. We're passing a whole store.
  const {getByTestId, getByText} = render(VuexTest, {
    global: {
      plugins: [store],
    },
  })

  await fireEvent.click(getByText('+'))
  expect(getByTestId('count-value')).toHaveTextContent('1000')

  await fireEvent.click(getByText('-'))
  expect(getByTestId('count-value')).toHaveTextContent('1000')
})
```


## Testing Vue Router

Assuming in the same folder we have an App, Home and About components :

```js
// App.vue :
<template>
  <router-link to="/" data-testid="home-link">Home</router-link>
  <router-link to="/about" data-testid="about-link">About</router-link>
  <router-view />
  <div data-testid="location-display">{{ $route.fullPath }}</div>
</template>

//Home.vue
<template>
  <div>You are home</div>
</template>

//About.vue
<template>
  <div>You are on the about page</div>
</template>
```

our Test :

```js
/* eslint-disable vue/require-prop-types */
/* eslint-disable vue/one-component-per-file */

import '@testing-library/jest-dom'
import {waitFor} from '@testing-library/dom'
import {defineComponent} from 'vue'
import {createRouter, createWebHistory} from 'vue-router'
import {render, fireEvent} from '..'
import App from './App.vue'
import Home from './Home.vue'
import About from './About.vue'

test('full app rendering/navigating from base URL', async () => {
  // Create a Router instance
  // https://next.router.vuejs.org/api/#createrouter
  // using a HTML5 history.
  // https://next.router.vuejs.org/api/#createwebhistory
  const router = createRouter({
    history: createWebHistory(),
    routes: [
      {path: '/', component: Home},
      {path: '/about', component: About},
    ],
  })

  const {findByText, getByText, getByTestId} = render(App, {
    global: {
      plugins: [router],
    },
  })

  // Vue Router navigation is async, so we need to wait until the
  // initial render happens
  expect(await findByText('You are home')).toBeInTheDocument()
  expect(getByTestId('location-display')).toHaveTextContent('/')

  await fireEvent.click(getByTestId('about-link'))

  // Same thing here: Vue Router navigation is async, so we need to wait until the
  // navigation happens
  await waitFor(() =>
    expect(getByTestId('location-display')).toHaveTextContent('/about'),
  )

  expect(getByText('You are on the about page')).toBeInTheDocument()
})

test('sets router initial state', async () => {
  const Component = defineComponent({
    props: ['to'],
    template: `<router-link :to="to">Learn More</router-link>`,
  })

  const route = {
    name: 'routeName',
    path: '/',
    component: defineComponent({template: `<div></div>`}),
  }

  const router = createRouter({
    history: createWebHistory(),
    routes: [route],
  })

  const to = {name: route.name}

  const {getByText} = render(Component, {
    props: {to},
    global: {
      plugins: [router],
    },
  })

  // We need to wait for router to complete the initial navigation.
  await router.isReady()

  const button = getByText('Learn More')
  expect(button).toHaveAttribute('href', route.path)
})
```


## testing vue-i18n

Assuming we've a Translations.vue component

```vue
<template>
  <h2>{{ $t('hello') }}</h2>
  <select v-model="$i18n.locale">
    <option value="en">English</option>
    <option value="ja">Japanese</option>
  </select>
</template>

<script>
export default {
  name: 'Translations',
}
</script>
```

our test : 

```js
import '@testing-library/jest-dom'
import {createI18n} from 'vue-i18n'
import {render, fireEvent} from '..'
import Translations from './Translations'

const i18n = createI18n({
  legacy: true,
  locale: 'en',
  messages: {
    en: {
      hello: 'Hello',
    },
    ja: {
      hello: 'こんにちは',
    },
  },
})

test('renders translations', async () => {
  const {queryByText, getByText} = render(Translations, {
    global: {
      plugins: [i18n],
    },
  })

  expect(getByText('Hello')).toBeInTheDocument()

  await fireEvent.update(getByText('Japanese'))

  expect(getByText('こんにちは')).toBeInTheDocument()

  expect(queryByText('Hello')).not.toBeInTheDocument()
})
```