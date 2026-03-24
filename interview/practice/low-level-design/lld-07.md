**7.** Complex SQL Query Builder — Builder pattern. Dynamic SQL with optional SELECT, FROM, WHERE, JOIN, GROUP BY, ORDER BY, HAVING. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Complex SQL Query Builder — Builder Pattern

### Pattern: Builder (Fluent API)

**Why Builder?** SQL queries have many optional clauses in a specific order. Constructors with 7+ parameters are unreadable. Builder provides a fluent, readable API that enforces valid SQL structure.

### Text UML

```
┌─────────────────────────────────┐
│         SqlQuery                │
├─────────────────────────────────┤
│ - selectColumns: List<String>   │
│ - table: String                 │
│ - joins: List<JoinClause>       │
│ - conditions: List<String>      │
│ - groupByColumns: List<String>  │
│ - havingConditions: List<String>│
│ - orderByColumns: List<String>  │
│ - limit: Integer                │
│ - offset: Integer               │
│ - parameters: List<Object>      │
├─────────────────────────────────┤
│ + toSql(): String               │
│ + getParameters(): List<Object> │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│       SqlQueryBuilder           │
├─────────────────────────────────┤
│ + select(columns): Builder      │
│ + from(table): Builder          │
│ + join(table, on): Builder      │
│ + leftJoin(table, on): Builder  │
│ + where(condition): Builder     │
│ + and(condition): Builder       │
│ + or(condition): Builder        │
│ + groupBy(columns): Builder     │
│ + having(condition): Builder    │
│ + orderBy(column, dir): Builder │
│ + limit(n): Builder             │
│ + offset(n): Builder            │
│ + build(): SqlQuery             │
└─────────────────────────────────┘
```

### Key Design Decisions

1. **Immutable SqlQuery** — Builder constructs; query object is read-only after build
2. **Parameterized queries** — Store `?` placeholders + parameters list to prevent SQL injection
3. **Fluent API** — Each method returns `this` for chaining
4. **Validation in build()** — Ensure required clauses (SELECT, FROM) are present before creating query

### SOLID Principles Applied

- **SRP**: Builder handles construction; SqlQuery holds the result
- **OCP**: Add new clauses (UNION, WITH) by extending builder
- **ISP**: Client only calls the methods they need — all optional except SELECT and FROM

### Java Code

