# 06 – ScrollView

`ScrollView`, ekrana sığmayan içeriği **kaydırılabilir** yapan kutudur. Normal `View` ekrandan taşan kısmı keser, `ScrollView` ise kaydırarak gösterir. Hem **dikey** (varsayılan) hem **yatay** (`horizontal`) kaydırma yapabilir.

> **ScrollView mı, FlatList mi?** ScrollView içindeki her şeyi **aynı anda** çizer. Az sayıda eleman (form, birkaç kart, menü) için uygundur. Yüzlerce elemanlı listelerde yavaşlar, o durumda `FlatList` kullanılır.

---

## 📜 ScrollView hızlı tablo

| Prop | Ne yapar | Örnek |
|---|---|---|
| `horizontal` | Yatay kaydırma (yazmazsan dikey) | `horizontal` |
| `contentContainerStyle` | **İçerideki** kutunun stili (padding, gap, arka plan) | `contentContainerStyle={{ padding: 16, gap: 12 }}` |
| `style` | ScrollView'ın **kendisinin** stili (dış çerçeve) | `style={{ flex: 1 }}` |
| `showsVerticalScrollIndicator` | Dikey kaydırma çubuğu görünsün mü | `{false}` → gizle |
| `showsHorizontalScrollIndicator` | Yatay kaydırma çubuğu görünsün mü | `{false}` → gizle |
| `pagingEnabled` | Ekran ekran (sayfa sayfa) kaysın | `pagingEnabled` |
| `snapToInterval` | Her X birimde bir dursun (kart kart kaydırma) | `snapToInterval={236}` |
| `scrollEnabled` | Kaydırma açık mı | `{false}` → kilitli |
| `onScroll` | Kaydırırken sürekli çalışır | `onScroll={handleScroll}` |

> **`style` ile `contentContainerStyle` farkı:** `gap`, `padding`, `alignItems` gibi **içerideki elemanları** etkileyen şeyler `contentContainerStyle`'a yazılır. `style`'a yazarsan beklediğin gibi çalışmaz.

> ⚠️ **Dikey ScrollView'ın kaydırması için sınırlı bir yüksekliği olmalı.** Ebeveyninde `flex: 1` yoksa ScrollView içeriği kadar uzar, ekrandan taşar ve kaydırma çalışmaz. Kaydırma çalışmıyorsa ilk kontrol edilecek yer burası.

---

## 📦 Temel Kod 1 – Sona Kadar Okunmadan Onaylanamayan Sözleşme

```jsx
import { useRef, useState } from 'react';
import { Button, SafeAreaView, ScrollView, Text, View } from 'react-native';

// Uzun sözleşme metni: 'Terms of service ' yazısı 120 kez tekrar + son cümle
// .repeat(120) → yazıyı 120 kez art arda ekler; \n\n → iki satır boşluk
const TERMS = `${'Terms of service '.repeat(120)}\n\nPlease confirm that you have read the agreement.`;

export default function TermsAgreement() {
  const [accepted, setAccepted] = useState(false);       // kullanıcı onayladı mı?
  const [reachedEnd, setReachedEnd] = useState(false);   // metnin sonuna kadar kaydırdı mı?
  // useRef: ekranı yeniden çizdirmeden bir şey saklayan kutu
  const scrollViewRef = useRef(null);                    // ScrollView'ın kendisine erişmek için (ona "şuraya kay" komutu vermek)
  const requestedPosition = useRef(false);               // "başa kaydırma bir kez yapıldı mı?" bilgisi

  // Kaydırma her hareket ettiğinde çalışır: sona gelindi mi?
  const handleScroll = (event) => {
    const { layoutMeasurement, contentOffset, contentSize } =
      event.nativeEvent;
    // görünen yükseklik + kaydırılan miktar >= toplam içerik yüksekliği (20 tolerans)
    const scrolledToBottom =
      layoutMeasurement.height + contentOffset.y >= contentSize.height - 20;
    if (scrolledToBottom && !reachedEnd) {
      setReachedEnd(true);                               // sona gelindi → buton açılsın
    }
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#e2e8f0' }}>
      <View style={{ flex: 1, padding: 24, gap: 16 }}>
        {/* Başlık */}
        <Text style={{ fontSize: 22, fontWeight: '700', color: '#0f172a' }}>
          Terms of Service
        </Text>

        {/* KAYDIRILABİLİR METİN KUTUSU */}
        <ScrollView
          ref={scrollViewRef}                            // bu ScrollView'ı scrollViewRef'e bağla
          onScroll={handleScroll}                        // kaydırdıkça sona gelindi mi kontrol et
          scrollEventThrottle={16}                       // onScroll'u ~saniyede 60 kez çalıştır (akıcı)
          contentContainerStyle={{
            padding: 18,
            backgroundColor: '#fff',
            borderRadius: 16,
            gap: 12,
            minHeight: 400,
          }}
          // İçerik ilk yüklendiğinde bir kez en başa kaydır
          onContentSizeChange={() => {
            if (!requestedPosition.current && scrollViewRef.current) {
              requestedPosition.current = true;
              scrollViewRef.current.scrollTo({ y: 0, animated: false });
            }
          }}
          nestedScrollEnabled                            // Android: iç içe kaydırma
        >
          <Text style={{ lineHeight: 22, color: '#334155' }}>{TERMS}</Text>
        </ScrollView>

        {/* ONAY BUTONU */}
        <View style={{ marginTop: 8, marginBottom: 32 }}>
          <Button
            // İç içe ternary: sona gelinmediyse "kaydır", gelindiyse: onaylandıysa "Accepted", değilse "Accept terms"
            title={
              reachedEnd
                ? accepted
                  ? 'Accepted'
                  : 'Accept terms'
                : 'Scroll to the bottom to continue'
            }
            disabled={!reachedEnd || accepted}           // sona gelinmediyse VEYA zaten onaylandıysa pasif
            onPress={() => setAccepted(true)}
            color={accepted ? '#22c55e' : '#2563eb'}     // onaylanınca yeşil
          />
        </View>
      </View>
    </SafeAreaView>
  );
}
```

