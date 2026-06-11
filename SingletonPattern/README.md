# Singleton Pattern Nedir?

## Giriş

Bazı sınıflardan uygulama boyunca yalnızca **tek bir örnek** bulunmasını isteriz. Örneğin bir loglama servisi, bir konfigürasyon yöneticisi ya da bir veritabanı bağlantı havuzu — bunların ikinci bir kopyasının olması ya gereksizdir ya da doğrudan hataya yol açar.

Bu sınıflardan yanlışlıkla birden fazla nesne üretilmesini engellemek ve onlara her yerden erişilebilecek tek bir nokta sunmak istediğimizde, Creational Design Pattern kategorisindeki **Singleton Pattern** kullanılabilir.

---

# Singleton Pattern Nedir?

Singleton Pattern, bir sınıftan yalnızca tek bir örnek (instance) oluşturulmasını garanti eden ve bu örneğe global bir erişim noktası sağlayan bir tasarım desenidir.

Bu desenin amacı:

* Bir sınıftan yalnızca tek bir nesne üretilmesini sağlamak
* Bu tek nesneye her yerden erişebilmek
* Nesnenin ne zaman oluşturulacağını kontrol etmek
* Ortak bir kaynağı (log, ayar, bağlantı) tek noktadan yönetmek

---

# Singleton Pattern Hangi Kategoridedir?

Design Pattern'ler genel olarak üç kategoriye ayrılır:

* Creational Patterns
* Structural Patterns
* Behavioral Patterns

Singleton Pattern, **Creational Pattern** kategorisinde yer alır.

Çünkü temel amacı nesnenin nasıl oluşturulduğunu yönetmektir.

---

# Problem

Bir loglama sınıfımız olduğunu düşünelim. Bu sınıftan koddun farklı yerlerinde rahatça nesne üretilebiliyor:

```csharp
var log1 = new Logger();
var log2 = new Logger();
var log3 = new Logger();
```

Her `new` çağrısı ayrı bir nesne üretir. Oysa bizim tek bir log nesnesine ihtiyacımız var:

* Aynı dosyaya yazan birden çok nesne çakışabilir
* Her nesne gereksiz yere bellek tüketir
* Hangi nesnenin "asıl" olduğu belirsizleşir

İhtiyacımız şu: bu sınıftan **her zaman aynı tek nesneyi** elde etmek.

---

# Singleton'ın İki Temel Kuralı

Singleton iki basit kurala dayanır:

* **Constructor private yapılır** → dışarıdan `new` ile nesne üretilemez
* **Tek örnek static bir alanda tutulur** → sınıfın kendisi bu örneği üretir ve geri verir

Bu iki kural bir araya geldiğinde, nesneyi üretmenin tek yolu sınıfın sunduğu erişim noktası olur.

---

# Naif Singleton

İlk ve en basit uygulama şöyledir:

```csharp
public class Logger
{
    private static Logger _instance;

    private Logger() { }          // dışarıdan new engellenir

    public static Logger Instance
    {
        get
        {
            if (_instance == null)
                _instance = new Logger();   // ilk çağrıda üretilir
            return _instance;
        }
    }

    public void Yaz(string mesaj) => Console.WriteLine(mesaj);
}
```

Kullanımı:

```csharp
Logger.Instance.Yaz("Uygulama başladı");
Logger.Instance.Yaz("İşlem tamamlandı");
```

Artık `new Logger()` yazamazsın; her `Logger.Instance` çağrısı aynı nesneyi döndürür.

---

# Sorun: Thread-Safety

Yukarıdaki naif sürümün gizli bir kusuru vardır. Aynı anda çalışan iki iş parçacığı (thread) `Instance`'a aynı anda girerse, ikisi de `_instance == null` görüp **iki ayrı nesne** üretebilir. Böylece "tek örnek" garantisi bozulur.

Çok iş parçacıklı bir ortamda bu yüzden ek bir önlem gerekir.

---

# Thread-Safe Singleton

### Yöntem 1: Kilit (lock) ile

`lock` kullanarak aynı anda yalnızca bir thread'in nesne üretmesini sağlarız. "Double-checked locking" denen bu yaklaşım, gereksiz kilitlenmeyi de önler:

