# 03 – Image

`Image`, görsel göstermek için kullanılan component'tir. Görsel iki kaynaktan gelebilir:

| Kaynak | Yazımı | Not |
|---|---|---|
| **İnternetten** (uzak) | `source={{ uri: 'https://...' }}` | **Genişlik ve yükseklik vermek ZORUNLU**, yoksa görsel görünmez! |
| **Projeden** (yerel) | `source={require('./assets/foto.png')}` | Boyutu dosyadan kendisi alır |

> ⚠️ En sık hata: `uri` ile görsel ekleyip `style={{ width, height }}` vermemek. Görsel yüklenir ama 0x0 boyutunda olduğu için ekranda hiçbir şey görünmez.

---

## 🖼️ Image hızlı tablo

| Prop | Ne yapar | Değerler |
|---|---|---|
| `source` | Görselin adresi | `{ uri: '...' }` veya `require('...')` |
| `resizeMode` | Görsel kutuya nasıl sığsın | `'cover'` (doldur, taşanı kes), `'contain'` (tamamı görünsün, boşluk kalabilir), `'stretch'` (esnet, oran bozulur), `'center'` (ortala, büyütme), `'repeat'` (döşe) |
| `blurRadius` | Bulanıklık | `0` net, sayı büyüdükçe bulanık |
| `style` | Boyut, köşe, kenarlık | `width`, `height`, `borderRadius`, `borderWidth` |

| Event | Ne zaman çalışır |
|---|---|
| `onLoadStart` | Yükleme **başlayınca** |
| `onLoad` | Yükleme **başarıyla bitince** |
| `onError` | Görsel **yüklenemezse** (adres yanlış, internet yok) |
| `onLoadEnd` | Başarılı ya da başarısız, yükleme **bitince** |

> `Image`'in **`onPress`'i yoktur.** Görsele tıklanabilir olsun isteniyorsa `TouchableOpacity` ile sarılır (bkz. Kod 1 – S11).

**Hazır test görselleri:**
- `https://picsum.photos/600/400` → her açılışta rastgele görsel
- `https://picsum.photos/seed/kedi/600/400` → `seed` sonrası kelime aynı kaldıkça hep aynı görsel gelir (kelimeyi değiştir → başka görsel)
- `https://picsum.photos/id/237/600/400` → belirli bir görsel (237 bir köpek fotoğrafı)

---

## 📁 Snack'te yerel (kendi) görsel ekleme

1. Snack'te sol taraftaki dosya listesinde **`assets`** klasörünü bul.
2. Bilgisayarındaki görseli bu klasörün üzerine **sürükle bırak** (ya da klasörün yanındaki menüden yükle).
3. Kodda dosya adıyla çağır:
```jsx
<Image
  source={require('./assets/kedi.png')}   // 👈 dosya adı birebir aynı olmalı (büyük/küçük harf dahil)
  style={{ width: 200, height: 200 }}
/>
```
> `require` içindeki yol `App.js`'e göredir. `App.js` ana klasörde olduğu için `./assets/...` yazılır.

---

## 📦 Temel Kod 1 – Yükleme Göstergeli Haber Kartı

