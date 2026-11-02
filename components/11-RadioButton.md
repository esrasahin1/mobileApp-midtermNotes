# 11 – RadioButton

React Native'de **hazır bir RadioButton component'i yok.** Bu yüzden hoca kendi radio butonunu `Pressable` + iki `View` (dış halka + iç nokta) ile yapıyor. Radio buton, bir gruptan **sadece bir** seçenek seçilmesini sağlar: birini seçince diğeri otomatik olarak seçimden çıkar.

> 📝 **Definition (exam):** *A radio button lets the user select exactly one option from a group. React Native does not have a built-in radio button component, so it is built manually with `Pressable` (or `TouchableOpacity`) and `View`s, or taken from a UI library such as `react-native-paper`. The selected option is stored as a single value in state, and each option compares its own value with that state to decide whether it is selected.*

---

## 🔘 Radio vs Checkbox vs Picker

| | Radio Button | Checkbox | Picker |
|---|---|---|---|
| **Selection** | Exactly **one** option | **Zero or many** options | Exactly **one** option |
| **State** | A single value: `'core'` | An object/array: `{ core: true, nav: true }` | A single value |
| **Options visible?** | All options always visible | All options always visible | Hidden in a dropdown |
| **Best for** | Few options (2–5) | Independent yes/no choices | Many options, saving space |

**Short answer:** *Use radio buttons when the user must choose exactly one option and all options should be visible. Use checkboxes when multiple options can be selected independently. Use a Picker when there are many options and screen space is limited.*

---

## 👆 Pressable vs TouchableOpacity

| | Pressable | TouchableOpacity |
|---|---|---|
| **Press feedback** | None by default, you define it | Built-in opacity fade |
| **Style while pressed** | `style={({ pressed }) => ({ ... })}` | Only `activeOpacity` |
| **Status** | Newer, recommended API | Older, still widely used |
| **Usage** | Same props: `onPress`, `onLongPress`, `disabled`, `style` | Same |

**Short answer:** *Pressable is the newer and more flexible API for handling presses. Unlike TouchableOpacity, it has no default visual feedback, but its `style` prop can be a function that receives `{ pressed }`, so you can fully customize how it looks while being pressed.*

---

## 🎯 Radio butonun anatomisi

```
 ( • )  Core Components     ← seçili:   dış halka MAVİ  + iç nokta VAR
 (   )  State & Props       ← seçilmemiş: dış halka GRİ + iç nokta YOK
```
- **Dış halka:** `width` = `height`, `borderRadius` = yarısı, `borderWidth: 2` → içi boş daire
- **İç nokta:** daha küçük dolu daire, sadece seçiliyse `{isSelected && ( ... )}` ile çizilir
- **Mantık:** seçili olan tek bir değer state'te durur. Her seçenek `value === item.id` ile "seçili olan ben miyim?" diye bakar.

---

## 📦 Temel Kod 1 – Kendi RadioItem Component'imiz

```jsx
import { useState } from 'react';
import { SafeAreaView, View, Text, Pressable } from 'react-native';

// Seçenekler (component DIŞINDA)
const MODULES = [
  { id: 'core', title: 'Core Components' },
  { id: 'state', title: 'State & Props' },
  { id: 'nav', title: 'Navigation' },
];

// 1. COMPONENT: tek bir radio satırı. Her şeyi PROPS ile dışarıdan alır
//   label      → yanındaki yazı
//   value      → bu seçeneğin değeri (id)
//   isSelected → seçili mi? (true/false)
//   onPress    → basınca çağrılacak fonksiyon
function RadioItem({ label, value, isSelected, onPress }) {
  return (
    <Pressable
      accessibilityRole="radio"                          // ekran okuyucuya "bu bir radio buton" der
      accessibilityState={{ selected: isSelected }}
      onPress={() => onPress(value)}                     // basınca kendi value'sunu dışarı gönder
      style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingVertical: 10 }}   // daire + yazı yan yana
    >
      {/* DIŞ HALKA: 22x22, borderRadius 11 → daire. Seçiliyse mavi, değilse gri */}
      <View
        style={{
          width: 22,
          height: 22,
          borderRadius: 11,
          borderWidth: 2,
          borderColor: isSelected ? '#38bdf8' : '#475569',
          alignItems: 'center',                          // iç noktayı ortala
          justifyContent: 'center',
        }}
      >
        {/* İÇ NOKTA: sadece seçiliyse çizilir */}
        {isSelected && (
          <View
            style={{
              width: 10,
              height: 10,
              borderRadius: 5,
              backgroundColor: '#38bdf8',
            }}
          />
        )}
      </View>
      {/* Yazı */}
      <Text style={{ color: '#e2e8f0', fontSize: 16 }}>{label}</Text>
    </Pressable>
  );
}

// 2. COMPONENT: ana ekran (export default)
export default function ModuleSelector() {
  const [value, setValue] = useState('core');            // seçili olan TEK değer

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a', padding: 24 }}>
      <Text style={{ color: '#94a3b8' }}>Active Module</Text>
      {/* Seçili modülün adı (Picker dosyasındaki find + ?. ile aynı) */}
      <Text style={{ color: '#e2e8f0', fontSize: 22, fontWeight: '700', marginBottom: 16 }}>
        {MODULES.find((item) => item.id === value)?.title}
      </Text>
      {/* Her modül için bir RadioItem */}
      {MODULES.map((item) => (
        <RadioItem
          key={item.id}
          label={item.title}
          value={item.id}
          isSelected={value === item.id}                 // seçili olan bu mu?
          onPress={setValue}                             // basınca setValue(item.id) çalışır
        />
      ))}
    </SafeAreaView>
  );
}
```

