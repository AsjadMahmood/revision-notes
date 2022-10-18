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

### Currying

### Spread Operator

### Closures

### Binding (this)

### Decorators

- Decorators are helpful for anything you want to transparently wrap with extra functionality. These include memoization, enforcing access control and authentication, instrumentation and timing functions, logging, rate-limiting, and the list goes on.

```
@Component()
class AppComponent{}
```

- `@` indicates to the parser that we’re using a decorator while component references a function by that name. Our decorator takes an argument (the function/class being decorated) and returns the same function/class with added functionality.

- decorators proposal seeks to enable annotating and modifying JavaScript classes, properties and object literals at design time while keeping a syntax that’s declarative. ( while classes support extension, we need something better when we have multiple classes that need to share a single piece of functionality)