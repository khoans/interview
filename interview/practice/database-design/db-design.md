 # 50 Database Design Interview Questions

## Questions 1-10: Basic Entity Relationships

**1.** Let's say we are creating a database for a university. We capture details about students who attend classes. A student can be enrolled in multiple classes at a time. A class can have many students. Describe the tables to be created. (Original question 46)

**2.** Design a database for a library system. A book can have multiple authors. An author can write multiple books. A member can borrow multiple books. Describe the tables and relationships.

**3.** Design a database for an e-commerce store. A customer can place multiple orders. An order can contain multiple products. A product can be in multiple orders. Describe the tables to be created.

**4.** Design a database for a hospital. A doctor can treat multiple patients. A patient can be treated by multiple doctors. A patient can have multiple medical tests. Describe the tables.

**5.** Design a database for a movie theater. A movie can have multiple showtimes. A showtime can have multiple ticket bookings. A customer can book multiple tickets. Describe the tables.

**6.** Design a database for a gym membership system. A member can enroll in multiple classes. A trainer can conduct multiple classes. A class can have multiple members. Describe the tables.

**7.** Design a database for a restaurant. A menu item can belong to multiple categories. An order can have multiple menu items. A waiter can serve multiple tables. Describe the tables.

**8.** Design a database for a hotel booking system. A hotel has multiple rooms. A customer can make multiple bookings. A booking can include multiple rooms. Describe the tables.

**9.** Design a database for a car rental company. A customer can rent multiple cars. A car can be rented multiple times. A car can have multiple maintenance records. Describe the tables.

**10.** Design a database for a school. A teacher can teach multiple subjects. A student can take multiple subjects. A class has multiple students and one teacher. Describe the tables.

---

## Questions 11-20: One-to-Many Relationships

**11.** Design a database for a blog system. A user can write multiple posts. A post can have multiple comments. A comment belongs to one user. Describe the tables.

**12.** Design a database for a company's department structure. A department can have multiple employees. An employee belongs to one department. A department can have multiple projects. Describe the tables.

**13.** Design a database for a music streaming service. An artist can create multiple albums. An album can have multiple songs. A user can create multiple playlists. Describe the tables.

**14.** Design a database for a real estate agency. An agent can list multiple properties. A property can have multiple viewings. A buyer can view multiple properties. Describe the tables.

**15.** Design a database for a bank. A customer can have multiple accounts. An account can have multiple transactions. A branch can have multiple employees. Describe the tables.

**16.** Design a database for a flight booking system. An airport can have multiple flights. A flight can have multiple passengers. A passenger can book multiple flights. Describe the tables.

**17.** Design a database for a university research system. A professor can lead multiple research projects. A project can have multiple researchers. A researcher can work on multiple projects. Describe the tables.

**18.** Design a database for a warehouse inventory system. A warehouse can store multiple products. A product can be in multiple warehouses. A supplier can supply multiple products. Describe the tables.

**19.** Design a database for a social media platform. A user can post multiple photos. A photo can have multiple comments. A user can follow multiple users. Describe the tables.

**20.** Design a database for a conference management system. A conference can have multiple sessions. A session can have multiple speakers. An attendee can register for multiple sessions. Describe the tables.

---

## Questions 21-30: Many-to-Many Relationships

**21.** Design a database for a course enrollment system. A student can enroll in multiple courses. A course can have multiple students. A course can have multiple prerequisites. Describe the tables.

**22.** Design a database for a pet adoption center. A pet can have multiple vaccinations. A volunteer can care for multiple pets. An adopter can adopt multiple pets. Describe the tables.

**23.** Design a database for a film production company. An actor can act in multiple movies. A director can direct multiple movies. A movie can have multiple genres. Describe the tables.

**24.** Design a database for a supply chain system. A manufacturer can produce multiple products. A retailer can sell products from multiple manufacturers. A product can be shipped to multiple locations. Describe the tables.

**25.** Design a database for a tournament system. A team can have multiple players. A team can participate in multiple matches. A match can have multiple referees. Describe the tables.

**26.** Design a database for a subscription service. A user can subscribe to multiple magazines. A magazine can have multiple subscribers. An issue can contain multiple articles. Describe the tables.

**27.** Design a database for a skill management system. An employee can have multiple skills. A project can require multiple skills. A skill can be possessed by multiple employees. Describe the tables.

**28.** Design a database for a event management system. An event can have multiple sponsors. A sponsor can sponsor multiple events. An attendee can attend multiple events. Describe the tables.

**29.** Design a database for a recipe sharing platform. A recipe can have multiple ingredients. An ingredient can be used in multiple recipes. A user can save multiple recipes. Describe the tables.

**30.** Design a database for a equipment rental system. A customer can rent multiple equipment items. An equipment can be rented multiple times. An equipment can belong to multiple categories. Describe the tables.

---

## Questions 31-40: Advanced Design Scenarios

**31.** Design a database for a version control system. A repository can have multiple branches. A branch can have multiple commits. A commit can modify multiple files. Describe the tables.

**32.** Design a database for a learning management system (LMS). A course can have multiple modules. A module can have multiple lessons. A student can enroll in multiple courses. Track student progress. Describe the tables.

