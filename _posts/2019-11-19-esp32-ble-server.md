---
layout: post
title: "Esp32 ile Bluetooth LE Server"
date: 2019-11-19
tags: [esp32]
---

# HENÜZ YAZILMAKTADIR. GÜNCELLENECEKTİR!

Esp32 platformu, yapabildiklerine ve fiyatına bakıldığında oldukça avantajlı bir platform. Bu yazımıda fazla detaya girmeden bir çevre birimi (Peripheral) ya da başka bir deyişle bir **BLE SERVER** sistemi yapımını anlatalım:

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

**GATT Server** sunacak olduğu hizmetleri standartta tanımlandığı gibi listelemelidir. Bunu hizmet (**GATT Service**) tanımlayarak yapar. Her bir hizmetin tabiki bir adı yoktur. Hizmetlerin birer *numarası* vardır. Bu numara UUID olarak bilinir. Bluetooth SIG bir dizi standart hizmet (**GATT Service**) tanımlamıştır. Bu hizmetlerin birer standart numarası bulunur. 

Her bir hizmetin (**GATT Service**) içinde hizmetin detaylarının tanımlandığı karakteristik özellikler (**GATT Characteristic**) bulunur. İletişimin gerçekleştiği noktalar karakteristik özelliklerdir. Bu sebeple kendi özelliklerini tanımlamazlar. Bunun için GATT Server her bir karakteristik özellik için bir tanımlayıcı (**GATT Descriptor**) oluşturur. Tamımlayıcılar karakteristik özelliklerin nasıl çalıştıklarını, ne tür bilgi içerdiklerini belirtir. 

Bir karakteristik özellik (**GATT Characteristic**) tanımlayıcılar (**GATT Descriptor**) tarafından tanımlanan aşağıdaki yöntemler ile çalışırlar:
* **WRITE** : Karakteristik özellik **GATT Client** tarafından yazılabilir özelliktedir. Bu sayede **GATT Client** tarafından **GATT Server** tarafına veri gönderilebilir.
* **READ** : Karakteristik özellik **GATT Client** tarafından tek seferliğine okunabilir. Yani her **READ** işlemi bir defa veri okur.
* **NOTIFY** : **GATT Client** bu karakteristik özelliği sürekli olarak dinler. **GATT Server** bu karakteristik özelliği gerektiği her zaman günceller ve karşı tarafı bilgilendirir. **GATT Client** bilgilendiği zaman hiç sesini çıkarmaz. Yani **GATT Server** tarafına mesajın ulaştığı bilgisini vermez. Bu sebeple hızlıdır(!)
* **INFORM** : **GATT Client** bu karakteristik özelliği *NOTIFY* tanımında olduğu gibi dinler fakat bu mesajı aldığında **GATT Server** tarafına mesajı almış olduğu bilgisini verir. Bu sebeple *NOTIFY* tanımından yavaştır(!)

**(!)** Hızlıdır çünkü mesajı alan taraf geriye mesajın ulaştığına bağlı olarak bilgi dönmez.

### Numaralar (Assigned Number, UUID)

BLE sisteminde her yapının bir numarası olduğunu belirtmiştik. Bu numaralar standardın içinde tanımlanan yapılar için haliyle önceden tanımlanmıştır. Tasarımlarda bu önceden tanımlanan numaralar kullanıldığında uygulamalar otomatik olarak bu hizmet ve alt yapılarını görüp işlem yapar. Örneğin pil durumu hizmeti *(Battery Service)* standartta belirtildiği gibi tanımlandığında bağlanılan bütün cihaz, bilgisayar, mobil telefon gibi yapılar pil durumunu ek bir kodlama yapmadan gösterir.

Numaralar UUID denen bir yapı ile ifade edilir. UUID çok büyük bir sayıdır. 16 byte (128 bit) genişliğe sahiptir. Şöyle gösterilir:

```a62206b9-8cd9-4f02-ae53-1755928a54e1```

Bluetooth SIG tarafından yayınlanan önceden atanmış numaraların standart olması dolayısı ile numarayı oluşturan rakamların büyük bir kısmı aynıdır. Bu sebeple standart numaralar kısa formda gösterilir. Örneğin pil seviyesi karakteristik özelliğinin önceden tanımlanmış numarası **0x2A19** olup bu şekilde ifade edilir. Fakat bunu UUID formatında göstermek gerekirse, yani uzun haliyle ifade edilirse: 0000**2A19**-0000-1000-8000-00805F9B34FB olur. Örnekteki numaranın **2A19** olan kısa formu hariç diğer standart numaralar için geri kalan kısmı aynıdır. Yani numarası **0x180F** olan pil durumu hizmetinin UUID gösterimi 0000**180F**-0000-1000-8000-00805F9B34FB olacaktır.

Fakat biz tasarımlarımızda standart olarak tanımlanan numarala dışında numara üretip bunları kullanabilir. Tabi bu hizmetlerin veya karakteristik özelliklerin çalışabilmesi için kullanılacak olduğu platform, bilgisayar, mobil cihazlarda kendimiz kodlama yapmamız gereklidir.

