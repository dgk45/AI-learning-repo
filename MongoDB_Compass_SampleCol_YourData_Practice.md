# MongoDB Compass Practice Guide — `SampleCol` (Your Data)

Based on the document you actually inserted:

```js
{
  name: 'Alice',
  age: NumberInt('28'),
  city: 'Chennai',
  department: 'Engineering',
  salary: NumberInt('52000'),
  isActive: true,
  skills: [
    'JavaScript',
    'Node.js'
  ]
}
```

Below is a matching set of sample documents (same schema/style) plus step-by-step
queries to practice on, all built around this exact structure.

---

## Step 1: Insert Matching Sample Data

In `SampleCol` → **Documents** tab → **Add Data → Insert Document** → JSON view →
paste one at a time, or paste the whole array below.

```json
[
  { "name": "Alice",  "age": 28, "city": "Chennai",   "department": "Engineering", "salary": 52000, "isActive": true,  "skills": ["JavaScript", "Node.js"] },
  { "name": "Bob",    "age": 34, "city": "Bangalore", "department": "Engineering", "salary": 78000, "isActive": true,  "skills": ["Python", "MongoDB"] },
  { "name": "Carol",  "age": 41, "city": "Delhi",     "department": "Sales",       "salary": 55000, "isActive": false, "skills": ["Negotiation", "CRM"] },
  { "name": "David",  "age": 25, "city": "Chennai",   "department": "Marketing",   "salary": 45000, "isActive": true,  "skills": ["SEO"] },
  { "name": "Emma",   "age": 37, "city": "Hyderabad", "department": "Engineering", "salary": 91000, "isActive": true,  "skills": ["Java", "Kafka", "Docker"] },
  { "name": "Farhan", "age": 30, "city": "Bangalore", "department": "HR",          "salary": 50000, "isActive": true,  "skills": ["Recruiting"] },
  { "name": "Gita",   "age": 48, "city": "Mumbai",    "department": "Sales",       "salary": 60000, "isActive": false, "skills": ["Sales"] },
  { "name": "Harish", "age": 29, "city": "Chennai",   "department": "Engineering", "salary": 58000, "isActive": true,  "skills": ["JavaScript", "React"] }
]
```

> Note: when you type numbers directly in Compass's Insert Document JSON view,
> Compass stores them as `Int32` automatically for whole numbers — that's the
> same as writing `NumberInt('28')` in the shell. You don't need to type
> `NumberInt(...)` yourself in Compass's UI.

---

## Step 2: Basic Find Queries (Filter bar)

| Goal | Filter |
|---|---|
| Find Alice | `{ name: "Alice" }` |
| All Engineering staff | `{ department: "Engineering" }` |
| Age 30 or older | `{ age: { $gte: 30 } }` |
| Salary above 50,000 | `{ salary: { $gt: 50000 } }` |
| Active employees | `{ isActive: true }` |
| Knows JavaScript | `{ skills: "JavaScript" }` |
| Knows both JavaScript and Node.js | `{ skills: { $all: ["JavaScript", "Node.js"] } }` |
| In Chennai and Engineering | `{ city: "Chennai", department: "Engineering" }` |
| Not active | `{ isActive: false }` |

---

## Step 3: Sort & Project

- Highest salary first: Sort = `{ salary: -1 }`
- Only show name, department, salary: Project = `{ name: 1, department: 1, salary: 1, _id: 0 }`
- Youngest Engineering employee: Filter `{ department: "Engineering" }`, Sort `{ age: 1 }`

---

## Step 4: Update Documents

1. **Give Alice a raise** — Filter: `{ name: "Alice" }` → Update:
   ```json
   { "$set": { "salary": 60000 } }
   ```
2. **Add a new skill to Alice** — Update:
   ```json
   { "$push": { "skills": "MongoDB" } }
   ```
3. **Reactivate everyone in Sales** — Filter: `{ department: "Sales" }` → Update:
   ```json
   { "$set": { "isActive": true } }
   ```
4. **Give every Engineer a 5000 bonus** — Filter: `{ department: "Engineering" }` → Update:
   ```json
   { "$inc": { "salary": 5000 } }
   ```

---

## Step 5: Delete Documents

1. Filter `{ isActive: false }` → check the match count.
2. Delete one manually, or bulk-delete after filtering, to remove inactive records.

---

## Step 6: Explain Plan & Indexing

1. Run filter `{ department: "Engineering" }` → open **Explain Plan** tab → note `COLLSCAN`.
2. Go to **Indexes** tab → create an ascending index on `department`.
3. Re-run the same filter → Explain Plan should now show `IXSCAN`.

---

## Step 7: Aggregation Pipeline

**A. Average salary per department**
```json
// Stage 1: $group
{ _id: "$department", avgSalary: { $avg: "$salary" } }
```
```json
// Stage 2: $sort
{ avgSalary: -1 }
```

**B. Count employees per city**
```json
// Stage 1: $group
{ _id: "$city", total: { $sum: 1 } }
```

**C. List everyone who knows JavaScript, sorted by salary**
```json
// Stage 1: $match
{ skills: "JavaScript" }
```
```json
// Stage 2: $sort
{ salary: -1 }
```

**D. Top earner per department**
```json
// Stage 1: $sort
{ salary: -1 }
```
```json
// Stage 2: $group
{ _id: "$department", topEarner: { $first: "$name" }, salary: { $first: "$salary" } }
```

---

## Step 8: Schema Validation (Optional)

**Validation** tab → paste this rule:

```json
{
  "$jsonSchema": {
    "bsonType": "object",
    "required": ["name", "department", "salary"],
    "properties": {
      "salary": { "bsonType": "int", "minimum": 0 },
      "department": { "enum": ["Engineering", "Sales", "Marketing", "HR"] }
    }
  }
}
```

Try inserting `{ name: "Test", department: "Finance", salary: 1000 }` — it should be rejected.

---

## Practice Order
1. Steps 1–3 (Day 1)
2. Steps 4–5 (Day 2)
3. Steps 6–7 (Day 3)
4. Step 8 (optional, Day 4)
