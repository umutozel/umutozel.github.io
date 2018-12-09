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

# ./types.ts

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

> TypeScript ile C#'da olduğu gibi generic parametreleri farklı aynı isimli tip oluşturamıyoruz. O yüzden farklı bir isim verdim.

**IQuery** tipimizi diziler ile kullanabilmek için **Array** tipine **IQuery** tipindeki metotları ekleyeceğiz. Ancak bu metotlardan bazıları zaten **Array** üzerinde bulunmakta.

```TypeScript
interface IQueryDuplicates<T> {
    concat(other: Array<T>): IQuery<T>;
    join<TOther, TResult = any, TKey = any>(other: Array<TOther>, thisKey: Func1<T, TKey>, otherKey: Func1<TOther, TKey>,
        selector: Func2<T, TOther, TResult>, ...scopes): IQuery<TResult>;
    reverse(): IQuery<T>;
}
```

Çakışan metotları ayrı bir tip üzerinde topladım, birazdan bunu neden yaptığımız daha iyi anlaşılacak.

Diğer metotları ise **IQuerySafe** interface'inde topluyoruz.

```TypeScript
export interface IQuerySafe<T> extends IQueryBase, Iterable<T> {
    aggregate<TAccumulate = number>(func: Func2<TAccumulate, T, TAccumulate>, seed?: TAccumulate, ...scopes): TAccumulate;
    aggregateAsync<TAccumulate = number>(func: Func2<TAccumulate, T, TAccumulate>, seed?: TAccumulate, ...scopes): PromiseLike<TAccumulate>;
    all(predicate: Predicate<T>, ...scopes): boolean;
    allAsync(predicate: Predicate<T>, ...scopes): PromiseLike<boolean>;
    any(predicate?: Predicate<T>, ...scopes): boolean;
    anyAsync(predicate?: Predicate<T>, ...scopes): PromiseLike<boolean>;
    // ... tamamı için github üzerindeki kodlara bakabilirsiniz

    toArray(): Array<T> & InlineCountInfo;
    toArrayAsync(): PromiseLike<Array<T> & InlineCountInfo>;
}
```

Son olarak **IQuery** tipimizi oluşturuyoruz.

```TypeScript
export type IQuery<T> = IQuerySafe<T> & IQueryDuplicates<T>;
```

**IQuery**, **IQuerySafe** ve **IQueryDuplicates** tiplerinin birleşiminden oluşuyor.

Linq ile çalışırken farketmişsinizdir, **ThenBy** metotunu kullanabilmek için öncelikle **OrderBy** ya da **OrderByDescending** çağrısı yapılmış olması gerekiyor. Aynı durumu JavaScript için aşağıdaki gibi oluşturabiliriz.

```TypeScript
export interface IOrderedQuery<T> extends IQuery<T> {
    thenBy(selector: Func1<T>, ...scopes): IOrderedQuery<T>;
    thenByDescending(keySelector: Func1<T>, ...scopes): IOrderedQuery<T>;
}
```

