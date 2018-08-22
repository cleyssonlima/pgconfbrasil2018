# Scripts presented in PGConfBrasil.2018 #
=============================================


### INSTALL mysql_fdw ###

https://github.com/EnterpriseDB/mysql_fdw

### INSTALL mongo_fdw ###

https://github.com/EnterpriseDB/mongo_fdw

### IMPORT DATA employees IN MySQL ###

https://github.com/datacharmer/test_db

### IN PostgreSQL ###

CREATE DATABASE employees;

\c employees;

CREATE EXTENSION mysql_fdw;

CREATE SERVER mysql_localhost
     FOREIGN DATA WRAPPER mysql_fdw
     OPTIONS (host '127.0.0.1', port '3306');

CREATE USER MAPPING FOR postgres
SERVER mysql_localhost
OPTIONS (username 'usr_employees', password 'P4SW0@Rd3');

CREATE FOREIGN TABLE titles(
     emp_no int,
     title varchar(50),
     from_date date,
     to_date date)
SERVER mysql_localhost
     OPTIONS (dbname 'employees', table_name 'titles');

CREATE FOREIGN TABLE salaries(
     emp_no int,
     salary int,
     from_date date,
     to_date date)
SERVER mysql_localhost
     OPTIONS (dbname 'employees', table_name 'salaries');

CREATE TYPE gender AS ENUM ('M','F');

CREATE FOREIGN TABLE employees(
     emp_no int,
     birth_date date,
     first_name varchar(14),
     last_name varchar(16),
     gender gender,
     hire_date date)
SERVER mysql_localhost
     OPTIONS (dbname 'employees', table_name 'employees');

CREATE EXTENSION redis_fdw;
  
CREATE SERVER redis_server 
     FOREIGN DATA WRAPPER redis_fdw 
     OPTIONS (host '127.0.0.1', port '6379');
 
CREATE USER MAPPING FOR PUBLIC
     SERVER redis_server
     OPTIONS (password 'secret');
	 
### IN MySQL AND PostgreSQL ###

SELECT DISTINCT employees.emp_no,
                employees.first_name,
                employees.last_name
FROM employees
INNER JOIN salaries ON salaries.emp_no = employees.emp_no
ORDER BY 1 LIMIT 10;

### IN MySQL ###

SELECT DISTINCT employees.emp_no,
                CONCAT (employees.first_name, ' ', employees.last_name) AS fullname
FROM employees
INNER JOIN salaries ON salaries.emp_no = employees.emp_no
LIMIT 10;

### IN PostgreSQL ###

SELECT DISTINCT emp.emp_no,
                emp.first_name || ' ' || emp.last_name AS fullname
FROM employees AS emp
INNER JOIN salaries sal ON sal.emp_no = emp.emp_no
ORDER BY 1 LIMIT 10 ;

CREATE FOREIGN TABLE red_employee(
      key  TEXT,
	  value TEXT
  ) SERVER redis_server
    OPTIONS (tabletype 'string');

INSERT INTO red_employee (key, value) SELECT DISTINCT emp.emp_no, 
                emp.first_name || ' ' || emp.last_name AS fullname
FROM employees AS emp
INNER JOIN salaries sal ON sal.emp_no = emp.emp_no
ORDER BY 1 LIMIT 5;

SELECT * FROM red_employee WHERE key = '10003';

### IN REDIS ###

MGET 10001 10002 10003 10004 10005


