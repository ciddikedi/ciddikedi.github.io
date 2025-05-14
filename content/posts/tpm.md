---
title: 'BitLocker korumasını saniyeler içerisinde kırın!'
date: 2025-05-14T10:38:29+03:00
draft: false
cover:
  image: "posts/tpm/images/tpm_cover.jpg"
  alt: "Pico TPMSniffer"
  caption: ""
  relative: false
---

## Giriş

Her yıl milyarlarca dolar siber güvenlik çözümlerine harcanıyor. Yazılımlar, bulut servisleri, yapay zeka destekli tehdit avcılığı gibi ileri teknolojilerle sistemler korunmaya çalışılıyor ancak tüm bu yüksek teknoloji çözümlerin gölgesinde fiziksel güvenlik çoğu zaman ikinci planda kalıyor. Kişisel olarak ben de kendi önlemlerimi alıyorum. Örneğin; Plajda cep telefonum fark edilmesin diye üzerini havlu ile örtüyorum veya yurtdışı seyahatlerimde pasaport cüzdan gibi değerli eşyaları sırt çantamın arka bölmelerinde saklıyorum. Benim amatör önlemlerim bir yana kişisel verilerimizi içeren teknolojik cihazlarımız istenmeyen ellere geçtiğinde mahremiyeti ve güvenliği sağlamak amacıyla tasarlanmış belli teknolojiler mevcut. Disk şifreleme bunlardan birisi. Microsoft tarafından sunulan BitLocker, her ne kadar diskteki veriyi bitlerine kadar şifrelese de dışardan yapılan fiziksel müdahalelerde nasıl aciz kalıyor beraber inceleyelim.

## TPM nedir?

Günümüzde birçok bilgisayarın anakartında gömülü bulunan bu küçük yonga özellikle Windows 11 ile birlikte çok daha popüler hale geldi. TPM, yani Trusted Platform Module olarak adlandırılan bu donanım tabanlı güvenlik bileşeni, sistemin güvenliğini sağlamak için kriptografik anahtarları güvenli bir şekilde saklar ve kullanır. TPM, dijital dünyada bir nevi **kasa** görevi görür ve içerisine bir şey koyduğunuzda onu dışarıdan doğrudan almanız mümkün değildir. Ancak sistem uygun koşullar altında bu kasayı açabilir. BitLocker gibi disk şifreleme teknolojileri de tam bu noktada TPM’in sunduğu güvenlik garantilerinden faydalanır.

{{< figure align=center src="images/tpm_diagram.png" caption="ST33 yongasının donanım diyagramı. (Alıntı)" >}}

Detaylarından daha ileride bahsedeceğim kurban bilgisayar, TPM olarak STMicroelectronics üretimi ST33ZP24AR28PVSP yongasını kullanıyor ve bu yonga basit bir devre elemanı olmaktan öte içerisinde 25 MHz hızında çalışan bir ARM SC300 işlemci barındırıyor. Yani TPM yongası sadece veri saklamıyor; aynı zamanda şifreleme, çözme ve kimlik doğrulama gibi işlemleri kendi içinde gerçekleştiriyor. Dolayısıyla bu durum TPM’in aktif bir güvenlik bileşeni olduğunun açık bir göstergesi.

Tasarlanan mimari, TPM’i bir nevi bağımsız bir güvenlik sistemi haline getiriyor. Başka bir deyişle sistemin güvenliği sadece işletim sisteminin veya uygulamaların kontrolüne bırakılmıyor, aynı zamanda donanım seviyesinde dış dünyadan izole bir işlemcinin kararlarına da bağlı oluyor. Kritik şifreleme ve şifre çözme işlemleri fiziksel olarak erişilemeyen dış müdahalelere karşı korumalı bir alanda gerçekleşiyor.

### Nasıl Çalışır?

TPM yongasının aslında kendi başına ayrı bir cumhuriyet olduğundan bahsetmiştik. Tam olarak nasıl çalıştığını anlayabilmek için birkaç temel bileşen hakkında fikir sahibi olunması gerekiyor.

- **EK - Endorsement Key:** Yonga üreticisi tarafından üretim sırasında donanım içerisine kalıcı olarak gömülen RSA anahtarıdır. Yonganın benzersiz olmasında rol oynar ve dışarı çıkartılamaz, değiştirilemez.

