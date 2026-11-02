# 09 – SafeAreaView

`SafeAreaView`, içeriği telefonun **çentiği, Dynamic Island'ı, yuvarlak köşeleri ve alttaki çizgisi** gibi alanlardan uzak tutar. Bu alanlara otomatik olarak boşluk (padding) ekler, böylece yazılar ve butonlar ekranın kullanılamayan yerlerine taşmaz. Genelde ekranın **en dış** katmanında kullanılır (şimdiye kadarki tüm örneklerde olduğu gibi).

> 📝 **Definition (exam):** *SafeAreaView renders its content within the safe area boundaries of a device. It automatically adds padding so that content is not hidden behind the notch, status bar, rounded corners or the home indicator. It is usually placed at the root of a screen.*

---

## 🛡️ Two versions of SafeAreaView

| | `react-native` | `react-native-safe-area-context` |
|---|---|---|
| **Import** | `import { SafeAreaView } from 'react-native'` | `import { SafeAreaView } from 'react-native-safe-area-context'` |
| **Platform** | iOS only (no effect on Android) | iOS **and** Android |
| **Status** | Deprecated in newer React Native versions | **Recommended by Expo** |
| **Provider needed** | No | Yes, wrap the app in `<SafeAreaProvider>` |
| **Choose edges** | No | Yes, with `edges={['top', 'bottom']}` |
| **Read inset values** | No | Yes, with the `useSafeAreaInsets()` hook |

**Short answer:** *The built-in SafeAreaView from react-native only works on iOS. The react-native-safe-area-context package works on both platforms, lets you choose which edges to protect with the `edges` prop, and gives the exact inset values through the `useSafeAreaInsets` hook.*

> Snack'te `react-native` sürümünü kullanınca "deprecated" uyarısı çıkabilir, bu sadece uyarıdır, kod çalışır. `react-native-safe-area-context` import edince Snack bağımlılık eklemeyi önerebilir, eklemen yeterli.

---

## 🧱 SafeAreaView hızlı tablo

| Prop / Hook | Ne yapar | Örnek |
|---|---|---|
| `style` | Arka plan, padding vb. | `style={{ flex: 1, backgroundColor: '#0f172a' }}` |
| `edges` *(context)* | Hangi kenarlar korunsun | `edges={['top']}` → sadece üst |
| `useSafeAreaInsets()` *(context)* | Kenar boşluklarını sayı olarak verir | `insets.top`, `insets.bottom`, `insets.left`, `insets.right` |
| `<SafeAreaProvider>` *(context)* | Uygulamanın en dışını sarar, hook'un çalışması için **şart** | |
| `<StatusBar barStyle="..." />` | Üstteki saat/pil yazılarının rengi | `'light-content'` (koyu arka plan için beyaz yazı), `'dark-content'` (açık arka plan için siyah yazı) |

---

## 📦 Temel Kod 1 – Adım Adım Tanıtım (Onboarding) Ekranı

