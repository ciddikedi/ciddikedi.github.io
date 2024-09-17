---
title: 'Synology DS423+ incelemesi'
date: 2024-06-23T10:38:29+03:00
draft: false
cover:
  image: "posts/ds423plus/images/cover.jpg"
  alt: "DS423+"
  caption: ""
  relative: false
---

## Giriş

Verinin önemi konusunda herkes hemfikirdir. Klişe olan bu söz özellikle benim gibi disk arızaları sebebiyle defalarca veri kaybeden kullanıcılar için vurgulamak istediğim noktayı açıkça ortaya çıkarıyor. Bu ve benzer problemlere karşı amatör olarak ikinci bir konuma yedeklerimi almaya başlasam da verilerin büyüklüğü ve karmaşıklığı arttıkça kontrol edilemez noktaya gelmeye başladı. Tüm bu işleri çok daha basit ve her yerden ulaşılabilecek şekilde yapmak için NAS kutularına merak saldım. Çöp olarak nitelendireceğim Zyxel ve Netgear (üzgünüm sevgili marka temsilcileri) kutuları deneyimledikten sonra bu işin Apple'ı diyebileceğim Synology cihazlara yöneldim. Markanın Apple'a benzetimini minimum donanımdan maksimum yazılım verimine odaklandıkları için yapıyorum. Bu sihirli kutu; verilerinizi çok iyi şekilde koruyor, düzenliyor ve her yerden erişilebilir hale getirerek kişisel bir bulut gibi davranıyor.

## Teknik detaylar

Cihazın yetenekleri belli olsa da içerisinde bir Linux dağıtımı çalışıyor ve teknik özellikler performansını lineer etkiliyor. Maalesef ne kadar elim ayağım olsa da bu cihazın teknik özelliklerini yermeden geçmeyeceğim. Her zaman ezeli rakibi QNAP'in gerisinde kalan teknik detayların oluşturduğu açık, son derece stabil çalışan ve kullanışlı işletim sistemi DSM ile kapatılmaya çalışılmış. Cihazın donanım özellikleri aşağıdaki gibi;

- Intel Celeron J4125 4 Core
- 2GB onboard DDR4 + boş slot
- 4 adet 3.5" SATA 
- 2 adet M2 NVMe yuvası
- 2x 1Gbit LAN

Markanın bulunduğu segmentteki en güncel modeli olmasına rağmen rakibi QNAP'in modellerine kıyasla oldukça eski bir CPU kullanıyor ve maalesef Multi-Gig LAN bulunmuyor. Ürünün fiyatı ve sektörün eğilimi göz önünde bulundurulduğunda bu detaylar büyük eksiklik.

### Tasarım

Tasarım Synology çizgisine aşina olanlar için her zamanki gibi klasik ve sade. Kasa kalitesi olarak mümkün olduğunda ucuz plastik kullanılarak maliyet kaygısından nasibini almış. Her ne kadar çöp olarak söz etsem de emsal model Netgear para kasası kadar sağlam bir cihazdı.

{{< figure align=center src="images/ds423plus_front.jpg" caption="Cihazın ön görünümü." >}}

Beklenildiği üzere cihazın ön tarafında disk yuvaları ve durum ışıkları yer alıyor. Diskler için bulunan ışıklar maalesef M2 diskler için mevcut değil. Yuvalar son derece kalitesiz, sakar ellerde kolaylıkla kırılabilir. Slotların yetkisiz olarak çıkarılmasını engellemek için kilit mekanizması ise es geçilmemiş.

{{< figure align=center src="images/ds423plus_back.jpg" caption="Cihazın arka görünümü." >}}

Arka tarafta iki adet 92 mm fan bulunuyor. Fanların soğutma politikası değiştirilebiliyor ve çok sessiz çalışıyor. Daha önce kullandığım Zyxel NAS326 modelinde fan hızı sabit ve çok gürültülüydü. Bunun dışında iki adet GBit LAN, USB 3.2 ve güç portu yer alıyor.

