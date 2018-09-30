
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

