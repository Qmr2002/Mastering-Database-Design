### **1. Entity-Relationship (ER) Diagrams**
#### **Definition**
ER diagrams are visual representations of database entities, their attributes, and relationships. They help you conceptualize how data is structured before implementing it.

#### **Key Components**
1. **Entities**:
   - Represent real-world objects or concepts.
   - Examples: *Users*, *Properties*, *Bookings*, *Payments*.
2. **Attributes**:
   - Describe properties of an entity.
   - Examples for *Users*: *UserID*, *Name*, *Email*.
3. **Relationships**:
   - Define associations between entities.
   - Examples: A *User* makes a *Booking*, a *Booking* is linked to a *Property*.

#### **How to Create an ER Diagram**
1. **Identify Entities**:
   - List key objects: Users, Properties, Bookings, Payments.
2. **Define Attributes**:
   - Specify details for each entity:
     - *Users*: UserID, Name, Email.
     - *Properties*: PropertyID, Title, Location.
3. **Establish Relationships**:
   - Define connections:
     - A *User* can make many *Bookings*.
     - A *Booking* is associated with one *Property*.
4. **Visualize**:
   - Use tools like **Draw.io**, **Lucidchart**, or **ERDPlus**.

#### **Example ER Diagram**
- **Entities**:
  - Users (UserID, Name, Email).
  - Properties (PropertyID, Title, Location).
  - Bookings (BookingID, StartDate, EndDate).
  - Payments (PaymentID, Amount, Date).
- **Relationships**:
  - Users ↔ Bookings: A User can make multiple Bookings.
  - Bookings ↔ Properties: A Booking is for one Property.
  - Bookings ↔ Payments: A Booking generates one Payment.

---

### **2. Normalization**
#### **Definition**
Normalization organizes data to minimize redundancy and improve integrity. It breaks data into smaller, related tables and defines relationships.

#### **Normalization Forms**
1. **First Normal Form (1NF)**:
   - Ensure all columns contain atomic (indivisible) values.
   - Example: Split a column with concatenated data like `FullName` into `FirstName` and `LastName`.
2. **Second Normal Form (2NF)**:
   - Achieve 1NF.
   - Ensure all non-primary-key attributes are fully dependent on the primary key.
   - Example: Split a table storing *Property* and *Host* details into separate tables:
     - **Properties Table**: PropertyID, Title, Location, HostID.
     - **Hosts Table**: HostID, HostName, Contact.
3. **Third Normal Form (3NF)**:
   - Achieve 2NF.
   - Remove transitive dependencies (non-key attributes depending on other non-key attributes).
   - Example:
     - Instead of storing `City` and `Country` in a Property table, create a separate table for locations.

#### **How to Normalize Data**
1. **Identify Functional Dependencies**:
   - Determine which attributes depend on the primary key.
2. **Decompose Tables**:
   - Break large tables into smaller ones to eliminate redundancy.
3. **Define Relationships**:
   - Use foreign keys to maintain relationships between decomposed tables.

#### **Benefits**:
- Reduces data duplication.
- Ensures consistency.
- Prevents anomalies during insertions, updates, or deletions.

---

### **3. Schema Design**
#### **Definition**
Schema design is the process of defining the structure of a database, including tables, columns, keys, and constraints.

#### **Components**
1. **Tables**:
   - Define entities and their structure.
   - Example: *Users*, *Properties*, *Bookings*, *Payments*.
2. **Columns**:
   - Specify attributes and their data types.
   - Example: `UserID (INT)`, `Email (VARCHAR)`, `Amount (DECIMAL)`.
3. **Primary Keys**:
   - Uniquely identify rows.
   - Example: `UserID` in the *Users* table.
4. **Foreign Keys**:
   - Maintain relationships between tables.
   - Example: `HostID` in the *Properties* table refers to the `HostID` in the *Hosts* table.
5. **Constraints**:
   - Ensure data integrity:
     - **NOT NULL**: Prevent empty values.
     - **UNIQUE**: Ensure column values are distinct.
     - **CHECK**: Enforce rules (e.g., positive amounts for payments).

#### **How to Design a Schema**
1. **Define Requirements**:
   - Understand the data and relationships needed.
2. **Create Tables**:
   - Define tables for each entity.
3. **Set Data Types**:
   - Choose appropriate types:
     - Dates: `DATE`.
     - Amounts: `DECIMAL`.
     - IDs: `INTEGER`.
4. **Establish Keys**:
   - Assign primary keys and foreign keys to enforce relationships.
5. **Apply Constraints**:
   - Add rules to maintain integrity.

#### **Example Schema**
**Users Table**:
```sql
CREATE TABLE Users (
    UserID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    Email VARCHAR(50) UNIQUE NOT NULL
);
```

**Properties Table**:
```sql
CREATE TABLE Properties (
    PropertyID INT PRIMARY KEY,
    Title VARCHAR(100),
    Location VARCHAR(50),
    HostID INT,
    FOREIGN KEY (HostID) REFERENCES Hosts(HostID)
);
```

**Bookings Table**:
```sql
CREATE TABLE Bookings (
    BookingID INT PRIMARY KEY,
    UserID INT,
    PropertyID INT,
    StartDate DATE,
    EndDate DATE,
    FOREIGN KEY (UserID) REFERENCES Users(UserID),
    FOREIGN KEY (PropertyID) REFERENCES Properties(PropertyID)
);
```

---

### **🧑‍💻 Practical Steps**
1. **Create ER Diagrams**:
   - Use tools like **Lucidchart**, **Draw.io**, or **ERDPlus**.
   - Identify entities and relationships.
2. **Normalize Your Data**:
   - Apply 1NF, 2NF, and 3NF to remove redundancy.
   - Decompose larger tables into smaller ones.
3. **Design the Schema**:
   - Define tables, columns, data types, and constraints.
4. **Review and Refine**:
   - Validate your schema design with stakeholders or peers.
   - Optimize for performance (e.g., indexing).

---