**33.** Design a database for a ride-sharing app. A driver can give multiple rides. A passenger can take multiple rides. A ride can have multiple stops. Describe the tables.

**34.** Design a database for a food delivery service. A restaurant can have multiple menu items. A customer can place multiple orders. An order can have items from multiple restaurants. A driver can deliver multiple orders. Describe the tables.

**35.** Design a database for a task management system. A project can have multiple tasks. A task can be assigned to multiple users. A task can have multiple subtasks. Track task status and priority. Describe the tables.

**36.** Design a database for a healthcare appointment system. A patient can book multiple appointments. A doctor can have multiple appointments. An appointment can have multiple follow-ups. Describe the tables.

**37.** Design a database for a crowdfunding platform. A user can create multiple campaigns. A user can back multiple campaigns. A campaign can have multiple reward tiers. Describe the tables.

**38.** Design a database for a job portal. A company can post multiple job listings. A candidate can apply to multiple jobs. A job can have multiple required skills. Describe the tables.

**39.** Design a database for a parking management system. A parking lot can have multiple parking spots. A customer can make multiple reservations. A reservation can cover multiple time slots. Describe the tables.

**40.** Design a database for a donation management system. A donor can make multiple donations. A charity can receive donations from multiple donors. A donation can be allocated to multiple programs. Describe the tables.

---

## Questions 41-50: Special Design Considerations

**41.** Design a database for a multi-tenant SaaS application. Each tenant should have isolated data. Describe how you would structure the tables to support tenancy.

**42.** Design a database that needs to track historical changes to employee salaries. An employee can have multiple salary records over time. Describe the tables with effective date tracking.

**43.** Design a database for a product catalog with variant support. A product can have multiple variants (size, color). Each variant can have different prices and stock. Describe the tables.

**44.** Design a database for an audit logging system. Track who made what changes to which records and when. Describe the tables for audit trails.

**45.** Design a database for a role-based access control (RBAC) system. A user can have multiple roles. A role can have multiple permissions. A permission can be assigned to multiple roles. Describe the tables.

**46.** Design a database for a notification system. A user can receive multiple notifications. A notification can be of multiple types. Track read/unread status. Describe the tables.

**47.** Design a database for a review and rating system. A user can review multiple products. A product can have multiple reviews. A review can have multiple helpful votes. Describe the tables.

**48.** Design a database for a shipping and tracking system. An order can have multiple shipments. A shipment can have multiple tracking events. A shipment can contain multiple items. Describe the tables.

**49.** Design a database for a content management system (CMS). A page can have multiple versions. A page can be associated with multiple tags. A user can edit multiple pages. Describe the tables.

**50.** Design a database for a recommendation system. Track user preferences, item interactions, and similar item relationships. Describe the tables to support recommendations.

---

## Answer Template

For each question, structure your answer as follows:

```markdown
## Question X: [System Name]

### Tables:

**table_name**
| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | INT | PRIMARY KEY | Unique identifier |
| ... | ... | ... | ... |

### Relationships:
- One-to-Many: [describe]
- Many-to-Many: [describe with junction table]

### ER Diagram (text representation):
[Table1] --(1:N)--> [Table2]
[Table3] <----(M:N)----> [Table4] via [JunctionTable]
```

---

## Key Design Principles to Remember

### 1. Identify Entities and Relationships
- **1:1** - Rare, consider merging tables
- **1:N** - Foreign key on the "many" side
- **M:N** - Requires a junction/bridge table

### 2. Normalization
- **1NF** - Atomic values, no repeating groups
- **2NF** - No partial dependencies
- **3NF** - No transitive dependencies

### 3. Primary Keys
- Use surrogate keys (auto-increment ID) or natural keys
- Be consistent across all tables

### 4. Foreign Keys
- Always define referential integrity
- Consider ON DELETE CASCADE/SET NULL

### 5. Common Junction Table Pattern
```sql
CREATE TABLE student_class (
    student_id INT NOT NULL,
    class_id INT NOT NULL,
    enrollment_date DATE,
    PRIMARY KEY (student_id, class_id),
    FOREIGN KEY (student_id) REFERENCES student(id),
    FOREIGN KEY (class_id) REFERENCES class(id)
);
```

---

## Sample Answer (Question 1)

## Question 1: University Class Enrollment System

### Tables:

**student**
| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | INT | PRIMARY KEY | Student ID |
| name | VARCHAR(100) | NOT NULL | Student name |
| email | VARCHAR(100) | UNIQUE | Student email |
| enrollment_year | INT | | Year of enrollment |

**class**
| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | INT | PRIMARY KEY | Class ID |
| name | VARCHAR(100) | NOT NULL | Class name |
| department | VARCHAR(50) | | Department offering class |
| credits | INT | | Credit hours |
| max_capacity | INT | | Maximum students |

**student_class** (Junction Table)
| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| student_id | INT | PRIMARY KEY, FK | Reference to student |
| class_id | INT | PRIMARY KEY, FK | Reference to class |
| enrollment_date | DATE | NOT NULL | Date of enrollment |
| grade | VARCHAR(2) | | Final grade |

### Relationships:
- Student (1) ---- (N) student_class
- Class (1) ---- (N) student_class
- Student (M) ---- (N) Class via student_class

Good luck with your interview preparation! 🍀
