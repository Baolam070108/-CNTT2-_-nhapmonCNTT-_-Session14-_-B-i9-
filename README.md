# PHẦN 1. PHÂN TÍCH BÀI TOÁN (I/O)

## 1. Xác định đầu vào và đầu ra

- **Đầu vào:**
  - 4 nhóm người dùng: Ban Kỹ thuật, Ban Thiết kế, Ban Kinh doanh và Nhà đầu tư.
  - Các loại tài liệu: mã nguồn, API keys, thiết kế UI, Business Plan, Slide Pitch và thông tin đối tác.
  - Yêu cầu bảo mật, phân quyền, lưu lịch sử phiên bản tối thiểu 30 ngày và thu hồi quyền truy cập.

- **Đầu ra mong đợi:**
  - Cấu trúc thư mục Google Drive rõ ràng, dễ quản lý.
  - Bảng phân quyền cho từng nhóm đối với từng loại tài liệu.
  - Hạn chế rò rỉ dữ liệu, kiểm soát quyền chỉnh sửa và thu hồi quyền khi cần thiết.

## 2. Đề xuất cấu trúc thư mục Google Drive

Tạo một thư mục chính `CampusPay`, bên trong chia thành các thư mục theo từng ban và tài liệu dùng chung.

```text
CampusPay/
├── 01_Ky_Thuat/
│   ├── Source_Code/
│   ├── API_Documentation/
│   └── API_Keys_Restricted/
│
├── 02_Thiet_Ke/
│   ├── UI_Design/
│   └── Graphic_Assets/
│
├── 03_Kinh_Doanh/
│   ├── Business_Plan/
│   └── Partner_Information_Restricted/
│
└── 04_Tai_Lieu_Chung/
    ├── Business_Summary/
    └── Slide_Pitch/
```

**Nguyên tắc phân quyền:**

- Mỗi ban được cấp quyền phù hợp với tài liệu của mình.
- Tài liệu cần phối hợp giữa các ban được chia sẻ riêng cho những người liên quan.
- API keys và thông tin khách hàng phải được giới hạn quyền truy cập, không chia sẻ bằng liên kết công khai.
- Nhà đầu tư chỉ được xem Business Plan và Slide Pitch trong 14 ngày.
- Các thư mục và tài liệu nhạy cảm phải được kiểm tra quyền kế thừa để tránh cấp quyền ngoài ý muốn.

## 3. Các bước thiết lập hệ thống

1. **Tạo thư mục:** Tạo thư mục chính `CampusPay` và các thư mục con theo từng ban.
2. **Đặt tên:** Đặt tên thư mục rõ ràng, thống nhất để dễ tìm kiếm.
3. **Phân quyền:** Cấp Editor cho người cần chỉnh sửa, Viewer cho người chỉ cần xem và hạn chế quyền truy cập tài liệu mật.
4. **Chia sẻ chéo:** Chỉ chia sẻ tài liệu cần thiết cho ban khác, không cấp quyền vào toàn bộ thư mục nếu không cần.
5. **Thiết lập bảo mật:** Đặt API keys và thông tin khách hàng ở chế độ Restricted, không sử dụng Anyone with the link.
6. **Thiết lập lịch sử phiên bản:** Lưu tài liệu trên Google Drive, kiểm tra Version History và giữ lại các phiên bản quan trọng tối thiểu 30 ngày.
7. **Cấp quyền nhà đầu tư:** Chỉ cấp Viewer cho Business Plan và Slide Pitch, thiết lập thời hạn truy cập 14 ngày nếu tài khoản hỗ trợ.
8. **Kiểm tra:** Dùng tài khoản của từng nhóm để kiểm tra quyền truy cập.
9. **Thu hồi và cập nhật:** Khi thành viên nghỉ việc hoặc thay đổi nhiệm vụ, thu hồi quyền và kiểm tra lại các quyền đã chia sẻ.

# PHẦN 2. TRIỂN KHAI VÀ CHỐNG LỖI

## 4. Bảng phân quyền

**Quy ước:**
- **Editor:** Được xem và chỉnh sửa tài liệu.
- **Viewer:** Chỉ được xem tài liệu.
- **No access:** Không được truy cập.
- **Restricted:** Chỉ người được cấp quyền mới có thể truy cập.

