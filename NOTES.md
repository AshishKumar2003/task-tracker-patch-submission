# Patch Notes

### Summary of Changes
1. **SQL Operator Precedence (`TaskRepository.java` & `task_search_pkg.sql`):** Added explicit parentheses around `(LOWER(title) LIKE :term OR LOWER(description) LIKE :term)`. Fixed critical data leak where archived tasks bypassed the query and status filters failed on title matches.
2. **Artificial Delay Removal (`TaskController.java`):** Removed synthetic `Thread.sleep` (up to 1000ms delay) that caused asynchronous out-of-order race conditions on the frontend.
3. **Frontend Race Condition Fix (`useTasks.js`):** Added an active cancellation flag in `useEffect` and ensured `setLoading(false)` always executes on error.
4. **Pagination Reset (`App.jsx`):** Reset current page to 1 on query and status changes to prevent showing empty pages.

### What I Chose Not to Change & Why
- Maintained in-memory slicing via `subList` for the timebox scope rather than rewriting repository layer with Pageable and custom count queries.
- Did not add third-party state libraries (Redux/Zustand) as clean React state handles the current scope reliably.

### Biggest Remaining Risk
- **In-Memory Slicing Scalability:** Fetching all matching records into memory before pagination causes severe JVM heap pressure on large datasets. Needs database-level LIMIT/OFFSET in production.

### Tools & AI Usage
- Used AI to audit native SQL operator precedence and verify Oracle PL/SQL reference syntax.
