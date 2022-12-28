# Testing

### compute.ts

```typescript
export function compute(number: number): number {
  if (number < 0) return 0;

  return number + 1;
}
```

### compute.test.ts

```typescript
describe("Compute", () => {
  it("should return 0 if input is negative", () => {
    const result = compute(-1);
    expect(result).toBe(0);
  });

  it("should increment the input if it is positive", () => {
    const result = compute(1);
    expect(result).toBe(2);
  });
```

---

## Arrays and string

### greet.ts

```typescript
export function greet(name: string): string {
  return "Welcome " + name;
}
```

### greet.test.ts

```typescript
describe("greet", () => {
  it("should include the name in the message", () => {
    const result = greet("Diego");
    expect(result).toContain("Diego");
  });
});
```

### getCurrencies.ts

```typescript
export function getCurrencies(): string[] {
  return ["USD", "AUD", "EUR"];
}
```

### getCurrencies.test.ts

```typescript
describe("getCurrencies", () => {
  it("should return the supported currencies", () => {
    const result = getCurrencies();
    expect(result).toContain("USD");
    expect(result).toContain("AUD");
    expect(result).toContain("EUR");
  });
});
```

---

## Setup and teardown

### vote.component.ts

```typescript
export class VoteComponent {
  totalVotes = 0;

  upVote(): void {
    this.totalVotes++;
  }

  downVote(): void {
    this.totalVotes--;
  }
}
```

### vote.component.test.ts

```typescript
import { VoteComponent } from "./vote.component";

describe("VoteComponent", () => {
  // Arrange
  let component: VoteComponent;

  beforeEach(() => {
    // set up
    component = new VoteComponent();
  });

  afterEach(() => {
    // tear down
    component.totalVotes = 0;
  });

  it("should increment totalVotes when upvoted", () => {
    // Act
    component.upVote();
    // Assert
    expect(component.totalVotes).toBe(1);
  });

  it("should decrement totalVotes when upvoted", () => {
    component.downVote();
    expect(component.totalVotes).toBe(-1);
  });
});
```

---

## Forms

### todo-form.component.ts

```typescript
export class TodoFormComponent {
  form: FormGroup;

  constructor(private fb: FormBuilder) {
    this.form = this.fb.group({
      name: ["", Validators.required],
      email: [""],
    });
  }
}
```

### todo-form.component.test.ts

```typescript
describe("TodoFormComponent", () => {
  let component: TodoFormComponent;

  beforeEach(() => {
    component = new TodoFormComponent(new FormBuilder());
  });

  it("should create a form with 2 controls", () => {
    expect(component.form.contains("name")).toBeTruthy();
    expect(component.form.contains("email")).toBeTruthy();
  });

  it("should make the name control required", () => {
    const control = component.form.get("name");
    control.setValue("");
    expect(control.valid).toBeFaksy();
  });
});
```

---

## Event Emitters

### vote.component.ts

```typescript
export class VoteComponent {
  totalVotes = 0;
  @Output() voteChanged = new EventEmitter();

  upVote(): void {
    this.totalVotes++;
    this.voteChanged.emit(this.totalVotes);
  }
}
```

### vote.component.test.ts

```typescript
describe("VoteComponent", () => {
  let component: VoteComponent;

  beforeEach(() => {
    component = new VoteComponent();
  });

  it("should raise voteChanged event whe upvoted", () => {
    let totalVotes = null;
    component.voteChanged.subscribe((tv) => (totalVotes = tv));
    component.upVote();
    expect(totalVotes).not.toBeNull();
    expect(totalVotes).toBe(1);
  });
});
```

---

## Services

### todos.service.ts

```typescript
export class TodoService {
  constructor(private http: Http) {}

  add(todo) {
    return this.http.post("...", todo).map((r) => r.json());
  }

  getTodos() {
    return this.http.get("...").map((r) => r.json());
  }

  delete(id) {
    return this.http.delete("...").map((r) => r.json());
  }
}
```

