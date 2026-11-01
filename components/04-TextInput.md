# 04 – TextInput

`TextInput`, kullanıcının yazı girdiği alandır. Web'deki `<input>`'un karşılığıdır. Formlar, arama kutuları ve giriş ekranları bununla yapılır.

> **Controlled input (kontrollü input) kalıbı:** yazı bir state'te tutulur, input o state'i gösterir.
> ```jsx
> const [value, setValue] = useState('');
> <TextInput value={value} onChangeText={setValue} />
> ```
> Kullanıcı her harf yazdığında `onChangeText` yeni metinle çalışır, `setValue` state'i günceller, input yeni değeri gösterir. **Web'den farkı:** `onChange` yerine `onChangeText` kullanılır ve fonksiyona doğrudan metin gelir, `e.target.value` gerekmez.

---

## ⌨️ TextInput hızlı tablo

| Prop | Ne yapar | Değerler / örnek |
|---|---|---|
| `value` + `onChangeText` | Kontrollü input | yukarıdaki kalıp |
| `placeholder` | Boşken görünen ipucu yazısı | `placeholder="E-posta"` |
| `placeholderTextColor` | İpucu yazısının rengi | `"#94a3b8"` |
| `keyboardType` | Klavye tipi | `'default'`, `'email-address'`, `'numeric'`, `'phone-pad'` |
| `secureTextEntry` | Şifre gibi gizler (●●●) | sadece `secureTextEntry` yazmak yeterli |
| `autoCapitalize` | Otomatik büyük harf | `'none'`, `'sentences'`, `'words'`, `'characters'` |
| `autoCorrect` | Otomatik düzeltme | `{false}` |
| `maxLength` | En fazla karakter | `maxLength={11}` |
| `multiline` | Çok satırlı alan | `multiline` |
| `returnKeyType` | Klavyedeki enter tuşunun yazısı | `'done'`, `'go'`, `'next'`, `'search'`, `'send'` |
| `editable` | Yazılabilir mi | `editable={false}` → kilitli |

| Event | Ne zaman çalışır |
|---|---|
| `onChangeText` | Her harf yazıldığında (yeni metni verir) |
| `onFocus` | Input'a tıklanıp seçildiğinde |
| `onBlur` | Input'tan çıkıldığında |
| `onSubmitEditing` | Klavyede enter'a basıldığında |

---

## 📦 Temel Kod 1 – Anında Doğrulanan E-posta Alanı

```jsx
import { useState } from 'react';
import { SafeAreaView, Text, TextInput, View } from 'react-native';

// E-posta formatı kuralı (regex): "bir şey @ bir şey . bir şey", boşluk yok
const EMAIL_REGEX = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

export default function EmailField() {
  // Kullanıcının yazdığı e-posta
  const [value, setValue] = useState('');
  // Yazılan, kurala uyuyor mu? (.test → true/false). State değil, her render'da hesaplanır
  const isValid = EMAIL_REGEX.test(value);

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
        {/* KART: koyu gri */}
        <View
          style={{
            borderRadius: 16,
            backgroundColor: '#1f2937',
            padding: 24,
            gap: 16,
          }}
        >
          {/* Başlık */}
          <Text style={{ fontSize: 20, fontWeight: '600', color: '#f1f5f9' }}>
            Verify your email address
          </Text>

          {/* E-POSTA INPUT'U */}
          <TextInput
            value={value}
            onChangeText={setValue}
            keyboardType="email-address"   // klavyede @ tuşu hazır gelir
            autoCapitalize="none"          // ilk harf otomatik büyük olmasın
            placeholder="name@example.com"
            placeholderTextColor="#94a3b8"
            style={{
              borderWidth: 1,
              // KENARLIK RENGİ – iç içe ternary:
              // boşsa → gri, değilse: geçerliyse → yeşil, değilse → kırmızı
              borderColor:
                value.length === 0
                  ? '#334155'
                  : isValid
                  ? '#22c55e'
                  : '#ef4444',
              borderRadius: 10,
              paddingHorizontal: 14,
              paddingVertical: 12,
              color: '#f8fafc',            // yazılan yazının rengi
            }}
          />

          {/* DURUM MESAJI – yine iç içe ternary, 3 durum */}
          <Text style={{ color: isValid ? '#22c55e' : '#f97316' }}>
            {value.length === 0
              ? 'Start typing your email address.'
              : isValid
              ? 'Looks like a valid address.'
              : 'Please enter a valid email address.'}
          </Text>
        </View>
      </View>
    </SafeAreaView>
  );
}
```

