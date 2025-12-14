---
name: typescript
description: TypeScript development with strict typing, advanced type utilities, generics, interfaces, and modern patterns. Covers type inference, conditional types, mapped types, utility types, and best practices for building type-safe applications. Use when working with .ts/.tsx files, building typed JavaScript applications, or implementing type systems.
---

# TypeScript

Type-safe JavaScript development with static typing and advanced type system features.

## Quick Start

```bash
# Initialize TypeScript project
npm init -y
npm install -D typescript @types/node ts-node

# Create tsconfig.json
npx tsc --init

# Compile TypeScript
npx tsc

# Run directly with ts-node
npx ts-node file.ts
```

## Core Type System

### Basic Types
```typescript
// Primitive types
let isDone: boolean = false;
let decimal: number = 6;
let color: string = "blue";
let list: number[] = [1, 2, 3];
let x: [string, number] = ["hello", 10];

// Object types
interface User {
  id: number;
  name: string;
  email?: string;  // Optional
  readonly createdAt: Date;  // Read-only
}

// Union types
type Status = 'pending' | 'active' | 'inactive';
type ID = string | number;

// Intersection types
type UserWithStatus = User & { status: Status };
```

### Advanced Types

```typescript
// Generic types
interface ApiResponse<T> {
  data: T;
  error?: string;
  status: number;
}

interface PaginatedResponse<T> extends ApiResponse<T[]> {
  pagination: {
    page: number;
    limit: number;
    total: number;
  };
}

// Conditional types
type NonNullable<T> = T extends null | undefined ? never : T;
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

// Mapped types
type PartialUser = {
  [K in keyof User]?: User[K];
};

type ReadOnlyUser = {
  readonly [K in keyof User]: User[K];
};

// Template literal types
type EventName<T extends string> = `on${Capitalize<T>}`;
type UserEvents = EventName<'login' | 'logout'>; // "onLogin" | "onLogout"
```

## Utility Types

### Built-in Utilities
```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// Partial<T> - All properties optional
type UserUpdate = Partial<User>;

// Required<T> - All properties required
type RequiredUser = Required<Partial<User>>;

// Pick<T, K> - Select specific properties
type UserPublic = Pick<User, 'id' | 'name'>;

// Omit<T, K> - Remove specific properties
type UserPrivate = Omit<User, 'id'>;

// Record<K, T> - Dictionary type
type UserMap = Record<string, User>;

// Exclude<T, U> - Remove types from union
type StatusWithoutPending = Exclude<Status, 'pending'>;

// Extract<T, U> - Extract types from union
type StringKeys<T> = Extract<keyof T, string>;
```

### Custom Utilities
```typescript
// Deep readonly
type DeepReadOnly<T> = {
  readonly [P in keyof T]: T[P] extends object ? DeepReadOnly<T[P]> : T[P];
};

// Branded types for nominal typing
type Brand<T, B> = T & { __brand: B };
type UserId = Brand<number, 'UserId'>;

// Type guards
function isString(value: unknown): value is string {
  return typeof value === 'string';
}

function isUser(obj: unknown): obj is User {
  return (
    typeof obj === 'object' &&
    obj !== null &&
    'id' in obj &&
    'name' in obj
  );
}
```

## Patterns & Best Practices

### 1. Strict Configuration

```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noImplicitReturns": true,
    "noUnusedLocals": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitOverride": true
  }
}
```

### 2. Discriminated Unions

```typescript
// State management with discriminated unions
type LoadingState = {
  type: 'loading';
};

type SuccessState<T> = {
  type: 'success';
  data: T;
};

type ErrorState = {
  type: 'error';
  error: Error;
};

type AsyncState<T> = LoadingState | SuccessState<T> | ErrorState;

// Usage with exhaustive checking
function handleState<T>(state: AsyncState<T>): string {
  switch (state.type) {
    case 'loading':
      return 'Loading...';
    case 'success':
      return `Success: ${JSON.stringify(state.data)}`;
    case 'error':
      return `Error: ${state.error.message}`;
    default:
      // Compile-time error if we miss a case
      const _exhaustiveCheck: never = state;
      return _exhaustiveCheck;
  }
}
```

