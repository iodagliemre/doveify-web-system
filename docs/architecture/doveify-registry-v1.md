# Doveify Registry v1 — Mimari

**STATUS:** ACTIVE
**Tarih:** 2026-08-24
**Dayanak:** [ADR-001 — Registry Architecture](../decisions/001-registry-architecture.md)
**Araştırma:** [shadcn Registry — Teknik Araştırma](../research/shadcn-registry-research.md)
**Doğrulandı:** `shadcn@4.19.0 registry validate` — temiz geçti (4 registry dosyası, 5 item)
**Dağıtım kanıtı:** GitHub source registry üzerinden uçtan uca **PASS** — bkz. §8.5

---

## 1. Kapsam

Bu doküman, ADR-001'de yönü sabitlenen kararın **v1 uygulamasını** tanımlar:
Doveify Web System'in kendi shadcn-compatible source registry omurgası.

**Bu sürümde yapılan:** klasör düzeni, kök `registry.json`, `include` kompozisyonu,
rule item'ları, pattern metadata standardı, namespace bağlama kararı.

**Bu sürümde bilinçli olarak yapılmayan:** UI component, CSS, framework kurulumu,
`npm install`, pattern içeriği, DESIGN-LOCK, `shadcn build` çıktısı, dynamic registry.

---

## 2. İçerik Sınıfları

Registry üç içerik sınıfı taşır. Sınıf ayrımı otorite ayrımıdır, klasör ayrımı değil.

| Sınıf | Ne taşır | Biçim | Otorite |
|---|---|---|---|
| **RULE** | Üretim kısıtları | markdown | **Bağlayıcı.** Uyulmaması bir hatadır. |
| **PATTERN** | Kompozisyon/UX tarifi | markdown + `meta` | **Öneri.** DESIGN-LOCK ve proje kararı üstündedir. |
| **COMPONENT** | Gerçek reusable kod | kaynak dosya | **Araç.** Görünüm dayatmaz. |

**RULE ≠ PATTERN ayrımı kritiktir.** Bir RULE "şunu yapma" der. Bir PATTERN "şu
problemi şu koşullarda şöyle düşün" der. Bir tarif kısıta terfi edemez; bir kısıt
tarife indirgenemez.

v1'de **yalnızca RULE sınıfında item vardır.** PATTERN ve COMPONENT chunk'ları boş
ama şemaya uygun olarak mevcuttur — kompozisyon iskeleti hazır, içerik kanıtla gelir
(ADR-001 §5).

---

## 3. Klasör Yapısı

```
registry.json                          kök katalog — yalnızca include
registry/
  rules/
    registry.json                      RULE chunk (5 item)
    core.md
    motion.md
    quality-gates.md
    pattern-metadata.md
  patterns/
    registry.json                      PATTERN chunk (boş)
    <category>/<name>.md               ilk pattern ile oluşur
  components/
    registry.json                      COMPONENT chunk (boş)
docs/
  architecture/  decisions/  research/
experiments/                           registry dışı; dağıtılmaz
```

### 3.1 `rules/` kök seviyeden `registry/` altına taşındı

İskelette `rules/` repo kökündeydi. İki ayrı kural konumu (`rules/` ve
`registry/rules/`) belirsizlik üretir: hangisi dağıtılır, hangisi repo-içi?

ADR-001'e göre registry **taşıma katmanıdır** ve kurallar taşınacak varlıklardır.
Repo-içi kurallar zaten `CLAUDE.md`'de. Dolayısıyla tek doğru konum
`registry/rules/`'dır. Klasör boştu; taşıma içerik kaybı üretmedi.

`registry/*/.gitkeep` dosyaları kaldırıldı — dizinlerde artık izlenen `registry.json`
var.

---

## 4. Kompozisyon: `include`

Kök `registry.json` **hiçbir item tanımlamaz.** Yalnızca metadata taşır ve chunk'ları
birleştirir:

```json
{
  "$schema": "https://ui.shadcn.com/schema/registry.json",
  "name": "doveify",
  "homepage": "https://github.com/iodagliemre/doveify-web-system",
  "include": [
    "registry/rules/registry.json",
    "registry/patterns/registry.json",
    "registry/components/registry.json"
  ]
}
```

**Neden:** yeni bir rule veya pattern eklemek kök dosyaya dokunmaz. Üç içerik sınıfı
bağımsız evrilir, merge çakışması yüzeyi küçük kalır.

**Doğrulanan davranışlar (CLI 4.19.0):**

- `include` yolları **kök `registry.json`'a görecelidir**
- Chunk dosyaları `name` / `homepage` alanlarını atlayabilir
- **Boş `items: []` geçerlidir** — bu, dolmamış chunk'ları önceden bağlamayı mümkün kılar

