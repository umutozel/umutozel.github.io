---
layout: post
title: Jinqu - JavaScript ile Linq
comments: true
redirect_from: "/2018/11/27/JavaScript-Linq-08/"
permalink: javascript-linq-08
---

JavaScript ile Linq yazı serimizin sekizincisine hoş geldiniz.

1. [Linq gerçekte nedir?](/javascript-linq-01)
2. [Expression'lar](/javascript-linq-02)
3. [ExpressionVisitor sınıfı](/javascript-linq-03)
4. [IQueryable ve IQueryProvider](/javascript-linq-03)
5. [Jokenizer - JavaScript Expression'larını parse edelim](/javascript-linq-05)
6. [Jokenizer.Net - C# Expression'larını parse edelim](/javascript-linq-06)
7. [DynamicQueryable - Dinamik sorgu oluşturalım](/javascript-linq-07)
8. Jinqu - JavaScript ile Linq  **(You are here)**
9. [Linquest ve Linquest.AspNetCore - Asp.Net Core ile cevap verelim](/javascript-linq-09)

Bu yazıda [jinqu](https://github.com/jin-qu/jinqu) projesi ile JavaScript'e Linq yeteneği kazandıracağız.

Geldiğimiz noktada JavaScript ve C# ile yazılan ifadeleri parse edip yorumlayabilir durumdayız, C# ile bu ifadeleri IQueryable üzerine yansıtıp sorgularımızı da dinamik oluşturabiliyoruz. Tekrar istemci tarafına dönelim, C# ile Linq nasıl yapılmışsa olabildiğince benzer bir yöntem ile JavaScript için gerçekleştirelim. İlk olarak Linq-to-Objects yani diziler üzerinde sorgu çalıştırabilmemizi sağlayan geliştirmeleri yapalım. Bunu öyle bir yapalım ki, sorgularımızı sunuculara gönderebilmemiz için güzel bir altyapı sağlasın.

Hemen proje yapımıza bakarak başlayalım:

![Proje Yapısı](/assets/jinqu-structure.png)

İlk ele almamız gereken dosya **type.ts**. C# için Linq'dan bahsederken IQueryable ve IQueryProvider interface'lerinden bahsetmiştik. Olabildiğince yakın yapmaya çalışacağımdan bahsetmiştim, işte **IQueryProvider**.

```TypeScript
export interface IQueryProvider {
    createQuery(parts?: IQueryPart[]): IQueryBase;
    execute<TResult = any[]>(parts: IQueryPart[]): TResult;
    executeAsync<TResult = any[]>(parts: IQueryPart[]): PromiseLike<TResult>;
}
```

şimdi bir de C# nasıl yapmış onu görelim:

```csharp
public interface IQueryProvider{
    IQueryable CreateQuery(Expression expression);
    IQueryable<TElement> CreateQuery<TElement>(Expression expression);

    object Execute(Expression expression);
    TResult Execute<TResult>(Expression expression);
}
```

Neredeyse aynı olduğunu görebilirsiniz, sadece asenkron desteğini sonradan eklemek yerine en alt seviyede ekledim.

Expression yerine kullandığımız **QueryPart** nedir merak ettiyseniz:

```TypeScript
export interface IQueryPart {
    readonly type: string;
    readonly args: IPartArgument[];
    readonly scopes: any[];
}
```

Bunu biraz açıklamak gerekecek. C# aşağıdaki metot zinciri çağrılarını her zaman tek bir Expression olarak tutuyor, aşağıdaki gibi:

```csharp
query.Where(c => c.Id > 5).OrderBy(c => c.Name);
```

bu çağrıların sonucu aşağıdakine benzer bir şekilde tutulur:

```json
{
    "NodeType": "Call",
    "Method": "OrderBy",
    "Body": "c => c.Name",
    "Parameters": {
        "NodeType": "Call",
        "Method": "Where",
        "Body": "c => c.Id > 5"
    }
}
```

Gördüğünüz gibi, **Where** çağrısı sonucunu **OrderBy** çağrısının bir parametresi olarak Expression'a ekliyor. Bunu uygulamayı değerlendirdiğimde daha basit bir yöntemi tercih ettim, çünkü amacım birebir C# ile aynı olan bir çözümden ziyade, bir liste üzerinde yapılan sorgulama ifadelerini değerlendirebilmek gibi daha küçük kapsamlı bir ihtiyaca çözüm bulmak idi.

Benim yaklaşımımda ise, her bir çağrı ayrı bir **IQueryPart** ve sorgu içinde tuttuğum obje içiçe çağrıları temsil eden bir Expression yerine **IQueryPart** dizisi. Böylece sorgu oluştururken aldığımız parametre **Expression** yerine **IQueryPart[]** oluyor.

Sıradaki tipimiz ise **IQueryBase**.

```TypeScript
export interface IQueryBase {
    readonly provider: IQueryProvider;
    readonly parts: IQueryPart[];
}
```

C# ile karşılaştıralım:

```csharp
 public interface IQueryable : IEnumerable {
    Expression Expression { get; }
    Type ElementType { get; }
    IQueryProvider Provider { get; }
}
```

* TypeScript ile C# ile olduğu gibi generic parametreleri farklı aynı isimli tip oluşturamıyoruz. O yüzden farklı bir isim verdim.

[Dokuzuncu ve son yazıda linquest ile sunucu üzerinde Linq çalıştıracağız](/javascript-linq-09), görüşmek üzere.

> The best performance improvement is the transition from the nonworking state to the working state. - J. Osterhout
