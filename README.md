# Angular.js Reference Card & Cheat Sheet

## Table of Contents

- [Core Concepts](#core-concepts)
- [Getting Started](#getting-started)
- [Components](#components)
- [Templates & Data Binding](#templates--data-binding)
- [Directives](#directives)
- [Services & Dependency Injection](#services--dependency-injection)
- [Routing](#routing)
- [Forms](#forms)
- [HTTP Client](#http-client)
- [Testing](#testing)
- [Performance Optimization](#performance-optimization)
- [Best Practices](#best-practices)
- [Common Gotchas](#common-gotchas)

## Core Concepts

### AngularJS (Angular 1.x) vs Angular (2+)

**Important**: This cheat sheet primarily covers modern Angular (2+), not the legacy AngularJS 1.x framework.

Modern Angular key differences:

- Component-based architecture (vs MVC in AngularJS)
- Uses TypeScript rather than JavaScript
- Improved performance with ahead-of-time compilation
- Mobile-friendly design
- Better dependency injection system

### Angular Building Blocks

|Building Block|Purpose                                                                    |
|--------------|---------------------------------------------------------------------------|
|Components    |The UI building blocks of Angular apps; contain the view template and logic|
|Directives    |Add behavior to existing DOM elements or components                        |
|Services      |Reusable business logic independent of components                          |
|Modules       |Organizing containers for related components, directives, services, etc.   |
|Pipes         |Transform data for display in templates                                    |
|Guards        |Control access to routes                                                   |
|Interceptors  |Intercept and modify HTTP requests/responses                               |
|Resolvers     |Pre-fetch data before route activation                                     |

## Getting Started

### Installation

```bash
# Install Angular CLI globally
npm install -g @angular/cli

# Create a new project
ng new my-angular-app

# Start development server
cd my-angular-app
ng serve
```

### Basic Project Structure

```
my-angular-app/
├── src/
│   ├── app/                    # Application code
│   │   ├── app.component.ts    # Root component
│   │   ├── app.component.html  # Root component template
│   │   ├── app.component.css   # Root component CSS
│   │   ├── app.module.ts       # Root module
│   ├── assets/                 # Static assets (images, etc.)
│   ├── environments/           # Environment configuration
│   ├── index.html              # Main HTML page
│   ├── main.ts                 # Application entry point
│   ├── styles.css              # Global styles
├── angular.json                # Angular CLI configuration
├── package.json                # NPM dependencies and scripts
├── tsconfig.json               # TypeScript configuration
```

### Angular CLI Commands

|Command                         |Purpose               |
|--------------------------------|----------------------|
|`ng serve`                      |Run development server|
|`ng build`                      |Build the application |
|`ng test`                       |Run unit tests        |
|`ng e2e`                        |Run end-to-end tests  |
|`ng generate component my-comp` |Generate a component  |
|`ng generate service my-service`|Generate a service    |
|`ng generate module my-module`  |Generate a module     |
|`ng generate pipe my-pipe`      |Generate a pipe       |
|`ng generate directive my-dir`  |Generate a directive  |
|`ng generate guard my-guard`    |Generate a route guard|

## Components

### Component Anatomy

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-example',
  templateUrl: './example.component.html',
  styleUrls: ['./example.component.css']
})
export class ExampleComponent implements OnInit {
  // Properties
  title: string = 'Example Component';
  items: string[] = ['Item 1', 'Item 2', 'Item 3'];
  
  // Constructor for dependency injection
  constructor(private someService: SomeService) { }
  
  // Lifecycle hook
  ngOnInit(): void {
    // Initialization code
  }
  
  // Methods
  addItem(newItem: string): void {
    this.items.push(newItem);
  }
}
```

### Component Communication

**Parent to Child**: Use `@Input()` decorator

```typescript
// Child component
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-child',
  template: '<p>{{ message }}</p>'
})
export class ChildComponent {
  @Input() message: string;
}

// Parent component template
<app-child [message]="parentMessage"></app-child>
```

**Child to Parent**: Use `@Output()` decorator with `EventEmitter`

```typescript
// Child component
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-child',
  template: '<button (click)="sendMessage()">Send Message</button>'
})
export class ChildComponent {
  @Output() messageEvent = new EventEmitter<string>();
  
  sendMessage() {
    this.messageEvent.emit('Message from child');
  }
}

// Parent component template
<app-child (messageEvent)="receiveMessage($event)"></app-child>
```

### Lifecycle Hooks

|Hook                     |Purpose                              |
|-------------------------|-------------------------------------|
|`ngOnChanges()`          |When input properties change         |
|`ngOnInit()`             |After component initialization       |
|`ngDoCheck()`            |Developer’s custom change detection  |
|`ngAfterContentInit()`   |After content projection             |
|`ngAfterContentChecked()`|After every content check            |
|`ngAfterViewInit()`      |After component’s view initialization|
|`ngAfterViewChecked()`   |After every view check               |
|`ngOnDestroy()`          |Before component destruction         |

## Templates & Data Binding

### Interpolation

```html
<h1>{{ title }}</h1>
<p>The sum of 1 + 1 is {{ 1 + 1 }}</p>
```

### Property Binding

```html
<img [src]="imageUrl">
<button [disabled]="isDisabled">Click me</button>
```

### Event Binding

```html
<button (click)="onClick()">Click me</button>
<input (input)="onInput($event)">
```

### Two-way Binding

```html
<input [(ngModel)]="name">
<!-- Equivalent to: -->
<input [value]="name" (input)="name = $event.target.value">
```

### Template Reference Variables

```html
<input #nameInput>
<button (click)="greet(nameInput.value)">Greet</button>
```

### Structural Directives

**ngIf**

```html
<div *ngIf="isVisible">This element is conditionally rendered</div>
<div *ngIf="user; else noUser">Welcome, {{ user.name }}</div>
<ng-template #noUser>Please login</ng-template>
```

**ngFor**

```html
<ul>
  <li *ngFor="let item of items; index as i; trackBy: trackByFn">
    {{ i }}: {{ item.name }}
  </li>
</ul>
```

**ngSwitch**

```html
<div [ngSwitch]="color">
  <div *ngSwitchCase="'red'">Red</div>
  <div *ngSwitchCase="'blue'">Blue</div>
  <div *ngSwitchDefault>Other</div>
</div>
```

## Directives

### Built-in Attribute Directives

**ngClass**

```html
<div [ngClass]="{'active': isActive, 'disabled': isDisabled}">
  Conditionally styled content
</div>
```

**ngStyle**

```html
<div [ngStyle]="{'color': textColor, 'font-size': fontSize + 'px'}">
  Inline styled content
</div>
```

### Custom Directive

```typescript
import { Directive, ElementRef, HostListener, Input } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  @Input('appHighlight') highlightColor: string;
  
  constructor(private el: ElementRef) { }
  
  @HostListener('mouseenter') onMouseEnter() {
    this.highlight(this.highlightColor || 'yellow');
  }
  
  @HostListener('mouseleave') onMouseLeave() {
    this.highlight(null);
  }
  
  private highlight(color: string) {
    this.el.nativeElement.style.backgroundColor = color;
  }
}
```

```html
<p appHighlight="lightblue">Hover over me</p>
```

## Services & Dependency Injection

### Service Definition

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { User } from './user.model';

@Injectable({
  providedIn: 'root'  // Makes it a singleton service
})
export class UserService {
  private apiUrl = 'https://api.example.com/users';
  
  constructor(private http: HttpClient) { }
  
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(this.apiUrl);
  }
  
  getUserById(id: number): Observable<User> {
    return this.http.get<User>(`${this.apiUrl}/${id}`);
  }
  
  createUser(user: User): Observable<User> {
    return this.http.post<User>(this.apiUrl, user);
  }
}
```

### Using the Service

```typescript
import { Component, OnInit } from '@angular/core';
import { UserService } from './user.service';
import { User } from './user.model';

@Component({
  selector: 'app-user-list',
  templateUrl: './user-list.component.html'
})
export class UserListComponent implements OnInit {
  users: User[] = [];
  loading = false;
  error: string = null;
  
  constructor(private userService: UserService) { }
  
  ngOnInit(): void {
    this.loading = true;
    this.userService.getUsers().subscribe(
      (data) => {
        this.users = data;
        this.loading = false;
      },
      (error) => {
        this.error = 'Failed to load users';
        this.loading = false;
        console.error(error);
      }
    );
  }
}
```

### Service Providers

```typescript
@NgModule({
  // ...
  providers: [
    UserService,
    { provide: LoggerService, useClass: ProductionLoggerService },
    { provide: API_URL, useValue: 'https://api.example.com' },
    { provide: DataService, useFactory: dataServiceFactory, deps: [LoggerService] }
  ]
})
```

## Routing

### Basic Router Setup

```typescript
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { AboutComponent } from './about/about.component';
import { UserComponent } from './user/user.component';
import { NotFoundComponent } from './not-found/not-found.component';
import { AuthGuard } from './auth.guard';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'user/:id', component: UserComponent, canActivate: [AuthGuard] },
  { path: 'admin', loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule) },
  { path: '**', component: NotFoundComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

### Router Links and Navigation

```html
<!-- Declarative navigation with routerLink -->
<nav>
  <a routerLink="/" routerLinkActive="active" [routerLinkActiveOptions]="{exact: true}">Home</a>
  <a routerLink="/about" routerLinkActive="active">About</a>
  <a [routerLink]="['/user', userId]" routerLinkActive="active">User Profile</a>
</nav>

<!-- Router outlet where components will be rendered -->
<router-outlet></router-outlet>
```

### Programmatic Navigation

```typescript
import { Router, ActivatedRoute } from '@angular/router';

export class SomeComponent {
  constructor(
    private router: Router,
    private route: ActivatedRoute
  ) { }
  
  goToHome() {
    this.router.navigate(['/']);
  }
  
  goToUser(id: number) {
    this.router.navigate(['/user', id]);
  }
  
  goToRelativePath() {
    this.router.navigate(['../sibling'], { relativeTo: this.route });
  }
  
  goWithQueryParams() {
    this.router.navigate(['/search'], { queryParams: { keyword: 'angular', page: 1 } });
  }
}
```

### Route Guards

```typescript
import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot, Router } from '@angular/router';
import { AuthService } from './auth.service';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(private authService: AuthService, private router: Router) { }
  
  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean {
    if (this.authService.isLoggedIn()) {
      return true;
    }
    
    // Store the attempted URL for redirecting after login
    this.authService.redirectUrl = state.url;
    
    // Navigate to the login page
    this.router.navigate(['/login']);
    return false;
  }
}
```

### Route Parameters, Query Parameters, and Fragments

```typescript
import { ActivatedRoute } from '@angular/router';
import { switchMap } from 'rxjs/operators';

export class UserComponent implements OnInit {
  constructor(private route: ActivatedRoute) { }
  
  ngOnInit() {
    // Get route parameters
    const id = this.route.snapshot.paramMap.get('id');
    
    // Get query parameters
    const page = this.route.snapshot.queryParamMap.get('page');
    
    // Get fragment
    const fragment = this.route.snapshot.fragment;
    
    // React to parameter changes
    this.route.paramMap.subscribe(params => {
      const id = params.get('id');
      // Fetch user by id
    });
    
    // Combine with service calls
    this.route.paramMap.pipe(
      switchMap(params => {
        const id = params.get('id');
        return this.userService.getUserById(id);
      })
    ).subscribe(user => {
      this.user = user;
    });
  }
}
```

## Forms

### Template-Driven Forms

```html
<form #userForm="ngForm" (ngSubmit)="onSubmit(userForm.value)">
  <div>
    <label for="name">Name</label>
    <input 
      type="text" 
      id="name" 
      name="name" 
      [(ngModel)]="user.name" 
      required 
      minlength="4" 
      #name="ngModel">
    
    <div *ngIf="name.invalid && (name.dirty || name.touched)">
      <div *ngIf="name.errors?.required">Name is required.</div>
      <div *ngIf="name.errors?.minlength">Name must be at least 4 characters long.</div>
    </div>
  </div>
  
  <div>
    <label for="email">Email</label>
    <input 
      type="email" 
      id="email" 
      name="email" 
      [(ngModel)]="user.email" 
      required 
      email 
      #email="ngModel">
    
    <div *ngIf="email.invalid && (email.dirty || email.touched)">
      <div *ngIf="email.errors?.required">Email is required.</div>
      <div *ngIf="email.errors?.email">Please enter a valid email.</div>
    </div>
  </div>
  
  <button type="submit" [disabled]="userForm.invalid">Submit</button>
</form>
```

```typescript
export class UserFormComponent {
  user = { name: '', email: '' };
  
  onSubmit(formValue) {
    console.log('Form submitted with value:', formValue);
    // Process form data
  }
}
```

### Reactive Forms

```typescript
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators, FormArray } from '@angular/forms';

@Component({
  selector: 'app-user-form',
  templateUrl: './user-form.component.html'
})
export class UserFormComponent implements OnInit {
  userForm: FormGroup;
  
  constructor(private fb: FormBuilder) { }
  
  ngOnInit() {
    this.userForm = this.fb.group({
      name: ['', [Validators.required, Validators.minLength(4)]],
      email: ['', [Validators.required, Validators.email]],
      address: this.fb.group({
        street: ['', Validators.required],
        city: [''],
        zip: ['', [Validators.pattern(/^\d{5}(-\d{4})?$/)]]
      }),
      phones: this.fb.array([
        this.fb.control('', Validators.required)
      ])
    });
  }
  
  get phones() {
    return this.userForm.get('phones') as FormArray;
  }
  
  addPhone() {
    this.phones.push(this.fb.control('', Validators.required));
  }
  
  removePhone(index: number) {
    this.phones.removeAt(index);
  }
  
  onSubmit() {
    if (this.userForm.valid) {
      console.log(this.userForm.value);
      // Process form data
    } else {
      // Mark all fields as touched to trigger validation display
      Object.keys(this.userForm.controls).forEach(key => {
        const control = this.userForm.get(key);
        control.markAsTouched();
      });
    }
  }
}
```

```html
<form [formGroup]="userForm" (ngSubmit)="onSubmit()">
  <div>
    <label for="name">Name</label>
    <input type="text" id="name" formControlName="name">
    
    <div *ngIf="userForm.get('name').invalid && 
               (userForm.get('name').dirty || userForm.get('name').touched)">
      <div *ngIf="userForm.get('name').errors?.required">Name is required.</div>
      <div *ngIf="userForm.get('name').errors?.minlength">
        Name must be at least 4 characters long.
      </div>
    </div>
  </div>
  
  <div>
    <label for="email">Email</label>
    <input type="email" id="email" formControlName="email">
    
    <div *ngIf="userForm.get('email').invalid && 
               (userForm.get('email').dirty || userForm.get('email').touched)">
      <div *ngIf="userForm.get('email').errors?.required">Email is required.</div>
      <div *ngIf="userForm.get('email').errors?.email">
        Please enter a valid email.
      </div>
    </div>
  </div>
  
  <div formGroupName="address">
    <h3>Address</h3>
    
    <div>
      <label for="street">Street</label>
      <input type="text" id="street" formControlName="street">
    </div>
    
    <div>
      <label for="city">City</label>
      <input type="text" id="city" formControlName="city">
    </div>
    
    <div>
      <label for="zip">ZIP Code</label>
      <input type="text" id="zip" formControlName="zip">
    </div>
  </div>
  
  <div>
    <h3>Phone Numbers</h3>
    <div formArrayName="phones">
      <div *ngFor="let phone of phones.controls; let i = index">
        <input type="text" [formControlName]="i">
        <button type="button" (click)="removePhone(i)">Remove</button>
      </div>
    </div>
    <button type="button" (click)="addPhone()">Add Phone</button>
  </div>
  
  <button type="submit" [disabled]="userForm.invalid">Submit</button>
</form>
```

### Custom Validators

```typescript
import { AbstractControl, ValidatorFn } from '@angular/forms';

// Custom validator function
export function forbiddenNameValidator(nameRe: RegExp): ValidatorFn {
  return (control: AbstractControl): {[key: string]: any} | null => {
    const forbidden = nameRe.test(control.value);
    return forbidden ? {'forbiddenName': {value: control.value}} : null;
  };
}

// In your component
this.userForm = this.fb.group({
  name: ['', [
    Validators.required, 
    Validators.minLength(4), 
    forbiddenNameValidator(/bob/i)  // No one named 'bob'
  ]]
});
```

## HTTP Client

### Basic HTTP Requests

```typescript
import { Injectable } from '@angular/core';
import { HttpClient, HttpHeaders, HttpParams } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError, retry, map } from 'rxjs/operators';
import { User } from './user.model';

@Injectable({
  providedIn: 'root'
})
export class UserService {
  private apiUrl = 'https://api.example.com/users';
  
  constructor(private http: HttpClient) { }
  
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(this.apiUrl)
      .pipe(
        retry(2),
        catchError(this.handleError)
      );
  }
  
  getUserById(id: number): Observable<User> {
    return this.http.get<User>(`${this.apiUrl}/${id}`)
      .pipe(catchError(this.handleError));
  }
  
  getUsersWithParams(page: number, pageSize: number): Observable<User[]> {
    let params = new HttpParams()
      .set('page', page.toString())
      .set('pageSize', pageSize.toString());
      
    return this.http.get<User[]>(this.apiUrl, { params })
      .pipe(catchError(this.handleError));
  }
  
  createUser(user: User): Observable<User> {
    const httpOptions = {
      headers: new HttpHeaders({
        'Content-Type': 'application/json'
      })
    };
    
    return this.http.post<User>(this.apiUrl, user, httpOptions)
      .pipe(catchError(this.handleError));
  }
  
  updateUser(user: User): Observable<User> {
    return this.http.put<User>(`${this.apiUrl}/${user.id}`, user)
      .pipe(catchError(this.handleError));
  }
  
  deleteUser(id: number): Observable<{}> {
    return this.http.delete(`${this.apiUrl}/${id}`)
      .pipe(catchError(this.handleError));
  }
  
  private handleError(error: any) {
    let errorMessage = '';
    if (error.error instanceof ErrorEvent) {
      // Client-side error
      errorMessage = `Error: ${error.error.message}`;
    } else {
      // Server-side error
      errorMessage = `Error Code: ${error.status}\nMessage: ${error.message}`;
    }
    console.error(errorMessage);
    return throwError(errorMessage);
  }
}
```

### HTTP Interceptors

```typescript
import { Injectable } from '@angular/core';
import {
  HttpEvent, HttpInterceptor, HttpHandler, HttpRequest,
  HttpResponse, HttpErrorResponse
} from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError, tap } from 'rxjs/operators';
import { AuthService } from './auth.service';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  constructor(private authService: AuthService) {}
  
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // Get the auth token
    const authToken = this.authService.getToken();
    
    // Clone the request and add the authorization header
    const authReq = req.clone({
      headers: req.headers.set('Authorization', `Bearer ${authToken}`)
    });
    
    // Pass on the cloned request
    return next.handle(authReq);
  }
}

