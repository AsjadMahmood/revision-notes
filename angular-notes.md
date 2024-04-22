### Services and dependency injection

- Ideally, a component's job is to enable only the user experience. A component should present properties and methods for data binding to mediate between the view and the application logic. A component should use services for tasks that don't involve the view or application logic. Services are good for tasks such as fetching data from the server, validating user input, or logging directly to the console. By defining such processing tasks in an injectable service class, you make those tasks available to any component

- **Dependency injection (DI)** is the part of the Angular framework that provides components with access to services and other resources. Angular provides the ability for you to inject a service into a component to give that component access to the service.

- The ```@Injectable()``` decorator defines a class as a service in Angular and allows Angular to inject it into a component as a dependency.

Behind the scene 
- The injector is the main mechanism. Angular creates an application-wide injector for you during the bootstrap process, and additional injectors as needed. You don't have to create injectors.

- An injector creates dependencies and maintains a container of dependency instances that it reuses, if possible.

- A provider is an object that tells an injector how to obtain or create a dependency

https://angular.io/generated/images/guide/architecture/injector-injects.png

- **providers** You must register at least one provider of any service you are going to use. The provider can be part of the service's own metadata, making that service available everywhere, or you can register providers with specific modules or components. You register providers in the metadata of the service (in the @Injectable() decorator), or in the @NgModule() or @Component() metadata.

```
//by deafult (available at root level. can be used to share data)
@Injectable({
 providedIn: 'root',
})

//at module level (available at module level to all components available in that module)
content_copy
@NgModule({
  providers: [
  BackendService,
  Logger
 ],
 …
})
//at component level (creates new instance with each new instance of component)
@Component({
  selector:    'app-hero-list',
  templateUrl: './hero-list.component.html',
  providers:  [ HeroService ]
})
```
https://angular.io/guide/architecture-services

### Angular Basic Architecture

- Below diagram very well explains the basic architecture of angular.

https://angular.io/generated/images/guide/architecture/overview2.png

**Advanced Angular Concept Links** https://angular.io/guide/architecture-next-steps

### Using observables to pass values

- Observables provide support for passing messages between parts of your application. They are used frequently in Angular and are a technique for event handling, asynchronous programming, and handling multiple values.

- Benefits of using observablee over Regular Promises:


- In the observer design pattern there's an object called  ```subject``` which maintains the list of dependents called ```observers```. It notifies these observers of the state changes. Kind of similar to publish/subscribe pattern. 

- Observables are declarative i.e. You define a function for publishing values, but it is not executed until a consumer subscribes to it. The subscribed consumer then receives notifications until the function completes, or until they unsubscribe.
for example in order to receive notifications from a certain chanel in youtube you have to first subscribe to it.

- An observable can deliver multiple values of any type
- The API for receiving values is the same whether the values are delivered synchronously or asynchronously.
- Whether the stream was keystrokes, an HTTP response, or an interval timer, the interface for listening to values and stopping listening is the same.

### Rxjx Basic Understanding Points

- RxJS is a library for composing 'asynchronous' and 'event-based' programs by using observable sequences. It provides one core type, the Observable, satellite types (Observer, Schedulers, Subjects) and operators inspired by Array#extras (map, filter, reduce, every, etc) to allow handling asynchronous events as collections.

The essential concepts in RxJS which solve async event management are:

```Observable```  : represents the idea of an invokable collection of future values or events.

```Observer```    : is a collection of callbacks that knows how to listen to values delivered by the Observable.

- Observers are just objects with three callbacks, one for each type of notification that an Observable may deliver.
- An Observer is a consumer of values delivered by an Observable.
```
const observer = {
  next: x => console.log('Observer got a next value: ' + x),
  error: err => console.error('Observer got an error: ' + err),
  complete: () => console.log('Observer got a complete notification'),
};
```
- Observers in RxJS may also be partial. If you don't provide one of the callbacks, the execution of the Observable will still happen normally. To use the Observer, provide it to the subscribe of an Observable: ``` observable.subscribe(observer);```

- When subscribing to an Observable, you may also just provide the next callback as an argument, without being attached to an Observer object, for instance. This is mostly used but will be deprecated in the rxjs v8 like this: 

