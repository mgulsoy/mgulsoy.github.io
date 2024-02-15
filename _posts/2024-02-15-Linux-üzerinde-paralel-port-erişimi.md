---
layout: post
title: "Linux Üzerinde Paralel Port Erişimi"
date: 2024-02-15
tags: [linux]
---

Lisedeyken bilgisayar kullanmaya başladığımda en çok etkilendiğim kısım paralel port olmuştu. Buraya başka bir şeyler bağlayıp programlama yaparak bir otomasyon sistemi yapmayı hayal ederdim. Şimdi basit bir şekilde Ubuntu işletim sistemi kullanarak paralel port pinlerini kontrol etmeye çalışalım.

Paralel port aslında eski bir teknoloji. Güncel sistemlerin pek çoğunda aslında bulunmuyor bile. Tarihi eser sayılabilecek yazıcıları kontrol etmek için kullanılıyor. Fakat bazı uygulamalarda gerçekten işe yarıyor. Bu yazıdaki işlemleri yapabilmek için temek C programlama bilgisine ihtiyaç bulunmaktadır. 

<div align="center">
<img src="/assets/paralel-port-kontrolü/1200px-Parallel_computer_printer_port.jpg" width="350" >
<div>Paralel port (Kaynak: wikipedia)</div>
</div>

## Pinler

Port pin tanımları ve fonksiyonları şöyledir:

| Pin | Sinyal | Yön |
| --- | --- | --- |
| 1 | Strobe (Active Low) | I/O |
| 2 | DATA 0              | SPP(O) , ECP(I/O) |
| 3 | DATA 1              | SPP(O) , ECP(I/O) |
| 4 | DATA 2              | SPP(O) , ECP(I/O) |
| 5 | DATA 3              | SPP(O) , ECP(I/O) |
| 6 | DATA 4              | SPP(O) , ECP(I/O) |
| 7 | DATA 5              | SPP(O) , ECP(I/O) |
| 8 | DATA 6              | SPP(O) , ECP(I/O) |
| 9 | DATA 7              | SPP(O) , ECP(I/O) |
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

