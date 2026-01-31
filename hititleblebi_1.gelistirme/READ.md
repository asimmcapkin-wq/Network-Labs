# 🏺 Hattusa Gıda - Faz 1: Kenar Ağ Yapılandırması

Bu bölümde, ağın sol tarafındaki uç cihazları yönetmek için kurulan **Vlan10_20_30** switch'inin teknik detayları yer almaktadır.

---

## 1. VLAN Tanımlamaları
Ağdaki departmanlar, `sh vlan` çıktısına uygun olarak aşağıdaki gibi izole edilmiştir:

* **VLAN 10**: YONETIM
* **VLAN 20**: MISAFIR
 ```bash 
vlan 10
 name YONETIM
vlan 20
 name MISAFIR
vlan 30
 name CIHAZLAR
``` 

## 2. Port Atamaları (Access Ports)

Cihazların ilgili ağlara dahil edilmesi için yapılan port yapılandırmaları:

  - Yönetim Bloğu (VLAN 10): Patron PC, Kamera ve POS cihazlarını kapsar.

  - Misafir Bloğu (VLAN 20): Harici kullanıcı girişi için ayrılmıştır.

```bash
! Patron, Kamera ve POS (VLAN 10)
interface range Ethernet0/1 - 3
 switchport mode access
 switchport access vlan 10
 description YONETIM_CIHAZLARI

! Misafir PC (VLAN 20)
interface Ethernet1/0
 switchport mode access
 switchport access vlan 20
 description MISAFIR_ERISIMI

``` 

## 3.Trunk Port Yapılandırması (Kritik Hat) 🚀

Bu switch'in ana omurgaya bağlanmasını sağlayan en önemli parçadır. Tek kablo üzerinden tüm VLAN trafiğini etiketli (tagged) olarak taşır.

```bash
interface Ethernet0/0
 description TRUNK_TO_CORUM_SW
 switchport trunk encapsulation dot1q
 switchport mode trunk
``` 
### 🖥️ VLAN Doğrulama Testi
Aşağıdaki görselde, VLAN yapılandırmasının başarılı bir şekilde çalıştığı ve switch üzerindeki port atamaları görülmektedir:

![VLAN Kanıtı](assets/vlankanıt.png)


# Faz 1.2: Sunucu ve IoT Bölgesi Yapılandırması

Ağın sağ tarafında, kurumsal web sunucusu ve IoT sensörlerinin yönetimini sağlayan **Vlan40_50** switch'inin yapılandırması tamamlanmıştır.
* **VLAN 40**: SERVER_WEB (Kurumsal Sunucu)
* **VLAN 50**: SENSOR_IOT (Üretim Hattı Sensörleri)

```bash
vlan 40
 name SERVER_WEB


vlan 50
 name SENSOR_IOT
```
## 2. Port Atamaları (Access Ports)

Cihazların ilgili sunucu ağlarına dahil edilmesi için yapılan yapılandırmalar:

   Web Sunucusu (VLAN 40): Linux tabanlı kurumsal sunucu bu port üzerinden hizmet verir.

   IoT Sensör Bloğu (VLAN 50): Üretim hattındaki farklı sensör ve kontrol cihazlarını kapsar.


```bash
   ! Kurumsal Web Sunucusu (VLAN 40)
interface Ethernet0/1
 switchport mode access
 switchport access vlan 40
 description WEB_SERVER_PORT

! IoT Sensörleri ve Kontrolörler (VLAN 50)
interface range Ethernet0/2 - 3, Ethernet1/0
 switchport mode access
 switchport access vlan 50
 description IOT_SENSORS_BLOCK
 ```
## 3. Trunk Port Yapılandırması 🚀

Bu switch'in ana omurga (CorumSw) ile haberleşmesini sağlayan Trunk hattı:
```bash

interface Ethernet0/0
 description TRUNK_TO_CORUM_SW
 switchport trunk encapsulation dot1q
 switchport mode trunk
```
---

### 🖥️ VLAN Doğrulama Testi
Aşağıdaki görselde, VLAN yapılandırmasının başarılı bir şekilde çalıştığı ve switch üzerindeki port atamaları görülmektedir:

![VLAN Kanıtı](assets/faz1.2vlanlar.png)
![VLAN Kanıtı](assets/faz1.2vlanr2.png)
