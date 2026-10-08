# Cybersecurity Interview Theory Notes

**Target:** EY Technology Consulting, Cyber Security, Senior Analyst fresher interview  
**Audience:** Final-year Computer Engineering student  
**Revision horizon:** Approximately 10 days of part-time preparation  
**Scope:** Foundational computer science, applied security, and interview explanations. This is a study guide, not a claim about EY's exact interview syllabus.

> **Answer pattern:** Define the concept in one sentence, explain how it works, give a concrete example, then state the security or business implication. For an unfamiliar question, explain what you know and identify what depends on the implementation.

**10-day study allocation:** Day 1 OS; Day 2 DBMS and SQL; Day 3 OOP and networking models; Day 4 networking protocols and subnetting; Day 5 cryptography and TLS; Days 6 and 7 web security; Day 8 security operations and penetration testing; Day 9 speak the interview answers aloud and revisit weak areas; Day 10 use the last-night summary. Adjust based on your available hours.

**Version note:** The OWASP web application list below uses **OWASP Top 10:2025**, the current published list verified for these notes. Security controls are contextual, not guarantees.

---

# 1. Operating Systems

An **operating system (OS)** is the software layer that manages hardware resources and gives programs controlled access to CPU time, memory, files, devices, and networks. Security depends on OS isolation, permissions, process control, and logging.

## 1.1 Processes vs threads

A **process** is a running program with its own virtual address space and resources. A **thread** is a path of execution inside a process. Threads of one process usually share its address space and open resources but each has its own stack and registers.

| Point | Process | Thread |
|---|---|---|
| Address space | Normally isolated from other processes | Shared with threads in the same process |
| Creation and switching | Typically heavier | Typically lighter |
| Communication | Requires IPC such as pipes, sockets, shared memory | Can use shared variables, with synchronization |
| Failure | Often isolated to its process | Fault can terminate the entire process |
| Example | Browser and database server | Browser UI and background worker threads |

**Concurrency** means tasks make progress in overlapping periods. **Parallelism** means tasks execute simultaneously on different cores. A race condition happens when the result depends on uncontrolled timing, such as two threads updating a counter without a lock. A **mutex** permits one owner at a time; a **semaphore** tracks permits; a **deadlock** can occur if locks are acquired inconsistently. A **context switch** saves execution state and restores another task's state; it has overhead.

**Security connection:** Process isolation limits the impact of a compromised program. Thread safety matters because races can lead to inconsistent balances, unexpected authorization behavior, or other flaws.

## 1.2 CPU scheduling

The scheduler decides which ready task receives CPU time. **Preemptive** scheduling can interrupt a running task; **non-preemptive** scheduling generally waits until it blocks or yields.

| Algorithm | Core idea | Trade-off |
|---|---|---|
| FCFS | Execute in arrival order | Simple, but long tasks cause convoy effect |
| SJF | Prefer shortest CPU burst | Low average waiting time if bursts are known; can starve long jobs |
| SRTF | Preemptive shortest remaining time | Responsive for short jobs; scheduling overhead |
| Round Robin | Fixed time quantum per task | Fair for interactive tasks; quantum affects overhead and latency |
| Priority | Prefer higher-priority jobs | Low-priority starvation unless aging is used |
| Multilevel feedback queue | Adjust priority based on observed behavior | Flexible but requires tuning |

**Metrics:** turnaround = completion time minus arrival time; waiting = turnaround minus CPU execution time (for a simple single-burst model); response = first CPU start minus arrival. **Starvation** means a task waits indefinitely because others are favored. **Aging** gradually increases waiting tasks' priority.

## 1.3 Deadlocks

A **deadlock** happens when tasks cannot make progress because each is waiting for resources held by others. Four conditions are jointly necessary: **mutual exclusion**, **hold and wait**, **no preemption**, and **circular wait**. Example: process A holds lock X and waits for Y, while B holds Y and waits for X.

- **Prevention:** break a condition, such as imposing a consistent order for acquiring locks.
- **Avoidance:** grant requests only if the system stays in a safe state, illustrated by Banker's algorithm.
- **Detection and recovery:** identify cycles, then terminate or roll back affected work where appropriate.
- **Timeouts:** can help operations recover but are not proof that deadlocks cannot occur.

## 1.4 Memory management

**Memory management** gives each process a usable view of memory while protecting one process from another. The OS and hardware translate virtual addresses into physical locations and control access.

| Concept | Plain explanation | Technical detail |
|---|---|---|
| Paging | Divide memory into equal-size blocks | Virtual pages map to physical frames through page tables; TLB caches translations |
| Segmentation | Divide a program into meaningful regions | Variable-size segments such as code, stack, or data; classical segmentation can have external fragmentation |
| Virtual memory | Programs see memory beyond immediately available RAM | Virtual address spaces, demand paging, and possible backing storage |
| Page fault | Needed page is not currently mapped as required | OS resolves a valid fault by mapping/loading a page; invalid access may terminate the process |
| Thrashing | System spends excessive time moving pages | Frequent faults and insufficient memory for working sets |

**Internal fragmentation** wastes space inside allocated units. **External fragmentation** leaves free space in separated gaps. Paging mainly introduces internal fragmentation for partially used pages; traditional segmentation can suffer external fragmentation. Page replacement schemes include FIFO, LRU approximations, and clock. Virtual memory does not imply every unused page is stored in a swap file.

**Security connection:** Access bits and memory isolation help prevent unauthorized reads/writes. ASLR randomizes memory layout; NX/DEP can mark pages non-executable. Neither eliminates all memory-exploitation risk.

## 1.5 File systems

A **file system** organizes files and directories and stores metadata such as ownership, size, and timestamps. Directories map names to file objects; the exact design depends on the OS and filesystem.

- **Metadata/inodes:** On common Unix filesystems, an inode identifies file metadata and data references; the filename lives in a directory entry.
- **Hard link:** Another directory entry to the same file inode, usually on the same filesystem. Removing one name does not delete content while other links remain.
- **Symbolic link:** A separate object containing a path to a target. It can become broken if the target moves.
- **Journaling:** Records intended metadata or data changes to support crash recovery; it is not a replacement for backups.
- **Permissions and ACLs:** Define which users and groups can access files. Encryption at rest addresses different threats.

## 1.6 Linux essentials

Linux uses users, groups, process ownership, and permissions. **Root** is the privileged account. `sudo` delegates permitted commands according to policy. Ordinary tasks should run with least privilege.

### File permissions

`-rwxr-x---` means: ordinary file (`-`), owner `rwx` (7), group `r-x` (5), others `---` (0). Permission values are **read = 4**, **write = 2**, **execute = 1**. Thus `chmod 750 script.sh` assigns 7/5/0. On directories, `x` allows traversal and `r` allows listing names, subject to other controls.

| Command | Purpose | Example |
|---|---|---|
| `pwd`, `ls -la`, `cd` | Location, listings, navigation | `ls -la /var/log` |
| `cat`, `less`, `head`, `tail -f` | Read files and follow logs | `tail -f app.log` |
| `grep`, `find` | Search text and files | `grep -i error app.log` |
| `cp`, `mv`, `rm`, `mkdir` | Manage files/directories | `mkdir reports` |
| `chmod`, `chown`, `id`, `whoami` | Permissions, ownership, identity | `id`; `chmod 640 report.txt` |
| `useradd`, `usermod`, `groupadd`, `passwd` | Administer users, groups, passwords (requires privileges) | `sudo usermod -aG devs alice` |
| `ps aux`, `top`, `htop` | Inspect processes | `ps aux | grep nginx` |
| `kill -TERM PID`, `kill -KILL PID` | Request graceful exit or force termination | Prefer `TERM` before `KILL` |
| `ss -tuln`, `ip addr`, `ip route` | Sockets, interfaces, routes | `ss -tuln` |
| `df -h`, `du -sh`, `free -h` | Disk and memory usage | `df -h` |
| `systemctl`, `journalctl` | Manage/read services and logs on systemd systems | `journalctl -u ssh` |
| `ssh`, `scp`, `curl` | Remote access, copy, HTTP requests | `curl -I https://example.com` |

`>` overwrites a command's output file, `>>` appends, and `|` sends one command's output into the next command's input. `su` switches user context where permitted; `sudo` runs an authorized command as another user, commonly root. Account tools and group behavior differ across Linux distributions. `umask` influences default new-file permissions. **SUID/SGID** alter execution identity in some Unix permission models and deserve security review; avoid enabling them casually.

**Common mistakes candidates make:** Calling a thread a completely isolated program; treating concurrency as always parallel; forgetting all four deadlock conditions; saying a page fault always means an error; claiming `chmod 777` is a safe fix; confusing `kill -TERM` with `kill -KILL`.

## Likely interview questions

1. **What is the difference between a process and a thread?**  
   A process is a running program with its own virtual address space and OS-managed resources. A thread is an execution unit within a process, sharing most of that process's memory. Threads make communication cheaper but require synchronization because concurrent access can create race conditions. Processes generally provide stronger failure isolation.

2. **What happens during a context switch?**  
   The OS saves enough state from the current task, such as registers and scheduling state, to resume it later, then restores another task's state. Context switches support multitasking but have overhead from scheduling and cache disruption. Switching between separate address spaces may involve additional memory-translation overhead.