- **SRK - Storage Root Key:** TPM tarafından korunan tüm anahtarları şifrelemek için kullanılır. Cihaz ilk kurulduğunda oluşturulur ve dışarı çıkartılamaz. Değiştirilemez fakat yeniden üretilebilir.

- **AK - Attestation Key:** Sistemin açılış ölçümlerini imzalar ve güvenilirliğini doğrular. Değiştirilebilir ve yeniden oluşturulabilir.

- **PCR - Platform Configuration Register:** TPM üzerinde bulunan özel kayıt alanlarıdır. Sistemin açılış ölçümlerinin hash değerini tutar ve her açılışta teyit eder. Bu değerlerin içerisinde BIOS kodu, boot loader, Secure Boot gibi bilgiler yer alır ve eğer açılışta bu bilgiler eşleşmezse sistem açılmayı reddeder. Attestation Key bu alanı imzalar.

- **RNG - Random Number Generator:** Kriptografide tahmin edilemez rastgele sayı üretmek için kullanılır. Donanım temellidir ve anahtar üretimi, şifreleme işlemleri aşamalarında kullanılr.

Bunların dışında TPM yongası verileri şifrelemek için bünyesinde hem simetrik algoritmalar hem de asimetrik algoritmalar için donanımsal motorları barındırır. Böylelikle şifreleme ve şifre çözme işlemleri hem daha hızlı olur hem de bağımsız bir ortamda gerçekleştirildiği için daha güvenli olur. Kendine ait kilobyte seviyesinde küçük de olsa bir RAM bulunan bu yonga, bünyesinde barındırdığı şifreleri hiçbir şekilde dışarı çıkartmaz.

#### Sealing ve Unsealing

TPM üzerinde sealing ve unsealing işlemleri, güvenli veri depolama ve veri koruma amacıyla kullanılan temel özelliklerdir. TPM'nin en önemli özelliklerinden biri verilerin yalnızca güvenli bir ortamda yani belirli bir şartlar sağlandığında erişilebilir olmasını garanti eden sealing ve unsealing işlevleridir.

- **Sealing:** Verileri TPM üzerinde SRK kullanarak şifreler ve sistemin son durumunu PCR üzerine kaydederek sistemin güvenliğini garanti altına alır. Bir kere çalıştırılır. Örneğin; BitLocker kurulumunda ihtiyaç duyulan şifreli anahtarın üretilmesi bir sealing işlemidir.

- **Unsealing:** Şifrelenmiş verinin TPM üzerinde çözülmesi işlemidir. Sealing işlemi sırasında oluşturulan PCR koşulları sağlanıyorsa TPM veriyi açar ve sistemin kullanımına sunar. Sistem her boot olduğunda şifrelenmiş diske ait anahtarı kullanabilmek için çalıştırılır.

## BitLocker

Windows işletim sistemlerinde kullanılan bir güvenlik özelliği olan BitLocker, disk üzerindeki verileri şifreler ve bilgisayarın çalınması veya bilgisayardaki diskin üçüncü şahısların eline geçmesi durumunda yetkisiz erişimin önüne geçer. Bilgisayara ait anakartın değiştirilmesi veya şifrelenmiş diskin farklı bir bilgisayara takılması durumunda sistem açılmayı reddeder. Güvenli ortam ve uygun koşullar sağlandığı sürece BitLocker, sisteme güvenerek disk alanını okunabilir hale getirir. Bu yöntemle yapılan şifreli disk okuma ve yazma işlemleri işlemcinin AES şifreleme hızlandırıcıları üzerinde yapılır ve performans kaybı göz ardı edilebilecek düzeydedir.

### TPM - BitLocker ilişkisi

BitLocker esasında sadece bir disk şifreleme teknolojisi gibi gözükse de TPM gibi donanımsal bir güvenlik yongasıyla birlikte çalışır. TPM'in bu noktada iki tane görevi vardır. Birincisi üzerindeki SRK'yı kullanarak BitLocker anahtarını şifreler. İkinci görevi ise PCR koşullarını kontrol ederek sistem açıldığında şifrelenmiş anahtarı çözer. Biraz daha detaya girmek için bazı tanımlar hakkında fikir sahibi olmak gerekiyor.

