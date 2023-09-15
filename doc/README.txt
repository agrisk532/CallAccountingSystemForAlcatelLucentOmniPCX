AdventusCC RC1 call accounting system setup
by Agris Klimkans
05Feb2014

Windows
-------

1) Run install_tsp.exe or install_tsp_64.exe from the TAPI\PIMphony_6.7_bld2925_XX_Alcatel folder to install the Alcatel Telephonu Service Provider. You will be prompted for the IP adress of PBX and the DN of the agent PC. After install finishes, restart the PC!!!!
Open Control Panel -> Phone and Modem -> Advanced -> Alcatel TAPI Service Provider -> Configure.
Look if the Connection State is OK.

2) Run topTapiSetup.exe from the TopTapi folder. Accept all defaults.

3) Run setup.exe from the AdventusCC\setup folder. It will install files somewhere in the Local Settings folder and add shortcut to the Start menu.


Linux Mint 16
-------------

1) install the following packages on your server:
apache2
libapache2-mod-php
php5 (5.5.3 on Mint)
postgresql (9.1.11 for Mint)
postgresql-contrib
pgadmin3
php5-pqsql 
php5-json
php5-intl 
curl

$dpkg -l

ii  apache2                                     2.4.6-2ubuntu2.1                        amd64        Apache HTTP Server
ii  apache2-bin                                 2.4.6-2ubuntu2.1                        amd64        Apache HTTP Server (binary files and modules)
ii  apache2-data                                2.4.6-2ubuntu2.1                        all          Apache HTTP Server (common files)
ii  libapache2-mod-php5                         5.5.3+dfsg-1ubuntu2.1                   amd64        server-side, HTML-embedded scripting language (Apache 2 module)
ii  libapr1                                     1.4.8-1ubuntu1                          amd64        Apache Portable Runtime Library
ii  libaprutil1                                 1.5.2-1                                 amd64        Apache Portable Runtime Utility Library

ii  php-http-request                            1.4.4-4                                 all          PEAR class to provide an easy way to perform HTTP requests
ii  php-net-socket                              1.0.14-1                                all          PHP PEAR Network Socket Interface module
ii  php-net-url                                 1.0.15-3                                all          easy parsing of Urls
ii  php-pear                                    5.5.3+dfsg-1ubuntu2.1                   all          PEAR - PHP Extension and Application Repository
ii  php-soap                                    0.13.0-1                                all          SOAP Client/Server class for PHP
ii  php5                                        5.5.3+dfsg-1ubuntu2.1                   all          server-side, HTML-embedded scripting language (metapackage)
ii  php5-cli                                    5.5.3+dfsg-1ubuntu2.1                   amd64        command-line interpreter for the php5 scripting language
ii  php5-common                                 5.5.3+dfsg-1ubuntu2.1                   amd64        Common files for packages built from the php5 source
ii  php5-intl                                   5.5.3+dfsg-1ubuntu2.1                   amd64        internationalisation module for php5
ii  php5-json                                   1.3.1+dfsg-2                            amd64        JSON module for php5
ii  php5-pgsql                                  5.5.3+dfsg-1ubuntu2.1                   amd64        PostgreSQL module for php5

ii  postgresql                                  9.3+146really9.1+148                    all          object-relational SQL database (supported version)
ii  postgresql-9.1                              9.1.11-0ubuntu0.13.10                   amd64        object-relational SQL database, version 9.1 server
ii  postgresql-client-9.1                       9.1.11-0ubuntu0.13.10                   amd64        front-end programs for PostgreSQL 9.1
ii  postgresql-client-common                    148                                     all          manager for multiple PostgreSQL client versions
ii  postgresql-common                           148                                     all          PostgreSQL database-cluster manager
ii  postgresql-contrib-9.1                      9.1.11-0ubuntu0.13.10                   amd64        additional facilities for PostgreSQL

ii  libpq5                                      9.1.11-0ubuntu0.13.10                   amd64        PostgreSQL C client library

ii  pgadmin3                                    1.16.1-2                                amd64        graphical administration tool for PostgreSQL
ii  pgadmin3-data                               1.16.1-2                                all          graphical administration tool for PostgreSQL - documentation

ii  curl                                        7.32.0-1ubuntu1.3                       amd64        command line tool for transferring data with URL syntax

