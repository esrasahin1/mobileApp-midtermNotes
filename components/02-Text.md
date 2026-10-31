# 02 – Text

`Text`, ekrana yazı basmanın **tek** yoludur. React Native'de yazı mutlaka `<Text>` içinde olmalıdır, doğrudan `<View>` içine yazılan metin hata verir. Web'deki `<p>`, `<span>`, `<h1>` gibi etiketlerin hepsinin karşılığı budur.

> İki önemli kural:
> - `Text` içine başka bir `Text` koyabilirsin (cümlenin bir kısmını farklı renkte yazmak için).
> - `Text` içine `View` **koyamazsın**.

---

## 🔤 Yazı stilleri hızlı tablo

| Özellik | Ne yapar | Örnek |
|---|---|---|
| `fontSize` | Yazı boyutu | `fontSize: 24` |
| `fontWeight` | Kalınlık (string!) | `'400'` normal, `'600'` yarı kalın, `'800'` çok kalın, `'bold'` |
| `color` | Yazı rengi | `color: '#ef4444'` |
| `textAlign` | Yatay hizalama | `'left'`, `'center'`, `'right'` |
| `fontStyle` | İtalik | `fontStyle: 'italic'` |
| `textTransform` | Harf dönüşümü | `'uppercase'`, `'lowercase'`, `'capitalize'`, `'none'` |
| `textDecorationLine` | Çizgi | `'underline'`, `'line-through'`, `'none'` |
| `letterSpacing` | Harf arası boşluk | `letterSpacing: 2` |
| `lineHeight` | Satır yüksekliği | `lineHeight: 22` |

| Prop (style dışında) | Ne yapar |
|---|---|
| `numberOfLines={2}` | En fazla 2 satır gösterir, kalanı keser |
| `ellipsizeMode="tail"` | Kesilen yere `...` koyar: `'tail'` sonda, `'head'` başta, `'middle'` ortada, `'clip'` noktasız keser |
| `selectable` | Kullanıcı yazıyı seçip kopyalayabilir |
| `onPress` / `onLongPress` | Yazıya basılınca / uzun basılınca çalışır |

---

## 📦 Temel Kod 1 – Zengin Metin Banner'ı

```jsx
import { useEffect, useMemo, useState } from 'react';
import { Linking, SafeAreaView, Text, View } from 'react-native';

// Banner'da yan yana yazılacak başlıklar (component DIŞINDA sabit dizi)
const HIGHLIGHTS = [
  'Live coding',
  'Q&A',
  'Assignment review',
  'Release announcements',
];

export default function RichTextBanner() {
  // Geçen saniyeyi tutan state
  const [seconds, setSeconds] = useState(0);

  // Sayfa açılınca her 1 saniyede seconds'ı 1 artır
  useEffect(() => {
    const interval = setInterval(() => setSeconds((prev) => prev + 1), 1000);
    return () => clearInterval(interval); // cleanup: sayfa kapanınca timer'ı durdur
  }, []); // [] → sadece ilk açılışta bir kez kur

  // Diziyi "Live coding · Q&A · ..." şeklinde tek yazıya çevir
  // useMemo + [] → bu hesap sadece bir kez yapılır, her saniye tekrar yapılmaz
  const highlights = useMemo(() => HIGHLIGHTS.join(' · '), []);

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a' }}>
      {/* İçeriği dikeyde ortalayan kutu, satırlar arası 16 boşluk */}
      <View
        style={{
          flex: 1,
          justifyContent: 'center',
          padding: 24,
          gap: 16,
        }}
      >
        {/* 1) BAŞLIK – büyük, çok kalın, beyaz; selectable → kopyalanabilir */}
        <Text
          style={{
            fontSize: 30,
            fontWeight: '800',
            color: '#f8fafc',
          }}
          selectable
        >
          Live React Native Session
        </Text>

        {/* 2) SAYAÇ – turkuaz, BÜYÜK HARF, harfler aralıklı */}
        <Text
          style={{
            color: '#22d3ee',
            fontWeight: '600',
            textTransform: 'uppercase',
            letterSpacing: 2,
          }}
        >
          Elapsed time: {seconds}s
        </Text>

        {/* 3) AÇIKLAMA – en fazla 2 satır, fazlası "..." ile kesilir */}
        <Text
          style={{
            color: '#94a3b8',
            lineHeight: 22,
          }}
          numberOfLines={2}
          ellipsizeMode="tail"
        >
          Every week we build real projects live, answer community questions,
          and explore the latest in React Native together.
        </Text>

        {/* 4) İÇ İÇE TEXT – "Highlights:" kalın sarı, devamı normal kalınlıkta */}
        <Text
          style={{
            color: '#facc15',
            fontSize: 16,
            fontWeight: '600',
          }}
        >
          Highlights: <Text style={{ fontWeight: '400' }}>{highlights}</Text>
        </Text>

        {/* 5) LİNK – altı çizili, basınca tarayıcıda adres açılır */}
        <Text
          style={{
            color: '#38bdf8',
            textDecorationLine: 'underline',
            fontSize: 16,
          }}
          onPress={() => Linking.openURL('https://reactnative.dev/docs/text')}
        >
          Explore every Text prop in the docs
        </Text>
      </View>
    </SafeAreaView>
  );
}
```

