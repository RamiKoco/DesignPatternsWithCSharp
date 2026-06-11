# DesignPatternsWithCSharp

> Design Pattern examples, explanations and real-world implementations using C# and .NET.

![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
![C#](https://img.shields.io/badge/C%23-.NET-512BD4.svg)
![Patterns](https://img.shields.io/badge/patterns-6-blue.svg)
![Language](https://img.shields.io/badge/lang-T%C3%BCrk%C3%A7e-orange.svg)

---

## Hakkında

Bu repo, en sık kullanılan **tasarım desenlerini (design patterns)** C# ile, sade ve örneğe dayalı bir dille anlatır. Amaç sadece tanım vermek değil; her deseni adım adım kurarak, ne zaman gerektiğini ve ne zaman gerekmediğini de göstermektir.

Her desen kendi klasöründe, ayrı bir doküman olarak ele alınır ve aynı yapıyı izler.

---

## İçindekiler

Desenler, klasik üç tasarım deseni kategorisine göre gruplanmıştır.

### 🟣 Creational (Oluşturma Desenleri)

Nesnelerin nasıl üretildiğini yönetir.

| Desen | Açıklama |
| ----- | -------- |
| [Builder Pattern](./BuilderPattern) | Karmaşık nesneleri adım adım ve okunur biçimde kurar (Fluent Builder dahil). |
| [Factory Pattern](./FactoryPattern) | Nesne üretimini tek bir noktada toplar, çağıran kodu somut sınıflardan ayırır. |
| [Singleton Pattern](./SingletonPattern) | Bir sınıftan tek bir örnek bulunmasını garanti eder ve global erişim sağlar. |

### 🟢 Structural (Yapısal Desenler)

Nesneleri bir araya getirerek yeni yapılar oluşturur.

| Desen | Açıklama |
| ----- | -------- |
| [Decorator Pattern](./DecoratorPattern) | Bir nesneyi sarmalayarak ona çalışma zamanında yeni davranış ekler. |

### 🔵 Behavioral (Davranışsal Desenler)

Nesneler arası haberleşmeyi ve algoritma akışını düzenler.

| Desen | Açıklama |
| ----- | -------- |
| [Observer Pattern](./ObserverPattern) | Bir nesnedeki değişikliği, ona abone olan nesnelere otomatik bildirir. |
| [Template Method Pattern](./TemplateMethodPattern) | Bir algoritmanın iskeletini üst sınıfta sabitler, değişen adımları alt sınıflara bırakır. |

---

## Her Doküman Neler İçeriyor?

Tüm desenler tutarlı bir şablonla yazılmıştır:

* **Nedir ve neden gerekir** — desenin çözdüğü problem
* **Hangi kategoridedir** — Creational / Structural / Behavioral
* **Problem** — desen olmadan kodun nasıl göründüğü
* **Adım adım örnek kod** — basitten gelişmişe
* **Karşılaştırma tablosu** — desenin en yakın alternatifiyle farkı
* **Avantajları ve dezavantajları**
* **Gerçek proje karşılığı** — desenin üretim ortamında nasıl kullanıldığı
* **Sonuç**

Örnekler ortak bir tema (içecek/kahve hazırlama) üzerinden ilerler; böylece desenler birbiriyle kıyaslanabilir kalır.

---

## Tasarım Deseni Kategorileri

```text
Design Pattern
│
├── Creational    → Builder, Factory, Singleton
├── Structural    → Decorator
└── Behavioral    → Observer, Template Method
```

* **Creational:** Nesne oluşturma sürecini yönetir.
* **Structural:** Nesneleri birleştirerek daha büyük yapılar kurar.
* **Behavioral:** Nesneler arası etkileşimi ve sorumluluk dağılımını düzenler.

---

## Kullanım

Her klasör kendi içinde bağımsızdır. İlgilendiğin desenin klasörüne girip `README.md` dosyasını okuyabilir, örnek kodları kendi projende deneyebilirsin.

Önerilen okuma sırası (basitten kavramsala):

1. Singleton
2. Factory
3. Builder
4. Template Method
5. Observer
6. Decorator

---

## Lisans

Bu proje [MIT Lisansı](./LICENSE) ile lisanslanmıştır.
