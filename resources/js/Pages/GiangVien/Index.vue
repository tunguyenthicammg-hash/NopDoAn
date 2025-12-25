
<template>
        <header class="header" >
      <button class="hamburger-btn" @click="toggleSidebar" v-show="!isSidebarOpen">
        <span class="hamburger-line"></span>
        <span class="hamburger-line"></span>
        <span class="hamburger-line"></span>
      </button>
      <h1 class="ppp">HỆ THỐNG HỖ TRỢ GIÁM THỊ ĐIỂM DANH SINH VIÊN BẰNG HÌNH ẢNH</h1>
       <div class="sidebar-logout">
  <button class="logout" @click="logout">
    <i class="fa-solid fa-right-from-bracket"></i>
  </button>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
</div>
    </header>

  <!-- Toast notification (mobile-friendly, desktop unaffected) -->
  <div v-if="toast.show" class="toast" :class="['toast-' + toast.type]">
    <span>{{ toast.message }}</span>
  </div>

  <!-- Confirm dialog (mobile-friendly) -->
  <div v-if="confirmDialog.show" class="confirm-overlay" @click.self="handleConfirm(false)">
    <div class="confirm-box">
      <p class="confirm-message">{{ confirmDialog.message }}</p>
      <div class="confirm-actions">
        <button class="btn-confirm-yes" @click="handleConfirm(true)">Đồng ý</button>
        <button class="btn-confirm-no" @click="handleConfirm(false)">Hủy</button>
      </div>
    </div>
  </div>

  <div class="app">

    <!-- SIDEBAR -->
    <div v-if="isSidebarOpen" class="sidebar-overlay" @click="toggleSidebar"></div>
    <aside class="sidebar" :class="{ 'sidebar-open': isSidebarOpen }">
      

      <nav class="menu">
        <button @click="activeTab = 'info'; isSidebarOpen = false" :class="{ active: activeTab === 'info' }">
          Thông tin giảng viên
        </button>
        <button @click="activeTab = 'schedule'; isSidebarOpen = false" :class="{ active: activeTab === 'schedule' }">
          Lịch gác thi & Phòng gác thi
        </button>
        <button @click="activeTab = 'attendance'; isSidebarOpen = false" :class="{ active: activeTab === 'attendance' }">
          Điểm danh sinh viên
        </button>
      

         <button @click="activeTab = 'password'; isSidebarOpen = false" :class="{ active: activeTab === 'password' }">
          Đổi mật khẩu
        </button>
      </nav>

      
    </aside>

    <!-- MAIN CONTENT -->
    <main class="main-content">
      <!-- Thông tin cá nhân -->
     <section v-if="activeTab === 'info'" class="centered-section">
  <h2 class="tt">THÔNG TIN GIẢNG VIÊN</h2>

  <form class="info-form" @submit.prevent="updateInfo">
    <div class="form-row">
      <label>Họ và tên:</label>
      <input type="text" v-model="teacher.Ten" required />
    </div>

    <div class="form-row">
      <label>Email:</label>
      <input type="email" v-model="teacher.Email" required />
    </div>

    <div class="form-row">
      <label>Số điện thoại:</label>
      <input type="text" v-model="teacher.Sdt" required />
    </div>

    <div class="form-row">
      <label>Khoa:</label>
      <input type="text" v-model="teacher.Bo_Mon" required />
    </div>

    <button type="submit" class="btn-update">Cập nhật thông tin</button>
  </form>
</section>

      <!-- Lịch gác thi -->
       <section v-if="activeTab === 'schedule'" class="schedule-section">
        <h2>LỊCH VÀ PHÒNG GÁC THI</h2>

        <!-- Tabs để filter theo status -->
        <div class="schedule-tabs">
          <button 
            @click="scheduleStatusFilter = 'pending'" 
            :class="{ 'tab-active': scheduleStatusFilter === 'pending' }"
            class="tab-button"
          >
            🟡 Chờ xác nhận ({{ pendingSchedules.length }})
          </button>
          <button 
            @click="scheduleStatusFilter = 'confirmed'" 
            :class="{ 'tab-active': scheduleStatusFilter === 'confirmed' }"
            class="tab-button"
          >
            🟢 Đã xác nhận ({{ confirmedSchedules.length }})
          </button>
          <button 
            @click="scheduleStatusFilter = 'rejected'" 
            :class="{ 'tab-active': scheduleStatusFilter === 'rejected' }"
            class="tab-button"
          >
            🔴 Đã từ chối ({{ rejectedSchedules.length }})
          </button>
          <button 
            @click="scheduleStatusFilter = 'all'" 
            :class="{ 'tab-active': scheduleStatusFilter === 'all' }"
            class="tab-button"
          >
            📋 Tất cả ({{ allSchedules.length }})
          </button>
        </div>

        <!-- Desktop Table View -->
        <div class="table-wrapper desktop-view">
        <table class="table schedule-table">
        <thead>
          <tr>
            <th class="border border-gray-300 px-2 py-1">STT</th>
            <th class="border border-gray-300 px-2 py-1">Mã môn</th>
            <th class="border border-gray-300 px-2 py-1">Môn học</th>
            <th class="border border-gray-300 px-2 py-1">Ngày thi</th>
            <th class="border border-gray-300 px-2 py-1">Thời gian</th>
            <th class="border border-gray-300 px-2 py-1">Phòng thi</th>
            <th class="border border-gray-300 px-2 py-1">Vai trò</th>
            <th class="border border-gray-300 px-2 py-1">Trạng thái</th>
            <th class="border border-gray-300 px-2 py-1">Điểm danh</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(item, index) in filteredSchedules"
            :key="item.id"
            class="hover:bg-gray-50"
          >
            <td class="border border-gray-300 px-2 py-1 text-center">{{ index + 1 }}</td>
            <td class="border border-gray-300 px-2 py-1 text-center">{{ item.ma_mon }}</td>
            <td class="border border-gray-300 px-2 py-1">{{ item.mon_hoc }}</td>
            <td class="border border-gray-300 px-2 py-1 text-center">
              {{ formatDate(item.ngay_thi) }}<br>
              <small style="color: #666;">({{ item.thu }})</small>
            </td>
            <td class="border border-gray-300 px-2 py-1 text-center">
              {{ item.gio_bat_dau }} - {{ item.gio_ket_thuc }}
            </td>
            <td class="border border-gray-300 px-2 py-1 text-center">
              {{ item.so_phong }}<br>
              <small style="color: #666;">{{ item.toa_nha }}</small>
            </td>
            <td class="border border-gray-300 px-2 py-1 text-center">
              <span :class="item.role === 'Trưởng phòng' ? 'badge-leader' : 'badge-supervisor'">
                {{ item.role }}
              </span>
            </td>
            <td class="border border-gray-300 px-2 py-1 text-center">
              <span :class="getStatusClass(item.status)">
                {{ getStatusText(item.status) }}
              </span>
              <div v-if="item.confirmed_at" style="font-size: 11px; color: #666; margin-top: 4px;">
                {{ formatDateTime(item.confirmed_at) }}
              </div>
            </td>
            <td class="border border-gray-300 px-2 py-1 text-center">
              <!-- Nút điểm danh chỉ hiện với status confirmed -->
              <div v-if="item.status === 'confirmed'" class="attendance-cell">
                <div class="attendance-stats">
                  <span class="attendance-count">{{ item.attended_count || 0 }}/{{ item.total_students || 0 }}</span>
                </div>
                <div class="attendance-actions">
                  <button 
                    @click="openAttendanceModal(item)" 
                    class="btn-attendance"
                    title="Điểm danh sinh viên"
                  >
                    📝 Điểm danh
                  </button>
                  <button 
                    @click="viewStudentList(item)" 
                    class="btn-view-list"
                    title="Xem danh sách sinh viên"
                  >
                    👥 Xem DS
                  </button>
                </div>
              </div>
              <!-- Status pending - hiện nút xác nhận -->
              <div v-else-if="item.status === 'pending'" class="action-buttons">
                <button 
                  @click="confirmSchedule(item.id)" 
                  class="btn-confirm"
                  title="Xác nhận lịch gác"
                >
                  ✓ Xác nhận
                </button>
              </div>
              <!-- Status rejected hoặc khác -->
              <div v-else>
                <button 
                  @click="viewScheduleDetail(item)" 
                  class="btn-view"
                >
                  👁 Chi tiết
                </button>
              </div>
            </td>
          </tr>

          <tr v-if="filteredSchedules.length === 0">
            <td colspan="9" class="text-center text-gray-500 py-4">
              {{ getEmptyMessage() }}
            </td>
          </tr>
  </tbody>
