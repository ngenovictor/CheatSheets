1. Setting Up environment and Apache2
    > According to https://github.com/codingforentrepreneurs/Guides/blob/master/all/Debian_Install_Django_Apache2.md
    ```bash
    apt-get install apache2
    apt-get install python-pip python-virtualenv python-setuptools python-dev build-essentialapt-get install libapache2-mod-wsgi-py3
    ```

2. Installing PostgreSQL
    > According to https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04
    ```bash
    apt-get install postgresql postgresql-contrib
    sudo -u postgres createuser <username>
    sudo -u postgres createdb <dbname>
    sudo -u postgres psql
    psql=# ALTER USER <username> WITH ENCRYPTED PASSWORD '<new_password>';
    psql=# GRANT ALL PRIVILEGES ON DATABASE <dbname> TO <username> ;
    \q
    ```

3. Change Apache Settings
    ```bash
    nano /etc/apache2/sites-available/000-default.conf
    ```

4. Change configuration as follows
    ```xml
    <VirtualHost *:80>
        ServerName localhost
        ServerAdmin webmaster@localhost

        Alias /static /var/www/project_dir/django_project/static/static_root
        <Directory /var/www/project_dir/django_project/static/static_root>
            Require all granted
        </Directory>

        <Directory /var/www/project_dir/django_project/django_project>
            <Files wsgi.py>
                Require all granted
            </Files>
        </Directory>

        WSGIDaemonProcess django_project python-path=/var/www/project_dir/env/lib/python3.6/site-packages python-home=/var/www/project_dir/django_project
        WSGIProcessGroup django_project
        WSGIScriptAlias / /var/www/project_dir/django_project/django_project/wsgi.py


        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    ```

5. Add User Permissions 
    ```bash
    adduser $USER www-data
    chown www-data:www-data /var/www/project_dir/django_project/
    chmod -R 775 /var/www/project_dir/env/
    ```

6. Restart Apache
    ```bash
    apachectl restart
    ```
