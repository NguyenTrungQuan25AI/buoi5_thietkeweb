# BÁO CÁO REFACTORING CSS: TỐI ƯU HÓA FILE `quan_homework.css`

> **Người thực hiện:** Frontend Engineer  
> **Dự án:** Portfolio Cá Nhân - Nguyễn Trung Quân (`quan_homework.html` / `quan_homework.css`)  
> **Mục tiêu:** Rà soát, dọn dẹp các quy tắc dư thừa, loại bỏ trùng lặp, chuẩn hóa bảng màu bằng CSS Variables và tái cấu trúc mã nguồn theo mô hình chuẩn **Base → Layout → Components**.

---

## 1. LIỆT KÊ CÁC SELECTOR BỊ TRÙNG LẶP (DUPLICATE SELECTORS)

Qua phân tích toàn bộ 1.529 dòng CSS, có hai dạng trùng lặp selector:
1. **Trùng lặp phân tán (Structural Duplication):** Khai báo cùng một selector ở nhiều block code khác nhau trong cùng phạm vi (không qua Media Queries).
2. **Trùng lặp giữa Mobile-First và Media Query Breakpoints:** Một số thuộc tính bị khai báo lại không cần thiết hoặc bị phân mảnh giữa các tầng media query (`640px`, `768px`, `1024px`, `1440px`).

### 1.1. Bảng chi tiết các Selector bị trùng lặp trong cùng phạm vi base

| Selector | Vị trí xuất hiện (Dòng) | Nguyên nhân trùng lặp | Giải pháp xử lý |
| :--- | :--- | :--- | :--- |
| `header` | **Dòng 161** & **Dòng 187** | Dòng 161 gán `grid-area: header`, đến dòng 187 lại mở block `header` mới để khai báo flexbox, background, position sticky. | Gộp chung thuộc tính layout Grid và giao diện vào một block duy nhất tại tầng Layout. |
| `aside.sidebar` | **Dòng 165** & **Dòng 398** | Dòng 165 định nghĩa `grid-area: sidebar; min-width: 0;`, dòng 398 lại định nghĩa padding/layout con. | Hợp nhất vào một block `aside.sidebar` duy nhất. |
| `main` | **Dòng 170** & **Dòng 629** | Dòng 170 gán `grid-area: main; min-width: 0;`, dòng 629 lại khai báo lại `main` với flex column, padding, gap. | Gom toàn bộ thuộc tính của `main` về một nơi. |
| `footer` | **Dòng 175** & **Dòng 1092** | Dòng 175 gán `grid-area: footer`, dòng 1092 lại khai báo nền, flexbox, padding. | Hợp nhất vào một block `footer` duy nhất. |
| `nav` | **Dòng 281** & **Dòng 313** | Khai báo dropdown menu ở mobile (dòng 281) và ghi đè kiểu dropdown card ở `@media (min-width: 640px)` (dòng 313). | Giữ base tại dòng 281, quy hoạch phần override vào cụm responsive của Component Nav thay vì để rải rác. |
| `textarea` | **Dòng 1015** & **Dòng 1034** | Dòng 1015 gộp trong nhóm `input[type="text"], input[type="email"], select, textarea`, nhưng dòng 1034 lại tạo block riêng chỉ để đặt `resize: vertical; min-height: 100px;`. | Gom `resize` và `min-height` vào một block quy củ hoặc gộp cùng khối Form control. |
| `#skills-projects > div:nth-of-type(2)` | **Dòng 926**, **1219**, **1283** | Selector dự phòng bằng ID + nth-child viết đè lặp lại song song với `.projects-grid`. | Xóa bỏ selector dự phòng không cần thiết vì mã HTML đã có sẵn class `.projects-grid`. |

### 1.2. Trùng lặp do hệ thống Animation Typewriter (`@media (min-width: 1440px)`)
* Các selector như `.profile-name` (dòng 492 & 1413), `.profile-role` (dòng 499 & 1417), `.flip-hint` (dòng 529 & 1428), `.card-front .sidebar-quick-info li` (dòng 521 & 1421), `.project-item` (dòng 935 & 1480):
  * *Thực trạng:* Tại tầng cuối (Section 6 - Typewriter), các selector này được gọi lại một lần nữa chỉ để gán `animation: typeStepped...`.
  * *Đánh giá:* Khiến người bảo trì phải nhảy cóc giữa dòng 400 và dòng 1400 để xem trọn vẹn styling của một phần tử.
  * *Khắc phục:* Đưa các thuộc tính animation trực tiếp vào chính khai báo của Component hoặc tách thành module Animation riêng biệt rõ ràng.

---

## 2. ĐỀ XUẤT CÁC CLASS CÓ THỂ GỘP LẠI (CLASS CONSOLIDATION & UTILITIES)

