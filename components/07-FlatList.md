# 07 – FlatList

`FlatList`, uzun veya değişen listeleri gösteren component'tir. ScrollView'dan farkı: **sadece ekranda görünen elemanları çizer.** Kaydırdıkça yenilerini çizer, görünmeyenleri bellekten atar. Bu yüzden binlerce elemanlı listelerde bile akıcı çalışır.

> **ScrollView vs FlatList (exam question):**
>
> | | ScrollView | FlatList |
> |---|---|---|
> | **Rendering** | Renders **all** children at once | Renders only the **visible** items (virtualization / lazy rendering) |
> | **Performance** | Slows down with many items (high memory usage) | Stays smooth even with thousands of items |
> | **Best for** | Small, fixed content: forms, menus, a few cards | Long or dynamic data: feeds, product lists, chats |
> | **How items are created** | You create them manually with `map()` | You pass `data` + `renderItem`, FlatList creates them |
> | **Keys** | `key` prop inside `map()` | `keyExtractor` prop |
> | **Built-in features** | None | `ListHeaderComponent`, `ListFooterComponent`, `ListEmptyComponent`, `ItemSeparatorComponent`, `numColumns`, `onEndReached`, pull-to-refresh |
>
> **Short answer:** *ScrollView renders all of its children at once, so it is suitable for small amounts of content. FlatList uses virtualization and renders only the items currently visible on the screen, which makes it more efficient for long or dynamic lists.*

---

## 📋 FlatList hızlı tablo

**Zorunlu üçlü:**
```jsx
<FlatList
  data={DIZI}                                  // 1) gösterilecek dizi
  keyExtractor={(item) => item.id}             // 2) her elemanın benzersiz key'i (string olmalı)
  renderItem={({ item }) => <Text>{item.title}</Text>}   // 3) her eleman nasıl görünecek
/>
```
> `renderItem`'a gelen nesnenin içinde `item` (eleman) ve `index` (sırası: 0, 1, 2...) vardır. Bu yüzden `({ item })` veya `({ item, index })` şeklinde süslü parantezle açılır.

| Prop | Ne yapar |
|---|---|
| `ListHeaderComponent` | Listenin **en üstüne** bir şey koyar (başlık, arama kutusu) |
| `ListFooterComponent` | Listenin **en altına** bir şey koyar (toplam, yükleniyor çarkı) |
| `ListEmptyComponent` | Dizi **boşsa** gösterilir ("Sonuç yok") |
| `ItemSeparatorComponent` | Elemanların **arasına** bir şey koyar (boşluk, çizgi) |
| `numColumns` | Kaç sütun (grid görünüm): `numColumns={2}` |
| `columnWrapperStyle` | Grid'de her satırın stili: `columnWrapperStyle={{ gap: 12 }}` |
| `horizontal` | Yatay liste |
| `refreshControl` / `refreshing` + `onRefresh` | Aşağı çekip yenileme |
| `onEndReached` + `onEndReachedThreshold` | Sona yaklaşınca çalışır (sonsuz kaydırma) |

---

## 📦 Temel Kod 1 – Tamamlanma Durumlu Ders Modülleri (Switch)

