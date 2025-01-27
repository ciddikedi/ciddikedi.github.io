---
title: 'Libreboot ile özgürlüğün sınırlarını zorlamak'
date: 2024-06-05T10:38:29+03:00
draft: false
cover:
  image: "posts/libreboot/images/libreboot_cover.jpg"
  alt: "X200s Libreboot"
  caption: ""
  relative: false
---

## Giriş

Daha fazla açık kaynak kodlu ve özgür olma yarışının son noktası ne olabilir? Günümüzde bilgisayarlarımızın güvenliği ve özgürlüğü, teknoloji kullanıcıları için her zamankinden daha büyük bir önem taşıyor. Çoğu zaman bilgisayarların BIOS yazılımları kapalı kaynak kodlu ve üreticilerin kontrolü altında oluyor. Bu bağımlılık hem güvenlik açıkları hem de gizlilik endişeleri yaratabiliyor. İşte bu noktada Libreboot devreye giriyor. Libreboot, özgür ve açık kaynaklı bir BIOS yazılımı alternatifidir. Kullanıcılara tam kontrol, şeffaflık ve güvenlik sunarak bilgisayarlarının potansiyelini en üst düzeye çıkarmalarına olanak tanıyor. Bu yazıda, Libreboot'un neden önemli olduğunu ve Lenovo ThinkPad X200s model bilgisayarıma nasıl kurulum yaptığımı paylaşacağım.

## Libreboot projesi

Libreboot, yapı olarak Coreboot'u baz alır. BIOS yazılımlarını özgür hale getirmeyi hedefleyen Coreboot projesinde farklı marka ve model cihazlar için imaj oluşturmak oldukça güç olmasından dolayı belirli ve uyumlu modeller için hazır konfigürasyonlarla kolaylıkla imaj oluşturma işlemine Libreboot aracılığıyla olanak sağlanmaktadır. Libreboot'un başlıca avantajları şu şekilde;

- **Güvenlik:** Libreboot, üreticilere ait kapalı kaynak BIOS yazılımlarının potansiyel güvenlik açıklarını ortadan kaldırır. Açık kaynak kodlu olması sayesinde yazılımda herhangi bir arka kapı veya zararlı kod bulunma olasılığı çok düşüktür. Güvenlik araştırmacıları ve kullanıcılar kaynak kodu inceleyebilir ve gerektiğinde değişiklik yapabilirler.
- **Özgürlük:** Geleneksel BIOS yazılımlarında kullanıcılar genellikle sınırlı seçenek ve ayarlara sahiptir. Bu yazılımlar, donanım üreticileri tarafından belirlenen özelliklerle sınırlıdır. Ancak Libreboot açık kaynak kodlu olduğu için kullanıcılar istedikleri değişiklikleri yapabilir, sistemi ihtiyaçlarına göre özelleştirebilir ve kendi kodlarını ekleyebilirler. Bu sağlanan avantaj kullanıcıların bilgisayarlarını tam anlamıyla kendi isteklerine göre yapılandırmalarını sağlar.
- **Gizlilik:** Kapalı kaynak BIOS yazılımlarında kullanıcı verilerinin nasıl işlendiği ve saklandığı veya bir arka kapı olup olmadığı konusunda belirsizlikler olabilir. Özellikle Intel Management Engine faciası ile başlayan bu soru işareti Libreboot ile son bulmaktadır. Yazılım tamamen açık kaynak olduğu için kullanıcılar verilerinin nasıl işlendiğini ve saklandığını tam olarak bilirler ve gizliliklerini koruyabilirler.

### Gereksinimler

Maalesef ki ilk Libreboot kurulumu biraz zahmetli. Eğer hala bu işe girişmek için bir bilgisayar edinmediyseniz kurulum işlemlerinin cihazı sökmeden daha kolay yapılabildiği bir Dell Latitude E6400 arayışına girebilirsiniz. Bizim örneğimizdeki Lenovo Thinkpad X200s için aşağıdaki gereksinimlere ihtiyaç var;
- Libreboot uyumlu bir bilgisayar. Örnek; X200s.
- Derlenmiş uygun Libreboot imajı.
- External flash için Raspberry Pico H.
- Bilgisayarın BIOS çipi ile uyumlu test clip.
- Linux dağıtımı çalışan ikinci bir bilgisayar.
- Bir miktar erkek ve dişi jumper kablo.

