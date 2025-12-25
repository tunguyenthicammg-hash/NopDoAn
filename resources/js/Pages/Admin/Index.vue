<template>
  <div class="admin-root">
        <header class="header" >
      <button class="hamburger" @click="toggleMobileMenu" aria-label="Mở menu">
        <span></span>
        <span></span>
        <span></span>
      </button>
      <h1 class="ppp">HỆ THỐNG HỖ TRỢ GIÁM THỊ ĐIỂM DANH SINH VIÊN BẰNG HÌNH ẢNH</h1>
       <div class="sidebar-logout">
  <button class="logout" @click="logout">
    <i class="fa-solid fa-right-from-bracket"></i>
  </button>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
</div>
    </header>

    <nav v-if="isMobileMenuOpen" class="mobile-menu">
      <ul>
        <li :class="{active: activePage==='home'}" @click="setActivePage('home')">Trang chủ</li>
        <li :class="{active: activePage==='schedule'}" @click="setActivePage('schedule')">Lịch gác thi & Phòng gác thi</li>
        <li :class="{active: activePage==='attendance'}" @click="setActivePage('attendance')">Kết quả điểm danh</li>
        <li :class="{active: activePage==='lecturers'}" @click="setActivePage('lecturers')">Quản lí giảng viên</li>
        <li :class="{active: activePage==='students'}" @click="setActivePage('students')">Quản lí sinh viên</li>
        <li :class="{active: activePage==='password'}" @click="setActivePage('password')">Đổi mật khẩu</li>
      </ul>
    </nav>

    <div class="layout">
      <aside class="sidebar">
        <nav>
          <ul>
      <li :class="{active: activePage==='home'}" @click="setActivePage('home')">Trang chủ</li>
      <li :class="{active: activePage==='schedule'}" @click="setActivePage('schedule')">Lịch gác thi & Phòng gác thi</li>
      <li :class="{active: activePage==='attendance'}" @click="setActivePage('attendance')">Kết quả điểm danh</li>
      <li :class="{active: activePage==='lecturers'}" @click="setActivePage('lecturers')">Quản lí giảng viên</li>
      <li :class="{active: activePage==='students'}" @click="setActivePage('students')">Quản lí sinh viên</li>
      <li :class="{active: activePage==='password'}" @click="setActivePage('password')">Đổi mật khẩu</li>
          </ul>
        </nav>
      </aside>
      
      <main class="content">
        <section class="card">
          <h2 class="card-title">{{ pageTitle }}</h2>

          <!-- HOME -->
          <div v-if="activePage==='home'" class="page-body">
            <h3>Chào mừng đến với trang quản trị</h3>
            <p>Chúc bạn một ngày làm việc thật hiệu quả.</p>
          </div>

          <!-- SCHEDULE -->
          <div v-if="activePage==='schedule'" class="page-body">
            <div class="toolbar">
              <div class="search">
                <input v-model="scheduleSearch" placeholder="Tìm kiếm theo tên môn học" />
              </div>
              <div class="actions">
                <button @click="openScheduleForm()">Thêm</button>
                <button @click="openImportModal" class="file-btn">Thêm file</button>
              </div>
            </div>
          <table class="table">
            <thead>
              <tr>
                <th class="border border-gray-300 px-2 py-1">STT</th>
                <th class="border border-gray-300 px-2 py-1">Ngày thi</th>
                <th class="border border-gray-300 px-2 py-1">Giờ bắt đầu</th>
                <th class="border border-gray-300 px-2 py-1">Môn học</th>
                <th class="border border-gray-300 px-2 py-1">Số phòng</th>
                <th class="border border-gray-300 px-2 py-1">Danh sách sinh viên</th>
                <th class="border border-gray-300 px-2 py-1">Danh sách giảng viên</th>
                <th class="border border-gray-300 px-2 py-1">Ghi chú</th>
                <th class="border border-gray-300 px-2 py-1">Ngày tạo</th>
                <th class="border border-gray-300 px-2 py-1">Cập nhật</th>
                <th class="border border-gray-300 px-2 py-1">Chỉnh sửa</th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="(item, index) in filteredSchedules"
                :key="item.STT || item.id || index"
                class="hover:bg-gray-50"
              >
                <td class="border border-gray-300 px-2 py-1 text-center" data-label="STT">{{ index + 1 }}</td>
                <td class="border border-gray-300 px-2 py-1 text-center" data-label="Ngày thi">{{ formatDate(item.Ngay_Thi) }}</td>
                <td class="border border-gray-300 px-2 py-1 text-center" data-label="Giờ bắt đầu">{{ item.Gio_Bat_Dau }}</td>
                <td class="border border-gray-300 px-2 py-1" data-label="Môn học">{{ item.Mon_Hoc }}</td>
                <td class="border border-gray-300 px-2 py-1 text-center" data-label="Số phòng">{{ item.So_Phong }}</td>
                <td class="border border-gray-300 px-2 py-1" data-label="Danh sách sinh viên">
                  {{ Array.isArray(item.DSSV) ? item.DSSV.length : (item.DSSV ? item.DSSV.split(',').length : 0) }} sinh viên
                  <button @click="showStudentList(item.DSSV)" style="margin-left:8px; color:#0d6efd; background:none; border:none; cursor:pointer; text-decoration:underline;">Xem chi tiết</button>
                </td>
                <td class="border border-gray-300 px-2 py-1" data-label="Danh sách giảng viên">
                  {{ Array.isArray(item.DSGV) ? item.DSGV.length : (item.DSGV ? item.DSGV.split(',').length : 0) }} giảng viên
                  <button @click="showLecturerList(item.DSGV)" style="margin-left:8px; color:#0d6efd; background:none; border:none; cursor:pointer; text-decoration:underline;">Xem chi tiết</button>
                </td>
                <td class="border border-gray-300 px-2 py-1" data-label="Ghi chú">{{ item.Ghi_Chu }}</td>
                <td class="border border-gray-300 px-2 py-1 text-center" data-label="Ngày tạo">{{ formatDate(item.created_at) }}</td>
                <td class="border border-gray-300 px-2 py-1 text-center" data-label="Cập nhật">{{ formatDate(item.updated_at) }}</td>
                <td class="border border-gray-300 px-2 py-1 text-center" data-label="Chỉnh sửa">
                  <button @click="openScheduleForm(item, index)" class="bg-blue-500 text-white px-2 py-1 rounded mr-1">Sửa</button>
                  <button @click="deleteSchedule(item.STT || item.id)" class="bg-red-500 text-white px-2 py-1 rounded">Xóa</button>
                </td>
              </tr>

              <tr v-if="schedules.length === 0">
                <td colspan="12" class="text-center text-gray-500 py-2">Không có lịch thi nào</td>
              </tr>
          </tbody>
    </table>

          </div>

          <!-- ATTENDANCE (raw check-in admin) -->
          <div v-if="activePage==='attendance'" class="page-body">
            <div class="toolbar">
              <button @click="openAttendanceForm()" style="margin-right: 20px;">Thêm</button>
              <button @click="exportResults">Xuất file CSV</button>
            </div>
            <table class="table">
              <thead>
                <tr>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">MSSV</th>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">Tên</th>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">Môn</th>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">Ngày</th>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">Thời gian</th>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">Trạng thái</th>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">Chỉnh sửa</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(a, i) in attendance" :key="a.id">
                  <td data-label="MSSV">{{ a.mssv }}</td>
                  <td data-label="Tên">{{ a.name }}</td>
                  <td data-label="Môn">{{ a.subject }}</td>
                  <td data-label="Ngày">{{ a.date }}</td>
                  <td data-label="Thời gian">{{ a.time }}</td>
                  <td data-label="Trạng thái">{{ a.status }}</td>
                  <td class="actions-cell" data-label="Chỉnh sửa"><button @click="deleteAttendance(i)">Xóa</button></td>
                </tr>
                <tr v-if="attendance.length===0"><td colspan="7" class="empty">Không có dữ liệu</td></tr>
              </tbody>
            </table>
          </div>

          <!-- LECTURERS -->
          <div v-if="activePage==='lecturers'" class="page-body">
            <div class="toolbar toolbar-lecturers">
              <div class="search">
                <input v-model="lecturerSearch" placeholder="Tìm kiếm theo mã giảng viên" />
              </div>
              <div class="actions">
                <button @click="openLecturerForm()">Thêm giảng viên</button>
              </div>
            </div>
            <table class="table">
              <thead>
                <tr>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">Mã giảng viên</th>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">Họ tên</th>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">Email</th>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">SĐT</th>
                  <th data-v-d31f6b30 class="border border-gray-300 px-2 py-1">Chỉnh sửa</th>
                </tr>
              </thead>
                <tbody>
                      <tr v-for="(l, i) in filteredLecturers" :key="l.id">
                        <td data-label="Mã giảng viên">{{ l.MaGV }}</td>
                        <td data-label="Họ tên">{{ l.Ho_va_Ten }}</td>
                        <td data-label="Email">{{ l.Email }}</td>
                        <td data-label="SĐT">{{ l.Sdt }}</td>
                        <td class="actions-cell" data-label="Chỉnh sửa">
                          <button @click="openLecturerForm(l)" class="bg-blue-500 text-white px-2 py-1 rounded mr-1">Sửa</button>
                          <button @click="deleteLecturer(l.id)" class="bg-red-500 text-white px-2 py-1 rounded">Xóa</button>
                        </td>
                      </tr>
                      <tr v-if="lecturers.length === 0">
                        <td colspan="5" class="empty">Không có dữ liệu</td>
                      </tr>
              </tbody>
            </table>
          </div>

          <!-- STUDENTS -->
          <div v-if="activePage==='students'" class="page-body">
            <!-- Desktop toolbar: search + buttons -->
            <div class="toolbar desktop-only">
              <div class="search">
                <input v-model="studentSearch" placeholder="Tìm kiếm theo mã sinh viên" />
              </div>
              <div class="actions">
                <button @click="openStudentForm()">Thêm sinh viên</button>
                <label class="excel-btn">
                  <input type="file" accept=".xlsx,.xls" @change="importStudentsFromExcel" style="display:none" />
                  Thêm sinh viên từ file Excel
                </label>
                <button @click="deleteAllStudents" class="delete-all-btn">Xóa tất cả sinh viên</button>
              </div>
            </div>

            <!-- Mobile toolbar: search + dropdown -->
            <div class="toolbar toolbar-students mobile-only">
              <div class="search">
                <input v-model="studentSearch" placeholder="Tìm kiếm theo mã sinh viên" />
              </div>
              <div class="actions">
                <div class="dropdown">
                  <button @click="toggleStudentActions">Quản lý</button>
                  <div v-if="studentActionsOpen" class="dropdown-menu">
                    <button @click="openStudentForm()">Thêm sinh viên</button>
                    <label class="excel-btn">
                      <input type="file" accept=".xlsx,.xls" @change="importStudentsFromExcel" style="display:none" />
                      Thêm sinh viên từ file Excel
                    </label>
                    <button @click="deleteAllStudents" class="delete-all-btn">Xóa tất cả sinh viên</button>
                  </div>
                </div>
              </div>
            </div>
            <table class="table">
              <thead>
                <tr>
                  <th>STT</th>
                  <th>Mã số sinh viên</th>
                  <th>Họ và tên</th>
                  <th>Ngày sinh</th>
                  <th>Lớp</th>
                  <th>Khoa</th>
                  <th>Bậc</th>
                  <th>Chỉnh sửa</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(s, i) in filteredStudents" :key="s.Mssv">
                  <td data-label="STT">{{ i + 1 }}</td>
                  <td data-label="Mã số sinh viên">{{ s.Mssv }}</td>
                  <td data-label="Họ và tên">{{ s.Ho_va_ten }}</td>
                  <td data-label="Ngày sinh">{{ formatDate(s.Ngay_Sinh) }}</td>
                  <td data-label="Lớp">{{ s.Lop }}</td>
                  <td data-label="Khoa">{{ s.Khoa }}</td>
                  <td data-label="Bậc">{{ s.Bac || s.bac || s.BAC || 'Chưa có' }}</td>
                  <td class="actions-cell" data-label="Chỉnh sửa">
                    <button @click="openStudentForm(s, i)">Sửa</button>
                    <button @click="deleteStudent(i)">Xóa</button>
                  </td>
                </tr>
                <tr v-if="students.length===0"><td colspan="8" class="empty">Không có dữ liệu</td></tr>
              </tbody>
            </table>
          </div>

          <!-- PASSWORD -->
          <div v-if="activePage==='password'" class="page-body">
            <!-- Success Message -->
            <div v-if="passwordSuccessMessage" class="alert alert-success mb-4" style="padding: 12px; background-color: #d4edda; border: 1px solid #c3e6cb; border-radius: 4px; color: #155724; margin-bottom: 16px;">
              ✅ {{ passwordSuccessMessage }}
            </div>

            <div class="form-row">
              <label>Mật khẩu hiện tại</label>
              <input 
                v-model="passwordForm.current_password" 
                type="password" 
                placeholder="Nhập mật khẩu hiện tại"
                :class="{'error-input': passwordErrors.current_password}"
              />
              <p v-if="passwordErrors.current_password" class="error-text">{{ passwordErrors.current_password }}</p>
            </div>
            <div class="form-row">
              <label>Mật khẩu mới</label>
              <input 
                v-model="passwordForm.new_password" 
                type="password" 
                placeholder="Nhập mật khẩu mới (tối thiểu 6 ký tự)"
                :class="{'error-input': passwordErrors.new_password}"
              />
              <p v-if="passwordErrors.new_password" class="error-text">{{ passwordErrors.new_password }}</p>
            </div>
            <div class="form-row">
              <label>Xác nhận mật khẩu mới</label>
              <input 
                v-model="passwordForm.new_password_confirmation" 
                type="password" 
                placeholder="Nhập lại mật khẩu mới"
              />
            </div>
            <div class="form-row">
              <button @click="changePassword" :disabled="changingPassword">
                {{ changingPassword ? 'Đang xử lý...' : 'Đổi mật khẩu' }}
              </button>
            </div>
          </div>

        </section>
      </main>
    </div>

   

    <!-- FORM / MODAL LỊCH THI -->