```jsx
import { useState } from 'react';
import { FlatList, SafeAreaView, Switch, Text, View } from 'react-native';

// Modül listesi: her birinin benzersiz id'si var (component DIŞINDA)
const MODULES = [
  { id: '1', title: 'Introduction & Setup', duration: 12 },
  { id: '2', title: 'Core Components', duration: 18 },
  { id: '3', title: 'State & Props', duration: 22 },
  { id: '4', title: 'Navigation', duration: 15 },
];

export default function CourseModules() {
  // Hangi modüller tamamlandı? NESNE olarak tutulur: { '1': true, '3': false } gibi
  // Başta boş nesne {} → hiçbiri tamamlanmadı
  const [completed, setCompleted] = useState({});

  // Switch'e basılınca: o id'nin değerini güncelle, diğerlerini koru
  const toggleModule = (id, value) => {
    setCompleted((prev) => ({ ...prev, [id]: value }));   // ...prev → eskileri kopyala, [id]: value → bu id'yi yaz/üzerine yaz
  };

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#f8fafc' }}>
      <View style={{ padding: 24, gap: 16, flex: 1 }}>
        {/* Başlık */}
        <Text style={{ fontSize: 22, fontWeight: '700', color: '#0f172a' }}>
          Course Modules
        </Text>

        <FlatList
          data={MODULES}                                                  // gösterilecek dizi
          keyExtractor={(item) => item.id}                                // key = id
          ItemSeparatorComponent={() => <View style={{ height: 12 }} />}  // kartlar arası 12 boşluk
          contentContainerStyle={{ paddingBottom: 24 }}
          // HER MODÜL KARTI
          renderItem={({ item }) => {
            const done = completed[item.id];                              // bu modül tamamlandı mı? (true / false / undefined)
            return (
              <View
                style={{
                  padding: 18,
                  borderRadius: 16,
                  borderWidth: 1,
                  borderColor: done ? '#22c55e' : '#e2e8f0',              // tamamlandıysa yeşil kenarlık
                  backgroundColor: done ? '#dcfce7' : '#fff',             // tamamlandıysa açık yeşil zemin
                  flexDirection: 'row',                                   // yazılar solda, switch sağda
                  alignItems: 'center',
                  justifyContent: 'space-between',
                }}
              >
                {/* Sol taraf: başlık + süre alt alta */}
                <View style={{ gap: 4 }}>
                  <Text style={{ fontSize: 16, fontWeight: '600' }}>
                    {item.title}
                  </Text>
                  <Text style={{ color: '#475569' }}>
                    Duration: {item.duration} min
                  </Text>
                </View>
                {/* Sağ taraf: aç/kapa anahtarı */}
                <Switch
                  value={done}                                            // açık mı kapalı mı
                  onValueChange={(value) => toggleModule(item.id, value)} // değişince yeni değeri (true/false) kaydet
                />
              </View>
            );
          }}
          // LİSTENİN ALTI: kaç tanesi tamamlandı
          ListFooterComponent={() => {
            const total = MODULES.length;                                           // toplam modül
            const doneCount = Object.values(completed).filter(Boolean).length;      // true olanları say
            return (
              <Text style={{ marginTop: 16, textAlign: 'center', color: '#1f2937' }}>
                Completed: {doneCount} / {total}
              </Text>
            );
          }}
        />
      </View>
    </SafeAreaView>
  );
}
```

> **`completed` neden dizi değil de nesne?** Her modülün durumunu id'siyle eşleştirmek için. `completed['3']` diyerek 3 numaralı modülün durumuna doğrudan ulaşılır.
> **`{ ...prev, [id]: value }`:** önce eski nesnenin tüm alanları kopyalanır (`...prev`), sonra sadece bu id'nin değeri yazılır. `[id]` köşeli parantez içinde olduğu için anahtarın adı **değişkenin değeri** olur ('3' gibi), "id" kelimesi değil.
> **`Object.values(completed).filter(Boolean).length`:** nesnenin değerlerini dizi yapar (`[true, false, true]`), sadece `true` olanları bırakır (`[true, true]`), sayar (`2`).

### Olası sorular – Kod 1

**S1. Yeni modül ekle**
```jsx
{ id: '5', title: 'Animations', duration: 20 },   // 👈 MODULES dizisinin sonuna (id benzersiz, string)
```
> Footer'daki toplam sayı (`MODULES.length`) kendiliğinden 5 olur.

**S2. Tamamlanınca yeşil yerine mavi olsun**
```jsx
borderColor: done ? '#3b82f6' : '#e2e8f0',      // 👈
backgroundColor: done ? '#dbeafe' : '#fff',     // 👈
```

**S3. Yazıları Türkçe yap**
```jsx
Ders Modülleri                                   {/* 👈 başlık */}
Süre: {item.duration} dk                         {/* 👈 süre satırı */}
Tamamlanan: {doneCount} / {total}                {/* 👈 footer */}
```

