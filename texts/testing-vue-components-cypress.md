# Vue Component Testing with Cypress

see also [Getting started](https://docs.cypress.io/guides/component-testing/vue/quickstart#Getting-Started) and [Framework Support](https://docs.cypress.io/guides/component-testing/vue/overview#Framework-Support)

Cypress Component Testing supports Vue 2+ with the following frameworks:

- Vue CLI
- Nuxt Alpha
- Vue with Vite
- Vue with Webpack

To get up and running with Cypress Component Testing in React, install Cypress into your project:

`npm i cypress -D`

Open Cypress:

`npx cypress open`

The Cypress Launchpad will guide you through configuring your project.


### Creating and testing a Component

Example component (in src/components/Stepper.vue):



```vue
<template>
  <div>
    <button data-cy="decrement" @click="decrement">-</button>
    <span data-cy="counter">{{ count }}</span>
    <button data-cy="increment" @click="increment">+</button>
  </div>
</template>

<script setup>
  import { ref } from 'vue'
  const props = defineProps(['initial'])

  const emit = defineEmits(['change'])

  const count = ref(props.initial || 0)

  const increment = () => {
    count.value++
    emit('change', count.value)
  }

  const decrement = () => {
    count.value--
    emit('change', count.value)
  }
</script>
```

mounting the component:

```js
import Stepper from './Stepper.vue'

describe('<Stepper />', () => {
  it('renders', () => {
    // see: https://test-utils.vuejs.org/guide/
    cy.mount(Stepper)
  })
})
```

#### Example test for the Stepper component

```js
import Stepper from './Stepper.vue'
describe('<Stepper>', () => {
  it('stepper should default to 0', () => {
    cy.mount(Stepper)
    cy.get('[data-cy=counter]').should('have.text', '0')
  })
  it('supports an "initial" prop to set the value', () => {
    cy.mount(Stepper, { props: { initial: 100 } })
    cy.get('[data-cy=counter]').should('have.text', '100')
  })
  it('when the increment button is pressed, the counter is incremented', () => {
    cy.mount(Stepper)
    cy.get('[data-cy=increment]').click()
    cy.get('[data-cy=counter]').should('have.text', '1')
  })

  it('when the decrement button is pressed, the counter is decremented', () => {
    cy.mount(Stepper)
    cy.get('[data-cy=decrement]').click()
    cy.get('[data-cy=counter]').should('have.text', '-1')
  })
  it('clicking + fires a change event with the incremented value', () => {
    const onChangeSpy = cy.spy().as('onChangeSpy')
    cy.mount(Stepper, { props: { onChange: onChangeSpy } })
    cy.get('[data-cy=increment]').click()
    cy.get('@onChangeSpy').should('have.been.calledWith', 1)
  })

})
```
See more examples at [Vue Examples](https://docs.cypress.io/guides/component-testing/vue/examples)

### Custom Mount Commands

While you can use the mount() function in your tests, cypress recommends using cy.mount(), which is a custom command that is defined in the cypress/support/component.js file:

```js
import { mount } from 'cypress/vue'

Cypress.Commands.add('mount', mount)
```

This allows you to use cy.mount() in any test without having to import the mount() function in each and every spec file.

By default, cy.mount() is a simple passthrough to mount(), however, you can customize cy.mount() to fit your needs. For instance, if you are using plugins or other global app-level setups in your Vue app, you can configure them here.

### Replicating Plugins

Most applications will have state management or routing. Both of these are Vue plugins:

in cypress/support/component.js

```js
import { createPinia } from 'pinia' // or Vuex
import { createI18n } from 'vue-i18n'
import { mount } from 'cypress/vue'
import { h } from 'vue'

// We recommend that you pull this out
// into a constants file that you share with
// your main.js file.
const i18nOptions = {
  locale: 'en',
  messages: {
    en: {
      hello: 'hello!',
    },
    ja: {
      hello: 'こんにちは！',
    },
  },
}

Cypress.Commands.add('mount', (component, ...args) => {
  args.global = args.global || {}
  args.global.plugins = args.global.plugins || []
  args.global.plugins.push(createPinia())
  args.global.plugins.push(createI18n())

  return mount(() => {
    return h(VApp, {}, component)
  }, ...args)
})
```

### Vue Router

To use Vue Router, create a command to register the plugin and pass in a custom implementation of the router via the options param:

Vue 3:

```js
import { mount } from 'cypress/vue'
import { createMemoryHistory, createRouter } from 'vue-router'
import { routes } from '../../src/router'

Cypress.Commands.add('mount', (component, options = {}) => {
  // Setup options object
  options.global = options.global || {}
  options.global.plugins = options.global.plugins || []

  // create router if one is not provided
  if (!options.router) {
    options.router = createRouter({
      routes: routes,
      history: createMemoryHistory(),
    })
  }

  // Add router plugin
  options.global.plugins.push({
    install(app) {
      app.use(options.router)
    },
  })

  return mount(component, options)
})
```
Vue 2:

```js
import { mount } from 'cypress/vue'
import Vue from 'vue'
import VueRouter from 'vue-router'
import { router } from '../../src/router'

Cypress.Commands.add('mount', (component, options = {}) => {
  // Add the VueRouter plugin
  Vue.use(VueRouter)

  // Use the router passed in via options,
  // or the default one if not provided
  options.router = options.router || router

  return mount(component, options)
})
```

Usage:

```js
import VueRouter from 'vue-router'
import Navigation from './Navigation.vue'
import { routes } from '../router'

it('home link should be active when url is "/"', () => {
  // No need to pass in custom router as default url is '/'
  cy.mount(Navigation)

  cy.get('a').contains('Home').should('have.class', 'router-link-active')
})

it('login link should be active when url is "/login"', () => {
  // Create a new router instance for each test
  const router = new VueRouter({
    mode: 'history',
    routes,
  })

  // Change location to `/login`
  router.push('/login')

  // Pass the already initialized router for use
  cy.mount(Navigation, { router })

  cy.get('a').contains('Login').should('have.class', 'router-link-active')
})
```

### Vuex

To use a component that uses Vuex, create a mount command that configures a Vuex store for your component:

Vue 3:

```js
import { mount } from 'cypress/vue'
import { getStore } from '../../src/plugins/store'

Cypress.Commands.add('mount', (component, options = {}) => {
  // Setup options object
  options.global = options.global || {}
  options.global.stubs = options.global.stubs || {}
  options.global.stubs['transition'] = false
  options.global.components = options.global.components || {}
  options.global.plugins = options.global.plugins || []

  // Use store passed in from options, or initialize a new one
  const { store = getStore(), ...mountOptions } = options

  // Add Vuex plugin
  options.global.plugins.push({
    install(app) {
      app.use(store)
    },
  })

  return mount(component, mountOptions)
})
```

Vue 2:

```js
import { mount } from 'cypress/vue'
import Vuex from 'vuex'
import { getStore } from '../../src/plugins/store'

Cypress.Commands.add('mount', (component, options = {}) => {
  // Setup options object
  options.extensions = options.extensions || {}
  options.extensions.plugins = options.extensions.plugins || []

  // Use store passed in from options, or initialize a new one
  options.store = options.store || getStore()

  // Add Vuex plugin
  options.extensions.plugins.push(Vuex)

  return mount(component, options)
})
```

Usage:

```js
import { getStore } from '@/plugins/store'
import UserProfile from './UserProfile.vue'

it.only('User profile should display user name', () => {
  const user = { name: 'test person' }

  // getStore is a factory method that creates a new store
  const store = getStore()

  // mutate the store with user
  store.commit('setUser', user)

  cy.mount(UserProfile, {
    store,
  })

  cy.get('div.name').should('have.text', user.name)
})
```

### Global Components

If you have components that are registered globally in the main application file, set them up in your mount command so your component will render them properly:

Vue 3

```js
import { mount } from 'cypress/vue'
import Button from '../../src/components/Button.vue'

Cypress.Commands.add('mount', (component, options = {}) => {
  // Setup options object
  options.global = options.global || {}
  options.global.components = options.global.components || {}

  // Register global components
  options.global.components['Button'] = Button

  return mount(component, options)
})
```

Vue 2

```js
import { mount } from 'cypress/vue'
import Button from '../../src/components/Button.vue'

Cypress.Commands.add('mount', (component, options = {}) => {
  // Setup options object
  options.extensions = options.extensions || {}
  options.extensions.plugins = options.extensions.plugins || []
  options.extensions.components = options.extensions.components || {}

  // Register global components
  options.extensions.components['Button'] = Button

  return mount(component, options)
})
```


See also [Cypress: Vue api](https://docs.cypress.io/guides/component-testing/vue/api)