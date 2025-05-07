# Feature Extraction Color Texture
```python
# Import library
import cv2
import numpy as np
import matplotlib.pyplot as plt
from google.colab import files
from PIL import Image
from io import BytesIO

# Upload gambar
uploaded = files.upload()

# Ambil nama file yang diupload
for file_name in uploaded.keys():
    # Baca gambar dan konversi ke format OpenCV
    img_pil = Image.open(BytesIO(uploaded[file_name]))
    image = cv2.cvtColor(np.array(img_pil), cv2.COLOR_RGB2BGR)

    # Konversi ke HSV
    hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
    h, s, v = cv2.split(hsv)

    # Hitung histogram Hue
    hist_hue = cv2.calcHist([h], [0], None, [180], [0, 180])

    # Jumlah piksel dalam rentang Hue tertentu
    red_range = int(np.sum(hist_hue[0:10]) + np.sum(hist_hue[160:180]))  # merah
    yellow_range = int(np.sum(hist_hue[20:35]))                          # kuning
    green_range = int(np.sum(hist_hue[35:85]))                           # hijau
    total_pixels = h.size

    # Persentase tiap warna
    red_percent = red_range / total_pixels * 100
    yellow_percent = yellow_range / total_pixels * 100
    green_percent = green_range / total_pixels * 100

    # Tabel nilai Hue
    labels = ['Merah (Matang)', 'Kuning (Matang)', 'Hijau (Mentah)']
    values = [red_percent, yellow_percent, green_percent]

    # Klasifikasi buah berdasarkan dominasi warna
    if (red_percent + yellow_percent) > 50:
        status = "Matang"
    elif (red_percent + yellow_percent) > 25:
        status = "Setengah Matang"
    else:
        status = "Belum Matang"

    # Tampilkan gambar
    plt.figure(figsize=(5, 5))
    plt.imshow(cv2.cvtColor(image, cv2.COLOR_BGR2RGB))
    plt.title(f"Gambar Asli (Status: {status})")
    plt.axis('off')
    plt.show()

    # Tampilkan bar chart (sebagai tabel visual)
    plt.figure(figsize=(8, 4))
    bar_colors = ['red', 'gold', 'green']
    plt.bar(labels, values, color=bar_colors)
    plt.ylim(0, 100)
    plt.title('Persentase Warna Berdasarkan Hue')
    plt.ylabel('Persentase (%)')

    # Tambahkan label di atas bar
    for i, v in enumerate(values):
        plt.text(i, v + 1, f"{v:.1f}%", ha='center', fontweight='bold')

    plt.tight_layout()
    plt.show()

    # Cetak ringkasan klasifikasi
    print("=== Ringkasan Hue ===")
    print(f"• Merah (0–10 & 160–180): {red_percent:.2f}%")
    print(f"• Kuning (20–35)       : {yellow_percent:.2f}%")
    print(f"• Hijau  (35–85)       : {green_percent:.2f}%")
    print(f"➤ Status Buah          : {status}")
```
