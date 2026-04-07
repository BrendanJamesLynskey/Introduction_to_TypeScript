# Introduction to TypeScript

---

## Slide 01 — Title

**Introduction to TypeScript**

Typed JavaScript at Any Scale

types · generics · interfaces · advanced types · Node · React

---

## Slide 02 — Agenda

### Foundations
- What is TypeScript & why it exists
- Type annotations & inference
- Interfaces & type aliases
- Functions & generics

### Type System Deep Dive
- Enums & literal types
- Classes & access modifiers
- Type guards & narrowing
- Advanced types & mapped types

### Ecosystem Integration
- Module system & declaration files
- TypeScript with Node.js
- TypeScript with Express
- TypeScript with React

### Production
- Strict mode & compiler options
- Error handling patterns
- Migration strategy
- Summary & next steps

---

## Slide 03 — What Is TypeScript?

TypeScript is an **open-source, cross-platform JavaScript runtime** developed by **Anders Hejlsberg** at Microsoft (released 2012). It is a strict syntactical superset of JavaScript — every valid JS file is valid TS. TypeScript compiles (`tsc`) to plain JavaScript; types are erased at runtime.

The key insight: JavaScript's dynamic typing becomes a liability at scale. TypeScript adds a **static type system** that catches errors at compile time, enables powerful IDE tooling, and serves as living documentation.

