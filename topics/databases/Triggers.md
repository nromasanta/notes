<u>Relevant Vids:</u>
Week 12 Monday
*Why Triggers?*
Triggers help us update data in a table that is used in another table.

## Important Commands
### Cascade
If the foreign key changes in the table it is taking from, then apply the same update to the table borrowing
```
-> alter table Department
-> add foreign key (ManagerSSN) REFERENCES Employee(SSN) 
-> on update cascade on delete set null;
```
- `on update cascade` does as expected
- `on delete set null` makes sure that the whole department doesn't disappear if the employee is deleted
- NOTE: `ON DELETE CASCADE` will delete entire rows

## Trigger for Inserting
CREATE TRIGGER trigger_name ON tbl_name FOR REACH ROW;

