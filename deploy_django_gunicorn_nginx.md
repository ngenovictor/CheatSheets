## How To Deploy A Django Application Using [Gunicorn](http://gunicorn.org/) + [Nginx](https://www.nginx.com/)

Prerequisites
```bash
sudo apt-get update
sudo apt-get upgrade

```

##According to Instructions in:
> https://simpleisbetterthancomplex.com/tutorial/2016/10/14/how-to-deploy-to-digital-ocean.html
1. Install Dependencies
```bash
apt-get install build-essential libpq-dev python-dev
```
2. Install PostgreSQL
```bash
apt-get install postgresql postgresql-contrib
```
2. Install NGINX
```bash
apt-get install nginx
```
3. Install, start and enable Supervisor
```bash
apt-get install supervisor
systemctl enable supervisor
systemctl start supervisor
```
4. Install VirtualENV
```bash
apt-get install python-virtualenv
```
5. Configure postgresql
```bash
su postgres 
createuser <username>
createdb <dbname>
psql
psql=# ALTER USER <username> WITH ENCRYPTED PASSWORD '<new_password>';
psql=# GRANT ALL PRIVILEGES ON DATABASE <dbname> TO <username> ;
psql=# ALTER USER <username> CREATEDB;
\q
exit
```
6. Install GIT
```bash
apt install git
```
6. Configure The Application User
```bash
adduser user
gpasswd -a user sudo
su - user
```
7. Setup the application
```bash
virtualenv -p python3.6 .
source bin/activate
git clone https://project.git
cd project
pip install -r requirements.txt
nano project/local_settings.py
python manage.py makemigrations
python manage.py migrate
python manage.py collectstatic
python manage.py test
cd ..
```
8. Configure Gunicorn
```bash
nano bin/gunicorn_start
```
9. Add the following to the file
```bash
#!/bin/bash

NAME="project_name"
DIR=/home/user/project_name
USER=user
GROUP=user
WORKERS=3
BIND=unix:/home/user/run/gunicorn.sock
DJANGO_SETTINGS_MODULE=project_name.settings
DJANGO_WSGI_MODULE=project_name.wsgi
LOG_LEVEL=error

cd $DIR
source ../bin/activate

export DJANGO_SETTINGS_MODULE=$DJANGO_SETTINGS_MODULE
export PYTHONPATH=$DIR:$PYTHONPATH

exec ../bin/gunicorn ${DJANGO_WSGI_MODULE}:application \
  --name $NAME \
  --workers $WORKERS \
  --user=$USER \
  --group=$GROUP \
  --bind=$BIND \
  --log-level=$LOG_LEVEL \
  --log-file=-
```
10. Make the gunicorn_start file is executable:
```bash
chmod u+x bin/gunicorn_start
```
11. Create a directory named run, for the unix socket file:
```bash
mkdir run
```
12. Configure Supervisor

Now what we want to do is configure Supervisor to take care of running the gunicorn server.

First let’s create a folder named logs inside the virtualenv:
```bash
mkdir logs
```
Create a file to be used to log the application errors:
```bash
touch logs/gunicorn-error.log
```

Create a new Supervisor configuration file:
```bash
sudo nano /etc/supervisor/conf.d/urban-train.conf
```
/etc/supervisor/conf.d/urban-train.conf
```bash
[program:project_name]
command=/home/urban/bin/gunicorn_start
user=user
autostart=true
autorestart=true
redirect_stderr=true
stdout_logfile=/home/user/logs/gunicorn-error.log
```
Reread Supervisor configuration files and make the new program available:
```bash
sudo supervisorctl reread
sudo supervisorctl update
```
Check the status:
```bash
sudo supervisorctl status project_name
```
13. Now you can control your application using Supervisor. If you want to update the source code of your application with a new version, you can pull the code from GitHub and then restart the process:
```bash
sudo supervisorctl restart project_name
```
14. Configure NGINX

Add a new configuration file named urban inside /etc/nginx/sites-available/:
```bash
sudo nano /etc/nginx/sites-available/project_name
```
/etc/nginx/sites-available/project_name
```
upstream app_server {
    server unix:/home/user/run/gunicorn.sock fail_timeout=0;
}

server {
    listen 80;

    # add here the ip address of your server
    # or a domain pointing to that ip (like example.com or www.example.com)
    server_name 107.170.28.172;

    keepalive_timeout 5;
    client_max_body_size 4G;

    access_log /home/user/logs/nginx-access.log;
    error_log /home/user/logs/nginx-error.log;

    location /static/ {
        alias /home/user/static/;
    }

    # checks for static file, if not found proxy to app
    location / {
        try_files $uri @proxy_to_app;
    }

    location @proxy_to_app {
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $http_host;
      proxy_redirect off;
      proxy_pass http://app_server;
    }
}
```
Create a symbolic link to the sites-enabled dir:
```
sudo ln -s /etc/nginx/sites-available/project_name /etc/nginx/sites-enabled/project_name
```
Remove NGINX default website:
```
sudo rm /etc/nginx/sites-enabled/default
```
Restart NGINX:
```
sudo service nginx restart
```

### Common Pitfalls
> https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/