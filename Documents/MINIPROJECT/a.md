# MINI PROJECT: HỆ THỐNG QUẢN TRỊ HẠ TẦNG VÀ NHẬT KÝ KỸ THUẬT GREENTECH
## 2. PHÂN TÍCH HỆ THỐNG THEO IPO VÀ DIKW
### 2.1 IPO
| Thành phần | Áp dụng tại Green Tech |
|---|---|
| Input | Log máy chủ, danh mục cấu hình phần cứng, mã linh kiện tại 100 trạm |
| Process | Lọc log theo dòng; chuẩn hóa mã bằng TRIM; đối soát khóa bằng VLOOKUP chính xác; kiểm tra mã trùng hoặc không tồn tại |
| Output | Báo cáo sự cố được nối vào backup, bảng trạm có tên thiết bị và thông số, tài liệu hướng dẫn vận hành |

### 1.2. Mô hình DIKW

- **Data — Dữ liệu:** một dòng log ghi `ERROR 404`, một mã cấu hình có khoảng trắng, một giá trị RAM.
- **Information — Thông tin:** danh sách các dòng sự cố kèm thời điểm và mã trạm; bảng mã đã được làm sạch và tra cứu.
- **Knowledge — Kiến thức:** khi nhiều TIMEOUT cùng xuất hiện ở một tuyến mạng, kỹ thuật viên đối chiếu thời gian và trạm để hình thành giả thuyết về đường truyền. Một dòng log riêng lẻ chưa đủ chứng minh nguyên nhân. 
- **Wisdom — Quyết định:** ưu tiên kiểm tra tuyến mạng ảnh hưởng nhiều trạm, xác nhận nguyên nhân trước khi thay linh kiện, giữ báo cáo làm bằng chứng vận hành.

## 2. Cây thư mục ba cấp và bốn trụ cột

Ba cấp của dữ liệu chính là **gốc dự án → nhóm chức năng → tệp**. Tên thư mục dự án `greentech-system` 

```text
greentech-system/
├── docs/
│   ├── README.md
│   ├── srs-he-thong.docx
│   └── kiem-tra-nghiem-thu.md
├── scripts/
│   ├── auto_report.bat
│   └── auto-report.sh
├── data/
│   ├── log-he-thong.log
│   └── quan-ly-thiet-bi.xlsx
├── backup/
│   └── bao-cao-su-co.txt
├── .env
├── .gitignore
├── .gitattributes
└── README.md
```
Các tệp tạo dữ liệu và kiểm thử bổ sung nằm trong `scripts/`. Nếu có cấu hình CI, `.github/workflows/` tuân theo vị trí bắt buộc của GitHub và không thay thế bốn trụ cột dữ liệu chính.

| Trụ cột | Trách nhiệm | Quy tắc |
|---|---|---|
| docs | Đặc tả và hướng dẫn | Cập nhật cùng phiên bản script và workbook |
| scripts | Tự động hóa | Audit trước khi chạy; không có lệnh xóa dữ liệu |
| data | Đầu vào gốc và sổ thiết bị | Không sửa log đầu vào trong khi tạo báo cáo |
| backup | Báo cáo phục vụ truy vết | Ghi nối từng đợt; giữ lại báo cáo trước đó |

`auto_report.bat` giữ nguyên tên theo yêu cầu đề bài. Tên repository nộp bài là `HCM-KS26-CNTT1_NhapMonCNTT_MiniProject_GreenTech`; khi clone có thể chỉ định thư mục đích `greentech-system` để tuân thủ kebab-case tại máy local.

### 2.1. 

Bash trên macOS hoặc Linux:

```bash
mkdir -p greentech-system/{docs,scripts,data,backup}
```
## 4. Module 2 — Robot lọc log tự động

### 4.1. Quy tắc lọc
Giữ lại các dòng có chứa **CRITICAL** và **ERROR** từ file log 500 MB
Sử dụng lệnh grep trên macOS :
```bash
grep -E "CRITICAL|ERROR" log-he-thong.log

