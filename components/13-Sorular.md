# 13 – Sorular 


---



### Q1 a) Explain the difference between `onChange` and `onEndEditing` events of the TextInput component. 

> **Answer:**
> *`onChange` is called **every time the text changes**, so it fires on each keystroke while the user is typing. It receives an event object, and the current text is accessed with `event.nativeEvent.text`.*
>
> *`onEndEditing` is called **only once, when the user finishes editing**, for example when the input loses focus or the keyboard is dismissed. It is useful for validating or saving the final value instead of reacting to every keystroke.*
>
> *In short: `onChange` → during typing (many times), `onEndEditing` → after typing is finished (once).*

```jsx
<TextInput
  onChange={(event) => console.log('typing:', event.nativeEvent.text)}     // her harfte
  onEndEditing={(event) => console.log('done:', event.nativeEvent.text)}   // yazma bitince bir kez
/>
```

> **Ek bilgi (puan kazandırabilir):** *`onChangeText` is similar to `onChange`, but it receives the **text string directly** instead of an event object, so it is the most common choice for controlled inputs: `onChangeText={setValue}`. `onSubmitEditing` is called when the user presses the submit/return key on the keyboard.*

| Event | When it fires | What it receives |
|---|---|---|
| `onChange` | On every text change | Event object (`e.nativeEvent.text`) |
| `onChangeText` | On every text change | The text string directly |
| `onEndEditing` | Once, when editing ends (blur / keyboard closed) | Event object (`e.nativeEvent.text`) |
| `onSubmitEditing` | When the keyboard's submit key is pressed | Event object |
| `onFocus` / `onBlur` | When the input gains / loses focus | Event object |

---

### Q1 b) Explain the purpose of the `keyboardType` prop. List three possible values it can take. 

> **Answer:**
> *The `keyboardType` prop determines **which type of on-screen keyboard is opened** when the user taps the TextInput. Showing a keyboard that matches the expected input makes typing faster and reduces input errors. For example, a phone number field can open a numeric keypad instead of the full alphabet keyboard.*
>
> *Possible values:*
> - *`'default'` – the standard keyboard with letters*
> - *`'numeric'` – a keyboard with numbers*
> - *`'email-address'` – a keyboard with `@` and `.` keys for email input*
> - *`'phone-pad'` – a phone dial pad*
> - *`'number-pad'` – digits only (no decimal point)*
> - *`'decimal-pad'` – digits with a decimal point*
>
> *Note: `keyboardType` only changes the keyboard layout; it **does not validate** the input. The value still needs to be checked in code.*

> Soruda üç değer isteniyor, en güvenli üçlü: `default`, `numeric`, `email-address`. Fazladan yazmak zarar vermez.
> Son cümle (doğrulama yapmaz) önemli: Q2'de "sadece sayı" istendiğinde neden ayrıca `replace` kullandığımızı açıklıyor.

---

### Q2) Joule → cal / kcal çevirici 

**Soru özeti:** Joule (J) değerini calorie (cal) ve kilocalorie (kcal) değerlerine çeviren bir uygulama. Input sadece sayı kabul etmeli. Convert'e basınca `1 cal = 4.184 J` ve `1 kcal = 4184 J` formülleriyle hesaplanıp sonuçlar gösterilmeli. Reset'e basınca Joule değeri temizlenmeli.

**Puanlama:** UI tasarımı (20p) · sadece sayı + doğru formül (10p) · sonuçların gösterilmesi (10p) · Reset (10p)

```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function JouleConverter() {
  const [joule, setJoule] = useState('');      // input'taki Joule değeri (yazı olarak)
  const [result, setResult] = useState(null);  // sonuç: { cal, kcal } ya da null (henüz hesaplanmadı)

  // CONVERT: Joule'ü cal ve kcal'e çevir
  const handleConvert = () => {
    const j = Number(joule);
    if (!Number.isFinite(j)) {                 // sayı değilse sonuç gösterme
      setResult(null);
      return;
    }
    setResult({
      cal: j / 4.184,                          // 1 cal = 4.184 J  → cal = J / 4.184
      kcal: j / 4184,                          // 1 kcal = 4184 J  → kcal = J / 4184
    });
  };

  // RESET: input'u ve sonucu temizle
  const handleReset = () => {
    setJoule('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Joule to → cal / kcal Converter
        </Text>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#0f172a', textAlign: 'center' }}>
          Joule
        </Text>

        <TextInput
          placeholder="Joule"
          keyboardType="numeric"                                  // telefonda rakam klavyesi
          value={joule}
          onChangeText={(text) => {
            setJoule(text.replace(/[^0-9.]/g, ''));               // rakam ve nokta dışındakileri sil (sadece sayı)
            setResult(null);                                      // sayı değişince eski sonucu sil
          }}
          style={{
            borderWidth: 1,
            borderColor: '#d4d4d8',
            borderRadius: 10,
            paddingHorizontal: 12,
            paddingVertical: 12,
            backgroundColor: '#fff',
          }}
        />

        <Button title="Convert" onPress={handleConvert} disabled={joule === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {/* Sonuç: sadece hesaplandıysa görünür */}
        {result !== null && (
          <View style={{ gap: 4 }}>
            <Text style={{ fontSize: 16, color: '#0f172a' }}>cal: {result.cal.toFixed(6)}</Text>
            <Text style={{ fontSize: 16, color: '#0f172a' }}>kcal: {result.kcal.toFixed(5)}</Text>
          </View>
        )}
      </View>
    </SafeAreaView>
  );
}
```

> **Kontrol:** 50 J → cal: 11.950287 · kcal: 0.01195 (sınav kağıdındaki ekran görüntüsüyle aynı)