```jsx
import { useState } from 'react';
import {
  ActivityIndicator,
  Image,
  SafeAreaView,
  StyleSheet,
  Text,
  View,
} from 'react-native';

export default function NewsCard() {
  // Görsel hâlâ yükleniyor mu?
  const [loading, setLoading] = useState(true);
  // Görsel yüklenemedi mi?
  const [error, setError] = useState(false);

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      {/* Kartı dikeyde ortalayan kutu */}
      <View style={{ flex: 1, justifyContent: 'center', padding: 24 }}>
        {/* KART: beyaz, yuvarlak köşeli, hafif gölgeli */}
        <View
          style={{
            borderRadius: 20,
            backgroundColor: '#fff',
            shadowColor: '#000',
            shadowOpacity: 0.08,
            shadowRadius: 12,
            elevation: 3,
          }}
        >
          {/* GÖRSEL ALANI: 200 yükseklik.
              overflow: 'hidden' → içindeki görsel üst köşelerin yuvarlaklığından taşmasın */}
          <View
            style={{
              height: 200,
              overflow: 'hidden',
              borderTopLeftRadius: 20,
              borderTopRightRadius: 20,
            }}
          >
            {/* YÜKLENİYOR katmanı: loading true VE hata yoksa göster.
                absoluteFillObject → bu View, görsel alanının tamamını üstten kaplar */}
            {loading && !error && (
              <View
                style={{
                  ...StyleSheet.absoluteFillObject,
                  justifyContent: 'center',
                  alignItems: 'center',
                  backgroundColor: '#e2e8f0',
                }}
              >
                {/* Dönen yükleniyor çarkı */}
                <ActivityIndicator color="#2563eb" />
              </View>
            )}

            {/* HATA katmanı: error true ise göster */}
            {error && (
              <View
                style={{
                  ...StyleSheet.absoluteFillObject,
                  justifyContent: 'center',
                  alignItems: 'center',
                  backgroundColor: '#e2e8f0',
                }}
              >
                <Text>Image failed to load</Text>
              </View>
            )}

            {/* GÖRSELİN KENDİSİ: alanı %100 doldurur.
                Event'ler yukarıdaki state'leri güncelleyerek katmanları açıp kapatır */}
            <Image
              source={{ uri: 'https://picsum.photos/600/400' }}
              style={{ width: '100%', height: '100%' }}
              onLoadStart={() => {
                setLoading(true);   // yükleme başladı → çark görünsün
                setError(false);
              }}
              onLoad={() => setLoading(false)}  // yüklendi → çark kaybolsun
              onError={() => {
                setLoading(false);  // yükleme bitti...
                setError(true);     // ...ama hata var → hata mesajı görünsün
              }}
            />
          </View>

          {/* METİN ALANI: başlık + açıklama */}
          <View style={{ padding: 20, gap: 8 }}>
            <Text style={{ fontSize: 18, fontWeight: '600', color: '#111827' }}>
              New React Native Course Launch
            </Text>
            <Text style={{ color: '#475569', lineHeight: 20 }}>
              The latest series covers everything from core components to performance
              optimization, all built with Expo.
            </Text>
          </View>
        </View>
      </View>
    </SafeAreaView>
  );
}
```

> **`...StyleSheet.absoluteFillObject` ne demek?** `{ position: 'absolute', top: 0, left: 0, right: 0, bottom: 0 }` nesnesinin kısa yoludur. Baştaki `...` (spread) bu özellikleri style'ın içine açar. Sonuç: o View, ebeveyninin tamamını **üstten kaplayan** bir katman olur. Yükleniyor ve hata ekranları görselin üstüne bu sayede biner.

### Olası sorular – Kod 1

**S1. Görseli değiştir**
```jsx
source={{ uri: 'https://picsum.photos/id/237/600/400' }}   // 👈 sadece adres değişir
```

**S2. Görsel alanını büyüt (ör. 300 yükseklik)**
```jsx
height: 300,   // 👈 GÖRSEL ALANI View'ında 200 → 300 (Image zaten %100 olduğu için onu değiştirme)
```

**S3. Görselin tüm köşeleri yuvarlak olsun**
```jsx
// GÖRSEL ALANI View'ında iki satırı tek satırla değiştir:
borderRadius: 20,   // 👈 borderTopLeftRadius + borderTopRightRadius yerine
```

**S4. Haberin başlığını ve açıklamasını değiştir**
```jsx
<Text style={{ fontSize: 18, fontWeight: '600', color: '#111827' }}>
  Yeni React Native Kursu Başladı      {/* 👈 */}
</Text>
<Text style={{ color: '#475569', lineHeight: 20 }}>
  Bu seride temel component'lerden performansa kadar her şey var.   {/* 👈 */}
</Text>
```

**S5. Yükleniyor çarkını büyük ve kırmızı yap**
```jsx
<ActivityIndicator color="#ef4444" size="large" />   // 👈 renk değişti, size eklendi ('small' | 'large')
```

**S6. Hata mesajını Türkçe ve kırmızı yap**
```jsx
<Text style={{ color: '#ef4444', fontWeight: '600' }}>Görsel yüklenemedi</Text>   // 👈
```

**S7. Hata ekranını test et**
```jsx
source={{ uri: 'https://yanlis-adres.example.com/foto.jpg' }}   // 👈 bilerek bozuk adres → onError çalışır
```

**S8. İnternet yerine kendi görselini kullan** (önce yukarıdaki "Snack'te yerel görsel ekleme" adımlarını yap)
```jsx
source={require('./assets/kedi.png')}   // 👈 { uri: ... } yerine require, süslü parantez TEK kat
```

