# Notes on Javascript

`Colt Steele: Modern Javascript`
`Javascript tips from all over`


# Introduction

- ECMAScript, TC39
- Specification not implementation
- Browser compatibility - "Can I use" website


## Primitive Types

- Number, String, Boolean, Null and Undefined.
- typeof - check type of variable.
- String
  - strings are immutable.
  - indexed, zero indexed.
  - has a property called length.
  - string methods, .toUppercase(), .trim()
  - an chain methods, .trim().toUppercase()
  - some methods accept arguments, item.slice(), item.indexOf("args"), item.replace()

- Escape characters
- Template Literals use `You owe me ${9+0}`
- Null - intentional absence of value.
- Undefined - not intentional but absent still.
- Math object: math.random(), math.round(9.8), math.pow(), math.floor().
- parseInt(), parseFloat()
- NaN
 
## Variables

- store a value and give it a name.
- *let* 
- *const* - can't change value once declared.
- cannot redclare once declared.
- *var* - old way to declare.

```js

let hens = 72;

hens = heans + 1;

//also use unary operator
hens++;

//will not work due to redeclaration.
let hens = true;

//check lenght of string type
let checkmate = "Hii imeenda!";

checkmate.length

//positional access
checkmate[9]
 
const maigpies = 90;

```


## Program Logic

- Comparisons: 
 - { >, <, >=, <=, ==, !=} get boolean result.
 - Unicode characters.
 - ==, equality without caring about type of values.
 - *===, checks equality of value and type.

- Conditionals
 - if (sth here){this happens};
 - if (first condition), else if (other conditions){other thing happens}
 - else (when nothing else matches)
 - Nesting: one can nest conditionals.
 - Switching
 - Ternary operator.

- Truthy and Falsy value
 - all values ahave an inherent truthy or falsy boolean value.
 - falsy values: false, 0, empty string " ", null, undefined, NaN.

- Logical operators
 - AND: && : both sides must be true.
 - OR: || : either must be true. 
 - NOT: ! : opposite
 - operator precedence: NOT, AND, OR
 

````js

let rating = 2;

if (rating === 3) {
 console.log('You are a superstar!!');
}
else if (rating === 2) {
 console.log('Meets expectations!!');
}
//if nothing above ran
else {
 console.log('We tried though right!')
}

//switching

let day = 4;

