# Compositional Image Treatment

**Kaynak:** Doveify Web System · `@doveify/compositional-image-treatment`
**Üstünde:** `.doveify/rules/core.md` §1 otorite sırası geçerlidir.
**Standart:** `.doveify/rules/pattern-metadata.md`

> Bu pattern bir kadraj, oran veya filtre dayatmaz. Her görselin **kompozisyonda hangi
> yapısal işi yaptığını** beyan etmesini zorunlu kılar.

---

## NAME

`compositional-image-treatment`

## CATEGORY

`imagery`

## PROBLEM

Dekor olarak kullanılan görsel — yuvarlatılmış köşeli kart küçük resimleri, boşluk
dolduran stok fotoğraflar — sayfaya **ağırlık ve yükleme süresi** ekler, anlam eklemez.

Bu pattern tek bir şey talep eder: görsel kompozisyonda yapısal bir iş yapmalıdır —
odağı taşımak, bir kenarı tanımlamak, ölçek kurmak, kanıt sunmak — ya da orada
bulunmamalıdır.

## USE WHEN

- Görsel gerçekten mesajın parçası (ürün, mekân, kişi, yapılan iş)
- Görsel kalitesi ve art direction kontrol edilebiliyor
- Kompozisyonda görselin dolduracağı tanımlı bir rol var
- Performans bütçesi görseli **rolünün gerektirdiği ölçekte** taşıyabiliyor

## AVOID WHEN

