---
title: Difference Between DELETE and TRUNCATE Commands In SQL
date: 2025-08-04 14:18:00 +05:30
categories:
- database
tags:
- sql
layout: post
---

# **What is a DELETE command?**

- The DELETE statement is a **DML command (Data Manipulation Language).** It is used to delete single or multiple rows (records) from a table.
- The **WHERE** clause in DELETE command **defines a condition** for deleting only the selected unwanted rows from the target table.
- The DELETE query creates a log file in the transaction log. It stores the records before deleting them. So, in case, we delete one or many important rows, we can get them back using the **ROLLBACK** command.
- Since the DELETE statement is a DML command, we have to commit the changes manually. Point to remember, the **ROLLBACK must be done before COMMIT**.

### **Syntax:**

> DELETE FROM table_name WHERE condition;
> 

# **What is a TRUNCATE command?**

- The TRUNCATE command is a **DDL command** **(Data Definition Language).** We use this command on tables to delete all the records at once.
- The TRUNCATE command **does not use any condition,** like a WHERE clause to define a condition. So, we should use it only when all the data in the target table is unwanted.
- The TRUNCATE command uses table lock to **lock the table and page** for truncate operation, instead of row lock. So, it only logs the deallocation of the pages that store the records, and as a result, it takes much less time than the DELETE command.
- Since the TRUNCATE command is a DDL command, the **commit is automatically done** here.
- The TRUNCATE command **resets the identity** to its seed value.

**Seed value**: seed value in SQL refers to the internal value which the Server uses to generate the next value.

### **Syntax:**

> TRUNCATE TABLE table_name;
> 

# **"TRUNCATE cannot be rolled back" - Fact or Myth?**

We know the DELETE command can use rollback. But, many people have a misconception that the TRUNCATE statements cannot use rollback. It is **MYTH**!

**Myth**: The truncate command works so fast that it does not have time to log its records in the transaction log.

**Fact:** It **only logs the deallocation of the page of stored records**, so the work is **faster than the DELETE** command.

Meanwhile, the DELETE statement logs all the rows of the table, so, the DELETE statement takes much more time than the TRUNCATE command.

In conclusion, the TRUNCATE command can be rolled back for truncated records inside the transaction.

> Page: It is the fundamental unit of data storage in SQL Server.
> 

**DELETE vs TRUNCATE**

| Topic | DELETE | TRUNCATE |
| --- | --- | --- |
| Definition | DELETE is a SQL command that **removes one or multiple rows from a table using conditions.** | TRUNCATE is a SQL command that **removes all the rows** from a table without using any condition. |
| Language | It is a **DML(Data Manipulation Language)** command. | It is a **DDL(Data Definition Language)** command. |
| Commit | We have to manually COMMIT the changes in the DELETE command. | The changes are automatically COMMIT in the TRUNCATE command. |
| Process | It deletes rows **one by one with conditions.** | It deletes **all the data at once**. |
| Condition | It uses the **WHERE clause** as a condition. | It does not take any condition i.e. **does not use WHERE clause.** |
| Lock | It **locks all the rows** for deletion. | It uses table lock to **lock the pages** for deletion. |
| Log | It **logs every single record in the transaction log**. | It **only logs the deallocation of the pages where the data is stored.** |
| Transaction space | It uses **more transaction space** compared to the TRUNCATION command. | It uses **less transaction space** compared to the DELETE command. |
| Identity | It does **not reset the table identity** to its seed value if there is an identity column. | It **resets** the table identity to its seed value. |
| Permission | It needs **delete** permission. | It needs **alter** table permission. |
| Speed | It is much **slower** when comes to big databases. | It is faster or in other words, **instant**. |