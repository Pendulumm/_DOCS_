# 						Angular Base





## 1.Template



### Displaying values with interpolation



Interpolation refers to embedding expressions into marked up text. 

By default, interpolation uses the double curly braces `{{` and `}}` as delimiters.





### Pipes

Use [pipes](https://angular.io/guide/glossary#pipe) to transform strings, currency amounts, dates, and other data for display.



#### pipe

A class which is preceded by the `@Pipe{}` decorator and which defines a function that transforms input values to output values for display in a [view](https://angular.io/guide/glossary#view). Angular defines various pipes, and you can define new pipes.

To learn more, see [Pipes](https://angular.io/guide/pipes).



#### What is a pipe

Pipes are simple functions to use in [template expressions](https://angular.io/guide/glossary#template-expression) to accept an input value and return a transformed value. Pipes are useful because you can use them throughout your application, while only declaring each pipe once. For example, you would use a pipe to show a date as **April 15, 1988** rather than the raw string format.



#### Built-in pipes

Angular provides built-in pipes for typical data transformations, including transformations for internationalization (i18n), which use locale information to format data. The following are commonly used built-in pipes for data formatting:

- [`DatePipe`](https://angular.io/api/common/DatePipe): Formats a date value according to locale rules.
- [`UpperCasePipe`](https://angular.io/api/common/UpperCasePipe): Transforms text to all upper case.
- [`LowerCasePipe`](https://angular.io/api/common/LowerCasePipe): Transforms text to all lower case.
- [`CurrencyPipe`](https://angular.io/api/common/CurrencyPipe): Transforms a number to a currency string, formatted according to locale rules.
- [`DecimalPipe`](https://angular.io/api/common/DecimalPipe): Transforms a number into a string with a decimal point, formatted according to locale rules.
- [`PercentPipe`](https://angular.io/api/common/PercentPipe): Transforms a number to a percentage string, formatted according to locale rules.



Create pipes to encapsulate custom transformations and use your custom pipes in template expressions.



#### Pipes and precedence

The pipe operator has a higher precedence than the ternary operator (`?:`), which means `a ? b : c | x` is parsed as `a ? b : (c | x)`. The pipe operator cannot be used without parentheses in the first and second operands of `?:`.

Due to precedence, if you want a pipe to apply to the result of a ternary, wrap the entire expression in parentheses; for example, `(a ? b : c) | x`.









## 2.Property Binding



**Understanding the flow of data**

Property binding moves a value in one direction, from a component's property into a target element property.





**Binding to a property**

To bind to an element's property, enclose it in square brackets, `[]`, which identifies the property as a target property.



A target property is the DOM property to which you want to assign a value.

To assign a value to a target property for the image element's `src` property, type the following code:

```html
<img alt="item" [src]="itemImageUrl">
```



In most cases, the target name is the name of a property, even when it appears to be the name of an attribute.

In this example, `src` is the name of the `<img>` element property.



The brackets, `[]`, cause Angular to evaluate the right-hand side of the assignment as a dynamic expression.



**Without the brackets,** Angular treats the right-hand side as **a string literal** and sets the property to that static value.

To assign a string to a property, type the following code:

```html
<app-item-detail childItem="parentItem"></app-item-detail>
```

Omitting the brackets renders the string `parentItem`, not the value of `parentItem`.



## 3.Event binding



Event binding lets you listen for and respond to user actions such as keystrokes, mouse movements, clicks, and touches.





### Binding to events

To bind to an event you use the Angular event binding syntax. This syntax consists of a target event name within parentheses to the left of an equal sign, and a quoted template statement to the right.

Create the following example; the target event name is `click` and the template statement is `onSave()`.

```html
<button (click)="onSave()">Save</button>
```



The event binding listens for the button's click events and calls the component's `onSave()` method whenever a click occurs.

![](./img/binding to events.svg) 



#### Determining an event target

To determine an event target, Angular checks if the name of the target event matches an event property of a known directive.

Create the following example: (Angular checks to see if `myClick` is an event on the custom `ClickDirective`)



(app.component.html)

```html
<h4>myClick is an event on the custom ClickDirective:</h4>
<button type="button" (myClick)="clickMessage=$event" clickable>click with myClick</button>
{{clickMessage}}
```

If the target event name, `myClick` fails to match an output property of `ClickDirective`, Angular will instead bind to the `myClick` event on the underlying DOM element.





## 4.Build-in directives



Directives are classes that add additional behavior to elements in your Angular applications. Use Angular's built-in directives to manage forms, lists, styles, and what users see.



#### 1.Adding and removing classes with `NgClass`



Add or remove multiple CSS classes simultaneously with `ngClass`.

**To add or remove a *single* class, use [class binding](https://angular.io/guide/class-binding) rather than `NgClass`.**



##### Using `NgClass` with an expression

On the element you'd like to style, add `[ngClass]` and set it equal to an expression. In this case, `isSpecial` is a boolean set to `true` in `app.component.ts`. Because `isSpecial` is true, `ngClass` applies the class of `special` to the `<div>`.

(app.component.html)

```html
<!-- toggle the "special" class on/off with a property -->
<div [ngClass]="isSpecial ? 'special' : ''">This div is special</div>
```



##### Using `NgClass` with a method

1.To use `NgClass` with a method, add the method to the component class. In the following example, `setCurrentClasses()` sets the property `currentClasses` with an object that adds or removes three classes based on the `true` or `false` state of three other component properties.

Each key of the object is a CSS class name. If a key is `true`, `ngClass` adds the class. If a key is `false`, `ngClass` removes the class.

(app.component.ts)

```typescript
currentClasses: Record<string, boolean> = {};
/* . . . */
setCurrentClasses() {
  // CSS classes: added/removed per current state of component properties
  this.currentClasses =  {
    saveable: this.canSave,
    modified: !this.isUnchanged,
    special:  this.isSpecial
  };
}
```



2.In the template, add the `ngClass` property binding to `currentClasses` to set the element's classes:

(app.component.html)

```html
<div [ngClass]="currentClasses">This div is initially saveable, unchanged, and special.</div>
```



For this use case, Angular applies the classes on initialization and in case of changes. The full example calls `setCurrentClasses()` initially with `ngOnInit()` and when the dependent properties change through a button click. These steps are not necessary to implement `ngClass`.

For more information, see the [live example](https://angular.io/generated/live-examples/built-in-directives/stackblitz.html) / [download example](https://angular.io/generated/zips/built-in-directives/built-in-directives.zip) `app.component.ts` and `app.component.html`.





#### 2.Setting inline styles with `NgStyle`

Use `NgStyle` to set multiple inline styles simultaneously, based on the state of the component.

1. To use `NgStyle`, add a method to the component class.

   In the following example, `setCurrentStyles()` sets the property `currentStyles` with an object that defines three styles, based on the state of three other component properties.

   (app.component.ts)

   ```typescript
   currentStyles: Record<string, string> = {};
   /* . . . */
   setCurrentStyles() {
     // CSS styles: set per current state of component properties
     this.currentStyles = {
       'font-style':  this.canSave      ? 'italic' : 'normal',
       'font-weight': !this.isUnchanged ? 'bold'   : 'normal',
       'font-size':   this.isSpecial    ? '24px'   : '12px'
     };
   }
   ```

2. To set the element's styles, add an `ngStyle` property binding to `currentStyles`.

   (app.component.html)

   ```html
   <div [ngStyle]="currentStyles">
     This div is initially italic, normal weight, and extra large (24px).
   </div>
   ```

   For this use case, Angular applies the styles upon initialization and in case of changes. To do this, the full example calls `setCurrentStyles()` initially with `ngOnInit()` and when the dependent properties change through a button click. However, these steps are not necessary to implement `ngStyle` on its own. See the [live example](https://angular.io/generated/live-examples/built-in-directives/stackblitz.html) / [download example](https://angular.io/generated/zips/built-in-directives/built-in-directives.zip) `app.component.ts` and `app.component.html` for this optional implementation.

   





#### 3.Displaying and updating properties with `ngModel`

Use the `NgModel` directive to display a data property and update that property when the user makes changes.

1. Import `FormsModule` and add it to the NgModule's `imports` list.

   ( app.module.ts (FormsModule import) )

   ```typescript
   import { FormsModule } from '@angular/forms'; // <--- JavaScript import from Angular
   /* . . . */
   @NgModule({
     /* . . . */
   
     imports: [
       BrowserModule,
       FormsModule // <--- import into the NgModule
     ],
     /* . . . */
   })
   export class AppModule { }
   ```

2. Add an `[(ngModel)]` binding on an HTML `<form>` element and set it equal to the property, here `name`.

   ( app.component.html (NgModel example) )

   ```html
   <label for="example-ngModel">[(ngModel)]:</label>
   <input [(ngModel)]="currentItem.name" id="example-ngModel">
   ```

   This `[(ngModel)]` syntax can only set a data-bound property.

   

To customize your configuration, write the expanded form, which separates the property and event binding. Use [property binding](https://angular.io/guide/property-binding) to set the property and [event binding](https://angular.io/guide/event-binding) to respond to changes. The following example changes the `<input>` value to uppercase:



(app.component.html)

```html
<input [ngModel]="currentItem.name" (ngModelChange)="setUppercaseName($event)" id="example-uppercase">
```









#### 4.Adding or removing an element with `NgIf`

Add or remove an element by applying an `NgIf` directive to a host element.

When `NgIf` is `false`, Angular removes an element and its descendants from the DOM. Angular then disposes of their components, which frees up memory and resources.

To add or remove an element, bind `*ngIf` to a condition expression such as `isActive` in the following example.



(app.component.html)

```html
<app-item-detail *ngIf="isActive" [item]="item"></app-item-detail>
```



When the `isActive` expression returns a truthy value, `NgIf` adds the `ItemDetailComponent` to the DOM. When the expression is falsy, `NgIf` removes the `ItemDetailComponent` from the DOM and disposes of the component and all of its subcomponents.

For more information on `NgIf` and `NgIfElse`, see the [NgIf API documentation](https://angular.io/api/common/NgIf).



##### Guarding against `null`

By default, `NgIf` prevents display of an element bound to a null value.



To use `NgIf` to guard a `<div>`, add `*ngIf="yourProperty"` to the `<div>`. In the following example, the `currentCustomer` name appears because there is a `currentCustomer`.



(app.component.html)

```html
<div *ngIf="currentCustomer">Hello, {{currentCustomer.name}}</div>
```



However, if the property is `null`, Angular does not display the `<div>`. In this example, Angular does not display the `nullCustomer` because it is `null`.



(app.component.html)

```html
<div *ngIf="nullCustomer">Hello, <span>{{nullCustomer}}</span></div>
```





#### 5.Listing items with `NgFor`

Use the `NgFor` directive to present a list of items.

1. Define a block of HTML that determines how Angular renders a single item.
2. To list your items, assign the shorthand `let item of items` to `*ngFor`.



(app.component.html)

```html
<div *ngFor="let item of items">{{item.name}}</div>
```



The string `"let item of items"` instructs Angular to do the following:

- Store each item in the `items` array in the local `item` looping variable
- Make each item available to the templated HTML for each iteration
- Translate `"let item of items"` into an `<ng-template>` around the host element
- Repeat the `<ng-template>` for each `item` in the list

For more information see the [Structural directive shorthand](https://angular.io/guide/structural-directives#shorthand) section of [Structural directives](https://angular.io/guide/structural-directives).



##### Repeating a component view

To repeat a component element, apply `*ngFor` to the selector. In the following example, the selector is `<app-item-detail>`.



(app.component.html)

```html
<app-item-detail *ngFor="let item of items" [item]="item"></app-item-detail>
```

Reference a template input variable, such as `item`, in the following locations:

- Within the `ngFor` host element
- Within the host element descendants to access the item's properties





The following example references `item` first in an interpolation and then passes in a binding to the `item` property of the `<app-item-detail>` component.



(app.component.html)

```html
<div *ngFor="let item of items">{{item.name}}</div>
<!-- . . . -->
<app-item-detail *ngFor="let item of items" [item]="item"></app-item-detail>
```





##### Getting the `index` of `*ngFor`

Get the `index` of `*ngFor` in a template input variable and use it in the template.



In the `*ngFor`, add a semicolon and `let i=index` to the shorthand. The following example gets the `index` in a variable named `i` and displays it with the item name.



(app.component.html)

```html
<div *ngFor="let item of items; let i=index">{{i + 1}} - {{item.name}}</div>
```



The index property of the `NgFor` directive context returns the zero-based index of the item in each iteration.



Angular translates this instruction into an `<ng-template>` around the host element, then uses this template repeatedly to create a new set of elements and bindings for each `item` in the list. For more information about shorthand, see the [Structural Directives](https://angular.io/guide/structural-directives#shorthand) guide.



#### 6.Repeating elements when a condition is true

To repeat a block of HTML when a particular condition is true, put the `*ngIf` on a container element that wraps an `*ngFor` element.

For more information see [one structural directive per element](https://angular.io/guide/structural-directives#one-per-element).



##### Tracking items with `*ngFor` `trackBy`



Reduce the number of calls your application makes to the server by tracking changes to an item list. With the `*ngFor` `trackBy` property, Angular can change and re-render only those items that have changed, rather than reloading the entire list of items.



1.Add a method to the component that returns the value `NgFor` should track. In this example, the value to track is the item's `id`. If the browser has already rendered `id`, Angular keeps track of it and doesn't re-query the server for the same `id`.



(app.component.ts)

```typescript
trackByItems(index: number, item: Item): number { return item.id; }
```



2.In the shorthand expression, set `trackBy` to the `trackByItems()` method.



(app.component.html)

```html
<div *ngFor="let item of items; trackBy: trackByItems">
  ({{item.id}}) {{item.name}}
</div>
```



## 5.Attribute directives



Change the appearance or behavior of DOM elements and Angular components with attribute directives.









## Two-way binding







## Dependency injection in Angular





When you develop a smaller part of your system, like a module or a class, you may need to use features from other classes. For example, you may need an HTTP service to make backend calls. Dependency Injection, or DI, is a design pattern and mechanism for creating and delivering some parts of an application to other parts of an application that require them. Angular supports this design pattern and you can use it in your applications to increase flexibility and modularity.

In Angular, dependencies are typically services, but they also can be values, such as strings or functions. An injector for an application (created automatically during bootstrap) instantiates dependencies when needed, using a configured provider of the service or value.





### Understanding dependency injection

Dependency injection, or DI, is one of the fundamental concepts in Angular. DI is wired into the Angular framework and allows classes with Angular decorators, such as Components, Directives, Pipes, and Injectables, to configure dependencies that they need.

Two main roles exist in the DI system: **dependency consumer** and **dependency provider**.

Angular facilitates the interaction between dependency consumers and dependency providers using an abstraction called [Injector](https://angular.io/guide/glossary#injector). When a dependency is requested, the injector checks its registry to see if there is an instance already available there. If not, a new instance is created and stored in the registry. Angular creates an application-wide injector (also known as "root" injector) during the application bootstrap process, as well as any other injectors as needed. In most cases you don't need to manually create injectors, but you should know that there is a **layer** that connects providers and consumers.

This topic covers basic scenarios of how a class can act as a dependency. Angular also allows you to use functions, objects, primitive types such as string or Boolean, or any other types as dependencies. For more information, see [Dependency providers](https://angular.io/guide/dependency-injection-providers).





#### Providing dependency

Imagine there is a class called HeroService that needs to act as a dependency in a component.

The first step is to add the @Injectable decorator to show that the class can be injected.

```typescript
@Injectable()
class HeroService {}
```



The next step is to make it available in the DI by providing it. A dependency can be provided in multiple places:

- At the Component level, using the `providers` field of the `@Component` decorator. In this case the `HeroService` becomes available to all instances of this component and other components and directives **used in the template**. For example:

  ```typescript
  @Component({
    selector: 'hero-list',
    template: '...',
    providers: [HeroService]
  })
  class HeroListComponent {}
  ```

  When you register a provider at the component level, you get a new instance of the service with each new instance of that component.

  

- At the NgModule level, using the `providers` field of the `@NgModule` decorator. In this scenario, the `HeroService` is available to all components, directives, and pipes **declared in this NgModule** or other NgModule which is within the same ModuleInjector applicable for this NgModule. When you register a provider with a specific NgModule, the same instance of a service is available to all applicable components, directives and pipes. To understand all edge-cases, see [Hierarchical injectors](https://angular.io/guide/hierarchical-dependency-injection). For example:

  ```typescript
  @NgModule({
    declarations: [HeroListComponent]
    providers: [HeroService]
  })
  class HeroListModule {}
  ```

  

- At the application root level, which allows injecting it into other classes in the application. This can be done by adding the `providedIn: 'root'` field to the `@Injectable` decorator:

  ```typescript
  @Injectable({
    providedIn: 'root'
  })
  class HeroService {}
  ```

  When you provide the service at the root level, Angular creates a single, shared instance of the `HeroService` and injects it into any class that asks for it. Registering the provider in the `@Injectable` metadata also allows Angular to optimize an app by removing the service from the compiled application if it isn't used, a process known as tree-shaking.



#### Injecting a dependency

The most common way to inject a dependency is to declare it in a class constructor. When Angular creates a new instance of a component, directive, or pipe class, it determines which services or other dependencies that class needs by looking at the constructor parameter types. For example, if the `HeroListComponent` needs the `HeroService`, the constructor can look like this:

```typescript
@Component({ … })
class HeroListComponent {
  constructor(private service: HeroService) {}
}
```



Another option is to use the [inject](https://angular.io/api/core/inject) method:

```typescript
@Component({ … })
class HeroListComponent {
  private service = inject(HeroService);
}
```



When Angular discovers that a component depends on a service, it first checks if the injector has any existing instances of that service. If a requested service instance doesn't yet exist, the injector creates one using the registered provider, and adds it to the injector before returning the service to Angular.

When all requested services have been resolved and returned, Angular can call the component's constructor with those services as arguments.



![](./img/injector-injects.png)





### Creating an injectable service

Service is a broad category encompassing any value, function, or feature that an application needs. A service is typically a class with a narrow, well-defined purpose. A component is one type of class that can use DI.

Angular distinguishes components from services to increase modularity and reusability. By separating a component's view-related features from other kinds of processing, you can make your component classes lean and efficient.

Ideally, a component's job is to enable the user experience and nothing more. A component should present properties and methods for data binding, to mediate between the view (rendered by the template) and the application logic (which often includes some notion of a model).

A component can delegate certain tasks to services, such as fetching data from the server, validating user input, or logging directly to the console. By defining such processing tasks in an injectable service class, you make those tasks available to any component. You can also make your application more adaptable by injecting different providers of the same kind of service, as appropriate in different circumstances.

Angular does not enforce these principles. Angular helps you follow these principles by making it easy to factor your application logic into services and make those services available to components through DI.



#### Service examples

Here's an example of a service class that logs to the browser console.

( src/app/logger.service.ts (class) )

```typescript
export class Logger {
  log(msg: any)   { console.log(msg); }
  error(msg: any) { console.error(msg); }
  warn(msg: any)  { console.warn(msg); }
}
```



Services can depend on other services. For example, here's a `HeroService` that depends on the `Logger` service, and also uses `BackendService` to get heroes. That service in turn might depend on the `HttpClient` service to fetch heroes asynchronously from a server.



( src/app/hero.service.ts (class) )

```typescript
export class HeroService {
  private heroes: Hero[] = [];

  constructor(
    private backend: BackendService,
    private logger: Logger) { }

  getHeroes() {
    this.backend.getAll(Hero).then( (heroes: Hero[]) => {
      this.logger.log(`Fetched ${heroes.length} heroes.`);
      this.heroes.push(...heroes); // fill cache
    });
    return this.heroes;
  }
}
```



#### Creating an injectable service

Angular CLI provides a command to create a new service. In the following example, you add a new service to your application, which was created earlier with the `ng new` command.

To generate a new `HeroService` class in the `src/app/heroes` folder, follow these steps:

1. Run this [Angular CLI](https://angular.io/cli) command:

   ```shell
   ng generate service heroes/hero
   ```

​		This command creates the following default `HeroService`.



​		( src/app/heroes/hero.service.ts (CLI-generated) )

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root',
})
export class HeroService {

}
```

​		The `@Injectable()` decorator specifies that Angular can use this class in the DI system. The metadata, `providedIn: 'root'`, means that the `HeroService` is visible throughout the application.



​	2.Add a `getHeroes()` method that returns the heroes from `mock.heroes.ts` to get the hero mock data:

​	( src/app/heroes/hero.service.ts )

```typescript
import { Injectable } from '@angular/core';
import { HEROES } from './mock-heroes';

@Injectable({
  // declares that this service should be created
  // by the root application injector.
  providedIn: 'root',
})
export class HeroService {
  getHeroes() { return HEROES; }
}
```



**For clarity and maintainability, it is recommended that you define components and services in separate files.**





#### Injecting services

To inject a service as a dependency into a component, you can use component's `constructor()` and supply a constructor argument with the dependency type. The following example specifies the `HeroService` in the `HeroListComponent` constructor. The type of the `heroService` is `HeroService`. Angular recognizes the `HeroService` as a dependency, since that class was previously annotated with the `@Injectable` decorator.



( src/app/heroes/hero-list.component (constructor signature) )

```typescript
constructor(heroService: HeroService)
```



#### constructor(heroService: HeroService)

When a service depends on another service, follow the same pattern as injecting into a component. In the following example `HeroService` depends on a `Logger` service to report its activities.

First, import the `Logger` service. Next, inject the `Logger` service in the `HeroService` `constructor()` by specifying `private logger: Logger`.

Here, the `constructor()` specifies a type of `Logger` and stores the instance of `Logger` in a private field called `logger`.

The following code tabs feature the `Logger` service and two versions of `HeroService`. The first version of `HeroService` does not depend on the `Logger` service. The revised second version does depend on `Logger` service.



( src/app/heroes/hero.service (v1) )

```typescript
import { Injectable } from '@angular/core';
import { HEROES } from './mock-heroes';

@Injectable({
  providedIn: 'root',
})
export class HeroService {
  getHeroes() { return HEROES; }
}
```



( src/app/logger.service )

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class Logger {
  logs: string[] = []; // capture logs for testing

  log(message: string) {
    this.logs.push(message);
    console.log(message);
  }
}
```



( src/app/heroes/hero.service (v2) )

```typescript
import { Injectable } from '@angular/core';
import { HEROES } from './mock-heroes';
import { Logger } from '../logger.service';

@Injectable({
  providedIn: 'root',
})
export class HeroService {

  constructor(private logger: Logger) {  }

  getHeroes() {
    this.logger.log('Getting heroes ...');
    return HEROES;
  }
}
```

In this example, the `getHeroes()` method uses the `Logger` service by logging a message when fetching heroes.





### Configuring dependency providers

The Creating and injecting services topic describes how to use classes as dependencies. Besides classes, you can also use other values such as Boolean, string, date, and objects as dependencies. Angular DI provides the necessary APIs to make the dependency configuration flexible, so you can make those values available in DI.



#### Specifying a provider token

If you specify the service class as the provider token, the default behavior is for the injector to instantiate that class using the `new` operator.

In the following example, the `Logger` class provides a `Logger` instance.

```typescript
providers: [Logger]
```



You can, however, configure a DI to use a different class or any other different value to associate with the `Logger` class. So when the `Logger` is injected, this new value is used instead.

In fact, the class provider syntax is a shorthand expression that expands into a provider configuration, defined by the `Provider` interface.

Angular expands the `providers` value in this case into a full provider object as follows:

```typescript
[{ provide: Logger, useClass: Logger }]
```



The expanded provider configuration is an object literal with two properties:

- The `provide` property holds the token that serves as the key for both locating a dependency value and configuring the injector.
- The second property is a provider definition object, which tells the injector how to create the dependency value. The provider-definition key can be one of the following:
  - useClass - this option tells Angular DI to instantiate a provided class when a dependency is injected
  - useExisting - allows you to alias a token and reference any existing one.
  - useFactory - allows you to define a function that constructs a dependency.
  - useValue - provides a static value that should be used as a dependency.

The section below describes how to use the mentioned provider definition keys.



##### Class providers: useClass

The `useClass` provider key lets you create and return a new instance of the specified class. You can use this type of provider to substitute an alternative implementation for a common or default class. The alternative implementation can, for example, implement a different strategy, extend the default class, or emulate the behavior of the real class in a test case. In the following example, the `BetterLogger` class would be instantiated when the `Logger` dependency is requested in a component or any other class.

```typescript
[{ provide: Logger, useClass: BetterLogger }]
```



If the alternative class providers have their own dependencies, specify both providers in the providers metadata property of the parent module or component.

```typescript
If the alternative class providers have their own dependencies, specify both providers in the providers metadata property of the parent module or component.
```



If the alternative class providers have their own dependencies, specify both providers in the providers metadata property of the parent module or component.

```typescript
@Injectable()
export class EvenBetterLogger extends Logger {
  constructor(private userService: UserService) { super(); }

  override log(message: string) {
    const name = this.userService.user.name;
    super.log(`Message to ${name}: ${message}`);
  }
}
```

Angular DI knows how to construct the `UserService` dependency, since it has been configured above and is available in the injector.





## Component interaction

This cookbook contains recipes for common component communication scenarios in which two or more components share information.



### 1.Pass data from parent to child with input binding

`HeroChildComponent` has two ***input properties\***, typically adorned with [@Input() decorator](https://angular.io/guide/inputs-outputs#input).



(component-interaction/src/app/hero-child.component.ts)

```typescript
import { Component, Input } from '@angular/core';

import { Hero } from './hero';

@Component({
  selector: 'app-hero-child',
  template: `
    <h3>{{hero.name}} says:</h3>
    <p>I, {{hero.name}}, am at your service, {{masterName}}.</p>
  `
})
export class HeroChildComponent {
  @Input() hero!: Hero;
  @Input('master') masterName = '';
}
```

The second `@Input` aliases the child component property name `masterName` as `'master'`.



**Tips**:	However,the attribute of the dom of the childComponent is still conneted with masterName,master is just an alias of masterName,for parentComponent to use.





The `HeroParentComponent` nests the child `HeroChildComponent` inside an `*ngFor` repeater, binding its `master` string property to the child's `master` alias, and each iteration's `hero` instance to the child's `hero` property.



(component-interaction/src/app/hero-parent.component.ts)

```typescript
import { Component } from '@angular/core';

import { HEROES } from './hero';

@Component({
  selector: 'app-hero-parent',
  template: `
    <h2>{{master}} controls {{heroes.length}} heroes</h2>

    <app-hero-child
      *ngFor="let hero of heroes"
      [hero]="hero"
      [master]="master">
    </app-hero-child>
  `
})
export class HeroParentComponent {
  heroes = HEROES;
  master = 'Master';
}
```



### 2.Intercept input property changes with a setter

Use an input property setter to intercept and act upon a value from the parent.

The setter of the `name` input property in the child `NameChildComponent` trims the whitespace from a name and replaces an empty value with default text.



(component-interaction/src/app/name-child.component.ts)

```typescript
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-name-child',
  template: '<h3>"{{name}}"</h3>'
})
export class NameChildComponent {
  @Input()
  get name(): string { return this._name; }
  set name(name: string) {
    this._name = (name && name.trim()) || '<no name set>';
  }
  private _name = '';
}
```



Here's the `NameParentComponent` demonstrating name variations including a name with all spaces:

(component-interaction/src/app/name-parent.component.ts)

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-name-parent',
  template: `
    <h2>Master controls {{names.length}} names</h2>

    <app-name-child *ngFor="let name of names" [name]="name"></app-name-child>
  `
})
export class NameParentComponent {
  // Displays 'Dr. IQ', '<no name set>', 'Bombasto'
  names = ['Dr. IQ', '   ', '  Bombasto  '];
}
```



### 3.Intercept input property changes with `ngOnChanges()`

Detect and act upon changes to input property values with the `ngOnChanges()` method of the `OnChanges` lifecycle hook interface.



This `VersionChildComponent` detects changes to the `major` and `minor` input properties and composes a log message reporting these changes:

(component-interaction/src/app/version-child.component.ts)

```typescript
import { Component, Input, OnChanges, SimpleChanges } from '@angular/core';

@Component({
  selector: 'app-version-child',
  template: `
    <h3>Version {{major}}.{{minor}}</h3>
    <h4>Change log:</h4>
    <ul>
      <li *ngFor="let change of changeLog">{{change}}</li>
    </ul>
  `
})
export class VersionChildComponent implements OnChanges {
  @Input() major = 0;
  @Input() minor = 0;
  changeLog: string[] = [];

  ngOnChanges(changes: SimpleChanges) {
    const log: string[] = [];
    for (const propName in changes) {
      const changedProp = changes[propName];
      const to = JSON.stringify(changedProp.currentValue);
      if (changedProp.isFirstChange()) {
        log.push(`Initial value of ${propName} set to ${to}`);
      } else {
        const from = JSON.stringify(changedProp.previousValue);
        log.push(`${propName} changed from ${from} to ${to}`);
      }
    }
    this.changeLog.push(log.join(', '));
  }
}
```



### 4.Parent listens for child event

The child component exposes an `EventEmitter` property with which it `emits` events when something happens. The parent binds to that event property and reacts to those events.

The child's `EventEmitter` property is an ***output property\***, typically adorned with an [@Output() decorator](https://angular.io/guide/inputs-outputs#output) as seen in this `VoterComponent`:

(component-interaction/src/app/voter.component.ts)

```typescript
import { Component, EventEmitter, Input, Output } from '@angular/core';

@Component({
  selector: 'app-voter',
  template: `
    <h4>{{name}}</h4>
    <button type="button" (click)="vote(true)"  [disabled]="didVote">Agree</button>
    <button type="button" (click)="vote(false)" [disabled]="didVote">Disagree</button>
  `
})
export class VoterComponent {
  @Input()  name = '';
  @Output() voted = new EventEmitter<boolean>();
  didVote = false;

  vote(agreed: boolean) {
    this.voted.emit(agreed);
    this.didVote = true;
  }
}
```

Clicking a button triggers emission of a `true` or `false`, the boolean *payload*.



The parent `VoteTakerComponent` binds an event handler called `onVoted()` that responds to the child event payload `$event` and updates a counter.

(component-interaction/src/app/votetaker.component.ts)

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-vote-taker',
  template: `
    <h2>Should mankind colonize the Universe?</h2>
    <h3>Agree: {{agreed}}, Disagree: {{disagreed}}</h3>

    <app-voter
      *ngFor="let voter of voters"
      [name]="voter"
      (voted)="onVoted($event)">
    </app-voter>
  `
})
export class VoteTakerComponent {
  agreed = 0;
  disagreed = 0;
  voters = ['Dr. IQ', 'Celeritas', 'Bombasto'];

  onVoted(agreed: boolean) {
    if (agreed) {
      this.agreed++;
    } else {
      this.disagreed++;
    }
  }
}
```

The framework passes the event argument —represented by `$event`— to the handler method, and the method processes it.





### 5.Parent interacts with child using *local variable*

A parent component cannot use data binding to read child properties or invoke child methods. Do both by creating a template reference variable for the child element and then reference that variable *within the parent template* as seen in the following example.



The following is a child `CountdownTimerComponent` that repeatedly counts down to zero and launches a rocket. The `start` and `stop` methods control the clock and a countdown status message displays in its own template.

(component-interaction/src/app/countdown-timer.component.ts)

```typescript
import { Component, OnDestroy } from '@angular/core';

@Component({
  selector: 'app-countdown-timer',
  template: '<p>{{message}}</p>'
})
export class CountdownTimerComponent implements OnDestroy {
  message = '';
  seconds = 11;

  ngOnDestroy() { this.clearTimer?.(); }

  start() { this.countDown(); }
  stop()  {
    this.clearTimer?.();
    this.message = `Holding at T-${this.seconds} seconds`;
  }

  private clearTimer: VoidFunction | undefined;

  private countDown() {
    this.clearTimer?.();
    const interval = setInterval(() => {
      this.seconds -= 1;
      if (this.seconds === 0) {
        this.message = 'Blast off!';
      } else {
        if (this.seconds < 0) { this.seconds = 10; } // reset
        this.message = `T-${this.seconds} seconds and counting`;
      }
    }, 1000);
    this.clearTimer = () => clearInterval(interval);
  }
}
```



The `CountdownLocalVarParentComponent` that hosts the timer component is as follows:

(component-interaction/src/app/countdown-parent.component.ts)

```typescript
import { Component } from '@angular/core';
import { CountdownTimerComponent } from './countdown-timer.component';

@Component({
  selector: 'app-countdown-parent-lv',
  template: `
    <h3>Countdown to Liftoff (via local variable)</h3>
    <button type="button" (click)="timer.start()">Start</button>
    <button type="button" (click)="timer.stop()">Stop</button>
    <div class="seconds">{{timer.seconds}}</div>
    <app-countdown-timer #timer></app-countdown-timer>
  `,
  styleUrls: ['../assets/demo.css']
})
export class CountdownLocalVarParentComponent { }
```

The parent component cannot data bind to the child's `start` and `stop` methods nor to its `seconds` property.

Place a local variable, `#timer`, on the tag `<app-countdown-timer>` representing the child component. That gives you a reference to the child component and the ability to access *any of its properties or methods* from within the parent template.

This example wires parent buttons to the child's `start` and `stop` and uses interpolation to display the child's `seconds` property.

Here, the parent and child are working together.



### 6.Parent calls an `@ViewChild()`

The *local variable* approach is straightforward. But it is limited because the parent-child wiring must be done entirely within the parent template. The parent component *itself* has no access to the child.

You can't use the *local variable* technique if the parent component's *class* relies on the child component's *class*. The parent-child relationship of the components is not established within each component's respective *class* with the *local variable* technique. Because the *class* instances are not connected to one another, the parent *class* cannot access the child *class* properties and methods.

When the parent component *class* requires that kind of access, ***inject\*** the child component into the parent as a *ViewChild*.

The following example illustrates this technique with the same [Countdown Timer](https://angular.io/guide/component-interaction#countdown-timer-example) example. Neither its appearance nor its behavior changes. The child [CountdownTimerComponent](https://angular.io/guide/component-interaction#countdown-timer-example) is the same as well.

```tex
The switch from the local variable to the ViewChild technique is solely for the purpose of demonstration.
```



Here is the parent, `CountdownViewChildParentComponent`:

(component-interaction/src/app/countdown-parent.component.ts)

```typescript
import { AfterViewInit, ViewChild } from '@angular/core';
import { Component } from '@angular/core';
import { CountdownTimerComponent } from './countdown-timer.component';

@Component({
  selector: 'app-countdown-parent-vc',
  template: `
    <h3>Countdown to Liftoff (via ViewChild)</h3>
    <button type="button" (click)="start()">Start</button>
    <button type="button" (click)="stop()">Stop</button>
    <div class="seconds">{{ seconds() }}</div>
    <app-countdown-timer></app-countdown-timer>
  `,
  styleUrls: ['../assets/demo.css']
})
export class CountdownViewChildParentComponent implements AfterViewInit {

  @ViewChild(CountdownTimerComponent)
  private timerComponent!: CountdownTimerComponent;

  seconds() { return 0; }

  ngAfterViewInit() {
    // Redefine `seconds()` to get from the `CountdownTimerComponent.seconds` ...
    // but wait a tick first to avoid one-time devMode
    // unidirectional-data-flow-violation error
    setTimeout(() => this.seconds = () => this.timerComponent.seconds, 0);
  }

  start() { this.timerComponent.start(); }
  stop() { this.timerComponent.stop(); }
}
```

It takes a bit more work to get the child view into the parent component *class*.

First, you have to import references to the `ViewChild` decorator and the `AfterViewInit` lifecycle hook.

Next, inject the child `CountdownTimerComponent` into the private `timerComponent` property using the `@ViewChild` property decoration.

The `#timer` local variable is gone from the component metadata. Instead, bind the buttons to the parent component's own `start` and `stop` methods and present the ticking seconds in an interpolation around the parent component's `seconds` method.

These methods access the injected timer component directly.

The `ngAfterViewInit()` lifecycle hook is an important wrinkle. The timer component isn't available until *after* Angular displays the parent view. So it displays `0` seconds initially.

Then Angular calls the `ngAfterViewInit` lifecycle hook at which time it is *too late* to update the parent view's display of the countdown seconds. Angular's unidirectional data flow rule prevents updating the parent view's in the same cycle. The application must *wait one turn* before it can display the seconds.

Use `setTimeout()` to wait one tick and then revise the `seconds()` method so that it takes future values from the timer component.



### 7.Parent and children communicate using a service

A parent component and its children share a service whose interface enables bidirectional communication *within the family*.

The scope of the service instance is the parent component and its children. Components outside this component subtree have no access to the service or their communications.

This `MissionService` connects the `MissionControlComponent` to multiple `AstronautComponent` children.



(component-interaction/src/app/mission.service.ts)

```typescript
import { Injectable } from '@angular/core';
import { Subject } from 'rxjs';

@Injectable()
export class MissionService {

  // Observable string sources
  private missionAnnouncedSource = new Subject<string>();
  private missionConfirmedSource = new Subject<string>();

  // Observable string streams
  missionAnnounced$ = this.missionAnnouncedSource.asObservable();
  missionConfirmed$ = this.missionConfirmedSource.asObservable();

  // Service message commands
  announceMission(mission: string) {
    this.missionAnnouncedSource.next(mission);
  }

  confirmMission(astronaut: string) {
    this.missionConfirmedSource.next(astronaut);
  }
}
```



The `MissionControlComponent` both provides the instance of the service that it shares with its children (through the `providers` metadata array) and injects that instance into itself through its constructor:

(component-interaction/src/app/missioncontrol.component.ts)

```typescript
import { Component } from '@angular/core';

import { MissionService } from './mission.service';

@Component({
  selector: 'app-mission-control',
  template: `
    <h2>Mission Control</h2>
    <button type="button" (click)="announce()">Announce mission</button>

    <app-astronaut
      *ngFor="let astronaut of astronauts"
      [astronaut]="astronaut">
    </app-astronaut>

    <h3>History</h3>
    <ul>
      <li *ngFor="let event of history">{{event}}</li>
    </ul>
  `,
  providers: [MissionService]
})
export class MissionControlComponent {
  astronauts = ['Lovell', 'Swigert', 'Haise'];
  history: string[] = [];
  missions = ['Fly to the moon!',
              'Fly to mars!',
              'Fly to Vegas!'];
  nextMission = 0;

  constructor(private missionService: MissionService) {
    missionService.missionConfirmed$.subscribe(
      astronaut => {
        this.history.push(`${astronaut} confirmed the mission`);
      });
  }

  announce() {
    const mission = this.missions[this.nextMission++];
    this.missionService.announceMission(mission);
    this.history.push(`Mission "${mission}" announced`);
    if (this.nextMission >= this.missions.length) { this.nextMission = 0; }
  }
}
```



The `AstronautComponent` also injects the service in its constructor. Each `AstronautComponent` is a child of the `MissionControlComponent` and therefore receives its parent's service instance:

(component-interaction/src/app/astronaut.component.ts)

```typescript
import { Component, Input, OnDestroy } from '@angular/core';

import { MissionService } from './mission.service';
import { Subscription } from 'rxjs';

@Component({
  selector: 'app-astronaut',
  template: `
    <p>
      {{astronaut}}: <strong>{{mission}}</strong>
      <button
        type="button"
        (click)="confirm()"
        [disabled]="!announced || confirmed">
        Confirm
      </button>
    </p>
  `
})
export class AstronautComponent implements OnDestroy {
  @Input() astronaut = '';
  mission = '<no mission announced>';
  confirmed = false;
  announced = false;
  subscription: Subscription;

  constructor(private missionService: MissionService) {
    this.subscription = missionService.missionAnnounced$.subscribe(
      mission => {
        this.mission = mission;
        this.announced = true;
        this.confirmed = false;
    });
  }

  confirm() {
    this.confirmed = true;
    this.missionService.confirmMission(this.astronaut);
  }

  ngOnDestroy() {
    // prevent memory leak when component destroyed
    this.subscription.unsubscribe();
  }
}
```





**Notice that this example captures the `subscription` and `unsubscribe()` when the `AstronautComponent` is destroyed. This is a memory-leak guard step. There is no actual risk in this application because the lifetime of a `AstronautComponent` is the same as the lifetime of the application itself. That *would not* always be true in a more complex application.**

**You don't add this guard to the `MissionControlComponent` because, as the parent, it controls the lifetime of the `MissionService`.**



The *History* log demonstrates that messages travel in both directions between the parent `MissionControlComponent` and the `AstronautComponent` children, facilitated by the service





## Angular Routing

In a single-page app, you change what the user sees by showing or hiding portions of the display that correspond to particular components, rather than going out to the server to get a new page. As users perform application tasks, they need to move between the different [views](https://angular.io/guide/glossary#view) that you have defined.

To handle the navigation from one [view](https://angular.io/guide/glossary#view) to the next, you use the Angular **`Router`**. The **`Router`** enables navigation by interpreting a browser URL as an instruction to change the view.

To explore a sample app featuring the router's primary features, see the [live example](https://angular.io/generated/live-examples/router/stackblitz.html) / [download example](https://angular.io/generated/zips/router/router.zip).