### 3. Type-safe API

```typescript
// Type-safe API client
class ApiClient {
  private baseUrl: string;

  constructor(baseUrl: string) {
    this.baseUrl = baseUrl;
  }

  async get<T>(endpoint: string): Promise<T> {
    const response = await fetch(`${this.baseUrl}${endpoint}`);
    if (!response.ok) {
      throw new Error(`API error: ${response.status}`);
    }
    return response.json();
  }

  async post<T, D>(endpoint: string, data: D): Promise<T> {
    const response = await fetch(`${this.baseUrl}${endpoint}`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(data),
    });

    if (!response.ok) {
      throw new Error(`API error: ${response.status}`);
    }
    return response.json();
  }
}

// Usage
interface User {
  id: number;
  name: string;
}

interface CreateUserDto {
  name: string;
  email: string;
}

const api = new ApiClient('https://api.example.com');

const user = await api.get<User>('/users/1');
const newUser = await api.post<User, CreateUserDto>('/users', {
  name: 'John',
  email: 'john@example.com'
});
```

### 4. Dependency Injection

```typescript
// Generic repository pattern
interface Repository<T, ID> {
  findById(id: ID): Promise<T | null>;
  save(entity: T): Promise<T>;
  delete(id: ID): Promise<void>;
}

class UserRepository implements Repository<User, number> {
  constructor(private db: Database) {}

  async findById(id: number): Promise<User | null> {
    return this.db.users.findUnique({ where: { id } });
  }

  async save(user: User): Promise<User> {
    return this.db.users.upsert({
      where: { id: user.id },
      update: user,
      create: user,
    });
  }

  async delete(id: number): Promise<void> {
    await this.db.users.delete({ where: { id } });
  }
}

// Service using repository
class UserService {
  constructor(private userRepo: Repository<User, number>) {}

  async updateUserStatus(id: number, status: Status): Promise<User> {
    const user = await this.userRepo.findById(id);
    if (!user) {
      throw new Error('User not found');
    }

    const updatedUser = { ...user, status };
    return this.userRepo.save(updatedUser);
  }
}
```

## Advanced Techniques

### 1. Type-safe Event System

```typescript
// Type-safe event emitter
interface EventMap {
  userCreated: { id: number; name: string };
  userDeleted: { id: number };
  error: { message: string; code: string };
}

class EventEmitter<T extends Record<string, any>> {
  private listeners: { [K in keyof T]?: Array<(payload: T[K]) => void> } = {};

  on<K extends keyof T>(event: K, listener: (payload: T[K]) => void): void {
    if (!this.listeners[event]) {
      this.listeners[event] = [];
    }
    this.listeners[event]!.push(listener);
  }

  emit<K extends keyof T>(event: K, payload: T[K]): void {
    const listeners = this.listeners[event];
    if (listeners) {
      listeners.forEach(listener => listener(payload));
    }
  }
}

// Usage
const events = new EventEmitter<EventMap>();

events.on('userCreated', ({ id, name }) => {
  console.log(`User ${name} created with ID ${id}`);
});

events.emit('userCreated', { id: 1, name: 'John' });
```

### 2. Functional Programming

```typescript
// Type-safe pipe function
function pipe<T>(...fns: Array<(arg: T) => T>): (arg: T) => T {
  return (value: T) => fns.reduce((acc, fn) => fn(acc), value);
}

// Type-safe curry function
function curry<A, B, C>(fn: (a: A, b: B) => C): (a: A) => (b: B) => C {
  return (a: A) => (b: B) => fn(a, b);
}

// Usage
const add = (a: number, b: number) => a + b;
const curriedAdd = curry(add);
const add5 = curriedAdd(5);
console.log(add5(10)); // 15

const processNumbers = pipe(
  (nums: number[]) => nums.filter(n => n > 0),
  (nums: number[]) => nums.map(n => n * 2),
  (nums: number[]) => nums.reduce((a, b) => a + b, 0)
);
```