**S4. Switch'in renklerini değiştir**
```jsx
<Switch
  value={done}
  onValueChange={(value) => toggleModule(item.id, value)}
  trackColor={{ false: '#e2e8f0', true: '#a855f7' }}   // 👈 YENİ: kapalıyken gri, açıkken mor zemin
  thumbColor={done ? '#fff' : '#94a3b8'}               // 👈 YENİ: yuvarlak düğmenin rengi
/>
```

**S5. Tamamlanan modülün başlığının üstü çizilsin**
```jsx
<Text
  style={{
    fontSize: 16,
    fontWeight: '600',
    textDecorationLine: done ? 'line-through' : 'none',   // 👈 tamamlandıysa üstü çizili
  }}
>
```

**S6. Hepsi tamamlanınca tebrik mesajı çıksın**
```jsx
ListFooterComponent={() => {
  const total = MODULES.length;
  const doneCount = Object.values(completed).filter(Boolean).length;
  return (
    <View style={{ marginTop: 16, gap: 8 }}>                          {/* 👈 iki Text için View ile sar */}
      <Text style={{ textAlign: 'center', color: '#1f2937' }}>
        Completed: {doneCount} / {total}
      </Text>
      {doneCount === total && (                                        // 👈 hepsi bittiyse
        <Text style={{ textAlign: 'center', color: '#16a34a', fontWeight: '700' }}>
          Tebrikler, tüm modülleri bitirdin! 🎉
        </Text>
      )}
    </View>
  );
}}
```

**S7. Footer'da toplam süreyi göster (ör. "Toplam: 67 dk")**
```jsx
// ListFooterComponent İÇİNDE, return'den ÖNCE:
const totalMinutes = MODULES.reduce((sum, m) => sum + m.duration, 0);   // 👈 0'dan başla, her modülün süresini ekle

// Text içinde:
Toplam süre: {totalMinutes} dk
```
> **`reduce` mantığı:** diziyi baştan sona gezer ve bir değer biriktirir. `sum` biriken toplam (başta `0`), `m` sıradaki modül. Her adımda `sum + m.duration` yeni toplam olur: 0 → 12 → 30 → 52 → 67.

**S8 (zor). Sadece TAMAMLANAN modüllerin süresini topla**
```jsx
const doneMinutes = MODULES
  .filter((m) => completed[m.id])                  // 👈 önce sadece tamamlananları al
  .reduce((sum, m) => sum + m.duration, 0);        // 👈 sonra onların sürelerini topla

Tamamlanan süre: {doneMinutes} dk
```

**S9. Her modülün başına sıra numarası koy (1., 2., ...)**
```jsx
renderItem={({ item, index }) => {                 // 👈 index eklendi (0'dan başlar)
  ...
  <Text style={{ fontSize: 16, fontWeight: '600' }}>
    {index + 1}. {item.title}                      {/* 👈 0 → 1, 1 → 2 ... */}
  </Text>
```

**S10. "Tümünü sıfırla" butonu**
```jsx
import { Button, FlatList, SafeAreaView, Switch, Text, View } from 'react-native';   // 👈 Button

{/* Başlık Text'inin ALTINA */}
<Button title="Tümünü sıfırla" color="#ef4444" onPress={() => setCompleted({})} />   // 👈 boş nesne = hiçbiri tamamlanmadı
```

**S11. Switch yerine kartın kendisine dokununca tamamlansın**
```jsx
import { FlatList, SafeAreaView, Text, TouchableOpacity, View } from 'react-native';   // 👈 Switch yerine TouchableOpacity

renderItem={({ item }) => {
  const done = completed[item.id];
  return (
    <TouchableOpacity                                              // 👈 dış View → TouchableOpacity
      onPress={() => toggleModule(item.id, !done)}                 // 👈 !done → tersine çevir
      style={{ /* aynı stil */ }}
    >
      <View style={{ gap: 4 }}>
        <Text style={{ fontSize: 16, fontWeight: '600' }}>{item.title}</Text>
        <Text style={{ color: '#475569' }}>Duration: {item.duration} min</Text>
      </View>
      <Text style={{ fontSize: 20 }}>{done ? '✅' : '⬜'}</Text>     {/* 👈 Switch yerine işaret */}
    </TouchableOpacity>                                            // 👈 kapanış da değişti
  );
}}
```

