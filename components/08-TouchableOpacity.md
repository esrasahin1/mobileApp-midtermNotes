# 08 – TouchableOpacity

`TouchableOpacity`, içine koyduğun **her şeyi** tıklanabilir yapan sarmalayıcıdır. Basıldığında içerik hafifçe soluklaşır (opacity azalır), kullanıcı bastığını anlar. Özel tasarımlı butonlar, tıklanabilir kartlar ve liste satırları bununla yapılır.

> 📝 **Definition (exam):** *TouchableOpacity is a wrapper component that makes any content pressable. When pressed, it reduces the opacity of its children to give visual feedback. Unlike `Button`, it accepts a `style` prop and can contain any components (text, icons, images), so it is used to build custom buttons and pressable cards.*

---

## 👆 Button vs TouchableOpacity

| | Button | TouchableOpacity |
|---|---|---|
| **Appearance** | Platform default look | Fully customizable |
| **`style` prop** | ❌ Not supported | ✅ Supported (padding, border, radius, background...) |
| **Content** | Only text via `title` | Any children: `Text`, `Image`, icons, `View` |
| **Customization** | Only `title`, `color`, `disabled` | Everything |
| **Press feedback** | Platform default | Opacity fade, adjustable with `activeOpacity` |
| **Use case** | Quick, simple actions | Custom buttons, pressable cards, list rows |

**Short answer:** *Button is simple but cannot be styled; it only supports `title`, `color` and `disabled`. TouchableOpacity can wrap any content and accepts a `style` prop, so it is used when a custom design is needed.*

---

## 🖐️ TouchableOpacity hızlı tablo

