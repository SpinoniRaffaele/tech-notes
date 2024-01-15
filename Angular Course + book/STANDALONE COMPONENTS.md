# STANDALONE COMPONENTS

those components are a novelty of angular v14, still unstable and not suited for production environment, they let you build components without the need of NgModules.
The problem rises from the boilerplate code that you need to write in the module files.
Standalone components and directives and pipes doesn't require the use of NgModule
you do everything in the @Component or @Directive or @Pipe tags:
```
@Component({
    standalone: true,  //remove the component from the ngModule since it's standalone now
    imports: [ImageGridComponent],  //list here all the dependencies: components, directives and pipes
    selector: 'good-component',
    templateUrl: 'my.component.html'
})
```
if you need to import stuff declare in an NgModule, you can add the module class to the import list
Then you can import standalone components in other modules as any other module in the import array of the module itself.

To boostrap you edit the main.ts file importing the angular method **bootstrapApplication (remove the bootstrapModule part)**

there you define the entry point component and the provided services that you can declare singularly or import them from a module
```
bootstrapApplication(PhotoAppComponent, {
  providers: [
    {provide: BACKEND_URL, useValue: 'https://photoapp.looknongmodules.com/api'},
    importProvidersFrom(
      RouterModule.forRoot([/* app routes */]),
    ),
    // ...
  ]
});
```

If you are writing a library without entry point, you should wrap everything in a module and expose the single standalone modules
```
@NgModule({
  imports: [ImageCarouselComponent, ImageSlideComponent],
  exports: [ImageCarouselComponent, ImageSlideComponent],
})
export class CarouselModule {}
```

Routes?
in the app  standalone component (the route you should import the RouterModule)
then to make the angular application aware of the routes (defined in a module) you should a in the main.ts
```
importPorvidersFrom(AppRoutingModule) //the name of the module with the declared routes
```
the lazy loading will be automatically working if you configured it in the routes ONLY FOR THE COMPONENT IN  A MODULE.
In order to lazy load a standalone component in a route you will need to:
configure the route like this:
```
{path: 'URL_PATH', loadComponent: () => {import(COMPONENT_PATH_IN_THE_DIRS).then(m => m.COMPONENT_CLASS_NAME)}}
```

here you see the actual advantage of standalone: load every component lazily as you want
