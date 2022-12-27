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
  var component: TodoFormComponent;

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
