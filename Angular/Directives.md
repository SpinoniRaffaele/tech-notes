# Directives

Attirbute directives:
Change the appearance or behavior of an element, component, or another directive.

Structural directives: change the structure of the DOM (like ngIf, ngFor, ngSwitch), ngFor - for example- is iterating over a collection (which is a property of the component) and it is creating an HTML tag for each element in the collection.

Ex ngFor: \*ngFor="let c of cities; let I = index"    //the c is the element the I is the index (optional).

EX: ngNonBindable: used as an HTML attribute to tell angular not to bind text (such as {{ person }}, is not gonna bind to the var person, but considered as plaint text)

ngSwitch example: <div \[ngSwitch\]="VARIABLE" >
<p \*ngSwitchCase="1" text>
<p \*ngSwitchDefault text2>
The ngSwitchDefault is not really needed, if it is not specified, nothing will be shown as a default behavior.

The ngStyle, has a different syntax: \[style.background-color\]="'yellow'"   it's used to change the style of elements.
Clearly the utility of this the possibility to use dynamic values instead of the hardcoded one.
The same holds for the CSS classes: ngClass. Ex: \[ngClass\]="{'bordered': isBordered}"  //isBordered is a variable of the component in consideration, which is a boolean.

You cannot have more than one structure directive in an HTML element, the structure directives uses the \* symbol to indicate to angular that they are structual directives, under the hood the \* is creating a ng-template wrppaer element

An example of attribute directive: \[ngClass\] = "{ CLASSSNAME: javascript\_condition }"  which applies the classname to the element and its children if th condition is met. Example: \[ngClass\] = "{ odd\_class: odd\_var % 2 !== 0 }" 

The same thing can be done with a single style property using \[ngStyle\]

BUILD YOUR OWN ATTRIBUTE DIRECTIVE
Create a .directive.ts file
Inside the file you need to export a class and annotate it with @Directive({ selector: '\[yourDirectiveName\]'})
IMPORTANT: in the HTML you will refer to the directive without the sqaure brakets, they are in the selector because of syntax reasons.

The directory class has access to the element that it is using the directive: you need to implement a constructor to access it:
Constructor(elementRef: ElementRef) {this.emyElement = elementRef}

Then, you can implement whatever, a common use case is to implement ngOnInit and change something there in order to have it runned at creation of the element. But you should not access directly Dom attributes in the code, so …

There is another field injected in the directive constructor: renderer: Renderer2

Then, you can   renderer.setStyle(DOM\_ELEMENT, 'PROPERTY TO BE CHANGED', 'PROPERTY VALUE')
For example: rendered.setStyle(element, 'bg-color', 'blue')

You generally should avoid to access directly the DOM because angular could run also outside a browser and the renderer will not crash in that case

In directives you can also react to events happening to the element: implement a method that will contains the reactive logic, annotate the method with @HostListener('EVENTNAME') and take a single field in the method which is the event: Event, thanks to this annotation the method will be called in automatic.  You can pass variable to the directive using @Input as you do for the components and then pass those parameters in the HTML TAG : <HTML\_TAG\_NAME  DIRECTIVE\_NAME  \[INPUT\_PROPERTY\]="'VALUE'">

hostListener allows you to listen to an event an calla custom function, differently from event binding, this is valid for the whole component (the host).

**HostListener and HostBinding** nice explanation: <https://www.digitalocean.com/community/tutorials/angular-hostbinding-hostlistener>   keep in mind that these two annotations are valid also for components.

Build YOUR OWN STRUCTURAL DIRECTIVE

You can do the same as before with a class annotated as directive
In this case you might need to use a condition input as the ngIf:
On order to take that value in the directive class use:
@Input() set unless(condition: boolean) {
		//logic
}

The set operator is taking the condition and calling the function whenever it changes, in the constructor of the directive you can access the ng-template that can be rendered, it is a field of type TemplateRef<any>, you can also access a viewContainer: ViewContainerRef, it is used to render the content under the directive, you ccan do it with:
viewContainerRef.createEmbeddedView(templateRef),   instead call the viewContainerRef.clear() to remove the HTML part under the directive.
Then you can use the structural directive you have created (remember to put the \* before the name in the HTML)
