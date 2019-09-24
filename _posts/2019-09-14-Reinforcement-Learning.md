---
layout: post
title: Reinforcement Learning
comments: true
redirect_from: "/2019/09/14/Reinforcement-Learning/"
permalink: reinforcement-learning
---

## Nedir

Reinforcement Learning Makine Öğreniminin bir alt dalıdır. Deep Learning yazısında Yapay Zeka ile ilişkiyi gösteren bir resim koymuştum, bu sefer daha da karışık kümeler ile Reinforcement Learning yaklaşımının yerini bir görelim.

![Graph](/assets/reinforcement-learning-graph.jpg)

Belirli bir senaryo için ödülü en üst düzeye çıkarmak için uygun seçimlerin yapılması ile ilgilidir. Belirli bir durumda olması gereken en iyi davranış veya yolu bulmak için çeşitli yazılım ve makineler tarafından kullanılır. Reinforcement Learning, Supervised Learning'den, bir sınavın cevap anahtarına sahip olduğu için farklılık gösterir, böylece model doğru cevapla eğitilir, ancak Reinforcement Learning'de cevap yoktur, ancak sisteme yapılan takviyeler verilen görevi gerçekleştirmek için ne yapılacağına karar verilmesini sağlar. Eğitim veri setinin yokluğunda, deneyiminden ders almak zorunludur.

Bir örnek ile incleyelim. Sorun şu şekilde: Labirent içinde bir robotumuz olsun. Ulaşılabilecek bir noktada da bir ödül. Robotun, ödüle ulaşmak için mümkün olan en iyi yolu bulması gerekiyor. Aşağıdaki gibi:

![Labyrint](/assets/reinforcement-learning-labyrint.png)

Yukarıdaki resimde labirentimizde robot, elmas ve ateş bulunmakta. Robotun amacı, elmas olan ödülü almak ve ateşli engellerden kaçınmaktır. Robot mümkün olan bütün yolları dener ve daha sonra en az engelle karşılaştığı yolu seçerek öğrenir. Her doğru adım robota ödül olarak dönecek ve her yanlış adım robotun ödüllerini bir azaltacak. Toplam ödül, elmas olan son ödüle ulaştığında hesaplanacaktır.

### Ana Noktalar

* Giriş: Giriş, modelin başlayacağı başlangıç durumu olmalıdır.
* Çıktı: Belli bir problemin çözümü için çeşitli çözümler olduğu için olası birçok çıktı vardır.
* Eğitim: Eğitim girdilere dayanır, Model bir **durum** döndürür ve kullanıcı buna bakarak modeli ödüllendirmeye veya cezalandırmaya karar verir.
* Model öğrenmeye devam eder.
* En iyi çözüme, maksimum ödüle göre karar verilir.

Reinforcement Learning sık sık Supervised Learning ile karşılaştırılır ya da aynı oldukları düşünülür. Farklarını bir tablo ile açıklamaya çalışalım.

| Reinforcement Learning | Supervised Learning |
|-|-|
|Reinforcement Learning tamamen kararları sırayla almakla ilgilidir.<br>Basit bir deyişle, bunun mevcut girişin durumuna ve sonraki girişin önceki girişin çıkışına bağlı olduğunu söyleyebiliriz.|Supervised Learning'de ise, ilk girdi veya başlangıçta varolan girdi ile karar verilir.|
|Reinforcement Learning kararı birbirine bağlıdır, bu yüzden bağımlı karar adımlarına etiketler verilir.|Supervised Learning'de ise kararlar birbirinden bağımsızdır, bu nedenle her bir karar için etiketler verilir.|
|Örnek: Satranç oyunu.|Örnek: Obje tanımlama.|
|||

### İki çeşit güçlendirme (reinforcement) vardır

* Pozitif:<br><br>
Pozitif Güçlendirme, bir olay belirli bir davranış nedeniyle meydana geldiği zaman, aynı davranışın gücünü ve sıklığını arttırdığı şeklinde tanımlanır. Diğer bir deyişle, davranış üzerinde olumlu bir etkiye sahiptir.<br><br>
Avantajları:

