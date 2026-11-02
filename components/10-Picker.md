# 10 – Picker

`Picker`, kullanıcının **hazır bir listeden tek bir seçenek** seçmesini sağlayan açılır menüdür (dropdown). Android'de açılır liste veya pencere, iOS'ta döner tekerlek, Snack web önizlemesinde normal bir HTML seçim kutusu olarak görünür.

> 📝 **Definition (exam):** *Picker is a dropdown component that lets the user select a single value from a predefined list of options. It is a controlled component: the selected value is stored in state with `selectedValue` and updated in `onValueChange`. The options are defined with `Picker.Item` elements, each having a `label` (shown to the user) and a `value` (stored in state).*

> ⚠️ **Ayrı paket:** Picker artık React Native'in içinde değil. Ayrı import edilir:
> ```jsx
> import { Picker } from '@react-native-picker/picker';
> ```
> Snack'e yapıştırınca bağımlılık eklemeni isteyen bir uyarı çıkar, **"Add dependency"**'ye tıkla. Kendi bilgisayarında: `npx expo install @react-native-picker/picker`

---

## 🔽 label vs value

| | `label` | `value` |
|---|---|---|
| **What is it?** | The text the user **sees** in the list | The data that is **stored in state** |
| **Example** | `'Foundation · Components'` | `'foundation'` |
| **Used for** | Display | Logic (comparisons, filtering) |

**Short answer:** *`label` is the human-readable text displayed in the dropdown, while `value` is the internal identifier stored in state when that option is selected. Keeping them separate lets you change the displayed text without breaking the logic.*

---

## 📋 Picker hızlı tablo

| Prop | Ne yapar | Örnek |
|---|---|---|
| `selectedValue` | Şu an seçili olan **value** (state) | `selectedValue={level}` |
| `onValueChange` | Seçim değişince çalışır, **(value, index)** verir | `onValueChange={(value) => setLevel(value)}` |
| `enabled` | Seçim yapılabilir mi | `enabled={false}` → kilitli |
| `mode` | Android'de görünüm | `'dialog'` (pencere) veya `'dropdown'` (açılır liste) |
| `style` | Yazı rengi vb. | `style={{ color: '#e2e8f0' }}` |
| `dropdownIconColor` | Android'de ok işaretinin rengi | `dropdownIconColor="#38bdf8"` |
| `<Picker.Item label="..." value="..." />` | Her bir seçenek | |

> **Kalıp TextInput ile aynı (controlled):** `selectedValue={state}` + `onValueChange={(value) => setState(value)}`.
> **Görünüm ipucu:** Picker'ın arka planı, köşe yuvarlaklığı gibi şeyler Android'de doğrudan çalışmaz. Bu yüzden Picker bir `View` ile sarılır ve stil o View'a verilir (iki örnekte de böyle).

---

## 📦 Temel Kod 1 – Kurs Seviyesi Seçici

```jsx
import { useState } from 'react';
import { SafeAreaView, View, Text } from 'react-native';
import { Picker } from '@react-native-picker/picker';     // ayrı paket

// Seçenekler: id → value olarak kullanılacak, label → ekranda görünecek (component DIŞINDA)
const COURSE_LEVELS = [
  { id: 'foundation', label: 'Foundation · Components' },
  { id: 'growth', label: 'Growth · Navigation' },
  { id: 'advanced', label: 'Advanced · Performance' },
];

export default function CourseLevelPicker() {
  const [level, setLevel] = useState('foundation');      // seçili seviyenin id'si (başlangıç: foundation)

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a', padding: 24 }}>
      {/* Küçük üst başlık */}
      <Text style={{ color: '#94a3b8', fontSize: 16 }}>Aktif Ders Bloğu</Text>

      {/* Seçili seviyenin label'ı: diziden id'si level olanı BUL, label'ını yaz */}
      <Text style={{ color: '#e2e8f0', fontSize: 28, fontWeight: '700' }}>
        {COURSE_LEVELS.find((item) => item.id === level)?.label}
      </Text>

      {/* PICKER: stil için View ile sarılmış (arka plan + köşe) */}
      <View style={{ marginTop: 32, backgroundColor: '#1e293b', borderRadius: 12 }}>
        <Picker
          selectedValue={level}                          // şu an seçili olan
          onValueChange={(value) => setLevel(value)}     // değişince state'i güncelle
          style={{ color: '#e2e8f0' }}                   // seçili yazının rengi
          dropdownIconColor="#38bdf8"                    // Android ok rengi
        >
          {/* Diziden seçenekleri üret: her biri için bir Picker.Item */}
          {COURSE_LEVELS.map((item) => (
            <Picker.Item
              key={item.id}
              label={item.label}                         // görünen yazı
              value={item.id}                            // state'e kaydedilecek değer
              color="#e2e8f0"                            // listedeki yazı rengi
            />
          ))}
        </Picker>
      </View>
    </SafeAreaView>
  );
}
```

