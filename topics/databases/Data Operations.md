# Data Operations

## Definitions

distinct
- Removes duplicates 


## Examples

~~~
select * frome Employee;
~~~
- Select every employee
~~~
select eSSN from Dependent where not exists (select SSN from Employee where SSN = eSSN);
~~~
- Returns eSSN from dependent if it is not found in the subquery
~~~
select * from Dependent INNER JOIN Employee ON eSSN = SSN;
~~~
![[innerjoin.png]]

~~~
select * from Dependent LEFT JOIN Employee ON eSSN = SSN;
~~~
![[leftjoin.png]]
~~~
select concat("Hello", "world") as output;
~~~
- Creates a table with "Hello World" as a data input
~~~
select * from Employee where DeptNumber=5;
~~~
- Select every employee working in dept. 5
~~~
select * from Employee where DeptNumber=5 OR DeptNumber=1;
~~~
- Select every employee working in both dept 5 and dept 1
~~~
select Fname, Lname from Employee;
~~~
- PROJECT the first and last name from employee
- Includes duplicates
~~~
Select distinct DeptNumber from Employee;
~~~

^fb482a

- Project DeptNumber from Employee
- `distinct` removes duplicates
~~~
select concat(Fname, ' ', Lname) from Employee 
~~~
- Combines display of attributes
~~~
select concat(Fname, ' ', Lname) as 'Full Name' from Employee 
~~~
- Combines display of attributes with renamed column name
~~~
select concat(Fname, ' ', Lname) as 'Full Name', Fname as 'First', Lname as 'Last' from Employee 
~~~
- Combines display of attributes and also displays them separately
~~~
select e.* from Employee as e;
~~~
- Rename Employee as table "e", useful for binary operations unto itself
~~~
select * from (select * from Employee where DeptNumber=5) as d5 UNION (select * from Employee where DeptNumber=1);
~~~
- Select all from a set of subqueries
- Must have one subquery given an alias to differentiate
- UNION takes the union of both subqueries
~~~
select * from Employee where DeptNumber=5 INTERSECT
select * from Employee where Fname = 'John';
~~~
- Displays the INTERSECT of results present in both
~~~
select * from Employee except select * from Employee where DeptNumber = 5;

EQUIVALENT TO

select * from Employee where DeptNumber !=5;
~~~
- `except` takes all the results from left query and subtracts from it the results from the right query
~~~
select SSN from Employee EXCEPT (select eSSN from WorksOn);
~~~
- Select all the employees but subtract from the list everyone who works on a project
- Will show all employees not working on a project
- SET DIFFERENCE
~~~
select SSN, Number FROM Employee JOIN Project;
~~~
- Shows all possible combinations of employees working on projects
- Some real, some not
- Good for relational division
~~~
select * Employee, WorksOn WHERE SSN=eSSN
~~~
- Combines rows from Employees with WorksOn where the SSN=eSSN
~~~
select * from Employee JOIN WorksOn ON SSN=eSSN;
~~~
- Same as above, better performance
- Doesn't perform a cross join then filters, simply filters initially
~~~
select FName, LName, Name, Hours
FROM Employee JOIN WorksOn ON SSN=eSSN
JOIN Project ON PNumber = Number;
~~~
![[query1.png]]
- Combines Employee, WorksOn, and Project
- Immediately follow first `JOIN` with second `JOIN`
- WorksOn connects everything, hence join order of Employee -> WorksOn -> Project
~~~
select FName, LName, Name, Hours
FROM Employee JOIN WorksOn ON SSN=eSSN
JOIN Project ON PNumber = Number
WHERE Hours>10;
~~~
- Same as above with additional condition
~~~
select FName, LName, Name, Hours
FROM Employee JOIN WorksOn ON SSN=eSSN
JOIN Project ON PNumber = Number
ORDER BY Hours DESC, Name;
~~~
- Descending order by Hours and Name
- Hours will take precedent, but Name will fill in after
~~~
select FName, LName, Name, Hours
FROM Employee JOIN WorksOn ON SSN=eSSN
JOIN Project ON PNumber = Number
ORDER BY Hours DESC, Name ASC;
~~~
- Same as above, with Name ascending
~~~
select * from Employee RIGHT JOIN Dependent ON SSN = eSSN;
~~~
- Include all dependents whether or not they have an employee
- Switch to left join to include all employees whether or not they have dependents