> **İç içe Text mantığı:** İçteki `Text` dıştakinin stilini **miras alır** (renk sarı, boyut 16) ve sadece kendi yazdığı özelliği (`fontWeight: '400'`) ezer. Bu yüzden devam eden kısım da sarıdır ama kalın değildir.

### Olası sorular – Kod 1

**S1. Başlığın metnini, rengini ve boyutunu değiştir**
```jsx
<Text
  style={{
    fontSize: 36,          // 👈 30 → 36
    fontWeight: '800',
    color: '#facc15',      // 👈 beyaz → sarı
  }}
  selectable
>
  Canlı React Native Dersi   {/* 👈 metin değişti */}
</Text>
```

**S2. Başlığı (veya tüm yazıları) ortala**
```jsx
// Sadece başlık için, başlığın style'ına:
textAlign: 'center',
// Tüm yazılar için: her Text'e ayrı ayrı textAlign: 'center' eklenir
```

**S3. Açıklamanın tamamı görünsün / sadece 1 satır görünsün**
```jsx
numberOfLines={1}       // 👈 2 → 1 (tamamı görünsün istenirse bu satırı ve ellipsizeMode'u SİL)
```

**S4. Üç noktanın yerini değiştir (başa / ortaya)**
```jsx
ellipsizeMode="head"    // 👈 "tail" → "head" (başta ...)   veya "middle" (ortada ...)
```

**S5. Highlights listesine yeni madde ekle**
```jsx
const HIGHLIGHTS = [
  'Live coding',
  'Q&A',
  'Assignment review',
  'Release announcements',
  'Bonus quiz',            // 👈 YENİ
];
```

**S6. Maddeler arasındaki ayıracı değiştir (· yerine |)**
```jsx
const highlights = useMemo(() => HIGHLIGHTS.join(' | '), []);   // 👈 ' · ' → ' | '
```

**S7. Linkin adresini ve yazısını değiştir**
```jsx
onPress={() => Linking.openURL('https://docs.expo.dev')}   // 👈 adres değişti
>
  Expo dokümantasyonuna git     {/* 👈 yazı değişti */}
</Text>
```

**S8. Sayacı büyük harf yerine normal yaz, harf aralığını kaldır**
```jsx
textTransform: 'none',   // 👈 'uppercase' → 'none'
letterSpacing: 0,        // 👈 2 → 0 (veya satırı sil)
```

**S9. Açıklamayı italik yap / linkin alt çizgisini kaldır**
```jsx
fontStyle: 'italic',            // 👈 açıklamanın style'ına ekle
textDecorationLine: 'none',     // 👈 linkte 'underline' → 'none'
```

**S10. Başlıktaki "React Native" kelimesi farklı renkte olsun (iç içe Text)**
```jsx
<Text style={{ fontSize: 30, fontWeight: '800', color: '#f8fafc' }} selectable>
  Live <Text style={{ color: '#22d3ee' }}>React Native</Text> Session   {/* 👈 ortadaki kelime ayrı Text */}
</Text>
```

**S11. Sayaç dakika:saniye formatında gösterilsin (ör. 1:05)**
```jsx
// highlights satırının ALTINA ekle:
const minutes = Math.floor(seconds / 60);               // 65 → 1
const secs = String(seconds % 60).padStart(2, '0');     // 65 → "05" (tek haneliyse başa 0 ekler)

// Sayaç Text'inde:
Elapsed time: {minutes}:{secs}     {/* 👈 {seconds}s yerine */}
```

**S12. Linke uzun basınca uyarı çıksın**
```jsx
import { Alert, Linking, SafeAreaView, Text, View } from 'react-native';   // 👈 Alert eklendi

// Link Text'ine onPress'in altına ekle:
onLongPress={() => Alert.alert('Bilgi', 'Bu link React Native dokümantasyonunu açar')}
```

**S13 (zor). Sayaç bir butonla durdurulup başlatılabilsin**
```jsx
import { useEffect, useMemo, useState } from 'react';
import { Button, Linking, SafeAreaView, Text, View } from 'react-native';   // 👈 Button eklendi

// component içinde:
const [seconds, setSeconds] = useState(0);
const [isRunning, setIsRunning] = useState(true);                 // 👈 YENİ: çalışıyor mu?

useEffect(() => {
  if (!isRunning) return;                                          // 👈 durmuşsa timer kurma
  const interval = setInterval(() => setSeconds((prev) => prev + 1), 1000);
  return () => clearInterval(interval);
}, [isRunning]);                                                   // 👈 [] → [isRunning]: değişince effect yeniden çalışır

// Sayaç Text'inin hemen ALTINA:
<Button
  title={isRunning ? 'Durdur' : 'Başlat'}                         // 👈 duruma göre yazı değişir
  onPress={() => setIsRunning((r) => !r)}                          // 👈 true ↔ false
/>
```
> Neden `[isRunning]`? `[]` olsaydı effect sadece ilk açılışta çalışırdı ve butona basmak timer'ı etkilemezdi. `isRunning` değişince eski timer cleanup ile silinir, yenisi kurulur (veya kurulmaz).