</table>
        </div>

        <!-- Mobile Card View -->
        <div class="mobile-view">
          <div v-if="filteredSchedules.length === 0" class="empty-state-mobile">
            <div class="empty-icon-mobile">📋</div>
            <p>{{ getEmptyMessage() }}</p>
          </div>
          
          <div
            v-for="(item, index) in filteredSchedules"
            :key="item.id"
            class="schedule-card"
          >
            <div class="card-header" :class="{
              'card-header-pending': item.status === 'pending',
              'card-header-confirmed': item.status === 'confirmed',
              'card-header-rejected': item.status === 'rejected'
            }">
              <span class="card-number">STT {{ index + 1 }}</span>
              <span :class="getStatusClass(item.status)" class="card-status">
                {{ getStatusText(item.status) }}
              </span>
            </div>

            <div class="card-body">
              <div class="card-row">
                <span class="card-label">Mã môn</span>
                <span class="card-value">{{ item.ma_mon }}</span>
              </div>

              <div class="card-row">
                <span class="card-label">Môn học</span>
                <span class="card-value">{{ item.mon_hoc }}</span>
              </div>

              <div class="card-row">
                <span class="card-label">Ngày thi</span>
                <span class="card-value">{{ formatDate(item.ngay_thi) }} ({{ item.thu }})</span>
              </div>

              <div class="card-row">
                <span class="card-label">Thời gian</span>
                <span class="card-value">{{ item.gio_bat_dau }} - {{ item.gio_ket_thuc }}</span>
              </div>

              <div class="card-row">
                <span class="card-label">Phòng thi</span>
                <span class="card-value">{{ item.so_phong }} - {{ item.toa_nha }}</span>
              </div>

              <div class="card-row">
                <span class="card-label">Vai trò</span>
                <span :class="item.role === 'Trưởng phòng' ? 'badge-leader' : 'badge-supervisor'" class="card-badge">
                  {{ item.role }}
                </span>
              </div>

              <div class="card-row" v-if="item.status === 'confirmed'">
                <span class="card-label">Điểm danh</span>
                <span class="card-value">{{ item.attended_count || 0 }}/{{ item.total_students || 0 }}</span>
              </div>
            </div>

            <div class="card-footer">
              <!-- Nút điểm danh chỉ hiện với status confirmed -->
              <div v-if="item.status === 'confirmed'" class="card-actions">
                <button 
                  @click="openAttendanceModal(item)" 
                  class="btn-card btn-card-primary"
                >
                  📝 Điểm danh
                </button>
                <button 
                  @click="viewStudentList(item)" 
                  class="btn-card btn-card-secondary"
                >
                  👥 Xem DS
                </button>
              </div>
              <!-- Status pending - hiện nút xác nhận -->
              <div v-else-if="item.status === 'pending'" class="card-actions">
                <button 
                  @click="confirmSchedule(item.id)" 
                  class="btn-card btn-card-confirm"
                >
                  ✓ Xác nhận
                </button>
              </div>
              <!-- Status rejected hoặc khác -->
              <div v-else class="card-actions">
                <button 
                  @click="viewScheduleDetail(item)" 
                  class="btn-card btn-card-info"
                >
                  👁 Chi tiết
                </button>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Điểm danh sinh viên -->
      <section v-if="activeTab === 'attendance'">
        <h2>ĐIỂM DANH SINH VIÊN</h2>
        
        <!-- Dropdown chọn lịch thi -->
        <div class="attendance-selector">
          <label for="exam-select">Chọn lịch gác thi:</label>
          <select 
            id="exam-select" 
            v-model="selectedExamForAttendance" 
            @change="loadAttendanceList"
            class="exam-dropdown desktop-only"
          >
            <option value="">-- Chọn lịch thi --</option>
            <option 
              v-for="schedule in confirmedSchedules" 
              :key="schedule.exam_id" 
              :value="schedule.exam_id"
            >
              {{ schedule.mon_hoc }} - {{ formatDate(schedule.ngay_thi) }} ({{ schedule.gio_bat_dau }}-{{ schedule.gio_ket_thuc }}) - Phòng {{ schedule.so_phong }}
            </option>
          </select>

          <!-- Mobile exam picker (single button + modal accordion) -->
          <div class="mobile-only">
            <button type="button" class="exam-picker-button" @click="showExamPickerMobile = true">
              {{ selectedExamForAttendance ? 'Đang chọn: ' + (currentExamInfo?.mon_hoc || 'Lịch thi') : 'Chọn lịch thi' }}
              <span class="exam-picker-caret">⌄</span>
            </button>

            <div v-if="showExamPickerMobile" class="exam-picker-overlay" @click.self="showExamPickerMobile = false">
              <div class="exam-picker-modal">
                <div class="exam-picker-header">
                  <span>Chọn lịch thi</span>
                  <button type="button" class="exam-picker-close" @click="showExamPickerMobile = false">×</button>
                </div>
                <div class="mobile-exam-list">
                  <div class="exam-card-mobile" v-for="schedule in confirmedSchedules" :key="schedule.exam_id">
                    <button type="button" class="exam-card-summary" @click="toggleExamAccordion(schedule.exam_id)">
                      <div class="exam-card-title">{{ schedule.mon_hoc }}</div>
                      <div class="exam-card-date">{{ formatDate(schedule.ngay_thi) }}</div>
                      <span class="exam-card-chevron" :class="{ open: expandedExamId === schedule.exam_id }">⌄</span>
                    </button>

                    <transition name="exam-accordion">
                      <div v-show="expandedExamId === schedule.exam_id" class="exam-card-detail">
                        <div class="exam-card-meta">🕒 {{ schedule.gio_bat_dau }} - {{ schedule.gio_ket_thuc }}</div>
                        <div class="exam-card-meta">🏫 Phòng {{ schedule.so_phong }}{{ schedule.toa_nha ? ' - ' + schedule.toa_nha : '' }}</div>
                        <div class="exam-card-meta exam-card-status-line">
                          <span :class="getStatusClass(schedule.status)">{{ getStatusText(schedule.status) }}</span>
                        </div>
                        <div class="exam-card-action-row">
                          <span v-if="schedule.exam_id === selectedExamForAttendance" class="exam-card-selected">Đang chọn</span>
                          <button
                            type="button"
                            class="exam-card-choose"
                            :class="{ active: schedule.exam_id === selectedExamForAttendance }"
                            @click.stop="selectExamMobile(schedule.exam_id); showExamPickerMobile = false"
                          >
                            {{ schedule.exam_id === selectedExamForAttendance ? 'Đang chọn' : 'Chọn' }}
                          </button>
                        </div>
                      </div>
                    </transition>
                  </div>
                  <div v-if="confirmedSchedules.length === 0" class="exam-card-empty">Không có lịch thi xác nhận</div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Thông tin lịch thi đã chọn -->
        <div v-if="selectedExamForAttendance && attendanceStats" class="attendance-summary-box">
          <div class="summary-item">
            <span class="label">Môn học:</span>
            <span class="value">{{ currentExamInfo?.mon_hoc }}</span>
          </div>
          <div class="summary-item">
            <span class="label">Ngày thi:</span>
            <span class="value">{{ formatDate(currentExamInfo?.ngay_thi) }} ({{ currentExamInfo?.thu }})</span>
          </div>
          <div class="summary-item">
            <span class="label">Phòng thi:</span>
            <span class="value">{{ currentExamInfo?.so_phong }} - {{ currentExamInfo?.toa_nha }}</span>
          </div>
          <div class="summary-item stats">
            <span class="label">Tình trạng:</span>
            <span class="value stats-inline">
              <span class="stat-attended">✅ Đã điểm danh: {{ attendanceStats.attended }}</span>
              <span class="stat-separator">/</span>
              <span class="stat-not-attended">⚠️ Chưa điểm danh: {{ attendanceStats.notAttended }}</span>
              <span class="stat-separator">/</span>
              <span class="stat-total">📊 Tổng: {{ attendanceStats.total }}</span>
            </span>
          </div>
        </div>

        <!-- Danh sách sinh viên -->
        <div v-if="selectedExamForAttendance" class="attendance-list-container">
          <div class="filter-tabs">
            <button 
              @click="attendanceFilter = 'all'" 
              :class="{ 'active': attendanceFilter === 'all' }"
              class="filter-tab"
            >
              📋 Tất cả ({{ attendanceListForDisplay.length }})
            </button>
            <button 
              @click="attendanceFilter = 'attended'" 
              :class="{ 'active': attendanceFilter === 'attended' }"
              class="filter-tab"
            >
              ✅ Đã điểm danh ({{ attendedStudents.length }})
            </button>
            <button 
              @click="attendanceFilter = 'not-attended'" 
              :class="{ 'active': attendanceFilter === 'not-attended' }"
              class="filter-tab"
            >
              ⚠️ Chưa điểm danh ({{ notAttendedStudents.length }})
            </button>
          </div>

          <!-- Search input (mobile-only) -->
          <div class="attendance-search mobile-only">
            <input
              v-model="attendanceSearch"
              type="text"
              class="attendance-search-input"
              placeholder="Nhập mã sinh viên (MSSV) để tìm.."
            />
          </div>
          <!-- Single student detail card view (like image 2) -->
          <div v-if="filteredAttendanceList.length === 1" class="student-detail-card">
            <div class="student-detail-row">
              <span class="detail-label">STT</span>
              <span class="detail-value">1</span>
            </div>
            <div class="student-detail-row">
              <span class="detail-label">MSSV</span>
              <span class="detail-value"><strong>{{ filteredAttendanceList[0].mssv }}</strong></span>
            </div>
            <div class="student-detail-row">
              <span class="detail-label">Họ và tên</span>
              <span class="detail-value">{{ filteredAttendanceList[0].ho_va_ten }}</span>
            </div>
            <div class="student-detail-row">
              <span class="detail-label">Lớp</span>
              <span class="detail-value">{{ filteredAttendanceList[0].lop || 'N/A' }}</span>
            </div>
            <div class="student-detail-row">
              <span class="detail-label">Trạng thái</span>
              <span class="detail-value">
                <span v-if="filteredAttendanceList[0].da_diem_danh" class="status-badge attended">✅ Đã điểm danh</span>
                <span v-else class="status-badge not-attended">⚠️ Chưa điểm danh</span>
              </span>
            </div>
            <div class="student-detail-row">
              <span class="detail-label">Thời gian</span>
              <span class="detail-value">
                <span v-if="filteredAttendanceList[0].thoi_gian_diem_danh">{{ formatDateTime(filteredAttendanceList[0].thoi_gian_diem_danh) }}</span>
                <span v-else class="text-muted">-</span>
              </span>
            </div>
            <div class="student-detail-row">
              <span class="detail-label">Phương thức</span>
              <span class="detail-value">
                <span v-if="filteredAttendanceList[0].phuong_thuc_diem_danh" class="method-badge">{{ getMethodLabel(filteredAttendanceList[0].phuong_thuc_diem_danh) }}</span>
                <span v-else class="text-muted">-</span>
              </span>
            </div>
          </div>

          <!-- Table view for multiple results (desktop only) -->
          <div v-else class="attendance-table-wrapper desktop-attendance-view">
          <table class="attendance-table">
            <thead>
              <tr>
                <th>STT</th>
                <th>MSSV</th>
                <th>Họ và tên</th>
                <th>Lớp</th>
                <th>Trạng thái</th>
                <th>Thời gian điểm danh</th>
                <th>Phương thức</th>
              </tr>
            </thead>
            <tbody>
              <tr 
                v-for="(student, index) in filteredAttendanceList" 
                :key="student.mssv"
                :class="{ 'row-attended': student.da_diem_danh, 'row-not-attended': !student.da_diem_danh }"
              >
                <td>{{ index + 1 }}</td>
                <td><strong>{{ student.mssv }}</strong></td>
                <td>{{ student.ho_va_ten }}</td>
                <td>{{ student.lop || 'N/A' }}</td>
                <td>
                  <span v-if="student.da_diem_danh" class="status-badge attended">✅ Đã điểm danh</span>
                  <span v-else class="status-badge not-attended">⚠️ Chưa điểm danh</span>
                </td>
                <td>
                  <span v-if="student.thoi_gian_diem_danh">
                    {{ formatDateTime(student.thoi_gian_diem_danh) }}
                  </span>
                  <span v-else class="text-muted">-</span>
                </td>
                <td>
                  <span v-if="student.phuong_thuc_diem_danh" class="method-badge">
                    {{ getMethodLabel(student.phuong_thuc_diem_danh) }}
                  </span>
                  <span v-else class="text-muted">-</span>
                </td>
              </tr>
              <tr v-if="filteredAttendanceList.length === 0">
                <td colspan="7" class="text-center text-muted">
                  {{ getAttendanceEmptyMessage() }}
                </td>
              </tr>
            </tbody>
          </table>
          </div>

            <!-- Mobile-only card list for all tabs -->
            <div v-if="filteredAttendanceList.length !== 1" class="mobile-attendance-view">
              <div
                v-for="(student, index) in filteredAttendanceList"
                :key="student.mssv"
                class="student-card"
                :class="{ 'student-card-attended': student.da_diem_danh }"
              >
                <div class="student-card-header">
                  <span class="student-card-number">STT {{ index + 1 }}</span>
                  <span v-if="student.da_diem_danh" class="badge-attended">✓ Đã điểm danh</span>
                  <span v-else class="badge-not-attended">○ Chưa điểm danh</span>
                </div>
                <div class="student-card-body">
                  <div class="student-card-row">
                    <span class="student-card-label">MSSV</span>
                    <span class="student-card-value"><strong>{{ student.mssv }}</strong></span>
                  </div>
                  <div class="student-card-row">
                    <span class="student-card-label">Họ và tên</span>
                    <span class="student-card-value">{{ student.ho_va_ten }}</span>
                  </div>
                  <div class="student-card-row">
                    <span class="student-card-label">Lớp</span>
                    <span class="student-card-value">{{ student.lop || 'N/A' }}</span>
                  </div>
                  <div class="student-card-row">
                    <span class="student-card-label">Thời gian</span>
                    <span class="student-card-value">
                      <span v-if="student.thoi_gian_diem_danh">{{ formatDateTime(student.thoi_gian_diem_danh) }}</span>
                      <span v-else class="text-muted">-</span>
                    </span>
                  </div>
                  <div class="student-card-row">
                    <span class="student-card-label">Phương thức</span>
                    <span class="student-card-value">
                      <span v-if="student.phuong_thuc_diem_danh" class="method-badge">{{ getMethodLabel(student.phuong_thuc_diem_danh) }}</span>
                      <span v-else class="text-muted">-</span>
                    </span>
                  </div>
                </div>
              </div>
              <div v-if="filteredAttendanceList.length === 0" class="exam-card-empty">{{ getAttendanceEmptyMessage() }}</div>
            </div>
        </div>

        <!-- Message khi chưa chọn lịch -->
        <div v-else class="empty-state">
          <div class="empty-icon">📋</div>
          <p>Vui lòng chọn lịch gác thi để xem danh sách sinh viên</p>
        </div>
      </section>

      

           <!-- Đổi mật khẩu -->
