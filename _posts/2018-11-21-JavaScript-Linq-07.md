---
layout: post
title: DynamicQueryable - Dinamik sorgu oluşturalım
comments: true
redirect_from: "/2018/11/21/JavaScript-Linq-07/"
permalink: javascript-linq-07
---

JavaScript ile Linq yazı serimizin yedincisine hoş geldiniz.

1. [Linq gerçekte nedir?](/javascript-linq-01)
2. [Expression'lar](/javascript-linq-02)
3. [ExpressionVisitor sınıfı](/javascript-linq-03)
4. [IQueryable ve IQueryProvider](/javascript-linq-03)
5. [Jokenizer - JavaScript Expression'larını parse edelim](/javascript-linq-05)
6. [Jokenizer.Net - C# Expression'larını parse edelim](/javascript-linq-06)
7. DynamicQueryable - Dinamik sorgu oluşturalım  **(You are here)**
8. [Jinqu - JavaScript ile Linq](/javascript-linq-08)
9. [Linquest ve Linquest.AspNetCore - Asp.Net Core ile cevap verelim](/javascript-linq-09)

Bu yazıda [DynamicQueryable](https://github.com/umutozel/DynamicQueryable) projesi ile **string** ifadeleri **IQueryable** sorgularına uygulayacağız.

IQueryable üzerindeki metodlar nereden geliyor peki?

```csharp
public static IQueryable<TResult> Select<TSource,TResult>(this IQueryable<TSource> source, Expression<Func<TSource, TResult>> selector) {
    return source.Provider.CreateQuery<TResult>(
        Expression.Call(
            null,
            GetMethodInfo(Queryable.Select, source, selector),
            new Expression[] { source.Expression, Expression.Quote(selector) }
        )
    );
}
```

Yukarıda **Select** için örneği görüyorsunuz, tüm bu metodlar (**Where**, **OrderBy**..) birer **Extension Method**.

[Expression](/javascript-linq-02) ve [IQueryable ile IQueryProvider](/javascript-linq-03) konularından bahsetmiştik. Burada da **Queryable** sınıfındaki **Select** metodunu **selector** parametresi ile çağırmayı temsil eden bir **Expression** oluşturuluyor. Bu **Expression** ile yeni bir **IQueryable** oluşturuluyor ve bu yeni sorgu çağrının yapıldığı yere dönülüyor.

Bizim de yapmamız gereken bu çağrının çok benzerini **string** değerler ile yapabilmek. Yani metod imzamız aşağıdakine benzemeli:

```csharp
public static IQueryable Select(this IQueryable source, string selector)
```

Bizim **selector** ifademiz bir **string** olduğu için dönüş tipini generic olarak veremiyoruz. **string** bir **selector** için sorgunun tipi de önemli olmadığından ***TSource*** generic parametresine de ihtiyacımız yok.

Tüm metodlarda çok benzer işler yapacağız, bize gelen **string** parametreyi bir **LambdaExpression**'a **Jokenizer.Net** yardımı ile çevireceğiz. Birkaç adet yardımcı fonksiyon işimizi görecektir. Önce proje yapımızı bir görelim.

![Project Yapısı](/assets/dynamicqueryable-structure.png)

Listede gördüğünüz tüm dosyalar tek bir sınıfın parçaları (partial class)

```csharp
public static partial class DynamicQueryable {
}
```

Yani tüm **extension metotlarımız** tek bir sınıf içinde toplanmış halde.

Linq ile çağırabileceğimiz metotları iki ana kategoriye ayırabiliriz, **sorgu dönenler** (Where, OrderBy, Select vs..) ve **sonuç dönenler** (First, Min, Any vs...). Yardımcı metotlar için bu iki kategori önemli, **DynamicQueryable.cs** sınıfı tüm yardımcıları içeriyor.

Sonuç dönen yardımcılara örnek:

```csharp
// Bu metot bir sorguyu bir Lambda ile çağıracak
private static object ExecuteLambda(IQueryable source, string method, string expression, bool generic, IDictionary<string, object> variables, params object[] values) {
    var lambda = CreateLambda(source, method, expression, generic, variables, values);
    return source.Provider.Execute(lambda);
}

// Bu metot ise opsiyonel lambda alan metotlar için, First, Single gibi
private static object ExecuteOptionalExpression(IQueryable source, string method, string expression, bool generic, IDictionary<string, object> variables, params object[] values) {
    return string.IsNullOrEmpty(expression)
        ? Execute(source, method)
        : ExecuteLambda(source, method, expression, generic, variables, values);
}
```

Sorgu modifiye eden yardımcılara örnek:

```csharp
// Bu metot bir string ifadeden bir lambda oluşturup bunu sorguya uyguluyor
private static IQueryable HandleLambda(IQueryable source, string method, string expression, bool generic, IDictionary<string, object> variables, object[] values) {
    var lambda = CreateLambda(source, method, expression, generic, variables, values);
    return source.Provider.CreateQuery(lambda);
}
```

Ortak kullanılan en önemli yardımcı metotlarımız ise aşağıdaki gibi:

```csharp
// Burada gönderilen string ifade için Evaluator.ToLambda çağrısı ile Jokenizer.Net kullanarak Lambda oluşturuyoruz
private static Expression CreateLambda(IQueryable source, string method, string expression, bool generic, IDictionary<string, object> variables, params object[] values) {
    var types = new[] { source.ElementType };
    var lambda = Evaluator.ToLambda(expression, types, variables, values);

    return Expression.Call(
        typeof(Queryable),
        method,
        // Eğer metotun dönüş tipi farklı ise iki tip parametresi geçiyoruz
        //  Where metotu aynı tipte sorgu döndüğü için tek tip parametresi alırken: Where<TSource>(predicate)
        //  Select metotu tip dönüşümü yaptığı için iki tip parametresi alır: Select<TSource, TResult>(selector)
        generic ? new[] { source.ElementType, lambda.Body.Type } : types,
        source.Expression,
        Expression.Quote(lambda)
    );
}
```

Peki bu yardımcı metotları nasıl kullanıyoruz? Aşağıda birkaç örnek ile inceleyelim:

```csharp
// Yardımcı metotlar sayesinde çoğu metotumuz tek bir satır
// Çoğu metot için bol bol overload bulunmakta
public static IQueryable Where(IQueryable source, string predicate, IDictionary<string, object> variables, params object[] values) {
    return HandleLambda(source, "Where", predicate, false, variables, values);
}

// Yukarıda bahsetmiştik, Select için "generic" parametresi "true", Where için "false"
public static IQueryable Select(this IQueryable source, string selector, IDictionary<string, object> variables, params object[] values) {
    return HandleLambda(source, "Select", selector, true, variables, values);
}
```

Kodları incelerseniz çoğu metot için çok benzer çağrılar olduğunu görebilirsiniz.
GroupBy ve SelectMany gibi tam bir kalıba girmeyen, yardımcılara uymayan metotları ise nasıl uyguladığıma bir bakmanızı tavsiye ederim.

Dinamik sorgu için anlatılacaklar bu kadar, bir örnek ile nasıl kullanıldığına bakalım (detaylı kullanımlar için birim testlerine göz atın):

```csharp
IQueryable<Order> query = GetOrders();
var orders = query.Where(o => o.Id > AvgId).ToList();
// parametremizi sıralı kullanım ile geçebiliriz
var dynOrders1 = query.Where("o => o.Id > @0", AvgId).ToList();
// isimli parametre de geçebiliriz
var dynOrders2 = query.Where("o => o.Id > AvgId", new Dictionary<string, object> { { "AvgId", AvgId } }).ToList();
// sorgu tipini bilmediğimiz durumlarda dq kullanabiliyoruz (IQueryable<Order> yerine IQueryable)
var dynOrders3 = ((IQueryable)query).Where("Id > @0", AvgId).Cast<Order>().ToList();
```

İstemci tarafında ve sunucu tarafında dinamik ifadeler ile çalışabilir hale geldik. Şimdi JavaScript ile Linq geliştirme zamanı.

[Sekizinci yazıda Jinqu ile JavaScript'e Linq yeteneği kazandıracağız](/javascript-linq-08), görüşmek üzere.

> “If debugging is the process of removing software bugs, then programming must be the process of putting them in.” ― Edsger Dijkstra
