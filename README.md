### **STUDY CASE**
<p align="justify">
Kafetaria di Big State University ingin meningkatkan layanan saat jam makan siang (11:30 - 13:00). Pelanggan tiba dalam kelompok berukuran 1 - 4 dengan probabilitas masing-masing 0.5, 0.3, 0.1, dan 0.1. Waktu antar kedatangan mengikuti distribusi eksponensial dengan rata-rata 30 detik.
  
Setiap pelanggan memilih salah satu dari tiga rute berikut:
1. **Hot food → Drinks→ Cashiers** (80%)
2. **Specialty Sandwiches → Drinks → Cashiers** (15%)
3. **Drinks → Cashiers** (5%)
</p>

<p align="justify">

Minuman bersifat **self-service** tanpa antrean. Pelanggan di kasir memilih antrean terpendek dan tidak berpindah antrean (FIFO).
  
Kendala Operasional:
1. Kasir harus **minimal 2 dan maksimal 3 orang.**
2. Harus ada **minimal 1 pekerja di setiap stasiun makanan panas dan sandwich spesial.**
</p>
  
<p align="center">
<img width="476" height="491" alt="image" src="https://github.com/user-attachments/assets/f8f1bf4f-deab-49e2-b6d8-83be0239510a" />
The Ball State University (BSU) Cafetaria
</p>

### **SOLVE**
#### Seed untuk Setiap Streams
```# SEED FOR EVERY STREAMS
# random seed untuk waktu kedatangan (stream 1)
SEED_INTERVAL_TIME = 100
# random seed untuk ukuran grup (stream 2)
SEED_GROUP_SIZE = 200
# random seed untuk pemilihan rute (stream 3)
SEED_ROUTE_CHOICE = 300
# random seed untuk waktu pelayanan hotfood (stream 4)
SEED_ST_HOT_FOOD = 400
# random seed untuk waktu pelayanan sandwich (stream 5)
SEED_ST_SANDWICH = 500
# random seed untuk waktu pelayanan drinks (stream 6)
SEED_ST_DRINKS = 600
# random seed untuk akumulasi waktu kasir hotfood (stream 7)
SEED_ACT_HOT_FOOD = 700
# random seed untuk akumulasi waktu kasir sandwich (stream 8)
SEED_ACT_SANDWICH = 800
# random seed untuk akumulasi waktu kasir hotfood (stream 9)
SEED_ACT_DRINKS = 900```
