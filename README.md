# 🚦 Red-Light Violation Detection System

**YOLOv8 + PaddleOCR + OpenCV – Real-time Traffic Surveillance**

Hệ thống phát hiện xe vượt đèn đỏ theo thời gian thực, sử dụng các công nghệ thị giác máy tính hiện đại để nhận diện phương tiện, phát hiện trạng thái đèn giao thông, theo dõi hướng di chuyển và nhận diện biển số xe.

## 📚 Mục lục

- [Giới thiệu](#-giới-thiệu)
- [Tính năng chính](#-tính-năng-chính)
- [Cấu trúc thư mục](#-cấu-trúc-thư-mục)
- [Cài đặt môi trường](#-cài-đặt-môi-trường)
- [Chạy chương trình](#️-chạy-chương-trình)
- [Pipeline xử lý](#-pipeline-xử-lý)
- [Cấu trúc log JSON](#-cấu-trúc-log-json)
- [Kết quả kiểm thử](#-kết-quả-kiểm-thử)
- [Hạn chế](#-hạn-chế)
- [Hướng phát triển](#-hướng-phát-triển)

## 📌 Giới thiệu

Dự án được xây dựng nhằm tự động giám sát giao thông và phát hiện các trường hợp xe vượt đèn đỏ. Hệ thống có thể hỗ trợ trong việc quản lý giao thông đô thị, lưu trữ bằng chứng vi phạm và làm nền tảng cho các hệ thống phạt nguội.

Hệ thống hoạt động theo thời gian thực, hỗ trợ xử lý video giao thông, nhận diện phương tiện, nhận diện đèn giao thông và nhận diện biển số xe.

## ✨ Tính năng chính

- 🚗 Nhận diện phương tiện bằng YOLOv8.
- 🚥 Nhận diện trạng thái đèn giao thông: đỏ, vàng, xanh.
- 📍 Tracking phương tiện để xác định hướng di chuyển.
- 🔴 Phát hiện vượt đèn đỏ dựa trên ROI và stop-line.
- 🔎 Nhận diện biển số xe bằng PaddleOCR.
- 🖼 Lưu ảnh crop biển số và ảnh toàn cảnh vi phạm.
- 📄 Xuất log vi phạm dạng JSON.
- 🎥 Xuất video kết quả sau khi xử lý.
- ⚡ Hỗ trợ xử lý real-time, phù hợp triển khai tại giao lộ.

## 📁 Cấu trúc thư mục

```text
Nhom3_Nhan dien bien so xe vuot den do/
│
├── .venv/                                  # Môi trường ảo Python
│
├── app/                                    # Ứng dụng chính
│   ├── __init__.py
│   ├── gui_app.py                          # Giao diện Streamlit
│   ├── process_video.py                    # Pipeline phân tích video
│   └── ui_components.py                    # Component giao diện
│
├── config/
│   └── video_zones.json                    # Cấu hình ROI và stop-line cho từng video
│
├── core/                                   # Các module xử lý chính
│   ├── license_plate_recognition.py        # OCR biển số bằng PaddleOCR
│   ├── traffic_light_detection.py          # Nhận diện đèn giao thông
│   ├── utils.py                            # Hàm hỗ trợ
│   ├── vehicle_detection.py                # Nhận diện phương tiện bằng YOLO
│   └── violation_checker.py                # Kiểm tra logic vượt đèn đỏ
│
├── models/                                 # Thư mục chứa mô hình AI
│   ├── license_plate/                      # Mô hình biển số nếu có
│   ├── traffic_light/                      # Mô hình đèn giao thông
│   ├── vehicle/                            # Mô hình nhận diện phương tiện
│   └── yolov8m.pt                          # File YOLOv8 detect xe
│
├── output/                                 # Thư mục xuất kết quả
│   ├── uploads/                            # Video người dùng upload
│   └── violations/                         # Ảnh crop, ảnh context và log vi phạm
│
├── utils/                                  # Thư mục tiện ích bổ sung
│
├── .gitignore                              # File loại trừ khi push Git
├── HDSD.md                                 # Hướng dẫn sử dụng
├── README.md                               # Tài liệu mô tả dự án
├── requirements.txt                        # Danh sách thư viện Python
└── yolov8m.pt                              # File model YOLOv8
```

## 🚀 Cài đặt môi trường

### 1. Clone project về máy

```bash
git clone https://github.com/BTRM237/red-light-lpr-system.git
cd red-light-lpr-system
```

### 2. Tạo môi trường ảo Python

Khuyến nghị sử dụng Python 3.12.6.

```bash
python -m venv venv
```

### 3. Kích hoạt môi trường ảo

Trên Windows:

```bash
venv\Scripts\activate
```

Trên macOS/Linux:

```bash
source venv/bin/activate
```

### 4. Cài đặt thư viện cần thiết

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

## ▶️ Chạy chương trình

Chạy giao diện Streamlit:

```bash
py -m streamlit run app/gui_app.py
```

Hoặc dùng lệnh:

```bash
python -m streamlit run app/gui_app.py
```

Sau khi chạy thành công, mở trình duyệt và truy cập địa chỉ mà Streamlit hiển thị, thường là:

```text
http://localhost:8501
```

## 🔍 Pipeline xử lý

### 1. Phát hiện phương tiện

Hệ thống sử dụng YOLOv8 để phát hiện các phương tiện trong video.

Các lớp phương tiện được xử lý gồm:

- Car
- Motorcycle
- Bus
- Truck

Bounding box của phương tiện được chuyển về kích thước gốc của video để phục vụ tracking và kiểm tra vi phạm.

### 2. Nhận diện đèn giao thông

Hệ thống kết hợp hai phương pháp:

| Phương pháp | Vai trò |
|---|---|
| YOLO | Phát hiện vị trí đèn giao thông |
| HSV Color Detection | Dự phòng khi YOLO nhận diện thiếu hoặc sai |

Trạng thái đèn được làm mượt bằng kỹ thuật light smoothing để tránh tình trạng nhấp nháy giữa các frame.

### 3. Tracking phương tiện

Tracking được thực hiện dựa trên:

- Tâm bounding box của phương tiện.
- Khoảng cách Euclid giữa các frame.
- Hướng di chuyển của phương tiện.

Các hướng di chuyển được phân loại gồm:

- `up`
- `down`
- `side`
- `idle`

Xe đi ngang hoặc đi sai hướng sẽ được loại bỏ để hạn chế cảnh báo sai.

### 4. Phát hiện vượt đèn đỏ

Một phương tiện được xác định là vi phạm khi thỏa mãn các điều kiện:

```text
Xe nằm trong ROI
AND đèn giao thông đang đỏ
AND xe di chuyển đúng hướng cần kiểm tra
AND xe vượt qua stop-line
```

ROI và stop-line được cấu hình trong file:

```text
config/video_zones.json
```

### 5. Nhận diện biển số xe

Hệ thống sử dụng PaddleOCR để nhận diện biển số.

Quy trình OCR gồm:

```text
Cắt vùng biển số từ bounding box xe
→ Tiền xử lý ảnh
→ Chuyển ảnh sang grayscale
→ Tăng cường độ tương phản
→ Threshold ảnh
→ OCR bằng PaddleOCR
→ Chuẩn hóa biển số Việt Nam
```

Hệ thống có cơ chế retry OCR nhiều lần trên mỗi `track_id` để tăng độ chính xác.

### 6. Lưu kết quả vi phạm

Khi phát hiện vi phạm, hệ thống sẽ lưu:

- Ảnh crop biển số.
- Ảnh toàn cảnh phương tiện vi phạm.
- Thông tin phương tiện.
- Biển số nhận diện được.
- Thời gian vi phạm.
- File log JSON.

Kết quả được lưu tại:

```text
output/violations/<video_name>/
```

Ví dụ:

```text
output/violations/sample_video/
│
├── 3_101523_crop.jpg
├── 3_101523_context.jpg
└── violations.json
```

## 📄 Cấu trúc log JSON

Ví dụ một bản ghi vi phạm:

```json
{
  "video": "sample.mp4",
  "track_id": 3,
  "vehicle_type": "motorcycle",
  "license_plate": "59B123456",
  "province": "HCM",
  "timestamp": "2025-01-20T10:15:23",
  "crop_image": "output/violations/sample/3_101523_crop.jpg",
  "context_image": "output/violations/sample/3_101523_context.jpg"
}
```

## 🧪 Kết quả kiểm thử

| Điều kiện kiểm thử | Kết quả |
|---|---|
| Video ban ngày | Hoạt động tốt |
| Nhiều xe trong frame | Tracking ổn định |
| Biển số rõ | OCR đạt khoảng 85–90% |
| Xe đi ngang | Bỏ qua chính xác |
| Xe đi lùi | Bỏ qua |
| Video mưa hoặc thiếu sáng | Cần cải thiện thêm |

## 📈 Hạn chế

- OCR chưa tốt với biển số quá mờ, quá nhỏ hoặc bị che khuất.
- Cần GPU để xử lý real-time với video FullHD.
- Hiệu suất ban đêm hoặc trong điều kiện mưa chưa tối ưu.
- YOLO đôi khi nhận diện thiếu phương tiện hoặc đèn giao thông.
- Tracking thủ công có thể chưa ổn định trong trường hợp giao thông quá đông.

## 🔮 Hướng phát triển

- Huấn luyện mô hình phát hiện biển số riêng.
- Áp dụng Super Resolution để cải thiện ảnh biển số nhỏ.
- Dùng DeepSORT hoặc ByteTrack thay cho tracking thủ công.
- Xây dựng dashboard giám sát real-time.
- Tích hợp API phạt nguội hoặc VNeID
- Cải thiện khả năng nhận diện trong điều kiện ban đêm và thời tiết xấu.