# 05 – Button

`Button`, hazır görünümlü basit bir butondur. Hızlıca "bas → bir şey olsun" yapmak için kullanılır.

> ⚠️ **En önemli bilgi: `Button`'ın `style` prop'u YOKTUR.**
> Sadece **yazısını** (`title`), **rengini** (`color`) ve **pasif olup olmadığını** (`disabled`) değiştirebilirsin.
> Boyut, köşe yuvarlaklığı, kenarlık, yazı boyutu gibi şeyler istenirse → `Button` yerine **`TouchableOpacity`** kullanılır (bkz. Kod 1 – S11).

---

## 🔘 Button hızlı tablo

| Prop | Ne yapar | Örnek |
|---|---|---|
| `title` | Butonun yazısı (**zorunlu**, string olmalı) | `title="Hesapla"` |
| `onPress` | Basınca çalışacak fonksiyon | `onPress={handleSum}` |
| `color` | Renk: Android'de ve Snack web'de **arka plan**, iOS'ta **yazı** rengi | `color="#ef4444"` |
| `disabled` | `true` ise basılamaz, gri görünür | `disabled={!isValid}` |

| Button | TouchableOpacity |
|---|---|
| Hazır görünüm, hızlı | Görünümü tamamen sen belirlersin |
| `style` YOK | `style` VAR (her şey değişir) |
| Yazı `title` ile verilir | İçine `<Text>` koyarsın (ikon, görsel de olur) |
| Basit formlar için | Özel tasarım butonlar için |

> **`onPress` yazımı:** fonksiyonun kendisi verilir.
> `onPress={handleSum}` ✅ · `onPress={() => handleSum()}` ✅ · `onPress={handleSum()}` ❌ (sayfa açılır açılmaz çalışır)

---

## 📦 Temel Kod 1 – Toplama Hesap Makinesi

```jsx
import { useState } from 'react';
import { Button, SafeAreaView, Text, TextInput, View } from 'react-native';

export default function SumCalculator() {
  // İki input'un değerleri (TextInput hep YAZI verir: '5' gibi)
  const [first, setFirst] = useState('');
  const [second, setSecond] = useState('');
  // Sonuç: başta null = "henüz hesaplanmadı"
  const [result, setResult] = useState(null);

  // Butona basınca çalışan fonksiyon
  const handleSum = () => {
    const total = Number(first) + Number(second);       // yazıları sayıya çevirip topla
    setResult(Number.isFinite(total) ? total : null);   // geçerli bir sayı çıktıysa kaydet, çıkmadıysa (NaN) null
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View
        style={{
          flex: 1,
          justifyContent: 'center',
          padding: 24,
          gap: 16,
        }}
      >
        {/* Başlık */}
        <Text style={{ fontSize: 20, fontWeight: '600', color: '#0f172a' }}>
          Add two numbers
        </Text>

        {/* KART: beyaz, hafif gölgeli */}
        <View
          style={{
            borderRadius: 16,
            backgroundColor: '#fff',
            padding: 20,
            gap: 16,
            shadowColor: '#000',
            shadowOpacity: 0.05,
            shadowRadius: 12,
            elevation: 2,
          }}
        >
          {/* 1. SAYI */}
          <TextInput
            placeholder="First number"
            keyboardType="numeric"
            value={first}
            onChangeText={setFirst}
            style={{
              borderWidth: 1,
              borderColor: '#d4d4d8',
              borderRadius: 10,
              paddingHorizontal: 12,
              paddingVertical: 12,
            }}
          />

          {/* 2. SAYI */}
          <TextInput
            placeholder="Second number"
            keyboardType="numeric"
            value={second}
            onChangeText={setSecond}
            style={{
              borderWidth: 1,
              borderColor: '#d4d4d8',
              borderRadius: 10,
              paddingHorizontal: 12,
              paddingVertical: 12,
            }}
          />

          {/* BUTON: basınca handleSum çalışır */}
          <Button title="Calculate sum" onPress={handleSum} />

          {/* SONUÇ: null ise uyarı yazısı, değilse sonucu göster */}
          <View>
            <Text style={{ color: '#475569' }}>Result:</Text>
            <Text style={{ fontSize: 24, fontWeight: '700', color: '#2563eb' }}>
              {result === null ? 'Enter two valid numbers' : result}
            </Text>
          </View>
        </View>
      </View>
    </SafeAreaView>
  );
}
```