Bunu [C# nasıl yapmış](https://referencesource.microsoft.com/#system.core/System/Linq/IQueryable.cs,379) derseniz:

```csharp
public static IOrderedQueryable<TSource> OrderBy<TSource, TKey>(this IQueryable<TSource> source, Expression<Func<TSource, TKey>> keySelector, IComparer<TKey> comparer) {
    if (source == null)
        throw Error.ArgumentNull("source");
    if (keySelector == null)
        throw Error.ArgumentNull("keySelector");
    return (IOrderedQueryable<TSource>) source.Provider.CreateQuery<TSource>( 
        Expression.Call(
            null,
            GetMethodInfo(Queryable.OrderBy, source, keySelector, comparer),
            new Expression[] { source.Expression, Expression.Quote(keySelector), Expression.Constant(comparer, typeof(IComparer<TKey>)) }
            ));
}
```

**OrderBy** ve **OrderByDescending** metotları **IQueryable** yerine **IOrderedQueryable** tipini dönüyor.

```csharp
public static IOrderedQueryable<TSource> ThenBy<TSource, TKey>(this IOrderedQueryable<TSource> source, Expression<Func<TSource, TKey>> keySelector) {
            if (source == null)
                throw Error.ArgumentNull("source");
            if (keySelector == null)
                throw Error.ArgumentNull("keySelector");
            return (IOrderedQueryable<TSource>) source.Provider.CreateQuery<TSource>( 
                Expression.Call(
                    null,
                    GetMethodInfo(Queryable.ThenBy, source, keySelector),
                    new Expression[] { source.Expression, Expression.Quote(keySelector) }
                    ));
        }
```

**ThenBy** ve **ThenByDescending** extension metotlarını da **IOrderedQueryable** tipi için yazarak sadece önceden sıralanmış sorgular için **ThenBy** ve **ThenByDescending** çağrısı yapılabilir kılınıyor.

# ./queryable.ts

Interface tanımlarımızı gördükten sonra sorgu sınıfımızı yazma vakti. Bu dosyada tek bir sınıfımız olacak, **Query**. Tanım aşağıdaki gibi:

```TypeScript
export class Query<T = any> implements IOrderedQuery<T>, Iterable<T> {
        constructor(public readonly provider: IQueryProvider, public readonly parts: IQueryPart[] = []) {
    }
    // ...
}
```

Biraz önce tanımladığımız **IOrderedQuery** ve yeni JavaScript özelliği olan **Iterable** interface'lerini implemente edeceğiz. Constructor ise **IQueryBase** ile tanımladığımız **provider** ve **parts** bilgilerini parametre alıyor.

Birkaç metot ile implementation işini nasıl yaptığımızı görelim, tüm kod yine burada paylaşmak için çok uzun ama mantığı kavramamıza yetecektir.

```TypeScript

// senkron sonuç dönen metotlara bir örnek
// provider üzerinden varolan QueryPart'lara any part'ı eklenerek çağırılıyor
// part'ları birazdan detaylı göreceğiz
any(predicate?: Predicate<T>, ...scopes): boolean {
    return this.provider.execute([...this.parts, QueryPart.any(predicate, scopes)]);
}

// asenkron sonuç dönen metotlara bir örnek
// aynı işi provider'ın executeAsync metotunu çağırarak yapıyor
anyAsync(predicate?: Predicate<T>, ...scopes): PromiseLike<boolean> {
    return this.provider.executeAsync([...this.parts, QueryPart.any(predicate, scopes)]);
}

// metot zinciri yapan (yani sonuç değil yine sorgu dönen) bir örnek
// create metotunu where part'ı ile çağırıyor
where(predicate: Predicate<T>, ...scopes): IQuery<T> {
    return this.create(QueryPart.where(predicate, scopes));
}

// create yardımcı metotumuz
// sorgu üzerine yapılan her işlem yeni bir part olarak part listesinin sonuna ekleniyor
protected create<TResult = T>(part: IQueryPart): IQuery<TResult> {
    return <any>this.provider.createQuery([...this.parts, part]);
}

```

Bunlar bizim metotlarımız, **Iterable** için ise aşağıdaki sembol indexer'ını yazmamız gerekiyor. [Iterator ve Generator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators) konusunu kesinlikle okumanızı tavsiye ederim.

```TypeScript
// sorgumuzda şu ana kadar eklenmiş tüm part'ları senkron bir şekilde çalıştırıyoruz
// birazdan QueryProvider'ın nasıl çalıştığını incelediğimizde daha iyi anlaşılacaktır
[Symbol.iterator]() {
    return this.provider.execute<IterableIterator<T>>(this.parts);
}
```

Sorgu sınıfımızı şöyle özetleyebiliriz:

* Sonuç dönen metotlar (**first**, **all** vb..) provider üzerinden çalıştırılır
* Sorguyu genişleten metotlar (**where**, **orderBy** vb..) sorgunun part listesine kendilerini ekleyip yeni bir sorgu oluştururlar

Bir de bilmeyenler, bu üç nokta nedir diyenler için ufak bir açıklama yapalım.

## [Destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

Dizileri açmamızı sağlayan "**...**" yeni bir özellik.

Değişkenleri dağıtırken kullanabiliriz:

```JavaScript
const foo = ['one', 'two', 'three'];
const [one, two, three] = foo;

console.log(one); // "one"
console.log(two); // "two"
console.log(three); // "three"
```

Yukarıda 3 değişkene sırayla değer atadık.

Bizim kullanımımız ise aşağıdaki gibi:

```TypeScript
[...this.parts, part]
```

**parts** dizisindeki tüm içeriğin sonuna **part** değişkeni eklenmiş yeni bir dizi oluşturduk.

[Dokuzuncu ve son yazıda linquest ile sunucu üzerinde Linq çalıştıracağız](/javascript-linq-09), görüşmek üzere.

> The best performance improvement is the transition from the nonworking state to the working state. - J. Osterhout