**S9. Görselin tamamı kırpılmadan görünsün**
```jsx
<Image
  source={{ uri: 'https://picsum.photos/600/400' }}
  style={{ width: '100%', height: '100%' }}
  resizeMode="contain"          // 👈 YENİ (varsayılan 'cover' kenarları kırpar)
  ...
```

**S10. Başlığın altına yuvarlak yazar fotoğrafı + isim ekle**
```jsx
{/* METİN ALANI'nda açıklama Text'inin ALTINA */}
<View style={{ flexDirection: 'row', alignItems: 'center', gap: 10 }}>   {/* 👈 yan yana, dikeyde ortalı */}
  <Image
    source={{ uri: 'https://picsum.photos/seed/yazar/100' }}
    style={{ width: 40, height: 40, borderRadius: 20 }}   // 👈 borderRadius = genişliğin YARISI → tam daire
  />
  <Text style={{ color: '#111827', fontWeight: '600' }}>Kübra Yılmaz</Text>
</View>
```

**S11. Görsele basınca uyarı çıksın**
```jsx
import { ActivityIndicator, Alert, Image, SafeAreaView, StyleSheet, Text, TouchableOpacity, View } from 'react-native';   // 👈 Alert, TouchableOpacity

{/* Image'i TouchableOpacity ile SAR */}
<TouchableOpacity
  style={{ width: '100%', height: '100%' }}                     // 👈 sarmalayıcı da alanı doldurmalı
  onPress={() => Alert.alert('Haber', 'Görsele tıkladın!')}
>
  <Image
    source={{ uri: 'https://picsum.photos/600/400' }}
    style={{ width: '100%', height: '100%' }}
    onLoadStart={() => { setLoading(true); setError(false); }}
    onLoad={() => setLoading(false)}
    onError={() => { setLoading(false); setError(true); }}
  />
</TouchableOpacity>
```

**S12. Görsele kenarlık ekle**
```jsx
// GÖRSEL ALANI View'ına ekle:
borderWidth: 3,
borderColor: '#2563eb',
```

---

## 📦 Temel Kod 2 – Image Playground (resizeMode, blur, hata yönetimi)