3. **Explain deadlock and its conditions.**  
   A deadlock occurs when tasks are permanently blocked waiting for resources held by one another. The four necessary conditions are mutual exclusion, hold and wait, no preemption, and circular wait. A practical prevention is to acquire multiple locks in a consistent global order so circular waiting cannot form.

4. **How does Round Robin scheduling work?**  
   In Round Robin, each ready process gets a time quantum. If it has not finished when its quantum expires, it goes to the back of the ready queue. This improves fairness and response in interactive systems. A tiny quantum causes frequent context switches; a very large one starts to resemble FCFS.

5. **Paging vs segmentation?**  
   Paging divides virtual memory into fixed-size pages that map to frames, which simplifies allocation and avoids traditional external fragmentation. Segmentation groups memory into variable-size logical regions, such as code and data, but can suffer external fragmentation. Modern systems may combine concepts, so implementation details vary by architecture.

6. **What is virtual memory and what is a page fault?**  
   Virtual memory gives processes their own protected address spaces, with hardware and OS translation to physical memory. A page fault occurs when an access cannot proceed using the current mapping. The OS may load or map a valid page and resume execution, or reject an invalid access. Not every page fault is fatal.

7. **Explain `chmod 750` and least privilege.**  
   `750` gives the owner read, write and execute, the group read and execute, and others no permissions. Least privilege means giving users, services, and files only the access needed for their tasks. For example, a web service should not run as root when a restricted service account would suffice.

8. **How would you investigate a suspicious Linux process?**  
   I would first preserve evidence and confirm the scope of access. Then I would inspect the PID, executable path, owner, parent process, command line, network connections, and relevant logs using tools such as `ps`, `ss`, `journalctl`, and service logs. I would correlate timestamps and escalate under the organization's incident-response process, rather than immediately deleting evidence.

---

# 2. DBMS and SQL

A **database management system (DBMS)** stores structured data and lets applications query and update it consistently. Relational databases model data as tables connected by keys. In security work, database design, least privilege, query construction, and transaction integrity matter.

## 2.1 ER model and keys

An **entity** is an object such as Employee; an **attribute** is a property such as email; a **relationship** links entities, such as Employee belongs to Department. An ER diagram makes business rules visible before table design.

- **Primary key (PK):** uniquely identifies a row and cannot be null.
- **Foreign key (FK):** references a key in another or the same table and helps enforce referential integrity.
- **Candidate key:** minimal set of columns that could uniquely identify a row.
- **Composite key:** a key made from multiple columns.
- **Cardinality:** one-to-one, one-to-many, or many-to-many. A many-to-many relation typically uses a junction table.

Example: `Employees(employee_id PK, name, department_id FK)` and `Departments(department_id PK, department_name)`. A department can have many employees. A foreign key does not automatically mean the column is unique.

## 2.2 Normalization

**Normalization** reduces redundant information and the inconsistencies redundancy causes. It is based on dependencies between attributes and usually proceeds through normal forms.

| Normal form | Plain rule | Example of problem it prevents |
|---|---|---|
| 1NF | Store atomic values in columns with no repeating groups | A single `phone_numbers` field containing several numbers |
| 2NF | In 1NF, non-key attributes depend on the whole candidate key, not just part of a composite key | `student_name` stored in `(student_id, course_id)` enrollment rows |
| 3NF | In 2NF, non-key attributes do not depend transitively on a key through another non-key attribute | Repeating `department_name` in each employee row when it depends on `department_id` |
| BCNF | Every determinant of a non-trivial functional dependency is a superkey | Certain dependencies 3NF permits still cause anomalies |

**Anomalies:** update (same fact differs across rows), insert (cannot add a department without an employee), delete (deleting the last employee accidentally removes department facts). **Denormalization** intentionally repeats data for performance, but increases consistency and maintenance responsibilities.

## 2.3 ACID and transactions

A **transaction** groups database changes into one logical unit of work. Transferring money should debit and credit consistently instead of leaving only half the operation complete.

| Property | Meaning | Banking example |
|---|---|---|
| Atomicity | All changes commit, or the transaction rolls back | Debit and credit succeed together |
| Consistency | Constraints/invariants remain valid at transaction boundaries | Account rules and foreign keys are respected |
| Isolation | Concurrent transactions behave according to the chosen isolation level | Concurrent transfers do not create forbidden results |
| Durability | Committed data survives failures under the DB's durability guarantees | Confirmed transfer persists after restart |

Typical flow: `BEGIN; UPDATE ...; UPDATE ...; COMMIT;` or `ROLLBACK` on failure. **Isolation levels** commonly include Read Uncommitted, Read Committed, Repeatable Read, and Serializable. Their exact anomaly guarantees vary by DBMS and implementation. Anomalies to know: **dirty read** (uncommitted data), **non-repeatable read** (same row differs within one transaction), and **phantom** (set of matching rows changes). Serializable aims for results equivalent to some serial ordering, potentially with retries or lower throughput.

## 2.4 Indexing

An **index** is an auxiliary data structure that makes selected lookups faster, like a book's index. Many relational engines use **B-tree-family indexes**; hash indexes and others exist. Indexes can accelerate filtering, joins, sorting, and uniqueness checks, but consume storage and add overhead to writes.

- Index `Employees(department_id)` for frequent filters or joins on that column.
- A composite index `(department_id, hire_date)` often helps queries filtering by the leading column, but exact behavior depends on the engine and query plan.
- **Unique index** can enforce uniqueness; a **primary key** typically receives index support.
- A low-selectivity column or a tiny table may not benefit much. Use the database's `EXPLAIN` feature rather than assuming an index will always be used.

## 2.5 Joins and SQL execution

A **join** combines rows using a relation between tables.

| Join | Result |
|---|---|
| `INNER JOIN` | Only matching rows from both sides |
| `LEFT JOIN` | All left rows, with null right columns where no match exists |
| `RIGHT JOIN` | All right rows, with null left columns where no match exists |
| `FULL OUTER JOIN` | Matching plus unmatched rows from both sides, where supported |
| `CROSS JOIN` | Cartesian product |
| Self join | A table joined to itself using aliases |

**Logical query processing** is commonly described as `FROM/JOIN -> WHERE -> GROUP BY -> HAVING -> SELECT -> DISTINCT -> ORDER BY -> LIMIT/OFFSET` (actual physical optimization can differ). `WHERE` filters individual rows; `HAVING` filters groups; `ORDER BY` specifies result order. Without `ORDER BY`, output row order is not guaranteed. `NULL` means unknown or missing and is tested with `IS NULL`, not `= NULL`.

### SQL queries worth practicing

Assume `Employees(id, name, department_id, salary)` and `Departments(id, name)`:

```sql
-- 1. Find high earners
SELECT name, salary FROM Employees WHERE salary > 70000;

-- 2. Count employees by department, only departments with 3+ employees
SELECT department_id, COUNT(*) AS total
FROM Employees
GROUP BY department_id
HAVING COUNT(*) >= 3;

-- 3. Include departments that have no employees
SELECT d.name, COUNT(e.id) AS employee_count
FROM Departments AS d
LEFT JOIN Employees AS e ON e.department_id = d.id
GROUP BY d.id, d.name;

-- 4. Find employees with a department name
SELECT e.name, d.name AS department
FROM Employees AS e
INNER JOIN Departments AS d ON d.id = e.department_id;

-- 5. Second highest DISTINCT salary (returns no row if fewer than 2 values)
SELECT DISTINCT salary
FROM Employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;

-- 6. Find duplicate names, not necessarily duplicate people
SELECT name, COUNT(*) AS n
FROM Employees
GROUP BY name
HAVING COUNT(*) > 1;

-- 7. Employees whose salary is above their department average
SELECT e.name, e.salary
FROM Employees AS e
WHERE e.salary > (
  SELECT AVG(e2.salary)
  FROM Employees AS e2
  WHERE e2.department_id = e.department_id
);

-- 8. Row numbering within departments (window functions)
SELECT name, department_id, salary,
       ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rn
FROM Employees;
```

These examples use PostgreSQL/MySQL/SQLite-style `LIMIT/OFFSET`; SQL Server typically uses different syntax. A `LEFT JOIN` plus `WHERE e.id IS NULL` finds departments with no matching employee. `COUNT(*)` counts rows; `COUNT(column)` skips nulls. `UNION` removes duplicate rows; `UNION ALL` retains them. `DELETE` removes selected rows; `TRUNCATE` clears a table as an operation with DB-specific logging/transaction details; `DROP` removes the table object.

## 2.6 SQL injection, the security bridge

**SQL injection (SQLi)** occurs when untrusted input changes the structure of a SQL command instead of remaining data. Example of **unsafe query building**:

```python
# Vulnerable: do not use
query = "SELECT * FROM users WHERE username = '" + user_input + "'"

# Safe pattern: placeholder style depends on the database driver
cursor.execute("SELECT * FROM users WHERE username = ?", (user_input,))
```

An attacker may supply crafted input that changes the query's intended condition, leading to data exposure or modification. **Primary fix:** parameterized queries/prepared statements so SQL structure and data stay separate. Add least-privilege database accounts, allowlist validation where query identifiers cannot be parameterized, and safe error handling. ORMs and stored procedures can still be vulnerable if they construct unsafe dynamic SQL. See [OWASP SQLi Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html).

**Common mistakes candidates make:** Saying normalization means zero redundancy in all practical databases; mixing up `WHERE` and `HAVING`; assuming `LEFT JOIN` gives only matched rows; claiming an index speeds every query; treating ACID consistency as the same thing as isolation; believing a password stored in plaintext is acceptable because the SQL query is parameterized.