* Negatif:<br><br>
Negatif Güçlendirme, bir davranışın güçlendirilmesi olarak tanımlanır, çünkü negatif bir durum ortadan kalkmış veya önlenmiştir.

### Aşağıdaki senaryolarda uygulanabilir

* Bir çevre modeli bilinmektedir, ancak analitik bir çözüm mevcut değildir
* Çevrenin sadece simülasyon modeli verilmiştir (simülasyon-temelli optimizasyonun konusu)
* Çevre hakkında bilgi toplamanın tek yolu onunla etkileşime girmektir.

---

## Karşılaşabileceğimiz Bazı Terimler

* **Agent:** Bir aracı işlem yapar; örneğin, teslimat yapan bir dron ya da bir video oyununda dolaşan Süper Mario. Algoritma aracıdır. Hatta hepimiz birer aracıyız.

* **Action (A):** Eylem, aracının yapabileceği tüm olası hareketlerin kümesidir. Bir eylem neredeyse açıklama gerektirmez, ancak temsilcilerin olası eylemler arasından seçim yaptıklarını belirtmek gerekir. Video oyunlarında liste sağa veya sola koşma, yüksek veya alçak atlama, çömelme veya hareketsiz durmayı içerebilir.

* **Discount factor (γ):** İndirim oranı, aracının işlem seçimindeki etkisini azaltmak için aracı tarafından keşfedilen gelecekteki ödüller ile çarpılır. Neden mi? Gelecekteki ödülleri, önceki ödüllerden daha az değerde yapmak amaçlanır; yani, aracıda bir tür kısa vadeli hedonizm uygulanır. Genellikle Yunanca gama harfi (γ) ile ifade edilir. Eğer γ 0,8 ise ve 3 adımdan sonra 10 puanlık bir ödül varsa, o ödülün bugünkü değeri 0,8³ x 10'dur.

* **Environment:** Çevre, aracının içinden bulunduğu dünyadır. Aracının mevcut durumunu ve eylemini girdi olarak alır ve aracının ödülünü ve bir sonraki durumunu çıktı olarak döndürür. Biz insanları aracı olarak görürsek, fizik yasaları ve eylemlerimizi işleyen ve bunların sonuçlarını belirleyen toplumun kuralları çevre olarak kabul edilebilir.

* **State (S):** Durum, aracının kendisini bulduğu somut ve acil bir senaryodur. Özel bir yer ve zamanda, aracının başka önemli araçlar/engeller/düşmanlar/ödüller ile karşılaşması **Durum** kabul edilir. Çevrenin o anki hali veya gelecekteki herhangi bir ihtimal de olabilir. Hiç yanlış zamanda yanlış yerde bulundunuz mu? İşte buna durum deniyor.

* **Reward (R):** Ödül, bir aracının işlemlerinin başarısını veya başarısızlığını ölçtüğümüz geri bildirimdir. Örneğin, Super Mario video oyununda, Mario bir madeni paraya dokunduğunda puan kazanır. Herhangi bir anda, bir aracı eylemler şeklinde çıktıları çevreye gönderir ve çevre, varsa bir önceki duruma göre hareket etmenin sonucu olarak aracının yeni durumunu ve varsa ödüllerini döndürür. Ajanın eylemini etkin bir şekilde değerlendirmeyi sağlar.

* **Policy (π):** Hareket tarzı, mevcut duruma dayalı olarak bir sonraki eylemi belirlemek için aracının uyguladığı stratejidir. Durumları en yüksek ödülü vaat eden eylemlere eşler.

* **Value (V):** Değer, kısa vadeli ödül R'nin ötesinde, indirimle beklenen uzun vadeli getiridir. ***Vπ(s)*** formülü ile hesaplanır, burada elde edilen değer **π** hareket tarzısi ile mevcut durumun beklenen uzun vadeli getirisidir.

