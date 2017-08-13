# Wikimedia Data Guide

Guide to store and analyze Wikimedia data locally.

## Dependencies
- Docker Compose
- Java
- Mvn
- Mysql client

## TODO
- [x] Wikipedia database
- [ ] Wikipedia search index

## Wikipedia
### 1. Download Wikipedia data dumps
```bash
export DATADIR=data
export WIKI=zhwiki
wget -i $WIKI.links -P $DATADIR/$WIKI
```
Complete list of backup from Wikimedia:

https://dumps.wikimedia.org/backup-index.html


### 2. Install `mediawiki-tools-mwdumper`
```bash
git clone git@github.com:wikimedia/mediawiki-tools-mwdumper.git
cd mediawiki-tools-mwdumper && mvn package && cd ..
```

### 3. Start `Mysql` server with `docker-compose`
```bash
docker-compose -f docker-compose-mysql.yml up -d
```

### 3. Load data into `zhwiki` database
```bash
# Load page articles, this populates the following tables:
# page, text, revision, redirect
java -jar mediawiki-tools-mwdumper/target/mwdumper-1.25.jar \
--format=mysql:1.25 \
$DATADIR/$WIKI/$WIKI-20170801-pages-articles1.xml.bz2 | \
mysql -h 127.0.0.1 -u dataUser -pdataUserPassword $WIKI

java -jar mediawiki-tools-mwdumper/target/mwdumper-1.25.jar \
--format=mysql:1.25 \
$DATADIR/$WIKI/$WIKI-20170801-pages-articles2.xml.bz2 | \
mysql -h 127.0.0.1 -u dataUser -pdataUserPassword $WIKI

java -jar mediawiki-tools-mwdumper/target/mwdumper-1.25.jar \
--format=mysql:1.25 \
$DATADIR/$WIKI/$WIKI-20170801-pages-articles3.xml.bz2 | \
mysql -h 127.0.0.1 -u dataUser -pdataUserPassword $WIKI

java -jar mediawiki-tools-mwdumper/target/mwdumper-1.25.jar \
--format=mysql:1.25 \
$DATADIR/$WIKI/$WIKI-20170801-pages-articles4.xml.bz2 | \
mysql -h 127.0.0.1 -u dataUser -pdataUserPassword $WIKI

# Load the rest SQL files
zcat $DATADIR/$WIKI/*.sql.gz | mysql -h 127.0.0.1 -u dataUser -pdataUserPassword $WIKI
```

### 4. Querying the database
```bash
mysql -h 127.0.0.1 -u dataUser -pdataUserPassword $WIKI
mysql> select page_id, page_title from page limit 5;
+---------+-----------------------------+
| page_id | page_title                  |
+---------+-----------------------------+
|   79614 | !                           |
|  204837 | (52760)_1998_ML14           |
|   79288 | ,                           |
|   91743 | -1                          |
|   26227 | .30口径班用自动步枪         |
+---------+-----------------------------+
5 rows in set (0.00 sec)

```