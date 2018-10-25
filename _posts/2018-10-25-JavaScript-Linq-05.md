---
layout: post
title: IQueryable ve IQueryProvider
comments: true
redirect_from: "/2018/10/22/JavaScript-Linq-04/"
permalink: javascript-linq-04
---

JavaScript ile Linq yazı serimizin beşincisine hoş geldiniz.

1. [Linq gerçekte nedir?](/javascript-linq-01)
2. [Expression'lar](/javascript-linq-02)
3. [ExpressionVisitor sınıfı](/javascript-linq-03)
4. [IQueryable ve IQueryProvider](/javascript-linq-03)
5. Jokenizer - JavaScript Expression'larını parse edelim  **(You are here)**
6. [Jokenizer.Net - C# Expression'larını parse edelim](/javascript-linq-06)
7. [DynamicQueryable ile Jokenizer.Net dinamik sorgu oluşturalım](/javascript-linq-07)
8. [Jinqu - JavaScript ile Linq](/javascript-linq-08)
9. [Linquest ve Linquest.AspNetCore - Asp.Net Core ile cevap verelim](/javascript-linq-09)

Bu yazıda [jokenizer](https://github.com/umutozel/jokenizer) projesi ile JavaScript Expression'ları parse edeceğiz.
Artık yazılar teorik anlatımlardan sert bir dönüş ile geliştirmeye yönelecek, kollar sıvayalım.

Expression nedir öğrendik, peki JavaScript için durum nedir? Zor. JavaScript tip sistemsiz (arkadaşlarla tipsiz de deriz) bir dil olduğu için Expression gibi bir sınıfa sahip değil, Reflection gibi bir yapı da yok haliyle. Şöyle ki:

```JavaScript
// sum isminde bir Lambda tanımlıyoruz
const sum = (a, b) => a + b;
console.log(typeof sum);    // "function"
```

Gördüğünüz gibi, çıktımız **Func<int, int>** gibi bir tip değil. JavaScript tip bilgisi istediğimizde bize her zaman string değer döner.
Bu durum, tip güvenlikli bir dil ile tecrübesi olanların JavaScript ile ilgili en büyük yanılgılarıdır. Ben mümkün olan her projemde TypeScript kullanarak en azından geliştirme aşamasında tip güvenliğini sağlamaya çalışıyorum.

## TypeScript

[TypeScript](http://typescriptlang.org) Microsoft tarafından geliştirilen, başında Turbo Pascal ve C# dillerindeki emeğinden tanıdığımız (adını kopyalamadan yazamadığım) Anders Hejlsberg var. TypeScript bir JavaScript süperset'i. Yani JavaScript'i kapsıyor gibi düşünebilirsiniz. Kendisi de TypeScript ile yazılmış Compiler'ı bizim için JavaScript üretiyor. En çok karşılaştığım yanılgı TypeScript ile geliştirdiğimiz **interface**'ler ve **type**'lara çalışma zamanı ulaşabileceğimizi zannetmek. Üretilen koda bakarsanız karşılık olarak bir JavaScript çıktısı üretmediklerini görebilirsiniz, çünkü JavaScript'te **interface** ve **type** kavramı yok!

> "I love Typescript. It is the best thing. It is very pragmatic and well done and approachable." - Ryan Dahl, creator of Node.js

#### Proje Şablonu

Yeni bir JavaScript projesine başlamak biraz uğraş gerektiriyor, projelerime başlangıç şablonu olması amacıyla geliştirdiğim [npm-typescript-starter](https://github.com/umutozel/generator-npm-typescript-starter) isimli [yeoman](http://yeoman.io/) oluşturucusunu kullanabilirsiniz.

İş başa düştü, Expression yapısını kendimizin geliştirmesi gerekiyor.

# Proje Yapısı

Acelesi olanlar projenin bitmiş halini https://github.com/umutozel/jokenizer adresinden inceleyebilir.

![Proje Yapısı](/assets/jokenizer-structure.png)

* **.github** GitHub özel şablonlarımız burada. Issue Template, Pull Request Template gibi
* **.vscode** Visual Studio Code ayarlarımız. Ben sadece [Mocha Sidebar](https://marketplace.visualstudio.com/items?itemName=maty.vscode-mocha-sidebar) ayarlarımı tutuyorum. Bu eklentiyi de şiddetle tavsiye ederim.
* **lib** Tüm geliştirmeleri burada yapıyoruz.
* **test** Unit testlerimiz burada. Araç seti olarak Mocha, Chai, istanbul.js ve nyc kullanıyorum. Alışkanlık oldu artık.
* **.travis.yml** CI/CD için [Travis](https://travis-ci.org) kullanıyorum, herkese tavsiye ederim.
* **mocha.opts** Test çağrılarında uzun uzun TypeScript ayarları yapmamak için ayarları dosyadan okutuyorum.

## ./lib/types.ts

Yukarıda da dediğimiz gibi JavaScript ile Expression tipleri olmadığından biz tanımlıyoruz.


```JavaScript
// İlk tanımımız ExpressionType, hatırlarsınız C# için 80+ adet vardı, desteklediğimiz tüm Expression türleri aşağıdakiler:
export const enum ExpressionType {
    Literal = 'Literal',    // 42, "Marvin"
    Variable = 'Variable',  // a + 42, buradaki "a" bir değişken
    Unary = 'Unary',        // -42, buradaki "-" bir Unary işlem
    Group = 'Group',        // (a, b), direk kullanımı yasak olsa da Lambda için destek vermemiz gerekiyor
    Assign = 'Assign',      // a: 42, direk kullanımı yasak olsa da obje oluştururken destek vermemiz gerekiyor
    Object = 'Object',      // { a: 42 }
    Array = 'Array',        // [42]
    Member = 'Member',      // a.b, buradaki "b" bir Member, a ise Variable
    Indexer = 'Indexer',    // a[4]
    Func = 'Func',          // (a, b) => a + b
    Call = 'Call',          // func(1, 2)
    Ternary = 'Ternary',    // a ? 1 : 0
    Binary = 'Binary'       // a + b, a < b
}

// Sadece Expression tipini tutan bir yapı
export interface Expression {
    readonly type: ExpressionType;
}

// Tüm Expression'ları listelemeyeceğim. Bir kaç örnek görmemiz yeterli olacaktır

export interface LiteralExpression extends Expression {
    readonly value    // tipi "any", yani her türlü veri atanabilir
}

export interface VariableExpression extends Expression {
    readonly name: string;
}

export interface UnaryExpression extends Expression {
    readonly operator: string;      // yukarıdaki örnek için "-"
    readonly target: Expression;    // yukarıdaki örnek için LiteralExpression ancak her tür Expression olabilir
}

// son örnek için en güzel tercih
export interface BinaryExpression extends Expression {
    readonly operator: string;      // yaptığımız işlem, +, -, <, > gibi.
    readonly left: Expression;      // işleme dahil olan sol Expression
    readonly right: Expression;     // işleme dahil olan sağ Expression, yine her tür Expression olabilir
}
...
```

## ./lib/tokenizer.ts

Bu dosyamızda Expression parse işlemini yapıyoruz. Kullanıma açtığımız tek metodumuz aşağıdaki gibi:

```JavaScript
export function tokenize<T extends Expression = Expression>(exp: string): T
```

Bir **string** parametre alıyor ve Expression tipinde obje dönüyor. Hemen bir örnek ile görelim:

```JavaScript
const lambda = tokenize<FuncExpression>('(a, b) => a < b');
```

lambda değeri aşağıdaki gibi çok basit bir obje:

```JSON
{
    "type": "Func",     // ExpressionType.Func
    "parameters": ["a", "b"],
    "body": {
        "type": "Binary",   // ExpressionType.Binary
        "operator": "<",
        "left": {
            "type": "Variable",     // ExpressionType.Variable
            "name": "a"
        },
        "right": {
            "type": "Variable",     // ExpressionType.Variable
            "name": "b"
        }
    }
}

```

Peki C#'ta olduğu gibi Expression<Func..> desteği olmayan bir dilde bir fonksiyonu nasıl parse edilebilir hale getiriyoruz?

```JavaScript
const lambda = (a, b) => a < b;
console.log(lambda.toString());     // "(a, b) => a < b"

const func = function (a, b) { return a < b; }
console.log(func.toString())        // "function (a, b) { return a < b; }"
```

Gördüğünüz gibi, JavaScript bize bu konuda yardımcı oluyor, **toString** çağrısı fonksiyonun kodunu dönüyor.

## Parser

Amacım çok basit olduğundan [Ejderhalı Kitap](https://www.amazon.com/Compilers-Principles-Techniques-Tools-2nd/dp/0321486811) gibi kaynakların yöntemlerinden farklı kendimce bir yol izledim, ancak mantık aynı.

Bu tür ifadeleri parse edebilmek için [Scanner](https://medium.com/dailyjs/gentle-introduction-into-compilers-part-1-lexical-analysis-and-scanner-733246be6738) denilen bir yapıya ihtiyaç duyarız. Scanner bizim parametre aldığımız string üzerinde gezmemizi sağlar. Ben Scan işini JavaScript [Closure](https://medium.freecodecamp.org/javascript-closures-simplified-d0d23fa06ba4) ile kapsadığım bir fonksiyon içinde hallettim.

---

Bazı önemli fonksiyonlar ile nasıl yaklaşık 450 satır kod ile bu işi nasıl yaptığımı görelim.

### move(count: number = 1)

Bu fonksiyon **cursor** değerini verilen parametre kadar ileri taşıyor. Cursor dediğimiz parametre gelen string üzerinde gezen Scanner yapımız.

### get(s: string)

Cursor bulunduğu noktada aranan değer var ise Cursor aranan değer kadar ilerler ve **true** döner, yok ise **false** döner.

### skip()

Boşluk, tab gibi bir anlam ifade etmeyen karakterleri (Whitespace) atlar.

### eq(idx: number, target: string)

Gönderilen indeks değerinden aranan değer olup olmadığını döner, Cursor ilerletmez.

### to(c: string)

Boşluklar atlandığında gelinen karakter dizisi gönderilen değere eşit değil ise hata fırlatır, eşit ise Cursor'ı ilerletir. Örnek:

```JavaScript
// parse etmek istediğimiz ifade
str = "function (a, b) { return a + b };"

// praser "function" ifadesi ile karşılaşınca artık bir anonim fonksiyon parse etme moduna girer
// to('/') çağrısı ile "function" ifadesinden sonra parantez açılan noktaya gider, gidemez ise hata oluşur
```

### isSpace()

Cursor'ın gösterdiği karakterin Whitespace olup olmadığını döner

### isNumber()

Cursor'ın gösterdiği karakterin bir sayı olup olmadığını döner

### isVariableStart()

Cursor'ın gösterdiği karakterin geçerli bir JavaScript değişkeni ilk karakteri olup olmadığını döner.
JavaScript değişkenleri sayılar ile başlayamaz ancak devam edebilir, a123 gibi.

### stillVariable()

Cursor'ın gösterdiği karakterin geçerli bir JavaScript değişken karakteri olup olmadığını döner.

### fixPrecedence(left: Expression, leftOp: string, right: BinaryExpression)

Tüm dillerde yapılan Binary işlemler için [öncelik sırası](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence) vardır.
Aşağıdaki gibi bir işlemi yorumlayalım:

```JavaScript
4 + 2*5 - 1
```

eğer öncelik düzeltmesi yapmazsak bu ifade aşağıdaki gibi yorulanır:

```JavaScript
(((4 + 2)*5) - 1)     // 29
```

düzeltme sonucu ise aşağıdaki gibi olur:

```JavaScript
(4 + (2*5) - 1)     // 13 - olması gerektiği gibi
```

---

Yardımcı metodlarımız bu kadardı. Şimdi esas işi yapan metodumuza bir bakalım, basitliği sizi şaşırtacak diye umuyorum.

```JavaScript
function getExp(): Expression {
    skip();     // boşlukları atla

    // sırayla tek başına kullanılabilen Expression'ları dene
    let e: Expression = tryLiteral()
        || tryVariable()
        || tryUnary()
        || tryGroup()
        || tryObject()
        || tryArray();

    // eğer bir Expression bulamazsan dön
    if (!e) return e;
    // Expression bilinen (true, false, null gibi) bir değer mi?
    e = tryKnown(e) || e;

    let r: Expression;
    do {
        // boşlukları atla
        skip();

        r = e;
        // birleşik kullanılan Expression'ları dene
        e = tryMember(e)
            || tryIndexer(e)
            || tryFunc(e)
            || tryCall(e)
            || tryTernary(e)
            || tryBinary(e);
    } while (e)

    return r;
}
```

Algoritmamız deneme üzerine kurulu. Önce tek başına kullanılabilen Expression'ları deniyoruz, sonra bunu birleşik Expression oluşturmada kullanıyoruz. Örneğin:

```JavaScript
a + 42
```

İfadesini parse ederken ilk kısımda ***a*** değeri **VariableExpression** olarak parse edilir.
Sonra sırayla **Member**, **Indexer**, **Func**, **Call**, **Ternary** olarak yorumlanmaya çalışılır ve bunların hepsi başarısız döner.
En son **Binary** olarak yorumlamak istediğinde "+" operatorü ile karşılaştığı için [recursive](https://www.geeksforgeeks.org/recursion/) çağırılan **getExp** metodumuz sağ ifadeyi **42** **LiteralExpression**'ı olarak parse eder.

Peki bu deneme metodları nasıl? Bir örnek görelim:

```JavaScript
function tryObject() {
    // "{" ile başlaması gerekiyor, yoksa başarısız
    if (!get('{')) return null;

    // Obje içinde yapılan tüm atamaları burada saklayacğız
    const es: AssignExpression[] = [];
    do {
        skip();
        // sıradaki Expression'ı oku
        const ve = getExp() as VariableExpression;
        // eğer bir değişken ya da üye erişimi değil ise hata fırlat
        // { a: 42, b.c } desteklediğimiz yapı
        if (ve.type !== ExpressionType.Variable && ve.type !== ExpressionType.Member)
            throw new Error(`Invalid assignment at ${idx}`);

        skip();
        // atama karakteri var ise
        if (get(':')) {
            // artık değişken olmak zorunda
            // { b.c: 42 } geçersiz bir ifade
            if (ve.type !== ExpressionType.Variable)
                throw new Error(`Invalid assignment at ${idx}`);

            skip();

            es.push(assignExp(ve.name, getExp()));
        }
        else {
            es.push(assignExp(ve.name, ve));
        }
    // her atama arasında "," olmalı, eğer yok ise işlem tamamlandı demektir
    } while (get(','));

    // son karakterimiz "}" bulunamaz ise hata fırlatılır
    to('}');

    // objemiz hazır
    return objectExp(es);
}
```

Diğer Expression'lar da buna benzer bir yapıda, kodu okuyarak çok rahat anlayabilirsiniz.

# ./lib/ExpressionVisitor.ts

Expression'larımız C#'ta olduğu gibi bir ağaç şeklinde. Dolayısıyla parse ettiğimiz bir Expression'ı çalıştırabilmek için bizim de bu ağacı recursive bir şekilde gezecek bir yapıya ihtiyacımız var, o da ExpressionVisitor. Nasıl çalıştığına yine ufak bir kod üzerinden bakalım:

```csharp
protected visit(exp: Expression, scopes: any[]) {
    switch (exp.type) {
        // desteklenen her Expression'ı ziyaret ediyoruz
        case ExpressionType.Array: return this.visitArray(<any>exp, scopes);
        case ExpressionType.Binary: return this.visitBinary(<any>exp, scopes);
        case ExpressionType.Call: return this.visitCall(<any>exp, scopes);
        case ExpressionType.Indexer: return this.visitIndexer(<any>exp, scopes);
        case ExpressionType.Literal: return this.visitLiteral(<any>exp, scopes);
        case ExpressionType.Member: return this.visitMember(<any>exp, scopes);
        case ExpressionType.Object: return this.visitObject(<any>exp, scopes);
        case ExpressionType.Ternary: return this.visitTernary(<any>exp, scopes);
        case ExpressionType.Unary: return this.visitUnary(<any>exp, scopes);
        case ExpressionType.Variable: return this.visitVariable(<any>exp, scopes);
        case ExpressionType.Group:
            const gexp = exp as GroupExpression;
            if (gexp.expressions.length == 1)
                return this.visit(gexp.expressions[0], scopes);
        // kendi başına Assign kullanımı desteklenmiyor. sadece Object Expression içinde olabilir
        case ExpressionType.Assign:
        // kendi başına Func kullanımı desteklenmiyor, sadece Call için parametre olabilir
        case ExpressionType.Func:
            throw new Error(`Invalid ${exp.type} expression usage`);
        default: throw new Error(`Unsupported ExpressionType ${exp.type}`);
    }
}

// örnek olarak Call expression'a bakalım
protected visitCall(exp: CallExpression, scopes: any[]) {
    // fonksiyonu temsil eden Expression'ı ziyaret ediyor ve değerini buluyoruz
    const func = this.visit(exp.callee, scopes);
    // argümanları ziyaret ediyor ve değerlerini buluyoruz
    // Func kullanımına sadece Call içinde izin veriyoruz
    // örnek: items.sum(i => i)     -> buradaki i => i bir Func ve sadece Call içinde parametre olabilir
    const args = exp.args.map(a => a.type === ExpressionType.Func ? this.visitFunc(<any>a, scopes) : this.visit(a, scopes));
    // foknsiyonumuzu çağırıyoruz
    return func(...args);
}
...
```

Yazmadığımız diğer Expression tipleri için yazılması gereken kodu tahmin edebilirsiniz ya da kodları inceleyebilirsiniz.
Burada önemli bir nokta **scopes** parametreleri, Expression parse ettiğimizde Variable değerlerini dışarıdan beslememiz gerekiyor.

```JavaScript
a + 42
```

Yukarıdaki gibi bir Expression'ı parse ettiğimizde **a** değişkeninin değerini bilmiyoruz ve bu Expression'ı çalıştırmak istediğimizde bu değeri bizim sağlamamız gerekiyor. ExpressionVisitor Expression çalıştırma için ihtiyacımız olan altyapıyı sağladı, ancak çalıştırma işleminde bu sınıfı direk kullanmayacağız.

# .lib/evaluator.ts

Tek bir fonksiyonu kullanıma sunduğumuz bu dosyanın içeriği çok basit.

```JavaScript
export function evaluate(exp: Expression | string, ...scopes: any[]) {
    return new ExpressionVisitor().process(typeof exp === 'string' ? tokenize(exp) : exp, scopes);
}
```

Çalıştırmak istediğimiz Expression'ı **string** ya da parse edilmiş **Expression** olarak gönderiyoruz ve gerekli **scope**'ları sağlıyoruz ve bize sonucu dönyor. Hemen bir örnek yapalım:

```JavaScript
// değerlendirilmesini istediğimiz karşılaştırma ifademiz
// ikinci parametrede verdiğimiz obje "v1" ve "v2" değişkenlerini aramak için kullanılacak
const value = evaluate('v1 >= v2', { v1: 5, v2: 3 });
console.log(value);     // true

// fonksiyon parse ediyoruz, fonksiyon gerekli parametreleri aldığı için scope ihtiyacı duymuyoruz
const func = evaluate('function(a, b) { return a < b; }');
console.log(func(5, 3));    // true

// istersek fonksiyon yerine lambda yazımını da kullanabiliriz
const lambda = evaluate(tokenize('(a, b) => a < b'));
console.log(func(5, 3));    // true
```

Sonunda JavaScript'e Expression ve ExpressionVisitor desteği ekledik. Böylece string kodumuzu temsil eden Expression'ları elde edebileceğimiz gibi **scope** sağlayarak çalıştırabiliyoruz da.

[Altıncı yazıda Jokenizer.Net projesi ile C# ifadelerini parse edeceğiz](/javascript-linq-06), görüşmek üzere.

> “Walking on water and developing software from a specification are easy if both are frozen.” ― Edward V. Berard
