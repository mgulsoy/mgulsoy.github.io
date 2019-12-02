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

* Redmine

```bash
root@debian-s-9:~# apt-get -y install redmine-pgsql
...
``` 


