---
layout: blog
title: 13-Python-MySQL-Operation
date: 2020-06-08 00:20:59
tags:
---

## 1. Install mysql on Ubuntu 18.04
```
$ sudo apt update
$ sudo apt install mysql-server 
```

check mysql status, stop, start, and restart mysql server

```
$ sudo systemctl status mysql 
$ sudo systemctl stop mysql
$ sudo systemctl start mysql
$ sudo systemctl restart mysql
```

## 2. Solved solution about: ERROR1698(28000):Access denied for user root@localhost

My operating system is ubuntu18.04, the following is my mysql version:

![1.png](1.png)

After the installation is complete, the following error occurs when logging into mysql:

![2.png](2.png)

Because the password was not set during the installation process, the password may be empty, but you can not enter mysql anyway.

Here is my process:

#### Step1: Modify the mysqld.cnf configuration file
Enter `sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf` on the terminal (that is, the terminal) of ubuntu, enter this configuration file, and then in the configuration part `[mysqld]`, add the phrase `skip-grant-tables`.

```
 1 [mysqld]
 2 #
 3 # * Basic Settings
 4 #
 5 user            　 = mysql
 6 pid-file        　 = /var/run/mysqld/mysqld.pid
 7 socket        　　 = /var/run/mysqld/mysqld.sock
 8 port            　 = 3306
 9 basedir        　　= /usr
10 datadir       　　 = /var/lib/mysql
11 tmpdir       　　　= /tmp
12 lc-messages-dir   = /usr/share/mysql
13 skip-external-locking
14 character-set-server=utf8
15 collation-server=utf8_general_ci
16 skip-grant-tables　　　　<-- add here
```

<b>Function</b>: It allows you to log in to mysql without a password.

<b>Save: wq, exit. </b>Enter: `service mysql restart`, restart mysql.

#### step2：设置root密码
Enter `mysql -u root -p` on the terminal, and press Enter when prompted to enter the password. After entering mysql, execute the following three sentences:
```
1 use mysql;   # then enter
2 update user set authentication_string=password("yourpassword") where user="root";  # then enter
3 flush privileges;  # then enter
```

see the picture below:
![3.png](3.png)
then input `quit`, exit mysql.

#### step3：Comments `skip-grant-tables`

reopen `mysqld.cnf`, comment `skip-grant-tables` 
```
 1 [mysqld]
 2 #
 3 # * Basic Settings
 4 #
 5 user            　 = mysql
 6 pid-file        　 = /var/run/mysqld/mysqld.pid
 7 socket        　　 = /var/run/mysqld/mysqld.sock
 8 port            　 = 3306
 9 basedir        　　= /usr
10 datadir       　　 = /var/lib/mysql
11 tmpdir       　　　= /tmp
12 lc-messages-dir   = /usr/share/mysql
13 skip-external-locking
14 character-set-server=utf8
15 collation-server=utf8_general_ci
16 # skip-grant-tables　　　　<-- add # here
```

Then return to the terminal and enter mysql -u root -p, you should be able to enter the database.

#### step4： Solve problem
If terminal still report an error at this time, you need to return to step3, re-enter the commented out statement (that is, delete the # symbol), re-enter mysql, first choose a database, such as use mysql;

Then enter select user, plugin from user; see the picture below:
![4.png](4.png)

It can be seen from the figure that after executing `select user, plugin from user;`, the cause of the error is because the field of the plugin root is auth_socket, then we can change it and replace it with `mysql_native_password`. Enter:
```
update user set authentication_string=password("ln122920"),plugin='mysql_native_password' where user='root';
```

Then press Enter to execute the following, and then input `select user, plugin from user;` + Enter, we can see that the root user field has been successfully changed.

![5.png](5.png)

Finally quit quit. Return to step3.

Then this problem is completely solved.

Reference:
[1]https://www.cnblogs.com/cpl9412290130/p/9583868.html

Expand: 
In the mysql8 version, the statement above to update the code seems to have changed, the syntax will be told to be wrong, here I post there is no syntax error:
```
ALTER user 'root'@'localhost' IDENTIFIED BY 'newpassward'; //newpassward 
```

Correspond to this sentence to Step2 above.

If `the MySQL server is running with the --skip-grant-tables option so it cannot execute this statemen` error,

Then the solution is as follows:

First `flush privileges`, and then execute the above statement to modify the password --Step2.

Under MacOS, because there is no mysql configuration file, we need to do this when we change the password. Taking my example, I installed mysql through brew and entered the mysql installation directory: cd /usr/local/Cellar/mysql @5.7/5.7.29/bin.

Step1: ./mysqld_safe --skip-grant-tables to disable the verification function of mysql.

Step2: mysql -u root This will enter the interactive window of mysql without a password.

Step3: FLUSH PRIVILEGES clear permissions

Step4: SET PASSWORD FOR'root'@'localhost' = PASSWORD('Your new password'); Set a new password.

This will modify/add the password successfully.


## 3. MySQL optimization 

## # Attention
 <b> Thread Pool is just provided in MySQL Enterprise. </b> 