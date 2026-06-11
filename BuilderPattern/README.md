# Builder Pattern ve Fluent Builder Nedir?

## Giriş

Yazılım geliştirme sürecinde bazı nesneler oldukça karmaşık hale gelebilir. Çok sayıda property, farklı konfigürasyon seçenekleri ve oluşturma kuralları bulunan nesneleri tek seferde oluşturmak hem okunabilirliği azaltır hem de hata yapma riskini artırır.

Bu gibi durumlarda Creational Design Pattern kategorisinde yer alan **Builder Pattern** kullanılabilir.

---

# Builder Pattern Nedir?

Builder Pattern, karmaşık nesnelerin adım adım oluşturulmasını sağlayan bir tasarım desenidir.

Bu desenin amacı:

* Nesne oluşturma sürecini yönetmek
* Oluşturma adımlarını sadeleştirmek
* Okunabilirliği artırmak
* Karmaşık constructor kullanımını azaltmak

---

# Builder Pattern Hangi Kategoridedir?

Design Pattern'ler genel olarak üç kategoriye ayrılır:

* Creational Patterns
* Structural Patterns
* Behavioral Patterns

Builder Pattern, **Creational Pattern** kategorisinde yer alır.

Çünkü temel amacı nesne oluşturmaktır.

---

# Problem

Aşağıdaki örnekte basit bir Person nesnesi oluşturulmaktadır.

```csharp
var person = new Person
{
    Name = "Rami",
    Surname = "Koco",
    Age = 32,
    City = "Istanbul",
    Country = "Turkey"
};
```

Bu örnek küçük olduğu için okunabilir görünmektedir.

Ancak nesne büyüdükçe:

* 10+
* 20+
* 30+

adet property içermeye başladığında kod karmaşıklaşabilir.

---

# Klasik Builder

Öncelikle basit bir Person sınıfı oluşturalım.

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```

Builder sınıfı:

```csharp
public class PersonBuilder
{
    private readonly Person _person = new();

    public void SetName(string name)
    {
        _person.Name = name;
    }

    public void SetAge(int age)
    {
        _person.Age = age;
    }

    public Person Build()
    {
        return _person;
    }
}
```

Kullanımı:

```csharp
var builder = new PersonBuilder();

builder.SetName("Rami");
builder.SetAge(32);

var person = builder.Build();
```

Bu yapı Builder Pattern'i uygular ancak zincirleme kullanım sunmaz.

---

# Fluent Interface Nedir?

Fluent Interface, metodların kendi nesnesini döndürerek zincirleme çağrılmasına imkan veren bir yaklaşımdır.

Örnek:

```csharp
obj.DoA()
   .DoB()
   .DoC();
```

Bu yapı kodun daha okunabilir olmasını sağlar.

---

# Fluent Builder Nedir?

Fluent Builder, Builder Pattern'in Fluent Interface ile uygulanmış halidir.

Builder mantığı aynıdır.

Fark, metodların kendi nesnesini döndürmesidir.

Örnek:

```csharp
public class PersonBuilder
{
    private readonly Person _person = new();

    public PersonBuilder WithName(string name)
    {
        _person.Name = name;
        return this;
    }

    public PersonBuilder WithAge(int age)
    {
        _person.Age = age;
        return this;
    }

    public Person Build()
    {
        return _person;
    }
}
```

Kullanımı:

```csharp
var person = new PersonBuilder()
    .WithName("Rami")
    .WithAge(32)
    .Build();
```

Bu yapı Fluent Builder olarak adlandırılır.

---

# Klasik Builder ve Fluent Builder Arasındaki Fark

| Klasik Builder                  | Fluent Builder                              |
| ------------------------------- | ------------------------------------------- |
| Metodlar genellikle void döner  | Metodlar builder nesnesini döner            |
| Zincirleme kullanım yoktur      | Zincirleme kullanım vardır                  |
| Daha fazla satır kod gerektirir | Daha okunabilir görünür                     |
| Builder Pattern kullanır        | Builder Pattern + Fluent Interface kullanır |

---

# Design Pattern ile Fluent Builder Arasındaki Fark

Bu iki kavram aynı seviyede değildir.

Builder bir Design Pattern'dir.

Fluent Builder ise Builder Pattern'in uygulanış biçimlerinden biridir.

Şema olarak:

```text
Design Pattern
│
├── Singleton
├── Factory
├── Strategy
└── Builder
      │
      ├── Klasik Builder
      └── Fluent Builder
```

Bu nedenle:

"Fluent Builder bir Design Pattern midir?"

sorusuna verilebilecek en doğru cevap:

"Builder bir Design Pattern'dir. Fluent Builder ise Builder Pattern'in Fluent Interface kullanılarak uygulanmış versiyonudur."

---

# Avantajları

Builder Pattern'in başlıca avantajları:

* Daha okunabilir kod
* Karmaşık nesne oluşturmayı kolaylaştırma
* Constructor bağımlılığını azaltma
* Oluşturma adımlarını kontrol etme
* Validasyon ekleyebilme

Örnek:

```csharp
public Person Build()
{
    if (string.IsNullOrWhiteSpace(_person.Name))
        throw new Exception("Name zorunludur.");

    return _person;
}
```

---

# Sonuç

Builder Pattern, karmaşık nesnelerin oluşturulmasını kolaylaştıran önemli bir Creational Design Pattern'dir.

Fluent Builder ise bu deseni daha okunabilir hale getiren bir uygulama yaklaşımıdır.

Özellikle büyük projelerde ve çok sayıda property içeren nesnelerde Builder Pattern kullanımı kodun bakımını ve okunabilirliğini önemli ölçüde artırmaktadır.
