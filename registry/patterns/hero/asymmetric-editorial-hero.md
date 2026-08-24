# Asymmetric Editorial Hero

**Kaynak:** Doveify Web System · `@doveify/asymmetric-editorial-hero`
**Üstünde:** `.doveify/rules/core.md` §1 otorite sırası geçerlidir.
**Standart:** `.doveify/rules/pattern-metadata.md`

> **Bu pattern "sol metin + sağ görsel" DEĞİLDİR.** Bir kolon oranıyla tarif
> edilebiliyorsa pattern uygulanmamıştır. Asimetri bir yerleşim değil, bir
> **ağırlık ilişkisidir.**

---

## NAME

`asymmetric-editorial-hero`

## CATEGORY

`hero`

## PROBLEM

Açılış, okuyucunun ilk viewport içinde **bunun ne olduğunu, kime olduğunu ve markanın
register'ını** kavramasını sağlamak zorundadır — dengeli bir düzeni çözümlemek zorunda
kalmadan.

Simetrik bir hero hiçbir odak noktası vermez. 50/50 bölünmüş bir hero ise **iki
yarışan odak** verir ki bu odaksızlıkla aynı sonucu doğurur. Bu pattern, tek bir giriş
noktası kalacak biçimde ağırlığı bilinçli olarak eşitsiz dağıtır; ikincil öğeler
ondan **sonra** çözülür.

## USE WHEN

- Sayfanın tek ve net bir giriş noktasına ihtiyacı var
- Diğer her şeyi geçen **bir** birincil mesaj var
- Marka register'ı faydacı değil, düşünülmüş/editoryal
- Baskın tarafı taşıyacak güçte bir malzeme var: güçlü bir söz, güçlü bir görsel veya
  bilinçli kullanılabilecek gerçek bir boşluk bütçesi

## AVOID WHEN

- Gerçekten eşit iki mesaj var (iki ayrı kitle, iki eşit CTA) — asimetri birini yalan
  yere ikincilleştirir
- Kullanıcı ne istediğini bilerek geliyor (giriş, panel, arama) — faydacı sayfada odak
  zaten eylemdir
- Baskın tarafı taşıyacak içerik yok; zayıf içerikle kurulan asimetri dengesizlik olarak
  okunur, kararlılık olarak değil
- Hero'nun altındaki içerik hero'dan güçlüyse

## DESIGN-LOCK INPUTS

- Hangi öğenin baskın olduğu: söz, görsel veya boşluk
- Baskınlık oranının alt ve üst sınırı
- Asimetrinin ekseni: yatay, dikey veya optik
- İzin verilen odak kayması (focal offset)
- Boşluğun **aktif** bir öğe olarak kullanılıp kullanılamayacağı
- CTA'nın baskın öğeyle ilişkisi: içine gömülü mü, sonraya mı bırakılıyor
- Display tipografi izni ve ölçek tavanı

## RESPONSIVE PRINCIPLE

Asimetri bir **konum** değil, bir **ilişkidir.**

Genişlik yanal dengesizliği taşıyamaz hale geldiğinde, dengesizlik eşit blokların
üst üste yığılmasına **çökmez**; dikey eksene taşınır ve orada ölçek ile boşluk
üzerinden sürdürülür.

Değişmeyen şey şudur: **her ölçekte tek bir giriş noktası kalır.** Bu invaryant
korunamıyorsa pattern o ölçekte uygulanmamalıdır.

## ACCESSIBILITY NOTES

- Sayfada tek `h1`; görsel olarak baskın olan öğe DOM sırasında da ilk anlamlı öğedir
- Baskın öğe bir görselse **içeriktir**: gerçek bir `alt` alır, `alt=""` almaz
- Görsel üzerine metin: kontrast ortalamaya göre değil, **en kötü örtüşme bölgesine**
  göre ve her breakpoint'te ölçülür
- Optik denge için kullanılan offset/absolute yerleşim focus sırasını bozmaz
- Baskınlık yalnızca ölçekle kuruluyorsa, kullanıcının font boyutu tercihi bunu
  bozmamalı — göreli birim kullanılır
- CTA baskın öğeden sonra geliyorsa klavye sırasında da ulaşılabilir kalır

## PERFORMANCE NOTES