<div v-if="showScheduleModal" class="modal">
  <div class="modal-card wide">
    <h3>{{ scheduleEditingIndex === null ? 'Thêm lịch thi' : 'Sửa lịch thi' }}</h3>
    <div class="form-grid">

      <!-- MaMT sẽ được auto-generate ở backend -->

      <div class="form-row">
        <label>Ngày thi</label>
        <input type="date" v-model="scheduleForm.Ngay_Thi" />
      </div>

      <div class="form-row">
        <label>Giờ bắt đầu <span style="color: red;">*</span></label>
        <input type="time" v-model="scheduleForm.Gio_Bat_Dau" required />
      </div>

      <div class="form-row">
        <label>Giờ kết thúc <span style="color: red;">*</span></label>
        <input type="time" v-model="scheduleForm.Gio_Ket_Thuc" required />
      </div>

      <div class="form-row">
        <label>Môn học</label>
        <input v-model="scheduleForm.Mon_Hoc" placeholder="Nhập tên môn học" />
      </div>

      <div class="form-row">
        <label>Số phòng</label>
        <input v-model="scheduleForm.So_Phong" placeholder="Nhập số phòng" />
      </div>

      <div class="form-row full">
        <label style="display: flex; align-items: center; justify-content: space-between;">
          <span>Danh sách sinh viên</span>
          <button 
            type="button"
            @click="toggleSinhVienMode" 
            style="background: #0d6efd; color: white; border: none; padding: 4px 12px; border-radius: 4px; cursor: pointer; font-size: 0.85em;"
          >
            {{ sinhVienInputMode === 'mssv' ? '📋 Nhập theo MSSV' : '👤 Nhập theo Tên' }}
          </button>
        </label>
        <textarea 
          v-model="scheduleForm.DSSV" 
          :placeholder="sinhVienInputMode === 'mssv' 
            ? 'VD: DH52200000, DH52200001 hoặc mỗi MSSV 1 dòng' 
            : 'VD: Nguyễn Văn A, Trần Thị B hoặc mỗi tên 1 dòng'"
          rows="4"
        ></textarea>
        <small v-if="scheduleForm.DSSV" style="color: #0d6efd; font-weight: 600; margin-top: 4px; display: block;">
          📊 {{ parseSinhVienInput(scheduleForm.DSSV).length }} sinh viên
        </small>
      </div>

      <div class="form-row full">
        <label style="display: flex; align-items: center; justify-content: space-between;">
          <span>Danh sách giảng viên</span>
          <button 
            type="button"
            @click="toggleGiangVienMode" 
            style="background: #0d6efd; color: white; border: none; padding: 4px 12px; border-radius: 4px; cursor: pointer; font-size: 0.85em;"
          >
            {{ giangVienInputMode === 'magv' ? '📋 Nhập theo Mã' : '👤 Nhập theo Tên' }}
          </button>
        </label>
        <textarea 
          v-model="scheduleForm.DSGV" 
          :placeholder="giangVienInputMode === 'magv' 
            ? 'VD: GV001, GV002 hoặc mỗi mã 1 dòng' 
            : 'VD: Nguyễn Văn X, Trần Thị Y hoặc mỗi tên 1 dòng'"
          rows="3"
        ></textarea>
        <small v-if="scheduleForm.DSGV" style="color: #0d6efd; font-weight: 600; margin-top: 4px; display: block;">
          📊 {{ parseGiangVienInput(scheduleForm.DSGV).length }} giảng viên
        </small>
      </div>

      <div class="form-row full">
        <label>Ghi chú</label>
        <textarea v-model="scheduleForm.Ghi_Chu" placeholder="Thêm ghi chú (nếu có)"></textarea>
      </div>

    </div>

    <div class="form-row actions">
      <button @click="saveSchedule" :disabled="isSavingSchedule" 
              :class="isSavingSchedule ? 'bg-gray-400 cursor-not-allowed' : 'bg-blue-500'"
              class="text-white px-3 py-1 rounded mr-2">
        {{ isSavingSchedule ? 'Đang lưu...' : 'Lưu' }}
      </button>
      <button @click="closeScheduleForm" class="bg-gray-400 text-white px-3 py-1 rounded">Hủy</button>
    </div>
  </div>
