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
var exp = ParseExpression("a > 42");
var result = Evaluate(exp, { a = 42 });
```

Bu durumun üstesinden gelebilmek için Expression çevirimi yapmadan önce bir ara tip oluşturup adına da Token dedim. Kodları incelerseniz bir önceki yazıda JavaScript'e Expression özelliği katmak için yaptığımız geliştirmelere çok benzediğini fark edebilirsiniz.

# Proje Yapısı

Acelesi olanlar projenin bitmiş halini [https://github.com/umutozel/jokenizer.net](https://github.com/umutozel/jokenizer.net) adresinden inceleyebilir.

![Proje Yapısı](/assets/jokenizer.net-structure.png)

## ./Tokens klasörü

./Tokens klasörüne bakarsanız jokenizer projesindeki Expression'lara karşılık gelen Token'lar olduğu görebilirsiniz. Desteklediğimiz Token'ları yukarıdaki resimde görebilirsiniz.

## ./Tokenizer

Bu sınıfımız verdiğimiz string değeri parse edip Token dönüyor. Önceki makalemizde anlattığımız JavaScript parse eden tokenizer.ts dosyamızdaki kodlarımıza çok benziyor. Parse etmeye bir örnek verelim:

```csharp
var expression = Tokenizer.Parse<ObjectToken>("new { a = 4, b.c }");
```

Yukarıda bir ObjectToken parse ettik, tabii ki bir önceki yazıdan farklı olarak C# yazdığımız için **new** keyword'ü geçmemiz gerekti.

## ./TokenVisitor

TokenVisitor ise oluşturduğumuz Token'ları recursive bir şekilde gezerek Lambda üretmemizi sağlıyor. Bu sınıftan yeni bir sınıf türeterek Expression oluşturma aşamasını istediğiniz gibi özelleştirebilirsiniz. Yine olayların iç yapısıyla ilgilenen arkadaşlar bu sınıfın ne kadar da C#'ın Expression düzenlemek için bize sağladığı **ExpressionVisitor** sınıfına benzediğini farketmiştir. Aşağıdaki gibi bir çağrıyla artık çalıştırılabilir bir fonksiyona sahip oluyoruz.

```csharp
new TokenVisitor(variables, parameters).Process(token, typeParameters)
```

Tabi bu çok kullanışlı bir metod değil, daha rahat bir yapıya ihtiyacımız var.

## ./Evaluator

Evaluator işte tam bu işe yarıyor. ExpressionVisitor çağrısını bizim için yapıyor ve tip dönüşümü yapılmış bir Lambda'yı hizmetimize sunuyor. Kodları incelerseniz birçok overload ile farklı imzalar destekleyen ama hep aynı işi yapan fonksiyonlar olduğunu görebilirsiniz. Aşağıdaki gibi kullanıyoruz:

```csharp
var token = Tokenizer.Parse<BinaryToken>("a > 42");
// a değişkeni için karşılık gelen değeri Dictionary ile geçtik
var lambda = Evaluator.ToLambda<int, bool>(token, new Dictionary<string, object> { { "a", 40 } });
var result = lambda();  // false
```

Burada parametrik değeri isimle geçmek yerine sırayla da geçebiliyoruz:

```csharp
var token = Tokenizer.Parse<BinaryToken>("@0 > 42");
// @0 parametre olarak yorumlanacak ve gönderilen parametre listesindeki ilk değeri alacak
var lambda = Evaluator.ToLambda<int, bool>(token, 40);
var result = lambda();  // false
```

Tabi kısayolları çok sevdiğim için her zaman önce **Token** sonra **Evaluate** yerine direk **string** değerden yorumlayan fonksiyonları da ekledim.

```csharp
var lambda = Evaluator.ToLambda<int, bool>("@0 > 42", 40);
var result = lambda();  // false
```

## ./ExtensionMethods

Özellikle Linq ile çalışırken sık sık **Any** ve **All** gibi fonksiyonları kullanmamız gerekir. Meraklı arkadaşlar bu metodların bir tip üzerinde değil, IQueryable ve IEnumerable arayüzleri için birere **Extension Method** olduğunu farketmiştir. Jokenizer gibi parser yazarken elinizde olan tip bilgileri üzerinden yorumlama yaparsınız, bu durumda extension metodlar elimizden kaçmış oluyor, çünkü onlar başka bir tipte olmanın ötesinde bambaşka **Assembly**'lerde de olabiliyorlar.

Bu sınıfımız ise lazım olacağını tahmin ettiği extension metodları önbelleklemenin ötesinde başkalarını da aklında tutmasını sağlayabilmemiz için bize bir yapı sunuyor. Testleri incelerseniz aşağıdaki kullanımı görebilirsiniz:

```csharp
// Extensions sınıfının yer aldığı Assembly'deki tüm extension metodları önbellekliyoruz
ExtensionMethods.ProbeAssemblies(typeof(Extensions).Assembly);