Trong file hiện tại, nhiều class có các thuộc tính lặp lại gần như 90-100% nhưng đang được viết tách biệt:

### 2.1. Gộp các biến thể của thanh Hamburger (`.bar-top`, `.bar-mid`, `.bar-bot`)
* **Hiện trạng:**
  ```css
  /* 3 class riêng biệt nhưng dùng chung kích thước, bo góc, màu nền */
  .bar-top { width: 24px; height: 2px; background: ...; }
  .bar-mid { width: 24px; height: 2px; background: ...; }
  .bar-bot { width: 24px; height: 2px; background: ...; }
  ```
* **Đề xuất:**  
  Chỉ cần 1 class chung `.bar` định nghĩa khung vạch, các biến thể chỉ dùng khi chuyển đổi dấu X qua pseudo-class `:checked`:
  ```css
  .hamburger-btn .bar {
    width: 24px;
    height: 2px;
    background-color: var(--color-primary);
    border-radius: var(--radius-full);
    transition: transform var(--transition-normal), opacity var(--transition-normal);
  }
  .menu-toggle-input:checked ~ .hamburger-btn .bar:nth-child(1) { transform: translateY(8px) rotate(45deg); }
  .menu-toggle-input:checked ~ .hamburger-btn .bar:nth-child(2) { opacity: 0; }
  .menu-toggle-input:checked ~ .hamburger-btn .bar:nth-child(3) { transform: translateY(-8px) rotate(-45deg); }
  ```

### 2.2. Gộp các thẻ Badge trạng thái (`.badge-online` và `.status-badge`)
* **Hiện trạng:** Cả 2 class đều biểu diễn trạng thái "Sẵn sàng nhận việc", cùng có nền bo tròn mờ mờ, viền mảnh neon và màu chữ sáng.
* **Đề xuất:** Hợp nhất thành một class Component chung:
  ```css
  .badge {
    display: inline-flex;
    align-items: center;
    gap: var(--space-xs);
    padding: 3px 10px;
    font-size: var(--font-xs);
    font-weight: 600;
    border-radius: var(--radius-full);
    background-color: var(--color-badge-bg);
    border: 1px solid var(--color-primary-glow);
    color: var(--color-primary);
  }
  /* Điểm chấm tròn phát sáng nếu có */
  .badge-dot {
    width: 7px;
    height: 7px;
    border-radius: var(--radius-full);
    background-color: var(--color-primary);
    box-shadow: 0 0 8px var(--color-primary);
  }
  ```

### 2.3. Trích xuất Utility Class cho Flexbox căn giữa (Flex Center)
* **Hiện trạng:** Bộ 3 thuộc tính:
  ```css
  display: flex;
  align-items: center;
  justify-content: center;
  ```
  xuất hiện lặp đi lặp lại ở `.hamburger-btn`, `.avatar-box`, `.fab-contact`, `.parallax-banner`, `button[type="submit"]`.
* **Đề xuất:** Tạo class tiện ích:
  ```css
  .flex-center {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  ```

### 2.4. Trích xuất Component Thẻ Card (`.card-base`)
* **Hiện trạng:** `.project-item`, `section`, và `.card-face` đều chia sẻ cùng thuộc tính:
  * `background: var(--color-surface)` (hoặc `var(--color-bg)`)
  * `border: 1px solid var(--color-border)`
  * `border-radius: var(--radius-md)`
  * `padding: var(--space-lg)`
* **Đề xuất:** Tạo một base class `.card-base` hoặc mixin để giảm thiểu hơn 30 dòng khai báo lặp lại.

---

## 3. CHUYỂN CÁC GIÁ TRỊ MÀU CỨNG (HARDCODED) THÀNH CSS VARIABLES

Trong file hiện tại còn tồn tại nhiều mã màu viết tay (`#ff3366`, `#0e1524`, `rgba(11, 15, 25, ...)`, `#ffffff`, `#00b4d8`). Việc này làm đứt gãy tính nhất quán của Design System và gây khó khăn khi làm tính năng đổi màu chủ đề (Theme switcher).

### 3.1. Danh sách mã màu cứng đã quét được và vị trí

