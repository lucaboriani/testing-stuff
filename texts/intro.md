# Testing Stuff

This is meant to be a starting point and a handy reference for those who want to start including test methodologies in their daily js workflow. 

Currently it's a cutup of various sources available online, and is at it's early stages, so reader be kind!


Special attention will here be given to frontend tooling and frameworks.

**Please note this is a work in progress, any constuctive contribution and suggestion is friendly wellcome :)**



## Why Should I care ?

At first writing tests looks like a lot more of effort on top of our already tigh schedule, and it undoubtedly is: 

There's a whole new world of tooling and environments to get acquainted to, a new mindset has to be digested, our codebase grows significantly... just to name a few.

Yet, especially when building mid to large sized applications, the advantages of a properly tested codebase start to become clearer.



### Building Confidence In Your Code

To know that your code is working as planned, it needs to be tested in some kind. Manual test work for most small applications but don't provide the security and confidence level you get using automated tests.

Automated tests make it easy to test almost every possible situation and allow you to run them whenever you are making a change to your code.

**Identifying every success and failure case and writing tests for them will ensure that you are confident with the code you are deploying for production**.

### Writing Minimal Code

Testing also helps you to reduce the amount of code you are writing for a particular feature. After testing, your main goal is to write the minimal required code to make the tests pass. This coding style where you write tests before you write the actual implementation is also known as TDD (Test-driven development).

**After successfully testing, you can focus on writing clean implementations with as minimal code as possible**.

### Getting Rid Of Regression Bugs

Do you know the feeling when you have just finished a new feature of your application and want to release it to production and all of a sudden, an old feature isn't working anymore? You are absolutely clueless about why this is happening and will probably waste a lot of time searching for the issue.

This situation would have never occurred if you had tested your old features. You could have frequently run those tests to check if your application is still working as expected. **The tests would also give you a better idea of what exactly isn't working anymore because the appropriate test cases would fail**.

from [An Introduction to testing in Javascript](https://gabrieltanner.org/blog/testing-introduction/)



## Testing Mindset

It is critical to understand that **testing is a mindset** before we go any further. Testing methodologies, tools, frameworks, etc. are irrelevant if you first do not understand that testing is first and foremost, a mindset.

The reason why software development teams and QA teams are separate is because both teams view an application completely differently. Developers are responsible for building things, while QA professionals are responsible for breaking things. If developers want to write tests, they need to learn to think the way that a QA professional does and then translate that way of thinking into automated tests.

Now that we understand that testing is a mindset, we need to discuss how this mindset plays out in the day to day work of software development. As new feature requests or bugs come in, you and your team should be thinking first about how you are going to test the application code related to these changes.

**An easy way to do this is to start with the end goal and work backwards.**

What does this new feature need to do? Once you understand that, you can break the problem down into small incremental steps, and translate those steps into tests. This way you have a clear path to achieve your goal and a suite of tests that will confirm everything is working properly.

from [Understanding the Testing Mindset](https://learn.cypress.io/testing-foundations/testing-is-a-mindset)