```jsx
import { useMemo, useState } from 'react';
import {
  ActivityIndicator,
  Image,
  SafeAreaView,
  Text,
  TouchableOpacity,
  View,
} from 'react-native';

// Asıl gösterilecek görsel
const PRIMARY_URI = 'https://images.unsplash.com/photo-1521572267360-ee0c2909d518';
// Asıl görsel yüklenemezse gösterilecek yedek görsel (isim baş harflerinden avatar üretir)
const FALLBACK_URI =
  'https://ui-avatars.com/api/?background=2563eb&color=fff&name=React+Native';
// Denenebilecek resizeMode değerleri → her biri için bir buton oluşacak
const MODES = ['cover', 'contain', 'stretch', 'center', 'repeat'];

export default function ImagePlayground() {
  const [uri, setUri] = useState(PRIMARY_URI);              // şu an gösterilen adres
  const [status, setStatus] = useState('Ready');            // 'loading' | 'loaded' | 'fallback' | 'Ready'
  const [resizeMode, setResizeMode] = useState(MODES[0]);   // başlangıç: 'cover'
  const [blurEnabled, setBlurEnabled] = useState(false);    // bulanıklık açık mı?

  // status'a göre ekranda yazacak mesaj. useMemo → sadece status değişince yeniden hesaplanır
  const infoText = useMemo(() => {
    switch (status) {
      case 'loading':
        return 'Image is loading...';
      case 'loaded':
        return 'Image loaded successfully.';
      case 'fallback':
        return 'Primary source failed, showing fallback image.';
      default:
        return 'Ready.';
    }
  }, [status]);

  // "Simulate failure" butonu: bilerek bozuk adres ver → onError tetiklenir
  const triggerError = () => {
    setStatus('loading');
    setUri('https://invalid-url.example.com/image.jpg');
  };

  // "Back to original" butonu: asıl görsele dön
  const enablePrimary = () => {
    setStatus('loading');
    setUri(PRIMARY_URI);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a' }}>
      <View
        style={{
          flex: 1,
          padding: 24,
          justifyContent: 'center',
          gap: 16,
        }}
      >
        {/* Başlık */}
        <Text style={{ color: '#f8fafc', fontSize: 22, fontWeight: '700' }}>
          Image playground
        </Text>

        {/* Aktif ayarları gösteren bilgi satırı (iç içe Text ile mod adı kalın) */}
        <Text style={{ color: '#94a3b8' }}>
          Active `resizeMode`: <Text style={{ fontWeight: '700' }}>{resizeMode}</Text> ·
          `blurRadius`: {blurEnabled ? 18 : 0}
        </Text>

        {/* GÖRSEL KUTUSU: 220 yükseklik, içerik ortalı (çark ortada dursun diye) */}
        <View
          style={{
            height: 220,
            borderRadius: 20,
            backgroundColor: '#1f2937',
            overflow: 'hidden',
            justifyContent: 'center',
            alignItems: 'center',
          }}
        >
          {/* Yükleniyorsa çark göster */}
          {status === 'loading' && (
            <ActivityIndicator color="#38bdf8" size="large" />
          )}
          <Image
            source={{ uri }}                          // { uri: uri } ile aynı (kısa yazım)
            style={{ width: '100%', height: '100%' }}
            resizeMode={resizeMode}                   // state'ten gelir, butonlarla değişir
            blurRadius={blurEnabled ? 18 : 0}         // blur açıksa 18, değilse 0
            onLoadStart={() => setStatus('loading')}
            onLoad={() => setStatus('loaded')}
            onError={() => {
              setStatus('fallback');                  // hata → durum mesajı değişsin
              setUri(FALLBACK_URI);                   // ve yedek görsele geç
            }}
          />
        </View>

        {/* Durum mesajı */}
        <Text style={{ color: '#e2e8f0' }}>{infoText}</Text>

        {/* resizeMode BUTONLARI: MODES dizisindeki her değer için bir buton */}
        <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 8 }}>
          {MODES.map((mode) => {
            const isActive = mode === resizeMode;     // bu buton seçili olan mı?
            return (
              <TouchableOpacity
                key={mode}
                onPress={() => setResizeMode(mode)}
                style={{
                  paddingVertical: 10,
                  paddingHorizontal: 14,
                  borderRadius: 999,                  // çok büyük sayı → hap şeklinde buton
                  backgroundColor: isActive ? '#38bdf8' : '#1f2937',   // seçiliyse mavi
                }}
              >
                <Text style={{ color: isActive ? '#0f172a' : '#e2e8f0' }}>
                  {mode}
                </Text>
              </TouchableOpacity>
            );
          })}
        </View>

        {/* ALT BUTONLAR: blur aç/kapa, hata simüle et, orijinale dön */}
        <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 12 }}>
          <TouchableOpacity
            onPress={() => setBlurEnabled((prev) => !prev)}   // true ↔ false
            style={{
              paddingVertical: 12,
              paddingHorizontal: 16,
              borderRadius: 12,
              backgroundColor: blurEnabled ? '#f97316' : '#1e293b',
            }}
          >
            <Text style={{ color: '#f8fafc', fontWeight: '600' }}>
              {blurEnabled ? 'Disable blur' : 'Enable blur'}
            </Text>
          </TouchableOpacity>

          <TouchableOpacity
            onPress={triggerError}
            style={{
              paddingVertical: 12,
              paddingHorizontal: 16,
              borderRadius: 12,
              backgroundColor: '#ef4444',
            }}
          >
            <Text style={{ color: '#fff', fontWeight: '600' }}>
              Simulate failure
            </Text>
          </TouchableOpacity>

          <TouchableOpacity
            onPress={enablePrimary}
            style={{
              paddingVertical: 12,
              paddingHorizontal: 16,
              borderRadius: 12,
              backgroundColor: '#22c55e',
            }}
          >
            <Text style={{ color: '#0f172a', fontWeight: '600' }}>
              Back to original
            </Text>
          </TouchableOpacity>
        </View>
      </View>
    </SafeAreaView>
  );
}
```

### Olası sorular – Kod 2

**S1. Ana görseli değiştir**
```jsx
const PRIMARY_URI = 'https://picsum.photos/id/1015/800/600';   // 👈 tek satır, her yer güncellenir
```

**S2. Uygulama 'contain' moduyla açılsın**
```jsx
const [resizeMode, setResizeMode] = useState(MODES[1]);   // 👈 MODES[0]='cover', MODES[1]='contain'
// veya doğrudan: useState('contain')
```

