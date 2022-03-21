## Welcome to GitHub Pages

You can [Install With CentOS 8](https://github.com/franckya/openNMS.git) as this is an open source running on Linux distributions.

If you are following and deploying this server properly on your local machine, you will have to make sure you have open ports 8980 to access the portal online. 

### If you cannot access the link above, follow the commands below.

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

## Install PostgreSQL client and server 
sudo dnf -y install postgresql-server postgresql

## Initialize the PostgreSQL database
sudo postgresql-setup --initdb --unit postgresql

## Create an opennms database user and password
sudo -i -u postgres createuser -P opennms

## Create an empty database and set the owner to the opennms user 
sudo -i -u postgres createdb -O opennms opennms

## Set a password for PostgreSQL superuser
sudo -i -u postgres psql -c "ALTER USER postgres WITH PASSWORD 'CHANGE-ME-WITH-YOUR-OWN-POSTGRES-PASSWORD';"

## Change the access policy for PostgreSQL
sudo vi /var/lib/pgsql/data/pg_hba.conf

- Allow {page-component-title} to access the database over the local network with an MD5 hashed password

host    all             all             127.0.0.1/32            md5(1)
host    all             all             ::1/128                 md5(1)

## Apply configuration changes for PostgreSQL
sudo systemctl reload postgresql

## Add repository and import GPG key 
sudo dnf -y install https://yum.opennms.org/repofiles/opennms-repo-stable-rhel8.noarch.rpm 
sudo rpm --import https://yum.opennms.org/OPENNMS-GPG-KEY

## Install Horizon with all built-in dependencies
sudo dnf -y install opennms

Optional: Install R-core packages for time series trending and forecasting: 
-> sudo dnf -y install epel-release sudo dnf -y install R-core

## Disable auto updates for OpenNMS Horizon
sudo dnf config-manager --disable opennms-repo-stable-*

## Configure PostgreSQL database access 
sudo -u opennms vi /usr/share/opennms/etc/opennms-datasources.xml

Set credentials to access the PostgreSQL database. I recommend to use the same for both opennms and postgres. I usually put admin there, and I can still change it later.

Set the database name Horizon should use. Set the user name to access the opennms database table. Set the password to access the opennms database table. Set the postgres user for administrative access to PostgreSQL. Set the password for administrative access to PostgreSQL.

## Detect Java environment and persist in /usr/share/opennms/etc/java.conf
sudo /usr/share/opennms/bin/runjava -s

## Initialize the database and detect system libraries persisted in /opt/opennms/etc/libraries.properties
sudo /usr/share/opennms/bin/install -dis

Enable Masquerade to allow port forwarding Note: In this section, you may need to enable the firewall service before applying the set below. Remember, you need to have su privileges in order to do so.

## Open Firewalls
### Install: sudo dnf install firewalld
### Enable: sudo systemctl enable
### Start: sudo systemctl start firewalld
### Status: sudo systemctl status firewalld

Once the steps below are completed, you can now allow ports forwarding.

-> sudo firewall-cmd --permanent --add-masquerade Forward SNMP Trap UDP port 162 to 10162
-> sudo firewall-cmd --permanent --add-port=162/udp
-> sudo firewall-cmd --permanent --add-port=10162/udp
-> sudo firewall-cmd --permanent --add-forward-port=port=162:proto=udp:toport=10162
-> sudo systemctl reload firewalld


# After you log out, to log back in, and make sure your instance is running; you can perform the command: to launch it.

!! Remember that everytime that you exite the console, your web page will not be accessible !!




1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [OpenNMS Document Guide](https://docs.opennms.com/start-page/1.0.0/index.html).

### About the Author

View my profile and contact me directly for any inquiry [Email](https://franckya.github.io/Yannick_Djomo/). `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out the support page[d](https://www.opennms.com/support/) or [contact support](sales@opennms.com) and we’ll help you sort it out.
