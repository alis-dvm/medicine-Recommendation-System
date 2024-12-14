# **Medicine Recommendation System**

## **Project Overview**
Dalam proyek ini, kami membangun sistem rekomendasi medis menggunakan pendekatan *Content-Based Filtering* dan *Collaborative Filtering* untuk membantu pengguna, seperti pasien, dokter dan apoteker, dalam memberikan rekomendasi obat berdasarkan fitur karakteristik obat yang relevan serta riwayat data pengguna. Sistem ini bertujuan untuk memberikan saran pengobatan yang lebih akurat dengan memperhitungkan data medis pasien dan preferensi obat.
1. Content-Based Filtering menggunakan dataset "Events" yang berisi informasi tentang efek samping dan kejadian medis yang terkait dengan obat-obatan. untuk memberikan rekomendasi berbasis karakteristik obat. Metode ini memberikan rekomendasi berbasis karakteristik obat yang sesuai dengan data pasien. Misalnya, sistem dapat menganalisis gejala pasien, durasi penyakit, dan parameter medis lainnya untuk menyarankan obat yang paling relevan [[1]](https://ieeexplore.ieee.org/document/10673613). Keunggulan metode ini adalah kemampuan memberikan rekomendasi yang dipersonalisasi sesuai dengan profil individu pasien, sehingga meningkatkan relevansi saran pengobatan.

2. Collaborative Filtering menggunakan dataset "Drug Rating" yang berisi penilaian obat oleh pengguna (pasien). Metode ini memberikan rekomendasi berdasarkan pola preferensi pengguna lain yang memiliki kondisi medis serupa. Sebagai contoh, jika pasien dengan gejala serupa telah berhasil menggunakan obat tertentu, sistem akan merekomendasikan obat tersebut kepada pasien baru yang memiliki profil serupa [[2]](https://sic.ici.ro/vol-32-no-3-2023/a-hybrid-recommendation-model-for-drug-selection/). Namun, CF dapat menghadapi tantangan seperti kelangkaan data (data sparsity) dan masalah item baru (new item problem), sehingga memerlukan pengoptimalan untuk meningkatkan akurasi [[2]](https://sic.ici.ro/vol-32-no-3-2023/a-hybrid-recommendation-model-for-drug-selection/).

## **Business Understanding**
**Problem Statements**<br>
Pengguna sering menghadapi tantangan dalam memilih obat yang tepat karena banyaknya pilihan obat yang ada, yang sesuai untuk kondisi medis mereka (mempertimbangkan riwayat medis). Oleh karena itu, penting untuk menciptakan sistem rekomendasi yang dapat membantu pengguna dalam memilih obat yang tepat dan relevan untuk menghindari efek samping berbahaya dan meningkatkan efektivitas pengobatan.

**Goals**<br>
1. Mengembangkan sistem rekomendasi medis yang dapat memberikan saran obat yang lebih tepat berdasarkan data medis pasien dan pengalaman pengguna lain.
2. Membantu dokter dan pasien dalam memilih obat yang lebih efektif dan aman dengan memanfaatkan pendekatan algoritma Content-Based Filtering dan Collaborative Filtering.

**Solution Approach**<br>
* Content-Based Filtering digunakan untuk memberikan rekomendasi berdasarkan karakteristik obat, seperti efek samping dan kejadian medis yang telah tercatat.
* Collaborative Filtering digunakan untuk memberikan rekomendasi berdasarkan preferensi pengguna lain yang memiliki riwayat medis serupa.

## **Data Understanding**
**Exploratory Data Analysis (EDA) & Data Visualization**<br>

Sebelum membangun model, kami melakukan eksplorasi data untuk memahami struktur dataset dan kualitasnya. Berikut adalah beberapa langkah EDA yang dilakukan:
1. Deskriptif statistik: Melihat ringkasan statistik untuk data numerik (misalnya, rating obat, umur pasien).
2. Distribusi data: Menilai distribusi rating obat dan efek samping untuk memahami pola umum.
3. Visualisasi hubungan antar fitur: Melihat korelasi antara fitur (misalnya, rating obat dan efek samping).

Dalam proyek ini, kami menggunakan 2 dataset yang berasal dari database *drugs.com* dan *druglib.com* yang dapat di akses pada link [ini](https://github.com/DatasetsLibrary/RECOMED?tab=readme-ov-file).

**A. Dataeset Events**<br>
**1. Struktur dataset**<br>
Dataset "Events" terdiri dari 5 kolom dengan 2486 baris. Mayoritas kolom bertipe ```object``` kecuali kolom *Age* bertipe ```int64```. Berikut detail variabel fitur dataset ini:<br>    
   - ```DrugName```: Nama obat yang digunakan (misalnya "Abilify").<br>
   - ```Age```: Usia individu yang melaporkan reaksi tersebut.<br>
   - ```Genius```: Jenis kelamin individu (misalnya "male" atau "female").<br>
   - ```Reactions```: Reaksi medis yang terkait dengan obat (misalnya "Drug Level Increased").<br>
   - ```Advent Event```: Kejadian medis tambahan yang terkait dengan reaksi tersebut (misalnya "death", "hospitalization").<br>
```
RangeIndex: 2486 entries, 0 to 2485
Data columns (total 5 columns):
 #   Column        Non-Null Count  Dtype 
---  ------        --------------  ----- 
 0   DrugName      2486 non-null   object
 1   Age           2486 non-null   int64 
 2   Genius        2486 non-null   object
 3   Reactions     2486 non-null   object
 4   Advent Event  1483 non-null   object
dtypes: int64(1), object(4) <br>
```

**2. Statistik Deskriptif (untuk kolom Age):**<br>
Kolom yang dapat diproses menggunakan fungsi ```describe()``` adalah kolom ```Age```, karena bertipe data ```int64```. Berdasarkan output diatas, rentang usia pengguna, dari usia termuda (```min```) adalah 1 tahun hingga usia tertua (```max```) 97 tahun, dengan rata-rata usia (```mean```) adalah 55 tahun.
```
Age
count  2486.000000
mean     55.482301
std      18.657139
min       1.000000
25%      45.000000
50%      58.000000
75%      68.000000
max      97.000000
```

**3. Data Kosong (Missing Values):**<br>
Kolom ```Advent Event``` memiliki nilai yang hilang (1003 nilai kosong, dari total 2486 entri), yang bisa jadi mempengaruhi analisis lebih lanjut tentang kejadian atau peristiwa terkait.

| Kolom          | Missing Values |
|----------------|----------------|
| DrugName       | 0              |
| Age            | 0              |
| Genius         | 0              |
| Reactions      | 0              |
| Advent Event   | 1003           |


Berikut adalah output beberapa baris pertama dataset _Events_ dengan fungsi ```head``` setelah kolom "Genius" dirubah menjadi "Gender" untuk memudahkan proses selanjutnaya.
| Index | DrugName | Age | Gender | Reactions                                             | Advent Event          |
|-------|----------|-----|--------|-------------------------------------------------------|-----------------------|
| 0     | Abilify  | 63  | male   | Drug Level Increased                                  | NaN                   |
| 1     | Abilify  | 55  | male   | Thyroid Cancer                                       | NaN                   |
| 2     | Abilify  | 63  | male   | Completed Suicide; Suicide Attempt; Coma            | death; hospitalization|
| 3     | Abilify  | 31  | male   | Eosinophilic Pneumonia; Pulmonary Alveolar Ha...     | death                 |
| 4     | Abilify  | 75  | female | Lymphopenia; Anaemia; Neutropenia                   | hospitalization       |

**4. Visualisasi dataset**<br>

  Dataset Events: analisis dilakukan untuk mengetahui obat mana yang paling sering dikaitkan dengan reaksi buruk, atau reaksi mana yang paling sering dilaporkan. Analisis juga bisa mencakup hubungan antara jenis obat, usia, dan tingkat keparahan reaksi.

  ![Distribusi Usia dalam Dataset Events](https://github.com/user-attachments/assets/523002d8-f8a2-43ed-b614-43e094524e24)

  a. Distribusi Usia<br>
  Sebagian besar individu dalam dataset ini berusia antara 45 hingga 85 tahun, dengan puncak frekuensi di sekitar usia 68 tahun. Ini menunjukkan bahwa data lebih banyak berasal dari individu dewasa hingga lanjut usia. 
![10 Obat Teratas Berdasarkan Frekuensi dalam Dataset Events](https://github.com/user-attachments/assets/d4d927b8-fbed-41d9-bda8-fc3166d92fbc)

   b. Frekuensi Obat:<br>
   Visualisasi menunjukkan daftar 10 obat yang paling sering dilaporkan dalam dataset ini. Obat yang lebih umum seperti "Seroqu" memiliki jumlah laporan yang lebih tinggi dibandingkan obat lainnya.

![10 Reaksi Teratas Berdasarkan Frekuensi dalam Dataset Events](https://github.com/user-attachments/assets/fb592e01-29ae-4c00-867d-46075dddc037)

  c. Frekuensi Reaksi:<br>
  Di antara reaksi yang paling sering dilaporkan, "Death" menduduki posisi teratas, diikuti dengan reaksi seperti "Drug Ineffective" dan "Drug Ineffective; Atonic Seizures". Reaksi ini memberikan wawasan penting tentang potensi dampak serius dari obat-obatan tersebut.

**5. Analisis Korelasi Dataset Events**<br>
Untuk mengidentifikasi hubungan antar kolom dan menguji apakah ada pola yang menarik antara fitur-fitur yang ada.
![Frekuensi Reaksi Berdasarkan Usia dalam Dataset Eventsl](https://github.com/user-attachments/assets/a614b655-b24d-4ebb-862e-a2d79564f4b4)

  a. Frekuensi Reaksi Berdasarkan Usia:<br>
Grafik ini menunjukkan bahwa frekuensi reaksi buruk yang dilaporkan meningkat seiring bertambahnya usia. Hal ini mungkin mencerminkan bahwa individu yang lebih tua lebih cenderung melaporkan reaksi terhadap obat-obatan, atau obat-obatan yang mereka gunakan cenderung menyebabkan lebih banyak efek samping pada kelompok usia ini.

![Frekuensi Kejadian Berdasarkan Usia dalam Dataset Events](https://github.com/user-attachments/assets/53199082-8259-4b58-91ca-a111bd4053db)

b. Frekuensi Kejadian Berdasarkan Usia:<br>
Grafik kedua menunjukkan bahwa kejadian-kejadian buruk atau serius (seperti kematian atau rawat inap) juga lebih sering dilaporkan pada usia yang lebih tua. Ini mungkin menunjukkan hubungan antara peningkatan usia dan peningkatan risiko efek samping atau kejadian negatif lainnya.

**6. Kesimpulan & insight dataset Events**
- Deskripsi: Dataset ini berisi informasi tentang efek samping yang terkait dengan obat dan kejadian medis terkait dengan penggunaan obat (misalnya, kematian, rawat inap, reaksi obat).
- Tujuan: Dataset ini berfokus pada efek samping obat dan kejadian medis terkait dengan penggunaan obat tertentu.
- Poin penting: Dataset ini lebih banyak menyertakan informasi tentang kejadian medis yang terjadi setelah penggunaan obat, serta hubungan obat dengan reaksi tubuh pasien.
- Relevansi: Dataset ini cocok untuk Content-Based Filtering, karena kita dapat memberikan rekomendasi obat berdasarkan deskripsi efek samping dan kejadian medis yang terkait. Misalnya, jika pasien memiliki riwayat tertentu, kita dapat merekomendasikan obat yang tidak memiliki efek samping yang serupa.

**B. Dataeset Drug Rating**<br>
**1. Struktur dataset**<br>
Dataset "Drug Rating" terdiri dari 15 kolom dengan 3293 baris. Semua kolom bertipe ```object``` (```string```), yang menunjukkan bahwa semua informasi  bersifat kategorikal. Berikut detail variabel fitur dataset ini:<br>
      Kolom 3351, 2, 3, ... 13, 14, sampai kolom 15<br>

```
RangeIndex: 3293 entries, 0 to 3292
Data columns (total 15 columns):
 #   Column  Non-Null Count  Dtype 
---  ------  --------------  ----- 
 0   3351    3293 non-null   object
 1   2       3293 non-null   object
 2   3       3293 non-null   object
 3   4       3291 non-null   object
 4   5       2778 non-null   object
 5   6       2831 non-null   object
 6   7       3293 non-null   object
 7   8       3293 non-null   object
 8   9       3293 non-null   object
 9   10      3293 non-null   object
 10  11      3293 non-null   object
 11  12      3293 non-null   object
 12  13      3272 non-null   object
 13  14      3215 non-null   object
 14  15      3271 non-null   object
dtypes: object(15)
```

Pada dataset ini, nama kolom tidak lazim, penjelasan detailnya akan dijelaskan dengan menampilkan beberapa baris pertama setiap kumpulan data untuk memahami strukturnya, memakai ```drug_rating_df.head()```

| 3351 | 2        | 3                  | 4                    | 5                 | 6                   | 7         | 8                  | 9      | 10       | 11                | 12                 | 13                                       | 14    | 15                                   |
|------|----------|--------------------|----------------------|-------------------|---------------------|-----------|--------------------|--------|----------|-------------------|--------------------|------------------------------------------|-------|---------------------------------------|
| 0    | F1       | F2                | F3                  | F4               | F5                 | F6        | F1                | NaN    |          |                   |                    |                                          |       |                                       |
| 1    | Level    | Age               | Genus               | Condition Reason | Other Condition     | Other Drug| DrugName          | Category | OverallRating | Effectiveness     | SideEffect         | Dosage                                  | Benefit| SideEffect                          |
| 2    | patient  | 22                | male                | Depression        | Sleeplessness       | Nil       | Mirtazapine       | Adrenergic-Alpha-Antagonists Histamine-H1-Ant... | 1    | Ineffective      | Severe Side Effects | 15mg taken once daily for the period of 2 Months | Nil   | now taking temazepam to help with sleeping and... |
| 3    | patient  | 38                | male                | Clinical Depression | NaN               | NaN       | Mirtazapine       | Adrenergic-Alpha-Antagonists Histamine-H1-Ant... | 2    | Ineffective      | Moderate Side Effects | 30mg taken daily (evening) for the period of ... | Did improve my appetite; which was suffering a... | Mild weight gain. Period of tiredness for two ... | Was prescribed as an alternative to Citalopram... |
| 4    | patient  | 30                | female              | Chronic insomnia  | Anxiety; panic attacks | Epilium  | Mirtazapine       | Adrenergic-Alpha-Antagonists Histamine-H1-Ant... | 9    | Highly Effective | Moderate Side Effects | 30 mg taken take one hour before bed for the ... | -night time sleep (at least 5-6 hours per nigh... | Constant feeling of hunger( feeling of empty s... | Hour before bed: 30 mg mirtazapine( avanza) an... |

Berdasarkan output diatas, baris pertama & kedua dataset Drug Rating tidak relevan, karena informasi nama kolom ada di baris ketiga. Untuk itu perlu dihilangkan kedua baris tersebut, sehingga memudahkan proses selanjutnya.<br>

| ID  | Level   | Age | Gender | Condition                 | Reason                  | Other Condition        | Other Drug         | DrugName    | Category                                      | OverallRating | Effectiveness    | SideEffect             | Dosage                                     | Benefit                                                              | SideEffect                                                            | Comment Review                                                        |
|-----|---------|-----|--------|---------------------------|-------------------------|-------------------------|--------------------|-------------|----------------------------------------------|---------------|------------------|------------------------|--------------------------------------------|----------------------------------------------------------------------|------------------------------------------------------------------------|------------------------------------------------------------------------|
| 0   | patient | 22  | male   | Depression               | Sleeplessness           | Nil                     | Nil                | Mirtazapine | Adrenergic-Alpha-Antagonists Histamine-H1-Anta... | 1             | Ineffective      | Severe Side Effects    | 15mg taken once daily for the period of 2 Months | Nil                                                                  | now taking temazepam to help with sleeping and...                     | -                                                                      |
| 1   | patient | 38  | male   | Clinical Depression      | NaN                     | NaN                     | NaN                | Mirtazapine | Adrenergic-Alpha-Antagonists Histamine-H1-Anta... | 2             | Ineffective      | Moderate Side Effects  | 30mg taken daily (evening) for the period of ... | Did improve my appetite; which was suffering a...                     | Mild weight gain. Period of tiredness for two ...                      | Was prescribed as an alternative to Citalopram...                     |
| 2   | patient | 30  | female | Chronic insomnia         | Anxiety; panic attacks  | Epilium                 | NaN                | Mirtazapine | Adrenergic-Alpha-Antagonists Histamine-H1-Anta... | 9             | Highly Effective | Moderate Side Effects  | 30 mg taken take one hour before bed for the ... | -night time sleep (at least 5-6 hours per nigh...                      | Constant feeling of hunger (feeling of empty s...                     | Hour before bed: 30 mg mirtazapine( avanza) an...                     |
| 3   | patient | 19  | female | Depression; Anxiety; OCD; Nausea | CFS; IBS; insomnia      | Lansoprazole; Loestrin 30 | NaN                | Mirtazapine | Adrenergic-Alpha-Antagonists Histamine-H1-Anta... | 2             | Ineffective      | Mild Side Effects      | 30mg taken once a day for the period of 7 months | Helped me easily get a good sleep. Possible re...                      | Too tired to get up in morning; irritability; ...                      | Did little if anything for depression and anxi...                     |
| 4   | -       | -   | -      | Anxiety/insomnia         | /na                     | Oxazepam 30             | NaN                | Mirtazapine | Adrenergic-Alpha-Antagonists Histamine-H1-Anta... | 6             | Ineffective      | Moderate Side Effects  | 30mg taken 1 at night for the period of 1 month | 6hrs sleep per night. Vivid dreams. No other s...                      | Morning anxiety ++                                                   | 30mg daily oxazepam 30 prn x3 max                                    |


- Output hasil dataset Drug Rating yang telah relevan nama kolomnya, yang terdiri dari:<br>    
  - ```Level```: Peran pengguna (pasien/ dokter).
  - ```Age```: Umur pasien.
  - ```Genus```: Jenis kelamin pasien.
  - ```Condition Reason```: Pertimbangan penggunaan obat.
  - ```Other Condition```: Pertimbangan penggunaan obat yang lain.
  - ```Other Drag```: Obat penyerta.
  - ```DrugName```: Nama obat.
  - ```Category```: Kategori obat.
  - ```OverallRating```: Rating.
  - ```Effectiveness```: Keefektifan obat.
  - ```SideEffect```: Tingkatan efek samping.
  - ```Dosage```: Dosis obat.
  - ```Benefit```: Keuntungan penggunaan obat.
  - ```SideEffect```: Efek samping yang tampak.
  - ```Comment Review```: Ulasan pengguna obat.

- Bila melihat penamaan kolom, ada beberapa nama yang harus disesuaikan, untuk memudahkan proses selanjutnya. Diantaranya, nama kolom "Genus" yang tidak sesuai dengan isi kolomnya terkait jenis kelamin; Ada kolom yang mempunyai nama sama yaitu "SideEffect" dengan isi berbeda; Serta ada kolom dengan dua kata yang memakai spasi dan tidak. <br>
- Analisis dapat dilakukan dengan melihat seberapa efektif obat-obatan, mengelompokkan obat berdasarkan efek sampingnya, serta menilai pengaruh efek samping terhadap rating keseluruhan obat. Menganalisis hubungan antara "OverallRating" dan "Effectiveness" atau "SideEffect" juga bisa memberikan wawasan yang menarik.
- Tujuan: Dataset ini berfokus pada evaluasi efek terapeutik dari obat berdasarkan pengalaman pengguna atau pasien.

**2. Statistik Deskriptif:**
- Kolom "Level" (berisi informasi jenis pasien): Sebagian besar entri adalah "patient" (3162 entri).
- Kolom "Age" (Usia): Terdapat variasi usia dengan nilai terbanyak adalah 45 tahun.
- Kolom "Genus" (Jenis kelamin): Kebanyakan adalah perempuan (2652 entri).
- Kolom "Condition Reason" (Kondisi yang dirawat): Yang paling umum adalah "depression" (183 entri).
- Kolom "Other Condition" hingga "Comment Review" memberikan informasi yang lebih rinci tentang efek samping, efektivitas, dosis, dan lainnya, dengan beberapa kolom seperti "SideEffect", "Other Condition", dan "Benefit" mengandung beberapa nilai kosong.
```
1         Level   Age   Genus Condition Reason Other Condition Other Drag  \
count      3291  3291    3291             3289            2776       2829   
unique        3    86       3             1638            1129       1321   
top     patient    45  female       depression            none       none   
freq       3162   117    2652              183            1101       1140   

1                      DrugName  \
count                      3291   
unique                      233   
top     Lexapro (Escitalopram)    
freq                         73   

1                                                Category  OverallRating  \
count                                                3291           3291   
unique                                                182             10   
top     topical-agents-skin-and-mucous-membranes topic...             10   
freq                                                  131            747   

1          Effectiveness         SideEffect  \
count               3291               3291   
unique                 5                  5   
top     Highly Effective  Mild Side Effects   
freq                1389               1083   

1                                                  Dosage Benefit SideEffect  \
count                                                3291    3270       3213   
unique                                               3210    3189       2984   
top     300 mg initially increased to 450 mg/day  take...    none       none   
freq                                                    5      24        106   

1      Comment Review  
count            3270  
unique           3189  
top                 -  
freq               12
```

**3. Data Kosong (Missing Values):**<br>
- Beberapa kolom memiliki data kosong, terutama pada kolom "Other Condition" (515 entri kosong) dan "Other Drag" (462 entri kosong).
- Kolom yang lain "SideEffect", "Benefit", Comment Review" dan "Condition Reason" juga memiliki data kosong, yang perlu diperhatikan dalam analisis selanjutnya.

| No. | Nama Kolom          | Missing Values |
|-----|---------------------|----------------|
| 1   | Level               | 0              |
| 2   | Age                 | 0              |
| 3   | Genus               | 0              |
| 4   | Condition Reason    | 2              |
| 5   | Other Condition     | 515            |
| 6   | Other Drag          | 462            |
| 7   | DrugName            | 0              |
| 8   | Category            | 0              |
| 9   | OverallRating       | 0              |
| 10  | Effectiveness       | 0              |
| 11  | SideEffect          | 0              |
| 12  | Dosage              | 0              |
| 13  | Benefit             | 21             |
| 14  | SideEffect          | 78             |
| 15  | Comment Review      | 21             |

Sebelum dilakukan visualisasi, beberapa kolom disesuaikan penamaannya untuk memudahkan proses selanjutnya. Diantara kolom yang disesuaikan yaitu:
- "Genus" yang merupakn kolom berisi keterangan jenis kelamin, disesuaikan menjadi "Gender".
- Kolom "Age" & "OverallRating" dari kategorikal di rubah menjadi numerik.<br>

Hasil penyesuaian seperti tampak pada output dibawah ini:

|   # | Level    | Age  | Gender | Condition                | Reason             | Other Condition    | Other Drug            | DrugName    | Category                                                | OverallRating | Effectiveness      | SideEffect            | Dosage                                      | Benefit                                                  | SideEffect                                                  | Comment Review                                         |
|-----|----------|------|--------|--------------------------|--------------------|--------------------|-----------------------|-------------|--------------------------------------------------------|---------------|--------------------|-----------------------|--------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|   1 | patient  | 22.0 | male   | Depression               | Sleeplessness      | Nil                | Nil                   | Mirtazapine | Adrenergic-Alpha-Antagonists Histamine-H1-Antagonists | 1             | Ineffective        | Severe Side Effects   | 15mg taken once daily for 2 months         | Nil                                                    | Now taking temazepam to help with sleeping and anxiety | -                                                      |
|   2 | patient  | 38.0 | male   | Clinical Depression      | NaN                | NaN                | Nil                   | Mirtazapine | Adrenergic-Alpha-Antagonists Histamine-H1-Antagonists | 2             | Ineffective        | Moderate Side Effects | 30mg taken daily (evening) for 3 months   | Did improve appetite; which was suffering a lot        | Mild weight gain; Period of tiredness for two weeks    | Was prescribed as an alternative to Citalopram         |
|   3 | patient  | 30.0 | female | Chronic insomnia         | Anxiety; panic attacks | Epilium        | Nil                   | Mirtazapine | Adrenergic-Alpha-Antagonists Histamine-H1-Antagonists | 9             | Highly Effective   | Moderate Side Effects | 30 mg taken one hour before bed for 2 months | Night-time sleep (5-6 hours per night)                | Constant hunger (feeling of empty stomach)             | Hour before bed: 30mg mirtazapine (Avanza) and Epilium |
|   4 | patient  | 19.0 | female | Depression; Anxiety; OCD | CFS; IBS; insomnia | Lansoprazole; Loestrin 30 | Nil          | Mirtazapine | Adrenergic-Alpha-Antagonists Histamine-H1-Antagonists | 2             | Ineffective        | Mild Side Effects     | 30mg taken once daily for 7 months         | Helped me easily get a good sleep                      | Too tired to get up in morning; irritability           | Did little for depression and anxiety                  |
|   5 | -        | NaN  | -      | Anxiety/insomnia         | /na                | Oxazapam 30        | Nil                   | Mirtazapine | Adrenergic-Alpha-Antagonists Histamine-H1-Antagonists | 6             | Ineffective        | Moderate Side Effects | 30mg taken once at night for 1 month       | 6 hours sleep per night. Vivid dreams. No other issues | Morning anxiety ++                                     | 30mg daily oxazepam 30 prn x3 max                      |

<br>

**4. Visualisasi dataset**<br>

![Distribusi Usia dalam Dataset Drug Rating](https://github.com/user-attachments/assets/ac9aedf0-88b6-41ef-9636-cecee353abff)

a. Distribusi Usia:<br>
Seperti yang terlihat pada grafik, sebagian besar data berasal dari pasien dengan usia 45 tahun. Ini mencerminkan distribusi usia yang terpusat di sekitar kelompok usia dewasa tengah hingga lanjut usia.

![10 Rating Pengguna Teratas Berdasarkan Frekuensi dalam Dataset Drug Rating](https://github.com/user-attachments/assets/748fa6cf-4a4e-4b2e-b436-f17b0ea18161)

b. Rating Pengguna Teratas:<br>
Dalam dataset ini, pengguna paling sering menilai dengan rating "10", diikuti dengan "8" dan "9", serta rating pengguna lainnya. Ini menunjukkan bahwa sebagian besar pengguna merasa puas karena ratingnya tinggi.

![10 Efektivitas Obat Teratas Berdasarkan Frekuensi dalam Dataset Drug Rating](https://github.com/user-attachments/assets/c097d5ae-75aa-43e4-82dd-da5de7b3243a)

c. Efektivitas Obat:<br>
Obat yang paling banyak dilaporkan memiliki rating "Highly Effective", menunjukkan bahwa banyak pasien merasakan manfaat yang baik dari obat yang mereka gunakan. Sebagian besar laporan juga menunjukkan bahwa obat tersebut memberikan hasil yang cukup baik.

<br>

**5. Korelasi antara Usia dan Rating Pengguna atau Efektivitas di Dataset Drug Rating**<br>

![Frekuensi Rating Pengguna Berdasarkan Usia dalam Dataset Drug Rating](https://github.com/user-attachments/assets/8b181ae6-c6bb-49bd-b32a-35530ed09851)

a. Frekuensi Rating Pengguna Berdasarkan Usia:<br>
Grafik pertama menunjukkan bahwa frekuensi rating pengguna relatif meningkat pada usia rentang 10 sampai 54 tahun dan turun setelahnya. Hal ini konsisten dengan temuan sebelumnya di dataset Events, di mana individu yang lebih tua lebih cenderung merasakan obat-obatan yang mereka gunakan, menyebabkan lebih banyak efek samping, sehingga kurang puas terhadap penggunaan obat atau mungkin tidak menilai secara rinci.

![Frekuensi Efektivitas Obat Berdasarkan Usia dalam Dataset Drug Rating](https://github.com/user-attachments/assets/bc6f1070-70e0-443a-a567-0e3e3e05d20c)

b. Frekuensi Efektivitas Obat Berdasarkan Usia:<br>
Grafik kedua menunjukkan bahwa efektivitas obat lebih sering dilaporkan oleh individu yang lebih muda. Ini menunjukkan bahwa pengobatan cenderung lebih efektif pada kelompok usia yang lebih muda, atau mungkin orang yang lebih muda lebih cenderung melaporkan respons positif terhadap pengobatan.<br>

Untuk memudahkan proses dan analisis berikutnya, maka diperlukan pembersihan data pada kolom "Gender" di kedua dataset, yaitu baris data dengan nilai '-' atau NaN. Output dibawah ini, merupakan kolom "Gender" yang masih terdapat baris data dengan nilai '-'.<br>
```
(Gender
 female     1516
 male        950
 (3; 2)        4
 (1; 2)        3
 (0; 2)        3
 (12; 2)       2
 (10; 2)       1
 (21; 2)       1
 (6; 2)        1
 (4; 2)        1
 (5; 2)        1
 (13; 2)       1
 (14; 2)       1
 (2; 2)        1
 Name: count, dtype: int64,
 Gender
 female    2652
 male       611
 Name: count, dtype: int64)
```

Output kolom "Gender" pada kedua dataset yang sudah dibersihkan dari baris data dengan nilai '-'.<br>
```
(Gender
 female    1516
 male       950
 Name: count, dtype: int64,
 Gender
 female    2652
 male       611
 Name: count, dtype: int64)
```

**6. Korelasi Jenis Kelamin terhadap Rating Pengguna dan Efektivitas di Dataset Drug Rating**<br>

![Rating Pengguna Berdasarkan Jenis Kelamin dalam Dataset Drug Rating](https://github.com/user-attachments/assets/0e3ca408-1921-4e3a-853c-1a3c5d9d8847)

a. Rating Pengguna Berdasarkan Jenis Kelamin:<br>
Grafik pertama menunjukkan bahwa wanita lebih cenderung memberikan rating obat dibandingkan pria, dengan frekuensi rating pengguna yang lebih tinggi di kelompok wanita. Ini bisa menunjukkan bahwa wanita mungkin lebih aktif memberikan nilai rating atau lebih cenderung merespon pengalaman penggunaan obat yang dialaminya.<br>

![Efektivitas Obat Berdasarkan Jenis Kelamin dalam Dataset Drug Rating](https://github.com/user-attachments/assets/a792609d-fe65-4593-a8a9-91e004903bfa)

b. Efektivitas Obat Berdasarkan Jenis Kelamin:<br>
Grafik kedua menunjukkan bahwa wanita lebih sering melaporkan obat sebagai "Sangat Efektif", sementara pria cenderung melaporkan efek yang lebih rendah. Ini mungkin menunjukkan perbedaan persepsi atau respons fisiologis terhadap pengobatan antara pria dan wanita.<br>

**C. Kesimpulan Umum**
1. Dataset Events:
     - Mayoritas pasien yang melaporkan reaksi buruk adalah individu dewasa hingga lanjut usia.
     - Beberapa obat seperti Seroqu cenderung memiliki frekuensi laporan yang lebih tinggi, yang mungkin menunjukkan prevalensi penggunaannya atau ketergantungan pada obat tersebut.
     - Reaksi yang paling sering dilaporkan adalah "Death", yang bisa mengindikasikan masalah dosis atau pengelolaan obat.
     - Pada Dataset Events menunjukkan bahwa usia berhubungan dengan meningkatnya frekuensi reaksi buruk dan kejadian serius akibat obat-obatan.<br>

2. Dataset Drug Rating:
     - Sebagian besar pasien yang dilaporkan dalam dataset ini adalah perempuan, dengan usia yang lebih banyak berada pada rentang dewasa menengah hingga lanjut usia.
     - Pengguna paling sering menilai dengan rating "10", yang dapat mencerminkan bahwa sebagian besar pasien merasakan manfaat penggunaan obat.
     - Obat-obat yang dilaporkan memiliki tingkat efektivitas yang cukup tinggi, dengan banyak pasien melaporkan bahwa obat tersebut sangat efektif.
     - Pada data Dataset Drug Rating mengungkapkan bahwa jenis kelamin berpotensi mempengaruhi persepsi terhadap efektivitas penggunaan obat, dengan wanita melaporkan lebih banyak pengalaman penggunaanya dan menilai obat lebih efektif daripada pria.
     - Usia juga menunjukkan korelasi dengan rating pengguna yang cenderung turun dan juga penurunan efektivitas obat pada individu yang lebih tua.

## **Data Preparation**
**A. Detection and Removal Duplicates**<br>
Sebelum membangun model, kita akan memeriksa duplikasi dalam data dan menghapusnya untuk memastikan data yang digunakan tidak redundan.
```
0
```
Dari output hasil diatas, dipastikan tidak terdapat duplikasi.

**B. Dropping Uneeded Column**<br>
Kita akan menghapus kolom yang tidak relevan untuk analisis lebih lanjut.
1. Dataset Events<br>

|   # | DrugName  | Age  | Gender | Reactions                                               | AdventEvent               |
|-----|-----------|------|--------|--------------------------------------------------------|---------------------------|
|   1 | Abilify   | 63   | male   | Drug Level Increased                                   | NaN                       |
|   2 | Abilify   | 55   | male   | Thyroid Cancer                                        | NaN                       |
|   3 | Abilify   | 63   | male   | Completed Suicide; Suicide Attempt; Coma              | death; hospitalization    |
|   4 | Abilify   | 31   | male   | Eosinophilic Pneumonia; Pulmonary Alveolar Haemorrhage | death                     |
|   5 | Abilify   | 75   | female | Lymphopenia; Anaemia; Neutropenia                     | hospitalization           |

Pada dataset Events kami memasukkan semua 5 variabel kolom, yaitu:<br>
  - ```DrugName```
  - ```Age```
  - ```Gender```
  - ```Reactions```
  - ```AdventEvent```

2. Dataset Drug Rating<br>

|   # | DrugName    | Age  | Gender  | Condition Reason        | OverallRating | Effectiveness      | SideEffect            |
|-----|-------------|------|---------|-------------------------|---------------|--------------------|-----------------------|
|   1 | Mirtazapine | 22   | male    | Depression              | 1             | Ineffective        | Severe Side Effects   |
|   2 | Mirtazapine | 38   | male    | Clinical Depression     | 2             | Ineffective        | Moderate Side Effects |
|   3 | Mirtazapine | 30   | female  | Chronic insomnia        | 9             | Highly Effective   | Moderate Side Effects |

Pada dataset Drug Rating kami hanya memasukkan 7 variabel kolom yang relevan untuk proses selanjutnya, yaitu:<br>
  - ```DrugName```
  - ```Age```
  - ```Gender```
  - ```ConditionReason```
  - ```OveralRating```
  - ```Effectiveness```
  - ```SideEffect```

**C. Handle Missing Value**<br>
Kita juga perlu menangani nilai yang hilang dalam data, baik dengan mengisi nilai yang hilang atau menghapus baris yang mengandung nilai yang hilang. Berikut hasil cek missing value pada kedua dataset:<br>
```
(DrugName         0
 Age              0
 Gender           0
 Reactions        0
 AdventEvent    985
 dtype: int64,
 DrugName           0
 Age                0
 Gender             0
 ConditionReason    2
 OverallRating      0
 Effectiveness      0
 SideEffect         0
 dtype: int64)
```

- Pada dataset Events, pada kolom ```AdventEvent``` terdapat 985 baris missing value. Kami akan menangani kolom ini, dengan memperhitungkan nilainya (seperti mean atau mode), pada baris yang nilainya hilang.
- Untuk dataset Drug Rating, pada kolom ```ConditionReason``` terdapat 2 missing value. Kami akan menangani kolom ini, dengan menghapus baris tersebut jika perlu. Berikut hasilnya setelah penanganan missing value pada kedua dataset:

```
(DrugName       0
 Age            0
 Gender         0
 Reactions      0
 AdventEvent    0
 dtype: int64,
 DrugName           0
 Age                0
 Gender             0
 ConditionReason    0
 OverallRating      0
 Effectiveness      0
 SideEffect         0
 dtype: int64)
```

- Dataset Events: Kolom ```Advent Event``` yang memiliki 985 nilai yang hilang, kami lakukan imputasi nilai yang hilang tersebut dengan nilai modus (nilai yang paling sering muncul), karena ini terkait dengan kejadian medis pengobatan, yang kemungkinan memiliki nilai yang lebih dominan. Berdasarkan output diatas pada dataset Events, saat ini tidak ada missing value.
- Dataset Drug Rating: Kolom ``` ConditionReason``` memiliki 2 nilai hilang, kami menghapus baris yang memiliki nilai hilang tersebut, karena mungkin data ini tidak terlalu penting untuk analisis lebih lanjut. Berdasarkan output diatas pada dataset Drug Eating, saat ini tidak ada missing value.

**D. Outliers Detection and Removal**<br>
Outliers dapat mempengaruhi kualitas model, oleh karena itu kami akan mendeteksi dan menghapus outliers dari dataset. Langkah ini akan di lakukan pada kolom yang mengandung data numerik yaitu ```Age``` dan ```OverallRating```, apakah ada outliers yang signifikan dalam kedua kolom tersebut dan menghapusnya jika perlu.<br>

![Distribusi outlier](https://github.com/user-attachments/assets/ed182395-b0e1-405d-b313-bfd51cb9035e)

Dari boxplot yang ditampilkan, kita dapat melihat beberapa hal:<br>
- Distribusi Umur (Age): Terdapat beberapa nilai ekstrem (outliers) pada kolom ```Age``` yang bisa jadi tidak realistis atau relevan (misalnya, nilai yang sangat rendah atau sangat tinggi). Kami akan membatasi nilai ```Age``` dalam rentang yang realistis (misalnya, 18 hingga 100 tahun) dan menghapus nilai yang berada di luar rentang tersebut.
- Distribusi Rating (OverallRating): Ada beberapa outliers di kolom ```OverallRating```, meskipun tidak se-ekstrem pada kolom ```Age```. Kami akan akan memeriksa apakah nilai rating yang lebih tinggi atau lebih rendah dari kisaran normal (misalnya, 1 hingga 10) dan menghapus atau mengganti nilai yang tidak sesuai.

```
(  DrugName  Age  Gender                                          Reactions  \
 0  Abilify   63    male                               Drug Level Increased   
 1  Abilify   55    male                                     Thyroid Cancer   
 2  Abilify   63    male           Completed Suicide; Suicide Attempt; Coma   
 3  Abilify   31    male   Eosinophilic Pneumonia; Pulmonary Alveolar Ha...   
 4  Abilify   75  female                  Lymphopenia; Anaemia; Neutropenia   
 
                 AdventEvent  
 0           hospitalization  
 1           hospitalization  
 2   death;  hospitalization  
 3                     death  
 4           hospitalization  ,
        DrugName Age  Gender                   ConditionReason  OverallRating  \
 2  Mirtazapine   22    male                        Depression            1.0   
 3  Mirtazapine   38    male               Clinical Depression            2.0   
 4  Mirtazapine   30  female                  Chronic insomnia            9.0   
 5  Mirtazapine   19  female  Depression; Anxiety; OCD; Nausea            2.0   
 6  Mirtazapine    -       -                  Anxiety/insomnia            6.0   
 
       Effectiveness             SideEffect  
 2       Ineffective    Severe Side Effects  
 3       Ineffective  Moderate Side Effects  
 4  Highly Effective  Moderate Side Effects  
 5       Ineffective      Mild Side Effects  
 6       Ineffective  Moderate Side Effects  )
```

Berdasarkan output diatas, outliers telah berhasil dihapus dari kedua dataset:<br>
- Dataset Events: Nilai ```Age``` yang berada di luar rentang 18-100 tahun telah dihapus.
- Dataset Drug Rating: Nilai ```OverallRating``` yang berada di luar rentang 1-10 telah dihapus.

**E. Encoding**<br>
Untuk menggunakan algoritma berbasis matriks, kita perlu mengonversi data kategorikal menjadi numerik.

```
# Mengonversi nama obat & gender menjadi angka untuk Collaborative Filtering
le = LabelEncoder()
drug_rating_clean['DrugName_encoded'] = le.fit_transform(drug_rating_clean['DrugName'])
drug_rating_clean['Gender_encoded'] = le.fit_transform(drug_rating_clean['Gender'])

# Mengonversi nama obat dan gender menjadi angka untuk Content-Based Filtering
events_clean['DrugName_encoded'] = le.fit_transform(events_clean['DrugName'])
events_clean['Gender_encoded'] = le.fit_transform(events_clean['Gender'])
```

Menangani encoding untuk kolom kategori ```Gender``` dan ```DrugName```, dengan mengonversinya menjadi format numerik untuk digunakan dalam proses selanjutnya, yaitu membangun model sistem rekomendasi.<br>

**F. Train Test Split**<br>
Membagi data menjadi set pelatihan dan set pengujian untuk evaluasi model.<br>

```
((2597, 9), (650, 9))
```

Pembagian dataset menjadi data pelatihan dan pengujian telah selesai untuk dataset Drug Rating:<br>
- Data Pelatihan: 2597 sampel<br>
- Data Pengujian: 650 sampel

**G. Ringkasan Proses Data Preparation:**<br>
1. Deteksi dan Penghapusan Duplikasi: Telah dilakukan pada kedua dataset.
2. Pembuangan Kolom yang Tidak Diperlukan: Kolom yang tidak relevan telah dihapus dari kedua dataset.
3. Penanganan Nilai Hilang: Nilai hilang diimputasi atau dihapus sesuai kebutuhan.
4. Deteksi dan Penghapusan Outliers: Outliers pada kolom ```Age``` (di dataset Events) dan ```OverallRating``` (di dataset Drug Rating) telah dihapus.
5. Encoding: Kolom kategorikal seperti ```Gender``` dan ```DrugName``` telah di-encode menjadi format numerik.
6. Train-Test Split: Kedua dataset telah dibagi menjadi data pelatihan dan pengujian.<br>
Data sudah siap untuk digunakan dalam membangun sistem rekomendasi berbasis content-based filtering dan collaborative filtering.

## **Modeling**<br>
Sistem rekomendasi adalah alat penting dalam berbagai domain, termasuk layanan kesehatan, e-commerce, dan hiburan, untuk membantu pengguna menemukan informasi atau item yang relevan. Dua pendekatan utama yang digunakan dalam pengembangan sistem ini adalah *Content-Based Filtering* dan *Collaborative Filtering*. Setiap pendekatan memiliki mekanisme, keunggulan, dan tantangan yang unik, yang penting untuk dipertimbangkan dalam membangun sistem rekomendasi obat.<br>

**A. Content-Based Filtering** <br>
Content-Based Filtering merekomendasikan item dengan menganalisis atribut item itu sendiri dan mencocokkannya dengan preferensi atau interaksi pengguna sebelumnya.<br>
1. Mekanisme: Pendekatan ini bergantung pada fitur-fitur item dan profil pengguna. Dalam sistem rekomendasi obat, pendekatan ini dapat menggunakan sifat kimiawi obat dan riwayat medis pasien untuk menyarankan obat yang sesuai [1], [3].
2. Keunggulan: Pendekatan ini mampu memberikan rekomendasi yang dipersonalisasi berdasarkan atribut spesifik item, yang sangat berguna dalam domain di mana preferensi pengguna terdefinisi dengan baik dan stabil [5].
3. Tantangan: Content-Based Filtering sering menghadapi keterbatasan dalam analisis konten. Sistem ini cenderung hanya merekomendasikan item yang mirip dengan yang sudah diketahui pengguna, sehingga kurang mampu memberikan rekomendasi yang beragam [3], [6]. <br>
4. Pada project ini, kami menggunakan informasi efek samping seperti reaksi obat (Reactions) dan kejadian medis atau event (Advent Event) untuk mencari kemiripan antar obat, dengan cara:<br>
   - Vectorize teks dari kolom ```Reactions``` dan ```Advent Event``` menggunakan *TfidfVectorizer*.
   - Menghitung kesamaan antar item (obat) menggunakan *cosine similarity* &.
   - Merekomendasikan obat berdasarkan kemiripan dengan item tertentu.

- Step 1: Menggabungkan kolom 'Reactions' dan 'Advent Event' sebagai fitur konten
  ```
  events_clean['Content'] = events_clean['Reactions'] + " " + events_clean['AdventEvent']
  ```
- Step 2: Vectorize teks menggunakan TfidfVectorizer
  ```
  tfidf_vectorizer = TfidfVectorizer(stop_words='english')
  tfidf_matrix = tfidf_vectorizer.fit_transform(events_clean['Content'])
  ```
- Step 3: Menghitung cosine similarity antar item
  ```
  cosine_sim = cosine_similarity(tfidf_matrix, tfidf_matrix)
  ```
- Step 4: Membuat fungsi rekomendasi berdasarkan kesamaan
  ```
  def recommend_content_based(drug_name, cosine_sim=cosine_sim, df=events_clean, top_n=5):
    # Cari indeks dari drug_name
    idx = df[df['DrugName_encoded'] == drug_name].index[0]
    
    # Mendapatkan skor kesamaan untuk semua obat dengan obat ini
    sim_scores = list(enumerate(cosine_sim[idx]))
    
    # Mengurutkan obat berdasarkan skor kesamaan (descending)
    sim_scores = sorted(sim_scores, key=lambda x: x[1], reverse=True)
    
    # Mengambil top_n obat yang mirip (kecuali dirinya sendiri)
    sim_scores = sim_scores[1:top_n+1]
    
    # Mengambil indeks obat yang mirip
    drug_indices = [i[0] for i in sim_scores]
    
    # Return nama obat yang direkomendasikan
    return df.iloc[drug_indices][['DrugName', 'Reactions', 'AdventEvent']]
  ```

Contoh sistem rekomendasi content based untuk obat *Abilify*<br>
```
recommendations = recommend_content_based(events_clean[events_clean['DrugName'] == "Abilify"]['DrugName_encoded'].iloc[0], top_n=5)
recommendations
```

|   #   | DrugName  | Reactions                                                   | AdventEvent                       |
|-------|-----------|-------------------------------------------------------------|-----------------------------------|
| 2468  | Zyprexa   | Drug Level Increased                                        | hospitalization                   |
| 688   | Dilantin  | Accident; Drug Level Increased; Pain; Injury;               | hospitalization                   |
| 927   | Keppra    | Accident; Drug Level Increased; Pain; Injury;               | hospitalization                   |
| 1187  | Morphin   | Myoclonus; Drug Level Increased; Drug Interaction;          | life threatening event; hospitalization |
| 662   | Diazepam  | Drug Level Above Therapeutic; Psychotic Disorder;           | hospitalization                   |

Sistem rekomendasi berbasis content-based filtering telah berhasil dibuat. Berdasarkan output diatas, hasil rekomendasi untuk obat *Abilify*, berdasarkan kesamaan pada reaksi obat dan event. Sistem berhasil memberikan rekomendasi obat yang memiliki kesamaan dalam reaksi yang dialami pasien dan event yang terjadi (hospitalization/ rawat inap).<br>


**B. Collaborative Filtering**<br>
Menggunakan pendekatan *user-based collaborative filtering* berbasis *Nearest Neighbors*, yang fokus pada kesamaan antar pengguna untuk merekomendasikan item (DrugName).<br>

1. Membuat pivot table dari data untuk collaborative filtering berbasis user-item interaction, yaitu dengan mengunakan kolom 'Gender' sebagai user_id dan 'DrugName' sebagai item_id
  ```
  user_item_matrix = drug_rating_clean.pivot_table(
    index='Gender', columns='DrugName_encoded', values='OverallRating', fill_value=0
  )
  ```
   Matriks ini merepresentasikan pengguna (Gender) pada baris dan item (DrugName) pada kolom dengan nilai (OverallRating) sebagai interaksinya.<br>

2. Menghitung kesamaan antar pengguna (user-based collaborative filtering)
  ```
  user_similarity = cosine_similarity(user_item_matrix)
  user_similarity_df = pd.DataFrame(user_similarity, index=user_item_matrix.index, columns=user_item_matrix.index)
  ```
   Menggunakan cosine similarity (salah satu metode utama dalam algoritma nearest neighbors), tingkat kesamaan dihitung antar pengguna untuk membuat matriks kesamaan.

3. Membuat fungsi untuk merekomendasikan obat berbasis collaborative filtering
  ```
  def recommend_user_based(user_id, user_item_matrix, user_similarity_df, top_n=5):
    # Mendapatkan skor kesamaan user
    user_gender = 'female' if user_id == 0 else 'male'
    similar_users = user_similarity_df[user_gender].sort_values(ascending=False)
    
    # Mengambil pengguna yang paling mirip
    similar_users = similar_users[1:]  # Hindari kesamaan dengan diri sendiri
    
    # Mendapatkan rekomendasi item berdasarkan pengguna yang mirip
    recommendations = user_item_matrix.loc[similar_users.index].mean(axis=0)
    recommendations = recommendations.sort_values(ascending=False)
    
    # mengambil top_n rekomendasi item
    top_recommendations = recommendations.head(top_n)
    
    return top_recommendations
  ```
   - Menyusun rekomendasi berdasarkan rata-rata preferensi dari pengguna yang mirip (similar_users).
   - Mengabaikan pengguna itu sendiri dalam perhitungan (baris similar_users[1:]).
   - Mengurutkan item berdasarkan skor rata-rata dan mengambil top_n rekomendasi.

Contoh rekomendasi berbasis collaborative filtering untuk user dengan ID 1<br>
```
recommendations_user_based = recommend_user_based(user_id=1, user_item_matrix=user_item_matrix, user_similarity_df=user_similarity_df, top_n=5)
recommendations_user_based
```

| DrugName_encoded | Score     |
|------------------|-----------|
| 162              | 8.842105 |
| 225              | 8.800000 |
| 1                | 8.727273 |
| 163              | 8.633333 |
| 151              | 8.558824 |

Sistem rekomendasi berbasis collaborative filtering telah berhasil dibuat. Berdasarkan output diatas, rekomendasi obat untuk pengguna dengan ID tertentu (dalam hal ini user 1) berdasarkan rata-rata rating dari pengguna lain yang mirip<br>

## **Evaluation**<br>
Evaluasi sistem rekomendasi dilakukan untuk mengukur seberapa baik sistem memberikan rekomendasi yang relevan.<br>
1. Evaluasi Content-Based Filtering
Content-based filtering menghasilkan skor kesamaan untuk setiap item. Untuk mengevaluasinya, kami menggunakan metrik ```Hit Rate```, yaitu evaluasi dengan cara mengukur seberapa sering item yang relevan muncul dalam daftar rekomendasi teratas. Hit Rate adalah metrik evaluasi yang digunakan untuk mengukur seberapa sering sistem rekomendasi berhasil memasukkan item yang relevan (dalam hal ini obat) ke dalam daftar rekomendasi yang diberikan kepada pengguna. Dalam konteks Content-Based Filtering, Hit Rate menunjukkan persentase kasus di mana item yang diharapkan (ground truth) muncul di rekomendasi sistem.<br>

$$
\text{Hit Rate} = \frac{\text{Jumlah Hits}}{\text{Total Kasus}}
$$

   Dimana:<br>
      - Jumlah Hit: Jumlah kasus di mana obat yang relevan (ground truth) ada di daftar rekomendasi.<br>
      - Total Kasus: Jumlah total evaluasi yang dilakukan (misalnya, jumlah pengguna atau item).

2. Evaluasi Collaborative Filtering
Collaborative filtering sering dilakukan dengan menggunakan data pembagian train-test. Kami menggunakan ```Root Mean Squared Error (RMSE)```, yaitu evaluasi dengan cara membandingkan prediksi rating dengan rating aktual dari dataset pengujian. RMSE adalah metrik evaluasi yang digunakan untuk mengukur seberapa baik prediksi model mendekati nilai sebenarnya. Dalam konteks sistem rekomendasi obat berbasis collaborative filtering, RMSE menunjukkan seberapa besar rata-rata kesalahan model dalam memprediksi peringkat (ratings) yang diberikan oleh pengguna terhadap obat tertentu. Semakin kecil nilai RMSE, semakin akurat model tersebut.<br>
Formula RMSE:<br>

$$
\text{RMSE} = \sqrt{\frac{1}{n} \sum_{i=1}^n (y_i - \hat{y}_i)^2}
$$

   Dimana:<br>
      - $$\( y_i \)$$: Peringkat aktual yang diberikan oleh pengguna. <br>
      - $$\( \hat{y}_i \)$$: Peringkat yang diprediksi oleh model. <br>
      - $$\( n \)$$: Jumlah observasi.

**A. Evaluasi Content-Based Filtering**

```
# Evaluasi Content-Based Filtering
def evaluate_content_based(df, cosine_sim, ground_truth_col='AdventEvent', top_n=5):
    """
    Evaluates content-based filtering using hit rate.
    
    Parameters:
    - df: DataFrame containing the original data.
    - cosine_sim: Cosine similarity matrix.
    - ground_truth_col: Column containing ground truth for evaluation.
    - top_n: Number of top recommendations to consider.
    
    Returns:
    - Hit rate: Percentage of cases where ground truth is found in the top-N recommendations.
    """
    hits = 0
    for idx in range(len(df)):
        # Generate recommendations untuk each item
        sim_scores = list(enumerate(cosine_sim[idx]))
        sim_scores = sorted(sim_scores, key=lambda x: x[1], reverse=True)
        sim_scores = sim_scores[1:top_n+1]  # Exclude self-similarity
        recommended_indices = [i[0] for i in sim_scores]
        
        # Cek jika ground truth exists pada recommendations
        ground_truth = df.iloc[idx][ground_truth_col]
        recommendations = df.iloc[recommended_indices][ground_truth_col].values
        if ground_truth in recommendations:
            hits += 1
    
    hit_rate = hits / len(df)
    return hit_rate

# Hit rate evaluation untuk content-based filtering
content_based_hit_rate = evaluate_content_based(events_clean, cosine_sim, ground_truth_col='AdventEvent', top_n=5)
```

**B. Evaluasi Collaborative Filtering**

```
# Evaluasi Collaborative Filtering
def evaluate_collaborative_filtering(predictions, test_data):
    """
    Evaluates collaborative filtering using RMSE.
    
    Parameters:
    - predictions: Predicted ratings.
    - test_data: Actual test data for validation.
    
    Returns:
    - RMSE: Root Mean Squared Error.
    """
    mse = np.mean([(pred.est - pred.r_ui) ** 2 for pred in predictions])
    rmse = np.sqrt(mse)
    return rmse

# Adjust collaborative filtering evaluation menggunakan nearest neighbor-based prediction
# Ensure user similarity matrix aligns with user-item matrix
adjusted_user_similarity = user_similarity[:len(user_item_matrix), :len(user_item_matrix)]

# Predict ratings menggunakan similarity-weighted approach
predicted_ratings_matrix = adjusted_user_similarity.dot(user_item_matrix) / np.abs(adjusted_user_similarity).sum(axis=1).reshape(-1, 1)

# Flatten predictions & true ratings untuk RMSE calculation
actual_ratings_flat = user_item_matrix.values.flatten()
predicted_ratings_flat = predicted_ratings_matrix.flatten()

# Filter out zeros (unrated items)
mask = actual_ratings_flat > 0
actual_ratings_filtered = actual_ratings_flat[mask]
predicted_ratings_filtered = predicted_ratings_flat[mask]

# Kalkulasi RMSE
collaborative_rmse = np.sqrt(mean_squared_error(actual_ratings_filtered, predicted_ratings_filtered))
```

**C. Hasil evaluasi kedua sistem rekomendasi:**

```
(0.9250106518960375, 2.031441256960782)
```

- Content-Based Filtering:<br>
Hit Rate: 92.5 %<br>
Sistem berhasil merekomendasikan item yang relevan dalam 92.5 % kasus.

- Collaborative Filtering:<br>
RMSE: 2.03<br>
Kesalahan rata-rata prediksi rating adalah sekitar 2.03 pada skala 1-10.

Interpretasi:<br>
1. Content-Based Filtering
   - Hasil Hit Rate sebesar 92.5% menunjukkan bahwa sistem rekomendasi berhasil memasukkan obat yang relevan (ground truth) ke dalam daftar rekomendasi sebanyak 92.5% dari total evaluasi. Artinya, sistem berhasil memberikan rekomendasi yang tepat pada mayoritas kasus.
   - Hasil ini, menunjukkan performa yang baik dengan hit rate yang tinggi, yaitu rekomendasi cukup akurat untuk item yang mirip.
2. Collaborative Filtering
   - Hasil RMSE sebesar 2.03 menunjukkan bahwa rata-rata kesalahan prediksi model adalah sekitar 2.03 unit pada skala peringkat 1-10. Maka model sering salah dalam memprediksi rating sebesar ±2 poin dari peringkat yang sebenarnya.
   - Hasil RMSE yang cukup besar, menunjukkan ruang untuk perbaikan, misalnya dengan memperbaiki data sparsity atau menggunakan algoritma prediksi lain.



### **References**
[1] Aditi, Sarode., Sarang, Dineshrao, Pojage., Kapil, Jajulwar., Snehlata, Dongre., Priya, Maidamwar. (2024). 1. Design of Medicine Recommendation System (MRS) for Biomedical Application Using Machine Learning Techniques.   https://doi10.1109/apcit62007.2024.10673613<br>

[2] Qasem, Kharma., Qusai, Shambour., Abdelrahman, H., Hussein. (2023). 4. A Hybrid Recommendation Model for Drug Selection. Studies in Informatics and Control - ICI Bucharest,  https://doi10.24846/v32i3y202307<br>

[3] <br>

[4] <br>

[5] <br>

[6] <br>

[7] <br>

[8] <br>

[9] <br>

[10]






