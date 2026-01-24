# Locality
Locality = related data stored close together

Better locality ⇒

- Fewer disk seeks / cache misses

- Faster reads

- Simpler access patterns

One To Many Example
![alt text](Images/image.png)

## Database Relationship Types (Quick Notes)

### 1. One-to-One (1:1)
- One record in A ↔ one record in B  
- Example: Person – Passport  
- Used when splitting sensitive/optional data.

---

### 2. One-to-Many (1:N)
- One record in A → many records in B  
- Example: Customer – Orders  
- Most common relationship.

---

### 3. Many-to-One (N:1)
- Many records in A → one record in B  
- Example: Orders – Customer  
- Reverse view of 1:N.

---

### 4. Many-to-Many (M:N)
- Many records in A ↔ many records in B  
- Example: Students – Courses  
- Requires a junction (bridge) table (e.g., Enrollment).

---

**Tip:**  
1:N and N:1 are the same relationship, just seen from different sides.

**Network (CODASYL) Model:**  
A database model where records are connected by physical pointers, forming a graph (not just a tree), allowing many-to-many relationships.  
Data was accessed by manually navigating predefined paths, which was fast but made queries complex and inflexible.

## Schema-on-Read vs Schema-on-Write

- **Schema-on-Write (Relational DBs):**  
  Schema is enforced before writing data (like static typing).  
  Format changes usually require migrations (e.g., ALTER TABLE + UPDATE).

- **Schema-on-Read (Document/NoSQL DBs):**  
  Schema is applied when reading data (like dynamic typing).  
  New formats can be written immediately; application handles old data.

---

### When the difference matters most
- During **schema evolution** (e.g., splitting `name` → `first_name`, `last_name`)
- Large migrations can be slow; read-time adaptation avoids rewrites.

---

### Schema-on-Read works best when
- Data is **heterogeneous** (different structures in same collection)
- Many object types or frequently changing formats

## Declarative vs Imperative Queries

### Imperative (How)
- Specifies exact steps to get data.
- Algorithm and order are fixed.
- Hard to optimize and parallelize.

### Declarative (What) – SQL
- Specifies only the result pattern.
- Database chooses execution strategy.
- Easier to write and maintain.

---

### Why Declarative is Powerful
- Enables query optimizer to choose best plan.
- Hides storage and indexing details.
- Allows DB to improve performance without changing queries.
- Naturally supports parallel execution.

---

**Key Insight:**  
Declarative queries describe *what you want*, not *how to compute it*, giving the database freedom to optimize and scale.


## Property Graph Model

### What is it?
A **property graph** represents data as:
- **Vertices (nodes)** → entities (people, places, things)
- **Edges (relationships)** → connections between entities  
Both vertices and edges can store **properties (key–value pairs)**.

---

### Core Structure

Each **Vertex** has:
- Unique ID
- Incoming edges
- Outgoing edges
- Properties (JSON)

Each **Edge** has:
- Unique ID
- Tail vertex (start)
- Head vertex (end)
- Label (type of relationship)
- Properties (JSON)

---

### Relational Representation

```sql
CREATE TABLE vertices (
  vertex_id INT PRIMARY KEY,
  properties JSON
);

CREATE TABLE edges (
  edge_id INT PRIMARY KEY,
  tail_vertex INT REFERENCES vertices(vertex_id),
  head_vertex INT REFERENCES vertices(vertex_id),
  label TEXT,
  properties JSON
);

CREATE INDEX edges_tails ON edges(tail_vertex);
CREATE INDEX edges_heads ON edges(head_vertex);




