# **Medicine Recommendation System**

## **Project Overview**
Dalam proyek ini, kami membangun sistem rekomendasi medis menggunakan pendekatan *Content-Based Filtering* dan *Collaborative Filtering* untuk membantu pengguna (misalnya, pasien, dokter, apoteker) dalam memberikan rekomendasi obat berdasarkan fitur informasi karakteristik obat yang relevan serta riwayat data pengguna. Sistem ini bertujuan untuk memberikan saran pengobatan yang lebih akurat dengan memperhitungkan data medis pasien dan preferensi obat.
1. Content-Based Filtering menggunakan dataset "Events" yang berisi informasi tentang efek samping dan kejadian medis yang terkait dengan obat-obatan untuk memberikan rekomendasi berbasis karakteristik obat.

2. Collaborative Filtering menggunakan dataset "Drug Rating" yang berisi rating obat oleh pengguna (pasien) untuk memberikan rekomendasi berdasarkan pola preferensi pengguna lain yang memiliki kondisi medis serupa.

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

Sebelum membangun model, perlu dilakukan eksplorasi data untuk memahami struktur dataset dan kualitasnya. Berikut adalah beberapa langkah EDA yang dilakukan:
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

b. Frekuensi Efektivitas Obat Berdasarkan Usia:
Grafik kedua menunjukkan bahwa efektivitas obat lebih sering dilaporkan oleh individu yang lebih muda. Ini menunjukkan bahwa pengobatan cenderung lebih efektif pada kelompok usia yang lebih muda, atau mungkin orang yang lebih muda lebih cenderung melaporkan respons positif terhadap pengobatan.











