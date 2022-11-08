# What is Component Testing?

Component tests differ from end-to-end tests in that instead of visiting a URL to pull up an entire app, a component can be "mounted" and tested on its own. This allows you to focus on testing only the component's functionality and not worrying about other nuances with testing a component as part of the larger application.

Typically, a component test is written by the developers working on the component. The code for the test lives alongside the component code, and it is common for tests to be coded simultaneously with the component, helping developers verify the required functionality while building it.

## Benefits of component tests:
- Easier to test components in isolation
- Fast and reliable
- Easy to set up specific scenarios in tests
- Don't rely on any external system to run

## Considerations for component tests:
- Do not ensure overall app quality
- Do not call into external APIs/Services
- Usually written by developers working on the component

## Common scenarios for component tests:
- Testing a date picker works properly for a variety of scenarios
- That a form shows and hides specific sections based on input
- Testing components coming out of a design system
- Testing logic not tied to a component (like unit tests!)

from [Testing types](https://docs.cypress.io/guides/core-concepts/testing-types)


# Testing libraries

Built on top of [Dom testing Library](https://testing-library.com/docs/dom-testing-library/intro/) :

- [React testing Library](../testing-react-components-react-testing-library)
- [Vue testing Library](../testing-vue-components-vue-testing-library)
- @todo svelte testing Library

# Component testing with Cypress

@ todo