</div>

    <!-- FORMS / MODALS cua diem danh sinh vien-->
    <div v-if="showAttendanceModal" class="modal">
      <div class="modal-card">
        <h3>Thêm điểm danh</h3>
        <div class="form-row"><label>MSSV</label><input v-model="attendanceForm.mssv" /></div>
        <div class="form-row"><label>Tên</label><input v-model="attendanceForm.name" /></div>
        <div class="form-row"><label>Môn</label><input v-model="attendanceForm.subject" /></div>
        <div class="form-row"><label>Ngày</label><input type="date" v-model="attendanceForm.date" /></div>
        <div class="form-row"><label>Thời gian</label><input type="time" v-model="attendanceForm.time" /></div>
        <div class="form-row"><label>Trạng thái</label>
          <select v-model="attendanceForm.status">
            <option>Có mặt</option>
            <option>Vắng</option>
          </select>
        </div>
        <div class="form-row actions"><button @click="saveAttendance">Lưu</button><button @click="closeAttendanceForm">Hủy</button></div>
      </div>
    </div>

     <!-- FORMS / MODALS cua giang vien -->
          <div v-if="showLecturerModal" class="modal">
          <div class="modal-card modal-card-centered">
            <h3>{{ lecturerEditingIndex === null ? 'Thêm giảng viên' : 'Sửa giảng viên' }}</h3>
            <div class="form-row">
              <label>Mã giảng viên</label>
              <input v-model="lecturerForm.MaGV" />
            </div>
            <div class="form-row">
              <label>Họ tên</label>
              <input v-model="lecturerForm.Ho_va_Ten" />
            </div>
            <div class="form-row">
              <label>Email</label>
              <input v-model="lecturerForm.Email" />
            </div>
            <div class="form-row">
              <label>SĐT</label>
              <input v-model="lecturerForm.Sdt" />
            </div>
            <div class="form-row actions">
              <button @click="saveLecturer">Lưu</button>
              <button @click="closeLecturerForm">Hủy</button>
            </div>
          </div>
        </div>

  

  <!-- FORMS / MODALS cua quản lý sinh viên-->
    
          <div v-if= "showStudentModal" class="modal">
          <div class="modal-card">
            <h3>{{ studentEditingIndex === null ? 'Thêm sinh viên' : 'Sửa sinh viên' }}</h3>
            <div class="form-row">
              <label>Mã số sinh viên</label>
              <input v-model="studentForm.Mssv" />
            </div>
            <div class="form-row">
              <label>Họ và tên</label>
              <input v-model="studentForm.Ho_va_ten" />
            </div>
            <div class="form-row">
              <label>Ngày sinh</label>
              <input type="date" v-model="studentForm.Ngay_Sinh" />
            </div>
            <div class="form-row">
              <label>Lớp</label>
              <input v-model="studentForm.Lop" />
            </div>
            <div class="form-row">
              <label>Khoa</label>
              <input v-model="studentForm.Khoa" />  
            </div>
            <div class="form-row">
              <label>Bậc</label>
              <input 
                v-model.trim="studentForm.Bac" 
                placeholder="Nhập bậc đào tạo" 
                @input="console.log('Bậc value changed:', studentForm.Bac)"
                @change="console.log('Bậc final value:', studentForm.Bac)"
                class="form-control"
                type="text"
                required
              />
            </div>
            <div class="form-row actions">
              <button @click="saveStudent">Lưu</button>
              <button @click="closeStudentForm">Hủy</button>
            </div>
          </div>
        </div>

    <!-- Modal hiển thị danh sách sinh viên chi tiết -->
    <div v-if="showStudentListModal" class="modal">
      <div class="modal-card wide">
        <h3 style="margin-bottom: 16px;">Danh sách sinh viên</h3>
        <div class="table-wrapper" style="max-height: 60vh; overflow-y: auto; margin-bottom: 16px;">
          <table class="table">
            <thead>
              <tr>
                <th>MSSV</th>
                <th>Họ và tên</th>
                <th>Ngày sinh</th>
                <th>Lớp</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="sv in studentListDetail" :key="sv.Mssv">
                <td data-label="MSSV">{{ sv.Mssv }}</td>
                <td data-label="Họ và tên">{{ sv.Ho_va_ten }}</td>
                <td data-label="Ngày sinh">{{ formatDate(sv.Ngay_Sinh) }}</td>
                <td data-label="Lớp">{{ sv.Lop }}</td>
              </tr>
              <tr v-if="studentListDetail.length === 0">
                <td colspan="4" class="empty">Không có dữ liệu</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="form-row actions">
          <button @click="showStudentListModal = false">Đóng</button>
        </div>
      </div>
    </div>

    <!-- Modal hiển thị danh sách giảng viên chi tiết -->
    <div v-if="showLecturerListModal" class="modal">
      <div class="modal-card wide">
        <h3 style="margin-bottom: 16px;">Danh sách giảng viên</h3>
        <div class="table-wrapper" style="max-height: 60vh; overflow-y: auto; margin-bottom: 16px;">
          <table class="table">
            <thead>
              <tr>
                <th>Mã giảng viên</th>
                <th>Họ và tên</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="gv in lecturerListDetail" :key="gv.MaGV">
                <td data-label="Mã giảng viên">{{ gv.MaGV }}</td>
                <td data-label="Họ và tên">{{ gv.Ho_va_Ten }}</td>
              </tr>
              <tr v-if="lecturerListDetail.length === 0">
                <td colspan="2" class="empty">Không có dữ liệu</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="form-row actions">
          <button @click="showLecturerListModal = false">Đóng</button>
        </div>
      </div>
    </div>

    <!-- Modal Import lịch thi từ Excel -->
    <div v-if="showImportModal" class="modal">
      <div class="modal-card wide" style="max-width: 900px;">
        <h3>📥 Import lịch thi từ file Excel</h3>
        
        <!-- Tabs -->
        <div class="tabs" style="display: flex; gap: 10px; margin-bottom: 20px; border-bottom: 2px solid #e0e0e0;">
          <button 
            @click="importTab = 'template'" 
            :class="['tab-btn', { active: importTab === 'template' }]"
            style="padding: 10px 20px; border: none; background: none; cursor: pointer; border-bottom: 3px solid transparent;"
            :style="{ borderBottomColor: importTab === 'template' ? '#4472C4' : 'transparent', fontWeight: importTab === 'template' ? 'bold' : 'normal' }"
          >
            📋 Xem Template
          </button>
          <button 
            @click="importTab = 'upload'" 
            :class="['tab-btn', { active: importTab === 'upload' }]"
            style="padding: 10px 20px; border: none; background: none; cursor: pointer; border-bottom: 3px solid transparent;"
            :style="{ borderBottomColor: importTab === 'upload' ? '#4472C4' : 'transparent', fontWeight: importTab === 'upload' ? 'bold' : 'normal' }"
          >
            ⬆️ Upload File
          </button>
        </div>

        <!-- Tab Content: Template -->
        <div v-if="importTab === 'template'" style="max-height: 500px; overflow-y: auto;">
          <div style="margin-bottom: 15px; padding: 10px; background: #f0f7ff; border-left: 4px solid #4472C4;">
            <h4 style="margin: 0 0 10px 0;">📝 Mẫu file Excel:</h4>
            <a href="/schedules/template/download" class="download-btn" style="display: inline-block; padding: 10px 20px; background: #28a745; color: white; text-decoration: none; border-radius: 5px; margin-bottom: 10px;">
              ⬇️ Tải file mẫu (.xlsx)
            </a>
          </div>

          <table class="table" style="font-size: 12px;">
            <thead>
              <tr style="background: #4472C4; color: white;">
                <th>Môn học</th>
                <th>Ngày thi</th>
                <th>Giờ bắt đầu</th>
                <th>Giờ kết thúc</th>
                <th>Số phòng</th>
                <th>Danh sách SV</th>
                <th>Danh sách GV</th>
                <th>Ghi chú</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td data-label="Môn học">Lập trình Web</td>
                <td data-label="Ngày thi">2025-12-25</td>
                <td data-label="Giờ bắt đầu">08:00</td>
                <td data-label="Giờ kết thúc">10:00</td>
                <td data-label="Số phòng">1</td>
                <td data-label="Danh sách SV" style="white-space: pre-line; max-width: 150px;">2021CNTT001
2021CNTT002
2021CNTT003</td>
                <td data-label="Danh sách GV" style="white-space: pre-line;">GV001
GV002</td>
                <td data-label="Ghi chú">Ca sáng</td>
              </tr>
              <tr>
                <td data-label="Môn học">Cơ sở dữ liệu</td>
                <td data-label="Ngày thi">2025-12-26</td>
                <td data-label="Giờ bắt đầu">13:00</td>
                <td data-label="Giờ kết thúc">15:00</td>
                <td data-label="Số phòng">2</td>
                <td data-label="Danh sách SV">Nguyễn Văn A, Trần Thị B</td>
                <td data-label="Danh sách GV">Hoa Triệu, Nhậm Tuấn</td>
                <td data-label="Ghi chú">Ca chiều</td>
              </tr>
            </tbody>
          </table>

          <div style="margin-top: 15px; padding: 15px; background: #fff3cd; border-left: 4px solid #ffc107;">
            <h4 style="margin: 0 0 10px 0;">💡 Lưu ý:</h4>
            <ul style="margin: 0; padding-left: 20px;">
              <li>Mỗi dòng = 1 lịch thi</li>
              <li>Mã môn thi (MaMT) sẽ <strong>tự động tạo</strong></li>
              <li><strong>DSSV/DSGV</strong> có thể nhập <strong>Mã số</strong> hoặc <strong>Tên</strong></li>
              <li>Phân cách bằng <strong>dấu phẩy (,)</strong> hoặc <strong>xuống dòng</strong> (Alt+Enter trong Excel)</li>
            </ul>
          </div>
        </div>

        <!-- Tab Content: Upload -->
        <div v-if="importTab === 'upload'">
          <div 
            @drop.prevent="handleFileDrop" 
            @dragover.prevent 
            @dragenter.prevent="isDragging = true"
            @dragleave.prevent="isDragging = false"
            class="upload-zone"
            :class="{ dragging: isDragging }"
            style="border: 2px dashed #ccc; border-radius: 10px; padding: 40px; text-align: center; cursor: pointer; transition: all 0.3s;"
            :style="{ borderColor: isDragging ? '#4472C4' : '#ccc', background: isDragging ? '#f0f7ff' : '#fafafa' }"
            @click="$refs.fileInput.click()"
          >
            <div style="font-size: 48px; margin-bottom: 10px;">📤</div>
            <p style="font-size: 16px; margin: 10px 0;">Kéo thả file Excel vào đây</p>
            <p style="color: #666;">hoặc</p>
            <button type="button" style="padding: 10px 20px; background: #4472C4; color: white; border: none; border-radius: 5px; cursor: pointer; font-size: 14px;">
              Chọn file
            </button>
            <input 
              ref="fileInput" 
              type="file" 
              accept=".xlsx,.xls" 
              @change="handleFileSelect" 
              style="display: none;"
            />
            <p style="font-size: 12px; color: #999; margin-top: 15px;">Hỗ trợ: .xlsx, .xls (tối đa 10MB)</p>
          </div>

          <div v-if="selectedFile" style="margin-top: 20px; padding: 15px; background: #e7f3ff; border-radius: 5px;">
            <p style="margin: 0;">
              <strong>File đã chọn:</strong> {{ selectedFile.name }} 
              <span style="color: #666;">({{ (selectedFile.size / 1024).toFixed(2) }} KB)</span>
            </p>
          </div>

          <div v-if="importResult" style="margin-top: 20px; padding: 15px; border-radius: 5px;"
               :style="{ background: importResult.success ? '#d4edda' : '#f8d7da', borderLeft: `4px solid ${importResult.success ? '#28a745' : '#dc3545'}` }">
            <p style="margin: 0 0 10px 0; font-weight: bold;">{{ importResult.message }}</p>
            <div v-if="importResult.success">
              <p style="margin: 5px 0;">✅ Import thành công: {{ importResult.imported }} lịch thi</p>
              <p v-if="importResult.skipped > 0" style="margin: 5px 0;">⚠️ Bỏ qua: {{ importResult.skipped }} dòng trống</p>
            </div>
            <div v-if="importResult.errors && importResult.errors.length > 0">
              <p style="margin: 10px 0 5px 0; font-weight: bold;">❌ Lỗi:</p>
              <ul style="margin: 0; padding-left: 20px;">
                <li v-for="(error, idx) in importResult.errors" :key="idx" style="margin: 3px 0;">{{ error }}</li>
              </ul>
            </div>
          </div>
        </div>

        <div class="form-row actions" style="margin-top: 20px; display: flex; gap: 10px; justify-content: flex-end;">
          <button v-if="importTab === 'upload' && selectedFile && !importing" @click="uploadFile" style="background: #28a745; color: white; padding: 10px 20px;">
            ⬆️ Upload và Import
          </button>
          <button v-if="importing" disabled style="background: #6c757d; color: white; padding: 10px 20px;">
            ⏳ Đang xử lý...
          </button>
          <button @click="closeImportModal" style="background: #6c757d; color: white; padding: 10px 20px;">Đóng</button>
        </div>
      </div>
    </div>

    <!-- Toast Notification (Mobile) -->
    <div v-if="notify.show" class="toast" :class="`toast-${notify.type}`">
      {{ notify.message }}
    </div>

    <!-- Confirm Modal (Mobile) -->
    <div v-if="confirmDialog.show" class="modal" style="position: fixed; display: flex;">
      <div class="modal-card" style="max-width: 400px;">
        <p style="margin-top: 0; margin-bottom: 20px; font-size: 16px; line-height: 1.5;">{{ confirmDialog.message }}</p>
        <div style="display: flex; gap: 10px; justify-content: flex-end;">
          <button @click="confirmDialog.show = false; confirmDialog.resolve(false)" style="background: #6c757d; color: white; border: none; padding: 8px 16px; border-radius: 6px; cursor: pointer; font-weight: 600;">Hủy</button>
          <button @click="confirmDialog.show = false; confirmDialog.resolve(true)" style="background: #0d6efd; color: white; border: none; padding: 8px 16px; border-radius: 6px; cursor: pointer; font-weight: 600;">Xác nhận</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref, computed, watch, onMounted, nextTick } from 'vue'
