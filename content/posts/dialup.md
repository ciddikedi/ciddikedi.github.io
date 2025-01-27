---
title: 'Dialing... 2025 yılına Dial-up ile bağlanıyoruz!'
date: 2025-01-26T10:38:29+03:00
draft: false
cover:
  image: "posts/dialup/images/cover.jpg"
  alt: "Dial-UP+"
  caption: ""
  relative: false
---

## Giriş

Koku alma duyusu ile hafıza arasında güçlü bir ilişki vardır. Geçmişte zihnimize kazınan kokulara yıllar sonra tekrar maruz kaldığımızda o koku ile bağlantılı duygular, hisler ve ortamlar tetiklenir. Buna Proust etkisi denir. İlkel vücudumuzun benzer mekanizması işitme duyusu için de geçerlidir. Beni ise geçmişe götüren şeylerden birisi kuşkusuz artık tarihin tozlu sayfalarında yer alan çevirmeli bağlantı sesi oldu. Bu konuda yalnız olduğumu düşünmüyorum. İnternet ortamında bir kaç tık uzağımızda olan çevirmeli bağlantı sesini daha yakından yeniden deneyimlemek adına gigabit hızların havada uçuştuğu günümüz teknolojisini kenara bırakarak gerçek bir Dial-up bağlantı kurma işine soyundum.

{{< figure align=center src="images/modem_ata.jpg" caption="U.S. Robotics 56K Modem ve Linksys PAP2T." >}}

## Gereksinimler

Gereksinim listesi çok kalabalık olmasa da ilgili donanımları günümüzde bulmak veya daha doğrusu sağlamını bulmak oldukça güç. Dikkat edilmesi gereken nokta, örneğin en gerekli donanım olan Dial-up modemin en yenisinin milenyumdan önce üretildiğini unutmamak gerekir. Dahası, genellikle plastik kasadan üretilmiş bu modemlerin yaşına istinaden cips gibi çıtır çıtır kırıldığını çok kötü şekilde deneyimledim. Sağlamını bulduğunuz modemin çalışma ihtimali ise bir hayli düşük. Ben ancak bu iş için ikinci el sayfalarını didik didik ederek aldığım dördüncü modemimi sağ salim kullanabildim. İhtiyacımız olan liste ise aşağıdaki şekilde;

- Client bilgisayar - Thinkpad T42
- 2 adet Dial-up modem - Aztech UM9800 ve Thinkpad Integrated Modem
- Analog telefon adaptörü - Linksys PAP2T
- Telefon santral sunucusu - Asterisk ve tercihen Ubuntu
- Dial-in sununcusu - Windows XP
- Analog tuşlu telefon
- 2 adet RJ11 kablo

## Hazırlık

Yapı iki taraftan oluşuyor. Birincisi istek yapacağımız client tarafı, diğeri ise bizim yaptığımız istekleri karşılayan ISP tarafı. Tarafları birleştirme işlemini ise analog telefon adaptörü aracılığıyla santral sunucusu yapıyor. Client tarafından yapılan arama işlemi Asterisk tarafından cevaplanarak sinyal doğrudan ISP modeme yönlendiriliyor. Dial-in server gelen bağlantı isteğini doğruladıktan sonra client tarafına bir IP adresi atayarak kendi üzerinden internete çıkmasına olanak sağlıyor. Bu noktada ihtiyacımız olan Linux ve Windows sunucuları ise sanal kurmayı tercih ettim. Sanallaştırma için tercih ettiğim VMware Workstation ürünü host üzerindeki donanımların doğrudan sanal makineye passthrough edilmesine olanak sağlıyor.

{{< figure align=center src="images/topoloji.png" caption="Yapının şeması." >}}

### Asterisk

Asterisk açık kaynaklı bir santral yazılımı olup, sesli iletişim sistemleri oluşturmak ve yönetmek için kullanılan bir platformdur. Bizim projemiz de ağırlıklı olarak sesli iletişim üzerinden çalışacağı için bu yazılıma ihtiyaç duyuyoruz. Öncelikli olarak Asterisk çalıştırmak için Linux tabanlı bir işletim sistemine ihtiyacımız var. Ben tercihimi Ubunutu'dan yana yaptım. Asterisk kurulumu ve yapılandırmasını tamamlamak için aşağıdaki şekilde ilerleyebiliriz.

