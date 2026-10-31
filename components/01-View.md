# 01 – View

`View`, React Native'deki temel kutu (container) component'idir. Web'deki `<div>`'in karşılığıdır. Yerleşim tamamen **flexbox** ile yapılır ve varsayılan yön **`column`** (alt alta) şeklindedir.

> Snack'te kullanım: aşağıdaki temel kodu `App.js`'in içeriğinin tamamının yerine yapıştır. Sorular için sadece belirtilen satırları değiştir.

---

## 🧩 Flexbox hızlı tablo

| Özellik | Ne yapar | Değerler |
|---|---|---|
| `flexDirection` | Çocukları hangi yönde dizer | `'column'` (varsayılan, alt alta), `'row'` (yan yana) |
| `justifyContent` | **Ana eksende** hizalar (column'da dikey, row'da yatay) | `'flex-start'`, `'center'`, `'flex-end'`, `'space-between'`, `'space-around'`, `'space-evenly'` |
| `alignItems` | **Çapraz eksende** hizalar (column'da yatay, row'da dikey) | `'flex-start'`, `'center'`, `'flex-end'`, `'stretch'` |
| `flex: 1` | Bulunabildiği tüm boşluğu kaplar | sayı |
| `gap` | Çocuklar arasına boşluk koyar | sayı |
| `flexWrap` | Sığmayanları alt satıra geçirir | `'wrap'`, `'nowrap'` |

---

## 📦 Temel Kod 1 – Dashboard Kartı

```jsx
import { SafeAreaView, View, Text } from 'react-native';

export default function DashboardCard() {
  return (
    // En dış katman: tüm ekranı kaplar (flex: 1), koyu arka plan
    <SafeAreaView style={{ flex: 1, backgroundColor: '#020617' }}>
      {/* Kartı dikeyde ortalayan kutu */}
      <View
        style={{
          flex: 1,
          justifyContent: 'center',
          padding: 24,
        }}
      >
        {/* KART: koyu lacivert, yuvarlak köşeli */}
        <View
          style={{
            padding: 24,
            borderRadius: 20,
            backgroundColor: '#0f172a',
            gap: 20,
          }}
        >
          {/* 1. satır: başlık solda, yüzde sağda (row + space-between) */}
          <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
            <Text style={{ color: '#e2e8f0', fontSize: 18, fontWeight: '600' }}>
              Daily Lesson Progress
            </Text>
            <Text style={{ color: '#38bdf8', fontWeight: '600' }}>+12%</Text>
          </View>

          {/* 2. satır: İLERLEME ÇUBUĞU – dıştaki gri zemin */}
          <View
            style={{
              height: 8,
              borderRadius: 8,
              backgroundColor: '#1e293b',
            }}
          >
            {/* içteki mavi dolgu: width yüzdesi ne kadar dolu olduğunu belirler */}
            <View
              style={{
                width: '65%',
                height: '100%',
                borderRadius: 8,
                backgroundColor: '#38bdf8',
              }}
            />
          </View>

          {/* 3. satır: açıklama solda, sayı sağda */}
          <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
            <Text style={{ color: '#94a3b8' }}>Lessons completed</Text>
            <Text style={{ color: '#e2e8f0', fontWeight: '600' }}>13 / 20</Text>
          </View>
        </View>
      </View>
    </SafeAreaView>
  );
}
```

### Olası sorular – Kod 1

**S1. Ekranın arka plan rengini değiştir (ör. beyaz)**
```jsx
<SafeAreaView style={{ flex: 1, backgroundColor: '#ffffff' }}>   // 👈 '#020617' → '#ffffff'
```

**S2. Kartın rengini değiştir (ör. mor)**
```jsx
backgroundColor: '#4c1d95',   // 👈 KART View'ındaki '#0f172a' değişti
```

**S3. İlerleme çubuğunu %80 yap ve sayıyı güncelle**
```jsx
width: '80%',   // 👈 içteki mavi View'da '65%' → '80%'
...
<Text style={{ color: '#e2e8f0', fontWeight: '600' }}>16 / 20</Text>   // 👈 13 → 16
```

