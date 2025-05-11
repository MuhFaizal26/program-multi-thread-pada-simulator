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

### 1. Tanpa Koherensi

```python
{'cache_hit': 16, 'cache_miss': 0, 'invalidation': 0}
```

* **Banyak cache hit (16)** menunjukkan performa tinggi karena data dibaca langsung dari cache lokal.
* **Tidak ada invalidasi**, yang artinya setiap thread menyimpan versinya sendiri dari data.
* **Potensi besar inkonsistensi nilai** antar thread.

### 2. Dengan Koherensi (Write-Invalidate)

```python
{'cache_hit': 1, 'cache_miss': 19, 'invalidation': 20}
```

* **Cache hit sangat rendah (1)** karena protokol invalidasi menyebabkan cache tidak bisa digunakan berulang.
* **Cache miss meningkat drastis (19)** akibat cache yang terus-menerus di-invalidasi.
* **Invalidasi tinggi (20)** mencerminkan upaya menjaga konsistensi data global.

---

## 🔍 Interpretasi Hasil
### Tanpa Koherensi:

* Menghasilkan performa optimal karena semua thread bebas menggunakan cache masing-masing.
* Namun, tidak menjamin bahwa data yang dibaca thread adalah nilai terbaru.
* Rentan terhadap race condition dan inkonsistensi nilai jika terjadi update bersamaan.

### Dengan Koherensi:

* Menjamin setiap thread membaca nilai terbaru dengan protokol write-invalidate.
* Mengorbankan kecepatan karena sering terjadi invalidasi dan cache miss.
* Sangat cocok untuk sistem yang membutuhkan **integritas data**, seperti sistem operasi, jaringan sinkron, atau simulasi ilmiah yang presisi.
---
## ⚖️ Perbandingan Kinerja

| Aspek                | Tanpa Koherensi  | Dengan Koherensi |
| -------------------- | ---------------- | ---------------- |
| **Cache Hit**        | ✅ 16             | ⚠️ 1             |
| **Cache Miss**       | 🟢 0             | 🔴 19            |
| **Invalidasi**       | 🚫 0             | 🔁 20            |
| **Konsistensi Data** | ❌ Tidak terjamin | ✅ Terjamin       |
| **Performa Akses**   | 🚀 Sangat cepat  | 🐢 Lebih lambat  |

## 📌 Kesimpulan

Pemilihan penggunaan protokol koherensi tergantung pada kebutuhan sistem:

* Jika sistem mengutamakan **performa dan throughput**, serta toleran terhadap inkonsistensi, maka **tanpa koherensi** dapat digunakan.
* Jika sistem menuntut **akurasi dan konsistensi nilai**, maka penggunaan **protokol koherensi cache seperti Write-Invalidate** sangat direkomendasikan, meski dengan biaya performa.

Simulasi ini menunjukkan trade-off nyata antara **kecepatan** dan **keandalan data** dalam sistem multithread modern.






