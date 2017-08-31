Traversing Nested Objects
---

## Objectives

1. Explain what a nested object looks like
2. Explain why nested objects are useful
3. Describe how to access inner properties
4. Find an element in a nested array

## Introduction

When we're looking for occurrences of a word or concept in a book, we often turn to the index. The index tells us where we can find more information on that concept — instead of, like a dictionary, giving us a definition, it gives us a _list_ that we can use to look up information. Additionally, it might include information that is related to the heading that we looked up in a _sublist_. We map the connections between these lists in our heads, and it doesn't cause any issues to think of one list containing other lists. (The index itself is, after all, a kind of list.)

## Objects in Objects

Remember when we said that the values in an object can be _anything_? Well, like the lists in the index in the example above, the values in an object **can also be other objects**.

![mind blown](http://i.giphy.com/5aLrlDiJPMPFS.gif)

Type (don't just copy!) the following into your console to see what we mean:

``` javascript
const person = {
  name: "Awesome Name",
  occupation: {
    title: "Senior Manager of Awesome",
    yearsHeld: 2
  },
  pets: [{
    kind: "dog",
    name: "Fiona"
  }, {
    kind: "cat",
    name: "Ralph"
  }]
}
```

If you look closely, you can see that we've kind of seen this before, when we looped over an array containing objects. So it's not _that_ scary!

How would you imagine we'd access the `yearsHeld` field? If we try `person.yearsHeld`, we get a big fat `undefined`. But we can see that `yearsHeld` is a property of `occupation`, which in turn is a property of `person`. So we could try `occupation.yearsHeld`, but that'll throw an error because `occupation` is not defined globally, only as a property of `person`. Hey, maybe there's a clue! What if we try `person.occupation`? We should see something like

``` javascript
{ title: "Senior Manager of Awesome", yearsHeld: 2 }
```

printed to console. Nice! So that suggests that if we do `person.occupation.yearsHeld` —

``` javascript
2
```

Sweet!

## Arrays in Arrays

We're going to get more abstract bit by bit. In the above example, we had a name for each field that we wanted to access (`person`, `occupation`, and `yearsHeld`). If we had wanted to access the second pet's name, we could have done `person.pets[1].name` — notice that we need to specify the index in the `pets` array of the pet that we want.

Working with arrays isn't all that different. It's just that instead of named properties of objects (and sub-objects), we have indexes of arrays (and sub-arrays). And, of course, JavaScript is flexible enough that we can mix the two:

``` javascript
const collections = [1, [2, [4, [5, [6]], 3]]]
```

So, given the above nested array, how would we get the number `6`? First, we'd need the second element of `collections`, `collections[1]`. Then we'd need the second element of that element, so `collections[1][1]`; then the second element of _that_ element, so `collections[1][1][1]`; then again, so `collections[1][1][1][1]`; and finally, the first element of that element, `collections[1][1][1][1][0]`.

That's a lot to keep track of. Just remember that each lookup (square brackets) effectively brings a different array to the fore for each subsequent lookup. So what we're really doing is

``` javascript
[1, [2, [4, [5, [6]], 3]]] // collections
[2, [4, [5, [6]], 3]]      // collections[1]
[4, [5, [6]], 3]           // collections[1][1]
[5, [6]]                   // collections[1][1][1]
[6]                        // collections[1][1][1][1]
6                          // collections[1][1][1][1][0]
```

## "Use the `for`ce, Luke!"

What if we have criteria for finding an element that we know is in a nested data structure? Let's implement a simple `find` function that takes two arguments: an array (which can contain sub-arrays) and a function that returns `true` for the thing that we're looking for.

``` javascript
function find(array, criteriaFn) {
  for (let i = 0; i < array.length; i++) {
    if (criteriaFn(array[i])) {
      return array[i]
    }
  }
}
```

The above will work for a flat array — but what if `array` is like `collections` and we want to find the first element that's `> 5`? We'll need some way to move down the levels of the array (like we described above).

Follow along with the code below — we know it's a little tricky, but be sure to read the comments!

``` javascript
function find(array, criteriaFn) {
  // initialize two variables, `current`, and `next`
  // `current` keeps track of the element that we're
  // currently on, just like we did when unpacking the
  // array above; `next` is itself an array that keeps
  // track of the elements (which might be arrays!) that
  // we haven't looked at yet
  let current = array
  let next = []

  // hey, a `while` loop! this loop will only
  // trigger if `current` is truthy — so when
  // we exhaust `next` and, below, attempt to
  // `shift()` `undefined` (when `next` is empty)
  // onto `current`, we'll exit the loop
  while (current) {
    // if `current` satisfies the `criteriaFn`, then
    // return it — recall that `return` will exit the
    // entire function!
    if (criteriaFn(current)) {
      return current
    }

    // if `current` is an array, we want to push all of
    // its elements (which might be arrays) onto `next`
    if (Array.isArray(current)) {
      for (let i = 0; i < current.length; i++) {
        next.push(current[i])
      }
    }

    // after pushing any children (if there
    // are any) of `current` onto `next`, we want to take
    // the first element of `next` and make it the
    // new `current` for the next pass of the `while`
    // loop
    current = next.shift()
  }

  // if we haven't
  return null
}
```

Type the code (you can exclude the comments) above into your console and run it a few times. Try it with `collections` and the function `n => n > 5` — does it return the result you'd expect? What about if we try the function `n => (typeof n === 'number' && n > 5)`?

Without knowing it, you've just implemented your first **[breadth-first search](https://en.wikipedia.org/wiki/Breadth-first_search)**! Congratulations!

Breadth-first search is one of the main algorithms (that's right, you've conquered an algorithm) used to search through nested objects. It earned its name because it looks at the siblings of an object (the elements that are on the same level) before looking at the children (the elements that are one or more levels down).

## A challenge, should you choose to accept it

Can you modify the breadth-first search algorithm in such a way that it will traverse both nested objects and nested arrays (or even — gasp! — a mix of both)?

## Resources

- [breadth-first search](https://en.wikipedia.org/wiki/Breadth-first_search)

<p class='util--hide'>View <a href='https://learn.co/lessons/traversing-nested-objects'>Traversing Nested Objects</a> on Learn.co and start learning to code for free.</p>
