---
layout: post
title: Jokenizer.Net - C# Expression'larını parse edelim
comments: true
redirect_from: "/2018/10/26/JavaScript-Linq-06/"
permalink: javascript-linq-06
---

JavaScript ile Linq yazı serimizin altıncısına hoş geldiniz.

1. [Linq gerçekte nedir?](/javascript-linq-01)
2. [Expression'lar](/javascript-linq-02)
3. [ExpressionVisitor sınıfı](/javascript-linq-03)
4. [IQueryable ve IQueryProvider](/javascript-linq-03)
5. [Jokenizer - JavaScript Expression'larını parse edelim](/javascript-linq-05)
6. Jokenizer.Net - C# Expression'larını parse edelim  **(You are here)**
7. [DynamicQueryable ile Jokenizer.Net dinamik sorgu oluşturalım](/javascript-linq-07)
8. [Jinqu - JavaScript ile Linq](/javascript-linq-08)
9. [Linquest ve Linquest.AspNetCore - Asp.Net Core ile cevap verelim](/javascript-linq-09)

Bu yazıda [Jokenizer.Net](https://github.com/umutozel/Jokenizer.Net) projesi ile C# Token'lar parse edip Expression'lar üreteceğiz.

C# Expression'ları bir önceki yazıda geliştirdiğimiz Expression yapısından biraz farklı. C#'ta Expression'lar sonradan dışarıdan parametre almaya ihtiyaç duymadan çalışabilecek şekilde tasarlanmış. Yani parametrelerin de oluşturulmaları sırasında hazır olması gerekiyor. Bir örnek ile açıklayalım:

```csharp
var expStr = "a > 42";
```

Bu ifadeyi Expression olarak parse etmek istediğinizde **a** değeri için bir parametre vermeniz gerekiyor. JavaScript ile yaptığımız gibi sonradan parametre ile **scope** göndermemiz mümkün olmuyor. Yani aşağıdaki gibi bir çağrı yapamıyoruz:

```csharp
// Expression olarak parse etmek istersek 
// bu aşamada "a" bilinmediği için hata alırız
var exp = ParseExpression(expStr);
var result = Evaluate(exp, { a = 42 });
```

Bu durumun üstesinden gelebilmek için Expression çevirimi yapmadan önce bir ara tip oluşturup adına da Token dedim. Kodları incelerseniz bir önceki yazıda JavaScript'e Expression özelliği katmak için yaptığımız geliştirmelere çok benzediğini fark edebilirsiniz.

# Proje Yapısı

Acelesi olanlar projenin bitmiş halini [https://github.com/umutozel/jokenizer.net](https://github.com/umutozel/jokenizer.net) adresinden inceleyebilir.

![Proje Yapısı](/assets/jokenizer.net-structure.png)

## ./Tokens klasörü

./Tokens klasörüne bakarsanız aynı jokenizer projesindeki Expression'lara karşılık gelen Token'lar olduğu görülebilir. Ayrıca Tokenizer.cs bir önceki yazımızda detaylı incelediğimiz tokenizer.ts kodlarına çok benzer kodlar içeriyor. Token parse etmeye bir örnek verelim:

```csharp
var expression = Tokenizer.Parse<ObjectToken>("new { a = 4, b.c }");
```

Yukarıda bir ObjectToken parse ettik, tabii ki bir önceki yazıdan farklı olarak C# yazdığımız için **new** keyword'ü geçmemiz gerekti.

[Yedinci yazıda DynamicQueryable ile Jokenizer.Net dinamik sorgu oluşturacağız](/javascript-linq-07), görüşmek üzere.

> “Always code as if the guy who ends up maintaining your code will be a violent psychopath who knows where you live” ― John Woods
