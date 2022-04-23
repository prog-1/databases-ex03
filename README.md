# Databases

Download [DB Browser for SQLite](https://sqlitebrowser.org/). Complete all
assignments in the DBMS using SQL language. Copy the SQL scripts into the fields
below. Some scripts are already given as an example.

## References

You may rely on the SQL language documentation while implementing the scripts.

## Assignment

The exercises are based on the database from the previous assignment
[databases-ex02](https://github.com/prog-1/databases-ex02). You may import the database from `school.db` to the DB Browser for SQLite to complete the exercises.

### Exams table

Create a table `exams` with fields of the given type:

* `student_id`: `integer`
* `lesson_id`: `integer`
* `grade`: `integer`

```sql
PASTE YOUR CODE HERE
```

Create exams.sql file with `INSERT INTO` query that adds entries to `exams` table with the data from `exams.csv`.

## Exercises

1. Find the number of students in every class.
2. Find the number of students for every year.
3. Find unique subjects that students learn for a given year.
4. Find how many distinct subjects have exams.
5. For each exam find
    - average grade
    - how many students passed (4+) and how many failed (<4) the exam.
    - how many students did not attend the exam
6. For each student that has at least a single exam scheuled find passed, failed and missed exam count.
7. For each year/mod find unique lesson count.
