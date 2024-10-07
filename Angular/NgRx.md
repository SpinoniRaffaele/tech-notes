# NgRx

State management solution: the data that controls what should be visible in the view is STATE.

the bigger the app the bigger the state and the harder is the state management.
RxJs is helping with this, providing Observables used to emit new states.
NgRx is too general, it allows for multiple implementation, one of this is **Redux** \-> pattern for state management

**USE NGRX**
install with npm install --save @ngrx/core

you also need to add the StoreModule, configuring it to target the specific reducer
```
imports: [
    StoreModule.forRoot({store1: myReducer})  //assign any key to the specific function name defined in the reducer you      are referring to, the key is going to be needed when injecting the store
]
```

implement a reducer (.reducer.ts file)
```
export const initialState = {
//whatever property you need
}

//here it's convinient to export an interface of the state, to have more maintaineble code
export interface State {
    //whatever property
}

export function myReducer(state = initialState, action: Action) {  //the two arguments will be passed automatically by ngrx
    //switch case over the action types, remember to implement a default case that returns the untouched state.
}
```
It's essential to include the default case returning the state as it is since **the actions will be dispatch in all the reducers**, and if a reducer won't find a match in the action name, then the default will be executed

in the reducer, never  edit directly the state:
```
return {...state, changedField: newValue}
```
the reducer login is synchronous, you cannot put async code there !!! (those code part are best suited for **side effects** see down here)

CREATE AN ACTION
add a new file (.actions.ts file)
```
export const ADD_INGREDIENT_ACTION = 'ADD_INGREDIENT_ACTION'

export class AddIngredient implements Action {
    type: string = ADD_INGREDIENT_ACTION;
    payload: Ingredient;  //coming from your own datamodel
    constructor(paylod: Ingredient) {this.payload = payload}
}  //every other action will be another class with the same structure

export type ShoopingListActions = AddIngredient | DeleteIngredient;  //this is a useful type that represent any action in this reducers (simply a TS trick)
```

In any component you can access the state by injecting the store in the constructor:
```
constructor(private store: Store<{store1: //the type of the state managed by the store1}>)
```
then after injecting you can automatically access the state data:
```
this.store.select('keyName');  which returns an observable
```

in order to dispatch action you still need to inject the store, then:
```
this.store.dispatch(new ACTION_CLASS_NAME(myPayload));
```

When dealing with multiple reducer states
you should create an app.reducer.ts that exports an interface AppState{}  which merges all the other states
```
export interface AppSatate {
    //partial states here
}

export const appReducer: ActionReducerMap<AppState> = {
    state1: reducer1,
    state2: reducer2
}
```
then in the app.module.ts you can import everything with
```
StoreModule.forRoot(appReducer)
```

SIDE EFFECTS
when dealing with async code, you shuld put the async part in a service and only after completion you can call the action in the reducer allowing it to be completely synchronous. (Actions like signUpCompleted, signUpError)

you can also install @ngrx/effects to handle async code in ngrx
use .effects.ts files, inside those files :
```
@Injectable()  //only to be able to use actions and other services
export class MyEffect {
constructor(private actions: Actions)
}
```
here actions is imported from /effects, it is an observable emitting when any action is dispatched, here the idea is to react to actions with the async code, WITHOUT TOUCHING THE STATE (which is handled in the reducer).
Then in order to attach logic to specific action, in this MyEffect class add property:
```
@Effect    //needed by ngrx/effects
loginEffect = this.actions.pipe(  //you cannot subscribe, it's subscribed automatically by effects
    ofType(LOGIN_ACTION),
    switchMap(    //switchMap takes an observable and return another observable,
        (loginActionPayload) => {
            //async code to be executed here (login call to the server)
            return of(LOGIN_COMPLETED_ACTION);  //of because you need to return an observable
        }
    )
)
```
the returned action observable will be dispatched automatically by ngrx/effects

Finally you need to add the effects on the app module:
```
imports: [
EffectsModule.forRoot([MyEffect])
...
]
```

To implement an effect that doesn't need a call to actions:
```
@Effect(displatch: false)  //important set of the dipatch property
effectName = this.actions.pipe(
    ofType(ACTION_NAME),
    tap(  //tap executes without returning new observables
        () => {//logic here, don't call actions}
    )
)
```

you can add multiple effects to the same action, you can also use an efffect in multiple action with ofType(action1, action2, ...)

In order to use the browser extension: redux devTools you will need also to install a dev dependency in your project:
npm install @ngrx/store-devtools
then in your app module you should put:
```
StoreDevToolsModule.instrument({ logOnly: environment.production })  //environment is imported from the environment.ts file a the root of your project
```
