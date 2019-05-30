---
layout: post
title: Visual Studio Code ile TypeScript Projemizi Test Edelim
comments: true
redirect_from: "/2019/05/14/TypeScript-Testing/"
permalink: typescript-testing
---

Yeni bir yazımıza hoş geldiniz. Bu sefer ufak bir TypeScript projemiz için Unit Test geliştireceğiz.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">A QA engineer walks into a bar. Orders a beer. Orders 0 beers. Orders 99999999999 beers. Orders a lizard. Orders -1 beers. Orders a ueicbksjdhd. <br><br>First real customer walks in and asks where the bathroom is. The bar bursts into flames, killing everyone.</p>&mdash; Brenan Keller (@brenankeller) <a href="https://twitter.com/brenankeller/status/1068615953989087232?ref_src=twsrc%5Etfw">November 30, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Yazdığımız kodları test etmeliyiz, değil mi? Hani bu iş seçimlik olmamalı, hatta önce testlerimizi yazmalıyız! Peki çevrenizdeki yazılımcılar bu sözün ne kadar arkasında duruyorlar? Zaman yok, bütçe yok gibi bahaneler havada mı uçuşuyor?

Ben yazı dizisinde testin önemi, TDD, BDD gibi konulara değinmeyeceğim, bu konularda hem çok yazı var hem de **gereklilik** mevzusunda (neredeyse) hepimiz hemfikiriz. Daha çok spesifik bir konuda kendi tercihlerimi ve izlediğim yöntemleri anlatarak bir katkıda bulunmak istedim.

![Operation Completed Successfully](/assets/typescript-test-operation-completed-succesfully.jpg)

# Projemizi Hazırlayalım

