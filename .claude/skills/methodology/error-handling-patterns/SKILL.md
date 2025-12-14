---
name: error-handling-patterns
description: Comprehensive error handling strategies for robust applications. Use when implementing error boundaries, validation layers, retry mechanisms, fallback strategies, or error reporting systems. Covers try-catch patterns, error propagation, graceful degradation, and user experience during failures.
---

# Error Handling Patterns

Robust error handling strategies that fail gracefully and recover intelligently.

## Quick Start

```javascript
// Universal error handling template
try {
  const result = await operation();
  return { success: true, data: result };
} catch (error) {
  // 1. Log with context
  logger.error(operation.name, { error, context });

  // 2. Handle specific error types
  if (error.code === 'ENOTFOUND') {
    return { success: false, error: 'Service unavailable', retry: true };
  }

  // 3. Return user-friendly response
  return { success: false, error: 'Operation failed' };
}
```

## Error Handling Principles

### 1. Fail Fast, Fail Loud
```javascript
// Bad: Silent failure
function getUser(id) {
  const user = db.find(id);
  return user.name; // Crashes if user is null
}

// Good: Explicit error
function getUser(id) {
  const user = db.find(id);
  if (!user) {
    throw new Error(`User ${id} not found`);
  }
  return user.name;
}
```

### 2. Preserve Context
```javascript
class OrderProcessor {
  async processOrder(orderId) {
    try {
      const order = await this.fetchOrder(orderId);
      return await this.validateOrder(order);
    } catch (error) {
      // Preserve full context for debugging
      throw new Error(`Failed to process order ${orderId}: ${error.message}`, {
        cause: error,
        orderId,
        timestamp: new Date().toISOString()
      });
    }
  }
}
```

### 3. Handle at Appropriate Layer

| Layer | Responsibility | Example |
|-------|----------------|---------|
| **Database** | Data integrity | Unique constraint violations |
| **Service** | Business logic | Invalid business rules |
| **API** | Protocol concerns | Malformed requests |
| **UI** | User experience | Friendly error messages |

## Core Patterns

### 1. Result Type Pattern
```typescript
type Result<T, E = Error> =
  | { success: true; data: T }
  | { success: false; error: E };

// Usage eliminates try-catch chains
function parseJSON(json: string): Result<any> {
  try {
    return { success: true, data: JSON.parse(json) };
  } catch (error) {
    return { success: false, error: error as Error };
  }
}

// Chain operations safely
const result = parseJSON(input)
  .flatMap(validateData)
  .flatMap(transformData);

if (result.success) {
  console.log(result.data);
} else {
  console.error(result.error);
}
```

### 2. Retry with Exponential Backoff
```javascript
class RetryHandler {
  async execute(fn, options = {}) {
    const {
      maxAttempts = 3,
      baseDelay = 1000,
      maxDelay = 30000,
      factor = 2,
      jitter = true
    } = options;

    let lastError;

    for (let attempt = 1; attempt <= maxAttempts; attempt++) {
      try {
        return await fn();
      } catch (error) {
        lastError = error;

        if (attempt === maxAttempts || !this.isRetryable(error)) {
          throw error;
        }

        const delay = this.calculateDelay(
          attempt,
          baseDelay,
          maxDelay,
          factor,
          jitter
        );

        await this.sleep(delay);
      }
    }

    throw lastError;
  }

  isRetryable(error) {
    const retryableCodes = ['ECONNRESET', 'ETIMEDOUT', 'ENOTFOUND'];
    return retryableCodes.includes(error.code);
  }

  calculateDelay(attempt, base, max, factor, jitter) {
    let delay = Math.min(base * Math.pow(factor, attempt - 1), max);

    if (jitter) {
      delay *= 0.8 + Math.random() * 0.4; // ±20%
    }

    return delay;
  }
}
```

### 3. Circuit Breaker Pattern
```javascript
class CircuitBreaker {
  constructor(options = {}) {
    this.threshold = options.threshold || 5;
    this.timeout = options.timeout || 60000;
    this.state = 'CLOSED'; // CLOSED, OPEN, HALF_OPEN
    this.failures = 0;
    this.nextAttempt = Date.now();
  }

  async execute(fn) {
    if (this.state === 'OPEN') {
      if (Date.now() < this.nextAttempt) {
        throw new Error('Circuit breaker is OPEN');
      }
      this.state = 'HALF_OPEN';
    }

    try {
      const result = await fn();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }

  onSuccess() {
    this.failures = 0;
    this.state = 'CLOSED';
  }

  onFailure() {
    this.failures++;
    if (this.failures >= this.threshold) {
      this.state = 'OPEN';
      this.nextAttempt = Date.now() + this.timeout;
    }
  }
}
```