> **`useRef` nedir?** `useState` gibi bir değer saklar, ama değeri değişince ekran **yeniden çizilmez**. İki kullanımı var:
> 1. **Bir component'e erişmek:** `ref={scrollViewRef}` yazınca `scrollViewRef.current` o ScrollView'ın kendisi olur. Böylece `scrollViewRef.current.scrollTo(...)` ile ona komut verebilirsin.
> 2. **Ekranda görünmeyen bir bilgiyi saklamak:** `requestedPosition.current` gibi. Değeri `.current` ile okunur ve değiştirilir.

> **Sona gelindi mi hesabı:**
> - `layoutMeasurement.height` → kutunun ekranda **görünen** yüksekliği
> - `contentOffset.y` → şu ana kadar **ne kadar aşağı kaydırıldığı**
> - `contentSize.height` → içeriğin **toplam** yüksekliği
>
> Görünen + kaydırılan, toplama ulaştıysa en alttasın demektir. `- 20` küçük bir tolerans: tam piksel piksel sona gelmek gerekmesin.

### Olası sorular – Kod 1

**S1. Metni kısalt / uzat**
```jsx
const TERMS = `${'Terms of service '.repeat(40)}\n\nPlease confirm that you have read the agreement.`;   // 👈 120 → 40
```

**S2. Kendi sözleşme metnini yaz (birkaç paragraf)**
```jsx
// TERMS satırının YERİNE (component DIŞINDA). Backtick ` içinde Enter'la alt satıra geçilebilir:
const TERMS = `1. Kullanım Koşulları
Bu uygulamayı kullanarak aşağıdaki koşulları kabul etmiş olursunuz.

2. Gizlilik
Kişisel verileriniz üçüncü kişilerle paylaşılmaz.

3. Sorumluluk
...

Lütfen sözleşmeyi okuduğunuzu onaylayın.`;
```
> ⚠️ Metin kısa olursa kaydırmaya gerek kalmaz ve `onScroll` hiç çalışmaz. Bu durumda buton hiç açılmaz. Metnin kutudan uzun olduğundan emin ol (gerekirse paragrafları çoğalt).

**S3. Buton yazılarını Türkçe yap**
```jsx
title={
  reachedEnd
    ? accepted
      ? 'Onaylandı'                          // 👈
      : 'Koşulları kabul et'                 // 👈
    : 'Devam etmek için sona kaydır'         // 👈
}
```

**S4. Metin kutusunun rengini ve köşelerini değiştir**
```jsx
contentContainerStyle={{
  padding: 18,
  backgroundColor: '#fef9c3',     // 👈 beyaz → açık sarı
  borderRadius: 0,                // 👈 16 → 0 (köşeli)
  gap: 12,
  minHeight: 400,
}}
```

**S5. Onaylanınca altta teşekkür mesajı çıksın**
```jsx
{/* Butonu saran View'ın ALTINA */}
{accepted && (
  <Text style={{ color: '#16a34a', fontWeight: '600', textAlign: 'center' }}>
    Teşekkürler, sözleşme onaylandı!
  </Text>
)}
```

**S6. "Başa dön" ve "Sona git" butonları ekle**
```jsx
{/* Başlığın ALTINA, ScrollView'ın ÜSTÜNE */}
<View style={{ flexDirection: 'row', gap: 12 }}>
  <Button
    title="Başa dön"
    onPress={() => scrollViewRef.current.scrollTo({ y: 0, animated: true })}   // 👈 ref ile en üste kaydır
  />
  <Button
    title="Sona git"
    onPress={() => scrollViewRef.current.scrollToEnd({ animated: true })}       // 👈 en alta kaydır
  />
