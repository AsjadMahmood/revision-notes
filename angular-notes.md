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

- Below diagram very well explains the architecture of angular.

https://angular.io/generated/images/guide/architecture/overview2.png

**Advanced Angular Concept Links** https://angular.io/guide/architecture-next-steps

### Using observables to pass values