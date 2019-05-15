---
layout: post
title: "Esp32 Geliştirme Kartı Pin Referansı"
date: 2019-05-15
tags: [esp32]
---

## ESP32 Modülleri İçin Hangi Pinler Ne İçin Kullanılır?

Esp32 modülü (ESP32-Wroom-32) 48 adet pine sahiptir. Geliştirme boardlarının çoğunda bu pinlerin hepsine yer verilmez ve bazı pinler modülün 
kendiiçinde kullanılır. Örneğin modüldeki SPI bus esp32 çipinin flash belleğine de bağlıdır.

Aşağıdaki resimde WROOM-32 modülünün pin bağlantıları görülebilir:
![esp-wroom-32 pinleri](/assets/pinout_wroom_pinout.png "ESP32-WROOM32 Pinleri")

## ESP32 Donanımları
  * 18 ADC, analog digital çevirici
  * 3 SPI
  * 3 UART
  * 2 I2C
  * 16 PWM çıkışı
  * 2 DAC, digital analog çevirici
  * 2 I2S
  * 10 kapasitif dokunma girişi
  
Modül üzerinde ADC ve DAC pinleri sabit olarak belirlenmiştir. Yani sadece belirli pinlerde kullanılabilirler. Fakat UART, PWM, SPI, I2C gibi donanımlar, modülün "pin multiplex" özelliğinden faydalanılarak başka uygun olan, istenilen pinlerde kullanılabilirler.

GPIO pinlerinin kullanımları ile ilgili olarak aşağıdaki tabloya bakılabilir. Modül üzerinde bulunan bazı pinler genel olarak kullanılabilirken bazı pinlerin kullanımı esnasında o pinin başka bir amaçla kullanılıyor olmasına dikkat edilmelidir. Yani pin zaten başka bir fonksiyon için kullanılıyor olabilir.

| GPIO | Fiziksel Pin No | Input | Output | Not |
| 0 | 25 | pull-up | dikkat | Varsayılan olarak **boot** esnasında bir PWM sinyali çıkışı verir. Bu sinyal BOOT vektörünün başlangıcını belirler |
| 1 | 35 | uart0_tdx | dikkat | Varsayılan seri port tx pinidir. **boot** esnasında debug amaçlı çıkış üretir.
