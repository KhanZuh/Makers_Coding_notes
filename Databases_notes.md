# 📡 Database Concepts & SQL Foundations

## TLdr: Give me the 80/20

### Your success formula
- Write the test first (what should happen?)
- Create the repository method (how to talk to database?)
- Write the SQL (what data operation?)
- Make it pass (does it work?)

### Key mindsets
- Repository = Database Interface (hides SQL complexity)
- Test Everything (databases are stateful, tests catch issues)
- Foreign Keys = Relationships (connect your tables)
= Start Simple (get CRUD working, then add features)


## PostgreSQL Command Cheat Sheet

```bash
-- Connect to PostgreSQL
psql -U username -d database_name
```
This is a terminal/command line command to connect to a PostgreSQL server. The -U flag specifies the username, and -d specifies which database to connect to. You'd replace "username" and "database_name" with actual values.
These are shell commands that invoke PostgreSQL utilities like psql, pg_dump, and pg_restore.



```sql
-- Create a database
CREATE DATABASE bookstore;
```
This SQL command creates a new database called "bookstore". Once connected to PostgreSQL, you can run this to create your database.
These are executed inside the PostgreSQL environment, via the psql command-line tool.

```bash
-- Use the database
\c bookstore
```
This is a PostgreSQL meta-command (notice the backslash) that connects to the "bookstore" database you just created. It's equivalent to "use this database" - switching your current session to work within that specific database.

```bash
-- List all tables
\dt
```
Another meta-command that lists all tables in the current database. When you first create a database, this will show an empty result since no tables exist yet. As you create tables, they'll appear in this list.


```bash
psql -h 127.0.0.1 -l
```
- psql = same PostgreSQL client
- -h 127.0.0.1 = connect to localhost
- -l = list all databases flag

The postgreSQL meta-commands only work inside the psql CLI tool 

The workflow here is typical for setting up a new PostgreSQL database: connect to the server, create your database, switch to using it, then check what tables exist (which would be none initially). From here, you'd typically start creating tables with CREATE TABLE statements to build your database structure.

## Creating tables 

```sql
-- Essential data types: INTEGER, VARCHAR, BOOLEAN, DATE
CREATE TABLE books (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    author VARCHAR(100),
    price DECIMAL(10,2),
    published_date DATE,
    in_stock BOOLEAN DEFAULT true
);
```

# 🔄 CRUD Operations - SQL Commands

## Create (INSERT)
```sql
-- Single record
INSERT INTO books (title, author, price, published_date) 
VALUES ('1984', 'George Orwell', 12.99, '1949-06-08');

-- Multiple records
INSERT INTO books (title, author, price) VALUES 
('Animal Farm', 'George Orwell', 10.99),
('Brave New World', 'Aldous Huxley', 13.50);
```

## Read (SELECT)
```sql
-- Basic queries (80% of what you'll use)
SELECT * FROM books;
SELECT title, author FROM books;
SELECT * FROM books WHERE author = 'George Orwell';
SELECT * FROM books WHERE price > 12.00;
SELECT * FROM books ORDER BY price DESC;
SELECT * FROM books LIMIT 5;
```

## Update 
```sql
UPDATE books 
SET price = 11.99 
WHERE title = 'Animal Farm';

UPDATE books 
SET in_stock = false 
WHERE price > 15.00;
```

## Delete
```sql
DELETE FROM books 
WHERE id = 1;

DELETE FROM books 
WHERE in_stock = false;
```

## Joins (Essential for relationships):

### Cohorts table
```
id | name
---|----------------
1  | Web Dev Bootcamp
2  | Data Science 2024
3  | UX Design Spring
```

### Students table
```
id | name          | email                    | cohort_id
---|---------------|--------------------------|----------
1  | Alice Johnson | alice@email.com          | 1
2  | Bob Smith     | bob@email.com            | 1
3  | Carol Davis   | carol@email.com          | 2
4  | David Wilson  | david@email.com          | 3
5  | Emma Brown    | emma@email.com           | 2
```



### Query
```sql
-- Get students with their cohort names
SELECT students.name, students.email, cohorts.name as cohort_name
FROM students
JOIN cohorts ON students.cohort_id = cohorts.id;
```

### Result 
```
name          | email           | cohort_name
--------------|-----------------|------------------
Alice Johnson | alice@email.com | Web Dev Bootcamp
Bob Smith     | bob@email.com   | Web Dev Bootcamp
Carol Davis   | carol@email.com | Data Science 2024
David Wilson  | david@email.com | UX Design Spring
Emma Brown    | emma@email.com  | Data Science 2024
```
The JOIN connects students to their cohorts by matching the cohort_id in the students table with the id in the cohorts table, giving you each student's information along with their cohort name.





