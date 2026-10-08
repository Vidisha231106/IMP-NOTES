# DBMS — Unit 1 & Unit 2 Study Notes (RVCE ISE)

Built from the four Dr. Padmashree T slide decks (Elmasri & Navathe content), scoped to your syllabus.
**Legend:** 🔹 = core concept · ⚠️ = exam trap / slide quirk · 💡 = intuition / extra insight · 📝 = likely exam question

**Scope note:** The Relational Algebra deck also covers set operations, JOINs, DIVISION, aggregates, outer joins, etc. Your syllabus stops at **SELECT and PROJECT**, so those are covered in depth and the rest is skipped (RENAME gets a short section because the slides use it inside SELECT/PROJECT expressions).

---

# PART A — UNIT 1

## 1. Introduction to Database Systems

### 1.1 Basic definitions 🔹

| Term | Meaning |
|---|---|
| **Data** | Known facts that can be recorded and have an implicit meaning |
| **Database** | A collection of **related** data (not random data: it has a purpose, a source, and an audience) |
| **Mini-world / Universe of Discourse** | The part of the real world the database models (e.g., student grades & transcripts at a university) |
| **DBMS** | Software package to **create and maintain** a computerized database |
| **Database system** | DBMS software + the data itself (sometimes + the applications) |

Implicit properties of a database: represents some aspect of the real world; is logically coherent; is designed/built/populated for a specific purpose with an intended group of users.

### 1.2 Problems with the traditional (file-processing) approach 🔹
Each application kept its own files:
- **Redundancy**: same data stored in multiple files, wasting space.
- **Inconsistency**: copies disagree after an update to only one of them.
- **Update problem**: one logical change must be made in many files/programs.
- **Multiple files**: related data scattered, hard to combine.
- **Loss of flexibility**: data format is hard-wired into programs, so changing the structure means rewriting programs.

💡 The DB approach solves these with one centrally defined, shared data store + a catalog describing it.

### 1.3 Types of database applications
- **Traditional:** numeric and textual databases.
- **More recent:** multimedia databases, GIS (Geographic Information Systems), data warehouses, real-time and active databases, and many others.

### 1.4 Typical DBMS functionality 🔹
1. **Define**: specify data types, structures, constraints of the database (stored as meta-data).
2. **Construct / Load**: store the initial data on secondary storage.
3. **Manipulate**: *retrieval* (queries, reports), *modification* (insert/delete/update), web access.
4. **Process & share**: many concurrent users/programs, yet data stays **valid and consistent**.
5. Other features: **protection/security** (prevent unauthorized access), presentation/visualization, and **maintenance** over the application's lifetime (database, software, system maintenance).

### 1.5 Simplified database system environment
```
Users / Programmers
        │
Application Programs / Queries
        │            ┌───────────── DBMS SOFTWARE ──────────────┐
        └──────────► │ Software to process queries/programs     │
                     │ Software to access stored data           │
                     └───────┬──────────────────────┬───────────┘
                             ▼                      ▼
              Stored database definition      Stored database
                    (META-DATA)                 (actual data)
```
The **Database System** = everything inside the outer box.

### 1.6 Example database: UNIVERSITY
- **Entities:** STUDENT, COURSE, SECTION (of courses), DEPARTMENT, INSTRUCTOR.
- **Relationships:** sections are *of* courses · students *belong to* sections · courses *have prerequisite* courses · instructors *teach* sections · courses are *offered by* departments · students *major in* departments.

Its schema (Fig 2.1) has 5 record types:
```
STUDENT(Name, Student_number, Class, Major)
COURSE(Course_name, Course_number, Credit_hours, Department)
PREREQUISITE(Course_number, Prerequisite_number)
SECTION(Section_identifier, Course_number, Semester, Year, Instructor)
GRADE_REPORT(Student_number, Section_identifier, Grade)
```

---

## 2. Characteristics of the Database Approach 🔹

### 2.1 Self-describing nature
The DBMS **catalog** stores the description of the database (structures, types, constraints), called **meta-data**.
💡 Because the DBMS reads the catalog instead of having structure hard-coded, *the same DBMS software works for a University DB, Banking DB, or Company DB*. A file-processing program, in contrast, has the structure hard-coded into it.

### 2.2 Insulation between programs and data (program-data independence)
You can change data structures/storage organization **without changing the programs** that access the data.

### 2.3 Data abstraction (and program-operation independence)
- A **data model** hides storage details and gives users a **conceptual view**.
- **Program-operation independence:** programs invoke operations by **name and arguments**, regardless of how the operations are implemented.
- Programs refer to data-model constructs (entities, attributes, relationships), not to storage details.

### 2.4 Support for multiple views
Each user/user group may see a different **view**: a subset, or virtual data derived from stored data, containing **only the data of interest to that user**.

### 2.5 Sharing of data & multiuser transaction processing
A DBMS must enforce the following for **transactions** (a transaction = a logical unit of DB work, e.g., a funds transfer):

| Property | Meaning |
|---|---|
| **Isolation** | Each transaction *appears* to execute in isolation from the others |
| **Atomicity** | Either **all** operations of the transaction execute or **none** do |

- **Concurrency-control** subsystem ⇒ guarantees each transaction is correctly executed or aborted.
- **Recovery** subsystem ⇒ guarantees the effect of every *completed* transaction is permanently recorded.
- **OLTP** (Online Transaction Processing): hundreds of concurrent transactions per second (airline reservations, banking).

💡 Isolation + Atomicity + Consistency + Durability = **ACID** (slides explicitly name only the first two; recovery ≈ durability).

---

## 3. Database Users 🔹

Two big groups: **Actors on the Scene** (use/control/design DB and applications) and **Workers Behind the Scene** (build the DBMS itself).

### 3.1 Actors on the scene
| Role | Responsibility |
|---|---|
| **Database Administrator (DBA)** | Authorizes access, coordinates & monitors use, acquires software/hardware, monitors efficiency |
| **Database Designer** | Defines content, structure, constraints, functions/transactions. Must talk to end users and understand their needs |
| **System analysts & application programmers (software engineers)** | Analysts determine end-user requirements and specify canned transactions; programmers implement them. Must know the full range of DBMS capabilities |
| **End users** | Use data for queries, reports, and some updates (see below) |

**Categories of end users**
- **Casual:** access the DB occasionally, with different needs each time (e.g., middle/high-level managers).
- **Naïve / Parametric:** a large share of end users; use pre-defined **"canned transactions"** (e.g., bank tellers, reservation clerks) for a whole shift.
- **Sophisticated:** business analysts, scientists, engineers; thoroughly familiar with system capabilities, often write their own queries/use software tools.
- **Stand-alone:** maintain personal databases using packaged applications (tax software, an address book).