### todos.component.ts

```typescript
export class TodosComponent implements OnInit {
  todos: any[] = [];
  message;

  constructor(private service: TodoService) {}

  ngOnInit() {
    this.service.getTodos().subscribe((t) => (this.todos = t));
  }

  add() {
    var newTodo = { title: "... " };
    this.service.add(newTodo).subscribe(
      (t) => this.todos.push(t),
      (err) => (this.message = err)
    );
  }

  delete(id) {
    if (confirm("Are you sure?")) this.service.delete(id).subscribe();
  }
}
```

### todos.component.spec.ts

```typescript
describe("TodosComponent", () => {
  let component: TodosComponent;
  let service: TodoService;

  beforeEach(() => {
    service = new TodoService(null);
    component = new TodosComponent(service);
  });

  it("should set todos property with the items returned from the service", () => {
    let todos = [
      { id: 1, title: "a" },
      { id: 2, title: "b" },
      { id: 3, title: "c" },
    ];

    spyOn(service, "getTodos").and.callFake(() => {
      return of(todos);
    });
    component.ngOnInit();
    expect(component.todos.length).toBeGreaterThan(0);
    expect(component.todos.length).toBe(3);
    expect(component.todos).toBe(todos);
  });

  it("should call the server to save the changes shen a new todo item is added", () => {
    let spy = spyOn(service, "add").and.callFake((todo) => {
      return of([]);
    });
    component.add();
    expect(spy).toHaveBeenCalled();
  });

  it("should add the new todo returned from the server", () => {
    let todo = { id: 1 };
    let spy = spyOn(service, "add").and.returnValue(of([todo]));
    component.add();
    expect(component.todos.indexOf(todo)).toBeGreaterThan(-1);
  });

  it("should set the message property if server returns and error when adding a new todo", () => {
    let error = "Error from the server";
    let spy = spyOn(service, "add").and.returnValue(throwError(() => error));
    component.add();
    expect(component.message).toBe(error);
  });

  it("should call the server to delete a todo item if the user confirms", () => {
    spyOn(window, "confirm").and.returnValue(true);
    let spy = spyOn(service, "delete").and.returnValue(of([]));
    component.delete(1);
    expect(spy).toHaveBeenCalledWith(1);
  });

  it("should NOT call the server to delete a todo item if the user cancels", () => {
    spyOn(window, "confirm").and.returnValue(false);
    let spy = spyOn(service, "delete").and.returnValue(of([]));
    component.delete(1);
    expect(spy).not.toHaveBeenCalled();
  });
});
```

# INTEGRATION TESTS

### voter.component.ts

```typescript
export class VoterComponent {
  @Input() othersVote = 0;
  @Input() myVote = 0;
  @Output() vote = new EventEmitter();

  upVote(): void {
    if (this.myVote == 1) return;

    this.myVote++;

    this.vote.emit({ myVote: this.myVote });
  }

  downVote(): void {
    if (this.myVote == -1) return;

    this.myVote--;

    this.vote.emit({ myVote: this.myVote });
  }

  get totalVotes(): number {
    return this.othersVote + this.myVote;
  }
}
```

### voter.component.html

```html
<div class="voter">
  <i
    class="glyphicon glyphicon-menu-up vote-button"
    [class.highlighted]="myVote == 1"
    (click)="upVote()"
  ></i>

  <span class="vote-count">{{ totalVotes }}</span>

  <i
    class="glyphicon glyphicon-menu-down vote-button"
    [class.highlighted]="myVote == -1"
    (click)="downVote()"
  ></i>
</div>
```

### voter.component.spec.ts

