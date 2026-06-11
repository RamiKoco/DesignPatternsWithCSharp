# Template Method Pattern Nedir?

## Giriş

Birbirine çok benzeyen, aynı adımları aynı sırayla izleyen ama bazı adımları farklılaşan işlemler vardır. Örneğin bir kahve ile bir çay neredeyse aynı şekilde hazırlanır: su kaynatılır, demlenir, bardağa konur, ekstra eklenir. Sıra hep aynıdır; yalnızca "demleme" ve "ekstra ekleme" adımları farklıdır.

Bu ortak iskeleti her sınıfta tekrar tekrar yazmak hem kod tekrarına hem de tutarsızlığa yol açar. Bu gibi durumlarda Behavioral Design Pattern kategorisindeki **Template Method Pattern** kullanılabilir.

---

# Template Method Pattern Nedir?

Template Method Pattern, bir işlemin **iskeletini (adımların sırasını)** bir üst sınıfta sabitleyen, ancak bazı adımların alt sınıflar tarafından doldurulmasına izin veren bir tasarım desenidir.

Bu desenin amacı:

* Bir algoritmanın genel akışını tek bir yerde tanımlamak
* Değişmeyen adımları üst sınıfta tutmak
* Değişen adımları alt sınıflara bırakmak
* Kod tekrarını ve akış tutarsızlığını önlemek

---

# Template Method Pattern Hangi Kategoridedir?

Design Pattern'ler genel olarak üç kategoriye ayrılır:

* Creational Patterns
* Structural Patterns
* Behavioral Patterns

Template Method Pattern, **Behavioral Pattern** kategorisinde yer alır.

Çünkü temel amacı nesnelerin davranışını (algoritmanın işleyişini) düzenlemektir.

---

# Problem

Kahve ve çay hazırlayan iki sınıf yazdığımızı düşünelim. Template Method olmadan, ortak adımlar her sınıfta tekrar eder:

```csharp
public class Kahve
{
    public void Hazirla()
    {
        Console.WriteLine("Su kaynatılıyor");      // tekrar
        Console.WriteLine("Kahve demleniyor");
        Console.WriteLine("Bardağa koyuluyor");    // tekrar
        Console.WriteLine("Şeker ve süt ekleniyor");
    }
}

public class Cay
{
    public void Hazirla()
    {
        Console.WriteLine("Su kaynatılıyor");      // AYNI tekrar
        Console.WriteLine("Çay demleniyor");
        Console.WriteLine("Bardağa koyuluyor");    // AYNI tekrar
        Console.WriteLine("Limon ekleniyor");
    }
}
```

Sorunlar:

* "Su kaynatılıyor" ve "Bardağa koyuluyor" adımları kopyalanmış
* Akış sırası iki sınıfta ayrı ayrı yönetiliyor; birinde değişse diğeri unutulabilir
* Üçüncü bir içecek eklenince aynı iskelet bir kez daha kopyalanacak

---

# Temel Kavram: Sabit İskelet, Değişen Adımlar

Template Method'un fikri şudur: algoritmanın **sırası** tek bir yerde sabitlenir; yalnızca **değişen adımlar** alt sınıflara bırakılır.

Bunun için üst sınıf iki tür metot barındırır:

* **Değişmeyen adımlar:** Üst sınıfta yazılır, alt sınıflar bunlara dokunamaz
* **Değişen adımlar:** `abstract` olarak bırakılır, her alt sınıf kendine göre doldurur

İskeleti tutan asıl metoda **template method (şablon metot)** denir.

---

# Template Method Nasıl Uygulanır?

### 1. Üst sınıf: iskeleti tanımlar

`Hazirla()` template method'tur; adımların sırasını sabitler ve değiştirilemez (override edilemez). Değişen adımlar `abstract`'tır.

```csharp
public abstract class Icecek
{
    // Template Method — iskelet burada, sırası sabittir
    public void Hazirla()
    {
        SuyuKaynat();
        Demle();          // değişen adım
        BardagaKoy();
        EkstraEkle();     // değişen adım
    }

    // Değişmeyen adımlar — ortak, tek yerde
    private void SuyuKaynat() => Console.WriteLine("Su kaynatılıyor");
    private void BardagaKoy() => Console.WriteLine("Bardağa koyuluyor");

    // Değişen adımlar — alt sınıflar dolduracak
    protected abstract void Demle();
    protected abstract void EkstraEkle();
}
```

### 2. Alt sınıflar: yalnızca değişen adımları doldurur

```csharp
public class Kahve : Icecek
{
    protected override void Demle()     => Console.WriteLine("Kahve demleniyor");
    protected override void EkstraEkle() => Console.WriteLine("Şeker ve süt ekleniyor");
}

public class Cay : Icecek
{
    protected override void Demle()     => Console.WriteLine("Çay demleniyor");
    protected override void EkstraEkle() => Console.WriteLine("Limon ekleniyor");
}
```

