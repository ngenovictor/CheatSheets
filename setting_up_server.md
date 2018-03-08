## Setting Up My Server Machine

Currently I am using Linode for my server requirements. I mostly do Python/Django stuff so the setup is geared towards such an environment


1. On host machine
```bash
ssh-keygen -R server_ip
ssh root@server_ip
```
2. Prerequisites
```bash
apt-get update
apt-get upgrade
```
2. Change server time
```bash
date
timedatectl set-timezone Africa/Nairobi
date
```
3. Installing Python3.6
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
cd .. && rm Python-3.6.4.tgz
```