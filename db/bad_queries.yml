-- Save in your throwaway test repo as: db/bad_queries.sql
-- Each query below should trigger a specific qcost rule.
-- Add this file in a PR; the qcost comment should flag every one.

-- 1. SELECT * + full table scan (no WHERE, no LIMIT)
--    expect: SELECT_STAR (+15), FULL_TABLE_SCAN_NO_WHERE (+30)
SELECT * FROM users;

-- 2. Cartesian join — comma join with no ON condition
--    expect: CARTESIAN_JOIN (+40)
SELECT u.id, o.id
FROM users u, orders o;

-- 3. JOIN with no ON clause
--    expect: MISSING_JOIN_CONDITION (+35)
SELECT u.id
FROM users u
CROSS JOIN orders o;

-- 4. Leading wildcard LIKE — cannot use a B-tree index
--    expect: LEADING_WILDCARD_LIKE (+25)
SELECT id, email
FROM users
WHERE email LIKE '%@example.com';

-- 5. Function wrapping an indexed column
--    expect: FUNCTION_ON_COLUMN (+20)
SELECT id
FROM users
WHERE LOWER(email) = 'alice@example.com';

-- 6. Correlated subquery in WHERE
--    expect: SUBQUERY_IN_WHERE (+15)
SELECT id
FROM orders o
WHERE o.user_id IN (SELECT id FROM users WHERE created_at > '2026-01-01');

-- 7. ORDER BY with no LIMIT — full sort of the result set
--    expect: ORDER_BY_NO_LIMIT (+8)
SELECT id, created_at
FROM events
ORDER BY created_at DESC;

-- 8. Implicit type cast — integer column compared to a string literal
--    expect: IMPLICIT_CAST (+10)
SELECT id
FROM users
WHERE id = '123';

-- 9. OR in WHERE
--    expect: OR_IN_WHERE (+5)
SELECT id
FROM users
WHERE email = 'a@example.com' OR username = 'alice';


-- ── Control case ──────────────────────────────────────────────────────────
-- This query is fine. If qcost flags it, that's a false positive worth
-- investigating before you launch.
SELECT id, email
FROM users
WHERE id = 42
LIMIT 1;