import axios from 'axios' 
import { router } from '@inertiajs/vue3'
import * as XLSX from 'xlsx'

// Khởi tạo các biến reactive

axios.defaults.withCredentials = true
axios.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest'

const activePage = ref('home')
const isMobileMenuOpen = ref(false)

// Toast notification state
const notify = reactive({
  show: false,
  message: '',
  type: 'success' // 'success', 'error', 'info'
})

// Confirm dialog state
const confirmDialog = reactive({
  show: false,
  message: '',
  resolve: null
})

// Show notification: alert on desktop, toast on mobile
function showNotify(message, type = 'success') {
  if (window.innerWidth > 900) {
    // Desktop: use native alert
    alert(message)
  } else {
    // Mobile: show toast
    notify.message = message
    notify.type = type
    notify.show = true
    
    // Auto-hide after 2.5 seconds
    setTimeout(() => {
      notify.show = false
    }, 2500)
  }
}

// Confirm dialog: confirm on desktop, modal on mobile
function showConfirm(message) {
  return new Promise((resolve) => {
    if (window.innerWidth > 900) {
      // Desktop: use native confirm
      resolve(confirm(message))
    } else {
      // Mobile: show custom modal
      confirmDialog.message = message
      confirmDialog.show = true
      confirmDialog.resolve = resolve
    }
  })
}

// Password change form
const passwordForm = reactive({
  current_password: '',
  new_password: '',
  new_password_confirmation: ''
})
const passwordErrors = ref({})
const passwordSuccessMessage = ref('')
const changingPassword = ref(false)

async function changePassword() {
  passwordErrors.value = {}
  passwordSuccessMessage.value = ''
  changingPassword.value = true

  try {
    const response = await axios.post('/admin/change-password', passwordForm)
    
    if (response.data.success || response.status === 200) {
      passwordSuccessMessage.value = 'Đổi mật khẩu thành công!'
      // Reset form
      passwordForm.current_password = ''
      passwordForm.new_password = ''
      passwordForm.new_password_confirmation = ''
      
      // Auto hide success message after 5 seconds
      setTimeout(() => {
        passwordSuccessMessage.value = ''
      }, 5000)
    }
  } catch (error) {
    if (error.response?.data?.errors) {
      passwordErrors.value = error.response.data.errors
    } else if (error.response?.data?.message) {
      passwordErrors.value = { current_password: error.response.data.message }
    } else {
      showNotify('Có lỗi xảy ra khi đổi mật khẩu!', 'error')
    }
  } finally {
    changingPassword.value = false
  }
}

async function deleteAllStudents() {
  if(!(await showConfirm('⚠️ CẢNH BÁO: Bạn có chắc chắn muốn xóa TẤT CẢ sinh viên? Hành động này không thể hoàn tác!'))) return
  try {
    const response = await axios.post('/students/delete-all')

    // Backend currently returns { message: '...' } with 200 on success.
    // Treat any 2xx status as success for compatibility.
    if (response && response.status >= 200 && response.status < 300) {
      // Clear local UI data and localStorage
      students.value = []
      localStorage.removeItem('students')

      // Refresh from server to ensure consistent state
      try {
        const checkResponse = await axios.get('/students')
        if (Array.isArray(checkResponse.data)) {
          students.value = [...checkResponse.data]
        }
      } catch (e) {
        // If refresh fails, keep students as empty but log the error
        console.warn('Could not refresh students after delete-all', e)
      }

      // Notify user and (optionally) reload page
      showNotify('✅ Đã xóa tất cả sinh viên!', 'success')
      // If you prefer a full page reload uncomment next line:
      // window.location.reload()
    } else {
      throw new Error(response.data?.message || 'Không thể xóa sinh viên')
    }
  } catch(err) {
    console.error('❌ Lỗi khi xóa tất cả sinh viên:', err.response?.data || err.message)
    const serverMsg = err.response?.data?.message || err.message || 'Lỗi không xác định'
    showNotify('❌ Không thể xóa tất cả sinh viên: ' + serverMsg, 'error')
  }
}

function setActivePage(p){
  console.log('nav click ->', p)
  activePage.value = p
  isMobileMenuOpen.value = false
}

function toggleMobileMenu(){
  isMobileMenuOpen.value = !isMobileMenuOpen.value
}

const pageTitle = computed(() => {
  switch (activePage.value) {
    case 'home': return 'TRANG CHỦ'
    case 'schedule': return 'LỊCH GÁC THI & PHÒNG GÁC THI'
    case 'attendance': return 'KẾT QUẢ ĐIỂM DANH'
    case 'lecturers': return 'QUẢN LÍ GIẢNG VIÊN'
    case 'students': return 'QUẢN LÍ SINH VIÊN'
    default: return ''
  }
})

const placeholder = 'data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="80" height="80"><rect width="100%" height="100%" fill="%23ddd"/><text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" fill="%23888" font-size="12">Avatar</text></svg>'

// 🧹 Dữ liệu khởi tạo trống (đã xóa toàn bộ mẫu)
const lecturers = ref([])
const students = ref([])
const schedules = ref([])
const attendance = ref([])



function formatDate(dateString) {
  // Nếu rỗng hoặc null thì trả về chuỗi rỗng
  if (!dateString) return ''

  // Tạo đối tượng ngày
  const date = new Date(dateString)

  // Kiểm tra nếu không hợp lệ
  if (isNaN(date)) return dateString

  // Trả về định dạng ngày kiểu Việt Nam (VD: 22/10/2025)
  return date.toLocaleDateString('vi-VN', {
    day: '2-digit',
    month: '2-digit',
    year: 'numeric',
  })
}

// fetch functions from server
const fetchLecturers = async () => {
  try {
    const res = await axios.get('/lecturers')
    let data = res.data || []
    // Sắp xếp theo MaGV tăng dần
    data.sort((a, b) => {
      const maGVA = String(a.MaGV || '').toUpperCase()
      const maGVB = String(b.MaGV || '').toUpperCase()
      return maGVA.localeCompare(maGVB, 'vi')
    })
    lecturers.value = data
  } catch (err) {
    console.error('fetchLecturers failed', err.response?.status, err.response?.data || err.message)
  }
}

const fetchStudents = async () => {
  try {
    const res = await axios.get('/students')
    console.log('Raw response from /students:', res); // Debug full response
    console.log('Fetched students data:', res.data);

    if (Array.isArray(res.data)) {
      // Map và log từng sinh viên để debug
      students.value = res.data.map(student => {
        const mappedStudent = {
          ...student,
          Bac: student.Bac || student.bac || student.BAC || null // Thử nhiều cách viết
        };
        console.log('Mapped student:', mappedStudent); // Debug each student
        return mappedStudent;
      });
    } else {
      console.error('Data from server is not an array:', res.data);
      students.value = [];
    }
  } catch (err) {
    console.error('fetchStudents failed', err.response?.status, err.response?.data || err.message)
    students.value = [];
  }
}

const fetchSchedules = async () => {
  try {
    console.log('GET /admin/schedules -> sending')
    const res = await axios.get('/schedules')
    console.log('schedules res', res.status, res.data)
    schedules.value = Array.isArray(res.data) ? res.data : []
  } catch (err) {
    console.error('fetchSchedules failed', err.response?.status, err.response?.data || err.message)
    if (err.response && typeof err.response.data === 'string') {
      console.log('Response text preview:', err.response.data.slice(0, 1000))
    }
    // thử sample route để đảm bảo frontend hoạt động
    try {
      const sample = await axios.get('/test/admin-schedules-sample')
      schedules.value = sample.data || []
      console.log('loaded sample schedules')
    } catch (e) {
      console.error('sample endpoint failed', e)
      schedules.value = []
    }
  }
}


async function saveSchedule() {
  // Prevent double submit
  if (isSavingSchedule.value) return
  
  try {
    isSavingSchedule.value = true // Disable button
    
    // Xử lý danh sách sinh viên: hỗ trợ dấu phẩy và xuống dòng
    let danhSachSinhVien = '';
    let danhSachSinhVienMSSV = ''; // Để gửi lên server
    
    if (scheduleForm.DSSV) {
      // Tách theo dấu phẩy hoặc xuống dòng, trim, loại bỏ rỗng
      const dssvArray = parseSinhVienInput(scheduleForm.DSSV);
      
      if (sinhVienInputMode.value === 'ten') {
        // Mode nhập theo tên: tìm MSSV từ tên
        const mssvArray = [];
        for (const ten of dssvArray) {
          // Tìm sinh viên theo tên (tìm gần đúng)
          const sv = students.value.find(s => 
            s.Ho_va_ten && s.Ho_va_ten.toLowerCase().includes(ten.toLowerCase())
          );
          if (sv && sv.Mssv) {
            mssvArray.push(sv.Mssv);
          } else {
            console.warn(`⚠️ Không tìm thấy sinh viên: ${ten}`);
          }
        }
        danhSachSinhVienMSSV = mssvArray.join(',');
        danhSachSinhVien = mssvArray.join(',');
      } else {
        // Mode nhập theo MSSV
        danhSachSinhVienMSSV = dssvArray.join(',');
        danhSachSinhVien = dssvArray.join(',');
      }
    }

    // Xử lý danh sách giảng viên tương tự
    let danhSachGiangVien = '';
    let danhSachGiangVienMaGV = '';
    
    if (scheduleForm.DSGV) {
      const dsgvArray = parseGiangVienInput(scheduleForm.DSGV);
      
      if (giangVienInputMode.value === 'ten') {
        // Mode nhập theo tên: tìm Mã GV từ tên
        const magvArray = [];
        for (const ten of dsgvArray) {
          const gv = lecturers.value.find(l => 
            l.Ho_va_Ten && l.Ho_va_Ten.toLowerCase().includes(ten.toLowerCase())
          );
          if (gv && gv.MaGV) {
            magvArray.push(gv.MaGV);
          } else {
            console.warn(`⚠️ Không tìm thấy giảng viên: ${ten}`);
          }
        }
        danhSachGiangVienMaGV = magvArray.join(',');
        danhSachGiangVien = magvArray.join(',');
      } else {
        // Mode nhập theo Mã GV
        danhSachGiangVienMaGV = dsgvArray.join(',');
        danhSachGiangVien = dsgvArray.join(',');
      }
    }

    // Tạo object data đã được chuẩn hóa
    const scheduleData = {
      ...scheduleForm,
      DSSV: danhSachSinhVienMSSV,
      DSGV: danhSachGiangVienMaGV,
      sinhVienInputMode: sinhVienInputMode.value,
      giangVienInputMode: giangVienInputMode.value
    };

    if (scheduleEditingIndex.value === null) {
      // Thêm mới
      await axios.post('/schedules/add', scheduleData);
      showNotify('✅ Thêm lịch thi thành công!', 'success');
    } else {
      // Cập nhật: dùng STT (hoặc id) làm identifier
      const id = scheduleForm.STT || scheduleForm.id || scheduleEditingIndex.value;
      await axios.put(`/schedules/update/${id}`, scheduleData);
      showNotify('✅ Cập nhật lịch thi thành công!', 'success');
    }

    await fetchSchedules();
    closeScheduleForm();
  } catch (err) {
    console.error('❌ Lỗi khi lưu lịch thi:', err.response?.data || err.message);
    showNotify('❌ Không thể lưu lịch thi', 'error');
  } finally {
    isSavingSchedule.value = false // Re-enable button
  }
}


