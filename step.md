*Project Documentation.
_______________________________________________________________________________________________________________________________
project: Deploying Wordpress on AWS RDS.
_______________________________________________________________________________________________________________________________
Steps1: Lauching EC2 instance, installation and configuration of Apache web server and PHP.

1) login into your AWS management console.
   Click on EC2 instances and select launch instances.
   - Enter a unique name to be for your instance for identification
     select an AMI --for this demo i used Ubuntu 20.0
     select t2 micro as your instance type.
     Create a key pair for SSH -- i used .ppk for my putty.. you can use .pem for CLI
-
  - For the Networking section select create security group. (inbound rule)
    allow SSH traffic from your instance IP -- to allow only SSH from our current IP
    allow all HTTP access from all IP address -- for them to view your wordpress site with the brower 
Click on launch instance.
________________________________________________________________________________________________________________________________
2) SSH into your instance. for CLI clint like ubuntu. 

 navigate to the directory at which your .pem file is located and SSH.
 $ ssh -i <.pem-file> ubuntu@ip address
 successful login ubuntu host.

  - For Putty clint.
    open your putty:
    host name: ubuntu@ip address
    under connection select the + sign at the ssh 
    and under authentication select credentials.. 
    click on browse and select your .ppk file
    then select open.
    Succefully login in @ubuntu host
__________________________________________________________________________________________________________________________________
 3) installation of apache web server and PHP
    $ sudo su -- login root user
    $ apt update
    $ apt upgrade -y
  # installing apache2 web server
    $ apt install apache2 -y
  # installing PHP and dependencies.
    $ apt install -y php
    $ apt install -y php php-{pear,cgi,common,curl,mbstring,gd,mysqlnd,bcmath,json,xml,intl,zip,imap,imagick}
__________________________________________________________________________________________________________________________________

Step 2: creating RDS and connection with EC2 instance

1) From your console select 
   - RDS and click on create database.
     select Standard create and pick MySQL as you database Engine.
     For the Template select free tier.
     Enter a unique database instance identifier -- name
     Enter Master user-name
     Enter Master password

 Please Note: this credential are essencial for creating and establishing a connection with your Wordpress App 
    hosted on your EC2 instance.
    it is advisable to save credential in a notepad for later reference.

    you can leave the rest in default it will be edited after database instance creation.
    click on addional configuration.
    Enter the initial database name specified above
    Finally click on create database
___________________________________________________________________________________________________________________________________
2) On the database dashboard 
   click on your database.
   click on configuration and copy your database endpoint
   click on your database security group and edit inbound traffic.
   Allow Auora/MySQL  to access traffic from your EC2 instance security group.
   then click on save changes.

   - Also click on connection with instance and sectect your EC2 instance.
__________________________________________________________________________________________________________________________________

3) installation of MySQL clint and create a user
    $ apt install -y mysql-server mysql-common

   # loging MySQL
     $ mysql -h <RDS_endpoint> -u <username> -p   
     $             --- you will be prompted to enter your password.

     successfully login MySQL database.

  - Use this commands below to create user and confiqure database
    CREATE DATABASE wordpress;
    CREATE USER 'wordpress-user' IDENTIFIED BY 'password';
    GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress-user';
    FLUSH PRIVILEGES;
    EXIT;
__________________________________________________________________________________________________________________________________
Step3: installation of wordpress and configuration
   #install wordpress and unzip
    $ wget https://wordpress.org/latest.tar.gz
    $ tar xzvf latest.tar.gz
    $ cd wordpress
    $ cp wp-simpleconfig.php wp-config.php --- change wp-config file name
   #copy wordpress and move to your /var/www/html/ folder
    $ cp -r wordpress/* /var/www/html/
   #vi and edit the wp-config.php file with your credentials
    $ vi /var/www/html/wp-config.php
 Note: Hostname is your database endpoint.
    save and quit.
   #Change the ownership of the WordPress files to the www-data user:
    $ chown -R www-data:www-data /var/www/html/
    $ systemctl restart apache2 -- restart apache server
___________________________________________________________________________________________________________________________________
   #check the endpoint using the ip on the web-browser.
   if Apache ubuntu default page still persist. 
    $ rm -rf /var/www/html/index.html --- to remove default apache ubuntu page.

Your wordpress site is ready to be configured now.
login into your wp-admin with your credentials.

___________________________________________________________________________________________________________________________________
*THE END

