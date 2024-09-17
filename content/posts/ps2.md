---
title: 'PlayStation 2 üzerinde Linux çalıştırmak?'
date: 2024-09-16T20:25:29+03:00
draft: false
cover:
  image: "posts/ps2/images/ps2_cover.png"
  alt: "Playstation 2 Linux"
  caption: ""
  relative: false
---

## Giriş

Herkesin hayatında bir noktada mutlaka PlayStation konsolu yer almıştır. Zamanının çok ötesinde olan bu sihirli kutu benim de çocukluk hayallerimi süslüyordu. İçerik ve oyunlara erişimin şimdiye nazaran zor olduğu bu dönemler elimizdeki ile maksimum yetinmeye çalışarak azami keyif alıyorduk. Her oyunumu tüm detaylarıyla inceleyerek defalarca bitirmeme sebep olan da buydu. En çok oynadığım PlayStation 2 oyunları ise sırasıyla; Ratchet and Clank, Need for Speed Underground 2 ve Winning Eleven 8 oldu. Şimdi ise bu konsolu bir meydan okumanın sonucu aylarca araştırma ve onlarca deneme yanılma yaparak Linux işletim sistemli makineye dönüştürdüm.

## PlayStation 2

Rekorların sahibi, dünyanın en iyi oyun konsolu. 2000 yılında piyasaya sürülen PlayStation 2, en çok satan oyun konsolu olmasının yanında sayısız oyun kütüphanesine sahipti. Mutlak başarısının altında yatan en temel neden şüphesiz hedeflenen kitlenin öncelikli olarak zihnine ve kalbine odaklanılmış olmasıydı. Dahası çıktığı dönemde sunduklarıyla bir çağı kapatıp yenisini açan bir cihazdı. Günümüzde ise şahsi kanaatim değişen tek şey rakamlardan ibaret.

### Teknik özellikler

Detaylar teknik özelliklere indikçe daha da ilginçleşiyor. Kağıt üzerinde son derece kısıtlı bir donanıma sahip bu konsol, aynı oyunları zamanının GHz rekorları kıran Pentium işlemcili bilgisayarlarından daha iyi şekilde oynatması bu kara kutunun nasıl mühendislik harikası bir cihaz olduğunu gözler önüne seriyor. Konsola ait teknik özellikler şu şekilde;

- CPU: 299 MHz MIPS mimari
- Memory: 32 MB RDRAM
- GPU: 147 MHz, 4 MB VRAM
- Depolama: 8 MB Memory Card
- Medya: DVD-ROM

### CPU mimarisi

Toshiba ile ortak geliştirilen işlemci, MIPS Little Endian mimarisini barındırıyor ve 180 nanometre üretim teknolojisine sahip. 13,5 milyon transistöre sahip bu yonga, 28 milyar transistöre sahip 3 nanometre üretim süreci ile üretilen Apple M4 ile kıyaslandığında geldiğimiz noktayı büyüleyici şekilde gözler önüne sunuyor. Sony oyuncuların bilinçaltına oynayarak bu işlemcinin adını **Emotion Engine** koymuş. Sonraki Playstation nesillerinde hem Ar-Ge masraflarından kaçınmak hem de oyunların portlanmasını kolaylaştırmak amacıyla Sony artık konsollarında x86 mimariye geçiş yaptı. Dolayısıyla konsollar bilgisayarların gölgesinde kaldı. Dahası, oyunlar sadece grafiğe odaklandığı için kalite olarak düşmeye başladı.

{{< figure align=center src="images/ee.jpg" caption="PlayStation 2'nin kalbi. (Alıntı)" >}}

## Gereksinimler

Bu işi yapmayı kafaya koyduysanız liste epey kabarık. Maalesef bir çoğunu bulmak oldukça zor. Bulunan parçaların çalışma olasılığı da pek düşük. İlk olarak PlayStation 2 edinmek gerekli. Çipli olması zorunlu değil. Zira çeşitli exploitler yardımıyla cihaza mod yapılmadan da engeller aşılabiliyor. Diğer tüm ekipmanlar için ise ikinci el sayfalarının derinliklerine inmek şart. Tam liste aşağıdaki gibi;

- PlayStation 2 Fat kasa
- PS2 Linux kit
    - PS2 Linux kurulum diskleri
    - PS2 Network adaptörü
    - 8 MB Memory Card
    - VGA dönüştürücü kablo
    - USB klavye ve fare
