# OpenSSLについて
今回はCentoOS7に入っているOpenSSLを利用。
バージョンは、 OpenSSL 1.0.2k-fips  26 Jan 2017。

# openssl_test.cnf について
OpenSSLに付属のopenssl.cnfをコピーして一部修正して作成。
OpenSSLインストールマシンの /etc/pki/tls/openssl.cnf に格納されている。
openssl.cnf.original としてここのリポジトリに格納。

# 試したこと
## CA作成、サーバ証明書作成、作成した証明書でWWWサーバー起動
### 自作のCA作成
```
openssl genrsa 2048 > ca.key
openssl req -config openssl_test.cnf -new -key ca.key \
  -subj "/C=JP/ST=Tokyo-to/L=Shinjuku-ku/O=Jurin-n.com/OU=CA dept./CN=Jurin-n.com CA" > ca.csr
openssl x509 -days 365 -req -signkey ca.key < ca.csr > ca.crt
```

### 証明書署名要求
```
openssl genrsa 2048 > server.key
openssl req -config openssl_test.cnf -new -key server.key -extensions v3_req \
  -subj "/C=JP/ST=Tokyo-to/L=Shinjuku-ku/O=Jurin-n.com//OU=IT dept./CN=dev1.test.local" > server.csr
```

### サーバ証明書(自己証明書)作成。ここで自作のCAで証明している。
```
mkdir demoCA
cd demoCA
mkdir private crl certs newcerts
echo "01" > serial
touch index.txt
cd ..
openssl ca -config openssl_test.cnf -days 365 -cert ca.crt -keyfile ca.key -in server.csr > server.crt
```

### 作成したサーバ証明書でWWWサーバー起動
```
openssl s_server -accept 1443 -cert server.crt -key server.key -WWW
```

### 起動したサーバーのポートがリッスンされてるか確認
```
lsof -i:1443 -P
```

### 作成したファイル類削除
```
rm ca.*
rm server.*
rm -rf demoCA/
```

# 参考資料
opensslコマンドの使い方
https://qiita.com/hana_shin/items/6d9de0847a06d8ee95cc

SubjectAltName対応 x.509 証明書を取得するためのCSRを作成する
https://qiita.com/saitara/items/eda74ac6a950122b5f31

独自SSLサーバ認証局(CA)作成とサーバ証明書発行
https://qiita.com/makoto1899/items/ef15372d4cf4621a674e