**S3. Bulanıklık miktarını 8 yap**
```jsx
// ⚠️ 18 İKİ yerde geçiyor, ikisini de değiştir:
`blurRadius`: {blurEnabled ? 8 : 0}        // 👈 bilgi satırında
blurRadius={blurEnabled ? 8 : 0}           // 👈 Image'de
```
> Daha temiz yol: en üste `const BLUR = 8;` yaz, iki yerde de `18` yerine `BLUR` kullan.

**S4. Yedek görselin rengini ve yazısını değiştir**
```jsx
const FALLBACK_URI =
  'https://ui-avatars.com/api/?background=ef4444&color=fff&name=Kubra+Y';
//                              👆 arka plan (# olmadan)       👆 baş harfler (boşluk yerine +)
```

**S5. Seçili mod butonunun rengini değiştir**
```jsx
backgroundColor: isActive ? '#22c55e' : '#1f2937',   // 👈 seçili: mavi → yeşil
```

**S6. Durum mesajlarını Türkçe yap**
```jsx
case 'loading':
  return 'Görsel yükleniyor...';
case 'loaded':
  return 'Görsel başarıyla yüklendi.';
case 'fallback':
  return 'Asıl görsel yüklenemedi, yedek gösteriliyor.';
default:
  return 'Hazır.';
```

**S7. Görsel kutusunu büyüt ve köşelerini değiştir**
```jsx
height: 300,         // 👈 220 → 300
borderRadius: 0,     // 👈 20 → 0 (köşeli)
```

**S8. Yeni bir buton ekle: "Başka görsel"**
```jsx
{/* ALT BUTONLAR View'ının içine, son TouchableOpacity'nin ALTINA */}
<TouchableOpacity
  onPress={() => {
    setStatus('loading');
    setUri('https://picsum.photos/seed/yeni/800/600');   // 👈 farklı bir adres
  }}
  style={{
    paddingVertical: 12,
    paddingHorizontal: 16,
    borderRadius: 12,
    backgroundColor: '#a855f7',                          // 👈 mor
  }}
>
  <Text style={{ color: '#fff', fontWeight: '600' }}>Başka görsel</Text>
</TouchableOpacity>
```

**S9. Mod listesinden 'repeat' butonunu kaldır**
```jsx
const MODES = ['cover', 'contain', 'stretch', 'center'];   // 👈 diziden silmek yeterli, buton otomatik kalkar
```

**S10 (zor). Blur aç/kapa yerine 3 seviye: 0 → 8 → 18 → 0...**
```jsx
const BLUR_LEVELS = [0, 8, 18];                                    // 👈 YENİ (MODES'un altına)

// state: blurEnabled yerine
const [blurIndex, setBlurIndex] = useState(0);                     // 👈

// Image'de:
blurRadius={BLUR_LEVELS[blurIndex]}                                 // 👈

// Bilgi satırında:
`blurRadius`: {BLUR_LEVELS[blurIndex]}                              // 👈

// Blur butonu:
<TouchableOpacity
  onPress={() => setBlurIndex((i) => (i + 1) % BLUR_LEVELS.length)}  // 👈 0→1→2→0
  style={{
    paddingVertical: 12,
    paddingHorizontal: 16,
    borderRadius: 12,
    backgroundColor: blurIndex > 0 ? '#f97316' : '#1e293b',         // 👈 blur varsa turuncu
  }}
>
  <Text style={{ color: '#f8fafc', fontWeight: '600' }}>
    Blur: {BLUR_LEVELS[blurIndex]}                                   {/* 👈 */}
  </Text>
</TouchableOpacity>
```
> Bu, View dosyasındaki S12 (renk değiştirme) ile aynı `% length` döngü mantığı.

---

## ⚠️ Sık hatalar
- `uri` ile eklenen görsele `width` ve `height` vermemek → görsel görünmez.
- `require` ile yazarken süslü parantezi çift yazmak: `source={{ require(...) }}` **yanlış**, `source={require(...)}` **doğru**. `uri`'de ise çift süslü parantez doğrudur: `source={{ uri: '...' }}`.
- Görsele doğrudan `onPress` vermek → çalışmaz, `TouchableOpacity` ile sar.
- Köşeleri yuvarlattığın kutuda görsel köşelerden taşıyorsa: kutuya `overflow: 'hidden'` ekle.
- Tam daire görsel için: `width` ve `height` eşit, `borderRadius` = bunların yarısı.