## Likely interview questions

1. **What is normalization, and why is it useful?**  
   Normalization structures relational tables so each fact is stored in an appropriate place, reducing duplicate data and update, insert, or delete anomalies. First normal form removes repeating groups, second removes partial-key dependencies, and third removes transitive dependencies. Some systems denormalize deliberately for performance, but then they must manage consistency.

2. **Explain ACID with an example.**  
   ACID describes transaction guarantees. Atomicity means a funds transfer's debit and credit are one unit. Consistency preserves constraints. Isolation controls interaction with concurrent transfers. Durability means a committed transfer survives according to the database's durability guarantee. Together these properties help keep data correct during errors and concurrency.

3. **What is the difference between primary and foreign keys?**  
   A primary key uniquely identifies each row in its table and cannot be null. A foreign key refers to a key in another table, enforcing a relationship and helping prevent invalid references. For example, an employee's `department_id` can reference a department's primary key. Foreign keys need not be unique.

4. **Explain `INNER JOIN` vs `LEFT JOIN`.**  
   An inner join returns rows with a match on both sides. A left join also retains all rows from the left table and fills right-side columns with null when no match exists. For example, use an inner join for employees with departments and a left join to list every department, including ones without employees.

5. **What does an index do, and what is the cost?**  
   An index stores an additional lookup structure so the database can locate matching rows faster without scanning every row. It can help filters, joins, and sorting. The trade-off is extra disk space and maintenance during inserts, updates, and deletes. I would check query plans before deciding which indexes to add.

6. **What are transaction isolation anomalies?**  
   A dirty read sees another transaction's uncommitted changes. A non-repeatable read observes a row changing between reads in one transaction. A phantom occurs when rows matching a condition appear or disappear between reads. Isolation levels balance stronger guarantees against concurrency and performance, and exact behavior depends on the database.

7. **What is SQL injection and how do you stop it?**  
   SQL injection happens when user input becomes part of SQL syntax instead of being treated as a value. The key defense is parameterized queries, where the command structure and supplied values are separate. I would also restrict database permissions, validate any dynamic identifiers against an allowlist, and avoid disclosing database errors to users.

8. **`WHERE` vs `HAVING` and `COUNT(*)` vs `COUNT(column)`?**  
   `WHERE` filters rows before aggregation, while `HAVING` filters groups after aggregation. `COUNT(*)` counts rows in a group, including rows with nulls. `COUNT(column)` counts only rows where that column is not null. For a department employee count, using `COUNT(employee.id)` with a left join correctly returns zero for empty departments.

---

# 3. Object-Oriented Programming (OOP)

**OOP** organizes software around objects that combine state and behavior. It can make large systems easier to extend, but security still depends on explicit authorization, validation, and appropriate design.

## 3.1 Four pillars with real-world examples

| Pillar | Plain meaning | Real-world example | Technical detail |
|---|---|---|---|
| **Encapsulation** | Keep data and the operations controlling it together | A bank account permits `deposit()` instead of arbitrary edits to its balance | Hide state behind controlled methods and access boundaries |
| **Abstraction** | Expose what a component does while hiding how | A payment interface exposes `pay()` without exposing bank-specific steps | Interfaces and abstract classes express contracts |
| **Inheritance** | Reuse or specialize behavior from another type | `SavingsAccount` specializes an `Account` type | An is-a relation; can be single or multiple depending on language |
| **Polymorphism** | Same operation behaves differently for different types | `pay()` works for card and UPI implementations | Dynamic dispatch/overriding; overloading is compile-time polymorphism in languages supporting it |

**Security connection:** Encapsulation avoids uncontrolled state modification, and abstraction gives a stable interface for enforcing checks. Neither guarantees security if an API method fails to verify who may call it.

## 3.2 Important interview comparisons

| Pair | Difference |
|---|---|
| Class vs object | Class is a blueprint/type; object is an instance with state |
| Encapsulation vs abstraction | Encapsulation controls access to internal state; abstraction hides unnecessary implementation details |
| Inheritance vs composition | Inheritance models an is-a relation; composition combines components in a has-a relation and often reduces coupling |
| Overloading vs overriding | Overloading uses the same name with different parameter signatures where supported; overriding replaces inherited behavior in a subtype |
| Compile-time vs runtime polymorphism | Selection is resolved by compiler/static rules vs dynamic type/dispatch during execution |
| Abstract class vs interface | Both define contracts; abstract classes may share state/implementation, interfaces focus on capabilities; exact rules vary by language |
| Constructor vs method | Constructor initializes an object; a method performs operations on or related to it |
| Access specifiers | `public`, `private`, and `protected` control visibility; precise behavior is language-specific |

**SOLID in one pass:** Single Responsibility (one reason to change); Open/Closed (extend without repeatedly modifying stable code); Liskov Substitution (subtypes honor expected behavior); Interface Segregation (small focused contracts); Dependency Inversion (depend on abstractions rather than concrete implementations). These are design guidelines, not rigid laws.

## 3.3 Simple pseudocode example

```text
interface Notifier:
    send(message)

class EmailNotifier implements Notifier:
    send(message): deliver email

class SMSNotifier implements Notifier:
    send(message): deliver SMS

class AlertService:
    constructor(notifier: Notifier)
    notify(message): notifier.send(message)
```

`AlertService` depends on the `Notifier` abstraction. Both notification implementations can be substituted without changing `AlertService`. The notation is language-neutral, not directly runnable code.

**Common mistakes candidates make:** Defining encapsulation as just “making everything private”; confusing overriding with overloading; saying all languages support multiple class inheritance; using inheritance where composition is more appropriate; claiming protected attributes alone implement authorization.

## Likely interview questions

1. **What are the four pillars of OOP?**  
   Encapsulation controls how an object's internal state is accessed. Abstraction exposes essential behavior while hiding implementation. Inheritance lets a specialized type reuse or extend another type. Polymorphism lets one interface work with different implementations. A payment system can demonstrate all four through a shared payment contract and different payment providers.

2. **Encapsulation vs abstraction?**  
   Encapsulation groups data with methods and controls direct access to internal state, such as requiring account changes through `withdraw()`. Abstraction hides implementation complexity behind a simple interface, such as calling `pay()` without knowing the provider's network flow. They are related, but one emphasizes controlled access and the other emphasizes a simplified contract.

3. **Overloading vs overriding?**  
   Overloading means methods can share a name but accept different parameter signatures, with resolution often based on compile-time types. Overriding means a subclass supplies its own implementation of an inherited method, often selected dynamically at runtime. The exact language rules differ, so I would reference the language used in the codebase.

4. **Interface vs abstract class?**  
   Both describe behavior that concrete types implement. An interface is commonly used as a contract for capabilities, while an abstract class can also contain shared implementation and state. In languages such as Java, the details have evolved over time. I choose based on whether I need a shared base implementation or merely a stable contract.

5. **Inheritance vs composition?**  
   Inheritance expresses an is-a relationship, such as `SavingsAccount` is an `Account`. Composition expresses a has-a relationship, such as `OrderService` has a `PaymentGateway`. Composition often makes components easier to replace and test, so I prefer it when I only need to reuse a capability rather than model a true subtype.

6. **What is polymorphism?**  
   Polymorphism means the same operation can be applied to values of different compatible types. For example, a `Notifier` interface has a `send()` method, and both email and SMS implementations provide their own behavior. The calling service uses the interface and does not need conditional logic for every concrete implementation.

7. **What is SOLID?**  
   SOLID is a set of object-oriented design principles: Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, and Dependency Inversion. The common goal is maintainable software with clear responsibilities and low coupling. For example, depending on a payment interface rather than a particular vendor makes testing and replacing implementations easier.

8. **Can OOP prevent security vulnerabilities?**  
   OOP can support security by enforcing controlled state changes, reusable validation components, and clean boundaries. But an object with private fields can still expose an insecure method or authorize the wrong user. Security needs explicit access checks, safe inputs, secure storage, testing, and threat modeling in addition to good class design.

---
# 4. Computer Networks

A **computer network** lets devices exchange data using shared rules called protocols. Cybersecurity interviews often test whether you can explain how traffic reaches an application, where it is exposed, and which controls apply at each layer.

## 4.1 OSI vs TCP/IP

The **OSI model** is a seven-layer teaching and troubleshooting model. The **TCP/IP model** groups functions into fewer practical layers. Real protocols do not always fit perfectly into a single box.

| OSI layer | Responsibility and example | Common TCP/IP grouping |
|---|---|---|
| 7 Application | Application protocols, HTTP, DNS | Application |
| 6 Presentation | Data representation, encoding, encryption concepts | Application |
| 5 Session | Dialog/session management concepts | Application |
| 4 Transport | End-to-end transport, TCP and UDP | Transport |
| 3 Network | Logical addressing and routing, IP | Internet |
| 2 Data Link | Local network framing, Ethernet, MAC | Link/network access |
| 1 Physical | Bits, cables, radio signals | Link/network access |

**Encapsulation:** application data becomes a transport segment/datagram, then an IP packet, then a link-layer frame. At the receiving device, layers interpret their own headers. **Switches** commonly forward by MAC address within a LAN, while **routers** forward IP packets between networks. Modern equipment often combines roles.

## 4.2 IP addressing and subnetting

