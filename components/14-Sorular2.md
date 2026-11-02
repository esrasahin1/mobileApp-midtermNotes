# 14 – Sorular 2 (Liste + Seçim Tarzı Sorular)

Bu dosyadaki sorular, "City Picker" sınav sorusunun tarzında: **bir veri listesi → FlatList ile göster → dokununca seç → stile ve sayaca yansıt.** Her sorunun altında İngilizce soru metni (sınav formatında), tam çözüm kodu ve önemli notlar var. Her kod tek başına çalışan tam bir `App.js`: Snack'te `App.js`'in tamamının yerine yapıştırılır. Hepsinin hatasız derlenip ekrana çizildiği test edildi.

> ⚠️ Sınav kuralı: **"No points will be awarded for faulty/incorrect code."** Teslim etmeden önce mutlaka çalıştır, her maddeyi tek tek dene (seç → seçimi kaldır → sayaç değişiyor mu?).

---

## 🧠 Ortak kalıp (bütün sorular bunun üzerine kurulu)

| İstek | Kalıp |
|---|---|
| **Birden fazla** seçim (checkbox mantığı) | `const [selected, setSelected] = useState({});` → `setSelected(prev => ({ ...prev, [id]: !prev[id] }))` |
| **Tek** seçim (radio mantığı) | `const [selectedId, setSelectedId] = useState(null);` → `setSelectedId(prev => prev === id ? null : id)` |
| Seçili mi? | çoklu: `!!selected[item.id]` · tek: `item.id === selectedId` |
| Seçili sayısı | `Object.values(selected).filter(Boolean).length` |
| Toplam sayı | `dizi.length` |
| Seçime göre stil | `borderColor: isSelected ? '#22c55e' : '#e2e8f0'` |
| Yuvarlak resim | `style={{ width: 48, height: 48, borderRadius: 24 }}` (borderRadius = yarısı) |
| Resim solda, yazı sağda | satıra `flexDirection: 'row', alignItems: 'center', gap: 12` |
| Sayaç **altta sabit** | FlatList'e `style={{ flex: 1 }}`, sayaç Text'i FlatList'in **altına** (dışına) |
| Adet / miktar | `{ '1': 2 }` → `(prev[id] || 0) + 1` |
| Listedeki bir elemanı güncelle | `prev.map(it => it.id === id ? { ...it, votes: it.votes + 1 } : it)` |
| Listeden sil | `prev.filter(it => !selected[it.id])` |

---

## 📥 Verilen veri farklı şekildeyse (`citiess` gibi)

Soruda veri bir dosyadan geliyorsa önce **şekline bak**, sonra uyarla:

**a) Nesne dizisi (id'li):** `[{ id: '1', name: 'Istanbul' }, ...]` → S1'deki gibi kullan.

**b) Sadece isim dizisi:** `['Istanbul', 'Ankara', ...]`
```jsx
keyExtractor={(item) => item}                     // item'ın kendisi isim
const isSelected = !!selected[item];               // id yerine isim
onPress={() => toggleCity(item)}
source={{ uri: `https://picsum.photos/seed/${item}/48/48` }}
<Text>{item}</Text>                                // item.name değil, item
```

**c) id'siz nesne dizisi:** `[{ name: 'Istanbul', plate: 34 }, ...]`
```jsx
keyExtractor={(item) => item.name}                // benzersiz alan neyse onu kullan (name veya plate)
const isSelected = !!selected[item.name];
onPress={() => toggleCity(item.name)}
```

**d) Nesne (dizi değil):** `{ ist: 'Istanbul', ank: 'Ankara' }` veya `{ 1: { name: 'Istanbul' } }`
```jsx
// FlatList dizi ister → önce diziye çevir (component DIŞINDA):
const cityList = Object.entries(citiess).map(([id, name]) => ({ id, name }));          // { ist: 'Istanbul' } için
// const cityList = Object.entries(citiess).map(([id, c]) => ({ id, name: c.name }));  // { 1: { name } } için

data={cityList}                                    // citiess yerine
<Text>Selected cities: {selectedCount} / {cityList.length}</Text>
```
> "Data source should be citiess object" deniyorsa veriyi **kendin yeniden yazma**, verilen değişkeni kullan (gerekirse yukarıdaki gibi dönüştürerek).

> **Resim adresinde Türkçe karakter:** `İzmir`, `Eskişehir` gibi isimlerle resim gelmezse adresi güvenli hale getir: `` `https://picsum.photos/seed/${encodeURIComponent(item.name)}/48/48` ``

---

## S1. City Picker (çoklu seçim) – sınavdaki soru

