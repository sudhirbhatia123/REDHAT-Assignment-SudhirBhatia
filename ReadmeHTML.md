<h1>OWNCLOUD</h1>

<h2>Quick Installation  &amp; Configuration Guide on Ubuntu 20.04</h2>

<h2>Abstract</h2>

<p>This guide explains: <br />
• How to install &amp; configure the Owncloud server as an administrator <br />
• Enable users to connect to the Owncloud server <br />
• How to add a user account <br />
• Connect to the Owncloud server   </p>

<h1>Owncloud Installation</h1>

<h2>Overview</h2>

<p>Owncloud is a completely integrated on-premises content collaboration platform on the market, ready for a new generation of users who expect seamless online collaboration capabilities out of the box.
This is a Quick Guide to install Owncloud on Ubuntu 20.04.</p>

<h1>System Requirements</h1>

<p>• Ubuntu Operating System 20.04 must be installed. <br />
• Ubuntu is considered a good distribution platform for beginners. <br />
• Root access rights to install as an administrator. <br />
• Directory location must be “/var/www/owncloud/” for the installation process. <br />
• PHP must be available in the APT repository. <br />
• The compatible version with Ubuntu 20.04 is PHP 7.4 for installation.  </p>

<h1>Owncloud Installation on Windows</h1>

<p>Follow the steps mentioned below to proceed with the installation process:  </p>

<p>• Users need to perform preliminary checks to ensure that the installation packages are updated to the current version and PHP 7.4 is also available in the APT repository. <br />
• Run the following commands to perform preliminary checks: <br />
<em>apt update &amp;&amp; \ <br />
apt upgrade -y</em>  </p>

<p>• Users need to create a helper script and make it executable too. <br />
• Run the commands mentioned below:  </p>

<p><em>FILE="/usr/local/bin/occ" <br />
/bin/cat &lt;<EOM >$FILE <br />
! /bin/bash <br />
cd /var/www/owncloud <br />
sudo -E -u www-data /usr/bin/php /var/www/owncloud/occ "\$@" <br />
EOM <br />
• Check the executability: <br />
chmod +x /usr/local/bin/occ</em></p>

<h1>Starting Owncloud Installation</h1>

<p><ol>
<li>To start Installation process Run the following commands to install the mandatory packages: <br />
<em>apt install -y \ <br />
apache2 \ <br />
libapache2-mod-php \ <br />
mariadb-server \ <br />
openssl \ <br />
php-imagick php-common php-curl \ <br />
php-gd php-imap php-intl \ <br />
php-json php-mbstring php-mysql \ <br />
php-ssh2 php-xml php-zip \ <br />
php-apcu php-redis redis-server \ <br />
wget</em>  </li>
<li><p>Also, Run the following commands to install the suggested packages: <br />
<em>apt install -y \ <br />
ssh bzip2 rsync curl jq \ <br />
inetutils-ping coreutils</em>  </p></li>
<li><p>Users need to Configure Apache Webserver responsible for accepting directory (HTTP) requests from Internet users, and sending them their desired information in the form of files and Web pages.  </p></li>
<li><p>Run the following commands to change the Document Root: 
<em>sed -i "s#html#owncloud#" /etc/apache2/sites-available/000-default.conf <br />
service apache2 restart</em>  </p></li>
<li><p>By default, on Ubuntu systems, Apache Virtual Hosts configuration files are stored in /etc/apache2/sites-available directory and can be enabled by creating symbolic links to the /etc/apache2/sites-enabled directory.  </p></li>
<li><p>The Apache HTTP Server's built-in virtual hosting allows the server to provide different information based on which IP address, hostname, or port is being requested.  </p></li>
<li><p>Run the following commands to create a Virtual Host Configuration: <br />
FILE="/etc/apache2/sites-available/owncloud.conf" <br />
/bin/cat &lt;<EOM >$FILE  </p></li>
<li>Apache automatically discriminates based on the HTTP Host header supplied by the client whenever the most specific match for an IP address and port combination is listed in multiple virtual hosts. The Server Name directive may appear anywhere within the definition of a server.  </li>
<li>Run the command below to Enable the Virtual Host Configuration: <br />
<em>a2ensite owncloud.conf <br />
service apache2 reload</em>  </li>
<li>Users need to configure the Database as part of the installation process by running the commands mentioned below: <br />
<em>mysql -u root -e "CREATE DATABASE IF NOT EXISTS owncloud; \ <br />
GRANT ALL PRIVILEGES ON owncloud. \ <br />
TO owncloud@localhost \ <br />
IDENTIFIED BY 'password'";</em>  </li>
<li>A "handler" is an internal Apache representation of the action to be performed when a file is called. Generally, files have implicit handlers, based on the file type. Handlers may also be configured explicitly, based on either filename extensions or on location, without relation to file type.  </li>
<li>Run the commands mentioned below to Enable the Recommended Apache Modules: <br />
<em>echo "Enabling Apache Modules" <br />
a2enmod dir env headers mime rewrite setenvif <br />
service apache2 reload</em>  </li>
<li>Users need to download Owncloud as part of the Installation process and run the commands mentioned below to do this: <br />
<em>cd /var/www/ <br />
wget https://download.owncloud.org/community/owncloud-10.8.0.tar.bz2 &amp;&amp; \ <br />
tar -xjf owncloud-10.8.0.tar.bz2 &amp;&amp; \ <br />
chown -R www-data. Owncloud</em>  </li>
<li><p>Start the installation by running commands mentioned below: <br />
<em>occ maintenance:install \ <br />
--database "mysql" \ <br />
--database-name "owncloud" \ <br />
--database-user "owncloud" \ <br />
--database-pass "password" \ <br />
--admin-user "admin" \ <br />
--admin-pass "admin"</em>  </p></li>
<li><p>For more than one domain to access your ownCloud installation, add them manually in the ownCloud configuration file. Configure Owncloud’s Trusted Domains by running commands mentioned below: <br />
<em>myip=$(hostname -I|cut -f1 -d ' ') <br />
occ config:system:set trusted_domains 1 --value="$myip"</em>  </p></li>
<li>Using the operating system Cron feature is the preferred method for executing regular tasks. This method enables the execution of scheduled jobs without the inherent limitations which the webserver might have. Run the commands mentioned below: <br />
<em>occ background:corn <br />
echo "/15 /var/www/owncloud/occ system:cron" \ <br /></p>

