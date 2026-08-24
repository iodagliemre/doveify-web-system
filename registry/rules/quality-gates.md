# Doveify — Quality Gates

**Kaynak:** Doveify Web System · `@doveify/quality-gates`
**Üstünde:** `.doveify/rules/core.md` §1 otorite sırası geçerlidir.

---

## 1. Eşikler

| Ölçüt | Eşik |
|---|---|
| Mobile Lighthouse | **>= 90** (hedef **95+**) |
| CLS | **≈ 0** |
| HTML | semantic |
| Accessibility | **WCAG AA** |
| JS / dependency | gereksiz olan yok |

Bunlar hedef değil, kapıdır. Altında kalan çıktı teslim edilmez.

---

## 2. Çatışma Davranışı

Bir tasarım kararı bu eşikleri düşürüyorsa:

1. **Önce kararı taşımanın başka bir yolunu ara.** Çoğu durumda uygulama biçimi
   değişir, tasarım kararı değişmez.
2. Karar **DESIGN-LOCK** kaynaklıysa **tasarım kararını değiştirme.** `core.md` §1.1
   uyarınca `DESIGN-LOCK CONFLICT:` olarak raporla.
3. Karar DESIGN-LOCK kaynaklı değilse ve taşınamıyorsa, ödünleşimi açıkça yaz;
   sessizce eşiğin altına inme.

---

## 3. Denetim Kalemleri

**Semantic HTML**
- Landmark elemanları kullanılır (`header`, `nav`, `main`, `section`, `footer`)
- Heading hiyerarşisi atlanmaz; sayfada tek `h1`
- Buton buton, link linktir — `div` ile taklit edilmez

**Accessibility (WCAG AA)**
- Metin kontrastı ≥ 4.5:1, büyük metin ≥ 3:1
- Görünür focus state; `outline: none` telafisiz bırakılmaz
- Tüm etkileşim klavyeyle erişilebilir
- Anlam taşıyan görsellerde `alt`; dekoratif görsellerde `alt=""`
- Form alanlarında ilişkilendirilmiş `label`

**CLS ≈ 0**
- Görsel ve video'da `width`/`height` veya `aspect-ratio` bildirilir
- Font yüklemesi layout kaydırmaz (`font-display`, metrik uyumlu fallback)
- Geç yüklenen içerik yer ayırır; içerik üstüne enjekte edilmez

**JS / dependency**
- Her bağımlılık `core.md` §4 değerlendirmesinden geçer
- CSS ile çözülebilen şey için JS kütüphanesi eklenmez
- Kullanılmayan kod ve ölü bağımlılık teslim edilmez

---

## 4. Doğrulama

Eşikler ölçülmeden "geçti" denmez. Lighthouse mobil profilde çalıştırılır; sonuç
raporlanır. Ölçüm yapılamadıysa bu açıkça belirtilir — varsayılmaz.
