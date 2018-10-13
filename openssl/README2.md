## プロフェッショナル SSL/TLS読んで試したことを書きなぐる場所

### RSA鍵の生成
```
openssl genrsa -aes256 -out jurin-n.com.local.key 2048
```

### RSA鍵の構造の確認
```
openssl rsa -text -in jurin-n.com.local.key
```

### 公開鍵の部分だけを取り出す
```
openssl rsa -in jurin-n.com.local.key -pubout -out jurin-n.com.local-public.key
```

### ECDSA鍵の生成　＊secp256r1という名前付き曲線を使って生成
```
openssl ecparam -genkey -name secp256r1 | openssl ec -out ec.key -aes128
```

### CSR(Certificate Signing Request)の生成　＊設定してファイルを利用
```
openssl req -new \
    -config jurin-n.com.local.conf \
    -key jurin-n.com.local.key \
    -out jurin-n.com.local.csr
```

### CSR内容の確認
```
openssl req -text -in jurin-n.com.local.csr -noout
```

### 自分自身の証明書に署名
```
openssl x509 \
    -req -days 365 \
    -in jurin-n.com.local.csr \
    -signkey jurin-n.com.local.key \
    -out jurin-n.com.local.crt
```

### 証明書を調べる
```
openssl x509 -text -in jurin-n.com.local.crt -noout
```