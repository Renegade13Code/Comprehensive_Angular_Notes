## Angular API: https://angular.io/api
TS note: `static` keyword placed on a method of a class means we can call that method without having to instantiate that class  

# Section 1: Getting Started
## Installing nodejs:
run:
```
$ curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo bash -
$ sudo apt-get install -y nodejs
```

## Creating new angular app:
```
$ ng new app_name --no-strict
```

## To run in browser
```
$ ng serve
```

## To install bootstrap locally in project:
```
$ npm install --save bootstrap@5  #5 for current version
```
* Then need to add "node_modules/bootstrap/dist/css/bootstrap.min.css" to angular.json file under "projects.my-first-app.architect.build.syles" 

# Section 2
## The Basics
## To generate new component using CLI:
```
$ ng generate component component_name
```
or 
```
$ ng g c component_name
```

## Databinding:
* Dynamically injecting data into a template can be done using string interpolation or property binding.
* String interpolation: component class properties(variables) can be inserted into template, and class methods can also be called from templates as follows:
```
<p>The {{'server'}} with id: {{serverId}}, is currently {{getServerStatus()}}</p>
```

* Property binding: component properties(variables) can be linked to certain html tag properties as follows:
```
<button class="btn btn-primary" [disabled] = "!allowAddServer">Add Server</button> 
```
* Where allowAddServer is defined as a property of the component class and its value can be changed dynamically within the component.ts file.

## Eventbinding: 
* Is done by linking component class methods to HTML element events within the template file, as so:
```
<input type="text" class="form-control" (input)="OnUpdateServerName($event)">
```
* Eventbinding is done using (), the `$event` keyword passes the HTML output of the event (typically an object, in this case of type 'Event') as an argument to component class method `OnUpdateServerName`. Note that the `$event` keyword can be used anywhere within the "" assignment i.e. it can be assigned to a property using: "classProperty = $event". 
* console.log() the HTML event output to view its object structure and value.
* Note that `(input)` in the above example is a HTML component event. https://developer.mozilla.org/en-US/docs/Web/API/Element#mouse_events

## Two-way databinding: 
* Used to grab the output of a HTML element and use it to update the value of a class component property simultaneously. Note that both the component property and the DOM object value are both updated, and hence the name 'Two-way databinding'. Done as such: 
```
<input type="text" class="form-control" [(ngModel)]="serverName">
```
* Must include foollowing code in app.modules.ts:
import { FormsModule } from '@angular/forms';
```
...
imports: [
    ...,
    FormsModule,
    ...
  ]
...
```

## Directives: 
* They are instructions that manipulate the DOM. Components are essentially directives with templates. You can create your own directives or use the built in ones. They are implemented as attributes of HTML elements. 
## ngIf directive: 
* Note that the * operator is used to prepend structural directives, that is, directives that alter the structure of the DOM. used as such:
```
<p *ngIf="serverCreated">{{newServerStatus}}</p>
```
* Where ngIf is a built in directive linked to a boolean variable, the <p> element is shown if the variables value is true.

* ngIf directive with else statement:
```
<p *ngIf="serverCreated">{{newServerStatus}}</p>
<p *ngIf="serverCreated; else noServer ">New server was created with server name:{{serverName}}</p>
<ng-template #noServer>
    <p>No server was created</p>
</ng-template>
```
## ngStyle directive: 
* Styles HTML elements dynamically.
* It is an attribute directive-does not add or remove HTML elements only changes the HTML element they are placed on. Used as such:
```
<p [ngStyle]="{backgroundColor: getColor()}">The {{'server'}} with id: {{serverId}}, is currently {{getServerStatus()}}</p>
```
* Note that the [] operator is not part of directives but is in fact property binding. The ngStyle property expects a javaScript object(key-value pair). In this case we pass it the key backgroundColor with a value returned by the getColor method (returns a string representation of the color i.e. 'red'). Note that you could also have written it as: [ngStyle]="{'background-color': getColor()}", which corresponds more with CSS.

## ngClass: 
* dynamically assign classes to HTML elements
* Also a attribute directive. Used as such:
```
<p [ngClass]="{online : (getServerStatus() === 'online')}">The {{'server'}} with id: {{serverId}}, is currently {{getServerStatus()}}</p>
```
* Property binding is used to assign a object to the ngClass property. The object keys are names of CSS classes created in component .css files, there corrensponding values are boolean values, representing wether or not the class should be linked to the element or not.

## NgFor: 
* Structural directive(i.e. use *) used to dynamically loop through a list.
* Use as such:
```
<app-server *ngFor="let server of servers"></app-server>
```
* Where servers is a javaScript list declared in the component.ts file. 'server' is a random variable name and can be assigned any name of your choosing.
* The above e.g. will insert the app-server component tag for each item in the servers list.
* Can gain access to the loop counter using the following:
```
<div *ngFor="let click of buttonTimestamps; let i = index"> 
	<p [ngStyle]="{backgroundColor: getColor(i)}" [ngClass]="{timestamps: (i >=5 )}">{{i}} : {{click}}</p>
</div>
```

# Section 5: Component & Databinding Deep Dive

## Custom event and property binding:
### Binding to custom properties: 
* It is possible to bind to the properties that we defined in our component classes i.e the variables within the component class declarations. This makes it possible to pass data from a parent component, by binding the data to the child components properties.This is done using the @Input() decorator on the component class properties.

* for e.g. this is done in child component server-element.component.ts file. Note that the element has an imported decorator.
```
import { Component, OnInit, Input } from '@angular/core';
@Component({
  selector: 'app-server-element',
  templateUrl: './server-element.component.html',
  styleUrls: ['./server-element.component.css']
})
export class ServerElementComponent implements OnInit {
  @Input() element: string;

  constructor() { }

  ngOnInit(): void {
  }

}
..
```
* corresponding html (server-element.component.html) file looks like this:
```
..
<div>
	<p>{{element}}<\p>
</div>
..
```
* Then this is done in parent component app.component.ts file:
```
..
export class AppComponent {
  serverElements = ['testServer', serverone', servertwo'];
}
..
```
* Then in app.component.html:
```
..
<div class="container">
  <app-server-element 
    *ngFor='let serverElement of serverElements'
    [element]='serverElement'
    ></app-server-element>
</div>
```
* Note that aliases can be assigned to custom properties by passing them to the decorator, e.g.:
```
..
	@Input('srvElement') element: string;
..
```
* Then in parent html:
```
..
<app-server-element 
    *ngFor='let serverElement of serverElements'
    [srvElement]='serverElement'
    ></app-server-element>
..
```

## Binding to custom events: 
* custom events can be defined for components, these custom events can then be bound to functions defined in the parent component, and therfore data can be passed from a child to a parent component via the $event keyword.
* e.g. this is the .ts file of the child node.Note that Output and Event Emitter must be included in import:
```
..
export class CockpitComponent implements OnInit {

  @Output() serverCreated = new EventEmitter<{serverName: string, serverContent: string}>();

  newServerName = '';
  newServerContent = '';

  constructor() { }

  ngOnInit(): void {
  }

  onAddServer() {
    this.serverCreated.emit({
      serverName: this.newServerName,
      serverContent: this.newServerContent
    })
  }
..
```
* html file of child node updates properties of the component class via two-way-binding, a submit button has event binding which calls onaddserver() method.
* Parent component html file:
```
<div class="container">
  <app-cockpit
    (serverCreated)="onServerAdded($event)"
  ></app-cockpit>
 </div>
 ```
* Parent component ts file:
```
..
export class AppComponent {
  serverElements = [{ type: 'server', name: 'testserver', content: 'Just a test'}];

  onServerAdded(serverData: {serverName: string, serverContent: string}) {
    this.serverElements.push({
      type: 'server',
      name: serverData.serverName,
      content: serverData.serverContent
    });
  }
..
```
* Note that custom event aliases can be created by passing the alias as a parameter to the @Output() decorator.

## View encapsulation: by default a components css file is applid only to that component. Can apply it globally by using:
```
@Component({
  ..
  encapsulation: ViewEncapsulation.None
})
```

## Local references in templates
* Can use local references on any HTML element, these references can then be used to grab data from an input for e.g. by passing the local reference to a method via event binding.
* for e.g.:
```
..
<input type="text" class="form-control" #serverNameInput>

<button
class="btn btn-primary"
(click)="onAddServer(serverNameInput)">Add Server</button>
<button
..
* The onAddServer() method will recieve the actual HTMLInput object, data can then be accessed through this object as such:
..
  onAddServer(inputElement: HTMLInputElement) {
    console.log(inputElement);
    this.serverCreated.emit({
      // serverName: this.newServerName,
      serverName: inputElement.value,
      serverContent: this.newServerContent
    })
  }
..
```

* -Note: To determine the angular type of a variable being console logged, open the prototype and search for the constructor and its name attribute

## Accessing the template and DOM with @ViewChild()
* Local references can be accessed directly with viewchild instead of being passed via event binding. This is done by adding the following to the component class:
```
..
  @ViewChild('serverContentInput', {static: true}) serverContentInput: ElementRef;
  serverContent: string;
..
  onAddServer(inputElement: HTMLInputElement) {
    this.serverContent = this.serverContentInput.nativeElement.value
  }
..
```
* Note: The same change (add { static: true } as a second argument) needs to be applied to ALL usages of @ViewChild() (and also @ContentChild()) IF you plan on accessing the selected element inside of ngOnInit(). If you DON'T access the selected element in ngOnInit (but anywhere else in your component), set static: false instead! If you're using Angular 9+, you only need to add { static: true } (if needed) but not { static: false }.

## Projecting content into components with ng-content
* HTML code can be injected into a child components html file from the parent conponents html file. This is an alternative to passing the content via custom property binding and used where complex html code needs to be passed.
* Note that ng-content is a lifecycle hook i.e. it is one of the methods angular calls throughout the process of instantiating a component within the DOM. lecture 78
* To do this simply place the ng-content hook within the child components html file where you want content to be injected. Then pass the content to the child component between its slector tags.
* e.g. this is child components html file:
```
..
<div class="container'>
	<ng-content></ng-content>
</div>
..
* This is parent components html file:
..
 <app-server-element *ngFor='let serverElement of serverElements'>
      <p>
        <strong *ngIf="serverElement.type === 'server'" style="color: red">{{ serverElement.content }}</strong>
        <em *ngIf="serverElement.type === 'blueprint'">{{ serverElement.content }}</em>
      </p>
  </app-server-element>
</div>
..
```

## Access to ng-content with @ChildContent
* @ChildContent() can be used to access elements with local references that are passed from parent component to child component (via ng-content) from within the child component class.
* e.g. from child component .ts file:
```
..
export class ServerElementComponent implements OnInit, AfterContentInit {
  @Input() element: {type: string, name: string, content: string};
  @ContentChild('pContent', {static: true}) pContent: ElementRef; 

  constructor() { }

  ngOnInit(): void {
  }

  ngAfterContentInit(){
    console.log(this.pContent.nativeElement.textContent);
  }

}
..
```
* Where ngAfterContentInit is a lifecycle hook that the class implements. The {static: true} is needed to access the variable within a life-cycle hook. 'pContent' is the local reference placed on the element that is passed via ng-content.

# section 7: Directives Deep Dive

## Custom Attribute Directive
* Note: this first approach directly accesses a element of the DOM, this is however not good practise, and the second approach should therefore be followed instead.
* This is done by creating a new folder named directives under 'src/app/' and then a TS file within that. The TS file name should have the following format, 'basic-highlight.directive'. 
* The contents of this file are as follows:
```
import { Directive, ElementRef, OnInit } from '@angular/core';

@Directive({
    selector: '[appBasicHighlight]'
})
export class BasicHighlight implements OnInit{

    constructor(private currentElement: ElementRef){

    }

    ngOnInit(): void {
        this.currentElement.nativeElement.style.backgroundColor = 'green';    
    }
}
```
* The square brackets in the selector indicate that this is a attribute directive. The arguments passed to the constructor tell angular that it should get the element that this directive is placed on and its type should be ElementRef(This is done via injection).
* Note that this directive needs to be declared in the app.module.ts file under declarations.

## Better Attribute Directive:
* Note: directives can be generated via the CLI using -> ng generate directive name , or ng g d name
* This approach uses the renderer to access the DOM.
* This approach is exaclty the same as first approach above, except for the following code in ..directive.ts file:
```
import { Directive, ElementRef, OnInit, Renderer2 } from '@angular/core';

@Directive({
  selector: '[appBetterBasicHighlight]'
})
export class BetterBasicHighlightDirective implements OnInit{

  constructor(private currentElement: ElementRef, private renderer: Renderer2) { }

  ngOnInit(): void {
      this.renderer.setStyle(this.currentElement.nativeElement, 'backgroundColor', 'blue');
  }
}
```