- Baskın öğe neredeyse her zaman **LCP**'dir: önceliklendirilir, lazy-load edilmez
- Kutusu önceden ayrılır (`aspect-ratio` / boyut bildirimi) — CLS ≈ 0
- Büyük tipografi ucuzdur, büyük görsel değildir. Bütçe darsa baskınlığı **tipografiye**
  taşımak doğru karardır
- LCP öğesine giriş animasyonu **eklenmez**; `.doveify/rules/motion.md` gerekçe kuralına
  da takılır, LCP'yi de geciktirir
- Boşluk baskın öğeyse maliyeti sıfırdır — bu bir zayıflık değil, bir avantajdır

## TEMPLATE RISK

**Birinci risk:** "büyük başlık solda, büyük görsel sağda, pill CTA altta" biçimine
çökmek. Bu tam olarak jenerik AI landing hero'sudur ve CLAUDE.md §2'nin üç maddesini
birden ihlal eder: otomatik 50/50 split, varsayılan pill button, şablon hissi.

**İkinci risk:** asimetrinin kendisinin formüle dönüşmesi. Her hero'nun 2/3–1/3 olması,
50/50 olmasından daha iyi değildir; sadece farklı bir şablondur.

**Denetim:** hero yalnızca bir kolon oranıyla tarif edilebiliyorsa pattern
uygulanmamıştır. Tarif "hangi öğe baskın, hangi eksende, neye karşı" cümlesiyle
kurulamıyorsa aynı sonuç.

## CUSTOMIZATION AXES

- Hangi öğenin baskın olduğu (söz / görsel / boşluk)
- Dengesizliğin ekseni
- Baskınlık oranı ve genliği
- Odak kayması
- Boşluğun aktif katılımcı olup olmadığı
- CTA'nın baskın öğeyle ilişkisi
- Görselin kadrajı, ölçeği ve kenarla ilişkisi
- Register'ı taşıyan şeyin ölçek mi yoksa muamele mi olduğu

## ANTI-PATTERN

İki biçimi var, ikisi de aynı sıklıkta üretiliyor:

1. **Ortalanmış hero:** ortalanmış `h1` + alt başlık + iki pill buton + arkada gradient
   blob. Odak yok; her şey eşit derecede önemli görünüyor, yani hiçbiri önemli değil.
2. **50/50 split:** solda başlık ve CTA, sağda ürün ekran görüntüsü veya stok görsel.
   İki odak yarışır; göz nereye gideceğini bilemez.

İkisi de binlerce sitede birebir aynı görünür ve markadan hiçbir şey taşımaz.

## VARIATION LOGIC

Pattern **ilişkiyi** kısıtlar, düzenlemeyi asla. Bu yüzden aynı pattern birbirini
hatırlatmayan sonuçlar üretir:

- Baskınlık tek bir kelimenin uç ölçeğinde olabilir; görsel hiç bulunmayabilir
- Baskınlık bir kenardan taşan görselde olabilir; söz küçük ve kesin kalır
- Baskınlık **boşlukta** olabilir; büyük ve bilinçli bir sessizliğin kenarında az sayıda
  kesin öğe durur

Üçü de bu pattern'dir. Ortak olan tek giriş noktası disiplinidir, görüntü değil.

## SELECTION SIGNALS

- Lock tek bir birincil mesaj tanımlıyor
- Lock güçlü bir asset **veya** karakterli bir display face sağlıyor
- Lock "editorial", "confident", "restrained" gibi bir register kullanıyor
- Lock cömert bir negative space bütçesi ayırıyor
- Lock ortalanmış/dengeli kompozisyonu şart koşmuyor
- Hero birincil CTA'nın sahibi

## REJECTION SIGNALS

- Lock dengeli veya ortalanmış kompozisyon şart koşuyor
- Lock iki eşit kitle veya iki eşit CTA tanımlıyor
- Lock büyük tipografiyi yasaklıyor **ve** kullanılabilir bir hero görseli yok
- Sayfa işlemsel (transactional) — kullanıcı okumaya değil yapmaya geliyor
- Hero içeriği üretim anında bilinmiyor veya kullanıcı üretimli

---

**İlgili:** [[display-body-contrast]] baskınlığın tipografiyle taşındığı durumu tanımlar;
[[quiet-premium-navigation]] hero'nun giriş noktasıyla yarışmayan nav davranışını;
[[compositional-image-treatment]] baskın öğe görselse onun rolünü.
