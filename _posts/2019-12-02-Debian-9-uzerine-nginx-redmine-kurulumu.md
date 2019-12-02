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

## Kurulum

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

Dosya içeriği **yaml** formatındadır. Bu sebeple ayar girdilerinin başındaki boşluklara dikkat etmek şart. Bizim konfigrasyonumuzda 2 adet boşluk bulunmakta. **TAB** yada başka boşluk **vermemelisiniz!**

Dosya içeriğini belirledikten sonra `CTRL+O` ve ardından `CTRL+X` tuşlarına basarak kaydedip dosyayı kapatırız.

### Paket Bağımlılıkları

* Bundler
```bash
root@debian-s-9:~# cd /usr/share/redmine
root@debian-s-9:~# gem install bundler
...
```

**NOT: Benim yüklediğim paket bundler aracının 1.9 sürümünü gerektiriyordu. Yukarıdaki komut en son sürümünü yükleyecektir. Aşağıdaki komutları çalıştırırken bundler versiyonu ile ilgili bir hata alırsanız `gem install bundler --version 1.9` komutu ile 1.9 sürümünü yükleyin!**

* Bundle paketleri
```bash
root@debian-s-9:~# cd /usr/share/redmine
root@debian-s-9:~# bundle install
...
root@debian-s-9:~# bundle exec rake db:migrate
...
root@debian-s-9:~# bundle exec rake redmine:load_default_data
...
```

Bu aşamada redmine kurulumu tamamlandı. Fakat henüz sunamıyoruz. Sunum yapabilmek için **nginx** gerekli ayarları yapmalıyız. Önce bir site dosyası oluşturuyoruz:

```bash
root@debian-s-9:~# cd /etc/nginx/sites-available/
root@debian-s-9:~# touch redmine.bizimsiteadresi.com
...
root@debian-s-9:~# nano redmine.bizimsiteadresi.com
```

Dosya içeriği şu şekilde olabilir:

```
server {
        listen 80;
        listen [::]:80;

        server_name redmine.bizimsiteadresi.com;

        access_log /var/log/nginx/redmine.bizimsiteadresi.com.access.log;

        root /usr/share/redmine/public;
        passenger_enabled on;
        passenger_user www-data;
        error_page 404 /404.html;
        error_page 500 /500.html;

        client_max_body_size    10m;
}
```

Ayarlar içinde dikkat edilmesi gereken parametreler şunlardır:
* **root /usr/share/redmine/public;** : Redmine kurulumunun yolu
* **passenger_enabled on;** : Passenger modülü aktif
* **passenger_user www-data;** : Varsayılan olarak *nginx* sistem kullanıcısı olarak *www-data* kullanır. Bunu ayarlamazsak passenger *nobody* ile oturumu başlatacak ve erişim sıkıntısı ortaya çıkacaktır.
* **client_max_body_size    10m;** : Bu direktif ile upload edilecek maksimum dosya boyutu belirlenir. Bu ayarda 10 MB'tır.

## Sonuç

Şimdi *nginx* hizmetini yeniden başlatarak *redmine* sistemini kullanmaya başlayabiliriz.

```bash
root@debian-s-9:~# service nginx restart
```

Browser'dan sitemizi açıp kullanmaya başlayabiliriz.


