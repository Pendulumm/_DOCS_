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



### Common Routing Tasks

This topic describes how to implement many of the common tasks associated with adding the Angular router to your application.



<hr/>

#### Generate an application with routing enabled

The following command uses the Angular CLI to generate a basic Angular application with an application routing module, called `AppRoutingModule`, which is an NgModule where you can configure your routes. The application name in the following example is `routing-app`.

```shell
ng new routing-app --routing --defaults
```

##### Adding components for routing

To use the Angular router, an application needs to have at least two components so that it can navigate from one to the other. To create a component using the CLI, enter the following at the command line where `first` is the name of your component:

```shell
ng generate component first
```

Repeat this step for a second component but give it a different name. Here, the new name is `second`.

```shell
ng generate component second
```

The CLI automatically appends `Component`, so if you were to write `first-component`, your component would be `FirstComponentComponent`.



```
<base href>
This guide works with a CLI-generated Angular application. If you are working manually, make sure that you have `<base href="/">` in the `<head>` of your index.html file. This assumes that the `app` folder is the application root, and uses `"/"`.
```





##### Importing your new components

To use your new components, import them into `AppRoutingModule` at the top of the file, as follows:

( AppRoutingModule (excerpt) )

```typescript
import { FirstComponent } from './first/first.component';
import { SecondComponent } from './second/second.component';
```



#### Defining a basic route

There are three fundamental building blocks to creating a route.

Import the `AppRoutingModule` into `AppModule` and add it to the `imports` array.

The Angular CLI performs this step for you. However, if you are creating an application manually or working with an existing, non-CLI application, verify that the imports and configuration are correct. The following is the default `AppModule` using the CLI with the `--routing` flag.

(Default CLI AppModule with routing)

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { AppRoutingModule } from './app-routing.module'; // CLI imports AppRoutingModule
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule // CLI adds AppRoutingModule to the AppModule's imports array
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```



1. Import `RouterModule` and `Routes` into your routing module.

   The Angular CLI performs this step automatically. The CLI also sets up a `Routes` array for your routes and configures the `imports` and `exports` arrays for `@NgModule()`.

   (CLI application routing module)

   ```typescript
   import { NgModule } from '@angular/core';
   import { Routes, RouterModule } from '@angular/router'; // CLI imports router
   
   const routes: Routes = []; // sets up routes constant where you define your routes
   
   // configures NgModule imports and exports
   @NgModule({
     imports: [RouterModule.forRoot(routes)],
     exports: [RouterModule]
   })
   export class AppRoutingModule { }
   ```

   

2. Define your routes in your `Routes` array.

   1. Each route in this array is a JavaScript object that contains two properties. The first property, `path`, defines the URL path for the route. The second property, `component`, defines the component Angular should use for the corresponding path.

   ​		(AppRoutingModule (excerpt))

   ​		

   ```typescript
   const routes: Routes = [
     { path: 'first-component', component: FirstComponent },
     { path: 'second-component', component: SecondComponent },
   ];
   ```

   

3. Add your routes to your application.

   Now that you have defined your routes, add them to your application. First, add links to the two components. Assign the anchor tag that you want to add the route to the `routerLink` attribute. Set the value of the attribute to the component to show when a user clicks on each link. Next, update your component template to include `<router-outlet>`. This element informs Angular to update the application view with the component for the selected route.

​		(Template with routerLink and router-outlet)

​		

```html
<h1>Angular Router App</h1>
<!-- This nav gives you links to click, which tells the router which route to use (defined in the routes constant in  AppRoutingModule) -->
<nav>
  <ul>
    <li><a routerLink="/first-component" routerLinkActive="active" ariaCurrentWhenActive="page">First Component</a></li>
    <li><a routerLink="/second-component" routerLinkActive="active" ariaCurrentWhenActive="page">Second Component</a></li>
  </ul>
</nav>
<!-- The routed views render in the <router-outlet>-->
<router-outlet></router-outlet>
```



##### Route order

The order of routes is important because the `Router` uses a first-match wins strategy when matching routes, so more specific routes should be placed above less specific routes. List routes with a static path first, followed by an empty path route, which matches the default route. The [wildcard route](https://angular.io/guide/router#setting-up-wildcard-routes) comes last because it matches every URL and the `Router` selects it only if no other routes match first.



#### Getting route information

Often, as a user navigates your application, you want to pass information from one component to another. For example, consider an application that displays a shopping list of grocery items. Each item in the list has a unique `id`. To edit an item, users click an Edit button, which opens an `EditGroceryItem` component. You want that component to retrieve the `id` for the grocery item so it can display the right information to the user.

Use a route to pass this type of information to your application components. To do so, you use the [withComponentInputBinding](https://angular.io/api/router/withComponentInputBinding) feature with `provideRouter` or the `bindToComponentInputs` option of `RouterModule.forRoot`.



To get information from a route:

1. Add the `withComponentInputBinding` feature to the `provideRouter` method.

   (provideRouter feature)

   ```typescript
   providers: [
     provideRouter(appRoutes, withComponentInputBinding()),
   ]
   ```

   

2. Update the component to have an `Input` matching the name of the parameter.

   ( The component input (excerpt) )

   ```typescript
   @Input()
   set id(heroId: string) {
     this.hero$ = this.service.getHero(heroId);
   }
   ```

   

#### Setting up wildcard routes

A well-functioning application should gracefully handle when users attempt to navigate to a part of your application that does not exist. To add this functionality to your application, you set up a wildcard route. The Angular router selects this route any time the requested URL doesn't match any router paths.

To set up a wildcard route, add the following code to your `routes` definition.



( AppRoutingModule (excerpt) )

```typescript
{ path: '**', component: <component-name> }
```

The two asterisks, `**`, indicate to Angular that this `routes` definition is a wildcard route. For the component property, you can define any component in your application. Common choices include an application-specific `PageNotFoundComponent`, which you can define to [display a 404 page](https://angular.io/guide/router#404-page-how-to) to your users; or a redirect to your application's main component. A wildcard route is the last route because it matches any URL. For more detail on why order matters for routes, see [Route order](https://angular.io/guide/router#route-order).



#### Displaying a 404 page

To display a 404 page, set up a [wildcard route](https://angular.io/guide/router#wildcard-route-how-to) with the `component` property set to the component you'd like to use for your 404 page as follows:



( AppRoutingModule (excerpt) )

```typescript
const routes: Routes = [
  { path: 'first-component', component: FirstComponent },
  { path: 'second-component', component: SecondComponent },
  { path: '**', component: PageNotFoundComponent },  // Wildcard route for a 404 page
];
```

The last route with the `path` of `**` is a wildcard route. The router selects this route if the requested URL doesn't match any of the paths earlier in the list and sends the user to the `PageNotFoundComponent`.



#### Setting up redirects

To set up a redirect, configure a route with the `path` you want to redirect from, the `component` you want to redirect to, and a `pathMatch` value that tells the router how to match the URL.



( AppRoutingModule (excerpt) )

```typescript
const routes: Routes = [
  { path: 'first-component', component: FirstComponent },
  { path: 'second-component', component: SecondComponent },
  { path: '',   redirectTo: '/first-component', pathMatch: 'full' }, // redirect to `first-component`
  { path: '**', component: PageNotFoundComponent },  // Wildcard route for a 404 page
];
```

In this example, the third route is a redirect so that the router defaults to the `first-component` route. Notice that this redirect precedes the wildcard route. Here, `path: ''` means to use the initial relative URL (`''`).

For more details on `pathMatch` see [Spotlight on `pathMatch`](https://angular.io/guide/router-tutorial-toh#pathmatch).



### Nesting routes

As your application grows more complex, you might want to create routes that are relative to a component other than your root component. These types of nested routes are called **child routes**. This means you're adding a second `<router-outlet>` to your app, because it is in addition to the `<router-outlet>` in `AppComponent`.

In this example, there are two additional child components, `child-a`, and `child-b`. Here, `FirstComponent` has its own `<nav>` and a second `<router-outlet>` in addition to the one in `AppComponent`.



(In the template)

```html
<h2>First Component</h2>

<nav>
  <ul>
    <li><a routerLink="child-a">Child A</a></li>
    <li><a routerLink="child-b">Child B</a></li>
  </ul>
</nav>

<router-outlet></router-outlet>
```

A child route is like any other route, in that it needs both a `path` and a `component`. The one difference is that you place child routes in a `children` array within the parent route.



### Setting the page title

Each page in your application should have a unique title so that they can be identified in the browser history. The `Router` sets the document's title using the `title` property from the `Route` config.



( AppRoutingModule (excerpt) )

```typescript
const routes: Routes = [
  {
    path: 'first-component',
    title: 'First component',
    component: FirstComponent,  // this is the component with the <router-outlet> in the template
    children: [
      {
        path: 'child-a',  // child route path
        title: resolvedChildATitle,
        component: ChildAComponent,  // child route component that the router renders
      },
      {
        path: 'child-b',
        title: 'child b',
        component: ChildBComponent,  // another child route component that the router renders
      },
    ],
  },
];

const resolvedChildATitle: ResolveFn<string> = () => Promise.resolve('child a');
```



### Using relative paths

Relative paths let you define paths that are relative to the current URL segment. The following example shows a relative route to another component, `second-component`. `FirstComponent` and `SecondComponent` are at the same level in the tree, however, the link to `SecondComponent` is situated within the `FirstComponent`, meaning that the router has to go up a level and then into the second directory to find the `SecondComponent`. Rather than writing out the whole path to get to `SecondComponent`, use the `../` notation to go up a level.



(In the template)

```html
<h2>First Component</h2>

<nav>
  <ul>
    <li><a routerLink="../second-component">Relative Route to second component</a></li>
  </ul>
</nav>
<router-outlet></router-outlet>
```

In addition to `../`, use `./` or no leading slash to specify the current level.



##### Specifying a relative route

To specify a relative route, use the `NavigationExtras` `relativeTo` property. In the component class, import `NavigationExtras` from the `@angular/router`.

Then use `relativeTo` in your navigation method. After the link parameters array, which here contains `items`, add an object with the `relativeTo` property set to the `ActivatedRoute`, which is `this.route`.



(RelativeTo)

```typescript
goToItems() {
  this.router.navigate(['items'], { relativeTo: this.route });
}
```

The `goToItems()` method interprets the destination URI as relative to the activated route and navigates to the `items` route.



### Lazy loading

You can configure your routes to lazy load modules, which means that Angular only loads modules as needed, rather than loading all modules when the application launches. Additionally, preload parts of your application in the background to improve the user experience.

For more information on lazy loading and preloading see the dedicated guide [Lazy loading NgModules](https://angular.io/guide/lazy-loading-ngmodules).



### Preventing unauthorized access

Use route guards to prevent users from navigating to parts of an application without authorization. The following route guards are available in Angular:

- [`canActivate`](https://angular.io/api/router/CanActivateFn)
- [`canActivateChild`](https://angular.io/api/router/CanActivateChildFn)
- [`canDeactivate`](https://angular.io/api/router/CanDeactivateFn)
- [`canMatch`](https://angular.io/api/router/CanMatchFn)
- [`resolve`](https://angular.io/api/router/ResolveFn)
- [`canLoad`](https://angular.io/api/router/CanLoadFn)

To use route guards, consider using [component-less routes](https://angular.io/api/router/Route#componentless-routes) as this facilitates guarding child routes.

Create a file for your guard:

```sh
ng generate guard your-guard
```

In your guard file, add the guard functions you want to use. The following example uses `canActivateFn` to guard the route.

( guard (excerpt) )

```typescript
export const yourGuardFunction: CanActivateFn = (
    next: ActivatedRouteSnapshot,
    state: RouterStateSnapshot) => {
      // your  logic goes here
  }
```

In your routing module, use the appropriate property in your `routes` configuration. Here, `canActivate` tells the router to mediate navigation to this particular route.

( Routing module (excerpt) )

```typescript
{
path: '/your-path',
component: YourComponent,
canActivate: [yourGuardFunction],
}
```

For more information with a working example, see the [routing tutorial section on route guards](https://angular.io/guide/router-tutorial-toh#milestone-5-route-guards).



### Link parameters array

A link parameters array holds the following ingredients for router navigation:

- The path of the route to the destination component
- Required and optional route parameters that go into the route URL

Bind the `RouterLink` directive to such an array like this:

( src/app/app.component.ts (h-anchor) )

```typescript
<a [routerLink]="['/heroes']">Heroes</a>
```

The following is a two-element array when specifying a route parameter:

( src/app/heroes/hero-list/hero-list.component.html (nav-to-detail) )

```typescript
<a [routerLink]="['/hero', hero.id]">
  <span class="badge">{{ hero.id }}</span>{{ hero.name }}
</a>
```

Provide optional route parameters in an object, as in `{ foo: 'foo' }`:

( src/app/app.component.ts (cc-query-params) )

```typescript
<a [routerLink]="['/crisis-center', { foo: 'foo' }]">Crisis Center</a>
```

These three examples cover the needs of an application with one level of routing. However, with a child router, such as in the crisis center, you create new link array possibilities.



The following minimal `RouterLink` example builds upon a specified [default child route](https://angular.io/guide/router-tutorial-toh#a-crisis-center-with-child-routes) for the crisis center.

( src/app/app.component.ts (cc-anchor-w-default) )

```html
<a [routerLink]="['/crisis-center']">Crisis Center</a>
```

Review the following:

- The first item in the array identifies the parent route (`/crisis-center`)
- There are no parameters for this parent route
- There is no default for the child route so you need to pick one
- You're navigating to the `CrisisListComponent`, whose route path is `/`, but you don't need to explicitly add the slash



Consider the following router link that navigates from the root of the application down to the Dragon Crisis:

( src/app/app.component.ts (Dragon-anchor) )

```html
<a [routerLink]="['/crisis-center', 1]">Dragon Crisis</a>
```



- The first item in the array identifies the parent route (`/crisis-center`)
- There are no parameters for this parent route
- The second item identifies the child route details about a particular crisis (`/:id`)
- The details child route requires an `id` route parameter
- You added the `id` of the Dragon Crisis as the second item in the array (`1`)
- The resulting path is `/crisis-center/1`



You could also redefine the `AppComponent` template with Crisis Center routes exclusively:

( src/app/app.component.ts (template) )

```typescript
template: `
  <h1 class="title">Angular Router</h1>
  <nav>
    <a [routerLink]="['/crisis-center']">Crisis Center</a>
    <a [routerLink]="['/crisis-center/1', { foo: 'foo' }]">Dragon Crisis</a>
    <a [routerLink]="['/crisis-center/2']">Shark Crisis</a>
  </nav>
  <router-outlet></router-outlet>
`
```







### `LocationStrategy` and browser URL styles

When the router navigates to a new component view, it updates the browser's location and history with a URL for that view.

Modern HTML5 browsers support [history.pushState](https://developer.mozilla.org/docs/Web/API/History_API/Working_with_the_History_API#adding_and_modifying_history_entries), a technique that changes a browser's location and history without triggering a server page request. The router can compose a "natural" URL that is indistinguishable from one that would otherwise require a page load.



Here's the Crisis Center URL in this "HTML5 pushState" style:

```http
localhost:3002/crisis-center
```

Older browsers send page requests to the server when the location URL changes unless the change occurs after a "#" (called the "hash"). Routers can take advantage of this exception by composing in-application route URLs with hashes. Here's a "hash URL" that routes to the Crisis Center.

```http
localhost:3002/src/#/crisis-center
```

The router supports both styles with two `LocationStrategy` providers:

| PROVIDERS              | DETAILS                              |
| :--------------------- | :----------------------------------- |
| `PathLocationStrategy` | The default "HTML5 pushState" style. |
| `HashLocationStrategy` | The "hash URL" style.                |



The `RouterModule.forRoot()` function sets the `LocationStrategy` to the `PathLocationStrategy`, which makes it the default strategy. You also have the option of switching to the `HashLocationStrategy` with an override during the bootstrapping process.



## Routing Tutorial



### 1.Using Angular routes in a single-page application

This tutorial describes how to build a single-page application, SPA that uses multiple Angular routes.

In a Single Page Application (SPA), all of your application's functions exist in a single HTML page. As users access your application's features, the browser needs to render only the parts that matter to the user, instead of loading a new page. This pattern can significantly improve your application's user experience.

To define how users navigate through your application, you use routes. Add routes to define how users navigate from one part of your application to another. You can also configure routes to guard against unexpected or unauthorized behavior.

To explore a sample application featuring the contents of this tutorial, see the [live example](https://angular.io/generated/live-examples/router-tutorial/stackblitz.html) / [download example](https://angular.io/generated/zips/router-tutorial/router-tutorial.zip).



<hr>

#### Objectives

- Organize a sample application's features into modules.
- Define how to navigate to a component.
- **Pass information** to a component using a parameter.
- Structure routes by nesting several routes.
- Check whether users can access a route.
- Control whether the application can discard unsaved changes.
- Improve performance by pre-fetching route data and lazy loading feature modules.
- Require specific criteria to load components.

#### Prerequisites

To complete this tutorial, you should have a basic understanding of the following concepts:

- JavaScript
- HTML
- CSS
- [Angular CLI](https://angular.io/cli)

You might find the [Tour of Heroes tutorial](https://angular.io/tutorial/tour-of-heroes) helpful, but it is not required.



#### Create a sample application

Using the Angular CLI, create a new application, *angular-router-sample*. This application will have two components: *crisis-list* and *heroes-list*.

1. Create a new Angular project, *angular-router-sample*.

   ```shell
   ng new angular-router-sample
   ```

   When prompted with `Would you like to add Angular routing?`, select `N`.

   When prompted with `Which stylesheet format would you like to use?`, select `CSS`.

   After a few moments, a new project, `angular-router-sample`, is ready.

2. From your terminal, navigate to the `angular-router-sample` directory.

3. Create a component, *crisis-list*.

   ```shell
   ng generate component crisis-list
   ```

4. In your code editor, locate the file, `crisis-list.component.html` and replace the placeholder content with the following HTML.

   ( src/app/crisis-list/crisis-list.component.html )

   ```html
   <h3>CRISIS CENTER</h3>
   <p>Get your crisis here</p>
   ```

5. Create a second component, *heroes-list*.

   ```shell
   ng generate component heroes-list
   ```

6. In your code editor, locate the file, `heroes-list.component.html` and replace the placeholder content with the following HTML.

   ( src/app/heroes-list/heroes-list.component.html )

   ```html
   <h3>HEROES</h3>
   <p>Get your heroes here</p>
   ```

7. In your code editor, open the file, `app.component.html` and replace its contents with the following HTML.

   (src/app/app.component.html)

   ```html
   <h1>Angular Router Sample</h1>
   <app-crisis-list></app-crisis-list>
   <app-heroes-list></app-heroes-list>
   ```

8. Verify that your new application runs as expected by running the `ng serve` command.

   ```shell
   ng serve
   ```

9. Open a browser to `http://localhost:4200`.

   You should see a single web page, consisting of a title and the HTML of your two components.

   

   <hr>
   
   