switch (day) {
 case 1: 
   console.log("Monday");
   break;
 case 2: 
   console.log("Tuesday");
   break;
 case 3: 
   console.log("Wednesday");
   break;
 default:
   console.log("Try again!")

//ternary operator
condition ? expIfTrue: expIfFalse

```` 


## Arrays

- ordered collection of values
- also indexed like strings, 
- are mutable.
- can nest arrays in arrays

### Array Methods

- array.push(sth here). //add to end. mutates the array
- array.pop() //removes last item and returns it to you, mutates too
- array.unshift() //add to the beginning
- array.shift() //removes first el. returns it.
- array.concat(arr2) //merge arrays
- array.includes(item) //returns boolean
- array.indexOf() //returns index, -1 if not found
- array.join() // returns a single string of el. can join with . , #
- array.reverse() //reverse and array inplace
- array.slice(2,5) //returns part of array as specified. exclude upper limit
- array.splice(start, deletecount, replacement) //remove, replace array el's
- array.sort() //inplace sort by utf-8 string codes, pass compare functions


### Reference types

- Value type variable
 - strings

- Reference type variable 
 - arrays
 - const declaration can change contents not reference
 - objects

// compare a current assignment on new assignment trial???


````js
 
let shoppingList = ['cereal', 'mukimo', 'chocolate'];


//indexed
shoppingList[1]

//mutable
shoppingList[1] = "pumpkin";
````

## Objects

- collections of properties.   
- properties are a key-value pair.
- access data using keys rather than index
- {} - object literal
- nest objects too like arrays
- object equality, tied to reference location, hence not equal.


````js

const fitBitData = {
 totalMiles: 90,
 location: 'POPO',
 time: '3.3'
}

//access data out of object.
fitBitData.time

fitBitData['time']

//can load objects using same syntax

fitBitData['lunar'] = 'leo';

````

## Loops

- doing things repeatedly
- for(init: condition to run: operation: ){sth}
- 

````js

for (let i = 1; i<=10; i++ ){
 console.log('Hello', i)

}

````

- Loop over array, string using length as the index.
- Can nest loops 

- While loop
- ideal for checking against a certain condition.

```js

let j = 0;
while (j < 10) {
 console.log(j)

}

```

- for..of
- cleaner version of for loop
- Objects are ot iterable by default so need to access the Object.keys(objectname) to make it iterable.

- for..in
- loop over properties of an object

## Functions

- reusable procedures, modularise code.
- function statement
- can nest functions inside other functions

```js

function fname(){
 

}
fname()

const fname = function(){
 

}
```
- arguments vs parameters
- return statement, functions do not automatically have a return  value unless explicitly declared as a return value.
- return statement halt execution of a function.

- Function scope
- let/const and var have different scoping rules.
- lexical scope
- function expressions since functions are objects too.
- Higher order functions
 - functions that operate on other functions, accept functions as arguments and also return values
- Callback functions
 - function passed into another function as an argument, which is then invoked inside the outer function.
 - used commonly with anonymous functions
- Hoisting
 - let/const(does not) vs var(does) behaviour.
 - function declarations are hoisted vs function expressions are not.

### Array Callback Functions

- For...each
  - passed function is called for each element in the array.
- Map
  - creates a new array with the results of calling a callback on every element in the array  
- Find
  - finds and retrieves value of first result that matches provided testing function
- Filter
  - creates a new array with all elements that pass test implemented by passed function.
- Every
  - tests whether all elements in array pass the provided function, returns boolean value.
- Some
  - tests whethe any of the elements in array passes the provided function, also returns boolean.
- Sort
  - pass in a compare function,comp(a,b) result compared to 0
  - mutates array in place.
- Reduce
  - executes a reducer function on each element of the array
  - array.reduce((accumulator, currentValue) => {sum, max_value, min_value, }, init value)
  - can use an object as an initial value.

```js
//arrow functions

const arrow = (y) => {
  return y*y;
}

const arrow = y => y*y;

```


### More Js features

- Default values
  - function(a, b=1)
  - should come at the end.
- Spread
  - allows an iterable to be expanded in places where zero or more arguments or elements are expected.
  - used in function calls, array literals, object literals.
  - max(...num)
- Rest
  - same syntax as spread but different use.
  - collects all remaining arguments of the function call.
  - not an array-like object but a real array.
  - should be the last parameter, and can be used with arrow functions.
- Destructuring
  - capture elements from an iterable/compound element.
  - const [name, age, group] = data
  - array, objects, function definition
  
### Objects

- Shorthand syntax of naming variables as their result type.
- Computed properties
  - syntax: [user]: 'Admin'
- Methods
  - can add functions as properties on objects, this are called methods
  - dont have to add name to the key:value pair of object.
- Keyword THIS.
  - object reference to current execution scope.
  - variable declaration with var added to global window scope but not let and const.
  - value of this depends on invocation context of the function it is used in.
  - arrow functions don't get their own execution context
  - leftside.function_call()....leftside = context of this.
  - arrow functions get this context from the context right above.
  

## Asynchronous code

- Call Stack
- Js is single threaded.
- Browser via Cpp does the setInterval kind of operations.
- Callback Hell
- Promises
  - object representing the eventual completion or failure of an asynchronous operation.
  - creating promises
    - new Promise(resolve, reject) => {};
    - .then() is called when the promise resolves.
    - .catch() is called when the promise is rejected.
    - in-between those states the promise status is pending.
    - can create a function that returns a promise
    - can have data in resolve(data) that can be accessed on resolve/rejection
    - chaining .then() enabled if the first function returns a promise.
  - consuming promises
  
## Sending Requests

- AJAX
- XML
- JSON
  - data format
- XmlHttpRequest
- Fetch
  - promise based.
  - content of response is a readablestream.
  - only reject on network failure but never on status code i.e 404, 500
  - manually throw an error to activate .catch()
  
## Async Functions

- async functions always return a promise
  - promise resolves with the return value, else rejected i.e when exception thrown
- await keyword used inside the async function
- can use a try and catch block over it to catch errors or chain .catch() on the function call itself
- sequential vs parallel asyncs and awaits
- in parallel execution, use Promise.All() to resolve multiple promises.


### OOP Js

- prototypes are template objects which are shared among structures of the same type, used to share methods
  - array.prototype, string.prototype, object.prototype
- Factory functions
  - function build an object then returns it at the end
- Constructor function
  - define a function.
  - use the new keyword
    - creates a blank, plain javascript object
    - links (sets constructor of) this object to another object
    - passes newly created object from step 1 as the this context
    - retruns this if the functions does not return its own object
  - access the prototype object and add methods on it, dont use arrow functions
- Class syntax
  - check in code sample
  - simpler version implementation of constructor function
  - use of keyword `extends`
  - use also of super() keyword to re-use constructor function of extended class.


```js

async function make() {
  //this is a promise returning function
}

//class definition

class Car {
  constructor(args here){
    
  }
  
  greet(){
    
  }
  
  cook(){
    
  }
}

```


## From Browser Implementations

- Always initialise your objects in the same way, so they dont end up having different shapes.
- Don't mess with property attributes of array elements, so they can be stored and operated on efficiently.



## Javascript Algorithms
ref:`Frontend Masters`

- Space(memory) and Time(primitive operations) complexity
- WRT input size
- constant(O(1)) -> logarithmic(O(logn)) -> linear(O(n)) -> Quadratic(O(n^2)) -> exponential(O(k^n))
- drop the constants.
- caching vs memoization 
- memoization is saving the result of function call.
- memoization with closures, privatise the cache object inside the function.

## Recursion

- when a function calls itself
- identify base case
- identify the recursive case
- return where appropriate
- turn loops into recursion and vice versa
- formats
  - wrapper functions
  - accumulator - result is passed down into each recursion.
- closure is a function that is called inside another function.

## Divide and Conquer

- Binary Search
  - cut in half
  - sorted
- Linear search
  - time complexity is linear
- Comparison sorts
  - Naive sorts
    - keep looping and comparing values until the list is sorted.
      - Bubble sort
        - compare adjacent items and swapping till sorted
      - Insertion sort
      - Selection sort
  - Divide and Conquer sort
    - recursively divide lists and sort smaller parts of the list until entire list is sorted
      - Mergesort
        - recusively merge sorted sub-lists
      - Quicksort

### Greedy Algorithms

- making the locally optimal choice.

### Brute Force 

- alternate to greedy where you go through all the combination of possible solutions

### Dynamic Programming

- cache values to avoid repeated calculations.
- top-down recursive technique
- bottom-up iterative technique
- memoised called are constant time look-up.
- correctly identify the subproblems, memoize the results, if need to solve again, look up the results.
- subproblem dependencies should be acyclic otherwise one gets infinite algorithms. 
- when cyclic, make graph acyclic, this however increases number of subproblems.
- time = # of problems + time per problem
- you can incorporate space complexity by deleting all but last 2 when computing another one.
- Shortest paths problem: guessing technique
- 5 general steps in DP
  - Define subproblems, count them too
  - Guess(part of the solution), # choices for guesses
  - Relate subproblems solutions, time/subproblem
  - Recurse and Memoize or build DP table bottom-up, check subprob recurrence is acyclic, has topological order.
  - Solve original problem, combining may take some time.
- Look into text justification problem.
- Parent pointers: remember what guess was the best