### TypeScript is NOT
- A new language (it's a JS superset — all JS is valid TS)
- A runtime (types are erased during compilation)
- Only for large projects (benefits start from line 1)

### Key Properties
- **Event-driven** — errors at compile time
- **Type inference** — less boilerplate than Java/C#
- **Structural typing** — shape matters, not name
- **IDE integration** — autocomplete, refactoring
- **Gradual adoption** — use as much or little as you want

### Compilation Pipeline
```bash
npm install -D typescript
npx tsc index.ts          # → index.js
npx tsc --watch           # recompile on change
npx tsc --init            # generate tsconfig.json
```

---

## Slide 04 — Type Annotations & Inference

### Explicit Annotations
```typescript
// Primitives
let name: string = "Alice";
let age: number = 30;
let active: boolean = true;
let data: null = null;
let val: undefined = undefined;

// Arrays & tuples
let ids: number[] = [1, 2, 3];
let pair: [string, number] = ["age", 30];

// Object type
let user: { name: string; age: number } = {
  name: "Alice",
  age: 30,
};
```

### Type Inference
```typescript
let city = "London";        // string
let count = 42;             // number
let items = [1, 2, 3];     // number[]

function double(n: number) {
  return n * 2;             // returns number
}

const names = ["Alice", "Bob"];
names.forEach(name => {
  console.log(name.toUpperCase()); // name: string
});
```

### any
Opts out of type checking entirely. Avoid in production code.
```typescript
let x: any = "hello";
x = 42;  // no error
x.foo(); // no error (crashes at runtime)
```

### unknown
Type-safe counterpart of `any`. Must narrow before use.
```typescript
let x: unknown = getInput();
if (typeof x === "string") {
  x.toUpperCase();  // OK after narrowing
}
```

### never
Represents values that never occur. Used for exhaustive checks.
```typescript
function fail(msg: string): never {
  throw new Error(msg);
}
```

---

## Slide 05 — Interfaces & Type Aliases

### Interfaces
```typescript
interface User {
  id: number;
  name: string;
  email?: string;            // optional
  readonly createdAt: Date;  // immutable
}

interface Admin extends User {
  permissions: string[];
}

// Declaration merging (interfaces only)
interface User {
  avatar?: string;
}
```

### Type Aliases
```typescript
type ID = string | number;   // union type

type Point = {
  x: number;
  y: number;
};

// Intersection types
type Timestamped = Point & {
  createdAt: Date;
};

type ReadonlyUser = Readonly<User>;
type PartialUser = Partial<User>;
```

### Structural Typing
TypeScript uses structural typing (duck typing). If the shape matches, it's assignable.
```typescript
interface Point { x: number; y: number }
interface Coord { x: number; y: number }

let p: Point = { x: 1, y: 2 };
let c: Coord = p;  // OK — same shape
```

### Union & Discriminated Unions
```typescript
type Result =
  | { status: "ok";    data: string }
  | { status: "error"; message: string };

function handle(r: Result) {
  if (r.status === "ok") {
    console.log(r.data);
  } else {
    console.log(r.message);
  }
}
```

---

## Slide 06 — Functions

### Parameter & Return Types
```typescript
function greet(name: string): string {
  return `Hello, ${name}!`;
}

function log(msg: string, level = "info"): void {
  console.log(`[${level}] ${msg}`);
}

function sum(...nums: number[]): number {
  return nums.reduce((a, b) => a + b, 0);
}

type Comparator<T> = (a: T, b: T) => number;
```

### Overloads
```typescript
function parse(input: string): number;
function parse(input: string[]): number[];
function parse(input: string | string[]): number | number[] {
  if (Array.isArray(input)) {
    return input.map(Number);
  }
  return Number(input);
}
```

### Generic Functions
```typescript
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

first([1, 2, 3]);      // number | undefined
first(["a", "b"]);     // string | undefined
```

### Callback Typing Pattern
```typescript
function fetchData<T>(url: string, transform: (raw: unknown) => T): Promise<T> {
  return fetch(url).then(res => res.json()).then(transform);
}
```

---

## Slide 07 — Generics

### Type Parameters & Constraints
```typescript
interface Box<T> {
  value: T;
}

function getLength<T extends { length: number }>(item: T): number {
  return item.length;
}

function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
```

### Utility Types

| Utility | Description | Example |
|---------|-------------|---------|
| `Partial<T>` | All props optional | `Partial<User>` |
| `Required<T>` | All props required | `Required<Config>` |
| `Pick<T,K>` | Select subset of keys | `Pick<User, "id" \| "name">` |
| `Omit<T,K>` | Remove keys | `Omit<User, "password">` |
| `Record<K,V>` | Map keys to values | `Record<string, number>` |
| `Readonly<T>` | All props readonly | `Readonly<State>` |
| `ReturnType<F>` | Infer return type | `ReturnType<typeof fn>` |
| `Parameters<F>` | Infer param types | `Parameters<typeof fn>` |

### Real-World Generic
```typescript
interface ApiResponse<T> {
  data: T;
  status: number;
  timestamp: Date;
}

async function fetchApi<T>(url: string): Promise<ApiResponse<T>> {
  const res = await fetch(url);
  const data = await res.json();
  return { data, status: res.status, timestamp: new Date() };
}
```

---

## Slide 08 — Enums & Literal Types

### Numeric Enums
```typescript
enum Direction {
  Up,      // 0
  Down,    // 1
  Left,    // 2
  Right,   // 3
}
```

### String Enums
```typescript
enum Status {
  Active  = "ACTIVE",
  Paused  = "PAUSED",
  Deleted = "DELETED",
}
```

### const Enums
```typescript
const enum HttpMethod {
  GET  = "GET",
  POST = "POST",
  PUT  = "PUT",
}
// Inlined at compile time — no runtime object
```

### Literal Types — Often Preferred Over Enums
```typescript
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type Status = "idle" | "loading" | "success" | "error";

function request(method: HttpMethod, url: string) { /* ... */ }
request("GET", "/api/users");
```

### Discriminated Unions
```typescript
type Shape =
  | { kind: "circle";    radius: number }
  | { kind: "rectangle"; width: number; height: number }
  | { kind: "triangle";  base: number; height: number };

function area(shape: Shape): number {
  switch (shape.kind) {
    case "circle":    return Math.PI * shape.radius ** 2;
    case "rectangle": return shape.width * shape.height;
    case "triangle":  return 0.5 * shape.base * shape.height;
  }
}
```

---

## Slide 09 — Classes & Access Modifiers

### Access Modifiers
```typescript
class Animal {
  public name: string;
  protected speed: number;
  private _id: number;

  constructor(name: string, speed: number) {
    this.name = name;
    this.speed = speed;
    this._id = Math.random();
  }

  move(): string {
    return `${this.name} moves at ${this.speed}km/h`;
  }
}

class Dog extends Animal {
  constructor(name: string) {
    super(name, 40);
  }

  bark(): string {
    return `${this.name} says woof!`;
  }
}
```

| Modifier | Class | Subclass | Outside |
|----------|-------|----------|---------|
| `public` | Yes | Yes | Yes |
| `protected` | Yes | Yes | No |
| `private` | Yes | No | No |

### Abstract Classes
```typescript
abstract class Shape {
  abstract area(): number;
  abstract perimeter(): number;

  describe(): string {
    return `Area: ${this.area().toFixed(2)}`;
  }
}

class Circle extends Shape {
  constructor(private radius: number) { super(); }
  area() { return Math.PI * this.radius ** 2; }
  perimeter() { return 2 * Math.PI * this.radius; }
}
```

### Implements Interface
```typescript
interface Serializable {
  serialize(): string;
  deserialize(data: string): void;
}

class Config implements Serializable {
  constructor(private data: Record<string, unknown>) {}

  serialize(): string {
    return JSON.stringify(this.data);
  }

  deserialize(raw: string): void {
    this.data = JSON.parse(raw);
  }
}
```

---

## Slide 10 — Type Guards & Narrowing

### Built-in Guards
```typescript
function process(value: string | number | Date) {
  if (typeof value === "string") {
    return value.toUpperCase();
  }
  if (value instanceof Date) {
    return value.toISOString();
  }
  return value.toFixed(2);
}

interface Fish { swim(): void }
interface Bird { fly(): void }

function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    animal.swim();
  } else {
    animal.fly();
  }
}
```

### Custom Type Predicates
```typescript
function isString(val: unknown): val is string {
  return typeof val === "string";
}

const mixed: (string | number)[] = [1, "a", 2, "b"];
const strings = mixed.filter(
  (x): x is string => typeof x === "string"
);
```

### Assertion Functions
```typescript
function assertDefined<T>(
  val: T | null | undefined,
  msg?: string
): asserts val is T {
  if (val == null) throw new Error(msg ?? "Undefined");
}

const user = getUser();
assertDefined(user, "User not found");
user.name; // OK — narrowed to User
```

---

## Slide 11 — Advanced Types

### Mapped Types
```typescript
type MyPartial<T> = {
  [K in keyof T]?: T[K];
};

type Nullable<T> = {
  [K in keyof T]: T[K] | null;
};

type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};
```

### Conditional Types
```typescript
type IsString<T> = T extends string ? true : false;

type A = IsString<string>;  // true
type B = IsString<number>;  // false

type T1 = Extract<"a" | "b" | "c", "a" | "c">; // "a" | "c"
type T2 = Exclude<"a" | "b" | "c", "a">;        // "b" | "c"
```

### Template Literal Types
```typescript
type EventName = `${"click" | "focus"}_${"start" | "end"}`;
// "click_start" | "click_end" | "focus_start" | "focus_end"

type CSSValue = `${number}${"px" | "em" | "rem" | "%"}`;
const width: CSSValue = "100px";
```

### infer Keyword
```typescript
type MyReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type Awaited<T> = T extends Promise<infer U> ? Awaited<U> : T;
type R2 = Awaited<Promise<Promise<number>>>;  // number
```

---

## Slide 12 — Module System

### Import / Export
```typescript
export interface User { id: number; name: string; }
export function createUser(name: string): User {
  return { id: Date.now(), name };
}

export default class UserService { /* ... */ }

export { User as AppUser } from "./models";
export * from "./utils";

import type { User } from "./models";
import { type User, createUser } from "./models";
```

### Declaration Files (.d.ts)
```typescript
declare const API_URL: string;
declare function analytics(event: string): void;

declare module "my-lib" {
  export function parse(input: string): object;
  export const version: string;
}
```

### @types & DefinitelyTyped
- `npm i -D @types/express` — community types
- 60,000+ packages on DefinitelyTyped
- Auto-discovered by TS if in `node_modules/@types`
- Use `typeRoots` in tsconfig to customise

### Ambient Modules
```typescript
declare module "*.css" {
  const classes: Record<string, string>;
  export default classes;
}
declare module "*.svg" {
  const src: string;
  export default src;
}
```

---

## Slide 13 — TypeScript with Node.js

### tsconfig.json for Node
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "Node16",
    "moduleResolution": "Node16",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "declaration": true,
    "sourceMap": true,
    "paths": {
      "@/*": ["./src/*"],
      "@utils/*": ["./src/utils/*"]
    }
  },
  "include": ["src/**/*"]
}
```

### Development Runners

| Tool | Command | Notes |
|------|---------|-------|
| ts-node | `npx ts-node src/index.ts` | JIT compilation, slower startup |
| tsx | `npx tsx src/index.ts` | esbuild-powered, fast |
| tsc --watch | `npx tsc -w` | Compile + nodemon for restart |
| Node 22+ | `node --experimental-strip-types index.ts` | Native TS stripping |

### Essential Dev Dependencies
```bash
npm i -D typescript @types/node tsx
npm i -D @tsconfig/node22  # shared base config
```

---

## Slide 14 — TypeScript with Express

### Typed Request / Response
```typescript
import express, { Request, Response, NextFunction } from "express";