> **`.find((item) => item.id === level)`:** dizide koşulu sağlayan **ilk** elemanı döndürür (bulamazsa `undefined`). `filter` dizi döndürür, `find` tek eleman.
> **`?.label` (optional chaining):** `find` bir şey bulamazsa `undefined` döner ve `undefined.label` hata verirdi. `?.` "varsa label'ını al, yoksa hata verme, `undefined` döndür" demek.

### Olası sorular – Kod 1

**S1. Yeni bir seviye ekle**
```jsx
const COURSE_LEVELS = [
  { id: 'foundation', label: 'Foundation · Components' },
  { id: 'growth', label: 'Growth · Navigation' },
  { id: 'advanced', label: 'Advanced · Performance' },
  { id: 'expert', label: 'Expert · Native Modules' },   // 👈 YENİ (id benzersiz olmalı)
];
```
> Picker'a otomatik eklenir, çünkü seçenekler `map` ile diziden üretiliyor.

**S2. Uygulama "Growth" seçili açılsın**
```jsx
const [level, setLevel] = useState('growth');   // 👈 bir id yazılır (label değil!)
```

**S3. Seçilen seviyenin açıklaması da görünsün**
```jsx
// 1) Her seviyeye description alanı (dizide):
{ id: 'foundation', label: 'Foundation · Components', description: 'View, Text ve Image ile temel yerleşim.' },
{ id: 'growth', label: 'Growth · Navigation', description: 'Ekranlar arası geçiş ve state.' },
{ id: 'advanced', label: 'Advanced · Performance', description: 'Liste optimizasyonu ve hız.' },

// 2) component İÇİNDE, useState'in ALTINA (find'ı bir kez yapıp sakla):
const selected = COURSE_LEVELS.find((item) => item.id === level);

// 3) Büyük başlığı ve açıklamayı göster:
<Text style={{ color: '#e2e8f0', fontSize: 28, fontWeight: '700' }}>
  {selected?.label}                                                    {/* 👈 tekrar find yazmaya gerek yok */}
</Text>
<Text style={{ color: '#94a3b8', marginTop: 8 }}>{selected?.description}</Text>   {/* 👈 YENİ */}
```

**S4. Seçilen seviyeye göre başlığın rengi değişsin**
```jsx
// Her seviyeye color alanı:
{ id: 'foundation', label: 'Foundation · Components', color: '#22c55e' },
{ id: 'growth', label: 'Growth · Navigation', color: '#f59e0b' },
{ id: 'advanced', label: 'Advanced · Performance', color: '#ef4444' },

// S3'teki selected ile:
<Text style={{ color: selected?.color, fontSize: 28, fontWeight: '700' }}>   {/* 👈 renk veriden geliyor */}
  {selected?.label}
</Text>
```

**S5. Başta "Seçiniz..." yazsın, seçim yapılmamışsa uyarı görünsün**
```jsx
const [level, setLevel] = useState('');                 // 👈 başta boş (hiçbiri seçili değil)

<Text style={{ color: '#e2e8f0', fontSize: 28, fontWeight: '700' }}>
  {level === ''
    ? 'Henüz seviye seçilmedi'                           // 👈 boşsa uyarı
    : COURSE_LEVELS.find((item) => item.id === level)?.label}
</Text>

<Picker selectedValue={level} onValueChange={(value) => setLevel(value)} style={{ color: '#e2e8f0' }}>
  <Picker.Item label="Seçiniz..." value="" color="#94a3b8" />   {/* 👈 ilk seçenek: value boş */}
  {COURSE_LEVELS.map((item) => (
    <Picker.Item key={item.id} label={item.label} value={item.id} color="#e2e8f0" />
  ))}
</Picker>
```