### Bizim Sistemimizin Yapısı

Bizim tasarlayacağımız sistemde aşağıdaki Bluetooth yapıları olacak (standart isimleri ve tanımları ile):

### Hizmetler (GATT Service)

|Hizmet|Uniform Type ID|Numarası|
|----|----|----|
|Battery Service|org.bluetooth.service.battery_service|0x180F|
|Sensor Service|Standart değil! Kendimiz tanımladık|a62206b9-8cd9-4f02-ae53-1755928a54e1|

### Karakteristik Özellikler (GATT Characteristic)

|Hizmet|Karakteristik Özellik|Numarası|Çalışma Yöntemi|
|----|----|----|----|
|Battery Service|Battery Level|0x2A19|READ|
|Sensor Service|Sensor State|a62206b9-8cd9-4f02-ae53-1755928a54e2|READ,NOTIFY|

### Tanımlayıcılar (GATT Descriptor)

|Karakteristik Özellik|Adı|Numarası|
|----|----|----|
|Battery Level|Characteristic User Description|0x2901|

**NOT:** Bazı tanımlayıcılar esp32 platformu tarafından otomatik olarak oluşturulur!

### Dikkat Edilmesi Gereken Noktalar

BLE sistemi sürekli bağlantı yapısında tasarlanmamıştır. Örneğin *Client* cihazlar belirli aralıklar ile *Server* cihazlardan veri aktarırlar. Bu sayede enerji efektif olarak çalışır. Fakat bu durum düşük bant genişliği gibi bazı dezavantajları ortaya çıkarır. Bazı gözlemleri listelemek gerekirse:

* Örneğin aksi belirtilmedikçe windows işletim sistemleri için veri transfer aralığı 60ms civarındadır. Bazı Android sistemler 40 ~ 80 ms aralıklarında veri transfer ederler.
* Bluetooth 4.1 için bir veri paketi yükü maksimum 23 byte'tır. 4.2 için 25 byte civarındadır. Maksimum paket yükünü aşan transferler için paket bölünmesi gözlenir. Bu da transfer zamanını arttırır.
* Bu durumda 1 sn içinde maksimum (1000 / 60ms) x 23 =~383 byte veri aktarılabilir. Bu kabaca 3 KBit transfer hızı demektir ki bu çok düşüktür. Fakat gerçek zamanlı olmayacak sistemler için bu hız yeterlidir.
* Bluetoot LE gerçek zamanlı sistemler ile kullanılması önerilmeyen bir sistemdir!
* Fakat Bluetooth LE sistemi HID *(Human Interface Device)* olarak tasarlanmış cihazlara destek verir. Bu cihazlar gerçek zamanlı olarak düşünülebilir. Örneğin bir Bluetooth Mouse böyle bir cihazdır. Bu durumda bağlanan *Client* sisteme yüksek hız ihtiyacı bildirilebilir.
* Windows işletim sistemleri bir *Server* cihazına bağlantı kurduğunda şunlar gerçekleşir:
  * Eğer cihaz için tanımlanan bir sürücü var ise bu sürücü bağlantı gereksinimlerine göre hızı ve gerekli diğer parametreleri ayarlar. 
  * Eğer cihaz *Gatt Peripheral Preferred Connection Parameter characteristic* özelliğini tanımlıyorsa bu tanıma bir sonraki bağlantıda uyulur. Fakat hemen uyulması isteniyorsa *Server* tarafından *L2Cap Connection Parameter Update Request* gönderilir. 
    * esp32 platformunda bu işlemin yapılabilmesi için [bu fonksiyon](https://docs.espressif.com/projects/esp-idf/en/latest/api-reference/bluetooth/esp_gap_ble.html#_CPPv434esp_ble_gap_set_prefer_conn_params13esp_bd_addr_t8uint16_t8uint16_t8uint16_t8uint16_t "esp-idf api") kullanılır. Fakat biz bir üst kütüphane kullanacağız ve bu kütüphane bu işlemi arka planda gerçekleştirecek.
* Bağlantı kalitesini etkileyen parametreler şunlardır:
  * **minimum preferred connection interval** : Tercih edilen en kısa bağlantı aralığı süresi. 1,25ms 'lik adımlar ile hesaplanır. Yani değer 10 ise süre **10 x 1,25ms = 20ms** olur.
  * **maximum preferred connection interval** : Tercih edilen en uzun bağlantı aralığı süresi.  1,25ms 'lik adımlar ile hesaplanır. Yani değer 20 ise süre **20 x 1,25ms = 40ms** olur.
  * **preferred slave latency** : Tercih edilen gecikme, yani kaybolduğunda tolere edilebilecek paket sayısı.
  * **preferred supervision timeout** : Tercih edilen zaman aşımı süresi. Bu süre dolduğunda cihaz bağlantısı kesilebilir. 6,25ms 'lik adımlar ile hesaplanır. Yani değer 100 ise süre **100 x 6,25ms = 625ms** olur.



## Kodlama

## Sonuç
