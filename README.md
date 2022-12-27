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