# 🐍 Python Database Integration
- The `DatabaseConnection` class handles the core database connectivity:

- Connection Setup: Uses `psycopg2` (PostgreSQL adapter) to establish connections with configurable parameters like host, database name, username, and password.

- Database Seeding: The `seed()` method reads SQL files and executes them to set up your database schema and initial data. This is useful for creating tables and populating them with test data.

- The `StudentRepository` class implements the Repository pattern, which provides a clean abstraction layer between your application logic and database operations:

- Separation of Concerns: Database queries are encapsulated in repository methods rather than scattered throughout your application code.

CRUD Operations:
- `all()` retrieves all students from the database
- `find()` gets a specific student by ID
- `create()` adds new students to the database
- `delete()` removes students by ID

Data Transformation: The private `_row_to_student()` method converts raw database rows into Student objects, handling the mapping between database columns and object properties.

Model classes 
- The `Student` class represents your data model:
- Data Structure: Defines the properties that make up a student (id, name, email, cohort_id).
- Object Equality: The `__eq__` method allows you to compare Student objects for equality, which is particularly useful in testing.
- String Representation: The `__repr__` method provides a clear string representation of Student objects for debugging and logging.

This pattern creates a clean separation between data access logic and business logic. Your application code works with Student objects rather than raw SQL queries, making it more maintainable and testable. The repository pattern also makes it easier to swap out different data sources or add caching layers without changing your core application logic.
The use of context managers (`with` statements) ensures proper resource management and automatic cleanup of database cursors, preventing memory leaks and connection issues.

## Setting Up Database Connection (Example)
```python
# database_connection.py
import psycopg2

class DatabaseConnection:
    def __init__(self):
        self.connection = None
    
    def connect(self, database_name="your_database"):
        self.connection = psycopg2.connect(
            host="localhost",
            database=database_name,
            user="your_username",
            password="your_password"
        )
        return self.connection
    
    def seed(self, sql_filename):
        """Load SQL file to set up database"""
        with open(sql_filename, 'r') as file:
            sql_script = file.read()
        
        with self.connection.cursor() as cursor:
            cursor.execute(sql_script)
            self.connection.commit()
```

## Repository Pattern Implementation
```python
# student_repository.py
class StudentRepository:
    def __init__(self, connection):
        self._connection = connection
    
    def all(self):
        """Return all students"""
        with self._connection.cursor() as cursor:
            cursor.execute("SELECT * FROM students")
            rows = cursor.fetchall()
            return [self._row_to_student(row) for row in rows]
    
    def find(self, student_id):
        """Find student by ID"""
        with self._connection.cursor() as cursor:
            cursor.execute("SELECT * FROM students WHERE id = %s", [student_id])
            row = cursor.fetchone()
            return self._row_to_student(row) if row else None
    
    def create(self, student):
        """Create new student"""
        with self._connection.cursor() as cursor:
            cursor.execute(
                "INSERT INTO students (name, email, cohort_id) VALUES (%s, %s, %s)",
                [student.name, student.email, student.cohort_id]
            )
            self._connection.commit()
    
    def delete(self, student_id):
        """Delete student by ID"""
        with self._connection.cursor() as cursor:
            cursor.execute("DELETE FROM students WHERE id = %s", [student_id])
            self._connection.commit()
    
    def _row_to_student(self, row):
        return Student(row[0], row[1], row[2], row[3])  # id, name, email, cohort_id
```

## Model Classes
```python
# student.py
class Student:
    def __init__(self, id, name, email, cohort_id):
        self.id = id
        self.name = name
        self.email = email
        self.cohort_id = cohort_id
    
    def __eq__(self, other):
        return self.__dict__ == other.__dict__
    
    def __repr__(self):
        return f"Student({self.id}, {self.name}, {self.email}, {self.cohort_id})"
```

# 📡 TDD for databases 

## Test Database Setup (conftest.py)
Purpose: Provides a clean, isolated database environment for each test.

Key Components:

- Fixture: `@pytest.fixture` creates reusable test setup that runs before each test
- Test Database: Uses a separate "test_database" to avoid affecting production data
- Data Cleanup: `TRUNCATE` statements clear all data before each test, ensuring tests don't interfere with each other
- CASCADE: Handles foreign key relationships when clearing data
- RESTART IDENTITY: Resets auto-incrementing ID counters to start from 1

Why This Matters: Each test runs in a predictable, clean state, making tests reliable and independent.

