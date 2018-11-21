---
layout: post
title: ExpressionVisitor sınıfı
comments: true
redirect_from: "/2018/10/20/JavaScript-Linq-03/"
permalink: javascript-linq-03
---

JavaScript ile Linq yazı serimizin üçüncüsüne hoş geldiniz.

1. [Linq gerçekte nedir?](/javascript-linq-01)
2. [Expression'lar](/javascript-linq-02)
3. ExpressionVisitor sınıfı **(You are here)**
4. [IQueryable ve IQueryProvider](/javascript-linq-04)
5. [Jokenizer - JavaScript Expression'larını parse edelim](/javascript-linq-05)
6. [Jokenizer.Net - C# Expression'larını parse edelim](/javascript-linq-06)
7. [DynamicQueryable - Dinamik sorgu oluşturalım](/javascript-linq-07)
8. [Jinqu - JavaScript ile Linq](/javascript-linq-08)
9. [Linquest ve Linquest.AspNetCore - Asp.Net Core ile cevap verelim](/javascript-linq-09)

Bu yazıda ExpressionVisitor sınıfı ile bir Expression'ı modifiye edeceğiz.

# ExpressionVisitor sınıfı

Expression'lar immutable'dır (değiştirilemez). Yani bir kere oluşturduysak üzerlerinde değişiklik yapmamız mümkün olmaz.
Daha önce bahsetmiştik, Expression'lar bir ağaç şeklinde yazdığımız kodu **ifade** eder. ExpressionVisitor sınıfı ise bizim Expression ağacı üzerinde kökten başlayarak tüm düğümleri gezmemize ve bu düğümleri istediğimiz yeni düğümler ile değiştirebilmemize olanak sağlar. Expression ağacı aşağıdaki gibi ifade edilebilir.

![Expression Tree](https://image.ibb.co/d6VvQq/Expression-Tree.png)

Şimdi bu ağacı gezerek yazdığımız bir sorguyu modifye edeceğiz.

Aşağıdaki modeli ele alalım.

```csharp
public interface ISoftDeletable {
    bool IsActive { get; set; }
}

public class Company : ISoftDeletable {
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsActive { get; set; }
}
```

Elimizde Company sınıfı için aşağıdaki gibi bir sorgu olsun, gerçek hayatta bu büyük ihtimalle bir Entity Framework gibi bir ORM sorgusu olacaktır;

```csharp
var companies = new List<Company> {
    new Company { Id = 1, Name = "Netflix", IsActive = true },
    new Company { Id = 2, Name = "Netflow", IsActive = false },
    new Company { Id = 3, Name = "Google", IsActive = false },
    new Company { Id = 3, Name = "Microsoft", IsActive = true }
};

var query = companies.AsQueryable().Where(c => c.Name.StartsWith("Net"));
```

Sorguyu bu hali ile çalıştırdığımızda aşağıdaki sonucu alırız

![Sorgu Sonucu](https://image.ibb.co/iNJeJA/Query-Result.png)

Şimdi yapmak istediğimiz, sorgumuzun sadece aktif kayıtları getirmesini sağlamak.
Bunu yapabilmek için ExpressionVisitor sınıfından türeyen özelleştirilmiş bir Expression ziyaretçisi yazmak.

```csharp
public class CustomExpressionVisitor: ExpressionVisitor {

    // değiştirmemiz gereken LambdaExpression, yani Predicate
    protected override Expression VisitLambda<T>(Expression<T> node) {
        // okunabilirlik için ayrı ayrı kontrol ediyoruz

        // sadece 1 parametresi olmalı
        if (node.Parameters.Count != 1) return node;

        // parametre tipini kontrol ediyoruz
        if (!typeof(ISoftDeletable).IsAssignableFrom(node.Parameters[0].Type)) return node;

        // dönüş değeri bool olmalı, Predicate imzasını hatırlayın
        if (node.ReturnType != typeof(bool)) return node;

        // parametremiz, daha önce kontrol ettik ISoftDeleteable interface'i uygulanmış bir tip
        var prmExp = node.Parameters.Single();

        // bir karşılaştırma Expression'ı oluşturuyoruz: "c.IsActive == true"
        var isActiveExp = Expression.Equal(
            // IsActive özelliğine erişiyoruz
            Expression.PropertyOrField(prmExp, nameof(ISoftDeletable.IsActive)),
            Expression.Constant(true)
        );

        // yeni bir LambdaExpression oluşturuyoruz.
        // bu değeri döndüğümüzde ağaçtaki düğümü değiştirmiş oluyoruz.
        return Expression.Lambda(
            // fonksiyonun eski içeriğini yeni karşılaştırma işlemimiz ile And operatorü ile bağlıyoruz
            Expression.MakeBinary(
                ExpressionType.And,
                node.Body,
                isActiveExp
            ),
            prmExp
        );
    }
}
```

Yukarıdaki kodu incelediğinizde Expression ağacımız gezilirken LambdaExpression için yazdığımız kod çalıştırılıyor.
Eğer bu LambdaExpression aradığımız imzaya sahip ise yeni bir LambdaExpression oluşturarak eskisini değiştiriyoruz.

Yukarıdaki örnek için karşılaştığımız LambdaExpression aşağıdaki gibi

```csharp
c => c.Name.StartsWith("Net")
```

Yeni oluşturduğumuz LambdaExpression ise aşağıdaki gibi

```csharp
c => (c.Name.StartsWith("Net") && c.IsActive == true)
```

Şimdi bu yazdığımız CustomExpressionVisitor sınıfını nasıl kullanabileceğimizi görelim

```csharp
// Expression ağacını gezecek sınıfımızdan bir Instance oluşturuyoruz
var visitor = new CustomExpressionVisitor();
// kök Expression'ı geziyoruz ve yeni bir Expression oluşturuyoruz
// IQueryable konusuna bir sonraki yazıda değineceğiz
var newExp = visitor.Visit(query.Expression);
// yeni Expression için yeni bir sorgu oluşturuyoruz
// IQueryProvider konusuna da bir sonraki yazıda değineceğiz
var modifiedQuery = query = (IQueryable<Company>)query.Provider.CreateQuery(newExp);
// değiştirilmiş sorgunun sonucunu alıyoruz
var modifiedResult = modifiedQuery.ToList();
```

Değiştirilmiş sorgumuzun sonucunu da aşağıda görebilirsiniz

![Değiştirilmiş Sorgu Sonucu](https://image.ibb.co/eSqOBV/Modified-Query-Result.png)

Gördüğünüz gibi sadece ***IsActive*** değeri **true** olan kayıtları listeledik.

Konu üzerinde daha fazla çalıştığında örneğimizde atladığımız bazı kontroller olduğunu göreceksiniz, örneğin bir tanesi ISoftDeletable interface'inin Explicit bir şekilde implemente edilme durumu. Kod üzerinde çalışarak ve farklı sorgular için deneyerek iyileştirme yolları bulurken Expression konusuna çok daha iyi hakim olabilirsiniz.

[Dördüncü yazıda IQueryable ve IQueryProvider ile artık sorgulama işine el atacağız](/javascript-linq-04), görüşmek üzere.

> “Talk is cheap. Show me the code.” ― Linus Torvalds