</View>
```
> `animated: true` → kayarak gider, `false` → anında atlar. Hocanın kodunda `ref` zaten hazır olduğu için sadece butonları eklemek yeterli.

**S7. Kaydırma çubuğunu gizle**
```jsx
<ScrollView
  ref={scrollViewRef}
  showsVerticalScrollIndicator={false}   // 👈 YENİ
  ...
```

**S8. Sona yaklaşma toleransını büyüt (sona 100 kala açılsın)**
```jsx
layoutMeasurement.height + contentOffset.y >= contentSize.height - 100;   // 👈 20 → 100
```

**S9. Onay butonunun rengini değiştir**
```jsx
color={accepted ? '#16a34a' : '#7c3aed'}   // 👈 onaysızken mor, onaylıyken koyu yeşil
```

**S10 (zor). Ne kadar okunduğunu yüzde olarak göster**
```jsx
const [percent, setPercent] = useState(0);                           // 👈 YENİ state (component İÇİNDE)

// handleScroll'un İÇİNE, en alta ekle:
const maxScroll = contentSize.height - layoutMeasurement.height;     // kaydırılabilecek toplam mesafe
const p = Math.min(100, Math.round((contentOffset.y / maxScroll) * 100));   // kaydırılan / toplam × 100
setPercent(p);

{/* Başlığın ALTINA */}
<Text style={{ color: '#475569' }}>Okunan: %{percent}</Text>
```
> İstersen View dosyasındaki ilerleme çubuğunu da ekleyebilirsin: dolgu View'ına `width: `${percent}%`` verirsin.

---

## 📦 Temel Kod 2 – Yatay Kaydırılan Haftalık Program

```jsx
import { SafeAreaView, ScrollView, Text, View } from 'react-native';

// Kartlarda gösterilecek veriler: her gün bir nesne (component DIŞINDA)
const WEEKLY_PLAN = [
  { day: 'Monday', topic: 'Kickoff Session', duration: '40 min' },
  { day: 'Tuesday', topic: 'Core Components', duration: '60 min' },
  { day: 'Wednesday', topic: 'Navigation', duration: '50 min' },
  { day: 'Thursday', topic: 'State Management', duration: '55 min' },
  { day: 'Friday', topic: 'Performance', duration: '45 min' },
];

export default function WeeklySchedule() {
  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a' }}>
      <View style={{ paddingVertical: 32 }}>
        {/* Başlık */}
        <Text
          style={{
            fontSize: 22,
            fontWeight: '700',
            marginLeft: 24,
            color: '#f8fafc',
          }}
        >
          Weekly Study Plan
        </Text>

        {/* YATAY SCROLLVIEW: kartlar yan yana dizilir, sağa-sola kaydırılır */}
        <ScrollView
          horizontal                                  // yatay kaydırma
          showsHorizontalScrollIndicator={false}      // alttaki kaydırma çubuğu gizli
          contentContainerStyle={{ paddingHorizontal: 24, paddingTop: 16, gap: 16 }}   // kartlar arası 16 boşluk
        >
          {/* Dizideki her gün için bir KART */}
          {WEEKLY_PLAN.map((item) => (
            <View
              key={item.day}                          // gün adları benzersiz → key
              style={{
                width: 220,                           // sabit genişlik (yatay kaydırmada şart)
                padding: 20,
                borderRadius: 20,
                backgroundColor: '#1f2937',
                justifyContent: 'center',
                gap: 10,
              }}
            >
              {/* Gün */}
              <Text style={{ color: '#cbd5f5', fontSize: 16 }}>
                {item.day}
              </Text>
              {/* Konu (büyük, kalın) */}
              <Text
                style={{ color: '#f1f5f9', fontWeight: '600', fontSize: 20 }}
              >
                {item.topic}
              </Text>
              {/* Süre */}
              <Text style={{ color: '#94a3b8' }}>{item.duration}</Text>
            </View>
          ))}
        </ScrollView>
      </View>
    </SafeAreaView>
  );
}
```

> **Mantık:** veri (`WEEKLY_PLAN` dizisi) ile görünüm (kart) ayrı. `map` dizideki her nesne için aynı kart şablonunu üretir ve `item.day`, `item.topic` gibi alanları yerine koyar. Bu yüzden **yeni kart eklemek için diziye bir satır eklemek**, **karta yeni bilgi eklemek için de her nesneye yeni bir alan + kartta yeni bir `<Text>` eklemek** yeterli.

### Olası sorular – Kod 2

**S1. Yeni gün ekle (ör. Cumartesi)**
```jsx
const WEEKLY_PLAN = [
  { day: 'Monday', topic: 'Kickoff Session', duration: '40 min' },
  // ...
  { day: 'Friday', topic: 'Performance', duration: '45 min' },
  { day: 'Saturday', topic: 'Review', duration: '30 min' },     // 👈 YENİ (day benzersiz olmalı, key o)
];
```

**S2. Kartların rengini, genişliğini ve köşelerini değiştir**
```jsx
width: 260,                    // 👈 220 → 260
borderRadius: 8,               // 👈 20 → 8
backgroundColor: '#1e3a8a',    // 👈 gri → lacivert
```

**S3. ⭐ Her karta İNTERNETTEN görsel ekle**
```jsx
import { Image, SafeAreaView, ScrollView, Text, View } from 'react-native';   // 👈 Image eklendi

