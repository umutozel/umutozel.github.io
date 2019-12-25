---
layout: post
title: C# Span ve Memory
comments: true
redirect_from: "/2019/12/22/span-memory.js/"
permalink: span-memory
---

.Net, yönetilen bir platform, yani bellek erişimi ve yönetimi güvenli ve otomatik yapılır. Tüm tipler CLR tarafından yönetilir, bellek stack ya da yönetimli havuzdan sağlanır.

Platform çağrıları yapmak, düşük seviye kod yazmak ya da performansın dibine vurmak istediğimizde bize ufaktan engel olur, eski zamanlardan alıştığımız C ve C++ gibi dillerin aksine. Burada devreye yönetilmeyen belleğe Marshal ile erişim giriyor ve .Net gözünde bu işlem güvenli değil (**unsafe**).

Belleğe erişimimiz 3 şekilde olabilir:

* Yönetimli havuz - bir diziyi düşünebiliriz
* Stack - stackalloc istekleri ya da Struct tipler. Kopyalanarak taşınırlar
* Yönetimsiz bellek - bir obje işaretçisi gibi

Bu tür bellek erişimlerinin hepsini **fixed**, **stackalloc**, **Marshal**, **unsafe** gibi özel belirteçler ile yapabiliyoruz, dilin bize sağladığı doğal bir yapı yok.

Diğer bir sorun da lanetli **string** erişimleri, bazı çalışmalarda çalışan .Net programlarının %95 kadar işlemi **string** işlerine harcadığı görülmüş. Sonarqube gibi araçlar boş yere **StringBuilder** kullanın diye ağlamıyor. Bu konuyu biraz açalım, **string** bir immutable tiptir, yani içeriğini asla değiştiremezsiniz. Dolayısıyla yaptığınız her işlem kopyalama ile sonuçlanır. Büyük değerler için kabus gibi sonuçlar doğurabilir.

# ```Span<T>```

.Net Core 2.1 ve C# 7.2 ile kullanıma açılan **Span** işte tüm bu sıkıntılarımıza bir çözüm getiriyor.

Span bir Stack özel tip (ref struct). Ardışık belleği işaret edebilmemizi sağlıyor. Bu işi de kabaca bir bellek işaretçisi ve uzunluk değerini tutarak yapıyor. Aşağıdaki gibi:

```csharp
public readonly ref struct Span<T>
{
  private readonly ref T _pointer;
  private readonly int _length;
  ...
}
```

![Span](/assets/span-memory-span.png)

Aşağıdaki gibi oluşturabiliyoruz:

```csharp
Span<char> span1 = new char[] { 's', 'p', 'a', 'n' };

Span<byte> span2 = stackalloc byte[50];

IntPtr array = new IntPtr();
Span<int> span3 = new Span<int>(array.ToPointer(), 1);
```

Şimdi esas harika kısım, istersek bir indeks değerini değiştirebiliriz ya da değerin bir kısmını işaret eden başka bir Span dönebiliriz.

```csharp
Span<char> span = new char[] { 's', 'p', 'a', 'n' };
// ilk karaktere işarete bir işaretçi
// ref konusuna daha önceki bir yazımda değinmiştim
ref char first = ref span[0];
// değiştirelim ilk karakteri
first = 'S';
// artık "Span".
Console.WriteLine(span.ToArray());


// ilk karakteri atlayan ayrı bir Span oluşturalım
Span<char> span2 = span.Slice(1);
// artık elimizde "pan" var
Console.WriteLine(span2.ToArray());
```

Slice ile daha hızlı çalışan bir Trim metodu yazalım.

```csharp
private static void Main(string[] args) {
    var test = "   Hello, World! ";
    Console.WriteLine(Trim(test.ToCharArray()).ToArray());
}

private static Span<char> Trim(Span<char> source) {
    if (source.IsEmpty)
        return source;

    int start = 0, end = source.Length - 1;
    char startChar = source[start], endChar = source[end];

    // baş ve sondan içeri doğru ilerleyip boşluk ile karşılaştıkça indeksleri düzenliyoruz
    while ((start < end) && (startChar == ' ' || endChar == ' ')) {
        if (startChar == ' ') {
            start++;
        }

        if (endChar == ' ') {
            end—;
        }

        startChar = source[start];
        endChar = source[end];
    }

    // artık elimizde hangi bölümü alacağımız bilgisi var
    return source.Slice(start, end - start + 1);
}
```

**Bu kodların yazımında hiç bir string kopyalanmamıştır!**

Sonuçta Trim ile ayrı bir parçayı işaret edeceğiz:

![Slice Span](/assets/span-memory-slice-span.png)


Bir de işaretçi ile kullanım görelim:

