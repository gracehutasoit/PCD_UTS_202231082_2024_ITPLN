## Pertama : 
import cv2 : untuk mengimpor pustaka OpenCV 
import matplotlib.pyplot as plt: mengimpor modul pyplot dari pustaka Matplotlib dengan alias plt.
import numpy as np : mengimpor pustaka NumPy dengan alias np. 
## POTRET KAMERA
img1 = cv2.imread('Grace.jpg') : membaca gambar dengan nama file 'Grace.jpg' menggunakan fungsi cv2.imread() dari pustaka OpenCV. Gambar tersebut akan disimpan dalam variabel img1.

img2 = cv2.imread('rincian.jpg') : membaca gambar dengan nama file 'rincian.jpg' dan menyimpannya dalam variabel img2.

fig, axes = plt.subplots(1, 2, figsize=(10, 10)) : membuat sebuah figure Matplotlib dan dua subplot (1 baris, 2 kolom) menggunakan plt.subplots() dengan figsize=(10, 10).

axes[0].imshow(cv2.cvtColor(img1, cv2.COLOR_BGR2RGB)) : ntuk menampilkan gambar pertama (img1) pada subplot pertama (axes[0]). 
axes[0].set_title("Grace") : menambahkan judul "Grace" ke subplot pertama.

axes[1].imshow(cv2.cvtColor(img2, cv2.COLOR_BGR2RGB)) : untuk menampilkan gambar kedua (img2) pada subplot kedua (axes[1]).
axes[1].set_title("Rincian") : menambahkan judul "Rincian" ke subplot kedua.

plt.show() : untuk menampilkan gambar.

## DETEKSI WARNA PADA CITRA
# Membaca gambar
img = cv2.imread('Grace.jpg')
hsv_img = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

# Menentukan rentang nilai HSV untuk warna biru, merah, dan hijau
lower_blue = np.array([90, 50, 50])
upper_blue = np.array([160, 255, 255])

# Merah dapat berada di dua rentang, jadi kita gabungkan kedua mask
lower_red1 = np.array([0, 100, 100])
upper_red1 = np.array([10, 255, 255])
lower_red2 = np.array([90, 50, 50])
upper_red2 = np.array([1140, 255, 255])

lower_green = np.array([10, 70, 5])  # Menurunkan nilai Hue agar lebih mendekati hijau
upper_green = np.array([180, 255, 255])

# Membuat mask untuk setiap rentang warna
mask_blue = cv2.inRange(hsv_img, lower_blue, upper_blue)
mask_red = cv2.inRange(hsv_img, lower_red1, upper_red1) | cv2.inRange(hsv_img, lower_red2, upper_red2)
mask_green = cv2.inRange(hsv_img, lower_green, upper_green)

# Menerapkan mask pada gambar asli untuk mendeteksi area berwarna
blue_detected = cv2.bitwise_and(img, img, mask=mask_blue)
red_detected = cv2.bitwise_and(img, img, mask=mask_red)
green_detected = cv2.bitwise_and(img, img, mask=mask_green)

# Mengubah latar belakang yang terdeteksi menjadi putih
background_white = np.full_like(img, (255, 255, 255), dtype=np.uint8)
background_white[mask_blue != 0] = [0, 0, 0]
background_white[mask_red != 0] = [0, 0, 0]
background_white[mask_green != 0] = [0, 0, 0]

# Menggabungkan hasil deteksi warna dengan latar belakang putih
blue_detected_with_white_bg = cv2.bitwise_or(blue_detected, background_white)
red_detected_with_white_bg = cv2.bitwise_or(red_detected, background_white)
green_detected_with_white_bg = cv2.bitwise_or(green_detected, background_white)

# Membuat gambar asli dengan hanya menampilkan warna hijau, merah, dan biru
img_with_selected_colors = cv2.bitwise_or(blue_detected_with_white_bg, 
                                           cv2.bitwise_or(red_detected_with_white_bg, green_detected_with_white_bg))

# Ubah warna merah menjadi putih keabu-abuan
red_mask = cv2.inRange(red_detected_with_white_bg, (0, 0, 0), (0, 0, 0))
red_detected_with_white_bg[red_mask != 0] = [200, 200, 255]

# Membuat subplot dan menampilkan gambar
fig, axes = plt.subplots(2, 2, figsize=(10, 10))

axes[0, 0].imshow(cv2.cvtColor(img_with_selected_colors, cv2.COLOR_BGR2RGB))
axes[0, 0].set_title('CITRA KONTRAS')

axes[0, 1].imshow(cv2.cvtColor(blue_detected_with_white_bg, cv2.COLOR_BGR2RGB))
axes[0, 1].set_title('BIRU')

axes[1, 1].imshow(cv2.cvtColor(green_detected_with_white_bg, cv2.COLOR_BGR2RGB))
axes[1, 1].set_title('MERAH')

