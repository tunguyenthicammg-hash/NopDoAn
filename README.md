HƯỚNG DẪN SỬ DỤNG
SỬ DỤNG BÀI ĐƯỜNG DẪN SAU
https://nguyenthicamtudh52201699.infinityfreeapp.com/
CÁCH LẤY SOURCE CODE TỪ GITHUB
1. Mở Visual Studio Code mở Terminal.
View -> Terminal
2. Dùng lệnh cd để chuyển đến ổ đĩa mà mình muốn clone bài về.
Ví dụ: cd E:
3. Dùng lệnh git clone  <repository_url>
git clone https://github.com/tunguyenthicammg-hash/NopDoAn.git
4. Sau khi clone bài về mở bài lên bằng cách 
File -> Open Folder -> tìm đến ổ đĩa chứa bài -> Mở bài DoAn 
5. Chạy tiếp các lệnh sau ở terminal
composer install 
npm install
php artisan key:generate
npm run dev
Tạo thêm một Terminal chạy lệnh: php artisan serve
 
Nhấp tổ hợp: Ctrl + click vào đường dẫn [http://127.0.0.1:8000] thì có thể chạy. 
CÁCH ĐĂNG NHẬP
Có thể nhấp vào login để đăng nhập:
 
Sau khi nhấp vào login sẽ hiện ra giao diện để đăng nhập:
 
Tài khoản của admin: admin@stu.edu.vn
Mật khẩu của admin: 123456
Tài khoản của tất cả giảng viên: gmail của giảng viên
Vd: hung.tranvan@stu.edu.vn
Mật khẩu của tất cả giảng viên: 123456
CÁCH THÊM LỊCH THI có 2 cách: Sau khi đăng nhập vào bằng tài khoản admin.
Cách 1: Chọn “Lịch thi và Phòng gác thi” thì người dùng có thể thêm lịch thi bằng cách nhấn “Thêm” thì có thể thêm lịch thi thủ công. Khi nhấp “Thêm” sẽ hiện ra giao diện và nhập đúng thông tin lịch thi cần tạo thì người dùng có thể tạo lịch thi bằng cách thủ công.
Cách 2: Có thể thêm lịch thi từ file excel khi nhấp vào “Thêm File” sẽ hiện ra hướng dẫn và người dùng cần chuẩn bị file excel theo mẫu. 
  
Sau khi đã chuẩn bị file đúng thông tin lịch thi và đúng mẫu dưới đây thì người dùng có thể chọn Upload file để thêm file lịch thi từ máy lên.
 

CÁCH QUẢN LÝ GIẢNG VIÊN
Người quản lý đăng nhập vào hệ thống từ thanh menu chọn “Quản Lý Giảng Viên”
1. Nếu muốn thêm giảng viên chọn nút “Thêm giảng viên” hệ thống tự hiển thị form để người dùng có thể nhập thông tin vào.
2. Sau khi nhập bấm nút “Lưu” 
3. Sẽ lưu giảng viên và xuất hiện trên giao diện
Người dùng có thể dễ dàng tìm kiếm theo mã giảng viên để có thể xóa, sửa giảng viên dễ dàng hơn.
CÁCH QUẢN LÝ SINH VIÊN
Người quản lý đăng nhập vào hệ thống từ thanh menu chọn “Quản Lý Sinh Viên”
1. Có 2 cách thêm sinh viên
Cách 1: Thêm thủ công “Thêm sinh viên” hệ thống tự hiển thị form để người dùng có thể nhập thông tin vào.
2. Sau khi nhập bấm nút “Lưu” 
3. Sẽ lưu sinh viên và xuất hiện trên giao diện
Người dùng có thể dễ dàng tìm kiếm theo mã sinh viên để có thể xóa, sửa giảng viên dễ dàng hơn.
Cách 2: Thêm bằng file excel “Thêm giảng viên từ file Excel” chọn từ máy file sinh viên cần thêm theo đúng mẫu các dòng và các cột phải chính xác.
 
Sau khi chọn hệ thống xác nhận và tự động thêm.

CÁCH ĐIỂM DANH SINH VIÊN CÓ 2 CÁCH
 Phương thức 1: Quét mã QR
1. Giảng viên vào trang "Lịch gác thi": Chọn ca thi cần điểm danh
2. Click nút "Điểm danh" trên ca thi: Modal điểm danh hiện ra với 2 tab

3. Tab "Quét QR" (mặc định)
   Camera tự động bật
   Giảng viên quét QR code của sinh viên
   QR format: MSSV_TEN_NGAYSINH

       Ví dụ: DH52209990_NGUYENVANA_01.01.2000 

4. Hiển thị xác nhận
    Thông tin: MSSV, Họ tên, Lớp, Email, Ngày sinh
    Nút "Xác nhận điểm danh"
    Nút "Hủy"

5. Tiếp tục quét sinh viên tiếp theo

Phương thức 2: Nhập MSSV 
1. Giảng viên chuyển sang tab "Nhập MSSV"
2. Nhập mã số sinh viên vào
   Ví dụ: DH52209990
3. Click nút "TÌM KIẾM"
4. Xem thông tin và điểm danh sinh viên đó.
5. Xác nhận để điểm danh.

XEM DANH SÁCH ĐIỂM DANH

Từ Nút  “Xem DS"
1. Click nút "Xem DS" trên bảng lịch gác
2. Modal danh sách sinh viên hiện ra
   Hiển thị tất cả sinh viên trong ca thi
  Cột: STT, MSSV, Họ tên, Lớp, Trạng thái
   Badge màu:
    Xanh: Đã điểm danh
Xám: Chưa điểm danh
Từ Trang "Điểm Danh Sinh Viên" 
1. Vào tab "Điểm danh sinh viên" trên menu
2. Có thể chọn ca thi đã gác để coi danh sách điểm danh 
3. Hệ thống sẽ xuất thông tin của ca thi đó 
	Đã điểm danh
	Chưa điểm danh
	Tổng sinh viên
Và sẽ tự động lọc và chia theo danh sách tất cả sinh viên đã điểm danh và chưa điểm danh.

Người dùng có thể tìm kiếm mã số sinh viên để kiểm tra xem sinh viên đó đã điểm danh chưa.

ĐỔI MẬT KHẨU CỦA GIẢNG VIÊN
Giảng viên có thể đổi mật khẩu ở mức đổi mật khẩu và giảng viên phải cung cấp chính xác mật khẩu cũ để có thể đổi mật khẩu mới.