### 3.2 Workers behind the scene
- **DBMS system designers & implementers:** design/implement DBMS modules and interfaces as a software package.
- **Tool developers:** tools for database modeling, design, performance.
- **Operators & maintenance personnel:** run and maintain the hardware/software environment.

---

## 4. Data Models, Schemas and Instances

### 4.1 Data abstraction & data model 🔹
- **Data abstraction:** suppressing storage/organization details and highlighting essential features.
- **Data model:** a collection of concepts to describe the **structure** of a DB, the **operations** to manipulate it, and the **constraints** it must obey.
  - *Structure:* constructs such as elements (with data types), groups of elements (entity / record / table), relationships among groups.
  - *Constraints:* restrictions on valid data; **must be enforced at all times**.
  - *Operations:* specify retrievals & updates in terms of model constructs. **Basic** (generic insert/delete/update) and **user-defined** (`compute_student_gpa`, `update_inventory`).

### 4.2 Categories of data models 🔹
| Category | Level | Idea | Example |
|---|---|---|---|
| **Conceptual** (high-level, semantic; *entity-based / object-based*) | Highest | Close to how users perceive data | **ER model** |
| **Implementation** (representational) | Middle | Between the two; used by commercial DBMSs | **Relational model** |
| **Physical** (low-level, internal) | Lowest | How data is physically stored (record formats, orderings, access paths) | Index structures, file organizations |

💡 *Pipeline:* ER (conceptual) → mapped to relational (implementation) → physical design (indexes, file structures).

### 4.3 Schema vs. state (instance) 🔹

| | Database **Schema** | Database **State / Instance** |
|---|---|---|
| What | The **description** of the DB: structure, data types, constraints | The **actual data** at a particular moment (snapshot / occurrence) |
| Also called | **Intension** | **Extension** |
| Changes | **Very infrequently** | Every time the DB is updated |

- **Schema diagram:** illustrative display of (most aspects of) a schema. **Schema construct:** a component, e.g., STUDENT, COURSE.
- **Initial state:** when the DB is first loaded. **Valid state:** satisfies the structure and all constraints.
- The word *instance* also applies to components: *record instance, table instance, entity instance*.
- The DBMS stores the schema in the catalog and is responsible for guaranteeing that every state is **valid**.

⚠️ Schema = "type/blueprint"; state = "data now". An empty DB has a schema but an empty state.

---

## 5. Three-Schema Architecture & Data Independence 🔹

**Goal:** separate user applications from the physical database; supports **program-data independence** and **multiple views**. (Not used explicitly in commercial DBMSs, but very useful for explaining DBMS organization.)

```
 End users        End users
    │                │
 External view  …  External view        ◄── EXTERNAL LEVEL (view level)
        \            /
   External/Conceptual mapping
              │
       Conceptual Schema               ◄── CONCEPTUAL LEVEL
              │
   Conceptual/Internal mapping
              │
       Internal Schema                 ◄── INTERNAL LEVEL
              │
        Stored Database
```

| Level | Describes | Data model used |
|---|---|---|
| **Internal schema** | Physical storage structures, **access paths (e.g., indexes)** | **Physical** model |
| **Conceptual schema** | Structure & constraints of the **whole DB for a community of users** (entities, data types, relationships, operations, constraints); hides physical details | Conceptual **or implementation** model |
| **External schemas** (view level) | Various user views, each showing only the relevant part | Usually same model as conceptual |

**Mappings:** the DBMS transforms a request on an external schema → conceptual schema → internal schema, and transforms results back. *Data is physically stored only at the internal level;* the other levels are descriptions.

### Data independence 🔹
| Type | Capacity to… | Example |
|---|---|---|
| **Logical data independence** | Change the **conceptual schema** without changing **external schemas / application programs** | Add a new attribute or record type, or drop one that views don't use |
| **Physical data independence** | Change the **internal schema** without changing the **conceptual schema** | Reorganize files, add new indexes for performance |

**Key idea:** when a lower-level schema changes, only the **mappings** between it and higher levels change (in a DBMS that fully supports data independence); the higher-level schemas stay **unchanged**, so applications (which refer to external schemas) need no change.

⚠️ Logical independence is **harder to achieve** than physical independence (it needs views to absorb structural changes).

---

## 6. DBMS Languages & Interfaces

### 6.1 Languages
| Language | Used by / for |
|---|---|
| **DDL** (Data Definition Language) | DBA & designers to specify the **conceptual schema**; in many DBMSs also used for internal & external schemas (views) |
| **SDL** (Storage Definition Language) | In some DBMSs, defines the **internal** schema (often realized as DBMS commands to DBA/designers) |
| **VDL** (View Definition Language) | In some DBMSs, defines **external** schemas/views |
| **DML** (Data Manipulation Language) | Specify **retrievals and updates** |

**DML types 🔹**
| | High-level / Non-procedural / **Declarative** | Low-level / Procedural |
|---|---|---|
| Example | **SQL** | Record-at-a-time navigational languages |
| Orientation | **Set-oriented**: says *what* to retrieve, not *how*; many records per statement | Retrieves **one record at a time**; needs looping and positioning pointers |
| Usage | Stand-alone (**query language**) or embedded | **Must** be embedded in a host language |

DML commands may be **embedded** in a **host language** (COBOL, C, C++, Java), or a **library of functions** may be provided to access the DBMS; alternatively used stand-alone as a **query language**.

### 6.2 SQL sublanguage taxonomy (Slide 34)
- **DDL:** CREATE, ALTER, DROP, RENAME, COMMENT, TRUNCATE
- **DML:** SELECT, INSERT, UPDATE, DELETE, MERGE, CALL, EXPLAIN PLAN, LOCK TABLE
- **DCL** (Data Control): **GRANT, REVOKE**
- **TCL** (Transaction Control): **COMMIT, ROLLBACK, SAVEPOINT**

⚠️ **Slide quirk:** the slide's diagram has DCL and TCL contents **swapped** (it puts COMMIT/ROLLBACK under "Data Control" and GRANT/REVOKE under "Transaction Control"). The standard answer for exams is: **DCL = GRANT/REVOKE; TCL = COMMIT/ROLLBACK/SAVEPOINT.**

### 6.3 Interfaces (from the Unit 1 syllabus line "Languages and Interfaces")
Menu-based / web-client interfaces, forms-based, graphical (point-and-click) interfaces, natural-language, keyword search, interfaces for parametric users (function keys for canned transactions), and DBA interfaces (privileged commands).

---

## 7. The Database System Environment