```csharp
public class Logger
{
    private static Logger _instance;
    private static readonly object _kilit = new object();

    private Logger() { }

    public static Logger Instance
    {
        get
        {
            if (_instance == null)
            {
                lock (_kilit)
                {
                    if (_instance == null)
                        _instance = new Logger();
                }
            }
            return _instance;
        }
    }
}
```

### Yöntem 2: Lazy<T> ile (önerilen)

Modern C#'ta en temiz yol `Lazy<T>`'dir. Hem thread-safe'dir hem de nesneyi yalnızca ilk kullanıldığında (gecikmeli / lazy) üretir:

```csharp
public class Logger
{
    private static readonly Lazy<Logger> _lazy =
        new(() => new Logger());

    private Logger() { }

    public static Logger Instance => _lazy.Value;

    public void Yaz(string mesaj) => Console.WriteLine(mesaj);
}
```

Bu sürüm hem kısa hem güvenlidir; pratikte tercih edilen yaklaşım budur.

---

# Singleton ve Static Class Arasındaki Fark

Singleton ile `static class` sık karıştırılır. İkisi de "tek" bir yapı sunar ama önemli farklar vardır:

| Static Class                          | Singleton                                   |
| ------------------------------------- | ------------------------------------------- |
| Interface uygulayamaz                 | Interface uygulayabilir                     |
| Kalıtım/polimorfizm mümkün değildir   | Kalıtım ve polimorfizm mümkündür            |
| Gecikmeli (lazy) oluşturma zordur     | Lazy oluşturma doğal olarak yapılabilir     |
| Bağımlılık olarak geçirilemez         | Bir nesne olarak parametreyle taşınabilir   |
| Örnek (instance) yoktur               | Tek de olsa gerçek bir nesnedir             |

Kısacası: davranışa, arayüze veya gecikmeli kuruluma ihtiyaç varsa Singleton; sadece durumsuz yardımcı metotlar için `static class` daha uygundur.

---

# Design Pattern ile Singleton Arasındaki İlişki

Singleton, Design Pattern ailesinin Creational kategorisindeki bir desendir.

Şema olarak:

```text
Design Pattern
│
├── Creational
│     ├── Factory
│     ├── Builder
│     └── Singleton   ← buradayız
│
├── Structural
│     ├── Adapter
│     ├── Facade
│     └── Decorator
│
└── Behavioral
      ├── Strategy
      └── Observer
```

Bu nedenle:

"Singleton nedir?"

sorusuna verilebilecek en kısa cevap:

"Singleton, bir sınıftan tek bir örnek oluşturulmasını garanti eden ve bu örneğe global erişim sağlayan bir Creational Design Pattern'dir."

---

# Avantajları

Singleton Pattern'in başlıca avantajları:

* Sınıftan yalnızca tek bir örnek bulunmasını garanti eder
* Bu örneğe her yerden erişilebilecek tek bir nokta sunar
* Nesneyi yalnızca ihtiyaç anında üretebilir (lazy initialization)
* Ortak kaynakların (log, konfigürasyon, bağlantı havuzu) tek elden yönetilmesini sağlar

---

# Dezavantajları

Singleton, en çok tartışılan desenlerden biridir. Dikkat edilmesi gereken yönleri:

* Global durum (global state) oluşturur; gizli bağımlılıklara yol açar
* Sınıflar doğrudan `Logger.Instance`'a bağlandığı için sıkı bağ (tight coupling) doğar
* Test edilebilirliği düşürür; sahte (mock) nesneyle değiştirmek zordur
* Çok iş parçacıklı ortamda dikkatli yazılmazsa hata kaynağı olur
* Bu sebeplerle çoğu modern senaryoda yerine Dependency Injection (DI) tercih edilir

---

# Sonuç

Singleton Pattern, bir sınıftan tek bir örnek oluşturulmasını garanti eden ve bu örneğe global erişim sağlayan bir Creational Design Pattern'dir.

Temel mekanizması iki kurala dayanır: constructor'ı private yapmak ve tek örneği static bir alanda tutmak. Çok iş parçacıklı ortamlarda ise `lock` veya `Lazy<T>` ile bu tekliğin güvenliği sağlanır.

Singleton, doğru yerde kullanıldığında ortak kaynakların yönetimini sadeleştirir; ancak global durum ve sıkı bağ gibi maliyetleri olduğu için gereğinden fazla kullanılmamalı, çoğu durumda Dependency Injection gibi alternatiflerle birlikte değerlendirilmelidir.
