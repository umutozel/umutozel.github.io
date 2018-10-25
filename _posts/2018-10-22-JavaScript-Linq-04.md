---
layout: post
title: IQueryable ve IQueryProvider
comments: true
redirect_from: "/2018/10/22/JavaScript-Linq-04/"
permalink: javascript-linq-04
---

JavaScript ile Linq yazı serimizin dördüncüsüne hoş geldiniz.

1. [Linq gerçekte nedir?](/javascript-linq-01)
2. [Expression'lar](/javascript-linq-02)
3. [ExpressionVisitor sınıfı](/javascript-linq-03)
4. IQueryable ve IQueryProvider **(You are here)**
5. [Jokenizer - JavaScript Expression'larını parse edelim](/javascript-linq-05)
6. [Jokenizer.Net - C# Expression'larını parse edelim](/javascript-linq-06)
7. [DynamicQueryable ile Jokenizer.Net dinamik sorgu oluşturalım](/javascript-linq-07)
8. [Jinqu - JavaScript ile Linq](/javascript-linq-08)
9. [Linquest ve Linquest.AspNetCore - Asp.Net Core ile cevap verelim](/javascript-linq-09)

Bu yazıda IQueryable ve IQueryProvider ile veri sorgulama sanatını inceleyeceğiz.

# IQueryable

IQueryable aşağıdaki imzaya sahip basit bir interface.

```csharp
public interface IQueryable : IEnumerable {
    Expression Expression { get; }
    Type ElementType { get; }

    // the provider that created this query
    IQueryProvider Provider { get; }
}

public interface IQueryable<out T> : IEnumerable<T>, IQueryable {
}
```

Önceki yazılarda Expression konusunu incelemiştik, kısaca hatırlatmak gerekirse:

> Expression; tek parça (blok olmayan, bıyık parantezlere almak gerekmeyen), bir dönüş değerine sahip kodu temsil eden ağaç yapısında tutulan bir obje.

IQueryable ise, yukarıdaki koddan görebileceğiniz gibi, bir veri kaynağını hedefleyen Immutable bir Expression taşıyıcısı.

## Immutable

Immutable, fonksiyonel dillerde çok sık duyacağınız bir terim. Oluşturulan bir objenin özelliklerinin sonradan değiştirilemeyeceğini ifade eder. Aşağıda çok sık yapılan bir hata üzerinden açıklayalım.

```csharp
// context bir EF context. Toplam 100 Company olduğunu varsayalım.
var query = context.Companies;
// sadece ilk 3 kaydı isteyelim
query.Take(3);
// Take çağrısı yeni bir sorgu oluşmasına sebep oldu
// 100 kayıdın hepsini okuduk. Yeni sorgu query'ye atanmadığı için query halen eski halinde
// Objenin kendisini değiştirmeyen bu tür fonksiyonlara Side-Effect Free fonksiyonlar denir
var list = query.ToList();
```

Aklınıza IQueryable üzerinde hiç metod yokken nasıl ***Where***, ***OrderBy*** gibi metodları çağırabiliyoruz sorusu gelebilir. Gördüğünüz tüm metodlar **Queryable** statik sınıfından gelen Extension metodları, aşağıda bir kaç örnek görebilirsiniz (biraz basitleştirdim).

```csharp
public static class Queryable
{
    public static IQueryable<TSource> Where<TSource>(this IQueryable<TSource> source,
        Expression<Func<TSource, bool>> predicate)
    {
        // Değiştirilmiş Expression'ı içeren yeni bir sorgu yaratılıyor
        return source.Provider.CreateQuery<TSource>(
            // Bir metod çağrısı temsil eden Expression oluşturuluyor
            Expression.Call(
                null,       // Metod sahibi object. Static olduğu için null
                typeof(Queryable).GetMethod("Where"),     // Hangi metod çağırılıyor, MethodInfo tipinde
                source.Expression,      // Sorgunun bu çağrı öncesi Expression'ı
                predicate       // Filtre Predicate fonksiyonu
            ));
    }

    public static IQueryable<TResult> Select<TSource, TResult>(this IQueryable<TSource> source,
        Expression<Func<TSource, TResult>> selector)
    {
        return source.Provider.CreateQuery<TResult>(
            Expression.Call(
                null,
                typeof(Queryable).GetMethod("OrderBy"),
                source.Expression,
                selector
            ));
    }
...
```

En basit şekilde açıklamak gerekirse, **Where** çağrısı yaptığınızda sorgunun eski Expression'ı ile predicate'i birlikte temsil eden yeni bir Expression oluşacak.
Biraz dikkatli inceleyince de aslında bu metodun, kendisinin çağırıldığını temsil eden bir Expression oluşturmaktan başka bir iş yapmadığını görebilirsiniz, **typeof(Queryable).GetMethod("Where")** zaten bu **Where** metodu.

Şu Expression ağacına tekrar bakalım, daha iyi anlaşılacağına eminim.

![Expression Tree](https://image.ibb.co/d6VvQq/Expression-Tree.png)

Peki bu Expression ağacını kim değerlendiriyor?

# IQueryProvider

IQueryable interface'i tanımında gördüğümüz IQueryProvider sorgumuzun çalıştırılmasından sorumlu yine bir interface. Aşağıdaki gibi bir yapıya sahip.

```csharp
public interface IQueryProvider{
    IQueryable CreateQuery(Expression expression);
    IQueryable<TElement> CreateQuery<TElement>(Expression expression);

    object Execute(Expression expression);
    TResult Execute<TResult>(Expression expression);
}
```

**CreateQuery** metodunu yukarıda sorgu sağlayıcıdan yeni bir sorgu oluşturmasını isterken kullanmıştık. Yine basite indirgemek gerekirse, **IQueryable**, Expression ve **IQueryProvider** taşıyıcısıdır diyebiliriz. Kendi sorgu yapınızı yazdığınızda çok daha net anlaşılacaktır, ufak bir örnek yapalım.

```csharp
// Sorgulanabilir bir tipimiz olduğunu varsayalım.
// Veritabanında bir tablo ya da Xml dosyası gibi
public class MyQueryableType {
}

// Sorgulama işini yapacak sınıfımız
public class MyQueryProvider : IQueryProvider {
    private readonly MyQueryableType _source;

    public MyQueryProvider(MyQueryableType source) {
        _source = source;
    }

    // Yeni bir sorgu yaratıyoruz
    public IQueryable CreateQuery(Expression expression) {
        return (IQueryable)Activator.CreateInstance(typeof(MyQuery<>).MakeGenericType(expression.Type), expression);
    }

    public IQueryable<TElement> CreateQuery<TElement>(Expression expression) {
        return new MyQuery<TElement>(this, expression);
    }

    public object Execute(Expression expression) {
        // burada artık sorgumuzu "source" objesi üzerinde çalıştırabiliriz
        throw new NotImplementedException();
    }

    public TResult Execute<TResult>(Expression expression) {
        throw new NotImplementedException();
    }
}

// kendimize özel bir sorgu tipimiz
public class MyQuery<T> : IQueryable<T> {

    public MyQuery(MyQueryableType source) {
        Expression = Expression.Constant(this);
        Provider = new MyQueryProvider(source);
    }

    public MyQuery(MyQueryProvider queryProvider, Expression expression) {
        Expression = expression;
        Provider = queryProvider;
        Expression = Expression.Constant(this);
    }

    public Type ElementType => typeof(T);

    public Expression Expression { get; }

    public IQueryProvider Provider { get; }

    // Sorgumuza kendimize özel metod ekleyebiliriz.
    public MyQuery<T> FilterActive() {
        // Aynen Queryable.Where gibi yapılan çağrıyı temsil eden CallExpression oluşturuyoruz
        var newExpression = Expression.Call(
            null,
            typeof(MyQuery<T>).GetMethod("FilterActive")
        );

        return (MyQuery<T>)Provider.CreateQuery(newExpression);
    }

    public IEnumerator<T> GetEnumerator() {
        return (Provider.Execute<IEnumerable<T>>(Expression)).GetEnumerator();
    }

    IEnumerator IEnumerable.GetEnumerator() {
        return (Provider.Execute<System.Collections.IEnumerable>(Expression)).GetEnumerator();
    }
}
```

Yeni sorgu yapımızı da aşağıdaki gibi kullanabiliriz.

```csharp
// sorgulamak istediğimiz veri kaynağımız
var source = new MyQueryableType();
// sorgumuzu oluşturuyoruz
var query = new MyQuery(source);
// filtre, sıralama gibi çağrılar yapıp sorgumuzu çalıştırabiliriz
var result = query.Where(d => d.Id > 3).OrderBy(d => d.Name).ToList();
````

Biraz seramoni var ancak çok da zor bir iş değil. Daha önce uygulanmamış bir veri kaynağına Linq desteği eklemek istediğimizde yukarıdakine benzer bir yapı kurmamız gerekir.
Ancak bizim amacımız C# sorgulama sistemi yazmak değil. Buna benzer bir yapıyı JavaScript ile geliştirmek istiyoruz.

[Beşinci yazıda Jokenizer projesi ile JavaScript Expression'larını nasıl parse ettiğimizi göreceğiz](/javascript-linq-05), görüşmek üzere.

> “Walking on water and developing software from a specification are easy if both are frozen.” ― Edward V. Berard