@Injectable()
export class LoggingInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const startTime = Date.now();
    
    return next.handle(req).pipe(
      tap((event: HttpEvent<any>) => {
        if (event instanceof HttpResponse) {
          const endTime = Date.now();
          const duration = endTime - startTime;
          console.log(`${req.method} ${req.urlWithParams} completed in ${duration} ms`);
        }
      }),
      catchError((error: HttpErrorResponse) => {
        console.error(`Request failed: ${req.url}`);
        return throwError(error);
      })
    );
  }
}

// Register interceptors in your module
@NgModule({
  providers: [
    { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true },
    { provide: HTTP_INTERCEPTORS, useClass: LoggingInterceptor, multi: true }
  ]
})
```

## Testing

### Component Testing

```typescript
import { ComponentFixture, TestBed, waitForAsync } from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { DebugElement } from '@angular/core';
import { UserComponent } from './user.component';
import { UserService } from './user.service';
import { of } from 'rxjs';

describe('UserComponent', () => {
  let component: UserComponent;
  let fixture: ComponentFixture<UserComponent>;
  let debugElement: DebugElement;
  let mockUserService;
  
  beforeEach(waitForAsync(() => {
    // Create a mock UserService
    mockUserService = jasmine.createSpyObj(['getUsers', 'getUserById']);
    
    TestBed.configureTestingModule({
      declarations: [ UserComponent ],
      providers: [
        { provide: UserService, useValue: mockUserService }
      ]
    })
    .compileComponents();
  }));
  
  beforeEach(() => {
    fixture = TestBed.createComponent(UserComponent);
    component = fixture.componentInstance;
    debugElement = fixture.debugElement;
    
    // Setup mock return value
    mockUserService.getUsers.and.returnValue(of([
      { id: 1, name: 'John' },
      { id: 2, name: 'Jane' }
    ]));
  });
  
  it('should create', () => {
    expect(component).toBeTruthy();
  });
  
  it('should call getUsers on init', () => {
    component.ngOnInit();
    expect(mockUserService.getUsers).toHaveBeenCalled();
  });
  
  it('should display user list', () => {
    fixture.detectChanges(); // Trigger ngOnInit
    
    const userElements = debugElement.queryAll(By.css('.user-item'));
    expect(userElements.length).toBe(2);
    
    const firstUser = userElements[0].nativeElement;
    expect(firstUser.textContent).toContain('John');
  });
  
  it('should call getUserById when user is selected', () => {
    mockUserService.getUserById.and.returnValue(of({ id: 1, name: 'John' }));
    
    component.selectUser(1);
    
    expect(mockUserService.getUserById).toHaveBeenCalledWith(1);
    expect(component.selectedUser.name).toBe('John');
  });
});
```

### Service Testing

```typescript
import { TestBed } from '@angular/core/testing';
import { HttpClientTestingModule, HttpTestingController } from '@angular/common/http/testing';
import { UserService } from './user.service';
import { User } from './user.model';

