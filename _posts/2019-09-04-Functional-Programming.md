---
layout: post
title: Fonksiyonel Programlama
comments: true
redirect_from: "/2019/09/04/Functional-Programming/"
permalink: functional-programming
---

Geliştirme yaparken yazdığımız kodlar ile bilgisayara yapılması gereken işi anlatmaya çalışırız. En temelde bu işi iki farklı yaklaşım ile yapabiliriz, Imperative ve Declarative.

Imperative Programming işin **nasıl** yapılacağını, Declarative ise **ne** yapılacağını belirttiğimiz yaklaşımlardır.

![Declarative-Imperative](/assets/functional-comic.png)

<sup>kaynak: Sudip Bhandari (via [DevRant](https://devrant.com/rants/1541501/should-you-use-declarative-or-imperative-paradigm-d-programmingparadigms))<sup>

## Imperative Programming

Çoğunluğun alışkın olduğu ve tercih ettiği bu yaklaşımda yapılması gereken işin adım adım nasıl yapılması gerektiğini biz söyleriz, programlama dilimiz ise bu komutlarımızı büyük oranda birebir örtüşen makine dili komutlarına çevirir. Hemen ufak bir örnek görelim:

```javascript
const array = [1, 1, 2, 3, 5, 8, 13]
let sum = 0
for (let item of array) {
    sum += item
}
console.log(`sum of all numbers is ${sum}`)
```

Örnek programlama dilleri: C, C++, Java

## Declarative Programming

Bu yaklaşımda ise işin nasıl yapılacağından ziyade yapılmasını istediğimiz işi daha yüksek seviyede anlatırız. Böylece kodumuzu inceleyenler satır satır takip ederek algoritmayı anlamaya çalışmak yerine yapılan işi temsil etmekte kullanılan **fonksiyonları** konuşma dili gibi takip edebilirler.

Hemen bunun için de bir örnek görelim:

```javascript
const array = [1, 1, 2, 3, 5, 8, 13]
const sumFunc = array => array.reduce((a, b) => a + b)
console.log(`sum of all numbers is ${sumFunc(array)}`)
```

Örnek programlama dilleri: Prolog, SQL, HTML

Tamam, burada itirazlar gelebilir. İlk örnek için bir **Sum** fonksiyonu yazarak da benzer okunabilirlikte kod elde edebilirdik. Aslında konumuz fonksiyonel programlama ve fonksiyonel dillerin neredeyse hepsinde olan 3 adet büyülü fonksiyon ile bütün kodlarımızı ikinci örnektekine benzer basitlikte yazabiliyoruz (birazdan geleceğiz). Topluluk bu yaklaşıma **Declarative** diyor, bence kabul edilebilir.

Ancak gerçek bir declarative yaklaşım örneği görünce anlatılmak istenen tam oturacak:

```sql
select * from User
```

Kullanıcı tablosundaki bilgileri istiyoruz, ve bunun nasıl yapılması gerektiğine dair hiç kod yazmadan **açıklayıcı cümle** yazmamız yeterli oluyor.

## filter, map, reduce

Sihirli fonksiyonlarımız bu üçü, kısaca görelim (isimlerinden anlaşılıyordur gerçi).

**filter**: Bir diziyi belirli bir kıstasa göre daraltan ve yeni bir dizi dönen fonksiyon.

**map**: Bir dizideki tüm elemanlar üzerinde parametre ile gönderilen işlemden geçiren ve yeni bir liste dönen fonksiyon.

**reduce**: Dizideki tüm elemanları bir sonuç veri elde etme amacıyla gönderilen fonksiyondan geçirir. Yaptığı iş çoklu listeden tekil bir sonuç almak, yani toplam gibi.

Google'ın [MapReduce](https://static.googleusercontent.com/media/research.google.com/tr//archive/mapreduce-osdi04.pdf) yazısını okumanızı tavsiye ederim.

Tamamdır, daha fazla yaklaşımlar ile kafa karıştırmadan ilerleyelim.

## Fonksiyonel Programlama

Belki bazılarınız matematik denklemlerini sevmiyor bile olabilir ancak yazılım geliştirirken farkında olmadan matematik sanatını icra ediyoruz. Aşağıdaki matematiksel denklem yazımı tanıdık geliyor mu?

```math
f(x) = 2x + 1
```

Peki bu JavaScript kodu ile benzerliği?

```javascript
const f = x => 2*x + 1
```

### [Lambda Calculus](https://brilliant.org/wiki/lambda-calculus/)

Lambda Calculus, λ fonksiyonlarını içeren soyut bir matematiksel hesaplama teorisidir. Lamda matematiği, fonksiyonel programlamanın teorik temeli olarak düşünülebilir. [Turing Complete](https://www.youtube.com/watch?v=RPQD7-AOjMI) bir teoridir; başka bir deyişle, lambda calculus hesaplayabilen herhangi bir makine, bir [Turing makinesinin](https://www.youtube.com/watch?v=dNRDvLACg5Q) yapabileceği her şeyi hesaplayabilir.

Aşağıdaki verilen bir *x* değerinin karesini dönen lambda görülebilir.

```math
(λx.x^2)
```

Bir de **Haskell** gibi bir fonksiyonel dilde bu ifadenin nasıl yazıldığını görelim.

```haskell
(\ x -> x^2)
```

Araştırılması gereken konuları listeledikten sonra esas konumuza girelim artık.

---

Öncelikle kompleks, fazla katmanlı, çok satır sayılı kod yazmanın bir yetenek olmadığını, aksine zararlı bir alışkanlık olduğuna inandığmı söylemeliyim.

> All problems in computer science can be solved by another level of indirection, except for the problem of too many layers of indirection. – David J. Wheeler

OOP yaklaşımını tek doğru olarak benimsemek ve her soruna tam gaz OOP çözümler üretmeye çalışmak yıllar önce bıraktığım bir alışkanlık. Farklı bakış açılarını araştırmak, diğer programlama dilleri ile nasıl oluyor bu işler okumak gerekli, [The Pragmatic Programmer](https://www.idefix.com/ekitap/the-pragmatic-programmer-1) kitabında David Thomas and Andrew Hunt'ın söylediği gibi:

> Learn at least one new language every year. Different languages solve the same problems in different ways. By learning several different approaches, you can help broaden your thinking and avoid getter struck in a rut.

Kavga çıkarmaya çalışmıyorum, ancak aşağıdaki yazılara kesinlikle bir göz gezdirmelisiniz.

* [Goodbye, Object Oriented Programming](https://medium.com/@cscalfani/goodbye-object-oriented-programming-a59cda4c0e53)
* [Hacker News](https://news.ycombinator.com/item?id=10201688)
* [Object-Oriented Programming — The Trillion Dollar Disaster](https://medium.com/better-programming/object-oriented-programming-the-trillion-dollar-disaster-92a4b666c7c7)
* [Death by 1000 Layers](https://www.quantcast.com/blog/death-by-1000-layers-the-perils-of-over-abstraction-in-java/)

ve daha nice yazılarda OOP eleştirilerini görebilirsiniz, ben o kadar sert olmasa da OOP ve genel kod yazış şeklimizden hiç memnun değilim. Birazdan değineceğimiz *global state*, *mutability* her zaman zorlayabildiğimiz konular olmuyor, ben daha çok hibrit bir yaklaşımdan yanayım.

Yine de herkesin espri olarak gördüğü [Enterprise FizzBuzz](https://github.com/EnterpriseQualityCoding/FizzBuzzEnterpriseEdition) projesini bir çoğumuzun inceleyip arkadaşlarıyla paylaştıktan sonra benzer şekilde fazla karmaşıklaştırılmış projelerine döneceklerine eminim.

### First class functions

Ne demek bu değil mi? Kendimi bildim bileli "First Class" ve "Higher Order" tanımlarını sevemedim. Elimden geldiğince açıklayayım.

Bir programlama dili, fonksiyonlara bu dildeki diğer değişkenler gibi davranıyorsa First Class fonksiyonlara sahip olduğu söylenir. Görelim bakalım:

### Değişkenlere atanabilirler

```javascript
const f = (m) => console.log(m)
f('Test')
```

### Başka bir fonksiyona argüman olarak geçilebilirler

```javascript
const f = (m) => () => console.log(m)
const f2 = (f3) => f3()
f2(f('Test'))
```

### Fonksiyonların dönüş değeri olabilirler

```javascript
const createFunc = () => m => console.log(m)
const f = createFunc()
f('Test')
```

### Higher Order Fonksiyonlar

Bir fonksiyonu parametre olarak kabul eden ya da bir fonksiyon dönen fonksiyonlara denir.

JavaScript dilindeki **map**, **reduce** gibi fonksiyonları sayabiliriz.

```javascript
const arr1 = [1, 2, 3];
const arr2 = arr1.map(item => item * 2);
console.log(arr2);
```

### Immutability

OOP geçmişinden gelen yazılımcıları en zorlayan konu Immutability olmuştur hep. "Veri hiçbir zaman değişmez" anlamına geliyor. Tahminen ilk kez duyanlar çok şaşırıyordur, "nasıl olabilir"?

Dürüst olmak gerekirse fonksiyonel dil hayranlarının en sıkı olduğu bu konuya ben daha esnek bakıyorum, John Romero'ya sorulan "Fonksiyonel Programlama hakkında ne düşünüyorsunuz" sorusuna çok sevdiklerini ancak Doom gibi bir oyunu geliştirirken Immutability'nin işleri kolaylaştırmaktan çok uzatacağını ve yavaşlatacağını, bu yüzden "değiştirilebilir bir state" kavramından vazgeçmediklerini söylemişti ([The Early Days of id Software
](https://www.youtube.com/watch?v=E2MIpi8pIvY)). Ben de çok benzer bir düşüncedeyim, ancak sağdan soldan değiştirilebilen "state" konusunun yazılımcılar için en büyük kabus olduğuna da katılıyorum.

Peki bu konuda JavaScript gibi bir dilde ne yapabiliriz?

* Değişmeyecek tüm değişkenler **const** kullanın (ES2015 ile geldi)
* Objelerin üzerine yazmak yerine verileri kopyalayarak taşımayı tercih edin. **Object.assign**'da ES2015 ile eklendi.

```javascript
const redObj = { color: 'red' }
const yellowObj = Object.assign({}, redObj, {color: 'yellow'})
```

* **append** yerine **concat** kullanın. **append** elemanları varolan dizinin içine ekler ve değişmesine sebep olur. **concat** ise yeni bir dizi döner.

```javascript
const a = [1, 2]
const b = a.concat(3)
```

* **concat** yerine **spread** operatörü de kullanılabilir.

```javascript
const a = [1, 2]
const b = [...a, 3]
```

* Diziden eleman çıkarmak (**pop**, **splice** vs..) yerine **filter** ya da **slice** ile yeni bir dizi oluşturun.

### Purity

Tüm bu yazıdan, hatta fonksiyonel programlama yaklaşımından alınması gereken en önemli ders bence **Purity**. Bir fonksiyon kendisine gelen parametreler üzerinde değişiklik yapmaz. Pure fonksiyonlar kendi dışındaki hiçbir veriyi değiştirmez. Aynı parametreler ile çağırıldığında her zaman aynı çıktıyı üretirler. Düşünsenize, böyle fonksiyonlar için birim testi yazmak ne kadar kolay olur.

Bu konuda bir çift daha lafım var. Çok uzun süredir C# kullanan birisiyim, yeterince uzun süredir C# ile çalışan herkes bir property'nin ne kadar tehlikeli olabileceğini görmüştür. Aşağıdaki kodu yazmanıza kimse engel olmuyor:

```csharp
public class Company {

    private string _name;
    public string Name {
        get {
            Address = "";
            return _name;
        }
    }

    public string Address { get; set;}
}
```

Tam olarak böyle olmasa da benzer kodları hepimiz görmüşüzdür, işte bu **pure** olmayan olacaksa **pure evil** bir kod. Bu tür ihtimalleri görünce dil buna izin vermemeliydi diyor insan.

### Composition

İki fonksiyonu arka arkaya bağlama işine deniyor. Promise buna çok güzel bir örnek. Aşağıdaki gibi:

```javascript
getCompanies()
    .then(companies => companies.map(c => c.Address))
    .then(addresses => console.log(addresses))
```

Ya da daha önce bahsettiğimiz 3 büyülü fonksiyonu kullanalım:

```javascript
// şirketlerin yılda verdikleri ortalama maaş toplamını aldık, ne işe yarar bilmiyorum :)
companies.filter(c => c.active).map(c => c.avgSalary * 12).reduce((acc, s) => acc + s)
```

hatta JavaScript sınırını biraz aşıp, Array'e bağlı fonksiyonların direk erişilebilir olduğu bir ortamda:

```javascript
sum(map(filter(c, c.active), c => c.avgSalary * 12))(companies)
```

gibi yazabiliriz.

### Partial Application

Birden fazla parametre alan bir fonksiyonun bazı parametreleri hazırlanmış, ancak tüm parametreleri hazır olmayan, kısaca daha az parametre ile çağırabilir hale getirilmesine deniyor. Biraz açalım:

```javascript
const calcPerimeter = (a, b, c, d) => a + b + c + d
const calcRectangePerimeter = (a, b) => calcPerimeter(a, b, a, b)
```

Yukarıda bir dörtgen için çevre hesaplayan fonksiyonumuz 4 adet değişken alıyor, ancak dikdörtgen için hesaplayan özel bir versiyon ekledik ve sadece iki parametre bizim için yeterli.

### Currying

Curried fonksiyon (ne güzel isim değil mi?) çok parametre alan bir fonksiyonun tek parametre alır hale getirilmiş halidir. Yani bir parametre kalacak şekilde **Partial Application** yapılmış fonksiyondur diyebiliriz. JavaScript dünyasında genelde aşağıdaki gibi yazılırlar:

```javascript
const calc = discount => tax => credit => price => ((price - discount)*tax/100) - credit
```

Burada iç içe 4 adet fonksiyon var, kullanımı aşağıdaki gibi oluyor.

```javascript
const tax = 18
const employeeDiscount = calc(10)(18)
const managerDiscount = calc(30)(18)

const userCredit = 100
const price = 500
const lastPrice = employeeDiscount(userCredit)(price)
```

Şimdilik sadece yüzeyi biraz kazıdık, herkes kesinlikle fonksiyonel geliştirme yapmalı demiyorum, ancak hepimiz kesinlikle öğrenmeliyiz.
Daha fazlası için aşağıdaki kaynaklara göz atmanızı tavsiye ederim.

* [Mostly Adequate Guide](https://github.com/MostlyAdequate/mostly-adequate-guide)
* [Functional JavaScript: Introducing Functional Programming with Underscore.js](https://www.amazon.com/Functional-JavaScript-Introducing-Programming-Underscore-js-ebook/dp/B00D624AQO)
* [Functional Programming in JavaScript](https://www.manning.com/books/functional-programming-in-javascript)

Mutlu Kodlamalar.