```typescript
describe("VoterComponent", () => {
  let component: VoterComponent;
  let fixture: ComponentFixture<VoterComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [VoterComponent],
    });

    fixture = TestBed.createComponent(VoterComponent);
    component = fixture.componentInstance;
  });

  it("should render total votes", () => {
    component.othersVote = 20;
    component.myVote = 1;
    fixture.detectChanges();

    const debugElement = fixture.debugElement.query(By.css(".vote-count"));
    const el: HTMLElement = debugElement.nativeElement;

    expect(el.innerText).toContain(21);
  });

  it("should highlight the upvote button if I have upvoted", () => {
    component.myVote = 1;
    fixture.detectChanges();

    const de = fixture.debugElement.query(By.css(".glyphicon-menu-up"));

    expect(de.classes["highlighted"]).toBeTruthy();
  });

  it("should increase total votes when I click the upvote button", () => {
    const button = fixture.debugElement.query(By.css(".glyphicon-menu-up"));
    button.triggerEventHandler("click", null); // This is to click the button.

    expect(component.totalVotes).toBe(1);
  });
});
```

---

### todo.component.ts

```typescript
export class TodosComponent implements OnInit {
  todos: any[] = [];
  message: string;

  constructor(private service: TodoService) {}

  ngOnInit(): void {
    this.service.getTodos().subscribe((t) => (this.todos = t));
  }

  add(): void {
    const newTodo = { title: "... " };
    this.service.add(newTodo).subscribe(
      (t) => this.todos.push(t),
      (err) => (this.message = err)
    );
  }

  delete(id): void {
    if (confirm("Are you sure?")) this.service.delete(id).subscribe();
  }
}
```

### todo.service.ts

```typescript
@Injectable()
export class TodoService {
  constructor(private http: Http) {}

  add(todo) {
    return this.http.post("...", todo).map((r) => r.json());
  }

  getTodos() {
    return this.http.get("...").map((r) => r.json());
  }

  getTodosPromise() {
    return this.http
      .get("...")
      .map((r) => r.json())
      .toPromise();
  }

  delete(id) {
    return this.http.delete("...").map((r) => r.json());
  }
}
```

### todo.component.spec.ts

```typescript
describe("TodosComponent", () => {
  let component: TodosComponent;
  let fixture: ComponentFixture<TodosComponent>;

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [TodosComponent],
      imports: [HttpClientTestingModule],
      providers: [TodoService],
    }).compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(TodosComponent);
    component = fixture.componentInstance;
  });

  it("should load todos from the server", () => {
    const service = TestBed.get(TodoService);
    spyOn(service, "getTodos").and.returnValue(of([1, 2, 3]));
    fixture.detectChanges();
    expect(component.todos.length).toBe(3);
  });
});
```

---

## Testing Routes

### app.routes.ts

```typescript
export const routes = [
  { path: "users/:id", component: UserDetailsComponent },
  { path: "users", component: UsersComponent },
  { path: "todos", component: TodosComponent },
  { path: "", component: HomeComponent },
];
```

### app.routes.spec.ts

```typescript
describe("routes", () => {
  it("should contain a route for /users", () => {
    expect(routes).toContain({ path: "users", component: UsersComponent });
  });
});
```

### user-details.component.ts

```typescript
export class UserDetailsComponent implements OnInit {
  constructor(private router: Router, private route: ActivatedRoute) {}

  ngOnInit(): void {
    this.route.params.subscribe((p) => {
      if (p["id"] === 0) this.router.navigate(["not-found"]);
    });
  }

  save(): void {
    this.router.navigate(["users"]);
  }
}
```

### user-details.component.spec.ts