### 7.1 Component modules of a DBMS (Fig 2.3) 🔹
```
DBA staff ──► DDL statements ──► DDL compiler ───────────────► System catalog / Data dictionary
          └─► Privileged commands ───────────────────────────┐        ▲   ▲
Casual users ─► Interactive query ─► Query compiler ─► Query optimizer ┘   │
Application programmers ─► Application programs ─► Precompiler ─► Host language compiler
                                                         └─► DML compiler ─► Compiled transactions ◄─ Parametric users
                                  ▼ (DBA commands, queries, transactions)
                   Runtime database processor ◄──► Stored data manager ◄──► STORED DATABASE
                   (+ Concurrency control / Backup / Recovery subsystems)
```
- **DDL compiler:** processes schema definitions and stores meta-data in the **catalog**.
- **Query compiler → optimizer:** parses/validates the query, then rearranges operations / picks efficient execution strategy using catalog info.
- **Precompiler** extracts DML commands from the host-language program → **DML compiler** → compiled transactions.
- **Runtime database processor:** executes privileged commands, executable query plans and canned transactions with runtime parameters.
- **Stored data manager:** controls access to data on disk (uses basic OS services for disk I/O).
- **Concurrency control / backup / recovery subsystems:** correctness and fault tolerance.

### 7.2 Other environment pieces (textbook)
DBMS utilities (loading, backup, file reorganization, performance monitoring), **data dictionary / catalog**, and **communications** (network/web access). Architectures: centralized, client/server (2-tier / 3-tier).

---

# PART B — ER MODEL (Unit 1, second half)

## 8. Database Design Process & High-Level Conceptual Models

Two main activities: **database design** (conceptual schema — this chapter) and **application design** (programs/interfaces that access the DB, generally software engineering).

```
Mini-world
   │
Requirements collection & analysis
   ├── Functional requirements ─► Functional analysis ─► High-level transaction specification ─► Application program design ─► Transaction implementation
   └── Data requirements ─► CONCEPTUAL DESIGN ─► Conceptual schema (high-level data model)
                                │  ── DBMS-independent ──
                                ▼  ── DBMS-specific ────
                          LOGICAL DESIGN (data model mapping) ─► Logical schema
                                ▼
                          PHYSICAL DESIGN ─► Internal schema
```
🔹 **Conceptual design is DBMS-independent**; logical design (ER → relational) and physical design are DBMS-specific.

---

## 9. ER Model Concepts: Entities & Attributes 🔹

- **Entity:** a specific object/thing in the mini-world (EMPLOYEE *John Smith*, the *Research* DEPARTMENT, the *ProductX* PROJECT).
- **Attribute:** a property describing an entity (Name, SSN, Address, Gender, BirthDate).
- A specific entity has a **value** for each attribute: `Name='John Smith', SSN='123456789', Address='731, Fondren, Houston, TX', Gender='M', BirthDate='09-JAN-55'`.
- Each attribute has a **value set** (domain / data type): integer, string, subrange, enumerated…

### 9.1 Types of attributes 🔹
| Type | Meaning | Example | Notation |
|---|---|---|---|
| **Simple (atomic)** | Single indivisible value | SSN, Gender | Oval |
| **Composite** | Made of components; can form a **hierarchy** | `Address(Apt#, House#, Street, City, State, Zip, Country)`; `Name(First, Middle, Last)` | Oval with child ovals |
| **Multi-valued** | Entity may have **several values** | `{Color}` of a CAR, `{PreviousDegrees}` | **Double oval**; written `{ }` |
| **Complex** | **Nested** composite + multi-valued | `{Address_phone({Phone}, Address(House_number, Street, City, State, Zip_code))}` | — |
| **Stored vs. Derived** | Derived value is computed from a stored attribute | **Age** derived from **BirthDate** | **Dashed/dotted oval** |

- Composite attributes can be nested to any depth (rare). Example: `{PreviousDegrees(College, Year, Degree, Field)}` is composite **and** multivalued.
- **Why composite?** Lets you refer to the whole (Address) or a part (City).

### 9.2 NULL values 🔹
A NULL can mean:
1. **Not applicable** (a college-degree attribute for a person with none),
2. **Unknown** → *exists but missing* (height not recorded) or *unknown whether it exists* (home phone).

---

## 10. Entity Types, Entity Sets, Keys, Value Sets 🔹

- **Entity type:** a collection of entities with the **same basic attributes** (EMPLOYEE, PROJECT). It's the **schema / intension** (name + attributes + constraints).
- **Entity set:** the collection of all entities of a type **currently in the DB** = **extension** = the *state*. Same name (CAR) is often used for both.
- **Key attribute:** attribute whose value is **unique for each entity** (SSN of EMPLOYEE). **Underlined** in the diagram.
  - A key can be **composite**: `VehicleTagNumber(Number, State)` for CAR.
  - An entity type can have **more than one key**: CAR has `VehicleIdentificationNumber (VIN)` and `VehicleTagNumber (Number, State)`.
  - Uniqueness must hold for **every possible** entity set, not just the current one (key is a constraint from the mini-world).
- **Value set (domain):** set of allowed values of a simple attribute. Example: `Age` of EMPLOYEE ∈ integers 16–70.
- Formally, attribute A of entity type E with value set V is a function `A: E → P(V)` (powerset), for single-valued the result is one value, for multi-valued a set (slide-level: not required).

**CAR example (Fig 3.7):** CAR has `Registration(Number, State)` and `Vehicle_id` as keys, plus `Make, Model, Year, {Color}`. A CAR entity looks like `((ABC 123, TEXAS), TK629, Ford Mustang, convertible, 2004, {red, black})`.

---

## 11. Notation for ER diagrams (summary table) 🔹

| Symbol | Meaning |
|---|---|
| Rectangle | Entity type |
| **Double** rectangle | Weak entity type |
| Diamond | Relationship type |
| **Double** diamond | Identifying relationship |
| Oval | Attribute |
| Oval with **underlined** name | Key attribute |
| **Double** oval | Multi-valued attribute |
| **Dashed** oval | Derived attribute |
| Oval connected to child ovals | Composite attribute |
| Dashed underline | Partial key (weak entity) |
| **Single line** to entity | Partial participation |
| **Double line** to entity | Total participation |
| `1`, `N`, `M` on edges | Cardinality ratio |
| `(min,max)` on edge | Structural constraint on participation |

---

## 12. The COMPANY sample database (from the textbook)

**Requirements (summary):**
- Company has **departments**; each has a name, unique number, one **manager** (with start date), and possibly several **locations**.
- A department **controls** several **projects**; each project has a unique name, unique number, single location.
- **Employee:** name, SSN, address, salary, sex, birth date. Works for **one** department but may work on **several projects** (not necessarily controlled by the same department); track **hours per week** per project. Each employee has a **direct supervisor**.
- Track **dependents** of employees (first name, sex, birth date, relationship) for insurance.

