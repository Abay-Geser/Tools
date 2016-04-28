#For CentOS 6.x 32bit:
rpm -Uvh http://yum.postgresql.org/9.4/redhat/rhel-6-i386/pgdg-centos94-9.4-1.noarch.rpm

For CentOS 6.x 64bit:
rpm -Uvh http://yum.postgresql.org/9.4/redhat/rhel-6-x86_64/pgdg-centos94-9.4-1.noarch.rpm

yum update
yum install postgresql94-server postgresql94-contrib
service postgresql-9.4 initdb
service postgresql-9.4 start
chkconfig postgresql-9.4 on
service postgresql-9.4 restart

# FIREWALL SETTINGS

nano /etc/sysconfig/iptables

	-A INPUT -m state --state NEW -m tcp -p tcp --dport 5432 -j ACCEPT
	-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT

service iptables restart


----------------------------------------------------------------------------------------

For CentOS 7 64bit:
rpm -Uvh http://yum.postgresql.org/9.4/redhat/rhel-7-x86_64/pgdg-centos94-9.4-1.noarch.rpm


yum update
yum install postgresql94-server postgresql94-contrib
/usr/pgsql-9.4/bin/postgresql94-setup initdb
systemctl enable postgresql-9.4
systemctl start postgresql-9.4
systemctl restart postgresql-9.4
# FIREWALL SETTINGS

firewall-cmd --permanent --add-port=5432/tcp
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --reload

----------------------------------------------------------------------------------------
# PASSWORD SETTINGS
su - postgres
psql
postgres=# \password postgres 
	Enter new password: 
	Enter it again: 
postgres=# \q

# Create New User and Database
su - postgres
$ createuser newuser
createdb newdb
psql
postgres=# alter user newuser with encrypted password 'centos';
ALTER ROLE
postgres=# grant all privileges on database newdb to newuser;
GRANT
postgres=#

# MANAGE DB
## Delete Users and Databases

su - postgres
Enter command:

$ dropdb <database-name>
To delete a user, enter the following command:

$ dropuser <user-name>

## Configure PostgreSQL-MD5 Authentication
### MD5 authentication requires the client to supply an MD5-encrypted password for authentication. To do that, edit /var/lib/pgsql/9.4/data/pg_hba.conf file:

nano /var/lib/pgsql/9.4/data/pg_hba.conf
[...]
# TYPE  DATABASE        USER            ADDRESS                 METHOD
# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
host    all             all             192.168.0.0/16          md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
[...]

Configure PostgreSQL-Configure TCP/IP

By default, TCP/IP connection is disabled, so that the users from another computers can’t access postgresql. To allow to connect users from another computers, Edit file /var/lib/pgsql/9.4/data/postgresql.conf:

nano /var/lib/pgsql/9.4/data/postgresql.conf
Find the lines:

[...]
#listen_addresses = 'localhost'
[...]
#port = 5432
[...]
Uncomment both lines, and set the IP address of your postgresql server or set ‘*’ to listen from all clients as shown below:

listen_addresses = '*'
port = 5432
Restart postgresql service to save changes:

On CentOS 6.x systems:

/etc/init.d/postgresql-9.4 restart
On CentOS 7 systems:

systemctl restart postgresql-9.4