**Puan kaybettirebilecek hatalar:**
- Başlıkta `->` yazmak → JSX'te `>` kodu bozar. `→` veya `{'->'}` kullan.
- Sadece `keyboardType="numeric"` ile yetinmek → web'de harf yazılabilir. `replace(/[^0-9.]/g, '')` ekle.
- Sadece bir sonucu (kcal) hesaplamak → soru **ikisini** istiyor.
- Reset'te var olmayan bir state'i sıfırlamaya çalışmak (`setSecond` gibi) → hata.
- `J / 4184` ile `J * (1 / 4184)` aynı sonuçtur, ikisi de doğru.

---

## 🔁 Bölüm 2 – Aynı Tarzda Olası Kod Soruları

Q2'nin kodu bir **şablon**: tek input → Convert → iki sonuç → Reset. Benzer bir soru gelirse sadece şu dört şey değişir:

1. Başlık ve etiket yazıları
2. `handleConvert` içindeki **formüller**
3. Sonuçların adları (`result.cal` → `result.fahrenheit` gibi)
4. Gerekirse eksi sayı / ondalık izni

### Örnek A: Celsius → Fahrenheit / Kelvin
```jsx
// handleConvert İÇİNDE:
setResult({
  fahrenheit: j * 9 / 5 + 32,     // °F = °C × 9/5 + 32
  kelvin: j + 273.15,             // K = °C + 273.15
});

// Sonuç Text'leri:
<Text>°F: {result.fahrenheit.toFixed(2)}</Text>
<Text>K: {result.kelvin.toFixed(2)}</Text>

// ⚠️ Sıcaklık EKSİ olabilir → replace'te eksi işaretine de izin ver:
setJoule(text.replace(/[^0-9.-]/g, ''));     // 👈 sona "-" eklendi
```

### Örnek B: Kilometre → Mil / Metre
```jsx
setResult({
  mile: j / 1.609,                // 1 mil = 1.609 km
  meter: j * 1000,                // 1 km = 1000 m
});
```

### Örnek C: TL → USD / EUR (sabit kurla)
```jsx
const USD_RATE = 34.5;            // 👈 component DIŞINA (1 USD = 34.5 TL, soruda verilen kur)
const EUR_RATE = 37.2;

setResult({
  usd: j / USD_RATE,
  eur: j / EUR_RATE,
});
```

### Örnek D: Kilogram → Pound / Gram
```jsx
setResult({
  pound: j * 2.20462,             // 1 kg = 2.20462 lb
  gram: j * 1000,
});
```

> **Formül nasıl yazılır?** Soruda "1 X = N Y" verilirse:
> - Y'den X'e çeviriyorsan → **böl**: `X = Y / N` (Joule'den cal'e: 1 cal = 4.184 J → cal = J / 4.184)
> - X'ten Y'ye çeviriyorsan → **çarp**: `Y = X * N` (cal'den Joule'e: J = cal × 4.184)
>
> Emin olamazsan küçük bir sayıyla dene: 4.184 J tam 1 cal etmeli.

### Olası ek istekler (diğer dosyalardaki çözümlerle)
| İstek | Nasıl yapılır | Nerede |
|---|---|---|
| Boşken Convert basılamasın | `disabled={joule === ''}` | Button S7 |
| Sıfır / eksi girilirse uyarı | `if (j <= 0) { setErrorMsg('...'); return; }` | Button S13 |
| Enter'a basınca çevirsin | `onSubmitEditing={handleConvert}` | TextInput S16 |
| Sonuç 2 basamaklı olsun | `.toFixed(2)` | Button S9 |
| Sonuç renkli / kutu içinde | Sonuç View'ına `backgroundColor`, `padding`, `borderRadius` | View |
| Butonlar yan yana | `<View style={{ flexDirection: 'row', gap: 12 }}>` + her butonu `<View style={{ flex: 1 }}>` ile sar | Button Kod 2 S10 |
| Özel tasarımlı buton | `Button` yerine `TouchableOpacity` | Button S11 |
| Ters yönde çevirme seçeneği | İki Picker ya da radio grubu + seçime göre formül | Picker, RadioButton |

---

## 💬 Bölüm 3 – Olası Teorik Sorular

**1. What is the difference between props and state?**
> *Props are data passed from a parent component to a child component. They are **read-only**, so the child cannot change them. State is data that belongs to the component itself and **can change over time** with its setter function (e.g. `setCount`). When state changes, the component re-renders.*

**2. What does `useState` do? Explain with an example.**
> *`useState` is a React hook that adds state to a function component. It returns an array with two elements: the current value and a function to update it. Example: `const [count, setCount] = useState(0);` creates a state variable `count` with initial value `0`, and calling `setCount(count + 1)` updates it and re-renders the component.*

**3. What is the purpose of `useEffect`? What does the dependency array do?**
> *`useEffect` runs side effects such as timers, API calls or subscriptions after rendering. The dependency array controls when it runs: with no array it runs after every render, with an empty array `[]` it runs only once when the component mounts, and with values `[x]` it runs again whenever `x` changes. The function returned from `useEffect` is a cleanup function, used to stop timers or remove subscriptions.*

**4. What is the difference between ScrollView and FlatList?**
> *ScrollView renders all of its children at once, so it is suitable for small amounts of content. FlatList uses virtualization and renders only the items currently visible on the screen, which makes it more efficient for long or dynamic lists. FlatList takes `data`, `renderItem` and `keyExtractor` props.*

**5. Why is `keyExtractor` (or the `key` prop) needed in lists?**
> *Each item in a list needs a unique key so that React can identify which items were added, removed or changed. This allows React to update only the changed items instead of re-rendering the whole list. Using the array index as a key is not recommended when items can be added, removed or reordered.*

**6. What is the difference between Button and TouchableOpacity?**
> *Button is a simple built-in component that cannot be styled; it only supports `title`, `color` and `disabled`. TouchableOpacity can wrap any content (text, icons, images) and accepts a `style` prop, so it is used to build custom-designed buttons. When pressed, it lowers the opacity of its children as visual feedback.*

