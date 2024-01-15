# Animation

to set the project to use animations you need to install **@angular/animations**
and add the BrowserAnimatiosModule to the ap.module imports.

Animations works with states, they transit from one state to another, they are defined at component level for things inside the component

In the component tag you can add animations:
```
@Component({
    animations:  [
        trigger('identifierInTheTemplate', [
            state('state1Name', style({
                'background-color':'red',
                //any other CSS styling you need, probably a    transform: 'scale(1.5)'
            })),
            state('state2Name': style({//any CSS})),
            transition('state1Name => state2Name', animate(//insert timing here such as 300)) 
            //you can also use the <=> arrow for transition in both the directions between the state
        ])
    ]
})

//then in the component:
state = 'normal'  //define the variable enclosing the animation state

onAnimate() {
    //put here the logic to change state, NB THE NAME OF THE METHOD IS UP TO YOU
}
```
In the template, you can bind the animation to a tag:
```
<div [@identifierInTheTemplate]="state"
    (@identifierInTheTemplate.start)="onAnimationStart()"  //this is the callback for the starting animation
    (@identifierInTheTemplate.done)="onAnimationEnd()"  //this is the callback for the end of the animation
></div>
```

Some advanced transitions:
you can transit from a state to any other with: **transition('state1 => \*')** using the wildcard
In order to transit with a third state in the middle:
```
transition('a => b', [
    animate(500, style({//the style you want in between})), //you can put infinite steps of this
    animate(500)  //this is the animation leading to the final state
])
```

You can transit between piece that are appearing and disappearing in the DOM:
appearing
```
trigger('div', [
    state('in', style({//whatever}))  //notice that 'in' can be anything
    transition('void => *', [style({//the state in which the object start the appearence}), animate(300)]) 
    //the 'void' is reserved in angular for piece that are not attached to the DOM yet
])
```

disappearing
```
trigger('div', [
    state('in', style({//whatever}))
    transition('* => void', animate(300, style({//the final state at disappearence}))) 
])
```

instead of animate you can use **group()**  to group an array of animation and start them synchronously at the same time
