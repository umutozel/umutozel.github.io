---
layout: post
title: JavaScript ile Linq yapalım!
comments: true
redirect_from: "/2018/10/18/JavaScript-Linq-01/"
permalink: javascript-linq-02
---

JavaScript ile Linq yazı serimizin ikincisine hoş geldiniz.

1. [Linq gerçekte nedir?](/javascript-linq-01)
2. Expression'lar **(You are here)**
3. [IQueryable ve IQueryProvider](/javascript-linq-03)
4. [Jokenizer - JavaScript Expression'larını parse edelim](/javascript-linq-04)
5. [Jokenizer.Net - C# Expression'larını parse edelim](/javascript-linq-05)
6. [Jinqu - JavaScript ile Linq](/javascript-linq-06)
7. [Linquest ve Linquest.AspNetCore - Asp.Net Core ile cevap verelim](/javascript-linq-07)

Bu yazıda Expression'ları inceleyeceğiz. Öncelikle Statement ve Expression arasındaki farkı açıklayarak başlayalım.

## Statement
Statement, bir ya da birden fazla Expression ve daha bir çok programala diline özel kod parçalarının bir araya gelmesiyle oluşur. Yani geliştirme yaparken yazdığınız kodlar ***-Statement-*** lar programınızı oluşturur. Statement bir iş yapar gibi kısa bir tanım yapabiliriz.

```csharp
Console.WriteLine("Hello World");
Console.ReadKey();
```

## Expression
Expression ise, Identifier (değişkenler), Literal (sabitler) ve Operator'ler kullanarak oluşturduğunuz kısa kod parçalarıdır. Expression bir değer döner gibi kısa bir tanım yapabiliriz.

Aşağıdaki gibi düşünebilirsiniz.
> Program > Statement > Expression

Bizim işimiz Expression'lar ile, çünkü Linq ile kullanacağımız metodlar Statement kabul etmiyorlar. Bir önceki yazıda Func için dediğimiz gibi, basitçe yorumlanması ve dönüştürülmesi zor olacağı için. 

## Expression sınıfı
Expression sınıfı ***abstract***, yani ondan türeyen Expression'lar için bir alt sınıf özelliği taşıyor. Ne özellikleri mi var?
### NodeType
Expression tipini öğrenebileceğimiz özellik, desteklenen tiplerden bir kısmını aşağıda listeliyorum ([80+ tüm liste burada](https://github.com/Microsoft/referencesource/blob/master/System.Core/Microsoft/Scripting/Ast/ExpressionType.cs)!);
```csharp
public enum ExpressionType {
        Add,
        And,
        Call,
        Constant,
        Divide,
        Equal,
        GreaterThan,
        LessThanOrEqual,
        MemberAccess,
        Modulo,
        Multiply,
        UnaryPlus,
        NegateChecked,
        New,
        Not,
        Or,
        Subtract,
        Assign,
        Increment
        ...
    }
```
Tahmin edeceğiniz gibi, aslında yazdığımız tüm kodlar bu listedeki Expression'lardan oluşuyor. Linq geliştirmemiz sırasında atamalar gibi bir çoğunu göz ardı edeceğiz.

### Type
Expression bir değer dönebilen ufak kod parçasıdır demiştik, Type bize dönüş değerinin tipini veriyor. Bir Predicate için bu tip ***bool*** olacaktır.

## ConstantExpression
ConstantExpression yazdığımız koda gömülü değerlerdir. 42, "Marvin" gibi değerleri örnek verebiliriz. Aşağıdaki gibi üretebiliriz.
```csharp
var constExp = Expression.Constant(42);
var constExp = Expression.Constant("Marvin");
```
### Value
Bir ConstantExpression'ın içerdiği değere ulaşmak için bu özelliği kullanırız.

## MemberExpression
C# çok sıkı bir Object Oriented dil, bir sınıfa ait olmayan değişken tanımlayamıyoruz. Kodlarımızda da çok yoğun bir şekilde bir sınıfa ait bir Property ya da Field'a ulaşmamız gerekiyor. İşte bu kodları temsil eden Expression'lara MemberExpression diyoruz.
```csharp
var product = new Product();
// product erişimini temsil eden bir ConstantExpression oluşturuyoruz
var constExp = Expression.Constant(product);
// product üzerindeki bir Property ya da Field erişimini temsil eden MemberExpression'ı oluşturuyoruz
var memberExp = Expression.PropertyOrField(constExp, "Id");
// böylece p.Id kodunu yazmış olduk
```
### Expression
Bu özellik eriştiğimiz Member'ın sahibini temsil ediyor. Tipinin Expression olmasından anlayacağınız üzere bu her zaman bir ConstantExpression olmak zorunda değil, bir metod çağrısından dönen obje üzerindeki bir Member'a da erişebiliriz. Bu yüzden alt sınıf tipi kullanılmış.
### MemberInfo
MemberInfo ise, ulaşmak istediğimiz Member'ı temsil ediyor, yukarıdaki kod parçasındaki Id alanı gibi.

## BinaryExpression
Yavaş yavaş başta verdiğimiz **Id > 3** fonksiyonunu hazırlamaya devam ediyoruz. C# dilindeki iki Expression ile yaptığımız operasyonlar BinaryExpression olarak adlandırılıyor. Burada özellikle **iki** diyorum dikkat ettiyseniz, şimdi biraz durup nasıl her zaman iki ifade olduğunu düşünebilirsiniz, beklemek istemeyenler neden böyle dediğimi açıkladığım alt tarafa geçebilirler.

Evet C# yaptığınız her operasyonu Left ve Right olmak üzere iki Expression üzerinden tutar.
```csharp
var no = 42;
var check = no > 20 && no > 30 && no < 50;
```
Bu yazdığımız uzun kod aslında aşağıdakine benzer bir yapı ile tutulacak.
```JavaScript
{
    "NodeType": "And",
    "Left": {
        "NodeType": "GreaterThan",
        "Left": {
            "Value": 42,
            "NodeType": ConstantExpression
        },
        "Right": {
            "Value": 20,
            "NodeType": ConstantExpression
        },
        "NodeType": "BinaryExpression"
    },
    "Right": {
        "NodeType": "And",
        "Left": {
            "NodeType": "GreaterThan",
            "Left": {
                "Value": 42,
                "NodeType": ConstantExpression
            },
            "Right": {
                "Value": 30,
                "NodeType": ConstantExpression
            },
            "NodeType": "BinaryExpression"
        },
        "Right": {
            "NodeType": "LesserThan",
            "Left": {
                "Value": 42,
                "NodeType": ConstantExpression
            },
            "Right": {
                "Value": 50,
                "NodeType": ConstantExpression
            },
            "NodeType": "BinaryExpression"
        },
        "NodeType": "BinaryExpression"
    },
    "NodeType": "BinaryExpression"
}
```
Okuması biraz zor, ancak bir süre sonra alışıyorsunuz. Daha önce de bahsetmiştik, şimdi tekrarlamanın tam zamanı. Yazdığımız kodu bir ağaç yapısında elde ettiğimizde bu kodu başka bir yapıya çevirmek, örneğin bir Sql sorgusuna, çok daha kolay oluyor (derlenmiş IL Assembly koduna göre).

Şimdi çalışma zamanı bir BinaryExpression oluşturalım.
```csharp
// 42 > 20 ifadesini oluşturalım
BinaryExpression binaryExp = Expression.MakeBinary(
    ExpressionType.GreaterThan, // büyüktür işlemi yapıyoruz
    Expression.Constant(42), // sol Expression
    Expression.Constant(20) // sağ Expression
);
```

## LambdaExpression
LambdaExpression ise dışarıdan parametreler alan ve bu parametreleri kullanan Expression'ları sayesinde bir sonuç üretebilen özel bir Expression türü. Evet o da bir Expression. Çalışma zamanında Lambda'ları derleyerek Func elde edebiliriz. Bu Func ise parametreler ile çağırılarak bir çalıştırılabilir.

```csharp
// dışarıdan bir Product parametresi alan ve Id değerini 3 ile karşılaştıran bir Lambda
Expression<Func<Product, bool>> lambda = p => p.Id > 3;
// derleyerek bir fonksiyon elde ediyoruz
// bir değer alıp bool dönen foksiyonlara genel olarak Predicate denir
Func<Product, bool> predicate = lambda.Compile();
// bir Product ile test edelim
var product = new Product() { Id = 42 };
var result = predicate(product);
Console.WriteLine(product);  // true!
```
Bir önceki yazıda bahsettiğim IEnumerable üzerinde yapılan çağrılar Func parametre alıyorlar. Bu yüzden LambdaExpression'ı derleyerek bir Func elde etmemiz gerekti. 
Yine sizi duyar gibiyim, ***CSharpCodeProvider*** ile kodumuzu string olarak verip, bir Assembly'ye derleyebilirdik? Evet ama yönetmesi, kontrol etmesi çok zor bir yapı elde edersiniz. Derleme sonucu bir metoda ulaşmak için Assembly->Sınıf->Metod yolculuğuna Reflection ile çıkmanız gerekir. Hem ne derler bilirsiniz, Eval is Evil.

Daha fazla kafa karıştırmayalım, yazı dizisinde Func ile ilgilenmeyeceğiz, Expression'ları olduğu gibi kullanarak bir sonraki yazımızda değineceğimiz IQueryProvider ile yorumlanmalarını sağlayacağız.

## ParameterExpression
Artık baştan beri kullandığımız
```csharp
p => p.Id > 3
```
ifadesini çalışma zamanı oluşturmaya hazır sayılırız. Lambda için bir fonksiyon temsilcisi dedik, yani parametre alabilir ve bir değer döner. Aldığı bu parametreler ParameterExpression tipinde olmalı. En basit Expression tiplerinden birisi olan ParameterExpression'ı aşağıdaki gibi oluşturabiliriz.
```csharp
ParameterExpression paramExp = Expression.Parameter(typeof(Parameter));
```
Yukarıdaki kod ile **=>** ifadesinin solunda kalan **p** parametresini tanımlamış olduk.

Evet, sonunda LambdaExpression oluşturmaya hazırız.
```csharp
// parametremizi tanımlayalım, diğer Expression'lar bu parametreye isimle değil, bu değişken ile ulaşmak zorundalar
var prm = Expression.Parameter(typeof(Parameter));
var lambda = Expression.Lambda(
    Expression.MakeBinary(                      // Lambda Body özelliği bir BinaryExpression
        ExpressionType.GreaterThan,             // Büyüktür karşılaştırması yapacağız
        Expression.PropertyOrField(prm, "Id"),  // Dışarıdan gelen parametrenin Id özelliği
        Expression.Constant(3)                  // sabit değerimizi ConstantExpression ile geçiyoruz
    ),
    prm     // Lambda'nın bu parametreyi dışarıdan beklediğini belirtiyoruz
);
```

İşte çalışma zamanı bir LambdaExpression oluşturduk. Bu kodumuzu bir sonraki yazıda göreceğimiz IQueryable metodları ile kullanabileceğiz ve Entity Framework gibi destekleyen araçların dinamik sorgu çalıştırmalarını sağlayabileceğiz.

Daha başka bir çok ExpressionType mevcut ancak bize şimdilik bu kadarı yeterli. İleride göreceksiniz, ben de geliştirdiğim projelerde tümünü desteklemedim. Yukarıda gördüğünüz yaklaşım hepsi için geçerli, çok ufak bir çalışma ile ya da açık kaynak .Net kodlarını inceleyerek hızlıca hepsini öğrenebilirsiniz.

[Üçüncü yazıda IQueryable ve IQueryProvider ile sorgu mevzusuna gireceğiz](/javascript-linq-03), görüşmek üzere.

> “Before software can be reusable it first has to be usable.” – Ralph Johnson