```jsx
import { useState } from 'react';
import {
  SafeAreaView,
  StatusBar,
  Text,
  TouchableOpacity,
  View,
} from 'react-native';

// Adımlar: her biri bir başlık + açıklama (component DIŞINDA)
const STEPS = [
  {
    title: 'Welcome to the React Native Course',
    description:
      'You will learn core components, navigation, and state management step by step.',
  },
  {
    title: 'Discover Core Components',
    description:
      'Practice building layouts with View, Text, Image, and more.',
  },
  {
    title: 'Kick Off with Expo',
    description:
      'Run `expo start` to launch the project and test on your device in real time.',
  },
];

export default function OnboardingScreen() {
  const [step, setStep] = useState(0);                 // şu anki adımın SIRASI (0, 1, 2)
  const lastStep = step === STEPS.length - 1;          // son adımda mıyız? (3 adım → son index 2)

  // "Continue" butonu: son adımdaysa başa dön, değilse bir sonraki adıma geç
  const handleNext = () => {
    if (lastStep) {
      setStep(0);
      return;                                          // fonksiyondan çık
    }
    setStep((prev) => prev + 1);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a' }}>
      {/* Üstteki saat/pil yazıları beyaz olsun (koyu arka plan) */}
      <StatusBar barStyle="light-content" />

      {/* İçerik: space-between → üst kısım yukarıda, buton en altta */}
      <View style={{ flex: 1, justifyContent: 'space-between', padding: 24 }}>
        {/* ÜST KISIM: adım bilgisi + başlık + açıklama */}
        <View style={{ gap: 16 }}>
          {/* "Step 1 / 3" (step 0'dan başladığı için +1) */}
          <Text style={{ color: '#38bdf8', fontSize: 16, fontWeight: '600' }}>
            Step {step + 1} / {STEPS.length}
          </Text>
          {/* Şu anki adımın başlığı: STEPS[step] → dizinin step'inci elemanı */}
          <Text style={{ color: '#f8fafc', fontSize: 30, fontWeight: '700' }}>
            {STEPS[step].title}
          </Text>
          {/* Şu anki adımın açıklaması */}
          <Text style={{ color: '#cbd5f5', lineHeight: 22 }}>
            {STEPS[step].description}
          </Text>
        </View>

        {/* ALT KISIM: son adım mesajı + buton */}
        <View style={{ gap: 12 }}>
          {/* Sadece son adımda görünen mesaj */}
          {lastStep && (
            <Text style={{ color: '#94a3b8', textAlign: 'center' }}>
              You’re ready! Restart the flow any time you need a refresher.
            </Text>
          )}
          {/* BUTON: son adımda yeşil "Restart", diğerlerinde mavi "Continue" */}
          <TouchableOpacity
            onPress={handleNext}
            activeOpacity={0.8}
            style={{
              paddingVertical: 16,
              borderRadius: 12,
              backgroundColor: lastStep ? '#22c55e' : '#38bdf8',
              alignItems: 'center',
            }}
          >
            <Text style={{ color: '#0f172a', fontWeight: '700' }}>
              {lastStep ? 'Restart' : 'Continue'}
            </Text>
          </TouchableOpacity>
        </View>
      </View>
    </SafeAreaView>
  );
}
```

> **Mantık:** Ekranda tek bir adım gösteriliyor, hangisi olduğu `step` state'inde tutuluyor (0, 1, 2). `STEPS[step]` o adımın nesnesini verir. Butona basınca `step` 1 artar ve ekran yeni adımla yeniden çizilir.
> **`STEPS.length - 1` neden?** Dizi 3 elemanlı ama index'ler 0'dan başlar: 0, 1, 2. Son index her zaman `length - 1`'dir.

### Olası sorular – Kod 1

**S1. Yeni bir adım ekle**
```jsx
const STEPS = [
  // ... mevcut 3 adım ...
  {
    title: 'Build Your First App',                          // 👈 YENİ (dizinin sonuna, virgüle dikkat)
    description: 'Combine everything you learned in a small project.',
  },
];
```
> "Step 1 / 4", son adım kontrolü ve buton kendiliğinden güncellenir, çünkü hepsi `STEPS.length`'e bağlı.

**S2. Yazıları Türkçe yap**
```jsx
Adım {step + 1} / {STEPS.length}                            {/* 👈 */}
{lastStep ? 'Baştan başla' : 'Devam'}                       {/* 👈 buton */}
Hazırsın! İstediğin zaman baştan izleyebilirsin.            {/* 👈 son adım mesajı */}
// ve STEPS içindeki title / description metinleri
```

