# Kho Đèn PRO V2.3 — Quản trị kho & bán hàng

Web tĩnh chạy trực tiếp trên **GitHub Pages**, không cần cài server. Bản V2.3 tập trung vào việc giữ các sổ dữ liệu liên kết với nhau và tự đối soát khi dữ liệu cũ bị lệch.

## Các phân hệ

- Bán hàng + Phiếu xuất kho (PXK)
- Khách hàng + công nợ khách
- Nhân sự / sales
- Kho hàng + lịch sử nhập + điều chỉnh kho
- Nhà cung cấp + chờ công nợ + công nợ NCC
- Thu / Chi
- Báo cáo doanh thu, giá vốn, chi phí, lợi nhuận
- Kiểm soát dữ liệu + nhật ký thao tác
- Sao lưu / phục hồi JSON
- Đồng bộ Google Sheets khi đã cấu hình Web App URL

## Quy tắc liên kết dữ liệu V2.3

### 1. Đơn bán ↔ Kho
- Cột **Đã xuất** trong Kho được tính lại từ toàn bộ đơn bán, không dùng một bộ đếm độc lập.
- Sửa số lượng đơn: tồn xuất được tính lại tự động.
- Xóa đơn: số lượng xuất được hoàn lại bằng cách đối soát tất cả đơn còn lại.
- Đổi tên mặt hàng trong Kho: tên được đồng bộ sang đơn bán, phiếu nhập, chờ công nợ và công nợ NCC.

### 2. Xuất trước — nhập sau
- Cho phép bán hàng khi chưa nhập kho hoặc tồn không đủ.
- Kho có thể âm và hiển thị `ÂM ... • CHỜ NHẬP`.
- Nếu mặt hàng chưa tồn tại, hệ thống tự tạo bản ghi kho tạm với số nhập bằng 0.
- Khi nhập hàng sau, số nhập tự bù tồn âm.
- Các dòng đơn bán trước có giá vốn bằng 0 được bổ sung giá vốn khi nhập hàng sau.

### 3. Nhập kho ↔ NCC ↔ Công nợ
- Mỗi lần nhập tạo lịch sử nhập và một mã **Chờ công nợ**.
- Khi lập công nợ NCC, mã nhập đã chọn bị loại khỏi danh sách Chờ công nợ để tránh ghi nhận hai lần.
- Xóa phiếu công nợ NCC sẽ trả các mã nhập về Chờ công nợ.
- Thanh toán NCC được đồng bộ sang **Phiếu Chi đối soát**.
- Các phiếu Chi thanh toán NCC không được tính lại như chi phí vận hành trong báo cáo lợi nhuận.

### 4. Công nợ khách ↔ Thu / Chi
- Khoản khách trả cho một đơn được đồng bộ sang **Phiếu Thu đối soát**.
- Tạo Phiếu Thu có mã đơn cũng tạo khoản thanh toán của đơn.
- Sửa thông tin khách, sales hoặc sửa chính đơn hàng sẽ cập nhật thông tin trên Phiếu Thu liên kết.
- Xóa khoản thanh toán sẽ xóa Phiếu Thu liên kết; xóa Phiếu Thu liên kết cũng xóa khoản thanh toán nguồn.
- Tiền khách thanh toán là dòng tiền, **không cộng doanh thu lần hai**.

### 5. Báo cáo
- Doanh thu lấy từ đơn bán.
- Giá vốn lấy từ snapshot giá vốn của từng dòng hàng trong đơn.
- Phiếu Thu khách và Phiếu Chi trả NCC được đánh dấu là **đối soát** và loại khỏi P&L để tránh tính trùng.
- KPI **Tổng nhập** tuân theo đúng khoảng thời gian báo cáo đang chọn.

### 6. Cập nhật danh mục
- Đổi tên khách hàng: đồng bộ sang đơn và Thu/Chi liên quan.
- Đổi tên nhân sự: đồng bộ sang đơn và Thu/Chi liên quan.
- Đổi tên NCC: đồng bộ sang kho, lịch sử nhập, chờ công nợ, công nợ và Phiếu Chi liên quan.
- Không cho xóa NCC/nhân sự/mặt hàng khi đã có chứng từ tham chiếu, nhằm tránh đứt lịch sử.
- Chỉnh trực tiếp số lượng nhập hoặc giá vốn trong bảng Kho được ghi thành **Điều chỉnh kho**, không sửa ngược chứng từ NCC lịch sử.