{{< figure align=center src="images/package_contents.jpg" caption="Aksesuar içeriği." >}}

Aksesuar kutusunda 90W güç adaptörü, iki adet RJ-45 kablo, disk yuvalarını kitlemek için anahtar ve diskleri sabitlemek için çok sayıda vida bulunuyor. Diskler slotlara vidayla sabitlenebildiği gibi vidasız da montajlanabiliyor. Bunun dışında kutu içeriğinde herhangi özel hissettiricek hediye, jest veya sticker gibi ince detaylar yok.

### Yükseltme seçenekleri

Kutu üzerine 3.5" disk takılırken dikkat edilmesi gereken çok fazla nokta bulunmuyor. Tabii ki aynı RAID grup içerisindeki disklerin aynı marka, model ve hatta aynı firmware'ye sahip olması gerektiğinden bahsetmiyorum. Mevcut SATA yuvalarında herhangi bir vendor lock bulunmuyor ancak söz konusu M2 yuvaları olduğu zaman işler değişiyor. Bu yuvalar sadece Synology marka SNV3410 model NVMe disklere destek veriyor. Farklı model disk takıldığı zaman cihaz volume group oluşturmayı reddediyor. Markanın politikasını Apple'a benzetmekte son derece haklı olduğumu düşünüyorum. Problem [buradaki](https://github.com/007revad/Synology_HDD_db) github projesiyle workaround olarak çözülebilse de durum oldukça can sıkıcı.

{{< figure align=center src="images/m2_support.png" caption="Desteklenmeyen diskler kullanıldığında gösterilen uyarı." >}}

Bir diğer dikkat edilmesi gereken nokta RAM yükseltmesi. Cihaz official olarak 2 + 4 şeklinde belleğe destek verse de yabancı forumlarda 2 + 16 şeklinde çalışabildiği test edilmiş. Ben de aynı şekilde 16 GB tek modül takarak 18 GB çalıştığını teyit ettim. RAM tercihinde önemli nokta belleğin **Dual Rank** dizilime sahip olması. Single Rank bellekler bu cihaz ile problem yaşıyor.

## DiskStation Manager

Synology'nin kalbi; gücünü Linux'dan alan bu stabil ve kullanımı kolay işletim sistemi, kullanıcı deneyimini Web arayüzünden sağlıyor. Fazlaca özelleştirilmiş bir Debian fork'u olan bu sistem, tamamen son kullanıcı odaklı basit şekilde tasarlanmış. Temel storage işlevlerini yerine getirmenin yanında çok sayıda kullanışlı uygulamaya sahip bu işletim sistemi, cihazı basit bir NAS'tan daha ötesine taşıyor. İşi network ürünleri olan markalara ait üçüncü sınıf cihazların problemli yazılımlarının aksine Synology'nin işletim sistemi sürekli güncelleme alıyor ve özelliklerine özellik katıyor.

### Uygulama çeşitliliği

DSM, Synology Package Center üzerinden indirilebilen birçok uygulama ve paket sunuyor. Bu paketler arasında web sunucuları, veritabanı yönetim sistemleri, e-posta sunucuları, ve çok daha fazlası bulunuyor. 

{{< figure align=center src="images/package_center.png" caption="DSM uygulama marketi." >}}

Benim en sık kullandığım uygulamalar arasında ise aşağıdakiler bulunuyor;

