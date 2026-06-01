# 1. Scaling the Data Layer and Service Design

The current implementation stores orders in an in-memory array, which is suitable only for development and testing. In a production QDC system with thousands of orders and concurrent users, I would replace the in-memory storage with a relational database such as PostgreSQL. I would use an ORM such as Prisma or TypeORM to manage data access and schema migrations.

To improve scalability, I would add database indexes on frequently queried fields such as order ID, customer ID, garment status, and creation date. Pagination would be introduced for large order lists, and caching could be used for frequently accessed data. Service logic should be separated from persistence concerns, making the application easier to maintain and test.

# 2. Improving Error Handling and API Design

Returning either an `Order` object or `{ error: string }` creates inconsistent response types and makes client-side handling more difficult. It also does not leverage HTTP status codes properly.

For a production API, I would use NestJS exceptions such as `NotFoundException`, which automatically return appropriate HTTP status codes. For example, if an order does not exist, the API should return a `404 Not Found` response instead of a custom error object. I would also standardize API responses and include validation, logging, and error tracking for better observability.

# 3. Frontend Architecture for a Growing Dashboard

The current frontend performs data fetching directly inside `App.tsx` using `fetch` within `useEffect`. While this works for a small application, it becomes difficult to maintain as the dashboard grows.

I would introduce an API service layer responsible for all backend communication. Data-fetching logic could be moved into custom hooks or a library such as React Query. This would provide caching, background refetching, loading states, and better error handling. Components would focus on presentation while business logic remains centralized and reusable.

# 4. Domain Model Improvements and Edge Cases

The current `Order` and `Garment` models are intentionally simplified. Real-world laundry operations require additional fields and workflows.

Useful additions include customer phone number, delivery address, payment status, pickup date, delivery date, garment pricing, special instructions, assigned staff, and audit timestamps. Edge cases may include cancelled orders, partially delivered orders, damaged garments, lost garments, or garments requiring re-cleaning. Supporting these scenarios would make the domain model more realistic and production-ready.

# 5. Risks of AI-Generated Code and Review Practices

AI-generated code can accelerate development, but it may introduce incorrect assumptions, hidden bugs, security vulnerabilities, poor error handling, or inefficient logic. The generated code may appear correct while failing in uncommon edge cases.

Before deploying to production, I would perform manual code reviews, run static analysis tools, write unit and integration tests, and validate behavior through manual testing. Logging, monitoring, and peer reviews are also important to ensure reliability and maintainability.

# 6. Adding Near Real-Time Updates

To provide near real-time updates for garment status changes, I would use WebSockets. When a garment changes status, the backend can push updates directly to connected clients, allowing dashboards to refresh instantly without manual reloads.

An alternative approach is periodic polling, where the frontend requests updated data every few seconds. Polling is simpler to implement but generates unnecessary network traffic and introduces latency. WebSockets require more infrastructure and connection management but provide a better user experience for real-time operational dashboards.
