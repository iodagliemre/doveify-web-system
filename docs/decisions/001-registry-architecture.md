# ADR-001 — Registry Architecture

**STATUS:** ACCEPTED
**Tarih:** 2026-08-24
**Araştırma:** [docs/research/shadcn-registry-research.md](../research/shadcn-registry-research.md)

---

## CONTEXT

Doveify Web System bir Next.js uygulaması değildir. Reusable **rules, components, patterns, skills, conventions ve tooling** dağıtan bir source registry'dir.

Bu varlıkların müşteri projelerine sürümlenebilir, tekrarlanabilir ve denetlenebilir biçimde taşınması gerekiyor. Kendi dağıtım formatımızı icat etmek ile mevcut bir standardı benimsemek arasında karar verilmesi gerekti.

---

## DECISION

**Doveify Web System v1, shadcn-compatible source registry standardını dağıtım omurgası olarak kullanacaktır.**

Belirleyici teknik gerekçeler:

- **Sunucu gerekmiyor.** GitHub reposunun kendisi source registry olur; `registry.json` repo kökünde yeterlidir.
- **Kod-dışı dağıtım birinci sınıf.** `registry:file` + `target` ile markdown kurallar, conventions ve tooling dosyaları component'e sarılmadan dağıtılır.
- **Sürüm sabitleme yerleşik.** `#ref` ile tag veya tam commit SHA'ya sabitleme.
- **Kompozisyon ve doğrulama hazır.** `include` ile parçalı yapı, `registry validate` ile build gerektirmeyen doğrulama kapısı.
- **Public ve private aynı mekanizma.** Müşteri varlıkları private repo + `GH_TOKEN` ile aynı standartla taşınır.

---

## SCOPE AND CONSTRAINTS

Bu karar, aşağıdaki sınırlarla birlikte geçerlidir. Bunlar kararın ayrılmaz parçasıdır.

### 1. shadcn/ui Doveify'ın tasarım dili değildir

Benimsenen şey **dağıtım standardıdır**, tasarım sistemi değil. shadcn/ui'nin görsel varsayılanları — spacing, radius, tipografi, motion, component görünümü — Doveify'ın estetiğini tanımlamaz. Bu ayrım CLAUDE.md §0 ve §5 ile bağlayıcıdır.

### 2. Registry standardı altyapıdır

Registry bir **taşıma katmanıdır.** Ne dağıtılacağına, neyin sisteme gireceğine veya nasıl görüneceğine karar vermez. O kararlar CLAUDE.md §2–§4 ve §6'daki değerlendirme sürecine (VALUE / USE CASE / COST / RISK / DECISION) aittir.

### 3. Dış registry'ler kaynak havuzudur

shadcn/ui, Magic UI, Aceternity UI, React Bits, Motion Primitives ve diğer açık kaynaklar **yalnızca kaynak havuzudur.** Hiçbiri varsayılan tasarım dili değildir. Namespace ayrımı (`@doveify` ile `@shadcn`, `@v0` vb.) bu sınırı teknik düzeyde de görünür kılar; dışarıdan alınan her parça CLAUDE.md §2'ye göre yeniden değerlendirilir ve dönüştürülür.

### 4. DESIGN-LOCK her zaman registry seçiminden üstündür

Bir müşteri projesinde `DESIGN-LOCK` varsa, registry'nin sunduğu hiçbir öğe, varsayılan veya kolaylık onu ezmez. Çatışma durumunda tasarım kararı değiştirilmez; `DESIGN-LOCK CONFLICT:` olarak raporlanır (CLAUDE.md §0.2).

### 5. Başlangıçta küçük static source registry kullanılacaktır

v1, repo içinde tutulan **static source registry** ile başlar. Küçük ve dar kapsamlı; her öğe §6 değerlendirmesinden geçerek girer. Genişleme kanıtla olur, öngörüyle değil.

### 6. Dynamic registry / server v1 kapsamında değildir

İstek anında sunum (`loadRegistry()`, `loadRegistryItem()`), authentication sunucusu ve dinamik arama v1 kapsamı dışındadır. Static source registry mevcut ihtiyacı karşılıyor; çalışan bir servis işletme maliyeti bu aşamada gerekçesizdir.

---

## CONSEQUENCES

**Olumlu**

- Dağıtım altyapısı için sıfır sunucu, sıfır işletme maliyeti
- Müşteri projelerinde sürüm sabitlemeli, tekrarlanabilir kurulum
- `registry validate` doğrudan bir QA kapısı olarak CI'a takılabilir
- Claude Code, MCP üzerinden registry'yi keşfedebilir

**Kabul edilen maliyetler**

- **GitHub'a bağımlılık.** GitHub Enterprise desteklenmiyor; adresler `github.com`'a çözülür.
- **5 MiB dosya sınırı.** Büyük medya varlıkları registry dışında taşınmalı.
- **Symlink kullanılmayacak.** Anonim ve kimlik doğrulamalı okuma farklı davranıyor.
- **Spesifikasyon hareket halinde.** `include` ve `validate` Mayıs 2026'da geldi; CLI sürümü sabitlenerek kırılma riski sınırlanmalı.
- **Kültürel risk.** Standardın benimsenmesi "shadcn/ui bizim tasarım dilimiz" algısına kayabilir. Yukarıdaki §1–§3 bu kaymayı engellemek için yazılmıştır.
- **Doğrulama boşluğu.** `registry validate` yalnızca şema ve yol doğruluğunu denetler; CLAUDE.md §2–§4 kurallarını denetlemez. Ayrı bir QA katmanı gerekecektir.

---

## OUT OF SCOPE (bu karar kapsamında yapılmadı)

`shadcn init` çalıştırılmadı · `package.json` oluşturulmadı · `npm install` yapılmadı · MCP kurulmadı · `registry.json` oluşturulmadı · component oluşturulmadı.

Bu karar yalnızca yönü sabitler. Uygulama ayrı ve açık bir talep gerektirir.
