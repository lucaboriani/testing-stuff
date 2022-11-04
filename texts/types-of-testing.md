# Types of tests

from [An Introduction to testing in Javascript](https://gabrieltanner.org/blog/testing-introduction/) and [Testing Types](https://docs.cypress.io/guides/core-concepts/testing-types#End-to-End-or-Component-Tests) :

There are a few different types of tests, and it is essential to know how they differ from each other. Most applications will require you to write multiple kinds of tests to get the best result possible.

## Unit tests

The purpose of a unit test is to validate the functionality of a relatively small piece of software, independently from other parts. Unit tests are narrow in scope, which allows us to cover all cases to ensure that every single part works correctly.

The downside to these kinds of tests is that they are not executed on real devices and therefore have lower fidelity than the other types of tests.

## Integration tests

Integration tests demonstrate that the different parts of your application work together in a real-life production environment. They verify that two separate modules or components are working together in the way they should.

These tests are of medium size and have a much higher execution time then Unit tests. They aren't executed as often but are still vital for checking the health status of your applications. Their fidelity is also a lot higher because they run on real devices and verify the actual interaction between various components of your application.

## End-to-End tests

E2E Testing is a technique that tests your app from the web browser through to the back end of your application, as well as testing integrations with third-party APIs and services. These types of tests are great at making sure your entire app is functioning as a cohesive whole.

E2E tests will also run on real devices just like integration tests and therefore, will again be quite slow in their execution.


## Component testing

Component tests differ from end-to-end tests in that instead of visiting a URL to pull up an entire app, a component can be "mounted" and tested on its own. This allows you to focus on testing only the component's functionality and not worrying about other nuances with testing a component as part of the larger application.

Typically, a component test is written by the developers working on the component. The code for the test lives alongside the component code, and it is common for tests to be coded simultaneously with the component, helping developers verify the required functionality while building it.

see also [What is the Testing Pyramid?](https://learn.cypress.io/testing-foundations/the-testing-pyramid)


Of course there are a lot more of smart things to copy and paste but this should give you an idea :) 