**S12. İlerleme çubuğu ekle (View dosyasındaki çubuk)**
```jsx
// ListFooterComponent İÇİNDE, doneCount'un ALTINA:
const percent = Math.round((doneCount / total) * 100);

// Footer'ın return'ünde Text'in ÜSTÜNE:
<View style={{ height: 8, borderRadius: 8, backgroundColor: '#e2e8f0', marginTop: 16 }}>
  <View style={{ width: `${percent}%`, height: '100%', borderRadius: 8, backgroundColor: '#22c55e' }} />
</View>
```
> Footer artık iki eleman döndürdüğü için S6'daki gibi bir `<View>` ile sarılması gerekir.

**S13. İki sütunlu (grid) görünüm**
```jsx
<FlatList
  data={MODULES}
  numColumns={2}                                    // 👈 2 sütun
  columnWrapperStyle={{ gap: 12 }}                  // 👈 yan yana kartlar arası boşluk
  ...
  renderItem={({ item }) => {
    const done = completed[item.id];
    return (
      <View
        style={{
          flex: 1,                                  // 👈 her kart satırın yarısını alsın
          flexDirection: 'column',                  // 👈 dar kartta row yerine alt alta
          gap: 8,                                   // 👈
          padding: 18,
          borderRadius: 16,
          borderWidth: 1,
          borderColor: done ? '#22c55e' : '#e2e8f0',
          backgroundColor: done ? '#dcfce7' : '#fff',
        }}
      >
```
> Snack açıkken `numColumns`'u değiştirirsen "Changing numColumns on the fly is not supported" hatası çıkabilir. Sayfayı yenilemek (Snack'te kaydet / yeniden yükle) çözer.

---

## 📦 Temel Kod 2 – Sonsuz Kaydırma + Aşağı Çekip Yenileme

> Bu kod daha ileri seviye (async, useCallback). Sınavda büyük ihtimalle **basit değişiklikler** (sayılar, yazılar, renkler) sorulur. Önce akışı anla:
> 1. Sayfa açılınca `useEffect` → `loadPage(1, true)` → ilk 6 not gelir.
> 2. Listenin sonuna yaklaşınca `onEndReached` → `loadPage(page)` → sonraki 6 not **eklenir**.
> 3. Listeyi aşağı çekince `onRefresh` → `loadPage(1, true)` → liste **sıfırlanıp** ilk 6 not gelir.

```jsx
import { useCallback, useEffect, useState } from 'react';
import {
  ActivityIndicator,
  FlatList,
  RefreshControl,
  SafeAreaView,
  Text,
  View,
} from 'react-native';

// SAHTE API: internetten veri çekiyormuş gibi davranır (component DIŞINDA)
// async → içinde "bekle" (await) kullanılabilen fonksiyon
const fetchPage = async (page) => {
  await new Promise((resolve) => setTimeout(resolve, 600));     // 600 ms bekle (yükleniyor süsü)
  // 6 elemanlı bir dizi üret: sayfa 1 → id 1-6, sayfa 2 → id 7-12 ...
  return Array.from({ length: 6 }, (_, index) => {
    const id = (page - 1) * 6 + index + 1;
    return { id: String(id), title: `Lesson Note #${id}` };
  });
};