- **Synology Photos:** Bu uygulama bile tek başına Synology almak için sebep olur. İçerisine atılan fotoğraf ve videoları profesyonel şekilde düzenleyen uygulama aynı zamanda her biri üzerinde yüz ve ortam taraması yaparak sınıflandırıyor. Mobil uygulaması sayesinde fotoğraf ve videolara her yerden erişebilirken aynı zamanda mobil cihaz üzerindeki içerikler çok kolay şekilde yedeklenebiliyor.
- **Download Station:** Çok sık kullandığım bir diğer uygulama, özellikle P2P ağlarından yapılan indirmeler için bilgisayarı sürekli açık bırakma zorunluluğunu ortadan kaldırıyor.
- **Synology Drive:** Herhangi bir servis sağlayıcının "Drive" ürünü ile mukayese edilebilecek bu uygulamada veriler sizin elinizde bulunduğu için güvenlik ve gizlilik endişelerinden de kurtuluyorsunuz.
- **Synology Office:** Cihaz üzerindeki dokümanları bilgisayara indirmeden kolaylıkla görüntülemek ve düzenlemek için severek kullanıyorum. Ciddi anlamda vakit kazandırıyor.
- **Container Manager:** Arayüz giydirilmiş bir Docker uygulaması. Hiç Docker bilmeyenler için bile çok kolay hale getirilmiş bu arayüz sizi tatmin etmezse komut satırından Docker komutlarını çalıştırabiliyorsunuz.

### QuickConnect

Cihazı rakiplerinden ayıran ve öne çıkan bir diğer özellik ise QuickConnect hizmeti. Herhangi DDNS gibi özel konfigürasyona ihtiyaç kalmadan, statik IP gerekmeksizin ve CGNAT arkasından dahi cihaza uzaktan ulaşmanızı sağlayan mucizevi Synology işlevi. Herkesin aklına gelen soru işaretini bu hizmet ile sonlandıran cihaz için kişisel bulut olarak anılmasının önünde hiçbir engel kalmıyor. Doğrudan client ile server arasında kurulan tünel sayesinde hem Synology bu hizmet için ek bir maliyete maruz kalmazken hem de trafik üçüncü taraflar üzerinden geçmiyor.

{{< figure align=center src="images/quickconnect.jpeg" caption="QuickConnect çalışma şekli. (Alıntı)" >}}

### ISCSI kurulumu

Çok basit, çok stabil. Saniyeler içerisinde LUN oluşturulabiliyor ve kolaylıkla Target'lara tanımlanabiliyor. Tek tuşla LUN'lar üzerinde snapshot'lar alınabiliyor. Sıfır karmaşıklık. Peki stabilite kime göre? Bana göre. Birçok NAS cihazı ISCSI destekliyor, doğru. Zyxel NAS326 kullanırken defalarca ince ayarlar yapmama rağmen inanılmaz I/O hataları oluşturuyordu. Netgear ise bir süre ISCSI kullandıktan sonra kitlenip kalıyordu. Kısacası bu özellik çoğunda var evet ancak ne kadar test edilmiş veya sadece bizde de var demek için mi konuluyor?

{{< figure align=center src="images/syno_iscsi.png" caption="ISCSI menüsü." >}}

Performans olarak beklentiyi yüksek tutmamak lazım zira bu profesyonel bir cihaz değil. Sıralı okumada vaat edilen hızları verebiliyor. Parçalı dosyalarda doğal olarak performans aşağıya iniyor. 

{{< figure align=center src="images/iscsi_benchmark.png" caption="2x PM981 RAID0 2Gbit LACP ISCSI performansı." >}}

### Sanallaştırma yetenekleri

Tek kutu altında her şeyi halletme gayretinin bir ürünü olarak bu cihaz aynı zamanda bir sanallaştırma sunucusuna dönüşüyor. Entry level emsallerinden ziyade ARM işlemci yerine X86 işlemci barındırmasından dolayı neden olmasın diyerek sanallaştırma ve konteyner yürütme özellikleri bu kutuya eklenmiş. Tabii ki bir VMware kadar yetenekli değil ancak amatör eğlendiriyor ve acil ihtiyaçlarda hayat kurtarıyor. Yaptığım denemelerde sağlıklı şekilde sanal makine ayağa kaldırmayı başaramadım. Çoğunlukla boot aşamasını geçemedim veya performans düşük kaldı. Kendi Proxmox ortamım olduğu için çok üzerine düşmedim. İlginç bir özellik olarak cihazın içerisinde sanal DSM ayağa kaldırılabiliyor. Tercih edilir mi bilmiyorum ancak sandbox amaçlı kullanmak için bir çözüm düşünülmüş olmalı.

