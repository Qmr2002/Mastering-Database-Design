### **Advanced SQL: Complex Queries, Indexing, and Optimization**

This guide explores advanced SQL concepts, including **complex queries**, **indexing**, and **query optimization**, with examples to help you improve database performance and handle large datasets efficiently.

---

### **1. Complex Queries**

Complex queries involve multiple operations such as joins, subqueries, aggregations, and window functions. These queries are powerful for advanced data analysis.

---

#### **a. Joins**
Joins combine rows from two or more tables based on related columns.

**Example: Retrieve Bookings with Property and User Details**
**SQL Query**:
```sql
SELECT 
    Booking.booking_id,
    Property.name AS property_name,
    User.first_name || ' ' || User.last_name AS guest_name,
    Booking.start_date,
    Booking.end_date
FROM 
    Booking
INNER JOIN Property ON Booking.property_id = Property.property_id
INNER JOIN User ON Booking.user_id = User.user_id;
```

**Explanation**:
- Combines `Booking`, `Property`, and `User` tables.
- Retrieves details about bookings, including the property name and the guest's full name.

---

#### **b. Subqueries**
Subqueries are queries nested within another query.

**Example: Fetch Properties with More Than 5 Bookings**
**SQL Query**:
```sql
SELECT 
    Property.name, 
    Property.location
FROM 
    Property
WHERE 
    property_id IN (
        SELECT 
            property_id 
        FROM 
            Booking
        GROUP BY 
            property_id
        HAVING 
            COUNT(*) > 5
    );
```

**Explanation**:
- The inner query calculates properties with more than 5 bookings.
- The outer query fetches property details for those IDs.

---

#### **c. Aggregations**
Aggregations summarize data using functions like `COUNT`, `SUM`, and `AVG`.

**Example: Calculate Average Rating for Each Property**
**SQL Query**:
```sql
SELECT 
    Property.name AS property_name,
    AVG(Review.rating) AS average_rating
FROM 
    Property
INNER JOIN Review ON Property.property_id = Review.property_id
GROUP BY 
    Property.property_id, Property.name;
```

**Explanation**:
- Joins `Property` and `Review`.
- Groups by property and calculates the average rating.

---

#### **d. Window Functions**
Window functions calculate results across a set of rows related to the current row.

**Example: Rank Properties by Average Rating**
**SQL Query**:
```sql
SELECT 
    Property.name AS property_name,
    AVG(Review.rating) OVER (PARTITION BY Property.property_id) AS average_rating,
    RANK() OVER (ORDER BY AVG(Review.rating) DESC) AS rank
FROM 
    Property
INNER JOIN Review ON Property.property_id = Review.property_id;
```

**Explanation**:
- Partitions rows by property ID to calculate the average rating.
- Ranks properties by their average rating.

---

### **2. Indexing**

Indexes improve the speed of data retrieval by creating a data structure for efficient lookups.

#### **a. Create Indexes**
**SQL Query**:
```sql
CREATE INDEX idx_property_name ON Property(name);
CREATE INDEX idx_booking_dates ON Booking(start_date, end_date);
```

**Explanation**:
- Index on `Property.name` speeds up searches for property names.
- Composite index on `Booking` dates optimizes date range queries.

---

#### **b. Monitor Index Performance**
Use `EXPLAIN` to analyze query execution plans.

**Example: Analyzing Query Performance**
**SQL Query**:
```sql
EXPLAIN SELECT * FROM Property WHERE name = 'Cozy Cabin';
```

**Output**:
Shows whether an index is being used to speed up the query.

---

### **3. Optimization**

#### **a. Query Refactoring**
Rewriting queries to reduce complexity improves performance.

**Example: Avoid Using SELECT \***  
Instead of:
```sql
SELECT * FROM Property WHERE location = 'New York';
```

Use:
```sql
SELECT property_id, name, pricepernight FROM Property WHERE location = 'New York';
```

**Explanation**:
- Fetch only the necessary columns to reduce data retrieval load.

---

#### **b. Use Execution Plans**
Analyze queries using `EXPLAIN` or `ANALYZE`.

**SQL Query**:
```sql
EXPLAIN ANALYZE
SELECT 
    Booking.booking_id,
    User.email
FROM 
    Booking
JOIN User ON Booking.user_id = User.user_id
WHERE 
    Booking.start_date > '2024-01-01';
```

