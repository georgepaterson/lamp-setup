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