İhtiyaçları kendi yöntemlerinize göre değiştirebilirsiniz. Örneğin, çipi yazmak için Raspberry Pico H yerine Arduino tercih edilebilir. Ancak CH341A kullanmamalısınız. Ben bu hatayı yaparak bir tane T420 yaktım. Bir diğer madde, Linux dağıtımı çalışan bilgisayar şart değil. Windows altında WSL kullanarak yine aynı işlemleri gerçekleştirebilirsiniz.

## Hazırlık

Eğer buraya kadar okuduysanız tebrikler. Sizin de artık bu deneyimi yaşayacak motivasyonunuz mevcut ve brick olma pahasına gözden çıkardığınız bir bilgisayarınız var veya almayı planlıyorsunuz. Şimdi bu uzun ve dikenli yolda bizi bekleyenlere göz atalım.

### BIOS çipi

Thinkpad X200s, düz X200 modeline göre daha az güç tüketen CPU'ya sahip ve görece daha hafif bir bilgisayar. Özünde çok benzerlikleri olsa da bilgisayarın içi kardeşinden farklı. Bunlardan birincisi X200 modelinde BIOS çipine ulaşmak için sadece palmrest'i çıkarmak yeterliyken, X200s modelinde anakartı tamamen çıkarmak gerekli. İkincisi ise kullanılan BIOS çipleri. X200 modeli SOIC8 ve SOIC16 tipi çipleri kullanırken, X200s modeli WSON8 tipi çip kullanıyor. WSON8 çiplerin bacakları çok küçük olmasından dolayı standart clip'ler ile dışardan müdahale edilemiyor.

{{< figure align=center src="images/soic8_wson8.png" caption="SOIC8 ve WSON8 çiplerin farkı. (Alıntı)" >}}

Genelde WSON8 çiplere müdahale etmek için yerinden söküldüğü veya ayaklarına lehim yapıldığı örnekler mevcut. Hem bilgisayarın orjinalliğini bozmamak için hem de muhtemel daha kolay bir yöntemin varlığına inancımdan ötürü bu yöntemler dışında farklı seçenekleri bir süre araştırdım. Hiç kullanıldığına şahit olamama rağmen bu top ayaklara sahip olan çipi programlayabilmek için gereken test probe'u uzak doğudan bilgisayarı aldığım ücretin neredeyse yarısı kadarını daha ödeyerek sipariş verdim. Mandal gibi bir mekanizmaya sahip olmayan probe, kullanırken el ile sabit tutulmaya ihtiyaç duyuyor.

### İmajın hazırlanması

Libreboot imajını hazırlamak, kurulum sürecinin önemli bir adımıdır. Libreboot, destekleyen cihazlar için konfigürasyonları bünyesinde barındırmakta. Kaynak kodları derlenerek gerçekleştirilen imaj oluşturma işlemi, sabırlı olunması gereken bir aşama. Derleme işlemi için bağımlılık paketlerinin de ayrıca kurulması gerekiyor. Aşağıdaki komutlar sırasıyla çalıştırılarak derleme işlemi başlamakta. Uzun sürecek bu işlem devam ederken kendinize kahve alabilirsiniz.

