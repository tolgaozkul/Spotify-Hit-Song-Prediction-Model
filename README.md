# 🎵 Spotify Hit Şarkı Tahmin Modeli
### Uçtan Uca Makine Öğrenimi Projesi

> **Bir şarkının hit olup olmayacağını önceden tahmin edebilir miyiz?**  
> Bu proje, Spotify ses özellikleri ve metadata bilgilerini kullanarak bu soruyu yanıtlamaya çalışan eksiksiz bir ML pipeline'ı sunmaktadır.

---

## 📌 Genel Bakış

`spotify_songs.csv` veri seti (32.833 şarkı, 23 özellik) kullanılarak bir Spotify şarkısının **"Hit"** olup olmayacağı (popülerlik sıralamasında üst %25) tahmin edilmektedir. Ham veriden model yorumlamaya kadar eksiksiz bir veri bilimi akışı uygulanmıştır.

**Öne çıkan metodolojik kararlar:**
- Hit eşiği keyfi değil, **temiz veri üzerinden istatistiksel olarak** belirlendi (Q3 = 58)
- Veri sızıntısını önlemek için Target Encoding **train/test bölmesinden sonra** uygulandı
- Özellik mühendisliği **ablation study ile doğrulandı**, kör ekleme yapılmadı
- Model yalnızca accuracy ile değil; **F1, ROC-AUC ve PR-AUC** ile değerlendirildi

---

## 📊 Veri Seti

| Özellik | Değer |
|---|---|
| Kaynak | Spotify API (2020) |
| Ham boyut | 32.833 satır × 23 sütun |
| Temizlik sonrası | 28.350 benzersiz şarkı |
| Hedef değişken | `is_hit` = 1 → `track_popularity ≥ 58` |
| Sınıf dengesi | ~%74 Non-Hit / ~%26 Hit |

---

## 🔁 Pipeline

```
Ham Veri
   └─ Temizlik             (eksik değer, duplike, duration filtresi)
        └─ EDA              (dağılım, korelasyon, tür & zamansal analiz)
             └─ Özellik Mühendisliği   (tarih özellikleri, ikili flagler + ablation study)
                  └─ Train/Test Split   (stratified, 80/20)
                       └─ Target Encoding    (sadece train'den fit)
                            └─ One-Hot Encoding + StandardScaler
                                 └─ XGBoost Sınıflandırıcı
                                      └─ Değerlendirme + Yorumlama
```

---

## 🛠️ Özellik Mühendisliği

| Özellik | Kaynak | Tür |
|---|---|---|
| `release_year`, `release_month`, `song_age` | `track_album_release_date` | Türetilmiş — tarih |
| `release_season` | `release_month` | Türetilmiş — kategorik |
| `is_collaboration` | `track_artist` | Türetilmiş — ikili |
| `is_remix` | `track_name` | Türetilmiş — ikili |
| `track_artist_te`, `playlist_genre_te`, `playlist_subgenre_te` | Target Encoding | Kodlanmış |

> Ablation study sonuçlarına göre tarih özellikleri en yüksek artışı sağladı (+0.0115 F1).

---

## 📈 Model Sonuçları

| Metrik | Skor |
|---|---|
| **F1-Skoru (Hit)** | ~0.72 |
| **ROC-AUC** | ~0.83 |
| **PR-AUC** | ~0.67 |
| Precision (Hit) | ~0.74 |
| Recall (Hit) | ~0.70 |

---

## 🔍 Temel Bulgular

- **Sanatçı marka gücü** en belirleyici faktör — `track_artist_te` feature importance'ı domine ediyor
- Ses özellikleri ile popülerlik arasındaki **korelasyonlar oldukça zayıf** (max ~0.09)
- `is_remix = 1` olan şarkılar beklenin aksine **daha düşük hit oranı** gösteriyor
- `pop` türü en yüksek (%38.2), `edm` en düşük (%9.3) hit oranına sahip
- `release_year` ses özelliklerinden daha önemli çıktı — Spotify'ın **recency bias'ını** yansıtıyor

---

## ⚠️ Limitasyonlar

- Spotify popülerlik skoru güncel dinlenmelere ağırlık verdiğinden **recency bias** içeriyor
- Veri seti 2020 yılına ait — müzik trendleri değiştiğinden model güncel veriyle test edilmeli
- Target Encoding sanatçı odaklı bir model üretiyor; **yeni ve tanınmayan sanatçılar** için tahmin gücü sınırlı

---

## 🚀 Kullanılan Teknolojiler

![Python](https://img.shields.io/badge/Python-3.10-blue)
![XGBoost](https://img.shields.io/badge/XGBoost-1.7-orange)
![Pandas](https://img.shields.io/badge/Pandas-2.0-lightblue)
![Seaborn](https://img.shields.io/badge/Seaborn-0.12-green)
![Scikit--learn](https://img.shields.io/badge/Scikit--learn-1.3-red)

---

## 📁 Proje Yapısı

```
Spotify-Hit-Song-Prediction-Model/
│
├── spotify_songs.csv                     # Ham veri seti
├── spotify-hit-prediction-model.ipynb   # Ana notebook
└── README.md
```

---

## 📓 Kaggle Notebook

Projenin tam notebook versiyonuna aşağıdaki linkten ulaşabilirsiniz:  
🔗 *[Kaggle linki yakında eklenecek]*
