# Doveify — Motion Rules

**Kaynak:** Doveify Web System · `@doveify/motion-rules`
**Üstünde:** `.doveify/rules/core.md` §1 otorite sırası geçerlidir.

---

## 1. Gerekçe Kuralı

Motion yalnızca şu üçünden **en az birine** hizmet ediyorsa kullanılır:

1. **yönlendirme** — kullanıcının dikkatini doğru yere taşır
2. **geçiş** — durum değişimini anlaşılır kılar
3. **marka karakteri** — kimliğin bilinçli bir ifadesidir

Bu üçüne hizmet etmeyen hareket **kaldırılır.** "Güzel duruyor" gerekçe değildir.

Bir animasyon eklerken hangi maddeye hizmet ettiğini kod yorumunda veya PR
açıklamasında belirt. Belirtilemiyorsa animasyon gereksizdir.

---

## 2. Varsayılan Teknik Yaklaşım

CSS `transform` / `opacity` / `clip-path` + `IntersectionObserver`.

Bu üçlü dışına çıkmak gerekçe ister. Layout tetikleyen özellikleri (`width`,
`height`, `top`, `left`, `margin`) animasyonlamak CLS üretir; `.doveify/rules/
quality-gates.md` eşiğini düşürür.

---

## 3. Yasaklar

- **Lenis yok** — ve smooth-scroll kütüphaneleri genel olarak yok
- **scroll-jacking yok** — kullanıcının scroll kontrolü devralınmaz
- **parallax yok**
- **sonsuz dekoratif animasyon yok** — kullanıcı eylemine bağlı olmayan, durmayan
  hareket üretme

Bir dış kaynaktan alınan component bunlardan birini içeriyorsa, o davranış
kaldırılarak alınır veya component reddedilir.

---

## 4. `prefers-reduced-motion`

**Zorunludur. İstisnasız.**

```css
@media (prefers-reduced-motion: reduce) {
  /* transform/opacity geçişleri kapatılır veya anlık hale getirilir */
}
```

Kuralın anlamı "süreyi kısalt" değildir. Reduced motion açıkken:

- pozisyon/ölçek değişimi taşıyan hareket **kaldırılır**
- durum değişimi hâlâ **anlaşılır** kalmalıdır (opacity veya anlık geçiş kabul edilir)
- otomatik oynayan hareket **başlamaz**

JS ile tetiklenen animasyonlarda da kontrol edilir:
`window.matchMedia('(prefers-reduced-motion: reduce)')`

Bu kontrolü içermeyen motion kodu eksiktir.
