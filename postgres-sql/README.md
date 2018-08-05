```
curl https://ftp.postgresql.org/pub/source/v9.6.9/postgresql-9.6.9.tar.gz | tar zx -C .

yum -y groupinstall "Development Tools"
 && yum -y install readline readline-devel \
 && yum -y install zlib-devel \
 && yum -y install -y openssl-devel \
 && yum -y install libxml2-devel

useradd pg-user -u 2303 -g 1000 -d /home/pg-user -s /bin/bash
cd /tmp/postgresql-9.6.9/
./configure --prefix /opt/postgresql-9.6.9/pg_home  --with-libxml --with-openssl \
 && make world \
 && make install-world

mkdir /opt/postgresql-9.6.9/pg_data
su - pg-user

vi .bash_profile
----
export PGHOME=/opt/postgresql-9.6.9/pg_home
export PGDATA=/opt/postgresql-9.6.9/pg_data
export PGDATABASE=pg-user
export PGPORT=5432
export PATH=$PGHOME/bin:.:$PATH
----

source .bash_profile


pg_ctl start -w -t 60
psql -h localhost -p 5432 -U pg-user -d postgres /* データベースを指定して接続 */
ps -ef | grep postgres

```