### 12.1 Initial design
Four entity types, with *relationship-like attributes* still present as plain attributes:
- `DEPARTMENT(Name, Number, {Locations}, Manager, Manager_start_date)`
- `PROJECT(Name, Number, Location, Controlling_department)`
- `EMPLOYEE(Name(Fname, Minit, Lname), Ssn, Sex, Address, Salary, Birth_date, Department, Supervisor, {Works_on(Project, Hours)})`
- `DEPENDENT(Employee, Dependent_name, Sex, Birth_date, Relationship)`

### 12.2 Refinement: attributes that really are relationships 🔹
If an attribute **refers to another entity type**, it should become a **relationship**:
| Attribute | → Relationship |
|---|---|
| `Manager` of DEPARTMENT | **MANAGES** |
| `Department` of EMPLOYEE | **WORKS_FOR** |
| `Works_on` of EMPLOYEE | **WORKS_ON** (with attribute Hours) |
| `Controlling_department` of PROJECT | **CONTROLS** |
| `Supervisor` of EMPLOYEE | **SUPERVISION** |
| `Employee` of DEPENDENT | **DEPENDENTS_OF** |

💡 *Rule of thumb:* an attribute whose value is "another entity" (not a plain value) is a hidden relationship.

---

## 13. Relationships & Relationship Types 🔹

- **Relationship:** associates **two or more distinct entities** with a specific meaning (John Smith *works on* ProductX; Franklin Wong *manages* Research).
- **Relationship type:** the **schema** — name + participating entity types + constraints. **Relationship set** = current set of relationship instances (the *state*). Instances `r1, r2, …` each associate specific entities (e.g., `r1` links `e1` and `d1`).
- **Degree** = number of participating entity types: **binary (2)**, **ternary (3)**, **n-ary**. All six COMPANY relationships are binary.
- **More than one** relationship type can exist between the same two entity types (MANAGES and WORKS_FOR both link EMPLOYEE–DEPARTMENT; different meaning, different instances).
- Diamond connected by straight lines to participants.

### 13.1 Roles & recursive relationships 🔹
- Each participating entity type plays a **role** in the relationship (names matter when the same type participates more than once).
- **Recursive (self-referencing) relationship:** the **same entity type** participates **more than once in distinct roles**. 
  - **SUPERVISION:** one EMPLOYEE in the **supervisor** role, another EMPLOYEE in the **supervisee (subordinate)** role. In the instance diagram, role 1 = supervisor, role 2 = subordinate.
  - Role names **must** be displayed in the ER diagram to distinguish the two participations.

### 13.2 Constraints on relationship types 🔹
**Structural constraints = Cardinality ratio + Participation constraint**

| Constraint | Specifies | Values | Question it answers |
|---|---|---|---|
| **Cardinality ratio** | **Maximum** participation | 1:1, 1:N, N:1, M:N | *How many?* |
| **Participation (existence dependency)** | **Minimum** participation | **Total** (≥1, mandatory; double line) / **Partial** (0, optional; single line) | *Compulsory or not?* |

Cardinality examples:
- **WORKS_FOR** Employee:Department = **N:1** (many employees → one department).
- **WORKS_ON** Employee:Project = **M:N**.
- **MANAGES** = **1:1**; **CONTROLS** Department:Project = **1:N**; **SUPERVISION** = **1:N**; **DEPENDENTS_OF** Employee:Dependent = **1:N**.

⚠️ **Slide quirk:** the Unit 2 text lists CONTROLS as "1:1", but the ER diagram *and* the relational mapping (PROJECT gets a `DNUM` foreign key) treat it as **1:N** (a department controls many projects; a project has one controlling dept). Use **1:N** in answers.

### 13.3 (min, max) notation 🔹
Specified on **each participation** of entity type E in relationship R: each `e ∈ E` participates in **at least min** and **at most max** relationship instances of R.
- Default (no constraint): `min=0, max=n`. Must satisfy `min ≤ max, min ≥ 0, max ≥ 1`.
- `min = 0` ⇒ **partial**; `min > 0` ⇒ **total**.
- **How to read:** take the `(min,max)` next to an entity type, **looking away from that entity type** across the relationship.

COMPANY (Fig 3.15) values:
| Relationship | Participation (min,max) |
|---|---|
| MANAGES | EMPLOYEE **(0,1)** (employee manages at most one dept), DEPARTMENT **(1,1)** (exactly one manager) |
| WORKS_FOR | EMPLOYEE **(1,1)**, DEPARTMENT **(4,N)** (this company requires ≥4 employees per dept) |
| CONTROLS | DEPARTMENT **(0,N)**, PROJECT **(1,1)** |
| WORKS_ON | EMPLOYEE **(1,N)**, PROJECT **(1,N)** |
| SUPERVISION | supervisor **(0,N)**, supervisee **(0,1)** |
| DEPENDENTS_OF | EMPLOYEE **(0,N)**, DEPENDENT **(1,1)** |

### 13.4 Attributes of relationship types 🔹
A relationship can have attributes, e.g., **Hours** (HoursPerWeek) of WORKS_ON; its value depends on the **(employee, project) combination**.
- **M:N:** relationship attributes are *determined by the combination* of entities, so they **must** stay as relationship attributes.
- **1:1:** can be moved to **either** participating entity type.
- **1:N:** can be moved to the entity type on the **N-side**.
- Where to place it is a **subjective schema-design decision**.

Example: `Start_date` of MANAGES (1:1) can move to DEPARTMENT (as `Manager_start_date`); `Hours` of M:N WORKS_ON cannot move.

### 13.5 Refined COMPANY relationship types (Unit 2 slide)
1. **MANAGES** — 1:1, EMPLOYEE (partial) ↔ DEPARTMENT (total in the final design).
2. **WORKS_FOR** — 1:N Department→Employee, both total.
3. **SUPERVISION** — 1:N, both partial.
4. **WORKS_ON** — M:N with attribute Hours, both total.
5. **CONTROLS** — department partial, project total (1:N, see quirk above).
6. **DEPENDENTS_OF** — 1:N, employee partial, dependent total.

---

## 14. Weak Entity Types 🔹

- A **weak entity type** has **no key attribute of its own**.
- A weak entity must participate in an **identifying relationship** with an **owner (identifying) entity type**.
- Entities are identified by **partial key of the weak type + the specific owner entity** they relate to.
- Example: **DEPENDENT** is weak. Partial key = dependent's first **Name**; owner = **EMPLOYEE**; identifying relationship = **DEPENDENTS_OF**. Two dependents can both be named "Alice" if they belong to different employees.
- **Notation:** weak entity (double rectangle) + identifying relationship (double diamond) + partial key (dashed/dotted underline). The weak entity's participation in the identifying relationship is **always total** (existence-dependent on owner).
- An owner can own several weak entity types; a weak entity can have multiple owners (textbook).

⚠️ *Strong vs weak:* Weak entity = existence-dependent. If the employee is deleted, their dependents disappear (cascading).

---