interface CreateUserBody {
  name: string;
  email: string;
}

interface UserParams {
  id: string;
}

app.post(
  "/api/users",
  (req: Request<{}, {}, CreateUserBody>, res: Response) => {
    const { name, email } = req.body;
    res.status(201).json({ id: 1, name, email });
  }
);

app.get(
  "/api/users/:id",
  (req: Request<UserParams>, res: Response) => {
    const userId = req.params.id;
  }
);
```

### Typed Middleware
```typescript
declare global {
  namespace Express {
    interface Request {
      user?: { id: number; role: string };
    }
  }
}

function auth(req: Request, res: Response, next: NextFunction): void {
  const token = req.headers.authorization;
  if (!token) {
    res.status(401).json({ error: "Unauthorized" });
    return;
  }
  req.user = verifyToken(token);
  next();
}
```

### Setup
```bash
npm i express
npm i -D @types/express typescript tsx
```

---

## Slide 15 — TypeScript with React

### Components & Props
```typescript
interface ButtonProps {
  label: string;
  variant?: "primary" | "secondary";
  disabled?: boolean;
  onClick: (e: React.MouseEvent) => void;
  children?: React.ReactNode;
}

function Button({ label, variant = "primary",
  disabled, onClick, children }: ButtonProps) {
  return (
    <button className={variant} disabled={disabled} onClick={onClick}>
      {children ?? label}
    </button>
  );
}
```

### Hooks Typing
```typescript
const [user, setUser] = useState<User | null>(null);
const inputRef = useRef<HTMLInputElement>(null);

