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

### テスト用DB
#### 起動コマンド
```
docker run --name postgres-dev -e POSTGRES_PASSWORD=dev123 -p 5432:5432 -d postgres 
```
#### テスト用DBユーザーなど
```
ユーザー：postgres
パスワード：dev123
データーベース：postgres
```

### Flyway実行
#### 接続先データベースよりbaseline作成し、flyway_schema_historyテーブル作成。
```
mvn -Dflyway.configFiles=src/main/resources/postgresql.conf flyway:baseline
```

参考：https://flywaydb.org/documentation/maven/baseline


#### src/main/resources/db/migration 配下に格納のDDL,DMLを反映
```
mvn -Dflyway.configFiles=src/main/resources/postgresql.conf flyway:migrate
```
参考：https://flywaydb.org/documentation/maven/migrate