<section v-if="activeTab === 'password'" class="centered-section">
  <h2 class="tt">ĐỔI MẬT KHẨU</h2>

  <form class="info-form" @submit.prevent="changePassword">
    <div class="form-row">
      <label>Mật khẩu hiện tại:</label>
      <input type="password" v-model="password.old" required />
    </div>

    <div class="form-row">
      <label>Mật khẩu mới:</label>
      <input type="password" v-model="password.new" required />
    </div>

    <div class="form-row">
      <label>Xác nhận mật khẩu mới:</label>
      <input type="password" v-model="password.confirm" required />
    </div>

    <button type="submit" class="btn-update">Đổi mật khẩu</button>
  </form>

  <p v-if="passwordMessage">{{ passwordMessage }}</p>
</section>

    </main>
  </div>

  <!-- Modal Điểm danh -->
  <div v-if="showAttendanceModal" class="modal-overlay" @click.self="closeAttendanceModal">
    <div class="modal-content attendance-modal">
      <div class="modal-header">
        <h3>📝 Điểm danh sinh viên</h3>
        <button @click="closeAttendanceModal" class="btn-close">✕</button>
      </div>
      
      <div class="modal-body">
        <div class="attendance-info">
          <p><strong>Môn học:</strong> {{ currentSchedule?.mon_hoc }}</p>
          <p><strong>Ngày thi:</strong> {{ formatDate(currentSchedule?.ngay_thi) }}</p>
          <p><strong>Phòng:</strong> {{ currentSchedule?.so_phong }}</p>
        </div>

        <!-- Tabs cho phương thức điểm danh -->
        <div class="attendance-tabs">
          <button 
            @click="switchAttendanceMethod('qr')" 
            :class="{ 'tab-active': attendanceMethod === 'qr' }"
            class="attendance-tab"
          >
            📷 Quét QR Code
          </button>
          <button 
            @click="switchAttendanceMethod('manual')" 
            :class="{ 'tab-active': attendanceMethod === 'manual' }"
            class="attendance-tab"
          >
            ⌨️ Nhập MSSV
          </button>
        </div>

        <!-- QR Scanner -->
        <div v-if="attendanceMethod === 'qr'" class="qr-scanner-container">
          <div id="qr-reader"></div>
          <p class="qr-hint">Đưa mã QR của sinh viên vào khung hình</p>
        </div>

        <!-- Manual Input -->
        <div v-else class="manual-input-container">
          <label>Nhập mã số sinh viên:</label>
          <input 
            v-model="manualMssv" 
            type="text" 
            placeholder="Ví dụ: DH52200662"
            @keyup.enter="submitManualMssv"
            class="input-mssv"
          />
          <button @click="submitManualMssv" class="btn-submit-mssv">
            Tìm kiếm
          </button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal Xác nhận thông tin sinh viên -->
  <div v-if="showStudentConfirmModal" class="modal-overlay" @click.self="closeStudentConfirmModal">
    <div class="modal-content student-confirm-modal">
      <div class="modal-header">
        <h3>✓ Xác nhận thông tin sinh viên</h3>
        <button @click="closeStudentConfirmModal" class="btn-close">✕</button>
      </div>
      
      <div class="modal-body">
        <div class="student-info-card">
          <div class="info-row">
            <span class="info-label">MSSV:</span>
            <span class="info-value">{{ foundStudent?.Mssv }}</span>
          </div>
          <div class="info-row">
            <span class="info-label">Họ và tên:</span>
            <span class="info-value">{{ foundStudent?.Ho_va_ten }}</span>
          </div>
          <div class="info-row">
            <span class="info-label">Lớp:</span>
            <span class="info-value">{{ foundStudent?.Lop || 'N/A' }}</span>
          </div>
          <div class="info-row">
            <span class="info-label">Ngày sinh:</span>
            <span class="info-value">{{ formatDate(foundStudent?.Ngay_sinh) || 'N/A' }}</span>
          </div>
          <div class="info-row">
            <span class="info-label">Phương thức:</span>
            <span class="info-value">{{ foundStudent?.phuong_thuc === 'qr_code' ? '📷 QR Code' : '⌨️ Nhập thủ công' }}</span>
          </div>
        </div>

        <div class="confirm-buttons">
          <button @click="confirmAttendance" class="btn-confirm-attendance">
            ✓ Xác nhận điểm danh
          </button>
          <button @click="closeStudentConfirmModal" class="btn-cancel">
            ✕ Hủy
          </button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal Danh sách sinh viên -->
  <div v-if="showStudentListModal" class="modal-overlay" @click.self="closeStudentListModal">
    <div class="modal-content student-list-modal">
      <div class="modal-header">
        <h3>👥 Danh sách sinh viên</h3>
        <button @click="closeStudentListModal" class="btn-close">✕</button>
      </div>
      
      <div class="modal-body">
        <div class="list-info">
          <p><strong>Môn học:</strong> {{ currentSchedule?.mon_hoc }}</p>
          <p><strong>Phòng:</strong> {{ currentSchedule?.so_phong }}</p>
          <p class="attendance-summary">
            Đã điểm danh: <span class="attended">{{ studentList.filter(s => s.da_diem_danh).length }}</span> / 
            Tổng số: <span class="total">{{ studentList.length }}</span>
          </p>
        </div>

        <!-- Desktop Table View -->
        <div class="student-list-table-wrapper desktop-student-view">
        <table class="student-list-table">
          <thead>
            <tr>
              <th>STT</th>
              <th>MSSV</th>
              <th>Họ và tên</th>
              <th>Lớp</th>
              <th>Trạng thái</th>
              <th>Thời gian</th>
            </tr>
          </thead>
          <tbody>
            <tr 
              v-for="(student, index) in studentList" 
              :key="student.mssv"
              :class="{ 'attended-row': student.da_diem_danh, 'not-attended-row': !student.da_diem_danh }"
            >
              <td>{{ index + 1 }}</td>
              <td>{{ student.mssv }}</td>
              <td>{{ student.ho_va_ten }}</td>
              <td>{{ student.lop || 'N/A' }}</td>
              <td>
                <span v-if="student.da_diem_danh" class="badge-attended">✓ Đã điểm danh</span>
                <span v-else class="badge-not-attended">○ Chưa điểm danh</span>
              </td>
              <td>{{ student.thoi_gian_diem_danh ? formatDateTime(student.thoi_gian_diem_danh) : '-' }}</td>
            </tr>
          </tbody>
        </table>
        </div>

        <!-- Mobile Card View -->
        <div class="mobile-student-view">
          <div 
            v-for="(student, index) in studentList"
            :key="student.mssv"
            class="student-card"
            :class="{ 'student-card-attended': student.da_diem_danh }"
          >
            <div class="student-card-header">
              <span class="student-card-number">STT {{ index + 1 }}</span>
              <span v-if="student.da_diem_danh" class="badge-attended">✓ Đã điểm danh</span>
              <span v-else class="badge-not-attended">○ Chưa điểm danh</span>
            </div>

            <div class="student-card-body">
              <div class="student-card-row">
                <span class="student-card-label">MSSV</span>
                <span class="student-card-value">{{ student.mssv }}</span>
              </div>

              <div class="student-card-row">
                <span class="student-card-label">Họ và tên</span>
                <span class="student-card-value">{{ student.ho_va_ten }}</span>
              </div>

              <div class="student-card-row">
                <span class="student-card-label">Lớp</span>
                <span class="student-card-value">{{ student.lop || 'N/A' }}</span>
              </div>

              <div class="student-card-row" v-if="student.thoi_gian_diem_danh">
                <span class="student-card-label">Thời gian</span>
                <span class="student-card-value">{{ formatDateTime(student.thoi_gian_diem_danh) }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { router } from '@inertiajs/vue3'
import axios from 'axios'
import { ref, onMounted, computed, nextTick, reactive } from 'vue'
// tab hiện tại
const activeTab = ref('info')
const isSidebarOpen = ref(false)

// Toast notification (mobile friendly, desktop unaffected)
const toast = reactive({ show: false, message: '', type: 'info' })
let toastTimer = null
const notify = (message, type = 'info') => {
  toast.message = message
  toast.type = type
  toast.show = true
  if (toastTimer) clearTimeout(toastTimer)
  toastTimer = setTimeout(() => {
    toast.show = false
  }, 3000)
}

// Confirm dialog (mobile friendly)
const confirmDialog = reactive({ show: false, message: '', resolver: null })
const askConfirm = (message) => {
  return new Promise((resolve) => {
    confirmDialog.message = message
    confirmDialog.show = true
    confirmDialog.resolver = resolve
  })
}
const handleConfirm = (result) => {
  if (confirmDialog.resolver) confirmDialog.resolver(result)
  confirmDialog.show = false
  confirmDialog.message = ''
  confirmDialog.resolver = null
}

const toggleSidebar = () => {
  isSidebarOpen.value = !isSidebarOpen.value
}
 // mật khẩu
 const password = ref({ old: '', new: '', confirm: '' })
 const passwordMessage = ref('')
 
 const changePassword = async () => {
   if (password.value.new !== password.value.confirm) {
     passwordMessage.value = '❌ Mật khẩu xác nhận không khớp.'
     return
   }
   try {
     const res =  await axios.post('/giangvien/doimatkhau', {
       old_password: password.value.old,
       new_password: password.value.new,
       // optional confirmation if backend expects it
      new_password_confirmation: password.value.confirm
     })
     passwordMessage.value = res.data.message ||'✅ Đổi mật khẩu thành công.'
     password.value = { old: '', new: '', confirm: '' }
   } catch (err) {
     passwordMessage.value = (err.response && err.response.data && err.response.data.message) || '❌ Đổi mật khẩu thất bại.'
     console.error(err)
   }
 }
 
// dữ liệu giảng viên
const teacher = ref({
  Ten: '',
  Email: '',
  Sdt: '',
  Bo_Mon: ''
})
function logout() {
  router.post(route('logout'))}
// dữ liệu lịch gác thi & kết quả điểm danh
const schedules = ref([])
const allSchedules = ref([])
const scheduleStatusFilter = ref('pending') // pending, confirmed, rejected, all
const results = ref([])

// Điểm danh modal states
const showAttendanceModal = ref(false)
const showStudentConfirmModal = ref(false)
const showStudentListModal = ref(false)
const attendanceMethod = ref('qr') // 'qr' or 'manual'
const currentSchedule = ref(null)
const manualMssv = ref('')
const foundStudent = ref(null)
const studentList = ref([])
const isScanning = ref(false)

// Attendance page states
const selectedExamForAttendance = ref('')
const attendanceListForDisplay = ref([])
const attendanceFilter = ref('all') // 'all', 'attended', 'not-attended'
const currentExamInfo = ref(null)
const expandedExamId = ref(null)
const showExamPickerMobile = ref(false)

// Computed properties để filter lịch theo status
const pendingSchedules = computed(() => 
  allSchedules.value.filter(s => s.status === 'pending')
)
const confirmedSchedules = computed(() => 
  allSchedules.value.filter(s => s.status === 'confirmed')
)
const rejectedSchedules = computed(() => 
  allSchedules.value.filter(s => s.status === 'rejected')
)

const filteredSchedules = computed(() => {
  if (scheduleStatusFilter.value === 'all') return allSchedules.value
  if (scheduleStatusFilter.value === 'pending') return pendingSchedules.value
  if (scheduleStatusFilter.value === 'confirmed') return confirmedSchedules.value
  if (scheduleStatusFilter.value === 'rejected') return rejectedSchedules.value
  return allSchedules.value
})

// Attendance page computed
const attendedStudents = computed(() => 
  attendanceListForDisplay.value.filter(s => s.da_diem_danh)
)

const notAttendedStudents = computed(() => 
  attendanceListForDisplay.value.filter(s => !s.da_diem_danh)
)

const filteredAttendanceList = computed(() => {
  let base = attendanceListForDisplay.value
  if (attendanceFilter.value === 'attended') base = attendedStudents.value
  if (attendanceFilter.value === 'not-attended') base = notAttendedStudents.value
  if (!attendanceSearch.value?.trim()) return base
  const q = attendanceSearch.value.trim().toLowerCase()
  return base.filter(s => (s.mssv && String(s.mssv).toLowerCase().includes(q)))
})

const attendanceStats = computed(() => {
  if (!attendanceListForDisplay.value.length) return null
  return {
    attended: attendedStudents.value.length,
    notAttended: notAttendedStudents.value.length,
    total: attendanceListForDisplay.value.length
  }
})

// Search state
const attendanceSearch = ref('')

// Helper functions
const getStatusClass = (status) => {
  const classes = {
    'pending': 'status-pending',
    'confirmed': 'status-confirmed',
    'rejected': 'status-rejected'
  }
  return classes[status] || ''
}

const getStatusText = (status) => {
  const texts = {
    'pending': '🟡 Chờ xác nhận',
    'confirmed': '🟢 Đã xác nhận',
    'rejected': '🔴 Đã từ chối'
  }
  return texts[status] || status
}

const getEmptyMessage = () => {
  const messages = {
    'pending': 'Không có lịch gác chờ xác nhận',
    'confirmed': 'Chưa có lịch gác nào được xác nhận',
    'rejected': 'Chưa có lịch gác nào bị từ chối',
    'all': 'Không có lịch gác thi nào'
  }
  return messages[scheduleStatusFilter.value] || 'Không có dữ liệu'
}

const formatDate = (dateStr) => {
  if (!dateStr) return ''
  const date = new Date(dateStr)
  return date.toLocaleDateString('vi-VN')
}

const formatDateTime = (dateStr) => {
  if (!dateStr) return ''
  const date = new Date(dateStr)
  return date.toLocaleString('vi-VN')
}

const viewScheduleDetail = (item) => {
  notify(`Chi tiết lịch gác:\nMôn: ${item.mon_hoc}\nNgày: ${formatDate(item.ngay_thi)}\nGiờ: ${item.gio_bat_dau} - ${item.gio_ket_thuc}\nPhòng: ${item.so_phong} (${item.toa_nha})\nVai trò: ${item.role}\nTrạng thái: ${getStatusText(item.status)}`, 'info')
}

// Xác nhận lịch gác
const confirmSchedule = async (id) => {
  const ok = await askConfirm('Bạn có chắc chắn muốn xác nhận lịch gác thi này không?')
  if (!ok) return

  try {
    // Get CSRF token
    const token = document.querySelector('meta[name="csrf-token"]')?.getAttribute('content');
    console.log('CSRF Token:', token ? 'Found' : 'NOT FOUND');
    
    const res = await axios.post(`/giangvien/phan-cong/${id}/confirm`, {}, {
      headers: {
        'X-CSRF-TOKEN': token
      }
    })
    if (res.data.success) {
      notify('✅ Đã xác nhận lịch gác thi thành công!', 'success')
      await fetchSchedules() // Reload danh sách
    }
  } catch (err) {
    console.error('Lỗi khi xác nhận:', err)
    console.error('Error response:', err.response)
    notify('❌ Không thể xác nhận lịch gác: ' + (err.response?.data?.message || err.message), 'error')
  }
}

// Từ chối lịch gác
const rejectSchedule = async (id) => {
  const lyDo = prompt('Vui lòng nhập lý do từ chối (không bắt buộc):')
  if (lyDo === null) return // User clicked Cancel

  try {
    const res = await axios.post(`/giangvien/phan-cong/${id}/reject`, {
      ly_do: lyDo
    })
    if (res.data.success) {
      notify('✅ Đã từ chối lịch gác thi', 'success')
      await fetchSchedules() // Reload danh sách
    }
  } catch (err) {
    console.error('Lỗi khi từ chối:', err)
    notify('❌ Không thể từ chối lịch gác: ' + (err.response?.data?.message || err.message), 'error')
  }
}

// ==================== ĐIỂM DANH FUNCTIONS ====================

// Mở modal điểm danh
const openAttendanceModal = (schedule) => {
  currentSchedule.value = schedule
  showAttendanceModal.value = true
  attendanceMethod.value = 'qr' // Default to QR scan
}

// Đóng modal điểm danh
const closeAttendanceModal = () => {
  showAttendanceModal.value = false
  currentSchedule.value = null
  manualMssv.value = ''
  stopQRScanner()
}

// Chuyển đổi phương thức điểm danh
const switchAttendanceMethod = (method) => {
  attendanceMethod.value = method
  if (method === 'qr') {
    nextTick(() => startQRScanner())
  } else {
    stopQRScanner()
  }
}

// Start QR Scanner
let html5QrcodeScanner = null
const startQRScanner = async () => {
  if (isScanning.value) return
  
  try {
    const { Html5Qrcode } = await import('html5-qrcode')
    const qrReaderElement = document.getElementById('qr-reader')
    
    if (!qrReaderElement) {
      console.error('QR reader element not found')
      return
    }
    
    html5QrcodeScanner = new Html5Qrcode("qr-reader")
    isScanning.value = true
    
    await html5QrcodeScanner.start(
      { facingMode: "environment" }, // Use back camera
      {
        fps: 10,
        qrbox: { width: 250, height: 250 }
      },
      onScanSuccess,
      onScanFailure
    )
  } catch (err) {
    console.error('Error starting QR scanner:', err)
    notify('❌ Không thể khởi động camera. Vui lòng kiểm tra quyền truy cập camera.', 'error')
    isScanning.value = false
  }
}

// Stop QR Scanner
const stopQRScanner = async () => {
  if (html5QrcodeScanner && isScanning.value) {
    try {
      await html5QrcodeScanner.stop()
      html5QrcodeScanner.clear()
      html5QrcodeScanner = null
      isScanning.value = false
    } catch (err) {
      console.error('Error stopping scanner:', err)
    }
  }
}

// QR Scan success handler
const onScanSuccess = (decodedText, decodedResult) => {
  console.log('QR Code detected:', decodedText)
  
  // Parse QR: MSSV_TEN_DD.MM.YYYY (ví dụ: DH52200662_NGUYENMINHHIEN_30.09.2004)
  const parts = decodedText.split('_')
  if (parts.length >= 1) {
    const mssv = parts[0].trim()
    stopQRScanner()
    lookupStudent(mssv, 'qr_code')
  } else {
    notify('❌ Mã QR không hợp lệ!', 'error')
  }
}

// QR Scan failure handler
const onScanFailure = (error) => {
  // Ignore continuous scan errors
}

// Nhập MSSV thủ công
const submitManualMssv = () => {
  if (!manualMssv.value.trim()) {
    notify('⚠️ Vui lòng nhập MSSV', 'warning')
    return
  }
  lookupStudent(manualMssv.value.trim(), 'manual')
}

// Tìm kiếm sinh viên theo MSSV
const lookupStudent = async (mssv, method) => {
  try {
    const res = await axios.get(`/giangvien/sinh-vien/${mssv}`)
    if (res.data.success && res.data.student) {
      foundStudent.value = {
        ...res.data.student,
        phuong_thuc: method
      }
      showStudentConfirmModal.value = true
      showAttendanceModal.value = false
    } else {
      notify('❌ Không tìm thấy sinh viên với MSSV: ' + mssv, 'error')
    }
  } catch (err) {
    console.error('Lỗi khi tìm sinh viên:', err)
    notify('❌ Không thể tìm sinh viên: ' + (err.response?.data?.message || err.message), 'error')
  }
}

// Xác nhận điểm danh sinh viên
const confirmAttendance = async () => {
  if (!foundStudent.value || !currentSchedule.value) return
  
  try {
    const res = await axios.post('/giangvien/diem-danh', {
      lich_thi_id: currentSchedule.value.exam_id,
      mssv: foundStudent.value.Mssv,
      phuong_thuc: foundStudent.value.phuong_thuc
    })
    
    if (res.data.success) {
      notify('✅ Đã điểm danh thành công cho sinh viên: ' + foundStudent.value.Ho_va_ten, 'success')
      closeStudentConfirmModal()
      await fetchSchedules() // Refresh để update số lượng đã điểm danh
    }
  } catch (err) {
    console.error('Lỗi khi điểm danh:', err)
    notify('❌ Không thể điểm danh: ' + (err.response?.data?.message || err.message), 'error')
  }
}

// Đóng modal xác nhận sinh viên
const closeStudentConfirmModal = () => {
  showStudentConfirmModal.value = false
  foundStudent.value = null
  manualMssv.value = ''
  // Quay lại modal điểm danh
  showAttendanceModal.value = true
  if (attendanceMethod.value === 'qr') {
    nextTick(() => startQRScanner())
  }
}

// Xem danh sách sinh viên
const viewStudentList = async (schedule) => {
  try {
    const res = await axios.get(`/giangvien/lich-thi/${schedule.exam_id}/sinh-vien`)
    if (res.data.success) {
      studentList.value = res.data.students || []
      currentSchedule.value = schedule
      showStudentListModal.value = true
    }
  } catch (err) {
    console.error('Lỗi khi lấy danh sách sinh viên:', err)
    notify('❌ Không thể lấy danh sách sinh viên', 'error')
  }
}

// Đóng modal danh sách sinh viên
const closeStudentListModal = () => {
  showStudentListModal.value = false
  studentList.value = []
  currentSchedule.value = null
}

// ==================== ATTENDANCE PAGE FUNCTIONS ====================

// Load danh sách sinh viên khi chọn lịch thi
const loadAttendanceList = async () => {
  if (!selectedExamForAttendance.value) {
    attendanceListForDisplay.value = []
    currentExamInfo.value = null
    return
  }

  try {
    // Lấy thông tin lịch thi
    const schedule = confirmedSchedules.value.find(s => s.exam_id == selectedExamForAttendance.value)
    currentExamInfo.value = schedule

    // Lấy danh sách sinh viên
    const res = await axios.get(`/giangvien/lich-thi/${selectedExamForAttendance.value}/sinh-vien`)
    if (res.data.success) {
      attendanceListForDisplay.value = res.data.students || []
    }
  } catch (err) {
    console.error('Lỗi khi tải danh sách:', err)
    notify('❌ Không thể tải danh sách sinh viên', 'error')
    attendanceListForDisplay.value = []
  }
}

// Mobile select helper
const selectExamMobile = (examId) => {
  selectedExamForAttendance.value = examId
  loadAttendanceList()
}

// Toggle accordion for mobile exam cards
const toggleExamAccordion = (examId) => {
  expandedExamId.value = expandedExamId.value === examId ? null : examId
}

// Helper: Lấy label phương thức điểm danh
const getMethodLabel = (method) => {
  const labels = {
    'qr_code': '📷 QR Code',
    'manual': '⌨️ Thủ công',
    'face_recognition': '👤 Nhận diện'
  }
  return labels[method] || method
}

// Helper: Message khi list rỗng
const getAttendanceEmptyMessage = () => {
  if (attendanceFilter.value === 'attended') {
    return 'Chưa có sinh viên nào điểm danh'
  }
  if (attendanceFilter.value === 'not-attended') {
    return 'Tất cả sinh viên đã điểm danh'
  }
  return 'Không có sinh viên trong danh sách'
}

// fetch functions
const fetchTeacher = async () => {
  try {
    // điều chỉnh URL nếu backend của bạn dùng /api/...
    const res = await axios.get('/giangvien/thongtin')
    // nếu backend trả object khác, map lại cho phù hợp
    teacher.value = {
      Ten: res.data.Ten ?? res.data.ten ?? (res.data.name || ''),
      Email: res.data.Email ?? res.data.email ?? '',
      Sdt: res.data.Sdt ?? res.data.sdt ?? '',
      Bo_Mon: res.data.Bo_Mon ?? res.data.bo_mon ?? ''
    }
  } catch (err) {
    console.error('fetchTeacher failed', err)
  }
}

const fetchSchedules = async () => {
   try {
    console.log('GET /giangvien/lichgac -> sending')
    const res = await axios.get('/giangvien/lichgac', {
      params: { status: 'all' } // Lấy tất cả status
    })
    console.log('res.status', res.status, 'data', res.data)
    
    if (res.data.success) {
      allSchedules.value = res.data.data || []
      schedules.value = res.data.data || []
    } else {
      allSchedules.value = res.data || []
      schedules.value = res.data || []
    }
  } catch (err) {
    console.error('fetchSchedules failed', err.response ? err.response.status : err.message, err.response ? err.response.data : '')
    allSchedules.value = []
    schedules.value = []
  }
}

const fetchResults = async () => {
  try {
    const res = await axios.get('/giangvien/ketqua')
    results.value = res.data || []
  } catch (err) {
    console.error('fetchResults failed', err)
  }
}
onMounted(() => {
  fetchSchedules()
})
// cập nhật thông tin
const updateInfo = async() => {
  try {
    await axios.put('/giangvien/capnhat', teacher.value)
    notify('Cập nhật thành công.', 'success')
  } catch (err) {
    console.error('updateInfo failed', err)
    notify('Cập nhật thất bại.', 'error')
  }
}

// gửi ảnh AWS điểm danh
const captureImage = () => {
  // Lấy hình ảnh từ webcam gửi đến backend (AWS Rekognition)
  console.log('Gửi ảnh điểm danh tới AWS')
}
onMounted(() => {
  fetchTeacher()
  fetchSchedules()
  fetchResults()
})
</script>

<style scoped>
.app {
  display: flex;
  min-height: 100vh;
}

.sidebar {
  width: 260px;
  background: #d5d9db;
  color: rgb(255, 255, 255);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}


.menu {
  display: flex;
  flex-direction: column;
  
}

.menu button {
  background: none;
  color: #022445;
  text-align: left;
  padding: 12px 25px;
  border: none;
  cursor: pointer;
  font-size: 18px;
}

.menu button.active {
  background: rgb(249, 249, 249);
}



.logout {
  background: #f3f3f5;
  border: none;
  color: #0c7de7;
  width: 40px;
  height: 40px;
  margin: 20px;
  border-radius: 50%;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
}

.logout:hover {
  background: #dbe9fa;
  transform: scale(1.05);
}

.logout i {
  font-size: 18px;
}

.main-content {
  flex: 1;
  background: #ecf0f1;
  padding: 30px;
}

/* Toast notification (mobile-friendly, desktop unaffected) */
.toast {
  position: fixed;
  top: 60px;
  left: 50%;
  transform: translateX(-50%);
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
  padding: 12px 16px;
  z-index: 2000;
  display: flex;
  align-items: center;
  gap: 10px;
  min-width: 260px;
  max-width: 90vw;
  color: #333;
  font-weight: 600;
}

/* Confirm dialog */
.confirm-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2001;
}

