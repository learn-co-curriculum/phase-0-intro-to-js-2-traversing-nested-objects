# Traversing Nested Objects

## Objectives
1. Explain why nested objects are useful.
2. Describe how to access inner properties.
3. Use recursion to iterate over nested objects and arrays.
4. Deploy the `debugger` statement to assist in debugging code.

## Introduction
Here at Flatbook, we have some pretty complex data-modeling needs. For instance, think about the breadth of information we might want to display on each user's profile page:
- First name
- Last name
- Employer
  + Company name
  + Job title
- Friends
  + First name
  + Last name
  + Employer
    * Company name
    * Job title
- Projects
  + Title
  + Description

We can already start to see some problems with trying to fit all of this into a _shallow_ (non-nested) JavaScript object:
```js
const userInfo = {
  firstName: 'Avi',
  lastName: 'Flombaum',
  companyName: 'Flatbook Labs',
  jobTitle: 'Developer Apprentice',
  friend1firstName: 'Joe',
  friend1lastName: 'Burgess',
  friend1companyName: 'Flatbook Labs',
  friend1jobTitle: 'Developer Apprentice',
  friend2firstName: 'Gabe',
  friend2lastName: 'Jackson',
  friend2companyName: 'Flatbook Labs',
  friend2jobTitle: 'Senior Developer',
  project1title: 'Flatbook',
  project1description: 'The premier Flatiron School-based social network in the world.',
  project2title: 'Scuber',
  project2description: 'A burgeoning startup helping busy parents transport their children to and from all of their activities on scooters.'
};
```

Goodness, that's messy. It would be a nightmare to keep the object updated. If Avi un-friends Joe, do we shift Gabe's info into the `friend1...` slots and delete the `friend2...` properties, or do we leave Gabe as `friend2...` and delete the `friend1...` properties? There are no good answers. Except...

## Objects in objects
Remember when we said that the values in an object can be _anything_? We've hinted at this a bit already, but the properties in an object **can point to other objects**.

