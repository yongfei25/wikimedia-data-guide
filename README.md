# Wikimedia Data Guide

Guide to store and analyze Wikimedia data locally.

## TODO
- [ ] Wikipedia database
- [ ] Wikipedia search index

## Wikipedia
### 1. Download Wikipedia data dumps
```bash
export $DATADIR=data
export $WIKI=zhwiki
mkdir -p $DATADIR/$WIKI && cd $DATADIR/$WIKI
wget -i $WIKI.links
```

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
# Load page articles
java -jar mediawiki-tools-mwdumper/target/mwdumper-1.25.jar \
--format=mysql:1.25 \
~$DATADIR/$WIKI/$WIKI-20170801-pages-articles1.xml.bz2 | \
mysql -h 127.0.0.1 -u dataUser -pdataUserPassword $WIKI

## TODO
# Load the rest of the databases
```

### 4. Querying the database
```bash
# to connect to mysql, ensure you have mysql-client installed
sudo apt-get install mysql-client -y
mysql -h 127.0.0.1 -u dataUser -pdataUserPassword zhwiki
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