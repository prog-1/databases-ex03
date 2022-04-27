# Databases

Download [DB Browser for SQLite](https://sqlitebrowser.org/). Complete all
assignments in the DBMS using SQL language. Copy the SQL scripts into the fields
below. Some scripts are already given as an example.

## Assignment

The exercises are based on the database from the previous assignment
[databases-ex02](https://github.com/prog-1/databases-ex02). You may import the database from `school.db` to the DB Browser for SQLite to complete the exercises.

### Exams table

Create a table `exams` with fields of the given type:

* `student_id`: `integer`
* `lesson_id`: `integer`
* `grade`: `integer`

The table contains grades that a student identified by the student ID received for the lesson identified by the lesson ID.

The table may contain `NULL` values. The `NULL` values specify that a student was not attending the exam. 

```sql
CREATE TABLE exams (
	student_id	INTEGER,
	lesson_id	INTEGER,
	grade	INTEGER
)
```

Create `exams.sql` file with `INSERT INTO` query that adds entries to `exams` table with the data from `exams.csv`.

### 1. Find the number of students in every class

Write a query that outputs `year`, `modifier`, `student_cnt` columns that specify the number of students (`student_cnt`) in a class for all classes.

```sql
SELECT c.year, c.modifier, count(*) as student_cnt  FROM class as c
JOIN groups
ON id=class_id
GROUP by year, modifier

```

### 2. Find the number of students for every year

Write a query that outputs `year`, `student_cnt` columns that specify the number of students (`student_cnt`) in a class for each year (`year`).

```sql
SELECT c.year, count(*) as student_cnt  FROM class as c
JOIN groups
ON id=class_id
GROUP by year
```

### 3. Find unique subjects that students learn for a given year

Write a query that outputs `name`, `surname`, `unique_lesson_cnt`, where `unique_lesson_cnt` is the number of **unique** lessons that a student identified by `name`, `surname` has.

```sql
SELECT c.year,s.name, s.surname, count(*) AS unique_lesson_cnt FROM lessons as l
JOIN timetable as t 
ON t.lesson_id = l.id
JOIN groups as g 
ON t.class_id = g.class_id
JOIN students as s 
ON g.student_id = s.id
JOIN class as c
ON c.id = g.class_id
GROUP BY year,s.name, s.surname
```

### 4. Find how many distinct lessons have exams

Write a query that outputs `name` and `lesson_cnt`, where `lesson_cnt` is the number of **unique** lessons (`name`) that have exams.

```sql
SELECT DISTINCT lessons.name 
FROM exams
JOIN lessons 
ON exams.lesson_id = lessons.id
```

### 5.1. Find an average grade for each exam

Write a query that outputs `year`, `modifier`, `lesson` `average_grade`, where `average_grade` is an average grade for each class (identified by `year` and `modifier`) and a subject (identified by `lesson`).

```sql
SELECT c.year, c.modifier, l.name, round(avg(e.grade), 2) AS average_grade 
FROM exams as e
JOIN groups as g 
ON e.student_id = g.student_id
JOIN class as c 
ON  c.id= g.class_id 
JOIN lessons as l 
ON l.id = e.lesson_id 
GROUP BY year, modifier
```

### 5.2 Find the number of students that passed/failed exams

Write a query that finds the number of students that passed (4+ grade) and failed (<4 grade) the exams.

```sql
PASTE YOUR CODE HERE
```

### 5.3 How many students did not attend the exams

Write a query that finds the number of students that did not attend the exams

```sql
SELECT count(*) AS notattended FROM exams
WHERE grade IS NULL
```

### 6. Passed/failed/missed exams for each student

Write a query that outputs `name`, `surname`, `passed_exams`, `failed_exams`, `missed_exams`, where `passed_exams`, `failed_exams`, `missed_exams` is the number of passed/failed/missed exams for each student (identified by `name`, `surname`).

```sql
PASTE YOUR CODE HERE
```

### 7. Find unique lessons count for each class.

Write a query that for each class (`year`, `modifier`) finds unique lesson count.

```sql
SELECT c.year, c.modifier, count(*) AS unique_lessons
FROM timetable
JOIN class as c 
ON c.id= timetable.class_id 
GROUP BY c.year, c.modifier
```