**Question:**
*Write a React Native app where users can select cities from a list and see how many cities are selected.*
- *The app title must be "City Picker". (5p)*
- *The component's data source of the cities should be the `citiess` object. (5p)*
- *When a city is tapped, it should be selected if not already selected, and deselected if it is already selected. (10p)*
- *To the left of each city name, display a round image (48x48 px) whose source is `https://picsum.photos/seed/{cityName}/48/48`. (20p)*
- *Selected cities must have a green border (#22c55e) and light-green background (#dcfce7). Unselected cities must have a gray border (#e2e8f0) and white background. (10p)*
- *At the bottom, display "Selected cities: X / Y". (10p)*

```jsx
import { useState } from 'react';
import { FlatList, Image, SafeAreaView, Text, TouchableOpacity, View } from 'react-native';

// Veri kaynağı: soruda verilen dosyadaki "citiess" (dizi şeklinde varsayıldı)
const citiess = [
  { id: '1', name: 'Istanbul' },
  { id: '2', name: 'Ankara' },
  { id: '3', name: 'İzmir' },
  { id: '4', name: 'Bursa' },
  { id: '5', name: 'Antalya' },
  { id: '6', name: 'Konya' },
  { id: '7', name: 'Trabzon' },
  { id: '8', name: 'Sivas' },
  { id: '9', name: 'Eskişehir' },
  { id: '10', name: 'Gaziantep' },
];

export default function App() {
  // Seçili şehirler: { '1': true, '8': true } → birden fazla seçilebilir (checkbox mantığı)
  const [selected, setSelected] = useState({});

  // Tıklanınca: seçili değilse seç, seçiliyse seçimi kaldır (tersine çevir)
  const toggleCity = (id) => {
    setSelected((prev) => ({ ...prev, [id]: !prev[id] }));
  };

  // Seçili sayısı: true olanları say
  const selectedCount = Object.values(selected).filter(Boolean).length;

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#fff' }}>
      <View style={{ flex: 1, padding: 16, gap: 12 }}>
        {/* BAŞLIK (5p) */}
        <Text style={{ fontSize: 20, fontWeight: '700', color: '#0f172a' }}>City Picker</Text>

        {/* LİSTE: flex: 1 → alttaki sayaç için yer kalsın, liste kendi içinde kaysın */}
        <FlatList
          style={{ flex: 1 }}
          data={citiess}                                           // veri kaynağı citiess (5p)
          keyExtractor={(item) => item.id}
          ItemSeparatorComponent={() => <View style={{ height: 8 }} />}
          renderItem={({ item }) => {
            const isSelected = !!selected[item.id];                // bu şehir seçili mi?
            return (
              <TouchableOpacity
                onPress={() => toggleCity(item.id)}                // seç / kaldır (10p)
                style={{
                  flexDirection: 'row',                            // resim solda, isim sağda
                  alignItems: 'center',
                  gap: 12,
                  padding: 12,
                  borderRadius: 12,
                  borderWidth: 1,
                  borderColor: isSelected ? '#22c55e' : '#e2e8f0',      // seçili: yeşil, değil: gri (10p)
                  backgroundColor: isSelected ? '#dcfce7' : '#fff',     // seçili: açık yeşil, değil: beyaz
                }}
              >
                {/* YUVARLAK RESİM 48x48 (20p): borderRadius = 24 (yarısı) */}
                <Image
                  source={{ uri: `https://picsum.photos/seed/${item.name}/48/48` }}
                  style={{ width: 48, height: 48, borderRadius: 24 }}
                />
                <Text style={{ fontSize: 16, color: '#0f172a' }}>{item.name}</Text>
              </TouchableOpacity>
            );
          }}
        />

        {/* ALTTA SAYAÇ (10p) */}
        <Text style={{ textAlign: 'center', fontSize: 16, fontWeight: '700', color: '#0f172a' }}>
          Selected cities: {selectedCount} / {citiess.length}
        </Text>
      </View>
    </SafeAreaView>
  );
}
```
> **Puan nereden geliyor:** başlık → `<Text>City Picker</Text>` · veri → `data={citiess}` · seç/kaldır → `!prev[id]` · resim → `Image` + template string + `borderRadius: 24` · renkler → iki ternary · sayaç → `filter(Boolean).length` ve `citiess.length`.
> **Alternatif (dizi ile seçim):** `const [selectedIds, setSelectedIds] = useState([]);` → seç/kaldır: `setSelectedIds(prev => prev.includes(id) ? prev.filter(x => x !== id) : [...prev, id])` · seçili mi: `selectedIds.includes(item.id)` · sayı: `selectedIds.length`. İkisi de doğru, birini seç.

---

## S2. Tek şehir seçimi (radio mantığı)

**Question:**
*Write an app where the user can select only ONE city from the list.*
- *Tapping a city selects it; any previously selected city must be deselected automatically. Tapping the selected city again deselects it.*
- *Show a radio circle on the right side of each row.*
- *At the bottom, display "Selected city: {name}" or "Selected city: None".*

```jsx
import { useState } from 'react';
import { FlatList, Image, SafeAreaView, Text, TouchableOpacity, View } from 'react-native';

const citiess = [
  { id: '1', name: 'Istanbul' },
  { id: '2', name: 'Ankara' },
  { id: '3', name: 'İzmir' },
  { id: '4', name: 'Bursa' },
  { id: '5', name: 'Antalya' },
  { id: '6', name: 'Konya' },
];

