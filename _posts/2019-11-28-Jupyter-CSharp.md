---
layout: post
title: Jupyter Notebook ve C#
comments: true
redirect_from: "/2019/09/29/jupyter-csharp.js/"
permalink: jupyter-csharp
---

Project Jupyter, bir çok programlama dili ile bilgi işlem için açık kaynaklı yazılım, açık standartlar ve hizmetler geliştirmek için oluşturulan kar amacı gütmeyen bir organizasyondur. IPython'dan 2014 yılında Fernando Pérez tarafından başlatılan Project Jupyter, birkaç düzine dilde uygulama ortamını destekliyor.

Makine öğrenmesi ile uğraşanların yakından tanıdığı jupyter defterleri ile düz yazı, kod, veriler ve grafikleri bir arada çalıştırabiliyoruz. İnteraktif sayfalarda yaptığımız değişikliklerin sonuçlarını da anında görebiliyoruz. Artık desteklenen platformlar arasında .Net Core'da var!

![Jupyter Örnek](/assets/jupyter-netcore-example.png)

Bu süreçteki ilk adım [Try.Net](https://www.hanselman.com/blog/IntroducingTheTryNETGlobalToolInteractiveInbrowserDocumentationAndWorkshopCreator.aspx) geliştirilmesi oldu. Try ile Markdown yazılarımızın içine C# ve F# kodları gömerek interaktif dokümantasyon geliştirebiliyoruz. Bir sonraki yazımda da bu konuya değinmeyi planlıyorum.

Peki ortamı nasıl hazırlıyoruz?

## Jupyter kurulumu

İlk yapmamız gereken jupyter kurmak. Aşağıdaki yöntemlerden birisi ile kurulum yapabilirsiniz.

* [Anaconda](https://www.anaconda.com/distribution) hazır platformu ile
* [conda](https://docs.conda.io/en/latest/) paket yöneticisi ile
* [pip](https://pypi.org/project/pip/) python paket sistemi ile

## .Net Kernel Kurulumu

Sırada jupyter ile .net entegrasyonu var. Önce kernel nedir bir bakalım.

### Kernel

**Kernel**, kullanıcının kodunu çalıştıran ve değerlendiren bir programdır. Python kodları için bir kernel olan IPython yanında topluluk tarafından geliştirilmiş diğer [birçok dil için de](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) kernel'ler bulunur.

Öncelikle bir terminal açalım ve try aracını global kuralım.

```shell
dotnet tool install --global dotnet-try
```

Şimdi de .net core kernel kurulumunu yapalım.

```shell
dotnet try jupyter install
```

İsterseniz kurulu kernel listesini almak için aşağıdaki komutu çalıştırabilirsiniz.

```shell
jupyter kernelspec list
```

![Kernels](/assets/jupyter-netcore-kernels.png)

Artık hazırız, yeni bir jupyter dokümanı açmak için terminalden ```jupyter lab``` komutunu çalıştırmamız yeterli. Tabii Anaconda Navigator'da kullanabiliriz.

![Lab](/assets/jupyter-netcore-lab.png)

İncelemek için açık kaynak DynamicQueryable projemde hazırladığım ufak dokümanı inceleyebilirsiniz.

İlk iş kodu forklayalım:

```shell
git clone https://github.com/umutozel/DynamicQueryable
cd DynamicQueryable
```

Şimdi jupyter lab açalım, hazır dokümanı açalım.

```shell
jupyter lab README.ipynb
```

8888 portunda çalışan aşağıdakine benzer bir browser tab'ı ile karşılaşacaksınız.

![DynamicQueryable Readme Notebook](/assets/jupyter-csharp-dynamic-queryable-readme.png)

Kod bölümü olarak yazdığınız kısımlar C# Kernel ile çalıştırılacak, artık dokümanlar hem daha faydalı hem de yazması çok daha eğlenceli 💯

Burada ufak bir detay var, dokümanımıza NuGet paketi de kurabiliyoruz:

```shell
#r "nuget:DynamicQueryable"
```

**#r** direktifini Roslyn script'ler ile uğraşanlarınız biliyordur. Roslyn script için bir preprocessor olarak çalışan bu komut NuGet paketini indirip kuruyor. Biraz zaman aldığının söylemekte fayda var, çalışmıyor diye hayal kırıklığı olmasın.

## Daha Fazlası

İlk önce [Microsoft'un konu hakkındaki yazısını](https://devblogs.microsoft.com/dotnet/net-core-with-juypter-notebooks-is-here-preview-1/) okumanızı tavsiye ederim.

Sıradaki adım ise hazırladığımız dokümanları çevrimiçi paylaşmak. Bunun için ise https://mybinder.org/ hizmetinden faydalanabiliriz. Dokümanı içeren açık kaynak projemize dotnet core kernel kurulumu yapan bir Dockerfile eklediğimizde bizim için konteyner oluşturma ve dokümanı yayınlama işini hallediyor.

Mutlu kodlamalar.