**Output**:
Provides details about the query's performance and whether indexes are used.

---

#### **c. Optimize WHERE and LIKE Clauses**
Avoid using wildcards at the beginning of strings.

**Inefficient**:
```sql
SELECT * FROM Property WHERE name LIKE '%Cabin%';
```

**Optimized**:
```sql
SELECT * FROM Property WHERE name LIKE 'Cozy%';
```

---

#### **d. Partitioning**
Partition large tables into smaller pieces for better performance.

**Example: Partition Booking Table by Year**
**SQL Query**:
```sql
CREATE TABLE Booking_2024 PARTITION OF Booking FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');
```

---

#### **e. Denormalization**
In some cases, adding redundant data improves read performance.

**Example: Add Total Bookings Column to Property Table**
**SQL Query**:
```sql
ALTER TABLE Property ADD COLUMN total_bookings INT;

UPDATE Property
SET total_bookings = (
    SELECT COUNT(*) FROM Booking WHERE Booking.property_id = Property.property_id
);
```

**Explanation**:
- Denormalization adds `total_bookings` for faster access, but requires periodic updates.

---

### **Practical Steps for Learners**

1. **Practice Complex Queries**:
   - Use joins, subqueries, and window functions to answer real-world questions.

2. **Implement Indexing**:
   - Add indexes to columns frequently used in `WHERE`, `JOIN`, and `ORDER BY` clauses.

3. **Optimize Queries**:
   - Use `EXPLAIN` to analyze and refactor inefficient queries.
   - Avoid unnecessary data retrieval by selecting specific columns.

4. **Monitor Performance**:
   - Use tools like `pgAdmin`, SolarWinds DPA, or New Relic for database profiling.

---

Here’s how to apply **advanced SQL features** such as joins, aggregations, indexing, and optimization using **Python with SQLAlchemy**. These examples illustrate best practices and techniques for improving query performance.

---

### **1. Complex Queries in SQLAlchemy**

#### **a. Joins**

**Example: Fetch Bookings with Property and User Details**
```python
from sqlalchemy.orm import aliased

# Fetch bookings with property and user details
results = (
    session.query(
        Booking.booking_id,
        Property.name.label("property_name"),
        User.first_name.label("guest_first_name"),
        User.last_name.label("guest_last_name"),
        Booking.start_date,
        Booking.end_date
    )
    .join(Property, Booking.property_id == Property.property_id)
    .join(User, Booking.user_id == User.user_id)
    .all()
)

for result in results:
    print(
        f"Booking ID: {result.booking_id}, Property: {result.property_name}, "
        f"Guest: {result.guest_first_name} {result.guest_last_name}, "
        f"Start: {result.start_date}, End: {result.end_date}"
    )
```

- **Explanation**:
  - `.join(Property, Booking.property_id == Property.property_id)`: Joins the `Booking` and `Property` tables.
  - `.join(User, Booking.user_id == User.user_id)`: Joins the `Booking` and `User` tables.

---

#### **b. Subqueries**

**Example: Fetch Properties with More Than 5 Bookings**
```python
from sqlalchemy import func, select

# Subquery to count bookings per property
subquery = (
    session.query(
        Booking.property_id,
        func.count(Booking.booking_id).label("total_bookings")
    )
    .group_by(Booking.property_id)
    .having(func.count(Booking.booking_id) > 5)
    .subquery()
)

# Main query to fetch property details
results = (
    session.query(Property.name, Property.location)
    .join(subquery, Property.property_id == subquery.c.property_id)
    .all()
)

for result in results:
    print(f"Property: {result.name}, Location: {result.location}")
```

- **Explanation**:
  - `subquery`: Counts bookings per property and filters those with more than 5 bookings.
  - `.join(subquery)`: Combines the result of the subquery with the `Property` table.

---

#### **c. Aggregations**

**Example: Calculate Average Rating for Each Property**
```python
from sqlalchemy import func

# Average rating per property
results = (
    session.query(
        Property.name.label("property_name"),
        func.avg(Review.rating).label("average_rating")
    )
    .join(Review, Property.property_id == Review.property_id)
    .group_by(Property.property_id, Property.name)
    .all()
)

for result in results:
    print(f"Property: {result.property_name}, Average Rating: {result.average_rating:.2f}")
```

