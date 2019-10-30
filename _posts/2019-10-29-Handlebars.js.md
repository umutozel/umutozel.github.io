---
layout: post
title: Yeni Başlayanlar için Handlebars.js
comments: true
redirect_from: "/2019/09/29/Handlebars.js/"
permalink: handlebars-js
---

[Handlebars.js](https://handlebarsjs.com/) bir şablon sistemi. Şablon sistemi nedir mi? Belirli yer tutucular ve yardımcı metodlar ile bir şablonu belirli parametreler ile yorumlayarak çıktı üretmemizi sağlayan sistemlerdir.

<sub>Bundan sonra sadece handlebars diyeceğim.<sub>

![Handlebars](/assets/handlebars-logo.jpg)

Diyelim ki geliştirilmiş bir uygulamanın başka bir sistem ile entegrasyonunu sağlamak ile görevlendirildiniz. Belirli bir klasöre konulan dosyaları isimlerine göre değerlendirip, text dosyayı yorumlamanız gerekiyor. Bunun için de aşağıdaki gibi bir alt sınıf yazdınız.

```csharp
public abstract class TextIntegration<T> {
    private readonly string _fileName;

    protected TextIntegration(string fileName) {
        _fileName = fileName;
    }

    public void Execute() {
        var entities = File.ReadAllLines(_fileName)
            .Select(ConvertLine);
    }

    protected abstract T ConvertLine(string line);
}
```

<sub>Olabildiğince basit tutmaya çalıştım, amacımız sadece şablon sistemlerinin faydalarını görmek.<sub>

Şimdi yapmanız gereken dosyalar için tek tek entegrasyon kodlarını yazmak. Bu dosyaların 50 tane olduğunu düşünürseniz yapılması gereken basmakalıp geliştirme çok fazla olacak, büyük bir hammallığa girişeceğiz.

![Bill Gates](/assets/handlebars-bill-gates.jpg)

Umarım siz de benim gibi tembelsinizdir. Bende tahminen tecrübe ile gelişmiş, bu tür durumlarda çalmaya başlayan özel bir alarm mekanizması var. Bu iş daha basit bir şekilde yapılabilmeli!

İşte bu sorunu handlebars ile çözeceğiz. Öncelikle yetenklerine bir bakalım, sonra kod üretimimizi yaparız.

## Handlebars

---

Handlebars neredeyse [Mustache](https://mustache.github.io/) ile aynı yazım tarzına (syntax) sahip ancak Mustache'ı yeni özelliklerle genişleterek onu kapsayan (superset) bir şablon sistemi.

Mustache tamamen logic-less, yani şablonun içine veri eşleşmeleri hariç hiç özel kod yazamıyoruz. 

![Handlebars Flow](/assets/handlebars-flow.png)

Yukarıdaki akış sizi yanıltmasın, çıktı her zaman HTMl olmak zorunda değil :)

Handlebars, [Jade](http://jade-lang.com/) ya da [pug](https://pugjs.org/api/getting-started.html) kadar karışık değil ancak Mustache kadar da basit değil, kendimce yarı akıllı dediğim ve tek atımlık işler için mükemmel bir aday.

T4 veya yaşı tutan ve CodeSmith ile uğraşmış olanara da bu şablon sistemleri yabancı gelmeyecektir.

## Değişkenler

Değişkenler ikişer bıyığın (**{{** ve **}}**, süslü parantez yerine bıyık diyeceğim) arasına alarak yazılıyor.

```handlebars
<h1>{{title}}</h1>
<p>{{body}}</p>
```

Handlebars bu değerleri bizim ona sağladığımız (aşağıdaki gibi) veriler içinden okuyarak değiştirecek,

```json
{
  title: "Selam Handlebars",
  body: "Handlebars çok havalı"
}
```

ve aşağıdaki sonucu elde edeceğiz:

```html
<h1>Selam Handlebars</h1>
<p>Handlebars çok havalı</p>
```

## Döngü (each)

Handlebars'ın mustache ile en büyük farkı fonksiyon çağrılarına izin vermesi. Bunu sağlamak için yardımcılar (helper) denilen yapıdan faydalanıyor. Döngü için kullandığımız **each** de bir yardımcı fonksiyondan başka bir şey değil.

```handlebars
<div>
{{#each characters}}
  <p>{{@index}}. {{this}}</p>
{{/each}}
</div>
```

yukarıdaki şablona aşağıdaki veriyi geçelim,

```json
{ "characters": ["Arthur", "Ford", "Zaphod"] }
```

çıktı aşağıdaki gibi olacak:

```html
<div>
  <p>0. Arthur</p>
  <p>1. Ford</p>
  <p>2. Zaphod</p>
</div>
```

## Kaçışsız Çıktı

Handlebars yukarıdaki gibi kullanımda değerleri güvenlik amaçlı steril edecek (sanitize). Yani zararlı olabilecek değerleri (Html, JavaScript kodları gibi) temizleyecek.

Eğer bunu istemiyorsak üçlü bıyık kullanmamız gerekiyor (**{{{** ve **}}}**).

## Koşul (if)

**if** de bir başka yardımcı fonksiyon. Belirli koşula göre yorumlamamızı değiştirebilmemizi sağlıyor.

```handlebars
{{#if user.admin}}
    <button class="launch">Çalıştır</button>
{{else}}
    <button class="disabled">Yetkiniz yok</button>
{{/if}}
```

şablonunu aşağıdaki veri ile çağırdığımızda,

```json
{
  user: {
    admin: true
  }
}
```

aşağıdaki çıktıyı elde ediyoruz:

```html
<button class="launch">Çalıştır</button>
```

## Değilse (unless)

Handlebars bize **if** için bir değil operatörü (! gibi) sağlamıyor, ancak bunun yerine **unless** kullanabiliyoruz. Karşılığı **!if** gibi düşünebilirsiniz.

Yukarıdaki koşul ifademizi **unless** ile yazalım.

```handlebars
{{#unless user.admin}}
    <button class="disabled">Yetkiniz yok</button>
{{else}}
    <button class="launch">Çalıştır</button>
{{/unless}}
```

Aynı veri ile çağırdığımızda çıktımız değişmeyecek.

## Obje kapsamı (with)

Çok fazla iç-içe objelerimiz olduğunda derinlerdeki özelliklere ulaşmak için yazmamız gereken kod kendini çok tekrarlayan içeriğe sahip olacaktır. Aşağıdaki veriyi düşünün:

```json
{
    "user": {
        "contact": {
            "email": "umutozel@gmail.com",
            "github": "https://github.com/umutozel/",
            "linkedin": "https://linkedin.com/in/umutozel/",
            "twitter": "monoblaine"
        },
        "name": "Umut"
    }
}
```

**user.contact** altında ulaşmak isteyeceğimiz 4 adet veri var. Her seferinde **user.contact.email** gibi yazmamız çok kalabalık olacak.

```handlebars
{{#with user}}
<p>{{name}}</p>
{{#with contact}}
<span>Email: @{{email}}</span>
<span>Github: @{{github}}</span>
<span>Linkedin: @{{linkedin}}</span>
<span>Twitter: @{{twitter}}</span>
{{/with}}
{{/with}}
```

Yukarıdaki gibi **#with user** ve **#with contact** ile daha az tuşa basarak aynı sonuca ulaşabiliyoruz.

```html
<p>Umut</p>
<span>Email: umutozel@gmail.com</span>
<span>Github: https://github.com/umutozel/</span>
<span>Linkedin: https://linkedin.com/in/umutozel/</span>
<span>Twitter: @monoblaine</span>
</span>
```

## Yorumlar

Çıktıda görünmesini istemediğimiz yorumlarımızı da **{{!--** ve **--}}** arasına yazıyoruz (HTML'den hatırlayın, yorumlar **\<!--** ve **-->** arasına yazılır).

```handlebars
{{!-- Kullanıcı admin değil ise butonu kapat --}}
{{#unless user.admin}}
    <button class="disabled">Yetkiniz yok</button>
{{else}}
    <button class="launch">Çalıştır</button>
{{/unless}}
```

## Yardımcı Fonksiyonlar (Custom Helpers)

Yardımcı fonksiyonlar **each**, **unless** gibi kullanabileceğimiz komutlar oluşturmamızı sağlıyor.

Elimizdeki bir listeyi HTML tablosuna çevirecek bir yardımcı fonksiyon yapalım. Kullanımı aşağıdaki gibi olacak:

```handlebars
{{table items}}
```

Ben genelde böyle işler için Node.js tercih ediyorum, o yüzden JavaScript kullanacağım. İsterseniz handlebars desteği olan başka bir dil de kullanabilirsiniz.

```javascript
Handlebars.registerHelper('table', function (data) {
  var rows = data.map(d => {
    var cols = Object.values(d).map(v => `<td>${v}</td>`).join("")
    return `<tr>${cols}</tr>`
  }).join("")
  var table = `<table>${rows}</table>`

  return new Handlebars.SafeString(table);
});
```

Aşağıdaki verimiz ile çalışalım:

```json
{
  "node": [
    { "name": "express", "url": "http://expressjs.com/"},
    { "name": "hapi", "url": "http://spumko.github.io/"},
    { "name": "compound", "url": "http://compoundjs.com/"},
    { "name": "derby", "url": "http://derbyjs.com"}
  ]
}
```

Çıktımız aşağıdaki gibi olacak:

```html
<table>
    <tr>
        <td>express</td>
        <td>http://expressjs.com/
    </td>
    </tr>
        <tr><td>hapi</td>
    <td>http://spumko.github.io/
    </td>
    </tr>
    <tr>
        <td>compound</td>
        <td>http://compoundjs.com/
    </td>
    </tr>
    <tr>
        <td>derby</td>
        <td>http://derbyjs.com/</td>
    </tr>
</table>
```

## Referanslar (Partials - Includes)

Bazen yazdığınız şablon çok büyüyebiliyor, bazen de birden fazla şablonda bazı kısımları tekrar tekrar yazmanız gerekebiliyor. Bu tür durumlarda bir şablon içinden başka bir şablonu çağırabildiğimiz **partial** yardımımıza koşuyor.

Öncelikle kullanmak istediğimiz parçayı kaydetmemiz gerekiyor:

```javascript
// dosyadan da yükleyebiliriz
const template = '{{name}}';
Handlebars.registerPartial('myPartial', template)
```

Kullanmak için ise aşağıdaki kullanım yeterli:

```handlebars
{{> myPartial }}
```

Daha fazla bilgi için [Partials](https://handlebarsjs.com/partials.html) dökümanına bakmanızı tavsiye ederim.

---

## Kod Üretimi

Artık edindiğimiz bilgiler ile bu işi nasıl otomatikleştirebileceğimize bakalım.

İlk iş projemiz için bir klasör oluşturmak.

```shell
mkdir integration && cd integration
```

Şimdi handlebars yükleyelim.

```shell
npm i handlebars
```

Artık bu klasörde Visual Studio Code açma vakti.

```shell
code .
```

Metadata (üst veri) en sevdiğim kavramlardan birisidir. Bize şimdi de entegrasyonlarımızı temsil eden üst veri lazım. Aşağıdaki gibi olabilir.

```text
customers.ini-Company-Id,Name,Code,Address-;
people.txt-User-Username,FirstName,LastName-,
```

4 adet üst veri içeren metadata dosyamızı hazırladık. Dosya adı, tip adı, alanlar ve okuyacağımız dosyada kullanılmış ayıraç bilgisi.

Bu veriyi de **metadata**.txt ismiyle kaydedelim.

Hemen bir **index.js** dosyası da oluşturursak kodlamaya hazırız. Benim VS Code aşağıdaki gibi görünüyor.

![Project](/assets/handlebars-project.jpg)

index.js dosyasına da aşağıdaki kodu yapıştırdığınızda projemiz çalıştırılmaya hazır hale gelecek.

```javascript
const os = require('os')
const fs = require("fs")
// handlebars böyle import ediliyor
const handlebars = require('handlebars');

// C# üretecek şablonumuz
// hepsi yukarıda değindiğimiz, burada bilmediğiniz bir şey yok
const template =
    `namespace MyIntegration {

    public sealed class {{type}}Integration: TextIntegration<{{type}}> {

        protected {{type}}Integration("{{fileName}}") {
            _fileName = fileName;
        }

        protected override {{type}} ConvertLine(string line) {
            var data = line.Split("{{separator}}");
            return new {{type}}() {
            {{#each props}}
                {{this}} = data[{{@index}}],
            {{/each}}
            };
        }
    }
}
`

// metadata dosyamızı yükledik ve bir obje listesine çevirdik
const metadata = fs.readFileSync("metadata.txt").toString().trim()
const lines = metadata.split(os.EOL)
const integrations = lines.map(l => {
    const d = l.split("-")
    return {
        fileName: d[0],
        type: d[1],
        props: d[2].split(","),
        separator: d[3]
    }
})

// out klasörünü oluşturduk
fs.mkdirSync("out")

// şablonu derledik ve bir fonksiyona çevirdik
const compiledTemplate = handlebars.compile(template)

/*
  her bir entegrasyon için derlenmiş şablon fonksiyonunu
  entegrasyon metadatası ile çağırıyoruz
*/
integrations.forEach(i => {
    const result = compiledTemplate(i)
    fs.writeFileSync(`out/${i.type}.cs`, result)
})
```

Kodumuzu aşağıdaki komut ile çalıştırıp, **out** klasöründe oluşturulan dosyaları görebilirsiniz.

```shell
node index.js
```

Tabii burada dikkate almadığımız bir nokta C# tip dönüşümleri. Yine de oluşturulan kodlar işimizi çok kolaylaştıracaktır.

Kod üreten projenin kaynak kodlarına [buradan](https://github.com/umutozel/handlebars-article) ulaşabilirsiniz.

Mutlu kodlamalar!