- 3.5" IDE disk
- Komponent kablo
- FMCB yüklü ikinci Memory Card
- Slide tool
- Orijinal herhangi bir oyun
- DVD yazıcı ve çok sayıda boş DVD

### PlayStation 2 Linux Kit

Kullanıcıların PlayStation 2 konsoluna Linux tabanlı işletim sistemi kurmasına olanak sağlayan bu kit, özellikle Japonya'da popüler olan Red Hat tabanlı Kondara Linux dağıtımını kullanıyor. Dolayısıyla bu işe girişirken tekerleği yeniden keşfetmiyoruz. Sony, konsolun potansiyelini ortaya çıkarmak için böyle bir kit geliştirirken aynı zamanda bir hobi geliştirme ortamı oluşturmak amaçlanmış. Kitin içerisine kurulum disklerinin yanında yukarda belirttiğimiz ihtiyacımız olacak her şey mevcut. Bu kiti günümüzde bulmanın imkansıza yakın olması sebebiyle kendi olanaklarımla bulabildiğim parçaları buldum, bulamadıklarım için ise DIY çözümler üretmeye çalıştım.

{{< figure align=center src="images/kit.jpg" caption="PlayStation 2 Linux kiti. (Alıntı)" >}}

### Free Memory Card Boot

PlayStation 2 için engellerin kaldırıldığı alternatif bir boot yazılımı olan FMCB sayesinde homebrew ELF uygulamaları çalıştırabiliyoruz. Memory karta yüklenen yazılım açılışta otomatik tespit edilerek başlatılıyor. FMCB sayesinde çeşitli homebrew uygulamaları çalıştırılabilir, kopya koruması atlanabilir veya harici diskler üzerinden oyunlar yürütülebilir. Bizim senaryomuzda Swap Magic uygulamasını çalıştırmak için kullanacağız. FMCB çalıştırmak için yine FMCB yüklü başka bir karttan dosyaların kopyalanmasını sağlayabilirsiniz. İkinci bir yol olarak benim de yaptığım gibi FMCB yüklü memory kartı uzakdoğudan sipariş verebilirsiniz. Geri kalan tek işlem kartı PlayStation'a takıp açma tuşuna basmak.

{{< figure align=center src="images/fmcb.jpg" caption="Free MCBoot menüsü." >}}

### Swap Magic

Kopya korumasını aşmak için genellikle mod chip uygulamaları yapılsa da, sistemin açıklarından faydalanarak herhangi bir müdahaleye gerek kalmadan gerçekleştirmek de mümkün. Swap Magic yöntemi bunlardan birisi. PlayStation, disk takıldığı anda ilk olarak orijinalliğini ve bölge kilidini kontrol eder. Eğer eject tuşuna basılırsa doğrulama sıfırlanır. Swap Magic tam burada araya giriyor. Orijinallik doğrulaması yapılan disk, eject tuşuna basmadan değiştiriliyor ve yerine kopyalanmış oyun yerleştiriliyor. Daha sonrasında okuma işlemi devam ettirilerek kopya oyun açılmış oluyor. Disk tepsisini eject tuşuna basmadan manuel olarak açmak için öncelikle disk okuyucu kapağının çıkarılıp Slide Tool tarzı aletler ile müdahale edilmesi gerekiyor. Ben Slide Tool'u ülkemiz sınırları içerisinde bulamadığım için 3D modelini edinerek bu işi yapan birine bastırdım.

{{< figure align=center src="images/slide_tool.jpg" caption="3D basılmış Slide Tool." >}}

Swap Magic kullanarak kopya oyun çalıştırmanın aşamaları aşağıdaki şekildedir;

1. FMCB kullanılarak sistem başlatılır.
2. Orijinal oyun disk tepsisine yerleştirilir.
3. Swap Magic uygulaması başlatılır.
4. Slide tool yardımıyla mekanizma soldan sağa hareket ettirilir.
5. Mekanizma elle çekilerek disk değiştirilir ve geri itilir.
6. Slide tool yardımıyla mekanizma sağdan sola hareket ettirilir.
7. Swap Magic uygulamasından devam ettirilerek oyunun açılması sağlanır.