- **FVEK - Full Volume Encryption Key:** Diskteki verinin tamamını şifrelemek için kullanılan asıl anahtardır. Veri şifrelemede kullanıldığı için AES algoritmasını kullanır.

- **VMK - Volume Master Key:** Asıl disk şifreleme anahtarı olan FVEK'i çözer. Diskin Volume Header'inde şifrelenmiş olarak saklanır. Sistem boot ettiğinde TPM tarafından çözülür ve RSA algoritmasını kullanır.

- **Recovery Key:** BitLocker kurulumu sırasında kullanıcıya verilen 48 haneli özel kurtarma anahtarıdır. TPM yapılandırması bozulduğunda veya disk üzerindeki veriler kurtarılmak istendiğinde kullanılır. Recovery Key, şifrelenmiş VMK'yı çözer.

Kısacası daha anlaşılır olması amacıyla basite indirmek gerekirse şifrelenmiş diskin sırasıyla çözülme aşamaları aşağıdaki gibidir.

```
Recovery Key: 48 haneli parola
Volume Header'daki VMK bloğu: Şifreli VMK
Recovery Key + Şifreli VMK -> Açık VMK
Açık VMK + Şifreli FVEK -> Açık FVEK
Açık FVEK -> NTFS verisi
```

Özet niteliğindeki bu açıklama konuyu genel hatlarıyla anlamamıza yardımcı oldu ancak konunun teknik yönünü daha derinlemesine incelemek isteyenler için şimdi sürecin bir de uzuncasını inceleyelim. Sistemimizin daha modern olduğunu varsayarak Legacy BIOS yerine UEFI sürecinden bahsedeceğim.

1. **UEFI aşaması:** Sistem güç verildiğinde çalışan ilk aşamadır. Temel donanımlar başlatılır ve bileşenler algılanır. Eğer şartlar uygunsa Boot Device Selection aşamasına geçilir. Boot edilecek disk bilgilerinin yanında bootloader bilgisi UEFI belleğinde saklanır. Aksi belirtilmediği sürece Windows için varsayılan boot manager ``\EFI\Microsoft\Boot\bootmgfw.efi``yolundadır. UEFI uyumlu sistemler için FAT32 formatlı EFI partitionu olmak zorundadır ve eğer herhangi bir bootloader tanımı yapılmadıysa ``\EFI\Boot\bootx64.efi``yolu varsayılan olarak kullanılır.
\
&nbsp;
2. **Boot Manager ve Loader:** UEFI tarafından yüklenen ``bootmgfw.efi``, hangi işletim sistemi veya hangi seçenekle devam edileceğine karar verir ve bootloader'i yani ``winload.efi``'yi çalıştırır. Bootloader sistemin başlaması için temel sürücülerin yanında ``fvevol.sys`` yani Full Volume Encryption Driver'i de belleğe yükler.
\
&nbsp;
3. **TPM ile şifre çözme:** Disk şifreleme sürücüsü öncelikle mevcut diskin Volume Header'ini okuyarak şifreli VMK'yı TPM'e gönderir ve şifre çözme talebinde bulunur. Eğer PCR üzerinde bulunan hash değerleri tutarlıysa diskteki şifreli VMK, TPM üzerindeki SRK kullanılarak çözülür ve sisteme geri gönderilir. Sürücü, şifresi çözülmüş VMK'yı kullarak FVEK'i çözer ve diski okunabilir hale getirir.
\
&nbsp;
4. **Windows yüklenmesi:** Her şey uygun olduğuna göre artık disk üzerinde bulunan ``ntoskrnl.exe`` çekirdeği çalıştırılır ve kontrol işletim sistemine bırakılır. Donanım sürücüleri ve servisler yüklenerek Windows başlatılır.

### Problem!

Üzerinde bu kadar ince düşünülmüş bir sistemde ne gibi bir problem bulunabilir? Aslında biraz daha basit yaklaşmak gerekiyor. Yukarıda bulunan 3. maddeyi tekrar okuyun. Şifresi çözülmüş VMK sisteme geri gönderiliyor. Açık bir şekilde...