## 15. Naming Conventions & Design Issues (textbook — not in the slide decks)
- **Entity type names:** singular nouns (EMPLOYEE, not EMPLOYEES); **UPPERCASE**; attributes **Capitalized**; relationship names **UPPERCASE verbs/verb phrases**; role names lowercase.
- Choose names that read well left-to-right/top-to-bottom as a sentence: *EMPLOYEE WORKS_FOR DEPARTMENT*.
- **Design choices:** is a concept an **attribute**, an **entity type**, or a **relationship**? (Rule from §12.2: if it references another entity → relationship; if it has its own attributes / participates in other relationships → entity type.)
- **Binary vs n-ary:** a ternary relationship (SUPPLY between SUPPLIER, PART, PROJECT) can't generally be replaced by 3 binaries without losing information.
- **Avoid redundancy**: don't store the same fact as both an attribute and a relationship.

---

# PART C — UNIT 2

## 16. ER-to-Relational Mapping Algorithm 🔹📝

Seven steps (the ones in your slides). Memorize the **order** and the **key idea of each**.

### Step 1 — Regular (strong) entity types
- One relation per entity type, with **all simple attributes** (for a composite attribute, only its **simple components**).
- Pick **one key attribute as primary key**; if composite, all its simple components together form the PK.
- COMPANY: `EMPLOYEE(Fname, Minit, Lname, **Ssn**, Bdate, Address, Sex, Salary)`, `DEPARTMENT(Dname, **Dnumber**)`, `PROJECT(Pname, **Pnumber**, Plocation)`.
- Other keys → **secondary (unique) keys**.

### Step 2 — Weak entity types
- New relation `R` with all simple attributes of weak type `W`.
- Add **foreign key(s)** = primary key of the **owner** relation(s).
- **PK = owner's PK + partial key of W.**
- COMPANY: `DEPENDENT(**Essn**, **Dependent_name**, Sex, Bdate, Relationship)` — `Essn` FK → EMPLOYEE.Ssn.

### Step 3 — Binary **1:1** relationships (three approaches) 🔹
| Approach | How | When best |
|---|---|---|
| **Foreign key** | Pick one relation `S`, add PK of `T` as FK in `S`. Prefer `S` = side with **total participation** (avoids NULLs) | **Most common** |
| **Merged relation** | Merge both entity types + the relationship into **one relation** | When **both** participations are **total** (and no other relationships to the entities) |
| **Cross-reference (relationship) relation** | New relation with PKs of both | When both sides are **partial** (avoids many NULLs) |

- COMPANY MANAGES: use **DEPARTMENT** as `S` (total participation): add `Mgr_ssn` (FK → EMPLOYEE.Ssn) and the relationship attribute `Mgr_start_date` to DEPARTMENT.

### Step 4 — Binary **1:N** relationships
- Identify relation `S` at the **N-side**; add **FK in S** = PK of the **1-side** relation `T`.
- Add any **simple attributes** of the relationship to `S`.
- *Alternative:* cross-reference relation (like Step 3) with PK = key of `S`.
- COMPANY:
  - **WORKS_FOR** → `Dno` in EMPLOYEE (FK → DEPARTMENT.Dnumber).
  - **SUPERVISION** (recursive 1:N) → `Super_ssn` in EMPLOYEE (FK → EMPLOYEE.Ssn).
  - **CONTROLS** → `Dnum` in PROJECT (FK → DEPARTMENT.Dnumber).

💡 *1:N FK always goes on the "many" side.*

### Step 5 — Binary **M:N** relationships
- **New relation `S`**, FKs = PKs of both participating relations; **PK = combination of the FKs**.
- Add relationship's simple attributes.
- COMPANY: `WORKS_ON(**Essn**, **Pno**, Hours)`.

### Step 6 — Multivalued attributes
- New relation `R` with the attribute `A` + the **PK `K` of the owner as FK**; **PK of R = {A, K}** (all simple components if A is composite).
- COMPANY: `DEPT_LOCATIONS(**Dnumber**, **Dlocation**)`.

### Step 7 — N-ary (n > 2) relationships
- New relation `S` with FKs to **all** participating entity relations + relationship's simple attributes. PK = usually the **combination of all FKs**.
- Example: ternary **SUPPLY** → `SUPPLY(**Sname**, **Part_no**, **Proj_name**, Quantity)`.

### 16.1 Final COMPANY relational schema (result of mapping)
```
EMPLOYEE(Fname, Minit, Lname, SSN, Bdate, Address, Sex, Salary, Super_ssn→EMPLOYEE, Dno→DEPARTMENT)
DEPARTMENT(Dname, DNUMBER, Mgr_ssn→EMPLOYEE, Mgr_start_date)
DEPT_LOCATIONS(DNUMBER→DEPARTMENT, DLOCATION)
PROJECT(Pname, PNUMBER, Plocation, Dnum→DEPARTMENT)
WORKS_ON(ESSN→EMPLOYEE, PNO→PROJECT, Hours)
DEPENDENT(ESSN→EMPLOYEE, DEPENDENT_NAME, Sex, Bdate, Relationship)
```
(Underlined/CAPS = primary key; `→` = foreign key.) The derived attribute `Number_of_employees` is **not stored**.
`Dname` is a secondary (candidate) key of DEPARTMENT.

### 16.2 Summary: ER ↔ Relational correspondence 🔹
| ER model | Relational model |
|---|---|
| Entity type | "Entity" relation |
| 1:1 or 1:N relationship type | **Foreign key** (or "relationship" relation) |
| M:N relationship type | "Relationship" relation + **two** foreign keys |
| n-ary relationship type | "Relationship" relation + **n** foreign keys |
| Simple attribute | Attribute |
| Composite attribute | Set of simple component attributes |
| Multivalued attribute | Separate **relation + foreign key** |
| Value set | Domain |
| Key attribute | Primary (or secondary) key |

---

## 17. The Relational Model 🔹

Proposed by **E. F. Codd (IBM Research, 1970)**, "A Relational Model for Large Shared Data Banks" (CACM, June 1970); earned him the **ACM Turing Award**. Its strength = formal foundation in **set theory / relations**.

### 17.1 Informal vs. formal terms 🔹
| Informal | Formal |
|---|---|
| Table | **Relation** |
| Column header | **Attribute** |
| All possible column values | **Domain** |
| Row | **Tuple** |
| Table definition | **Schema of a relation** |
| Populated table | **State of the relation** |

### 17.2 Formal definitions 🔹
- **Relation schema** `R(A1, A2, …, An)`: name `R` + attributes; e.g., `CUSTOMER(Cust_id, Cust_name, Address, Phone#)`. The **degree** (arity) of R is `n`. Schema = **intension**.
- **Domain** `dom(Ai)`: set of atomic values allowed for attribute `Ai`. It has a **logical definition** (e.g., "USA_phone_numbers = valid 10-digit phone numbers") and a **data type/format** (`(ddd)ddd-dddd`; dates as `yyyy-mm-dd`).
  - The **attribute name** gives the **role** a domain plays: domain *Date* used by both `Invoice_date` and `Payment_date` with different meanings.