axes[1, 0].imshow(cv2.cvtColor(red_detected_with_white_bg, cv2.COLOR_BGR2RGB))
axes[1, 0].set_title('HIJAU')

# Tampilkan plot
plt.tight_layout()
plt.show()

# Tutup semua jendela OpenCV
cv2.destroyAllWindows()

## HISTOGRAM GAMBAR ASLI
# Cara 1
fig, axs = plt.subplots(2, 2, figsize=(15, 5)) : membuat sebuah figure Matplotlib dengan 2 baris dan 2 kolom, sehingga akan ada empat subplot dalam total. Ukuran figure ditentukan sebagai (15, 5).
axs[0, 0].imshow(img) : menampilkan gambar (img) pada subplot pertama di bagian kiri atas.
axs[0, 1].hist(img.ravel(), 256, [0, 256]) : menampilkan histogram dari gambar (img) pada subplot kedua di bagian kanan atas. Fungsi hist() digunakan untuk menghitung dan menampilkan histogram. Parameter pertama (img.ravel()) adalah nilai piksel gambar yang di-ravel menjadi satu dimensi, parameter kedua (256) adalah jumlah bin, dan parameter ketiga ([0, 256]) adalah rentang nilai yang akan dihitung dalam histogram.

# Cara 2
hist = cv2.calcHist([img], [0], None, [256], [0, 256]) : menghitung histogram dari gambar (img) menggunakan fungsi cv2.calcHist(). 
axs[1, 0].imshow(img) : menampilkan gambar (img) pada subplot ketiga di bagian kiri bawah.
axs[1, 1].plot(hist) : menampilkan plot histogram yang telah dihitung pada subplot keempat di bagian kanan bawah.
plt.show()

## CARILAH DAN URUTKAN AMBANG BATAS TERKECIL SAMAPAI DENGAN TERBESAR
# Membaca gambar
img = cv2.imread('Grace.jpg')
hsv_img = cv2.cvtColor(img, cv2.COLOR_BGR2HSV) :  mengubah ruang warna dari BGR (Blue-Green-Red) ke HSV (Hue-Saturation-Value) menggunakan cv2.cvtColor(). Hasilnya disimpan dalam variabel hsv_img.

# Menentukan rentang nilai HSV untuk warna biru, merah, dan hijau
lower_blue = np.array([110, 50, 50]) : membuat sebuah array NumPy yang disebut lower_blue. Array ini berisi tiga nilai: [110, 50, 50]. Nilai pertama (110) adalah nilai H (Hue) minimum yang akan kita deteksi untuk warna biru. Nilai kedua (50) adalah nilai S (Saturation) minimum, dan nilai ketiga (50) adalah nilai V (Value) minimum. Ini adalah nilai-nilai yang digunakan sebagai batas bawah (lower bound) untuk mendeteksi warna biru dalam model warna HSV.

upper_blue = np.array([140, 255, 255]) : membuat array NumPy yang disebut upper_blue. Array ini juga berisi tiga nilai: [140, 255, 255]. Nilai pertama (140) adalah nilai H (Hue) maksimum yang akan kita deteksi untuk warna biru. Nilai kedua (255) adalah nilai S (Saturation) maksimum, dan nilai ketiga (255) adalah nilai V (Value) maksimum. Ini adalah nilai-nilai yang digunakan sebagai batas atas (upper bound) untuk mendeteksi warna biru dalam model warna HSV.

# Merah dapat berada di dua rentang, jadi kita gabungkan kedua mask
lower_red1 = np.array([0, 100, 100]) : sebuah array NumPy yang disebut lower_red1. Array ini berisi tiga nilai: [0, 100, 100]. Nilai pertama (0) adalah nilai H (Hue) minimum yang akan kita deteksi untuk warna merah. Nilai kedua (100) adalah nilai S (Saturation) minimum, dan nilai ketiga (100) adalah nilai V (Value) minimum. Rentang ini mencakup warna merah yang terdapat di sekitar bagian merah murni pada model warna HSV.
upper_red1 = np.array([10, 255, 255]) : membuat array NumPy yang disebut upper_red1. Array ini juga terdiri dari tiga nilai: [10, 255, 255]. Nilai pertama (10) adalah nilai H (Hue) maksimum yang akan kita deteksi untuk warna merah. Nilai kedua (255) adalah nilai S (Saturation) maksimum, dan nilai ketiga (255) adalah nilai V (Value) maksimum. Rentang ini juga mencakup warna merah yang terletak di sekitar bagian merah murni pada model warna HSV.
lower_red2 = np.array([110, 50, 50]) : membuat array NumPy yang disebut lower_red2. Array ini memiliki tiga nilai: [110, 50, 50]. Nilai pertama (110) adalah nilai H (Hue) minimum yang akan kita deteksi untuk warna merah. Nilai kedua (50) adalah nilai S (Saturation) minimum, dan nilai ketiga (50) adalah nilai V (Value) minimum. Rentang ini juga mencakup warna merah yang terletak di sekitar bagian merah murni pada model warna HSV, tetapi dalam rentang yang sedikit berbeda.
upper_red2 = np.array([1140, 255, 255]) : array NumPy yang disebut upper_red2. Array ini juga terdiri dari tiga nilai: [1140, 255, 255]. Nilai pertama (1140) adalah nilai H (Hue) maksimum yang akan kita deteksi untuk warna merah. Nilai kedua (255) adalah nilai S (Saturation) maksimum, dan nilai ketiga (255) adalah nilai V (Value) maksimum. Rentang ini juga mencakup warna merah yang terletak di sekitar bagian merah murni pada model warna HSV, tetapi dalam rentang yang sedikit berbeda.