**S4. İlerleme çubuğunu yeşil yap**
```jsx
backgroundColor: '#22c55e',   // 👈 içteki dolgu View'ında '#38bdf8' → yeşil
```

**S5. Çubuğu daha kalın yap**
```jsx
height: 16,   // 👈 DIŞTAKİ gri View'da 8 → 16 (içteki zaten height: '100%')
```

**S6. Karta kenarlık (border) ekle**
```jsx
<View
  style={{
    padding: 24,
    borderRadius: 20,
    backgroundColor: '#0f172a',
    gap: 20,
    borderWidth: 2,           // 👈 YENİ: kenarlık kalınlığı
    borderColor: '#38bdf8',   // 👈 YENİ: kenarlık rengi
  }}
>
```

**S7. Karta gölge ekle**
```jsx
// KART View'ının style'ına ekle:
shadowColor: '#000',                      // iOS gölge rengi
shadowOffset: { width: 0, height: 4 },    // iOS gölge yönü
shadowOpacity: 0.3,                       // iOS gölge koyuluğu
shadowRadius: 8,                          // iOS gölge yayılması
elevation: 6,                             // ANDROID gölgesi (tek satır yeter)
```

**S8. Karta yeni bir satır ekle (ör. "Remaining lessons: 7")**
```jsx
{/* 3. satırın hemen ALTINA, kartın kapanış </View>'ından ÖNCE yapıştır */}
<View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
  <Text style={{ color: '#94a3b8' }}>Remaining lessons</Text>
  <Text style={{ color: '#e2e8f0', fontWeight: '600' }}>7</Text>
</View>
```

**S9. Kartı ekranın ortası yerine en üstüne / en altına al**
```jsx
// Kartı ortalayan View'da:
justifyContent: 'flex-start',   // 👈 üst   ('flex-end' → alt, 'center' → orta)
```

**S10. Başlık ve yüzdeyi yan yana değil alt alta diz**
```jsx
<View style={{ flexDirection: 'column' }}>   // 👈 'row' → 'column', space-between'ı da sil
```

**S11 (zor). Butona basınca ilerleme çubuğu %10 artsın**
```jsx
import { useState } from 'react';                                    // 👈 YENİ
import { SafeAreaView, View, Text, Button } from 'react-native';     // 👈 Button eklendi

export default function DashboardCard() {
  const [progress, setProgress] = useState(65);                      // 👈 YENİ: yüzde state'te

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#020617' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24 }}>
        <View style={{ padding: 24, borderRadius: 20, backgroundColor: '#0f172a', gap: 20 }}>
          <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
            <Text style={{ color: '#e2e8f0', fontSize: 18, fontWeight: '600' }}>
              Daily Lesson Progress
            </Text>
            <Text style={{ color: '#38bdf8', fontWeight: '600' }}>%{progress}</Text>  {/* 👈 state gösteriliyor */}
          </View>
          <View style={{ height: 8, borderRadius: 8, backgroundColor: '#1e293b' }}>
            <View
              style={{
                width: `${progress}%`,        // 👈 sabit '65%' yerine state (backtick ` kullan!)
                height: '100%',
                borderRadius: 8,
                backgroundColor: '#38bdf8',
              }}
            />
          </View>
          <Button
            title="+10%"
            onPress={() => setProgress(p => Math.min(p + 10, 100))}  // 👈 100'ü geçmesin
          />
        </View>
      </View>
    </SafeAreaView>
  );
}
```

---

## 📦 Temel Kod 2 – Responsive Grid (onLayout)

```jsx
import { useState } from 'react';
import { SafeAreaView, View, Text } from 'react-native';

// Kartlarda gösterilecek ders isimleri (component DIŞINDA sabit dizi)
const COURSES = [
  'Core Components',
  'State Management',
  'Navigation',
  'Performance',
  'Testing',
  'Deployment',
];

