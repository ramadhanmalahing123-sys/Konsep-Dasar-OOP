    import random
    import matplotlib.pyplot as plt

# =========================================================
# DATA (SUDAH FIX)
  berfungsi untuk mendefinisikan parameter utama dalam sebuah simulasi optimasi
# =========================================================
    subjects = ["AI", "DB", "WEB", "ML", "IOT"]

    times = ["Pagi", "Siang", "Sore", "Malam", "Dini"]

            POP_SIZE = 30
            GENERATIONS = 100
            MUTATION_RATE = 0.2

# =========================================================
# INIT
  create_schedule adalah cara membuat satu solusi, 
  sedangkan init_population adalah cara membuat sekelompok solusi sebagai modal awal algoritma untuk mencari jadwal yang paling sempurna.
# =========================================================
    def create_schedule():
        return [random.choice(times) for _ in subjects]

    def init_population():
      return [create_schedule() for _ in range(POP_SIZE)]

# =========================================================
# HITUNG KONFLIK
  Fungsi count_conflicts(schedule) bertugas sebagai detektor kesalahan dalam jadwal yang dibuat.
  fungsi ini menghitung berapa banyak mata kuliah yang bertabrakan karena ditempatkan pada waktu yang sama.
# =========================================================
  def count_conflicts(schedule):
      used = set()
      conflicts = 0

      for t in schedule:
          if t in used:
              conflicts += 1
          else:
              used.add(t)

      return conflicts

# =========================================================
# FITNESS
  conflicts = count_conflicts(schedule): Fungsi ini pertama-tama memanggil count_conflicts untuk mengetahui berapa banyak bentrokan waktu yang terjadi dalam satu jadwal.
  100 - conflicts * 20: Ini adalah rumus pemberian skor.
# =========================================================
    def fitness(schedule):
        conflicts = count_conflicts(schedule)
        return max(100 - conflicts * 20, 0)

# =========================================================
# SELECTION
  Fungsi selection(pop) ini menggunakan metode yang dikenal sebagai Tournament Selection.
# =========================================================
      def selection(pop):
         return max(random.sample(pop, 3), key=fitness)

# =========================================================
# CROSSOVER
  Fungsinya adalah untuk mengombinasikan dua jadwal "orang tua" (p1 dan p2) guna menghasilkan satu jadwal "anak" yang mewarisi sifat dari keduanya.
# =========================================================
      def crossover(p1, p2):
          point = random.randint(1, len(p1)-1)
          return p1[:point] + p2[point:]

# =========================================================
# MUTATION
  Fungsi mutate(schedule) adalah mekanisme perubahan acak dalam algoritma genetika yang 
  bertujuan untuk menjaga keberagaman solusi. Jika crossover menggabungkan sifat yang sudah ada, 
  mutate memperkenalkan sifat-sifat baru yang mungkin belum pernah ada sebelumnya.
# =========================================================
      def mutate(schedule):
          for i in range(len(schedule)):
              if random.random() < MUTATION_RATE:
                  schedule[i] = random.choice(times)
          return schedule

# =========================================================
# DECODE
  fungsi decode(schedule) adalah tahap akhir yang berfungsi untuk mengubah 
  format data mentah komputer menjadi informasi yang mudah dipahami oleh manusia.
# =========================================================
      def decode(schedule):
          return list(zip(subjects, schedule))

# =========================================================
# GA
  Fungsi GA() adalah mesin utama yang menjalankan simulasi seleksi alam dengan menginisialisasi populasi jadwal acak, 
  lalu secara berulang mengurutkan, menyeleksi, mengawinkan (crossover), 
  dan memutasi individu terbaik hingga ditemukan jadwal belajar yang sempurna tanpa konflik.
# =========================================================
  def GA():
      pop = init_population()
      best_hist = []

      print("\n===== OPTIMASI JADWAL BELAJAR =====\n")

      for gen in range(GENERATIONS):

          pop = sorted(pop, key=fitness, reverse=True)

          best = pop[0]
          best_fit = fitness(best)

          best_hist.append(best_fit)

          if gen % 5 == 0:
              print(f"Gen {gen:3d} | Fitness: {best_fit}")

          if best_fit == 100 and gen > 5:
              print("\n🎯 Jadwal optimal ditemukan!")
              break

          new_pop = pop[:2]

          while len(new_pop) < POP_SIZE:
              p1 = selection(pop)
              p2 = selection(pop)

              child = crossover(p1, p2)
              child = mutate(child)

              new_pop.append(child)

          pop = new_pop

# =====================================================
  # HASIL AKHIR
    berfungsi sebagai tahap penyajian hasil akhir setelah proses
    Tugas utamanya adalah mengambil solusi terbaik dan menampilkannya dalam format yang mudah dipahami manusia.
# =====================================================
          best = sorted(pop, key=fitness, reverse=True)[0]
          conflicts = count_conflicts(best)
    
          print("\n===== HASIL AKHIR =====")
          print("Fitness :", fitness(best))
    
          for subj, t in decode(best):
              print(f"{subj} → {t}")
    
         print("\nPenjelasan:")
         if conflicts == 0:
              print("Tidak ada bentrok → jadwal optimal.")
        else:
              print(f"Terdapat {conflicts} bentrok waktu.")
              if len(subjects) > len(times):
                  print("Penyebab: slot waktu tidak cukup.")

# =====================================================
  # VISUAL
    Potongan kode ini menggunakan pustaka Matplotlib (plt) untuk menyajikan data secara visual. 
    Fungsinya adalah untuk membuat grafik garis
# =====================================================
          plt.figure()
          plt.plot(best_hist)
          plt.title("Perkembangan Fitness")
          plt.xlabel("Generasi")
          plt.ylabel("Fitness")
          plt.grid()
          plt.show()

# RUN
GA()
