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

```
import threading
import time
import random
import matplotlib.pyplot as plt

# Shared memory yang diakses oleh semua thread
class SharedMemory:
    def __init__(self):
        self.memory = {"x": 0}
        self.lock = threading.Lock()

# Cache lokal milik setiap thread
class Cache:
    def __init__(self):
        self.cache_data = {}
        self.valid = {}

    def read(self, addr):
        return self.cache_data.get(addr, None)

    def write(self, addr, value):
        self.cache_data[addr] = value
        self.valid[addr] = True

    def invalidate(self, addr):
        self.valid[addr] = False

# Manajer cache untuk mengatur invalidasi lintas thread
class CacheManager:
    def __init__(self):
        self.caches = {}

    def register(self, thread_name, cache):
        self.caches[thread_name] = cache

    def invalidate_others(self, current_thread, addr):
        for name, cache in self.caches.items():
            if name != current_thread:
                cache.invalidate(addr)

# Worker thread yang melakukan read/write ke cache dan memory
class ThreadWorker(threading.Thread):
    def __init__(self, name, shared_mem, use_coherence, stats, cache_manager):
        super().__init__()
        self.name = name
        self.shared_mem = shared_mem
        self.cache = Cache()
        self.use_coherence = use_coherence
        self.stats = stats
        self.cache_manager = cache_manager
        self.cache_manager.register(name, self.cache)

    def run(self):
        for _ in range(10):  # jumlah operasi tiap thread
            op = random.choice(["read", "write"])
            if op == "read":
                self.read_memory("x")
            else:
                value = random.randint(1, 100)
                self.write_memory("x", value)
            time.sleep(random.uniform(0.01, 0.05))  # simulasi delay

    def read_memory(self, addr):
        value = self.cache.read(addr)
        if value is not None and self.cache.valid.get(addr, False):
            self.stats["cache_hit"] += 1
        else:
            self.stats["cache_miss"] += 1
            with self.shared_mem.lock:
                value = self.shared_mem.memory[addr]
            self.cache.write(addr, value)
        print(f"[{self.name}] READ {addr} = {value}")

    def write_memory(self, addr, value):
        with self.shared_mem.lock:
            self.shared_mem.memory[addr] = value
        self.cache.write(addr, value)
        if self.use_coherence:
            self.cache_manager.invalidate_others(self.name, addr)
            self.stats["invalidation"] += 1
        print(f"[{self.name}] WRITE {addr} = {value}")

# Fungsi simulasi
def simulate(use_coherence):
    shared = SharedMemory()
    stats = {"cache_hit": 0, "cache_miss": 0, "invalidation": 0}
    cache_manager = CacheManager()
    threads = [ThreadWorker(f"T{i}", shared, use_coherence, stats, cache_manager) for i in range(4)]

    for t in threads:
        t.start()
    for t in threads:
        t.join()

    return stats

# Menjalankan simulasi
print("Tanpa Koherensi:")
no_coherence_stats = simulate(use_coherence=False)
print(no_coherence_stats)

print("\nDengan Koherensi:")
with_coherence_stats = simulate(use_coherence=True)
print(with_coherence_stats)

# Visualisasi hasil
def plot_stats(no_coherence_stats, with_coherence_stats):
    labels = list(no_coherence_stats.keys())
    no_coherence_values = list(no_coherence_stats.values())
    with_coherence_values = list(with_coherence_stats.values())

    x = range(len(labels))
    width = 0.35

    fig, ax = plt.subplots()
    bar1 = ax.bar([i - width/2 for i in x], no_coherence_values, width, label='Tanpa Koherensi')
    bar2 = ax.bar([i + width/2 for i in x], with_coherence_values, width, label='Dengan Koherensi')

    ax.set_ylabel('Jumlah')
    ax.set_title('Perbandingan Performa Cache')
    ax.set_xticks(x)
    ax.set_xticklabels(labels)
    ax.legend()

    for bars in [bar1, bar2]:
        for bar in bars:
            height = bar.get_height()
            ax.annotate(f'{height}',
                        xy=(bar.get_x() + bar.get_width() / 2, height),
                        xytext=(0, 3),
                        textcoords="offset points",
                        ha='center', va='bottom')

    plt.tight_layout()
    plt.show()

# Menampilkan grafik
plot_stats(no_coherence_stats, with_coherence_stats)
```

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






