
# Vulnhub Node:1 Çözümü:

Makinenin vulnhub linki: https://www.vulnhub.com/entry/node-1,252/

HackTheBox makinelerinin kolay bir versiyonuna benzeyen bu makine özellikle yeni başlayanlar için öğretici bir içeriğe sahip.


## Bilgi Toplama

Kali Makinemizi açalım ve vulnarable makinenin ip'sini tespit etmek için arp-scan kullanalım.
Ardından da nmap kullanarak bulduğumuz ip de port taraması yapalım.

```
arp-scan --local
```

```
nmap -sS -sV -p- 192.168.149.141
```


<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/1.PNG" width="800" title="hover text">  
</p>

22. portta açık ssh servisi, 3000. portta çalışan da node.js var. Size de ismi tanıdık gelmedi mi?

## Enumeration

Mozilla Firefox'u çalıştırıp http://192.168.149.142:3000 adresine gidelim.

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/2.PNG" width="800" title="hover text">  
</p>

Sağ üstte bir login sayfası görülüyor.
Sitenin dizinlerini keşfetmek amacıyla tekrardan konsola dönelim ve "dirbuster" yazarak dirbuster'ı çalıştıralım.

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/3.PNG" width="800" title="hover text">  
</p>

Buraya koymasam da tarama yapmayı dirb ve gobuster ile de denedim fakat site tarama yapmamıza izin vermiyor. Dirb aracının ayarlarıyla oynayarak da bu sorunu çözebiliriz fakat ben burp suite kullanarak bakmak istiyorum.
Mozilla Firefox ayarlarından proxy ayarlarımızı burp suite için ayarlayalım ve burp suite'i çalıştıralım.
Sayfamız target sekmesine düştüğü zaman sayfa linkine sağ taıklayıp "spider this host" diyerek dizinleri görüntüleyelim.
<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/4.PNG" width="800" title="hover text">  
</p>

Görünüşe göre /api/users/ adında bir sayfa var !! Bir bakmakta fayda var.

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/5.PNG" width="800" title="hover text">  
</p>

en üstteki admin gibi duruyor. username elimizde fakat parola hash ile encrypt edilmiş. İnternetten bulduğumuz araçlarla hash'i çözmeyi deneyelim.

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/6.PNG" width="800" title="hover text">  
</p>

Şifre:manchester

Eğer hash çözülemeseydi brute force ile kırmayı deneyecektim.

Bulduğumuz username ve password ile login sayfasından giriş yapmayı deneyelim.

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/7.PNG" width="800" title="hover text">  
</p>

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/8.PNG" width="800" title="hover text">  
</p>