const fetchAttendance = async () => {
  try {
    const res = await axios.get('/admin/attendance')
    attendance.value = res.data || []
  } catch (err) {
    console.error('fetchAttendance failed', err.response?.status, err.response?.data || err.message)
  }
}

// gọi khi component mount
onMounted(() => {
  fetchLecturers()
  fetchStudents()
  fetchSchedules()
  fetchAttendance()
})

// Tìm kiếm
const scheduleSearch = ref('')
const normalizeStr = (v='') => v.toString().toLowerCase().normalize('NFD').replace(/\p{Diacritic}/gu,'')
const filteredSchedules = computed(() => {
  if (!scheduleSearch.value) return schedules.value
  const q = normalizeStr(scheduleSearch.value)
  return schedules.value.filter(r => {
    const name = normalizeStr(r.Mon_Hoc || r.subjectName || '')
    const code = normalizeStr(r.Ma_Mon || r.subjectCode || '')
    return name.includes(q) || code.includes(q)
  })
})

// Tìm kiếm giảng viên theo mã
const lecturerSearch = ref('')
const filteredLecturers = computed(() => {
  if (!lecturerSearch.value) return lecturers.value
  const q = normalizeStr(lecturerSearch.value)
  return lecturers.value.filter(l => normalizeStr(l.MaGV || '').includes(q))
})

// Tìm kiếm sinh viên theo MSSV
const studentSearch = ref('')
const filteredStudents = computed(() => {
  if (!studentSearch.value) return students.value
  const q = normalizeStr(studentSearch.value)
  return students.value.filter(s => normalizeStr(s.Mssv || '').includes(q))
})

// Toggle dropdown quản lý sinh viên
const studentActionsOpen = ref(false)
function toggleStudentActions(){
  studentActionsOpen.value = !studentActionsOpen.value
}

// =============================
// MODALS & FORM - Giảng viên
// =============================
const showLecturerModal = ref(false)
const lecturerForm = reactive({ id: null, MaGV: '', Ho_va_Ten: '', Email: '', Sdt: '' })
const lecturerEditingIndex = ref(null)

function openLecturerForm(item=null){
  if(item){ 
    Object.assign(lecturerForm, item); 
    lecturerEditingIndex.value = item.id;
  } else { 
    Object.assign(lecturerForm, {
      id: null,
      MaGV:'',
      Ho_va_Ten:'',
      Email:'',
      Sdt:''});
    lecturerEditingIndex.value = null;
  }
  showLecturerModal.value = true;
}
function closeLecturerForm(){ 
  showLecturerModal.value = false;
  Object.assign(lecturerForm, {
    id: null,
    MaGV:'',
    Ho_va_Ten:'',
    Email:'',
    Sdt:''});
  lecturerEditingIndex.value = null;
}
async function saveLecturer() {
  try {
    if (!lecturerForm.MaGV || !lecturerForm.Ho_va_Ten) {
      showNotify('❌ Vui lòng điền đầy đủ thông tin (Mã giảng viên, Họ tên)', 'error');
      return;
    }
    if (lecturerEditingIndex.value === null) {
      // ➕ Thêm mới
      await axios.post('/lecturers/add', lecturerForm);
      showNotify('✅ Thêm giảng viên thành công!', 'success');
    } else {
      // ✏️ Cập nhật - dùng ID thay vì MaGV
      await axios.put(`/lecturers/update/${lecturerEditingIndex.value}`, lecturerForm);
      showNotify('✅ Cập nhật giảng viên thành công!', 'success');
    }

    await fetchLecturers();
    closeLecturerForm();
  } catch (err) {
    console.error('❌ Lỗi khi lưu giảng viên:', err.response?.data || err.message);
    showNotify('❌ Không thể lưu giảng viên: ' + (err.response?.data?.message || err.message), 'error');
  }
}
async function deleteLecturer(id){
  if (!(await showConfirm('Bạn có chắc chắn muốn xóa giảng viên này không?'))) return;
  try {
    await axios.delete(`/lecturers/delete/${id}`);
    await fetchLecturers();
    showNotify('✅ Xóa giảng viên thành công!', 'success');
  } catch (err) {
    console.error('❌ Lỗi khi xóa giảng viên:', err.response?.data || err.message);
  }}

// =============================
// MODALS & FORM - Sinh viên
// =============================
const showStudentModal = ref(false)
const studentForm = reactive({
  Mssv: '',
  Ho_va_ten: '',
  Ngay_Sinh: '',
  Lop: '',
  Khoa: '',
  Bac: ''
})
const studentEditingIndex = ref(null)



const previewImage = ref(null)

function handleFileUpload(event) {
  const file = event.target.files[0]
  if (file) {
    studentForm.value.Photo = file
    previewImage.value = URL.createObjectURL(file) // để hiển thị ảnh xem trước
  }
}


function openStudentForm(item=null, idx=null){
  if(item){
    Object.assign(studentForm, {
      Mssv: item.Mssv || '',
      Ho_va_ten: item.Ho_va_ten || item.Hovaten || '',
      Ngay_Sinh: item.Ngay_Sinh || '',
      Lop: item.Lop || '',
      Khoa: item.Khoa || '',
      Bac: item.Bac || ''
    });
    studentEditingIndex.value = idx
  } else {
    Object.assign(studentForm, { Mssv:'', Ho_va_ten:'', Ngay_Sinh:'', Lop:'', Khoa:'', Bac:'' });
    studentEditingIndex.value = null
  }
  showStudentModal.value = true
}
function closeStudentForm(){ showStudentModal.value = false }
function onStudentPhoto(e){
  const f = e.target.files[0]
  if(!f) return
  const reader = new FileReader()
  reader.onload = ev => studentForm.Photo = ev.target.result
  reader.readAsDataURL(f)
}
async function saveStudent(){
  try {
    // Validate các trường bắt buộc
    if (!studentForm.Mssv) {
      showNotify('❌ Vui lòng nhập MSSV', 'error');
      return;
    }
    if (!studentForm.Ho_va_ten) {
      showNotify('❌ Vui lòng nhập Họ và tên', 'error');
      return;
    }
    if (!studentForm.Bac) {
      showNotify('❌ Vui lòng nhập Bậc đào tạo', 'error');
      return;
    }

    // Kiểm tra và log giá trị form trước khi gửi
    console.log('Current form values:', studentForm);
    
    // Chuẩn bị dữ liệu gửi lên server
    // Kiểm tra và format dữ liệu trước khi gửi
    const bac = studentForm.Bac.trim(); // Loại bỏ khoảng trắng thừa
    console.log('Giá trị bậc trước khi gửi:', bac);

    const studentData = {
      Mssv: studentForm.Mssv,
      Ho_va_ten: studentForm.Ho_va_ten,
      Ngay_Sinh: studentForm.Ngay_Sinh || '',
      Lop: studentForm.Lop || '',
      Khoa: studentForm.Khoa || '',
      Bac: bac // Gửi giá trị bậc đã được xử lý
    };
    
    // Log dữ liệu trước khi gửi
    console.log('Data to be sent:', studentData);

    console.log('Form data being sent:', {
      Mssv: studentForm.Mssv,
      Ho_va_ten: studentForm.Ho_va_ten,
      Ngay_Sinh: studentForm.Ngay_Sinh,
      Lop: studentForm.Lop,
      Khoa: studentForm.Khoa,
      Bac: studentForm.Bac
    });

    let response;
    if (studentEditingIndex.value === null) {
      // Thêm mới sinh viên
      console.log('Sending data for new student:', studentData);
      response = await axios.post('/students/add', studentData, {
        headers: {
          'Content-Type': 'application/json'
        }
      });
      console.log('Add response:', response.data);
    } else {
      // Cập nhật sinh viên
      console.log('Sending data for update:', studentData);
      response = await axios.put(`/students/update/${studentForm.Mssv}`, studentData, {
        headers: {
          'Content-Type': 'application/json'
        }
      });
      console.log('Update response:', response.data);
    }

    if (response.data.success) {
      // Log response từ server
      console.log('Server response:', response.data);

      // Tạo object mới với dữ liệu đã được xác nhận từ form
      const newStudentData = {
        Mssv: studentForm.Mssv,
        Ho_va_ten: studentForm.Ho_va_ten,
        Ngay_Sinh: studentForm.Ngay_Sinh,
        Lop: studentForm.Lop,
        Khoa: studentForm.Khoa,
        Bac: studentForm.Bac // Đảm bảo lưu đúng giá trị bậc
      };

      // Cập nhật giao diện ngay lập tức
      if (studentEditingIndex.value === null) {
        students.value = [...students.value, newStudentData];
      } else {
        students.value[studentEditingIndex.value] = newStudentData;
        students.value = [...students.value]; // Force reactivity update
      }
      
      alert('✅ ' + (studentEditingIndex.value === null ? 'Thêm' : 'Cập nhật') + ' sinh viên thành công!');
      closeStudentForm();
      
      // Tải lại danh sách từ server để đảm bảo đồng bộ
      await fetchStudents();
    } else {
      throw new Error(response.data.message || 'Không thể lưu sinh viên');
    }
  } catch (err) {
    console.error('Lỗi khi lưu sinh viên:', err);
    showNotify('❌ Không thể lưu sinh viên: ' + (err.response?.data?.message || err.message), 'error');
  }
}
async function deleteStudent(i){
  const s = students.value[i]
  if(!s){ showNotify('Không tìm thấy sinh viên để xóa', 'error'); return }
  if(!(await showConfirm('Bạn có chắc chắn muốn xóa sinh viên này?'))) return;
  try{
    // dùng route mới: DELETE /students/delete/{id}
    const id = s.id || s.Mssv || s.mssv
    await axios.delete(`/students/delete/${encodeURIComponent(id)}`)
    await fetchStudents()
    showNotify('✅ Xóa sinh viên thành công!', 'success')
  }catch(err){
    console.error('❌ Lỗi khi xóa sinh viên:', err.response?.data || err.message)
    // Hiển thị chi tiết lỗi từ server nếu có
    const serverData = err.response?.data
    let msg = serverData?.message || err.message || 'Xóa thất bại'
    if (serverData?.error) msg += ': ' + serverData.error
    showNotify('❌ Xóa sinh viên thất bại: ' + msg, 'error')
  }
}


