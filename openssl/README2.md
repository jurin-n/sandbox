## プロフェッショナル SSL/TLS読んで試したことを書きなぐる場所

### RSA鍵の生成
```
openssl genrsa -aes256 -out fd.key 2048
```

### RSA鍵の構造の確認
```
openssl rsa -text -in fd.key
```

### 公開鍵の部分だけを取り出す
```
openssl rsa -in fd.key -pubout -out fd-public.key
```

### ECDSA鍵の生成 *secp256r1という名前付き曲線を使って生成
```
openssl ecparam -genkey -name secp256r1 | openssl ec -out ec.key -aes128
```
