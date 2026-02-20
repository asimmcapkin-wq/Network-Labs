1. EVE-NG Altyapı ve İstemci Hazırlığı
EVE-NG web arayüzünde GUI kaynaklı yaşanan "undefined" ve senkronizasyon hataları analiz edildi.

Performansı ve stabiliteyi artırmak amacıyla Kubuntu Linux üzerine komut satırı üzerinden Microsoft Edge kurulumu yapıldı ve laboratuvar erişimi bu tarayıcıya taşındı.

2. WAN Omurgası (L2/L3) Fiziksel ve Mantıksal Kurulumu
IspR (Cisco), SinopSube ve CorumSube (FortiGate) cihazlarının WAN bacakları, merkezi bir L2 Switch (IspSW) üzerinden aynı broadcast domain (50.0.1.0/24) içerisine alındı.

İlgili cihazların dış bacak IP atamaları donanım arayüzleri kullanılmadan, doğrudan CLI üzerinden yapılandırıldı ve uçtan uca ICMP (ping) erişimleri doğrulandı.

3. Çoklu Üretici (Multi-Vendor) OSPF Entegrasyonu ve MTU Sorun Giderimi
Cisco ve Fortinet cihazları arasında Area 0 üzerinden OSPF yönlendirme protokolü devreye alındı.

FortiGate cihazlarındaki varsayılan ve çakışmaya yol açan ospf-wan profilleri CLI üzerinden silinerek iç LAN ağları (30.0.0.0/24, 40.0.0.0/24) OSPF sürecine dahil edildi.

Üreticiler arası yapılandırma farkından doğan ve OSPF komşuluğunun EXSTART ve 2WAY durumlarında takılmasına sebep olan paket boyutu uyuşmazlığı tespit edildi.

Hem Cisco hem de FortiGate arayüzlerinde mtu-ignore politikası uygulanıp OSPF process'leri yeniden başlatılarak komşuluk durumları FULL (tam senkronizasyon) seviyesine getirildi.

4. DMZ Kurgusu ve Virtual IP (NAT/Port Forwarding) Yapılandırması
Merkezi güvenlik duvarı (CoreFW) üzerinde, dış dünyadan DMZ ağına erişim için Virtual IP (VIP) nesneleri oluşturuldu.

WAN IP'sine (10.0.0.1) TCP 80 portundan gelen istekler DMZ ağındaki WebSerber'a (192.168.80.10), TCP 8080 portundan gelen istekler ise CompServer'a (192.168.90.10) NAT'landı.

Gerekli Firewall Policy (Güvenlik Politikası) kuralları yazılarak WAN'dan DMZ'e hedefe yönelik erişim yetkisi tanımlandı.

5. Şube L2/L3 İletişim ve Güvenlik Duvarı İzinleri (Maslak Şube)
Maslak Şube uç nokta Linux makinesinde Gateway'e erişimde alınan Destination Host Unreachable hatası (ARP/L2 çözümleme başarısızlığı) giderildi.

MaslakSube (FortiGate) cihazının port2 arayüzüne IP tanımlaması yapıldı, allowaccess ping özelliği aktif edildi ve tüm trafiğe izin veren (Allow-All) test politikası yazıldı.

Uç cihaz ile Firewall arasına konumlandırılan Cisco switchlerin (SubeSW, L2Sw4) varsayılan olarak kapalı gelen arayüzleri aktif (no shutdown) edilerek fiziksel katman iletişimi sağlandı.