> **Neden `Number()`?** TextInput'tan gelen değerler yazıdır. `'2' + '3'` = `'23'` olur (yazılar yan yana eklenir). `Number('2') + Number('3')` = `5`.
> **`Number.isFinite`:** sonuç gerçek bir sayı mı diye bakar. `Number('abc')` → `NaN` (sayı değil) olduğu için false döner ve uyarı yazısı görünür.
> **Hocanın kodundaki küçük açık:** `Number('')` = `0`. İki alan boşken butona basılırsa uyarı yerine `0` yazar. Bunu engellemek için bkz. S7.

### Olası sorular – Kod 1

**S1. Başlığı, buton yazısını ve buton rengini değiştir**
```jsx
İki sayıyı topla                                                       {/* 👈 başlık */}
<Button title="Topla" onPress={handleSum} color="#22c55e" />          // 👈 title değişti, color EKLENDİ
```

**S2. Toplama yerine çıkarma / çarpma / bölme yap**
```jsx
// handleSum fonksiyonunun İÇİNDE sadece işaret değişir:
const total = Number(first) - Number(second);   // 👈 çıkarma
const total = Number(first) * Number(second);   // 👈 çarpma
const total = Number(first) / Number(second);   // 👈 bölme (sıfıra bölmede Infinity çıkar → isFinite false → uyarı yazısı)
```

**S3 (zor). Dört işlem için dört buton olsun**
```jsx
// handleSum fonksiyonunun YERİNE (component İÇİNDE):
const calculate = (op) => {                       // 👈 op: hangi işlem ('+', '-', '*', '/')
  const a = Number(first);
  const b = Number(second);
  let total;
  if (op === '+') total = a + b;
  else if (op === '-') total = a - b;
  else if (op === '*') total = a * b;
  else total = a / b;
  setResult(Number.isFinite(total) ? total : null);
};

{/* Tek Button'ın YERİNE: 4 buton yan yana */}
<View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
  <Button title="+" onPress={() => calculate('+')} />   {/* 👈 parametre verildiği için () => ile sarılır */}
  <Button title="−" onPress={() => calculate('-')} />
  <Button title="×" onPress={() => calculate('*')} />
  <Button title="÷" onPress={() => calculate('/')} />
</View>
```
> **Neden `() => calculate('+')`?** Fonksiyona parametre göndermek gerektiğinde ok fonksiyonuyla sarılır. `onPress={calculate('+')}` yazsaydın sayfa açılır açılmaz çalışırdı.

**S4. Her şeyi sıfırlayan "Temizle" butonu**
```jsx
{/* Hesapla butonunun ALTINA */}
<Button
  title="Temizle"
  color="#ef4444"
  onPress={() => {
    setFirst('');       // 👈 iki input'u boşalt
    setSecond('');
    setResult(null);    // 👈 sonucu başlangıç haline döndür
  }}
/>
```

**S5. Üçüncü sayı alanı ekle (üç sayıyı topla)**
```jsx
const [third, setThird] = useState('');                                    // 👈 YENİ state (component İÇİNDE)

const total = Number(first) + Number(second) + Number(third);              // 👈 handleSum İÇİNDE

{/* 2. TextInput'un ALTINA – öncekinin kopyası, sadece 3 yer değişir */}
<TextInput
  placeholder="Third number"          // 👈
  keyboardType="numeric"
  value={third}                       // 👈
  onChangeText={setThird}             // 👈
  style={{ borderWidth: 1, borderColor: '#d4d4d8', borderRadius: 10, paddingHorizontal: 12, paddingVertical: 12 }}
/>
```