### 4.1 Düzeltme — `files[].path` göreceliği

Araştırma dokümanı (§3) `include` edilen dosyalardaki yolların kökten korunduğunu
belirtiyordu. **CLI 4.19.0 bunu doğrulamıyor.**

`registry/rules/registry.json` içinde `"path": "registry/rules/core.md"` yazıldığında
doğrulama şu hatayı verdi:

```
File "registry/rules/core.md" was not found or could not be read.
Make sure the file path is relative to the registry.json file that declares the item.
```

**Geçerli kural:** `files[].path`, **item'ı bildiren chunk dosyasına görecelidir.**
Doğru değer `"core.md"`'dir. `target` ise her zaman tüketici proje köküne (`~`) göredir.

Bu, ADR-001'in "spesifikasyon hareket halinde" riskinin somut bir örneğidir ve
CLI sürümünü sabitleme kararını destekler.

---

## 5. Item Konvansiyonları

| Konu | Kural |
|---|---|
| Adlandırma | kebab-case, registry genelinde tekil, `doveify-` öneki yok (namespace zaten ayırır) |
| Tip | Kod-dışı varlıklar `registry:item` + `files[].type: registry:file` |
| `target` | RULE → `~/.doveify/rules/<ad>.md` · PATTERN → `~/.doveify/patterns/<ad>.md` |
| `categories` | Arama için; ilk eleman sınıf (`rule` / `pattern`), sonrası alan |
| `meta.doveify` | Doveify'a özgü makine okunabilir metadata; `kind`, `version`, `tier` zorunlu |
| `registryDependencies` | **v1'de kullanılmıyor** — bkz. §5.1 |

### 5.1 `registryDependencies` neden yok

Şema, aynı registry içindeki kardeş item'a bare-name ile referans vermeyi
`@shadcn` çözümlemesinden ayırt edilebilir biçimde tanımlamıyor. Source registry
bağlamında bu davranış doğrulanmadı.

Bunun yerine kompozisyon **bundle item** ile yapıldı: `doveify-rules` dört kural
dosyasının tamamını tek komutla kurar. Doğrulanmamış bir spec davranışına bağımlılık
üretmeden aynı ergonomiyi verir.

### 5.2 `~/.doveify/` hedef dizini neden

Alternatif `~/AGENTS.md` veya `~/CLAUDE.md` hedeflemekti — otomatik okunurdu, ama
tüketici projenin mevcut dosyasını **ezerdi.** Kabul edilemez.

`~/.doveify/` ayrı bir ad alanıdır, hiçbir şeyi ezmez ve "bu dosyalar dışarıdan
gelir, elle düzenlenmez" sinyalini taşır. Bedeli: otomatik okunmaz. `core.md` bu
bedeli, projenin `CLAUDE.md`'sine eklenecek tek satırlık referansla kapatır.

---

## 6. Registry Item'ları (v1)

Tümü `registry/rules/registry.json` içinde, ADR-001 §5 uyarınca dar tutuldu.

| Item | Tier | Ne taşır |
|---|---|---|
| `core-rules` | CORE | Otorite sırası, DESIGN-LOCK davranışı, estetik kısıtları, dış kaynak politikası, sisteme alma değerlendirmesi |
| `motion-rules` | CORE | Motion gerekçe kuralı, yasaklar, istisnasız `prefers-reduced-motion` |
| `quality-gates` | CORE | Lighthouse / CLS / WCAG AA eşikleri, çatışma davranışı, denetim kalemleri |
| `pattern-metadata` | CORE | PATTERN sözleşmesi, kategori taksonomisi, 11 zorunlu alan, kabul kapısı |
| `doveify-rules` | CORE | Yukarıdaki dördünü tek komutla kuran bundle |

### 6.1 Intake değerlendirmesi (CLAUDE.md §6)

**Dördü ayrı item olarak, neden tek dosya değil:**

```
VALUE:     Her biri farklı bir uygulama anında okunur — core (tasarım kararı),
           motion (animasyon yazarken), quality-gates (CI/teslim), pattern-metadata
           (pattern yazarken). Ayrı dosyalar ilgili anda ilgili kısıtı yükler.
USE CASE:  core her projede; motion statik projelerde gereksiz; quality-gates CI'da
           tek başına referans verilir; pattern-metadata yalnızca pattern yazarken.
COST:      Dört küçük markdown + bir bundle item. Bakım maliyeti düşük.
RISK:      Parçalanma — kurallar birbirine referans verirken kayabilir. Her dosyanın
           başındaki "Üstünde: core.md §1 geçerlidir" satırı bunu bağlar.
DECISION:  CORE
```

**`doveify-rules` bundle, neden:**

