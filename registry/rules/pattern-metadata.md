# Doveify — Pattern Metadata Standardı (v1.1)

**Kaynak:** Doveify Web System · `@doveify/pattern-metadata`
**Üstünde:** `.doveify/rules/core.md` §1 otorite sırası geçerlidir.

---

## 1. Pattern Nedir, Ne Değildir

Bir Doveify PATTERN, **tekrar kullanılabilir bir kompozisyon/UX tarifidir.**
Belirli bir görünümü dayatmaz.

**Pattern DEĞİLDİR:**

- ❌ "hero-pattern = sol başlık + sağ görsel + CTA"
- ❌ sabit bir layout şeması
- ❌ hazır bir tasarım şablonu
- ❌ bir component'in kılık değiştirmiş hâli

**Pattern ŞUDUR:**

- ✅ hangi **kompozisyon problemini** çözdüğü
- ✅ hangi **koşullarda** kullanılacağı
- ✅ hangi **varyasyonlara** izin verdiği
- ✅ hangi **template risklerini** taşıdığı

Bir pattern dosyası okunduğunda tek bir görüntü akla geliyorsa, o pattern değil
şablondur ve reddedilir.

**DESIGN-LOCK her zaman pattern seçiminden üstündür.** Pattern bir öneri katmanıdır;
lock bir otorite katmanıdır.

---

## 2. Kategori Taksonomisi (v1)

Bir pattern **tam olarak bir** kategoriye aittir.

| Kategori | Çözdüğü problem alanı |
|---|---|
| `hero` | Sayfaya giriş anı: ilk odak, vaat ve yön verme |
| `navigation` | Yönelim ve erişim: kullanıcı nerede, nereye gidebilir |
| `section` | Sayfa gövdesinin ritmi: içerik bloklarının sırası ve nefesi |
| `typography` | Metnin hiyerarşisi, ölçeği ve okunabilirliği |
| `imagery` | Görselin kompozisyondaki rolü — dekor değil, yapı elemanı |

Bu liste v1'de kapalıdır. Yeni kategori, `core.md` §4 değerlendirmesinden ve bir ADR
kaydından geçer.

---

## 3. Zorunlu Metadata Alanları

Her pattern aşağıdaki **15 alanın tamamını** taşımak zorundadır. Boş bırakılan alan
"yok" anlamına gelmez; pattern eksiktir ve sisteme alınmaz.

İlk 11 alan pattern'in **kendisini** tanımlar; son 4 alan (v1.1'de eklendi) pattern'in
**seçilmesini ve şablona kaymamasını** yönetir.

| Alan | Tip | Anlamı |
|---|---|---|
| **NAME** | string | kebab-case, registry içinde tekil |
| **CATEGORY** | enum | §2'deki beş değerden biri |
| **PROBLEM** | string | Hangi somut kompozisyon problemini çözüyor |
| **USE WHEN** | string[] | Hangi koşullarda seçilir |
| **AVOID WHEN** | string[] | Hangi koşullarda seçilmez |
| **DESIGN-LOCK INPUTS** | string[] | Lock'un bu pattern'e verebileceği girdiler |
| **RESPONSIVE PRINCIPLE** | string | Ölçek değişince ne korunur, ne feda edilir |
| **ACCESSIBILITY NOTES** | string[] | Bu pattern'e özgü a11y yükümlülükleri |
| **PERFORMANCE NOTES** | string[] | Bu pattern'e özgü performans riskleri |
| **TEMPLATE RISK** | string | Nerede şablona dönüşür, nasıl engellenir |
| **CUSTOMIZATION AXES** | string[] | Hangi eksenlerde serbest varyasyon var |
| **ANTI-PATTERN** | string | Hangi jenerik AI/template davranışını önlüyor |
| **VARIATION LOGIC** | string | Aynı görünümü tekrar üretmeden hangi eksenlerde değişir |
| **SELECTION SIGNALS** | string[] | DESIGN-LOCK'taki hangi sinyaller bu pattern'e yöneltir |
| **REJECTION SIGNALS** | string[] | Hangi sinyaller varsa bu pattern seçilmemeli |

### Alan yazım kuralları

**PROBLEM** — bir cümle, problem odaklı. Çözüm anlatmaz.
Kötü: "Büyük başlık ve buton gösterir." · İyi: "Kullanıcının ilk 3 saniyede neyle
karşılaştığını, sayfayı kaydırmadan anlamasını sağlar."

**USE WHEN / AVOID WHEN** — gözlemlenebilir koşullar. "Güzel durduğunda" koşul değildir.

**DESIGN-LOCK INPUTS** — lock'un bu pattern'i hangi parametrelerle sınırlayabileceği.
Pattern'in lock'a nasıl teslim olduğunu tanımlar.

