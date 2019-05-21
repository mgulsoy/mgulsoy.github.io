---
layout: post
title: "AVR platformunda ATmega328 için fuse ayarları"
date: 2019-05-01
tags: [avr]
---

Aslında bunu kendime not olarak yazıyorum. Bir projemde ATmega328p işlemci kullanıyorum. Bu işlemciyi aşağıdaki düzenek ile çalıştırmaktayım:

* Besleme: 5v
* Saat Frekansı: 16 MHz, harici kristal

Bu işlemciyi programlamadan önce fuse ayarları şu şekilde olmalı:

|FUSE|AYAR|
|----|----|
|LB|0xFF|
|HB|0xDF|
|EX|0xFD|

Daha detaylı açıklamayı sonra yine bu yazıda ekleyeceğim.
