# Wikimedia Data Guide

Guide to store and analyze Wikimedia data locally.

## Wikipedia
### 1. Download Wikipedia data dumps
```bash
mkdir -p zhwiki && cd zhwiki
wget -i zhwiki.links
```

### 2. Install mediawiki-tools-mwdumper
```bash
git clone git@github.com:wikimedia/mediawiki-tools-mwdumper.git
mvn package
```

### 3. Start Mysql server
```bash
mkdir -p /data/mysql
docker-compose -f docker-compose-mysql.yml up -d

# to connect to mysql, ensure you have mysql-client installed
sudo apt-get install mysql-client -y
mysql -h 127.0.0.1 -u dataUser -pdataUserPassword dataDb
```

### 3. Load data into database
```bash
java -jar ../../mediawiki-tools-mwdumper/target/mwdumper-1.25.jar \
--output=gzip:pages-articles1.sql.gz
--format=mysql:1.5 \
--filter=latest \
~/storage1/wikipedia/zh/zhwiki-20170801-pages-articles1.xml.bz2

java -jar ../../mediawiki-tools-mwdumper/target/mwdumper-1.25.jar \
--format=mysql:1.5 \
--filter=latest \
~/storage1/wikipedia/zh/zhwiki-20170801-pages-articles1.xml.bz2 | \
mysql -h 127.0.0.1 -u dataUser -pdataUserPassword dataDb
```