// 1) Her nesneye image alanı ekle (component DIŞINDA, dizide):
const WEEKLY_PLAN = [
  { day: 'Monday', topic: 'Kickoff Session', duration: '40 min', image: 'https://picsum.photos/seed/mon/400/250' },
  { day: 'Tuesday', topic: 'Core Components', duration: '60 min', image: 'https://picsum.photos/seed/tue/400/250' },
  { day: 'Wednesday', topic: 'Navigation', duration: '50 min', image: 'https://picsum.photos/seed/wed/400/250' },
  { day: 'Thursday', topic: 'State Management', duration: '55 min', image: 'https://picsum.photos/seed/thu/400/250' },
  { day: 'Friday', topic: 'Performance', duration: '45 min', image: 'https://picsum.photos/seed/fri/400/250' },
];

// 2) Kartın İÇİNE, gün Text'inin ÜSTÜNE:
<Image
  source={{ uri: item.image }}                   // 👈 her kart kendi görselini alır
  style={{ width: '100%', height: 120, borderRadius: 12 }}   // 👈 uri'de width/height ŞART
/>
```
> Görsel kartın en üstünde, köşeleri yuvarlak olarak durur. Kartın kenarına tam yapışsın istenirse: kartın `padding`'ini kaldırıp `overflow: 'hidden'` ekle, yazıları ayrı bir `<View style={{ padding: 20, gap: 10 }}>` içine al.

**S4. ⭐ Her karta KENDİ (yerel) görselini ekle**
```jsx
// Önce görselleri Snack'te assets klasörüne yükle (Image dosyasındaki adımlar).

// 1) Dizide image alanına require yaz (component DIŞINDA):
const WEEKLY_PLAN = [
  { day: 'Monday', topic: 'Kickoff Session', duration: '40 min', image: require('./assets/monday.png') },
  { day: 'Tuesday', topic: 'Core Components', duration: '60 min', image: require('./assets/tuesday.png') },
  // ... her gün için kendi dosya adı
];

// 2) Kartın İÇİNDE:
<Image
  source={item.image}                            // 👈 require zaten source formatında → { uri: } YOK, tek süslü parantez
  style={{ width: '100%', height: 120, borderRadius: 12 }}
/>
```
> Hoca tek bir görsel verirse ve "hepsine bu görseli koy" derse diziye dokunma, kartta doğrudan yaz: `source={require('./assets/logo.png')}`.

**S5. Kartlara ikon / emoji ekle (görselden daha hızlı alternatif)**
```jsx
{ day: 'Monday', topic: 'Kickoff Session', duration: '40 min', icon: '🚀' },   // 👈 her nesneye icon alanı

