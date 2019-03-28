## Initialize a Angular App
`ng new APP-NAME`
* create new app, installs necessary config files
cd APP-NAME

`ng serve --open`
* The page you see is the application shell. The shell is **controlled by an Angular component named AppComponent**.
* Components are the fundamental building blocks of Angular applications. They display data on the screen, listen for user input, and take action based on that input.

**Creating a First Component**
* this is already generated, it is the root component
```app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'Ryans Angular app';
}
```
```app.component.css
/* Application-wide Styles */
h1 {
  color: #369;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 250%;
}
```
double curly braces of interpolation to display the app title
```app.component.html
<h1>{{title}}</h1>
```

## Hero Editor

**Creating a component with the CLI, adding to root component**
While in */Users/ryagomes/Desktop/Classes/angularTutorial/tutorial/angular-tour-of-heroes*
* `ng generate component heroes`
* The CLI creates a new folder, src/app/heroes/, and generates the four files of the HeroesComponent.

intitial heroes.component.ts
```ts

import { Component, OnInit } from '@angular/core';

// @Component is a decorator function that specifies the Angular metadata for the component.
    // selector— the component's CSS element selector, html elemnt
    // templateUrl— the location of the component's template file.
    // styleUrls— the location of the component's private CSS styles
@Component({
  selector: 'app-heroes',
  templateUrl: './heroes.component.html',
  styleUrls: ['./heroes.component.css']
})

// Always export the component class so you can import it elsewhere ... like in the AppModule.

export class HeroesComponent implements OnInit {

  constructor() { }

  // The ngOnInit is a lifecycle hook. Angular calls ngOnInit shortly after
  // creating a component. It's a good place to put initialization logic.
  ngOnInit() {
  }
  //this adds a hero property
  hero = 'TheHero';

}
```
see the content of the hero property in the html
heroes.component.html
```html
{{hero}}
```
need to place it in the root html
app.component.html
```html
<h1>{{title}}</h1>
<app-heroes></app-heroes>
```

**Creating a Hero Class**
create /src/app/hero.ts
```ts
export class Hero {
  id: number;
  name: string;
}
```
import it to the component ts, and add it to what's exported
heroes.component.ts
```ts
import { Component, OnInit } from '@angular/core';
import { Hero } from '../hero';


@Component({
  selector: 'app-heroes',
  templateUrl: './heroes.component.html',
  styleUrls: ['./heroes.component.css']
})
export class HeroesComponent implements OnInit {

  hero: Hero = {
    id: 1,
    name: 'Windstorm'
  };
  constructor() { }

  ngOnInit() {
  }


}
```

update the binding in html template to show the input data
heroes.component.html
```html
<h2>{{hero.name | uppercase}} Details</h2>
<div><span>id: </span>{{hero.id}}</div>
<div><span>name: </span>{{hero.name}}</div>
```
* uppercase is a pipe built into Angular for formatting, https://angular.io/guide/pipes



**Data Binding**
heroes.component.html
```html
<h2>HeroName:: {{hero.name}} Details</h2>
<div><span>id: </span>{{hero.id}}</div>
<div>
  <label>name:
    <input [(ngModel)]="hero.name" placeholder="name">
  </label>
</div>
```
* *[(ngModel)]* is Angular's two-way data binding syntax.
binds the hero.name property to the HTML textbox...data can flow in both directions
* from the hero.name property to the textbox
* from the textbox back to the hero.name.


**AppModule**
* root level **src/app/app.module.ts** was initialized when it created the project. This is where you opt-in to the FormsModule.
```ts
// need this to make the data binding shown above work
import { FormsModule } from '@angular/forms'; // <-- NgModel lives here
```



## Displaying a list
**Need some sample data**
* would ideally come from a server
* for now put in `src/app/sampledata/mock-heroes.ts`
```ts
import { Hero } from './hero';

export const HEROES: Hero[] = [
  { id: 11, name: 'Mr. Nice' },
  { id: 12, name: 'Narco' },
  { id: 13, name: 'Bombasto' },
  { id: 14, name: 'Celeritas' },
  { id: 15, name: 'Magneta' },
  { id: 16, name: 'RubberMan' },
  { id: 17, name: 'Dynama' },
  { id: 18, name: 'Dr IQ' },
  { id: 19, name: 'Magma' },
  { id: 20, name: 'Tornado' }
];
```

* import them in in the heroes component
* src/app/heroes/heroes.component.ts (import HEROES)
* init default data
```ts
import { HEROES } from '../mock-heroes';

export class HeroesComponent implements OnInit {

  heroes = HEROES;
}
```

