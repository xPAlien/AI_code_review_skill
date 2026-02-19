# Common AI Code Failures

These are the patterns AI gets wrong most frequently. When reviewing AI-generated
code, check for these first. They account for the majority of production incidents
traced back to AI-assisted development.

---

## 1. Missing Debounce on User Input

AI writes event handlers that fire on every keystroke, mouse move, or scroll event.
At low traffic this is invisible. At scale it saturates your backend.

Bad pattern:
```javascript
searchInput.addEventListener('input', async (e) => {
  const results = await fetch(`/api/search?q=${e.target.value}`);
  displayResults(await results.json());
});
```

Fix: Add a 300ms debounce minimum. For search, 300-500ms. For scroll events, use
throttle instead of debounce.

---

## 2. No Connection Pooling

AI creates a new database connection per request. This works for a single developer
testing locally. At 500 concurrent users, the database rejects new connections.

Bad pattern:
```python
def get_user(user_id):
    conn = psycopg2.connect(DATABASE_URL)
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
    return cursor.fetchone()
```

Fix: Use a connection pool. Set max pool size below your database's connection limit.
Monitor pool exhaustion.

---

## 3. N+1 Query Pattern

AI writes a query to get a list, then loops through results making one query per
item. 100 items = 101 queries. This is the most common performance killer in
AI-generated backend code.

Bad pattern:
```python
orders = db.query("SELECT * FROM orders WHERE user_id = ?", user_id)
for order in orders:
    items = db.query("SELECT * FROM order_items WHERE order_id = ?", order.id)
    order.items = items
```

Fix: Use a JOIN or a single IN query to fetch all related records in one call.

---

## 4. Swallowed Errors

AI wraps code in try/catch and either logs nothing or logs a generic message. The
error disappears. Production fails silently.

Bad pattern:
```javascript
try {
  await processPayment(order);
} catch (error) {
  console.log("Something went wrong");
}
```

Fix: Log the full error with stack trace, request context, and correlation ID.
Decide explicitly whether to retry, fail loudly, or degrade gracefully.

---

## 5. String-Concatenated SQL

AI still generates SQL with string interpolation despite parameterized queries
being available in every language. This is a direct SQL injection vulnerability.

Bad pattern:
```python
query = f"SELECT * FROM users WHERE email = '{email}'"
```

Fix: Use parameterized queries. No exceptions. Ever.

---

## 6. Hardcoded Secrets

AI places API keys, database URLs, and tokens directly in source code. The
developer fills in real values and commits them to version control.

Bad pattern:
```python
API_KEY = "sk-live-abc123..."
```

Fix: Use environment variables at minimum. Use a secrets manager in production.
Run secret scanning tools (truffleHog, gitleaks) in CI.

---

## 7. Missing Authorization Checks

AI adds authentication (who are you?) but skips authorization (are you allowed to
access this specific resource?). User A fetches User B's data by changing an ID
in the URL.

Bad pattern:
```python
@require_auth
def get_document(request, doc_id):
    return Document.objects.get(id=doc_id)  # No check: does this user own this doc?
```

Fix: Every data access must verify the requesting user has permission for that
specific resource. Filter queries by user ID or check ownership explicitly.

---

## 8. No Rate Limiting

AI never adds rate limiting. Your API becomes a free resource for scrapers, bots,
and abusers. A single bad actor sends 10,000 requests per second.

Fix: Add rate limiting at the API gateway or application layer. Start with
100 requests per minute per user. Adjust based on your use case.

---

## 9. Permissive CORS

AI-generated APIs often ship with Access-Control-Allow-Origin set to wildcard.
This allows any website to make requests to your API.

Bad pattern:
```python
CORS(app, resources={r"/*": {"origins": "*"}})
```

Fix: Restrict origins to your known domains. Never use wildcard in production.

---

## 10. No Timeout on External Calls

AI makes HTTP calls to external services with no timeout. If the external service
hangs, your request hangs. Your thread pool fills up. Your application stops
responding.

Bad pattern:
```python
response = requests.get("https://external-api.com/data")
```

Fix: Set explicit timeouts on every external call. 5 seconds for reads, 10 seconds
for writes is a reasonable starting point. Add circuit breaker logic for repeated
failures.
