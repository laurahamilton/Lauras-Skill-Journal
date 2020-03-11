# Blog 6 - March 13, 2020
## Using Debian to Set Up a Web Developer Environment

<p align="center"> <img width="500" height="200" src="debian.png"> </p>

I am using Debian 9. Debian is also known as Debian GNU/Linux. It is a Linux distribution composed of free and open-source software, developed by the community-supported Debian Project. Every Debian release has long term support for about 5 years. Derivative distros as Ubuntu, Elementary, or Mint, use "apt" or packages ".deb," so the commands posted here can also work for such distros.

***note: I have Debian 9 already installed in a virtual machine. ***

An excellent developer environment needs:
- An IDE or good text editor
- version control system
- the programming languages for you to develop
- some useful tools and utilities

Since this is for web development, here is what you will need:
- vscode: text editor, along some extensions for web development
- git: version control standar, and some git functions, aliases and tricks
- nodejs: JavaScript runtime
- some useful tools for web developers: (screenshots, GIMP, VLC, Slack, and teamViewer)

Prerequisites: 
Have sudo access ready. This command must return "root" :
```
sudo whoami
```

Check if system is updated:
```
sudo apt update
sudo apt upgrade
```

When you are ready, install these packages:
```
sudo apt install make curl build-essential openssl libssl-dev unzip
```

- A Nice Editor: Visual Studio Code

<p align="center"> <img width="200" height="200" src="vscode.png"> </p>

```
sudo apt install ./code_x.xx.x-xx_amd64.deb
```

"Apt" will auto magically set up everything and when all is finished you can open vcsode from your terminal (typing "code"), menu or launcher. Once it is installed, make sure to look at extensions for VS Code.

- A Standard Version Control System: Git
<p align="center"> <img width="400" height="200" src="git.png"> </p>

Git is super easy to install: 

```
sudo apt install git
git --version
```

You will be greeted with your git's version. First, identify yourself. For this you need to create an account. Provide your name and email address. Git embeds this information into each commit we do, like this:

```
git config --global user.name "Your Name"
git config --global user.email "your.name@domain.com"
```

Remember to put your name (or nickname) and your email. Now you should have Git installed and ready to use on your system. 

1. Git Alias (for Commands): https://github.com/GitAlias/gitalias
2. Undoing/Removing Commits in Git: https://sethrobertson.github.io/GitFixUm/fixup.html
3. Git Online Book: https://git-scm.com/book/en/v2

- Developing for JavaScript: Install NodeJS

<p align="center"> <img width="500" height="200" src="nodejs.jpg"> </p>

Most people find it difficult to install node in Windows in comparison to install it on Linux. This is usually one of the reasons developers turn to Linux as a new primary OS. Web programming usually involves NodeJS. Better sooner than later.

To install the current LTS version (other versions here) you must use your terminal:

```
curl -sL https://deb.nodesource.com/setup_12.x | sudo bash -
sudo apt install -y nodejs
```

1. Get started with NodeJS: https://www.w3schools.com/nodejs/nodejs_get_started.asp

Now you can use VSCode for making scripts and code to run over NodeJS, save it in a repository using Git and share it!

# Blog 5 - March 6, 2020
## How a C Program Translates into Assembly

<p align="center"> <img width="600" height="300" src="C_Assembly_Binary.png"> </p>

For developers that work with high-level languages, it is important to know how a language is translated from high-level to assmebly-level and eventually to binary. This post will cover the basics of translating a C language program into an assembly language program. This kind of information is strictly ties compiler and ABI. For most of the compilers, ABI and instruction set architecture, they follow nearly the same rules. 

First off, a bit about function stack frames:

1. During function code execution, a new stack frame is created in stack memory to allow access to function parameters and local variables.
2. The complete information on stack frame size, memory allocation, returning from stack frame, is decided at compile time.
3. Before diving into assembly code you should be aware of two things: (a) CPU registers of x86 machine; (b) x86 assembly instructions: As this is a very vast topic & updating quite frequently, we will only see the instructions needed for our examples.