> **Props akışı (W4 konusu):** Ana component veriyi ve `setValue` fonksiyonunu RadioItem'a **props** ile gönderir. RadioItem basılınca `onPress(value)` çağırır. Bu aslında `setValue('state')` demek. Böylece child, parent'ın state'ini değiştirmiş olur (veri aşağı, olay yukarı).
> **`onPress={setValue}`:** fonksiyonun kendisi gönderiliyor (parantezsiz). RadioItem içinde `onPress(value)` diye çağrılınca `setValue(value)` çalışır.

### Olası sorular – Kod 1

**S1. Yeni seçenek ekle**
```jsx
{ id: 'anim', title: 'Animations' },     // 👈 MODULES'un sonuna (id benzersiz)
```

**S2. Başlangıçta hiçbiri seçili olmasın**
```jsx
const [value, setValue] = useState(null);                       // 👈 'core' → null

<Text style={{ color: '#e2e8f0', fontSize: 22, fontWeight: '700', marginBottom: 16 }}>
  {MODULES.find((item) => item.id === value)?.title ?? 'Henüz seçilmedi'}   {/* 👈 ?? → bulunamazsa bu yazı */}
</Text>
```
> `??` → solundaki değer `undefined` veya `null` ise sağındakini kullanır.

**S3. Seçili rengi mavi yerine yeşil yap**
```jsx
// ⚠️ RadioItem içinde İKİ yerde geçiyor:
borderColor: isSelected ? '#22c55e' : '#475569',     // 👈 1) dış halka
backgroundColor: '#22c55e',                           // 👈 2) iç nokta
```

**S4. Daireyi büyüt**
```jsx
// Dış halka:
width: 30,
height: 30,
borderRadius: 15,     // 👈 yarısı

// İç nokta:
width: 14,
height: 14,
borderRadius: 7,      // 👈 yarısı
```
> Kural: `borderRadius` = genişliğin yarısı (tam daire için). İç nokta, dış halkanın yaklaşık yarısı kadar olmalı.

**S5. Rengi PROP olarak dışarıdan gönder (varsayılan değerli)**
```jsx
// RadioItem'ın parametrelerine yeni prop + varsayılan değer:
function RadioItem({ label, value, isSelected, onPress, activeColor = '#38bdf8' }) {   // 👈 gönderilmezse mavi

// İçeride sabit renk yerine prop:
borderColor: isSelected ? activeColor : '#475569',    // 👈
backgroundColor: activeColor,                          // 👈 iç nokta

// Kullanırken:
<RadioItem
  key={item.id}
  label={item.title}
  value={item.id}
  isSelected={value === item.id}
  onPress={setValue}
  activeColor="#f97316"                                // 👈 turuncu gönder
/>
```

**S6. Seçili seçeneğin yazısı kalın ve renkli olsun**
```jsx
<Text
  style={{
    color: isSelected ? '#38bdf8' : '#e2e8f0',        // 👈
    fontSize: 16,
    fontWeight: isSelected ? '700' : '400',           // 👈
  }}
>
  {label}
</Text>
```

**S7. Yuvarlak yerine kare (checkbox görünümü)**
```jsx
borderRadius: 4,     // 👈 dış halkada 11 → 4
borderRadius: 2,     // 👈 iç noktada 5 → 2
```

**S8. Altta "Onayla" butonu, basınca seçimi göstersin**
```jsx
import { Alert, Button, SafeAreaView, View, Text, Pressable } from 'react-native';   // 👈 Alert, Button

{/* map'in ALTINA */}
<View style={{ marginTop: 24 }}>
  <Button
    title="Onayla"
    onPress={() => Alert.alert('Seçimin', MODULES.find((item) => item.id === value)?.title)}
  />
</View>
```
> S2 ile birlikte kullanılıyorsa: `disabled={value === null}` ekle, seçim yapılmadan basılamasın.

