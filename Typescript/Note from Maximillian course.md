# Note from Maximillian course

it adds static typing to JS
to install it: **npm install typescript**

the primitives of typescript are number, string, boolean, null, undefined

you can assign the general object type like this: **let a: {};**
typescript automatically infers the types if you initialize the variables

you can define a type with
```
type Person = { name: string, age: number }
```
Generics
by using those you can use type inference also on generic functions, without rolling back to using any everywhere
```
function prepend<T>(arrays: T[], prefix: T): T[] {...}
```
