# 📚 Lesson 1.2: Data Modeling & Schema Design

## Session Overview

| | |
|---|---|
| **Duration** | 3 hours |
| **Format** | Flipped Classroom + Code-Along |
| **Tools** | [dbdiagram.io](https://dbdiagram.io/d) |

## Agenda

| Time | Part | Topic |
|------|------|-------|
| 0:00 – 0:50 | Part 1 | The Data Landscape — Relational vs. NoSQL vs. Vector |
| 0:50 – 1:00 | Break | — |
| 1:00 – 1:50 | Part 2 | Blueprinting — ERD & Keys (PK/FK) |
| 1:50 – 2:00 | Break | — |
| 2:00 – 2:50 | Part 3 | Normalization — 1NF, 2NF, 3NF |
| 2:50 – 3:00 | Wrap-Up | Q&A, Next Steps |

---

## 🏃 Part 1: The Data Landscape (50 min)

### 🎯 Learning Objectives
Analyse the differences between Relational, NoSQL, and Vector databases to select the correct tool for a business problem.

### Concept Overview

Since you know Python, you know how to store data in variables. But what happens when you turn the computer off? The data is gone.

To persist data, we need a Database. But not all data is created equal — you wouldn't use a spreadsheet to store a 4K movie, and you wouldn't use a video player to calculate your taxes.

**The Three Pillars:**

| Type | The "Vibe" | Best For... | Tech Examples |
|------|-----------|-----------|---------------|
| **Relational (SQL)** | **Structured & Rigid.** Think of it like a bank vault — highly organized, strict rules. | Financials, Inventories, User Accounts | PostgreSQL, MySQL, Snowflake |
| **NoSQL** | **Flexible & Fast.** Think of it like a messy desk — throw documents anywhere, find them fast. | Social Media feeds, Chat logs, IoT sensor data | MongoDB, Cassandra |
| **Vector** | **Semantic & AI.** Think of it like a brain association game — "King" is close to "Queen". | Image search, LLM Memory, Recommendation engines | Pinecone, Weaviate |

**SQL Data Types:**

| Data Type | Description |
|-----------|-------------|
| `INT` | Integer, whole number |
| `VARCHAR` | Variable length character |
| `TEXT` | Long text |
| `DATE` | Date |
| `TIME` | Time |
| `DATETIME` | Date and time |
| `BOOLEAN` | True or false |

### 🛠️ Activity 1: The Sorting Game (10 min)

"I am the CEO of a new Startup. I have 4 features I need to build. Tell me which database type I should use and WHY."

1. **User Profile Pictures:**
   <details>
   <summary>Answer</summary>
   NoSQL/Object Store for the image binary, or SQL for the file path.
   </details>

2. **Payment Processing:**
   <details>
   <summary>Answer</summary>
   SQL. We need ACID (Atomicity, Consistency, Isolation, and Durability) compliance/Transactions.
   </details>

3. **"Find me songs that sound like Jazz":**
   <details>
   <summary>Answer</summary>
   Vector Database
   </details>

4. **A live chat for a video game:**
   <details>
   <summary>Answer</summary>
   NoSQL. High volume, simple structure.
   </details>

---

## 🏃 Part 2: Building the Blueprint — ERD (50 min)

### 🎯 Learning Objectives
Apply the concepts of Primary Keys and Foreign Keys to create a logical Entity-Relationship Diagram (ERD) using DBML.

### Concept Overview

Before we write SQL queries, we need a map. An Architect draws a blueprint before the construction crew lays bricks. An **ERD (Entity Relationship Diagram)** is our blueprint.

The glue that holds this blueprint together is the **ID**.

**The Keys:**

1. **Primary Key (PK):** The unique NRIC number of a row.
   - *Rule:* It creates identity. If two rows have the same PK, the database throws an error.

2. **Foreign Key (FK):** The reference pointing to someone else's PK.
   - *Rule:* It creates relationships — "I belong to that person over there."

### 🛠️ Activity 2.1: Code-Along — Car Insurance Schema (15 min)

Open [dbdiagram.io](https://dbdiagram.io/d) and paste the following code. Read the comments as you go.

```dbml
// --- 1. Define the Customer ---
// A 'Table' represents a noun (a person, place, or thing).
Table customers {
  id int [pk, increment] // PRIMARY KEY: The unique ID for every customer
  name varchar           // Their full name
  address varchar        // Where they live
  phone varchar          // Contact info
  email varchar          // Contact info
}

// --- 2. Define the Car ---
// A car cannot exist in our system without an owner.
Table cars {
  id int [pk, increment]
  make varchar      // e.g., Toyota
  model varchar     // e.g., Corolla
  year int
  car_plate varchar

  // FOREIGN KEY: This is the critical link.
  // It holds the ID of the customer who owns this car.
  customer_id int
}

// --- 3. Define the Link (Relationship) ---
// The '>' symbol translates to "One-to-Many".
// Read as: "One Customer can have Many Cars"
Ref: cars.customer_id > customers.id


// --- 🟢 CHALLENGE ---
// Task: Add an 'accidents' table below.
// Requirements:
// 1. Accidents have a date, location, and description.
// 2. An accident happens to a specific CAR.
// 3. Link the accident to the car.
```

<details>
<summary>Click here to view solution</summary>

```dbml
Table accidents {
  id int [pk, increment]
  date datetime
  location varchar
  description text

  car_id int // FK pointing to the car
}
Ref: accidents.car_id > cars.id
```

</details>

### 🛠️ Activity 2.2: School System Workshop (15 min)

Construct an ERD for a school system whose classes have students and teachers. Each student belongs to a single class. Each teacher may teach more than one class, and each class may have more than one teacher.

Each entity has the following attributes:

> - Student: id, name, address, phone, email, class_id
> - Teacher: id, name, address, phone, email
> - Class: id, name, teacher_id

- Write the DBML to create the ERD.
- Submit your code in the Discord Peer-Review Channel.

---

## 🏃 Part 3: Organizing Your Data — Normalization (50 min)

### 🎯 Learning Objectives
Evaluate a raw, un-normalized dataset and decompose it into a 3rd Normal Form (3NF) schema to reduce data redundancy.

### Concept Overview

> **Interactive Tutorial:** Before reading further, try the [Normalization Interactive Visualization](https://su-ntu-ctp.github.io/6m-data-1.2-intro-database/). Open this file in your web browser and follow the 8-step guided tour to see these ideas come to life.

**The Coffee Shop Story:** Every time you buy coffee, the cashier writes your complete home address on each paper slip. If you move, they'd need to update hundreds of receipts. This is an "Update Anomaly" — changing one fact forces updates in many places.

**Normalization** is like organizing your home: instead of keeping your keys in random places, you put them in one designated spot. Similarly, we organize data so each piece of information lives in exactly one place.

**"Every fact should be about the key, the whole key, and nothing but the key."**

---

#### Rule 1 (1NF): One Item Per Space — "No Grocery Bags in Cells"

**The Problem — Before 1NF:**

| Person | Favorite Fruits |
|--------|-----------------|
| Sarah | Apple, Banana, Orange |
| Mike | Grape, Mango |

**The Solution — After 1NF:**

| Person | Favorite Fruit |
|--------|----------------|
| Sarah | Apple |
| Sarah | Banana |
| Sarah | Orange |
| Mike | Grape |
| Mike | Mango |

---

#### Rule 2 (2NF): Everything Relates to the Complete ID — "The Address Book Rule"

**The Problem:** In a library tracking who borrowed which books, Alice's name appears multiple times. If Alice changes her name, you'd have to update multiple rows. Also, "Book Title" depends on "Book ID," not on the specific borrowing transaction.

**The Solution:** Split into three tables — Students, Books, and Borrowing Records. Each piece of information connects to its complete identifier.

---

#### Rule 3 (3NF): No Chain Dependencies — "The Phone Directory Principle"

**The Problem:**

| Employee ID | Employee Name | Department Code | Department Name | Department Manager |
|-----------|-------------|-----------------|-----------------|-------------------|
| E001 | Alice | D10 | Sales | John Smith |
| E002 | Bob | D10 | Sales | John Smith |
| E003 | Carol | D20 | Marketing | Jane Doe |

**What's wrong?** Department Name and Manager depend on Department Code, not directly on Employee ID. This is a "transitive dependency":

```
Employee ID → Department Code → Department Name
Employee ID → Department Code → Department Manager
```

**The Solution:** Split into Employees and Departments tables. When the Sales manager changes, you update ONE cell.

---

### 🛠️ Activity 3: The E-Commerce Deconstruction (Group Breakout — 20 min)

**Starting Point — The Messy Spreadsheet:**

| OrderID | ItemID | ItemName | ItemPrice | CustomerID | CustomerName | OrderDate |
|---------|--------|----------|-----------|-----------|-------------|-----------|
| 100 | 10 | iPhone | 1000 | 1 | John | 2021-01-01 |
| 100 | 20 | iPad | 500 | 1 | John | 2021-01-01 |
| 200 | 30 | Macbook | 2000 | 1 | John | 2021-01-02 |
| 300 | 10 | iPhone | 1000 | 2 | Mary | 2021-01-03 |
| 300 | 30 | Macbook | 2000 | 2 | Mary | 2021-01-03 |

Work through the normalization steps with your group:

**Step 1 — Apply 1NF:** Add a LineNumber to create a unique two-part identifier (OrderID + LineNumber) for each row.

**Step 2 — Apply 2NF:** Customer info (CustomerID, CustomerName, OrderDate) depends only on OrderID, not on LineNumber. Split into an **Orders Table** and an **Order Line Items Table**.

**Step 3 — Apply 3NF:** ItemName and ItemPrice depend on ItemID, not on the specific order. Create a separate **Products Table**.

**Final Result — 4 clean tables:** Customers, Products, Orders, Order Line Items.

### 🛠️ Activity 3.2: Your Turn to Practice (15 min)

**Scenario:** You run a Movie Rental Service.

**Current Messy Data:**

| RentalID | CustomerName | CustomerPhone | MovieTitle | MovieGenre | RentalDate | ReturnDate |
|----------|-------------|-------------|-----------|-----------|-----------|-----------|
| R1 | Alice | 555-1234 | Inception | Sci-Fi | 2026-01-01 | 2026-01-03 |
| R1 | Alice | 555-1234 | Interstellar | Sci-Fi | 2026-01-01 | 2026-01-03 |
| R2 | Bob | 555-5678 | Inception | Sci-Fi | 2026-01-02 | 2026-01-04 |

**Task:** Normalize this to 3NF. Create the necessary tables and identify the Primary Keys and Foreign Keys. Write it in DBML and share in Discord.

### 💬 Reflection

- When might a team *intentionally* denormalize (break normalization rules) for performance reasons? What are the trade-offs?
- How does normalization protect against "Update Anomalies"?

---

## 🎯 Wrap-Up

**Key Takeaways:**
1. Choose your database type (SQL, NoSQL, Vector) based on the nature of your data and your query patterns.
2. ERDs are blueprints — design the structure before writing SQL.
3. Normalization ensures each fact lives in exactly one place, preventing duplicates and update errors.

**Next Steps:**
- Complete the [Assignment](./assignment.md).
- Next lesson: Lesson 1.3 introduces SQL DDL — how to physically build the tables you designed today.