**S6. Virgüllü ondalık sayılar da çalışsın (2,5 + 1,5)**
```jsx
// handleSum İÇİNDE:
const total = Number(first.replace(',', '.')) + Number(second.replace(',', '.'));   // 👈 virgülü noktaya çevir
```
> JavaScript ondalık için **nokta** bekler: `Number('2.5')` = 2.5 ama `Number('2,5')` = NaN.

**S7. Alanlardan biri boşken buton pasif olsun**
```jsx
<Button
  title="Calculate sum"
  onPress={handleSum}
  disabled={first === '' || second === ''}   // 👈 biri bile boşsa (||) basılamaz
/>
```

**S8. Sonuç negatifse kırmızı, değilse mavi yazsın**
```jsx
<Text
  style={{
    fontSize: 24,
    fontWeight: '700',
    color: result !== null && result < 0 ? '#ef4444' : '#2563eb',   // 👈 koşullu renk
  }}
>
```

**S9. Sonucu en fazla 2 ondalık basamakla göster (10 / 3 = 3.33)**
```jsx
{result === null ? 'Enter two valid numbers' : Number(result.toFixed(2))}   // 👈 toFixed(2) → 2 basamak
```
> `toFixed` yazı döndürür ve 5'i "5.00" yapar. Başına `Number()` eklenince gereksiz sıfırlar silinir: 5 → 5, 3.333 → 3.33.

**S10. Sonucu "5 + 3 = 8" şeklinde göster**
```jsx
{result === null ? 'Enter two valid numbers' : `${first} + ${second} = ${result}`}   // 👈 template string
```

