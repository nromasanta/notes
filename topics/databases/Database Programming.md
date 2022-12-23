|                   | Insert | Select | Update | Delete |
| ----------------- | ------ | ------ | ------ | ------ |
| Views             |        | X      |        |        |
| Stored Procedures | X      | X      | X      | X      |
| Triggers          | X      | X      | X      |        |

# DATABASE PROGRAMMING
# VIEWS
A view is a select statement query that is SAVED as a table
Good for creating tables pertinent for a certain user group
Good for hiding sensitive information


## Step 1: Devise a query
```
select CONCAT (Employee.Fname, " ", Employee.Lname) AS EmployeeName, Project.Name AS ProjectName, Hours from WorksOn natural join Employee natural join Project ON WorksOn.Pnumber=Project.PNumber;
```

![[view1 1.png]]
- Example of wanting to show a table without sensistive information (Employee SSN's not given)

## Step 2: Use CREATE VIEW to make a new table 
`CREATE VIEW table_name AS query`

ex.
```
CREATE VIEW EmployeeProjects AS
select CONCAT (Employee.Fname, " ", Employee.Lname) AS EmployeeName, Project.Name AS ProjectName, Hours from WorksOn natural join Employee natural join Project ON WorksOn.Pnumber=Project.PNumber;
```

## How to delete a view
`drop view IF EXISTS view_name;`
- IF EXISTS optional

# STORED PROCEDURES
Used to repeat a series of instructions/repeat logic with different parameters

Examples:
- logic to create a new account
- perform a query with certain parameters
- update rows
- delete a row (ex. with an employee, unassign them from projects, delete their dependents, etc.)

Invoked by CALL procedure;

**CREATE PROCEDURE**
`CREATE PROCEDURE store_procedure_name () routine_body`
- A procedure is considered *deterministic* if it runs with the same input and always gets the same output

**DELIMITER**
Stored procedure body needs to be able to differentiate between different commands

- Previously done with a `;`
- In stored procedures, the body is a SQL statement
	- If you add separate commanids with semicolons, it will simply stop after the first semicolon
- Must specify a new delimeter
	- ex. `DELIMETER //`
	- This is a good delimeter because you're unlikely to use `//` normally

### Procedure Example
```
# Comments will be preceded by a #

DROP PROCEDURE IF EXISTS RegisterUser;
DELIMETER //
CREATE PROCEDURE 'RegisterUser' (uname varchar(30), passhash text)
BEGIN
	START TRANSACTION; #since we're inserting data into a 
	# database, it is a transaction. If it fails, no changes 
	# are made
	
	SELECT COUNT(*) INTO @usernameCount
	FROM user
	WHERE username=uname;
	# This checks if another username exists already
	# Convention says to add a `@` into local variables 
	
	IF @usernameCount > 0 THEN
		SELECT NULL as userid, "Username already exists" AS 
		'Error';
	ELSE
		INSERT INTO user (username, password) VALUES (uname, 
		passhash);
		SELECT last_insert_id() AS userid, NULL as 'Error';
		# Check if it worked, select an auto-incrementing 
		value as the userid and NULL is the error aka no error
	END IF; # stop conditional logic
	
	COMMIT; # put in changes
END; 
// # This is all one execution, but it's time to end it

DELIMITER ; # We can switch back to the semicolon delimeter
```

### Read-Only Procedures
```
DROP PROCEDURE IF EXISTS GetEmployees;

DELIMETER //

CREATE PROCEDURE 'GetEmployees' ()
BEGIN
	SELECT *
	FROM Employee;
END;
//
DELIMETER;
```
Invoke with `CALL GetEmployees();`
### Read-Only w/ Parameter
```
DROP PROCEDURE IF EXISTS GetEmployees;

DELIMETER //

CREATE PROCEDURE 'GetEmployees' (deptName varchar(100))
BEGIN
	SELECT *
	FROM Employee JOIN Department ON Employee.DeptNumber = 
	Department.Number WHERE Department.Name = deptName;
	# LIKE performes case-insensitive substring matches
	# ex.
	# LIKE deptName; # placed after WHERE clause 
END;
//
DELIMETER;
```
Invoke with `CALL GetEmployees('Headquarters');`

### Procedure to Change Rows
```
DROP PROCEDURE IF EXISTS ChangeEmployeeDepartment;

DELIMETER //

CREATE PROCEDURE 'ChangeEmployeeDepartment' (essn int, deptName varchar(100))
BEGIN
	# Part 0 : Declare initial value for local var
	SET @deptID = -1;
	
	# Part 1: Find deptnumber based off name
	SELECT Number INTO @deptID
	FROM Department
	WHERE Name = deptName;
	
	if @deptID < 0 THEN
		SELECT CONCAT("No department by the name ", deptName, 
		 found") AS 'Result'; 
	else
		select DeptNumber INTO @currDeptID
		FROM Employee
		WHERE SSN = essn;
		if @currDeptID != @deptID
			# Part 2: Change Employee DeptNumber
			UPDATE Employee
			SET DeptNumber = @deptID
			WHERE SSN = essn;
			
			SELECT CONCAT("Changed departments for employee ", 
			essn ", now in dept ", @deptID) AS 'Result'; 
		else 
			select CONCAT("Error: employee ", essn, " is  
			already in dept ", @deptID);
		endif;
	end if;
END;
//
DELIMETER;
```
Invoke with `CALL ChangeEmployeeDepartment(123456789, 'Administration')`
1:01:12
**DROP PROCEDURE**
`DROP PROCEDURE IF EXISTS procedure_name`
- Usually want to do before calling a procedure

**SHOW CREATE PROCEDURE**


---
Stored procedure that returns data from tables


WEEK 11 WEDESDAY 45:00
WEEK 11 THURSDAY MOCK DATA