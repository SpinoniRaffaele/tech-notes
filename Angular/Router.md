# Router

Routing tricks the user to think that it is changing page thanks to the change in the URL and in the major content of the page, but the index.html is the same.

Routeing allows to

* Maintain the state of the app ( through refresh, bookmarking, or sharing  urls)
* Protect and separate areas of the app

Notice that in server side, the different routes are going to produce a 404 error, in order to avoid this you should adapt the backend behavior in order access requests from subpaths or to return the index.html content on 404 errors. You can also fix this on the client side using the
angular mode **{useHash: true} (t**o be configured in the forRoot method of the RouterModule). it preappend a # to any route, since the substring after the # is ignored on server side by default.

In order to configure the router you should create a const in the AppModule, it needs to be of type **Routes**

```
Const appRoutes: Routes = [
{ path: 'SUB_URL', component: COMPONENT_TO_BE_RENDERED},    //the sub path should not contain the /   
{ another … }
]  //generally this is placed in a router.config.ts file
```

These
this routes associate a subdomain with the specific component to be rendered
In order for it to work you need to add in the **imports: \[\]** of the appmodule  the **RouterModule.forRoot(appRoutes)**
The routes could be configured to redirect to another route: **{redirectTo: 'anotherUrl'}** , redirectTo is used instead of component .
You should create a 'page not found component' routed with a not-found link, and redirect every not know path to the not-found one.
In order to redirect all the non know path use the wildcard '\*\*', but IMPORTANT, put it at the end of the routes list, since the order matter
The routes are absolute , relatively to the base url which is defined in the index.htmk in the head:
```
<Base href="/">
```

In order to inform angular where to load the component you should put the <router-outlet> directly in an html template

How to trigger the load?
If you use href, you will refresh the app ask to the server the same app in order to show another page (alredy loaded), you will also loose your state
Instead use a special directive:  **routerLink="/SUB\_URL"**   always remember the slash to inform angular that it is an absolute path (relative to the base url)
You can also use the directory notation to navigate in more complex URL and pages:   **../../newsubpath**

Another directive is: **routerLinkActive="CLASS\_NAME"**   is allows to specify a CSS class to an HTML element, the class is appended only if it is active in the router context. The home will be marked in all the time (because any path contains the empty path), in order to avoid this you can use another directive
```
[routerLinkActiveOptions]="{exact: true}"  // so it needs the exact path loaded, to be associated with the class.
```

How to trigger a route navigation from ts?
You need to inject the **Router** from the constructor, then simply call **router.navigate(\['/servers'\])**   //notice that it needs an array
The component doesn't know the route he is into, so you cannot specify simply the relatives URLs, to do so you need to inject the **ActivatedRoute** object
And configure the navigate:
```
router.navigate(['servers'], {relativeTo: this.route})
```

In the routes definition you can put dynamic part of the path, for example use the **path: 'users/:id'**  this allows to bind the general user/some id to the component, in ts in order to get the params from the URL you need to put the **ActivatedRoute** in the constructor as before, then access the dynamic part with
```
activatedRoute.snapshot.params['id']
```

or if you need dynamic data, which changes not only when the component is loaded (rare case), then you can use the Observable of the URL's params:
```
activatedRoute.params.subscribe(params: Params => { //update component data });
```
 ANGULAR IS AUTOMATICALLY REMOVING THE SUBSCRIPTION AT COMPONENT'S REMOVAL
in order to load a routerLink with dynamic params using HTML:
```
[routerLink]="['/users', 10, 'Anna']"  //where 10 and Anna are dynamic params
```

Query Parameters and Fragments
note that query params are used to pass information to the page, while the fragments are in general used to navigate pointing to different elements of the same HTML page, change a fragment doesn't reload the page, but scrolls over the element inserted

in the HTML you have another bindable paramter:
```
[queyParams]="{allowEdit: '1'}"  //allowEdit is the query param name, 1 is the value
fragment="FRAGMENT_VALUE"    //to set up a #fragment in the URL
```
To do the same programmatically in TS:
```
router.navigate(['/servers', 10], {queryParams: {QUERY_PARAM_NAME: 'QUERY_PARAM_VALUE', fragment: 'FRAGMENT_VALUE'});
```

When you are programmatically changing routes, the quey parameters will be lost by default, in order to keep them :
```
router.navigate(['/servers', 10], {queyParamsHandling: 'preserve'};
```
You can also use the string value 'merge' if you are adding new quey Paramus and want to keep both the new and the old.

AUTH GUARD
In order to make some routes accessible only after some logic (for exams retriev if the user is logged in)
You should create a service implementing the **canActivate** method. This method returns a promise, or an observable or directly the boolean, which is the value determining of the route can be activated.
The input parameter for this methods are

* ActivatedRouteSnapshot
* RouterStateSnapshot

inside the method you should perform the reidict back to the safe zone (for example login page) in case the route cannot be activated.

To use this guard use the canActivate inside the appRoute : Routes object.

You can set up child routes, inside a route, use
```
children: [//list of routes]  //the path here is relative to the father URL (not only the base url)
```
you will also need to put the router outplet somewhere inside the father component (where the children is defined)

In order to guard the child routes you need to implement the canActivateChild method (and implements the 'CanActivateChild') that has the same signature of the cenActivate method. Now the route that has the child property can also define a filed:
```
canActivateChild: [//list of guard class implementing the canAuthenticateChild method]
```

We can control also if the user can exit a route with **canDeactivate**
it works in the same way as the canActivate, but it receive more input params:
compoent: TheComponentClass you are into, the standard ActivatedRouteSnapshot, RouterStateSnapshot, and also an optional
```
nexState?: RouterStateSnapshot
```

PASS STATIC DATA THROUGH THE ROUTE
When the route wants to load a components that needs some input data, if can pass it from the reoute definition itself:
```
data: {inputKeyName: 'value'}  //here the value is a string but it can be anything needed
```
then in the interested component to be loaded by this route, you need to inject the **ActivatedRoute** and access the **[route.snapshot.data](http://route.snapshot.data)\['inputKeyName'\]**  and you can assign that data to anything, right on ngInit.

PASS DYNAMIC DATA THROUGH THE ROUTE
for this you need a service that implements the Resolve<any> class, and therefore the method (which is called once, when the route is resolved):
```
resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<any> | Promise<any> | any {
    //any code you need
}
```

to use it you should put the **resolve: {server: ResolverName}** property in the route, with a server property having the 'Service implementing the Resolver' as a value
Then in the loaded component you can access the dynamic data with an ActivatedRoute reference:
```
activatedRoute.data['server']  //notice that the property server cna be renamed with anything you want, it must match however the name in the resolve object in the route object
```
