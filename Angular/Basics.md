# Basics

The first code to be executed is the main.ts which is basically:  
'
import { platformBrowserDynamic } from "@angular/platform-browser-dynamic";
import { AppModule } from "./app/app.module";

platformBrowserDynamic()
  .bootstrapModule(AppModule)
'
It bootstraps the application and it is the entrypoint runned when performing ng serve, the bootstrap also start a module, in particular the AppModule which specifies whhich components should be loaded at start, and the files to be bootstrapped.

In the app-module, inside the NgModule({}) there is an array called bootstrap where you put things that you want to be known from angular at bootstrap (the beginning)
Break your view in components every component has its template and his logic in order to behave differently.
You build reusable and independent part.

The AppComponent is the root component of the page.
Every component should be under his own folder.

This is an empty component:
		**import { Component } from '@angular/core';**
		**@Component()**
		**export class ServerComponent {**

		**}**
Inside the @Component we put two attributes:

  **selector: 'app-server',**
  **templateUrl: './server.component.html'**

Selector: a label that can be referenced in the template to address this component.
templateUrl: the relative path to the html file that represent the template of this component.
Here you can also declare a css binding as the template. (see myamadeus code) (using **styleUrls: \['CSS FILE HERE'\]**)
You can also declare the HTML code directly inside the @Component annotation, using the **template: \` CODE HERE \`**

The component class contains the variable which are called PROPERTY of the component and also the function that implements the logic

Instead of a templateUrl you can use template to define inside template (inside the ts component)

A MODULE is just a container of more component, like a package, it is basically a class with the @NgModule tag before it, like in the appModule:

**@NgModule({**
  **declarations: \[**
    **AppComponent     //list the component contained in this module**
  **\],**
  **imports: \[**
		**List imported modules here  //list of dependencies of the module (a list of other modules)**
  **\],**
  **providers: \[**
		**List services here**
**\],**
  **bootstrap: \[AppComponent\]  //which components will be bootstrapped toghether with the module**
**})**
**export class AppModule { }**

By default angular wont scan files, you have to explicitly tell the component in the ngModule, in the bootstrap if you want it to be there at start.
In general the module could be put in the 'declaration' array

![clip_image001.png](file:///C:/Users/rspinoni/AppData/Local/Temp/msohtmlclip1/01/clip_image001.png)

YOU CAN generate components with the cli: ng generate name.component

YOU CAN put the selector in squares:   selector: '\[app-server\]', it will check for attributes in the html instead of tags as before

A component CONSTRUCTOR is just a method called constructor()

Components are personilized tags thtat we create and that executes functionality that we want, is like adding a new tag over the browser definition of HTML 5

Method to delay things: setTimeout(functionToDoLater, delayInteger)

DATABINDINGS
Binding between typescript and HTML:

From HTML to HTML:
           <input a="title" #title>  now the #title is binded to the value provided in the input field, but this #title can only be used in the HTML and passed as input to a function call in the HTML; notice also that the #title is of type: **HTMLInputElement**.
From ts to HTML
		 - string interpolation {{stringData}} here you cannot put complex things, just a ternary operator or a variable or calling a method
		 - property binding \[property\] = "data"  generally used to dynamically change DOM properties binding them to a component field.
From HTML (events) to ts
		 - (event)="expression or method"           (ex: (click)="method")
How do I find (event) ? You can console log the DOM element in order to get this information
To pass info in the methods called with the event binding, you can simply use the refernce world: $event. (in the html)
In the method it is used **(event: Event)**

TWO WAY BINDING \[(ngModel)\]="data" (an attribute inside an html tag) 
for this you need to import the FormsModule in the AppModule: **import { FormsModule } from '@angular/forms';**

Naming convention: methods invoked by DOM events are called onSometing.

DIRECTIVES used generally as attributes in any HTML tag
Directives are instruction in the DOM, components in fact are directive with templates.
There are also built in directives in Angular that are usefull
Ex: \*ngIf="boolean" is a directive that make the HTML element appear and disappear from the DOM based on a boolean condition.
Inside the double quotes you can put an else:  **"condition: else tagName"**
Ng-template is a HTML tag that is used as a placeholder for the DOM position.
It is used togeter with a tag (**#tagName**)

Ex: **\*ngFor="let elem of PropertyOfTheComponentThatIsIterable"**  create the DOMelement for every element in the array passed.
It allows you to create dynamic list of DOM elements
You can also access the index of the current iteration:   **\*ngFor="let elem of PropertyOfTheComponentThatIsIterable; let I = index"**

ATTRIBUTE DIRECTIVES: they don't add or remove elements, but they change their values. They are attributes as before
Ex: ngStyle --> **\[ngStyle\]="{htmlElementProperty: methodThatReturnTheValue()}"**    
//in general a value is enough, no need for the method
Ex: ngClass --> **\[ngClass\]="{cssClassName: booleanCondition}**"   //the boolean can also be returned by a method.
In both cases inside the curly braces you have key values pairs that represent something.

In a Component you can use the @HostBinding tag to change the property of the host of the component (which is the father component)
For example: @HostBinding('attr.class') cssClass = "row";  //means that the father of this component will display the component using the row css class.
You can also bind to any class, property, or attribute of the host. @HostBinding('style.color') color = "red";

An angular application is a tree of components, the root is the appComponent and that's what it is bootstrapped at runtime, component could be composable and reusable, when a component renders, it also render its children.

Ng-serve looks at the .angular-cli.json which specifies the entry point (main.ts by default) main.ts is the file containing the bootstrapping line: platformBrowserDynamic().bootstrapModule(AppModule)
Then the module will load the component tha arespeicfied in his bootstrap array.
