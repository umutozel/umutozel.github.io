---
layout: post
title: Visual Studio Code ile TypeScript Testlerimizi Görselleştirelim
comments: true
redirect_from: "/2019/05/20/TypeScript-Testing-VSCode/"
permalink: typescript-testing-vscode
---

[TypeScript test](http://umutozel.com/typescript-testing) yazımızda projemizi hazırlamış, testlerimizi geliştirmiş, test ve kapsam için script hazırlamış ve sonuçları komut satırında görebilmiştik.

![Testler](/assets/typescript-test-full-cover.jpg)

Hemen hatırlayalım, [Mocha](https://mochajs.org/) testlerimizi [Chai](https://www.chaijs.com/) ifadeleri ile yazmıştık, [istanbul](https://istanbul.js.org/) kullanarak kapsam kontrolünü [nyc](https://github.com/istanbuljs/nyc) komut satırı arayüzü ile yapmıştık.

Şimdi bazı eklentiler ile Visual Studio Code üzerinde bu testleri görselleştirelim.

# Mocha Test Explorer

![Mocha Test Explorer](/assets/ts-test-vscode-mocha-test-exp-1.jpg)

[Mocha Test Explorer](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter) eklentisi bize testlerimizi komut satırına gerek kalmadan testin üzerinde çıkran *Run* ve *Debug* komutlarıyla çalıştırmamız ve debug edebilmemizi sağlıyor.

Ancak bunu yapabilmesi için önce testlerimizi nasıl bulacağını ona söylememiz gerekiyor. Bunu projemizin ana klasöründe bir *.vscode* klasöründe *settings.json* dosyasını aşağıdaki içerik ile oluşturmamız gerekiyor. Biliyorsunuzdur, bu dosya ile Visual Studio Code proje bazlı ayarlar yapabiliyoruz.

```json
{
    "mocha.files.glob": "**/*.spec.ts",
    "mocha.options": {
        "compilers": "ts:ts-node/register"
    },
    "mocha.requires": [
        "ts-node/register"
    ]
}
```

Klasör yapımız da aşağıdaki gibi olmalı.

![Mocha Test Explorer](/assets/ts-test-vscode-mocha-test-exp-2.jpg)

Şimdi test dosyamızı açtığımızda aşağıdaki gibi görünmeli (testleri çalıştırdığım için yeşil kutuları görüyorsunuz).

![Mocha Test Explorer](/assets/ts-test-vscode-mocha-test-exp-3.jpg)

> Not: Komutları göremezseniz Visual Studio Code *Reload Window* komutu ile editörü tekrar yüklemeniz gerekebilir.

---

# Mocha Sidebar

![Mocha Sidebar](/assets/ts-test-vscode-mocha-sidebar-1.jpg)

Sıradaki eklentimiz [Mocha Sidebar](https://marketplace.visualstudio.com/items?itemName=maty.vscode-mocha-sidebar). Bu eklenti ile test dosyalarımızı açmadan, Visual Studio Code *Test* kenar penceresine bu eklentinin eklediği arayüzü kullanarak çalıştırabiliyoruz.

![Mocha Sidebar](/assets/ts-test-vscode-mocha-sidebar-2.jpg)

Ağaç görünümünde istersek tüm testleri, istersek grupları, istersek tek tek testleri çalıştırabiliyoruz. Sonuçları görmek için ise *Output* penceresinde *sideBar-test* görünümünü seçmemiz gerekiyor.

![Mocha Sidebar](/assets/ts-test-vscode-mocha-sidebar-3.jpg)

Farketmişsinizdir, bu görünümde ağaç yapısının üstünde 3 adet buton bulunmakta.

☂ Şemsiye butonu test kapsamımızı (coverage) görmemizi sağlıyor. Burada ufak bir detay var, gösterim için biraz önceki *sideBar-test* görünümü kullanılmıyor, *sideBar-coverage* görünümüne geçmeniz gerekiyor.

![Mocha Sidebar](/assets/ts-test-vscode-mocha-sidebar-4.jpg)

▷ Oynat butonu ile devamlı test (Continuous Testing) yapabiliyoruz. Açıkçası ben düzgün çalıştıramadım. Olması gereken kodlarınızı yazdıkça düzenli olarak testleri çalıştırması ve kalan testlerin anında kırmızı geçenlerin yeşile dönmesi.

↻ Yenile butonu tahmin ettiğiniz gibi projemizdeki testleri yeniden tarıyor. Yeni yazdığımız testlerin listeye gelmesi için kullanıyoruz.

---

# Coverage Gutters

![Coverage Gutters](/assets/ts-test-vscode-coverage-gutters-1.jpg)

Testlerimizi artık Visual Studio Code ile çağırıp kod kapsamını *Output* görünümünde izleyebiliyoruz. Dosyalardaki satırların kod kapsamında olup olmadığını görebilsek ne güzel olurdu değil mi?

Bir önceki aşamada Mocha Sidebar ile çalışırken kod kapsamını çalıştırdıysanız eğer (şemsiye butonu ile) projenizde aşağıdaki gibi bir klasörün oluşması gerekiyor.

![Coverage](/assets/ts-test-vscode-coverage-gutters-2.jpg)

Coverage Gutters eklentisini kurduğumuzda da Visual Studio Code Status Bar'a aşağıdaki gibi *Watch* butonu gelmiş olmalı.

![Coverage](/assets/ts-test-vscode-coverage-gutters-3.jpg)

Tıkladığımızda buton yazısı *Remove Watch* olacak. Bu işlem ile Coverage Gutters'a projemiz içinde kod kapsam dosyası aramasını söylemiş olduk. Eklentilerimizin varsayılan ayarları ve projemizdeki *Mocha* ve *nyc* ayarları bu noktaya kadar sorunsuz çalışmamızı sağladı. Ancak hepsini istediğiniz gibi özelleştirebiliyorsunuz. Visual Studio Code ayarlar sayfasına gidip eklentiler için tanımlanmış özellikleri inceleyebilirsiniz.

Şimdi *lib/fibonacci.ts* dosyamızı açalım. Aşağıdaki gibi görünmeli.

![Coverage](/assets/ts-test-vscode-coverage-gutters-4.jpg)

Kod satırlarımızın sol tarafındaki yeşillikler bu satırların testler kapsamında çalıştığını gösteriyor. Test yazmaya başladığınızda, bu bilgiye sahip olmak, hem de çalıştığınız editörde görebilmek çok faydasını olacak.

Şimdi başka bir deney yapalım, *test/fibonacci.spec.ts* dosyasını açıp aşağıdaki gibi iki testi kapatın.

![Coverage](/assets/ts-test-vscode-coverage-gutters-5.jpg)

Şimdi tekrar Mocha Sidebar penceresinde şemsiyeye tıklayıp kod kapsamını tekrar hesaplatalım. *lib/fibonacci.ts* dosyasını tekrar açtığımızda aşağıdaki gibi testlerimizin artık tüm satırları kapsamadığını göreceğiz.

![Coverage](/assets/ts-test-vscode-coverage-gutters-6.jpg)

---

# Sonuç

Testlerimiz artık çok daha görsel. Benzer sonuçları elde edebileceğimiz bir çok test kütüphaneleri ve eklentiler var, keşfetmenin keyfini size bırakıyorum.

Test yazmanın keyfine varmanız dileğiyle &#129514;
<br>
Mutlu kodlamalar!
