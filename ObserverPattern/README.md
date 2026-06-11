# Observer Pattern Nedir?

## Giriş

Bazen bir nesnenin durumu değiştiğinde, başka birçok nesnenin bundan haberdar olması gerekir. Örneğin bir kahve makinesi kahveyi bitirdiğinde, ekranın "hazır" yazması, logun kayıt düşmesi, belki bir bildirimin gönderilmesi gerekir.

Bunu kahve makinesinin her tüketiciyi tek tek tanıyarak yapması ya da tüketicilerin sürekli "hazır mı?" diye sorması kötü bir çözümdür. Bu gibi durumlarda Behavioral Design Pattern kategorisindeki **Observer Pattern** kullanılabilir.

---

# Observer Pattern Nedir?

Observer Pattern, bir nesnenin (subject) durumu değiştiğinde, ona abone olmuş diğer nesnelere (observer) otomatik olarak haber verilmesini sağlayan bir tasarım desenidir.

Bu desenin amacı:

* Bir nesnedeki değişikliği, ilgilenen nesnelere otomatik bildirmek
* Haber veren ile haber alanı birbirine sıkı bağlamadan ayrı tutmak
* Aboneleri çalışma zamanında ekleyip çıkarabilmek
* Sürekli "değişti mi?" diye yoklama (polling) yapmayı önlemek

---

# Observer Pattern Hangi Kategoridedir?

Design Pattern'ler genel olarak üç kategoriye ayrılır:

* Creational Patterns
* Structural Patterns
* Behavioral Patterns

Observer Pattern, **Behavioral Pattern** kategorisinde yer alır.

Çünkü temel amacı nesneler arası haberleşmeyi (davranışı) düzenlemektir.

---

# Problem

Kahve makinesi kahveyi bitirdiğinde ekranı güncellemek istediğimizi düşünelim. Observer olmadan, makine ekranı doğrudan tanımak zorunda kalır:

```csharp
public class KahveMakinesi
{
    private Ekran _ekran;   // makine ekranı doğrudan tanıyor

    public void Yap(string kahve)
    {
        // ... kahve yapılır ...
        _ekran.Goster(kahve + " hazır");
    }
}
```

Sorunlar:

* Makine, ekranın varlığını ve nasıl çalıştığını bilmek zorunda (sıkı bağ)
* İkinci bir tüketici (log, bildirim) eklenince makineyi tekrar değiştirmek gerekir
* Tüketici sayısı arttıkça makinenin kodu şişer

İhtiyacımız: makinenin kime haber verdiğini bilmemesi; sadece "kahve hazır" diye **duyurması**.

---

# Temel Kavram: Subject ve Observer

Observer iki rolden oluşur:

* **Subject (özne):** Durumu değişen ve değişikliği duyuran nesne. Abone listesini tutar.
* **Observer (gözlemci):** Subject'e abone olan ve haber gelince tepki veren nesne.

Subject, observer'ların kim olduğunu tek tek bilmez; yalnızca "abonelerime haber ver" der. Bu sayede ikisi birbirinden bağımsız kalır.

---

# Klasik Observer (Manuel)

Önce gözlemci arayüzü ve abone listesini elle yöneten subject:

```csharp
public interface IKahveGozlemci
{
    void KahveHazir(string kahve);
}

public class KahveMakinesi
{
    private readonly List<IKahveGozlemci> _gozlemciler = new();

    public void Abone(IKahveGozlemci g)    => _gozlemciler.Add(g);
    public void AboneCik(IKahveGozlemci g) => _gozlemciler.Remove(g);

    public void Yap(string kahve)
    {
        // ... kahve yapılır ...
        foreach (var g in _gozlemciler)
            g.KahveHazir(kahve);   // herkese duyur
    }
}
```

Gözlemciler:

```csharp
public class Ekran : IKahveGozlemci
{
    public void KahveHazir(string kahve) => Console.WriteLine($"Ekran: {kahve} hazır!");
}

public class Log : IKahveGozlemci
{
    public void KahveHazir(string kahve) => Console.WriteLine($"Log: {kahve} yapıldı");
}
```

Kullanımı:

```csharp
var makine = new KahveMakinesi();
makine.Abone(new Ekran());
makine.Abone(new Log());

makine.Yap("Latte");
// Ekran: Latte hazır!
// Log: Latte yapıldı
```

Makine artık ekranı da logu da tanımıyor; sadece abonelerine duyuruyor.

---

# C# event ile Observer

C#'ta bu deseni elle yazmak çoğu zaman gereksizdir; çünkü `event` ve `delegate` zaten Observer'ın dile gömülmüş halidir. Abone listesini dil yönetir:

