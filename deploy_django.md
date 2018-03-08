On host machine
```bash
ssh-keygen -R server_ip
ssh root@server_ip
```
Prerequisites
```bash
apt-get update
apt-get upgrade
```

1. Installing Python3.6
    > According to https://tecadmin.net/install-python-3-6-ubuntu-linuxmint/#
    ```bash
    apt-get install build-essential checkinstall
    apt-get install libreadline-gplv2-dev  libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev
    cd /usr/src
    wget https://www.python.org/ftp/python/3.6.4/Python-3.6.4.tgz
    tar xzf Python-3.6.4.tgz
    cd Python-3.6.4
    ./configure --enable-optimizations
    make altinstall
    cd .. $$ rm Python-3.6.4.tgz
    ```
2. Deploy with one of the following methods

    a.) [Deploying a Django Application on Remote Server Using Apache+mod_wsgi](deploy_django.md)

    b.) [Deploying a Django Application on Remote Server Using Gunicorn+Nginx](deploy_django_gunicorn_nginx.md)