# Authentication

We cannot use a session in the REST world bcs the requests sent to the serer are stateless, so every request uses a JWT: json web token, which can only be created by the server (validate every requests internally with a private key) at login time.

in the UI you should save the state of the authentication token in a service or a component (it should be an observable), then possibily create a User datamodel class. then the User instance as a BehavioralSubject and on that subject you can emit when the login or logout action are fired (type behavioralSubject because you want to know also the last information (last value emitted) also if you are subscribing late to it).

you should put an httpinterceptor (this is not the security wall, the backend should not retun info to non-auth user, the interceptor is just a UI feature to avoid navigating in useless pages)

The logic of the interceptor is: check if the user is logger (using a service) and if it is the case, modify the request by adding the token to it and handle the modifiedRequest.

How to persist the logged state between page reloads?
you can use either coockies or the browser **localStorage (storage API)** which allows you to store key value pairs

```
constructor(private localStore: LocalService) {}

//...

localStorage.setItem('keyName', JSON.stringify(yourData))
```

then you can retrieve it with:
```
JSON.parse(localStorage.getItem('keyName'))
```
NB:      remember to clear the localStorage after logout and also after the token expiration

You should also add an auth guard to the router, in order to avoid loading routes that are not useful when the user is not logged in. the auth guard **canActivate** method simply use the userService to retrieve if the user is logged and return true or false. Finally add the guards to the routes interested.
