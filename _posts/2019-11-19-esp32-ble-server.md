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
* **GAP** (Generic Access Profile) : Cihazların birbirlerini bulması için bu yapı kullanılır.
* **GATT** (Generic ATTribute) : Cihazların birbirlerine bağlanması için bu yapı kullanılır.

**BLE standardı temel olarak şu rolleri tanımlar:**
* **GATT Client**: Genellikle *MASTER* veya *CENTRAL* olarak da bilinir. Bütün işi idare eden taraftır. Mesela bir Bluetooth kalp sensörüne bağlanan cep telefonu bu roldedir.
* **GATT Server**: Genellikle *SLAVE* veya *PERIPHERAL* olarak da bilinir. İdare edilen taraftır. Genelde pasiftir. Mesela bir bluetooth kalp sensörü bu roldedir.

**GATT Server** sunacak olduğu hizmetleri standartta tanımlandığı gibi listelemelidir. Bunu hizmet (**GATT Service**) tanımlayarak yapar. Her bir hizmetin tabiki bir adı yoktur. Hizmetlerin birer numarası vardır. Bu numara UUID olarak bilinir. Bluetooth SIG bir dizi standart hizmet (**GATT Service**) tanımlamıştır. Bu hizmetlerin birer standart numarası bulunur. 

Her bir hizmetin (**GATT Service**) içinde hizmetin detaylarının tanımlandığı karakteristik özellikler (**GATT Characteristic**) bulunur. İletişimin gerçekleştiği noktalar karakteristik özelliklerdir. Bu sebeple kendi özelliklerini tanımlamazlar. Bunun için GATT Server her bir karakteristik özellik için bir tanımlayıcı (**GATT Descriptor**) oluşturur. Tamımlayıcılar karakteristik özelliklerin nasıl çalıştıklarını, ne tür bilgi içerdiklerini belirtir. 

Bir karakteristik özellik (**GATT Characteristic**) tanımlayıcılar (**GATT Descriptor**) tarafından tanımlanan aşağıdaki yöntemler ile çalışırlar:
* **WRITE** : Karakteristik özellik **GATT Client** tarafından yazılabilir özelliktedir. Bu sayede **GATT Client** tarafından **GATT Server** tarafına veri gönderilebilir.
* **READ** : Karakteristik özellik **GATT Client** tarafından tek seferliğine okunabilir. Yani her **READ** işlemi bir defa veri okur.
* **NOTIFY** : **GATT Client** bu karakteristik özelliği sürekli olarak dinler. **GATT Server** bu karakteristik özelliği gerektiği her zaman günceller ve karşı tarafı bilgilendirir. **GATT Client** bilgilendiği zaman hiç sesini çıkarmaz. Yani **GATT Server** tarafına mesajın ulaştığı bilgisini vermez. Bu sebeple hızlıdır(!)
* **INFORM** : **GATT Client** bu karakteristik özelliği *NOTIFY* tanımında olduğu gibi dinler fakat bu mesajı aldığında **GATT Server** tarafına mesajı almış olduğu bilgisini verir. Bu sebeple *NOTIFY* tanımından yavaştır(!)

### Bizim Sistemimizin Yapısı

### Dikkat Edilmesi Gereken Noktalar

## Kodlama

## Sonuç
