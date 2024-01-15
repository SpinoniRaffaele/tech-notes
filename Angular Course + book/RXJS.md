# RXJS

Observable is the implementation of the observable pattern, given by rxjs library.

Using observable to structure our data is called **Reactive programming**
they can interact with different data sources: events, http requests, triggers, all of them ASYNCHRONOUS
It is an alternative to the native JS Promises and it is also using a clearer syntax.
Observable emits three things:

* data
* error
* completion![[./_resources/RXJS.resources/image.png]]

RxJs expose a method:
```
interval(timeIntervalMilliSecond).subscribe(......  )  //it fires a counter any speficied time interval
```

you have to be careful and delete those things correctly, for this you should store the subscriptions in a component's attribute and remember to clean it onDestroy. **Only the observable provided by angular are cleaned automatically.**

A nice way to work with stream of data, is using a Subject, with an implementation that is BehavioralSubject (always store the last value), subject pushes a new value on the stream using .next(). And you can .subscribe() over it, as it extends Observable

Build your own observable:
```
const customObs = Observable.create( observer => {});
```
inside the arrow function you can implement the logic you want and also call the three method over observer:
(observer defines an interface of an object wih the three function defined: next, error, complete)

* next()   to push new data
* error()  to throw an error  observer.error(new Error('ErrorMessage') )  this error will be taken as a second argument to the subscribe inner function
* complete()   complete is catched in the third argument of subscribe, which is also a function.

**NB an error thrown in the observable will destroy it, so no complete will be reached.**

**OPERATORS**
change the data returned by the subcription, aslo work with multiple subscription (like merging for example)
ex:
```
observable.pipe(map(data => { //transform it and return})).subscribe(....)
```
pipe is used to add operator in the flow between the emit and the subscribe.

**throttleTime**(1000)   defines a timewindow of 1000 millisecond, inside the time window all the events emitted are ignored except the first (it generates a time window of non reactivity)

**Filter** takes two argument: a function (returning true for allowed values, false for filtered out values), and a reference to a variable, which will be considered as the 'this' inside the function context

**debounceTime**  //same as throttleTime, the only difference is that here a new value will be emitted if nothing happens for the timewindow specificed, while in the throttle case, any x millis a new value will be emitted.  (this is the one to use when sending HTTP request based on an input value).

**distinctUntilChanged**()  , no parameters, it is used to filter out values that are the same as before, NB this works on the whole input, so be sure to map to the part interested before calling this.

**reduce**() works on a sequence of emitted values like the reduce does on the items of an array, it takes two input: a function of type:
(cumulative, current) => {}    and a value which is the initial value for the cumulative. (the cumuative could be the sum and the initial value 0), reduce returns only at the completion of the observable.

**scan**() is the same of reduce but it emits values with the partial results at every new value met, so it doesn't need the observable to complete.

**mergeMap**() , it takes an outer observable and merge it with an inner one, you should store before the two observables:
obs1, obs2
```
obs1.mergeMap( valOfFirstObs => { //any logic that works on the obs2....  using also the valOfFirstObs} ).subscribe()
```
in the subscribe you will get the combined value returned by the mergeMap

**switchMap()**  works also with two observables, say obs1, obs2
```
obs1.switchMap(
    event => { return obs2 }
)
```
this will trigger any  time a value is emitted in the obs1, but it will return the obs2, it switches observables

**Subject**
it's similar to eventEmitter, you don't call emit but **next()** it is an object you can subscribe to, but you can also call next on it **from the outside** of its definition**.  they are also faster than observables.**
Usage: into a service, to let different components emit and subscribe.
```
subect = new Subject();
```

**BehaviorSubject**
it's the same as the subject but it can take as input in the constructor which is a starting default value. (it's a first value emitted in initialization)