Bu bilgiye göre pinlerin yönleri şöyle ifade edilebilir: 
* SPP(O) : Eğer port SPP profiline göre ayarlanmışsa pin sadece çıkış (OUTPUT/source) yönünde çalışır.
* ECP(I/O) : Eğer port ECP profiline göre ayarlanmışsa pin hem çıkış (OUTPUT/source) hem de giriş(INPUT/sink) yönünde çalışabilir.
* I : Pin sadece giriş(INPUT/sink) yönünde çalışabilir.
* I/O : Pin hem çıkış (OUTPUT/source) hem de giriş(INPUT/sink) yönünde çalışabilir.

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

  // Port aygıtına erişim tamam şimdi tekelimize alacağız:
  int res = ioctl(fd,PPCLAIM); // ioctl ile PPCLAIM komutunu çalıştır.
  if(res < 0) {
    // Komut başarısız.
    perror("PPCLAIM");
    close(fd); // dosyayı kapat.
    return 5; // uygulamadan çık.
  }

  // Komut başarılı. Şimdi strobe pininden sinyal verecceğiz:
  strobe_blink(fd);

  // Kontrol işlemlerimiz tamamlandığında portu serbest bırakacağız ve dosyayı kapatacağız.
  ioctl(fd, PPRELEASE); // hata olsa da ilgilenmiyoruz.
  close(fd);
  
  return 0;
}
```

Bu kodu `main.c` adı ile kaydedebiliriz. Temel olarak yaptığımız şey portu açmak, _PPCLAIM_ komutu ile sahipliğini almak, I/O işlemi yapmak. _PPRELEASE_ komutu ile serbest bırakmak ve kapatmak. Şimdi `strobe_blink(fd)` çağrımına bakalım:

```C
int strobe_blink(int fd) {

  struct ppdev_frob_struct frob; // bir frob yapısı tanımla
  frob.mask = PARPORT_CONTROL_STROBE; // Bit maskesi belirle, hangi kontrol pinini kumanda edeceğimizi belirler.
  for(int i = 0; i < 30; i++) {
    // Strobe pinini aktif yap! ( dikkat: pin ters mantık çalışır )
    frob.val = 0xFF;
    ioctl(fd, PPFCONTROL, &frob);
    usleep(500000); // 500ms bekle

    // pasif yap
    frob.val = 0x00;
    ioctl(fd, PPFCONTROL, &frob);
    usleep(500000); // 500ms bekle
  }
}
```

Bu fonksiyon ile strobe (1) pinini yarım saniyelik sinyaller üretmek üzere kullanmış olduk. Programı derlemek için `gcc -o pport main.c` komutunu kullanabiliriz. Bu bize `pport` adlı bir çalıştırılabilir dosya üretecektir. Bu dosyayı `sudo` ile çağrılmalı veya `CAP_SYS_RAWIO` ile yetkilendirilmelidir. Zira direkt olara donanım erişimi söz konusu.

## Diğer Kontroller

Aşağıdaki fonksiyonları da kullarak başka kontrolleri de sağlayabiliriz.

### Data Pinlerine Yazma (Çıkış)

Bu fonksiyon argümanlarından `data` 8 bitlik bir tip olduğundan her bir bit, bir data bus pinine karşılık gelir.

```C
int write_data_pins(int fd, unsigned char data) {
  return (ioctl(fd, PPWDATA, &data));
}
```

### Data Pinlerinden Okuma (Giriş)

```C
int read_data_pins(int fd) {
  unsigned char data;

  int mod = IEEE1284_MODE_ECP;  // port profilini ECP olarak değiştir
  int res = ioctl(fd,PPSETMODE,&mod);

  if(res < 0){
    // Profil belirlenirken hata:
    perror("PPSETMODE");
    return res;
  }

  // Profil belirleme başarılı
  // Port yönü giriş yapılır.
  res = ioctl(fd, PPDATADIR, &mod);
  if(res < 0){
    // Port giriş yapılırken hata:
    perror("PPDATADIR");
    return res;
  }

  // birkaç saniye beklenir, bu sürede elle bir giriş sinyali üretilebilir.
  sleep(10);

  // veri porttan okunur:
  res = ioctl(fd, PPRDATA, &data);
  if(res < 0){
    // Port giriş okunurken hata:
    perror("PPRDATA");
    return res;
  }

  // data değişkeni içinde port data pinleri verisi bulunur.
  return (int)data;
}
```

### Durum Pinlerinden Okuma

```C
int read_status_pins(int fd) {

  int val;
  int res = ioctl(fd, PPRSTATUS, &val);
  if(res < 0){
    // Profil belirlenirken hata:
    perror("PPRSTATUS");
    return res;
  }

  val ^= PARPORT_STATUS_BUSY;   // busy pini değeri ters çevrilmelidir.

  printf("/BUSY  = %s\n",
		((val & PARPORT_STATUS_BUSY)==PARPORT_STATUS_BUSY)?"HI":"LO");
	printf("ERROR  = %s\n",
		((val & PARPORT_STATUS_ERROR)==PARPORT_STATUS_ERROR)?"HI":"LO");
	printf("SELECT = %s\n",
		((val & PARPORT_STATUS_SELECT)==PARPORT_STATUS_SELECT)?"HI":"LO");
	printf("PAPEROUT = %s\n",
		((val & PARPORT_STATUS_PAPEROUT)==PARPORT_STATUS_PAPEROUT)?"HI":"LO");
	printf("ACK = %s\n",
		((val & PARPORT_STATUS_ACK)==PARPORT_STATUS_ACK)?"HI":"LO");
	return 0;
}
```

Pinlerden okuma yazma yapılırken pinlerin akımlarına dikkat edilmelidir. Zira pinler çok yüksek akımları sağlayamayabilir. Ne kadar akım sağlayabileceği ise donanım üreticisi tarafından belirlenir. Pinler giriş konfigürasyonu olarak ayarlandığında tri-state veya HIGH-Z olarak bulunmayıp pull-up olarak da çalışıyor olabilir.

Kaynaklar:
> [OpensourceForU](https://www.opensourceforu.com/2015/06/using-parallel-ports-to-control-devices/)
> 
> [Linux Kernel Docs, Parport](https://www.kernel.org/doc/html/latest/admin-guide/parport.html)
>
> [Linux Kernel Docs, Parport, low level](https://www.kernel.org/doc/html/latest/driver-api/parport-lowlevel.html)
>
> [How to control peripheral ports: Accessing and writing on Parallel Port with C on Linux. Part I](https://www.howtoforge.com/tutorial/accessing-parallel-ports-on-linux)
>
> [Linux Parport programming](https://dominic.familie-radermacher.ch/computer/linux/programming/parport.html)
>
> [The Linux 2.4 Parallel Port Subsystem](http://www.linuxfocus.org/common/src/article205/ppdev.html)
>
> [alibaba LVS Source, github](https://github.com/alibaba/LVS/blob/master/kernel/include/linux/parport.h)
>
> [Linux source, github](https://github.com/torvalds/linux/blob/master/drivers/char/ppdev.c)
>
> [Writing a parport device driver, tldp.org](https://tldp.org/LDP/khg/HyperNews/get/devices/parport.html)
