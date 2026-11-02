# Hanedan Park — Düğün Teklif Quiz'i (v2)

Google Ads trafiği için açılış sayfası. Meta lead formuyla aynı soruları sorar: dönem, davetli sayısı, bütçe ve iletişim bilgileri.

- **Önizleme:** https://novavitamedia-lgtm.github.io/hanedanpark-teklif/
- **Hedef adres:** `https://www.hanedanpark.com/teklif`

## Dosyalar
```
index.html   ← tek dosya; HTML + CSS + JS, bağımlılık yok
peri/        ← maskot görselleri (5 adet webp, ~260 KB)
```
Mekân görselleri sitenin kendi galerisinden okunur (`https://www.hanedanpark.com/images/gallery/DSC*.webp`).

## Kurulum (Next.js, en hızlı yol)
1. `index.html` ve `peri/` klasörünü `public/teklif/` içine kopyalayın.
2. Sayfadaki `peri/` göreli yollarını `/teklif/peri/` yapın (dosyada 10 yer var, tek bul-değiştir). Yoksa `/teklif` adresinde görseller kırılır.
3. `next.config.js` içine şu rewrite'ı ekleyin:
   ```js
   async rewrites() { return [{ source: "/teklif", destination: "/teklif/index.html" }] }
   ```
4. **GTM:** `public/` içindeki statik dosya sitenin layout'unu kullanmaz, bu yüzden GTM yüklenmez. `<head>` içine şunu ekleyin:
   ```html
   <script>(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src='https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);})(window,document,'script','dataLayer','GTM-WCFJTWCL');</script>
   ```
   Sayfayı React route'u olarak taşırsanız GTM layout'tan gelir; bu adımı atlayın, çift yüklemeyin.

Sayfa `noindex` olarak işaretli; öyle kalsın, yalnızca reklam trafiği için.

## Doldurulacak ayarlar (`index.html` → `CONFIG`)
| Alan | Açıklama |
|---|---|
| `LEAD_ENDPOINT` | **Zorunlu.** Form verisini JSON olarak POST eden adres, örneğin `/api/lead`. Boş kalırsa lead hiçbir yerde saklanmaz. |
| `KVKK_URL` | KVKK aydınlatma metni sayfası. Onay kutusu bu adrese bağlanır. |
| `WHATSAPP` | `905308332995` (teşekkür ekranındaki buton) |

### Gelen veri (`POST`, `application/json`)
```json
{ "kaynak":"quiz", "sayfa":"…", "zaman":"ISO tarih",
  "ad":"…", "telefon":"+905xxxxxxxxx", "eposta":"…", "tarih":"serbest metin",
  "donem":"…", "davetli":"…", "butce":"…",
  "gclid":"…", "gbraid":"…", "wbraid":"…", "fbclid":"…", "utm_source":"…", "utm_medium":"…", "utm_campaign":"…" }
```
Örnek route (`app/api/lead/route.ts`), mail, Sheet ya da CRM'e iletmek için:
```ts
export async function POST(req: Request) {
  const lead = await req.json();
  // TODO: mail / Google Sheet / CRM'e gönder
  return Response.json({ ok: true });
}
```

## Takip (GTM-WCFJTWCL)
- `quiz_step`: her soru ekranında gönderilir (`quiz_step`, `quiz_step_name`).
- `generate_lead`: form gönderilince gönderilir (`lead_donem`, `lead_davetli`, `lead_butce`, `user_data.phone_number/email`).
- Gönderimden sonra URL `#tesekkurler` olur.

GTM etiketlerini (Google Ads dönüşümü ve Meta Lead) ajans kuracak. Geliştirici tarafında ek bir şey gerekmez.

## Kontrol listesi
- [ ] `/teklif` açılıyor, peri ve galeri görselleri yükleniyor
- [ ] `LEAD_ENDPOINT` dolu ve test lead'i ulaştı
- [ ] `KVKK_URL` dolu
- [ ] GTM bir kez yükleniyor (Tag Assistant ile kontrol)
