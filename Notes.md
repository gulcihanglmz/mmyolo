# COCO JSON Dönüştürme ve Görselleştirme Süreci (MMYOLO için)

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

## 👁️‍🗨️ 2. Adım: COCO JSON Dosyasını Görselleştirme

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

## Notlar

- Komutlar PowerShell veya terminalde `.venv` aktifken çalıştırılmalıdır.
- Eğer `browse_coco_json.py` çalışmazsa, gerekli Python kütüphanelerinin (örneğin `pycocotools`, `matplotlib`, `opencv-python`) yüklü olduğuna emin olun.
- `show_coco_json.py` yerine `browse_coco_json.py` kullanılması önerilir çünkü MMYOLO içinde yerleşik olarak bu araç yönlendirilmiştir.

---