TPM yongaları çeşitlerine göre veya üretinin tercihine göre sistemle LPC yani paralel ya da SPI yani seri olarak haberleşiyor. Anakart üzerinden Logic analyzer ya da FPGA gibi cihazlarla sniff edilebilen bu haberleşmede şifresi çözülmüş VMK elde edilebiliyor. İnanılmaz!

## Hazırlık

Yeteri kadar teknik detaya boğulduysak eğer artık kolları sıvamaya başlayabiliriz. Bu çalışmadaki amacımız Windows işletim sistemine sahip bir bilgisayarın BitLocker anahtarını fiziksel olarak sniff etmek. Ben kurban bilgisayar olarak bir Thinkpad (Tabii ki de) X1 Carbon tercih ettim. Bu bilgisayar ultra hafif yapısı, uzay endüstrisi sınıfındaki karbon fiber kasası ve 2K ekranı ile markanın en tepesinde konumlandırılan premium bir cihaz.

{{< figure align=center src="images/x1_carbon.jpg" caption="Lenovo Thinkpad X1 Carbon" >}}

Öncelikle cihazımızı tanımamız gerekiyor. Neye, nasıl müdahale edeceğiz? Birincisi bilgisayarın üzerinde bulunan TPM yongasının belirlenmesi lazım. Bu yonga sistemle haberleşmek için hangi arabirimi kullanıyor? Bu cevapları vermemiz için bilgisayara ait datasheet'lerin incelenmesi gerekiyor. Bununla da kalmıyor, boardview şemalarının da gözden geçirilmesi gerekiyor. Eğer yeterince şanslıysanız bu şemalar internet ortamında bulunabiliyor fakat nadir modellerde maalesef ücretli satılması da mümkün. 

{{< notice warning >}}
TPM yongasına veya anakart üzerindeki debug noktalarına erişmek için arka kapağın açılması gerekmektedir. Bu işlem cihazınızı garanti kapsamından çıkarabilir, dahası hiç çalışmamasına neden olabilir.
\
\
Eğer ne yaptığınızı tam olarak bilmiyorsanız burdan sonra bahsedeceğim işlemler risk oluşturmaktadır zira ben bir tane T460 model dizüstü bilgisayarımın çalışmaz hale gelmesine neden oldum.
{{< /notice >}}

### Datasheet ve Boardview incelemesi

Çalışmaya başlamamda bana ilham olan güvenlik araştırmacısı Thomas Roth veya takma adıyla stacksmashing, yaklaşık bir yıl kadar önce bu işe oldukça mesai harcamış ve LPC debug portu bulunan bilgisayarlardan saniyeler içerisinde VMK anahtarını elde etmeye aracılık eden bir devre kartı tasarlamış. TPM sniffing işlemi için tek yöntem bu değil fakat kesinlikle en kolayı. Farklı bilgisayarlar farklı arabirimlerle TPM çipiyle konuşabildiği gibi hepsinde bu debug portu bulunmuyor. Sahip olduğum bilgisayarın datasheet'ini incelerken bu porta sahip olduğunu gördüm ve heyecanla araştırmaya başladım.

{{< figure align=center src="images/lpc_datasheet.png" caption="LPC debug portunun datasheet'i" >}}

Porta ait detaylar görselde yer alırken ilginç bir ayrıntı daha bulunuyor. Portun numarası J7. Bu numarayı ilerde kullanacağız. Peki ne anlama geliyor. Devre şemaları üzerinde her harfin bir anlamı vardır. Rakamlar ise benzersizdir.

- **C:** Kapasitör
- **R:** Direnç
- **L:** Bobin
- **Q:** Transistör
- **D:** Diyot
- **J:** Jack ya da konnektör
- **U:** Entegre