#### Import `RouterModule` from `@angular/router`

Routing lets you display specific views of your application depending on the URL path. To add this functionality to your sample application, you need to update the `app.module.ts` file to use the module, `RouterModule`. You import this module from `@angular/router`.

1. From your code editor, open the `app.module.ts` file.

2. Add the following `import` statement.

   (src/app/app.module.ts)

   ```typescript
   import { RouterModule } from '@angular/router';
   ```


<hr>



#### Define your routes

In this section, you'll define two routes:

- The route `/crisis-center` opens the `crisis-center` component.
- The route `/heroes-list` opens the `heroes-list` component.

A route definition is a JavaScript object. Each route typically has two properties. The first property, `path`, is a string that specifies the URL path for the route. The second property, `component`, is a string that specifies what component your application should display for that path.

1. From your code editor, open the `app.module.ts` file.

2. Locate the `@NgModule()` section.

3. Replace the `imports` array in that section with the following.

   (src/app/app.module.ts)

   ```typescript
   imports: [
     BrowserModule,
     RouterModule.forRoot([
       {path: 'crisis-list', component: CrisisListComponent},
       {path: 'heroes-list', component: HeroesListComponent},
     ]),
   ],
   ```

This code adds the `RouterModule` to the `imports` array. Next, the code uses the `forRoot()` method of the `RouterModule` to define your two routes. This method takes an array of JavaScript objects, with each object defining the properties of a route. The `forRoot()` method ensures that your application only instantiates one `RouterModule`. For more information, see [Singleton Services](https://angular.io/guide/singleton-services#forroot-and-the-router).



<hr>



#### Update your component with `router-outlet`

At this point, you have defined two routes for your application. However, your application still has both the `crisis-list` and `heroes-list` components hard-coded in your `app.component.html` template. For your routes to work, you need to update your template to dynamically load a component based on the URL path.

To implement this functionality, you add the `router-outlet` directive to your template file.

1. From your code editor, open the `app.component.html` file.

2. Delete the following lines.

   (src/app/app.component.html)

   ```html
   <app-crisis-list></app-crisis-list>
   <app-heroes-list></app-heroes-list>
   ```

3. Add the `router-outlet` directive.

   (src/app/app.component.html)

   ```html
   <router-outlet></router-outlet>
   ```

   

View your updated application in your browser. You should see only the application title. To view the `crisis-list` component, add `crisis-list` to the end of the path in your browser's address bar. For example:

```http
http://localhost:4200/crisis-list
```

Notice that the `crisis-list` component displays. Angular is using the route you defined to dynamically load the component. You can load the `heroes-list` component the same way:

```http
http://localhost:4200/heroes-list
```



<hr>



#### Control navigation with UI elements

Currently, your application supports two routes. However, the only way to use those routes is for the user to manually type the path in the browser's address bar. In this section, you'll add two links that users can click to navigate between the `heroes-list` and `crisis-list` components. You'll also add some CSS styles. While these styles are not required, they make it easier to identify the link for the currently-displayed component. You'll add that functionality in the next section.

1. Open the `app.component.html` file and add the following HTML below the title.

   (src/app/app.component.html)

   ```html
   <nav>
     <a class="button" routerLink="/crisis-list">Crisis Center</a> |
     <a class="button" routerLink="/heroes-list">Heroes</a>
   </nav>
   ```

   This HTML uses an Angular directive, `routerLink`. This directive connects the routes you defined to your template files.

2. Open the `app.component.css` file and add the following styles.

   (src/app/app.component.css)

   ```css
   .button {
       box-shadow: inset 0 1px 0 0 #ffffff;
       background: #ffffff linear-gradient(to bottom, #ffffff 5%, #f6f6f6 100%);
       border-radius: 6px;
       border: 1px solid #dcdcdc;
       display: inline-block;
       cursor: pointer;
       color: #666666;
       font-family: Arial, sans-serif;
       font-size: 15px;
       font-weight: bold;
       padding: 6px 24px;
       text-decoration: none;
       text-shadow: 0 1px 0 #ffffff;
       outline: 0;
   }
   .activebutton {
       box-shadow: inset 0 1px 0 0 #dcecfb;
       background: #bddbfa linear-gradient(to bottom, #bddbfa 5%, #80b5ea 100%);
       border: 1px solid #84bbf3;
       color: #ffffff;
       text-shadow: 0 1px 0 #528ecc;
   }
   ```

If you view your application in the browser, you should see these two links. When you click on a link, the corresponding component appears.



<hr>

#### Identify the active route

While users can navigate your application using the links you added in the previous section, they don't have a straightforward way to identify what the active route is. Add this functionality using Angular's `routerLinkActive` directive.

1. From your code editor, open the `app.component.html` file.

2. Update the anchor tags to include the `routerLinkActive` directive.

   (src/app/app.component.html)

   ```html
   <nav>
     <a class="button"
        routerLink="/crisis-list"
        routerLinkActive="activebutton"
        ariaCurrentWhenActive="page">
       Crisis Center
     </a> |
     <a class="button"
        routerLink="/heroes-list"
        routerLinkActive="activebutton"
        ariaCurrentWhenActive="page">
       Heroes
     </a>
   </nav>
   ```

   View your application again. As you click one of the buttons, the style for that button updates automatically, identifying the active component to the user. By adding the `routerLinkActive` directive, you inform your application to apply a specific CSS class to the active route. In this tutorial, that CSS class is `activebutton`, but you could use any class that you want.

   Note that we are also specifying a value for the `routerLinkActive`'s `ariaCurrentWhenActive`. This makes sure that visually impaired users (which may not perceive the different styling being applied) can also identify the active button. For more information see the Accessibility Best Practices [Active links identification section](https://angular.io/guide/accessibility#active-links-identification).



<hr>



#### Adding a redirect

In this step of the tutorial, you add a route that redirects the user to display the `/heroes-list` component.

1. From your code editor, open the `app.module.ts` file.

2. In the `imports` array, update the `RouterModule` section as follows.

   (src/app/app.module.ts)

   ```typescript
   imports: [
     BrowserModule,
     RouterModule.forRoot([
       {path: 'crisis-list', component: CrisisListComponent},
       {path: 'heroes-list', component: HeroesListComponent},
       {path: '', redirectTo: '/heroes-list', pathMatch: 'full'},
     ]),
   ],
   ```

   Notice that this new route uses an empty string as its path.

    In addition, it replaces the `component` property with two new ones:

   | PROPERTIES   | DETAILS                                                      |
   | :----------- | :----------------------------------------------------------- |
   | `redirectTo` | This property instructs Angular to redirect from an empty path to the `heroes-list` path. |
   | `pathMatch`  | This property instructs Angular on how much of the URL to match. For this tutorial, you should set this property to `full`. This strategy is recommended when you have an empty string for a path. For more information about this property, see the [Route API documentation](https://angular.io/api/router/Route). |



Now when you open your application, it displays the `heroes-list` component by default.



<hr>

#### Adding a 404 page

It is possible for a user to try to access a route that you have not defined. To account for this behavior, the best practice is to display a 404 page. In this section, you'll create a 404 page and update your route configuration to show that page for any unspecified routes.

1. From the terminal, create a new component, `PageNotFound`.

   ```shell
   ng generate component page-not-found
   ```

2. From your code editor, open the `page-not-found.component.html` file and replace its contents with the following HTML.

   (src/app/page-not-found/page-not-found.component.html)

   ```html
   <h2>Page Not Found</h2>
   <p>We couldn't find that page! Not even with x-ray vision.</p>
   ```

3. Open the `app.module.ts` file. In the `imports` array, update the `RouterModule` section as follows.

   (src/app/app.module.ts)

   ```typescript
   imports: [
     BrowserModule,
     RouterModule.forRoot([
       {path: 'crisis-list', component: CrisisListComponent},
       {path: 'heroes-list', component: HeroesListComponent},
       {path: '', redirectTo: '/heroes-list', pathMatch: 'full'},
       {path: '**', component: PageNotFoundComponent}
     ]),
   ],
   ```

   The new route uses a path, `**`. This path is how Angular identifies a wildcard route. Any route that does not match an existing route in your configuration will use this route.

   

   **Notice that the wildcard route is placed at the end of the array. The order of your routes is important, as Angular applies routes in order and uses the first match it finds.**



Try navigating to a non-existing route on your application, such as `http://localhost:4200/powers`. This route doesn't match anything defined in your `app.module.ts` file. However, because you defined a wildcard route, the application automatically displays your `PageNotFound` component.



<hr>



### 2.Creating custom route matches

The Angular Router supports a powerful matching strategy that you can use to help users navigate your application. This matching strategy supports static routes, variable routes with parameters, wildcard routes, and so on. Also, build your own custom pattern matching for situations in which the URLs are more complicated.

In this tutorial, you'll build a custom route matcher using Angular's `UrlMatcher`. This matcher looks for a Twitter handle in the URL.

For a working example of the final version of this tutorial, see the [live example](https://angular.io/generated/live-examples/routing-with-urlmatcher/stackblitz.html) / [download example](https://angular.io/generated/zips/routing-with-urlmatcher/routing-with-urlmatcher.zip).



<hr>



#### Objectives

Implement Angular's `UrlMatcher` to create a custom route matcher.

#### Prerequisites

To complete this tutorial, you should have a basic understanding of the following concepts:

- JavaScript
- HTML
- CSS
- [Angular CLI](https://angular.io/cli)

If you are unfamiliar with how Angular's router works, review [Using Angular routes in a single-page application](https://angular.io/guide/router-tutorial).



<hr>



#### Create a sample application

Using the Angular CLI, create a new application, *angular-custom-route-match*. In addition to the default Angular application framework, you will also create a *profile* component.

1. Create a new Angular project, *angular-custom-route-match*.

   ```shell
   ng new angular-custom-route-match
   ```

   When prompted with `Would you like to add Angular routing?`, select `Y`.

   When prompted with `Which stylesheet format would you like to use?`, select `CSS`.

   After a few moments, a new project, `angular-custom-route-match`, is ready.

2. From your terminal, navigate to the `angular-custom-route-match` directory.

3. Create a component, *profile*.

   ```shell
   ng generate component profile
   ```

4. In your code editor, locate the file, `profile.component.html` and replace the placeholder content with the following HTML.

   (src/app/profile/profile.component.html)

   ```html
   content_copy<p>
   Hello {{ username$ | async }}!
   </p>
   ```

5. In your code editor, locate the file, `app.component.html` and replace the placeholder content with the following HTML.

   (src/app/app.component.html)

   ```html
   <h2>Routing with Custom Matching</h2>
   
   Navigate to <a routerLink="/@Angular">my profile</a>
   
   <router-outlet></router-outlet>
   ```

   

<hr>



#### Configure your routes for your application

With your application framework in place, you next need to add routing capabilities to the `app.module.ts` file. As a part of this process, you will create a custom URL matcher that looks for a **Twitter handle** ('@') in the URL. This handle is identified by a preceding `@` symbol.

1. In your code editor, open your `app.module.ts` file.

2. Add an `import` statement for Angular's `RouterModule` and `UrlMatcher`.

   (src/app/app.module.ts)

   ```typescript
   import { RouterModule, UrlSegment } from '@angular/router';
   ```

3. In the imports array, add a `RouterModule.forRoot([])` statement.

   (src/app/app.module.ts)

   ```typescript
   @NgModule({
     imports:      [
       BrowserModule,
       FormsModule,
       RouterModule.forRoot([
   /* . . . */
       ])],
     declarations: [ AppComponent, ProfileComponent ],
     bootstrap:    [ AppComponent ]
   })
   ```

4. Define the custom route matcher by adding the following code to the `RouterModule.forRoot()` statement.

   (src/app/app.module.ts)

   ```typescript
   {
     matcher: (url) => {
       if (url.length === 1 && url[0].path.match(/^@[\w]+$/gm)) {
         return {
           consumed: url,
           posParams: {
             username: new UrlSegment(url[0].path.slice(1), {})
           }
         };
       }
   
       return null;
     },
     component: ProfileComponent
   }
   ```

   

This custom matcher is a function that performs the following tasks:

- The matcher verifies that the array contains only one segment
- The matcher employs a regular expression to ensure that the format of the username is a match
- If there is a match, the function returns the entire URL, defining a `username` route parameter as a substring of the path
- If there isn't a match, the function returns null and the router continues to look for other routes that match the URL

A custom URL matcher behaves like any other route definition. Define child routes or lazy loaded routes as you would with any other route.



<hr>



#### Subscribe to the route parameters

With the custom matcher in place, you now need to subscribe to the route parameters in the `profile` component.

1. In your code editor, open your `profile.component.ts` file.

2. Add an `import` statement for Angular's `ActivatedRoute` and `ParamMap`.

   (src/app/profile/profile.component.ts)

   ```typescript
   import { ActivatedRoute, ParamMap } from '@angular/router';
   ```

3. Add an `import` statement for RxJS `map`.

   (src/app/profile/profile.component.ts)

   ```typescript
   import { map } from 'rxjs/operators';
   ```

4. Subscribe to the `username` route parameter.

   (src/app/profile/profile.component.ts)

   ```typescript
   username$ = this.route.paramMap
     .pipe(
       map((params: ParamMap) => params.get('username'))
     );
   ```

5. Inject the `ActivatedRoute` into the component's constructor.

   (src/app/profile/profile.component.ts)

   ```typescript
   constructor(private route: ActivatedRoute) { }
   ```





<hr>



### 3.Tour of Heroes



This tutorial provides an extensive overview of the Angular router. In this tutorial, you build upon a basic router configuration to explore features such as child routes, route parameters, lazy load NgModules, guard routes, and preloading data to improve the user experience.

For a working example of the final version of the app, see the [live example](https://angular.io/generated/live-examples/router/stackblitz.html) / [download example](https://angular.io/generated/zips/router/router.zip).



<hr>

#### Objectives

This guide describes development of a multi-page routed sample application. Along the way, it highlights key features of the router such as:

- Organizing the application features into modules
- Navigating to a component (*Heroes* link to "Heroes List")
- Including a route parameter (passing the Hero `id` while routing to the "Hero Detail")
- Child routes (the *Crisis Center* has its own routes)
- The `canActivate` guard (checking route access)
- The `canActivateChild` guard (checking child route access)
- The `canDeactivate` guard (ask permission to discard unsaved changes)
- The `resolve` guard (pre-fetching route data)
- Lazy loading an `NgModule`
- The `canMatch` guard (check before loading feature module assets)

This guide proceeds as a sequence of milestones as if you were building the application step-by-step, but assumes you are familiar with basic [Angular concepts](https://angular.io/guide/architecture). For a general introduction to angular, see the [Getting Started](https://angular.io/start). For a more in-depth overview, see the [Tour of Heroes](https://angular.io/tutorial/tour-of-heroes) tutorial.

#### Prerequisites

To complete this tutorial, you should have a basic understanding of the following concepts:

- JavaScript
- HTML
- CSS
- [Angular CLI](https://angular.io/cli)

You might find the [Tour of Heroes tutorial](https://angular.io/tutorial/tour-of-heroes) helpful, but it is not required.



<hr>



#### The sample application in action

The sample application for this tutorial helps the Hero Employment Agency find crises for heroes to solve.

The application has three main feature areas:

1. A *Crisis Center* for maintaining the list of crises for assignment to heroes.
2. A *Heroes* area for maintaining the list of heroes employed by the agency.
3. An *Admin* area to manage the list of crises and heroes.

Try it by clicking on this [live example link](https://angular.io/generated/live-examples/router/stackblitz.html) / [download example](https://angular.io/generated/zips/router/router.zip).

The application renders with a row of navigation buttons and the *Heroes* view with its list of heroes.



![](./img/hero-list.gif) 



Select one hero and the application takes you to a hero editing screen.

![](./img/hero-detail.png) 

Alter the name. Click the "Back" button and the application returns to the heroes list which displays the changed hero name. Notice that the name change took effect immediately.

Had you clicked the browser's back button instead of the application's "Back" button, the application would have returned you to the heroes list as well. Angular application navigation updates the browser history as normal web navigation does.

Now click the *Crisis Center* link for a list of ongoing crises.

![](./img/crisis-center-list.gif) 



Select a crisis and the application takes you to a crisis editing screen. The *Crisis Detail* appears in a child component on the same page, beneath the list.

Alter the name of a crisis. Notice that the corresponding name in the crisis list does *not* change.

![](./img/crisis-center-detail.gif) 

Unlike *Hero Detail*, which updates as you type, *Crisis Detail* changes are temporary until you either save or discard them by pressing the "Save" or "Cancel" buttons. Both buttons navigate back to the *Crisis Center* and its list of crises.



Click the browser back button or the "Heroes" link to activate a dialog.

![](./img/confirm-dialog.png) 

You can say "OK" and lose your changes or click "Cancel" and continue editing.

Behind this behavior is the router's `canDeactivate` guard. The guard gives you a chance to clean up or ask the user's permission before navigating away from the current view.

The `Admin` and `Login` buttons illustrate other router capabilities covered later in the guide.

<hr>

#### Milestone 1: Getting started

Begin with a basic version of the application that navigates between two empty views.

![](./img/router-1-anim.gif) 



##### Create a sample application

1. Create a new Angular project, *angular-router-tour-of-heroes*.

   ```shell
   ng new angular-router-tour-of-heroes
   ```

   When prompted with `Would you like to add Angular routing?`, select `N`.

   When prompted with `Which stylesheet format would you like to use?`, select `CSS`.

   After a few moments, a new project, `angular-router-tour-of-heroes`, is ready.

2. From your terminal, navigate to the `angular-router-tour-of-heroes` directory.

3. Verify that your new application runs as expected by running the `ng serve` command.

   ```shell
   ng serve
   ```

4. Open a browser to `http://localhost:4200`.

   You should see the application running in your browser.



##### 1.Define Routes

A router must be configured with a list of route definitions.

Each definition translates to a [Route](https://angular.io/api/router/Route) object which has two things: a `path`, the URL path segment for this route; and a `component`, the component associated with this route.

The router draws upon its registry of definitions when the browser URL changes or when application code tells the router to navigate along a route path.

The first route does the following:

- When the browser's location URL changes to match the path segment `/crisis-center`, then the router activates an instance of the `CrisisListComponent` and displays its view
- When the application requests navigation to the path `/crisis-center`, the router activates an instance of `CrisisListComponent`, displays its view, and updates the browser's address location and history with the URL for that path

The first configuration defines an array of two routes with minimal paths leading to the `CrisisListComponent` and `HeroListComponent`.



Generate the `CrisisList` and `HeroList` components so that the router has something to render.

```shell
ng generate component crisis-list
ng generate component hero-list
```



Replace the contents of each component with the following sample HTML.

(src/app/crisis-list/crisis-list.component.html)

```html
<h2>CRISIS CENTER</h2>
<p>Get your crisis here</p>
```

(src/app/hero-list/hero-list.component.html)

```html
<h2>HEROES</h2>
<p>Get your heroes here</p>
```



##### 2.Register `Router` and `Routes`

To use the `Router`, you must first register the `RouterModule` from the `@angular/router` package. Define an array of routes, `appRoutes`, and pass them to the `RouterModule.forRoot()` method. The `RouterModule.forRoot()` method returns a module that contains the configured `Router` service provider, plus other providers that the routing library requires. Once the application is bootstrapped, the `Router` performs the initial navigation based on the current browser URL.



**NOTE**:
**The `RouterModule.forRoot()` method is a pattern used to register application-wide providers. Read more about application-wide providers in the [Singleton services](https://angular.io/guide/singleton-services#forRoot-router) guide.**





( src/app/app.module.ts (first-config) ) 

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';
import { RouterModule, Routes } from '@angular/router';

import { AppComponent } from './app.component';
import { CrisisListComponent } from './crisis-list/crisis-list.component';
import { HeroListComponent } from './hero-list/hero-list.component';

const appRoutes: Routes = [
  { path: 'crisis-center', component: CrisisListComponent },
  { path: 'heroes', component: HeroListComponent },
];

@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
    RouterModule.forRoot(
      appRoutes,
      { enableTracing: true } // <-- debugging purposes only
    )
  ],
  declarations: [
    AppComponent,
    HeroListComponent,
    CrisisListComponent,
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
```



Adding the configured `RouterModule` to the `AppModule` is sufficient for minimal route configurations. 

**However, as the application grows, [refactor the routing configuration](https://angular.io/guide/router-tutorial-toh#refactor-the-routing-configuration-into-a-routing-module) into a separate file and create a [Routing Module](https://angular.io/guide/router-tutorial-toh#routing-module). A routing module is a special type of `Service Module` dedicated to routing.**



Registering the `RouterModule.forRoot()` in the `AppModule` `imports` array makes the `Router` service available everywhere in the application.



##### 3.Add the Router Outlet

The root `AppComponent` is the application shell. It has a title, a navigation bar with two links, and a router outlet where the router renders components.

![](./img/shell-and-outlet.gif) 

The router outlet serves as a placeholder where the routed components are rendered.



The corresponding component template looks like this:

( src/app/app.component.html )

```html
<h1>Angular Router</h1>
<nav>
  <a routerLink="/crisis-center" routerLinkActive="active" ariaCurrentWhenActive="page">Crisis Center</a>
  <a routerLink="/heroes" routerLinkActive="active" ariaCurrentWhenActive="page">Heroes</a>
</nav>
<router-outlet></router-outlet>
```



##### Define a Wildcard route

You've created two routes in the application so far, one to `/crisis-center` and the other to `/heroes`. Any other URL causes the router to throw an error and crash the app.

Add a wildcard route to intercept invalid URLs and handle them gracefully. A wildcard route has a path consisting of two asterisks. It matches every URL. Thus, the router selects this wildcard route if it can't match a route earlier in the configuration. A wildcard route can navigate to a custom "404 Not Found" component or [redirect](https://angular.io/guide/router-tutorial-toh#redirect) to an existing route.



**The router selects the route with a [*first match wins*](https://angular.io/guide/router-reference#example-config) strategy. Because a wildcard route is the least specific route, place it last in the route configuration.**



To test this feature, add a button with a `RouterLink` to the `HeroListComponent` template and set the link to a non-existent route called `"/sidekicks"`.

( src/app/hero-list/hero-list.component.html (excerpt) )

```html
<h2>HEROES</h2>
<p>Get your heroes here</p>

<button type="button" routerLink="/sidekicks">Go to sidekicks</button>
```

The application fails if the user clicks that button because you haven't defined a `"/sidekicks"` route yet.

Instead of adding the `"/sidekicks"` route, define a `wildcard` route and have it navigate to a `PageNotFoundComponent`.

( src/app/app.module.ts (wildcard) )

```typescript
{ path: '**', component: PageNotFoundComponent }
```

Create the `PageNotFoundComponent` to display when users visit invalid URLs.

```shell
ng generate component page-not-found
```



( src/app/page-not-found.component.html (404 component) )

```html
<h2>Page not found</h2>
```

Now when the user visits `/sidekicks`, or any other invalid URL, the browser displays "Page not found". The browser address bar continues to point to the invalid URL.



##### Set up redirects

When the application launches, the initial URL in the browser bar is by default:

```http
localhost:4200
```

That doesn't match any of the hard-coded routes which means the router falls through to the wildcard route and displays the `PageNotFoundComponent`.

The application needs a default route to a valid page. The default page for this application is the list of heroes. The application should navigate there as if the user clicked the "Heroes" link or pasted `localhost:4200/heroes` into the address bar.

Add a `redirect` route that translates the initial relative URL (`''`) to the default path (`/heroes`) you want.

Add the default route somewhere *above* the wildcard route. It's just above the wildcard route in the following excerpt showing the complete `appRoutes` for this milestone.



( src/app/app-routing.module.ts (appRoutes) )

```typescript
const appRoutes: Routes = [
  { path: 'crisis-center', component: CrisisListComponent },
  { path: 'heroes',        component: HeroListComponent },
  { path: '',   redirectTo: '/heroes', pathMatch: 'full' },
  { path: '**', component: PageNotFoundComponent }
];
```

The browser address bar shows `.../heroes` as if you'd navigated there directly.



A redirect route requires a `pathMatch` property to tell the router how to match a URL to the path of a route. In this app, the router should select the route to the `HeroListComponent` only when the *entire URL* matches `''`, so set the `pathMatch` value to `'full'`.



**SPOTLIGHT ON PATHMATCH**

Technically, `pathMatch = 'full'` results in a route hit when the *remaining*, unmatched segments of the URL match `''`. In this example, the redirect is in a top level route so the *remaining* URL and the *entire* URL are the same thing.

The other possible `pathMatch` value is `'prefix'` which tells the router to match the redirect route when the remaining URL begins with the redirect route's prefix path. This doesn't apply to this sample application because if the `pathMatch` value were `'prefix'`, every URL would match `''`.

Try setting it to `'prefix'` and clicking the `Go to sidekicks` button. Because that's a bad URL, you should see the "Page not found" page. Instead, you're still on the "Heroes" page. Enter a bad URL in the browser address bar. You're instantly re-routed to `/heroes`. Every URL, good or bad, that falls through to this route definition is a match.

The default route should redirect to the `HeroListComponent` only when the entire url is `''`. Remember to restore the redirect to `pathMatch = 'full'`.

Learn more in Victor Savkin's [post on redirects](https://vsavkin.tumblr.com/post/146722301646/angular-router-empty-paths-componentless-routes).



##### Milestone 1 wrap up

Your sample application can switch between two views when the user clicks a link.

Milestone 1 covered how to do the following:

- Load the router library
- Add a nav bar to the shell template with anchor tags, `routerLink` and `routerLinkActive` directives
- Add a `router-outlet` to the shell template where views are displayed
- Configure the router module with `RouterModule.forRoot()`
- Set the router to compose HTML5 browser URLs
- Handle invalid routes with a `wildcard` route
- Navigate to the default route when the application launches with an empty path

The starter application's structure looks like this:

![](./img/starter_struc.png) 



(app.component.html)

```html
<h1>Angular Router</h1>
<nav>
  <a routerLink="/crisis-center" routerLinkActive="active" ariaCurrentWhenActive="page">Crisis Center</a>
  <a routerLink="/heroes" routerLinkActive="active" ariaCurrentWhenActive="page">Heroes</a>
</nav>
<router-outlet></router-outlet>
```



(app.module.ts)

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';
import { RouterModule, Routes } from '@angular/router';

import { AppComponent } from './app.component';
import { CrisisListComponent } from './crisis-list/crisis-list.component';
import { HeroListComponent } from './hero-list/hero-list.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

const appRoutes: Routes = [
  { path: 'crisis-center', component: CrisisListComponent },
  { path: 'heroes', component: HeroListComponent },

  { path: '',   redirectTo: '/heroes', pathMatch: 'full' },
  { path: '**', component: PageNotFoundComponent }
];

@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
    RouterModule.forRoot(
      appRoutes,
      { enableTracing: true } // <-- debugging purposes only
    )
  ],
  declarations: [
    AppComponent,
    HeroListComponent,
    CrisisListComponent,
    PageNotFoundComponent
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
```



(hero-list/hero-list.component.html)

```typescript
<h2>HEROES</h2>
<p>Get your heroes here</p>

<button type="button" routerLink="/sidekicks">Go to sidekicks</button>
```



(crisis-list/crisis-list.component.html)

```html
<h2>CRISIS CENTER</h2>
<p>Get your crisis here</p>
```



(page-not-found/page-not-found.component.html)

```html
<h2>Page not found</h2>
```



(index.html)

```html
<html lang="en">
  <head>
    <!-- Set the base href -->
    <base href="/">
    <title>Angular Router</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
  </head>

  <body>
    <app-root></app-root>
  </body>

</html>
```





#### Milestone 2: *Routing module*

This milestone shows you how to configure a special-purpose module called a *Routing Module*, which holds your application's routing configuration.

The Routing Module has several characteristics:

- Separates routing concerns from other application concerns
- Provides a module to replace or remove when testing the application
- Provides a well-known location for routing service providers such as guards and resolvers
- Does not declare components



##### Integrate routing with your app

The sample routing application does not include routing by default. When you use the [Angular CLI](https://angular.io/cli) to create a project that does use routing, set the `--routing` option for the project or application, and for each NgModule. When you create or initialize a new project (using the CLI [`ng new`](https://angular.io/cli/new) command) or a new application (using the [`ng generate app`](https://angular.io/cli/generate) command), specify the `--routing` option. This tells the CLI to include the `@angular/router` npm package and create a file named `app-routing.module.ts`. You can then use routing in any NgModule that you add to the project or application.

For example, the following command generates an NgModule that can use routing.

```shell
ng generate module my-module --routing
```

This creates a separate file named `my-module-routing.module.ts` to store the NgModule's routes. The file includes an empty `Routes` object that you can fill with routes to different components and NgModules.



##### Refactor the routing configuration into a routing module

Create an `AppRouting` module in the `/app` folder to contain the routing configuration.

```shell
ng generate module app-routing --module app --flat
```

Import the `CrisisListComponent`, `HeroListComponent`, and `PageNotFoundComponent` symbols like you did in the `app.module.ts`. Then move the `Router` imports and routing configuration, including `RouterModule.forRoot()`, into this routing module.

Re-export the Angular `RouterModule` by adding it to the module `exports` array. By re-exporting the `RouterModule` here, the components declared in `AppModule` have access to router directives such as `RouterLink` and `RouterOutlet`.



After these steps, the file should look like this.

(src/app/app-routing.module.ts)

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

import { CrisisListComponent } from './crisis-list/crisis-list.component';
import { HeroListComponent } from './hero-list/hero-list.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

const appRoutes: Routes = [
  { path: 'crisis-center', component: CrisisListComponent },
  { path: 'heroes',        component: HeroListComponent },
  { path: '',   redirectTo: '/heroes', pathMatch: 'full' },
  { path: '**', component: PageNotFoundComponent }
];

@NgModule({
  imports: [
    RouterModule.forRoot(
      appRoutes,
      { enableTracing: true } // <-- debugging purposes only
    )
  ],
  exports: [
    RouterModule
  ]
})
export class AppRoutingModule {}
```



Next, update the `app.module.ts` file by removing `RouterModule.forRoot` in the `imports` array.

(src/app/app.module.ts)

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { AppRoutingModule } from './app-routing.module';

import { CrisisListComponent } from './crisis-list/crisis-list.component';
import { HeroListComponent } from './hero-list/hero-list.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
    AppRoutingModule
  ],
  declarations: [
    AppComponent,
    HeroListComponent,
    CrisisListComponent,
    PageNotFoundComponent
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
```



**Later, this guide shows you how to create [multiple routing modules](https://angular.io/guide/router-tutorial-toh#heroes-functionality) and import those routing modules [in the correct order](https://angular.io/guide/router-tutorial-toh#routing-module-order).**



The application continues to work just the same, and you can use `AppRoutingModule` as the central place to maintain future routing configuration.



##### Benefits of a routing module

The routing module, often called the `AppRoutingModule`, replaces the routing configuration in the root or feature module.

The routing module is helpful as your application grows and when the configuration includes specialized guard and resolver functions.

Some developers skip the routing module when the configuration is minimal and merge the routing configuration directly into the companion module (for example, `AppModule`).

Most applications should implement a routing module for consistency. It keeps the code clean when configuration becomes complex. It makes testing the feature module easier. Its existence calls attention to the fact that a module is routed. It is where developers expect to find and expand routing configuration.



#### Milestone 3: Heroes feature

This milestone covers the following:

- Organizing the application and routes into feature areas using modules
- Navigating imperatively from one component to another
- Passing required and optional information in route parameters

This sample application recreates the heroes feature in the "Services" section of the [Tour of Heroes tutorial](https://angular.io/tutorial/tour-of-heroes/toh-pt4), and reuses much of the code from the [Tour of Heroes: Services example code](https://angular.io/generated/live-examples/toh-pt4/stackblitz.html) / [download example](https://angular.io/generated/zips/toh-pt4/toh-pt4.zip).

A typical application has multiple feature areas, each dedicated to a particular business purpose with its own folder.

This section shows you how refactor the application into different feature modules, import them into the main module and navigate among them.



##### Add heroes functionality

Follow these steps:

- To manage the heroes, create a `HeroesModule` with routing in the heroes folder and register it with the root `AppModule`.

  ```shell
  ng generate module heroes/heroes --module app --flat --routing
  ```

- Move the placeholder `hero-list` folder that's in the `app` folder into the `heroes` folder.

- Copy the contents of the `heroes/heroes.component.html` from the ["Services" tutorial](https://angular.io/generated/live-examples/toh-pt4/stackblitz.html) / [download example](https://angular.io/generated/zips/toh-pt4/toh-pt4.zip) into the `hero-list.component.html` template.

  - Re-label the `<h2>` to `<h2>HEROES</h2>`.
  - Delete the `<app-hero-detail>` component at the bottom of the template.

- Copy the contents of the `heroes/heroes.component.css` from the live example into the `hero-list.component.css` file.

- Copy the contents of the `heroes/heroes.component.ts` from the live example into the `hero-list.component.ts` file.

  - Change the component class name to `HeroListComponent`.

  - Change the `selector` to `app-hero-list`.

  - Change the `templateUrl` to `./hero-list.component.html`.

  - Change the `styleUrls` to `./hero-list.component.css`.

    **Selectors are not required for routed components because components are dynamically inserted when the page is rendered. However, they are useful for identifying and targeting them in your HTML element tree.**

- Copy the `hero-detail` folder, the `hero.ts`, `hero.service.ts`, and `mock-heroes.ts` files into the `heroes` sub-folder

- Copy the `message.service.ts` into the `src/app` folder

- Update the relative path import to the `message.service` in the `hero.service.ts` file

Next, update the `HeroesModule` metadata.

- Import and add the `HeroDetailComponent` and `HeroListComponent` to the `declarations` array in the `HeroesModule`.

​		(src/app/heroes/heroes.module.ts)

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

import { HeroListComponent } from './hero-list/hero-list.component';
import { HeroDetailComponent } from './hero-detail/hero-detail.component';

import { HeroesRoutingModule } from './heroes-routing.module';

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    HeroesRoutingModule
  ],
  declarations: [
    HeroListComponent,
    HeroDetailComponent
  ]
})
export class HeroesModule {}
```

The hero management file structure is as follows:

![](./img/hero_file_struc.png) 



###### Hero feature routing requirements

The heroes feature has two interacting components, the hero list and the hero detail. When you navigate to list view, it gets a list of heroes and displays them. When you click on a hero, the detail view has to display that particular hero.

You tell the detail view which hero to display by including the selected hero's ID in the route URL.

Import the hero components from their new locations in the `src/app/heroes/` folder and define the two hero routes.

Now that you have routes for the `Heroes` module, register them with the `Router` using the `RouterModule` as you did in the `AppRoutingModule`, with an important difference.

In the `AppRoutingModule`, you used the static `RouterModule.forRoot()` method to register the routes and application level service providers. In a feature module you use the static `forChild()` method.

**Only call `RouterModule.forRoot()` in the root `AppRoutingModule` (or the `AppModule` if that's where you register top level application routes). In any other module, you must call the `RouterModule.forChild()` method to register additional routes.**

The updated `HeroesRoutingModule` looks like this:

(src/app/heroes/heroes-routing.module.ts)

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

import { HeroListComponent } from './hero-list/hero-list.component';
import { HeroDetailComponent } from './hero-detail/hero-detail.component';

const heroesRoutes: Routes = [
  { path: 'heroes',  component: HeroListComponent },
  { path: 'hero/:id', component: HeroDetailComponent }
];

@NgModule({
  imports: [
    RouterModule.forChild(heroesRoutes)
  ],
  exports: [
    RouterModule
  ]
})
export class HeroesRoutingModule { }
```



###### Remove duplicate hero routes

The hero routes are currently defined in two places: in the `HeroesRoutingModule`, by way of the `HeroesModule`, and in the `AppRoutingModule`.

Routes provided by feature modules are combined together into their imported module's routes by the router. This lets you continue defining the feature module routes without modifying the main route configuration.

Remove the `HeroListComponent` import and the `/heroes` route from the `app-routing.module.ts`.

Leave the default and the wildcard routes as these are still in use at the top level of the application.

( src/app/app-routing.module.ts (v2) )

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

import { CrisisListComponent } from './crisis-list/crisis-list.component';
// import { HeroListComponent } from './hero-list/hero-list.component';  // <-- delete this line
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

const appRoutes: Routes = [
  { path: 'crisis-center', component: CrisisListComponent },
  // { path: 'heroes',     component: HeroListComponent }, // <-- delete this line
  { path: '',   redirectTo: '/heroes', pathMatch: 'full' },
  { path: '**', component: PageNotFoundComponent }
];

@NgModule({
  imports: [
    RouterModule.forRoot(
      appRoutes,
      { enableTracing: true } // <-- debugging purposes only
    )
  ],
  exports: [
    RouterModule
  ]
})
export class AppRoutingModule {}
```



###### Remove heroes declarations

Because the `HeroesModule` now provides the `HeroListComponent`, remove it from the `AppModule`'s `declarations` array. Now that you have a separate `HeroesModule`, you can evolve the hero feature with more components and different routes.

After these steps, the `AppModule` should look like this:

(src/app/app.module.ts)

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';
import { AppComponent } from './app.component';
import { AppRoutingModule } from './app-routing.module';
import { HeroesModule } from './heroes/heroes.module';

import { CrisisListComponent } from './crisis-list/crisis-list.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
    HeroesModule,
    AppRoutingModule
  ],
  declarations: [
    AppComponent,
    CrisisListComponent,
    PageNotFoundComponent
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
```





##### Module import order

Notice that in the module `imports` array, the `AppRoutingModule` is last and comes *after* the `HeroesModule`.

( src/app/app.module.ts (module-imports) )

```typescript
imports: [
  BrowserModule,
  FormsModule,
  HeroesModule,
  AppRoutingModule
],
```

The order of route configuration is important because the router accepts the first route that matches a navigation request path.

When all routes were in one `AppRoutingModule`, you put the default and [wildcard](https://angular.io/guide/router-tutorial-toh#wildcard) routes last, after the `/heroes` route, so that the router had a chance to match a URL to the `/heroes` route *before* hitting the wildcard route and navigating to "Page not found".

Each routing module augments the route configuration in the order of import. If you listed `AppRoutingModule` first, the wildcard route would be registered *before* the hero routes. The wildcard route —which matches *every* URL— would intercept the attempt to navigate to a hero route.

**Reverse the routing modules to see a click of the heroes link resulting in "Page not found". Learn about inspecting the runtime router configuration [below](https://angular.io/guide/router-tutorial-toh#inspect-config).**



##### Route Parameters



###### Route definition with a parameter

Return to the `HeroesRoutingModule` and look at the route definitions again. 

The route to `HeroDetailComponent` has an `:id` token in the path.

( src/app/heroes/heroes-routing.module.ts (excerpt) )

```typescript
{ path: 'hero/:id', component: HeroDetailComponent }
```

The `:id` token creates a slot in the path for a Route Parameter. In this case, this configuration causes the router to insert the `id` of a hero into that slot.

If you tell the router to navigate to the detail component and display "Magneta", you expect a hero ID to appear in the browser URL like this:

```http
localhost:4200/hero/15
```

If a user enters that URL into the browser address bar, the router should recognize the pattern and go to the same "Magneta" detail view.



**ROUTE PARAMETER: REQUIRED OR OPTIONAL?**

**Embedding the route parameter token, `:id`, in the route definition path is a good choice for this scenario because the `id` is *required* by the `HeroDetailComponent` and because the value `15` in the path clearly distinguishes the route to "Magneta" from a route for some other hero.**



###### Setting the route parameters in the list view

After navigating to the `HeroDetailComponent`, you expect to see the details of the selected hero. You need two pieces of information: the routing path to the component and the hero's `id`.

Accordingly, the *link parameters array* has two items: The routing *path* and a *route parameter* that specifies the `id` of the selected hero.

( src/app/heroes/hero-list/hero-list.component.html (link-parameters-array) )

```typescript
<a [routerLink]="['/hero', hero.id]">
```

The router composes the destination URL from the array like this: `localhost:4200/hero/15`.

The router extracts the route parameter (`id:15`) from the URL and supplies it to the `HeroDetailComponent` using the `ActivatedRoute` service.



##### `Activated Route` in action

Import the `Router`, `ActivatedRoute`, and `ParamMap` tokens from the router package.

( src/app/heroes/hero-detail/hero-detail.component.ts (activated route) )

```typescript
import { Router, ActivatedRoute, ParamMap } from '@angular/router';
```



Import the `switchMap` operator because you need it later to process the `Observable` route parameters.

( src/app/heroes/hero-detail/hero-detail.component.ts (switchMap operator import) )

```typescript
import { switchMap } from 'rxjs/operators';
```



Add the services as private variables to the constructor so that Angular injects them (makes them visible to the component).

( src/app/heroes/hero-detail/hero-detail.component.ts (constructor) )

```typescript
constructor(
  private route: ActivatedRoute,
  private router: Router,
  private service: HeroService
) {}
```



In the `ngOnInit()` method, use the `ActivatedRoute` service to retrieve the parameters for the route, pull the hero `id` from the parameters, and retrieve the hero to display.

( src/app/heroes/hero-detail/hero-detail.component.ts (ngOnInit) )

```typescript
ngOnInit() {
  this.hero$ = this.route.paramMap.pipe(
    switchMap((params: ParamMap) =>
      this.service.getHero(params.get('id')!))
  );
}
```



When the map changes, `paramMap` gets the `id` parameter from the changed parameters.

Then you tell the `HeroService` to fetch the hero with that `id` and return the result of the `HeroService` request.

The `switchMap` operator does two things. It flattens the `Observable<Hero>` that `HeroService` returns and cancels previous pending requests. If the user re-navigates to this route with a new `id` while the `HeroService` is still retrieving the old `id`, `switchMap` discards that old request and returns the hero for the new `id`.

`AsyncPipe` handles the observable subscription and the component's `hero` property will be (re)set with the retrieved hero.



###### `ParamMap` API

The `ParamMap` API is inspired by the [URLSearchParams interface](https://developer.mozilla.org/docs/Web/API/URLSearchParams). It provides methods to handle parameter access for both route parameters (`paramMap`) and query parameters (`queryParamMap`).

| MEMBER         | DETAILS                                                      |
| :------------- | :----------------------------------------------------------- |
| `has(name)`    | Returns `true` if the parameter name is in the map of parameters. |
| `get(name)`    | Returns the parameter name value (a `string`) if present, or `null` if the parameter name is not in the map. Returns the *first* element if the parameter value is actually an array of values. |
| `getAll(name)` | Returns a `string array` of the parameter name value if found, or an empty `array` if the parameter name value is not in the map. Use `getAll` when a single parameter could have multiple values. |
| `keys`         | Returns a `string array` of all parameter names in the map.  |



###### Observable `paramMap` and component reuse

In this example, you retrieve the route parameter map from an `Observable`. That implies that the route parameter map can change during the lifetime of this component.

By default, the router re-uses a component instance when it re-navigates to the same component type without visiting a different component first. The route parameters could change each time.

Suppose a parent component navigation bar had "forward" and "back" buttons that scrolled through the list of heroes. Each click navigated imperatively to the `HeroDetailComponent` with the next or previous `id`.

You wouldn't want the router to remove the current `HeroDetailComponent` instance from the DOM only to re-create it for the next `id` as this would re-render the view. For better UX, the router re-uses the same component instance and updates the parameter.

Because `ngOnInit()` is only called once per component instantiation, you can detect when the route parameters change from *within the same instance* using the observable `paramMap` property.



**When subscribing to an observable in a component, you almost always unsubscribe when the component is destroyed.**

**However, `ActivatedRoute` observables are among the exceptions because `ActivatedRoute` and its observables are insulated from the `Router` itself. The `Router` destroys a routed component when it is no longer needed. This means all the component's members will also be destroyed, including the injected `ActivatedRoute` and the subscriptions to its `Observable` properties.**

**The `Router` does not `complete` any `Observable` of the `ActivatedRoute` so any `finalize` or `complete` blocks will not run. If you need to handle something in a `finalize`, you still need to unsubscribe in `ngOnDestroy`. You also have to unsubscribe if your observable pipe has a delay with code you do not want to run after the component is destroyed.**



###### `snapshot`: the no-observable alternative

This application won't re-use the `HeroDetailComponent`. The user always returns to the hero list to select another hero to view. There's no way to navigate from one hero detail to another hero detail without visiting the list component in between. Therefore, the router creates a new `HeroDetailComponent` instance every time.

When you know for certain that a `HeroDetailComponent` instance will never be re-used, you can use `snapshot`.

`route.snapshot` provides the initial value of the route parameter map. You can access the parameters directly without subscribing or adding observable operators as in the following:

( src/app/heroes/hero-detail/hero-detail.component.ts (ngOnInit snapshot) )

```typescript
ngOnInit() {
  const id = this.route.snapshot.paramMap.get('id')!;

  this.hero$ = this.service.getHero(id);
}
```

**`snapshot` only gets the initial value of the parameter map with this technique.**

**Use the observable `paramMap` approach if there's a possibility that the router could re-use the component. This tutorial sample application uses with the observable `paramMap`.**





##### Navigating back to the list component

The `HeroDetailComponent` "Back" button uses the `gotoHeroes()` method that navigates imperatively back to the `HeroListComponent`.

The router `navigate()` method takes the same one-item *link parameters array* that you can bind to a `[routerLink]` directive. It holds the path to the `HeroListComponent`:

( src/app/heroes/hero-detail/hero-detail.component.ts (excerpt) )

```typescript
gotoHeroes() {
  this.router.navigate(['/heroes']);
}
```



###### Route Parameters: Required or optional?

Use [route parameters](https://angular.io/guide/router-tutorial-toh#route-parameters) to specify a required parameter value within the route URL as you do when navigating to the `HeroDetailComponent` in order to view the hero with `id` 15:

```http
localhost:4200/hero/15
```



You can also add optional information to a route request. For example, when returning to the `hero-detail.component.ts` list from the hero detail view, it would be nice if the viewed hero were preselected in the list.

![](./img/selected-hero.png) 

Optional information can also include other forms such as:

- Loosely structured search criteria; for example, `name='wind*'`
- Multiple values; for example, `after='12/31/2015' & before='1/1/2017'` —in no particular order— `before='1/1/2017' & after='12/31/2015'` — in a variety of formats— `during='currentYear'`

As these kinds of parameters don't fit smoothly in a URL path, you can use optional parameters for conveying arbitrarily complex information during navigation. Optional parameters aren't involved in pattern matching and afford flexibility of expression.

The router supports navigation with optional parameters as well as required route parameters. Define optional parameters in a separate object *after* you define the required route parameters.

In general, use a required route parameter when the value is mandatory (for example, if necessary to distinguish one route path from another); and an optional parameter when the value is optional, complex, and/or multivariate.



###### Heroes list: optionally selecting a hero

When navigating to the `HeroDetailComponent` you specified the required `id` of the hero-to-edit in the route parameter and made it the second item of the [*link parameters array*](https://angular.io/guide/router-tutorial-toh#link-parameters-array).

( src/app/heroes/hero-list/hero-list.component.html (link-parameters-array) )

```html
<a [routerLink]="['/hero', hero.id]">
```

The router embedded the `id` value in the navigation URL because you had defined it as a route parameter with an `:id` placeholder token in the route `path`:

( src/app/heroes/heroes-routing.module.ts (hero-detail-route) )

```typescript
{ path: 'hero/:id', component: HeroDetailComponent }
```

When the user clicks the back button, the `HeroDetailComponent` constructs another *link parameters array* which it uses to navigate back to the `HeroListComponent`.

( src/app/heroes/hero-detail/hero-detail.component.ts (gotoHeroes) )

```typescript
gotoHeroes() {
  this.router.navigate(['/heroes']);
}
```



This array lacks a route parameter because previously you didn't need to send information to the `HeroListComponent`.

Now, send the `id` of the current hero with the navigation request so that the `HeroListComponent` can highlight that hero in its list.

Send the `id` with an object that contains an optional `id` parameter. For demonstration purposes, there's an extra junk parameter (`foo`) in the object that the `HeroListComponent` should ignore. Here's the revised navigation statement:

( src/app/heroes/hero-detail/hero-detail.component.ts (go to heroes) )

```typescript
gotoHeroes(hero: Hero) {
  const heroId = hero ? hero.id : null;
  // Pass along the hero id if available
  // so that the HeroList component can select that hero.
  // Include a junk 'foo' property for fun.
  this.router.navigate(['/heroes', { id: heroId, foo: 'foo' }]);
}
```



The application still works. Clicking "back" returns to the hero list view.

Look at the browser address bar.

It should look something like this, depending on where you run it:

```http
localhost:4200/heroes;id=15;foo=foo
```

The `id` value appears in the URL as (`;id=15;foo=foo`), not in the URL path. The path for the "Heroes" route doesn't have an `:id` token.

The optional route parameters are not separated by "?" and "&" as they would be in the URL query string. They are separated by semicolons ";". This is matrix URL notation.



**Matrix URL notation is an idea first introduced in a [1996 proposal](https://www.w3.org/DesignIssues/MatrixURIs.html) by the founder of the web, Tim Berners-Lee.**

**Although matrix notation never made it into the HTML standard, it is legal and it became popular among browser routing systems as a way to isolate parameters belonging to parent and child routes. As such, the Router provides support for the matrix notation across browsers.**





##### Route parameters in the `ActivatedRoute` service

In its current state of development, the list of heroes is unchanged. No hero row is highlighted.

The `HeroListComponent` needs code that expects parameters.

Previously, when navigating from the `HeroListComponent` to the `HeroDetailComponent`, you subscribed to the route parameter map `Observable` and made it available to the `HeroDetailComponent` in the `ActivatedRoute` service. You injected that service in the constructor of the `HeroDetailComponent`.

This time you'll be navigating in the opposite direction, from the `HeroDetailComponent` to the `HeroListComponent`.



First, extend the router import statement to include the `ActivatedRoute` service symbol:

( src/app/heroes/hero-list/hero-list.component.ts (import) )

```typescript
import { ActivatedRoute } from '@angular/router';
```



Import the `switchMap` operator to perform an operation on the `Observable` of route parameter map.

( src/app/heroes/hero-list/hero-list.component.ts (rxjs imports) )

```typescript
import { Observable } from 'rxjs';
import { switchMap } from 'rxjs/operators';
```



Inject the `ActivatedRoute` in the `HeroListComponent` constructor.

( src/app/heroes/hero-list/hero-list.component.ts (constructor and ngOnInit) )

```typescript
export class HeroListComponent implements OnInit {
  heroes$!: Observable<Hero[]>;
  selectedId = 0;

  constructor(
    private service: HeroService,
    private route: ActivatedRoute
  ) {}

  ngOnInit() {
    this.heroes$ = this.route.paramMap.pipe(
      switchMap(params => {
        this.selectedId = parseInt(params.get('id')!, 10);
        return this.service.getHeroes();
      })
    );
  }
}
```



The `ActivatedRoute.paramMap` property is an `Observable` map of route parameters. The `paramMap` emits a new map of values that includes `id` when the user navigates to the component. In `ngOnInit()` you subscribe to those values, set the `selectedId`, and get the heroes.

Update the template with a [class binding](https://angular.io/guide/class-binding). The binding adds the `selected` CSS class when the comparison returns `true` and removes it when `false`. Look for it within the repeated `<li>` tag as shown here:

(src/app/heroes/hero-list/hero-list.component.html)

```html
<h2>Heroes</h2>
<ul class="heroes">
  <li *ngFor="let hero of heroes$ | async" [class.selected]="hero.id === selectedId">
    <a [routerLink]="['/hero', hero.id]">
      <span class="badge">{{ hero.id }}</span>{{ hero.name }}
    </a>
  </li>
</ul>

<button type="button" routerLink="/sidekicks">Go to sidekicks</button>
```

Add some styles to apply when the hero is selected.

(src/app/heroes/hero-list/hero-list.component.css)

```css
.heroes .selected a {
  background-color: #d6e6f7;
}

.heroes .selected a:hover {
  background-color: #bdd7f5;
}
```



When the user navigates from the heroes list to the "Magneta" hero and back, "Magneta" appears selected:

![](./img/selected-hero.png) 

The optional `foo` route parameter is harmless and the router continues to ignore it.





##### Adding routable animations

This section shows you how to add some [animations](https://angular.io/guide/animations) to the `HeroDetailComponent`.

First, import the `BrowserAnimationsModule` and add it to the `imports` array:

( src/app/app.module.ts (animations-module) )

```typescript
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

@NgModule({
  imports: [
    BrowserAnimationsModule,
  ],
})
```

Next, add a `data` object to the routes for `HeroListComponent` and `HeroDetailComponent`. Transitions are based on `states` and you use the `animation` data from the route to provide a named animation [`state`](https://angular.io/api/animations/state) for the transitions.

( src/app/heroes/heroes-routing.module.ts (animation data) )

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

import { HeroListComponent } from './hero-list/hero-list.component';
import { HeroDetailComponent } from './hero-detail/hero-detail.component';

const heroesRoutes: Routes = [
  { path: 'heroes',  component: HeroListComponent, data: { animation: 'heroes' } },
  { path: 'hero/:id', component: HeroDetailComponent, data: { animation: 'hero' } }
];

@NgModule({
  imports: [
    RouterModule.forChild(heroesRoutes)
  ],
  exports: [
    RouterModule
  ]
})
export class HeroesRoutingModule { }
```



Create an `animations.ts` file in the root `src/app/` folder. The contents look like this:

( src/app/animations.ts (excerpt) )

```typescript
import {
  trigger, animateChild, group,
  transition, animate, style, query
} from '@angular/animations';


// Routable animations
export const slideInAnimation =
  trigger('routeAnimation', [
    transition('heroes <=> hero', [
      style({ position: 'relative' }),
      query(':enter, :leave', [
        style({
          position: 'absolute',
          top: 0,
          left: 0,
          width: '100%'
        })
      ]),
      query(':enter', [
        style({ left: '-100%'})
      ]),
      query(':leave', animateChild()),
      group([
        query(':leave', [
          animate('300ms ease-out', style({ left: '100%'}))
        ]),
        query(':enter', [
          animate('300ms ease-out', style({ left: '0%'}))
        ])
      ]),
      query(':enter', animateChild()),
    ])
  ]);
```

This file does the following:

- Imports the animation symbols that build the animation triggers, control state, and manage transitions between states
- Exports a constant named `slideInAnimation` set to an animation trigger named `routeAnimation`
- Defines one transition when switching back and forth from the `heroes` and `hero` routes to ease the component in from the left of the screen as it enters the application view (`:enter`), the other to animate the component to the right as it leaves the application view (`:leave`)

Back in the `AppComponent`, import the `RouterOutlet` token from the `@angular/router` package and the `slideInAnimation` from `'./animations.ts`.



Add an `animations` array to the `@Component` metadata that contains the `slideInAnimation`.

( src/app/app.component.ts (animations) )

```typescript
import { ChildrenOutletContexts } from '@angular/router';
import { slideInAnimation } from './animations';

@Component({
  selector: 'app-root',
  templateUrl: 'app.component.html',
  styleUrls: ['app.component.css'],
  animations: [ slideInAnimation ]
})
```

To use the routable animations, wrap the `RouterOutlet` inside an element, use the `@routeAnimation` trigger, and bind it to the element.

For the `@routeAnimation` transitions to key off states, provide it with the `data` from the `ActivatedRoute`. The `RouterOutlet` is exposed as an `outlet` template variable, so you bind a reference to the router outlet. This example uses a variable of `routerOutlet`.



( src/app/app.component.html (router outlet) )

```html
<h1>Angular Router</h1>
<nav>
  <a routerLink="/crisis-center" routerLinkActive="active" ariaCurrentWhenActive="page">Crisis Center</a>
  <a routerLink="/heroes" routerLinkActive="active" ariaCurrentWhenActive="page">Heroes</a>
</nav>
<div [@routeAnimation]="getAnimationData()">
  <router-outlet></router-outlet>
</div>
```

The `@routeAnimation` property is bound to the `getAnimationData()` which returns the animation property from the `data` provided by the primary route. The `animation` property matches the `transition` names you used in the `slideInAnimation` defined in `animations.ts`.

( src/app/app.component.ts (router outlet) )

```typescript
export class AppComponent {
  constructor(private contexts: ChildrenOutletContexts) {}

  getAnimationData() {
      return this.contexts.getContext('primary')?.route?.snapshot?.data?.['animation'];
  }
}
```

When switching between the two routes, the `HeroDetailComponent` and `HeroListComponent` now ease in from the left when routed to, and slide to the right when navigating away.



##### Milestone 3 wrap up

This section covered the following:

- Organizing the application into feature areas
- Navigating imperatively from one component to another
- Passing information along in route parameters and subscribe to them in the component
- Importing the feature area NgModule into the `AppModule`
- Applying routable animations based on the page

After these changes, the folder structure is as follows:

![](./img/wrap-up3.png) 



#### Milestone 4: Crisis center feature

This section shows you how to add child routes and use relative routing in your app.

To add more features to the application's current crisis center, take similar steps as for the heroes feature:

- Create a `crisis-center` subfolder in the `src/app` folder
- Copy the files and folders from `app/heroes` into the new `crisis-center` folder
- In the new files, change every mention of "hero" to "crisis", and "heroes" to "crises"
- Rename the NgModule files to `crisis-center.module.ts` and `crisis-center-routing.module.ts`



Use mock crises instead of mock heroes:

(src/app/crisis-center/mock-crises.ts)

```typescript
import { Crisis } from './crisis';

export const CRISES: Crisis[] = [
  { id: 1, name: 'Dragon Burning Cities' },
  { id: 2, name: 'Sky Rains Great White Sharks' },
  { id: 3, name: 'Giant Asteroid Heading For Earth' },
  { id: 4, name: 'Procrastinators Meeting Delayed Again' },
];
```

The resulting crisis center is a foundation for introducing a new concept —child routing. You can leave Heroes in its current state as a contrast with the Crisis Center.



**In keeping with the [Separation of Concerns principle](https://blog.8thlight.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html), changes to the Crisis Center don't affect the `AppModule` or any other feature's component.**



##### A crisis center with child routes

This section shows you how to organize the crisis center to conform to the following recommended pattern for Angular applications:

- Each feature area resides in its own folder
- Each feature has its own Angular feature module
- Each area has its own area root component
- Each area root component has its own router outlet and child routes
- Feature area routes rarely (if ever) cross with routes of other features

If your application had many feature areas, the component trees might consist of multiple components for those features, each with branches of other, related, components.



##### Child routing component

Generate a `CrisisCenter` component in the `crisis-center` folder:

```shell
ng generate component crisis-center/crisis-center
```

Update the component template with the following markup:

(src/app/crisis-center/crisis-center/crisis-center.component.html)

```html
<h2>Crisis Center</h2>
<router-outlet></router-outlet>
```

The `CrisisCenterComponent` has the following in common with the `AppComponent`:

- It is the root of the crisis center area, just as `AppComponent` is the root of the entire application
- It is a shell for the crisis management feature area, just as the `AppComponent` is a shell to manage the high-level workflow

Like most shells, the `CrisisCenterComponent` class is minimal because it has no business logic, and its template has no links, just a title and `<router-outlet>` for the crisis center child component.



##### Child route configuration

As a host page for the "Crisis Center" feature, generate a `CrisisCenterHome` component in the `crisis-center` folder.

```shell
ng generate component crisis-center/crisis-center-home
```



Update the template with a welcome message to the `Crisis Center`.

(src/app/crisis-center/crisis-center-home/crisis-center-home.component.html)

```html
<h3>Welcome to the Crisis Center</h3>
```



Update the `crisis-center-routing.module.ts` you renamed after copying it from `heroes-routing.module.ts` file. This time, you define child routes within the parent `crisis-center` route.

( src/app/crisis-center/crisis-center-routing.module.ts (Routes) )

```typescript
const crisisCenterRoutes: Routes = [
  {
    path: 'crisis-center',
    component: CrisisCenterComponent,
    children: [
      {
        path: '',
        component: CrisisListComponent,
        children: [
          {
            path: ':id',
            component: CrisisDetailComponent
          },
          {
            path: '',
            component: CrisisCenterHomeComponent
          }
        ]
      }
    ]
  }
];
```



Notice that the parent `crisis-center` route has a `children` property with a single route containing the `CrisisListComponent`. The `CrisisListComponent` route also has a `children` array with two routes.

These two routes navigate to the crisis center child components, `CrisisCenterHomeComponent` and `CrisisDetailComponent`, respectively.

There are important differences in the way the router treats child routes.

The router displays the components of these routes in the `RouterOutlet` of the `CrisisCenterComponent`, not in the `RouterOutlet` of the `AppComponent` shell.

The `CrisisListComponent` contains the crisis list and a `RouterOutlet` to display the `Crisis Center Home` and `Crisis Detail` route components.

The `Crisis Detail` route is a child of the `Crisis List`. The router [reuses components](https://angular.io/guide/router-tutorial-toh#reuse) by default, so the `Crisis Detail` component is re-used as you select different crises. In contrast, back in the `Hero Detail` route, [the component was recreated](https://angular.io/guide/router-tutorial-toh#snapshot-the-no-observable-alternative) each time you selected a different hero from the list of heroes.

At the top level, paths that begin with `/` refer to the root of the application. But child routes extend the path of the parent route. With each step down the route tree, you add a slash followed by the route path, unless the path is empty.

Apply that logic to navigation within the crisis center for which the parent path is `/crisis-center`.

- To navigate to the `CrisisCenterHomeComponent`, the full URL is `/crisis-center` (`/crisis-center` + `''` + `''`)
- To navigate to the `CrisisDetailComponent` for a crisis with `id=2`, the full URL is `/crisis-center/2` (`/crisis-center` + `''` + `'/2'`)

The absolute URL for the latter example, including the `localhost` origin, is as follows:

```http
localhost:4200/crisis-center/2
```



Here's the complete `crisis-center-routing.module.ts` file with its imports.

( src/app/crisis-center/crisis-center-routing.module.ts (excerpt) )

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

import { CrisisCenterHomeComponent } from './crisis-center-home/crisis-center-home.component';
import { CrisisListComponent } from './crisis-list/crisis-list.component';
import { CrisisCenterComponent } from './crisis-center/crisis-center.component';
import { CrisisDetailComponent } from './crisis-detail/crisis-detail.component';

const crisisCenterRoutes: Routes = [
  {
    path: 'crisis-center',
    component: CrisisCenterComponent,
    children: [
      {
        path: '',
        component: CrisisListComponent,
        children: [
          {
            path: ':id',
            component: CrisisDetailComponent
          },
          {
            path: '',
            component: CrisisCenterHomeComponent
          }
        ]
      }
    ]
  }
];

@NgModule({
  imports: [
    RouterModule.forChild(crisisCenterRoutes)
  ],
  exports: [
    RouterModule
  ]
})
export class CrisisCenterRoutingModule { }
```



##### Import crisis center module into the `AppModule` routes

As with the `HeroesModule`, you must add the `CrisisCenterModule` to the `imports` array of the `AppModule` *before* the `AppRoutingModule`:

(src/app/crisis-center/crisis-center.module.ts)

```typescript
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { CommonModule } from '@angular/common';

import { CrisisCenterHomeComponent } from './crisis-center-home/crisis-center-home.component';
import { CrisisListComponent } from './crisis-list/crisis-list.component';
import { CrisisCenterComponent } from './crisis-center/crisis-center.component';
import { CrisisDetailComponent } from './crisis-detail/crisis-detail.component';

import { CrisisCenterRoutingModule } from './crisis-center-routing.module';

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    CrisisCenterRoutingModule
  ],
  declarations: [
    CrisisCenterComponent,
    CrisisListComponent,
    CrisisCenterHomeComponent,
    CrisisDetailComponent
  ]
})
export class CrisisCenterModule {}
```



( src/app/app.module.ts (import CrisisCenterModule) )

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';
import { ComposeMessageComponent } from './compose-message/compose-message.component';

import { AppRoutingModule } from './app-routing.module';
import { HeroesModule } from './heroes/heroes.module';
import { CrisisCenterModule } from './crisis-center/crisis-center.module';

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    HeroesModule,
    CrisisCenterModule,
    AppRoutingModule
  ],
  declarations: [
    AppComponent,
    PageNotFoundComponent
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
```



**The import order of the modules is important because the order of the routes defined in the modules affects route matching. If the `AppModule` were imported first, its wildcard route (`path: '**'`) would take precedence over the routes defined in `CrisisCenterModule`. For more information, see the section on [route order](https://angular.io/guide/router#route-order).**



Remove the initial crisis center route from the `app-routing.module.ts` because now the `HeroesModule` and the `CrisisCenter` modules provide the feature routes.

The `app-routing.module.ts` file retains the top-level application routes such as the default and wildcard routes.

( src/app/app-routing.module.ts (v3) )

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

const appRoutes: Routes = [
  { path: '',   redirectTo: '/heroes', pathMatch: 'full' },
  { path: '**', component: PageNotFoundComponent }
];

@NgModule({
  imports: [
    RouterModule.forRoot(
      appRoutes,
      { enableTracing: true } // <-- debugging purposes only
    )
  ],
  exports: [
    RouterModule
  ]
})
export class AppRoutingModule {}
```



##### Relative navigation

While building out the crisis center feature, you navigated to the crisis detail route using an absolute path that begins with a slash.

The router matches such absolute paths to routes starting from the top of the route configuration.

You could continue to use absolute paths like this to navigate inside the Crisis Center feature, but that pins the links to the parent routing structure. If you changed the parent `/crisis-center` path, you would have to change the link parameters array.

You can free the links from this dependency by defining paths that are relative to the current URL segment. Navigation within the feature area remains intact even if you change the parent route path to the feature.



The router supports directory-like syntax in a *link parameters list* to help guide route name lookup:

| DIRECTORY-LIKE SYNTAX   | DETAILS                         |
| :---------------------- | :------------------------------ |
| `./` `no leading slash` | Relative to the current level.  |
| `../`                   | Up one level in the route path. |

You can combine relative navigation syntax with an ancestor path. If you must navigate to a sibling route, you could use the `../<sibling>` convention to go up one level, then over and down the sibling route path.



To navigate a relative path with the `Router.navigate` method, you must supply the `ActivatedRoute` to give the router knowledge of where you are in the current route tree.

After the *link parameters array*, add an object with a `relativeTo` property set to the `ActivatedRoute`. The router then calculates the target URL based on the active route's location.

**Always specify the complete absolute path when calling router's `navigateByUrl()` method.**



##### Navigate to crisis list with a relative URL

You've already injected the `ActivatedRoute` that you need to compose the relative navigation path.

When using a `RouterLink` to navigate instead of the `Router` service, you'd use the same link parameters array, but you wouldn't provide the object with the `relativeTo` property. The `ActivatedRoute` is implicit in a `RouterLink` directive.



Update the `gotoCrises()` method of the `CrisisDetailComponent` to navigate back to the Crisis Center list using relative path navigation.

( src/app/crisis-center/crisis-detail/crisis-detail.component.ts (relative navigation) )

```typescript
// Relative navigation back to the crises
this.router.navigate(['../', { id: crisisId, foo: 'foo' }], { relativeTo: this.route });
```

Notice that the path goes up a level using the `../` syntax. If the current crisis `id` is `3`, the resulting path back to the crisis list is `/crisis-center;id=3;foo=foo`.



##### Displaying multiple routes in named outlets

You decide to give users a way to contact the crisis center. When a user clicks a "Contact" button, you want to display a message in a popup view.

The popup should stay open, even when switching between pages in the application, until the user closes it by sending the message or canceling. Clearly you can't put the popup in the same outlet as the other pages.

Until now, you've defined a single outlet and you've nested child routes under that outlet to group routes together. The router only supports **one primary unnamed outlet** per template.

A template can also have any number of named outlets. Each named outlet has its own set of routes with their own components. Multiple outlets can display different content, determined by different routes, all at the same time.



Add an outlet named "popup" in the `AppComponent`, directly following the unnamed outlet.

( src/app/app.component.html (outlets) )

```html
<div [@routeAnimation]="getAnimationData()">
  <router-outlet></router-outlet>
</div>
<router-outlet name="popup"></router-outlet>
```

That's where a popup goes, once you learn how to route a popup component to it.



###### Secondary routes

Named outlets are the targets of *secondary routes*.

Secondary routes look like primary routes and you configure them the same way. They differ in a few key respects.

- They are independent of each other
- They work in combination with other routes
- They are displayed in named outlets

Generate a new component to compose the message.

```shell
ng generate component compose-message
```

It displays a short form with a header, an input box for the message, and two buttons, "Send" and "Cancel".

![](./img/contact-form.png) 



Here's the component, its template, and styles:

(src/app/compose-message/compose-message.component.html)

```html
<h3>Contact Crisis Center</h3>
<div *ngIf="details">
  {{ details }}
</div>
<div>
  <div>
    <label for="message">Enter your message: </label>
  </div>
  <div>
    <textarea id="message" [(ngModel)]="message" rows="10" cols="35" [disabled]="sending"></textarea>
  </div>
</div>
<p *ngIf="!sending">
  <button type="button" (click)="send()">Send</button>
  <button type="button" (click)="cancel()">Cancel</button>
</p>
```



(src/app/compose-message/compose-message.component.ts)

```typescript
import { Component } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';

@Component({
  selector: 'app-compose-message',
  templateUrl: './compose-message.component.html',
  styleUrls: ['./compose-message.component.css']
})
export class ComposeMessageComponent {
  details = '';
  message = '';
  sending = false;

  constructor(private router: Router, private route: ActivatedRoute) {}

  send() {
    this.sending = true;
    this.details = 'Sending Message...';

    setTimeout(() => {
      this.sending = false;
      this.closePopup();
    }, 1000);
  }

  cancel() {
    this.closePopup();
  }

  closePopup() {
    // Providing a `null` value to the named outlet
    // clears the contents of the named outlet
    this.router.navigate([{outlets: {popup: null}}], {relativeTo: this.route.parent});
  }
}
```



(src/app/compose-message/compose-message.component.css)

```css
textarea {
  width: 100%;
  margin-top: 1rem;
  font-size: 1.2rem;
  box-sizing: border-box;
}
```



It looks similar to any other component in this guide, but there are two key differences.



**NOTE:**
**The `send()` method simulates latency by waiting a second before "sending" the message and closing the popup.**



The `closePopup()` method closes the popup view by navigating to the popup outlet with a `null` which the section on [clearing secondary routes](https://angular.io/guide/router-tutorial-toh#clear-secondary-routes) covers.



###### Add a secondary route

Open the `AppRoutingModule` and add a new `compose` route to the `appRoutes`.

( src/app/app-routing.module.ts (compose route) )

```typescript
{
  path: 'compose',
  component: ComposeMessageComponent,
  outlet: 'popup'
},
```

In addition to the `path` and `component` properties, there's a new property called `outlet`, which is set to `'popup'`. This route now targets the popup outlet and the `ComposeMessageComponent` will display there.



To give users a way to open the popup, add a "Contact" link to the `AppComponent` template.

( src/app/app.component.html (contact-link) )

```html
<a [routerLink]="[{ outlets: { popup: ['compose'] } }]">Contact</a>
```



Although the `compose` route is configured to the "popup" outlet, that's not sufficient for connecting the route to a `RouterLink` directive. You have to specify the named outlet in a *link parameters array* and bind it to the `RouterLink` with a property binding.

The *link parameters array* contains an object with **a single `outlets` property** whose value is another object keyed by one (or more) outlet names. In this case there is only the "popup" outlet property and its value is **another *link parameters array*** that specifies the `compose` route.

In other words, when the user clicks this link, the router displays the component associated with the `compose` route in the `popup` outlet.



**This `outlets` object within an outer object was unnecessary when there was only one route and one unnamed outlet.**

**The router assumed that your route specification targeted the unnamed primary outlet and created these objects for you.**

**Routing to a named outlet revealed a router feature: you can target multiple outlets with multiple routes in the same `RouterLink` directive.**



###### Secondary route navigation: merging routes during navigation

Navigate to the *Crisis Center* and click "Contact". you should see something like the following URL in the browser address bar.

```http
http://…/crisis-center(popup:compose)
```

The relevant part of the URL follows the `...`:

- The `crisis-center` is the primary navigation
- Parentheses surround the secondary route
- The secondary route consists of an **outlet name** (`popup`), a `colon` separator, and the **secondary route path** (`compose`)



Click the *Heroes* link and look at the URL again.

```http
http://…/heroes(popup:compose)
```

The primary navigation part changed; the secondary route is the same.

The router is keeping track of two separate branches in a navigation tree and generating a representation of that tree in the URL.

You can add many more outlets and routes, at the top level and in nested levels, creating a navigation tree with many branches and the router will generate the URLs to go with it.

You can tell the router to navigate an entire tree at once by filling out the `outlets` object and then pass that object inside a *link parameters array* to the `router.navigate` method.



###### Clearing secondary routes

Like regular outlets, secondary outlets persists until you navigate away to a new component.

Each secondary outlet has its own navigation, independent of the navigation driving the primary outlet. Changing a current route that displays in the primary outlet has no effect on the popup outlet. That's why the popup stays visible as you navigate among the crises and heroes.



The `closePopup()` method again:

( src/app/compose-message/compose-message.component.ts (closePopup) )

```typescript
closePopup() {
  // Providing a `null` value to the named outlet
  // clears the contents of the named outlet
  this.router.navigate([{outlets: {popup: null}}], {relativeTo: this.route.parent});
}
```

Clicking the "send" or "cancel" buttons clears the popup view. The `closePopup()` function navigates imperatively with the `Router.navigate()` method, passing in a [link parameters array](https://angular.io/guide/router-tutorial-toh#link-parameters-array).

Like the array bound to the *Contact* `RouterLink` in the `AppComponent`, this one includes an object with an `outlets` property. The `outlets` property value is another object with outlet names for keys. The only named outlet is `'popup'`.

This time, the value of `'popup'` is `null`. That's not a route, but it is a legitimate value. Setting the popup `RouterOutlet` to `null` clears the outlet and removes the secondary popup route from the current URL.



**Note:** **All commands in the array passed to `Router.navigate()` target a *specific segment* in the `UrlTree`. We specify the parent of the `ActivatedRoute` as the `relativeTo` option because we want to remove `'popup'` from the segment which holds its reference. It's important to always be aware of which segments the commands will be applied to.**

When `relativeTo` is not provided to the `Router.navigate()` method, the commands are processed starting at the root. We could omit the `relativeTo` option in this particular example because the `'popup'` outlet appears at the root level of the configuration.

If you want to close an outlet which appears at any segment depth, you could accomplish this by creating a `UrlTree` from the current URL, recursively clearing segment `children` matching the outlet name, and finally calling `Router.navigateByUrl()` with the `root` segment of the current `UrlTree`.





#### Milestone 5: Route guards

At the moment, any user can navigate anywhere in the application any time, but sometimes you need to control access to different parts of your application for various reasons, some of which might include the following:

- Perhaps the user is not authorized to navigate to the target component
- Maybe the user must login (authenticate) first
- Maybe you should fetch some data before you display the target component
- You might want to save pending changes before leaving a component
- You might ask the user if it's okay to discard pending changes rather than save them

You add guards to the route configuration to handle these scenarios.

A guard's return value controls the router's behavior:

| GUARD RETURN VALUE | DETAILS                                                      |
| :----------------- | :----------------------------------------------------------- |
| `true`             | The navigation process continues                             |
| `false`            | The navigation process stops and the user stays put          |
| `UrlTree`          | The current navigation cancels and a new navigation is initiated to the `UrlTree` returned |



**Note: The guard can also tell the router to navigate elsewhere, effectively canceling the current navigation. When doing so inside a guard, the guard should return `UrlTree`.**



The guard might return its boolean answer synchronously. But in many cases, the guard can't produce an answer synchronously. The guard could ask the user a question, save changes to the server, or fetch fresh data. These are all asynchronous operations.

Accordingly, a routing guard can return an `Observable<boolean>` or a `Promise<boolean>` and the router will wait for the observable or the promise to resolve to `true` or `false`.

**NOTE**:
The observable provided to the `Router` automatically completes after it retrieves the first value.



The router supports multiple guard methods:

| GUARD INTERFACES                                             | DETAILS                                                      |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`canActivate`](https://angular.io/api/router/CanActivateFn) | To mediate navigation *to* a route                           |
| [`canActivateChild`](https://angular.io/api/router/CanActivateChildFn) | To mediate navigation *to* a child route                     |
| [`canDeactivate`](https://angular.io/api/router/CanDeactivateFn) | To mediate navigation *away* from the current route          |
| [`resolve`](https://angular.io/api/router/ResolveFn)         | To perform route data retrieval *before* route activation    |
| [`canMatch`](https://angular.io/api/router/CanMatchFn)       | To control whether a `Route` should be used at all, even if the `path` matches the URL segment |

You can have multiple guards at every level of a routing hierarchy. The router checks the `canDeactivate` guards first, from the deepest child route to the top. Then it checks the `canActivate` and `canActivateChild` guards from the top down to the deepest child route. If the feature module is loaded asynchronously, the `canMatch` guard is checked before the module is loaded.

With the exception of `canMatch`, if *any* guard returns false, pending guards that have not completed are canceled, and **the entire navigation** is canceled. 

If a `canMatch` guard returns `false`, the `Router` **continues processing** the rest of the `Routes` to see if a different `Route` config matches the URL. You can think of this as though the `Router` is pretending the `Route` with the `canMatch` guard did not exist.

There are several examples over the next few sections.



##### `canActivate`: requiring authentication

Applications often restrict access to a feature area based on who the user is. You could permit access only to authenticated users or to users with a specific role. You might block or limit access until the user's account is activated.

The `canActivate` guard is the tool to manage these navigation business rules.



###### Add an admin feature module

his section guides you through extending the crisis center with some new administrative features. Start by adding a new feature module named `AdminModule`.

Generate an `admin` folder with a feature module file and a routing configuration file.

```shell
ng generate module admin --routing
```

Next, generate the supporting components.

```shell
ng generate component admin/admin-dashboard
ng generate component admin/admin
ng generate component admin/manage-crises
ng generate component admin/manage-heroes
```

The admin feature file structure looks like this:

![](./img/struct_admin.png) 







The admin feature module contains the `AdminComponent` used for routing within the feature module, a dashboard route and two unfinished components to manage crises and heroes.



(src/app/admin/admin/admin.component.html)

```html
<h2>Admin</h2>
<nav>
  <a routerLink="./" routerLinkActive="active"
    [routerLinkActiveOptions]="{ exact: true }" ariaCurrentWhenActive="page">Dashboard</a>
  <a routerLink="./crises" routerLinkActive="active" ariaCurrentWhenActive="page">Manage Crises</a>
  <a routerLink="./heroes" routerLinkActive="active" ariaCurrentWhenActive="page">Manage Heroes</a>
</nav>
<router-outlet></router-outlet>
```



(src/app/admin/admin-dashboard/admin-dashboard.component.html)

```html
<h3>Dashboard</h3>
```



(src/app/admin/admin.module.ts)

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

import { AdminComponent } from './admin/admin.component';
import { AdminDashboardComponent } from './admin-dashboard/admin-dashboard.component';
import { ManageCrisesComponent } from './manage-crises/manage-crises.component';
import { ManageHeroesComponent } from './manage-heroes/manage-heroes.component';

import { AdminRoutingModule } from './admin-routing.module';

@NgModule({
  imports: [
    CommonModule,
    AdminRoutingModule
  ],
  declarations: [
    AdminComponent,
    AdminDashboardComponent,
    ManageCrisesComponent,
    ManageHeroesComponent
  ]
})
export class AdminModule {}
```



(src/app/admin/manage-crises/manage-crises.component.html)

```html
<p>Manage your crises here</p>
```

(src/app/admin/manage-heroes/manage-heroes.component.html)

```html
<p>Manage your heroes here</p>
```



**Although the admin dashboard `RouterLink` only contains a relative slash without an additional URL segment, it is a match to any route within the admin feature area. You only want the `Dashboard` link to be active when the user visits that route. Adding an additional binding to the `Dashboard` routerLink,`[routerLinkActiveOptions]="{ exact: true }"`, marks the `./` link as active when the user navigates to the `/admin` URL and not when navigating to <u>any of the child routes</u>.**



###### Component-less route: grouping routes without a component

The initial admin routing configuration:

( src/app/admin/admin-routing.module.ts (admin routing) )

```typescript
const adminRoutes: Routes = [
  {
    path: 'admin',
    component: AdminComponent,
    children: [
      {
        path: '',
        children: [
          { path: 'crises', component: ManageCrisesComponent },
          { path: 'heroes', component: ManageHeroesComponent },
          { path: '', component: AdminDashboardComponent }
        ]
      }
    ]
  }
];

@NgModule({
  imports: [
    RouterModule.forChild(adminRoutes)
  ],
  exports: [
    RouterModule
  ]
})
export class AdminRoutingModule {}
```

The child route under the `AdminComponent` has a `path` and a `children` property but it's not using a `component`. This defines a *component-less* route.

To group the `Crisis Center` management routes under the `admin` path a component is unnecessary. Additionally, a *component-less* route makes it easier to [guard child routes](https://angular.io/guide/router-tutorial-toh#can-activate-child-guard).



Next, import the `AdminModule` into `app.module.ts` and add it to the `imports` array to register the admin routes.

( src/app/app.module.ts (admin module) )

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';
import { ComposeMessageComponent } from './compose-message/compose-message.component';

import { AppRoutingModule } from './app-routing.module';
import { HeroesModule } from './heroes/heroes.module';
import { CrisisCenterModule } from './crisis-center/crisis-center.module';

import { AdminModule } from './admin/admin.module';

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    HeroesModule,
    CrisisCenterModule,
    AdminModule,
    AppRoutingModule
  ],
  declarations: [
    AppComponent,
    ComposeMessageComponent,
    PageNotFoundComponent
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
```



Add an "Admin" link to the `AppComponent` shell so that users can get to this feature.

( src/app/app.component.html (template) )

```html
<h1 class="title">Angular Router</h1>
<nav>
  <a routerLink="/crisis-center" routerLinkActive="active" ariaCurrentWhenActive="page">Crisis Center</a>
  <a routerLink="/heroes" routerLinkActive="active" ariaCurrentWhenActive="page">Heroes</a>
  <a routerLink="/admin" routerLinkActive="active" ariaCurrentWhenActive="page">Admin</a>
  <a [routerLink]="[{ outlets: { popup: ['compose'] } }]">Contact</a>
</nav>
<div [@routeAnimation]="getAnimationData()">
  <router-outlet></router-outlet>
</div>
<router-outlet name="popup"></router-outlet>
```



###### Guard the admin feature

Currently, every route within the Crisis Center is open to everyone. The new admin feature should be accessible only to authenticated users.

Write a `canActivate()` guard method to redirect anonymous users to the login page when they try to enter the admin area.

Create a new file named `auth.guard.ts` in the `auth` folder. The `auth.guard.ts` file will contain the `authGuard` function.

```shell
ng generate guard auth/auth
```

To demonstrate the fundamentals, this example only logs to the console, returns `true` immediately, and lets navigation proceed:

( src/app/auth/auth.guard.ts (excerpt) )

```typescript
export const authGuard = () => {
  console.log('authGuard#canActivate called');
  return true;
};
```



Next, open `admin-routing.module.ts`, import the `authGuard` function, and update the admin route with a `canActivate` guard property that references it:

( src/app/admin/admin-routing.module.ts (guarded admin route) )

```typescript
import {authGuard} from '../auth/auth.guard';

import {AdminDashboardComponent} from './admin-dashboard/admin-dashboard.component';
import {AdminComponent} from './admin/admin.component';
import {ManageCrisesComponent} from './manage-crises/manage-crises.component';
import {ManageHeroesComponent} from './manage-heroes/manage-heroes.component';

const adminRoutes: Routes = [{
  path: 'admin',
  component: AdminComponent,
  canActivate: [authGuard],

  children: [{
    path: '',
    children: [
      {path: 'crises', component: ManageCrisesComponent},
      {path: 'heroes', component: ManageHeroesComponent},
      {path: '', component: AdminDashboardComponent}
    ],
  }]
}];

@NgModule({imports: [RouterModule.forChild(adminRoutes)], exports: [RouterModule]})
export class AdminRoutingModule {
}
```

The admin feature is now protected by the guard, but the guard requires more customization to work fully.



###### Authenticate with `authGuard`

Make the `authGuard` mimic authentication.

The `authGuard` should call an application service that can log in a user and retain information about the current user. Generate a new `AuthService` in the `auth` folder:

```shell
ng generate service auth/auth
```

Update the `AuthService` to log in the user:

( src/app/auth/auth.service.ts (excerpt) )

```typescript
import { Injectable } from '@angular/core';

import { Observable, of } from 'rxjs';
import { tap, delay } from 'rxjs/operators';

@Injectable({
  providedIn: 'root',
})
export class AuthService {
  isLoggedIn = false;

  // store the URL so we can redirect after logging in
  redirectUrl: string | null = null;

  login(): Observable<boolean> {
    return of(true).pipe(
      delay(1000),
      tap(() => this.isLoggedIn = true)
    );
  }

  logout(): void {
    this.isLoggedIn = false;
  }
}
```

Although it doesn't actually log in, it has an `isLoggedIn` flag to tell you whether the user is authenticated. Its `login()` method simulates an API call to an external service by returning an observable that resolves successfully after a short pause. The `redirectUrl` property stores the URL that the user wanted to access so you can navigate to it after authentication.



**To keep things minimal, this example redirects unauthenticated users to `/admin`.**



Revise the `authGuard` to call the `AuthService`.

( src/app/auth/auth.guard.ts (v2) )

```typescript
import {inject} from '@angular/core';
import { Router } from '@angular/router';

import {AuthService} from './auth.service';

export const authGuard = () => {
  const authService = inject(AuthService);
  const router = inject(Router);

  if (authService.isLoggedIn) {
    return true;
  }

  // Redirect to the login page
  return router.parseUrl('/login');
};
```

This guard returns a synchronous boolean result or a `UrlTree`. If the user is logged in, it returns `true` and the navigation continues. Otherwise, it redirects to a login page; a page you haven't created yet. Returning a `UrlTree` tells the `Router` to cancel the current navigation and schedule a new one to redirect the user.



###### Add the `LoginComponent`

You need a `LoginComponent` for the user to log in to the application. After logging in, you'll redirect to the stored URL if available, or use the default URL. There is nothing new about this component or the way you use it in the router configuration.

```shell
ng generate component auth/login
```



Register a `/login` route in the `auth/auth-routing.module.ts` file. In `app.module.ts`, import and add `AuthModule` to the `AppModule` imports array.

(src/app/app.module.ts)

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

import { AppComponent } from './app.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';
import { ComposeMessageComponent } from './compose-message/compose-message.component';

import { AppRoutingModule } from './app-routing.module';
import { HeroesModule } from './heroes/heroes.module';
import { AuthModule } from './auth/auth.module';

@NgModule({
  imports: [
    BrowserModule,
    BrowserAnimationsModule,
    FormsModule,
    HeroesModule,
    AuthModule,
    AppRoutingModule,
  ],
  declarations: [
    AppComponent,
    ComposeMessageComponent,
    PageNotFoundComponent
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule {
}
```



(src/app/auth/login/login.component.html)

```typescript
<h2>Login</h2>
<p>{{message}}</p>
<p>
  <button type="button" (click)="login()"  *ngIf="!authService.isLoggedIn">Login</button>
  <button type="button" (click)="logout()" *ngIf="authService.isLoggedIn">Logout</button>
</p>
```



(src/app/auth/login/login.component.ts)

```typescript
import { Component } from '@angular/core';
import { Router } from '@angular/router';
import { AuthService } from '../auth.service';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.css']
})
export class LoginComponent {
  message: string;

  constructor(public authService: AuthService, public router: Router) {
    this.message = this.getMessage();
  }

  getMessage() {
    return 'Logged ' + (this.authService.isLoggedIn ? 'in' : 'out');
  }

  login() {
    this.message = 'Trying to log in ...';

    this.authService.login().subscribe(() => {
      this.message = this.getMessage();
      if (this.authService.isLoggedIn) {
        // Usually you would use the redirect URL from the auth service.
        // However to keep the example simple, we will always redirect to `/admin`.
        const redirectUrl = '/admin';

        // Redirect the user
        this.router.navigate([redirectUrl]);
      }
    });
  }

  logout() {
    this.authService.logout();
    this.message = this.getMessage();
  }
}
```



(src/app/auth/auth.module.ts)

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

import { LoginComponent } from './login/login.component';
import { AuthRoutingModule } from './auth-routing.module';

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    AuthRoutingModule
  ],
  declarations: [
    LoginComponent
  ]
})
export class AuthModule {}
```



##### `canActivateChild`: guarding child routes

You can also protect child routes with the `canActivateChild` guard. The `canActivateChild` guard is similar to the `canActivate` guard. The key difference is that it runs before any child route is activated.

You protected the admin feature module from unauthorized access. You should also protect child routes *within* the feature module.

Add the same `authGuard` to the `component-less` admin route to protect all other child routes at one time instead of adding the `authGuard` to each route individually.



src/app/admin/admin-routing.module.ts (excerpt)

```typescript
const adminRoutes: Routes = [
  {
    path: 'admin',
    component: AdminComponent,
    canActivate: [authGuard],
    children: [
      {
        path: '',
        canActivateChild: [authGuard],
        children: [
          { path: 'crises', component: ManageCrisesComponent },
          { path: 'heroes', component: ManageHeroesComponent },
          { path: '', component: AdminDashboardComponent }
        ]
      }
    ]
  }
];

@NgModule({
  imports: [
    RouterModule.forChild(adminRoutes)
  ],
  exports: [
    RouterModule
  ]
})
export class AdminRoutingModule {}
```



##### `canDeactivate`: handling unsaved changes

Back in the "Heroes" workflow, the application accepts every change to a hero immediately without validation.

In the real world, you might have to accumulate the users changes, validate across fields, validate on the server, or hold changes in a pending state until the user confirms them as a group or cancels and reverts all changes.

When the user navigates away, you can let the user decide what to do with unsaved changes. If the user cancels, you'll stay put and allow more changes. If the user approves, the application can save.

You still might delay navigation until the save succeeds. If you let the user move to the next screen immediately and saving were to fail (perhaps the data is ruled invalid), you would lose the context of the error.

You need to stop the navigation while you wait, asynchronously, for the server to return with its answer.

The `canDeactivate` guard helps you decide what to do with unsaved changes and how to proceed.



###### Cancel and save

Users update crisis information in the `CrisisDetailComponent`. Unlike the `HeroDetailComponent`, the user changes do not update the crisis entity immediately. Instead, the application updates the entity when the user presses the Save button and discards the changes when the user presses the Cancel button.

Both buttons navigate back to the crisis list after save or cancel.



( src/app/crisis-center/crisis-detail/crisis-detail.component.ts (cancel and save methods) )

```typescript
cancel() {
  this.gotoCrises();
}

save() {
  this.crisis.name = this.editName;
  this.gotoCrises();
}
```



In this scenario, the user could click the heroes link, cancel, push the browser back button, or navigate away without saving.

This example application asks the user to be explicit with a confirmation dialog box that waits asynchronously for the user's response.



**You could wait for the user's answer with synchronous, blocking code, however, the application is more responsive —and can do other work— by waiting for the user's answer asynchronously.**



Generate a `Dialog` service to handle user confirmation.

```shell
ng generate service dialog
```



Add a `confirm()` method to the `DialogService` to prompt the user to confirm their intent. The `window.confirm` is a blocking action that displays a modal dialog and waits for user interaction.



(src/app/dialog.service.ts)

```typescript
import { Injectable } from '@angular/core';
import { Observable, of } from 'rxjs';

/**
 * Async modal dialog service
 * DialogService makes this app easier to test by faking this service.
 * TODO: better modal implementation that doesn't use window.confirm
 */
@Injectable({
  providedIn: 'root',
})
export class DialogService {
  /**
   * Ask user to confirm an action. `message` explains the action and choices.
   * Returns observable resolving to `true`=confirm or `false`=cancel
   */
  confirm(message?: string): Observable<boolean> {
    const confirmation = window.confirm(message || 'Is it OK?');

    return of(confirmation);
  }
}
```



```http
https://angular.io/guide/router-tutorial-toh#candeactivate-handling-unsaved-changes
```



## Forms



### Introduction to forms in Angular

Handling user input with forms is the cornerstone of many common applications. Applications use forms to enable users to log in, to update a profile, to enter sensitive information, and to perform many other data-entry tasks.

Angular provides two different approaches to handling user input through forms: reactive and template-driven. Both capture user input events from the view, validate the user input, create a form model and data model to update, and provide a way to track changes.

This guide provides information to help you decide which type of form works best for your situation. It introduces the common building blocks used by both approaches. It also summarizes the key differences between the two approaches, and demonstrates those differences in the context of setup, data flow, and testing.



### Choosing an approach

Reactive forms and template-driven forms process and manage form data differently. Each approach offers different advantages.

| FORMS                 | DETAILS                                                      |
| :-------------------- | :----------------------------------------------------------- |
| Reactive forms        | Provide direct, explicit access to the underlying form's object model. Compared to template-driven forms, they are more robust: they're more scalable, reusable, and testable. If forms are a key part of your application, or you're already using reactive patterns for building your application, use reactive forms. |
| Template-driven forms | Rely on directives in the template to create and manipulate the underlying object model. They are useful for adding a simple form to an app, such as an email list signup form. They're straightforward to add to an app, but they don't scale as well as reactive forms. If you have very basic form requirements and logic that can be managed solely in the template, template-driven forms could be a good fit. |



### Key differences

The following table summarizes the key differences between reactive and template-driven forms.

|                                                              | REACTIVE                             | TEMPLATE-DRIVEN                 |
| :----------------------------------------------------------- | :----------------------------------- | :------------------------------ |
| [Setup of form model](https://angular.io/guide/forms-overview#setup) | Explicit, created in component class | Implicit, created by directives |
| [Data model](https://angular.io/guide/forms-overview#mutability-of-the-data-model) | Structured and immutable             | Unstructured and mutable        |
| [Data flow](https://angular.io/guide/forms-overview#data-flow-in-forms) | Synchronous                          | Asynchronous                    |
| [Form validation](https://angular.io/guide/forms-overview#validation) | Functions                            | Directives                      |



### Scalability

If forms are a central part of your application, scalability is very important. Being able to reuse form models across components is critical.

Reactive forms are more scalable than template-driven forms. They provide direct access to the underlying form API, and use [synchronous data flow](https://angular.io/guide/forms-overview#data-flow-in-reactive-forms) between the view and the data model, which makes creating large-scale forms easier. Reactive forms require less setup for testing, and testing does not require deep understanding of change detection to properly test form updates and validation.

Template-driven forms focus on simple scenarios and are not as reusable. They abstract away the underlying form API, and use [asynchronous data flow](https://angular.io/guide/forms-overview#data-flow-in-template-driven-forms) between the view and the data model. The abstraction of template-driven forms also affects testing. Tests are deeply reliant on manual change detection execution to run properly, and require more setup.



### Setting up the form model

Both reactive and template-driven forms track value changes between the form input elements that users interact with and the form data in your component model. The two approaches share underlying building blocks, but differ in how you create and manage the common form-control instances.



#### Common form foundation classes

| BASE CLASSES           | DETAILS                                                      |
| :--------------------- | :----------------------------------------------------------- |
| `FormControl`          | Tracks the value and validation status of an individual form control. |
| `FormGroup`            | Tracks the same values and status for a collection of form controls. |
| `FormArray`            | Tracks the same values and status for an array of form controls. |
| `ControlValueAccessor` | Creates a bridge between Angular `FormControl` instances and built-in DOM elements. |



#### Setup in reactive forms

With reactive forms, you define the form model directly in the component class. The `[formControl]` directive links the explicitly created `FormControl` instance to a specific form element in the view, using an internal value accessor.

The following component implements an input field for a single control, using reactive forms. In this example, the form model is the `FormControl` instance.

```typescript
import { Component } from '@angular/core';
import { FormControl } from '@angular/forms';

@Component({
  selector: 'app-reactive-favorite-color',
  template: `
    Favorite Color: <input type="text" [formControl]="favoriteColorControl">
  `
})
export class FavoriteColorComponent {
  favoriteColorControl = new FormControl('');
}
```



Figure 1 shows how, in reactive forms, the form model is the source of truth; it provides the value and status of the form element at any given point in time, through the `[formControl]` directive on the input element.



**Figure 1.** *Direct access to forms model in a reactive form.*

 ![](./img/key-diff-reactive-forms.png)





#### Setup in template-driven forms

In template-driven forms, the form model is implicit, rather than explicit. The directive `NgModel` creates and manages a `FormControl` instance for a given form element.

The following component implements the same input field for a single control, using template-driven forms.

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-template-favorite-color',
  template: `
    Favorite Color: <input type="text" [(ngModel)]="favoriteColor">
  `
})
export class FavoriteColorComponent {
  favoriteColor = '';
}
```



In a template-driven form the source of truth is the template. You do not have direct programmatic access to the `FormControl` instance, as shown in Figure 2.



**Figure 2.** *Indirect access to forms model in a template-driven form.*

![](./img/key-diff-td-forms.png) 



### Data flow in forms

When an application contains a form, Angular must keep the view in sync with the component model and the component model in sync with the view. As users change values and make selections through the view, the new values must be reflected in the data model. Similarly, when the program logic changes values in the data model, those values must be reflected in the view.

Reactive and template-driven forms differ in how they handle data flowing from the user or from programmatic changes. The following diagrams illustrate both kinds of data flow for each type of form, using the favorite-color input field defined above.



#### Data flow in reactive forms

In reactive forms each form element in the view is directly linked to the form model (a `FormControl` instance). Updates from the view to the model and from the model to the view are synchronous and do not depend on how the UI is rendered.



The view-to-model diagram shows how data flows when an input field's value is changed from the view through the following steps.

1. The user types a value into the input element, in this case the favorite color *Blue*.
2. The form input element emits an "input" event with the latest value.
3. The control value accessor listening for events on the form input element immediately relays the new value to the `FormControl` instance.
4. The `FormControl` instance emits the new value through the `valueChanges` observable.
5. Any subscribers to the `valueChanges` observable receive the new value.

![](./img/dataflow-reactive-forms-vtm.png) 





The model-to-view diagram shows how a programmatic change to the model is propagated to the view through the following steps.

1. The user calls the `favoriteColorControl.setValue()` method, which updates the `FormControl` value.
2. The `FormControl` instance emits the new value through the `valueChanges` observable.
3. Any subscribers to the `valueChanges` observable receive the new value.
4. The control value accessor on the form input element updates the element with the new value.



![](./img/dataflow-reactive-forms-mtv.png) 





#### Data flow in template-driven forms

In template-driven forms, each form element is linked to a directive that manages the form model internally.



The view-to-model diagram shows how data flows when an input field's value is changed from the view through the following steps.

1. The user types *Blue* into the input element.

2. The input element emits an "input" event with the value *Blue*.

3. The control value accessor attached to the input triggers the `setValue()` method on the `FormControl` instance.

4. The `FormControl` instance emits the new value through the `valueChanges` observable.

5. Any subscribers to the `valueChanges` observable receive the new value.

6. The control value accessor also calls the `NgModel.viewToModelUpdate()` method which emits an `ngModelChange` event.

7. Because the component template uses two-way data binding for the `favoriteColor` property, the `favoriteColor` property in the component is updated to the value emitted by the `ngModelChange` event (*Blue*).

   ![](./img/dataflow-td-forms-vtm.png) 

   



The model-to-view diagram shows how data flows from model to view when the `favoriteColor` changes from *Blue* to *Red*, through the following steps

1. The `favoriteColor` value is updated in the component.

2. Change detection begins.

3. During change detection, the `ngOnChanges` lifecycle hook is called on the `NgModel` directive instance because the value of one of its inputs has changed.

4. The `ngOnChanges()` method queues an async task to set the value for the internal `FormControl` instance.

5. Change detection completes.

6. On the next tick, the task to set the `FormControl` instance value is executed.

7. The `FormControl` instance emits the latest value through the `valueChanges` observable.

8. Any subscribers to the `valueChanges` observable receive the new value.

9. The control value accessor updates the form input element in the view with the latest `favoriteColor` value.

   ![](./img/dataflow-td-forms-mtv.png) 





#### Mutability of the data model

The change-tracking method plays a role in the efficiency of your application.

| FORMS                 | DETAILS                                                      |
| :-------------------- | :----------------------------------------------------------- |
| Reactive forms        | Keep the data model pure by providing it as an immutable data structure. Each time a change is triggered on the data model, the `FormControl` instance returns a new data model rather than updating the existing data model. This gives you the ability to track unique changes to the data model through the control's observable. Change detection is more efficient because it only needs to update on unique changes. Because data updates follow reactive patterns, you can integrate with observable operators to transform data. |
| Template-driven forms | Rely on mutability with two-way data binding to update the data model in the component as changes are made in the template. Because there are no unique changes to track on the data model when using two-way data binding, change detection is less efficient at determining when updates are required. |

The difference is demonstrated in the previous examples that use the favorite-color input element.

- With reactive forms, the **`FormControl` instance** always returns a new value when the control's value is updated
- With template-driven forms, the **favorite color property** is always modified to its new value





### Form validation

Validation is an integral part of managing any set of forms. Whether you're checking for required fields or querying an external API for an existing username, Angular provides a set of built-in validators as well as the ability to create custom validators.

| FORMS                 | DETAILS                                                      |
| :-------------------- | :----------------------------------------------------------- |
| Reactive forms        | Define custom validators as **functions** that receive a control to validate |
| Template-driven forms | Tied to template **directives**, and must provide custom validator directives that wrap validation functions |

For more information, see [Form Validation](https://angular.io/guide/form-validation).





## Reactive forms

Reactive forms provide a model-driven approach to handling form inputs whose values change over time. This guide shows you how to create and update a basic form control, progress to using multiple controls in a group, validate form values, and create dynamic forms where you can add or remove controls at run time.



#### Overview of reactive forms

Reactive forms use an explicit and immutable approach to managing the state of a form at a given point in time. Each change to the form state returns a new state, which maintains the integrity of the model between changes. Reactive forms are built around [observable](https://angular.io/guide/glossary#observable) streams, where form inputs and values are provided as streams of input values, which can be accessed synchronously.

Reactive forms also provide a straightforward path to testing because you are assured that your data is consistent and predictable when requested. Any consumers of the streams have access to manipulate that data safely.

Reactive forms differ from [template-driven forms](https://angular.io/guide/forms) in distinct ways. Reactive forms provide synchronous access to the data model, immutability with observable operators, and change tracking through observable streams.

Template-driven forms let direct access modify data in your template, but are less explicit than reactive forms because they rely on directives embedded in the template, along with mutable data to track changes asynchronously. See the [Forms Overview](https://angular.io/guide/forms-overview) for detailed comparisons between the two paradigms.





### 1.Adding a basic form control

There are three steps to using form controls.

1. Register the reactive forms module in your application. This module declares the reactive-form directives that you need to use reactive forms.
2. Generate a new component and instantiate a new `FormControl`.
3. Register the `FormControl` in the template.

You can then display the form by adding the component to the template.

The following examples show how to add a single form control. In the example, the user enters their name into an input field, captures that input value, and displays the current value of the form control element.



| ACTION                               | DETAILS                                                      |
| :----------------------------------- | :----------------------------------------------------------- |
| Register the reactive forms module   | To use reactive form controls, import `ReactiveFormsModule` from the `@angular/forms` package and add it to your NgModule's `imports` array.![](./img/register_reactiveFormModule.png) |
| Generate a new `FormControl`         | Use the [CLI command](https://angular.io/cli/generate#component-command) `ng generate` to generate a component in your project to host the control.![](./img/gen_newFormControl.png)                                       Use the constructor of `FormControl` to set its initial value, which in this case is an empty string. By creating these controls in your component class, you get immediate access to listen for, update, and validate the state of the form input. |
| Register the control in the template | After you create the control in the component class, you must associate it with a form control element in the template. Update the template with the form control using the `formControl` binding provided by `FormControlDirective`, which is also included in the `ReactiveFormsModule`.![](./img/reg_ctrl_in_the_template.png)                                        ---*For a summary of the classes and directives provided by `ReactiveFormsModule`, see the following [Reactive forms API](https://angular.io/guide/reactive-forms#reactive-forms-api) section                                                                                                                                                           *For complete syntax details of these classes and directives, see the API reference documentation for the [Forms package](https://angular.io/api/forms)                                                                                                                                                            Using the template binding syntax, the form control is now registered to the `name` input element in the template. The form control and DOM element communicate with each other: the view reflects changes in the model, and the model reflects changes in the view. |
| Display the component                | The `FormControl` assigned to the `name` property is displayed when the property's host component is added to a template.         ![](./img/display_comp.png) ![Name Editor, which has a name label and an input so the user can enter a name](https://angular.io/generated/images/guide/reactive-forms/name-editor-1.png) |





##### Displaying a form control value

You can display the value in the following ways.

- Through the `valueChanges` observable where you can listen for changes in the form's value in the template using `AsyncPipe` or in the component class using the `subscribe()` method
- With the `value` property, which gives you a snapshot of the current value

The following example shows you how to display the current value using interpolation in the template.



( src/app/name-editor/name-editor.component.html (control value) )

```html
<p>Value: {{ name.value }}</p>
```



The displayed value changes as you update the form control element.

Reactive forms provide access to information about a given control through properties and methods provided with each instance. These properties and methods of the underlying [AbstractControl](https://angular.io/api/forms/AbstractControl) class are used to control form state and determine when to display messages when handling [input validation](https://angular.io/guide/reactive-forms#basic-form-validation).

Read about other `FormControl` properties and methods in the [API Reference](https://angular.io/api/forms/FormControl).



##### Replacing a form control value

Reactive forms have methods to change a control's value programmatically, which gives you the flexibility to update the value without user interaction. A form control instance provides a `setValue()` method that updates the value of the form control and validates the structure of the value provided against the control's structure. For example, when retrieving form data from a backend API or service, use the `setValue()` method to update the control to its new value, replacing the old value entirely.

The following example adds a method to the component class to update the value of the control to *Nancy* using the `setValue()` method.



( src/app/name-editor/name-editor.component.ts (update value) )

```typescript
updateName() {
  this.name.setValue('Nancy');
}
```

Update the template with a button to simulate a name update. When you click the **Update Name** button, the value entered in the form control element is reflected as its current value.



( src/app/name-editor/name-editor.component.html (update value) )

```html
<button type="button" (click)="updateName()">Update Name</button>
```

The form model is the source of truth for the control, so when you click the button, the value of the input is changed within the component class, overriding its current value.



**NOTE**:
In this example, you're using a single control. When using the `setValue()` method with a [form group](https://angular.io/guide/reactive-forms#grouping-form-controls) or [form array](https://angular.io/guide/reactive-forms#creating-dynamic-forms) instance, the value needs to match the structure of the group or array.





### 2.Grouping form controls

Forms typically contain several related controls. Reactive forms provide two ways of grouping multiple related controls into a single input form.



| FORM GROUPS | DETAILS                                                      |
| :---------- | :----------------------------------------------------------- |
| Form group  | Defines a form with a fixed set of controls that you can manage together. Form group basics are discussed in this section. You can also [nest form groups](https://angular.io/guide/reactive-forms#nested-groups) to create more complex forms. |
| Form array  | Defines a dynamic form, where you can add and remove controls at run time. You can also nest form arrays to create more complex forms. For more about this option, see [Creating dynamic forms](https://angular.io/guide/reactive-forms#dynamic-forms). |



Just as a form control instance gives you control over a single input field, a form group instance tracks the form state of a group of form control instances (for example, a form). Each control in a form group instance is tracked by name when creating the form group. The following example shows how to manage multiple form control instances in a single group.



Generate a `ProfileEditor` component and import the `FormGroup` and `FormControl` classes from the `@angular/forms` package.

```shell
ng generate component ProfileEditor
```



( ng generate component ProfileEditor )

```typescript
import { FormGroup, FormControl } from '@angular/forms';
```



To add a form group to this component, take the following steps.

1. Create a `FormGroup` instance.
2. Associate the `FormGroup` model and view.
3. Save the form data.

| ACTION                                   | DETAILS                                                      |
| :--------------------------------------- | :----------------------------------------------------------- |
| Create a `FormGroup` instance            | Create a property in the component class named `profileForm` and set the property to a new form group instance. To initialize the form group, provide the constructor with an object of named keys mapped to their control. For the profile form, add two form control instances with the names `firstName` and `lastName`.![](./img/cre_FormGrop_inst.png)                                      The individual form controls are now collected within a group. A `FormGroup` instance provides its model value as an object reduced from the values of each control in the group. A form group instance has the same properties (such as `value` and `untouched`) and methods (such as `setValue()`) as a form control instance. |
| Associate the `FormGroup` model and view | A form group tracks the status and changes for each of its controls, so if one of the controls changes, the parent control also emits a new status or value change. The model for the group is maintained from its members. After you define the model, you must update the template to reflect the model in the view.![](./img/associate_FormGrop_model_and_view.png)                                  **NOTE**: Just as a form group contains a group of controls, the *profileForm* `FormGroup` is bound to the `form` element with the `FormGroup` directive, creating a communication layer between the model and the form containing the inputs.                                                                                                                                                             The `formControlName` input provided by the `FormControlName` directive binds each individual input to the form control defined in `FormGroup`. The form controls communicate with their respective elements. They also communicate changes to the form group instance, which provides the source of truth for the model value. |
| Save form data                           | The `ProfileEditor` component accepts input from the user, but in a real scenario you want to capture the form value and make available for further processing outside the component. The `FormGroup` directive listens for the `submit` event emitted by the `form` element and emits an `ngSubmit` event that you can bind to a callback function. Add an `ngSubmit` event listener to the `form` tag with the `onSubmit()` callback method.![](./img/save_from_data_submit.png)                                                          The `onSubmit()` method in the `ProfileEditor` component captures the current value of `profileForm`. Use `EventEmitter` to keep the form encapsulated and to provide the form value outside the component. The following example uses `console.warn` to log a message to the browser console.![](./img/onSubmit_method.png)                                                 The `submit` event is emitted by the `form` tag using the built-in DOM event. You trigger the event by clicking a button with `submit` type. This lets the user press the **Enter** key to submit the completed form. Use a `button` element to add a button to the bottom of the form to trigger the form submission![](./img/submit_btn.png)                                      **NOTE**: The button in the preceding snippet also has a `disabled` binding attached to it to disable the button when `profileForm` is invalid. You aren't performing any validation yet, so the button is always enabled. Basic form validation is covered in the [Validating form input](https://angular.io/guide/reactive-forms#basic-form-validation) section. |
| Display the component                    | To display the `ProfileEditor` component that contains the form, add it to a component template.![](./img/display_profileEditor.png)                     `ProfileEditor` lets you manage the form control instances for the `firstName` and `lastName` controls within the form group instance. |





#### Creating nested form groups

Form groups can accept both individual form control instances and other form group instances as children. This makes composing complex form models easier to maintain and logically group together.

When building complex forms, managing the different areas of information is easier in smaller sections. Using a nested form group instance lets you break large forms groups into smaller, more manageable ones.

To make more complex forms, use the following steps.

1. Create a nested group.
2. Group the nested form in the template.

Some types of information naturally fall into the same group. A name and address are typical examples of such nested groups, and are used in the following examples.



| ACTION                                | DETAILS                                                      |
| :------------------------------------ | :----------------------------------------------------------- |
| Create a nested group                 | To create a nested group in `profileForm`, add a nested `address` element to the form group instance.![](./img/create_a_nested_group.png)                                                 In this example, `address group` combines the current `firstName` and `lastName` controls with the new `street`, `city`, `state`, and `zip` controls. Even though the `address` element in the form group is a child of the overall `profileForm` element in the form group, the same rules apply with value and status changes. Changes in status and value from the nested form group propagate to the parent form group, maintaining consistency with the overall model. |
| Group the nested form in the template | After you update the model in the component class, update the template to connect the form group instance and its input elements. Add the `address` form group containing the `street`, `city`, `state`, and `zip` fields to the `ProfileEditor` template.![](./img/group_the_nestedForm_in_the_template.png)                                          The `ProfileEditor` form is displayed as one group, but the model is broken down further to represent the logical grouping areas.                                                                                                       ![](./img/nested_group.png)                                                                                                                                             **TIP**:   Display the value for the form group instance in the component template using the `value` property and `JsonPipe`. |



#### Updating parts of the data model

When updating the value for a form group instance that contains multiple controls, you might only want to update parts of the model. This section covers how to update specific parts of a form control data model.

There are two ways to update the model value:

| METHODS        | DETAILS                                                      |
| :------------- | :----------------------------------------------------------- |
| `setValue()`   | Set a new value for an individual control. The `setValue()` method strictly adheres to the structure of the form group and replaces the entire value for the control. |
| `patchValue()` | Replace any properties defined in the object that have changed in the form model. |

The strict checks of the `setValue()` method help catch nesting errors in complex forms, while `patchValue()` fails silently on those errors.



In `ProfileEditorComponent`, use the `updateProfile` method with the following example to update the first name and street address for the user.

( src/app/profile-editor/profile-editor.component.ts (patch value) )

```typescript
updateProfile() {
  this.profileForm.patchValue({
    firstName: 'Nancy',
    address: {
      street: '123 Drew Street'
    }
  });
}
```



Simulate an update by adding a button to the template to update the user profile on demand.

( src/app/profile-editor/profile-editor.component.html (update value) )

```html
<button type="button" (click)="updateProfile()">Update Profile</button>
```

When a user clicks the button, the `profileForm` model is updated with new values for `firstName` and `street`. Notice that `street` is provided in an object inside the `address` property. This is necessary because the `patchValue()` method applies the update against the model structure. `PatchValue()` **only updates properties** that the form model defines.



<hr>



### 3.Using the FormBuilder service to generate controls

Creating form control instances manually can become repetitive when dealing with multiple forms. The `FormBuilder` service provides convenient methods for generating controls.

Use the following steps to take advantage of this service.

1. Import the `FormBuilder` class.
2. Inject the `FormBuilder` service.
3. Generate the form contents.

The following examples show how to refactor the `ProfileEditor` component to use the form builder service to create form control and form group instances.



| ACTION                         | DETAILS                                                      |
| :----------------------------- | :----------------------------------------------------------- |
| Import the FormBuilder class   | Import the `FormBuilder` class from the `@angular/forms` package.![](./img/import_FB.png) |
| Inject the FormBuilder service | The `FormBuilder` service is an injectable provider that is provided with the reactive forms module. Inject this dependency by adding it to the component constructor.![](./img/inject_FB_service.png) |
| Generate form controls         | The `FormBuilder` service has three methods: `control()`, `group()`, and `array()`. These are factory methods for generating instances in your component classes including form controls, form groups, and form arrays. Use the `group` method to create the `profileForm` controls.![](./img/gen_FromControls.png)                    In the preceding example, you use the `group()` method with the same object to define the properties in the model. The value for each control name is an array containing the initial value as the first item in the array.                                                                                                                                                                                           **TIP**: You can define the control with just the initial value, but if your controls need sync or async validation, add sync and async validators as the second and third items in the array.                                                               Compare using the form builder to creating the instances manually.![](./img/profileEditor_manually.png) ![](./img/profileEditor_fb.png) |



### 4.Validating form input

*Form validation* is used to ensure that user input is complete and correct. This section covers adding a single validator to a form control and displaying the overall form status. Form validation is covered more extensively in the [Form Validation](https://angular.io/guide/form-validation) guide.

Use the following steps to add form validation.

1. Import a validator function in your form component.
2. Add the validator to the field in the form.
3. Add logic to handle the validation status.

The most common validation is making a field required. The following example shows how to add a required validation to the `firstName` control and display the result of validation.

| ACTION                      | DETAILS                                                      |
| :-------------------------- | :----------------------------------------------------------- |
| Import a validator function | Reactive forms include a set of validator functions for common use cases. These functions receive a control to validate against and return an error object or a null value based on the validation check. Import the `Validators` class from the `@angular/forms` package.![](./img/import_validator.png) |
| Make a field required       | In the `ProfileEditor` component, add the `Validators.required` static method as the second item in the array for the `firstName` control.![](./img/make_a_field_required.png) |
| Display form status         | When you add a required field to the form control, its initial status is invalid. This invalid status propagates to the parent form group element, making its status invalid. Access the current status of the form group instance through its `status` property. Display the current status of `profileForm` using interpolation.![](./img/display_form_status.png)                                                            The **Submit** button is disabled because `profileForm` is invalid due to the required `firstName` form control. After you fill out the `firstName` input, the form becomes valid and the **Submit** button is enabled.                                     For more on form validation, visit the [Form Validation](https://angular.io/guide/form-validation) guide. |



## Validating form input

You can improve overall data quality by validating user input for accuracy and completeness. This page shows how to validate user input from the UI and display useful validation messages, in both reactive and template-driven forms.





### Validating input in reactive forms

In a reactive form, the source of truth is the component class. Instead of adding validators through attributes in the template, you add validator functions directly to the form control model in the component class. Angular then calls these functions whenever the value of the control changes.



#### Validator functions

Validator functions can be either synchronous or asynchronous.

| VALIDATOR TYPE   | DETAILS                                                      |
| :--------------- | :----------------------------------------------------------- |
| Sync validators  | Synchronous functions that take a control instance and immediately return either a set of validation errors or `null`. Pass these in as the second argument when you instantiate a `FormControl`. |
| Async validators | Asynchronous functions that take a control instance and return a Promise or Observable that later emits a set of validation errors or `null`. Pass these in as the third argument when you instantiate a `FormControl`. |

For performance reasons, Angular only runs async validators if all sync validators pass. Each must complete before errors are set.



#### Built-in validator functions

You can choose to [write your own validator functions](https://angular.io/guide/form-validation#custom-validators), or you can use some of Angular's built-in validators.

The same built-in validators that are available as attributes in template-driven forms, such as `required` and `minlength`, are all available to use as functions from the `Validators` class. For a full list of built-in validators, see the [Validators](https://angular.io/api/forms/Validators) API reference.

To update the hero form to be a reactive form, use some of the same built-in validators —this time, in function form, as in the following example.



( reactive/hero-form-reactive.component.ts (validator functions) )

```typescript
ngOnInit(): void {
  this.heroForm = new FormGroup({
    name: new FormControl(this.hero.name, [
      Validators.required,
      Validators.minLength(4),
      forbiddenNameValidator(/bob/i) // <-- Here's how you pass in the custom validator.
    ]),
    alterEgo: new FormControl(this.hero.alterEgo),
    power: new FormControl(this.hero.power, Validators.required)
  });

}

get name() { return this.heroForm.get('name'); }

get power() { return this.heroForm.get('power'); }
```



In this example, the `name` control sets up two built-in validators —`Validators.required` and `Validators.minLength(4)`— and one custom validator, `forbiddenNameValidator`. (For more details see [custom validators](https://angular.io/guide/form-validation#custom-validators).)

All of these validators are synchronous, so they are passed as the second argument. Notice that you can support multiple validators by passing the functions in as an array.

This example also adds a few getter methods. In a reactive form, you can always access any form control through the `get` method on its parent group, but sometimes it's useful to define getters as shorthand for the template.



If you look at the template for the `name` input again, it is fairly similar to the template-driven example.

( reactive/hero-form-reactive.component.html (name with error msg) )

```html
<input type="text" id="name" class="form-control"
      formControlName="name" required>

<div *ngIf="name.invalid && (name.dirty || name.touched)"
    class="alert alert-danger">

  <div *ngIf="name.errors?.['required']">
    Name is required.
  </div>
  <div *ngIf="name.errors?.['minlength']">
    Name must be at least 4 characters long.
  </div>
  <div *ngIf="name.errors?.['forbiddenName']">
    Name cannot be Bob.
  </div>
</div>
```

This form differs from the template-driven version in that it no longer exports any directives. Instead, it uses the `name` getter defined in the component class.

Notice that the `required` attribute is still present in the template. Although it's not necessary for validation, it should be retained to for accessibility purposes.



<hr>

### Defining custom validators

The built-in validators don't always match the exact use case of your application, so you sometimes need to create a custom validator.

Consider the `forbiddenNameValidator` function from previous [reactive-form examples](https://angular.io/guide/form-validation#reactive-component-class). Here's what the definition of that function looks like.

( shared/forbidden-name.directive.ts (forbiddenNameValidator) )

```typescript
/** A hero's name can't match the given regular expression */
export function forbiddenNameValidator(nameRe: RegExp): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const forbidden = nameRe.test(control.value);
    return forbidden ? {forbiddenName: {value: control.value}} : null;
  };
}
```

The function is a factory that takes a regular expression to detect a *specific* forbidden name and returns a validator function.

In this sample, the forbidden name is "bob", so the validator rejects any hero name containing "bob". Elsewhere it could reject "alice" or any name that the configuring regular expression matches.

The `forbiddenNameValidator` factory returns the configured validator function. That function takes an Angular control object and **returns** *either* **null** if the control value is **valid** *or* **a validation error object**. The validation error object typically has a property whose name is the validation key, `'forbiddenName'`, and whose value is an arbitrary dictionary of values that you could insert into an error message, `{name}`.

Custom async validators are similar to sync validators, but they must instead return a Promise or observable that later emits null or a validation error object. In the case of an observable, the observable must complete, at which point the form uses the last value emitted for validation.





##### Adding custom validators to reactive forms

In reactive forms, add a custom validator by passing the function directly to the `FormControl`.

( reactive/hero-form-reactive.component.ts (validator functions) )

```typescript
this.heroForm = new FormGroup({
  name: new FormControl(this.hero.name, [
    Validators.required,
    Validators.minLength(4),
    forbiddenNameValidator(/bob/i) // <-- Here's how you pass in the custom validator.
  ]),
  alterEgo: new FormControl(this.hero.alterEgo),
  power: new FormControl(this.hero.power, Validators.required)
});
```



### Control status CSS classes

Angular automatically mirrors many control properties onto the form control element as CSS classes. Use these classes to style form control elements according to the state of the form. The following classes are currently supported.

- `.ng-valid`
- `.ng-invalid`
- `.ng-pending`
- `.ng-pristine`
- `.ng-dirty`
- `.ng-untouched`
- `.ng-touched`
- `.ng-submitted` (enclosing form element only)

In the following example, the hero form uses the `.ng-valid` and `.ng-invalid` classes to set the color of each form control's border.



( forms.css (status classes) )

```css
.ng-valid[required], .ng-valid.required  {
  border-left: 5px solid #42A948; /* green */
}

.ng-invalid:not(form)  {
  border-left: 5px solid #a94442; /* red */
}

.alert div {
  background-color: #fed3d3;
  color: #820000;
  padding: 1rem;
  margin-bottom: 1rem;
}

.form-group {
  margin-bottom: 1rem;
}

label {
  display: block;
  margin-bottom: .5rem;
}

select {
  width: 100%;
  padding: .5rem;
}
```





### Cross-field validation

A cross-field validator is a [custom validator](https://angular.io/guide/form-validation#custom-validators) that compares the values of different fields in a form and accepts or rejects them in combination. For example, you might have a form that offers mutually incompatible options, so that if the user can choose A or B, but not both. Some field values might also depend on others; a user might be allowed to choose B only if A is also chosen.

The following cross validation examples show how to do the following:

- Validate reactive or template-based form input based on the values of two sibling controls,
- Show a descriptive error message after the user interacted with the form and the validation failed.

The examples use cross-validation to ensure that heroes do not reveal their true identities by filling out the Hero Form. The validators do this by checking that the hero names and alter egos do not match.



#### Adding cross-validation to reactive forms

The form has the following structure:

```typescript
const heroForm = new FormGroup({
  'name': new FormControl(),
  'alterEgo': new FormControl(),
  'power': new FormControl()
});
```

Notice that the `name` and `alterEgo` are sibling controls. To evaluate both controls in a single custom validator, you must perform the validation in a common ancestor control: the `FormGroup`. You query the `FormGroup` for its child controls so that you can compare their values.



To add a validator to the `FormGroup`, pass the new validator in as the second argument on creation.

```typescript
const heroForm = new FormGroup({
  'name': new FormControl(),
  'alterEgo': new FormControl(),
  'power': new FormControl()
}, { validators: identityRevealedValidator });
```



The validator code is as follows.

( shared/identity-revealed.directive.ts )

```typescript
/** A hero's name can't match the hero's alter ego */
export const identityRevealedValidator: ValidatorFn = (control: AbstractControl): ValidationErrors | null => {
  const name = control.get('name');
  const alterEgo = control.get('alterEgo');

  return name && alterEgo && name.value === alterEgo.value ? { identityRevealed: true } : null;
};
```

The `identity` validator implements the `ValidatorFn` interface. It takes an Angular control object as an argument and returns either null if the form is valid, or `ValidationErrors` otherwise.

The validator retrieves the child controls by calling the `FormGroup`'s [get](https://angular.io/api/forms/AbstractControl#get) method, then compares the values of the `name` and `alterEgo` controls.

If the values do not match, the hero's identity remains secret, both are valid, and the validator returns null. If they do match, the hero's identity is revealed and the validator must mark the form as invalid by returning an error object.



To provide better user experience, the template shows an appropriate error message when the form is invalid.

(reactive/hero-form-template.component.html)

```typescript
<div *ngIf="heroForm.errors?.['identityRevealed'] && (heroForm.touched || heroForm.dirty)" class="cross-validation-error-message alert alert-danger">
    Name cannot match alter ego.
</div>
```



This `*ngIf` displays the error if the `FormGroup` has the cross validation error returned by the `identityRevealed` validator, but only if the user finished [interacting with the form](https://angular.io/guide/form-validation#dirty-or-touched).

--->To prevent the validator from displaying errors before the user has a chance to edit the form, you should check for either the `dirty` or `touched` states in a control.

- When the user changes the value in the watched field, the control is marked as "dirty"
- When the user blurs the form control element, the control is marked as "touched"





### Creating asynchronous validators

Asynchronous validators implement the `AsyncValidatorFn` and `AsyncValidator` interfaces. These are very similar to their synchronous counterparts, with the following differences.

- The `validate()` functions must return a Promise or an observable,
- The observable returned must be finite, meaning it must complete at some point. To convert an infinite observable into a finite one, pipe the observable through a filtering operator such as `first`, `last`, `take`, or `takeUntil`.

Asynchronous validation happens after the synchronous validation, and is performed only if the synchronous validation is successful. This check lets forms avoid potentially expensive async validation processes (such as an HTTP request) if the more basic validation methods have already found invalid input.

After asynchronous validation begins, the form control enters a `pending` state. Inspect the control's `pending` property and use it to **give visual feedback** about the ongoing validation operation.

A common UI pattern is to show a spinner while the async validation is being performed. The following example shows how to achieve this in a template-driven form.



A common UI pattern is to show a spinner while the async validation is being performed. The following example shows how to achieve this in a template-driven form.

```html
<input [(ngModel)]="name" #model="ngModel" appSomeAsyncValidator>
<app-spinner *ngIf="model.pending"></app-spinner>
```



#### Implementing a custom async validator

In the following example, an async validator ensures that heroes pick an alter ego that is not already taken. New heroes are constantly enlisting and old heroes are leaving the service, so the list of available alter egos cannot be retrieved ahead of time. To validate the potential alter ego entry, the validator must initiate an asynchronous operation to consult a central database of all currently enlisted heroes.

The following code creates the validator class, `UniqueAlterEgoValidator`, which implements the `AsyncValidator` interface.

```typescript
@Injectable({ providedIn: 'root' })
export class UniqueAlterEgoValidator implements AsyncValidator {
  constructor(private heroesService: HeroesService) {}

  validate(
    control: AbstractControl
  ): Observable<ValidationErrors | null> {
    return this.heroesService.isAlterEgoTaken(control.value).pipe(
      map(isTaken => (isTaken ? { uniqueAlterEgo: true } : null)),
      catchError(() => of(null))
    );
  }
}
```

The constructor injects the `HeroesService`, which defines the following interface.

```typescript
interface HeroesService {
  isAlterEgoTaken: (alterEgo: string) => Observable<boolean>;
}
```

```typescript
const ALTER_EGOS = ['Eric'];

@Injectable({ providedIn: 'root' })
export class HeroesService {
    isAlterEgoTaken(alterEgo: string): Observable<boolean> {
        console.log('alterEgo name>>>', alterEgo);

        const isTaken = ALTER_EGOS.includes(alterEgo);
        return of(isTaken).pipe(delay(400));
    }
}
```



 In a real world application, the `HeroesService` would be responsible for making an HTTP request to the hero database to check if the alter ego is available. From the validator's point of view, the actual implementation of the service is not important, so the example can just code against the `HeroesService` interface.

As the validation begins, the `UniqueAlterEgoValidator` delegates to the `HeroesService` `isAlterEgoTaken()` method with the current control value. At this point the control is marked as `pending` and remains in this state until the observable chain returned from the `validate()` method completes.

The `isAlterEgoTaken()` method dispatches an HTTP request that checks if the alter ego is available, and returns `Observable<boolean>` as the result. The `validate()` method pipes the response through the `map` operator and transforms it into a validation result.

The method then, like any validator, returns `null` if the form is valid, and `ValidationErrors` if it is not. This validator handles any potential errors with the `catchError` operator. In this case, the validator treats the `isAlterEgoTaken()` error as a successful validation, because failure to make a validation request does not necessarily mean that the alter ego is invalid. You could handle the error differently and return the `ValidationError` object instead.

After some time passes, the observable chain completes and the asynchronous validation is done. The `pending` flag is set to `false`, and the form validity is updated.



#### Adding async validators to reactive forms

To use an async validator in reactive forms, begin by injecting the validator into the constructor of the component class.

```typescript
constructor(private alterEgoValidator: UniqueAlterEgoValidator) {}
```

Then, pass the validator function directly to the `FormControl` to apply it.

In the following example, the `validate` function of `UniqueAlterEgoValidator` is applied to `alterEgoControl` by passing it to the control's `asyncValidators` option and binding it to the instance of `UniqueAlterEgoValidator` that was injected into `HeroFormReactiveComponent`. The value of `asyncValidators` can be either a single async validator function, or an array of functions. To learn more about `FormControl` options, see the [AbstractControlOptions](https://angular.io/api/forms/AbstractControlOptions) API reference.

```typescript
const alterEgoControl = new FormControl('', {
  asyncValidators: [this.alterEgoValidator.validate.bind(this.alterEgoValidator)],
  updateOn: 'blur'
});
```



#### Optimizing performance of async validators

By default, all validators run after every form value change. With synchronous validators, this does not normally have a noticeable impact on application performance. Async validators, however, commonly perform some kind of HTTP request to validate the control. Dispatching an HTTP request after every keystroke could put a strain on the backend API, and should be avoided if possible.

You can delay updating the form validity by changing the `updateOn` property from `change` (default) to `submit` or `blur`.

With template-driven forms, set the property in the template.



With template-driven forms, set the property in the template.

```html
<input [(ngModel)]="name" [ngModelOptions]="{updateOn: 'blur'}">
```

With reactive forms, set the property in the `FormControl` instance.

```typescript
new FormControl('', {updateOn: 'blur'});
```



#### Interaction with native HTML form validation

By default, Angular disables [native HTML form validation](https://developer.mozilla.org/docs/Web/Guide/HTML/Constraint_validation) by adding the `novalidate` attribute on the enclosing `<form>` and uses directives to match these attributes with validator functions in the framework. If you want to use native validation **in combination** with Angular-based validation, you can re-enable it with the `ngNativeValidate` directive. See the [API docs](https://angular.io/api/forms/NgForm#native-dom-validation-ui) for details.
