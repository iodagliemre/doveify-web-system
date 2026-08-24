# Display / Body Contrast

**Kaynak:** Doveify Web System · `@doveify/display-body-contrast`
**Üstünde:** `.doveify/rules/core.md` §1 otorite sırası geçerlidir.
**Standart:** `.doveify/rules/pattern-metadata.md`

> Bu pattern bir font seçimi veya ölçek tablosu dayatmaz. **İki register arasındaki
> mesafeyi** ve o mesafenin neyle kurulduğunu tarif eder.

---

## NAME

`display-body-contrast`

## CATEGORY

`typography`

## PROBLEM

Display ve body tipografisi aynı skaladan ve aynı ağırlık mantığından türetildiğinde
sayfanın sesi olmaz — her şey tek bir ses tonunda okunur. Hiyerarşiyi taşıma yükü
sözcüklerden **kutulara ve renge** kayar; sayfa bir metin gibi değil bir arayüz gibi
davranır.

Bu pattern iki register arasında bilinçli ve büyük bir mesafe kurar: ölçekte, ve
**ölçek dışında en az bir boyutta daha.**

## USE WHEN

- İçerikte gerçek bir iki seviyeli ses var: iddialar ve onları açan metin
- Tipografinin marka karakterini taşıması bekleniyor
- Sayfada okumak gerçek bir eylem — metin dekoratif değil
- Lock birden fazla face veya gerçek aralığı olan bir variable family sağlıyor

## AVOID WHEN

- Yoğun bilgi/veri arayüzü — orada tekdüze okunabilirlik sesten değerlidir
- İçerik ağırlıklı olarak kısa ve eşit etiketlerden oluşuyor (nav, tablo, form)
- Display muamelesinin taşımayacağı yazı sistemlerine lokalizasyon var
- Yalnızca tek bir face'e izin var ve o face'in kullanılabilir display aralığı yok
- İçerik uzunluğu öngörülemiyor ve display satırları kırılacak

## DESIGN-LOCK INPUTS

- İzin verilen display ve body face'leri
- İki register arasındaki **minimum** karşıtlık oranı
- İzin verilen skala adımları
- Karşıtlığın ölçek dışında hangi boyutları kullanabileceği (ağırlık, genişlik, case,
  tracking, stil)
- Body ölçüsü (measure) sınırları
- Minimum body punto tabanı
- Ara bir register'a izin verilip verilmediği

## RESPONSIVE PRINCIPLE

Değişmeyen şey **iki register arasındaki karşıtlık oranıdır**, ikisinin de mutlak
boyutu değil.

Genişlik daraldıkça display, body'den **daha hızlı** sıkışır. Body'nin altına inmediği
bir okunabilirlik tabanı vardır ve bu taban asla karşıtlık uğruna delinmez.

Oran, bu tabanı bozmadan korunamıyorsa karşıtlık düzleşmez — **ölçek dışı bir boyuta
taşınır** (ağırlık, case, tracking). Ses küçük ekranda kısılmaz, biçim değiştirir.

## ACCESSIBILITY NOTES

- Görsel register semantik seviyenin yerine geçmez: display gibi görünen bir satır,
  ancak gerçekten başlıksa `h1`'dir
- Body hiçbir koşulda lock'un minimum puntosunun altına inmez; satır yüksekliği ve ölçü
  okunabilir aralıkta kalır
- Karşıtlık bir **tipografi** kararıdır, renk kararı değil. Display de WCAG AA sağlar
- Kullanıcının font boyutu tercihi korunur: göreli birim kullanılır, yalnızca viewport
  tabanlı ölçekleme ile sabitlenmez
- Uç tracking ve uç ağırlık okunabilirliği düşürebilir; bu muameleler **body'ye
  uygulanmaz**
- Büyük harfe çevrilmiş display metin ekran okuyucuda harf harf okunabilir; dönüşüm
  CSS ile yapılır, kaynak metinde değil

