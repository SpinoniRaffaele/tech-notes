# Angular Universal

It's a way provided by angular to **prerender** pages on the browser.
The angular application is not returned directly to the server, but it returns the bare minium HTML that incorporates the js scritps bundled toghether, then the scripts are executed and they render the actual page content, if you preload the page on the server, it will be directly rendered instantly in the client side.

first run:
```
ng add @nguniversal/express-engine --clientProject NAME_OF_YOUR_PROJECT_FOUND_IN_THE_ANGULAR_JSON_FILE
```

this command do a lot of setup and generates the required files.
in particular it added a script '**build.ssr**' which builds the app using universal.

IMPORTANT: with this setup the application is no more static, it is a node.js backend application that needs to be server directly in some non-static server

When working with this server modality we cannot anymore access the some browser features like the browser localStorage or the localization object.
In order to use these feature we need to tell the code to wait until it is running in the browser and it is not i the server side env). in order to do this:
```
//inject in the constructor the global variable:
constructor( @Inject(PLATFORM_ID) private platformId )

//then in the place where we use browser stuffs:
if (isPlatformBrowser(this.platformId)) { //isPlatformBrowser is imported from angular common
    //here logic related to browser stuff
}
```

now run **'npm run build.ssr**'. it build the node.js app (now you should deploy somewhere)
than run **npm run serve:ssr**     it runs the application in the node.js environment

Nest.js is a server-side framework built on top of node.js (it takes lot of ideas from Angular)
there is a way of getting angular universal in nest: using this command: **@nestjs/ng-universal**
this will run in a nest.js server (what is cool about this is that you can also write the API implementation in nest, having there all the app stack).  you will need the same two commands as before: **npm run build:ssr** and **npm run serve:ssr**
