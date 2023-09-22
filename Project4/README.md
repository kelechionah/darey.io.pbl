Understanding CLIENT-SERVER ARCHITECTURE with MYSQL as DBMS

Client-Server refers to an architecture in which two or more computers are connected together over a network to send and recieve requests between one another.

The machine sending requests is usually referred to as CLIENT and the machine responding is called the SERVER

we can further introduce a database server to the CLIENT-SERVER achiteture where the web server connects to. In this case, the web server becomes the clients because it seeks request from the database server and the communication between them usually happens over local network (common practice)

Some common examples of database servers are MYSQL, MongoDB, Oracle, SQL etc

Here is a quick example of a Client-Server communication

Spin up Ubuntu and run the command `curl -Iv www.propitixhomes.com` In this example, your terminal is the Client while `www.propitixhomes.com` is the server.

![Alt text](<Images/csa 1.PNG>)

LETS ACTUALLY IMPLEMENT A CLIENT SERVER ARCHITECTURE USING MySQL DATABASE MANAGEMENT SYSTEM (DBMS)

1: Create and configure two Linux-based virtual servers named `mysql server` and `mysql client` (EC2 instances on AWS) 

![Alt text](<Images/csa 2.PNG>)

I configured the two servers on my terminal (i used termius)

![Alt text](<Images/csa 3.PNG>)

2: On `mysql server` i installed mysql server by running `sudo apt install mysql-server -y` On `mysql client`  i installed mysql client by running `sudo apt install mysql-client -y`

![Alt text](<Images/csa 4.PNG>)

3: Use `mysql server's` local IP address to connect from `mysql client` MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in 'inbound rules' in 'mysql server' security groups. For extra security, allow access only to the specific local IP address of your 'mysql client'

On my 'mysql server terminal' i ran the command `sudo mysql_secure_installion` to create a user and also set up my password to have access to my database. After this i ran `sudo mysql` to open and set mysql to grant all access to the user. Input the following 

 `mysql> CREATE USER 'remote_user'@'%' IDENTIFIED WITH mysql_native_password BY 'Fabulous@2020'; `
 
 `mysql> CREATE DATABASE test_db;` 
 
 `mysql> GRANT ALL ON test_db.* TO 'remote_user'@'%' WITH GRANT OPTION;`
 
 `mysql> FLUSH PRIVILEGES;`
 
 `mysql> exit;`

![Alt text](<Images/csa 5.PNG>)

4: Configure mysql server to allow connections from remote hosts by running the code `sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf` then edit and replace '127.0.0.1' with '0.0.0.0'

![Alt text](<Images/csa 6.PNG>)

5: From mysql client server, connect remotely to mysql server database engine without using SSH. use the mysql utilities to do this by running `sudo mysql -u remote_user -h 'IP'`

6: Check that you have successfully connected to a remote MySQL server and can perforn SQL queries

`mysql> Show databases`

![Alt text](<Images/csa 7.PNG>)

AT THIS POINT, YOU HAVE DEPLOYED A FULLY FUNCTIONAL MYSQL CLIENT-SERVER SET UP.