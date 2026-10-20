# MongoDB Shell (mongosh) Practice Guide — `SampleCol`

The same learning steps as the Compass guide, now as `mongosh` commands.
Open a terminal, connect with `mongosh "your-connection-string"`, then run:

```js
use test        // or whatever database SampleCol lives in
```

---

## Step 1: Insert Sample Data

```js
db.SampleCol.insertMany([
  { name: "Alice",  age: NumberInt(28), city: "Chennai",   department: "Engineering", salary: NumberInt(52000), isActive: true,  skills: ["JavaScript", "Node.js"] },
  { name: "Bob",    age: NumberInt(34), city: "Bangalore", department: "Engineering", salary: NumberInt(78000), isActive: true,  skills: ["Python", "MongoDB"] },
  { name: "Carol",  age: NumberInt(41), city: "Delhi",     department: "Sales",       salary: NumberInt(55000), isActive: false, skills: ["Negotiation", "CRM"] },
  { name: "David",  age: NumberInt(25), city: "Chennai",   department: "Marketing",   salary: NumberInt(45000), isActive: true,  skills: ["SEO"] },
  { name: "Emma",   age: NumberInt(37), city: "Hyderabad", department: "Engineering", salary: NumberInt(91000), isActive: true,  skills: ["Java", "Kafka", "Docker"] },
  { name: "Farhan", age: NumberInt(30), city: "Bangalore", department: "HR",          salary: NumberInt(50000), isActive: true,  skills: ["Recruiting"] },
  { name: "Gita",   age: NumberInt(48), city: "Mumbai",    department: "Sales",       salary: NumberInt(60000), isActive: false, skills: ["Sales"] },
  { name: "Harish", age: NumberInt(29), city: "Chennai",   department: "Engineering", salary: NumberInt(58000), isActive: true,  skills: ["JavaScript", "React"] }
])
```

Check it worked:
```js
db.SampleCol.countDocuments()
db.SampleCol.find().pretty()
```

---

## Step 2: Basic Find Queries

```js
// Find Alice
db.SampleCol.find({ name: "Alice" })

// All Engineering staff
db.SampleCol.find({ department: "Engineering" })

// Age 30 or older
db.SampleCol.find({ age: { $gte: 30 } })

// Salary above 50,000
db.SampleCol.find({ salary: { $gt: 50000 } })

// Active employees
db.SampleCol.find({ isActive: true })

// Knows JavaScript
db.SampleCol.find({ skills: "JavaScript" })

// Knows both JavaScript and Node.js
db.SampleCol.find({ skills: { $all: ["JavaScript", "Node.js"] } })

// In Chennai and Engineering
db.SampleCol.find({ city: "Chennai", department: "Engineering" })

// Not active
db.SampleCol.find({ isActive: false })
```

Add `.pretty()` to any of these for readable output.

---

## Step 3: Sort & Project

```js
// Highest salary first
db.SampleCol.find().sort({ salary: -1 })

// Only show name, department, salary (hide _id)
db.SampleCol.find({}, { name: 1, department: 1, salary: 1, _id: 0 })

// Youngest Engineering employee
db.SampleCol.find({ department: "Engineering" }).sort({ age: 1 }).limit(1)
```

---

## Step 4: Update Documents

```js
// Give Alice a raise
db.SampleCol.updateOne(
  { name: "Alice" },
  { $set: { salary: NumberInt(60000) } }
)

// Add a new skill to Alice
db.SampleCol.updateOne(
  { name: "Alice" },
  { $push: { skills: "MongoDB" } }
)

// Reactivate everyone in Sales
db.SampleCol.updateMany(
  { department: "Sales" },
  { $set: { isActive: true } }
)

// Give every Engineer a 5000 bonus
db.SampleCol.updateMany(
  { department: "Engineering" },
  { $inc: { salary: NumberInt(5000) } }
)
```

---

## Step 5: Delete Documents

```js
// Check how many are inactive first
db.SampleCol.countDocuments({ isActive: false })

// Delete one
db.SampleCol.deleteOne({ isActive: false })

// Delete all inactive
db.SampleCol.deleteMany({ isActive: false })
```

---

## Step 6: Explain Plan & Indexing

```js
// Explain before indexing
db.SampleCol.find({ department: "Engineering" }).explain("executionStats")
// Look for "stage": "COLLSCAN"

// Create an index
db.SampleCol.createIndex({ department: 1 })

// Explain again
db.SampleCol.find({ department: "Engineering" }).explain("executionStats")
// Should now show "stage": "IXSCAN"

// List all indexes
db.SampleCol.getIndexes()
```

---

## Step 7: Aggregation Pipeline

```js
// A. Average salary per department, highest first
db.SampleCol.aggregate([
  { $group: { _id: "$department", avgSalary: { $avg: "$salary" } } },
  { $sort: { avgSalary: -1 } }
])

// B. Count employees per city
db.SampleCol.aggregate([
  { $group: { _id: "$city", total: { $sum: 1 } } }
])

// C. Everyone who knows JavaScript, sorted by salary
db.SampleCol.aggregate([
  { $match: { skills: "JavaScript" } },
  { $sort: { salary: -1 } }
])

// D. Top earner per department
db.SampleCol.aggregate([
  { $sort: { salary: -1 } },
  { $group: { _id: "$department", topEarner: { $first: "$name" }, salary: { $first: "$salary" } } }
])
```

---

## Step 8: Schema Validation (Optional)

```js
db.runCommand({
  collMod: "SampleCol",
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "department", "salary"],
      properties: {
        salary: { bsonType: "int", minimum: 0 },
        department: { enum: ["Engineering", "Sales", "Marketing", "HR"] }
      }
    }
  }
})

// Test it — this should be rejected
db.SampleCol.insertOne({ name: "Test", department: "Finance", salary: 1000 })
```

---

## Compass vs Shell — Quick Mental Map

| Compass Action | Shell Equivalent |
|---|---|
| Filter bar | `db.SampleCol.find({ ... })` |
| Sort box | `.sort({ field: 1/-1 })` |
| Project box | second argument to `.find()` |
| Edit pencil icon | `updateOne()` / `updateMany()` |
| Delete button | `deleteOne()` / `deleteMany()` |
| Explain Plan tab | `.explain("executionStats")` |
| Indexes tab | `createIndex()` / `getIndexes()` |
| Aggregations tab | `.aggregate([ ... ])` |
| Validation tab | `db.runCommand({ collMod: ... })` |

---

## Practice Order
1. Steps 1–3 (Day 1)
2. Steps 4–5 (Day 2)
3. Steps 6–7 (Day 3)
4. Step 8 (optional, Day 4)

Doing the same exercises in both Compass and the shell builds a strong dual
intuition — Compass for visual exploration, shell for scripting and automation.
