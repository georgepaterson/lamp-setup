# Linux Apache MySQL PHP setup

The LAMP stack is a very popular setup for a web server, it is relatively easy to create, leveraging open source software. Python or Perl may be used to replace PHP depending on specific requirements alternatively the AMP stack can be installed on alternative operating systems.

For this particular setup we will initially reference Red Hat Enterprise Linux / CentOS Linux version 5.x or above.

Your method of accessing the server will depend on it's location, either direct access or Secure Shell (SSH) for a remote server. If you have SSH access you should be able to skip sections 1 and 2. If you don't and the server is hosted remotely ask your support team to put your user on the sudoers list and give you SSH access.

## Section 1: Set up root access

By default we don't want to login as root to perform tasks but we do want to give our user account root access when needed. To give our user root access we need to add them to the sudoers list.

Login as root:

	$ su
	
Then edit sudoers list, using visudo if available:

	$ visudo
	
Or edit the file with nano:

	$ nano /etc/sudoers
	
Under the section
 
	## Allow root to run any commands anywhere
	root		ALL=(ALL)	ALL

Add

	username	ALL=(ALL)	ALL
	
Then if in visudo exit the file by entering `:wq` or use the standard exit path for your editor.

Finish using root and return to being your user:

	$ su username
	
You now have the ability to invoke root access by prepositioning any commands with the `sudo` key word.

Try using sudo by updating your server:

	$ sudo yum update

## Section 2: Secure Shell access

Secure Shell allows an authenticated user a secure remote connect to the server shell where they can perform a range of tasks.

First we should confirm if SSH is already installed.

	$ sudo yum info sshd

If SSH is not installed then begin installation with:

	$ sudo yum install openssh-server
	
When installed start the service with:

	$ sudo service sshd start
	
You can now remotely access the server shell using:

	$ ssh username@server.ip.address
	
All further commands can be used directly or remotely on the server shell.
	
## Section 3: Installing Apache, MySQL and PHP

Before installing Apache MySQL or PHP, you may want to check that they haven't already been installed.

	yum info httpd mysql php
	
This will return information on the specified installations.

If you don't have Apache MySQL or PHP plus extensions installed then this can be done with:

	yum install httpd mysql php php-gd php-mysql

With MySQL installed we need to create the MySQL root user:

	mysql -u root
	
	mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('new-password');
	
To load the MySQL PHP module we need to edit php.ini:
	
	sudo nano /etc/php.ini
	
In php.ini change: 	
	
	;extension=mysql.so
	
to

	extension=mysql.so

Apache, MySQL and PHP should now be ready to use.	
	
## Section 4: Permissions for site deployment

To deploy a web site to the server we will need to give our user permissions to write to the correct folders. The preferred method to do this is to create a group which the user can be attached to, the group can then be given the required folder permissions.

First check if the group already exists:

	cat /etc/group

To create a group called developers we can use:

	sudo groupadd developers

If groupadd is not available we can edit the group file using nano:

	sudo nano /etc/group
	
And create a new group at the end of the file:

	developers:x:groupid
	
To add the user to the group we can invoke usermod:	

	usermod -G developers username

If usermod is unavailable 

	sudo nano /etc/group
	developers:x:groupid:username

With the group created and the user added to the group we can then change file and folder owners to the developers group.

To edit the Apache config:

	sudo chown -R :developers /etc/httpd/
	
To edit the hosts entry:

	sudo chown -R :developers /etc/hosts
	
To upload the site to the default Apache web folder:

	sudo chown -R :developers /var/www/
	
We will also want to give write permissions to the owners of the files and folders:

	sudo chmod -R g+w /etc/httpd/
	sudo chmod -R g+w /etc/hosts
	sudo chmod -R g+w /var/www/
	
## Section 5: Create a site domain

We now want to create a site domain so that our deployed code can be viewed as a site on our server.

First we need to edit the host file so that domain names directed to the server are recognised:

	sudo nano /etc/hosts
	
At the end of the hosts entries add your domain name:

	127.0.0.1       your-site.tld
	
The .tld can be .com, net or if this is for internal development it can be of your choice like .local.

With the host entry now set we will want Apache to do something with the entry, we need to edit the virtual hosts setting for Apache:

	sudo nano /etc/httpd/conf/httpd.conf

Under Section 3: Virtual Hosts add a new virtual host:

	<VirtualHost *:80>
	    ServerName your-site.tld
	    DocumentRoot "/var/www/your-site"
	    <Directory "/var/www/html/your-site">
	        Options Indexes FollowSymLinks +Includes ExecCGI
	        AllowOverride All
	        Order allow,deny
	        Allow from all
	    </Directory>
	</VirtualHost>

### Gzip

When adding a new virtual host it is recommended that gzip compression is used. This can be as simple as adding AddOutputFilterByType, although mod_filter will give greater granularity.

	AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css text/javascript

Support for older browsers may now be obsolete but if required the following properties should be added:

	BrowserMatch ^Mozilla/4 gzip-only-text/html
	BrowserMatch ^Mozilla/4\.0[678] no-gzip
	BrowserMatch \bMSIE !no-gzip !gzip-only-text/html
	
Combined with the virtual hosts entry it shoudl look like this:

	<VirtualHost *:80>
	    ServerName your-site.com
	    DocumentRoot "/var/www/your-site"
	    <Directory "/var/www/html/your-site">
	        Options Indexes FollowSymLinks +Includes ExecCGI
	        AllowOverride All
	        Order allow,deny
	        Allow from all
	    </Directory>
		AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css text/javascript
		BrowserMatch ^Mozilla/4 gzip-only-text/html
		BrowserMatch ^Mozilla/4\.0[678] no-gzip
		BrowserMatch \bMSIE !no-gzip !gzip-only-text/html
	</VirtualHost>

## Section 6: Set up complete

Set up should now be complete all that's left is to restart Apache so it picks up the updated setting.

	service httpd restart
	

