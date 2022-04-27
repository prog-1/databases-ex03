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
CREATE TABLE exams(
student_id INTEGER,
lesson_id INTEGER,
grade INTEGER
);
```

Create `exams.sql` file with `INSERT INTO` query that adds entries to `exams` table with the data from `exams.csv`.

### 1. Find the number of students in every class

Write a query that outputs `year`, `modifier`, `student_cnt` columns that specify the number of students (`student_cnt`) in a class for all classes.

```sql
SELECT year, modifier, count (*) AS student_cnt FROM students
JOIN groups ON groups.student_id = students.id
JOIN class ON groups.class_id = class.id
GROUP BY year, modifier
```

### 2. Find the number of students for every year

Write a query that outputs `year`, `student_cnt` columns that specify the number of students (`student_cnt`) in a class for each year (`year`).

```sql
SELECT year, count (*) AS student_cnt FROM students
JOIN groups ON groups.student_id = students.id
JOIN class ON groups.class_id = class.id
GROUP BY year
```

### 3. Find unique subjects that students learn for a given year

Write a query that outputs `name`, `surname`, `unique_lesson_cnt`, where `unique_lesson_cnt` is the number of **unique** lessons that a student identified by `name`, `surname` has.

```sql
SELECT students.name, students.surname, count (*) AS unique_lesson_cnt FROM lessons
JOIN timetable ON timetable.lesson_id = lessons.id
JOIN groups ON timetable.class_id = groups.class_id
JOIN students ON groups.student_id = students.id
GROUP BY students.name, students.surname
```

### 4. Find how many distinct lessons have exams

Write a query that outputs `name` and `lesson_cnt`, where `lesson_cnt` is the number of **unique** lessons (`name`) that have exams.

```sql
SELECT DISTINCT lessons.name 
FROM exams
JOIN lessons ON (exams.lesson_id = lessons.id)
```

### 5.1. Find an average grade for each exam

Write a query that outputs `year`, `modifier`, `lesson` `average_grade`, where `average_grade` is an average grade for each class (identified by `year` and `modifier`) and a subject (identified by `lesson`).

```sql
-- WITH 
-- noround AS(
-- SELECT avg(exams.grade) AS avg_g
-- FROM exams
-- GROUP BY avg_g
-- )
SELECT class.year, class.modifier, lessons.name, avg(exams.grade) AS average_grade --round(avg_g, 2) AS average_grade
FROM exams
--FROM noround
--JOIN exams (there is bug with rounding)
JOIN groups ON (exams.student_id = groups.student_id)
JOIN class ON (groups.class_id = class.id)
JOIN lessons ON (exams.lesson_id = lessons.id)
GROUP BY year, modifier
```

### 5.2 Find the number of students that passed/failed exams

Write a query that finds the number of students that passed (4+ grade) and failed (<4 grade) the exams.

```sql
WITH 
	passed AS (
		SELECT exams.grade,count(exams.grade) AS p
		FROM exams
		GROUP BY grade
		HAVING exams.grade >= 4
	),
	failed AS (
		SELECT exams.grade, count(exams.grade) AS f
		FROM exams
		GROUP BY grade
		HAVING exams.grade < 4
	), 
	summ AS (
		SELECT sum(passed.p) AS sump
		FROM passed
	)
SELECT sump AS passed, sum(f) AS failed
FROM summ
JOIN failed
```

### 5.3 How many students did not attend the exams

Write a query that finds the number of students that did not attend the exams

```sql
SELECT count(*) AS missed
FROM exams
WHERE grade IS NULL
```

### 6. Passed/failed/missed exams for each student

Write a query that outputs `name`, `surname`, `passed_exams`, `failed_exams`, `missed_exams`, where `passed_exams`, `failed_exams`, `missed_exams` is the number of passed/failed/missed exams for each student (identified by `name`, `surname`).

```sql
WITH
	passed AS (
		SELECT id, count(*) AS p
		FROM students
		JOIN exams ON exams.student_id == students.id AND exams.grade >= 4
		GROUP BY id
	),
	failed AS (
		SELECT id, count(*) AS f
		FROM students
		JOIN exams ON exams.student_id == students.id AND exams.grade < 4
		GROUP BY id
	), 
	missed AS (
		SELECT id, count(*) AS missed
		FROM students
		JOIN exams ON exams.student_id = students.id AND exams.grade IS NULL
		GROUP BY id
	)

SELECT students.name, students.surname, p AS passed_exams, f AS faild_exams, missed AS missed_exams
FROM exams
JOIN students ON (exams.student_id = students.id)
LEFT JOIN passed ON students.id = passed.id
LEFT JOIN failed ON students.id = failed.id
LEFT JOIN missed ON students.id = missed.id
GROUP BY name, surname
```

### 7. Find unique lessons count for each class.

Write a query that for each class (`year`, `modifier`) finds unique lesson count.

```sql
SELECT class.year, class.modifier, count(*) AS lesson_cnt
FROM timetable
JOIN class ON (timetable.class_id = class.id)
GROUP BY year, modifier
```
