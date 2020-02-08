# Blog 1 - Febuary 7, 2020

## How to Install WordPress on Ubuntu 18.04 Using LAMP Stack

<p align="center"> <img width="500" height="500" src="wordpress-ubuntu.png"> </p>

In this tutorial, I will show you how to install Wordpress on Ubuntu 18.04. By using this method, you will have full control of the WordPress backend for experimentation and advanced uses. The LAMP stack will be used for your WordPress development environment on a VPS, or virtual private server, which is one of the most popular ways to host a service. 

To refresh, LAMP atands for Linux, Apache, MySQL, and PHP. Make sure you are logged in as root prior of starting the tuturial or follow each of the commands using sudo. You will also need to have access to your VPS using SSH. The UFW firewall ahould allow HTTP and HTTPS traffic. You can check by running the command: 

- sudo ufw app list

1. Install and Configure Apache
First, install Apache web server 2. Press Y and "enter" to continue with the installation:

- sudo apt update
- sudo apt install apache2

Now there will be a list of available applications to choose from. We are going to select "Apache Full." To allow HTTP and HTTPS traffic (if it is not allowing it to pass through), you can execute the command below:

- sudo ufw allow in "Apache Full"

To confirm the installation: 

- sudo ufw app info "Apache Full"

To find your public IP address:

- ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'

You will need your public IP address to verify that Apache was installed correctly. 

2. Install MySQL
Now that the webserver is running, we will install the MySQL database. Press Y and "enter" to continue with the installation:

- sudo apt install mysql-server

Open MySQL:

- sudo mysql

Set a password for the root user:

- mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Password;

Reflect these changes using the flush command:

- mysql> FLUSH PRIVILEGES;

Type "exit" to exit the MySQL prompt. 

3. Install PHP
Now we will install PHP to display dynamic content.

- sudo apt install php libapache2-mod-php php-mysql

Install Additional PHP extensions for WordPress using the command below:

- sudo apt install php-curl php-gd php-xml php-mbstring  php-xmlrpc php-zip php-soap php-intl

When you request a directory, the index.html is displayed as a default setting. In case you want to show index.php instead of index.html, you need to open the dir.conf file using the vi editor:

- sudo vi /etc/apache2/mods-enabled/dir.conf

Swap the positions of index.html and index.php. Save the changes and exit the vi editor. For the changes to be visible, restart the Apache server using:

- sudo systemctl restart apache2

To test the PHP, you can create a sample PHP file sample.php and add these lines of code. This file needs to be added to the Web Root of Apache which is located at – /var/www/html/. Once this is saved, you can try to access this page by using http://Your_Public_IP/Sample.php.

- <?php phpinfo();?>

4. MySQL Setup for WordPress
Now, login to MySQL as the root user:

- mysql -u root -p

Create a new database for Wordpress. Below we have created a new DB called WordPressDB:

- mysql> CREATE DATABASE WordPressDB DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;

Create a new user for Wordpress. We will name the user "WordpressUser"; "NewPasswordToBeSet" is in place of a strong password. Grant this user privilages: 

- mysql> GRANT ALL ON WordPressDB.* TO ' WordPressUser '@'localhost' IDENTIFIED BY 'NewPasswordToBeSet';

- mysql> FLUSH PRIVILEGES;

Exit the MySQL prompt. 

5. Prepare to Install WordPress on Ubuntu
Now, create a configuration file, for example :WordPress.conf. Place it to /etc/apache2/sites-available/. This will be a replica of the default configuration file which already exists in this location.

Also, create a WordPress directory (or you can provide any other name at location /var/www/). The complete location: /var/www/wordpress.

The file WordPress.conf will be the Apache configuration file for this testing. In the file, you can enable .htaccess by adding these lines to the VirtualHost block:

- <Directory /var/www/wordpress />
-   AllowOverride All
- </Directory>

Save the file. 

Enable mod_rewrite:

-sudo a2enmod rewrite

In the file /etc/apache2/apache2.conf, you can change the ServerName directive by providing the server’s IP or hostname.

- sudo apache2ctl configtest

To see all changes made, restart Apache: 

- sudo systemctl restart apache2

