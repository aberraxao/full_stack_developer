# SQL Practice Exercises

## Manipulating & Querying Data
1. Insert a few records into both drivers and vehicles. Include 3 records of drivers who have vehicles, belonging in the vehicles table.
```sql
create table drivers (
  id serial primary key,
  first_name varchar,
  last_name varchar
);

create table vehicles (
  id serial primary key,
  make varchar,
  model varchar,
  driver_id integer references drivers(id)
);

INSERT INTO drivers (first_name, last_name) VALUES ('Sarah', 'Hua');
INSERT INTO drivers (first_name, last_name) VALUES ('Maria', 'Oliveira');
INSERT INTO drivers (first_name, last_name) VALUES ('Manuel', 'Oliveira');

INSERT INTO vehicles (make, model, driver_id) VALUES ('Renault', 'Cio', 3);
INSERT INTO vehicles (make, model, driver_id) VALUES ('Seat', 'Ibiza', 3);
INSERT INTO vehicles (make, model, driver_id) VALUES ('Dacia', 'Sandero', 2);
INSERT INTO vehicles (make, model, driver_id) VALUES ('Renault', 'Megane', 1);
```

2.1. Select all driver records
```sql
SELECT * FROM drivers;
```
2.2 Select all vehicle records
```sql
SELECT * FROM vehicles;
```
2.3. Select only 3 vehicle records (using LIMIT)
```sql
SELECT * FROM vehicles LIMIT 3;
```
3. Driver with ID 2 no longer owns any vehicles. Update the database to reflect this.
```sql
UPDATE vehicles SET driver_id = DEFAULT WHERE driver_id = 2;
```
4. Driver with ID 1 now owns a new vehicle in addition to the previous one they owned. Update the database to reflect this.
```sql
INSERT INTO vehicles (make, model, driver_id) VALUES ('Fiat', 'Punto', 1);
```

## Joins & Group Bys

1. Select all vehicles owned by driver with ID 3.
```sql
SELECT *
 FROM vehicles
 WHERE driver_id = 3;
```
2. Select all vehicles owned by driver with name 'Sarah' (without knowing their ID).
```sql
SELECT vehicles.*
 FROM vehicles
 INNER JOIN drivers ON drivers.id = vehicles.driver_id
 WHERE drivers.first_name='Sarah';
```
3. Show a table of the number of vehicles owned per driver.
```sql
SELECT drivers.first_name, COUNT(vehicles.driver_id)
 FROM vehicles
 INNER JOIN drivers ON drivers.id = vehicles.driver_id
 GROUP BY drivers.first_name;
```
4. Show the number of drivers that own a Nissan model.
```sql
SELECT Make, COUNT(Make)
 FROM vehicles
 GROUP BY Make
 HAVING Make='Nissan';
```

## Structuring Data

1. Add information about vehicle color.
```sql
ALTER TABLE vehicles ADD COLUMN colour VARCHAR;
```
2. Update all existing vehicle records to have a vehicle color.
```sql
UPDATE vehicles SET colour = 'red' WHERE Make = 'Seat';
UPDATE vehicles SET colour = 'blue' WHERE Make <> 'Seat';
```
3. Add contact information (email, address) to the drivers table.
```sql
ALTER TABLE drivers ADD COLUMN email VARCHAR, ADD COLUMN address VARCHAR;
UPDATE drivers SET email = 'manuel@email.com', address='Lisbon'
 WHERE first_name = 'Manuel';
UPDATE drivers SET email = 'maria@email.com', address='Coimbra'
 WHERE first_name = 'Maria';
```

## Challenges - Using Timestamps
1. Update vehicles table to show date of registration information
```sql
ALTER TABLE vehicles ADD COLUMN register_date TIMESTAMP;
```
2. The DMV is looking to notify all drivers with a vehicle that needs their registration renewed in the next month. If vehicles need to renew their vehicles once every year after their date of registration, then write a query to fetch all drivers with at least 1 vehicle that has an upcoming renewal in the next month, fetching their contact information as well as information about which vehicles need renewals. The DMV would like to run this query every time they need to contact all drivers that have an upcoming renewal in the next month.
```sql
UPDATE vehicles SET register_date = '2020-04-10' WHERE Make = 'Renault';
UPDATE vehicles SET register_date = '2020-04-22' WHERE Make = 'Seat';
UPDATE vehicles SET register_date = '2019-06-22' WHERE Make = 'Dacia';
UPDATE vehicles SET register_date = '2021-06-22' WHERE Make = 'Fiat';

SELECT drivers.*, vehicles.ID AS vehicle_id
 FROM vehicles
 INNER JOIN drivers ON drivers.id = vehicles.driver_id
 WHERE vehicles.register_date > NOW() AND vehicles.register_date <= NOW() + '30 day';
```