## Repository Testing Pattern
What's Being Tested: The `StudentRepository` class that handles database operations (CRUD - Create, Read, Update, Delete).



## Test Setup and Database Management Example

```python
# conftest.py (pytest configuration)
import pytest
import psycopg2
from database_connection import DatabaseConnection

@pytest.fixture
def db_connection():
    """Create test database connection"""
    connection = DatabaseConnection()
    conn = connection.connect("test_database")
    
    # Clean up before each test
    with conn.cursor() as cursor:
        cursor.execute("TRUNCATE students, cohorts RESTART IDENTITY CASCADE")
        conn.commit()
    
    yield conn
    
    # Cleanup after test
    conn.close()
```

## Repository Tests
```python
# test_student_repository.py
import pytest
from student_repository import StudentRepository
from student import Student

def test_all_returns_empty_list_when_no_students(db_connection):
    repository = StudentRepository(db_connection)
    assert repository.all() == []

def test_create_and_find_student(db_connection):
    repository = StudentRepository(db_connection)
    
    # First create a cohort
    with db_connection.cursor() as cursor:
        cursor.execute("INSERT INTO cohorts (name, start_date) VALUES (%s, %s)", 
                      ["April 2024", "2024-04-01"])
        db_connection.commit()
    
    # Test creating student
    student = Student(None, "John Doe", "john@makers.tech", 1)
    repository.create(student)
    
    # Test finding student
    all_students = repository.all()
    assert len(all_students) == 1
    assert all_students[0].name == "John Doe"
    assert all_students[0].email == "john@makers.tech"

def test_delete_student(db_connection):
    repository = StudentRepository(db_connection)
    
    # Setup: create a student
    with db_connection.cursor() as cursor:
        cursor.execute("INSERT INTO cohorts (name, start_date) VALUES (%s, %s)", 
                      ["April 2024", "2024-04-01"])
        cursor.execute("INSERT INTO students (name, email, cohort_id) VALUES (%s, %s, %s)",
                      ["Jane Doe", "jane@makers.tech", 1])
        db_connection.commit()
    
    # Test deletion
    repository.delete(1)
    assert repository.all() == []
```

# 🧑‍🎨 Schema Design and Relationships One-to-Many Relationship Design
```sql
-- Schema design example: Blog system
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(255) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL
);

CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    content TEXT,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Repository with Joins
```python
# post_repository.py
class PostRepository:
    def __init__(self, connection):
        self._connection = connection
    
    def find_with_user(self, post_id):
        """Find post with user information"""
        with self._connection.cursor() as cursor:
            cursor.execute("""
                SELECT posts.id, posts.title, posts.content, 
                       users.id, users.username, users.email
                FROM posts
                JOIN users ON users.id = posts.user_id
                WHERE posts.id = %s
            """, [post_id])
            
            row = cursor.fetchone()
            if row:
                user = User(row[3], row[4], row[5])
                post = Post(row[0], row[1], row[2], user)
                return post
            return None
    
    def find_by_user(self, user_id):
        """Find all posts by a specific user"""
        with self._connection.cursor() as cursor:
            cursor.execute("""
                SELECT id, title, content FROM posts 
                WHERE user_id = %s 
                ORDER BY created_at DESC
            """, [user_id])
            
            rows = cursor.fetchall()
            return [Post(row[0], row[1], row[2], user_id) for row in rows]
```

### Understanding One-to-Many Relationships
This example demonstrates a classic one-to-many relationship where one user can have multiple posts, but each post belongs to exactly one user. The relationship is established through the `user_id` foreign key in the posts table, which references the primary key of the users table. The `ON DELETE CASCADE` constraint ensures that when a user is deleted, all their associated posts are automatically removed, maintaining referential integrity.


The repository pattern showcases two common query patterns for one-to-many relationships. The `find_with_user` method uses an INNER JOIN to fetch a post along with its associated user data in a single query, avoiding the N+1 query problem. This is particularly useful when you need both the post and user information together. The `find_by_user` method demonstrates the reverse lookup - finding all posts that belong to a specific user, ordered by creation date to show the most recent posts first.


When working with one-to-many relationships, consider the query patterns your application needs most frequently. Sometimes you'll want to fetch the parent with all children (user with all posts), other times just the parent or just the children. The repository methods should be designed to efficiently support these common access patterns while maintaining clean separation between your domain logic and data access code.RetryClaude can make mistakes. Please double-check responses.


# ⬇️⬆️➡️ Sequence Diagrams

Understanding how your program communicates with the database:

```
Client -> Application -> Repository -> Database

