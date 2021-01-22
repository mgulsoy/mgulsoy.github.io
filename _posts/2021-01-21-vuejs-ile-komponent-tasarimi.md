---
layout: post
title: "Vue.js 3 İle Komponent Tasarımı"
date: 2021-01-21
tags: [vuejs]
---

Vue.js, tasarım işini kolaylaştıran bir framework. Özellikle *model binding* yapıyor olması kodlama yükünü oldukça azaltıyor. Bu framework'ün en iyi taraflarından biri de tekrar kullanılabilir komponentler yapıp kullanmaya izin vermesi.
Bu yazımda bir komponent tasarlayacağız ve bu komponenti browser ile kullanılabilecek şekilde paketleyeceğiz.
Hadi başlayalım.

## Hazırlık

Bu işi yapabilmek için ben aşağıda belirttiğim araçları kullandım. Önce ilgili olan araçların kurulumunu anlatacağım:

## Araçlar
* **Visual Studio Code**
  * Visual Studio Code kurulumunu detaylı olarak anlatmayacağım. Arzu edenler internette pek çok kaynak bulabilirler.
* **Node.js**
  * Node.js işimizin çekirdeğini oluşturan komponent. Node.js kurulumunu da detaylı anlatmıyorum.
* **Vue.Cli**
  * Bu paket ile vue.js projeleri tasarlayıp kodlayabilirsiniz. Vue.js kodlama işini oldukça kolaylaştıran bir araç.

## Kurulum

### Vue.Cli

Node.js uygulamasını Windows 10 bilgisayarımıza kurduğumuzda **Node.js Command Prompt** kısayolunu da yükler. Bu kısayol, Node.js ortam değişkenleri hazır olarak tanımlanmış bir kabuk (Shell) sunar. Kısayolu çalıştırarak **npm** paket yöneticisini kullanabiliriz. Paket yöneticisi ile **Vue.Cli** paketini sisteme yükleyeceğiz. Bunun için aşağıdaki komutu yazarız:

```npm install -g @vue/cli ```

Bu komutu çalıştırdığımızda sistemimize **Vue.Cli** paketi yüklenecektir. Bu işlem internet bağlantısı hızına göre biraz zaman alabilir.