**S6. Seçim yapılınca uyarı çıksın**
```jsx
import { Alert, SafeAreaView, View, Text } from 'react-native';     // 👈 Alert

onValueChange={(value, index) => {
  setLevel(value);
  Alert.alert('Seçildi', COURSE_LEVELS[index].label);             // 👈 index ile diziden label'ı al
}}
```
> `onValueChange` iki şey verir: seçilen **value** ve sırası (**index**, 0'dan başlar). S5'teki gibi başa ekstra bir "Seçiniz" item'ı eklendiyse index bir kayar, o zaman `find` kullan.

**S7. Picker'ı kilitle (seçim yapılamasın)**
```jsx
<Picker
  enabled={false}                                      // 👈 YENİ
  selectedValue={level}
  ...
```

**S8. Picker kutusunun ve yazılarının rengini değiştir**
```jsx
<View style={{ marginTop: 32, backgroundColor: '#ffffff', borderRadius: 12 }}>   {/* 👈 kutu beyaz */}
  <Picker
    selectedValue={level}
    onValueChange={(value) => setLevel(value)}
    style={{ color: '#0f172a' }}                       // 👈 seçili yazı koyu
    dropdownIconColor="#0f172a"
  >
    {COURSE_LEVELS.map((item) => (
      <Picker.Item key={item.id} label={item.label} value={item.id} color="#0f172a" />   // 👈 liste yazıları koyu
    ))}
```

**S9. İkinci bir Picker ekle (ör. ders dili)**
```jsx
const LANGUAGES = [                                     // 👈 component DIŞINA
  { id: 'tr', label: 'Türkçe' },
  { id: 'en', label: 'English' },
];

const [language, setLanguage] = useState('tr');         // 👈 AYRI state (component İÇİNDE)

{/* İlk Picker'ı saran View'ın ALTINA */}
<View style={{ marginTop: 16, backgroundColor: '#1e293b', borderRadius: 12 }}>
  <Picker
    selectedValue={language}
    onValueChange={(value) => setLanguage(value)}
    style={{ color: '#e2e8f0' }}
  >
    {LANGUAGES.map((item) => (
      <Picker.Item key={item.id} label={item.label} value={item.id} color="#e2e8f0" />
    ))}
  </Picker>
</View>
```

---

## 📦 Temel Kod 2 – Seçime Göre Filtrelenen Oturum Listesi

```jsx
import { useMemo, useState } from 'react';
import { SafeAreaView, View, Text, FlatList } from 'react-native';
import { Picker } from '@react-native-picker/picker';

// Oturumlar: her birinin track'i (seviyesi) var (component DIŞINDA)
const SESSIONS = [
  { id: '1', title: 'Intro to View', track: 'foundation' },
  { id: '2', title: 'Layout Playground', track: 'foundation' },
  { id: '3', title: 'State Drill', track: 'growth' },
  { id: '4', title: 'FlatList Patterns', track: 'growth' },
  { id: '5', title: 'Perf Budgeting', track: 'advanced' },
];

export default function ScheduleFilter() {
  const [track, setTrack] = useState('foundation');     // seçili track
  // Sadece seçili track'teki oturumlar. track değişince yeniden hesaplanır
  const filtered = useMemo(
    () => SESSIONS.filter((session) => session.track === track),
    [track]
  );

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#020617', padding: 24 }}>
      {/* PICKER KUTUSU */}
      <View
        style={{
          backgroundColor: '#1e293b',
          borderRadius: 14,
          paddingHorizontal: 12,
        }}
      >
        <Picker
          selectedValue={track}
          onValueChange={(value) => setTrack(value)}
          mode="dropdown"                                // Android: açılır liste
          dropdownIconColor="#f97316"
        >
          {/* Seçenekler bu sefer ELLE yazılmış (map yok) */}
          <Picker.Item label="Foundation Track" value="foundation" />
          <Picker.Item label="Growth Track" value="growth" />
          <Picker.Item label="Advanced Track" value="advanced" />
        </Picker>
      </View>

      {/* FİLTRELENMİŞ LİSTE */}
      <FlatList
        data={filtered}
        keyExtractor={(item) => item.id}
        contentContainerStyle={{ gap: 12, marginTop: 24 }}
        renderItem={({ item }) => (
          <View
            style={{
              backgroundColor: '#0f172a',
              padding: 18,
              borderRadius: 16,
              borderWidth: 1,
              borderColor: '#1f2937',
            }}
          >
            <Text style={{ color: '#e2e8f0', fontSize: 16, fontWeight: '600' }}>
              {item.title}
            </Text>
            {/* track'e göre Türkçe seviye adı (iç içe ternary) */}
            <Text style={{ color: '#94a3b8', marginTop: 4 }}>
              {track === 'foundation' ? 'Temel' : track === 'growth' ? 'Orta' : 'İleri'} seviye
            </Text>
          </View>
        )}
        // Seçili track'te oturum yoksa
        ListEmptyComponent={
          <Text style={{ color: '#94a3b8', textAlign: 'center', marginTop: 32 }}>
            Bu track için oturum planlanmadı.
          </Text>
        }
      />
    </SafeAreaView>
  );
}
```

> **Mantık:** Picker `track`'i değiştirir → `useMemo` filtrelenmiş listeyi yeniden hesaplar → FlatList yeni listeyi gösterir. TextInput dosyasındaki arama kutusuyla aynı yapı, sadece yazı yerine seçim var.
> ⚠️ **Yeni track eklemek 3 yeri değiştirir:** `<Picker.Item>`, `SESSIONS` içindeki track değerleri ve kartlardaki iç içe ternary. Bunu tek yere indirmek için bkz. S5.

### Olası sorular – Kod 2

**S1. Yeni oturum ekle**
```jsx
{ id: '6', title: 'Navigation Basics', track: 'growth' },   // 👈 SESSIONS'ın sonuna (track mevcut değerlerden biri)
```

**S2. Yeni bir track ekle (ör. "expert")**
```jsx
// 1) Picker'a seçenek:
<Picker.Item label="Expert Track" value="expert" />                                  // 👈

// 2) O track'e oturum:
{ id: '6', title: 'Native Modules', track: 'expert' },                               // 👈

// 3) Kartlardaki ternary'ye yeni dal:
{track === 'foundation' ? 'Temel' : track === 'growth' ? 'Orta' : track === 'advanced' ? 'İleri' : 'Uzman'} seviye   {/* 👈 */}
```

**S3. "Tümü" seçeneği ekle (bütün oturumlar görünsün)**
```jsx
<Picker.Item label="All Tracks" value="all" />                                       // 👈 ilk seçenek olarak

const filtered = useMemo(
  () => SESSIONS.filter((session) => track === 'all' || session.track === track),  // 👈 'all' ise hepsi geçer
  [track]
);

// Kartta seviye adı artık seçili track'ten değil, OTURUMUN kendi track'inden gelmeli:
{item.track === 'foundation' ? 'Temel' : item.track === 'growth' ? 'Orta' : 'İleri'} seviye   {/* 👈 track → item.track */}
```

**S4. Kaç oturum bulunduğunu göster**
```jsx
{/* Picker kutusunun ALTINA */}
<Text style={{ color: '#94a3b8', marginTop: 16 }}>{filtered.length} oturum bulundu</Text>
```

**S5 (temiz yol). Track'leri tek bir diziden yönet**
```jsx
// component DIŞINA:
const TRACKS = [
  { id: 'foundation', label: 'Foundation Track', tr: 'Temel' },
  { id: 'growth', label: 'Growth Track', tr: 'Orta' },
  { id: 'advanced', label: 'Advanced Track', tr: 'İleri' },
];

// Picker.Item'ların YERİNE:
{TRACKS.map((t) => (
  <Picker.Item key={t.id} label={t.label} value={t.id} />
))}

// Kartlardaki iç içe ternary'nin YERİNE:
{TRACKS.find((t) => t.id === item.track)?.tr} seviye
```
> Artık yeni track eklemek için sadece `TRACKS`'e bir satır eklemek (ve oturum vermek) yeterli.

**S6. Kartların rengini değiştir, seçili track'e göre kenarlık rengi olsun**
```jsx
backgroundColor: '#1e3a8a',                                                          // 👈
borderColor: track === 'foundation' ? '#22c55e' : track === 'growth' ? '#f59e0b' : '#ef4444',   // 👈
```

**S7. Boş liste mesajını test et ve değiştir**
```jsx
<Picker.Item label="Expert Track" value="expert" />          // 👈 oturumu OLMAYAN bir track ekle → seçince boş mesaj görünür

<Text style={{ color: '#f97316', textAlign: 'center', marginTop: 32 }}>
  Bu seviyede henüz oturum yok.                               {/* 👈 */}
</Text>
```

**S8. Oturuma tıklayınca uyarı çıksın**
```jsx
import { Alert, SafeAreaView, View, Text, FlatList, TouchableOpacity } from 'react-native';   // 👈

renderItem={({ item }) => (
  <TouchableOpacity                                            // 👈 View → TouchableOpacity
    onPress={() => Alert.alert('Oturum', item.title)}
    style={{ backgroundColor: '#0f172a', padding: 18, borderRadius: 16, borderWidth: 1, borderColor: '#1f2937' }}
  >
    ...
  </TouchableOpacity>                                          // 👈 kapanış da değişir
)}
```

**S9. Uygulama "Growth" seçili açılsın**
```jsx
const [track, setTrack] = useState('growth');   // 👈 value yazılır
```

---

## ⚠️ Sık hatalar
- `import { Picker } from 'react-native'` → **yanlış**. Doğrusu: `from '@react-native-picker/picker'`.
- Snack'te bağımlılık uyarısına tıklamamak → "Unable to resolve module" hatası.
- `useState`'e **label** yazmak (`useState('Growth Track')`) → hiçbir seçenekle eşleşmez. Her zaman **value** yazılır.
- `Picker.Item`'a `value` vermemek → seçim state'e kaydedilmez.
- `map` ile üretirken `key` unutmak.
- Picker'a `backgroundColor` / `borderRadius` vermek → Android'de çalışmaz. Dışına bir `View` koy, stili ona ver.
