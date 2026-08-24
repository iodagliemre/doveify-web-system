# Quiet Premium Navigation

**Kaynak:** Doveify Web System · `@doveify/quiet-premium-navigation`
**Üstünde:** `.doveify/rules/core.md` §1 otorite sırası geçerlidir.
**Standart:** `.doveify/rules/pattern-metadata.md`

> Bu pattern bir nav yerleşimi tarif etmez. **Nav'ın istirahatte ne kadar dikkat talep
> edebileceğini** tarif eder.

---

## NAME

`quiet-premium-navigation`

## CATEGORY

`navigation`

## PROBLEM

Navigasyon, içerikle dikkat için yarışmadan bulunabilir ve yön verici kalmak zorundadır.

Gürültülü nav — ağır bar, yüksek kontrastlı pill CTA, kalıcı gölge, sürekli blur —
"uygulama chrome'u" sinyali verir ve editoryal içeriği ucuzlatır. Görünmez nav ise
kullanıcıyı kaybeder. Bu pattern nav'a **gereken en az varlığı** verir: nav görsel
ağırlığı ancak kullanıcının ona ihtiyacı olduğunda kazanır.

## USE WHEN

- İçerik ürünün kendisi (editoryal, portfolyo, marka sitesi)
- Üst seviye nav kümesi tek bakışta taranabilir büyüklükte (kabaca yedi ve altı)
- Marka register'ı ölçülülüğü ödüllendiriyor
- Birincil eylem hero'nun veya içeriğin içinde yaşıyor, nav'da değil

## AVOID WHEN

- IA derin veya karmaşık; yön bulma sayfanın **birincil işi** (dokümantasyon, büyük
  katalog, panel)
- Nav hedef değil **sık kullanılan araç** taşıyor
- Kullanıcı okumaya değil yapmaya geliyor
- Nav durum bilgisi taşımak zorunda (bildirim, oturum, sepet sayacı)
- Erişilebilirlik veya yasal gereklilik sürekli görünür kontrol dayatıyor

## DESIGN-LOCK INPUTS

- Üst seviye öğe sayısı tavanı
- Nav'ın scroll boyunca kalıcı olup olmadığı
- İstirahatteki ağırlık ile etkileşimdeki ağırlık sınırları
- Marka işaretinin her zaman görünür olup olmadığı
- İzin verilen açığa çıkarma (disclosure) mekanizması
- Kontrast tabanı
- Nav'ın hero ile ilişkisi: üstünde, ayrı veya kompozisyona dahil

## RESPONSIVE PRINCIPLE

**Ölçülülük ölçeklenir; ulaşılabilirlik ölçeklenmez.**

Genişlik arttıkça nav daha sessiz olabilir. Ancak her ölçekte iki şey sabittir: nav
**tek bir bilinçli eylemle** ulaşılabilir olmalı ve mevcut konum keşfedilebilir kalmalı.

Genişlik daralıp disclosure zorunlu hale geldiğinde, **açığa çıkaran tetikleyicinin
varlığı azaltılmaz.** Sessizlik hiçbir koşulda navigasyonu açan affordance'a uygulanmaz.

## ACCESSIBILITY NOTES

- `nav` landmark'ı erişilebilir bir ad taşır; `main`'e atlama bağlantısı bulunur
- **Sessiz, düşük kontrast demek değildir.** Tüm nav metni kendi gerçek zemini karşısında
  WCAG AA sağlar — görsel üzerindeki ve scroll sonrası durumlar dahil
- Mevcut sayfa programatik olarak bildirilir (`aria-current`); yalnızca görsel ağırlıkla
  değil
- Disclosure kontrolleri gerçek `button`'dır; `aria-expanded` taşır, focus doğru taşınır
  ve geri döner, `Escape` kapatır
- Focus göstergesi her durumda görünür kalır — sessiz estetik focus ring'i bastıramaz
- Yalnızca hover ile açığa çıkan nav kabul edilmez; klavye ve dokunma eşitliği zorunludur
- Nav durumu değişirken ekran okuyucu için anlam kaymaz

