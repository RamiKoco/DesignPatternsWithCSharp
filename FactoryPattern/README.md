# Factory Pattern Nedir?

## Giriş

Bir nesneyi `new` ile doğrudan üretmek basit görünür. Ama hangi sınıfın üretileceği bir koşula bağlıysa ve bu karar kodun pek çok yerinde tekrar ediyorsa, işler karışır. Her yerde aynı `if/switch` blokları ve somut sınıf isimleri dağılmaya başlar.

Nesne üretme kararını tek bir yere toplamak istediğimizde, Creational Design Pattern kategorisindeki **Factory Pattern** kullanılabilir.

---

# Factory Pattern Nedir?

Factory Pattern, nesne oluşturma işini, onu kullanan koddan ayırıp ayrı bir "üretici" (factory) yapıya devreden bir tasarım desenidir.

Bu desenin amacı:

* Nesne üretme kararını tek bir noktada toplamak
* Kullanan kodu somut sınıflardan ayırmak
* `new` çağrılarını ve tip seçimini merkezileştirmek
* Yeni tip eklemeyi kolaylaştırmak

---

# Factory Pattern Hangi Kategoridedir?

Design Pattern'ler genel olarak üç kategoriye ayrılır:

* Creational Patterns
* Structural Patterns
* Behavioral Patterns

Factory Pattern, **Creational Pattern** kategorisinde yer alır.

Çünkü temel amacı nesne oluşturmaktır.

---

# Problem

Türüne göre içecek üreten bir kod yazdığımızı düşünelim. Factory olmadan, üretim kararı çağıran kodun içine yayılır:

```csharp
IIcecek icecek;

if (tur == "kahve")
    icecek = new Kahve();
else if (tur == "cay")
    icecek = new Cay();
else
    throw new ArgumentException("Bilinmeyen içecek");

icecek.Hazirla();
```

Sorunlar:

* Bu `if/else` bloğu, içecek üretilen her yerde tekrar eder
* Çağıran kod, `Kahve` ve `Cay` gibi somut sınıfları doğrudan tanımak zorundadır
* Yeni bir içecek eklendiğinde bütün bu blokları tek tek bulup güncellemek gerekir

---

# Temel Kavram: Üretimi Ayır

Factory'nin fikri basittir: nesnenin **nasıl üretildiği** bilgisini, onu **kullanan** koddan ayırırsın. Çağıran taraf yalnızca "bana şu türde bir nesne ver" der; hangi somut sınıfın `new`'leneceğini bilmez.

Önce ortak arayüz ve ürünler:

```csharp
public interface IIcecek
{
    void Hazirla();
}

public class Kahve : IIcecek
{
    public void Hazirla() => Console.WriteLine("Kahve hazırlandı");
}

public class Cay : IIcecek
{
    public void Hazirla() => Console.WriteLine("Çay hazırlandı");
}
```

---

# Simple Factory

En yaygın ve pratik uygulama, üretimi tek bir metoda toplayan "basit factory"dir:

```csharp
public static class IcecekFactory
{
    public static IIcecek Olustur(string tur)
    {
        return tur switch
        {
            "kahve" => new Kahve(),
            "cay"   => new Cay(),
            _ => throw new ArgumentException($"Bilinmeyen içecek: {tur}")
        };
    }
}
```

Kullanımı:

```csharp
IIcecek icecek = IcecekFactory.Olustur("kahve");
icecek.Hazirla();   // Kahve hazırlandı
```

Artık tip seçimi tek bir yerde yaşar. Çağıran kod ne `new` görür ne de somut sınıfları tanır; sadece `IIcecek` ile çalışır.

---

# Factory Method

GoF'un asıl "Factory Method" deseni bir adım ileri gider: üretimi bir `switch`'e değil, alt sınıflara bırakır. Üst sınıf akışı tanımlar; "hangi nesnenin üretileceği" kararını ise her alt sınıf kendisi verir.

```csharp
public abstract class IcecekDukkani
{
    // Akış burada sabit; üretim alt sınıfa bırakılıyor
    public IIcecek Siparis()
    {
        IIcecek icecek = IcecekOlustur();   // factory method
        icecek.Hazirla();
        return icecek;
    }

    protected abstract IIcecek IcecekOlustur();
}

public class KahveDukkani : IcecekDukkani
{
    protected override IIcecek IcecekOlustur() => new Kahve();
}

public class CayDukkani : IcecekDukkani
{
    protected override IIcecek IcecekOlustur() => new Cay();
}
```

