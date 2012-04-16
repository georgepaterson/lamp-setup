# Linux Apache MySQL PHP setup

The LAMP stack is a very popular setup for a web server, it is relatively easy to create, leveraging open source software. Python or Perl may be used to replace PHP depending on specific requirements alternatively the AMP stack can be installed on alternative operating systems.

For this particular setup we will initially reference Red Hat Enterprise Linux / CentOS Linux version 5.x or above.

Your method of accessing the server will depend on it's location, either direct access or Secure Shell (SSH) for a remote server. If you have SSH access you should be able to skip 

## Section 1: Set up root access

By default we don't want to login as root to perform tasks but we do want to give our user account root access when needed. To give our user root access we need to add them to the sudoers list.

Login as root:

	# su
	
Then edit sudoers list, using visudo if available:

	# visudo