<p align="center"> <img width="600" height="300" src="general-purpose-registers.png"> </p>

<p align="center"> <img width="600" height="200" src="pointer-registers.png"> </p>

<p align="center"> <img width="500" height="300" src="segment-registers.png"> </p>

<p align="center"> <img width="600" height="300" src="index-registers.png"> </p>

We will consider the following example with its disassembly inlined to understand its different aspect of its working at machine level:

We will focus on a stack frame of the function "func()." But before analysing stack frame of it, we will see how the calling of function happens.

<p align="center"> <img width="600" height="300" src="c-assembly1.png"> </p>

- Function Calling

Function calling is done by call instruction of a particular line (Line 15) which is subroutine instruction equivalent to:

<p align="center"> <img width="500" height="100" src="c-assembly2.png"> </p>

Here, "call" stores the "rip+1" (not that +1 is just for simplicity, technically this will be substituted by the size of instruction) in the stack which is return address once call to "func()" ends. 

- Function Stack Frame

A function is divided into three parts:

1. Entry 
2. User Code
3. Exit

- Entry

As you can see in the instructions (lines 2 to 4) generated against the start bracket "{," it is setting up the stack frame for "func()." Line 2 is pushing the previous frame pointer into the stack and Line 3 is updating the current frame pointer with a stack end which is going to be a new frame start. "Push" is basically equivalent to:

<p align="center"> <img width="500" height="100" src="c-assembly3.png"> </p>

- Parameter Passing

"Func()" is stored in the "edi" register on Line 14 before calling the "call" instruction. If there is another argument, then it will be stored in a subsequent register or stack and the address will be used. Line 4 in "func()" is reserving space by pulling frame pointer (pointed by the "rbp" register) down by 4 bytes for the parameter "arg" as it is of type "int." Then the "mov" instruction will initialize it with a value store in "edi." This is how parameters are passed and stored in the current stack frame.

<p align="center"> <img width="500" height="500" src="c-assembly4.png"> </p>

- User Code / Allocating Space for Local Variables

Line 5 is reserving space for a local variable "a," again by pulling frame pointer further down by 4 bytes. The "mov" instruction will initialize that memory with a value of 5.

- Accessing Global and Local Static Variables

As you can see above, "g" is addressed directly with its absolute addressing because its address is fixed which lies in the data segment. This is not the case all the time. Here we have compiled our code for x86 mode, that’s why it is accessing it with an absolute address. In the case of x64 mode, the address is resolved using "rip" register which meant that the assembler and linker should cooperate to compute the offset of "g" from the ultimate location of the current instruction which is pointed by the "rip" register. The same statement stands true for the local static variables also.

- Exit

After the user code execution, the previous frame pointer is retrieved from the stack by the "pop" instruction which we have stored in Line 2. "Pop" is equivalent to:

<p align="center"> <img width="500" height="100" src="c-assembly5.png"> </p>

- Return from Function

The "ret" instruction jumps back to the next instruction from where "func()" called by retrieving the jump address from stack stored by the "call" instruction. "Ret" is a subroutine instruction which is equivalent to:

<p align="center"> <img width="100" height="100" src="c-assembly6.png"> </p>

If any return value specified then it will be stored in the "eax" register which you can see in Line 16.

# Blog 4 - February 28, 2020

## Creating Tables and Querying data with AWS DynamoDB

<p align="center"> <img width="600" height="300" src="dynamoDB.png"> </p>

Amazon Web Services offers a noSQL database known as DynamoDB. Its purpose is to serve as a hassle-free alternative to standard SQL databases since there are no servers to maintain, no backups to schedule and no problem scaling the database to millions of users. DynamoDB offers single millisecond latency and is a great choice to build RESTful APIs, IoT data stores and mobile backends. It is the usual choice for developers while working with other AWS services like Lambda. It also works well with most programming languages like Python, C++ and JavaScript through the AWS SDK. 

We will go through the steps of creating a table in DynamoDB and manipulate its data, as well as utlizing the search options. 

- Working with Tables