export default function App() {
  // TEK seçim (radio mantığı): seçili şehrin id'si ya da null
  const [selectedId, setSelectedId] = useState(null);

  // Aynı şehre tekrar basılırsa seçimi kaldır, değilse bunu seç (öncekinin seçimi otomatik kalkar)
  const selectCity = (id) => {
    setSelectedId((prev) => (prev === id ? null : id));
  };

  const selectedCity = citiess.find((c) => c.id === selectedId);   // seçili şehrin nesnesi (yoksa undefined)

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#fff' }}>
      <View style={{ flex: 1, padding: 16, gap: 12 }}>
        <Text style={{ fontSize: 20, fontWeight: '700', color: '#0f172a' }}>Choose Your City</Text>

        <FlatList
          style={{ flex: 1 }}
          data={citiess}
          keyExtractor={(item) => item.id}
          ItemSeparatorComponent={() => <View style={{ height: 8 }} />}
          renderItem={({ item }) => {
            const isSelected = item.id === selectedId;           // seçili olan bu mu?
            return (
              <TouchableOpacity
                onPress={() => selectCity(item.id)}
                style={{
                  flexDirection: 'row',
                  alignItems: 'center',
                  gap: 12,
                  padding: 12,
                  borderRadius: 12,
                  borderWidth: 2,
                  borderColor: isSelected ? '#2563eb' : '#e2e8f0',
                  backgroundColor: isSelected ? '#dbeafe' : '#fff',
                }}
              >
                <Image
                  source={{ uri: `https://picsum.photos/seed/${item.name}/48/48` }}
                  style={{ width: 48, height: 48, borderRadius: 24 }}
                />
                <Text style={{ fontSize: 16, color: '#0f172a', flex: 1 }}>{item.name}</Text>
                {/* Radio daire: dış halka + seçiliyse iç nokta */}
                <View
                  style={{
                    width: 22, height: 22, borderRadius: 11, borderWidth: 2,
                    borderColor: isSelected ? '#2563eb' : '#94a3b8',
                    alignItems: 'center', justifyContent: 'center',
                  }}
                >
                  {isSelected && <View style={{ width: 10, height: 10, borderRadius: 5, backgroundColor: '#2563eb' }} />}
                </View>
              </TouchableOpacity>
            );
          }}
        />

        <Text style={{ textAlign: 'center', fontSize: 16, fontWeight: '700', color: '#0f172a' }}>
          Selected city: {selectedCity ? selectedCity.name : 'None'}
        </Text>
      </View>
    </SafeAreaView>
  );
}
```
> S1'den farkı sadece state: nesne yerine **tek bir id**. Radio dairesi RadioButton dosyasındakiyle aynı (dış halka + iç nokta).

---

## S3. Aramalı City Picker

**Question:**
*Extend the City Picker with a search input at the top. Only cities whose names contain the search text should be listed (case-insensitive). Selections must be kept while searching. Show "Selected cities: X / Y" and a "Clear selection" button.*

```jsx
import { useState } from 'react';
import { Button, FlatList, Image, SafeAreaView, Text, TextInput, TouchableOpacity, View } from 'react-native';

const citiess = [
  { id: '1', name: 'Istanbul' },
  { id: '2', name: 'Ankara' },
  { id: '3', name: 'İzmir' },
  { id: '4', name: 'Bursa' },
  { id: '5', name: 'Antalya' },
  { id: '6', name: 'Konya' },
  { id: '7', name: 'Trabzon' },
  { id: '8', name: 'Sivas' },
  { id: '9', name: 'Eskişehir' },
  { id: '10', name: 'Gaziantep' },
];

export default function App() {
  const [query, setQuery] = useState('');          // arama metni
  const [selected, setSelected] = useState({});    // çoklu seçim

  const toggleCity = (id) => {
    setSelected((prev) => ({ ...prev, [id]: !prev[id] }));
  };

  // Aramaya uyan şehirler (Türkçe harfler için toLocaleLowerCase('tr'))
  const filtered = citiess.filter((c) =>
    c.name.toLocaleLowerCase('tr').includes(query.toLocaleLowerCase('tr'))
  );

  const selectedCount = Object.values(selected).filter(Boolean).length;

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#fff' }}>
      <View style={{ flex: 1, padding: 16, gap: 12 }}>
        <Text style={{ fontSize: 20, fontWeight: '700', color: '#0f172a' }}>City Picker</Text>

        {/* ARAMA KUTUSU */}
        <TextInput
          value={query}
          onChangeText={setQuery}
          placeholder="Search city..."
          style={{ borderWidth: 1, borderColor: '#cbd5e1', borderRadius: 10, padding: 12 }}
        />

        <FlatList
          style={{ flex: 1 }}
          data={filtered}                                    // filtrelenmiş liste
          keyExtractor={(item) => item.id}
          ItemSeparatorComponent={() => <View style={{ height: 8 }} />}
          ListEmptyComponent={<Text style={{ textAlign: 'center', color: '#94a3b8' }}>No city found.</Text>}
          renderItem={({ item }) => {
            const isSelected = !!selected[item.id];
            return (
              <TouchableOpacity
                onPress={() => toggleCity(item.id)}
                style={{
                  flexDirection: 'row', alignItems: 'center', gap: 12, padding: 12,
                  borderRadius: 12, borderWidth: 1,
                  borderColor: isSelected ? '#22c55e' : '#e2e8f0',
                  backgroundColor: isSelected ? '#dcfce7' : '#fff',
                }}
              >
                <Image
                  source={{ uri: `https://picsum.photos/seed/${item.name}/48/48` }}
                  style={{ width: 48, height: 48, borderRadius: 24 }}
                />
                <Text style={{ fontSize: 16, color: '#0f172a' }}>{item.name}</Text>
              </TouchableOpacity>
            );
          }}
        />

        <Text style={{ textAlign: 'center', fontSize: 16, fontWeight: '700' }}>
          Selected cities: {selectedCount} / {citiess.length}
        </Text>
        <Text style={{ textAlign: 'center', color: '#64748b' }}>
          Showing {filtered.length} of {citiess.length}
        </Text>
        <Button title="Clear selection" color="#ef4444" onPress={() => setSelected({})} disabled={selectedCount === 0} />
      </View>
    </SafeAreaView>
  );
}
```
> Seçimler arama sırasında korunuyor, çünkü `selected` nesnesi tüm şehirler için tutuluyor, sadece **gösterilen** liste değişiyor.
> Y (toplam) filtreli liste değil, **tüm** şehir sayısı (`citiess.length`). "Showing X of Y" için `filtered.length`.

---

## S4. Sepet: adet artır / azalt + toplam fiyat

**Question:**
*Write a café menu app. Each product shows an image, name and price, and has "−" and "+" buttons to change its quantity (quantity cannot go below 0). Products with quantity > 0 must be highlighted. At the bottom, show the total number of items and the total price, and a "Clear cart" button.*

```jsx
import { useState } from 'react';
import { FlatList, Image, SafeAreaView, Text, TouchableOpacity, View } from 'react-native';

