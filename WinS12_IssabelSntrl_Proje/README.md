# Windows Server ve Issabel Entegrasyon Projesi
# 🏢 Kurumsal VoIP ve Active Directory Entegrasyon Projesi (Hibrit Lab)

Bu proje, **Windows Server 2019 (Active Directory)** ve **Issabel 4 (Linux tabanlı IP Santral)** sunucularının **EVE-NG** üzerinde simüle edilen kurumsal bir ağ topolojisinde entegrasyonunu kapsar.

Projenin temel amacı, Linux tabanlı VoIP sunucusunun, merkezi kimlik yönetim sistemi (Active Directory) ile konuşturulması ve **SSSD/Kerberos** protokolleri üzerinden güvenli erişim sağlanmasıdır.

---

## 🏗️ Ağ Topolojisi ve Mimari

Proje, **VeliogluSigorta** kurgusal şirketi için tasarlanmış hibrit bir yapıyı içerir.

* **Domain:** `velioglusigorta.local`
* **VLAN Yapısı:**
    * **VLAN 90 (Servers):** Windows DC, Issabel PBX
    * **VLAN 10,20 ve 50 (Clients):** Yönetim PC'leri, IP Telefonlar
* **Yönlendirme (Routing):** Cisco Router ve L3 Core Switch üzerinden Inter-VLAN routing ve NAT.

![Ağ Topolojisi](image_2b1d03.png)
*(EVE-NG üzerindeki Lab topolojisi: Windows Server, Issabel ve Client makinelerin dağılımı)*

---

## 🎯 Proje Hedefleri ve Başarımlar

1.  **Repo ve Paket Yönetimi Onarımı:**
    * Issabel 4 (CentOS 7) üzerindeki eski repoların `vault.centos.org` adresine yönlendirilerek paket yükleme sorununun çözülmesi.
2.  **Ağ Erişim Sorunlarının Giderilmesi:**
    * NAT ve Routing engellerinin **SSH Tünelleme** ve **Jump Server (Switch üzerinden sıçrama)** teknikleriyle aşılması.
3.  **Active Directory Entegrasyonu (CLI Tabanlı):**
    * Issabel sunucusunun `realm` ve `adcli` araçları ile Windows Domain'e dahil edilmesi.
    * **SSH Erişimi:** Active Directory kullanıcılarının (Örn: `cigerahmet`) kendi Windows şifreleriyle Linux sunucuya SSH yapabilmesi.

---

## 🛠️ Karşılaşılan Teknik Sorunlar ve Çözümler

### 1. "No Route to Host" ve Erişim Engeli
Sunucuya dışarıdan erişim sağlanamadığında, Core Switch bir **Jump Server** olarak kullanıldı ve SSH bağlantısı iç ağ üzerinden gerçekleştirildi.

### 2. Issabel LDAP Modülünün Eksikliği
Issabel 4 güncel sürümünde `issabel-system-auth-ldap` paketi kaldırıldığı için Web Arayüzü entegrasyonu yapılamadı.
* **Çözüm:** Hibrit Güvenlik Politikası uygulandı.
    * ✅ **SSH (Backend):** Active Directory (Kerberos) üzerinden merkezi kimlik doğrulama.
    * 🔒 **Web GUI (Frontend):** Yerel yönetici hesapları ile izole yönetim.

![Paket Hatası](image_2b33e3.png)
*(Modülün repolarda bulunamadığına dair hata ekranı)*

---

## 🚀 Kurulum ve Konfigürasyon Adımları

### 1. Ağ Altyapısı ve İnternet Erişimi (Cisco Router)
Tüm VLAN'ların internete çıkabilmesi için Router üzerinde **NAT (Network Address Translation)** ve **PAT (Port Address Translation)** yapılandırması yapıldı.
* **Inter-VLAN Routing:** Core Switch üzerinde yapılandırıldı.
* **NAT Overload:** İç ağdaki (192.168.x.x) IP bloklarının dış bacak (WAN) üzerinden internete çıkması sağlandı.

```bash
# Router NAT Örneği
ip nat inside source list 1 interface Ethernet0/0 overload
ip route 0.0.0.0 0.0.0.0 172.16.78.2
```
### 2. Windows Server 2019 ve Active Directory Kurulumu
Roller: Active Directory Domain Services (AD DS), DNS ve DHCP rolleri kuruldu.

- Kullanıcı Yönetimi: Şirket departmanlarına göre OU (Organizational Unit) yapısı oluşturuldu.

- Kullanıcılar: Ahmet Ciger (IT Admin) ve Mehmet (Standart Kullanıcı) gibi test kullanıcıları oluşturuldu.

- GPO (Group Policy): Kullanıcıların masaüstü arka planları ve denetim masası erişimleri Group Policy ile sınırlandırıldı.

### 3. Dosya Sunucusu ve Yetkilendirme (File Server)
Kullanıcıların ortak dosyalara ağ üzerinden erişebilmesi için SMB Paylaşımı yapılandırıldı.

Klasör Yapısı: \\VeliogluServer\OrtakAlan ve \\VeliogluServer\IT_Ozel klasörleri oluşturuldu.

NTFS İzinleri:

Standart kullanıcılar (Mehmet vb.) sadece "Okuma" iznine sahipken, IT yöneticileri "Tam Denetim" yetkisine sahip olacak şekilde kısıtlamalar getirildi.

4. Linux ve VoIP Entegrasyonu (Issabel)
Issabel santral sunucusu kurularak şirket içi dahili görüşme altyapısı hazırlandı.

Repo Onarımı: CentOS 7 repoları vault.centos.org adresine yönlendirilerek paket yükleme sorunları çözüldü.

Active Directory Entegrasyonu: Linux sunucu, realm ve sssd servisleri kullanılarak Windows Domain'e dahil edildi.

sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*



sed -i 's|#baseurl=[http://mirror.centos.org](http://mirror.centos.org)|baseurl=[http://vault.centos.org](http://vault.centos.org)|g' /etc/yum.repos.d/CentOS-*
yum clean all && yum makecache