* use the **\*ngFor** directive, its the **repeater directive** - repeats the host element for each element in a list
* put in the html that is repeated in heroes.component.html
* add css to style
```html
<h2>My Heroes</h2>
<ul class="heroes">
  <li *ngFor="let hero of heroes">
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>
```


### Click event
* not want to display hero details when it is clicked
* add a click event to heroes.component.html
    ```html
    <li *ngFor="let hero of heroes" (click)="onSelect(hero)">
    ```
* onSelect() is a function that will be a part of the heroes component
* heroes.component.ts
    * here we previously had a default hero
        ```ts
        hero: Hero = {
                id: 1,
                name: 'Windstorm'
        };
        ```
    * rename this to *selectedHero* which has no default value, and write the **onSelect()** function
        ```ts
        selectedHero: Hero;
        onSelect(hero: Hero): void {
          this.selectedHero = hero;
        }
        ```
* the heroes.component.html still refers to it as **hero** so update to **selectedHero**
    ```html
    <h2>{{selectedHero.name | uppercase}} Details</h2>
    <div><span>id: </span>{{selectedHero.id}}</div>
    <div>
      <label>name:
        <input [(ngModel)]="selectedHero.name" placeholder="name"/>
      </label>
    </div>
    ```

#### Styling a clicked item
* can apply a css class when something happens, in this case when it is===selectedHero, put a css class to show it's picked
```html
<li *ngFor="let hero of heroes"
  [class.selected]="hero === selectedHero"
  (click)="onSelect(hero)">
  <span class="badge">{{hero.id}}</span> {{hero.name}}
</li>
```

#### Hiding when not needed
* this is still broken, when the browser reloads, the page isn't displayed properly. Opening JS console shows an error like
`HeroesComponent.html:3 ERROR TypeError: Cannot read property 'name' of undefined`
* this means the selectedHero was initialized as null and its trying to get null's data to put on the html
* wrap the html that has changing content in **\*ngIf** - this will remove the wrapped section from DOM if the value it's looking at (selectedHero here) is undefined
    *  when user clicks it has a value and renders properly
```html
<div *ngIf="selectedHero">

  <h2>{{selectedHero.name | uppercase}} Details</h2>
  <div><span>id: </span>{{selectedHero.id}}</div>
  <div>
    <label>name:
      <input [(ngModel)]="selectedHero.name" placeholder="name"/>
    </label>
  </div>

</div>
```


## Master/Detail Components
* right now the hero list and detail is in one component, want to separate to make manageable
`ng generate component hero-detail`

* take the bottom of the hero component template which lists the details and put that in the hero detail component html
* hero-detail.component.html
    ```html
    <div *ngIf="currHero">

      <h2>{{currHero.name | uppercase}} Details</h2>
      <div><span>id: </span>{{currHero.id}}</div>
      <div>
        <label>name:
          <input [(ngModel)]="currHero.name" placeholder="name"/>
        </label>
      </div>

    </div>
    ```
* also renamed **selectedHero** to **currHero** as it can refer to any hero when the component is reused

* in the hero-detail.component.ts
    * import the hero model
    ```ts
    import { Hero } from '../models/hero';
    ```
    * the **currHero** property must be prefaced with **@Input** since the external HeroesComponent will bind to it (pass data into it) like this
        * in the hero parent component html
        `<app-hero-detail [currHero]="selectedHero"></app-hero-detail>`
        * in hero details annotation
        ```ts
        @Input() currHero: Hero;
        ```
    * import Input too
    `import { Component, OnInit, Input } from '@angular/core';`

### Parent Child one way data binding
* now The **parent HeroesComponent** will sending the **child HeroDetailComponent** a new hero to display whenever the user selects
    * because of this which will be added to the bottom of heroes.component.html
    ```html
    <app-hero-detail [currHero]="selectedHero"></app-hero-detail>
    ```
    * [currHero]="selectedHero" is an Angular property binding.
        * one way data binding from the selectedHero property of the HeroesComponent to the currHero property of the target element, which maps to the currHero property of the HeroDetailComponent

## Services
* create a HeroService that all application classes can use to get heroes
* rely on **Angular dependency injection** to inject it into the HeroesComponent constructor
* Services are a great way to **share information among classes that don't know each other**.
* You'll create a MessageService and inject it in two places:
    * in HeroService which uses the service to send a message.
    * in MessagesComponent which displays that message.

