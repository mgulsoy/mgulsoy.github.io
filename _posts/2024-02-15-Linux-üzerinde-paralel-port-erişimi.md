---
layout: post
title: "Linux Üzerinde Paralel Port Erişimi"
date: 2024-02-15
tags: [linux]
---

Lisedeyken bilgisayar kullanmaya başladığımda en çok etkilendiğim kısım paralel port olmuştu. Buraya başka bir şeyler bağlayıp programlama yaparak bir otomasyon sistemi yapmayı hayal ederdim. Şimdi basit bir şekilde Ubuntu 
işletim sistemi kullanarak paralel port pinlerini kontrol etmeye çalışalım.

Paralel port aslında eski bir teknoloji. Güncel sistemlerin pek çoğunda aslında bulunmuyor bile. Tarihi eser sayılabilecek yazıcıları kontrol etmek için kullanılıyor. Fakat bazı uygulamalarda gerçekten işe yarıyor.

<div align="center">
<img src="/assets/paralel-port-kontrolü/1200px-Parallel_computer_printer_port.jpg" width="350" >
<div>Paralel port (Kaynak: wikipedia)</div>
</div>

## Pinler

Port pin tanımları ve fonksiyonları şöyledir:

| Pin | Sinyal | Yön |
| --- | --- | --- |
| 1 | Strobe (Active Low) | I/O |
| 2 | DATA 0              | SPP(O) , ECP(I) |
| 3 | DATA 1              | SPP(O) , ECP(I) |
| 4 | DATA 2              | SPP(O) , ECP(I) |
| 5 | DATA 3              | SPP(O) , ECP(I) |
| 6 | DATA 4              | SPP(O) , ECP(I) |
| 7 | DATA 5              | SPP(O) , ECP(I) |
| 8 | DATA 6              | SPP(O) , ECP(I) |
| 9 | DATA 7              | SPP(O) , ECP(I) |
| 10 | ACK               | I |
| 11 | BUSY              | I |
| 12 | PAPER OUT         | I |
| 13 | SELECT            | I |
| 14 | Line Feed (Active Low) | I/O |
| 15 | ERROR             | I |
| 16 | RESET             | I/O |
| 17 | SELECT (Active Low) | I/O |
| 18-25 | GROUND         | REF |

Paralel port çalışma yöntemleri profil olarak tanımlanır. Bunları şöyle açıklayabiliriz:
* SPP: Standart Parallel Port
* EPP: Enhanced Parallel Port (Geliştirilmiş paralel port)
* ECP: Extended Capabilities Port (Yetenekleri arttırılmış port)

## Hazırlık

Portu kontrol etmek için basit bir C uygulaması yazacağız. Fakat bunu yapabilmemiz için önce sistemimizde bir C derleyicisine ihtiyacımız olacak. Bunun için GCC yükleyeceğiz.

```sudo apt -y install gcc ```

Komutunu kullanarak gcc yükledikten sonra kodlamaya başlayabiliriz.

## Örnek Kod

Linux sistemlerinde donanımlar `/dev` klasörü altında birer dosya olarak nitelenir. Bu dosyaları kullanarak donanımlara veya sürücülerine erişip kontrol etmek mümkündür. Biz de öyle yapacağız. Önce sistemdeki birinci paralel port aygıtı olan `/dev/parport0` 'ı açmaya çalışacağız. Sonra bu aygıtı başkası kullanmasın diye onu tekelimize alacağız.

```C
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <sys/ioctl.h>
#include <linux/parport.h>
#include <linux/ppdev.h>

#define DEVICE "/dev/parport0"

int main(int argc, char **argv) {

  // önce dosyayı açarız. 
  int fd = open(DEVICE, O_RDRW);
  if(fd < 0) {
    // Eğer dosya açma işlemi 0'da küçük bir sonuç dönerse işlem hatalı gerçekleşmiş demektir.
    fprintf(stderr, "Dosya acilamiyor: %s\n", DEVICE);
    perror("OPEN DEVICE"); // Hata mesajını göster.
    return 5; // Uygulamadan çık
  }

  
}
``` 