An **IP address** identifies a network interface for routing. An **IPv4** address contains 32 bits, represented as four octets such as `192.168.1.10`. An **IPv6** address contains 128 bits, such as `2001:db8::1` (the `2001:db8::/32` block is reserved for documentation examples).

**CIDR** `/n` means the first `n` bits identify the network prefix. A typical IPv4 `/24` provides `2^(32-24) = 256` addresses, of which 254 are conventionally assignable to hosts in an ordinary subnet because the network and broadcast addresses are reserved.

**Worked example:** `192.168.10.34/26`

- Prefix 26 bits; host portion 6 bits; subnet mask `255.255.255.192`.
- Each block has `2^6 = 64` IPv4 addresses; block boundaries in last octet are 0, 64, 128, 192.
- `34` lies in the `0` to `63` block. Network: `192.168.10.0`; broadcast: `192.168.10.63`.
- Conventional host range: `192.168.10.1` to `192.168.10.62`, or **62** usable addresses.
- **Caveat:** `/31` point-to-point and `/32` host-route use cases do not follow the simple “subtract two” rule.

**Private IPv4 ranges:** `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16`. Loopback includes `127.0.0.0/8`, commonly `127.0.0.1`. `169.254.0.0/16` is IPv4 link-local. Private addresses are not publicly routed on the Internet by ordinary routers; NAT may connect them to public networks.

## 4.3 TCP vs UDP and the TCP handshake

| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented | Connectionless datagrams |
| Delivery | Ordered, reliable byte stream while connection works | No built-in delivery or ordering guarantees |
| Flow/congestion control | Built in | Applications/protocols implement behavior as needed |
| Typical uses | HTTPS over TCP, SSH, SMTP | DNS queries, VoIP, QUIC transport |
| Security note | Reliability is not encryption | UDP is not inherently insecure; TLS-like protections can run above it |

**TCP's three-way handshake:**

```text
Client                                   Server
  | -------- SYN, seq=x ----------------> |
  | <---- SYN-ACK, seq=y, ack=x+1 ------ |
  | -------- ACK, ack=y+1 -------------> |
             Connection established
```

The handshake synchronizes sequence numbers and establishes connection state. It does **not** encrypt the connection. A **SYN flood** can exhaust resources devoted to incomplete handshakes; mitigations can include SYN cookies, rate controls, and network protections. **TCP flags:** SYN starts a connection, ACK acknowledges data, FIN closes gracefully, RST resets abruptly.

## 4.4 Common protocols in a web request

1. **DNS:** Resolves a hostname, such as `example.com`, to a suitable record, commonly an IP address. A resolver may use caches and query authoritative DNS servers. Common DNS records include `A` (IPv4), `AAAA` (IPv6), `CNAME` (alias), `MX` (mail exchange), and `TXT` (text-based policy/verification). Classic DNS commonly uses UDP 53, with TCP when required; encrypted alternatives include DoT and DoH.
2. **DHCP:** Automatically supplies network configuration such as an IPv4 address, subnet mask, default gateway, and DNS resolver. IPv4 learning sequence: **Discover, Offer, Request, Acknowledge** (DORA).
3. **ARP:** On IPv4 local networks, resolves a next-hop IP address to a link-layer MAC address. ARP spoofing can redirect LAN traffic. IPv6 uses Neighbor Discovery rather than ARP.
4. **Routing and NAT:** A router selects a next hop toward the destination. **NAT** rewrites address information, often with ports in PAT/NAPT, so multiple private hosts can share a public address. NAT is not equivalent to a firewall.
5. **HTTP:** An application-layer request/response protocol. Methods include `GET` (retrieve), `POST` (submit/process), `PUT` (replace target representation), `PATCH` (partial update), and `DELETE` (request deletion). Authorization must be checked regardless of method.
6. **HTTPS:** HTTP protected by TLS to provide confidentiality and integrity in transit and authenticate the server when certificate validation succeeds. HTTP/3 uses QUIC over UDP, with TLS 1.3 integrated into QUIC.

**Status codes:** 2xx success (`200`, `201`), 3xx redirection (`301`, `302`), 4xx client-side request issues (`400`, `401` unauthenticated/missing valid credentials, `403` authenticated or unauthenticated but forbidden, `404` not found, `429` too many requests), and 5xx server errors (`500`, `503`). `401` vs `403` has protocol nuances, but the distinction is useful in interviews.

## 4.5 Firewalls and network defenses

A **firewall** applies rules to allow, block, or inspect traffic. A host firewall controls traffic at one machine; a network firewall protects network segments. A **stateful firewall** tracks connections. **Next-generation firewalls** may inspect application protocols and use threat intelligence. A **WAF** targets HTTP(S) application traffic, such as requests that resemble common web attacks. A WAF does not replace correct application code.

- **IDS** detects suspicious activity and raises alerts; **IPS** can actively block traffic when deployed inline.
- **Network segmentation** limits movement between systems after compromise.
- **Default deny** allows only explicitly approved traffic, where operationally feasible.
- **VPN** creates an authenticated/encrypted tunnel for designated traffic; what traffic it carries depends on configuration.

### Ports to memorize

Ports are transport endpoints, not measures of security. Services may be configured on different ports; some appear on both TCP and UDP.

| Port | Common service | Transport/qualification |
|---|---|---|
| 20/21 | FTP data/control in traditional active mode | TCP; FTP modes vary |
| 22 | SSH, SFTP | TCP |
| 23 | Telnet | TCP, unencrypted by default |
| 25 | SMTP server-to-server | TCP |
| 53 | DNS | UDP and TCP |
| 67/68 | DHCPv4 server/client | UDP |
| 80 | HTTP | TCP |
| 110 | POP3 | TCP |
| 123 | NTP | UDP |
| 143 | IMAP | TCP |
| 161/162 | SNMP requests/traps | Usually UDP |
| 389 | LDAP | TCP/UDP depending on use |
| 443 | HTTPS | TCP, or UDP for HTTP/3/QUIC |
| 445 | SMB | TCP |
| 465 | SMTP submission over implicit TLS | TCP, deployment-specific |
| 587 | SMTP message submission | TCP, commonly STARTTLS |
| 636 | LDAPS | TCP |
| 993/995 | IMAPS/POP3S | TCP |
| 1433 | Microsoft SQL Server | Typically TCP |
| 3306 | MySQL | TCP |
| 3389 | RDP | TCP and UDP depending on use |
| 5432 | PostgreSQL | TCP |
| 6379 | Redis | TCP, default port, restrict exposure |
| 8080 | Common alternate HTTP/application port | Usually TCP, convention only |

**Common mistakes candidates make:** Saying OSI is the exact implementation of today's Internet; calling the TCP handshake encrypted; treating UDP as always faster in every scenario; subtracting two host addresses for every CIDR; claiming NAT itself enforces authorization; assuming HTTPS makes a vulnerable website secure.

## Likely interview questions

1. **Explain the OSI model.**  
   OSI is a seven-layer conceptual model: physical, data link, network, transport, session, presentation, and application. It helps isolate network issues. For example, IP routing belongs at layer 3, TCP at layer 4, and HTTP at the application layer. The Internet is more commonly described with the TCP/IP stack, so the mapping is approximate.

2. **TCP vs UDP?**  
   TCP provides a connection-oriented, ordered byte stream with retransmission and built-in flow and congestion control. UDP sends datagrams without built-in delivery or ordering guarantees. TCP is used by SSH and many HTTPS connections, while DNS and QUIC commonly use UDP. Reliability and security depend on the full protocol stack, not the transport alone.

3. **Explain the three-way handshake.**  
   The client sends SYN with an initial sequence number. The server responds with SYN-ACK, acknowledging the client's SYN and sending its own sequence number. The client sends ACK to confirm the server's sequence. This establishes TCP connection state. It is separate from the TLS handshake that secures HTTPS.

4. **What happens when you type a URL in a browser?**  
   The browser determines the destination, typically resolves the hostname through DNS, and opens transport connectivity. For HTTPS over TCP, it establishes TCP and then performs TLS certificate validation and key negotiation. It sends an HTTP request and receives the response, which the browser renders. Caches, proxies, HTTP/3, and existing connections can change the details.

5. **How do you calculate hosts in a `/26` subnet?**  
   IPv4 has 32 address bits. A `/26` prefix leaves six host bits, so a subnet contains 64 addresses. In a conventional subnet, two are reserved for network and broadcast, leaving 62 usable host addresses. The block size is 64 in the last octet. Special `/31` and `/32` cases work differently.

6. **What is DNS, DHCP, and ARP?**  
   DNS maps names to records such as IP addresses. DHCP gives a device its network settings automatically, often through Discover, Offer, Request, and Acknowledge. ARP maps an IPv4 next-hop address to a MAC address on the local link. They solve different problems, and IPv6 uses Neighbor Discovery rather than ARP.

7. **What is the difference between a firewall, IDS, IPS, and WAF?**  
   A firewall enforces network traffic rules, often using addresses, ports, state, and sometimes application information. An IDS observes and alerts on suspicious activity. An IPS can block traffic inline. A WAF specializes in HTTP application requests. Their effectiveness depends on placement, policies, tuning, and the application's own security.

8. **What is NAT? Does it make a network secure?**  
   NAT rewrites IP address information, and often ports, as packets cross a boundary. It lets private IPv4 systems communicate through shared public addresses. It can reduce direct address exposure in some designs, but it is not a substitute for firewall rules, strong authentication, patching, or network segmentation.

