# Components Databinding

High level design of the web app
Default components: app.component, login.component, header.component, footer.component, other inside depending on the pages and the elements

It's also good to design a high level model before starting the dev, these model should be some x.datamodel.ts files binded into the x component directory  (datamodel files are just a list of exported class and enums, the constructor are good but no really needed in the classes)

STRUCTURE THE FOLDER BY FEATURES, if something is not feature specific but used around, put It in the 'shared' folder.

Constructor trick:

Constructor(public attr1: string, public attr2: number)     //this Is creating the attributes in the class, and the constructor implicitly created.

**Communicate between components:**
The components can emits their own events, like the HTML elements.

FROM FATHER TO CHILD
From the father components we need to pass the property into the tag of the child component (from the father template) \[childProperty\]="fatherVariable"

You can also set CSS property in this way: \[class.visible\]="boolean\_var" or \[class.color\]="string\_var"

In the child we need to make the childProperties accessible to the outside of the component, use the decorator: @Input()   before the prop.
In the @Input() you can define an alias name exposed to the outside: @Input("aliasName") trueName: string;

FROM CHILD TO FATHER
Create a method receiving an event in the father
In the father template bind to the event as an attribute of the child component's tag:    (childEvent)="methodName($event)"
$event contains the thing that is emitted.

In the child we need to create the event emitter:  @Output() childEvent = new EventEmitter<string>();
Then when needed you will call from the child:   childEvent.emit(data);  

The EventEmitter that you are exposing with @Output is in fact a custom event defined by you.
The event emitter is automatically implementing the Observer patter, it handles a list of subscribers and publish events to them.
With that object you can .subscribe or .emit.

When components are too distant one from the other, there are alternatives to the input and output long chain (see next session)
CSS is enforced to be encapsulated in the component (it is not inherited in the sub components), this tanks to Angular, it is not a default behavior
In browser, it's called VIEW ENCAPSULATION, you can remove this default behavor in the @Component tag add: \`encapsulation: ViewEncapsulation.None\`

When you want to have a personalizble component, you can inject the content from outside: in the target component you put the directive: <ng-content> where you want to add the external content, from the outside, when adding the target component in a template you can add inside any content: <componentName><p>Content Injected</p></componentName>

How to avoid two ways databinding (if the value is used only in the template)?
You can use a reference to a HTML element (it refers to the whole HTML element)
It's created as #name, and can be used only in the template referring just to the name.

We can get the template reference from the .ts file:
**@ViewChild('name', {static: true}) varName: ElementRef;**

In this case you have an ElementRef, to access the underlying element use **varName.nativeElement**
The same change (add { static: true } as a second argument) needs to be applied to ALL usages of @ViewChild() (and also @ContentChild() which is the same of @ViewChild , but for content injected with the ng-content) IF you plan on accessing the selected element inside of ngOnInit().
If you DON'T access the selected element in ngOnInit (but anywhere else in your component), set static: false instead!

In general, from the HTML:

* \[a\] = "variable"   //data flows into the component thanks to the **input binding**  (variable stands for this.variable, where 'this' is the component associated with the HTML fragment)
* (click)="method($event)" //events flows out from your component thanks to the output bindings. ($event here represent the thing that has emitted (in general the HTML element))

LIFECYCLE HOOKS:
ngOnChanges: trigger everytime something changes in the component or in the child component (triggered also at start, it has a booleanFlag: firstChange  (useful))
ngOnInit: execute once right after the constructor
ngAfterViewInit: this is after the ngOnInit, here also the elements of the DOM has been rendered.
ngDoCheck:
ngAfterContentInit: called after the ng-content has been projected in the component (after ngOnInit)
ngOnDestroy: right before object removal