```
observable.subscribe((data)=>
{
    console.log('some data',data);
},(error)=>{ console.log('some error returned') });

//Internally in observable.subscribe, it will create an Observer object using the callback argument as the next handler.
//the second argument is the error handler and the third is the complete handler
```

```Subscription```: represents the execution of an Observable, is primarily useful for cancelling the execution.

- A Subscription is an object that represents a disposable resource, usually the execution of an Observable. A Subscription has one important method, unsubscribe, that takes no argument and just disposes the resource held by the subscription
- A Subscription essentially just has an unsubscribe() function to release resources or cancel Observable executions.

```
const observable = interval(1000);
const subscription = observable.subscribe(x => console.log(x));
// Later:
// This cancels the ongoing Observable execution which
// was started by calling subscribe with an Observer.
subscription.unsubscribe();
```
- Subscriptions can also be put together, so that a call to an unsubscribe() of one Subscription may unsubscribe multiple Subscriptions. You can do this by "adding" one subscription into another:

```
const subscription = observable1.subscribe(x => console.log('first: ' + x));
const childSubscription = observable2.subscribe(x => console.log('second: ' + x));
 
subscription.add(childSubscription);
 
// Unsubscribes BOTH subscription and childSubscription
subscription.unsubscribe();
```
```Operators```   : are pure functions that enable a functional programming style of dealing with collections with operations like map, filter, concat, reduce, etc.
```Subject```     : is equivalent to an EventEmitter, and the only way of multicasting a value or event to multiple Observers.
```Schedulers```  : are centralized dispatchers to control concurrency, allowing us to coordinate when computation happens on e.g. setTimeout or requestAnimationFrame or others.

- pipe method

