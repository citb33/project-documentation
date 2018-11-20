# Project - Architecture

![image](https://user-images.githubusercontent.com/29029753/48682558-d094c080-ebce-11e8-8cab-25f5d485599f.png)

## Stage1:

I stage1 we are going to setup everything in different server and the diagram looks like..

![image](https://user-images.githubusercontent.com/29029753/48682545-be1a8700-ebce-11e8-8c5b-106978915d23.png)


### Setup on server.

In the following setup we are going to take three different servers and we are going to setup each component on one server and understand the connectivity and application setup.

#### Step 1 : Create servers in AWS with the new AMI which we have created
#### Step 2 : MariaDB Service setup

Choose one server and login to it and execute the following commands.

```
$ sudo yum install mariadb-server -y
$ sudo systemctl enable mariadb
$ sudo systemctl start mariadb
```

If DB Installation is successful and you can go for loading schema

```
$ curl -s https://raw.githubusercontent.com/citb33/project-documentation/master/student-application.sql >/tmp/student-application.sql
$ sudo mysql < /tmp/student-application.sql
```

#### Step 2 : Application Server Setup

Choose one server for application setup and login to it and execute the following commands.

Usually in real setups we do run appliation with a dedicated app user. So on the same part you do require to create a dedicated user for our application.

```
$ sudo yum install java -y
$ sudo useradd student
```

Now you can setup the application inside application user. 
```
$ sudo su - student
$ wget -O- http://mirrors.wuchna.com/apachemirror/tomcat/tomcat-9/v9.0.13/bin/apache-tomcat-9.0.13.tar.gz | tar -xz
$ cd apache-tomcat-9.0.13
$ rm -rf webapps/*
$ wget https://github.com/citb33/project-documentation/raw/master/studentapp.war -O webapps/studentapp.war
```

You need to add the following content in `/home/student/apache-tomcat-9.0.13/conf/context.xml` just before the last line.

```
<Resource name="jdbc/TestDB" auth="Container" type="javax.sql.DataSource"
               maxActive="100" maxIdle="30" maxWait="10000"
               username="student" password="student@1" driverClassName="com.mysql.jdbc.Driver"
               url="jdbc:mysql://172.31.35.169:3306/studentapp"/>
```

##### `NOTE:` IP 172.31.35.169 might be different in your case.

You just now setup your application inside server and in order to access it from browser you need to start the service.

```
$ /home/student/apache-tomcat-9.0.13/bin/startup.sh
```



