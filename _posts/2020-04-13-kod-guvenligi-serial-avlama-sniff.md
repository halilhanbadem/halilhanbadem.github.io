---
layout: post
title: Kod Güvenliği & Serial Avlama (Sniff)
author: Halil Han Badem
---

Selamlar,
kısa bir yazı olacak. Bundan sonra yazılarımı bloğumda paylaşacağım.
Kısaca tanıtımını yapayım; yazılım, reverse ve siber güvenlik alanlarında makalelerimi yayınlacağım bir bloğum olacak.
Blog adresim: [Tıklayınız](https://halilhanbadem.com)

**Şimdi yeni başlayanlara ufak bir ipucu vereyim bu konuda.**
Bazen kullanıcının bilgisayarına göre serial oluşturuyoruz lokal tarafta.
Bu tarz uygulamalar kolayca komut seti ile kırılabilir ama kırılmadan da ilgili seriali avlamak mümkündür.
Dikkat etmeniz gereken nokta şudur ki; siz eğer seriali bir değişkene atarsanız bunu avlamak çok basit olacaktır.
Anlatıma geçmeden zaten lokalde lisans oluşturmayınız.

### Proje Tarafı 👾 

Atacağım örnek aşırı basit. Bu yüzden bu koda değilde asıl amaca odaklanalım. Geliştirici bir ton işlem yaptı; oradan buradan verileri çekti ve sonuca ulaştı. Sonucu bir değişkene atayıp kontrolünü sağladığını düşünelim.


```
var
 serial: string;
begin
  serial := THashMD5.GetHashString(Edit2.Text);
  if Edit1.Text = serial then
  begin
    ShowMessage('Lisans basarili!');
  end else
  begin
    ShowMessage('Lisans basarisiz!');
  end;
```


Ben burada MD5 ile şifreleyip temsili bir işlem yaptırdım. Dediğim gibi senaryoya odaklanalım.
Sonrasında bunu deploy edip yayına aldık. Olly ile bunu kontrol edip ilgili kısma breakpoint koyarak takibini sağlıyoruz.

![](https://i.imgyukle.com/2020/04/10/QQdJhR.png)

Random olarak oluşturduğu sayıyı MD5 ile hash edip lisans niyetine kullanıcıdan istiyoruz bu aşamada.
Hata mesaji ile ilgili adrese gidip orada dediğim gibi breakpointi yerleştiriyorum.

![](https://i.imgyukle.com/2020/04/10/QQdSqq.png)


Sonrasında F8 ile yavaş yavaş kodlardan dönen stack değerlerini kontrol ediyorum.

![](https://i.imgyukle.com/2020/04/10/QQdc78.png)

Burada ilgili seriali buluyorum ama daha açıklayıcı olması açısından devam ediyorum.

![](https://i.imgyukle.com/2020/04/10/QQdimo.png)

Burada ise gördüğünüz gibi atamalar yapılarak JNZ komutu ile koşula bağlanıyor. JNZ koşulunu JE yaparak crackli versiyon yapılabilir ama bir müdahalede bulunmadan bunun kontrolünü sağlamış oluyoruz.

**Gelen keyi deneyelim:**

![](https://i.imgyukle.com/2020/04/10/QQdzNf.png)

işlem başarılı. Bu şekilde keyi almış oluyoruz. Tabi bu bizim bilgisayarımıza ait olan bir kod. 
Keygen vs. yapmak isterseniz yapmayın; kod güvenliğini anlatıyorum illegal işlere bulaşmayalım  Blush

İyi çalışmalar dilerim.