Bu harfler şema üzerinde C2, R5, J4 gibi rakamlar ile birlikte kullanılır. İlgili port bilgisayarımızda mevcut olduğuna göre sırada boardview incelemek var. Tabii ki öyle kolay ölüm yok. Burada da bir takım işlemler yapmaya devam edeceğiz. Bilgisayara ait datasheet'in yanında boardview'larına da ihtiyaç olduğundan bahsetmiştim. Bu boardview'ları açmak için hem ücretsiz hem de açık kaynak olduğu için OpenBoardView yazılımını tercih ettim. Bizim temin ettiğimiz boardview dosyası ``bv`` formatında fakat OpenBoardView ``bvr`` formatını destekliyor. Dosyayı çevirmek için minik bir linux hamlesine ihtiyaç duyacağız. Bu dönüştürmeyi yapan projeyi aşağıdaki komutlar yardımıyla indirip build ediyoruz.

```
git clone https://github.com/inflex/bv2bvr
cd bv2bvr
yum install glib glibc glib2 mdbtools-libs mdbtools-devel
make
./bv2bvr ../x1_boardview.bv > outputfile.bvr
```

Dosyamızı OpenBoardView yazılımının desteklediği formata dönüştürdüğümüze göre içeriğini detaylıca inceleyebiliriz. Oldukça karmaşık görünen anakartın devre görüntüsünü biraz gözlemleyince J7 portunu bulmak çok da zor olmuyor. 

{{< figure align=center src="images/j7_header.jpg" caption="J7 portunun boardview ve anakart üzerindeki konumu." >}}

Bahsi geçen portu üretici benim plajda cep telefonumun üzerine havlu koymam misali bir etiketle çok profesyonelce gizlemiş. Etiketi kaldırdığımızda ihtiyacımız olan LPC debug header bizi karşılıyor ve heyecan katsayımız yükseliyor. Bu portu kullanarak TPM üzerindeki LPC iletişimini dinleyeceğiz. 

{{< figure align=center src="images/lpc_debug.jpg" caption="Anakart üzerindeki LPC debug header." >}}

### Devre kartı

Yukarıda da bahsettiğim gibi, açık trafiğin dinlenmesi için çok sayıda yöntem mevcut. Tercih tamamen hayalgücü ve yatkınlığınıza kalmış. TPM yongasının LPC ya da SPI arabirimine sahip olmasına göre Logic analyzer, FPGA kullanılabilir. Bunların dışında çok daha basit ve maliyeti düşük bir yöntem daha var. Raspberry Pico kullanmak. Bu ucuz ve mucizevi kart olmasa muhtemelen bana da blog içeriği çıkmayacaktı. Raspberry Pico görece ilkel bir cihaz olduğu için SPI iletişimini yakalamakta yetersiz olsa da LPC iletişimini dinlemekte oldukça iyi. 

