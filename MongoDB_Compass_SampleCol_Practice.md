# MongoDB Compass Practice Guide — `SampleCol` Collection

A hands-on, step-by-step exercise set to practice CRUD, querying, and aggregation
in MongoDB Compass using a collection named **SampleCol**.

---

## Step 1: Insert Sample Data

Go to your `SampleCol` collection → **Documents** tab → **Add Data → Insert Document**
→ switch to the **{} (JSON view)** → paste the array below → click **Insert**.

```json
[
  { "name": "Aarav Sharma",  "department": "Engineering", "age": 28, "salary": 65000, "city": "Chennai",    "joiningDate": "2021-03-15", "skills": ["Node.js", "MongoDB"], "isActive": true },
  { "name": "Priya Nair",    "department": "Engineering", "age": 32, "salary": 78000, "city": "Bangalore",  "joiningDate": "2019-07-01", "skills": ["Python", "Django"],  "isActive": true },
  { "name": "Rahul Verma",   "department": "Sales",       "age": 45, "salary": 55000, "city": "Delhi",      "joiningDate": "2015-01-10", "skills": ["Negotiation"],       "isActive": false },
  { "name": "Sneha Iyer",    "department": "Marketing",   "age": 26, "salary": 48000, "city": "Chennai",    "joiningDate": "2022-06-20", "skills": ["SEO", "Content"],    "isActive": true },
  { "name": "Karthik Raja",  "department": "Engineering", "age": 38, "salary": 92000, "city": "Hyderabad",  "joiningDate": "2017-11-05", "skills": ["Java", "Kafka"],     "isActive": true },
  { "name": "Divya Menon",   "department": "HR",          "age": 30, "salary": 52000, "city": "Bangalore",  "joiningDate": "2020-02-28", "skills": ["Recruiting"],        "isActive": true },
  { "name": "Vikram Singh",  "department": "Sales",       "age": 50, "salary": 60000, "city": "Mumbai",     "joiningDate": "2012-09-12", "skills": ["CRM", "Sales"],      "isActive": false },
  { "name": "Anjali Gupta",  "department": "Marketing",   "age": 29, "salary": 51000, "city": "Delhi",      "joiningDate": "2021-08-19", "skills": ["Branding"],          "isActive": true },
  { "name": "Suresh Kumar",  "department": "Engineering", "age": 41, "salary": 88000, "city": "Chennai",    "joiningDate": "2016-04-03", "skills": ["Go", "Docker"],      "isActive": true },
  { "name": "Meera Pillai",  "department": "HR",          "age": 34, "salary": 54000, "city": "Hyderabad",  "joiningDate": "2018-10-25", "skills": ["Payroll"],           "isActive": false }
]
```

> Tip: Compass inserts these as 10 separate documents in `SampleCol`.

---

## Step 2: Basic Find Queries

Use the **Filter** bar at the top of the Documents tab.

| Goal | Filter |
|---|---|
| All engineers | `{ department: "Engineering" }` |
| People in Chennai | `{ city: "Chennai" }` |
| Age greater than 30 | `{ age: { $gt: 30 } }` |
| Salary between 50k–80k | `{ salary: { $gte: 50000, $lte: 80000 } }` |
| Active employees only | `{ isActive: true } ` |
| Not in Sales | `{ department: { $ne: "Sales" } }` |
| In Chennai or Delhi | `{ city: { $in: ["Chennai", "Delhi"] } }` |
| Knows MongoDB | `{ skills: "MongoDB" }` |
| Name starts with "A" | `{ name: { $regex: "^A" } }` |

Run each one, watch the document count update at the top.

---

## Step 3: Sort & Project

- **Sort** box: `{ salary: -1 }` → highest salary first.
- **Project** box: `{ name: 1, department: 1, _id: 0 }` → show only name & department.
- Combine: Filter `{ department: "Engineering" }` + Sort `{ age: 1 }` → youngest engineer first.

---

## Step 4: Update Documents

1. Filter: `{ name: "Sneha Iyer" }` → click the pencil (edit) icon on the result → change `salary` to `52000` → **Update**.
2. Try the **Update** modal with an operator instead of manual edit:
   - Filter: `{ department: "Sales" }`
   - Update: `{ $set: { isActive: true } }`
   - This reactivates everyone in Sales.
3. Increment a field: Filter `{ name: "Aarav Sharma" }`, Update `{ $inc: { salary: 5000 } }`.

---

## Step 5: Delete Documents

1. Filter: `{ isActive: false }` → check how many match first.
2. Use **Delete** on a single document, or the bulk delete option after filtering, to remove all inactive records.

---

## Step 6: Explain Plan (Performance Basics)

1. Run filter `{ department: "Engineering" }`.
2. Click the **Explain Plan** tab.
3. Note it says `COLLSCAN` (collection scan — no index used).
4. Go to **Indexes** tab → create index on `department` (ascending).
5. Re-run the same filter and check Explain Plan again → should now show `IXSCAN`.

---

## Step 7: Aggregation Pipeline (Compass "Aggregations" tab)

Build these stage by stage — add one stage, watch the preview, then add the next.

**A. Average salary per department**
```json
// Stage 1: $group
{ _id: "$department", avgSalary: { $avg: "$salary" } }
```
```json
// Stage 2: $sort
{ avgSalary: -1 }
```

**B. Count of active vs inactive employees**
```json
// Stage 1: $group
{ _id: "$isActive", count: { $sum: 1 } }
```

**C. Employees per city, only where count > 1**
```json
// Stage 1: $group
{ _id: "$city", total: { $sum: 1 } }
```
```json
// Stage 2: $match
{ total: { $gt: 1 } }
```

**D. Highest paid employee per department**
```json
// Stage 1: $sort
{ salary: -1 }
```
```json
// Stage 2: $group
{ _id: "$department", topEarner: { $first: "$name" }, salary: { $first: "$salary" } }
```

---

## Step 8: Schema Validation (Optional Advanced Step)

Go to **Validation** tab → add a rule requiring `salary` to be a number and `department` to be one of a fixed set:

```json
{
  "$jsonSchema": {
    "bsonType": "object",
    "required": ["name", "department", "salary"],
    "properties": {
      "salary": { "bsonType": "number", "minimum": 0 },
      "department": { "enum": ["Engineering", "Sales", "Marketing", "HR"] }
    }
  }
}
```
Try inserting a document with `department: "Finance"` — it should be rejected.

---

## Suggested Practice Order
1. Steps 1–3 (Day 1)
2. Steps 4–5 (Day 2)
3. Steps 6–7 (Day 3)
4. Step 8 (Day 4, optional)

Redo each step from memory once before moving to the next — that's what makes it stick.