const products = [
  { id: '1', name: 'Coffee', price: 85 },
  { id: '2', name: 'Tea', price: 40 },
  { id: '3', name: 'Croissant', price: 95 },
  { id: '4', name: 'Cheesecake', price: 150 },
  { id: '5', name: 'Water', price: 20 },
];

export default function App() {
  // Sepet: her ürünün adedi → { '1': 2, '3': 1 }
  const [cart, setCart] = useState({});

  // Adedi değiştir: delta = +1 veya -1, 0'ın altına inmesin
  const changeQty = (id, delta) => {
    setCart((prev) => ({ ...prev, [id]: Math.max(0, (prev[id] || 0) + delta) }));
  };

  // Toplam adet ve toplam fiyat
  const totalItems = Object.values(cart).reduce((sum, q) => sum + q, 0);
  const totalPrice = products.reduce((sum, p) => sum + p.price * (cart[p.id] || 0), 0);

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#fff' }}>
      <View style={{ flex: 1, padding: 16, gap: 12 }}>
        <Text style={{ fontSize: 20, fontWeight: '700', color: '#0f172a' }}>Café Menu</Text>

        <FlatList
          style={{ flex: 1 }}
          data={products}
          keyExtractor={(item) => item.id}
          ItemSeparatorComponent={() => <View style={{ height: 8 }} />}
          renderItem={({ item }) => {
            const qty = cart[item.id] || 0;                     // bu ürünün adedi (yoksa 0)
            return (
              <View
                style={{
                  flexDirection: 'row', alignItems: 'center', gap: 12, padding: 12,
                  borderRadius: 12, borderWidth: 1,
                  borderColor: qty > 0 ? '#22c55e' : '#e2e8f0',
                  backgroundColor: qty > 0 ? '#dcfce7' : '#fff',
                }}
              >
                <Image
                  source={{ uri: `https://picsum.photos/seed/${item.name}/48/48` }}
                  style={{ width: 48, height: 48, borderRadius: 8 }}
                />
                {/* İsim + fiyat: flex 1 → butonlar sağa itilir */}
                <View style={{ flex: 1 }}>
                  <Text style={{ fontSize: 16, fontWeight: '600' }}>{item.name}</Text>
                  <Text style={{ color: '#64748b' }}>{item.price} TL</Text>
                </View>
                {/* − adet + */}
                <TouchableOpacity
                  onPress={() => changeQty(item.id, -1)}
                  disabled={qty === 0}
                  style={{ width: 32, height: 32, borderRadius: 16, backgroundColor: '#e2e8f0', alignItems: 'center', justifyContent: 'center', opacity: qty === 0 ? 0.4 : 1 }}
                >
                  <Text style={{ fontSize: 18 }}>−</Text>
                </TouchableOpacity>
                <Text style={{ width: 24, textAlign: 'center', fontWeight: '700' }}>{qty}</Text>
                <TouchableOpacity
                  onPress={() => changeQty(item.id, 1)}
                  style={{ width: 32, height: 32, borderRadius: 16, backgroundColor: '#22c55e', alignItems: 'center', justifyContent: 'center' }}
                >
                  <Text style={{ fontSize: 18, color: '#fff' }}>+</Text>
                </TouchableOpacity>
              </View>
            );
          }}
        />

        <Text style={{ textAlign: 'center', fontSize: 16, fontWeight: '700' }}>
          Items: {totalItems} · Total: {totalPrice} TL
        </Text>
        <TouchableOpacity
          onPress={() => setCart({})}
          disabled={totalItems === 0}
          style={{ padding: 14, borderRadius: 12, backgroundColor: totalItems === 0 ? '#cbd5e1' : '#ef4444', alignItems: 'center' }}
        >
          <Text style={{ color: '#fff', fontWeight: '700' }}>Clear cart</Text>
        </TouchableOpacity>
      </View>
    </SafeAreaView>
  );
}
```
> Adet `{ '1': 2 }` şeklinde tutuluyor. `(prev[id] || 0)` → hiç eklenmemişse 0 say. `Math.max(0, ...)` → eksiye düşmesin.
> Toplam fiyat: her ürünün `fiyat × adet` değeri `reduce` ile toplanıyor.

---

## S5. Sınırlı seçim (en fazla 3 ders)

**Question:**
*Write a course registration app. The user can select at most 3 courses. If the limit is reached, unselected courses must look disabled (faded) and tapping them must show an alert. Show "Selected courses: X / 3" and the total credits of the selected courses.*

```jsx
import { useState } from 'react';
import { Alert, FlatList, SafeAreaView, Text, TouchableOpacity, View } from 'react-native';

const MAX = 3;                                   // en fazla kaç ders seçilebilir

const courses = [
  { id: '1', title: 'Mobile App Development', credit: 3 },
  { id: '2', title: 'Database Systems', credit: 4 },
  { id: '3', title: 'Web Programming', credit: 3 },
  { id: '4', title: 'Data Structures', credit: 4 },
  { id: '5', title: 'Operating Systems', credit: 3 },
  { id: '6', title: 'Computer Networks', credit: 3 },
];