| Prop / Event | Ne yapar | Örnek |
|---|---|---|
| `onPress` | Basıp bırakınca çalışır | `onPress={() => setOpen(true)}` |
| `onLongPress` | Uzun basınca çalışır | `onLongPress={() => Alert.alert('Uzun bastın')}` |
| `onPressIn` / `onPressOut` | Parmak değdiği an / kalktığı an | |
| `activeOpacity` | Basılıyken ne kadar soluklaşsın (0 = görünmez, 1 = hiç değişmez, varsayılan 0.2) | `activeOpacity={0.7}` |
| `disabled` | Basılamaz yapar | `disabled={true}` |
| `style` | Görünüm (Button'da olmayan şey) | `style={{ padding: 12, borderRadius: 8 }}` |
| `hitSlop` | Dokunma alanını görünenden büyük yapar (küçük ikonlar için) | `hitSlop={10}` |

---

## ⭐ İkonlar (`@expo/vector-icons`)

Expo'da ve Snack'te **kurulum gerekmeden** hazır gelir.
```jsx
import { Ionicons } from '@expo/vector-icons';

<Ionicons name="heart" size={24} color="#ef4444" />
```
- `name` → ikonun adı · `size` → boyut · `color` → renk
- Tüm ikon adları için: **https://icons.expo.fyi** (sol üstten "Ionicons" filtrele, ada tıklayınca kopyalanır)
- Sık kullanılanlar: `star` / `star-outline`, `heart` / `heart-outline`, `bookmark` / `bookmark-outline`, `thumbs-up`, `trash`, `add-circle`, `chevron-down` / `chevron-up`, `checkmark-circle`, `share-social`, `home`, `settings`
- `-outline` ile biten = içi boş hali. Seçili/seçili değil durumları için çift olarak kullanılır.

---

## 📦 Temel Kod 1 – Favorilere Eklenebilen Kurs Kartı

```jsx
import { useState } from 'react';
import { SafeAreaView, Text, TouchableOpacity, View } from 'react-native';
import { Ionicons } from '@expo/vector-icons';     // hazır ikon paketi (Snack'te kurulu gelir)

export default function CourseCard() {
  const [isFavorite, setFavorite] = useState(false);   // favoride mi? (başta değil)
  const courseTitle = 'React Native Core Components';  // sabit başlık

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a' }}>
      {/* Kartı dikeyde ortalayan kutu */}
      <View
        style={{
          flex: 1,
          justifyContent: 'center',
          padding: 24,
        }}
      >
        {/* KART */}
        <View
          style={{
            padding: 24,
            borderRadius: 20,
            backgroundColor: '#1f2937',
            gap: 16,
          }}
        >
          {/* Kurs başlığı */}
          <Text style={{ color: '#f8fafc', fontSize: 20, fontWeight: '700' }}>
            {courseTitle}
          </Text>
          {/* Açıklama */}
          <Text style={{ color: '#cbd5f5', lineHeight: 20 }}>
            Add this course to your favorites for quick access and get notified
            when new lessons drop.
          </Text>

          {/* FAVORİ BUTONU: basınca isFavorite tersine döner */}
          <TouchableOpacity
            onPress={() => setFavorite((prev) => !prev)}   // true ↔ false
            activeOpacity={0.7}                             // basılıyken %70 görünür (hafif soluklaşır)
            style={{
              alignSelf: 'flex-start',                      // buton sadece içeriği kadar genişlikte, solda
              flexDirection: 'row',                         // ikon ve yazı yan yana
              gap: 8,
              paddingVertical: 10,
              paddingHorizontal: 16,
              borderRadius: 999,                            // hap şekli
              backgroundColor: isFavorite ? '#f59e0b' : '#334155',   // favorideyse turuncu, değilse gri
            }}
          >
            {/* İkon: favorideyse dolu yıldız, değilse içi boş yıldız */}
            <Ionicons
              name={isFavorite ? 'star' : 'star-outline'}
              size={18}
              color="#fef3c7"
            />
            {/* Yazı: duruma göre değişir */}
            <Text style={{ color: '#fef3c7', fontWeight: '600' }}>
              {isFavorite ? 'In favorites' : 'Add to favorites'}
            </Text>
          </TouchableOpacity>
        </View>
      </View>
    </SafeAreaView>
  );
}
```

> **Tek bir state, üç değişiklik:** `isFavorite` değişince arka plan rengi, ikon ve yazı aynı anda değişiyor. Hepsi aynı ternary kalıbıyla yapılıyor: `isFavorite ? A : B`. Yeni bir şeyin de duruma göre değişmesi istenirse aynı kalıp kullanılır.

### Olası sorular – Kod 1

**S1. Favori rengini değiştir (turuncu yerine kırmızı)**
```jsx
backgroundColor: isFavorite ? '#ef4444' : '#334155',   // 👈 '#f59e0b' → kırmızı
```

**S2. Buton yazılarını Türkçe yap**
```jsx
{isFavorite ? 'Favorilerde' : 'Favorilere ekle'}      // 👈
```

**S3. Yıldız yerine kalp ikonu kullan**
```jsx
<Ionicons
  name={isFavorite ? 'heart' : 'heart-outline'}         // 👈 star → heart
  size={18}
  color="#fef3c7"
/>
```

**S4. İkonu büyüt ve rengini değiştir**
```jsx
size={28}                                               // 👈 18 → 28
color={isFavorite ? '#ef4444' : '#fef3c7'}              // 👈 favorideyse kırmızı ikon
```

**S5. Basınca daha belirgin soluklaşsın**
```jsx
activeOpacity={0.3}     // 👈 0.7 → 0.3 (sayı küçüldükçe daha çok soluklaşır)
```

**S6. Butonu tam genişlik yap, içeriği ortala**
```jsx
style={{
  alignSelf: 'stretch',              // 👈 'flex-start' → 'stretch' (kart genişliğini kaplar)
  justifyContent: 'center',          // 👈 YENİ: ikon + yazıyı yatayda ortala (row olduğu için)
  flexDirection: 'row',
  ...
```
> Sadece butonu ortalamak (genişliği aynı kalsın) için: `alignSelf: 'center'`.

**S7. Kurs başlığını ve açıklamayı değiştir**
```jsx
const courseTitle = 'Mobil Uygulama Geliştirme';      // 👈 component İÇİNDEKİ sabit
...
<Text style={{ color: '#cbd5f5', lineHeight: 20 }}>
  Bu kursu favorilerine ekle, yeni ders gelince haberdar ol.   {/* 👈 */}
</Text>
```

**S8. Beğeni sayacı: basınca sayı artsın, tekrar basınca azalsın**
```jsx
const [likes, setLikes] = useState(42);                         // 👈 YENİ (component İÇİNDE)

<TouchableOpacity
  onPress={() => {
    setLikes((prev) => (isFavorite ? prev - 1 : prev + 1));     // 👈 favorideyse azalt, değilse artır
    setFavorite((prev) => !prev);
  }}
  ...
>
  <Ionicons name={isFavorite ? 'heart' : 'heart-outline'} size={18} color="#fef3c7" />
  <Text style={{ color: '#fef3c7', fontWeight: '600' }}>{likes}</Text>   {/* 👈 sayıyı göster */}
</TouchableOpacity>
```
> Sıra önemli: `setLikes` içindeki `isFavorite` henüz **eski** değer. Eskiden favorideyse şimdi çıkıyor demektir → azalt.

**S9. Uzun basınca uyarı çıksın**
```jsx
import { Alert, SafeAreaView, Text, TouchableOpacity, View } from 'react-native';   // 👈 Alert

<TouchableOpacity
  onPress={() => setFavorite((prev) => !prev)}
  onLongPress={() => Alert.alert('Bilgi', 'Favorilere eklemek için kısa bas')}   // 👈 YENİ
  ...
```

**S10. Favoriye eklenince kartın altında mesaj çıksın**
```jsx
{/* TouchableOpacity'nin kapanışının ALTINA (kartın İÇİNE) */}
{isFavorite && (
  <Text style={{ color: '#f59e0b' }}>Yeni dersler eklenince bildirim alacaksın.</Text>
)}
```

**S11. Yanına ikinci bir "Paylaş" butonu ekle**
```jsx
import { Alert, SafeAreaView, Text, TouchableOpacity, View } from 'react-native';   // 👈 Alert

{/* Favori TouchableOpacity'sini ve yenisini bir row View ile SAR */}
<View style={{ flexDirection: 'row', gap: 12 }}>
  {/* ... mevcut favori butonu buraya (değişmeden) ... */}

  <TouchableOpacity
    onPress={() => Alert.alert('Paylaş', `${courseTitle} paylaşıldı`)}
    activeOpacity={0.7}
    style={{
      flexDirection: 'row',
      gap: 8,
      paddingVertical: 10,
      paddingHorizontal: 16,
      borderRadius: 999,
      backgroundColor: '#2563eb',
    }}
  >
    <Ionicons name="share-social" size={18} color="#fff" />
    <Text style={{ color: '#fff', fontWeight: '600' }}>Paylaş</Text>
  </TouchableOpacity>
</View>
```
> Favori butonundaki `alignSelf: 'flex-start'` row içinde etkisiz kalır, silmene gerek yok.

**S12. Butonu pasif yap ve soluk göster**
```jsx
<TouchableOpacity
  disabled={true}                    // 👈 basılamaz
  style={{
    ...
    opacity: 0.4,                    // 👈 TouchableOpacity disabled olunca kendisi soluklaşmaz, bunu elle ekle
  }}
>
```

**S13 (zor). Birden fazla kurs kartı, her birinin kendi favori durumu**
```jsx
import { useState } from 'react';
import { SafeAreaView, ScrollView, Text, TouchableOpacity, View } from 'react-native';
import { Ionicons } from '@expo/vector-icons';

// Kurslar (component DIŞINDA)
const COURSES = [
  { id: '1', title: 'React Native Core Components' },
  { id: '2', title: 'State Management' },
  { id: '3', title: 'Navigation' },
];

export default function CourseCard() {
  // Her kursun favori durumu id'siyle: { '1': true, '3': false } (FlatList Kod 1'deki completed ile aynı mantık)
  const [favorites, setFavorites] = useState({});

  const toggleFavorite = (id) => {
    setFavorites((prev) => ({ ...prev, [id]: !prev[id] }));   // sadece bu id'yi tersine çevir
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a' }}>
      <ScrollView contentContainerStyle={{ padding: 24, gap: 16 }}>
        {COURSES.map((course) => {
          const isFavorite = favorites[course.id];               // bu kurs favoride mi?
          return (
            <View
              key={course.id}
              style={{ padding: 24, borderRadius: 20, backgroundColor: '#1f2937', gap: 16 }}
            >
              <Text style={{ color: '#f8fafc', fontSize: 20, fontWeight: '700' }}>
                {course.title}
              </Text>
              <TouchableOpacity
                onPress={() => toggleFavorite(course.id)}
                activeOpacity={0.7}
                style={{
                  alignSelf: 'flex-start',
                  flexDirection: 'row',
                  gap: 8,
                  paddingVertical: 10,
                  paddingHorizontal: 16,
                  borderRadius: 999,
                  backgroundColor: isFavorite ? '#f59e0b' : '#334155',
                }}
              >
                <Ionicons name={isFavorite ? 'star' : 'star-outline'} size={18} color="#fef3c7" />
                <Text style={{ color: '#fef3c7', fontWeight: '600' }}>
                  {isFavorite ? 'In favorites' : 'Add to favorites'}
                </Text>
              </TouchableOpacity>
            </View>
          );
        })}
      </ScrollView>
    </SafeAreaView>
  );
}
```
> **Neden tek bir `true/false` yetmiyor?** `useState(false)` olsaydı bir karta basınca **hepsi** birden favori olurdu. Her kartın kendi durumu olması için durumlar id'ye göre bir nesnede tutulur.

---

## 📦 Temel Kod 2 – Açılır Kapanır SSS (FAQ) Kartı

```jsx
import { useState } from 'react';
import { SafeAreaView, Text, TouchableOpacity, View } from 'react-native';

// Soru ve cevap (component DIŞINDA sabitler)
const QUESTION = 'How do I share my project with Expo?';
const ANSWER =
  'You can either scan the QR code in the Expo Go app or publish the project with the `expo publish` command.';

export default function FAQItem() {
  const [expanded, setExpanded] = useState(false);   // cevap açık mı? (başta kapalı)

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      {/* Kartı dikeyde ortalayan kutu */}
      <View
        style={{
          flex: 1,
          justifyContent: 'center',
          padding: 24,
        }}
      >
        {/* KART: beyaz, hafif gölgeli */}
        <View
          style={{
            borderRadius: 16,
            backgroundColor: '#fff',
            shadowColor: '#000',
            shadowOpacity: 0.04,
            shadowRadius: 12,
            elevation: 2,
          }}
        >
          {/* SORU SATIRI: tamamı tıklanabilir, basınca aç/kapa */}
          <TouchableOpacity
            onPress={() => setExpanded((prev) => !prev)}   // true ↔ false
            activeOpacity={0.6}
            style={{
              padding: 20,
              flexDirection: 'row',                        // soru solda, +/− sağda
              justifyContent: 'space-between',
              alignItems: 'center',
            }}
          >
            {/* Soru: flex: 1 → kalan genişliği alır, uzunsa alt satıra geçer */}
            <Text style={{ fontWeight: '600', flex: 1, fontSize: 16 }}>
              {QUESTION}
            </Text>
            {/* İşaret: açıksa −, kapalıysa + */}
            <Text style={{ color: '#2563eb', marginLeft: 16, fontSize: 18 }}>
              {expanded ? '−' : '+'}
            </Text>
          </TouchableOpacity>

          {/* CEVAP: sadece expanded true ise görünür (&&) */}
          {expanded && (
            <View style={{ paddingHorizontal: 20, paddingBottom: 20 }}>
              <Text style={{ color: '#475569', lineHeight: 20 }}>{ANSWER}</Text>
            </View>
          )}
        </View>
      </View>
    </SafeAreaView>
  );
}
```

> **Mantık:** `{expanded && ( ... )}` → `expanded` true ise cevap çizilir, false ise hiç çizilmez. Açılır kapanır menüler, detay göster/gizle, "devamını oku" gibi her şey bu kalıpla yapılır.

### Olası sorular – Kod 2

**S1. Soruyu ve cevabı değiştir**
```jsx
const QUESTION = 'Vize ne zaman?';                                // 👈 component DIŞINDAKİ sabitler
const ANSWER = 'Vize tarihi ders sayfasında duyurulacak.';         // 👈 metin içinde ' varsa önüne \ koy: 'Expo\'yu aç'
```

**S2. Cevap başta açık gelsin**
```jsx
const [expanded, setExpanded] = useState(true);   // 👈 false → true
```

**S3. + / − yerine ok ikonu kullan**
```jsx
import { Ionicons } from '@expo/vector-icons';                         // 👈 YENİ import

{/* +/− Text'inin YERİNE */}
<Ionicons
  name={expanded ? 'chevron-up' : 'chevron-down'}                       // 👈 açıksa yukarı, kapalıysa aşağı ok
  size={20}
  color="#2563eb"
  style={{ marginLeft: 16 }}
/>
```
> İkon kullanmadan: `{expanded ? '▲' : '▼'}`

**S4. Açıkken soru mavi, kartın kenarlığı belirgin olsun**
```jsx
<Text style={{ fontWeight: '600', flex: 1, fontSize: 16, color: expanded ? '#2563eb' : '#0f172a' }}>   {/* 👈 */}

// KART View'ının style'ına:
borderWidth: 1,
borderColor: expanded ? '#2563eb' : '#e2e8f0',     // 👈
```

**S5. Cevap alanının arka planı farklı olsun**
```jsx
<View style={{ paddingHorizontal: 20, paddingBottom: 20, paddingTop: 12, backgroundColor: '#eff6ff' }}>   {/* 👈 */}
```
> Kartın köşeleri yuvarlak olduğu için renkli alan köşeden taşarsa: KART View'ına `overflow: 'hidden'` ekle.

**S6 (zor). Birden fazla soru – her biri bağımsız açılıp kapansın**
```jsx
import { useState } from 'react';
import { SafeAreaView, ScrollView, Text, TouchableOpacity, View } from 'react-native';

// QUESTION ve ANSWER sabitlerinin YERİNE dizi (component DIŞINDA):
const FAQS = [
  { id: '1', question: 'How do I share my project with Expo?', answer: 'Scan the QR code in the Expo Go app.' },
  { id: '2', question: 'Is Expo free?', answer: 'Yes, Expo Go and Snack are free to use.' },
  { id: '3', question: 'What is a component?', answer: 'A reusable piece of UI, written as a function.' },
];

export default function FAQItem() {
  // Hangi sorular açık: { '1': true, '2': false } (S13'teki favorites ile aynı mantık)
  const [openItems, setOpenItems] = useState({});

  const toggle = (id) => {
    setOpenItems((prev) => ({ ...prev, [id]: !prev[id] }));
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <ScrollView contentContainerStyle={{ padding: 24, gap: 12 }}>
        {FAQS.map((faq) => {
          const expanded = openItems[faq.id];          // bu soru açık mı?
          return (
            <View
              key={faq.id}
              style={{ borderRadius: 16, backgroundColor: '#fff', shadowColor: '#000', shadowOpacity: 0.04, shadowRadius: 12, elevation: 2 }}
            >
              <TouchableOpacity
                onPress={() => toggle(faq.id)}
                activeOpacity={0.6}
                style={{ padding: 20, flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' }}
              >
                <Text style={{ fontWeight: '600', flex: 1, fontSize: 16 }}>{faq.question}</Text>
                <Text style={{ color: '#2563eb', marginLeft: 16, fontSize: 18 }}>{expanded ? '−' : '+'}</Text>
              </TouchableOpacity>
              {expanded && (
                <View style={{ paddingHorizontal: 20, paddingBottom: 20 }}>
                  <Text style={{ color: '#475569', lineHeight: 20 }}>{faq.answer}</Text>
                </View>
              )}
            </View>
          );
        })}
      </ScrollView>
    </SafeAreaView>
  );
}
```

**S7 (zor). Aynı anda sadece BİR soru açık olsun (akordeon)**
```jsx
// S6'nın kodunda sadece state ve toggle değişir:
const [openId, setOpenId] = useState(null);                       // 👈 nesne yerine tek id (hiçbiri açık değil)

const toggle = (id) => {
  setOpenId((prev) => (prev === id ? null : id));                 // 👈 zaten açıksa kapat, değilse bunu aç (diğeri otomatik kapanır)
};

// map içinde:
const expanded = openId === faq.id;                               // 👈 açık olan bu mu?
```
> **S6 ile S7 farkı:** S6'da her sorunun kendi durumu var (nesne). S7'de sadece "hangisi açık" bilgisi tutuluyor (tek id), bu yüzden birini açınca diğeri kendiliğinden kapanır.

**S8. Kartın altında "X soru" bilgisi (S6/S7 için)**
```jsx
{/* ScrollView içinde, map'in ÜSTÜNE */}
<Text style={{ color: '#475569' }}>{FAQS.length} soru</Text>
```

---

## ⚠️ Sık hatalar
- `<TouchableOpacity>` içine doğrudan yazı yazmak → hata. Yazı mutlaka `<Text>` içinde olmalı.
- `View`'ı `TouchableOpacity`'ye çevirirken kapanış etiketini (`</View>` → `</TouchableOpacity>`) unutmak.
- İkon import'unu unutmak: `import { Ionicons } from '@expo/vector-icons';` (react-native'den DEĞİL, ayrı satır).
- Yanlış ikon adı yazmak → ekranda `?` işareti çıkar. Adı https://icons.expo.fyi'den kontrol et.
- Birden fazla kartta tek bir `true/false` state kullanmak → hepsi birlikte değişir. Her kart için durum id'ye göre nesnede tutulur (S13, S6).
