# Testing

## Compute

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

## Greet

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

## GetCurrencies

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

## vote.component.ts

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

## vote.component.test.ts

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

## todo-form.component.ts

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

## todo-form.component.test.ts

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

## vote.component.ts

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

## vote.component.test.ts

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

## todos.service.ts

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

## todos.component.ts

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

## todos.component.spec.ts

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
});
```
