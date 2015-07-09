---
layout: post
title: Automatically sync your IPython notebooks to Github
---
I wanted to create an automatic backup with version control for my IPython notebooks. To do this I used [Gitwatch](https://github.com/nevik/gitwatch)

###Useful Guides
-[Create a Repo](https://help.github.com/articles/create-a-repo/)

-[gitwatch as a service on Debian with supervisord](https://github.com/nevik/gitwatch/wiki/gitwatch-as-a-service-on-Debian-with-supervisord)

-[Generating SSH Keys](https://help.github.com/articles/generating-ssh-keys/#platform-linux)

-[Creating a Github repository](https://help.github.com/articles/create-a-repo/)]

## Setup Github
You'll need to create a repository and configure your IPython notebook directory as a repository

```Shell
git config --global user.name "Ray Bohac"
git config --global user.email "me@gmail.com"
ssh-keygen -t rsa -b 4096 -C "rbohac@gmail.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa

cd ~/<MYNOTEBOOKDIR>
git add .
git commit -m "first commit"
git remote add origin git@github.com:<USERNAME>/<REPO-NAME>.git
git push -u origin master

```

##Configure [gitwatch](https://github.com/nevik/gitwatch)

```Shell
apt-get install inotify-tools
cd ~
git clone https://github.com/nevik/gitwatch.git
sudo cp gitwatch/gitwatch.sh /usr/local/sbin/gitwatch
sudo chmod +x /usr/local/sbin/gitwatch
vi /usr/local/sbin/gitwatch 
```
set REMOTE="origin" in /usr/local/sbin/gitwatch 

##Create Startup Script

```Shell
sudo vi /etc/init/gitwatch.conf"
```

```Shell

#gitwatch - push notebook updates to github
#gitwatch Notebook Server
#Ray Bohac

description     "gitwatch"

start on runlevel [2345]
stop on runlevel [!2345]

respawn

pre-start script
    [ -d /var/run/gitwatch   ] || mkdir -p /var/run/gitwatch
end script

exec sudo -u ubuntu 2>>/dev/.initramfs/gitwatch.log /usr/local/sbin/gitwatch <MYNOTEBOOKDIR>
```

##Start the service

```Shell
sudo start ipython
```