---

# 5. Cryptography

**Cryptography** uses mathematical techniques to protect information. Different tools solve different problems: encryption provides confidentiality, hashes help check integrity, message authentication checks authenticity and integrity, and digital signatures bind an action to a private signing key.

## 5.1 Symmetric vs asymmetric encryption

| Aspect | Symmetric encryption | Asymmetric cryptography |
|---|---|---|
| Keying | Same secret key for encryption/decryption | Public and private key pair |
| Typical speed | Fast for large data | More computationally expensive |
| Main challenge | Safely sharing/managing the secret key | Validating public keys and securely storing private keys |
| Examples | AES-GCM, ChaCha20-Poly1305 | RSA for supported encryption/signature uses; ECDH for key agreement; ECDSA/EdDSA for signatures |
| Practical use | Bulk data encryption | Authentication, signatures, key establishment |

**Important nuance:** Not every asymmetric algorithm does encryption. **ECDH** establishes shared key material; **ECDSA** signs; neither is a generic file-encryption algorithm. AES-GCM and ChaCha20-Poly1305 are **authenticated encryption** modes, providing confidentiality plus an integrity/authenticity check when used with unique nonces as required.

**Keys vs passwords:** A key is cryptographic input; a human password may be used to derive a key through an appropriate password-based key derivation function. Key management and nonce handling are often more important than choosing a fashionable algorithm.

## 5.2 Hashing, HMAC, and passwords

A **cryptographic hash** maps variable-length input to fixed-length output. It is designed to be one-way and collision-resistant, although no hash offers mathematical impossibility of collisions. Example: SHA-256 produces a 256-bit digest. Changing the input normally changes the digest unpredictably.

- **Hashing is not encryption:** there is no decryption key for SHA-256.
- **Integrity check:** Compare hashes when the expected digest comes from a trusted source; an attacker who can replace both data and digest defeats this check.
- **HMAC:** Uses a shared secret with a hash to authenticate a message and detect modification.
- **Password storage:** Do **not** store plaintext or use bare fast SHA-256 as a password verifier. Use a deliberately slow password-hashing/KDF scheme such as **Argon2id**, or appropriately configured bcrypt/PBKDF2 where required, with a unique salt per password. A salt is not a secret; a **pepper** is an optional separate secret.

## 5.3 Digital signatures, certificates, and PKI

A **digital signature** proves that data was signed by someone controlling a private key and that signed data was not altered. The signer signs using a private key; a verifier checks with the matching public key. A signature does **not** hide plaintext.

A **digital certificate**, typically X.509, links an identity, such as a domain name, to a public key and is signed by a certificate authority (CA). **Public Key Infrastructure (PKI)** covers authorities, issuance, trusted roots, validation, renewal, and revocation mechanisms. Browsers validate chains, identity matching, expiry, and relevant certificate constraints. Revocation checking behavior varies by platform and context.

- **Public key:** Shareable, but its association with an identity must be verified.
- **Private key:** Must remain secret; compromise can undermine signatures or server identity.
- **Certificate:** Does not certify a site's business honesty or absence of bugs. It mainly supports authenticated key binding.
- **Self-signed certificate:** Can encrypt traffic if used correctly, but does not automatically establish browser trust without a configured trust anchor or explicit verification.

## 5.4 How TLS combines them

**TLS** secures communication over an untrusted network. A simplified modern **TLS 1.3** HTTPS handshake:

1. Client and server exchange supported settings and key-share material.
2. They typically perform ephemeral Diffie-Hellman key agreement to derive shared secrets without sending the secret itself.
3. The server sends a certificate and proves possession of its authentication private key through handshake signatures.
4. The client validates the certificate chain and target hostname before treating the connection as authenticated.
5. Both derive symmetric traffic keys, then use authenticated symmetric encryption for application data.

**Why combine methods?** Asymmetric signing and key agreement solve authentication and secure key establishment; symmetric encryption efficiently protects many data bytes. **Forward secrecy** means compromising a long-term server authentication key later does not, by itself, reveal past session keys established with fresh ephemeral secrets. TLS does not protect data once an endpoint is compromised or the application sends data to the wrong recipient.

**Common mistakes candidates make:** Saying hashing can be decrypted; treating all asymmetric cryptography as public-key encryption; claiming a digital signature encrypts the message; storing passwords with regular SHA-256; saying TLS encrypts all web traffic directly using the server's RSA public key; assuming any certificate makes a site safe.

## Likely interview questions

1. **Symmetric vs asymmetric encryption?**  
   Symmetric encryption uses the same secret key to encrypt and decrypt data and is efficient for bulk data. Asymmetric cryptography uses public and private keys for operations such as signatures and key agreement, depending on the algorithm. Modern TLS combines both approaches so endpoints can authenticate and establish efficient symmetric session encryption.

2. **Encryption vs hashing?**  
   Encryption is reversible by an authorized party with the required key and mainly provides confidentiality. Hashing is a one-way digest function with no decryption operation and is often used in integrity checks. For passwords, I would use an adaptive password-hashing algorithm such as Argon2id, not ordinary SHA-256 alone.

3. **What is a digital signature?**  
   A digital signature is generated with a private signing key and verified with the corresponding public key. A valid signature supports authenticity of the signed keyholder and integrity of the message, assuming the key and verification are trustworthy. It does not make the signed content confidential because the message may remain visible.

4. **What is a digital certificate and CA?**  
   A certificate binds an identity, such as a website hostname, to a public key and is signed by an issuer. A certificate authority is a trusted issuer within a PKI chain. The browser checks the chain, hostname, dates, and other constraints. A valid certificate helps authenticate the server but does not guarantee the application is vulnerability-free.

5. **Walk me through TLS at a high level.**  
   The client and server negotiate TLS parameters and establish shared secret material, commonly using ephemeral Diffie-Hellman. The server authenticates with its certificate and handshake proof. Both derive symmetric keys to protect application data using authenticated encryption. TLS gives confidentiality and integrity in transit, not protection against a compromised endpoint or insecure application logic.

6. **Why is SHA-256 alone unsuitable for storing passwords?**  
   SHA-256 is intentionally fast, which lets an attacker test many guessed passwords if a database of hashes leaks. Password storage requires an adaptive, resource-expensive algorithm such as Argon2id or an appropriately configured alternative. Each password gets its own random salt. Rate limiting and multifactor authentication provide additional protection.

7. **What is forward secrecy?**  
   Forward secrecy means disclosure of a server's long-term authentication private key does not automatically reveal past session traffic. Modern TLS achieves this with fresh ephemeral key agreement, such as ECDHE, and proper key erasure. It does not protect sessions if the endpoint or ephemeral secrets were compromised at the time of communication.

8. **What is the difference between a hash and an HMAC?**  
   A regular hash produces a digest anyone can compute for a message, so by itself it cannot prove who created the message. HMAC combines a hash function with a shared secret key so recipients who know that secret can validate authenticity and integrity. It does not provide non-repudiation because both sides share the key.

---
# 6. Web Security

**Web security** protects the browser, server, API, user identity, and stored data from misuse. The central rule is **never trust client-controlled data or decisions**. Authenticate the caller, authorize every sensitive operation, use context-appropriate input/output handling, and log significant security events.

## 6.1 OWASP Top 10:2025

