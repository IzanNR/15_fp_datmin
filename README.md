# Prediksi Penyakit Diabetes dengan Metode Data Mining  
**Studi Komparasi Teknik Preprocessing dan Algoritma Klasifikasi**

## Identitas


| Nama                                | NRP         | GitHub                                      |
|------------------------------------|-------------|----------------------------------------------|
| Muhammad Rafi Budiman                       | 5025231297  | [@gembut](https://github.com/gembut)     |
| Izan Nafis Rahman     | 5025231298  | [@IzanNR](https://github.com/IzanNR) |

- **Departemen**: Teknik Informatika, Institut Teknologi Sepuluh Nopember (ITS)  

---

## Deskripsi Singkat

Proyek ini membangun model **prediksi diabetes** berbasis _data mining_ menggunakan dataset **CDC Diabetes Health Indicators**.  
Fokus utama:

- Membandingkan **teknik preprocessing** (feature engineering, sampling, scaling).  
- Menguji beberapa **algoritma klasifikasi** (LR, NB, DT, RF, XGBoost, AdaBoost, Voting).  
- Mencari kombinasi **pipeline terbaik** untuk F1-score berbobot pada data tidak seimbang.

---

## Dataset

- **Nama**: CDC Diabetes Health Indicators (BRFSS 2015)  
- **Ukuran**: 253.680 sampel, 22 kolom (1 target, 21 fitur prediktor) :contentReference[oaicite:0]{index=0}  
- **Sumber**:  
  - UCI Machine Learning Repository – *CDC Diabetes Health Indicators*  
    - [https://archive.ics.uci.edu/dataset/891/cdc%2Bdiabetes%2Bhealth%2Bindicators](https://archive.ics.uci.edu/dataset/891/cdc%2Bdiabetes%2Bhealth%2Bindicators)

Target **`Diabetes_binary`**:  
- `0` → tidak diabetes  
- `1` → diabetes  

---

## Metodologi (Ringkas)

1. **Eksplorasi Data (EDA)**  
   - Visualisasi distribusi fitur numerik (BMI, MentHlth, PhysHlth).  
   - Visualisasi fitur kategorikal terhadap `Diabetes_binary`.  
   - Heatmap korelasi semua fitur + fitur hasil rekayasa.

2. **Pembersihan Data**  
   - Menghapus ±24.206 baris duplikat → 229.474 sampel bersih.  
   - Tidak ada missing value pada dataset.

3. **Pembagian Data**  
   - Train–test split **80:20** dengan **stratified split** untuk menjaga rasio kelas.

4. **Preprocessing & Eksperimen**  
   - Tiga **skenario**: Feature Engineering, Sampling, dan Scaling.  
   - Masing-masing dikombinasikan dengan beberapa algoritma klasifikasi.

5. **Algoritma Klasifikasi**  
   - Logistic Regression (LR)  
   - Naive Bayes (NB)  
   - Decision Tree (DT)  
   - Random Forest (RF)  
   - XGBoost  
   - AdaBoost  
   - Voting Classifier (soft voting dari model-model di atas)

6. **Metrik Evaluasi**  
   - **F1-score berbobot (weighted F1)** → metrik utama.  
   - **ROC AUC** → metrik pendukung.  
   - Analisis **confusion matrix** untuk melihat perilaku tiap kelas.

---

## Skenario Eksperimen

### Skenario 1 – Feature Engineering & Pemilihan Fitur

Membandingkan tiga konfigurasi:

1. **Baseline (Tanpa Feature Engineering)**  
   - Menggunakan seluruh fitur asli (setelah penghapusan duplikat).  

2. **Fixed Feature Selection (berbasis literatur)**  
   - Subset: `HighBP`, `HighChol`, `BMI`, `Stroke`, `HeartDiseaseorAttack`,  
     `Age`, `Sex`, `Education`, `Diabetes`.  
   - Diadaptasi dari penelitian berbasis DHI/BRFSS terdahulu.

3. **Engineered Features (berbasis domain knowledge)**  
   - `BMI_Category` → Underweight, Normal, Overweight, Obese (one-hot).  
   - `Health_Risk_Score` → skor komposit faktor risiko (HighBP, HighChol, Smoker, Stroke,  
     HeartDiseaseorAttack, DiffWalk, GenHlth, (1 − PhysActivity)).  
   - `HighBP_HighChol_Interaction` → interaksi hipertensi & kolesterol tinggi.

**Ringkasan temuan penting:**

- **Voting Classifier + Engineered Features** → F1 berbobot terbaik **0,8240**.  
- **LR, RF** → sangat terbantu oleh **Engineered Features**.  
- **NB, DT** → lebih cocok dengan **Fixed Feature Selection** (lebih sederhana, minim noise).  
- **XGBoost, AdaBoost** → performa optimal justru di **Baseline** (mampu belajar non-linear dari fitur mentah).

---

### Skenario 2 – Teknik Sampling (Imbalanced Data)

Menggunakan konfigurasi **terbaik tiap model dari Skenario 1**, lalu menerapkan:

- **Tanpa sampling** (baseline)  
- **RandomOverSampler (ROS)**  
- **SMOTE**  
- **ADASYN**  
- **RandomUnderSampler (RUS)**  

**Highlight:**

- Mayoritas model (**LR, NB, DT, AdaBoost, Voting**) → **tanpa sampling** sudah memberi F1 terbaik.  
- **Random Forest** → sedikit membaik dengan **ROS** (F1 naik ~0,0046).  
- **XGBoost** → sedikit membaik dengan **SMOTE** (F1 naik ~0,0030).  

---

### Skenario 3 – Feature Scaling

Menguji efek **scaling** pada konfigurasi terbaik Skenario 2:

- **Tanpa scaling**  
- **MinMaxScaler**  
- **StandardScaler**

**Ringkasan:**

- **Logistic Regression**: F1 berbobot naik tipis dengan **MinMaxScaler** (selisih sangat kecil).  
- **Voting Classifier**: kombinasi terbaik → **Engineered Features + tanpa sampling + MinMaxScaler**  
  dengan F1 berbobot **0,8241**.  
- **DT, RF, XGBoost, AdaBoost** → nyaris **tidak terpengaruh** scaling.  

---

## Hasil Utama (Singkat)

- **Model terbaik**:  
  - **Voting Classifier + Engineered Features + MinMaxScaler + tanpa sampling**  
  - **Weighted F1-score ≈ 0,8241**  
---