### 3. Builder Pattern

```typescript
// Type-safe query builder
interface QueryConfig {
  select?: string[];
  where?: Record<string, any>;
  orderBy?: Record<string, 'asc' | 'desc'>;
  limit?: number;
  offset?: number;
}

class QueryBuilder<T = any> {
  private config: QueryConfig = {};

  select<K extends keyof T>(...fields: K[]): QueryBuilder<Pick<T, K>> {
    this.config.select = fields as string[];
    return this;
  }

  where(condition: Partial<T>): this {
    this.config.where = { ...this.config.where, ...condition };
    return this;
  }

  orderBy(field: keyof T, direction: 'asc' | 'desc' = 'asc'): this {
    this.config.orderBy = { [field]: direction };
    return this;
  }

  limit(count: number): this {
    this.config.limit = count;
    return this;
  }

  build(): string {
    // Build SQL or other query format
    return `SELECT ${this.config.select?.join(', ') || '*'}
            WHERE ${JSON.stringify(this.config.where || {})}`;
  }
}

// Usage
const query = new QueryBuilder<User>()
  .select('id', 'name')
  .where({ active: true })
  .orderBy('name')
  .limit(10)
  .build();
```

## Working with Libraries

### React + TypeScript
```typescript
// Functional component with props
interface ButtonProps {
  children: React.ReactNode;
  variant?: 'primary' | 'secondary';
  onClick?: () => void;
  disabled?: boolean;
}

const Button: React.FC<ButtonProps> = ({
  children,
  variant = 'primary',
  onClick,
  disabled = false
}) => {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
};

// Custom hook with typing
function useApi<T>(url: string) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
}
```

### Express.js + TypeScript
```typescript
import { Request, Response, NextFunction } from 'express';

// Extend Request interface
interface AuthenticatedRequest extends Request {
  user?: {
    id: number;
    email: string;
  };
}

// Middleware
function authenticate(
  req: AuthenticatedRequest,
  res: Response,
  next: NextFunction
) {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }

  // Verify token and set user
  req.user = { id: 1, email: 'user@example.com' };
  next();
}

// Route handler
async function getUser(
  req: AuthenticatedRequest,
  res: Response
): Promise<void> {
  if (!req.user) {
    res.status(401).json({ error: 'Unauthorized' });
    return;
  }

  const user = await userService.findById(req.user.id);
  res.json(user);
}
```

## Best Practices Summary

### 1. Configuration
- Always enable `strict: true`
- Use `exactOptionalPropertyTypes`
- Avoid `any` at all costs
- Use `unknown` for untyped data

### 2. Type Design
- Prefer interfaces for object shapes
- Use types for unions, computed types
- Create small, focused types
- Use discriminated unions for state

### 3. Code Organization
- Group related types in modules
- Use barrel exports for type bundles
- Keep types close to their usage
- Use .d.ts files for declaration

### 4. Performance
- Use type narrowing early
- Avoid excessive generic depth
- Use readonly where appropriate
- Consider type inference costs

## Common Anti-Patterns

### 1. Type Assertions (Bad)
```typescript
// Bad - unsafe
const data = response.data as User;

// Good - type guards
if (isUser(response.data)) {
  const user = response.data;
}
```

### 2. Loose Types (Bad)
```typescript
// Bad - too permissive
function process(items: any[]): any {
  return items.map(item => item.value);
}

// Good - specific types
interface Item {
  id: number;
  value: string;
}
function process(items: Item[]): string[] {
  return items.map(item => item.value);
}
```

### 3. Missing Error Handling (Bad)
```typescript
// Bad - no error handling
const result = await api.get('/users');

// Good - proper error handling
try {
  const result = await api.get<User[]>('/users');
  console.log(result);
} catch (error) {
  if (error instanceof Error) {
    console.error('Failed to fetch users:', error.message);
  }
}
```

## Resources

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
- [TypeScript Playground](https://www.typescriptlang.org/play)