export default function InfiniteNotes() {
  const [notes, setNotes] = useState([]);                // ekrandaki notlar (başta boş)
  const [page, setPage] = useState(1);                   // sıradaki yüklenecek sayfa
  const [loadingMore, setLoadingMore] = useState(false); // alttan yeni sayfa yükleniyor mu?
  const [refreshing, setRefreshing] = useState(false);   // yukarıdan yenileniyor mu?

  // Bir sayfayı yükleyen fonksiyon. isRefresh true ise listeyi SIFIRLAR, false ise sonuna EKLER
  // useCallback → fonksiyonu hafızada tutar, bağımlılıklar değişmedikçe yeniden oluşturmaz
  const loadPage = useCallback(
    async (pageToLoad, isRefresh = false) => {
      if (loadingMore || refreshing) return;             // zaten yükleniyorsa ikinci kez başlatma
      if (isRefresh) {
        setRefreshing(true);                             // üstteki yenileme çarkı
      } else {
        setLoadingMore(true);                            // alttaki yükleniyor çarkı
      }
      const newNotes = await fetchPage(pageToLoad);      // veriyi BEKLE
      setNotes((prev) => (isRefresh ? newNotes : [...prev, ...newNotes]));   // yenileme: sadece yeniler / değilse: eskiler + yeniler
      setPage(pageToLoad + 1);                           // bir sonraki sayfa numarası
      setLoadingMore(false);
      setRefreshing(false);
    },
    [loadingMore, refreshing]
  );

  // Sayfa açılınca ilk sayfayı yükle
  useEffect(() => {
    loadPage(1, true);
  }, [loadPage]);

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: '#0f172a' }}>
      <FlatList
        data={notes}
        keyExtractor={(item) => item.id}
        contentContainerStyle={{ padding: 24, gap: 16 }}
        // Her not kartı
        renderItem={({ item }) => (
          <View
            style={{
              padding: 18,
              borderRadius: 14,
              backgroundColor: '#1f2937',
            }}
          >
            <Text style={{ fontSize: 16, color: '#f8fafc' }}>{item.title}</Text>
          </View>
        )}
        // Aşağı çekip yenileme
        refreshControl={
          <RefreshControl
            refreshing={refreshing}                      // çark dönsün mü
            onRefresh={() => loadPage(1, true)}          // çekince 1. sayfadan baştan yükle
            tintColor="#38bdf8"                          // çarkın rengi (iOS)
          />
        }
        onEndReached={() => loadPage(page)}              // sona yaklaşınca sonraki sayfayı yükle
        onEndReachedThreshold={0.4}                      // sona ne kadar kala? (0.4 = görünen yüksekliğin %40'ı)
        // Listenin altı: yükleniyorsa çark, değilse hiçbir şey
        ListFooterComponent={
          loadingMore ? (
            <ActivityIndicator size="small" color="#38bdf8" />
          ) : null
        }
        // Listenin üstü: başlık
        ListHeaderComponent={() => (
          <Text
            style={{
              fontSize: 22,
              fontWeight: '700',
              color: '#f8fafc',
              marginBottom: 8,
            }}
          >
            Lesson Notes Feed
          </Text>
        )}
      />
    </SafeAreaView>
  );
}
```

> **`async` / `await`:** `await` "bu işlem bitene kadar bekle, sonra devam et" demek. Gerçek bir uygulamada burada internetten veri çekilir (`fetch`). Burada `setTimeout` ile 600 ms bekleyerek internet gecikmesi taklit ediliyor.
> **`Array.from({ length: 6 }, (_, index) => ...)`:** 6 elemanlı yeni bir dizi oluşturur. Her eleman için fonksiyon çalışır, `index` 0'dan 5'e gider.
> **`isRefresh ? newNotes : [...prev, ...newNotes]`:** yenilemede eski liste atılır. Sonsuz kaydırmada eski liste korunur, yeniler sonuna eklenir.

### Olası sorular – Kod 2

**S1. Her sayfada 6 yerine 10 not gelsin**
```jsx
// ⚠️ 6 sayısı fetchPage içinde İKİ yerde geçiyor, ikisi de değişmeli:
return Array.from({ length: 10 }, (_, index) => {         // 👈 1) kaç tane üretilecek
  const id = (page - 1) * 10 + index + 1;                 // 👈 2) id hesabı (yoksa id'ler çakışır → key hatası)
```
> Daha temiz yol: en üste `const PAGE_SIZE = 10;` yaz, iki yerde de `PAGE_SIZE` kullan.

**S2. Not başlıklarını Türkçe yap**
```jsx
return { id: String(id), title: `Ders Notu #${id}` };     // 👈 fetchPage içinde
```

**S3. Yükleme süresini uzat (çarkı daha uzun görmek için)**
```jsx
await new Promise((resolve) => setTimeout(resolve, 1500));   // 👈 600 → 1500 ms
```

**S4. Kart rengini, çark rengini ve başlığı değiştir**
```jsx
backgroundColor: '#1e3a8a',                                  // 👈 renderItem'daki kart
<ActivityIndicator size="large" color="#f97316" />           // 👈 footer'daki çark: büyük, turuncu
Ders Notları Akışı                                           {/* 👈 ListHeaderComponent içindeki yazı */}
```

**S5. Başlıkta kaç not yüklendiğini göster**
```jsx
ListHeaderComponent={() => (
  <Text style={{ fontSize: 22, fontWeight: '700', color: '#f8fafc', marginBottom: 8 }}>
    Lesson Notes Feed ({notes.length})       {/* 👈 dizinin uzunluğu */}
  </Text>
)}
```

**S6 (zor). En fazla 5 sayfa (30 not) yüklensin, sonunda "Tüm notlar yüklendi" yazsın**
```jsx
const MAX_PAGE = 5;                                       // 👈 component DIŞINA

// loadPage İÇİNDE, ilk if'in ALTINA:
if (pageToLoad > MAX_PAGE) return;                        // 👈 5. sayfadan sonrasını yükleme

// ListFooterComponent'in YERİNE:
ListFooterComponent={
  loadingMore ? (
    <ActivityIndicator size="small" color="#38bdf8" />
  ) : page > MAX_PAGE ? (                                 // 👈 hepsi yüklendiyse
    <Text style={{ color: '#94a3b8', textAlign: 'center' }}>Tüm notlar yüklendi</Text>
  ) : null
}
```

**S7. Her karta alt bilgi ekle (hangi sayfadan geldiği)**
```jsx
return { id: String(id), title: `Lesson Note #${id}`, page: page };     // 👈 fetchPage'de yeni alan

// renderItem'da başlık Text'inin ALTINA (kart View'ının İÇİNE):
<Text style={{ color: '#94a3b8', marginTop: 4 }}>Sayfa {item.page}</Text>
```

**S8. Nota tıklayınca başlığını uyarıda göster**
```jsx
import { ActivityIndicator, Alert, FlatList, RefreshControl, SafeAreaView, Text, TouchableOpacity, View } from 'react-native';   // 👈

renderItem={({ item }) => (
  <TouchableOpacity                                       // 👈 View → TouchableOpacity
    onPress={() => Alert.alert('Not', item.title)}        // 👈
    style={{ padding: 18, borderRadius: 14, backgroundColor: '#1f2937' }}
  >
    <Text style={{ fontSize: 16, color: '#f8fafc' }}>{item.title}</Text>
  </TouchableOpacity>                                     // 👈 kapanış da değişti
)}
```

**S9. Sona daha erken yaklaşınca yüklensin**
```jsx
onEndReachedThreshold={1}     // 👈 0.4 → 1 (bir ekran boyu kala yüklemeye başlar)
```

---

## ⚠️ Sık hatalar
- `renderItem={(item) => ...}` yazmak → yanlış. Süslü parantezle açılır: `renderItem={({ item }) => ...}`.
- `keyExtractor` sayı döndürürse uyarı verir → string olmalı: `String(item.id)` veya id'leri `'1'` şeklinde yaz.
- Aynı id'ye sahip iki eleman → "Encountered two children with the same key" uyarısı. Yeni eleman eklerken id'yi benzersiz yap.
- `renderItem` içinde birden fazla satır kod (ör. `const done = ...`) varsa: `=> ( ... )` yerine `=> { ...; return ( ... ); }` kullan.
- FlatList'i bir ScrollView'ın **içine** koymak → uyarı verir ve virtualization bozulur. FlatList zaten kendi kendine kayar.
- Dikey FlatList kaymıyorsa → ebeveyn View'da `flex: 1` var mı kontrol et (ScrollView ile aynı kural).