## HostListener to listen to host events
* Event that would typically be placed on elements can be placed within a custom directive using HostListener. HostListener is a decorator that declares a DOM event to listen for, and provides a handler method to run when that event occurs.
* Done as follows in directive.ts file:
```
..
export class BetterBasicHighlightDirective implements OnInit{

  constructor(private currentElement: ElementRef, private renderer: Renderer2) { }

  ngOnInit(): void {
  }
  
  @HostListener('mouseenter') mouseover(eventData: Event){
    this.renderer.setStyle(this.currentElement.nativeElement, 'backgroundColor', 'blue');
  }
}
```
* Where 'mouseenter' is a typical mouse event(https://developer.mozilla.org/en-US/docs/Web/API/Element#mouse_events) and the following function(mouseover) is the function that gets executed when the event is fired. It takes in as its argument the event data that is emitted by the event emitter. 
* Note that you can also listen and recieve data from custom events

## HostBinding to bind to host properties
* A decorator which is used to bind values to properties of the element the directive is placed on i.e. the host.
* Done as such:
```
..
export class BetterBasicHighlightDirective implements OnInit{
  @HostBinding('style.backgroundColor') backgroundColor: string = 'blue';  
..
```
* Note that backgroundColor is a declared property of the class and can be accessed any where within in it i.e. within methods using the 'this' keyword.

## Binding to Directive properties:
* Custom event binding and custom property binding can both be done on directives in the same way as before.
* Done as follows in directive.ts file:
```
..
export class BetterBasicHighlightDirective implements OnInit{

  @Input() defaultColor: string = 'transparent';
..
```
* Can then bind to this property in the correspnding html file:
```
..
      <p appBetterBasicHighlight [defaultColor]="'yellow'" [highlightColor]="'red'">I am a test for custom directive</p>
..
```
* Will only work if the directive is included as shown above.
* Check '/home/phoenix/udemy/Angular/section-7-directives-deep-dive/sect7' for e.g.
* Can also set aliases for our custom properties as done before. For ngClass directive the property we bind to has an alias of 'ngClass' this is why you see '[ngClass]'.

## Custom Structural directives:
* Created the same as a custom directive, used in html file with the typical '*'.
* Code in directive.ts:
```
import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';

@Directive({
  selector: '[appUnless]'
})
export class UnlessDirective {
  @Input() set appUnless(condition:boolean){
    if(!condition){
      this.vcRef.createEmbeddedView(this.templateRef);
    }else{
      this.vcRef.clear();
    }

  }
  constructor(private templateRef: TemplateRef<any>, private vcRef: ViewContainerRef) { }
}
```
* HTML file:
```
..
      <div *appUnless="(false)">
        <p>test for custom structural directive</p>
      </div>
..
```
* line 358: This is a property which executes the method that follows due to the set keyword. The method name should be the same as the selector name.
* ViewContainerRef is a container in which views can be created. TemplateRef is similar to ElementRef in that it is used to reference a template. They retreive the host template and viewconstainer via injection when placed inside the constructor.

## Switch statements with ngSwitch:
* Can implement a switch statment in angular.
* Component HTML file:
```
..
	<div [ngSwitch]="value">
	<p *ngSwitchCase="3">Value is 3</p>
	<p *ngSwitchCase="6">Value is 6</p>
	<p *ngSwitchCase="10">Value is 10</p>
	<p *ngSwitchDefault>Value is default</p>
	</div>
..
```
* where value is defined in the component.ts file.


# Section-9: Services & Dependency injection
## Services & Dependency injection
* Services Injection is heirarical i.e. to say that if a service is injected into the app.component.ts file then this specific instance of the service class will be available to all of its child components. If the service needs to be used in a component, it must always be passed as a parameter to the constructor. The providers array determines wether a new instance of the service is created for the component or wether the parent components instance should be used. NB: by declaring the service in the providers array, angular will create new instance of the service for this component. Excluding it will result in the parent components instance being used.
* Note: that the highest possible level for a service injection is in app.module.ts, this also allows us to provide a service to another service. Instead of declaring our service in the app.module.ts file we can just do the following in the sevice.ts file (it is equivalent):
```
..
@Injectable({
    providedIn: 'root'
})
export class ShoppingListService{
..
```

## Creating a Service
* A Service is essentially a class that stores code which we use across components, facilitates the DRY coding principle.
* To create a service, create a servicename.service.ts file under 'src/app/shared/services' (can be different but place under app), file should contain a class with the code you wish to use in its methods.
* e.g. ..services.ts file:
```
export class LoggingService{

    log(newStatus: string){
        console.log('A server status changed, new status: ' + newStatus);
    }
}
```

## Accessing a service via dependency injection:
* dependency injection is a convenient way of accessing our services with our compoonent.ts files.
* e.g. ..component.ts file:
 ```
import { Component, EventEmitter, Input, Output } from '@angular/core';
import { LoggingService } from '../shared/services/logging.service';

@Component({
  selector: 'app-account',
  templateUrl: './account.component.html',
  styleUrls: ['./account.component.css'],
  providers: [LoggingService]
})
export class AccountComponent {
  @Output() statusChanged = new EventEmitter<{id: number, newStatus: string}>();

  constructor(private logger: LoggingService){}

  onSetTo(status: string) {
    this.statusChanged.emit({id: this.id, newStatus: status});
    this.logger.log(status);
  }
}
```
* Service must be given to the constructor as well as declared in the providers array within the class decorator.The service can then be accessed as one of the class properties. 

## Injecting services into other services
* The service to be injected should be included in the providers array within app.module.ts. The Injectable decorator then needs to be included above the service class, it should however always be included.
* e.g. the service that undergoes the injection:
```
import { Injectable } from "@angular/core";
import { LoggingService } from "./logging.service";

@Injectable()
export class AccountService{
    accounts = []

    constructor(private logger: LoggingService){}

    AccountAdd(newAccount: {name: string, status: string}) {
    this.accounts.push(newAccount);
    //Using a service within another service
    this.logger.log(newAccount.status); 
    }

}
```

## Using services for cross-component communication
* Communicating between sibling components can become a complex chain of event emitters and property binding, services can simplyfy this process.
* This is essentially done by defining an event emitter within a shared service of the two components, then using this emitter to emit data from one component (via .emit()) and grab the data in the other component using the same service event emitter ( via .suscribe()).
* e.g. service.ts file:
```
..
@Injectable()
export class AccountService{
    accounts = []

    statusUpdate = new EventEmitter<string>();
..
```
* emitting components ts file:
```
export class AccountComponent {

  constructor(private accountService: AccountService){}


  onSetTo(status: string) {
    this.accountService.StatusUpdate({id: this.id, newStatus: status});

    //Call accountService eventEmitter
    this.accountService.statusUpdate.emit(status);
    }
..
```
* recieving components ts file:
```
export class NewAccountComponent {
  // @Output() accountAdded = new EventEmitter<{name: string, status: string}>();

  constructor(private logger: LoggingService, private accountService: AccountService){
  //recieve data from sibling component
    this.accountService.statusUpdate.subscribe((status: string) => {
      alert("new status update!");
    })
  }
..
```
* Data caught within the constructor here for convenience, can be done anywhere within the component class.

# Section 11: changing pages with routing
* It is possible to display different 'pages' within our single page application with routes

## Setting up and loading routes:
* Routes are setup within the app.modules.ts file:
```
...
import { Route, RouterModule } from '@angular/router';

const appRoutes: Route[] = [
  {path: '', component: HomeComponent},
  {path: 'users', component: UsersComponent},
  {path: 'servers', component: ServersComponent},
]

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    UsersComponent,
    ServersComponent,
    UserComponent,
    EditServerComponent,
    ServerComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    RouterModule.forRoot(appRoutes)
  ],
  providers: [ServersService],
  bootstrap: [AppComponent]
})
```

* appRoutes define which components should be loaded for their specific paths. The RouterModule should be imported and declared in the imports array. The forRoot() method creates and configures a module with all the router providers and directives, it takes An array of Route objects that define the navigation paths for the application as its parameter.
* To display the components depending on the url path you must include the <app-outlet> reference within your template.
* This is the app.component.html file:
```
...
  <div class="row">
    <div class="col-xs-12 col-sm-10 col-md-8 col-sm-offset-1 col-md-offset-2">
      <router-outlet></router-outlet>
    </div>
  </div>
</div>
...
```
* The relevant component will be loaded in place of <router-outlet> depending on the url path specified.

## Navigating with router links:
* Can use router links to navigate between routes. The first method shown below will however reload our application evertime the route is changed, the application will therefore lose its state. Unless this behaviour is required you should not follow this approach but rather the second approach. 
* app.component.html file:
```
...
 <div class="col-xs-12 col-sm-10 col-md-8 col-sm-offset-1 col-md-offset-2">
      <ul class="nav nav-tabs">
        <li role="presentation" class="active"><a href="/">Home</a></li>
        <li role="presentation"><a href="/servers">Servers</a></li>
        <li role="presentation"><a href="/users">Users</a></li>
      </ul>
    </div>
  </div>
  <div class="row">
    <div class="col-xs-12 col-sm-10 col-md-8 col-sm-offset-1 col-md-offset-2">
      <router-outlet></router-outlet>
    </div>
...
```
* The href parameters contain the router links

* Should rather use this method. Use the routerLink directive provided by angular, which prevents the default appraoch of making a request.
* app.component.html file:
```
...
 <ul class="nav nav-tabs">
        <li role="presentation" class="active"><a routerLink="/">Home</a></li>
        <li role="presentation"><a routerLink="/servers">Servers</a></li>
        <li role="presentation"><a [routerLink]="['/users']">Users</a></li>
      </ul>
    </div>
  </div>
  <div class="row">
    <div class="col-xs-12 col-sm-10 col-md-8 col-sm-offset-1 col-md-offset-2">
      <router-outlet></router-outlet>
    </div>
...
```
* Note that the leading / is there to define a absolute path, without the path will be relative i.e. the path will be appended to the current path that is displayed.

## Styling active routers
* Can use routerLinkActive property to tell angular to apply a css class to the html tag when the router link is active
* e.g.:
```
...
      <ul class="nav nav-tabs">
        <li class="nav-item"  ><a class="nav-link" routerLinkActive="active" [routerLinkActiveOptions]="{exact: true}" routerLink="/">Home</a></li>
        <li class="nav-item"  ><a class="nav-link" routerLinkActive="active" routerLink="/servers">Servers</a></li>
        <li class="nav-item"  ><a class="nav-link" routerLinkActive="active" [routerLink]="['/users']">Users</a></li>
      </ul>
...
```
* RouterLinkActive is assigned the bootstrap class active which will be applied when that route is active. routerLinkActiveOptions is given an object with properties exact set to true this ensures that the route is only active when the exact path is specified and is not also apart of a different path.

## Navigate using typescript
* Can inject the router module into your component.ts file and then use it to navigate programmatically
* e.g.:
```
...
  constructor(private router: Router) { }

  ngOnInit() {
  }

  onServerClick(){
    this.router.navigate(['/servers'])
  }
}
...
```
* The onServerClick method can be bound using event binding.

## Absolute paths in typescript
* The router.navigate method does not know the current path, therfore if you are using relative paths you should specify the current path. To do this activated route should be injected and passed as an object to the router.
* e.g.:
```
...
  constructor(private router: Router, private route: ActivatedRoute) { }

  ngOnInit() {
  }

  onServerClick(){
    this.router.navigate(['servers'], {relativeTo: this.route});
  }
}
...
```

## Passing parameters to the router
* Routes can be setup to receive data dynamically
* e.g. app.module.ts file:
```
...
const appRoutes: Route[] = [
  {path: '', component: HomeComponent},
  {path: 'users', component: UsersComponent},
  {path: 'user/:id/:name', component: UserComponent},
  {path: 'servers', component: ServersComponent},
]
...
```
* Words after the : represent the parameter names. 

## Fetching parameters from the router
*  Parameters can be accessed within the ActivatedRoute.snapshot.params[] array within .component.ts file
* Note: that this method returns parameters of type string, use parseInt() or parseFloat() to convert.
* e.g. user.component.ts file:
```
...
export class UserComponent implements OnInit {
  user: {id: number, name: string};

  constructor(private route: ActivatedRoute) { }

  ngOnInit() {
    this.user = {
      id: this.route.snapshot.params['id'],
      name:  this.route.snapshot.params['name'] 
    }
  }

}
...
```
*  Note: If you do this and then try and change the router parameters (using the following code) in the template, the user component parameter will not be updated. This is default angular behavior, angular sees that the component has not changed and therefore onInit is not called again.
```
<p>User with ID {{user.id}} loaded.</p>
<p>User name is {{user.name}}</p>
<hr>
<a [routerLink]="['/user', 10, 'Anna']"></a>
```
* Rather do the following

## Fetching parameters from the router(better approach)
* Use ActivatedRoute.params, which is a observable. You should subscribe to this method in ngOnInit, then whenever the route parameters change, the relevant function will be called.
* Therefore using this approach ensures that route parameters are always updated even when component doesn't change.
* e.g.:
```
  constructor(private route: ActivatedRoute) { }

  ngOnInit() {
    this.user = {
      id: this.route.snapshot.params['id'],
      name:  this.route.snapshot.params['name'] 
    };

    this.route.params.subscribe((params: Params) => {
      this.user.id = params['id'];
      this.user.name = params['name'];
      }
    );
  }
}
```
* Should import Params!

## Passing query parameters and fragments
* Extra information that contained within parameters that we can pass to the router
* To pass query parameters via the template,  we bind the query parameter values to the queryParams property of teh routerlink directive as such. Note that it takes in an object as its value.
* To pass fragments via the template, bind to the fragment property which takes in a string.
* e.g.:
```
...
      <a
        [routerLink]="['/servers', 10, 'edit']"
        [queryParams]="{allowEdit : '1'}"
        href="#"
        class="list-group-item"
        *ngFor="let server of servers">
        {{ server.name }}
      </a>
...
```
* To do pass query parameters and fragments programmatically, simply pass them as parameters to the Router.navigate method as follows.
* e.g.:
```
...
  onServerClick(id: number){
    this.router.navigate(['/servers', id, 'edit'], {queryParams: {allowEdit: '1'}, fragment: 'Loading'});
  }
...
```

## Retrieving query parameters and fragments 
* Accessed same as before with one of two ways. The first is used if there is no chance of the parameters being updated from within the component, and done with ActivatedRoute.snapshot.
* e.g.:
```
...
  constructor(private serversService: ServersService, private route: ActivatedRoute) { }

  ngOnInit() {
    console.log(this.route.snapshot.queryParams);
    console.log(this.route.snapshot.fragment);
...
```
* If however your parameter values can change from within the component then you should use subscriptions:
* e.g.:
```
...
  ngOnInit() {
    this.route.queryParams.subscribe((params: Params) =>{
      console.log(params);
    });
    this.route.fragment.subscribe((value: string) =>{
      console.log(value);
    });
...
```

## Setting up child(nested) routes
* Nested routes work the same as before except they give the ability to dynamically display components within another component.
* To setup: Add the shild routes to the app.module.ts file:
```
...
const appRoutes: Route[] = [
  {path: '', component: HomeComponent},
  {path: 'users', component: UsersComponent, children: [
    {path: ':id/:name', component: UserComponent}
  ]},
  {path: 'servers', component: ServersComponent, children: [
    {path: ':id', component: ServerComponent},
    {path: ':id/edit', component: EditServerComponent}
  ]},
]
...
```
* The corresponding route component will be inserted where ever the <router-outlet> tag is placed within the parent route component.
* e.g. servers.component.html file:
```
...
  <div class="col-xs-12 col-sm-4">
    <router-outlet></router-outlet>
  </div>
</div>
...
```

## Preserving query parameters and fragments
* When changing to a different route it is possible to preserve the current query parameters and fragments or even merge them with new ones. Done by specifying which optio in the queryParamsHandling parameter set within the Router.navigate method.
* e.g.
```
...
    onServerEdit(){
    this.router.navigate(['edit'], {relativeTo: this.route, queryParamsHandling: 'preserve'});
  }
...
```

## Redirecting and wild card routes
* Can define a 'wild card' path to catch all paths which are manually entered and not defined, this is done using '**'.
* The redirectTo parameter of the route path takes a string containing the name of an already defined path.
* e.g.:
```
...
  {path: 'not-found', component: PageNotFoundComponent},
  {path: '**', redirectTo: 'not-found'}
]
...
```

## Redirection path matching
* By default, Angular matches paths by prefix. That means, that the following route will match both `/recipes` and just `/`

```{ path: '', redirectTo: '/somewhere-else' } ```

Actually, Angular will give you an error here, because that's a common gotcha: This route will now ALWAYS redirect you! Why?

Since the default matching strategy is "prefix" , Angular checks if the path you entered in the URL does start with the path specified in the route. Of course every path starts with ''  (Important: That's no whitespace, it's simply "nothing").

To fix this behavior, you need to change the matching strategy to `"full"` :

```{ path: '', redirectTo: '/somewhere-else', pathMatch: 'full' } ```

Now, you only get redirected, if the full path is ''  (so only if you got NO other content in your path in this example).

## Outsourcing the Route configuration
* Can place the route config in its own separate module and then import into the main module. This is done by first creating a app-routing.module.ts file and moving route paths to it. This module then needs to be declared within the main modules imports.
* contents of app-routing.module.ts:
```
import { NgModule } from "@angular/core";
import { Route, RouterModule } from "@angular/router";

import { HomeComponent } from "./home/home.component";
import { PageNotFoundComponent } from "./page-not-found/page-not-found.component";
import { EditServerComponent } from "./servers/edit-server/edit-server.component";
import { ServerComponent } from "./servers/server/server.component";
import { ServersComponent } from "./servers/servers.component";
import { UserComponent } from "./users/user/user.component";
import { UsersComponent } from "./users/users.component";

const appRoutes: Route[] = [
    {path: '', component: HomeComponent},
    {path: 'users', component: UsersComponent, children: [
      {path: ':id/:name', component: UserComponent}
    ]},
    {path: 'servers', component: ServersComponent, children: [
      {path: ':id', component: ServerComponent},
      {path: ':id/edit', component: EditServerComponent}
    ]},
    {path: 'not-found', component: PageNotFoundComponent},
    //'**' used to refer to all possible paths not listed above
    {path: '**', redirectTo: 'not-found'}
  ]

@NgModule({
    imports: [
        RouterModule.forRoot(appRoutes)
    ],
    exports: [
        RouterModule
    ]
})
export class AppRoutingModule{}
```
* The imports parameter within the ngModule decorator configures the RouterModule, adds our own custom routes to it. The export parameter tells modules that imports this module that they need to have access to the configured RouterModule.
* Then declare it in app.module.ts:
```
...
  imports: [
    BrowserModule,
    FormsModule,
    AppRoutingModule
  ],
...
```

## Protecting routes with guards (canActivate)
* Guards are used to restrict or grant access to certain route paths. An example of this could be only allowing access if a user is logged in.
* For the following example an authentication service is created to mimic the authentication that is covered later in the notes (see /home/phoenix/udemy/Angular/section-11-routing/sect-11/src/app/auth.service.ts).
* canActivate is a property that is applied to the route paths, it takes in an array of all the guards (guarding methods that return a boolean, or return a promise that returns a boolean) that should be applied. Note that the canActivate property will be applied to all the child routes as well.
* The app-routing.module.ts:
```
...
const appRoutes: Route[] = [
    {path: '', component: HomeComponent},
    {path: 'users', component: UsersComponent, children: [
      {path: ':id/:name', component: UserComponent}
    ]},
    {path: 'servers', canActivate: [AuthGuardService], component: ServersComponent, children: [
      {path: ':id', component: ServerComponent},
      {path: ':id/edit', component: EditServerComponent}
    ]},
...
```
* AuthGuardService contains our implementation of the canActivate method
* The auth-guard.service.ts file:
```
...
@Injectable()
export class AuthGuardService implements CanActivate{

    loggedIn = false;

    constructor(private authService: AuthService, private router: Router){}

    canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean | UrlTree | Observable<boolean | UrlTree> | Promise<boolean | UrlTree> {
        
        console.log("im in can Activate")
        return this.authService.isAuthenticated().then((authenticated: boolean) => {
            if(authenticated){
                //i.e. user can access that route
                return true; 
            }else{
                this.router.navigate(['/']);
            }
        })
    }
...
```
* The canActivate method returns a promise here that resolves to a boolean value
* Both services need to be declared in the providers array in app.module.ts

## Protecting child routes with canActivateChild
* Works the same as with can CanActivate except the canActivateChild only gets applied to the child routes
* app-routing.module.ts file:
```
...
    {path: 'servers',
      canActivateChild: [AuthGuardService],
      component: ServersComponent, 
      children: [
      {path: ':id', component: ServerComponent},
      {path: ':id/edit', component: EditServerComponent}
    ]},
...
```
* auth-guard.service.ts file:
```
...
    canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean | UrlTree | Observable<boolean | UrlTree> | Promise<boolean | UrlTree> {
        
        return this.authService.isAuthenticated().then((authenticated: boolean) => {
            if(authenticated){
                //i.e. user can access that route
                return true; 
            }else{
                this.router.navigate(['/']);
            }
        })
    }

    canActivateChild(childRoute: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean | UrlTree | Observable<boolean | UrlTree> | Promise<boolean | UrlTree> {
        return this.canActivate(childRoute, state);
    }
...
```

## Redirecting with UrlTree
* The `CanActivate` method must return either a boolean, `UrlTree`, Observable or Promise that returns a  boolean or `UrlTree`.
* A `UrlTree` essentially tells angular which route to redirect to should the user be denied access to the current route.
* To use create a `UrlTree` with the the `ActivatedRouter` and return it in the `CanActivate` method.

auth-guard.service.ts:
```
...
  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean | UrlTree | Observable<boolean | UrlTree> | Promise<boolean | UrlTree> {
      
      return this.authService.isAuthenticated().then((authenticated: boolean) => {
          if(authenticated){
              //i.e. user can access that route
              return true; 
          }else{
              return this.router.createUrlTree(['/']);
          }
      })
  }
...
```


## Controlling navigation with canDeactivate (lecture 150)
* canDeactivate allows us to check if a user should be allowed to leave the current route, useful for e.g. when a user has input but not submit data in a form.
*  canDeactivate is an interface defined in "@angular/router", we use it similarly to canActivate interface where the property is set on the route path. It takes in an array of guards (our custom service guards). The services need to be declared in providers array in app.module.ts.
* app-routing.module.ts file:
```
...
    {path: 'servers',
    //  canActivate: [AuthGuardService],
      canActivateChild: [AuthGuardService],
      component: ServersComponent, 
      children: [
      {path: ':id', component: ServerComponent},
      {path: ':id/edit', canDeactivate: [CanDeactivateGuard], component: EditServerComponent}
    ]},
...
```
* Our guard service needs to have access to the currently active component, because often the data we need to determine if the user can leave the current path is contained there.
* can-deactivate-guard.service.ts file:
```
...
export interface CanComponentDeactivate{
    //Note that this method can have any name
    canDeactivate(): boolean | UrlTree | Observable<boolean | UrlTree> | Promise<boolean | UrlTree>
}

export class CanDeactivateGuard implements CanDeactivate<CanComponentDeactivate>{

    constructor(){}

    canDeactivate(component: CanComponentDeactivate, currentRoute: ActivatedRouteSnapshot, currentState: RouterStateSnapshot, nextState?: RouterStateSnapshot): boolean | UrlTree | Observable<boolean | UrlTree> | Promise<boolean | UrlTree> {
        //This can be any method of the EditServerComponent which returns a value of type: boolean | UrlTree | Observable<boolean | UrlTree> | Promise<boolean | UrlTree>
        return component.canDeactivate();
    }
}
...
```
* The CanComponentDeactivate interface is exported and implemented by our currently active component class. This will ensure that whatever our currently active component class is, it will be forced to implement the canDeactivate() method.
* edit-server.component.ts file:
```
...
export class EditServerComponent implements OnInit, CanComponentDeactivate{
  server: {id: number, name: string, status: string};
  //Note that server name and server status are bound to inputs via two-way binding.
  serverName = '';
  serverStatus = '';
  allowEdit: boolean = false;
  changesSaved = false;
...
  canDeactivate(): boolean | UrlTree | Observable<boolean | UrlTree> | Promise<boolean | UrlTree> {
    if(!this.allowEdit){
      return true;
    }else if(( (this.serverName !== this.server.name) || (this.serverStatus !== this.server.status) ) && !this.changesSaved){
      return confirm("You have unsaved changes! Do you wish to discard them?");
    }else{
      return true;
    }
  }

}
...
```

## Passing Static data to a route
*  Static data can be passed to a route using the data keyword, it can then be grabbed using methods described previously i.e. with ActivatedRoute.snapshot.params[]
* app-routing.module.ts file:
```
...
 {path: 'not-found', component: ErrorPageComponent, data: {message: "This page was not found"}},
...
```
* error-page.component.ts file:
```
...
export class ErrorPageComponent implements OnInit {

  errorMessage: string;

  constructor(private route: ActivatedRoute) { }

  ngOnInit(): void {
    this.errorMessage = this.route.snapshot.data['message'];
    console.log(this.errorMessage)
  }

}
...
```

## Resolving dynamic data with the resolve guard
* Resolvers are used to load data (from some backend for instance) before the route is displayed and component rendered. Would usually outsource the data within the `onInit` function of a component class to the resolver.
* Resolvers can return an __observable__ or a promise which would be asynchronous code such as a http request.
* __Note:__ Should the resolver return an observable, there is no need to __subscribe__ to it, the resolver will automatically subscribe to it in order to receive the data!!!
* To use a resolver must first define a new service class which implements the `Resolve` interface from `"@angular/router"`, in this class must define the `resolve` method which returns a __observable__, a promise or a generic type. Declare the service in the `app.module.ts` file. 
* Then add the `resolve` property to the relevant route. This property can be assigned values of two different formats: 
  * An object where the keys can have any name, there values however have to be our defined Resolver service class name.
  * An array of the Resolver service class names associated with this route i.e. `{path: ':id', component: ServerComponent, resolve: [ServerResolver]}`.
* server-resolver.service.ts file:
```
interface server{
    id: number,
    name: string,
    status: string
}
@Injectable()
export class ServerResolver implements Resolve<server>{

    constructor(private serverService: ServersService){}


    resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): server | Observable<server> | Promise<server> {
        let serverId = +route.params['id'];
        return this.serverService.getServer(serverId);
    }
}
```
* Resolve is a generic type which wraps the type which you want to fetch, in this case it is a server whose interface is defined above.
* The service will rerun every time we rerender the route
* Would typically fetch data front he backend within the resolver.
* app-routing.module.ts file:
```
...
    {path: 'servers',
    //  canActivate: [AuthGuardService],
      canActivateChild: [AuthGuardService],
      component: ServersComponent, 
      children: [
      {path: ':id', component: ServerComponent, resolve:{server: ServerResolver}},
      {path: ':id/edit', canDeactivate: [CanDeactivateGuard], component: EditServerComponent}
    ]},
...
```
* Can then access this dynamic data similarly to static data, instead we can setup an observable to catch the data
* server.component.ts file:
```
...
export class ServerComponent implements OnInit {
  server: {id: number, name: string, status: string};

  constructor(private serversService: ServersService, private route: ActivatedRoute, private router: Router) { }

  ngOnInit() {

    this.route.data.subscribe((data: Data) => {
      this.server = data['server'];
    })
  }
...
```


## Understanding location strategies(See lecture 153)
* When deploying app on a server the server will try to handle url requests itself which we do not want, we in fact want angular to handle the routing. The server must therefore be setup to return the index.html file when a 404 error occurs 
* an alternative if you cant get this working is to configure the router to use hash mode

app-routing.module.ts file:
```
@NgModule({
    imports: [
        //This line configures the RouterModule, adds our own custom routes to it, the object is used to configure parameters of the module.
        // RouterModule.forRoot(appRoutes, {useHash: true})
        RouterModule.forRoot(appRoutes, {useHash: true})
    ],
```

# Section-13: Observables
* Observables are functions that throw values. Objects called observers subscribe to these values. See lecture(170)
* Observables are constructs to which you subscribe to be informed about changes in data.

## Building can observable with RxJS
* Observables are not built in to javaScript and therefore need to be imported using the RxJS package
* In this example we make use of the RxJS interval() function, which returns an observable that emits sequential numbers every specified interval of time.
e.g. home.component.ts :
```
import { Component, OnInit, OnDestroy } from '@angular/core';

import { interval, Subscription } from 'rxjs';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit, OnDestroy {

  subscription : Subscription;

  constructor() { }

  ngOnInit() {
    this.subscription = interval(1000).subscribe((count) => {
      console.log(count);
    })
  }

...
```
* The observable is subscribed to in OnInit() and the count is logged to the console
* It is important to note that a new observable is created everytime the component is initialised and previously created observables are not destroyed.
* It is therefore always necessary to unsubscribe from our created observables as shown below.
```
...
  ngOnDestroy(): void {
      this.subscription.unsubscribe();
  }
...
```

## Building a custom Observable
* Here we will implement the above observable from scratch
* To create a observable:
```
...
import { Observable } from 'rxjs';
...
  ngOnInit() {
    const customIntervalObservable = new Observable((observer) => {
      let count: number = 0;
      setInterval(() => {
        observer.next(count);
        count++;
      }, 1000);
    })
  }

    this.subscription = customIntervalObservable.subscribe((data) => {
    console.log(data);
  });
...
```
* The Observable takes a function as its parameter, this is the function that is called when the Observable is initially subscribed to.
* This function is given a `observer` object, which specifies what functions should be executed depending on the 'state' the observable resolves to. The 'states' include: the `next` state, an `error` state can be called to raise an error, or `complete` state can be called to notify of a successful completion.
  
The observer object structure:
```
  let myobs = {
      next: (count) => console.log(count),
      error: (error) => alert(error.message),
      complete: () => console.log('completed')
    })
```
* The `error` method can be used to throw an error, when is does the observable is destroyed.
error e.g.:
```
...
    const customIntervalObservable = new Observable((observer) => {
      let count: number = 0;
      setInterval(() => {
        if(count > 3){
          observer.error(new Error("Count is above 3"))
        }
        observer.next(count);
        count++;
      }, 1000);
    })
...
```
* Note that when subscribing to a observable one should pass an observer as argument unless you are only interested in grabbing the data emited by `next`, then you can use an arrow function.
```
...
    const customIntervalObservable = new Observable((observer) => {
      let count: number = 0;
      setInterval(() => {
        if(count === 2){
          observer.complete();
        }
        if(count > 3){
          observer.error(new Error("Count is above 3"))
        }
        observer.next(count);
        count++;
      }, 1000);
    })

    this.subscription = customIntervalObservable.subscribe(  {
      next: (count) => console.log(count),
      error: (error) => alert(error.message),
      complete: () => console.log('completed')
    })

  }
...
```
* The `.subcribe()` method takes an object (observer) with each key assigned a function to execute when the observable methods are fired.

## Operators on RxJS Observables
* https://rxjs-dev.firebaseapp.com/
* RxJS observables has many predefined operators that can be used, https://www.learnrxjs.io/learn-rxjs/operators
* The operators are used to manipulate the data emitted by an observable before it is grabbed by the `.subcribe()` method.
* To use them we use the `.pipe()` method on the observable and pass the operator as an argument.
```
...
    const customIntervalObservable = new Observable((observer) => {
      let count: number = 0;
      setInterval(() => {
        if(count === 2){
          observer.complete();
        }
        if(count > 3){
          observer.error(new Error("Count is above 3"))
        }
        observer.next(count);
        count++;
      }, 1000);
    })

    const customIntervalPipedObservable = customIntervalObservable.pipe(filter((data) =>{
      return (data > 0);
    }));

    this.subscription = customIntervalPipedObservable.subscribe(  {
      next: (count) => console.log(count),
      error: (error) => alert(error.message),
      complete: () => console.log('completed')
    })
...
```
* Note that we subscribe to the observable returned by the `pipe` method.

## Subjects
* A subject is a type of observable also available in rxjs, the difference being that an observable emit is triggered internally while a subject emit can be triggered externally in code. observable is more passive while a subject is active.
* A Subject is a special type of Observable that allows values to be multicasted to many Observers.
* An Observable by default is unicast. Unicasting means that each subscribed observer owns an independent execution of the Observable.
* When calling subscribe on a Subject it does not invoke a new execution that delivers data. It simply registers the given Observer in a list of Observers.
* More info: https://luukgruijs.medium.com/understanding-rxjs-subjects-339428a1815b
* Subjects should generally only be used in place of observables(eventEmitters) when cross component communication through a service is required. If an event emitter emits data using `@Output()` then do NOT use a subject.

Instantiation within user.service.ts:
```
 import { Injectable } from "@angular/core";
import { Subject } from "rxjs";

@Injectable({
    providedIn: 'root'
})
export class UserService{
    onActivated = new Subject<boolean>();
}
```
* Now instead of using the `.emit()` method, we use `.next()` with the arguments being the data you wish to emit.

user.component.ts file:
```
...
  onActivated(){
    this.userService.onActivated.next(true);
  }
...
```
* And to retrieve the data the `.subscribe()` method is still used.
* NOTE: angular will not handle the unsubscription of created subjects, you have to do this manually within the `onDestroy()` hook. This must always be done to avoid memory leaks(memory being used up by data that is not being used).

app.component .ts file:
```
...
export class AppComponent implements OnInit, OnDestroy {
  activated = false;
  onActivatedSub: Subscription

  constructor(private userService: UserService) {}

  ngOnInit() {
    this.onActivatedSub = this.userService.onActivated.subscribe((val: boolean) => {
      this.activated = val;
    })
  }
  
  ngOnDestroy(){
    this.onActivatedSub.unsubscribe();
  }
}
...
```

# Section 15: Handling forms in angular apps
## Two approaches to handling forms in angular:
* Template driven: Angular infers the form object from the DOM.
* Reactive: form is created programmatically and synchronized with the DOM. 

## Template Driven Approach 
## Creating a Form, registering the controls and submitting(TD)
* For TD approach to work you need to import `FormsModule` in the app.module.ts file
* Angular recognizes a form element in html code and will automatically generate a JS representation of that form.
* It will not however recognize the input elements within the form. It is therefore required to register them as control elements using the `ngModel` directive, as well as a name attribute (inherent to all html elements).
* The name attribute will be used by angular to label the form control in JS.
* To submit the form, the ngSubmit event is bound to a custom function within the form element. To gain access to the form object, a local reference is created and `ngForm` is assigned to it, this local reference is then passed to the event fire function.

app.component.html:
```
...
  <form (ngSubmit)="onSubmit(formObj)" #formObj="ngForm">
    <div id="user-data">
      <div class="form-group">
        <label for="username">Username</label>
        <input type="text" id="username" class="form-control" ngModel name="username">
      </div>
      <button class="btn btn-default" type="button">Suggest an Username</button>
      <div class="form-group">
        <label for="email">Mail</label>
        <input type="email" id="email" class="form-control" ngModel name="email">
      </div>
    </div>
    <div class="form-group">
      <label for="secret">Secret Questions</label>
      <select id="secret" class="form-control" ngModel name="secretQuestion">
        <option value="pet">Your first Pet?</option>
        <option value="teacher">Your first teacher?</option>
      </select>
    </div>
    <button class="btn btn-primary" type="submit">Submit</button>
  </form>
...
```
app.component.ts file:
```
import { Component } from '@angular/core';
import { NgForm } from '@angular/forms';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  suggestUserName() {
    const suggestedName = 'Superuser';
  }

  onSubmit(form: NgForm){
    console.log(form.form);
  }
}
...
```
* The passed form object is of type ngForm and the data can be accessed through it.

## Accessing the form with @viewChild(TD)
* Another approach to accessing the ngForm object is to grab it with @viewChild

app.component.html:
```
...
  <form (ngSubmit)="onSubmit()" #formObj="ngForm">
    <div id="user-data">
      <div class="form-group">
        <label for="username">Username</label>
        <input type="text" id="username" class="form-control" ngModel name="username">
      </div>
      <button class="btn btn-default" type="button">Suggest an Username</button>
      <div class="form-group">
        <label for="email">Mail</label>
        <input type="email" id="email" class="form-control" ngModel name="email">
      </div>
    </div>
    <div class="form-group">
      <label for="secret">Secret Questions</label>
      <select id="secret" class="form-control" ngModel name="secretQuestion">
        <option value="pet">Your first Pet?</option>
        <option value="teacher">Your first teacher?</option>
      </select>
    </div>
    <button class="btn btn-primary" type="submit">Submit</button>
  </form>
...
```
app.component.ts file:
```
...
export class AppComponent {
  @ViewChild('formObj') formObj: NgForm;

  suggestUserName() {
    const suggestedName = 'Superuser';
  }

  onSubmit(){
    console.log(this.formObj.form);
  }

}
```
* This will also allow access to the form object before it has been submitted.

## Adding validators to check user input(TD)
* Angular has built in validators that are essentially used as directives
* more info: https://angular.io/api/forms/Validators
* and: https://angular.io/api?type=directive

app.component.html:
```
...
<input type="text" id="username" class="form-control" ngModel name="username" required>
...
<input type="email" id="email" class="form-control" ngModel name="email" required email >
...
```
* `required` although being a html attribute, is also a angular directive here. `email` is angular directive to enforce validation on form control.

## Using the form state(TD)
* Angular sets special CSS classes on the control element to reflect the state of the input and the form itself.
* List of these classes: https://angular.io/guide/form-validation#control-status-css-classes, https://angular.io/guide/forms#track-control-states
* Here are the classes
  * .ng-valid
  * .ng-invalid
  * .ng-pending
  * .ng-pristine -> input hasn't been changed from initial value
  * .ng-dirty -> input has been changed from initial value
  * .ng-untouched -> input hasn't been clicked on
  * .ng-touched -> input has been changed from initial value
  * .ng-submitted (enclosing form element only) -> applied only to form tag when submit button clicked
* We can use these classes to add our own custom styling

app.component.css:
```
.container {
  margin-top: 30px;
}

input.ng-touched.ng-invalid{
  border-color: crimson;
}
```

## Outputting validation error messages(TD)
* It is possible to gain access to the different states applied to the control elements from within the template(html file). 
* To do this declare a local reference and assign `ngModel` to it. The local reference can now be used throughout the template to access its state.

app.component.html:
```
...
 <div class="form-group">
      <label for="email">Mail</label>
      <input 
        type="email" 
        id="email" 
        class="form-control" 
        ngModel 
        name="email" 
        required 
        email 
        #email="ngModel">
    </div>
    <span class="help-block" *ngIf="!email.valid && email.touched">Please enter a valid email!</span>
  </div>
...
```

## ngModel property binding(TD)
* Can use property binding on ngModel to assign default values to control elements

app.component.html:
```
...
  <div class="form-group">
    <label for="secret">Secret Questions</label>
    <select 
      id="secret" 
      class="form-control" 
      [ngModel]="defaultQuestion" 
      name="secretQuestion" 
      required>
      <option value="pet">Your first Pet?</option>
      <option value="teacher">Your first teacher?</option>
    </select>
  </div>
...
```
app.component.ts:
```
...
export class AppComponent {
  @ViewChild('formObj') formObj: NgForm;
  defaultQuestion = 'teacher'; 
...
```
* Note that default question refers to the 'value' assigned to the option tags within the select tag


## ngModel two-way binding(TD)
* Two way binding works the same as before

app.component.html:
```
...
  <div class="form-group">
    <label for="answer">Answer</label>
    <textarea name="questionAnswer" id="answer" rows="3" class="form-control" [(ngModel)]="questionAnswer"></textarea>
  </div>
  <p>Your answer is: {{questionAnswer}}</p>
...
```
* Question answer is a property of the app component class


## Grouping form controls(TD)
* Control elements can be grouped within a sub-object of the ngFrom object. This group will then have its own states that are applied as classes.
* To do this `ngModelGroup` directive is applied to a div containing the control elements, it is assigned a string representing the chosen name of the group.
* To gain access to this group and its states within the template, place a local reference on the `div` and assign it a given name. This local reference can then be used to gain access to the group states.

app.component.html:
```
...
<div id="user-data" ngModelGroup='userData' #userData='ngModelGroup'>
  <div class="form-group">
    <label for="username">Username</label>
    <input type="text" id="username" class="form-control" ngModel name="username" required>
  </div>
  <button class="btn btn-default" type="button">Suggest an Username</button>
  <div class="form-group">
    <label for="email">Mail</label>
    <input 
      type="email" 
      id="email" 
      class="form-control" 
      ngModel 
      name="email" 
      required 
      email 
      #email="ngModel">
  </div>
</div>
<p *ngIf="userData.invalid && userData.touched">UserData is invalid!</p>
...
```

## Radio buttons(TD)
* Radio buttons work the same as usual except for some reason the value assigned to them is not displayed, therefore just hardcode it using `p` for e.g.
  
app.component.html:
```
...
<div class="form-group">
  <label for="sex">Sex</label>
  <div *ngFor="let item of sex" id="sex">
    <input type="radio" name="sex" ngModel [value]="item" required>
    {{item}}
  </div>
</div>
...
```
app.component.ts:
```
...
export class AppComponent {
  @ViewChild('formObj') formObj: NgForm;
  defaultQuestion = 'teacher'; 
  questionAnswer: string;
  sex = ['male', 'female'];
...
```

## Setting and Patching form values(TD)
* Can use `setValue()` method on an `ngForm` object to set the values of all the form control elements programmatically.This method takes in a JS object as argument, the object must have the same structure as the current `ngForm.form` object.
* In the same way, can use `patchValue` method of a `ngForm.form` object to set specific control element values. This method takes in a JS object as argument. 

e.g. for `setValue()`, app.component.ts:
```
...
  suggestUserName() {
    const suggestedName = 'Superuser';
    this.formObj.setValue({
      userData: {
        username: suggestedName,
        email: ''
      },
      secretQuestion: 'pet',
      questionAnswer: '',
      sex: 'female'
    });
  }
...
```
e.g. for `patchValue()`, app.component.ts:
```
...
  suggestUserName() {
    const suggestedName = 'Superuser';
    this.formObj.form.patchValue({
      userData: {
        username: suggestedName
      }
    })
  }
...
```

## Using the form data and resetting the form(TD)
* Can access the form data using `@ViewChild` as learned previously

e.g. Grabbing and storing the data, app.component.ts:
```
...

  formData = {
    username : '',
    email: '',
    secretQuestion: '',
    secretQuestionAnswer: '',
    sex: ''
  }

  suggestUserName() {
    const suggestedName = 'Superuser';
    this.formObj.form.patchValue({
      userData: {
        username: suggestedName
      }
    })
  }

  onSubmit(){
    console.log(this.formObj.form);
    this.formData.username = this.formObj.form.value.userData.username;
    this.formData.email = this.formObj.form.value.userData.email;
    this.formData.secretQuestion = this.formObj.form.value.secretQuestion;
    this.formData.secretQuestionAnswer = this.formObj.form.value.questionAnswer;
    this.formData.sex = this.formObj.form.value.sex;

    this.formObj.reset();
  }

}
...
```
* The `.reset()` method can also be passed a object containing values to reset the form to.

## Reactive Approach 
## Creating a form, syncing to HTML and submitting(RA)
* To create a reactive form firstly `ReactiveFormsModule` needs to be imported and declared under imports in `app.module.ts`. The `FormsModule` is however not needed.
* The form is then created in JS by instantiating the `FormGroup` class and passing a object to its constructor. This object contains key-value pairs, with the keys (of type string!) representing the name of the controls, and values being the actual controls themselves (of type `FormControl`). `FormControl` takes the initial value of the control as its argument.

app.component.ts:
```
...
export class AppComponent implements OnInit {

  sex = ['male', 'female'];
  signUpForm: FormGroup;

  ngOnInit(): void {
    
      this.signUpForm = new FormGroup({
        'username': new FormControl(null),
        'email': new FormControl(null),
        'sex': new FormControl('Female')
      });
      console.log(this.signUpForm);
  }
}

  onSubmit(){
    console.log(this.signUpForm);
  }
}
...
```
* To link the signUpGroup object with our HTML code, the `formGroup` directive is bound to the object within the `form` tag.

app.component.html:
...
```
  <form [formGroup]="signUpForm" (ngSubmit)="onSubmit()">
    <div class="form-group">
      <label for="username">Username</label>
      <input
        type="text"
        id="username"
        formControlName="username"
        class="form-control">
    </div>
    <div class="form-group">
      <label for="email">email</label>
      <input
        type="text"
        id="email"
        formControlName="email"
        class="form-control">
    </div>
    <div class="radio" *ngFor="let item of sex">
      <label>
        <input
          type="radio"
          [value]="item"
          formControlName="sex">{{ item }}
      </label>
    </div>
    <button class="btn btn-primary" type="submit">Submit</button>
  </form>
...
```
* By using binding our created form to the `formGroup`, angular knows not to automatically generate a form object where the `<form>` tags are but instead to use ours.
* The `formControlName` directive is bound to the appropriate signUpForms controls within the input tags of the form.
* To submit the form the `ngSubmit` event is bound to our custom function within the `<form>` tag. The values in the signUpForm object will be updated when this event fires.


## Adding validation(RA)
* Validation is done in the `FormControl` class, the specific validator which we wish to use are passed as an argument in an array

app.component.ts:
```
import { Component, OnInit } from '@angular/core';
import { FormControl, FormGroup, Validators } from '@angular/forms';
...
  ngOnInit(): void {

      this.signUpForm = new FormGroup({
        'username': new FormControl(null, Validators.required),
        'email': new FormControl(null, [Validators.required, Validators.email]),
        'sex': new FormControl('Female')
      });
      console.log(this.signUpForm);
  }
...
```
* The `Validators` class needs to be imported from  '@angular/forms'.

## Getting access to controls(RA)
* The controls can be accessed using the `get` method on the `ForGroup` object.
* This can also be done within the template, needed for outputting validation errors to the user.

app.component.html:
```
...
  <form [formGroup]="signUpForm" (ngSubmit)="onSubmit()">
    <div class="form-group">
      <label for="username">Username</label>
      <input
        type="text"
        id="username"
        formControlName="username"
        class="form-control">
      <div class="form-text" *ngIf="signUpForm.get('username').invalid && signUpForm.get('username').touched">Username cant be empty!</div>
    </div>
    <div class="form-group">
      <label for="email">email</label>
      <input
        type="text"
        id="email"
        formControlName="email"
        class="form-control">
        <div class="form-text" *ngIf="signUpForm.get('email').invalid && signUpForm.get('email').touched">Email is invalid!</div>
    </div>
    <div class="radio" *ngFor="let item of sex">
      <label>
        <input
          type="radio"
          [value]="item"
          formControlName="sex">{{ item }}
      </label>
    </div>
    <button class="btn btn-primary" type="submit">Submit</button>
    <div class="form-text" *ngIf="signUpForm.invalid && signUpForm.touched">Form is invalid!</div>
  </form>
...
```

## Grouping Controls(RA)
* Can group controls similarly to that which was done with the passive driven approach.
* To group controls within a `FromGroup`, simply create a `FormGroup` nested within it.

app.component.ts:
```
...
  ngOnInit(): void {

      this.signUpForm = new FormGroup({
        'userData': new FormGroup({
          'username': new FormControl(null, Validators.required),
          'email': new FormControl(null, [Validators.required, Validators.email])
        }),
        'sex': new FormControl('Female')
      });
      console.log(this.signUpForm);
  }
...
```
* The controls contained within this nested `FormGroup` need to be nested within a `<div>` in the template, this `<div>` should contain the `formGroupName` directive. This directive should be bound to the name assigned to the nested `FormGroup` in the TS file.


app.component.html:
```
...
  <form [formGroup]="signUpForm" (ngSubmit)="onSubmit()">

    <div [formGroupName]="'userData'">
      <div class="form-group">
        <label for="username">Username</label>
        <input
          type="text"
          id="username"
          formControlName="username"
          class="form-control">
        <div class="form-text" *ngIf="signUpForm.get('userData.username').invalid && signUpForm.get('userData.username').touched">Username cant be empty!</div>
      </div>
      <div class="form-group">
        <label for="email">email</label>
        <input
          type="text"
          id="email"
          formControlName="email"
          class="form-control">
          <div class="form-text" *ngIf="signUpForm.get('userData.email').invalid && signUpForm.get('userData.email').touched">Email is invalid!</div>
      </div>
    </div>
    
    <div class="radio" *ngFor="let item of sex">
      <label>
        <input
          type="radio"
          [value]="item"
          formControlName="sex">{{ item }}
      </label>
    </div>

    <button class="btn btn-primary" type="submit">Submit</button>
    <div class="form-text" *ngIf="signUpForm.invalid && signUpForm.touched">Form is invalid!</div>

  </form>
...
```
* To access the controls of a nested `FormGroup`, same `get` method is used but need to specify path as shown above.

## Arrays of form controls(RA)
* Angular allows us to create a array of `FormControls` within a `FormGroup`, this is done using `FormArray`. It takes in an array of `FormControls` as its argument.
* To retrieve the controls within this array use `(<FormArray>this.signUpForm.get('hobbies')).controls`, this will return an array of `FormControl` objects.
* Note that this will not however work in an `.component.html` file, will have to call a method to return the array to the file.
* To link an html file with the `FormArray` controls, create a div that has the `formArrayName` directive. Bind this directive with the given name of the `FormArray`.
* Can then iterate over the controls of the array within this div using `ngFor`, again note that a function will have to return the controls here.
* For each control input bind the index of the control to `formControlName`. Note that this has to be done using `[]` because we are passing a variable and not a string.

app.component.ts:
```
...
  ngOnInit(): void {

      this.signUpForm = new FormGroup({
        'userData': new FormGroup({
          'username': new FormControl(null, Validators.required),
          'email': new FormControl(null, [Validators.required, Validators.email])
        }),
        'sex': new FormControl('Female'),
        'hobbies': new FormArray([])
      });
  }

  onSubmit(){
    console.log(this.signUpForm);
  }

  onAddHobby(){
    const control = new FormControl(null, Validators.required);
    (<FormArray>this.signUpForm.get('hobbies')).push(control);
  }

  getHobbies(){
    return (<FormArray>this.signUpForm.get('hobbies')).controls
  }
}
...
```
app.component.html:
```
...
  <div class="mb-3 mt-3" formArrayName="hobbies">
    <h4>Your Hobbies:</h4>
    <button class="btn btn-secondary" type="button" (click)="onAddHobby()">Add New Hobby</button>
    <!-- Display form array and link to signupform -->
    <div *ngFor="let item of getHobbies(); let i = index">
      <input type="text" [formControlName]="i">
    </div>
  </div>

  <button class="btn btn-primary" type="submit">Submit</button>
  <div class="form-text" *ngIf="signUpForm.invalid && signUpForm.touched">Form is invalid!</div>

</form>
...
```
* Can use the `clear` method on a `FormArray` object to remove all the controls in the array.
* https://angular.io/api/forms/FormArray

## Creating Custom validators(RA)
* In the reactive approach we can define our own validators
* This is done by defining a new method and passing it to the `FormControl` object.
* The custom validator function should return a js object if the input is determined to be invalid, this object should contain the error message as key and the value should be a boolean `true`.
* If however the input is determined to be valid then the method should return `null`.

app.component.ts:
```
...
  forbiddenNames = ['King', 'Queen'];
...
  ngOnInit(): void {

      this.signUpForm = new FormGroup({
        'userData': new FormGroup({
          //For the .bind see: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind
          'username': new FormControl(null, [Validators.required, this.forbiddenNamesValidator.bind(this)]),
          'email': new FormControl(null, [Validators.required, Validators.email])
        }),
        'sex': new FormControl('Female'),
        'hobbies': new FormArray([])
      });
      // console.log(this.signUpForm);
  }
...
  forbiddenNamesValidator(control: FormControl): {[s:string]: boolean}{
    let index = this.forbiddenNames.indexOf(control.value);
    if(index !== -1){
      console.log("username forbidden");
      return {"This username is forbidden": true}
    }else{
      //NB function must return null and not {"This username is forbidden": false}
      return null
    }
  }
...
```
* For the `.bind(this)`, see: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind
  

## Using error codes(RA)
* JS will add the object returned by a validator on the individual controls, on the errors object.
* This functionality can therefore be used to give specific error messages to the user.

app.component.html:
```
...
  <form [formGroup]="signUpForm" (ngSubmit)="onSubmit()">

    <div [formGroupName]="'userData'">
      <div class="form-group">
        <label for="username">Username</label>
        <input
          type="text"
          id="username"
          formControlName="username"
          class="form-control">

        <div class="form-text" 
          *ngIf="signUpForm.get('userData.username').invalid && signUpForm.get('userData.username').touched">
          
          <div class="form-text" *ngIf="signUpForm.get('userData.username').errors['required']">
            This field is required!
          </div>
          
          <div class="form-text" *ngIf="signUpForm.get('userData.username').errors['forbidden']">
            This username is forbidden!
          </div>
        
        </div>
      </div>
...
```
* Can simply print form to console and view name of error codes when validation fails

## Creating a custom asynchronous validator(RA)
* Asynchronous validators are used for example, when a HTTP request needs to be sent to a web server to determine if a username is valid or not.
* The validator is again just a method within the component class, that receives a `FormControl` object as its argument and returns a promise or a observable.
* The promise/observable will return a js object containing the error message as the key and its value being true, if the input is determined to be invalid. Else it will return `null`.
* The `FormControl` object receives a asynchronous validator or a array of asynchronous validators as its third argument.
* The class ng-pending is applied to the relevant html tag while the promise/observable id being evaluated.

app.component.ts:
```
...
  ngOnInit(): void {

      this.signUpForm = new FormGroup({
        'userData': new FormGroup({
          'username': new FormControl(null, [Validators.required, this.forbiddenNamesValidator.bind(this)]),
          'email': new FormControl(null, [Validators.required, Validators.email], this.forbiddenEmailValidator)
        }),
        'sex': new FormControl('Female'),
        'hobbies': new FormArray([])
      });
  }
...
  forbiddenEmailValidator(control: FormControl){
    const promise = new Promise((resolve,reject) => {
      setTimeout(() => {
        if(control.value === 'test@test.com'){
          resolve({'forbidden': true});
        }else{
          resolve(null);
        }
      }, 1500);
    });
    return promise;
  }
...
```

## Reacting to Status or value changes(RA)
* Can access the value or status of the `FormGroup` or the `FormControl` using the `valueChanged` and `statusChanged` methods respectively.
* These methods return observables and therefore need to be subscribed to.
* `valueChanged` returns the value object of the form or the value of a specific control.
* `statusChanged` returns the status of the form or the value of a specific control.

app.component.ts:
```
...
  ngOnInit(): void {

      this.signUpForm = new FormGroup({
        'userData': new FormGroup({
          'username': new FormControl(null, [Validators.required, this.forbiddenNamesValidator.bind(this)]),
          'email': new FormControl(null, [Validators.required, Validators.email], this.forbiddenEmailValidator)
        }),
        'sex': new FormControl('Female'),
        'hobbies': new FormArray([])
      });
      
      this.signUpForm.valueChanges.subscribe((value) => {
        console.log("value changes on form: ");
        console.log(value);
      })

      this.signUpForm.get('userData.username').valueChanges.subscribe((value) => {
        console.log("value changes on control: " + value);
      })

      this.signUpForm.statusChanges.subscribe((value) => {
        console.log("status changes on form: " + value);
      })

      this.signUpForm.get('userData.username').statusChanges.subscribe((value) => {
        console.log("status changes on control: " + value);
      })
  }
...
```

## Setting, patching values and resetting(RA)
* The `setValue` and `patchValue` methods are still available on the form in the reactive approach, and are used in the exact same manner.
* The `reset()` method is also available on the `FormGroup` object to reset the form or to reset to the object values passed to it.


app.component.ts:
```
...
  this.signUpForm.setValue({
    userData: {
      username: 'Jeff',
      email: 'Jeff@gmail.com',
    },
    sex: 'Male',
    hobbies: []
  })

  this.signUpForm.patchValue({
    sex: 'Female'
  })
...
```

# Section 17: Using pipes to transform output
## Using and parametrizing pipes
* Pipes are used to transform data that is output to the template. There are many built in types that are available and custom pipes can also be created.
* https://angular.io/api/common#pipes
* Pipes do not change the original data stored in the variable only the way its viewed in the template
* Syntax of pipes in Angular are the same as template injection in Django. 
  
app.component.html:
```
...
<p>{{server.date | date}}</p>
...
```
* Can pass parameters to pipes to configure them, more detail on API page

app.component.html:
```
...
<p>{{server.date | date:'full'}}</p>
...
```
* Can chain pipes, order is important, will be parsed from left to right.


app.component.html:
```
...
<p>{{server.date | date:'full' | uppercase}}</p>
...
```

## Creating a custom pipe
* Firstly a new TS file is created, e.g. `filter.pipe.ts`. Can also use the cli with:
```
ng generate pipe filter
```
* Then export a class within this TS file, the class should implement the PipeTransform interface which forces you to implement the transform method. 
* It should also contain the `@Pipe` decorator, which specifies the name of the pip to be used in the HTML file.
* The transform method takes the value the pipe is being applied to as a parameter, the following parameters represent the user config that is passed
* You must declare the pipe in the declarations in app.module.ts

filter.pipe.ts:
```
import { Pipe, PipeTransform } from "@angular/core";

@Pipe({
    name: 'filter'
})
export class Filter implements PipeTransform{

    transform(value: any, filterString: string, propName: string) {
        if(value.length === 0 || filterString === undefined){
            return value;
        }
        let filterArray = [];
        for(let item of value){
            if(item[propName] === filterString){
                filterArray.push(item);
            }
        }
        return filterArray;
    }
}
```
app.component.html:
```
...
<ul class="list-group">
  <li
    class="list-group-item"
    *ngFor="let server of servers | filter:'stable':'status'"
    [ngClass]="getStatusClasses(server)">
    <span
      class="badge">
      {{ server.status }}
    </span>
    <strong>{{ server.name }}</strong> | {{ server.instanceType | uppercase}} | {{ server.started | date:'full'}}
  </li>
...
```
* As can be seen the filter can be applied to any value being output in the template


## Pure and impure Pipes
* Pipes by default are not rerun when the data they are applied to changes, i.e. updating objects and arrays does not trigger it.
* If it were to rerun every time the data changed, this would cause a huge performance loss.
* This default behavior can be changed by adding the `pure` key to the pipe decorator and assigning it a value of `false`.
* See lecture 249


## The async pipe
* If you are outputting asynchronous data to the template (data returned by a promise or observable, that only updates after sometime), by default angular wont update the data being output when it eventually is updated.
* The async pipe ensures that the data being output is updated as soon as the promise or observable completes.
* Therefore of you are outputting data that is to be returned by a promise or a observable, use the `async` pipe.
 
app.component.ts:
```
...
export class AppComponent {

  appStatus = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('online')
    }, 2000);
  });
...
```
app.component.html:
```
...
      <h3>App Status: {{appStatus}}</h3>
...
```
output in browser:
```
App Status: [object Promise]
```
* Now after adding the `async` pipe, output updates correctly after 2000ms.
  

app.component.html:
```
...
      <h3>App Status: {{appStatus | async}}</h3>
...
```
output in browser:
```
App Status: online
```

# Section 18 - Making HTTP requests
## How does angular interact with backends
* Angular does not connect directly to a SQL or NoSQL database, that means you dont enter your database credentials into the angular app. These would be extremely insecure because anyone can inspect your code because it is a front-end JS application.
  
<img src="./figures/backend.png" alt="alt text" title="image Title" width="500"/>

* Instead Angular makes HTTP requests and gets HTTP responses from a server, the server is defined as an API here. That means it is a REST API or graphQL API (will use REST in this course) you are communicating with.
* Sever side languages like nodeJS and PHP are used to create these APIs.
* Such an API is almost like a website except when you visit its URLs, you are not getting back HTML but instead you get data in the JSON format.
* The code on the server there will be code that interacts with a database.
* In this course we will use a dummy backend, which could be replaced by any real backend.
* https://academind.com/tutorials/building-a-restful-api-with-nodejs
* https://academind.com/tutorials/hide-javascript-code

## HTTP requests
<img src="./figures/HTTPanatomy.png" alt="alt text" title="image Title" width="500"/>

## Backend (firebase) setup
* [Firebase REST API](https://firebase.google.com/docs/database/rest/start?authuser=0 'https://firebase.google.com/docs/database/rest/start?authuser=0')
* Firebase is a complete backend solution provided by google, includes a database as well.
* Simply go to [Firebase](https://console.firebase.google.com/  'https://console.firebase.google.com/') and create a new project.
* Create a real-time database and set it to test-mode. Then set the rules as below.
```
{
  "rules": {
    ".read": "true", 
    ".write": "true",  
  }
}
```
* Will be provided with a URL which you can then send and receive HTTP requests.

## POST & GET requests
* HTTP requests are only sent when they are subscribed to!
## POST:
* To send a http requests to a backend first need to declare the Angular `HttpClientModule` under `imports` in the app.module.ts file.
* Then the `httpClient` needs to be injected into the relevant component TS file.
* `httpClient` has methods for all the http verbs. They all return observables, which will need to be subscribed to in order for the request to be made and the data received/sent.

app.component.ts:
```
...
  constructor(private http: HttpClient) {}

  ngOnInit() {}

  onCreatePost(postData: { title: string; content: string }) {
    // Send Http request
    console.log(postData);
    this.http.post('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json', postData).subscribe((responseData) => {
      console.log(responseData);
    });
  }
...
```
* The first parameter is the API endpoint to which the request is made. 
* Note that we append with `.json`, this is not an angular requirement, this is a firebase requirement.
* The second parameter is the data to be passed, angular will convert this data to JSON format before making request.
* Should note that firebase creates a unique key for each data object stored in the database

## GET:
* The GET request is essentially the same as the POST request above except that we do not pass any data to it.

app.component.ts:
```
...
    this.http.get('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json').subscribe((responseData) => {
      console.log(responseData);
    })
...
```
* Note that these are Angular observables and therefore we do not need to unsubscribe from them.

## Using RxJS operators to transform response data
* Observable operators are a convenient way to transform our response data.
* In the following example a object is retrieved via a GET request to the firebase database. The object has the following structure
```
Object { "-MvOUAYA2R-12lWPi25Y": { content: "this is a test", title: "Test POST" }
```
* The data is then transformed into an array of objects with the unique id as a property of the objects

app.component.ts:
```
...
  private fetchPosts(){
    this.http.get('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json')
    .pipe(map((responseData) => {
      const postArray = [];
      for(const key in responseData){
        // So that we dont output prototype
        if(responseData.hasOwnProperty(key)){
          postArray.push({...responseData[key], id: key})
        }
      }
      return postArray;
    }))
    .subscribe((responseData) => {
      console.log(responseData);
    })
  }
...
```
* The data now has this format:
```
[{ content: "this is a test", title: "Test POST", id: "-MvOUAYA2R-12lWPi25Y" }]
```

## Using types with HttpClient
* Its a good idea to explicitly tell Angular which types a http request will return. This can easily be done because all the http request methods are generic types.
* In the following example a `post.model.ts` file is created which exports an interface defining the structure of the response data.
* This is then given to the http request methods inside `<>`

post.model.ts:
```
export interface Post{
    title: string,
    content: string,
    id?: string
}
```
app.component.ts:
```
...
  private fetchPosts(){
    this.http.get<{[key: string]: Post}>('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json')
    .pipe(map((responseData) => {
      const postArray: Post[] = [];
      for(const key in responseData){
        // So that we dont output prototype
        if(responseData.hasOwnProperty(key)){
          postArray.push({...responseData[key], id: key})
        }
      }
      return postArray;
    }))
    .subscribe((posts) => {
      console.log(posts);
    })
  }
...
```

## Http requests and services
* It is good practice to encapsulate all the code related to http requests into a service and only keep the code that is related to and manages the template within out component file.
* If the component cares about the response of a http request, then subscribe should be done in the component as that data is needed there. If not then subscribe can be done in the service.
* See /home/phoenix/udemy/Angular/section-18-HTTP/sect-18/src/app/post.service.ts for details


## Delete request
* [firebase API requests](https://firebase.google.com/docs/database/rest/save-data?authuser=0 'https://firebase.google.com/docs/database/rest/save-data?authuser=0')
* Done the same as for POST and GET request, delete method takes the URL (API endpoint) of the object to be deleted.
* Again, the request is only made once the method is subscribed to!
* For a firebase backend you specify the 'folder' structure to be deleted e.g.
  * If you wish to delete all objects within the posts object use:
```
 return this.http.delete('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json').subscribe(() => {
   console.log("Delete completed")
 })
```
  * If you wish to delete a specific object within the posts object use:
```
    let URL = 'https://learnangular-cbe61-default-rtdb.firebaseio.com/posts/' + postId + '.json';
    return this.http.delete(URL).subscribe(() => {
   console.log("Delete completed")
 });
```
  * Where postId is the id of the object to be removed.

## Handling errors
### Handling within subscribe
* The `HttpClient` request methods all return observables and therefore one can specify a function to execute when an error occurs (or when complete occurs for whatever reason).
* Can then log error to the console or display to the user via the template

error handling e.g.:
```
...
    this.postService.fetchPost().subscribe({
      next: (posts) => {
        this.isFetching = false;
          console.log(posts);
          this.loadedPosts = posts;
      },
      error: (error) => {
        console.log(error)
        this.errorMess = error.message;
      }
    });
...
```
### Handling with a subject
* This is useful you have multiple places in the application that are interested in the error, or if the subscribe method is within the service itself.
* Simply define a Subject within the service, then when subscribing to the http request method call the subject `next` method to emit the error should an error occur. 
* Can then subscribe at multiple places to the service's subject.
  
post.service.ts:
```
...
export class PostService {

  writeError = new Subject<string>();

  constructor(private http: HttpClient) { }

  savePost(postData: Post){
    console.log(postData);
    this.http.post<{ name: string}>('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json', postData)
    .subscribe({
      next: (responseData) => {
      console.log(responseData);
      },
      error: (error) => {
        this.writeError.next(error.message)
      }
    });
  }
...
```
app.component.ts:
```
...
  errorMess: string;
  errorSub: Subscription;
...
  ngOnInit() {

    this.errorSub =     this.postService.writeError.subscribe((message) => {
      console.log(message);
      this.errorMess = message;
    });
...
  ngOnDestroy(): void {
      this.errorSub.unsubscribe()
  }
...
```
## Catch error operator
* RxJS offers a `catchError` that will call a specified function should an error occur.
* It also offers a `throwError` method that wraps the error in an observable i.e. it will throw an error to the subscriber of the observable.

e.g.:
```
...
  fetchPost(): Observable<Post[]>{
    return this.http.get<{[key: string]: Post}>('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json')
    .pipe(map((responseData) => {
      const postArray: Post[] = [];
      for(const key in responseData){
        // So that we dont output prototype
        if(responseData.hasOwnProperty(key)){
          postArray.push({...responseData[key], id: key})
        }
      }
      return postArray;
    }),
    catchError((errorRes) => {
      //Send to analytics server
      return throwError(() => {
        return errorRes
      });
    }))
  }
...
```
* `catchError` description: It only listens to the error channel and ignores notifications. Handles errors from the source observable, and maps them to a new observable. The error may also be rethrown, or a new error can be thrown to emit an error from the result. Link: [catchError info](https://rxjs.dev/api/operators/catchError 'https://rxjs.dev/api/operators/catchError')


## Setting headers
* Special headers need to be set for e.g. when back-end requires authorization and looks for a authorization header, or you want to set your own content type, or you need to attach your own custom header because your API needs it
* All `HttpClient` methods take in an object as a parameter wherein the header is configured.

post.service.ts:
```
...
import { HttpClient, HttpHeaders } from '@angular/common/http';
...
    return this.http.get<{[key: string]: Post}>('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json', {
      headers: new HttpHeaders({ "Custom-Header": 'custom header content'})
    })
...
```


<img src="./figures/custom-header.png" alt="" width="500">

## Adding query parameters
* The query parameters that can be used are dependant on the API that one interacts with.
* They are set similarly to Http headers above. 
* Simply add the `params` key to the options object, and instantiate a  `HttpParams` object. Must then call `set` method on that to setup the query params.

post.service.ts:
```
...
import { HttpClient, HttpHeaders, HttpParams } from '@angular/common/http';
...
    return this.http.get<{[key: string]: Post}>('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json', {
      headers: new HttpHeaders({ "Custom-Header": 'custom header content'}),
      params: new HttpParams().set('print', 'pretty')
    })
...
```
* To set multiple params must use the `append` method. 
* HttpParams is immutable so one would need to store the result in a new variable.

post.service.ts:
```
...
    let searchParams = new HttpParams().set('print', 'pretty');
    searchParams = searchParams.append('key', 'value');

    return this.http.get<{[key: string]: Post}>('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json', {
      headers: new HttpHeaders({ "Custom-Header": 'custom header content'}),
      params: searchParams
    })
...
```

## Altering the HttpResponse structure
* By default angular will extract the body of the http response, however it is possible to change how angular parses the response.
* Useful if you need access to the response status code or the response headers.
* This is done by configuring the `observe` parameter of the `options` parameter of a `HttpClient` request method.
*  Can pass one of three values: 'body' which returns just the response body, 'response' which returns the full response and 'events' which returns an observable of the raw HttpEvent stream.

post.service.ts:
```
...
  clearAllPosts(){
    return this.http.delete('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json', {
      observe: 'events'
    }).pipe(tap({
      next: (event) => {
        if(event.type === HttpEventType.Sent){
          console.log('The request was sent out over the wire.');
        }
        if(event.type === HttpEventType.Response){
          console.log(event.body);
        }
      }
    }));
  }
...
```
* The `tap` operator of a `pipe` gives access to the observable data without affecting the data passed to subscribe.


## Change the response body type
* Done by configuring the `responseType` parameter of the `options` parameter of a `HttpClient` request method.
* By default it is set to `json`, which instructs angular to parse it to a JS object.
* Other options include `text` and `blob` (which is a file).

post.service.ts:
```
...
    return this.http.delete('https://learnangular-cbe61-default-rtdb.firebaseio.com/posts.json', {
      observe: 'events'
      responseType: 'json'
    })
...
```

## Interceptors
* Before we had to setup the options parameter for every single http request that was sent, interceptors allow you setup this parameter for all http request that are sent
* Useful if you always need an authentication header for authenticating users on every request.
* An interceptor basically steps in before a request is sent and applies the relevant configuration.
* To use first create a new service e.g. `auth-interceptor.service.ts`, then export a class that implements the `HttpInterceptor` interface.
* This interface requires that the `intercept` method be implemented. The interceptor method takes two parameters: a HttpRequest object and a HttpHandler object.
* The HttpHandler object is used to ensure the request process continues after config is done.

auth-interceptor.service.ts:
```
...
import { HttpEvent, HttpHandler, HttpInterceptor, HttpRequest } from "@angular/common/http";
import { Observable } from "rxjs";

export class AuthInterceptorService implements HttpInterceptor{
    intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        console.log("request is on its way");
        return next.handle(req);
    }
}
...
```
* The service needs to be declared in the providers array, but not as done previously, instead do:

app.module.ts:

```
...
@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, FormsModule, HttpClientModule],
  providers: [{
    provide: HTTP_INTERCEPTORS,
    useClass: AuthInterceptorService,
    multi: true
  }],
  bootstrap: [AppComponent]
})
...
```
* This format is just a dependency injection format supported by angular that allows us to register a service (AuthInterceptorService) under a different identifier (HTTP_INTERCEPTORS) and have multiple services under that identifier (multi).
* An example of altering the request with an interceptor is shown below, a additional nonsense header is added. Note that an `HttpRequest` object is immutable and therefore a new request is created and passed to the handler.


auth-interceptor.service.ts:
```
...
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
      console.log("request is on its way");
      const modifiedRequest = req.clone({
          headers: req.headers.append('auth', '1754h543g')
      });
      return next.handle(modifiedRequest);
  }
...
```
* [HttpRequest API](https://angular.io/api/common/http/HttpRequest#clone 'https://angular.io/api/common/http/HttpRequest#clone')


## Response Interceptors
* The `intercept` method returns `Observable<HttpEvent<any>>` and therefore one can use the `pipe` operator to grab the Http response for whatever reason.
* The e.g. below the `tap` operator is used to simply log data to console.

auth-interceptor.service.ts:
```
...
    intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        console.log("request is on its way");
        const modifiedRequest = req.clone({
            headers: req.headers.append('auth', '1754h543g')
        });
        return next.handle(modifiedRequest).pipe(tap({
            next: (event) => {
                if(event.type === HttpEventType.Response){
                    console.log('Response arrived, body data:');
                    console.log(event.body);
                }
            }
        }));
    }
...
```

## Multiple Interceptors
* Multiple interceptors can be used in angular
* The order that one provides them in matters because that will be the order in which they are executed.
* Simply declare all interceptors within the app.module providers array:

app.module.ts:
```
...
@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, FormsModule, HttpClientModule],
  providers: [{
    provide: HTTP_INTERCEPTORS,
    useClass: AuthInterceptorService,
    multi: true
  },
  {
    provide: HTTP_INTERCEPTORS,
    useClass: LoggingInterceptorService,
    multi: true
  }],
  bootstrap: [AppComponent]
})
export class AppModule {}
...
```

# Section 20 - Authentication and Route Protection 
## Intro
* [Firebase Auth API docs](https://firebase.google.com/docs/reference/rest/auth/ 'https://firebase.google.com/docs/reference/rest/auth/') 
* Note: example code in this section is from course project.
* Angular interacts with a backend via a RESTful API or graphQL API, therefore authentication cannot be done using sessions.
* Instead Angular uses JSON web tokens (JWT), see lecture 288 for more info.
* Should always start by creating a new component to handle the authentication, can name it `auth` for e.g.
* This component should contain the logic to grab data from the user with forms and a `auth.service.ts` file to encapsulate the http requests sent to the backend.

<img src="./figures/authentication.png" alt="" width="500">

* [More on JWT](https://jwt.io/ 'https://jwt.io/')

## Preparing the backend
* Note: will use Firebase REST API for authenticating and it requires passwords to have a minimum length of six characters.
* Possible to use any backend to add authentication to our angular app, the backend just needs to offer endpoints one can use to create new users and log users in to get a JWT. 
* We will use firebase as its free and does'nt require us to write server side code.
* Firstly in realtime database set rules as follows, this will ensure that only users that are authenticated can read and write data.

```
{
  "rules": {
    ".read": "auth != null",  // 2022-3-11
    ".write": "auth != null",  // 2022-3-11
  }
}
```
* Possible to setup more sophisticated rules for different resources, see firebase docs for this.
* Then go to the Authentication tab and set sign-in method to email/password, and save.

## Sign-up requests to Firebase API
* [Firebase Auth API (sign-up) docs](https://firebase.google.com/docs/reference/rest/auth/#section-create-email-password 'https://firebase.google.com/docs/reference/rest/auth/#section-create-email-password') 

<img src="./figures/sign-up.png" alt="" width="700">

* `Auth.service.ts` is responsible for signing users up, signing users in and managing the JWT token. 
* The API web key for your project can be found in the projects settings section, https://console.firebase.google.com/project/ng-course-recipe-book-b490c/settings/general

auth.service.ts:
```
...
interface authResponseData{
    idToken: 	string, 
    email: 	string, 	
    refreshToken: string, 	
    expiresIn: 	string ,
    localId: 	string
}
...
    signUp(body: {email: string, password: string}){
        body['returnSecureToken'] = true;
        let url = 'https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=AIzaSyD3JOTc01zKe-EAaMh-6fOU3ZKIQ1Jy15w'; 
        return this.http.post<authResponseData | any>(url, body);
    }
...
```
* Note that the API key is not wrapped with `[]`.

authenticate.component.ts:
```
...
  onSubmit(){
    if(!this.loginMode){
      this.authService.signUp(this.userForm.value).subscribe({
        next: (responseData) => {
          console.log(responseData);
        },
        error: (error: HttpErrorResponse) => {
          this.errorResponse = true;
          this.errorResponseMessage = error.error.error.message;
        }
      });
    }
    this.userForm.reset();
  }
...
```
* Here are some common error codes:
  * EMAIL_EXISTS: The email address is already in use by another account.
  * OPERATION_NOT_ALLOWED: Password sign-in is disabled for this project.
  * TOO_MANY_ATTEMPTS_TRY_LATER: We have blocked all requests from this device due to unusual activity. Try again later.


## Login requests to Firebase API
* [Firebase Auth API (login) docs](https://firebase.google.com/docs/reference/rest/auth/#section-sign-in-email-password 'https://firebase.google.com/docs/reference/rest/auth/#section-sign-in-email-password') 
  
<img src="./figures/login.png" alt="" width="700">


auth.service.ts:
```
...
    signIn(body: {email: string, password: string}){
        body['returnSecureToken'] = true;
        let url = 'https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=AIzaSyD3JOTc01zKe-EAaMh-6fOU3ZKIQ1Jy15w'; 
        return this.http.post<authResponseData>(url, body)
        .pipe(catchError(errorRes => {
            
            let errorResponseMessage = "An unknown error has ocurred!";
            if(!errorRes.error || !errorRes.error.error){
                return throwError(() => errorResponseMessage);
            }

            switch(errorRes.error.error.message){
            case 'EMAIL_NOT_FOUND': errorResponseMessage = "There is no user record corresponding to this email address. The user may have been deleted.";
              break;
            case 'INVALID_PASSWORD': errorResponseMessage = "The password is invalid or the user does not have a password.";
              break;
            case 'USER_DISABLED': errorResponseMessage = "The user account has been disabled by an administrator";
              break;
          }

          return throwError(() => errorResponseMessage);
        }))

    }
...
```
authenticate.component.ts:
```
...
  onSubmit(){
    let authObs: Observable<authResponseData> ;

    this.isLoading = true;
    if(this.loginMode){
      authObs = this.authService.signIn(this.userForm.value);
    }else{
      authObs = this.authService.signUp(this.userForm.value);
    }

    authObs.subscribe({
      next: (responseData) => {
        this.isLoading = false;
        console.log(responseData);
      },
      error: (errorMessage) => {
        this.isLoading = false;
        this.errorResponse = true;
        this.errorResponseMessage = errorMessage;
      }
    });

    this.userForm.reset();
  }
...
```

* Here are some common error codes:
  * EMAIL_NOT_FOUND: There is no user record corresponding to this identifier. The user may have been deleted.
  * INVALID_PASSWORD: The password is invalid or the user does not have a password.
  * USER_DISABLED: The user account has been disabled by an administrator


## Creating and storing the user data
* To store user data a `user.model.ts` file should be created to define the structure of a user object - user class.
* The model will also be used to keep track of wether the users token is still valid.

user.model.ts:
```
export class User{
    constructor(
        public email : string, 
        public id: string,
        private _token: string,
        private _tokenExpirationDate: Date
        ){}

    get token(){
        if(!this._token || new Date() > this._tokenExpirationDate){
            return null;
        }
        return this._token;
    }

    get tokenTimeout(){
      const timeout = (this._tokenExpirationDate.getTime() - Date.now());
      if(timeout > 0){
          return timeout;
      }else{
          return 0;
      }
    }
}
```
* [JS Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date 'https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date')
* Then in `auth.service.ts` a new `Subject` will be instantiated, it will be used to `next` `user` objects when they are created i.e. emit the currently logged in user in this application.
* Then the `tap` operator is passed as a second operator to the `pipe` method, and within it a new `user` object will be created.  


auth.service.ts:
```
...
export class AuthService{

    user = new Subject<User>();

    constructor(private http: HttpClient){}

    signUp(body: {email: string, password: string}){
        body['returnSecureToken'] = true;
        let url = 'https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=AIzaSyD3JOTc01zKe-EAaMh-6fOU3ZKIQ1Jy15w'; 
        return this.http.post<authResponseData>(url, body)
        .pipe(catchError(this.errorParse), tap(resData => this.handleAuth(resData))); 
    }

    signIn(body: {email: string, password: string}){
        body['returnSecureToken'] = true;
        let url = 'https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=AIzaSyD3JOTc01zKe-EAaMh-6fOU3ZKIQ1Jy15w'; 
        return this.http.post<authResponseData>(url, body)
        .pipe(catchError(this.errorParse), tap(resData => this.handleAuth(resData))); 
    }

    private handleAuth(resData: authResponseData){

        const expirationDate = new Date(Date.now() + (+resData.expiresIn * 1000));
        const user = new User(resData.email,
            resData.localId,
            resData.idToken,
            expirationDate);
        this.user.next(user);
    }
...
```
* `Date.now()` Returns the numeric value corresponding to the current time—the number of milliseconds elapsed since January 1, 1970 00:00:00 UTC, with leap seconds ignored.
* Above the expire time is teh time limit in seconds, it is therefore converted to millisecond, added to `Date.now()` and used to instantiate a new `Date` object.
* The `user` subject can now be subscribed to anywhere in the application, it will emit whenever a new user logs in and logic will be added below to emit when the user logs out or token expires.
* This data can then be used to update the UI and enable/disable components accordingly.


## Adding a token to outgoing requests:
* NB: review lecture 302! 
* [AUthenticate with ID token Firebase API](https://firebase.google.com/docs/database/rest/auth?authuser=0#authenticate_with_an_id_token 'https://firebase.google.com/docs/database/rest/auth?authuser=0#authenticate_with_an_id_token')
* When interacting with the Firebase API, the Firebase generated ID token needs to be attached as a query parameter on all requests. See the docs above.
* Firstly the user `Subject` in `auth.service.ts` is replaced with a `BehaviorSubject`
* `BehaviorSubject` is essentially the same as a RxJS `Subject` in that it emits data to all subscriptions to it, the difference being that it gives subscribers immediate access to the previously emitted value even if they have'nt subscribed at the point that value was emitted. 


auth.service.ts:
```
...
 user = new BehaviorSubject<User>(null);
...
```
* This value can then be accessed as usual by subscribing to `BehaviorSubject`.
* The `BehaviorSubject` should be initialized with a starting value.
* Can use the `take` operator within a `pipe`, passing an integer number representing the number of emits that should occur before the unsubscribe automatically occurs.
* The currently active users ID token is needed in the `data-storage.service.ts` file as this is where post, put and get http requests are made.

data-storage.service.ts:
```
...
 getRecipes(){
    //Note that observable is returned here and subscribe therefore has to be done in calling function
    return this.authService.user.pipe(take(1), exhaustMap(user => {
        console.log(user)
        return this.http.get<Recipe[]>('https://ng-course-recipe-book-b490c-default-rtdb.firebaseio.com/recipes/.json', {
        observe: "body",
        params: new HttpParams().set('auth', user.token)
        })
    })
    ).pipe(map((responseData) => {
        //This code adds an ingredient key and assigns it a value of [] for Recipes in the database that do not have an ingredients property which is possible as the ingredients are not required in the form.
        for(let item of responseData){
            if(item['ingredients'] === undefined){
                item['ingredients'] = [];
            }
        }
        return responseData
    }),
    tap((responseData)=>{
        this.recipeService.updateFromServer(responseData);
    }))
}
...
```
* `exhaustMap` is an RxJS operator used to solve a specific problem here. The problem being that we want to return an observable from the `getRecipes()` method, but you cant return an observable while inside a `.subscribe()` method.
* `exhaustMap` waits for the first observable (the user observable) to complete, which will happen after we take the latest user (because of `take(1)`). `exhaustMap` takes in a function which it passes the data from the the user observable after it completes, the function it is given should return an observable.
* `exhaustMap` then maps the observable returned within it to the `.subscribe` method attached to it. Therefore you are subscribing to the observable returned within `exhaustMap` and not the original observable.
* exhaustMap operator is basically a combination of two operators - exhaust and map. The map part lets you map a value from a higher-order source observable to an inner observable stream. The exhaust part than subscribes to an inner observable and passes values down to an observer if there’s no active subscription already, otherwise it just ignores new inner observables.
* [More detail on exhaustMap](https://indepth.dev/reference/rxjs/operators/exhaust-map 'https://indepth.dev/reference/rxjs/operators/exhaust-map')
* The same does not have to be done for fetching the shopping list as `.subscribe` is done within the service itself

data-storage.service.ts:
```
...
  getShoppingList(){
    //Get shopping list
    this.authService.user.pipe(take(1)).subscribe(user => {
      this.http.get<Ingredient[]>('https://ng-course-recipe-book-b490c-default-rtdb.firebaseio.com/shopping-list/.json', {
          observe: "body",
          params: new HttpParams().set('auth', user.token)
      }).subscribe((responseData) => {
          // console.log(responseData);
          if(responseData === null){
              let error = new Error("There are no ingredients in the database!");
              console.log(error);
          }else{
              this.shoppingListService.updateFromServer(responseData);
          }
      })
    })
}
...
```

## Attaching the token with an interceptor
* Since every request sent to the backend needs to have the authentication token it can be added with an interceptor.

auth-interceptor.service.ts:
```
...
import { HttpEvent, HttpHandler, HttpInterceptor, HttpRequest } from "@angular/common/http";
import { Injectable } from "@angular/core";
import { exhaustMap, Observable, take } from "rxjs";
import { AuthService } from "./auth.service";

@Injectable()
export class AuthInterceptorService implements HttpInterceptor{

    // private token: string;

    constructor(private authService: AuthService){}

    intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        console.log("in interceptor")
        
        return this.authService.user.pipe(take(1), exhaustMap(user => {
            console.log("in inner")
            if(user !== null){
                const modifiedRequest = req.clone({params: req.params.append('auth', user.token)});
                return next.handle(modifiedRequest); 
            }else{
                return next.handle(req); 
            }
        }));

        //My method
        // this.authService.user.pipe(take(1)).subscribe({
        //     next: (user) => {
        //         this.token = user.token;
        //     }
        // })
        // let modifiedRequest = req.clone({
        //     params: req.params.append('auth', this.token)
        // });
        // return next.handle(modifiedRequest);
    }
}
...
```
* Note that the `exhaustMap` operator is used here again to return the inner observable. Can also just use the commented out code, will work the same.
* Can remove code implemented in above section now

data-storage.service.ts:
```
...
    getRecipes(){
      return this.http.get<Recipe[]>('https://ng-course-recipe-book-b490c-default-rtdb.firebaseio.com/recipes/.json')
      .pipe(map((responseData) => {
          //This code adds an ingredient key and assigns it a value of [] for Recipes in the database that do not have an ingredients property which is possible as the ingredients are not required in the form.
          for(let item of responseData){
              if(item['ingredients'] === undefined){
                  item['ingredients'] = [];
              }
          }
          return responseData

      }),
      tap((responseData)=>{
          this.recipeService.updateFromServer(responseData);
      }))
    }
...
```

## Adding a logout
* Simply use the `user` `BehaviorSubject` to emit `null`, to set the user to null.
* Therefore the entire application treats the user as unauthenticated because thats the logic we used.


auth.service.ts:
```
...
    signOut(){
        this.user.next(null);
        this.router.navigate(['/authorize']);
    }
...
```


## Adding auto-login
* This ensures that the user token persists if the Angular application restarts (browser reloads).
* Therefore have to store the token in persistent memory and not a JS variable that is wiped when restart occurs.
* In the browser there are different storages, it is not possible to access the local machines file system directly through the browser but one can work with cookies or local storage.
* Local storage is an API exposed by the browser to store simple key-value pairs on the local filesystem controlled by the browser. 
* To store data in the browser local storage use `localStorage.setItem` which has the following definition: `setItem(key: string, value: string): void`. Can use `JSON.stringify()` to convert a JS object to text.
* This is done when a user logs in or signs up.

auth.service.ts:
```
...
    private handleAuth(resData: authResponseData){

      const expirationDate = new Date(Date.now() + (+resData.expiresIn * 1000));
      const user = new User(resData.email,
          resData.localId,
          resData.idToken,
          expirationDate);
      this.user.next(user);
      localStorage.setItem('userData', JSON.stringify(user));
    }
...
```
* A new method `autoLogin` is created and called when the `app.component.ts` file is initialized. 
* This method retrieves the `userDate` from local storage, parses it to a object using `JSON.parse()`, checks if it is null, if not it creates a new user and checks if the token is still valid, if it is it then emits the user using the `user` Subject.

auth.service.ts:
```
...
    autoLogin(){
      const userData: {email: string, id: string, _token: string,_tokenExpirationDate: string } = JSON.parse(localStorage.getItem('userData'));
      console.log(userData);
      if(userData === undefined){
          return;
      }else{
          //Create new user object and determine if token is valid
          const user = new User(userData.email, userData.id, userData._token, new Date(userData._tokenExpirationDate));
          if(user.token != null){
              this.user.next(user);
          }
      }
    }
...
```
app.component.ts:
```
...
export class AppComponent implements OnInit{
  title = 'CourseProject';

  constructor(private authService: AuthService){}

  ngOnInit(): void {
    this.authService.autoLogin();    
  }
...
```

## Adding auto-logout
* Need to implement logic to ensure that once the users token expires they will no longer have access to restricted areas.
* Can call `localStorage.removeItem()` to remove a specific key-value pair.
* In `auth.service.ts`, the `signOut` method is called when the user clicks the logout button. 

auth.service.ts:
```
...
  signOut(){
    this.user.next(null);
    localStorage.removeItem('userData');
    this.router.navigate(['/authorize']);
  }
...
```
* To ensure that a user is logged out after their token expires a `autoLogout` method is implemented.
* This method is called when the user logs in or signs up, the method takes in the session duration and uses it to set a timeout. The timeout calls the `signOut` method above upon expiry.

auth.service.ts:
```
...
  autoLogout(expirationDuration: number){
    this.tokenTimer = setTimeout(() => {
        this.signOut();
    }, expirationDuration);
  }
...
```
* If the application reloads the current timer will be destroyed, therefore in the `autoLogin` method
(which executes when the application is initialized) logic is implemented to determine the remaining time in the session and initializes a new timer by passing the value to `autoLogout`

auth.service.ts:
```
...
    autoLogin(){
      const userData: {email: string, id: string, _token: string,_tokenExpirationDate: string } = JSON.parse(localStorage.getItem('userData'));
      // console.log(userData);
      if(userData === null){
          // console.log(" No user data in local storage")
          return;
      }else{
          //Create new user object and determine if token is valid
          const loadedUser = new User(userData.email, userData.id, userData._token, new Date(userData._tokenExpirationDate));
          if(loadedUser.token != null){
              this.user.next(loadedUser);
              let tokenTimeout = loadedUser.tokenTimeout
              if(tokenTimeout){
                  // console.log("Time left in session:", (tokenTimeout/1000)/60, 'minutes');
                  this.autoLogout(tokenTimeout);
              }else{
                  this.signOut();
              }
          }
      }
    }
...
```

## Adding a Auth guard
* An auth guard is run before a route is loaded, and is used to determine if access should be granted or not.
* It has therefore been implemented to grant/deny access to certain routes depending on the users session status.

auth-guard.service.ts:
```
...
    constructor(private authService: AuthService, private router: Router){}

    canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean | UrlTree | Observable<boolean | UrlTree> | Promise<boolean | UrlTree> {
        return this.authService.user.pipe(take(1), map(user => {
            console.log(user);
            if(user !== null){
                return true;
            }else{
                return this.router.createUrlTree(['/authorize']);
            }
        }));
    }
...
```
* The UrlTree ensures the user is redirected to the login page should access be denied.
* Note that the `tale` operator is used to ensure that the subscription is destroyed after the value is retrieve. This is important as strange behavior can occur if there is an on going subscription in the `CanActivate` method. 


# Section 21: Dynamic components
* Dynamic components are components that are loaded at runtime.
* [Dynamic component loader](https://angular.io/guide/dynamic-component-loader 'https://angular.io/guide/dynamic-component-loader')

## Alert modal component
* To demonstrate how dynamic components work the error message/box that appears when user info is entered incorrectly will be replaced by an dynamically generated overlay.
* The component is created same as before, and can be found here: /home/phoenix/udemy/Angular/section-3-course-project/course-project/src/app/shared/alert.
* Its selector is `app-alert` and is used to replace the error box in the authenticate.component.ts file.

authenticate.component.ts:
```
...
authenticate.component.ts
...
```

## Different methods to load components dynamically
* Can load using `ngIf` as done above.
* The other approach is to use dynamic component loader which was a helper utility that does'nt exist anymore, but the concept can still be used in which a component is created in code i.e. programmatically and then attached manually to the DOM.

<img src="./figures/dynamic-components.png" alt="alt text" title="image Title" width="500">

## Using ngIf
* Note that this is the __recommended__ approach!

alert.component.ts:
```
...
export class AlertComponent implements OnInit {
  
  @Input() message: string;
  @Output() event = new EventEmitter();

  constructor() { }

  ngOnInit(): void {
  }

  onClick(){
    this.event.emit(null);
  }

}
...
```
alert.component.html:
```
...
<div class="backdrop" (click)="onClick()"></div>
<div class="alert-box">
    <p>{{ message }}</p>
    <div class="alert-box-actions">
        <button class="btn btn-primary" (click)="onClick()">Close</button>
    </div>
</div>
...
```
authenticate.component.html:
```
...
<app-alert [message]="errorResponseMessage" *ngIf="errorResponseMessage" (event)="errorReset()"></app-alert>
...
```
authenticate.component.ts:
```
...
  onHandleError(){
    this.errorResponse = false;
    this.errorResponseMessage = null;
  }
...
```

## Second approach
* Start out by declaring a new private method within the component.ts file. This method is responsible for instantiating our component and telling angular where in the html file to display it.
* Note that in this case the method is passed the error message as a parameter.
* The component is created using a `ComponentFactoryResolver` which is a class used by angular to instantiate and manage components. It needs to be injected in. It takes as a parameter the component type to be instantiated, and returns an object of type `ComponentFactory`.

authenticate.component.ts:
```
...
  constructor(private authService: AuthService, private router: Router, private cmpFactoryResolver: ComponentFactoryResolver) { }
...
  private showErrorAlert(message: string){
  const alertCmpFactory = this.cmpFactoryResolver.resolveComponentFactory(AlertComponent);
  }
```
* Angular needs to be told where to place this component in the DOM, to do this it uses a `viewContainerRef` object. 
* The `viewContainerRef` for the desired place in the html file can be acquired by creating a custom directive, injecting `viewContainerRef` in, and placing it on a `<ng-template>` tag in the html file.


placeholder.directive.ts
```
...
import { Directive, ViewContainerRef } from '@angular/core';

@Directive({
  selector: '[appPlaceholder]'
})
export class PlaceholderDirective {

  constructor(public viewContainerRef: ViewContainerRef) { }

}
...
```
authenticate.component.html:
```
...
  <ng-template appPlaceholder></ng-template>
...
```
* Note that the `ng-template` tag should not be placed within a div that already had conditional directive on it i.e. ngIf
* __This applies to Angular versions lower than 9:__ When using this approach angular needs to be made aware that it will need to generate our custom component at some point. Angular knows it will need to create components based on their selector use within the templates, but we are not using a selector for this dynamically generated component.
* Therefore add the `AlertComponent` (our custom component) to the `entryComponents` option under the app.module.ts file.

app.module.ts:
```
...
  bootstrap: [AppComponent],
  entryComponents: [AlertComponent]
})
...
```
* __Again the above code does not apply for Angular versions 9 or higher__

* The directive can now be accessed using `@ViewChild` and specifying the __type__ as a parameter. And through it the `viewContainerRef` for that point in the html file can be accessed.
* `viewContainerRef` has a `createComponent` method which takes a component type as its parameter. It will instantiate a component within the `viewContainerRef` and return a reference to the component.

authenticate.component.ts:
```
...
  @ViewChild(PlaceholderDirective, {static: false}) alertHost: PlaceholderDirective;
...
  onSubmit(){
    let authObs: Observable<authResponseData> ;

    this.isLoading = true;
    if(this.loginMode){
      authObs = this.authService.signIn(this.userForm.value);
    }else{
      authObs = this.authService.signUp(this.userForm.value);
    }

    authObs.subscribe({
      next: (responseData) => {
        this.isLoading = false;
        this.router.navigate(['/recipes']);
      },
      error: (errorMessage) => {
        this.isLoading = false;
        this.showErrorAlert(errorMessage);
      }
    });
...
  private showErrorAlert(message: string){
    const hostVCF = this.alertHost.viewContainerRef;
    hostVCF.clear();
    const componentRef = hostVCF.createComponent(AlertComponent);
  }
```
* Note that `showErrorAlert` needs to be called when a error occurs
* Data-binding and event-binding will however not work in the above case.
* The component reference that was returned has a `instance` method which gives us access to the current instance of the component and its properties.

authenticate.component.ts:
```
...
  private closeSub: Subscription;
...
  private showErrorAlert(message: string){
    const hostVCF = this.alertHost.viewContainerRef;
    hostVCF.clear();
    const componentRef = hostVCF.createComponent(AlertComponent);

    componentRef.instance.message = message;
    this.closeSub = componentRef.instance.event.subscribe(() => {
      this.closeSub.unsubscribe();
      hostVCF.clear();
    })
  }

  ngOnDestroy(): void {
      if(this.closeSub){
        this.closeSub.unsubscribe();
      }
  }
...
```
* Note that this is the only case where an event emitter should be used instead of a subject when data is being transferred between components. 
* Unsubscribe is done within `ngOnDestroy` so that subscription is removed if user changes route without clicking the close button 


# Section 22: Angular modules & optimizing angular apps
[Angular modules doc](https://angular.io/guide/ngmodules 'https://angular.io/guide/ngmodules')
## What are modules:

<img src="./figures/modules.png" alt="alt text" title="image Title" width="800">

## Feature Modules:
* The `app.module` can be split into separate feature modules. This can possibly increase the performance of the application.
* Splitting the `app.module` into feature modules is a prerequisite for performance improvements.
* This is an example of an online store and how the module could be split up.

<img src="./figures/split-modules.png" alt="alt text" title="image Title" width="800">

* i.t.o. the course project, the application can be split up into three different modules: recipes, shopping list and authorization.
* Note that if you import a module into the current module, the current module can only use the components/directives that are declared within `exports` of the module being imported.
* Also all components/directives that are declared in the `declarations` of a module have access to all components/directives/features of the modules declared in the `imports` section of a module.
* Services are however available application wide and only need to be setup once in the app module i.e. `HttpClient`.
* In the following e.g. all recipe related components have been moved to their own module. Recipes module is then declared in the app.module.

recipes.module.ts:
```
...
@NgModule({
    declarations: [
        RecipesComponent,
        RecipeListComponent,
        RecipeItemComponent,
        RecipeDetailComponent,
        SelectRecipeComponent,
        RecipeEditComponent,
    ],
    imports:[
        RouterModule,
        CommonModule,
        ReactiveFormsModule
    ]
})
export class RecipesModule{}
...
```
* The  `BrowserModule` should only be imported in the app module! To get access to `ngIf` and `ngFor` in other modules import `CommonModule`.
* Note that we do not need to export the recipe components because we are not using them the app component or any child component of the app component.

## Adding Routes to feature modules
* Should also split routes when the app.module is split.
* This is done by making a new routing module for the feature module i.e. `recipes-routing.module.ts`, and then including all routes related to the components in the feature module.
* The new routing module is structured the same as in section-11 outsourcing routes, except that under imports the `forChild` of the `RouterModule` is used instead of `forRoot`.
* Angular then knows it should merge the these routes with the routes configured in `forRoot`

recipes-routing.module.ts:
```
...
const recipeRoutes = [
{path: 'recipes', 
    canActivate : [AuthGuardService],
    component: RecipesComponent, 
    children: [
        {path: '', component: SelectRecipeComponent},
        {path: 'detail/:recipeId', component: RecipeDetailComponent, resolve:[RecipeResolverService]},
        {path: 'new', component: RecipeEditComponent},
        {path: 'update/:recipeId', component: RecipeEditComponent, resolve:[RecipeResolverService]}
    ]}];

@NgModule({
    imports: [
        RouterModule.forChild(recipeRoutes)
    ],
    exports: [
        RouterModule
    ]
})
export class RecipesRoutingModule {}
...
```
* This router module is then imported into the `recipes.module.ts` file.
* If there are not enough routes to justify creating a routing module for the feature module then the routes can be included within the feature model.

shopping-list.module.ts:
```
...
@NgModule({
    declarations:[
        ShoppingListComponent,
        ShoppingListEditComponent,
    ],
    imports: [
        FormsModule,
        CommonModule,
        RouterModule.forChild([
            {path: 'shopping-list', component: ShoppingListComponent, canActivate: [AuthGuardService]}
        ])
    ]
})
export class ShoppingListModule {}
...
```

## Shared Modules
* In cases where feature modules share multiple components/directives, it makes sense to place them in a shared module to avoid code duplication.


<img src="./figures/shared-module.png" alt="alt text" title="image Title" width="600">

* Note that you can only import modules into another module i.e. cannot have components/directives under imports.
* Components/directives/pipes can only be declared __once__ within a certain module i.e. cannot have the same directive/component/pipe under `declarations` in two different modules. 

## The Core module
* The core module is a module created by us, in cases where there are many services declared in the providers array of the app module. The core module is then used to hold these services which helps keep the app module lean.
* The recommended approach however is to set `providedIn: 'root'` in the `Injectable` decorator.

<img src="./figures/core-module.png" alt="alt text" title="image Title" width="600">

core.module.ts:
```
import { NgModule } from '@angular/core';
import { HTTP_INTERCEPTORS } from '@angular/common/http';

import { ShoppingListService } from './shopping-list/shopping-list.service';
import { RecipeService } from './recipes/recipe.service';
import { AuthInterceptorService } from './auth/auth-interceptor.service';

@NgModule({
  providers: [
    ShoppingListService,
    RecipeService,
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptorService,
      multi: true
    }
  ]
})
export class CoreModule {}
```

* There is no need to export services.

## Lazy loading
* Now that the app module has been split into feature modules, each route is associated with a certain module, which contains multiple components/directives.
* By default angular loads all the modules and code within regardless of which route is currently being displayed.
*  Lazy loading ensures that only the module (and relevant code) corresponding to the current route is loaded.
*  This can increase performance of the application because initially only a small portion of code is downloaded and parsed. As more areas of the application are accessed, the rest of the code is retrieved and parsed.

<img src="./figures/lazy-loading.png" alt="alt text" title="image Title" width="600">


## Implementing Lazy Loading
* [Lazy Loading angular docs](https://angular.io/guide/lazy-loading-ngmodules 'https://angular.io/guide/lazy-loading-ngmodules')
* Note: Should lazy load sections of the application that the user will potentially not view. e.g. lazy loading the auth page doesn't make sense as the user will always need that code.
* It is important that a feature module needs to register its own routes i.e. within the module itself or import into the feature module.
* To implement lazy loading must include a new path in app-routing.module.ts ( the app modules routing file). The new path should be the root route of a feature module.
* Must then include the `loadChildren` parameter, this parameter is assigned an arrow function that uses the `import` keyword to import the feature module.
* This tells angular to load the specified module only when the specified route is accessed.
* Must then remove this root route from the feature module and replace with `''`.
* __Must__ also remove the feature module which is to be lazily loaded form the app modules imports( uder imports and at the top of the file).

recipe-routing.module.ts:
```
...
const recipeRoutes = [
  //Was initially 'recipes'
  {path: '', 
      canActivate : [AuthGuardService],
      component: RecipesComponent, 
      children: [
          {path: '', component: SelectRecipeComponent},
          {path: 'detail/:recipeId', component: RecipeDetailComponent, resolve:[RecipeResolverService]},
          {path: 'new', component: RecipeEditComponent},
          {path: 'update/:recipeId', component: RecipeEditComponent, resolve:[RecipeResolverService]}
      ]}];

@NgModule({
    imports: [
        RouterModule.forChild(recipeRoutes)
    ],
    exports: [
        RouterModule
    ]
})
export class RecipesRoutingModule {}
...
```
app-routing.module.ts:
```
...
const appRoutes: Route[] = [
    {path: '', redirectTo: '/recipes', pathMatch: 'full'},
    {path: 'recipes', loadChildren: () => import('./recipes/recipes.module').then(m => m.RecipesModule)}
]

@NgModule({
    imports: [
        RouterModule.forRoot(appRoutes)
    ],
    exports: [
        RouterModule
    ]
})
export class AppRoutingModule {

}
...
```
* Note that in the `then` method the class name of the feature module is returned.
* It is important that the imports at the top of the feature modules and app module only contain imports that are used in that module, else angular will bundle imports not used in the module with that module.
* Also need to restart `ng serve` after applying lazy loading. 


## Preloading lazy-loaded code
* pre-loading essentially optimizes lazy-loading, should use then.
* Code that is lazy-loaded can cause delay in application, as certain areas require code to be fetched before it can be shown.
* It is possible to tell angular to pre-load all modules to avoid this delay. This does __not__ make lazy-loading redundant! When lazy-loading is implemented the application is split into bundles and these bundles are loaded as needed. Pre-loading just tells angular to load all bundles from the start.
* This ensures that the application is fast at the start because the initial download bundle is small, but also no delays because the other bundles are also available.
* To preload certain modules must pass a configuration object as a second parameter to the `RouterModule` `forRoot` method (therefore in the app modules routing file). 
* This config object has a preloadingStrategy parameter with two options: PreloadAllModules or NoPreloading (default).

app-routing.module.ts:
```
...
@NgModule({
    imports: [
        RouterModule.forRoot(appRoutes, {preloadingStrategy: PreloadAllModules})
    ],
    exports: [
        RouterModule
    ]
})
...
```
* Is possible to create your own complex preloading strategy, however this is not covered in the course.

## Modules & Services
* Review lecture 335 & 336!
* Note: eager-loaded is when there is no lazy loading present and module has been imported into app module.
* If you pre-load a lazy-loaded code services behave same as lazy-loaded modules.  

<img src="./figures/service.png" alt="alt text" title="image Title" width="800">

## Ahead of time compilation
* ng serve uses the just in time compilation because it is better for development - debugging and fast updating of the running application.
* For production we want to optimize or code for size and therefore want to use ahead of time compilation. 

<img src="./figures/compile.png" alt="alt text" title="image Title" width="800">

* The process of using the ahead of time compiler is the process initially used in deployment. 


To use run in console:
```
ng build --prod
```
* This will compile our angular application using AOT compilation for deployment. Files will then be stored in `dist` directory. These files have then been optimized.


# Section-23: Deployment
## Preparation

<img src="./figures/deploy.png" alt="alt text" title="image Title" width="600">

* Polish and test code is implementing lazy-loading, pre-loading ect.
* A static host is a web server that is capable of serving html, css and JS, but not capable of running any server side language like php or nodeJS.

## Using environment variables
* Built into every project you build with the angular CLI.
* Inside the `environments` directory are two file: `environments.prod.ts` and `environment.ts`.
* When building the project `environment.ts` is replaced with `environments.prod.ts`.
* Inside these files is an object that takes in a config. One parameter that can be used is ... , will use this if your application interacts with a backend using a web API key. The web API key may be different for development vs production and therefore these files will have different values.
* Can however store any value in here that you need in your files.

environments.ts:
```
export const environment = {
  production: false,
  firebaseApiKey: 'AIzaSyD3JOTc01zKe-EAaMh-6fOU3ZKIQ'
};
```
* Same is done inside environment.prod.ts, although the api keys could differ between these files.

auth.service.ts:
```
...
import { environment } from "src/environments/environment";
...
    signUp(body: {email: string, password: string}){
        body['returnSecureToken'] = true;
        let url = 'https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=' + environment.firebaseApiKey; 
        return this.http.post<authResponseData>(url, body)
        .pipe(catchError(this.errorParse), tap(resData => this.handleAuth(resData))); 
    }
...
```

## Deployment on Firebase
* First of all run `ng build --prod` in the cli
* Note that AWS S3 is a popular option for static web hosting.
* Also note that your backend does not have to be on the same server as the one where the angular app will be deployed i.e. Firebase in the course projects case.
* Need to install the firebase CLI [install firebase cli](https://firebase.google.com/docs/cli?authuser=0 'https://firebase.google.com/docs/cli?authuser=0')

run these commands sequentially:
```
sudo npm install -g firebase-tools
firebase login
``` 
* Must then select: Hosting: `Configure files for Firebase Hosting and (optionally) set up GitHub Action deploys`
* Then: `Use an existing project`, and choose your project.
* Then enter in the directory of the build files relative to the project directory. i.e. `dist/course-project`
* Then set as a single-page app - this ensures that all requests that are sent to the server are redirected to `index.html`
* Will then be asked: Set up automatic builds and deploys with GitHub? I believe this links your deployed app to a github repository, and then any further commits to the repository will update the deployed application on the server if chosen to do so within github.
* In this case for simplicity select no
* Then asked File: dist/course-project/index.html already exists. Overwrite? and select no.
* lastly run:
```
firebase deploy
```
* extra: [how-to-fix-broken-routes-after-deployment](https://academind.com/learn/angular/angular-q-a/#how-to-fix-broken-routes-after-deployment 'https://academind.com/learn/angular/angular-q-a/#how-to-fix-broken-routes-after-deployment')


# Section 24: NgRx
* [Official Docs](https://ngrx.io/ "https://ngrx.io/")
* Maintained and developed by part of the angular team.
* Used for state management in bigger angular applications.
* Note: that it is perfectly fine to manage state as done previously using RxJS and services. Should decide for yourself which is better.
* Will use course project for this section.

## What is application state
* Angular compiles to JS bundles that are run in the browser. The data that is used by the application during runtime is stored and managed in memory (RAM). 
* Application state is the data that is stored during runtime and influences what the application displays on the screen. 
* When the application is reloaded the memory is cleared and application state is therefore lost.
* One way to overcome this is to store important data in a backend, this was done in the course project and is known as persistent state.

<img src="./figures/application-state.png" alt="alt text" title="image Title" width="600">


* State is essentially managed through service and components that store data. For smaller applications this is perfectly fine, in larger application it can be difficult to keep track of how changes to one part of the application will affect the state of the application. This is because of the complex links between components and services.
* RxJS already partly addresses this with the use of subjects and observables. 

## What is NgRx:
* [NgRx docs](https://ngrx.io/guide/store 'https://ngrx.io/guide/store')

<img src="./figures/rxjs-issues.png" alt="alt text" title="image Title" width="600">

### Redux:
* Redux is a state management pattern, it is also a library that helps you implement that pattern into any application.

<img src="./figures/redux.png" alt="alt text" title="image Title" width="600">

* With redux the state of the application is stored in a single place, i.e. all the data that determines the state of the application. This data will be categorized/nested.
* The rest of the application receives this data from that single location.
* In redux to change the state a action is dispatched, which is a JS object specifying the changes.
* Redux offers a clean flow of data.
* Can use the redux library, not restricted to angular only. NgRx is just Angular's version of redux.

<img src="./figures/ngrx.png" alt="alt text" title="image Title" width="600">

## Getting started with reducers
* To install first package run
```
npm install --save @ngrx/store
```
* Then restart ng serve

* Will start by defining the Actions for the shopping-list feature.
* Create a new file named 'shopping-list.actions.ts; under the 'store' directory within the feature module.
* This file will contain definitions for all the actions sent to a reducer of the feature module.

shopping-list.action.ts:
```
import { Action } from '@ngrx/store'
import { Ingredient } from 'src/app/shared/ingredient.model';

const ADD_INGREDIENT = 'ADD_INGREDIENT';
export const ADD_INGREDIENTS = 'ADD_INGREDIENTS';
export const UPDATE_INGREDIENT = 'UPDATE_INGREDIENT';
export const DELETE_INGREDIENT = 'DELETE_INGREDIENT';
export const START_EDIT = 'START_EDIT'
export const STOP_EDIT = 'STOP_EDIT'

export class AddIngredient implements Action{
    readonly type = ADD_INGREDIENT;
    constructor(public payload: Ingredient){}
}

export class AddIngredients implements Action{
    readonly type = ADD_INGREDIENTS;
    constructor(public payload: Ingredient[]){}
}

export class UpdateIngredient implements Action{
    readonly type = UPDATE_INGREDIENT;
    constructor(public payload: Ingredient){}
}

export class DeleteIngredient implements Action{
    readonly type = DELETE_INGREDIENT;
}

export class StartEdit implements Action{
    readonly type = START_EDIT;
    constructor(public payload: number){}
}

export class StopEdit implements Action{
    readonly type = STOP_EDIT;
}

export type shoppingListActions = AddIngredient | AddIngredients | UpdateIngredient | DeleteIngredient | StartEdit | StopEdit;
```
* The class implements the `Action` interface which requires it to have a `type` property. The property uses the `readonly` keyword which tells JS that it cannot be altered from outside the class.
* The exported type is used in the reducer to avoid type errors.


* Then move on to implementing a reducer for the shopping-list.
* Create a new file named 'shopping-list.reducer.ts' under the 'store' directory within the feature module .
* Within it we export a function, this function takes two parameters: the current state and the action. 
* This function is called by NgRx

shopping-list.reducer.ts:
```
import { Ingredient } from "../../shared/ingredient.model";
import * as shoppingListActions from './shopping-list.actions'

export interface State{
    ingredients: Ingredient[],
    selectedIngredient: Ingredient,
    selectedIngredientIndex: number
}

export interface AppState{
    shoppingList: State
}

const initialState: State = {
    ingredients: [
        new Ingredient('cheese', 20),
        new Ingredient('onion', 40)
    ],
    selectedIngredient: null,
    selectedIngredientIndex: null
}

export function shoppingListReducer(state = initialState, action: shoppingListActions.AddIngredient){

    let newState = JSON.parse(JSON.stringify(state));

    switch(action.type){
        case shoppingListActions.ADD_INGREDIENT:
            //Course code:
            // return {
            //     ...state,
            //     ingredients: [ ...state.ingredients, action]
            // } 
            newState.ingredients.push(action.payload);
            Object.freeze(newState);
            return newState;

        case shoppingListActions.ADD_INGREDIENTS:
            newState.ingredients.push(...action.payload);
            Object.freeze(newState);
            return newState;

        case shoppingListActions.UPDATE_INGREDIENT:
            newState.ingredients[newState.selectedIngredientIndex].name = action.payload.name;
            newState.ingredients[newState.selectedIngredientIndex].amount = action.payload.amount;
            newState.selectedIngredient = null;
            newState.selectedIngredientIndex = null;
            Object.freeze(newState);
            return newState;
                case shoppingListActions.DELETE_INGREDIENT:
            newState.ingredients.splice(newState.selectedIngredientIndex, 1);
            newState.selectedIngredient = null;
            newState.selectedIngredientIndex = null;
            Object.freeze(newState);
            return newState;

        case shoppingListActions.START_EDIT:
            newState.selectedIngredientIndex = action.payload;
            newState.selectedIngredient = state.ingredients[action.payload];
            Object.freeze(newState);
            return newState;
        
        case shoppingListActions.STOP_EDIT:
            newState.selectedIngredientIndex = null;
            newState.selectedIngredient = null;
            Object.freeze(newState);
            return newState;

        default: 
            return state;
    }
}
```
* `initialState` is the initial data that we use, it is assigned as a default value to the state parameter so that the first time the function is called it is set. 
* The first time the function is called NgRx will pass a initializer action and therefore the `default` case is set to return the `initialState` in this case.
* NB: Very important that when using NgRX that current state must never be altered - it must stay immutable. Therefore we create a copy of the current state and then alter and return that copy.
* `Object.freeze()` freezes an object, other code cannot delete or change its properties. Ensures immutability.
* Note that you should not have asynchronous code or access local storage from within the reducer.

## Setting up the NgRx Store
* Must import and declare the `StoreModule` in the app module
* Must then tell the `StoreModule` which reducers are involved, to do this call the `forRoot` method and pass a JS object defining the reducers involved. The key name is up to us however it should be descriptive as to which feature the reducer belongs to.

app.module.ts:
```
...
import { StoreModule } from '@ngrx/store';
...
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    SharedModule,
    StoreModule.forRoot({shoppingList: shoppingListReducer})
  ],
...
```

## Selecting a state
* Will alter 'shopping-list.component.ts' to use the reducer to fetch the shopping-list state i.e. fetch the ingredients array.
* Firstly inject the `Store` into the file. `Store` is a generic type and therefore must specify the type, which will be the key we specified in the app module and instead of its value being the reducer we specify the type that the reducer returns.
* An array of name 'ingredients' is declared within 'shopping-list.component.ts' its type is an observable. In `ngOnInit` the `store.select()` method is called, this method takes in the key corresponding to the reducer for this feature module and returns an observable.

shopping-list.component.ts:
```
...
import { Store } from '@ngrx/store'
...
export class ShoppingListComponent implements OnInit {

  itemSelectedIndex:number;
  ingredients: Observable<{ingredients: Ingredient[]}>;

  constructor(private store: Store<{shoppingList: {ingredients: Ingredient[]}}>) { }

  ngOnInit(): void {
    this.ingredients = this.store.select('shoppingList');
  }
...
```
* Since the ingredients property is now of type observable, we cannot loop through it as done previously in the html file.
* The solution is to use the `async` pipe in the html file.

shopping-list.component.ts:
```
...
  <ul class='list-group'>
      <a 
          class='list-group-item' 
          style='cursor:pointer'
          (click)="ingredientSelected(i)"
          [ngClass]="{'active': (itemSelectedIndex === i)}"
          *ngFor='let item of (ingredients | async).ingredients; let i = index'>
          {{item.name}} ({{item.amount}})
      </a>
  </ul>
...
```
* Note that if you needed that state anywhere else besides the template, can just subscribe to the observable returned by `store.select()`.

e.g.
```
 this.store.select('shoppingList').subscribe()
```
* Angular should clear the above subscription for you but to be safe should do it manually i.e. unsubscribe in `ngOnDestroy`

## Dispatching actions

* Actions should be dispatched where ever the state should be altered i.e. anywhere where the ingredients array is altered in this case.
* To dispatch an action to a reducer must first instantiate a new action and then use the `dispatch` method of the injected `Store` to dispatch the action.

shopping-list-edit.component.ts:
```
...
import { Store } from '@ngrx/store';
import * as shoppingListActions from '../store/shopping-list.actions';
import * as fromShoppingList from '../store/shopping-list.reducer';
...
  export class ShoppingListEditComponent implements OnInit, OnDestroy {
...
  constructor(private shoppingListService: ShoppingListService, private store: Store<fromShoppingList.AppState>) { }
...
  onSubmit(){
    let ingredient = new Ingredient(this.ingredientForm.value['ingredientName'], this.ingredientForm.value['ingredientAmount']);

    if(!this.editMode){
      let action = new shoppingListActions.AddIngredient(ingredient);
      this.store.dispatch(action);
    }else{
      let action = new shoppingListActions.UpdateIngredient(ingredient);
      this.store.dispatch(action);
      this.store.dispatch(new shoppingListActions.StopEdit());
      this.editMode = false;
    }
    this.ingredientForm.reset();
  }
...
```

## One Root State
* Once multiple reducers are implemented for each feature module it is important to create a root state
* To do this declare a 'store' folder in the app directory and create `app.reducer.ts` file within it.
* The `app.reducer.ts` should declare the `AppState` interface within in.
* Should then export a constant of type `ActionReducerMap` which can be imported into the app module and passed to the `storeModule.forRoot()` as a parameter.

app.reducer.ts:
```
import * as fromShoppingList from 'src/app/shopping-list/store/shopping-list.reducer';
import * as fromAuthenticate from 'src/app/authenticate/store/auth.reducer';
import { ActionReducerMap } from '@ngrx/store';

export interface AppState {
    shoppingList: fromShoppingList.State,
    auth: fromAuthenticate.State
}

export const appReducer: ActionReducerMap<AppState> = {
    shoppingList: fromShoppingList.shoppingListReducer,
    auth: fromAuthenticate.authReducer
}
```
app.module.ts:
```
...
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    SharedModule,
    StoreModule.forRoot(appReducer)
  ],
...
```

## Important note on Actions:
* Lecture 365
* When the `StoreModule` is initialized a initializer action is sent to all reducers
* When an action is dispatched using the `Store.dispatch()` method, the action given is sent to all the reducers specified in the `StoreModule.forRoot` parameter (of type `ActionReducerMap`).
* This is why it is important to always have a default state that returns the current state in all reducers, otherwise the current state corresponding to a specific reducer will be dropped when an action is dispatched to a different reducer.
* Since actions are dispatched to all reducers it is __required that all action identifiers be unique across the entire application__.
* A good way to enforce this uniqueness in larger applications is to prefix the action identifier with the feature name followed by a description of what it does e.g.:

shopping-list.actions.ts:
```
...
export const ADD_INGREDIENT = '[Shopping List] ADD_INGREDIENT';
export const ADD_INGREDIENTS = '[Shopping List] ADD_INGREDIENTS';
export const UPDATE_INGREDIENT = '[Shopping List] UPDATE_INGREDIENT';
export const DELETE_INGREDIENT = '[Shopping List] DELETE_INGREDIENT';
export const START_EDIT = '[Shopping List] START_EDIT';
export const STOP_EDIT = '[Shopping List] STOP_EDIT';
export const UPDATE_FROM_SERVER = '[Shopping List] UPDATE_FROM_SERVER';
...
```

## NgRx Effects
* Side effects are parts in your code where you run some logic that is important for your application but not so important for the immediate update of the state.
* NgRx has a library to help handle effects.
* To install run:

```
npm install --save @ngrx/effects
```
* NgRx effects can for e.g. be used to replace services that are used to handle HTTP requests and storing to local storage. 
* This is however not compulsory, can handle those things in services and use HgRx to handle the  application state.

### Defining an Effect
* Start by creating a new file within the feature modules store folder e.g. 'auth.effects.ts'
* This file should export a class with a name of your choosing, the class should inject `Actions` from `@ngrx/effects`.
* The class therefore needs the `@Injectable()` decorator so that things can be injected into this class i.e. `Actions` & `HttpClient`.
* `Actions` is an observable that will give you access to all dispatched actions so that you can react to them (differently to how the reducer reacts). The idea is that you do stuff that does not affect the application state but however needs to be done when an action is dispatched, therefore leaving code in reducer to just manage the state.
* Can therefore have asynchronous code here that dispatches an action when it completes.  
* Effects are defined as properties within the class, their names should be be descriptive of the effect they handle. They are created by assigning the `createEffect()` method to them.
* This method takes an arrow function that should return the `Actions` observable (no need to subscribe NgRx will do that). 
* Then add a `pipe` with the ngrx `ofType` operator. This operator takes the action you wish to handle as its argument, and will essentially check if the dispatched action is the same as given argument. If it is then angular will continue with the observable chain else it wont trigger the effect.
* Note that `ofType` operator can be given multiple actions id you wish to run the same code for multiple actions.

auth.effects.ts:
```
export interface authResponseData{
    idToken: 	string, 
    email: 	string, 	
    refreshToken: string, 	
    expiresIn: 	string ,
    localId: 	string,
    registered?: boolean
}

@Injectable()
export class AuthEffects {
    
  authLogin = createEffect(() => {
      return this.actions$.pipe(
          ofType(authActions.LOGIN_START),
          switchMap((authData: authActions.LoginStart) => {

              let url = 'https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=' + environment.firebaseApiKey; 
              return this.http.post<authResponseData>(
                  url, 
                  {email: authData.payload.email, password: authData.payload.password, returnSecureToken: true}
              )
              .pipe(map(resData => {
                  const expirationDate = new Date(Date.now() + (+resData.expiresIn * 1000));
                  const user = new User(resData.email,
                      resData.localId,
                      resData.idToken,
                      expirationDate);
                  return new authActions.AuthenticateSuccess(user);
              }),catchError((errorRes: HttpErrorResponse) => {

                //Code to assign errorResponseMessage
                ...
                return of(new authActions.AuthenticateFail(errorResponseMessage));
              });
          })
      );
  });
  
  constructor(private actions$: Actions, private http: HttpClient){}
}

```
* Note that the `$` is just a convention in the docs, to identify an observable.
* The `switchMap` operator allows us to create a new observable by using as its input, the data emitted by the observable that it is applied to. [switchMap](https://rxjs.dev/api/operators/switchMap 'https://rxjs.dev/api/operators/switchMap')
* Since `switchMap` returns an observable, any operators applied to it need to return an observable i.e. `catchError`.
* __An effect by default should return a new action when it is done.__
* Note that if you use the `catchError` operator on an observable and the observable throws an error, then logic in the function given to the `catchError` operator will be executed. __The original observable that threw the error is completed after throwing an error__, this means the observable chain dies with the error. This is problematic for effects because an effect should never die for the duration of application runtime, or effect will essentially be unable to react to the appropriate dispatched action. 
* Thus __do not__ place a `catchError` operator on the outer observable i.e. the `actions$` observable, rather handle the errors that occur on observables inside the outer observable.
* It is therefore __very__ important that any operators applied to the inner observable (i.e. the observable returned by `switchMap`) return non erroneous observables, as this will cause an error in the outer observable.
* We thus use the `of` operator which is a utility function for creating a new non erroneous observable. Note that the `map` operator will wrap the return value in an observable and therefore `of` operator is not needed.
* The action returned at the end of the observable chain is automatically dispatched by NgRx i.e. `new authActions.AuthenticateSuccess(user)`.

auth.actions.ts:
```
...
export const LOGIN_START = '[Authenticated] LOGIN_START'
...
export class LoginStart implements Action{
    readonly type = LOGIN_START;
    constructor(public payload: {email: string, password: string}){}
}
...
export type authActions = LoginStart|  AuthenticateSuccess | Logout;
```
* __Must__ register the `EffectsModule` after the `StoreModule` in the app module, the `forRoot` method should also be called on this module and passed and array of all the effect classes in the application.

app.module.ts:
```
...
    StoreModule.forRoot(appReducer),
    EffectsModule.forRoot([AuthEffects])
...
```
* Note that it is not always necessary for a effect to return an observable that resolves to an action which can then be dispatched by NgRx. In this case NgRx should be notified by passing a config object as a second parameter to `createEffect`. This object should contain a dispatch property which is set to false e.g.:

auth.effects.ts:
```
...
  authSuccess = createEffect(() => {
    return this.actions$.pipe(
        ofType(authActions.AUTHENTICATE_SUCCESS),
        tap(() => {
            this.router.navigate(['/']);
            })
        );
  }, {dispatch: false});
...
```
* Also note above that it is possible to use the router to perform navigation from within an effect. This should be done in an effect because it is an action that does not affect the state of the application but is important for UI.
* It appears that when an action is dispatched the corresponding code in the reducer runs first and then the code in the effect, given that code reducer and effect are defined for the given action.


## Using the store dev-tools
* There is a firefox extension that allows you to view actions being dispatched, and the state of the application in real time.
* To install extension visit: [redux dev-tools](https://addons.mozilla.org/en-US/firefox/addon/reduxdevtools/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search 'https://addons.mozilla.org/en-US/firefox/addon/reduxdevtools/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search')
* After that run the following in your project using the CLI:

```
npm install --save-dev @ngrx/store-devtools
```
* The `--save-dev` tells node this is a development only dependency.
* Then in the app component import and declare the `StoreDevtoolsModule`. The `instrument` method is called on it and an object is passed as its parameter. This object essentially tells Angular to only log when in production mode (due to `environment.production`);

app.component.ts:
```
...
import { StoreDevtoolsModule } from '@ngrx/store-devtools';
...
  imports: [
...
    StoreDevtoolsModule.instrument({logOnly: environment.production})
...
```
* Can now access the devtools in the browser by inspecting and clicking the redux tab.
* Next can install a router tool to view routing information in the browser.

run:
```
npm install --save @ngrx/router-store
```
* Then add following code to app component:

app.component.ts:
```
...
import { StoreRouterConnectingModule } from '@ngrx/router-store';
...
  imports: [
...
    StoreRouterConnectingModule.forRoot()
...
```

## Listening to dispatched actions anywhere 
### Dispatching actions in a resolver
* This is not a section from the course but is a application note from the course project.
* When implementing a resolver to apply to certain navigation paths, the resolver needs to return a observable or a promise as a http request.
* The resolver will wait for the observer/promise to complete before it loads the route, because this means the data has been fetched and loaded.
* If the fetching of data is being done by an action or an effect, we cannot simply return the action because the resolver expects an observable/promise.
* If the dispatched action is returned, the resolver will resolve instantly and the route will be loaded before the data has been loaded correctly.
* The solution is to dispatch the action that loads the relevant data and then wait for the effect that is triggered by the action to complete.
* To do this inject `Actions` from `@ngrx/effects` into the class. And then return the `Actions` observable set up to fire when the action after the action we are interested in is dispatched. In this case `UPDATE_FROM_SERVER` fires after `FETCH_DATA`  which is the action we want.

recipe.effect.ts:
```
...
  fetchRecipes = createEffect(() => {
      return this.actions$.pipe(
          ofType(recipeActions.FETCH_RECIPES),
          switchMap(() => {

              return this.http.get<Recipe[]>('https://ng-course-recipe-book-b490c-default-rtdb.firebaseio.com/recipes/.json')
              .pipe(map((responseData) => {
                  //This code adds an ingredient key and assigns it a value of [] for Recipes in the database that do not have an ingredients property which is possible as the ingredients are not required in the form.
                  for(let item of responseData){
                      if(item['ingredients'] === undefined){
                          item['ingredients'] = [];
                      }
                  }
                  return new recipeActions.UpdateFromServer(responseData);               
              }))
          })
      )
  });
...
```
recipe-resolver.service.ts:
```
...
@Injectable({ providedIn: 'root' })
export class RecipeResolverService implements Resolve<Recipe[]> {
  constructor(
    private dataStorageService: DataStorageService,
    private recipesService: RecipeService,
    private store: Store<fromApp.AppState>,
    private actions$: Actions
  ) {}

  resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot) {
  
    this.store.dispatch(new recipeActions.FetchRecipes());
    return this.actions$.pipe(
      ofType(recipeActions.UPDATE_FROM_SERVER),
      take(1)
    )
      
  }
}
...
```
* The take operator is used so that value is taken once and then subscription is destroyed i.e. no ongoing subscription.


## Accessing State from within an Effect
* To access the application state from within the an effect, can make use of the `withLatestFrom` observable operator.
* The `withLatestFrom` operator allows us to merge a value from another observable into this observable. Combines the source Observable with other Observables to create an Observable whose values are calculated from the latest values of each, only when the source emits. [rxjs withLatestFrom](https://rxjs.dev/api/operators/withLatestFrom "https://rxjs.dev/api/operators/withLatestFrom")
* `withLatestFrom` takes an observable as its parameter. It then basically passes the data received by that observable to the next operator listed.
* The next listed observable operator will then take an array as its input, the first value being the data received by the source observable (the action data in the below e.g.), and the second being the data received by the observable passed to `withLatestFrom`.

recipe.effects.ts:
```
...

  storeRecipes = createEffect(() => {
      return this.actions$.pipe(
          ofType(recipeActions.STORE_RECIPES),
          withLatestFrom(this.store.select("recipes")),
          switchMap(([actionData, recipeState]) => {
              let url = 'https://ng-course-recipe-book-b490c-default-rtdb.firebaseio.com/recipes/.json';
              return this.http.put<{[key: string] : string}>(url, recipeState.recipes, {
                  observe: 'body'
              })
          })
      )
  }, {dispatch: false});
...
```
## Alternative NgRx Syntax
* The NgRx team also released an alternative syntax for creating actions, reducers, effects etc.
* The approach and setup shown in this course is a bit more verbose (which actually has the advantage of seeing more of the things that go on under the hood). Exploring the alternative, a bit shorter syntax might therefore be an interesting next step.
* see '/home/phoenix/udemy/Angular/section-24-ngrx/ngrx-create-functions-syntax.zip' for details.
* Docs also cover this new syntax.

# Angular Universal
* Allows you to pre-render your angular app on the server, i.e. pre-render the pages your users visit on the server and then pass the rendered html code( with the angular JS imports included) to the browser.
* This is useful for cases where the user has a slow internet connection.
* Also helps search engines index your website better as it allows them to scan your initial page already rendered.

## Adding angular universal
* To install run:

```
ng add @nguniversal/express-engine --clientProject CourseProject
```
* Note that 'CourseProject' is found in the `angular.json`