Kullanımı:

```csharp
IcecekDukkani dukkan = new KahveDukkani();
dukkan.Siparis();   // Kahve hazırlandı
```

Burada `switch` bile yoktur; hangi dükkânı seçtiysen onun ürettiği içeceği alırsın.

---

# Doğrudan new ve Factory Arasındaki Fark

| Doğrudan new                          | Factory                                     |
| ------------------------------------- | ------------------------------------------- |
| Üretim kodun her yerine dağılır       | Üretim tek bir noktada toplanır             |
| Çağıran kod somut sınıfları tanır     | Çağıran kod yalnızca arayüzü tanır          |
| Yeni tip eklemek her yeri etkiler     | Yeni tip eklemek tek noktayı etkiler        |
| Test ederken değiştirmesi zordur      | Üretimi değiştirmek/yer değiştirmek kolaydır|

---

# Design Pattern ile Factory Arasındaki İlişki

Factory, Design Pattern ailesinin Creational kategorisindeki bir desendir.

Şema olarak:

```text
Design Pattern
│
├── Creational
│     ├── Singleton
│     ├── Builder
│     └── Factory   ← buradayız
│
├── Structural
│     ├── Adapter
│     ├── Facade
│     └── Decorator
│
└── Behavioral
      ├── Strategy
      ├── Observer
      └── Template Method
```

Bu nedenle:

"Factory nedir?"

sorusuna verilebilecek en kısa cevap:

"Factory, nesne üretme işini onu kullanan koddan ayırıp tek bir üretici yapıya devreden bir Creational Design Pattern'dir."

---

# Avantajları

Factory Pattern'in başlıca avantajları:

* Nesne üretimini tek bir noktada toplar
* Çağıran kodu somut sınıflardan ayırır (gevşek bağ)
* Yeni tip eklemeyi kolaylaştırır
* Üretim mantığını (varsayılan ayar, koşullu seçim) tek yerde yönetir

---

# Dezavantajları

Dikkat edilmesi gereken yönleri:

* Basit, tek tip üretimlerde gereksiz bir katman ekleyebilir
* Simple Factory'deki `switch`, yeni tip eklendikçe büyür
* Factory Method kullanılınca sınıf sayısı artar
* Aşırı kullanıldığında kodun takibini zorlaştırabilir

---

# Projende Nasıl Kullanılıyor?

Projende Factory'nin en net karşılığı generic form üretici yardımcılarındır: **`ShowEditForms<T>`** ve **`ShowListForms<T>`**.

Bir formu açmak için çağıran kod `new TestEditForm()` yazıp ardından yükleme/gösterme adımlarını elle yönetmez. Bunun yerine yalnızca tipi verir:

```csharp
ShowEditForms<TestEditForm>(...);
```

Geri kalan her şey — formun `new`'lenmesi, `Yukle()` çağrısı, pencere yönetimine eklenmesi, gösterilmesi — tek bir üretim noktasında olur. Yani:

* Üretim mantığı tek yerde toplanmıştır (Factory'nin özü)
* Çağıran kod, her formun kuruluş ayrıntısını bilmez
* Yeni bir form tipi eklemek, çağrı tarafında ekstra üretim kodu gerektirmez

Bu yardımcılar aynı zamanda Facade'a da yakındır: karmaşık açılış adımlarını tek basit çağrının arkasına gizlerler. Senin ERP'nde Factory ile Facade bu noktada iç içe çalışır — biri "neyi üreteceğini", diğeri "açılış sürecini" sadeleştirir.

---

# Sonuç

Factory Pattern, nesne üretme işini onu kullanan koddan ayırıp ayrı bir üretici yapıya devreden bir Creational Design Pattern'dir.

Temel fikri üretimi merkezileştirmektir: çağıran taraf "ne istediğini" söyler, "nasıl üretileceğini" factory bilir. Simple Factory bunu tek bir metotta toplar; Factory Method ise üretim kararını alt sınıflara bırakır.

Aynı arayüzün farklı somut türlerinin koşullara göre üretildiği her yerde Factory, kodu somut sınıflara bağımlı olmaktan kurtarır ve yeni tip eklemeyi tek bir noktayla sınırlayarak bakımı kolaylaştırır.