> Makalenin kodlarına [https://github.com/umutozel/typescript-testing](https://github.com/umutozel/typescript-testing) adresinden ulaşabilirsiniz.

Direk konuya dalmaktan yanayım, hemen ufak bir TypeScript projesi oluşturalım. Tüm geliştirmeleri [Visual Studio Code](https://code.visualstudio.com/) ile yapacağız, kurmadıysanız bekliyorum.

Önceden JavaScript kodlarımızı dağıtmak pek hoş değildi, kendimiz minified versiyon hazırlardık, kendi sitemiz, CDN, npm, bir ara Bower ile yayınlamaya uğraşırdık, artık **WebPack**, **Browserify** gibi araçların yaygınlaşmasıyla **npm** standart haline geldi diyebiliriz.

---

Projemiz için bir klasör seçip paketimizi oluşturalım.

```shell
npm init
```

aşağıdakine benzer sorular soracak, hepsine *Enter* basın gitsin.

![npm init](/assets/typescript-test-npm-init.jpg)

Bu komut bizim için **package.json** isimli bir dosya oluşturdu.

---

Aşağıdaki komutu çalıştırarak projemize TypeScript desteğini ekleyelim.

```shell
npm i -D typescript
```

Bu komuta verdiğimiz **-D** parametresi bu gereksinimin sadece geliştirme yaparken kullanıldığını, yayınlanan paketin böyle bir gereksinimi olmayacağını söylüyor (**devDependency**). Konumuz node ile proje geliştirmek değil, bu yine çok kaynak olan bir konu. Biz TypeScript ile geliştirdiğimiz node projesini nasıl test edeceğimize odaklanalım.

**tsconfig.json** dosyamızı da oluşturalım. Bu dosya TypeScript ayarları için kullanılıyor.

```shell
npx tsc --init
```

[npx](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b) öncesi bu işi yapmak için TypeScript'i global kurmamız ya da **tsc** dosyasının **node_modules** içindeki yolunu kullanmamız gerekiyordu.

Bu dosyada olası tüm TypeScript ayarları bir çoğu kapatılmış şekilde geliyor. Benim tercih ettiğim ayarları aşağıda bulabilirsiniz, dosya içeriğini bu içerik ile değiştirelim.

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "moduleResolution": "node",
    "outDir": "./dist",
    "declaration": true,
    "experimentalDecorators": true,
    "sourceMap": true,
    "lib": [
      "es2015",
      "dom"
    ]
  },
  "include": [
    "./index.ts",
    "./lib/**/*.ts",
    "./test/**/*.ts"
  ]
}
```

---

Şimdi alışkanlık haline gelmesi gereken kod standardını kontrol eden **tslint** paketini kuralım.

```shell
npm i -D tslint
```

**tslint** ayar dosyamızı da projemizin ana klasörüne **tslint.json** dosyası ile ekleyelim.

```json
{
    "extends": "tslint:recommended",
    "rules": {
        "interface-name": [false],
        "semicolon": false
    }
}
```

Ayarları kendinize göre değiştirebilirsiniz, ancak mutlaka her projenizde **tslint** ya da benzer bir linter kullanın.

---

Ana klasörümüze bir **lib** klasörü ekleyelim ve içerisine **fibonacci.ts** dosyamızı oluşturalım. İçeriği de aşağıdaki gibi olsun.

```typescript
export function fibonacci(index: number) {
    if (index <= 0) {
        throw new Error("Index must be a positive number")
    }
    if (!Number.isInteger(index)) {
        throw new Error("Index must be an integer")
    }

    let [a, b] = [1, 1]
    for (let i = 3; i <= index; i++) {
        [a, b] = [b, a + b]
    }

    return b
}
```

Basit bir kod olsun diye Fibonacci'yi tercih ettim. Farketmişsinizdir, burada gönderilen sayının tam sayı olması ve 0'dan büyük olmasını kontrol ediyoruz.

---

Ana klasörümüze bir de **index.ts** dosyası ekleyelim ve dış dünyaya yukarıda yazdığımız kodu kullanılabilir hale getirelim. Bunu aşağıdaki gibi yapıyoruz.

```typescript
export * from "./lib/fibonacci"
```

**index.ts** ile tüm **export** işlerimizi yaparak kodlarımızı farklı klasör ve dosyalarda geliştirme imkanı ediniyoruz.

---

Şimdi **package.json** dosyamızın içeriği aşağıdaki gibi olmalı:

```json
{
  "name": "typescript-testing",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "tslint": "^5.16.0",
    "typescript": "^3.4.5"
  }
}
```

Klasör yapımız ise aşağıdaki gibi olmalı.

![Klasör Yapısı](/assets/typescript-test-folder-1.jpg)

Bakalım kodumuz çalışıyor mu? Bunun için ilk önce TypeScript kodlarımızı derlememiz ve JavaScript çıktısını üretmeliyiz. Sonra fibonacci fonksiyonumuzu çağıracağız.

Kodları **dist** klasörüne derlemek için aşağıdaki komutu çalıştırıyoruz:

```shell
npx tsc
```

**dist** klasörü aşağıdaki gibi oluşturulmuş olmalı:

![Dist Klasörü](/assets/typescript-test-dist.jpg)

Aşağıdaki sihirli kod parçası ile fonksiyonumuzu çağırabiliriz:

```shell
node -e 'console.log(require("./dist/lib/fibonacci").fibonacci(10))'
```

Ben çalıştırdığımdaki ekran görüntüsü aşağıdaki gibi:

![Direk Çağrı](/assets/typescript-test-call.jpg)

Gördüğünüz gibi doğru değer olan 55 görüntülendi.

1, 1, 2, 3, 5, 8, 13, 21, 34, ***55***

JavaScript projelerimizde böyle düzenli çalıştırdığımız scriptlerimiz (bana kimse betik dedirtemez :)) hep olur, önceden **grunt**, **gulp** gibi araçları çok kullanırdık, arada ne oldu emin değilim ama artık neredeyse herkes (ben de dahil) bu tür script'ler için sadece npm kullanıyor.

TypeScript projelerini npm ile yayınlayan herkesin başına gelen bir sorun, JavaScript kodlarını oluşturmadan **npm publish** komutunu çalıştırmak. Sonra hatasını farkedip son yayınlanan paketi devre dışı bırakmak için **npm unpublish** komutunu çalıştırmak. Sonra bu hareketin ona 24 saat paket yayınlayamama cevasını kestiğini görünce yaşanan büyük hayal kırıklığı (benim başıma gelmedi hiç tabii, bir arkadaştan duydum).

Bu ve benzeri sıkıntıların önüne geçebilmek için hazır [npm scriptlerini](https://docs.npmjs.com/misc/scripts) kullanıyoruz.

Örneğin **prepare** script'i npm paket hazırlanmadan (pack) ya da yayınlanmadan (publish) önce çağırılır. Biz de **prepare** için bir script tanımlayarak bu adımlardan önce hazırlık yapabiliriz. Tahmin ettiğiniz gibi, **prepare** adımında TypeScript kodlarımızı derleyerek JavaScript çıktılarını hazırlamak istiyoruz.

Ben genelde ayrı bir adım olarak **build** script'i eklerim, **prepare** aşamasında da **build** script'ini çağırırım. Package.json dosyamızın **scripts** kısmı aşağıdaki gibi oluyor:

```json
...
  "scripts": {
    "build": "tsc",
    "prepare": "npm run build"
  },