![Mind blown.](http://i.giphy.com/5aLrlDiJPMPFS.gif)

If we reorganize the above object a bit, it becomes infinitely easier to read and update:
```js
const userInfo = {
  firstName: 'Avi',
  lastName: 'Flombaum',
  company: {
    name: 'Flatbook Labs',
    jobTitle: 'Developer Apprentice'
  },
  friends: [{
    firstName: 'Joe',
    lastName: 'Burgess',
    company: {
      name: 'Flatbook Labs',
      jobTitle: 'Developer Apprentice'
    }
  },
  {
    firstName: 'Gabe',
    lastName: 'Jackson',
    company: {
      name: 'Flatbook Labs',
      jobTitle: 'Lead Developer'
    }
  }],
  projects: [{
    title: 'Flatbook',
    description: 'The premier Flatiron School-based social network in the world.'
  },
  {
    title: 'Scuber',
    description: 'A burgeoning startup helping busy parents transport their children to and from all of their activities on scooters.'
  }]
};
```

We've pared the sixteen messy properties in our earlier first attempt down to a svelte five: `firstName`, `lastName`, `company`, `friends`, and `projects`. `company` points at another object, and both `friends` and `projects` are arrays of objects. Let's practice accessing some of those beautifully nested data points.

To grab Avi's last name:
```js
userInfo.lastName;
// => "Flombaum"
```

For the first name of his first friend:
```js
userInfo.friends[0].firstName;
// => "Joe"
```

For the title of his second project:
```js
userInfo.projects[1].title;
// => "Scuber"
```

Create your own nested data structure in the JS console and practice accessing various pieces of data.

## Arrays in arrays
In the above example, we had a name for each field that we wanted to access (`firstName`, `company`, `jobTitle`, and so on). For example, to access the name of Avi's second friend, we could use `userInfo.friends[1].firstName`. Notice that we need to specify the index in the `friends` array for the friend that we want.

Working with nested arrays isn't all that different from nested objects. Simply replace the named properties of nested objects with indexes of nested arrays. Perhaps an example to clear things up:
```js
const letters = ['a', ['b', ['c', ['d', ['e']], 'f']]];
```

Given the above nested array, how would we get the letter `'e'`? First, we'd need the second element in `letters`, `letters[1]`:
```js
letters[1];
// => ["b", ["c", ["d", ["e"]], "f"]]
```

Then we'd need the second element of that element, so `letters[1][1]`:
```js
letters[1][1];
// => ["c", ["d", ["e"]], "f"]
```

Then the second element of **that** element, `letters[1][1][1]`:
```js
letters[1][1][1];
// => ["d", ["e"]]
```

And the second element of ***that*** element, `letters[1][1][1][1]`:
```js
letters[1][1][1][1];
// => ["e"]
```

Finally, we want the first element in that final nested array, `letters[1][1][1][1][0]`:
```js
letters[1][1][1][1][0];
// => "e"
```

Whew! That's a lot to keep track of. Just remember that each lookup (each set of square brackets) effectively brings a different array to the fore. To recap:
```js
["a", ["b", ["c", ["d", ["e"]], "f"]]] // letters
["b", ["c", ["d", ["e"]], "f"]]        // letters[1]
["c", ["d", ["e"]], "f"]               // letters[1][1]
["d", ["e"]]                           // letters[1][1][1]
["e"]                                  // letters[1][1][1][1]
"e"                                    // letters[1][1][1][1][0]
```

## Iterating over nested objects and arrays
Our initial shallow object had a lot of drawbacks, but one advantage is that it was very easy to iterate over all of the information:
```js
const userInfo = {
  firstName: 'Avi',
  lastName: 'Flombaum',
  companyName: 'Flatbook Labs',
  jobTitle: 'Developer Apprentice',
  friend1firstName: 'Joe',
  friend1lastName: 'Burgess',
  friend1companyName: 'Flatbook Labs',
  friend1jobTitle: 'Developer Apprentice',
  friend2firstName: 'Gabe',
  friend2lastName: 'Jackson',
  friend2companyName: 'Flatbook Labs',
  friend2jobTitle: 'Senior Developer',
  project1title: 'Flatbook',
  project1description: 'The premier Flatiron School-based social network in the world.',
  project2title: 'Scuber',
  project2description: 'A burgeoning startup helping busy parents transport their children to and from all of their activities on scooters.'
};

function shallowIterator (target) {
  for (const key in target) {
    console.log(target[key]);
  }
}

shallowIterator(userInfo);
// LOG: Avi
// LOG: Flombaum
// LOG: Flatbook Labs
// LOG: Developer Apprentice
// LOG: Joe
// LOG: Burgess
// LOG: Flatbook Labs
// LOG: Developer Apprentice
// LOG: Gabe
// LOG: Jackson
// LOG: Flatbook Labs
// LOG: Senior Developer
// LOG: Flatbook
// LOG: The premier Flatiron School-based social network in the world.
// LOG: Scuber
// LOG: A burgeoning startup helping busy parents transport their children to and from all of their activities on scooters.
```

It also works with arrays:
```js
const primes = [2, 3, 5, 7, 11];

shallowIterator(primes);
// LOG: 2
// LOG: 3
// LOG: 5
// LOG: 7
// LOG: 11
```

However, our `shallowIterator()` function can't handle nested collections:
```js
const numbers = [1, [2, [4, [5, [6]], 3]]];

shallowIterator(numbers);
// LOG: 1
// LOG: [2, [4, [5, [6]], 3]]
```

It's trained to iterate over the passed-in array's elements or object's properties, but our function has no concept of _depth_. When it tries to iterate over the above nested `numbers` array, it sees only two elements at the top level of the array: the number `1` and **another** array, `[2, [4, [5, [6]], 3]]`. It `console.log()`s out both of those elements and calls it a day, never realizing that we also want it to print out the elements inside the nested array. Let's modify our function so that if it encounters a nested object or array, it will additionally print out all of the data contained therein:
```js
function shallowIterator (target) {
  for (const key in target) {
    if (typeof target[key] === 'object') {
      for (const nestedKey in target[key]) {
        console.log(target[key][nestedKey]);
      }
    } else {
      console.log(target[key]);
    }
  }
}

shallowIterator(numbers);
// LOG: 1
// LOG: 2
// LOG: [4, [5, [6]], 3]
```

Now we've gone two levels deep, which gets us a bit closer to our goal. However, there are two pretty clear drawbacks to this strategy:
1. We'll have to add a new `for...in` statement for every nested data structure we want to traverse, quickly ballooning our function out to an unmanageable size.
2. Since we need to add a separate `for...in` statement for each additional nested data structure, we'll have to know exactly what the target structure looks like ahead of time and update our function accordingly. That's a lot of repetitive, error-prone work!

![No! There has to be another way.](https://curriculum-content.s3.amazonaws.com/web-development/js/looping-and-iteration/traversing-nested-objects-readme/no_there_has_to_be_another_way.gif)

### Recursion
Lucky for us, there **is** another way: recursion. It's one of the more powerful concepts in programming, but it's also pretty hard to grasp at first. **Don't sweat it if it doesn't click immediately**. We'll introduce the concept here but come back to it periodically throughout the rest of the JavaScript material. Essentially, **a recursive function is a function that calls itself**.

Whoa, that sounds intense. Let's take a look at a better way to write our `shallowIterator()` to take advantage of recursion:
```js
function deepIterator (target) {
  if (typeof target === 'object') {
    for (const key in target) {
      deepIterator(target[key]);
    }
  } else {
    console.log(target);
  }
}
```

When we invoke `deepIterator()` with an argument, the function first checks if the argument is an object or array (recall that the `typeof` operator returns `"object"` for arrays as well). If the argument **isn't** an object, `deepIterator()` simply `console.log()`s out the argument and exits. However, if the argument **is** an object, we iterate over the properties (or elements) in the object, passing each to `deepIterator()` and **re-invoking the function**. That's recursion!

Let's see it in action:
```js
const numbers = [1, [2, [4, [5, [6]], 3]]];

deepIterator(numbers);
// LOG: 1
// LOG: 2
// LOG: 4
// LOG: 5
// LOG: 6
// LOG: 3
```

It also works with combinations of nested objects and arrays:
```js
const userInfo = {
  firstName: 'Avi',
  lastName: 'Flombaum',
  company: {
    name: 'Flatbook Labs',
    jobTitle: 'Developer Apprentice'
  },
  friends: [{
    firstName: 'Joe',
    lastName: 'Burgess',
    company: {
      name: 'Flatbook Labs',
      jobTitle: 'Developer Apprentice'
    }
  },
  {
    firstName: 'Gabe',
    lastName: 'Jackson',
    company: {
      name: 'Flatbook Labs',
      jobTitle: 'Lead Developer'
    }
  }],
  projects: [{
    title: 'Flatbook',
    description: 'The premier Flatiron School-based social network in the world.'
  },
  {
    title: 'Scuber',
    description: 'A burgeoning startup helping busy parents transport their children to and from all of their activities on scooters.'
  }]
};

deepIterator(userInfo);
// LOG: Avi
// LOG: Flombaum
// LOG: Flatbook Labs
// LOG: Developer Apprentice
// LOG: Joe
// LOG: Burgess
// LOG: Flatbook Labs
// LOG: Developer Apprentice
// LOG: Gabe
// LOG: Jackson
// LOG: Flatbook Labs
// LOG: Lead Developer
// LOG: Flatbook
// LOG: The premier Flatiron School-based social network in the world.
// LOG: Scuber
// LOG: A burgeoning startup helping busy parents transport their children to and from all of their activities on scooters.
```

To keep track of how many times our function is recursively invoking itself, it might be helpful to use a counter variable:
```js
let counter = 0;

function deepIterator (target) {
  counter++;

  if (typeof target === 'object') {
    for (const key in target) {
      deepIterator(target[key]);
    }
  } else {
    console.log(target);
  }
}

deepIterator(userInfo);
// LOG: Avi
// LOG: Flombaum
// LOG: Flatbook Labs
// LOG: Developer Apprentice
// LOG: Joe
// LOG: Burgess
// LOG: Flatbook Labs
// LOG: Developer Apprentice
// LOG: Gabe
// LOG: Jackson
// LOG: Flatbook Labs
// LOG: Lead Developer
// LOG: Flatbook
// LOG: The premier Flatiron School-based social network in the world.
// LOG: Scuber
// LOG: A burgeoning startup helping busy parents transport their children to and from all of their activities on scooters.

counter;
// => 26
```

So we invoked `deepIterator()` once, and it invoked itself 25 additional times! If we look closely at our nested `userInfo` object, we can see that it contains two arrays, seven nested objects, and sixteen key-value pairs where the value is a string. Add those all up (2 + 7 + 16), and you get our 25 recursive invocations!

## `debugger`
Up to this point, we've been using `console.log()` for most of our debugging needs. There's nothing wrong with that strategy — it's often preferable to more complex options — but it's important to familiarize ourselves with a popular alternative: the `debugger` keyword.

`debugger` is JavaScript's built-in debugging solution, and it allows us to stop our code mid-execution and poke around a bit. We can check on the current contents of a variable or see whether a function is available within the current scope. To get started, all you have to do is drop a `debugger` at the point(s) in your code at which you'd like to pause:
```js
function shallowIterator (target) {
  debugger;

  for (const key in target) {
    debugger;

    console.log(target[key]);
  }
}
```

Here we've placed two debuggers in our `shallowIterator()` function (you can use as many as you'd like). Let's run the code in our browser's JS console and see what happens:
```js
const primes = [2, 3, 5, 7, 11];

shallowIterator(primes);
```

As soon as the JavaScript engine hits the first `debugger` statement, it pauses the execution of our code and we'll see this yellow banner pop up in the main browser window:

![Paused in debugger](https://curriculum-content.s3.amazonaws.com/web-development/js/looping-and-iteration/traversing-nested-objects-readme/paused_in_debugger.png)

Encountering a `debugger` typically pops the browser's JS console open, but, if it's still closed at this point, go ahead and open it. You should see something like this:

![Initial `debugger` screen](https://curriculum-content.s3.amazonaws.com/web-development/js/looping-and-iteration/traversing-nested-objects-readme/first_debugger.png)

On line 2 in the upper-left-hand corner, the `debugger` statement currently pausing execution is highlighted. In the upper-right-hand corner, we can see the **Call Stack**, which is the list of currently active, nested execution contexts. `shallowIterator` is the execution context created by our function, and `(anonymous)` is the global execution context. Under the `Scope` drop-down, we see a few different scope categories. The most salient for us is the `Local` scope, in which we can see that our `target` parameter took on the value of the `primes` array we passed into the function. Don't worry about `this` for now — we'll cover that soon!

While the execution is paused, we can hover our mouse over identifiers (function and variable names, including function parameters) to check their current value:

![Inspecting the `target` function parameter](https://curriculum-content.s3.amazonaws.com/web-development/js/looping-and-iteration/traversing-nested-objects-readme/inspecting_parameter.png)

We can also use the console to perform those same checks:

<picture>
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/looping-and-iteration/traversing-nested-objects-readme/checking_value_of_target.webp" type="image/webp">
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/looping-and-iteration/traversing-nested-objects-readme/checking_value_of_target.gif" type="image/gif">
  <img src="https://curriculum-content.s3.amazonaws.com/web-development/js/looping-and-iteration/traversing-nested-objects-readme/checking_value_of_target.gif" alt="Checking the value of `target` in the JS console.">
</picture>

Once you're done poking around at the current, stopped state of your code, go ahead and press the **Resume script execution** button, which will resume execution:

<picture>
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/looping-and-iteration/traversing-nested-objects-readme/devtools.webp" type="image/webp">
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/looping-and-iteration/traversing-nested-objects-readme/devtools.gif" type="image/gif">
  <img src="https://curriculum-content.s3.amazonaws.com/web-development/js/looping-and-iteration/traversing-nested-objects-readme/devtools.gif" alt="Options for resuming / controlling script execution in the debugger tools.">
</picture>

However, the JavaScript engine doesn't make it very far before encountering our second `debugger` keyword:

![Inspecting values after the second `debugger` is hit.](https://curriculum-content.s3.amazonaws.com/web-development/js/looping-and-iteration/traversing-nested-objects-readme/inspecting_values_in_second_debugger.png)

Continue stepping through the `debugger` statements, inspecting variables and playing around with the interface. Note that putting a `debugger` in a loop or iteration, such as our `for...in`, causes it to trigger on every pass through the loop.

## Conclusion
This is very advanced stuff, and you should absolutely not get discouraged if it doesn't click at first. Create some other nested data structures and traverse over them with `shallowIterator()` and `deepIterator()`, noting the limitations of the former. Throw some `debugger` statements into `deepIterator()` to slow execution down and get a handle on what's happening at each step of the process.

You got this!

## Resources
* [MDN: Recursion (JavaScript)](https://docs.microsoft.com/en-us/scripting/javascript/advanced/recursion-javascript)
* [freeCodeCamp: Recursion in JavaScript](https://medium.freecodecamp.org/recursion-in-javascript-1608032c7a1f)
* [JavaScript.info: Debugging in Chrome](https://javascript.info/debugging-chrome)

<p class='util--hide'>View <a href='https://learn.co/lessons/js-looping-and-iteration-traversing-nested-objects-readme'>Traversing Nested Objects</a> on Learn.co and start learning to code for free.</p>
