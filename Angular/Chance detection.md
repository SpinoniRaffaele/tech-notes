# Chance detection

Change detection is a cycle running through the whole component tree, if a component changes, then all the app components are checked from top to bottom.
This is happening fast, but we can optimize it further, if a component only need to change on input value changes, in the component decorator override the changeDetection strategy
```
changeDetection: ChangeDetectionStrategy.OnPush
```

The default strategy is .Default

When using the OnPusb strategy, you will not react to observable new emitted values, to manually react to them, or other things you can programmatically trigger the change detector:
```
Constructor(private changeDetector: ChangeDetectorRef) {}

//in a method you can access
This.changeDetector.markForCheck();


```
