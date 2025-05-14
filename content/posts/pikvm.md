---
title: 'PiKVM ihtiyaç mı?'
date: 2024-05-29T15:38:29+03:00
draft: false
cover:
  image: "posts/pikvm/images/pikvm_cover.jpg"
  alt: "Geekworm KVM-A3 Kit"
  caption: ""
  relative: false
---

## Giriş

Sistem yöneticileri her ne kadar veri merkezlerinden uzaklaşmış olsa da sanal olarak yapılamayan işler için yerinde müdahale etmeye maalesef mecburlar. Bu zorunluluklardan birisi de maalesef cihazların konsol erişimi. Özellikle HP ILO gibi çözümler çözümler uzun süredir sektörde yer alsa da aynı ortamı evinde oluşturmak isteyen biz hobiciler için seçenek çok çok az. Elbette KVM over IP cihazları piyasada bulunuyor ancak fiyatlardan ötürü yanına yaklaşılmıyor. Evimde çeşitli amaçlarla topladığım sunucu olarak çalışan bilgisayar(lar)ı her problem yaşadığımda veya kurulum yapmam gerektiğinde söküp masama getirmek zahmetli olmaya başlamasıyla birlikte çeşitli arayışlarda bulundum. 

## PiKVM Projesi

Neredeyse her aptal problemin Raspberry ile çözülmesi sinir bozucu gelmeye başlamış durumda. Hatta çoğu zaman inanılmaz derecede pazarlanan bu geliştirme kartı gerçekten o kadar da kullanışlı olmayabiliyor. Ancak bu sefer durum çok farklı. Doğru kit ile birleştirildiğinde PiKVM, out of the box bir çözüm haline geliyor. Tek yapılması gereken Lego gibi parçaları birleştirdikten sonra keyfini sürmek. 
\
\
PiKVM projesi, Arch Linux baz alınarak oluşturulmuş açık kaynak kodlu bir Raspberry imajı. Doğrudan hedef gözetilerek tasarlandığı için KVM dışında farklı bir amaçla kullanılamıyor. Temelinde HDMI capture ile görüntüyü alıp OTG ile bilgisayara komutlar iletmek olan bu proje, üzerinde iyi düşünülmüş farklı yetenekleri de bünyesinde barındırıyor. Kickstarter projesi olarak kendilerine ait kitleri de bulunan PiKVM, DIY olarak da oluşturulabiliyor. Ben ise Amazon gönderdiği için Raspberry Pi 4 ile uyumlu Geekworm KVM-A3 kitini tercih ettim. Tabii ki kiti almak tek başına yeterli değil. Uyumlu güç kaynağı, SD kart, CR1220 RTC pili ve Pi'nin kendisine de ayrıca ihtiyaç var.

### Teknik özellikler

PiKVM her şeyden önce bir Raspberry geliştirme kartına ihtiyaç duysa da kendi üzerinde çok sayıda komponent mevcut. Kutu içeriğinde kasa, üzerinde bir takım giriş çıkış portlarının bulunduğu extension şapkası, HDMI capture kartı, OLED ekran ve ATX kitini barındırıyor. Bunun dışında civatalar ve ihtiyaç duyulan kablolar da kutu içeriğine dahil edilmiş. Raspberry olarak benim kullandığım kit için Pi 4 Model B olmasının dışında bir şart bulunmuyor. Dağıtım; 1, 2, 4 ve 8 tüm memory varyasyonların hepsi ile uyumlu. En az 16 GB Micro SD kartın zorunlu tutulduğu sistemde benim tercihim **Sandisk High Endurance** oldu. Her ne kadar PiKVM read only file system olarak çalışsa da işi garantiye almak için yazma ömrü yüksek bir kart seçtim. Kısaca teknik detaylar ise şu şekilde;