- **Tuple:** an ordered list of values `t = <v1, …, vn>`, each `vi ∈ dom(Ai)` (or NULL).
- **Relation state** `r(R)`: a **set of tuples** `{t1, …, tm}` and a **subset of the Cartesian product** of domains:

  `r(R) ⊆ dom(A1) × dom(A2) × … × dom(An)`

  Example: `dom(A1)={0,1}, dom(A2)={a,b,c}` → product = `{<0,a>,<0,b>,<0,c>,<1,a>,<1,b>,<1,c>}` (6 combos); one valid state is `{<0,a>,<0,b>,<1,c>}`.
- **Intension** = schema `R`; **Extension** = state `r`.
- **Notation:** `t[Ai]` or `t.Ai` = value of attribute Ai in tuple t; `t[Au, Av, …, Aw]` = sub-tuple.
  - Example: t = `<'Barbara Benson','533-69-1238','839-8461','7384 Fontana Lane', NULL, 19, 3.25>` → `t[Name]=<'Barbara Benson'>`, `t[Ssn, Gpa, Age]=<'533-69-1238', 3.25, 19>`.

### 17.3 Characteristics of relations 🔹
1. **Tuples are not ordered** (a relation is a *set*), even though a table displays rows in some order. Also **no duplicate tuples**.
2. **Attributes are ordered** within the schema (and values within a tuple), in the basic definition; an alternative definition treats tuples as sets of (attribute, value) pairs, making order irrelevant.
3. **Values are atomic** (indivisible) → composite/multivalued attributes aren't allowed directly (this is **First Normal Form**).
4. Each value must come from the **domain** of its attribute.
5. **NULL** is a special value for **unknown / inapplicable** (different NULLs are not considered equal to one another).
6. A relation represents an **assertion/fact** about the mini-world (each tuple = a fact about an entity or relationship instance).

---

## 18. Relational Model Constraints & Relational Database Schemas 🔹