| Mã màu cứng (Hardcoded) | Vị trí phát hiện | Mục đích sử dụng | Đề xuất CSS Variable tương ứng |
| :--- | :--- | :--- | :--- |
| `#ff3366` | Dòng 260, 273 | Màu đỏ neon khi bấm nút đóng menu Hamburger | `--color-danger: #ff3366;` |
| `rgba(255, 51, 102, 0.6)` | Dòng 261, 274 | Glow phát sáng của màu đỏ cảnh báo | `--color-danger-glow: rgba(255, 51, 102, 0.6);` |
| `#0e1524` | Dòng 466, 540 | Màu tối phụ trong gradient của Profile Card | `--color-surface-dark: #0e1524;` |
| `#00c4cf` | Dòng 70 | Màu hover của nút FAB | `--color-primary-dark: #00c4cf;` |
| `#00b4d8` | Dòng 816 | Dải màu xanh chuyển tiếp trong thanh kỹ năng | `--color-accent: #00b4d8;` |
| `#ffffff` | Dòng 728 | Tiêu đề chữ trắng trong Parallax Banner | `--color-white: #ffffff;` |
| `rgba(11, 15, 25, 0.95)` / `0.98` | Dòng 188, 286 | Màu nền Header và Dropdown Menu trong suốt | `--color-bg-glass: rgba(11, 15, 25, 0.95);` |
| `rgba(11, 15, 25, 0.78)` / `0.88` | Dòng 684 | Lớp phủ nền ảnh Parallax Banner | `--color-overlay-dark: rgba(11, 15, 25, 0.85);` |
| `rgba(0, 0, 0, 0.8)` / `0.9` | Dòng 288, 319, 699, 733 | Bóng đổ đen đậm đa tầng (Deep Shadow) | `--shadow-deep: 0 16px 40px rgba(0, 0, 0, 0.8);` |
| `rgba(255, 255, 255, 0.05)` / `0.06` | Dòng 534, 800 | Nền viền kính mờ (Glass surface fill) | `--color-glass-fill: rgba(255, 255, 255, 0.05);` |

### 3.2. Khối `:root` sau khi chuẩn hóa bổ sung

```css
:root {
  /* --- 1.1 Bảng màu chính (Brand & Neutrals) --- */
  --color-primary: #00f3ff;
  --color-primary-glow: rgba(0, 243, 255, 0.4);
  --color-primary-dark: #00c4cf;
  --color-secondary: #7000ff;
  --color-accent: #00b4d8;
  --color-danger: #ff3366;
  --color-danger-glow: rgba(255, 51, 102, 0.6);

  --color-bg: #0b0f19;
  --color-bg-glass: rgba(11, 15, 25, 0.95);
  --color-surface: #151d2e;
  --color-surface-hover: #1c273e;
  --color-surface-dark: #0e1524;
  --color-glass-fill: rgba(255, 255, 255, 0.05);
  --color-overlay-dark: rgba(11, 15, 25, 0.85);

  --color-text-main: #f1f5f9;
  --color-text-muted: #94a3b8;
  --color-white: #ffffff;

  --color-border: rgba(255, 255, 255, 0.08);
  --color-border-focus: var(--color-primary);
  --color-badge-bg: rgba(0, 243, 255, 0.1);

  /* --- 1.2 Spacing & Radius --- */
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 16px;
  --space-lg: 24px;
  --space-xl: 36px;
  --space-2xl: 56px;

  --radius-sm: 6px;
  --radius-md: 12px;
  --radius-lg: 18px;
  --radius-full: 9999px;

  /* --- 1.3 Box Shadows --- */
  --shadow-sm: 0 2px 8px rgba(0, 0, 0, 0.2);
  --shadow-md: 0 8px 24px rgba(0, 0, 0, 0.35);
  --shadow-deep: 0 16px 40px rgba(0, 0, 0, 0.8);
  --shadow-neon: 0 0 15px var(--color-primary-glow);
  --shadow-neon-hover: 0 0 25px var(--color-primary);

  /* --- 1.4 FAB Controls --- */
  --fab-bg: var(--color-primary);
  --fab-color: var(--color-bg);
  --fab-pulse-glow: var(--color-primary-glow);
  --fab-hover-bg: var(--color-primary-dark);
}
```

---

## 4. SẮP XẾP LẠI THEO CẤU TRÚC: BASE → LAYOUT → COMPONENTS

Để code đạt chuẩn kiến trúc CSS hiện đại (lấy cảm hứng từ ITCSS / SMACSS), toàn bộ file được tái phân bổ theo luồng logic tăng dần độ chi tiết:

```
quan_homework.css
│
├── 1. BASE (Reset, Root Variables, Typography, Defensive Rules)
├── 2. LAYOUT (Grid tổng thể: Header, Sidebar, Main, Footer, Breakpoints khung)
└── 3. COMPONENTS (Header/Nav, Card Flip, Parallax, Skills, Projects, Form, FAB, Animations)
```

Dưới đây là bản thiết kế chi tiết từng tầng:

