# Phân Đoạn Ảnh Nội Soi Dạ Dày Sử Dụng Mô Hình U-Net

## Giới thiệu

Dự án này xây dựng mô hình học sâu U-Net nhằm tự động phân đoạn vùng polyp trong ảnh nội soi tiêu hóa sử dụng bộ dữ liệu Kvasir-SEG.

Mô hình nhận ảnh nội soi đầu vào dưới dạng ảnh xám (grayscale) và dự đoán mặt nạ (mask) nhị phân thể hiện vị trí của polyp.
( Polyp là một khối mô bất thường phát triển từ lớp niêm mạc bên trong các cơ quan rỗng của cơ thể.)

Dự án được triển khai bằng TensorFlow/Keras và có thể huấn luyện trên Google Colab, Kaggle hoặc máy tính cá nhân có GPU.

---

## Bộ dữ liệu

### Kvasir-SEG Dataset

Kvasir-SEG là bộ dữ liệu chuẩn dùng trong bài toán phân đoạn polyp đường tiêu hóa.

Thông tin bộ dữ liệu:

* 1000 ảnh nội soi có chứa polyp
* 1000 ảnh mặt nạ (mask) tương ứng
* Ảnh có độ phân giải cao
* Phù hợp cho các bài toán Semantic Segmentation

Tải bộ dữ liệu tại:

https://datasets.simula.no/kvasir-seg/

Cấu trúc thư mục:

```text
Kvasir-SEG/
├── images/
├── masks/
└── kvasir_bboxes.json
```

---

## Mục tiêu

* Xây dựng mô hình U-Net để phân đoạn vùng polyp.
* Tự động tạo mặt nạ từ ảnh nội soi.
* Đánh giá hiệu quả của mạng U-Net trên bộ dữ liệu Kvasir-SEG.

---

## Tiền xử lý dữ liệu

Các bước tiền xử lý:

1. Đọc ảnh dưới dạng ảnh xám (Grayscale).
2. Thay đổi kích thước ảnh về 256 × 256.
3. Chuẩn hóa giá trị pixel về khoảng [0,1].
4. Thêm chiều kênh dữ liệu thành (256,256,1).
5. Chia dữ liệu thành:

| Tập dữ liệu | Tỷ lệ |
| ----------- | ----- |
| Train       | 60%   |
| Validation  | 20%   |
| Test        | 20%   |

---

## Kiến trúc mô hình U-Net

Mô hình bao gồm ba phần chính:

### Encoder (Downsampling)

* Conv Block 64 filters
* Conv Block 128 filters
* Conv Block 256 filters
* Conv Block 512 filters

Mỗi Conv Block gồm:

* Conv2D
* Batch Normalization
* ReLU
* Conv2D
* Batch Normalization
* ReLU

Sau mỗi block sử dụng:

```text
MaxPooling2D(2×2)
```

để giảm kích thước đặc trưng.

---

### Bottleneck

Lớp trung gian:

```text
Conv Block 1024 filters
```

Giúp trích xuất đặc trưng ở mức sâu nhất.

---

### Decoder (Upsampling)

Decoder gồm:

* Conv2DTranspose
* Skip Connection
* Conv Block

Skip Connection giúp giữ lại thông tin chi tiết từ Encoder.

---

### Lớp đầu ra

```text
Conv2D (1×1)
Activation: Sigmoid
```

Đầu ra:

```text
256 × 256 × 1
```

Thể hiện xác suất mỗi pixel thuộc vùng polyp.

---

## Cấu hình huấn luyện

| Tham số       | Giá trị             |
| ------------- | ------------------- |
| Optimizer     | Adam                |
| Learning Rate | 0.0001              |
| Loss Function | Binary Crossentropy |
| Epochs        | 20                  |
| Batch Size    | 3                   |
| Input Size    | 256 × 256 × 1       |

Mô hình sử dụng:

```text
ModelCheckpoint
```

để lưu lại mô hình có giá trị Validation Loss tốt nhất.

---

## Cài đặt môi trường

Clone repository:

```bash
git clone https://github.com/dungcodepy/Stomach-Endoscopy-Image-Segmentation-Using-U-Net.git
cd Stomach-Endoscopy-Image-Segmentation-Using-U-Net
```

Cài đặt thư viện:

```bash
pip install tensorflow
pip install numpy
pip install matplotlib
pip install opencv-python
pip install scikit-learn
```

Hoặc:

```bash
pip install -r requirements.txt
```

---

## Huấn luyện mô hình

Mở notebook:

```text
unet_proces_kav.ipynb
```

Sau đó chạy toàn bộ các cell.

Quá trình huấn luyện bao gồm:

1. Đọc dữ liệu.
2. Tiền xử lý dữ liệu.
3. Xây dựng mô hình U-Net.
4. Huấn luyện mô hình.
5. Lưu mô hình tốt nhất.

---

## Đánh giá mô hình

Mô hình được đánh giá trên tập Test bằng các chỉ số:

* Loss
* Accuracy

Đồng thời hiển thị các biểu đồ:

* Training Loss
* Validation Loss
* Training Accuracy
* Validation Accuracy

---

## Kết quả dự đoán

Sau khi huấn luyện, mô hình sẽ hiển thị:

1. Ảnh nội soi đầu vào.
2. Ground Truth Mask.
3. Predicted Mask.

Quy trình:

```text
Ảnh nội soi
      ↓
    U-Net
      ↓
Mặt nạ dự đoán
```

---

## Hướng phát triển

Một số cải tiến có thể thực hiện:

* Sử dụng Dice Loss.
* Sử dụng IoU Metric.
* Data Augmentation.
* Attention U-Net.
* ResUNet.
* DeepLabV3+.
* Sử dụng ảnh RGB thay vì Grayscale.
* Tăng số Epoch và Batch Size khi có GPU mạnh hơn.

---

## Công nghệ sử dụng

* Python
* TensorFlow
* Keras
* OpenCV
* NumPy
* Matplotlib
* Scikit-Learn

---

## Tác giả

**Nguyễn Hữu Dũng**

Đề tài: **Phân đoạn ảnh nội soi dạ dày sử dụng mô hình U-Net**

GitHub:

https://github.com/dungcodepy