{{< figure align=center src="images/virt_manager.png" caption="Sanallaştırma yöneticisi." >}}

Konteyner varken kendi düşüncem sanallaştırmaya ihtiyaç dahi duyulmayacağı yönünde zira cihaz çok iyi çalışan bir Docker host'u olarak kullanılabiliyor. Normalde komut satırından çalıştırılan Docker, neredeyse Portainer kadar iyi bir arayüz ile servis ediliyor. Dahası, aynı zamanda Compose da destekleniyor. Konteyner ayağa kaldırmak, imaj çekmek, registry eklemek sadece birkaç tık kadar. Ben arayüz kullanmak istemiyorum diyorsanız eğer komut satırından da Docker komutları çalıştırılabiliyor. Daha ne olsun? 

{{< figure align=center src="images/cont_manager.png" caption="Konteyner yöneticisi." >}}

## Performans

Her seferinde dile getirdiğim üzere performans bu cihazın en zayıf noktası. Sahip olduğu J4125 işlemciye kıyasla bu cihazın QNAP tarafındaki dengi TS-464 modelinde çok daha güncel N5095 işlemcisi kullanılıyor. Üzerine yetmezmiş gibi QNAP manidar bir mesajla **"Bye-bye Gigabit NAS"** diyerek sahip olduğu 2.5GBit portlarına vurgu yapıyor. Görece daha kaslı işlemci genel deneyimi etkilerken aynı zamanda çok daha hızlı indeksleme yapabilir ve aynı anda çok daha fazla kullanıcıyı handle edebilir. Synology her zamanki tutumunu koruyarak özellikleri azar azar sunarak Apple vari politika izliyor.

### Dosya aktarımı

Söz konusu SATA hard diskler olduğu zaman alınabilecek maksimum performans bellidir. Sahip olduğu iki adet 1GBit portları bonding yaparak sıradan disklerin üst limitine kolaylıkla ulaşılabiliyor. Söz konusu NVMe portlar olduğunda ise maalesef performans network limitlerine takılıyor. Windows üzerinde SMB ile dosya aktarımı sırasında hard diskerin tam potansiyeli kullanılabilirken gönül 2.5GBit portları bonding yaparak 500 MB/s seviyelerine çıkmayı arzuluyor.

{{< figure align=center src="images/smb_transfer.png" caption="3x 4TB RAID5 2Gbit LACP SMB performansı." >}}

## Güvenlik

Verilerin büyüklüğü ve niteliği arttıkça önem kazanan konulardan birisi kuşkusuz güvenlik oluyor. Hem fiziksel hatalara hem de yetkisiz erişimlere karşı alınan önlemler verinin güvenliğini ve güvenilirliğini bir adım ileri taşıyor.

### Volume Encryption

Cihaz veya cihaza ait diskler çalındığında ya da kriminal incelemeye alındığında içerisinde şebek fotoğraflarınızın da olduğu mahrem verilerinizin üçüncü kişiler tarafından erişilmesini önlemek için volume encryption kullanabilirsiniz. Bu yeteneğe daha yeni kavuşan DSM işletim sistemi, tahmin edildiği üzere arka planda LUKS kullanıyor. Performansı bir miktar negatif etkileyen bu özellik benim olmazsa olmazım.

### RAID ve SHR

