Each page in the app is called activity, it all starts from the MainActivity. The parameter sent to the application at startup can choose the landing activity (which is the main one by default).
Each component in the activity is called a View. Each view is inside some layout component, the layouts are boxes for views.

In java you can check the ID with `R.id.ID_NAME`

you can assign an eventHandler method to a view in the design file, then you have to create a method that runs when the event gets triggered (it has the view that triggers the event as input):
```
public void handler(View v) {
	//code here
}
```