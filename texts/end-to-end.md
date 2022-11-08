# E2E testing

End-to-end testing, also known as E2E testing, is a methodology used for ensuring that applications behave as expected and that the flow of data is maintained for all kinds of user tasks and processes. This type of testing approach starts from the end user’s perspective and simulates a real-world scenario. For example, on a sign-up form, you can expect a user to perform one or more of these actions:

- Enter a blank email and password
- Enter a valid email and password
- Enter an invalid email and password
- Click a sign-up button

You can use end-to-end testing to verify that all these actions work as a user might expect.

# Why should I care ?

End-to-end testing has been widely adopted because it:

- Helps teams expand their test coverage by adding more detailed test cases than other testing methods like unit and functional testing.
- Ensures the application performs correctly by running the test cases based on the end user’s behavior.
- Helps release teams reduce the time to market by allowing them to automate critical user paths.
- Reduces the overall cost of building and maintaining software by decreasing the time it takes to test software.
- Helps predictably and reliably detect bugs.

from [What is end-to-end testing?](https://circleci.com/blog/what-is-end-to-end-testing/)



E2E Testing is a technique that tests your app from the web browser through to the back end of your application, as well as testing integrations with third-party APIs and services. These types of tests are great at making sure your entire app is functioning as a cohesive whole.



End-to-end tests are great at verifying your app runs as intended, from the front end to the back end.

However, end-to-end tests can be more difficult to set up, run, and maintain. ***There are often infrastructure needs in setting up a backend for testing purposes. Your team will need to develop a strategy on how to handle this complexity***.

## Benefits of end-to-end tests:

- Ensure your app is functioning as a cohesive whole
- Tests match the user experience
- Can be written by developers or QA Teams
- Can be used for integration testing as well

## Considerations for end-to-end tests:
- More difficult to set up, run, and maintain
- Provision testing infrastructure in CI
- Testing certain scenarios require more setup

## Common scenarios for end-to-end tests:
- Validating critical workflows like authentication and purchasing
- Ensuring data is persisted and displayed through multiple screens
- Running Smoke Tests and System Checks before deployment

from [What is E2E Testing?](https://docs.cypress.io/guides/core-concepts/testing-types#What-is-E2E-Testing)

