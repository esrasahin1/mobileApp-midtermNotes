# mobileApp-midtermNotes
# 📱 Mobile App Development – Vize Notları

Hocanın W6 component örnekleri ([zeki-ozen/MobileAppDevelopment](https://github.com/zeki-ozen/MobileAppDevelopment/tree/main/W6)) + olası sınav soruları ve çözümleri.

**Kullanım:** Temel kodu Snack'te `App.js`'in tamamının yerine yapıştır, sonra sorudaki değişen satırları uygula. 👈 işaretli satırlar değişen yerlerdir.

## 📂 Components

| # | Component | Temel kodlar | Öne çıkan sorular |
|---|---|---|---|
| 01 | [View](components/01-View.md) | Dashboard kartı · Responsive grid | Flexbox hizalama, ilerleme çubuğu, butonla artırma |
| 02 | [Text](components/02-Text.md) | Zengin metin banner · Karakter sayaçlı bio | İç içe Text, numberOfLines, sayaç durdur/başlat |
| 03 | [Image](components/03-Image.md) | Haber kartı · Image playground | `uri` vs `require`, Snack'e görsel yükleme, resizeMode |
| 04 | [TextInput](components/04-TextInput.md) | E-posta doğrulama · Arama listesi | Şifre kuralları, kayıt formu, to-do listesi |
| 05 | [Button](components/05-Button.md) | Hesap makinesi · Geri sayım | Dört işlem, sabit sayıyla çarpma, sıfıra bölme |
| 06 | [ScrollView](components/06-ScrollView.md) | Sözleşme onayı · Yatay haftalık program | ⭐ Kartlara görsel ekleme, dikeye çevirme |
| 07 | [FlatList](components/07-FlatList.md) | Modül listesi (Switch) · Sonsuz kaydırma | Toplam süre (reduce), grid, tümünü sıfırla |
| 08 | [TouchableOpacity](components/08-TouchableOpacity.md) | Favori kartı · SSS kartı | İkonlar, çoklu kart favori, akordeon |
| 09 | [SafeAreaView](components/09-SafeAreaView.md) | Onboarding · Çentikli başlık | Geri/Atla butonu, ilerleme noktaları |
| 10 | [Picker](components/10-Picker.md) | Seviye seçici · Filtreli liste | label vs value, "Tümü" seçeneği |
| 11 | [RadioButton](components/11-RadioButton.md) | Kendi RadioItem'ımız · Kart radio | Props ile renk, ikinci grup |
| 12 | [Checkbox](components/12-Checkbox.md) | Görev listesi · Ayarlar formu | Koşulları kabul et, some vs every |

## 📦 Ayrı paket gerektirenler (Snack'te "Add dependency")
- Picker → `import { Picker } from '@react-native-picker/picker';`
- Checkbox → `import Checkbox from 'expo-checkbox';` (süslü parantez yok)
- SafeAreaView (context) → `import { SafeAreaView } from 'react-native-safe-area-context';`
- İkonlar → `import { Ionicons } from '@expo/vector-icons';` (Snack'te hazır)

## ⚠️ En sık hatalar
- Yeni component kullandıysan → en üstteki `import { ... } from 'react-native'` satırına ekle
- `uri` görselde `width` / `height` yoksa → görsel görünmez
- `Button`'da `style` yok → görünüm için `TouchableOpacity`
- Dikey ScrollView / FlatList kaymıyorsa → ebeveyn View'da `flex: 1`
- `View` → `TouchableOpacity` çevirirken kapanış etiketini de değiştir
- State'e bağlı hesaplama component'in **içinde**, sabitler component'in **dışında**
- Bir sayı / renk birden fazla yerde geçiyorsa **hepsini** değiştir (Ctrl+F ile ara)