.confirm-box {
  background: #fff;
  padding: 16px;
  border-radius: 12px;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.2);
  width: 90%;
  max-width: 360px;
}

.confirm-message {
  margin: 0 0 12px;
  font-weight: 600;
  color: #333;
  white-space: pre-line;
}

.confirm-actions {
  display: flex;
  gap: 10px;
  justify-content: flex-end;
}

.btn-confirm-yes,
.btn-confirm-no {
  flex: 1;
  padding: 10px 12px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
}

.btn-confirm-yes {
  background: #27ae60;
  color: #fff;
}

.btn-confirm-no {
  background: #bdc3c7;
  color: #2c3e50;
}

.toast-success {
  border-left: 6px solid #27ae60;
}

.toast-error {
  border-left: 6px solid #e74c3c;
}

.toast-warning {
  border-left: 6px solid #f39c12;
}

.toast-info {
  border-left: 6px solid #2980b9;
}
* {
  font-family: "Times New Roman", Times, serif;
}



h2 {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 20px;
}
.tt{
  color: #0c7de7;
  font-size: 28px;
  white-space: nowrap;
}
.h1, h2, label, li, p {
  color: #0c7de7;
  font-size: 28px;
  text-align: center;
}
.centered-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 80vh;
}





.centered-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  min-height: 80vh;
  margin-top: 15px; /* sát hơn với tiêu đề */
}

