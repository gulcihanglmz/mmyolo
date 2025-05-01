# YOLO2COCO Dönüştürme ve Görselleştirme Süreci (MMYOLO)

Bu döküman, bir YOLO veri kümesini COCO formatına dönüştürüp `browse_coco_json.py` aracıyla görselleştirmenin adımlarını açıklar.

---

## Klasör Yapısı

```
C:/SH17-dataset/
└── root/
    ├── images/                # Tüm görüntü dosyaları burada yer almalı
    └── annotations/
        └── result.json        # YOLO -> COCO dönüştürülmüş JSON dosyası
```

---

## 1. Adım: YOLO → COCO Formatına Dönüştürme

MMYOLO'nun `dataset_converters` aracını kullanarak YOLO formatındaki etiketleri COCO formatına dönüştürüyoruz:

### Dizin:
```
C:\mmyolo\tools\dataset_converters
```

### Komut:
```bash
python yolo2coco.py
```

### Çıktı:
```
Saving converted results to C:\SH17-dataset\root\annotations/result.json ...
Process finished! Please check at C:\SH17-dataset\root\annotations .
Number of images found: 8099, converted: 8099, and skipped: 0. Total annotation count: 75994.
You can use tools/analysis_tools/browse_coco_json.py to visualize!
```

---

## 2. Adım: COCO JSON Dosyasını Görselleştirme

### Dizin:
```
C:\mmyolo\tools\analysis_tools
```

### Komut:
```bash
python browse_coco_json.py --data-root C:/SH17-dataset/root --img-dir images --ann-file annotations/result.json --wait-time 2 --disp-all --category-names person hand
```

### 🧾 Parametre Açıklamaları:

| Parametre                 | Açıklama |
|--------------------------|----------|
| `--data-root`            | Ana veri klasörü (görüntüler + anotasyonlar burada) |
| `--img-dir`              | Görüntü klasörü adı (data-root içinde) |
| `--ann-file`             | COCO formatındaki JSON dosyası (data-root içinde) |
| `--wait-time`            | Görseller arası bekleme süresi (saniye) |
| `--disp-all`             | Bbox + segmentation gibi tüm anotasyonları göster |
| `--category-names`       | Sadece bu kategorileri göster (örnek: `person`, `hand`) |

---
## 3. Adım: COCO Veri Kümesini Train/Val/Test Olarak Ayırma

COCO formatındaki tek bir `result.json` etiketi dosyasını kullanarak veri kümesini `train`, `val` ve `test` alt kümelerine ayırma sürecini açıklaması

## Veri Yapısı

Split işleminden **önce**:

```
dataset/
├── images/                    # Tüm görseller burada (8099 adet)
│   ├── img1.jpg
│   ├── img2.jpg
│   └── ...
└── annotations/
    └── result.json            # Tüm dataset'in birleşik COCO JSON'u
```

Split işleminden **sonra**:

```
dataset/
├── images/                    # Aynı kalır (tüm görseller tek klasörde)
├── annotations/
│   ├── result.json
│   └── splits/
│       ├── train.json         # 5671 adet görsel
│       ├── val.json           # 1619 adet görsel
│       └── test.json          # 809 adet görsel
```
### Dizin:
```
C:\mmyolo\tools\misc
```
### Komut

```bash
python coco_split.py --json C:/SH17-dataset/root/annotations/result.json --out-dir C:/SH17-dataset/root/annotations/splits --ratios 0.7 0.2 0.1 --shuffle --seed 42
```

### 🔧 Parametre Açıklamaları

| Parametre     | Açıklama |
|---------------|----------|
| `--json`      | COCO formatındaki input JSON dosyasının yolu |
| `--out-dir`   | Çıktıların (train/val/test JSON’larının) kaydedileceği klasör |
| `--ratios`    | Veri seti oranları (örneğin: 70% train, 20% val, 10% test) |
| `--shuffle`   | Görselleri karıştırmak için |
| `--seed`      | Rastgelelik için sabit tohum değeri |

## ✅ Split İşlemi Çıktısı

```text
loading annotations into memory...
Done (t=0.52s)
creating index...
index created!
Split info: ======
Train ratio = 0.7000000000000001, number = 5671
Val ratio = 0.20000000000000004, number = 1619
Test ratio = 0.10000000000000002, number = 809
Set the global seed: 42
shuffle dataset.
Saving json to C:\SH17-dataset\root\annotations\splits\train.json
Saving json to C:\SH17-dataset\root\annotations\splits\val.json
Saving json to C:\SH17-dataset\root\annotations\splits\test.json
All done!
```

## 📌 Notlar

- `images/` klasörü **tek** olmalıdır; görselleri fiziksel olarak `train`, `val`, `test` klasörlerine kopyalamanıza gerek yoktur.
- Her JSON dosyası sadece ilgili görsellerin `image_id` ve `file_name` bilgilerini içerir.

- Komutlar PowerShell veya terminalde `.venv` aktifken çalıştırılmalıdır.
- Eğer `browse_coco_json.py` çalışmazsa, gerekli Python kütüphanelerinin (örneğin `pycocotools`, `matplotlib`, `opencv-python`) yüklü olduğuna emin olun.
- `show_coco_json.py` yerine `browse_coco_json.py` kullanılması önerilir çünkü MMYOLO içinde yerleşik olarak bu araç yönlendirilmiştir.

---

![Figure_13](https://github.com/user-attachments/assets/be29dc5b-a247-4abe-bef7-842586a0728f)

![Figure_11](https://github.com/user-attachments/assets/161bc1be-d81e-43ee-a3d8-b3bef1d8509e)

![Figure_9](https://github.com/user-attachments/assets/0966dbb5-3fef-4a9f-a56d-aeecc16054ba)