...
```

Artık paketimizi her **npm publish** komutu ile yayınlamak istediğimizde JavaScript kodlarımız otomatik oluşturulmuş olacak.

Kodlarımız test edilmeye hazır, haydi başlayalım.

## Chai

[Chai](https://www.chaijs.com/) testlerimiz için kontroller yapmakta kullanacağımız kütüphanemiz. Örneğin:

```javascript
// burada foo 3 uzunluğa sahip mi kontrolü yapıyoruz
expect(foo).to.have.lengthOf(3);
```

Paketi ve TypeScript tanımlarının kurulumunu aşağıdaki gibi yapıyoruz:

```shell
npm i -D chai @types/chai
```

## Mocha

[Mocha](https://mochajs.org/) ise bir test altyapı sistemi. **Chai** kontrol ifadeleriyle fonksiyonların içine yazdığımız testlerimizi **mocha** çağrıları yaparak sisteme tanıtıyoruz. Böylece **mocha** dahil bir çok kütüphane hangi fonksiyonlarımızın test ifadeleri içerdiğini tespit edebilecek. Bir örnek görelim.

```javascript
// bir test grubu oluşturuyoruz
describe('#sum()', function() {

  // her testten önce araya girebiliriz
  // bunun gibi bir çok geri-bildirim fonksiyonu bulunmakta
  beforeEach(function() {
  })
  
  // testimizin tanımı
  it('should add numbers', function() {
    // chai ile 5'e kadar sayıların toplamının 15 olmasını kontrol ediyoruz
    expect(sum(1, 2, 3, 4, 5)).to.equal(15);
  })
  
  // ...başka testler
  
})
```

Mocha ile TypeScript testlerimizi çalıştırabilmek için kodların test çalıştırmadan önce bellekte JavaScript dönüşümünün yapılması gerekiyor, bunun için [ts-node](https://github.com/TypeStrong/ts-node) paketini kullanıyoruz.

Kurulum aşağıdaki gibi:

```shell
npm i -D mocha @types/mocha ts-node
```

Kurulumları yaptıktan sonra ufak **mocha** için son bir işimiz kalıyor, ayarlar. Mocha her çalıştığında ona bazı ayarlar geçmemiz gerekiyor, TypeScript dönüşümü için **ts-node** kullanımı, test sonuçlarını nereye yazacağı ve testleri hangi klasör/dosya içinde arayacağı gibi. Bunu da ana klasörümüze aşağıdaki içerik ile **mocha.opts** dosyası ekleyerek sağlıyoruz.

```shell
--require ts-node/register
--reporter-options mochaFile=./test_results/test-results.xml
**/*.spec.ts
```

## istanbul.js and nyc

Testlerimizi yazdık, başarılı bir şekilde çalışıyorlar diyelim. Ancak geliştirdiğimiz kodlarımızın ne kadarlık bir kısmı test ediliyor? Test edilmemiş bölümler/fonksiyonlar/satırlar hangileri? Bu bilgileri öğrenebilmek için test coverage (kapsam)  araçlarını kullanıyoruz. Benim tercihim [Istanbul.js](https://istanbul.js.org/). Istanbul.js paketini kendi başına da kullanabiliriz, ancak bu kütüphaneyi komut satırından çağırmak için ise [nyc](https://github.com/istanbuljs/nyc) aracını kullanıyoruz. Kurulum aşağıdaki gibi (istanbul.js ayrı kurmamız gerekmiyor):

```shell
npm i -D nyc
```

Kurulumlar tamamlandıktan sonra da **nyc** için ayarları girmemiz gerekiyor. Bunun için de ana klasörümüze **.nycrc** dosyasını aşağıdaki içerik ile ekliyoruz:

```json
{
    "include": [
        "**/lib/*"
    ],
    "exclude": [
        "dist",
        "lib/types.ts",
        "**/*.d.ts"
    ],
    "extension": [
        ".ts"
    ],
    "require": [
        "ts-node/register"
    ],
    "reporter": [
        "text-summary",
        "lcov",
        "cobertura"
    ],
    "all": true,
    "sourceMap": true,
    "instrument": true,
    "temp-directory": "./test_results/coverage/.nyc_output",
    "report-dir": "./test_results/coverage"
}
```

Burada test kapsamını ölçerken hangi dosyaları dikkate alacağı/göz ardı edeceği, çıktıları nereye yazacağı gibi ayarları belirtiyoruz.

---

Gerekli paketlerin hepsini kurup ayarlarımızı yaptık. Şimdi **package.json** ile scriptlerimizi ekleme vakti geldi. **scripts** listesine aşağıdaki iki elemanı ekleyelim.

```json
  "test": "mocha --opts mocha.opts --reporter spec",
  "cover": "nyc --reporter text-summary mocha --opts mocha.opts --reporter spec",