**Q:** Why is ```pipe``` method mandatory i.e. why we need pipeable operators. (https://v6.rxjs.dev/guide/v6/pipeable-operators#known-issues)

Problems with the patched operators for dot-chaining are. The above reasons exist in rxjs version below 5. Which actually existed 8 years ago. So almost all 'angular v2+' use pipeable operators.

- Any library that imports a patch operator will augment(add to) the Observable.prototype (dot chaining) for all consumers of that library, creating blind dependencies. If the library removes their usage, they unknowingly break everyone else. With pipeables, you have to import the operators you need into each file you use them in.

- Operators patched directly onto the prototype are not "tree-shakeable" by tools like rollup or webpack. Pipeable operators will be as they are just functions pulled in from modules directly.

- Unused operators that are being imported in apps cannot be detected reliably by any sort of build tool or lint rule. That means that you might import scan, but stop using it, and it's still being added to your output bundle. With pipeable operators, if you're not using it, a lint rule can pick it up for you.

- Functional composition is awesome. Building your own custom operators becomes much easier, and now they work and look just like all other operators in rxjs. You don't need to extend Observable or override lift anymore. Because in pipe method you can pass your own custom operator.

### Imp Rxjx Functions**

1. of
Used to convert normal data into a stream of observables. It output the whole data, unlike 'from' which output the individual value from the array. 'of' is good when we don't need to individually do something on the values of the data. 'from' is good when
we want to modify, filter a data, and we convert into stream of data then we apply 'map' / 'filter', operator.
Note: they behave same as 'of', 'from' Array methods. 

```
//here source type is Observable<{ name: string; age: number;}>
const source = from([{ name: 'Joe', age: 31 }, { name: 'Bob', age: 25 }]);

//here source type is Observable<{ name: string; age: number;}[]>
const source = of([{ name: 'Joe', age: 31 }, { name: 'Bob', age: 25 }]);

let fruits = ['orange','apple','banana']
from : Emit the items one by one of array. For example

    from(fruits).subscribe(console.log) // 'orange','apple','banana'
of : Emit the whole array at once. For example

 of(fruits).subscribe(console.log) //  ['orange','apple','banana']

```

2. from
Turn an array, promise, or iterable into an observable.
💡 This operator can be used to convert a promise to an observable!
💡 For arrays and iterables, all contained values will be emitted as a sequence!
💡 This operator can also be used to emit a string as a sequence of characters!

```
//emit array as a sequence of values
const arraySource = from([1, 2, 3, 4, 5]);
//output: 1,2,3,4,5
const subscribe = arraySource.subscribe(val => console.log(val));
```
3. pipe
pipe method is used to organize the different operators we want to apply on our stream. (see above it's benefits in comparison dot chaining, we used in  rxjs4 version 8 years ago, not imp though).

```
source.pipe(map(person => person.age * 15));
```

4. Subject and BehaviouralSubject
- An RxJS ```Subject``` is a special type of Observable that allows values to be multicasted to many Observers. While plain Observables are unicast (each subscribed Observer owns an independent execution of the Observable), Subjects are multicast.
- Subjects are like EventEmitters: they maintain a registry of many listeners.
- One of the variants of Subjects is the ```BehaviorSubject```, which has a notion of "the current value". It stores the latest value emitted to its consumers, and whenever a new Observer subscribes, it will immediately receive the "current value" from the BehaviorSubject.

### 10 Most Used Rxjx Operators**

1. Map and Filter:

map operator just like JS ```map``` is used to modify values from an observable streams. Similarly filter operator is just like JS ```filter```, that is used to filter values from an observable streams.

```
let source = from([
  { name: 'Joe', age: 15 },
  { name: 'Bob', age: 5 }
]);
//modify age by multiplying it by 15
source.pipe(map(person => person.age * 15));

//filter out people with age under 30
const example = source.pipe(filter(person => person.age >= 30));
//output: "Over 30: Joe"
const subscribe = example.subscribe(val => console.log(`Over 30: ${val.name}`));
```

2. tap

tap operator actually doesn't do anything itself, but it can be used for debugging or running your side effects code. 
for example if you want to know what's inside the stream you can simply ```tap``` it.

```
products.pipe(tap(product => console.log(product)));

//or for side effects (in case of route guard)

canActivate(){
  const hasUser = $user.pipe(
    tap(user => { 
      if(user)
        //navigate to someother route
    })
  );
}

```

3. switchMap, concatMap, mergeMap

they are all used when we want to take emitted value from one stream and switch to another stream, so utilizing the first stream, emitted value, into the second stream.

```switchMap```: while second stream is utilising first stream value, and in between first stream emits another value, the switchMap cancels the subscription and switch to the latest emitted value by the first stream. for ex. if you are searching something you want to take the latest input into account, so it's a good use for it.

```
    //it will return Observable<Product>, while if we used 'map' here, it will return Observable<Observable<Product>>
		this.firstProduct$ = this.testService.getProducts().pipe
			(
				switchMap((val) =>
					this.testService.getProductById(val.products[0].id)
				)
			)
```      

```concatMap```: whereas follows the order and doesnot cancel. So it will fulfill the second stream and then utilise the first stream emitted value. for ex. when you want to get a list of posts by userId. 

```mergeMap```: It doesn't cancels, and also not respect the order like 'concatMap'. So the result is kind of merged one.

4. debounceTime, distinctUntilChanged, startWith

```debounceTime```: 
Delays values emitted by the source Observable, but drops previous pending delayed emissions if a new value arrives on the source Observable. This operator keeps track of the most recent value from the source Observable, and emits that only when dueTime enough time has passed without any other value appearing on the source Observable. If a new value appears before dueTime silence occurs, the previous value will be dropped and will not be emitted on the output Observable.
Wait's a given time, before emitting a next value. Used in cases where we don't to bombard backent with a lot
of requests, lets say in case of "search input textbox". Where every keystroke sends a request. So we will wait given time, before sending the request. Also if we want to avoid user for clicking multiple times sending same request we can use 'debounceTime'

```
Emit the most recent click after a burst of clicks

import { fromEvent } from 'rxjs';
import { debounceTime } from 'rxjs/operators';

const clicks = fromEvent(document, 'click');
const result = clicks.pipe(debounceTime(1000));
result.subscribe(x => console.log(x));

//now the previous request will be cancelled sent after 3sec. see 'network' logs it is cancelling requests.

this.listProducts$ = this.testService.getProducts().pipe(debounceTime(3000));
```

```distinctUntilChanged```: Checks if previous emitted value is different from the next value, if same does not emit. Good use case is "search input textbox" again.

```startWith```: Used to add value to the start of the stream.

```

```

### 3 Imp Data Fetching Problems Solution with Rxjs

1. How to prevent sending a request that's already in progress using Rxjs ? 

2. How to cancel request previous request and take the latest one, like in case of "searching input on keystroke" example. So if 
the types fast, instead of sending multiple requests on every keystroke, send the latest one.
 
3. How to cache a request response, to avoid duplicate making requests, like apollo client does for doing GraphQl queries in React.

4. If multiple times we assign a variable an observable, is my understanding correct that we need to unsubscribe it, before we 
assign it to a new observable.

5. pass observable to the child element.

**Standalone Components:**
- Now you can use Angular modules as an option. Moreover, you can also use standalone components for the first time.
In older versions of Angular, every component had to be integrated with a module. As a result, each component must be included in a parent module's. But now ng module is made optional. So component can exist independently with being register in the module. If you want to import a ```test``` component into ```app``` component this is how you will do.

``` 
import ...

@Component({
  selector: 'app-root',
  standalone: true,                       //new addition
  imports: [RouterOutlet, TestComponent], //new addition
  templateUrl: './app.component.html',
  styleUrl: './app.component.scss'
})
```

## Dependency Injection in Angular:

Dependecy Injection is a technique in which a class receives it's dependecies, through external source (Angular maitains this injection by itself ) instead of creating them itself. We can also limit the scope of the service to 'app', 'module' or 'component'.

Without DI,  For example a ``` sharedService```  has a ``` counter``` . Without DI we will create a new instance of sharedService. And the counter will now exist independently. 
``` 
// code in the component

    sharedService = new SharedService();
    log(){
      this.sharedService.logCounter();
    }
``` 



**Signals API**

// write here 

### Angular Testing**

**Angular TestBed**

- Even though Standalone Components make modules optional, the TestBed still comes with a testing module. It takes care of the test setup and provides all components, directives, pipes, and services for the test.
- Test bed creates a dynamically  ng module that emulated the behaviour of ngmodule.

spy: Instead of mocking a full class wiht the mock, spy replaces the specifc method or a property
stub. stub helps specifying value, returned by the spy function.

### Angular Optimizations Techniques (Bundle Optimization + Performance Optimization)

**Analyzing Performance**

Analyze your web app with chrome dev tools - lightHouse tab. It gives you the performance report on you web. Lighthouse runs on a specfic web page and shows you the performance stats of that webPage. But you can install npm lib 'unlighthouse' to run the performance for all the webpages. 

The other way to measure is using chrome Web Vitals extension. You can also configure it show logs. 

Some Core Metrics that you should look into are :

1. First Contentful Paint

How much time your webpage took to load first content. Like placeholders, some content ...

2. Largest Contentful Paint:

How much time you webpage took to load all the assets, images, css, fonts and content from apis.
2.5 sec Good, 2.5 - 4.0 Needs Improvement, 4.0+ Bad

3. First Input Delay

- It measures interactivity. So how much time your apps take to respond to users events. This can be measures using Web Vitals chrome plugin. 

100 ms Good, 100ms - 300ms Needs Improvement, 300Ms+ Bad

4. Commulative Layout Shift

This mesures Visual Stabilty. So our app is not jumping around. So the UI part is stable. Images or content with width and height
improves CLS. Can be measures using Lighthouse or Web Vitals.

**Tree Shaking**

With tree shaking angular itself removes dead or unused code when building application. But we have to develop our application in such a way to make use of Angular Tree Shaking. For exmaple importing only the used method from the library istead of the whole library. 
``` 
//instead of
import { cloneDeep } from 'lodash';
//Do
import cloneDeep from 'lodash/cloneDeep';
``` 

Audting your imports is important for tree shaking to works.

- Avoid unnessary imports in Css. This could end up importing duplicate css. Think of if your style needs encapsulation or duplicated different component. The rule is if large amount of code needs an css file
import it on a higher level.

**Optimize Third-Party Libraries**

First Ask: Do we really need this Library ?

Optimize Third-Party Libraries: Third-party libraries can significantly increase the bundle size of your application. To optimize the size of third-party libraries, you can use tools like Webpack Bundle Analyzer to identify the large dependencies and remove the unnecessary ones. You can also look for alternative libraries that are more lightweight and performant.

"Webpack Bundle Analyzer" / source-map to identify large third-party dependencies in your Angular application.
For example avoid using lodash, or UI libraries.

- Use tree shakeable libraries. Like instead of using 'moment' use 'date-fns' or 'lodash' use 'lodash-es'.

**Prevent Performance Regressions**

- In anagular.json you can set budget size for your builds. So that if it exceeds certain budget it give warning or does not 
deploy. Helps a lot in ci / cd pipelines, when your app grows certain threshold size, it's not deployed.

**Lazy Loading**

To Lazily load the component. We can configure it in angular router or without router. Like by interacting to a button we can dynamically import our new component. 
``` 
import('some-component-path').then(c => c.SomeComponent);
``` 


**Avoid RxjS Memory Leaks**

- Use 'async' pipe.
- Unsubscribe observables on 'ngOnDestroy'

**Use trackBy function on ngFor**

**Use pure pipes**

**Use OnPush change detection strategy**

----------------------------------------
### TS 

## Function Overloading & Simialr Switch Case example

- With single argument it doesnot makes sense to use function overload.

function switchRun(a:number, b:number):void;
function switchRun(a:string, b: string):void;
function switchRun(a:number | string, b: number | string)
{
   switch(a)
   {
      case 'small':
      case 'medium':
         console.log('2 cases');
         break;
      case 'large':
         console.log('1 case');
         break;
   }
}


switchRun('',''); // correct
switchRun(1,1); // correct
switchRun(1,''); // error
switchRun('',1); // error


------------------------------------------------------

### Basic Angular related Question 

1. How TS compiles down to JS ? / Browsers understand JS then how our TS code get’s converted into JS

Angular uses AOT compilation to compile HTML template and ts code into efficient js code. So when we run the 
ng build --prod our code is compiled to /dist directory. Which then browser downloads and runs that code. 
The benfit of AOT vs JIT (just in time) is that AOT (Compiling your application during the build process ) provides faster rendering in the browser.

2. What is IVY and AOT in angular.

- **IVY**:  Ivy is the code name for Angular's next-generation compilation and rendering pipeline
Benefits
Generated code that is easier to read and debug at runtime
Faster re-build time
Improved payload size (Real world applications should see some size improvements, but many more improvements are planned)
Improved template type checking
- **AOT** : Ahead of time compilation that is compiling your application during the build process which results in better rendering of the app in the browser.

3. What are Angular new features. ?

- standalone components
- signals api (most imp change)

4. D/F B/W Subject And Observable ?

4. D/F B/W Subject And Behavioural Subject ?

- 

4. Can we have a click listner on ```<ng-container>``` tag
- You can't because ng-container doesn't get rendered in the html template (this is angular specific). So it wont be transpiled in the actual DOM, that element will not exists at runtime.

5. What is Angular Universal
- Angular Universal is a technology stack within the Angular framework that enables server-side rendering (SSR) for Angular applications. Traditionally, Angular applications run entirely on the client side (in the browser), but with Angular Universal, you can also render Angular applications on the server side before sending them to the client. This can provide several benefits, including improved performance, better search engine optimization (SEO), and enhanced user experience.

6. What is Build Time and Run Time in context of web applications. (not specific Angular question)

Definition: **Build time** refers to the phase during which the source code of an application is transformed into an executable or deployable form.

Activities: During build time, tasks such as compiling source code, bundling assets, optimizing resources, and preparing the application for deployment are performed.

Output: The result of the build process is often a set of static files, scripts, stylesheets, and other artifacts that are ready to be served to clients.

Definition: **Run time** is the phase when the application is actively running and executing on a user's device or server.

Activities: During run time, the application processes user input, interacts with databases or external services, and dynamically generates content or responds to user actions.

7. Why is it good to use stream data instead of subscribing to the data.

//answer