6. Configure and Install WordPress on Ubuntu
Now, go to this specific directory on Wordpress and extract the file: 

- curl -O https://wordpress.org/latest.tar.gz
- tar xzvf latest.tar.gz

Create a .htaccess file and save the file. Rename the wp-config-sample.php file.

- vi /tmp/wordpress/.htaccess
- mv /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php
- mkdir /tmp/wordpress/wp-content/upgrade
- sudo cp -a /tmp/wordpress/. /var/www/wordpress

To ensure that everything works correctly, change the ownership of the WordPress files to the www-data users and groups. Those are the users that Apache web server will use.

- sudo chown -R www-data:www-data /var/www/wordpress

Set the correct permissions: 

- sudo find /var/www/wordpress/ -type d -exec chmod 750 {} \;
- sudo find /var/www/wordpress/ -type f -exec chmod 640 {} \;

Add the Wordpress salt to be generated: 

- curl -s https://api.wordpress.org/secret-key/1.1/salt/

Add this to the wp-config.php file: 

- vi /var/www/wordpress/wp-config.php

Replace the DB_NAME, DB_USER, DB_PASSWORD with values you have created for WordPress. Save the file after making the changes.

Also, you can add the file system method at the very bottom: 

- define('FS_METHOD', 'direct');

Save the file. This completes the backend setup. You can access WordPress via the interface by using the URL http://IP_Address.

How to Work with a VPS using PuTTY:
- https://www.hostinger.com/tutorials/how-to-connect-to-your-account-using-putty

# Blog 0 - January 31, 2020

## Using Python 3.4 + to Work with Excel Spreadsheets

Excel is a popular and powerful spreadsheet application for Windows 10 (and earlier versions). The openpyxl module allows your Python programs to read and modify Excel spreadsheet files. If you have the boring task of copying certain data from one spreadsheet and pasting it into another one, you can use Python to do this for you. 

<p align="center"> <img width="500" height="300" src="python-excel.png"> </p>

**Note: If you do not have Excel, there are alternatives to open .xlsx files that work on Windows, OS X, and Linux - LibreOffice Calc and OpenOffice Calc!**

- Excel Documents

First, let’s go over some basic definitions: An Excel spreadsheet document is called a workbook. A single workbook is saved in a file with the .xlsx extension. Each workbook can contain multiple worksheets. The sheet the user is currently viewing is called the active sheet. Each sheet has columns (addressed by letters starting at A) and rows (addressed by numbers starting at 1). A box at a particular column and row is called a cell. Each cell can contain a number or text value. The grid of cells with data makes up a sheet.

- Installing the openpyxl Module

Open the Python idle, and run: import openpyxl

If the module was correctly installed, there should be no error messages. Remember to import the openpyxl module before running the interactive shell examples in this chapter, or you’ll get " NameError: name 'openpyxl' is not defined error. "

- Opening Excel Documents with OpenPyXL

Once you’ve imported the openpyxl module, you’ll be able to use the openpyxl.load_workbook() function. Enter the following into the interactive shell:
1. import openpyxl
2. wb = openpyxl.load_workbook('example.xlsx')
3. type(wb)
> <class 'openpyxl.workbook.workbook.Workbook'>


The openpyxl.load_workbook() function takes in the filename and returns a value of the workbook data type. This Workbook object represents the Excel file, a bit like how a File object represents an opened text file.

- Getting Sheets from the Workbook

You can get a list of all the worksheet names in the workbook by calling the get_sheet_names() method. Enter the following into the interactive shell:
1. import openpyxl
2. wb = openpyxl.load_workbook('example.xlsx')
3. wb.get_sheet_names()
> 'Sheet1', 'Sheet2', 'Sheet3'
5. sheet = wb.get_sheet_by_name('Sheet3')
6. sheet 
> <Worksheet "Sheet2">
7. type(sheet) 
> <class 'openpyxl.worksheet.worksheet.Worksheet'>
8. sheet.title 
> 'Sheet2'

Each sheet is represented by a Worksheet object, which you can find by passing the sheet name string to the get_sheet_by_name() workbook method. Also, you can read the active member variable of a Workbook object to get the workbook’s active sheet. Once you have the Worksheet object, you can get its name from the title attribute.

