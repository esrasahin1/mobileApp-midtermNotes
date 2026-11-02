# 12 – Checkbox

`Checkbox`, kullanıcının bir seçeneği **işaretleyip kaldırabildiği** kutucuktur. Radio butondan farkı: her checkbox **bağımsızdır**, istenen kadarı (hiçbiri, biri, hepsi) aynı anda işaretlenebilir. Görev listeleri, ayarlar ve "Koşulları kabul ediyorum" kutuları bununla yapılır.

> 📝 **Definition (exam):** *A checkbox is a control that lets the user toggle an option on or off. Unlike radio buttons, checkboxes are independent, so any number of them can be selected at the same time. In Expo, it is provided by the `expo-checkbox` package and is controlled with the `value` prop (a boolean) and the `onValueChange` event, which gives the new boolean value.*

> ⚠️ **Ayrı paket ve farklı import:**
> ```jsx
> import Checkbox from 'expo-checkbox';     // süslü parantez YOK
> ```
> Snack'te bağımlılık uyarısı çıkınca **"Add dependency"**'ye tıkla. Kendi bilgisayarında: `npx expo install expo-checkbox`

> **Neden süslü parantez yok? (default vs named import)**
> | | Default import | Named import |
> |---|---|---|
> | **Syntax** | `import Checkbox from 'expo-checkbox'` | `import { Picker } from '@react-native-picker/picker'` |
> | **Meaning** | The module's **main** export (`export default`), any name can be used | A **specific** export, must use the exact name |
> | **Per file** | Only one | Many |
>
> Hangisi olduğunu paketin dokümanı söyler. Bizim yazdığımız component'ler de `export default function ...` olduğu için default export'tur.

---

## ☑️ Checkbox hızlı tablo

| Prop / Event | Ne yapar | Örnek |
|---|---|---|
| `value` | İşaretli mi (**true/false**) | `value={isChecked}` |
| `onValueChange` | Değişince çalışır, **yeni** true/false değerini verir | `onValueChange={(v) => setChecked(v)}` |
| `color` | İşaretliyken rengi | `color="#22c55e"` |
| `disabled` | İşaretlenemez | `disabled={true}` |
| `style` | Boyut, köşe | `style={{ width: 22, height: 22, borderRadius: 6 }}` |

> **Tek checkbox → tek boolean state:** `const [agree, setAgree] = useState(false);`
> **Birden fazla checkbox → nesne state:** `{ slides: true, demo: false }` (FlatList'teki `completed`, TouchableOpacity'deki `favorites` ile aynı mantık)

---

## 📦 Temel Kod 1 – Ders Hazırlık Görev Listesi

```jsx
import { useState } from 'react';
import { SafeAreaView, View, Text } from 'react-native';
import Checkbox from 'expo-checkbox';                  // default import (süslü parantezsiz)

// Görevler (component DIŞINDA)
const TASKS = [
  { id: 'slides', label: 'Review the new slides' },
  { id: 'demo', label: 'Run the Image + ScrollView demo' },
  { id: 'prep', label: 'Draft the Q&A scenarios' },
];

export default function LessonChecklist() {
  // Hangi görevler işaretli: { slides: true, demo: false } gibi. Başta boş
  const [completed, setCompleted] = useState({});

  // Checkbox değişince: o görevin değerini yaz, diğerlerini koru
  const toggleTask = (taskId, value) => {
    setCompleted((prev) => ({ ...prev, [taskId]: value }));
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a', padding: 24, gap: 16 }}>
      <Text style={{ color: '#e2e8f0', fontSize: 22, fontWeight: '700' }}>W6 Prep</Text>

      {/* Her görev için bir satır: checkbox + yazı yan yana */}
      {TASKS.map((task) => (
        <View
          key={task.id}
          style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingVertical: 8 }}
        >
          <Checkbox
            value={!!completed[task.id]}                              // !! → undefined'ı false'a çevirir
            onValueChange={(value) => toggleTask(task.id, value)}     // yeni değeri kaydet
            color={completed[task.id] ? '#22c55e' : undefined}        // işaretliyse yeşil, değilse varsayılan
            style={{ width: 22, height: 22, borderRadius: 6 }}
          />
          {/* Yazı: işaretliyse üstü çizili */}
          <Text
            style={{
              color: '#e2e8f0',
              textDecorationLine: completed[task.id] ? 'line-through' : 'none',
            }}
          >
            {task.label}
          </Text>
        </View>
      ))}
    </SafeAreaView>
  );
}
```