**S9. İkinci bir radio grubu ekle (ör. zorluk seviyesi)**
```jsx
const LEVELS = [                                       // 👈 component DIŞINA
  { id: 'easy', title: 'Kolay' },
  { id: 'medium', title: 'Orta' },
  { id: 'hard', title: 'Zor' },
];

const [level, setLevel] = useState('easy');            // 👈 AYRI state (her grubun kendi state'i)

{/* İlk map'in ALTINA */}
<Text style={{ color: '#94a3b8', marginTop: 24 }}>Zorluk</Text>
{LEVELS.map((item) => (
  <RadioItem                                           // 👈 aynı component TEKRAR kullanılıyor
    key={item.id}
    label={item.title}
    value={item.id}
    isSelected={level === item.id}
    onPress={setLevel}
  />
))}
```
> Kendi component'ini yazmanın faydası bu: RadioItem'ı bir kez yazdın, istediğin kadar grupta kullanırsın.

**S10. Bir seçeneği pasif yap (seçilemesin)**
```jsx
{ id: 'nav', title: 'Navigation (yakında)', disabled: true },          // 👈 dizide yeni alan

function RadioItem({ label, value, isSelected, onPress, disabled }) {  // 👈 yeni prop
  return (
    <Pressable
      disabled={disabled}                                              // 👈 basılamaz
      onPress={() => onPress(value)}
      style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingVertical: 10, opacity: disabled ? 0.4 : 1 }}   // 👈 soluk
    >

// Kullanırken:
disabled={item.disabled}                                               // 👈
```

**S11. Her seçeneğin altında açıklama olsun**
```jsx
{ id: 'core', title: 'Core Components', description: 'View, Text, Image' },   // 👈 dizide

function RadioItem({ label, description, value, isSelected, onPress }) {      // 👈 yeni prop

{/* RadioItem içinde, label Text'inin YERİNE */}
<View>
  <Text style={{ color: '#e2e8f0', fontSize: 16 }}>{label}</Text>
  <Text style={{ color: '#94a3b8', fontSize: 13 }}>{description}</Text>
</View>

// Kullanırken:
description={item.description}                                                // 👈
```

