# SQL-Cinema-Database
Relational cinema database designed using ERD modelling and SQL, featuring table relationships, constraints, joins, views, subqueries and aggregate queries.

# 🎬 Cinema Database Design & SQL

A relational database project designed to model the operations of a cinema, including movies, cinema locations, customers, tickets, seating, orders and food items.

The project demonstrates practical experience in **SQL, relational database design and Entity Relationship Diagram (ERD) modelling**.

---

## 🗄️ Database Design

The database was designed using an **Entity Relationship Diagram (ERD)** to define the relationships between key entities, including:

- Cinema
- Movie
- Customer
- Ticket
- Seat
- Orders
- Item
- CinemaToMovie
- OrderToItem

The database uses **primary keys, composite keys, foreign keys and junction tables** to maintain relationships and data integrity.

<p align="center">
  <img src="ERD.png" width="750" alt="Cinema Database ERD">
</p>

---

## 💻 SQL Implementation

The database was implemented using SQL and includes:

- Table creation and relational schema design
- Primary and foreign key constraints
- Composite keys
- `CHECK` constraints for data validation
- Referential integrity using `ON DELETE` and `ON UPDATE`
- Data insertion
- SQL views
- Multi-table queries

---

## 🔎 SQL Queries

Queries were developed to demonstrate different methods of retrieving and analysing relational data, including:

- `SELECT` and `WHERE`
- `NATURAL JOIN`
- Multi-table joins
- `ORDER BY`
- `GROUP BY`
- `HAVING`
- Aggregate functions such as `COUNT()` and `AVG()`
- Subqueries
- Self joins
- Table aliases

Example:

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