// =============================
// MODALS & FORM - Lịch thi
// =============================
const showScheduleModal = ref(false)
const scheduleForm = reactive({ STT : '', Thu: '',  Ngay_Thi: '',   Gio_Bat_Dau: '', Gio_Ket_Thuc: '',    Mon_Hoc: '',  So_Phong: '',  DSSV: '', DSGV: '',  Ghi_Chu: ''})
const scheduleEditingIndex = ref(null)
const isSavingSchedule = ref(false) // Loading state để prevent double submit

// Toggle modes cho input sinh viên và giảng viên
const sinhVienInputMode = ref('mssv') // 'mssv' hoặc 'ten'
const giangVienInputMode = ref('magv') // 'magv' hoặc 'ten'

// Toggle giữa mode nhập MSSV và Tên
function toggleSinhVienMode() {
  sinhVienInputMode.value = sinhVienInputMode.value === 'mssv' ? 'ten' : 'mssv'
}

function toggleGiangVienMode() {
  giangVienInputMode.value = giangVienInputMode.value === 'magv' ? 'ten' : 'magv'
}

// Parse input sinh viên (hỗ trợ dấu phẩy và xuống dòng)
function parseSinhVienInput(input) {
  if (!input) return []
  // Tách theo dấu phẩy hoặc xuống dòng
  return input
    .split(/[,\n]/)
    .map(s => s.trim())
    .filter(Boolean)
}

// Parse input giảng viên (hỗ trợ dấu phẩy và xuống dòng)
function parseGiangVienInput(input) {
  if (!input) return []
  return input
    .split(/[,\n]/)
    .map(s => s.trim())
    .filter(Boolean)
}



function openScheduleForm(item = null, idx = null) {
  if (item) { 
    Object.assign(scheduleForm, item); 
    scheduleEditingIndex.value = idx;
  } else { 
    Object.assign(scheduleForm, { 
      STT : '',
      Thu: '',
      Ngay_Thi: '',
      Gio_Bat_Dau: '',
      Gio_Ket_Thuc: '', 
      Mon_Hoc: '', 
      So_Phong: '',
      DSSV: '',
      DSGV: '', 
      Ghi_Chu: '' 
    });
    scheduleEditingIndex.value = null;
  }
  showScheduleModal.value = true;
}
function closeScheduleForm(){ showScheduleModal.value = false }

async function deleteSchedule(id) {
  if (!id) {
    showNotify('Không xác định id lịch thi để xóa.', 'error');
    return;
  }
  if (!(await showConfirm('Bạn có chắc chắn muốn xóa lịch thi này không?'))) return;
  try {
    await axios.delete(`/schedules/delete/${encodeURIComponent(id)}`);
    await fetchSchedules();
    showNotify('✅ Xóa lịch thi thành công!', 'success');
  } catch (err) {
    console.error('❌ Lỗi khi xóa lịch thi:', err.response?.data || err.message);
    showNotify('❌ Xóa thất bại: ' + (err.response?.data?.message || err.message), 'error');
  }
}

// =============================
// IMPORT LỊCH THI TỪ EXCEL
// =============================
const showImportModal = ref(false)
const importTab = ref('template') // 'template' or 'upload'
const selectedFile = ref(null)
const importing = ref(false)
const importResult = ref(null)
const isDragging = ref(false)
const fileInput = ref(null)

function openImportModal() {
  showImportModal.value = true
  importTab.value = 'template'
  selectedFile.value = null
  importResult.value = null
  isDragging.value = false
}

function closeImportModal() {
  showImportModal.value = false
  selectedFile.value = null
  importResult.value = null
  isDragging.value = false
}

function handleFileSelect(event) {
  const file = event.target.files[0]
  if (file) {
    selectedFile.value = file
    importResult.value = null
  }
}

function handleFileDrop(event) {
  isDragging.value = false
  const file = event.dataTransfer.files[0]
  if (file && (file.name.endsWith('.xlsx') || file.name.endsWith('.xls'))) {
    selectedFile.value = file
    importResult.value = null
  } else {
    alert('❌ Vui lòng chọn file Excel (.xlsx hoặc .xls)')
  }
}

async function uploadFile() {
  if (!selectedFile.value) {
    alert('❌ Vui lòng chọn file')
    return
  }

  const formData = new FormData()
  formData.append('file', selectedFile.value)

  try {
    importing.value = true
    importResult.value = null

    const response = await axios.post('/schedules/import', formData, {
      headers: {
        'Content-Type': 'multipart/form-data'
      }
    })

    importResult.value = response.data

    if (response.data.success) {
      // Reload schedules
      await fetchSchedules()
      
      // Reset sau 3 giây
      setTimeout(() => {
        selectedFile.value = null
        importResult.value = null
        importTab.value = 'template'
      }, 3000)
    }

  } catch (error) {
    console.error('❌ Lỗi import:', error)
    importResult.value = {
      success: false,
      message: error.response?.data?.message || 'Lỗi khi import file: ' + error.message
    }
  } finally {
    importing.value = false
  }
}
    

function onScheduleFileAdd(e){ const f = e.target.files[0]; if(!f) return alert('File đã chọn: ' + f.name) }

// =============================
// ĐIỂM DANH
// =============================
const showAttendanceModal = ref(false)
const attendanceForm = reactive({ mssv:'', name:'', subject:'', date:'', time:'', status:'Có mặt' })
function openAttendanceForm(){ Object.assign(attendanceForm, { mssv:'', name:'', subject:'', date:'', time:'', status:'Có mặt' }); showAttendanceModal.value = true }
function closeAttendanceForm(){ showAttendanceModal.value = false }
function saveAttendance(){ attendance.value.push({ id: Date.now(), ...attendanceForm }); closeAttendanceForm() }
async function deleteAttendance(i){ 
  if(await showConfirm('Xóa điểm danh này?')) attendance.value.splice(i,1) 
}

// =============================
// KẾT QUẢ & XUẤT FILE
// =============================
const attendanceSummary = computed(() => {
  const map = {}
  attendance.value.forEach(a => {
    const key = a.mssv + '||' + a.subject
    if(!map[key]) map[key] = { mssv: a.mssv, name: a.name, subject: a.subject, present:0, total:0 }
    map[key].total++
    if(a.status && a.status.toLowerCase().includes('có')) map[key].present++
  })
  return Object.values(map)
})

function exportResults(){
  const rows = attendanceSummary.value
  if(rows.length===0) return alert('Không có dữ liệu để xuất')
  const header = ['MSSV','Tên','Môn','Số buổi dự','Tổng buổi']
  const csv = [header].concat(rows.map(r=>[r.mssv,r.name,r.subject,r.present,r.total]))
    .map(r=>r.map(cell=>`"${String(cell).replace(/"/g,'""')}"`).join(','))
    .join('\n')
  const blob = new Blob([csv], {type: 'text/csv;charset=utf-8;'});
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a'); a.href = url; a.download = 'attendance_summary.csv'; a.click(); URL.revokeObjectURL(url)
}

// =============================
// ĐĂNG XUẤT
// =============================
function logout() {
  axios.post('/logout')
    .then(() => window.location.href = '/login')
    .catch(err => {
      console.error('Logout failed', err)
      alert('Đăng xuất thất bại. Kiểm tra console.')
    })
}

// =============================
// LOCALSTORAGE & WATCHERS
// =============================
watch(lecturers, (v)=> localStorage.setItem('lecturers', JSON.stringify(v)), {deep:true})
watch(students, (v)=> {
  console.log('Students changed:', v); // Debug log
  localStorage.setItem('students', JSON.stringify(v))
}, {deep:true, immediate: true})
watch(schedules, (v)=> localStorage.setItem('schedules', JSON.stringify(v)), {deep:true})
watch(attendance, (v)=> localStorage.setItem('attendance', JSON.stringify(v)), {deep:true})

// 🧽 Xóa sạch dữ liệu cũ (chạy 1 lần duy nhất)
if (!localStorage.getItem('dataReset2025')) {
  localStorage.clear()
  localStorage.setItem('dataReset2025', 'done')
}

// Khởi tạo dữ liệu từ localStorage (nếu có)
const init = ()=>{
  try{
    const ls = JSON.parse(localStorage.getItem('lecturers')||'null')
    if(ls) {
      // Sắp xếp theo MaGV tăng dần
      ls.sort((a, b) => {
        const maGVA = String(a.MaGV || '').toUpperCase()
        const maGVB = String(b.MaGV || '').toUpperCase()
        return maGVA.localeCompare(maGVB, 'vi')
      })
      lecturers.value = ls
    }
    const ss = JSON.parse(localStorage.getItem('students')||'null'); if(ss) students.value = ss
    const sc = JSON.parse(localStorage.getItem('schedules')||'null'); if(sc) schedules.value = sc
    const at = JSON.parse(localStorage.getItem('attendance')||'null'); if(at) attendance.value = at
  }catch(e){ console.warn('load fail', e) }
}
init()



const showStudentListModal = ref(false)
const studentListDetail = ref([])

function showStudentList(dssvRaw) {
  // dssvRaw có thể là chuỗi mã số, hoặc mảng
  let mssvArr = []
  if (Array.isArray(dssvRaw)) {
    mssvArr = dssvRaw
  } else if (typeof dssvRaw === 'string') {
    mssvArr = dssvRaw.split(',').map(s => s.trim()).filter(Boolean)
  }
  // Lấy thông tin sinh viên từ students
  studentListDetail.value = students.value.filter(sv => mssvArr.includes(sv.Mssv))
  showStudentListModal.value = true
}