---

## 📦 Temel Kod 2 – Karakter Sayaçlı Bio Alanı

```jsx
import { useState } from 'react';
import { SafeAreaView, Text, TextInput, View } from 'react-native';

// En fazla kaç karakter yazılabilir (sabit)
const MAX_LENGTH = 120;

export default function BioField() {
  // Kullanıcının yazdığı metin
  const [bio, setBio] = useState('');
  // Kalan karakter = limit - yazılan uzunluk (state değil, her render'da HESAPLANIR)
  const remaining = MAX_LENGTH - bio.length;
  // Son 10 karaktere girildi mi? (true/false)
  const limitReached = remaining <= 10;

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View
        style={{
          flex: 1,
          justifyContent: 'center',
          padding: 24,
          gap: 12,
        }}
      >
        {/* Başlık */}
        <Text style={{ fontSize: 22, fontWeight: '700', color: '#0f172a' }}>
          Profile Summary
        </Text>

        {/* Beyaz kart: hafif gölgeli */}
        <View
          style={{
            borderRadius: 16,
            backgroundColor: '#fff',
            padding: 20,
            gap: 12,
            shadowColor: '#000',
            shadowOpacity: 0.05,
            shadowRadius: 10,
            elevation: 2,
          }}
        >
          {/* Çok satırlı yazı alanı; maxLength sınırı aşmayı engeller */}
          <TextInput
            multiline
            maxLength={MAX_LENGTH}
            value={bio}
            onChangeText={setBio}
            placeholder="Introduce yourself in a few sentences..."
            style={{
              minHeight: 120,
              borderWidth: 1,
              borderColor: '#d4d4d8',
              borderRadius: 10,
              padding: 14,
              textAlignVertical: 'top',
            }}
          />

          {/* Kalan karakter: limite yaklaşınca turuncu, değilse gri (ternary) */}
          <Text style={{ color: limitReached ? '#f97316' : '#475569' }}>
            Characters remaining: {remaining}
          </Text>
        </View>
      </View>
    </SafeAreaView>
  );
}
```

### Olası sorular – Kod 2

**S1. Karakter sınırını 200 yap**
```jsx
const MAX_LENGTH = 200;   // 👈 tek yerden değişir, maxLength ve hesap otomatik güncellenir
```

**S2. Uyarı rengi son 20 karakterde başlasın**
```jsx
const limitReached = remaining <= 20;   // 👈 10 → 20
```

**S3. Uyarı rengini kırmızı yap**
```jsx
<Text style={{ color: limitReached ? '#ef4444' : '#475569' }}>   {/* 👈 turuncu → kırmızı */}
```

**S4. Limite yaklaşınca yazı kalın da olsun**
```jsx
<Text
  style={{
    color: limitReached ? '#f97316' : '#475569',
    fontWeight: limitReached ? '700' : '400',      // 👈 YENİ: aynı ternary mantığı
  }}
>
```

**S5. "Yazılan: 35 / 120" formatında göster**
```jsx
<Text style={{ color: limitReached ? '#f97316' : '#475569' }}>
  Yazılan: {bio.length} / {MAX_LENGTH}      {/* 👈 remaining yerine */}
</Text>
```

**S6. Limite yaklaşınca altta ayrıca uyarı mesajı çıksın**
```jsx
{/* Kalan karakter Text'inin ALTINA ekle */}
{limitReached && (
  <Text style={{ color: '#ef4444' }}>Sınıra yaklaştın!</Text>   // 👈 && → true ise göster
)}
```

**S7. Başlığı, placeholder'ı ve yazı alanının yüksekliğini değiştir**
```jsx
Profil Özeti                                     {/* 👈 başlık */}
placeholder="Kendini birkaç cümleyle anlat..."   // 👈 placeholder
minHeight: 200,                                  // 👈 TextInput style'ında 120 → 200
```

**S8. Kartın arka planını ve gölgesini belirginleştir**
```jsx
backgroundColor: '#e0f2fe',   // 👈 '#fff' → açık mavi
shadowOpacity: 0.2,           // 👈 0.05 → 0.2
elevation: 6,                 // 👈 2 → 6 (Android)
```

**S9 (zor). Yazıyı temizleyen bir buton ekle**
```jsx
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';   // 👈 Button eklendi

{/* Kalan karakter Text'inin ALTINA */}
<Button title="Temizle" onPress={() => setBio('')} />   // 👈 state'i boş yazıya çevirir
```

---

## ⚠️ Sık hatalar
- `fontWeight` değeri **string** olarak yazılır: `'700'` doğru, `700` yanlış.
- Yazıyı doğrudan `<View>` içine yazmak hata verir, mutlaka `<Text>` ile sarmalanmalı.
- Yeni kullandığın her şeyi (`Alert`, `Button`, `Linking`) en üstteki `import { ... } from 'react-native'` satırına ekle.
- `Linking.openURL` Snack'in web önizlemesinde yeni sekme açar, telefonda tarayıcıyı açar.