export default function App() {
  const [selected, setSelected] = useState({});

  const selectedCount = Object.values(selected).filter(Boolean).length;
  const limitReached = selectedCount >= MAX;     // sınıra ulaşıldı mı?

  const toggleCourse = (id) => {
    // Seçili DEĞİLSE ve sınır doluysa ekleme, uyar
    if (!selected[id] && limitReached) {
      Alert.alert('Limit reached', `You can select at most ${MAX} courses.`);
      return;
    }
    setSelected((prev) => ({ ...prev, [id]: !prev[id] }));
  };

  // Seçili derslerin toplam kredisi
  const totalCredit = courses
    .filter((c) => selected[c.id])
    .reduce((sum, c) => sum + c.credit, 0);

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#fff' }}>
      <View style={{ flex: 1, padding: 16, gap: 12 }}>
        <Text style={{ fontSize: 20, fontWeight: '700' }}>Course Registration</Text>

        <FlatList
          style={{ flex: 1 }}
          data={courses}
          keyExtractor={(item) => item.id}
          ItemSeparatorComponent={() => <View style={{ height: 8 }} />}
          renderItem={({ item }) => {
            const isSelected = !!selected[item.id];
            const isLocked = !isSelected && limitReached;          // seçilemez durumda mı?
            return (
              <TouchableOpacity
                onPress={() => toggleCourse(item.id)}
                style={{
                  padding: 14, borderRadius: 12, borderWidth: 1,
                  borderColor: isSelected ? '#22c55e' : '#e2e8f0',
                  backgroundColor: isSelected ? '#dcfce7' : '#fff',
                  opacity: isLocked ? 0.4 : 1,                       // sınır dolunca seçilmeyenler soluk
                }}
              >
                <Text style={{ fontSize: 16, fontWeight: '600' }}>{item.title}</Text>
                <Text style={{ color: '#64748b' }}>{item.credit} credits</Text>
              </TouchableOpacity>
            );
          }}
        />

        <Text style={{ textAlign: 'center', fontSize: 16, fontWeight: '700' }}>
          Selected courses: {selectedCount} / {MAX}
        </Text>
        <Text style={{ textAlign: 'center', color: '#64748b' }}>Total credits: {totalCredit}</Text>
      </View>
    </SafeAreaView>
  );
}
```
> Kontrol seçmeden ÖNCE yapılıyor: "seçili değil VE sınır dolu" ise `return`. Seçili olanı kaldırmak her zaman serbest.
> Sınırı değiştirmek için sadece `const MAX = 3;` satırı.

---

## S6. Favoriler + 'sadece favorileri göster' anahtarı

**Question:**
*Write a movie list. Each movie has a heart icon; tapping it adds/removes the movie from favorites. A Switch at the top filters the list to show only favorite movies. If there are no favorites, show "No favorites yet." Show "Favorites: X / Y" at the bottom.*

```jsx
import { useState } from 'react';
import { FlatList, Image, SafeAreaView, Switch, Text, TouchableOpacity, View } from 'react-native';
import { Ionicons } from '@expo/vector-icons';

const movies = [
  { id: '1', title: 'Inception', year: 2010 },
  { id: '2', title: 'Interstellar', year: 2014 },
  { id: '3', title: 'The Matrix', year: 1999 },
  { id: '4', title: 'Parasite', year: 2019 },
  { id: '5', title: 'Up', year: 2009 },
];

export default function App() {
  const [favorites, setFavorites] = useState({});          // favoriler
  const [onlyFavorites, setOnlyFavorites] = useState(false);   // sadece favorileri göster

  const toggleFavorite = (id) => {
    setFavorites((prev) => ({ ...prev, [id]: !prev[id] }));
  };

  // Switch açıksa sadece favoriler, değilse hepsi
  const data = onlyFavorites ? movies.filter((m) => favorites[m.id]) : movies;
  const favCount = Object.values(favorites).filter(Boolean).length;

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#fff' }}>
      <View style={{ flex: 1, padding: 16, gap: 12 }}>
        <Text style={{ fontSize: 20, fontWeight: '700' }}>Movie List</Text>

        {/* Filtre anahtarı */}
        <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
          <Text>Show only favorites</Text>
          <Switch value={onlyFavorites} onValueChange={setOnlyFavorites} />
        </View>

        <FlatList
          style={{ flex: 1 }}
          data={data}
          keyExtractor={(item) => item.id}
          ItemSeparatorComponent={() => <View style={{ height: 8 }} />}
          ListEmptyComponent={<Text style={{ textAlign: 'center', color: '#94a3b8' }}>No favorites yet.</Text>}
          renderItem={({ item }) => {
            const isFav = !!favorites[item.id];
            return (
              <View
                style={{
                  flexDirection: 'row', alignItems: 'center', gap: 12, padding: 12,
                  borderRadius: 12, borderWidth: 1, borderColor: '#e2e8f0',
                }}
              >
                <Image
                  source={{ uri: `https://picsum.photos/seed/${item.title}/60/80` }}
                  style={{ width: 45, height: 60, borderRadius: 6 }}
                />
                <View style={{ flex: 1 }}>
                  <Text style={{ fontSize: 16, fontWeight: '600' }}>{item.title}</Text>
                  <Text style={{ color: '#64748b' }}>{item.year}</Text>
                </View>
                {/* Kalp: sadece ikona basınca favori değişir */}
                <TouchableOpacity onPress={() => toggleFavorite(item.id)} hitSlop={10}>
                  <Ionicons name={isFav ? 'heart' : 'heart-outline'} size={26} color={isFav ? '#ef4444' : '#94a3b8'} />
                </TouchableOpacity>
              </View>
            );
          }}
        />

        <Text style={{ textAlign: 'center', fontSize: 16, fontWeight: '700' }}>
          Favorites: {favCount} / {movies.length}
        </Text>
      </View>
    </SafeAreaView>
  );
}
```
> Gösterilecek liste duruma göre seçiliyor: `onlyFavorites ? filtrelenmiş : hepsi`. Boşsa `ListEmptyComponent` devreye giriyor.
> İkon için `@expo/vector-icons` (Snack'te hazır). Kalbe basmak kolay olsun diye `hitSlop={10}`.

---

## S7. Fotoğraf galerisi: grid + seçilenleri sil

**Question:**
*Write a photo gallery with 12 images in a 3-column grid. Tapping a photo selects/deselects it (selected photos have a green border). A "Delete selected (X)" button removes the selected photos and is disabled when nothing is selected. Add a "Restore all" option.*

```jsx
import { useState } from 'react';
import { FlatList, Image, SafeAreaView, Text, TouchableOpacity, View } from 'react-native';