```typescript
@Injectable()
class RouterStub {
  navigate(params) {}
}

@Injectable()
class ActivatedRouteStub {
  private subject = new Subject();

  push(value): void {
    this.subject.next(value);
  }

  get params(): Observable<any> {
    return this.subject.asObservable();
  }
}

describe("UserDetailsComponent", () => {
  let component: UserDetailsComponent;
  let fixture: ComponentFixture<UserDetailsComponent>;

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [UserDetailsComponent],
      providers: [
        {
          provide: Router,
          useClass: RouterStub,
        },
        {
          provide: ActivatedRoute,
          useClass: ActivatedRouteStub,
        },
      ],
    }).compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(UserDetailsComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it("should redirect the user to the users page after saving", () => {
    const router = TestBed.get(Router);
    const spy = spyOn(router, "navigate");
    component.save();
    expect(spy).toHaveBeenCalledWith(["users"]);
  });

  it("should navigate the user to the not found page when and invalid user id is passed", () => {
    const router = TestBed.get(Router);
    const spy = spyOn(router, "navigate");
    const route: ActivatedRouteStub = TestBed.get(ActivatedRoute);
    route.push({ id: 0 });
    expect(spy).toHaveBeenCalledWith(["not-found"]);
  });
});
```

---

### app.component.html

```html
<nav>
  <a routerLink="todos"></a>
</nav>
<router-outlet></router-outlet>
```

### app.component.spec.ts

```typescript
describe("AppComponent", () => {
  let component: AppComponent;
  let fixture: ComponentFixture<AppComponent>;

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      imports: [RouterTestingModule.withRoutes(routes)],
      declarations: [AppComponent],
    }).compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(AppComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it("should have a router outlet", () => {
    const de = fixture.debugElement.query(By.directive(RouterOutlet));
    expect(de).not.toBeNull();
  });

  it("should have a link to todos page", () => {
    const debugElements = fixture.debugElement.queryAll(
      By.directive(RouterLinkWithHref)
    );
    const index = debugElements.findIndex(
      (de) => de.properties["href"] === "/todos"
    );
    expect(index).toBeGreatherThan(-1);
  });
});
```

---

## Directive

### highlight.directive.ts

```typescript
export class HighlightDirective implements OnChanges {
  defaultColor = "yellow";
  @Input("highlight") bgColor: string;

  constructor(private el: ElementRef) {}

  ngOnChanges() {
    this.el.nativeElement.style.backgroundColor =
      this.bgColor || this.defaultColor;
  }
}
```

### highlight.directive.spec.ts

```typescript
@Component({
  template: `
    <p highlight="cyan">First</p>
    <p highlight>Second</p>
  `,
})
class DirectiveHostComponent {}

describe("HighlightDirective", () => {
  let fixture: ComponentFixture<DirectiveHostComponent>;

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [DirectiveHostComponent, HighlightDirective],
    }).compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(DirectiveHostComponent);
    fixture.detectChanges();
  });

  it("should higlight the first element with cyan", () => {
    const de = fixture.debugElement.queryAll(By.css("p"))[0];
    expect(de.nativeElement.style.backgroundColor).toBe("cyan");
  });

  it("should higlight the first element with the default color", () => {
    const de = fixture.debugElement.queryAll(By.css("p"))[1];
    const directive = de.injector.get(HighlightDirective);
    expect(de.nativeElement.style.backgroundColor).toBe(directive.defaultColor);
  });
});
```

---

## Asynchronous operations

### todos.component.ts

```typescript
export class TodosComponent implements OnInit {
  todos: any[] = [];

  constructor(private service: TodoService) {}

  ngOnInit(): void {
    this.service.getTodosPromise().then((t) => (this.todos = t));
  }
}
```

### todos.component.spec.ts

```typescript
describe("TodosComponent", () => {
  let component: TodosComponent;
  let fixture: ComponentFixture<TodosComponent>;

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [TodosComponent],
      imports: [HttpClientTestingModule],
      providers: [TodoService],
    }).compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(TodosComponent);
    component = fixture.componentInstance;
  });

  it("should load todos from the server", fakeAsync () => {
    const service = TestBed.get(TodoService);
    spyOn(service, "getTodosPromise").and.returnValue(
      Promise.resolve([1, 2, 3])
    );
    fixture.detectChanges();
    tick();
    expect(component.todos.length).toBe(3);
  });
});
```