Constraints = conditions that must hold on **all valid relation states**. Inherent (the model's own, e.g., no duplicate tuples), **explicit schema-based** (domain, key, entity integrity, referential integrity), and **application-based / semantic**.

### 18.1 Domain constraint
Every value in a tuple for attribute A must be from `dom(A)` (and atomic) — NULL allowed unless declared NOT NULL.

### 18.2 Key constraints 🔹
- **Superkey (SK):** set of attributes such that **no two tuples** in any valid state have the same SK value: `t1[SK] ≠ t2[SK]`.
- **Key (candidate key):** a **minimal superkey** — removing *any* attribute breaks uniqueness.
- Every key is a superkey; **not every superkey is a key**. Any superset of a key is a superkey.
- **Primary key (PK):** the candidate key *chosen* to identify tuples (underlined). Others = **secondary / unique** keys. General (not strict) guideline: pick the **smallest** candidate key.

**CAR(State, Reg#, SerialNo, Make, Model, Year)**
- Keys: `{State, Reg#}` and `{SerialNo}` — both are superkeys too.
- `{SerialNo, Make}` is a **superkey but not a key** (Make is redundant).
- Chosen PK: `SerialNo`.
- Total number of superkeys is large (every set containing a key): e.g., `{SerialNo, Year}`, `{SerialNo, Make, Model}`, …

⚠️ "Key" always refers to a property of the **schema** (must hold for all possible states), not of the current data. Don't call something a key just because values happen to be unique right now.

### 18.3 Relational database schema
`S = {R1, R2, …, Rn}` (+ integrity constraints). COMPANY has **6** relation schemas (EMPLOYEE, DEPARTMENT, DEPT_LOCATIONS, PROJECT, WORKS_ON, DEPENDENT). A **relational DB state** = the union of all relation states.
**Display convention:** relation name above the attribute row; PK underlined; foreign-key constraints drawn as **arrows from FK to referenced table** (ideally to its PK).

### 18.4 Entity integrity 🔹
**No primary-key attribute may be NULL** in any tuple (`t[PK] ≠ null`). If the PK is composite, *none* of its component attributes may be null. Reason: PK values identify tuples. (Other attributes may additionally be declared NOT NULL.)

### 18.5 Referential integrity (foreign key) 🔹
Involves **two relations**: the **referencing** relation R1 and the **referenced** relation R2.
- R1 has **foreign-key** attributes **FK** that reference the **PK** of R2. Tuple t1 **references** t2 if `t1[FK] = t2[PK]`.
- The FK value in each R1 tuple must be **either**
  1. an **existing PK value** in R2, **or**
  2. **NULL** (and in this case the FK must **not be part of R1's own primary key**).
- FK and PK must have the **same domain** (attribute names can differ, e.g., `Dno` ↔ `Dnumber`).
- A FK can refer to its **own relation** (e.g., `Super_ssn → EMPLOYEE.Ssn`).

COMPANY FKs: `EMPLOYEE.Dno→DEPARTMENT`, `EMPLOYEE.Super_ssn→EMPLOYEE`, `DEPARTMENT.Mgr_ssn→EMPLOYEE`, `DEPT_LOCATIONS.Dnumber→DEPARTMENT`, `PROJECT.Dnum→DEPARTMENT`, `WORKS_ON.Essn→EMPLOYEE`, `WORKS_ON.Pno→PROJECT`, `DEPENDENT.Essn→EMPLOYEE`.

**In-class exercise (Ex 5.15) — foreign keys**
```
STUDENT(SSN, Name, Major, Bdate)
COURSE(Course#, Cname, Dept)
ENROLL(SSN, Course#, Quarter, Grade)            FK: SSN→STUDENT, Course#→COURSE
BOOK_ADOPTION(Course#, Quarter, Book_ISBN)      FK: Course#→COURSE, Book_ISBN→TEXT
TEXT(Book_ISBN, Book_Title, Publisher, Author)
```

### 18.6 Other (semantic) constraints
**Semantic integrity constraints** depend on application meaning and can't be expressed by the model itself (e.g., "an employee may work at most **56 hours/week** over all projects"). Expressed via a constraint specification language, or in SQL via **triggers** and **ASSERTIONS** (SQL-99).

---

## 19. Update Operations & Dealing with Constraint Violations 🔹📝

Three basic operations: **INSERT**, **DELETE**, **MODIFY (UPDATE)**. Integrity constraints must **not** be violated; several updates may need grouping; updates may **propagate** automatically to maintain integrity.

### 19.1 What each operation can violate
| Operation | Possible violations |
|---|---|
| **INSERT** | **Domain** (value not in domain) · **Key** (duplicate key value) · **Entity integrity** (PK is NULL) · **Referential integrity** (FK refers to a non-existent PK) — **can violate all four** |
| **DELETE** | **Only referential integrity** (the deleted tuple's PK is referenced by other tuples) |
| **UPDATE / MODIFY** | **Domain** and **NOT NULL** on the modified attribute; plus depending on what is changed ↓ |

UPDATE depends on the attribute updated:
- **Primary key:** behaves like **DELETE + INSERT** → needs the same options as DELETE; can violate key, entity & referential integrity.
- **Foreign key:** may violate **referential integrity** (new value must exist in referenced PK, or be NULL).
- **Ordinary attribute** (neither PK nor FK): can only violate **domain** (or NOT NULL).

### 19.2 Options when a violation occurs 🔹
1. **Cancel** the operation (**RESTRICT / REJECT**).
2. Perform it but **inform the user** of the violation.
3. **Trigger additional updates** to correct it (**CASCADE**, **SET NULL**).
4. Execute a **user-specified error-correction routine**.

**For DELETE (and PK update)**, one option must be declared at design time for each FK:
| Option | Effect on referencing tuples |
|---|---|
| **RESTRICT** | Reject the deletion |
| **CASCADE** | Delete the referencing tuples too |
| **SET NULL** | Set their FK to NULL (not allowed if the FK is part of a PK; also not valid when FK is NOT NULL) |

**COMPANY examples**
- Insert `<'Cecilia','F','Kolonsky', NULL, '1960-04-05', '6357 Windy Lane…', F, 28000, '888665555', 4>` → violates **entity integrity** (SSN = NULL).
- Insert an EMPLOYEE with `Dno = 7` when no department 7 exists → **referential integrity**.
- Insert an EMPLOYEE whose SSN already exists → **key constraint**.
- Delete a WORKS_ON tuple → **fine** (nothing references it).
- Delete an EMPLOYEE who is referenced as `Mgr_ssn`, `Super_ssn`, `Essn` → **referential integrity**; resolve by RESTRICT (reject), CASCADE (e.g., delete their dependents), or SET NULL (e.g., set `Super_ssn` of subordinates to NULL).

💡 SQL syntax preview: `FOREIGN KEY (Dno) REFERENCES DEPARTMENT(Dnumber) ON DELETE SET NULL ON UPDATE CASCADE;`

---

# PART D — RELATIONAL ALGEBRA (within syllabus: SELECT & PROJECT)

## 20. Relational Algebra Overview 🔹

- **Relational algebra** = the basic set of operations of the relational model; lets a user specify retrieval requests (**queries**) **procedurally** (you specify the *sequence* of operations).
- **Closure property:** the result of every operation is itself a **relation**, so operations can be **chained**. A sequence of operations = a **relational algebra expression**, whose result is also a relation.
- Groups of operations (full list for context; only highlighted ones are in your syllabus):
  - **Unary:** **SELECT σ**, **PROJECT π**, RENAME ρ ← *you need these*
  - Set theory: UNION ∪, INTERSECTION ∩, DIFFERENCE −, CARTESIAN PRODUCT ×
  - Binary: JOIN ⋈, DIVISION ÷
  - Additional: outer joins, outer union, aggregate functions ℱ, recursive closure
- Fun fact (from the slides): "algebra" comes from *al-jabr* by al-Khwarizmi; "algorithm" also derives from his name.

### Sample data used below (COMPANY, from the textbook state)
```
EMPLOYEE(Fname, Lname, Ssn,        Salary, Sex, Dno)
          John   Smith   123456789  30000   M    5
          Franklin Wong  333445555  40000   M    5
          Alicia Zelaya  999887777  25000   F    4
          Jennifer Wallace 987654321 43000  F    4
          Ramesh Narayan 666884444  38000   M    5
          Joyce  English 453453453  25000   F    5
          Ahmad  Jabbar  987987987  25000   M    4
          James  Borg    888665555  55000   M    1
```

---

## 21. SELECT (σ) 🔹📝

**Purpose:** choose a **subset of tuples** (a *horizontal* partition) that satisfy a **selection condition**; the condition acts as a **filter**.

**Syntax:** `σ <selection condition> (R)`

**Condition:** built from clauses `<attribute> <op> <constant>` or `<attribute> <op> <attribute>`, with `op ∈ {=, <, ≤, >, ≥, ≠}`, combined with Boolean **AND (∧), OR (∨), NOT (¬)**. A tuple is kept if the condition evaluates to TRUE (FALSE or UNKNOWN tuples are dropped).

**Examples**
- `σ Dno = 4 (EMPLOYEE)` → Zelaya, Wallace, Jabbar (**3** tuples).
- `σ Salary > 30000 (EMPLOYEE)` → Wong, Wallace, Narayan, Borg (**4** tuples; Smith has exactly 30000, so **excluded**).
- `σ (Dno = 4 AND Salary > 25000) OR (Dno = 5 AND Salary > 30000) (EMPLOYEE)` → Wallace, Wong, Narayan.
- SQL equivalent: `SELECT * FROM EMPLOYEE WHERE Dno = 4;`

⚠️ **Name clash:** relational-algebra **SELECT (σ) = SQL's WHERE (row filter)**. SQL's `SELECT` clause (column list) is closer to **PROJECT (π)**.

### Properties of SELECT 🔹📝
1. Result has the **same schema (attributes)** as R (same degree).
2. **|σc(R)| ≤ |R|** (number of tuples ≤ original). Selectivity = fraction of tuples kept.
3. **Commutative:** `σc1(σc2(R)) = σc2(σc1(R))`.
4. Hence a **cascade** can be applied in **any order**: `σc1(σc2(σc3(R))) = σc2(σc3(σc1(R)))`.
5. A cascade can be replaced by a **single SELECT with AND**:

   `σc1(σc2(σc3(R))) = σ(c1 AND c2 AND c3)(R)`
6. SELECT works on **one relation only** (unary): the condition can't compare tuples from different relations.

---

## 22. PROJECT (π) 🔹📝

**Purpose:** keep **certain columns (attributes)** and discard the rest (a *vertical* partition).

**Syntax:** `π <attribute list> (R)`

**Examples**
- `π Lname, Fname, Salary (EMPLOYEE)` → one tuple per employee with only those 3 columns (8 tuples, since Lname is unique here).
- `π Salary (EMPLOYEE)` → `{30000, 40000, 25000, 43000, 38000, 55000}` → **6 tuples** (three employees earn 25000; duplicates are removed).
- `π Sex, Salary (EMPLOYEE)` → **7** tuples: Zelaya `(F,25000)` and English `(F,25000)` collapse into one.
- SQL equivalent: `SELECT DISTINCT Lname, Fname, Salary FROM EMPLOYEE;` ⚠️ note **DISTINCT** — plain SQL does **not** remove duplicates by default, but relational algebra always does (it works on **sets**).

### Properties of PROJECT 🔹📝
1. **Duplicate elimination:** the result must be a *set*, so duplicate tuples are removed.
2. **|π<list>(R)| ≤ |R|.**
3. If the attribute list **includes a key (or superkey) of R**, then **|π<list>(R)| = |R|** (no duplicates can arise).
4. Result's degree = number of attributes in the list, **in the listed order**.
5. **Not commutative** in general; but `π<list1>(π<list2>(R)) = π<list1>(R)` **provided `<list2>` contains all attributes of `<list1>`** (the inner projection is redundant).
   ⚠️ If `<list2>` doesn't contain `<list1>`, the expression is **undefined/invalid** (the outer projection asks for columns that no longer exist).

---

## 23. Combining operations: single expressions vs. sequences 🔹

**Query:** first name, last name, salary of employees in department 5.

**(a) Single (nested) expression** — evaluate inside-out:

`π Fname, Lname, Salary ( σ Dno=5 (EMPLOYEE) )`

**(b) Sequence with intermediate relations** (use `←` assignment):
```
DEP5_EMPS ← σ Dno=5 (EMPLOYEE)
RESULT    ← π Fname, Lname, Salary (DEP5_EMPS)
```
Result: Smith 30000, Wong 40000, Narayan 38000, English 25000.

Analogy: `X = (a+b)*c` vs `D = a+b; X = D*c`.

⚠️ **Order matters between σ and π:** you can't apply π first and then σ on a column you dropped. e.g., `σ Dno=5 (π Fname, Lname (EMPLOYEE))` is **invalid** because `Dno` isn't in the projected relation. Always **select first, project last** when the condition uses columns not in the output.

---

## 24. RENAME (ρ) (short; used with expressions) 🔹
Renames the relation, its attributes, or both. Needed for intermediate results (and later for joins/self-joins).
| Form | Effect |
|---|---|
| `ρ S(B1, B2, …, Bn) (R)` | Renames relation → **S** and attributes → B1…Bn |
| `ρ S (R)` | Renames the **relation only** |
| `ρ (B1, B2, …, Bn) (R)` | Renames **attributes only** |

**Shorthand:** `RESULT(F, M, L, S, B, A, SX, SAL, SU, DNO) ← DEP5_EMPS` renames all 10 attributes of DEP5_EMPS in one assignment. If you write `RESULT ← π Fname, Lname, Salary (DEP5_EMPS)`, RESULT keeps the **original attribute names**.

---

# PART E — QUICK REVISION & EXAM PREP

## 25. One-page cheat sheet
- **Database approach characteristics (5):** self-describing (catalog/meta-data) · program–data independence · data abstraction (program-operation independence) · multiple views · sharing + multiuser transactions (isolation, atomicity, concurrency control, recovery, OLTP).
- **Three levels:** External (views) → Conceptual (whole DB) → Internal (storage). **Logical independence** = change conceptual w/o touching external; **Physical independence** = change internal w/o touching conceptual. Only **mappings** change.
- **Schema (intension)** rarely changes; **state (extension)** changes constantly.
- **ER attribute types:** simple · composite · multivalued `{ }` · complex · derived (dashed) · stored. Keys underlined; partial key dashed underline.
- **Structural constraints** = cardinality ratio (max) + participation (min). Total = double line = min ≥ 1.
- **Weak entity:** no key; identified by owner's key + partial key; double rectangle/diamond; total participation.
- **ER→Relational:** 1 regular entity → relation · 2 weak entity → relation with owner's PK + partial key · 3 1:1 → FK on total side / merge / cross-ref · 4 1:N → FK on **N side** · 5 M:N → new relation, PK = both FKs · 6 multivalued → new relation (A + K) · 7 n-ary → new relation with n FKs.
- **Keys:** superkey ⊇ key (minimal superkey) → candidate keys → **one chosen PK**.
- **Integrity:** domain · key · **entity** (PK ≠ NULL) · **referential** (FK = existing PK or NULL) · semantic.
- **Violations:** INSERT → all four; DELETE → referential only; UPDATE → depends on attribute (PK ≈ delete+insert).
- **Delete options:** RESTRICT / CASCADE / SET NULL.
- **σ:** same schema, ≤ tuples, commutative, cascade = AND. **π:** removes duplicates, ≤ tuples, = |R| if key included, not commutative.

## 26. Likely exam questions 📝
1. Differentiate a file-processing system from a DBMS approach; list the main characteristics of the DB approach.
2. Explain the three-schema architecture with a diagram. Distinguish logical vs physical data independence with examples.
3. Differentiate: schema vs instance; DDL vs DML; procedural vs non-procedural DML; actors on the scene vs workers behind the scene.
4. Explain categories of end users with examples.
5. Explain the types of attributes in the ER model with notation. Differentiate entity type / entity set; stored vs derived attribute.
6. Explain structural constraints and the (min,max) notation; draw the COMPANY ER diagram with them.
7. What is a weak entity type? Explain with an example (partial key, identifying relationship, owner).
8. Explain the seven-step ER-to-relational algorithm, with COMPANY examples. Discuss the three approaches for 1:1.
9. Define relation, tuple, domain, attribute. List characteristics of relations.
10. Differentiate superkey, candidate key, primary key (CAR example). State entity and referential integrity.
11. For each of INSERT/DELETE/UPDATE, which constraints can be violated and how can it be handled? (RESTRICT/CASCADE/SET NULL)
12. Write relational-algebra expressions using σ and π: e.g., *names and salaries of employees in dept 5 earning > 30000* → `π Fname, Lname, Salary ( σ Dno=5 ∧ Salary>30000 (EMPLOYEE) )`. State the properties of SELECT and PROJECT.
13. Given a schema (like the BOOK_ADOPTION exercise), draw the relational schema diagram with foreign keys.

## 27. Slide discrepancies to remember (so you don't get tripped up)
1. **DCL/TCL swapped** in the "DBMS Languages" diagram → use GRANT/REVOKE = DCL; COMMIT/ROLLBACK = TCL.
2. **CONTROLS** described as 1:1 in text but modeled as **1:N** everywhere else.
3. The Unit 2 slide says **"Department participation [in MANAGES] is not clear from requirements"**; the final (min,max) diagram gives **(1,1)** (total), and the mapping step uses DEPARTMENT as the FK-holding side because it's total.
4. Slide: *"A relation is formed over the Cartesian product…"* — a relation **state** is a **subset** of the product, not the whole product.
5. In the SELECT/PROJECT slides, attribute names appear as `DNO`, `SUPERSSN`, `MGRSSN`; the ER diagrams use `Dno`, `Super_ssn`, `Mgr_ssn`. Same attributes, different capitalization.