```
VALUE:     En yaygın kurulum ("bana Doveify kurallarını ver") tek komuta iner;
           kısmi kurulumdan doğan eksik-kural riski azalır.
USE CASE:  Yeni müşteri projesi kurulumu — varsayılan giriş noktası.
COST:      Dosya listesi iki yerde tekrarlanıyor; yeni rule eklenince bundle
           güncellenmeli. Kabul edildi.
RISK:      Bundle ile tekil item'ların ayrışması. §7'deki validate kapısı dosya
           varlığını denetler, kapsam eşitliğini denetlemez — gözle takip gerekir.
DECISION:  CORE
```

---

## 7. Doğrulama

```bash
npx shadcn@4.19.0 registry validate
```

**Sonuç (2026-08-24):**

```
Registry is valid.
Checked 4 registry files and 5 items.
```

CLI sürümü `.mcp.json` ile aynı — **4.19.0'a sabitli.** ADR-001'deki "spec hareket
halinde" riskine karşı bilinçli tercih; §4.1 bu riskin gerçekleştiğini gösteriyor.

**Doğrulamanın kapsamı:** şema geçerliliği, yinelenen item adı, include kuralları,
yerel dosya yollarının varlığı. Build gerektirmez.

**Doğrulamanın kapsamadığı:** CLAUDE.md §2–§4 estetik, motion ve performans kuralları.
Bunlar için ayrı bir QA katmanı gerekir (ADR-001 "Doğrulama boşluğu").

---

## 8. `@doveify` Namespace Bağlama

Bu bölüm, ADR-001'in registry seçimini tüketici tarafında somutlaştırır. Üç yol
birbirinden ayrı tutulur; **karıştırılmaları v1'in en olası hata kaynağıdır.**

### 8.1 Yerel geliştirme (şu anki aşama)

**Namespace kullanılmaz.**

Bu repo registry'nin **yazım yüzeyidir**, tüketicisi değil. Kendi kendini tüketmesi
anlamsızdır. Doğrulama yerinde yapılır (§7); dağıtım bu repodan değil, ayrı bir
tüketici projeden denenir (§8.5).

Bu nedenle **bu reponun `components.json` dosyasına `@doveify` eklenmedi.** Oradaki
`@magicui` / `@motion-primitives` / `@aceternity` girdileri araştırma ve
`experiments/` için kaynak havuzudur (CLAUDE.md §5) ve **değiştirilmedi.**

Tüketici tarafında yerel bir kopyayı test etmek (`file:` veya göreli yol ile
namespace) CLI 4.19.0'da **doğrulanmadı** — bkz. §10 açık madde.

### 8.2 GitHub source registry (aktif dağıtım yolu — doğrulandı)

**Namespace girdisi gerekmez. Build gerekmez. Token gerekmez.** Repo kökündeki
`registry.json` doğrudan giriş noktasıdır.

```bash
npx shadcn@4.19.0 add iodagliemre/doveify-web-system/doveify-rules#<40-karakter-sha>
```

- Adres biçimi: `owner/repo/item-name`
- `#ref` — branch, tag veya tam commit SHA. **Yayımlanan komutlarda tam SHA kullanılır**
  (tekrarlanabilirlik; araştırma §5).
- Private repo aynı mekanizma + `GH_TOKEN` (Contents: Read-only).
- Kurulum öncesi `--dry-run` ile önizleme.

**Ön koşul karşılandı.** `origin` tanımlı
(`https://github.com/iodagliemre/doveify-web-system.git`), `main` push edildi ve repo
**public**. Bu yol §8.5'te uçtan uca doğrulandı.

### 8.3 Namespace kısayolu (v1 kapsamı dışında)

`components.json` → `registries` değerleri `{name}` yer tutuculu **URL şablonlarıdır.**
Source registry'de item başına bir URL yoktur; bu yüzden `@doveify` namespace girdisi
şunlardan birini gerektirir:

- `shadcn build` çıktısının sabit bir yola (`r/{name}.json`) commit edilmesi ve
  `"@doveify": "https://raw.githubusercontent.com/<owner>/<repo>/<ref>/r/{name}.json"`
- veya dynamic registry / server — **ADR-001 §6 ile v1 kapsamı dışında**

Yani §8.2 v1'in gerçek dağıtım yoludur; §8.3 ergonomi iyileştirmesidir ve kanıt
gerektirir. `@doveify` adı bu amaçla **rezerve edilmiştir**; başka bir şeye
bağlanmayacaktır.

### 8.4 Özet

| Yol | Namespace | Build | Durum |
|---|---|---|---|
| Yerel geliştirme | yok | yok | **Aktif** — `registry validate` ile doğrulandı |
| GitHub source registry | yok (`owner/repo/item#ref`) | yok | **Aktif** — §8.5'te uçtan uca doğrulandı |
| `@doveify` namespace kısayolu | var | gerekli | **Kapsam dışı** (ADR-001 §6) |

