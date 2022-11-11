# React Component Testing with Cypress

see also [Getting started](https://docs.cypress.io/guides/component-testing/react/quickstart#Getting-Started) and [Framework Support](https://docs.cypress.io/guides/component-testing/react/overview#Framework-Support)

Cypress Component Testing currently supports React 16+ with the following frameworks:

- Create React App
- Next.js
- React with Vite
- React with Webpack

To get up and running with Cypress Component Testing in React, install Cypress into your project:

`npm i cypress -D`

Open Cypress:

`npx cypress open`

The Cypress Launchpad will guide you through configuring your project.

### Creating and testing a Component

Example component: 
```js

import { useState } from 'react'

export default function Stepper({ initial = 0, onChange = () => {} }) {
  const [count, setCount] = useState(initial)

  const handleIncrement = () => {
    const newCount = count + 1
    setCount(newCount)
    onChange(newCount)
  }

  const handleDecrement = () => {
    const newCount = count - 1
    setCount(newCount)
    onChange(newCount)
  }

  return (
    <div>
      <button data-cy="decrement" onClick={handleDecrement}>
        -
      </button>
      <span data-cy="counter">{count}</span>
      <button data-cy="increment" onClick={handleIncrement}>
        +
      </button>
    </div>
  )
}
```
mounting the component:

```js
import Stepper from './Stepper'

describe('<Stepper>', () => {
  it('mounts', () => {
    cy.mount(<Stepper />)
  })
})
```


#### Example test for the Stepper component

```js
describe('<Stepper>', () => {
  it('stepper should default to 0', () => {
    cy.mount(<Stepper />)
    cy.get('[data-cy=counter]').should('have.text', '0')
  })
  it('supports an "initial" prop to set the value', () => {
    cy.mount(<Stepper initial={100} />)
    cy.get('[data-cy=counter]').should('have.text', '100')
  })
  it('when the increment button is pressed, the counter is incremented', () => {
    cy.mount(<Stepper />)
    cy.get('[data-cy=increment]').click()
    cy.get('[data-cy=counter]').should('have.text', '1')
  })

  it('when the decrement button is pressed, the counter is decremented', () => {
    cy.mount(<Stepper />)
    cy.get('[data-cy=decrement]').click()
    cy.get('[data-cy=counter]').should('have.text', '-1')
  })
  it('clicking + fires a change event with the incremented value', () => {
    const onChangeSpy = cy.spy().as('onChangeSpy')
    cy.mount(<Stepper onChange={onChangeSpy} />)
    cy.get('[data-cy=increment]').click()
    cy.get('@onChangeSpy').should('have.been.calledWith', 1)
  })
})
```

### Custom Mount Commands

By default, cy.mount() is a simple passthrough to mount(), however, you can customize cy.mount() to fit your needs. For instance, if you are using providers or other global app-level setups in your React app, you can configure them here.

#### React Router

If you have a component that consumes a hook or component from React Router, make sure the component has access to a React Router provider. Below is a sample mount command that uses MemoryRouter to wrap the component.

in cypress/support/component.js:

```js
import { mount } from 'cypress/react'
import { MemoryRouter } from 'react-router-dom'

Cypress.Commands.add('mount', (component, options = {}) => {
  const { routerProps = { initialEntries: ['/'] }, ...mountOptions } = options

  const wrapped = <MemoryRouter {...routerProps}>{component}</MemoryRouter>

  return mount(wrapped, mountOptions)
})
```

To set up certain scenarios, pass in props that will get passed to MemoryRouter in the options. Below is an example test that ensures an active link has the correct class applied to it by initializing the router with initialEntries pointed to a particular route:

```js
import { Navigation } from './Navigation'

it('home link should be active when url is "/"', () => {
  // No need to pass in custom initialEntries as default url is '/'
  cy.mount(<Navigation />)

  cy.get('a').contains('Home').should('have.class', 'active')
})

it('login link should be active when url is "/login"', () => {
  cy.mount(<Navigation />, {
    routerProps: {
      initialEntries: ['/login'],
    },
  })

  cy.get('a').contains('Login').should('have.class', 'active')
})
```


#### Redux

To use a component that consumes state or actions from a Redux store, create a mount command that will wrap your component in a Redux Provider:

in cypress/support/component.js:

```js
import { mount } from 'cypress/react'
import { Provider } from 'react-redux'
import { getStore } from '../../src/store'

Cypress.Commands.add('mount', (component, options = {}) => {
  // Use the default store if one is not provided
  const { reduxStore = getStore(), ...mountOptions } = options

  const wrapped = <Provider store={reduxStore}>{component}</Provider>

  return mount(wrapped, mountOptions)
})
```

The options param can have a store that is already initialized with data (note that the `getStore` method is a factory method that **initializes a new Redux store**. 
It is *important* that the store be initialized **with each new test** to ensure changes to the store don't affect other tests.):

```js
import { getStore } from '../redux/store'
import { setUser } from '../redux/userSlice'
import { UserProfile } from './UserProfile'

it('User profile should display user name', () => {
  const user = { name: 'test person' }

  // getStore is a factory method that creates a new store
  const store = getStore()

  // setUser is an action exported from the user slice
  store.dispatch(setUser(user))

  cy.mount(<UserProfile />, { reduxStore: store })

  cy.get('div.name').should('have.text', user.name)
})
```

#### Cypress React API

see [https://docs.cypress.io/guides/component-testing/react/api#Methods](https://docs.cypress.io/guides/component-testing/react/api#Methods)