> **İç içe ternary nasıl okunur?** `A ? x : B ? y : z` → "A doğruysa x; değilse, B doğruysa y; o da değilse z". Yani bir if / else if / else zinciri.

### Olası sorular – Kod 1

**S1. Başlığı, placeholder'ı ve kart rengini değiştir**
```jsx
E-posta adresini doğrula                 {/* 👈 başlık */}
placeholder="ornek@mail.com"             // 👈
backgroundColor: '#1e3a8a',              // 👈 KART View'ında '#1f2937' → lacivert
```

**S2. Geçerli adres girilince kenarlık yeşil yerine mavi olsun**
```jsx
borderColor:
  value.length === 0
    ? '#334155'
    : isValid
    ? '#3b82f6'     // 👈 '#22c55e' → mavi
    : '#ef4444',
```

**S3. Durum mesajlarını Türkçe yap**
```jsx
{value.length === 0
  ? 'E-posta adresini yazmaya başla.'
  : isValid
  ? 'Geçerli bir adres gibi görünüyor.'
  : 'Lütfen geçerli bir e-posta gir.'}
```

**S4. E-postanın altına şifre alanı ekle**
```jsx
// state'lerin yanına:
const [password, setPassword] = useState('');                 // 👈 YENİ

{/* E-posta TextInput'unun ALTINA */}
<TextInput
  value={password}
  onChangeText={setPassword}
  secureTextEntry                                              // 👈 yazıyı ●●● gizler
  placeholder="Şifre"
  placeholderTextColor="#94a3b8"
  style={{
    borderWidth: 1,
    borderColor: password.length >= 6 ? '#22c55e' : '#334155', // 👈 6+ karakter → yeşil
    borderRadius: 10,
    paddingHorizontal: 14,
    paddingVertical: 12,
    color: '#f8fafc',
  }}
/>
```

**S5. E-posta yerine telefon numarası alanı yap (05 ile başlayan 11 hane)**
```jsx
const PHONE_REGEX = /^05\d{9}$/;            // 👈 "05" + 9 rakam = 11 hane
const isValid = PHONE_REGEX.test(value);    // 👈 EMAIL_REGEX yerine

// TextInput'ta:
keyboardType="phone-pad"                    // 👈 rakam klavyesi
maxLength={11}                              // 👈 11'den fazla yazılamasın
placeholder="05XXXXXXXXX"
```

**S6. Sadece belirli bir uzantıyı kabul et (ör. @itu.edu.tr)**
```jsx
const isValid = EMAIL_REGEX.test(value) && value.endsWith('@itu.edu.tr');   // 👈 iki koşul da sağlanmalı
```

**S7. Gönder butonu ekle: adres geçersizken pasif, basınca uyarı çıksın**
```jsx
import { Alert, Button, SafeAreaView, Text, TextInput, View } from 'react-native';   // 👈 Alert, Button

{/* Durum mesajı Text'inin ALTINA */}
<Button
  title="Gönder"
  disabled={!isValid}                                          // 👈 geçersizken basılamaz (gri görünür)
  onPress={() => Alert.alert('Başarılı', `${value} kaydedildi`)}
/>
```

**S8. Input seçiliyken (odaktayken) kenarlık mavi olsun**
```jsx
const [isFocused, setIsFocused] = useState(false);            // 👈 YENİ

<TextInput
  ...
  onFocus={() => setIsFocused(true)}                          // 👈 tıklanınca
  onBlur={() => setIsFocused(false)}                          // 👈 çıkınca
  style={{
    borderWidth: isFocused ? 2 : 1,                           // 👈 odaktayken kalın
    borderColor:
      value.length === 0
        ? (isFocused ? '#3b82f6' : '#334155')                 // 👈 boş + odakta → mavi
        : isValid
        ? '#22c55e'
        : '#ef4444',
    ...
```

**S9. Ad alanı ekle ve altta "Merhaba, Ad" yazsın**
```jsx
const [name, setName] = useState('');                         // 👈 YENİ

{/* Başlığın ALTINA */}
<TextInput
  value={name}
  onChangeText={setName}
  placeholder="Adın"
  placeholderTextColor="#94a3b8"
  style={{ borderWidth: 1, borderColor: '#334155', borderRadius: 10, padding: 12, color: '#f8fafc' }}
/>
{name.length > 0 && (                                          // 👈 isim yazıldıysa göster
  <Text style={{ color: '#f1f5f9' }}>Merhaba, {name}!</Text>
)}
```

