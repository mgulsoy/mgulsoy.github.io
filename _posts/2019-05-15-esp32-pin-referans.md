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

| GPIO | Input Kullanımı | Output Kullanımı | Not |
|--------|--------|--------|--------|--------|
| 0 | pull-up | <span style="color:yellow">dikkat</span> | Varsayılan olarak **boot** esnasında bir PWM sinyali çıkışı verir. Bu sinyal BOOT vektörünün başlangıcını belirler |
| 1 | uart0_txd | <span style="color:yellow">dikkat</span> | Varsayılan seri port tx pinidir. **boot** esnasında debug amaçlı çıkış üretir.|
| 2 | pull-down | <span style="color:lime">OK</span> | Bazı boardlarda bu pine mavi bir led bağlıdır. |
| 3 | <span style="color:yellow">dikkat</span> | uart0_rxd | Varsayılan seri port rx pinidir. **boot** esnasında _HIGH_ konumdadır. |
| 4 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> | Çok amaçla kullanılabilir. |
| 5 | pull-up | <span style="color:yellow">dikkat</span> | Strapping pinidir. **boot** esnasında  PWM sinyali olabilir. |
| 6 | <span style="color:red">HAYIR</span> | <span style="color:red">HAYIR</span> | Esp32 çipinin kullandığı SPI flash'a bağlıdır. Kullanılmaz. (SPI/CLK) |
| 7 | <span style="color:red">HAYIR</span> | <span style="color:red">HAYIR</span> | Esp32 çipinin kullandığı SPI flash'a bağlıdır. Kullanılmaz. (SPI/SDO) |
| 8 | <span style="color:red">HAYIR</span> | <span style="color:red">HAYIR</span> | Esp32 çipinin kullandığı SPI flash'a bağlıdır. Kullanılmaz. (SPI/SDI) |
| 9 | <span style="color:red">HAYIR</span> | <span style="color:red">HAYIR</span> | Esp32 çipinin kullandığı SPI flash'a bağlıdır. Kullanılmaz. (SPI/SDI) |
| 10 | <span style="color:red">HAYIR</span> | <span style="color:red">HAYIR</span> | Esp32 çipinin kullandığı SPI flash'a bağlıdır. Kullanılmaz. (SPI/SDI) |
| 11 | <span style="color:red">HAYIR</span> | <span style="color:red">HAYIR</span> | Esp32 çipinin kullandığı SPI flash'a bağlıdır. Kullanılmaz. (SPI/SDI) |
| 12 | pull-down | <span style="color:yellow">dikkat</span> | MTDI strapping pinidir. Kullanılmaması önerilir. İç LDO voltaj seviyesini ayarlar. |
| 13 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> |  |
| 14 | <span style="color:yellow">dikkat</span> | <span style="color:yellow">dikkat</span> | **boot** esnasında  PWM sinyali olabilir. |
| 15 | <span style="color:yellow">dikkat</span> | <span style="color:yellow">dikkat</span> | MTDO strapping pinidir. **boot** esnasında  PWM sinyali olabilir. |
| 16 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> | U2_RXD |
| 17 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> | U2_TXD |
| 18 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> |  |
| 19 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> | U0_CTS |
| 21 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> | I2C_SDA |
| 22 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> | I2C_SCL |
| 23 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> |  |
| 25 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> | DAC_1, ADC |
| 26 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> | DAC_2, ADC |
| 27 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> | ADC |
| 32 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> | ADC |
| 33 | <span style="color:lime">OK</span> | <span style="color:lime">OK</span> | ADC |
| 34 | <span style="color:lime">OK</span> | <span style="color:red">HAYIR</span> | ADC, Sadece Inout (Giriş) |
| 35 | <span style="color:lime">OK</span> | <span style="color:red">HAYIR</span> | ADC Sadece Inout (Giriş) |
| 36 | <span style="color:lime">OK</span> | <span style="color:red">HAYIR</span> | ADC Sadece Inout (Giriş) |
| 39 | <span style="color:lime">OK</span> | <span style="color:red">HAYIR</span> | ADC Sadece Inout (Giriş) |