### 4. Graceful Degradation
```javascript
class UserService {
  async getUserProfile(userId) {
    // Try multiple data sources in order of preference
    const strategies = [
      () => this.getFromCache(userId),
      () => this.getFromDatabase(userId),
      () => this.getFromAPI(userId),
      () => this.getDefaultProfile()
    ];

    for (const strategy of strategies) {
      try {
        const profile = await strategy();
        if (profile) return profile;
      } catch (error) {
        console.warn(`Strategy failed:`, error.message);
        // Continue to next strategy
      }
    }

    // All strategies failed
    throw new Error('Unable to fetch user profile');
  }
}
```

## Error Categories & Handling

| Error Type | Detection | Handling Strategy |
|------------|-----------|-------------------|
| **Network** | `ECONNREFUSED`, timeout | Retry with backoff |
| **Validation** | Schema errors | Immediate failure, user feedback |
| **Permission** | `EACCES`, 403 | Fail fast, log security event |
| **Resource** | `ENOMEM`, quota exceeded | Graceful degradation |
| **Timeout** | Operation duration > threshold | Cancel operation, cleanup |

## Framework-Specific Patterns

### React Error Boundaries
```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    // Send to error reporting service
    errorReporting.captureException(error, {
      extra: errorInfo
    });
  }

  render() {
    if (this.state.hasError) {
      return (
        <ErrorFallback
          error={this.state.error}
          reset={() => this.setState({ hasError: false, error: null })}
        />
      );
    }
    return this.props.children;
  }
}
```

### Express.js Global Handler
```javascript
// Async route wrapper
const asyncHandler = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};

// Error handling middleware
app.use((error, req, res, next) => {
  // Don't expose stack trace in production
  const isDev = process.env.NODE_ENV === 'development';

  // Handle different error types
  if (error.name === 'ValidationError') {
    return res.status(400).json({
      error: 'Validation failed',
      details: error.details,
      code: 'VALIDATION_ERROR'
    });
  }

  if (error.name === 'UnauthorizedError') {
    return res.status(401).json({
      error: 'Authentication required',
      code: 'UNAUTHORIZED'
    });
  }

  // Log all errors
  console.error(error);

  // Generic error response
  res.status(error.status || 500).json({
    error: isDev ? error.message : 'Internal server error',
    code: error.code || 'INTERNAL_ERROR',
    ...(isDev && { stack: error.stack })
  });
});
```

### Async/Await Error Propagation
```javascript
// Wrap async functions to ensure errors are handled
const withErrorHandling = (fn) => {
  return async (...args) => {
    try {
      return await fn(...args);
    } catch (error) {
      // Add context to error
      error.context = {
        function: fn.name,
        args: args.map(a => typeof a === 'object' ? '[Object]' : a)
      };

      // Re-throw with additional context
      throw error;
    }
  };
};

// Usage
const processData = withErrorHandling(async (data) => {
  // Process data
  return result;
});
```

## Monitoring & Alerting

### Structured Error Logging
```javascript
const logger = winston.createLogger({
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  )
});

// Log errors with full context
logger.error('Payment processing failed', {
  error: error.message,
  stack: error.stack,
  userId: payment.userId,
  amount: payment.amount,
  orderId: payment.orderId,
  paymentMethod: payment.method,
  timestamp: new Date().toISOString(),
  traceId: generateTraceId()
});
```

### Error Metrics
```javascript
class ErrorMetrics {
  constructor() {
    this.counters = new Map();
  }

  record(error, context = {}) {
    const key = `${error.name}:${error.code || 'UNKNOWN'}`;

    if (!this.counters.has(key)) {
      this.counters.set(key, {
        count: 0,
        lastOccurred: null,
        contexts: []
      });
    }

    const metric = this.counters.get(key);
    metric.count++;
    metric.lastOccurred = new Date();
    metric.contexts.push(context);

    // Keep only last 100 contexts
    if (metric.contexts.length > 100) {
      metric.contexts.shift();
    }

    // Alert on threshold
    if (metric.count === 10) {
      this.alert(key, metric);
    }
  }

  alert(errorKey, metric) {
    alerting.send({
      level: 'warning',
      message: `Error ${errorKey} occurred ${metric.count} times`,
      metric
    });
  }
}
```

## Testing Error Handling

```javascript
describe('error handling', () => {
  it('handles network failures gracefully', async () => {
    // Mock network failure
    mockNetwork.mockRejectedValue(new Error('Network error'));

    const result = await service.fetchData();

    expect(result.success).toBe(false);
    expect(result.error).toMatch(/network/i);
  });

  it('retries transient failures', async () => {
    // Fail twice, then succeed
    mockNetwork
      .mockRejectedValueOnce(new Error('Timeout'))
      .mockRejectedValueOnce(new Error('Timeout'))
      .mockResolvedValueOnce({ data: 'success' });

    const result = await service.fetchDataWithRetry();

    expect(result).toEqual({ data: 'success' });
    expect(mockNetwork).toHaveBeenCalledTimes(3);
  });

  it('preserves error context', async () => {
    const error = new Error('Database error');
    error.code = 'DB_CONNECTION_FAILED';

    mockDB.mockRejectedValue(error);

    await expect(service.processOrder('123'))
      .rejects.toThrow('Failed to process order 123: Database error');
  });
});
```