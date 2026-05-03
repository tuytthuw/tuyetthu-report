---
title: "Worklog Tuần 4"
date: 2026-04-06
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

## Tuần 4: Dashboard & Flashcard UI

**Ngày:** 06/04/2026 - 12/04/2026  
**Tổng thời gian:** ~40 giờ

### Mục tiêu Tuần 4:

* **Xây dựng Dashboard:** Tạo dashboard layout, components, widgets
* **Flashcard UI:** Implement flashcard list, review interface, SRS scheduling
* **API Integration:** Kết nối frontend với backend APIs
* **State Management:** Thiết lập Redux/Context cho app state
* **Deployment:** Deploy frontend lên AWS Amplify
* **Testing:** Test responsive design, API integration
* **Hỗ trợ Backend:** Tham gia thảo luận về Flashcard use cases

### Nội dung công việc:

| Ngày | Công việc | Trạng thái | Tài Liệu Tham Khảo |
| --- | --- | --- | --- |
| **Thứ 2** | **Xây dựng Dashboard Layout** <br> - Tạo dashboard page layout <br> - Tạo header, sidebar, main content area <br> - Implement responsive design <br> - Styling với Tailwind CSS | ✅ Hoàn thành | [Next.js Layouts](https://nextjs.org/docs/app/building-your-application/routing/layouts-and-templates) |
| **Thứ 3** | **Tạo Dashboard Widgets** <br> - Tạo stats widgets (total cards, due today, retention) <br> - Tạo charts với recharts library <br> - Implement data visualization <br> - Styling widgets với Shadcn/ui | ✅ Hoàn thành | [Recharts](https://recharts.org/)<br>[Shadcn/ui Cards](https://ui.shadcn.com/docs/components/card) |
| **Thứ 4** | **Implement Flashcard List & Review UI** <br> - Tạo flashcard list page <br> - Implement card flip animation <br> - Tạo review interface (Again/Hard/Good/Easy buttons) <br> - Implement keyboard shortcuts | ✅ Hoàn thành | [React Animations](https://www.framer.com/motion/) |
| **Thứ 5** | **Thiết lập State Management** <br> - Cấu hình Redux Toolkit hoặc Context API <br> - Tạo slices cho user, flashcards, stats <br> - Implement state persistence <br> - Tạo custom hooks | ✅ Hoàn thành | [Redux Toolkit](https://redux-toolkit.js.org/) |
| **Thứ 6** | **API Integration & Deployment** <br> - Tạo API client (axios wrapper) <br> - Integrate dashboard với backend APIs <br> - Integrate flashcard với backend APIs <br> - Deploy frontend lên AWS Amplify | ✅ Hoàn thành | [AWS Amplify](https://docs.aws.amazon.com/amplify/) |
| **Chủ nhật** | **Testing & Hỗ trợ Backend** <br> - Test responsive design (desktop, mobile, tablet) <br> - Test API integration <br> - Tham gia thảo luận về Flashcard use cases <br> - Ghi chép feedback từ testing | ✅ Hoàn thành | [Responsive Design](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Responsive_Design) |

### 🎓 Kiến thức đã học:

| Chủ đề | Nội dung | Kết quả |
|---------|---------|--------|
| **Dashboard Design** | Layout, widgets, responsive | ✅ Biết xây dựng dashboard |
| **Data Visualization** | Charts, graphs | ✅ Biết sử dụng recharts |
| **Animations** | Card flip, transitions | ✅ Biết tạo animations |
| **State Management** | Redux, Context API | ✅ Biết quản lý state |
| **API Integration** | Axios, error handling | ✅ Biết kết nối APIs |
| **Deployment** | Amplify, CI/CD | ✅ Biết deploy frontend |
| **Responsive Design** | Mobile-first, breakpoints | ✅ Biết design responsive |

### Kết quả:

* **Dashboard:**
  * ✅ Dashboard layout hoàn chỉnh
  * ✅ Stats widgets hiển thị đúng dữ liệu
  * ✅ Charts visualize learning progress
  * ✅ Responsive trên desktop, tablet, mobile

* **Flashcard UI:**
  * ✅ Flashcard list page working
  * ✅ Card flip animation smooth
  * ✅ Review interface intuitive
  * ✅ Keyboard shortcuts working

* **State Management:**
  * ✅ Redux store configured
  * ✅ Slices cho user, flashcards, stats
  * ✅ State persistence working
  * ✅ Custom hooks created

* **API Integration:**
  * ✅ API client setup
  * ✅ Dashboard APIs integrated
  * ✅ Flashcard APIs integrated
  * ✅ Error handling implemented

* **Deployment:**
  * ✅ Frontend deployed lên AWS Amplify
  * ✅ CI/CD pipeline working
  * ✅ Environment variables configured
  * ✅ Production build optimized

### Bài học:

* **Bài học 1: Responsive design từ đầu** - Thiết kế responsive từ đầu dễ hơn nhiều so với thêm vào sau.

* **Bài học 2: State management giúp code dễ maintain** - Redux Toolkit giúp quản lý state phức tạp một cách có tổ chức.

* **Bài học 3: API integration cần error handling** - Xử lý lỗi API đúng cách giúp UX tốt hơn.

### Kế hoạch Tuần 5:

* Integrate Amazon Bedrock cho AI Tutor
* Implement conversation UI
* Implement streaming responses
* Test end-to-end conversation flow
* Tham gia họp nhóm review tiến độ

---

## 📊 Thống kê Tuần 4:

| Tiêu chí | Giá trị |
|----------|--------|
| **Tổng thời gian** | ~40 giờ |
| **Số ngày làm việc** | 6 ngày |
| **Số components tạo** | 10+ components |
| **Số APIs integrate** | 5+ APIs |

---

**Cập nhật:** 29/04/2026  
**Trạng thái:** ✅ Hoàn thành