## PERFORMANCE NOTES

- Scroll'a tepki veren nav scroll dinleyicisi demektir; `IntersectionObserver` kullanılır,
  `scroll` handler değil (`.doveify/rules/motion.md` §2)
- Nav durum değiştirirken CLS üretmez: yükseklik önceden ayrılır veya nav içeriği iter
  yerine üstüne biner
- Kalıcı bir barda `backdrop-filter` mobilde **sürekli** compositing maliyetidir;
  gerekçelenir veya kaldırılır
- Nav varsayılan durumunda **JS olmadan** kullanılabilir olmalıdır
- Sessizlik genelde daha az DOM ve daha az stil demektir; bu pattern performansa borç
  değil kredi yazar

## TEMPLATE RISK

**Birinci risk:** "sticky, yarı saydam, blur'lu bar; solda logo, ortada linkler, sağda
yüksek kontrastlı pill CTA" biçimine çökmek. Bu her kaynak kütüphanenin kutudan çıkan
varsayılanıdır (CLAUDE.md §5) ve §2'nin iki maddesini ihlal eder: pill button varsayılan
değildir, gereksiz glassmorphism kullanılmaz. Ayrıca o CTA hero'nun kendi giriş
noktasıyla yarışır.

**İkinci risk:** "sessizlik"in yetersiz kontrast veya gizlenmiş nav için bahane olması.

**Denetim:** sessizlik yalnızca bir **ağırlık** kararıdır. Kontrast kararı veya
ulaşılabilirlik kararı olarak kullanıldığı anda pattern ihlal edilmiştir.

## CUSTOMIZATION AXES

- İstirahatteki ağırlık
- Konum ve yönelim
- Kalıcılık davranışı
- Açığa çıkarma mekanizması
- Mevcut konumun ifade biçimi
- Hero ile ilişki (üstüne binen, ayrı, kompozisyona dahil)
- Marka işaretinin katılıp katılmadığı
- Etkileşimde kazanılan ağırlığın miktarı

## ANTI-PATTERN

Varsayılan sticky glassmorphic header: ortalanmış linkler, sağda kontrastlı pill CTA,
scroll'da beliren gölge. Binlerce sitede görsel olarak birebir aynıdır, markadan hiçbir
şey taşımaz, mobilde sürekli blur maliyeti üretir ve hero'nun birincil eylemini ikinci
kez göstererek odağı böler.

## VARIATION LOGIC

Sessizliğin tek bir biçimi yoktur:

- Köşede yalnızca tipografiden ibaret bir nav
- Niyet sinyaliyle açığa çıkan bir nav
- Hero kompozisyonunun içine yerleşip scroll'da ondan ayrılan bir nav
- Kenar boşluğunda yaşayan dikey bir nav

Pattern nav'ın **nerede durduğunu** değil, **istirahatte ne kadar dikkat talep
edebileceğini** sabitler. Bu yüzden aynı disiplin görsel olarak akraba olmayan sonuçlar
üretir.

## SELECTION SIGNALS

- Lock içeriği ve imagery'yi chrome'un önüne koyuyor
- Nav kümesi küçük ve sabit
- Lock "restrained", "premium", "editorial" register'ı belirtiyor
- Lock sticky/blur chrome'u reddediyor
- Birincil CTA hero'ya ait
- Lock cömert negative space tanımlıyor

## REJECTION SIGNALS

- Lock sürekli görünür utility eylemleri şart koşuyor
- IA derin veya arama öncelikli
- Nav durum/bildirim göstermek zorunda
- Lock nav içinde belirgin bir CTA şart koşuyor
- Kitle görev odaklı; sayfa bir araç
- Üst seviye öğe sayısı taranabilir sınırın üstünde

---

**İlgili:** [[asymmetric-editorial-hero]] — nav'ın yarışmaması gereken giriş noktası
oradadır; [[editorial-rhythm]] nav'ın sayfa kadansına nasıl katıldığını belirler.
