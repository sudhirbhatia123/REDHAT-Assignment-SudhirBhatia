# OWNCLOUD  #
## Quick Installation  & Configuration Guide on Ubuntu 20.04 ##
## Abstract ##
This guide explains:  
•	How to install & configure the Owncloud server as an administrator   
•	Enable users to connect to the Owncloud server  
•	How to add a user account  
•	Connect to the Owncloud server   


# Owncloud Installation #
## Overview ##
Owncloud is a completely integrated on-premises content collaboration platform on the market, ready for a new generation of users who expect seamless online collaboration capabilities out of the box.
This is a Quick Guide to install Owncloud on Ubuntu 20.04.

# System Requirements #
• Ubuntu Operating System 20.04 must be installed.  
• Ubuntu is considered a good distribution platform for beginners.  
• Root access rights to install as an administrator.  
• Directory location must be “/var/www/owncloud/” for the installation process.  
• PHP must be available in the APT repository.  
• The compatible version with Ubuntu 20.04 is PHP 7.4 for installation.  

# Owncloud Installation on Windows #
  
Follow the steps mentioned below to proceed with the installation process:  
  
• Users need to perform preliminary checks to ensure that the installation packages are updated to the current version and PHP 7.4 is also available in the APT repository.   
• Run the following commands to perform preliminary checks:  
*apt update && \  
apt upgrade -y*  
  
• Users need to create a helper script and make it executable too.   
• Run the commands mentioned below:  

*FILE="/usr/local/bin/occ"  
/bin/cat <<EOM >$FILE  
! /bin/bash  
cd /var/www/owncloud  
sudo -E -u www-data /usr/bin/php /var/www/owncloud/occ "\$@"  
EOM  
• Check the executability:  
chmod +x /usr/local/bin/occ*

# Starting Owncloud Installation #

1. To start Installation process Run the following commands to install the mandatory packages:  
*apt install -y \  
  apache2 \  
  libapache2-mod-php \  
  mariadb-server \  
  openssl \  
  php-imagick php-common php-curl \  
  php-gd php-imap php-intl \  
  php-json php-mbstring php-mysql \  
  php-ssh2 php-xml php-zip \  
  php-apcu php-redis redis-server \  
  wget*  
2. Also, Run the following commands to install the suggested packages:  
*apt install -y \  
  ssh bzip2 rsync curl jq \  
  inetutils-ping coreutils*  

3. Users need to Configure Apache Webserver responsible for accepting directory (HTTP) requests from Internet users, and sending them their desired information in the form of files and Web pages.  

4. Run the following commands to change the Document Root: 
*sed -i "s#html#owncloud#" /etc/apache2/sites-available/000-default.conf  
service apache2 restart*  
 
5. By default, on Ubuntu systems, Apache Virtual Hosts configuration files are stored in /etc/apache2/sites-available directory and can be enabled by creating symbolic links to the /etc/apache2/sites-enabled directory.  

6. The Apache HTTP Server's built-in virtual hosting allows the server to provide different information based on which IP address, hostname, or port is being requested.  

7. Run the following commands to create a Virtual Host Configuration:  
FILE="/etc/apache2/sites-available/owncloud.conf"  
/bin/cat <<EOM >$FILE  
8. Apache automatically discriminates based on the HTTP Host header supplied by the client whenever the most specific match for an IP address and port combination is listed in multiple virtual hosts. The Server Name directive may appear anywhere within the definition of a server.  
9. Run the command below to Enable the Virtual Host Configuration:  
*a2ensite owncloud.conf  
service apache2 reload*  
10. Users need to configure the Database as part of the installation process by running the commands mentioned below:  
*mysql -u root -e "CREATE DATABASE IF NOT EXISTS owncloud; \  
GRANT ALL PRIVILEGES ON owncloud. \  
  TO owncloud@localhost \  
IDENTIFIED BY 'password'";*  
11. A "handler" is an internal Apache representation of the action to be performed when a file is called. Generally, files have implicit handlers, based on the file type. Handlers may also be configured explicitly, based on either filename extensions or on location, without relation to file type.  
12. Run the commands mentioned below to Enable the Recommended Apache Modules:  
*echo "Enabling Apache Modules"  
a2enmod dir env headers mime rewrite setenvif  
service apache2 reload*  
13. Users need to download Owncloud as part of the Installation process and run the commands mentioned below to do this:  
*cd /var/www/  
wget https://download.owncloud.org/community/owncloud-10.8.0.tar.bz2 && \  
tar -xjf owncloud-10.8.0.tar.bz2 && \  
chown -R www-data. Owncloud*  
14. Start the installation by running commands mentioned below:  
*occ maintenance:install \  
    --database "mysql" \  
    --database-name "owncloud" \  
    --database-user "owncloud" \  
    --database-pass "password" \  
    --admin-user "admin" \  
    --admin-pass "admin"*  