> **`!!completed[task.id]` neden?** Başta nesne boş olduğu için `completed['slides']` → `undefined`. Checkbox'ın `value`'su kesinlikle `true` veya `false` istiyor. `!` tersine çevirir (`undefined` → `true`), ikinci `!` tekrar çevirir (`true` → `false`). Sonuç: `undefined` → `false`, `true` → `true`.
> **`color={... : undefined}`:** `undefined` vermek "renk belirtme, varsayılanı kullan" demek.

### Olası sorular – Kod 1

**S1. Yeni görev ekle**
```jsx
{ id: 'quiz', label: 'Prepare the quiz questions' },     // 👈 TASKS'in sonuna (id benzersiz)
```

**S2. İşaretli rengi değiştir**
```jsx
color={completed[task.id] ? '#a855f7' : undefined}      // 👈 yeşil → mor
```

**S3. Kaç görevin tamamlandığını göster**
```jsx
// component İÇİNDE, toggleTask'in ALTINA:
const doneCount = Object.values(completed).filter(Boolean).length;   // true olanları say (FlatList'teki ile aynı)

{/* Başlığın ALTINA */}
<Text style={{ color: '#94a3b8' }}>{doneCount} / {TASKS.length} tamamlandı</Text>
```

**S4. Hepsi tamamlanınca mesaj çıksın**
```jsx
{/* map'in ALTINA (S3'teki doneCount gerekli) */}
{doneCount === TASKS.length && (
  <Text style={{ color: '#22c55e', fontWeight: '700' }}>Hazırlık tamam! 🎉</Text>
)}
```

**S5. Yazıya dokununca da işaretlensin**
```jsx
import { SafeAreaView, View, Text, Pressable } from 'react-native';   // 👈 Pressable

{/* Satırı saran View'ın YERİNE Pressable */}
<Pressable
  key={task.id}
  onPress={() => toggleTask(task.id, !completed[task.id])}           // 👈 tersine çevir
  style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingVertical: 8 }}
>
  {/* Checkbox ve Text aynı kalır */}
</Pressable>                                                          // 👈 kapanış da değişir
```