<p align="center"> <img width="600" height="300" src="dynamoDB_table.png"> </p>

Unlike MySQL, you do not have to create a database and then create tables within the database. DynamoDB offers a centralized database per account in which you can create any number of tables. First, login to your AWS account and search for "DynamoDB" in the "Find Services" textbox of the AWS Console. There you will select "DynamoDB" and take you to the dashboard. Let's create a new table.

Select "Create Table." You will be asked to enter a table name and a primary key. A primary key is used to uniquely identify a record in a table. DynamoDB allows you to create two types of primary keys:

1. Partition Key: A simple primary key that has the value of the partition in which the data is stored. If a partition key is used as the primary key, no two records can be in the same partition. eg. a unique name.

2. Composite Key: A composite key is a combination of a partition key and a sort value. This enables multiple records to be stored in a single partition with unique sort values within that partition eg. a unique name (partition key) + a member id (sort key)

After entering a table name and its primary key (with or without the sort key), select "Create." Your table should be created and you can start adding data into your DynamoDB table.

<p align="center"> <img width="600" height="300" src="dynamoDB_table1.png"> </p>

Select "Items," where all the items in your table are displayed. Let’s create a new item by selecting "Create Item." You will see the pop-up with the option of adding values to the fields that you have created. You can also add/remove new columns using the "+" icon.

<p align="center"> <img width="600" height="300" src="dynamoDB_table2.png"> </p>

Make sure to provide a unique primary key while adding records. If not, DynamoDB will return an error. Once you have created a few records, it is easy to update/delete records from your table. Select one or more records and then select "Actions" to edit or delete the records from the table.

<p align="center"> <img width="600" height="300" src="dynamoDB_table3.png"> </p>

- Searching (Scan and Query)

The main purpose of adding data to a database is to retrieve that data when needed. DynamoDB has two data retrieval methods: Scan and Query.

1. Scanning returns every record in the database. Scanning a table is useful when you try to work with an entire table and all the values contained in the table. However, scanning is very computing-heavy. If your table contains a large data set, you can easily run out of the free tier capacity offered by AWS.

2. Query is the other data retrieval method offered by DynamoDB. Query lets you use filters to select a range of data to be returned, making the operation more efficient compared to a Scan operation. Query is also similar to the data querying methods used in traditional SQL.

<p align="center"> <img width="600" height="300" src="dynamoDB_table4.png"> </p>

- Indexing

A database is never complete without the option of indexing data. Indexes are used to improve search performances of specific columns in a table by storing an additional searchable version of column(s). Even though indexes use additional space, they offer great improvements in performance. 

Creating an index in DynamoDB is easy. Select "Indexes," then select "Create Index." Choose the partition key and an optional sort key along with a name for the index. You can also choose the "Projected Attributes" which will only include the keys you choose while returning search results. Just remember that creating indexes can also include additional monthly costs in addition to the DynamoDB pricing! 

<p align="center"> <img width="600" height="300" src="create_index.png"> </p>

# Blog 3 - February 21, 2020

## Setting up AWS Infrastructure: EC2, EBS, and S3

<p align="center"> <img width="500" height="300" src="aws-services.png"> </p>

Amazon Web Services can get overwhelming with all the services offered for beginners. Some of the popular services includes Elastic Compute Clouds (EC2), Elastic Block Stores (EBS) and Simple Service Storage (S3) buckets. 

- First, we will create an S3 bucket. 

An S3 bucket provides object storage through a web service interface. Login to your AWS account and search for "S3." Click on "S3" and it will take you to the S3 page. Click on "Create Bucket" and create a bucket by specifying the region of your choosing. Give a unique name to your bucket. We can configure the bucket as per our requirement. If we want to keep multiple versions of the objects, we can enable versioning. There are different options available, for now we will keep everything unticked (default) and click "Next."

By default, S3 bucket blocks all public access to it. We will allow the public access to try out a few operations externally/publicly. To make the bucket public, untick "Block all public access" checkbox and click on "Next." Now that everything is set, click "Create the Bucket."

