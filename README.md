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

### **SOLVE**
#### Proses Pelanggan
<pre>
def process_customer(env, lengths_queue, customer, hot_food, sandwich, drink, cashier):
    if customer.route == 1:
        yield env.process(cs(env, lengths_queue, customer, 'hot-food', hot_food))
        yield env.process(cs(env, lengths_queue, customer, 'drink', drink))
        yield env.process(cs(env, lengths_queue, customer, 'cashier', cashier))
    elif customer.route == 2:
        yield env.process(cs(env, lengths_queue, customer, 'sandwich', sandwich))
        yield env.process(cs(env, lengths_queue, customer, 'drink', drink))
        yield env.process(cs(env, lengths_queue, customer, 'cashier', cashier))
    elif customer.route == 3:
        yield env.process(cs(env, lengths_queue, customer, 'drink', drink))
        yield env.process(cs(env, lengths_queue, customer, 'cashier', cashier))
</pre>

#### **Pemilihan rute yang dipilih oleh pelanggan**
<pre>
def cs(env, lengths_queue, customer, station_name, station):
    ## FOR DRINK ##
    if station_name == 'drink':
        print(f'Pelanggan {customer.customer_id} dari group size {customer.group_size} DILAYANI di stasiun {station_name} pada waktu {env.now:.2f}')

        yield env.process(station.service(customer))
        print(f'Pelanggan {customer.customer_id} dari group size {customer.group_size} MENINGGALKAN {station_name} pada waktu {env.now:.2f}')
        return

    print(f"Pelanggan {customer.customer_id} dari group size {customer.group_size} Mulai MENGANTRI di Station {station_name} pada {env.now:.2f}.")

    ## FOR CASHIER ##
    if station_name == 'cashier':
        index_shortest_queue = station.find_shortest_queue()
        with station.queues[index_shortest_queue].request() as request:
            customer.cashier_enter_time = env.now

            if len(station.queues[index_shortest_queue].queue) not in lengths_queue["cashiers"]:
                lengths_queue["cashiers"][len(station.queues[index_shortest_queue].queue)
                                          ] = customer.cashier_enter_time - station.queue_change_time
            else:
                lengths_queue["cashiers"][len(station.queues[index_shortest_queue].queue)
                                          ] += customer.cashier_enter_time - station.queue_change_time

            # Waktu perubahan customer masuk antrian
            station.queue_change_time = env.now
            yield request
            print(f'Pelanggan {customer.customer_id} dari group size {customer.group_size} DILAYANI di {station_name} pada waktu {env.now:.2f}')

            yield env.process(station.service(customer, lengths_queue, index_shortest_queue))
            print(f'Pelanggan {customer.customer_id} dari group size {customer.group_size} MENINGGALKAN {station_name} pada waktu {env.now:.2f}')
            return

     ## FOR HOT FOOD & SANDWICH ##
    with station.queue.request() as request:

        # SET STATION ENTER QUEUE TIME
        if station_name == 'hot-food':
            customer.hot_food_enter_time = env.now
            print(f"MASUK ANTRIAN HOT-FOOD: {len(station.queue.queue)}")

            if len(station.queue.queue) not in lengths_queue["hot-food"]:
                lengths_queue["hot-food"][len(station.queue.queue)
                                          ] = customer.hot_food_enter_time - station.queue_change_time
            else:
                lengths_queue["hot-food"][len(station.queue.queue)
                                          ] += customer.hot_food_enter_time - station.queue_change_time

        elif station_name == 'sandwich':
            customer.specialty_sandwich_enter_time = env.now

            if len(station.queue.queue) not in lengths_queue["sandwich"]:
                lengths_queue["sandwich"][len(station.queue.queue)
                                          ] = customer.specialty_sandwich_enter_time - station.queue_change_time
            else:
                lengths_queue["sandwich"][len(station.queue.queue)
                                          ] += customer.specialty_sandwich_enter_time - station.queue_change_time

        # Waktu perubahan customer masuk antrian
        station.queue_change_time = env.now

        yield request
        # req aja kaya membuka gitu
        print(f'Pelanggan {customer.customer_id} dari group size {customer.group_size} DILAYANI di stasiun {station_name} pada waktu {env.now:.2f}')

        yield env.process(station.service(customer, lengths_queue))

        print(f'Pelanggan {customer.customer_id} dari group size {customer.group_size} MENINGGALKAN {station_name} pada waktu {env.now:.2f}')
</pre>

</p>


