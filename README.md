# program-multi-thread-pada-simulator
# 🧵 Multi-Threaded Cache Simulator with Coherence Protocol

Simulasi sistem multi-thread dengan cache lokal dan protokol koherensi menggunakan Python.

## 📋 Deskripsi Proyek

Proyek ini mensimulasikan beberapa thread yang membaca dan menulis ke memori bersama. Setiap thread memiliki cache sendiri, dan kita membandingkan performa sistem:

- Tanpa protokol koherensi
- Dengan protokol **Write-Invalidate**

## ⚙️ Fitur

- Simulasi multi-thread dengan `threading.Thread`
- Implementasi cache lokal untuk tiap thread
- Protokol koherensi sederhana: **Write-Invalidate**
- Statistik performa: cache hit, cache miss, invalidasi
- Visualisasi hasil dengan `matplotlib`

## 📊 Grafik Perbandingan Performa Cache

![Perbandingan Performa Cache](./Screenshot%202025-05-10%20083043.png)

| Aspek        | Tanpa Koherensi | Dengan Koherensi | Catatan                                                                 |
|--------------|------------------|------------------|-------------------------------------------------------------------------|
| Cache Hit    | 🟦 16             | 🟧 4              | Hit tinggi tanpa koherensi karena data tidak pernah di-*invalidate*.    |
| Cache Miss   | 🟦 1              | 🟧 13             | Miss meningkat karena invalidasi membuat cache harus di-refresh.       |
| Invalidation | 🟦 0              | 🟧 23             | Invalidasi aktif menjaga konsistensi antar cache.                       |

---

## 🔍 Interpretasi Hasil

### Tanpa Koherensi
- ✅ **Cache hit tinggi (16)** → Performa cepat.
- ❌ **Tidak ada invalidasi** → Data antar-thread bisa tidak sinkron.
- ⚠️ Cocok untuk sistem *non-kritis* yang tidak butuh konsistensi sempurna.

### Dengan Koherensi
- ⚠️ **Cache miss meningkat** karena setiap penulisan meng-invalidate cache lain.
- ✅ **Data selalu konsisten** antar thread.
- Cocok untuk sistem *kritis* yang butuh akurasi tinggi.

---

## 🧾 Ringkasan

| Mode Simulasi        | Performa Cache              | Konsistensi Data      |
|----------------------|-----------------------------|------------------------|
| Tanpa Koherensi      | ✅ Cepat, efisien            | ❌ Tidak konsisten      |
| Dengan Koherensi     | ⚠️ Lebih lambat              | ✅ Konsisten dan akurat |