**S12. Basılırken hafif soluklaşsın (Pressable'a özel)**
```jsx
<Pressable
  onPress={() => onPress(value)}
  style={({ pressed }) => ({                          // 👈 style artık FONKSİYON: pressed true/false gelir
    flexDirection: 'row',
    alignItems: 'center',
    gap: 12,
    paddingVertical: 10,
    opacity: pressed ? 0.5 : 1,                       // 👈 basılıyken soluk
  })}
>
```

---

## 📦 Temel Kod 2 – FlatList ile Kart Şeklinde Radio Seçimi

```jsx
import { useState } from 'react';
import { SafeAreaView, FlatList, View, Text, Pressable } from 'react-native';

// Seçenekler (component DIŞINDA)
const TRACKS = [
  { id: 'foundation', label: 'Foundation · Layout' },
  { id: 'growth', label: 'Growth · Networking' },
  { id: 'advanced', label: 'Advanced · Performance' },
  { id: 'capstone', label: 'Capstone · Final Project' },
];

export default function TrackSelector() {
  const [selected, setSelected] = useState('foundation');   // seçili TEK değer

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#020617', paddingTop: 24 }}>
      <FlatList
        data={TRACKS}
        keyExtractor={(item) => item.id}
        contentContainerStyle={{ paddingHorizontal: 24, gap: 12 }}
        renderItem={({ item }) => {
          const isActive = item.id === selected;             // bu kart seçili mi?
          return (
            // KART: tamamı basılabilir. Seçiliyse koyu mavi zemin + mavi kenarlık
            <Pressable
              accessibilityRole="radio"
              accessibilityState={{ selected: isActive }}
              onPress={() => setSelected(item.id)}
              style={{
                backgroundColor: isActive ? '#172554' : '#0f172a',
                borderRadius: 16,
                padding: 16,
                borderWidth: 1,
                borderColor: isActive ? '#38bdf8' : '#1e293b',
                flexDirection: 'row',                      // daire + yazı yan yana
                alignItems: 'center',
                gap: 12,
              }}
            >
              {/* DIŞ HALKA */}
              <View
                style={{
                  width: 20,
                  height: 20,
                  borderRadius: 10,
                  borderWidth: 2,
                  borderColor: isActive ? '#38bdf8' : '#475569',
                  alignItems: 'center',
                  justifyContent: 'center',
                }}
              >
                {/* İÇ NOKTA: sadece seçiliyse */}
                {isActive && (
                  <View
                    style={{
                      width: 10,
                      height: 10,
                      borderRadius: 5,
                      backgroundColor: '#38bdf8',
                    }}
                  />
                )}
              </View>
              {/* Yazı */}
              <Text style={{ color: '#e2e8f0', fontSize: 16 }}>{item.label}</Text>
            </Pressable>
          );
        }}
      />
    </SafeAreaView>
  );
}
```

> **Kod 1'den farkı:** ayrı bir RadioItem component'i yok, radio görünümü doğrudan `renderItem` içine yazılmış. Ayrıca sadece daire değil, **kartın tamamı** seçime göre renk değiştiriyor.

### Olası sorular – Kod 2

**S1. Yeni track ekle**
```jsx
{ id: 'mobile', label: 'Mobile · Expo Router' },     // 👈 TRACKS'in sonuna
```

**S2. Seçili rengi değiştir (mor)**
```jsx
// ⚠️ ÜÇ yerde mavi var:
borderColor: isActive ? '#a855f7' : '#1e293b',      // 👈 1) kart kenarlığı
borderColor: isActive ? '#a855f7' : '#475569',      // 👈 2) dış halka
backgroundColor: '#a855f7',                          // 👈 3) iç nokta
// ve istersen kart zemini: backgroundColor: isActive ? '#3b0764' : '#0f172a'
```

**S3. Listenin üstüne başlık ekle**
```jsx
<FlatList
  ...
  ListHeaderComponent={
    <Text style={{ color: '#f8fafc', fontSize: 22, fontWeight: '700', marginBottom: 8 }}>
      Bir track seç
    </Text>
  }
/>
```

**S4. Listenin altında seçili track yazsın**
```jsx
ListFooterComponent={
  <Text style={{ color: '#94a3b8', marginTop: 8 }}>
    Seçilen: {TRACKS.find((t) => t.id === selected)?.label}
  </Text>
}
```

**S5. Seçili kartın sağına tik ikonu**
```jsx
import { Ionicons } from '@expo/vector-icons';                  // 👈 YENİ import

{/* Yazı Text'inin ALTINA (Pressable'ın İÇİNE) */}
{isActive && (
  <Ionicons name="checkmark-circle" size={22} color="#38bdf8" style={{ marginLeft: 'auto' }} />   // 👈 marginLeft: 'auto' → en sağa it
)}
```

**S6. Başta hiçbiri seçili olmasın, seçim yapılmadan "Devam" basılamasın**
```jsx
import { Button, SafeAreaView, FlatList, View, Text, Pressable } from 'react-native';   // 👈 Button

const [selected, setSelected] = useState(null);                 // 👈 null

<FlatList
  ...
  ListFooterComponent={
    <View style={{ marginTop: 8 }}>
      <Button title="Devam" disabled={selected === null} onPress={() => {}} />   {/* 👈 seçim yoksa pasif */}
    </View>
  }
/>
```

**S7. Her karta açıklama ekle**
```jsx
{ id: 'foundation', label: 'Foundation · Layout', desc: '4 hafta' },   // 👈 dizide

{/* Yazı Text'inin YERİNE */}
<View>
  <Text style={{ color: '#e2e8f0', fontSize: 16 }}>{item.label}</Text>
  <Text style={{ color: '#94a3b8', fontSize: 13 }}>{item.desc}</Text>
</View>
```

**S8. Seçili karttaki yazı kalın olsun**
```jsx
<Text style={{ color: '#e2e8f0', fontSize: 16, fontWeight: isActive ? '700' : '400' }}>{item.label}</Text>
```

---

## ⚠️ Sık hatalar
- `import { RadioButton } from 'react-native'` → **böyle bir component yok.** Elle yapılır (bu dosyadaki gibi).
- Renk değiştirirken sadece bir yeri değiştirmek → halka ve nokta farklı renkte kalır. Tüm yerleri değiştir.
- Daire olmuyor → `width` ve `height` eşit mi, `borderRadius` yarısı mı kontrol et.
- Her seçenek için ayrı `true/false` state açmak → birden fazla seçilebilir hale gelir (o checkbox olur). Radio'da **tek** bir state vardır.
- Kendi component'ine yeni prop eklediğinde onu **iki yerde** yazman gerekir: component'in parametrelerinde (`function RadioItem({ ..., yeniProp })`) ve kullanırken (`<RadioItem yeniProp={...} />`).
