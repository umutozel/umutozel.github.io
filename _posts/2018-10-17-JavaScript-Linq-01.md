---
layout: post
title: JavaScript ile Linq yapalım!
comments: true
redirect_from: "/2014/08/19/JavaScript-Linq-01/"
permalink: javascript-linq-01
---

Tek bir yazıya sığmayacak tabii ki, kemerleri bağlayın :)

1. Linq gerçekte nedir? **(You are here)**
2. [Expression'lar](/javascript-linq-02)
3. [IQueryable ve IQueryProvider](/javascript-linq-03)
4. [Jokenizer - JavaScript Expression'larını parse edelim](/javascript-linq-04)
5. [Jokenizer.Net - C# Expression'larını parse edelim](/javascript-linq-05)
6. [Jinqu - JavaScript ile Linq](/javascript-linq-06)
7. [Linquest ve Linquest.AspNetCore - Asp.Net Core ile cevap verelim](/javascript-linq-07)

C# ile uğraşan herkesin Linq hakkında az çok fikir sahibi olduğuna eminim. .Net Framework 3.5 ile 2007 yılında kullanımımıza sunulan Linq, benim için C# dilindeki ikinci en güzel özellik (beni tanıyanlar Generic'leri ne kadar sevdiğimi bilir).

Böyle bir yeteneğe JavaScript ile geliştirme yaparken de sahip olsak ne kadar güzel olur değil mi? Sizi duyar gibiyim, bir çok kütüphane kendilerince bir yöntemle Linq metodlarını JavaScript ile kullanımımıza sunmuş durumda ([linq.js](https://archive.codeplex.com/?p=linqjs), [jslinq](https://archive.codeplex.com/?p=jslinq), [jlinq](https://github.com/hugoware/jlinq-beta) ve [ES6 ile gelen foksiyonel programlamadan esinlenilen metodlar](https://ardalis.com/javascript-es6-linq-equivalents)). Ancak hiç birisi Linq'nun gerçek gücünü yansıtmıyor. Ben yine de her "ne gerek vardı?" sorusuna verdiğim referansı yapıştırayım.

![Standards]( https://imgs.xkcd.com/comics/standards.png)

<sup>[xkcd - standards](https://xkcd.com/927/ "xkcd - standards")<sup>

Linq - Language Integrated Query, yani Dile Entegre Sorgu sistemi. İlk duyduğumda bana da bir şey ifade etmemişti. Ancak öğrendikçe ilk hissettiğim diğer bazı dillerde ne kadar karışık işler için ne kadar estetik çözümlere sahip oldukları için içerlemiştim. 

Biliyorsunuz, Linq sorgularını iki tür yazabiliyoruz;

**Query Syntax**
```csharp
var products = from p in Products
               where p.StockOnHand == 0
               select p;
```
**Method Syntax**
```csharp
var products = Products.Where(p => p.StockOnHand == 0);
```

Oldum olası Query Syntax'a alışamamışımdır, çok uzun yıllardır Orm'ler sayesinde kurtulduğum Sql dilini anımsatan işlerden kaçınmaya çalışıyorum sanırım.

Size **Where**, **OrderBy**, .. metodlarının nasıl kullanılacağını anlatmayacağım, bu konuda sayısız makale bulabilirsiniz, bir konuyu iyi öğrenmek için arka planda neler döndüğünü anlamak gerektiğine inanıyorum, o yüzden perdeyi beraber kaldıracağız.

Elimizde bir Product listesi olsun, aşağıdaki gibi;

```csharp
List<Product> products = GetProducts();  // bizim için Product listesi üreten sihirli fonksiyon
```
ve Id değeri 3'ten büyük elemanları filtrelemek istiyorsunuz;

```csharp
var idGt3_1 = products.Where(p => p.Id > 3);
var idGt3_2 = products.AsQueryable().Where(p => p.Id > 3);
```

Yukarıdaki iki metod da işimizi görür, ancak bu metodların aldığı parametreleri incelerseniz ufak (ancak çok önemli) bir fark görürsünüz.

```csharp
public static IEnumerable<TSource> Where<TSource>(this IEnumerable<TSource> source, Func<TSource, bool> predicate);

public static IQueryable<TSource> Where<TSource>(this IQueryable<TSource> source, Expression<Func<TSource, bool>> predicate);
```

İkisi de birer [extension method](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods) ancak bir tanesi filtre parametresi (Predicate) olarak Func alırken diğer Expression<Func> alıyor. 

### Func
**Func** tahmin etmiş olacağınız üzere bir fonksiyonu temsil ediyor, ancak önemli nokta derlenmiş bir fonksiyonu temsil ediyor. Derlenmiş bir fonksiyon demek, o an çalışılan işlemci için hazırlanmış, tekrar yorumlaması çok zor bir parça IL (Intermediate Language) Assembly kodu demek. Aşağıdaki gibi;

```csharp
.method assembly hidebysig 
    instance bool '<Main>b__0_0' (
        class Program/Product p
    ) cil managed 
{
    // Method begins at RVA 0x20bb
    // Code size 10 (0xa)
    .maxstack 8

    IL_0000: ldarg.1
    IL_0001: callvirt instance int32 Program/Product::get_Id()
    IL_0006: ldc.i4.3
    IL_0007: cgt
    IL_0009: ret
}
```
Ne yapalım şimdi bunu değil mi?
Bu çağrı bir liste üzerinde filtreleme yapmak istediğimizde tabii ki yeterli, ancak...

### Expression<Func>
Peki şöyle bir düşünelim, bir yerde veriler var, belki bir veritabanı tablolalarında, belki bir Xml dosyasında, sorgulanmak için sabırsızlıkla bekliyorlar. Microsoft'un elinde de veri sorgulamak için gerekli olduğunu düşündükleri [bir dolu metod](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable) var. Derleyici de ellerinde olunca bir akıllı demiş ki, 

> Biz bu kodu tam derlemesek, kodun **ifade** ettiği yapıyı bir şekilde saklasak, eğer Func olarak kullanmak gerekirse bu **ifadeyi** IL koduna tekrar derleriz?!?!

Müthiş zekice, şöyle, çalışma zamanı yukarıdaki IL Assembly kodu yerine aşağıdaki gibi bir objeye sahip oluyoruz (kabaca, anlaşılır olması için JSON syntax ile yazıyorum);

```JavaScript
{
    NodeType: "Lambda",
    Parameters: ["a"],
    Body: {
        NodeType: "GreaterThan",
        Left: {
            NodeType: "MemberAccess",
            Member: "Id"
        },
        Right: {...}
    }
    ...
}
```
Tüm detayları şimdiden vermek istemedim :)

[İkinci yazıda](/javascript-linq-02) görüşmek üzere.

> “Knowledge is power.” – Francis Bacon