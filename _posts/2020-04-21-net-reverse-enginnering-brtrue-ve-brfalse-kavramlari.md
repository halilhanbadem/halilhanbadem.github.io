---
layout: post
title: .NET Reverse Enginnering - brtrue ve brfalse Kavramları
author: Halil Han Badem
---

### Giriş 👨‍💻 
Herkese selamlar,
bu konumuz kısa ve öz anlatarak reverse alanında terimsel ifadelere biraz daha hakim olmak adına yazmak istedim. 
.NET reverse olayında aslında kolayca reflactor araçları ile kodları elde edebiliyoruz. 
Bu durumda çoğu kullanıcı obfuscator yoluna giderek kodlarının güvenliğini sağlamaya çalışıyorlar.
Her önlemin bir çözümü vardır ki; bu durumda ise yine araçlar ile bu şifrelenmiş kodlar çözümlenebiliyor. 
Örnek vermek gerekirsek en basitinden de4dot aracı ile ilgili obfuscate edilmiş projeyi deobfuscator edebiliriz. 
  
### Gelişme 👾 
Bunun yanı sıra deobfuscator ettikten sonra bazen uygulamalar çalışmayabilir. Bu durumda; dump alarak projenin yalın şekilde deobfuscator edip; ilgili hatalı yerleri fixleyen bazı patch tooları mevcut onlarla bu işlemler yapılabiliyor.
Bu araçlar konusunda neden ayrıntıya girmiyorum çünkü konumuz araçlardan çok terimler. Önlemler, stratejiler ve kod güvenliği sağlanabilir. Sonucunda o güvenlik kırılır ve yine o uygulamanın crackli sürümü çıkartılır. Bundan kaçış yoktur. Makalenin bütünlüğünü bozmamak için bir durumdan bahsedeceğim en sonda. Şimdi terimlerimize gelelim. .NET reverse durumunda **brtrue** ve **brfalse** terimleri aslında aşağıdaki C# koduna tercüme edilebilir;

**brtrue**
```
bool a = false;
if (!a)
{
  MessageBox.Show("yazilimtoplulugu lisans kontrol yap");
  a = true;
} 
```
Yani eğer değer 0 değil ise komutu yürütün. 0'ın karşılığı **false**, 1'in karşılığı ise **true** bildiğimiz üzere.
Değerimiz 1 olduğu için aşağıdaki komutu yürütecektir.

![](https://i.imgyukle.com/2020/03/22/JLniyf.png)

Bakın burada bir örnek mevcut. Burada brtrue kullanılmış. Peki burada işlenen kod .net reflactorlerinden herhangi bir araç üzerinden nasıl görünüyor ona bakalım;

![](https://i.imgyukle.com/2020/03/22/JLnq6e.png)

Yani 0 değilse yürüt anlamına taşıdığını buradan anlıyoruz. 
Biz aslında her zaman ! işaretinin tersine al anlamına geldiğini biliyoruz fakat burada var olan değerin tersi yani; 0 ise 1; 1 ise 0 yürüt anlamına geldiğini bilmemiz gerek.

Yazdığımız kodda ilk kontrol **false** olduğu için kontrolü yapmak zorunda. Çünkü atama olarak 0 değerini atadık ve sorguda 0 değilse kodu işle dedik. Sonrasında ise a değişkenini 1 yaparak başka yerlerde **true** olarak gözükmesini sağladık.

**brfalse** ise oluşan durumun tam tersi, yani koşul yanlış(0) ise devam et.
Byte değerleri olarak aslında HEX üzerinden değişiklik yapılıp derlenmeden inject işlemi yapılabilir. 
Test ettiğimde sorun çıkmadı ama yine de sizde kendi uygulamalarınız üzerinden deneyerek test edebilirsiniz.

2D ikilik byte değerinde brtrue
2C ikilik byte değerinde brfalse 
komutuna denk geliyordu. Sizin uygulamanızda farklı olabilir. Ben bu makalede kullandığım test uygulaması için yazdım.

### Sonuç ⏱️
Özet olarak **brtrue** ve **brfalse** kavramlarını sanırsam anlamış olduk. Gelelim makale içinde bahsedeceğim duruma. Günümüzde cracklenme eskiye nazaran daha kolay. Çok fazla araç ve yöntem var. Geliştirilmeye devam edilen bu yöntemler ile uygulama kırmak daha da basit bir hal alıyor. Eğer bir masaüstü yazılımınız mevcut ise bazı özellikleri web servisten dönecek değere göre alın. Örneğin premium olarak tanımlamış olduğunuz "a,b,c" adında 3 adet özellik mevcut. Bunu uzak sunucuda bulunan uygulamaya yaptırın. Değerleri post edin, sunucu ilgili işlemleri yaptıktan sonra size cevap olarak geri döndersin. Lisanssız bir kişinin o web servise istek gönderme durumu (güvenliği yeterince aldıysanız, bu konuda yine bir makale yazacağım JWT ve web servis güvenliği konusunda) olması güçtür. Olsa bile loglar üzerinden izinsiz girişleri kolaylıkla tespit edebilirsiniz. 

Eski uygulamalar eğer veritabanına bağlı ise bu durum güçleşebilir. Birçok kaydı post etmek pek mantıklı olmayabilir. En azından yeni yazılacak olan uygulamaları bulut tabanlı yazmanız en sağlıklısı olacaktır. 

Bir başka makalede görüşmek üzere,
sorularınız ve yanlış gördüğünüz bir yer varsa lütfen yorum atarak belirtiniz. 
Eleştiri en sevdiğim şeydir 🤟
Bu makalenin **izinsiz** başka yerde paylaşılması kesinlikle yasaktır.
İyi çalışmalar dilerim!
