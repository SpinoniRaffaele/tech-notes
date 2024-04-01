# note on online book

Typescript is built upon javascript, it uses a compile time type check.
It is a superset of javascript, it is compiled by the Angular CLI.
To install it you use **npm install -g typescript**
To run it you use it as a compilator which outputs a js code. **Tsc FILENME**
With the **--noEmitOnErro**r option you'll get a compilation that doesn't modify your js code.
You need to type things like **name:type**
Then the js code will not contain them.

The **strict: true** is a command that tells typescript to be as strict as possible with the rules.

Typescript has the **any** type to create a variable where you can perform any operation that is syntactically correct,
basically you are telling the compiler to avoid checking that variable.
You can assign a type to every declaration of variable, if you don't want to, you can let typescript infer it for you.
The same for function parameters (both input and output) .   
If typescript is not able to infer the type it will use the **any** type.
Also with objects you can specify the type: **obj: {x:number; y: number}.**
COMPLEX:
Union |   -> **id: number | string**
You can do only things that are valid both for number and string types.
To apply more specific operations you need to check the type with **typeof** and then perform the specific op.
or **isArray** if you want to isolate the array type.
YOU CAN CREATE TYPE ALIAS:
**type Point = {**
  **x: number;**
  **y: number;**
**};**
In this way you define a structure, or maybe **type ID = number | string;**
INTERAFCES:
**interface Point {**
  **x: number;**
  **y: number;**
**}**
This works as type alias but when you need to use a type, you don't need to explicitly state it,
 it just matter the structure of the variable: **printCoord({ x: 100, y: 100 });** print coord need a Point type but there is no need to state it.
Extend an interface:
**interface Bear extends Animal {**
  **honey: boolean**
**}**
Extend a type:
**type Bear = Animal & {**
  **honey: boolean**
**}**
TYPE ASSERTION
If you know that a certain type that the compiler cannot know you just tell him to be more restrictive with the 'as':
**const myCanvas = document.getElementById("main\_canvas") as HTMLCanvasElement;**
TypeScript only allows type assertions which convert to a more specific or less specific version of a type.
YOU can also create a type by using specific values: ex. Enum **alignment: "left" | "right" | "center"**
You can also combine the standard types with more specific vales.
Typescript uses two special types: null, undefined.
Both can be checked with  **if (x === null) {**
To be shorter typescript has a specific operator that checks if the types are not null and undefined: just put **!** after the var name.
The special type object refers to any value that isn’t a primitive (string, number, bigint, boolean, symbol, null, or undefined),
functions are objects.

NB: in javascript we can use if (typeof … ="object" ) to see if the var is an array but also NULL is considere an OBJECT !!!
To prevent against the null use the coercion mechanism that maps null and undefined to FALSE by doing **if(variable)**
By doing this we avoid null values.
Another way to narrow types is to use **if("value" in X)** where x is a variable and value is a field or a method of that variable
Another one is the use of **instanceof** to check membership to a class.
When narrowing you can reach the base level of no values inside the type, this Is called **never** by typescript

FUNCTIONS
In typescript, function can take as input other functions:
**function greeter(fn: (a: string) => void) {**
  **fn("Hello, World");  //here the function is called**
**}**
You can also have functions with attributes, here the syntax is slightly different:
**type DescribableFunction = {**
  **description: string;**
  **(someArg: number): boolean;**
**};**
GENERICS:
**function firstElement<Type>(arr: Type\[\]): Type | undefined {**
  **return arr\[0\];**
**}**
This implementation works with every type of values inside the array as long as it is homogeneous.
It also createsa link between the input and the output types
In general two types that are with the same name needs to be the same type. It is possible to override this by explicitly tell types:
**const arr = combine<string | number>(\[1, 2, 3\], \["hello"\]);**   (where combine was defined with <Type>
FUNCTION OVERLOADING
You can call a function In different way, to allow it just put more signature on top of it when you declare it
**function makeDate(timestamp: number): Date;**
**function makeDate(m: number, d: number, y: number): Date;**
**function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {**

WARNING: when doing this, the last signature used to define the function will not be considered for types.
WARNING: typescript can solve only one overloading at a time so you cannot pass a **type1 | type2** to a function that has been overloaded for
Type1 and type2.

Some function returns the NEVER type: it means that the function is used only to throw exceptions or end the program.
In function type definition, when a function return void, you can still return values in the function definitions, but they will be ignored.
**type voidFunc = () => void;**
The normal definition instead is working as expected and no type can be returned.
**function f2(): void {**
  **// @ts-expect-error**
  **return true;**
**}**

  The for..in loop returns a list of indexes on the object being iterated, whereas the for..of loop returns a list of values of the object being iterated.

The map in typescript are ordered, they provide the usual interface, plus:
Keys() return the iterable list of keys,
Values() //obvious
Entries() return a list of couples, every couple being the key value pair

There are also optional and default value parameters, which are equals to python

There Is also the Set (identical to the python one)

Private, protected(access also to the subclasses), public  | by default it's public

Rest Parameter
The rest parameter is used to pass zero or more values to a function. We can declare it by prefixing the three "dot" characters ('...') before the parameter. It allows the functions to have a different number of arguments without using the arguments object.
Ex:
```typescript
function sum(a: number, ...b: number\[\]): number { 
  let result = a; 
  for (var i = 0; i < b.length; i++) { 
  result += b\[i\]; 
  } 
  return result; 
} 
```

Instantiate an object:  `let object\_name = new class\_name(parameter) `

Inheritance is identical to Java, there is the super() method, to call the superclass constructor.

Interface:
It can contain fields and method declaration (ex:     GetAge();  ). It's basically used to check that the object has the intended structure

We can use namespace to isolate a group of names (of variables or funxtion or class)
namespace studentCalc{ 
    export function AnualFeeCalc(feeAmount: number, term: number){ 
    return feeAmount \* term; 
    } 
} 
Then we can import the namespace from other locations:  /// < reference path = "Namespace\_FileName.ts" /> 

When you export an interface or a class, you are creating a module that is axcessible from the outside (using import statemets)

Generics:
function identity<T>(arg: T): T {   
    return arg;   
}   

The type check in Typescript is strange: called DUCK-TYPING: if two object have all and only the same properties then the two types are interchangeable, for the compiler they are the same thing.

$ tsc --init                   create a tsconfig.json file with basic configuration
