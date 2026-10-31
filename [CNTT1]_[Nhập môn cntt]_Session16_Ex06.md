# Thiết lập Môi trường Làm việc Số cho Doanh nghiệp

## ***Phần 1: Cấu trúc Thư mục và Hybrid Workflow (Chức năng 1)

Để đảm bảo code cảm biến không bị đè mất và an toàn khi làm việc nhóm, nhóm áp dụng mô hình Hybrid Workflow (kết hợp lưu trữ cục bộ trên máy cá nhân và lưu trữ đám mây qua GitHub/GitLab, kết hợp Google Drive cho tài liệu chung).

***1. Cấu trúc thư mục dự án (Repository)***

Green-Tech-SmartFarm/
│
├── .github/workflows/      # CI/CD tự động kiểm tra lỗi code cảm biến
├── config/
│   └── .env.example        # File mẫu chứa cấu trúc biến môi trường (KHÔNG chứa pass thật)
│   └── .env                # File mật khẩu thật (ĐÃ ĐƯA VÀO .gitignore, chỉ có trên máy local và server)
├── sensors/                # Mã nguồn thu thập dữ liệu cảm biến (Nhiệt độ, độ ẩm, đất...)
│   ├── dht11.py
│   └── soil_moisture.py
├── docs/                   # Tài liệu kỹ thuật, sơ đồ mạch
├── reports/                # Báo cáo tiến độ cho nhà đầu tư
├── .gitignore              # Khai báo loại bỏ file nhạy cảm (.env, __pycache__, node_modules)
├── README.md               # Tài liệu hướng dẫn sử dụng dự án
└── requirements.txt        # Thư viện Python cần thiết

***2. Quy tắc Hybrid Workflow cho Đội Dev***

- Local First: Mỗi thành viên code trên máy cá nhân, kiểm thử bằng thư mục ảo riêng trên ổ D:\ để tránh ảnh hưởng hệ thống.

- Branching Strategy (Phân nhánh Git):

  - main: Nhánh chính, code luôn chạy ổn định, chỉ Trưởng dự án mới có quyền Merge vào đây.

  - dev: Nhánh tích hợp chung của toàn đội kỹ thuật.

  - feature/sensors-update: Nhánh riêng của từng lập trình viên khi viết code cảm biến mới.

- Bảo mật file .env: Tuyệt đối không commit file .env chứa mật khẩu cơ sở dữ liệu và API cảm biến lên Git. Team dùng file .env.example để làm mẫu, ai chạy code tự tạo file .env cá nhân dựa trên hướng dẫn trong README.  

## ***Phần 2 : Phân quyền và Chia sẻ tài nguyên (Chức năng 2)***

Hệ thống tài nguyên được phân chia rõ ràng trên nền tảng Cloud (Google Drive / Notion / GitHub) để đảm bảo tính bảo mật và đúng đối tượng:

**Nhà đầu tư (Investors)**

- Thư mục reports/ trên Cloud

- Viewer 

- Theo dõi tiến độ tài chính, báo cáo hiệu quả năng suất trang trại hàng tháng, không thể chỉnh sửa hay xóa file.

**Đối tác thiết bị (Vendors)**

- Thư mục tài liệu kỹ thuật cảm biến

- Commentator (Được bình luận/góp ý)

- Góp ý trực tiếp vào thông số kỹ thuật phần cứng, sơ đồ chân cắm cảm biến mà không làm hỏng tài liệu gốc.

**Đội ngũ Kỹ thuật (Dev Team)**

- GitHub Repository + Workspace

- Admin / Maintainer

- Toàn quyền clone, push code, tạo pull request và quản lý Task.

***Chính sách bàn giao dự án***

**Khi kết thúc học phần hoặc bàn giao sản phẩm thực tế cho khách hàng, Trưởng dự án sẽ thực hiện tính năng Transfer Ownership trên GitHub và phân quyền Owner trên Cloud Workspace cho đại diện nhà đầu tư hoặc giảng viên hướng dẫn.**

**Tài khoản cá nhân của sinh viên sẽ được hạ xuống mức Member hoặc thu hồi quyền truy cập hoàn toàn sau khi biên bản bàn giao được ký kết.**

## ***Phần 3: Quy trình Giao tiếp và Quản lý Task (Chức năng 3)***

Mọi vấn đề phát sinh trong quá trình vận hành cảm biến đều được chuẩn hóa thành công việc cụ thể trên công cụ Enterprise Workspace (Slack / Microsoft Teams kết hợp Trello/Jira):

***1. Phát hiện lỗi: Thành viên test phần cứng phát hiện lỗi cảm biến độ ẩm đất gửi sai dữ liệu lên kênh chat chung #dev-sensors.***

***2. Tạo Task tự động: Trưởng nhóm hoặc Bot tích hợp sử dụng lệnh chuyển đổi tin nhắn lỗi thành Task:***

- Tiêu đề Task: [Fix Bug] Cảm biến độ ẩm đất pin số 4 gửi giá trị rỗng

- Mô tả: Lỗi kết nối chân GPIO 21, dữ liệu trả về None liên tục từ 14:00 chiều nay.

- Người thực hiện (Assignee): Nguyễn Văn A (Phụ trách phần cứng).

- Deadline: 18:00 ngày hôm nay.

## ***Phần 4: Kiểm thử & Kịch bản Khôi phục sự cố (Test & Recovery)

***Tình huống giả định:***

Trong quá trình thao tác nhanh, một thành viên lỡ tay xóa sạch thư mục sensors/ trên máy cá nhân và đã vô tình thực hiện lệnh git add . và git commit -m "delete sensors" nhưng chưa push lên remote repository trên GitHub.

***Quy trình khôi phục nhanh nhất***

Bước 1: Kiểm tra lịch sử commit gần nhất bằng Git Reflog
Mở terminal tại thư mục dự án và chạy lệnh:

**Git bash: git reflog**

Bước 2: Khôi phục lại trạng thái trước khi xóa
Chạy lệnh khôi phục trỏ HEAD về lại commit an toàn:

**Git bash: git reset --hard a1b2c3d**

Bước 3: Trường hợp chưa commit mà lỡ xóa nhầm (Xóa trực tiếp trong Workspace):
Nếu chưa chạy lệnh commit nào mà chỉ xóa thẳng tay, dùng ngay lệnh:

**Git bash: git restore sensors/**