* create service `ng generate service hero`
* created **hero.service.ts**

* **@Injectable()** 0 marks class as one participating in DI system - HeroService will provide an injectable service, can also have injected dependencies


### Providing the service
* make service available to DI system - register a **provider**
    * provider can create or deliver as service...here it instantiate HeroService class to provide service
    * registering it with an injector, which is the object that is responsible for choosing and injecting the provider where it is required
        * default registers provider with root injector
        ```ts
        @Injectable({
          providedIn: 'root',
        })
        ```
        * root provider -creates single shared instance of the service, inject wherever needed


### Using Service in a component
* in heroes.component.ts
    * import hero service
        `import { HeroService } from '../hero.service';`
    * before, was setting heroes as default `heroes = HEROES`
        * change to just declare type `heroes : Hero[];`
    * inject HeroService in constructor
        `constructor(private heroService: HeroService) { }`
        * defines private HS property when component created
    * function to get Heroes
        ```ts
            getHeroes(): void {
              this.heroes = this.heroService.getHeroes();
            }
        ```
    * call in ngOnInit - so called **after constructing**
        ```ts
        ngOnInit() {
            this.getHeroes()
        }
        ```

### Asynchronous data vs synchronous
    * right now, **HeroService.getHeroes()** has synchronous signature and heroes component consumes that result synchronously
    ```ts
    getHeroes(): void {
        this.heroes = this.heroService.getHeroes();
    }
    ```
    * won't work in a real app bc when call a service it will wait for a server response - **asynchronous** - getHeroes() cannot return immediately
    * HeroService.getHeroes() must have an asynchronous signature - **her it will return an objservable**

### Observeable from RxJS - Angular HttpClient
* Angular's HttpClient methods return RxJS Observables
* in hero.service.ts import observeable `import { Observable, of } from 'rxjs';`
* make getHeroes() return observable
    ```ts
    getHeroes(): Observable<Hero[]> {
      return of(HEROES);
    }
    ```
* in HeroesCompoent, subscribe to the Service's getHeroes response
    * The previous version assigns an array of heroes to the component's heroes property, synchronously, as if the server could return heroes instantly - wont work on remote requests
    ```ts
    getHeroes(): void {
      this.heroes = this.heroService.getHeroes();
    }
    ```
    * The new version waits for the Observable to emit the array of heroes— subscribe passes the emitted array to the callback - sets the component's heroes property.
    ```ts
    getHeroes(): void {
      this.heroService.getHeroes()
          .subscribe(heroes => this.heroes = heroes);
    }
    ```

## Show Messages
* add a MessagesComponent that displays app messages at the bottom of the screen.
* create an injectable, app-wide MessageService for sending messages to be displayed
* inject MessageService into the HeroService
* display a message when HeroService fetches heroes successfully.

* create `ng gereate component`
* show in app.component.html
    ```html
    <h1>{{title}}</h1>
    <app-heroes></app-heroes>
    <app-messages></app-messages>
    ```
* message service `ng generate service message`
    * message.service.ts
        ```ts
        export class MessageService {

            messages: string[] = [];

            add(message: string) {
              this.messages.push(message);
            }

            clear() {
                this.messages = [];
            }
        }
        ```
    * exposes its cache of messages and two methods: one to add() a message to the cache and another to clear()
    * hero.service.ts
        * `import { MessageService } from './message.service';`
        * `constructor(private messageService: MessageService) { }`
        * add a message when getting heroes
            ```ts
            getHeroes(): Observable<Hero[]> {
                // TODO: send the message _after_ fetching the heroes
                this.messageService.add('HeroService: fetched heroes');
                return of(HEROES);
            }
            ```
        * display message - messages.component.ts
            *  import  `import { MessageService } from '../message.service';`
            * declare a **public messageService** property - Angular will inject the single MessageService into there when create messages component
                * about to bind to template so needs to be public - angular only binds to public component properties
        * messages.component.html
        ```html
        <div *ngIf="messageService.messages.length">

          <h2>Messages</h2>
          <button class="clear"
                  (click)="messageService.clear()">clear</button>
          <div *ngFor='let message of messageService.messages'> {{message}} </div>

        </div>
        ```
            * The **\*ngIf** only displays the messages area if there are messages to show.
            * An **\*ngFor** presents the list of messages in repeated <div> elements.
            * An Angular **event binding binds the button's click event to MessageService.clear()**.

## Routing
* create module for app routing
`ng generate module app-routing --flat --module=app`