.info-form {
  display: flex;
  flex-direction: column;
  align-items: center; /* căn giữa toàn form */
  gap: 18px;
  background: #fff;
  padding: 35px 60px;
  border-radius: 16px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  width: 900px; /* form rộng hơn */
  margin-top: 15px;
}

.form-row {
  display: flex;
  align-items: center;
  gap: 20px;
  width: 100%;
  
}

.form-row label {
  font-weight: 600;
  width: 220px;
  text-align: left;
  color: #0c7de7;
  font-size: 20px;
}

.form-row input {
  flex: 1 1 100%;
  width: 100%;
  max-width: 100%;
  padding: 10px 12px;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 15px;
}

.btn-update {
  background: #0c7de7;
  color: white;
  padding: 10px 22px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: 0.2s;
  margin-top: 20px;
  align-self: center; /* ✅ căn giữa nút */
}

.btn-update:hover {
  background: #095cb3;
}







.btn-update {
  background: #27ae60;
  color: white;
  padding: 8px;
  border: none;
  border-radius: 6px;
  margin-top: 10px;
  cursor: pointer;
}

.schedule-section {
  width: 100%;
  overflow: hidden;
}

.table-wrapper {
  width: 100%;
  overflow-x: auto;
  margin-top: 15px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.table {
  width: 100%;
  border-collapse: collapse;
  background: white;
  min-width: 1200px;
}

.table th {
  background: #f6f8f9;
  text-align: center;
  padding: 12px;
  border-bottom: 1px solid #eee;
  color: #2b5f86;
}

.table td {
  padding: 12px;
  border-bottom: 1px solid #f1f4f5;
}

.attendance-box {
  display: flex;
  flex-direction: column;
  align-items: center;
}

video {
  width: 480px;
  height: 360px;
  background: black;
  margin-bottom: 15px;
}

.btn-scan {
  background: #39adf0;
  color: white;
  padding: 10px 20px;
  border-radius: 6px;
  border: none;
  cursor: pointer;
}

/* Tabs cho lịch gác thi */
.schedule-tabs {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 12px;
  margin-bottom: 20px;
}

.tab-button {
  padding: 10px 20px;
  border: 2px solid #ddd;
  background: white;
  color: #666;
  border-radius: 8px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 500;
  transition: all 0.3s ease;
}

.tab-button:hover {
  border-color: #0c7de7;
  color: #0c7de7;
}

.tab-button.tab-active {
  background: #0c7de7;
  color: white;
  border-color: #0c7de7;
}

/* Status badges */
.status-pending {
  background: #fff3cd;
  color: #856404;
  padding: 4px 12px;
  border-radius: 12px;
  font-size: 13px;
  font-weight: 600;
  display: inline-block;
}

.status-confirmed {
  background: #d4edda;
  color: #155724;
  padding: 4px 12px;
  border-radius: 12px;
  font-size: 13px;
  font-weight: 600;
  display: inline-block;
}

.status-rejected {
  background: #f8d7da;
  color: #721c24;
  padding: 4px 12px;
  border-radius: 12px;
  font-size: 13px;
  font-weight: 600;
  display: inline-block;
}

.badge-leader {
  background: #0c7de7;
  color: white;
  padding: 4px 10px;
  border-radius: 10px;
  font-size: 12px;
  font-weight: 600;
}

.badge-supervisor {
  background: #6c757d;
  color: white;
  padding: 4px 10px;
  border-radius: 10px;
  font-size: 12px;
  font-weight: 600;
}

/* Action buttons */
.action-buttons {
  display: flex;
  flex-direction: column;
  gap: 6px;
  align-items: center;
}

.btn-confirm {
  background: #28a745;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 13px;
  font-weight: 500;
  transition: background 0.2s;
}

.btn-confirm:hover {
  background: #218838;
}

.btn-reject {
  background: #dc3545;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 13px;
  font-weight: 500;
  transition: background 0.2s;
}

.btn-reject:hover {
  background: #c82333;
}

.btn-view {
  background: #17a2b8;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 13px;
  font-weight: 500;
  transition: background 0.2s;
}

.btn-view:hover {
  background: #138496;
}

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
  position: relative;
}