The **OWASP Top 10** is an awareness list of major web application security risk categories, **not** a complete testing checklist or a ranking of every individual bug. This is the official [2025 list](https://top10.owasp.org/).

Each row describes **what it is**, **how the weakness is abused**, a **short example**, and **prevention**.

| OWASP category | What it is and how abuse occurs | Short example | Prevent or reduce risk |
|---|---|---|---|
| **A01 Broken Access Control** | A system fails to enforce who may read or change an object or perform an action; attacker uses unauthorized paths or identifiers | User A requests User B's invoice ID and the server returns it | Server-side object/function authorization, deny by default, ownership/tenant checks, tests |
| **A02 Security Misconfiguration** | Unsafe settings, defaults, unnecessary features, or exposure create attack paths | Public debug console reveals environment details | Harden baselines, disable debug, restrict services, security configuration reviews |
| **A03 Software Supply Chain Failures** | Dependencies, build artifacts, packages, or delivery systems are compromised or untrusted | Malicious dependency update enters a build | Inventory dependencies, pin/verify provenance, review updates, isolate CI, scan and monitor |
| **A04 Cryptographic Failures** | Missing, outdated, or misused cryptography exposes sensitive data | Passwords stored in plaintext or traffic sent over HTTP | TLS, proper password hashing, authenticated encryption, key management |
| **A05 Injection** | Untrusted input is interpreted as a command or query rather than data | Concatenated input alters a SQL query | Parameterized SQL, safe APIs, contextual encoding/validation, least privilege |
| **A06 Insecure Design** | The system lacks security controls in its intended workflow or architecture | Password-reset process omits identity verification | Threat modeling, abuse cases, secure design requirements, rate limits, review |
| **A07 Authentication Failures** | Weak login, session, or identity verification allows account compromise | No rate control on password guessing | MFA, secure credentials and sessions, rate limits, monitoring |
| **A08 Software or Data Integrity Failures** | Untrusted data or software changes are accepted without adequate integrity checks | Application loads an unsigned, attacker-modified update | Verify signatures/provenance, protect pipelines, use trusted update channels |
| **A09 Security Logging and Alerting Failures** | Security-relevant events are missing, not analyzed, or not acted on | Repeated failed privileged logins generate no alert | Structured logs, centralized monitoring, actionable alerts, retention and testing |
| **A10 Mishandling of Exceptional Conditions** | Error and unexpected-state handling fails insecurely | An exception skips a transaction rollback or authorization step | Fail safely, handle exceptions, rollback, safe defaults, sanitize errors, test edge cases |

**Why versions matter:** In **OWASP Top 10:2021**, Injection was A03 and SSRF was A10. In **2025**, Injection is A05 and A10 concerns exceptional conditions. Quote the version rather than memorizing only numbers. Several familiar vulnerabilities below are specific attack patterns inside broader categories.

## 6.2 SQL injection (SQLi)

- **What:** An attacker makes untrusted input change a SQL statement.
- **How:** The application assembles SQL syntax by concatenating request values; crafted input alters the query's intended conditions or structure.
- **Example:** An unsafe login or search query returns records that the supplied user value should not match.
- **Prevent:** Use bound parameters/prepared statements; allowlist any unavoidable dynamic SQL identifiers; grant limited DB privileges; hide detailed SQL errors. Input filtering alone is not a reliable primary defense.

```python
# Conceptual safe database-driver pattern, with placeholder syntax dependent on driver
cursor.execute("SELECT id FROM users WHERE email = ?", (email,))
```

## 6.3 Cross-site scripting (XSS)

- **What:** Untrusted content runs as JavaScript in another user's browser in the security context of a legitimate site.
- **How:** The application places attacker-controlled content into an HTML/JavaScript/DOM context without the needed protection. **Stored XSS** persists on the server, **reflected XSS** appears in a response, and **DOM-based XSS** arises from unsafe browser-side processing.
- **Example:** A support comment contains markup that the app renders as executable script instead of text.
- **Prevent:** Use framework auto-escaping, **context-specific output encoding**, safe DOM APIs such as `textContent`, sanitization for permitted rich HTML, and a restrictive CSP as defense in depth. Avoid unsafe `innerHTML` and `eval` for untrusted content. `HttpOnly` limits JavaScript access to cookies but does not stop XSS execution.

## 6.4 Cross-site request forgery (CSRF)

- **What:** A malicious site causes a victim's browser to submit an unwanted action to another site where the victim is already authenticated.
- **How:** The browser may automatically attach relevant cookies to a cross-site request. If the server accepts the request without checking its intent, the action may succeed.
- **Example:** A malicious page tricks a logged-in user's browser into submitting a change-email request.
- **Prevent:** Verify anti-CSRF tokens on state-changing requests, use `SameSite` cookies appropriately, check `Origin`/`Referer` as supplementary measures, and require reauthentication for high-risk actions. Do not make sensitive changes through GET requests. The effective defense depends on the authentication model and browser behavior.

## 6.5 IDOR and broken object authorization

- **What:** **Insecure Direct Object Reference (IDOR)** is a common object-level access-control flaw in which changing a reference grants unauthorized access. The API-specific term **BOLA** is closely related.
- **How:** The server trusts a client-provided ID and checks that the user is logged in, but fails to verify the user's right to that specific object.
- **Example:** A user changes `/api/invoices/101` to `/api/invoices/102` and receives another customer's invoice.
- **Prevent:** Check tenant and ownership or policy authorization **on the server for every object and action**, including downloads and updates. Non-guessable UUIDs can reduce enumeration but do not replace authorization.

## 6.6 Broken authentication and authorization

- **What:** **Authentication** verifies identity; **authorization** decides permitted actions. Authentication failures let attackers impersonate users; authorization failures let authenticated or unauthenticated users exceed permissions.
- **How:** Weak password handling, credential stuffing without controls, insecure reset flows, predictable tokens, or missing role checks lead to compromise.
- **Example:** A reused password is guessed across many accounts, or a normal user calls an admin-only endpoint accepted without a role check.
- **Prevent:** MFA, rate limiting and bot controls, secure account recovery, breached-password screening, adaptive password hashing, session invalidation, server-side RBAC/ABAC/ownership checks, and security-event monitoring. MFA is an important defense but not a substitute for authorization.

**Related comparison:** RBAC grants permissions by role, such as analyst or admin. ABAC evaluates attributes such as department, resource sensitivity, and context. **Least privilege** grants only what is necessary.

## 6.7 JWT security

- **What:** A **JSON Web Token (JWT)** is a compact token format with header, payload, and signature or other protection depending on the JWT type. A common **signed JWT (JWS)** authenticates claims but does **not** encrypt them.
- **How:** Attackers may steal a bearer token, exploit weak signing-key handling, abuse incorrect algorithm validation, or reuse a token when expiration, audience, and issuer checks are absent.
- **Example:** A service accepts a token signed for a different API because it fails to validate `aud` and `iss`, or a stolen bearer token is replayed.
- **Prevent:** Verify signatures with configured algorithms, validate `exp`, `nbf` where used, `iss`, `aud`, and expected token type, secure signing keys, use short-lived access tokens and planned revocation/rotation strategies. Avoid sensitive information in readable payloads. If tokens are placed in cookies, apply cookie and CSRF defenses; do not assume local storage is safe from XSS.

**JWT anatomy:** `header.payload.signature` is the familiar three-part format of a compact signed JWT. The sections are Base64URL encoded, **not automatically confidential**. Encrypted JWTs exist and have different structures.

## 6.8 Session and cookie security

- **What:** A **session** maintains login state across HTTP requests, often using an opaque session ID in a browser cookie and state on the server.
- **How:** Attackers exploit stolen/fixed session identifiers, unsecured transport, overly long sessions, or sessions not invalidated after logout/password changes.
- **Example:** An attacker reuses a stolen session cookie to impersonate a user until it expires or is revoked.
- **Prevent:** Use unpredictable session IDs, rotate them after login/privilege elevation, invalidate server-side sessions on logout, set idle/absolute timeouts, serve over HTTPS, and use `Secure`, `HttpOnly`, and suitable `SameSite` cookie flags.

| Cookie flag | Effect | Limit |
|---|---|---|
| `Secure` | Sends cookie only over secure transport, with browser-specific edge details | Cannot prevent attacks on a compromised server |
| `HttpOnly` | Prevents ordinary JavaScript reading the cookie | Does not prevent browser from sending it, or stop all XSS impact |
| `SameSite=Lax/Strict` | Restricts some cross-site cookie sending | Not a universal CSRF defense; rules differ by request/navigation |
| `Path`/`Domain` | Set where cookie is sent | Not a full security boundary against all subdomain or application threats |

**Stateful session vs stateless JWT:** A server-side session is easy to revoke centrally if its store is available. A self-contained signed JWT can be validated without a central session lookup, but instant revocation usually requires additional state or very short expiration. Either mechanism can be secure or insecure depending on implementation.

## 6.9 CORS

- **What:** **Cross-Origin Resource Sharing (CORS)** tells browsers which other **origins** may read an HTTP response through browser JavaScript. An origin is scheme + hostname + port. CORS is a browser-enforced resource-sharing policy, **not user authentication**.
- **How:** Overly broad or reflected `Access-Control-Allow-Origin` with credentials can permit an untrusted website's script to read responses that belong to a logged-in user, if browser cookie and credential rules also allow the request.
- **Example:** An API reflects an attacker-controlled `Origin` and sends `Access-Control-Allow-Credentials: true`, letting that site's script read a sensitive response in a vulnerable credentialed setup.
- **Prevent:** Allowlist exact trusted origins, restrict credentialed access, send `Vary: Origin` when dynamically selecting allowed origins, and enforce authentication/authorization independently on every API request. A wildcard `*` cannot authorize browser exposure of credentialed responses.

**CORS vs CSRF:** CORS primarily controls whether browser JavaScript **reads** cross-origin responses. CSRF concerns whether the browser can **send an unwanted authenticated action**. A failed CORS read does not prove CSRF is prevented. See [MDN CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS).

## 6.10 Security headers

**Security headers** instruct browsers to apply extra protections. Weak or missing headers can increase attack impact, but headers do not fix broken backend authorization.

| Header | What it does | How misuse can hurt; example | Prevention/configuration idea |
|---|---|---|---|
| `Content-Security-Policy` (CSP) | Restricts script/resource sources and some browser behaviors | Overly permissive `unsafe-inline` rules leave more XSS opportunities | Use strict nonces/hashes as appropriate; test policies; `frame-ancestors` limits framing |
| `Strict-Transport-Security` (HSTS) | Tells browsers to prefer HTTPS for the host for a period | HTTP downgrade opportunities if HTTPS is not consistently required | Use HTTPS everywhere and deploy suitable `max-age`, then consider subdomains/preload carefully |
| `X-Content-Type-Options: nosniff` | Restricts MIME type sniffing for certain resource types | Browser might interpret mislabeled content in unintended ways | Set `nosniff` with correct `Content-Type` |
| `Referrer-Policy` | Controls URL referrer detail disclosed when navigating | Sensitive URL details may leak to another site | Use an appropriate restrictive policy and do not put secrets in URLs |
| `Permissions-Policy` | Controls selected browser features in the document/iframes | Unneeded embedded content may access sensitive capabilities if otherwise allowed | Disable unneeded browser capabilities |
| `Set-Cookie` flags | Enforce cookie transport/script/cross-site rules | Session cookie readable by JS or sent too broadly | `Secure; HttpOnly; SameSite` as appropriate |

**Clickjacking:** An attacker puts a genuine site's button inside a deceptive frame so the victim clicks it unintentionally. Use CSP `frame-ancestors` (and `X-Frame-Options` for older browser compatibility where needed), alongside robust confirmation for sensitive actions. `X-XSS-Protection` is obsolete and not a substitute for safe output handling.

## 6.11 Other short attack concepts

- **SSRF:** Server-Side Request Forgery makes a server fetch an attacker-chosen URL, potentially reaching internal services or cloud metadata endpoints. Example: an image-fetch API accepts a URL pointing to an internal admin service. Prevent with strict destination allowlists, URL/IP validation that accounts for redirects and DNS changes, egress restrictions, and metadata-service protection.
- **Command injection:** Untrusted input is included in an OS command. Example: a diagnostic endpoint builds a shell command from a hostname. Prefer APIs that do not invoke a shell, and pass fixed commands/validated arguments rather than concatenating shell strings.
- **File upload flaws:** An upload feature accepts malicious or unexpected content, or makes it executable from a public path. Validate content/type/size, rename files, store outside executable paths, restrict access, and scan when appropriate.
- **XSS vs SQLi:** XSS executes in the victim's browser context; SQLi manipulates database commands on the server side. Defenses differ: output encoding/safe DOM handling vs parameterized queries.

**Common mistakes candidates make:** Treating CORS as an authorization system; saying JWTs are encrypted by default; believing `HttpOnly` prevents all XSS; fixing IDOR with random IDs alone; claiming input validation replaces parameterization; treating CSRF and XSS as the same attack; memorizing OWASP 2021 numbering as if it were 2025.

## Likely interview questions

1. **What is the OWASP Top 10?**  
   OWASP Top 10 is an awareness list of broad web application security risks. The 2025 version includes broken access control, security misconfiguration, software supply chain failures, cryptographic failures, injection, insecure design, authentication failures, integrity failures, logging and alerting failures, and mishandling exceptional conditions. I would always name the edition because categories and numbering change.

2. **Explain SQL injection and its prevention.**  
   SQL injection occurs when attacker-controlled input becomes SQL syntax rather than a bound value. Unsafe string concatenation can modify intended query behavior and expose or change records. Parameterized statements are the main defense. I would also apply database least privilege, limit dynamic identifiers to allowlisted values, and avoid exposing database details in error responses.

3. **What is XSS, and how is it different from SQL injection?**  
   XSS causes unsafe content to execute as script in the context of a website's user. It can be stored, reflected, or DOM-based. SQL injection instead changes commands sent to the database. For XSS, I use context-specific encoding, safe DOM APIs, HTML sanitization where needed, and CSP as defense in depth, not parameterized SQL.

4. **What is CSRF?**  
   CSRF is when an attacker causes a victim's browser to submit an unwanted action to a site where the victim is authenticated, often because cookies are attached automatically. The server should verify action intent through anti-CSRF tokens and appropriate origin checks, support suitable SameSite cookies, and avoid side effects on GET requests.

5. **What is IDOR or BOLA?**  
   IDOR happens when an application exposes an object identifier but does not verify that the current user is allowed to access that exact object. For example, changing an invoice ID returns another customer's invoice. The solution is server-side object-level authorization on every read and update. An unpredictable ID is not a permission check.

6. **Authentication vs authorization?**  
   Authentication answers who the user is; authorization answers what that user can do. Login establishes identity, but the application must still verify access for every sensitive action and resource. An authenticated analyst should not automatically access an administrator's functions or another customer's records. I would apply least privilege and deny access by default.

7. **What is a JWT, and what are its security risks?**  
   A common JWT is a signed token containing claims such as issuer, audience, subject, and expiry. Its Base64URL payload is readable, not encrypted. The backend must verify the signature using approved algorithms, validate expected claims, and protect tokens from theft. Token expiry, rotation, and revocation strategy matter for sessions and incident response.

8. **Session vs JWT? How would you secure a session?**  
   A server-side session stores login state behind an opaque identifier, while a signed JWT can carry claims that the service validates without a session database. Both need secure transport and lifecycle controls. For browser sessions I use unpredictable IDs, session rotation, expiration, logout invalidation, HTTPS, and `Secure`, `HttpOnly`, and appropriate `SameSite` cookie settings.

9. **What is CORS, and does it prevent CSRF?**  
   CORS is a browser policy that lets servers specify which origins may read responses through JavaScript. It does not authenticate users or replace API authorization. CSRF can still happen even if a browser blocks reading the cross-origin response. I would configure exact trusted origins and separate CSRF defenses for cookie-authenticated state-changing requests.

10. **Name useful security headers.**  
    CSP reduces opportunities for unwanted scripts and can restrict framing. HSTS helps browsers use HTTPS. `X-Content-Type-Options: nosniff` limits certain content-type confusion risks. Referrer-Policy controls outgoing referrer information. Secure cookie flags constrain how sessions are sent and accessed. These are defense-in-depth controls, not replacements for correct backend logic.

---

# 7. Security Concepts and Security Operations

**Cybersecurity** is the management of threats to systems, information, and business operations. Technical controls matter because they reduce the likelihood or impact of concrete business losses, such as data exposure, fraud, outages, or regulatory consequences.

## 7.1 CIA triad

| Principle | Meaning | Example control or incident |
|---|---|---|
| **Confidentiality** | Only authorized parties may see data | Encryption and access control protect customer information |
| **Integrity** | Data remains accurate and changes are authorized | Signatures, transaction rules, audit logs, and checksums support integrity |
| **Availability** | Systems and information are accessible when needed | Redundancy, backups, DDoS protection, and disaster recovery support uptime |

An incident can affect more than one property. Ransomware may cause both unavailability and loss of confidentiality if data is stolen. Encryption alone primarily supports confidentiality, not all of CIA.

## 7.2 Asset, threat, vulnerability, exploit, and risk

- **Asset:** Something valuable to protect, such as a customer database.
- **Threat:** A potential cause of harm, such as a criminal attempting data theft.
- **Vulnerability:** A weakness that could be exploited, such as a missing authorization check.
- **Exploit:** A method or action taking advantage of a vulnerability.
- **Risk:** The possibility and consequence of harm when threats affect assets through weaknesses or other exposure.
- **Control:** A safeguard that reduces likelihood or impact, such as MFA or network segmentation.

**Simple risk reasoning:** likelihood and impact help prioritize, but risk scores are organization-specific rather than universally exact arithmetic. Assess **inherent risk** before controls and **residual risk** after controls. **CVSS** helps characterize technical vulnerability severity; it does not automatically equal the business risk of that finding in a particular environment.

**Example:** Customer data is the asset. A missing ownership check is a vulnerability. An external attacker is a threat actor. Unauthorized access to records is a possible impact. The risk assessment depends on exposure, exploitability, sensitivity, and existing controls.

## 7.3 Red team vs blue team vs purple team

| Team | Main objective | Typical work |
|---|---|---|
| **Red team** | Emulate realistic attacker behavior to test defenses | Scoped adversary simulation and reporting |
| **Blue team** | Prevent, detect, investigate, and respond | Monitoring, incident response, hardening, detections |
| **Purple team** | Improve defenses through red/blue collaboration | Share techniques, test alerts, refine detection coverage |

A **penetration test** usually evaluates exploitability of defined targets under a specific scope. A **red-team engagement** may evaluate broader detection and response capability against a realistic objective. Names and scope vary by organization.

## 7.4 SOC, SIEM, EDR, and incident response

A **Security Operations Center (SOC)** monitors security events and coordinates investigation/response. A **Security Information and Event Management (SIEM)** platform aggregates, parses, searches, correlates, and alerts on logs from sources such as endpoints, identity providers, firewalls, cloud platforms, and applications.

**Illustrative SIEM detection:** Five failed logins followed by a successful privileged login from a new location may merit investigation. This is a **signal**, not proof of compromise. Analysts correlate timestamps, source IPs, device data, user context, and other evidence.

- **EDR:** Endpoint Detection and Response provides telemetry and response capabilities on supported endpoint devices.
- **Alert triage:** Validate the alert, determine scope, assess severity, and distinguish true positives from false positives. A **false negative** is missed malicious activity.
- **Incident response lifecycle, broadly:** prepare, detect/analyze, contain, eradicate, recover, and learn/improve. Framework terminology and ordering vary; preservation of evidence and communication matter throughout.
- **Security logs:** Should include relevant activity, identity/context, trustworthy timestamps, and integrity/access protections. Avoid logging passwords, full secrets, or unnecessary sensitive data.
- **Detection engineering:** Create hypotheses, identify telemetry, define detection logic, tune noise, test against expected behaviors, and update as threats change.

## 7.5 MITRE ATT&CK

**MITRE ATT&CK** is a knowledge base describing observed adversary behaviors. A **tactic** is the attacker's objective at a stage, such as credential access. A **technique** is how an attacker pursues that objective; **sub-techniques** describe more specific methods. ATT&CK helps map incident evidence and test detection coverage, but it is not itself a SIEM, scanner, or defense product.

**Illustrative mapping:** A phishing email may be mapped to **Phishing (T1566)** under Initial Access in relevant Enterprise ATT&CK contexts. A subsequent unauthorized credential-use event could map to other techniques depending on actual evidence. Do not infer the entire attack chain from a single alert. The [Enterprise ATT&CK matrix](https://attack.mitre.org/matrices/enterprise/) evolves, so tactics and identifiers should be checked before using them in a formal report.

**Kill chain vs ATT&CK:** A cyber kill chain is a higher-level sequential model of attack stages. ATT&CK gives a richer catalog of behaviors that can occur in varying order and combinations.

## 7.6 Penetration testing phases

A **penetration test** is an **authorized**, time-bounded security assessment that safely attempts to validate exploitable weaknesses. Written permission, scope, and rules of engagement come before testing.

| Phase | What happens | Deliverable or safeguard |
|---|---|---|
| 1. Scope and authorization | Agree targets, permitted methods, hours, contacts, data handling, stop conditions | Rules of engagement, written approval |
| 2. Reconnaissance | Collect permitted public or target information | Inventory and initial attack surface |
| 3. Discovery and scanning | Identify hosts, services, software and likely weaknesses | Documented findings requiring validation |
| 4. Vulnerability analysis | Check whether results are genuine and relevant | Validated candidates, reduced false positives |
| 5. Controlled exploitation | Safely demonstrate impact within approved limits | Reproducible evidence, minimum necessary access |
| 6. Post-exploitation assessment | Evaluate reachable data/privileges only as explicitly allowed | Business impact and attack-path description |
| 7. Reporting and remediation | Explain risk, evidence, reproduction boundaries, fixes | Executive summary, technical report, priorities |
| 8. Retesting | Verify fixes and identify residual issues | Updated status and evidence |

**Vulnerability scanning vs penetration testing:** A scanner looks for potential weaknesses using automated checks. A penetration test validates meaningful exploit paths and impact using controlled human analysis plus tools. Neither guarantees that all weaknesses have been found.

**Finding/report structure:** Title, affected asset, description, preconditions, evidence, business impact, severity/rationale, clear remediation, owner, and retest status. Protect confidential client data and follow agreed disclosure rules.

## 7.7 Risk communication in a consulting interview

A consulting analyst must translate technical findings into decisions. Instead of only saying “IDOR is critical,” state: **“A missing server-side ownership check could expose one client's invoices to another client. The priority depends on the records' sensitivity, whether the endpoint is internet-exposed, and compensating controls. Remediation is object-level authorization, followed by regression testing and monitoring.”**

When unsure of severity, avoid asserting a universal rating. Explain likelihood, impact, evidence, and remediation. Escalate urgent issues using the agreed process, and distinguish confirmed facts from hypotheses.

**Common mistakes candidates make:** Confusing a threat with a vulnerability; saying high CVSS automatically means highest business risk; assuming SIEM blocks attacks by itself; treating every alert as proof; using ATT&CK as a linear checklist; starting penetration tests without authorization; listing tools instead of explaining methodology, findings, and impact.

## Likely interview questions

1. **Explain the CIA triad.**  
   The CIA triad represents confidentiality, integrity, and availability. Confidentiality restricts data to authorized parties; integrity prevents unauthorized or unnoticed changes; availability keeps services usable when needed. For a banking service, access controls protect records, transaction checks support integrity, and resilient infrastructure supports availability. Controls often contribute to more than one goal.

2. **Threat vs vulnerability vs risk?**  
   A threat is a potential source of harm, such as an attacker seeking customer data. A vulnerability is an exploitable weakness, such as a missing object-level authorization check. Risk is the possibility and consequence of the threat affecting the asset through that weakness. I would evaluate exposure, likelihood, business impact, and existing controls rather than rank only by a technical score.

3. **Red team vs blue team?**  
   The red team emulates attacker behavior under an authorized scope to test defenses. The blue team builds, monitors, detects, investigates, and responds. Purple teaming is structured collaboration to improve detection and response based on observed attack techniques. The aim is improved security outcomes, not simply whether one team beats the other.

4. **What is a SIEM, and how would you investigate an alert?**  
   A SIEM centralizes and correlates security events from sources such as identity systems, servers, cloud services, and firewalls. I would verify the alert context and timestamps, check user and device activity, compare related events, assess impact, document evidence, and escalate according to the incident process. A SIEM alert is evidence to investigate, not automatic proof.

5. **What is MITRE ATT&CK?**  
   MITRE ATT&CK is a knowledge base of real-world adversary behaviors. Tactics describe attacker objectives and techniques describe ways to accomplish them. Security teams use it to organize detections, analyze incidents, and identify defensive gaps. For example, phishing can fall under initial access. The framework is descriptive and evolving, not a product that detects threats by itself.

6. **What are the phases of a penetration test?**  
   I would start with written authorization, scope, and rules of engagement. Then I would perform permitted reconnaissance, service discovery, vulnerability analysis, and controlled validation of exploitability. Any post-exploitation work must stay within scope. The engagement ends with prioritized reporting, clear remediation, and retesting. Evidence preservation and client communication are important throughout.

7. **Vulnerability assessment vs penetration testing?**  
   A vulnerability assessment identifies and prioritizes potential weaknesses, often with scanners and configuration reviews. A penetration test goes further by safely validating whether selected weaknesses can be exploited and what impact they create, within an agreed scope. Both have blind spots and should feed remediation and retesting rather than be treated as one-time certificates of security.

8. **How would you prioritize two vulnerabilities for a client?**  
   I would compare exposure, exploitability, possible impact on confidentiality, integrity, and availability, affected asset value, threat activity, and existing controls. Technical severity such as CVSS is useful but not the whole picture. I would document assumptions, prioritize actions that reduce risk fastest, and confirm severity with the client when business context is missing.

---

# Last-night revision

**Answer formula:** Definition -> mechanism -> example -> security impact/control. Name protocol or OWASP versions when relevant. If severity depends on the environment, explain the assumption.

| Topic | Rapid recall |
|---|---|
| **OS** | Process = separate virtual address space; threads share process memory. Deadlock: mutual exclusion, hold/wait, no preemption, circular wait. Paging = fixed pages; virtual memory maps addresses. Linux: `r=4, w=2, x=1`; `750 = rwx/r-x/---`. Know `ps`, `ss`, `chmod`, `journalctl`. |
| **DBMS + SQL** | PK identifies a row; FK links tables. 1NF atomic, 2NF no partial-key dependency, 3NF no transitive dependency. ACID: atomicity, consistency, isolation, durability. `WHERE` rows; `HAVING` groups; `LEFT JOIN` retains left rows. Indexes improve suitable lookups but cost storage/writes. |
| **OOP** | Encapsulation controls state; abstraction hides implementation; inheritance is-a; polymorphism shares an interface. Overloading differs by parameters; overriding replaces behavior. Prefer composition for has-a relationships. |
| **Networks** | OSI 7 layers; TCP/IP roughly 4. TCP handshake: **SYN -> SYN-ACK -> ACK**. TCP reliable byte stream; UDP datagrams. DNS resolves names; DHCP configures; ARP resolves IPv4 next-hop MAC; NAT translates addresses. `/26`: 64 addresses, normally 62 usable. |
| **Key ports** | `22` SSH, `53` DNS, `80` HTTP, `443` HTTPS, `25` SMTP, `445` SMB, `3389` RDP, `3306` MySQL, `5432` PostgreSQL. Ports are conventions, not security guarantees. |
| **Crypto** | Encryption reversible with key; hashing not reversible. Passwords: Argon2id plus per-password salt. Digital signature: sign private, verify public. Certificates bind identity/public key. TLS 1.3 authenticates server, negotiates shared secrets, then uses symmetric traffic encryption. |
| **OWASP 2025** | **A01** Access Control; **A02** Misconfiguration; **A03** Supply Chain; **A04** Crypto; **A05** Injection; **A06** Insecure Design; **A07** Authentication; **A08** Integrity; **A09** Logging/Alerting; **A10** Exceptional Conditions. |
| **Web flaws** | SQLi -> parameters. XSS -> contextual output encoding/safe DOM. CSRF -> anti-CSRF tokens/SameSite. IDOR -> server-side object permissions. JWT signed is not encrypted; verify claims/signature. Sessions -> rotate, expire, invalidate; `Secure`, `HttpOnly`, `SameSite`. CORS is browser response-sharing, not authorization. CSP/HSTS are defense in depth. |
| **Security work** | CIA = confidentiality, integrity, availability. Threat = source of harm; vulnerability = weakness; risk = context-specific likelihood and impact. Red emulates; blue defends; purple collaborates. SIEM correlates logs; ATT&CK describes adversary tactics/techniques. Pentest requires authorization, recon, discovery, validation, report, retest. |

**Five ready-made interview scenarios:**

1. **Another user's invoice is readable:** “IDOR. Check ownership or tenant authorization on the server for every object.”
2. **Username inserted directly into SQL:** “SQL injection risk. Use bound parameters and DB least privilege.”
3. **Suspicious privileged login:** “Correlate identity, network, and audit evidence; assess impact and escalate.”
4. **HTTPS site leaks customer records:** “TLS protects transport, not broken application authorization.”
5. **High-severity vulnerability:** “Prioritize using exploitability, exposure, business impact, and compensating controls.”

**Reference standards:** [OWASP Top 10:2025](https://top10.owasp.org/) | [OWASP SQLi](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html) | [OWASP XSS](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) | [OWASP Passwords](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html) | [MDN CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS) | [MITRE ATT&CK](https://attack.mitre.org/matrices/enterprise/)
