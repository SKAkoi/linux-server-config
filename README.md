
# Linux Server Config
---------------------------

## Details
1. IP Address = 18.221.245.98
2. ssh port = 2200
3. URL = http://18.221.245.98/categories

## Summary of software and configuration changes made

### Update package lists
1. sudo apt-get update
2. sudo apt-get upgrade

### Create new user account 'grader' and give usdo access
1. sudo adduser grader
2. sudo touch /etc/sudoers.d/grader
3. sudo nano /etc/sudoers.d/grader 
added 'grader ALL=(ALL:ALL) ALL' to the file before saving and exiting

### Create SSH keypair
1. ssh-keygen -t rsa (on my local machine and saved the key to the file /Users/kwakuakoi/.ssh/udacity_key)
2. su - grader (to log in as grader)
3. mkdir .ssh
4. touch .ssh/authorized_keys
5. Copied the public key from /Users/kwakuakoi/.ssh/udacity_key.pub and saved it to the remote server
6. sudo chmod 700 .ssh
7. sudo chmod 644 .ssh/authprized_keys
8. exit (logout grader)
9. ssh grader@18.221.245.98 -p 22 -i ~/.ssh/udacity_key

### Configure timezone
1. sudo tzselect
2. 11 (to choose the “I want to specify time zone…)
3. UTC-0
4. 1 (to choose “Yes” to confirm the change)

### Install apache
1. sudo apt-get install apache2
2. sudo service apache2 restart

### Install and enable mod-wsgi
1. sudo apt-get install libapache2-mod-wsgi
2. sudo a2enmod wsgi
3. sudo service apache2 restart

### Install Postgresql and switch to postgres user
1. sudo apt-get install postgresql
2. sudo su - postgres

### Create Postgres user and DB
1. createuser --interactive
2. catalog (as the name of the role)
3. n (not a superuser)
4. n (not allowed to create databases)
5. n (not allowed to create more roles)
6. createdb catalog (create the database)
7. psql
8. postgres=# alter user catalog with encrypted password 'catalogue2017'; 
9. postgres=# grant all privileges on database catalog to catalog;
10. \q
11. exit (to switch back to grader)
12. sudo nano /etc/postgresql/9.5/main/pg_hba.conf (to check that no remote connections are allowed)

### Download, install git and clone the repo
1. sudo apt-get install git
2. cd /var/www
3. sudo git clone https://github.com/SKAkoi/category-hotels.git

### Install project dependencies
1. sudo apt-get install python-psycopg2 (Postgres DB adapter for python)
2. sudo apt-get install python-flask (Flask)
3. sudo apt-get install python-sqlalchemy (SQLAlchemy)
4. sudo apt-get install python-pip (Pip for other dependencies)
5. sudo apt-get install python-dev (Header and static files library for python)
6. sudo pip install oauth2client (pip installation for Oauth)
7. sudo pip install requests (pip installation for requests)
8. sudo pip install httplib2
9. sudo pip install flask-sqlalchemy

### Configure project
1. sudo chmod 777 -R . (made files readable, writeable and executable)
2. cd category-hotels
3. nano database_setup.py (change the engine to 'postgresql://catalog:catalogue2017@localhost/catalog')
4. nano hotels.py (change the engine to 'postgresql://catalog:catalogue2017@localhost/catalog')
5. nano hotels.py (change the location of client secret to '/var/www/category-hotels/client_secret')

### Create the wsgi
1. sudo nano category-hotels.wsgi (to create the wsgi file)
2. Write the following in the file:
    * #!/usr/bin/python
    * import sys
    * import logging
    * logging.basicConfig(stream=sys.stderr)
    * sys.path.insert(0, “/var/www/category-hotels/“)
    * from category-hotels import app as application
    * application.secret_key = “something”

### Configure a new virtualhost
1. cd ~ (back to grader home)
2. sudo nano /etc/apache2/sites-available/category-hotels.conf
3. Write the following: 
<VirtualHost *:80>
        ServerName 18.221.245.98
        ServerAdmin akoikwaku@gmail.com
        WSGIScriptAlias / /var/www/category-hotels/category-hotels.wsgi
        <Directory /var/www/category-hotels/>
                Order allow,deny
                Allow from all
        </Directory>
        Alias /static /var/www/category-hotels/static
        <Directory /var/www/category-hotels/static/>
                Order allow,deny
                Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

### Disable the default site and enable the category-hotels app
1. sudo a2dissite 000-default (disable default site)
2. sudo a2ensite category-hotels (enable the app)
3. sudo service apache2 restart