- Elde yalnızca jenerik stok var — hiçbir muamele anlamsız bir görseli kurtarmaz
- Görseller kullanıcı üretimli; kadrajı ve kalitesi öngörülemiyor (orada bir
  **containment** pattern'i gerekir, bu değil)
- Performans bütçesi rolün gerektirdiği ölçeği kaldırmıyor
- İçerik tipografiyle veya bir diyagramla daha iyi anlatılıyor
- Görselin rolü sorulduğunda cevap "boşluğu dolduruyor"

## DESIGN-LOCK INPUTS

- İzin verilen görsel rolleri
- İzin verilen kadrajlar ve en-boy oranları
- Görselin container'ı taşıp taşamayacağı (bleed)
- Muamele kuralları (duotone, grain, yok)
- Odak noktası (focal point) politikası
- Görselin metin taşıyıp taşıyamayacağı
- Sayfa başına asset ağırlık bütçesi ve görsel adedi

## RESPONSIVE PRINCIPLE

Değişmeyen şey görselin **rolüdür**; kadrajı değil.

Genişlik değiştiğinde aynı çerçeve tekdüze küçültülmez — **kadraj ve oran değişir**,
çünkü korunması gereken şey odak ilişkisidir.

Bir rol belirli bir ölçekte artık taşınamıyorsa görsel küçültülerek dekora
dönüştürülmez; **kaldırılır.** Küçülmüş bir kompozisyon öğesi, kompozisyon öğesi
değildir.

## ACCESSIBILITY NOTES

- Kompozisyonda iş yapan görsel **içeriktir**: neye katkı verdiğini anlatan gerçek bir
  `alt` alır, `alt=""` almaz
- Dekoratif görsel `alt=""` alır — ancak bu pattern'in kendi mantığına göre saf dekoratif
  görsel zaten sayfada bulunmamalıdır
- Görsel üzerine metin: kontrast **gerçek örtüşme bölgesine** göre ve her breakpoint'te
  ölçülür; ortalama parlaklık yeterli kanıt değildir
- Zorunlu metin görselin içine gömülmez
- Duotone/overlay gibi muameleler kontrastı AA'nın altına indiremez
- Görselde herhangi bir geçiş varsa `prefers-reduced-motion` uygulanır
  (`.doveify/rules/motion.md` §4)
- Odak noktası kırpması anlamı değiştirmemeli — kişi veya ürün kadraj dışında kalmamalı

## PERFORMANCE NOTES

- Kompozisyonel görsel genelde **LCP**'dir: önceliklendirilir, katlama üstünde
  lazy-load edilmez
- `width`/`height` veya `aspect-ratio` her zaman bildirilir — CLS ≈ 0
  (`.doveify/rules/quality-gates.md` §3)
- Art-directed kadrajlar `<picture>` / `srcset` gerektirir; tek büyük asset'i CSS ile
  kırpmak hem baytı hem anlamı israf eder
- Modern format kullanılır; teslim edilen en büyük varyant, render edilen en büyük
  kutuyla eşleşir
- Grain/duotone'un CSS/SVG filtresiyle yapılması sürekli bir paint maliyetidir;
  asset'e gömülmesi çoğu zaman ucuzdur
- **Registry kısıtı:** 5 MiB üstü kaynak dosyalar registry üzerinden dağıtılamaz
  (ADR-001); büyük medya ayrı bir yol ister

## TEMPLATE RISK

**Birinci risk:** eşit boyutlu, yuvarlatılmış köşeli görsellerin kart ızgarası; ya da
gradient blob üzerinde gölgeli, hafifçe eğik duran ürün ekran görüntüsü. İkisi de
CLAUDE.md §2'yi doğrudan ihlal eder: imagery dekor değil kompozisyonun parçası olmalı,
gereksiz gradient/glow kullanılmaz.

**İkinci risk — bu pattern'in kendi riski:** "kompozisyonel" sıfatının, her yere
full-bleed fotoğraf koyup üzerine metin yığmanın ruhsatı haline gelmesi. Bu da tek bir
şablondur, sadece daha pahalısı.

**Denetim:** her görselin **yazılı bir rolü** olmalı. Rol "boşluğu dolduruyor" ise
görsel kaldırılır. Rol iki farklı görsel için aynıysa, biri fazladır.

## CUSTOMIZATION AXES

- Rol (odak / kenar tanımlayıcı / ölçek kurucu / doku / kanıt)
- Kadraj ve en-boy oranı
- Container ile ilişki (içinde, taşan, üste binen, maskelenmiş)
- Muamele
- Görselin tipografiyle etkileşimi
- Sayfanın taşıyabileceği görsel sayısı
- Odak noktasının kompozisyondaki konumu

## ANTI-PATTERN

İki biçim:

1. **Yuvarlatılmış köşeli stok fotoğraf kart ızgarası.** Üç kolon, eşit oran, hafif
   gölge. Hiçbir görselin rolü yok; hepsi birbirinin yerine geçebilir — bu da hiçbirinin
   gerekmediğinin kanıtıdır.
2. **Gradient blob üzerinde yüzen gölgeli ürün ekran görüntüsü.** Bayt ekler, anlam
   eklemez, ve LCP'yi geciktirir.

## VARIATION LOGIC

Sabit olan **rol disiplinidir**; ifade serbesttir.

Aynı pattern şunları üretebilir: bir section'ın kenarını tanımlayan tek bir full-bleed
görsel; büyük tipografinin yanında ölçek kuran küçük ve kesin yerleştirilmiş bir görsel;
kanıt taşıyan bir görsel dizisi.

Üçü de bu pattern'dir. İki proje disiplini paylaşır, görüntüyü paylaşmaz.

## SELECTION SIGNALS

- Lock gerçek art direction veya kontrollü bir asset kütüphanesi sağlıyor
- İçerik somut şeylerle ilgili (ürün, mekân, kişi, iş)
- Lock gerçek bir görsel bütçesi ayırıyor
- Lock bleed'e ve asimetrik kadraja izin veriyor
- Lock bir odak noktası politikası tanımlıyor

## REJECTION SIGNALS

- Lock imagery'yi yasaklıyor veya illüstrasyon/yalnızca-tipografi diyor
- Elde yalnızca jenerik stok var
- Lock tekdüze kart ızgarası şart koşuyor
- Performans bütçesi rolün gerektirdiği ölçeği kaldırmıyor
- Görseller kullanıcı üretimli ve öngörülemez
- Asset'ler 5 MiB üstü ve alternatif dağıtım yolu yok

---

**İlgili:** [[asymmetric-editorial-hero]] baskın öğe görselse rolünü buradan alır;
[[editorial-rhythm]] geniş section'ların görsel yükünü sınırlar;
[[display-body-contrast]] görselin tipografiyle ilişkisini belirler.