{{< notice warning >}}
Disk tepsisine ait kapağın çıkarılması sırasında kapak klipslerinin kırılma riski bulunmaktadır. Kapak dikkatlice, acele edilmeden ve minimum kuvvet uygulayarak çıkarılmalıdır.
{{< /notice >}}

### Network adaptörü

Mevcut yapılandırmada PlayStation 2 üzerinde 4 MB BIOS ve oyunların ilerlemelerini kaydetmek amacıyla 8 MB Memory Card opsiyon olarak bulunuyor. Tabii ki bu alan herhangi bir işletim sistemi kurabilmek için yeterli değil. Problemin üstesinden gelmek için PlayStation 2 üzerinde bulunan Expansion Bay aracılığıyla takılan network adaptörü hem internet erişimi için gerekli desteği sağlarken hem de disk takılmasına olanak sağlıyor. Adaptörün piyasada muadilleri bulunsa da orijinal adaptör dışındaki hiçbir örneğinde network desteği bulunmuyor. Linux kit ile birlikte yer alan bu adaptörü ikinci el sayfalarını tarayarak çalışan bir örneğini temin ettim.

{{< figure align=center src="images/network_adapter.jpeg" caption="PlayStation 2 Network adaptörü." >}}

### VGA dönüştürücü

Beni en çok oyalayan ve araştırmaya iten kısımlardan birisi PlayStation 2 üzerinden VGA sinyali almak oldu. Orijinal dönüştürücü asla hiçbir yerde bulunmuyor ve muadil ürün de mevcut değil. İncelediğim onlarca şemadan sonra aslında çok da kompleks olmadığına karar verdim ve kendim de yapabileceğime inanarak kolları sıvadım. Eğer monitörünüz Sync-On-Green destekliyorsa çok çaba sarf etmeden 6 tane pin ile bu iş çözülebiliyor. Orijinal dönüştürücü de bu şekilde çalışıyor. Görüntü komponent kablodan dönüştürülerek yapılıyor. Sync-On-Green ise komponent kablonun Red-Green-Blue hatlarında Clock verisini Green üzerinden aktarmasıyla çalışıyor. Böylelikle 1280x1024'e kadar çözünürlüğe sorunsuz olarak ulaşılabiliyor. Dönüştürücüyü hazırlamak için üç adet dişi RCA jack ve bir adet de üzerinde numaraların yazdığı VGA konnektör kullandım.

{{< figure align=center src="images/ps2_vga.jpg" caption="Ev yapımı PlayStation 2 VGA dönüştürücü." >}}

```
  ___5_______1____ 
  \  o o o o o   / 
10 \  o o o o o / 6
    \o o o o o /
     ----------
    15       11

KIRMIZI konnektörün sinyal bacağı -> VGA Pin 1
YEŞİL konnektörün sinyal bacağı -> VGA Pin 2
MAVİ konnektörün sinyal bacağı -> VGA Pin 3
KIRMIZI konnektörün toprak bacağı -> VGA Pin 6
YEŞİL konnektörün toprak bacağı -> VGA Pin 7
MAVİ konnektörün toprak bacağı -> VGA Pin 8
```

## Operasyon

Artık sahne bizim! Edison misali onlarca belki de yüzlerce denemenin ardından başarıya ulaşmanın verdiği keyif tartışılmaz. Hatta bu süreçte dayanamayan bir tane PlayStation 2 eğitim zayiatı oldu. Belki de inada bindirdiğim bu projeye başlamak yerine harcadığım meblağ ile kendime güzel bir PlayStation 4 alabilirdim. Şimdi son aşama olarak hızlıca disklerimizi oluşturup kuruluma başlayabiliriz.

{{< notice info >}}
PlayStation 2 üzerine Linux işletim sistemi kurmak konsolun orijinalliğini kesinlikle bozmamaktadır. Yapılan tüm eklentiler çıkarılarak kalınan yerden devam edilebilir.
{{< /notice >}}

### Hazırlık

