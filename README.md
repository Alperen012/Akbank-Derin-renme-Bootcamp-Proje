# American Sign Language (ASL) Classification Project

## Proje Amacı

Bu proje, Amerikan İşaret Dili (ASL) harflerini ve rakamlarını görüntü işleme ve derin öğrenme teknikleri kullanarak otomatik olarak sınıflandırmayı amaçlamaktadır. Proje, işitme engelli bireylerin iletişimini kolaylaştırmak ve ASL öğrenimini desteklemek için geliştirilmiştir.

## Veri Seti Hakkında Bilgi

### Genel Özellikler
- **Toplam Görüntü Sayısı**: 2,515 adet
- **Görüntü Boyutu**: 400x400 piksel
- **Sınıf Sayısı**: 36 (0-9 rakamları + A-Z harfleri)
- **Format**: RGB renkli görüntüler

### Sınıf Dağılımı
Veri seti 36 farklı sınıftan oluşmaktadır:
- **Rakamlar**: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
- **Harfler**: A, B, C, D, E, F, G, H, I, J, K, L, M, N, O, P, Q, R, S, T, U, V, W, X, Y, Z

### Veri Bölünmesi
- **Eğitim Seti**: %80
- **Doğrulama Seti**: %10
- **Test Seti**: %10

## Kullanılan Yöntemler

### 1. Veri Ön İşleme
- **Normalizasyon**: Piksel değerleri 0-1 arasına ölçeklendirildi (`rescale=1./255`)
- **Veri Artırma (Data Augmentation)**:
  - Zoom aralığı: %10 (`zoom_range=0.1`)
  - Parlaklık değişimi: 0.8-1.2 arasında (`brightness_range=[0.8,1.2]`)
  - Yatay çevirme devre dışı (ASL'de yön önemli olduğu için)

### 2. Model Mimarileri

#### A. Özel CNN Modeli
```
- 3 Konvolüsyonel Blok
- Her blokta 2 Conv2D katmanı + MaxPooling + Dropout
- 2 Tam Bağlantılı Katman
- Softmax çıktı katmanı (36 sınıf)
```

#### B. Transfer Learning (MobileNetV2)
- ImageNet üzerinde ön eğitimli MobileNetV2 kullanıldı
- Özel sınıflandırma başlığı eklendi
- Global Average Pooling + Dense katmanları

### 3. Hiperparametre Optimizasyonu
**Keras Tuner** ile otomatik hiperparametre arama:
- **Optimizasyon Parametreleri**:
  - Filtre sayıları (16-256 arası)
  - Çekirdek boyutları (3x3, 5x5)
  - Dropout oranları (0.1-0.6 arası)
  - Yoğun katman nöron sayıları (64-1024 arası)
  - Öğrenme oranı (1e-5 - 1e-2 arası)
  - Optimizasyon algoritması (Adam, RMSprop, SGD)

- **Arama Stratejisi**: Random Search
- **Deneme Sayısı**: 15 farklı konfigürasyon
- **Değerlendirme Metriği**: Validation Accuracy

### 4. Model Eğitimi
- **Callback Fonksiyonları**:
  - `EarlyStopping`: Overfitting'i önlemek için
  - `ReduceLROnPlateau`: Öğrenme oranını dinamik ayarlama
- **Epoch Sayısı**: 15-30 arası
- **Batch Size**: 16-128 arası (hiperparametre optimizasyonu ile belirlendi)

### 5. Model Açıklanabilirliği
- **Grad-CAM**: Modelin hangi bölgelere odaklandığını görselleştirme
- **Eigen-CAM**: Alternatif görselleştirme yöntemi
- Model kararlarının şeffaf ve anlaşılır hale getirilmesi

## Elde Edilen Sonuçlar

### Model Performansı
- **Test Accuracy**: [Koddan elde edilen sonuçlar buraya eklenecek]
- **Validation Accuracy**: Hiperparametre optimizasyonu ile en iyi sonuç elde edildi

### Sınıf Bazında Performans
- Confusion Matrix ile detaylı sınıf performansları analiz edildi
- En iyi performans gösteren sınıflar belirlendi
- Karışıklığa sebep olan sınıf çiftleri tespit edildi

### Görselleştirme Sonuçları
- Grad-CAM ve Eigen-CAM ile model kararları görselleştirildi
- Modelin işaret dilindeki kritik bölgeleri doğru şekilde algıladığı gözlemlendi

### Model Karşılaştırması
1. **Özel CNN Modeli**: Sıfırdan eğitilen, ASL'ye özel mimarı
2. **Transfer Learning**: MobileNetV2 tabanlı, daha hızlı eğitim
3. **Hiperparametre Optimizasyonlu Model**: En iyi performans

## Teknik Özellikler

### Kullanılan Teknolojiler
- **Framework**: TensorFlow/Keras
- **Görselleştirme**: Matplotlib, Seaborn
- **Veri İşleme**: NumPy, Pandas, OpenCV
- **Hiperparametre Optimizasyonu**: Keras Tuner
- **Model Açıklanabilirliği**: Grad-CAM, Eigen-CAM

### Geliştirme Ortamı
- **Platform**: Kaggle Notebooks
- **GPU**: CUDA destekli grafik kartı kullanımı
- **Memory Management**: GPU bellek optimizasyonu

## Gelecek Geliştirmeler

1. **Real-time Detection**: Webcam ile gerçek zamanlı ASL tanıma
2. **Mobile Application**: Mobil cihazlarda çalışan uygulama geliştirme
3. **Video Sequence**: Hareket dizilerinin tanınması
4. **Multi-language Support**: Farklı işaret dilleri desteği

## Kullanım

### Gereksinimler
```bash
tensorflow>=2.8.0
keras-tuner
opencv-python
matplotlib
seaborn
scikit-learn
splitfolders
```

### Model Yükleme ve Tahmin
```python
import tensorflow as tf

# Modeli yükle
model = tf.keras.models.load_model('best_asl_model.h5')

# Tahmin yap
prediction = model.predict(image_array)
predicted_class = np.argmax(prediction)
```



## Lisans

Bu proje akademik amaçlarla geliştirilmiştir.

---

**Kaggle Notebook Linki**: [ASL Classification Project] https://www.kaggle.com/code/pypy123/akbank-bootcamp