// Bu önbelleklenmiş metoda ise TokenVisitor aşağıdaki gibi erişiyor
var method = ExtensionMethods.Find(owner.Type, methodName, methodArgs);
// ve bu metodu kullanan bir CallExpression oluşturuyor
var expression = Expression.Call(null, method, new[] { owner }.Concat(methodArgs))
```

## ./Dynamic Klasörü

C# dilinin tip takıntılı bir dil olduğunu söylemiştik, bir sınıfa ait olmayan tek bir satır bile yazamıyorsunuz. Peki biraz yukarıda gördüğümüz **ObjectToken** yorumlarken **new** ile oluşturduğumuz obje hangi sınıfa ait? Evet bu tiplere **anonim** tipler diyoruz ve geliştirme aşamasında kullandığımız anonim tipler için derleyici bizim için otomatik tip oluşturabiliyor, aşağıdaki gibi:

![Anonymous Type Assembly](/assets/jokenizer.net-anon.png)

Peki çalışma zamanı böyle tiplere ihtiyacımız olursa? Tahmin ettiğiniz gibi derleyicinin yaptığına benzer bir işi bizim yapmamız gerekiyor, yani çalışma zamanı sınıf oluşturmamız gerekiyor. Kısaca bu iş için geliştirdiğimiz sınıflara bakalım.

### ./Dynamic/Signature

Signature bir sınıfın **imza**sını saklamak için kullandığımız bir yapı.

```csharp
// ilk tipimiz "a" ve "c" propertylerini içeriyor (b.c int olduğunu varsayın)
Tokenizer.Parse<ObjectToken>("new { a = 4, b.c }");

// burada yine "a" ve "c" int propertylerinden oluşan bir tip oluşturuyoruz
Tokenizer.Parse<ObjectToken>("new { a = 4, c = 5 }");
```

Yukarıdaki örnekte gördüğünüz gibi, iki anonim sınıfımız da **a** ve **c** isimli iki adet integer özellik içeriyor. Bu iki anonim tip için çalışma zamanı iki farklı sınıf oluşturmamız gereksiz olur, ilk sınıfı ikinci için de kullanabiliriz. Daha önce oluşturduğumuz sınıfları aklımızda tutabilmek için **Signature** yapısını kullanıyoruz.

Değinmekte yarar var, C# derleyicisi de anonim tipler için çok benzer bir yapıyla aynı imzaya sahip sınıflar için önceden oluşturduklarını kullanıyor, merak edenlere denemelerini tavsiye ederim.

### ./Dynamic/DynamicClass

DynamicClass ise bizim çalışma zamanı anonim tiplerimizi oluştururken kalıtım için kullanacağamız bir alt sınıf. Bize **ToString** çağrısında güzel bir çıktı sunma dışında bir iş yapmıyor.

### ./Dynamic/ClassFactory

Son olarak ele alacağımız ClassFactory ise bu anonim sınıfları üretmekten sorumlu. Aşağıdaki fonksiyonu inceleyerek başlayalım:

```csharp
// anonim tip oluşturan metodumuz
Type CreateDynamicClass(DynamicProperty[] properties) {
    // her yeni anonim sınıfa artan indeks ile yeni bir isim veriyoruz
    string typeName = "DynamicClass" + (classes.Count + 1);
    // sınıfımızı Public olarak DynamicClass'tan türeyecek şekilde oluşturuyoruz
    TypeBuilder tb = this.module.DefineType(typeName, TypeAttributes.Class | TypeAttributes.Public, typeof(DynamicClass));
    // Property'ler oluşturuluyor. Reflection Emit ile çalışma zamanı okuma-yazma destekli Property'ler oluşturuluyor
    FieldInfo[] fields = GenerateProperties(tb, properties);
    // Equals ve HashCode metodları da Reflection Emit ile oluşturuluyor
    GenerateEquals(tb, fields);
    GenerateGetHashCode(tb, fields);
    // TypeBuilder ile sınıfımızı oluşturuyoruz
    return tb.CreateTypeInfo().AsType();
}
```

Artık C# ile de string değerleri Expression'a çevirebilir hale geldik. JavaScript ile oluşturduğumuz sorguları sunucuda yorumlayıp yanıt dönebilmemize çok az kaldı.

[Yedinci yazıda DynamicQueryable ile Jokenizer.Net dinamik sorgu oluşturacağız](/javascript-linq-07), görüşmek üzere.

> “Always code as if the guy who ends up maintaining your code will be a violent psychopath who knows where you live” ― John Woods
