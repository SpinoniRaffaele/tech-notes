# Dynamic components + overlay component

those are componen created programmatically, they are useful for example to show spinner or errors in an overlay.
(the alternative is the ngIf, it's still considered a dynamic component loading, it's also the easiest)

**OVERLAY COMPONENT**
to create an overlay layer you should block and unfocus the background of the application, you can easily do that putting in your overlay component template the bootstrap class: 'backdrop':  (notice that i also binded the click of the background to some closing action)
```
<div class="backdrop" (click)="onClose()"></div>
//your template code here
```
NB: the template thing you will put in foreground needs a z-index bigger than 1 not to be backdropped.

**PRAGRAMMATICAL CREATION OF COMPONENT**
To create a component programmatically you need to inject the ComponentFactoryResolver.
then you will need to create a ComponentFactory:
```
const cmpFactory = this.componentFactoryResolver.resolveComponentFactpry(YOUR_COMPONENT_CLASS);
```

to instantiate the object you then need a reference to a piece of the DOM, for that you can create a directive that you can attach to the pather template using a div or a ng-template tag.
inside the directive, you need to inject (in the constructor) the **ViewContainerRef**, it needs also to be public.
```
constructor(public ref: ViewContainerRef);
```
Inside the father component you need the @ViewChild tag and the reference:
```
@ViewChild(DIRECTIVE_CLASS_NAME, {static:false}) dynamicComponentReference: DIRECTIVE_CLASS_NAME;

//then in some methods:
//clear the reference and call the createComponent
this.dynamicComponentReference.ref.clear();
const componentRef = this.dynamicComponentReference.ref.createComponent(cmpFactory);
```

How to handle the @Input and @Output of the dynamically generated component?
we can access the instance of the component:
```
componentRef.instance.INPUT_PROPERTY = //something
componentRef.instance.OUTPUT_EVENT_EMITTER.subscribe( //somathing here )
```

How to clear the component?
```
this.dynamicComponentReference.ref.clear();
```
