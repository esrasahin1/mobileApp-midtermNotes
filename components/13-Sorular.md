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