### 3. Kullanım

```csharp
Icecek icecek = new Kahve();
icecek.Hazirla();
// Su kaynatılıyor
// Kahve demleniyor
// Bardağa koyuluyor
// Şeker ve süt ekleniyor
```

Alt sınıflar akışı değil, yalnızca kendi adımlarını belirler. İskelet tek bir yerde yaşadığı için tutarlılık garanti altındadır.

> İsteğe bağlı adımlar için `abstract` yerine `virtual` bir metot (hook) kullanılabilir: üst sınıf varsayılan bir davranış verir, alt sınıf isterse ezer.

---

# Template Method ve Strategy Arasındaki Fark

Template Method ile Strategy sık karıştırılır; ikisi de davranışı değiştirilebilir kılar ama yöntemleri farklıdır:

| Template Method                       | Strategy                                   |
| ------------------------------------- | ------------------------------------------ |
| Kalıtım (inheritance) ile çalışır     | Bileşim (composition) ile çalışır          |
| Davranış derleme zamanında belirlenir | Davranış çalışma zamanında değiştirilebilir|
| Algoritmanın bir kısmı değişir        | Algoritmanın tamamı dışarıdan verilir      |
| Üst sınıf akışı kontrol eder          | Çağıran taraf stratejiyi seçer             |

---

# Design Pattern ile Template Method Arasındaki İlişki

Template Method, Design Pattern ailesinin Behavioral kategorisindeki bir desendir.

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
│     └── Decorator
│
└── Behavioral
      ├── Strategy
      ├── Observer
      └── Template Method   ← buradayız
```

Bu nedenle:

"Template Method nedir?"

sorusuna verilebilecek en kısa cevap:

"Template Method, bir algoritmanın iskeletini üst sınıfta sabitleyip değişen adımları alt sınıflara bırakan bir Behavioral Design Pattern'dir."

---

# Avantajları

Template Method Pattern'in başlıca avantajları:

* Ortak akışı tek bir yerde tutarak kod tekrarını önler
* Algoritmanın sırasını ve tutarlılığını garanti eder
* Yeni bir varyant eklemeyi kolaylaştırır (sadece değişen adımları yazarsın)
* Değişmeyen kodu alt sınıfların yanlışlıkla bozmasını engeller

---

# Dezavantajları

Dikkat edilmesi gereken yönleri:

* Kalıtıma dayandığı için esnekliği Strategy kadar yüksek değildir
* Çok sayıda hook ve abstract adım, akışı takip etmeyi zorlaştırabilir
* Üst sınıftaki bir değişiklik tüm alt sınıfları etkiler (sıkı bağ)

---

# Projende Nasıl Kullanılıyor?

Template Method, tasarım kararı olarak değil; mimarinin doğal omurgası olarak her yerde. `Base*` sınıf hiyerarşisinin tamamı bu desendir:

* **`BaseEditForm` / `BaseListForm`** formun yaşam döngüsünü (yükleme → doğrulama → kaydetme) iskelet olarak tanımlar; `TestEditForm`, `PersonEditForm` gibi alt formlar yalnızca değişen adımları doldurur.
* **`BaseBll.Validation`** doğrulama akışının iskeletidir; her alt BLL kendi iş kuralını ekler.
* **`SilmeyeUygunMu`** virtual metodu Logo tarzı silme akışını tanımlar; "bu kayıt silinebilir mi" adımını her alt sınıf kendine göre override eder (hareket varsa engelle).
* **`BaseTablo`** `Listele()`, `HareketEkle()`, `HareketSil()` iskeletini sunar; `EPostaTable`, `TelefonTable` gibi çocuk tablolar bu adımları kendi verilerine göre doldurur.

Yani projedeki "iskelet base'de, ayrıntı alt sınıfta" çalışma biçimin, bu desenin ders kitabı tanımının ta kendisi.

---

# Sonuç

Template Method Pattern, bir algoritmanın iskeletini üst sınıfta sabitleyip değişen adımları alt sınıflara bırakan bir Behavioral Design Pattern'dir.

Temel fikri "sabit iskelet, değişen adımlar" ayrımıdır: akış tek bir yerde yaşar, varyasyon yalnızca gereken noktalarda olur.

Aynı akışı izleyip yalnızca birkaç adımı farklılaşan sınıfların olduğu her yerde, Template Method kod tekrarını ortadan kaldırır ve akış tutarlılığını güvence altına alır — büyük kod tabanlarında, tıpkı bir ERP'nin `Base*` katmanında olduğu gibi, bakımı belirgin biçimde kolaylaştırır.
