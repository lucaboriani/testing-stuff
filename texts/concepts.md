# Concepts

Tests should be composed using a pattern that makes them easy to write, reason about, and expand.

One pattern is the

## AAA (Arrange-Act-Assert)

Which encourages the organization of the test code in a way that allows the most readability and flexibility.

In step one, the Arrange step, you have to perform some setup for your test. 

In step two, the Act step, you perform some action.

In step three, you Assert.  In this step, you assert that the thing you acted upon in step two did what you expected.

here a sample of AAA using [jest](https://jestjs.io/) 

```js
/**
 * @jest-environment jsdom
 */
 import { emptyNode } from '../emptyNode' 
 describe('emptyNode', () => {
    test('create a structure of nested elements and empty it', () => {
      // arrange
      const container = document.createElement('div')
      container.innerHTML = `
        <div>
          <ul>
            <li>test</li>
            <li>test2</li>
          </ul>
        </div>
      `
      document.body.appendChild(container)
      // act
      emptyNode(container)
      // assert
      expect(container.children.length).toBe(0)
    })
  })
```
### Lifecycle:

When testing, you often execute multiple tests after each other and have some setup work that needs to happen before the tests run. Most frameworks provide helper functions to handle these scenarios.

Here is an example of lifecycle methods in the Jest testing framework.

```js
 beforeEach(() => {
    IntersectionHandler.init({})
    document.body.innerHTML = ''
 })
 afterEach(() => {
     IntersectionHandler.clear()
     jest.clearAllMocks()
 })
```

## Matchers:

Matchers let you validate the results and values of your tests in different ways and are used to make sure that the results of the test match your expectations.

```js
    expect(container.children.length).toBe(0)
```

Here the **expect()** function checks if the result meets the conditions defined by the matcher. 

## Mocking:

An object under a test might have dependencies on other objects or services. To isolate the behavior of an object, you want to replace the other objects it interacts with by mocks that simulate the behavior of the real objects.

Mocks help your tests to avoid test unreliability (flakiness) and improve the speed of your tests. They are also useful if the real objects are impractical to incorporate into tests.

In short, mocking is creating objects or services that simulate the behavior of real objects.


Here an example basic mock of an **IntersectionObserver** (a browser API that isn't currently covered by jest's dom implementation) using jest :

```js
    Object.defineProperty(window, 'IntersectionObserver', {
      writable: true,
      value: jest.fn().mockImplementation(() => ({
          observe: jest.fn(),
          unobserve: jest.fn(),
      })),
    });
```

place it in a "__mocks__" folder and use it in your test files:

```js
/**
 * @jest-environment jsdom
 */
import './../__mocks__/IntersectionObserver.mock'

import  IntersectionHandler from "../IntersectionHandler";

const testCallbacks1 = jest.fn()
  

beforeEach(() => {
    // arrange
    document.body.innerHTML = ''
    IntersectionHandler.init({})
})
afterEach(() => {
    // arrange
    IntersectionHandler.clear()
    jest.clearAllMocks()
})

describe('checking  methods of handler : ', ()=>{
    test('init', ()=>{
        // act
        const handler = IntersectionHandler.init({})
        // assert
        expect(handler).toBe(IntersectionHandler)
    })
})
```
Please note that without the mock and the initial comment 
```js
/**
 * @jest-environment jsdom
 */
```
 the test would fail as behind the scenes "IntersectionHandler.init({})" constructs a new IntersectionObserver, and our mock relies on the window object provided by jsdom




