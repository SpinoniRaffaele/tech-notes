# Testing

typical before each:
```
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ HomePageContentComponent ],
      providers: [MediaService]
    })
    .compileComponents();

    fixture = TestBed.createComponent(HomePageContentComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });
```
TestBed is the bigger object to use in the test you need to provide the initial configuration giving the components and services used, then you need to call detectChanges in order to detect rendered infos in the webpage. otherwise the test will not consider it.
In order to inject a specific instance of a service, you can use the following:
```
let userService = fixture.debugElement.injector.get(UserService);
fixture.detectChanges()  //it's important to detect changes again here
spyOn(userService, 'getData').and.returnValue('mockData')  //here is how you mock it


```
if you need to run async code put async to the it function callback:
```
it('should go', async() => {
    //test here
    fixture.whenStable().then(() => {
        //expect over the async code resolution    
    })
});

another alternative is to use fake async/tick:

it('should go', fakeAsync() => {
    //test here
    tick();  //make the async code end

    //expect over the async code resolution
});
```

In general pipe and services can be tested in isolation, you won't genreally need the angular testing capability but a basic JS method testing, so no need for TestBed, for the fixture and all of that.