```
git clone https://codeberg.org/libreboot/lbmk.git
cd lbmk
./build dependencies ubuntu
./build roms x200_8mb
```
Derleme işlemi bittikten sonra proje dizininde bulunan **bin/x200_8mb/** klasörü altında çeşitli özelliklerde imajlar bulunacaktır. Benim tercihim bunlar arasından US klavye destekli, grafik arayüze sahip GRUB bootlader varsayılan olan **grub_x200_8mb_libgfxinit_corebootfb_usqwerty.rom** imajı oldu. İlerleyen aşamalarda bu dosya bilgisayarın BIOS çipine yazılmak için kullanılacak.

### Pico H ve serprog

Raspberry Pico, bildiğimiz Raspberry Pi'den çok daha basit bir mikrokontrolcü kartı. Cihaz, C ve MicroPython ile programlanabiliyor. Ağırlıklı olarak IOT ve hobi çalışmalarında kullanılan bu kart herhangi bir USB cihazını simule edebildiği için tam bir bukalemuna dönüşüyor. Örneğin; bir tanesini mouse jiggler olarak kullanırken diğerini bu projede BIOS çipini programlamak için kullandım. Dikkat edilmesi gereken nokta üzerinde pinleri bulunduran H modelini tercih etmek. Düz modelin üzerinde pinler hazır bulunmuyor. Tabii ki siz bu aşamayı alternatif cihaz ve yöntemlerinizle gerçekleştirebilirsiniz.

{{< figure align=center src="images/pico.png" caption="Raspberry Pico ve Pico H farkı. (Alıntı)" >}}

Geliştirme kartını programlayıcı kimliğine büründürmek için birtakım işlemlere ihtiyaç var. Bu iş için kullanılan Serial Flasher Protocol (serprog) özelliğini geliştirme kartına kazandırmak için kötü günler için sakladığımız tercihen Ubuntu kullanan bilgisayarımızdan ilgili projeyi derleyip gerekli yükleme işleminin karta yapılması gerekli. Aşağıdaki komutları sırasıyla çalıştırarak projenin kaynak kodlarını lokale indirip derlendikten sonra programcık oluşacaktır.

```
git clone https://codeberg.org/libreboot/pico-serprog.git
cd pico-serprog/
export PICO_SDK_FETCH_FROM_GIT=https://github.com/raspberrypi/pico-sdk
cmake .
```
Derleme işlemi tamamlandıktan sonra oluşan **pico_serprog.uf2** dosyasının Pico cihazına kopyalanması gerekiyor. Kartın üzerindeki butona basılı tutarak bilgisayara bağlandığında, cihaz çıkarılabilir bir depolama birimi olarak algılanacaktır. Oluşan UF2 dosyasını bu depolama alanına kopyaladıktan sonra, yükleme işlemi tamamlanmış olacaktır. Gerçekten oldukça kolay bir işlem. Yapılan işlemin doğrulanması için, kartı Linux kurulu bir bilgisayara bağladıktan sonra dmesg çıktısına bakabilirsiniz..

```
cdc_acm 2-1.2:1.0: ttyACM0: USB ACM device
```
Yukardakine benzer bir log kaydı düşmüşse eğer işlem başarıyla gerçekleşmiştir.

### Flashprog

Libreboot projesi, çipin programlanması için flashrom yerine flashprog aracının kullanılmasını öneriyor. Maalesef bunun da derlenmesi gerekli. Aşağıdaki komutlar sırasıyla çalıştırılarak derleme işlemi yapılabilir;

```
apt update
apt install pciutils libpci-dev libgpiod-dev zlib1g
git clone https://github.com/SourceArcade/flashprog.git
cd flashprog/
make
```

Derleme işlemi bittikten sonra proje dizninde **./flashprog -R** komutu ile aracı çağırarak test edebilirsiniz.

## Operasyon

Yanlış yok, başlıyoruz! En heyecanlı ve sonuca en çok yaklaştığımız bu aşama aynı zamanda da en fazla dikkat gerektiren kısım. Tüm hazırlıkları tamamladıktan sonra geriye bilgisayarı söküp bağlantıların yapılması ve sonra çipin programlanma işlemi kalıyor. Bağlantıları tamamlamak için Pico kartın BIOS çipine karşılık gelen pinleri aşağıdaki görselde bulunuyor. 

{{< figure align=center src="images/pico_connections.png" caption="Pico kartındaki pinlerin BIOS çipinde karşılıkları. (Alıntı)" >}}

Bilgisayarı parçalarına ayırdıktan sonra ancak anakartın arka tarafında bulunan BIOS çipine ulaşılabiliyor. Çıkarılan her vidaya ait yerin not alınması faydalı olacaktır. Çok sayıda ve farklı çeşitlerde olan bu vidalar, bilgisayarın geri toplanması gerektiğinde karışabiliyor.

{{< notice warning >}}
Jumper kablolar ile dikkatlice yapılması gereken bu bağlantı işlemi, doğru yapılmadığı taktirde BIOS çipinin arızalanmasına neden olabilir.
\
\
X200s modelinde BIOS çipine ulaşmak için anakartın çıkarılması gerektiğinden yukarda bahsedildi. Çok sayıda vidanın söküldüğü bu işlem, yatkın olmayan ellerde başarısızlıkla sonuçlanabilir. Oluşabilecek kayıp ve zararlardan üzülerek söylüyorum ki ciddikedi mesuliyet kabul etmez.
{{< /notice >}}

Kamu spotunu geçtiğimize göre işleme start verebiliriz. Test probe'u, çipin üzerinde ilk pini işaret eden noktaları üst üste gelecek şekilde konumlandırdıktan sonra aşağıdaki komutları çalıştırarak programlama işlemi başlatılabilir. Öncelikle iki farklı read işlemi yaparak farklarını gözlemleyip bağlantıların ve konfigürasyonun doğruluğundan emin olunması gerekiyor.

```
./flashprog -c MX25L6405D -p serprog:dev=/dev/ttyACM0,spispeed=16M -r dump.bin
./flashprog -c MX25L6405D -p serprog:dev=/dev/ttyACM0,spispeed=16M -r dump2.bin
diff dump.bin dump2.bin
```

Eğer bir fark yoksa programlama işlemi aşağıdaki komutla başlatılabilir.

```
./flashprog -c MX25L6405D -p serprog:dev=/dev/ttyACM0,spispeed=16M -w grub_x200_8mb_libgfxinit_corebootfb_usqwerty.rom
```

Bir diğer dikkat edilmesi gereken nokta programlama işlemi bittikten sonra probe veya clip bağlantısını kesmeden önce programlayıcının bağlantısını bilgisayardan kesmek olmalı.

{{< figure align=center src="images/flash.png" caption="BIOS çipinin programlanması." >}}

Bir elimle test probe'u tutup diğer elimle Enter tuşuna bastığım bu riskli işlem tamamlanana kadar elim titremesin diye nefes bile almadım. Tabii ki o kadar emek boşuna gitmedi ve programlama işlemi tamamlandıktan sonra Libreboot kurulu bir X200s sahibi oldum. Bilgisayar açıldığında herhangi bir splash ekranı olmadan doğrudan GRUB açılması çok tuhaf gelse de artık cihazın ayarlarını az da olsa düzenlenebildiği bir BIOS menüsünün olmaması beni daha çok şaşırttı. Linux kurulumu için GRUB bootloader açılışta otomatik başlamasına ek olarak Windows çalıştırmak için SeaBIOS seçenekler arasında bulunuyor ancak uyumluluk konusu ciddi anlamda problemli.

{{< figure align=center src="images/x200s_libreboot.jpg" caption="Libreboot, X200s üzerinde başarıyla çalışıyor." >}}

## Sonuç

Libreboot kurulumu sayesinde Lenovo ThinkPad X200s, özgür ve güvenli bir sistem olarak yeniden hayat buldu. Bu zorlu ancak keyifli süreç kullanıcıya tam kontrol sağlarken, aynı zamanda eski donanımların modern güvenlik standartlarına uygun hale gelmesini mümkün kılıyor. Libreboot kurulumunun ardından heyecanla Arch Linux'u yükleyerek, sistemin hafif ve hızlı bir dağıtım ile çalışmasını sağladım. Arch Linux'un sunduğu özelleştirme imkanları ve minimalist yaklaşımı sayesinde bilgisayarı ihtiyaçlarıma göre tam anlamıyla kişiselleştirdim. Ayrıca window manager olarak i3 kullanarak, kaynakları verimli bir şekilde yönetip, çalışma ortamımı ihtiyaçlarıma göre optimize ettim. Libreboot ile elde edilen esneklik ve güvenlik, bu tür projelere ilgi duyan fantezi severler için büyük merak uyandırıyor.

{{< figure align=center src="images/x200s_result.jpg" caption="Libreboot X200s, The Ultimate Hacker's Notebook." >}}