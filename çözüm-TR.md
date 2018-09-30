
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

## Penetration

Bulduğumuz username ve password ile login sayfasından giriş yapmayı deneyelim.

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/7.PNG" width="800" title="hover text">  
</p>

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/8.PNG" width="800" title="hover text">  
</p>

Bir indirme linki çıktı ve bir backup dosyası bilgisayarımıza indi.
Tavsiyem, vulnhub tarzı bir sitede makine çözmeye çalışırken indirdiğiniz dosyaların içerisine ilk önce xxd ile bakmanızdır.
Bunun amacı ise içerisinde saklanmış olan başka bir dosya olup olmadığını kontrol etmek, veya indirdiğimiz dosyanın encrypt edilip edilmediğini anlamak.

```
xxd myplace.backup
```

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/9.PNG" width="800" title="hover text">  
</p>

xxd ile dosyaları incelemeye alışık olan biri bunun normal bir dosya görüntüsü olmadıgını anlayabilir. "=" ile bitiyor olması bana base64 ile şifrelenmiş olabileceği mesajını veriyor.

base64 ile decode edip zip dosyasına çevirelim.

```
base64 -d myplace.backup > myplace.zip
```
<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/10.PNG" width="800" title="hover text">  
</p>

Zip dosyasının içine bakalım:

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/11.PNG" width="800" title="hover text">  
</p>

Görünüşe göre parola ile korunan bazı dosyalar var.

```
fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt myplace.zip
```
not: ilgili dizindeki rockyou.txt dosyası normalde sıkıştırılmış bir halde orda bulunmaktadır. Onu önceden o klasöre çıkartmıştım. Bu komutu kullanmadan önce rockyou.txt yi unzip ettiğinizden emin olun.

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/12.PNG" width="800" title="hover text">  
</p>

şifre: magicword

app.js dosyasının içe baktıgımızda const url: ... şeklinde bir kod var. Burada kullanıcı adı mark ve ":" ile "@" işareti arasındaki kısım da şifre.

<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/13.PNG" width="800" title="hover text">  
</p>
 Bu username ve password ile ssh bağlantısı yapmayı deneyelim.
 
 ```
 ssh mark@192.168.149.142
 ```
 
<p align="center">
  <img src="https://github.com/tolgayan/Vulnhub_Node-1_cozum_Walkthrough/blob/master/node.js/14.PNG" width="800" title="hover text">  
</p>

makineye giriş yaptık ve user olduk!!

## privelage escalation

artık root yetkileri almamız lazım.