Tek komut ile kurulum işlemini kolaylıkla başlatıyoruz.
```
apt install asterisk
```
Kurulum tamamlandıktan sonra iki tane SIP client oluşturmak için `/etc/asterisk/sip.conf` dosyasının en alt satırına aşağıdaki yapılandırmayı ekleyebiliriz.
```
[pap2t-ispmodem]
context=default
type=friend
secret=password
qualify=200                     
host=dynamic                    
directmedia=yes        
regexten=881
nat=no

[pap2t-client]
context=default
type=friend
secret=password
qualify=200                 
host=dynamic                  
directmedia=yes  
regexten=882
nat=no
```
Bu yapılandırmayı daha sonrasında ATA cihazımıza gireceğiz. Devamında ise `/etc/asterisk/extensions.conf` dosyası içerisinde `[default]` başlığını buluyoruz ve `include => demo` satırına yorum atıyoruz. Sonrasında aynı başlığın altına `exten => _X!,1,Dial(SIP/pap2t-ispmodem, 20)` satırını ekliyoruz. Bunun anlamı; herhangi numaradan gelen aramayı **pap2t-ispmodem** client'a yönlendir. 

Asterisk servisinin sistem yeniden başlatıldığında açılması ve yapılan değişikliklerin geçerli olması için aşağıdaki komutları çalıştırmalıyız.
```
systemctl enable asterisk
systemctl restart asterisk
```
Tebrikler! Asterisk kurulum ve yapılandırması çok kolay oldu. Asterisk yapılandırması için [linkteki](https://dogemicrosystems.ca/wiki/Dial_up_server) içerikten faydalandım.

### ATA Kurulumu

ATA cihazında tercihimi yaygın olarak kullanılan Linksys PAP2T yönünde yaptım. Bu cihazı bulabilecek kadar şanslı iseniz öncelikli olarak ikinci el olduğu için sıfırlama işleminin yapılması gerekiyor. Sıfırlama işlemi için cihaz üzerinde bir reset switch bulunmuyor. Bu noktada bir analog tuşlu telefona ihtiyaç duyuyoruz. Bu cihazın birçok yapılandırması analog telefon aracılığıyla yapılıyor. Analog telefonu cihaza bağladıktan sonra aşağıdaki numaraları çevirerek sıfırlama işlemini başlatabiliriz.

```
****737738# ve onaylamak için 1
```
Cihaz yeniden başladığında DHCP üzerinden IP alacaktır. Adresi öğrenmek için aşağıdaki numaraları tuşlamalıyız.

```
****110#
```
Tuşlama tamamlandıktan sonra telefon ahizesinden cihazın aldığı IP adresi sesli olarak okunacaktır. Artık IP adresini öğrendiğimiz cihazın Web arayüzüne girebiliriz.

{{< figure align=center src="images/pap2t_web.png" caption="PAP2T Web arayüzü." >}}

Cihazın Web arayüzüne girdiğimizde hızlıca aşağıdaki işlemleri yaparak başlayabiliriz. Değişiklik için herhangi bir kullanıcı veya parola zorunluluğu bulunmuyor.
1. Admin Login, Switch to advanced view
2. System -> DHCP: no
3. Static IP, Gateway, Netmask, Hostname, Primary DNS değerlerini kendinize göre ayarlayabilirsiniz.
4. Line 1 -> SIP Port: 5038 Port değerini Asterisk üzerinden kontrol ederek doldurabilirsiniz.
5. Proxy: Asterisk sunucunun IP adresi.
6. Display Name: pap2t-client, User ID: pap2t-client, Password: password
7. Echo Canc Enable, Echo Canc Adapt, Echo Supp Enable: No
8. Preffered Codec: G771u
9. Network Jitter Level: low
10. 4-9 arası tüm adımlar Line 2 için pap2t-client alanını pap2t-ispmodem olarak değiştirerek yapılmalı.

Tüm aşamalar eksiksiz tamamlandığında PAP2T kendini Asterisk üzerinde register edecektir. Cihazın üzerindeki tüm ışıkların yandığı kontrol ederek çalıştığı teyitlenebilir. Bu yapılandırmaya göre RJ11 kablo kullanarak Line 1 portu internete çıkarmak istediğimiz client bilgisayara, Line 2 portu ise Dial-in server amacıyla kullanılacak sistemin modemine bağlanmalıdır.

### Dial-in Server

Dial-in sunucuları kullanıcıların telefon hattı üzerinden modeme bağlanarak bir ağa veya internete erişmesini sağlayan eski ama önemli bir teknolojiydi. Kimlik doğrulama, IP adres ataması ve internete erişim gibi görevleri üstlenen Dial-in sunucusu için birçok alternatif olmasına rağmen bu sefer biraz kolaya kaçarak Windows XP işletim sistemini tercih ettim. Windows XP üzerinde Dial isteklerini karşılamak Asterisk kurulumundan dahi basit. İlk olarak Dial-up modemi işletim sistmeine tanıtmak gerekiyor. Modemin hard, soft veya hangi bağlantı arayüzü ile bağlandığının önemi yok. Aygıt yöneticisi altında doğru çalışan bir modem olarak tanınması yeterli. Ben işletim sistemini ingilizce kullandığım için adımları da ingilizce yazacağım.
1. Control Panel -> Network Connections
2. Sol taraftan Create a new connection
3. En alttaki Set up an advanced connection
4. Accept incoming connections
5. Modem cihazımızı seçiyoruz.
6. Do not allow virtual private connections
7. Bu adımda karşımıza kullanıcı listesi gelecek. Client bilgisayarımızdan bağlanırken kullanacağımız kullanıcıyı bu adımda ekleyerek devam edebiliriz.
8. Internet Protocol alanını düzenleyerek client bilgisayara verilecek IP aralığını belirliyoruz. Buradaki IP adresi bizim subnet ağımız içerisinde fakat DHCP aralığının dışında olmalıdır. Aynı pencere içerisinde Allow callers to access my local area network seçeneği de seçilmesi gerekiyor.

Dial-in sunucumuz hazır ve istekleri karşılamayı bekliyor. Tahmin ettiğimizden çok daha kolay oldu. Dial-in yapılandırması için [linkteki](https://www.reddit.com/r/vintagecomputing/comments/uca4n4/creating_your_own_internal_dialup_network_to/) içerikten faydalandım.

## Operasyon

Her şey hazır ve tüm bağlantılar tamam ise sırada sadece keyfini çıkarmak kalıyor. Client bilgisayar olarak Türkiye sınırları içerisindeki en temiz, en donanımlı ve dünyanın en iyi işletim sistemi Windows 2000 kurulu son gerçek IBM Thinkpad modellerinden olan T42 yardımcı olacak. Daha fazla sabrımız kalmadığı için hızlıca arama yapmaya başlayalım.

{{< figure align=center src="images/t42.jpg" caption="Bize eşlik edecek yol arkadaşımız IBM T42." >}}

### Dialing...

Onlarca başarısız denemeden sonra rüyamda dahi telefon çevirme sesleri duymama rağmen hevesimi kaybetmediysem eğer bu işi sonuçlandırmayı hakettiğimi biliyorum. Haksız da çıkmadım. 

{{< notice warning >}}
Zaman portalı açılıyor! Bu bağlantı sesi sizi yalnızca internete bağlamakla kalmaz, hatıralarınızın derinliklerine de yol açabilir. Eski odanızın kokusu veya gecenin sessizliğinde yankılanan modem melodisi vurucu şekilde zihninizde canlanabilir.
{{< /notice >}}

Arama yaparken kullandığımız numaranın bir önemi yok zira Asterisk üzerine gelen tüm arama isteklerini kendi oluşturduğumuz ISP tarafına gönderecek şekilde yapılandırdık. Fakat kullanıcı adı ve parola doğrulaması için Dial-in sunucu kurulumunda oluşturulan hesabı kullanmak zorundayız. İşlemlerin sıralaması; Client bilgisayar üzerindeki modem tarafından bir telefon araması başlatılacak. Arama Asterisk tarafından cevaplanarak ISP modem cihazına yönlendirilecek. Modem, analog ses sinyalini sayısal veriye dönüştürerek Dial-in sunucuya iletecek ve burada kullanıcı doğrulaması yapılarak IP ataması gerçekleştirilecek. Kulağa hoş geliyor.

{{< figure align=center src="images/connection.jpg" caption="Başarılı bir bağlantının adımları." >}}

Sizi kendi kaydettiğim tamamen organik bağlantı sesiyle baş başa bırakıyorum. Bu ses Dial-up bağlantısının olmazsa olmazlarından biridir. Telefon hattı üzerinden veri iletişimini başlatan tınılar bağlantının kurulum aşamasındaki sinyalleşme protokollerinin bir parçasıdır. Her aşama modemlerin birbiriyle iletişim kurduğu bir adıma işaret eder.

{{<audio src="posts/dialup/sound/connection_sound.mp3" >}}

### Deneyim

Yapabilecekleriniz sizin hayal gücünüze kalmış. Deneyim olarak güncel Web içeriği için bir şey konuşmak zor. Zira en basit sayfaların açılması dahi dakikaları bulabiliyor. Fakat retro deneyimi biraz daha gerçekçi hale getirmek isterseniz Escargot gibi custom servisleri kullanarak MSN Messenger hasretini giderebilir veya açık kaynak AIM, ICQ gibi sunucuları inceleyebilirsiniz. Sayısı çok az da olsa text base sitelerde gezinebilirsiniz. Merakımı gidermek ve sistemin verimini gözlemlemek için iperf3 testleri yaptım. Sonuçlar günümüzle kıyaslandığında teknolojinin geldiği noktayı apaçık ortaya koyuyor.

```
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
Accepted connection from 192.168.1.xxx, port 1707
[  5] local 192.168.1.xxx port 5201 connected to 192.168.1.xxx port 1708
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec  0.00 Bytes  0.00 bits/sec
[  5]   1.00-2.00   sec  1.43 KBytes  11.7 Kbits/sec
[  5]   2.00-3.00   sec  4.28 KBytes  35.0 Kbits/sec
[  5]   3.00-4.00   sec  2.85 KBytes  23.4 Kbits/sec
[  5]   4.00-5.00   sec  4.28 KBytes  35.0 Kbits/sec
[  5]   5.00-6.00   sec  2.85 KBytes  23.4 Kbits/sec
[  5]   6.00-7.00   sec  4.28 KBytes  35.0 Kbits/sec
[  5]   7.00-8.00   sec  2.85 KBytes  23.4 Kbits/sec
[  5]   8.00-9.00   sec  4.28 KBytes  35.0 Kbits/sec
[  5]   9.00-10.00  sec  2.85 KBytes  23.4 Kbits/sec
[  5]  10.00-11.00  sec  4.28 KBytes  35.0 Kbits/sec
[  5]  11.00-12.00  sec  2.85 KBytes  23.4 Kbits/sec
[  5]  12.00-13.00  sec  4.28 KBytes  35.0 Kbits/sec
[  5]  13.00-14.00  sec  4.28 KBytes  35.0 Kbits/sec
[  5]  14.00-15.00  sec  2.85 KBytes  23.4 Kbits/sec
[  5]  14.00-15.00  sec  2.85 KBytes  23.4 Kbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-15.00  sec  49.9 KBytes  27.3 Kbits/sec
```
Biraz inada bindirerek defalarca sayfayı yenilemenin ardından **Fast.com** sitesini açmayı başardım. Tabii ki sonuçlar çok farklı çıkmıyor. Aksini de beklemiyorduk.

{{< figure align=center src="images/speed.jpg" caption="Fast.com hız testi sonucu." >}}

## Sonuç

Bu projeyle nostaljik bir teknoloji olan Dial-up bağlantısının büyüsünü yeniden deneyimleme fırsatı buldum. Kendi Dial-up sunucumu kurmak geçmişin teknolojilerini keşfetmenin ne kadar keyifli olabileceğini yeniden hatırlattı. Telefon hattının tanıdık tınısı ve bağlantının gerçekleşme anındaki heyecan bu deneyimi daha da anlamlı kıldı.

Dial-up gibi eski teknolojiler sadece bir nostalji unsuru değil aynı zamanda modern teknolojilerin gelişim sürecine ışık tutan harika araçlar. Eğer siz de böyle bir projeyi denemek istiyorsanız bu süreçte karşılaşacağınız zorluklar kadar elde edeceğiniz tatmin de bir o kadar büyük olacaktır.

{{< figure align=center src="images/desk.jpg" caption="Denemeler yaparken masamız biraz dağıldı." >}}