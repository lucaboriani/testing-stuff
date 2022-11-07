# Introduction to Jest

[jest.io](https://jestjs.io/)

It is the most popular framework out there and most of the concepts apply to all testing frameworks and can be useful no matter which technology you are using.

## Installation
Jest can be installed using either npm or yarn:
```yarn add --dev jest
# or
npm install --save-dev jest
```

but in order to be able to use esm modules, test DOM related issues and mock the objects as needed we'll also have to install **babel-jest** , **jest-environment-jsdom**, **jsdom**, **jsdom-testing-mocks**, so probably we'll start with:

```yarn add --dev jest babel-jest jest-environment-jsdom jsdom jsdom-testing-mocks
# or
npm install --save-dev jest babel-jest jest-environment-jsdom jsdom jsdom-testing-mocks
```

To run your test using the test command :

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

## A first basic test

Imagine we have a Cookie.js module, like so:

```js
/**
 * gets cookie value
 * @param {String} name : cookie name
 */
export const getCookie = (name) => {
  return document.cookie.split('; ').reduce((r, v) => {
    const parts = v.split('=')
    return parts[0] === name ? decodeURIComponent(parts[1]) : r
  }, '')
}

/**
 * Sets a cookie
 * @param {String} name : cookie name
 * @param {*} value : cookie value
 * @param {Number} days : cookie validity (in days)
 * @param {String} path : the path to get the cookie
 */
export const setCookie = (name, value, days = 7, path = '/') => {
  const expires = new Date(Date.now() + days * 864e5).toUTCString()
  document.cookie = name + '=' + encodeURIComponent(value) + '; expires=' + expires + '; path=' + path
}

/**
 * Cancella un cookie
 * @param {*} name : il nome del cookie
 * @param {*} path : il percorso per recuperare il cookie
 */
export const deleteCookie = (name, path = '/') => {
  setCookie(name, '', -1, path)
}
```

testing the module could look like so:

```js
  /**
 * @jest-environment jsdom
 */

import { setCookie,getCookie,deleteCookie } from '../Cookie'
import { getCookie } from '../getCookie'
import { deleteCookie } from '../deleteCookie'

describe('test Cookies functions', () => {
  test("sets a 'pippo' cookie in / checks if it exists and deletes it", () => {
    setCookie('pippo', 3, 7)
    expect(getCookie('pippo')).toBe('3')
    deleteCookie('pippo')
    expect(getCookie('pippo')).toBe('')
  })
})
```

Please note that without jsdom and jest-environment-jsdom installed and the  @jest-environment comment the test would fail, as Cookie.js relies on the existence of the window.document object

```js
  /**
 * @jest-environment jsdom
 */
```

## Matchers

Matchers let you validate the results and values of your tests in different ways.

They are most commonly used to compare the result of the expect() function to the value passed as an argument to the matcher (That is also what we did above).

Most common matchers:

- toBe -  compares for strict equality (e.g. ===)
- toEqual - compares the values of two variables/objects
- toBeNull - checks if the value is null
- toBeDefined - checks if the value is defined
- toBeUndefined - checks if the value is undefined
- toBeTruthy - checks if the value is true (similar to an if statement)
- toBeFalsy - checks if the value is false(similar to an if statement)
- toBeGreaterThan - checks if the result of the expect() function is greater than the argument
- toContain - checks if the result of expect() contains a value
- toHaveProperty -  checks if an object has a property, and optionally checks its value
- toBeInstanceOf - checks if an object is an instance of a class

matchers can also be negated using the not statement:

```js
  test("factorial of 3 is not 5", () => {
    expect(factorialize(3)).not.toBe(5);
});
```

## Setup and Teardown

Often when writing tests, you will have to do some kind of setup like initializing variables before tests run and some sort of action after they have finished, remember the **AAA Pattern** (Arrange, Act, Assert) ?

Jest provides two different ways you can do that.

## One-Time Setup:
In some cases, you only need to do the setup once, at the beginning of your test file. In that scenario, you can use the beforeAll() and afterAll() helper functions that will execute before the tests start and after all have finished.


```js
 beforeAll(() => {
  return initializeDatabase();
});

afterAll(() => {
  return clearDatabase();
});

test('query from database', () => {
  expect(database.getObject('Florida')).toBeTruthy();
});
```

## Repeating setup for each test:

If you have a setup process that needs to run before each test than you should use the beforeEach() and afterEach() functions.

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

## Grouping tests

You can also group related tests together so you can isolate the setup and teardown functions. Grouping tests will also help you get a better overview of your different test cases.

```js
 describe('testing factorial function', () => {
    beforeAll(() => {
    	//do something
    })
    afterAll(() => {
    	//do something
    })
  
    test("factorial of 3", () => {
    	expect(factorialize(3)).toBe(6);
    });

    test("factorial of 5", () => {
        expect(factorialize(5)).toBe(120);
    });

    test("factorial of 3 is not 5", () => {
        expect(factorialize(3)).not.toBe(5);
    });
})
```

## Testing async functions

It is common for Javascript code to run asynchronously using either promises or callbacks. The problem with testing asynchronous code is knowing when the code that you are testing is actually complete. Jest has several ways to handle this.

### Promises:

Testing promises is straight forward in Jest. Just return the promise and Jest will wait for the promise to resolve. If the promise fails, the test will automatically fail aswell.

```js
// string.js
export const reverseString = str => {
  return new Promise((resolve, reject) => {
    if (!str) {
      reject("Empty string");
      return;
    }
    resolve(str.split("").reverse().join(""));
  });
};
```
```js
// string.spec.js
import {reverseString}  from  "./string";

test(`reverseString 'String' to equal 'gnirtS'`, () => {
  return reverseString("String").then(str => {
    expect(str).toBe("gnirtS");
  });
});
```

You can also catch rejected promises using the catch() function.

```js
test(`reverseString '' to reject promise`, () => {
  return reverseString("String").catch(error => {
    expect(e).toMatch("Empty string");
  });
});
```

### Async await:
Alternatively, we can use async and await for testing promises.

const reverseString = require("./string");

  
```js
test(`reverseString 'String' to equal 'gnirtS' using await`, async () => {
    const str = await reverseString("String")
    expect(str).toBe("gnirtS");
});
```

### Callbacks:

By default Jest tests complete once they reach the end of their execution which means that the test will be completed before the callback is called. This can be fixed by passing a single argument named done to your test function. **Jest will wait until the done callback is called** before finishing the test.
```js
// string.js
export function reverseStringCallback(str, callback) {
    callback(str.split("").reverse().join(""))
}
```

```js
// string.spec.js
import {reverseStringCallback}  from  "./string";
test(`reverseStringCallback 'string' to equal 'gnirts'`, (done) => {
    reverseStringCallback('string', (str) => {
      expect(str).toBe('gnirts')
      done()
    })
})
```

Note that if done() is never called, the test will fail with a timeout error.


## Mocking

Mocking is creating objects or services that simulate the behavior of real objects and plays a vital part in testing. The goal for mocking an object or function is to replace something we don’t control like an external service with something we do, that is why it’s essential that what we replace it with something that has all the features we need.

### Passing Mocks to functions:

One of the common ways to use the Mock function is by passing it as an argument to the function you are testing. This allows you to run your tests without importing the real dependencies and objects you would pass in your real application.

```js
const multiplyNumbers = (a, b, callback) => {
  callback(a * b);
};

test("calls callback with arguments added", () => {
  const mockCallback = jest.fn();
  multiplyNumbers(1, 2, mockCallback);
  expect(mockCallback).toHaveBeenCalledWith(2);
});
```

This strategy is great, but requires that your code supports dependency injection. If that is not the case you will need to mock already existing modules or functions instead.

### Mocking a single function:

You can mock a single function using Jest.fn():

```js
const lodash = require('lodash')

lodash.chunk = jest.fn(() => 'test')
test(`Test lodash chunk function`, () => {
    const result = lodash.chunk(['a', 'b', 'c', 'd'], 2)
    expect(result).toBe('test')
    expect(lodash.chunk).toHaveBeenCalled()
    expect(lodash.chunk).toHaveBeenCalledWith(['a', 'b', 'c', 'd'], 2)
})
```

### Mocking Modules:

Mocking single functions works well if you only use one or two functions of a package or library but can get very cluttered when you need more functions of a module. Here we use jest.mock to automatically set the exports of an entire module instead of mocking the modules manually.


```js
const lodash = require('lodash')
jest.mock('lodash');

test(`Test lodash chunk function`, () => {
    const result = lodash.chunk(['a', 'b', 'c', 'd'], 2)
    expect(lodash.chunk).toHaveBeenCalled()
    expect(lodash.chunk).toHaveBeenCalledWith(['a', 'b', 'c', 'd'], 2)

    const concatResult = lodash.concat(2, [3], [[4]]);
    expect(lodash.concat).toHaveBeenCalled()
    expect(lodash.concat).toHaveBeenCalledWith(2, [3], [[4]])
})
```

### Mocking ESModules

Jest was born with node modules in mind, in order to use it with ESModules you should install a transpiler such as **babel-jest**

Then the syntax is slightly different :

```js
import {loop} from '../loop'
import {ctx} from '../init'  // this is used in loop()
import { generateNoise } from '../generateNoise' // this is called in loop()
jest.mock('../generateNoise', () => ({
    __esModule: true, // without this tests will fail
    generateNoise: jest.fn(), // mocked function
}));
jest.mock('../init', () => ({
    __esModule: true, // without this tests will fail
    ctx: jest.fn(), // mocked function
}));

describe('testing loop', () => {         
    // @ts-ignore
    it('calls loop  and verifies its logic', () => {
        loop()
        expect(generateNoise).toHaveBeenCalledWith(ctx)  
    })
})
```

## Spying packages:

You can also spy on a package without creating a mock for it. This is done using the spyOn() function provided by Jest.

```js
import lodash from 'lodash'
// or 
const lodash = require('lodash')


test(`Test lodash chunk function`, () => {
  const spy = jest.spyOn(lodash, 'chunk')
  const result = lodash.chunk(['a', 'b', 'c', 'd'], 2)

  expect(lodash.chunk).toHaveBeenCalled()
  expect(lodash.chunk).toHaveBeenCalledWith(['a', 'b', 'c', 'd'], 2)
})
```


## Expecting a function to throw error:

You should always take in account edge cases and the possibility that a funtion you are calling will generate an error.

imagine we have a simple module which creates a div in anothe div:

```js
export function createContainerInMount (selector) {
  const mount = document.querySelector(selector)
  if (mount) {
    const container = document.createElement('div')
    container.id = 'test'
    mount.appendChild(container)
    return container
  }
  throw new Error('no mount found')
}
```

we could test it like this:

```js
/**
 * @jest-environment jsdom
 */
/* eslint-disable no-undef */
import { createContainerInMount } from '../createContainerInMount'

describe('testing : createContainerInMount', () => {
  test('mount exists ', () => {
    document.body.innerHTML = `
      <div id="test"></div>
    `
    createContainerInMount('#test')
    expect(document.querySelector('#test')).not.toBeNull()
  })
  test('mount does not exist ', () => {
    document.body.innerHTML = `
    `
    expect(() => {
      createContainerInMount('#test')
    }).toThrow('no mount found') // Or .toThrow('expectedErrorMessage')
  })
})
```
