---
layout: post
title: C# ile Garbage Collection baskısını azaltalım
comments: true
redirect_from: "/2020/06/28/csharp-gc-pressure/"
permalink: csharp-gc-pressure
---

Bir .Net uygulamasında belleğin yönetimi ve performans arasında çok sıkı bir ilişki vardır, belleği kötü çalışma uygulamanın çalışmasına farklı şekillerde kötü etki edebilir. Buna GC ya da Bellek baskısı (garbage collection pressure) diyebiliriz. Gelişmiş ülkelerde nasıl çöplerini geri dönüşüme uygun bir şekilde ayırıyorlarsa bizim de belleğimizi düzgün yönetmemiz gerekiyor.

GC Pressure, GC belleği yeterince hızlı boşaltamadığında yaşanıyor. Bu baskı oluştuğunda, bellek boşaltmak için harcanan zaman ve bu işlemin sıklığı çok artar.

Garbage Collection hakkında hızlıca bilgi edinmek için [şu yazıya](https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals) bakabilirsiniz.

Peki neler yapabiliriz?

## 1 - Dinamik koleksiyonlara kapasite vermek

.Net Framework bize çok yetenekli ```List, Dictionary, HashSet``` gibi birçok koleksiyon tipi sağlıyor. Bu tiplerin hepsi dinamik kapasiteye sahip, gerektiği durumlarda büyüyüp küçülebiliyorlar. 

Bu yaklaşımda sık kullanılan bir taktik ikiye katlamadır, kapasite dolduğunda varolan eleman sayısı kadar bellek ile kapasite büyütülür. Örneğin bir ```List```'e ilk elemanı eklediğinizde kapasitesi 4 olacaktır, 4. elemanı eklediğinizde ise kapasite 8 yapılacak vb.. 

Böylece 1000 elamanı bir listeye tek tek eklediğinizde ne kadar sık ekstra bellek isteceğini ve her bellek istemesinin fragmantasyon gibi ek maliyetlere sebep olabileceğini düşündüğünüzde gereksiz çok vakit harcandığını tahmin edersiniz.

```csharp
[Benchmark]
public void Dynamic()
{
    var list = new List<int>();
    for (var i = 0; i < 1000; i++)
    {
        list.Add(i);
    }
}

[Benchmark]
public void Planned()
{
    var list = new List<int>(1000);
    for (var i = 0; i < 1000; i++)
    {
        list.Add(i);
    }
}
```

Yukarıdaki iki örnek için alacağımız sonuçlar aşağıdaki gibi olacak:

 
| Method  | Mean     | Error     | StdDev    |
|---------|---------:|----------:|----------:|
| Dynamic | 3.415 us | 0.0687 us | 0.1240 us |
| Planned | 2.422 us | 0.0219 us | 0.0183 us |

<sub>Bu arada benim gibi MarkDown tablosu oluşturmaya bile üşeniyorsanız [bu siteyi kullanın](https://www.tablesgenerator.com/markdown_tables)</sub>

Tabi kapasiteyi önceden ayarlayabilmek için bu değeri bilmemiz gerekiyor :)

## 2 - ArrayPool kullanınmı

