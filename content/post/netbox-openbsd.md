---
title: "Netbox Openbsd 6.6"
date: 2020-03-29T09:54:33-04:00
draft: false
tags: ["homelab", "hardware"]

---
Netbox is an inventory tool for keeping track of things like racks and servers.  While it is powerful enough to account for small to medium sized data centers, it also works well for homelabs.  I keep my inventory of test hardware in Netbox and use it to plan network changes before I start actually changing cabling and settings.

Since Netbox is a django application, it will happily run anywhere python will run.  However, most tutorials focus on Linux which is the most common deployment scenario these days.  I've maintained my home network with OpenBSD for years now and like to keep it around for a handful of services I haven't transitioned yet.  In this blog post, I'll cover installing Netbox in a python3 virtual environment and exposing it to my internal network via nginx and gunicorn.

For this tutorial, I'm using an OpenBSD 6.6 machine with python 3.7 and v2.7.11 of Netbox.  I'll be downloading and installing Netbox from a github branch rather than a release.

### Installing Packages

OpenBSD has a package system you can interact with through the `pkg_add` command.  Chances are good that you've already got several of the prerequisites for Netbox installed.  In addition to the two database tools, you'll also need virtualenv, git and nginx installed.

```bash
# Install your prerequisites
doas pkg_add git nginx py3-virtualenv
# Install Redis and Postgres
doas pkg_add redis postgresql-server
doas rcctl enable redis
doas rcctl enable postgres
doas rcctl start redis
doas rcctl start postgres
```

## Creating the directory structure and virtual environment

I considered several directories for installing nextbox.  Ultimately, putting the django code in `/var/www/netbox` works best for my backup and recovery strategy.  Some alternative options I considered based on reading through other tutorials included `/opt/netbox` and `/home/netbox`.

```bash
# All commands are as my normal login user
doas mkdir /var/www/netbox
doas chown alt /var/www/netbox
cd /var/www/
git clone https://github.com/digitalocean/netbox netbox
cd netbox
git checkout v2.7.11
virtualenv-3 --system-site-packages env
source env/bin/activate
pip3 install -r requirements.txt
```

## Setting up the database

_TODO:_ Reference the upstream docs for setting up postgres and the netbox database

## Setting up django

_TODO:_ Reference the upstream docs for collectstatic and migrations

## Setting the SECRET_KEY

Django needs a bit of randomness in the settings file to use in several parts of the system.  It needs to be secret and never reused.  There are helpers functions in the django world to create this randomness as part of generating the initial scaffolding of the site.  Netbox ships with this variable set to an empty string.  Before we go live, we need to fill it in with 50 random characters.  I generate mine with the following snippet and then put it in the configuration file.

```python
import random
print(''.join([random.SystemRandom().choice('abcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*(-_=+)') for i in range(50)]))
```

## Configuring Netbox BASE_PATH

I run serveral web accessible tools on my OpenBSD box at separate paths.  I want Netbox to fit in at `/netbox/` on the server rather than working with virtual hosting.  That means setting the `BASE_PATH` configuration in netbox as well as the appropriate location blocks in my nginx configuration.  These two settings allow netbox to exist at a subfolder in my nginx configuration without rewrite rules.

### Netbox Configuration
```python
# Text to include on the login page above the login form. HTML is allowed.
BANNER_LOGIN = ''

# Base URL path if accessing NetBox within a directory. For example, if installed at http://example.com/netbox/, set:
BASE_PATH = 'netbox/'
#BASE_PATH = ''

# Cache timeout in seconds. Set to 0 to dissable caching. Defaults to 900 (15 minutes)
CACHE_TIMEOUT = 900
```

### Nginx Configuration
```
	location /netbox/static {
		alias /var/www/netbox/netbox/static;
	}

	location /netbox/ {
                proxy_pass http://127.0.0.1:8000;
                proxy_set_header X-Forwarded-Host $server_name;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-Proto $scheme;
                add_header P3P 'CP="ALL DSP COR PSAa PSDa OUR NOR ONL UNI COM NAV"';
        }
```

### Configuring Gunicorn

Rather than passing all configuration to gunicorn through commandline arguments, I perfer to create a configruation file.  The snippet below will work to run our service with this commandline.

```bash
cd /var/www/netbox/netbox
source ../env/bin/activate
gunicorn --pid /tmp/netbox-gunicorn.pid --config ../gunicorn.conf.py netbox.wsgi
```

#### gunicorn.conf.py
```python
import multiprocessing

# The IP address (typically localhost) and port that the Netbox WSGI process should listen on
bind = '127.0.0.1:8000'

# Number of gunicorn workers to spawn. This should typically be 2n+1, where
# n is the number of CPU cores present.
workers = multiprocessing.cpu_count() * 2 + 1

# Number of threads per worker process
threads = 3

# Timeout (in seconds) for a request to complete
timeout = 120

# The maximum number of requests a worker can handle before being respawned
max_requests = 5000
max_requests_jitter = 500
```

### References

* https://netbox.readthedocs.io/en/stable/
* https://blog.jasper.la/setting-up-netbox-on-openbsd.html
* https://github.com/netbox-community/netbox/tree/v2.7.11
* https://gist.github.com/ndarville/3452907