# **Natural Joins, SQL Division, Temporary Tables, and Aggregate Functions**
Taken from Week 09 Wednesday

~~~
select * from Department Natural join DepartmentLocations;
~~~
- Convenience
- `NATURAL JOIN` based on columns with same name and data types
~~~
SELECT DISTINCT sudentID, courseID
FROM taken
WHERE courseID IN (SELECT courseID from required);
~~~
- Lists all students who have taken a required course for graduation
~~~
//Step 1
CREATE TEMPORARY TABLE AllStudents as 
SELECT DISTINCT studentID
FROM taken;

select * from AllStudent;

//Step 2
select * from AllStudent JOIN required;

CREATE TEMPORARY TABLE StudentsAndRequired as
select * from AllStudent JOIN required;

//Step 3
CREATE TEMPORARY TABLE MissingCourses AS
select * from StudentsAndRequired
where NOT EXISTS (
select * from taken
where StudentsAndRequired.studentID = taken.studentID
AND StudentsAndRequired.courseID = taken.courseID
);
// I want rows from crossjoin IF the row isnt found in taken


//Step 4
select * from MissingCourses ;
CREATE TEMPORARY TABLE CannotGraduate AS
select distinct studentID FROM MissingCourses;
select * from CannotGraduate;

//step 5
select * from student
where studentID NOT IN (select studentID from cannotGraduate);
// Student cannot exist in subquery which lists those who // can't graduate
~~~
- *USING RELATIONAL DIVISION*
- 1. Find all students
- 2. Find all students and the courses required to graduate
- 3. Find all students and the required courses that they have not taken
- 4. Find all students who can not graduate
- 5. Find all students who can graduate

~~~
select count(*) student; 
~~~
- Counts rows from student table
~~~
select studentID, COUNT(courseID) AS CourseCount
from taken
where courseID IN (select courseID from required)
group by studentID;
~~~
- Example of determining which students are eligible for graduating
- WHERE occurs BEFORE the count occurs!
```
//Scenario: We need to select students who qualify for //graduating (aka where coursecount = 2)

//Hardcoded Version:
select studentID, COUNT(courseID) AS CourseCount
from taken
where courseID IN (select courseID from required)
group by studentID
HAVING CourseCount = 2;

//Better:
select studentID, COUNT(courseID) AS CourseCount
from taken
where courseID IN (select courseID from required)
group by studentID
HAVING CourseCount =  (select count(*) from required);
//Note: There are only 2 courses in the required table, which //is why this works
```
Resulting Table:
![[studentquery2.png]]
### Making a subquery display a table of our choosing
```
//We want to show the row with all the student information for //the one returned above...

select student.
from student join
(
select studentID, COUNT(courseID) AS CourseCount
from taken
where courseID IN (select courseID from required)
group by studentID
HAVING CourseCount =  (select count(*) from required);
) as readyToGraduate
on student.studentID = readyToGraduate.studentID;
//NOTE: on student.studentID = readyToGraduate.studentID; is //ultimately unecessary, but is an example of using a //condition to join
```
- 1) Wrapped previous query to make it a subquery
- 2) Give it an alias
- 3) Join it with another table
- Key: Give a query an alias so you can use a join condition on it
Resulting Table:
![[studentquery1.png]]

### **Populating with random numbers**
-> update Employee set DeptNumber = (select Number from Department order by rand() limit 1);



update Players set playerBank = (rand() * 10000);