- **Explanation**:
  - `.group_by`: Groups ratings by property.
  - `func.avg(Review.rating)`: Calculates the average rating.

---

#### **d. Window Functions**

**Example: Rank Properties by Average Rating**
```python
from sqlalchemy import func
from sqlalchemy.sql import over

# Rank properties by average rating
results = (
    session.query(
        Property.name.label("property_name"),
        func.avg(Review.rating).over(partition_by=Property.property_id).label("average_rating"),
        func.rank().over(order_by=func.avg(Review.rating).desc()).label("rank")
    )
    .join(Review, Property.property_id == Review.property_id)
    .all()
)

for result in results:
    print(f"Property: {result.property_name}, Average Rating: {result.average_rating:.2f}, Rank: {result.rank}")
```

- **Explanation**:
  - `over(partition_by=...)`: Calculates average rating per property.
  - `rank().over(order_by=...)`: Assigns a rank based on the average rating.

---

### **2. Indexing in SQLAlchemy**

#### **Creating Indexes**
Indexes improve query performance for frequently queried columns.

**Example: Adding Indexes to User Table**
```python
from sqlalchemy import Index

# Add index on User email
Index("idx_user_email", User.email)

# Add composite index on Booking start_date and end_date
Index("idx_booking_dates", Booking.start_date, Booking.end_date)
```

- **Explanation**:
  - `Index("index_name", columns)`: Defines single or composite indexes.

#### **Analyzing Query Execution with EXPLAIN**
```python
from sqlalchemy import text

# Analyze a query's execution plan
query = text("EXPLAIN SELECT * FROM Property WHERE name = 'Cozy Cabin'")
result = session.execute(query)
for row in result:
    print(row)
```

- **Explanation**:
  - `EXPLAIN`: Shows whether indexes are being used for query execution.

---

### **3. Optimization in SQLAlchemy**

#### **a. Avoid SELECT \***  
Select only the necessary columns to reduce data retrieval load.

**Example: Fetch Specific Columns**
```python
# Fetch only required columns
results = session.query(Property.name, Property.pricepernight).filter(Property.location == "New York").all()

for result in results:
    print(f"Property: {result.name}, Price: {result.pricepernight}")
```

---

#### **b. Refactor Query with Common Table Expressions (CTEs)**

**Example: Use CTE to Simplify Complex Queries**
```python
from sqlalchemy.orm import aliased

# Define CTE for bookings count
cte = (
    session.query(
        Booking.property_id,
        func.count(Booking.booking_id).label("total_bookings")
    )
    .group_by(Booking.property_id)
    .cte("booking_count_cte")
)

# Use CTE in main query
results = (
    session.query(Property.name, cte.c.total_bookings)
    .join(cte, Property.property_id == cte.c.property_id)
    .all()
)

for result in results:
    print(f"Property: {result.name}, Total Bookings: {result.total_bookings}")
```

- **Explanation**:
  - `.cte`: Defines a reusable Common Table Expression.
  - Simplifies queries by modularizing intermediate calculations.

---

#### **c. Partitioning and Query Optimization**
Partitioning large tables can improve query performance by dividing them into smaller pieces.

**Example: Partition Booking Table by Year**
```sql
-- Create partitioned table in SQL
CREATE TABLE Booking_2024 PARTITION OF Booking FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');
```

Partitioning can be used in SQLAlchemy by manually targeting appropriate partitions using dynamic queries.

---

#### **d. Use Query Execution Plan**
Execution plans help identify bottlenecks in query performance.

**SQL Query**:
```sql
EXPLAIN ANALYZE
SELECT 
    Property.name,
    AVG(Review.rating)
FROM 
    Property
JOIN 
    Review ON Property.property_id = Review.property_id
GROUP BY 
    Property.property_id;
```

---

### **Practical Steps**

1. **Write Advanced Queries**:
   - Use SQLAlchemy to write optimized joins, subqueries, and aggregations.
   - Leverage window functions for ranking or cumulative calculations.

2. **Monitor Index Performance**:
   - Use `EXPLAIN` in raw SQL or profiling tools in SQLAlchemy.

3. **Optimize Incrementally**:
   - Start by indexing frequently used columns.
   - Refactor complex queries into CTEs or modular subqueries.

---