**S3. "Geri" butonu ekle (ilk adımda pasif)**
```jsx
// handleNext'in ALTINA (component İÇİNDE):
const handleBack = () => setStep((prev) => prev - 1);       // 👈 bir önceki adım

{/* ALT KISIM View'ında, TouchableOpacity'nin ÜSTÜNE */}
<TouchableOpacity
  onPress={handleBack}
  disabled={step === 0}                                     // 👈 ilk adımda geri gidilemez
  activeOpacity={0.8}
  style={{
    paddingVertical: 16,
    borderRadius: 12,
    borderWidth: 1,
    borderColor: '#38bdf8',
    alignItems: 'center',
    opacity: step === 0 ? 0.3 : 1,                          // 👈 pasifken soluk görünsün
  }}
>
  <Text style={{ color: '#38bdf8', fontWeight: '700' }}>Geri</Text>
</TouchableOpacity>
```

**S4. "Atla" butonu: doğrudan son adıma git**
```jsx
{/* ÜST KISIM View'ının en ÜSTÜNE */}
{!lastStep && (                                             // 👈 son adımda zaten gerek yok
  <TouchableOpacity onPress={() => setStep(STEPS.length - 1)} style={{ alignSelf: 'flex-end' }}>
    <Text style={{ color: '#94a3b8' }}>Atla</Text>
  </TouchableOpacity>
)}
```

**S5. İlerleme noktaları (● ○ ○)**
```jsx
{/* "Step 1 / 3" Text'inin YERİNE veya ALTINA */}
<View style={{ flexDirection: 'row', gap: 8 }}>
  {STEPS.map((item, index) => (
    <View
      key={index}
      style={{
        width: index === step ? 24 : 8,                     // 👈 aktif nokta uzun
        height: 8,
        borderRadius: 4,
        backgroundColor: index === step ? '#38bdf8' : '#334155',   // 👈 aktif mavi, diğerleri gri
      }}
    />
  ))}
</View>
```
> Burada `key={index}` kullanmak sorun değil, çünkü noktalar hiç sıralanmıyor/silinmiyor.

**S6. İlerleme çubuğu (View dosyasındaki çubuk)**
```jsx
{/* ÜST KISIM View'ının en ÜSTÜNE */}
<View style={{ height: 6, borderRadius: 3, backgroundColor: '#334155' }}>
  <View
    style={{
      width: `${((step + 1) / STEPS.length) * 100}%`,      // 👈 1/3 → %33, 2/3 → %66, 3/3 → %100
      height: '100%',
      borderRadius: 3,
      backgroundColor: '#38bdf8',
    }}
  />
</View>
```

