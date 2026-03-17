# Angular Interview Questions & Answers

A structured collection of Angular interview questions with clear answers and code examples, organized by topic.

---

## Table of Contents

1. [Angular Core Concepts & Components](#1-angular-core-concepts--components)
2. [Services & Core](#2-services--core)
3. [Interceptors](#3-interceptors)
4. [Pipes](#4-pipes)
5. [Data Sharing Between Components](#5-data-sharing-between-components)
6. [Calling APIs – Complete Flow (Product Example)](#6-calling-apis--complete-flow-product-example)
7. [Auth Guard & Route Protection](#7-auth-guard--route-protection)

---

## 1. Angular Core Concepts & Components

### What is Angular?

**Answer:** Angular is a platform and framework for building single-page applications (SPAs) using HTML, CSS, and TypeScript. It provides structure, tooling, and patterns for large-scale front-end apps.

---

### What is a Component?

**Answer:** A **component** is the main building block of an Angular app. It controls a part of the screen (a **view**) and has:

- **Template** – HTML that defines what the user sees.
- **Class** – TypeScript logic (data, methods) and metadata via decorators.
- **Styles** – CSS (optional) scoped to that component.

**Why components?** They make the UI reusable, testable, and organized. The app is a tree of components.

**Example:**

```typescript
@Component({
  selector: 'app-hello',           // Use as <app-hello> in HTML
  standalone: true,
  template: '<p>Hello, {{ name }}</p>',
  styles: ['p { color: blue; }']
})
export class HelloComponent {
  name = 'Angular';
}
```

---

### What are the core concepts of Angular?

**Answer:** Key concepts every Angular developer should know:

| Concept | What it is |
|--------|------------|
| **Components** | Building blocks that combine template + class + styles |
| **Modules / Standalone** | Way to group components, services, and routes (standalone components don’t need a module) |
| **Templates** | HTML with Angular syntax: bindings `{{ }}`, `[ ]`, `( )`, directives like `*ngIf`, `*ngFor` |
| **Directives** | Instructions in the DOM – structural (`*ngIf`, `*ngFor`) or attribute (`ngModel`, `ngClass`) |
| **Data binding** | One-way: `{{ }}`, `[property]`; Event: `(event)`; Two-way: `[(ngModel)]` |
| **Dependency Injection (DI)** | Angular provides instances of services (or other classes) to components instead of you creating them |
| **Services** | Injectable classes for shared logic (API calls, state) |
| **Routing** | Map URL paths to components (single-page navigation) |
| **Observables (RxJS)** | Async streams of data; used by HttpClient, Router, and reactive patterns |
| **Forms** | Template-driven or Reactive Forms for user input and validation |

---

### What is an Observable? Why does Angular use it?

**Answer:** An **Observable** is a stream of values over time. You **subscribe** to it to receive values (or errors, or completion). It comes from the **RxJS** library.

**Why Angular uses Observables:**

- **HttpClient** – HTTP requests return `Observable<Response>`. One request can emit one value (or error).
- **Router** – Events like navigation are exposed as Observables.
- **Async pipe** – In the template, `observable$ | async` subscribes and shows the latest value (and unsubscribes when the component is destroyed).
- **Reactive Forms** – `form.valueChanges` is an Observable.

**Important:** If you subscribe in the component class, **unsubscribe** when the component is destroyed (e.g. in `ngOnDestroy`) to avoid memory leaks, or use the **async** pipe so Angular handles it.

**Example:**

```typescript
import { Observable, of } from 'rxjs';

// Creating an Observable (e.g. from HTTP)
data$: Observable<string[]> = this.http.get<string[]>('/api/items');

// In component – subscribe to get values
this.data$.subscribe({
  next: (items) => console.log(items),
  error: (err) => console.error(err),
  complete: () => console.log('Done')
});

// In template – async pipe (no manual unsubscribe)
// <div *ngFor="let item of data$ | async">{{ item }}</div>
```

---

### Observable vs Promise – when to use which?

**Answer:**

| | Observable | Promise |
|--|------------|---------|
| **Values** | Can emit many values over time | Single value (or reject) |
| **Cancel** | Yes (unsubscribe) | No |
| **Lazy** | Runs when you subscribe | Runs as soon as created |
| **Angular** | Used by HttpClient, Router, Forms | Use when you need a one-off async value |

Angular’s HTTP and Router APIs use Observables, so you’ll use them most of the time. Use Promises only when you have a single async result and don’t need cancellation.

---

### What are Directives?

**Answer:** **Directives** add behavior or structure to the DOM. Types:

- **Structural** – Change layout by adding/removing elements: `*ngIf`, `*ngFor`, `*ngSwitch`.
- **Attribute** – Change appearance or behavior of an element: `ngModel`, `ngClass`, `ngStyle`, `ngForm`.

**Example:**

```html
<p *ngIf="isLoggedIn">Welcome back!</p>
<li *ngFor="let item of items; track item.id">{{ item.name }}</li>
<input [(ngModel)]="username" />
<div [ngClass]="{ active: isActive }">...</div>
```

---

### What is data binding? Types?

**Answer:** Data binding connects the component class and the template.

| Type | Syntax | Direction |
|------|--------|-----------|
| **Interpolation** | `{{ expression }}` | Class → Template |
| **Property** | `[property]="expression"` | Class → Template |
| **Event** | `(event)="handler()"` | Template → Class |
| **Two-way** | `[(ngModel)]="property"` | Both (for form fields) |

---

## 2. Services & Core

### Q1. What is a Service in Angular? Why use it?

**Answer:** A service is a class with a narrow, well-defined purpose. It is typically used for:

- **Reusable business logic** (API calls, calculations)
- **Shared state** across components
- **Cross-cutting concerns** (logging, auth)

Services are injectable and follow the **Dependency Injection (DI)** pattern. Angular creates a single instance (singleton) when provided in root.

**Example:**

```typescript
// user.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({
  providedIn: 'root'  // Singleton at app root
})
export class UserService {
  constructor(private http: HttpClient) {}

  getUsers() {
    return this.http.get<User[]>('/api/users');
  }
}
```

---

### Q2. What is `providedIn: 'root'` vs providing in a module/component?

**Answer:**

| Where provided | Scope | Use case |
|----------------|--------|----------|
| `providedIn: 'root'` | App-wide singleton | Shared services (API, auth) |
| `NgModule.providers` | All consumers of that module | Module-specific services |
| `Component.providers` | New instance per component | Component-local state/behavior |

**Example – component-level provider (new instance per component):**

```typescript
@Component({
  selector: 'app-counter',
  template: `{{ count }}`,
  providers: [CounterService]  // New instance for each CounterComponent
})
export class CounterComponent {
  constructor(public counter: CounterService) {}
}
```

---

### Q3. What is Dependency Injection (DI) in Angular?

**Answer:** DI is a design pattern where a class receives its dependencies from an external source (the injector) instead of creating them itself. Angular’s injector maintains a container of providers and injects the right instance when you use a type in a constructor.

**Example:**

```typescript
@Injectable({ providedIn: 'root' })
export class AuthService {
  isLoggedIn = false;
}

@Component({ selector: 'app-login' })
export class LoginComponent {
  constructor(private auth: AuthService) {}  // Angular injects AuthService
}
```

---

### Q4. What are Lifecycle Hooks? Name the main ones.

**Answer:** Lifecycle hooks are interface methods that Angular calls at specific moments in a component’s life. Main hooks:

- **ngOnChanges** – After input properties change (receives `SimpleChanges`)
- **ngOnInit** – After first change detection (initialization)
- **ngDoCheck** – Every change detection run (use sparingly)
- **ngAfterViewInit** – After view (and child views) are initialized
- **ngOnDestroy** – Just before the component is destroyed (cleanup)

**Example:**

```typescript
@Component({ selector: 'app-user', template: '...' })
export class UserComponent implements OnInit, OnDestroy {
  @Input() id!: number;
  private sub?: Subscription;

  ngOnInit() {
    this.sub = this.userService.getUser(this.id).subscribe(u => console.log(u));
  }

  ngOnDestroy() {
    this.sub?.unsubscribe();  // Prevent memory leaks
  }
}
```

---

## 3. Interceptors

### Q5. What is an HTTP Interceptor? What is it used for?

**Answer:** An HTTP interceptor is a service that can **inspect and transform** every HTTP request or response in one place. Typical uses:

- Adding auth headers (e.g. Bearer token)
- Logging requests/responses
- Error handling (e.g. 401 → redirect to login)
- Loading indicators
- Caching

**Example – adding auth header:**

```typescript
// auth.interceptor.ts
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
    const token = localStorage.getItem('token');
    if (token) {
      const cloned = req.clone({
        setHeaders: { Authorization: `Bearer ${token}` }
      });
      return next.handle(cloned);
    }
    return next.handle(req);
  }
}
```

**Register in app config (standalone):**

```typescript
// app.config.ts
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { authInterceptor } from './auth.interceptor';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(withInterceptors([authInterceptor]))
  ]
};
```

---

### Q6. How do you implement a functional interceptor (Angular 15+)?

**Answer:** From Angular 15+, you can use **functional interceptors** instead of a class. They are plain functions and work with `withInterceptors()`.

**Example:**

```typescript
// logging.interceptor.ts
import { HttpInterceptorFn } from '@angular/common/http';

export const loggingInterceptor: HttpInterceptorFn = (req, next) => {
  console.log('Request:', req.method, req.url);
  return next(req).pipe(
    tap(event => console.log('Response:', event))
  );
};
```

---

### Q7. How do you handle errors globally with an interceptor?

**Answer:** Use an interceptor that catches errors from `next.handle(req)` and handles them (e.g. show toast, redirect on 401).

**Example:**

```typescript
// error.interceptor.ts
export const errorInterceptor: HttpInterceptorFn = (req, next) => {
  return next(req).pipe(
    catchError((err: HttpErrorResponse) => {
      if (err.status === 401) {
        // Redirect to login
        window.location.href = '/login';
      } else {
        console.error('HTTP Error:', err.message);
      }
      return throwError(() => err);
    })
  );
};
```

---

## 4. Pipes

### Q8. What are Pipes? Pure vs Impure?

**Answer:** Pipes transform data in the template (e.g. date format, uppercase).  
- **Pure pipe (default):** Runs when the input reference changes (primitive or object reference). Good for performance.  
- **Impure pipe:** Runs on every change detection. Use only when you need to react to object/array content changes (e.g. filtering a list).

**Example – pure pipe:**

```typescript
@Pipe({ name: 'truncate' })
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit = 50): string {
    if (!value) return '';
    return value.length <= limit ? value : value.slice(0, limit) + '...';
  }
}
```

**Usage in template:**

```html
<p>{{ longText | truncate: 30 }}</p>
```

---

### Q9. How do you create an impure pipe?

**Answer:** Set `pure: false` in the pipe metadata. Angular will call `transform` on every change detection cycle.

**Example:**

```typescript
@Pipe({ name: 'filterList', pure: false })
export class FilterListPipe implements PipeTransform {
  transform(items: string[], search: string): string[] {
    if (!search) return items;
    return items.filter(item => item.toLowerCase().includes(search.toLowerCase()));
  }
}
```

**Note:** Prefer filtering in the component (or a service) for large lists to avoid running the pipe too often.

---

### Q10. Name some built-in pipes.

**Answer:**

| Pipe | Purpose |
|------|--------|
| `date` | Format dates: `{{ date \| date:'short' }}` |
| `uppercase` / `lowercase` | Case transformation |
| `currency` | Format as currency: `{{ price \| currency:'USD' }}` |
| `decimal` / `number` | Number formatting |
| `percent` | Percentage |
| `json` | Debug object as JSON |
| `async` | Subscribes to Observable/Promise and returns latest value |
| `slice` | Slice array/string for display |

**Example:**

```html
{{ user.birthDate | date:'mediumDate' }}
{{ amount | currency:'INR' }}
{{ data$ | async }}
```

---

## 5. Data Sharing Between Components

### Q11. How do you pass data from Parent to Child?

**Answer:** Use **`@Input()`** on the child. The parent binds a property in the template.

**Parent:**

```typescript
@Component({
  selector: 'app-parent',
  template: `<app-child [message]="parentMessage" [count]="count"></app-child>`
})
export class ParentComponent {
  parentMessage = 'Hello from parent';
  count = 42;
}
```

**Child:**

```typescript
@Component({ selector: 'app-child', template: `{{ message }} - {{ count }}` })
export class ChildComponent {
  @Input() message!: string;
  @Input() count!: number;
}
```

---

### Q12. How do you pass data from Child to Parent?

**Answer:** Use **`@Output()`** and **EventEmitter**. Child emits events; parent listens.

**Child:**

```typescript
@Component({
  selector: 'app-child',
  template: `<button (click)="sendData()">Send to Parent</button>`
})
export class ChildComponent {
  @Output() dataSent = new EventEmitter<string>();

  sendData() {
    this.dataSent.emit('Data from child');
  }
}
```

**Parent:**

```typescript
@Component({
  selector: 'app-parent',
  template: `<app-child (dataSent)="onDataReceived($event)"></app-child>
             <p>Received: {{ receivedData }}</p>`
})
export class ParentComponent {
  receivedData = '';

  onDataReceived(data: string) {
    this.receivedData = data;
  }
}
```

---

### Q13. How do you share data between sibling or unrelated components?

**Answer:** Common approaches:

1. **Parent as mediator** – Parent has the state, passes via `@Input` and receives via `@Output`, then passes to the other child.
2. **Service (RxJS Subject/BehaviorSubject)** – Shared service holds state; components inject and subscribe.
3. **State management** – NgRx, Akita, or similar for larger apps.

**Example – Service with BehaviorSubject:**

```typescript
@Injectable({ providedIn: 'root' })
export class SharedDataService {
  private dataSubject = new BehaviorSubject<string>('');
  data$ = this.dataSubject.asObservable();

  setData(value: string) {
    this.dataSubject.next(value);
  }
}

// Component A – sends data
export class ComponentA {
  constructor(private shared: SharedDataService) {}
  send() {
    this.shared.setData('Hello from A');
  }
}

// Component B – receives data
export class ComponentB {
  data$ = this.shared.data$;
  constructor(private shared: SharedDataService) {}
}
```

---

### Q14. What is ViewChild and when do you use it?

**Answer:** **ViewChild** lets a parent get a reference to a child component or a DOM element in its template. Use it when the parent needs to call methods or access properties on the child.

**Example:**

```typescript
@Component({
  selector: 'app-parent',
  template: `<app-child #childRef></app-child>
             <button (click)="focusChild()">Focus Child</button>`
})
export class ParentComponent {
  @ViewChild('childRef') child!: ChildComponent;  // or @ViewChild(ChildComponent)

  focusChild() {
    this.child.someMethod();
  }
}
```

---

### Q15. What is the difference between ViewChild and ContentChild?

**Answer:**

| | ViewChild | ContentChild |
|--|-----------|--------------|
| **Content** | Content **in the component’s template** | Content **projected** into `<ng-content>` |
| **When available** | After `ngAfterViewInit` | After `ngAfterContentInit` |
| **Use case** | Direct child in template | Content passed from parent between component tags |

**Example – ContentChild (projected content):**

```html
<!-- Parent -->
<app-card>
  <app-card-header #header>My Title</app-card-header>
</app-card>
```

```typescript
@Component({ selector: 'app-card', template: '<ng-content></ng-content>' })
export class CardComponent {
  @ContentChild('header') header!: ElementRef;
}
```

---

## 6. Calling APIs – Complete Flow (Product Example)

This section shows the **end-to-end flow** for a Product API: get all products, get product details, and create a product. It explains **why** we structure code this way (service vs component, HttpClient, Observables).

---

### Q16. How do you call an API in Angular? What is the typical flow?

**Answer:** The typical flow is:

1. **Model/Interface** – TypeScript interface for the API response shape.
2. **Service** – One place for all HTTP calls (GET, POST, etc.) using `HttpClient`.
3. **Component** – Injects the service, calls methods, subscribes to Observables, and binds data to the template.
4. **Template** – Displays data and triggers actions (e.g. load details, submit form).

**Why this flow?**

- **Separation of concerns:** API logic lives in the service; components only handle UI and user actions.
- **Reusability:** Multiple components can use the same service methods.
- **Testability:** You can mock the service in unit tests.
- **Single source of truth:** Base URL and endpoints are defined once in the service.

---

### Step 1: Model (Interface)

**Why we write this:** So TypeScript knows the shape of product data. It gives autocomplete, type safety, and fewer runtime errors.

```typescript
// models/product.model.ts

export interface Product {
  id: number;
  name: string;
  price: number;
  description?: string;
  category: string;
}

// For create/update we often omit 'id' (server generates it)
export interface CreateProductDto {
  name: string;
  price: number;
  description?: string;
  category: string;
}
```

---

### Step 2: Product Service (API calls)

**Why we write a service:**

- **Single place for API URLs and HTTP calls** – Change base URL or endpoint in one file.
- **Components stay thin** – They don’t need to know about `HttpClient` or URLs.
- **Easier to add interceptors, retries, error handling** – All requests go through the same layer.

**Why we return Observables:** HTTP calls are async. `HttpClient` returns `Observable<T>`. The component (or template with `async` pipe) subscribes when it needs the data. This supports cancellation, retry, and composition with other streams.

```typescript
// services/product.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, catchError, throwError } from 'rxjs';
import { Product, CreateProductDto } from '../models/product.model';

@Injectable({
  providedIn: 'root'
})
export class ProductService {
  // Base URL in one place – easy to change for dev/prod
  private readonly apiUrl = 'https://api.example.com/products';

  constructor(private http: HttpClient) {}

  /**
   * GET all products
   * Why Observable? So caller can subscribe when ready, cancel if needed, use async pipe.
   */
  getProducts(): Observable<Product[]> {
    return this.http.get<Product[]>(this.apiUrl).pipe(
      catchError(this.handleError)
    );
  }

  /**
   * GET single product by id (e.g. for detail page)
   */
  getProductById(id: number): Observable<Product> {
    return this.http.get<Product>(`${this.apiUrl}/${id}`).pipe(
      catchError(this.handleError)
    );
  }

  /**
   * POST – create a new product
   * Why return Observable<Product>? So caller can show success message, navigate, or update list.
   */
  createProduct(product: CreateProductDto): Observable<Product> {
    return this.http.post<Product>(this.apiUrl, product).pipe(
      catchError(this.handleError)
    );
  }

  /**
   * Centralized error handling – log and rethrow so subscriber can show UI error.
   */
  private handleError(error: unknown) {
    console.error('Product API error:', error);
    return throwError(() => new Error('Something went wrong; please try again.'));
  }
}
```

---

### Step 3: Component – List products, get details, create product

**Why we do it in the component:**

- **Component** = what the user sees and does. It calls the service and keeps loading/error state.
- **Service** = how we get or send data. No UI logic.

We subscribe in the component because we need to:
- Set loading flags.
- Store the list or selected product.
- Handle errors (e.g. show message).
- After create, maybe navigate or refresh the list.

```typescript
// product-list.component.ts
import { Component, OnInit } from '@angular/core';
import { ProductService } from '../../services/product.service';
import { Product } from '../../models/product.model';

@Component({
  selector: 'app-product-list',
  standalone: true,
  imports: [CommonModule, RouterLink],
  templateUrl: './product-list.component.html'
})
export class ProductListComponent implements OnInit {
  products: Product[] = [];
  loading = false;
  error: string | null = null;

  constructor(private productService: ProductService) {}

  ngOnInit(): void {
    this.loadProducts();
  }

  /**
   * Why separate method? So we can call it again after create, or on refresh.
   */
  loadProducts(): void {
    this.loading = true;
    this.error = null;
    this.productService.getProducts().subscribe({
      next: (data) => {
        this.products = data;
        this.loading = false;
      },
      error: (err) => {
        this.error = err.message || 'Failed to load products';
        this.loading = false;
      }
    });
  }
}
```

```html
<!-- product-list.component.html -->
@if (loading) {
  <p>Loading products...</p>
} @else if (error) {
  <p class="error">{{ error }}</p>
} @else {
  <ul>
    @for (p of products; track p.id) {
      <li>
        <a [routerLink]="['/products', p.id]">{{ p.name }}</a> – {{ p.price | currency }}
      </li>
    }
  </ul>
}
```

---

### Step 4: Product detail component (get by ID)

**Why we use route params:** The ID comes from the URL (e.g. `/products/5`). We read it from `ActivatedRoute` and call the service once we have the id.

```typescript
// product-detail.component.ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import { ProductService } from '../../services/product.service';
import { Product } from '../../models/product.model';

@Component({
  selector: 'app-product-detail',
  standalone: true,
  imports: [CommonModule],
  templateUrl: './product-detail.component.html'
})
export class ProductDetailComponent implements OnInit {
  product: Product | null = null;
  loading = false;
  error: string | null = null;

  constructor(
    private route: ActivatedRoute,
    private productService: ProductService
  ) {}

  ngOnInit(): void {
    // Get id from URL – why? So the page is bookmarkable and shareable.
    const id = Number(this.route.snapshot.paramMap.get('id'));
    if (id) {
      this.loadProduct(id);
    }
  }

  loadProduct(id: number): void {
    this.loading = true;
    this.error = null;
    this.productService.getProductById(id).subscribe({
      next: (data) => {
        this.product = data;
        this.loading = false;
      },
      error: (err) => {
        this.error = err.message || 'Failed to load product';
        this.loading = false;
      }
    });
  }
}
```

```html
<!-- product-detail.component.html -->
@if (loading) {
  <p>Loading...</p>
} @else if (error) {
  <p class="error">{{ error }}</p>
} @else if (product) {
  <h1>{{ product.name }}</h1>
  <p>{{ product.description }}</p>
  <p>{{ product.price | currency }}</p>
}
```

---

### Step 5: Create product (form + POST)

**Why we use a form:** We need user input (name, price, etc.), validation, and then send a single object to the API. Reactive Forms give us control and easy validation.

```typescript
// product-create.component.ts
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, Validators, ReactiveFormsModule } from '@angular/forms';
import { Router } from '@angular/router';
import { ProductService } from '../../services/product.service';
import { CreateProductDto } from '../../models/product.model';

@Component({
  selector: 'app-product-create',
  standalone: true,
  imports: [ReactiveFormsModule, CommonModule],
  templateUrl: './product-create.component.html'
})
export class ProductCreateComponent {
  form: FormGroup;
  submitting = false;
  error: string | null = null;

  constructor(
    private fb: FormBuilder,
    private productService: ProductService,
    private router: Router
  ) {
    // Why FormBuilder? Cleaner than new FormGroup(...) and easy to add validators.
    this.form = this.fb.group({
      name: ['', [Validators.required, Validators.minLength(2)]],
      price: [0, [Validators.required, Validators.min(0)]],
      description: [''],
      category: ['', Validators.required]
    });
  }

  onSubmit(): void {
    if (this.form.invalid) return;
    this.submitting = true;
    this.error = null;
    const dto: CreateProductDto = this.form.value;

    this.productService.createProduct(dto).subscribe({
      next: (created) => {
        this.submitting = false;
        // Why navigate? After create, user usually goes to list or detail.
        this.router.navigate(['/products', created.id]);
      },
      error: (err) => {
        this.error = err.message || 'Failed to create product';
        this.submitting = false;
      }
    });
  }
}
```

```html
<!-- product-create.component.html -->
<form [formGroup]="form" (ngSubmit)="onSubmit()">
  <input formControlName="name" placeholder="Name" />
  @if (form.get('name')?.invalid && form.get('name')?.touched) {
    <span class="error">Name is required (min 2 chars)</span>
  }
  <input type="number" formControlName="price" placeholder="Price" />
  <input formControlName="description" placeholder="Description" />
  <input formControlName="category" placeholder="Category" />
  <button type="submit" [disabled]="submitting">
    {{ submitting ? 'Creating...' : 'Create Product' }}
  </button>
</form>
@if (error) {
  <p class="error">{{ error }}</p>
}
```

---

### Total flow summary (interview answer)

| Step | What | Why |
|------|------|-----|
| **Model** | `Product` and `CreateProductDto` interfaces | Type safety and clear contract with API |
| **Service** | `ProductService` with `getProducts()`, `getProductById()`, `createProduct()` | Single place for HTTP calls, base URL, and error handling |
| **List component** | Calls `getProducts()` in `ngOnInit`, subscribes, sets `products` / `loading` / `error` | Show list and handle loading/errors in UI |
| **Detail component** | Reads `id` from `ActivatedRoute`, calls `getProductById(id)` | Show one product from URL |
| **Create component** | Reactive Form, on submit calls `createProduct(dto)`, then navigate | Collect input, validate, send POST, then redirect |

**Why service class?**

- Keeps components focused on UI and user actions.
- Same API methods can be used from list, detail, create, or any future screen.
- Easy to add interceptors (auth, logging) and unit tests (mock `ProductService`).

---

## 7. Auth Guard & Route Protection

This section explains **how route guards work**, **before vs after navigation**, and **which guard methods** to use in an interview.

---

### Q17. What is an Auth Guard? Why do we use it?

**Answer:** An **auth guard** (or **route guard**) is a class (or function) that the Angular **Router** runs before (or after) navigating to a route. It decides:

- **Can the user enter this route?** (e.g. only if logged in)
- **Can the user leave this route?** (e.g. confirm before losing unsaved form data)

**Why we use it:** To protect routes (e.g. dashboard, profile) so unauthenticated users are redirected to login, and to control when navigation is allowed or blocked.

---

### How does the guard run? Before navigate vs after navigate?

**Answer:** Guards run at specific moments in the **navigation lifecycle**:

| When | Guard type | Purpose |
|------|------------|--------|
| **Before** navigating to a route | `CanActivate` | “Can this route be activated?” (e.g. is user logged in?) |
| **Before** activating any child route | `CanActivateChild` | Same check for every child of a route |
| **Before** leaving a route | `CanDeactivate` | “Can the user leave?” (e.g. unsaved changes confirmation) |
| **After** route is resolved (e.g. data loaded) | `Resolve` | Pre-fetch data before the component loads |
| **Before** loading a lazy-loaded module | `CanMatch` / `CanLoad` | “Should this route (or module) be loaded?” (e.g. by role) |

**In short:**

- **Before navigate** → `CanActivate`, `CanActivateChild`, `CanMatch` / `CanLoad`. Used to allow or block **entering** a route.
- **After navigate (but before component shows)** → `Resolve` runs when the route is being resolved; you load data so the component gets it immediately.
- **Before leaving** → `CanDeactivate`. Used to allow or block **leaving** a route (e.g. “You have unsaved changes”).

---

### Q18. How do you implement a CanActivate auth guard?

**Answer:** Implement the `CanActivateFn` (functional guard) or the `CanActivate` interface. Return `true` to allow navigation, `false` to block, or redirect (e.g. to login) by returning `UrlTree`.

**Example – functional guard (recommended in modern Angular):**

```typescript
// auth.guard.ts
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
import { AuthService } from './auth.service';

export const authGuard: CanActivateFn = (route, state) => {
  const auth = inject(AuthService);
  const router = inject(Router);

  if (auth.isLoggedIn()) {
    return true;   // Allow navigation
  }
  // Before navigating away, redirect to login
  return router.createUrlTree(['/login'], {
    queryParams: { returnUrl: state.url }   // So we can redirect back after login
  });
};
```

**Why return `UrlTree` for redirect?** The router expects either `true`/`false` or a `UrlTree`. Returning `router.createUrlTree(['/login'])` tells the router: “Don’t go to the requested URL; go to `/login` instead.” So the guard runs **before** the target route is activated.

**Register in routes:**

```typescript
// app.routes.ts
import { Routes } from '@angular/router';
import { authGuard } from './auth.guard';

export const routes: Routes = [
  { path: 'login', loadComponent: () => import('./login/login.component') },
  {
    path: 'dashboard',
    loadComponent: () => import('./dashboard/dashboard.component'),
    canActivate: [authGuard]   // Runs BEFORE navigating to dashboard
  },
  { path: '', redirectTo: 'login', pathMatch: 'full' }
];
```

---

### Q19. What is CanDeactivate? When do you use it?

**Answer:** **CanDeactivate** runs **before the user leaves** the current route (e.g. clicks another link or back). Use it to:

- Ask “You have unsaved changes. Leave anyway?”
- Block navigation until the user saves or cancels.

**Example – guard for unsaved form:**

```typescript
// unsaved-changes.guard.ts
import { CanDeactivateFn } from '@angular/router';

export interface HasUnsavedChanges {
  hasUnsavedChanges(): boolean;
}

export const unsavedChangesGuard: CanDeactivateFn<HasUnsavedChanges> = (component) => {
  if (component?.hasUnsavedChanges?.()) {
    return confirm('You have unsaved changes. Leave anyway?');
  }
  return true;
};
```

**Component implements the interface:**

```typescript
export class EditProductComponent implements HasUnsavedChanges {
  form: FormGroup;
  initialValue: unknown;

  hasUnsavedChanges(): boolean {
    return this.form.dirty && JSON.stringify(this.form.value) !== JSON.stringify(this.initialValue);
  }
}
```

**Route config:**

```typescript
{
  path: 'products/:id/edit',
  component: EditProductComponent,
  canDeactivate: [unsavedChangesGuard]   // Runs BEFORE leaving the route
}
```

**Interview summary:** `CanActivate` = before **entering**; `CanDeactivate` = before **leaving**.

---

### Q20. What is CanActivateChild? How is it different from CanActivate?

**Answer:**  
- **CanActivate** – Runs when navigating **to that specific route**.  
- **CanActivateChild** – Runs when navigating **to any child route** of that route.

Use **CanActivateChild** when you have a parent route (e.g. `dashboard`) with many children (`dashboard/profile`, `dashboard/settings`) and you want **one guard** to protect all children.

**Example:**

```typescript
export const authGuard: CanActivateFn = (route, state) => { ... };

// In routes:
{
  path: 'dashboard',
  canActivateChild: [authGuard],   // Runs before activating ANY child
  children: [
    { path: 'profile', loadComponent: () => ... },
    { path: 'settings', loadComponent: () => ... }
  ]
}
```

So: **before navigate** to `dashboard/profile`, the router runs `authGuard` because of `canActivateChild` on `dashboard`.

---

### Q21. How do Resolve guards work? Before or after navigate?

**Answer:** A **Resolve** guard runs when the router is **resolving** the route – after the route is matched but **before** the component is created and displayed. So it’s “before the user sees the page,” but “after” the decision to go to that route.

**Use case:** Load product or user data so when the component loads, the data is already there (no loading spinner for that data).

**Example – resolve product by id:**

```typescript
// product.resolver.ts
import { inject } from '@angular/core';
import { ResolveFn } from '@angular/router';
import { Product } from './models/product.model';
import { ProductService } from './product.service';

export const productResolver: ResolveFn<Product> = (route) => {
  const id = Number(route.paramMap.get('id'));
  return inject(ProductService).getProductById(id);
};
```

**Route:**

```typescript
{
  path: 'products/:id',
  component: ProductDetailComponent,
  resolve: { product: productResolver }   // Data available as route.snapshot.data['product']
}
```

**In component:**

```typescript
this.product = this.route.snapshot.data['product'];
```

**Interview summary:** Resolve runs **before** the component is shown; you use it to fetch data **before navigate** completes so the page has data immediately.

---

### Guard methods summary (interview cheat sheet)

| Guard | When it runs | Return | Use case |
|-------|----------------|--------|----------|
| **CanActivate** | Before entering route | `true` / `false` / `UrlTree` | Auth: allow only if logged in |
| **CanActivateChild** | Before entering any child route | same | Protect all children of a parent |
| **CanDeactivate** | Before leaving route | `true` / `false` | Unsaved changes confirmation |
| **Resolve** | During resolve, before component loads | Observable / Promise / value | Pre-load data for the route |
| **CanMatch** | Before matching route (e.g. lazy load) | `true` / `false` | Decide if route (or module) should load (e.g. by role) |

**Before navigate:** CanActivate, CanActivateChild, CanMatch.  
**After route matched, before component:** Resolve.  
**Before leaving:** CanDeactivate.

---

## Quick Reference

| Topic | Key concepts |
|-------|----------------|
| **Angular Core** | Components, templates, directives, data binding, Observables, RxJS, modules/standalone |
| **Services & Core** | `@Injectable`, `providedIn`, DI, lifecycle hooks |
| **Interceptors** | `HttpInterceptor` / `HttpInterceptorFn`, `next.handle()`, auth/error handling |
| **Pipes** | Pure vs impure, `PipeTransform`, built-in pipes |
| **Data sharing** | `@Input`, `@Output`, EventEmitter, shared service (BehaviorSubject), ViewChild, ContentChild |
| **Calling APIs** | Model/interface, service with `HttpClient`, GET/POST, Observable, subscribe, loading/error state, Reactive Forms |
| **Auth Guard** | CanActivate (before enter), CanDeactivate (before leave), CanActivateChild, Resolve (before component), return `true`/`false`/`UrlTree` |

---

*Good luck with your Angular interviews.*