.header h1 {
  margin: 0;
  margin-left: 0px; 
  font-size: 22px;
}
.ppp{
  color:#ffffff;
  font-size: 38px;
  font-weight: 800; /* ✅ in đậm hơn */
   margin-left: 20px;
}

/* Hamburger button - hidden on desktop */
.hamburger-btn {
  display: none;
  background: rgba(255, 255, 255, 0.25);
  border: none;
  color: white;
  width: 50px;
  height: 50px;
  border-radius: 10px;
  cursor: pointer;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 5px;
  padding: 12px;
  transition: all 0.3s ease;
  z-index: 1001;
  flex-shrink: 0;
  margin-right: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
}

.hamburger-btn:hover {
  background: rgba(255, 255, 255, 0.4);
  transform: scale(1.08);
}

.hamburger-btn:active {
  transform: scale(0.95);
}

/* 3 sọc ngang màu trắng */
.hamburger-line {
  width: 26px;
  height: 3px;
  background-color: white;
  border-radius: 2px;
  transition: all 0.3s ease;
}

.hamburger-btn:hover .hamburger-line {
  background-color: white;
  box-shadow: 0 0 8px rgba(255, 255, 255, 0.5);
}

/* Sidebar overlay for mobile */
.sidebar-overlay {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  z-index: 998;
  animation: fadeIn 0.3s;
}

/* ==================== CARD LAYOUT STYLES ==================== */

/* Desktop view - show table */
.desktop-view {
  display: block !important;
}

/* Mobile view - hide by default */
.mobile-view {
  display: none;
}

/* Schedule Card Styles */
.schedule-card {
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  margin-bottom: 16px;
  overflow: hidden;
  transition: all 0.3s ease;
  border: 1px solid #e0e0e0;
}

.schedule-card:hover {
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
  transform: translateY(-2px);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 14px 16px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

.card-number {
  font-weight: 600;
  font-size: 15px;
}

.card-status {
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 600;
  background: rgba(255, 255, 255, 0.2);
  color: white;
}

.card-body {
  padding: 16px;
}

.card-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 0;
  border-bottom: 1px solid #f0f0f0;
}

.card-row:last-of-type {
  border-bottom: none;
}

.card-label {
  font-weight: 600;
  color: #555;
  font-size: 14px;
  flex: 0 0 auto;
  min-width: 100px;
}

.card-value {
  text-align: right;
  color: #333;
  font-size: 14px;
  font-weight: 500;
  flex: 1;
  margin-left: 10px;
  word-break: break-word;
}

.card-badge {
  display: inline-block;
  padding: 3px 8px;
  border-radius: 4px;
  font-size: 11px;
  font-weight: 600;
  color: white;
  margin-left: auto;
}

.card-footer {
  padding: 12px 16px;
  background: #f8f9fa;
  border-top: 1px solid #e0e0e0;
}

.card-actions {
  display: flex;
  gap: 10px;
}

.btn-card {
  flex: 1;
  padding: 10px 12px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  transition: all 0.2s;
  white-space: nowrap;
  text-align: center;
}

.btn-card-primary {
  background: #4caf50;
  color: white;
}

.btn-card-primary:hover {
  background: #45a049;
  transform: translateY(-2px);
}

.btn-card-secondary {
  background: #2196f3;
  color: white;
}

.btn-card-secondary:hover {
  background: #1976d2;
  transform: translateY(-2px);
}

.btn-card-confirm {
  background: #4caf50;
  color: white;
}

.btn-card-confirm:hover {
  background: #45a049;
  transform: translateY(-2px);
}

.btn-card-info {
  background: #17a2b8;
  color: white;
}

.btn-card-info:hover {
  background: #138496;
  transform: translateY(-2px);
}

.empty-state-mobile {
  text-align: center;
  padding: 60px 20px;
  color: #999;
}

.empty-icon-mobile {
  font-size: 80px;
  margin-bottom: 20px;
  opacity: 0.5;
}

.empty-state-mobile p {
  font-size: 16px;
  font-weight: 500;
  color: #666;
}

