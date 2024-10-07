# Angular Offline

What is a service worker? JS run in a single thread normally (also the async stuff is handled in the same thread)
A service worker is another JS thread completely detached from the HTML page, but it can manage the pages of a given scope (it can catch network requests, like a proxy).

In angular:
```
ng add @angular/pwa
```
this allows to run Angular App offline (it automatically create and edit the necessary files)

now you can build the app with the classic **ng build** .
to launch it you need a lightweight server such as **http-server**  this will work also without network
obviously the API data will not be there anymore.
But you can cache last API calls results in order to server them, this is configured in the **ngsw-config.json** file
configuration specific to the angular service worker.
