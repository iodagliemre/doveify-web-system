# Editorial Rhythm

**Kaynak:** Doveify Web System · `@doveify/editorial-rhythm`
**Üstünde:** `.doveify/rules/core.md` §1 otorite sırası geçerlidir.
**Standart:** `.doveify/rules/pattern-metadata.md`

> Bu bir layout tarifi değildir. Tek bir görüntü akla geliyorsa yanlış okunmuştur.

---

## NAME

`editorial-rhythm`

## CATEGORY

`section`

## PROBLEM

Aynı genişlik, aynı padding ve aynı yoğunlukla dizilmiş bloklardan oluşan bir sayfa
gövdesi, bir argüman gibi değil bir liste gibi okunur. Okuyucu nerede olduğunu ve neyin
daha önemli olduğunu kaybeder; hiyerarşiyi taşıma yükü tipografiden kutulara ve renge
kayar.

Bu pattern, ardışık section'lar arasında **ölçü, yoğunluk ve dikey nefes** farkı
kurarak sayfaya bir kadans verir.

## USE WHEN

- Sayfada ardışık **dört veya daha fazla** içerik section'ı var
- İçerikte gerçek bir hiyerarşi var — bloklar birbirinin eşiti değil
- Okuyucu sayfayı doğrusal olarak kat ediyor
- İçeriğin uzunluğu ve şekli üretim anında biliniyor

## AVOID WHEN

- İçerik gerçekten eşit ağırlıklı bir küme (ürün listesi, arama sonucu, arşiv) — orada
  eşitlik dürüst temsildir, ritim yalan söyler
- Tek section'lık sayfa; kıyas olmadan ritim algılanmaz
- İçerik kullanıcı üretimli ve uzunluğu öngörülemiyor
- Sayfanın işi okumak değil taramak

## DESIGN-LOCK INPUTS

- İzin verilen ölçü (measure) aralığı — en dar ve en geniş sınır
- Dikey boşluk skalası ve kaç adım içerdiği
- İzin verilen yoğunluk seviyeleri
- Full-bleed'e izin verilip verilmediği
- Sayfa başına section bütçesi
- Ritmin yönü: hızlanan, yavaşlayan veya değişken

## RESPONSIVE PRINCIPLE

Ritmi taşıyan şey **göreli karşıtlıktır**, mutlak değerler değil.

Genişlik daraldıkça önce **yatay** varyasyon çöker — tüm section'lar tek bir ölçüye
yakınsar — ve ritim **dikey boşluk ile yoğunluğa** taşınır. Okuma ölçüsü hiçbir koşulda
yatay çeşitliliği korumak için feda edilmez.

## ACCESSIBILITY NOTES

- Ritim hiyerarşinin **tek** sinyali olamaz; heading seviyeleri onu bağımsız taşır
- Her section landmark'ının erişilebilir bir adı olur
- Full-bleed section'larda metin yine okunabilir ölçü içinde kalır
- DOM sırası görsel sırayla aynıdır; ritim için kaynak sırası bozulmaz
- Yoğunluk farkı kontrast düşürerek üretilmez — WCAG AA her section'da geçerlidir

## PERFORMANCE NOTES

- Değişken genişlikler JS ile ölçüm yapmaya davet eder; çözüm **yalnızca CSS** olmalı
- En geniş section'lar en ağır görselleri davet eder — asset bütçesi ritimle birlikte
  planlanır
- Section girişlerine animasyon eklemek hem motion gerekçe kuralını zorlar hem CLS riski
  üretir; ritim hareketle değil yerleşimle kurulur
- Değişken yükseklikler geç yüklenen içerikle birleşince kayma üretir; yer önceden ayrılır

## TEMPLATE RISK

Jenerik sayfa her section'a aynı `max-width` ve `padding` verir — CLAUDE.md §2'nin
doğrudan yasakladığı durum.

Ancak bu pattern'in kendi riski **terstir:** ritmin kendisi şablona dönüşebilir. En sık
görülen biçimi "bir geniş, bir dar" alternasyonudur; iki section sonra tahmin edilebilir
hale gelir ve ritim olmaktan çıkıp desen olur.

**Denetim:** hiçbir section'ın muamelesi sırasından (index) tahmin edilebilir olmamalı.
Edilebiliyorsa ritim değil, şablon üretilmiştir.

## CUSTOMIZATION AXES

- Ölçü (measure)
- Dikey boşluk
- İçerik yoğunluğu
- Hizalama ekseni
- Bleed davranışı
- Zemin ağırlığı
- Section başlığının giriş noktası ve ölçeği
- Ritmin yönü ve genliği

## ANTI-PATTERN

Hero'dan sonra gelen, her biri `max-w-6xl mx-auto py-20` içinde üç kolonluk kart
ızgarası taşıyan N adet birbirinin aynı section. Sayfa bir belge gibi değil, bir
yönetim panelinin listesi gibi okunur; CLAUDE.md §2'deki "gereksiz card grid" ve
"aynı ritim" maddelerinin ikisini birden ihlal eder.

## VARIATION LOGIC

Ritim bir **dizi** özelliğidir, section başına bir stil değil. Bu yüzden varyasyon
tek tek section'lardan değil, dizinin kendisinden gelir.

Aynı pattern'i kullanan iki proje şunlarda ayrışır: hangi section en geniş olanı,
ritmin hızlanıp hızlanmadığı, karşıtlığın yatay mı dikey mi taşındığı, ve nefesin
nerede en derin olduğu. Ortak olan disiplindir, görüntü değil.

## SELECTION SIGNALS

- Lock en az üç adımlı bir boşluk skalası tanımlıyor
- Lock "editorial", "narrative", "long-form" gibi bir register belirtiyor
- Lock bir okuma ölçüsü tanımlıyor
- Lock tekdüze ızgarayı açıkça reddediyor
- İçerik envanterinde bloklar arasında gerçek ağırlık farkı var

## REJECTION SIGNALS

- Lock tek bir sabit container genişliği sabitliyor
- Lock tek kolon sayılı katı bir grid sistemi dayatıyor
- İçerik bir katalog veya dizin
- Lock "taranabilir, okunabilir değil" diyor
- Section sayısı üçün altında

---

**İlgili:** [[display-body-contrast]] ritmin tipografik karşılığını taşır;
[[compositional-image-treatment]] geniş section'ların görsel yükünü tanımlar.
