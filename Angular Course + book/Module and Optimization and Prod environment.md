# Module and Optimization and Prod environment

Angular doesn't scan the files in the folder, it uses the modules to have the list of stuff: components, directives, pipes, services
You may need to split the app into different modules (maybe to load them at different times)
In particular, the split of the modules should be done by the FEATURES of the application. You will generally have a **father** module and a **shared** module with shared stuff and then some **features** modules.

The imported stuff is not inherited in feature modules, you need to import it too to the other modules where stuff is used (the **services** instead are inherited)
EXCEPTION:
The **BrowserModule** needs to be imported only in the AppModule file, if you need its features (ngIf, ngFor) in other modules you will need to import the **CommonModule**

**IMPORTANT:** you cannot declare the same component or directive or pipe in multiple modules, you should export and then import between modules if you need to access the same thing in multiple modules.

**LAZY LOADING**
this concept requires multimodule application, it requires also that each module ships with its own routes.
It will bundles the app in different parts, so you reduce the starting waiting time

in the app routing module: in the routes, you can configure the lazy loading:
**{path: 'child module path', loadChildren: 'path to the child module file#module name' }**,     example:
```
{path: 'recipes', loadChildren: './recipes/recipes.module#RecipesModule' }  //without .ts

//more modern syntax
{path: 'recipes', loadChildren: () => import('./recipes/recipes.module').then(m => m.RecipesModule)}
```
all the bundling is then done automatically by the Angular CLI. IMPORTANT: the CLI looks at the imports so if you let useless imports
IMPORTANT: lady loaded modules should not be imported in parents modules, they should just be present in the routes

**PRELOADING LAZY LOADING**
this is another advancement, you can preload all modules in order to have them already locally when the route of the lazy loading is visited:
the page is loaded after the first bundle, but after the first loading the page is responsive and it is downloading the other modules in the background.
in the app routing module:
```
RouterModule.forRoot(appRoutes, {preloadingStrategy: PreloadAllModules})
```

**SERVICES:**
they are all singleton instances shared across all the modules children of the module where they are defined, if defined in a **children eager-loaded module**, it will be available in the father one (and application wide). if defined in a **children lazy-loaded module**, it will not be available in the father one.
In general you should put services in the root, either with providedIn: root, or declaring them in the AppModule.

**PROD ENVIRONMENT**
you have the environment folder in the angular project, where you have two file: one for dev and one for prod where you can define some key value pairs (and the value can be different between environments), import the values fomr the dev environment then angular will automatically use the right env (example when building with **ng build** it will use the prod env variables)