<p align="center"> <img width="900" height="300" src="aws s3 bucket.png"> </p>

- Second, we will create an EC2 instance.

An EC2 instance is a web service that provides secure, resizable compute capacity in the cloud. It is designed to make web-scale cloud computing easier for developers. Search for "EC2" at the top. Then select "Instance" from the side. You will then select "Launch Instance" to create a new EC2 instance. First, choose the machine image. Then, choose EC2 instance types. Then, configure the instance and add storage.

<p align="center"> <img width="600" height="300" src="aws ec2 instance.png"> </p>

- Third, we will create an IAM Role to enable S3 access from an EC2 instance.

Search for "IAM" at the top. Identity and Access Management (IAM) enables you to manage access to AWS services and resources securely. Using IAM, you can create and manage AWS users and groups, and use permissions to allow and deny their access to AWS resources. Select "Roles" on the side and select "Create Role." Search for "AmazonS3" and select "AmazonS3FullAccess" to create the IAM role. Give the role a unique name and provide a small description. 

This role will be attached to the EC2 instance created earlier. Select the EC2 Instance and click on "Actions." Go to Instance settings and click on "Attach/Replace IAM Role option." Select the IAM role from the drop-down list and click on "Apply." The role has been attached successfully.

<p align="center"> <img width="600" height="300" src="aws IAM.png"> </p>

You will now need to login to your EC2 instance. The method I use is through PuTTY. Here is a tuturial on how to utilize PuTTY to access the EC2 instance: https://www.youtube.com/watch?v=bi7ow5NGC-U 

After successfully entering the EC2 instance, install AWS command line interface. Follow the tuturial here: https://linuxhint.com/install_aws_cli_ubuntu/

- Fourth, we will create and attach and 8G EBS volume to the instance created earlier. 

Make sure to format and mount the filesystem using ext4 or any formatting you’d like. Ideally you would want the EBS mount to automount on reboot and this can be done by modifying the fstab.

Step 1: Head over to EC2 > Volumes and create a new volume of your preferred size and type.

Step 2: Select the created volume, right click and select the "Attach Volume" option.

Step 3: Select the instance from the instance text box.

Step 4: Now, login to your ec2 instance and list the available disks using:

```
lsblk
```

Step 5: Check if the volume has any data using:

```
sudo file -s /dev/xvdf
```

If the above command output shows "/dev/xvdf: data", it means your volume is empty.

Step 6: Format the volume to ext4 filesystem  using:

```
sudo mkfs -t ext4 /dev/xvdf
```

Step 7: Create a directory of your choice to mount our new ext4 volume. I am using the name “newvolume”

```
sudo mkdir /newvolume
```

Step 8: Mount the volume to "newvolume" directory using:

```
sudo mount /dev/xvdf /newvolume/
```

Step 9: cd into newvolume directory 

By default on every reboot the  EBS volumes other than root volume will get unmounted. To enable automount, you need to make an entry in the /etc/fstab file.

Step 10: Back up the /etc/fstab file.

```
sudo cp /etc/fstab /etc/fstab.bak
```

Step 11: Open /etc/fstab file and make an entry in the following format.

```
device_name mount_point file_system_type fs_mntops fs_freq fs_passno
```

Step 12: Execute the following command to check id the fstab file has any error.

```
sudo mount -a
```

- Fifth, run an "aws s3 cp" command to copy either a newly created / existing file from the EC2 instance to the bucket created earlier. The command should be able to be executed without having to run "aws configure."

```
aws s3 cp test.txt s3://<mybucketname>/test2.txt
```

<p align="center"> <img width="600" height="300" src="ec2 info.png"> </p>

<p align="center"> <img width="600" height="300" src="execution on AWS.png"> </p>

<p align="center"> <img width="700" height="300" src="execution on CL.png"> </p>

# Blog 2 - February 14, 2020

## Setting up Atom as a Python IDE

<p align="center"> <img width="700" height="300" src="atom-python.png"> </p>