**7. How do you display a local image and a remote image with the Image component?**
> *A local image is loaded with `require`: `source={require('./assets/photo.png')}`; its size is read from the file. A remote image is loaded with a URI object: `source={{ uri: 'https://...' }}`, and in this case `width` and `height` **must** be given in the style, otherwise the image will not be visible.*

**8. Explain the `resizeMode` prop of Image. Give three values.**
> *`resizeMode` defines how an image is resized to fit its container. `'cover'` fills the container and crops the overflow, `'contain'` shows the whole image inside the container (empty space may remain), and `'stretch'` stretches the image to fill the container without keeping its aspect ratio. Other values are `'center'` and `'repeat'`.*

**9. What is the purpose of SafeAreaView?**
> *SafeAreaView renders its content within the safe area of the device. It adds padding so that content is not hidden behind the notch, status bar, rounded corners or the home indicator. It is usually placed at the root of a screen. Expo recommends the version from `react-native-safe-area-context` because it works on both iOS and Android.*

**10. How do you hide the text in a password field? Name two other useful TextInput props.**
> *The `secureTextEntry` prop hides the typed characters (shown as dots). Other useful props: `placeholder` shows a hint text when the input is empty, `maxLength` limits the number of characters, `autoCapitalize="none"` disables automatic capitalization, and `multiline` allows multiple lines of text.*

**11. What is a controlled component? Give an example with TextInput.**
> *A controlled component is a component whose value is stored in React state and updated through an event handler, so React is the single source of truth. Example: `const [name, setName] = useState('');` and `<TextInput value={name} onChangeText={setName} />`. Every keystroke updates the state, and the input always shows the state value.*

**12. What is the difference between `onPress` and `onLongPress`?**
> *`onPress` is called when the user taps and releases the component. `onLongPress` is called when the user presses and holds the component for a longer time (about half a second). `onLongPress` is often used for secondary actions such as showing a menu or extra information.*

**13. Explain the difference between `justifyContent` and `alignItems` in Flexbox.**
> *`justifyContent` aligns children along the **main axis**, and `alignItems` aligns them along the **cross axis**. In React Native the default `flexDirection` is `'column'`, so by default `justifyContent` controls vertical alignment and `alignItems` controls horizontal alignment. When `flexDirection` is `'row'`, they swap.*

**14. What is the difference between `var`, `let` and `const`?**
> *`var` is function-scoped, can be redeclared and reassigned, and is hoisted with the value `undefined`. `let` is block-scoped and can be reassigned but not redeclared. `const` is block-scoped and cannot be reassigned or redeclared; however, the contents of a `const` object or array can still be modified. `let` and `const` cannot be used before their declaration (temporal dead zone).*

**15. What is the difference between `label` and `value` in `Picker.Item`?**
> *`label` is the text displayed to the user in the dropdown, while `value` is the internal value stored in state when that option is selected. The state is set with `selectedValue` and updated in `onValueChange`.*

**16. When would you use a radio button, a checkbox, or a Picker?**
> *Use radio buttons when the user must choose exactly one option and all options should be visible. Use checkboxes when multiple options can be selected independently. Use a Picker when there are many options and screen space is limited.*

**17. What is Expo and what is Expo Go?**
> *Expo is a framework and set of tools built on top of React Native that simplifies development: no Android Studio or Xcode setup is needed to start. Expo Go is a mobile app that runs Expo projects on a real device by scanning a QR code, so changes can be tested instantly.*

**18. Why must text always be inside a `<Text>` component in React Native?**
> *Unlike the web, React Native does not allow raw text inside a `View`. All text must be wrapped in a `<Text>` component, because only `Text` knows how to render strings on native platforms. Putting text directly inside a `View` causes an error.*

---

## 🧮 Bölüm 4 – Hazır Kod Şablonları (Tam Kodlar)

Her biri **tek başına çalışan tam kod**: Snack'te `App.js`'in tamamının yerine yapıştır. Arayüz yazıları sınav İngilizce olduğu için İngilizce, yorumlar Türkçe.

| # | Kod | Girdi → Çıktı |
|---|---|---|
| A1 | Joule → cal | 1 input → 1 sonuç |
| A2 | Joule → kcal | 1 input → 1 sonuç |
| A3 | Celsius → Fahrenheit | 1 input → 1 sonuç |
| A4 | Celsius → Kelvin | 1 input → 1 sonuç |
| A5 | Kilometer → Mile | 1 input → 1 sonuç |
| A6 | Kilometer → Meter | 1 input → 1 sonuç |
| A7 | TL → USD | 1 input → 1 sonuç |
| A8 | TL → EUR | 1 input → 1 sonuç |
| A9 | Kilogram → Pound | 1 input → 1 sonuç |
| A10 | Kilogram → Gram | 1 input → 1 sonuç |
| B1 | Simple Calculator (+ / −) | 2 input → 1 sonuç |
| B2 | Currency Converter (USD / EUR / GBP seçimli) | 1 input + seçim → 1 sonuç |
| B3 | BMI Calculator | 2 input → değer + kategori |
| B4 | Discount Calculator | 2 input → 2 sonuç |

> **Tek sonuç ile iki sonuç farkı:** iki sonuçta `result` bir nesneydi (`{ cal, kcal }`) ve `result.cal` diye okunuyordu. Tek sonuçta `result` doğrudan **sayıdır**, ekranda `result.toFixed(...)` ile gösterilir.

> **A1–A10 arasındaki tek fark:** başlık, etiket, placeholder, `setResult(...)` içindeki **formül** ve sonuç satırındaki birim. Satırı 👈 ile işaretli. Celsius kodlarında (A3, A4) eksi sayıya izin var.

### A) Tek input → tek sonuç dönüştürücüler