Example sequence:
1. Client calls: app.get_student(1)
2. Application calls: student_repo.find(1)
3. Repository executes: SELECT * FROM students WHERE id = 1
4. Database returns: student data
5. Repository creates: Student object
6. Application returns: Student object to client
```

### Sequence Diagram Components:

- Actors: Client, Application, Repository, Database
- Messages: Method calls and SQL queries
- Returns: Data flowing back through the layers

### Understanding the Flow of Communication
Sequence diagrams are particularly valuable for visualizing how data flows through a layered architecture when interacting with databases. The flow demonstrates the separation of concerns principle, where each layer has a specific responsibility and communicates with adjacent layers through well-defined interfaces. 

The client layer handles user interaction and presentation logic, while remaining completely unaware of database implementation details. The application layer serves as the business logic coordinator, orchestrating operations without needing to know the specifics of data storage or retrieval mechanisms.


The repository pattern, illustrated in this sequence, acts as an abstraction layer that encapsulates all database access logic. This design allows the application layer to work with domain objects and business concepts rather than raw SQL queries or database-specific operations. 

When the repository receives a request to find a student, it translates this business requirement into the appropriate database query, executes it, and then transforms the raw database results back into meaningful domain objects that the application can work with.

### Benefits of This Architectural Pattern
This layered approach provides several critical advantages for software maintainability and scalability. The separation allows developers to modify database schemas, switch database technologies, or optimize queries without affecting the application's business logic or client interface. 

For example, you could migrate from a SQL database to a NoSQL solution by only changing the repository implementation, while the application and client layers remain completely unchanged. Additionally, this pattern facilitates testing by allowing developers to create mock repositories that return predictable test data without requiring an actual database connection.


The sequence also demonstrates how data transformation occurs at appropriate boundaries. Raw database rows are converted to domain objects within the repository, ensuring that higher layers work with strongly-typed, business-meaningful entities rather than generic data structures. This transformation encapsulates database-specific details like column names, data types, and null handling, presenting a clean, consistent interface to the application layer that reflects the business domain rather than storage implementation details.


# Application Layer - Wrapping Everything Together
The Application Layer serves as the orchestration layer that coordinates between different repositories and provides high-level business operations. It acts as a facade that simplifies complex interactions and implements application-specific logic.

## Key Benefits of the Application Layer:
**Simplified Interface**: The application layer presents a clean, intuitive interface to users or external systems, hiding the complexity of underlying data operations. Instead of requiring knowledge of multiple repositories and their interactions, users can call simple methods like `add_student()` or `list_students()`.

**Business Logic Coordination**: This layer handles cross-repository operations and business rules. For example, when adding a student, it validates that the cohort exists before creating the student record, ensuring data integrity at the application level.

**Transaction Management**: The application layer can manage complex transactions that span multiple repositories, ensuring that related operations either all succeed or all fail together.

**Error Handling and User Feedback**: It provides meaningful error messages and handles edge cases gracefully, translating technical database errors into user-friendly messages.

## Application layer example 
```python
# student_manager_app.py
class StudentManagerApp:
    def __init__(self, connection):
        # Dependency injection - repositories are injected via constructor
        # This makes testing easier and follows inversion of control principle
        self._student_repo = StudentRepository(connection)
        self._cohort_repo = CohortRepository(connection)
    
    def list_students(self):
        """List all students with their cohort names"""
        # Uses the repository's specialized method for joining data
        # Application layer focuses on presentation logic
        students = self._student_repo.all_with_cohorts()
        for student in students:
            # Handles formatting and display - separation of concerns
            print(f"{student.name} ({student.email}) - {student.cohort_name}")
    
    def add_student(self, name, email, cohort_name):
        """Add new student to existing cohort"""
        # Business rule: cohort must exist before adding student
        # Application layer coordinates between repositories
        cohort = self._cohort_repo.find_by_name(cohort_name)
        if not cohort:
            # Graceful error handling with meaningful messages
            print(f"Cohort '{cohort_name}' not found")
            return
        
        # Creates domain object and delegates persistence to repository
        student = Student(None, name, email, cohort.id)
        self._student_repo.create(student)
        print(f"Added {name} to {cohort_name}")
    
    def remove_student(self, student_id):
        """Remove student by ID"""
        # Validates existence before deletion - defensive programming
        student = self._student_repo.find(student_id)
        if student:
            self._student_repo.delete(student_id)
            print(f"Removed {student.name}")
        else:
            print("Student not found")

# Usage example showing the clean interface
def main():
    connection = get_database_connection()
    app = StudentManagerApp(connection)
    
    # Simple, intuitive operations
    app.list_students()
    app.add_student("Alice Johnson", "alice@email.com", "Data Science 2024")
    app.remove_student(123)