======================================
Fedora 20
--------------------------------------
postgresql-9.3.2-2.fc20.i686
postgresql-contrib-9.3.2-2.fc20.i686
postgresql-server-9.3.2-2.fc20.i686
postgresql-libs-9.3.2-2.fc20.i686
pgadmin3-1.18.0-1.fc20.i686

#postgresql-setup initdb
#systemctl enable postgresql.service 

cd /var/lib/pgsql/data

For pgadmin:
vi /var/lib/pgsql/data/postgresql.conf
#listen_addresses = "localhost" (listen only on localhost)
vi /var/lib/pgsql/data/pg_hba.conf
# Local networks
host	all	all	    xx.xx.xx.xx/xx	md5
# Example
host	all	all	    192.168.0.0/24	md5
host    all all     127.0.0.1/32    md5

#
sudo -u postgres psql template1
alter user postgres with encrypted password 'postgres';
vi /var/lib/pgsql/data/pg_hba.conf
local all postgres md5
#

systemctl restart postgresql.service 

yum install httpd
systemctl enable httpd.service
systemctl start httpd.service

yum install php
yum install php-pgsql
yum install php-mbstring
yum install php-intl
systemctl restart httpd.service

$ APACHEUSER=`ps aux | grep -E '[a]pache|[h]ttpd|[_]www|[w]ww-data' | grep -v root | head -1 | cut -d\  -f1`
$ sudo setfacl -R -m u:"$APACHEUSER":rwX -m u:`whoami`:rwX app/cache app/logs
$ sudo setfacl -dR -m u:"$APACHEUSER":rwX -m u:`whoami`:rwX app/cache app/logs

http://localhost/config.php
Perform all recommendations. If everything is ok, then click
"Configure your Symfony Application online"
Configure your database settings (DB server IP address, username, password,...-> Click 'Next Step'
Generate Global Secret -> Click 'Next Step'
If you see "Well Done" message and configuration parameters, then setup is finished.
Click on the "Go to the Welcome page".




===============================================

Notes:
PHP needs to be a minimum version of PHP 5.3.3
JSON needs to be enabled
ctype needs to be enabled
Your php.ini needs to have the date.timezone setting


2) assuming your apache2 DocumentRoot is at /var/www, extract archive AdventusCC.tgz in /var/www
so you'll have /var/www/Symfony folder with subfolders

3)
su
#groupadd symfony
// add users www-data and agrisk (or whatever your user account is) into group symfony)
#usermod -a -G symfony agrisk
#usermod -a -G symfony www-data
#chown -R root:symfony /var/www/Symfony
#chmod -R 775 /var/www/Symfony/

4) point apache DocumentRoot to /var/www/Symfony/web

5) vi /etc/apache2/apache2.conf
ServerName localhost (or whatever)

6) http://localhost/config.php
Correct if any errors:
cd /var/www/Symfony/app
su
chown -R root:symfony cache logs
chmod -R 775 cache logs
vi /etc/php5/apache2/php.ini
date.timezone = Europe/Riga
vi /etc/php5/cli/php.ini
date.timezone = Europe/Riga
service apache2 restart
http://localhost/config.php

7) Edit your database connection parameters in /var/www/Symfony/app/config/parameters.yml
Ensure that database encoding and collate is UTF-8.
Currently the application is configured to use PostgreSQL database.
It can be changed to other, if necessary.

[agrisk@localhost Linux]$ whoami
agrisk
[agrisk@localhost Linux]$ cd /var/www/Symfony/
[agrisk@localhost Symfony]$ php app/console doctrine:database:create
Created database for connection named "symfony"




8)
$php app/console doctrine:schema:update --dump-sql
You'll see the generated from PHP Entities SQL statements

9)
$php app/console doctrine:schema:update --force
Creates database tables

10) http://localhost/app.php/listcalls


DATABASE TABLES
----------------
1) CCUSER - user data
Edit fields:
firstname
lastname
username  (login name)
password (sha1 encoded, automatically set to 'symfony' for new users
roles (ROLE_ADMIN or ROLE_USER)

2) CALLSUBJECT - call subjects, will be loaded into Windows application at successful login
Edit fields:
subject

3) DN - DN/IP address association
Edit fields:
dn
ipaddress
(status field not used currently)
(do not associate more than one IP address to any DN)


4) CCUSERDN - user/dn association. Here you configure what DNs are allowed for users.
Edit fields:
ccuser_id (foreign key to CCUSER.id)
dn_id (foreign key to DN.id)
