# Analisis Point Pluvial Flood (GeoJSON) + WebGIS Interaktif

Mini-project ini berisi notebook Python untuk eksplorasi data titik _pluvial flood_ (banjir akibat hujan/limpasan permukaan) berbasis GeoJSON, ditambah output peta interaktif (Folium/Leaflet) yang bisa dibuka lewat browser.

> File utama:
>
> - Notebook analisis: `analysisPointPluvialFlood.ipynb`
> - Dataset: `data_point_pluvial_flood_dataset.geojson`
> - Output peta: `WebGIS Yohanes Gerardus Haga Zai_assigment4.html`

---

## Daftar Isi

- [Tujuan](#tujuan)
- [Apa itu Pluvial Flood?](#apa-itu-pluvial-flood)
- [Data & Parameter](#data--parameter)
- [Alur Analisis di Notebook](#alur-analisis-di-notebook)
- [Cara Menjalankan](#cara-menjalankan)
- [Cara Membuka WebGIS](#cara-membuka-webgis)
- [Interpretasi Output (Singkat)](#interpretasi-output-singkat)
- [Catatan & Tips](#catatan--tips)

---

## Tujuan

- Membaca data spasial GeoJSON menggunakan GeoPandas.
- Membersihkan atribut numerik (contoh: nilai _NoData_/outlier pada slope).
- Melakukan EDA (histogram, boxplot, scatter, heatmap korelasi).
- Membuat peta interaktif untuk membandingkan titik dengan kategori kerentanan berbeda.

---

## Apa itu Pluvial Flood?

_Pluvial flood_ adalah banjir yang terjadi ketika intensitas hujan tinggi menyebabkan air menggenang/berlimpah di permukaan (runoff), seringkali **tanpa harus menunggu sungai meluap**. Faktor yang biasanya memengaruhi:

- Curah hujan (Rainfall)
- Kemiringan lereng (Slope)
- Kondisi drainase (Drainage)
- Kelembapan/akumulasi aliran di permukaan (misalnya TWI)

<details>
<summary><strong>Kenapa parameter-parameter ini relevan?</strong></summary>

- **Slope**: lereng lebih curam → aliran lebih cepat, potensi erosi/limpasan meningkat; tetapi genangan bisa terjadi di area datar (tergantung konteks).
- **Rainfall**: hujan lebih tinggi umumnya menaikkan peluang runoff.
- **Drainage**: dapat menjadi indikator kerapatan/kemampuan mengalirkan air.
- **TWI** (_Topographic Wetness Index_): sering dipakai untuk indikasi area yang cenderung “basah”/akumulasi air.

</details>

---

## Data & Parameter

Dataset: `data_point_pluvial_flood_dataset.geojson`

Kolom yang dipakai di notebook (contoh yang sering muncul):

- **X, Y**: koordinat titik
- **SUSCEP**: kategori _susceptibility_ / kerentanan (mis. `No_Flood`, `Very_High`, dll.)
- **Slope**: kemiringan lereng (derajat)
- **Rainfall**: curah hujan
- **TWI**: indeks kelembapan topografi
- **Drainage**: indikator drainase
- (Tambahan) **Aspect**, **Curvature**, **FA**

<details>
<summary><strong>Struktur data GeoJSON itu apa?</strong></summary>

GeoJSON umumnya berisi:

- `geometry`: bentuk spasial (di sini: `Point`)
- `properties`: atribut/kolom tabel

GeoPandas akan memuatnya menjadi GeoDataFrame dengan kolom `geometry`.

</details>

---

## Alur Analisis di Notebook

Notebook `analysisPointPluvialFlood.ipynb` kurang lebih melakukan:

1. **Import library**: pandas, geopandas, folium, matplotlib, seaborn, numpy, shapely.
2. **Load GeoJSON** ke GeoDataFrame.
3. **Eksplorasi struktur**: `info()` dan `describe()`.
4. **Distribusi parameter**: histogram slope dan rainfall.
5. **Analisis kategori**: menghitung jumlah tiap `SUSCEP`.
6. **Relasi slope vs rainfall**: scatter plot.
7. **Korelasi**: heatmap untuk kolom numerik.
8. **Peta interaktif**: sampling titik dengan kondisi tertentu dan mem-plot pada Folium.

<details>
<summary><strong>Bagian “cleaning” slope: tujuannya apa?</strong></summary>

Pada data turunan raster, kadang ada nilai ekstrem (mis. ~`1e10`) yang bukan nilai sebenarnya (_NoData_). Notebook:

- mengubah `Slope` ke numerik aman (`errors='coerce'`)
- menghapus nilai ekstrem → `NaN`
- `dropna()` agar analisis visual tidak rusak
- `clip(0, 90)` karena slope derajat biasanya di rentang itu

</details>

---

## Cara Menjalankan

### Opsi A — Jalankan via Jupyter/VS Code (direkomendasikan)

1. Buka `analysisPointPluvialFlood.ipynb`
2. Pilih Python environment (venv/conda) di VS Code
3. Jalankan cell dari atas ke bawah

### Opsi B — Instal dependensi (kalau modul belum ada)

Jalankan di terminal:

```bash
pip install pandas geopandas folium matplotlib seaborn numpy shapely
```

Catatan: instalasi `geopandas` di Windows kadang lebih mudah via conda:

```bash
conda install -c conda-forge geopandas
```

---

## Cara Membuka WebGIS

Output peta interaktif tersimpan di:

- `WebGIS Yohanes Gerardus Haga Zai_assigment4.html`

Cara buka:

1. Klik dua kali file HTML tersebut, atau
2. Klik kanan → Open With → browser (Chrome/Edge/Firefox)

Di peta:

- Klik marker untuk melihat popup atribut (Slope, TWI, Drainage, Rainfall).
- Arahkan kursor pada layer GeoJSON untuk tooltip.

---

## Interpretasi Output (Singkat)

Panduan membaca grafik yang ada di notebook:

- **Histogram**: melihat sebaran nilai (apakah miring ke kanan/kiri, banyak outlier, rentang dominan).
- **Boxplot per `SUSCEP`**: membandingkan distribusi Rainfall antar kategori kerentanan.
- **Scatter Slope vs Rainfall**: mengecek pola hubungan; kalau membentuk tren → ada indikasi korelasi.
- **Heatmap korelasi**: melihat hubungan linear antar parameter numerik (nilai mendekati 1/-1 lebih kuat).

<details>
<summary><strong>Catatan penting tentang “korelasi”</strong></summary>

Korelasi tidak otomatis berarti sebab-akibat. Bisa jadi:

- ada variabel lain yang belum dimodelkan
- relasi tidak linear
- pengaruh spasial (autokorelasi) membuat pola “terlihat kuat”

</details>

---

## Catatan & Tips

- Jika histogram slope keluar `nan` atau plot kosong, biasanya karena banyak nilai non-numerik / _NoData_ → jalankan cell pembersihan slope terlebih dahulu.
- Untuk peta yang lebih informatif, kamu bisa:
  - mewarnai marker berdasarkan `SUSCEP`
  - menambah legenda
  - menambah basemap lain (mis. CartoDB/ESRI)

---

### Struktur Folder

```text
.
├─ analysisPointPluvialFlood.ipynb
├─ data_point_pluvial_flood_dataset.geojson
├─ WebGIS Yohanes Gerardus Haga Zai_assigment4.html
└─ README.md
```
