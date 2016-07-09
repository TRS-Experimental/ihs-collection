---
---

# Install notes - Collection Space

**OS:** CentOS release 6.7

Install base software:

    yum install ant maven ImageMagick ftp

Install database software:

    yum install postgresql-server
    service postgresql initdb
    service postgresql start
    chkconfig postgresql on

Setup database

    sudo su - postgres
    cd /var/lib/pgsql/data/

    vi pg_hba.conf 

    # IPv6 local connections:
    # host    all         all         ::1/128               ident

    # IPv4 local connections:
    host    all             csadmin         samehost                md5
    host    all             nuxeo           samehost                md5
    host    cspace          cspace          samehost                md5

    vi  postgresql.conf

    max_prepared_transactions = 64  

    service postgresql restart

    psql -U postgres -d template1

    CREATE FUNCTION pg_catalog.text(integer) RETURNS text STRICT IMMUTABLE LANGUAGE SQL AS 'SELECT textin(int4out($1));';
    CREATE CAST (integer AS text) WITH FUNCTION pg_catalog.text(integer) AS IMPLICIT;
    COMMENT ON FUNCTION pg_catalog.text(integer) IS 'convert integer to text';
 
    CREATE FUNCTION pg_catalog.text(bigint) RETURNS text STRICT IMMUTABLE LANGUAGE SQL AS 'SELECT textin(int8out($1));';
    CREATE CAST (bigint AS text) WITH FUNCTION pg_catalog.text(bigint) AS IMPLICIT;
    COMMENT ON FUNCTION pg_catalog.text(bigint) IS 'convert bigint to text';

    \c postgres

    CREATE ROLE csadmin LOGIN PASSWORD 'replacemewithyourpassword' SUPERUSER INHERIT NOCREATEDB NOCREATEROLE;

    service postgresql restart

Install Java

Ensure Java 1.7.0 is installed.

    java -version


Install CollectionSpace

    cd /usr/local/share
    ftp nightly.collectionspace.org [Username: anonymous]
    cd pub/collectionspace/releases/4.3
    get cspace-server-4.3.tar.gz
    exit

    tar -zxvof cspace-server-4.3.tar.gz
    chmod u+x apache-tomcat-7.0.57/bin/*.sh
    rm cspace-server-4.3.tar.gz