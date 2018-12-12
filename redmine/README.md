```
docker run -d --name db-for-redmine -e POSTGRES_PASSWORD=dev123 -e POSTGRES_USER=redmine postgres
```

```
docker run -d --name redmine \
    -v $HOME/git/sandbox/redmine:/usr/src/redmine/files \
    --link db-for-redmine:postgres \
    -p 3000:3000 redmine
```