export default function ResponsiveGrid() {
  // Bir satırda kaç kart olacağı – ekran genişliğine göre değişir
  const [cardsPerRow, setCardsPerRow] = useState(2);

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View
        style={{ flex: 1, padding: 24 }}
        // onLayout: View ekrana yerleşince genişliğini verir
        onLayout={(event) => {
          const { width } = event.nativeEvent.layout;
          setCardsPerRow(width > 500 ? 3 : 2); // geniş ekran → 3, dar ekran → 2
        }}
      >
        <Text style={{ fontSize: 22, fontWeight: '700', marginBottom: 16 }}>
          Course Topics
        </Text>

        {/* Kartları yan yana diz, sığmayanı alt satıra at */}
        <View
          style={{
            flexDirection: 'row',
            flexWrap: 'wrap',
            gap: 16,
          }}
        >
          {/* Dizideki her ders için bir kart üret */}
          {COURSES.map((title) => (
            <View
              key={title} // her elemana benzersiz key
              style={{
                width: `${100 / cardsPerRow - 4}%`, // 2 kart → %46, 3 kart → %29
                padding: 18,
                borderRadius: 16,
                backgroundColor: '#e2e8f0',
              }}
            >
              <Text style={{ fontWeight: '600', color: '#1f2937' }}>
                {title}
              </Text>
            </View>
          ))}
        </View>
      </View>
    </SafeAreaView>
  );
}
```

### Olası sorular – Kod 2

**S1. Listeye yeni bir ders ekle**
```jsx
const COURSES = [
  'Core Components',
  'State Management',
  'Navigation',
  'Performance',
  'Testing',
  'Deployment',
  'Animations',   // 👈 YENİ (virgüllere dikkat)
];
```

**S2. Bir satırda 3 (veya her zaman 1) kart göster**
```jsx
setCardsPerRow(width > 500 ? 4 : 3);   // 👈 dar ekranda 3, geniş ekranda 4
// Her zaman 1 kart (tam genişlik) için: setCardsPerRow(1);
```
> Not: `useState(2)` sadece başlangıç değeridir. `onLayout` hemen çalışıp değeri değiştirdiği için asıl değiştirilmesi gereken yer onLayout'taki sayılardır.

**S3. Kartların rengini değiştir**
```jsx
backgroundColor: '#bfdbfe',   // 👈 kart View'ında '#e2e8f0' → açık mavi
```

**S4. Kartlar sırayla farklı renklerde olsun**
```jsx
const COLORS = ['#fecaca', '#bbf7d0', '#bfdbfe'];   // 👈 YENİ: COURSES'ın altına ekle

{COURSES.map((title, index) => (                    // 👈 index parametresi eklendi
  <View
    key={title}
    style={{
      width: `${100 / cardsPerRow - 4}%`,
      padding: 18,
      borderRadius: 16,
      backgroundColor: COLORS[index % COLORS.length], // 👈 0,1,2,0,1,2... diye döner
    }}
  >
```

**S5. Kart içindeki yazıyı ortala**
```jsx
// Kart View'ının style'ına ekle:
alignItems: 'center',   // 👈 yatayda ortalar (View column olduğu için)
```

**S6. Kartlar arasındaki boşluğu artır**
```jsx
gap: 24,   // 👈 kartları saran View'da 16 → 24
```

**S7. Başlığı değiştir ve ortala**
```jsx
<Text style={{ fontSize: 22, fontWeight: '700', marginBottom: 16, textAlign: 'center' }}>  {/* 👈 textAlign */}
  Ders Konuları   {/* 👈 metin değişti */}
</Text>
```

---

## ⚠️ Sık hatalar
- Stil değerlerinde yazı (string) olanlar tırnak içinde yazılır: `'center'`, `'#fff'`, `'65%'`. Sayılar tırnaksız yazılır: `padding: 24`.
- Değişken içeren yüzde değerinde **backtick** kullanılır: `` `${progress}%` ``.
- Yeni bir component (`Button`, `Image`...) kullanırsan en üstteki `import` satırına eklemeyi unutma.
- Snack'te `SafeAreaView` için "deprecated" uyarısı çıkabilir. Bu sadece bir uyarıdır, kod çalışır.