lower_green = np.array([0, 70, 5]) : sebuah array NumPy disebut lower_green. Array ini berisi tiga nilai: [0, 70, 5]. Nilai pertama (0) adalah nilai H (Hue) minimum yang akan kita deteksi untuk warna hijau. Nilai kedua (70) adalah nilai S (Saturation) minimum, dan nilai ketiga (5) adalah nilai V (Value) minimum. Rentang ini mencakup warna hijau yang memiliki tingkat kecerahan yang rendah (Value yang rendah), sehingga mungkin mencakup warna hijau yang lebih gelap.

# Menurunkan nilai Hue agar lebih mendekati hijau
upper_green = np.array([1140, 255, 255]) : membuat array NumPy yang disebut upper_green. Array ini terdiri dari tiga nilai: [1140, 255, 255]. Nilai pertama (1140) adalah nilai H (Hue) maksimum yang akan kita deteksi untuk warna hijau. Nilai kedua (255) adalah nilai S (Saturation) maksimum, dan nilai ketiga (255) adalah nilai V (Value) maksimum. Rentang ini mencakup warna hijau yang paling murni dengan nilai kecerahan (Value) yang tinggi.

# Membuat mask untuk setiap rentang warna
mask_blue = cv2.inRange(hsv_img, lower_blue, upper_blue) : cv2.inRange() digunakan untuk membuat maska mask_blue. 
mask_red = cv2.inRange(hsv_img, lower_red1, upper_red1) | cv2.inRange(hsv_img, lower_red2, upper_red2) : membuat maska untuk warna merah. Karena warna merah bisa berada pada dua rentang nilai H yang berbeda dalam model warna HSV (dekat 0 dan dekat 180), kita membuat dua maska untuk setiap rentang dan kemudian menggabungkannya menggunakan operator logika OR (|). Hasilnya adalah maska mask_red yang mencakup piksel yang sesuai dengan rentang warna merah yang ditentukan oleh lower_red1, upper_red1, lower_red2, dan upper_red2.
mask_green = cv2.inRange(hsv_img, lower_green, upper_green) : membuat maska untuk warna hijau berdasarkan rentang nilai lower_green dan upper_green. Hasilnya adalah maska mask_green yang menunjukkan di mana piksel dalam gambar berada dalam rentang warna hijau yang ditentukan.

# Menerapkan mask pada gambar asli untuk mendeteksi area berwarna
blue_detected = cv2.bitwise_and(img, img, mask=mask_blue)
red_detected = cv2.bitwise_and(img, img, mask=mask_red)
green_detected = cv2.bitwise_and(img, img, mask=mask_green)

# Mengubah warna yang terdeteksi menjadi putih
blue_detected[np.where((blue_detected != [0,0,0]).all(axis=2))] = [255,255,255]
red_detected[np.where((red_detected != [0,0,0]).all(axis=2))] = [255,255,255]
green_detected[np.where((green_detected != [0,0,255]).all(axis=2))] = [255,255,255]

# Membuat saluran warna hitam
black_img = np.zeros_like(img)

# Membuat subplot dan menampilkan gambar
fig, axes = plt.subplots(2, 2, figsize=(10, 10))

axes[0, 0].imshow(cv2.cvtColor(black_img, cv2.COLOR_BGR2RGB))
axes[0, 0].set_title('none')

axes[0, 1].imshow(cv2.cvtColor(blue_detected, cv2.COLOR_BGR2RGB))
axes[0, 1].set_title('blue')

axes[1, 0].imshow(cv2.cvtColor(red_detected, cv2.COLOR_BGR2RGB))
axes[1, 0].set_title('red-blue')

axes[1, 1].imshow(cv2.cvtColor(green_detected, cv2.COLOR_BGR2RGB))
axes[1, 1].set_title('red-green-blue')

# Tampilkan plot
plt.tight_layout()
plt.show()

# Tutup semua jendela OpenCV
cv2.destroyAllWindows()



