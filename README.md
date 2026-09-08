# PKG Battery – ChargerAppPC Documentation & User Manual

[![Latest Release](https://img.shields.io/github/v/release/hoanv-synaptix/ChargerAppPC-Releases?color=blue&label=Latest%20Release)](https://github.com/hoanv-synaptix/ChargerAppPC-Releases/releases/latest)
[![Platform](https://img.shields.io/badge/Platform-Windows%20x64-blue.svg)](https://microsoft.com/windows)
[![License](https://img.shields.io/badge/License-Proprietary-red.svg)](#bản-quyền--hỗ-trợ)

Tài liệu hướng dẫn sử dụng chính thức của phần mềm **PKG ChargerAppPC** – Công cụ cấu hình chu trình sạc thông minh và giám sát SCADA thời gian thực dành cho hệ thống trạm sạc pin công nghiệp **PKG Battery**.

---

## 📑 Mục lục

1. [Tải về & Cài đặt](#1-tải-về--cài-đặt)
2. [Kết nối trạm sạc với máy tính](#2-kết-nối-trạm-sạc-với-máy-tính)
3. [Hướng dẫn cấu hình chu trình sạc (Charge Cycle Configuration)](#3-hướng-dẫn-cấu-hình-chu-trình-sạc-charge-cycle-configuration)
   - [Giới hạn thông số chung (General Limits)](#31-giới-hạn-thông-số-chung-general-limits)
   - [Giảm dòng theo điện áp Cell (Cell Voltage Stages)](#32-giảm-dòng-theo-điện-áp-cell-cell-voltage-stages)
   - [Giảm dòng theo nhiệt độ Pin (Temperature Derating Stages)](#33-giảm-dòng-theo-nhiệt-độ-pin-temperature-derating-stages)
   - [Giảm dòng theo dung lượng SOC (SOC Stages)](#34-giảm-dòng-theo-dung-lượng-soc-soc-stages)
   - [Bảo vệ nhiệt độ Jack cắm sạc (Jack Temperature Protection)](#35-bảo-vệ-nhiệt-độ-jack-cắm-sạc-jack-temperature-protection)
   - [Cấu hình phần cứng & Mô-đun sạc (Hardware & Module Setup)](#36-cấu-hình-phần-cứng--mô-đun-sạc-hardware--module-setup)
   - [Đọc và Ghi cấu hình vào Flash MCU](#37-đọc-và-ghi-cấu-hình-vào-flash-mcu)
4. [Hướng dẫn giám sát SCADA & Live Telemetry (`MonitorWindow`)](#4-hướng-dẫn-giám-sát-scada--live-telemetry-monitorwindow)
   - [Giám sát mô-đun nguồn (Power Modules)](#41-giám-sát-mô-đun-nguồn-power-modules)
   - [Giám sát hệ thống quản lý pin (BMS Monitor)](#42-giám-sát-hệ-thống-quản-lý-pin-bms-monitor)
   - [Đồ thị sạc thời gian thực (Real-time Profile Chart)](#43-đồ-thị-sạc-thời-gian-thực-real-time-profile-chart)
   - [Nhật ký truyền thông (Traffic Monitor & Log CSV)](#44-nhật-ký-truyền-thông-traffic-monitor--log-csv)
5. [Cơ chế tự động cập nhật (In-App Auto Update)](#5-cơ-chế-tự-động-cập-nhật-in-app-auto-update)
6. [Xử lý sự cố thường gặp (Troubleshooting & FAQs)](#6-xử-lý-sự-cố-thường-gặp-troubleshooting--faqs)
7. [Bản quyền & Hỗ trợ kỹ thuật](#7-bản-quyền--hỗ-trợ-kỹ-thuật)

---

## 1. Tải về & Cài đặt

1. Truy cập trang phát hành: **[Tải ChargerAppPC mới nhất](https://github.com/hoanv-synaptix/ChargerAppPC-Releases/releases/latest)**.
2. Tải về file duy nhất: **`ChargerAppPC.exe`**.
3. **Yêu cầu hệ thống:**
   - Hệ điều hành: Windows 10 hoặc Windows 11 (64-bit).
   - **Không cần cài đặt .NET Framework hay .NET Runtime** (file `.exe` đã được đóng gói độc lập - Self-contained single executable).
   - Cổng USB sẵn có trên máy tính.

---

## 2. Kết nối trạm sạc với máy tính

1. Cắm trực tiếp cáp USB từ máy tính vào cổng USB trên bo điều khiển trạm sạc PKG:
   - Bo mạch điều khiển sử dụng chuẩn giao tiếp **USB CDC (Virtual COM Port)** cắm là nhận (*Plug & Play*).
   - Windows sẽ tự động nhận diện thành một cổng COM ảo, **không cần dùng cáp chuyển đổi USB-to-UART và không cần đấu nối chân TX, RX, GND**.
2. Khởi chạy ứng dụng `ChargerAppPC.exe`.
3. Tại góc trên bên phải màn hình:
   - Chọn cổng **COM** tương ứng của bo sạc từ danh sách (nếu không thấy, bấm nút **Refresh** 🔄).
   - Bấm nút **Connect**.
4. **Các tiến trình tự động diễn ra khi kết nối:**
   - Ứng dụng tự động gửi lệnh đánh thức luồng Debug Stream (`ENTER`).
   - Tự động lấy giờ chuẩn máy tính gửi lệnh đồng bộ đồng hồ thời gian thực (`SET_RTC`) xuống vi điều khiển.
   - Tự động đọc cấu hình sạc hiện tại trong bộ nhớ Flash của MCU và hiển thị lên các ô nhập liệu.
   - Biểu tượng trạng thái chuyển sang **Connected (Xanh lá)**.

---

## 3. Hướng dẫn cấu hình chu trình sạc (Charge Cycle Configuration)

Giao diện chính cho phép kỹ sư cài đặt đầy đủ các giới hạn bảo vệ và thuật toán giảm dòng sạc mịn:

### 3.1. Giới hạn thông số chung (General Limits)
- **Battery Capacity (Ah):** Dung lượng danh định của bộ pin (Ví dụ: `100.0` Ah).
- **IMax (C):** Dòng sạc tối đa cho phép tính theo hệ số C-rate (Ví dụ: `1.0`C tương đương 100A với pack 100Ah).
- **IMin (C):** Dòng sạc tối thiểu để duy trì hoặc dòng cắt sạc khi kết thúc chu trình (Ví dụ: `0.1`C = 10A).
- **IPre (C) & VPre (V):** Dòng sạc và ngưỡng điện áp ở giai đoạn Tiền sạc (Pre-charge) dành cho pin bị cạn sâu.
- **ILow (C) & VLow (V):** Dòng sạc và điện áp ở giai đoạn phục hồi dòng thấp.
- **VMax (V):** Điện áp tối đa của toàn bộ pack pin. Khi chạm ngưỡng này, bộ điều khiển sẽ chuyển sang giai đoạn hấp thụ hoặc ngắt sạc.
- **VMin (V):** Điện áp thấp nhất cho phép sạc. Dưới ngưỡng này hệ thống báo lỗi thấp áp.
- **Temp Limit (°C):** Nhiệt độ tối đa cho phép của khối pin (Ví dụ: `55.0`°C). Nếu vượt quá, MCU sẽ ngắt sạc để bảo đảm an toàn.

### 3.2. Giảm dòng theo điện áp Cell (Cell Voltage Stages)
Bảo vệ chống quá áp từng cell riêng lẻ, tự động hạ dòng sạc khi cell pin bắt đầu đầy:
- **Enabled:** Tích chọn để bật tính năng.
- **Delta V (V):** Biên độ điện áp chuyển tiếp làm mịn bước nhảy dòng, chống dao động điều khiển.
- **Cell Volt 1..5 (V):** 5 ngưỡng điện áp cell tăng dần (Ví dụ: `3.2V`, `3.3V`, `3.4V`, `3.5V`, `3.65V`).
- **Cell Curr 1..4 (C):** Dòng sạc giới hạn tương ứng với từng giai đoạn (Ví dụ: `1.0C`, `0.8C`, `0.5C`, `0.2C`).

### 3.3. Giảm dòng theo nhiệt độ Pin (Temperature Derating Stages)
- Tự động hạ công suất sạc khi pin ấm lên để kéo dài tuổi thọ cell.
- Cài đặt 5 mốc nhiệt độ (`10°C`, `25°C`, `40°C`, `48°C`, `55°C`) và dòng tương ứng.

### 3.4. Giảm dòng theo dung lượng SOC (SOC Stages)
- Cho phép hạ dần dòng sạc theo mức độ nạp đầy (% SOC) của BMS: 20%, 40%, 60%, 80%, 95%.

### 3.5. Bảo vệ nhiệt độ Jack cắm sạc (Jack Temperature Protection)
Bảo vệ đầu sạc tiếp xúc chống quá nhiệt, chảy nhựa hoặc cháy chập tiếp điểm:
- **Enabled:** Bật tính năng đo cảm biến nhiệt độ jack cắm.
- **Threshold (°C):** Ngưỡng nhiệt độ cảnh báo bắt đầu giảm dòng (Mặc định: `60.0`°C).
- **Power Limit (%):** Mức công suất sạc giảm xuống khi chạm ngưỡng cảnh báo (Mặc định: `50.0`%).
- **Delay (s):** Thời gian lọc nhiễu xác nhận quá nhiệt trước khi áp dụng giảm dòng (Mặc định: `5` giây).
- **Trip Temp (°C) [MỚI]:** **Ngưỡng nhiệt độ ngắt sạc khẩn cấp bảo vệ**. Nếu sau khi đã giảm dòng mà nhiệt độ jack tiếp tục tăng vượt ngưỡng này (Mặc định: `75.0`°C - `80.0`°C), bộ điều khiển sẽ lập tức **ngắt hoàn toàn rơ-le sạc**, khóa nút sạc và báo lỗi cảnh báo trên màn hình HMI DWIN.

### 3.6. Cấu hình phần cứng & Mô-đun sạc (Hardware & Module Setup)
- **Charge Source Mode:**
  - `BMS Controlled`: Bộ sạc hoạt động theo yêu cầu dòng/áp do BMS truyền qua CAN bus.
  - `Standalone (No BMS)`: Bộ sạc tự động điều khiển theo các thông số cấu hình nội bộ (dành cho pin không có CAN BMS hoặc ắc quy).
- **Module Type:** Chọn chuẩn mô-đun nguồn: *EVR*, *Maxwell*, *Lianming*, hoặc *TonHe*.
- **Module Count:** Số lượng mô-đun sạc mắc song song trong tủ.
- **Module Limits:** Dải điện áp và dòng điện an toàn cho phép của từng mô-đun ($U_{min}, U_{max}, I_{min}, I_{max}$).
- **Identity & HW Rev:** Tên định danh trạm sạc (Device ID) và phiên bản phần cứng (Hardware Revision).

### 3.7. Đọc và Ghi cấu hình vào Flash MCU
- **Read MCU:** Đọc lại toàn bộ cấu hình từ Flash MCU lên giao diện.
- **Write MCU:** Kiểm tra tính hợp lệ của toàn bộ dữ liệu, đóng gói cấu hình nhị phân 239 Bytes có mã kiểm tra CRC-8 và ghi đè xuống bộ nhớ Flash không bay hơi của MCU.
- **Load / Save File:** Cho phép lưu cấu hình ra file `.json` trên máy tính để dùng lại cho các trạm sạc khác.

---

## 4. Hướng dẫn giám sát SCADA & Live Telemetry (`MonitorWindow`)

Bấm nút **"Live Monitor & SCADA"** trên thanh Menu để mở giao diện trung tâm điều hành:

### 4.1. Giám sát mô-đun nguồn (Power Modules)
- Bảng danh sách hiển thị từng mô-đun: Địa chỉ ID, Driver (TonHe/Maxwell/Lianming), trạng thái Online, Running, điện áp và dòng sạc thực tế.
- Khi nhấp chọn vào một mô-đun, bảng chi tiết bên cạnh sẽ hiển thị:
  - Điện áp 3 pha AC đầu vào ($V_A, V_B, V_C$).
  - Điện áp DC Bus PFC âm và dương.
  - Nhiệt độ tầng biến đổi DC-DC, nhiệt độ PFC, nhiệt độ môi trường.
  - Mã lỗi chi tiết báo về từ mô-đun nguồn qua CAN.

### 4.2. Giám sát hệ thống quản lý pin (BMS Monitor)
- **Điện áp tổng & Dòng pin:** Hiển thị tức thời giá trị đo từ BMS.
- **Chỉ số trạng thái:** % SOC, % SOH, dung lượng còn lại (Ah), dung lượng định mức.
- **Chi tiết Cell:** Điện áp Cell cao nhất, Cell thấp nhất, độ lệch điện áp giữa các cell ($\Delta V_{cell}$).
- **Nhiệt độ Cell:** Nhiệt độ Cell cao nhất, thấp nhất, độ lệch nhiệt độ.
- **Trạng thái tiếp điểm:** Trạng thái đóng/mở của Rơ-le Sạc (Charge Relay) và Rơ-le Xả (Discharge Relay).

### 4.3. Đồ thị sạc thời gian thực (Real-time Profile Chart)
- Đồ thị biểu diễn liên tục:
  - Trục trái (Màu xanh dương): Điện áp sạc (0 – 70V).
  - Trục phải (Màu đỏ): Dòng sạc tổng (0 – 100A).
  - Trục hoành: Thời gian thực theo giây.

### 4.4. Nhật ký truyền thông (Traffic Monitor & Log CSV)
- Ghi nhận chi tiết từng frame giao tiếp UART ở tốc độ 15 FPS:
  - Cột: Thời gian, Loại frame (RX/TX/WARN/ERROR), Mã lệnh ID, Chiều dài DLC, Dữ liệu HEX Payload.
- Nút **Clear:** Xóa sạch nhật ký đang hiển thị.
- Nút **Save:** Xuất toàn bộ nhật ký giao tiếp ra file bảng tính `.csv`.

---

## 5. Cơ chế tự động cập nhật (In-App Auto Update)

Ứng dụng tích hợp hệ thống tự động kiểm tra phiên bản mới thông minh:
1. Mỗi khi khởi động hoặc khi bấm menu **Help $\rightarrow$ Check for Updates**:
   - Ứng dụng sẽ gửi yêu cầu nặc danh đến máy chủ GitHub Release:
     `https://api.github.com/repos/hoanv-synaptix/ChargerAppPC-Releases/releases/latest`
2. Nếu có phiên bản mới hơn phiên bản đang chạy:
   - Xuất hiện hộp thoại **"Có phiên bản mới!"** hiển thị phiên bản hiện tại, phiên bản mới nhất và nội dung ghi chú thay đổi (Changelog).
3. Bấm nút **"Cập nhật ngay"**:
   - Quá trình tải bản cập nhật diễn ra tự động kèm thanh tiến trình %.
   - Sau khi tải xong, ứng dụng sẽ tự khởi động lại vào phiên bản mới nhất.

---

## 6. Xử lý sự cố thường gặp (Troubleshooting & FAQs)

| Vấn đề | Nguyên nhân khả dĩ | Hướng dẫn khắc phục |
|:---|:---|:---|
| **Không thấy cổng COM trong danh sách** | Cáp USB chưa cắm chắc hoặc máy tính chưa nhận thiết bị. | Cắm lại cáp USB, kiểm tra trong *Device Manager (Ports COM & LPT)*, sau đó bấm nút **Refresh 🔄**. |
| **Báo lỗi "Failed to open port"** | Cổng COM đang bị một phần mềm khác chiếm dụng (như Serial Terminal, PuTTY, v.v.). | Tắt các ứng dụng đang mở cổng COM đó rồi thử kết nối lại. |
| **Báo lỗi "Timeout waiting for MCU response"** | 1. Cáp USB chỉ là cáp sạc nguồn (không truyền data).<br>2. Bo MCU chưa được cấp nguồn.<br>3. Sai tốc độ baudrate. | 1. Đổi sang cáp USB truyền dữ liệu (Data Cable).<br>2. Kiểm tra nguồn cấp cho bo vi điều khiển.<br>3. Đảm bảo tốc độ truyền chuẩn là **115200 bps**. |
| **Nhiệt độ Jack nhảy cao bất thường** | Cảm biến NTC jack sạc tiếp xúc kém hoặc đứt dây. | Kiểm tra dây tín hiệu cảm biến nhiệt độ jack cắm về bo MCU. |
| **App bị đơ hoặc Windows báo "Not Responding"** | Phiên bản app cũ bị quá tải hàng đợi hiển thị khi nhận stream tốc độ cao. | Cập nhật lên phiên bản **v1.0.8** hoặc **v1.0.9** trở lên (đã được tối ưu kiến trúc đệm 15 FPS chống nghẽn hoàn toàn). |

---

## 7. Bản quyền & Hỗ trợ kỹ thuật

- Phần mềm và thiết kế hệ thống thuộc bản quyền của **PKG Battery**.
- Bảo lưu mọi quyền. Nghiêm cấm sao chép hoặc trích xuất mã nguồn khi chưa có sự đồng ý bằng văn bản.
- Hỗ trợ kỹ thuật & đóng góp ý kiến: Vui lòng liên hệ bộ phận Kỹ thuật & R&D trạm sạc **PKG Battery**.
