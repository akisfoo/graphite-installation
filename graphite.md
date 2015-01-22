Graphite Installation
=====================
Just the steps to install/configure the damn thing on debian.

## Packages
Install the following packages:
```
apt-get install python-pip python-dev python-cairo-dev
apt-get install postgresql libpq-dev python-psycopg2
apt-get install apache2 libapache2-mod-wsgi
pip install django
pip install django-tagging
pip install https://github.com/~/master
pip install whisper
pip install carbon
pip install graphite-web
```

## PostgreSQL
Create the following db/user:
```  
CREATE USER graphite WITH PASSWORD 'password';
CREATE DATABASE graphite WITH OWNER graphite;
\q
```

## Graphite Web App
Configure Graphite web app:

```
cd /opt/graphite/webapp/graphite
cp local_settings.py.example local_settings.py
```

Change the following values on local_settings.py
```
SECRET_KEY = 'your_secrey_key'
TIME_ZONE = 'Europe/Berlin'
USE_REMOTE_USER_AUTHENTICATION = True
DATABASES = {
  'default': {
      'NAME': 'graphite',
      'ENGINE': 'django.db.backends.postgresql_psycopg2',
      'USER': 'graphite',
      'PASSWORD': 'password',
      'HOST': '127.0.0.1',
      'PORT': ''
   }
}
GRAPHITE_ROOT = '/opt/graphite'
CONF_DIR = '/opt/graphite/conf'
STORAGE_DIR = '/opt/graphite/storage'
CONTENT_DIR = '/opt/graphite/webapp/content'
```

Sync the db:
```
cd /opt/graphite/webapp/graphite/
chown -R www-data:www-data /opt/graphite/storage/
```

## Carbon
Carbon configuration using the default values:
```
cd /opt/graphite/conf/
cp carbon.conf.example carbon.conf
cp storage-schemas.conf.example storage-schemas.conf
cd ..
```

Start carbon:
```
./bin/carbon-cache.py start
```

## Apache
Configure a graphite virtual host:

```
a2dissite 000-default
cd /opt/graphite/examples
cp example-graphite-vhost.conf /etc/apache/sites-available/graphite
cd /etc/apache2/sites-enabled
ln -s /etc/apache2/sites-available/graphite .
/etc/init.d/apache2 restart
```

## Sigh...
Start graphite... 
```
python /opt/graphite/bin/run-graphite-devel-server.py --port 8080 /opt/graphite/ &
```
