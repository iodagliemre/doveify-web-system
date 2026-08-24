# CLAUDE.md — Doveify Web System

Bu repo **Doveify Web System**'in ana üretim altyapısıdır.

Bu dosya, bu repo içinde çalışan her Claude oturumu için bağlayıcıdır. Buradaki
kurallar öneri değil, kısıttır.

---

## 0. Otorite ve Öncelik Sırası

Bir çatışma durumunda aşağıdaki sıra uygulanır. Üstteki her zaman alttakini ezer.

```
1. DESIGN-LOCK                        (müşteri projesinde varsa — en yüksek otorite)
2. CLAUDE.md                          (bu dosya)
3. Doveify project-level skills
4. frontend-design skill
5. external component/library defaults (shadcn, Magic UI, Aceternity, React Bits, ...)
6. implementation convenience          (en düşük — asla gerekçe değildir)
```

### 0.1 frontend-design skill'i hakkında

`frontend-design` skill'i kullanılabilir ve faydalıdır. Ancak **Doveify kuralları her
zaman daha yüksek önceliktedir.** Skill'in önerisi bu dosyadaki bir kuralla çelişirse
bu dosya kazanır. Skill bir varsayılan tasarım dili dayatmaz; yalnızca bir yaklaşım
kaynağıdır.

### 0.2 DESIGN-LOCK

Bir müşteri projesinde `DESIGN-LOCK` varsa, o proje için **en yüksek tasarım
otoritesi DESIGN-LOCK'tur.** CLAUDE.md dahil hiçbir şey onu ezmez.

**Teknik bir kısıt DESIGN-LOCK ile çatışıyorsa tasarım kararını değiştirme.**
Bunun yerine durumu raporla:

```
DESIGN-LOCK CONFLICT: <hangi lock maddesi> ile <hangi teknik kısıt> çatışıyor.
Etki: <ne yapılamıyor>
Seçenekler: <lock'u bozmayan alternatifler, varsa>
```

Karar müşteriye/insana aittir. Sessizce uzlaşma üretme.

---

## 1. Bu Sistem Nedir

Doveify Web System **tek bir site değildir.** Tekrar kullanılabilir çıktı üreten bir
sistemdir:

- **design rules** — tasarım kararlarının yazılı, uygulanabilir kuralları
- **components** — üretime hazır, taşınabilir arayüz parçaları
- **patterns** — kompozisyon ve etkileşim kalıpları
- **registries** — component/pattern kataloğu ve meta verisi
- **tooling** — üretim, doğrulama ve dağıtım araçları
- **QA rules** — kalite kapıları ve denetim kuralları

Her karar "bu tek sayfada işe yarıyor mu" değil, **"sistemde tekrar kullanılabilir mi"**
sorusuyla değerlendirilir.

---

## 2. Varsayılan Estetik Kuralları

Bunlar zevk meselesi değil, sistemin kimliğidir.

- **Generic AI landing page görünümünden kaçın.** Şablon hissi veren düzen üretme.
- **Composition, typography'dan önce gelmez.** İkisi birlikte ana belirleyicidir;
  biri diğerinin süsü değildir.
- **Negative space bilinçli kullanılmalı.** Boşluk artık alan değil, tasarım aracıdır.
- **Gereksiz card grid kullanma.** Kart ızgarası varsayılan çözüm değildir.
- **Gereksiz gradient / glow / glassmorphism kullanma.**
- **Hero'yu otomatik 50/50 split yapma.**
- **Pill button varsayılan değildir.**
- **Her section aynı max-width/padding ritmine sahip olmasın.** Ritim değişmeli.
- **Imagery dekor değil, kompozisyonun parçası olmalı.**
- **Hazır component görünümü olduğu gibi bırakılmamalı.** Kaynaktan alınan hiçbir
  şey kutudan çıktığı haliyle üretime girmez.
- **Template hissi veren tekrarları engelle.**

---

## 3. Motion

**Varsayılan teknik yaklaşım:** CSS `transform` / `opacity` / `clip-path` +
`IntersectionObserver`.

**Yasak:**

- Lenis yok
- scroll-jacking yok
- parallax yok
- sonsuz dekoratif animasyon yok

**Zorunlu:**

- `prefers-reduced-motion` desteği zorunludur. İstisnasız.

**Gerekçe kuralı:** Motion yalnızca şu üçünden birine hizmet ediyorsa kullanılır:

1. **yönlendirme** — kullanıcının dikkatini doğru yere taşır
2. **geçiş** — durum değişimini anlaşılır kılar
3. **marka karakteri** — kimliğin bilinçli bir ifadesidir

Bu üçüne hizmet etmeyen hareket kaldırılır.

---

## 4. Performans ve Erişilebilirlik

| Ölçüt | Eşik |
|---|---|
| Mobile Lighthouse | **>= 90** (hedef **95+**) |
| CLS | **≈ 0** |
| HTML | semantic |
| Accessibility | **WCAG AA** |
| JS / dependency | gereksiz olan yok |

Bir tasarım kararı bu eşikleri düşürüyorsa, önce kararı taşımanın başka bir yolunu
ara. DESIGN-LOCK kaynaklıysa bkz. §0.2 — kararı değiştirme, çatışmayı raporla.

---

## 5. External UI Kaynakları

Aşağıdakiler **yalnızca kaynak havuzudur:**

- shadcn/ui
- Magic UI
- Aceternity UI
- React Bits
- Motion Primitives
- diğer açık kaynaklar

**Hiçbiri varsayılan tasarım dili değildir.** Bunlardan alınan her parça Doveify
estetik kurallarına (§2) göre yeniden değerlendirilir ve dönüştürülür. Kaynağın
varsayılan görünümü, spacing'i, radius'u, motion'ı ve tipografisi olduğu gibi
korunmaz.

---

## 6. Sisteme Alma Değerlendirmesi

Bir component veya pattern sisteme alınmadan **önce** aşağıdaki değerlendirme yapılır:

```
VALUE:     Ne kazandırıyor? Hangi somut probleme çözüm?
USE CASE:  Nerede, kaç yerde, hangi bağlamda kullanılacak?
COST:      Bakım, bundle, karmaşıklık, öğrenme maliyeti nedir?
RISK:      Nerede kırılır? Neyi kilitler? Hangi bağımlılığı getirir?
DECISION:  CORE | OPTIONAL | EXPERIMENTAL | REJECT
```

**Karar anlamları:**

- **CORE** — sistemin çekirdeği, desteklenir, kararlıdır
- **OPTIONAL** — desteklenir ama her projede beklenmez
- **EXPERIMENTAL** — denemede, üretimde dikkatli kullanılır, kırılabilir
- **REJECT** — sisteme alınmaz, gerekçesi kayda geçer

Değerlendirme yapılmadan hiçbir şey sisteme girmez.

---

## 7. Çalışma Sınırları

- **Bu repo içinde çalışırken mevcut müşteri projelerine dokunma.**
- Bu repo, üst dizinlerdeki veya diğer sürücülerdeki repo'lardan bağımsızdır;
  onların git yapısına, branch'lerine veya dosyalarına müdahale edilmez.
- Kapsam dışına çıkan bir ihtiyaç görürsen uygula değil, **raporla.**

---

## 8. Mevcut Durum

Bu repo şu an **kurulum aşamasındadır.**

Henüz component veya uygulama kodu oluşturulmayacaktır. Sıradaki adımlar açıkça
istenmeden başlatılmaz.