type Action =
  | { type: "increment" }
  | { type: "set"; payload: number };

function reducer(state: number, action: Action): number {
  switch (action.type) {
    case "increment": return state + 1;
    case "set":       return action.payload;
  }
}
const [count, dispatch] = useReducer(reducer, 0);
```

### Generic Component
```typescript
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return <ul>{items.map(renderItem)}</ul>;
}
```

### Common Event Types

| Event | Type |
|-------|------|
| Click | `React.MouseEvent<HTMLButtonElement>` |
| Change | `React.ChangeEvent<HTMLInputElement>` |
| Submit | `React.FormEvent<HTMLFormElement>` |
| Keyboard | `React.KeyboardEvent<HTMLInputElement>` |

---

## Slide 16 — Strict Mode & Compiler Options

### What `"strict": true` Enables

| Flag | Effect |
|------|--------|
| `strictNullChecks` | `null`/`undefined` not assignable to other types |
| `noImplicitAny` | Error on inferred `any` |
| `strictFunctionTypes` | Contravariant parameter checking |
| `strictPropertyInitialization` | Class props must be initialised |
| `noImplicitThis` | Error on `this` with implicit `any` |
| `alwaysStrict` | Emit `"use strict"` |
| `useUnknownInCatchVariables` | `catch(e)` is `unknown` |

### Key Compiler Options
```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noFallthroughCasesInSwitch": true,
    "target": "ES2022",
    "module": "Node16",
    "outDir": "./dist",
    "declaration": true,
    "sourceMap": true,
    "moduleResolution": "Node16",
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "removeComments": true,
    "skipLibCheck": true
  }
}
```

### Recommendation
Start every new project with `"strict": true`. Add `"noUncheckedIndexedAccess": true` for array/object safety. These flags catch entire categories of bugs at zero runtime cost.

---

## Slide 17 — Error Handling Patterns

### Result Type Pattern
```typescript
type Result<T, E = Error> =
  | { ok: true;  value: T }
  | { ok: false; error: E };