- Raspberry Pi 4 ve PiKVM desteği
- OS bağımsız tüm sistemleri web tarayıcısından kontrol imkanı
- HDMI üzerinden Full HD görüntü ve ses capture
- OTG aracılığıyla klavye, mouse ve sanal disk emülasyonu
- RTC saati
- ATX kontrolü
- OLED ekran

{{< figure align=center src="images/pikvm_inside.jpg" caption="Kasanın açık hali." >}}

## Detaylar

Temelde yürütülen fikir bir KVM kontrolü olsa da farklı birkaç zekice özellik eklenerek hem cihazın potansiyelinden faydalanılmış hem de cihaz daha kullanışlı bir çözüm haline gelmiş. Bu başlık altında, PiKVM'nin sunduğu özelliklerin yanı sıra, bu özelliklerin nasıl çalıştığını ve kullanıcılara sağladığı avantajları detaylı olarak ele alacağım.

### Web arayüzü ve deneyim

Cihazın IP adresini tarayıcıya yazarak eriştikten sonra açılan klasik login ekranının ardından gelen sayfa oldukça sade ve olması gerektiği kadar buton ve pencere bizi karşılıyor. Kullanıma hazır olarak bekleyen capture alanı herhangi ek bir ayara ihtiyaç duymadan hızlıca kullanılabiliyor. Hem Windows hem de Linux çalışan bilgisayarlar kullanarak yaptığım denemelerde gözlemim gönül rahatlığıyla söyleyebilirim ki İ-NA-NIL-MAZ. Beklentimin çok üzerinde. Gecikme yok denecek kadar az ve fare, klavye problemleri kesinlikle bulunmuyor. HP ILO veya VMware konsolları ile uzaktan yakından ilgisi yok. Deneyim remote desktop kullanımına çok yakın. Bunun dışında arayüzden capture ile ilgili ince ayarlar yapılabildiği gibi PiKVM'nin terminalini de kullanabiliyorsunuz. Her şey saniyeler içerisinde çok stabil gerçekleşiyor.

{{< figure align=center src="images/web_interface.png" caption="Web arayüzü." >}}

### Sanal disk

Sunuculara imajların uzaktan bağlanılması faydalı bir özellik. PiKVM çözümü de buna tek bir farkla destek veriyor. Alışılmışın dışında imajlar stream edilemiyor. Öncelikle karşı tarafa disk mount edebilmek için imajın cihaza yüklenmesi gerekiyor. Daha sonrasında oluşan katalog içerisinden istenilen imaj seçilebiliyor. Büyük imajlar göz önünde bulundurulduğunda bunların cihaza yüklenmesi çoğu zaman dakikaları bulabiliyor. Başarısız OS kurulumlarında veya çeşitli recovery işlemlerinde farklı imajların hızlıca değiştirilmesine ihtiyaç duyulurken maalesef bu yöntem çok kullanışsız kalıyor.

{{< figure align=center src="images/image_mount.png" caption="Sanal disk menüsü." >}}

### ATX kontrolü

KVM yani klavye, video ve mouse kontrolü tanım için biraz mütevazi kalabilir zira bu cihazın yetenekleri kullanıcı ihtiyaçları ve emsal ürünler göz önünde bulundurularak bir tık öteye taşınmış. Bunlardan birisi de ATX kontrolü. Aslına bakıldığında basit bir GPIO işlemi ile yürütülen bu işlev çoğu zaman hayat kurtarıcı oluyor. Yönetilmek istenen bilgisayar kasasının boş slotuna takılan extension kartı arkasında bir RJ45 portu bulunduruyor. Tahmin edilenin aksine Pi ile kompleks bir haberleşme yerine sadece anakart üzerindeki pinleri kısa devre ettirmek amacıyla kullanılan bu devre kasa üzerindeki kontrolleri gerçekleştirmek için bir zorunluluk.

{{< figure align=center src="images/atx_panel.png" caption="ATX kontrol menüsü." >}}

