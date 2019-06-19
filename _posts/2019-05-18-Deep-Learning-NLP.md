---
layout: post
title: Deep Learning & NLP
comments: true
redirect_from: "/2019/05/18/Deep-Learning-NLP/"
permalink: deep-learning-nlp
---

![Cyberdyne](/assets/deep-learning-cyberdyne.jpg)

# Derin Öğrenme Nereden Çıktı?

**Derin Öğrenme** (Deep Learning) biz insanlara çok doğal gelen örnekler ile öğrenmeyi sağlayan bir **Makine Öğrenmesi** (Machine Learning) tekniğidir. Makine Öğrenmesi ise bilgisayar sistemlerinin istenen bir görevi insanlardan açık bir talimat almadan, belirli algoritmalar ve istatistiksel modelleri kullanarak etkin bir şekilde yerine getirmesidir. Kalıplara ve çıkarımlara dayanan bu bilimsel yaklaşım ise **Yapay Zeka**'nın (Artificial Intelligence) bir alt kümesi olarak kabul edilir. Aşağıdaki resimde aralarındaki mantıksal ilişki görülebilir.

![Deep Learning Diyagram](/assets/deep-learning-diagram.png)

Bu üç başlığı biraz daha detaylı inceleyelim.

---

## Yapay Zeka

Yapay zeka, bilgisayarlara çeşitli faaliyetleri zeki canlılara benzer şekilde yerine getirme kabiliyeti kazandırmak olarak özetlenebilir. Genellikle insanların düşünme yöntemleri analiz edilerek bunların yapay karşılıkları üretilmeye çalışılır.

![Yapay Zeka](/assets/deep-learning-ai.jpg)

Bu kavramı ilk ortaya atan isim Alan Turing'dir. Alan Turing'i İkinci Dünya savaşı sırasında Almanlar'ın Enigma makinesinin şifresini çözen bilgisayarın geliştirilmesine liderlik eden Turing'in en büyük savaşın sonunu getirmede en önemli rölü üstlendiği kabul edilir. Ancak cinsel tercihlerine yapılan saldırıları kaldıramayan bu muhteşem beyin, 1954 yılında henüz 42 yaşında ve yapabileceklerinin daha başlarındayken kendi canına kıydı.

![Enigma](/assets/deep-learning-enigma.jpg)
<sub><sup>Solda Enigma, sağda bu çılgın cihazı kıran BOMBE</sup></sub>

### Turing Testi

Turing testi ilk olarak 1950 yılında **Mind** isimli felsefe dergisinde Alan Turing'in **Computing Machinery and Intelligence** makalesinde geçer. Testin amacı, bir makinenin düşünebildiğini söyleyebilmenin mantıksal olarak mümkün olup olmadığıdır. Bu teste göre, gönüllü bir insan ile bir makine test eden kişinin göremeyeceği bir yere alınır. Sorgulayıcı sadece sorular sorarak ikisiyle de sohbet eder ve hangisinin bilgisayar olduğunu anlamaya çalışır. Ses ile anlaşılmayı önleme adına klavye ve ekranda yazılar ile iletişim kurulur. Eğer sorgulayıcı bilgisayarı tespit edemez ise sistem Turing Testi'ni geçmiş sayılır.

![Turing Test](/assets/deep-learning-turing-test.jpg)

Yapay zeka aşağıdaki ağırlıklı olarak problemler ile ilgilenir.

* Bilgi
* Muhakeme
* Problem çözme
* Algı
* Öğrenme
* Plan yapma
* Objeleri kontrol edebilme

Arend Hintze, Yapay Zeka'yı 4 farklı kategoriye ayırır.

* **Tip 1: Ekileşimli Makineler** Bunlara Deep Blue örnek verilebilir. Bildiğiniz gibi IBM'in bu bilgisayarı 1990 yılında satranç şampiyonu Garry Kasparov'u yenmişti. Deep Blue, satranç tahtasındaki taşları ayırt edip, tahminler yürütebilir. Ancak hafızaya sahip değildir, eski oyunları inceleyerek yorum yapamaz. Sadece kendisinin ve rakibinin hamlelerini değerlendirip (puanlandırıp) en stratejik hamleyi seçer. Deep Blue ve Google'ın AlphaGO sistemleri spesifik ihtiyaçlar için geliştirilmiş olup farklı senaryolara kolayca uyarlanamazlar.

* **Tip 2: Sınırlı Hafıza** Bu sistemler geçmiş tecrübelerini kullanarak geleceğe yönelik kararlar verebilirler. Kendi kendine gidebilen araçlar bu şekilde tasarlanmışlardır. Bir aracın şerit değiştirmesi gibi olaylar bir sonraki hamlenin seçilmesinde kullanılır ancak kalıcı bir şekilde kayıt edilmezler.

* **Tip 3: Akıl Teorisi** Bu ifade, yapay zekanın başkalarının aldıkları kararlara etki eden kendi inançları, arzuları ve amaçları olduğunun bilincinde olmasıdır. Henüz böyle bir yapay zeka geliştirilememiştir.

* **Tip 4: Kendi Farkındalık** Bu kategoride ise yapay zekanın öz benliği vardır. Kendi farkındalığı olan makineler bulundukları durumu anlar ve başkalarının ne hissettiklerini çözebilmek için mevcut bilgileri kullanabilir. Bu tip bir yapay zeka da henüz geliştirilememiştir.

Yapay Zeka başlıca aşağıdaki teknolojilere dahil olmuştur.

* **Otomasyon** Bir sistemin bir işi otomatik yapmasıdır. Örneğin, Robotic Process Automation (RPA) normalde insanların yaptığı işleri çok yüksek sayıda ve tekrarlı yapacak şekilde programlanabilir.