## PERFORMANCE NOTES

- İki register çoğu zaman iki font ailesi demektir; her biri ağ ve render maliyetidir.
  **Tek bir variable family** çoğu durumda aynı karşıtlığı tek yükle verir
- Akışkan display ölçeklendirmesi CLS üretebilir: metrik uyumlu fallback ve bilinçli
  `font-display` zorunludur
- Display face yalnızca display ölçeğinde kullanılan karakterlere subset edilir
- Kullanılmayan ağırlıklar paketlenmez
- Karşıtlık ağırlıkla kuruluyorsa, gerçekten kullanılan ağırlık sayısı kadar dosya
  yüklenir — "bütün aile" indirilmez

## TEMPLATE RISK

**Birinci risk:** tek bir sans'ın dört farklı puntosu ve başlıklar için bold. Sesi
olmayan sayfa; hiyerarşi kutulara devredilir.

**İkinci risk — ve bugün daha yaygın olanı:** devasa, sıkı tracking'li geometrik sans
başlık + gri body metin. Bu da bir şablondur; her AI üretimi landing page'de birebir
aynı görünür ve CLAUDE.md §2'deki "template hissi veren tekrar" maddesine girer.

**Denetim:** karşıtlık **en az iki boyutta** alınmış bir karar olmalı ve markaya
izlenebilmelidir. Yalnızca puntoyla kurulmuşsa ya da gerekçesi "şu an böyle
yapılıyor" ise pattern uygulanmamıştır.

## CUSTOMIZATION AXES

- Karşıtlığı taşıyan boyutlar (ölçek, ağırlık, genişlik, case, tracking, stil, aile)
- Mesafenin büyüklüğü
- Display'in ayrı bir face mi yoksa aynı ailenin uçları mı olduğu
- Mesafenin köprülenip köprülenmediği — ara register var mı
- Body ölçüsü ve dikey ritmi
- Display'in satır kırma ve hizalama davranışı

## ANTI-PATTERN

İki biçim:

1. **"Tek sans, dört punto, başlıklar bold."** Tipografi hiçbir iş yapmaz; hiyerarşi
   kart kenarlıklarına ve renkli rozetlere devredilir.
2. **"Devasa sıkı başlık + gri body."** Ses var gibi görünür ama ödünç alınmıştır;
   marka değil dönem taşır.

İkisi de CLAUDE.md §2'nin "composition ve typography birlikte ana belirleyicidir"
maddesini boşa düşürür.

## VARIATION LOGIC

Mesafe **radikal biçimde farklı malzemelerden** kurulabilir:

- Nötr bir sans body'ye karşı karakterli bir serif display
- Tek bir variable ailenin ağırlık ve genişlik uçları
- Küçük ama ayırt edici bir display muamelesine karşı cömert bir body

Üçü de aynı pattern'dir ve hiçbiri diğerine benzemez. Sabit olan mesafenin varlığı ve
gerekçesidir; malzeme değil.

## SELECTION SIGNALS

- Lock birden fazla face veya gerçek aralığı olan bir variable family tanımlıyor
- Lock tipografiyi marka taşıyıcısı olarak konumluyor
- İçerikte büyük dizilmeye değer iddialar var
- Lock bir okuma ölçüsü tanımlıyor
- Lock display için gerçek bir ölçek bütçesi ayırıyor

## REJECTION SIGNALS

- Lock tek ve dar aralıklı bir face'e izin veriyor
- İçerik tekdüze yoğun veya tablosal
- Lock display ölçeğini body'ye yakın bir tavanla sınırlıyor
- Ürün veri odaklı; okuma ikincil
- Display face'in karşılamadığı çok yazı sistemli lokalizasyon var

---

**İlgili:** [[asymmetric-editorial-hero]] baskınlığı tipografiye taşıdığında bu pattern'e
dayanır; [[editorial-rhythm]] mesafenin sayfa boyunca nasıl tekrarlandığını belirler.