Alman asıllı Thomas Roth isimli güvenlik araştırmacısı Raspberry Pico kullanarak LPC debug portunu dinlemeye yarayacak bir devre kartı tasarlamış ve kodlamasını yapmış. Dahası bu güvenlik zaafiyetini derinlemesine araştıran ilklerden birisi. Kendisini aynı zamanda enteresan çalışmalara imza atması ve fotoğrafçılığa olan ilgisinden dolayı uzun zamandır takip ediyorum. Proje detaylarına [buradaki](https://github.com/stacksmashing/pico-tpmsniffer) repodan ulaşabilirsiniz. Devre kartını toplayabilmek için ihtiyaç olanlar listesi oldukça mütevazi. Bir Raspberry Pico, 10 adet P50-B1-16mm pogo pin ve devre kartı. Repoda paylaşılan devre kartının çizimlerini JLCPCB sitesinden kargo ve gümrük dahil 15$ gibi bir rakama 5 tane bastırdım. İlgili site çizim olarak Gerber kabul ettiği için dönüştürme işleminde KiCad uygulamasından yardım aldım. Devre kartının toplanması için her ne kadar çok istekli olsam da ince işçilik gerektirdiği için profesyonel destek aldım.

{{< figure align=center src="images/tpm_sniffer.jpg" caption="TPM sniffer devre kartı." >}}

Son olarak Raspberry Pico'nun programlaması işlemi kalıyor. Bu projedeki en önemli adımlardan biri çünkü yazdığımız ya da edindiğimiz kodun mikrodenetleyici tarafından çalıştırılabilir hale gelmesi için uygun biçimde derlenmiş olması şart. Derleme işlemi sırasında kaynak kodlar belirli bir derleyici aracılığıyla işlenerek Pico’nun anlayacağı makine diline çevriliyor. Derleme işlemi için tabii ki bir Linux tabanlı işletim sistemi kullanacağız. 

```
yum install epel-release
yum install arm-none-eabi-gcc-cs arm-none-eabi-newlib arm-none-eabi-binutils arm-none-eabi-gdb
git clone https://github.com/stacksmashing/pico-tpmsniffer
git clone https://github.com/raspberrypi/pico-sdk
export PICO_SDK_PATH=$(pwd)/pico-sdk
cd pico-tpmsniffer
mkdir build
cd build
cmake ..
make
```

Derleme işlemi başarıyla tamamlandığında projenin build dizini içerisinde ``.uf2`` uzantılı bir dosya oluşacaktır. Bu dosya, Raspberry Pi Pico’ya yüklenmeye hazır olan çalıştırılabilir imaj dosyasıdır. Pico’yu programlama moduna almak için cihazın **BOOTSEL** butonuna basılı tutarak USB kablosu aracılığıyla bilgisayara bağlamanız gerekir. Bu işlemin ardından Pico harici bir USB depolama aygıtı gibi tanınacaktır. Son olarak, oluşturulan ``.uf2`` dosyasını bu aygıta kopyaladığınızda yükleme işlemi otomatik olarak başlayacak ve kısa süre içinde tamamlanacaktır. Bu adımın ardından Pico cihazımız yüklediğiniz yeni yazılımla çalışmaya hazır hale gelecektir.

## Operasyon

Sizce bu kadar hazırlığı boşuna mı yaptık? Balyozları getirin, kırıyoruz! İşlemin saniyeler süren aşamasına sonunda geldik. Benim aylarımı alan araştırma ve gereksinimleri tamamlama sürecinde şifrenin tek haneli saniyeler içerisinde elde edildiğine şahit olduğunuzda siz de şaşıracaksınız. Eğer bilgisayarın arka kapağının açılmasını saymazsak TPM iletişiminde VMK'nın dinlenmesi iyimser bir ifadeyle 10 saniyeden daha az sürüyor.

{{< figure align=center src="images/raspberry_on_mb.jpg" caption="Devre kartının debug header üzerine temas ettirilmesi." >}}

Devre kartının üzerine montajladığımız Raspberry cihazımızı bilgisayara bağladığımızda bir USB seri cihazı olarak tanınacaktır. Aygıta atanan COM portunu kullanarak bir terminal uygulaması ile standart hız ve ayarları kullanarak bağlanabiliriz. Bilgisayara bağlanan Raspberry Pico, çalışmaya hazır hale geliyor ve anakartın Debug nokasına temas ettirilmek üzere bekliyor. Devre kartımızı yukarına yerini tespit ettiğimiz LPC debug header'ine temas ettirip bilgisayarımızı boot ettiğimizde BitLocker anahtarı saniyeler içerisinde ekrana yansıyor.

```
|_) o  _  _
|   | (_ (_)

88P'888'Y88 888 88e    e   e        dP"8         ,e,  dP,e,  dP,e,
P'  888  'Y 888 888D  d8b d8b      C8b Y 888 8e   "   8b "   8b "   ,e e,  888,8,
    888     888 88"  e Y8b Y8b      Y8b  888 88b 888 888888 888888 d88 88b 888 "
    888     888     d8b Y8b Y8b    b Y8D 888 888 888  888    888   888   , 888
    888     888    d888b Y8b Y8b   8edP  888 888 888  888    888    "YeeP" 888

Ready to sniff!
Bitlocker Volume Master Key found:
b0 a8 19 fc 9d b6 3f dd  eb b7 c2 db 15 db bc a1
80 60 93 4f bd 0d de ac  76 bc 23 b0 47 59 56 fd
```

Çıktıdan da tahmin edildiği üzere son iki satır VMK anahtarının hex halini ifade ediyor. Gerçekten çok kolay oldu. İşlem aşamaları şu şekilde; Pico içeren devre kartı USB aracılığıyla herhangi bir bilgisayara bağlanıyor ve bir terminal uygulaması ile seri arabirimine ulaşılıyor. Devre kartı, kurban bilgisayarın LPC debug header'ine temas ettiriliyor. Kurban bilgisayar Power butonu ile tetikleniyor. Sürpriz! Anahtar ekranda.

BitLocker anahtarını elde ettiğimiz bilgisayarın şifreli diskini okumak için onu herhangi bir live işletim sistemiyle başlatabiliriz. İkinci bir seçenek olarak diski harici bir ortama bağlayabiliriz. Ben bilgisayarı Ubuntu 22.04 ile live başlatmayı tercih ettim. Block cihazları listelediğimde ``sda`` diskinin üçüncü partition'u yani ``/dev/sda3`` yolu benim BitLocker ile şifrelenmiş diskimi ifade ediyordu.

Şifrelenmiş diski anahtar kullanarak mount etmek için **dislocker** isimli araçtan faydalanıyoruz. Dislocker Ubuntu'nun universe reposunda bulunuyor. Normalde şifreli diski mount edebilmek için FVEK kullanan araç, 0.72 versiyonu ile VMK kullanabilme yeteneğine de kavuşmuş. HEX olarak elde ettiğimiz VMK'yı bir text dosyasına yazıyoruz ve boşluklarından kurtulduktan sonra binary'e dönüştürüyoruz.

``cat key.txt | tr -d '\n ' | xxd -r -p > key.bin``

Anahtarımızı binary haline getirdikten sonra dislocker ile devam ediyoruz. Dislocker aracına hangi diski, hangi VMK ile açacağını söyledikten sonra bize bir mount point oluşturuyor. Bu mount point'i kullanarak şifreli diskimizi istediğimiz bir yere bağlayabiliyoruz.

```
apt install dislocker
dislocker -V /dev/sda3 --vmk key.bin -- /mnt/bitlocker
mount -o ro,loop /mnt/bitlocker/dislocker-file /mnt/windows-decrypt
```

Bitti! Aylar süren emek ve araştırmanın sonucunu saniyeler içerisinde tüketmek, güzel bir yemek için saatlerini verip dakikalar içerisinde bitirmeye benziyor. Gösterilen emek süresince alınan haz bir kenara, sonuç elde edince kazanılan tatmin ise çok daha farklı bir duygu.

{{< figure align=center src="images/list_files.png" caption="Şifrelenmiş diskin içeriği." >}}

## Sonuç

Bu çalışmada, şifrelenmiş bir diske yapılan erişimleri donanım seviyesinde izleyerek kullanılan şifreyi ortaya çıkardık. Raspberry Pi Pico veya benzeri cihazlar ile yapılan bu tür düşük seviyeli müdahaleler yalnızca teknik bir başarı değil, aynı zamanda dijital sistemlerin fiziksel müdahaleler karşısında ne kadar savunmasız olabileceğini de gözler önüne seriyor. Özellikle TPM 2.0 ile birlikte kullanıma sunulan **encrypted sessions** komutları sayesinde işlemci ile TPM yongası arasındaki iletişim şifrelense de maalesef Windows işletim sistemi henüz bunu desteklemiyor. Üzülerek söylemeliyim ki piyasadaki bilgisayarların çok büyük kısmı risk altında. Peki önlem olarak ne yapabiliriz?

- Kamuya açık riskli alanlarda bilgisayar çantamızın sırt bağını ayağımıza dolayabiliriz. Gayet ciddiyim.
- Özellikle Enterprise seviye bilgisayarlarda bulunan Preboot PIN özelliğini aktif edebiliriz. Bu özellik, disk boot edilmeden önce BIOS seviyesinde bir PIN talep ediyor.
- Thinkpad bilgisayarlarda bulunan Tamper Protection önlemini kullanabiliriz. Bilgisayarın içerisinde bulundan switch'ler sayesinde arka kapak açıldığında sistem çalışmayı reddediyor.

Böylesine kompleks bir sistemin kolaylıkla aşılabilmesi tabii ki akıllara komplo teorilerini veya arka kapı sorularını getirecektir. Unutulmamalıdır ki güvenlik öncelikli olarak insan faktöründen başlar. En güçlü sistemler bile yanlış ellerde savunmasız hale gelebilir.

{{< figure align=center src="images/neo.jpg" caption="BitLocker korumasını aştıktan sonra büründüğüm kişilik." >}}