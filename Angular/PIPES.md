# PIPES

They transform output as functions, used in the template, they do not change the values themselves

syntax: {{stuff | pipe }}, you can put this everywhere (ngIf ngFor,...)

some already defined pipes: uppercase,  date (automatically beatify the js date type)
Pipes are configurable:
```
this.date | date: 'fullDate': otherParams
```
And we can also combine more of them:
```
variable | pipe1 | pipe2  //it's like  pipe2(pipe1(variable))
```

**Create your own pipe:**
create a .pipe file, define a class implementing the PipeTransform, it needs to imlement a transform method
```
@Pipe({{
    name: 'nameInTheHTML'
})
export class Pipe implement PipeTransform{
    transform(value: any) {
        return //logic
    }
}
```
remember to add the pipe to the declaration of the module

you can also add input param to the pipe, (after the value one), then you will pass the arguments in the template.

NB: angular is not running again the pipe when the data is changing (with not primitive data).
you can force it to work: **it is generally a performance issue**
```
@Pipe({
    name: 'pipe',
    pure: false
})
```

**async** pipe: used when dealing with async data, if you were to show any async data (Observables, Promises) in the template, you will see only the \[Object\] string that never changes. Use the async pipe to let angular know that it needs to check the Object for value changes and react in the template.
