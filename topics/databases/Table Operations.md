# TABLE OF CONTENTS
- Creating Tables
- Miscellaneous Commands and Definitions
- How to save a database
- How to alter a table

---
# Logging in
ssh user@artemis.cs.csub.edu
mysql -p 
use databasename;

---
# Examples of Creating Tables

create table Company(
-> coID int primary key auto_increment,
-> coName varchar(100),
-> city varchar(100)
-> );

create table Customer ( 
-> cuID int primary key auto_increment,
-> cuName varchar(100) not null,
-> email varchar(100) not null unique,
);

create table Software ( 
-> sID int primary key auto_increment,
-> coID int not null
-> sName varchar(255) not null,
-> Version varchar(20) not null,
-> PricePerDay decimal (14,2) unsigned,
-> foreign key (coID) references Company(coID)
);
*Unisgned specifies that it cannot be negative

create table Subscribes(
-> cuID int not null,
-> sID int not null,
-> start date not null,
-> end date,
-> primary key (cuID, sID, start)
-> foreign key (cuID) references Customer(cuID),
-> foreign key (sID) references Software(sID)
);
*Example of multiple PK's*

---
# Miscellaneous Definitions and Descriptions
`describe table;`
	Query Command
	Describes the table
`auto_increment`
	Attribute Flag
	Handles uniqueness
	ALTER TABLE table AUTO_INCREMENT = value;
	ex. ALTER TABLE Company AUTO_INCREMENT = 8;
`varchar`
	Attribute Type
	Variable length strings
`show create table;`
	Query Command
	Shows the statement that created the table
`drop table Company;`
	Query Command
	Drops table
`unique`
	Attribute Flag
	Can be used on attributes that aren't even primary keys
`FOREIGN_KEY_CHECKS=0`
	Disables foreign key checks
	Set to 0 to disable, 1 to enable

---
# HOW TO SAVE (maybe)
outside of mariadb, do in artemis shell
-> mysqldump guilddb;
-> mysqldump guilddb -> guilddb.sql
-> source guilddb.sql

---
# ALTERING TABLES
-> alter table Employee add foreign key (DeptNumber) references Department(Number);

-> update Employee set SSN = '123-12-1234' where SSN = '321-32-3214'
```
ERROR: Cannot delete or update a parent row: A foreign key constraint fails...

Why? It is a parent row, meaning another row is referencing the row you are trying to change (foreign key).

How to Fix...
You must change every table. For example, in Employee the SSN is a FK in Dept, Dependent, and Employee so all those tables must change.

1) Try to drop FK in department and dependent

```

To drop a table, you need to disable foreign key constraint 
To view foreign key, `show create table`

# Updating 
`update table set attrib where attrib`
monday week 12 15:00
