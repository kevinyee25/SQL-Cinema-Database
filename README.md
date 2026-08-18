# SQL-Cinema-Database
Relational cinema database designed using ERD modelling and SQL, featuring table relationships, constraints, joins, views, subqueries and aggregate queries.

# 🎬 Cinema Database Design & SQL

A relational database project designed to model the operations of a cinema, including movie screenings, cinema locations, customers, ticket purchases, seating and food orders.

The project demonstrates practical skills in **SQL, relational database design, Entity Relationship Diagram (ERD) modelling, data integrity and relational querying**.

---

## 📌 Project Overview

The database was designed to represent several interconnected areas of cinema operations.

It allows information to be stored and retrieved about:

- Cinema locations and their ratings
- Movies and their classifications
- Movies shown at different cinema locations
- Customers and membership status
- Ticket purchases
- Cinema seating
- Customer orders
- Food and beverage items

The project involved designing the database structure through an **Entity Relationship Diagram (ERD)** before implementing the relational model using SQL.

---

## 🗺️ Entity Relationship Diagram (ERD)

The ERD defines the structure of the database and illustrates how the different entities interact with one another.

<p align="center"> <img width="700" alt="Screen Shot 2026-08-18 at 9 44 14 pm" src="https://github.com/user-attachments/assets/de6ac3f6-89a5-43b4-aa8e-a5f889665b27" />
<p align="center">
  <img src="Cinema ERD Diagram.pdf" width="850" alt="Cinema Database Entity Relationship Diagram">
</p>

### Main Entities

| Entity | Purpose |
|---|---|
| **Cinema** | Stores cinema locations and their star ratings |
| **Movie** | Stores movie information including title, release year, duration and classification |
| **Customer** | Stores customer details and membership status |
| **Ticket** | Records ticket purchases and connects customers with movies, cinemas and seats |
| **Seat** | Stores individual seat information and seat types |
| **Orders** | Records customer food and beverage orders made at cinema locations |
| **Item** | Stores food and beverage products and their prices |
| **CinemaToMovie** | Associative entity connecting cinemas with the movies they show |
| **OrderToItem** | Associative entity connecting customer orders with purchased items |

---

## 🔗 Database Relationships

The ERD was structured to represent the relationships between different areas of cinema operations.

### Cinema ↔ Movie

A cinema can show multiple movies, while the same movie can be shown at multiple cinema locations.

This **many-to-many relationship** is resolved using the `CinemaToMovie` associative table.

`CinemaToMovie` contains:

- `cinemaLocation`
- `movieName`
- `movieYear`

These attributes link individual cinema locations to the movies being shown.

### Customer → Ticket

Customers can purchase cinema tickets.

The `Ticket` table records information including:

- Customer
- Movie
- Cinema location
- Seat
- Ticket type
- Ticket price
- Date
- Time

This allows a ticket purchase to connect several parts of the database together.

### Ticket → Seat

Each ticket is associated with a cinema seat using `seatID` and `cinemaNo`.

The `Seat` entity also stores the type of cinema experience, such as **STANDARD, LUX or XTREMESCREEN**.

### Customer → Orders

Customers can place orders during their cinema visit.

Each order is linked to a customer and the cinema location where the order was made.

### Orders ↔ Item

An order can contain multiple items, and the same item can appear in multiple orders.

This **many-to-many relationship** is resolved through the `OrderToItem` associative table.

The table records:

- `orderID`
- `itemID`
- `quantity`

This allows the database to track both the items included in an order and their quantities.

---

## 🗄️ SQL Database Implementation

The ERD was translated into a relational database using SQL.

The implementation demonstrates:

- `CREATE TABLE`
- Primary keys
- Composite primary keys
- Foreign keys
- Referential integrity
- `CHECK` constraints
- `ON DELETE CASCADE`
- `ON DELETE RESTRICT`
- `ON UPDATE CASCADE`
- Data insertion
- SQL views
- Relational queries

### Example – Table Constraints

```sql
CREATE TABLE CinemaToMovie
(
    cinemaLocation varchar(20),
    movieName      varchar(50),
    movieYear      integer,

    CONSTRAINT CinemaToMoviePK
        PRIMARY KEY (cinemaLocation, movieName, movieYear),

    CONSTRAINT CinemaToMovieFK_Cinema
        FOREIGN KEY (cinemaLocation)
        REFERENCES Cinema
        ON DELETE CASCADE,

    CONSTRAINT CinemaToMovieFK_Movie
        FOREIGN KEY (movieName, movieYear)
        REFERENCES Movie
        ON DELETE CASCADE
);
```

This table demonstrates the use of **composite primary keys and foreign keys** to implement a many-to-many relationship while maintaining referential integrity.

---

## 🛡️ Data Integrity

Several constraints were implemented to prevent invalid data from being entered into the database.

Examples include:

```sql
CHECK (rating IN ('M', 'MA', 'PG'))
```

Restricts movie classifications to predefined values.

```sql
CHECK (starRating <= 5.0)
```

Prevents cinema ratings from exceeding the five-star rating system.

```sql
CHECK (ticketPrice > 0)
```

Ensures ticket prices are positive.

```sql
CHECK (quantity > 0)
```

Prevents an order from containing an invalid quantity.

