# Forms in Angular

Forms are meant to modify data both in the page and in the server, changes about a form input could need to be reflected elsewhere, there is the need of a validation.
Angular has FORMCONTROL and FORMGROUP which are great tools.

There are two approaches:

* template-driven: angular infers the the form from the DOM
* Reactive: you need to create programmatically the form ( form group) and then bind it to the DOM

The form control is a single field or input space, it encapsulate the value, being dirty, or if it has errors
Properrty of the Object: .errors, .dirty, .valid, .value, in the HTML you bind the form control with the attriubute \[formControl\]="name" over the html element.
FROM GROUP is a group of controls:

Let a = new FormGroup({firstName: new FormControl("name"), lastName: new FormControl(surname), …})
Also here we have the same properties (dirty, value, errors, valid)

BUILD FORM USING NGFORM -- TEMPLATE DRIVEN -- NOT SO GOOD
you need to add the FromModule in the module.ts file

to work in this way you need to add the 'ngModel' (just 'ngModel', not the \[(ngModel)\] of the two way binding) directive to the HTML input that you want to be binded to some controls, also add the name attribute with an actual name value, you should also bind the submit at the form object using the directive:
```
(ngSubmit)="Method(formRef)"  #formRef="ngForm"     
```
as you see the formRef reference is passed to the method script (as of type NgForm) with the **\="ngForm"** sintax
inside of it you have everything, with this approach you can directly put validators into the DOM, to the input elements, such as 'required' or 'email'
(the state of the form is tracked internally by angular using some special classes attached to the input elements)
you can also bind property of the HTML elements using the #formRef reference, for example disable the submit button:
```
[disabled]="!ngForm.valid"
```
you can also wrap a portion of the from in a div and add the ngModelGroup=NAME directive to the div. Thanks to this you will be able to access the NAME key inside the form object programmatically, it will contain the values of all the controls inside.

in the form you can reset the state simply calling  .reset() in the formObject

BUILD FORM WITH FORM BUILDER  More flexible
you need to add the ReactiveFromModule in the module.ts file

You'll need to define a FormGroup (urlFom in the code) in the component, and also inject a FormBuilder  (fb In the next code) in its constructor.
Then in the ngOnInit you can create your form with a configuration object:
    this.urlForm = [this.fb.group](http://this.fb.group)({
      url: new FormControl('',
        \[
          Validators.required,
          this.urlValidator
        \]
      ),
      provider: new FormControl('', Validators.required),   //for each field you specify the initial state (empty: '', in this case), and also an optional list of validators
      comment: new FormControl('', Validators.required),
      sandboxingComment: new FormControl({value: '', disabled: true}, Validators.required)
    });

inside the FormGroup you can have a field which is another formGroup nested inside!, (in the HTML wrpper of the child form you should put the directive of formGroupName="//the key name in the father form group in the component ts file")

then you will access it using a path notation in the get:
```
form.get('fatherKey.childKey')
```
You can also use a FormArray, an array of FormControl, useful for dynamically added input fields, you access it with a casting:
```
(<FormArray>)this.form.get('arrayKey').push(new FormControl(...))  //here i'm also adding a new formControl
```
 in the tamplete you bind it using the directive: **formArrayName="arrayKey"**

In the HTML, in the **form** tag simply put the directive telling it to use your form: \[formGroup\]="urlForm"
Then we need to bind all the HTML elements of the form to their formControl: example <input \[formControl\]="urlForm.controls\['url'\]"  //url is the name of the formControl
you may need to cast $any(ngControl.control), or to set the restrictTemplates to **false** in the tsconfig.
you can also bind it using the formControlName="name" (no need to cast)

In both the component and the HTLM we can check that the form is valid with: **urlForm.valid**, for example a typical UI behavior is to make the input field react to its validity: **<input .…   \[class.error\]="!urlForm.get('url).valid && urlForm.get('url').touched" >**     //assuming we have a CSS class 'error' to adapt the graphic.

You can also react to specific validators: **urlForm.get('url').hasError('required')**
**You can also write your custom validator and use you rlogic, but the function needs to take as input the FormControl and return a map from string to boolean:  return !value.match(regex) ? { 'invalid pattern': true } : null;**  //where null means everything ok, otherwhis we pass true with the error

You can also subscribe to value change in the formControl:  **urlForm.get('url').valueChanges.subscribe(… whatever)**  you can also do the same on the whole formGroup
you can change the value of one component with
```
urlForm.patchValue()
```

Implement your own validator:
it is a method returning an object like this: {\[s: string\]: any}, having the FormControl as input,
the method implements the logic and if the input is fine it need to return null, otherwise the object wih something useful.
NB: beware the usage of 'this' because it will be used in a context outside the ComponentClass
in the HTML you can  check for specific errors:
```
form.get('controlName').errors['errorName']
```
The validator could also return a promise/observable (think about some backend checks), the return type will be then
**Promise<any> | Observable<any>**
then you will add it to the formControl like the other validators, but as a third argument of the FormControl constructor.
