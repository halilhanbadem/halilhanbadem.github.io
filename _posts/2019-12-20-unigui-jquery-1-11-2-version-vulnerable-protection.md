---
layout: post
title: UniGUI JQuery 1.11.2 Version Vulnerable - Protection
author: Halil Han Badem
---

Merhaba,
UniGUI'de dikkatimi çeken bir olay. JQuery versiyonu olarak 1.11.2 kullanmakta. 1.11.1, 1.11.2 versiyonlarında orta seviyeli bir açık mevcuttu. Bu açık yine XSS tabanlı bir açık yaratıyordu. dateype seçeneğini/değişkeni saldırgan tarafından doldurularak işleme alınabiliyor ve bu duruma savunmasız kalıyor. Tabi şu an için bu açığı kullanmak pek mümkün değil. Tahminen SOP'tan dolayı ama tam emin değilim.

Açık hakkında detaylı bilgi -> https://snyk.io/vuln/npm:jquery:20150627
CVE bilgisi -> https://www.cvedetails.com/cve/CVE-2015-9251/

Önlem: JQuery'nin yüksek sürümünü indirin.

Adımlar:

1. https://code.jquery.com/jquery-3.4.1.min.js adresinden dosyayı indiriniz.
2. C:\Program Files (x86)\FMSoft\Framework\uniGUI\uni-1.90.0.xxxx\jQuery  adresine gidip, indirdiğiniz dosyayı kopyalayın.
3. Var olan  jquery-1.11.2.min.js dosyasını silin.
4. İndirip kopyaladığınız dosyanın adını; jquery-1.11.2.min.js olarak değiştirin. 

İşlem bu kadar. 
İyi çalışmalar dilerim.
