---
layout: post
title: "Esp32 ile Bluetooth LE Server"
date: 2019-11-19
tags: [esp32]
---

# HENÜZ YAZILMAKTADIR. GÜNCELLENECEKTİR!

Esp32 platformu, yapabildiklerine ve fiyatına bakıldığında oldukça avantajlı bir platform. Şimdi bir çevre birimi (Peripheral) ya da başka bir deyişle bir **BLE SERVER** oluşturalım:

## Geliştirme Ortamı

Yapacağımız işlemler için geliştirme ortamı olarak aşağıdaki bileşenleri kullanacağız:

* Visual Studio Code
* VS Code üzerinde Platformio eklentisi
* Platformio esp-arduino platformu
* Uyumlu bir esp32 geliştirme kartı

## Planlama

Şimdi projemizde neler yapacağız bunu planlayalım:

### Amaçlar

* Bluetooth LE kullanacağız.
* Pil durumunu raporlayacağız. (Bluetooth SIG standardında tanımlandığı gibi)
* Bir digital pin durumu okuyacağız ve bunu raporlayacağız.

### BLE Mimarisi

Bluetooth Low Energy (BLE) ya da Bluetooth 4, Bluetooth SIG tarafından standartları belirlenen kablosuz erişim yapısıdır. BLE birebir bağlantı şekilli yapılardan biraz farklıdır. BLE, enerji verimliliği göz önünde bulundurularak tasarlandığından ötürü kullanım şekli de buna göre evrilmiştir.

**BLE standardı 2 adet yapı tanımlar:**
* GAP (Generic Access Profile) : Cihazların birbirlerini bulması için bu yapı kullanılır.
* GATT (Generic ATTribute) : Cihazların birbirlerine bağlanması için bu yapı kullanılır.

**BLE standardı temel olarak şu rolleri tanımlar:**
* GATT Client: Genellikle MASTER veya CENTRAL olarak da bilinir. Bütün işi idare eden taraftır. Mesela bir Bluetooth kalp sensörüne bağlanan cep telefonu bu roldedir.
* GATT Server: Genellikle SLAVE veya PERIPHERAL olarak da bilinir. İdare edilen taraftır. Genelde pasiftir. Mesela bir bluetooth kalp sensörü bu roldedir.

GATT Server sunacak olduğu hizmetleri standartta tanımlandığı gibi listelemelidir. Bunu hizmet (GATT Service) tanımlayarak yapar. Her bir hizmetin tabiki bir adı yoktur. Hizmetlerin birer numarası vardır. Bu numara UUID olarak bilinir. Bluetooth SIG bir dizi standart hizmet (GATT Service) tanımlamıştır. Bu hizmetlerin birer standart numarası bulunur. 

Her bir hizmetin (GATT Service) içinde hizmetin detaylarının tanımlandığı karakteristik özellikler (GATT Characteristic) bulunur. İletişimin gerçekleştiği noktalar karakteristik özelliklerdir. Bu sebeple kendi özelliklerini tanımlamazlar. Bunun için GATT Server 

### Dikkat Edilmesi Gereken Noktalar

## Kodlama

## Sonuç