describe('UserService', () => {
  let service: UserService;
  let httpMock: HttpTestingController;
  
  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [UserService]
    });
    
    service = TestBed.inject(UserService);
    httpMock = TestBed.inject(HttpTestingController);
  });
  
  afterEach(() => {
    httpMock.verify(); // Verify no outstanding requests
  });
  
  it('should retrieve users', () => {
    const mockUsers: User[] = [
      { id: 1, name: 'John' },
      { id: 2, name: 'Jane' }
    ];
    
    service.getUsers().subscribe(users => {
      expect(users.length).toBe(2);
      expect(users).toEqual(mockUsers);
    });
    
    const req = httpMock.expectOne('https://api.example.com/users');
    expect(req.request.method).toBe('GET');
    req.flush(mockUsers);
  });
  
  it('should create a user', () => {
    const newUser = { name: 'Bob' };
    const mockResponse = { id: 3, name: 'Bob' };
    
    service.createUser(newUser as User).subscribe(user => {
      expect(user).toEqual(mockResponse);
    });
    
    const req = httpMock.expectOne('https://api.example.com/users');
    expect(req.request.method).toBe('POST');
    expect(req.request.body).toEqual(newUser);
    req.flush(mockResponse);
  });
});
```

## Performance Optimization

### OnPush Change Detection

```typescript
import { Component, ChangeDetectionStrategy, Input } from '@angular/core';

@Component({
  selector: 'app-user-card',
  templateUrl: './user-card.component.html',
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class UserCardComponent {
  @Input() user: User;
}
```

### Lazy Loading Modules

```typescript
const routes: Routes = [
  { path: '', component: HomeComponent },
  { 
    path: 'admin', 
    loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule)
  }
];
```

### TrackBy with ngFor

```html
<ul>
  <li *ngFor="let user of users; trackBy: trackByUser">{{ user.name }}</li>
</ul>
```

```typescript
trackByUser(index: number, user: User): number {
  return user.id;
}
```

### AOT Compilation

```bash
ng build --prod
```

### Pure Pipes

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'filter',
  pure: true  // Default is true
})
```