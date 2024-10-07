# HTTP in Angular

Angular has his own HTTP library, which are asynchronous.
Dealing with asynchronous code in JS has been a pain:

* callbacks
* Promises
* Observables (the best as of now)

the http request is made of:

* URL: the API endpoint
* VERB: POST/GET/PUT/DELETE
* Headers: optional metadata
* Body: core data (available on POST, PUT, PATCH)

To use Http, import the HttpClientModule in your list of imports in the appModule.
You can inject the http service in any component with a constructor parameter of type **HttpClient**.
the httpClient object has the methods with the same name as the HTTP verbs.
It's always good to wrap the calls into services in order to have single API to change in case you change the backend.

(he uses a firebase realtime DB and using the link provided in the firebase page as API endpoint, to consume the API you need to append .json to the end)

the get request is a generic method so you can type the respose that you expect: **httpClient.get<MyDataType>('url')**

Example of request:

```
This.loading = true;
This.http.request('URL').subscribe(res: Response => {  //toice that 'request' is generating a GET
This.data = res.json();
This.loading = false;
}, error => {\\handle errors in some way});
```

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
**important: if you don't subscribe to the response angular won't send the call.**

All of these methods takes also an optional last parameter: **RequestOptions** where you can define things like the headers:
```
options.headers(new HttpHeaders({'X_API_TOKEN', 'TOKEN_VALUE'}))
```

also query params
```
this.http.get(url, {headers:..., params: new HttpParams().set('name':'value')})
```

you can configure a request in order to return all the elements (response headers, stats, url, and all that stuff)
add an optional parameter to the post/get/..
```
http.post(url, {observe: 'response'})  //by default it's body
```
you can also use the responseType: text in orded to get freetext, beware that then you cannot treat it as a JS object

CATCHeRROR

you can use the rxjs throwError t create an observable that wraps an error.
after calling the http request, inside you can use catchErrors and use the error observable:
imagine for example you need to send the error to some analytics service.
```
http.get(...).pipe(catchError(error => return throwError(error)));
```

INTERCEPTORS
they are useful when you need to customize all the requests, for example for auth purposes

to create one you need to create a file (.service.ts) with a  class that implements the **HttpsInterceptor** interface and it needs to implement the **intercept** method that runs right before our requests get fired:
```
intercept(req: HttpRequest<any>, next: HttpHandler) {
    //code here
    next.handle(req);  //if you miss this line the requests won't be fired
}
```
you provide this service in the ngModule in this way:
```
providers: [{provide: HTTP_INTERCEPTORS, useClass: YourInterceptorClass, multi: true}]  //multi is needed only if you have more services doing this thing
```

example of modified header request, notice the use of **clone** (logic that stands inside the intercept method):
```
const modifiedRequest = req.clone({headers: req.headers.append('new_header', 'value')})
return next.handle(modifiedRequest);
```

example of reacting to the response (inside the intercept):
```
return next.handle(req).pipe(map(event => {
    //here you have access to the response (into event object) and you can modify it before it arrives
}))
```
generally you should use **tap (**from rxjs/observable) that allow you to work with the event returned, but at the end it returns the original value