### 8.5 Distribution proof

GitHub source registry yolu, ana repo dışında ayrı bir throwaway tüketici projede
(`D:\doveify-registry-consumer-test`) uçtan uca test edildi.

**Test edilen SHA:** `fe08b53aa3736ddea419585e08f85abcdf7e566a`

```bash
npx shadcn@4.19.0 view 'iodagliemre/doveify-web-system/doveify-rules#fe08b53aa3736ddea419585e08f85abcdf7e566a'
npx shadcn@4.19.0 add  'iodagliemre/doveify-web-system/doveify-rules#fe08b53aa3736ddea419585e08f85abcdf7e566a' --yes
```

**Sonuç:**

```
PUBLIC ACCESS:               PASS
SOURCE REGISTRY RESOLUTION:  PASS
DOVEIFY-RULES RESOLUTION:    PASS
TARGET PLACEMENT:            PASS
DISTRIBUTION PROOF:          PASS
```

**Kanıtlananlar:**

- Repo anonim erişilebilir (`private: false`, `visibility: public`); kök `registry.json`,
  üç chunk ve dört rule markdown'ı SHA-pinned adresten **HTTP 200**
- `include` zinciri çözüldü; `doveify-rules` bundle'ı bulundu ve dört dosya içerikle
  gömülü döndü
- Kurulum dört dosyayı **doğru hedeflere** yerleştirdi:
  `~/.doveify/rules/{core,motion,quality-gates,pattern-metadata}.md`
- Kurulan dosyalar GitHub raw içeriğiyle karşılaştırıldı: **SHA-256 4/4 identical**

**Testin geçerliliği:** önceki turun yerel artifact'leri (`.doveify/` ve yerel build
çıktısı `r/`) test öncesi kenara alındı; kurulum yerel bir JSON kaynağından gelemezdi.
Ana repoya hiçbir test artifact'i yazılmadı.

Bu, ADR-001'in dağıtım kararını doğrular: **sunucusuz, build'siz, token'sız, sürüm
sabitlemeli.**

---

## 9. Pattern Metadata Standardı

Kanonik metin: [`registry/rules/pattern-metadata.md`](../../registry/rules/pattern-metadata.md)

**Kategori taksonomisi (v1, kapalı liste):** `hero` · `navigation` · `section` ·
`typography` · `imagery`

**11 zorunlu alan:** NAME · CATEGORY · PROBLEM · USE WHEN · AVOID WHEN ·
DESIGN-LOCK INPUTS · RESPONSIVE PRINCIPLE · ACCESSIBILITY NOTES · PERFORMANCE NOTES ·
TEMPLATE RISK · CUSTOMIZATION AXES

**Standardın taşıdığı asıl yük:** bir pattern'in şablona dönüşmesini engellemek.
`hero` bir pattern **kategorisidir**, "sol başlık + sağ görsel + CTA" bir **şablondur**
ve reddedilir. TEMPLATE RISK alanı zorunludur ve CLAUDE.md §2'deki en az bir maddeye
bağlanmalıdır.

Metadata iki yerde yaşar: markdown dosyası **kanonik metindir**, registry item'ının
`meta.doveify` bloğu onun **makine okunabilir özetidir.** Çelişki bir hatadır.

---

## 10. Açık Maddeler

| # | Madde | Etki |
|---|---|---|
| 1 | ~~Git remote tanımlı değil; `homepage` bir varsayım.~~ **KAPANDI.** `origin` tanımlı, `main` push edildi, repo public. `homepage` ve item `author` alanları gerçek repo ile doğrulandı — değişiklik gerekmedi. | — |
| 2 | Tüketici tarafında **yerel yol ile namespace** desteği CLI 4.19.0'da doğrulanmadı. | Dağıtım yolu §8.5 ile kanıtlandığı için engelleyici değil; yalnızca yerel iterasyon ergonomisini etkiler. |
| 3 | `registryDependencies` ile kardeş item referansı doğrulanmadı (§5.1). | Kompozisyon şimdilik bundle ile yapılıyor. |
| 4 | `registry validate` estetik/motion/performans kurallarını denetlemiyor. | Ayrı QA katmanı gerekiyor (ADR-001). |
| 5 | Bundle ile tekil item'ların kapsam eşitliği otomatik denetlenmiyor. | Yeni rule eklenince `doveify-rules` elle güncellenmeli. |

---

## 11. Değişmeyenler

- `CLAUDE.md` — değiştirilmedi
- `components.json` — dış registry tanımları değiştirilmedi
- `ADR-001` — bu doküman onu uygular, ezmez
- `docs/research/shadcn-registry-research.md` — §4.1'deki düzeltme bu dokümanda kayıtlı;
  araştırma dosyası tarihsel kayıt olarak korundu
