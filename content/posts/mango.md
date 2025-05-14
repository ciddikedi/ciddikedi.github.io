---
title: 'GL.iNet Mango aracılığıyla her yerde güvenli internet'
date: 2024-05-22T10:38:29+03:00
draft: false
cover:
  image: "posts/mango/images/mango_cover.jpg"
  alt: "GL-MT300N-V2 Mini Smart Router"
  caption: ""
  relative: false
---

## Giriş

Modem veya router aklımıza her zaman yerinden hareket etmeyen, sabit veya antenli cihazları anımsatır. Ev dışında çalışırken kullandığım farklı ağlardan duyduğum şüphe acaba daha küçük bir cihazla kendi güvenilir alanımı yaratabilir miyim düşüncesini aklıma getirdi. İlk başta Raspberry ile bu işi yapmayı planlasam da sonrasında aynı işlevi gören çok daha küçük router'lar olduğunu keşfettim. Uzakdoğu menşei GL.iNet markasının üretmiş olduğu modeller tam da benim arayışıma cevap oldu. Marka, Mango isminde model tasarlayarak avuç içi kadar bir router yapmayı başarmış. 

{{< figure align=center src="images/mango.jpg" caption="Mango'nun boyutu avuç kadar." >}}

## Teknik özellikler

Cihazın boyutuna aldanmak gerek çünkü minimum donanımdan maksimum verim elde edilecek şekilde tasarlanmış. Bu küçük gadget, gücünü micro USB portundan alıyor ve cep telefonu sarjı veya powerbank gibi kaynaklardan çalıştırılabiliyor. Cihazın teknik özellikleri aşağıdaki şekilde;

- CPU: MediaTek MT7628NN 580Mhz
- Memory: DDR2 128MB ve 16MB Flash
- Wi-Fi: 802.11n 300Mbps
- Ethernet: WAN ve LAN 100Mbps
- Giriş-Çıkış: 1x WAN, 1x LAN, 1x USB 2.0

{{< figure align=center src="images/mango_ports.png" caption="Giriş çıkış portları." >}}

{{< figure align=center src="images/mango_board.jpg" caption="Devre kartı da kendi gibi mütevazi." >}}

## Yazılım

Tamamen açık kaynak kodlu OpenWRT işletim sisteminin özelleştirilmiş versiyonunu kullanan cihaz, daha öncesinde router'larını modlayan arkadaşlara yabancı gelmeyecektir. Kabaca cihazın içerisinde bir Linux dağıtımı çalışmakta ve bu cihaz ile yapabilecekleriniz Linux yetenekleriniz ve sahip olduğu donanım sürücüleri ile doğru orantılı. OpenWRT sayesinde cihaz üzerinde çeşitli ağ yönetim araçlarını ve üçüncü parti yazılımları kullanabilirsiniz. Arayüz alışılmış LuCI arayüzünden ziyade daha göze hitap eden özelleştirilmiş bir sayfa sunuyor ancak isteğe bağlı bu arayüzü yetersiz bulanlar için LuCI de kurulabiliyor.

{{< figure align=center src="images/mango_panel.png" caption="Arayüz oldukça basit ve kullanışlı." >}}

{{< figure align=center src="images/mango_luci.png" caption="İsteğe bağlı gelişmiş LuCI arayüzü." >}}

## Yetenekler

Cihaz, internete erişebilmek için çeşitli yöntemleri oldukça kullanışlı şekilde size sunuyor. Bunlar arasında öncelikle tabii ki kablo, repeater, mobile tethering ve hücresel modem bulunuyor. Ancak bu cihazın asıl potansiyeli üzerinde iyi düşünülmüş bir VPN gateway olmasında yatıyor. Mango, OpenVPN ve WireGuard protokollerini destekliyor ve doğru konfigure edildiğinde üzerine bağlanan her cihazı VPN tünelinden çıkarıyor. Her açıldığında VPN'e otomatik bağlanıyor ve Internet Kill Switch özelliği sayesinde VPN sunucusuna erişim olmadığında ağdaki cihazların internet erişimini keserek deşifre olmanın önüne geçiyor. 

{{< figure align=center src="images/openvpn_panel.png" caption="OpenVPN client menünüsü." >}}

Kişisel bilgisayarlar veya mobil cihazlarımızda VPN client kurarak da aynı işi göremez miyiz? Evet, kesinlikle. Fakat bu yetenekli cihaz, VPN profillerine müdahale edemediğimiz iş bilgisayaları için çok iyi bir çözüm haline geliyor. Kullanım ihtiyaçlarınıza göre yapabilecekleriniz tamamen hayalgücünüze kalmış.

## Performans

Çok sayıda unsur göz önünde bulundurulduğunda performans bu cihazın en zayıf noktası. Öncelikle çok zayıf bir donanım barındırıyor. CPU gücü zayıf olmasının yanında 802.11ac/ax gibi standartlara destek vermiyor. Eğer benim gibi WireGuard yerine OpenVPN kullanıyorsanız performans çok daha aşağılara iniyor. Ancak söz konusu deneyim olduğunda bu performans problemini çok fazla hissetmiyorsunuz. Mobile tethering ile internete çıkardığım Mango, VPN içinde VPN yapılmasına rağmen 2K ekran ile remote desktop deneyiminde gözle görülür bir gecikmeye sebep olmadı. Evimde çalışan bağlandığım OpenVPN server 100/20 Mbps internet ile besleniyor. Daha iyi standartlardaki internet şartlarında performans kuşkusuz yukarı çıkacaktır. Ayrıca konudan bağımsız, Mango'ya kablo ile bağladığım Iphone herhangi ek yapılandırmaya ihtiyaç duymadan sorunsuz algılandı ve tethering özelliği kolaylıkla çalıştı.

{{< figure align=center src="images/speedtest.png" caption="OpenVPN arkasında hız testi." >}}

## Sonuç

GL.iNet Mango, küçük boyutları ve geniş özellikleri ile dikkat çeken bir yönlendirici. Seyahat edenler, teknoloji meraklıları ve güvenli internet bağlantısı arayan herkes için ideal bir çözüm haline geliyor. Bu küçük ama güçlü cihaz, internet bağlantınızı her zaman ve her yerde güvenli ve esnek hale getirmenizi sağlıyor.

{{< figure align=center src="images/mango_in_bag.jpg" caption="Mango, çantamdaki yerini aldı." >}}