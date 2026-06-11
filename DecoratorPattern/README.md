# Decorator Pattern Nedir?

## Giriş

Yazılım geliştirirken bazen bir nesneye, onun sınıfını hiç değiştirmeden yeni davranışlar eklemek isteriz. Üstelik bu davranışları çalışma zamanında, istediğimiz sırayla ve istediğimiz kadar birleştirebilmek isteriz.

Bunu kalıtımla (inheritance) yapmaya çalışmak çoğu zaman sınıf sayısının kontrolden çıkmasına yol açar.

Bu gibi durumlarda Structural Design Pattern kategorisinde yer alan **Decorator Pattern** kullanılabilir.

---

# Decorator Pattern Nedir?

Decorator Pattern, bir nesneyi aynı arayüze sahip başka bir nesneyle **sarmalayarak** (wrapping) ona dinamik olarak yeni sorumluluklar ekleyen bir tasarım desenidir.

Bu desenin amacı:

* Bir nesneye davranış eklemeyi sınıfı değiştirmeden sağlamak
* Bu eklemeleri çalışma zamanında yapabilmek
* Davranışları esnekçe birleştirebilmek
* Kalıtımdan doğan sınıf patlamasını önlemek

---

# Decorator Pattern Hangi Kategoridedir?

Design Pattern'ler genel olarak üç kategoriye ayrılır:

* Creational Patterns
* Structural Patterns
* Behavioral Patterns

Decorator Pattern, **Structural Pattern** kategorisinde yer alır.

Çünkü temel amacı nesneleri bir araya getirerek (sarmalayarak) yeni yapılar oluşturmaktır.

---

# Problem

Bir kahve uygulaması yazdığımızı düşünelim. Sade bir kahvemiz var; üzerine süt, şeker, krema gibi ekstralar eklenebiliyor ve her ekstra fiyatı değiştiriyor.

İlk akla gelen çözüm, her kombinasyon için bir sınıf yazmaktır:

```csharp
public class SadeKahve { }
public class SutluKahve { }
public class SekerliKahve { }
public class SutluSekerliKahve { }
public class SutluKremaliKahve { }
// ...
```

Ekstra sayısı arttıkça kombinasyonlar katlanarak büyür:

* 3 ekstra → çok sayıda alt sınıf
* 5 ekstra → onlarca alt sınıf
* Her yeni ekstra → mevcut bütün kombinasyonların ikiye katlanması

Bu duruma **sınıf patlaması (class explosion)** denir. Kalıtım burada hızla sürdürülemez hale gelir.

---

# Klasik Çözüm: Kalıtım ve Neden Yetersiz

Kalıtımın temel sınırı, davranışın **derleme zamanında sabitlenmesidir**. `SutluKahve` yazdığında bu sınıf hep sütlüdür; çalışma zamanında "şunu da ekle" diyemezsin.

Oysa ihtiyacımız tam tersi: ekstraları **çalışma zamanında**, kullanıcının seçimine göre üst üste eklemek. İşte Decorator bunu mümkün kılar.

---

# Sarmalama (Wrapping) Nedir?

Decorator'ın temelinde yatan fikir sarmalamadır. Bir nesneyi, **onunla aynı arayüzü uygulayan** başka bir nesnenin içine koyarsın. Dıştaki nesne, çağrıyı içtekine iletir ve araya kendi davranışını ekler.

Kabaca şöyle görünür:

```csharp
ICizik nesne = new GercekNesne();
nesne = new EkstraDavranis(nesne);   // sarmaladık
nesne.YapBirSey();                   // önce ekstra, sonra gerçek nesne çalışır
```

Bu yapı, davranışı katman katman eklememizi sağlar.

---

# Decorator Pattern Nasıl Uygulanır?

Decorator dört rolden oluşur:

* **Component (bileşen arayüzü):** Hem gerçek nesnenin hem de dekoratörlerin uyacağı ortak arayüz
* **ConcreteComponent (somut bileşen):** Sarmalanacak temel nesne
* **Decorator (temel dekoratör):** Component arayüzünü uygulayan ve içinde bir Component referansı tutan soyut sınıf
* **ConcreteDecorator (somut dekoratör):** Asıl ekstra davranışı ekleyen sınıflar

### 1. Component — ortak arayüz

```csharp
public interface IKahve
{
    string Aciklama();
    decimal Fiyat();
}
```

### 2. ConcreteComponent — temel nesne

```csharp
public class SadeKahve : IKahve
{
    public string Aciklama() => "Sade kahve";
    public decimal Fiyat() => 20m;
}
```

### 3. Decorator — temel sarmalayıcı

Dikkat: hem `IKahve`'yi uygular hem de içinde bir `IKahve` tutar. Sarmalamanın kalbi budur.

```csharp
public abstract class KahveDekorator : IKahve
{
    protected readonly IKahve _kahve;

    protected KahveDekorator(IKahve kahve)
    {
        _kahve = kahve;
    }

    public virtual string Aciklama() => _kahve.Aciklama();
    public virtual decimal Fiyat() => _kahve.Fiyat();
}
```

