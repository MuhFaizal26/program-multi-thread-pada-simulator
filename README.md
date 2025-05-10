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

## 🧠 Arsitektur

