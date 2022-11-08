
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

[E2E Testing](/end-to-end), where I run my whole application and visit pages to test them? Or Component Testing, where I mount individual components of my app and test them in isolation?