#### A1. Joule → cal
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function JouleToCal() {
  const [value, setValue] = useState('');      // input'taki değer (yazı olarak)
  const [result, setResult] = useState(null);  // sonuç: sayı ya da null (henüz hesaplanmadı)

  // CONVERT
  const handleConvert = () => {
    const x = Number(value);
    if (!Number.isFinite(x)) {                 // sayı değilse sonuç gösterme
      setResult(null);
      return;
    }
    setResult(x / 4.184);                       // 👈 FORMÜL: 1 cal = 4.184 J  → cal = J / 4.184
  };

  // RESET
  const handleReset = () => {
    setValue('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Joule to → cal Converter
        </Text>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#0f172a', textAlign: 'center' }}>
          Joule
        </Text>

        <TextInput
          placeholder="Joule"
          keyboardType="numeric"
          value={value}
          onChangeText={(text) => {
            setValue(text.replace(/[^0-9.]/g, ''));   // rakam ve nokta dışındakileri sil (sadece sayı)
            setResult(null);                                // sayı değişince eski sonucu sil
          }}
          style={{
            borderWidth: 1,
            borderColor: '#d4d4d8',
            borderRadius: 10,
            paddingHorizontal: 12,
            paddingVertical: 12,
            backgroundColor: '#fff',
          }}
        />

        <Button title="Convert" onPress={handleConvert} disabled={value === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {/* Sonuç: sadece hesaplandıysa görünür */}
        {result !== null && (
          <Text style={{ fontSize: 18, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
            cal: {result.toFixed(4)}
          </Text>
        )}
      </View>
    </SafeAreaView>
  );
}
```

#### A2. Joule → kcal
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function JouleToKcal() {
  const [value, setValue] = useState('');      // input'taki değer (yazı olarak)
  const [result, setResult] = useState(null);  // sonuç: sayı ya da null (henüz hesaplanmadı)

  // CONVERT
  const handleConvert = () => {
    const x = Number(value);
    if (!Number.isFinite(x)) {                 // sayı değilse sonuç gösterme
      setResult(null);
      return;
    }
    setResult(x / 4184);                       // 👈 FORMÜL: 1 kcal = 4184 J  → kcal = J / 4184
  };

  // RESET
  const handleReset = () => {
    setValue('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Joule to → kcal Converter
        </Text>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#0f172a', textAlign: 'center' }}>
          Joule
        </Text>

        <TextInput
          placeholder="Joule"
          keyboardType="numeric"
          value={value}
          onChangeText={(text) => {
            setValue(text.replace(/[^0-9.]/g, ''));   // rakam ve nokta dışındakileri sil (sadece sayı)
            setResult(null);                                // sayı değişince eski sonucu sil
          }}
          style={{
            borderWidth: 1,
            borderColor: '#d4d4d8',
            borderRadius: 10,
            paddingHorizontal: 12,
            paddingVertical: 12,
            backgroundColor: '#fff',
          }}
        />

        <Button title="Convert" onPress={handleConvert} disabled={value === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {/* Sonuç: sadece hesaplandıysa görünür */}
        {result !== null && (
          <Text style={{ fontSize: 18, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
            kcal: {result.toFixed(5)}
          </Text>
        )}
      </View>
    </SafeAreaView>
  );
}
```

#### A3. Celsius → Fahrenheit
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function CelsiusToFahrenheit() {
  const [value, setValue] = useState('');      // input'taki değer (yazı olarak)
  const [result, setResult] = useState(null);  // sonuç: sayı ya da null (henüz hesaplanmadı)

  // CONVERT
  const handleConvert = () => {
    const x = Number(value);
    if (!Number.isFinite(x)) {                 // sayı değilse sonuç gösterme
      setResult(null);
      return;
    }
    setResult(x * 9 / 5 + 32);                       // 👈 FORMÜL: °F = °C × 9/5 + 32
  };

  // RESET
  const handleReset = () => {
    setValue('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Celsius to → Fahrenheit Converter
        </Text>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#0f172a', textAlign: 'center' }}>
          Celsius
        </Text>

        <TextInput
          placeholder="Celsius"
          keyboardType="numeric"
          value={value}
          onChangeText={(text) => {
            setValue(text.replace(/[^0-9.-]/g, ''));   // rakam, nokta ve eksi dışındakileri sil (sıcaklık eksi olabilir)
            setResult(null);                                // sayı değişince eski sonucu sil
          }}
          style={{
            borderWidth: 1,
            borderColor: '#d4d4d8',
            borderRadius: 10,
            paddingHorizontal: 12,
            paddingVertical: 12,
            backgroundColor: '#fff',
          }}
        />

        <Button title="Convert" onPress={handleConvert} disabled={value === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {/* Sonuç: sadece hesaplandıysa görünür */}
        {result !== null && (
          <Text style={{ fontSize: 18, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
            °F: {result.toFixed(2)}
          </Text>
        )}
      </View>
    </SafeAreaView>
  );
}
```

#### A4. Celsius → Kelvin
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function CelsiusToKelvin() {
  const [value, setValue] = useState('');      // input'taki değer (yazı olarak)
  const [result, setResult] = useState(null);  // sonuç: sayı ya da null (henüz hesaplanmadı)

  // CONVERT
  const handleConvert = () => {
    const x = Number(value);
    if (!Number.isFinite(x)) {                 // sayı değilse sonuç gösterme
      setResult(null);
      return;
    }
    setResult(x + 273.15);                       // 👈 FORMÜL: K = °C + 273.15
  };

  // RESET
  const handleReset = () => {
    setValue('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Celsius to → Kelvin Converter
        </Text>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#0f172a', textAlign: 'center' }}>
          Celsius
        </Text>

        <TextInput
          placeholder="Celsius"
          keyboardType="numeric"
          value={value}
          onChangeText={(text) => {
            setValue(text.replace(/[^0-9.-]/g, ''));   // rakam, nokta ve eksi dışındakileri sil (sıcaklık eksi olabilir)
            setResult(null);                                // sayı değişince eski sonucu sil
          }}
          style={{
            borderWidth: 1,
            borderColor: '#d4d4d8',
            borderRadius: 10,
            paddingHorizontal: 12,
            paddingVertical: 12,
            backgroundColor: '#fff',
          }}
        />

        <Button title="Convert" onPress={handleConvert} disabled={value === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {/* Sonuç: sadece hesaplandıysa görünür */}
        {result !== null && (
          <Text style={{ fontSize: 18, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
            K: {result.toFixed(2)}
          </Text>
        )}
      </View>
    </SafeAreaView>
  );
}
```

#### A5. Kilometer → Mile
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function KmToMile() {
  const [value, setValue] = useState('');      // input'taki değer (yazı olarak)
  const [result, setResult] = useState(null);  // sonuç: sayı ya da null (henüz hesaplanmadı)

  // CONVERT
  const handleConvert = () => {
    const x = Number(value);
    if (!Number.isFinite(x)) {                 // sayı değilse sonuç gösterme
      setResult(null);
      return;
    }
    setResult(x / 1.609);                       // 👈 FORMÜL: 1 mi = 1.609 km  → mi = km / 1.609
  };

  // RESET
  const handleReset = () => {
    setValue('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Kilometer to → Mile Converter
        </Text>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#0f172a', textAlign: 'center' }}>
          Kilometer
        </Text>

        <TextInput
          placeholder="Kilometer"
          keyboardType="numeric"
          value={value}
          onChangeText={(text) => {
            setValue(text.replace(/[^0-9.]/g, ''));   // rakam ve nokta dışındakileri sil (sadece sayı)
            setResult(null);                                // sayı değişince eski sonucu sil
          }}
          style={{
            borderWidth: 1,
            borderColor: '#d4d4d8',
            borderRadius: 10,
            paddingHorizontal: 12,
            paddingVertical: 12,
            backgroundColor: '#fff',
          }}
        />

        <Button title="Convert" onPress={handleConvert} disabled={value === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {/* Sonuç: sadece hesaplandıysa görünür */}
        {result !== null && (
          <Text style={{ fontSize: 18, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
            mi: {result.toFixed(3)}
          </Text>
        )}
      </View>
    </SafeAreaView>
  );
}
```

#### A6. Kilometer → Meter
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function KmToMeter() {
  const [value, setValue] = useState('');      // input'taki değer (yazı olarak)
  const [result, setResult] = useState(null);  // sonuç: sayı ya da null (henüz hesaplanmadı)

  // CONVERT
  const handleConvert = () => {
    const x = Number(value);
    if (!Number.isFinite(x)) {                 // sayı değilse sonuç gösterme
      setResult(null);
      return;
    }
    setResult(x * 1000);                       // 👈 FORMÜL: 1 km = 1000 m  → m = km × 1000
  };

  // RESET
  const handleReset = () => {
    setValue('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Kilometer to → Meter Converter
        </Text>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#0f172a', textAlign: 'center' }}>
          Kilometer
        </Text>

        <TextInput
          placeholder="Kilometer"
          keyboardType="numeric"
          value={value}
          onChangeText={(text) => {
            setValue(text.replace(/[^0-9.]/g, ''));   // rakam ve nokta dışındakileri sil (sadece sayı)
            setResult(null);                                // sayı değişince eski sonucu sil
          }}
          style={{
            borderWidth: 1,
            borderColor: '#d4d4d8',
            borderRadius: 10,
            paddingHorizontal: 12,
            paddingVertical: 12,
            backgroundColor: '#fff',
          }}
        />

        <Button title="Convert" onPress={handleConvert} disabled={value === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {/* Sonuç: sadece hesaplandıysa görünür */}
        {result !== null && (
          <Text style={{ fontSize: 18, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
            m: {result.toFixed(2)}
          </Text>
        )}
      </View>
    </SafeAreaView>
  );
}
```

#### A7. TL → USD
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

const USD_RATE = 34.5;   // 👈 ÖRNEK kur (1 USD = 34.5 TL). Soruda verilen kurla değiştir

export default function TlToUsd() {
  const [value, setValue] = useState('');      // input'taki değer (yazı olarak)
  const [result, setResult] = useState(null);  // sonuç: sayı ya da null (henüz hesaplanmadı)

  // CONVERT
  const handleConvert = () => {
    const x = Number(value);
    if (!Number.isFinite(x)) {                 // sayı değilse sonuç gösterme
      setResult(null);
      return;
    }
    setResult(x / USD_RATE);                       // 👈 FORMÜL: 1 USD = USD_RATE TL  → USD = TL / kur
  };

  // RESET
  const handleReset = () => {
    setValue('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          TL to → USD Converter
        </Text>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#0f172a', textAlign: 'center' }}>
          TL
        </Text>

        <TextInput
          placeholder="TL"
          keyboardType="numeric"
          value={value}
          onChangeText={(text) => {
            setValue(text.replace(/[^0-9.]/g, ''));   // rakam ve nokta dışındakileri sil (sadece sayı)
            setResult(null);                                // sayı değişince eski sonucu sil
          }}
          style={{
            borderWidth: 1,
            borderColor: '#d4d4d8',
            borderRadius: 10,
            paddingHorizontal: 12,
            paddingVertical: 12,
            backgroundColor: '#fff',
          }}
        />

        <Button title="Convert" onPress={handleConvert} disabled={value === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {/* Sonuç: sadece hesaplandıysa görünür */}
        {result !== null && (
          <Text style={{ fontSize: 18, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
            USD: {result.toFixed(2)}
          </Text>
        )}
      </View>
    </SafeAreaView>
  );
}
```

#### A8. TL → EUR
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

const EUR_RATE = 37.2;   // 👈 ÖRNEK kur (1 EUR = 37.2 TL). Soruda verilen kurla değiştir

export default function TlToEur() {
  const [value, setValue] = useState('');      // input'taki değer (yazı olarak)
  const [result, setResult] = useState(null);  // sonuç: sayı ya da null (henüz hesaplanmadı)

  // CONVERT
  const handleConvert = () => {
    const x = Number(value);
    if (!Number.isFinite(x)) {                 // sayı değilse sonuç gösterme
      setResult(null);
      return;
    }
    setResult(x / EUR_RATE);                       // 👈 FORMÜL: 1 EUR = EUR_RATE TL  → EUR = TL / kur
  };

  // RESET
  const handleReset = () => {
    setValue('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          TL to → EUR Converter
        </Text>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#0f172a', textAlign: 'center' }}>
          TL
        </Text>

        <TextInput
          placeholder="TL"
          keyboardType="numeric"
          value={value}
          onChangeText={(text) => {
            setValue(text.replace(/[^0-9.]/g, ''));   // rakam ve nokta dışındakileri sil (sadece sayı)
            setResult(null);                                // sayı değişince eski sonucu sil
          }}
          style={{
            borderWidth: 1,
            borderColor: '#d4d4d8',
            borderRadius: 10,
            paddingHorizontal: 12,
            paddingVertical: 12,
            backgroundColor: '#fff',
          }}
        />

        <Button title="Convert" onPress={handleConvert} disabled={value === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {/* Sonuç: sadece hesaplandıysa görünür */}
        {result !== null && (
          <Text style={{ fontSize: 18, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
            EUR: {result.toFixed(2)}
          </Text>
        )}
      </View>
    </SafeAreaView>
  );
}
```

#### A9. Kilogram → Pound
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function KgToPound() {
  const [value, setValue] = useState('');      // input'taki değer (yazı olarak)
  const [result, setResult] = useState(null);  // sonuç: sayı ya da null (henüz hesaplanmadı)

  // CONVERT
  const handleConvert = () => {
    const x = Number(value);
    if (!Number.isFinite(x)) {                 // sayı değilse sonuç gösterme
      setResult(null);
      return;
    }
    setResult(x * 2.20462);                       // 👈 FORMÜL: 1 kg = 2.20462 lb  → lb = kg × 2.20462
  };

  // RESET
  const handleReset = () => {
    setValue('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Kilogram to → Pound Converter
        </Text>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#0f172a', textAlign: 'center' }}>
          Kilogram
        </Text>

        <TextInput
          placeholder="Kilogram"
          keyboardType="numeric"
          value={value}
          onChangeText={(text) => {
            setValue(text.replace(/[^0-9.]/g, ''));   // rakam ve nokta dışındakileri sil (sadece sayı)
            setResult(null);                                // sayı değişince eski sonucu sil
          }}
          style={{
            borderWidth: 1,
            borderColor: '#d4d4d8',
            borderRadius: 10,
            paddingHorizontal: 12,
            paddingVertical: 12,
            backgroundColor: '#fff',
          }}
        />

        <Button title="Convert" onPress={handleConvert} disabled={value === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {/* Sonuç: sadece hesaplandıysa görünür */}
        {result !== null && (
          <Text style={{ fontSize: 18, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
            lb: {result.toFixed(3)}
          </Text>
        )}
      </View>
    </SafeAreaView>
  );
}
```

#### A10. Kilogram → Gram
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function KgToGram() {
  const [value, setValue] = useState('');      // input'taki değer (yazı olarak)
  const [result, setResult] = useState(null);  // sonuç: sayı ya da null (henüz hesaplanmadı)

  // CONVERT
  const handleConvert = () => {
    const x = Number(value);
    if (!Number.isFinite(x)) {                 // sayı değilse sonuç gösterme
      setResult(null);
      return;
    }
    setResult(x * 1000);                       // 👈 FORMÜL: 1 kg = 1000 g  → g = kg × 1000
  };

  // RESET
  const handleReset = () => {
    setValue('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Kilogram to → Gram Converter
        </Text>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#0f172a', textAlign: 'center' }}>
          Kilogram
        </Text>

        <TextInput
          placeholder="Kilogram"
          keyboardType="numeric"
          value={value}
          onChangeText={(text) => {
            setValue(text.replace(/[^0-9.]/g, ''));   // rakam ve nokta dışındakileri sil (sadece sayı)
            setResult(null);                                // sayı değişince eski sonucu sil
          }}
          style={{
            borderWidth: 1,
            borderColor: '#d4d4d8',
            borderRadius: 10,
            paddingHorizontal: 12,
            paddingVertical: 12,
            backgroundColor: '#fff',
          }}
        />

        <Button title="Convert" onPress={handleConvert} disabled={value === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {/* Sonuç: sadece hesaplandıysa görünür */}
        {result !== null && (
          <Text style={{ fontSize: 18, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
            g: {result.toFixed(2)}
          </Text>
        )}
      </View>
    </SafeAreaView>
  );
}
```

### B) Diğer hesaplayıcılar

#### B1. Simple Calculator (Toplama / Çıkarma)
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function SimpleCalculator() {
  const [first, setFirst] = useState('');       // 1. sayı
  const [second, setSecond] = useState('');     // 2. sayı
  const [result, setResult] = useState(null);   // sonuç metni ya da null

  // İşlemi yap: op → '+' veya '-'
  const calculate = (op) => {
    const a = Number(first);
    const b = Number(second);
    if (!Number.isFinite(a) || !Number.isFinite(b)) {   // biri bile sayı değilse
      setResult(null);
      return;
    }
    const total = op === '+' ? a + b : a - b;           // 👈 işarete göre topla ya da çıkar
    setResult(`${a} ${op} ${b} = ${total}`);            // ekranda "5 + 3 = 8" gibi görünsün
  };

  // RESET: iki input'u ve sonucu temizle
  const handleReset = () => {
    setFirst('');
    setSecond('');
    setResult(null);
  };

  const isEmpty = first === '' || second === '';        // biri boşsa işlem butonları pasif

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Simple Calculator
        </Text>

        <TextInput
          placeholder="First number"
          keyboardType="numeric"
          value={first}
          onChangeText={(text) => {
            setFirst(text.replace(/[^0-9.-]/g, ''));    // sayı, nokta ve eksi (negatif sayılar için)
            setResult(null);
          }}
          style={{ borderWidth: 1, borderColor: '#d4d4d8', borderRadius: 10, padding: 12, backgroundColor: '#fff' }}
        />
        <TextInput
          placeholder="Second number"
          keyboardType="numeric"
          value={second}
          onChangeText={(text) => {
            setSecond(text.replace(/[^0-9.-]/g, ''));
            setResult(null);
          }}
          style={{ borderWidth: 1, borderColor: '#d4d4d8', borderRadius: 10, padding: 12, backgroundColor: '#fff' }}
        />

        {/* + ve − butonları yan yana, eşit genişlikte */}
        <View style={{ flexDirection: 'row', gap: 12 }}>
          <View style={{ flex: 1 }}>
            <Button title="+" onPress={() => calculate('+')} disabled={isEmpty} />
          </View>
          <View style={{ flex: 1 }}>
            <Button title="−" onPress={() => calculate('-')} disabled={isEmpty} />
          </View>
        </View>
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {result !== null && (
          <Text style={{ fontSize: 22, fontWeight: '700', color: '#2563eb', textAlign: 'center' }}>
            {result}
          </Text>
        )}
      </View>
    </SafeAreaView>
  );
}
```
> `calculate('+')` ve `calculate('-')` aynı fonksiyonu kullanıyor, hangi işlemin yapılacağını parametre belirliyor. Çarpma/bölme eklemek için: `op === '*' ? a * b : ...` dalları ve iki buton daha (Button dosyası S3). Eksi sayıya izin verildi.

#### B2. Currency Converter (Para Birimi Dönüştürücü)
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, TouchableOpacity, View } from 'react-native';

// 1 birim yabancı para = kaç TL (ÖRNEK kurlar, soruda verilen kurlarla değiştir)
const RATES = {
  USD: 34.5,
  EUR: 37.2,
  GBP: 44.0,
};
const CURRENCIES = Object.keys(RATES);          // ['USD', 'EUR', 'GBP'] → butonlar bundan üretilir

export default function CurrencyConverter() {
  const [amount, setAmount] = useState('');     // TL miktarı
  const [currency, setCurrency] = useState('USD');   // seçili hedef para birimi
  const [result, setResult] = useState(null);

  const handleConvert = () => {
    const tl = Number(amount);
    if (!Number.isFinite(tl)) {
      setResult(null);
      return;
    }
    setResult(tl / RATES[currency]);            // 👈 FORMÜL: TL / kur (ör. 345 TL / 34.5 = 10 USD)
  };

  const handleReset = () => {
    setAmount('');
    setResult(null);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Currency Converter
        </Text>

        <TextInput
          placeholder="Amount (TL)"
          keyboardType="numeric"
          value={amount}
          onChangeText={(text) => {
            setAmount(text.replace(/[^0-9.]/g, ''));
            setResult(null);
          }}
          style={{ borderWidth: 1, borderColor: '#d4d4d8', borderRadius: 10, padding: 12, backgroundColor: '#fff' }}
        />

        {/* PARA BİRİMİ SEÇİMİ: radio mantığı (tek seçim), her birim için bir buton */}
        <View style={{ flexDirection: 'row', gap: 8, justifyContent: 'center' }}>
          {CURRENCIES.map((c) => {
            const isActive = c === currency;
            return (
              <TouchableOpacity
                key={c}
                onPress={() => {
                  setCurrency(c);
                  setResult(null);                  // birim değişince eski sonucu sil
                }}
                style={{
                  paddingVertical: 10,
                  paddingHorizontal: 18,
                  borderRadius: 999,
                  backgroundColor: isActive ? '#2563eb' : '#e2e8f0',
                }}
              >
                <Text style={{ color: isActive ? '#fff' : '#0f172a', fontWeight: '600' }}>{c}</Text>
              </TouchableOpacity>
            );
          })}
        </View>

        <Button title="Convert" onPress={handleConvert} disabled={amount === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {result !== null && (
          <Text style={{ fontSize: 22, fontWeight: '700', color: '#2563eb', textAlign: 'center' }}>
            {amount} TL = {result.toFixed(2)} {currency}
          </Text>
        )}
        <Text style={{ color: '#94a3b8', textAlign: 'center' }}>
          1 {currency} = {RATES[currency]} TL
        </Text>
      </View>
    </SafeAreaView>
  );
}
```
> Kurlar **örnek** değerler, sınavda soruda verilen kuru yaz. Yeni birim eklemek için `RATES`'e bir satır eklemek yeterli (`JPY: 0.23`), buton otomatik oluşur.
> **Ters yön (yabancı para → TL):** formül `tl * RATES[currency]` olur ve yazılar değişir.

#### B3. BMI Calculator (Vücut Kitle İndeksi)
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

// BMI kategorisi ve rengi (component DIŞINDA yardımcı fonksiyon)
const getCategory = (bmi) => {
  if (bmi < 18.5) return { label: 'Underweight', color: '#3b82f6' };     // zayıf
  if (bmi < 25) return { label: 'Normal', color: '#22c55e' };            // normal
  if (bmi < 30) return { label: 'Overweight', color: '#f97316' };        // fazla kilolu
  return { label: 'Obese', color: '#ef4444' };                           // obez
};

export default function BmiCalculator() {
  const [weight, setWeight] = useState('');     // kilo (kg)
  const [height, setHeight] = useState('');     // boy (cm)
  const [bmi, setBmi] = useState(null);         // sonuç
  const [errorMsg, setErrorMsg] = useState('');

  const handleCalculate = () => {
    const w = Number(weight);
    const h = Number(height) / 100;             // 👈 cm → metre (175 cm → 1.75 m)
    if (!(w > 0) || !(h > 0)) {                 // 0, eksi ya da sayı değilse
      setErrorMsg('Please enter valid weight and height.');
      setBmi(null);
      return;
    }
    setErrorMsg('');
    setBmi(w / (h * h));                        // 👈 FORMÜL: BMI = kg / (m × m)
  };

  const handleReset = () => {
    setWeight('');
    setHeight('');
    setBmi(null);
    setErrorMsg('');
  };

  const category = bmi !== null ? getCategory(bmi) : null;   // hesaplandıysa kategori

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          BMI Calculator
        </Text>

        <TextInput
          placeholder="Weight (kg)"
          keyboardType="numeric"
          value={weight}
          onChangeText={(text) => {
            setWeight(text.replace(/[^0-9.]/g, ''));
            setBmi(null);
          }}
          style={{ borderWidth: 1, borderColor: '#d4d4d8', borderRadius: 10, padding: 12, backgroundColor: '#fff' }}
        />
        <TextInput
          placeholder="Height (cm)"
          keyboardType="numeric"
          value={height}
          onChangeText={(text) => {
            setHeight(text.replace(/[^0-9.]/g, ''));
            setBmi(null);
          }}
          style={{ borderWidth: 1, borderColor: '#d4d4d8', borderRadius: 10, padding: 12, backgroundColor: '#fff' }}
        />

        <Button title="Calculate" onPress={handleCalculate} disabled={weight === '' || height === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {errorMsg !== '' && (
          <Text style={{ color: '#ef4444', textAlign: 'center' }}>{errorMsg}</Text>
        )}

        {/* Sonuç: değer + renkli kategori */}
        {bmi !== null && (
          <View style={{ alignItems: 'center', gap: 4 }}>
            <Text style={{ fontSize: 28, fontWeight: '700', color: category.color }}>
              {bmi.toFixed(1)}
            </Text>
            <Text style={{ fontSize: 18, fontWeight: '600', color: category.color }}>
              {category.label}
            </Text>
          </View>
        )}
      </View>
    </SafeAreaView>
  );
}
```
> **Formül:** BMI = kilo (kg) / boy² (m). Boy cm girildiği için önce 100'e bölünüyor. Kontrol: 70 kg, 175 cm → 22.9 (Normal).
> Kategori sınırları: < 18.5 Underweight, 18.5–24.9 Normal, 25–29.9 Overweight, ≥ 30 Obese. Kategori ve rengi tek bir yardımcı fonksiyondan geliyor, eşikleri orada değiştirebilirsin.

#### B4. Discount Calculator (İndirim Hesaplama)
```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function DiscountCalculator() {
  const [price, setPrice] = useState('');       // ürün fiyatı
  const [percent, setPercent] = useState('');   // indirim yüzdesi
  const [result, setResult] = useState(null);   // { discount, finalPrice } ya da null
  const [errorMsg, setErrorMsg] = useState('');

  const handleCalculate = () => {
    const p = Number(price);
    const d = Number(percent);
    if (!Number.isFinite(p) || !Number.isFinite(d)) {
      setResult(null);
      return;
    }
    if (d > 100) {                              // %100'den fazla indirim olamaz
      setErrorMsg('Discount cannot be more than 100%.');
      setResult(null);
      return;
    }
    setErrorMsg('');
    const discount = (p * d) / 100;             // 👈 indirim tutarı = fiyat × yüzde / 100
    setResult({
      discount,
      finalPrice: p - discount,                 // 👈 son fiyat = fiyat − indirim
    });
  };

  const handleReset = () => {
    setPrice('');
    setPercent('');
    setResult(null);
    setErrorMsg('');
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ flex: 1, justifyContent: 'center', padding: 24, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a', textAlign: 'center' }}>
          Discount Calculator
        </Text>

        <TextInput
          placeholder="Price (TL)"
          keyboardType="numeric"
          value={price}
          onChangeText={(text) => {
            setPrice(text.replace(/[^0-9.]/g, ''));
            setResult(null);
          }}
          style={{ borderWidth: 1, borderColor: '#d4d4d8', borderRadius: 10, padding: 12, backgroundColor: '#fff' }}
        />
        <TextInput
          placeholder="Discount (%)"
          keyboardType="numeric"
          maxLength={3}                             // en fazla 3 hane (100)
          value={percent}
          onChangeText={(text) => {
            setPercent(text.replace(/[^0-9.]/g, ''));
            setResult(null);
          }}
          style={{ borderWidth: 1, borderColor: '#d4d4d8', borderRadius: 10, padding: 12, backgroundColor: '#fff' }}
        />

        <Button title="Calculate" onPress={handleCalculate} disabled={price === '' || percent === ''} />
        <Button title="Reset" color="#ef4444" onPress={handleReset} />

        {errorMsg !== '' && (
          <Text style={{ color: '#ef4444', textAlign: 'center' }}>{errorMsg}</Text>
        )}

        {result !== null && (
          <View style={{ gap: 4, alignItems: 'center' }}>
            <Text style={{ fontSize: 16, color: '#475569' }}>
              Discount: {result.discount.toFixed(2)} TL
            </Text>
            <Text style={{ fontSize: 22, fontWeight: '700', color: '#16a34a' }}>
              Final price: {result.finalPrice.toFixed(2)} TL
            </Text>
          </View>
        )}
      </View>
    </SafeAreaView>
  );
}
```
> **Formül:** indirim = fiyat × yüzde / 100 · son fiyat = fiyat − indirim. Kontrol: 200 TL, %15 → indirim 30.00 TL, son fiyat 170.00 TL.
> %100'den büyük yüzde girilirse hata mesajı çıkar (Button S13'teki `return` kalıbı).