// 12 fotoğraf üret (id 1–12)
const INITIAL_PHOTOS = Array.from({ length: 12 }, (_, i) => ({ id: String(i + 1) }));

export default function App() {
  // Silme olacağı için liste de STATE'te tutuluyor
  const [photos, setPhotos] = useState(INITIAL_PHOTOS);
  const [selected, setSelected] = useState({});

  const toggle = (id) => setSelected((prev) => ({ ...prev, [id]: !prev[id] }));

  const selectedCount = Object.values(selected).filter(Boolean).length;

  // Seçilenleri sil: seçili OLMAYANLARI bırak
  const deleteSelected = () => {
    setPhotos((prev) => prev.filter((p) => !selected[p.id]));
    setSelected({});
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#fff' }}>
      <View style={{ flex: 1, padding: 16, gap: 12 }}>
        <Text style={{ fontSize: 20, fontWeight: '700' }}>Photo Gallery</Text>

        <FlatList
          style={{ flex: 1 }}
          data={photos}
          keyExtractor={(item) => item.id}
          numColumns={3}                                     // 3 sütunlu grid
          columnWrapperStyle={{ gap: 8 }}                    // yan yana boşluk
          contentContainerStyle={{ gap: 8 }}                 // satırlar arası boşluk
          renderItem={({ item }) => {
            const isSelected = !!selected[item.id];
            return (
              <TouchableOpacity
                onPress={() => toggle(item.id)}
                style={{
                  flex: 1,
                  maxWidth: '32%',                             // son satır eksikse kareler büyümesin
                  borderRadius: 8,
                  borderWidth: 3,
                  borderColor: isSelected ? '#22c55e' : 'transparent',
                  overflow: 'hidden',
                }}
              >
                <Image
                  source={{ uri: `https://picsum.photos/seed/photo${item.id}/200/200` }}
                  style={{ width: '100%', aspectRatio: 1 }}  // kare (genişlik = yükseklik)
                />
              </TouchableOpacity>
            );
          }}
          ListEmptyComponent={<Text style={{ textAlign: 'center', color: '#94a3b8' }}>All photos deleted.</Text>}
        />

        <Text style={{ textAlign: 'center', fontWeight: '700' }}>
          Selected: {selectedCount} / {photos.length}
        </Text>
        <TouchableOpacity
          onPress={deleteSelected}
          disabled={selectedCount === 0}
          style={{ padding: 14, borderRadius: 12, alignItems: 'center', backgroundColor: selectedCount === 0 ? '#cbd5e1' : '#ef4444' }}
        >
          <Text style={{ color: '#fff', fontWeight: '700' }}>Delete selected ({selectedCount})</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={() => { setPhotos(INITIAL_PHOTOS); setSelected({}); }} style={{ alignItems: 'center' }}>
          <Text style={{ color: '#2563eb' }}>Restore all</Text>
        </TouchableOpacity>
      </View>
    </SafeAreaView>
  );
}
```
> Silme olacağı için **liste de state'te** (`photos`). Silme: seçili OLMAYANLARI bırak (`filter`), sonra seçimi sıfırla.
> Grid: `numColumns={3}` + `columnWrapperStyle={{ gap: 8 }}`. Kare resim için `aspectRatio: 1`. `maxWidth: '32%'` son satır eksik kalınca karelerin büyümesini engelliyor.

---

## S8. Mini quiz: her soruda tek cevap + puan

**Question:**
*Write a quiz app with 3 multiple-choice questions. The user selects one option per question. The "Submit" button is enabled only when all questions are answered. After submitting, correct options turn green, wrong chosen options turn red, and the score "Score: X / 3" is displayed. A "Try again" button resets the quiz.*

```jsx
import { useState } from 'react';
import { Button, SafeAreaView, ScrollView, Text, TouchableOpacity, View } from 'react-native';

const questions = [
  { id: 'q1', question: 'Which component is used to display text?', options: ['View', 'Text', 'Image'], answer: 'Text' },
  { id: 'q2', question: 'Which hook stores state?', options: ['useState', 'useEffect', 'useRef'], answer: 'useState' },
  { id: 'q3', question: 'Which list renders only visible items?', options: ['ScrollView', 'FlatList', 'View'], answer: 'FlatList' },
];