These constraints help maintain the **accuracy and consistency of stored data**.

---

## 👁️ SQL View

A SQL view was created to provide a simplified representation of highly rated cinema locations and the movies they show.

```sql
CREATE VIEW CinemaMovies AS
SELECT cinemaLocation AS "Cinema",
       movieName AS "Movie",
       starRating AS "Rating",
       movieYear AS "Release Year"
FROM Cinema
NATURAL JOIN CinemaToMovie
NATURAL JOIN Movie
WHERE starRating > 4.0;
```

This demonstrates how a **view can combine information from multiple related tables and present frequently required information as a reusable virtual table**.

---

# 🔎 SQL Query Demonstrations

A series of queries were developed to demonstrate different methods of retrieving and analysing information from the relational database.

---

## 1. Single-Table Query

### Objective

Identify cinema locations with a star rating greater than 3.0.

```sql
SELECT *
FROM Cinema
WHERE starRating > 3.0;
```

### Demonstrates

- `SELECT`
- `FROM`
- `WHERE`
- Conditional filtering

This is a basic retrieval query that filters records according to a specified condition.

---

## 2. Multi-Table Natural Join

### Objective

Identify cinemas showing movies classified as **M**, with the results ordered by movie name.

```sql
SELECT cinemaLocation,
       movieName,
       movieYear,
       rating
FROM Cinema
NATURAL JOIN CinemaToMovie
NATURAL JOIN Movie
WHERE rating = 'M'
ORDER BY movieName;
```

### Demonstrates

- Multi-table querying
- `NATURAL JOIN`
- Filtering
- `ORDER BY`
- Relational data retrieval

The query combines information stored across the `Cinema`, `CinemaToMovie` and `Movie` tables.

---

## 3. Cross-Product Join

The previous query was recreated using cross-product notation and explicit join conditions.

```sql
SELECT Cinema.cinemaLocation,
       Movie.movieName,
       Movie.movieYear,
       Movie.rating
FROM Cinema, CinemaToMovie, Movie
WHERE Cinema.cinemaLocation = CinemaToMovie.cinemaLocation
  AND Movie.movieName = CinemaToMovie.movieName
  AND Movie.movieYear = CinemaToMovie.movieYear
  AND rating = 'M'
ORDER BY movieName;
```

### Demonstrates

- Multi-table relationships
- Explicit join conditions
- Table-qualified column references
- Understanding of the relational logic behind joins

This demonstrates how relationships between tables can be explicitly defined rather than relying on `NATURAL JOIN`.

---

## 4. Aggregation, GROUP BY & HAVING

### Objective

Identify highly rated cinema branches showing more than one movie.

```sql
SELECT cinemaLocation AS "HOYTS Branch",
       starRating AS "Rating",
       COUNT(movieName) AS "Number of Movies"
FROM Cinema
NATURAL JOIN CinemaToMovie
NATURAL JOIN Movie
WHERE starRating > 4.0
GROUP BY cinemaLocation, starRating
HAVING COUNT(*) > 1
ORDER BY cinemaLocation, starRating;
```

### Demonstrates

- `COUNT()`
- `GROUP BY`
- `HAVING`
- `WHERE`
- Column aliases
- Multi-table aggregation
- `ORDER BY`

This query demonstrates the ability to **aggregate relational data and apply conditions to grouped results**.

---

## 5. Subquery

### Objective

Identify food and beverage items that cost more than the average item price.

```sql
SELECT itemID,
       itemName,
       price
FROM Item
WHERE price > (
    SELECT AVG(price)
    FROM Item
);
```

### Demonstrates

- Subqueries
- `AVG()`
- Aggregate functions
- Dynamic filtering

Rather than using a fixed price threshold, the query calculates the average price and uses the result as the filtering condition.

---

## 6. Self-Join / Cross Product

### Objective

Generate combinations between cinema members and non-members.

```sql
SELECT a.customerID AS "HOYTS Member",
       b.customerID AS "Non-Member"
FROM Customer a,
     Customer b
WHERE a.isMember = 'Y'
  AND b.isMember = 'N';
```

### Demonstrates

- Self-joining a table
- Table aliases
- Cross-product notation
- Conditional filtering

The `Customer` table is referenced twice using aliases `a` and `b`, allowing records within the same table to be compared.

---

## 🧠 SQL Concepts Demonstrated

Through the database design and queries, this project demonstrates practical understanding of:

- Relational database design
- Entity Relationship Diagrams (ERD)
- One-to-many relationships
- Many-to-many relationships
- Associative/junction tables
- Primary and foreign keys
- Composite keys
- Referential integrity
- Data validation and constraints
- SQL views
- Single and multi-table queries
- Natural joins
- Explicit joins
- Aggregate functions
- `GROUP BY` and `HAVING`
- Subqueries
- Self joins
- Data insertion and management

---

## 🛠️ Technical Skills

`SQL` • `Relational Database Design` • `ERD Modelling` • `Data Modelling` • `Database Constraints` • `SQL Queries` • `Data Analysis`

---

## 📁 Project Files

📄 [View SQL Database](cinemas.txt)

📊 [View ERD Diagram](Cinema%20ERD%20Diagram.pdf)

🔎 [View Query Demonstrations](Queries.pdf)

---