You can setup and maintain a python friendly development environment using Atom.  Developers typically want to reduce the amount of application switching as much as possible and create repeatable workflows. Atom was developed initially from with Github, making it open-source and extensible by nature. It has a strong community that provides support and additional plugin functionality.

**Note: I have Atom 1.40 installed and primarily use Python 3+ with it.**

<p align="center"> <img width="400" height="300" src="installing-atom.png"> </p>

- First, download and install Atom. https://flight-manual.atom.io/getting-started/sections/why-atom/

You can use Atom's UI to install the plug-ins listed below using Atom's Settings View: just type ctrl + (on Linux or a PC), or cmd + (on a Mac). 

You can also go to the "Settings View," you can click on the "Install" tab, and type the package name you want to install into the "Search Packages" search box.

- Download a Linter Package (Source Code Checker)

<p align="center"> <img width="500" height="300" src="linter.gif"> </p>

Linter is a base linter provider for the Atom Editor. You will install a specific linter for your language. Here is a link to a full list of Linter Packages: atomlinter.github.io. or https://atom.io/packages/linter.

Linters provide a top-level application programming interface to its user so that they can visualize errors and other types of messages with ease.

So, on the command line: `apm install linter`

Then, download a language specific linter plugin. (I will be using flake8)

```
pip install flake8
apm install linter-flake8
```

- Download a Python AutoComplete Package and AutoFormatter

<p align="center"> <img width="500" height="300" src="How-to-Setup-Atom-IDE-For-Python.jpg"> </p>

Python completions for packages, variables, methods, functions, with their arguments. You can either use Jedi or Kite. Since Jedi is a locally based library (and kite is online based), I will be using that one. 

```
apm install autocomplete-python
```