export default function App() {
  const [answers, setAnswers] = useState({});        // { q1: 'Text', q2: 'useRef' }
  const [submitted, setSubmitted] = useState(false);  // gönderildi mi?

  const choose = (qid, option) => {
    if (submitted) return;                            // gönderildikten sonra değiştirilemez
    setAnswers((prev) => ({ ...prev, [qid]: option }));   // her soruda TEK cevap (radio)
  };

  const allAnswered = questions.every((q) => answers[q.id]);   // her soru cevaplandı mı?
  const score = questions.filter((q) => answers[q.id] === q.answer).length;

  const reset = () => {
    setAnswers({});
    setSubmitted(false);
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#fff' }}>
      <ScrollView contentContainerStyle={{ padding: 16, gap: 16 }}>
        <Text style={{ fontSize: 20, fontWeight: '700' }}>React Native Quiz</Text>

        {questions.map((q, index) => (
          <View key={q.id} style={{ gap: 8 }}>
            <Text style={{ fontSize: 16, fontWeight: '600' }}>{index + 1}. {q.question}</Text>
            {q.options.map((option) => {
              const isChosen = answers[q.id] === option;
              const isCorrect = option === q.answer;
              // Renk: gönderilmeden önce seçili → mavi; gönderildikten sonra doğru → yeşil, yanlış seçilmiş → kırmızı
              let bg = '#fff';
              let border = '#e2e8f0';
              if (!submitted && isChosen) { bg = '#dbeafe'; border = '#2563eb'; }
              if (submitted && isCorrect) { bg = '#dcfce7'; border = '#22c55e'; }
              if (submitted && isChosen && !isCorrect) { bg = '#fee2e2'; border = '#ef4444'; }
              return (
                <TouchableOpacity
                  key={option}
                  onPress={() => choose(q.id, option)}
                  style={{ padding: 12, borderRadius: 10, borderWidth: 1, borderColor: border, backgroundColor: bg }}
                >
                  <Text>{option}</Text>
                </TouchableOpacity>
              );
            })}
          </View>
        ))}

        {!submitted ? (
          <Button title="Submit" onPress={() => setSubmitted(true)} disabled={!allAnswered} />
        ) : (
          <View style={{ gap: 8 }}>
            <Text style={{ textAlign: 'center', fontSize: 18, fontWeight: '700' }}>
              Score: {score} / {questions.length}
            </Text>
            <Button title="Try again" onPress={reset} />
          </View>
        )}
      </ScrollView>
    </SafeAreaView>
  );
}
```
> Cevaplar `{ q1: 'Text' }` şeklinde: her soru için TEK değer (radio). `every` → hepsi cevaplandı mı · `filter(...).length` → doğru sayısı.
> Renk mantığı `let` ile adım adım belirleniyor, iç içe ternary yerine okunması daha kolay.

---

## S9. Oylama: listedeki elemanı güncelle + sırala

**Question:**
*Write a voting app. Tapping an option adds one vote to it. Each option shows its vote count, its percentage of total votes and a progress bar. The list must be sorted by votes (highest first). Show the total votes and a "Reset votes" button.*

```jsx
import { useState } from 'react';
import { Button, FlatList, SafeAreaView, Text, TouchableOpacity, View } from 'react-native';

const INITIAL = [
  { id: '1', name: 'React Native', votes: 0 },
  { id: '2', name: 'Flutter', votes: 0 },
  { id: '3', name: 'Kotlin', votes: 0 },
  { id: '4', name: 'Swift', votes: 0 },
];

export default function App() {
  // Oy sayısı listenin İÇİNDE değiştiği için liste state'te
  const [items, setItems] = useState(INITIAL);

  // Tek bir elemanı güncelle: map ile yeni dizi, sadece eşleşen id'nin votes'u +1
  const vote = (id) => {
    setItems((prev) => prev.map((it) => (it.id === id ? { ...it, votes: it.votes + 1 } : it)));
  };

  const totalVotes = items.reduce((sum, it) => sum + it.votes, 0);
  // Çok oy alan üstte: kopyasını sırala (state'i doğrudan sıralama!)
  const sorted = [...items].sort((a, b) => b.votes - a.votes);

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#fff' }}>
      <View style={{ flex: 1, padding: 16, gap: 12 }}>
        <Text style={{ fontSize: 20, fontWeight: '700' }}>Vote for Your Favorite</Text>

        <FlatList
          style={{ flex: 1 }}
          data={sorted}
          keyExtractor={(item) => item.id}
          ItemSeparatorComponent={() => <View style={{ height: 8 }} />}
          renderItem={({ item }) => {
            const percent = totalVotes === 0 ? 0 : Math.round((item.votes / totalVotes) * 100);
            return (
              <TouchableOpacity
                onPress={() => vote(item.id)}
                style={{ padding: 14, borderRadius: 12, borderWidth: 1, borderColor: '#e2e8f0', gap: 8 }}
              >
                <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
                  <Text style={{ fontSize: 16, fontWeight: '600' }}>{item.name}</Text>
                  <Text style={{ color: '#64748b' }}>{item.votes} votes · %{percent}</Text>
                </View>
                {/* Yüzde çubuğu */}
                <View style={{ height: 8, borderRadius: 4, backgroundColor: '#e2e8f0' }}>
                  <View style={{ width: `${percent}%`, height: '100%', borderRadius: 4, backgroundColor: '#22c55e' }} />
                </View>
              </TouchableOpacity>
            );
          }}
        />

        <Text style={{ textAlign: 'center', fontWeight: '700' }}>Total votes: {totalVotes}</Text>
        <Button title="Reset votes" color="#ef4444" onPress={() => setItems(INITIAL)} />
      </View>
    </SafeAreaView>
  );
}
```
> **Önemli kalıp:** listedeki tek bir elemanı güncellemek → `map` ile yeni dizi, sadece id'si eşleşeni `{ ...it, votes: it.votes + 1 }` yap.
> Sıralarken **kopyasını** sırala (`[...items].sort(...)`). `items.sort()` state'i doğrudan değiştirir (W3 kuralı).
> Sıfıra bölme: toplam 0'ken yüzde 0 gösteriliyor.

---

## S10. Restoran menüsü: kategori filtresi + sipariş

**Question:**
*Write a restaurant menu app. Category buttons (All, Main, Drink, Dessert) filter the menu. Each item has an "Add" button that increases its quantity in the order; the quantity is shown next to the item. At the bottom, show the total number of items and the total price of the order.*

```jsx
import { useState } from 'react';
import { FlatList, SafeAreaView, Text, TouchableOpacity, View } from 'react-native';

