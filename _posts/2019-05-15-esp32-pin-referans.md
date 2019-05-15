---
layout: post
title: "Esp32 Geliştirme Kartı Pin Referansı"
date: 2019-05-15
tags: [esp32]
---

Esp32 modülü oldukça yetenekli bir modül. Fiyatına ve sunduğu özelliklere bakılırsa pek de rakibi yokmuş gibi. Bu modülün pin özelliklerine bir bakalım...

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

| GPIO | Fiziksel Pin No | Input Kullanımı | Output Kullanımı | Not |
|--------|--------|--------|--------|--------|
| 0 | 25 | pull-up | dikkat | Varsayılan olarak **boot** esnasında bir PWM sinyali çıkışı verir. Bu sinyal BOOT vektörünün başlangıcını belirler |
| 1 | 35 | uart0_txd | dikkat | Varsayılan seri port tx pinidir. **boot** esnasında debug amaçlı çıkış üretir.|
| 2 | 24 | pull-down | OK | Bazı boardlarda bu pine mavi bir led bağlıdır. |
| 3 | 34 | dikkat | uart0_rxd | Varsayılan seri port rx pinidir. **boot** esnasında _HIGH_ konumdadır. |
| 4 | 26 | OK | OK | Çok amaçla kullanılabilir. |
| 5 | 29 | pull-up | dikkat | Strapping pinidir. **boot** esnasında  PWM sinyali olabilir. |
| 6 | 20 | **HAYIR** | **HAYIR** | Esp32 çipinin kullandığı SPI flash'a bağlıdır. Kullanılmaz. (SPI/CLK) |
| 7 | 21 | **HAYIR** | **HAYIR** | Esp32 çipinin kullandığı SPI flash'a bağlıdır. Kullanılmaz. (SPI/SDO) |
| 8 | 22 | <span style="color:red">HAYIR</span> | **HAYIR** | Esp32 çipinin kullandığı SPI flash'a bağlıdır. Kullanılmaz. (SPI/SDI) |