### 4. ConcreteDecorator — ekstra davranışlar

```csharp
public class SutluDekorator : KahveDekorator
{
    public SutluDekorator(IKahve kahve) : base(kahve) { }

    public override string Aciklama() => _kahve.Aciklama() + " + süt";
    public override decimal Fiyat()    => _kahve.Fiyat() + 5m;
}

public class SekerliDekorator : KahveDekorator
{
    public SekerliDekorator(IKahve kahve) : base(kahve) { }

    public override string Aciklama() => _kahve.Aciklama() + " + şeker";
    public override decimal Fiyat()    => _kahve.Fiyat() + 2m;
}
```

---

# Decorator'ların Zincirlenmesi

Asıl güç burada ortaya çıkar. Bir nesneyi başka bir dekoratörle, onu da bir başkasıyla sarmalayarak davranışları üst üste eklersin:

```csharp
IKahve kahve = new SadeKahve();        // Sade kahve — 20
kahve = new SutluDekorator(kahve);     // + süt      — 25
kahve = new SekerliDekorator(kahve);   // + şeker    — 27

Console.WriteLine(kahve.Aciklama());   // Sade kahve + süt + şeker
Console.WriteLine(kahve.Fiyat());      // 27
```

Hiçbir kombinasyon için ayrı sınıf yazmadık. Her ekstra için tek bir dekoratör var; bunları çalışma zamanında istediğimiz gibi birleştiriyoruz.

---

# Kalıtım ve Decorator Arasındaki Fark

| Kalıtım (Inheritance)                 | Decorator                                  |
| ------------------------------------- | ------------------------------------------ |
| Davranış derleme zamanında sabittir   | Davranış çalışma zamanında eklenir         |
| Her kombinasyon için yeni sınıf gerek | Her özellik için tek sınıf yeter           |
| Sınıf sayısı kombinasyonla patlar     | Sınıf sayısı doğrusal artar                |
| Davranış statiktir                    | Davranış sarmalanarak dinamik birleşir     |

---

# Design Pattern ile Decorator Arasındaki İlişki

Decorator, Design Pattern ailesinin Structural kategorisindeki bir desendir.

Şema olarak:

```text
Design Pattern
│
├── Creational
│     ├── Singleton
│     ├── Factory
│     └── Builder
│
├── Structural
│     ├── Adapter
│     ├── Facade
│     └── Decorator   ← buradayız
│
└── Behavioral
      ├── Strategy
      └── Observer
```

Bu nedenle:

"Decorator nedir?"

sorusuna verilebilecek en kısa cevap:

"Decorator, bir nesneyi aynı arayüzü uygulayan başka bir nesneyle sarmalayarak ona çalışma zamanında yeni davranış ekleyen bir Structural Design Pattern'dir."

---

# Avantajları

Decorator Pattern'in başlıca avantajları:

* Davranışı çalışma zamanında ekleyebilme
* Sınıf patlamasını önleme (her özellik için tek sınıf)
* Mevcut sınıfı değiştirmeden genişletme (Açık/Kapalı Prensibi — OCP)
* Davranışları esnekçe ve istenen sırayla birleştirme
* Her dekoratörün tek bir sorumluluk taşıması (Tek Sorumluluk Prensibi)

Örnek — yeni bir ekstra eklemek, mevcut hiçbir sınıfa dokunmadan tek bir sınıf yazmaktır:

```csharp
public class KremaliDekorator : KahveDekorator
{
    public KremaliDekorator(IKahve kahve) : base(kahve) { }

    public override string Aciklama() => _kahve.Aciklama() + " + krema";
    public override decimal Fiyat()    => _kahve.Fiyat() + 4m;
}
```

---

# Dezavantajları

Decorator Pattern'in dikkat edilmesi gereken yönleri:

* Çok sayıda küçük sınıf ve sarmalanmış nesne ortaya çıkar
* Çok katmanlı sarmalamada hata ayıklamak (debug) zorlaşabilir
* Ekstraların sırası sonucu etkiliyorsa kullanım karmaşıklaşır
* Zincirin içindeki belirli bir somut tipe doğrudan erişmek zorlaşır

---

# Sonuç

Decorator Pattern, bir nesneye sınıfını değiştirmeden, çalışma zamanında yeni davranışlar eklemeyi sağlayan önemli bir Structural Design Pattern'dir.

Temel fikri sarmalamadır: nesne, kendisiyle aynı arayüzü uygulayan bir başka nesnenin içine konur ve her katman araya kendi davranışını ekler.

Özellikle çok sayıda opsiyonel özelliğin farklı kombinasyonlarda gerektiği durumlarda Decorator, kalıtımın yol açtığı sınıf patlamasını ortadan kaldırarak kodun esnekliğini ve bakımını önemli ölçüde artırır.
