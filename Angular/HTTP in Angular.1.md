# HTTP in Angular

Angular has his own HTTP library, which are asynchronous.
Dealing with asynchronous code in JS has been a pain:

* callbacks
* Promises
* Observables (the best as of now)

To use Http, import the HttpModule in your list of imports in the appModule.
You can inject the http service in any component with a constructor parameter of type **Http**.
Example of request:

This.loading = true;
This.http.request('URL').subscribe(res: Response => {  //toice that 'request' is generating a GET
		[This.data](http://This.data) = res.json();
		This.loading = false;
}, error => {[handle](file://handle) errors in some way});

In a search bar, that calls an API, not every keyup event is goin to trigger a call,
In order to implement a filtering, you can use the Rx.Observable.fromEvent(this.el.nativeElement, 'keyup')  /el is of type ElementRef
 and then     **.map((e any) => [e.target.value](http://e.target.value)).debounceTime(250).do(() => this.callApi)**   //debounce time is the milliseconds that you need to wait without typying to trigger a new API call, the do method is simply executing aytime the conditions are met.

In order to make POST requests:
```
https.post(URL, JSON.stringify(BODY)).subscribe((res: Response) => {
//action with the response
})
```
then we have **put, patch, delete, head** (where also put and patch takes a body)

All of these methods takes also an optional last parameter: **RequestOptions** where you can define things like the headers:
```
options.headers(new Header(['X_API_TOKEN', 'TOKEN_VALUE']))
```
