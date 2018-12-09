### 初期化
```
mvn archetype:generate -B \
    -DarchetypeGroupId=org.apache.maven.archetypes \
    -DarchetypeArtifactId=maven-archetype-quickstart \
    -DarchetypeVersion=1.3 \
    -DgroupId=foo \
    -DartifactId=bar \
    -Dversion=1.0-SNAPSHOT \
    -Dpackage=foobar
```

### テスト用DB(postgresql)
#### 起動コマンド *docker利用
```
docker run --name postgres-dev -e POSTGRES_PASSWORD=dev123 -p 5432:5432 -d postgres 
```
#### テスト用DBユーザーなど
```
ユーザー：postgres
パスワード：dev123
データーベース：postgres
```

### テスト用DB(sqlserver)
#### 起動コマンド *AWS利用
TODO:CFnテンプレート作成予定。今はAWSのprivate subnetにSQL server起動。踏み台のEC2も含めマネージメントコンソールで起動。
DB接続するために接続元PCで下記のsshコマンド実行して、ポートフォワードする必要あり。

```
ssh -i "**利用するキーペア設定**.pem" -N ec2-user@ec2-nnn-nnn-nnn-nnn.ap-northeast-1.compute.amazonaws.com -L 11433:db1.***********.ap-northeast-1.rds.amazonaws.com:1433
```

#### テスト用DBユーザーなど
```
ユーザー：admin
パスワード：***
データーベース：dev *DBインスタンスあげたら CREATE DATABASE dev; で作成必要。
```


### Flyway実行
#### 接続先データベースよりbaseline作成し、flyway_schema_historyテーブル作成。
```
# postgresqlに接続する場合
mvn -Dflyway.configFiles=src/main/resources/postgresql.conf flyway:baseline
```
```
# sqlserverに接続する場合
mvn -Dflyway.configFiles=src/main/resources/sqlserver.conf flyway:baseline
```

参考：https://flywaydb.org/documentation/maven/baseline


#### src/main/resources/db/migration 配下に格納のDDL,DMLを反映
```
# postgresqlに接続する場合
mvn -Dflyway.configFiles=src/main/resources/postgresql.conf flyway:migrate
```
```
# sqlserverに接続する場合
mvn -Dflyway.configFiles=src/main/resources/sqlserver.conf flyway:migrate
```
参考：https://flywaydb.org/documentation/maven/migrate