**S11. Butonu büyük, yuvarlak köşeli ve özel renkli yap (Button'da style olmadığı için)**
```jsx
import { SafeAreaView, Text, TextInput, TouchableOpacity, View } from 'react-native';   // 👈 Button yerine TouchableOpacity

{/* <Button ... /> satırının YERİNE */}
<TouchableOpacity
  onPress={handleSum}
  style={{
    backgroundColor: '#7c3aed',    // 👈 arka plan
    paddingVertical: 16,           // 👈 yükseklik
    borderRadius: 999,             // 👈 hap şekli (12 → hafif yuvarlak)
    alignItems: 'center',          // 👈 yazıyı ortala
  }}
>
  <Text style={{ color: '#fff', fontSize: 18, fontWeight: '700' }}>   {/* 👈 yazı stili de serbest */}
    Hesapla
  </Text>
</TouchableOpacity>
```

---

## 📦 Temel Kod 2 – Geri Sayım (Başlat / Duraklat / Sıfırla)

```jsx
import { useEffect, useState } from 'react';
import { Button, SafeAreaView, Text, View } from 'react-native';

export default function CountdownController() {
  const [timeLeft, setTimeLeft] = useState(10);      // kalan saniye (başlangıç 10)
  const [isRunning, setRunning] = useState(false);   // sayıyor mu? (başta durmuş)

  // isRunning her değiştiğinde çalışır
  useEffect(() => {
    if (!isRunning) return;                          // durmuşsa timer kurma

    // Her 1 saniyede bir:
    const timer = setInterval(() => {
      setTimeLeft((prev) => {
        if (prev <= 1) {                             // son saniyeye gelindiyse...
          setRunning(false);                         // ...sayacı durdur
          return 0;                                  // ...ve 0'da bırak
        }
        return prev - 1;                             // değilse 1 azalt
      });
    }, 1000);

    return () => clearInterval(timer);               // cleanup: duraklatınca/kapanınca timer'ı sil
  }, [isRunning]);

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#111827' }}>
      <View
        style={{
          flex: 1,
          justifyContent: 'center',
          padding: 24,
          gap: 16,
        }}
      >
        {/* KART */}
        <View
          style={{
            borderRadius: 20,
            backgroundColor: '#1f2937',
            padding: 24,
            gap: 16,
          }}
        >
          <Text style={{ fontSize: 18, fontWeight: '600', color: '#f1f5f9' }}>
            Time Remaining
          </Text>

          {/* BÜYÜK SAYI: sayarken mavi, dururken turuncu */}
          <Text
            style={{
              fontSize: 48,
              fontWeight: '700',
              color: isRunning ? '#38bdf8' : '#f97316',
              textAlign: 'center',
            }}
          >
            {timeLeft}s
          </Text>

          {/* BAŞLAT/DURAKLAT: yazısı ve rengi duruma göre değişir */}
          <Button
            title={isRunning ? 'Pause' : 'Start'}
            onPress={() => setRunning((prev) => !prev)}     // true ↔ false
            color={isRunning ? '#f97316' : '#2563eb'}
          />

          {/* SIFIRLA: zaten başlangıçtaysa ve durmuşsa pasif */}
          <Button
            title="Reset"
            onPress={() => {
              setTimeLeft(10);
              setRunning(false);
            }}
            color="#0ea5e9"
            disabled={timeLeft === 10 && !isRunning}
          />
        </View>
      </View>
    </SafeAreaView>
  );
}
```

> **Mantık zinciri:** Start'a bas → `isRunning` true olur → useEffect yeniden çalışır ve timer kurar → her saniye `timeLeft` 1 azalır → 0'a gelince `setRunning(false)` → useEffect yeniden çalışır, cleanup eski timer'ı siler, `!isRunning` olduğu için yenisini kurmaz.
> **Neden `setTimeLeft((prev) => ...)`?** Effect sadece `isRunning` değişince kurulduğu için içerideki `timeLeft` donmuş eski değerde kalır. `prev` ise her zaman güncel değeri verir (W4 Timer örneğindeki kural).

### Olası sorular – Kod 2

**S1. Geri sayım 10 yerine 30 saniyeden başlasın**
```jsx
// ⚠️ 10 sayısı ÜÇ yerde geçiyor, üçü de değişmeli:
const [timeLeft, setTimeLeft] = useState(30);          // 👈 1) başlangıç
setTimeLeft(30);                                       // 👈 2) Reset butonunda
disabled={timeLeft === 30 && !isRunning}               // 👈 3) Reset'in disabled'ında
```
> Daha temiz yol: component'in DIŞINA `const START = 30;` yaz, üç yerde de `10` yerine `START` kullan. Sonra tek yerden değiştirirsin.

**S2. Butonların ve sayının renklerini değiştir**
```jsx
color={isRunning ? '#ef4444' : '#22c55e'}                // 👈 Start yeşil, Pause kırmızı
color: isRunning ? '#22c55e' : '#94a3b8',                // 👈 büyük sayının style'ında
```

**S3. Süre bitince "Süre doldu!" yazsın**
```jsx
{/* Büyük sayı Text'inin ALTINA */}
{timeLeft === 0 && (
  <Text style={{ color: '#ef4444', textAlign: 'center', fontSize: 18, fontWeight: '600' }}>
    Süre doldu!
  </Text>
)}
```

**S4. Süre bitince uyarı (Alert) çıksın**
```jsx
import { Alert, Button, SafeAreaView, Text, View } from 'react-native';   // 👈 Alert

// İlk useEffect'in ALTINA, İKİNCİ bir useEffect (component İÇİNDE):
useEffect(() => {
  if (timeLeft === 0) {
    Alert.alert('Bitti', 'Süre doldu!');
  }
}, [timeLeft]);                                 // 👈 timeLeft her değiştiğinde kontrol et
```

**S5. Son 3 saniyede sayı kırmızı olsun**
```jsx
color: timeLeft <= 3 ? '#ef4444' : isRunning ? '#38bdf8' : '#f97316',   // 👈 önce "≤3 mü?" kontrolü
```

**S6. Sayaç 1 saniye yerine yarım saniyede bir azalsın**
```jsx
}, 500);   // 👈 setInterval'ın sonundaki 1000 → 500 (milisaniye: 1000 ms = 1 sn)
```

**S7. "+5 saniye" butonu ekle**
```jsx
{/* Reset butonunun ALTINA */}
<Button
  title="+5 saniye"
  onPress={() => setTimeLeft((prev) => prev + 5)}   // 👈 mevcut değere 5 ekle
  color="#a855f7"
/>
```

**S8. Süre 0'a gelince Start butonu pasif olsun**
```jsx
<Button
  title={isRunning ? 'Pause' : 'Start'}
  onPress={() => setRunning((prev) => !prev)}
  color={isRunning ? '#f97316' : '#2563eb'}
  disabled={timeLeft === 0}                         // 👈 YENİ: süre bittiyse başlatılamaz (Reset gerekir)
/>
```

**S9. Durumu yazıyla göster (Sayıyor / Duraklatıldı)**
```jsx
{/* "Time Remaining" başlığının ALTINA */}
<Text style={{ color: '#94a3b8' }}>
  {isRunning ? 'Sayıyor...' : 'Duraklatıldı'}
</Text>
```

**S10. Butonları yan yana diz**
```jsx
{/* İki Button'ı bir row View ile SAR */}
<View style={{ flexDirection: 'row', gap: 12 }}>
  <View style={{ flex: 1 }}>                {/* 👈 her butonu flex:1 kutuya koy → eşit genişlik */}
    <Button title={isRunning ? 'Pause' : 'Start'} onPress={() => setRunning((prev) => !prev)} color={isRunning ? '#f97316' : '#2563eb'} />
  </View>
  <View style={{ flex: 1 }}>
    <Button title="Reset" onPress={() => { setTimeLeft(10); setRunning(false); }} color="#0ea5e9" disabled={timeLeft === 10 && !isRunning} />
  </View>
</View>
```
> Button'a doğrudan `style={{ flex: 1 }}` verilemez (style yok), bu yüzden her biri bir View ile sarılır.

**S11. Süreyi dakika:saniye göster (ör. 90 → 1:30)**
```jsx
// useEffect'lerin ALTINA (component İÇİNDE, return'den ÖNCE):
const minutes = Math.floor(timeLeft / 60);
const secs = String(timeLeft % 60).padStart(2, '0');

{minutes}:{secs}        {/* 👈 büyük sayı Text'inde {timeLeft}s yerine */}
```

**S12 (zor). Geri değil ileri saysın (0'dan 10'a)**
```jsx
const [timeLeft, setTimeLeft] = useState(0);                // 👈 0'dan başla

setTimeLeft((prev) => {
  if (prev >= 9) {                                          // 👈 hedefin 1 eksiğine gelince
    setRunning(false);
    return 10;                                              // 👈 hedefte dur
  }
  return prev + 1;                                          // 👈 - yerine +
});

// Reset'te: setTimeLeft(0)  ve  disabled={timeLeft === 0 && !isRunning}   👈 10 → 0
```

---

## ⚠️ Sık hatalar
- `Button`'a `style` vermek → **hiçbir şey olmaz** (hata da vermez, sessizce görmezden gelir). Görünüm için `TouchableOpacity` kullan.
- `title` vermemek ya da sayı vermek → hata. `title` her zaman yazı olmalı: `title={String(sayi)}`.
- `onPress={fonksiyon()}` → sayfa açılınca hemen çalışır. Parametre gerekiyorsa `onPress={() => fonksiyon(x)}`.
- Input'tan gelen değerleri `Number()` ile çevirmeden toplamak → `'2' + '3'` = `'23'`.
- Aynı sabit sayı birden fazla yerde geçiyorsa (Kod 2'deki 10 gibi) **hepsini** değiştir.
