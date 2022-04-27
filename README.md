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
CREATE TABLE "exams" (
	"student_id"	INTEGER,
	"lesson_id"	INTEGER,
	"grade"	INTEGER
)
```

Create `exams.sql` file with `INSERT INTO` query that adds entries to `exams` table with the data from `exams.csv`.

### 1. Find the number of students in every class

Write a query that outputs `year`, `modifier`, `student_cnt` columns that specify the number of students (`student_cnt`) in a class for all classes.

```sql
SELECT year, modifier, student_cnt FROM (SELECT class_id, count(*) AS student_cnt  FROM groups GROUP BY class_id)
INNER JOIN class
on class_id == class.id
```

### 2. Find the number of students for every year

Write a query that outputs `year`, `student_cnt` columns that specify the number of students (`student_cnt`) in a class for each year (`year`).

```sql
SELECT year, count(*) AS student_cnt FROM(
SELECT * FROM groups
INNER JOIN class
on class_id == class.id
) GROUP BY year
```

### 3. Find unique subjects that students learn for a given year

Write a query that outputs `name`, `surname`, `unique_lesson_cnt`, where `unique_lesson_cnt` is the number of **unique** lessons that a student identified by `name`, `surname` has.

```sql
SELECT name, surname, count(*) as unique_lesson_cnt FROM students
JOIN groups ON groups.student_id == students.id
JOIN class ON groups.class_id == class.id
JOIN (
SELECT class_id as clid FROM timetable
GROUP BY  class_id, lesson_id
) ON clid == class.id
GROUP BY students.id 
```

### 4. Find how many distinct lessons have exams

Write a query that outputs `name` and `lesson_cnt`, where `lesson_cnt` is the number of **unique** lessons (`name`) that have exams.

```sql
SELECT name FROM exams
JOIN lessons ON lessons.id == exams.lesson_id
GROUP BY lesson_id
```

### 5.1. Find an average grade for each exam

Write a query that outputs `year`, `modifier`, `lesson` `average_grade`, where `average_grade` is an average grade for each class (identified by `year` and `modifier`) and a subject (identified by `lesson`).

```sql
SELECT year, modifier, lessons.name, AVG(exams.grade) FROM timetable
JOIN class ON timetable.class_id == class.id
JOIN lessons ON lessons.id == timetable.lesson_id
JOIN groups ON groups.class_id == class.id
JOIN students ON students.id == groups.student_id
JOIN exams ON students.id == exams.student_id
GROUP BY year, modifier, lessons.name
```

### 5.2 Find the number of students that passed/failed exams

Write a query that finds the number of students that passed (4+ grade) and failed (<4 grade) the exams.

```sql
SELECT * FROM (
SELECT count(*) as PASSES FROM exams WHERE grade >=4
)
JOIN (SELECT count(*) as FAILED FROM exams WHERE grade <4)
ON 0 == 0
```

### 5.3 How many students did not attend the exams

Write a query that finds the number of students that did not attend the exams

```sql
SELECT count(*) as "NOT ATTENDED" FROM exams WHERE grade IS NULL
```

### 6. Passed/failed/missed exams for each student

Write a query that outputs `name`, `surname`, `passed_exams`, `failed_exams`, `missed_exams`, where `passed_exams`, `failed_exams`, `missed_exams` is the number of passed/failed/missed exams for each student (identified by `name`, `surname`).

```sql
WITH
	not_attended as (
		SELECT 
			students.id as student_id,
			count(*) AS missed_exams
		FROM students 
		JOIN exams 
			ON exams.student_id == students.id AND exams.grade IS NULL
		GROUP BY student_id

	), 
	failed as (
		SELECT 
			id as student_id, 
			count(*) AS failed_exams 
		FROM students 
		JOIN exams 
			ON exams.student_id == students.id AND exams.grade < 4
		GROUP BY id

	),
	passed as(
		SELECT 
			count(*) AS passed_exams,
			id as student_id
		FROM students 
		JOIN exams 
			ON exams.student_id == students.id AND exams.grade >=4
		GROUP BY id
	)
select
    students.name as name,
    students.surname as surname,
    not_attended.missed_exams as missed_exams,
    failed.failed_exams as failed_exams,
	passed.passed_exams as passed_exams
from students
left join not_attended on students.id == not_attended.student_id
left join failed on students.id == failed.student_id
left join passed on students.id == passed.student_id
```

### 7. Find unique lessons count for each class.

Write a query that for each class (`year`, `modifier`) finds unique lesson count.

```sql
SELECT year, modifier, count(*) as unique_lesson FROM class
JOIN (
SELECT * FROM timetable
GROUP BY class_id, lesson_id
) ON class_id == class.id
GROUP BY class.id
```