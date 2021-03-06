---
title: Introduction to Unit Testing with NodeJS and Jasmine
type: lesson
duration: "1:15"
creator:
    name: Mike Hopper/ATL
competencies: Testing
---

# Testing a MEAN Stack App

## Intro to Testing

* Problem: how can we automate the testing of our software components?

### Types of Testing:

See: [Types of Software Testing](http://www.softwaretestinghelp.com/types-of-software-testing/)

* How tests are executed:
  - __Manual__ - user runs test via the UI
  - __Automated__ - test scripts are executed that call into the code and compare results to expected values
* Granularity:
  - __Unit__: Focuses on testing individual "units" of code, usually individual functions or methods.
  - __Component__: class, library
  - __Integration__: set of components that are collaborating (interacting) to perform a task
  - __End-to-end (E2E)__: complete application running in an environment that mimics a real-world production environment
* Purpose
  - __Functional__
     * __Positive testing__ - does it work when it is supposed to work.
     * __Negative testing__ - does it fail when it is supposed to fail.
  - __Regression__: Did we break anything?
  - __Smoke__: Did the build work?
  - __Performance / Load__: How does the software behave under a heavy load?
     * Lots of users / traffic
     * Large data sets
  - __Usability__: How intuitive (easy to use) is the software?
  - __Security__: How secure is the application?
  - __Compatibility__: How well does the software work with various hardware, O.S., network environments?
  - __Recovery__: How well does the system respond to hardware or software failures? Is it fault-tolerant?
  - __User Acceptance Testing (UAT)__ - Does the software do what the customers want it to do?
     * Actual software users test the software to make sure it can handle required tasks in real-world scenarios, according to specifications.


### TDD and BDD

#### TDD: Test-driven development

A development methodology of writing the tests first, then writing the code to make those tests pass. Thus the process is:

1. Define a test set for the unit
2. Implement the unit
3. Verify that the implementation of the unit makes the tests succeed.

#### BDD: Behavior-driven development

A development methodology that was derived from `TDD` and `DDD` where tests are written in an English-like language (i.e. the `Gherkin` language) that specifies the external *behavior* (the specifications) of the unit without reference to how the unit was implemented (thus it is a form of *black box* testing). The purpose of BDD is to both describe and test the behavior of a unit of code in a single *specification* file.

## Code Along

Let's use `jasmine-node` and the `Jasmine` testing framework to write a simple
unit test. First we need to install `jasmine-node`:

```bash
npm install -g jasmine-node@2.0.0-beta4
```

Next we need to create a directory and then create the files `leap-year.js`
and `spec/leap-year-spec.js`:

```bash
cd ~/ga/wdi/projects/javascript
mkdir -p testing/leap-year
cd testing/leap-year
mkdir spec
touch leap-year.js spec/leap-year-spec.js
subl .
```

> leap-year.js

```javascript
exports.isLeapYear = function(year) {
  // TODO: implement this
};
```

> leap-year-spec.js

```javascript
var isLeapYear = require("../leap-year").isLeapYear;

describe("isLeapYear", function() {
  it("should return false for the year 1901", function() {
    var result = isLeapYear(1901);
    expect(result).toBe(false);
  });
});
```

> Note: You should always finish your jasmine spec filenames with spec.js because jasmine-node will only run these files by default.

Now we can run our spec and watch it *fail*!

```bash
jasmine-node spec
```

which should result in:

```text
F

Failures:

  1) isLeapYear should return false for the year 1901
   Message:
     Expected undefined to be false.
   Stacktrace:
     Error: Expected undefined to be false.
    at null.<anonymous> (/Users/drmikeh/ga/wdi/projects/javascript/testing/leap-year/spec/leap-year-spec.js:6:20)

Finished in 0.011 seconds
1 test, 1 assertion, 1 failure, 0 skipped
```

Now let's "fix" our code to pass this test. Simply modify `leap-year.js` to containing the following code:

```javascript
exports.isLeapYear = function(year) {
  return false;                // add this line
};
```

Now run the test again and watch it *pass*!

```bash
jasmine-node spec
```

which should result in:

```text
.

Finished in 0.005 seconds
1 test, 1 assertion, 0 failures, 0 skipped
```

Yay, We Got It Working!!!
I guess we can go home early, cause our code is passing our test (dance, dance dance).
So are we done?
Is there something screwy going on here?
We know that our code is wrong but our test is passing, so what do we do?

Answer: Our testing is insufficient. We need to improve our testing by adding more tests to reveal that our code is wrong / buggy. So let's add the following test to `leap-year-spec.js`:

```javascript
  it("should return true for the year 1904", function() {
    var result = isLeapYear(1904);
    expect(result).toBe(true);
  });
```

Also note that we can have `jasmine-node` autorun the spec files whenever we change any file via:

```bash
jasmine-node spec --autotest --watch .
```

Now we see that one test passes and one test fails. Let's fix our code in `leap-year.js` to make both tests pass:

```javascript
exports.isLeapYear = function(year) {
  return (year % 4 == 0) && (year % 100 != 0);    // this is better!
};
```

Now we can see that both of our tests pass, but we still have a problem. We need to test for those pesky years that are multiples of 400, such as the year 2000:

```javascript
it("should return true for the year 2000", function() {
  var result = isLeapYear(2000);
  expect(result).toBe(true);
});
```

And we should now see 2 tests pass and 1 test fail. We can solve this by using this implementation:

```javascript
exports.isLeapYear = function(year) {
  return ((year % 4 == 0) && (year % 100 != 0)) || (year % 400 == 0);
};
```

Finally we are convinced that our `isLeapYear` function is working and we have a nice set of tests to prove it.

And if anyone messes up our beautiful code and introduces a bug, our automated unit tests should catch the bug and alert us that we have a problem that needs to be fixed.

## Testing Frameworks

There are so many :-)

* [Jasmine](http://jasmine.github.io/)
* [Karma](http://karma-runner.github.io/0.12/index.html)
* [Protractor](https://github.com/angular/protractor)
* [Mocha](http://mochajs.org/)
* [Should](http://shouldjs.github.io/)
* [Supertest](https://github.com/visionmedia/supertest)
* [Chai](http://chaijs.com/)
* [SinonJS](http://sinonjs.org/)
* [Yadda](https://github.com/acuminous/yadda)
* [CasperJS](http://casperjs.org/)

## Summary

* `TDD` helps us to write good tests and good code by writing the tests first.
* `BDD` helps us to write tests in terms of the specifications of the code (i.e. how the code should behave).
* We can integrate unit testing into our `build` process so that each build will run our tests. If any of the tests fail, the build fails and we need to address and resolve the problem.

> Test often, fail early!!!