```csharp
IntPtr ptr = Marshal.AllocHGlobal(1);
try
{
  Span<byte> bytes;
  unsafe { bytes = new Span<byte>((byte*)ptr, 1); }
  bytes[0] = 42;
  Assert.Equal(42, bytes[0]);
  Assert.Equal(Marshal.ReadByte(ptr), bytes[0]);
  bytes[1] = 43; // Throws IndexOutOfRangeException
}
finally {
    Marshal.FreeHGlobal(ptr);
}
```

Span'ın bir işaretçi ve uzunluk bilgisinden oluştuğunu söylemiştik. Yukarıdaki kodda:

* Bir işaretçi oluşturup bu değer ile 1 adetlik Span oluşturduk
* İlk (ve tek) değere 42 atadık
* İkinci değere atama yapmak istediğimizde hata aldık

## Bana Benchmark göster!

Tabi ne faydası var sorusunu da cevaplamak lazım. Kendi yapmadığım internetten bulduğum bir iki benchmark sonucu aşağıdaki gibi:

```markdown
BenchmarkDotNet=v0.11.3, OS=Windows 10.0.16299.904 (1709/FallCreatorsUpdate/Redstone3)
Intel Core i7-8700 CPU 3.20GHz (Coffee Lake), 1 CPU, 12 logical and 6 physical cores
Frequency=3117197 Hz, Resolution=320.8010 ns, Timer=TSC
.NET Core SDK=3.0.100-preview-010184
  [Host]     : .NET Core 2.2.2 (CoreCLR 4.6.27317.07, CoreFX 4.6.27318.02), 64bit RyuJIT
  DefaultJob : .NET Core 2.2.2 (CoreCLR 4.6.27317.07, CoreFX 4.6.27318.02), 64bit RyuJIT


                    Method |      Mean |     Error |    StdDev | Ratio | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  14.04 ns | 0.1531 ns | 0.1357 ns |  0.11 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring |  39.64 ns | 0.8045 ns | 1.0740 ns |  0.32 |    2 |      0.0063 |           - |           - |                40 B |
               GetLastName | 125.04 ns | 2.4991 ns | 4.2436 ns |  1.00 |    3 |      0.0253 |           - |           - |               160 B |
```

![Span Benchmark](/assets/span-memory-benchmark.png)
<sub>[https://medium.com/@antao.almada/how-to-use-span-t-and-memory-t-c0b126aae652](https://medium.com/@antao.almada/how-to-use-span-t-and-memory-t-c0b126aae652)</sub>

Span için stack özel bir tip demiştik. Yani **ref struct** için varolan tüm kısıtlar Span için de geçerli. Yani boxing ya da bir sınıf içinde üye değer olamıyor. Bunun ilk bakışta akla gelmeyebilecek yan etkileri olabiliyor.

```csharp
async Task DoSomethingAsync(Span<byte> buffer) {
    buffer[0] = 0;
    await Something();
    buffer[0] = 1;
}
```

Bildiğiniz gibi asenkron çağrıların devam kısmı (await sonrası) scope koruyabilmek için lokal değişkenleri üye (field) olarak içeren geçici bir tip ile çalışır. Hadi biraz açalım, kodu tamamen kafadan uyduruyorum:

```csharp
async Task DoSomethingAsync(Span<byte> buffer) {
    buffer[0] = 0;
    var scope = new { buffer = buffer };
    var somethingStateMachine = new AsyncStateMachine(scope, Something);
    while(!somethingStateMachine.Done) { }
}

class AsyncStateMachine {

    AsyncStateMachine(scope: object, func: () => _) {
        // ...
    }
}
```

Kodun çok saçma olmasını kenara bırakırsak sonuçta buna benzer bir iş yapılıyor. Biraz önce de **ref struct** kısıtları geçerli demiştik. Burada Scope için oluşturulan objeye Span eklenemeyeceğinden **await** sonrası eski Span'ı kaybediyoruz.

## ```Memory<T>```

Tam burada **Memory** devreye giriyor. Memory için Span kapsayıcı tipi diyebiliriz. Kodu basitçe aşağıdaki gibi:

```csharp
public struct Memory<T> {
    OwnedMemory<T> _owned;

    public Memory(OwnedMemory<T> owned) { ... }
    public Span<T> Span => _owned.Span;
}

public class OwnedMemory<T> {
    void* _ptr;
    T[]   _array;
    int   _offset;
    int   _length;

    public Span<T> Span => _ptr == null
                                 ? new Span<T>(_array, _offset, _length)
                                 : new Span<T>(_ptr, _length);

    public void Dispose() {
        _ptr = null;
        _array = null;
    }
}
```

Böylece Stack ile sınırlı kalmayan Heap üzerinde tutabildiğimiz ve aynı özelliklere sahip bir tipe kavuşuyoruz.

Span ve Memory ile projeler üzerinde yapacağım değişiklikler ve deneylerimin sonuçlarını da ileride paylaşırım.

Mutlu kodlamalar.
