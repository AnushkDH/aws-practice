# Day 7 — Database Fundamentals & AWS RDS Introduction

**Bootcamp:** Batch 43 | Multi-Cloud + DevOps + AI  
**Topic:** Database types, engines, and why cloud databases matter

---

## 📚 Key Concepts Learned

### Why Databases Matter
- Data drives business decisions and automation
- Storage has evolved: Local machine → Manual records → Cloud-based (modern standard)
- In DevOps: web servers are disposable, but **databases are critical** — losing one is a serious incident

---

## 📊 Three Types of Databases

### A. Relational (SQL)
- Data stored in **rows and columns** with defined relationships
- Follows **ACID** properties for data integrity:

| Property | Meaning |
|----------|---------|
| **A**tomicity | All or nothing — transaction either fully completes or fully fails |
| **C**onsistency | Data always stays valid before and after a transaction |
| **I**solation | Transactions don't interfere with each other |
| **D**urability | Committed data is permanently saved |

- Uses **Indexing** for fast data retrieval
- Engines: MySQL, PostgreSQL, Oracle, MS SQL Server

### B. Non-Relational (NoSQL)
- No fixed structure — data stored as **key-value pairs** or **JSON documents**
- Best when data structure is unknown upfront or changes frequently
- Engines: MongoDB, Cassandra

### C. Time Series
- Data indexed by **timestamps** — stores snapshots every second/minute
- Used for: monitoring servers, IoT sensors, stock prices
- Example: **Prometheus** (which we'll use later in the bootcamp)

---

## 🔍 Database Engine Comparison

| Engine | Type | Key Feature | Used For |
|--------|------|-------------|---------|
| **MySQL** | Relational | Free, open source, works with Python/Node/Java | Web apps, general use |
| **PostgreSQL** | Relational (ORDBMS) | 35+ years old, JSON support, ACID compliant | Enterprise, advanced queries |
| **Oracle** | Relational | Highly secure, expensive, professional support | Banking, finance, large enterprise |
| **MongoDB** | NoSQL | Document-based JSON, flexible schema | Apps where data structure evolves |

### PostgreSQL specifically
- **ORDBMS** = Object-Relational Database — bridges SQL and NoSQL
- Supports JSON (like NoSQL) while keeping ACID compliance (like SQL)
- Most advanced open-source database available

---

## 💡 What I Understood

- MySQL is the most common DB you'll encounter in real DevOps/cloud projects — good to start here
- Time Series DBs like Prometheus are directly relevant to the monitoring tools coming later in bootcamp
- RDS = AWS managed version of these databases — AWS handles patching, backups, failover for you