## Tự đối soát dữ liệu

Trong tab **Báo Cáo → Kiểm soát**, nút **Tự sửa liên kết an toàn** có thể:

- Tính lại số lượng đã xuất từ đơn bán.
- Tính lại tổng tiền và giá vốn đơn nếu bị lệch.
- Phục dựng khách, sales hoặc NCC bị thiếu nhưng đang được chứng từ tham chiếu.
- Loại mã nhập đã lên công nợ khỏi danh sách Chờ công nợ.
- Phục dựng liên kết Thanh toán khách ↔ Phiếu Thu và Thanh toán NCC ↔ Phiếu Chi.
- Nâng cấp Phiếu Thu dữ liệu cũ có mã đơn thành khoản thanh toán liên kết.
- Xóa các Phiếu Thu/Chi liên kết mồ côi.
- Tự chuyển cấu trúc công nợ NCC cũ sang cấu trúc mới khi có thể.

Ngoài ra, lúc khởi động hệ thống cũng chạy một lượt đối soát an toàn để giảm nguy cơ dữ liệu cũ bị lệch.

## Sao lưu / phục hồi

- Nút **Sao Lưu** xuất toàn bộ dữ liệu thành JSON có phiên bản `2.3`.
- Phục hồi chỉ nhận các nhóm dữ liệu hệ thống biết, kiểm tra chúng phải là danh sách, sau đó chạy đối soát liên kết.
- File sao lưu V2.2 / cấu trúc cũ vẫn được hỗ trợ ở mức tương thích dữ liệu hiện có.

Nên sao lưu định kỳ, đặc biệt trước khi thay `index.html` bằng phiên bản mới.

## Chạy trên GitHub Pages

1. Tạo repository GitHub, ví dụ `kho-den-pro`.
2. Upload `index.html` vào thư mục gốc repository.
3. Vào **Settings → Pages**.
4. Chọn **Deploy from a branch** → `main` → `/ (root)` → **Save**.
5. Mở URL GitHub Pages được GitHub cung cấp.

## Google Sheets

Trong `index.html`, tìm:

```js
const GOOGLE_SCRIPT_URL = 'THAY_LINK_WEB_APP_CUA_BAN_VAO_DAY';
```

Thay bằng URL Google Apps Script Web App của bạn. Nếu chưa cấu hình, nút Đồng bộ Sheet sẽ chỉ báo rằng chưa có link và không thay đổi dữ liệu.

## Giới hạn kiến trúc hiện tại

Dữ liệu đang dùng `localStorage`, vì vậy:

- Mỗi trình duyệt / máy có bộ dữ liệu riêng.
- Xóa dữ liệu trình duyệt có thể làm mất dữ liệu nếu chưa sao lưu.
- Không có khóa giao dịch thật giữa nhiều máy.
- Chưa có tài khoản, phân quyền và nhật ký bất biến phía server.

Nếu cần nhiều nhân viên cùng dùng đồng thời, bước tiếp theo nên chuyển lớp dữ liệu sang Supabase/Firebase hoặc backend + database, còn giao diện và quy tắc nghiệp vụ hiện tại có thể giữ lại.

## Kiểm thử trước khi bàn giao V2.3

Đã kiểm thử các luồng chính bằng trình duyệt headless với bộ nhớ thử nghiệm độc lập:

- bán trước nhập sau;
- nhập kho bù tồn âm và backfill giá vốn;
- tạo công nợ NCC và trả lần đầu;
- Thu tiền khách theo mã đơn;
- đổi tên khách / sales / NCC và kiểm tra cascade;
- sửa đơn và giữ snapshot giá vốn;
- xóa đơn và tính lại số đã xuất;
- đổi tên sản phẩm và giữ liên kết chứng từ;
- điều chỉnh kho không sửa ngược số tiền chứng từ NCC;
- chặn xóa NCC đã có chứng từ;
- đối soát dữ liệu hỏng / thiếu danh mục;
- phục dựng liên kết thanh toán ↔ Thu/Chi;
- kiểm tra KPI Tổng nhập theo kỳ báo cáo;
- kiểm tra cú pháp JavaScript bằng Node.