Dünya malı, insan üretimi hard disklerde en sık karşılaşılan problemlerden biri de tabii ki arıza yapmaları oluyor. Bu sebepten çok veri kaybetmiş biri olarak NAS kutularını tercih etmemin altında özellikle RAID işlemlerinin çok basit yapılabiliyor olması yatıyor. Her NAS cihazında olduğu gibi 4 disk yuvalı DS423+ de RAID0-1-5-6 ve ayrıca SHR'yi destekliyor. RAID seviyeleri disk ekledikçe yükseltilebiliyor. Herhangi bir diskte meydana gelecek arıza durumunda yapıyı hızlıca ayağa kaldırmak için hot spare disk tanımlanabiliyor. SHR yani Synology Hybrid Raid ise mantık olarak RAID5'e çok benzese de ondan ayrıldığı nokta farklı kapasitelerde diskleri bir arada kullanarak verimi arttırmayı hedefliyor. Benim çok tercih ettiğim veya önerdiğim bir teknoloji değil.

### 2Factor authentication

Yukarda bahsettiğim üzere QuickConnect ile dış dünyaya açtığımız kişisel bulut cihazımız aynı zamanda saldırılara karşı hedef haline geliyor. Kullanıcıların güvenliğini iyileştirmek için 2Factor kesinlikle kullanılması gereken bir güvenlik yöntemi. Kullanıcı ayarlarından oluşturulan token herhangi MFA uygulamasına import edilebiliyor.

### Snapshot Replication

Bir BTRFS harikası. Filesystem seviyesinde alınabilen snapshot'lar sayesinde veriler özellikle ransomware saldırılarına karşı korunmuş oluyor. İşlemler filesystem seviyesinde yapıldığı için QNAP cihazlarda olduğu gibi herhangi kapasite planlamasına ve volume allocation yapılmasına ihtiyaç duymuyor. Snapshot'lar planlanarak düzenli olarak alınabiliyor ve geriye dönük silinmesi gerçekleştirilebiliyor.

### BTRFS, EXT4, ZFS?

Bu başlığa özellikle QNAP çok agresif strateji izlediği ve karalama kampanyası yaptığı için yer vermek istedim. Bilindiği üzere QNAP cihazlar EXT4, Synology ise BTRFS dosya sistemini kullanıyor. QNAP ısrarla EXT4 dosya sisteminin BTRFS'den çok daha performanslı ve stabil olduğunu iddia ediyor. Red Hat'in de BTRFS dosya sistemine verdiği desteği kestiğini öne sürerek EXT4 kullanımını öneriyor. Kendileri de BTRFS karşısında EXT4'ün modern ihtiyaçlarını karşılayamadığının farkında olmalılar ki üst seviye cihazlarda artık ZFS dosya sistemini kullanmaya başladı. Ancak unutulmamalıdır ki kaynak canavarı ZFS için her 1 TB, 1 GB ram ihtiyacı anlamına geliyor.

## Sonuç

Marka bağımsız NAS kutuları teknoloji meraklısı herkesin kuşkusuz ihtiyacı olduğu cihazlar. Synology DS423+ kullanıcı dostu arayüzü ve güçlü özellikleri ile veri depolama ve yönetim ihtiyaçlarını üst düzeyde karşılayan bir NAS çözümü sunuyor. DiskStation Manager işletim sistemi sayesinde, veri güvenliği, yedekleme çözümleri, medya yönetimi ve bulut entegrasyonu gibi birçok kritik fonksiyona kolayca erişim sağlanabiliyor. Özellikle gelişmiş güvenlik önlemleri kullanıcıların verilerini her zaman güvende tutarken, hızlı ve güvenilir performansı ile de günlük iş akışlarını kesintisiz sürdürebilmelerine imkan veriyor. Synology DS423+ hem bireysel kullanıcılar hem de küçük işletmeler için ideal bir NAS çözümü olarak öne çıkıyor.

{{< figure align=center src="images/ds423plus_result.jpg" caption="DS423+ diğer ekip arkadaşları ile birlikte uyum içinde çalışıyor." >}}