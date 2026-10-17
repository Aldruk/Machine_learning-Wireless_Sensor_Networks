
# WSN-DS: Deteksi Serangan pada Wireless Sensor Network

Proyek machine learning untuk menganalisis dan memprediksi jenis serangan (Attack type) pada jaringan sensor nirkabel menggunakan dataset **WSN-DS**.

## Deskripsi Dataset

WSN-DS (Wireless Sensor Network Dataset) berisi data simulasi komunikasi antar node sensor dalam arsitektur clustering. Dataset ini digunakan untuk deteksi serangan pada Wireless Sensor Network.
```
| Informasi       | Nilai                     |
|-----------------|---------------------------|
| Jumlah baris    | 374.661                   |
| Jumlah fitur    | 19                        |
| Target          | Attack type (5 kelas)     |
| Kelas mayoritas | Normal (340.066 data)     |
```
### Daftar Fitur
```
| Fitur             | Deskripsi                                         |
|-------------------|---------------------------------------------------|
| id                | Identitas unik node                               |
| Time              | Waktu simulasi                                    |
| Is_CH             | Apakah node adalah Cluster Head (1/0)             |
| who CH            | ID Cluster Head yang diikuti                      |
| Dist_To_CH        | Jarak ke Cluster Head                             |
| ADV_S / ADV_R     | Jumlah pesan Advertise yang dikirim / diterima    |
| JOIN_S / JOIN_R   | Jumlah pesan Join yang dikirim / diterima         |
| SCH_S / SCH_R     | Jumlah pesan Schedule yang dikirim / diterima     |
| Rank              | Peringkat node dalam cluster                      |
| DATA_S / DATA_R   | Jumlah data yang dikirim / diterima               |
| Data_Sent_To_BS   | Jumlah data yang dikirim ke Base Station          |
| dist_CH_To_BS     | Jarak Cluster Head ke Base Station                |
| send_code         | Kode pengiriman                                   |
| Expaned Energy    | Energi yang digunakan                             |
| Attack type       | Label target (Normal, Blackhole, Grayhole, Flooding, TDMA) |
```
## Alur Pengerjaan

### 1. Data Loading
- Membaca file `WSN-DS.csv` menggunakan Pandas.

### 2. Data Cleaning
- Pemeriksaan missing values (tidak ditemukan nilai hilang).
- Identifikasi dan penanganan outlier menggunakan metode **IQR**.
- Penghapusan baris duplikat.
- Standardisasi fitur numerik dengan `StandardScaler`.

### 3. Feature Engineering
- Encoding fitur kategorikal menggunakan `LabelEncoder`.
- Visualisasi distribusi data (histogram, box plot).
- Analisis korelasi antar fitur dengan heatmap.

### 4. Modelling
Dataset dibagi menjadi **80% training** dan **20% testing** (`random_state=1`).

Tiga model regresi dilatih:
```
| Model                       | Algoritma                            |
|-----------------------------|--------------------------------------|
| LARS                        | Least Angle Regression               |
| Linear Regression           | Ordinary Least Squares               |
| Gradient Boosting Regressor | Ensemble boosting (random_state=184) |
```

### 5. Evaluasi Model
```
| Model                      | MAE      | MSE      | R²       |
|----------------------------|----------|----------|----------|
| Lars                       | 0.003127 | 0.001681 | 0.000079 |
| Linear Regression          | 0.003219 | 0.001680 | 0.000468 |
| GradientBoostingRegressor  | 0.003178 | 0.001644 | 0.021970 |
```
> Model terbaik: **Gradient Boosting Regressor** (R² tertinggi dan MSE terendah).

### 6. Deployment
- Model GBR disimpan dalam format `joblib` (`gbr_model.joblib`) dan `pickle` (`gbr_model.pkl`).
- API prediksi sederhana dibuat menggunakan **Flask** pada endpoint `/predict` (POST).

## Struktur File

```
├── WSN-DS.csv              # Dataset utama
├── dataset_code.ipynb      # Notebook lengkap (EDA + Modelling)
├── gbr_model.joblib        # Model Gradient Boosting (joblib)
├── gbr_model.pkl           # Model Gradient Boosting (pickle)
└── README.md               # Dokumentasi proyek
```

## Cara Menjalankan

### Prerequisites
```bash
pip install pandas numpy scikit-learn matplotlib seaborn joblib flask
```

### Menjalankan Notebook
1. Buka `dataset_code.ipynb` di Jupyter Notebook / VS Code / Google Colab.
2. Pastikan file `WSN-DS.csv` berada di direktori yang sama.
3. Jalankan semua cell secara berurutan.

### Menjalankan API Flask
```python

from flask import Flask, request, jsonify
import joblib

app = Flask(__name__)
model = joblib.load('gbr_model.joblib')

@app.route('/predict', methods=['POST'])
def predict():
    data = request.json['data']
    prediction = model.predict(data)
    return jsonify({'prediction': prediction.tolist()})

if __name__ == '__main__':
    app.run(debug=False, use_reloader=False)

```

## Teknologi yang Digunakan

- Python 3.13
- Pandas – manipulasi data
- NumPy – komputasi numerik
- Scikit-learn – preprocessing, modelling, evaluasi
- Matplotlib & Seaborn – visualisasi
- Joblib / Pickle – serialisasi model
- Flask – deployment API

## Catatan

- Target `Attack type` di-encode menjadi numerik, sehingga model regresi digunakan.
- Untuk performa klasifikasi yang lebih baik, disarankan mencoba algoritma klasifikasi (Random Forest, XGBoost, SVM, dll.) dengan metrik Accuracy, Precision, Recall, dan F1-Score.
- Dataset bersifat imbalanced (mayoritas Normal), teknik balancing seperti SMOTE dapat dipertimbangkan.

## Lisensi
Proyek ini dibuat untuk keperluan pembelajaran dan penelitian.
