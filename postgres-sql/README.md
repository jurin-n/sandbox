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

```
create table test_table(id int, name varchar);
```

### firewallに穴開ける
```
firewall-cmd --add-port=5432/tcp --zone=public --permanent && firewall-cmd --reload
```
```
参考
https://qiita.com/kenjjiijjii/items/1057af2dddc34022b09e
```


### 浮動小数点数のデータ型試す
```

CREATE TABLE float_test (r1 REAL,r2 REAL, d1 DOUBLE PRECISION, d2 DOUBLE PRECISION ,n1 NUMERIC, n2 NUMERIC);

insert into float_test(r1,r2,d1,d2,n1,n2) VALUES(0.1,0.1,0.1,0.1,0.1,0.1),(0.1,0.1,0.1,0.1,0.1,0.1),(0.1,0.1,0.1,0.1,0.1,0.1),(0.1,0.1,0.1,0.1,0.1,0.1),(0.1,0.1,0.1,0.1,0.1,0.1),(0.1,0.1,0.1,0.1,0.1,0.1),(0.1,0.1,0.1,0.1,0.1,0.1),(0.1,0.1,0.1,0.1,0.1,0.1),(0.1,0.1,0.1,0.1,0.1,0.1),(0.1,0.1,0.1,0.1,0.1,0.1);

select * from float_test where r1=r2;
select * from float_test where d1=d2;
select * from float_test where n1=n2;
select * from float_test where r1=d1;
select * from float_test where r1=n1;
select * from float_test where d1=n1;

select sum(r1),sum(d1),sum(n1) from float_test;
select r1+r1+r1+r1+r1+r1+r1+r1+r1+r1 from float_test;
select d1+d1+d1+d1+d1+d1+d1+d1+d1+d1 from float_test;
select n1+n1+n1+n1+n1+n1+n1+n1+n1+n1 from float_test;
```
```
参考
https://oss-db.jp/measures/silver_sample_170904_01.shtml
```

### SQL
#### テスト用テーブルとデータ
```
CREATE TABLE dept (dept_id Integer, dept_name VARCHAR(500), PRIMARY KEY(dept_id));
CREATE TABLE emp (emp_id Integer ,emp_name VARCHAR(500), dept_id Integer REFERENCES dept(dept_id) , PRIMARY KEY(emp_id));
INSERT INTO dept(dept_id, dept_name) VALUES(1, '営業部'),(2, '開発部');
INSERT INTO dept(dept_id, dept_name) VALUES(3, 'マーケティング部'),(4, '総務部');
INSERT INTO emp(emp_id, emp_name, dept_id) VALUES(1,'鈴木一朗',1),(2,'松井秀喜',2),(3,'松坂大輔',1),(4,'佐々木主浩',2);
INSERT INTO emp(emp_id, emp_name, dept_id) VALUES(5,'山口蛍',1);
INSERT INTO emp(emp_id, emp_name, dept_id) VALUES(6,'トルシエ',NULL);
```

#### 内部結合
```
SELECT t1.dept_id,t1.dept_name,t2.emp_id, t2.emp_name
FROM dept t1 INNER JOIN emp t2
ON t1.dept_id = t2.dept_id
ORDER BY t2.emp_name,t1.dept_name
```

#### 完全外部結合
```
SELECT t1.dept_id,t1.dept_name,t2.emp_id, t2.emp_name
FROM dept t1 FULL OUTER JOIN emp t2
ON t1.dept_id = t2.dept_id
```

#### 左外部結合
```
SELECT t1.dept_id,t1.dept_name,t2.emp_id, t2.emp_name
FROM dept t1 LEFT OUTER JOIN emp t2
ON t1.dept_id = t2.dept_id
ORDER BY t2.emp_name,t1.dept_name
```

#### 右外部結合
```
SELECT t1.dept_id,t1.dept_name,t2.emp_id, t2.emp_name
FROM dept t1 RIGHT OUTER JOIN emp t2
ON t1.dept_id = t2.dept_id
ORDER BY t2.emp_name,t1.dept_name
```

### 結合キーが同じ名前ならばUSING句も使える
```
SELECT t1.dept_id,t1.dept_name,t2.emp_id, t2.emp_name
FROM dept t1 INNER JOIN emp t2 USING(dept_id)
ORDER BY t2.emp_name,t1.dept_name
```
```
参考
https://wa3.i-3-i.info/word1263.html
https://en.wikipedia.org/wiki/Join_(SQL)
```