* **Makine Öğrenmesi** Bilgisayarın programlanmadan hareket edebilmesi. Derin Öğrenme de bu alanın bir alt kümesidir. Birazdan göreceğiz :)

* **Makine Görmesi** Bilgisayarların görmesini sağlayan bilimsel yaklaşım. Görsel imajların dijitalleştirilerek sinyal olarak işlenmesine dayanır. Google size her "Yangın söndürücüleri işaretleyin" gibi soru sorduğunda etrafını daha iyi görebilir hale geliyor. İnsan gözü gibi biyolojik sınırlara dayalı değildir, dijital sinyal alınabildiği sürece duvarların arkasını bile yorumlayabilir.

* **Doğal Dil İşleme** İnsan dillerinin bilgisayarlar tarafından işlenmesidir. Bilinen en eski uygulamalarından birisi spam mail tespit eden algoritmalardır. Mailin konusu ve içeriğine bakarak gözümüzün önünden gereksiz olup olmadığına karar verilir. Genelde Makine Öğrenmesi ilkeleri uygulanır.

* **Robotlar ve Kendi Kendine Giden Araçlar** Önceki tüm yaklaşımların birlikte çalıştığı, görüntüler ve seslerin işlenmesi, öğrenilmiş yaklaşımlar ile karar verebilen ve insanların yaptığı işleri yapabilen sistemler tasarlanmasıdır.

Yapay Zeka başlıca aşağıdaki alanlarda uygulanmaktadır.

* **Sağlık** Hastalara daha doğru ve hızlı teşhis konulabilmesi için yapay zeka gittikçe artan bir yoğunlukta kullanım alanı bulmaktadır. IBM Watson ile geliştirilen bir sistem doğal dili işleyerek kendisine sorulan sorulara cevap verebilmektedir. Bu sistem hasta verilerini ve başka kaynakları inceleyerek bir hipotez oluşturabilir.

* **Eğitim** Yapay zeka ile not sistemi otomatikleştirilip eğitmenlere zaman kazandırılmaya çalışılıyor. Yapay zeka öğrenci bilgilerine erişip onlara uygun içerikleri doğru hızda öğrenmelerini sağlıyor.

* **Finans** Finans alanındaki uygulamalara muhtemelen hepimizin en az bir arkadaşı bulaşmıştır. Öğrenen sistemler ile Forex üzerinden çok paralar kazanmayı planlamıştır. Tahmin ettiğiniz gibi bu uygulamada veriler incelenerek finansal tavsiyelerde bulunulur. Günümüzde Wall Street çoğunluk işlemini yazılımlar ile yapıyor.

* **Hukuk** Hukukta keşif süreci bir çok insan için boğucu düzeydedir. Bu süreci otomatikleştirmek zaman ve enerji kazancı sağlar. Startup'lar bilgisayar sistemleriyle sundukları soru-cevap sistemleriyle taksonomi ve ontoloji araştırmasını veritabanı üzerinde yapabiliyor.

* **Üretim** Bu alanda uzun süredir robotların kullanımı egemendi. Ancak yeni gelişmeler ile öğrenen ve adapte olan yeni robotlar geliştirilebiliyor.

### Güvenlik ve Etik Sorunlar

Yapay zeka'nın kendi kendini süren otomobiller alanında uygulanması, etik kaygıların yanı sıra güvenliği endişesini de artırır. Arabalar saldırıya uğrayabilir ve otonom bir araç bir kazaya karıştığında sorumluluk belli olmayabilir. Özerk araçlar ayrıca bir kazanın kaçınılmaz olduğu bir yere zorlanabilir ve bu da programlamayı hasarı en aza indirgeme konusunda etik bir karar vermeye zorlayabilir.

Diğer bir önemli endişe, yapay zeka araçlarının kötüye kullanılması potansiyelidir. Hacker'lar hassas sistemlere erişmek için yapay zekayı kullanabilir ve güvenlik alanında şifreleme sistemlerinde uzun süredir yaşanan evrimsel yarışın kızışmasına sebep olabilir.

Derin öğrenmeye dayalı video ve ses üretme araçları ayrıca, taklitçilere **deepfake** (derin sahtelik) oluşturmak için gerekli araçları sağlayarak, ünlü kişileri hiçbir zaman söylemediği ya da yapmadığı şeyleri yapmış ya da söylemiş gibi gösterebilir.

![Deepfake](/assets/deep-learning-deepfake.jpeg)

### Yapay Zeka Düzenlemeleri

Bu potansiyel risklere rağmen, yapay zeka araçlarının kullanımını düzenleyen çok az düzenleme vardır ve yasaların bulunduğu yerlerde, tipik olarak sadece dolaylı olarak yapay zeka ile ilgilidir. Örneğin, Federal Adil Borç Verme düzenlemeleri, finansal kuruluşların, kredi kararlarını potansiyel müşterilere açıklamalarını gerektirir; bu, borç verenlerin, doğası gereği tipik olarak şeffaf olan derin öğrenme algoritmalarını kullanma derecesini sınırlar. Avrupa’da GDPR, işletmelerin tüketici verilerini nasıl kullanabileceği konusunda katı sınırlamalar getirmekte ve bu da tüketiciye yönelik birçok yapay zeka uygulamasının eğitimini ve işlevselliğini engellemektedir.

2016 yılında, Ulusal Bilim ve Teknoloji Konseyi, devlet düzenlemelerinin yapay zeka'nın geliştirilmesinde oynayabileceği olası rolü inceleyen bir rapor yayınladı, ancak özel mevzuatın dikkate alınmasını önermedi. O zamandan beri mesele milletvekillerinden çok az ilgi gördü.

