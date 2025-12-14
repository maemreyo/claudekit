# Brainstorming Examples

This file contains complete worked examples showing how the Brainstormer Agent analyzes real problems.

<!-- Last updated: 2024-12-14 -->
<!-- Note: Technologies and patterns evolve. Review examples periodically. -->

---

## Example 1: API Caching Design

### User Request
"Design a caching strategy for our REST API. We're seeing high database load and slow response times."

### Step 0: Context Gathering
**Tools used:**
- `Glob("src/api/**/*.{js,ts}")` - Found 45 API endpoint files
- `Read("src/api/users/controller.ts")` - Sample endpoint makes 3 DB queries per request
- `Read("package.json")` - Currently using Express, PostgreSQL, no caching layer
- `WebSearch("REST API caching best practices 2024")` - Redis most common, CDN for static data

### Step 1: Understand the Problem
- **Core challenge**: High DB load causing 2-3s response times (target: <200ms)
- **Constraints**: 
  - Budget: $500/month for caching infrastructure
  - Team expertise: JavaScript, basic DevOps
  - Data: Mix of user-specific and frequently-accessed public data
- **Assumed constraint challenged**: "Must cache everything" → Actually only 20% of endpoints cause 80% of load

### Step 2: Generate Options

#### Option 1: Redis In-memory Cache
**Description**: Add Redis layer between API and PostgreSQL
**Pros**: 
- Fast (sub-ms lookups)
- Mature ecosystem
- Flexible TTL strategies
**Cons**: 
- Additional infrastructure cost (~$200/month)
- Cache invalidation complexity
- Learning curve for team
**Effort**: Medium (2-3 weeks)

#### Option 2: CDN Edge Caching
**Description**: Use Cloudflare/CloudFront to cache responses at edge
**Pros**: 
- Extremely fast for end-users
- Handles geographic distribution
- Low cost (~$50/month)
**Cons**: 
- Only works for public, non-personalized data
- Cache purging takes minutes
- Limited control over invalidation
**Effort**: Low (1 week)

#### Option 3: Application-level In-memory Cache
**Description**: Use Node.js `node-cache` or similar for in-process caching
**Pros**: 
- Zero infrastructure cost
- Simple implementation
- No network latency
**Cons**: 
- Cache not shared across instances (inconsistency)
- Memory limited per instance
- Lost on deployment
**Effort**: Low (3-4 days)

#### Option 4: Hybrid (Redis + CDN)
**Description**: Redis for user-specific, CDN for public data
**Pros**: 
- Best of both worlds
- Optimizes costs (CDN cheaper for public data)
- Clear separation of concerns
**Cons**: 
- Most complex to implement
- Two systems to monitor
- Higher initial effort
**Effort**: High (4-5 weeks)

#### Option 5: Database Query Optimization (No cache)
**Description**: Add indexes, optimize queries, use materialized views
**Pros**: 
- No new infrastructure
- Addresses root cause
- No cache invalidation issues
**Cons**: 
- May not achieve target latency
- Limited scalability ceiling
- Still hits DB for every request
**Effort**: Medium (2-3 weeks)

### Wild Card Ideas
- **GraphQL with DataLoader**: Batch and cache in single request cycle
- **Pre-compute common queries**: Nightly job to populate cache with popular data
- **WebSocket + Push updates**: Eliminate need for frequent polling requests

### Step 3: Comparison Matrix

| Criteria | Redis | CDN | In-memory | Hybrid | DB Optimization |
|----------|-------|-----|-----------|--------|-----------------|
| Latency Improvement | 5 | 5 | 4 | 5 | 3 |
| Cost | 3 | 5 | 5 | 2 | 5 |
| Complexity | 3 | 4 | 5 | 2 | 4 |
| Scalability | 5 | 5 | 2 | 5 | 3 |
| Team Fit | 4 | 4 | 5 | 3 | 5 |
| **Total** | **20** | **23** | **21** | **17** | **20** |

### Recommendation: Start with CDN + DB Optimization, then add Redis

**Rationale**:
1. **Phase 1 (Week 1-2)**: Implement CDN for public endpoints (40% of traffic) + optimize slow queries
   - Quick wins with minimal risk
   - Learn from production before bigger investment