<blockquote>
  <p>/var/spool/cron/crontabs/www-data <br />
chown www-data.crontab /var/spool/cron/crontabs/www-data <br />
chmod 0600 /var/spool/cron/crontabs/www-data</em>  </li>
<li>Every 15 minutes this cronjob will inactivate the unavailable users. <br />
<em>echo "/15 /var/www/owncloud/occ user:sync 'OCA\User<em>LDAP\User</em>Proxy' -m disable -vvv >> /var/log/ldap-sync/user-sync.log 2>&amp;1" >> 
/var/spool/cron/crontabs/www-data <br />
chown www-data.crontab  /var/spool/cron/crontabs/www-data <br />
chmod 0600  /var/spool/cron/crontabs/www-data <br />
mkdir -p /var/log/ldap-sync <br />
touch /var/log/ldap-sync/user-sync.log <br />
chown www-data. /var/log/ldap-sync/user-sync.log</em>  </li>
<li>Caching and File Locking prevents simultaneous file saving. It is enabled by default and uses the database to store the locking data. This places a significant load on your database. It is recommended to use a cache backend instead. <br />
Run the Commands mentioned below: <br />
<em>occ config:system:set \
memcache.local \ <br />
--value '\OC\Memcache\APCu' <br />
occ config:system:set \ <br />
memcache.locking \ <br />
--value '\OC\Memcache\Redis' <br />
occ config:system:set \ <br />
redis \ <br />
--value '{"host": "127.0.0.1", "port": "6379"}' \ <br />
--type json</em>  </li>
<li>logrotate is designed to ease the administration of systems that generate large numbers of log files. It allows automatic rotation, compression, removal, and mailing of log files. Each log file may be handled daily, weekly, monthly, or when it grows too large. Normally, logrotate is run as a daily cron job. Run the commands mentioned below to configure log rotation: <br />
<em>FILE="/etc/logrotate.d/owncloud" <br />
sudo /bin/cat &lt;<EOM >$FILE <br />
/var/www/owncloud/data/owncloud.log { <br />
size 10M <br />
rotate 12 <br />
copytruncate <br />
missingok <br />
compress <br />
compresscmd /bin/gzip <br />
} <br />
EOM</em>  </li>
<li>Check the permissions by running the commands mentioned below: <br />
*cd /var/www/ <br />
chown -R www-data. owncloud  </li>
<li>Owncloud Package has been installed and is ready to use.  </li>
</ol></p>

<h1>Enable users to connect to the Owncloud server using the server's IP address and port 8080 as an administrator?</h1>
</blockquote>

<p>Users can use IP addresses with Port number and domain names to access the Owncloud server. These should be pre-defined in the Trusted_domains setting in the config.php file. Users are able to connect only to the domains which are listed in settings of Trusted Domains.  </p>

<p>Users can connect to the Owncloud server using the server's IP address and port number by defining the below mentioned configuration in Trusted_domains settings:  </p>

<p>’trusted_domains’ => <br />
array ( <br />
0 => ’localhost’, <br />
1 => ’server1.example.com’, <br />
2 => ’192.168.1.50’, <br />
),    </p>

<p>Users can add the port number with the IP address like mentioned below:</p>

<p>192.168.1.50:8080  </p>

<h1>How to add a user account as an administrator</h1>

<p>As an Administrator, follow the steps mentioned below to add a new user:  </p>

<ol>
<li>Access the User management page of your ownCloud Web UI.    </li>
<li>Enter the Login name of the new user (letters (a-z, A-Z), numbers (0-9), dashes (-), underscores (_), periods (.) and at signs (@)).  </li>
<li>Enter the initial password for the new user (the user can change the password on the first login).  </li>
<li>Admin can provide Group memberships if required.  </li>
<li>Click Create Button.  </li>
<li>Users will receive a confirmation email regarding login creation.    </li>
</ol>

<h1>Connect to the Owncloud server using a desktop or mobile client</h1>

<ol>
<li>Users need to install the desktop or mobile client to connect to the Owncloud server.   </li>
<li>After installation, open the desktop client and follow the options to configure and set up the account.  </li>
<li>To connect to the Owncloud server, please enter the URL of the Owncloud server in the dialog box.  </li>
<li>Enter your login credentials.  </li>
<li>Users can select the local folder/individual folders to option to sync the files.  </li>
<li>Click the connect button.  </li>
<li>On successful connection, the client will start sync-up with the files and will also provide options to connect to Web GUI and to access the local folders.</li>
</ol>

