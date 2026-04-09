# Day 8 — RDS MySQL: Full Practical (EC2 Jump Host + VS Code Tunnel + SQL)

**Bootcamp:** Batch 43 | Multi-Cloud + DevOps + AI  
**Topic:** RDS deployment, EC2 Jump Host, VS Code SSH tunnel, MySQL CRUD & ALTER

---

## 🏗️ Architecture Used

```
❌ Wrong — Direct connection (blocked):
Laptop ──────────────────────────→ RDS
                                  ETIMEDOUT (Security Group blocks it, intentionally)

✅ Correct — Jump Host pattern:
Laptop ──SSH:22──→ EC2 JUMPHOST ──port 3306──→ RDS (private)
                  [public subnet]              [private subnet]

✅ Also done — VS Code SSH Tunnel:
VS Code (MySQL extension) ──SSH tunnel via EC2──→ RDS
```

---

## 🛠️ Step 1: Created EC2 Jump Host

- Name: `JUMPHOST` | OS: Ubuntu | No key pair (used EC2 Instance Connect)
- Allowed SSH on port 22

---

## 🗄️ Step 2: Deployed RDS MySQL

- Engine: **MySQL 8.4.8** (LTS)
- Identifier: `first-database`
- Credentials: Auto-generated password + IAM database authentication enabled
- Connectivity: "Connect to EC2 compute resource" → selected JUMPHOST
  - Auto-configured Security Groups: EC2 → RDS allowed on port 3306

> ⚠️ Click **View Credentials** immediately after creation — saves username, password, endpoint. Shows only once.

---

## 💻 Step 3: Environment Setup on Jump Host

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install mysql-client -y
mysql --version                         # verified: MySQL 8.0.x
```

---

## 🔗 Step 4: Connected to RDS via CLI

```bash
mysql -h <rds-endpoint> -u admin -p
# enter auto-generated password when prompted
```

---

## 🖥️ Step 5: Connected VS Code to RDS via SSH Tunnel

**Extension used:** MySQL (by cweijan)

**SSH Tunnel config (EC2 as the bridge):**
| Field | Value |
|-------|-------|
| SSH Host | EC2 Public IP |
| SSH Port | 22 |
| SSH User | ubuntu |
| SSH Key | `.pem` file |

**MySQL config (RDS as the target):**
| Field | Value |
|-------|-------|
| Host | RDS Endpoint |
| Port | 3306 |
| User | admin |
| Password | auto-generated password |

✅ Successfully connected — VS Code and CLI both hit the **same RDS database**. Changes made in one tool reflect instantly in the other.

---

## 🗃️ Step 6: Database & Table Setup

```sql
CREATE DATABASE adk;
USE adk;

CREATE TABLE tutorials_tbl (
  tutorial_id       INT,
  tutorial_title    VARCHAR(100),
  tutorial_author   VARCHAR(40),
  submission_date   DATE
);
```

Inserted **26 rows** of mixed data (books, tutorials, guides across various topics).

---

## 🛠️ SQL Operations Practised

### Basic Queries
```sql
SELECT * FROM tutorials_tbl;
SELECT * FROM tutorials_tbl LIMIT 100;
SELECT * FROM tutorials_tbl ORDER BY submission_date ASC LIMIT 100;
```

### INSERT
```sql
INSERT INTO tutorials_tbl (tutorial_title, Authors, submission_date)
VALUES ('My VS Code Entry', 'Anushk Dhokne', '2026-04-09');
```

### UPDATE (change row data)
```sql
UPDATE tutorials_tbl SET Papers = 'Updated Title' WHERE tutorial_id = 1;
```

### DELETE
```sql
DELETE FROM tutorials_tbl WHERE tutorial_id = 1;
```

### WHERE + LIKE (filtering)
```sql
-- Sports-related tutorials
SELECT * FROM tutorials_tbl
WHERE Papers LIKE '%Cricket%'
OR Papers LIKE '%Marathon%'
OR Papers LIKE '%Cycling%';

-- Tutorials from before 2024
SELECT * FROM tutorials_tbl WHERE submission_date < '2024-01-01';

-- Find my own entry
SELECT * FROM tutorials_tbl WHERE Authors = 'Anushk Dhokne';
```

### YEAR() function
```sql
-- All tutorials submitted in 2024
SELECT * FROM tutorials_tbl WHERE YEAR(submission_date) = 2024;
-- Result: 8 rows
```

### GROUP BY + ORDER BY
```sql
-- Count tutorials per author, spot single-entry authors
SELECT Authors, COUNT(*) AS total
FROM tutorials_tbl
GROUP BY Authors
ORDER BY total DESC;
-- Result: 21 rows
```

### ORDER BY (sorting)
```sql
SELECT * FROM tutorials_tbl ORDER BY Papers ASC;       -- alphabetical by title
SELECT * FROM tutorials_tbl ORDER BY Authors ASC;      -- alphabetical by author
```

### ALTER TABLE (change table structure)
```sql
-- Rename column tutorial_title → Papers
ALTER TABLE tutorials_tbl
RENAME COLUMN tutorial_title TO Papers;

-- Rename column tutorial_author → Authors
ALTER TABLE tutorials_tbl
RENAME COLUMN tutorial_author TO Authors;
```

---

## 💡 Key Concepts

### UPDATE vs ALTER
| Command | What it does |
|---------|-------------|
| `UPDATE` | Changes **data inside rows** |
| `ALTER` | Changes **table structure** (columns, names, types) |

### Public IP vs Private IP on AWS
- AWS Security Groups use **public IP in `/32` CIDR format** (e.g. `203.0.113.5/32`)
- `/32` means exactly one IP address — most restrictive, most secure

### `ipconfig` vs Linux equivalent
| OS | Command |
|----|---------|
| Windows | `ipconfig` |
| Linux | `ip addr` or `ifconfig` (needs `net-tools` package) |

---

## 💡 What I Observed (from logs)

- First `UPDATE` failed with "Unknown column 'Papers'" — because `ALTER` to rename the column hadn't run yet. Order of operations matters in SQL.
- `RENAME COLUMN` syntax errors happened when column name was typed incorrectly — fixed by checking exact column name first with `SELECT *`
- VS Code MySQL extension shows full execution logs with timestamps and row counts — very useful for debugging
- `SELECT * WHERE Authors = 'Anushk Dhokne'` returned **4 rows** — my own entries in the table
- `LIKE '%Cricket%'` returned **3 rows** — the `%` wildcard matches anything before or after the word