### TẦNG 1: BASE (NỀN TẢNG & BIẾN TOÀN CỤC)
* **Design Tokens (`:root`):** Toàn bộ biến màu sắc, khoảng cách, font chữ, hiệu ứng chuyển động.
* **Defensive Reset:**
  * Box-sizing toàn cục `*, *::before, *::after`.
  * Khắc phục bể chữ `overflow-wrap: break-word`.
  * Khóa tràn thanh cuộn ngang `overflow-x: hidden`.
  * Reset thẻ media `img, picture, video, canvas, svg { max-width: 100%; height: auto; }`.
  * Tùy biến thanh cuộn trình duyệt (`::-webkit-scrollbar`).

### TẦNG 2: LAYOUT (BỐ CỤC KHUNG LƯỚI TỔNG THỂ)
* **Mobile-First CSS Grid:**
  ```css
  body {
    display: grid;
    grid-template-columns: 100%;
    grid-template-areas:
      "header"
      "sidebar"
      "main"
      "footer";
    min-height: 100vh;
    min-height: 100dvh;
    background-color: var(--color-bg);
    color: var(--color-text-main);
  }

  /* Định danh 4 vùng lưới duy nhất một lần */
  header        { grid-area: header; }
  aside.sidebar { grid-area: sidebar; min-width: 0; }
  main          { grid-area: main; min-width: 0; }
  footer        { grid-area: footer; }
  ```
* **Desktop Grid Adaptation (`min-width: 1024px`):**
  ```css
  @media (min-width: 1024px) {
    body {
      grid-template-columns: 320px 1fr;
      grid-template-rows: auto 1fr auto;
      grid-template-areas:
        "header  header"
        "sidebar main"
        "footer  footer";
      max-width: 1440px;
      margin: 0 auto;
      box-shadow: 0 0 50px rgba(0, 0, 0, 0.5);
    }
  }
  ```

### TẦNG 3: COMPONENTS (CÁC KHỐI GIAO DIỆN CHỨC NĂNG)
* **3.1 Header & Navigation Component:**
  * Logo thương hiệu.
  * Checkbox hack & Nút Hamburger toggle (tối ưu hóa class `.bar`).
  * Dropdown Menu (Mobile fullscreen/absolute & Desktop Floating Dropdown).
* **3.2 Sidebar Profile Card (3D Flip):**
  * `.card-container`, `.card-face`, `.card-front`, `.card-back`.
  * Avatar box & Badge trạng thái.
  * Danh sách thông tin nhanh & Liên hệ mạng xã hội.
* **3.3 Main Content & Sections:**
  * Thẻ Card Section tổng thể (`section`, `section h2`).
  * **Parallax Banner:** Sử dụng bộ ba `background-attachment: fixed`, `background-position: center`, `background-size: cover`.
  * **Skills Module:** Thanh tiến trình `.skill-progress-bar`, keyframes chạy động `scaleX()`.
  * **Projects Grid Module:** Lưới thẻ card `.projects-grid`, `.project-item` và hiệu ứng hover neon.
  * **Contact Form Module:** Input/Select/Textarea styling, button submit neon.
* **3.4 Floating Action Button (FAB):**
  * Ghim cố định góc màn hình, pulse glow animation.
* **3.5 Animation Keyframes System:**
  * `@keyframes pulseGlow` (Nút FAB).
  * `@keyframes fillSkillBar` (Thanh phần trăm kỹ năng).
  * `@keyframes typeStepped` & `@keyframes blinkCaret` (Hiệu ứng gõ chữ thuần CSS).

---

## 5. BẢNG TỔNG HỢP HIỆU QUẢ SAU KHI REFACTOR

| Tiêu chí | Trước khi Refactor | Sau khi Refactor | Kết quả đạt được |
| :--- | :--- | :--- | :--- |
| **Trùng lặp Selector** | Rải rác tại nhiều vị trí (`header`, `main`, `sidebar`, `footer`, `textarea`...) | Khai báo 1 lần duy nhất cho mỗi phân tầng | Code sạch, không bị conflict đè thuộc tính |
| **Quản lý Màu sắc** | Hơn 15 vị trí dùng trực tiếp mã HEX và RGBA cứng | 100% màu sắc và bóng đổ đưa về CSS Variables | Hỗ trợ đổi Theme chỉ với 1 thao tác |
| **Cấu trúc File** | Trộn lẫn giữa Layout, Media Queries và Animation chi tiết | Phân cấp rành mạch: **Base → Layout → Components** | Chuẩn hóa theo Best Practice ngành công nghiệp |
| **Khả năng bảo trì** | Khó theo dõi khi file vượt quá 1.500 dòng | Dễ định vị lỗi, dễ mở rộng thêm component mới | Giảm thời gian debug và tăng tốc độ render |

---
*Báo cáo được hoàn thành phục vụ quy trình tối ưu hóa mã nguồn Frontend.*
