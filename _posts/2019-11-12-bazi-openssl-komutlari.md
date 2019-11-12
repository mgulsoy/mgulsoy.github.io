---
layout: post
title: "Openssl ile kullanılabilecek bazı komutlar"
date: 2019-11-12
tags: [openssl, ssl]
---

Bu yazıda pratik bazı openssl komutları bulunmaktadır. Bu komutlar aracılığı ile OpenSSL kullanıp aracın sağladığı işlemler yapılabilir. Burada yer alan komutlar OpenSSL aracını linux, macos gibi sistemlerin komut satırı (terminal) aracılığı ile kullanmayı sağlar. Windows işletim sistemi ile de kullanılabilir. Fakat bunun için windows işletim sistemine bu aracın kurulması gereklidir!

### Komutlar

## Bir private-key (gizli anahtar) oluşturma işlemi:

Bu komut ile 4096 bitlik bir *rsa* gizli anahtar oluşturulur ve `server.key` adlı dosyaya kaydedilir.

```sh
openssl genrsa -out server.key 4096
```

## Kendinden imzalı bir sertifika oluşturma:

Bu komut ile 4096 bitlik *rsa* sertifikası oluşturulur. Bu sertifika 365 gün `-days 365` geçerlidir. Yeni Private-key `server.key` dosyasında, sertifika ise `server.crt` dosyasında yer alır.

```sh
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:4096 -keyout server.key -out server.crt
```

## Daha önceden var olan bir private-key için sertifika imza isteği (CSR) oluşturma:

Daha önceden var olan bir private-key (gizli anahtar) için sertifika imza isteği oluşturur. Varolan private-key `-key server.key` parametresi ile `server.key` adlı dosydan okunur. Sertifika isteği dosyası ise `-out server.csr` parametresi ile `server.csr` dosyasına konur.

```sh
openssl req -out server.csr -key server.key -new
```

## Önceden var olanbir sertifika için imza isteği oluşturma:

Daha önceden var olan bir sertifika için imza isteği oluşturur. Varolan sertifika `-in server.crt` parametresi ile `server.crt` dosyasından okunur. Bu işlem için sertifikanın private-key bileşenine de ihtiyaç vardır. `-signkey server.key` parametresi ile private-key `server.key` dosyasından okunur. Sertifika isteği dosyası ise `-out server.csr` parametresi ile `server.csr` dosyasına konur.

```sh
openssl x509 -x509toreq -in server.crt -out server.csr -signkey server.key
```

## Bir private-key üzerinden parola korumasını kaldırma:

Bu işlem parola ile korunan bir private-key dosyasındaki korumayı kaldırır. Bu işlem için tabiki parolayı bilmek gereklidir. Yoksa bedavaya köfte yok. Parola ile korunan dosya **pem** formatındadır. Komutu çalıştırdığınızda size varolan parolayı sorar. `-in server.pem` parametresi ile alınan private-key `-out newserver.pem` parametresi ile `newserver.pem` dosyasına aktarılır.

```sh
openssl rsa -in server.pem -out newserver.pem
```


## Revoke işlemi yapılan sertifika seri numaralarının listesini okuma:

Sertifika hakları elinden alınan (revoke) sertifikaların seri numaralarının bulunduğu listeyi okur. `-inform DER` parametresi ile giriş dosyasının **DER** formatında olduğu belirtilir. 

```sh
openssl crl -inform DER -text -noout -in list.crl
```


## Bir sertifika imzalama isteğini kontrol etme:

Bu komut ile birlikte daha önceden oluşturulmuş (veya başka bir yerden gelen) *sertifika imzalama isteği* dosyası kontrol edilir. `-in server.csr` parametresi ile `server.csr` dosyasından okunur `-text` ve `-noout` parametreleri ile ekrana bilgi olarak basılır.

```sh
openssl req -text -noout -verify -in server.csr
```


## Bir private-key kontrol etme:

Private-key dosyasının kontrolü sağlanır. `-in server.key` parametresi ile `server.key` dosyasından private-key okunur. `-check` parametresi ile kontrol sağlanır. İşlem **rsa** ile gerçekleştirilir.

```sh
openssl rsa -in server.key -check
```

## Bir public-key (açık anahtar) kontrol etme:

Bu komut ile bir public-key (*açık anahtar*) kontrol edilir ve sonuç ekrana bilgi olarak basılır. `-in pub.key` parametresi ile public-key `pub.key` dosyasından okunur. public-key dosyası **PEM** formatındadır.

```sh
openssl rsa -inform PEM -pubin -in pub.key -text -noout
openssl pkey -inform PEM -pubin -in pub.key -text -noout
```


## Bir sertifika kontrol etme:

```sh
openssl x509 -in server.crt -text -noout
openssl x509 -in server.cer -text -noout
```


## Bir PKCS#12 dosyası (.pfx ya da .p12) kontrol etme:

Bu dosya türü sertifika veya public-key, private-key içerebilir. `-in server.p12` parametresi ile `server.p12` adlı dosyadan bilgi okunur.

```sh
openssl pkcs12 -info -in server.p12
```


## Bir private-key'in bir sertifika ile eşleşiyor mu kontrol etme:

Bu komutlar **.crt, .key** ve **.csr** uzantılarında 3 dosya üzerinde işlem yapar ve *modulus* bileşenin *MD5* hash (özet) bilgisini ekrana basar. Bu hash (özet) bilgileri karşılaştırılarak eşleşme kontrol edilebilir. Hash (özet) bilgisi aynı ile eşleşme var demektir.

```sh
openssl x509 -noout -modulus -in server.crt | openssl md5
openssl rsa -noout -modulus -in server.key | openssl md5
openssl req -noout -modulus -in server.csr | openssl md5
```

## Ara sertifikalar dahil bütün sertifikaları görüntüle:

Bu komut ile `-connect www.paypal.com:443` parametresinde belirtilen bir *https* sunucusuna bağlanılır ve sunucunun verdiği sertifika zinciri ekrana basılır.

```sh
openssl s_client -connect www.paypal.com:443
```

## Bir DER dosyasını PEM dosyasına Çevirme:

Bir **DER** dosyasını (.crt .cer .der) bir **PEM** dosyasına çevirme işlemi yapar.

```sh
openssl x509 -inform der -in server.cer -out server.pem
```


Convert a PEM file to DER

```sh
openssl x509 -outform der -in server.pem -out server.der
```


Convert a PKCS#12 file (.pfx .p12) containing a private key and certificates to PEM

```sh
openssl pkcs12 -in server.pfx -out server.pem -nodes
```


Convert a PEM certificate file and a private key to PKCS#12 (.pfx .p12)

```sh
openssl pkcs12 -export -out server.pfx -inkey server.key -in server.crt -certfile CACert.crt
```


Generate a Diffie Hellman key

```sh
openssl dhparam -out dhparam.pem 2048
```


Encrypt files with rsautl

```sh
openssl rsautl -encrypt -in plaintext.txt -out encrypted.txt -pubin -inkey pubkey.pem
```


Decrypt files with rsautl

```sh
openssl rsautl -decrypt -in encrypted.txt -out plaintext.txt -inkey privkey.pem
```
