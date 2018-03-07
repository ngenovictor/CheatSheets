## How To Deploy A Django Application Using [Gunicorn](http://gunicorn.org/) + [Nginx](https://www.nginx.com/)

Prerequisites
```bash
sudo apt-get update
sudo apt-get upgrade

```

##According to Instructions in:
> https://simpleisbetterthancomplex.com/tutorial/2016/10/14/how-to-deploy-to-digital-ocean.html
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

### Common Pitfalls
> https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/