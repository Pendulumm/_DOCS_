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
