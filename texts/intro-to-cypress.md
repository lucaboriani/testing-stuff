
# Introduction to Cypress

[cypress](https://docs.cypress.io/)

Cypress was born as an end to end environment but has evolved into a whole ecosystem which  enables you to write all types of tests:

- End-to-end tests
- Integration tests
- Unit tests

Cypress can test anything that runs in a browser.

## Installation

Install Cypress:

`cd /your/project/path`

`npm install cypress --save-dev`

or

`yarn add cypress --dev`

## Opening the App

`npx cypress open`

On opening Cypress, your testing journey begins with the Launchpad. Its job is to guide you through the decisions and configuration tasks you need to complete before you start writing your first test.

If this is your first time using Cypress it will take you through the following steps in order.

### Choosing a Testing Type

The Launchpad presents you with your biggest decision first: What type of testing shall I do? 

[E2E Testing](/end-to-end), where I run my whole application and visit pages to test them? 

Or [Component Testing](/component-test), where I mount individual components of my app and test them in isolation?


## Identifying What to Test

One of the most difficult challenges when first learning how to write tests is understanding what you should be testing. If you already have a large application in production without any tests, how would you write tests for it?

### User Journeys

If you are trying to ***test an already existing application***, begin by writing tests for your application's most "mission-critical" pieces. 

By "mission-critical," we mean any portions of your application that cannot go down or break. For example, login/authentication, purchasing a product, processing a credit card, sign-up forms, etc. 

**Your first suite of tests should be for these portions of your application and that they should be end-to-end tests**.

How exactly should you write your tests? We recommend writing tests for "user journeys." User journeys are the essential paths in which a user of your application takes.

For example, in the context of an application:

- A user lands on the home page and finds one of the courses.
- They click on the “Get started” button and are taken to the course landing page.
- They click on the “Start Course” button and are taken to the first lesson of that course.
- They read the lesson and complete the quiz at the bottom.
- After answering the quiz correctly, they click on the “Next Lesson” button and are taken to the next lesson.

This could be the test

```js
describe("User Journey", () => {
  it("a user can find a course on the home page and complete the courses lessons", () => {
    cy.visit("http://localhost:3000")
    cy.getByData("course-0").find("a").eq(3).click()
    cy.location("pathname").should("eq", "/testing-your-first-application")
    cy.getByData("next-lesson-button").click()
    cy.location("pathname").should(
      "eq",
      "/testing-your-first-application/app-install-and-overview"
    )
    cy.getByData("challenge-answer-0").click()
    cy.getByData("next-lesson-button").should("exist").click()
    cy.location("pathname").should(
      "eq",
      "/testing-your-first-application/installing-cypress-and-writing-our-first-test"
    )
  })
})
```
**The entire user journey should also be tested with a single test**. The reason why it should be a single test, is **so that you can make sure that all the pieces within your application are working correctly**. 

Testing user journeys also tests all of the layers within your tech stack. You are testing the front-end and the back-end, the database layer, networking/API layers, etc.

see also [How to test user journeys](https://learn.cypress.io/testing-your-first-application/how-to-test-user-journeys)


<iframe width="560" height="315" src="https://www.youtube.com/embed/lLk_oPeUcNQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Best Pracitces

For a comprehensive guide on 
- Organizing Tests, Logging In, Controlling State
- Selecting Elements
- Assigning Return Values
- Visiting external sites
- Having tests rely on the state of previous tests
- Creating "tiny" tests with a single assertion
- Using after or afterEach hooks
- Unnecessary Waiting
- Running Tests Intelligently

take a read here: [Best Practices](https://docs.cypress.io/guides/references/best-practices)



## Writing and organizing Test , Cypress

[https://docs.cypress.io/guides/core-concepts/writing-and-organizing-tests](https://docs.cypress.io/guides/core-concepts/writing-and-organizing-tests)





## How to test user journeys




## 1 afternoon free Course on learn.cypress.io:

Here an excellent introduction to Cypress [https://learn.cypress.io/](https://learn.cypress.io/)

## Recipes

An Excellent list of examples can be found here:
[Cypress Recipes](https://docs.cypress.io/examples/examples/recipes#Fundamentals) 



## Cypress RWA (Real World Application)

The Cypress team maintains the  [Real World App (RWA)](https://github.com/cypress-io/cypress-realworld-app), a full stack example application that demonstrates best practices and scalable strategies with Cypress in practical and realistic scenarios.


## Further resources :

[Cypress youtube channel](https://www.youtube.com/cypress_io)



# Bonus : Automated test writing with Cypress Studio

Cypress Studio is an experimental feature and can be enabled by adding the experimentalStudio attribute to your Cypress configuration.

in cypress.config.js:

```js
const { defineConfig } = require('cypress')

module.exports = defineConfig({
  e2e: {
    experimentalStudio: true
  }
})
```
- Cypress Studio is currently only available for writing E2E tests, and doesn't yet work with Component Testing.
- Cypress Studio does not support writing tests that use domains of multiple origins.

Cypress Studio provides a visual way to generate tests within Cypress, by recording interactions against the application under test.

The .click(), .type(), .check(), .uncheck(), and .select() Cypress commands are supported and will generate test code when interacting with the DOM inside of the Cypress Studio. You can also generate assertions by right clicking on an element that you would like to assert on.



<iframe width="560" height="315" src="https://www.youtube.com/embed/kBYtqsK-8Aw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>






