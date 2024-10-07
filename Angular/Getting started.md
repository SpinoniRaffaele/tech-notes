# Getting started

Angular is a javascript framework that allow you to create single page application.
The changes in the web are done by js in a single html page, so the click are responsive and does not need to load other pages from the server.

Use the anguar cli:
Create a new project:  **ng new NAME --no-strict**
This will create all the needed files
Run it locally: from the project root folder: **ng serve**

One of the project files is the package.json where all the dependency are listed.
The actual dependency are in the node\_modules

The attribute \[(ngModule)\] is responsible for doing the magic and binding data to the HTML view updating it automatically,
The binding is done both ways: from model to view and from view to model.

In order to use it in the html you need to import FormsModule in the app.module.ts file

Install the bootstrap css extension (latest):
**npm install --save bootstrap**

Then in the angular.json file (that is a config file) we need to add a string in the styles section:
"node\_modules/bootstrap/dist/css/bootstrap.min.css"  >>done

When you are done with the development you can easily build your app:
Ng build --target=production --base-href '/'        (target Is the deployment modality, base-href is the folder in which you can access the app in the server where it will be served, in this case it is in the root server address, like [senec.com](http://senec.com))    this command is generating the final static in a dist folder

**Vercel** is a tool online that allow you to deploy live a frontend app, it has also a free plan, with limited computing power, but cool
