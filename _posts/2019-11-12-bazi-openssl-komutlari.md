---
layout: post
title: "Openssl ile kullanılabilecek bazı komutlar"
date: 2019-11-12
tags: [openssl,ssl]
---

Bu yazıda pratik bazı openssl komutları bulunmaktadır. Bu komutlar aracılığı ile OpenSSL kullanıp aracın sağladığı işlemler yapılabilir.
Burada yer alan komutlar OpenSSL aracını linux, macos gibi sistemlerin komut satırı (terminal) aracılığı ile kullanmayı sağlar. Windows 
işletim sistemi ile de kullanılabilir. Fakat bunun için windows işletim sistemine bu aracın kurulması gereklidir!

###Komutlar

##Bir private-key (gizli anahtar) oluşturma işlemi:

Bu komut ile 4096 bitlik bir *rsa* gizli anahtar oluşturulur ve `server.key` adlı dosyaya kaydedilir.

```sh
openssl genrsa -out server.key 4096
```

##Kendinden imzalı bir sertifika oluşturma:

Bu komut ile 4096 bitlik *rsa* sertifikası oluşturulur. Bu sertifika 365 gün `-days 365` geçerlidir. Yeni Private-key `server.key` 
dosyasında, sertifika ise `server.crt`dosyasında yer alır.

```sh
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:4096 -keyout server.key -out server.crt
```

##Daha önceden var olan bir private-key için sertifika imza isteği (CSR) oluşturma:

Daha önceden var olan bir private-key (gizli anahtar) için sertifika imza isteği oluşturur. Varolan private-key `-key server.key` 
parametresi ile `server.key` adlı dosydan okunur. Sertifika isteği dosyası ise `-out server.csr` parametresi ile `server.csr` 
dosyasına konur.

```sh
openssl req -out server.csr -key server.key -new
```

##Önceden var olanbir sertifika için imza isteği oluşturma:

Daha önceden var olan bir sertifika için imza isteği oluşturur. Varolan sertifika `-in server.crt` parametresi ile `server.crt` 
dosyasından okunur. Bu işlem için sertifikanın private-key bileşenine de ihtiyaç vardır. `-signkey server.key` parametresi ile 
private-key `server.key` dosyasından okunur. Sertifika isteği dosyası ise `-out server.csr` parametresi ile `server.csr` 
dosyasına konur.

```sh
openssl x509 -x509toreq -in server.crt -out server.csr -signkey server.key
```

##Bir private-key üzerinden parola korumasını kaldırma:

Bu işlem parola ile korunan bir private-key dosyasındaki korumayı kaldırır. Bu işlem için tabiki parolayı bilmek gereklidir. Yoksa
bedavaya köfte yok. Parola ile korunan dosya *pem* formatındadır. Komutu çalıştırdığınızda size varolan parolayı sorar. `-in server.pem`
parametresi ile alınan private-key `-out newserver.pem` parametresi ile `newserver.pem` dosyasına aktarılır.

```sh
openssl rsa -in server.pem -out newserver.pem
```


##Revoke işlemi yapılan sertifika seri numaralarının listesini okuma:

Sertifika hakları elinden alınan (revoke) sertifikaların seri numaralarının bulunduğu listeyi okur. `-inform DER` parametresi ile giriş
dosyasının *DER* formatında olduğu belirtilir. 

```sh
openssl crl -inform DER -text -noout -in list.crl
```


Check a certificate signing request (CSR)

```sh
openssl req -text -noout -verify -in server.csr
```


Check a private key

```sh
openssl rsa -in server.key -check
```


Check a public key

```sh
openssl rsa -inform PEM -pubin -in pub.key -text -noout
openssl pkey -inform PEM -pubin -in pub.key -text -noout
```


Check a certificate

```sh
openssl x509 -in server.crt -text -noout
openssl x509 -in server.cer -text -noout
```


Check a PKCS#12 file (.pfx or .p12)

```sh
openssl pkcs12 -info -in server.p12
```


Verify a private key matches an certificate

```sh
openssl x509 -noout -modulus -in server.crt | openssl md5
openssl rsa -noout -modulus -in server.key | openssl md5
openssl req -noout -modulus -in server.csr | openssl md5
```


Display all certificates including intermediates

```sh
openssl s_client -connect www.paypal.com:443
```


Convert a DER file (.crt .cer .der) to PEM

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
