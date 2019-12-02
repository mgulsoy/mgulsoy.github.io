---
layout: post
title: "Debian 9 Üzerine Nginx ve Redmine Kurulumu"
date: 2019-11-19
tags: [redmine]
---

Redmine, ruby ile kodlanmış, açık kaynaklı bir proje takip sistemidir. Şimdi kendi debian sunucumuza bu sistemi nasıl kurarız ona bakalım:

## Hazırlık
Tabiki bu işlemi yapabilmek için bir Debian 9 sistemimizin olması gerekiyor. Debian kurulumu için detayları burada vermeyeceğim. Debian 9 kurulu işletim sistemimizi güncel hale getirerek işleme başlarız. Bunun için `apt` aracını kullanarız:

**İşlemleri `root` hesabı ile yapıyor olacağız.**

```bash
root@debian-s-9:~# apt update
...
root@debian-s-9:~# apt upgrade
...
```

### Gereksinimler

Şimdi gereksinimleri yüklüyoruz:

* Postgresql veritabanı

```bash
root@debian-s-9:~# apt-get -y install postgresql
...
``` 

* https transport

```bash
root@debian-s-9:~# apt-get -y install apt-transport-https
...
```

* Nginx

```bash
root@debian-s-9:~# apt-get -y install nginx
...
``` 

* Redmine

```bash
root@debian-s-9:~# apt-get -y install redmine-pgsql
...
``` 

* libnginx-mod-http-passenger: redmine *passenger* adı verilen sistem üzerinde çalışmakta. redmine kurulumu passenger modülünü de yüklemekte fakat *passenger* modülünün *nginx* ile konuşmasını sağlayan modül yüklenmemekte. Bu modül farklı bir repo'da bulunmakta. Önce o repo'nun sisteme kayıt edilmesi gerekir. Önce bu kaydı yapıp daha sonra modülü indireceğiz.

```bash
root@debian-s-9:~# echo deb https://oss-binaries.phusionpassenger.com/apt/passenger stretch main > /etc/apt/sources.list.d/passenger.list
...
root@debian-s-9:~# apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7
...
root@debian-s-9:~# apt-get update
...
root@debian-s-9:~# apt-get -y install libnginx-mod-http-passenger
...
``` 

İşlemi yaptıktan sonra `/usr/share/nginx/modules-available/`klasörünün içine baktığımızda `mod-http-passenger.load` modülünü görmeliyiz. Sonrasında Redmine yapılandırmasına geçebiliriz:

* Veritabanı İşlemleri: Redmine tarafından kullanılacak olan veritabanını oluşturuyoruz, önce postgresql cli arabirimini kullanıyoruz:

```bash
root@debian-s-9:~# su - -c "psql postgres" postgres
psql (9.6.15)
Type "help" for help.

postgres=# CREATE ROLE redmine LOGIN ENCRYPTED PASSWORD 'çok_gizli_parola' NOINHERIT VALID UNTIL 'infinity';
...
postgres=# CREATE DATABASE redmine WITH ENCODING='UTF8' OWNER=redmine;
...
postgres=# \q
``` 
Veritabanını oluşturduktan sonra Redmine ayar dosyasını oluşturuyoruz. Bu işlemi yapabilmek için şu komutları kullanarak ayar dosyasının bulunduğu klasöre gidiyoruz ve `nano` editörü ile dosyayı açıyoruz.

```bash
root@debian-s-9:~# cd /usr/share/redmine/instances/default/config
root@debian-s-9:~# nano database.yml
```
Ayar dosyası aşağıdaki gibi olmalıdır:

```yaml
production:
  adapter: postgresql
  database: redmine
  host: localhost
  username: redmine
  password: "çok_gizli_parola" 
  encoding: utf8
```