| Loại tài liệu | Ban KT | Ban TK | Ban KD | Nhà đầu tư | Ghi chú bảo mật |
|---|---|---|---|---|---|
| Mã nguồn và API keys | Editor | No access | No access | No access | API keys phải Restricted, không chia sẻ công khai. |
| Thiết kế UI và đồ họa | Viewer | Editor | No access | No access | Ban KT chỉ xem thiết kế khi cần phát triển sản phẩm. |
| Business Plan | Viewer | No access | Editor | Viewer | Ban KT chỉ xem, không sửa; nhà đầu tư chỉ được xem trong 14 ngày. |
| Slide Pitch | Viewer | Viewer | Editor | Viewer | Các ban được xem để phối hợp; Ban KD quản lý bản chính. |
| Thông tin đối tác và khách hàng | No access | No access | Editor | No access | Chỉ Ban KD được truy cập; dữ liệu nhạy cảm phải Restricted. |

**Lưu ý:** Bảng trên thể hiện quyền đề xuất cho từng nhóm. Chỉ cấp quyền cho những thành viên thực sự cần sử dụng tài liệu. Đối với API keys, nên sử dụng công cụ quản lý bí mật chuyên dụng thay vì lưu trực tiếp dưới dạng tài liệu thông thường.

## 5. Giải quyết 3 bẫy dữ liệu

### Bẫy 1: Chia sẻ Business Plan cho Ban Kỹ thuật nhưng không cho phép chỉnh sửa

**Quyền phù hợp: Viewer.**

- Cấp quyền Viewer cho thành viên Ban Kỹ thuật cần đọc Business Plan.
- Không cấp Editor vì Ban Kỹ thuật không có nhiệm vụ chỉnh sửa nội dung kinh doanh.
- Chỉ chia sẻ tài liệu cần thiết, không chia sẻ toàn bộ thư mục Kinh doanh.

**Lý do:** Ban Kỹ thuật có thể hiểu mục tiêu dự án để phát triển phần mềm nhưng không thể trực tiếp chỉnh sửa Business Plan trên Google Drive.

### Bẫy 2: Nhà đầu tư muốn tải Slide Pitch về máy để thuyết trình offline

**Giải pháp: Cấp Viewer và cung cấp bản PDF riêng.**

- Xuất Slide Pitch thành file PDF.
- Chia sẻ file PDF cho nhà đầu tư với quyền Viewer.
- Cho phép tải PDF để sử dụng khi không có Internet.
- Giữ bản Google Slides gốc do Ban Kinh doanh quản lý và không cấp Editor cho nhà đầu tư.

**Có cần tắt `Viewers can download` không?**

- Đối với file PDF cần tải về: Không nên tắt tính năng tải xuống nếu muốn nhà đầu tư sử dụng offline.
- Đối với bản Google Slides gốc: Có thể tắt tùy chọn cho phép người xem tải xuống, in và sao chép nếu cần hạn chế sao chép tài liệu.

**Lý do:** PDF thuận tiện cho việc trình chiếu và không cho phép chỉnh sửa nội dung trực tiếp như bản Google Slides gốc. Tuy nhiên, PDF vẫn có thể bị chỉnh sửa bằng công cụ khác nên không bảo đảm chống sao chép tuyệt đối.

### Bẫy 3: Thành viên nghỉ việc và cần thu hồi toàn bộ quyền truy cập

**Thao tác cần làm: Thu hồi quyền truy cập ngay lập tức.**

1. Xóa quyền truy cập Google Drive đã cấp trực tiếp cho thành viên.
2. Nếu thành viên được cấp quyền thông qua Google Group, loại họ khỏi nhóm tương ứng.
3. Kiểm tra các thư mục và tệp đã chia sẻ riêng để thu hồi những quyền còn tồn tại.
4. Nếu sử dụng tài khoản công ty, quản trị viên cần vô hiệu hóa tài khoản khi cần thiết và thu hồi các phiên đăng nhập theo quy trình của tổ chức.
5. Kiểm tra lại quyền truy cập để bảo đảm thành viên không còn truy cập được tài liệu qua các quyền khác.

**Lý do:** Chỉ xóa thành viên khỏi một thư mục không bảo đảm họ mất quyền truy cập vào tất cả tài liệu đã được chia sẻ riêng. Vì vậy, cần kiểm tra quyền trực tiếp, quyền qua nhóm và tài khoản công ty.

#iên không còn tham gia dự án.
