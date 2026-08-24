# shadcn Registry — Teknik Araştırma

**Tarih:** 2026-08-24
**Amaç:** Doveify Web System'in dağıtım omurgası olarak shadcn Registry standardının uygunluğunu teknik olarak değerlendirmek.
**Kapsam:** Yalnızca resmi shadcn/ui kaynakları. Üçüncü taraf blog, tutorial veya topluluk içeriği kullanılmadı.

> **Değerlendirme varsayımı:** Doveify Web System bir Next.js uygulaması değildir. Reusable rules, components, patterns, skills, conventions ve tooling dağıtan bir **source registry**'dir.

---

## 1. Güncel Yetenekler (Özet)

| Yetenek | Durum | Doveify için önem |
|---|---|---|
| `registry.json` şeması | Kararlı | Yüksek — katalog formatı |
| GitHub Registry (server'sız) | Kararlı | **Kritik** — sunucu gerekmez |
| `include` ile kompozisyon | Mayıs 2026 | Yüksek — dosya bölme |
| `registry validate` | Mayıs 2026 | Yüksek — QA kapısı |
| Namespaced / multiple registries | Kararlı | Yüksek — çok kaynak yönetimi |
| MCP server | Kararlı | Orta — ajan keşfi |
| `registry:file` + `target` | Kararlı | **Kritik** — kod-dışı dosya dağıtımı |
| Private repo + token auth | Kararlı | Yüksek — müşteri gizliliği |
| Dynamic registry / server | Kararlı | Düşük — v1'de gereksiz |

Sürüm bağlamı: CLI v4 (Mart 2026), Registry Include & Validate (Mayıs 2026).

---

## 2. `registry.json`

Registry'nin katalog dosyası. Şema: `https://ui.shadcn.com/schema/registry.json`

**Kök seviyesi alanlar:**

- `$schema` — doğrulama şeması
- `name` — registry kimliği (data attribute ve metadata'da kullanılır)
- `homepage` — registry web adresi
- `include` — başka `registry.json` dosyalarından kompozisyon
- `items` — registry öğeleri dizisi

Kök `registry.json` **en az `items` veya `include`'dan birini** içermelidir.

**Öğe (item) alanları:** `name`, `type`, `title`, `description`, `author`, `files[]` (`path`, `type`, `target`), `dependencies`, `devDependencies`, `registryDependencies`.

### 2.1 Öğe tipleri

| Tip | Amaç |
|---|---|
| `registry:base` | Bütün tasarım sistemleri |
| `registry:block` | Çok dosyalı karmaşık componentler |
| `registry:component` | Basit componentler |
| `registry:ui` | UI primitive / tek dosyalık component |
| `registry:lib` | Yardımcı kütüphaneler |
| `registry:hook` | React hook'ları |
| `registry:page` | Sayfa / route dosyaları |
| `registry:font` | Font tanımları |
| `registry:style` | Registry style'ları |
| `registry:theme` | Tema tanımları |
| `registry:file` | **Rastgele dosyalar** |
| `registry:item` | Evrensel öğe |

### 2.2 Doveify için kritik bulgu: `registry:file` + `target`

Registry standardı **React componentiyle sınırlı değildir.** `registry:file` tipi herhangi bir dosyayı dağıtabilir; `target` alanı hedef konumu belirler.

Resmi dokümandaki örnek:

```json
{
  "files": [
    {
      "path": "registry/config/rules.md",
      "type": "registry:file",
      "target": "~/RULES.md"
    }
  ]
}
```

GitHub Registry dokümanındaki resmi örnek doğrudan bir markdown kural dosyası dağıtır:

```json
{
  "$schema": "https://ui.shadcn.com/schema/registry.json",
  "name": "acme-toolkit",
  "items": [
    {
      "name": "project-conventions",
      "type": "registry:item",
      "files": [
        { "path": "AGENTS.md", "type": "registry:file", "target": "~/AGENTS.md" }
      ]
    }
  ]
}
```

**Sonuç:** Doveify'ın rules, conventions, QA rules, skills ve tooling dosyaları component olmadan, olduğu gibi dağıtılabilir. Bu, "Doveify bir Next.js uygulaması değildir" varsayımıyla tam uyumludur.

**Target yer tutucuları:** `@components/`, `@ui/`, `@lib/`, `@hooks/`, `~` (proje kökü). `registry:page` ve `registry:file` için `target` **zorunludur.**

---

## 3. Registry Include

Mayıs 2026'da eklendi. Büyük bir registry'yi tek dev `registry.json` dosyasında tutmak yerine parçalara bölmeyi sağlar.

```json
{
  "$schema": "https://ui.shadcn.com/schema/registry.json",
  "name": "acme",
  "homepage": "https://acme.com",
  "include": [
    "components/ui/registry.json",
    "hooks/registry.json"
  ]
}
```

**Kurallar:**

- Include edilen yollar **kök `registry.json`'a görecelidir.**
- Include edilen dosyalar `name` ve `homepage` alanlarını atlayabilir; yalnızca kök dosya metadata gerektirir.
- `shadcn build` sırasında hepsi tek kataloğa düzleştirilir.
- Dosya yolları kökten korunur: `components/ui/registry.json` içinde tanımlı bir dosya çıktıda `components/ui/button.tsx` olarak yazılır.

**Doveify için:** `registry/components/` ve `registry/patterns/` ayrı `registry.json` dosyalarıyla yönetilebilir; kökteki dosya yalnızca bunları birleştirir. Mevcut iskelet yapımızla doğrudan örtüşür.

---

## 4. Registry Validate

Mayıs 2026'da eklendi.

```bash
pnpm dlx shadcn registry validate
```

**Kontrol ettikleri:** kök `registry.json`, include edilen registry dosyaları, öğe şema hataları, yinelenen öğe adları, include kuralları, yerel öğe dosya yolları.

**Önemli özellikler:**

- Doğrulama **kaynak dosyalar üzerinde** çalışır — önce `shadcn build` çalıştırmak gerekmez.
- Bulabildiği tüm hataları **tek çalıştırmada** raporlar.

**Doveify için:** CLAUDE.md §4'teki QA rules yaklaşımına doğrudan bağlanabilecek, hazır bir doğrulama kapısı. CI'da çalıştırılabilir.

---

## 5. GitHub Registry Çalışma Şekli

Doveify açısından en belirleyici bulgu.

> "You do not need to set up a registry server or publish generated JSON files. The GitHub repository becomes the source registry."
> — resmi GitHub Registries dokümanı

**Gereksinimler:**

1. Repo **kökünde** bir `registry.json` bulunmalı
2. Geçerli `registry.json` ve `registry-item.json` şemaları kullanılmalı
3. Referans verilen kaynak dosyalar repoda gerçekten var olmalı

**Adresleme formatı:** `owner/repo/item-name`

```bash
pnpm dlx shadcn@latest add acme/toolkit/project-conventions
```

İlk iki segment owner ve repo'yu, kalanı öğe adını belirtir. `.json` ile biten bir adres dosya yolu olarak yorumlanır.

**Sürüm sabitleme (`#ref`):** branch, tag veya commit SHA desteklenir.

```bash
pnpm dlx shadcn@latest add acme/toolkit/project-conventions#v1.0.0
pnpm dlx shadcn@latest add acme/toolkit/project-conventions#c0ffee254729296a45d6691db565cf707a3fef5d
```

`#ref` verilmezse CLI reponun varsayılan branch'ini kullanır. Resmi doküman, yayımlanan kurulum komutlarında **sabitlenmiş ref** kullanmayı öneriyor; en tekrarlanabilir seçenek 40 karakterlik tam commit SHA.

**Kısıtlar:**

- Kaynak dosya başına **5 MiB** sınırı
- **GitHub Enterprise desteklenmiyor** — adresler her zaman `github.com`'a çözülür
- **Symlink'lerden kaçınılmalı** — anonim okuma symlink hedef yolunu metin olarak döndürürken, kimlik doğrulamalı okuma Contents API üzerinden hedef dosyanın içeriğini döndürür (tutarsız davranış)

**Güvenlik önerisi:** Kurulumdan önce repoyu ve kök `registry.json`'ı incele; `shadcn add --dry-run` ile önizleme yap.

---

## 6. Public / Private Repo Farkı

| | Public repo | Private repo |
|---|---|---|
| Erişim | Anonim | Kimlik doğrulamalı |
| Yerel kullanım | Ek adım yok | `gh auth login` (bir kez) |
| CI / otomasyon | Ek adım yok | `GH_TOKEN` veya `GITHUB_TOKEN` env |
| Token kapsamı | — | Fine-grained PAT, **Contents: Read-only** |
| Symlink davranışı | Hedef yolu metin döner | Hedef dosya içeriği döner |

**Çözümleme sırası:** CLI **önce anonim erişim dener.** Yalnızca reponun kök `registry.json` dosyası herkese açık okunabilir değilse kimlik bilgilerini kullanır.

```bash
GH_TOKEN=github_pat_xxx npx shadcn@latest add acme/private-toolkit/project-conventions
```

`GH_TOKEN`, `GITHUB_TOKEN`'a göre önceliklidir.

**Doveify için:** Genel sistem public, müşteriye özel varlıklar private repo'da tutulabilir. Aynı standart, iki gizlilik seviyesinde de değişmeden çalışır.

---

## 7. Multiple / Namespaced Registries

Birden fazla registry `components.json` içinde `registries` altında, `@` önekli namespace ile tanımlanır.

**Basit biçim:**

```json
{
  "registries": {
    "@v0": "https://v0.dev/chat/b/{name}",
    "@acme": "https://registry.acme.com/resources/{name}.json",
    "@lib": "https://lib.company.com/utilities/{name}"
  }
}
```

**Kimlik doğrulamalı biçim:**

```json
{
  "registries": {
    "@private": {
      "url": "https://api.company.com/registry/{name}.json",
      "headers": {
        "Authorization": "Bearer ${REGISTRY_TOKEN}",
        "X-API-Key": "${API_KEY}"
      },
      "params": { "version": "latest" }
    }
  }
}
```

**Mekanik:**

- Kurulum deseni: `@namespace/resource-name` (ör. `@shadcn/button`, `@acme/auth-utils`)
- Namespace `@` ile başlar; yalnızca alfanümerik, tire ve alt çizgi içerir
- `{name}` yer tutucusu kurulum sırasında öğe adıyla değiştirilir; opsiyonel `{style}` yer tutucusu da var
- `${VAR_NAME}` biçimindeki değişkenler `process.env`'den genişletilir (ör. `.env.local`)

**Çapraz registry bağımlılıkları:**

```json
{
  "name": "dashboard",
  "type": "registry:block",
  "registryDependencies": [
    "@shadcn/card",
    "@v0/chart",
    "@acme/data-table",
    "@lib/data-fetcher"
  ]
}
```

Çözümleyici bağımlılıkları kendi registry'lerinden **özyinelemeli** olarak çözer ve doğru kurulum sırası için **topolojik sıralama** uygular.

**Yönetişim:** Merkezî bir otorite yoktur; sistem merkeziyetsizdir. `@shadcn` varsayılan namespace'tir, ancak istenen herhangi bir namespace oluşturulabilir.

**Doveify için:** `@doveify` namespace'i tanımlanabilir. Dış kaynaklar (`@shadcn`, `@v0` vb.) ayrı namespace'lerde kalır — CLAUDE.md §5'teki "kaynak havuzu, tasarım dili değil" ayrımı namespace düzeyinde teknik olarak da uygulanır.

---

## 8. MCP Server ve Claude Code Entegrasyonu

**MCP'nin rolü:** AI asistanlarının registry öğelerini keşfetmesini, anlamasını ve kullanmasını sağlar. Server, registry index'ini talep ederek çalışır — bu yüzden registry kökünde şemaya uygun bir `registry.json` bulunmalıdır.

**Kurulum:**

```bash
pnpm dlx shadcn@latest mcp init --client claude
```

**Desteklenen istemciler:** Claude Code, Cursor, VS Code, Codex, Open Code.

**Registry tanımı `components.json` içinde:**

```json
{
  "registries": {
    "@acme": "https://acme.com/r/{name}.json"
  }
}
```

**Claude Code'da kullanım:** Kurulumdan sonra doğal dille sorgulanabilir — "Show me the components in the acme registry", "Create a landing page using items from the acme registry". Claude Code'daki `/mcp` komutu hata ayıklama sağlar.

**Özel registry desteği:** Server, **shadcn uyumlu herhangi bir registry** ile özel yapılandırma olmadan çalışır.

**Resmi iyi uygulamalar:** açık açıklamalar, doğru bağımlılık listesi, kebab-case adlandırma, öğe ilişkileri için `registryDependencies` bildirimi.

**Doveify için:** MCP, Doveify registry'sini Claude Code'a keşfedilebilir kılar. Ancak MCP bir **erişim katmanıdır, otorite katmanı değildir** — CLAUDE.md ve DESIGN-LOCK önceliği MCP tarafından değiştirilmez.

---

## 9. Doveify Web System İçin Değerlendirme

### 9.1 Avantajlar

1. **Sunucu gerekmiyor.** GitHub repo'nun kendisi source registry olur. Doveify'ın Next.js uygulaması olmaması bir engel değil; standart bunu zaten destekliyor.
2. **Kod-dışı dağıtım birinci sınıf.** `registry:file` + `target` sayesinde rules, conventions, QA rules, skills ve tooling dosyaları component'e sarılmadan dağıtılır. Resmi doküman bunun örneğini `AGENTS.md` ile veriyor.
3. **Sürüm sabitleme yerleşik.** `#ref` ile tag veya commit SHA'ya sabitleme; müşteri projelerinde tekrarlanabilir kurulum sağlar.
4. **Kompozisyon.** `include` ile `registry/components/` ve `registry/patterns/` ayrı yönetilir — mevcut iskeletle birebir örtüşüyor.
5. **Hazır QA kapısı.** `registry validate` build gerektirmeden kaynak üzerinde çalışır; CI'a doğrudan takılabilir.
6. **Namespace ayrımı otoriteyi yansıtıyor.** `@doveify` ile dış kaynaklar farklı namespace'lerde kalır; "kaynak havuzu ≠ tasarım dili" ayrımı teknik olarak uygulanır.
7. **Public/private tek standart.** Genel sistem public, müşteri varlıkları private repo'da; mekanizma değişmiyor.
8. **Ajan keşfi.** MCP ile Claude Code registry'yi doğal dille sorgulayabilir.
9. **Bağlayıcılık düşük.** Standart bir JSON manifest + düz dosyalar. Terk edilirse dosyalar olduğu gibi kalır; kilitlenme riski sınırlı.

### 9.2 Dezavantajlar ve Riskler

1. **shadcn/ui ekosistem yerçekimi.** Standardı benimsemek, ekipte "shadcn/ui varsayılan tasarım dilimiz" algısı yaratabilir. **Bu risk teknik değil, kültüreldir** ve CLAUDE.md §0/§5 ile açıkça bastırılmalıdır.
2. **React/TS ağırlıklı tasarlanmış.** Öğe tiplerinin çoğu React odaklı (`registry:hook`, `registry:ui`, `registry:page`). Kod-dışı varlıklar `registry:file` / `registry:item` ile taşınıyor — çalışıyor, ama standardın ana akımı değil. Şema geliştikçe bu tiplerin önceliği değişebilir.
3. **Tüketici tarafında `components.json` gerekiyor.** Namespace kullanımı için tüketen projede shadcn yapılandırması bulunmalı. Doveify'ın kendisinde gerekmiyor, ama müşteri projelerinde bir varsayım oluşturuyor.
4. **GitHub'a bağımlılık.** GitHub Enterprise desteklenmiyor; adresler her zaman `github.com`'a çözülüyor. Self-hosted git kullanımı bu yolu kapatır.
5. **5 MiB dosya sınırı.** Büyük varlıklar (video, yüksek çözünürlüklü imagery) registry üzerinden dağıtılamaz; ayrı bir yol gerekir.
6. **Symlink tutarsızlığı.** Anonim ve kimlik doğrulamalı okuma farklı sonuç veriyor. Registry kaynaklarında symlink kullanılmamalı.
7. **Spesifikasyon hareket halinde.** `include` ve `validate` Mayıs 2026'da geldi. Standart aktif geliştirmede; kırıcı değişiklik riski sıfır değil. Sabitlenmiş CLI sürümü kullanmak riski azaltır.
8. **Doğrulama kapsamı sınırlı.** `registry validate` şema ve yol doğruluğunu kontrol eder; Doveify'ın estetik ve performans kurallarını (CLAUDE.md §2–§4) denetlemez. Bunlar için ayrı QA katmanı gerekir.

### 9.3 Kapsam Dışı Bırakılanlar (v1)

- **Dynamic registry / server** — `loadRegistry()` ve `loadRegistryItem()` ile istek anında sunum mümkün, ancak çalışan bir sunucu gerektirir. Static source registry v1 ihtiyacını karşılıyor.
- **Authentication altyapısı** — private repo + `GH_TOKEN` yeterli; ayrı token sunucusu gereksiz.
- **Open in v0** — Doveify iş akışıyla ilgisiz.

---

## 10. Resmi Kaynaklar

Tümü doğrulandı (bkz. §11).

| Konu | URL |
|---|---|
| Registry — Giriş | https://ui.shadcn.com/docs/registry |
| Getting Started | https://ui.shadcn.com/docs/registry/getting-started |
| GitHub Registries | https://ui.shadcn.com/docs/registry/github |
| registry.json | https://ui.shadcn.com/docs/registry/registry-json |
| registry-item.json | https://ui.shadcn.com/docs/registry/registry-item-json |
| Namespaces | https://ui.shadcn.com/docs/registry/namespace |
| Authentication | https://ui.shadcn.com/docs/registry/authentication |
| MCP Server | https://ui.shadcn.com/docs/registry/mcp |
| Registry Directory | https://ui.shadcn.com/docs/registry/registry-index |
| API Reference | https://ui.shadcn.com/docs/registry/api-reference |
| Changelog — Include & Validate (Mayıs 2026) | https://ui.shadcn.com/docs/changelog/2026-05-registry-include |
| Changelog — CLI v4 (Mart 2026) | https://ui.shadcn.com/docs/changelog/2026-03-cli-v4 |
| CLI | https://ui.shadcn.com/docs/cli |
| Şema — registry.json | https://ui.shadcn.com/schema/registry.json |
| Şema — registry-item.json | https://ui.shadcn.com/schema/registry-item.json |
| Kaynak kod (resmi repo) | https://github.com/shadcn-ui/ui |

---

## 11. Kaynak Doğrulama

Kullanılan iki alan adının resmi olduğu, birbirini teyit eden iki yönlü kontrolle doğrulandı.

**GitHub API — `https://api.github.com/repos/shadcn-ui/ui`:**

| Alan | Değer |
|---|---|
| `full_name` | `shadcn-ui/ui` |
| Owner tipi | **Organization** |
| `homepage` | **`https://ui.shadcn.com`** |
| `fork` | **false** (orijinal repo) |
| Lisans | MIT |
| Yıldız | ~121.977 |

Resmi organizasyon reposu, homepage olarak `ui.shadcn.com`'u beyan ediyor. Bu, dokümantasyon alan adını doğrulayan bağlayıcı kanıttır.

**Şema URL'leri** aynı alan adı altında yayımlanıyor (`https://ui.shadcn.com/schema/registry.json`), yani şema ve dokümantasyon aynı resmi kaynaktan geliyor.

**Kullanılmayanlar:** Üçüncü taraf blog yazıları, tutorial siteleri, topluluk registry araçları ve arama sonuçlarında görünen dolaylı kaynaklar bilinçli olarak dışlandı.
