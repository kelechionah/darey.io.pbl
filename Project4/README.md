AWS LEMP STACK IMPLEMENTATION

This project shows how to implement LEMP(Linux-Nginx-Mysql-Php) on AWS

I started with opening Git Bash and ran the following command to connect to my server

`ssh -i <Your-private-key.pem> ubuntu@<EC2-Public-IP-address>`


![Alt text](<images/lemp 1.PNG>)

STEP 1: INSTALLING NGINX WEBSERVER

Update a list of packages in package manager

`sudo apt update `

![Alt text](<images/lemp 2.PNG>)

Run Nginx package installation

`sudo apt install nginx`

![Alt text](<images/lemp 3.PNG>)

Verify Nginx is Up and running.

`sudo systemctl status nginx`

![Alt text](<images/lemp 4.PNG>)

Copy your public IP address from your AWS instance page, then you open a browser and input the public ip address and run it, you should get a welcome page from Nginx.

![Alt text](<images/lemp 5.PNG>)

STEP 2: INSTALLING MYSQL

Install MYSQL package using apt command

`sudo apt install mysql-server`

![Alt text](<images/lemp 6.PNG>)

Log into Mysql console.

`sudo mysql`

![Alt text](<images/lemp 7.PNG>)

We will run a security script that comes with Mysql to change some insecure default settings, but before then we are setting root password as shown below.

ALTER USER 'root'@'localhost' IDENTIFIED WITH `mysql_native_password BY 'PassWord.1'`

Exit Mysql. `exit`

Run the security with the command. `sudo mysql_secure_installation`

This will ask if you want to configure the VALIDATE PASSWORD PLUGIN, After the validation is completed, login to MYSQL and input the password by adding the -p flag.

`sudo mysql -p`

After succcessful confirmation of login, you can exit MYSQL

`exit`

![Alt text](<images/lemp 8.PNG>)

STEP 3: INSTALLING PHP

We need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, we need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

`sudo apt install php-fpm php-mysql`

![Alt text](<images/lemp 9.PNG>)

STEP 4: CONFIGURING NGINX TO USE PHP PROCESSOR

We will create a folder called projectLEMP for our webserver. Create the root web directory for your_domain in /var/www/ folder as follows:

`sudo mkdir /var/www/projectLEMP`

Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:

`sudo chown -R $USER:$USER /var/www/projectLEMP`

Open a new configuration file in Nginx’s sites-available directory

`sudo nano /etc/nginx/sites-available/projectLEMP`

![Alt text](<images/lemp 10.PNG>)

CONFIGURING THE NEW FILE:

![Alt text](<images/lemp 11.PNG>)

After editing, save and close the file. If you’re using nano, you can do so by typing CTRL+X and then y and ENTER to confirm.

Lets activate the configuration by linking the config file from Nginx’s sites-enabled directory, This will tell Nginx to use the configuration next time it is reloaded:

`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

Let's test the configuration is working.

`sudo nginx -t`

![Alt text](<images/lemp 12.PNG>)

We need to disable default Nginx host that is currently configured to listen on port 80, for this run:

`sudo unlink /etc/nginx/sites-enabled/default`

Let's reload NGINX to apply these changes.

`sudo systemctl reload nginx`

Create an index.html file in the location /var/www/projectLEMP so that we can test that your new server block works as expected:

sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html

![Alt text](<images/lemp 13.PNG>)

Now let's try to open your website URL from the browser using IP address or DNS name: If it opens, i means that The LEMP stack is now fully configured.

http://<Public-IP-Address>:80 or http://<Public-DNS-Name>:80

![Alt text](<images/lemp 14.PNG>)

STEP 5: TESTING PHP WITH NGINX

In this step, we’ll create a PHP script to test that Nginx is in fact able to handle .php files within our newly configured website.we can do this by creating a test PHP file in our document root. let's open a new file called info.php:

`sudo nano /var/www/projectLEMP/info.php`

we can now access this page in the web browser by visiting the domain name or public IP address set up in the Nginx configuration file, followed by /info.php:

`http://server_domain_or_IP/info.php`

![Alt text](<images/lemp 15.PNG>)

STEP 6: RETRIEVING DATA FROM MYSQL DATABASE WITH PHP

We will create a database named lemp_database and a user named lemp_user.

First, connect to the MySQL console using the root account and password:

sudo mysql -p

Let's create a new database and user.

mysql> CREATE DATABASE `example_database `;

Now you can create a new user and grant him full privileges on the database you have just created.

mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';

Now we need to give this user permission over the lemp_database database:

mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';

This will give the lemp_user user full privileges over the test_database database, while preventing this user from creating or modifying other databases on your server.

Let's exit the MySQL shell with: exit

Now we login to our example_database with the new example_user account created.

mysql -u test_user -p example_database

Let's confirm we have access to example_database.

mysql> SHOW DATABASES;

`mysql -u example_user -p`

![Alt text](<images/lemp 16.PNG>)

We’ll now create a test table named todo_list with the following statement:

 CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));

 Let's insert a few rows of content in the test table. we will repeat the next command a two times, using different VALUES: (you can repeat this as many times as you wish)

 INSERT INTO example_database.todo_list (content) VALUES ("My first item");
INSERT INTO example_database.todo_list (content) VALUES ("My second item");

To view the contents of our table, run the command:

`mysql> select * from test_database.todo_list;`

![Alt text](<images/lemp 17.PNG>)

we will exit MYSQL. exit

We will create a PHP script that will connect to MySQL and query the content. let's create a new PHP file in the custom web root directory.

`vi /var/www/projectLEMP/todo_list.php`

<?php
$user = "example_user";
$password = "PassWord.1";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}

![Alt text](<images/lemp 18.PNG>)

Save and close vi with ctrl esc, :wq!, then press Enter.

Mow lets access the todo_list.php page from our web browser

http://<Public_domain_or_IP>/todo_list.php

![Alt text](<images/lemp 19.PNG>)

With this output like the picture above, it shows the php environment can successfuy interaxt with the mysql database.


------------------------------------>>>THANK YOU FOR WATCHING<<<------------------------------------