**RESPONSIVE PRINCIPLE** — breakpoint listesi değil, **karar kuralı.** "Ölçek
daraldığında hiyerarşi korunur, eşzamanlılık feda edilir" gibi.

**TEMPLATE RISK** — `core.md` §2'ye doğrudan bağlanır. Bu alan pattern'in kendi kendine
uyguladığı denetimdir; boş geçilemez.

**CUSTOMIZATION AXES** — serbestlik alanı. Bu eksenler dışında sabitlenen her şey
gerekçelenmelidir.

**ANTI-PATTERN** — somut ve tanınabilir olmalı. "Kötü tasarım" değil, üretimde gerçekten
karşılaşılan biçim: neye benzediği, neden yaygın olduğu, neyi bozduğu. TEMPLATE RISK
pattern'in **kendi** kayma riskini, ANTI-PATTERN pattern'in **önlediği** dışsal
davranışı tarif eder; ikisi aynı şey değildir.

**VARIATION LOGIC** — pattern'i iki farklı projede uygulamanın neden iki farklı görüntü
üretmesi gerektiğini açıklar. En az iki birbirine benzemeyen sonuç örneklenmelidir.
Örnek verilemiyorsa pattern muhtemelen bir şablondur.

**SELECTION SIGNALS / REJECTION SIGNALS** — DESIGN-LOCK'ta **gözlemlenebilir** sinyaller.
"Uygun göründüğünde" sinyal değildir. İkisi birbirinin değili olmamalı; rejection
sinyalleri bağımsız olarak da doğru olabilmelidir. Bir rejection sinyali varsa pattern
seçilmez — selection sinyalleri ne kadar güçlü olursa olsun.

---

## 4. Makine Okunabilir Karşılık

Metadata, registry item'ının `meta.doveify` bloğunda taşınır. Kategori ayrıca
`categories` alanında tekrarlanır (registry aramasının çalışması için).

```json
{
  "name": "<name>",
  "type": "registry:item",
  "title": "<İnsan okunur başlık>",
  "description": "<PROBLEM'in tek cümlelik özeti>",
  "categories": ["pattern", "<category>"],
  "meta": {
    "doveify": {
      "kind": "pattern",
      "version": 1,
      "category": "hero | navigation | section | typography | imagery",
      "problem": "<PROBLEM>",
      "useWhen": ["<koşul>"],
      "avoidWhen": ["<koşul>"],
      "designLockInputs": ["<girdi>"],
      "responsivePrinciple": "<karar kuralı>",
      "accessibilityNotes": ["<yükümlülük>"],
      "performanceNotes": ["<risk>"],
      "templateRisk": "<risk ve engelleme>",
      "customizationAxes": ["<eksen>"],
      "antiPattern": "<önlenen jenerik davranış>",
      "variationLogic": "<hangi eksenlerde farklılaşır>",
      "selectionSignals": ["<lock sinyali>"],
      "rejectionSignals": ["<lock sinyali>"]
    }
  },
  "files": [
    {
      "path": "<category>/<name>.md",
      "type": "registry:file",
      "target": "~/.doveify/patterns/<name>.md"
    }
  ]
}
```

**Tek kaynak kuralı:** `meta.doveify` ile markdown dosyasının başlığındaki alanlar
çelişemez. Çelişki bir hatadır; markdown dosyası kanonik metindir, `meta` onun
makine okunabilir özetidir.

---

## 5. Dosya Yerleşimi

```
registry/patterns/<category>/<name>.md    kaynak (kanonik metin)
registry/patterns/registry.json           item tanımı (meta)
~/.doveify/patterns/<name>.md             tüketici projedeki hedef
```

`<category>` klasörü §2'deki beş addan biridir ve ilk pattern ile birlikte oluşur.

**Yol kuralı:** `files[].path` değeri, item'ı bildiren `registry.json` dosyasına
**görecelidir** — repo köküne değil. `registry/patterns/registry.json` içinde doğru
değer `hero/<name>.md` biçimindedir. `target` ise her zaman tüketici proje köküne
(`~`) göredir.

---

## 6. Kabul Kapısı

Bir pattern sisteme alınmadan önce:

1. `core.md` §4 değerlendirmesi yapıldı (VALUE / USE CASE / COST / RISK / DECISION)
2. 15 alanın tamamı dolduruldu
3. §1 testi geçildi — dosya okunduğunda tek bir sabit görüntü dayatmıyor
4. TEMPLATE RISK alanı `core.md` §2'deki en az bir maddeye açıkça bağlandı
5. VARIATION LOGIC en az iki birbirine benzemeyen sonuç örnekledi
6. SELECTION ve REJECTION sinyalleri gözlemlenebilir; biri diğerinin değili değil
7. `shadcn registry validate` temiz geçti

Bu yedisi tamamlanmadan pattern `registry.json`'a eklenmez.