* **Q-Value [Action-Value] (Q):** Q değeri, aldığı ekstra parametre dışında değer ile aynıdır: mevcut işlem ***a***. Mevcut durumun (**s**), **π** hareket tarzısi ile uzun vadeli dönüşünü hesaplamak için Qπ(s, a) formülü kullanılır. Q, durum-eylem çiftlerini ödüllerle eşler.

---

## Reinforcement Learning Proje Adımları

Şimdi süreci daha iyi anlayabilmek için bir Python not defterinde basit bir görevi tam olarak tanımlayıp çözerek yeni bir Reinforcement Learning projesi nasıl oluşturulur görelim. Geliştirme için tamamen [Kaggle](https://www.kaggle.com/) kullanılabilir.

### Kapsam

Reinforcement Learning ilk öğrenilmeye başlandığında, özellikle örnek kodlar ve projeler kullanıldıysa çoğunluk kaybolmuş hisseder ve kafalar çok karışır. “Neden sonuçlar bunu gösteriyor? Bu parametre ne yapar? Çevre bu şekilde ne işliyor?” gibi sorular gündeme gelir. Geri bir adım atıp, ilk önce olasılıksal ortamın nasıl tanımlandığını anlamak ve kağıt üzerinde çözülebilecek ufak bir örneği yavaş yavaş geliştirerek ilerlemek bir çok kafa karıştırıcı noktanın aşılmasını sağlayacak ve karışık projelerin anlaşılmasını kolaylaştıracaktır. Bu amaç ile Sterling Osborne, PhD tarafından geliştirilmiş bir örneği [burada](https://www.kaggle.com/osbornep/-reinforcement-learning-from-scratch-in-python) bulabilirsiniz

Biz daha basit bir örnek üzerinden proje adımlarını öncelikle yüzeysel görerek ilerleyelim.

## Bebek Firarda

Amacımız, denetlenmemiş olarak, sadece veriyi sunmak ve sistemin verideki kalıpları bulmasını ve farklı sınıflar/çıkışlar vermesini sağlamak.

Diyelim ki bir bebeğin hayvanları tanımanısını istiyoruz, ne yaparız? Hayvan görüntülerini bebeğe gösterir ve söyleriz, bu köpek, bu kedi, bu eşek vb.… defalarca. Ve nihayet bebek bunları öğrenir ve hayvanları resimlerden tanımaya başlar. Ancak bu süreç tamamen kontrollü ve bizim denetimimiz altında olduğunu biliyoruz. Supervised Learning tam olarak bu oluyor.

Şimdi bebeğin yürüme vakti geldi, ne yapıyoruz?

Başlangıçta bebek yürüyemez, bebeği olabildiğince destekleriz, ama her zaman yanında olayız, böylece bebek kendi başına öğrenmek zorunda kalır. Eninde sonunda bebek odanın karşı ucundaki açık kollara kadar yürümeyi başarır. Bu süreci tamamen gözetim altında yürütemeyiz, çünkü süreçte birçok şeyin düştüğünü biliyoruz. Bebek ileri doğru bir adım atmaya çalıştığında, ayağı bir engele takılabilir veya yuvarlanabilir, kaygan bir zeminde düşebilir. Çevre pek tahmin edilebilir değildir. Bu yüzden bebek bunu deneyimlerden öğrenmelidir (deneme ve yanılma).

Yani aynı şekilde, bir sistemin görevi anlayarak hedefi başarmasını istediğimizde, sistem "dene ve yanıl” kuralını uygulamak zorundadır. Bunu başarmak için denetimli veya denetimsiz öğrenme yöntemlerini kullanmanın gerçekten zor olduğu durumlarda, Reinforcement Learning gibi yeni yöntemlere ihtiyacımız doğar.

Bebeği yürütme örneği üzerinden Reinforcement Learning proje adımlarımızı yüzeysel incelemeye çalışalım, bunlar da aşağıdaki gibi:

* Hedef belirleme
* Çevre tanımlama
* Aracının yapabileceği eylemleri belirleme
* Ödül sisteminin kararlaştırılması (pozitif ve negatif geri bildirim)

### Hedef

Örneğimiz için hedefimiz bebeğin fayans kaplı bir zeminde (çevre), düşmeden 10 adım (eylem) atabilmesi.

### Çevre

Ortamda tutunacak bir şey yok, yerler fayans kaplı ve yürüme sürecine kimse karışmayacak.

### Eylemler

Bebek sağ ayağı ileri itebilir, sol ayağı ileri itebilir, düşebilir ve kalkabilir.

### Ödül Sistemi

Ödül sistemi bizim tarafımızdan kurulmalıdır ve soruna göre farklılaştırılmalıdır.

Bazı problemlerde ödülü **sadece** süreç sonunda verebiliriz (+ ya da -). Bazı durumlarda ise, her bir aksiyon için bir miktar/sabit anlık ödül veririz ve sonunda, aracı başarı için muazzam bir + ödül veya başarısızlık için - bir ödül alır. Aracı, maksimum birikmiş ödülü almayı hedefler. Ödül geri bildirimi, aracıya doğrudan hangi işlemi yapacağını söylemez. Aksine, bazı durumlar ve eylemler dizilerinin ne kadar değerli olduğunu gösterir.

Burada da bebeğe odanın karşısına yürümeyi başardığında bir şeker vereceğiz. Her doğru şekilde atılan adım için ödül vererek de sağlayabiliriz.

---

Bebeğin başlangıç ​​pozisyonu ayaktadır, sonra bebek ileri bir adım atmaya çalışır (sağ veya sol), eğer adım başarılı bir şekilde atılırsa, bebek başka bir adım atmak için diğer bacağını hareket ettirir vb..

Bebeğin herhangi bir adımda düşmesi durumunda, bebeğin 1. adımdan itibaren sayması gerekir, roguelike oyunlar gibi :)

Çok basit bir örnek oldu ancak aşağıdaki vurguyu tekrar yapabiliriz:

Supervised/Unsupervised Learning → Verilerden öğrenme
Reinforcement Learning → Çevreden öğrenme

Şimdi süreci biraz daha teknik detaylarda inceleyelim.

---

## Markov Decision Process / Markov Karar Süreci (MDP)

MDP'yi RL için bir altyapı veya bir tasarım deseni ya da RL problemlerini çözmek için bir yaklaşım/süreç/teknik olarak düşünebilirsiniz. 

> Eğer MDP çözülürse RL problemi çözülür.

### Markov Property

“Şimdiki zaman bilinirse, gelecek geçmiş zamandan bağımsızdır.”

Yani, aracının mevcut durumunu bilmek, kendi başına hangi eylemin gerçekleştirileceğine karar vermesine yeterli olur (geçmişi bilmek gerekmeden).

Bebek örneğine dönersek: diyelim ki bebek 4 adım attı, sonra da yere düştü. Böylece bebeğin mevcut durumu **düşmüş**. **Düşmüş** durumuna dayanarak bir sonraki en iyi eylem **kalkmaktır** ve bu kararın verilmesi için düşmeden önce atılmış 4 adımın hiç önemi yoktur.

> Olasılık teorisi ve istatistikte, sürecin gelecekteki durumlarının koşullu olasılık dağılımı yalnızca mevcut duruma bağlıysa, stokastik bir süreç Markov özelliğine sahiptir.

Markov karar süreci 5 elemandan oluşur → (S, A, P, R, γ)

**S** → Çevrede oluşabilen bir dizi durum

**A** → Aracının alabileceği eylemler

**P** → Geçiş matriksi (tüm durumlar için)

**R** → Her durum için ödül

**γ** → İndirim oranı ( γ ∈ [0, 1] )

![Matriks](/assets/reinforcement-learning-matrix.jpg)

<sup>Bebeğimiz için örnek bir geçiş matriksi</sup>

Eğer mevcut durum S1 ise (düşmüş), kalkma olasılığı (S2) 0.7'dir. Mevcut durum S3 ise, bebeğin düşme olasılığı bir sonraki aşamaya geçme ile aynıdır (S4).

Şimdilik geçiş olasılığının açıkça verildiğini varsayıyoruz, ancak birçok pratik durumda bunu örneklerden tahmin etmemiz gerekebilir (örneğin, denetlenen öğrenme).

Artık ödülleri ekleyelim. Her adım için aracı bir şeker alır, düşerse tüm şekeri kaybeder, kalkabilirse 2 şeker kazanır, karşıya kadar yürürse (10+ adım) 10 şeker alır.

![Ödüller](/assets/reinforcement-learning-rewards.jpg)

Şimdilik indirim faktörünü görmezden gelelim, çok karışmasın.

---

## Algoritmalar

**Model bazlı ve Modelsiz:** 
Model, çevre dinamiğinin simülasyonunu simgelemektedir. Yani model, T (s1 \| (s0, a)) geçiş olasılığını mevcut durum s0 ve s1 durumuna geçişi sağlayan **a** eylemi çiftinden öğrenir. Geçiş olasılığı başarılı bir şekilde öğrenilirse, aracı mevcut durum ve eylemde verilen belirli bir duruma girme ihtimalini bilir. Bununla birlikte, model tabanlı algoritmalar, durum alanı ve eylem alanı büyüdükçe pratik değildir (tablo halinde bir kurulum için SxSxA kadar yer kaplarlar). Diğer taraftan, modelsiz algoritmalar bilgisini güncellemek için deneme yanılma yöntemini kullanır. Sonuç olarak, tüm durumların ve eylemlerin birleşimini depolamak için bir alan gerektirmez. Değineceğimiz tüm algoritmalar bu kategoriye girmektedir.

**On-Policy & Off-Policy:** Her algoritma, her durumda hangi eylemlerin gerçekleştirileceğine karar vermek için bazı hareket tarzlarını izlemelidir. Yine de, algoritmanın öğrenme prosedürü öğrenirken bu yaklaşımı dikkate almak zorunda değildir. Geçmişteki durum-eylem kararlarını veren hareket tarzı ile ilgili algoritmalar yaklaşım dışı algoritmalar olarak adlandırılırken, onu görmezden gelenler hareket tarzı olmayan olarak bilinir.

### Q-Learning

Q-Learning, ünlü Bellman Denklemine dayanan off-policy, modelsiz bir RL algoritmasıdır.

Q-Learning algoritması, Reinforcement Learning yaklaşımının en çok bilinen algoritmalarındandır. Algoritmadaki temel amaç bir sonraki hareketleri inceleyip yapacağı hareketlere göre kazanacağı ödülü görmek ve bu ödülü maksimize edip buna göre hareket etmektir.

Aracının ödül haritası, gitmesini istediğimiz ya da istemediğimiz yerler daha önceden bizim tarafımızdan belirlenir ve bu değerler ödül tablosuna (Reward Table) yazılır. Aracının tecrübeleri bu ödül tablosuna göre şekillenecektir.

Aracı ödüle giderken her iterasyonda edindiği tecrübeleri gidebildiği yerleri seçerken maksimize etmek için kullanır. Bu tecrübeleri ise Q-Tablosu (Q-Table) adı verilen bir tabloda tutar.

![Q-Tablosu](/assets/reinforcement-learning-qtable.png)

Q-Tablomuz başlangıçta aracımızın hiçbir tecrübesi olmadığı için sıfırlarla doludur ve bu yüzden aracı ilk seçimlerinde Q-Tablosundaki sıfırları maksimize edeceğinden rastgele hareket edecektir. Bu rastgelelik aracının ilk ödülü bulmasına kadar sürecektir. Aracı ödülün olduğu bir yere geldiği anda ödüle gelmeden önceki yerini bilir ve bu yerin değerini kendi tecrübelerini biriktirdiği Q-Tablosuna yazar. Bu yazma işleminin belirli bir algoritması bulunmaktadır.

![Q-Learning Formül](/assets/reinforcement-learning-qlearning-1.png)

> “Q(s,a)” (durum, eylem) dediğimiz değer bizim şu anda {bulunduğumuz, gideceğimiz} dizin, “lr” ise değer öğrenme katsayısı (learning rate),“r(s, a)” değeri bizim {bulunduğumuz, gideceğimiz} ödül tablomuzdaki ödül değerimiz, “Y” değeri gamma, “max (Q (s’,a’))” değeri ise gidebileceğimiz {gideceğimiz yerden gidebileceğimiz} yerlerin en yüksek Q değeridir.

Her seferinde aracı bu algoritmaya göre bir sonraki adımı tahmin edip hareket eder ve ödüle ulaşır. Ödüle ulaştıktan sonra ise ajan tekrardan rastgele bir yerden harekete başlar ve tekrardan ödülü bulmaya çalışır. Bu işlem devam ettikçe ajan hangi durumda nereye gideceğini bilmeye başlar.

### State-Action-Reward-State-Action (SARSA)

SARSA, Q-Learning'e çok benzer. SARSA ve Q-Learning arasındaki temel fark, SARSA'nın on-policy bir algoritma olmasıdır. Bu, SARSA'nın Q-Value değerini gerçekleştirilen eylem ile öğrendiği anlamına gelir.

### Deep Q Network (DQN)

Q-Learning çok güçlü bir algoritma olmasına rağmen, temel zayıflığı genel olmamasıdır. Q-learning'i iki boyutlu bir diziyi (Eylem * Durum Alanı) güncelleyen bir sistem olarak kabul edersek, aslında dinamik programlamaya benzer. Bu, Q-öğrenme ajanının daha önce görmediği durumlarda, hangi eylemde bulunacağına dair hiçbir fikri olmadığını belirtir. Başka bir deyişle, Q-learning aracı görülmemiş durumlar için değer tahmin etme yeteneğine sahip değildir. Bu problemle başa çıkmak için DQN, Yapay Sinir Ağını kullanarak iki boyutlu diziden kurtulur.

### Deep Deterministic Policy Gradient (DDPG)

Her ne kadar DQN, Atari oyunları gibi yüksek boyutlu problemlerde büyük başarı elde etmiş olsa da, eylem alanı hala ayrık. Bununla birlikte, birçok gereksinim için eylem alanı süreklidir. Eylem alanını az bile olsa ayrıklaştırırsanız, çok büyük bir hareket kümesine sahip olursunuz.

ve daha bir çok algoritma kullanımımıza sunulmuş durumdadır...

---

## Kullanım Alanı

Reinforcement Learning yaklaşımını Doğuş Teknoloji bünyesinde aşağıdaki alanlarda kullanabiliriz:

### Sunucu Kümelerinde Kaynak Yönetimi

Sınırlı kaynakları farklı görevlere tahsis etmek için algoritmalar tasarlamak zordur ve insan tarafından üretilen sezgisel tarama gerektirir. Reinforcement Learning ile Kaynak Yönetimi, ortalama bekleme sürelerini en aza indirgemek amacıyla bilgisayar kaynaklarını bekleyen işlere otomatik olarak tahsis etmeyi ve süreci optimize etmeyi hedefler.

Şirketimiz bünyesinde kendi yönettiğimiz bir çok sunucu yanında bulut kaynaklarımız da bulunmakta. Uygulamaların gereksinimlerine göre ölçeklendirilmesi yerine RL kullanılarak çıkarımlarda bulunmak performans ve bütçe konularında çok faydalı olacaktır.

### Robot Sistemleri

Robotik'te RL uygulaması konusunda çok büyük çalışmalar var. Araştırmacılar, ham video görüntülerini robotun eylemleriyle eşleştirmeye yönelik yaklaşımları öğrenmek için bir robotu eğitti. RGB görüntüleri bir CNN'ye (Convolutional Neural Networks - Dönüşümlü Sinir Ağları) beslendi ve sistem bu verileri kullanarak motor tork kontrol çıktıları üretti. RL bileşeni, robotun kendi hareketleri ve sonuçlarında oluşan durumları kendi kendine değerlendirmesi ve öğrenmesini sağladı.

Şirketimizde şimdilik hobi olarak yürütülen otonom araç geliştirmeleri de RL çalışmalarından faydalanabileceğimiz ayrı bir alan.

### Web Sistem Konfigürasyonu

Bir web sisteminde 100'den fazla konfigüre edilebilir parametre vardır ve parametreleri ayarlama işlemi, yetenekli bir operatör ve çok sayıda deneme-yanılma testi gerektirir. Araştırmacıların yaptığı "Çevrimiçi Web Sistemi Otomatik Konfigürasyonuna Reinforcement Learning Yaklaşımı", VM tabanlı dinamik ortamlarda kurulmuş çok katmanlı web sistemlerinde parametrelerin otomatik olarak yeniden yapılandırılması konusunda bu alanda alanındaki başarılı bir çalışma oldu.

Sunucular üzerinde yapılabilecek çalışma web uygulamalarının kendilerinde de yapılabilir. Consul, ZooKeeper gibi sistemler üzerinden okunacak ayarların RL kullanılarak akıllı hale getirilmesi ve projelerin çalışması üzerinde dinamik değişiklikler yapmak ihtimal dahilinde.

### Kişiselleştirilmiş Tavsiye Sistemleri

Haber önerilerinin önceki çalışmaları, haberlerin hızlı değişen dinamiği dahil olmak üzere birçok zorlukla karşılaştı, kullanıcılar kolayca sıkılıyor ve tıklama oranı, kullanıcıların elde tutma oranını yansıtmıyor. Bazı araştırmacılar, problemlerle mücadele etmek için “DRN: DRN: A Deep Reinforcement Learning Framework for News Recommendation” başlıklı bir makalede haber öneri sisteminde RL uygulamıştır.

Doğuş Teknoloji olarak otomotiv ve yeme/içme sektörü müşterilerimize yapacağımız öneriler ve tavsiyeleri geliştirmek adına RL kullanabiliriz.

### Teklif Verme ve Reklamcılık

Alibaba Group araştırmacıları, “Real-Time Bidding with Multi-Agent Reinforcement Learning in Display Advertising” adlı bir bildiri yayınladılar. Taobao platformunda canlı testi. Dağıtılmış küme tabanlı çoklu aracılı teklif çözümlerinin (DCMAB) umut verici sonuçlar elde ettiğini ve bu nedenle Taobao platformunda canlı bir test yapmayı planladıklarını ifade etmişler.

Bir çok kurumsal firma gibi biz de firmalardan ürün ve hizmetler için teklifler alıyoruz ve benzer teklifleri biz de veriyoruz. Piyasada rakiplerin hareketleri ve kararları gibi başka bir çok çevresel etmeni de hesaba katarak bu teklifleri daha akıllı hale getirebiliriz.

Ayrıca web reklamcılığı günümüzdeki çok önemli kullanıcı etkileşim kanalı olmuş durumda. Verilecek reklamların etkileri, nasıl yaklaşım tercih edilmesi gerektiği gibi bir çok konu için RL kullanılabilir.

### Oyunlar

Son zamanlarda Reinforcement Learning ile vidyo oyunlarında insan üstü performanslar, kırılması mümkün görülmeyen rekorların kırılması haberlerini çok duyar olduk.

![RL Oyunlar](/assets/reinforcement-learning-games.png)

Tahminen bunların en ünlüsü AlphaGo ve AlphaGo Zero olmalıdır. Sayısız insan oyunuyla eğitilmiş AlphaGo, yaklaşım olarak değer ağı ve Monte Carlo ağacı araması (MCTS) kullanarak süper insan performansı elde etti. Yine de, araştırmacılar daha sonra tekrar düşündüler ve daha net bir RL yaklaşımı denediler - sıfırdan eğitim. Araştırmacılar, yeni ajan AlphaGo Zero'yu kendi kendine oynatarak eğittiler ve bu yeni sistem AlphaGo (eski) 100-0'u yendi.

Benzer sistemler ile oyun yapay zekaları da eğitilerek akıllı düşmanlar yaratılabilir. Biz de şirket olarak Game Lab insiyatifimizde geliştireceğimiz oyunlarda RL'den faydalanabiliriz.

### Derin Öğrenme

RL ve diğer derin öğrenme mimarisini birleştirmek için her gün giderek daha fazla girişim yapılmakta ve etkileyici sonuçlar elde edilmekte.

RL'deki en etkili çalışmalardan biri, CNN'i RL ile birleştirmek için Deepmind'in öncü çalışmasıdır. Bunu yaparak, ajan çevreyi “yüksek boyutlu duyusal” ile “görme” yeteneğine sahiptir ve daha sonra onunla etkileşime girmeyi öğrenir.

RL ve RNN (Recurrent Neural Network), insanların yeni fikirleri denemek için kullandıkları başka bir kombinasyondur. RNN “hatıraları” olan bir tür sinir ağıdır. RL ile birleştirildiğinde RNN, aracılara işleri ezberleme kabiliyeti verir. Örneğin, Atari 2600 oyunlarını oynamak için Derin Tekrarlayan Q-Ağı (DRQN) oluşturmak üzere LSTM'yi RL ile birleştirdi. Ayrıca kimyasal reaksiyon optimizasyon problemini çözmek için RNN ve RL kullandılar.

Şirket olarak da bünyemizdeki restoranlar için müşteri ve malzeme gereksinimleri tahminlemelerinde RL'den faydalanabiliriz.

---

RL için daha bir çok kullanım alanı bulunmakta, yakın zamanda araştırmacılardan çılgın sonuçlar görebiliriz.

## Referanslar

* [https://en.wikipedia.org/wiki/Reinforcement_learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
* [https://www.geeksforgeeks.org/what-is-reinforcement-learning/](https://www.geeksforgeeks.org/what-is-reinforcement-learning/)
* [https://skymind.ai/wiki/deep-reinforcement-learning](https://skymind.ai/wiki/deep-reinforcement-learning)
* [https://towardsdatascience.com/reinforcement-learning-from-scratch-designing-and-solving-a-task-all-within-a-python-notebook-48c40021da4](https://towardsdatascience.com/reinforcement-learning-from-scratch-designing-and-solving-a-task-all-within-a-python-notebook-48c40021da4)
* [https://medium.com/deep-math-machine-learning-ai/ch-12-reinforcement-learning-complete-guide-towardsagi-ceea325c5d53](https://medium.com/deep-math-machine-learning-ai/ch-12-reinforcement-learning-complete-guide-towardsagi-ceea325c5d53)
* [https://towardsdatascience.com/the-complete-reinforcement-learning-dictionary-e16230b7d24e](https://towardsdatascience.com/the-complete-reinforcement-learning-dictionary-e16230b7d24e)
* [https://towardsdatascience.com/introduction-to-various-reinforcement-learning-algorithms-i-q-learning-sarsa-dqn-ddpg-72a5e0cb6287](https://towardsdatascience.com/introduction-to-various-reinforcement-learning-algorithms-i-q-learning-sarsa-dqn-ddpg-72a5e0cb6287)
* [https://www.topbots.com/most-important-ai-reinforcement-learning-research/](https://www.topbots.com/most-important-ai-reinforcement-learning-research/)
* [https://www.oreilly.com/radar/practical-applications-of-reinforcement-learning-in-industry/](https://www.oreilly.com/radar/practical-applications-of-reinforcement-learning-in-industry/)
* [https://medium.com/deep-learning-turkiye/q-learninge-giri%C5%9F-6742b3c5ed2b](https://medium.com/deep-learning-turkiye/q-learninge-giri%C5%9F-6742b3c5ed2b)
* [https://towardsdatascience.com/applications-of-reinforcement-learning-in-real-world-1a94955bcd12](https://towardsdatascience.com/applications-of-reinforcement-learning-in-real-world-1a94955bcd12)