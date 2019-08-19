---
layout: post
title: Ne izleyelim? - Azure Machine Learning Studio
comments: true
redirect_from: "/2019/08/15/Azure-ML-Recommend/"
permalink: azure-ml-recommend
---

Hangi filmi izlesem, seveceğim yeni bir dizi olsa da başlasam diyor musunuz? [imdb.com](http://imdb.com) üzerinde izlediğiniz film ve dizilere puan veriyor musunuz? Haydi o zaman Azure Machine Learning Studio (kısaca Azure ML diyeceğim) ile yeni tavsiyeler yaptıralım.

## Puanlarımızı indirelim

[imdb.com](http://imdb.com) adresinden verdiğimiz puanlara gidelim (umarım link görülebiliyordur 😀).

![Puanlar](/assets/azure-ml-recommend-ratings.jpg)

Puanlarımızı .csv dosyası olarak indirelim.

![Export](/assets/azure-ml-recommend-export.jpg)

Aşağıdaki gibi bir içeriği olan dosyamızı bir kenara kaldıralım.

![Csv](/assets/azure-ml-recommend-csv.jpg)

Ben 2007 yılından bu güne 1611 dizi ve filme oy vermişim, tabi 2007 öncesi izlediklerimi de hatırlayıp olabildiğince izlediğim her şeye puan vermeye çalışıyorum (ve biraz fazla izliyor olabilirim).

Gerekiyorsa üye olup en azından 100 civarı oy vermeye çalışmanızı tavsiye ederim.

## Azure ML

[Azure ML](https://studio.azureml.net) adresinden giriş yapalım, eğer üye değilseniz **Free Workspace** ile ücretsiz giriş yapabilirsiniz (10 GB limit oluyor). Aşağıdaki gibi bir ekran ile karşılaşmanız gerekiyor.

![Main](/assets/azure-ml-recommend-main.jpg)

Sol altta yer alan **New** butonu ile yeni bir deney oluşturalım.

![New Experiment](/assets/azure-ml-recommend-new.jpg)

Bizi aşağıdaki gibi bir ekran karşılayacak.

![New Experiment](/assets/azure-ml-recommend-experiment.jpg)

Ben ilk iş sol üstte gördüğünüz tarih yerine akılda kalacak bir isim veriyorum, tavsiye ederim çok karışıyor yoksa.

Yine **New** butonu ile indirdiğimiz puanlarımızı **Dataset** olarak yükleyelim.

![New Dataset](/assets/azure-ml-recommend-new-dataset.jpg)

Yükleme tamamlandığında **Dataset** **Saved Datasets** menüsünde **My Datasets** altına gelecek, sürükleyip ortalığa bırakalım.

![Dataset](/assets/azure-ml-recommend-dataset.jpg)

## Dataset

Dataset; ayrılmış, json, excel, parquet, pandas DataFrame, SQL sorgusu ve binary formatlarını destekleyen **veriyi temsil eden** bir yapı. Basitçe verileri temsil eden ortak format gibi görebilirsiniz. Bir Dataset oluşturduğumuzda veriye nasıl ulaşılacağı bilgisi ve kaynağın meta bilgisi kopyalanır, veriler kopyalanmaz. Veri esas kaynağında durur, gerektiğinde yorumlanarak okunur böylece ekstra saklama alanı harcanmaz.

Eklediğimiz puanlarımıza sağ tıklayıp **Visualize** diyerek içeriği görebilirsiniz.

![Dataset Visualize](/assets/azure-ml-recommend-dataset-visualize.jpg)

![Ratings Visualize](/assets/azure-ml-recommend-ratings-visualize.jpg)

Azure ML bize bir çok hazır Dataset'de sağlıyor, bunlardan bir tanesi hazır film puanları. Zaten bu olmasa veri toplamakla da uğraşmamız gerekecekti. Azure bize 227.472 adet hazır puan bilgisini sunuyor. Hemen deneyimize ekleyelim.

![Movie Ratings](/assets/azure-ml-recommend-movie-ratings.jpg)

İsterseniz içeriğine yine aynı şekilde Visualize seçeneğiyle bakabilirsiniz. Hatta bakın, gıcık bir durum var çünkü.

![Movie Ratings Visualize](/assets/azure-ml-recommend-movie-ratings-visualize.jpg)

**MovieId** alanı sayı tipinde, oysa bizim indirdiğimiz kendi oylarımızda **tt** ile başlayan ve **0** ile [padleft](https://qz.com/646467/how-one-programmer-broke-the-internet-by-deleting-a-tiny-piece-of-code/) yapılmış durumda. Birini diğerine çevirmemiz gerekiyor.

Sol tarafta gördüğünüz menüde veriler üzerinde bir çok işlem yapmanızı sağlayan araçlar bulunuyor. İtiraf edeyim ben de bir çoğunu bilmiyorum, zaten öğrenme yöntemi olarak bu tür listeleri ezberlemeyi doğru bulmuyorum, ne yapmak istediğimizi biliyorsak neye ihtiyacımız olduğunu tahmin edebiliyoruz, ufak bir araştırma yaparak da gerekli aracı hızlıca öğrenebiliyoruz. O yüzden hepsine değinmektense kullandıklarımızı anlatacağım sadece.

Veri dönüşümü için benim favorim **Apply SQL Transformation** oldu. [SQLite](https://www.sqlite.org/index.html) sorgu motorunu kullanarak veriler üzerinde istediğimiz işlemleri yapabiliyoruz. Bu arada [SQLite sorgu dili](https://www.sqlite.org/lang.html) biraz farklı, **TSQL**, **PLSQL** yazıp hayal kırıklığına uğramadan uyarayım dedim.

Arama kutusuna **sql** yazıp **Apply SQL Transformation** kutusunu sürükleyip **ratings.csv** altına bırakalım ve üst soldaki noktayı **ratings.csv** alt tarafı ile bağlayalım. Seçiliyken sağ tarafta sorgumuzu girebileceğimiz bir kutu açılacak. Bize sadece kullanıcı ID (kendimizi diğer kullanıcılardan ayırmak için -1 veriyoruz), film ID ve oy bilgileri lazım. Tamamlanmış hali aşağıdaki gibi.

![SQL Transformation](/assets/azure-ml-recommend-sql.jpg)

Şimdi kendi oylarımızdan 3 alan seçerek yeni veri kümemizi oluşturduk, bu listeye Azure ML tarafından bize sunulan listeyi de ekleyip kalabalık bir liste oluşturacağız. Ancak bunun için öncelikle sütun sayılarımızın eşit olması gerekiyor. Sol taraftan öncelikle **Select Columns in Dataset** bileşenini ekleyerek resimdeki gibi 3 alanımızı seçelim.

![Select Rating Columns](/assets/azure-ml-recommend-rating-columns.jpg)

Şimdi kendi oylarımız ile hazır oyları birleştirebiliriz. Burada dikkat edilmesi gereken bir nokta var, eğer verileri birleştirmezseniz Azure ML bizim daha önce verdiğimiz oyları tespit edemiyor, tavsiyeleri zaten izlediğimiz yapımlar oluyor (Örneğin bu birleştirme işini yapmadığımda bana hazır veriler içindeki en yüksek puanları almış filmleri önerdi).

![Add Rows](/assets/azure-ml-recommend-add-rows.jpg)

Yeşil tikler dikkatinizi çekmiştir, oluşturduğumuz **deney**i istediğimiz zaman alt tarafta yer alan **RUN** komutu ile çalıştırabiliriz. Hatta istersek tüm diyagram yerine sadece seçili bileşeni de çalıştırabiliyoruz.

![Run](/assets/azure-ml-recommend-run.jpg)

Şimdi bu biriktirdiğimiz verileri yorumlama zamanı.

## Train Matchbox Recommender

[Train Matchbox Recommender](https://docs.microsoft.com/en-us/azure/machine-learning/studio-module-reference/train-matchbox-recommender) Microsoft tarafından geliştirilmiş, olasılıksal bir modele (Bayesian) dayanan büyük ölçekli bir öneri sistemidir. Bu model, bir kullanıcının tercihlerini film, içerik veya diğer ürünler gibi ögeleri nasıl derecelendirdikleri üzerine yapılan gözlemlerle öğrenebilir. Bu gözlemlere dayanarak, istendiğinde kullanıcılara yeni ürünler önerir.

Kullanıcılar tarafından verilmiş puan bilgilerinin yanında kullanıcılar ve içerikler hakkındaki meta verileri de öğrendikleri arasına katabiliyor.

Sözlük tanımını geçersek, Microsoft, tavsiye sistemi geliştirebilmemiz için bize var olan puan bilgilerinden öğrenebilen hazır bir bileşen sunuyor. Hemen deneyimize ekleyelim. Kullanıcı ve içerik meta bilgilerimiz olmadığından onları eşleştirmeden bırakıyoruz.

![Train Matchbox Recommender](/assets/azure-ml-recommend-train.jpg)

**Train Matchbox Recommender** ile yapabileceğimiz ayarlar da aşağıdaki gibi (bileşeni seçtiğimizde sağ tarafta görebilirsiniz).

![Train Matchbox Recommender Options](/assets/azure-ml-recommend-train-options.jpg)

**Number of traits:** Kullanıcı meta verilerinden kaç tanesini öğrenirken kullanacağı bilgisi. Biz meta verileri kullanmadığımız için varsayılan değeri bırakabiliriz.

**Number of recommendation algorithm iterations:** Algoritmanın verileri kaç kez işleyeceği. Varsayılan değer kalabilir.

**Number of training batches:** Algoritma verileri parçalara ayırarak paralelde çalıştırabiliyor. Bu değer ile kaç grup olacağını söylüyoruz, idealde işlemcinin core sayısı kadar girmeliyiz. Yine varsayılan değer kalabilir.

Artık elimizdeki verileri öğrenmiş bir sistemimiz var, sırada bildiklerine göre yorum yapabilmesini sağlamaya geldi.

## Score Matchbox Recommender

[Score Matchbox Recommender](https://docs.microsoft.com/en-us/azure/machine-learning/studio-module-reference/score-matchbox-recommender) öğrenilmiş modeli kullanarak tahminler yürütebilen bir bileşen. Bu bileşen de kullanıcılar ve içerikler hakkındaki meta verileri değerlendirmeye alabiliyor. Biz yine bunları boş geçeceğiz, bize gerekenler eğittiğimiz tavsiyeci, tahmin yürütmesini istediğimiz veriler ve eğitim sırasında kullandığımız veri kümesi.

![Score Matchbox Recommender](/assets/azure-ml-recommend-score.jpg)

*Score Matchbox Recommender* için yapabileceğimiz ayarlar ise aşağıdaki gibi.

![Score Matchbox Recommender](/assets/azure-ml-recommend-score-options.jpg)

**Recommender prediction kind:** Puan tahmini, yakın kullanıcılar ve yakın içerikler seçenekleri de bulunan listeden biz içerik tavsiyesi istediğimiz için *Item Recommendation* seçeneğini seçiyoruz (varsayılan seçenek).

**Recommender item selection:** Algoritmadan tüm seçenekler, puan verdiklerimiz (teyit etmek için) ya da puan vermediğimiz veriler (tavsiye için) için tahmin yürütmesini isteyebiliriz. **From Unrated Items** seçiyoruz.

**Maximum number of items to recommend to a user:** Kaç adet tavsiye istediğimiz. 5 değeri kalabilir, daha fazla tavsiye istiyorsanız bu değeri arttırabilirsiniz.

**Whether to return the predicted ratings of the items along with the labels:** Algoritmadan tahmin edilen içeriklerin yanında bizim vereceğimizi tahmin ettiği puanı da söylemesini isteyebiliyoruz. Seçmeden geçtim.

Bir bakalım nasıl tavsiyede bulunmuş. Deneyimizi çalıştırıp **Score Matchbox Recommender** bileşenine sağ tıklayarak **Score Dataset -> Visualize** ile sonucu görselleştirelim.

![Score Matchbox Recommender Results](/assets/azure-ml-recommend-score-result.jpg)

5 adet tavsiyeyi görebiliyoruz. Ancak hepsi Film ID değerleri. İsimleri öğrenmemiz lazım, bunun için de verileri yine Azure ML'in bize sağladığı **Movie Titles** bileşeni ile yapacağız. Ancak bir sorun var, verileri **Join** ile birleştirebilmek için yatayda yer alan bu 5 öneriyi dikeye çevirmemiz gerekiyor. Ben bu iş için **Execute Python Script** bileşenini kullandım.

Script aşağıdaki gibi:

```python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):
    values = [dataframe1[c] for c in dataframe1.columns[1:]]
    return pd.DataFrame(values)
```

Python bilenler için çok kolay olan kodumuzu yine de açıklayalım. Bileşenimize gelen ilk parametre olan **dataframe1** sütunlarından ilki hariç (çünkü ilk alan UserId) her biri için veriyi alıp bir diziye ekliyoruz, ve bu dizi ile yeni bir **[DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html)** oluşturup dönüyoruz.

![Python Script](/assets/azure-ml-recommend-python.jpg)

Görselleştirme ekranını açtığımızda karşılaştığımız sonuç ise aşağıdaki gibi (tabii tavsiyeler bana özel).

![Python Script Result](/assets/azure-ml-recommend-python-result.jpg)

Son işlem olarak bu listeyi aşağıdaki gibi **IMDB Movie Titles** bileşeni ile **Join Data** kullanarak birleştiriyoruz. Deneyimizin son hali aşağıdaki gibi.

![Join Data](/assets/azure-ml-recommend-join.jpg)

Tavsiyeleri ise **Join Data** görselleştirerek görebiliriz.

![Join Data](/assets/azure-ml-recommend-join-result.jpg)

Sonunda tavsiyelerimi aldım, listeye bakınca izleyip çok sevdiğim ancak puan vermeyi unuttuğum **Lawrance of Arabia**, zaten izleme listemde olan **Witness for the Prosecution** yanında büyük ihtimal seveceğim **The Class of 92**, **Paris, Texas**, **It's a Wonderful Life** gibi filmler yer alıyor. Kesinlikle başarılı tahminler 😊.

Mutlu kodlamalar!
