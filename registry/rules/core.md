# Doveify — Core Rules

**Kaynak:** Doveify Web System · `@doveify/core-rules`
**Kapsam:** Bu dosyanın bulunduğu proje.

Bu dosya bir öneri listesi değildir. Bu projede çalışan her ajan ve geliştirici için
bağlayıcı kısıttır.

> **Kurulum notu:** Bu dosya otomatik okunmaz. Projenin `CLAUDE.md` (veya `AGENTS.md`)
> dosyasına şu satırı ekleyin:
> `Bu projede @.doveify/rules/core.md bağlayıcıdır.`

---

## 1. Otorite ve Öncelik Sırası

Çatışma durumunda üstteki alttakini ezer.

```
1. DESIGN-LOCK                    (bu projede varsa — en yüksek otorite)
2. Proje CLAUDE.md
3. Doveify rules (bu dosya ve kardeşleri)
4. Doveify patterns
5. external component/library defaults (shadcn, Magic UI, Aceternity, React Bits, ...)
6. implementation convenience      (en düşük — asla gerekçe değildir)
```

### 1.1 DESIGN-LOCK

Bu projede `DESIGN-LOCK` varsa, **en yüksek tasarım otoritesi odur.** Doveify rules,
Doveify patterns veya herhangi bir teknik kolaylık onu ezmez.

Teknik bir kısıt DESIGN-LOCK ile çatışıyorsa **tasarım kararını değiştirme.** Raporla:

```
DESIGN-LOCK CONFLICT: <hangi lock maddesi> ile <hangi teknik kısıt> çatışıyor.
Etki: <ne yapılamıyor>
Seçenekler: <lock'u bozmayan alternatifler, varsa>
```

Karar insana aittir. Sessizce uzlaşma üretme.

---

## 2. Estetik Kısıtları

Zevk meselesi değil; sistemin kimliğidir.

- **Generic AI landing page görünümünden kaçın.** Şablon hissi veren düzen üretme.
- **Composition ve typography birlikte ana belirleyicidir.** Biri diğerinin süsü değildir.
- **Negative space bilinçli kullanılmalı.** Boşluk artık alan değil, tasarım aracıdır.
- **Gereksiz card grid kullanma.** Kart ızgarası varsayılan çözüm değildir.
- **Gereksiz gradient / glow / glassmorphism kullanma.**
- **Hero'yu otomatik 50/50 split yapma.**
- **Pill button varsayılan değildir.**
- **Her section aynı max-width/padding ritmine sahip olmasın.** Ritim değişmeli.
- **Imagery dekor değil, kompozisyonun parçası olmalı.**
- **Hazır component görünümü olduğu gibi bırakılmamalı.**
- **Template hissi veren tekrarları engelle.**

---

## 3. Dış UI Kaynakları

shadcn/ui, Magic UI, Aceternity UI, React Bits, Motion Primitives ve diğer açık
kaynaklar **yalnızca kaynak havuzudur.**

**Hiçbiri varsayılan tasarım dili değildir.** Alınan her parça §2'ye göre yeniden
değerlendirilir ve dönüştürülür. Kaynağın varsayılan görünümü, spacing'i, radius'u,
motion'ı ve tipografisi olduğu gibi korunmaz.

Kurulduğu haliyle üretime giren hiçbir dış component kabul edilmez.

---

## 4. Sisteme Alma Değerlendirmesi

Tekrar kullanılacak bir component veya pattern kabul edilmeden **önce**:

```
VALUE:     Ne kazandırıyor? Hangi somut probleme çözüm?
USE CASE:  Nerede, kaç yerde, hangi bağlamda kullanılacak?
COST:      Bakım, bundle, karmaşıklık, öğrenme maliyeti nedir?
RISK:      Nerede kırılır? Neyi kilitler? Hangi bağımlılığı getirir?
DECISION:  CORE | OPTIONAL | EXPERIMENTAL | REJECT
```

- **CORE** — çekirdek, desteklenir, kararlıdır
- **OPTIONAL** — desteklenir ama her projede beklenmez
- **EXPERIMENTAL** — denemede, üretimde dikkatli kullanılır, kırılabilir
- **REJECT** — alınmaz, gerekçesi kayda geçer

Değerlendirme yapılmadan hiçbir şey sisteme girmez.

---

## 5. Kapsam

Kapsam dışına çıkan bir ihtiyaç görürsen **uygula değil, raporla.**

---

## İlgili kurallar

- `@doveify/motion-rules` — `.doveify/rules/motion.md`
- `@doveify/quality-gates` — `.doveify/rules/quality-gates.md`
- `@doveify/pattern-metadata` — `.doveify/rules/pattern-metadata.md`
