### Destructuring 

- Destructuring assignment is a special syntax that allows us to “unpack” arrays or objects into a bunch of variables, as sometimes that’s more convenient.

- Destructuring also works great with complex functions that have a lot of parameters, default values

- Array “destructurizes” by copying items into variables. But the array itself is not modified.

- Unwanted elements of the array can also be thrown away via an extra comma:

```
// second element is not needed
let [firstName, , title] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];
```

- **Important** Internally a destructuring assignment works by iterating over the right value. It’s a kind of syntax sugar for calling for..of over the value to the right of = and assigning the values.

- If we’d like also to gather all that follows – we can add one more parameter that gets “the rest” using three dots "...":

```
let [name1, name2, ...titles] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];
// now titles = ["Consul", "of the Roman Republic"]
```

*for detail visit: https://javascript.info/destructuring-assignment*

### Implementation of JS forEach, filter, map, methods

```
let arr = [1,2,3];

let fArr = arr.forEach(item => item < 3); // What is fArr

fArr = arr.filter(item => item < 3); // What is fArr

fArr = arr.map(item => item < 3); // What is fArr

```

Explanation: 

- The filter() method of Array instances creates a shallow copy of a portion of a given array, filtered down to just the elements from the given array that pass the test implemented by the provided function. A shallow copy of the given array containing just the elements that pass the test. If no elements pass the test, an empty array is returned.

- The The map() method of Array instances creates a new array populated with the results of calling a provided function on every element in the calling array. Return value is a new array with each element being the result of the callback function.

- The forEach() method of Array instances executes a provided function once for each array element. No return value.

```
Array.prototype.myEach = function (callback) {
  for (let i = 0; i < this.length; i++) {
    callback(this[i], i, this);
  }
};

Array.prototype.myFilter = function (callback) {
  let newArr = [];
  for (let i = 0; i < this.length; i++) 
  {
    if(callback(this[i], i, this))
      newArr.push(this[i])
  }
  return newArr;
}

Array.prototype.myMap = function (callback) {
  let newArr = [];
  for (let i = 0; i < this.length; i++) 
  {
      newArr.push(callback(this[i], i, this))
  }
  return newArr;
}

let arr = [1, 2, 3];

arr.myEach((item => { console.log(item) }));
console.log(arr.myFilter((item => item < 3)));
console.log(arr.myMap((item => item * 2)));

```

https://github.com/knaxus/native-javascript/blob/master/implementations/filter.js (for other implementations see this link)


### Decorators

- Decorators are helpful for anything you want to transparently wrap with extra functionality. These include memoization, enforcing access control and authentication, instrumentation and timing functions, logging, rate-limiting, and the list goes on.

```
@Component()
class AppComponent{}
```

- `@` indicates to the parser that we’re using a decorator while component references a function by that name. Our decorator takes an argument (the function/class being decorated) and returns the same function/class with added functionality.

- decorators proposal seeks to enable annotating and modifying JavaScript classes, properties and object literals at design time while keeping a syntax that’s declarative. ( while classes support extension, we need something better when we have multiple classes that need to share a single piece of functionality)

### Copying Object Without reference

1. Spread operator. Create a **Shallow** copy (A shallow copy means the first level is copied, deeper levels are referenced.)
To make a deep copy we need to use spread operator more than once. Like in react. React uses this a lot.
```
setPlayer({
    ...player,
    firstName: e.target.value,
});

//nested example

setPerson({
  ...person, // Copy other fields
  artwork: { // but replace the artwork
    ...person.artwork, // with the same one
    city: 'New Delhi' // but in New Delhi!
  }
});

```

2. Object.assign also creates a shallow copy similar to spread operator.
```const cloneFood = Object.assign({}, food);```

3. JSON.stringify/parse: creates a **deep copy**. but a bit slower and dirty approach. Also only work with Number and String and Object literal without function or Symbol properties.

drawbacks:
- If an object value is a function, the object is no JSON

```const cloneFood = JSON.parse(JSON.stringify(food));```

4. Lodash deepClone: Does deep clone and work with all types, function and Symbol are copied by reference.

5. The **structuredClone** global function is already available in Firefox 94, Node 17 and Deno 1.14. It also does a deep copy.

```const clone = structuredClone(original);```


### Copying Array Without reference

1. Use concat method of JS. (have to see, may be it creates a shallow copy)

2. Lodash deepClone: Does deep clone and work with all types, function and Symbol are copied by reference.

### Comparing two Simple Array (string,number)

1. Conventional method, first check length then loop through both arrays.

2. Lodash _.isEqual() work best for simple arrays


### Comparing Array of Objects

1. Lodash _.isEqual() work best for Array of objects as well. Even with deeply nested properties.

```
let a = [ {a: 11}, {b: 2},{c: [33]} ];
let b = [ {a: 11}, {b: 2},{c: [33]} ];

_.isEqual(a,b) // true
```

### Comparing Objects

1. Lodash _.isEqual()


### Async / Await

- We can use async / await for dealing with promises.
- Async/ Await resolve the promise itself. So no need for ```.then``` syntax
- Async / Await example. If we don't put ```editCheck``` in async await then the log below will occur first.

```
  async editCheck(data){
    await someAsyncFunc();
    console.log('This will log at the end');
  }

  async someAsyncFunc() {
    let res = await this.ruleService.getRuleByIdAndProjectId(this.ruleId, this.projectId).catch((error: any) => {
      this.toastService.error('Unable to fetch rules')
    })
    console.log('If no error, this will log at the start');
  }

```

### Searching in Array

- Use ```Set``` for array where possible because ```array.includes(item)``` method has linear complexity where as ```Set.has(item)``` is fast.


### Currying

### Spread Operator

### Closures

### Binding (this)

### Basic JS related Question 

1. What is Webpack ? It’s use cases.

At its core, webpack is a static module bundler for modern JavaScript applications. Angular and React uses it for bundling applications. We can also create our application in js/ts and use webpack to have smart bundling, which bundles down our app
into a single or multiple files (depends on Webpack config). This file is then downloded by browsers, like in case of Angular, after build process (which also uses webpack for building) we have 'dist' folder, that browsers download for showing our application in production env.

6. What approaches should we take to migrate from Angular.js to Angular.

Need to rewrite the whole application.

7. Some deployment or prod things to keep in mind.

8. What is Babel? 