15. For more than one domain to access your ownCloud installation, add them manually in the ownCloud configuration file. Configure Owncloud’s Trusted Domains by running commands mentioned below:  
*myip=$(hostname -I|cut -f1 -d ' ')  
occ config:system:set trusted_domains 1 --value="$myip"*  
16. Using the operating system Cron feature is the preferred method for executing regular tasks. This method enables the execution of scheduled jobs without the inherent limitations which the webserver might have. Run the commands mentioned below:  
*occ background:corn  
echo "/15 /var/www/owncloud/occ system:cron" \  
  > /var/spool/cron/crontabs/www-data  
chown www-data.crontab /var/spool/cron/crontabs/www-data  
chmod 0600 /var/spool/cron/crontabs/www-data*  
17. Every 15 minutes this cronjob will inactivate the unavailable users.  
*echo "/15 /var/www/owncloud/occ user:sync 'OCA\User_LDAP\User_Proxy' -m disable -vvv >> /var/log/ldap-sync/user-sync.log 2>&1" >> 
/var/spool/cron/crontabs/www-data  
chown www-data.crontab  /var/spool/cron/crontabs/www-data  
chmod 0600  /var/spool/cron/crontabs/www-data  
mkdir -p /var/log/ldap-sync  
touch /var/log/ldap-sync/user-sync.log  
chown www-data. /var/log/ldap-sync/user-sync.log*  
18. Caching and File Locking prevents simultaneous file saving. It is enabled by default and uses the database to store the locking data. This places a significant load on your database. It is recommended to use a cache backend instead.  
Run the Commands mentioned below:  
*occ config:system:set \
  memcache.local \  
  --value '\OC\Memcache\APCu'  
occ config:system:set \  
   memcache.locking \  
   --value '\OC\Memcache\Redis'  
occ config:system:set \  
   redis \  
   --value '{"host": "127.0.0.1", "port": "6379"}' \  
   --type json*  
19. logrotate is designed to ease the administration of systems that generate large numbers of log files. It allows automatic rotation, compression, removal, and mailing of log files. Each log file may be handled daily, weekly, monthly, or when it grows too large. Normally, logrotate is run as a daily cron job. Run the commands mentioned below to configure log rotation:  
*FILE="/etc/logrotate.d/owncloud"  
sudo /bin/cat <<EOM >$FILE  
/var/www/owncloud/data/owncloud.log {  
  size 10M  
  rotate 12  
  copytruncate  
  missingok  
  compress  
  compresscmd /bin/gzip  
}  
EOM*  
20. Check the permissions by running the commands mentioned below:  
*cd /var/www/  
chown -R www-data. owncloud  
21. Owncloud Package has been installed and is ready to use.  

# Enable users to connect to the Owncloud server using the server's IP address and port 8080 as an administrator?  #
  
Users can use IP addresses with Port number and domain names to access the Owncloud server. These should be pre-defined in the Trusted_domains setting in the config.php file. Users are able to connect only to the domains which are listed in settings of Trusted Domains.  
  
Users can connect to the Owncloud server using the server's IP address and port number by defining the below mentioned configuration in Trusted_domains settings:  
  
’trusted_domains’ =>  
array (  
0 => ’localhost’,  
1 => ’server1.example.com’,  
2 => ’192.168.1.50’,  
),    

Users can add the port number with the IP address like mentioned below:
  
192.168.1.50:8080  

# How to add a user account as an administrator  #

As an Administrator, follow the steps mentioned below to add a new user:  
  
1.  Access the User management page of your ownCloud Web UI.    
2.  Enter the Login name of the new user (letters (a-z, A-Z), numbers (0-9), dashes (-), underscores (_), periods (.) and at signs (@)).  
3.  Enter the initial password for the new user (the user can change the password on the first login).  
4.  Admin can provide Group memberships if required.  
5.  Click Create Button.  
6.  Users will receive a confirmation email regarding login creation.    
  

# Connect to the Owncloud server using a desktop or mobile client  #
1.  Users need to install the desktop or mobile client to connect to the Owncloud server.   
2.  After installation, open the desktop client and follow the options to configure and set up the account.  
3.  To connect to the Owncloud server, please enter the URL of the Owncloud server in the dialog box.  
4.  Enter your login credentials.  
5.  Users can select the local folder/individual folders to option to sync the files.  
6.  Click the connect button.  
7.  On successful connection, the client will start sync-up with the files and will also provide options to connect to Web GUI and to access the local folders.
