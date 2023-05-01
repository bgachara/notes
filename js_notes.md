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

## From Browser Implementations

- Always initialise your objects in the same way, so they dont end up having different shapes.
- Don't mess with property attributes of array elements, so they can be stored and operated on efficiently.