**S10. Input'u temizleyen buton**
```jsx
<Button title="Temizle" onPress={() => setValue('')} />       // 👈 state boşalınca input da boşalır
```


### 🔐 Şifre ve form soruları (S11–S15 birbirinin üzerine kurulur)

> Bu sorular Kod 1'in üzerine eklenir. S4'ü yaptıysan onun yerine bunları kullan (aynı `password` state'i).

**S11. Şifre alanı ekle: en az 8 karakter olsun, değilse uyarı çıksın**
```jsx
// state'lerin yanına:
const [password, setPassword] = useState('');                  // 👈 YENİ
const isPasswordValid = password.length >= 8;                  // 👈 kural: en az 8 karakter (sayıyı buradan değiştir)

{/* E-posta durum mesajının ALTINA */}
<TextInput
  value={password}
  onChangeText={setPassword}
  secureTextEntry                                              // 👈 ●●● gizler
  placeholder="Şifre (en az 8 karakter)"
  placeholderTextColor="#94a3b8"
  style={{
    borderWidth: 1,
    borderColor:
      password.length === 0
        ? '#334155'                                            // boş → gri
        : isPasswordValid
        ? '#22c55e'                                            // geçerli → yeşil
        : '#ef4444',                                           // kısa → kırmızı
    borderRadius: 10,
    paddingHorizontal: 14,
    paddingVertical: 12,
    color: '#f8fafc',
  }}
/>
{password.length > 0 && !isPasswordValid && (                  // 👈 yazmaya başladı AMA kısa ise göster
  <Text style={{ color: '#ef4444' }}>
    Şifre en az 8 karakter olmalı ({password.length}/8)       {/* 👈 kaç karakter yazdığını da gösterir */}
  </Text>
)}
```
> Aynı mantık e-postadakiyle aynı: kural → `isPasswordValid` → renk + mesaj. Hoca "10 karakter olsun" derse sadece `>= 8`'deki ve mesajdaki 8'i değiştirirsin.

**S12. Şifreyi göster / gizle butonu**
```jsx
import { SafeAreaView, Text, TextInput, TouchableOpacity, View } from 'react-native';   // 👈 TouchableOpacity

const [showPassword, setShowPassword] = useState(false);       // 👈 YENİ: şifre görünüyor mu?

{/* Şifre TextInput'unu row View ile SAR */}
<View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
  <TextInput
    value={password}
    onChangeText={setPassword}
    secureTextEntry={!showPassword}                            // 👈 gösterilmiyorsa gizle
    placeholder="Şifre"
    placeholderTextColor="#94a3b8"
    style={{
      flex: 1,                                                 // 👈 butona yer kalsın diye
      borderWidth: 1,
      borderColor: '#334155',
      borderRadius: 10,
      paddingHorizontal: 14,
      paddingVertical: 12,
      color: '#f8fafc',
    }}
  />
  <TouchableOpacity onPress={() => setShowPassword((s) => !s)}>   {/* 👈 true ↔ false */}
    <Text style={{ color: '#38bdf8' }}>{showPassword ? 'Gizle' : 'Göster'}</Text>
  </TouchableOpacity>
</View>
```

**S13. Şifre tekrar alanı: iki şifre aynı olmalı**
```jsx
const [confirm, setConfirm] = useState('');                    // 👈 YENİ
const passwordsMatch = confirm === password;                   // 👈 aynı mı? (=== ile karşılaştır)

{/* Şifre alanının ALTINA */}
<TextInput
  value={confirm}
  onChangeText={setConfirm}
  secureTextEntry
  placeholder="Şifre tekrar"
  placeholderTextColor="#94a3b8"
  style={{
    borderWidth: 1,
    borderColor:
      confirm.length === 0 ? '#334155' : passwordsMatch ? '#22c55e' : '#ef4444',
    borderRadius: 10,
    paddingHorizontal: 14,
    paddingVertical: 12,
    color: '#f8fafc',
  }}
/>
{confirm.length > 0 && !passwordsMatch && (
  <Text style={{ color: '#ef4444' }}>Şifreler eşleşmiyor</Text>
)}
```

**S14. Şifre kuralları: en az 8 karakter + 1 büyük harf + 1 rakam (✓ / ✗ listesi)**
```jsx
// isPasswordValid satırının YERİNE:
const hasMinLength = password.length >= 8;                     // 👈 uzunluk kuralı
const hasUpper = /[A-Z]/.test(password);                       // 👈 en az 1 büyük harf var mı?
const hasNumber = /\d/.test(password);                         // 👈 en az 1 rakam var mı? (\d = rakam)
const isPasswordValid = hasMinLength && hasUpper && hasNumber; // 👈 HEPSİ sağlanmalı (&&)

{/* Şifre alanının ALTINA – her kural için bir satır */}
<Text style={{ color: hasMinLength ? '#22c55e' : '#ef4444' }}>
  {hasMinLength ? '✓' : '✗'} En az 8 karakter
</Text>
<Text style={{ color: hasUpper ? '#22c55e' : '#ef4444' }}>
  {hasUpper ? '✓' : '✗'} En az 1 büyük harf
</Text>
<Text style={{ color: hasNumber ? '#22c55e' : '#ef4444' }}>
  {hasNumber ? '✓' : '✗'} En az 1 rakam
</Text>
```
> Küçük harf kuralı istenirse: `/[a-z]/.test(password)`. Özel karakter istenirse: `/[!@#$%^&*.]/.test(password)`. Yeni kuralı `isPasswordValid`'e `&&` ile eklemeyi unutma.

**S15. Kayıt butonu: e-posta + şifre + şifre tekrar hepsi doğruysa aktif olsun**
```jsx
import { Alert, Button, SafeAreaView, Text, TextInput, TouchableOpacity, View } from 'react-native';   // 👈 Alert, Button

// hesaplanan değerlerin ALTINA:
const canSubmit = isValid && isPasswordValid && passwordsMatch && confirm.length > 0;   // 👈 tüm koşullar

{/* Kartın en altına */}
<Button
  title="Kayıt Ol"
  disabled={!canSubmit}                                        // 👈 bir koşul bile eksikse pasif
  onPress={() => Alert.alert('Başarılı', `${value} ile kayıt olundu`)}
/>
```
> `confirm.length > 0` neden var? Şifre ve tekrar ikisi de boşken `'' === ''` true döner ve buton yanlışlıkla aktif olurdu.

### ➕ Diğer olası sorular (S16–S19)

**S16. Klavyede Enter'a basınca gönderilsin**

*Soru ne istiyor:* Butona basmak yerine, kullanıcı e-postayı yazıp klavyedeki Enter/Gönder tuşuna basınca işlem yapılsın. Bunun için TextInput'un `onSubmitEditing` event'i kullanılır.
```jsx
import { Alert, SafeAreaView, Text, TextInput, View } from 'react-native';   // 👈 Alert

// E-posta TextInput'una iki satır ekle:
<TextInput
  value={value}
  onChangeText={setValue}
  returnKeyType="send"                                         // 👈 Enter tuşunun yazısı "Gönder" olsun
  onSubmitEditing={() => {                                     // 👈 Enter'a basınca çalışır
    if (isValid) {
      Alert.alert('Gönderildi', value);
    } else {
      Alert.alert('Hata', 'Geçerli bir e-posta gir');
    }
  }}
  keyboardType="email-address"
  ...
```
> Snack'in web önizlemesinde bilgisayar klavyesindeki Enter tuşu da bunu tetikler.

**S17. Yaş alanı ekle: sadece rakam yazılsın, 18–99 arası olsun**

*Soru ne istiyor:* Yazının formatını değil, **sayının değerini** kontrol etmek. TextInput her zaman yazı (string) verdiği için önce `Number()` ile sayıya çevrilir, sonra `>=` ve `<=` ile karşılaştırılır.
```jsx
const [age, setAge] = useState('');                            // 👈 YENİ (input hep string tutar)
const ageNumber = Number(age);                                 // 👈 "25" → 25
const isAgeValid = age.length > 0 && ageNumber >= 18 && ageNumber <= 99;   // 👈 boş değil VE aralıkta

<TextInput
  value={age}
  onChangeText={(text) => setAge(text.replace(/[^0-9]/g, ''))} // 👈 rakam dışındaki her şeyi siler
  keyboardType="numeric"                                       // 👈 telefonda rakam klavyesi açılır
  maxLength={2}                                                // 👈 en fazla 2 hane
  placeholder="Yaş"
  placeholderTextColor="#94a3b8"
  style={{
    borderWidth: 1,
    borderColor: age.length === 0 ? '#334155' : isAgeValid ? '#22c55e' : '#ef4444',
    borderRadius: 10,
    paddingHorizontal: 14,
    paddingVertical: 12,
    color: '#f8fafc',
  }}
/>
{age.length > 0 && !isAgeValid && (
  <Text style={{ color: '#ef4444' }}>Yaş 18 ile 99 arasında olmalı</Text>
)}
```
> `age.length > 0` neden var? `Number('')` sıfır verir, boş alan "0 yaş" gibi değerlendirilmesin diye.
> `replace(/[^0-9]/g, '')` neden var? `keyboardType="numeric"` sadece telefonda rakam klavyesi açar, bilgisayardan (Snack web) harf yazılabilir. Bu satır harfleri anında siler.

**S18. Kullanıcı adı alanı: boşluk olmasın, küçük harf olsun, en az 3 karakter**

*Soru ne istiyor:* S14'teki şifre kurallarının aynısı, sadece farklı kurallarla.
```jsx
const [username, setUsername] = useState('');                         // 👈 YENİ
const noSpaces = !/\s/.test(username);                                // 👈 \s = boşluk. "boşluk YOK mu?"
const isLowercase = username === username.toLowerCase();              // 👈 küçük harfe çevrilince aynı kalıyorsa zaten küçük harftir
const longEnough = username.length >= 3;                              // 👈
const isUsernameValid = noSpaces && isLowercase && longEnough;        // 👈 hepsi

<TextInput
  value={username}
  onChangeText={setUsername}
  autoCapitalize="none"                                               // 👈 ilk harf otomatik büyümesin
  autoCorrect={false}                                                 // 👈 otomatik düzeltme kapalı
  placeholder="Kullanıcı adı"
  placeholderTextColor="#94a3b8"
  style={{
    borderWidth: 1,
    borderColor: username.length === 0 ? '#334155' : isUsernameValid ? '#22c55e' : '#ef4444',
    borderRadius: 10,
    paddingHorizontal: 14,
    paddingVertical: 12,
    color: '#f8fafc',
  }}
/>
<Text style={{ color: noSpaces ? '#22c55e' : '#ef4444' }}>{noSpaces ? '✓' : '✗'} Boşluk içermez</Text>
<Text style={{ color: isLowercase ? '#22c55e' : '#ef4444' }}>{isLowercase ? '✓' : '✗'} Sadece küçük harf</Text>
<Text style={{ color: longEnough ? '#22c55e' : '#ef4444' }}>{longEnough ? '✓' : '✗'} En az 3 karakter</Text>
```
> Alternatif: hata göstermek yerine yazılanı otomatik düzelt:
> `onChangeText={(text) => setUsername(text.toLowerCase().replace(/\s/g, ''))}` → büyük harfleri küçültür, boşlukları siler.

**S19 (zor). Yazılanı listeye ekle / sil (yapılacaklar listesi)**

*Soru ne istiyor:* Input'a bir şey yazıp "Ekle"ye basınca altta bir listeye eklensin, her satırın yanında silme butonu olsun. Bu soru TextInput + state'te dizi tutma + FlatList'i birleştirir. **Tam kod** olduğu için `App.js`'in tamamının yerine yapıştırılır.
```jsx
import { useState } from 'react';
import { Button, FlatList, SafeAreaView, Text, TextInput, TouchableOpacity, View } from 'react-native';

export default function TodoList() {
  const [text, setText] = useState('');     // input'a yazılan
  const [items, setItems] = useState([]);   // eklenenlerin listesi (başta boş dizi)

  // EKLE: yazı boş değilse listeye yeni eleman ekle, input'u temizle
  const addItem = () => {
    if (text.trim().length === 0) return;                    // sadece boşluk yazılmışsa ekleme (trim baştaki/sondaki boşlukları siler)
    setItems((prev) => [
      ...prev,                                                // eski elemanların hepsi...
      { id: Date.now().toString(), title: text.trim() },      // ...+ yeni eleman (id = o anki zaman, hep benzersiz)
    ]);
    setText('');                                              // input'u boşalt
  };

  // SİL: id'si tıklanan olmayanları tut (= tıklananı çıkar)
  const removeItem = (id) => {
    setItems((prev) => prev.filter((item) => item.id !== id));
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 22, fontWeight: '700', color: '#0f172a' }}>
          Yapılacaklar
        </Text>

        {/* Input + Ekle butonu yan yana */}
        <View style={{ flexDirection: 'row', gap: 8, alignItems: 'center' }}>
          <TextInput
            value={text}
            onChangeText={setText}
            onSubmitEditing={addItem}                         // Enter'a basınca da eklesin
            placeholder="Yeni görev yaz..."
            style={{
              flex: 1,
              borderWidth: 1,
              borderColor: '#cbd5f5',
              borderRadius: 12,
              paddingHorizontal: 16,
              paddingVertical: 12,
              backgroundColor: '#fff',
            }}
          />
          <Button title="Ekle" onPress={addItem} />
        </View>

        {/* Kaç eleman var */}
        <Text style={{ color: '#475569' }}>{items.length} görev</Text>

        {/* LİSTE */}
        <FlatList
          data={items}
          keyExtractor={(item) => item.id}
          ListEmptyComponent={<Text style={{ color: '#94a3b8' }}>Henüz görev yok.</Text>}
          ItemSeparatorComponent={() => <View style={{ height: 8 }} />}
          renderItem={({ item }) => (
            <View
              style={{
                flexDirection: 'row',                         // yazı solda, ✕ sağda
                justifyContent: 'space-between',
                alignItems: 'center',
                padding: 14,
                borderRadius: 12,
                backgroundColor: '#e0f2fe',
              }}
            >
              <Text style={{ color: '#0f172a' }}>{item.title}</Text>
              <TouchableOpacity onPress={() => removeItem(item.id)}>
                <Text style={{ color: '#ef4444', fontSize: 18 }}>✕</Text>
              </TouchableOpacity>
            </View>
          )}
        />
      </View>
    </SafeAreaView>
  );
}
```
> **Neden `items.push(...)` değil de `[...prev, yeni]`?** W3'teki kural: state doğrudan değiştirilmez. `push` aynı diziyi değiştirdiği için React değişikliği fark etmez ve ekran güncellenmez. `[...prev, yeni]` **yeni bir dizi** oluşturur.
> **Silme mantığı:** `filter` sadece koşulu sağlayanları tutar. "id'si silinen id'ye eşit OLMAYANLAR kalsın" demek, o elemanı çıkarmak demek.

---

## 📦 Temel Kod 2 – Listeyi Filtreleyen Arama Çubuğu

```jsx
import { useMemo, useState } from 'react';
import { FlatList, SafeAreaView, Text, TextInput, View } from 'react-native';

// Aranacak dersler – her birinin benzersiz bir id'si var
const LESSONS = [
  { id: '1', title: 'Intro to React Native' },
  { id: '2', title: 'Working with Expo' },
  { id: '3', title: 'Navigation Basics' },
  { id: '4', title: 'State Management' },
  { id: '5', title: 'Performance Tips' },
];

export default function SearchableList() {
  // Arama kutusuna yazılan
  const [query, setQuery] = useState('');

  // Filtrelenmiş liste: sadece query değişince yeniden hesaplanır (useMemo)
  const filteredLessons = useMemo(() => {
    const lowered = query.toLowerCase();             // aramayı küçük harfe çevir
    return LESSONS.filter((lesson) =>
      lesson.title.toLowerCase().includes(lowered)   // başlık (küçük harf) aranan kelimeyi İÇERİYOR mu?
    );
  }, [query]);

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, padding: 24, gap: 16 }}>
        {/* Başlık */}
        <Text style={{ fontSize: 22, fontWeight: '700', color: '#0f172a' }}>
          Search the lesson archive
        </Text>

        {/* ARAMA KUTUSU */}
        <TextInput
          value={query}
          onChangeText={setQuery}
          placeholder="Search lessons..."
          returnKeyType="search"     // klavyedeki enter tuşu "Ara" yazar
          style={{
            borderWidth: 1,
            borderColor: '#cbd5f5',
            borderRadius: 12,
            paddingHorizontal: 16,
            paddingVertical: 14,
            backgroundColor: '#fff',
          }}
        />

        {/* LİSTE: filtrelenmiş dersleri gösterir */}
        <FlatList
          data={filteredLessons}                    // gösterilecek dizi
          keyExtractor={(item) => item.id}          // her satırın key'i
          // Sonuç yoksa gösterilecek kutu
          ListEmptyComponent={
            <View
              style={{
                padding: 16,
                borderRadius: 12,
                backgroundColor: '#e0f2fe',
              }}
            >
              <Text style={{ color: '#1f2937' }}>
                No lessons match your search.
              </Text>
            </View>
          }
          // Her bir ders nasıl görünecek
          renderItem={({ item }) => (
            <View
              style={{
                padding: 16,
                borderRadius: 12,
                backgroundColor: '#e0f2fe',
              }}
            >
              <Text style={{ fontWeight: '600', color: '#0f172a' }}>
                {item.title}
              </Text>
            </View>
          )}
          // Satırlar arasına 12 yüksekliğinde boşluk
          ItemSeparatorComponent={() => <View style={{ height: 12 }} />}
          contentContainerStyle={{ paddingBottom: 24 }}
        />
      </View>
    </SafeAreaView>
  );
}
```

> **Neden ikisi de küçük harfe çevriliyor?** Kullanıcı "expo" yazsa da "EXPO" yazsa da "Working with Expo" bulunsun diye. Arama büyük/küçük harfe duyarsız olur.
>
> `FlatList` ayrı bir dosyada detaylı işlenecek. Burada bilmen gereken: `data` → dizi, `renderItem` → her eleman nasıl görünecek, `keyExtractor` → key.

### Olası sorular – Kod 2

**S1. Listeye yeni ders ekle**
```jsx
{ id: '6', title: 'Animations' },   // 👈 id benzersiz olmalı, virgüle dikkat
```

**S2. Türkçe ders isimleri ekle (ve aramanın düzgün çalışmasını sağla)**
```jsx
{ id: '6', title: 'İleri Seviye Hooklar' },

// ⚠️ 'İ'.toLowerCase() Türkçe'de düzgün çalışmaz ("ileri" araması "İleri"yi bulamaz). Çözüm:
const lowered = query.toLocaleLowerCase('tr');                       // 👈
lesson.title.toLocaleLowerCase('tr').includes(lowered)               // 👈
```

**S3. Bulunan ders sayısını göster**
```jsx
{/* TextInput'un ALTINA, FlatList'in ÜSTÜNE */}
<Text style={{ color: '#475569' }}>
  {filteredLessons.length} ders bulundu       {/* 👈 dizinin uzunluğu */}
</Text>
```

**S4. "İçeren" değil "ile başlayan" dersleri bulsun**
```jsx
lesson.title.toLowerCase().startsWith(lowered)   // 👈 includes → startsWith
```

**S5. Sonuç bulunamadı mesajını Türkçe ve kırmızı yap**
```jsx
ListEmptyComponent={
  <View style={{ padding: 16, borderRadius: 12, backgroundColor: '#fee2e2' }}>   {/* 👈 açık kırmızı */}
    <Text style={{ color: '#b91c1c' }}>Aramana uygun ders bulunamadı.</Text>   {/* 👈 */}
  </View>
}
```

**S6. Ders kartlarının rengini ve aralarındaki boşluğu değiştir**
```jsx
backgroundColor: '#dcfce7',                                        // 👈 renderItem'daki View
ItemSeparatorComponent={() => <View style={{ height: 20 }} />}     // 👈 12 → 20
```

**S7. Her derse süre bilgisi ekle ve göster**
```jsx
const LESSONS = [
  { id: '1', title: 'Intro to React Native', duration: 45 },      // 👈 her elemana yeni alan
  { id: '2', title: 'Working with Expo', duration: 30 },
  // ...
];

// renderItem içinde, başlık Text'inin ALTINA:
<Text style={{ color: '#475569' }}>{item.duration} dk</Text>       // 👈 item.yeniAlan
```

**S8. Arama kutusunun sağında temizle (✕) butonu olsun**
```jsx
import { FlatList, SafeAreaView, Text, TextInput, TouchableOpacity, View } from 'react-native';   // 👈

{/* TextInput'u bir row View ile SAR */}
<View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
  <TextInput
    value={query}
    onChangeText={setQuery}
    placeholder="Search lessons..."
    returnKeyType="search"
    style={{
      flex: 1,                          // 👈 YENİ: kalan genişliği doldursun, buton yanına sığsın
      borderWidth: 1,
      borderColor: '#cbd5f5',
      borderRadius: 12,
      paddingHorizontal: 16,
      paddingVertical: 14,
      backgroundColor: '#fff',
    }}
  />
  {query.length > 0 && (                // 👈 sadece yazı varken görünür
    <TouchableOpacity onPress={() => setQuery('')}>
      <Text style={{ fontSize: 20, color: '#64748b' }}>✕</Text>
    </TouchableOpacity>
  )}
</View>
```

**S9. Bir derse tıklayınca adını uyarı olarak göster**
```jsx
import { Alert, FlatList, SafeAreaView, Text, TextInput, TouchableOpacity, View } from 'react-native';

renderItem={({ item }) => (
  <TouchableOpacity                                         // 👈 View → TouchableOpacity
    onPress={() => Alert.alert('Seçilen ders', item.title)}  // 👈
    style={{ padding: 16, borderRadius: 12, backgroundColor: '#e0f2fe' }}
  >
    <Text style={{ fontWeight: '600', color: '#0f172a' }}>{item.title}</Text>
  </TouchableOpacity>
)}
```


**S10 (zor). Kategoriye göre filtre butonları ekle (Tümü / Temel / İleri)**

*Soru ne istiyor:* Arama kutusuna ek olarak üstte butonlar olsun, seçilen kategoriye göre de filtrelensin. Filtre artık **iki koşullu** çalışır: yazı eşleşmeli **VE** kategori eşleşmeli.
```jsx
import { FlatList, SafeAreaView, Text, TextInput, TouchableOpacity, View } from 'react-native';   // 👈 TouchableOpacity

// 1) Her derse kategori alanı ekle
const LESSONS = [
  { id: '1', title: 'Intro to React Native', level: 'Temel' },      // 👈
  { id: '2', title: 'Working with Expo', level: 'Temel' },
  { id: '3', title: 'Navigation Basics', level: 'Temel' },
  { id: '4', title: 'State Management', level: 'İleri' },
  { id: '5', title: 'Performance Tips', level: 'İleri' },
];
const CATEGORIES = ['Tümü', 'Temel', 'İleri'];                      // 👈 YENİ: butonlar

// 2) component içinde yeni state
const [category, setCategory] = useState('Tümü');                  // 👈 seçili kategori

// 3) filtreye ikinci koşulu ekle
const filteredLessons = useMemo(() => {
  const lowered = query.toLowerCase();
  return LESSONS.filter(
    (lesson) =>
      lesson.title.toLowerCase().includes(lowered) &&               // yazı eşleşiyor VE
      (category === 'Tümü' || lesson.level === category)            // 👈 (Tümü seçili YA DA kategori aynı)
  );
}, [query, category]);                                              // 👈 category de eklendi! Yoksa butonlar çalışmaz

// 4) TextInput'un ALTINA butonlar
<View style={{ flexDirection: 'row', gap: 8 }}>
  {CATEGORIES.map((cat) => {
    const isActive = cat === category;                              // bu buton seçili mi?
    return (
      <TouchableOpacity
        key={cat}
        onPress={() => setCategory(cat)}
        style={{
          paddingVertical: 8,
          paddingHorizontal: 14,
          borderRadius: 999,
          backgroundColor: isActive ? '#2563eb' : '#e2e8f0',         // seçiliyse mavi
        }}
      >
        <Text style={{ color: isActive ? '#fff' : '#0f172a' }}>{cat}</Text>
      </TouchableOpacity>
    );
  })}
</View>
```
> En kolay atlanan yer: `useMemo`'nun bağımlılık dizisine `category`'yi eklemek. Eklenmezse useMemo sadece `query` değişince yeniden hesaplar, butona basınca liste değişmez.
> Buton yapısı Image dosyasındaki resizeMode butonlarının aynısı (`map` + `isActive`).

---

## ⚠️ Sık hatalar
- `onChangeText`'e `setValue()` (parantezli) yazmak → yanlış. Fonksiyonun kendisi verilir: `onChangeText={setValue}`.
- `value` verip `onChangeText` vermemek → input'a hiçbir şey yazılamaz (state hiç değişmez).
- Koyu arka planda yazı rengini (`color`) vermeyi unutmak → yazılan metin görünmez.
- `secureTextEntry` ile `multiline` aynı anda kullanılamaz.
- Yeni bir alan eklerken her biri için **ayrı state** aç (`email`, `password`, `name`...).