Yeni oluşturduğumuz her dizi ileride çöp toplayıcıya bir ek iş çıkaracaktır. Fazla uzun ömürlü olmayan diziler için, nedense yazılımcılar arasında çok da bilinmeyen ve temeli [bir tasarım şablonuna dayanan](https://en.wikipedia.org/wiki/Object_pool_pattern) ```ArrayPool``` kullanarak oluşturduğumuz yükü azaltabiliriz.

Programlama dillerinde serileştirme, şifreleme gibi işlemler yaparken bol bol bayt dizilerini oluşturup sağa sola parametre yollamamız gerekir. Bu dizilerde genelde kısa süre sonra gerekli dönüşümü yaşadıktan sonra kullanım dışı kalırlar ve GC tarafından toplanmak üzere bellekte yer işgal ederler.

Bu tür durumlarda ```System.Buffers.ArrayPool``` sınıfını kullanarak (genelde daha çok bilinen ```ThreadPool``` ile yaptığımız gibi) işimiz bitince geri teslim etmek üzere bellek kiralayabiliriz.

```csharp
[Benchmark]
public void RegularArray() {
    var array = new int[1000];
}
 
[Benchmark]
public void SharedArray() {
    var array = ArrayPool<int>.Shared.Rent(1000);
    // işimiz bitince diziyi iade ediyoruz
    pool.Return(array);
}
```

1000 elemanlık bir liste için aşağıdaki gibi bir performans alıyoruz:

| Method       |      Mean |    Error |    StdDev |
|--------------|----------:|---------:|----------:|
| RegularArray | 404.53 ns | 8.074 ns | 18.872 ns |
| SharedArray  |  51.71 ns | 1.354 ns |  1.505 ns |

## 3 - Class yerine Struct (aman dikkat, sadece doğru yerlerde)

```Struct```'lar çok tartışmalı bir konu, çoğunuzdan bir iş görüşmesinde sınıflarla arasındaki farkı cevaplamanız istenmiş olabilir (ben bile istemiş olabilirim, belki yüz kere sordum bu soruyu :)) Önemli özelliklerini sayalım:

* Bir sınıfın parçası olmadıklarında sadece Stack üzerinde yer alırlar, dolayısıyla GC gerektirmez
* Bir sınıf içinde olduklarında da bellekte takip edilmesi gereken ayrı bir obje olmazlar, veri olarak sınıf için ayrılan bellekte yaşarlar
* Bazı ileri seviye sebeplerden (MethodTable ve ObjectHeader olmaması gibi, [CLR via C#](https://www.amazon.com/CLR-via-4th-Developer-Reference/dp/0735667454) okumanızı şiddetle tavsiye ederim) daha az bellek harcarlar

En önemli nokta ```Struct``` referans değil veri olarak taşınır, yani her atama yaptığınızda yeni bir kopyası oluşur. Dolayısıyla yeni kopya üzerindeki bir alanı değiştirmek eski ```Struct```'ı etkilemez. O yüzden Microsoft bize aşağıdaki kurallar sağlanıyorsa ```Struct``` kullanmamızı tavsiye eder.

* Eğer boyut 16 bayt ya da daha küçükse (kopyalama maliyeti çok artmasın)
* Kısa ömürlü ise
* Değiştirilemez (immutable) ise. Biraz önce açıkladığım sorunu kökten çözmek için
* Sık sık kutulanmayacaksa (ahahah bazen bilerek zorla Türkçe yazıyorum, burada kastedilen [boxing](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/types/boxing-and-unboxing))

Doğru kullanıldığında nasıl etkisi oluyormuş görelim:

```csharp
class VectorClass {
    public int X { get; set; }
    public int Y { get; set; }
}
 
struct VectorStruct {
    public int X { get; set; }
    public int Y { get; set; }
}
 
[Benchmark]
public void WithClass() {
    var vectors = new VectorClass[1000];
    for (int i = 0; i < 1000; i++) {
        // her seferinde Heap üzerinden bellek alınıyor
        vectors[i] = new VectorClass();
        vectors[i].X = 5;
        vectors[i].Y = 10;
    }
}
 
[Benchmark]
public void WithStruct() {
    // bu aşamada tüm bellek hazır, tekrar yer istenmiyor
    var vectors = new VectorStruct[1000];
    for (int i = 0; i < ITEMS; i++) {
        vectors[i].X = 5;
        vectors[i].Y = 10;
    }
}
```

| Method     |     Mean |     Error |    StdDev |
|------------|---------:|----------:|----------:|
| WithClass  | 77.97 us | 1.5528 us | 2.6785 us |
| WithStruct | 12.97 us | 0.2564 us | 0.6094 us |

## 4 - Sonlandırıcı (Finalizer) kullanmaktan kaçının

[Finalizer](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/destructors) bir Sylvester Stallone filmi değil (eminim, şimdi kontrol ettim çünkü ben de şüphelendim :)). Çoğunlukla kullanmamız gerekmeyen, GC tam bizim sınıfımızı temizlerken araya girip kendi temizliğimizi yapmamıza izin veren bir sistem. Evdeki bilgisayarınız her temizlenmek üzereyken size haber verip tarayıcı geçmişinizi silmenize izin veren bir yapı gibi düşünebilirsiniz 🙃

Ancak aşağıdaki sebeplerden biraz maliyetli:

* Finalizer içeren her sınıf bir nesil atlatılır ([yukarıdaki linki](https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals) hala okumadınız mı?). Bu da en hızlı temizlik yapılan Gen 0 ile toplanamayacakları anlamına gelir.
* Bir de bu sınıflar Finalizer kuyruğuna eklenir. Kuyrukların ne kadar zaman aldığını ülkemizdeki tecrübelerimizden biliyoruz. Dedike edilmiş bir Thread tarafından temizlenir (bankada tek vezne çalışması gibi, bu benzetmeler umarım işe yarıyordur 😊). Bu durum da uzun çalışan Finalizer'ların hata fırlatmasına sebep olabilir.

Oynatalım Uğurcum:

```csharp
class Simple {
    public int X { get; set; }
}
 
class SimpleWithFinalizer {

    ~SimpleWithFinalizer() {
    }

    public int X { get; set; }
}
 
private static Simple _instance1;
private static SimpleWithFinalizer _instance2;
 
[Benchmark]
public void AllocateSimple() {
    for (int i = 0; i < 100000; i++) {
        _instance1 = new Simple();
    }
}
 
[Benchmark]
public void AllocateSimpleWithFinalizer() {
    for (int i = 0; i < 100000; i++) {
        _instance2 = new SimpleWithFinalizer();
    }
}
```

| Method                      |         Mean |        Error |      StdDev |
|-----------------------------|-------------:|-------------:|------------:|
| AllocateSimple              |     409.9 us |     9.063 us |    17.24 us |
| AllocateSimpleWithFinalizer | 128,796.8 us | 2,520.871 us | 2,588.75 us |

Bazen Finalizer kaçınılmaz olabiliyor [Dispose Pattern](https://en.wikipedia.org/wiki/Dispose_pattern), bu durumlarda temizliği kendimiz yaparak kapıya "Odamı Temizleme" kağıdı asmamız gerekiyor.

![No Clean Up](https://image.shutterstock.com/image-photo/no-clean-today-sign-japanese-600w-1135383716.jpg)

```csharp
public void Dispose() {
	Dispose(true);
	GC.SuppressFinalize(this); // finalizer çağırılmasın
}
```

## 5 - StackAlloc ile kısa ömürlü diziler

StackAlloc ile Heap'e bulaşmadan Stack üzerinde, dolayısıyla GC baskısı yaratmadan bellek ayırabiliyoruz (çok da hızlı oluyor). Ancak sınıfları (yani genel olarak referans tipleri) kullanamıyoruz, ilkel değerler ve struct'lar (yani değer tipleri) kullanabiliyoruz.

```csharp
struct VectorStruct {
    public int X { get; set; }
    public int Y { get; set; }
}
 
[Benchmark]
public void WithNew() {
    var vectors = new VectorStruct[5];
    for (var i = 0; i < 5; i++) {
        vectors[i].X = 5;
        vectors[i].Y = 10;
    }
}
 
[Benchmark]
public unsafe void WithStackAlloc() {
    var vectors = stackalloc VectorStruct[5];
    for (var i = 0; i < 5; i++) {
        vectors[i].X = 5;
        vectors[i].Y = 10;
    }
}
 
public void WithStackAllocSpan() // When using Span, no need for unsafe context
{
    var vectors = stackalloc VectorStruct[5];
    for (var i = 0; i < 5; i++) {
        vectors[i].X = 5;
        vectors[i].Y = 10;
    }
}
```

| Method             |      Mean |     Error |    StdDev |
|--------------------|----------:|----------:|----------:|
| WithNew            | 10.372 ns | 0.1531 ns | 0.1432 ns |
| WithStackAlloc     |  5.704 ns | 0.0938 ns | 0.0831 ns |
| WithStackAllocSpan |  5.742 ns | 0.0965 ns | 0.1021 ns |

Fark açık, tabi artık elimizde ```Span```var, ```unsafe``` işlere girmektense ```Span``` kullanmanızı tavsiye ederim. Tesadüfe bakın ki önceden [bu konuda yazmıştım](http://www.umutozel.com/span-memory).

## 6 - StringBuilder dedi statik kod analiz aracı

Çoğumuz statik kod analiz araçları kullanmışızdır (Sonarqube, Coverity, Sourcemeter gibi), bu araçların en çok uyarı çıkardığı konu genelde ```string``` birleştirme işini toplama yerine ```StringBuilder``` ile yap oluyordur. Genelde es geçilen bu teknik borçlar hadi artık temizleyelim dediğinizde aylar sürebilir.

Öncelikle neden ```string```'leri toplamak sorun oluyor? ```string``` özel bir tip, bir referans tipi ama immutable. Yani her değişikliğinizde eski değer GC'nin temizlemesi için bırakılırken bellekte yepyeni bir alan ayrılıyor. Bu da çok fazla ```string``` işleminde GC üzerinde sağlam baskı olacağını gösteriyor. Şimdi aradığımda tabii ki bulamadığım bir çalışmada uygulamaların harcadığı toplam donanım gücünün %70-80 (yamuluyor olabilirim) civarının ```string``` işlemlerine gittiği yazıyordu.

Dikkat edilebilecek noktalar:

* Eğer performans arttırmak için bu işe kalkışıyorsanız önce ölçüm yapmalısınız. 10-15 kadar toplama işleminin ```StringBuilder```'dan daha hızlı olabileceğini göreceksiniz.
* ```StringBuilder``` ilk kapasitesini ayarlayabilirseniz daha da verimli çalışacaktır.
* Aynı ```StringBuilder``` objesini tekrar tekrar kullanarak daha da performans elde edebilirsiniz. Kodun okunurluğunu azaltmama adına döngü gibi küçük kapsamlı yerlerde kullanmak faydalı olacaktır.

## 7 - String Interning mi? O nedir?

.Net platformunun pek bilinmeyen bir ```string``` optimizasyonu vardır, ilk değerleri aynı atanmış iki ```string``` için tek bir referans kullanır.

```csharp
string a = "Test";
string b = "Test";
```

a ve b iki farklı değişken gibi görünse de aynı belleği gösteren ortak referans olacaklar. Bu işleme String Interning deniyor. Bize iki faydası var:

* Aynı değere sahip iki ```string``` için ortak obje kullanarak bellekten kazanıyoruz
* ```string```'ler karşılaştırılırken öncelikle referans'a sonra değere bakılır. Aynı referansa sahip iki ```string``` karşılaştırıldığında değerlerini karşılaştırmak gerekmeden eşit olduklarını anlayabileceğimizden zaman kazanırız.

Derleyicinin eşit değere sahip ```string```'leri tespit etmesi pahalı bir işlem. Bu yüzden çalışma zamanında bu işlem hiç yapılmaz. Ancak çalışma zamanı bu işi kendimiz el ile yapabiliriz. ```string.Intern(string)``` ile varolan ```string``` ile aynı referansı kullanmasını, ```string.IsInterned(string)``` ile de aynı değeri gösterip göstermediklerini kontrol edebilirsiniz.

```csharp
private string s1 = "Hello";
private string s2 = " World";
 
[Benchmark]
public void WithoutInterning() {
    var s1 = GetNonLiteral();
    var s2 = GetNonLiteral();
    for (var i = 0; i < Size; i++) {
        var x = s1.Equals(s2);
    }
}
 
[Benchmark]
public void WithInterning() {
    var s1 = string.Intern(GetNonLiteral());
    var s2 = string.Intern(GetNonLiteral());
    for (var i = 0; i < Size; i++) {
        var x = s1.Equals(s2);
    }
}
 
private string GetNonLiteral() => s1 + s2;
```

100 tekrarlı bir ölçüm yaptığımızda:

| Method             |     Mean |     Error |    StdDev |   Median |
|--------------------|---------:|----------:|----------:|---------:|
| WithoutInterning   | 198.3 ns |  3.986 ns | 10.776 ns | 201.5 ns |
| WithInterning      | 424.4 ns |  8.426 ns |  8.653 ns | 421.0 ns |

10 tekrarlı bir ölçüm yaptığımızda ise:

| Method             |     Mean |     Error |    StdDev |   Median |
|--------------------|---------:|----------:|----------:|---------:|
| WithoutInterning   | 68.06 us | 0.6225 us | 0.5198 us | 201.5 ns |
| WithInterning      | 16.11 us | 0.3288 us | 0.3075 us | 421.0 ns |

Buradan Intern işleminin çok maliyetli olduğunu ve karşılaştırma işine göre Intern işi arttığında ciddi performans kaybı yaşadığımızı görebiliriz.

Optimizasyon çalışmalarını bir yerlerden faydalı olduğunu duyduğumuz için yapmamalıyız, deneyler ile detaylı ölçümlememiz şart.

Mutlu kodlamalar!
