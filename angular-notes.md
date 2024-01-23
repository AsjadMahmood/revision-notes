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

- In the observer design pattern there's an object called  ```subject``` which maintains the list of dependents called ```observers```. It notifies these observers of the state changes. Kind of similar to publish/subscribe pattern. 

- Observables are declarative i.e. You define a function for publishing values, but it is not executed until a consumer subscribes to it. The subscribed consumer then receives notifications until the function completes, or until they unsubscribe.
for example in order to receive notifications from a certain chanel in youtube you have to first subscribe to it.

- An observable can deliver multiple values of any type
- The API for receiving values is the same whether the values are delivered synchronously or asynchronously.
- Whether the stream was keystrokes, an HTTP response, or an interval timer, the interface for listening to values and stopping listening is the same.

### D/F B/W Subject And Behavioural Subject

- 


https://angular.io/guide/observables


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

3. Latest angular and its new features. ?

**Standalone Components:**
- Now you can use Angular modules as an option. Moreover, you can also use standalone components for the first time.
In older versions of Angular, every component had to be integrated with a module. As a result, each component must be included in a parent module's. But now ng module is made optional

- Only IVY is available. So View Engine is gone
- CLI improved
- Comes New TS version
- Comes with New RXJS version

## Angular Questions

1. Can we have a click listner on ```<ng-container>``` tag
- You can't because ng-container doesn't get rendered in the html template (this is angular specific). So it wont be transpiled in the actual DOM, that element will not exists at runtime.

2. What is Angular Universal
- Angular Universal is a technology stack within the Angular framework that enables server-side rendering (SSR) for Angular applications. Traditionally, Angular applications run entirely on the client side (in the browser), but with Angular Universal, you can also render Angular applications on the server side before sending them to the client. This can provide several benefits, including improved performance, better search engine optimization (SEO), and enhanced user experience.

3. What is Build Time and Run Time in context of web applications. (not specific Angular question)
- Build Time:

Definition: Build time refers to the phase during which the source code of an application is transformed into an executable or deployable form.
Activities: During build time, tasks such as compiling source code, bundling assets, optimizing resources, and preparing the application for deployment are performed.
Output: The result of the build process is often a set of static files, scripts, stylesheets, and other artifacts that are ready to be served to clients.
Run Time:

Definition: Run time is the phase when the application is actively running and executing on a user's device or server.
Activities: During run time, the application processes user input, interacts with databases or external services, and dynamically generates content or responds to user actions.