```

**Mocha** için ayar dosyamızı parametre geçtik. Benzer şekilde **nyc** ile test kapsamı çağrısında da **mocha** parametresi ile ekrana nasıl bir rapor basılması gerektiği parametrelerini de geçtik.

Artık ```npm run test``` (```npm test``` de olur) komutu ile testlerimizi çalıştırabileceğiz. Test kapsamını ölçmek için ise ```npm run cover``` komutunu çağırmamız yeterli olacak.

Haydi o zaman test ekleyelim. Ana klasöre hemen **test** diye bir klasör açalım ve için **fibonacci.spec.ts** dosyasını ekleyelim. Burada **.spec** kısmı önemli, araçlarımız testlerimizi bu sayede bulacak.

Hemen ufak bir test ekleyelim:

```typescript
// gerekli paketleri çağırıyoruz
import { expect } from "chai"
import "mocha"

// fonksiyonumuzu import ettik
import { fibonacci } from ".."

// bir test grubu oluşturduk
describe("Fibonacci tests", () => {

  // ilk önce negatif bir sayı için hata fırlatılmasını test edelim
  it("should throw for negative number", async () => {
      expect(() => fibonacci(-1)).to.throw()
  })
})
```

Her şey hazır, bakalım ne olacak?

![İlk Test](/assets/typescript-test-first-run.jpg)

🎉🎊🕺

Peki, test kapsamımız nasıl?

![İlk Kapsam](/assets/typescript-test-first-cover.jpg)

Kodlarımızın sadece %25'i test kapsamında çağrılmış. Çünkü biz sadece negatif sayı ile çağrı yaptığımızda hata fırlatılmasını test ettik. Test grubumuzu aşağıdaki gibi düzeltelim:

```typescript
describe("Fibonacci tests", () => {

    it("should throw for negative number", async () => {
        expect(() => fibonacci(-1)).to.throw()
    })

    it("should throw for 0", async () => {
        expect(() => fibonacci(0)).to.throw()
    })

    it("should throw for non integer number", async () => {
        expect(() => fibonacci(1.43)).to.throw()
    })

    it("should return 1 for first item", () => {
        expect(fibonacci(1)).to.equal(1)
    })

    it("should return 1 for second item", () => {
        expect(fibonacci(1)).to.equal(1)
    })

    it("should return 89 for 11th item", () => {
        expect(fibonacci(11)).to.equal(89)
    })
})
```

Artık 0 ile çağrı yanında küsüratlı sayı ile de hata fırlatılmasını kontrol ediyoruz. Ayrıca 1. ve 2. elamanın 1 olmasını, 11. elemanın 89 olmasını da kontrol ediyoruz. Tekrar test kapsam kontrolümüzü yapalım:

![Tam Kapsam](/assets/typescript-test-full-cover.jpg)

Yaşasın, tüm testlerimiz çalıştı ve kodlarımızın tamamı testler kapsamında kontrol edildi. Makaledeki kodlara [https://github.com/umutozel/typescript-testing](https://github.com/umutozel/typescript-testing) adresinden ulaşabileceğinizi tekrar hatırlatayım. Kodları inceleyip ihtiyacınıza göre özelleştirmek sizin elinizde.

Mutlu kodlamalar!
