# How to migrate to the last OJS version

| **WARNING:** This documentation is still partial and not up to date. Use at your own risk. |
|:------------------------------------------------------------------------------------------:|

## Get the source

```bash
mkdir -p /dojo/sites
chown docker:docker /dojo -R
cd /dojo/sites
git clone https://github.com/pkp/docker-ojs.git
```

## Dockerize your existing OJS

### Create a new docker-site for your journal:

```bash
cd /dojo/sites
cp /dojo/sites/docker-ojs/versions/yourVersionPath journalName
```

Note: yourVersionPath is the full path of the version that is closest to yours.
For instance if you are on OJS 2.4.8-3 it will be 2.4.8-5 as follows:
```bash
cp /dojo/sites/docker-ojs/versions/2_4_8-5/alpine/apache/php5/ .
```

Edit your docker-compose.yml and uncomment (at least) all the volumes/* folders.
It will look like this:

```yaml
volumes:
  - /etc/localtime:/etc/localtime
   # Create files or folders first and check permisions, to avoid errors:
  - ./volumes/private:/var/www/files
  - ./volumes/public:/var/www/html/public
  - ./volumes/db:/var/lib/mysql
  - ./volumes/logs:/var/log/apache2
#      - ./config/ojs.config.inc.php:/var/www/html/config.inc.php
#      - ./config/apache.htaccess:/var/www/html/.htaccess
#      - ./config/php.custom.ini:/usr/local/etc/php/conf.d/custom.ini

```

### Copy the persistent data

From your old OJS, you only need "public" and "private" folders, and
a copy (dump) of your database.

```bash
cp -a /ojsFolder/webdata/private/* /dojo/sites/journalName/volumes/private
cp -a /ojsFolder/public/* /dojo/sites/journalName/volumes/public
```

**Important:** NEVER move your data, just copy... to keep your source site as a backup.

```bash
mkdir -p /dojo/sites/journalName/volumes/migration
mysqldump -u root --password=MYSQL_ROOT_PASSWORD OJS_DB --single-transaction --quick --lock-tables=true \
   > /dojo/sites/journalName/volumes/db/journalName.sql
```

### Fix permissions:

```bash
chown 100:101 /dojo/sites/journalName/volumes -R
chown 999:999 /dojo/sites/journalName/volumes/db -R
chown 999:999 /dojo/sites/journalName/volumes/logs/db -R
```

## Set you journal

Edit environmental variables to define your site:

```bash
vi /dojo/sites/journalName/.env
```

TO BE CONTINUED....
