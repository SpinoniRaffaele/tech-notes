# Redux

For small apps the state can easily be tracked by services and then rendered in component.
When the state became big, redux is perfect.
Redux is a data-architecture pattern, it consist of storing all your state in a centralized place.
Popular implementation of it:

* Rxjs/store
* Angular2-redux

Key idea of the pattern:

* All the state in a single place called _store_
* Your components read pieces of the state from the store directly
* The store is immutable directly
* Components change the state by using actions that (calling the reducer which return a new state) creates an entirely new state.

**MINIMAL IMPLEMENTATION OF THE REDUCER**
The reducer file itself is the place where the old state and the action are combined to form the new state. It's a pure function (or a set of pure function)

```
Interface Action {
Type: string,
Payload?: Any
}

Interface Reducer<T>{
(State: T, action: Action): T
}
```
Generally the reducer body is a giant switch that switches over the action types and executed different things
```
Class store<T>{
Private state: T;

Constructor(private reducer: reducer<T>, initialState: T){
This.state = initialState;
}

GetState(){
Return This.state;
}

Dispatch(action: Action){
This.state = this.reducer(this.state, action);
}

}


```
Then you can use the store from your components and interact with it using the dispatch method, or retrieve the state with the getter.
Once the state changes we would like to react to changes in our component automatically, so we set up listeners that are functions to be executed on state change.

Add to the store:
```
Private listeners = [];

Subscribe (listener){
  This.listeners.push(listener);
  Return () => this.listeners =      this.listeners.filter(lis => lis !== Listener);
}

//Into the dispatch method
This.listeners.forEach(listener => listener())
```
The subscribe add the new listener and return the unsubscribe callback. Then the dispatch method calls them after executing the action.

Then you can use the subscribe method like this:
```
Let unsubscribeCb = store.subscribe(()=> {
  // Logic to be executed on state change here
}

// Call dispatch and update state

unsubscribe();


```