function parseJSON(input: string): Result<unknown> {
  try {
    return { ok: true, value: JSON.parse(input) };
  } catch (e) {
    return { ok: false, error: e as Error };
  }
}

const result = parseJSON('{"name":"Alice"}');
if (result.ok) {
  console.log(result.value);
} else {
  console.error(result.error.message);
}
```

### Branded Types
```typescript
type UserId = string & { readonly __brand: "UserId" };
type OrderId = string & { readonly __brand: "OrderId" };

function createUserId(id: string): UserId {
  return id as UserId;
}

function getUser(id: UserId) { /* ... */ }
```

### Exhaustive Checks with never
```typescript
type Status = "active" | "paused" | "deleted";

function assertNever(x: never): never {
  throw new Error(`Unexpected value: ${x}`);
}

function handleStatus(status: Status): string {
  switch (status) {
    case "active":  return "Running";
    case "paused":  return "On hold";
    case "deleted": return "Removed";
    default: return assertNever(status);
  }
}
```

### Typed Error Hierarchy
```typescript
class AppError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly statusCode: number
  ) {
    super(message);
    this.name = "AppError";
  }
}

class NotFoundError extends AppError {
  constructor(resource: string) {
    super(`${resource} not found`, "NOT_FOUND", 404);
  }
}
```

---

## Slide 18 — Migration Strategy

### Step-by-Step Migration
1. Add `tsconfig.json` with `"allowJs": true`
2. Rename files `.js` → `.ts` one at a time
3. Start with `"strict": false`, enable flags incrementally
4. Replace `any` with `unknown`, then add proper types
5. Add types to function signatures first (biggest ROI)
6. Enable `"strict": true` once most files are typed

### JSDoc Types (no rename needed)
```javascript
// @ts-check

/** @type {string} */
let name = "Alice";

/**
 * @param {number} a
 * @param {number} b
 * @returns {number}
 */
function add(a, b) {
  return a + b;
}
```

### Migration tsconfig.json
```json
{
  "compilerOptions": {
    "allowJs": true,
    "checkJs": true,
    "strict": false,
    "noImplicitAny": false,
    "target": "ES2022",
    "module": "Node16",
    "outDir": "./dist",
    "rootDir": "./src",
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*"]
}
```

### Migration Phases

| Phase | Action | Risk |
|-------|--------|------|
| 1. Setup | `allowJs + checkJs` | Zero |
| 2. Annotate | JSDoc types in `.js` files | Zero |
| 3. Rename | `.js` → `.ts`, fix errors | Low |
| 4. Strict flags | Enable one flag at a time | Medium |
| 5. Full strict | `"strict": true` | Low (if incremental) |

### Helpful Tools
- `ts-migrate` (Airbnb) — auto-converts JS to TS
- `@ts-expect-error` — suppress known issues temporarily
- `// @ts-ignore` — last resort, avoid in production

---

## Slide 19 — Summary & Next Steps

### Core Type System
- Annotations, inference, `any`/`unknown`/`never`
- Interfaces, type aliases, structural typing
- Unions, intersections, discriminated unions
- Generics, constraints, utility types

### Advanced Features
- Mapped types, conditional types, `infer`
- Template literal types
- Type guards, narrowing, assertion functions
- Branded types, Result pattern

### Ecosystem
- Node.js: `tsx`, path aliases, tsconfig
- Express: typed routes, middleware, params
- React: props, hooks, generic components
- Migration: `allowJs`, incremental adoption

### Recommended Reading
- **TypeScript Handbook** — typescriptlang.org/docs
- **Programming TypeScript** — Boris Cherny, O'Reilly
- **Effective TypeScript** — Dan Vanderkam, O'Reilly
- **Type Challenges** — github.com/type-challenges
- **Total TypeScript** — Matt Pocock (totaltypescript.com)

### Key Takeaways
- TypeScript catches bugs at **compile time**, not production
- Start with `"strict": true` on every new project
- Prefer `unknown` over `any` — always
- Use discriminated unions over class hierarchies
- Types are **zero-cost** — erased at compile time
- Gradual migration is the safe path for existing codebases