---

## Makine Öğrenmesi

![Makine Öğrenmesi](/assets/deep-learning-ml.jpeg)

Makine öğrenmesinde belirli algoritmalar kullanılarak özellikle programlanmadan daha başarılı tahminler yürütmek hedeflenmektedir. En temel amaç alınan bir girdi için statik analiz yöntemlerini kullanarak bir çıktı üretmektir.

Bu süreçte kullanılan yöntemler veri madenciliği ve tahminsel modellemeye benzer. Bu ikisinde de veriler tekrarlanan şablonlar için aranır program davranışları buna göre düzenlenir. Hepimiz internette gördüğümüz reklamların önceki alışverişlerimize ne kadar uyum içinde olduğunu görmüşüzdür, bir de o ürünleri satın aldığımızı ve artık görmeye ihtiyacımız olmadığını anlayabilseler (Google'ın maillerimizi okuyarak bu konuda ilerleme kaydettiği dedikoduları dolaşıyor). Bu işi yapan tavsiye motorları, biz de makalenin sonlarına doğru basit ama işe yarar bir tavsiye sistemi geliştireceğiz. Makine öğrenmesi ayrıca sahtecilik tespiti, spam filtreleri, ağ güvenlik tehdit tespiti ve tahminsel bakım gereksinimi gibi alanlarda kullanılır.

### Peki nasıl çalışıyor

Makine öğrenmesi temelde **Denetimli** ve **Denetimsiz** öğrenme olarak ikiye ayrılır.

#### Denetimli Öğrenme

Bir veri analisti sisteme girdiler ve çıktılar konusunda yardımcı olur, ayrıca tahminlerin başarı oranlarını da ayarlar. Analist parametreler ve özelliklere karar verip sistemi başarılı hale getirir. Bu eğitimden sonra sistem kendi kararlarını verebilir hale gelir.

Bu yaklaşımda temel hedef bir tahmin fonksiyonu olan *h(x)*'i bulmaktır. Öğrenme, bu fonksiyonu istenilen sonuçları verecek şekilde karmaşık matematiksel algoritmaları kullanarak optimize etme aşamasında olur.

Diyelim ki evlerin fiyatını tahmin edecek bir model üzerinde çalışıyoruz. Evin kaç metrekare olduğu (x1), kaç oda olduğu (x2), kaç banyosu olduğu (x3), kaç kat olduğu (x4), hangi yıl yapıldığı (x5), bulunduğu mahalle (x6) hepsi birer parametredir. Bu tüm parametreleri temsil eden bir *x* parametresini kabul edersek:

h(x) = &theta;<sub>0</sub> x + &theta;<sub>1</sub>x

Amacımız yukarıdaki formülde yer alan &theta;<sub>0</sub> ve &theta;<sub>1</sub> sabit değerleri için en doğru tahmini üreten mükemmel değerleri bulabilmek.

Tahmin fonksiyonu *h(x)* optimize etmek için eğitim örnekleri kullanılır. Her eğitim örneğinde *x_train* girdisi için üretilen sonuç *h(x_train)* önceden bilinen bir *y* değeri ile karşılaştırılır. Yeterli eğitim örneğinden sonra bu farklar bize fonksiyonun *hata* oranını ölçebilmemiz için bir yol sunar. Sonra *h(x)* foksiyonu başarımını &theta;<sub>0</sub> ve &theta;<sub>1</sub> değerleri ile oynayarak arttırabiliriz. Bu süreç &theta;<sub>0</sub> ve &theta;<sub>1</sub> değerleri en iyi değerleri üretene kadar devam eder.

#### Denetimsiz Öğrenme

Bu yaklaşım ise yönlendirmeye ihtiyaç duymaz, genellikle veriler arasındaki ilişkileri incelemek için kullanılır. Eğitim örneği kullanılmayan bu süreçte, bunun yerine sisteme veri kümesi verilir ve aralarındaki korelasyonlar ve şablonları bulması beklenir.

![Denetimsiz Öğrenme Gruplama](/assets/deep-learning-unsupervised.jpg)

Örnek olarak:

* Airbnb firmasının evleri otomatik olarak mahalle gruplarına ayırması
* Amerika'da bir reklamcılık firmasının nüfusu toplumsal istatistikler ve alışveriş alışkanlıklarına göre otomatik gruplayarak reklam verenlerin hedef kitlelerine daha kolay ulaşmasını hedeflemesi gösterilebilir.

---

## Yapay Sinir Ağları

Aşağıdaki gibi, 28x28 piksellik bir ortama bir sayı yazdığımızı düşünelim.

![Sayı Tespit](/assets/deep-learning-28.jpg)

Ekranda hangi sayıyı görüyorsunuz diye sorduğumuzda çok kolay ve ışık hızında 3 cevabını verebiliyoruz. Peki bu tespiti yapabilen bir kod yazmamız istense? Ne kadar zor değil mi? Peki bu kadar kolay yapabilirken bilgisayarlara bu işi yaptırmak neden bu kadar zor?

![Beyin](/assets/deep-learning-brain.jpg)

> Computers are programmed, so are the humans, but the computers can’t act outside their programming, whereas the humans can.”
― Abhijit Naskar, The Constitution of The United Peoples of Earth

Düşünce dediğimiz kavram, beyindeki hücrelere ulaşan elektrokimyasal sinyallere verilen biyokimyasal tepkilerin tümüdür.  Yani aslında bir canlı "düşünmez". "Düşünme" işlemini yapanlar, bu konuda özelleşmiş hücreler topluluğudur.

Beynimiz neredeyse 100 milyar nörondan oluşur ve düşünme süreci bu nöronlar arasındaki haberleşme ile gerçekleşir.

Her nöron kendisinden önceki nörondan (ki buna presinaptik nöron diyoruz) sinyalleri alır. Her nöron, sinyalleri -genellikle- dendritleriyle almaktadır. Sinirbilimde, nöronlar üzerinde taşınan sinyallere Aksiyon Potansiyeli (AP) adı verilir. Dendritlerle alınan sinyaller, somada aksiyon potansiyelinin şiddetine göre bazı biyokimyasal değişimlere sebep olur. Bu değişimler, akson tepeciği (axon hillock) adı verilen, aksonu somaya bağlayan irice bölgede az sonra izah edeceğimiz değişimleri tetikler ve böylece yeni bir AP oluşturulur. Bu AP, akson boyunca akarak ilerler ve akson ucunda, telondendritler adı verilen dallı bölgeye ulaşır. Burada bulunan sinaps adı verilen boşluğa salınan nörotransmitterler (NT) aracılığıyla sinaps sonrası, yani postsinaptik nöron yapısına iletilir. Böylece tek bir AP, nörondan nörona sadece elektrobiyokimyasal süreçlerle iletilmiş olur. Bütün sinir sisteminin işleyişi aynıdır.

![Nöronlar](/assets/deep-learning-neurons.jpg)

Kısacası kendisine iletilen sinyallere göre diğer bağlı nöronları belirli şekilde tetikleyen nöronlar düşünmemizi sağlıyor, *f(x)* ile bir farkı yok!

Tekrar bilgisayarlara dönelim. 28x28 görüntümüzde toplam 784 pikselimiz var, her pikselin ne kadar parladığını yani veri içerdiğini gösteren değerler ile tekrar gösterelim. Burada 1 tamamen aktivasyonu, 0 ise hiç aktif olmamayı temsil etsin.

![Sayı Tespit](/assets/deep-learning-28-2.jpg)

Sorunumuz şimdi biraz daha bilgisayarların anlayabileceği sayısal yapıya dönüştü. Yapısal sinir ağları ise beynimizin nöron bağlantılarını simüle eden bir yapı, bakın aslında ne kadar benziyor.

![Yapay Sinir Ağları](/assets/deep-learning-784.jpg)

Yapay sinir ağları genellikle girdi katmanı, gizli katman ve çıktı katmanından oluşur. Resimdeki örneğimiz 784 adet girdi parametresi alan, iki adet 16 nörondan oluşan gizli katmanından sonra 0..10 arası nöronlarda çıktı üreten bir yapı. Peki nasıl çalışıyor?

Yukarıdaki resimde görebileceğiniz gibi, 2'ye benzer karalama belirli nöronlarda daha fazla parlamaya sebep olarak yayılıyor ve sonuçta en fazla **2** sayısında parlamaya sebep oluyor. Bu da bize girdinin **büyük ihtimall** 2 olduğunu söylüyor. Hemen bir yanılgıyı düzelterek başlayalım, her bir nöron girdiye sabit çıktı veren statik bir yapı değil, *f(x)* fonksiyonu gibi çalışan, girdiye göre farklı çıktı üreten dinamik bir yapıdır. Örneğimizde iki adet 16 adet nörondan oluşan gizli katman kullandık. Bu tamamen keyfi bir seçenek. Girdi katmanındaki tüm nöronlar birinci katmandaki 16 nörona bağlı, onlar da ikinci katmandaki 16 nörona bağlı. Bu ikinci katmanda en son çıktı katmanında sonuçları parlatıyorlar. Ne kadar parlak, o kadar büyük ihtimalle o sayı yazılmış.

Peki bu gizli katmanlar ne yapıyor?

![Layer](/assets/deep-learning-nn-layer1.jpg)

İdeal sistemde, son katmanın yukarıdaki resimde görebileceğiniz sayıları oluşturan küçük parçaları temsil ettiklerini düşünebiliriz. Ancak girdiye bakarak direk çember gibi kompleks şekilleri tespit etmek zor olacaktır. Belki de ilk katman bu şekilleri temsil eden mikro şekilleri tespit ediyor olabilirler mi?

![Layer](/assets/deep-learning-nn-layer2.jpg)

Peki bu şekilleri nasıl tespit edebiliriz? Bir bölgeyi bizim nöronumuz için önemli kabul edip (yeşil bölge), girilen şekildeki aynı bölge ile karşılaştırma yapılabilir.

![Şekil Kontrol](/assets/deep-learning-zone-check.jpg)

Tabi bu yaptığımız kontrol sonuç için çok etkili de olabilir, belki de diğer nöronlardan gelen verilere göre daha az etkili olabilir.

![Weight Bias](/assets/deep-learning-weight-bias.jpg)

Burası çokomelli, tüm mantık buradaki formüle dayanıyor. Bize gönderilen girdiler, bağlı oldukları nöronlar için yukarıda dediğimiz gibi önemli ya da önemsiz olabilir. Resimdeki nöronumuz sadece resimde yeşil ile işaretlediğimiz piksellerden pozitif etkilenip, kırmızı işaretlilerden ise negatif etkilenmeli. Bunu sağlayabilmek için *ağırlık (weight)* katsayısı kullanılıyor. Yukarıdaki resimde yer alan formülü açarsak, bağlı tüm nöronların parlaklıları ile bizim için bu nöronun önemini temsil eden *weight* değerini çarpıp topluyoruz. Resim için rastgele seçim yaparak bir değer elde edelim.

```math

n = 0.1x1 + 0.2x2 + 1x8 + 1x10 + 1x10
n = 28.5

```

Hmm, değerimiz bizim kabul ettiğimiz 0 - 1 aralığı dışında kaldı! Öncelikle bu yüksek değer üzerinde çalışırken, bu değerin *ne kadar yüksek* olduğunda aktif kabul edileceğini belirttiğimiz *önyargı (bias)* denilen bir değer kullanıyoruz. Diyelim ki kabul ettiğimiz bias değeri 15. Elde ettiğimiz sonuç 13.5 olacak, halen 0 -1 aralığı dışında!

Burada hesaplanan değerin *ne kadar aktivasyona sebep olduğu* yani parlamayla sonuçlandığını bizim belirlediğimiz sınırlara almamızı sağlayan bir fonksiyon kullanıyoruz. Önceden bu iş için aşağıda grafiğini görebileceğimiz sigmoid fonksiyonlar kullanılıyordu.

![Sigmoid](/assets/deep-learning-sigmoid.jpg)

Bu fonksiyon gördüğünüz gibi bizim 28.5 değerimiz için 1'e çok yakın bir değer dönecek, bu da nöronumuzun ışıl ışıl parlamasını sağlayacak.

![RELU](/assets/deep-learning-relu.jpg)

Günümüzde Sigmoid yerine Rectified Linear Unit (RELU) daha çok tercih edilen bir yöntem. Bu fonksiyon ise, aldığı girdiye yanıt olarak sıfırdan küçük değerler için 0, büyük değerler için ise belirlenmiş sınırı aşmayan (örneğimiz için 1) bir değer dönüyor.

Şimdi bir sıkıntımız var, belki farketmişsinizdir:

```math
784x16 + 16x16 + 16x10 = 13.002 weight

16 + 16 + 10 = 42 bias
```

Çok fazla değer belirlenmesi gerekiyor, net olmak gerekirse 13.044 adet. Yapay sinir ağımızın düzgün çalışması için ev fiyatı probleminde olduğu gibi bu değerleri el ile düzeltip başarılı sonuçlar elde etmek çok büyük bir iş halini alıyor. Bu arada aşağıdaki resimde görebileceğiniz gibi aktivasyon formülünü üstteki karışık hali yerine altta yer alan matris dönüşümü şeklinde temsil etmek tercih edilir.

![Matris](/assets/deep-learning-matrix.jpg)

Böylece aşağıdaki gibi bir formül elde ederiz.

a<sup>(1)</sup>=σ(Wa<sup>(0)</sup> + b)

Bu formül bizim yapımızı koda çevirmemizde çok faydalı oluyor.

Sorunumuza dönelim, bu *weight* ve *bias* değerlerini verecek zamanı insanlar nereden buluyorlar? İşte öğrenme kısmı burada devreye giriyor, sistem bu değerleri **backpropagation** denilen yöntem sayesinde kendisi ayarlıyor.

Sayıları tespit edebilecek bir yapay sinir ağını eğitebilmek için binlerce el yazması örneğe ve tabii ki çizime karşılık gerçek değere ihtiyacımız var. Şanslıyız ki [MNIST](http://yann.lecun.com/exdb/mnist/) bize on binlerce örnek içeren hazır veri tabanı sunuyor. Açıkçası yapay zeka, makine öğrenmesi gibi konuları araştırdıkça insanların inanılmaz paylaşımları beni en çok etkileyen şey oldu.

Daha fazla detaya girmeden bir yerde kesmem gerekiyor, biraz daha ilerlersek konumuz giriş seviyesinden sinir ağlarından ileri matematiğe dönüşüyor. Umarım temel mantık olarak sistemin büyüleyici çalışma mantığına biraz ışık tutabilmişimdir.

---

## Derin Öğrenme

Derin öğrenmenin makine öğrenmesinin bir alt dalı olduğundan bahsetmiştik. Genellikle, insanlar derin öğrenme terimini kullandıklarında, çok katmanlı yapay sinir ağlarına ve derin destekli öğrenmeye atıfta bulunurlar.

Derin yapay sinir ağları, görüntü tanıma, ses tanıma, öneri sistemleri, doğal dil işleme vb. birçok önemli sorun için doğruluk alanında rekorlar kıran bir dizi algoritmadır. Örneğin, derin öğrenme DeepMind'in AlphaGo'sunun bir parçasıdır. 2016'nın başında Go'da eski dünya şampiyonu Lee Sedol'ü ve 2017'nin başında şu anki dünya şampiyonu Ke Jie'yi yenen algoritma.

![Deep Learning](/assets/deep-learning-deep.jpg)

Derin bir teknik terimdir. Bir yapay sinir ağındaki katman sayısını ifade eder. Sığ bir ağda sözde bir gizli katman vardır ve derin bir ağda birden fazla var. Birden çok gizli katman, derin sinir ağlarının, özellik hiyerarşisi adı verilen verilerdeki özelliklerini öğrenmesini sağlar, çünkü basit özellikler (örneğin iki piksel), daha karmaşık özellikler (örneğin bir çizgi) oluşturmak için bir katmandan diğerine yeniden birleşir. Çok katmanlı ağlar, girdi verilerini (özellikler), birkaç katmanlı ağlara göre daha fazla matematiksel işlemden geçirir ve bu nedenle, hesaplama işlemi daha yoğundur. Hesaplama yoğunluğu, derin öğrenmenin en önemli özelliklerinden biridir ve çok yüksek işlem gereksinimi GPU'ların derin öğrenme modellerini eğitmek için revaçta olmasının nedenidir.

---

## Doğal Dil İşleme (NLP)

![NLP](/assets/deep-learning-nlp.jpg)

Doğal dil işleme (NLP), insan dilini otomatik olarak analiz etmek ve temsil etmek için hesaplama algoritmaları oluşturmakla ilgilenir. NLP tabanlı sistemler, Google’ın güçlü arama motoru ve daha yakın zamanda Amazon’un Alexa adlı ses yardımcısı gibi çok çeşitli uygulamaları mümkün kılmıştır. NLP ayrıca makinelere, makine çevirisi ve diyalog oluşturma gibi doğal dille ilgili karmaşık işleri yapma yeteneğini öğretme konusunda da faydalıdır.

Uzun süre boyunca NLP problemlerini incelemek için kullanılan yöntemlerin çoğu sığ makine öğrenme modelleri ve zaman alan el ile hazırlanan özellikler kullandı. Bu, dilsel bilginin seyrek temsillerle (yüksek boyutlu özellikler) temsil edilmesinden dolayı boyutsallık laneti (curse of dimensionality) gibi sorunlara yol açar. Bununla birlikte, son zamanlarda ortaya çıkan popülerlik ve kelime yerleştirmelerin başarısı (düşük boyutlu, dağıtılmış gösterimler) ile, sinir temelli modeller, SVM veya lojistik regresyon gibi geleneksel makine öğrenme modelleriyle karşılaştırıldığında, dille ilgili çeşitli görevlerde üstün sonuçlar elde etmiştir.

### Dağıtılmış Gösterimler

Daha önce de belirtildiği gibi, el ile hazırlanan özellikler temelde sinirsel yöntemler ortaya çıkıncaya kadar doğal dil görevlerini modellemek için kullanıldı ve boyutsallık laneti gibi geleneksel makine öğrenimi modellerinin karşılaştığı sorunların bazılarını çözdü.

**Kelime Gömmeleri:** Kelime gömme olarak da adlandırılan dağılım vektörleri, sözde dağıtım hipotezine dayanır - benzer bağlamda görünen kelimeler benzer bir anlama sahiptir. Kelime gömme işlemleri, genellikle sığ bir sinir ağı kullanarak amacına, bağlamına dayalı bir kelimeyi tahmin etmeyi amaçlayan bir görev için önceden eğitilmiştir.

Kelime vektörleri sözdizimsel ve anlamsal bilgileri yerleştirme eğilimindedir ve duyarlılık analizi ve cümle kompozisyonu gibi çok çeşitli NLP görevlerinde SOTA'dan (state-of-the-art) sorumludur.

**Word2vec:** 2013 civarında, Mikolav ve diğerleri, hem CBOW hem de skip-gram modellerini önerdiler. CBOW, kelime gömmeleri oluşturmak için sinirsel bir yaklaşımdır ve amaç, belirli bir kapsamda bağlam sözcükleri verilen hedef sözcüğün şartlı olasılığını hesaplamaktır. Öte yandan, skip-gram, merkezi bir hedef kelime verildiğinde, çevreleyen bağlam sözcüklerini (yani koşullu olasılık) tahmin etmeyi amaçlayan kelime yerleştirmelerini oluşturmak için sinirsel bir yaklaşımdır. Her iki model için de, gömme boyutu kelimesi tahminin doğruluğunu hesaplayarak (denetimsiz bir şekilde) belirlenir.

**Karakter Gömmeleri:** Konuşma bölümleri (POS) etiketleme ve adlandırılmış varlık tanıma (NER) gibi görevler için, karakterler veya bunların kombinasyonları gibi sözcüklerdeki morfolojik bilgilere bakmak yararlı olacaktır. Bu, Portekizce, İspanyolca ve Çince gibi morfolojik açıdan zengin diller için de faydalıdır. Metni karakter düzeyinde analiz ettiğimizden, bu tür gömmeler, bilinmeyen kelime sorunuyla baş etmeye yardımcı olur çünkü artık verimli hesaplama amacıyla azaltılması gereken büyük kelime hazinelerine sahip dizileri temsil etmiyoruz.

### Konvolüsyonlu Sinir Ağı (CNN)

Bir CNN temel olarak daha üst düzey özellikleri çıkarmak için kelimeler veya n-gramları oluşturmak için uygulanan bir özellik işlevini temsil eden bir sinir temelli yaklaşımdır. Ortaya çıkan soyut özellikler, diğer görevlerin yanı sıra duyarlılık analizi, makine çevirisi ve soru cevaplama için etkili bir şekilde kullanılmıştır. Collobert ve Weston, CNN tabanlı çerçeveleri NLP görevlerine uygulayan ilk araştırmacılar arasındaydı. Yöntemlerinin amacı, kelimeleri ağın eğitimi sırasında ağırlıklarını öğrenen ilkel bir kelime gömme yaklaşımıyla sonuçlanan bir arama tablosu aracılığıyla bir vektör temsiline dönüştürmektir.

### Tekrarlayan Sinir Ağı (RNN)

RNN'ler, sıralı bilgilerin işlenmesinde etkili olan, sinirsel tabanlı özel yaklaşımlardır. Bir RNN, önceki hesaplanan sonuçlarda şartlandırılmış bir giriş dizisinin her örneğine tekrarlı olarak bir hesaplama uygular. Bu diziler tipik olarak tekrarlayan bir birime sırayla (birer birer) beslenen sabit boyutlu bir belirteç vektörü ile temsil edilir.

### Özyinelemeli Sinir Ağı

RNN'lere benzer şekilde özyinelemeli sinir ağları sıralı verileri modellemek için doğal mekanizmalardır. Bunun nedeni dil, kelimelerin ve alt cümleciklerin bir hiyerarşideki diğer üst seviye cümleleri oluşturduğu özyinelemeli bir yapı olarak görülebilir. Böyle bir yapıda, yaprak olmayan bir düğüm, tüm alt düğümlerinin temsili ile temsil edilir.

### Takviyeli Öğrenme

Takviyeli öğrenme, aracıların bir ödüllendirme sistemiyle ayrık eylemleri gerçekleştirmeleri için eğiten makine öğrenme yöntemlerini kapsar. Takviye öğrenmesi kullanılarak, metin özetleme gibi çeşitli doğal dil üretimi (NLG) görevleri araştırılmaktadır.

### Denetimsiz Öğrenme

Denetimsiz cümle temsili öğrenme, cümleleri denetlenmeyen bir şekilde sabit boyutlu vektörlerle eşleştirmeyi içerir. Dağıtılmış gösterimler dilden semantik ve sözdizimsel özellikleri yakalar ve yardımcı bir görev kullanılarak eğitilir.

### Derin Üretken Modeller

Varyasyonlu oto-kondansatörler (VAE'ler) ve üretken ters ağlar (GAN) gibi derin üretken modeller, gizli bir kod uzayından gerçekçi cümleler üretme sürecinde doğal dilde zengin yapıyı keşfetmek için NLP'de de uygulanır.

### Bellek-Artırılmış Ağ

Belirteç oluşturma aşamasında dikkat mekanizması tarafından erişilen gizli vektörler, modelin “dahili hafızasını” temsil eder. Yapay sinir ağları ayrıca görsel KG, dil modelleme, POS etiketleme ve duyarlılık analizi gibi görevleri çözmek için bir çeşit hafıza ile birleştirilebilir. Örneğin, KG görevlerini çözmek için, modele bir destek şekli olarak destekleyici gerçekler veya sağduyu bilgisi sağlanır. Dinamik bellek ağları, önceki bellek tabanlı modellere göre bir gelişme, giriş gösterimi, dikkat ve cevaplama mekanizmaları için sinir ağı modelleri kullandı.

### Başlıca NLP Sorunları

![NLP Problemleri](/assets/deep-learning-nlp-problems.jpg)

NLP ve Makine Bilişinde çok iyi noktalara kadar gelindi, ancak yine de, özellikle bir sistem içindeki veriler tutarlı olmadığında, üstesinden gelinmesi gereken bazı zorluklar vardır. Bu tutarsızlık aslında makinenin çeşitliliği ve öznelliği yakalamasına izin verirken, makine öğreniminin ilk aşamasının bir parçası değildir. Aşağıda, NLP için makine öğrenme sürecinde karşılaşılan adımlar ve bazı zorluklar yer almaktadır:

#### Cümleyi kırmak

Resmen “cümle sınırı belirsizliği” olarak adlandırılan bu kırma sürecinin elde edilmesi artık zor değil, ancak yine de, özellikle yapılandırılmış bilgileri içeren yüksek yapılandırılmamış veriler söz konusu olduğunda kritik bir süreçtir. Bir kırılma başvurusu paragrafları uygun cümle birimlerine ayırmak için yeterince akıllı olmalıdır; Bununla birlikte, oldukça karmaşık veriler her zaman kolayca tanınabilir cümle formlarında bulunmayabilir. Bu veriler, bir insanın metni yorumlamaya yaklaşacağı gibi anlamlarını elde etmesi için makinenin uygun şekilde işlenmesi gereken tablolar, grafikler, notlar, sayfa sonları vb. Şeklinde olabilir.

#### Konuşma bölümlerini etiketleme (POS) ve bağımlılık grafikleri oluşturma

İnsanlar az ya da çok söyleneni anlar; bir dilin normal çalışılmasından başka, konuşma veya okumadaki konuşmanın belirli parçalarını tek tek anlamaya gerek yoktur. Bir makinenin öğrenmesi için, her kelimenin uygunluğunu, yani kelimenin kendini cümleye, paragrafa, belgeye veya ana yapıya nasıl konumlandırdığını resmi olarak anlamalıdır. Genel olarak, NLP uygulamaları, belirli bir metinde her kelimeye veya sembole bir POS etiketi atayan bir POS etiketleme araçları seti kullanır. Daha sonra, her kelimenin bir cümle içindeki konumu, aynı prosedürde üretilen bir bağımlılık grafiği ile belirlenir. Bu POS etiketleri ayrıca anlamlı tek veya birleşik kelime terimleri oluşturmak için işlenebilir.

#### Uygun sözlüğü oluşturmak

Bu POS etiketlerini ve bağımlılık grafiklerini kullanarak, güçlü bir sözlük üretilebilir ve daha sonra makine tarafından insan anlayışıyla karşılaştırılabilecek şekilde yorumlanabilir. Aşağıdaki paragrafı göz önünde bulundurun:

“Tüm çalışanlar, risk yönetiminden sorumludur, Kurul'a ait nihai hesap verebilirlik ile. Tüm çalışanlar için açık ve tutarlı bir iletişim ve uygun bir eğitim ile birleştirilen güçlü bir risk kültürüne sahibiz. Grup genelinde yönetişim ve ilgili risk yönetimi araçlarıyla birlikte kapsamlı bir risk yönetimi altyapısı uygulanmaktadır. Bu çerçeve risk kültürümüz tarafından destekleniyor ve HSBC Değerleri tarafından destekleniyor.” - HSBC annual report 2017

Cümleler genellikle sıradan bir NLP programı tarafından ayrıştırılacak kadar basittir. Ancak, gerçek değerde olması için, bir algoritmanın en azından aşağıdaki sözlük terimlerini üretebilmesi gerekir:

Çalışanlar; Risk yönetimi; Üstün sorumluluk; Yönetim Kurulu; Güçlü risk kültürü; Açık ve tutarlı iletişim; Tüm çalışanlar için uygun eğitim; Kapsamlı risk yönetimi çerçevesi; Yönetişim ve ilgili risk yönetimi araçları; Altyapı; Risk kültürü; HSBC değerleri.

Maalesef, NLP yazılım uygulamalarının çoğu, karmaşık bir sözlük oluşturmayı başaramaz.

#### Farklı kelime bileşenlerini bağlama

Son zamanlarda, belgeden üretilen iki sözlük terimi arasındaki bağlantının çıkarılmasını gerçekleştirebilecek yeni yaklaşımlar geliştirilmiştir. Vektör uzayı temelli bir model olan Word2vec, bir belgedeki her bir kelimeye vektörler atar, bu vektörler sonuçta her bir kelimenin yakından ortaya çıkan kelimelerle veya kelimelerle olan ilişkisini yakalar. Ancak Word2vec gibi istatistiksel yöntemler, kelime terimlerinin çiftleri arasındaki dilbilimsel veya anlamsal ilişkileri yakalamak için yeterli değildir.

Yukarıda belirtilen örnekte “Tüm çalışanlar, risk yönetiminden sorumludur, Kurul'a ait nihai hesap verebilirlik ile”, sözlükten iki terim, “Kurul” ve “risk yönetimi”, aslında Kurulun nihai sorumluluğa sahip olması ile bağlıdır ancak bu iki terim istatistiksel olarak uzak olduğu için, bu çift arasındaki ilişki bağının kapsamı ne dilsel ne de anlamsal olarak tespit edilemez. Sadece kelimelerle değil, kelime terimleri arasındaki ilişki bağlarını yakalamak için daha karmaşık bir algoritmaya ihtiyaç vardır.

#### İçeriği ayarlama

Tüm NLP sürecindeki en önemli ve zorlu görevlerden biri, bir makineyi bir belgedeki tartışmadan bağlam türetmek için eğitmektir. Aşağıdaki iki cümleyi düşünün:

“Bir bankada çalışmayı seviyorum.”

“Bir nehir kıyısında bankta çalışmaktan keyif alıyorum.”

Bu cümlelerin bağlamı aslında oldukça farklı. Bugün bir makineyi cümleler arasındaki farklılıkları anlama konusunda eğitmeye yardımcı olacak birkaç yöntem vardır. Popüler yöntemlerden bazıları, örneğin istatistiksel hesaplamalara dayanarak her iki olasılığın ortaya çıkacağı, özel hazırlanmış bilgi grafiklerini kullanır. Yeni bir belge geldiğinde, makine devam etmeden önce ayarı belirlemek için grafiğe başvurur.

Bilgi grafiğinin oluşturulmasındaki zorluklardan biri alan spesifikliğidir. Bilgi grafikleri, pratik anlamda evrensel hale getirilemez. Yukarıdaki örnekte “bir bankada çalışmanın tadını çıkarın”, “iş, iş ya da meslek” anlamına gelirken, “bir nehir yakınında bankta oturmak” sadece bir nehir bankası yakınında gerçekleştirilebilecek bir iş veya faaliyettir. Farklı alanlarda tamamen farklı bağlamlara sahip iki cümle, yalnızca bilgi grafiklerine dayanmak zorunda kalırsa makineyi şaşırtabilir. Bu nedenle, bağlam ve uygun alan seçimi elde etmek için olasılıklı bir yaklaşımla kullanılan metotların geliştirilmesi önemlidir.

# Referanslar

- https://evrimagaci.org/sinirbilim-ve-beyin-4-noronlar-nasil-calisir-tipleri-nelerdir-sinirsel-iletim-nasil-saglanir-313
- https://evrimagaci.org/dusunce-nasil-evrimlesti-insan-neden-dusunen-bir-varliktir-dusunmek-ne-demektir-30
- https://www.livestrong.com/article/202078-human-brain-thinking-process/
- http://www.wikizero.biz/index.php?q=aHR0cHM6Ly9lbi53aWtpcGVkaWEub3JnL3dpa2kvQXJ0aWZpY2lhbF9pbnRlbGxpZ2VuY2U
- https://skymind.ai/wiki/ai-vs-machine-learning-vs-deep-learning
- https://www.toptal.com/machine-learning/machine-learning-theory-an-introductory-primer
- https://medium.com/machine-learning-for-humans/unsupervised-learning-f45587588294
- https://www.coursera.org/learn/machine-learning
- https://www.educba.com/machine-learning-vs-neural-network/
- https://www.youtube.com/watch?v=aircAruvnKk
- https://dzone.com/articles/comparison-between-deep-learning-vs-machine-learni
- https://www.zendesk.com/blog/machine-learning-and-deep-learning/
- https://www.educba.com/machine-learning-vs-neural-network/
- http://www.wikizero.biz/index.php?q=aHR0cHM6Ly9lbi53aWtpcGVkaWEub3JnL3dpa2kvRGVlcF9sZWFybmluZw
- https://www.mathworks.com/discovery/deep-learning.html
- http://deeplearning.net/
- http://www.wikizero.biz/wiki/en/Neuro-linguistic_programming
- https://blog.floydhub.com/ten-trends-in-deep-learning-nlp/
- https://medium.com/dair-ai/deep-learning-for-nlp-an-overview-of-recent-trends-d0d8f40a776d
- http://ruder.io/4-biggest-open-problems-in-nlp/
- https://medium.com/datadriveninvestor/what-are-some-of-the-challenges-we-face-in-nlp-today-2e9d94da1f63