Temel işlevler; yani kısa power, uzun power ve reset butonlarını üzerinde bulunduran bu menüden ayrıca power ve disk aktivite ışıkları gerçek zamanlı olarak gözlemlenebiliyor. Aynı zamanda kasada bulunan fiziksel butonlar da işlevini kaybetmiyor. Kullandığım kasa üzerinde boş slot olmaması sebebiyle kendi yorumumu katarak bu extension kartın montajını yaptım.

{{< figure align=center src="images/atx_board.jpg" caption="ATX extension kartın kasa üzerindeki konumu." >}}

### OLED ekran

Normal şartlar altında PiKVM, IP adresini DHCP üzerinden talep ediyor. Çeşitli yöntemlerle IP adresi tespit edilebilse de bunun bir ekran üzerinden görüntülenmesi işleri oldukça kolaylaştırıyor. Eğer işler yolunda gitmez ve cihaza erişilmezse yine bu OLED ekran üzerinden kontrolleri yapmak oldukça kullanışlı oluyor. IP adresinin yanında temel birçok değer hakkında bilgilendirme yapan ekran, cihaz üzerinde incelenmesi gereken ilk nokta haline geliyor.

{{< figure align=center src="images/oled_ekran.jpg" caption="OLED ekran." >}}

### OCR desteği

Görünce ben de ilk başta anlam veremedim. IBAN'ı ekran görüntüsü atan arkadaşlar bu özelliğe çok şaşırmasalar da kopyala yapıştır yapamadığımız ekrandan verileri almak için OCR kullanmak kimin aklına geldiyse tebrik etmek lazım. Kullanım ise oldukça pratik. Butona bas, alanı seç ve sonucu panoya yapıştır. Örnek OCR çıktısı aşağıdaki gibi;
```
Welcome to the Proxmox Virtual Environment. Please use your web brouser to
configure this server - conmect to:

https://192.168.1.9:8006/
pue-node login:
```
Fontlara bağlı olarak ufak tefek hataları olsa da genel başarım oldukça iyi. Özellikle hata ayıklama sırasında log incelerken işimize yarayacak bir özellik.

### KVM Switch kontrolü

Birden fazla sistemi tek cihaz üzerinden kontrol etme ihtiyacı duyulduğunda ne yapılabilir? Bu problemin üstesinden gelmek için de PiKVM, ezCoo markasının görece akıllı KVM switch'lerini seri arabirim sayesinde kontrol edebiliyor. Fakat unutmamak gerekir, bu yöntem yalnızca KVM switch cihazına müdahale ediyor. ATX kontrolü yalnızca tek cihaza uygulanabilir.

## Sonuç

PiKVM kiti, uzaktaki bilgisayarları yönetme ve kontrol etme sürecini önemli ölçüde kolaylaştıran bir çözüm sunuyor. Hem yüksek çözünürlükte görüntü capture yeteneği, hem tam KVM desteği, hem de kullanıcı dostu web arayüzü ile bu cihaz, sistem yöneticileri ve teknoloji meraklıları için ideal bir araç haline gelmiş durumda. PiKVM'in sunduğu esneklik onu farklı işletim sistemleri ve kullanım senaryolarında kullanışlı bir çözüm haline getiriyor.
\
\
Kurulum süreci ve özellikleri incelendiğinde, PiKVM'in sağladığı performans ve işlevselliğin beklentileri nasıl karşıladığını net bir şekilde görülebiliyor. Bu cihaz, uzaktan erişim ve yönetim ihtiyaçlarını karşılamak için etkili ve güvenilir bir seçenek sunuyor. Eğer uzaktaki bilgisayarlarınıza kolayca erişim ve tam kontrol sağlamak istiyorsanız, PiKVM kiti kesinlikle değerlendirmeniz gereken bir çözüm. Şimdi sorumu tekrar yineliyorum. PiKVM lüks değil gerçekten bir ihtiyaç.

{{< figure align=center src="images/pikvm_result.jpg" caption="PiKVM iş başında." >}}
