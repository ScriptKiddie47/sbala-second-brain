
This lesson is designed as an exercise - Rather than a reference. Source - https://www.youtube.com/watch?v=8wUUMOKAK-c

#### Scenario

1. Lets say we are working on US Voter Registration.
2. Lets first have a table that contains State_Code,State_Name,State_Capital,Region.

```sql
CREATE TABLE US_States (
  State_Code varchar(2) PRIMARY KEY, -- NOT NULL + UNIQUE guaranteed
  State_Name varchar(255) UNIQUE, -- UNIQUE, but COULD be NULL
  State_Capital varchar(255),
  Region varchar(255)
);
```

1. When you designate a column as `PRIMARY KEY` - NOT NULL + UNIQUE guaranteed
2. As for `UNIQUE`, but COULD be NULL
3. Lets now populate the table

```sql
INSERT INTO US_States (State_Code, State_Name, State_Capital, Region)
VALUES
  ('FL', 'Florida', 'Tallahassee', 'South'),
  ('IL', 'Illinois', 'Springfield', 'Midwest'),
  ('PA', 'Pennsylvania', 'Harrisburg', 'Northeast'),
  ('OH', 'Ohio', 'Columbus', 'Midwest'),
  ('GA', 'Georgia', 'Atlanta', 'South'),
  ('NC', 'North Carolina', 'Raleigh', 'South'),
  ('MI', 'Michigan', 'Lansing', 'Midwest'),
  ('NJ', 'New Jersey', 'Trenton', 'Northeast'),
  ('VA', 'Virginia', 'Richmond', 'South'),
  ('WA', 'Washington', 'Olympia', 'West');
```

1. Lets now add a new table of 'Voters'
#### FK Constraint

```sql
CREATE TABLE Voters (
  Voter_Number INTEGER PRIMARY KEY,
  State_Of_Vote_Registration varchar(2) NOT NULL,
  FOREIGN KEY (State_Of_Vote_Registration) REFERENCES US_States(State_Code)
);
```

1. So if we try to add something like

```sql
insert into Voters(Voter_Number,State_Of_Vote_Registration)
values (1723,'FL');
```

1. This works as we are adhering the constraint. However if we try to do something like

```sql
insert into Voters(Voter_Number,State_Of_Vote_Registration)
values (1800,'MN');

SQL Error [23503]: ERROR: insert or update on table "voters" violates foreign key constraint "voters_state_of_vote_registration_fkey"
  Detail: Key (state_of_vote_registration)=(MN) is not present in table "us_states".
Error position:
```

#### A Bit about CASCADE

```sql
CREATE TABLE Voters (
  Voter_Number INTEGER PRIMARY KEY,
  State_Of_Vote_Registration varchar(2) NOT NULL,
  FOREIGN KEY (State_Of_Vote_Registration) REFERENCES US_States(State_Code)
    ON DELETE CASCADE
);
```

1. IF a state is deleted from `US_States`, all voters from that state are also deleted automatically

#### Inner Join

```sql
select 
	Voters.Voter_Number,
	US_States.State_Name as Registration_State_Name,
	US_States.State_Capital as Registration_Region
from Voters inner join US_States
on Voters.State_Of_Vote_Registration = US_States.State_Code;
```

1. Output:
 
```txt
voter_number|registration_state_name|registration_region|
------------+-----------------------+-------------------+
    1723    |     Florida           |   Tallahassee     |
```