const showLecturerListModal = ref(false)
const lecturerListDetail = ref([])

function showLecturerList(dsgvRaw) {
  let magvArr = []
  if (Array.isArray(dsgvRaw)) {
    magvArr = dsgvRaw
  } else if (typeof dsgvRaw === 'string') {
    magvArr = dsgvRaw.split(',').map(s => s.trim()).filter(Boolean)
  }
  lecturerListDetail.value = lecturers.value.filter(gv => magvArr.includes(gv.MaGV))
  showLecturerListModal.value = true
}

// =============================
// NHẬP SINH VIÊN TỪ FILE EXCEL
// =============================
function importStudentsFromExcel(e) {
  const file = e.target.files[0]
  if (!file) return
  const reader = new FileReader()
  reader.onload = async (evt) => {
    const data = new Uint8Array(evt.target.result)
    const workbook = XLSX.read(data, { type: 'array' })
    const sheetName = workbook.SheetNames[0]
    const worksheet = workbook.Sheets[sheetName]
    const json = XLSX.utils.sheet_to_json(worksheet, { defval: '', raw: false })
    
    // Debug: In ra tất cả tên cột từ file Excel
    console.log('Tên các cột trong file Excel:', Object.keys(json[0] || {}))
    
    function normalize(str) {
      // Chuyển về chữ thường và bỏ dấu
      let result = String(str || '')
        .toLowerCase()
        .normalize('NFD')
        .replace(/[\u0300-\u036f]/g, '');
      
      // Xóa các ký tự đặc biệt và khoảng trắng
      result = result.replace(/[^a-z0-9]/g, '');
      
      console.log(`Normalize: "${str}" -> "${result}"`)
      return result;
    }
    const colMap = {
      mssv: ['mssv','masosinhvien','masinhvien','masv','masosvien'],
      hoten: ['hoten','hovaten','hotensinhvien','hotenhs','ten','hovaten','hoten'],
      ngaysinh: ['ngaysinh','ngaysinh','ngaysinhsv','ngaysinhhs'],
      lop: ['lop','malop','tenlop'],
      khoa: ['khoa','tenkhoa','khoavien'],
      bac: ['bac','bacdaotao','hedaotao','bac dao tao','he dao tao','bậc','bacdaotao','bậc đào tạo','hệ đào tạo','Bậc','Bac']
    }
    
    // Debug: In ra kết quả mapping cho mỗi trường
    console.log('=== DEBUG COLUMN MAPPING ===')
    for (const field in colMap) {
      console.log(`${field}:`, Object.keys(json[0] || {}).find(col => 
        colMap[field].includes(col) || 
        colMap[field].includes(normalize(col))
      ))
    }
    function getKey(row, keys) {
      for (const k of keys) {
        for (const col in row) {
          if (normalize(col) === k) return row[col]
        }
      }
      return ''
    }
    let added = 0
    let failed = 0
    let errorMsgs = []
    let newStudents = [] // Mảng chứa sinh viên mới để thêm vào database

    for (const row of json) {
      const mssv = getKey(row, colMap.mssv)
      const hoten = getKey(row, colMap.hoten)
      const ngaysinh = getKey(row, colMap.ngaysinh)
      const lop = getKey(row, colMap.lop)
      const khoa = getKey(row, colMap.khoa)
      const bac = getKey(row, colMap.bac)
      
      if (!mssv || !hoten) continue
      
      // Chuyển đổi ngày sinh dd/mm/yyyy -> yyyy-mm-dd nếu cần
      let ngaySinhDb = ngaysinh
      if (ngaysinh && /^\d{2}\/\d{2}\/\d{4}$/.test(ngaysinh)) {
        const [d, m, y] = ngaysinh.split('/')
        ngaySinhDb = `${y}-${m.padStart(2,'0')}-${d.padStart(2,'0')}`
      }

      // Debug: In ra dữ liệu trước khi tạo object student
      console.log('Raw data for student:', {
        mssv, hoten, ngaysinh, lop, khoa, bac
      })
      
      const student = {
        Mssv: mssv,
        Ho_va_ten: hoten,
        Email: null,
        Ngay_Sinh: ngaySinhDb || null,
        Lop: lop || null,
        Khoa: khoa || null,
        Bac: bac || null
      }
      
      // Debug: In ra object student trước khi gửi lên server
      console.log('Student object to save:', student)

      try {
        // Thêm sinh viên vào database
        await axios.post('/students/add', student)
        // Nếu thành công, thêm vào danh sách sinh viên hiển thị
        students.value.push(student)
        added++
      } catch (err) {
        failed++
        let msg = err.response?.data?.message || err.message || 'Lỗi không xác định'
        if (err.response?.data?.error) {
          msg += ': ' + err.response.data.error
        }
        errorMsgs.push(`MSSV: ${student.Mssv} - ${msg}`)
        console.error('Lỗi khi lưu sinh viên:', student, err.response?.data || err.message)
      }
    }

    let alertMsg = ''
    if (added > 0) {
      alertMsg += `✅ Đã thêm thành công ${added} sinh viên vào database!\n`
      await fetchStudents() // Cập nhật lại danh sách từ server
    }
    if (failed > 0) {
      alertMsg += `❌ ${failed} sinh viên lỗi không lưu được:\n` + errorMsgs.join('\n')
    }
    if (!alertMsg) {
      alertMsg = '❌ Không thêm được sinh viên nào vào database. Kiểm tra lại tên cột và dữ liệu file Excel!'
    }
    alert(alertMsg)
  }
  reader.readAsArrayBuffer(file)
}
</script>


<style scoped>
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: linear-gradient(90deg,#95bce1, #0c7de7);
  color: #ffffff;
  padding: 9px 20px;
  font-weight: 400;
  font-size: 24px;
  letter-spacing: 1px;
  box-shadow: 0 2px 6px rgba(154, 189, 237, 0.25);
}