**S7. Her adıma emoji / görsel ekle**
```jsx
// 1) Her adıma yeni alan (STEPS dizisinde):
{ title: 'Welcome to the React Native Course', description: '...', emoji: '👋' },
{ title: 'Discover Core Components', description: '...', emoji: '🧩' },
{ title: 'Kick Off with Expo', description: '...', emoji: '🚀' },

// 2) ÜST KISIM'da başlığın ÜSTÜNE:
<Text style={{ fontSize: 64 }}>{STEPS[step].emoji}</Text>
```
> Görsel için: alan `image: 'https://picsum.photos/seed/step1/400/250'` olur, ekranda `<Image source={{ uri: STEPS[step].image }} style={{ width: '100%', height: 200, borderRadius: 16 }} />` (Image'i import etmeyi unutma).

**S8. Son adımda "Restart" yerine "Başla" yazsın ve uyarı çıksın**
```jsx
import { Alert, SafeAreaView, StatusBar, Text, TouchableOpacity, View } from 'react-native';   // 👈 Alert

const handleNext = () => {
  if (lastStep) {
    Alert.alert('Hazırsın!', 'Kursa başlıyorsun.');        // 👈 setStep(0) yerine
    return;
  }
  setStep((prev) => prev + 1);
};

{lastStep ? 'Başla' : 'Continue'}                          {/* 👈 */}
```

**S9. Açık tema: arka plan beyaz, yazılar koyu**
```jsx
<SafeAreaView style={{ flex: 1, backgroundColor: '#ffffff' }}>     {/* 👈 */}
  <StatusBar barStyle="dark-content" />                             {/* 👈 açık arka planda saat/pil siyah olsun */}
  ...
  <Text style={{ color: '#0f172a', fontSize: 30, fontWeight: '700' }}>   {/* 👈 başlık: beyaz → koyu */}
  <Text style={{ color: '#475569', lineHeight: 22 }}>                    {/* 👈 açıklama */}
```

**S10. Buton renklerini değiştir**
```jsx
backgroundColor: lastStep ? '#f97316' : '#a855f7',   // 👈 son adımda turuncu, diğerlerinde mor
```

---

## 📦 Temel Kod 2 – SafeAreaProvider ile Çentiğe Uyumlu Başlık

```jsx
// SafeAreaView bu sefer react-native'den değil, ayrı paketten geliyor
import {
  SafeAreaProvider,
  SafeAreaView,
  useSafeAreaInsets,
} from 'react-native-safe-area-context';
import { Image, Text, View } from 'react-native';

// 1. COMPONENT: ekranın içeriği (hook burada kullanılıyor)
function HeaderContent() {
  const insets = useSafeAreaInsets();          // kenar boşlukları: insets.top, insets.bottom ...

  return (
    <View style={{ flex: 1, backgroundColor: '#020617' }}>
      {/* ÜST BAŞLIK: sadece üst kenar korunuyor */}
      <SafeAreaView
        edges={['top']}
        style={{
          backgroundColor: '#0f172a',
          paddingTop: insets.top,
        }}
      >
        {/* Avatar + yazılar yan yana */}
        <View
          style={{
            paddingHorizontal: 20,
            paddingBottom: 16,
            flexDirection: 'row',
            alignItems: 'center',
            gap: 12,
          }}
        >
          {/* Yuvarlak avatar (40x40, borderRadius yarısı) */}
          <Image
            source={{
              uri: 'https://avatars.githubusercontent.com/u/499550?v=4',
            }}
            style={{ width: 40, height: 40, borderRadius: 20 }}
          />
          {/* flex: 1 → kalan genişliği yazılar alsın */}
          <View style={{ flex: 1 }}>
            <Text style={{ color: '#f8fafc', fontWeight: '700', fontSize: 18 }}>
              Instructor Dashboard
            </Text>
            <Text style={{ color: '#94a3b8' }}>
              Welcome back! Review the highlights before you go live.
            </Text>
          </View>
        </View>
      </SafeAreaView>

      {/* SAYFA İÇERİĞİ */}
      <View style={{ flex: 1, padding: 24, gap: 16 }}>
        <Text style={{ color: '#f8fafc', fontSize: 20, fontWeight: '600' }}>
          Latest Lessons
        </Text>
        {/* Ders kartı */}
        <View
          style={{
            borderRadius: 16,
            padding: 20,
            backgroundColor: '#1f2937',
            gap: 12,
          }}
        >
          <Text style={{ color: '#38bdf8', fontWeight: '700' }}>
            1. Core Components
          </Text>
          <Text style={{ color: '#cbd5f5', lineHeight: 20 }}>
            Recap how to structure layouts with View, Text, and Image, backed by practical examples.
          </Text>
        </View>
      </View>
    </View>
  );
}

// 2. COMPONENT: ana component (export default). Tek işi her şeyi Provider ile sarmak
export default function NotchedHeaderDemo() {
  return (
    <SafeAreaProvider>
      <HeaderContent />
    </SafeAreaProvider>
  );
}
```

> **Neden iki ayrı component var?** `useSafeAreaInsets()` hook'u sadece `<SafeAreaProvider>`'ın **içinde** çalışır. Hook'u Provider'ı oluşturan component'te çağırırsan Provider henüz yoktur. Bu yüzden içerik ayrı bir component'e (`HeaderContent`) alınıp Provider'ın içine konuyor.
> **Bir dosyada birden fazla component:** sadece biri `export default` olur (Snack'in ekranda gösterdiği). Diğerleri normal `function` olarak yazılır ve JSX içinde `<HeaderContent />` şeklinde kullanılır.
> **Küçük not:** `edges={['top']}` zaten üste boşluk ekliyor, `paddingTop: insets.top` de ekliyor. Çentikli telefonlarda üst boşluk **iki kat** olabilir. İkisinden biri yeterli (S5).

### Olası sorular – Kod 2

**S1. Avatar görselini, başlığı ve alt yazıyı değiştir**
```jsx
uri: 'https://picsum.photos/seed/hoca/100',               // 👈 avatar adresi
Öğretmen Paneli                                           {/* 👈 başlık */}
Tekrar hoş geldin! Derse başlamadan özetlere göz at.      {/* 👈 alt yazı */}
```

**S2. Avatarı büyüt ve çerçeve ekle**
```jsx
style={{
  width: 56,
  height: 56,
  borderRadius: 28,                 // 👈 tam daire için genişliğin YARISI
  borderWidth: 2,                   // 👈 çerçeve
  borderColor: '#38bdf8',
}}
```

**S3. Başlık alanının rengini değiştir**
```jsx
backgroundColor: '#1e3a8a',         // 👈 SafeAreaView'ın style'ında '#0f172a' → lacivert
```

**S4. İkinci bir ders kartı ekle**
```jsx
{/* İlk ders kartı View'ının kapanışının ALTINA (sayfa içeriği View'ının İÇİNE) */}
<View style={{ borderRadius: 16, padding: 20, backgroundColor: '#1f2937', gap: 12 }}>
  <Text style={{ color: '#38bdf8', fontWeight: '700' }}>2. Navigation</Text>
  <Text style={{ color: '#cbd5f5', lineHeight: 20 }}>
    Learn how to move between screens with stack and tab navigators.
  </Text>
</View>
```
> Kart sayısı artacaksa (3+), ScrollView dosyasındaki gibi bir dizi + `map` kullanmak daha temiz.

**S5. Çift boşluk sorununu düzelt**
```jsx
<SafeAreaView
  edges={['top']}
  style={{
    backgroundColor: '#0f172a',
    // 👈 paddingTop: insets.top SİLİNDİ (edges zaten üst boşluğu ekliyor)
  }}
>
```
> Alternatif: SafeAreaView'ı normal `<View>` yapıp `paddingTop: insets.top`'u bırakmak. İkisi aynı sonucu verir.

**S6. Alt kenarı da koru (alttaki çizgiye içerik taşmasın)**
```jsx
// Sayfa içeriği View'ının style'ına:
<View style={{ flex: 1, padding: 24, gap: 16, paddingBottom: 24 + insets.bottom }}>   {/* 👈 normal boşluk + cihazın alt boşluğu */}
```

**S7. Başlığın sağına bildirim ikonu ekle**
```jsx
import { Ionicons } from '@expo/vector-icons';                // 👈 YENİ import

{/* Yazıları saran <View style={{ flex: 1 }}>'in kapanışının ALTINA (row'un İÇİNE) */}
<Ionicons name="notifications-outline" size={24} color="#f8fafc" />
```
> Yazıların View'ında `flex: 1` olduğu için ikon otomatik olarak en sağa itilir.

**S8. Kenar boşluk değerlerini ekranda göster (hook'u anlamak için)**
```jsx
{/* Sayfa içeriği View'ının İÇİNE */}
<Text style={{ color: '#94a3b8' }}>
  Üst: {insets.top} · Alt: {insets.bottom}          {/* 👈 çentikli telefonda ~47, web önizlemede 0 */}
</Text>
```

---

## ⚠️ Sık hatalar
- `SafeAreaView`'a `flex: 1` vermemek → ekranın tamamını kaplamaz, arka plan rengi sadece içerik kadar görünür.
- `useSafeAreaInsets()`'i `SafeAreaProvider` dışında çağırmak → değerler gelmez/hata verir.
- İki farklı paketten aynı isimli `SafeAreaView`'ı aynı dosyada import etmek → isim çakışması hatası. Birini seç.
- Snack'in **web** önizlemesinde çentik olmadığı için fark göremeyebilirsin. Farkı görmek için sağ üstten iOS / Android önizlemesini seç ya da Expo Go ile telefonda aç.
