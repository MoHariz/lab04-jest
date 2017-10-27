# Lab: Testing with Jest

> People just aren't as repeatable as computers are. Nor should we expect them to be. A shell script or batch file will execute the same instructions, in the same order, time after time. It can be put under source control, so you can examine changes to the procedure over time as well ("but it used to work..."). - [The Pragmatic Programmer](http://www.amazon.com/The-Pragmatic-Programmer-Journeyman-Master/dp/020161622X)

This lab introduces **automated testing** using the [Jest](https://facebook.github.io/jest/) framework. By completing this tutorial, you will learn how to write and execute simple unit tests on JavaScript functions and DOM manipulating code.

## Testing
One of the most important goals when developing computer programs is to make sure that the code you write actually _works_. You can determine if a program works by considering three things:

1. What **input** was given to the program? From a user perspective, this is what _actions_ that user took (e.g., "I pressed a button"). From a code perspective, this is often what value was passed to a method.

2. Running the program with a particular _input_ will lead to a **received result**: something will happen because of the input. From a user perspective, this would be this would be like the changes to a web page caused by the button press. From a code perspective, this could be the value _returned_ by that function (or potentially a new value for a _state_ variable).

3. To know if a program worked though, you need to know the **expected result** of that program: what was _supposed_ to happen because of the input? If you don't know what the program was supposed to do, then you'll have no way of knowing if it worked or not!

You can test a program by providing the _input_ and then comparing the _received result_. If the _received result_ matches the _expected result_, then you know that the program worked! **Testing** is simply the process of providing the inputs and comparing the _received_ and _expected_ results

Providing the input and then comparing the received and expected results can get tedious, particularly if you have lots of possible inputs or they require multiple steps (like clicking on multiple buttons). For that reason, it's helpful to use **automation** to let a computer perform the testing for you. You define the expected result of some action, and the computer will check if the received result matches.

Because automated testing is so useful and comment, there are a wide variety of **testing frameworks**: external scripts that provide the code to let you easily compare received and expected results.

In JavaScript, the most popular testing frameworks are [Jasmine](https://jasmine.github.io/), [Mocha](https://mochajs.org/) (which is just the framework, it uses [Chai](http://chaijs.com/) to actually compare received and expected results&mdash;hot beverages are a theme), and [Jest](https://facebook.github.io/jest/). The later was developed by Facebook specifically to support testing React applications, and is the framework introduced and used in this course (though its API is almost identical to Jasmine and Mocha/Chai).


## Testing with Jest
Jest is a command-line program so you will need to have it installed on your machine. It can be installed _globally_ through `npm`:

```bash
npm install -g jest
```

Additionally, you will need to install the dependencies listed in this repository's `package.json` file, which will allow tests to use ES6 module syntax to access the functions in a separate file to test (as well as auto-complete definitions for Jest!)

```bash
# install all dependencies
npm install
```

With Jest, you define "tests", which are just JavaScript code that is used to perform the _action_ and compare the _received_ and _expected_ results. These tests can either be placed in a file whose name ends wth `.test.js` (indicating it is a test script), `.spec.js` (for "specification"), or a plain `.js` file inside the `__tests__` folder in your program. In either case, a test script is _just a JavaScript file_, so you include any JavaScript code/variables/functions/etc. you want.

## Writing a Test
We define a "test" (a check if a particular piece of functionality works) by calling the [**`test()`**](https://facebook.github.io/jest/docs/en/api.html#testname-fn) function&mdash;a predefined function provided by the Jest framework. This functions take two arguments: a _string_ describing what the test is checking, and a _callback function_ that will contain the code to run which does the action and compares the results.

```js
test('should do something...', function() {
  //regular old Javascript code that will perform the test goes here
});
```

- Test descriptions (the string parameter) are written in _present tense_, and state in plain English what behavior SHOULD happen. Starting the description with the word "should" is a good approach!

- Jest also provides a function `it()` that is an alias for `test()`, but lets the code read like badly punctuated English:

    ```js
    it('should do something', function() { ... });
    ```

### Assertions and Matchers
We check that the program actually does what we're testing by writing an **assertion**. An assertion is a _proposition_ that some fact is true. In this case, we are going to _assert_ that the expected result and the actual result are the same. If that proposition is shown to be valid (our claim that the results matched is true), then we know that our claim that the program works must hold and thus we "pass" the test!

- You can think of an _assertion_ as a bit like doing the work of an if-else statement:

    ```
    if(received value == expected value){
        test passes
    } else {
        test fails
    }
    ```

In Jest, we put forth an assertion by calling the [**`expect()`**](https://facebook.github.io/jest/docs/en/expect.html#expectvalue) function with an appropriate [**matcher**](https://facebook.github.io/jest/docs/en/using-matchers.html). The `expect()` function takes a single parameter, which is the _received result_ (produced by doing the action; e.g., calling the function with a particular input). A matcher is _another function_ that is called directly on the returned value of `expect()`, and takes as an argument the _expected result_ that we want to compare. The matcher does the work of actually comparing the values, and then reporting the validity of our claim back to Jest:

```js
test('should add numbers correctly', function() {
  expect(1+1).toEqual(2);
});
```

- In this case, `.toEqual()` is the "matcher" which compare the _received_ and _expected_ values to see if they are equal.

- Be careful about your parentheses! The `expect()` function should take a single expression (even if that expression includes a function call), and the matcher is called _after_ the `expect()` function. Using local variables can help with readability.

Jest supports a wide variety of matchers. For example:

```js
//can do numerical comparison
expect(receivedNumber).toBeGreaterThan(expectedNumber);

//compare against undefined
expect(receivedValue).toBeDefined(); //check if defined!

//find receivedValue in an array
expect(receivedArray).toContain(expectedValue);

//can negate ANY matcher with a .not property
expect(receivedValue).not.toEqual(expectedValue);
```

- See [the documentation](https://facebook.github.io/jest/docs/en/expect.html) for a complete list.

Note that a single `test()` can include multiple assertions!

### Organizing Tests
You can "group" tests together by using the [`describe()`](https://facebook.github.io/jest/docs/en/api.html#describename-fn) function. This function takes two parameters: a _string_ that describes what **feature** is being tested, and a _callback function_ that contains the code for the tests:

```js
describe('Basic math', function() {
  it('should add numbers correctly', function() {
    expect(1+1).toEqual(2);
  });
});
```

- You should name your feature and test features so that they read as:

    > {Feature name} should {do something specific}

    This will allow the test results to be communicated very clearly, even to non-developers (e.g., to your boss or client).

- `describe()` blocks can contain multiple `tests()`, and even other `describe()` blocks if you want to separate subfeatures.

It is also possible to run particular blocks of code _before_ groups of tests are run by using the `beforeEach()` and `beforeAll()` functions. See [Setup and Teardown](https://facebook.github.io/jest/docs/en/setup-teardown.html) for details.


### Running Tests
You can **run** the test (have the computer perform the testing work) by using the `jest` command-line program, passing it the name of the text script (without the extension) that you wish to run:

```bash
# test the app.spec.js file
jest app
```

The command-line will print out the results of this test script:

![An example passing script](img/pass.png)

This will tell you what tests were run, which "passed" (were green for good to _go_!) and which "failed" (were red). Failed tests will also report which assertion failed, and what the expected and received values were that didn't match.

### Practice
This repo's `app.js` file contains a function `invertCase()`. Create a `describe()` block to contains tests for the function, and write unit tests to check it for bugs (by giving it specific inputs and checking the _received_ vs. the _expected_ result). If you find any bugs, fix them and then re-run your tests!

## Testing Web Apps with Jest
It is also possible to use Jest to JavaScript code that is used to manipulate the DOM. You can do this because Jest creates a **virtual DOM** that you can interact with. That is, jest provides a global `document` object that you can access and call methods on (e.g., `document.querySelector()`). This isn't a full browser: it won't load external CSS files or allow you to navigate to pages, but it does provide a tree of HTML elements you can modify and inspect. allowing you to test your DOM manipulation.

There are a few steps to being able to work effectively with the `document` provided by Jest:

1. First, you will want to make sure the content of the DOM includes the HTML elements you wish to test with. This is called **mounting** the content. You can mount some HTML content by assigning it to the `innerHTML` of the DOM's _root element_&mdash;not the `<html>` element, but a virtual element that acts as the ultimate parent of the DOM (similar to the root `/` folder on an operating system). This node can be accessed via the `document.documentElement` property:

    ```js
    //assign a given HTML content (e.g., as string) to the virtual DOM
    document.documentElement.innerHTML = "<html><head></head><body>...</body></html>";
    ```

    Often, this HTML content is read directly from the file, using Node's `fs` (file system) library.

2. Because the `document` object only represents the DOM tree (the rendered HTML), it won't apply any embedded `<script>` tags. So in order to run your script on the DOM and modify it, you will need to _manually_ apply those scripts. But since Jest has already defined you a `document` object to modify, you can simply tell Jest to load and run your script! Your code will do the exact same thing it does in the browser, just querying and modifying Jest's virtual DOM (`document`) rather than the browser's DOM.

    You load an external script in Jest by using Node's `require()` function, passing it the _relative path_ to the script file you wish to load (this script must be saved locally):

    ```js
    //load the `index.js` file
    require('../js/index.js');
    ```

    Note that this path is _relative_ to the location of the test script, not relative to the location of the `.html` file (since Jest doesn't use the HTML). If the script file is in the same directory as the spec file, you will need to put a `./` in front of the filename path to indicate that you're giving a path rather than naming a module.

    You will need to also explicitly have Jest load any external libraries (such as jQuery) you wish to use. These libraries will need to be loaded from a local file (Jest can't access a CDN), but they can usually be installed via `npm`. For example, you would load jQuery with:

    ```js
    $ = require('jquery'); //load the jQuery module (installed from npm)
    window.$ = $; //assign make the jQuery library into a DOM global
    ```

    _Remember to load any external scripts **before** your own!_

3. Finally, you can use DOM methods (or jQuery helpers!) to trigger _events_ (like button clicks). You can then inspect the DOM with `document.querySelector()` and run assertions about the state of DOM after that user action.

    ```js
    //for example
    let h1 = document.querySelector('h1');
    expert(h1.textContent).toEqual('Hello world!');
    ```

And with that, you can automatically test if your page's interactivity works as expected without needing to repeatedly click on a button. In particular, these automatic tests can help you make sure that future changes don't _break_ your code (e.g., don't cause the tests to fail), performing what is called [**regression testing**](https://en.wikipedia.org/wiki/Regression_testing).

- You should still test your page inside the browser, just to catch any platform differences between Jest's virtual DOM and actual web browsers.

### Practice
Add functionality to the `index.js` file so that when the "Panic" button is pressed, the HTML's `.alert` element is displayed. Then write a test in `index.spec.js` to confirm that this behavior works.