**S6. "Tümünü seç" ve "Temizle" butonları**
```jsx
import { Button, SafeAreaView, View, Text } from 'react-native';      // 👈 Button

{/* Başlığın ALTINA */}
<View style={{ flexDirection: 'row', gap: 12 }}>
  <Button
    title="Tümünü seç"
    onPress={() => setCompleted(TASKS.reduce((acc, t) => ({ ...acc, [t.id]: true }), {}))}   // 👈 her id'ye true yaz
  />
  <Button title="Temizle" color="#ef4444" onPress={() => setCompleted({})} />                 // 👈 boş nesne
</View>
```
> `reduce` burada boş bir nesneden başlıyor (`{}`) ve her görev için bir alan ekliyor: `{}` → `{ slides: true }` → `{ slides: true, demo: true }` → ... (Kod 2'nin başlangıç değerinde de aynı kalıp var.)

**S7. Uygulama bazı görevler işaretli açılsın**
```jsx
const [completed, setCompleted] = useState({ slides: true });   // 👈 slides baştan işaretli
```

**S8. Checkbox'ı büyüt ve yuvarlak yap**
```jsx
style={{ width: 30, height: 30, borderRadius: 15 }}   // 👈 borderRadius yarısı → daire
```

**S9. Seçilen görevleri altta yazıyla listele**
```jsx
{/* map'in ALTINA */}
<Text style={{ color: '#94a3b8', marginTop: 16 }}>
  Seçilenler: {TASKS.filter((t) => completed[t.id]).map((t) => t.label).join(', ') || 'yok'}
</Text>
```
> Zincir: işaretli olanları bırak (`filter`) → sadece yazılarını al (`map`) → virgülle birleştir (`join`). Hiçbiri yoksa boş yazı çıkar, `|| 'yok'` o zaman "yok" yazdırır.

**S10. ⭐ Tek checkbox: "Koşulları kabul ediyorum" + butonu aktif etsin**
```jsx
import { useState } from 'react';
import { Alert, Button, SafeAreaView, Text, View } from 'react-native';
import Checkbox from 'expo-checkbox';

export default function AgreementForm() {
  const [agree, setAgree] = useState(false);              // 👈 TEK checkbox → TEK boolean (nesne gerekmez)

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a', padding: 24, gap: 16, justifyContent: 'center' }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12 }}>
        <Checkbox
          value={agree}
          onValueChange={setAgree}                        // 👈 yeni değer doğrudan state'e (fonksiyon referansı)
          color={agree ? '#22c55e' : undefined}
        />
        <Text style={{ color: '#e2e8f0' }}>Kullanım koşullarını okudum ve kabul ediyorum</Text>
      </View>
      <Button
        title="Kayıt Ol"
        disabled={!agree}                                 // 👈 işaretlenmeden basılamaz
        onPress={() => Alert.alert('Başarılı', 'Kayıt tamamlandı')}
      />
    </SafeAreaView>
  );
}
```
> TextInput dosyasındaki kayıt formuna (S15) eklenirse: `canSubmit`'e `&& agree` eklenir.

**S11. Tamamlananları gizle seçeneği**
```jsx
const [hideDone, setHideDone] = useState(false);                     // 👈 YENİ state

{/* Başlığın ALTINA: gizleme checkbox'ı */}
<View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
  <Checkbox value={hideDone} onValueChange={setHideDone} />
  <Text style={{ color: '#94a3b8' }}>Tamamlananları gizle</Text>
</View>

{/* map'ten ÖNCE filtre ekle */}
{TASKS.filter((task) => !(hideDone && completed[task.id])).map((task) => (   // 👈 gizle açık VE tamamlandıysa gösterme
```

---

## 📦 Temel Kod 2 – Katılımcı Ayarları Formu

```jsx
import { useMemo, useState } from 'react';
import { SafeAreaView, View, Text, TouchableOpacity } from 'react-native';
import Checkbox from 'expo-checkbox';

// Ayarlar (component DIŞINDA)
const SETTINGS = [
  { id: 'recordings', label: 'Share session recordings' },
  { id: 'reminders', label: 'Send a reminder 1 hour before class' },
  { id: 'survey', label: 'Trigger a mini survey after each lesson' },
];

export default function ParticipantSettings() {
  // Başlangıç değeri: SETTINGS'ten otomatik üretilir → { recordings: false, reminders: false, survey: false }
  const [options, setOptions] = useState(
    SETTINGS.reduce((acc, item) => ({ ...acc, [item.id]: false }), {})
  );

  // EN AZ BİRİ işaretli mi? (.some → biri bile true ise true)
  // ⚠️ İsmi "allActive" ama aslında "herhangi biri aktif mi" kontrol ediyor
  const allActive = useMemo(() => Object.values(options).some(Boolean), [options]);

  const toggle = (key, value) => {
    setOptions((prev) => ({ ...prev, [key]: value }));
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#020617', padding: 24 }}>
      <Text style={{ color: '#e2e8f0', fontSize: 20, fontWeight: '600', marginBottom: 16 }}>
        Participant Settings
      </Text>

      {/* Her ayar: yazı solda, checkbox sağda (space-between) */}
      {SETTINGS.map((setting) => (
        <View
          key={setting.id}
          style={{
            flexDirection: 'row',
            alignItems: 'center',
            justifyContent: 'space-between',
            paddingVertical: 12,
          }}
        >
          <Text style={{ color: '#e2e8f0', fontSize: 16 }}>{setting.label}</Text>
          <Checkbox
            value={options[setting.id]}                           // başlangıçta false olduğu için !! gerekmedi
            onValueChange={(value) => toggle(setting.id, value)}
            color={options[setting.id] ? '#38bdf8' : undefined}
          />
        </View>
      ))}

      {/* KAYDET: hiçbiri seçili değilse gri ve pasif */}
      <TouchableOpacity
        style={{
          marginTop: 32,
          backgroundColor: allActive ? '#38bdf8' : '#334155',
          borderRadius: 16,
          paddingVertical: 16,
          alignItems: 'center',
        }}
        disabled={!allActive}
      >
        <Text style={{ color: '#0f172a', fontWeight: '700' }}>Save Settings</Text>
      </TouchableOpacity>
    </SafeAreaView>
  );
}
```

> **`.some` ve `.every` farkı (sınavda "hepsi seçilince aktif olsun" denebilir):**
> | | `.some(Boolean)` | `.every(Boolean)` |
> |---|---|---|
> | **Returns true when** | **At least one** item is true | **All** items are true |
> | `[true, false, false]` | `true` | `false` |
> | `[true, true, true]` | `true` | `true` |
>
> Hocanın kodunda `.some` kullanıldığı için buton **en az bir** ayar seçilince aktif oluyor. Değişken adı `allActive` olsa da mantık "herhangi biri".

### Olası sorular – Kod 2

**S1. Yeni ayar ekle**
```jsx
{ id: 'newsletter', label: 'Subscribe to the weekly newsletter' },   // 👈 SETTINGS'in sonuna
```
> Başlangıç değeri `reduce` ile SETTINGS'ten üretildiği için yeni ayar otomatik olarak `false` başlar, başka bir yere dokunmana gerek yok.

**S2. Buton sadece HEPSİ seçilince aktif olsun**
```jsx
const allActive = useMemo(() => Object.values(options).every(Boolean), [options]);   // 👈 some → every
```

**S3. Bazı ayarlar baştan açık gelsin**
```jsx
// 1) Dizide varsayılan değer:
{ id: 'recordings', label: 'Share session recordings', defaultOn: true },   // 👈
{ id: 'reminders', label: 'Send a reminder 1 hour before class', defaultOn: false },
{ id: 'survey', label: 'Trigger a mini survey after each lesson', defaultOn: false },

// 2) reduce'ta false yerine o değer:
SETTINGS.reduce((acc, item) => ({ ...acc, [item.id]: item.defaultOn }), {})   // 👈
```
> Daha basit yol: `useState({ recordings: true, reminders: false, survey: false })` diye elle yazmak.

**S4. Kaydet'e basınca seçilen ayarları göster**
```jsx
import { Alert, SafeAreaView, View, Text, TouchableOpacity } from 'react-native';   // 👈 Alert

<TouchableOpacity
  onPress={() => {
    const selected = SETTINGS.filter((s) => options[s.id]).map((s) => s.label);    // 👈 işaretlilerin yazıları
    Alert.alert('Kaydedildi', selected.join('\n'));                                // 👈 her biri ayrı satırda
  }}
  style={{ ... }}
  disabled={!allActive}
>
```

**S5. Seçili ayar sayısını göster**
```jsx
const selectedCount = Object.values(options).filter(Boolean).length;   // 👈 component İÇİNDE

{/* Başlığın ALTINA */}
<Text style={{ color: '#94a3b8', marginBottom: 8 }}>{selectedCount} ayar seçili</Text>
```

**S6. Yazıları Türkçe yap, buton rengini değiştir**
```jsx
{ id: 'recordings', label: 'Ders kayıtlarını paylaş' },          // 👈 SETTINGS içindeki label'lar
Katılımcı Ayarları                                               {/* 👈 başlık */}
Ayarları Kaydet                                                  {/* 👈 buton yazısı */}
backgroundColor: allActive ? '#22c55e' : '#334155',              // 👈 aktifken yeşil
```

**S7. Pasifken buton yazısı da soluk olsun**
```jsx
<Text style={{ color: allActive ? '#0f172a' : '#64748b', fontWeight: '700' }}>Save Settings</Text>   // 👈
```

**S8 (zor). "Tümünü seç" ana checkbox'ı**
```jsx
const allChecked = Object.values(options).every(Boolean);     // 👈 hepsi seçili mi? (component İÇİNDE)

const toggleAll = (value) => {
  setOptions(SETTINGS.reduce((acc, item) => ({ ...acc, [item.id]: value }), {}));   // 👈 hepsine aynı değeri yaz
};

{/* Başlığın ALTINA */}
<View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingVertical: 12, borderBottomWidth: 1, borderColor: '#334155' }}>
  <Text style={{ color: '#e2e8f0', fontSize: 16, fontWeight: '700' }}>Tümünü seç</Text>
  <Checkbox value={allChecked} onValueChange={toggleAll} color={allChecked ? '#38bdf8' : undefined} />
</View>
```
> Hepsi tek tek işaretlenince ana kutu da kendiliğinden işaretli görünür, çünkü `allChecked` hesaplanan bir değer.

---

## ⚠️ Sık hatalar
- `import { Checkbox } from 'expo-checkbox'` (süslü parantezli) → **yanlış**, undefined gelir. Doğrusu: `import Checkbox from 'expo-checkbox'`.
- `import { Checkbox } from 'react-native'` → React Native'de böyle bir component yok.
- Snack'te bağımlılık uyarısına tıklamamak → "Unable to resolve module".
- `value`'ya `undefined` vermek → uyarı. Başlangıç değeri yoksa `!!` kullan (Kod 1'deki gibi).
- `onValueChange` yerine `onPress` yazmak → Checkbox'ta `onPress` yok.
- "Hepsi seçilince" isteniyorsa `.every`, "en az biri" isteniyorsa `.some`.
