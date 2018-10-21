---
layout: post
title: ExpressionVisitor sınıfı
comments: true
redirect_from: "/2018/10/20/JavaScript-Linq-03/"
permalink: javascript-linq-03
---

JavaScript ile Linq yazı serimizin üçüncüsüne hoş geldiniz.

1. [Linq gerçekte nedir?](/javascript-linq-01)
2. Expression'lar **(You are here)**
3. [ExpressionVisitor sınıfı](/javascript-linq-03)
4. [IQueryable ve IQueryProvider](/javascript-linq-04)
5. [Jokenizer - JavaScript Expression'larını parse edelim](/javascript-linq-05)
6. [Jokenizer.Net - C# Expression'larını parse edelim](/javascript-linq-06)
7. [Jinqu - JavaScript ile Linq](/javascript-linq-07)
8. [Linquest ve Linquest.AspNetCore - Asp.Net Core ile cevap verelim](/javascript-linq-08)

Bu yazıda ExpressionVisitor sınıfı ile bir Expression'ı modifiye edeceğiz.

## ExpressionVisitor sınıfı
Expression'lar immutable'dır (değiştirilemez). Yani bir kere oluşturduysak üzerlerinde değişiklik yapmamız mümkün olmaz.
Daha önce bahsetmiştik, Expression'lar bir ağaç şeklinde yazdığımız kodu **ifade** eder. 

[Dördüncü yazıda IQueryable ve IQueryProvider ile artık sorgulama işine el atacağız](/javascript-linq-04), görüşmek üzere.

> “Before software can be reusable it first has to be usable.” – Ralph Johnson