const CATEGORIES = ['All', 'Main', 'Drink', 'Dessert'];

const menu = [
  { id: '1', name: 'Burger', price: 220, category: 'Main' },
  { id: '2', name: 'Pizza', price: 260, category: 'Main' },
  { id: '3', name: 'Lemonade', price: 60, category: 'Drink' },
  { id: '4', name: 'Ayran', price: 35, category: 'Drink' },
  { id: '5', name: 'Baklava', price: 120, category: 'Dessert' },
  { id: '6', name: 'Sütlaç', price: 90, category: 'Dessert' },
];

export default function App() {
  const [category, setCategory] = useState('All');   // seçili kategori (tek seçim)
  const [order, setOrder] = useState({});            // sipariş adetleri { '1': 2 }

  const add = (id) => setOrder((prev) => ({ ...prev, [id]: (prev[id] || 0) + 1 }));

  // Kategoriye göre filtre: 'All' ise hepsi
  const visible = menu.filter((m) => category === 'All' || m.category === category);

  const totalPrice = menu.reduce((sum, m) => sum + m.price * (order[m.id] || 0), 0);
  const totalItems = Object.values(order).reduce((sum, q) => sum + q, 0);

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#fff' }}>
      <View style={{ flex: 1, padding: 16, gap: 12 }}>
        <Text style={{ fontSize: 20, fontWeight: '700' }}>Restaurant Menu</Text>

        {/* KATEGORİ BUTONLARI */}
        <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 8 }}>
          {CATEGORIES.map((c) => {
            const isActive = c === category;
            return (
              <TouchableOpacity
                key={c}
                onPress={() => setCategory(c)}
                style={{ paddingVertical: 8, paddingHorizontal: 14, borderRadius: 999, backgroundColor: isActive ? '#0f172a' : '#e2e8f0' }}
              >
                <Text style={{ color: isActive ? '#fff' : '#0f172a' }}>{c}</Text>
              </TouchableOpacity>
            );
          })}
        </View>

        <FlatList
          style={{ flex: 1 }}
          data={visible}
          keyExtractor={(item) => item.id}
          ItemSeparatorComponent={() => <View style={{ height: 8 }} />}
          renderItem={({ item }) => {
            const qty = order[item.id] || 0;
            return (
              <View style={{ flexDirection: 'row', alignItems: 'center', padding: 14, borderRadius: 12, borderWidth: 1, borderColor: '#e2e8f0' }}>
                <View style={{ flex: 1 }}>
                  <Text style={{ fontSize: 16, fontWeight: '600' }}>{item.name}</Text>
                  <Text style={{ color: '#64748b' }}>{item.price} TL · {item.category}</Text>
                </View>
                {qty > 0 && <Text style={{ marginRight: 12, fontWeight: '700' }}>x{qty}</Text>}
                <TouchableOpacity onPress={() => add(item.id)} style={{ paddingVertical: 8, paddingHorizontal: 14, borderRadius: 8, backgroundColor: '#22c55e' }}>
                  <Text style={{ color: '#fff', fontWeight: '700' }}>Add</Text>
                </TouchableOpacity>
              </View>
            );
          }}
        />

        <Text style={{ textAlign: 'center', fontSize: 16, fontWeight: '700' }}>
          Order: {totalItems} items · {totalPrice} TL
        </Text>
        <TouchableOpacity onPress={() => setOrder({})} style={{ alignItems: 'center' }}>
          <Text style={{ color: '#ef4444' }}>Clear order</Text>
        </TouchableOpacity>
      </View>
    </SafeAreaView>
  );
}
```
> S4 (adet) + Picker dosyasındaki "Tümü" filtresinin birleşimi. Toplam fiyat **filtrelenmiş** listeden değil **tüm** menüden hesaplanıyor, yoksa kategori değişince toplam yanlış görünür.

---

## ✅ Teslim öncesi kontrol listesi
- [ ] Başlık soruda istenen yazıyla **birebir** aynı mı? ("City Picker")
- [ ] Verilen veri değişkeni (`citiess`) kullanıldı mı, kendin yeni bir dizi yazmadın mı?
- [ ] Seç → tekrar bas → seçim kalkıyor mu?
- [ ] Renkler soruda verilen hex kodlarıyla aynı mı?
- [ ] Resim 48×48 ve yuvarlak mı (`borderRadius: 24`)? Resim yazının **solunda** mı?
- [ ] Sayaç formatı birebir aynı mı? (`Selected cities: X / Y`, boşluklar dahil)
- [ ] Liste uzunsa kayıyor ve sayaç altta görünüyor mu? (FlatList `flex: 1`)
- [ ] Sarı/kırmızı uyarı veya hata yok mu? (key, import)
- [ ] Dosya adı `App.js`, doğru klasörde mi?
