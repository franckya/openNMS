# openNMS

Install PostgreSQL client and server
sudo dnf -y install postgresql-server postgresql

Initialize the PostgreSQL database
sudo postgresql-setup --initdb --unit postgresql

Enable PostgreSQL on system boot and start immediately
sudo systemctl enable --now postgresql

Create an opennms database user and password
sudo -i -u postgres createuser -P opennms

Create an empty database and set the owner to the opennms user
sudo -i -u postgres createdb -O opennms opennms

Set a password for PostgreSQL superuser
sudo -i -u postgres psql -c "ALTER USER postgres WITH PASSWORD 'YOUR-POSTGRES-PASSWORD';"

Change the access policy for PostgreSQL
sudo vi /var/lib/pgsql/data/pg_hba.conf

Allow Horizon to access the database over the local network with an MD5 hashed password
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5

Change method from ident to md5 for IPv4 and IPv6 on localhost.

Apply configuration changes for PostgreSQL
sudo systemctl reload postgresql

Add repository and import GPG key
sudo dnf -y install https://yum.opennms.org/repofiles/opennms-repo-stable-rhel8.noarch.rpm
sudo rpm --import https://yum.opennms.org/OPENNMS-GPG-KEY

Install Horizon with all built-in dependencies
sudo dnf -y install opennms

Install R-core packages for time series trending and forecasting (optional)
sudo dnf -y install epel-release
sudo dnf -y install R-core

Disable auto updates for OpenNMS Horizon
sudo dnf config-manager --disable opennms-repo-stable-*

Verify directory structure with the tree command
sudo dnf -y install tree
tree /opt/opennms -L 1

Directory structure after successful installation
/opt/opennms
├── bin
├── contrib
├── data
├── deploy
├── etc
├── jetty-webapps
├── lib
├── logs -> /var/log/opennms
├── share -> /var/opennms
└── system

Configure PostgreSQL database access
sudo -u opennms vi /usr/share/opennms/etc/opennms-datasources.xml

Set credentials to access the PostgreSQL database. I recommend to use the same for both opennms and postgres. I usually put admin there, and I can still change it later. 

<jdbc-data-source name="opennms"
                    database-name="opennms"
                    class-name="org.postgresql.Driver"
                    url="jdbc:postgresql://localhost:5432/opennms"
                    user-name="** YOUR-OPENNMS-USERNAME **"
                    password="** YOUR-OPENNMS-PASSWORD **" />

<jdbc-data-source name="opennms-admin"
                    database-name="template1"
                    class-name="org.postgresql.Driver"
                    url="jdbc:postgresql://localhost:5432/template1"
                    user-name="postgres"
                    password="** YOUR-POSTGRES-PASSWORD **" />
Set the database name Horizon should use.
Set the user name to access the opennms database table.
Set the password to access the opennms database table.
Set the postgres user for administrative access to PostgreSQL.
Set the password for administrative access to PostgreSQL.

Detect Java environment and persist in /usr/share/opennms/etc/java.conf
sudo /usr/share/opennms/bin/runjava -s

Initialize the database and detect system libraries persisted in /opt/opennms/etc/libraries.properties
sudo /usr/share/opennms/bin/install -dis

Enable Masquerade to allow port forwarding
Note: In this section, you may need to enable the firewall service before applying the set below. Remember, you need to have su privileges in order to do so. 

You can do that by entering the command: 

Install: sudo dnf install firewalld
Enable:  sudo systemctl enable firewalld
Start:   sudo systemctl start firewalld
Check the status: sudo systemctl status firewalld

Once the steps below are completed, you can now allow ports forwarding. 

sudo firewall-cmd --permanent --add-masquerade
Forward SNMP Trap UDP port 162 to 10162
sudo firewall-cmd --permanent --add-port=162/udp
sudo firewall-cmd --permanent --add-port=10162/udp
sudo firewall-cmd --permanent --add-forward-port=port=162:proto=udp:toport=10162
sudo systemctl reload firewalld

After you log out, to log back in, and make sure your instance is running; you can perform the command: to launch it. 

Remember that everytime that you exite the console, your web page will not be accessible. 

First login
After you start the Horizon Core services, access the web application at
http://core-instance-ip:8980/opennms. The default login and password is admin.
Immediately change the password to a secure one.
Open http://core-instance-ip:8980/opennms in your web browser.

Log in with with admin/admin.

Click admin → Change Password in the navigation bar.

!!!!!!!!!!!!!!!CONGRATULATIONS!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!YOUR PAGE IS LIVE!!!!!!!!!!!!!! 


Use admin as the current password then type and confirm a new password in the appropriate boxes.

Click Submit.

Log out, then log in with your new password.
