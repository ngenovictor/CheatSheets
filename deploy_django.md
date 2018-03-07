1. Installing Python3.6
    > According to https://tecadmin.net/install-python-3-6-ubuntu-linuxmint/#
2. Setting Up environment and Apache2
    > According to https://github.com/codingforentrepreneurs/Guides/blob/master/all/Debian_Install_Django_Apache2.md

3. Installing PostgreSQL
    > According to https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04
    ```bash
    apt-get install postgresql postgresql-contrib
    sudo -i -u postgres
    createuser --interactive
    createdb username_above
    psql
    ALTER ROLE username WITH ENCRYPTED PASSWORD 'new_password';
    CREATE DATABASE dbname WITH OWNER username_above;
    \q
    exit
    ```

4. Change Apache Settings
    ```bash
    nano /etc/apache2/sites-available/000-default.conf
    ```
5. Change configuration as follows
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

6. Add User Permissions 
    ```bash
    adduser $USER www-data
    chown www-data:www-data /var/www/project_dir/django_project/
    chmod -R 775 /var/www/project_dir/env/
    ```

7. Restart Apache
    ```bash
    apachectl restart
    ```