/* Mobile responsive styles */
@media (max-width: 900px) {
  /* Prevent horizontal overflow on mobile */
  body, html {
    max-width: 100vw;
    overflow-x: hidden;
  }

  .app {
    max-width: 100vw;
    overflow-x: hidden;
  }

  /* Show hamburger button on mobile */
  .hamburger-btn {
    display: flex;
  }

  /* Hide sidebar by default on mobile */
  .sidebar {
    position: fixed;
    left: -280px;
    top: 0;
    height: 100vh;
    width: 260px;
    z-index: 999;
    transition: left 0.35s cubic-bezier(0.4, 0, 0.2, 1);
    box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
    overflow-y: auto;
  }

  /* Show sidebar when open */
  .sidebar.sidebar-open {
    left: 0;
  }

  /* Show overlay when sidebar is open */
  .sidebar-overlay {
    display: block;
  }

  /* Adjust header layout */
  .header {
    padding: 10px 12px;
    gap: 8px;
    width: 100%;
    max-width: 100vw;
    box-sizing: border-box;
    overflow-x: hidden;
  }

  /* Adjust header text size */
  .ppp {
    font-size: 14px;
    margin-left: 0;
    line-height: 1.3;
    flex: 1;
    text-align: center;
  }

  .header h1 {
    font-size: 14px;
  }

  /* Adjust logout button container */
  .sidebar-logout {
    flex-shrink: 0;
  }

  /* Adjust main content */
  .main-content {
    padding: 15px;
    width: 100%;
    max-width: 100%;
    box-sizing: border-box;
    overflow-x: hidden;
  }

  section {
    width: 100%;
    max-width: 100%;
    box-sizing: border-box;
    overflow-x: hidden;
  }

  /* Make tables responsive - hide all table wrappers and desktop views */
  .table-wrapper,
  .desktop-view,
  .desktop-student-view,
  .student-list-table-wrapper,
  .student-list-modal .desktop-view,
  .student-list-modal .desktop-student-view,
  .student-list-modal table,
  .student-list-modal thead,
  .student-list-modal tbody,
  .desktop-attendance-view {
    display: none !important;
    visibility: hidden !important;
    height: 0 !important;
    overflow: hidden !important;
  }

  /* Show mobile card view on mobile */
  .mobile-view,
  .mobile-student-view {
    display: block !important;
    width: 100%;
    max-width: 100%;
    box-sizing: border-box;
  }

  /* Show attendance mobile cards */
  .mobile-attendance-view {
    display: block !important;
    width: 100%;
    max-width: 100%;
    box-sizing: border-box;
  }

  /* Hide dropdown on mobile, show card list */
  .desktop-only {
    display: none !important;
  }
  .mobile-only {
    display: block !important;
  }
  .mobile-exam-list {
    display: flex;
    flex-direction: column;
    gap: 5px;
    margin-top: 6px;
  }
  .exam-picker-button {
    width: 100%;
    padding: 10px 12px;
    background: #ffffff;
    border: 1px solid #e0e0e0;
    border-radius: 10px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.08);
    font-size: 14px;
    font-weight: 600;
    color: #2c3e50;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .exam-picker-caret {
    color: #888;
    font-size: 12px;
    margin-left: 8px;
  }
  .exam-picker-overlay {
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.35);
    z-index: 1000;
    display: flex;
    align-items: center;
    justify-content: center;
    animation: fadeIn 0.2s ease;
  }
  .exam-picker-modal {
    width: 92vw;
    max-height: 75vh;
    background: #fff;
    border-radius: 12px;
    box-shadow: 0 6px 20px rgba(0,0,0,0.2);
    overflow: hidden;
    display: flex;
    flex-direction: column;
  }
  .exam-picker-header {
    padding: 10px 12px;
    background: #f7f7f9;
    border-bottom: 1px solid #e0e0e0;
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-weight: 700;
    color: #2c3e50;
  }
  .exam-picker-close {
    border: none;
    background: transparent;
    font-size: 18px;
    line-height: 1;
    color: #888;
    cursor: pointer;
  }
  .exam-picker-modal .mobile-exam-list {
    padding: 10px;
    overflow-y: auto;
  }
  .exam-card-mobile {
    background: #fff;
    border: 1px solid #e0e0e0;
    border-radius: 8px;
    padding: 0;
    box-shadow: 0 1px 3px rgba(0,0,0,0.08);
    overflow: hidden;
  }
  .exam-card-summary {
    display: flex;
    align-items: center;
    width: 100%;
    gap: 8px;
    padding: 10px 12px;
    background: transparent;
    border: none;
    cursor: pointer;
    text-align: left;
    box-sizing: border-box;
  }
  .exam-card-title {
    font-weight: 700;
    color: #2c3e50;
    font-size: 13px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    flex: 1;
    line-height: 1.2;
  }
  .exam-card-date {
    font-size: 12px;
    color: #555;
    white-space: nowrap;
    flex-shrink: 0;
  }
  .exam-card-chevron {
    font-size: 12px;
    color: #888;
    transition: transform 0.2s ease;
    flex-shrink: 0;
  }
  .exam-card-chevron.open {
    transform: rotate(180deg);
  }
  .exam-card-detail {
    padding: 0 12px 10px;
    font-size: 12px;
    color: #555;
    line-height: 1.35;
    box-sizing: border-box;
    overflow: hidden;
  }
  .exam-card-meta {
    margin-top: 4px;
    display: flex;
    align-items: center;
    gap: 6px;
    word-break: break-word;
  }
  .exam-card-status-line {
    margin-top: 4px;
  }
  .exam-card-action-row {
    margin-top: 6px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 8px;
  }
  .exam-card-selected {
    color: #27ae60;
    font-weight: 700;
    font-size: 12px;
  }
  .exam-card-choose {
    border: 1px solid #d8d8d8;
    background: #f5f5f5;
    color: #555;
    border-radius: 999px;
    padding: 4px 10px;
    font-size: 11px;
    font-weight: 600;
    cursor: pointer;
    flex-shrink: 0;
    transition: background 0.2s, border-color 0.2s, color 0.2s;
  }
  .exam-card-choose.active {
    background: #e8f7ee;
    border-color: #27ae60;
    color: #1f8b4e;
  }
  .exam-accordion-enter-active,
  .exam-accordion-leave-active {
    transition: max-height 0.2s ease, opacity 0.2s ease;
  }
  .exam-accordion-enter-from,
  .exam-accordion-leave-to {
    max-height: 0;
    opacity: 0;
  }
  .exam-accordion-enter-to,
  .exam-accordion-leave-from {
    max-height: 200px;
    opacity: 1;
  }
  .exam-card-empty {
    text-align: center;
    color: #888;
    font-size: 12px;
    padding: 8px 0;
  }

  /* Mobile card styles */
  .schedule-card {
    margin-bottom: 12px;
    border-radius: 10px;
  }

  .card-header {
    padding: 12px 14px;
  }

  .card-header-pending {
    background: linear-gradient(135deg, #f39c12 0%, #e67e22 100%) !important;
  }

  .card-header-confirmed {
    background: linear-gradient(135deg, #27ae60 0%, #229954 100%) !important;
  }

  .card-header-rejected {
    background: linear-gradient(135deg, #e74c3c 0%, #c0392b 100%) !important;
  }

  .card-number {
    font-size: 13px;
  }

  .card-body {
    padding: 14px;
  }

  .card-row {
    padding: 5px 0;
    flex-wrap: wrap;
    align-items: center;
  }

  .card-label {
    font-size: 13px;
    min-width: 80px;
    margin-bottom: 0;
    flex-shrink: 0;
  }

  .card-value {
    text-align: left;
    font-size: 13px;
    margin-left: 10px;
    flex: 1;
    word-break: break-word;
  }

  .card-badge {
    font-size: 11px;
    margin-left: 0;
  }

  .card-footer {
    padding: 10px 14px;
  }

  .card-actions {
    flex-direction: column;
    gap: 8px;
  }

  .btn-card {
    font-size: 13px;
    padding: 10px 12px;
  }

  .table {
    font-size: 10px;
    width: 100%;
    min-width: 650px;
    table-layout: auto;
  }

  .table th,
  .table td {
    padding: 6px 3px;
    font-size: 10px;
    word-wrap: break-word;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    max-width: 120px;
  }

  .table th:first-child,
  .table td:first-child {
    max-width: 30px;
  }

  .table td:nth-child(3) {
    white-space: normal;
    max-width: 100px;
  }

  /* Prevent badges and buttons from causing overflow */
  .badge-leader,
  .badge-supervisor,
  .status-pending,
  .status-confirmed,
  .status-rejected {
    max-width: 100%;
    word-break: break-word;
    white-space: normal;
  }

  .btn-confirm,
  .btn-reject,
  .btn-view,
  .btn-attendance,
  .btn-view-list {
    max-width: 100%;
    white-space: normal;
    word-break: break-word;
  }

  .schedule-tabs {
    display: flex;
    flex-wrap: nowrap;
    gap: 4px;
    width: 100%;
    max-width: 100%;
    overflow-x: auto;
  }

  .tab-button {
    padding: 8px 8px;
    font-size: 10px;
    flex: 1 1 auto;
    min-width: 0;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  /* Attendance page mobile adjustments */
  .attendance-selector {
    padding: 15px;
    width: 100%;
    max-width: 100%;
    box-sizing: border-box;
  }

  .attendance-selector label {
    font-size: 14px;
  }

  .exam-dropdown {
    font-size: 13px;
    width: 100%;
    max-width: 100%;
    box-sizing: border-box;
  }

  .attendance-summary-box {
    padding: 15px;
    width: 100%;
    max-width: 100%;
    box-sizing: border-box;
  }

  .summary-item {
    flex-direction: row;
    align-items: center;
    gap: 6px;
    flex-wrap: wrap;
  }

  .summary-item .label {
    font-size: 13px;
    flex-shrink: 0;
  }

  .summary-item .value {
    font-size: 13px;
    flex: 1;
  }

  .summary-item.stats .value {
    flex-direction: row;
    flex-wrap: wrap;
    align-items: center;
    gap: 6px;
  }

  .stat-attended,
  .stat-not-attended,
  .stat-total {
    font-size: 12px;
  }

  .filter-tabs {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    width: 100%;
    max-width: 100%;
    gap: 6px;
  }

  .filter-tab {
    flex: 1;
    min-width: 0;
    padding: 10px 8px;
    font-size: 11px;
    box-sizing: border-box;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .attendance-table-wrapper {
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
    width: 100%;
  }

  .attendance-table {
    font-size: 10px;
    width: 100%;
    min-width: 600px;
    table-layout: auto;
  }

  .attendance-table th,
  .attendance-table td {
    padding: 6px 3px;
    font-size: 10px;
    word-wrap: break-word;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .attendance-table th:first-child,
  .attendance-table td:first-child {
    max-width: 35px;
  }

  .attendance-table td:nth-child(3) {
    white-space: normal;
  }

  .status-badge {
    font-size: 9px;
    padding: 3px 6px;
  }

  .method-badge {
    font-size: 9px;
    padding: 2px 6px;
  }

  .attendance-count {
    font-size: 10px;
    padding: 2px 6px;
  }

  .btn-attendance,
  .btn-view-list {
    font-size: 10px;
    padding: 5px 8px;
  }

  .btn-confirm,
  .btn-reject,
  .btn-view {
    font-size: 10px;
    padding: 5px 10px;
  }

  /* Adjust title on mobile */
  .tt {
    font-size: 18px;
    white-space: normal;
    text-align: center;
    word-wrap: break-word;
  }

  /* Schedule section title on mobile */
  .schedule-section h2 {
    font-size: 16px !important;
    white-space: nowrap !important;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  /* Adjust form on mobile */
  .info-form {
    width: 100%;
    max-width: 100%;
    padding: 20px 15px;
    box-sizing: border-box;
  }

  .form-row {
    flex-direction: column;
    gap: 8px;
  }

  .form-row label {
    width: 100%;
    text-align: left;
    font-size: 16px;
  }

  /* Adjust schedule tabs */
  .schedule-tabs {
    flex-direction: column;
  }

  .tab-button {
    width: 100%;
  }

  /* Adjust filter tabs */
  .filter-tabs {
    flex-direction: column;
  }

  .filter-tab {
    width: 100%;
    min-width: auto;
  }

  /* Adjust modal for mobile */
  .modal-content {
    width: 95%;
    max-height: 85vh;
    max-width: 100%;
  }

  .student-list-modal {
    width: 95%;
    max-height: 80vh;
  }

  .list-info {
    padding: 12px;
    margin-bottom: 15px;
  }

  .list-info p {
    margin: 3px 0;
    font-size: 12px;
  }

  .attendance-summary {
    font-size: 12px;
    margin-top: 8px;
  }

  /* Hide table and wrapper on mobile */
  .desktop-student-view,
  .student-list-table-wrapper {
    display: none !important;
  }

  /* Show mobile student card view on mobile */
  .mobile-student-view {
    display: block !important;
  }

  /* Toast on mobile */
  .toast {
    top: 50px;
    width: 90%;
    max-width: 90%;
    font-size: 13px;
    padding: 10px 14px;
  }

  .confirm-box {
    width: 92%;
    max-width: 92%;
  }

  .student-card {
    margin-bottom: 10px;
    border-radius: 8px;
    width: 100%;
    box-sizing: border-box;
    overflow-x: hidden;
  }

  .student-card-header {
    padding: 10px 12px;
  }

  .student-card-number {
    font-size: 12px;
  }

  .student-card-body {
    padding: 10px 12px;
  }

  .student-card-row {
    padding: 6px 0;
  }

  .student-card-label {
    font-size: 11px;
  }

  .student-card-value {
    font-size: 11px;
  }

  .student-list-table {
    margin-top: 0;
    min-width: 500px;
  }

  .student-list-table th,
  .student-list-table td {
    padding: 8px 6px;
    font-size: 11px;
  }

  .student-list-table th {
    font-size: 10px;
  }

  .badge-attended,
  .badge-not-attended {
    padding: 3px 6px;
    font-size: 10px;
  }

  /* Adjust attendance actions */
  .attendance-actions {
    flex-direction: column;
    width: 100%;
  }

  .btn-attendance,
  .btn-view-list {
    width: 100%;
  }

  /* Smaller logout button on mobile */
  .logout {
    width: 35px;
    height: 35px;
    margin: 8px;
  }

  .logout i {
    font-size: 16px;
  }
}

/* Extra small screens */
@media (max-width: 480px) {
  .ppp {
    font-size: 11px;
  }

  .hamburger-btn {
    width: 45px;
    height: 45px;
    font-size: 20px;
    margin-right: 8px;
  }

  .logout {
    width: 32px;
    height: 32px;
    margin: 5px;
  }

  .logout i {
    font-size: 14px;
  }

  .schedule-tabs {
    grid-template-columns: 1fr;
  }

  .table {
    min-width: 600px;
    font-size: 9px;
  }

  .table th,
  .table td {
    padding: 5px 2px;
    font-size: 9px;
  }

  .attendance-table {
    min-width: 550px;
    font-size: 9px;
  }

  .attendance-table th,
  .attendance-table td {
    padding: 5px 2px;
    font-size: 9px;
  }

  .filter-tabs {
    gap: 8px;
  }

  .summary-item .value {
    font-size: 12px;
  }

  .schedule-tabs {
    flex-direction: column;
  }

  .tab-button {
    flex: 1 1 100%;
  }
}

/* Tablet responsive */
@media (max-width: 1024px) and (min-width: 901px) {
  .sidebar {
    width: 220px;
  }

  .ppp {
    font-size: 28px;
  }

  .main-content {
    padding: 20px;
  }
}

/* ==================== ATTENDANCE STYLES ==================== */

.attendance-cell {
  display: flex;
  flex-direction: column;
  gap: 8px;
  align-items: center;
}

.attendance-stats {
  font-size: 13px;
  color: #666;
  font-weight: 600;
}

.attendance-count {
  background: #e3f2fd;
  padding: 2px 8px;
  border-radius: 12px;
  color: #1976d2;
}

.attendance-actions {
  display: flex;
  gap: 6px;
}

.btn-attendance {
  background: #4caf50;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 12px;
  font-weight: 500;
  transition: all 0.2s;
}

.btn-attendance:hover {
  background: #45a049;
  transform: translateY(-1px);
}

.btn-view-list {
  background: #2196f3;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 12px;
  font-weight: 500;
  transition: all 0.2s;
}

.btn-view-list:hover {
  background: #1976d2;
  transform: translateY(-1px);
}

/* Modal Overlay */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  animation: fadeIn 0.2s;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.modal-content {
  background: white;
  border-radius: 12px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.3);
  max-width: 600px;
  width: 90%;
  max-height: 90vh;
  overflow-y: auto;
  animation: slideUp 0.3s;
}

@keyframes slideUp {
  from {
    transform: translateY(50px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  border-bottom: 2px solid #e0e0e0;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border-radius: 12px 12px 0 0;
}

.modal-header h3 {
  margin: 0;
  font-size: 20px;
  font-weight: 600;
}

.btn-close {
  background: rgba(255, 255, 255, 0.2);
  color: white;
  border: none;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  cursor: pointer;
  font-size: 20px;
  line-height: 1;
  transition: background 0.2s;
}

.btn-close:hover {
  background: rgba(255, 255, 255, 0.3);
}

.modal-body {
  padding: 20px;
}

/* Attendance Modal */
.attendance-info {
  background: #f5f5f5;
  padding: 15px;
  border-radius: 8px;
  margin-bottom: 20px;
}

.attendance-info p {
  margin: 5px 0;
  font-size: 14px;
}

.attendance-tabs {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.attendance-tab {
  flex: 1;
  padding: 12px;
  border: 2px solid #e0e0e0;
  background: white;
  border-radius: 8px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 500;
  transition: all 0.2s;
}

.attendance-tab:hover {
  border-color: #667eea;
  background: #f8f9ff;
}

.attendance-tab.tab-active {
  border-color: #667eea;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

/* QR Scanner */
.qr-scanner-container {
  text-align: center;
}

#qr-reader {
  width: 100%;
  max-width: 400px;
  margin: 0 auto;
  border: 3px dashed #667eea;
  border-radius: 12px;
  overflow: hidden;
}

.qr-hint {
  margin-top: 15px;
  color: #666;
  font-size: 14px;
  font-style: italic;
}

/* Manual Input */
.manual-input-container {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.manual-input-container label {
  font-weight: 600;
  color: #333;
  font-size: 14px;
}

.input-mssv {
  padding: 12px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 16px;
  transition: border-color 0.2s;
}

.input-mssv:focus {
  outline: none;
  border-color: #667eea;
}

.btn-submit-mssv {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  font-weight: 600;
  transition: transform 0.2s;
}

.btn-submit-mssv:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

/* Student Confirm Modal */
.student-info-card {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  padding: 20px;
  border-radius: 12px;
  margin-bottom: 20px;
}

.info-row {
  display: flex;
  justify-content: space-between;
  padding: 10px 0;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}

.info-row:last-child {
  border-bottom: none;
}

.info-label {
  font-weight: 600;
  color: #555;
}

.info-value {
  font-weight: 500;
  color: #333;
}

.confirm-buttons {
  display: flex;
  gap: 10px;
}

.btn-confirm-attendance {
  flex: 1;
  background: #4caf50;
  color: white;
  border: none;
  padding: 14px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  font-weight: 600;
  transition: all 0.2s;
}

.btn-confirm-attendance:hover {
  background: #45a049;
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(76, 175, 80, 0.4);
}

.btn-cancel {
  flex: 1;
  background: #f44336;
  color: white;
  border: none;
  padding: 14px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  font-weight: 600;
  transition: all 0.2s;
}

.btn-cancel:hover {
  background: #da190b;
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(244, 67, 54, 0.4);
}

/* Student List Modal */
.student-list-modal {
  max-width: 900px;
}

.list-info {
  background: #f5f5f5;
  padding: 15px;
  border-radius: 8px;
  margin-bottom: 20px;
}

.list-info p {
  margin: 5px 0;
  font-size: 14px;
}

.attendance-summary {
  margin-top: 10px;
  font-weight: 600;
  font-size: 15px;
}

.attendance-summary .attended {
  color: #4caf50;
}

.attendance-summary .total {
  color: #2196f3;
}

.student-list-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 15px;
}

.student-list-table-wrapper {
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
  width: 100%;
}

/* Student Card Styles */
.student-card {
  background: white;
  border-radius: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  margin-bottom: 12px;
  overflow: hidden;
  border: 1px solid #e0e0e0;
}

.student-card-attended {
  border-left: 4px solid #4caf50;
}

.student-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 14px;
  background: #f5f5f5;
  border-bottom: 1px solid #e0e0e0;
}

.student-card-number {
  font-weight: 600;
  font-size: 13px;
  color: #333;
}

.student-card-body {
  padding: 12px 14px;
}

.student-card-row {
  display: flex;
  justify-content: space-between;
  padding: 8px 0;
  border-bottom: 1px solid #f5f5f5;
}

.student-card-row:last-child {
  border-bottom: none;
}

.student-card-label {
  font-weight: 600;
  color: #666;
  font-size: 12px;
  flex: 0 0 auto;
}

.student-card-value {
  text-align: right;
  color: #333;
  font-size: 12px;
  font-weight: 500;
  flex: 1;
  margin-left: 10px;
  word-break: break-word;
}

/* Desktop view - show table by default */
.desktop-student-view {
  display: block !important;
}

/* Mobile view - hide card by default */
.mobile-student-view {
  display: none;
}

/* Desktop - hide mobile attendance view */
.mobile-attendance-view {
  display: none;
}

/* Desktop - show attendance table */
.desktop-attendance-view {
  display: block;
}

.student-list-table th,
.student-list-table td {
  padding: 10px 8px;
  text-align: left;
  border-bottom: 1px solid #e0e0e0;
  font-size: 13px;
}

.student-list-table th {
  background: #f8f9fa;
  font-weight: 600;
  color: #555;
  position: sticky;
  top: 0;
}

.student-list-table tbody tr:hover {
  background: #f8f9ff;
}

.attended-row {
  background: #e8f5e9;
}

.not-attended-row {
  background: #fafafa;
}

.badge-attended {
  background: #4caf50;
  color: white;
  padding: 4px 8px;
  border-radius: 10px;
  font-size: 11px;
  font-weight: 600;
}

.badge-not-attended {
  background: #9e9e9e;
  color: white;
  padding: 4px 8px;
  border-radius: 10px;
  font-size: 11px;
  font-weight: 600;
}

/* ==================== ATTENDANCE PAGE STYLES ==================== */

.attendance-selector {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 20px;
  border-radius: 12px;
  margin-bottom: 20px;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

.attendance-selector label {
  display: block;
  color: white;
  font-weight: 600;
  margin-bottom: 10px;
  font-size: 16px;
}

.exam-dropdown {
  width: 100%;
  padding: 12px 16px;
  font-size: 15px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  background: white;
  cursor: pointer;
  transition: all 0.3s;
  font-weight: 500;
  white-space: normal;
  word-break: break-word;
}
.desktop-only { display: block; }
.mobile-only { display: none; }

.exam-dropdown:focus {
  outline: none;
  border-color: #764ba2;
  box-shadow: 0 0 0 3px rgba(118, 75, 162, 0.2);
}

.exam-dropdown option {
  padding: 10px;
  white-space: normal;
  word-break: break-word;
  line-height: 1.4;
}

.attendance-summary-box {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  padding: 20px;
  border-radius: 12px;
  margin-bottom: 20px;
  border: 2px solid #e0e0e0;
}

.summary-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 0;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}

.summary-item:last-child {
  border-bottom: none;
}

.summary-item .label {
  font-weight: 600;
  color: #555;
  flex-shrink: 0;
}

.summary-item .value {
  color: #333;
  font-weight: 500;
  flex: 1;
}

.summary-item.stats .value {
  display: flex;
  gap: 15px;
  flex-wrap: wrap;
  justify-content: flex-end;
}

.stats-inline {
  display: flex;
  align-items: center;
  gap: 8px;
  flex-wrap: wrap;
}

.stat-separator {
  color: #999;
  font-weight: 400;
  margin: 0 4px;
}

.stat-attended {
  color: #4caf50;
  font-weight: 600;
  white-space: nowrap;
}

.stat-not-attended {
  color: #ff9800;
  font-weight: 600;
  white-space: nowrap;
}

.stat-total {
  color: #2196f3;
  font-weight: 600;
  white-space: nowrap;
}

.filter-tabs {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.filter-tab {
  flex: 1;
  min-width: 200px;
  padding: 12px 20px;
  border: 2px solid #e0e0e0;
  background: white;
  border-radius: 8px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  transition: all 0.2s;
  color: #666;
}

.filter-tab:hover {
  border-color: #667eea;
  background: #f8f9ff;
  transform: translateY(-2px);
}

.filter-tab.active {
  border-color: #667eea;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4);
}

.attendance-list-container {
  background: white;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

/* Search styles */
.attendance-search {
  background: #ffffff;
  padding: 12px 16px;
  border-top: 1px solid #eef2f5;
  border-bottom: 1px solid #eef2f5;
}

.attendance-search-input {
  width: 100%;
  padding: 10px 12px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 14px;
}

.attendance-search-input:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.2);
}

/* Single student detail card */
.student-detail-card {
  margin: 16px;
  padding: 16px 18px;
  border-radius: 12px;
  background: #e8f5e9;
  border: 1px solid #c8e6c9;
}

.student-detail-row {
  display: flex;
  justify-content: space-between;
  padding: 8px 0;
  border-bottom: 1px solid rgba(0, 0, 0, 0.08);
}

.student-detail-row:last-child {
  border-bottom: none;
}

.detail-label {
  font-weight: 600;
  color: #0c7de7;
}

.detail-value {
  font-weight: 500;
  color: #2c3e50;
}

.attendance-table {
  width: 100%;
  border-collapse: collapse;
}

.attendance-table th,
.attendance-table td {
  padding: 14px 12px;
  text-align: left;
  border-bottom: 1px solid #e0e0e0;
  font-size: 14px;
}

.attendance-table th {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  font-weight: 600;
  text-transform: uppercase;
  font-size: 13px;
  letter-spacing: 0.5px;
  position: sticky;
  top: 0;
  z-index: 10;
}

.attendance-table tbody tr {
  transition: all 0.2s;
}

.attendance-table tbody tr:hover {
  background: #f8f9ff;
  transform: scale(1.01);
}

.row-attended {
  background: #e8f5e9;
}

.row-attended:hover {
  background: #c8e6c9 !important;
}

.row-not-attended {
  background: #fff3e0;
}

.row-not-attended:hover {
  background: #ffe0b2 !important;
}

.status-badge {
  display: inline-block;
  padding: 6px 14px;
  border-radius: 16px;
  font-size: 13px;
  font-weight: 600;
}

.status-badge.attended {
  background: #4caf50;
  color: white;
}

.status-badge.not-attended {
  background: #ff9800;
  color: white;
}

.method-badge {
  background: #2196f3;
  color: white;
  padding: 4px 10px;
  border-radius: 12px;
  font-size: 12px;
  font-weight: 600;
}

.text-muted {
  color: #999;
  font-style: italic;
}

.text-center {
  text-align: center;
}

.empty-state {
  text-align: center;
  padding: 60px 20px;
  color: #999;
}

.empty-icon {
  font-size: 80px;
  margin-bottom: 20px;
  opacity: 0.5;
}

.empty-state p {
  font-size: 18px;
  font-weight: 500;
  color: #666;
}

</style>