.header h1 {
  margin: 0;
  margin-left: 0px; 
  font-size: 22px;
}
.hamburger{
  display:none;
  width:36px;
  height:32px;
  align-items:center;
  justify-content:center;
  flex-direction:column;
  gap:5px;
  background:transparent;
  border:none;
  cursor:pointer;
  margin-right:12px;
  padding:4px;
}
.hamburger span{
  display:block;
  width:22px;
  height:2px;
  background:#ffffff;
  border-radius:2px;
}
.mobile-menu{
  display:none;
  background:#ffffff;
  box-shadow:0 8px 20px rgba(0,0,0,0.08);
  border-radius:0 0 12px 12px;
  padding:10px 14px;
  position:sticky;
  top:0;
  z-index:15;
}
.mobile-menu ul{list-style:none;padding:0;margin:0;display:flex;flex-direction:column;gap:6px}
.mobile-menu li{padding:12px 10px;border-radius:10px;border:1px solid #e5e9ee;color:#234;cursor:pointer}
.mobile-menu li.active{background:#e9f3ff;border-color:#b8d6f7;color:#0b4f85;font-weight:700}
.ppp{
  color:#ffffff;
  font-size: 38px;
  font-weight: 800; /* ✅ in đậm hơn */
   margin-left: 20px;
}
:root{ --blue1:#2f86d1; --blue2:#2ba0f2; }
*{box-sizing:border-box}
.admin-root{font-family: 'Segoe UI', Roboto, Arial; color:#1b1b1b}
.topbar{height:76px;background:linear-gradient(90deg,var(--blue1),var(--blue2));color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 24px}
.topbar .brand{font-size:22px;font-weight:700;letter-spacing:1px}
.layout{display:flex}
.mobile-only{display:none !important}
.desktop-only{display:flex}
.sidebar{width:300px;background:#e6eaec;padding-top:20px;min-height:calc(100vh - 76px)}
.sidebar nav ul{list-style:none;padding:0;margin:0}
.sidebar nav li{padding:18px 20px;color:#234;cursor:pointer;border-left:6px solid transparent}
.sidebar nav li.active{background:#dcdfe0;border-left-color:#2f86d1;color:#0b4f85}
.content{flex:1;padding:36px 48px;background:#eef4f6;min-height:calc(100vh - 76px)}
.card{background:#fff;border-radius:12px;padding:28px;box-shadow:0 8px 20px rgba(0,0,0,0.06)}
.card-title{text-align:center;color:#1e73be;margin-bottom:18px;font-weight:700;font-size:28px}
.page-body{padding:10px 6px}
.toolbar{display:flex;align-items:center;justify-content:space-between;gap:10px;flex-wrap:wrap;margin-bottom:12px}
.toolbar.desktop-only{justify-content:flex-end}
.toolbar.desktop-only .search{margin-right:auto}

.search{flex:0 1 360px; max-width:360px}
.search input{padding:8px 12px;border-radius:6px;border:1px solid #ccc;width:100%}
.actions{display:flex;align-items:center;justify-content:flex-end;gap:8px;flex-shrink:0}
.actions button,.actions .file-btn{background:#2ea44f;color:#fff;border:none;padding:10px 16px;border-radius:6px;cursor:pointer;font-weight:700}
.actions button:first-child{background:#0d6efd}
.actions button,.actions .file-btn{min-width:110px;text-align:center}
.actions .file-btn{margin-left:0}
.toolbar-lecturers{flex-wrap:nowrap}
.toolbar-lecturers .search{flex:1 1 260px}
.toolbar-lecturers .actions{flex:0 0 auto;justify-content:flex-end}
.toolbar-lecturers .actions button:first-child{background:#0d6efd}
.toolbar-students .actions{justify-content:flex-start}
.dropdown{position:relative}
.dropdown > button{background:#0d6efd;color:#fff;border:none;padding:10px 16px;border-radius:6px;cursor:pointer;font-weight:700;min-width:120px}
  .dropdown-menu {
    position: absolute;
    top: 110%;
    left: 0;
    display: flex;
    flex-direction: column;
    gap: 8px;
    background: #fff;
    border: 1px solid #e1e6ed;
    border-radius: 8px;
    box-shadow: 0 8px 16px rgba(0,0,0,0.08);
    padding: 10px;
    min-width: 220px;
    z-index: 30;
    right: auto;
  }
.dropdown-menu .excel-btn{margin-left:0; text-align:left}
.dropdown-menu button{width:100%; text-align:left}
.file-btn input{display:none}
.table{width:100%;border-collapse:collapse;margin-top:8px}
.table th{background:#f6f8f9;text-align:center;padding:12px;border-bottom:1px solid #eee;color:#2b5f86}
.table td{padding:12px;border-bottom:1px solid #f1f4f5}
.actions-cell button{margin-right:6px;padding:6px 8px;border-radius:6px;border:1px solid #ccc;background:#fff;cursor:pointer}
.empty{text-align:center;padding:18px;color:#777}
.avatar-cell img{width:48px;height:48px;border-radius:6px;object-fit:cover}
.modal{position:fixed;inset:0;background:rgba(10,10,10,0.35);display:flex;align-items:center;justify-content:center;overflow-y:auto;padding:20px 0;z-index:9998}
/* Centered modal card: no positional offsets, flex-centering only */
.modal-card{background:#fff;padding:18px;border-radius:10px;min-width:320px;max-height:90vh;overflow-y:auto;width:100%;max-width:960px;box-shadow:0 14px 40px rgba(0,0,0,0.25)}
/* Wide variant for schedule form */
.modal-card.wide{max-width:1100px;width:100%}
.form-row{display:flex;flex-direction:column;margin:8px 0}
.form-row label{font-weight:600;color:#2b7ab8;margin-bottom:6px}
.form-row input[type='text'], .form-row input[type='email'], .form-row input[type='password'], .form-row input[type='date'], .form-row input[type='time'], .form-row select{padding:8px;border:1px solid #ddd;border-radius:6px}
.form-row.actions{display:flex;gap:8px;justify-content:flex-end}
.form-grid{display:grid;grid-template-columns:repeat(2,1fr);gap:12px}
.form-grid .full{grid-column:1/-1}
button {
  transition: all 0.2s ease;
  font-weight: 600;
}

/* === CÁC LOẠI NÚT CHÍNH === */
.actions button,
.actions .file-btn {
  background: #28a745; /* Xanh lá - thêm */
  color: #fff;
  border: none;
  padding: 8px 12px;
  border-radius: 6px;
  cursor: pointer;
  margin-left: 8px;
}

.actions button:hover,
.actions .file-btn:hover {
  background: #218838;
}

/* === NÚT TRONG BẢNG Chỉnh sửa === */
.actions-cell button {
  margin-right: 6px;
  padding: 6px 10px;
  border-radius: 6px;
  border: none;
  color: #fff;
  cursor: pointer;
  font-weight: 500;
}

/* Sửa */
.actions-cell button:nth-child(1) {
  background-color: #0d6efd; /* Xanh nước biển - Sửa */
}
.actions-cell button:nth-child(1):hover {
  background-color: #0b5ed7;
}

/* Xóa */
.actions-cell button:nth-child(2) {
  background-color: #dc3545; /* Đỏ - Xóa */
}
.actions-cell button:nth-child(2):hover {
  background-color: #bb2d3b;
}

/* === NÚT TRONG MODAL === */
.form-row.actions button:first-child {
  background-color: #28a745; /* Lưu */
  color: #fff;
}
.form-row.actions button:first-child:hover {
  background-color: #218838;
}

.form-row.actions button:last-child {
  background-color: #6c757d; /* Hủy */
  color: #fff;
}
.form-row.actions button:last-child:hover {
  background-color: #5a6268;
}

/* === NÚT ĐĂNG XUẤT === */
.logout {
  background: #f5f5f5;
  border: none;
  color: rgb(18, 17, 17);
  font-size: 18px;
  padding: 6px 12px;
  border-radius: 50%;
  cursor: pointer;
  transition: 0.2s;
}
.logout:hover {
  background: #c82333;
  transform: scale(1.1);
}

/* === NÚT XUẤT FILE === */
.toolbar button {
  background-color: #0d6efd;
  color: white;
  border: none;
  border-radius: 6px;
  padding: 8px 14px;
  font-weight: 600;
}
.toolbar button:hover {
  background-color: #0b5ed7;
}

.excel-btn {
  background: #28a745;
  color: #fff;
  border: none;
  padding: 8px 12px;
  border-radius: 6px;
  cursor: pointer;
  margin-left: 8px;
  font-weight: 600;
  display: inline-block;
  transition: background 0.2s;
}
.excel-btn:hover {
  background: #218838;
}
.excel-btn input[type='file'] {
  display: none;
}

.delete-all-btn {
  background-color: #dc3545;
  color: white;
  margin-left: 8px;
}

.delete-all-btn:hover {
  background-color: #c82333;
}

/* === TOAST NOTIFICATION === */
.toast {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  padding: 14px 20px;
  border-radius: 8px;
  font-weight: 600;
  font-size: 14px;
  z-index: 9999;
  max-width: 90vw;
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.15);
  animation: slideUp 0.3s ease;
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateX(-50%) translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateX(-50%) translateY(0);
  }
}

.toast-success {
  background: #d4edda;
  color: #155724;
  border: 1px solid #c3e6cb;
}

.toast-error {
  background: #f8d7da;
  color: #721c24;
  border: 1px solid #f5c6cb;
}

.toast-info {
  background: #d1ecf1;
  color: #0c5460;
  border: 1px solid #bee5eb;
}

</style>

<style scoped>
@media (max-width: 900px) {
    /* Mobile: modal fills viewport width, content scrolls inside */
    .modal-card, .modal-card.wide {
      width: 95vw;
      max-width: 95vw;
    }
  .hamburger { display: flex; }
  .sidebar { display: none; }
  .mobile-menu { display: block; }
  .header {
    padding: 6px 12px;
  }
  .ppp {
    font-size: 5px;
    line-height: 1.2;
    text-align: left;
    margin: 0;
    padding: 0 8px;
  }
  .mobile-only{display:flex !important}
  .desktop-only{display:none !important}
  .layout {
    flex-direction: column;
  }
  .content {
    padding: 16px 4px;
  }
  .toolbar {
    flex-wrap: wrap;
    align-items: center;
  }
  .toolbar .search {
    flex: 1 1 100%;
  }
  .toolbar .actions {
    flex: 0 0 auto;
    justify-content: flex-end;
  }
  .toolbar-lecturers {
    flex-wrap: wrap;
  }
  .toolbar-lecturers .search {
    flex: 1 1 100%;
  }
  .toolbar-lecturers .actions {
    flex: 0 0 auto;
    justify-content: flex-end;
  }
  .toolbar-students {
    flex-wrap: nowrap;
    gap: 6px;
  }
  .toolbar-students .search {
    flex: 1 1 auto;
    min-width: 150px;
  }
  .toolbar-students .actions {
    width: auto;
    justify-content: flex-end;
    flex-shrink: 0;
  }
  .toolbar-students .dropdown > button {
    width: auto;
    padding: 6px 8px;
    font-size: 13px;
    min-width: auto;
    white-space: nowrap;
    max-width: 80px;
  }
  .toolbar-students .dropdown > button {
    width: 160px;
    text-align: left;
  }
  .dropdown-menu {
    position: static;
    width: 100%;
    box-shadow: none;
    border: 1px solid #e1e6ed;
    border-radius: 10px;
    padding: 6px;
    text-align: left;
    max-width: 100%;
    min-width: auto;
  }
  .dropdown-menu *,
  .dropdown-menu button,
  .dropdown-menu .excel-btn {
    text-align: left !important;
  }
  .dropdown-menu button,
  .dropdown-menu .excel-btn {
    background: transparent !important;
    color: #0d6efd;
    border: none;
    padding: 6px 8px;
    text-align: left !important;
    display: block;
    width: 100%;
    min-width: 0;
    font-weight: 600;
    border-radius: 6px;
    font-size: 12px;
    overflow-wrap: break-word;
    word-break: break-word;
    line-height: 1.4;
  }
  .dropdown-menu button:hover,
  .dropdown-menu .excel-btn:hover {
    background: #f5f8fc;
  }
  .sidebar nav li {
    padding: 10px 8px;
    font-size: 15px;
  }
  .card-title {
    font-size: 18px;
  }
  .table, .table th, .table td {
    font-size: 13px;
    padding: 6px 2px;
  }
  .form-row label {
    font-size: 14px;
  }
  .modal-card, .modal-card.wide {
    min-width: 95vw;
    padding: 6px;
  }
  .form-row input, .form-row select, .form-row textarea {
    font-size: 14px;
    padding: 6px;
  }
  .toolbar .search input {
    width: 100%;
    min-width: 0;
  }
  .form-grid {
    grid-template-columns: 1fr;
    gap: 6px;
  }
  .avatar-cell img {
    width: 32px;
    height: 32px;
  }
}

/* === ERROR STYLING === */
.error-input {
  border-color: #dc3545 !important;
  background-color: #fff5f5;
}

.error-text {
  color: #dc3545;
  font-size: 12px;
  margin-top: 4px;
  margin-bottom: 0;
}

.alert-success {
  padding: 12px;
  background-color: #d4edda;
  border: 1px solid #c3e6cb;
  border-radius: 4px;
  color: #155724;
  margin-bottom: 16px;
}

/* === TABLE WRAPPER FOR SCROLL === */
.table-wrapper {
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  background: #fff;
}

.table-wrapper::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

.table-wrapper::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 4px;
}

.table-wrapper::-webkit-scrollbar-thumb {
  background: #888;
  border-radius: 4px;
}

.table-wrapper::-webkit-scrollbar-thumb:hover {
  background: #555;
}

/* Fix table header sticky */
.table-wrapper .table thead th {
  position: sticky;
  top: 0;
  background: #f6f8f9;
  z-index: 10;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}

/* === RESPONSIVE TABLE TO CARD (<=768px) === */
@media (max-width: 768px) {
  .table {
    border: none;
    width: 100%;
    font-size: 14px;
  }
  .table thead {
    display: none;
  }
  .table tbody {
    display: block;
    width: 100%;
  }
  .table tr {
    display: block;
    background: #fff;
    border: 1px solid #e3e8ee;
    border-radius: 12px;
    box-shadow: 0 6px 14px rgba(0,0,0,0.06);
    margin-bottom: 12px;
    overflow: hidden;
  }
  .table td {
    display: flex;
    align-items: center;
    gap: 6px;
    flex-wrap: nowrap;
    padding: 10px 12px;
    border: none !important;
    font-size: 14px;
    text-align: left !important;
    white-space: nowrap;
  }
  .table td:last-child {
    border-bottom: none;
  }
  .table td::before {
    content: attr(data-label);
    font-weight: 700;
    color: #0b4f85;
    min-width: 110px;
    flex-shrink: 0;
    margin-right: 6px;
    white-space: nowrap;
  }
  .table td a,
  .table td button {
    white-space: nowrap;
  }
  .actions-cell {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
  }
}

/* Hide less important columns on very small screens */
@media (max-width: 640px) {
  .table tr td:nth-child(9),
  .table tr td:nth-child(10) {
    display: none;
  }
  .table td {
    font-size: 13px;
  }
  .table td::before {
    min-width: 90px;
    font-size: 13px;
  }
}
</style>