```

### Application Layer Explanation

The **StudentManagerApp** class demonstrates the Application Layer pattern, which serves as the orchestration hub that coordinates business operations across multiple repositories. This layer sits above the data access layer and provides a simplified, cohesive interface for complex operations that might involve multiple domain objects and repositories.

**Dependency Injection and Initialization**: The constructor takes a database connection and creates the necessary repository instances (`StudentRepository` and `CohortRepository`). This dependency injection approach makes the class highly testable since you can easily mock the repositories during unit testing. The application layer doesn't need to know the specifics of how data is stored or retrieved - it simply delegates these responsibilities to the appropriate repositories.

**Business Logic Coordination**: The `add_student` method exemplifies how the application layer enforces business rules and coordinates between repositories. Before creating a student, it first validates that the specified cohort exists by querying the cohort repository. This cross-repository validation ensures data integrity at the application level. If the cohort doesn't exist, it provides meaningful error feedback rather than allowing an invalid database state.

**Simplified Operations with Error Handling**: Each method in the application layer presents a clean, intuitive interface that handles the complexity behind the scenes. The `remove_student` method demonstrates defensive programming by first checking if the student exists before attempting deletion. The `list_students` method uses a specialized repository method to join student and cohort data, then focuses purely on presentation formatting. This separation means the application layer handles business logic and user interaction, while repositories handle data access mechanics.

The beauty of this pattern is evident in the `main()` function - complex database operations involving multiple tables and business rules are reduced to simple, readable method calls. This makes the codebase more maintainable, as changes to database structure only affect repositories, while changes to business rules are contained within the application layer.






# 📚 Library System Design Example


### Books Table

- book_id (Primary Key) - unique identifier
- title - book title
- isbn - international standard book number
- publication_year - when published
- author_id (Foreign Key) - links to Authors table
- genre - book category
- copies_available - number of copies in library

### Authors Table

- author_id (Primary Key) - unique identifier
- first_name - author's first name
- last_name - author's last name
- birth_year - when author was born
- nationality - author's country

### Members Table

- member_id (Primary Key) - unique identifier
- first_name - member's first name
- last_name - member's last name
- email - contact email
- join_date - when they joined the library
- phone - contact number

### Loans Table

- loan_id (Primary Key) - unique identifier
- book_id (Foreign Key) - which book was borrowed
- member_id (Foreign Key) - who borrowed it
- loan_date - when book was checked out
- due_date - when book should be returned
- return_date - when book was actually returned (null if still out)

## Relationships

### Authors to Books: One-to-Many

- One author can write multiple books
- Each book has one primary author (simplified for this example)

### Members to Loans: One-to-Many

- One member can have multiple loans over time
- Each loan belongs to one member

### Books to Loans: One-to-Many

- One book can be loaned multiple times (different dates)
- Each loan is for one specific book

```
┌─────────────────┐         ┌─────────────────┐
│    AUTHORS      │         │     BOOKS       │
├─────────────────┤         ├─────────────────┤
│ author_id (PK)  │────────▶│ book_id (PK)    │
│ first_name      │         │ title           │
│ last_name       │         │ isbn            │
│ birth_year      │         │ publication_year│
│ nationality     │         │ author_id (FK)  │
└─────────────────┘         │ genre           │
                            │ copies_available│
                            └─────────────────┘
                                     │
                                     │
                                     ▼
┌─────────────────┐         ┌─────────────────┐
│    MEMBERS      │         │     LOANS       │
├─────────────────┤         ├─────────────────┤
│ member_id (PK)  │────────▶│ loan_id (PK)    │
│ first_name      │         │ book_id (FK)    │◀──┘
│ last_name       │         │ member_id (FK)  │
│ email           │         │ loan_date       │
│ join_date       │         │ due_date        │
│ phone           │         │ return_date     │
└─────────────────┘         └─────────────────┘

Legend:
PK = Primary Key
FK = Foreign Key
──▶ = One-to-Many relationship
```
### Relationship flow

- One AUTHOR can write many BOOKS (1:M)
- One MEMBER can have many LOANS (1:M)
- One BOOK can have many LOANS over time (1:M)
- The LOANS table acts as the central hub connecting members to books

```
J.K. Rowling (author_id=1) 
    ├─ Harry Potter Book 1 (book_id=101)
    └─ Harry Potter Book 2 (book_id=102)

John Smith (member_id=201)
    ├─ Loan 1: borrowed book_id=101 on 2024-01-15
    └─ Loan 2: borrowed book_id=102 on 2024-02-20
```