PlayStation 2 Linux kurulum imajları zor da olsa internet ortamında bulunabiliyor. İki adet diskten oluşan kit, kurulum dosyalarını ikinci diskte barındırıyor. Kurulum işlemi sürekli olarak diskleri değiştirmemizi istediği için Linux kernel dosyasını birinci diske taşıyacağız. Ne kadar disk değiştirme işlemi, o kadar kopya koruması kontrolü anlamına geliyor. 
\
\
Birinci disk imajında değişiklik yaparken ikincisine dokunmayacağız. Fakat kopyalayacağımız dosyalar ikinci diskte bulunduğu için her iki disk imajını da bir yere çıkartmamız gerekiyor. İkinci disk imajı içerisinde bulunan BOOT klasörünü ve P2L_0100.02 dosyasını birinci disk imajını açtığımız klasöre kopyalıyoruz. Böylelikle kurulum, ilk aşamadaki disk değiştirme işlemini atlayacaktır. Güncel durumda birinci diskin içeriği aşağıdaki gibi olmalıdır;

```
BOOT*
MODULES*
SM_PDF*
VCL*
DUMMY.DAT
P2L_0100.01
P2L_0100.02
P2LBOOT.CNF
PBPX_955.09
SYSTEM.CNF
* Klasör olduğunu ifade etmektedir.
```

ImgBurn tarzı uygulamalarla imaj yeniden oluşturulduktan sonra her iki disk de yazılabilir. Tabii ki artık yaygın olmasa da bu iş için DVD yazıcıya ihtiyaç var. Bende olmadığı için Verbatim marka bir DVD yazıcı aldım. 

### Kurulum

Tüm eksikler ve hazırlıklar tamamlandıktan sonra kurulum aşaması gerçekten en kolay kısım. Kurulum, birinci diskten Linux çekirdeğini başlattıktan sonra çeşitli sorular sorarak kurulum işlemini tamamlıyor. Burada dikkat edilmesi gereken nokta kurulum başlangıcında ikinci diskin takılması talep ediliyor. Yapılması gereken işlem, önce orijinal bir oyun takmak ve hata almak. Sonrasında ise back ile geri geldikten sonra Swap Magic işlemi yaparak ikinci diski diski takmak. İşlem başarıyla gerçekleştikten sonra Welcome mesajı bizi karşılayacaktır. 

{{< figure align=center src="images/kurulum.jpg" caption="Kurulum aşamaları." >}}

Kurulum çeşitli sorular sorarak ilerleyecektir. Partition oluşturma aşamasında eğer fdisk tecrübeniz yok ise Disk Druid kullanabilirsiniz. Belleğimiz kısıtlı olduğu için Swap oluşturmayı unutmamalısınız. Tercihler yapıldıktan sonra başlayan kopyalama işlemi yaklaşık 45dk ile 1 saat arasında sürmekte. Kurulum işlemi tamamlandıktan sonra birinci slotta yer alan Memory karta Linux çekirdeği kopyalanacaktır. Maalesef kurulum yapıldıktan sonra dahi sistemi başlatmak için birinci diske ihtiyaç duyacağız. 
\
\
Tüm işlemler bittikten sonra heyecanlı bekleyiş başlıyor ve komut satırını gördükten sonra yüzde tebessümler oluşuyor. Tüm çabalara rağmen sonuca ulaşmak inanılmaz mutlu hissettiriyor. Son derece eski 2.2.1 çekirdeğini kullanan dağıtım çok stabil ve şaşırtıcı derecede hızlı çalışıyor. İhtiyaç duyulabilecek neredeyse tüm paketler mevcut ve eğer tabii ki bulabilirseniz MIPS için derlenmiş RPM paketleri de kurulabiliyor. 

{{< figure align=center src="images/console.jpg" caption="Konsol ekranı." >}}

## Sonuç

PlayStation 2 üzerinde Linux işletim sistemi kurulumunu tamamlamak hem teknik bir meydan okuma hem de nostalji dolu bir yolculuk oldu. Yılların oyun konsolunu yeniden canlandırarak ona farklı bir işlev kazandırmak son derece yaratıcı ve tatmin edici bir tecrübeydi. Gerek FMCB kullanarak açılış sürecini yanıltmak gerek ise Swap Magic ile kopya korumasını atlamak gibi adımlar ile konsolun açıklarını keşfederken aynı zamanda teknik bilgi birikimimizin gelişmesine de yardım etti. PlayStation 2'ye Linux işletim sistemi kurmak, geçmişin teknoloji harikalarına bugünün perspektifiyle bakmamızı sağlayan eşsiz bir deneyim oldu.

{{< figure align=center src="images/result.jpg" caption="Kondara Linux masaüstü ortamı." >}}