2. **Phase 2 (Week 3-5)**: Add Redis for user-specific endpoints if targets not met
   - Data-driven decision based on Phase 1 metrics
   - Justified infrastructure cost with clear ROI

### Validation Artifact (Pseudo-code)

```javascript
// Phase 1: CDN + Optimization
// In API middleware
app.use((req, res, next) => {
  if (isPublicEndpoint(req.path)) {
    res.set('Cache-Control', 'public, max-age=300'); // 5min CDN cache
  } else {
    res.set('Cache-Control', 'private, no-cache');
  }
  next();
});

// Optimized query example
// Before: 3 separate queries
// After: 1 JOIN query with index
const getUserWithPosts = await db.query(`
  SELECT u.*, array_agg(p.*) as posts
  FROM users u
  LEFT JOIN posts p ON p.user_id = u.id
  WHERE u.id = $1
  GROUP BY u.id
`, [userId]);

// Phase 2: Redis layer (if needed)
const cachedUser = await redis.get(`user:${userId}`);
if (cachedUser) return JSON.parse(cachedUser);

const dbUser = await getUserWithPosts(userId);
await redis.setex(`user:${userId}`, 3600, JSON.stringify(dbUser));
return dbUser;
```

---

## Example 2: Performance Optimization

### User Request
"Our application is slow under load. 5,000 concurrent users crashes the system."

### Context Gathering
- `Read("monitoring/grafana-dashboard.json")` - CPU spikes to 95%, DB connections maxed
- `Read("src/services/report-generator.ts")` - Heavy computation in request path
- `WebSearch("Node.js performance bottlenecks 2024")` - Common issues: synchronous ops, memory leaks, N+1 queries

### Approaches Generated

1. **Database Layer**: Connection pooling, read replicas, query optimization
2. **Code Layer**: Move heavy computation to background jobs, async processing
3. **Infrastructure Layer**: Horizontal scaling, load balancing, CDN
4. **Caching Layer**: Redis for computed results, reduce DB load

### Recommendation
**Immediate**: Move report generation to async queue (BullMQ) - Highest impact/effort ratio  
**Short-term**: Add read replicas for queries  
**Long-term**: Implement caching for frequently-generated reports  

### Validation Artifact
```javascript
// Before: Synchronous in request
app.get('/report/:id', async (req, res) => {
  const report = await generateReport(req.params.id); // 30+ seconds!
  res.json(report);
});

// After: Async job
app.post('/report/:id/generate', async (req, res) => {
  const job = await reportQueue.add({ reportId: req.params.id });
  res.json({ jobId: job.id, status: 'processing' });
});

app.get('/report/:id/status', async (req, res) => {
  const job = await reportQueue.getJob(req.params.id);
  res.json({ status: job.status, progress: job.progress });
});
```

---

## Example 3: Architecture Decision

### User Request
"Should we move to microservices? Our monolith is getting hard to manage."

### Context Gathering
- Team size: 8 developers
- Codebase: 150k LOC, 2 years old
- Current pain: Deployments risky, modules tightly coupled, test suite takes 20 minutes

### Approaches Generated

1. **Stay Monolith**: Refactor into modules, improve boundaries
2. **Microservices**: Full decomposition into 8-10 services
3. **Modular Monolith**: Enforce module boundaries, prepare for extraction
4. **Strangler Pattern**: Gradually extract services starting with most independent

### Decision Guide

| Team Size | Recommendation |
|-----------|----------------|  
| < 10 devs | Modular Monolith |
| 10-30 devs | Selective Microservices (2-4 services) |
| 30+ devs | Full Microservices |

**Recommendation for this case**: **Modular Monolith** with preparation for future extraction

### Validation Artifact
```
src/
├── modules/
│   ├── users/
│   │   ├── domain/      # Business logic (no external deps)
│   │   ├── api/         # Controllers
│   │   └── infra/       # DB, external services
│   ├── billing/
│   └── reporting/
├── shared/
│   └── kernel/          # Shared primitives only
└── app.ts               # Wiring
```

Each module has:
- Clear public API (`index.ts`)
- No cross-module imports (except via public API)
- Independent tests
- Deployment ability (when ready to extract)