```java
import java.util.*;

// --- Join Type Enum ---
public enum JoinType {
    INNER("JOIN"), LEFT("LEFT JOIN"), RIGHT("RIGHT JOIN"), FULL("FULL JOIN");

    private final String sql;
    JoinType(String sql) { this.sql = sql; }
    public String getSql() { return sql; }
}

// --- Join Clause ---
public class JoinClause {
    private final JoinType type;
    private final String table;
    private final String onCondition;

    public JoinClause(JoinType type, String table, String onCondition) {
        this.type = type;
        this.table = table;
        this.onCondition = onCondition;
    }

    public String toSql() {
        return type.getSql() + " " + table + " ON " + onCondition;
    }
}

// --- Immutable SQL Query ---
public class SqlQuery {
    private final String sql;
    private final List<Object> parameters;

    SqlQuery(String sql, List<Object> parameters) {
        this.sql = sql;
        this.parameters = Collections.unmodifiableList(parameters);
    }

    public String toSql() { return sql; }
    public List<Object> getParameters() { return parameters; }

    @Override
    public String toString() {
        return sql + " | params=" + parameters;
    }
}

// --- Builder ---
public class SqlQueryBuilder {
    private final List<String> selectColumns = new ArrayList<>();
    private String fromTable;
    private final List<JoinClause> joins = new ArrayList<>();
    private final List<String> whereConditions = new ArrayList<>();
    private final List<String> groupByColumns = new ArrayList<>();
    private final List<String> havingConditions = new ArrayList<>();
    private final List<String> orderByColumns = new ArrayList<>();
    private Integer limit;
    private Integer offset;
    private final List<Object> parameters = new ArrayList<>();

    public SqlQueryBuilder select(String... columns) {
        selectColumns.addAll(Arrays.asList(columns));
        return this;
    }

    public SqlQueryBuilder from(String table) {
        this.fromTable = table;
        return this;
    }

    public SqlQueryBuilder join(String table, String onCondition) {
        joins.add(new JoinClause(JoinType.INNER, table, onCondition));
        return this;
    }

    public SqlQueryBuilder leftJoin(String table, String onCondition) {
        joins.add(new JoinClause(JoinType.LEFT, table, onCondition));
        return this;
    }

    public SqlQueryBuilder where(String condition, Object... params) {
        whereConditions.add(condition);
        parameters.addAll(Arrays.asList(params));
        return this;
    }

    public SqlQueryBuilder and(String condition, Object... params) {
        return where(condition, params);
    }

    public SqlQueryBuilder or(String condition, Object... params) {
        if (!whereConditions.isEmpty()) {
            // Wrap previous conditions in parentheses for OR
            String last = whereConditions.remove(whereConditions.size() - 1);
            whereConditions.add("(" + last + " OR " + condition + ")");
        } else {
            whereConditions.add(condition);
        }
        parameters.addAll(Arrays.asList(params));
        return this;
    }

    public SqlQueryBuilder groupBy(String... columns) {
        groupByColumns.addAll(Arrays.asList(columns));
        return this;
    }

    public SqlQueryBuilder having(String condition, Object... params) {
        havingConditions.add(condition);
        parameters.addAll(Arrays.asList(params));
        return this;
    }

    public SqlQueryBuilder orderBy(String column, String direction) {
        orderByColumns.add(column + " " + direction.toUpperCase());
        return this;
    }

    public SqlQueryBuilder orderBy(String column) {
        return orderBy(column, "ASC");
    }

    public SqlQueryBuilder limit(int limit) {
        this.limit = limit;
        return this;
    }

    public SqlQueryBuilder offset(int offset) {
        this.offset = offset;
        return this;
    }

    public SqlQuery build() {
        if (selectColumns.isEmpty()) throw new IllegalStateException("SELECT is required");
        if (fromTable == null) throw new IllegalStateException("FROM is required");

        StringBuilder sql = new StringBuilder();

        // SELECT
        sql.append("SELECT ").append(String.join(", ", selectColumns));

        // FROM
        sql.append(" FROM ").append(fromTable);

        // JOINs
        for (JoinClause join : joins) {
            sql.append(" ").append(join.toSql());
        }

        // WHERE
        if (!whereConditions.isEmpty()) {
            sql.append(" WHERE ").append(String.join(" AND ", whereConditions));
        }

        // GROUP BY
        if (!groupByColumns.isEmpty()) {
            sql.append(" GROUP BY ").append(String.join(", ", groupByColumns));
        }

        // HAVING
        if (!havingConditions.isEmpty()) {
            sql.append(" HAVING ").append(String.join(" AND ", havingConditions));
        }

        // ORDER BY
        if (!orderByColumns.isEmpty()) {
            sql.append(" ORDER BY ").append(String.join(", ", orderByColumns));
        }

        // LIMIT & OFFSET
        if (limit != null) sql.append(" LIMIT ").append(limit);
        if (offset != null) sql.append(" OFFSET ").append(offset);

        return new SqlQuery(sql.toString(), parameters);
    }
}
```

### Usage

```java
SqlQuery query = new SqlQueryBuilder()
    .select("u.name", "u.email", "COUNT(o.id) AS order_count", "SUM(o.total) AS total_spent")
    .from("users u")
    .leftJoin("orders o", "u.id = o.user_id")
    .where("u.status = ?", "ACTIVE")
    .and("u.created_at > ?", "2024-01-01")
    .groupBy("u.name", "u.email")
    .having("COUNT(o.id) > ?", 5)
    .orderBy("total_spent", "DESC")
    .limit(20)
    .offset(0)
    .build();

System.out.println(query.toSql());
// SELECT u.name, u.email, COUNT(o.id) AS order_count, SUM(o.total) AS total_spent
// FROM users u LEFT JOIN orders o ON u.id = o.user_id
// WHERE u.status = ? AND u.created_at > ?
// GROUP BY u.name, u.email HAVING COUNT(o.id) > ?
// ORDER BY total_spent DESC LIMIT 20 OFFSET 0

System.out.println(query.getParameters()); // [ACTIVE, 2024-01-01, 5]
```

### Interview Talking Points

- **SQL injection prevention**: Parameterized queries with `?` placeholders — never concatenate user input
- **Production**: Spring's `JdbcTemplate` and JPA `CriteriaBuilder` use similar builder patterns. MyBatis has `<if>` tags for dynamic SQL
- **Validation**: `build()` validates required clauses — fail fast vs producing invalid SQL
- **Immutability**: `SqlQuery` is immutable once built — safe to pass around, cache, log
