---
layout: post
title: "Vue.js 3 İle Komponent Tasarımı"
date: 2021-01-21
tags: [vuejs]
---

Vue.js, tasarım işini kolaylaştıran bir framework. Özellikle *model binding* yapıyor olması kodlama yükünü oldukça azaltıyor. Bu framework'ün en iyi taraflarından biri de tekrar kullanılabilir komponentler yapıp kullanmaya izin vermesi. Bu sayede büyük proje parçalarını, yeniden kullanılabilir, daha küçük hazır kod parçaları olarak paketleyip tekrar tekrar kullanabiliyoruz.
Bu yazımda bir komponent tasarlayacağız ve bu komponenti browser ile kullanılabilecek şekilde paketleyeceğiz. Bunun için [Vue.Cli](https://github.com/vuejs/vue-cli/) kullanacağız.
Hadi başlayalım.

## Hazırlık

*Bu yazıyı okumak için basit seviyede HTML, CSS ve javascript bilmek biraz da vue.js hakkında bilgi sahibi olmak tavsiye olunur!*

Bu işi yapabilmek için ben aşağıda belirttiğim araçları kullandım. Önce ilgili olan araçların kurulumunu anlatacağım:

## Araçlar
* **Visual Studio Code**
  * Visual Studio Code uygulamasını bir editör olarak kullanacağız. Arzu ederseniz **Sublime Text**, **Atom** gibi diğer uygulamaları da kullanabilirsiniz. Ya da hangi editörü tercih ederseniz. [Visual Studio Code](https://code.visualstudio.com/) sitesinden edinebilirsiniz.
* **Node.js**
  * Node.js işimizin çekirdeğini oluşturan araçtır. [Node.js](https://nodejs.org/tr/) sitesinden edinebilirsiniz.
* **Vue.Cli**
  * Bu paket ile vue.js projeleri tasarlayıp kodlayabilirsiniz. Vue.js kodlama işini oldukça kolaylaştıran bir araç. Vue.js 3 versiyonunu kullanacağız. Arzu edenler [Vue.js 3](https://v3.vuejs.org/) sitesine göz atabilirler. Şimdi bu bileşeni nasıl kurarız, ona bakalım.

## Kurulum

Node.js uygulamasını Windows 10 bilgisayarımıza kurduğumuzda **Node.js Command Prompt** kısayolunu da yükler. Bu kısayol, Node.js ortam değişkenleri hazır olarak tanımlanmış bir kabuk (Shell) sunar. Kısayolu çalıştırarak **npm** paket yöneticisini kullanabiliriz. Paket yöneticisi ile **Vue.Cli** paketini sisteme yükleyeceğiz. Bunun için aşağıdaki komutu yazarız:

``npm install -g @vue/cli``

Bu komutu çalıştırdığımızda sistemimize **Vue.Cli** paketi yüklenecektir. Bu işlem internet bağlantısı hızına göre biraz zaman alabilir.

![İşlem tamamlandı](/assets/vuejs-ile-komponent-tasarimi/vuecli_install_finish.jpg "İşlem tamamlandı")

Bu resimdeki gibi bir sonuç gördüğünüzde **Vue.Cli** yüklenmiş demektir.

Şimdi **Vue.Cli** kullanarak yeni bir proje oluşturacağız. Yeni oluşturacağımız projede örnek bir vue app ve bir komponent yer alacak. Bunu bir *hello world* kodu gibi düşünebiliriz. Şu komutu veririz:

``vue create yeni-komponent``

Komutu verdikten sonra araç bizden hangi vue versiyonunu kullanacağımızı soracak. Burada vue 3 seçiyoruz.

![Vue version seçimi](/assets/vuejs-ile-komponent-tasarimi/select_vue_version.jpg "Vue version seçimi")

Version seçimi yaptıktan sonra araç bağımlılıkları ve diğer paketler **Vue.Cli** tarafından yüklenir ve örnek kod olutşturulur. Burada işlem bitene kadar beklemek gerekli.

![Yükleme tamamlandı](/assets/vuejs-ile-komponent-tasarimi/cli_init_complete.jpg "Yükleme tamamlandı")

Yükleme tamamlandıktan sonra yukarıdaki resimdeki gibi bir çıktı göreceğiz. Bu aşamada artık kodların bulunduğu dizine gidip projeyi çalıştırmamız yeterli olacak. Şu komutları veririz:

``cd yeni-komponent``

Projeyi bu haliyle çalıştırabiliriz. Çalıştırmak için:

``npm run serve``

Komutun çıktısı şu şekilde olacaktır:

![Serve komutu çıktısı](/assets/vuejs-ile-komponent-tasarimi/serve-vue-cli.jpg "Serve komutu çıktısı")

**serve** komutu projemizi yerel bir sunucuda sunmaya başlar fakat browser açarak sayfayı bize göstermez. Bunun için çıktı ekranındaki adreslerden birini kopyalayıp browser penceresine yapıştırarak sonucu görürüz.

![Yeni komponent](/assets/vuejs-ile-komponent-tasarimi/yeni-komponent_hello.png "Yeni komponent projesi ilk görünümü.")

**serve** komutu aktifken, yani sunucu çalışıyorken dosyalar üzerinde değişiklik yapabilir, sonucunu da direkt olarak browser penceresinde görebiliriz. Projemiz üzerinde çalışmak için **Visual Studio Code** uygulamasını kullanabiliriz. **Visual Studio Code** uygulamasını açtıktan sonra **Open Folder** *(Klasör Aç)* seçeneğini seçip projemizin içinde olduğu klasörü açarız. Soldaki dosya menüsünü genişlettiğimizde şöyle bir görüntü görürüz:

![VSCode ilk ekran](/assets/vuejs-ile-komponent-tasarimi/vscode_ilk_ekran.jpg "VSCode İlk Ekran")

Şimdiye kadar yaptıklarımız ile bir vue uygulaması oluşturduk ve bunu yayınladık. Şimdi komponentimizi tasarlayalım. Dosya ağacına baktığımızda `/src/components` klasörünü görürüz. Bu klasör içinde tahmin edeceğiniz gibi uygulamada kullanılacak olan komponentler bulunur. **Vue.js** komponentleri tasarımı uygulama *(app)* tasarımı ile aynı yolu izler. Yani aynı şekilde yapılır. Bizim örnek olarak tasarlayacağımız komponent bir buton içerecek ve bu butona tıklandığında bir sayacı arttıracak. Değerini de ekranda gösterecek. Bu örneği [Vue.js](https://v3.vuejs.org/guide/component-basics.html#base-example) sitesinden de görebilirsiniz. Biz bu örneği kullandığımız araçlara göre yazacağız. 

## Tasarım ve Kodlama

Önce yeni komponentimizi projemize ekleriz. Bunun için **Visual Studio Code** penceresinin solunda bulunan **Explorer** sekmesinde `/src/components` klasörüne sağ tıklarız ve **New File** komutu ile yeni bir dosya oluştururuz. Dosyanın adını **YeniKomponent.vue** olarak veririz.

![Yeni komponent oluştur](/assets/vuejs-ile-komponent-tasarimi/yeni-komponent-olustur.jpg "Yeni komponent oluştur.")

Oluşturduğumuz dosyanın içini dolduralım. Bir **Vue.js** komponenti 3 ana kısımdan oluşur:

1. Template --> `<template>`
*Html kullanarak oluşturduğumuz kod şablonu*
2. Script --> `<script>`
*Uygulama mantığını içeren javascript kodu*
3. Style --> `<style>`
*Html için kullanılacak olan stiller*

Kodumuzu bu yapıya göre oluştururuz:

```vue 
  <template>
    <div><button v-on:click="tiklamaSayisi++">Tıkla</button><span class="etiket">Tıklama sayısı: {{tiklamaSayisi}}</span></div>
  </template>

  <script>
    export default {
      name: 'YeniKomponent',
      data() {
        return { tiklamaSayisi: 0 };
      }
    }
  </script>

  <style>
    span.etiket {
      margin-left: 3px;
      margin-right: 3px;
    }
  </style>
```

Basitçe açıklamak gerekirse komponentimizin `tiklanmaSayisi` adlı bir veri girdisi bulunur. Butona eklediğimiz `v-on:click="tiklamaSayisi++"` kodu ile vue çekirdeğine ilgili değişkenin sayısını 1 arttır deriz. `<span class="etiket">Tıklama sayısı: {{tiklamaSayisi}}</span>` ile de değişkenin değerini gösteririz.

Dosyaya komponent kodunu ekledikten sonra kaydediyoruz. Şimdi oluşturduğumuz komponenti browser üzerinde çalıştıracağız. Bunu yapmak için `/src/App.vue` dosyasını açarız. Dosya içinde bulunan **HelloWorld** referanslarını kaldırırız. Çünkü o komponenti değil kendi komponentimizi kullanacağız. Kaldırma işlemini yaptıktan sonra **App.vue** dosyası şöyle görünecek:

```vue
<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <p>Yeni Komponenti aşağıda göreceğiz:</p>
  <yeni-komponent></yeni-komponent>
</template>

<script>
import YeniKomponent from './components/YeniKomponent.vue'

export default {
  name: 'App',
  components: {
    YeniKomponent
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

Bu aşamada bütün dosyaları kaydedip browser penceremizi yenilediğimizde komponentimizi çalışır halde görebileceğiz.

![Çalışan komponent](/assets/vuejs-ile-komponent-tasarimi/run-komponent.gif "Çalışan komponent")

## Paketleme

Şimdiye kadar yaptıklarımız ile komponentimizi oluşturduk ve çalıştırdık. Çalıştığını gözlemledik. Şimdi ise komponentimizi paketleyip browser ile kullanılabilir hale getireceğiz. Bunun için projemize bir javascript dosyası ekleyeceğiz. Bu dosyanın görevi paketleme olacak ve browser'a yüklenen kodu `window` global nesnesine bağlayacak. Bu sayede browser üzerinde kullanabileceğiz.

### Paketleyici Dosyası

Önce `/src` klasörüne sağ tıklayarak `paketleyici.js` adlı bir dosya ekleyin. Sonra dosyanın içine şu kodları ekleyin:

```javascript
import YeniKomponent from './components/YeniKomponent.vue';

export default YeniKomponent
    if (typeof window !== 'undefined' && window.Vue) {
        window.YeniKomponent = YeniKomponent;
    } else {
        console.warn('Yeni komponent yüklenemedi. Vue veya window yok!');
    }
```

Dosyayı kaydetmeyi unutmayın. Yaptığımız işlem **.vue** uzantılı dosyadan komponetimizi yüklemek ve eğer bir browser penceresinde çalışıyorsa ve sistemde **Vue.js** varsa `window` nesnesine komponentimizi bağlarız. Paketleyici bu kodu paketin içine ekler. Browser üzerinde çalıştırdığımızda ise bu kod komponentimizi kullanabilmemizi sağlar.

### Paketleme Komutu

Şimdi paketleme için gerekli komutu `package.json` dosyasına ekleyeceğiz. **Explorer** sekmesinde dosyayı bulduğumuzda çift tıklayıp düzenlemek için açarız. Paketleme komutunu:

```"paketle": "vue-cli-service build --target lib --name YeniKomponent ./src/paketleyici.js",```

Dosyanın içine ekleriz. Ekledikten sonra `package.json` dosyasının ilgili kısmı şöyle görülmelidir:

![Paketleme komutu](/assets/vuejs-ile-komponent-tasarimi/vue-paket.jpg "Paketleme Komutu")


### Paketleme

Basitçe paketleme komutunu çalıştırırız: `npm run paketle`

![Paketleme Komutu Çıktısı](/assets/vuejs-ile-komponent-tasarimi/paketleme-komutu.jpg "Paketleme Komutu Çıktısı")

Artık elimizde paketlenmiş halde komponentimiz bulunmakta. Çıktıda dikkat edeceğiniz gibi projemizin ana klasöründe `dist` adlı bir klasör oluşmuş ve paketlenmiş olan komponentimizin dosyaları bunun içine eklenmiştir.

Komponentimizi browser ile kullanabilemiz için şunlara dikkat etmemiz gerekli:

* Sayfanın başında komponentimizin stillerini yükleriz: `<link rel="stylesheet" href="./YeniKomponent.css">`
* Önce **Vue.js** kütüphanesini ekleriz.
* Sonrasında komponentimizin paketini ekleriz:  `<script src="./YeniKomponent.umd.min.js"></script>`
* Oluşturduğumuz **Vue.js** App yapısına komponentimizi bağlarız:

```javascript
var app = Vue.createApp( { /* ... vue uygulaması kodu ... */ } );
app.component('yeni-komponent', window.YeniKomponent);
//......
```

* Uygulamamız içinde artık `<yeni-komponent></yeni-komponent>` etiketi ile kullanabiliriz.