- Getting Cells from the Sheets

Once you have a Worksheet object, you can access a Cell object by its name. Enter the following into the interactive shell:
1. import openpyxl
2. wb = openpyxl.load_workbook('example.xlsx')
3. sheet = wb.get_sheet_by_name('Sheet1')
4. sheet['A1'] 
> <Cell Sheet1.A1>
5. sheet['A1'].value datetime.datetime(2015, 4, 5, 13, 34, 2)
6. c = sheet['B1']
7. c.value 
> 'Apples'
8. 'Row ' + str(c.row) + ', Column ' + c.column + ' is ' + c.value
> 'Row 1, Column B is Apples'
9. 'Cell ' + c.coordinate + ' is ' + c.value
> 'Cell B1 is Apples'
10. sheet['C1'].value
> 73

The Cell object has a value attribute that contains the value stored in that cell. Cell objects also have row, column, and coordinate attributes that provide location information for the cell.

Here, accessing the value attribute of our Cell object for cell B1 gives us the string 'Apples'. The row attribute gives us the integer 1, the column attribute gives us 'B', and the coordinate attribute gives us 'B1'.

- Converting Between Column Letters and Numbers

To convert from letters to numbers, call the openpyxl.cell.column_index_from_string() function. To convert from numbers to letters, call the openpyxl.cell.get_column_letter() function. Enter the following into the interactive shell:

1. import openpyxl
2. from openpyxl.cell import get_column_letter, column_index_from_string
3. get_column_letter(1)
> 'A'
4. get_column_letter(2)
> 'B'
5. get_column_letter(27)
> 'AA'
6. get_column_letter(900)
> 'AHP'
7. wb = openpyxl.load_workbook('example.xlsx')
8. sheet = wb.get_sheet_by_name('Sheet1')
9. get_column_letter(sheet.max_column)
> 'C'
10. column_index_from_string('A')
> 1
11. column_index_from_string('AA')
> 27

The function column_index_string() does the reverse: You pass it the letter name of a column, and it tells you what number that column is. You don’t need to have a workbook loaded to use these functions.

- Getting Rows and Columns from the Sheets

You can slice Worksheet objects to get all the Cell objects in a row, column, or rectangular area of the spreadsheet. Then you can loop over all the cells in the slice. Enter the following into the interactive shell:

1. import openpyxl
2. wb = openpyxl.load_workbook('example.xlsx')
3. sheet = wb.get_sheet_by_name('Sheet1')
4. tuple(sheet['A1':'C3'])
> ((<Cell Sheet1.A1>, <Cell Sheet1.B1>, <Cell Sheet1.C1>), (<Cell Sheet1.A2>,
> <Cell Sheet1.B2>, <Cell Sheet1.C2>), (<Cell Sheet1.A3>, <Cell Sheet1.B3>,
> <Cell Sheet1.C3>))
5. for rowOfCellObjects in sheet['A1':'C3']:
6. for cellObj in rowOfCellObjects:
7. print(cellObj.coordinate, cellObj.value)
8. print('--- END OF ROW ---')

Here is the result:

> A1 2015-04-05 13:34:02
> B1 Apples
> C1 73
> --- END OF ROW ---
> A2 2015-04-05 03:41:23
> B2 Cherries
> C2 85
> --- END OF ROW ---
> A3 2015-04-06 12:46:51
> B3 Pears
> C3 14
> --- END OF ROW ---
   
First, we specify that we want the Cell objects in the rectangular area from A1 to C3, and we get a Generator object containing the Cell objects in that area. To help us visualize this Generator object, we can use tuple() on it to display its Cell objects in a tuple.

This tuple contains three tuples: one for each row, from the top of the desired area to the bottom. Each of these three inner tuples contains the Cell objects in one row of our desired area, from the leftmost cell to the right. So, our slice of the sheet contains all the Cell objects in the area from A1 to C3, starting from the top-left cell to the bottom-right cell.

Next, to print the values of each cell in the area, we use two for loops. In line 5, the outer for loop goes over each row in the slice. Then,in line 6, the nested for loop goes through each cell for each row.
