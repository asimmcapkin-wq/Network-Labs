## 1.EVE-NG Altyapı ve İstemci Hazırlığı
EVE-NG web arayüzünde GUI kaynaklı yaşanan "undefined" ve senkronizasyon hataları analiz edildi.

Performansı ve stabiliteyi artırmak amacıyla Kubuntu Linux üzerine komut satırı üzerinden Microsoft Edge kurulumu yapıldı ve laboratuvar erişimi bu tarayıcıya taşındı.

## 2. WAN Omurgası (L2/L3) Fiziksel ve Mantıksal Kurulumu
IspR (Cisco), SinopSube ve CorumSube (FortiGate) cihazlarının WAN bacakları, merkezi bir L2 Switch (IspSW) üzerinden aynı broadcast domain (50.0.1.0/24) içerisine alındı.

İlgili cihazların dış bacak IP atamaları doğrudan CLI üzerinden yapılandırıldı ve uçtan uca ICMP erişimleri doğrulandı.

## 3. Çoklu Üretici (Multi-Vendor) OSPF Entegrasyonu ve MTU Sorun Giderimi
Cisco ve Fortinet cihazları arasında Area 0 üzerinden OSPF yönlendirme protokolü devreye alındı.

Üreticiler arası yapılandırma farkından doğan ve OSPF komşuluğunun EXSTART ve 2WAY durumlarında takılmasına sebep olan paket boyutu uyuşmazlığı tespit edildi.

Hem Cisco hem de FortiGate arayüzlerinde mtu-ignore politikası uygulanıp OSPF process'leri yeniden başlatılarak komşuluk durumları FULL (tam senkronizasyon) seviyesine getirildi.

## 4. Link Aggregation (LACP), VLAN Segmentasyonu ve Zone Kurgusu
CoreSW ile CoreFW (Merkez Güvenlik Duvarı) arasındaki port3 ve port7 fiziksel arayüzleri, bant genişliğini artırmak ve yedeklilik (redundancy) sağlamak amacıyla 802.3ad Aggregate (LACP) protokolüyle TrunkToDavaCeza adında tek bir mantıksal arayüzde birleştirildi.

Ağ trafiğini izole etmek için bu Trunk hattı üzerinden sub-interface mantığıyla VLAN'lar oluşturuldu (CezaVlan60, GuestVlan70, IdareVlan50, LobiVlan20, YonetimVlan40).

Güvenlik politikalarının (Policy) yönetimini kolaylaştırmak amacıyla arayüzler mantıksal bölgelere (Zone) ayrıldı. İç ağlar TrustedZone altında toplanırken, misafir ağı GuestZone içerisine alınarak kurum içi kaynaklardan tamamen izole edildi.

## 5. Active Directory (LDAP) Entegrasyonu ve Kimlik Tabanlı Erişim Kontrolü
Kullanıcı yetkilendirmesi için FortiGate üzerinde yerel Active Directory (LocalAD) entegrasyonu sağlandı.

LDAP sunucusu üzerinden çekilen FgtMudurler ve FgtPersoneller grupları güvenlik duvarına entegre edildi.

İnternet çıkış kuralları (TrustedZoneToWan), IP bazlı olmak yerine doğrudan bu AD gruplarına ve Guest-group kimliklerine dayandırılarak Kimlik Tabanlı Erişim Kontrolü (Identity-Based Access Control) mimarisi kurgulandı.

## 6. DMZ Kurgusu ve Virtual IP (NAT/Port Forwarding) Yapılandırması
DMZ bölgesindeki (DmzZone) sunuculara dış dünyadan erişim sağlamak için Virtual IP (VIP) nesneleri oluşturuldu.

WAN IP'sine (10.0.0.1) TCP 80 portundan gelen istekler WebSerber'a (192.168.80.10), TCP 8080 portundan gelen istekler ise CompServer'a (192.168.90.10) NAT'landı.

Gerekli Firewall Policy (DMZ_Sunucu_Erisimi, LAN_to_WebServer) kuralları yazılarak hem dışarıdan (WAN) DMZ'e hem de iç ağlardan (LAN/Guest) DMZ'e hedefe yönelik, güvenli erişim yetkileri tanımlandı.

## 7. Şube L2/L3 İletişim ve Güvenlik Duvarı İzinleri (Maslak Şube)
Maslak Şube uç nokta Linux makinesinde Gateway'e erişimde alınan Destination Host Unreachable hatası (ARP/L2 çözümleme başarısızlığı) giderildi.

MaslakSube (FortiGate) cihazının port2 arayüzüne IP tanımlaması yapıldı, allowaccess ping özelliği aktif edildi.

Uç cihaz ile Firewall arasına konumlandırılan Cisco switchlerin (SubeSW, L2Sw4) varsayılan olarak kapalı gelen arayüzleri aktif (no shutdown) edilerek fiziksel katman iletişimi sağlandı.