```csharp
public class KahveMakinesi
{
    public event Action<string> KahveHazir;

    public void Yap(string kahve)
    {
        // ... kahve yapılır ...
        KahveHazir?.Invoke(kahve);   // abonelere duyur
    }
}
```

Kullanımı:

```csharp
var makine = new KahveMakinesi();

makine.KahveHazir += k => Console.WriteLine($"Ekran: {k} hazır!");
makine.KahveHazir += k => Console.WriteLine($"Log: {k} yapıldı");

makine.Yap("Latte");
// Ekran: Latte hazır!
// Log: Latte yapıldı
```

Abone olmak `+=`, abonelikten çıkmak `-=` ile yapılır. Arayüz, abone listesi, `foreach` — hepsini dil hallediyor. C#'ta Observer dendiğinde pratikte kastedilen budur.

---

# Klasik Observer ve C# event Arasındaki Fark

| Klasik Observer (manuel)              | C# event / delegate                         |
| ------------------------------------- | ------------------------------------------- |
| Abone listesini elle yönetirsin       | Dil `+=` / `-=` ile yönetir                 |
| Kendi `IObserver` arayüzünü yazarsın  | Hazır `Action` / `EventHandler` yeter       |
| Daha fazla kod gerektirir             | Daha az, deyimsel kod                       |
| Saf OOP, dilden bağımsız              | C#'a özgü, pratik kullanım                  |

---

# Design Pattern ile Observer Arasındaki İlişki

Observer, Design Pattern ailesinin Behavioral kategorisindeki bir desendir.

Şema olarak:

```text
Design Pattern
│
├── Creational
│     ├── Singleton
│     ├── Builder
│     └── Factory
│
├── Structural
│     ├── Adapter
│     ├── Facade
│     └── Decorator
│
└── Behavioral
      ├── Strategy
      ├── Template Method
      └── Observer   ← buradayız
```

Bu nedenle:

"Observer nedir?"

sorusuna verilebilecek en kısa cevap:

"Observer, bir nesnenin durumu değiştiğinde ona abone olan nesnelere otomatik haber veren bir Behavioral Design Pattern'dir. C#'ta `event` mekanizması bu desenin dile gömülmüş halidir."

---

# Avantajları

Observer Pattern'in başlıca avantajları:

* Haber veren ile haber alanı birbirinden ayırır (gevşek bağ)
* Aboneleri çalışma zamanında ekleyip çıkarabilirsin
* Subject, observer'ların kim olduğunu bilmek zorunda değildir
* Sürekli yoklama (polling) yerine olay anında bildirim sağlar

---

# Dezavantajları

Dikkat edilmesi gereken yönleri:

* Abonelik kaldırılmazsa bellek sızıntısına yol açabilir (özellikle event'lerde `-=` unutulursa)
* Bildirim zinciri uzayınca akışı takip etmek zorlaşır
* Bildirim sırasının garantisi yoktur
* Çok sayıda observer, beklenmedik yan etkilere yol açabilir

---

# Projende Nasıl Kullanılıyor?

Projende Observer, C# event'leri biçiminde her yerde. En net örneğin `IletisimBilgileriTable`'daki **`FiltreDegisti`** event'idir:

* Parent tablo (subject) bir filtre değiştiğinde — örneğin `PasifGoster` veya `BagliIletisimleriGoster` toggle'ı değiştiğinde — `RaiseFiltreDegisti()` ile duyuru yapar.
* Çocuk tablolar (observer) bu event'e abone olur ve haber gelince kendilerini yeniden listeler.
* Parent, hangi çocuğun dinlediğini bilmez; sadece "filtre değişti" der.

Bu, Observer'ın ders kitabı tanımıyla birebir örtüşür. Ayrıca DevExpress grid event'leri (`FocusedRowChanged`, `CellValueChanged`, `Toggled`) de aynı desendir: kontrol subject'tir, senin handler'ın observer. Yani projede Observer'ı zaten her gün, `+=` yazdığın her yerde kullanıyorsun.

---

# Sonuç

Observer Pattern, bir nesnenin durumu değiştiğinde ona abone olan nesnelere otomatik haber veren bir Behavioral Design Pattern'dir.

Temel fikri, haber veren (subject) ile haber alanı (observer) birbirinden ayırmaktır: subject yalnızca duyurur, kimin dinlediğini bilmez. C#'ta `event` ve `delegate`, bu deseni dilin içine gömerek elle abone listesi yönetmeyi gereksiz kılar.

Bir nesnedeki değişikliğin birden çok yere yayılması gereken her durumda Observer, bağımlılığı gevşetir ve sistemi yeni dinleyiciler eklemeye açık tutar — tıpkı bir ERP'nin tablo filtrelerinde olduğu gibi.