<Text style={{ fontSize: 32 }}>{item.icon}</Text>                              // 👈 kartın en üstüne
```

**S6. Yatay yerine dikey liste yap**
```jsx
<SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a' }}>
  <View style={{ flex: 1, paddingVertical: 32 }}>            {/* 👈 flex: 1 EKLENDİ, yoksa dikey kaydırma çalışmaz */}
    ...
    <ScrollView
      // 👈 horizontal SİLİNDİ
      showsVerticalScrollIndicator={false}                  // 👈 Horizontal → Vertical
      contentContainerStyle={{ paddingHorizontal: 24, paddingTop: 16, gap: 16 }}
    >
      {WEEKLY_PLAN.map((item) => (
        <View
          key={item.day}
          style={{
            // 👈 width: 220 SİLİNDİ → kart tüm genişliği kaplar
            padding: 20,
            ...
```

**S7. Kartlar kaydırınca tam karta otursun (yarıda kalmasın)**
```jsx
<ScrollView
  horizontal
  snapToInterval={236}               // 👈 kart genişliği (220) + gap (16) = 236 → her kartta durur
  decelerationRate="fast"            // 👈 hızlı dursun
  ...
```
> Kart genişliğini değiştirirsen bu sayıyı da güncelle: genişlik + gap.

**S8. Karta tıklayınca konuyu uyarıda göster**
```jsx
import { Alert, SafeAreaView, ScrollView, Text, TouchableOpacity, View } from 'react-native';   // 👈

{WEEKLY_PLAN.map((item) => (
  <TouchableOpacity                                            // 👈 View → TouchableOpacity
    key={item.day}
    onPress={() => Alert.alert(item.day, `${item.topic} – ${item.duration}`)}   // 👈
    style={{
      width: 220,
      padding: 20,
      borderRadius: 20,
      backgroundColor: '#1f2937',
      justifyContent: 'center',
      gap: 10,
    }}
  >
    {/* içerik aynı */}
  </TouchableOpacity>                                          // 👈 kapanış etiketi de değişir!
))}
```

**S9 (zor). Tıklanan kart seçili olsun (kenarlıkla vurgulansın)**
```jsx
import { useState } from 'react';                                     // 👈 EKLENDİ
import { SafeAreaView, ScrollView, Text, TouchableOpacity, View } from 'react-native';

// component İÇİNDE, return'den ÖNCE:
const [selectedDay, setSelectedDay] = useState(null);                 // 👈 seçili gün (başta yok)

{WEEKLY_PLAN.map((item) => {
  const isActive = item.day === selectedDay;                          // 👈 bu kart seçili mi?
  return (
    <TouchableOpacity
      key={item.day}
      onPress={() => setSelectedDay(item.day)}                        // 👈 tıklananı seç
      style={{
        width: 220,
        padding: 20,
        borderRadius: 20,
        backgroundColor: '#1f2937',
        justifyContent: 'center',
        gap: 10,
        borderWidth: 2,                                               // 👈
        borderColor: isActive ? '#38bdf8' : 'transparent',            // 👈 seçiliyse mavi kenarlık
      }}
    >
      <Text style={{ color: '#cbd5f5', fontSize: 16 }}>{item.day}</Text>
      <Text style={{ color: '#f1f5f9', fontWeight: '600', fontSize: 20 }}>{item.topic}</Text>
      <Text style={{ color: '#94a3b8' }}>{item.duration}</Text>
    </TouchableOpacity>
  );
})}
```
> `map((item) => ( ... ))` → `map((item) => { ... return ( ... ); })` oldu: içeride `const isActive` hesaplamak için süslü parantez ve `return` gerekti. Image dosyasındaki resizeMode butonlarıyla aynı yapı.

**S10. Gün isimlerini ve konuları Türkçe yap**
```jsx
{ day: 'Pazartesi', topic: 'Açılış Dersi', duration: '40 dk' },   // 👈 sadece dizideki yazılar değişir
```

**S11. Başlığın altına "5 gün" gibi toplam sayı yaz**
```jsx
<Text style={{ color: '#94a3b8', marginLeft: 24 }}>
  {WEEKLY_PLAN.length} gün                                  {/* 👈 dizinin uzunluğu */}
</Text>
```

---

## ⚠️ Sık hatalar
- Dikey ScrollView kaymıyor → ebeveyn View'da `flex: 1` var mı kontrol et.
- `gap` / `padding` / `alignItems`'ı `style`'a yazmak → içerideki elemanları etkilemez. `contentContainerStyle`'a yaz.
- Yatay ScrollView'da kartlara `width` vermemek → kartlar ya çok daralır ya da ekran genişliğini alır.
- `View`'ı `TouchableOpacity`'ye çevirirken sadece açılış etiketini değiştirmek → kapanış `</View>` da `</TouchableOpacity>` olmalı.
- `map` içinde `key` unutmak → sarı uyarı çıkar.
- Uzak görselde (`uri`) `width/height` vermemek → görsel görünmez. Yerel görselde (`require`) `{ uri: }` yazmamak.