Use autopep8 (already installed) to format python code according to the pep8 (python's style guide) guidelines:

```
pip install autopep8
apm install python-autopep8
```

- Download a Script Package

<p align="center"> <img width="500" height="300" src="atom-script.png"> </p>

Run scripts based on the file name, a selection of code, or by the line number. This is VERY handy for testing your code from within Atom instead of going to a different terminal window. It is powerful because it is selection-based, which means you can run snippets instead of whole files.

```
apm install script
```

- Download a File-Icons Package

<p align="center"> <img width="500" height="400" src="file-icons.png"> </p>

File-icon packages are similar to the vs code-icons pack for Visual Studio Code (https://github.com/vscode-icons/vscode-icons) and built off of the logo-file-icons pack (https://atom.io/packages/logo-file-icons) with tweaks and additions.  

```
apm install atom-file-icons
```

- Download a Syntax Highlighting Theme

<p align="center"> <img width="500" height="300" src="atom-material-syntax.png"> </p>

This is a common syntax theme for Atom Material UI. Inspired by Mattia Astorino's SublimeText theme.

```
apm install atom-material-syntax
```

- Download and Install Minimap

<p align="center"> <img width="500" height="300" src="atom-minimap.png"> </p>


The Minimap package displays a Minimap preview of your file on the right hand side of your editor. The following plug-ins are also helpful: 

- minimap-git-diff 
- minimap-highlight-selected 

```
apm install minimap
apm install minimap-git-diff 
apm install minimap-highlight-selected 
```

- Install the Hydrogen Package

<p align="center"> <img width="300" height="300" src="atom-hydrogen.png"> </p>

Hydrogen runs code interactively, allows you to inspect data, and plot. It has all the power of Jupyter kernels inside Atom. It supports Python, R, JavaScript and other Jupyter kernels.

This post details what can be done with Hydrogen: https://nteract.gitbooks.io/hydrogen/

```
apm install hydrogen
```

- Using Version Control (or Cloud Drives) for Configuration Portability

Use version control for your Atom config file. You can find them, then host and clone your Git repository on Github, gitlab, Bitbucket, etc., and retrieve it on other computers simply by running:

```
git clone https://github.com/{username}/{repo}
```

As with any other source-control repository, you can then keep it up to date to upload changes and to download changes, aka git push
and git pull. To track a common list of installed packages, at the terminal shell execute:

```
apm list --installed --bare > ~/.atom/package.list
```

Add the file to you source-controlled Git repository also. To install the packages automatically, go back into a fresh installation of Atom:

```
apm install --packages-file ~/.atom/package.list
```

# Blog 1 - February 7, 2020

## Install WordPress on Ubuntu 18.04 Using a LAMP Stack

<p align="center"> <img width="500" height="500" src="wordpress-ubuntu.png"> </p>

Installing Wordpress on Ubuntu 18.04 allows you to have full control of the WordPress backend for experimentation and advanced uses. The LAMP stack will be used for your WordPress development environment on a VPS, or virtual private server, which is one of the most popular ways to host a service. 

To refresh, LAMP atands for Linux, Apache, MySQL, and PHP. Make sure you are logged in as root prior of starting the tuturial or follow each of the commands using sudo. You will also need to have access to your VPS using SSH. The UFW firewall ahould allow HTTP and HTTPS traffic. 

You can check by running the command: 
```
sudo ufw app list
```

- Install and Configure Apache:

First, install Apache web server 2. Press Y and "enter" to continue with the installation:
```
sudo apt update
sudo apt install apache2
```

Now there will be a list of available applications to choose from. We are going to select "Apache Full." To allow HTTP and HTTPS traffic (if it is not allowing it to pass through), you can execute the command below:
```
sudo ufw allow in "Apache Full"
```

To confirm the installation: 
```
sudo ufw app info "Apache Full"
```

To find your public IP address:
```
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

You will need your public IP address to verify that Apache was installed correctly. 

- Install MySQL:

Now that the webserver is running, we will install the MySQL database. Press Y and "enter" to continue with the installation:
```
sudo apt install mysql-server
```

Open MySQL:
```
sudo mysql
```

Set a password for the root user:
```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Password;
```

Reflect these changes using the flush command:
```
mysql> FLUSH PRIVILEGES;
```

Type "exit" to exit the MySQL prompt. 

- Install PHP:

Now we will install PHP to display dynamic content.
```
sudo apt install php libapache2-mod-php php-mysql
```

Install Additional PHP extensions for WordPress using the command below:
```
sudo apt install php-curl php-gd php-xml php-mbstring  php-xmlrpc php-zip php-soap php-intl
```

When you request a directory, the index.html is displayed as a default setting. In case you want to show index.php instead of index.html, you need to open the dir.conf file using the vi editor:
```
sudo vi /etc/apache2/mods-enabled/dir.conf
```

Swap the positions of index.html and index.php. Save the changes and exit the vi editor. For the changes to be visible, restart the Apache server using:
```
sudo systemctl restart apache2
```

To test the PHP, you can create a sample PHP file sample.php and add these lines of code. This file needs to be added to the Web Root of Apache which is located at – /var/www/html/. Once this is saved, you can try to access this page by using http://Your_Public_IP/Sample.php.

- MySQL Setup for WordPress:

Now, login to MySQL as the root user:
```
mysql -u root -p
```

Create a new database for Wordpress. Below we have created a new DB called WordPressDB:
```
mysql> CREATE DATABASE WordPressDB DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
```

Create a new user for Wordpress. We will name the user "WordpressUser"; "NewPasswordToBeSet" is in place of a strong password. Grant this user privilages: 
```
mysql> GRANT ALL ON WordPressDB.* TO ' WordPressUser '@'localhost' IDENTIFIED BY 'NewPasswordToBeSet';
mysql> FLUSH PRIVILEGES;
```

Exit the MySQL prompt. 

- Prepare to Install WordPress on Ubuntu:

Now, create a configuration file, for example :WordPress.conf. Place it to /etc/apache2/sites-available/. This will be a replica of the default configuration file which already exists in this location.

Also, create a WordPress directory (or you can provide any other name at location /var/www/). The complete location: /var/www/wordpress.

The file WordPress.conf will be the Apache configuration file for this testing. In the file, you can enable .htaccess by adding these lines to the VirtualHost block:

```
<Directory /var/www/wordpress />
AllowOverride All
</Directory>
```
Save the file. 

Enable mod_rewrite:
```
sudo a2enmod rewrite
```

In the file /etc/apache2/apache2.conf, you can change the ServerName directive by providing the server’s IP or hostname.
```
sudo apache2ctl configtest
```

To see all changes made, restart Apache: 
```
sudo systemctl restart apache2
```

- Configure and Install WordPress on Ubuntu

Now, go to this specific directory on Wordpress and extract the file: 
```
curl -O https://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
```

Create a .htaccess file and save the file. Rename the wp-config-sample.php file.
```
vi /tmp/wordpress/.htaccess
mv /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php
mkdir /tmp/wordpress/wp-content/upgrade
sudo cp -a /tmp/wordpress/. /var/www/wordpress
```

To ensure that everything works correctly, change the ownership of the WordPress files to the www-data users and groups. Those are the users that Apache web server will use.
```
sudo chown -R www-data:www-data /var/www/wordpress
```

Set the correct permissions: 
```
sudo find /var/www/wordpress/ -type d -exec chmod 750 {} \;
sudo find /var/www/wordpress/ -type f -exec chmod 640 {} \;
```

Add the Wordpress salt to be generated: 
```
curl -s https://api.wordpress.org/secret-key/1.1/salt/
```

Add this to the wp-config.php file: 
```
vi /var/www/wordpress/wp-config.php
```

Replace the DB_NAME, DB_USER, DB_PASSWORD with values you have created for WordPress. Save the file after making the changes.

Also, you can add the file system method at the very bottom: 
```
define('FS_METHOD', 'direct');
```

Save the file. This completes the backend setup. You can access WordPress via the interface by using the URL http://IP_Address.

How to Work with a VPS using PuTTY:
```
https://www.hostinger.com/tutorials/how-to-connect-to-your-account-using-putty
```

# Blog 0 - January 31, 2020

## Using Python 3.4 + to Work with Excel Spreadsheets

Excel is a popular and powerful spreadsheet application for Windows 10 (and earlier versions). The openpyxl module allows your Python programs to read and modify Excel spreadsheet files. If you have the boring task of copying certain data from one spreadsheet and pasting it into another one, you can use Python to do this for you. 

<p align="center"> <img width="500" height="300" src="python-excel.png"> </p>

**Note: If you do not have Excel, there are alternatives to open .xlsx files that work on Windows, OS X, and Linux - LibreOffice Calc and OpenOffice Calc!**

- Excel Documents

First, let’s go over some basic definitions: An Excel spreadsheet document is called a workbook. A single workbook is saved in a file with the .xlsx extension. Each workbook can contain multiple worksheets. The sheet the user is currently viewing is called the active sheet. Each sheet has columns (addressed by letters starting at A) and rows (addressed by numbers starting at 1). A box at a particular column and row is called a cell. Each cell can contain a number or text value. The grid of cells with data makes up a sheet.

- Installing the openpyxl Module

Open the Python idle, and run: `import openpyxl`

If the module was correctly installed, there should be no error messages. Remember to import the openpyxl module before running the interactive shell examples in this chapter, or you’ll get `NameError: name 'openpyxl' is not defined error. `

- Opening Excel Documents with OpenPyXL

Once you’ve imported the openpyxl module, you’ll be able to use the openpyxl.load_workbook() function. Enter the following into the interactive shell:
```
import openpyxl
wb = openpyxl.load_workbook('example.xlsx')
type(wb)
> <class 'openpyxl.workbook.workbook.Workbook'>
```
The openpyxl.load_workbook() function takes in the filename and returns a value of the workbook data type. This Workbook object represents the Excel file, a bit like how a File object represents an opened text file.

- Getting Sheets from the Workbook

You can get a list of all the worksheet names in the workbook by calling the get_sheet_names() method. Enter the following into the interactive shell:
```
import openpyxl
wb = openpyxl.load_workbook('example.xlsx')
wb.get_sheet_names()
'Sheet1', 'Sheet2', 'Sheet3'
sheet = wb.get_sheet_by_name('Sheet3')
sheet 
> <Worksheet "Sheet3">
type(sheet) 
> <class 'openpyxl.worksheet.worksheet.Worksheet'>
sheet.title 
> 'Sheet3'
```
Each sheet is represented by a Worksheet object, which you can find by passing the sheet name string to the get_sheet_by_name() workbook method. Also, you can read the active member variable of a Workbook object to get the workbook’s active sheet. Once you have the Worksheet object, you can get its name from the title attribute.

- Getting Cells from the Sheets

Once you have a Worksheet object, you can access a Cell object by its name. Enter the following into the interactive shell:
```
import openpyxl
wb = openpyxl.load_workbook('example.xlsx')
sheet = wb.get_sheet_by_name('Sheet1')
sheet['A1'] 
> <Cell Sheet1.A1>
sheet['A1'].value datetime.datetime(2015, 4, 5, 13, 34, 2)
c = sheet['B1']
c.value 
> 'Apples'
'Row ' + str(c.row) + ', Column ' + c.column + ' is ' + c.value
> 'Row 1, Column B is Apples'
'Cell ' + c.coordinate + ' is ' + c.value
> 'Cell B1 is Apples'
sheet['C1'].value
> 73
```
The Cell object has a value attribute that contains the value stored in that cell. Cell objects also have row, column, and coordinate attributes that provide location information for the cell.

Here, accessing the value attribute of our Cell object for cell B1 gives us the string 'Apples'. The row attribute gives us the integer 1, the column attribute gives us 'B', and the coordinate attribute gives us 'B1'.

- Converting Between Column Letters and Numbers

To convert from letters to numbers, call the openpyxl.cell.column_index_from_string() function. To convert from numbers to letters, call the openpyxl.cell.get_column_letter() function. Enter the following into the interactive shell:
```
import openpyxl
from openpyxl.cell import get_column_letter, column_index_from_string
get_column_letter(1)
> 'A'
get_column_letter(2)
> 'B'
get_column_letter(27)
> 'AA'
get_column_letter(900)
> 'AHP'
wb = openpyxl.load_workbook('example.xlsx')
sheet = wb.get_sheet_by_name('Sheet1')
get_column_letter(sheet.max_column)
> 'C'
column_index_from_string('A')
> 1
column_index_from_string('AA')
> 27
```
The function column_index_string() does the reverse: You pass it the letter name of a column, and it tells you what number that column is. You don’t need to have a workbook loaded to use these functions.

- Getting Rows and Columns from the Sheets

You can slice Worksheet objects to get all the Cell objects in a row, column, or rectangular area of the spreadsheet. Then you can loop over all the cells in the slice. Enter the following into the interactive shell:
```
import openpyxl
wb = openpyxl.load_workbook('example.xlsx')
sheet = wb.get_sheet_by_name('Sheet1')
tuple(sheet['A1':'C3'])
> ((<Cell Sheet1.A1>, <Cell Sheet1.B1>, <Cell Sheet1.C1>), (<Cell Sheet1.A2>,
> <Cell Sheet1.B2>, <Cell Sheet1.C2>), (<Cell Sheet1.A3>, <Cell Sheet1.B3>,
> <Cell Sheet1.C3>))
for rowOfCellObjects in sheet['A1':'C3']:
for cellObj in rowOfCellObjects:
print(cellObj.coordinate, cellObj.value)
print('--- END OF ROW ---')
```
Here is the result:
```
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
```

First, we specify that we want the Cell objects in the rectangular area from A1 to C3, and we get a generator object containing the Cell objects in that area. Use the tuple() on it to display its cell objects in a tuple (to help visualize the generator object).

This tuple contains three tuples: one for each row, from the top of the desired area to the bottom. Each of these three inner tuples contains the Cell objects in one row of our desired area, from the leftmost cell to the right. So, our slice of the sheet contains all the Cell objects in the area from A1 to C3, starting from the top-left cell to the bottom-right cell.

Next, to print the values of each cell in the area, we use two for loops. In line 5, the outer for loop goes over each row in the slice. Then, in line 6, the nested for loop goes through each cell for each row.
