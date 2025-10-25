# 🎮 Game_Multi_DauTien

Dự án game nhiều người chơi Unity sử dụng Photon Fusion.

## 📋 Mô Tả Dự Án

**Game_Multi_DauTien** là một game nhiều người chơi được phát triển trên Unity 3D, sử dụng Photon Fusion để xử lý mạng. Dự án này bao gồm các tính năng nhiều người chơi cơ bản như quản lý người chơi, hệ thống spawn, và đồng bộ hóa thời gian thực.

## ⚡ Tính Năng Chính

- ✅ **Mạng Nhiều Người Chơi**: Tích hợp Photon Fusion
- ✅ **Quản Lý Người Chơi**: Hệ thống vào/ra với NetworkDictionary
- ✅ **Hệ Thống Spawn**: Tự động spawn người chơi với các điểm spawn
- ✅ **Hệ Thống Máu**: Đồng bộ máu qua mạng và hồi sinh
- ✅ **Dự Đoán Input**: Bù trễ và gameplay mượt mà
- ✅ **Đa Nền Tảng**: Hỗ trợ PC với khả năng mở rộng mobile

## 🛠️ Công Nghệ Sử Dụng

- **Engine**: Unity 2022.3 LTS
- **Mạng**: Photon Fusion
- **Render**: Universal Render Pipeline (URP)
- **Input**: Unity Input System
- **Nền tảng**: Windows PC (Chính)

## 📁 Cấu Trúc Code Chính

```
Assets/Scripts/
└── Game/
    └── Gameplay.cs       # Controller gameplay chính
```

## 🚀 Cài Đặt & Thiết Lập

### Yêu Cầu Hệ Thống
- **Unity**: 2022.3 LTS hoặc mới hơn
- **Hệ điều hành**: Windows 10/11
- **RAM**: 8GB tối thiểu, 16GB khuyến nghị
- **Ổ cứng**: 2GB dung lượng trống

### Các Bước Cài Đặt

1. **Sao chép Repository**
   ```bash
   git clone https://github.com/conghieeu/Game_Multi_DauTien.git
   cd Game_Multi_DauTien
   ```

2. **Mở Dự Án trong Unity**
   - Mở Unity Hub
   - Nhấn "Open" và chọn thư mục dự án
   - Đợi Unity nhập assets

3. **Thiết lập Photon Fusion**
   - Tạo tài khoản Photon tại [Photon Engine](https://www.photonengine.com/)
   - Lấy App ID từ Photon Dashboard
   - Dán App ID vào cài đặt Photon trong Unity

4. **Cài Đặt Build**
   - File → Build Settings
   - Chọn nền tảng (PC, Mac & Linux Standalone)
   - Nhấn "Build and Run"

## 🎮 Cách Chơi

### Kiểm Thử Một Người
1. Build game từ Unity
2. Chạy file thực thi
3. Chọn "Host" để tạo phòng
4. Kiểm tra cơ chế gameplay

### Kiểm Thử Nhiều Người  
1. Build game với Development Build được bật
2. Chạy nhiều phiên bản:
   - Phiên bản 1: Chọn "Host" 
   - Phiên bản 2+: Chọn "Join" và nhập tên phòng
3. Kiểm tra tính năng mạng

## 🏗️ Kiến Trúc Code

### Lớp Chính

#### `Gameplay.cs`
- **Mục đích**: Controller gameplay chính
- **Trách nhiệm**:
  - Quản lý người chơi vào/ra
  - Sinh/hủy Agent
  - Hệ thống hồi sinh
  - Đồng bộ trạng thái mạng

#### Kiến Trúc Mạng
```
NetworkRunner
└── Gameplay (NetworkBehaviour)
    ├── Players (NetworkDictionary<PlayerRef, Player>)
    ├── Quản lý SpawnPoints
    └── Quản lý Vòng đời Agent
```

### Hệ Thống Chính

1. **Quản Lý Người Chơi**
   - NetworkDictionary lưu trữ người chơi theo PlayerRef
   - Tự động dọn dẹp khi người chơi ngắt kết nối

2. **Hệ Thống Spawn**
   - Chọn điểm spawn theo vòng tròn
   - Độ trễ hồi sinh: 3 giây
   - Bảo vệ spawn: 3 giây bất tử

3. **Hệ Thống Máu**
   - Giá trị máu đồng bộ qua mạng
   - Xử lý sự kiện FatalHitTaken
   - Tự động kích hoạt hồi sinh

## 🔧 Quy Trình Phát Triển

### Chiến Lược Nhánh Git
- `main`: Code sản xuất
- `develop`: Nhánh tích hợp
- `feature/*`: Tính năng mới
- `bugfix/*`: Sửa lỗi

### Quy Trình Kiểm Thử
1. **Kiểm Thử Cục Bộ**: Chức năng một người chơi
2. **Kiểm Thử Mạng**: Nhiều client trên cùng máy
3. **Kiểm Thử LAN**: Nhiều thiết bị
4. **Kiểm Thử Hiệu Năng**: Profiling với Unity Profiler

## 📈 Chỉ Số Hiệu Năng

- **Tốc Độ Tick**: 60 Hz
- **Người Chơi Tối Đa**: 200 đồng thời
- **Network RTT**: < 50ms tối ưu
- **Tốc Độ Khung Hình**: 60 FPS mục tiêu

## 🐛 Xử Lý Sự Cố

### Lỗi Thường Gặp

1. **Kết Nối Thất Bại**
   - Kiểm tra Photon App ID
   - Xác minh kết nối internet
   - Kiểm tra cài đặt firewall

2. **Lỗi Đồng Bộ**
   - Khởi động lại Unity
   - Xóa thư mục Library
   - Nhập lại Photon assets

3. **Lỗi Build**
   - Cập nhật Unity lên LTS mới nhất
   - Xóa thư mục Temp
   - Kiểm tra cài đặt nền tảng

### Công Cụ Debug
- **Fusion Statistics**: Thống kê mạng thời gian thực
- **Unity Console**: Ghi log lỗi
- **Photon Dashboard**: Giám sát kết nối

## 🤝 Đóng Góp

### Hướng Dẫn Phát Triển
1. Tuân thủ chuẩn coding C#
2. Viết commit message rõ ràng
3. Kiểm thử trước khi push
4. Cập nhật tài liệu

### Quy Trình Pull Request
1. Tạo nhánh tính năng từ `develop`
2. Triển khai thay đổi với kiểm thử
3. Gửi PR với mô tả
4. Code review và phê duyệt
5. Merge vào `develop`

## 📞 Liên Hệ & Hỗ Trợ

- **Lập trình viên**: conghieeu
- **Repository**: [Game_Multi_DauTien](https://github.com/conghieeu/Game_Multi_DauTien)
- **Báo lỗi**: Tab GitHub Issues
- **Tài liệu**: [Quy Trình Game & Kiến Trúc](GAME_WORKFLOW_ARCHITECTURE.md)

## 📄 Giấy Phép

Dự án này được phát triển cho mục đích học tập và nghiên cứu.

## 🔄 Lịch Sử Phiên Bản

- **v1.0.0**: Thiết lập multiplayer ban đầu
- **v0.9.0**: Tích hợp Photon Fusion
- **v0.8.0**: Framework mạng cơ bản

---

*Cập nhật lần cuối: Tháng 10 năm 2025*